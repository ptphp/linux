##环境

* OS:Fedora-17-x86_64


##修改登陆界面显示信息

### ` /etc/issue ` 文件的设置的详细参数有：

	\d    本地端时间的日期
	\l    显示第几个终端机的接口;
	\m    显示硬件的等级(i386/i486/i586/i686....)
	\n    显示主机的网络名称
	\o    显示 domain name
	\r    操作系统的版本 (类似 uname-r)
	\t    显示本地端时间的时间
	\s    操作系统的名称
	\v    操作系统的版本

###使开机运行修改 ` /etc/issue `

	# vim /etc/rc.d/rc.local

增加如下信息

	#!/bin/bash
	ipaddr=$(ifconfig eth0 | grep 'inet ' | awk '{print $2}')	
	echo $'\x1b\x5b\x48\x1b\x5b\x32\x4a\x0a'' PtPHP-Server 1.0 ' > /etc/issue
	echo ' copyright@www.ptphp.com Fedora 17 x86_64 \r ' >> /etc/issue	
	echo ' ===================================' >> /etc/issue	
	echo ' IP Address : ['$ipaddr'] ' >> /etc/issue
	echo ' ===================================' >> /etc/issue	

###开机运行
	
	# chmod +x rc.local
	# systemctl restart rc-local.service



##禁用SELINUX

	sestatus -v #查看SELINUX 状态
	
	setenforce 0 #临时关闭 不需要重启 成为permissive模式
			   1 #开启设置SELinux 成为enforcing模式
	
	
	sudo sed -i "s/SELINUX=enforcing/SELINUX=disabled/g" /etc/sysconfig/selinux
	
	
	
SELinux简介

	SELinux全称是Security Enhanced Linux，由美国国家安全部(National Security Agency)领导开发的GPL项目，它拥有一个灵活而强制性的访问控制结构，旨在提高Linux系统的安全性，提供强健的安全保证，可防御未知攻击，据称相当于B1级的军事安全性能。比MS NT所谓的C2等高得多。 
	
	应用SELinux后，可以减轻恶意攻击或恶意软件带来的灾难，并提供对机密性和完整性有很高要求的信息很高的安全保障。 SELinux vs Linux 普通Linux安全和传统Unix系统一样，基于自主存取控制方法，即DAC，只要符合规定的权限，如规定的所有者和文件属性等，就可存取资源。在传统的安全机制下，一些通过setuid/setgid的程序就产生了严重安全隐患，甚至一些错误的配置就可引发巨大的漏洞，被轻易攻击。
	
	而SELinux则基于强制存取控制方法，即MAC，透过强制性的安全策略，应用程序或用户必须同时符合DAC及对应SELinux的MAC才能进行正常操作，否则都将遭到拒绝或失败，而这些问题将不会影响其他正常运作的程序和应用，并保持它们的安全系统结构。


##禁用iptabels

	systemctl disable iptables.service


##生成SSH key

按三次回车就可以

	# ssh-keygen -t rsa -C "server@ptphp.com"
	# ls ~/.ssh
	

## 安装PHP + Apache + MySQL 

###检查MySQL版本，并安装

	yum list mysql mysql-server
	
	Loaded plugins: langpacks, presto, refresh-packagekit
	Available Packages
	mysql.i686                             5.5.27-1.fc17                     updates
	mysql.x86_64                           5.5.27-1.fc17                     updates
	mysql-server.x86_64                    5.5.27-1.fc17                     updates

	yum install mysql mysql-server


###启动MySql server并且设置开机启动MySql服务

	# systemctl start mysqld.service
	# systemctl enable mysqld.service


