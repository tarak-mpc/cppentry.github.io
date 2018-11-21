---
layout:     post
title:      Hbase入门之三HBase Client API使用入门1
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<span style="color:rgb(102,102,102);font-family:'微软雅黑';font-size:14px;line-height:24px;">Hbase的基本介绍</span><br style="list-style:none;color:rgb(102,102,102);font-family:'微软雅黑';font-size:14px;line-height:24px;"><p style="list-style:none;color:rgb(102,102,102);font-family:'微软雅黑';font-size:14px;line-height:24px;">
　　今天公司进行了一样关于Hbase的培训，让我对Hbase有了基础的了解。和大家分享一样。自己的理解不是很深，如果有说的不对地方，希望大家斧正。</p>
<p style="list-style:none;color:rgb(102,102,102);font-family:'微软雅黑';font-size:14px;line-height:24px;">
　　什么是Hbase？</p>
<p style="list-style:none;color:rgb(102,102,102);font-family:'微软雅黑';font-size:14px;line-height:24px;">
　　HBase是一个分布式的、面向列的开源数据库。</p>
<p style="list-style:none;color:rgb(102,102,102);font-family:'微软雅黑';font-size:14px;line-height:24px;">
　　分布式：Hbase的分布式是依托于Hadoop的，那Hadoop又是什么？Hadoop 是一个能够对大量数据进行分布式处理的软件框架。其实我也不懂这是个什么框架，我的理解是利用Hadoop可以将很多台服务器集成一个集群，接受这个框架统一的调配。不再是分散的，而变成一个有机的整体。Hbase架设在整个服务器集群上。当Hbase创建表时，Hbase会建立一个region，假设为A（默认的region大小为64M），当表中数据越来越大，大小超过64M时，就会发生spite即将这台服务器上该region移动到另外一台服务器上，重新建立一个region
 B继续保存数据，然后超过64M又继续spite。 这样可以将数据分摊到整个服务器的集群中，在查询的时候会首先查找A，如果没有继续查B....这样充分利用了多台服务器资源，防止某一单一的服务器查询工作量过大，实现了负载均衡。 Hbase还可以实现MapReduce。 MapReduce其实就是Map和Reduce两个步骤。Map是将数据分散到各个服务器上去。Reduce就是每个服务器做同样的工作，然后统计出结果。比如：有一张全国所有人名字的表，服务器集群有6台机器，我们想统计出全国姓”张“有多少个。首先Map过程会将这样表分成6份，放到集群中的6台机器中。Reduce就是每台机器都统计自己分到的表中有多少个姓”张“的。然后将每台机器的结果汇总。得出结果。让效率得到极大的提高。</p>
<p style="list-style:none;color:rgb(102,102,102);font-family:'微软雅黑';font-size:14px;line-height:24px;">
　　面向列：指的是同一个列簇里所有数据都存放在一个文件中，从而在读写时有效降低磁盘I/O的开销，并且由于类似数据存放在一起，提高了压缩比。Hbase数据存储是一个四维的表，1、Rowkey 2、版本数 3、ColumnFamily 4、Qualify。Rowkey就是这个表的关键字，相当于一个Map的key。然后value是ColumnFamily。 版本数是数据的版本的数，是以时间戳来记录的。默认的Hbase记录数据的3个版本。就是第一次存入的数据是第一个版本，然后更新之后的数据，Hbase不会讲原来的数据删除，而是作为该数据的老版本继续保留。直到更新的3次。才将最原始数据删除。这样导致数据量翻了3倍。如果不需要最好在更改默认设置。ColumnFamily也相当于一个Map里面继续存Qualify。不过ColumnFamily最好不要超过3个。3个以上会导致查询速率出现问题。Qualify同样是一个Map结构。里面支持好像支持100W对键值对。 以一个例子来看一样Hbase的存储结构吧。例如：一张个人信息表，姓名：Mark
 年龄：22 性别：男 毕业学校：xxx学校  获奖情况：最佳新人奖。 可以这样建立表结构，Rowkey中存入姓名Mark，2个ColumnFamily一个保存这个人的base基本信息如：年龄，性别，一个保存other其他信息。如：学校、获奖。然后Qualify保存详细信息，如name：mark。 最后得到格式为：  mark:{"base":{"name":"Mark","age":22,"sex":"man"},"other":{"school":"xxx学校",{"reward":"最佳新人奖"}}} 
 可以看到所有的列名都会存在数据库中所以最好将名字取得短。</p>
<p style="list-style:none;color:rgb(102,102,102);font-family:'微软雅黑';font-size:14px;line-height:24px;">
　　还有一个索引问题，这种map类型的数据格式。是没有索引的。如果要加索引就是以要加索引的字段为key，原来的Rowkey做值重新建立一张表。也可以存入其他数据。这个就看使用空间换时间还是用时间换空间了。</p>
<p style="list-style:none;color:rgb(102,102,102);font-family:'微软雅黑';font-size:14px;line-height:24px;">
　　大概就是这么一个基本介绍。</p>
<p style="list-style:none;color:rgb(102,102,102);font-family:'微软雅黑';font-size:14px;line-height:24px;">
　　最佳实践：</p>
<p style="list-style:none;color:rgb(102,102,102);font-family:'微软雅黑';font-size:14px;line-height:24px;">
　　1、如果不要用到多版本记录。那么就将最大版本数设为1。否则会多增加很多数据量。</p>
<p style="list-style:none;color:rgb(102,102,102);font-family:'微软雅黑';font-size:14px;line-height:24px;">
　　2、尽可能将字段的名字长度减少，因为这些也都会保存到数据库中。</p>
<p style="list-style:none;color:rgb(102,102,102);font-family:'微软雅黑';font-size:14px;line-height:24px;">
　　3、当存入数据量很大，很快的时候，可以调节region的大小。防止过多的spite操作。过多的spite的操作可能导致hbase挂掉，从而会导致整个集群宕机。</p>
<p style="list-style:none;color:rgb(102,102,102);font-family:'微软雅黑';font-size:14px;line-height:24px;">
　　4、在存入数据前，先把表格式定好。因为存Hbase中的数据一般都是上T的数据，没办法更改表结构。只能重新建立表导入。</p>
<p style="list-style:none;color:rgb(102,102,102);font-family:'微软雅黑';font-size:14px;line-height:24px;">
<br></p>
<p style="list-style:none;color:rgb(102,102,102);font-family:'微软雅黑';font-size:14px;line-height:24px;">
<br></p>
<p style="list-style:none;color:rgb(102,102,102);font-family:'微软雅黑';font-size:14px;line-height:24px;">
</p>
<h2 style="font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;font-weight:normal;line-height:22px;color:rgb(51,51,51);font-size:20px;text-indent:1em;">
<span style="text-indent:0px;">前言</span></h2>
<hr style="border-right-width:0px;border-left-width:0px;border-top-style:solid;border-top-color:rgb(238,238,238);border-bottom-style:solid;border-bottom-color:rgb(255,255,255);color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;font-size:16px;line-height:28.796875px;"><p></p>
<p style="font-size:16px;line-height:28.796875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
1. 创建表：（由master完成）</p>
<ul style="list-style-type:none;font-size:16px;line-height:28.796875px;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;"><li style="line-height:1.8em;list-style-type:disc;">首先需要获取master地址（master启动时会将地址告诉zookeeper）因而客户端首先会访问zookeeper获取master的地址</li><li style="line-height:1.8em;list-style-type:disc;">client和master通信，然后有master来创建表（ 包括表的列簇，是否 <span lang="en-us" xml:lang="en-us">cache</span> ，设置存储的最大版本数，是否压缩等 ）。</li></ul><p style="font-size:16px;line-height:28.796875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
2. 读写删除数据</p>
<ul style="list-style-type:none;font-size:16px;line-height:28.796875px;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;"><li style="line-height:1.8em;list-style-type:disc;">client与regionserver通信，读写、删除数据</li><li style="line-height:1.8em;list-style-type:disc;">写入和删除数据时讲数据打上不同的标志append，真正的数据删除操作在compact时发生</li></ul><p style="font-size:16px;line-height:28.796875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
 3. 版本信息</p>
<p style="font-size:16px;line-height:28.796875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
<img src="http://img0.tuicool.com/neeEBf.png" alt="" style="vertical-align:middle;border:0px none;text-align:center;display:block;"></p>
<h2 style="font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;font-weight:normal;line-height:22px;color:rgb(51,51,51);font-size:20px;text-indent:1em;">
<span style="text-indent:0px;">configuration</span></h2>
<hr style="border-right-width:0px;border-left-width:0px;border-top-style:solid;border-top-color:rgb(238,238,238);border-bottom-style:solid;border-bottom-color:rgb(255,255,255);color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;font-size:16px;line-height:28.796875px;"><p style="font-size:16px;line-height:28.796875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
HbaseConfiguration， 表示HBase的配置信息</p>
<p style="font-size:16px;line-height:28.796875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
两种构造函数如下：</p>
<p style="font-size:16px;line-height:28.796875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
</p>
<p style="font-size:16px;line-height:28.796875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
public HBaseConfiguration() -----------默认的构造方式会从hbase-default.xml和hbase-site.xml中读取配置，如果classpath中没有这两个文件，需要自己配置</p>
<p style="font-size:16px;line-height:28.796875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
public HBaseConfiguration(final Configuration c)</p>
<p style="font-size:16px;line-height:28.796875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
</p>
<p style="font-size:16px;line-height:28.796875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
eg：</p>
<pre class="prettyprint cs" style="font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;color:rgb(51,51,51);font-size:14px;line-height:1.5em;background-color:rgb(246,246,246);"><span style="color:rgb(255,102,0);">    <span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">static</span> HBaseConfiguration cfg = <span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">null</span>;
    </span><span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">static</span></span><span style="color:rgb(0,0,0);"> {
        Configuration HBASE_CONFIG </span>= <span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">new</span></span><span style="color:rgb(0,0,0);"> Configuration();
        HBASE_CONFIG.<span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">set</span>(</span><span class="string" style="color:rgb(221,17,68);">"hbase.zookeeper.quorum"</span>, <span class="string" style="color:rgb(221,17,68);">"192.168.1.95"</span><span style="color:rgb(0,0,0);">);
        HBASE_CONFIG.<span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">set</span>(</span><span class="string" style="color:rgb(221,17,68);">"hbase.zookeeper.property.clientPort"</span>, <span class="string" style="color:rgb(221,17,68);">"2181"</span><span style="color:rgb(0,0,0);">);
      <span style="color:rgb(255,102,0);">  cfg </span></span><span style="color:rgb(255,102,0);">= <span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">new</span></span><span style="color:rgb(0,0,0);"><span style="color:rgb(255,102,0);"> HBaseConfiguration(HBASE_CONFIG);</span>
    }</span></pre>
