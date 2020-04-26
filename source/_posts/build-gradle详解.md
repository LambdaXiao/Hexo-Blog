---
title: build.gradle详解
date: 2019-04-18 11:46:33
tags: gradle
categories: gradle
---

### 1.什么是Gradle的Android插件

Gradle有很多插件，为了支持Android项目的构建，谷歌为Gradle编写了Android插件，新的Android构建系统就是由Gradle的Android插件组成的，Gradle是一个高级构建工具包，它管理依赖项并允许开发者自定义构建逻辑。Android Studio使用Gradle wrapper来集成Gradle的Android插件。需要注意的是，Gradle的Android插件也可以独立于AndroidStudio运行。
在 Android的官方网站提到了新的Android构建系统主要有以下几个特点：

- 代码和资源易于重用
- 无论是针对多个apk发行版还是针对不同风格的应用程序，都可以很容易创建应用程序的多个不同版本。
- 易于配置、扩展和自定义构建过程
- 良好的IDE集成

Gradle的Android插件结合Android Studio成为了目前最为流行的Android构建系统。
<!-- more -->
### 2. Android Studio的模块类型

Android Studio中的每个项目包含一个或多个含有源代码文件和资源文件的模块，这些模块可以独立构建、测试或调试，一个Android Studio的模块类型可以有以下几种：

**Android应用程序模块**

 Android应用程序模块可能依赖于库模块，尽管许多Android应用程序只包含一个应用程序模块，构建系统会将其生成一个APK。

**Android 库模块**

Android库模块包含可重用的特定于Android的代码和资源，构建系统会将其生成一个AAR。

**App 引擎模块**

包含应用程序引擎集成的代码和资源。

**Java 库模块**

包含可重用的代码，构建系统会将其生成一个JAR包。

所有构建文件在 Gradle Scripts 层级下显示，大概介绍下这些文件的用处。

- **项目build.gradle：** 配置项目的整体属性，比如指定使用的代码仓库、依赖的Gradle插件版本等等。
- **模块build.gradle：** 配置当前Module的编译参数。
- **gradle-wrapper.properites：** 配置Gradle Wrapper，可以查看Gradle核心思想（四）看似无用，实则重要的Gradle Wrapper这篇文章。

- **gradle.properties：** 配置Gradle的编译参数。具体配置见Gradle官方文档

- **settings.gradle：** 配置Gradle的多项目管理，比如这个项目包含哪些模块等。

- **local.properties：** 一般用来存放该Android项目的私有属性配置，比如配置SDK或者NDK的环境路径，各个机器上这个变量可能都是不一样的，所以不应该进入版本库。

- **./app/proguard-rules.pro**
这个和上面说的一样混淆文件

- **./gradlew 和 ./gradlew.bat**
自动完成 gradle 环境的脚本，在linux和mac下直接运行gradlew会自动完成gradle环境的搭建。


- **./XXX.iml 和 ./app/app.iml**
iml是Intellij模块文件。Intellij是一款JAVA的IDE。Android Studio是基于开源的Intellij IDEA开发出来的IDE。
所以Android Studio有的IDE功能是需要有.iml才能使用的。比如我们删除了iml文件，可能就在Android Studio中看不到一些目录了。

这篇文章主要介绍项目build.gradle和模块build.gradle。

### 3.项目build.gradle
build.grade有两个，一个是全局的，一个是在模块里面。
全局的build.grade主要设置的是声明仓库源，gradle的版本号说明等。

文件目录如下：
./build.gradle

```
buildscript { //设置脚本的运行环境
    repositories {
        // 声明仓库源，比如我们构建了一个安卓的库，现在想要把库上传到jcenter中供别人一起使用，则可以上传到jcenter中
        // 具体上传步骤见：http://www.jcodecraeer.com/a/anzhuokaifa/Android_Studio/2015/0227/2502.html
        jcenter()
        google()
    }
    dependencies {
        /* 配置依赖的Gradle插件版本，Gradle插件属于第三方插件，因此这里在buildscrip块中配置谷歌的Maven库和JCenter库，这样Gradle系统才能找到对应的Gradle插件。如果使用google()报not found: 'google()'错误，可以用如下代码替代：maven { url 'https://maven.google.com' } */
        classpath 'com.android.tools.build:gradle:1.3.0'

        // NOTE: Do not place your application dependencies here; they belong
        // in the individual module build.gradle files
    }
}

// 所有项目都继承这个配置
allprojects {
    repositories {
        mavenLocal()
        jcenter()
    }
}
//配置全局的sdk版本号，其他module引用这个的
ext {
compileSdkVersion = 23
buildToolsVersion = '23.0.1'
minSdkVersion = '15'
targetSdkVersion = '22'
supportLibraryVersion = '24.0.0'
gradleVersion = '2.0.0-beta7'
}
```

