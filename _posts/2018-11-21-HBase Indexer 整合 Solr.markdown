---
layout:     post
title:      HBase Indexer 整合 Solr
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/sxhong/article/details/79217092				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div class="blog-post">
<div class="content-responsive">
<p>Lily HBase Indexer提供了快速、简单的HBase的内容检索方案，它可以帮助你在Solr中建立HBase的数据索引，从而通过Solr进行数据检索。由于索引过程是异步的，所以并不影响HBase的写负载，同时借助 SolrCloud 可实现分布式索引。</p>
<p>该项目起源于多年研究HBase索引方案的平台Lily。</p>
<span id="more"></span>
<ul style="margin-left:20px;"><li><a href="http://www.niuchaoqun.com/14543825447680.html#toc_0" rel="nofollow" style="color:rgb(51,122,183);">Lily HBase Indexer</a></li><li><a href="http://www.niuchaoqun.com/14543825447680.html#toc_1" rel="nofollow" style="color:rgb(51,122,183);">工作机制</a></li><li><a href="http://www.niuchaoqun.com/14543825447680.html#toc_2" rel="nofollow" style="color:rgb(51,122,183);">独立安装</a>
<ul style="margin-left:20px;"><li><a href="http://www.niuchaoqun.com/14543825447680.html#toc_3" rel="nofollow" style="color:rgb(51,122,183);">要求：</a></li><li><a href="http://www.niuchaoqun.com/14543825447680.html#toc_4" rel="nofollow" style="color:rgb(51,122,183);">复制 jar 包到HBase</a></li><li><a href="http://www.niuchaoqun.com/14543825447680.html#toc_5" rel="nofollow" style="color:rgb(51,122,183);">运行 solr</a></li><li><a href="http://www.niuchaoqun.com/14543825447680.html#toc_6" rel="nofollow" style="color:rgb(51,122,183);">运行 HBase-indexer</a></li></ul></li><li><a href="http://www.niuchaoqun.com/14543825447680.html#toc_7" rel="nofollow" style="color:rgb(51,122,183);">Cloudear CDH 集成Solr</a>
<ul style="margin-left:20px;"><li><a href="http://www.niuchaoqun.com/14543825447680.html#toc_8" rel="nofollow" style="color:rgb(51,122,183);">创建测试表，增加复制参数</a></li><li><a href="http://www.niuchaoqun.com/14543825447680.html#toc_9" rel="nofollow" style="color:rgb(51,122,183);">修改已存在的测试表</a></li><li><a href="http://www.niuchaoqun.com/14543825447680.html#toc_10" rel="nofollow" style="color:rgb(51,122,183);">创建索引配置文件</a></li><li><a href="http://www.niuchaoqun.com/14543825447680.html#toc_11" rel="nofollow" style="color:rgb(51,122,183);">创建Solr索引</a></li><li><a href="http://www.niuchaoqun.com/14543825447680.html#toc_12" rel="nofollow" style="color:rgb(51,122,183);">查看索引</a></li><li><a href="http://www.niuchaoqun.com/14543825447680.html#toc_13" rel="nofollow" style="color:rgb(51,122,183);">更新数据测试</a></li></ul></li><li><a href="http://www.niuchaoqun.com/14543825447680.html#toc_14" rel="nofollow" style="color:rgb(51,122,183);">常用操作</a></li><li><a href="http://www.niuchaoqun.com/14543825447680.html#toc_15" rel="nofollow" style="color:rgb(51,122,183);">索引配置</a>
<ul style="margin-left:20px;"><li><a href="http://www.niuchaoqun.com/14543825447680.html#toc_16" rel="nofollow" style="color:rgb(51,122,183);">全局索引属性</a></li><li><a href="http://www.niuchaoqun.com/14543825447680.html#toc_17" rel="nofollow" style="color:rgb(51,122,183);">索引元素定义</a></li><li><a href="http://www.niuchaoqun.com/14543825447680.html#toc_18" rel="nofollow" style="color:rgb(51,122,183);">配置范例</a></li></ul></li><li><a href="http://www.niuchaoqun.com/14543825447680.html#toc_19" rel="nofollow" style="color:rgb(51,122,183);">数据批量导入</a></li></ul><h2 id="toc_0" style="font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;line-height:1.1;color:rgb(51,51,51);font-size:24px;">
Lily HBase Indexer</h2>
<p>参考：<br><a href="http://ngdata.github.io/hbase-indexer/" rel="nofollow" style="color:rgb(51,122,183);">http://ngdata.github.io/hbase-indexer/</a><br><a href="https://github.com/NGDATA/hbase-indexer/wiki" rel="nofollow" style="color:rgb(51,122,183);">https://github.com/NGDATA/hbase-indexer/wiki</a></p>
<h2 id="toc_1" style="font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;line-height:1.1;color:rgb(51,51,51);font-size:24px;">
工作机制</h2>
<p>HBase Indexer 通过HBase 的复制功能来实现，当数据写入HBase Region 时，数据会被异步复制到 HBase索引协处理器，索引优化器创建文档并推送到 SolrCloud Servers 中。</p>
<p>借助Zookeeper，索引，HBase都可以水平扩展。</p>
<p>通常索引节点和 Solr 节点失效都不会导致数据丢失。</p>
<h2 id="toc_2" style="font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;line-height:1.1;color:rgb(51,51,51);font-size:24px;">
独立安装</h2>
<h3 id="toc_3" style="font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;line-height:1.1;color:rgb(51,51,51);font-size:20px;">
要求：</h3>
<ul style="margin-left:20px;"><li>HBase 0.94.x</li><li>Solr4.x</li><li>zookeeper 3.x</li></ul><p>HBase Indexer 依赖 Zookeeper，所以需要配置自己的Zookeeper配置段与HBase的配置段：<br>
conf/hbase-indexer-site.xml</p>
<pre style="overflow:auto;font-family:Menlo, Monaco, Consolas, 'Courier New', monospace;line-height:1;color:rgb(51,51,51);border-width:1px;border-style:solid;border-color:rgb(204,204,204);"><code style="font-family:'Source Code Pro', Consolas, Menlo, Monaco, 'Courier New', monospace;color:inherit;background-color:transparent;">&lt;property&gt;
  &lt;name&gt;hbaseindexer.zookeeper.connectstring&lt;/name&gt;
  &lt;value&gt;zookeeperhost&lt;/value&gt;