<h2 style="font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;font-weight:normal;line-height:22px;color:rgb(51,51,51);font-size:20px;text-indent:1em;">
<span style="text-indent:0px;">创建表</span></h2>
<hr style="border-right-width:0px;border-left-width:0px;border-top-style:solid;border-top-color:rgb(238,238,238);border-bottom-style:solid;border-bottom-color:rgb(255,255,255);color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;font-size:16px;line-height:28.796875px;"><p style="font-size:16px;line-height:28.796875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
 使用HBaseAdmin对象的createTable方法</p>
<p style="font-size:16px;line-height:28.796875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
 eg：</p>
<pre class="prettyprint perl" style="font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;color:rgb(51,51,51);font-size:14px;line-height:1.5em;background-color:rgb(246,246,246);"><span style="color:rgb(0,0,255);">public</span> <span style="color:rgb(0,0,255);">static</span> <span style="color:rgb(0,0,255);">void</span><span style="color:rgb(0,0,0);"> createTable(String tableName) { 
            System.out.println(</span><span class="string" style="color:rgb(221,17,68);">"<span class="variable" style="color:rgb(0,128,128);">**</span><span class="variable" style="color:rgb(0,128,128);">**</span><span class="variable" style="color:rgb(0,128,128);">**</span><span class="variable" style="color:rgb(0,128,128);">**</span><span class="variable" style="color:rgb(0,128,128);">**</span><span class="variable" style="color:rgb(0,128,128);">**</span>start create table<span class="variable" style="color:rgb(0,128,128);">**</span><span class="variable" style="color:rgb(0,128,128);">**</span><span class="variable" style="color:rgb(0,128,128);">**</span><span class="variable" style="color:rgb(0,128,128);">**</span><span class="variable" style="color:rgb(0,128,128);">**</span>"</span><span style="color:rgb(0,0,0);">); 
            </span><span style="color:rgb(0,0,255);">try</span><span style="color:rgb(0,0,0);"> { 
               <span style="color:rgb(255,102,0);"> HBaseAdmin hBaseAdmin </span></span><span style="color:rgb(255,102,0);">= new</span><span style="color:rgb(0,0,0);"><span style="color:rgb(255,102,0);"> HBaseAdmin(cfg);</span> 
                </span><span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">if</span></span> (hBaseAdmin.tableExists(tableName)) {<span style="color:rgb(0,128,0);"><span class="regexp" style="color:rgb(0,153,38);">//</span></span><span style="color:rgb(0,128,0);"> 如果存在要创建的表，那么先删除，再创建 </span>
<span style="color:rgb(0,0,0);"><span style="color:rgb(255,102,0);"><span class="indent">  </span>hBaseAdmin.disableTable(tableName); 
<span class="indent">  </span>hBaseAdmin.deleteTable(tableName);</span> 
<span class="indent">  </span>System.out.println(tableName </span>+ <span class="string" style="color:rgb(221,17,68);">" is exist"</span><span style="color:rgb(0,0,0);">); 
                } 
                HTableDescriptor tableDescriptor </span>= <span style="color:rgb(0,0,255);">new</span> HTableDescriptor(tableName);<span style="color:rgb(0,128,0);"><span class="regexp" style="color:rgb(0,153,38);">//</span></span><span style="color:rgb(0,128,0);"> 代表表的schema </span>
                tableDescriptor.addFamily(<span style="color:rgb(0,0,255);">new</span> HColumnDescriptor(<span class="string" style="color:rgb(221,17,68);">"name"</span>)); <span style="color:rgb(0,128,0);"><span class="regexp" style="color:rgb(0,153,38);">//</span></span><span style="color:rgb(0,128,0);">增加列簇 </span>
                tableDescriptor.addFamily(<span style="color:rgb(0,0,255);">new</span> HColumnDescriptor(<span class="string" style="color:rgb(221,17,68);">"age"</span><span style="color:rgb(0,0,0);">)); 
                tableDescriptor.addFamily(</span><span style="color:rgb(0,0,255);">new</span> HColumnDescriptor(<span class="string" style="color:rgb(221,17,68);">"gender"</span><span style="color:rgb(0,0,0);">)); 
                hBaseAdmin.createTable(tableDescriptor); 
            } </span><span style="color:rgb(0,0,255);">catch</span><span style="color:rgb(0,0,0);"> (MasterNotRunningException e) { 
                e.printStackTrace(); 
            } </span><span style="color:rgb(0,0,255);">catch</span><span style="color:rgb(0,0,0);"> (ZooKeeperConnectionException e) { 
                e.printStackTrace(); 
            } </span><span style="color:rgb(0,0,255);">catch</span><span style="color:rgb(0,0,0);"> (IOException e) { 
                e.printStackTrace(); 
            } 
            System.out.println(</span><span class="string" style="color:rgb(221,17,68);">"<span class="variable" style="color:rgb(0,128,128);">**</span><span class="variable" style="color:rgb(0,128,128);">**</span><span class="variable" style="color:rgb(0,128,128);">*end</span> create table<span class="variable" style="color:rgb(0,128,128);">**</span><span class="variable" style="color:rgb(0,128,128);">**</span><span class="variable" style="color:rgb(0,128,128);">**</span><span class="variable" style="color:rgb(0,128,128);">**</span><span class="variable" style="color:rgb(0,128,128);">**</span><span class="variable" style="color:rgb(0,128,128);">**</span><span class="variable" style="color:rgb(0,128,128);">*"</span><span style="color:rgb(0,0,0);">); 
        } </span></span><span style="color:rgb(0,0,0);"></span></pre>
<pre class="prettyprint cs" style="font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;color:rgb(51,51,51);font-size:14px;line-height:1.5em;background-color:rgb(246,246,246);"><span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">public</span></span> <span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">static</span></span> <span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">void</span></span><span style="color:rgb(0,0,0);"> main(String[] agrs) {
        </span><span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">try</span></span><span style="color:rgb(0,0,0);"> {
            String tablename </span>= <span class="string" style="color:rgb(221,17,68);">"wishTest"</span><span style="color:rgb(0,0,0);">;
            HBaseTest.createTable(tablename);
        } </span><span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">catch</span></span><span style="color:rgb(0,0,0);"> (Exception e) {
            e.printStackTrace();
        }
    }</span></pre>
<p style="font-size:16px;line-height:28.796875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
日志信息如下：</p>
<p style="font-size:16px;line-height:28.796875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
</p>
<p style="font-size:16px;line-height:28.796875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
************start create table********** <br>
14/05/18 14:14:22 INFO zookeeper.ZooKeeper: Client environment:zookeeper.version=3.4.5-1392090, built on 09/30/2012 17:52 GMT <br>
14/05/18 14:14:22 INFO zookeeper.ZooKeeper: Client environment:host.name=LJ-PC <br>
14/05/18 14:14:22 INFO zookeeper.ZooKeeper: Client environment:java.version=1.6.0_11 <br>
14/05/18 14:14:22 INFO zookeeper.ZooKeeper: Client environment:java.vendor=Sun Microsystems Inc. <br>
14/05/18 14:14:22 INFO zookeeper.ZooKeeper: Client environment:java.home=D:\java\jdk1.6.0_11\jre <br>
14/05/18 14:14:22 INFO zookeeper.ZooKeeper: Client environment:java.class.path=.... <br>
... <br>
14/05/18 14:14:22 INFO zookeeper.RecoverableZooKeeper: The identifier of this process is 6560@LJ-PC <br>
14/05/18 14:14:22 INFO zookeeper.ClientCnxn: Socket connection established to hadoop/192.168.1.95:2181, initiating session <br>
14/05/18 14:14:22 INFO zookeeper.ClientCnxn: Session establishment complete on server hadoop/192.168.1.95:2181, sessionid = 0x460dd23bda0007, negotiated timeout = 180000 <br>
14/05/18 14:14:22 INFO zookeeper.ZooKeeper: Session: 0x460dd23bda0007 closed <br>
14/05/18 14:14:22 INFO zookeeper.ClientCnxn: EventThread shut down <br>
*****end create table*************</p>
<p style="font-size:16px;line-height:28.796875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
</p>
<p style="font-size:16px;line-height:28.796875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
在centos中查看是否创建成功：</p>
<p style="font-size:16px;line-height:28.796875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
<img src="http://img1.tuicool.com/7fuyi2.png" alt="" style="vertical-align:middle;border:0px none;text-align:center;display:block;"></p>
<p style="font-size:16px;line-height:28.796875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
  网页上查看：</p>
<p style="font-size:16px;line-height:28.796875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
 <img src="http://img2.tuicool.com/vuQNze.png" alt="" style="vertical-align:middle;border:0px none;text-align:center;display:block;"></p>
