---
layout: post
title: "Ubuntu下彻底卸载并重新安装MySQL"
date: 2014-05-17 18:45
comments: true
categories: Ubuntu MySQL
---

###Step1.卸载MySQL
终端下依次输入以下命令卸载MySQL

	sudo apt-get autoremove --purge mysql-server-5.0
	sudo apt-get remove mysql-server
	sudo apt-get autoremove mysql-server
	sudo apt-get remove mysql-common(非常重要)
	sudo apt-get remove apparmor

上面的其实有一些是多余的，建议还是按照顺序执行一遍

###Step2.清理残留数据

	dpkg -l |grep ^rc|awk '{print $2}' |sudo xargs dpkg -P

<!--more-->
###Step3.删除残余目录
	sudo rm /var/lib/mysql/ -R
	sudo rm /etc/mysql/ -R

###Step4.安装 mysql
	sudo apt-get install mysql-server mysql-client
	sudo apt-get install php5-mysql 	//(安装php5-mysql 是将php和mysql连接起来 ) 
 
###Step5.验证是否安装成功
一旦安装完成，MySQL 服务器应该自动启动。您可以在终端提示符后运行以下命令来检查 MySQL 服务器是否正在运行：

	netstat -tap | grep mysql

当您运行该命令时，您可以看到类似下面的行：

`tcp 0 0 localhost.localdomain:mysql *:* LISTEN`

如果服务器不能正常运行，您可以通过下列命令启动它：

	sudo /etc/init.d/mysql restart

###Step6.配置管理员密码
进入mysql命令行模式

	mysql -uroot -p 管理员密码

配置 MySQL 的管理员密码

	mysqladmin -u root password newpassword
	

----
版权声明：欢迎自由转载-非商用-非衍生-保持署名 | [Creative Commons BY-NC-ND 3.0](http://creativecommons.org/licenses/by-nc-nd/3.0/deed.zh)

