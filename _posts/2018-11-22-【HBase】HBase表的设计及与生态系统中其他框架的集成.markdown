---
layout:     post
title:      【HBase】HBase表的设计及与生态系统中其他框架的集成
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，转载请注明出处。					https://blog.csdn.net/gongxifacai_believe/article/details/81395995				</div>
								            <div id="content_views" class="markdown_views prism-atelier-sulphurpool-light">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2><a id="1_0"></a>1、创建表</h2>
<p>（1）创建ns1命名空间下的t1表，列簇为f1，指定列的最大版本数为5：</p>
<pre><code>hbase&gt; create 'ns1:t1', {NAME =&gt; 'f1', VERSIONS =&gt; 5}
</code></pre>
<p>（2）创建t1表，拥有三个列簇f1、f2和f3：</p>
<pre><code> hbase&gt; create 't1', {NAME =&gt; 'f1'}, {NAME =&gt; 'f2'}, {NAME =&gt; 'f3'}
</code></pre>
<p>上面语句等价于如下简写形式：</p>
<pre><code>hbase&gt; create 't1', 'f1', 'f2', 'f3'
</code></pre>
<p>（3）创建t1表，列簇为f1，最大版本数为1，表的生存时间为2592000，使用blockcache缓存hbase表中读写的数据：</p>
<pre><code>hbase&gt; create 't1', {NAME =&gt; 'f1', VERSIONS =&gt; 1, TTL =&gt; 2592000, BLOCKCACHE =&gt; true}
</code></pre>
<p>（4）对表进行预分区：</p>
<blockquote>
<p>默认情况的情况下，创建一个HBase表，自动为表分配一个Region。结合实际使用来看，无论是在测试环境还生产环境，我们创建好HBase一张表以后，需要往表中导入大量的数据。file/datas 生成 hfile 用 bulk load 工具导入到 hbase table中。<br>
而当短时间导入大量数据时，RegionServer会负载过大，因此我们需要把数据拆分，插入不同的region中，而我们怎么知道要将哪些数据插入哪个region中呢 ？<br>
解决方案：创建表时，多创建一些Region（结合业务，依据表的rowkey进行设计）被多个RegionServer进行管理，这样，在插入数据时，会向多个Region中分别插入对应的数据，RegionServer的负载就均衡了。而在创建表的时候，预先创建一些Region，叫做HBase 表的预分区。Region划分，依赖于rowkey，需要预先预估一些rowkey。</p>
</blockquote>
<pre><code> hbase&gt; create 'ns1:t1', 'f1', SPLITS =&gt; ['10', '20', '30', '40']   用指定rowkey值预分区
 hbase&gt; create 't1', 'f1', SPLITS_FILE =&gt; 'splits.txt', OWNER =&gt; 'johndoe'    在文件中写入rowkey值预分区
 hbase&gt; create 't1', 'f1', {NUMREGIONS =&gt; 15, SPLITALGO =&gt; 'HexStringSplit'}   指定region数为15个，用类来预分区
</code></pre>
<p>（5）针对namespace的操作：</p>
<pre><code>Group name: namespace
  Commands: alter_namespace, create_namespace, describe_namespace, drop_namespace, list_namespace, list_namespace_tables
</code></pre>
<h2><a id="2RowKey_34"></a>2、RowKey的设计</h2>
<p>如何在海量数据中，获取我所需要的数据（查询的数据）？<br>
核心思想：依据rowkey查询最快，考虑对rowkey进行范围查询，在不同的region中查询，考虑rowkey的前缀匹配。<br>
rowkey的设计示例：<br>
（1）telphone  +  （starttime - endtime）；<br>
（2）telphone + time；<br>
可以设计索引表，索引表的：<br>
rowkey：phone_time<br>
列簇：info<br>
列：主表的rowkey<br>
然后使用get方式查询数据，get方式是最快的数据查询。</p>
<blockquote>
<p>主表和索引表的数据如何同步？</p>
<ol>
<li>写程序，事务控制</li>
<li>phoenix工具，只有JDBC方式才能同步</li>
<li>用solr创建索引表，常用工具有lily和cloudera公司的search工具。</li>
</ol>
</blockquote>
<h2><a id="3HBase_Snappy_52"></a>3、配置HBase Snappy压缩</h2>
<p>（1）Haodop支持的压缩格式：<br>
<code>[beifeng@hadoop-senior hadoop-2.5.0]$ bin/hadoop checknative</code><br>
（2）配置HBase<br>
<code>$ export HBASE_HOME=/opt/modules/hbase-0.98.6-cdh5.3.6 $ export HADOOP_SNAPPY_HOME=/opt/modules/hadoop-snappy-0.0.1-SNAPSHOT $ export HADOOP_HOME=/opt/modules/hadoop-2.5.0-cdh5.3.6 $ cp $HADOOP_SNAPPY_HOME/lib/hadoop-snappy-0.0.1-SNAPSHOT.jar $HBASE_HOME/lib $ mkdir $HBASE_HOME/lib/native $ ln -s $HADOOP_HOME/lib/native $HBASE_HOME/lib/native/Linux-amd64-64</code><br>
（3）在hbase-site.xml中设置如下属性：</p>
<pre><code>&lt;property&gt;
	&lt;name&gt;hbase.regionserver.codecs&lt;/name&gt;
	&lt;value&gt;snappy&lt;/value&gt;
