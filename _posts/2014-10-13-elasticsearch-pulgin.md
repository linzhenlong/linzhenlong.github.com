---
layout: post
title:  "分布式搜索引擎Elasticsearch 插件汇总"
date:   2014-10-14 16:36:18
categories: elasticsearch
---

原文地址：http://blog.csdn.net/zhuzhenyu307/article/details/37929217

Elasticsearch扩展性非常好，有很多官方和第三方开发的插件，下面以分词、同步、数据传输、脚本支持、站点、其它这几个类别进行划分。

##分词插件

[Combo Analysis Plugin (作者 Olivier Favre, Yakaz)](https://github.com/yakaz/elasticsearch-analysis-combo/)

简介：组合分词器，可以把多个分词器的结果组合在一起。

[Smart Chinese Analysis Plugin(作者 elasticsearch 团队)](https://github.com/elasticsearch/elasticsearch-analysis-smartcn)

简介：lucene默认的中文分词器

[ICU Analysis plugin (作者 elasticsearch 团队)](https://github.com/elasticsearch/elasticsearch-analysis-icu)

简介：lucene自带的ICU分词，ICU是一套稳定、成熟、功能强大、轻便易用和跨平台支持Unicode 的开发包。

[Stempel (Polish) Analysis plugin (作者 elasticsearch 团队)](https://github.com/elasticsearch/elasticsearch-analysis-stempel)

简介：法文分词器