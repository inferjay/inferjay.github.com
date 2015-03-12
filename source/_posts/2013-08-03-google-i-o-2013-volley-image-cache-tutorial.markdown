---
layout: post
title: "[译]Google　I/O 2013:Volley 图片缓存教程"
date: 2013-08-03 11:28
comments: true
categories: Android
---
Gooogle I/O 2013已经结束了，并且对于Android开发的未来它给我们留下了更大的期望。令人非常兴奋的是在今年的I/O大会上展示了一个叫[```Volley```](https://android.googlesource.com/platform/frameworks/volley/)的库。```Volley```是一个处理和缓存网络请求的库，减少开发人员在实际的每一个应用中写同样的样板代码。写样板代码是很无聊的并且也增加了开发人员出错的几率。Google是出于这些考虑创建了```Volley```。

如果你还没有看过[Gooogle　I/O中关于Volley的介绍](https://developers.google.com/events/io/sessions/325304728)，在继续这篇文章之前我建议你先去看看关于```Volley```的介绍，对它有一些基本的理解。

在Google I/O介绍的时候，Ficus Kirpatrick讲了很多关于Volley如何的有助于图片加载。你会发现在Volley作为你的图片加载解决方案的时候，虽然Volley自己处理了L2的缓存，它需要但是没有包含L1的缓存。许多人会使用像[Universal Image Loader](https://github.com/nostra13/Android-Universal-Image-Loader)或者[Square`s newer Picasso](https://github.com/square/picasso)这些第三方的库去处理图片的加载；然而这些库通常已经同时处理了图片的加载和缓存。所以，我们如何使用Volley来替换图片的加载和缓存呢？首先，让我们看看Volley提供的便利的加载方法，我们稍后再看他们的不同之处。

<!--More-->

####ImageLoader

 ```ImageLoader```这个类需要一个```Request```的实例以及一个```ImageCache```的实例。图片通过一个```URL```和一个```ImageListener```实例的```get()```方法就可以被加载。从哪里，```ImageLoader```会检查```ImageCache```,而且如果缓存里没有图片就会从网络上获取。

####NetworkImageView

这个类在布局文件中替换```ImageViews```，并且将使用```ImageLoader```。```NetworkImageView```的```setUrl()```这个方法需要一个字符串的URL路径以及一个```ImageLoader```的实例。然后它使用```ImageLoader```的```get()```方法获取图片数据。

```
<com.android.volley.toolbox.NetworkImageView
    android:id="@+id/twitterUserImage"
    android:layout_alignParentLeft="true"
    android:layout_alignParentTop="true"
    android:layout_width="40dp"
    android:layout_height="40dp"
    android:padding="5dp"
    />

```

####ImageCache
 
 ```Volley```的```ImageCache```接口允许你使用你喜欢的L1缓存实现。不幸的是```Volley```没有提供默认的实现。在I/O的介绍中展示了```BitmapLruCache```的一点代码片段，但是```Volley```这个库本身并不包含任何相关的实现。

 ```ImageCache```接口有两个方法，```getBitmap(String url)```和```putBitmap(String url, Bitmap bitmap)```.这两个方法足够简单直白，他们可以添加任何的缓存实现。

####在Volley中添加图片缓存

对于这个例子，我创建了[一个简单的应用](https://github.com/rdrobinson3/VolleyImageCacheExample)，它从Twitter上搜索提取“CapTech”这个词的推文，并且把包含这个词的推文的用户名和照片显示在一个ListView中，在你滑动这个列表的时候将自动加载以前的记录，并根据需要从缓存中拉取图片。

![alt text](/images/notes_images/volley-example.png "volley-example")

例子里有２个可用的缓存实现。一个基于内存的LRU缓存。对于磁盘缓存实现我选择使用由Jake Wharton写的[DiskLruCache](https://github.com/JakeWharton/DiskLruCache)。我只所以选择这个实现是因为他在Android社区中被经常使用和推荐的并且有一些人试图去改进它。使用一个基于磁盘的L1缓存有可能导致i/o阻塞的问题。```Volley```已经内置了一个磁盘L2缓存。磁盘L1缓存包括在内了，由于我原来不知道```Volley```是如何处理图片请求缓存的。

###在这个例子中主要的组件实现如下:

####RequestManager

 ```RequestManager```维护了我们的一个```RequestQueue```的引用。```Volley```使用RequestQueue不仅处理了我们给Twitter的数据请求，而且也处理了我的的图片加载。

####GsonRequest

这个类虽然跟图片加载没有直接的关系，但是它是具有代表性，它是如何继承```Volley```的```Request```类去处理你的JSON解析。它使用GET请求到Twtter并获取```TwitterData```对象。

####BitmapLruImageCache

这是一个基于```“least recently used(最近最少使用算法,简称LRU)”```内存缓存实现。它是快速的并且不会引起I/O阻塞的。推荐这种方法。

####DiskLruImageCache

 ```DiskLruImageCache```是一个```DiskLruCache```和```bitmap-centered```的包装实现。它从```DiskLruCache```中获取和添加```bitmaps```，并且处理缓存的实例。一个磁盘缓存或许会引起I/O的阻塞。

####ImageCacheManager

 ```ImageCacheManager```持有一个我们的```ImageCache```和```Volley ImageLoader```的引用。

有一件事情你要注意，在ImageCacheManager中我们使用了字符串URL的hashCode()值作为缓存的Key。这么做是因为在URL中的某些字符不能作为缓存的Key。

####BuzzArrayAdapter

这是一个简单的Adapter。这里唯一要注意的是我们实现了Volley的Listener和ErrListener接口并且将这个Adapter作为
NetworkImageView’s的setUrl(String string , Listener listener, ErrorListener errorListener) 方法的Listener。这个Adapter还有一点额外的代码，用来在滚动的时候加载老的推文。

```
Tweet tweet = mData.get(position);
if(tweet != null){
viewHolder.twitterUserImage.setImageUrl(tweet.getUserImageUrl(),ImageCacheManager.getInstance().getImageLoader());
	viewHolder.userNameTextView.setText("@" + tweet.getUsername());
	viewHolder.messageTextView.setText(tweet.getMessage());
	viewHolder.tweetTimeTextView.setText(formatDisplayDate(tweet.getCreatedDate()));
	viewHolder.destinationUrl = tweet.getDestinationUrl();
}
```

####Putting it all together

把这些组件组合在一起，图片加载和缓存是如此的简单。在启动时，在```MainApplication```这个类中初始化```RequestManager```和```ImageCacheManager```。在那里你可以定义你想要的L1缓存类型。内存缓存是默认的。

在```MainActivity```中我们调用```TwitterManager```并且加载我们的的初始数据集。一旦我们接收到响应我们就把这个响应传递给一个```BuzzArrayAdapter```并把这个```Adapter```设置到我们的```ListView```上。

正如我们已经在上面看到了```BuzzArrayAdapter```的代码,对于```NetworkImageView```所有繁重的图片加载操作我们仅仅需要从我们的```ImageCacheManager```中获取一个ImageLoader的实例传递给它就可以了。

 ```ImageCacheManager```会检查我的LRU缓存实现并且如果这个图片是可用的就会返回它。如果这个图片不在在缓存中那么就从网络获取它。

当你滚动```ListView```的时候```BuzzArrayAdapter```将一起加载额外的推文和图片，并且重用在缓存中已经存在的图片。

####Closing Thoughts on Volley
虽然```Volley```是好用的，快速的，很容易实现的；但他也有一些不足的地方:

* 这个库没有任何的文档和例子。
* 比如缓存配置组件，他们为什么不做成可配置的。
* 从以上可以看出，除了一个比较奇怪的基本图片缓存实现以外,它甚至可能有使用NoImageCache的实现,或者在缓存完全可选的时候，你只是想从网络上获取任何东西。

关于Volley在开发者社区中有很多令人激动的事情，并且有很的好的理由。感觉它就像一个库，它包含了一部分旧的Android API.像在I/O上公布的新的定位API,它是非常纯净的~~~~

####Github上的例子源码

[VolleyImageCacheExample](https://github.com/rdrobinson3/VolleyImageCacheExample)

####附件:

[VolleyImageCacheExample-master.zip](http://blogs.captechconsulting.com/sites/default/files/VolleyImageCacheExample-master.zip)

####原文作者:

Trey Robinson

####原文地址:

[http://blogs.captechconsulting.com/blog/raymond-robinson/google-io-2013-volley-image-cache-tutorial](http://blogs.captechconsulting.com/blog/raymond-robinson/google-io-2013-volley-image-cache-tutorial)

####关于本文

前端时间偶然间看到了```Volley```，最近开始做一个新项目又重新去看了看I/O中关于```Volley```的介绍,感觉```Volley```很牛逼，于是就想在新项目中试试```Volley```,找来找去关于```Volley```的资料几乎没有，在Google中找到了这篇文章，看了觉得不错，于是就决定翻译一下，一来加深自己的理解，二来呢顺便补一下自己那惨不忍睹的英语。由于本人英语水平比较烂，翻译的时候有可能会曲解原作者的意思，建议英语好的大牛飘过此文去看作者的原文，欢迎大家吐槽和拍砖，觉得译文中有那些地方我翻译的不妥的地方欢迎回复指正，我们相互学习～～～


----
版权声明：原创译文欢迎自由转载-非商用-非衍生-保持署名 | [Creative Commons BY-NC-ND 3.0](http://creativecommons.org/licenses/by-nc-nd/3.0/deed.zh)