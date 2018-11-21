---
layout:     post
title:      cdh-配置其他CDH组件以使用HDFS HA
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="配置其他cdh组件以使用hdfs-ha">配置其他CDH组件以使用HDFS HA</h1>

<p>可以将HDFS高可用性NameNodes与CDH的其他组件一起使用。</p>



<h2 id="配置hbase以使用hdfs-ha">配置HBase以使用HDFS HA</h2>



<h3 id="配置hbase以使用hdfs-ha使用cloudera-manager">配置HBase以使用HDFS HA使用Cloudera Manager</h3>

<p>如果您将HBase配置为使用启用HA的HDFS实例，则Cloudera Manager会自动为您处理HA配置。</p>



<h3 id="使用命令行配置hbase以使用hdfs-ha">使用命令行配置HBase以使用HDFS HA</h3>

<p>要配置HBase使用HDFS HA，请按以下步骤操作。 <br>
1.关闭HBase群集 <br>
1.1停止Thrift服务器和客户端：</p>



<pre class="prettyprint"><code class=" hljs bash"><span class="hljs-built_in">sudo</span> service hbase-thrift stop</code></pre>

<p>1.2通过关闭Master和RegionServers来停止集群： <br>
在主控主机上使用以下命令：</p>



<pre class="prettyprint"><code class=" hljs bash"><span class="hljs-built_in">sudo</span> service hbase-master stop</code></pre>

<p>在承载RegionServer的每个主机上使用以下命令：</p>



<pre class="prettyprint"><code class=" hljs bash"><span class="hljs-built_in">sudo</span> service hbase-regionserver stop</code></pre>

<p>2.配置hbase.rootdir <br>
将hbase-site.xml中的分布式文件系统URI更改为hdfs-site.xml中dfs.nameservices属性中指定的名称。客户端还必须能够访问hdfs-site.xml的dfs.client。*设置才能正确使用HA。</p>

<p>例如，假设HDFS高可用性属性dfs.nameservices在hdfs-site.xml中设置为ha-nn。要将HBase配置为使用HA NameNode，请将相同的值指定为hbase-site.xml的hbase.rootdir值的一部分：</p>



<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-comment">&lt;!-- Configure HBase to use the HA NameNode nameservice --&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>hbase.rootdir<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>hdfs://ha-nn/hbase<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span></code></pre>

<p>3.重新启动HBase <br>
启动HBase Master。 <br>
启动每个HBase RegionServers。 <br>
4.HBase-HDFS HA故障排除 <br>
问题：HMasters无法启动。</p>

<p>解决方案：检查HMaster日志中的此错误：</p>



