---
layout: post
title:  "Elasticsearch模块功能之-路由（routing)"
date:   2015-03-03 10:36:18
categories: elasticsearch
---

原文地址:http://blog.csdn.net/changong28/article/details/38427311

###Elasticsearch模块功能之-路由（routing）

索引分片分配能够控制索引分片在节点上怎么分布，那对于具体的文档能否控制具体节点的分布呢？

答案是可以，根据路由公式shard = hash(routing) % number_of_primary_shards，

Elasticsearch使用相同的routing参数来实现这个功能，但我们在创建索引时需如下进行配置：


    "mappings":{
        "doc": {
            "_routing": {
                "required": true,
                "path":"_routing"
            },
            "properties": {
                "title": {
                    "type":"string"
                }
            }
        }
    }


如果我们想在建索引时将相关的文档存放到一个分片下就可以这样做：


    curl -XPUT 'localhost:9200/documents/doc/1' - d '
    {
        "title": "Document No.1",
        "_routing":"A"
    }'

    curl- XPUT 'localhost:9200/documents/doc/2' - d '
    {
        "title": "Document No.1",
        "_routing":"A"
    }'

    curl- XPUT 'localhost:9200/documents/doc/3' - d '
    {
        "title": "Document No.1",
        "_routing":"B"
    }'


这样将id为1和2的文档将会存在统一个分片上去。

既然索引文档的时候使用了路由，那么肯定得在查询的时候利用了，在查询的时候使用routing参数将使得查询更加高效，如下：

    curl-XGET 'localhost:9200/documents/_search?pretty&q=*:*&routing=A'