<pre class="prettyprint http" style="font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;color:rgb(51,51,51);font-size:14px;line-height:1.5em;background-color:rgb(246,246,246);">　　<strong><span class="attribute" style="color:rgb(0,128,128);">HTableDescriptor其他方法如下：</span></strong></pre>
<p style="font-size:16px;line-height:28.796875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
</p>
<ul style="list-style-type:none;font-size:16px;line-height:28.796875px;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;"><li style="line-height:1.8em;list-style-type:disc;"><span lang="en-us" xml:lang="en-us">setMaxFileSize</span> ，指定最大的 <span lang="en-us" xml:lang="en-us">region size</span></li><li style="line-height:1.8em;list-style-type:disc;"><span lang="en-us" xml:lang="en-us">setMemStoreFlushSize</span>   指定 <span lang="en-us" xml:lang="en-us">memstore flush</span> 到 <span lang="en-us" xml:lang="en-us">HDFS</span> 上的文件大小，默认是<span lang="en-us" xml:lang="en-us">64M</span></li><li style="line-height:1.8em;list-style-type:disc;"><span lang="en-us" xml:lang="en-us">public void addFamily(final HColumnDescriptor family)</span></li></ul><p style="font-size:16px;line-height:28.796875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
</p>
<pre class="prettyprint undefined" style="font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;color:rgb(51,51,51);font-size:14px;line-height:1.5em;background-color:rgb(246,246,246);">　<span lang="en-us" xml:lang="en-us">HColumnDescriptor</span> <strong>其他方法如下：</strong></pre>
<p style="font-size:16px;line-height:28.796875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
</p>
<ul style="list-style-type:none;font-size:16px;line-height:28.796875px;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;"><li style="line-height:1.8em;list-style-type:disc;"><span lang="en-us" xml:lang="en-us">setTimeToLive:</span> 指定最大的 <span lang="en-us" xml:lang="en-us">TTL,</span> 单位是 <span lang="en-us" xml:lang="en-us">ms,</span> 过期数据会被自动删除。</li><li style="line-height:1.8em;list-style-type:disc;"><span lang="en-us" xml:lang="en-us">setInMemory:</span> 指定是否放在内存中，对小表有用，可用于提高效率。默认关闭</li><li style="line-height:1.8em;list-style-type:disc;"><span lang="en-us" xml:lang="en-us">setBloomFilter:</span> 指定是否使用 <span lang="en-us" xml:lang="en-us">BloomFilter,</span> 可提高随机查询效率。默认关闭</li><li style="line-height:1.8em;list-style-type:disc;"><span lang="en-us" xml:lang="en-us">setCompressionType:</span> 设定数据压缩类型。默认无压缩。</li><li style="line-height:1.8em;list-style-type:disc;"><span lang="en-us" xml:lang="en-us">setScope(scope):</span> 集群的 <span lang="en-us" xml:lang="en-us">Replication</span> ，默认为 <span lang="en-us" xml:lang="en-us">flase</span></li><li style="line-height:1.8em;list-style-type:disc;"><span lang="en-us" xml:lang="en-us">setBlocksize(blocksize); block</span> 的大小默认是 <span lang="en-us" xml:lang="en-us">64kb</span> ， <span lang="en-us" xml:lang="en-us">block</span> 小适合随机读，但是可能导 <span lang="en-us" xml:lang="en-us">Index</span> 过大而使内存 <span lang="en-us" xml:lang="en-us">oom,
 block</span> 大利于顺序读。</li><li style="line-height:1.8em;list-style-type:disc;"><span lang="en-us" xml:lang="en-us">setMaxVersions:</span> 指定数据最大保存的版本个数。默认为 <span lang="en-us" xml:lang="en-us">3</span> 。版本数最多为<span lang="en-us" xml:lang="en-us">Integer.MAX_VALUE,</span>  但是版本数过多可能导致 <span lang="en-us" xml:lang="en-us">compact</span> 时 <span lang="en-us" xml:lang="en-us">out
 of memory</span> 。</li><li style="line-height:1.8em;list-style-type:disc;"><span lang="en-us" xml:lang="en-us">setBlockCacheEnabled:</span> 是否可以 <span lang="en-us" xml:lang="en-us">cache,</span>  默认设置为 <span lang="en-us" xml:lang="en-us">true</span> ，将最近读取的数据所在的 <span lang="en-us" xml:lang="en-us">Block</span> 放入内存中，标记为 <span lang="en-us" xml:lang="en-us">single</span> ，若下次读命中则将其标记为 <span lang="en-us" xml:lang="en-us">multi</span></li></ul><p style="font-size:16px;line-height:28.796875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
</p>
<h2 style="font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;font-weight:normal;line-height:22px;color:rgb(51,51,51);font-size:20px;text-indent:1em;">
<span style="text-indent:0px;">插入数据</span></h2>
<hr style="border-right-width:0px;border-left-width:0px;border-top-style:solid;border-top-color:rgb(238,238,238);border-bottom-style:solid;border-bottom-color:rgb(255,255,255);color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;font-size:16px;line-height:28.796875px;"><p style="font-size:16px;line-height:28.796875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
使用HTable获取table   <em>注意： <span lang="en-us" xml:lang="en-us">HTable</span> 不是线程安全的，因此当多线程插入数据的时候推荐使用 <span lang="en-us" xml:lang="en-us">HTablePool</span></em></p>
<p style="font-size:16px;line-height:28.796875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
使用put插入数据，可以单条插入数据和批量插入数据，put方法如下：</p>
<p style="font-size:16px;line-height:28.796875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
</p>
<p style="font-size:16px;line-height:28.796875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
public void put(final Put put) throws IOException </p>
<p style="font-size:16px;line-height:28.796875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
public void put(final List&lt;Put&gt; puts) throws IOException</p>
<p style="font-size:16px;line-height:28.796875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
</p>
<p style="font-size:16px;line-height:28.796875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
put 常用方法：</p>
<p style="font-size:16px;line-height:28.796875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
</p>
<ul style="list-style-type:none;font-size:16px;line-height:28.796875px;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;"><li style="line-height:1.8em;list-style-type:disc;"><span lang="en-us" xml:lang="en-us">add:</span> 增加一个 <span lang="en-us" xml:lang="en-us">Cell</span></li><li style="line-height:1.8em;list-style-type:disc;"><span lang="en-us" xml:lang="en-us">setTimeStamp:</span> 指定所有 <span lang="en-us" xml:lang="en-us">cell</span> 默认的 <span lang="en-us" xml:lang="en-us">timestamp,</span> 如果一个 <span lang="en-us" xml:lang="en-us">Cell</span> 没有指定<span lang="en-us" xml:lang="en-us">timestamp,</span> 就会用到这个值。如果没有调用， <span lang="en-us" xml:lang="en-us">HBase</span> 会将当前时间作为未指定<span lang="en-us" xml:lang="en-us">timestamp</span> 的 <span lang="en-us" xml:lang="en-us">cell</span> 的 <span lang="en-us" xml:lang="en-us">timestamp.</span></li><li style="line-height:1.8em;list-style-type:disc;"><span lang="en-us" xml:lang="en-us">setWriteToWAL: WAL</span> 是 <span lang="en-us" xml:lang="en-us">Write Ahead Log</span> 的缩写，指的是 <span lang="en-us" xml:lang="en-us">HBase</span> 在插入操作前是否写 <span lang="en-us" xml:lang="en-us">Log</span> 。默认是打开，关掉会提高性能，但是如果系统出现故障 <span lang="en-us" xml:lang="en-us">(</span> 负责插入的<span lang="en-us" xml:lang="en-us">Region
 Server</span> 挂掉 <span lang="en-us" xml:lang="en-us">)</span> ，数据可能会丢失。</li></ul><p style="font-size:16px;line-height:28.796875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
</p>
<p style="font-size:16px;line-height:28.796875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
下面两个方法会影响插入性能</p>
<p style="font-size:16px;line-height:28.796875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
</p>
<ul style="list-style-type:none;font-size:16px;line-height:28.796875px;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;"><li style="line-height:1.8em;list-style-type:disc;"><span lang="en-us" xml:lang="en-us">setAutoFlash:</span></li></ul><p style="font-size:16px;line-height:28.796875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
<em><span lang="en-us" xml:lang="en-us">AutoFlush</span> 指的是在每次调用 <span lang="en-us" xml:lang="en-us">HBase</span> 的 <span lang="en-us" xml:lang="en-us">Put</span> 操作，是否提交到 <span lang="en-us" xml:lang="en-us">HBase Server</span> 。默认是 <span lang="en-us" xml:lang="en-us">true,</span> 每次会提交。如果此时是单条插入，就会有更多的 <span lang="en-us" xml:lang="en-us">IO,</span> 从而降低性能。进行大量 <span lang="en-us" xml:lang="en-us">Put</span> 时， <span lang="en-us" xml:lang="en-us">HTable</span> 的 <span lang="en-us" xml:lang="en-us">setAutoFlush</span> 最好设置为 <span lang="en-us" xml:lang="en-us">flase</span> </em>。否则每执行一个 <span lang="en-us" xml:lang="en-us">Put</span> 就需要和 <span lang="en-us" xml:lang="en-us">RegionServer</span> 发送一个请求。如果 <span lang="en-us" xml:lang="en-us">autoFlush
 = false</span> ，会等到写缓冲填满才会发起请求。显式的发起请求，可以调用 <span lang="en-us" xml:lang="en-us">flushCommits</span> 。 <span lang="en-us" xml:lang="en-us">HTable</span> 的 <span lang="en-us" xml:lang="en-us">close</span> 操作也会发起<span lang="en-us" xml:lang="en-us">flushCommits</span></p>