### 4.模块build.gradle

这里介绍app模块的build.gradle，其他库模块类似

文件目录如下：
./app/build.grade 
```
/*
* Android插件有多个类型分别为：
* 应用程序插件，插件id为com.android.application，会生成一个APK。
* 库插件，插件id为com.android.library，会生成一个AAR，提供给其他应用程序模块用。
* 测试插件，插件id为com.android.test，用于测试其他的模块。
* feature插件，插件id为com.android.feature，创建Android Instant App时需要用到的插件。
* Instant App插件，插件id为com.android.instantapp，是Android Instant App的入口。
*/

apply plugin: 'com.android.application'   
 
android {       // 配置了所有android构建的所有参数
    
    compileSdkVersion 23                           //编译sdk的版本，也就是API Level，例如API-19、API-20、API-21等等。
    buildToolsVersion "23.0.1"                     //build tools的版本，其中包括了打包工具aapt、dx等等。这个工具的目录位于你的sdk目录/build-tools/下

    aaptOptions.cruncherEnabled = false  
    aaptOptions.useNewCruncher = false             //关闭Android Studio的PNG合法性检查的

    defaultConfig {                                //默认配置
        
        applicationId "com.awesomeproject"         // 应用包名
        minSdkVersion 16                           //最小sdk版本，如果设备小于这个版本或者大于maxSdkVersion(一般不用)将无法安装这个应用     
        targetSdkVersion 22                        //目标sdk版本，如果设备等于这个版本那么android平台就不进行兼容性检查，运行效率会高一点
        
        versionCode 1                              // 版本号,版本更新了几次，第一版应用是1，以后每更新一次加1
        versionName "1.0"                          //版本信息，这个会显示给用户，就是用户看到的版本号	
    
        archivesBaseName = "weshare-$versionName"  //指定打包成Jar文件时候的文件名称
        
        multiDexEnabled true                       //当方法数超过65535(方法的索引使用的是一个short值，而short最大值是65535)的时候允许打包成多个dex文件，动态加载dex。这里面坑很深啊
        
        ndk {                                                     // 原生
            moduleName "xiaopaowifisafe"                          //设置库(so)文件名称
            ldLibs "log", "z", "m", "jnigraphics", "android"
            //引入库，比如要用到的__android_log_print
            abiFilters "armeabi", "x86", "armeabi-v7a"            //, "x86"  显示指定支持的ABIs
            cFlags "-std=c++11 -fexceptions"                      // C++11
            stl "gnustl_static"
        }
    }

    buildTypes {                                            //配置构建不同类型的APK
        release {                                           //release版本的配置
            zipAlignEnabled true                            //是否支持zip
            shrinkResources true                            // 移除无用的resource文件
            minifyEnabled true                              //是否进行混淆
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'  //指定混淆规则文件,release的Proguard默认为Module下的proguard-rules.pro文件.           
            debuggable false                                //是否支持调试
            signingConfig signingConfigs.release            //设置签名信息
            applicationIdSuffix ".uat"                      //配置applicationId的后缀
            //manifest占位符
            manifestPlaceholders = [
                    APP_NAME   : "@string/app_name_uat",
                    FACEBOOK_ID: rootProject.facebook_id_uat
            ]
            buildConfigField('String', 'SERVER_ADDRESS', "test") //配置同字段不同开发环境取不同的值
            jniDebuggable false                          //关闭jni调试
            multiDexEnabled true              //是否启动自动拆分多个dex的功能
            
            ndk {                                                                 //ndk的一些配置
                // cFlags "-std=c++11 -fexceptions -O3 -D__RELEASE__"             // C++11
                // platformVersion  = "19"
                moduleName "xiaopaowifisafe" //设置库(so)文件名称
                ldLibs "log", "z", "m", "jnigraphics", "android"
                //引入库，比如要用到的__android_log_print
                abiFilters "armeabi", "x86", "armeabi-v7a"//, "x86"
                cFlags "-std=c++11 -fexceptions" // C++11
                stl "gnustl_static"
            }
            
            applicationVariants.all { variant ->                                  //采用动态替换字符串的方式生成不同的release.apk
                variant.outputs.each { output ->
                    def outputFile = output.outputFile
                    if (outputFile != null && outputFile.name.endsWith('release.apk')) {
                        def timeStamp = new Date().format('yyyyMMddHH');
                        def fileName = "WeShare-${defaultConfig.versionName}" + "-" + timeStamp + "-lj-" + ".apk";
                        output.outputFile = file("${outputFile.parent}/${fileName}")
                    }
                }
            }
            
        }
        debug {                                                                //debug版本的配置
            minifyEnabled false
            zipAlignEnabled true          
            shrinkResources true                                               // 移除无用的resource文件
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
            debuggable true
//          jniDebuggable true
            ndk {
                cFlags "-std=c++11 -fexceptions -g -D __DEBUG__" // C++11
            }
            jniDebuggable true
        }
    }

    lintOptions {                   //Lint配置，程序在编译的时候会检查lint，有任何错误提示会停止build，我们可以关闭这个开关
        abortOnError false          //即使报错也不会停止打包     
        checkReleaseBuilds false    //打包release版本的时候进行检测
        
    }

    sourceSets {                                            //配置目录指向，默认的一些文件路径的配置
        main {
             manifest.srcFile 'AndroidManifest.xml'         //指定AndroidManifest文件
            java.srcDirs = ['src']                          //指定source目录
            resources.srcDirs = ['src']                     //指定source目录
            aidl.srcDirs = ['src']                          //指定source目录
            renderscript.srcDirs = ['src']                  //指定source目录
            res.srcDirs = ['res']                           //指定资源目录
            assets.srcDirs = ['assets']                     //指定assets目录
            jniLibs.srcDirs = ['libs']                      //指定lib库目录
        }
	debug.setRoot('build-types/debug')                  //指定debug模式的路径
        release.setRoot('build-types/release')              //指定release模式的路径
    }

	
    dexOptions {                          //DEX工具配置，multiDex的一些相关配置，这样配置可以让你的编译速度更快
        preDexLibraries = false           //让它不要对Lib做preDexing        
        incremental true                  //开启incremental dexing,优化编译效率，这个功能android studio默认是关闭的。       
        javaMaxHeapSize "4g"              //增加java堆内存大小
    }


    compileOptions {
         //在这里你可以进行 Java 的版本配置，
         //以便使用对应版本的一些新特性
    }

    signingConfigs {                                 //签名配置
        release {                                    //发布版签名配置
            storeFile file("fk.keystore")            //密钥文件路径
            storePassword "123"                      //密钥文件密码
            keyAlias "fk"                            //key别名
            keyPassword "123"                        //key密码
        }
        debug {                                      //debug版签名配置
            storeFile file("fk.keystore")
            storePassword "123"
            keyAlias "fk"
            keyPassword "123"
        }
    }

   packagingOptions {       //打包时的相关配置
        exclude 'META-INF/ASL2.0'
        exclude 'META-INF/LICENSE'
        exclude 'META-INF/NOTICE'
        exclude 'META-INF/MANIFEST.MF'
    }
    productFlavors {            //多渠道配置
           //在这里你可以设置你的产品发布的一些东西，
           //比如你现在一共软件需要发布到不同渠道，
           //且不同渠道中的包名不同，那么可以在此进行配置；
           //甚至可以设置不同的 AndroidManifest.xml 文件。
        xiaopao {
        }
        googlePlay {
        }
        solo {
        }
    }
    productFlavors.all {
        flavor -> flavor.manifestPlaceholders = [UMENG_CHANNEL_VALUE: name]
    }
    //所谓ProductFlavors其实就是可定义的产品特性，
    //配合 manifest merger 使用的时候就可以达成在一次编译
    //过程中产生多个具有自己特性配置的版本。

    //上面这个配置的作用就是，为每个渠道包产生不同的 UMENG_CHANNEL_VALUE 的值。
}


repositories {                                 //远程依赖
        
    mavenCentral()                             //从中央库里面获取依赖
    maven{                                     //或者使用指定的本地maven库
        url "file://F:/githubrepo/releases"
    }  
    
    maven{                                     //或者使用指定的远程maven库
        url "远程库地址" 
    }
}

task copyNativeLibs(type: Copy) {              //.so文件的导入
        from fileTree(dir: 'libs', include: 'armeabi/*.so') into 'build/lib'
    }

dependencies {                                                // 依赖的工具包
    compile fileTree(dir: 'libs', include: ['*.jar'])         //编译lib目录下的.jar文件
    compile 'com.android.support:appcompat-v7:23.0.0'         //编译来自Jcenter的第三方开源库
    compile project(':Easylink')                              //编译附加的项目
}


apply plugin: 'com.google.gms.google-services'                //声明是要使用谷歌服务框架


task showMeCache << {                                         //第三方依赖库的本地缓存路径
    configurations.compile.each { println it }
}
//使用maven仓库。android有两个标准的library文件服务器，一个jcenter一个maven。两者毫无关系。
//jcenter有的maven可能没有，反之亦然。
//如果要使用jcenter的话就把mavenCentral()替换成jcenter()
repositories {
    mavenCentral()
}


```

