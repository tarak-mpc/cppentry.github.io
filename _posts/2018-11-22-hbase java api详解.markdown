---
layout:     post
title:      hbase java api详解
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
HBase是Hadoop的数据库，能够对大数据提供随机、实时读写访问。他是开源的，分布式的，多版本的，面向列的，存储模型。</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
在讲解的时候我首先给大家讲解一下HBase的整体结构，如下图：</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
<img alt="HBase Java API详解" src="http://news.open-open.com/lib/uploadImg/20120717/20120717163859_417.jpg" width="603" height="389" style="margin-left:0px;border-top-width:0px;border-right-width:0px;border-bottom-width:0px;border-left-width:0px;"></p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
HBase Master是服务器负责管理所有的HRegion服务器，HBase Master并不存储HBase服务器的任何数据，HBase逻辑上的表可能会划分为多个HRegion，然后存储在HRegion Server群中，HBase Master Server中存储的是从数据到HRegion Server的映射。</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
一台机器只能运行一个HRegion服务器，数据的操作会记录在Hlog中，在读取数据时候，HRegion会先访问Hmemcache缓存，如果 缓存中没有数据才回到Hstore中上找，没一个列都会有一个Hstore集合，每个Hstore集合包含了很多具体的HstoreFile文件，这些文 件是B树结构的，方便快速读取。</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
 </p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
再看下HBase数据物理视图如下：</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
</p>
<table border="1" cellspacing="0" cellpadding="0" style="margin-left:0px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);"><tbody style="margin-left:0px;"><tr style="margin-left:0px;"><td rowspan="2" width="67" style="margin-left:0px;line-height:18px;font-family:Verdana, '宋体', sans-serif;">
<span style="margin-left:0px;">Row Key</span></td>
<td rowspan="2" width="104" style="margin-left:0px;line-height:18px;font-family:Verdana, '宋体', sans-serif;">
<span style="margin-left:0px;">Timestamp</span></td>
<td width="385" colspan="2" style="margin-left:0px;line-height:18px;font-family:Verdana, '宋体', sans-serif;">
<span style="margin-left:0px;">Column Family</span></td>
</tr><tr style="margin-left:0px;"><td width="227" style="margin-left:0px;line-height:18px;font-family:Verdana, '宋体', sans-serif;">
<span style="margin-left:0px;">URI</span></td>
<td width="158" style="margin-left:0px;line-height:18px;font-family:Verdana, '宋体', sans-serif;">
<span style="margin-left:0px;">Parser</span></td>
</tr><tr style="margin-left:0px;"><td rowspan="3" width="67" style="margin-left:0px;line-height:18px;font-family:Verdana, '宋体', sans-serif;">
r1</td>
<td width="104" style="margin-left:0px;line-height:18px;font-family:Verdana, '宋体', sans-serif;">
t3</td>
<td width="227" style="margin-left:0px;line-height:18px;font-family:Verdana, '宋体', sans-serif;">
url=http://www.taobao.com</td>
<td width="158" style="margin-left:0px;line-height:18px;font-family:Verdana, '宋体', sans-serif;">
title=天天特价</td>
</tr><tr style="margin-left:0px;"><td width="104" style="margin-left:0px;line-height:18px;font-family:Verdana, '宋体', sans-serif;">
t2</td>
<td width="227" style="margin-left:0px;line-height:18px;font-family:Verdana, '宋体', sans-serif;">
host=taobao.com</td>
<td width="158" style="margin-left:0px;line-height:18px;font-family:Verdana, '宋体', sans-serif;">
 </td>
</tr><tr style="margin-left:0px;"><td width="104" style="margin-left:0px;line-height:18px;font-family:Verdana, '宋体', sans-serif;">
t1</td>
<td width="227" style="margin-left:0px;line-height:18px;font-family:Verdana, '宋体', sans-serif;">
 </td>
<td width="158" style="margin-left:0px;line-height:18px;font-family:Verdana, '宋体', sans-serif;">
 </td>