<ul style="list-style-type:none;font-size:16px;line-height:28.796875px;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;"><li style="line-height:1.8em;list-style-type:disc;"><span lang="en-us" xml:lang="en-us">setWriteBufferSize:</span></li></ul><p style="font-size:16px;line-height:28.796875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
<span lang="en-us" xml:lang="en-us">Write Buffer Size</span> 在 <span lang="en-us" xml:lang="en-us">AutoFlush</span> 为 <span lang="en-us" xml:lang="en-us">false</span> 的时候起作用，默认是 <span lang="en-us" xml:lang="en-us">2MB,</span> 也就是当插入数据超过 <span lang="en-us" xml:lang="en-us">2MB,</span> 就会自动提交到 <span lang="en-us" xml:lang="en-us">Server</span></p>
<p style="font-size:16px;line-height:28.796875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
</p>
<p style="font-size:16px;line-height:28.796875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
eg:</p>
<pre class="prettyprint perl" style="font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;color:rgb(51,51,51);font-size:14px;line-height:1.5em;background-color:rgb(246,246,246);"><span style="color:rgb(0,0,255);">public</span> <span style="color:rgb(0,0,255);">static</span> <span style="color:rgb(0,0,255);">void</span><span style="color:rgb(0,0,0);"> insert(String tableName) { 
            System.out.println(</span><span class="string" style="color:rgb(221,17,68);">"<span class="variable" style="color:rgb(0,128,128);">**</span><span class="variable" style="color:rgb(0,128,128);">**</span><span class="variable" style="color:rgb(0,128,128);">**</span><span class="variable" style="color:rgb(0,128,128);">**</span><span class="variable" style="color:rgb(0,128,128);">**</span><span class="variable" style="color:rgb(0,128,128);">**</span>start insert <span class="variable" style="color:rgb(0,128,128);">**</span><span class="variable" style="color:rgb(0,128,128);">**</span><span class="variable" style="color:rgb(0,128,128);">**</span><span class="variable" style="color:rgb(0,128,128);">**</span><span class="variable" style="color:rgb(0,128,128);">**</span><span class="variable" style="color:rgb(0,128,128);">**</span>"</span><span style="color:rgb(0,0,0);">); 
           <span style="color:rgb(255,102,0);"> HTablePool pool </span></span><span style="color:rgb(255,102,0);">= new HTablePool(cfg, <span class="number" style="color:rgb(0,153,153);">1000</span></span><span style="color:rgb(0,0,0);"><span style="color:rgb(255,102,0);">);</span></span><span style="color:rgb(255,102,0);">
            Put put = new Put(<span class="string" style="color:rgb(221,17,68);">"1"</span>.getBytes());</span><span style="color:rgb(0,128,0);"><span class="regexp" style="color:rgb(0,153,38);">//</span></span><span style="color:rgb(0,128,0);"> 一个PUT代表一行数据,每行一个唯一的ROWKEY，此处rowkey为<span class="number" style="color:rgb(0,153,153);">1</span> </span>
           <span style="color:rgb(255,102,0);"> put.add(<span class="string" style="color:rgb(221,17,68);">"name"</span>.getBytes(), null, <span class="string" style="color:rgb(221,17,68);">"wish"</span>.getBytes())</span>;<span style="color:rgb(0,128,0);"><span class="regexp" style="color:rgb(0,153,38);">//</span></span><span style="color:rgb(0,128,0);"> 本行数据的第一列 </span>
            put.add(<span class="string" style="color:rgb(221,17,68);">"age"</span>.getBytes(), <span style="color:rgb(0,0,255);">null</span>, <span class="string" style="color:rgb(221,17,68);">"20"</span>.getBytes());<span style="color:rgb(0,128,0);"><span class="regexp" style="color:rgb(0,153,38);">//</span></span><span style="color:rgb(0,128,0);"> 本行数据的第三列 </span>
            put.add(<span class="string" style="color:rgb(221,17,68);">"gender"</span>.getBytes(), <span style="color:rgb(0,0,255);">null</span>, <span class="string" style="color:rgb(221,17,68);">"female"</span>.getBytes());<span style="color:rgb(0,128,0);"><span class="regexp" style="color:rgb(0,153,38);">//</span></span><span style="color:rgb(0,128,0);"> 本行数据的第三列 </span>
            <span style="color:rgb(0,0,255);">try</span><span style="color:rgb(0,0,0);"> { 
                 <span style="color:rgb(255,102,0);">pool.getTable(tableName).put(put);</span> 
            } </span><span style="color:rgb(0,0,255);">catch</span><span style="color:rgb(0,0,0);"> (IOException e) { 
                e.printStackTrace(); 
            } 
            System.out.println(</span><span class="string" style="color:rgb(221,17,68);">"<span class="variable" style="color:rgb(0,128,128);">**</span><span class="variable" style="color:rgb(0,128,128);">**</span><span class="variable" style="color:rgb(0,128,128);">**</span><span class="variable" style="color:rgb(0,128,128);">**</span><span class="variable" style="color:rgb(0,128,128);">**</span><span class="variable" style="color:rgb(0,128,128);">**</span>end insert<span class="variable" style="color:rgb(0,128,128);">**</span><span class="variable" style="color:rgb(0,128,128);">**</span><span class="variable" style="color:rgb(0,128,128);">**</span><span class="variable" style="color:rgb(0,128,128);">**</span><span class="variable" style="color:rgb(0,128,128);">**</span><span class="variable" style="color:rgb(0,128,128);">**</span>"</span><span style="color:rgb(0,0,0);">); 
        } </span></pre>
<pre class="prettyprint cs" style="font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;color:rgb(51,51,51);font-size:14px;line-height:1.5em;background-color:rgb(246,246,246);"><span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">public</span></span> <span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">static</span></span> <span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">void</span></span><span style="color:rgb(0,0,0);"> main(String[] agrs) {
        </span><span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">try</span></span><span style="color:rgb(0,0,0);"> {
            String tablename </span>= <span class="string" style="color:rgb(221,17,68);">"wishTest"</span><span style="color:rgb(0,0,0);">;
            HBaseTest.insert(tablename);
        } </span><span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">catch</span></span><span style="color:rgb(0,0,0);"> (Exception e) {
            e.printStackTrace();
        }
    }</span></pre>
<p style="font-size:16px;line-height:28.796875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
日志信息如下：</p>
<p style="font-size:16px;line-height:28.796875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
</p>
<p style="font-size:16px;line-height:28.796875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
************start insert ************ <br>
14/05/18 15:01:17 WARN hbase.HBaseConfiguration: instantiating HBaseConfiguration() is deprecated. Please use HBaseConfiguration#create() to construct a plain Configuration <br>
14/05/18 15:01:17 INFO zookeeper.ZooKeeper: Client environment:zookeeper.version=3.4.5-1392090, built on 09/30/2012 17:52 GMT <br>
14/05/18 15:01:17 INFO zookeeper.ZooKeeper: Client environment:host.name=LJ-PC <br>
14/05/18 15:01:17 INFO zookeeper.ZooKeeper: Client environment:java.version=1.6.0_11 <br>
14/05/18 15:01:17 INFO zookeeper.ZooKeeper: Client environment:java.vendor=Sun Microsystems Inc. <br>
14/05/18 15:01:17 INFO zookeeper.ZooKeeper: Client environment:java.home=D:\java\jdk1.6.0_11\jre <br>
..... <br>
14/05/18 15:01:17 INFO zookeeper.ZooKeeper: Client environment:java.compiler=&lt;NA&gt; <br>
14/05/18 15:01:17 INFO zookeeper.ZooKeeper: Client environment:os.name=Windows Vista <br>
14/05/18 15:01:17 INFO zookeeper.ZooKeeper: Client environment:os.arch=x86 <br>
14/05/18 15:01:17 INFO zookeeper.ZooKeeper: Client environment:os.version=6.1 <br>
14/05/18 15:01:17 INFO zookeeper.ZooKeeper: Client environment:user.name=root <br>
14/05/18 15:01:17 INFO zookeeper.ZooKeeper: Client environment:user.home=C:\Users\LJ <br>
14/05/18 15:01:17 INFO zookeeper.ZooKeeper: Client environment:user.dir=D:\java\eclipse4.3-jee-kepler-SR1-win32\workspace\hadoop <br>
14/05/18 15:01:17 INFO zookeeper.ZooKeeper: Initiating client connection, connectString=192.168.1.95:2181 sessionTimeout=180000 watcher=hconnection <br>
14/05/18 15:01:17 INFO zookeeper.RecoverableZooKeeper: The identifier of this process is 1252@LJ-PC <br>
14/05/18 15:01:17 INFO zookeeper.ClientCnxn: Opening socket connection to server hadoop/192.168.1.95:2181. Will not attempt to authenticate using SASL (无法定位登录配置) <br>
14/05/18 15:01:17 INFO zookeeper.ClientCnxn: Socket connection established to hadoop/192.168.1.95:2181, initiating session <br>
14/05/18 15:01:17 INFO zookeeper.ClientCnxn: Session establishment complete on server hadoop/192.168.1.95:2181, sessionid = 0x460dd23bda000b, negotiated timeout = 180000 <br>
************end insert************</p>
<p style="font-size:16px;line-height:28.796875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
</p>
<p style="font-size:16px;line-height:28.796875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
查看插入结果：</p>
<p style="font-size:16px;line-height:28.796875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
<img src="http://img0.tuicool.com/NzaMfy.png" alt="" style="vertical-align:middle;border:0px none;text-align:center;display:block;"></p>
<h2 style="font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;font-weight:normal;line-height:22px;color:rgb(51,51,51);font-size:20px;text-indent:1em;">
<span style="text-indent:0px;">查询数据</span></h2>
<hr style="border-right-width:0px;border-left-width:0px;border-top-style:solid;border-top-color:rgb(238,238,238);border-bottom-style:solid;border-bottom-color:rgb(255,255,255);color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;font-size:16px;line-height:28.796875px;"><p style="font-size:16px;line-height:28.796875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
分为单条查询和批量查询，单条查询通过get查询。 通过HTable的getScanner实现批量查询</p>
<p style="font-size:16px;line-height:28.796875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
</p>
<p style="font-size:16px;line-height:28.796875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
public Result get(final Get get)   //单条查询</p>
<p style="font-size:16px;line-height:28.796875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
public ResultScanner getScanner(final Scan scan)  //批量查询</p>
<p style="font-size:16px;line-height:28.796875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
</p>
<p style="font-size:16px;line-height:28.796875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
eg：单条查询：</p>
<pre class="prettyprint cs" style="font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;color:rgb(51,51,51);font-size:14px;line-height:1.5em;background-color:rgb(246,246,246);"><span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">public</span></span> <span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">static</span></span> <span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">void</span></span><span style="color:rgb(0,0,0);"> querySingle(String tableName) { 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span style="color:rgb(255,102,0);">HTablePool pool </span></span><span style="color:rgb(255,102,0);">= <span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">new</span> HTablePool(cfg, <span class="number" style="color:rgb(0,153,153);">1000</span></span><span style="color:rgb(0,0,0);"><span style="color:rgb(255,102,0);">);</span> 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span></span><span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">try</span></span><span style="color:rgb(0,0,0);"> { 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span style="color:rgb(255,102,0);"> Get <span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">get</span> </span></span><span style="color:rgb(255,102,0);">= <span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">new</span> Get(<span class="string" style="color:rgb(221,17,68);">"1"</span>.getBytes());<span class="comment" style="color:rgb(153,153,136);font-style:italic;">//</span></span><span class="comment" style="color:rgb(153,153,136);font-style:italic;"><span style="color:rgb(0,128,0);"><span style="color:rgb(255,102,0);"> 根据rowkey查询</span> </span></span><span style="color:rgb(0,128,0);"></span>
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>  <span style="color:rgb(255,102,0);">  Result r =</span><span style="color:rgb(0,0,0);"><span style="color:rgb(255,102,0);"> pool.getTable(tableName).<span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">get</span>(<span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">get</span>);</span> 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span> System.<span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">out</span>.println(</span><span class="string" style="color:rgb(221,17,68);">"rowkey:"</span> + <span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">new</span></span><span style="color:rgb(0,0,0);"> String(r.getRow())); 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span> </span><span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">for</span></span><span style="color:rgb(0,0,0);"> (KeyValue keyValue : r.raw()) { 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>  System.<span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">out</span>.println(</span><span class="string" style="color:rgb(221,17,68);">"列："</span> + <span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">new</span></span><span style="color:rgb(0,0,0);"> String(keyValue.getFamily()) 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span> </span>+ <span class="string" style="color:rgb(221,17,68);">"	 值:"</span> + <span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">new</span></span><span style="color:rgb(0,0,0);"> String(keyValue.getValue())); 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span> } 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>} </span><span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">catch</span></span><span style="color:rgb(0,0,0);"> (IOException e) { 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span> e.printStackTrace(); 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>} 
<span class="indent">  </span><span class="indent">  </span>  } </span></pre>
<pre class="prettyprint cs" style="font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;color:rgb(51,51,51);font-size:14px;line-height:1.5em;background-color:rgb(246,246,246);"><span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">public</span></span> <span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">static</span></span> <span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">void</span></span><span style="color:rgb(0,0,0);"> main(String[] agrs) {
        </span><span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">try</span></span><span style="color:rgb(0,0,0);"> {
            String tablename </span>= <span class="string" style="color:rgb(221,17,68);">"wishTest"</span><span style="color:rgb(0,0,0);">;
            HBaseTest.querySingle(tablename);
        } </span><span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">catch</span></span><span style="color:rgb(0,0,0);"> (Exception e) {
            e.printStackTrace();
        }
    }</span></pre>