&lt;/property&gt;
&lt;property&gt;
  &lt;name&gt;hbase.zookeeper.quorum&lt;/name&gt;
  &lt;value&gt;zookeeperhost&lt;/value&gt;
&lt;/property&gt;
</code></pre>
<p>HBase 开启复制机制，hbase-site.xml</p>
<pre style="overflow:auto;font-family:Menlo, Monaco, Consolas, 'Courier New', monospace;line-height:1;color:rgb(51,51,51);border-width:1px;border-style:solid;border-color:rgb(204,204,204);"><code style="font-family:'Source Code Pro', Consolas, Menlo, Monaco, 'Courier New', monospace;color:inherit;background-color:transparent;">&lt;configuration&gt;
  &lt;!-- SEP is basically replication, so enable it --&gt;
  &lt;property&gt;
    &lt;name&gt;hbase.replication&lt;/name&gt;
    &lt;value&gt;true&lt;/value&gt;
  &lt;/property&gt;
  &lt;property&gt;
    &lt;name&gt;replication.source.ratio&lt;/name&gt;
    &lt;value&gt;1.0&lt;/value&gt;
  &lt;/property&gt;
  &lt;property&gt;
    &lt;name&gt;replication.source.nb.capacity&lt;/name&gt;
    &lt;value&gt;1000&lt;/value&gt;
  &lt;/property&gt;
  &lt;property&gt;
    &lt;name&gt;replication.replicationsource.implementation&lt;/name&gt;
    &lt;value&gt;com.ngdata.sep.impl.SepReplicationSource&lt;/value&gt;
  &lt;/property&gt;
