---
layout: post
title:  "CI框架Active Record 类"
date:   2014-08-21 10:36:18
categories: php
---

Active Record 类

CodeIgniter 使用的是修改过的 Active Record 数据库模式。 这种模式是以较少的程序代码来实现信息在数据库中的获取，插入，更改。
有时只用一两行的代码就能完成对数据库的操作。 CodeIgniter 不需要每一个数据库表拥有自己的类。它提供了一个更简单的接口。

不只是简单，使用 Active Record 的一个主要的优点是允许你创建独立的数据库应用程序，因为查询语法是由数据库的适配器来产生的。
它可以进行更安全的查询，因为系统会自动的对所有的输入值进行转义。

选择数据

下面的函数帮助你构建 SQL SELECT语句。

备注：如果你正在使用 PHP5，你可以在复杂情况下使用链式语法。本页面底部有具体描述。
<pre>
<code>
$this->db->get();
</code>
</pre>
运行选择查询语句并且返回结果集。可以获取一个表的全部数据。
<pre>
<code>
$query = $this->db->get('mytable');

// Produces: SELECT * FROM mytable
</code>
</pre>
第二和第三个参数允许你设置一个结果集每页纪录数(limit)和结果集的偏移(offset)
<pre>
<code>
$query = $this->db->get('mytable', 10, 20);

// Produces: SELECT * FROM mytable LIMIT 20, 10 (MySQL语法. 其他数据库在语法上略有不同)
</code>
</pre>
注意：第二参数是每页纪录数，第三个参数是偏移

你会注意到上面的函数由一个变量$query执行，这个$query可以用来显示结果集。
<pre>
<code>
$query = $this->db->get('mytable');

foreach ($query->result() as $row)
{
    echo $row->title;
}
</code>
</pre>

$this->db->get_where();
跟上面的函数一样，只是它允许你在函数的第二个参数那里添加一个 where 从句，从而不用使用 db->where() 这个函数：
<pre>
<code>
$query = $this->db->get_where('mytable', array('id' => $id), $limit, $offset);
</code>
</pre>
$this->db->select();

允许你在SQL查询中写 SELECT 部分:
<pre>
<code>
$this->db->select('title, content, date');

$query = $this->db->get('mytable');

// Produces: SELECT title, content, date FROM mytable
</code>
</pre>
注意: 如果你要查询表中的所有列，你可以不用写这个函数。省略后，CodeIgniter 会认为你要查询 全部列(SELECT *)。
$this->db->select() 可接受一个可选的第二个参数。如果你把它设为FALSE， CodeIgniter 将不会使用反引号保护你的字段或者表名 。这在进行复合查询时很有用。
<pre>
<code>
$this->db->select("('SELECT SUM(payments.amount) FROM payments WHERE payments.invoice_id=4') AS amount_paid", FALSE);
$query = $this->db->get('mytable');

</code>
</pre>

$this->db->select_max();

为你的查询编写一个 "SELECT MAX(field)"。你可以选择性的给出第二个参数，以便重命名结果字段名。
<pre>
<code>
$this->db->select_max('age');
$query = $this->db->get('members');
// Produces: SELECT MAX(age) as age FROM members

$this->db->select_max('age', 'member_age');
$query = $this->db->get('members');
// Produces: SELECT MAX(age) as member_age FROM members
</code>
</pre>
$this->db->select_min();

为你的查询编写一个 "SELECT MIN(field)" 。与 select_max() 相似, 你可以选择性地给出第二个参数，用来给结果字段重命名。
<pre>
<code>
$this->db->select_min('age');
$query = $this->db->get('members');
// 生成: SELECT MIN(age) as age FROM members
</code>
</pre>

$this->db->select_avg();

为你的查询编写一个 "SELECT AVG(field)" 。与 select_max() 相似, 你可以选择性地给出第二个参数，用来给结果字段重命名。
<pre>
<code>
$this->db->select_avg('age');
$query = $this->db->get('members');
// 生成: SELECT AVG(age) as age FROM members
</code>
</pre>

$this->db->select_sum();
为你的查询编写一个 "SELECT SUM(field)" 。与 select_max() 相似, 你可以选择性地给出第二个参数，用来给结果字段重命名。
<pre>
<code>
$this->db->select_sum('age');
$query = $this->db->get('members');
// 生成: SELECT SUM(age) as age FROM members
</code>
</pre>

$this->db->from();
允许你编写查询中的FROM部分:
<pre>
<code>
$this->db->select('title, content, date');
$this->db->from('mytable');

$query = $this->db->get();

// 生成: SELECT title, content, date FROM mytable
</code>
</pre>
说明: 正如前面所说，查询中的FROM部分可以在 $this->db->get() 函数中指定，所以你可以根据自己的喜好来选择使用哪个方法。

