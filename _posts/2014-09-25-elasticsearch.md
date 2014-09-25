##Elasticsearch 学习


##面向文档

程序中的对象很少是单纯的键值与数值的列表。更多的时候它拥有一个复杂的结构，比如包含了日期、地理位置、对象、数组等。

迟早你会把这些对象存储在数据库中。你会试图将这些丰富而又庞大的数据都放到一个由行与列组成的关系数据库中，然后你不得不根据每个字段的格式来调整数据，然后每次重建它你都要检索一遍数据。

Elasticsearch是面向文档型数据库，这意味着它存储的是整个对象或者文档，它不但会存储它们，还会为他们建立索引，这样你就可以搜索他们了。你可以在Elasticsearch中索引、搜索、排序和过滤这些文档。不需要成行成列的数据。这将会是完全不同的一种面对数据的思考方式，这也是为什么Elasticsearch可以执行复杂的全文搜索的原因。


##JSON

Elasticsearch使用JSON (或称作JavaScript Object Notation )作为文档序列化的格式。JSON已经被大多数语言支持，也成为NoSQL领域的一个标准格式。它简单、简洁、易于阅读。

把这个JSON想象成一个用户对象:

    {
        "email":      "john@smith.com",
        "first_name": "John",
        "last_name":  "Smith",
        "about": {
            "bio":         "Eco-warrior and defender of the weak",
            "age":         25,
            "interests": [ "dolphins", "whales" ]
        },
        "join_date": "2014/05/01",
    }
    
虽然user这个对象非常复杂，但是它的结构和含义都被保留到JSON中了。在Elasticsearch中，将对象转换为JSON并作为索引要比在表结构中做相同的事情简单多了

>将你的数据转换为JSON

几乎所有的语言都有将任意数据转换、机构化成JSON，或者将对象转换为JSON的模块。查看serialization以及marshalling两个JSON模块。The official Elasticsearch clients 也可以帮你自动结构化JSON。


##启程

为了能让你感受一下Elasticsearch能做什么以及它是有多么的易用，我们会先为你简单展示一下，其中包括了基本的创建索引，搜索以及聚合。

我们会在这里向你介绍一些新的术语以及简单的概念，即使你没有马上接受这些概念也没有关系。我们会在之后的章节中逐渐帮你理解它们。

所以，准备开始享受Elasticsearch的学习之旅把！


##建立一个员工名单

想象我们正在为一个名叫megacorp的公司的HR部门制作一个新的员工名单系统，这些名单应该可以满足实时协同工作，所以它应该可以满足以下要求：

+    数据可以包含多个值的标签、数字以及纯文本内容，
+    可以检索任何职员的所有数据。
+    允许结构化搜索。例如，查找30岁以上的员工。
+    允许简单的全文搜索以及相对复杂的短语搜索。
+    在返回的匹配文档中高亮关键字。
+    拥有数据统计与管理的后台。



##为员工档案创建索引

这个项目的第一步就是存储员工的数据。这样你就需要一个“员工档案”的表单，这样每个文档都代表着一个员工。在Elasticsearch中，存储数据的行为就叫做索引(indexing)，但是在我们索引数据前，我们需要决定将数据存储在哪里。

在Elasticsearch中，文档术语一种类型(type)，各种各样的类型存在于一个索引中。你也可以通过类比传统的关系数据库得到一些大致的相似之处：

    关系数据库     ⇒ 数据库 ⇒ 表    ⇒ 行    ⇒ 列(Columns)
    Elasticsearch  ⇒ 索引   ⇒ 类型  ⇒ 文档  ⇒ 字段(Fields)
    
一个Elasticsearch集群可以包含多个索引（数据库），也就是说其中包含了很多类型（表）。这些类型中包含了很多的文档（行），然后每个文档中又包含了很多的字段（列）。

>索引 索引 索引

你可能发现在Elasticsearch中，索引这个词汇已经被赋予了太多意义，所以在这里我们有必要澄清一下：

>索引 (名词)

如上文所说，一个索引就类似于传统关系型数据库中的数据库。这里就是存储相关文档的的地方。

>索引 (动词)

为一个文档创建索引是把一个文档存储到一个索引(名词)中的过程，这样它才能被检索。这个过程非常类似于SQL中的**INSERT**命令，如果已经存在文档，新的文档将会覆盖旧的文档。

>反向索引

在关系数据库中的某列添加一个索引，比如多路搜索树(B-Tree)索引，就可以加速数据的取回速度， Elasticsearch以及Lucene使用的是一个叫做反向索引(inverted index)的结构来实现相同的功能。

>通常，每个文档中的字段都被创建了索引（拥有一个反向索引），因此他们可以被搜索。如果一个字段缺失了反向索引的话，它将不能被搜索。我们将会在之后的《反向索引》章节中详细介绍它。