&lt;/configuration&gt;
</code></pre>
<h3 id="toc_4" style="font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;line-height:1.1;color:rgb(51,51,51);font-size:20px;">
复制 jar 包到HBase</h3>
<pre style="overflow:auto;font-family:Menlo, Monaco, Consolas, 'Courier New', monospace;line-height:1;color:rgb(51,51,51);border-width:1px;border-style:solid;border-color:rgb(204,204,204);"><code style="font-family:'Source Code Pro', Consolas, Menlo, Monaco, 'Courier New', monospace;color:inherit;background-color:transparent;">cp lib/hbase-sep-* $HBASE_HOME/lib
</code></pre>
<h3 id="toc_5" style="font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;line-height:1.1;color:rgb(51,51,51);font-size:20px;">
运行 solr</h3>
<pre style="overflow:auto;font-family:Menlo, Monaco, Consolas, 'Courier New', monospace;line-height:1;color:rgb(51,51,51);border-width:1px;border-style:solid;border-color:rgb(204,204,204);"><code style="font-family:'Source Code Pro', Consolas, Menlo, Monaco, 'Courier New', monospace;color:inherit;background-color:transparent;">cd $SOLR_HOME/example
java -Dbootstrap_confdir=./solr/collection1/conf -Dcollection.configName=myconf -DzkHost=localhost:2181/solr  -jar start.jar
</code></pre>
<h3 id="toc_6" style="font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;line-height:1.1;color:rgb(51,51,51);font-size:20px;">
运行 HBase-indexer</h3>
<pre style="overflow:auto;font-family:Menlo, Monaco, Consolas, 'Courier New', monospace;line-height:1;color:rgb(51,51,51);border-width:1px;border-style:solid;border-color:rgb(204,204,204);"><code style="font-family:'Source Code Pro', Consolas, Menlo, Monaco, 'Courier New', monospace;color:inherit;background-color:transparent;">cd $INDEXER_HOME
./bin/hbase-indexer server
</code></pre>
<h2 id="toc_7" style="font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;line-height:1.1;color:rgb(51,51,51);font-size:24px;">
Cloudear CDH 集成Solr</h2>
<p>Cloudear CDH 套件中集成了该组件，可以通过服务添加。虽然CDH的组件版本比要求更新，但是 Cloudera 做好了兼容性补丁，并且免去了一些配置，和jar包的复制。</p>
<p>直接添加这两个服务即可，Solr的分布式依赖Zookeeper组件，另外需要注意的是 Solr 需要在配置界面，进行初始化以后才能启动。</p>
<p>安装完成后，启动HBase-Indexer与Solr，然后需要以下几步：</p>
<ul style="margin-left:20px;"><li>配置 HBase ，打开的复制配置，通过CDH管理界面</li><li>创建一个基于SolrCloud 的 Core，和独立安装不同，Core和索引会创建到 HDFS 上，可通过 CDH自带的命令 solrctl完成</li></ul><pre style="overflow:auto;font-family:Menlo, Monaco, Consolas, 'Courier New', monospace;line-height:1;color:rgb(51,51,51);border-width:1px;border-style:solid;border-color:rgb(204,204,204);"><code style="font-family:'Source Code Pro', Consolas, Menlo, Monaco, 'Courier New', monospace;color:inherit;background-color:transparent;"># 初始化目录
solrctl instancedir --generate /opt/lib/solr/collection1
# 修改solr字段配置
vi $HOME/collection1/conf/schema.xml
# 生成HDFS目录
solrctl instancedir --create collection1 /opt/lib/solr/collection1
# 创建SolrCloud 的Core，可指定分片数和副本数量
solrctl collection --create collection1 -s 3 -r 1
</code></pre>
<h3 id="toc_8" style="font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;line-height:1.1;color:rgb(51,51,51);font-size:20px;">
创建测试表，增加复制参数</h3>
<pre style="overflow:auto;font-family:Menlo, Monaco, Consolas, 'Courier New', monospace;line-height:1;color:rgb(51,51,51);border-width:1px;border-style:solid;border-color:rgb(204,204,204);"><code style="font-family:'Source Code Pro', Consolas, Menlo, Monaco, 'Courier New', monospace;color:inherit;background-color:transparent;">hbase&gt; create 'test:content_k', { NAME =&gt; 'info', REPLICATION_SCOPE =&gt; '1' }
</code></pre>
<h3 id="toc_9" style="font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;line-height:1.1;color:rgb(51,51,51);font-size:20px;">
修改已存在的测试表</h3>
<pre style="overflow:auto;font-family:Menlo, Monaco, Consolas, 'Courier New', monospace;line-height:1;color:rgb(51,51,51);border-width:1px;border-style:solid;border-color:rgb(204,204,204);"><code style="font-family:'Source Code Pro', Consolas, Menlo, Monaco, 'Courier New', monospace;color:inherit;background-color:transparent;">hbase&gt; disable 'test:content_k'
hbase&gt; alter 'test:content_k', {NAME=&gt;'d',REPLICATION_SCOPE =&gt; 1}
hbase&gt; enable 'test:content_k'
</code></pre>
<h3 id="toc_10" style="font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;line-height:1.1;color:rgb(51,51,51);font-size:20px;">
创建索引配置文件</h3>
<pre style="overflow:auto;font-family:Menlo, Monaco, Consolas, 'Courier New', monospace;line-height:1;color:rgb(51,51,51);border-width:1px;border-style:solid;border-color:rgb(204,204,204);"><code style="font-family:'Source Code Pro', Consolas, Menlo, Monaco, 'Courier New', monospace;color:inherit;background-color:transparent;">vi index_test_content_k.xml

