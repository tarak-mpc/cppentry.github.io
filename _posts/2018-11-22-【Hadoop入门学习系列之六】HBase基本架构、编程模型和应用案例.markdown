---
layout:     post
title:      【Hadoop入门学习系列之六】HBase基本架构、编程模型和应用案例
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为生命奇迹泉原创文章，转载请注明出处生命奇迹泉http://blog.csdn.net/shengmingqijiquan					https://blog.csdn.net/shengmingqijiquan/article/details/52922009				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2 id="一hbase概述">一.HBase概述</h2>

<p>HBase是一个构建在HDFS上的<strong>分布式列存储系统</strong>；</p>

<p>HBase是Apache Hadoop生态系统中的重要一员，主要用于<strong>海量结构化数据存储</strong>；</p>

<p>从逻辑上讲， HBase将数据按照<strong>表</strong>、<strong>行</strong>和<strong>列</strong>进行存储。</p>

<p>Hbase是Hadoop生态系统的一个组成部分 <br>
<img src="https://img-blog.csdn.net/20161026100500055" alt="这里写图片描述" title=""></p>

<p><strong>Hbase与HDFS对比</strong></p>

<ul>
<li>共同点： <br>
两者都具有良好的容错性和扩展性，都可以扩展到成百上千个节点；</li>
<li>不同点： <br>
HDFS： <br>
<strong>适合批处理场景</strong> <br>
<strong>不支持数据随机查找</strong> <br>
<strong>不适合增量数据处理</strong> <br>
<strong>不支持数据更新</strong> <br>
HBase： <br>
<strong>大：</strong>一个表可以有数十亿行，上百万列； <br>
<strong>无模式：</strong>每行都有一个可排序的主键和任意多的列，列可以根据需要动态的增加，同一张表中不同的行可以有截然不同的列； <br>
<strong>面向列：</strong>面向列（族）的存储和权限控制，列（族）独立检 <br>
索； <br>
<strong>稀疏：</strong>对于空（ null）的列，并不占用存储空间，表可以设计 <br>
的非常稀疏； <br>
<strong>数据多版本：</strong>每个单元中的数据可以有多个版本，默认情况 <br>
下版本号自动分配，是单元格插入时的时间戳； <br>
<strong>数据类型单一：</strong> Hbase中的数据都是字符串，没有类型</li>
</ul>

<p>行存储与列存储 <br>
<img src="https://img-blog.csdn.net/20161026101413380" alt="这里写图片描述" title=""></p>

<hr>



<h2 id="二hbase模型和基本架构">二.HBase模型和基本架构</h2>



<h3 id="21-hbase数据模型">2.1 HBase数据模型</h3>

<p>HBase是基于Google BigTable模型开发的，典型的key/value系统； <br>
<img src="https://img-blog.csdn.net/20161026101629318" alt="这里写图片描述" title=""> <br>
Hbase逻辑视图 <br>
<img src="https://img-blog.csdn.net/20161026101804665" alt="这里写图片描述" title=""> <br>
Rowkey与Column Family <br>
<img src="https://img-blog.csdn.net/20161026102002727" alt="这里写图片描述" title=""> <br>
Hbase支持的操作 <br>
所有操作均是基于<strong>rowkey</strong>的； <br>
支持CRUD（ Create、 Read、 Update和Delete）和Scan； <br>
单行操作 <br>
 Put <br>
 Get <br>
 Scan <br>
 多行操作 <br>
 Scan <br>
 MultiPut <br>
 没有内置join操作，可使用MapReduce解决。</p>



<h3 id="22-hbase物理模型">2.2 HBase物理模型</h3>

<ul>
<li>每个column family存储在HDFS上的一个单独文件中；</li>
<li>Key 和 Version number在每个 column family中均由一份；</li>
<li>空值不会被保存。</li>
</ul>

<p>Table中的所有行都按照row key的字典序排列；Table 在行的方向上分割为多个Region； <br>
<img src="https://img-blog.csdn.net/20161026102751230" alt="这里写图片描述" title=""> <br>
Region按大小分割的，每个表开始只有一个region，随着数据增多， region不断增大，当增大到一个阀值的时候，region就会等分会两个新的region，之后会有越来越多的region； <br>
<img src="https://img-blog.csdn.net/20161026103851375" alt="这里写图片描述" title=""> <br>
Region是HBase中分布式存储和负载均衡的最小单元。不同Region分布到不同RegionServer上； <br>
<img src="https://img-blog.csdn.net/20161026103956109" alt="这里写图片描述" title=""> <br>
Region虽然是分布式存储的最小单元，但并不是存储的最小单元。Region由一个或者多个Store组成，每个store保存一个columns family；每个Strore又由一个memStore和0至多个StoreFile组成；memStore存储在内存中， StoreFile存储在HDFS上。 <br>
<img src="https://img-blog.csdn.net/20161026104231010" alt="这里写图片描述" title=""></p>



