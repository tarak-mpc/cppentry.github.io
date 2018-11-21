---
layout:     post
title:      [一起学Hive]之十三-Hive整合HBase，操作HBase表
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h1>十二、Hive整合HBase，操作HBase表</h1>

<p>HBase是被设计用来做k-v查询的，但有时候，也会遇到基于HBase表的复杂统计，写MR很不方便。Hive考虑到了这点，提供了操作HBase表的接口。</p>

<p>关于Hive操作HBase表的原理，请参考我之前的博文：</p>

<p><a href="http://lxw1234.com/archives/2015/04/101.htm" rel="nofollow">         http://lxw1234.com/archives/2015/04/101.htm</a></p>

<p>值得商榷的是，使用Hive操作HBase中的表，只是提供了便捷性，对于性能上，较MapReduce并不会提升太多，请大家酌情使用。</p>

<p>下面来看使用方法（基于Hive0.13和HBase0.96）：</p>

<h2>12.1 HBase中的表</h2>

<p>先在HBase中创建表：</p>

<pre>

 </pre>

<ol><li>create 'lxw1234',{NAME =&gt; 'f1',VERSIONS =&gt; 1},{NAME =&gt; 'f2',VERSIONS =&gt; 1},</li>
	<li>{NAME =&gt; 'f3',VERSIONS =&gt; 1}</li>
</ol><p>表’lxw1234’有三个列族f1,f2,f3</p>

<p>向HBase表中插入数据：</p>

<pre>

 </pre>

<ol><li>put 'lxw1234','lxw1234.com','f1:c1','name1'</li>
	<li>put 'lxw1234','lxw1234.com','f1:c2','name2'</li>
	<li>put 'lxw1234','lxw1234.com','f2:c1','age1'</li>
	<li>put 'lxw1234','lxw1234.com','f2:c2','age2'</li>
	<li>put 'lxw1234','lxw1234.com','f3:c1','job1'</li>
	<li>put 'lxw1234','lxw1234.com','f3:c2','job2'</li>
	<li>put 'lxw1234','lxw1234.com','f3:c3','job3'</li>
</ol><p>完成后数据如下：</p>

<pre>

 </pre>

<ol><li>hbase(main):025:0* scan 'lxw1234'</li>
	<li>ROW COLUMN+CELL</li>
	<li>lxw1234.com column=f1:c1, timestamp=1435624625198, value=name1</li>
	<li>lxw1234.com column=f1:c2, timestamp=1435624591717, value=name2</li>
	<li>lxw1234.com column=f2:c1, timestamp=1435624608759, value=age1</li>
	<li>lxw1234.com column=f2:c2, timestamp=1435624635261, value=age2</li>
	<li>lxw1234.com column=f3:c1, timestamp=1435624662282, value=job1</li>
	<li>lxw1234.com column=f3:c2, timestamp=1435624697028, value=job2</li>
	<li>lxw1234.com column=f3:c3, timestamp=1435624697065, value=job3</li>
	<li>1 row(s) in 0.0350 seconds</li>
</ol><h2>12.2 Hive中创建基于HBase的表</h2>

<p>在Hive中使用如下语句建表：</p>

<pre>

 </pre>

<ol><li>SET hbase.zookeeper.quorum=zkNode1,zkNode2,zkNode3;</li>
	<li>SET zookeeper.znode.parent=/hbase;</li>
	<li>ADD jar /usr/local/apache-hive-0.13.1-bin/lib/hive-hbase-handler-0.13.1.jar;</li>
	<li> </li>
	<li>CREATE EXTERNAL TABLE lxw1234 (</li>
	<li>rowkey string,</li>
	<li>f1 map&lt;STRING,STRING&gt;,</li>
	<li>f2 map&lt;STRING,STRING&gt;,</li>
	<li>f3 map&lt;STRING,STRING&gt;</li>
	<li>) STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'</li>
	<li>WITH SERDEPROPERTIES ("hbase.columns.mapping" = ":key,f1:,f2:,f3:")</li>
	<li>TBLPROPERTIES ("hbase.table.name" = "lxw1234");</li>