<p style="font-size:16px;line-height:28.796875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
查询结果：</p>
<p style="font-size:16px;line-height:28.796875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
</p>
<p style="font-size:16px;line-height:28.796875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
rowkey:1 <br>
列：age 值:20 <br>
列：gender 值:female <br>
列：name 值:wish</p>
<p style="font-size:16px;line-height:28.796875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
</p>
<p style="font-size:16px;line-height:28.796875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
eg：批量查询：</p>
<pre class="prettyprint cs" style="font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;color:rgb(51,51,51);font-size:14px;line-height:1.5em;background-color:rgb(246,246,246);"><span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">public</span></span> <span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">static</span></span> <span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">void</span></span><span style="color:rgb(0,0,0);"> queryAll(String tableName) { 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>HTablePool pool </span>= <span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">new</span></span> HTablePool(cfg, <span class="number" style="color:rgb(0,153,153);">1000</span><span style="color:rgb(0,0,0);">); 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span></span><span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">try</span></span><span style="color:rgb(0,0,0);"> { 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>ResultScanner rs </span>= pool.getTable(tableName).getScanner(<span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">new</span></span><span style="color:rgb(0,0,0);"> Scan()); 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span></span><span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">for</span></span><span style="color:rgb(0,0,0);"> (Result r : rs) { 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>System.<span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">out</span>.println(</span><span class="string" style="color:rgb(221,17,68);">"rowkey:"</span> + <span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">new</span></span><span style="color:rgb(0,0,0);"> String(r.getRow())); 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span></span><span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">for</span></span><span style="color:rgb(0,0,0);"> (KeyValue keyValue : r.raw()) { 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>System.<span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">out</span>.println(</span><span class="string" style="color:rgb(221,17,68);">"列："</span> + <span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">new</span></span><span style="color:rgb(0,0,0);"> String(keyValue.getFamily()) 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span></span>+ <span class="string" style="color:rgb(221,17,68);">"	 值:"</span> + <span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">new</span></span><span style="color:rgb(0,0,0);"> String(keyValue.getValue())); 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>} 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>} 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>} </span><span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">catch</span></span><span style="color:rgb(0,0,0);"> (IOException e) { 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>e.printStackTrace(); 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>} 
<span class="indent">  </span><span class="indent">  </span>} </span></pre>
<pre class="prettyprint cs" style="font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;color:rgb(51,51,51);font-size:14px;line-height:1.5em;background-color:rgb(246,246,246);"><span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">public</span></span> <span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">static</span></span> <span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">void</span></span><span style="color:rgb(0,0,0);"> main(String[] agrs) {
        </span><span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">try</span></span><span style="color:rgb(0,0,0);"> {
            String tablename </span>= <span class="string" style="color:rgb(221,17,68);">"wishTest"</span><span style="color:rgb(0,0,0);">;
            HBaseTest.queryAll(tablename);
        } </span><span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">catch</span></span><span style="color:rgb(0,0,0);"> (Exception e) {
            e.printStackTrace();
        }
    }</span></pre>
<p style="font-size:16px;line-height:28.796875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
结果如下：</p>
<p style="font-size:16px;line-height:28.796875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
</p>
<p style="font-size:16px;line-height:28.796875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
rowkey:1 <br>
列：age 值:20 <br>
列：gender 值:female <br>
列：name 值:wish <br>
rowkey:112233bbbcccc <br>
列：age 值:20 <br>
列：gender 值:female <br>
列：name 值:wish <br>
rowkey:2 <br>
列：age 值:20 <br>
列：gender 值:female <br>
列：name 值:rain</p>
<p style="font-size:16px;line-height:28.796875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
</p>
<h2 style="font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;font-weight:normal;line-height:22px;color:rgb(51,51,51);font-size:20px;text-indent:1em;">
删除数据</h2>
<hr style="border-right-width:0px;border-left-width:0px;border-top-style:solid;border-top-color:rgb(238,238,238);border-bottom-style:solid;border-bottom-color:rgb(255,255,255);color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;font-size:16px;line-height:28.796875px;"><p style="font-size:16px;line-height:28.796875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
使用HTable的delete删除数据：</p>
<p style="font-size:16px;line-height:28.796875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
</p>
<p style="font-size:16px;line-height:28.796875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
public void delete(final Delete delete)</p>
<p style="font-size:16px;line-height:28.796875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
</p>
<p style="font-size:16px;line-height:28.796875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
 eg:</p>
<pre class="prettyprint perl" style="font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;color:rgb(51,51,51);font-size:14px;line-height:1.5em;background-color:rgb(246,246,246);"><span style="color:rgb(0,0,255);">public</span> <span style="color:rgb(0,0,255);">static</span> <span style="color:rgb(0,0,255);">void</span><span style="color:rgb(0,0,0);"> deleteRow(String tablename, String rowkey)  { 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span></span><span style="color:rgb(0,0,255);">try</span><span style="color:rgb(0,0,0);"> { 
<span style="color:rgb(255,102,0);"><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>HTable table </span></span><span style="color:rgb(255,102,0);">= new</span><span style="color:rgb(0,0,0);"><span style="color:rgb(255,102,0);"> HTable(cfg, tablename); 
</span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>List list </span>= <span style="color:rgb(0,0,255);">new</span><span style="color:rgb(0,0,0);"> ArrayList(); 
<span style="color:rgb(255,102,0);"><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>Delete d1 </span></span><span style="color:rgb(255,102,0);">= new</span><span style="color:rgb(0,0,0);"><span style="color:rgb(255,102,0);"> Delete(rowkey.getBytes()); 
</span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>list.add(d1); 
<span style="color:rgb(255,102,0);"><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>table.<span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">delete</span>(list); 
</span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>System.out.println(</span><span class="string" style="color:rgb(221,17,68);">"删除行成功!"</span><span style="color:rgb(0,0,0);">); 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span> 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>} </span><span style="color:rgb(0,0,255);">catch</span><span style="color:rgb(0,0,0);"> (IOException e) { 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>e.printStackTrace(); 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>} 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span> 
<span class="indent">  </span> 
<span class="indent">  </span><span class="indent">  </span>} 
<span class="indent">  </span> 