所以为了创建员工名单，我们需要进行如下操作：

+    为每一个员工的文档创建索引，每个文档都包含了一个员工的所有信息.
+    每个文档都会被标记为employee类型。
+    这种类型将存活在employee这个索引中。
+    这个索引将会存储在Elasticsearch的集群中

在实际的操作中，这些操作是非常简单的（及时看起来有这么多步骤）。我们可以把如此之多的操作通过一个命令来完成：

    PUT /megacorp/employee/1
    {
        "first_name" : "John",
        "last_name" :  "Smith",
        "age" :        25,
        "about" :      "I love to go rock climbing",
        "interests": [ "sports", "music" ]
    }
    
注意在/megacorp/employee/1路径下，包含了三个部分：

<table>
    <th>名字</th>
    <th>内如</th>
    <tr>
        <td>megacorp</td>
        <td>索引的名字</td>
    </tr>
    <tr>
        <td>megacorp</td>
        <td>索引的名字</td>
    </tr>
    <tr>
        <td>1</td>
        <td>当前员工的ID</td>
    </tr>
</table>


请求部分，也就是JSON文档，在这里包含了关于这名员工的所有信息。他的名字是``John Smith''，他已经25岁了，他很喜欢攀岩。

怎么样？很简单吧！我们在操作前不需要进行任何管理操作，比如创建索引，或者为字段指定数据的类型。我们就这么直接地为一个文档创建了索引。Elasticsearch会在创建的时候为它们设定默认值，所以所有管理操作已经在后台被默默地完成了。

在进行下一步之前，我们再为这个目录添加更多的员工信息吧：

    PUT /megacorp/employee/2
    {
        "first_name" :  "Jane",
        "last_name" :   "Smith",
        "age" :         32,
        "about" :       "I like to collect rock albums",
        "interests":  [ "music" ]
    }

    PUT /megacorp/employee/3
    {
        "first_name" :  "Douglas",
        "last_name" :   "Fir",
        "age" :         35,
        "about":        "I like to build cabinets",
        "interests":  [ "forestry" ]
    }
    
 
##检索文档

现在，我们已经在Elasticsearch中存储了一些数据，我们可以开始根据这个项目的需求进行工作了。第一个需求就是要能搜索每一个员工的数据。

对于Elasticsearch来说，这是非常简单的。我们只需要执行一次HTTP GET请求，然后指出文档的地址，也就是索引、类型以及ID即可。通过这三个部分，我们就可以得到原始的JSON文档：

    GET /megacorp/employee/1
    
返回的内容包含了这个文档的元数据信息，而John Smith的原始JSON文档也在_source字段中出现了：

    {
      "_index" :   "megacorp",
      "_type" :    "employee",
      "_id" :      "1",
      "_version" : 1,
      "found" :    true,
      "_source" :  {
          "first_name" :  "John",
          "last_name" :   "Smith",
          "age" :         25,
          "about" :       "I love to go rock climbing",
          "interests":  [ "sports", "music" ]
      }
    }
    

我们通过将HTTP后的请求方式由GET改变为PUT来获取文档，同理，我们也可以将其更换为DELETE来删除这个文档，HEAD是用来查询这个文档是否存在的。如果你想替换一个已经存在的文档，你只需要使用PUT再次发出请求即可。


##简易搜索

GET命令真的相当简单，你只需要告诉它你要什么即可。接下来，我们来试一下稍微复杂一点的搜索。

我们首先要完成一个最简单的搜索命令来搜索全部员工：

    GET /megacorp/employee/_search
    
你可以发现我们正在使用**megacorp**索引，**employee**类型，但是我们我们并没有指定文档的ID，我们现在使用的是**_search**端口。

你可以再返回的hits中我们发现们录入的三个文档。搜索会默认返回最前的10个数值。

    {
       "took":      6,
       "timed_out": false,
       "_shards": { ... },
       "hits": {
          "total":      3,
          "max_score":  1,
          "hits": [
             {
                "_index":         "megacorp",
                "_type":          "employee",
                "_id":            "3",
                "_score":         1,
                "_source": {
                   "first_name":  "Douglas",
                   "last_name":   "Fir",
                   "age":         35,
                   "about":       "I like to build cabinets",
                   "interests": [ "forestry" ]
                }
             },
             {
                "_index":         "megacorp",
                "_type":          "employee",
                "_id":            "1",
                "_score":         1,
                "_source": {
                   "first_name":  "John",
                   "last_name":   "Smith",
                   "age":         25,
                   "about":       "I love to go rock climbing",
                   "interests": [ "sports", "music" ]
                }
         },
         {
                "_index":         "megacorp",
                "_type":          "employee",
                "_id":            "2",
                "_score":         1,
                "_source": {
                   "first_name":  "Jane",
                   "last_name":   "Smith",
                   "age":         32,
                   "about":       "I like to collect rock albums",
                   "interests": [ "music" ]
                }
             }
          ]
       }
    }


