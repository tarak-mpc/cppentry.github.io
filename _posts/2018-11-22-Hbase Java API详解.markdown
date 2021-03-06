---
layout:     post
title:      Hbase Java API详解
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：https://blog.csdn.net/jiaxinhong					https://blog.csdn.net/jiaxinhong/article/details/82826254				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <div class="postBody">
			<div id="cnblogs_post_body" class="blogpost-body"><p>HBase是Hadoop的数据库，能够对大数据提供随机、实时读写访问。他是开源的，分布式的，多版本的，面向列的，存储模型。</p>
<p>在讲解的时候我首先给大家讲解一下HBase的整体结构，如下图：</p>
<p></p>
<p>HBase Master是服务器负责管理所有的HRegion服务器，HBase Master并不存储HBase服务器的任何数据，HBase逻辑上的表可能会划分为多个HRegion，然后存储在HRegion Server群中，HBase Master Server中存储的是从数据到HRegion Server的映射。</p>
<p>一台机器只能运行一个HRegion服务器，数据的操作会记录在Hlog中，在读取数据时候，HRegion会先访问Hmemcache缓存，如果 缓存中没有数据才回到Hstore中上找，没一个列都会有一个Hstore集合，每个Hstore集合包含了很多具体的HstoreFile文件，这些文 件是B树结构的，方便快速读取。</p>
<p> </p>
<p>再看下HBase数据物理视图如下：</p>
<p> </p>
<table border="1" cellspacing="0" cellpadding="0"><tbody><tr><td rowspan="2" width="67"><strong>Row Key</strong></td>
<td rowspan="2" width="104"><strong>Timestamp</strong></td>
<td colspan="2" width="385"><strong>Column Family</strong></td>
</tr><tr><td width="227"><strong>URI</strong></td>
<td width="158"><strong>Parser</strong></td>
</tr><tr><td rowspan="3" width="67">r1</td>
<td width="104">t3</td>
<td width="227">url=http://www.taobao.com</td>
<td width="158">title=天天特价</td>
</tr><tr><td width="104">t2</td>
<td width="227">host=taobao.com</td>
<td width="158"> </td>
</tr><tr><td width="104">t1</td>
<td width="227"> </td>
<td width="158"> </td>
</tr><tr><td rowspan="2" width="67">r2</td>
<td width="104">t5</td>
<td width="227">url=http://www.alibaba.com</td>
<td width="158">content=每天…</td>
</tr><tr><td width="104">t4</td>
<td width="227">host=alibaba.com</td>
<td width="158"> </td>
</tr></tbody></table><p>Ø  Row Key: 行键，Table的主键，Table中的记录按照Row Key排序</p>
<p>Ø  Timestamp: 时间戳，每次数据操作对应的时间戳，可以看作是数据的version number</p>
<p>Ø  Column Family：列簇，Table在水平方向有一个或者多个Column Family组成，一个Column Family中可以由任意多个Column组成，即Column Family支持动态扩展，无需预先定义Column的数量以及类型，所有Column均以二进制格式存储，用户需要自行进行类型转换。</p>
<p> </p>
<p>了解了HBase的体系结构和HBase数据视图够，现在让我们一起看看怎样通过Java来操作HBase数据吧！</p>
<p>先说说具体的API先，如下</p>
<p> </p>
<p>HBaseConfiguration是每一个hbase client都会使用到的对象，它代表的是HBase配置信息。它有两种构造方式：</p>
<p>public HBaseConfiguration()</p>
<p>public HBaseConfiguration(final Configuration c)</p>
<p>默认的构造方式会尝试从hbase-default.xml和hbase-site.xml中读取配置。如果classpath没有这两个文件，就需要你自己设置配置。</p>
<p>Configuration HBASE_CONFIG = new Configuration();</p>
<p>HBASE_CONFIG.set(“hbase.zookeeper.quorum”, “zkServer”);</p>
<p>HBASE_CONFIG.set(“hbase.zookeeper.property.clientPort”, “2181″);</p>
<p>HBaseConfiguration cfg = new HBaseConfiguration(HBASE_CONFIG);</p>
<p> </p>
<p>创建表</p>
<p>创建表是通过HBaseAdmin对象来操作的。HBaseAdmin负责表的META信息处理。HBaseAdmin提供了createTable这个方法：</p>
<p>public void createTable(HTableDescriptor desc)</p>
<p>HTableDescriptor 代表的是表的schema, 提供的方法中比较有用的有</p>
<p>setMaxFileSize，指定最大的region size</p>
<p>setMemStoreFlushSize 指定memstore flush到HDFS上的文件大小</p>
<p> </p>
<p>增加family通过 addFamily方法</p>
<p>public void addFamily(final HColumnDescriptor family)</p>
<p>HColumnDescriptor 代表的是column的schema，提供的方法比较常用的有</p>
<p>setTimeToLive:指定最大的TTL,单位是ms,过期数据会被自动删除。</p>
<p>setInMemory:指定是否放在内存中，对小表有用，可用于提高效率。默认关闭</p>
<p>setBloomFilter:指定是否使用BloomFilter,可提高随机查询效率。默认关闭</p>
<p>setCompressionType:设定数据压缩类型。默认无压缩。</p>
<p>setMaxVersions:指定数据最大保存的版本个数。默认为3。</p>
<p> </p>
<p>一个简单的例子，创建了4个family的表：</p>
<div class="cnblogs_code">
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a title="复制代码"><img src="https://common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre>HBaseAdmin hAdmin = new HBaseAdmin(hbaseConfig);