<span class="indent">  </span></span><span style="color:rgb(0,0,255);">public</span> <span style="color:rgb(0,0,255);">static</span> <span style="color:rgb(0,0,255);">void</span><span style="color:rgb(0,0,0);"> main(String[] agrs) {
<span class="indent">  </span><span class="indent">  </span></span><span style="color:rgb(0,0,255);">try</span><span style="color:rgb(0,0,0);"> {
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>String tablename </span>= <span class="string" style="color:rgb(221,17,68);">"wishTest"</span><span style="color:rgb(0,0,0);">;
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>System.out.println(</span><span class="string" style="color:rgb(221,17,68);">"<span class="variable" style="color:rgb(0,128,128);">**</span><span class="variable" style="color:rgb(0,128,128);">**</span><span class="variable" style="color:rgb(0,128,128);">**</span><span class="variable" style="color:rgb(0,128,128);">**</span><span class="variable" style="color:rgb(0,128,128);">**</span><span class="variable" style="color:rgb(0,128,128);">**</span><span class="variable" style="color:rgb(0,128,128);">**</span><span class="variable" style="color:rgb(0,128,128);">**</span><span class="variable" style="color:rgb(0,128,128);">**</span><span class="variable" style="color:rgb(0,128,128);">**</span><span class="variable" style="color:rgb(0,128,128);">**</span><span class="variable" style="color:rgb(0,128,128);">**</span><span class="variable" style="color:rgb(0,128,128);">**</span><span class="variable" style="color:rgb(0,128,128);">**</span>删除前数据<span class="variable" style="color:rgb(0,128,128);">**</span><span class="variable" style="color:rgb(0,128,128);">**</span><span class="variable" style="color:rgb(0,128,128);">**</span><span class="variable" style="color:rgb(0,128,128);">**</span><span class="variable" style="color:rgb(0,128,128);">**</span><span class="variable" style="color:rgb(0,128,128);">**</span><span class="variable" style="color:rgb(0,128,128);">**</span><span class="variable" style="color:rgb(0,128,128);">**</span><span class="variable" style="color:rgb(0,128,128);">**</span><span class="variable" style="color:rgb(0,128,128);">**</span><span class="variable" style="color:rgb(0,128,128);">**</span>"</span><span style="color:rgb(0,0,0);">);
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>HBaseTest.queryAll(tablename);
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>HBaseTest.deleteRow(tablename,</span><span class="string" style="color:rgb(221,17,68);">"112233bbbcccc"</span><span style="color:rgb(0,0,0);">);
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>System.out.println(</span><span class="string" style="color:rgb(221,17,68);">"<span class="variable" style="color:rgb(0,128,128);">**</span><span class="variable" style="color:rgb(0,128,128);">**</span><span class="variable" style="color:rgb(0,128,128);">**</span><span class="variable" style="color:rgb(0,128,128);">**</span><span class="variable" style="color:rgb(0,128,128);">**</span><span class="variable" style="color:rgb(0,128,128);">**</span><span class="variable" style="color:rgb(0,128,128);">**</span><span class="variable" style="color:rgb(0,128,128);">**</span><span class="variable" style="color:rgb(0,128,128);">**</span><span class="variable" style="color:rgb(0,128,128);">**</span><span class="variable" style="color:rgb(0,128,128);">**</span><span class="variable" style="color:rgb(0,128,128);">**</span><span class="variable" style="color:rgb(0,128,128);">**</span><span class="variable" style="color:rgb(0,128,128);">**</span>删除后数据<span class="variable" style="color:rgb(0,128,128);">**</span><span class="variable" style="color:rgb(0,128,128);">**</span><span class="variable" style="color:rgb(0,128,128);">**</span><span class="variable" style="color:rgb(0,128,128);">**</span><span class="variable" style="color:rgb(0,128,128);">**</span><span class="variable" style="color:rgb(0,128,128);">**</span><span class="variable" style="color:rgb(0,128,128);">**</span><span class="variable" style="color:rgb(0,128,128);">**</span><span class="variable" style="color:rgb(0,128,128);">**</span><span class="variable" style="color:rgb(0,128,128);">**</span><span class="variable" style="color:rgb(0,128,128);">**</span>"</span><span style="color:rgb(0,0,0);">);
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>HBaseTest.queryAll(tablename);
<span class="indent">  </span><span class="indent">  </span>} </span><span style="color:rgb(0,0,255);">catch</span><span style="color:rgb(0,0,0);"> (Exception e) {
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>e.printStackTrace();
<span class="indent">  </span><span class="indent">  </span>}
<span class="indent">  </span>}</span></pre>
<p style="font-size:16px;line-height:28.796875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
结果如下：</p>
<p style="font-size:16px;line-height:28.796875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
</p>
<p style="font-size:16px;line-height:28.796875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
****************************删除前数据**********************</p>
<p style="font-size:16px;line-height:28.796875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
rowkey:1 <br>
列：age 值:20 <br>
列：gender 值:female <br>
列：name 值:wish <br>
rowkey:112233bbbcccc <br>
列：age 值:20 <br>
列：gender 值:female <br>
列：name 值:wish <br>
rowkey:2 <br>
列：age 值:20 <br>
列：gender 值:female <br>
列：name 值:rain <br>
删除行成功! <br>
****************************删除后数据********************** <br>
rowkey:1 <br>
列：age 值:20 <br>
列：gender 值:female <br>
列：name 值:wish <br>
rowkey:2 <br>
列：age 值:20 <br>
列：gender 值:female <br>
列：name 值:rain</p>
<p style="font-size:16px;line-height:28.796875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
</p>
<h2 style="font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;font-weight:normal;line-height:22px;color:rgb(51,51,51);font-size:20px;text-indent:1em;">
删除表</h2>
<hr style="border-right-width:0px;border-left-width:0px;border-top-style:solid;border-top-color:rgb(238,238,238);border-bottom-style:solid;border-bottom-color:rgb(255,255,255);color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;font-size:16px;line-height:28.796875px;"><p style="font-size:16px;line-height:28.796875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
<em>和hbase shell中类似，删除表前需要先disable表 </em>；分别使用disableTable和deleteTable来删除和禁用表</p>
<p style="font-size:16px;line-height:28.796875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
同创建表一样需要使用HbaseAdmin</p>
<p style="font-size:16px;line-height:28.796875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
eg：</p>
<pre class="prettyprint cs" style="font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;color:rgb(51,51,51);font-size:14px;line-height:1.5em;background-color:rgb(246,246,246);"><span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">public</span></span> <span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">static</span></span> <span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">void</span></span><span style="color:rgb(0,0,0);"> dropTable(String tableName) { 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span></span><span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">try</span></span><span style="color:rgb(0,0,0);"> { 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span style="color:rgb(255,102,0);"><span class="indent">  </span>HBaseAdmin admin </span></span><span style="color:rgb(255,102,0);">= <span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">new</span></span><span style="color:rgb(0,0,0);"><span style="color:rgb(255,102,0);"> HBaseAdmin(cfg);</span> 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span style="color:rgb(255,102,0);">admin.disableTable(tableName); 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>admin.deleteTable(tableName)</span>; 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>System.<span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">out</span>.println(</span><span class="string" style="color:rgb(221,17,68);">"table: "</span>+tableName+<span class="string" style="color:rgb(221,17,68);">"删除成功！"</span><span style="color:rgb(0,0,0);">);
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>} </span><span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">catch</span></span><span style="color:rgb(0,0,0);"> (MasterNotRunningException e) { 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>e.printStackTrace(); 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>} </span><span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">catch</span></span><span style="color:rgb(0,0,0);"> (ZooKeeperConnectionException e) { 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>e.printStackTrace(); 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>} </span><span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">catch</span></span><span style="color:rgb(0,0,0);"> (IOException e) { 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>e.printStackTrace(); 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>} 
<span class="indent">  </span> 
<span class="indent">  </span><span class="indent">  </span>} 
<span class="indent">  </span> 

<span class="indent">  </span></span><span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">public</span></span> <span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">static</span></span> <span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">void</span></span><span style="color:rgb(0,0,0);"> main(String[] agrs) {
<span class="indent">  </span><span class="indent">  </span></span><span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">try</span></span><span style="color:rgb(0,0,0);"> {
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>String tablename </span>= <span class="string" style="color:rgb(221,17,68);">"wishTest"</span><span style="color:rgb(0,0,0);">;
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>HBaseTest.dropTable(tablename);
<span class="indent">  </span><span class="indent">  </span>
<span class="indent">  </span><span class="indent">  </span>} </span><span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">catch</span></span><span style="color:rgb(0,0,0);"> (Exception e) {
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>e.printStackTrace();
<span class="indent">  </span><span class="indent">  </span>}
<span class="indent">  </span>}</span></pre>
<p style="font-size:16px;line-height:28.796875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
结果：</p>
<p style="font-size:16px;line-height:28.796875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
</p>
<p style="font-size:16px;line-height:28.796875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
table: wishTest删除成功！</p>
<p style="font-size:16px;line-height:28.796875px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
</p>
<h2 style="font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;font-weight:normal;line-height:22px;color:rgb(51,51,51);font-size:20px;text-indent:1em;">
完整代码</h2>
<hr style="border-right-width:0px;border-left-width:0px;border-top-style:solid;border-top-color:rgb(238,238,238);border-bottom-style:solid;border-bottom-color:rgb(255,255,255);color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;font-size:16px;line-height:28.796875px;"><pre><code class="language-java"><span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">package</span></span><span style="color:rgb(0,0,0);"> wish.hbase;

</span><span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">import</span></span><span style="color:rgb(0,0,0);"> java.io.IOException;
</span><span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">import</span></span><span style="color:rgb(0,0,0);"> java.util.ArrayList;
</span><span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">import</span></span><span style="color:rgb(0,0,0);"> java.util.List;

</span><span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">import</span></span><span style="color:rgb(0,0,0);"> org.apache.hadoop.conf.Configuration;
</span><span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">import</span></span><span style="color:rgb(0,0,0);"> org.apache.hadoop.hbase.HBaseConfiguration;
</span><span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">import</span></span><span style="color:rgb(0,0,0);"> org.apache.hadoop.hbase.HColumnDescriptor;
</span><span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">import</span></span><span style="color:rgb(0,0,0);"> org.apache.hadoop.hbase.HTableDescriptor;
</span><span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">import</span></span><span style="color:rgb(0,0,0);"> org.apache.hadoop.hbase.KeyValue;
</span><span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">import</span></span><span style="color:rgb(0,0,0);"> org.apache.hadoop.hbase.MasterNotRunningException;
</span><span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">import</span></span><span style="color:rgb(0,0,0);"> org.apache.hadoop.hbase.ZooKeeperConnectionException;
</span><span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">import</span></span><span style="color:rgb(0,0,0);"> org.apache.hadoop.hbase.client.Delete;
</span><span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">import</span></span><span style="color:rgb(0,0,0);"> org.apache.hadoop.hbase.client.Get;
</span><span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">import</span></span><span style="color:rgb(0,0,0);"> org.apache.hadoop.hbase.client.HBaseAdmin;
</span><span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">import</span></span><span style="color:rgb(0,0,0);"> org.apache.hadoop.hbase.client.HTable;
</span><span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">import</span></span><span style="color:rgb(0,0,0);"> org.apache.hadoop.hbase.client.HTablePool;
</span><span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">import</span></span><span style="color:rgb(0,0,0);"> org.apache.hadoop.hbase.client.Put;
</span><span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">import</span></span><span style="color:rgb(0,0,0);"> org.apache.hadoop.hbase.client.Result;
</span><span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">import</span></span><span style="color:rgb(0,0,0);"> org.apache.hadoop.hbase.client.ResultScanner;
</span><span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">import</span></span><span style="color:rgb(0,0,0);"> org.apache.hadoop.hbase.client.Scan;