&lt;?xml version="1.0"?&gt;
&lt;indexer table="test:content_k"&gt;
  &lt;field name="md5_s" value="d:md5"/&gt;
  &lt;field name="title_s" value="d:title"/&gt;
  &lt;field name="catid_i" value="d:catid" type="int"/&gt;
  &lt;field name="modelid_i" value="d:modelid" type="int"/&gt;
  &lt;field name="published_i" value="d:published" type="int"/&gt;
  &lt;field name="publishedby_i" value="d:publishedby" type="int"/&gt;
  &lt;field name="time_s" value="d:time"/&gt;
  &lt;field name="datetime_s" value="d:datetime"/&gt;
  &lt;field name="status_i" value="d:status" type="int"/&gt;
&lt;/indexer&gt;
</code></pre>
<h3 id="toc_11" style="font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;line-height:1.1;color:rgb(51,51,51);font-size:20px;">
创建Solr索引</h3>
<pre style="overflow:auto;font-family:Menlo, Monaco, Consolas, 'Courier New', monospace;line-height:1;color:rgb(51,51,51);border-width:1px;border-style:solid;border-color:rgb(204,204,204);"><code style="font-family:'Source Code Pro', Consolas, Menlo, Monaco, 'Courier New', monospace;color:inherit;background-color:transparent;">hbase-indexer add-indexer -n index_test_content_k -c index_test_content_k.xml \
-cp solr.zk=hadoop-03,hadoop-02,hadoop-05:2181/solr -cp solr.collection=collection1
</code></pre>
<h3 id="toc_12" style="font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;line-height:1.1;color:rgb(51,51,51);font-size:20px;">
查看索引</h3>
<pre style="overflow:auto;font-family:Menlo, Monaco, Consolas, 'Courier New', monospace;line-height:1;color:rgb(51,51,51);border-width:1px;border-style:solid;border-color:rgb(204,204,204);"><code style="font-family:'Source Code Pro', Consolas, Menlo, Monaco, 'Courier New', monospace;color:inherit;background-color:transparent;">hbase-indexer list-indexers -z hadoop-03,hadoop-02,hadoop-05:2181
</code></pre>
<h3 id="toc_13" style="font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;line-height:1.1;color:rgb(51,51,51);font-size:20px;">
更新数据测试</h3>
<p>注意：Solr是否立即更新依赖 autocommit 这项配置</p>
<h2 id="toc_14" style="font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;line-height:1.1;color:rgb(51,51,51);font-size:24px;">
常用操作</h2>
<h2 id="toc_15" style="font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;line-height:1.1;color:rgb(51,51,51);font-size:24px;">
索引配置</h2>
<p>最简单的索引配置只有一个表名和一个字段，就像这样：</p>
<pre style="overflow:auto;font-family:Menlo, Monaco, Consolas, 'Courier New', monospace;line-height:1;color:rgb(51,51,51);border-width:1px;border-style:solid;border-color:rgb(204,204,204);"><code style="font-family:'Source Code Pro', Consolas, Menlo, Monaco, 'Courier New', monospace;color:inherit;background-color:transparent;">&lt;indexer table="mytable"&gt;
  &lt;field name="fieldname" value="columnfamily:qualifier" type="string"/&gt;
