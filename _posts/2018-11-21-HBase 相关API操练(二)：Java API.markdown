---
layout:     post
title:      HBase 相关API操练(二)：Java API
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，欢迎大家转载交流，但必须在博文明显位置标明转载地址。否则维权必究！					https://blog.csdn.net/py_123456/article/details/80630551				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2 id="一hbase-java编程">一、HBase Java编程</h2>

<p>（1）HBase是用Java语言编写的，它支持Java编程；</p>

<p>（2）HBase支持CRUD操作：Create，Read，Update和Delete；</p>

<p>（3）Java API包含HBase Shell支持的所有功能，甚至更多；</p>

<p>（4）Java API是访问HBase最快的一种方式。</p>

<h2 id="二hbase-java编程程序设计步骤">二、HBase Java编程——程序设计步骤</h2>

<p>　第一步：创建一个Configuration对象</p>

<p>　　Configuration conf = HBaseConfiguration.create();</p>

<p>　　1)Configuration对象包含各种配置信息</p>

<p>　第二步：构建一个HTable句柄</p>

<p>　　HTable table = new HTable(conf, tableName);</p>

<p>　　1)提供Configuration对象；</p>

<p>　　2)提供待访问Table的名称。</p>

<p>　第三步：执行操作</p>

<p>　　table.getTableName();</p>

<p>　　1)执行put、get、delete、scan等操作</p>

<p>　第四步：关闭HTable句柄</p>

<p>　　table.close();</p>

<p>　　1)将内存数据刷新到磁盘上去</p>

<p>　　2)释放各种资源</p>

<h2 id="三hbase交互">三、HBase交互</h2>

<p>　　与 HBase 集群进行交互，有很多种不同的方式可以选择，如 Java API、REST、Thrift 等。下面我们以Java API为例介绍它们的用法。</p>

<p><strong>Java API 交互</strong></p>

<p>HBase、 与 Hadoop 一样，都是用 java 编写的，所以 HBase 对 java 支持是必需的，下面看看怎么使用java 语言对 HBase 进行操作。Java API 核心类介绍如下。</p>

<p><strong>1、 HBaseConfiguration 类</strong></p>

<p>　　HBaseConfiguration 是每一个 HBase Client 都会使用到的对象，它代表 HBase 配置信息。有两种构造方式。</p>

<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">public</span> <span class="hljs-title">HBaseConfiguration</span>()
<span class="hljs-keyword">public</span> <span class="hljs-title">HBaseConfiguration</span>(<span class="hljs-keyword">final</span> Configuration c)</code></pre>

<p>　　默认构造方式会尝试从 hbase-default.xml 和 HBase-site.xml 文件中读取配置。如果CLASSPATH 没有这两个文件，就需要自己配置。</p>

<pre class="prettyprint"><code class=" hljs cs">Configuration HBASE_CONFIG = <span class="hljs-keyword">new</span> Configuration();
HBASE_CONFIG.<span class="hljs-keyword">set</span>(<span class="hljs-string">"hbase.ZooKeeper.quorum"</span>,<span class="hljs-string">"zkServer"</span>);    <span class="hljs-comment">//hbase 服务地址</span>
HBASE_CONFIG.<span class="hljs-keyword">set</span>(<span class="hljs-string">"hbase.ZooKeeper.property.clientPort"</span>,<span class="hljs-string">"2181"</span>);    <span class="hljs-comment">//端口号</span>
HBaseConfiguration cfg = <span class="hljs-keyword">new</span> HBaseConfiguration(HBASE_CONFIG);     <span class="hljs-comment">//读取配置文件</span></code></pre>

<p>2、 创建表</p>

<p>　　创建表通过 HBaseAdmin 对象操作。HBaseAdmin 负责META 表信息的处理。HBaseAdmin 提供了 createTable 方法。</p>

<p>public void createTable(HTableDescriptor desc) <br>
 　　HTableDescriptor 表示表的 Schema，提供常用方法有以下两个。</p>

<pre><code>     1)setMaxFileSize：指定最大 Region 的大小。

     2)setMemStoreFlushSize：指定 MemStore Flush 到 HDFS 上的文件大小。
</code></pre>

<p>3、 增加 Family</p>

<p>使用 addFamily 方法实现 Family 的添加。</p>

<p>public void addFamily(final HColumnDescriptor family) <br>
　　HColumnDescriptor 代表 Column 的 Schema，提供的常用方法有以下几个。</p>

<pre><code>    1、setTimeToLive：指定最大的 TTL（单位是 ms），过期数据会被自动删除。

    2、setInMemory：指定是否放在内存中，对小表有用，可用于提高效率。默认关闭。

    3、setBloomFilter：指定是否使用 BloomFilter，可提高随机查询效率。默认关闭。

    4、setCompressionType：设定数据压缩类型。默认无压缩。

    5、setMaxVersions：指定数据最大保存的版本个数。默认为3。