HTableDescriptor t = new HTableDescriptor(tableName);

t.addFamily(new HColumnDescriptor(“f1″));

t.addFamily(new HColumnDescriptor(“f2″));

t.addFamily(new HColumnDescriptor(“f3″));

t.addFamily(new HColumnDescriptor(“f4″));

hAdmin.createTable(t);</pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a title="复制代码"><img src="https://common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
</div>
<p> </p>
<p> </p>
<p>删除表</p>
<p>删除表也是通过HBaseAdmin来操作，删除表之前首先要disable表。这是一个非常耗时的操作，所以不建议频繁删除表。</p>
<p>disableTable和deleteTable分别用来disable和delete表。</p>
<p>Example:</p>
<div class="cnblogs_code">
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a title="复制代码"><img src="https://common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre>HBaseAdmin hAdmin = new HBaseAdmin(hbaseConfig);

if (hAdmin.tableExists(tableName)) {

       hAdmin.disableTable(tableName);

       hAdmin.deleteTable(tableName);

}</pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a title="复制代码"><img src="https://common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
</div>
<p> </p>
<p>查询数据</p>
<p>查询分为单条随机查询和批量查询。</p>
<p>单条查询是通过rowkey在table中查询某一行的数据。HTable提供了get方法来完成单条查询。</p>
<p>批量查询是通过制定一段rowkey的范围来查询。HTable提供了个getScanner方法来完成批量查询。</p>
<p>public Result get(final Get get)</p>
<p>public ResultScanner getScanner(final Scan scan)</p>
<p>Get对象包含了一个Get查询需要的信息。它的构造方法有两种：</p>
<p>  public Get(byte [] row)</p>
<p>  public Get(byte [] row, RowLock rowLock)</p>
<p>Rowlock是为了保证读写的原子性，你可以传递一个已经存在Rowlock，否则HBase会自动生成一个新的rowlock。</p>
<p>Scan对象提供了默认构造函数，一般使用默认构造函数。</p>
<p> </p>
<p>Get/Scan的常用方法有：</p>
<p>addFamily/addColumn:指定需要的family或者column,如果没有调用任何addFamily或者Column,会返回所有的columns.</p>
<p>setMaxVersions:指定最大的版本个数。如果不带任何参数调用setMaxVersions,表示取所有的版本。如果不掉用setMaxVersions,只会取到最新的版本。</p>
<p>setTimeRange:指定最大的时间戳和最小的时间戳，只有在此范围内的cell才能被获取。</p>
<p>setTimeStamp:指定时间戳。</p>
<p>setFilter:指定Filter来过滤掉不需要的信息</p>
<p> </p>
<p>Scan特有的方法：</p>
<p>setStartRow:指定开始的行。如果不调用，则从表头开始。</p>
<p>setStopRow:指定结束的行（不含此行）。</p>
<p>setBatch:指定最多返回的Cell数目。用于防止一行中有过多的数据，导致OutofMemory错误。</p>
<p>ResultScanner是Result的一个容器，每次调用ResultScanner的next方法，会返回Result.</p>
<p>public Result next() throws IOException;</p>
<p>public Result [] next(int nbRows) throws IOException;</p>
<p> </p>
<p>Result代表是一行的数据。常用方法有：</p>
<p>getRow:返回rowkey</p>
<p>raw:返回所有的key value数组。</p>
<p>getValue:按照column来获取cell的值</p>
<p> </p>
<p>Example:</p>
<div class="cnblogs_code">
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a title="复制代码"><img src="https://common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre>Scan s = new Scan();

s.setMaxVersions();

ResultScanner ss = table.getScanner(s);