&lt;/indexer&gt;
</code></pre>
<h3 id="toc_16" style="font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;line-height:1.1;color:rgb(51,51,51);font-size:20px;">
全局索引属性</h3>
<ul style="margin-left:20px;"><li>table</li></ul><p>指定被索引的 HBase 的表名，是索引配置的唯一必须值</p>
<ul style="margin-left:20px;"><li>mapping-type</li></ul><p>映射类型，因为HBase是列式数据库，可能有高表和宽表的概念，所以该字段指定索引是基于行还是基于列，默认是基于行。</p>
<ul style="margin-left:20px;"><li>read-row</li></ul><p>该属性有两个值，dynamic 与 never。默认是 dynamic</p>
<p>当使用基于行的映射类型时，该属性比较重要，它决定了是否从 HBase 来更新已存在数据的索引。</p>
<ul style="margin-left:20px;"><li>mapper</li></ul><p>自定义映射属性，默认是com.ngdata.hbaseindexer.parse.DefaultResultToSolrMapper</p>
<ul style="margin-left:20px;"><li>unique-key-formatter</li></ul><p>唯一键格式，默认使用com.ngdata.hbaseindexer.uniquekey.StringUniqueKeyFormatter，会将 HBase 的Row Key作为字符串来处理，当然你可以扩展该属性。</p>
<ul style="margin-left:20px;"><li>unique-key-field</li></ul><p>指定 Solr 的唯一键，默认是id这个字段</p>
<ul style="margin-left:20px;"><li>row-field</li></ul><p>指定 Solr 用于存储 HBase Rowkey 的字段名，默认为空，意味着rowkey 不会存储在Solr的索引中</p>
<ul style="margin-left:20px;"><li>column-family-field</li></ul><p>指定 Solr 用于存储 HBase 列族的字段名，默认为空，同上。</p>
<ul style="margin-left:20px;"><li>table-name-field</li></ul><p>指定 Solr 用于存储 HBase 表名称的字段名，默认为空，同上。</p>
<h3 id="toc_17" style="font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;line-height:1.1;color:rgb(51,51,51);font-size:20px;">
索引元素定义</h3>
<p>索引元素只有三种类型：</p>
<ul style="margin-left:20px;"><li>field</li></ul><p>该类型定义一个独立的用于 Solr 索引的字段，包含三个属性</p>
<p>name， 索引字段名称<br>
value, 索引值，来源于 HBase 的值，可以有三种表示方法：</p>
<pre style="overflow:auto;font-family:Menlo, Monaco, Consolas, 'Courier New', monospace;line-height:1;color:rgb(51,51,51);border-width:1px;border-style:solid;border-color:rgb(204,204,204);"><code style="font-family:'Source Code Pro', Consolas, Menlo, Monaco, 'Courier New', monospace;color:inherit;background-color:transparent;">mycolumnfamily:myqualifier
mycolumnfamily:my*
mycolumnfamily:*
</code></pre>
<p>source, 决定用于索引的类型，有两种类型：value 与 qualifier， 表示值还是列标识符</p>
<p>type， 指定 HBase的索引数据类型，因为HBase 存储所有的数据类型都是字节数组，而Solr一般都是作为文本来进行索引，所以需要字节数组向真实数据类型的转换。这个属性值可以为 HBase 的 Byte类所支持的所有类型： int, long, string ,boolean, float, double, short, bigdecimalj。当然你也可以自定义。</p>
<ul style="margin-left:20px;"><li>param</li></ul><p>定义了一个键值对，具体使用场景不明。</p>
<h3 id="toc_18" style="font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;line-height:1.1;color:rgb(51,51,51);font-size:20px;">
配置范例</h3>
<pre style="overflow:auto;font-family:Menlo, Monaco, Consolas, 'Courier New', monospace;line-height:1;color:rgb(51,51,51);border-width:1px;border-style:solid;border-color:rgb(204,204,204);"><code style="font-family:'Source Code Pro', Consolas, Menlo, Monaco, 'Courier New', monospace;color:inherit;background-color:transparent;">&lt;!--
   Do row-based indexing on table "table1", never re-reading updated content.
   Store the unique document id in Solr field called "custom-id".
   Additionally store the row key in a Solr field called "custom-row", and store the 
   column family in a Solr field called "custom-family".

   Perform conversion of byte array keys using the class "com.mycompany.MyKeyFormatter".