###安装设置MySql 安全相关

	# /usr/bin/mysql_secure_installation


	NOTE: RUNNING ALL PARTS OF THIS SCRIPT IS RECOMMENDED FOR ALL MySQL
	      SERVERS IN PRODUCTION USE!  PLEASE READ EACH STEP CAREFULLY!
	
	
	In order to log into MySQL to secure it, we'll need the current
	password for the root user.  If you've just installed MySQL, and
	you haven't set the root password yet, the password will be blank,
	so you should just press enter here.
	
	Enter current password for root (enter for none):
	OK, successfully used password, moving on...
	
	Setting the root password ensures that nobody can log into the MySQL
	root user without the proper authorisation.
	
	Set root password? [Y/n] Y
	New password:
	Re-enter new password:
	Password updated successfully!
	Reloading privilege tables..
	 ... Success!
	
	
	By default, a MySQL installation has an anonymous user, allowing anyone
	to log into MySQL without having to have a user account created for
	them.  This is intended only for testing, and to make the installation
	go a bit smoother.  You should remove them before moving into a
	production environment.
	
	Remove anonymous users? [Y/n] Y
	 ... Success!
	
	Normally, root should only be allowed to connect from 'localhost'.  This
	ensures that someone cannot guess at the root password from the network.
	
	Disallow root login remotely? [Y/n] n
	 ... skipping.
	
	By default, MySQL comes with a database named 'test' that anyone can
	access.  This is also intended only for testing, and should be removed
	before moving into a production environment.
	
	Remove test database and access to it? [Y/n] Y
	 - Dropping test database...
	 ... Success!
	 - Removing privileges on test database...
	 ... Success!
	
	Reloading the privilege tables will ensure that all changes made so far
	will take effect immediately.
	
	Reload privilege tables now? [Y/n] y
	 ... Success!
	
	Cleaning up...
	
	
	
	All done!  If you've completed all of the above steps, your MySQL
	installation should now be secure.
	
	Thanks for using MySQL!


###连接本地MySql数据库

	# mysql -u root -p
	
OR

	# mysql -h localhost -u root -p

###安装 PHP

	# yum install php php-devel php-common

###安装PHP相关模块

	# yum install php-pear php-pdo php-mysql php-pgsql php-pecl-memcache php-gd php-mbstring php-mcrypt php-xml phpmyadmin



	yum install php-posix #用于 posix_getpwuid()

	error_reporting = E_ALL & ~E_NOTICE & ~E_WARNING & ~E_DEPRECATED

	sudo sed -i "s/display_errors = Off/display_errors = On/g" /etc/php.ini
	sudo sed -i "s/;date.timezone = ""/date.timezone = "Asia/Shanghai"/g" /etc/php.ini


###安装xhprof

	# yum install xhprof

###安装apache

	# yum install httpd

###启动HTTP server (httpd) 并设置开机启动 Apache

	apachectl start 
	
### 设置开机启动

	#systemctl enable httpd.service 
	
	vim /var/www/html/index.php

	<?php
	echo phpinfo();
	?>
	

##phpDocumentor

	 # yum search PhpDocumentor
	 # yum install php-pear-PhpDocumentor	 
	 
* http://baike.baidu.com/view/1269751.htm

	 

##XHProf


xhprof是Fackbook开源的一个PHP代码“跟踪”程序和性能检测程序，可以生成php程序的“函数”调用关系图，以及各个“函数”调用的次数及花费的时间。

当下，xhprof已经入住PHP的PECL库，可以能过pecl install xhprof安装（可能会提示你需要使用带版本号的名字，pecl install xhprof-0.9.2）。

也可以下载源代码编译安装（其实与使得pecl安装效果是一样的）。

> 当使用php 5.4时，安装xhprof可能会遇到麻烦，详情见这个bug公告：
> https://bugs.php.net/bug.php?id=61674，为xhprof-0.9.2打上补丁即可编译通过
> xhprof还不支持php5.2 以下的版本

###参考资料

* http://www.phpwebgo.com/2012/04/29/243.html
* http://hi.baidu.com/thinkinginlamp/blog/item/f4bd08fa1a03ba9e59ee90fd.html
* https://bugs.php.net/bug.php?id=61674
* https://github.com/facebook/xhprof/commit/a6bae51236.diff



###编译安装

	#yum install xhprof

获取源代码包

	# wget http://pecl.php.net/get/xhprof-0.9.2.tgz	
	# tar zxf xhprof-0.9.2.tgz
	# cd xhprof-0.9.2
	
#复制xhprof的展示页面目录和库目录到web目录下，可以为xhprof_html建个虚拟目录来访问，也可以把这两个目录拷贝到应用的根目录下
	
	# cp -r xhprof_html xhprof_lib /var/www/html/  	
	# cd extension/
	
	
编译插件

	phpize
	./configure --with-php-config=/usr/local/php/bin/php-config
	make && make install 

 