### dependencies 块

dependencies 块用于配置该module构建过程中所依赖的所有库。Gradle插件3.4版本新增了 api 和 implementation 来代替 compile 配置依赖，其中 api 和此前的 compile是一样的。implementation和api主要以下的区别：

- implementation可以让module在编译时隐藏自己使用的依赖，但是在运行时这个依赖对所有模块是可见的。而api与compile一样，无法隐藏自己使用的依赖。
- 如果使用api，一个module发生变化，这条依赖链上所有的module都需要重新编译，而使用implemention，只有直接依赖这个module需要重新编译。

### 全局配置

如果有多个module的配置是一样的，可以将这些配置提取出来，也就是使用全局配置。全局配置有多种方式，这里介绍其中的两种。

**1. 使用ext块配置**

 在项目build.gradle中使用ext块，如下所示。

```
ext{
    compileSdkVersion =28
    buildToolsVersion ="28.0.3"
    minSdkVersion =15
    targetSdkVersion =28
}
```
在某个module的build.gradle中使用配置：

```
apply plugin: 'com.android.application'
android {
    compileSdkVersion rootProject.ext.compileSdkVersion
    buildToolsVersion rootProject.ext.buildToolsVersion
    defaultConfig {
        applicationId "com.example.liuwangshu.hookinstrumentation"
        minSdkVersion rootProject.ext.minSdkVersion
        targetSdkVersion rootProject.ext.targetSdkVersion
        versionCode 1
        versionName "1.0"
        testInstrumentationRunner "android.support.test.runner.AndroidJUnitRunner"
    }
  ...
}
...
```
**2. 使用config.gradle配置**