注意：反馈值中不仅会到诉你匹配到那些文档，同时也会把这个文档都会包含到其中：我们需要搜索的用户的所有信息。

接下来，我们将要尝试着实现搜索一下哪些员工的姓氏中包含Smith。为了实现这个，我们需要使用一种轻量的搜索方法。这种方法经常被称做查询字符串(query string)搜索，因为我们通过URL来传递查询的关键字：

    GET /megacorp/employee/_search?q=last_name:Smith
    
我们依旧使用_search端口，然后可以将参数传入给q=。这样我们就可以得到姓Smith的结果：

    {
       ...
       "hits": {
          "total":      2,
          "max_score":  0.30685282,
          "hits": [
             {
                ...
                "_source": {
                   "first_name":  "John",
                   "last_name":   "Smith",
                   "age":         25,
                   "about":       "I love to go rock climbing",
                   "interests": [ "sports", "music" ]
                }
             },
             {
                ...
                "_source": {
                   "first_name":  "Jane",
                   "last_name":   "Smith",
                   "age":         32,
                   "about":       "I like to collect rock albums",
                   "interests": [ "music" ]
               }
             }
          ]
       }
    }
    
    
##使用Query DSL搜索

查询字符串是通过命令语句完成点对点(ad hoc)的搜索，但是这也有它的局限性（可参阅《搜索局限性》章节）。Elasticsearch提供了更加丰富灵活的查询语言，它被称作Query DSL，通过它你可以完成更加复杂、强大的搜索任务。

DSL (Domain Specific Language 领域特定语言)需要使用JSON作为主体，我们还可以这样查询姓Smith的员工：

    GET /megacorp/employee/_search
    {
        "query" : {
            "match" : {
                "last_name" : "Smith"
            }
        }
    }
    

这个请求会返回同样的结果。你会发现我们在这里没有使用查询字符串，而是使用了一个由JSON构成的请求体，其中使用了match查询法，随后我们还将会学习到其他的查询类型。

##更加复杂的搜索

接下来，我们再提高一点儿搜索的难度。我们依旧要寻找出姓Smith的员工，但是我们还将添加一个年龄大于30岁的限定条件。我们的查询语句将会有一些细微的调整来以识别结构化搜索的限定条件 filter（过滤器）:

    GET /megacorp/employee/_search
    {
        "query" : {
            "filtered" : {
                "filter" : {
                    "range" : {
                        "age" : { "gt" : 30 } <1>
                    }
                },
                "query" : {
                    "match" : {
                        "last_name" : "Smith" <2>
                    }
                }
            }
        }
    }
    
    
+    这一部分的语句是rangefilter ，它可以查询所有超过30岁的数据 -- gt代表 greater than （大于）。
+    这一部分我们前一个操作的matchquery 是一样的

先不要被这么多的语句吓到，我们将会在之后带你逐渐了解他们的用法。你现在只需要知道我们添加了一个filter，可以在match的搜索基础上再来实现区间搜索。现在，我们的只会显示32岁的名为Jane Smith的员工了：

    {
       ...
       "hits": {
          "total":      1,
          "max_score":  0.30685282,
          "hits": [
             {
                ...
                "_source": {
                   "first_name":  "Jane",
                   "last_name":   "Smith",
                   "age":         32,
                   "about":       "I like to collect rock albums",
                   "interests": [ "music" ]
                }
             }
          ]
       }
    }
    

##全文搜索

上面的搜索都很简单：名字搜索、通过年龄过滤。接下来我们来学习一下更加复杂的搜索，全文搜索——一项在传统数据库很难实现的功能。 我们将会搜索所有喜欢rock climbing的员工:

    GET /megacorp/employee/_search
    {
        "query" : {
            "match" : {
                "about" : "rock climbing"
            }
        }
    }
    

你会发现我们同样使用了match查询来搜索about字段中的rock climbing。我们会得到两个匹配的文档：

    {
       ...
       "hits": {
          "total":      2,
          "max_score":  0.16273327,
          "hits": [
             {
                ...
                "_score":         0.16273327, <1>
                "_source": {
                   "first_name":  "John",
                   "last_name":   "Smith",
                   "age":         25,
                   "about":       "I love to go rock climbing",
                   "interests": [ "sports", "music" ]
                }
             },
             {
                ...
                "_score":         0.016878016, <1>
                "_source": {
                   "first_name":  "Jane",
                   "last_name":   "Smith",
                   "age":         32,
                   "about":       "I like to collect rock albums",
                   "interests": [ "music" ]
                }
             }
          ]
       }
    }
    
1.    相关评分

