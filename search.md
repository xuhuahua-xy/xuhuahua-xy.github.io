## 项目介绍

百年智是一个综合性的B2B2C的电商平台，网站采用商家入驻模式，商家入驻平台提交申请，平台审核模式

**电商前台**

​		主要包括网站首页，商家首页，商品详细页，搜索页，等

**商家管理系统**

​	入驻商家的管理的后台，主要功能是对商品的管理以及订单统计查询。包括新增商品的时候以oss的方式上传图片。

**运营商管理系统** 

​	是电商平台管理人员的后台，主要功能是 商家审核，品牌管理，规格管理，模板管理等

**后台管理系统**

​	

​	对于我们这管理系统，因为使用的用户，一般是平台的管理任员，或者是平台商家。所以采用我一般都是采用前后端分离的方式，后台系统的前端采用的是 vue+elementui

1.vue因为它的响应式编程和组件化开发。可以给用户展示丰富，复杂式数据。使用户操作起来更加简单。

2.方便快速开发，因为vue是数据双向数据绑定，虚拟化的dom。局部相应更快。

**电商平台**

是一个B2C大型综合性的电商网站。因为VUE不利于SEO优化。所以采用的模板的方式，

讲两点，

​	1.为什么不利于SEO优化，

​		1.vue渲染数据。当比如要在百度搜索页面的时候，会通过爬虫技术采取相应的url，通过dom节点来解析页面数据。vue采用的双向绑定机制，导致爬虫获取的可能不是渲染后的最终页面。所以vue来渲染数据对SEO并不友好

​	 2.vue的页面不是一个页面，而是一个页面应用。当一个HTML 的页面查看源代码的时候一个完整的xml格式的页面，但是vue的页面看到的是个个组件。这样的网站不利于搜索引擎搜索的到。这对于一个电商前台是不能够容忍的。

​	2.为什么采用模板的方式

​	采用模板，更适合搭配nginx 来做动静分离，负载均衡，和页面静态化

​	1.对于我们网站前台的首页，比如css ，js ，img 因为这些文件很大，对后端服务有不小的压力。并且很多项目使用的静态资源大部分相同。所以将的将静态资源 放在ng里。不仅减少后端服务的压力，还可以1对多的形式来渲染页面。

​	 2.负载均衡。ng和tomcat 处理能力并不是一个level。所以更加适合做，tomcat的横向扩容，当618.双11.为了使后端能抗压力。我们采用轮训的方式分发请求。

​	  3.页面静态化，

1.对于业务数据展示相对简单，访问相对频繁的页面，比如商品详情页。当我们第一次访问的时候，通过我们的模板+数据生成一个HtmL的页面，放入我们的ng里。以后在访问的时候，就直接访问这个带数据的页面。就用在访问后端。减轻后端服务的压力。

​	对于电商网站的商品详细页来说，至少几百万个商品，每个商品又有大量的信息，这样的情况同样也适用于使用网页静态化来解决。

网页静态化技术和缓存技术的共同点都是为了减轻数据库的访问压力，但是具体的应用场景不同，缓存比较适合小规模的数据，而网页静态化比较适合大规模且相对变化不太频繁的数据。另外网页静态化还有利于SEO。
另外我们如果将网页以纯静态化的形式展现，就可以使用Nginx这样的高性能的web服务器来部署。Nginx可以承载5万的并发，而Tomcat只有几百。



## 搜索模块

搜索模块上游准备。

1.使用redis 给我们的商品分类做缓存。

​	说到redis做缓存，会有几种关于缓存的问题

​	1.缓存雪崩

​		大部分缓存在同一时间的失效。导致大量请求冲击数据库，有可能导致数据库宕机。

​		缓存雪崩，问题也挺好解决的。当给缓存数据设置生效时间的时候可以使用TTL+randomTTL的这种方式，总是让他们的失效时间不一致就好。

​	2.缓存击穿