for(Result r:ss){

    System.out.println(new String(r.getRow()));

    for(KeyValue kv:r.raw()){

       System.out.println(new String(kv.getColumn()));

    }

}</pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a title="复制代码"><img src="https://common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
</div>
<p> </p>
<p> </p>
<p>插入数据</p>
<p>HTable通过put方法来插入数据。 </p>
<p>public void put(final Put put) throws IOException</p>
<p>public void put(final List puts) throws IOException</p>
<p>可以传递单个批Put对象或者List put对象来分别实现单条插入和批量插入。</p>
<p>Put提供了3种构造方式：</p>
<p>public Put(byte [] row)</p>
<p>public Put(byte [] row, RowLock rowLock)</p>
<p>public Put(Put putToCopy) </p>
<p> </p>
<p>Put常用的方法有：</p>
<p>add:增加一个Cell</p>
<p>setTimeStamp:指定所有cell默认的timestamp,如果一个Cell没有指定timestamp,就会用到这个值。如果没有调用，HBase会将当前时间作为未指定timestamp的cell的timestamp.</p>
<p>setWriteToWAL: WAL是Write Ahead Log的缩写，指的是HBase在插入操作前是否写Log。默认是打开，关掉会提高性能，但是如果系统出现故障(负责插入的Region Server挂掉)，数据可能会丢失。</p>
<p>另外HTable也有两个方法也会影响插入的性能</p>
<p>setAutoFlash: AutoFlush指的是在每次调用HBase的Put操作，是否提交到HBase Server。默认是true,每次会提交。如果此时是单条插入，就会有更多的IO,从而降低性能.</p>
<p>setWriteBufferSize: Write Buffer Size在AutoFlush为false的时候起作用，默认是2MB,也就是当插入数据超过2MB,就会自动提交到Server</p>
<p> </p>
<p>Example:</p>
<div class="cnblogs_code">
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a title="复制代码"><img src="https://common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre>HTable table = new HTable(hbaseConfig, tableName);

table.setAutoFlush(autoFlush);

List lp = new ArrayList();

int count = 10000;

byte[] buffer = new byte[1024];

Random r = new Random();

for (int i = 1; i &lt;= count; ++i) {

       Put p = new Put(String.format(“row%09d”,i).getBytes());

       r.nextBytes(buffer);

       p.add(“f1″.getBytes(), null, buffer);

       p.add(“f2″.getBytes(), null, buffer);

       p.add(“f3″.getBytes(), null, buffer);

       p.add(“f4″.getBytes(), null, buffer);

       p.setWriteToWAL(wal);

       lp.add(p);

       if(i%1000==0){

           table.put(lp);

           lp.clear();

       }

    }</pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a title="复制代码"><img src="https://common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
</div>
<p> </p>
<p> </p>
<p>删除数据</p>
<p>HTable 通过delete方法来删除数据。</p>
<p>  public void delete(final Delete delete) </p>
<p> </p>
<p>Delete构造方法有：</p>
<p>public Delete(byte [] row)</p>
<p>public Delete(byte [] row, long timestamp, RowLock rowLock)</p>
<p>public Delete(final Delete d)</p>
<p>Delete常用方法有</p>
<p>deleteFamily/deleteColumns:指定要删除的family或者column的数据。如果不调用任何这样的方法，将会删除整行。</p>
<p>注意：如果某个Cell的timestamp高于当前时间，这个Cell将不会被删除，仍然可以查出来。</p>
<p> </p>
<p>Example:</p>
<div class="cnblogs_code">
<pre>HTable table = new HTable(hbaseConfig, “mytest”);

Delete d = new Delete(“row1″.getBytes());

table.delete(d) </pre>
</div>
<p> </p>
<p>切分表</p>
<p>HBaseAdmin提供split方法来将table 进行split.</p>
<p>public void split(final String tableNameOrRegionName)</p>
<p> </p>
<p>如果提供的tableName，那么会将table所有region进行split ;如果提供的region Name，那么只会split这个region.</p>
<p>由于split是一个异步操作，我们并不能确切的控制region的个数。</p>
<p> </p>
<p>Example:</p>
<div class="cnblogs_code">
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a title="复制代码"><img src="https://common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre>public void split(String tableName,int number,int timeout) throws Exception {

    Configuration HBASE_CONFIG = new Configuration();

    HBASE_CONFIG.set(“hbase.zookeeper.quorum”, GlobalConf.ZOOKEEPER_QUORUM);

    HBASE_CONFIG.set(“hbase.zookeeper.property.clientPort”, GlobalConf.ZOOKEEPER_PORT);

    HBaseConfiguration cfg = new HBaseConfiguration(HBASE_CONFIG);

    HBaseAdmin hAdmin = new HBaseAdmin(cfg);

    HTable hTable = new HTable(cfg,tableName);

    int oldsize = 0;

    t =  System.currentTimeMillis();

    while(true){

       int size = hTable.getRegionsInfo().size();

       logger.info(“the region number=”+size);

       if(size&gt;=number ) break;

       if(size!=oldsize){

           hAdmin.split(hTable.getTableName());

           oldsize = size;

       }       else if(System.currentTimeMillis()-t&gt;timeout){

           break;

       }

       Thread.sleep(1000*10);

    }

}</pre>
</div></div><div id="MySignature"></div>
<div class="clear"></div>
<div id="blog_post_info_block">
<div id="BlogPostCategory"></div>
<div id="EntryTag"></div>
<div id="blog_post_info">
</div>
<div class="clear"></div>
<div id="post_next_prev"></div>
</div>


		</div>            </div>
                </div>