通常情况下，Elasticsearch会通过相关性来排列顺序，第一个结果中，John Smith的about字段中明确地写到rock climbing。而在Jane Smith的about字段中，提及到了rock，但是并没有提及到climbing，所以后者的_score就要比前者的低。

这个例子很好地解释了Elasticsearch是如何执行全文搜索的。对于Elasticsearch来说，相关性的感念是很重要的，而这也是它与传统数据库在返回匹配数据时最大的不同之处。


##段落搜索

能够找出每个字段中的独立单词最然很好，但是有的时候你可能还需要去匹配精确的短语或者段落。例如，我们只需要查询到about字段只包含rock climbing的短语的员工。

为了实现这个效果，我们将对match查询变为match_phrase查询：

    GET /megacorp/employee/_search
    {
        "query" : {
            "match_phrase" : {
                "about" : "rock climbing"
            }
        }
    }
    
    
这样，系统会没有异议地返回John Smith的文档：

    {
       ...
       "hits": {
          "total":      1,
          "max_score":  0.23013961,
          "hits": [
             {
                ...
                "_score":         0.23013961,
                "_source": {
                   "first_name":  "John",
                   "last_name":   "Smith",
                   "age":         25,
                   "about":       "I love to go rock climbing",
                   "interests": [ "sports", "music" ]
                }
             }
          ]
       }
    }
    

##高亮我们的搜索

很多程序希望能在搜索结果中高亮匹配到的关键字来告诉用户这个文档是如何匹配他们的搜索的。在Elasticsearch中找到高亮片段是非常容易的。

让我们回到之前的查询，但是添加一个highlight参数：

    GET /megacorp/employee/_search
    {
        "query" : {
            "match_phrase" : {
                "about" : "rock climbing"
            }
        },
        "highlight": {
            "fields" : {
                "about" : {}
            }
        }
    }
    
    
当我们运行这个查询后，相同的命中结果会被返回，但是我们会得到一个新的名叫highlight的部分。在这里包含了about字段中的匹配单词，并且会被<em></em>HTML字符包裹住：

    {
       ...
       "hits": {
          "total":      1,
          "max_score":  0.23013961,
          "hits": [
             {
                ...
                "_score":         0.23013961,
                "_source": {
                   ""first_name":  "John",
                   "last_name":   "Smith",
                   "age":         25,
                   "about":       "I love to go rock climbing",
                   "interests": [ "sports", "music" ]
                },
                "highlight": {
                   "about": [
                      "I love to go <em>rock</em> <em>climbing</em>" <1>
                   ]
                }
             }
          ]
       }
    }
    
在原有文本中高亮关键字


##统计

最后，我们还有一个需求需要完成：可以让老板在职工目录中进行统计。Elasticsearch把这项功能称作汇总 (aggregations)，通过这个功能，我们可以针对你的数据进行复杂的统计。这个功能有些类似于SQL中的GROUP BY，但是要比它更加强大。

例如，让我们找一下员工中最受欢迎的兴趣是什么：

    GET /megacorp/employee/_search
    {
      "aggs": {
        "all_interests": {
          "terms": { "field": "interests" }
        }
      }
    }
    
    
请忽略语法，让我们先来看一下结果：

    {
       ...
       "hits": { ... },
       "aggregations": {
          "all_interests": {
             "buckets": [
                {
                   "key":       "music",
                   "doc_count": 2
                },
                {
                   "key":       "forestry",
                   "doc_count": 1
                },
                {
                   "key":       "sports",
                   "doc_count": 1
                }
             ]
          }
       }
    }
    
    
我们可以发现有两个员工喜欢音乐，还有一个喜欢森林，还有一个喜欢运动。这些数据并没有被预先计算好，它们是在文档被查询的同时实时计算得出的。如果你想要查询姓Smith的员工的兴趣汇总情况，你就可以执行如下查询：


    GET /megacorp/employee/_search
    {
      "query": {
        "match": {
          "last_name": "smith"
        }
      },
      "aggs": {
        "all_interests": {
          "terms": {
            "field": "interests"
          }
        }
      }
    }
    
这样，all_interests的统计结果就只会包含满足查询的文档了：

    ...
      "all_interests": {
         "buckets": [
            {
               "key": "music",
               "doc_count": 2
            },
            {
               "key": "sports",
               "doc_count": 1
            }
         ]
      }
      
汇总还允许多个层面的统计。比如我们还可以统计每一个兴趣下的平均年龄：

    GET /megacorp/employee/_search
    {
        "aggs" : {
            "all_interests" : {
                "terms" : { "field" : "interests" },
                "aggs" : {
                    "avg_age" : {
                        "avg" : { "field" : "age" }
                    }
                }
            }
        }
    }
    