--&gt; 
&lt;indexer
    table="table1"
    mapping-type="row"
    read-row="never"
    unique-key-field="custom-id"
    row-field="custom-row"
    column-family-field="custom-family"
    table-name-field="custom-table"
    unique-key-formatter="com.mycompany.MyKeyFormatter"
    &gt;

  &lt;!-- A float-based field taken from any qualifier in the column family "colfam" --&gt;
  &lt;field name="field1" value="colfam:*" source="qualifier" type="float"/&gt;

  &lt;param name="globalKeyA" value="globalValueA"/&gt;
  &lt;param name="globalKeyB" value="globalValueB"/&gt;

&lt;/indexer&gt;r
</code></pre>
<h2 id="toc_19" style="font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;line-height:1.1;color:rgb(51,51,51);font-size:24px;">
数据批量导入</h2>
<p>批量导入已存在的 HBase 表数据，HBase-Indexer提供了MR工具，来完成批量导入功能</p>
<p>直接导入</p>
<pre style="overflow:auto;font-family:Menlo, Monaco, Consolas, 'Courier New', monospace;line-height:1;color:rgb(51,51,51);border-width:1px;border-style:solid;border-color:rgb(204,204,204);"><code style="font-family:'Source Code Pro', Consolas, Menlo, Monaco, 'Courier New', monospace;color:inherit;background-color:transparent;">sudo -u hdfs hadoop jar hbase-indexer-mr-1.5-cdh5.5.1-job.jar --hbase-indexer-zk hadoop-02:2181,hadoop-03:2181,hadoop-05:2181 --hbase-indexer-name index_test_content_k --reducers 0
</code></pre>
</div>
</div>
<div class="share-comment">
<div id="SOHUCS" style="clear:both;border:0px;overflow:visible;font-size:12px;color:rgb(51,51,51);line-height:1;">
<div id="SOHU_MAIN" style="border:0px;overflow:visible;line-height:1;">
<div class="module-cmt-header" style="border:0px;vertical-align:baseline;overflow:visible;">
<div class="clear-g section-title-w" style="border:0px;vertical-align:baseline;overflow:visible;z-index:12;font-family:'PingFang SC', 'Lantinghei SC', 'Lucida Grande', '微软雅黑', 'Microsoft YaHei', FreeSans, 'WenQuanYi Micro Hei', 'Hiragino Sans GB', 'Hiragino Sans GB W3', SimSun, sans-serif, tahoma, arial;background-color:rgb(255,255,255);">
<div class="title-user-w" style="border:0px;vertical-align:baseline;overflow:visible;">
<div class="clear-g user-wrap-w" style="border:0px;vertical-align:baseline;overflow:visible;text-indent:11px;z-index:10;">
<span class="wrap-name-w" style="border:0px;font-size:16px;vertical-align:baseline;overflow:visible;text-indent:0px;display:inline;color:rgb(67,152,237);font-family:'Microsoft YaHei';z-index:2;"></span></div>
</div>
</div>
<div class="section-cbox-w" style="border:0px;vertical-align:baseline;overflow:visible;font-family:'PingFang SC', 'Lantinghei SC', 'Lucida Grande', '微软雅黑', 'Microsoft YaHei', FreeSans, 'WenQuanYi Micro Hei', 'Hiragino Sans GB', 'Hiragino Sans GB W3', SimSun, sans-serif, tahoma, arial;background-color:rgb(255,255,255);">
<div class="cbox-block-w clear-g" style="border:0px;vertical-align:baseline;overflow:visible;">
<div class="block-head-w" style="border:0px;vertical-align:baseline;overflow:visible;z-index:10;">
参考:</div>
<div class="block-head-w" style="border:0px;vertical-align:baseline;overflow:visible;z-index:10;">
http://www.niuchaoqun.com/14543825447680.html<br></div>
</div>
</div>
</div>
</div>
</div>
</div>
            </div>
                </div>