<h3 id="23-基本架构">2.3 基本架构</h3>

<p><img src="https://img-blog.csdn.net/20161026142605159" alt="Hbase架构" title=""></p>



<h4 id="231hbase基本组件">2.3.1Hbase基本组件</h4>

<ul>
<li><strong>Client</strong> <br>
包含访问HBase的接口，并维护cache来加快对HBase的访问</li>
<li><strong>Zookeeper</strong> <br>
保证任何时候，集群中只有一个master <br>
存贮所有Region的寻址入口 <br>
实时监控Region server的上线和下线信息。并实时通知给Master <br>
存储HBase的schema和table元数据 <br>
<strong>Master</strong> <br>
为Region server分配region <br>
负责Region server的负载均衡 <br>
发现失效的Region server并重新分配其上的region <br>
管理用户对table的增删改查操作 <br>
<strong>Region Server</strong> <br>
Region server维护region，处理对这些region的IO请求 <br>
Region server负责切分在运行过程中变得过大的region</li>
</ul>



<h4 id="232-zookeeper作用">2.3.2 Zookeeper作用</h4>

<ul>
<li>HBase 依赖ZooKeeper</li>
<li>默认情况下,HBase管理ZooKeeper实例,比如， 启动或者停止ZooKeeper</li>
<li>Master与RegionServers 启动时会向ZooKeeper注 册</li>
<li><strong>Zookeeper的引入使得 Master不再是单点故障</strong> <br>
<img src="https://img-blog.csdn.net/20161026143823529" alt="这里写图片描述" title=""></li>
</ul>



<h3 id="24hbase容错性">2.4HBase容错性</h3>

<ul>
<li>Master容错： Zookeeper重新选择一个新的Master <br>
无Master过程中，数据读取仍照常进行； <br>
无master过程中， region切分、负载均衡等无法进行；</li>
<li>RegionServer容错：定时向Zookeeper汇报心跳，如果一旦时 间内未出现心跳 <br>
Master将该RegionServer上的Region重新分配到其他RegionServer上； <br>
失效服务器上“预写”日志由主服务器进行分割并派送给新的 <br>
RegionServer</li>
<li>Zookeeper容错： Zookeeper是一个可靠地服务 <br>
一般配置3或5个Zookeeper实例。</li>
</ul>



<h2 id="三hbase应用举例">三.HBase应用举例</h2>



<h3 id="31何时使用hbase">3.1何时使用HBase?</h3>

<ul>
<li><strong>需对数据进行随机读操作或者随机写操作；</strong></li>
<li><strong>大数据上高并发操作，比如每秒对PB级数据进行上千次操作；</strong></li>
<li><strong>读写访问均是非常简单的操作。</strong></li>
</ul>



<h3 id="32-hbase应用企业">3.2 HBase应用企业</h3>

<p><img src="https://img-blog.csdn.net/20161026144510898" alt="这里写图片描述" title=""> <br>
<img src="https://img-blog.csdn.net/20161026144606587" alt="淘宝指数" title=""></p>



<h2 id="四hbase编程实战">四.HBase编程实战</h2>



<h3 id="41-hbase-访问方式">4.1 Hbase 访问方式</h3>

<ol>
<li><strong>Native Java API</strong> <br>
 最常规和高效的访问方式；</li>
<li><strong>HBase Shell</strong> <br>
 HBase的命令行工具，最简单的接口，适合HBase管理使用；</li>
<li><strong>Thrift Gateway</strong> <br>
 利用Thrift序列化技术，支持C++，PHP，Python等多种语言，适合其他异构系统在线访问HBase表数据；</li>
<li><strong>REST Gateway</strong> <br>
 支持REST 风格的Http API访问HBase, 解除了语言限制；</li>
<li><strong>MapReduce</strong> <br>
 直接使用MapReduce作业处理Hbase数据； <br>
 使用Pig/hive处理Hbase数据</li>
</ol>



<h3 id="42-hbase-java编程">4.2 Hbase Java编程</h3>



<h4 id="421-hbase-java-api概述">4.2.1 Hbase Java API概述</h4>