</code></pre>

<p>举个简单的例子，创建 4 个 Family 表，命令如下。</p>

<pre class="prettyprint"><code class=" hljs cs">HBaseAdmin hAdmin = <span class="hljs-keyword">new</span> HBaseAdmin(hbaseConfig);
HTableDescriptor table = <span class="hljs-keyword">new</span> HTableDescriptor(tableName);
table.addFamily(<span class="hljs-keyword">new</span> HColumnDescriptor(<span class="hljs-string">"f1"</span>));
table.addFamily(<span class="hljs-keyword">new</span> HColumnDescriptor(<span class="hljs-string">"f2"</span>));
table.addFamily(<span class="hljs-keyword">new</span> HColumnDescriptor(<span class="hljs-string">"f3"</span>));
table.addFamily(<span class="hljs-keyword">new</span> HColumnDescriptor(<span class="hljs-string">"f4"</span>));
hAdmin.createTable(table);</code></pre>

<p><strong>4、 删除表</strong></p>

<p>删除表也是通过 HBaseAdmin 来操作，删除表之前首先要 disable 表。这是一个非常耗时的操作，所以不建议频繁删除表。</p>

<p>disableTable 和 deleteTable 分别用来执行 disable 和 delete 操作。使用方法如下。</p>

<pre class="prettyprint"><code class=" hljs avrasm">HBaseAdmin hAdmin = new HBaseAdmin(hbaseConfig)<span class="hljs-comment">;</span>
if(hAdmin<span class="hljs-preprocessor">.tableExists</span>(tableName)){
        hAdmin<span class="hljs-preprocessor">.disableTable</span>(tableName)<span class="hljs-comment">;</span>
        hAdmin<span class="hljs-preprocessor">.deleteTable</span>(tableName)<span class="hljs-comment">;</span>
}</code></pre>

<p><strong>5、查询数据</strong></p>

<p>查询分为单条随机查询和批量查询。单条查询通过 Row Key 在Table 中查询某一行的数据，HTable 提供了get 方法完成单条查询。批量查询通过制定一段 Row Key 的范围来查询，HTable 提供了 getScanner 方法完成批量查询。</p>

<pre class="prettyprint"><code class=" hljs "></code></pre>

<p>public Result get(final Get get) <br>
public ResultScanner getScanner(final Scan scan)</p>



<pre class="prettyprint"><code class=" hljs "></code></pre>

<p>Get 对象包含一个 Get 查询需要的信息，它的构造方法有两种。</p>

<pre class="prettyprint"><code class=" hljs cs"><span class="hljs-keyword">public</span> <span class="hljs-title">Get</span>(<span class="hljs-keyword">byte</span> [] row)
<span class="hljs-keyword">public</span> <span class="hljs-title">Get</span>(<span class="hljs-keyword">byte</span> [] row，RowLock rowLock)</code></pre>

<p>Row Lock 为了保证读写的原子性，可以传递一个已经存在 Row Lock，否则 HBase 会自动生成一个新的 Row Lock。</p>

<p>**重点内容**Scan 对象提供了默认构造函数，一般使用默认构造函数</p>

<p>1) Get 和 Scan 的常用方法有以下几个。</p>

<p>addFamily/addColumn：指定需要的 Family 或者 Column，如果没有调用任何 Family 或者 Column，会返回所有的 Column。</p>

<p>setMaxVersions：指定最大的版本个数。如果不带任何参数调用 setMaxVersions，表示取所有的版本。如果不调用 setMaxVersions，只会取到最新的版本。</p>

<p>setTimeRange：指定最大的时间戳和最小的时间戳，只有在此范围内的 Cell 才能被获取。</p>

<p>setTimeStamp：指定时间戳。</p>

<p>setFilter：指定 Filter 过滤不需要的信息。</p>

<p>2) Scan 特有的方法如下。</p>

<p>setStartRow：指定开始的行。如果不调用，从表头开始。</p>

<p>setStopRow：指定结束的行（不含此行）。</p>

<p>setBatch：指定最多返回的 Cell 数目。防止一行中有过多的数据，导致 OOM 错误。</p>

<p>3) Result 代表是一行的数据。常用方法有以下几个。</p>

<p>getRow：返回 Row Key。</p>

<p>raw：返回所有的 KeyValue 数组。</p>

<p>getValue：按照 Column 来获取 Cell 的值。</p>

<p>ResultScanner 是 Result 的一个容器，每次调用ResultScanner 的next 方法会返回Result。</p>

<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">public</span> Result <span class="hljs-title">next</span>() <span class="hljs-keyword">throws</span> IOException;
<span class="hljs-keyword">public</span> Result [] <span class="hljs-title">next</span>(<span class="hljs-keyword">int</span> nbRows) <span class="hljs-keyword">throws</span> IOException;</code></pre>

