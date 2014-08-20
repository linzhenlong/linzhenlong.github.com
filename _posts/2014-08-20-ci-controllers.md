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

<pre>
<code>
public function _remap()
{
    // Some code here...
}
</code>
</pre>
注意：如果你的控制器中包含一个名为 _remap() 的方法，那么不管你的 URI 中包含什么，它总会被忽略掉。
这个方法会废除掉由 URI 片段来决定哪个方法被调用的规则，允许你重新定义调用方法的规则（方法的路由规则）。

被重新定义的方法调用方式（一般是 URI 中的第二片段）将作为一个参数传递给 _remap() ：
<pre>
<code>
public function _remap($method)
{
    if ($method == 'some_method')
    {
        $this->$method();
    }
    else
    {
        $this->comments();
    }
}
</code>
</pre>
任何附加在该方法名称之后的段都会被视为 _remap() 的第二个参数（可选）。
这个可选的数组参数可以与PHP的call_user_func_array联用，模拟CodeIgniter的默认行为。
<pre>
<code>
public function _remap($method, $params = array())
{
    $method = 'process_'.$method;
    if (method_exists($this, $method))
    {
        return call_user_func_array(array($this, $method), $params);
    }
    show_404();
}
</code>
</pre>

处理输出
CodeIgniter 拥有一个输出类用来确保你修改的数据会自动被传递给浏览器。
关于这个的更多信息可以在视图和输出类里找到。
有些时候，你可能想要自己发布修改一些最终的数据或是自己把它传递给浏览器。
CodeIgniter 允许你给你的控制器增加一个名为 _output() 的方法来接收最终的数据。

注意： 如果你的控制器包含一个 _output() 方法，那么它将总是被调用，而不是直接输出最终的数据。
这个方法类似于OO里的析构函数，不管你调用任何方法这个方法总是会被执行。

例如：
<pre>
<code>
public function _output($output)
{
    echo $output;
}
</code>
</pre>

请注意，你的 _output() 将接收最终的数据。
Benchmark和内存的使用率数据将被渲染，缓存文件会被写入（如果已启用缓存），
并且 HTTP 头也将被发送（如果您使用该功能），然后交给 _output() 函数。

为了让你的控制器输出缓存正确, 它的 _output() 函数可以这样来写:
<pre>
<code>
if ($this->output->cache_expiration > 0)
{
    $this->output->_write_cache($output);
}
</code>
</pre>
如果您正在使用页面执行时间和内存使用统计的功能，这可能不完全准确，
因为他们不会考虑到你所做的任何进一步的动作。请在输出类参用可用的方法，
来控制输出以使其在任何最终进程完成之前执行。

私有方法

在某些情况下，你可能想要隐藏一些方法使之无法对外查阅。
将方法私有化很简单，只要在方法名字前面加一个下划线（“_”）做前缀就无法通过 URL 访问到了。
例如，如果你有一个像这样的方法：

<pre>
<code>
private function _utility()
{
  // some code
}
</code>
</pre>

那么，通过下面这样的 URL 进行访问是无法访问到的：
<pre>
<code>
http://test.lo/index.php/blog/_utility/

</code>
</pre>

如何将控制器放入子文件夹中
如果你在建立一个大型的应用程序，你会发现 CodeIgniter 可以很方便的将控制器放到一些子文件夹中。

只要在 application/controllers 目录下创建文件夹并放入你的控制器就可以了。

注意：  如果你要使用某个子文件夹下的功能，就要保证 URI 的第一个片段是用于描述这个文件夹的。例如说你有一个控制器在这里：

<pre>
<code>
application/controllers/products/shoes.php
</code>
</pre>
调用这个控制器的时候你的 URI 要这么写：
<pre>
<code>
http://test.lo/index.php/products/shoes/show/123
</code>
</pre>
你的每个子文件夹中需要包含一个默认的控制器，这样如果 URI 中只有子文件夹而没有具体功能的时候它将被调用。
只要将你作为默认的控制器名称在 application/config/routes.php 文件中指定就可以了。

CodeIgniter 也允许你使用 URI 路由 功能来重新定向 URI。

构造函数

如果要在你的任意控制器中使用构造函数的话，那么必须在里面加入下面这行代码：

<pre>
<code>
parent::__construct();
</code>
</pre>

这行代码的必要性在于，你此处的构造函数会覆盖掉这个父控制器类中的构造函数，所以我们要手动调用它。

<pre>
<code>
class Blog extends CI_Controller {

       public function __construct()
       {
            parent::__construct();
       }
}
</code>
</pre>
如果你需要设定某些默认的值或是在实例化类的时候运行一个默认的程序，那么构造函数在这方面就非常有用了。
构造函数并不能返回值，但是可以用来设置一些默认的功能。