</tr><tr style="margin-left:0px;"><td rowspan="2" width="67" style="margin-left:0px;line-height:18px;font-family:Verdana, '宋体', sans-serif;">
r2</td>
<td width="104" style="margin-left:0px;line-height:18px;font-family:Verdana, '宋体', sans-serif;">
t5</td>
<td width="227" style="margin-left:0px;line-height:18px;font-family:Verdana, '宋体', sans-serif;">
url=http://www.alibaba.com</td>
<td width="158" style="margin-left:0px;line-height:18px;font-family:Verdana, '宋体', sans-serif;">
content=每天…</td>
</tr><tr style="margin-left:0px;"><td width="104" style="margin-left:0px;line-height:18px;font-family:Verdana, '宋体', sans-serif;">
t4</td>
<td width="227" style="margin-left:0px;line-height:18px;font-family:Verdana, '宋体', sans-serif;">
host=alibaba.com</td>
<td width="158" style="margin-left:0px;line-height:18px;font-family:Verdana, '宋体', sans-serif;">
 </td>
</tr></tbody></table><p style="line-height:21px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);border-right-width:0px;list-style-type:none;border-top-width:0px;border-bottom-width:0px;border-left-width:0px;">
<span style="margin-left:0px;line-height:25px;color:rgb(51,51,255);">Ø  Row
 Key: 行键，Table的主键，Table中的记录按照Row Key排序</span></p>
<p style="line-height:21px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);border-right-width:0px;list-style-type:none;border-top-width:0px;border-bottom-width:0px;border-left-width:0px;">
<span style="margin-left:0px;line-height:25px;color:rgb(51,51,255);">Ø  Timestamp:
 时间戳，每次数据操作对应的时间戳，可以看作是数据的version number</span></p>
<p style="line-height:21px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);border-right-width:0px;list-style-type:none;border-top-width:0px;border-bottom-width:0px;border-left-width:0px;">
<span style="margin-left:0px;line-height:25px;color:rgb(51,51,255);">Ø  Column
 Family：列簇，Table在水平方向有一个或者多个Column Family组成，一个Column Family中可以由任意多个Column组成，即Column Family支持动态扩展，无需预先定义Column的数量以及类型，所有Column均以二进制格式存储，用户需要自行进行类型转换。</span></p>
<br style="margin-left:0px;line-height:10px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);"><p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
了解了HBase的体系结构和HBase数据视图够，现在让我们一起看看怎样通过Java来操作HBase数据吧！</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
先说说具体的API先，如下</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
 </p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
<span style="margin-left:0px;color:rgb(255,0,0);">HBaseConfiguration</span>是每一个hbase client都会使用到的对象，它代表的<span style="margin-left:0px;color:rgb(255,0,0);">是HBase配置信息</span>。它有两种构造方式：</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
public HBaseConfiguration()</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
public HBaseConfiguration(final Configuration c)</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
默认的构造方式会尝试从hbase-default.xml和hbase-site.xml中读取配置。如果classpath没有这两个文件，就需要你自己设置配置。</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
Configuration HBASE_CONFIG = new Configuration();</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
HBASE_CONFIG.set(“hbase.zookeeper.quorum”, “zkServer”);</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
HBASE_CONFIG.set(“hbase.zookeeper.property.clientPort”, “2181″);</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
HBaseConfiguration cfg = new HBaseConfiguration(HBASE_CONFIG);</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
 </p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
创建表</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
<span style="margin-left:0px;color:rgb(255,0,0);">创建表是通过HBaseAdmin对象来操作的</span>。HBaseAdmin负责表的META信息处理。HBaseAdmin提供了createTable这个方法：</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
public void createTable(HTableDescriptor desc)</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
<span style="margin-left:0px;color:rgb(255,0,0);">HTableDescriptor 代表的是表的schema</span>, 提供的方法中比较有用的有</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
setMaxFileSize，指定最大的region size</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
setMemStoreFlushSize 指定memstore flush到HDFS上的文件大小</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
 </p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
增加family通过 addFamily方法</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
public void addFamily(final HColumnDescriptor family)</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
HColumnDescriptor 代表的是column的schema，提供的方法比较常用的有</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
<span style="margin-left:0px;color:rgb(255,0,0);">setTimeToLive:指定最大的TTL,单位是ms,过期数据会被自动删除</span>。</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
setInMemory:指定是否放在内存中，对小表有用，可用于提高效率。默认关闭</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
setBloomFilter:指定是否使用BloomFilter,可提高随机查询效率。默认关闭</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
setCompressionType:设定数据压缩类型。默认无压缩。</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
setMaxVersions:指定数据最大保存的版本个数。默认为3。</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
 </p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
<span style="margin-left:0px;color:rgb(51,51,255);">一个简单的例子，创建了4个family的表：</span></p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
 </p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
