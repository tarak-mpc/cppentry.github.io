---
layout:     post
title:      Hadoop之Hbase架构以及应用介绍
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p style="color:rgb(63,63,63);font-family:'microsoft yahei';font-size:15px;">
<a href="http://lib.csdn.net/base/hbase" rel="nofollow" class="replace_word" title="Hbase知识库" style="color:rgb(223,52,52);font-weight:bold;"><span></span>Hbase</a>全称为<a href="http://lib.csdn.net/base/hadoop" rel="nofollow" class="replace_word" title="Hadoop知识库" style="color:rgb(223,52,52);font-weight:bold;">Hadoop</a> Database，即<a href="http://lib.csdn.net/base/hbase" rel="nofollow" class="replace_word" title="Hbase知识库" style="color:rgb(223,52,52);font-weight:bold;">hbase</a>是<a href="http://lib.csdn.net/base/hadoop" rel="nofollow" class="replace_word" title="Hadoop知识库" style="color:rgb(223,52,52);font-weight:bold;">hadoop</a>的<a href="http://lib.csdn.net/base/mysql" rel="nofollow" class="replace_word" title="MySQL知识库" style="color:rgb(223,52,52);font-weight:bold;">数据库</a>，是一个分布式的存储系统。Hbase利用Hadoop的HDFS作为其文件存储系统，利用Hadoop的MapReduce来处理Hbase中的海量数据。利用zookeeper作为其协调工具。 <br>
本篇文章将重点介绍Hbase三个方面的内容：Hbase体系结构（<a href="http://lib.csdn.net/base/architecture" rel="nofollow" class="replace_word" title="大型网站架构知识库" style="color:rgb(223,52,52);font-weight:bold;">架构</a>）的介绍、Hbase shell的操作、Hbase的<a href="http://lib.csdn.net/base/java" rel="nofollow" class="replace_word" title="Java 知识库" style="color:rgb(223,52,52);font-weight:bold;">Java </a>api的客户端操作。 <br></p>
<h1>一、Hbase的架构介绍 </h1>
<img src="https://img-blog.csdn.net/20160517203027186" alt="这里写图片描述" title="" style="border:none;"> <br><span></span>Hbase的体系结构是一个主从式的结构，主节点Hmaster在整个集群当中只有一个在运行，从节点HRegionServer有很 多个在运行，主节点Hmaster与从节点HRegionServer实际上指的是不同的物理机器，即有一个机器上面跑的进程是Hmaster，很多机器上面跑的进程是HRegionServer，Hmaster没有单点问题，Hbase集群当中可以启动多个Hmaster，但是通过zookeeper的事件处理机制保证整个集群当中只有一个Hmaster在运行。 <br>
既然Hbase是数据库，那么数据库从根本上来说就是存储表table的，但是我们在这里必须注意一下，Hbase并非是传统的关系型数据库(例如：<a href="http://lib.csdn.net/base/mysql" rel="nofollow" class="replace_word" title="MySQL知识库" style="color:rgb(223,52,52);font-weight:bold;">MySQL</a>、<a href="http://lib.csdn.net/base/oracle" rel="nofollow" class="replace_word" title="Oracle知识库" style="color:rgb(223,52,52);font-weight:bold;">Oracle</a>)，而是非关系型数据库，因为Hbase是一个面向列的分布式存储系统。下面我们有必要介绍一下Hbase中表table的数据模型，先给大家呈现一个hbase数据库的表users： <br><img src="https://img-blog.csdn.net/20160518112608882" alt="这里写图片描述" title="" style="border:none;"> <br><p></p>
<blockquote style="border:none;">
<p style="color:rgb(63,63,63);font-family:'microsoft yahei';font-size:15px;">
特别注意：表中的空白单元并不表示有这个单元存在，在传统的数据库中，空白单元表示该单元存在其值为空(null,这是因为传统数据库总是结构化的)。但在Hbase中，画成二维表只是在逻辑上便于理解，其本质完全是非结构化的。 </p>
</blockquote>
<blockquote style="border:none;">
<p style="color:rgb(63,63,63);font-family:'microsoft yahei';font-size:15px;">
我在下面举例子的过程中用的也是这张users表。 </p>
</blockquote>
<blockquote style="border:none;">
<p style="color:rgb(63,63,63);font-family:'microsoft yahei';font-size:15px;">
上面这个表就是一个典型的hbase table，与传统的关系型数据库具有很大的差别，下面我们详细介绍有关table的相关概念： </p>
</blockquote>
<p style="color:rgb(63,63,63);font-family:'microsoft yahei';font-size:15px;">
RowKey(行健)：table的主键，table中的记录默认按照RowKey升序排序。 <br>
列族(Column Family):即表中的address、info。table在水平方向上有一个或者多个Column Family组成，一个Column Family中可以由任意多个Column(例如address中的province、city、country、town)组成，即列族支持动态扩展，无需预先定义Column的数量以及类型，所有Column均已二进制格式进行存储，用户需要自行进行类型转换。 <br>
TimeStamp(时间戳)：每次用户对数据进行操作对应的时间，可以看做是数据的Version number。例如在上面的表中，xiaoming所对应的company有两个数据信息(alibaba、baidu)，而这两个单元格信息实际上是对应操作时间的，如下图所示： <br><img src="https://img-blog.csdn.net/20160517215121637" alt="这里写图片描述" title="" style="border:none;"> <br><span></span>既然Hbase可以将表中的数据进行分布式存储，那么它到底是以怎样的形式进行分布式存储的呢？我们自然而然想到了HDFS这个分布式文件管理系统是将海量数据切分成若干个block块进行存储的，同理Hbase也采取了类似的存储机制， 将一个table切分成若干个region进行存储，下面我们就介绍Region的相关概念： <br>
当Table随着记录数不断增加而变大后，Table在行的方向上会被切分成多个Region，一个Region由[startkey,endkey) 表示，每个Region会被Master分散到不同的HRegionServer上面进行存储，类似于我的block块会被分散到不同的DataNode节点上面进行存储。下面是Hbase表中的数据与HRegionServer的分布关系，如图所示： <br><img src="https://img-blog.csdn.net/20160518092800255" alt="这里写图片描述" title="" style="border:none;"> <br>
接下来介绍在Hbase的体系结构中，Hmaster、HRegionServer、Zookeeper集群这三个角色的作用： <br><img src="https://img-blog.csdn.net/20160518093628477" alt="这里写图片描述" title="" style="border:none;"> <br>
Hmaster节点的作用： <br>
①不负责存储表数据，负责管理RegionServer的负载均衡（即防止某些RegionServer存储数据量大，有些 <br>
RegionServer存储数据量小），调整RegionServer上面Region的分布 <br>
②管理RegionServer的状态，例如在HRegionServer宕机后，负责失效HRegionServer上Regions的迁移 <br>
③在Region Split后，负责新Region的分配 <br>
HRegionServer节点的作用： <br>
HRegionServer主要负责响应用户的I/O请求，即负责响应用户向表中的读写操作，是Hbase体系结构中最核心的模块。HRegionServer内部存储了很多的HRegion，就像DataNode节点中存储了很多的Block块一样，从上图Hbase完整的体系结构中我们可以看到，HRegion实际上是由很多个HStore组成的，所谓HStore就是表中的一个Column Family，可以看出每个Column Family其实就是一个集中的存储单元，这恰恰也帮助我们理解了为什么Hbase是NoSql系列的数据库，为什么是面向列的数据库，在Hbase的表设计中，我们最好将具备共同I/O特性的Column放在同一个列族中，这样读写才最高效，为了让大家更好的理解HRegionServer、HRegion、HStore、ColumnFamily四者之间的关系，我结合之前提到的users表画了一个四者关系的示意图： <br><img src="https://img-blog.csdn.net/20160518101647715" alt="这里写图片描述" title="" style="border:none;"> <br>
简单来说：就是HRegionServer服务器中存储了很多的HRegion，每个HRegion是由很过个HStore组成的，每个ColumnFamily就是一个HSore。 <br>
在此还要简单介绍一下HLog与MemStore这两个角色的作用： <br>
Hlog：Hlog中存储了用户对表数据的最新的一些操作日志记录。 <br>
MemSore：HRegion会将大量的热数据、访问频次最高的数据存储到MemStore中，这样用户在读写数据的时候不需要从磁盘中进行操作，直接在内存中既可以读取到数据，正因为MemStore这个重要角色的存在，Hbase才能支持随机，高速读取的功能。 <br>
Zookeeper集群的作用： <br>
①通过zk集群的事件处理机制，可以保证集群中只有一个运行的Hmater <br>
②Zookeeper集群中记录了-ROOT-表的位置 <br>
在这里顺便介绍一下Hbase中两张特殊的表：-ROOT-表与.META.表 <br>
-ROOT-表：记录了所有.META.表的元数据信息，-ROOT-表只有一个Region <br>
.META.表：记录了Hbase中所有用户表的HRegion的元数据信息，.META.表可以有多个Region <br>
③Zookeeper集群实时监控着HRegionServer这些服务器的状态，将HRegionServer的上线和下线信息实时通知给Hmaster节点，使得Hmaster节点可以随时感知各个HRegionServer的健康状态。 <br>
在上面我们依次介绍了HMaster、HRegionServer、zookeeper集群的作用，Client使用Hbase的RPC机制与Hmaster与HRegionServer进行通信，对于管理类操作，Client与Hmaster进行RPC进行通信，对于数据(表)的读写类操作，Client与HRegionServer进行通信。注意：在用户对数据表的读写过程中，与Hmaster是没有任何关系的，Hmaster在这一点上不同于我们的NameNode节点，可以看出由于zookeeper集群的存在，Hmaster节点的作用被大大弱化了。 <br>
在Hbase的架构介绍中在介绍最后一点：Hbase的寻址机制，即Hbase在海量的表数据中，是如何找到用户所需要的表数据的呢？——Hbase是通过索引的机制解决了这个问题。 <br><img src="https://img-blog.csdn.net/20160518111937377" alt="这里写图片描述" title="" style="border:none;"> <br>
Client访问用户数据之前需要首先访问zookeeper集群，通过zookeeper集群首先确定-ROOT-表在的位置，然后在通过访问-ROOT- 表确定相应.META.表的位置，最后根据.META.中存储的相应元数据信息找到用户数据的位置去访问。通过 这种索引机制解决了复杂了寻址问题。 <br></p>
<h1>二、Hbase中shell的相关操作 </h1>
前面讲的都是Hbase的理论知识，对于Hbase的操作分为两种方式：shell与<a href="http://lib.csdn.net/base/java" rel="nofollow" class="replace_word" title="Java 知识库" style="color:rgb(223,52,52);font-weight:bold;">java </a>api的方式，既然是对数据库的操作，无非就是增删改查四中操作，下面我列出里几个常用的Hbase Shell操作，如下： <br><img src="https://img-blog.csdn.net/20160518131043928" alt="这里写图片描述" title="" style="border:none;"> <br>
以之前的users表为例，我现在用shell命令进行相应操作： <br>
①创建users表 <br>
create ‘users’, ‘address’,’info’ <br>
②向表中用put进行插入操作 <br>
put ‘users’,’xiaoming’,’address:province’,’zhejiang’ <br>
put ‘users’,’xiaoming’,’address:city’,’hangzhou’ <br>
put ‘users’,’xiaoming’,’address:country’,’china’ <br>
put ‘users’,’xiaoming’,’info:age’,’24’ <br>
put ‘users’,’xiaoming’,’info:age’,’25’ <br>
put ‘users’,’xiaoming’,’info:birthday’,’1987/06/17’ <br>
put ‘users’,’xiaoming’,’info:company’,’alibaba’ <br>
put ‘users’,’xiaoming’,’info:company’,’baidu’ <br>
……………… <br>
③通过get命令获取表中的信息，例如： <br>
1、获取xiaoming的country单元格信息 <br>
get ‘users’,’xiaoming’,’address:country’ <br>
2、获取小明两个版本的年龄信息 <br>
get ‘users’,’xiaoming’,{COLUMN=&gt;’info:age’,VERSIONS=&gt;2} <br>
3、获取zhangyifei全部的信息 <br>
get ‘users’,’zhangyifei’ <br>
4、获取zhangyifei的address列族的全部信息 <br>
get ‘users’,’zhangyifei’,’address’ <br>
5、遍历表中的全部信息 <br>
scan ‘users’ <br>
④用shell命令删除users表 <br>
disable ‘users’ <br>
drop ‘users’ <br>
综上：Hbase的shell命令操作整体来说还是非常简单的，获取信息的方法主要是按照下面的主线进行： <br>
tablename———》rowkey———》Column Family:Colum———-&gt;Version信息——》获取信息！ <br><img src="https://img-blog.csdn.net/20161103172536411" alt="这里写图片描述" title="" style="border:none;"> <br><img src="https://img-blog.csdn.net/20161103172332941" alt="这里写图片描述" title="" style="border:none;"> <br>
注:在<a href="http://lib.csdn.net/base/java" rel="nofollow" class="replace_word" title="Java 知识库" style="color:rgb(223,52,52);font-weight:bold;">Java</a> api操作中,对遍历一条记录或者遍历所有记录所用的思想就是：key为{行健、类族、列名、版本号}，而value为{具体数值}，即一行记录本质上为包含很多键值对。 <br><h1>三、Hbase中Java api的相关操作 </h1>
Hbase中，可以通过shell命令进行增删改查四种操作，但是shell命令用起来比较麻烦，比如说用put一次只能查询一个单元格的信息等等。因此我们在日常工作中总是用Java api的方式对Hbase进行相应操作，在这里还是以上面的users举例，下面是完整的代码操作： <br><img src="https://img-blog.csdn.net/20161103211508118" alt="这里写图片描述" title="" style="border:none;"><p></p>
<pre class="prettyprint" style="font-family:'Source Code Pro', monospace;font-size:14px;line-height:1.45;color:rgb(51,51,51);background-color:rgba(128,128,128,.0470588);border:0px solid rgb(136,136,136);"><code class="hljs avrasm has-numbering" style="display:block;background:transparent;color:inherit;font-family:'Source Code Pro', monospace;">package IT01<span class="hljs-comment" style="color:rgb(136,0,0);">;</span>