首先在根目录下创建config.gradle文件来进行配置。

config.gradle

```
ext{
    android=[
            applicationId:"com.example.liuwangshu.hookinstrumentation",
            compileSdkVersion :28,
            buildToolsVersion :"28.0.3",
            minSdkVersion : 15,
            targetSdkVersion : 28,
    ]

    dependencies =[
            "appcompat-v7" : "com.android.support:appcompat-v7:28.0.0",
            "constraint"  : "com.android.support.constraint:constraint-layout:1.1.3",
    ]
}
```
接着在项目build.gradle中添加
```
apply from: 'config.gradle'
```
这样项目的所有module都能用config.gradle中定义的参数。
最后在module的build.gradle中使用配置：

```
apply plugin: 'com.android.application'
apply from: 'config.gradle'

android {
    compileSdkVersion rootProject.ext.android.compileSdkVersion
    buildToolsVersion rootProject.ext.android.buildToolsVersion
    defaultConfig {
        applicationId rootProject.ext.android.applicationId
        minSdkVersion rootProject.ext.android.minSdkVersion
        targetSdkVersion rootProject.ext.android.targetSdkVersion
        versionCode 1
        versionName "1.0"
        testInstrumentationRunner "android.support.test.runner.AndroidJUnitRunner"
    }
   ...
dependencies {
    implementation rootProject.ext.dependencies["constraint"]
    implementation rootProject.ext.dependencies["appcompat-v7"]
    ...
}
```