​		 缓存击穿就是一个热点key失效。导致大量请求冲击数据库，

​		这种也很好解决，

​		 第一种就是可以使用redis 分布式锁，在去redis 命中缓存前，要检查是否有该缓存。

​		 第二种，使热点key用不失效。这是个很笨的方法。很容易造成数据不一致

​	3.缓存穿透   

​		这种一半情况下就是一个非法数据，在redis 命中不了，就去数据库查，查出来来时空值。导致这种值不能够让在redis里做缓存。

​		1.这种问题最好的方式就是对数据的验证，比如一个订单号如果不是17位，或者不是指定数字开头的就pass了

​		2.给这个非法数据，在redis 缓存一个短ttl的空



讲到了redis 我感觉redis是一个神奇的东西，

不同的数据结构可以做不同的事情

1.string  hash 可以做缓存，hash 甚至可以做分布式配置中心，可以做注册中心。

2.list 可以做消息中间件，把list当做队列模式，一方把消息push 到list，一方监控list是否有值。

​	还可以做向微博，那种的无痕分页，下拉分页

3.sub/pub结构，可以做一个实时的聊天室，还可以做消息推送，比如公众号文章推送。

4.set zset

​	set 特点是无序。且唯一。可以用它来做去重的功能

​	2.zset 

 		我觉着是redis 最特色的数据结构 ，类似java中的sortSet 和HashMap的结合体

​			1.一方面他是一个set 保证了内部value的唯一性。

​			2.另一方面他可以给每一个value 赋一个评分代表着这个value的排序权重，

​			他的内部实现是一种叫着跳跃列表的数据结构

​			设么是跳表

​					是一种链表的改造优化，在链表上建立索引，即每两个结点提取到一个结点到上一级

​	抽出来叫做 1级索引。然后以及索引继续往向提取 ，2级索引， 三级索引。

​	**跳表的特点**

​	1.由许多层级构成

​	2.没一层都是有序链表

​	3.最底层是一个包含所有元素的链表

​	4.一个元素如果出现在上级索引中，那这个元素必然在下级索引和原链表都会出现

​	5.除了原链表每个节点都有两个指针，一个指向同级，一个指向下级

1.查询入口

​	1.导航栏里的分类。

​	2.搜索框的关键字查询



​		进入到search页面在商品展示上面，会有更多条件选项，让用户根据自己的需求，自行选择，贴合用户。使更加贴近用户的方式，精准命中用户想要的产品

会在

我们的多条件选择栏里有

​	分类选择框

​	品牌选择框

​	产品规格选择框

如果从分类选择选项进入的话，

​	1.分类确定，在分类栏展示中只有该分类。

如果是从搜索框入口，因为是关键字查询，会在品牌字段，和分类字段出现过的都会

1.分类

2.品牌

3.规格



ES定义

Elasticsearch是一个开源的高扩展的分布式全文检索引擎，它可以近乎实时的存储、检索数据；本身扩展性很好，可以扩展到上百台服务器

使用Lucene作为其核心来实现所有索引和搜索的功能，但是它的目的是通过简单的RESTful API来隐藏Lucene的复杂性，从而让全文搜索变得简单。



 Lucene 能实现全文搜索主要是因为它实现了倒排索引的查询结构。

倒排索引

当用户发起查询时（假设查询为一个关键词），搜索引擎会扫描索引库中的所有文档，找出所有包含关键词的文档，这样依次从文档中去查找是否含有关键词的方法叫做正向索引

为了增加效率，搜索引擎会把正向索引变为反向索引（倒排索引）即把“文档→单词”的形式变为“单词→文档”的形式

为了创建倒排索引，我们通过分词器将每个文档的内容域拆分成单独的词（我们称它为词条或 Term），创建一个包含所有不重复词条的排序列表，然后列出每个词条出现在哪个文档。

