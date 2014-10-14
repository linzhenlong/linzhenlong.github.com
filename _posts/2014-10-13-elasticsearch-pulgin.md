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

[IK Analysis Plugin (作者 Medcl)](https://github.com/medcl/elasticsearch-analysis-ik)

简介：大名鼎鼎的ik分词，都懂的！

[Mmseg Analysis Plugin (作者 Medcl)](https://github.com/medcl/elasticsearch-analysis-mmseg)

简介：mmseg中文分词

[Hunspell Analysis Plugin (作者 Jörg Prante)](https://github.com/jprante/elasticsearch-analysis-hunspell)

简介：lucene自带的Hunspell模块

[Japanese (Kuromoji) Analysis plugin (作者 elasticsearch 团队).](https://github.com/elasticsearch/elasticsearch-analysis-kuromoji)

简介：日文分词器

[Japanese Analysis plugin (作者 suguru).](https://github.com/suguru/elasticsearch-analysis-japanese)

简介：日文分词器

[Russian and English Morphological Analysis Plugin (作者 Igor Motov)](https://github.com/imotov/elasticsearch-analysis-morphology)

简介：俄文英文分词器

[Pinyin Analysis Plugin (作者 Medcl)](https://github.com/medcl/elasticsearch-analysis-pinyin)

简介：拼音分词器

[String2Integer Analysis Plugin (作者 Medcl)] (https://github.com/medcl/elasticsearch-analysis-string2int)

简介：字符串转整型工具。主要用在facet这个功能上，如果facet的field的值是字符串的话，计算起来比较耗资源。可以把字符串映射成整型，对整型进行facet操作要比对字符串的快很多。


##同步插件

[CouchDB River Plugin (作者 elasticsearch 团队)](https://github.com/elasticsearch/elasticsearch-river-couchdb)

简介：CouchDB和elasticsearch的同步插件

[Wikipedia River Plugin (作者 elasticsearch 团队)](https://github.com/elasticsearch/elasticsearch-river-wikipedia)

简介：wikipedia文件读取插件。wikipedia是维基百科的一个离线库，不定期发布最新数据，是以xml形式发布的。这个river读取这个文件来建索引。

[Twitter River Plugin (作者 elasticsearch 团队)] (https://github.com/elasticsearch/elasticsearch-river-twitter)

简介：twitter的同步插件，可以同步你twitter上的微博。

[RabbitMQ River Plugin (作者 elasticsearch 团队)](https://github.com/elasticsearch/elasticsearch-river-rabbitmq)

简介：rabbitmq同步插件，读取rabbitmq上的队列信息并索引。

[RSS River Plugin (作者 David Pilato)](http://www.pilato.fr/rssriver/)

简介：定期索引指定一个或多个RSS源的数据。

[MongoDB River Plugin (作者 Richard Louapre)](https://github.com/richardwilly98/elasticsearch-river-mongodb/)

简介：mongodb同步插件，mongodb必须搭成副本集的模式，因为这个插件的原理是通过定期读取mongodb中的oplog来同步数据。

[Open Archives Initiative (OAI) River Plugin (作者 Jörg Prante)](https://github.com/jprante/elasticsearch-river-oai/)

简介：可以索引oai数据提供者提供的数据。

[St9 River Plugin (作者 Sunny Gleason)](https://github.com/sunnygleason/elasticsearch-river-st9)

简介：可以索引索引st9数据（st9是神马？囧！！！）

[Sofa River Plugin (作者 adamlofts)](https://github.com/adamlofts/elasticsearch-river-sofa)

简介：这个插件可以把多个CouchDB的数据库同步到同一个es索引中。

[JDBC River Plugin (作者 Jörg Prante)](https://github.com/jprante/elasticsearch-river-jdbc)

简介：关系型数据库的同步插件

[FileSystem River Plugin (作者 David Pilato)] (http://www.pilato.fr/fsriver/)

简介：本地文件系统文件同步插件，使用方法是指定一个本地目录路径，es会定期扫描索引该目录下的文件。

[LDAP River Plugin (作者 Tanguy Leroux)](https://github.com/tlrx/elasticsearch-river-ldap)

简介：索引LDAP目录下的文件数据。

[Dropbox River Plugin (作者 David Pilato)](http://www.pilato.fr/dropbox/)

简介：索引dropbox网盘上的文件。通过oauth协议来调用dropbox上的api建索引。

[ActiveMQ River Plugin (作者 Dominik Dorn)] (https://github.com/domdorn/elasticsearch-river-activemq/)

简介：activemq队列的同步插件，和之前rabbitmq的类似

[Solr River Plugin (作者 Luca Cavanna)](https://github.com/javanna/elasticsearch-river-solr/)

简介：solr同步插件，可以把solr里面的索引同步到es

[CSV River Plugin (作者 Martin Bednar)](https://github.com/AgileWorksOrg/elasticsearch-river-csv)

简介：通过指定目录地址来索引csv文件。


##数据传输插件

[Servlet transport (作者 elasticsearch 团队)](https://github.com/elasticsearch/elasticsearch-transport-wares)

简介：Servlet rest插件，通过servlet来封装rest接口。

[Memcached transport plugin (作者 elasticsearch 团队)](https://github.com/elasticsearch/elasticsearch-transport-memcached)

简介：本插件可以通过memcached协议进行rest接口的调用。注意：这里不是使用memcache作为es的缓存。

[Thrift Transport (作者 elasticsearch 团队)](https://github.com/elasticsearch/elasticsearch-transport-thrift)

简介：使用thrift进行数据传输。

[ZeroMQ transport layer plugin (作者 Tanguy Leroux)](https://github.com/tlrx/transport-zeromq)

简介：使用zeromq进rest接口的调用。

[Jetty HTTP transport plugin (作者 Sonian Inc.)](https://github.com/sonian/elasticsearch-jetty)

简介：使用jetty来提供http rest接口。默认是使用netty。这个插件的好处是可以对http接口进行一些权限的设置。



##脚本插件

[Python language Plugin (作者 elasticsearch 团队)](https://github.com/elasticsearch/elasticsearch-lang-python)

简介：python脚本支持

[JavaScript language Plugin (作者 elasticsearch 团队)](https://github.com/elasticsearch/elasticsearch-lang-javascript)

简介：javascript脚本支持

[Groovy lang Plugin (作者 elasticsearch 团队)](https://github.com/elasticsearch/elasticsearch-lang-groovy)

简介：groovy脚本支持

[Clojure Language Plugin (作者 Kevin Downey)](https://github.com/hiredman/elasticsearch-lang-clojure)

简介：clojure脚本支持

##站点插件（以网页形式展现）

[BigDesk Plugin (作者 Lukáš Vlček)](https://github.com/lukas-vlcek/bigdesk)

简介：监控es状态的插件，推荐！

[Elasticsearch Head Plugin (作者 Ben Birch)](https://github.com/mobz/elasticsearch-head)

简介：很方便对es进行各种操作的客户端。

[Paramedic Plugin (作者 Karel Minařík)](https://github.com/karmi/elasticsearch-paramedic)

简介：es监控插件

[SegmentSpy Plugin (作者 Zachary Tong)](https://github.com/polyfractal/elasticsearch-segmentspy)

简介：查看es索引segment状态的插件

[Inquisitor Plugin (作者 Zachary Tong)](https://github.com/polyfractal/elasticsearch-inquisitor)

简介：这个插件主要用来调试你的查询。


##其它插件

[Mapper Attachments Type plugin (作者 elasticsearch 团队)](https://github.com/elasticsearch/elasticsearch-mapper-attachments)

简介：附件类型插件，通过tika库把各种类型的文件格式解析成字符串。

[Hadoop Plugin (作者 elasticsearch team)](https://github.com/elasticsearch/elasticsearch-hadoop)

简介：hadoop和elasticsearch的集成插件，可以通过hadoop的mapreduce算法来并行建立索引，同时支持cascading，hive和pig等框架。

[AWS Cloud Plugin (作者 elasticsearch 团队)](https://github.com/elasticsearch/elasticsearch-cloud-aws)

简介：elasticsearch与amazon web services的集成。

[ElasticSearch Mock Solr Plugin (作者 Matt Weber)](https://github.com/mattweber/elasticsearch-mocksolrplugin)

简介：elasticsearch的solr api接口。用了这个插件可以使用solr的api来调用es，直接用solrj就可以调用es。比较适用于从solr转es时暂时过度。

[Suggester Plugin (作者 Alexander Reelsen)](https://github.com/spinscale/elasticsearch-suggest-plugin)

简介：es 搜索提示功能插件，不过es0.9版本后自带了这个功能，

[ElasticSearch PartialUpdate Plugin (作者 Medcl)](https://github.com/medcl/elasticsearch-partialupdate)

简介：elasticsearch的部分更新插件

[ZooKeeper Discovery Plugin (作者 Sonian Inc.)](https://github.com/sonian/elasticsearch-zookeeper)

简介：通过zookeeper管理集群的插件。通过这个插件，es的分布式架构和solrcloud相似。

[ElasticSearch Changes Plugin (作者 Thomas Peuss)](https://github.com/derryx/elasticsearch-changes-plugin)

简介：elasticsearch索引操作记录插件。通过这个插件可以查看用户对索引的增删改操作。

[ElasticSearch View Plugin (作者 Tanguy Leroux)](http://tlrx.github.io/elasticsearch-view-plugin/)

简介：这个插件可以把es的文档以html，xml或text的方式显示出来，它也可以通过查询生成web页面。

[ElasticSearch New Relic Plugin (作者 Vinicius Carvalho)](https://github.com/viniciusccarvalho/elasticsearch-newrelic)

简介：elasticsearch和newrelic的集成插件。newrelica是一个性能监控工具。这个插件会把节点的状态数据传到newrelic的账号上。

