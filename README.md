##  ES学习笔记
### 20191022
1、如果用数据库做搜索会怎么样？
* 比方说，每条记录的指定字段的文本，可能会很长，比如说“商品描述”字段的长度，有长达数千个，甚至数万个字符，这个时候，每次都要对每条记录的所有文本进行扫描，懒判断说，你包不包含我指定的这个关键词（比如说“牙膏”）
* 还不能将搜索词拆分开来，尽可能去搜索更多的符合你的期望的结果，比如输入“生化机”，就搜索不出来“生化危机”

![avator](http://i.caigoubao.cc/613363/elasticsearch%E5%9B%BE%E8%A7%A3/%E5%A6%82%E6%9E%9C%E7%94%A8%E6%95%B0%E6%8D%AE%E5%BA%93%E5%81%9A%E6%90%9C%E7%B4%A2%E4%BC%9A%E6%80%8E%E4%B9%88%E6%A0%B7.png)

用数据库来实现搜索，性能会很差。

2、什么是全文检索、倒排索引和Lucene？  
(1)全文检索，倒排索引  
![avator](http://i.caigoubao.cc/613363/elasticsearch%E5%9B%BE%E8%A7%A3/%E4%BB%80%E4%B9%88%E6%98%AF%E5%85%A8%E6%96%87%E6%A3%80%E7%B4%A2.png)  
(2)lucene，就是一个jar包，里面包含了封装好的各种建立倒排索引，以及进行搜索的代码，包括各种算法。我们就用java开发的时候，引入lucene jar，然后基于lucene的api进行去进行开发就可以了。用lucene，我们就可以去将已有的数据建立索引，lucene会在本地磁盘上面，给我们组织索引的数据结构。另外的话，我们也可以用lucene提供的一些功能和api来针对磁盘上额

3、什么是Elasticsearch？  
![avator](http://i.caigoubao.cc/613363/elasticsearch%E5%9B%BE%E8%A7%A3/%E4%BB%80%E4%B9%88%E6%98%AF%E5%85%A8%E6%96%87%E6%A3%80%E7%B4%A2.png)

### 20191024
1、Elasticsearch的功能  
（1）分布式的搜索引擎和数据分析引擎  
搜索：百度，网站的站内搜索，IT系统的检索  
数据分析：电商网站，最近7天牙膏这种商品销量排名前10的商家有哪些；新闻网站，最近1个月访问量排名前3的新闻版块是哪些  
分布式，搜索，数据分析  
（2）全文检索，结构化检索，数据分析  
全文检索：我想搜索商品名称包含牙膏的商品，select * from products where product_name like "%牙膏%"  
结构化检索：我想搜索商品分类为日化用品的商品都有哪些，select * from products where category_id='日化用品'  
部分匹配、自动完成、搜索纠错、搜索推荐  
数据分析：我们分析每一个商品分类下有多少个商品，select category_id,count(*) from products group by category_id  
（3）对海量数据进行近实时的处理  
分布式：ES自动可以将海量数据分散到多台服务器上去存储和检索
海联数据的处理：分布式以后，就可以采用大量的服务器去存储和检索数据，自然而然就可以实现海量数据的处理了
近实时：检索个数据要花费1小时（这就不要近实时，离线批处理，batch-processing）；在秒级别对数据进行搜索和分析  
跟分布式/海量数据相反的：lucene，单机应用，只能在单台服务器上使用，最多只能处理单台服务器可以处理的数据量

----------------------------------------------------------------------------------------------------------------------  
2、Elasticsearch的适用场景  
（1）维基百科，类似百度百科，牙膏，牙膏的维基百科，全文检索，高亮，搜索推荐  
（2）The Guardian（国外新闻网站），类似搜狐新闻，用户行为日志（点击，浏览，收藏，评论）+社交网络数据（对某某新闻的相关看法），数据分析，给到每篇新闻文章的作者，让他知道他的文章的公众反馈（好，坏，热门，垃圾，鄙视，崇拜）  
（3）Stack Overflow（国外的程序异常讨论论坛），IT问题，程序的报错，提交上去，有人会跟你讨论和回答，全文检索，搜索相关问题和答案，程序报错了，就会将报错信息粘贴到里面去，搜索有没有对应的答案  
（4）GitHub（开源代码管理），搜索上千亿行代码  
（5）电商网站，检索商品  
（6）日志数据分析，logstash采集日志，ES进行复杂的数据分析（ELK技术，elasticsearch+logstash+kibana）  
（7）商品价格监控网站，用户设定某商品的价格阈值，当低于该阈值的时候，发送通知消息给用户，比如说订阅牙膏的监控，如果高露洁牙膏的家庭套装低于50块钱，就通知我，我就去买  
（8）BI系统，商业智能，Business Intelligence。比如说有个大型商场集团，BI，分析一下某某区域最近3年的用户消费金额的趋势以及用户群体的组成构成，产出相关的数张报表，**区，最近3年，每年消费金额呈现100%的增长，而且用户群体85%是高级白领，开一个新商场。ES执行数据分析和挖掘，Kibana进行数据可视化  
（9）国内：站内搜索（电商，招聘，门户，等等），IT系统搜索（OA，CRM，ERP，等等），数据分析（ES热门的一个使用场景）

----------------------------------------------------------------------------------------------------------------------

3、Elasticsearch的特点

（1）可以作为一个大型分布式集群（数百台服务器）技术，处理PB级数据，服务大公司；也可以运行在单机上，服务小公司  
（2）Elasticsearch不是什么新技术，主要是将全文检索、数据分析以及分布式技术，合并在了一起，才形成了独一无二的ES；lucene（全文检索），商用的数据分析软件（也是有的），分布式数据库（mycat）  
（3）对用户而言，是开箱即用的，非常简单，作为中小型的应用，直接3分钟部署一下ES，就可以作为生产环境的系统来使用了，数据量不大，操作不是太复杂  
（4）数据库的功能面对很多领域是不够用的（事务，还有各种联机事务型的操作）；特殊的功能，比如全文检索，同义词处理，相关度排名，复杂数据分析，海量数据的近实时处理；Elasticsearch作为传统数据库的一个补充，提供了数据库所不不能提供的很多功能  

4、elasticsearch的核心概念  
（1）Near Realtime（NRT）：近实时，两个意思，从写入数据到数据可以被搜索到有一个小延迟（大概1秒）；基于es执行搜索和分析可以达到秒级  
（2）Cluster：集群，包含多个节点，每个节点属于哪个集群是通过一个配置（集群名称，默认是elasticsearch）来决定的，对于中小型应用来说，刚开始一个集群就一个节点很正常  
（3）Node：节点，集群中的一个节点，节点也有一个名称（默认是随机分配的），节点名称很重要（在执行运维管理操作的时候），默认节点会去加入一个名称为“elasticsearch”的集群，如果直接启动一堆节点，那么它们会自动组成一个elasticsearch集群，当然一个节点也可以组成一个elasticsearch集群  
（4）Document&field：文档，es中的最小数据单元，一个document可以是一条客户数据，一条商品分类数据，一条订单数据，通常用JSON数据结构表示，每个index下的type中，都可以去存储多个document。一个document里面有多个field，每个field就是一个数据字段。  
product document
```
{
  "product_id": "1",
  "product_name": "高露洁牙膏",
  "product_desc": "高效美白",
  "category_id": "2",
  "category_name": "日化用品"
}  
```
（5）Index：索引，包含一堆有相似结构的文档数据，比如可以有一个客户索引，商品分类索引，订单索引，索引有一个名称。一个index包含很多document，一个index就代表了一类类似的或者相同的document。比如说建立一个product index，商品索引，里面可能就存放了所有的商品数据，所有的商品document。  
（6）Type：类型，每个索引里都可以有一个或多个type，type是index中的一个逻辑数据分类，一个type下的document，都有相同的field，比如博客系统，有一个索引，可以定义用户数据type，博客数据type，评论数据type。  
商品index，里面存放了所有的商品数据，商品document  
但是商品分很多种类，每个种类的document的field可能不太一样，比如说电器商品，可能还包含一些诸如售后时间范围这样的特殊field；生鲜商品，还包含一些诸如生鲜保质期之类的特殊field  
type，日化商品type，电器商品type，生鲜商品type  

日化商品type：product_id，product_name，product_desc，category_id，category_name
电器商品type：product_id，product_name，product_desc，category_id，category_name，service_period
生鲜商品type：product_id，product_name，product_desc，category_id，category_name，eat_period  

每一个type里面，都会包含一堆document  
```
{
  "product_id": "2",  
  "product_name": "长虹电视机",  
  "product_desc": "4k高清",  
  "category_id": "3",  
  "category_name": "电器",  
  "service_period": "1年"  
}
```

```
{
  "product_id": "3",
  "product_name": "基围虾",
  "product_desc": "纯天然，冰岛产",
  "category_id": "4",
  "category_name": "生鲜",
  "eat_period": "7天"
}
```
  
（7）shard：单台机器无法存储大量数据，es可以将一个索引中的数据切分为多个shard，分布在多台服务器上存储。有了shard就可以横向扩展，存储更多数据，让搜索和分析等操作分布到多台服务器上去执行，提升吞吐量和性能。每个shard都是一个lucene index。  
（8）replica：任何一个服务器随时可能故障或宕机，此时shard可能就会丢失，因此可以为每个shard创建多个replica副本。replica可以在shard故障时提供备用服务，保证数据不丢失，多个replica还可以提升搜索操作的吞吐量和性能。primary shard（建立索引时一次设置，不能修改，默认5个），replica shard（随时修改数量，默认1个），默认每个索引10个shard，5个primary shard，5个replica shard，最小的高可用配置，是2台服务器。


![avator](http://i.caigoubao.cc/613363/elasticsearch%E5%9B%BE%E8%A7%A3/shard%E5%92%8Creplica%E7%9A%84%E8%A7%A3%E9%87%8A.png)

----------------------------------------------------------------------------------------------------------------------------------------

1、document数据格式

面向文档的搜索分析引擎

（1）应用系统的数据结构都是面向对象的，复杂的  
（2）对象数据存储到数据库中，只能拆解开来，变为扁平的多张表，每次查询的时候还得还原回对象格式，相当麻烦  
（3）ES是面向文档的，文档中存储的数据结构，与面向对象的数据结构是一样的，基于这种文档数据结构，es可以提供复杂的索引，全文检索，分析聚合等功能  
（4）es的document用json数据格式来表达        
```
public class Employee {

  private String email;
  private String firstName;
  private String lastName;
  private EmployeeInfo info;
  private Date joinDate;

}

private class EmployeeInfo {
  
  private String bio; // 性格
  private Integer age;
  private String[] interests; // 兴趣爱好

}

EmployeeInfo info = new EmployeeInfo();
info.setBio("curious and modest");
info.setAge(30);
info.setInterests(new String[]{"bike", "climb"});

Employee employee = new Employee();
employee.setEmail("zhangsan@sina.com");
employee.setFirstName("san");
employee.setLastName("zhang");
employee.setInfo(info);
employee.setJoinDate(new Date());
```

employee对象：里面包含了Employee类自己的属性，还有一个EmployeeInfo对象

两张表：employee表，employee_info表，将employee对象的数据重新拆开来，变成Employee数据和EmployeeInfo数据
employee表：email，first_name，last_name，join_date，4个字段
employee_info表：bio，age，interests，3个字段；此外还有一个外键字段，比如employee_id，关联着employee表
```
{
    "email":      "zhangsan@sina.com",
    "first_name": "san",
    "last_name": "zhang",
    "info": {
        "bio":         "curious and modest",
        "age":         30,
        "interests": [ "bike", "climb" ]
    },
    "join_date": "2017/01/01"
}
```
这就是es的document数据格式和数据库的关系型数据格式的区别

----------------------------------------------------------------------------------------------------------------------------
3、简单的集群管理  
（1）快速检查集群的健康状况  
es提供了一套api，叫做cat api，可以查看es中各种各样的数据  
GET /_cat/health?v  
```
epoch      timestamp cluster       status node.total node.data shards pri relo init unassign pending_tasks max_task_wait_time active_shards_percent
1488006741 15:12:21  elasticsearch yellow          1         1      1   1    0    0        1             0                  -                 50.0%
```
如何快速了解集群的健康状况？green、yellow、red？  
green：每个索引的primary shard和replica shard都是active状态的  
yellow：每个索引的primary shard都是active状态的，但是部分replica shard不是active状态，处于不可用的状态  
red：不是所有索引的primary shard都是active状态的，部分索引有数据丢失了  
为什么现在会处于一个yellow状态？

我们现在就一个笔记本电脑，就启动了一个es进程，相当于就只有一个node。现在es中有一个index，就是kibana自己内置建立的index。由于默认的配置是给每个index分配5个primary shard和5个replica shard，而且primary shard和replica shard不能在同一台机器上（为了容错）。现在kibana自己建立的index是1个primary shard和1个replica shard。当前就一个node，所以只有1个primary shard被分配了和启动了，但是一个replica shard没有第二台机器去启动。  

做一个小实验：此时只要启动第二个es进程，就会在es集群中有2个node，然后那1个replica shard就会自动分配过去，然后cluster status就会变成green状态。  
（2）快速查看集群中有哪些索引  
GET /_cat/indices?v  
```
health status index   uuid                   pri rep docs.count docs.deleted store.size pri.store.size
yellow open   .kibana rUm9n9wMRQCCrRDEhqneBg   1   1          1            0      3.1kb          3.1kb
```
（3）简单的索引操作  
创建索引：PUT /test_index?pretty  
```
health status index      uuid                   pri rep docs.count docs.deleted store.size pri.store.size
yellow open   test_index XmS9DTAtSkSZSwWhhGEKkQ   5   1          0            0       650b           650b
yellow open   .kibana    rUm9n9wMRQCCrRDEhqneBg   1   1          1            0      3.1kb          3.1kb
```
删除索引：DELETE /test_index?pretty  
```
health status index   uuid                   pri rep docs.count docs.deleted store.size pri.store.size
yellow open   .kibana rUm9n9wMRQCCrRDEhqneBg   1   1          1            0      3.1kb          3.1kb
```
----------------------------------------------------------------------------------------------------------------------------

4、商品的CRUD操作

（1）新增商品：新增文档，建立索引
```
PUT /index/type/id
{
  "json数据"
}

PUT /ecommerce/product/1
{
    "name" : "gaolujie yagao",
    "desc" :  "gaoxiao meibai",
    "price" :  30,
    "producer" :      "gaolujie producer",
    "tags": [ "meibai", "fangzhu" ]
}

{
  "_index": "ecommerce",
  "_type": "product",
  "_id": "1",
  "_version": 1,
  "result": "created",
  "_shards": {
    "total": 2,
    "successful": 1,
    "failed": 0
  },
  "created": true
}

PUT /ecommerce/product/2
{
    "name" : "jiajieshi yagao",
    "desc" :  "youxiao fangzhu",
    "price" :  25,
    "producer" :      "jiajieshi producer",
    "tags": [ "fangzhu" ]
}

PUT /ecommerce/product/3
{
    "name" : "zhonghua yagao",
    "desc" :  "caoben zhiwu",
    "price" :  40,
    "producer" :      "zhonghua producer",
    "tags": [ "qingxin" ]
}
```
es会自动建立index和type，不需要提前创建，而且es默认会对document每个field都建立倒排索引，让其可以被搜索

（2）查询商品：检索文档
```
GET /index/type/id
GET /ecommerce/product/1

{
  "_index": "ecommerce",
  "_type": "product",
  "_id": "1",
  "_version": 1,
  "found": true,
  "_source": {
    "name": "gaolujie yagao",
    "desc": "gaoxiao meibai",
    "price": 30,
    "producer": "gaolujie producer",
    "tags": [
      "meibai",
      "fangzhu"
    ]
  }
}
```
（3）修改商品：替换文档
```
PUT /ecommerce/product/1
{
    "name" : "jiaqiangban gaolujie yagao",
    "desc" :  "gaoxiao meibai",
    "price" :  30,
    "producer" :      "gaolujie producer",
    "tags": [ "meibai", "fangzhu" ]
}

{
  "_index": "ecommerce",
  "_type": "product",
  "_id": "1",
  "_version": 1,
  "result": "created",
  "_shards": {
    "total": 2,
    "successful": 1,
    "failed": 0
  },
  "created": true
}

{
  "_index": "ecommerce",
  "_type": "product",
  "_id": "1",
  "_version": 2,
  "result": "updated",
  "_shards": {
    "total": 2,
    "successful": 1,
    "failed": 0
  },
  "created": false
}


PUT /ecommerce/product/1
{
    "name" : "jiaqiangban gaolujie yagao"
}

替换方式有一个不好，即使必须带上所有的field，才能去进行信息的修改
```
（4）修改商品：更新文档
```
POST /ecommerce/product/1/_update
{
  "doc": {
    "name": "jiaqiangban gaolujie yagao"
  }
}

{
  "_index": "ecommerce",
  "_type": "product",
  "_id": "1",
  "_version": 8,
  "result": "updated",
  "_shards": {
    "total": 2,
    "successful": 1,
    "failed": 0
  }
}
```

（5）删除商品：删除文档
```
DELETE /ecommerce/product/1

{
  "found": true,
  "_index": "ecommerce",
  "_type": "product",
  "_id": "1",
  "_version": 9,
  "result": "deleted",
  "_shards": {
    "total": 2,
    "successful": 1,
    "failed": 0
  }
}

{
  "_index": "ecommerce",
  "_type": "product",
  "_id": "1",
  "found": false
}
```
### 20191028
1、query string search  
搜索全部商品：GET /ecommerce/product/_search  
took：耗费了几毫秒  
timed_out：是否超时，这里是没有  
_shards：数据拆成了5个分片，所以对于搜索请求，会打到所有的primary shard（或者是它的某个replica shard也可以）  
hits.total：查询结果的数量，3个document  
hits.max_score：score的含义，就是document对于一个search的相关度的匹配分数，越相关，就越匹配，分数也高  
hits.hits：包含了匹配搜索的document的详细数据  
```
{
  "took" : 6,
  "timed_out" : false,
  "_shards" : {
    "total" : 5,
    "successful" : 5,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : 3,
    "max_score" : 1.0,
    "hits" : [
      {
        "_index" : "ecommerce",
        "_type" : "product",
        "_id" : "2",
        "_score" : 1.0,
        "_source" : {
          "name" : "jiajieshi yagao",
          "desc" : "youxiao fangzhu",
          "price" : 25,
          "producer" : "jiajieshi producer",
          "tags" : [
            "fangzhu"
          ]
        }
      },
      {
        "_index" : "ecommerce",
        "_type" : "product",
        "_id" : "1",
        "_score" : 1.0,
        "_source" : {
          "name" : "jiaqiangban gaolujie yagao",
          "desc" : "gaoxiao meibai",
          "price" : 30,
          "producer" : "gaolujie producer",
          "tags" : [
            "meibai",
            "fangzhu"
          ]
        }
      },
      {
        "_index" : "ecommerce",
        "_type" : "product",
        "_id" : "3",
        "_score" : 1.0,
        "_source" : {
          "name" : "zhonghua yagao",
          "desc" : "caoben zhiwu",
          "price" : 40,
          "producer" : "zhonghua producer",
          "tags" : [
            "qingxin"
          ]
        }
      }
    ]
  }
}
```
query string search的由来，因为search参数都是以http请求的query string来附带的  
搜索商品名称中包含yagao的商品，而且按照售价降序排序：GET /ecommerce/product/_search?q=name:yagao&sort=price:desc  
适用于临时的在命令行使用一些工具，比如curl，快速的发出请求，来检索想要的信息；但是如果查询请求很复杂，是很难去构建的  
在生产环境中，几乎很少使用query string search  
2、query DSL  
DSL：Domain Specified Language，特定领域的语言  
http request body：请求体，可以用json的格式来构建查询语法，比较方便，可以构建各种复杂的语法  
查询所有的商品
```
GET /ecommerce/product/_search
{
  "query": { "match_all": {} }
}
```
查询名称包含yagao的商品，同时按照价格降序排序
```
GET /ecommerce/product/_search
{
    "query" : {
        "match" : {
            "name" : "yagao"
        }
    },
    "sort": [
        { "price": "desc" }
    ]
}
```
分页查询商品，总共3条商品，假设每页就显示1条商品，现在显示第2页，所以就查出来第2个商品
```
GET /ecommerce/product/_search
{
  "query": { "match_all": {} },
  "from": 1,
  "size": 1
}
```
指定要查询出来商品的名称和价格就可以
```
GET /ecommerce/product/_search
{
  "query": { "match_all": {} },
  "_source": ["name", "price"]
}
```
3、query filter  
搜索商品名称包含yagao，而且售价大于25元的商品  
```
GET /ecommerce/product/_search
{
    "query" : {
        "bool" : {
            "must" : {
                "match" : {
                    "name" : "yagao" 
                }
            },
            "filter" : {
                "range" : {
                    "price" : { "gt" : 25 } 
                }
            }
        }
    }
}
```
4、full-text search（全文检索）
```
GET /ecommerce/product/_search
{
    "query" : {
        "match" : {
            "producer" : "yagao producer"
        }
    }
}
```
producer这个字段，会先被拆解，建立倒排索引  
special	4  
yagao		4  
producer	1,2,3,4  
gaolujie	1  
zhognhua	3  
jiajieshi	2  
yagao producer ---> yagao和producer  
所以id为4的document数据的相关匹配度最高   如下
```
{
  "took" : 9,
  "timed_out" : false,
  "_shards" : {
    "total" : 5,
    "successful" : 5,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : 4,
    "max_score" : 0.8092568,
    "hits" : [
      {
        "_index" : "ecommerce",
        "_type" : "product",
        "_id" : "4",
        "_score" : 0.8092568,
        "_source" : {
          "name" : "special yagao",
          "desc" : "special yagao",
          "price" : 50,
          "producer" : "special yagao producer",
          "tags" : [
            "meibai"
          ]
        }
      },
      {
        "_index" : "ecommerce",
        "_type" : "product",
        "_id" : "1",
        "_score" : 0.2876821,
        "_source" : {
          "name" : "jiaqiangban gaolujie yagao",
          "desc" : "gaoxiao meibai",
          "price" : 30,
          "producer" : "gaolujie producer",
          "tags" : [
            "meibai",
            "fangzhu"
          ]
        }
      },
      {
        "_index" : "ecommerce",
        "_type" : "product",
        "_id" : "3",
        "_score" : 0.2876821,
        "_source" : {
          "name" : "zhonghua yagao",
          "desc" : "caoben zhiwu",
          "price" : 40,
          "producer" : "zhonghua producer",
          "tags" : [
            "qingxin"
          ]
        }
      },
      {
        "_index" : "ecommerce",
        "_type" : "product",
        "_id" : "2",
        "_score" : 0.19856805,
        "_source" : {
          "name" : "jiajieshi yagao",
          "desc" : "youxiao fangzhu",
          "price" : 25,
          "producer" : "jiajieshi producer",
          "tags" : [
            "fangzhu"
          ]
        }
      }
    ]
  }
}

```
5、phrase search（短语搜索）  
跟全文检索相对应，相反，全文检索会将输入的搜索串拆解开来，去倒排索引里面去一一匹配，只要能匹配上任意一个拆解后的单词，就可以作为结果返回  
phrase search，要求输入的搜索串，必须在指定的字段文本中，完全包含一模一样的，才可以算匹配，才能作为结果返回  
```
{
  "took" : 16,
  "timed_out" : false,
  "_shards" : {
    "total" : 5,
    "successful" : 5,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : 1,
    "max_score" : 0.8092568,
    "hits" : [
      {
        "_index" : "ecommerce",
        "_type" : "product",
        "_id" : "4",
        "_score" : 0.8092568,
        "_source" : {
          "name" : "special yagao",
          "desc" : "special yagao",
          "price" : 50,
          "producer" : "special yagao producer",
          "tags" : [
            "meibai"
          ]
        }
      }
    ]
  }
}

```
6、highlight search（高亮搜索结果）
```
GET /ecommerce/product/_search
{
    "query" : {
        "match" : {
            "producer" : "producer"
        }
    },
    "highlight": {
        "fields" : {
            "producer" : {}
        }
    }
}
```
第一个分析需求：计算每个tag下的商品数量
```
GET /ecommerce/product/_search
{
  "size":0,
  "aggs": {
    "group_by_tags": {
      "terms": { "field": "tags" }
    }
  }
}
```
```
{
  "took" : 3,
  "timed_out" : false,
  "_shards" : {
    "total" : 5,
    "successful" : 5,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : 4,
    "max_score" : 0.0,
    "hits" : [ ]
  },
  "aggregations" : {
    "group_by_tags" : {
      "doc_count_error_upper_bound" : 0,
      "sum_other_doc_count" : 0,
      "buckets" : [
        {
          "key" : "fangzhu",
          "doc_count" : 2
        },
        {
          "key" : "meibai",
          "doc_count" : 2
        },
        {
          "key" : "qingxin",
          "doc_count" : 1
        }
      ]
    }
  }
}

```
第二个聚合分析的需求：对名称中包含yagao的商品，计算每个tag下的商品数量
```
GET /ecommerce/product/_search
{
  "size": 0,
  "query": {
    "match": {
      "name": "yagao"
    }
  },
  "aggs": {
    "all_tags": {
      "terms": {
        "field": "tags"
      }
    }
  }
}
```

第三个聚合分析的需求：先分组，再算每组的平均值，计算每个tag下的商品的平均价格
```
GET /ecommerce/product/_search
{
    "size": 0,
    "aggs" : {
        "group_by_tags" : {
            "terms" : { "field" : "tags" },
            "aggs" : {
                "avg_price" : {
                    "avg" : { "field" : "price" }
                }
            }
        }
    }
}
```
```
{
  "took" : 31,
  "timed_out" : false,
  "_shards" : {
    "total" : 5,
    "successful" : 5,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : 4,
    "max_score" : 0.0,
    "hits" : [ ]
  },
  "aggregations" : {
    "group_by_tags" : {
      "doc_count_error_upper_bound" : 0,
      "sum_other_doc_count" : 0,
      "buckets" : [
        {
          "key" : "fangzhu",
          "doc_count" : 2,
          "avg_price" : {
            "value" : 27.5
          }
        },
        {
          "key" : "meibai",
          "doc_count" : 2,
          "avg_price" : {
            "value" : 40.0
          }
        },
        {
          "key" : "qingxin",
          "doc_count" : 1,
          "avg_price" : {
            "value" : 40.0
          }
        }
      ]
    }
  }
}

```
第四个数据分析需求：计算每个tag下的商品的平均价格，并且按照平均价格降序排序
```
GET /ecommerce/product/_search
{
    "size": 0,
    "aggs" : {
        "all_tags" : {
            "terms" : { "field" : "tags", "order": { "avg_price": "desc" } },
            "aggs" : {
                "avg_price" : {
                    "avg" : { "field" : "price" }
                }
            }
        }
    }
}
```
第五个数据分析需求：按照指定的价格范围区间进行分组，然后在每组内再按照tag进行分组，最后再计算每组的平均价格
```
GET /ecommerce/product/_search
{
  "size": 0,
  "aggs": {
    "group_by_price": {
      "range": {
        "field": "price",
        "ranges": [
          {
            "from": 0,
            "to": 20
          },
          {
            "from": 20,
            "to": 40
          },
          {
            "from": 40,
            "to": 50
          }
        ]
      },
      "aggs": {
        "group_by_tags": {
          "terms": {
            "field": "tags"
          },
          "aggs": {
            "average_price": {
              "avg": {
                "field": "price"
              }
            }
          }
        }
      }
    }
  }
}
```
### 20191029
![avator](http://i.caigoubao.cc/613363/elasticsearch%E5%9B%BE%E8%A7%A3/ES%E7%9A%84%E5%9F%BA%E7%A1%80%E5%88%86%E5%B8%83%E5%BC%8F%E6%9E%B6%E6%9E%84.png)
1、Elasticsearch对复杂分布式机制的透明隐藏特性  
Elasticsearch是一套分布式的系统，分布式是为了应对大数据量隐藏了复杂的分布式机制  
分片机制（我们之前随随便便就将一些document插入到es集群中去了，我们有没有care过数据怎么进行分片的，数据到哪个shard中去）  
cluster discovery（集群发现机制，我们之前在做那个集群status从yellow转green的实验里，直接启动了第二个es进程，那个进程作为一个node自动就发现了集群，并且加入了进去，还接受了部分数据，replica shard）    
shard负载均衡（举例，假设现在有3个节点，总共有25个shard要分配到3个节点上去，es会自动进行均匀分配，以保持每个节点的均衡的读写负载请求）  
shard副本，请求路由，集群扩容，shard重分配  

--------------------------------------------------------------------------------------------------------------------

2、Elasticsearch的垂直扩容与水平扩容

垂直扩容：采购更强大的服务器，成本非常高昂，而且会有瓶颈，假设世界上最强大的服务器容量就是10T，但是当你的总数据量达到5000T的时候，你要采购多少台最强大的服务器啊

水平扩容：业界经常采用的方案，采购越来越多的普通服务器，性能比较一般，但是很多普通服务器组织在一起，就能构成强大的计算和存储能力

扩容对应用程序的透明性

--------------------------------------------------------------------------------------------------------------------

3、增减或减少节点时的数据rebalance

保持负载均衡

--------------------------------------------------------------------------------------------------------------------

4、master节点

（1）创建或删除索引  
（2）增加或删除节点

--------------------------------------------------------------------------------------------------------------------

5、节点平等的分布式架构 

（1）节点对等，每个节点都能接收所有的请求  
（2）自动请求路由  
（3）响应收集  

--------------------------------------------------------------------------------------------------------------------
6、shard&replica机制 
![avator](http://i.caigoubao.cc/613363/elasticsearch%E5%9B%BE%E8%A7%A3/shard-replica%E6%9C%BA%E5%88%B6%E5%86%8D%E6%AC%A1%E6%A2%B3%E7%90%86.png)
（1）index包含多个shard  
（2）每个shard都是一个最小工作单元，承载部分数据，lucene实例，完整的建立索引和处理请求的能力  
（3）增减节点时，shard会自动在nodes中负载均衡  
（4）primary shard和replica shard，每个document肯定只存在于某一个primary shard以及其对应的replica shard中，不可能存在于多个primary shard  
（5）replica shard是primary shard的副本，负责容错，以及承担读请求负载  
（6）primary shard的数量在创建索引的时候就固定了，replica shard的数量可以随时修改  
（7）primary shard的默认数量是5，replica默认是1，默认有10个shard，5个primary shard，5个replica shard  
（8）primary shard不能和自己的replica shard放在同一个节点上（否则节点宕机，primary shard和副本都丢失，起不到容错的作用），但是可以和其他primary shard的replica shard放在同一个节点上  

------------------------------------------------------------------------------------------------

7、图解单node环境下创建index是什么样子的
![avator](http://i.caigoubao.cc/613363/elasticsearch%E5%9B%BE%E8%A7%A3/%E5%8D%95node%E7%8E%AF%E5%A2%83%E4%B8%8B%E5%88%9B%E5%BB%BAindex.png)
（1）单node环境下，创建一个index，有3个primary shard，3个replica shard  
（2）集群status是yellow  
（3）这个时候，只会将3个primary shard分配到仅有的一个node上去，另外3个replica shard是无法分配的  
（4）集群可以正常工作，但是一旦出现节点宕机，数据全部丢失，而且集群不可用，无法承接任何请求  
```
PUT /test_index
{
   "settings" : {
      "number_of_shards" : 3,
      "number_of_replicas" : 1
   }
}
```
### 20191101
1、图解横向扩容过程，如何超出扩容极限，以及如何提升容错性

（1）primary&replica自动负载均衡，6个shard，3 primary，3 replica  
（2）每个node有更少的shard，IO/CPU/Memory资源给每个shard分配更多，每个shard性能更好  
（3）扩容的极限，6个shard（3 primary，3 replica），最多扩容到6台机器，每个shard可以占用单台服务器的所有资源，性能最好  
（4）超出扩容极限，动态修改replica数量，9个shard（3primary，6 replica），扩容到9台机器，比3台机器时，拥有3倍的读吞吐量  
（5）3台机器下，9个shard（3 primary，6 replica），资源更少，但是容错性更好，最多容纳2台机器宕机，6个shard只能容纳1台机器宕机  
（6）这里的这些知识点，你综合起来看，就是说，一方面告诉你扩容的原理，怎么扩容，怎么提升系统整体吞吐量；另一方面要考虑到系统的容错性，怎么保证提高容错性，让尽可能多的服务器宕机，保证数据不丢失  
![avator](http://i.caigoubao.cc/613363/elasticsearch%E5%9B%BE%E8%A7%A3/%E6%89%A9%E5%AE%B9%E8%BF%87%E7%A8%8B%E5%88%86%E6%9E%90.png)
2.图解Elasticsearch容错机制：master选举，replica容错，数据恢复

（1）9 shard，3 node  
（2）master node宕机，自动master选举，red  
（3）replica容错：新master将replica提升为primary shard，yellow  
（4）重启宕机node，master copy replica到该node，使用原有的shard并同步宕机后的修改，green 
![avator](http://i.caigoubao.cc/613363/elasticsearch%E5%9B%BE%E8%A7%A3/es%E5%AE%B9%E9%94%99%E8%BF%87%E7%A8%8B%E5%88%86%E6%9E%90.png) 
3._index元数据  _type元数据_id元数据
```
{
  "_index": "test_index",
  "_type": "test_type",
  "_id": "1",
  "_version": 1,
  "found": true,
  "_source": {
    "test_content": "test test"
  }
}
```
------------------------------------------------------------------------------------------------------------------------------------------

1、_index元数据

（1）代表一个document存放在哪个index中  
（2）类似的数据放在一个索引，非类似的数据放不同索引：product index（包含了所有的商品），sales index（包含了所有的商品销售数据），inventory index（包含了所有库存相关的数据）。如果你把比如product，sales，human resource（employee），全都放在一个大的index里面，比如说company index，不合适的。  
（3）index中包含了很多类似的document：类似是什么意思，其实指的就是说，这些document的fields很大一部分是相同的，你说你放了3个document，每个document的fields都完全不一样，这就不是类似了，就不太适合放到一个index里面去了。  
（4）索引名称必须是小写的，不能用下划线开头，不能包含逗号：product，website，blog  

2、_type元数据

（1）代表document属于index中的哪个类别（type）  
（2）一个索引通常会划分为多个type，逻辑上对index中有些许不同的几类数据进行分类：因为一批相同的数据，可能有很多相同的fields，但是还是可能会有一些轻微的不同，可能会有少数fields是不一样的，举个例子，就比如说，商品，可能划分为电子商品，生鲜商品，日化商品，等等。  
（3）type名称可以是大写或者小写，但是同时不能用下划线开头，不能包含逗号  

3、_id元数据

（1）代表document的唯一标识，与index和type一起，可以唯一标识和定位一个document     
（2）我们可以手动指定document的id（put /index/type/id），也可以不指定，由es自动为我们创建一个id

![avator](http://i.caigoubao.cc/613363/elasticsearch%E5%9B%BE%E8%A7%A3/index%E5%A6%82%E4%BD%95%E5%88%9B%E5%BB%BA%E7%9A%84%E5%8F%8D%E4%BE%8B%E5%88%86%E6%9E%90.png)

4、手动指定document id

（1）根据应用情况来说，是否满足手动指定document id的前提：

一般来说，是从某些其他的系统中，导入一些数据到es时，会采取这种方式，就是使用系统中已有数据的唯一标识，作为es中document的id。举个例子，比如说，我们现在在开发一个电商网站，做搜索功能，或者是OA系统，做员工检索功能。这个时候，数据首先会在网站系统或者IT系统内部的数据库中，会先有一份，此时就肯定会有一个数据库的primary key（自增长，UUID，或者是业务编号）。如果将数据导入到es中，此时就比较适合采用数据在数据库中已有的primary key。  

如果说，我们是在做一个系统，这个系统主要的数据存储就是es一种，也就是说，数据产生出来以后，可能就没有id，直接就放es一个存储，那么这个时候，可能就不太适合说手动指定document id的形式了，因为你也不知道id应该是什么，此时可以采取下面要讲解的让es自动生成id的方式。

（2）put /index/type/id
```
PUT /test_index/test_type/2
{
  "test_content": "my test"
}
```

5、自动生成document id

（1）post /index/type
```
POST /test_index/test_type
{
  "test_content": "my test"
}

{
  "_index": "test_index",
  "_type": "test_type",
  "_id": "AVp4RN0bhjxldOOnBxaE",
  "_version": 1,
  "result": "created",
  "_shards": {
    "total": 2,
    "successful": 1,
    "failed": 0
  },
  "created": true
}
```
（2）自动生成的id，长度为20个字符，URL安全，base64编码，GUID，分布式系统并行生成时不可能会发生冲突
![avator](http://i.caigoubao.cc/613363/elasticsearch%E5%9B%BE%E8%A7%A3/GUID%E4%B8%8D%E5%86%B2%E7%AA%81%E8%A7%A3%E9%87%8A.png)
6、_source元数据
```
put /test_index/test_type/1
{
  "test_field1": "test field1",
  "test_field2": "test field2"
}

get /test_index/test_type/1

{
  "_index": "test_index",
  "_type": "test_type",
  "_id": "1",
  "_version": 2,
  "found": true,
  "_source": {
    "test_field1": "test field1",
    "test_field2": "test field2"
  }
}
```
_source元数据：就是说，我们在创建一个document的时候，使用的那个放在request body中的json串，默认情况下，在get的时候，会原封不动的给我们返回回来。

------------------------------------------------------------------------------------------------------------------

7、定制返回结果

定制返回的结果，指定_source中，返回哪些field
```
GET /test_index/test_type/1?_source=test_field1,test_field2

{
  "_index": "test_index",
  "_type": "test_type",
  "_id": "1",
  "_version": 2,
  "found": true,
  "_source": {
    "test_field2": "test field2"
  }
}
```
8、document的全量替换

（1）语法与创建文档是一样的，如果document id不存在，那么就是创建；如果document id已经存在，那么就是全量替换操作，替换document的json串内容  
（2）document是不可变的，如果要修改document的内容，第一种方式就是全量替换，直接对document重新建立索引，替换里面所有的内容  
（3）es会将老的document标记为deleted，然后新增我们给定的一个document，当我们创建越来越多的document的时候，es会在适当的时机在后台自动删除标记为deleted的document  

------------------------------------------------------------------------------------------------------------------------

9、document的强制创建

（1）创建文档与全量替换的语法是一样的，有时我们只是想新建文档，不想替换文档，如果强制进行创建呢？  
（2）PUT /index/type/id?op_type=create，PUT /index/type/id/_create

------------------------------------------------------------------------------------------------------------------------

10、document的删除

（1）DELETE /index/type/id   
（2）不会理解物理删除，只会将其标记为deleted，当数据越来越多的时候，在后台自动删除  
11、elasticsearch并发冲突问题
![avator](http://i.caigoubao.cc/613363/elasticsearch%E5%9B%BE%E8%A7%A3/%E6%B7%B1%E5%BA%A6%E5%9B%BE%E8%A7%A3%E5%89%96%E6%9E%90Elasticsearch%E5%B9%B6%E5%8F%91%E5%86%B2%E7%AA%81%E9%97%AE%E9%A2%98.png )
11-1..悲观锁与乐观锁两种并发控制方案
![avator](http://i.caigoubao.cc/613363/elasticsearch%E5%9B%BE%E8%A7%A3/%E6%B7%B1%E5%BA%A6%E5%9B%BE%E8%A7%A3%E5%89%96%E6%9E%90%E6%82%B2%E8%A7%82%E9%94%81%E4%B8%8E%E4%B9%90%E8%A7%82%E9%94%81%E4%B8%A4%E7%A7%8D%E5%B9%B6%E5%8F%91%E6%8E%A7%E5%88%B6%E6%96%B9%E6%A1%88.png)
12、图解Elasticsearch内部如何基于_version进行乐观锁并发控制
![avator](http://i.caigoubao.cc/613363/elasticsearch%E5%9B%BE%E8%A7%A3/%E5%9B%BE%E8%A7%A3Elasticsearch%E5%86%85%E9%83%A8%E5%A6%82%E4%BD%95%E5%9F%BA%E4%BA%8E_version%E8%BF%9B%E8%A1%8C%E4%B9%90%E8%A7%82%E9%94%81%E5%B9%B6%E5%8F%91%E6%8E%A7%E5%88%B6.png)
（1）_version元数据
```
PUT /test_index/test_type/6
{
  "test_field": "test test"
}

{
  "_index": "test_index",
  "_type": "test_type",
  "_id": "6",
  "_version": 1,
  "result": "created",
  "_shards": {
    "total": 2,
    "successful": 1,
    "failed": 0
  },
  "created": true
}
```
第一次创建一个document的时候，它的_version内部版本号就是1；以后，每次对这个document执行修改或者删除操作，都会对这个_version版本号自动加1；哪怕是删除，也会对这条数据的版本号加1
```
{
  "found": true,
  "_index": "test_index",
  "_type": "test_type",
  "_id": "6",
  "_version": 4,
  "result": "deleted",
  "_shards": {
    "total": 2,
    "successful": 1,
    "failed": 0
  }
}
```
我们会发现，在删除一个document之后，可以从一个侧面证明，它不是立即物理删除掉的，因为它的一些版本号等信息还是保留着的。先删除一条document，再重新创建这条document，其实会在delete version基础之上，再把version号加1  
### 20191104  
1、上机动手实战演练基于_version进行乐观锁并发控制

（1）先构造一条数据出来
```
PUT /test_index/test_type/7
{
  "test_field": "test test"
}
```
（2）模拟两个客户端，都获取到了同一条数据
```
GET test_index/test_type/7

{
  "_index": "test_index",
  "_type": "test_type",
  "_id": "7",
  "_version": 1,
  "found": true,
  "_source": {
    "test_field": "test test"
  }
}
```
（3）其中一个客户端，先更新了一下这个数据

同时带上数据的版本号，确保说，es中的数据的版本号，跟客户端中的数据的版本号是相同的，才能修改
```
PUT /test_index/test_type/7?version=1 
{
  "test_field": "test client 1"
}

{
  "_index": "test_index",
  "_type": "test_type",
  "_id": "7",
  "_version": 2,
  "result": "updated",
  "_shards": {
    "total": 2,
    "successful": 1,
    "failed": 0
  },
  "created": false
}
```
（4）另外一个客户端，尝试基于version=1的数据去进行修改，同样带上version版本号，进行乐观锁的并发控制
```
PUT /test_index/test_type/7?version=1 
{
  "test_field": "test client 2"
}

{
  "error": {
    "root_cause": [
      {
        "type": "version_conflict_engine_exception",
        "reason": "[test_type][7]: version conflict, current version [2] is different than the one provided [1]",
        "index_uuid": "6m0G7yx7R1KECWWGnfH1sw",
        "shard": "3",
        "index": "test_index"
      }
    ],
    "type": "version_conflict_engine_exception",
    "reason": "[test_type][7]: version conflict, current version [2] is different than the one provided [1]",
    "index_uuid": "6m0G7yx7R1KECWWGnfH1sw",
    "shard": "3",
    "index": "test_index"
  },
  "status": 409
}
```
（5）在乐观锁成功阻止并发问题之后，尝试正确的完成更新
```
GET /test_index/test_type/7

{
  "_index": "test_index",
  "_type": "test_type",
  "_id": "7",
  "_version": 2,
  "found": true,
  "_source": {
    "test_field": "test client 1"
  }
}
```
基于最新的数据和版本号，去进行修改，修改后，带上最新的版本号，可能这个步骤会需要反复执行好几次，才能成功，特别是在多线程并发更新同一条数据很频繁的情况下
```
PUT /test_index/test_type/7?version=2 
{
  "test_field": "test client 2"
}

{
  "_index": "test_index",
  "_type": "test_type",
  "_id": "7",
  "_version": 3,
  "result": "updated",
  "_shards": {
    "total": 2,
    "successful": 1,
    "failed": 0
  },
  "created": false
}
```
2、上机动手实战演练基于external version进行乐观锁并发控制

external version

es提供了一个feature，就是说，你可以不用它提供的内部_version版本号来进行并发控制，可以基于你自己维护的一个版本号来进行并发控制。举个列子，假如你的数据在mysql里也有一份，然后你的应用系统本身就维护了一个版本号，无论是什么自己生成的，程序控制的。这个时候，你进行乐观锁并发控制的时候，可能并不是想要用es内部的_version来进行控制，而是用你自己维护的那个version来进行控制。

?version=1  
?version=1&version_type=external

version_type=external，唯一的区别在于，_version，只有当你提供的version与es中的_version一模一样的时候，才可以进行修改，只要不一样，就报错；当version_type=external的时候，只有当你提供的version比es中的_version大的时候，才能完成修改

es，_version=1，?version=1，才能更新成功  
es，_version=1，?version>1&version_type=external，才能成功，比如说?version=2&  version_type=external

（1）先构造一条数据
```
PUT /test_index/test_type/8
{
  "test_field": "test"
}

{
  "_index": "test_index",
  "_type": "test_type",
  "_id": "8",
  "_version": 1,
  "result": "created",
  "_shards": {
    "total": 2,
    "successful": 1,
    "failed": 0
  },
  "created": true
}
```
（2）模拟两个客户端同时查询到这条数据
```
GET /test_index/test_type/8

{
  "_index": "test_index",
  "_type": "test_type",
  "_id": "8",
  "_version": 1,
  "found": true,
  "_source": {
    "test_field": "test"
  }
}
```
（3）第一个客户端先进行修改，此时客户端程序是在自己的数据库中获取到了这条数据的最新版本号，比如说是2
```
PUT /test_index/test_type/8?version=2&version_type=external
{
  "test_field": "test client 1"
}

{
  "_index": "test_index",
  "_type": "test_type",
  "_id": "8",
  "_version": 2,
  "result": "updated",
  "_shards": {
    "total": 2,
    "successful": 1,
    "failed": 0
  },
  "created": false
}
```
（4）模拟第二个客户端，同时拿到了自己数据库中维护的那个版本号，也是2，同时基于version=2发起了修改
```
PUT /test_index/test_type/8?version=2&version_type=external
{
  "test_field": "test client 2"
}

{
  "error": {
    "root_cause": [
      {
        "type": "version_conflict_engine_exception",
        "reason": "[test_type][8]: version conflict, current version [2] is higher or equal to the one provided [2]",
        "index_uuid": "6m0G7yx7R1KECWWGnfH1sw",
        "shard": "1",
        "index": "test_index"
      }
    ],
    "type": "version_conflict_engine_exception",
    "reason": "[test_type][8]: version conflict, current version [2] is higher or equal to the one provided [2]",
    "index_uuid": "6m0G7yx7R1KECWWGnfH1sw",
    "shard": "1",
    "index": "test_index"
  },
  "status": 409
}
```
（5）在并发控制成功后，重新基于最新的版本号发起更新
```
GET /test_index/test_type/8

{
  "_index": "test_index",
  "_type": "test_type",
  "_id": "8",
  "_version": 2,
  "found": true,
  "_source": {
    "test_field": "test client 1"
  }
}

PUT /test_index/test_type/8?version=3&version_type=external
{
  "test_field": "test client 2"
}

{
  "_index": "test_index",
  "_type": "test_type",
  "_id": "8",
  "_version": 3,
  "result": "updated",
  "_shards": {
    "total": 2,
    "successful": 1,
    "failed": 0
  },
  "created": false
}
```
3、什么是partial update？

PUT /index/type/id，创建文档&替换文档，就是一样的语法

一般对应到应用程序中，每次的执行流程基本是这样的：

（1）应用程序先发起一个get请求，获取到document，展示到前台界面，供用户查看和修改  
（2）用户在前台界面修改数据，发送到后台  
（3）后台代码，会将用户修改的数据在内存中进行执行，然后封装好修改后的全量数据  
（4）然后发送PUT请求，到es中，进行全量替换  
（5）es将老的document标记为deleted，然后重新创建一个新的document  
```
partial update

post /index/type/id/_update 
{
   "doc": {
      "要修改的少数几个field即可，不需要全量的数据"
   }
}
```
看起来，好像就比较方便了，每次就传递少数几个发生修改的field即可，不需要将全量的document数据发送过去

4、图解partial update实现原理以及其优点

partial update，看起来很方便的操作，实际内部的原理是什么样子的，然后它的优点是什么

5、上机动手实战演练partial update
```
PUT /test_index/test_type/10
{
  "test_field1": "test1",
  "test_field2": "test2"
}

POST /test_index/test_type/10/_update
{
  "doc": {
    "test_field2": "updated test2"
  }
}
```
![avator](http://i.caigoubao.cc/613363/elasticsearch%E5%9B%BE%E8%A7%A3/%E5%9B%BE%E8%A7%A3partial%20update%E5%AE%9E%E7%8E%B0%E5%8E%9F%E7%90%86%E4%BB%A5%E5%8F%8A%E5%85%B6%E4%BC%98%E7%82%B9.png)

5.es，其实是有个内置的脚本支持的，可以基于groovy脚本实现各种各样的复杂操作
基于groovy脚本，如何执行partial update
es scripting module，我们会在高手进阶篇去讲解，这里就只是初步讲解一下
```
PUT /test_index/test_type/11
{
  "num": 0,
  "tags": []
}
```
（1）内置脚本
```
POST /test_index/test_type/11/_update
{
   "script" : "ctx._source.num+=1"
}

{
  "_index": "test_index",
  "_type": "test_type",
  "_id": "11",
  "_version": 2,
  "found": true,
  "_source": {
    "num": 1,
    "tags": []
  }
}
```
（2）外部脚本
```
ctx._source.tags+=new_tag

POST /test_index/test_type/11/_update
{
  "script": {
    "lang": "groovy", 
    "file": "test-add-tags",
    "params": {
      "new_tag": "tag1"
    }
  }
}
```
（3）用脚本删除文档
```
ctx.op = ctx._source.num == count ? 'delete' : 'none'

POST /test_index/test_type/11/_update
{
  "script": {
    "lang": "groovy",
    "file": "test-delete-document",
    "params": {
      "count": 1
    }
  }
}
```
（4）upsert操作
```
POST /test_index/test_type/11/_update
{
  "doc": {
    "num": 1
  }
}

{
  "error": {
    "root_cause": [
      {
        "type": "document_missing_exception",
        "reason": "[test_type][11]: document missing",
        "index_uuid": "6m0G7yx7R1KECWWGnfH1sw",
        "shard": "4",
        "index": "test_index"
      }
    ],
    "type": "document_missing_exception",
    "reason": "[test_type][11]: document missing",
    "index_uuid": "6m0G7yx7R1KECWWGnfH1sw",
    "shard": "4",
    "index": "test_index"
  },
  "status": 404
}
```
如果指定的document不存在，就执行upsert中的初始化操作；如果指定的document存在，就执行doc或者script指定的partial update操作
```
POST /test_index/test_type/11/_update
{
   "script" : "ctx._source.num+=1",
   "upsert": {
       "num": 0,
       "tags": []
   }
}
```
6.图解partial update内置乐观锁并发控制  
（1）partial update内置乐观锁并发控制  
（2）retry_on_conflict  
（3）_version  
post /index/type/id/_update?retry_on_conflict=5&version=6
![avator](http://i.caigoubao.cc/613363/elasticsearch%E5%9B%BE%E8%A7%A3/partial%20update%E5%86%85%E7%BD%AE%E4%B9%90%E8%A7%82%E9%94%81%E5%B9%B6%E5%8F%91%E6%8E%A7%E5%88%B6.png)
课程大纲

7、批量查询的好处

就是一条一条的查询，比如说要查询100条数据，那么就要发送100次网络请求，这个开销还是很大的
如果进行批量查询的话，查询100条数据，就只要发送1次网络请求，网络请求的性能开销缩减100倍

、mget的语法

（1）一条一条的查询
```
GET /test_index/test_type/1
GET /test_index/test_type/2
```
（2）mget批量查询
```
GET /_mget
{
   "docs" : [
      {
         "_index" : "test_index",
         "_type" :  "test_type",
         "_id" :    1
      },
      {
         "_index" : "test_index",
         "_type" :  "test_type",
         "_id" :    2
      }
   ]
}

{
  "docs": [
    {
      "_index": "test_index",
      "_type": "test_type",
      "_id": "1",
      "_version": 2,
      "found": true,
      "_source": {
        "test_field1": "test field1",
        "test_field2": "test field2"
      }
    },
    {
      "_index": "test_index",
      "_type": "test_type",
      "_id": "2",
      "_version": 1,
      "found": true,
      "_source": {
        "test_content": "my test"
      }
    }
  ]
}
```
（3）如果查询的document是一个index下的不同type种的话
```
GET /test_index/_mget
{
   "docs" : [
      {
         "_type" :  "test_type",
         "_id" :    1
      },
      {
         "_type" :  "test_type",
         "_id" :    2
      }
   ]
}
```
（4）如果查询的数据都在同一个index下的同一个type下，最简单了
```
GET /test_index/test_type/_mget
{
   "ids": [1, 2]
}
```
3、mget的重要性

可以说mget是很重要的，一般来说，在进行查询的时候，如果一次性要查询多条数据的话，那么一定要用batch批量操作的api
尽可能减少网络开销次数，可能可以将性能提升数倍，甚至数十倍，非常非常之重要








