---
layout: post
title: "Gradle Experimental Plugin User Guide"
date: 2015-08-16 01:07:13 +0800
comments: true
categories: Gradle NDK
---

### Contents

1. [Introduction](#introduction)
2. [Requirements](#requirements)
3. [Migrating from Traditional Android Gradle Plugin](#migrating-from-traditional-android-gradle-plugin)
4. [Ndk Integration](ndk-integration)
	1. [Known Limitations](#known-linitations)
	2. [Samples](#samples)
	
### Introduction

The new experimental plugin is based on Gradle’s new component model mechanism, while allows significant reduction in configuration time. It also includes NDK integration for building JNI applications. This user guides provides details on how to use it and highlights the difference between the new plugin and the original plugin.

**WARNING:** Note that this is plugin is at the experimental stage. The Gradle API for the new component model is not final, which means it’ll only work with a specific version of Gradle until the APIs are final.

Additionally, the DSL is likely change significantly, as APIs to create the DSL are finalized.

This is a very early preview of the plugin for feedback on performance and NDK integration.

<!--more-->

### Requirements

* Gradle 2.5 only
* Android NDK r10e (if you are using NDK)
* SDK with Build Tools at least version 19.0.0 and we aim to minimize the amount of changes needed for the migration process in the future. Some features may require a more recent version.

### Migrating from Traditional Android Gradle Plugin

A typical Android Studio project may have a directory structure as follows. File that needs to be change is highlighted in red:

There are some significant changes in the DSL between the new plugin and the traditional one.

![image](/images/notes_images/gradle-project-folder-tree.png)


**./gradle/wrapper/gradle-wrapper.properties**

* The new plugin supports only gradle-2.5.

```
#Wed Apr 10 15:27:10 PDT 2013
distributionBase=GRADLE_USER_HOME
distributionPath=wrapper/dists
zipStoreBase=GRADLE_USER_HOME
zipStorePath=wrapper/dists
distributionUrl=https\://services.gradle.org/distributions/gradle-2.5-all.zip
```

**./build.gradle**

* Classpath for the plugin is com.android.tools.build:gradle-experimental instead of com.android.tools.build:gradle.
* The current version is 0.2.0.

![image](/images/notes_images/gradle-project-top-level-gradle-build-file-content.png)

**./app/build.gradle**
There are significant changes to the DSL of the plugin.  We understand that many of the changes are frustrating and seem unnecessary, and our goal is to remove some of these current changes to minimize the migration process from the traditional plugin in the future.

DSL Changes:

* Plugin name is `com.android.model.application` instead of `com.android.application`.  Or use `apply plugin: 'com.android.model.library'` if you want to create an Android aar library.
* Configuration is wrapped with the `model { }` block
* Most properties require the = operator
* Adding elements to a Collection should be done using the += operator.

Current DSL Limitations that will hopefully go away:

* buildTypes, productFlavors and signingConfigs must be place outside of the  `android { }` block.
* Nested options within the `android { }` block must be configured using the with keyword.
* Properties are only set with their direct types only, with no way to accept other types and adapting them. For instance:
	* Properties of type File accepts only File instead of File and String objects.
	* minSdkVersion cannot directly receive either an integer or string (for codename).
* Creating a buildType or productFlavor requires calling the create method.  Modifying an existing one such as the release and debug buildType can be done using the just the name.
* The DSL for modifying variants and their tasks is very, very limited right now.

![image](/images/notes_images/gradle-project-app-build-gradle.png)

### Ndk Integration

The experimental plugin comes with NDK integration for creating native applications.  To use the NDK integration:

* Use the SDK Manager inside Studio to download the NDK.
* Set ndk.dir in local.properties or the `ANDROID_NDK_HOME` environment variable to the directory containing the NDK.
* Add an `android.ndk` block to the model in build.gradle. 

### Known Limitations

There’s no support for NDK-only modules. The only supported project types are hybrid app projects and hybrid Library Projects.

* Consumed Library project don’t impact compilation of jni code in the consuming project (ie the AAR so files are simply packaged in the APK)
* No support for creating and depending on static libraries
* No support for using a NDK modules like cpu_features
* No support for integrating external build systems.

The build.gradle of a simple NDK application may look like this:

![image](/images/notes_images/gradke-project-android-ndk-model-name.png)

*Note that the moduleName is **required**.  It determines the name of the resulting native library.

By default, it will look in src/main/jni for C/C++ file.  Configure android.sources to change the source directory.

```
model {
    android {
        compileSdkVersion = 22
        buildToolsVersion = "22.0.1"
    }
    android.ndk {
        moduleName = "native"
    }
    android.sources {
        main {
            jni {
                source {
                    srcDir 'src'
                }
            }
        }
    }
}
```


Various build options can be set within the `android.ndk { }` block.  For example, 

```
model {
    android {
        compileSdkVersion = 22
        buildToolsVersion = "22.0.1"
    }
    android.ndk {
        // All configurations that can be changed in android.ndk.
        moduleName = "native"
        toolchain = "clang"
        toolchainVersion = "3.5"
        // Note that CFlags has a capital C, which is inconsistent with
        // the naming convention of other properties.  This is a
        // technical limitation that will be resolved
        CFlags += "-DCUSTOM_DEFINE"
        cppFlags += "-DCUSTOM_DEFINE"
        ldFlags += "-L/custom/lib/path"
        ldLibs += "log"
        stl = "stlport_static"
    }
    android.buildTypes {
        release {
            ndk.with {
                debuggable = true
            }
        }
    }
    android.productFlavors {
        create("arm") {
            ndk.with {
                // You can customize the NDK configurations for each
                // productFlavors and buildTypes.
                abiFilters += "armeabi-v7a"
            }
        }
        create("fat") {
            // If ndk.abiFilters is not configured, the application
            // compile and package all suppported ABI.
        }
    }

    // You can modify the NDK configuration for each variant.
    components.android {
        binaries.afterEach { binary ->
            binary.mergedNdkConfig.cppFlags.add(
                    "-DVARIANT=\"" + binary.name + "\"")
        }
    }
}		
```


### Samples

Additional samples can be found at [https://github.com/googlesamples/android-ndk](https://github.com/googlesamples/android-ndk).


原文链接：[http://tools.android.com/tech-docs/new-build-system/gradle-experimental](http://tools.android.com/tech-docs/new-build-system/gradle-experimental)

----
版权声明：版权归原网站所有。
