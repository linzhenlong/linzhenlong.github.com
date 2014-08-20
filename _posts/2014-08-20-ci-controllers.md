---
layout: post
title:  "CI框架控制器相关"
date:   2014-08-20 10:36:18
categories: php
---

什么是控制器？


简而言之，一个控制器就是一个类文件，是以一种能够和 URI 关联在一起的方式来命名的。

假设这个 URI:
<pre>
<code>
http://test.lo/index.php/blog/
</code>
</pre>

在上面的例子中，CodeIgniter 将尝试寻找并装载一个名为 blog.php 的控制器。

当控制器的名字匹配 URI 的第一段时，它将被装载。

让我们试试看:  Hello World！

我们来创建一个简单的控制器，以便更直观地了解其工作原理。使用你的文本编辑器，创建一个名为 blog.php 的文件，然后输入下列代码：

<pre>
<code>
class Blog extends CI_Controller
{

    function __construct()
    {
        parent::__construct();
    }

    public function index()
    {
        $this->load->view('blog_message');
    }

    public function test($id = '1', $name = 'blog')
    {
        echo $id;
        echo "<br>";
        echo $name;
    }
}
</code>
</pre>





