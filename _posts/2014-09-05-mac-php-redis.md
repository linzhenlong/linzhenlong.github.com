---
layout: post
title:  "Mac下配置Redis服务器（自启动、后台运行）"
date:   2014-09-05 16:36:18
categories: mac
---
##Mac下配置Redis服务器（自启动、后台运行）

###一、先装redis服务

>Redis服务器在从诸多文章上看来，是个极为优秀的Key-value数据库软件。其NB之处可以从[这篇文章中](http://timyang.net/data/redis-misunderstanding/)略知一二。

>PHP下使用redius可以参考这个教材：[phpredis中文手册——《redis中文手册》 php版](http://www.cnblogs.com/ikodota/archive/2012/03/05/php_redis_cn.html)


首先是安装，它会默认安装到/usr/local/bin下

    wget http://redis.googlecode.com/files/redis-2.6.9.tar.gz 
    tar -zxf redis-2.6.9.tar.gz 
    cd redis-2.6.9
    make
    sudo make install
    
然后下载一些配置文件（主要就是把deamon打开之类的，没对比与默认配置的区别）

    wget https://github.com/ijonas/dotfiles/raw/master/etc/redis.conf
    sudo mv redis.conf /etc/redis.conf
    
然后以root身份做以下事情:

>在/Library/LaunchDaemons下新建com.redis.plist，内容如下：

    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
    <plist version="1.0">
        <dict>
            <key>Label</key>
            <string>com.redis</string>
            <key>RunAtLoad</key>
            <true/>
            <key>ProgramArguments</key>
            <array>
                <string>/usr/local/bin/redis-server</string>
                <string>/etc/redis.conf</string>
            </array>
        </dict>
    </plist>
    

之后运行
    
    sudo launchctl load /Library/LaunchDaemons/com.redis.plist
    sudo launchctl start com.redis
    
然后执行一下

    redis-cli
看见：
    
    redis 127.0.0.1:6379>
    
在测试一下：
    
    redis 127.0.0.1:6379> set test fuck
    OK
    redis 127.0.0.1:6379> get test
    "fuck"
    redis 127.0.0.1:6379>
    
如果上面测试没有问题的话，应该就是已经装上了。



###二、装phpredis 扩展

安装过程比较简单

首先将phpredis 源码包下载下来。
phpredis 的github地址：

[https://github.com/nicolasff/phpredis](https://github.com/nicolasff/phpredis)

编译安装：

>1.首先git clone 项目到本地，切换phpredis目录下

    git clone https://github.com/nicolasff/phpredis
    cd phpredis
    
>2.在shell 中输入phpize 然后./configure 进行配置

    phpize
    ./configure

>3.接下来就是最后的make 和make install了

    make 
    sudo make install
    
>4.make install 之后修改php.ini 加上redis 模块模块，重启服务，执行如下，看下是否装上

    php -m grep | redis
    
>5.执行上面命令出现redis代表已经装上了。

    
        
    
    
    
        
    