import org<span class="hljs-preprocessor" style="color:rgb(68,68,68);">.apache</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.hadoop</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.conf</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.Configuration</span><span class="hljs-comment" style="color:rgb(136,0,0);">;</span>
import org<span class="hljs-preprocessor" style="color:rgb(68,68,68);">.apache</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.hadoop</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.hbase</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.HBaseConfiguration</span><span class="hljs-comment" style="color:rgb(136,0,0);">;</span>
import org<span class="hljs-preprocessor" style="color:rgb(68,68,68);">.apache</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.hadoop</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.hbase</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.HColumnDescriptor</span><span class="hljs-comment" style="color:rgb(136,0,0);">;</span>
import org<span class="hljs-preprocessor" style="color:rgb(68,68,68);">.apache</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.hadoop</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.hbase</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.HTableDescriptor</span><span class="hljs-comment" style="color:rgb(136,0,0);">;</span>
import org<span class="hljs-preprocessor" style="color:rgb(68,68,68);">.apache</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.hadoop</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.hbase</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.KeyValue</span><span class="hljs-comment" style="color:rgb(136,0,0);">;</span>
import org<span class="hljs-preprocessor" style="color:rgb(68,68,68);">.apache</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.hadoop</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.hbase</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.client</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.Get</span><span class="hljs-comment" style="color:rgb(136,0,0);">;</span>
import org<span class="hljs-preprocessor" style="color:rgb(68,68,68);">.apache</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.hadoop</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.hbase</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.client</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.HBaseAdmin</span><span class="hljs-comment" style="color:rgb(136,0,0);">;</span>
import org<span class="hljs-preprocessor" style="color:rgb(68,68,68);">.apache</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.hadoop</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.hbase</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.client</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.HTable</span><span class="hljs-comment" style="color:rgb(136,0,0);">;</span>
import org<span class="hljs-preprocessor" style="color:rgb(68,68,68);">.apache</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.hadoop</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.hbase</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.client</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.Put</span><span class="hljs-comment" style="color:rgb(136,0,0);">;</span>
import org<span class="hljs-preprocessor" style="color:rgb(68,68,68);">.apache</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.hadoop</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.hbase</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.client</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.Result</span><span class="hljs-comment" style="color:rgb(136,0,0);">;</span>
import org<span class="hljs-preprocessor" style="color:rgb(68,68,68);">.apache</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.hadoop</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.hbase</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.client</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.ResultScanner</span><span class="hljs-comment" style="color:rgb(136,0,0);">;</span>
import org<span class="hljs-preprocessor" style="color:rgb(68,68,68);">.apache</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.hadoop</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.hbase</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.client</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.Scan</span><span class="hljs-comment" style="color:rgb(136,0,0);">;</span>