$this->db->join();
允许你编写查询中的JOIN部分:
<pre>
<code>
$this->db->select('*');
$this->db->from('blogs');
$this->db->join('comments', 'comments.id = blogs.id');

$query = $this->db->get();

// 生成:
// SELECT * FROM blogs
// JOIN comments ON comments.id = blogs.id
</code>
</pre>
如果你想要在查询中使用多个连接，可以多次调用本函数。

如果你需要指定 JOIN 的类型，你可以通过本函数的第三个参数来指定。
可选项包括：left, right, outer, inner, left outer, 以及 right outer.
<pre>
<code>
$this->db->join('comments', 'comments.id = blogs.id', 'left');

// 生成: LEFT JOIN comments ON comments.id = blogs.id
</code>
</pre>

$this->db->where();
本函数允许你使用四种方法中的一种来设置 WHERE 子句:
说明: 传递给本函数的所有值都会被自动转义，以便生成安全的查询。

1.简单的 key/value 方法:
<pre>
<code>
$this->db->where('name', $name);

// 生成: WHERE name = 'Joe'
</code>
</pre>
请注意等号已经为你添加。
如果你多次调用本函数，那么这些条件会被 AND 连接起来:
<pre>
<code>
$this->db->where('name', $name);
$this->db->where('title', $title);
$this->db->where('status', $status);

// WHERE name = 'Joe' AND title = 'boss' AND status = 'active'
</code>
</pre>
2.自定义 key/value 方法:
你可以在第一个参数中包含一个运算符，以便控制比较:
<pre>
<code>
$this->db->where('name !=', $name);
$this->db->where('id <', $id);

// 生成: WHERE name != 'Joe' AND id < 45
</code>
</pre>
3.关联数组方法:
<pre>
<code>
$array = array('name' => $name, 'title' => $title, 'status' => $status);

$this->db->where($array);

// 生成: WHERE name = 'Joe' AND title = 'boss' AND status = 'active'
</code>
</pre>
使用这个方法时你也可以包含运算符:
<pre>
<code>
$array = array('name !=' => $name, 'id <' => $id, 'date >' => $date);

$this->db->where($array);
</code>
</pre>

4.自定义字符串:
你可以手动的编写子句:
<pre>
<code>
$where = "name='Joe' AND status='boss' OR status='active'";

$this->db->where($where);
</code>
</pre>

$this->db->where() 接受可选的第三个参数。如果你将它设置为 FALSE, CodeIgniter 将不会为你那些包含反勾号的字段名或表名提供保护。
<pre>
<code>
$this->db->where('MATCH (field) AGAINST ("value")', NULL, FALSE);
</code>
</pre>
$this->db->or_where();
本函数与上面的那个几乎完全相同，唯一的区别是本函数生成的子句是用 OR 来连接的:
<pre>
<code>
$this->db->where('name !=', $name);
$this->db->or_where('id >', $id);

// 生成: WHERE name != 'Joe' OR id > 50
</code>
</pre>
说明: or_where() 以前被叫作 orwhere(), 后者已经过时，现已从代码中移除 orwhere()。

$this->db->where_in();

生成一段 WHERE field IN ('item', 'item') 查询语句，如果合适的话，用 AND 连接起来。

<pre>
<code>
$names = array('Frank', 'Todd', 'James');
$this->db->where_in('username', $names);
// 生成: WHERE username IN ('Frank', 'Todd', 'James')
</code>
</pre>

$this->db->or_where_in();

生成一段 WHERE field IN ('item', 'item') 查询语句，如果合适的话，用 OR 连接起来。
<pre>
<code>
$names = array('Frank', 'Todd', 'James');
$this->db->or_where_in('username', $names);
// 生成: OR username IN ('Frank', 'Todd', 'James')
</code>
</pre>

$this->db->where_not_in();

生成一段 WHERE field NOT IN ('item', 'item') 查询语句，如果合适的话，用 AND 连接起来。
<pre>
<code>
$names = array('Frank', 'Todd', 'James');
$this->db->where_not_in('username', $names);
// 生成: WHERE username NOT IN ('Frank', 'Todd', 'James')
</code>
</pre>

$this->db->or_where_not_in();
生成一段 WHERE field NOT IN ('item', 'item') 查询语句，如果合适的话，用 OR 连接起来。
<pre>
<code>
$names = array('Frank', 'Todd', 'James');
$this->db->or_where_not_in('username', $names);
// 生成: OR username NOT IN ('Frank', 'Todd', 'James')
</code>
</pre>

$this->db->like();
<hr>
本函数允许你生成 LIKE 子句，在做查询时非常有用。
说明: 传递给本函数的所有值都会被自动转义。