&lt;/property&gt;
</code></pre>
<p>（4）在hbase中创建表时指定压缩格式为snappy：</p>
<pre><code>hbase(main):001:0&gt; create 'test', {NAME =&gt; 'cf', COMPRESSION =&gt; 'SNAPPY'}
</code></pre>
<h2><a id="4Memstore__BlockCache_76"></a>4、Memstore 与 BlockCache</h2>
<p>（1）Memstore<br>
HBase上Regionserver的内存分为两个部分，一部分作为Memstore，主要用来写；另外一部分作为BlockCache，主要用于读。<br>
写请求会先写入Memstore，Regionserver会给每个region提供一个Memstore，当Memstore满64MB以后，会启动 flush刷新到磁盘。当Memstore的总大小超过限制时（heapsize * hbase.regionserver.global.memstore.upperLimit * 0.9），会强行启动flush进程，从最大的Memstore开始flush直到低于限制。<br>
（2）BlockCache<br>
将Cache分级思想的好处在于：<br>
首先，通过inMemory类型Cache，可以有选择地将in-memory的column families放到RegionServer内存中，例如Meta元数据信息；通过区分Single和Multi类型Cache，可以防止由于Scan操作带来的Cache频繁颠簸，将最少使用的Block加入到淘汰算法中。默认配置下，对于整个BlockCache的内存，又按照以下百分比分配给Single、Multi、InMemory使用：0.25、0.50和0.25。其中InMemory队列用于保存HBase Meta表元数据信息，因此如果将数据量很大的用户表设置为InMemory的话，可能会导致Meta表缓存失效，进而对整个集群的性能产生影响。<br>
（3）Memstore 与 BlockCache的配合</p>
<blockquote>
<p>在内存中， MemStore大约占据40%的内存，主要用于写入数据，BlockCache大约占据40%的内存，主要用于读取数据，其他大约占据20%的内存。<br>
当用户去读取数据，首先查询MemStore，然后查询BlockCache，每个RegionServer只有一个BlockCache，最后到HDFS中的hfile中查询，然后把查询到的数据进行合并，返回数据集。</p>
</blockquote>
<p>读请求先到Memstore中查数据，查不到就到BlockCache中查，再查不到就会到磁盘上读，并把读的结果放入BlockCache。由于BlockCache采用的是LRU策略，因此BlockCache达到上限(heapsize * hfile.block.cache.size * 0.85)后，会启动淘汰机制，淘汰掉最老的一批数据。在注重读响应时间的应用场景下，可以将 BlockCache设置大些，Memstore设置小些，以加大缓存的命中率。<br>
<img src="https://img-blog.csdn.net/20180803223324249?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2dvbmd4aWZhY2FpX2JlbGlldmU=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt=""><br>
（4）Memstore和BlockCache的参数设置</p>

