---
layout: post
title: "Mac下解决无法更新Android Studio的问题"
date: 2013-09-26 12:53
comments: true
categories: Android
---
	
昨天在G+中看到Android Studio又有更新了就心血来潮想去更新体验一下，可是无论我怎么点Check Update却一直提示`Connection failed. Please check your network connection and try again`，开始以为是由于G*W在捣乱，但是设置了Proxy后还是无法更新，然后开始问谷哥找到了下面的解决办法。

首先打开`Finder`在左边选择`Application`目录，在右边找到`Android Studio.app`,然后右击选择“显示包内容”如下图：

![alt text](/images/notes_images/android_studio_in_finder.png "android_studio_in_finder")

<!--More-->

接着用自己趁手的编辑器打开bin目录下的idea.vmoptions文件添加如下内容：

```
-Djava.net.preferIPv4Stack=true  
-Didea.updates.url=http://dl.google.com/android/studio/patches/updates.xml  
-Didea.patches.url=http://dl.google.com/android/studio/patches/  
```
保存后，重新打开`Android Studio`点击Check Update就会弹出更新信息提示了

![alt text](/images/notes_images/check_update_info.png "check_update_info")

如果仍然无效，将url里的修改`http`为`https`，然后重启点击Check Update试试~~~