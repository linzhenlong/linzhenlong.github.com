---
layout: post
title:  "elasticSearch php 使用方法"
date:   2014-10-31 16:36:18
categories: elasticsearch
---


##elasticSearch php 使用方法

转自：http://hi.baidu.com/whosafe/item/5e077ff6ecaf48c90dd1c8a4

1.首先需要下载  elasticSearch   有个大牛做好了一个，带ik中文分词的版本

[下载地址](https://github.com/medcl/elasticsearch-rtf)

2.需要下载  elasticSearch  php  扩展包

[下载地址](https://github.com/ruflin/Elastica)


下面开始安装  elasticSearch

这是一个傻瓜版的

下载解压后进入文件

目录如下：

elasticsearch

packages_list.txt

README.textile

tutorial

    cd elasticsearch/
    chmod +x build.sh
    ./build.sh

在当前目录下会产生一个release.tar.gz文件，这个就是  elasticSearch  所需要的全部文件，相当于一个备份  ，可以直接放到服务器上用的。

下面我们来测试一下  下载的文件是否是否能正常使用

    cd bin/
    chmod +x ./plugin
    ./plugin -install mobz/elasticsearch-head 安装一个客户端，  http://localhost:9200/_plugin/head/  可以直观的看到结果


启动  elasticSearch

    chmod +x service/elasticsearch

    ./elasticsearch start       也可以在监控状态先启动   console


 下面来测试一下  elasticSearch


 创建一个  index

    curl -XPUT 'http://localhost:9200/app'

 返回结果：

     {"ok":true,"acknowledged":true}

 测试 ik分词是否生效

     curl 'http://localhost:9200/app/_analyze?analyzer=ik_max_word&pretty=true' -d '{"text":"中华人民共和国"}'


测试结果

    {
      "tokens" : [ {
        "token" : "text",
        "start_offset" : 2,
        "end_offset" : 6,
        "type" : "ENGLISH",
        "position" : 1
      },
      {
        "token" : "中华人民共和国",
        "start_offset" : 9,
        "end_offset" : 16,
        "type" : "CN_WORD",
        "position" : 2
      },
      {
        "token" : "中华人民",
        "start_offset" : 9,
        "end_offset" : 13,
        "type" : "CN_WORD",
        "position" : 3
      },
      {
        "token" : "中华",
        "start_offset" : 9,
        "end_offset" : 11,
        "type" : "CN_WORD",
        "position" : 4
      }, {
        "token" : "华人",
        "start_offset" : 10,
        "end_offset" : 12,
        "type" : "CN_WORD",
        "position" : 5
      },
      {
        "token" : "人民共和国",
        "start_offset" : 11,
        "end_offset" : 16,
        "type" : "CN_WORD",
        "position" : 6
      },
      {
        "token" : "人民",
        "start_offset" : 11,
        "end_offset" : 13,
        "type" : "CN_WORD",
        "position" : 7
      },
      {
        "token" : "共和国",
        "start_offset" : 13,
        "end_offset" : 16,
        "type" : "CN_WORD",
        "position" : 8
      },
      {
        "token" : "共和",
        "start_offset" : 13,
        "end_offset" : 15,
        "type" : "CN_WORD",
        "position" : 9
      },
      {
        "token" : "国",
        "start_offset" : 15,
        "end_offset" : 16,
        "type" : "CN_CHAR",
        "position" : 10
      } ]
    }




测试2

    curl 'http://localhost:9200/app/_analyze?analyzer=ik_max_word&pretty=true' -d '{"text":"阿里旺旺"}'

    {
      "tokens" : [ {
        "token" : "text",
        "start_offset" : 2,
        "end_offset" : 6,
        "type" : "ENGLISH",
        "position" : 1
      }, {
        "token" : "阿里",
        "start_offset" : 9,
        "end_offset" : 11,
        "type" : "CN_WORD",
        "position" : 2
      }, {
        "token" : "里",
        "start_offset" : 10,
        "end_offset" : 11,
        "type" : "CN_WORD",
        "position" : 3
      }, {
        "token" : "旺旺",
        "start_offset" : 11,
        "end_offset" : 13,
        "type" : "CN_WORD",
        "position" : 4
      }, {
        "token" : "旺",
        "start_offset" : 11,
        "end_offset" : 12,
        "type" : "CN_WORD",
        "position" : 5
      }, {
        "token" : "旺",
        "start_offset" : 12,
        "end_offset" : 13,
        "type" : "CN_WORD",
        "position" : 6
      } ]
    }

这样的结果是满足需求了。

也可以自己创建分词库，文件添加到这个里面就好了，

config/ik/IKAnalyzer.cfg.xml

config/ik/main.dic    这个为ik自己的分词库，我是直接就添加到这里来的。

分词合并方式

将sougou.dic   分词合并到main.dic  中

    cat custom/sougou.dic main.dic >tmp.dic

    tail tmp.dic

    sort -n tmp.dic | awk '{if($0!=line)print; line=$0}' >t.dic

    mv main.dic main.dic.bak

    mv t.dic main.dic

重启服务  就好了

下面创建一个type

在常见type（iphone）的同时  我们要创建一个mapping   如果不创建的话mapping的话，系统将不使用ik中文分词。

    curl -XPOST http://localhost:9200/app/iphone/_mapping -d'
    {
        "iphone": {
                 "_all": {
                "indexAnalyzer": "ik",
                "searchAnalyzer": "ik",
                "term_vector": "no",
                "store": "false"
            },
            "properties": {
                "content": {
                    "type": "string",
                    "store": "no",
                    "term_vector": "with_positions_offsets",
                    "indexAnalyzer": "ik",
                    "searchAnalyzer": "ik",
                    "include_in_all": "true",
                    "boost": 8
                },
                "name": {
                    "type": "string",
                    "store": "no",
                    "term_vector": "with_positions_offsets",
                    "indexAnalyzer": "ik",
                    "searchAnalyzer": "ik",
                    "include_in_all": "true",
                    "boost": 8
                } ,
               "lastPrice": {
                "type": "float",
                "index": "not_analyzed"
               },
             "published":{
                    "type":"integer",
                    "index":"not_analyzed"
                }
            }
        }
    }'


 返回结果：

     {"ok":true,"acknowledged":true}

 需要注意的是   在json第一行的key移动要和type相同


 ##插入数据：


     curl -XPOST http://localhost:9200/app/iphone/1 -d '
    {
        "name": "阿里旺旺卖家版",
        "content": "【软件介绍】阿里旺旺卖家版是淘宝网专为淘宝掌柜量身打造的手机旺旺软件，集成    了:会员订单改价，客服工作台，群组聊天，支持交易焦点及E客服账号登陆，方便您出门在外时仍旧可以与买家进行无缝沟通。【核心功能",
        "published": 1355997199,
        "lastPrice": 0.1
    }'

返回结果

    {"ok":true,"_index":"app","_type":"iphone","_id":"1","_version":1}

搜索数据

内容包括几个部分：

分页:from/size、字段:fields、排序sort、查询:query、过滤:filter、高亮:highlight、统计:facet


    curl -XPOS http://localhost:9200/app/iphone/_search -d '
    {
        "from": 0,
        "size": 10,
        "fields": [
            "name",
            "content",
            "published",
            "lastPrice"
        ],
        "sort": [
            {
                "published": {
                    "order": "asc"
                }
            },
            "_score"
        ],
        "query": {
            "bool": {
                "should": [
                {
                    "term": {
                        "name": "阿里"
                    }
                },
                {
                    "term": {
                        "content": "阿里"
                    }
                }
            ]
        }
    },
    "highlight": {
        "pre_tags": [
            "<tag1>",
            "<tag2>"
        ],
        "post_tags": [
            "</tag1>",
            "</tag2>"
        ],
        "filter": {
            "range": {
                "published": {
                    "gte": 11
                }
            }
        },
        "fields": {
            "title": {
                "fragment_size": 100
            },
            "content": {
                "fragment_size": 100
            }
        },
        "facets": {
            "cate": {
                "terms": {
                    "field": "name"
                }
            }
        }
    }
    }'


 返回结果


     {
        "took": 3,
        "timed_out": false,
        "_shards": {
            "total": 5,
            "successful": 5,
            "failed": 0
        },
        "hits": {
            "total": 1,
            "max_score": null,
            "hits": [
                {
                    "_index": "app",
                    "_type": "iphone",
                    "_id": "1",
                    "_score": 1.0848885,
                    "fields": {
                    "content": "【软件介绍】阿里旺旺卖家版是淘宝网专为淘宝掌柜量身打造的手机旺旺软件，集成了:会员订单改价，客服工作台，群组聊天，支持交易焦点及E客服账号登陆，方便您出门在外时仍旧可以与买家进行无缝沟通。【核心功能",
                    "lastPrice": 0.1,
                    "published": 1355997199,
                    "name": "阿里旺旺卖家版"
                    },
                    "highlight": {
                        "content": [
                        "【软件介绍】<tag2>阿里</tag2>旺旺卖家版是淘宝网专为淘宝掌柜量身打造的手机旺旺软件，集成了:会员订单改价，客服工作台，群组聊天，支持交易焦点及E客服账号登陆，方便您出门在外时仍旧可以与买家进行无缝沟通。【核心功能"
                    ]
                },
                "sort": [
                    1355997199,
                    1.0848885
                ]
            }
        ]
    }
    }


删除一条数据：

    curl -XDELETE 'http://localhost:9200/app/iphone/1'

    {"ok":true,"found":true,"_index":"app","_type":"iphone","_id":"1","_version":3}




====================================================================================================
php篇：

载入 elastic类

这里使用命名空间

    use Elastica\Client;
    use Elastica\Type\Mapping;
    use Elastica\Document;


    $client = new Client();
    $index=$client->getIndex("app");

    $index->create(array(),true); //创建一个app  index

    $type=$index->getType("iphone");


    #创建mapping
    $argv=array(
        "_all"=>array(
            "indexAnalyzer"=> "ik",
            "searchAnalyzer"=> "ik",
            "term_vector"=> "no",
            "store"=> "false"
        ),
        "properties"=>array(
            "content"=>array(
                "type"=>"string",
                "store"=>"no",
                "term_vector"=>"with_positions_offsets",
                "indexAnalyzer"=>"ik",
                "searchAnalyzer"=>"ik",
                "include_in_all"=>"true",
            ),
            "name"=>array(
                "type"=>"string",
                "store"=>"no",
                "term_vector"=>"with_positions_offsets",
                "indexAnalyzer"=>"ik",
                "searchAnalyzer"=>"ik",
                "include_in_all"=>"true",
                "boost"=>5 //权重
            ),
            "published"=>array(
                "type"=>"integer",
                "index"=>"not_analyzed"
            ),
            "lastPrice"=>array(
                "type"=>"float",
                "index"=>"not_analyzed"
            )
        )
    );

    $mapping=new Mapping($type);
    $mapping->setParam("properties",$argv['properties']);
    $mapping->setParam("_all",$argv['_all']);

    $mapping->send();
    $index->optimize();

    #插入数据

    $docs[] = new Document("news_1",
                    array(
                        'name'=>  "阿里旺旺卖家版",
                        'published'=>111111,
                        'content'=> "【软件介绍】阿里旺旺卖家版是淘宝网专为淘宝掌柜量身打造的手机旺旺软件，集成了:会员订单改价，客服工作台，群组聊天，支持交易焦点及E客服账号登陆，方便您出门在外时仍旧可以与买家进行无缝沟通。【核心功能",
                        'lastPrice'=>1.1
                        )
                );


    $type->addDocuments($docs);

    #这里有两种方式  一种是插入单条   还有一种是插入多条数据   上面问插入多条的，下面为插入单条数据

    $docs = new Document("news_{$tmp['contentid']}",
                    array(
                        'name'=>  "阿里旺旺卖家版",
                        'published'=>111111,
                        'content'=> "【软件介绍】阿里旺旺卖家版是淘宝网专为淘宝掌柜量身打造的手机旺旺软件，集成了:会员订单改价，客服工作台，群组聊天，支持交易焦点及E客服账号登陆，方便您出门在外时仍旧可以与买家进行无缝沟通。【核心功能",
                        'lastPrice'=>1.1
                        )
                );


    $type->addDocument($docs);

    $index->refresh();     //刷新数据


    #删除数据的方法是：

    $client->deleteIds(array("new_1"),$index,$type);



    #下面是搜索方式：
    use Elastica\Client;
    use Elastica\Search;
    use Elastica\Query;


    $client= new Client();
    $search=new Search($client);

    $index=$client->getIndex("app");
    $search->addIndex($index);

    //如果需要执行在那个type中搜索
    $type=$index->getType($sType);
    $search->addType($type);


    $seachApp=array(
            'from' => 0,
            'size' => 10,
            'fields' =>array (
                'name',
                'alias',
                'discription',
                'appid',
                'app',
                'catid',
                'weight',
            'lastPrice',
            "updateTime",
            'starOverall'
        ),
        'sort' =>array (
            array (
                'weight' =>
                array (
                    'order' => 'desc',
                ),
            ),
            '_score',
        ),
        'highlight' =>array (
            'pre_tags' =>array (
                '<em>',
                '<em>',
            ),
            'post_tags' =>array (
                '</em>',
                '</em>',
            ),

        'fields' =>array (
                'alias' => array (
                    'fragment_size' => 100
                ),
                'discription' =>array (
                    'fragment_size' => 100
                ),
            ),
        ),
    );

    //创建搜索关键词

    $seachArgv['query']['bool']['should'][]['term']['title']="阿里";
    $seachArgv['query']['bool']['should'][]['term']['content']="阿里";

    //创建搜索范围

    $seachArgv['filter']['range']['published']['gte']=strtotime(date('Y-m-d') ."-1 year");

    //开始搜索
    $query = Query::create(self::$seachArgv);
    $resultSet=$search->search($query);

    //获取搜索结果
    $data=$resultSet->getResponse()->getData();

怎么用  那就自己根据   业务需要去完成了，基本上我们需要用到的  就是这些，有什么问题  亲留言，大家一起学习，主要是没看到和php相关的信息，所以就写了一下   希望能帮上各位点忙！！！