虽然这次返回的汇总结果变得更加复杂了，但是它依旧很容易理解：

    ...
      "all_interests": {
         "buckets": [
            {
               "key": "music",
               "doc_count": 2,
               "avg_age": {
                  "value": 28.5
               }
            },
            {
               "key": "forestry",
               "doc_count": 1,
               "avg_age": {
                  "value": 35
               }
            },
            {
               "key": "sports",
               "doc_count": 1,
               "avg_age": {
                  "value": 25
               }
            }
         ]
      }


在这个丰富的结果中，我们不但可以看到兴趣的统计数据，还能针对不同的兴趣来分析喜欢这个兴趣的平均年龄。

即使你现在还不能很好地理解语法，但是相信你还是能发现，用这个功能来实现如此复杂的统计工作是这样的简单。你的极限取决于你存入了什么样的数据哟

##文档是什么？

在很多程序中，大部分实体或者对象都被序列化为包含键和值的JSON对象。键是一个字段或者属性的名字，值可以是一个字符串、数字、布尔值、对象、数组或者是其他的特殊类型，比如代表日期的字符串或者单表地理位置的对象：

    {
    "name":         "John Smith",
    "age":          42,
    "confirmed":    true,
    "join_date":    "2014-06-01",
    "home": {
        "lat":      51.5,
        "lon":      0.1
    },
    "accounts": [
        {
            "type": "facebook",
            "id":   "johnsmith"
        },
        {
            "type": "twitter",
            "id":   "johnsmith"
        }
        ]
    }
    
通常情况下，我们使用可以互换对象和文档。然而，还是有一个区别的。对象(object )仅仅是一个JSON对象,类似于哈希，哈希映射，字典或关联数组。对象(Objects)则可以包含其他对象(Objects)。
在Elasticsearch中，文档这个单词有特殊的含义。它指的是在Elasticsearch中被存储到唯一ID下的由最高级或者根对象 (root object )序列化而来的JSON。


#文档元数据

>一个文档不只包含了数据。它还包含了元数据(metadata) —— 关于文档的信息。有三个元数据元素是必须存在的，它们是：

<table>
    <th><strong>名字</strong></th>
    <th><strong>说明</strong></th>
    <tr>
        <td>_index</td>
        <td>文档存储的地方</td>
    </tr>
    <tr>
        <td>_type</td>
        <td>文档代表的对象种类</td>
    </tr>
    <tr>
        <td>_id</td>
        <td>文档的唯一编号</td>
    </tr>
</table>


##_index

索引 类似于传统数据库中的"数据库"——也就是我们存储并且索引相关数据的地方。

>*TIP:*

在Elasticsearch中，我们的数据都在分片中被存储以及索引，索引只是一个逻辑命名空间，它可以将一个或多个分片组合在一起。然而，这只是一个内部的运作原理——我们的程序可以根本不用关心分片。对于我们的程序来说，我们的文档存储在索引中。剩下的交给Elasticsearch就可以了。

*索引名称必须全部小写，也不能以下划线开头，不能包含逗号。我们可以用website作为我们的索引名称*


##_type

在程序中，我们使用对象代表“物品”，比如一个用户、一篇博文、一条留言或者一个邮件。每一个对象都属于一种类型，类型定义了对象的属性或者与数据的关联。用户类的对象可能就会包含名字、性别、年龄以及邮箱地址等。

在传统的数据库中，我们总是将同类的数据存储在同一个表中，因为它们的数据格式是相同的。同理，在Elasticsearch中，我们使用同样类型的文档来代表同类“事物”，也是因为它们的数据结构是相同的。

每一个类型都拥有自己的映射(mapping)或者结构定义，它们定义了当前类型下的数据结构，类似于数据库表中的列。所有类型下的文档会被存储在同一个索引下，但是映射会告诉Elasticsearch不同的数据应该如何被索引。


##_id

id是一个字符串，当它与_index以及_type组合时，就可以来代表Elasticsearch中一个特定的文档。我们创建了一个新的文档时，你可以自己提供一个_id，或者也可以让Elasticsearch帮你生成一个。


#索引一个文档

文档通过**索引**API被*索引*——存储并使其可搜索。但是最开始我们需要决定我们将文档存储在那里。正如之前提到的，一篇文档通过**_index**, **_type**以及**_id**来确定它的唯一性。我们可以自己提供一个_id，或者也使用indexAPI 帮我们生成一个。

##使用自己的ID

如果你的文档拥有天然的标示符（例如**user_account**字段或者文档中其他的标识值），这时你就可以提供你自己的**_id**，这样使用**index**API：

    PUT /{index}/{type}/{id}
    {
      "field": "value",
      ...
    }
    
几个例子。如果我们的索引叫做**"website"**，我们的类型叫做**"blog"**，然后我们选择**"123"**作为ID的编号。这时，请求就是这样的：

    PUT /website/blog/123
    {
      "title": "My first blog entry",
      "text":  "Just trying this out...",
      "date":  "2014/01/01"
    }
    