<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-number">2012</span>-<span class="hljs-number">05</span>-<span class="hljs-number">17</span> <span class="hljs-number">12</span>:<span class="hljs-number">21</span>:<span class="hljs-number">28</span>,<span class="hljs-number">929</span> FATAL master<span class="hljs-preprocessor">.HMaster</span> (HMaster<span class="hljs-preprocessor">.java</span>:abort(<span class="hljs-number">1317</span>)) - Unhandled exception. Starting shutdown.
<span class="hljs-label">java.lang.IllegalArgumentException:</span> java<span class="hljs-preprocessor">.net</span><span class="hljs-preprocessor">.UnknownHostException</span>: ha-nn
        at org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.security</span><span class="hljs-preprocessor">.SecurityUtil</span><span class="hljs-preprocessor">.buildTokenService</span>(SecurityUtil<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">431</span>)
        at org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.NameNodeProxies</span><span class="hljs-preprocessor">.createNonHAProxy</span>(NameNodeProxies<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">161</span>)
        at org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.NameNodeProxies</span><span class="hljs-preprocessor">.createProxy</span>(NameNodeProxies<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">126</span>)
...</code></pre>

<p>如果是这样，请确认Hadoop的hdfs-site.xml和core-site.xml文件位于hbase / conf目录中。如果你把你的配置放在非标准的地方，这可能是必要的。</p>



<h2 id="升级hive-metastore以使用hdfs-ha">升级Hive Metastore以使用HDFS HA</h2>

<p>Hive Metastore可以配置为使用HDFS高可用性。</p>



<h3 id="使用cloudera-manager升级hive-metastore以使用hdfs-ha">使用Cloudera Manager升级Hive Metastore以使用HDFS HA</h3>

<p>1.去Hive服务。 <br>
2.选择操作&gt;停止。 <br>
注意：您可能需要先停止Hue和Impala服务（如果存在），因为它们依赖于Hive服务。 <br>
  单击停止以确认该命令。 <br>
3.备份Hive Metastore数据库。 <br>
4.选择“操作”&gt;“更新Hive Metastore NameNodes”并确认该命令。 <br>
5.选择操作&gt;开始。 <br>
如果在更新Metastore之前停止了它们，请重新启动Hue和Impala服务。</p>



<h3 id="使用命令行升级hive-metastore以使用hdfs-ha">使用命令行升级Hive Metastore以使用HDFS HA</h3>

<p>重要： <br>
在不使用Cloudera Manager的系统上遵循这些命令行指示信息。 此信息特别适用于CDH 5.11.x.有关其他版本的信息，请参阅Cloudera文档。</p>

<p>要配置Hive Metastore以使用HDFS HA，请更改记录以反映在dfs.nameservices属性中指定的位置，使用Hive metatool获取和更改位置。 <br>
注意：在尝试升级Hive Metastore以使用HDFS HA之前，请关闭Metastore并将其备份到持久存储区。</p>

<p>如果您不确定使用哪个版本的Avro SerDe，请同时使用serdePropKey和tablePropKey参数。例如：</p>



<pre class="prettyprint"><code class=" hljs avrasm">$ hive --service metatool -listFSRoot
.
.
.
<span class="hljs-label">hdfs:</span>//&lt;oldnamenode&gt;<span class="hljs-preprocessor">.com</span>/user/hive/warehouse

$ hive --service metatool -updateLocation hdfs://&lt;new_nameservice1&gt;
<span class="hljs-label">hdfs:</span>//&lt;oldnamenode&gt;<span class="hljs-preprocessor">.com</span> -tablePropKey &lt;avro<span class="hljs-preprocessor">.schema</span><span class="hljs-preprocessor">.url</span>&gt; 
-serdePropKey &lt;schema<span class="hljs-preprocessor">.url</span>&gt;
.
.
.
$ hive --service metatool -listFSRoot
.
.
.
<span class="hljs-label">hdfs:</span>//nameservice1/user/hive/warehouse</code></pre>

<p>其中： <br>
hdfs://oldnamenode.com/user/hive/warehouse 标识NameNode的位置 <br>
hdfs://nameservice1 指定新的位置并且应该匹配dfs.nameservices属性的值。 <br>
tablePropKey是一个表属性键，其值字段可能引用HDFS NameNode位置，因此可能需要更新。要更新Avro SerDe模式URL，请为此参数指定avro.schema.url。 <br>
serdePropKey是SerDe属性键，其值域可能引用HDFS NameNode位置，因此可能需要更新。要更新Haivvero模式URL，请为此参数指定schema.url。 <br>
注意：Hive metatool是尽力更新尽可能多的Hive Metastore记录的尽力服务。如果在记录更新期间遇到错误，则跳到下一条记录。</p>



<h2 id="使用cloudera-manager将hue配置为使用hdfs-ha">使用Cloudera Manager将Hue配置为使用HDFS HA</h2>

<p>1添加HttpFS角色。 <br>
2命令完成后，转到Hue服务。 <br>
3单击配置选项卡。 <br>
4找到HDFS Web界面角色属性或通过在搜索框中键入其名称来搜索它。 <br>
5选择您刚创建的HttpFS角色而不是NameNode角色，然后保存更改。 <br>
6重新启动Hue服务。</p>



<h2 id="配置impala以使用hdfs-ha">配置Impala以使用HDFS HA</h2>

<p>1如上一节所述，完成重新配置Hive Metastore数据库的步骤。 Impala与Hive共享相同的底层数据库，以管理数据库，表格等的元数据。 <br>
2从Impala shell发出INVALIDATE METADATA语句。此一次性操作使群集中的所有Impala守护程序都知道Hive Metastore数据库的最新设置。或者，重新启动Impala服务。</p>



<h2 id="配置oozie以使用hdfs-ha">配置Oozie以使用HDFS HA</h2>

<p>要将Oozie工作流配置为使用HDFS HA，请使用HDFS名称服务，而不是工作流的元素中的NameNode URI。 <br>
例如：</p>



<pre class="prettyprint"><code class=" hljs lasso"><span class="hljs-subst">&lt;</span>action name<span class="hljs-subst">=</span><span class="hljs-string">"mr-node"</span><span class="hljs-subst">&gt;</span>
  <span class="hljs-subst">&lt;</span><span class="hljs-built_in">map</span><span class="hljs-attribute">-reduce</span><span class="hljs-subst">&gt;</span>
    <span class="hljs-subst">&lt;</span>job<span class="hljs-attribute">-tracker</span><span class="hljs-subst">&gt;</span>${jobTracker}<span class="hljs-subst">&lt;</span>/job<span class="hljs-attribute">-tracker</span><span class="hljs-subst">&gt;</span>
    <span class="hljs-subst">&lt;</span>name<span class="hljs-attribute">-node</span><span class="hljs-subst">&gt;</span>hdfs:<span class="hljs-comment">//ha-nn</span></code></pre>

<p>其中ha-nn是hdfs-site.xml中dfs.nameservices的值。</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>