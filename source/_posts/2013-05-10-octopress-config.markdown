---
layout: post
title: "对Octopress做一些个性化配置"
date: 2013-05-10 20:11
comments: true
categories: Octopress
---

###首页只显示部分正文

在文章任意位置加入
```<!--more-->```
	
这样首页只显示一部分正文，并在每篇文章底下加一个```Read on```超链接。

然后在```_config.yml```文件中修改```excerpt_link```的值```Rade on```为以你想要的，比如```More```