<ol>
<li>Hbase是用Java语言编写的，支持Java编程是自然而然的事情；</li>
<li>支持CRUD操作； <br>
Create, Read, Update, Delete <br>
 Java API包含Hbase shell支持的所有功能，甚至更多；</li>
<li>Java API是访问Hbase最快的方式。</li>
</ol>



<h4 id="422-java-api程序设计步骤">4.2.2 Java API程序设计步骤</h4>

<ol>
<li>步骤1：创建一个Configuration对象，包含各种配置信息</li>
</ol>



<pre class="prettyprint"><code class=" hljs sql">Configuration conf = HbaseConfiguration.<span class="hljs-operator"><span class="hljs-keyword">create</span>();</span></code></pre>

<ol>
<li>步骤2：构建一个HTable句柄 <br>
 提供Configuration对象 <br>
 提供待访问Table的名称</li>
</ol>



<pre class="prettyprint"><code class=" hljs fix"><span class="hljs-attribute">HTable table </span>=<span class="hljs-string"> new HTable(conf, tableName);</span></code></pre>

<ol>
<li>步骤3：执行相应的操作 <br>
 执行put、 get、 delete、 scan等操作</li>
</ol>



<pre class="prettyprint"><code class=" hljs lua"><span class="hljs-built_in">table</span>.getTableName();</code></pre>

<ol>
<li>步骤4：关闭HTable句柄 <br>
 将内存数据刷新到磁盘上 <br>
 释放各种资源</li>
</ol>



<pre class="prettyprint"><code class=" hljs lua"><span class="hljs-built_in">table</span>.close();</code></pre>

<p>实例： <br>
<img src="https://img-blog.csdn.net/20161026150238347" alt="这里写图片描述" title=""></p>



<h4 id="423-向hbase写入数据">4.2.3 向HBase写入数据</h4>

<p>步骤1：创建一个Put对象；</p>



<pre class="prettyprint"><code class=" hljs mathematica"><span class="hljs-keyword">Put</span> put = new <span class="hljs-keyword">Put</span>(Bytes.toBytes(<span class="hljs-string">"rowkey"</span>));</code></pre>

<p>步骤2：设置cell值；</p>



<pre class="prettyprint"><code class=" hljs livecodeserver">Put.<span class="hljs-built_in">add</span>(family, column, <span class="hljs-built_in">value</span>)
Put.<span class="hljs-built_in">add</span>(family, column, timestamp, <span class="hljs-built_in">value</span>)
Put.<span class="hljs-built_in">add</span>(KeyValue kv)</code></pre>

<p>步骤3：调用HTable中的put方法，写入数据； <br>
 步骤4：关闭HTable句柄。 <br>
<img src="https://img-blog.csdn.net/20161026151229554" alt="这里写图片描述" title=""></p>



<h4 id="424-从hbase中读取数据">4.2.4 从Hbase中读取数据</h4>

<ul>
<li><strong>支持的API类型</strong> <br>
 通过rowkey获取一行数据 <br>
 通过一个rowkey集合获取多条记录 <br>
 扫描整个表或者表的一部分</li>
<li><strong>扫描表</strong> <br>
 可指定扫描的范围[startkey endkey) <br>
 表中数据是按照rowkey排序的</li>
<li><p><strong>API 特点</strong> <br>
 数目有限、使用简单</p></li>
<li><p><strong>读取数据时注意事项：</strong> <br>
 只读取需要的数据 <br>
 尽可能增加数据约束条件 <br>
 可增加family, column(s), time range 和 max versions等约束条件</p></li>
<li><strong>接口实例</strong> <br>
 get.setTimeRange(minStamp, maxStamp) <br>
 get.setMaxVersions(maxVersions) <br>
 get.addFamily(family) <br>
 get.addColumn(family, column)</li>
</ul>

<h4 id="425-从hbase中删除数据">4.2.5 从Hbase中删除数据</h4>

<p><img src="https://img-blog.csdn.net/20161026153025147" alt="这里写图片描述" title=""></p>

<h4 id="426-从hbase中scan数据">4.2.6 从Hbase中scan数据</h4>

<p><img src="https://img-blog.csdn.net/20161026153155312" alt="这里写图片描述" title=""></p>



<h2 id="五总结">五.总结</h2>

<p>本博客是记录了HBase的基础内容，核心掌握HBase的设计思想和java编程，搞清楚HBase在什么时候使用！三种情况：需要随机访问，批量大数据高并发操作，操作为简单读写访问！</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>