public class HbaseJavaapi
{
       public static String tablename = <span class="hljs-string" style="color:rgb(0,136,0);">"users"</span><span class="hljs-comment" style="color:rgb(136,0,0);">;</span>
       public static void main(String[]  args) throws Exception
       {
             Configuration conf = HBaseConfiguration<span class="hljs-preprocessor" style="color:rgb(68,68,68);">.create</span>()<span class="hljs-comment" style="color:rgb(136,0,0);">;</span>
             <span class="hljs-comment" style="color:rgb(136,0,0);">/**下面这行代码必须有，指定Hbase所用的zookeeper集群信息****/</span>
             <span class="hljs-comment" style="color:rgb(136,0,0);">/*Configuration conf = new Configuration();这样写的话不会自动读取hbase-site.xml文件，所以别用这个*/</span>
             conf<span class="hljs-preprocessor" style="color:rgb(68,68,68);">.set</span>(<span class="hljs-string" style="color:rgb(0,136,0);">"hbase.zookeeper.quorum"</span>,<span class="hljs-string" style="color:rgb(0,136,0);">"hadoop80"</span>)<span class="hljs-comment" style="color:rgb(136,0,0);">;</span>

             <span class="hljs-comment" style="color:rgb(136,0,0);">/*创建表-----用的Java api是HbaseAdmin**/</span>
            @SuppressWarnings(<span class="hljs-string" style="color:rgb(0,136,0);">"resource"</span>)
            HBaseAdmin hBaseAdmin = new HBaseAdmin(conf)<span class="hljs-comment" style="color:rgb(136,0,0);">;</span>
            if(hBaseAdmin<span class="hljs-preprocessor" style="color:rgb(68,68,68);">.tableExists</span>(tablename))
            {
                hBaseAdmin<span class="hljs-preprocessor" style="color:rgb(68,68,68);">.disableTable</span>(tablename)<span class="hljs-comment" style="color:rgb(136,0,0);">;</span>
                hBaseAdmin<span class="hljs-preprocessor" style="color:rgb(68,68,68);">.deleteTable</span>(tablename)<span class="hljs-comment" style="color:rgb(136,0,0);">;</span>
            }
            HTableDescriptor desc = new HTableDescriptor(tablename)<span class="hljs-comment" style="color:rgb(136,0,0);">;//指定创建表的名字users</span>
            HColumnDescriptor family1 = new HColumnDescriptor(<span class="hljs-string" style="color:rgb(0,136,0);">"address"</span>)<span class="hljs-comment" style="color:rgb(136,0,0);">;</span>
            desc<span class="hljs-preprocessor" style="color:rgb(68,68,68);">.addFamily</span>(family1 )<span class="hljs-comment" style="color:rgb(136,0,0);">;</span>
            HColumnDescriptor family2 = new HColumnDescriptor(<span class="hljs-string" style="color:rgb(0,136,0);">"info"</span>)<span class="hljs-comment" style="color:rgb(136,0,0);">;//指定列族的名称</span>
            desc<span class="hljs-preprocessor" style="color:rgb(68,68,68);">.addFamily</span>(family2 )<span class="hljs-comment" style="color:rgb(136,0,0);">;</span>
            hBaseAdmin<span class="hljs-preprocessor" style="color:rgb(68,68,68);">.createTable</span>(desc)<span class="hljs-comment" style="color:rgb(136,0,0);">;</span>

            <span class="hljs-comment" style="color:rgb(136,0,0);">/*****插入记录------用的Java api是HTable****/</span>
            @SuppressWarnings(<span class="hljs-string" style="color:rgb(0,136,0);">"resource"</span>)
            HTable hTable = new HTable(conf,tablename)<span class="hljs-comment" style="color:rgb(136,0,0);">;  //一定要注意:HTable这个客户端只有在表已经存在的时候才有意义！！！！不然抛出异常！</span>
            Put put1 = new Put(<span class="hljs-string" style="color:rgb(0,136,0);">"xiaoming"</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.getBytes</span>())<span class="hljs-comment" style="color:rgb(136,0,0);">;//插入记录时要指定行健</span>
            put1<span class="hljs-preprocessor" style="color:rgb(68,68,68);">.add</span>(<span class="hljs-string" style="color:rgb(0,136,0);">"address"</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.getBytes</span>(), <span class="hljs-string" style="color:rgb(0,136,0);">"province"</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.getBytes</span>(), <span class="hljs-string" style="color:rgb(0,136,0);">"zhejiang"</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.getBytes</span>())<span class="hljs-comment" style="color:rgb(136,0,0);">;</span>
            put1<span class="hljs-preprocessor" style="color:rgb(68,68,68);">.add</span>(<span class="hljs-string" style="color:rgb(0,136,0);">"address"</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.getBytes</span>(), <span class="hljs-string" style="color:rgb(0,136,0);">"city"</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.getBytes</span>(), <span class="hljs-string" style="color:rgb(0,136,0);">"hangzhou"</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.getBytes</span>())<span class="hljs-comment" style="color:rgb(136,0,0);">;</span>
            put1<span class="hljs-preprocessor" style="color:rgb(68,68,68);">.add</span>(<span class="hljs-string" style="color:rgb(0,136,0);">"address"</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.getBytes</span>(), <span class="hljs-string" style="color:rgb(0,136,0);">"country"</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.getBytes</span>(), <span class="hljs-string" style="color:rgb(0,136,0);">"china"</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.getBytes</span>())<span class="hljs-comment" style="color:rgb(136,0,0);">;</span>
            put1<span class="hljs-preprocessor" style="color:rgb(68,68,68);">.add</span>(<span class="hljs-string" style="color:rgb(0,136,0);">"info"</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.getBytes</span>(),<span class="hljs-string" style="color:rgb(0,136,0);">"age"</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.getBytes</span>(),<span class="hljs-string" style="color:rgb(0,136,0);">"24"</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.getBytes</span>())<span class="hljs-comment" style="color:rgb(136,0,0);">;</span>
            put1<span class="hljs-preprocessor" style="color:rgb(68,68,68);">.add</span>(<span class="hljs-string" style="color:rgb(0,136,0);">"info"</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.getBytes</span>(),<span class="hljs-string" style="color:rgb(0,136,0);">"age"</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.getBytes</span>(),<span class="hljs-string" style="color:rgb(0,136,0);">"25"</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.getBytes</span>())<span class="hljs-comment" style="color:rgb(136,0,0);">;</span>
            put1<span class="hljs-preprocessor" style="color:rgb(68,68,68);">.add</span>(<span class="hljs-string" style="color:rgb(0,136,0);">"info"</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.getBytes</span>(),<span class="hljs-string" style="color:rgb(0,136,0);">"birthday"</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.getBytes</span>(),<span class="hljs-string" style="color:rgb(0,136,0);">"1987/06/17"</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.getBytes</span>())<span class="hljs-comment" style="color:rgb(136,0,0);">;</span>
            put1<span class="hljs-preprocessor" style="color:rgb(68,68,68);">.add</span>(<span class="hljs-string" style="color:rgb(0,136,0);">"info"</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.getBytes</span>(),<span class="hljs-string" style="color:rgb(0,136,0);">"company"</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.getBytes</span>(),<span class="hljs-string" style="color:rgb(0,136,0);">"baidu"</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.getBytes</span>())<span class="hljs-comment" style="color:rgb(136,0,0);">;</span>
            put1<span class="hljs-preprocessor" style="color:rgb(68,68,68);">.add</span>(<span class="hljs-string" style="color:rgb(0,136,0);">"info"</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.getBytes</span>(),<span class="hljs-string" style="color:rgb(0,136,0);">"company"</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.getBytes</span>(),<span class="hljs-string" style="color:rgb(0,136,0);">"alibaba"</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.getBytes</span>())<span class="hljs-comment" style="color:rgb(136,0,0);">;</span>
            hTable<span class="hljs-preprocessor" style="color:rgb(68,68,68);">.put</span>(put1 )<span class="hljs-comment" style="color:rgb(136,0,0);">;</span>
            Put put2 = new Put(<span class="hljs-string" style="color:rgb(0,136,0);">"zhangyifei"</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.getBytes</span>())<span class="hljs-comment" style="color:rgb(136,0,0);">;//插入记录时要指定行健</span>
            put2<span class="hljs-preprocessor" style="color:rgb(68,68,68);">.add</span>(<span class="hljs-string" style="color:rgb(0,136,0);">"address"</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.getBytes</span>(), <span class="hljs-string" style="color:rgb(0,136,0);">"province"</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.getBytes</span>(), <span class="hljs-string" style="color:rgb(0,136,0);">"guangdong"</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.getBytes</span>())<span class="hljs-comment" style="color:rgb(136,0,0);">;</span>
            put2<span class="hljs-preprocessor" style="color:rgb(68,68,68);">.add</span>(<span class="hljs-string" style="color:rgb(0,136,0);">"address"</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.getBytes</span>(), <span class="hljs-string" style="color:rgb(0,136,0);">"city"</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.getBytes</span>(), <span class="hljs-string" style="color:rgb(0,136,0);">"shenzhen"</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.getBytes</span>())<span class="hljs-comment" style="color:rgb(136,0,0);">;</span>
            put2<span class="hljs-preprocessor" style="color:rgb(68,68,68);">.add</span>(<span class="hljs-string" style="color:rgb(0,136,0);">"address"</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.getBytes</span>(), <span class="hljs-string" style="color:rgb(0,136,0);">"city"</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.getBytes</span>(), <span class="hljs-string" style="color:rgb(0,136,0);">"nansha"</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.getBytes</span>())<span class="hljs-comment" style="color:rgb(136,0,0);">;</span>
            put2<span class="hljs-preprocessor" style="color:rgb(68,68,68);">.add</span>(<span class="hljs-string" style="color:rgb(0,136,0);">"address"</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.getBytes</span>(), <span class="hljs-string" style="color:rgb(0,136,0);">"country"</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.getBytes</span>(), <span class="hljs-string" style="color:rgb(0,136,0);">"china"</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.getBytes</span>())<span class="hljs-comment" style="color:rgb(136,0,0);">;</span>
            put2<span class="hljs-preprocessor" style="color:rgb(68,68,68);">.add</span>(<span class="hljs-string" style="color:rgb(0,136,0);">"address"</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.getBytes</span>(), <span class="hljs-string" style="color:rgb(0,136,0);">"town"</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.getBytes</span>(), <span class="hljs-string" style="color:rgb(0,136,0);">"xianqiao"</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.getBytes</span>())<span class="hljs-comment" style="color:rgb(136,0,0);">;</span>
            put2<span class="hljs-preprocessor" style="color:rgb(68,68,68);">.add</span>(<span class="hljs-string" style="color:rgb(0,136,0);">"info"</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.getBytes</span>(),<span class="hljs-string" style="color:rgb(0,136,0);">"age"</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.getBytes</span>(),<span class="hljs-string" style="color:rgb(0,136,0);">"45"</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.getBytes</span>())<span class="hljs-comment" style="color:rgb(136,0,0);">;</span>
            put2<span class="hljs-preprocessor" style="color:rgb(68,68,68);">.add</span>(<span class="hljs-string" style="color:rgb(0,136,0);">"info"</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.getBytes</span>(),<span class="hljs-string" style="color:rgb(0,136,0);">"birthday"</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.getBytes</span>(),<span class="hljs-string" style="color:rgb(0,136,0);">"1987/02/12"</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.getBytes</span>())<span class="hljs-comment" style="color:rgb(136,0,0);">;</span>
            put2<span class="hljs-preprocessor" style="color:rgb(68,68,68);">.add</span>(<span class="hljs-string" style="color:rgb(0,136,0);">"info"</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.getBytes</span>(),<span class="hljs-string" style="color:rgb(0,136,0);">"company"</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.getBytes</span>(),<span class="hljs-string" style="color:rgb(0,136,0);">"tengxun"</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.getBytes</span>())<span class="hljs-comment" style="color:rgb(136,0,0);">;</span>
            put2<span class="hljs-preprocessor" style="color:rgb(68,68,68);">.add</span>(<span class="hljs-string" style="color:rgb(0,136,0);">"address"</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.getBytes</span>(), <span class="hljs-string" style="color:rgb(0,136,0);">"favorite"</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.getBytes</span>(), <span class="hljs-string" style="color:rgb(0,136,0);">"movie"</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.getBytes</span>())<span class="hljs-comment" style="color:rgb(136,0,0);">;</span>
            hTable<span class="hljs-preprocessor" style="color:rgb(68,68,68);">.put</span>(put2 )<span class="hljs-comment" style="color:rgb(136,0,0);">;</span>


            <span class="hljs-comment" style="color:rgb(136,0,0);">/****查询记录--------------用的Java api是Htable***/</span>
            <span class="hljs-comment" style="color:rgb(136,0,0);">/**1------&gt;获取小明的country单元格信息****/</span>
            Get get = new Get(<span class="hljs-string" style="color:rgb(0,136,0);">"xiaoming"</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.getBytes</span>())<span class="hljs-comment" style="color:rgb(136,0,0);">;</span>
            Result result = hTable<span class="hljs-preprocessor" style="color:rgb(68,68,68);">.get</span>(get)<span class="hljs-comment" style="color:rgb(136,0,0);">;</span>
            byte[] value = result<span class="hljs-preprocessor" style="color:rgb(68,68,68);">.getValue</span>(<span class="hljs-string" style="color:rgb(0,136,0);">"address"</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.getBytes</span>(), <span class="hljs-string" style="color:rgb(0,136,0);">"country"</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.getBytes</span>())<span class="hljs-comment" style="color:rgb(136,0,0);">;</span>
            System<span class="hljs-preprocessor" style="color:rgb(68,68,68);">.out</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.println</span>(new String(value))<span class="hljs-comment" style="color:rgb(136,0,0);">;</span>

            System<span class="hljs-preprocessor" style="color:rgb(68,68,68);">.out</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.println</span>(<span class="hljs-string" style="color:rgb(0,136,0);">"**************************"</span>)<span class="hljs-comment" style="color:rgb(136,0,0);">;</span>

            <span class="hljs-comment" style="color:rgb(136,0,0);">/**2------&gt;获取zhangyifei的一行的记录****/</span>
            Get get2 = new Get(<span class="hljs-string" style="color:rgb(0,136,0);">"zhangyifei"</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.getBytes</span>())<span class="hljs-comment" style="color:rgb(136,0,0);">;</span>
            Result result2 = hTable<span class="hljs-preprocessor" style="color:rgb(68,68,68);">.get</span>(get2)<span class="hljs-comment" style="color:rgb(136,0,0);">;</span>
            for(KeyValue key  : result2<span class="hljs-preprocessor" style="color:rgb(68,68,68);">.list</span>())//一行的记录当中包含很多的键值对信息，依次遍历即可
            {
                 System<span class="hljs-preprocessor" style="color:rgb(68,68,68);">.out</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.println</span>(key<span class="hljs-preprocessor" style="color:rgb(68,68,68);">.toString</span>())<span class="hljs-comment" style="color:rgb(136,0,0);">;</span>
                 System<span class="hljs-preprocessor" style="color:rgb(68,68,68);">.out</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.println</span>(new String(key<span class="hljs-preprocessor" style="color:rgb(68,68,68);">.getValue</span>()))<span class="hljs-comment" style="color:rgb(136,0,0);">;        </span>
            }

            System<span class="hljs-preprocessor" style="color:rgb(68,68,68);">.out</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.println</span>(<span class="hljs-string" style="color:rgb(0,136,0);">"**************************"</span>)<span class="hljs-comment" style="color:rgb(136,0,0);">;</span>

            <span class="hljs-comment" style="color:rgb(136,0,0);">/**3------&gt;遍历所有的记录****/</span>
            Scan scan = new Scan()<span class="hljs-comment" style="color:rgb(136,0,0);">;</span>
            ResultScanner scanner = hTable<span class="hljs-preprocessor" style="color:rgb(68,68,68);">.getScanner</span>(scan)<span class="hljs-comment" style="color:rgb(136,0,0);">;//scanner中包含很多条行记录</span>
            for (Result result3 : scanner)
            {
                for(KeyValue key  : result3<span class="hljs-preprocessor" style="color:rgb(68,68,68);">.list</span>())//一行的记录当中包含很多的键值对信息，依次遍历即可
                {
                     System<span class="hljs-preprocessor" style="color:rgb(68,68,68);">.out</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.println</span>(key<span class="hljs-preprocessor" style="color:rgb(68,68,68);">.toString</span>())<span class="hljs-comment" style="color:rgb(136,0,0);">;</span>
                     System<span class="hljs-preprocessor" style="color:rgb(68,68,68);">.out</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.println</span>(new String(key<span class="hljs-preprocessor" style="color:rgb(68,68,68);">.getValue</span>()))<span class="hljs-comment" style="color:rgb(136,0,0);">;        </span>
                }
            }

            System<span class="hljs-preprocessor" style="color:rgb(68,68,68);">.out</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.println</span>(<span class="hljs-string" style="color:rgb(0,136,0);">"**************************"</span>)<span class="hljs-comment" style="color:rgb(136,0,0);">;</span>

            <span class="hljs-comment" style="color:rgb(136,0,0);">/**4------&gt;仅仅获取每行的city信息****/</span>
            Scan scan1 =new Scan()<span class="hljs-comment" style="color:rgb(136,0,0);">;</span>
            ResultScanner scanner1 = hTable<span class="hljs-preprocessor" style="color:rgb(68,68,68);">.getScanner</span>(scan1 )<span class="hljs-comment" style="color:rgb(136,0,0);">;</span>
            for (Result result4 : scanner1)
            {
                  System<span class="hljs-preprocessor" style="color:rgb(68,68,68);">.out</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.println</span>(new String(result4<span class="hljs-preprocessor" style="color:rgb(68,68,68);">.getRow</span>()))<span class="hljs-comment" style="color:rgb(136,0,0);">;</span>
                  System<span class="hljs-preprocessor" style="color:rgb(68,68,68);">.out</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.println</span>(new String(result4<span class="hljs-preprocessor" style="color:rgb(68,68,68);">.getValue</span>(<span class="hljs-string" style="color:rgb(0,136,0);">"address"</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.getBytes</span>(), <span class="hljs-string" style="color:rgb(0,136,0);">"city"</span><span class="hljs-preprocessor" style="color:rgb(68,68,68);">.getBytes</span>())))<span class="hljs-comment" style="color:rgb(136,0,0);">;</span>
            }
       }
}</code></pre><ul class="pre-numbering" style="width:50px;background-color:rgb(238,238,238);border-right:1px solid rgb(221,221,221);list-style:none;text-align:right;opacity:0;"><li>1</li><li>2</li><li>3</li><li>4</li><li>5</li><li>6</li><li>7</li><li>8</li><li>9</li><li>10</li><li>11</li><li>12</li><li>13</li><li>14</li><li>15</li><li>16</li><li>17</li><li>18</li><li>19</li><li>20</li><li>21</li><li>22</li><li>23</li><li>24</li><li>25</li><li>26</li><li>27</li><li>28</li><li>29</li><li>30</li><li>31</li><li>32</li><li>33</li><li>34</li><li>35</li><li>36</li><li>37</li><li>38</li><li>39</li><li>40</li><li>41</li><li>42</li><li>43</li><li>44</li><li>45</li><li>46</li><li>47</li><li>48</li><li>49</li><li>50</li><li>51</li><li>52</li><li>53</li><li>54</li><li>55</li><li>56</li><li>57</li><li>58</li><li>59</li><li>60</li><li>61</li><li>62</li><li>63</li><li>64</li><li>65</li><li>66</li><li>67</li><li>68</li><li>69</li><li>70</li><li>71</li><li>72</li><li>73</li><li>74</li><li>75</li><li>76</li><li>77</li><li>78</li><li>79</li><li>80</li><li>81</li><li>82</li><li>83</li><li>84</li><li>85</li><li>86</li><li>87</li><li>88</li><li>89</li><li>90</li><li>91</li><li>92</li><li>93</li><li>94</li><li>95</li><li>96</li><li>97</li><li>98</li><li>99</li><li>100</li><li>101</li><li>102</li><li>103</li><li>104</li><li>105</li><li>106</li><li>107</li><li>108</li><li>109</li><li>110</li></ul><ul class="pre-numbering" style="width:50px;background-color:rgb(238,238,238);border-right:1px solid rgb(221,221,221);list-style:none;text-align:right;"><li>1</li><li>2</li><li>3</li><li>4</li><li>5</li><li>6</li><li>7</li><li>8</li><li>9</li><li>10</li><li>11</li><li>12</li><li>13</li><li>14</li><li>15</li><li>16</li><li>17</li><li>18</li><li>19</li><li>20</li><li>21</li><li>22</li><li>23</li><li>24</li><li>25</li><li>26</li><li>27</li><li>28</li><li>29</li><li>30</li><li>31</li><li>32</li><li>33</li><li>34</li><li>35</li><li>36</li><li>37</li><li>38</li><li>39</li><li>40</li><li>41</li><li>42</li><li>43</li><li>44</li><li>45</li><li>46</li><li>47</li><li>48</li><li>49</li><li>50</li><li>51</li><li>52</li><li>53</li><li>54</li><li>55</li><li>56</li><li>57</li><li>58</li><li>59</li><li>60</li><li>61</li><li>62</li><li>63</li><li>64</li><li>65</li><li>66</li><li>67</li><li>68</li><li>69</li><li>70</li><li>71</li><li>72</li><li>73</li><li>74</li><li>75</li><li>76</li><li>77</li><li>78</li><li>79</li><li>80</li><li>81</li><li>82</li><li>83</li><li>84</li><li>85</li><li>86</li><li>87</li><li>88</li><li>89</li><li>90</li><li>91</li><li>92</li><li>93</li><li>94</li><li>95</li><li>96</li><li>97</li><li>98</li><li>99</li><li>100</li><li>101</li><li>102</li><li>103</li><li>104</li><li>105</li><li>106</li><li>107</li><li>108</li><li>109</li><li>110</li></ul><p style="color:rgb(63,63,63);font-family:'microsoft yahei';font-size:15px;">
代码的运行结果如下： <br><img src="https://img-blog.csdn.net/20160518142821249" alt="这里写图片描述" title="" style="border:none;"> <br><img src="https://img-blog.csdn.net/20160518142834597" alt="这里写图片描述" title="" style="border:none;"> <br>
对于Hbase的Java api操作就简单的介绍到这里！ </p>
            </div>
                </div>