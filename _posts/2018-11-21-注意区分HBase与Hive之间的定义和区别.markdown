---
layout:     post
title:      注意区分HBase与Hive之间的定义和区别
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h2>HBase<br>
              官方定义：</h2>

<p style="margin-left:7px;"><span style="color:#333333;">                             <a class="externalLink" href="http://www.apache.org/" rel="nofollow">Apache</a> HBase™是<a class="externalLink" href="http://hadoop.apache.org/" rel="nofollow">Hadoop</a>数据库，是一个分布式，可扩展的大数据存储。</span></p>

<p style="margin-left:7px;"><span style="color:#333333;">                         当您需要对大数据进行随机，实时读/写访问时，请使用Apache HBase™，</span>Apache HBase是一个开源的，分布式的，      版本化的非关系数据库，</p>

<h1 style="margin-left:7px;"><span style="color:#333333;">Hive<br>
             官方定义：</span></h1>

<p style="margin-left:7px;">                             <span style="color:#3399ea;">Apache Hive</span>™数据仓库软件有助于使用SQL读取，编写和管理驻留在分布式存储中的大型数据集。可以将结                构 投 影到已存 储的数据中。提供了命令行工具和JDBC驱动程序以将用户连接到Hive。离线处理。<br>
 </p>

<h2 style="margin-left:7px;">HBase相关概念：</h2>

<pre class="has">
<code class="language-ruby">HBase相关概念简介
---------------------------------------
    1.Configuration描述：
        HBase采用hadoop中的Configuration对象来加载配置文件信息；
    2.HMaster作用：
       a.监控集群中所有的regionserver；
       b.对元数据操作进行管理；
       c.通常情况下，HMaster和NameNode在一个节点上；
       d.管理regionserver的故障转移和region分区；
          表级操作：Table (createTable, modifyTable, removeTable, enable, disable)
	  列簇操作：ColumnFamily (addColumn, modifyColumn, removeColumn)
	  分区操作：Region (move, assign, unassign)
	  负载均衡：LoadBalancer
    3.RegionServer作用：
       a.负责服务和管理region；
       b.通常情况下，HRegionServer和DataNode在一个节点上；
       c.MemStore刷盘操作；
       d.WAL(Write-ahead-log):写前日志
          数据操作：Data (get, put, delete, next, etc.)
          分区操作：Region (splitRegion, compactRegion, etc.)
       e.minor和major
    4.MemStore说明：
       a.每一个region可能存在一个或多个MemStore，每个MemStore对应着当前表的当前区域的一个列簇；
       b.如果达到刷盘条件，写入到磁盘中：StoreFile(HFile)

</code></pre>

<h1 style="margin-left:7px;">Hive相关概念：：<br>
 </h1>

<pre class="has">
<code class="language-ruby"> 1.  hive是数据仓库，在Hadoop基础上处理结构化数据；它驻留在hadoop之上，用户对数据的统计、查询和简单的分析操作；
 2.表模型存储在database(关系型)中，处理的数据存储在HDFS上；
 3.元数据 ：hive选择独立的的数据库(MySQL)用于存储metadata，元数据包含：表的信息、databases信息、表的列信息、字段类型信息和HDFS mapping(映射)；
 4. hive不是： 
        a.关系型数据库；
        b.OLTP
        c.实时查询和行级更新操作
5.存储格式多种，可支持Text，SequenceFile，ParquetFile，RCFILE等；
6.只需要在创建表的时候告诉 Hive 数据中的列分隔符和行分隔符，Hive 就可以解析数据；
7.Hive 中包含以下数据模型：DB--数据库、Table--表，External Table--外部表，Partition--分区，8.Bucket分桶；
9..db:是创建数据库的后缀，是HDFS在${hive.metastore.warehouse.dir}路径下的一个子目录；
10.tables:是hdfs下的.db目录下的一个文件夹；
11.external table：与table类似，不过其数据存放位置可以在任意指定路径
12.管理表：删除表后，都删除元数据和数据；
13.外部表：删除表后，只删除元年数据，不删除HDFS下的数据；
14.partition--分区：在hdfs中表现为table目录下的子目录；
15.bucket：在hdfs中表现为同一个表目录下根据hash散列之后的多个文件
16. hive分桶操作的效果：

         把一个文件按照某个特定的字段和桶数 散列成多个文件</code></pre>

<p style="margin-left:7px;"> </p>

<p style="margin-left:7px;"> </p>

<p style="margin-left:7px;"> </p>

<p style="margin-left:7px;"> </p>

<p style="margin-left:7px;"> </p>

<p style="margin-left:7px;"> </p>

<p style="margin-left:7px;"> </p>

<p style="margin-left:7px;"> </p>

<p style="margin-left:7px;"> </p>

<p style="margin-left:7px;"> </p>            </div>
                </div>