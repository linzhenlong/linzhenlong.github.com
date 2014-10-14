---
layout: post
title:  "分布式搜索elasticsearch单机与服务器环境搭建"
date:   2014-10-13 16:36:18
categories: elasticsearch
---

参考：http://blog.csdn.net/laigood12345/article/details/7415607

先到http://www.elasticsearch.org/download/下载最新版的elasticsearch运行包，本文写时最新的是0.19.1，作者是个很勤快的人，es的更新很频繁，bug修复得很快。下载完解开有三个包:bin是运行的脚本，config是设置文件，lib是放依赖的包。如果你要装插件的话就要多新建一个plugins的文件夹，把插件放到这个文件夹中。

1.单机环境：

单机版的elasticsearch运行很简单，linux下直接 bin/elasticsearch就运行了，windows运行bin/elasticsearch.bat。如果是在局域网中运行elasticsearch集群也是很简单的，只要cluster.name设置一致，并且机器在同一网段下，启动的es会自动发现对方，组成集群。

2.服务器环境：

如果是在服务器上就可以使用elasticsearch-servicewrapper这个es插件，它支持通过参数，指定是在后台或前台运行es，并且支持启动，停止，重启es服务（默认es脚本只能通过ctrl+c关闭es）。使用方法是到https://github.com/elasticsearch/elasticsearch-servicewrapper下载service文件夹，放到es的bin目录下。下面是命令集合：
bin/service/elasticsearch +
console 在前台运行es
start 在后台运行es
stop 停止es
install 使es作为服务在服务器启动时自动启动
remove 取消启动时自动启动

在service目录下有个elasticsearch.conf配置文件，主要是设置一些java运行环境参数，其中比较重要的是下面的

参数：

#es的home路径，不用用默认值就可以
set.default.ES_HOME=<Path to ElasticSearch Home>

#分配给es的最小内存
set.default.ES_MIN_MEM=256

#分配给es的最大内存
set.default.ES_MAX_MEM=1024


# 启动等待超时时间（以秒为单位）
wrapper.startup.timeout=300

# 关闭等待超时时间（以秒为单位）

wrapper.shutdown.timeout=300

# ping超时时间(以秒为单位)

wrapper.ping.timeout=300