<p>示例代码如下。</p>

<pre class="prettyprint"><code class=" hljs avrasm">Scan scan = new Scan()<span class="hljs-comment">;</span>
scan<span class="hljs-preprocessor">.setMaxVersions</span>()<span class="hljs-comment">;</span>
ResultScanner ss = table<span class="hljs-preprocessor">.getScanner</span>(scan)<span class="hljs-comment">;</span>
for(Result r:ss){
        System<span class="hljs-preprocessor">.out</span><span class="hljs-preprocessor">.println</span>(new String(r<span class="hljs-preprocessor">.getRow</span>()))<span class="hljs-comment">;</span>
        for(KeyValue kv:r<span class="hljs-preprocessor">.raw</span>){
                 System<span class="hljs-preprocessor">.out</span><span class="hljs-preprocessor">.println</span>(new String(kv<span class="hljs-preprocessor">.getColumn</span>()))<span class="hljs-comment">;</span>
        }
}</code></pre>

<p><strong>6、 插入数据</strong></p>

<p>HTable 通过 put 方法插入数据，可以传递单个 put 对象 或 List put 对象分别实现单条插入和批量插入。</p>

<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">put</span>(<span class="hljs-keyword">final</span> Put put) <span class="hljs-keyword">throws</span> IOException
<span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">put</span>(<span class="hljs-keyword">final</span> List&lt; Put&gt; puts) <span class="hljs-keyword">throws</span> IOException</code></pre>

<p>Put 提供3 种构造方式。</p>

<pre class="prettyprint"><code class=" hljs cs"><span class="hljs-keyword">public</span> <span class="hljs-title">Put</span>(<span class="hljs-keyword">byte</span> [] row)
<span class="hljs-keyword">public</span> <span class="hljs-title">Put</span>(<span class="hljs-keyword">byte</span> [] row)
<span class="hljs-keyword">public</span> <span class="hljs-title">Put</span>(<span class="hljs-keyword">byte</span> [] row,RowLock rowLock)
<span class="hljs-keyword">public</span> <span class="hljs-title">Put</span>(Put putToCopy)</code></pre>

<p><strong>Put 常用的方法有以下几个</strong>。</p>

<p>1)add：增加一个 Cell。</p>

<p>2)setTimeStamp：指定所有 Cell 默认的 timestamp，如果一个 Cell 没有指定 timestamp，就会用到这个值。如果没有调用，HBase 会将当前时间作为未指定 timestamp 的Cell 的 timestamp。</p>

<p>3)setWriteToWAL：WAL 是 Write Ahead Log 的缩写，指的是 HBase 在插入操作前是否写 Log。默认是打开，关掉会提高性能，但是如果系统出现故障（负责插入的Region Server 挂掉），数据可能会丢失。</p>

<p>　另外 HTable 也有两个方法会影响插入的性能。</p>

<p>1)setAutoFlash：AutoFlush 指的是在每次调用 HBase 的 Put 操作，是否提交到 HBase Server。默认是 true，每次会提交。如果此时是单条插入，就会有更多的I/O，从而降低其性能。</p>

<p>2)setWriteBufferSize：Write Buffer Size 在 AutoFlush 为false 的时候起作用，默认是 2MB，也就是插入数据超过 2MB，就会自动提交到 Server。</p>

<p>示例代码如下。</p>

<pre class="prettyprint"><code class=" hljs axapta">HTable table = <span class="hljs-keyword">new</span> HTable(hbaseConfig, tableName);
table.setAutoFlush(autoFlush);
List&lt; Put&gt; lp = <span class="hljs-keyword">new</span> ArrayList&lt; Put&gt;();
<span class="hljs-keyword">int</span> <span class="hljs-keyword">count</span> = <span class="hljs-number">10000</span>;
<span class="hljs-keyword">byte</span>[] buffer = <span class="hljs-keyword">new</span> <span class="hljs-keyword">byte</span>[<span class="hljs-number">1024</span>];
Random r = <span class="hljs-keyword">new</span> Random();
<span class="hljs-keyword">for</span>(<span class="hljs-keyword">int</span> i = <span class="hljs-number">1</span>;i &lt;= <span class="hljs-keyword">count</span>;++i){
               Put p = <span class="hljs-keyword">new</span> Put(String.format(<span class="hljs-string">"row%09d"</span>,i).getBytes());
               r.nextBytes(buffer);
               p.add(<span class="hljs-string">"f1"</span>.getBytes(), <span class="hljs-keyword">null</span>, buffer);
               p.add(<span class="hljs-string">"f2"</span>.getBytes(), <span class="hljs-keyword">null</span>, buffer);
               p.add(<span class="hljs-string">"f3"</span>.getBytes(), <span class="hljs-keyword">null</span>, buffer);
               p.add(<span class="hljs-string">"f4"</span>.getBytes(), <span class="hljs-keyword">null</span>, buffer);
               p.setWriteToWAL(wal);
               lp.add(p);
               <span class="hljs-keyword">if</span>(i%<span class="hljs-number">1000</span>==<span class="hljs-number">0</span>){
                               table.put(lp);
                               lp.clear();
               }
} </code></pre>

