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
        echo $name;
    }
}
</code>
</pre>
然后保存文件到 application/controllers/ 文件夹。

现在使用类似这样的 URL 访问你的站点：
<pre>
<code>
http://test.lo/index.php/blog/
</code>
</pre>
如果你做的没错，你应该看到Hello World！.

注意：类名必须以大写字母开头。换句话说，这是有效的：

<pre>
<code>
class Blog extends CI_Controller {

}
</code>
</pre>
下面的blog首字母b小写，是属于无效的写法：

<pre>
<code>
class blog extends CI_Controller {

}
</code>
</pre>
同时，始终确保你的控制器扩展自父控制器类，以便它能够继承其所有的方法。

方法
上面的例子中用到的方法名是 index()。如果 URI 的第二部分为空的话，会默认载入 “index” 方法。也可以将地址写成这样来访问 “Hello World”：
<pre>
<code>
http://test.lo/index.php/blog/index/
</code>
</pre>

URI 的第二部分是用来决定调用控制器中哪个方法的。

我们再来试试。在你的控制器中加入一个新的方法:
<pre>
<code>
class Blog extends CI_Controller {

 function __construct()
 {
  parent::__construct();
 }

 public function index()
 {
  echo 'Hello World！';
 }

 public function comments()
 {
  echo '看这里！';
 }
}
</code>
</pre>
现在在地址栏中输入下面的内容来访问 comments 方法：
<pre>
<code>
http://test.lo/index.php/blog/comments/
</code>
</pre>
你应该看到新的信息了：看这里！

将 URI 片段传递给方法

如果你的 URI 超过两个部分，那么超过的将被作为参数传递给方法。

举例来说，如果你的 URI 是这样的：
<pre>
<code>
http://test.lo/index.php/products/shoes/sandals/123
</code>
</pre>
URI 的第3和第4部分会被传递给你的方法（“sandals” 和 “123”）：
<pre>
<code>
class Products extends CI_Controller {

    public function shoes($sandals, $id)
    {
        echo $sandals;
        echo $id;
    }
}
</code>
</pre>
上面的方法调用时必须给两个相应的参数，要不然会出错。当然你也可以像下面这样写，就不用在调用的时候给参数了！
<pre>
<code>
class Products extends CI_Controller {

    public function shoes($sandals='Test', $id=1)
    {
        echo $sandals;
        echo $id;
    }
}
</code>
</pre>

<strong>注意：</strong>如果你使用 URI 路由特性，则传递到方法中的 URI 片段将被重新路由一次。

定义默认控制器
当你的网站不存在某个URI 或者 用户直接从根目录访问的时候，CodeIgniter 会加载默认控制器。
打开 application/config/routes.php 文件来设置默认控制器：
<pre>
<code>
$route['default_controller'] = 'Blog';
</code>
</pre>
这里的 Blog 就是你希望使用的控制器的名字。如果此时你不指定任何 URI 片段来访问你的主页就会看到默认的“Hello World”信息。

重新定义方法的调用规则

如上所述，URI 的第二片段决定会调用控制器中的哪个方法。CodeIgniter 允许你使用 _remap() 方法来废除这种规则：














