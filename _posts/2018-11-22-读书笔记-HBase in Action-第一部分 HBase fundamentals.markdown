---
layout:     post
title:      读书笔记-HBase in Action-第一部分 HBase fundamentals
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/cargogo/article/details/29594519				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
新项目准备上HBase。HBase目前由组里某牛负责。本着学会<span style="font-size:12px;"><strong>使用</strong></span><span style="font-size:12px;">HBase的目标，先阅读下HBase in Action，一共十章组织成三部分，需要学习的内容包括HBase基本实现原理，使用方法，Schema设计原则和实战等。借用Michael Stack（HBase Chair）的话，“At a highlevel, HBase is like
 theatomic bomb. Its basic operation can be explained onthe back of a napkin over adrink (or two). Its deployment is another matter。(HBase像原子弹一样，它的基本操作可以写在一张餐巾纸的背面，但是它的部署就是另一回事了。)</span>
<p align="left">首先，HBase可以理解为Database on Hadoop，即山寨版的BigTable on GFS，具备以下几个主要特点：Key-Value分布式存储、面向列、实时随机读写。</p>
<h1>基本概念&amp;基本操作</h1>
<p>以下是HBase的一些基本术语：</p>
<p></p>
<ol><li>Table：和关系型数据库一样，HBase数据以表的形式组织，表也由行和列组成。</li><li>Column families：列族，即列分组，每个列都属于一个列族。</li><li>Row key：即每行数据的主键，无数据类型，HBase按照rowkey字节序顺序存储记录。</li><li>Version:以时间戳表示的版本号。默认每个数据保留三个版本，按照版本号降序存储。</li><li>Cell：表中的基本存储单元，由{rowkey, column families,column, version} 唯一确定，无数据类型，和row key一样都是字节数组。</li></ol><p></p>
<p>基本操作包括5种：</p>
<p><strong>Put</strong>（存储或者修改单行记录）/<strong>Get</strong>（读取单行记录，可以指定列族、列等）/<strong>Delete</strong>（删除单行记录，由于底层存储不可变，实现为添加墓碑记录）/<strong>Scan</strong>（返回行集合，可以指定扫描起止row key，过滤条件等）/<strong>Increment</strong>（增加某个单元值，原子操作）</p>
<h1>数据模型</h1>
<p>逻辑上，可以把HBase数据理解成有序Map。其中记录行按照row key升序存储，version降序存储。</p>
<p></p>
<pre><code class="language-java">Map&lt;RowKey, Map&lt;ColumnFamily,Map&lt;Column,Map&lt;Version, Data&gt;&gt;&gt;&gt;</code></pre>
<p></p>
<p>物理上（Region及HDFS细节下一节介绍），数据存储在HFile中，HFile只包含某一个列族数据，而每一个列族可能使用多个HFile。HFile使用列式存储。下图是HFile存储示意图，TheRealMT为<strong>row key</strong>，info为<strong>列族</strong>，email/name/password为<strong>列名</strong>，后两列为<strong>时间戳版本号</strong>和<strong>cell值</strong>（注：由于使用列式存储，HBase不用存储null值）。</p>
<p><img src="https://img-blog.csdn.net/20140609171512390?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaWRvbnR3YW50b2Jl/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="">        </p>
<h1>数据读写过程</h1>
<p>HBase数据同时写入到WAL（wrete-ahead log）和MemStore。每个列族都有一个MemStore缓存区，用于提高写入性能，数据定期同步到HFile；WAL用于保障可靠性，出问题时，如果MemStore数据没有同步到HFile中，可以从WAL回放恢复（WAL日志顺序写入，写入性能尚可）。</p>
<p><img src="https://img-blog.csdn.net/20140609171550406?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaWRvbnR3YW50b2Jl/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p>数据读取使用BlockCache/MemStore提高性能。</p>
<p><img src="https://img-blog.csdn.net/20140609171620250?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaWRvbnR3YW50b2Jl/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<h1>HBase与HDFS</h1>
<p>理论上，HBase能运行在各种分布式文件系统之上，实际上，HBase和HDFS紧耦合在一起。HDFS提供可扩展性和容错性。HBase表可以存储数十亿行数据，每行可以包含成百上千个字段。Table随着记录数不断增加变大，会逐渐分裂成多个Region。Region由RegionServer进行管理。借用一张<a href="http://www.searchtb.com/2011/01/understanding-hbase.html" rel="nofollow">淘宝技术博客</a>的图。在实际生产环境中，RegionServer可以部署在HDFS
 DataNode节点上，节省网络IO。</p>
<p><img src="https://img-blog.csdn.net/20140609171715484?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaWRvbnR3YW50b2Jl/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p>从高层次来讲，通过HDFS引入统一命名空间，也保障了HBase的可用性，当某台RegionServer挂掉，其他ReginServer可以读取HDFS上数据。</p>
<p>Region分裂带来一个新问题：怎么查找特定Region。HBase通过两张特殊表-ROOT-和.META来实现。其中-ROOT-表记录了.META表Region信息，-ROOT-表只有一个Region，其位置记录在ZooKeeper上。.META表记录用户表Region信息，可以有多个Region。可以把查找Region看做是在高度为3的B+Tree树中查找叶子节点的过程。</p>
<p><img src="https://img-blog.csdn.net/20140609172634750?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaWRvbnR3YW50b2Jl/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<h1>HBase与MapReduce</h1>
<p>HBase主要用于低延迟访问场景，如果应用强调吞吐量，不关心延迟时间，可以考虑使用MapReduce进行数据处理。TableMapper和TableReduer封装了访问HBase数据的细节。Map和Reduce方法签名如下：</p>
<p align="left"></p>
<pre><code class="language-java">protected void map(ImmutableBytesWritable rowkey,Resultresult,Context context);
protected void reduce(ImmutableBytesWritablerowkey,Iterable&lt;Put&gt; values,Context context);</code></pre>
<p></p>
<p>还需要使用TableMapReduceUtil帮助类初始化Job，指定HBase表名等参数。</p>
<p align="left"></p>
<pre><code class="language-java">TableMapReduceUtil.initTableMapperJob(
  "twits",
  scan,
  Map.class,
  ImmutableBytesWritable.class,
  Result.class,
  job);</code></pre><br>
另外，由于HBase天然是Key-Value存储，可以把它看做是一个分布式Map，利用HBase随机访问高性能特性，使用Get和小范围的Scan操作帮助实现Map-Join，如下图所示：
<p></p>
<p><img src="https://img-blog.csdn.net/20140609171900187?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaWRvbnR3YW50b2Jl/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p> </p>
            </div>
                </div>