<table>
<thead>
<tr>
<th>属性</th>
<th>值</th>
<th>说明</th>
</tr>
</thead>
<tbody>
<tr>
<td>RegionServer中所有MemStore的最大大小</td>
<td>hbase.regionserver.global.memstore.upperLimit=0.4（默认值）</td>
<td>阻止新更新和强迫刷新前，RegionServer的所有memstore的最大大小</td>
</tr>
<tr>
<td>Memstore刷新的低水位线</td>
<td>hbase.regionserver.global.memstore.lowerLimit=0.38（默认值）</td>
<td>当memstores被迫刷新以节省内存时，请一直刷新直到达到此数量，如此数量等于hbase.regionserver.global.memstore.upperLimit，则由于memstore限制，阻止更新时，可能会最低限度地进行刷新</td>
</tr>
<tr>
<td>HBase Memstore刷新大小</td>
<td>hbase.hregion.memstore.flush.size=128MB（默认值）</td>
<td>如memstore大小超过此值（字节数），Memstore将刷新到磁盘，通过运行由hbase.server.thread.wakefrequency指定的频率的线程检查此值</td>
</tr>
<tr>
<td>HFile块缓存大小</td>
<td>hfile.block.cache.size=0.4（默认值）</td>
<td>用于阻止HFile/StoreFile使用的缓存所分配的最大堆（-Xmx设置）的百分比。要禁用，请将此值设置为0。</td>
</tr>
</tbody>
</table><h2><a id="5HBase_99"></a>5、HBase表管理</h2>
<p>（1）HRegoin Server上的storefile文件是被后台线程监控的，以确保这些文件保持在可控状态。磁盘上的storefile的数量会随着越来越多的memstore被刷新而变得越来越多——每次刷新都会生成一个storefile文件。当storefile数量满足一定条件时（可以通过配置参数类调整），会触发文件合并操作——minor compaction，将多个比较小的storefile合并成一个大的storefile文件，直到合并的文件大到超过单个文件配置允许的最大值时会触发一次region的自动分割，即region split操作，将一个region平分成2个。<br>
<strong>（2）minor compaction，轻量级</strong><br>
minor compaction将符合条件的最早生成的几个storefile合并生成一个大的storefile文件，它不会删除被标记为“删除”的数据和以过期的数据，并且执行过一次minor合并操作后还会有多个storefile文件。<br>
<strong>（3）major compaction，重量级</strong><br>
major compaction把所有的storefile合并成一个单一的storefile文件，在文件合并期间系统会删除标记为"删除"标记的数据和过期失效的数据，同时会block所有客户端对该操作所属的region的请求直到合并完毕，最后删除已合并的storefile文件。</p>
<h2><a id="6HBaseHive_108"></a>6、HBase与Hive的集成</h2>
<p>数据存储在HBase中，hive 表的描述信息存储在hive中。Hive就是HBase客户端。<br>
HBase与Hive集成的两种方式：<br>
<strong>（1）管理表</strong><br>
创建hive表的时候，指定数据存储在hbase表中。</p>
<pre><code>CREATE TABLE hbase_table_1(key int, value string) 
STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
WITH SERDEPROPERTIES ("hbase.columns.mapping" = ":key,cf1:val")
TBLPROPERTIES ("hbase.table.name" = "xyz");

</code></pre>
<p><strong>（2）外部表</strong><br>
现在已经存在一个HBase表，需要对表中数据进行分析。</p>
<pre><code>CREATE EXTERNAL TABLE hbase_user(id int, name string,age int) 
STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
WITH SERDEPROPERTIES ("hbase.columns.mapping" = ":key,info:name,info:age")
TBLPROPERTIES ("hbase.table.name" = "user");
</code></pre>
<p>需要导入的jar包：</p>
<pre><code>export HBASE_HOME=/opt/modules/hbase-0.98.6-hadoop2
export HIVE_HOME=/opt/modules/hive-0.13.1/lib
ln -s $HBASE_HOME/lib/hbase-server-0.98.6-hadoop2.jar $HIVE_HOME/hbase-server-0.98.6-hadoop2.jar
ln -s $HBASE_HOME/lib/hbase-client-0.98.6-hadoop2.jar $HIVE_HOME/hbase-client-0.98.6-hadoop2.jar
ln -s $HBASE_HOME/lib/hbase-protocol-0.98.6-hadoop2.jar $HIVE_HOME/hbase-protocol-0.98.6-hadoop2.jar
ln -s $HBASE_HOME/lib/hbase-it-0.98.6-hadoop2.jar $HIVE_HOME/hbase-it-0.98.6-hadoop2.jar 
ln -s $HBASE_HOME/lib/htrace-core-2.04.jar $HIVE_HOME/htrace-core-2.04.jar
ln -s $HBASE_HOME/lib/hbase-hadoop2-compat-0.98.6-hadoop2.jar $HIVE_HOME/lib/hbase-hadoop2-compat-0.98.6-hadoop2.jar
ln -s $HBASE_HOME/lib/hbase-hadoop-compat-0.98.6-hadoop2.jar $HIVE_HOME/lib/hbase-hadoop-compat-0.98.6-hadoop2.jar
ln -s /opt/modules/hbase-0.98.6-hadoop2/lib/high-scale-lib-1.1.1.jar /opt/modules/hive-0.13.1/lib/high-scale-lib-1.1.1.jar
</code></pre>
<h2><a id="7HBaseSqoop_144"></a>7、HBase与Sqoop的集成</h2>
<p>使用命令如下：</p>
<pre><code>$ sqoop import \
	-- connect jdbc:mysql://localhost/serviceorderdb \
	-- username root -P \
	-- table customercontactinfo \
	-- columns "customernum, contactinfo" \
	-- hbase-table customercontactinfo \
	-- column-family ContactInfo \
	-- hbase-row-key customernum -m 1
