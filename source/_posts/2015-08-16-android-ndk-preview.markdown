---
layout: post
title: "Android NDK Preview"
date: 2015-08-16 00:48:52 +0800
comments: true
categories: NDK
---


We’ve just released another update to Android Studio 1.3's canary channel, Release Candidate 1.

This build contains a big new feature: Android NDK support, with support for editing, running and debugging C and C++ code! Note however that NDK support is only preview quality, and will remain in preview status for the upcoming final 1.3 release. There are many significant limitations (described below), but we hope that the preview will be useful for many Android NDK developers even in its current state!

### Limitations

First, the Android NDK support only works with the new experimental Gradle plugin for Android (which in turn requires Gradle 2.5, released yesterday.)

While the new gradle plugin delivers some major performance improvements (and Android NDK build support), note that it also requires changes to the build file “DSL” (the way your build is described in your build.gradle files.) Not only will you need to modify your build.gradle files (a process we hope to automate before the plugin graduates from experimental to stable), but we anticipate making some additional incompatible changes along the way.

<!--more-->

Second, note also that Android Studio has not yet been updated to fully handle the experimental plugin. This means that for example the Project Structure Dialog, and various quick fixes which automatically update the build data, do not work correctly. You’ll need to edit your build.gradle files manually to configure your projects. As another example, the various templates which update the build files (such as New Module), have not yet been updated.

Finally, there are still some known bugs in this build; when debugging with LLDB breakpoints do not always work on Windows; if you run into this, you can switch to GDB debugging as a temporary workaround.

### Bundled Android NDK

Historically, the Android NDK was distributed separately from the Android SDK, as a giant .zip file download, and installed in a separate location from where the Android SDK resides.

In this release we’ve tried to unify this; the NDK should now be installed under the SDK home in “ndk-bundle”. We’ve updated the IDE built-in SDK Manager to let you install the NDK in the right place, and the Project Structure Dialog’s SDK Location panel (where you can edit the locations), now also offers a quick link to install it directly; just click on the Download hyperlink shown below:

Note that the NDK is a large download (~1 GB!) so unless you have a really great network connection, prepare to be patient...

Speaking of large downloads, note also that the IDE update patches from previous 1.3 builds to this one are really large: not only does this build contain all the new code to support Android NDK development, but it also contains LLDB binaries (~200 MB uncompressed). This is just a temporary situation; we'll move these into the NDK.

### How to get started

Since the Android NDK support depends on an experimental plugin, with an incompatible build file syntax which we know will change over the next few months, we have not added a simple way to create Android NDK projects. Instead, to explore the Android NDK support, either

1. Follow the [Experimental Gradle Plugin](http://tools.android.com/tech-docs/new-build-system/gradle-experimental) document which describes how to modify your project to use the current version of the experimental plugin, or

2. Check out some of the existing Android NDK samples which have been updated to work with the new Gradle plugin.

To check out the samples, invoke VCS > Checkout from Version Control > Git, and in the resulting dialog point to [https://github.com/googlesamples/android-ndk.git](https://github.com/googlesamples/android-ndk.git) . Pick a location to check out the samples into. **NOTE:** At the end of the import the IDE will ask you whether you want to create a project from the checked out sources. Answer no. The android-ndk git project contains a large number of separate projects. Instead, once the import is done, go to File > Open..., and navigate to (for example) the Teapot folder and select the file build.gradle in that folder. The project will now be opened and synced. This will take a while the first time as the new Gradle plugin and dependencies are downloaded. When you’re done you can look at the project sources and start exploring the IDE support for Android NDK.

For example, in the Project View you’ll see that the project has a “jni” source set next to the normal “java” source set; that’s where you’ll find the native code:

![ndk-jni-folder](/images/notes_images/ndk-jni-folder.png)

You can now build & run the project, and explore the various editing features:

* Code completion
* Code navigation (go to declaration, jump between header and implementation, etc)
* Quick fixes
* Intentions
* Refactoring
* Source format
…

![ndk-jni-editor-small](/images/notes_images/ndk-jni-editor-small.png)

### Debugging

To debug the project, you'll need to create a **native** run configuration. Invoke Run > Edit Configurations..., then choose the Native configuration type:

![ndk-native](/images/notes_images/ndk-native.png)

Name the configuration, and choose the module to run. If you run into bugs with the LLDB debugger in this version of the IDE, you can try switching to the GDB debugger instead as a workaround.

![ndk-native2](/images/notes_images/ndk-native2.png)

原文链接：[http://tools.android.com/tech-docs/android-ndk-preview](http://tools.android.com/tech-docs/android-ndk-preview)

----
版权声明：版权归原网站所有。