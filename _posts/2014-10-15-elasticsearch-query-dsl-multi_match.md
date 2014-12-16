---
layout: post
title:  "分布式搜索elasticsearch query dsl multi_match"
date:   2014-10-13 16:36:18
categories: elasticsearch
---

参考:http://www.elasticsearch.org/guide/en/elasticsearch/reference/current/query-dsl-multi-match-query.html

multi match query


1.multi_match

    $params['index'] = 'raw_data';
    $params['type'] = 'article';
    $params['body']['query']['multi_match']['query'] = 'iphone';
    $params['body']['query']['multi_match']['fields'] = array('raw_title','raw_content');

2.dis_max

    $params['index'] = 'raw_data';
    $params['type'] = 'article';
    $params['body']['query']['dis_max']['queries'][]['match']['raw_title'] = '苹果';
    $params['body']['query']['dis_max']['queries'][]['match']['raw_content'] = 'iPhone';
    $params['body']['query']['dis_max']['queries'][]['match']['raw_mall'] = '亚马逊';
    $params['body']['query']['dis_max']['tie_breaker'] = 0.3;


3.operator and minimum_should_match






