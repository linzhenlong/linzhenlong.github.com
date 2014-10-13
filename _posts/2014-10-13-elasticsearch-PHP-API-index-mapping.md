---
layout: post
title:  "分布式搜索引擎elasticsearch PHP API index mapping操作"
date:   2014-10-13 16:36:18
categories: elasticsearch
---

	require '../vendor/autoload.php';
	function p ($param) {
	if (!is_array($param) && !is_object($param))
	{
		echo $param;
		return true;
	}
		echo '<pre>';
		print_r($param);
		echo '</pre>';
	}

	$client = new Elasticsearch\Client();

	//mapping操作
	//为index_002mapping
	$indexParam['index'] = 'index_002';
	$indexParam['type'] = 'type_001';
	$mapParam = array(
		'_source' => array(
				'enable' => true,
			),
		'properties' => array(
			'title' => array(
					'type' => 'string',//field_name_1值为string类型
					'analyzer' => 'standard'//分析
				),
			'score' => array(
					'type' => 'float',//分数为float
					'index' => 'not_analyzed'
				),
			'url' => array(
					'type' => 'string',
					'no-index'=>'not_analyzed'//不进行索引,默认就是不索引
				)
			),
		);
	//index为db,type为table
	//mapp就相当与给table分配字段以及属性
	// $indexParam['body'][$indexParam['type']] = $mapParam;
	// $ret = $client -> indices() -> putMapping($indexParam);
	// p($ret);
	//Array
	// (
	//     [acknowledged] => 1
	// )

	//查看刚才创建的mapping

	$ret = $client -> indices() -> getMapping($indexParam);
	p($ret);
	//打印结果
    // 	Array
    // (
    //     [index_002] => Array
    //         (
    //             [mappings] => Array
    //                 (
    //                     [type_001] => Array
    //                         (
    //                             [properties] => Array
    //                                 (
    //                                     [score] => Array
    //                                         (
    //                                             [type] => float
    //                                         )

    //                                     [title] => Array
    //                                         (
    //                                             [type] => string
    //                                             [analyzer] => standard
    //                                         )

    //                                     [url] => Array
    //                                         (
    //                                             [type] => string
    //                                         )

    //                                 )

    //                         )

    //                 )

//         )

// )