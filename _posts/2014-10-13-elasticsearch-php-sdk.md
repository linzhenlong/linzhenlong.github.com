---
layout: post
title:  "分布式搜索引擎elasticsearch 安装官方php sdk"
date:   2014-10-13 16:36:18
categories: elasticsearch
---

基本 环境:

mac os 10.9.5

php 5.4.39

elasticsearch1.2.2(目前最新版)

1.安装官api SDK

    mkdir -p /data/wordspace/es
    vi composer.json
2.composer.json

    {
        "require": {
            "elasticsearch/elasticsearch": "~1.0"
        }
    }
    
    
3.安装composer
    
    curl -s http://getcomposer.org/installer | php
    php composer.phar install
    
4.加载客户端
    
    require 'vendor/autoload.php';
    $client = new Elasticsearch\Client();
    
    var_dump($client);
    
    