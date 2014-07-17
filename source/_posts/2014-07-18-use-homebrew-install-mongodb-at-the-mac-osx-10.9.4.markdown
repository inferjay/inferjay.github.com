---
layout: post
title: "Mac OSX 10.9.4下使用Homebrew安装MongoDB"
date: 2014-07-18 00:09:28 +0800
comments: true
categories: MongoDB
---

##安装Homebrew

>Homebrew是Mac OSX下一个包依赖管理工具，用它来安装软件非常的方便只需要`brew install 软件名`这一条命令就可以将你所需要的软件安装好，不用再操心安装过程中软件的依赖问题，这些问题Homebrew统统帮你搞定，Homebrew的官网：[http://brew.sh/](http://brew.sh/)  在官网上对Homebrew的安装和用法有详细的描述这里就不多扯了。如果你还没有安装，还等什么赶紧动手安装吧！:]

Homebrew安装方法也很简单：打开终端，然后输入命令

	ruby -e "$(curl -fsSL https://raw.github.com/mxcl/homebrew/go)"

对，就是这么简单一条命令就可以将神器Homebrew安装好.

##安装MongoDB

>MongoDB是一个基于分布式文件存储的数据库。由C++语言编写。旨在为WEB应用提供可扩展的高性能数据存储解决方案。
MongoDB是一个介于关系数据库和非关系数据库之间的产品，是非关系数据库当中功能最丰富，最像关系数据库的。他支持的数据结构非常松散，是类似json的bson格式，因此可以存储比较复杂的数据类型。Mongo最大的特点是他支持的查询语言非常强大，其语法有点类似于面向对象的查询语言，几乎可以实现类似关系数据库单表查询的绝大部分功能，而且还支持对数据建立索引.

进入正题开始用Homebrew安装主角MongoDB～～～

在终端输入如下命令即可安装MongoDB

	brew install mongodb
	
用Homebrew安装软件是不是很爽，耐心等待一会儿MongoDB就会安装好了......

<!--more-->

##测试MongoDB是否安装成功

在终端输入如下命令进行测试

	mongod
	
**`mongod`**命令是用来启动MongoDB的服务的

执行`mongos`命令后返回了一堆错误信息，如下：
	
	mongod --help for help and startup options
	2014-07-17T21:34:21.732+0800 [initandlisten] MongoDB starting : pid=13117 port=27017 	dbpath=/data/db 64-bit host=zhcmacbook-pro.local
	2014-07-17T21:34:21.732+0800 [initandlisten] db version v2.6.3
	2014-07-17T21:34:21.732+0800 [initandlisten] git version: nogitversion
	2014-07-17T21:34:21.732+0800 [initandlisten] build info: Darwin minimavericks.local 13.2.0 	Darwin Kernel Version 13.2.0: Thu Apr 17 23:03:13 PDT 2014; root:xnu-2422.100.13~1/	RELEASE_X86_64 x86_64 BOOST_LIB_VERSION=1_49
	2014-07-17T21:34:21.732+0800 [initandlisten] allocator: tcmalloc
	2014-07-17T21:34:21.732+0800 [initandlisten] options: {}
	2014-07-17T21:34:21.733+0800 [initandlisten] exception in initAndListen: 10296
	*********************************************************************
 	ERROR: dbpath (/data/db) does not exist.
 	Create this directory or give existing directory in --dbpath.
 	See http://dochub.mongodb.org/core/startingandstoppingmongo
	*********************************************************************
	, terminating
	2014-07-17T21:34:21.733+0800 [initandlisten] dbexit:
	2014-07-17T21:34:21.733+0800 [initandlisten] shutdown: going to close listening sockets...
	2014-07-17T21:34:21.733+0800 [initandlisten] shutdown: going to flush diaglog...
	2014-07-17T21:34:21.733+0800 [initandlisten] shutdown: going to close sockets...
	2014-07-17T21:34:21.733+0800 [initandlisten] shutdown: waiting for fs preallocator...
	2014-07-17T21:34:21.733+0800 [initandlisten] shutdown: lock for final commit...
	2014-07-17T21:34:21.733+0800 [initandlisten] shutdown: final commit...
	2014-07-17T21:34:21.733+0800 [initandlisten] shutdown: closing all files...
	2014-07-17T21:34:21.733+0800 [initandlisten] closeAllFiles() finished
	2014-07-17T21:34:21.733+0800 [initandlisten] dbexit: really exiting now

这么一堆错误信息，重点是这几句

 	ERROR: dbpath (/data/db) does not exist.
 	Create this directory or give existing directory in --dbpath.
 	See http://dochub.mongodb.org/core/startingandstoppingmongo
 	
错误信息的意思是`dbpath (/data/db)`不存在，需要创建`/data/db`这个目录或者使用`--dbpath`参数项指定一个已经存在的目录。

	dbpath (/data/db)这个目录是用来存储MongoDB数据文件的地方

既然不存在`/data/db`那我们就创建这个目录

首先在终端输入`cd /`命令返回到磁盘根目录

然后输入`mddir -p /data/db`创建`/data/db`

再次输入`mongos`命令启动MongoDB的服务，服务又退出了返回一堆信息如下：

	mongod --help for help and startup options
	2014-07-17T23:56:40.285+0800 [initandlisten] MongoDB starting : pid=21182 port=27017 	dbpath=/data/db 64-bit host=zhcmacbook-pro.local
	2014-07-17T23:56:40.285+0800 [initandlisten] db version v2.6.3
	2014-07-17T23:56:40.285+0800 [initandlisten] git version: nogitversion
	2014-07-17T23:56:40.285+0800 [initandlisten] build info: Darwin minimavericks.local 13.2.0 	Darwin Kernel Version 13.2.0: Thu Apr 17 23:03:13 PDT 2014; root:xnu-2422.100.13~1/	RELEASE_X86_64 x86_64 BOOST_LIB_VERSION=1_49
	2014-07-17T23:56:40.285+0800 [initandlisten] allocator: tcmalloc
	2014-07-17T23:56:40.285+0800 [initandlisten] options: {}
	2014-07-17T23:56:40.287+0800 [initandlisten] exception in initAndListen: 10309 Unable to 	create/open lock file: /data/db/mongod.lock errno:13 Permission denied Is a mongod 	instance already running?, terminating
	2014-07-17T23:56:40.287+0800 [initandlisten] dbexit:
	2014-07-17T23:56:40.287+0800 [initandlisten] shutdown: going to close listening sockets...
	2014-07-17T23:56:40.287+0800 [initandlisten] shutdown: going to flush diaglog...
	2014-07-17T23:56:40.287+0800 [initandlisten] shutdown: going to close sockets...
	2014-07-17T23:56:40.287+0800 [initandlisten] shutdown: waiting for fs preallocator...
	2014-07-17T23:56:40.287+0800 [initandlisten] shutdown: lock for final commit...
	2014-07-17T23:56:40.287+0800 [initandlisten] shutdown: final commit...
	2014-07-17T23:56:40.287+0800 [initandlisten] shutdown: closing all files...
	2014-07-17T23:56:40.288+0800 [initandlisten] closeAllFiles() finished
	2014-07-17T23:56:40.288+0800 [initandlisten] shutdown: removing fs lock...
	2014-07-17T23:56:40.288+0800 [initandlisten] couldn't remove fs lock errno:9 Bad file 	descriptor
	2014-07-17T23:56:40.288+0800 [initandlisten] dbexit: really exiting now
	
出错原因是这几句log

	create/open lock file: /data/db/mongod.lock errno:13 Permission denied Is a mongod 	instance already running?, terminating
	
意思是不创建和打开`/data/db/mongod.lock`这个文件，原因是**`Permission denied`**（权限拒绝），看来是当前用户执行`mongod`这个命令时，对`/data/db`这个目录没有操作权限，OK，知道原因就好办了，给`/data/db`加上权限。

在终端输入如下命令：

	sudo chown -R  当前登录的用户名 /data
	
再次在终端输入`mongod`启动MongoDB的服务，返回如下信息：

	mongod --help for help and startup options
	2014-07-18T01:29:23.286+0800 [initandlisten] MongoDB starting : pid=22420 port=27017 	dbpath=/data/db 64-bit host=zhcmacbook-pro.local
	2014-07-18T01:29:23.286+0800 [initandlisten] db version v2.6.3
	2014-07-18T01:29:23.286+0800 [initandlisten] git version: nogitversion
	2014-07-18T01:29:23.286+0800 [initandlisten] build info: Darwin minimavericks.local 13.2.0 	Darwin Kernel Version 13.2.0: Thu Apr 17 23:03:13 PDT 2014; root:xnu-2422.100.13~1/	RELEASE_X86_64 x86_64 BOOST_LIB_VERSION=1_49
	2014-07-18T01:29:23.286+0800 [initandlisten] allocator: tcmalloc
	2014-07-18T01:29:23.287+0800 [initandlisten] options: {}
	2014-07-18T01:29:23.288+0800 [initandlisten] journal dir=/data/db/journal
	2014-07-18T01:29:23.288+0800 [initandlisten] recover : no journal files present, no 	recovery needed
	2014-07-18T01:29:23.437+0800 [initandlisten] waiting for connections on port 27017

终于启动成功了，然后再打开一个新的终端窗口输入`mongo`命令，返回如下信息：

	MongoDB shell version: 2.6.3
	connecting to: test
	Welcome to the MongoDB shell.
	For interactive help, type "help".
	For more comprehensive documentation, see
	http://docs.mongodb.org/
	Questions? Try the support group
	http://groups.google.com/group/mongodb-user
	>

哦耶！，成功进入MongoDB的命令行模式，到这里MongoDB就安装好了！:] :] :]......

----
版权声明：欢迎自由转载-非商用-非衍生-保持署名 | [Creative Commons BY-NC-ND 3.0](http://creativecommons.org/licenses/by-nc-nd/3.0/deed.zh)