</span><span style="color:rgb(0,128,0);"><span class="comment" style="color:rgb(153,153,136);font-style:italic;">//</span></span><span class="comment" style="color:rgb(153,153,136);font-style:italic;"><span style="color:rgb(0,128,0);">import org.apache.hadoop.hbase.io.BatchUpdate;</span></span><span style="color:rgb(0,128,0);"></span>
<span style="color:rgb(0,0,0);">
<span class="annotation">@SuppressWarnings</span>(</span><span class="string" style="color:rgb(221,17,68);">"deprecation"</span><span style="color:rgb(0,0,0);">)
</span><span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">public</span></span> <span style="color:rgb(0,0,255);"><span class="class" style="color:rgb(68,85,136);font-weight:bold;"><span class="keyword" style="color:rgb(51,51,51);">class</span></span></span><span class="class" style="color:rgb(68,85,136);font-weight:bold;"><span style="color:rgb(0,0,0);"> <span class="title" style="color:rgb(68,85,136);">HBaseTest</span> {</span></span><span style="color:rgb(0,0,0);">
<span class="indent">  </span></span><span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">static</span></span> HBaseConfiguration cfg = <span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">null</span></span><span style="color:rgb(0,0,0);">;
<span class="indent">  </span></span><span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">static</span></span><span style="color:rgb(0,0,0);"> {
<span class="indent">  </span><span class="indent">  </span>Configuration HBASE_CONFIG </span>= <span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">new</span></span><span style="color:rgb(0,0,0);"> Configuration();
<span class="indent">  </span><span class="indent">  </span>HBASE_CONFIG.set(</span><span class="string" style="color:rgb(221,17,68);">"hbase.zookeeper.quorum"</span>, <span class="string" style="color:rgb(221,17,68);">"192.168.1.95"</span><span style="color:rgb(0,0,0);">);
<span class="indent">  </span><span class="indent">  </span>HBASE_CONFIG.set(</span><span class="string" style="color:rgb(221,17,68);">"hbase.zookeeper.property.clientPort"</span>, <span class="string" style="color:rgb(221,17,68);">"2181"</span><span style="color:rgb(0,0,0);">);
<span class="indent">  </span><span class="indent">  </span>cfg </span>= <span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">new</span></span><span style="color:rgb(0,0,0);"> HBaseConfiguration(HBASE_CONFIG);
<span class="indent">  </span>}

<span class="indent">  </span></span><span style="color:rgb(0,128,0);"><span class="javadoc" style="color:rgb(153,153,136);font-style:italic;">/**</span></span><span class="javadoc" style="color:rgb(153,153,136);font-style:italic;"><span style="color:rgb(0,128,0);">
<span class="indent">  </span> * 创建一张表
<span class="indent">  </span> </span><span style="color:rgb(0,128,0);">*/</span></span><span style="color:rgb(0,128,0);"></span>

<span class="indent">  </span> <span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">public</span></span> <span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">static</span></span> <span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">void</span></span><span style="color:rgb(0,0,0);"> createTable(String tableName) { 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>System.out.println(</span><span class="string" style="color:rgb(221,17,68);">"************start create table**********"</span><span style="color:rgb(0,0,0);">); 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span></span><span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">try</span></span><span style="color:rgb(0,0,0);"> { 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>HBaseAdmin hBaseAdmin </span>= <span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">new</span></span><span style="color:rgb(0,0,0);"> HBaseAdmin(cfg); 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span></span><span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">if</span></span> (hBaseAdmin.tableExists(tableName)) {<span style="color:rgb(0,128,0);"><span class="comment" style="color:rgb(153,153,136);font-style:italic;">//</span></span><span class="comment" style="color:rgb(153,153,136);font-style:italic;"><span style="color:rgb(0,128,0);"> 如果存在要创建的表，那么先删除，再创建 </span></span><span style="color:rgb(0,128,0);"></span>
<span style="color:rgb(0,0,0);"><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>hBaseAdmin.disableTable(tableName); 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>hBaseAdmin.deleteTable(tableName); 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>System.out.println(tableName </span>+ <span class="string" style="color:rgb(221,17,68);">" is exist"</span><span style="color:rgb(0,0,0);">); 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>} 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>HTableDescriptor tableDescriptor </span>= <span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">new</span></span> HTableDescriptor(tableName);<span style="color:rgb(0,128,0);"><span class="comment" style="color:rgb(153,153,136);font-style:italic;">//</span></span><span class="comment" style="color:rgb(153,153,136);font-style:italic;"><span style="color:rgb(0,128,0);"> 代表表的schema </span></span><span style="color:rgb(0,128,0);"></span>
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>tableDescriptor.addFamily(<span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">new</span></span> HColumnDescriptor(<span class="string" style="color:rgb(221,17,68);">"name"</span>)); <span style="color:rgb(0,128,0);"><span class="comment" style="color:rgb(153,153,136);font-style:italic;">//</span></span><span class="comment" style="color:rgb(153,153,136);font-style:italic;"><span style="color:rgb(0,128,0);">增加列簇 </span></span><span style="color:rgb(0,128,0);"></span>
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>tableDescriptor.addFamily(<span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">new</span></span> HColumnDescriptor(<span class="string" style="color:rgb(221,17,68);">"age"</span><span style="color:rgb(0,0,0);">)); 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>tableDescriptor.addFamily(</span><span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">new</span></span> HColumnDescriptor(<span class="string" style="color:rgb(221,17,68);">"gender"</span><span style="color:rgb(0,0,0);">)); 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>hBaseAdmin.createTable(tableDescriptor); 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>} </span><span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">catch</span></span><span style="color:rgb(0,0,0);"> (MasterNotRunningException e) { 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>e.printStackTrace(); 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>} </span><span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">catch</span></span><span style="color:rgb(0,0,0);"> (ZooKeeperConnectionException e) { 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>e.printStackTrace(); 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>} </span><span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">catch</span></span><span style="color:rgb(0,0,0);"> (IOException e) { 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>e.printStackTrace(); 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>} 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>System.out.println(</span><span class="string" style="color:rgb(221,17,68);">"*****end create table*************"</span><span style="color:rgb(0,0,0);">); 
<span class="indent">  </span><span class="indent">  </span>} 