Elasticsearch回馈内容：

    {
       "_index":    "website",
       "_type":     "blog",
       "_id":       "123",
       "_version":  1,
       "created":   true
    }
    
这个回馈意味着我们的索引请求已经被成功创建，其中还包含了**_index**, **_type**以及**_id**的元数据，以及一个新的元素**_version**。

在Elasticsearch中，每一个文档都有一个版本号码。每当文档产生变化时（包括删除），**_version**就会增大。在《版本控制》中，我们将会详细讲解如何使用**_version**的数字来确认你的程序不会随意替换掉不想覆盖的数据。


##自增ID

如果我们的数据中没有天然的标示符，我们可以让Elasticsearch为我们自动生成一个。请求的结构发生了变化：我们把**PUT**——“把文档存储在这个地址中”变量变成了**POST**——“把文档存储在这个地址下”。

这样一来，请求中就只包含 **_index**和**_type**了：

    POST /website/blog/
    {
      "title": "My second blog entry",
      "text":  "Still trying this out...",
      "date":  "2014/01/01"
    }
    
这次的反馈和之前基本一样，只有**_id**改成了系统生成的自增值:

    {
       "_index":    "website",
       "_type":     "blog",
       "_id":       "wM0OSFhDQXGZAWDf0-drSA",
       "_version":  1,
       "created":   true
    }
    
自生成ID是由22个字母组成的，安全 universally unique identifiers 或者被称为UUIDs。


##搜索文档

要从Elasticsearch中获取文档，我们需要使用同样的**_index**，**_type**以及 **_id**但是不同的HTTP变量GET：

    GET /website/blog/123?pretty
    
返回结果包含了之前提到的内容，以及一个新的字段**_source**，它包含我们在最初创建索引时的原始JSON文档。

    {
      "_index" :   "website",
      "_type" :    "blog",
      "_id" :      "123",
      "_version" : 1,
      "found" :    true,
      "_source" :  {
          "title": "My first blog entry",
          "text":  "Just trying this out..."
          "date":  "2014/01/01"
      }
    }
    
>pretty

在任意的查询字符串中添加**pretty**参数，类似上面的请求，Elasticsearch就可以得到**优美打印**的更加易于识别的JSON结果。**_source**字段不会执行优美打印，它的样子取决于我们录入的样子。


GET请求的返回结果中包含**{"found": true}**。这意味着这篇文档确实被找到了。如果我们请求了一个不存在的文档，我们依然会得到JSON反馈，只是**found**的值会变为**false**。


同样，HTTP返回码也会由**'200 OK'**变为**'404 Not Found'**。我们可以在**curl后添加-i**，这样你就能得到反馈头文件：

    curl -i -XGET /website/blog/124?pretty
    
反馈结果就会是这个样子：

    HTTP/1.1 404 Not Found
    Content-Type: application/json; charset=UTF-8
    Content-Length: 83

    {
      "_index" : "website",
      "_type" :  "blog",
      "_id" :    "124",
      "found" :  false
    }
    

##检索文档中的一部分

通常，**GET**请求会将整个文档放入**_source**字段中一并返回。但是可能你只需要**title**字段。你可以使用**_source**得到指定字段。如果需要多个字段你可以使用逗号分隔：

    GET /website/blog/123?_source=title,text
    
现在_source字段中就只会显示你指定的字段:

    {
      "_index" :   "website",
      "_type" :    "blog",
      "_id" :      "123",
      "_version" : 1,
      "exists" :   true,
      "_source" : {
          "title": "My first blog entry" ,
          "text":  "Just trying this out..."
      }
    }
    
或者你只想得到_source字段而不要其他的元数据，你可以这样请求：

    GET /website/blog/123/_source
    
这样结果就只返回:

    {
       "title": "My first blog entry",
       "text":  "Just trying this out...",
       "date":  "2014/01/01"
    }
    

##检查文档是否存在

如果确实想检查一下文档是否存在，你可以试用**HEAD**来替代**GET**方法，这样就是会返回HTTP头文件：

    curl -i -XHEAD /website/blog/123
    
如果文档存在，Elasticsearch将会返回200 OK的状态码：

    HTTP/1.1 200 OK
    Content-Type: text/plain; charset=UTF-8
    Content-Length: 0
    
如果不存在将会返回404 Not Found状态码：

    curl -i -XHEAD /website/blog/124
    
    HTTP/1.1 404 Not Found
    Content-Type: text/plain; charset=UTF-8
    Content-Length: 0
    
当然，这个反馈只代表了你查询的那一刻文档不存在，但是不代表几毫秒后它是否存在，很可能与此同时，另一个进程正在穿件文档。


##更新整个文档

在Documents中的文档是不可改变的。所以如果我们需要改变已经存在的文档，我们可以使用《索引》中提到的**index**API来重新索引或者替换掉它：

    PUT /website/blog/123
    {
      "title": "My first blog entry",
      "text":  "I am starting to get the hang of this...",
      "date":  "2014/01/02"
    }
    
