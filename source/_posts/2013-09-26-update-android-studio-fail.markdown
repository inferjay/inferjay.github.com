---
layout: post
title: "Android Studio 自动更新失败解决办法"
date: 2014-12-26 12:53
comments: true
categories: Android
---
	
昨天在G+中看到Android Studio又有更新了就心血来潮想去更新体验一下，可是无论我怎么点Check Update却一直提示`Connection failed. Please check your network connection and try again`，开始以为是由于G*W在捣乱，但是打开VPN后还是无法更新，然后开始Google了一下找到了下面的解决办法。

![image](/images/notes_images/android-studio-update-error.png "android-studio-update-error")

### Mac OSX
首先打开`Finder`在左边选择`Application`目录，在右边找到`Android Studio.app`,然后右击选择“显示包内容”如下图：

![alt text](/images/notes_images/android_studio_in_finder.png "android_studio_in_finder")

<!--More-->

然后找到 `Contents/bin/` 目录下的 `.vmoptions` 后缀的文件

![image](/images/notes_images/mac-studio-vmoptions.png "mac-studio-vmoptions")

### Linux
找到 `bin/` 目录下的 `studio.vmoptions` (32位系统) 或者 `studio64.vmoptions` (64位系统)文件

![image](/images/notes_images/linux-studio-vmoptions.png "linux-studio-vmoptions")

### Windows
找到 `bin/` 目录下的 `studio.vmoptions` (32位系统) 或者 `studio64.exe.vmoptions` (64位系统)文件

![image](/images/notes_images/windows-studio-vmoptions.png "windows-studio-vmoptions")


接着用自己趁手的编辑器打开 `idea.vmoptions` 或者 `studio.vmoptions/studio64.vmoptions` 或者 `studio.exe.vmoptions/studio64.exe.vmoptions` 文件添加如下内容：

```
-Djava.net.preferIPv4Stack=true  
-Didea.updates.url=http://dl.google.com/android/studio/patches/updates.xml  
-Didea.patches.url=http://dl.google.com/android/studio/patches/  
```
保存后，重新打开`Android Studio`点击Check Update就会弹出更新信息提示了

![alt text](/images/notes_images/android-studio-update-info.jpg "android-studio-update-info")

如果仍然无效，将url里的修改`http`为`https`，然后重启点击Check Update试试~~~

----
版权声明：欢迎自由转载-非商用-非衍生-保持署名 | [Creative Commons BY-NC-ND 3.0](http://creativecommons.org/licenses/by-nc-nd/3.0/deed.zh)