<span style="margin-left:0px;color:rgb(51,51,255);">HBaseAdmin hAdmin = new HBaseAdmin(hbaseConfig);</span></p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
<span style="margin-left:0px;color:rgb(51,51,255);">HTableDescriptor t = new HTableDescriptor(tableName);</span></p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
<span style="margin-left:0px;color:rgb(51,51,255);">t.addFamily(new HColumnDescriptor(“f1″));</span></p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
<span style="margin-left:0px;color:rgb(51,51,255);">t.addFamily(new HColumnDescriptor(“f2″));</span></p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
<span style="margin-left:0px;color:rgb(51,51,255);">t.addFamily(new HColumnDescriptor(“f3″));</span></p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
<span style="margin-left:0px;color:rgb(51,51,255);">t.addFamily(new HColumnDescriptor(“f4″));</span></p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
<span style="margin-left:0px;color:rgb(51,51,255);">hAdmin.createTable(t);</span></p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
 </p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
删除表</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
<span style="margin-left:0px;color:rgb(255,0,0);">删除表也是通过HBaseAdmin来操作，删除表之前首先要disable表。</span>这是一个非常耗时的操作，所以不建议频繁删除表。</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
disableTable和deleteTable分别用来disable和delete表。</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
Example:</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
HBaseAdmin hAdmin = new HBaseAdmin(hbaseConfig);</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
if (hAdmin.tableExists(tableName)) {</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
       hAdmin.disableTable(tableName);</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
       hAdmin.deleteTable(tableName);</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
}</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
 </p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
查询数据</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
<span style="margin-left:0px;color:rgb(255,0,0);">查询分为单条随机查询和批量查询。</span></p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
<span style="margin-left:0px;color:rgb(255,0,0);">单条查询是通过rowkey在table中查询某一行的数据</span>。HTable提供了get方法来完成单条查询。</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
<span style="margin-left:0px;color:rgb(255,153,0);">批量查询是通过制定一段rowkey的范围来查询</span>。HTable提供了个getScanner方法来完成批量查询。</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
public Result get(final Get get)</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
public ResultScanner getScanner(final Scan scan)</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
Get对象包含了一个Get查询需要的信息。它的构造方法有两种：</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
  public Get(byte [] row)</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
  public Get(byte [] row, RowLock rowLock)</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
Rowlock是为了保证读写的原子性，你可以传递一个已经存在Rowlock，否则HBase会自动生成一个新的rowlock。</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
Scan对象提供了默认构造函数，一般使用默认构造函数。</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
 </p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
Get/Scan的常用方法有：</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
addFamily/addColumn:指定需要的family或者column,如果没有调用任何addFamily或者Column,会返回所有的columns.</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
setMaxVersions:指定最大的版本个数。如果不带任何参数调用setMaxVersions,表示取所有的版本。如果不掉用setMaxVersions,只会取到最新的版本。</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
setTimeRange:指定最大的时间戳和最小的时间戳，只有在此范围内的cell才能被获取。</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
setTimeStamp:指定时间戳。</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
setFilter:指定Filter来过滤掉不需要的信息</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
 </p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
Scan特有的方法：</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
setStartRow:指定开始的行。如果不调用，则从表头开始。</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
setStopRow:指定结束的行（不含此行）。</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
setBatch:指定最多返回的Cell数目。用于防止一行中有过多的数据，导致OutofMemory错误。</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
ResultScanner是Result的一个容器，每次调用ResultScanner的next方法，会返回Result.</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
public Result next() throws IOException;</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
public Result [] next(int nbRows) throws IOException;</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
 </p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
Result代表是一行的数据。常用方法有：</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
getRow:返回rowkey</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
raw:返回所有的key value数组。</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
getValue:按照column来获取cell的值</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
 </p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
Example:</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
Scan s = new Scan();</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
s.setMaxVersions();</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
ResultScanner ss = table.getScanner(s);</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
for(Result r:ss){</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
    System.out.println(new String(r.getRow()));</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
    for(KeyValue kv:r.raw()){</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
       System.out.println(new String(kv.getColumn()));</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
    }</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
}</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
 </p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
<span style="margin-left:0px;color:rgb(255,0,0);">插入数据</span></p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
HTable通过put方法来插入数据。 </p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
public void put(final Put put) throws IOException</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
public void put(final List puts) throws IOException</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
可以传递单个批Put对象或者List put对象来分别实现单条插入和批量插入。</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
Put提供了3种构造方式：</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
public Put(byte [] row)</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
public Put(byte [] row, RowLock rowLock)</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
public Put(Put putToCopy) </p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
 </p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
