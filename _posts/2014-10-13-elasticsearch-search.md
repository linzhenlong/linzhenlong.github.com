---
layout: post
title:  "分布式搜索引擎elasticsearch 所有支持的查询方式"
date:   2014-10-13 16:36:18
categories: elasticsearch
---

###match query
http://www.elasticsearch.org/guide/en/elasticsearch/reference/current/query-dsl-match-query.html


    function match_query()
    {
        $params['index'] = 'my_product';
        $params['type'] = 'product';
        $params['body']['query'] = array(
            'match' => array(
                'pro_name' => '京东 箱包专场'
            ),
        );
        $docs = $this->es->search($params);
        print_r($docs);
    }