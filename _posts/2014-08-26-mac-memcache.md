---
layout: post
title:  "在Mac OS X中完善PHP环境：memcache"
date:   2014-08-22 16:36:18
categories: mac
---

##一、首先准备Xcode和autoconf。

>安装的过程需要Xcode的命令行工具，编译PHP扩展需要有autoconf。

>Xcode是苹果公司提供的免费开发工具，请从App Store中搜索安装。完成安装之后，请通过Xcode的菜单【Xcode】-【Preferences...】打开对话框并切换到【Downloads】页，下载**Command Line Tools。**

>autoconf请从如下地址下载：

    http://ftp.gnu.org/gnu/autoconf/
    
    提醒：
    本文涉及的所有下载和编译安装，建议把源代码存放到路径不存在空格（和其他特殊符号）的位置，以避        
    免执行make install时发生意外。
    
>解压并进入autoconf目录，依次执行如下命令：

    ./configure
    make
    make install

##二、安装memcache

>memcached是一套高性能的内存对象缓存系统。在PHP中，有两套扩展可以用来支持memcached。其中一套是原生的扩展，称为“memcache”，下面介绍的就是这个扩展。还有另外一个套扩展是基于libmemcached库的，功能更强一些。

>10.8的mac中，可以在/usr/bin/路径下找到memcached这个文件，/System/Library/LaunchDaemons/路径下还有个com.danga.memcached.plist文件。打开com.danga.memcached.plist文件，删除掉如下两行：

    <key>Disable</key>
    <true/>
    
>保存文件之后重新启动，打开终端执行：

    #memcached的默认端口为11211
    telnet 127.0.0.1 11211
    #stats命令显示memcached的各种状态信息
    stats
    #quit退出与memcached的连接
    quit
    
>如果一切正常，开始安装memcache的PHP扩展。请从如下地址下载：

    http://pecl.php.net/package/memcache
    
>解包下载的文件之后进入文件目录，请依次执行如下命令：

    phpize
    ./configure
    make
    sudo make install
    
>如果看到如下结果：

    Installing shared extensions:     /usr/lib/php/extensions/no-debug-non-zts-20090626/


>说明安装正常，接下去打开/etc/php.ini文件，添加如下行：

    extension=memcache.so
    
>重启apache之后，就ok拉








