---
layout: post
title:  "redis 学习(二) String数据类型"
date:   2015-04-19 10:36:18
categories: redis
---

### 一、概述

字符串类型是Redis中最为基础的数据存储类型，它在Redis中是二进制安全的，这便意味着该类型可以接受任何格式的数据，如JPEG图像数据或Json对象描述信息等。在Redis中字符串类型的Value最多可以容纳的数据长度是512M。

### 二、相关命令:

 <table class="table table-bordered table-striped table-condensed">
     <tr>
        <td>命令原型</td>
        <td>命令描述</td>
        <td>返回值</td>
     </tr>
     <tr>
        <td>APPEND key value</td>
        <td>如果该Key已经存在，APPEND命令将参数Value的数据追加到已存在Value的末尾。如果该Key不存在，APPEND命令将会创建一个新的Key/Value。</td>
        <td>追加后Value的长度。</td>
     </tr>
     <tr>
         <td>DECR key</td>
         <td>将指定Key的Value原子性的递减1。如果该Key不存在，其初始值为0，在decr之后其值为-1。如果Value的值不能转换为整型值，如Hello，该操作将执行失败并返回相应的错误信息。注意：该操作的取值范围是64位有符号整型。</td>
         <td>递减后的Value值。</td>
     </tr>
     <tr>
          <td>INCR key</td>
          <td>将指定Key的Value原子性的递增1。如果该Key不存在，其初始值为0，在incr之后其值为1。如果Value的值不能转换为整型值，如Hello，该操作将执行失败并返回相应的错误信息。注意：该操作的取值范围是64位有符号整型。 </td>
          <td>递增后的Value值。</td>
      </tr>
      <tr>
           <td>DECRBY key decrement </td>
           <td>将指定Key的Value原子性的减少decrement。如果该Key不存在，其初始值为0，在decrby之后其值为-decrement。如果Value的值不能转换为整型值，如Hello，该操作将执行失败并返回相应的错误信息。注意：该操作的取值范围是64位有符号整型。 </td>
           <td>减少后的Value值。</td>
      </tr>
      <tr>
            <td>INCRBY key increment</td>
            <td>将指定Key的Value原子性的增加increment。如果该Key不存在，其初始值为0，在incrby之后其值为increment。如果Value的值不能转换为整型值，如Hello，该操作将执行失败并返回相应的错误信息。注意：该操作的取值范围是64位有符号整型。 </td>
            <td>增加后的Value值。</td>
      </tr>
      <tr>
            <td>GET key </td>
            <td>获取指定Key的Value。如果与该Key关联的Value不是string类型，Redis将返回错误信息，因为GET命令只能用于获取string Value。 </td>
            <td>与该Key相关的Value，如果该Key不存在，返回nil。</td>
      </tr>
      <tr>
            <td>SET key value </td>
            <td>设定该Key持有指定的字符串Value，如果该Key已经存在，则覆盖其原有值。</td>
            <td>总是返回"OK"。</td>
      </tr>
      <tr>
            <td>GETSET key value</td>
            <td>原子性的设置该Key为指定的Value，同时返回该Key的原有值。和GET命令一样，该命令也只能处理string Value，否则Redis将给出相关的错误信息。</td>
            <td>返回该Key的原有值，如果该Key之前并不存在，则返回nil。</td>
      </tr>
      <tr>
            <td>STRLEN key</td>
            <td>返回指定Key的字符值长度，如果Value不是string类型，Redis将执行失败并给出相关的错误信息。</td>
            <td>返回指定Key的Value字符长度，如果该Key不存在，返回0。</td>
      </tr>
      <tr>
            <td>SETEX key seconds value</td>
            <td>原子性完成两个操作，一是设置该Key的值为指定字符串，同时设置该Key在Redis服务器中的存活时间(秒数)。该命令主要应用于Redis被当做Cache服务器使用时。</td>
            <td> </td>
      </tr>
      <tr>
            <td>SETNX key value </td>
            <td>如果指定的Key不存在，则设定该Key持有指定字符串Value，此时其效果等价于SET命令。相反，如果该Key已经存在，该命令将不做任何操作并返回。</td>
            <td>1表示设置成功，否则0。</td>
      </tr>
      <tr>
            <td>SETRANGE key offset value </td>
            <td>替换指定Key的部分字符串值。从offset开始，替换的长度为该命令第三个参数value的字符串长度，其中如果offset的值大于该Key的原有值Value的字符串长度，Redis将会在Value的后面补齐(offset - strlen(value))数量的0x00，之后再追加新值。如果该键不存在，该命令会将其原值的长度假设为0，并在其后添补offset个0x00后再追加新值。鉴于字符串Value的最大长度为512M，因此offset的最大值为536870911。最后需要注意的是，如果该命令在执行时致使指定Key的原有值长度增加，这将会导致Redis重新分配足够的内存以容纳替换后的全部字符串，因此就会带来一定的性能折损。 </td>
            <td>修改后的字符串Value长度。</td>
      </tr>
      <tr>
            <td>GETRANGE key start end</td>
            <td>如果截取的字符串长度很短，我们可以该命令的时间复杂度视为O(1)，否则就是O(N)，这里N表示截取的子字符串长度。该命令在截取子字符串时，将以闭区间的方式同时包含start(0表示第一个字符)和end所在的字符，如果end值超过Value的字符长度，该命令将只是截取从start开始之后所有的字符数据。</td>
            <td>子字符串</td>
      </tr>
      <tr>
            <td>SETBIT key offset value </td>
            <td>设置在指定Offset上BIT的值，该值只能为1或0，在设定后该命令返回该Offset上原有的BIT值。如果指定Key不存在，该命令将创建一个新值，并在指定的Offset上设定参数中的BIT值。如果Offset大于Value的字符长度，Redis将拉长Value值并在指定Offset上设置参数中的BIT值，中间添加的BIT值为0。最后需要说明的是Offset值必须大于0。 </td>
            <td>在指定Offset上的BIT原有值。</td>
      </tr>
      <tr>
            <td>GETBIT key offset </td>
            <td>返回在指定Offset上BIT的值，0或1。如果Offset超过string value的长度，该命令将返回0，所以对于空字符串始终返回0。 </td>
            <td>在指定Offset上的BIT值 </td>
      </tr>
      <tr>
            <td>MGET key [key ...] </td>
            <td>N表示获取Key的数量。返回所有指定Keys的Values，如果其中某个Key不存在，或者其值不为string类型，该Key的Value将返回nil。 </td>
            <td>返回一组指定Keys的Values的列表。 </td>
      </tr>
      <tr>
            <td>MSET key value [key value ...] </td>
            <td>N表示指定Key的数量。该命令原子性的完成参数中所有key/value的设置操作，其具体行为可以看成是多次迭代执行SET命令。 </td>
            <td>该命令不会失败，始终返回OK。  </td>
      </tr>
      <tr>
            <td>MSETNX key value [key value ...] </td>
            <td>N表示指定Key的数量。该命令原子性的完成参数中所有key/value的设置操作，其具体行为可以看成是多次迭代执行SETNX命令。然而这里需要明确说明的是，如果在这一批Keys中有任意一个Key已经存在了，那么该操作将全部回滚，即所有的修改都不会生效。</td>
            <td>1表示所有Keys都设置成功，0则表示没有任何Key被修改。</td>
      </tr>
 </table>
 
 
 
   命令原型 | 命令描述 | 返回值 
   -------—|---------|------
   aaaa    | bbbbb   | cccc  
   aaaa    | bbbbb   | cccc  
   aaaa    | bbbbb   | cccc  
   aaaa    | bbbbb   | cccc  
   aaaa    | bbbbb   | cccc  
 
 
 
 
 ### 三、命令示例：
 
 1.SET / GET/ APPEND/ STRLEN
 
    redis-cli -h 127.0.0.1 -p 6379
    127.0.0.1:6379> EXISTS mykey   #判断该键是否存在，存在返回1，否则返回0。
    (integer) 0
    127.0.0.1:6379> APPEND mykey "hello"  #该键并不存在，因此append命令返回当前Value的长度。
    (integer) 5
    127.0.0.1:6379> APPEND mykey "world"  #该键已经存在，因此返回追加后Value的长度。
    (integer) 10
    127.0.0.1:6379> GET mykey             #通过get命令获取该键，以判断append的结果。
    "helloworld"
    127.0.0.1:6379> SET mykey "this is a test"   # 通过set命令为键设置新值，并覆盖原有值。
    OK
    127.0.0.1:6379> get mykey
    "this is a test"
    127.0.0.1:6379> STRLEN mykey       #获取指定Key的字符长度，等效于C库中strlen函数
    (integer) 14  
    