在反馈中，我们可以发现Elasticsearch已经将_version数值增加了：

    {
      "_index" :   "website",
      "_type" :    "blog",
      "_id" :      "123",
      "_version" : 2,
      "created":   false <1>
    }
    
1.    **created**被标记为 **false**是因为在同索引、同类型下已经存在同ID的文档。

在内部，Elasticsearch已经将旧文档标记为删除并且添加了新的文档。旧的文档并不会立即消失，但是你也无法访问他。Elasticsearch会在你继续添加更多数据的时候在后台清理已经删除的文件。

+    从旧的文档中检索JSON
+    修改它
+    删除修的文档
+    i索引一个新的文档

唯一不同的是，使用了**update**API你就不需要再使用**get**然后再操作**index**请求了。


##创建一个文档

我们是如何确定当我们索引一个文档时，我们是创建了一个新的文档还是覆盖了一个已经存在的文档呢？

请牢记**_index**,**_type**以及**_id**组成了唯一的文档标记，所以为了确定我们创建的内容是全新的的最简单的方法就是使用**POST**方法，让Elasticsearch自动创建不同的**_id**：

    POST /website/blog/
    { ... }
    
然而，我们我们可能已经决定好了**_id**，所以我们需要告诉Elasticsearch只有当同**_index**，**_type**以及**_id**的文档不存在时才接受我们的请求。实现这个目的有两种方法，他们实质上是一样的，你可以选择你认为方便的那种：

第一种是在查询中中添加**op_type**参数：

    PUT /website/blog/123?op_type=create
    { ... }
    
或者在请求最后添加 **/_create**:

    PUT /website/blog/123/_create
    { ... }
    
如果成功创建了新的文档，Elasticsearch将会返回常见的元数据以及**201 Created**的HTTP反馈码。

而如果存在同名文件，Elasticsearch将会返回一个**409 Conflict**的HTTP反馈码，以及如下方的错误信息：

    {
      "error" : "DocumentAlreadyExistsException[[website][4] [blog][123]:
             document already exists]",
      "status" : 409
    }
    
    
    
##删除一个文档

删除文档的基本模式和之前的基本一样，只不过是需要更换成**DELETE**方法：

    DELETE /website/blog/123

如果文档存在，那么Elasticsearch就会返回一个**200 OK**的HTTP相应码，返回的结果就会像下面展示的一样。请注意**_version**的数字已经增加了。

    {
      "found" :    true,
      "_index" :   "website",
      "_type" :    "blog",
      "_id" :      "123",
      "_version" : 3
    }
    
如果文档不存在，那么我们就会得到一个**404 Not Found**的响应码，返回的内容就会是这样的：

    {
      "found" :    false,
      "_index" :   "website",
      "_type" :    "blog",
      "_id" :      "123",
      "_version" : 4
    }
    
尽管文档并不存在（**"found"**值为**false**），但是**_version**的数值仍然增加了。这个。这个就是内部管理的一部分，它保证了我们在多个节点间的不同操作都被标记了正确的顺序。

**删除一个文档也不会立即生效，它只是被标记成已删除。Elasticsearch将会在你之后添加更多索引的时候才会在后台进行删除内容的清理。**

##处理冲突

当你使用索引API来更新一个文档时，我们先看到了原始文档，然后修改它，最后一次性地将整个新文档进行再次索引处理。Elasticsearch会根据请求发出的顺序来选择出最新的一个文档进行保存。但是，如果在你修改文档的同时其他人也发出了指令，那么他们的修改将会丢失。

很长时间以来，这其实都不是什么大问题。或许我们的主要数据还是存储在一个关系数据库中，而我们只是将为了可以搜索，才将这些数据拷贝到Elasticsearch中。或许发生多个人同时修改一个文件的概率很小，又或者这些偶然的数据丢失并不会影响到我们的正常使用。

但是有些时候如果我们丢失了数据就会出大问题。想象一下，如果我们使用Elasticsearch来存储一个网店的商品数量。每当我们卖出一件，我们就会将这个数量减少一个。

突然有一天，老板决定来个大促销。瞬间，每秒就产生了多笔交易。并行处理，多个进程来处理交易：


##悲观并发控制（PCC）

这一点在关系数据库中被广泛使用。假设这种情况很容易发生，我们就可以组织对这一资源的访问。典型的例子就是当我们在读取一个数据前先锁定这一行，然后确保只有读取到数据的这个线程可以修改这一行数据。



##乐观并发控制（OCC）

Elasticsearch所使用的。假设这种情况并不会经常发生，也不会去组织某一数据的访问。然而，如果基础数据在我们读取和写入的间隔中发生了变化，更新就会失败。这时候就由程序来决定如何处理这个冲突。例如，它可以重新读取新数据来进行更新，又或者它可以将这一情况直接反馈给用户。