</ol><p>这里使用外部表映射到HBase中的表，这样，在Hive中删除表，并不会删除HBase中的表，否则，就会删除。</p>

<p>另外，除了rowkey，其他三个字段使用Map结构来保存HBase中的每一个列族。</p>

<p>其中，参数解释如下：</p>

<ul><li>hbase.zookeeper.quorum：</li>
</ul><p>指定HBase使用的zookeeper集群，默认端口是2181，可以不指定，如果指定，格式为zkNode1:2222,zkNode2:2222,zkNode3:2222</p>

<ul><li>zookeeper.znode.parent</li>
</ul><p>指定HBase在zookeeper中使用的根目录</p>

<ul><li>hbase.columns.mapping</li>
</ul><p>Hive表和HBase表的字段映射关系，分别为：Hive表中第一个字段映射:key(rowkey)，第二个字段映射列族f1，第三个字段映射列族f2,第四个字段映射列族f3</p>

<ul><li>hbase.table.name</li>
</ul><p>HBase中表的名字</p>

<p>         <strong>也可以直接在Hive</strong><strong>中创建表的同时，完成在HBase</strong><strong>中创建表</strong>。</p>

<p>加入之前没有在HBase中创建表lxw1234,那么使用上面的语句在Hive创建表的时候，会同时在HBase中创建。</p>

<h2>12.3 Hive中查询HBase表</h2>

<p>上面在Hive中创建好表之后，直接查询：</p>

<pre>

 </pre>

<ol><li>hive&gt; select * from lxw1234;</li>
	<li>OK</li>
	<li>lxw1234.com {"c1":"name1","c2":"name2"} {"c1":"age1","c2":"age2"} {"c1":"job1","c2":"job2","c3":"job3"}</li>
</ol><p> </p>

<p>可以看到，Hive中只有一行数据，因为只有一个rowkey，每一个列族的列和值，分别被存储到Map结构中。</p>

<h2>12.4 Hive中插入数据到HBase表</h2>

<p>可以在Hive表中通过Insert语句，完成对HBase表数据的插入。</p>

<p>比如，执行下面的语句：</p>

<pre>

 </pre>

<ol><li>INSERT INTO TABLE lxw1234</li>
	<li>SELECT 'row1' AS rowkey,</li>
	<li>map('c3','name3') AS f1,</li>
	<li>map('c3','age3') AS f2,</li>
	<li>map('c4','job3') AS f3</li>
	<li>FROM DUAL</li>
	<li>limit 1;</li>
</ol><p>在HBase中查看数据：</p>

<pre>

 </pre>

<ol><li>hbase(main):028:0* scan 'lxw1234'</li>
	<li>ROW COLUMN+CELL</li>
	<li>lxw1234.com column=f1:c1, timestamp=1435624625198, value=name1</li>
	<li>lxw1234.com column=f1:c2, timestamp=1435624591717, value=name2</li>
	<li>lxw1234.com column=f2:c1, timestamp=1435624608759, value=age1</li>
	<li>lxw1234.com column=f2:c2, timestamp=1435624635261, value=age2</li>
	<li>lxw1234.com column=f3:c1, timestamp=1435624662282, value=job1</li>
	<li>lxw1234.com column=f3:c2, timestamp=1435624697028, value=job2</li>
	<li>lxw1234.com column=f3:c3, timestamp=1435624697065, value=job3</li>
	<li>row1 column=f1:c3, timestamp=1435625971410, value=name3</li>
	<li>row1 column=f2:c3, timestamp=1435625971410, value=age3</li>
	<li>row1 column=f3:c4, timestamp=1435625971410, value=job3</li>
	<li>2 row(s) in 0.0420 seconds</li>
</ol><p>Hive中的外部表lxw1234，就和其他外部表一样，只有一份元数据，真正的数据是在HBase表中，Hive通过hive-hbase-handler来操作HBase中的表。</p>            </div>
                </div>