<p><strong>7、 删除数据</strong></p>

<p>HTable 通过 delete 方法删除数据。</p>

<pre class="prettyprint"><code class=" hljs actionscript"><span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-keyword">delete</span>(<span class="hljs-keyword">final</span> Delete <span class="hljs-keyword">delete</span>)</code></pre>

<p>　Delete 构造方法如下。</p>

<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">public</span> <span class="hljs-title">Delete</span>(<span class="hljs-keyword">byte</span> [] row)
<span class="hljs-keyword">public</span> <span class="hljs-title">Delete</span>(<span class="hljs-keyword">byte</span> [] row, <span class="hljs-keyword">long</span> timestamp, RowLock rowLock)
<span class="hljs-keyword">public</span> <span class="hljs-title">Delete</span>(<span class="hljs-keyword">final</span> Delete d)</code></pre>

<p>　Delete 常用方法有 deleteFamily/deleteColumn，用来指定要删除的 Family 或者 Column 的数据。 如果不调用任何这样的方法，将会删除整行。</p>

<p>　注意： 如果某个 Cell 的 timestamp 高于当前时间，这个 Cell 将不会被删除，仍然可以查出来。</p>

<p>　示例代码如下。</p>

<pre class="prettyprint"><code class=" hljs actionscript">HTable table = <span class="hljs-keyword">new</span> HTable(hbaseConfig,<span class="hljs-string">"mytest"</span>);
Delete d = <span class="hljs-keyword">new</span> Delete(<span class="hljs-string">"row1"</span>.getBytes());
table.<span class="hljs-keyword">delete</span>(d)</code></pre>

<p><strong>8、 切分表</strong></p>

<p>HBaseAdmin 提供 split 方法将 table 进行切分。</p>

<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">split</span>(<span class="hljs-keyword">final</span> String tableNameOrRegionName)</code></pre>

<p>如果提供的是 tableName，会将 table 所有 Region 进行切分；如果提供的是 RegionName，只会切分这个Region。Split 是一个异步操作，因此它并不能确切控制 Region 的个数。</p>

<p>示例代码如下。</p>

<pre class="prettyprint"><code class=" hljs cs"><span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">split</span>(String tableName,<span class="hljs-keyword">int</span> number,<span class="hljs-keyword">int</span> timeout) throws Exception{
        Configuration HBASE_CONFIG = <span class="hljs-keyword">new</span> Configuration();
        HBASE_CONFIG.<span class="hljs-keyword">set</span>(<span class="hljs-string">"hbase.ZooKeeper.quorum"</span>,GlobalConf.ZOOKEEPER_QUORUM);
        HBaseConfiguration cfg = <span class="hljs-keyword">new</span> HBaseConfiguration(HBASE_CONFIG);
        HBaseAdmin hAdmin = <span class="hljs-keyword">new</span> HBaseAdmin(cfg);
        HTable hTable = <span class="hljs-keyword">new</span> HTable(cfg,tableName);
        <span class="hljs-keyword">int</span> oldsize = <span class="hljs-number">0</span>;
        <span class="hljs-keyword">long</span> time = System.currentTimeMillis();
        <span class="hljs-keyword">while</span>(<span class="hljs-keyword">true</span>){
                 <span class="hljs-keyword">int</span> size = hTable.getRegionsInfo().size();
                 logger.info(<span class="hljs-string">"the region number="</span>+size);
                 <span class="hljs-keyword">if</span>(size&gt;=number) <span class="hljs-keyword">break</span>;
                 <span class="hljs-keyword">if</span>(size !=oldsize){
                         hAdmin.split(hTable.getTableName());
                         oldsize = size;
                 }<span class="hljs-keyword">else</span> <span class="hljs-keyword">if</span>(System.currentTimeMillis()-time&gt;timeout){
                         <span class="hljs-keyword">break</span>;
                 }
                 Thread.sleep(<span class="hljs-number">1000</span>*<span class="hljs-number">10</span>);
        }
}</code></pre>

<p><strong>以上就是博主为大家介绍的这一板块的主要内容，这都是博主自己的学习过程，希望能给大家带来一定的指导作用，有用的还望大家点个支持，如果对你没用也望包涵，有错误烦请指出。如有期待可关注博主以第一时间获取更新哦，谢谢！</strong></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>