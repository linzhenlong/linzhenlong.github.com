---
layout: post
title:  "CI框架数据库主从配置"
date:   2014-08-22 15:36:18
categories: php
---
#**php CodeIgniter 实战：主从数据库**

*转自http://jingyan.baidu.com/article/37bce2be2e94c01002f3a2fe.html*

CodeIgniter简称CI是最流行的一个php MVC框架之一，本人讲从实际项目使用中写系列实战经验，有别与其他的理论讲解文章，会附上实战流程和代码。

本篇为配置多个数据库，使用场景为集群，分布式，数据库读写分离，多台主从互备只有一台为读写数据库，其他为只读数据库。

##工具/原料
>php开发环境

>CodeIgniter

##方法/步骤
>在config/database.php 里面配置多一个数据库源，default为默认的可以是localhost也可以是IP，writedb为可读写的数据库，由于写的为主从互备的需要用IP远程调用，本人配置为：
    
    $active_group = 'default';
    $active_record = TRUE;
    $db['default']['hostname'] = 'localhost';
    $db['default']['username'] = 'default_username';
    $db['default']['password'] = 'default_password';
    $db['default']['database'] = 'default_dbname';
    $db['default']['dbdriver'] = 'mysql';
    $db['default']['dbprefix'] = '';
    $db['default']['pconnect'] = TRUE;
    $db['default']['db_debug'] = FALSE;
    $db['default']['cache_on'] = FALSE;
    $db['default']['cachedir'] = '';
    $db['default']['char_set'] = 'utf8';
    $db['default']['dbcollat'] = 'utf8_general_ci';
    $db['default']['swap_pre'] = '';
    $db['default']['autoinit'] = TRUE;
    $db['default']['stricton'] = FALSE;
    $db['writedb']['hostname'] = '202.187.194.160';
    $db['writedb']['username'] = 'writedb_name';
    $db['writedb']['password'] = 'writedb_password';
    $db['writedb']['database'] = 'writedb_db';
    $db['writedb']['dbdriver'] = 'mysql';
    $db['writedb']['dbprefix'] = '';
    $db['writedb']['pconnect'] = TRUE;
    $db['writedb']['db_debug'] = TRUE;
    $db['writedb']['cache_on'] = FALSE;
    $db['writedb']['cachedir'] = '';
    $db['writedb']['char_set'] = 'utf8';
    $db['writedb']['char_names'] = 'utf8';
    $db['writedb']['dbcollat'] = 'utf8_general_ci';
    $db['writedb']['swap_pre'] = '';
    $db['writedb']['autoinit'] = TRUE;
    $db['writedb']['stricton'] = FALSE;
    
    
>M(Model)需要用到的Model配置两个数据源进来，只需要读的配置一个就可以了，当然如果是只写的也可以单独配置写的一个。
本人一个问题反馈的实例feedbackmodel.php：

    class Feedbackmodel extends CI_Model {
        function __construct() {
            parent::__construct ();
            $this->db = $this->load->database ('default',true);
            $this->writedb = $this->load->database ('writedb',true);
        }
        public function add($data)
        {
            $this->writedb->insert('feedback',$data);
            if($this->writedb->affected_rows() == 1){
                return true;
            }
            return false ;
        }
    }    
    
   
>C(Controller)控制器的调用跟普通的一样，只要引入Model就可以了，本人实例：


    class Feedback extends CI_Controller {
        function __construct(){
            parent::__construct();
        }
        function index(){  
              $this->load->model('feedbackmodel');  
              $this->load->helper('url'); 
              $data['name'] = "feedback";
              $this->load->view('feedbackview',$data);
       }
    } 
    
    
    