词条（Term）： 索引里面最小的存储和查询单元，对于英文来说是一个单词，对于中文来说一般指分词后的一个词。

词典（Term Dictionary）： 或字典，是词条 Term 的集合。搜索引擎的通常索引单位是单词，单词词典是由文档集合中出现过的所有单词构成的字符串集合，单词词典内每条索引项记载单词本身的一些信息以及指向“倒排列表”的指针。

倒排表（Post list）： 一个文档通常由多个词组成，倒排表记录的是某个词在哪些文档里出现过以及出现的位置。每条记录称为一个倒排项（Posting）。倒排表记录的不单是文档编号，还存储了词频等信息。

倒排文件（Inverted File）： 所有单词的倒排列表往往顺序地存储在磁盘的某个文件里，这个文件被称之为倒排文件，倒排文件是存储倒排索引的物理文件。

词典和倒排表是 Lucene 中很重要的两种数据结构，是实现快速检索的重要基石。词典和倒排文件是分两部分存储的，词典在内存中而倒排文件存储在磁盘上。

基本概念



1. 基本概念
   节点（node）
   	运行了单个实例ES主机称为节点，他是集群的一个成员，可以存储数据，参与集群索引和检索操作，几点通过为期配置的ES集群名称确定其所要加入的集群 
   	候选主节点可以被选举为主节点（Master 节点），集群中只有候选主节点才有选举权和被选举权，其他节点不参与选举的工作。
   	主节点负责创建索引、删除索引、跟踪哪些节点是群集的一部分，并决定哪些分片分配给相关的节点、追踪集群中节点的状态等，稳定的主节点对集群的健康是非常重要的。
   集群
   	ES可以作为一个独立的单个搜索服务器，不过，一般为了处理大型数据集群，实现容错和高可用。可多ES实例进行配合叫做集群
   分片
    	ES的“分片(shard)”机制可将一个索引内部的数据分布地存储于多个节点，它通过将一个索引切分为多个底层物理的Lucene索引完成索引数据的分割存储功能，这每一个物理的Lucene索引称为一个分片(shard)。
           这样的好处是可以把一个大的索引拆分成多个，分布到不同的节点上。降低单服务器的压力，构成分布式搜索，提高整体检索的效率
   副本
           副本是一个分片的精确复制，每个分片可以有零个或多个副本。副本的作用一是提高系统的容错性，当某个节点某个分片损坏或丢失时可以从副本中恢复。二是提高es的查询效率，es会自动对搜索请求进行负载均衡。
   	将数据分片是为了提高可处理数据的容量和易于进行水平扩展，为分片做副本是为了提高集群的稳定性和提高并发量。
   	副本是乘法，越多消耗越大，但也越保险。分片是除法，分片越多，单分片数据就越少也越分散。
   	副本越多，集群的可用性就越高，但是由于每个分片都相当于一个 Lucene 的索引文件，会占用一定的文件句柄、内存及 CPU。并且分片间的数据同步也会占用一定的网络带宽，所以索引的分片数和副本数也不是越多越好
   映射（Mapping）
   	映射是用于定义 ES 对索引中字段的存储类型、分词方式和是否存储等信息，就像数据库中的 Schema ，描述了文档可能具有的字段或属性、每个字段的数据类型。
   	对字段类型根据数据格式自动识别的映射称之为动态映射 ，我们创建索引时具体定义字段类型的映射称之为静态映射或显示映射

2. ES的数据架构
   索引
   	  ES将数据存储于一个或多个索引中，索引是具有类似特性的文档的集合。类比传统的关系型数据库领域来说，索引相当于SQL中的一个数据库。
   类型
   	类型是索引内部的逻辑分区，然而其意义完全取决于用户需求。因此，一个索引内部可定义一个或多个类型(type)。一般来说，类型就是为那些拥有相同的域的文档做的预定义。类比传统的关系型数据库领域来说，类型相当于“表”。



​	