配置 `/etc/php.d/xhprof.ini` 文件
	
	[xhprof]
	extension=xhprof.so
	xhprof.output_dir=/tmp
 
重启web服务器
 

 
 
为了更加清晰显示程序执行、调用结构，安装Graphviz，如果安装了Graphviz，XHProf会用比较牛的图形方式展现统计数据。
获取源码包

> yum install graphviz

	# wget http://www.graphviz.org/pub/graphviz/stable/SOURCES/graphviz-2.24.0.tar.gz
	
	# tar zxf graphviz-2.24.0.tar.gz
	# cd graphviz-2.24.0

	# ./configure
	# make $$ make install
 
接下来在要统计的php应用中加入以下语句：

	//统计的代码部分之前加，如果要显示CPU占用 可以加入XHPROF_FLAGS_CPU参数，内存是XHPROF_FLAGS_MEMORY，如果两个一起：XHPROF_FLAGS_CPU + XHPROF_FLAGS_MEMORY，如：xhprof_enable(XHPROF_FLAGS_CPU + XHPROF_FLAGS_MEMORY);
	xhprof_enable();
	
	
	//统计的代码部分之后加
	xhprof_disable();
	 
这样xhprof就可以统计当前页面的函数性能情况了。xhprof会把每次访问加入统计代码的页面的性能统计结果在指定目录下生成一个文件，文件名命名方式为：本次访问的系统ID.命名空间，每次刷新页面都会重新生成一个文件，每个的系统ID都不同。
 
然后通过xhprof_html目录的http访问url来查看结果。如：`http://app/xhprof_html/?run=运行id&source=命名空间`（其中运行ID和命名空间可以根据xhprof生成的文件名来确定）
 
下面是个例子：

	<?php
	function a(){
	 echo 'a';
	}
	xhprof_enable(XHPROF_FLAGS_CPU + XHPROF_FLAGS_MEMORY);   //XHPROF_FLAGS_CPU + XHPROF_FLAGS_MEMORY
	// run program  
	a();
	// stop profiler  
	$xhprof_data = xhprof_disable();   //返回运行数据
	 
	// 下面是保存运行数据
	include_once "xhprof_lib/utils/xhprof_lib.php";  
	include_once "xhprof_lib/utils/xhprof_runs.php";  
	 
	$xhprof_runs = new XHProfRuns_Default();  
	 
	$run_id = $xhprof_runs->save_run($xhprof_data, "sourcejoy");   //第一个参数是 xhprof_disable()函数返回的运行信息，第二个参数是自定义的命名空间字符串（任意字符串），返回运行ID。
	
	echo "---------------\n<br>".
	"Assuming you have set up the http based UI for \n<br>".
	"XHProf at some address, you can view run at \n<br>".
	"/xhprof/?run=$run_id&source=sourcejoy\n<br>".
	"---------------\n";
		
	?>
	 
 
名词：

1. Inclusive Time ：包括子函数所有执行时间。
2. Exclusive Time/Self Time：函数执行本身花费的时间，不包括子树执行时间。
3. Wall Time：花去了的时间或挂钟时间。
4. CPU Time：用户耗的时间+内核耗的时间
5. Inclusive CPU：包括子函数一起所占用的CPU
6. Exclusive CPU：函数自身所占用的CPU



###生产环境中用法;

	//以万分之一的几率启用xhprof，平时悄悄的不打枪。
	
	if (mt_rand(1, 10000) == 1) {
	 xhprof_enable(XHPROF_FLAGS_MEMORY);
	 $xhprof_on = true;
	}
	
	//在程序结尾处调用方法保存profile
	
	if ($xhprof_on) {
	 // stop profiler
	 $xhprof_data = xhprof_disable();
	
	 // save $xhprof_data somewhere (say a central DB)
	 ...
	}
	
	//也可以用register_shutdown_function方法指定在程序结束时保存xhprof信息，这样就免去了结尾处判断，给个改写的不完整例子：
	
	if (mt_rand(1, 10000) == 1) {
	 xhprof_enable(XHPROF_FLAGS_MEMORY);
	 register_shutdown_function(create_funcion('', "$xhprof_data = xhprof_disable(); save $xhprof_data;"));
	}
	
	
	
	
	