##乐观并发控制

Elasticsearch是分布式的。当文档被创建、更新或者删除时，新版本的文档就会被复制到集群中的其他节点上。Elasticsearch即是同步的又是异步的，也就是说复制的请求被平行发送出去，然后可能会混乱地到达目的地。这就需要一种方法能够保证新的数据不会被旧数据所覆盖。

我们在上文提到每当有**索引**、**put**和**删除**的操作时，无论文档有没有变化，它的**_version**都会增加。Elasticsearch使用**_version**来确保所有的改变操作都被正确排序。如果一个旧的版本出现在新版本之后，它就会被忽略掉。

我们可以利用**_version**的优点来确保我们程序修改的数据冲突不会造成数据丢失。我们可以按照我们的想法来指定**_version**的数字。如果数字错误，请求就是失败。

我们来创建一个新的博文:

    PUT /website/blog/1/_create
    {
      "title": "My first blog entry",
      "text":  "Just trying this out..."
    }
    
反馈告诉我们这是一个新建的文档，它的**_version**是1。假设我们要编辑它，把这个数据加载到网页表单中，修改完毕然后保存新版本。

首先我们先要得到文档：

    GET /website/blog/1
    
返回结果显示**_version**为1：

    {
      "_index" :   "website",
      "_type" :    "blog",
      "_id" :      "1",
      "_version" : 1,
      "found" :    true,
      "_source" :  {
          "title": "My first blog entry",
          "text":  "Just trying this out..."
      }
    }


现在，我们试着重新索引文档以保存变化，我们这样指定了version的数字：

    PUT /website/blog/1?version=1 <1>
    {
      "title": "My first blog entry",
      "text":  "Starting to get the hang of this..."
    }
    
我们只希望当索引中文档的_version是1时，更新才生效。

请求成功相应，返回内容告诉我们_version已经变成了2：

    {
      "_index":   "website",
      "_type":    "blog",
      "_id":      "1",
      "_version": 2
      "created":  false
    }
    
然而，当我们再执行同样的索引请求，并依旧指定**version=1**时，Elasticsearch就会返回一个**409 Conflict**的响应码，返回内容如下：

    {
      "error" : "VersionConflictEngineException[[website][2] [blog][1]:
             version conflict, current [2], provided [1]]",
      "status" : 409
    }
    
这里面指出了文档当前的**_version**数字是2，而我们要求的数字是1。

我们需要做什么取决于我们程序的需求。比如我们可以告知用户已经有其它人修改了这个文档，你应该再保存之前看一下变化。而对于上文提到的库存量问题，我们可能需要重新读取一下最新的文档，然后显示新的数据。

所有的有关于更新或者删除文档的API都支持version这个参数，有了它你就通过修改你的程序来使用乐观并发控制。


##使用外部系统的版本

还有一种常见的情况就是我们还是使用其他的数据库来存储数据，而Elasticsearch只是帮我们检索数据。这也就意味着主数据库只要发生的变更，就需要将其拷贝到Elasticsearch中。如果多个进程同时发生，就会产生上文提到的那些并发问题。

如果你的数据库已经存在了版本号码，或者也可以代表版本的**时间戳**。这是你就可以在Elasticsearch的查询字符串后面添加**version_type=external**来使用这些号码。版本号码必须要是大于零小于**9.2e+18**（Java中long的最大正值）的整数。

Elasticsearch在处理外部版本号时会与对内部版本号的处理有些不同。它不再是检查**_version**是否与制定中的数组相同,而是检查当前的_version是否比指定的数值小。如果请求成功，那么外部的版本号就会被存储到文档中的**_version**中。

外部版本号不仅可以在索引和删除请求时使用，还可以在创建时使用。

例如，创建一篇使用外部版本号为5的博文，我们可以这样操作：

    PUT /website/blog/2?version=5&version_type=external
    {
      "title": "My first external blog entry",
      "text":  "Starting to get the hang of this..."
    }
    
在返回结果中，我们可以发现**_version**是5：

    {
      "_index":   "website",
      "_type":    "blog",
      "_id":      "2",
      "_version": 5,
      "created":  true
    }
    

现在我们更新这个文档，并指定**version**为**10**：

    PUT /website/blog/2?version=10&version_type=external
    {
      "title": "My first external blog entry",
      "text":  "This is a piece of cake..."
    }
    
    
请求被成功执行并且**version**也变成了10：

    {
      "_index":   "website",
      "_type":    "blog",
      "_id":      "2",
      "_version": 10,
      "created":  false
    }
    
    
如果你再次执行这个命令，你会得到之前的错误提示信息，因为你所指定的版本号并没有大于当前Elasticsearch中的版本号。






