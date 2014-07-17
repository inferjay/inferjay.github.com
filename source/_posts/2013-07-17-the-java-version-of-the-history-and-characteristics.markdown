---
layout: post
title: "[转]Java 的版本历史与特性"
date: 2013-07-17 15:53
comments: true
categories: Java
---

一个比较流行的问题是，“Java下一个版本会有什么特性呢？” 。这是否是个好问题却有待商榷。在下面的内容里，我总结了至今为止的Java主要发行版中各自引入的新特性，这样做的目的是为了突出各个新特性是在哪个发行版中引入的。除了列出的特性外，每个发行版还做了很多优化和修复BUG的工作。

### Java Version SE 7 

开发代号是Dolphin（海豚），于2011-07-28发行。

![alt text](/images/notes_images/dolphin.png "Dolphin")

引入的新特性包括：

* switch语句块中允许以字符串作为分支条件；
* 在创建泛型对象时应用类型推断；
* 在一个语句块中捕获多种异常；
* 支持动态语言；
* 支持try-with-resources；
* 引入Java NIO.2开发包；
* 数值类型可以用2进制字符串表示，并且可以在字符串表示中添加下划线；
* 钻石型语法；
* null值的自动处理。

<!--more-->

###Java Version SE 6 

开发代号为Mustang（野马），于2006-12-11发行。

![alt text](/images/notes_images/Mustang.png "Mustang")

引入的新特性包括：

* 支持脚本语言；
* 引入JDBC 4.0 API；
* 引入Java Compiler API；
* 可插拔注解；
* 增加对Native PKI(Public Key Infrastructure)、Java GSS(Generic Security Service)、Kerberos和LDAP(Lightweight Directory Access Protocol)的支持；
* 继承Web Services；
* 做了很多优化。

### Java Version SE 5.0 

开发代号为Tiger（老虎），于2004-09-30发行。

![alt text](/images/notes_images/tiger.jpg "Tiger")

引入的新特性包括:

* 引入泛型；
* 增强循环，可以使用迭代方式；
* 自动装箱与自动拆箱；
* 类型安全的枚举；
* 可变参数；
* 静态引入；
* 元数据（注解）；
* 引入Instrumentation。

###J2SE Version 1.4 

开发代号为Merlin（隼），于2004-02-06发行（首次在JCP下发行）。

![alt text](/images/notes_images/merlin.jpg "Merlin")

引入的新特性包括:

* XML处理；
* Java打印服务；
* 引入Logging API；
* 引入Java Web Start；
* 引入JDBC 3.0 API；
* 引入断言；
* 引入Preferences API；
* 引入链式异常处理；
* 支持IPv6；
* 支持正则表达式；
* 引入Image I/O slot machine API。

###J2SE Version 1.3 

开发代号为Kestrel（红隼），于2000-05-08发行。

![alt text](/images/notes_images/Kestrel.jpg "Kestrel")

引入的新特性包括：

* 引入Java Sound API；
* jar文件索引；
* 对Java的各个方面都做了大量优化和增强。

###J2SE Version 1.2 

开发代号为Playground（操场），于1998-12-08发行。

引入的新特性包括：

* 引入集合（Collection）框架；
* 对字符串常量做内存映射；
* 引入JIT（Just In Time）编译器；
* 引入对打包的Java文件进行数字签名；
* 引入控制授权访问系统资源的策略工具；
* 引入JFC（Java Foundation Classes），包括Swing 1.0、拖放和Java 2D类库；
* 引入Java 插件；
* 在JDBC中引入可滚动结果集、BLOB、CLOB、批量更新和用户自定义类型；
* 在Applet中添加声音支持。

###JDK Version 1.1 

于1997-02-19发行。

引入的新特性包括：

* 引入JDBC（Java Database Connectivity）；
* 支持内部类；
* 引入Java Bean；
* 引入RMI（Remote Method Invocation）；
* 引入反射（仅用于内省）。

###JDK Version 1.0 

开发代号为Oak（橡树），于1996-01-23发行。


英文原文：javapapers ，编译：ImportNew  - 曹旭东
译文地址： http://www.importnew.com/844.html

----
版权声明：欢迎自由转载-非商用-非衍生-保持署名 | [Creative Commons BY-NC-ND 3.0](http://creativecommons.org/licenses/by-nc-nd/3.0/deed.zh)