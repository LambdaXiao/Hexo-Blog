---
title: 升级gradle至3.0+，一路填坑
date: 2018-03-02 19:22:32
tags: gradle
categories: gradle
---

本人项目中的gradle版本比较低，为了不影响后续开发，故决定升级到最新的gradle3.1.2，特此记录一下自己填过的坑，以供后来者借鉴，避免躺在同样的坑。

### 1，首先修改项目根目录下的build.gradle文件

把gradle依赖升级到3.1.2。

```
// 说明gradle的版本号
classpath 'com.android.tools.build:gradle:3.1.2'
```
### 2，修改项目根目录下gradle/gradle-wrapper.properties

```
distributionUrl=https\://services.gradle.org/distributions/gradle-4.4-all.zip
```

sync一下，

<!-- more -->
### 3，报错如下：


```
Cannot set the value of read-only property 'outputFile' for ApkVariantOutputImpl_Decorated{apkData=Main{type=MAIN, fullName=googlePlayDebug, filters=[]}} of type com.android.build.gradle.internal.api.ApkVariantOutputImpl.
```


原因是输出apk名称的outputFile是只读属性，度娘一波，修改为：


```
android.applicationVariants.all { variant ->
        variant.outputs.all {
            //定制apk输出路径
//            variant.getPackageApplication().outputDirectory = new File(project.rootDir.absolutePath + "/app/build/outputs/apk")
            //定制apk输出文件名
            outputFileName = "app_${variant.flavorName}_V${defaultConfig.versionName}_${variant.buildType.name}.apk"
        }
    }
```

sync一下，

### 4，报错如下：

```
Unable to load class 'org.gradle.api.internal.component.Usage'.
```

显示找不到该类，是因为项目内使用了

```
classpath 'com.novoda:bintray-release:0.3.4'//添加
```

该类在gradle4.4版本被更改了名字，随后将该库升级到0.8.0的版本，编译通过这个坑。


sync一下，

### 5，报错如下：


```
The specified Android SDK Build Tools version (25.0.0) is ignored, as it is below the minimum supported version (27.0.3) for Android Gradle Plugin 3.1.2.
Android SDK Build Tools 27.0.3 will be used.
To suppress this warning, remove "buildToolsVersion '25.0.0'" from your build.gradle file, as each version of the Android Gradle Plugin now has a default version of the build tools.

Update Build Tools version and sync project
```


Android Studio3.0之后不需要再指定buildToolsVersion，每个版本的Android Gradle插件都有默认版本的构建工具，删掉就行。

继续填坑，


```
Configuration 'androidTestCompile' is obsolete and has been replaced with 'androidTestImplementation' and 'androidTestApi'.

Configuration 'provided' is obsolete and has been replaced with 'compileOnly'.

Configuration 'compile' is obsolete and has been replaced with 'implementation' and 'api'.

Configuration 'androidTestApi' is obsolete and has been replaced with 'androidTestImplementation'.
Configuration 'testCompile' is obsolete and has been replaced with 'testImplementation' and 'testApi'.

Configuration 'testApi' is obsolete and has been replaced with 'testImplementation'.
```


意思是以上那些编译指令已经过时了，根据提示使用它推荐的替换掉就行，其中compile，implementation，api比较特殊，替换时需特别注意，网上查到

api指令
完全等同于compile指令，没区别，你将所有的compile改成api，完全没有错。

implement指令
这个指令的特点就是，对于使用了该命令编译的依赖，对该项目有依赖的项目将无法访问到使用该命令编译的依赖中的任何程序，也就是将该依赖隐藏在内部，而不对外部公开。

编译如果再次报错某些内容已经找不到了的话，大意为项目中某个module的依赖被隐藏于内部，获取不到——这是因为新换的“implementation”的原因！，这是建议用替换成api指令编译就OK


sync一下，

### 6，报错如下：


```
All flavors must now belong to a named flavor dimension.
```


原来是Plugin3.0.0 之后，引入了一个新的依赖机制flavorDimensions，用于基于多个标准构建多个版本，需要为所有的版本都指定dimension。


官方文档用法如下：

```
[java] view plain copy
// Specifies two flavor dimensions.  
flavorDimensions "tier", "minApi"  
  
productFlavors {  
     free {  
      // Assigns this product flavor to the "tier" flavor dimension. Specifying  
      // this property is optional if you are using only one dimension.  
      dimension "tier"  
      ...  
    }  
  
    paid {  
      dimension "tier"  
      ...  
    }  
  
    minApi23 {  
        dimension "minApi"  
        ...  
    }  
  
    minApi18 {  
        dimension "minApi"  
        ...  
    }  
}
```

目前我只需要一个标准的版本，故直接在defaultconfig下面添加
flavorDimensions 'default'

sync一下，

### 7，报错如下：

```
DSL element 'DexOptions.incremental' is obsolete and will be removed at the end of 2018.
```


是因为我项目用了incremental
dexOptions {                          //multiDex的一些相关配置，这样配置可以让你的编译速度更快
    preDexLibraries = false           //让它不要对Lib做preDexing
    incremental true                  //开启incremental dexing,优化编译效率，这个功能android studio默认是关闭的。
    javaMaxHeapSize "4g"              //增加java堆内存大小
}
incremental这个将在2018年后被弃用，故我直接删掉incremental就好了

sync一下，

### 8，报错如下


```
Unable to resolve dependency for ':app@googlePlayUat/compileClasspath': Could not resolve project :volley.
```


报一堆的依赖的module无法找到，纠结了半天，最后分析了错误日志，和BuildType有关，我项目的BuildType有三个，release，uat，debug

错误日志是googlePlayUat的compileClasspath找不到，网上搜了一波终于找到解决方法，在uat闭包里面添加


```
uat { ...                

//关键代码，release， debug为library中已有buildType                

matchingFallbacks = ['release', 'debug']        

}
```


sync一下，

### 9，报错如下

```
Failed to resolve: runtime
```


真是折磨了我半天，无任何提示，不知错误在哪里，网上搜了一波又一波也无结果，幸亏皇天不负有心人，最后误打误撞让我解决了，如下


```
allprojects {
    repositories {
        google()//新添加
        jcenter()
    }
}
```

在项目根目录下的build.gradle的allprojects新添加google()，填完这个坑真开心，但紧接着又出现下一个坑


sync一下，

### 10，报错如下


```
Manifest merger failed : Attribute meta-data#android.support.VERSION@value value=(25.4.0) from [com.android.support:design:25.3.1] AndroidManifest.xml:27:9-31
	is also present at [com.android.support:support-core-utils:26.1.0] AndroidManifest.xml:28:13-35 value=(26.1.0).
	Suggestion: add 'tools:replace="android:value"' to <meta-data> element at AndroidManifest.xml:26:9-28:38 to override.
```

意思是Manifest 合并失败，可能support库的版本没有统一，统一调高support库版本就OK，


sync一下，终极大坑终于来了

### 11，报错如下

```
AAPT2 error: check logs for details
```


这个问题原因是因为android studio3.0 是默认把AApt2开启的，解决起来也非常简单，只需要gradle.properties文件中，添上android.enableAapt2=false，问题得以解决。

但是也不算完全意义上的解决，提示说android.enableAapt2=false将在2018年底弃用，暂时没找到更好的方法，如大家有更好的解决方案，欢迎提出来！



以上众坑，大家可以借鉴借鉴！

