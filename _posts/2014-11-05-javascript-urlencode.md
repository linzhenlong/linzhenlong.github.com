---
layout: post
title:  "分布式搜索elasticsearch单机与服务器环境搭建"
date:   2014-10-13 16:36:18
categories: javascript
---

###js urlencode , encodeURIComponent


js 对文字进行编码涉及3个函数：escape,encodeURI,encodeURIComponent，相应3个解码函数：unescape,decodeURI,decodeURIComponent

*    传递参数时需要使用encodeURIComponent，这样组合的url才不会被#等特殊字符截断。

例如：

    <script language="javascript">document.write('<a href="http://passport.baidu.com/?logout&aid=7& u='+encodeURIComponent("http://cang.baidu.com/bruce42")+'">退出</a& gt;');</script>

*    进行url跳转时可以整体使用encodeURI

例如：

    Location.href="/encodeURI"("http://cang.baidu.com/do/s?word=百度&ct=21");

*    js使用数据时可以使用escape

例如：搜藏中history纪录

*    escape对0-255以外的unicode值进行编码时输出%u****格式，其它情况下escape，encodeURI，encodeURIComponent编码结果相同。


最多使用的应为encodeURIComponent，它是将中文、韩文等特殊字符转换成utf-8格式的url编码，所以如果给后台传递参数需要使用encodeURIComponent时需要后台解码对utf-8支持（form中的编码方式和当前页面编码方式相同）

escape不编码字符有69个：*，+，-，.，/，@，_，0-9，a-z，A-Z

encodeURI不编码字符有82个：!，#，$，&，'，(，)，*，+，,，-，.，/，:，;，=，?，@，_，~，0-9，a-z，A-Z

encodeURIComponent不编码字符有71个：!， '，(，)，*，-，.，_，~，0-9，a-z，A-Z