</code></pre>
<h2><a id="8HBaseHue_159"></a>8、HBase与Hue的集成</h2>
<p><strong>（1）Thrift工具</strong><br>
Thrift所在的包：<code>org.apache.hadoop.hbase.thrift</code><br>
启动thriftserver：<code>$ bin/hbase-daemon.sh start thrift</code><br>
停止thriftserver：<code>$ bin/hbase-daemon.sh stop thrift</code><br>
（2）编辑配置文件/opt/app/hue-3.7.0-cdh5.3.6/desktop/conf/hue.ini</p>
<pre><code>[hbase]
hbase_clusters=(Cluster|hadoop-senior.ibeifeng.com:9090)
hbase_conf_dir=/opt/modules/hbase-0.98.6-hadoop2/conf
</code></pre>
<p>（3）重新启动hbase及hue即可。</p>
<h2><a id="9HBase_174"></a>9、HBase的完全分布式环境搭建</h2>
<p>（1）HBase集群的设计</p>

<table>
<thead>
<tr>
<th>Node Name</th>
<th>Master</th>
<th>ZooKeeper</th>
<th>RegionServer</th>
</tr>
</thead>
<tbody>
<tr>
<td><a href="http://hadoop-senior.ibeifeng.com" rel="nofollow">hadoop-senior.ibeifeng.com</a></td>
<td>yes</td>
<td>yes</td>
<td>no</td>
</tr>
<tr>
<td><a href="http://hadoop-senior02.ibeifeng.com" rel="nofollow">hadoop-senior02.ibeifeng.com</a></td>
<td>backup</td>
<td>yes</td>
<td>yes</td>
</tr>
<tr>
<td><a href="http://hadoop-senior03.ibeifeng.com" rel="nofollow">hadoop-senior03.ibeifeng.com</a></td>
<td>no</td>
<td>yes</td>
<td>yes</td>
</tr>
</tbody>
</table><p>（2）编辑配置文件/opt/modules/hbase-0.98.6-hadoop2/conf/hbase-site.xml。</p>
<pre><code>&lt;configuration&gt;
	&lt;property&gt;
		&lt;name&gt;hbase.rootdir&lt;/name&gt;
		&lt;value&gt;hdfs://hadoop-senior.ibeifeng.com:8020/hbase&lt;/value&gt;
	&lt;/property&gt;
	&lt;property&gt;
		&lt;name&gt;hbase.cluster.distributed&lt;/name&gt;
		&lt;value&gt;true&lt;/value&gt;
	&lt;/property&gt;
	&lt;property&gt;
		&lt;name&gt;hbase.zookeeper.quorum&lt;/name&gt;
		&lt;value&gt;hadoop-senior.ibeifeng.com,hadoop-senior02.ibeifeng.com,hadoop-senior03.ibeifeng.com&lt;/value&gt;
	&lt;/property&gt;
&lt;/configuration&gt;
</code></pre>
<p>（3）编辑配置文件/opt/modules/hbase-0.98.6-hadoop2/conf/regionservers。</p>
<pre><code>hadoop-senior.ibeifeng.com
hadoop-senior02.ibeifeng.com
hadoop-senior03.ibeifeng.com
</code></pre>
<p>（4）编辑配置文件/opt/modules/hbase-0.98.6-hadoop2/conf/backup-masters。</p>
<pre><code>hadoop-senior02.ibeifeng.com
hadoop-senior03.ibeifeng.com
</code></pre>
<h2><a id="10_214"></a>10、电商网站订单查询实例</h2>
<p>使用数据库：<br>
（1）RDBMS，无法满足海量数据实时查询，保存未完成订单，每个用户没有大量数据；<br>
（2）NoSQL，海量数据存储准实时查询 — HBase，保存已完成订单，每个用户都有大量数据。<br>
数据分开存储优势：<br>
（1）分开存储，减轻在线订单库压力；<br>
（2）统一各个系统对历史订单的查询；<br>
（3）灵活支撑各种维度历史订单的条件查询。</p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>