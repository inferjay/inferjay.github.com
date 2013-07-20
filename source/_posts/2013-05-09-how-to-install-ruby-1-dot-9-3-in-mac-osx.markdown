---
layout: post
title: "如何在Mac OS X下升级Ruby到1.9.3版本"
date: 2013-05-09 21:34
comments: true
categories: Ruby
---
###安装环境

	* Mac OSX 10.8.3
	* Xcode 4.6.1
	* Ruby 1.8.7

###第一步:安装rvm

为什么要安装rvm呢，因为rvm可以让你拥有多个版本的ruby，并且可以在多个版本之间自由切换。安装方式有两种：单用户模式和多用户模式.而单用户和多用户的区别在于是否有root权限，单用户模式rvm会安装在~/.rvm目录下,而多用户模式则会安装在/usr/local/rvm目录下。

######单用户安装命令：

	bash < <(curl -s https://raw.github.com/wayneeseguin/rvm/master/binscripts/rvm-installer )

######多用户安装命令：

	sudo bash < <(curl -s https://raw.github.com/wayneeseguin/rvm/master/binscripts/rvm-installer )

单用户需要设置环境变量，多用户安装重启就行了，确认rvm安装是否成功
在终端输入：

	rvm -v
	
接着会看到rvm的版本信息如下：

	rvm 1.19.6 () by Wayne E. Seguin <wayneeseguin@gmail.com>, Michal Papis <mpapis@gmail.com> 	[https://rvm.io/]

<!--more-->

###第二步:安装ruby

######查看是否安装gcc编译器

在开始安装ruby前请确认你是否安装了gcc，如果你安装了Xcode，请检查是否安装了command line tools, 打开Xcode，在Xcode -> Preference -> Download下，查看“Command Line tools”这一项是否为“installed”
如下图：
![alt text](/images/notes_images/xcode_download_info.png "xcode_download_info")

如果不是installed，点击install安装或者下载[OSX-GCC-Installer](https://github.com/kennethreitz/osx-gcc-installer "OSX-GCC-Installer")来安装gcc，不然在安装ruby编译的时候会报误（每个人的环境不一样报的错误也会不一样），如下图：

![alt text](/images/notes_images/install_ruby_error_info.png "install_ruby_error_info")

######查看所有可以安装的Ruby版本

	rvm list known

接着会看到各种版本的Ruby

######安装Ruby

	rvm install ruby --head

######设置Ruby默认版本为1.9.3版本

	rvm use 1.9.3 --default

###第三步:更换源

######查看已有的安装源

	gem source

显示如下：
 
CURRENT SOURCES

http://rubygems.org/

######修改更新源

	gem update --system
	gem uninstall rubygems-update
	gem sources -r http://rubygems.org/
	gem sources -a http://ruby.taobao.org