<span class="indent">  </span></span><span style="color:rgb(0,128,0);"><span class="javadoc" style="color:rgb(153,153,136);font-style:italic;">/**</span></span><span class="javadoc" style="color:rgb(153,153,136);font-style:italic;"><span style="color:rgb(0,128,0);">
<span class="indent">  </span> * 插入数据
<span class="indent">  </span> </span><span style="color:rgb(0,128,0);">*/</span></span><span style="color:rgb(0,128,0);"></span>
<span class="indent">  </span> 
<span class="indent">  </span> <span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">public</span></span> <span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">static</span></span> <span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">void</span></span><span style="color:rgb(0,0,0);"> insert(String tableName) { 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>System.out.println(</span><span class="string" style="color:rgb(221,17,68);">"************start insert ************"</span><span style="color:rgb(0,0,0);">); 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>HTablePool pool </span>= <span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">new</span></span> HTablePool(cfg, <span class="number" style="color:rgb(0,153,153);">1000</span><span style="color:rgb(0,0,0);">); 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span></span><span style="color:rgb(0,128,0);"><span class="comment" style="color:rgb(153,153,136);font-style:italic;">//</span></span><span class="comment" style="color:rgb(153,153,136);font-style:italic;"><span style="color:rgb(0,128,0);">HTable table = (HTable) pool.getTable(tableName); </span></span><span style="color:rgb(0,128,0);"></span>
<span style="color:rgb(0,0,0);"><span class="indent">  </span><span class="indent">  </span>   
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>Put put </span>= <span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">new</span></span> Put(<span class="string" style="color:rgb(221,17,68);">"2"</span>.getBytes());<span style="color:rgb(0,128,0);"><span class="comment" style="color:rgb(153,153,136);font-style:italic;">//</span></span><span class="comment" style="color:rgb(153,153,136);font-style:italic;"><span style="color:rgb(0,128,0);"> 一个PUT代表一行数据，再NEW一个PUT表示第二行数据,每行一个唯一的ROWKEY，此处rowkey为put构造方法中传入的值 </span></span><span style="color:rgb(0,128,0);"></span>
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>put.add(<span class="string" style="color:rgb(221,17,68);">"name"</span>.getBytes(), <span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">null</span></span>, <span class="string" style="color:rgb(221,17,68);">"rain"</span>.getBytes());<span style="color:rgb(0,128,0);"><span class="comment" style="color:rgb(153,153,136);font-style:italic;">//</span></span><span class="comment" style="color:rgb(153,153,136);font-style:italic;"><span style="color:rgb(0,128,0);"> 本行数据的第一列 </span></span><span style="color:rgb(0,128,0);"></span>
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>put.add(<span class="string" style="color:rgb(221,17,68);">"age"</span>.getBytes(), <span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">null</span></span>, <span class="string" style="color:rgb(221,17,68);">"20"</span>.getBytes());<span style="color:rgb(0,128,0);"><span class="comment" style="color:rgb(153,153,136);font-style:italic;">//</span></span><span class="comment" style="color:rgb(153,153,136);font-style:italic;"><span style="color:rgb(0,128,0);"> 本行数据的第三列 </span></span><span style="color:rgb(0,128,0);"></span>
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>put.add(<span class="string" style="color:rgb(221,17,68);">"gender"</span>.getBytes(), <span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">null</span></span>, <span class="string" style="color:rgb(221,17,68);">"female"</span>.getBytes());<span style="color:rgb(0,128,0);"><span class="comment" style="color:rgb(153,153,136);font-style:italic;">//</span></span><span class="comment" style="color:rgb(153,153,136);font-style:italic;"><span style="color:rgb(0,128,0);"> 本行数据的第三列 </span></span><span style="color:rgb(0,128,0);"></span>
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">try</span></span><span style="color:rgb(0,0,0);"> { 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span> pool.getTable(tableName).put(put); 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>} </span><span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">catch</span></span><span style="color:rgb(0,0,0);"> (IOException e) { 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>e.printStackTrace(); 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>} 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>System.out.println(</span><span class="string" style="color:rgb(221,17,68);">"************end insert************"</span><span style="color:rgb(0,0,0);">); 
<span class="indent">  </span><span class="indent">  </span>} 
<span class="indent">  </span> 

<span class="indent">  </span></span><span style="color:rgb(0,128,0);"><span class="javadoc" style="color:rgb(153,153,136);font-style:italic;">/**</span></span><span class="javadoc" style="color:rgb(153,153,136);font-style:italic;"><span style="color:rgb(0,128,0);">
<span class="indent">  </span> * 查询所有数据
<span class="indent">  </span> </span><span style="color:rgb(0,128,0);">*/</span></span><span style="color:rgb(0,128,0);"></span>
<span class="indent">  </span>
<span class="indent">  </span> <span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">public</span></span> <span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">static</span></span> <span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">void</span></span><span style="color:rgb(0,0,0);"> queryAll(String tableName) { 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>HTablePool pool </span>= <span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">new</span></span> HTablePool(cfg, <span class="number" style="color:rgb(0,153,153);">1000</span><span style="color:rgb(0,0,0);">); 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span></span><span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">try</span></span><span style="color:rgb(0,0,0);"> { 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>ResultScanner rs </span>= pool.getTable(tableName).getScanner(<span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">new</span></span><span style="color:rgb(0,0,0);"> Scan()); 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span></span><span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">for</span></span><span style="color:rgb(0,0,0);"> (Result r : rs) { 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>System.out.println(</span><span class="string" style="color:rgb(221,17,68);">"rowkey:"</span> + <span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">new</span></span><span style="color:rgb(0,0,0);"> String(r.getRow())); 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span></span><span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">for</span></span><span style="color:rgb(0,0,0);"> (KeyValue keyValue : r.raw()) { 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>System.out.println(</span><span class="string" style="color:rgb(221,17,68);">"列："</span> + <span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">new</span></span><span style="color:rgb(0,0,0);"> String(keyValue.getFamily()) 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span></span>+ <span class="string" style="color:rgb(221,17,68);">"	 值:"</span> + <span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">new</span></span><span style="color:rgb(0,0,0);"> String(keyValue.getValue())); 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>} 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>} 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>} </span><span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">catch</span></span><span style="color:rgb(0,0,0);"> (IOException e) { 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>e.printStackTrace(); 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>} 
<span class="indent">  </span><span class="indent">  </span>} 
<span class="indent">  </span> 
<span class="indent">  </span> </span><span style="color:rgb(0,128,0);"><span class="comment" style="color:rgb(153,153,136);font-style:italic;">/*</span></span><span class="comment" style="color:rgb(153,153,136);font-style:italic;"><span style="color:rgb(0,128,0);">
<span class="indent">  </span>  * 查询单条数据
<span class="indent">  </span>  </span><span style="color:rgb(0,128,0);">*/</span></span><span style="color:rgb(0,128,0);"></span>
<span class="indent">  </span> <span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">public</span></span> <span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">static</span></span> <span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">void</span></span><span style="color:rgb(0,0,0);"> querySingle(String tableName) { 
<span class="indent">  </span><span class="indent">  </span> 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>HTablePool pool </span>= <span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">new</span></span> HTablePool(cfg, <span class="number" style="color:rgb(0,153,153);">1000</span><span style="color:rgb(0,0,0);">); 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span></span><span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">try</span></span><span style="color:rgb(0,0,0);"> { 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>Get get </span>= <span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">new</span></span> Get(<span class="string" style="color:rgb(221,17,68);">"1"</span>.getBytes());<span style="color:rgb(0,128,0);"><span class="comment" style="color:rgb(153,153,136);font-style:italic;">//</span></span><span class="comment" style="color:rgb(153,153,136);font-style:italic;"><span style="color:rgb(0,128,0);"> 根据rowkey查询 </span></span><span style="color:rgb(0,128,0);"></span>
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>Result r =<span style="color:rgb(0,0,0);"> pool.getTable(tableName).get(get); 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>System.out.println(</span><span class="string" style="color:rgb(221,17,68);">"rowkey:"</span> + <span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">new</span></span><span style="color:rgb(0,0,0);"> String(r.getRow())); 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span></span><span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">for</span></span><span style="color:rgb(0,0,0);"> (KeyValue keyValue : r.raw()) { 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>System.out.println(</span><span class="string" style="color:rgb(221,17,68);">"列："</span> + <span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">new</span></span><span style="color:rgb(0,0,0);"> String(keyValue.getFamily()) 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span></span>+ <span class="string" style="color:rgb(221,17,68);">"	值:"</span> + <span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">new</span></span><span style="color:rgb(0,0,0);"> String(keyValue.getValue())); 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>} 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>} </span><span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">catch</span></span><span style="color:rgb(0,0,0);"> (IOException e) { 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>e.printStackTrace(); 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>} 
<span class="indent">  </span><span class="indent">  </span>} 
<span class="indent">  </span> 
<span class="indent">  </span> </span><span style="color:rgb(0,128,0);"><span class="comment" style="color:rgb(153,153,136);font-style:italic;">/*</span></span><span class="comment" style="color:rgb(153,153,136);font-style:italic;"><span style="color:rgb(0,128,0);">
<span class="indent">  </span>  * 删除数据
<span class="indent">  </span>  </span><span style="color:rgb(0,128,0);">*/</span></span><span style="color:rgb(0,128,0);"></span>
<span class="indent">  </span> <span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">public</span></span> <span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">static</span></span> <span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">void</span></span><span style="color:rgb(0,0,0);"> deleteRow(String tablename, String rowkey)  { 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span></span><span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">try</span></span><span style="color:rgb(0,0,0);"> { 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>HTable table </span>= <span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">new</span></span><span style="color:rgb(0,0,0);"> HTable(cfg, tablename); 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>List list </span>= <span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">new</span></span><span style="color:rgb(0,0,0);"> ArrayList(); 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>Delete d1 </span>= <span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">new</span></span><span style="color:rgb(0,0,0);"> Delete(rowkey.getBytes()); 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>list.add(d1); 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>table.delete(list); 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>System.out.println(</span><span class="string" style="color:rgb(221,17,68);">"删除行成功!"</span><span style="color:rgb(0,0,0);">); 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span> 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>} </span><span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">catch</span></span><span style="color:rgb(0,0,0);"> (IOException e) { 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>e.printStackTrace(); 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>} 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span> 
<span class="indent">  </span> 
<span class="indent">  </span><span class="indent">  </span>} 
<span class="indent">  </span> 
<span class="indent">  </span> </span><span style="color:rgb(0,128,0);"><span class="comment" style="color:rgb(153,153,136);font-style:italic;">/*</span></span><span class="comment" style="color:rgb(153,153,136);font-style:italic;"><span style="color:rgb(0,128,0);">
<span class="indent">  </span>  * 删除表
<span class="indent">  </span>  </span><span style="color:rgb(0,128,0);">*/</span></span><span style="color:rgb(0,128,0);"></span>
<span class="indent">  </span> <span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">public</span></span> <span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">static</span></span> <span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">void</span></span><span style="color:rgb(0,0,0);"> dropTable(String tableName) { 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span></span><span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">try</span></span><span style="color:rgb(0,0,0);"> { 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>HBaseAdmin admin </span>= <span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">new</span></span><span style="color:rgb(0,0,0);"> HBaseAdmin(cfg); 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>admin.disableTable(tableName); 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>admin.deleteTable(tableName); 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>System.out.println(</span><span class="string" style="color:rgb(221,17,68);">"table: "</span>+tableName+<span class="string" style="color:rgb(221,17,68);">"删除成功！"</span><span style="color:rgb(0,0,0);">);
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>} </span><span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">catch</span></span><span style="color:rgb(0,0,0);"> (MasterNotRunningException e) { 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>e.printStackTrace(); 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>} </span><span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">catch</span></span><span style="color:rgb(0,0,0);"> (ZooKeeperConnectionException e) { 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>e.printStackTrace(); 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>} </span><span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">catch</span></span><span style="color:rgb(0,0,0);"> (IOException e) { 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>e.printStackTrace(); 
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>} 
<span class="indent">  </span> 
<span class="indent">  </span><span class="indent">  </span>} 
<span class="indent">  </span> 

<span class="indent">  </span></span><span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">public</span></span> <span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">static</span></span> <span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">void</span></span><span style="color:rgb(0,0,0);"> main(String[] agrs) {
<span class="indent">  </span><span class="indent">  </span></span><span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">try</span></span><span style="color:rgb(0,0,0);"> {
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>String tablename </span>= <span class="string" style="color:rgb(221,17,68);">"wishTest"</span><span style="color:rgb(0,0,0);">;
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>HBaseTest.dropTable(tablename);
<span class="indent">  </span><span class="indent">  </span>} </span><span style="color:rgb(0,0,255);"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;">catch</span></span><span style="color:rgb(0,0,0);"> (Exception e) {
<span class="indent">  </span><span class="indent">  </span><span class="indent">  </span>e.printStackTrace();
<span class="indent">  </span><span class="indent">  </span>}
<span class="indent">  </span>}
}</span></code></pre>
<br>            </div>
                </div>