Put常用的方法有：</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
add:增加一个Cell</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
setTimeStamp:指定所有cell默认的timestamp,如果一个Cell没有指定timestamp,就会用到这个值。如果没有调用，HBase会将当前时间作为未指定timestamp的cell的timestamp.</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
setWriteToWAL: WAL是Write Ahead Log的缩写，指的是HBase在插入操作前是否写Log。默认是打开，关掉会提高性能，但是如果系统出现故障(负责插入的Region Server挂掉)，数据可能会丢失。</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
另外HTable也有两个方法也会影响插入的性能</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
setAutoFlash: AutoFlush指的是在每次调用HBase的Put操作，是否提交到HBase Server。默认是true,每次会提交。如果此时是单条插入，就会有更多的IO,从而降低性能.</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
setWriteBufferSize: Write Buffer Size在AutoFlush为false的时候起作用，默认是2MB,也就是当插入数据超过2MB,就会自动提交到Server</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
 </p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
Example:</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
HTable table = new HTable(hbaseConfig, tableName);</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
table.setAutoFlush(autoFlush);</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
List lp = new ArrayList();</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
int count = 10000;</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
byte[] buffer = new byte[1024];</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
Random r = new Random();</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
for (int i = 1; i &lt;= count; ++i) {</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
       Put p = new Put(String.format(“row%09d”,i).getBytes());</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
       r.nextBytes(buffer);</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
       p.add(“f1″.getBytes(), null, buffer);</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
       p.add(“f2″.getBytes(), null, buffer);</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
       p.add(“f3″.getBytes(), null, buffer);</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
       p.add(“f4″.getBytes(), null, buffer);</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
       p.setWriteToWAL(wal);</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
       lp.add(p);</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
       if(i%1000==0){</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
           table.put(lp);</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
           lp.clear();</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
       }</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
    }</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
 </p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
<span style="margin-left:0px;color:rgb(255,0,0);">删除数据</span></p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
HTable 通过delete方法来删除数据。</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
  public void delete(final Delete delete) </p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
 </p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
Delete构造方法有：</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
public Delete(byte [] row)</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
public Delete(byte [] row, long timestamp, RowLock rowLock)</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
public Delete(final Delete d)</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
Delete常用方法有</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
deleteFamily/deleteColumns:指定要删除的family或者column的数据。如果不调用任何这样的方法，将会删除整行。</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
注意：如果某个Cell的timestamp高于当前时间，这个Cell将不会被删除，仍然可以查出来。</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
 </p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
Example:</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
HTable table = new HTable(hbaseConfig, “mytest”);</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
Delete d = new Delete(“row1″.getBytes());</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
table.delete(d) </p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
 </p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
<span style="margin-left:0px;color:rgb(255,0,0);">切分表</span></p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
HBaseAdmin提供split方法来将table 进行split.</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
public void split(final String tableNameOrRegionName)</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
 </p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
如果提供的tableName，那么会将table所有region进行split ;如果提供的region Name，那么只会split这个region.</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
由于split是一个异步操作，我们并不能确切的控制region的个数。</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
 </p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
Example:</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
public void split(String tableName,int number,int timeout) throws Exception {</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
    Configuration HBASE_CONFIG = new Configuration();</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
    HBASE_CONFIG.set(“hbase.zookeeper.quorum”, GlobalConf.ZOOKEEPER_QUORUM);</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
    HBASE_CONFIG.set(“hbase.zookeeper.property.clientPort”, GlobalConf.ZOOKEEPER_PORT);</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
    HBaseConfiguration cfg = new HBaseConfiguration(HBASE_CONFIG);</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
    HBaseAdmin hAdmin = new HBaseAdmin(cfg);</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
    HTable hTable = new HTable(cfg,tableName);</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
    int oldsize = 0;</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
    t =  System.currentTimeMillis();</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
    while(true){</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
       int size = hTable.getRegionsInfo().size();</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
       logger.info(“the region number=”+size);</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
       if(size&gt;=number ) break;</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
       if(size!=oldsize){</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
           hAdmin.split(hTable.getTableName());</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
           oldsize = size;</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
       }       else if(System.currentTimeMillis()-t&gt;timeout){</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
           break;</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
       }</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
       Thread.sleep(1000*10);</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
    }</p>
<p style="line-height:25px;color:rgb(64,64,64);font-family:arial, sans-serif, verdana, helvetica;font-size:14px;text-align:left;background-color:rgb(250,250,250);">
}</p>
            </div>
                </div>