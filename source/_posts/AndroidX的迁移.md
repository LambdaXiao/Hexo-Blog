---
title: AndroidX的迁移
date: 2019-05-09 18:18:13
tags: AndroidX
categories: 其他
---

Google 2018 IO 大会推出了 Android新的扩展库 AndroidX，用于替换原来的 Android扩展库，将原来的android.*替换成androidx.*；只有包名和Maven工件名受到影响，原来的类名，方法名和字段名不会更改。接下来我们来看看使用AndroidX的扩展库需要哪些配置。

#### AndroidX变化

- 常用依赖库对比：

Old build artifact | AndroidX build artifact
---|---
com.android.support:appcompat-v7:28.0.2 | androidx.appcompat:appcompat:1.0.0
com.android.support:design:28.0.2 | com.google.android.material:material:1.0.0
com.android.support:support-v4:28.0.2|	androidx.legacy:legacy-support-v4:1.0.0
com.android.support:recyclerview-v7:28.0.2|	androidx.recyclerview:recyclerview:1.0.0
com.android.support.constraint:constraint-layout:1.1.2|	androidx.constraintlayout:constraintlayout:1.1.2

具体路径参考请下载[CSV格式](https://developer.android.google.cn/topic/libraries/support-library/downloads/androidx-artifact-mapping.csv)

- 常用支持库类对比：


Support Library class | AndroidX class
---|---
android.support.v4.app.Fragment | androidx.fragment.app.Fragment
android.support.v4.app.FragmentActivity | androidx.fragment.app.FragmentActivity
android.support.v7.app.AppCompatActivity | androidx.appcompat.app.AppCompatActivity
android.support.v7.app.ActionBar | androidx.appcompat.app.ActionBar
android.support.v7.widget.RecyclerView|androidx.recyclerview.widget.RecyclerView


具体路径参考请下载[CSV格式](https://developer.android.google.cn/topic/libraries/support-library/downloads/androidx-class-mapping.csv)
<!-- more -->
#### 一键迁移AndroidX库

AS 3.2 及以上版本提供了更加方便快捷的方法一键迁移到AndroidX。选择菜单上的ReFactor —— Migrate to AndroidX...即可。（如果迁移失败，就需要按以下AndroidX配置手动去修改迁移）

#### AndroidX手动配置

- 更新升级插件

将AS更新至 AS 3.2及以上；

compileSdkVersion 版本升级到 28及以上；

buildToolsVersion 版本改为 28.0.2及以上；

Gradle 版本改为 3.2及以上；
```
classpath 'com.android.tools.build:gradle:3.4.0'
```

Gradle 插件版本改为 4.6及以上；

项目下 gradle/wrapper/gradle-wrapper.propertie 文件中的distributionUrl改为

```
distributionUrl=https\://services.gradle.org/distributions/gradle-5.1.1-all.zip
```


- 开启迁移AndroidX

在项目的gradle.properties文件里添加如下配置：

```
android.useAndroidX=true
android.enableJetifier=true
```
表示项目启用 AndroidX 并迁移到 AndroidX。

- 替换依赖库

修改项目app目录下的build.gradle依赖库：

```
implementation 'com.android.support:appcompat-v7:28.0.2' → implementation 'androidx.appcompat:appcompat:1.0.0'
implementation 'com.android.support:design:28.0.2'  → implementation 'com.google.android.material:material:1.0.0'
implementation 'com.android.support.constraint:constraint-layout:1.1.2' → implementation 'androidx.constraintlayout:constraintlayout:1.1.2'
...
```
- 修改支持库类

将原来import的android.**包删除，重新import新的androidx.**包；

```
import android.support.v7.app.AppCompatActivity; → import androidx.appcompat.app.AppCompatActivity;
```

#### 遇到的问题

1. 引入ButterKnife、EventBus导致的
 
```
The given artifact contains a string literal with a package reference 'android.support.v4.content' that cannot be safely rewritten. Libraries using reflection such as annotation processors need to be updated manually to add support for androidx.
```
原因是里面有代码涉及到ClassName.get("android.support.v4.content", "ContextCompat");由于在Androidx项目已经找不到android.support.v4.content包了，所以get()就报错了。

**解决方案是：**
kapt 替换 annotationProcessor
```
apply plugin: 'kotlin-kapt'

......

implementation "com.jakewharton:butterknife:8.8.1"
kapt "com.jakewharton:butterknife-compiler:8.8.1"
implementation "org.greenrobot:eventbus:3.1.1"
kapt "org.greenrobot:eventbus-annotation-processor:3.1.1"
```
> annotationProcessor顾名思义是注解处理器的意思,是APT(Annotation Processing Tool)工具中的一种，随着Android Gradle 插件 2.2 版本的发布，Android Gradle 插件提供了名为 annotationProcessor 的功能来完全代替 android-apt，不需要引入，可以直接在build.gradle文件中使用。它对源代码文件进行检测找出其中的Annotation，根据注解自动生成代码。 Annotation处理器在处理Annotation时可以根据源文件中的Annotation生成额外的源文件和其它的文件，之后将编译生成的源文件和原来的源文件一起生成class文件
。生成额外文件的规则是在依赖库里定义的，只在编译的时候执行，但是库最终不打包到apk中，所以使用Butterknife这类编译注解框架并不会增加apk的大小

> kapt也是APT工具的一种，使用 Kotlin 开发 Android 应用时，要在预编译阶段处理注解必须使用kotlin-kapt而不能使用annotationProcessor,kotlin-kapt不是Android Gradle 内置插件需要额外引入

**特别注意**：将 annotaionProcessor换成kapt时如果之前编译过，必须要先执行clean project再编译，才能让注解被正常处理！

重新build后还是报一样的错误，网上查了下得知要把butterknife调到最新，于是

```
implementation "com.jakewharton:butterknife:10.0.0"
kapt "com.jakewharton:butterknife-compiler:10.0.0"
```
再次build又抛出如下错误：

```
Error: Static interface methods are only supported starting with Android N (--min-api 24): void butterknife.Unbinder.lambda$static$0()\
```
谷歌以下解决如下：

```
//在android闭包里面添加支持 java-8
android {

...........................

    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
}
```
最后终于解决所有错误，成功运行了，特此记录总结一下。

以上在AndroidX的迁移过程所遇到的问题及解决方案希望对大家有所帮助！