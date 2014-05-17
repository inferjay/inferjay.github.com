---
layout: post
title: "对Octopress做一些个性化配置"
date: 2013-05-10 20:11
comments: true
categories: Octopress
---

###首页只显示部分正文

在文章任意位置加入

	<!--more-->
	
这样首页只显示一部分正文，并在每篇文章底下加一个```Read on```超链接。

然后在```_config.yml```文件中修改```excerpt_link```的值```Rade on```为以你想要的，比如```More```

###设置使用指定的编辑器自动打开新建的blog
每次用```rake　new_post```新建一篇blog后都要跑到```/source/_posts```找到新建的blog才能开始写blog，有木有赶脚很不爽，要是每次建完后自动用支持Markdown语法的编辑器打开那该多爽啊，google了一下还这有方法，试了式太爽了，Mark一下以免以后忘记了～～～

打开```Rakefile```文件，找到```## -- Misc Configs -- ##```这段注释，然后在```server_port```下面加入```editor = "open"```

<!--more-->
######如图所示：
![alt text](/images/notes_images/set_open_editer.png "set_open_editer") 

然后再找到```new_post```命令，在末尾加入如下代码：

```
  if #{editor}
    system "#{editor} #{filename}"
  end

```
######如图所示：
![alt text](/images/notes_images/set_open_editer_code.png "set_open_editer_code") 

这样每次用```rake　new_post```新建一篇blog的时候就会自动用指定的编辑器打开直接写blog了～～～

###设置自动打开浏览器预览生成的blog

打开```Rakefile```文件，找到```task :preview do```这段代码，在其中加入

```
system "sleep 2; open http://localhost:#{server_port}/"

```
######如图所示：
![alt text](/images/notes_images/set_auto_preview.png "set_auto_preview") 

####未完待续～～～