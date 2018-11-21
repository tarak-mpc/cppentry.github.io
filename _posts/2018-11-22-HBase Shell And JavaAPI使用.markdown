---
layout:     post
title:      HBase Shell And JavaAPI使用
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <div class="iteye-blog-content-contain" style="font-size:14px;">
<p> </p>
<p>HBase shell操作</p>
<p>表创建</p>
<pre class="js">#进入hbase shell
[root@centos bin]# hbase shell
#创建一张user表，有三个列族
hbase(main):001:0&gt; create 'user','uid','address','info44'
#查看表
hbase(main):002:0&gt; list
TABLE
user</pre>
<p>查看表结构</p>
<pre class="js">hbase(main):001:0&gt; describe 'user'
DESCRIPTION                                                                                           ENABLED
 'user', {NAME =&gt; 'address', DATA_BLOCK_ENCODING =&gt; 'NONE', BLOOMFILTER =&gt; 'NONE', REPLICATION_SCOPE  true
 =&gt; '0', VERSIONS =&gt; '3', COMPRESSION =&gt; 'NONE', MIN_VERSIONS =&gt; '0', TTL =&gt; '2147483647', KEEP_DELET
 ED_CELLS =&gt; 'false', BLOCKSIZE =&gt; '65536', IN_MEMORY =&gt; 'false', ENCODE_ON_DISK =&gt; 'true', BLOCKCACH
 E =&gt; 'true'}, {NAME =&gt; 'info44', DATA_BLOCK_ENCODING =&gt; 'NONE', BLOOMFILTER =&gt; 'NONE', REPLICATION_S
 COPE =&gt; '0', VERSIONS =&gt; '3', COMPRESSION =&gt; 'NONE', MIN_VERSIONS =&gt; '0', TTL =&gt; '2147483647', KEEP_
 DELETED_CELLS =&gt; 'false', BLOCKSIZE =&gt; '65536', IN_MEMORY =&gt; 'false', ENCODE_ON_DISK =&gt; 'true', BLOC
 KCACHE =&gt; 'true'}, {NAME =&gt; 'uid', DATA_BLOCK_ENCODING =&gt; 'NONE', BLOOMFILTER =&gt; 'NONE', REPLICATION
 _SCOPE =&gt; '0', VERSIONS =&gt; '3', COMPRESSION =&gt; 'NONE', MIN_VERSIONS =&gt; '0', TTL =&gt; '2147483647', KEE
 P_DELETED_CELLS =&gt; 'false', BLOCKSIZE =&gt; '65536', IN_MEMORY =&gt; 'false', ENCODE_ON_DISK =&gt; 'true', BL
 OCKCACHE =&gt; 'true'}
</pre>
<p> 删除表</p>
<pre><code class="language-java">#不能直接删除
hbase(main):005:0* drop 'user'
ERROR: Table user is enabled. Disable it first.'
Here is some help for this command:
Drop the named table. Table must first be disabled: e.g. "hbase&gt; drop 't1'"
#先disable一张表
hbase(main):006:0&gt; disable 'user'
0 row(s) in 2.1410 seconds
#才能删除
hbase(main):007:0&gt; drop 'user'
0 row(s) in 1.1180 seconds
hbase(main):008:0&gt; list
TABLE
0 row(s) in 0.0400 seconds
</code></pre>
<p> 插入一条记录</p>
<pre class="js">#表明，行健，列族，值
hbase(main):004:0&gt; put 'user','lisi','address:city','beijing'
hbase(main):005:0&gt; get 'user','lisi'
COLUMN                                   CELL
 address:city                            timestamp=1439284919358, value=beijing
#查询address列族信息
hbase(main):005:0&gt; get 'user','lisi','address'
#查询address列族中city修饰符
hbase(main):005:0&gt; get 'user','lisi','address:city'</pre>
<p>查看记录版本信息，默认最多显示3条例是版本</p>
<pre class="js">#查看wangwu info列族的age标识符版本信息
hbase(main):016:0&gt; get 'user','wangwu',{COLUMN=&gt;'info:age',VERSIONS=&gt;3}
COLUMN                                   CELL
 info:age                                timestamp=1439285717405, value=28
 info:age                                timestamp=1439285714541, value=27
 info:age                                timestamp=1439285711351, value=26
</pre>
<p> 查询整长表</p>
<pre class="js">hbase(main):001:0&gt; scan 'user'
</pre>
<p>删除一行记录</p>
<pre class="js">hbase(main):001:0&gt; deleteall 'user','wangwu'</pre>
<p> </p>
<p>删除一列记录</p>
<pre class="js">#删除行健为wangwu的info列族中age标识符列
hbase(main):001:0&gt;delete 'user','wangwu','info:age'</pre>
<p>清空表数据</p>
<pre class="js">hbase(main):001:0&gt; truncate 'user'</pre>
<p> </p>
<p> </p>
<p>HBase JavaAPI</p>
<p>创建表</p>
<pre><code class="language-java">@Test
	public void createTableTest() throws Exception {
		Configuration configuration = HBaseConfiguration.create();
		configuration.set("hbase.rootdir", "hdfs://192.168.56.101:/hbase");
		configuration.set("hbase.zookeeper.quorum", "centos");
		
		HBaseAdmin hBaseAdmin = new HBaseAdmin(configuration);
		// exist table name
		if (!hBaseAdmin.tableExists("tablee")) {
			// create table
			HTableDescriptor tableDescriptor = new HTableDescriptor("tablee");
			// create colum family
			HColumnDescriptor columnDescriptor = new HColumnDescriptor("familyy");
			// set column family in table
			tableDescriptor.addFamily(columnDescriptor);
			// create
			hBaseAdmin.createTable(tableDescriptor);
		}
		hBaseAdmin.close();
	}</code></pre>
<p> 删除表</p>
<pre><code class="language-java">@Test
	public void deleteTableTest() throws Exception {
		Configuration configuration = HBaseConfiguration.create();
		configuration.set("hbase.rootdir", "hdfs://192.168.56.101:/hbase");
		configuration.set("hbase.zookeeper.quorum", "centos");
		
		HBaseAdmin hBaseAdmin = new HBaseAdmin(configuration);
		hBaseAdmin.disableTable("tablee");
		hBaseAdmin.deleteTable("tablee");
		hBaseAdmin.close();
	}</code></pre>
<p> 插入一条记录</p>
<pre><code class="language-java">@Test
	public void insertDataTest() throws Exception {
		Configuration configuration = HBaseConfiguration.create();
		configuration.set("hbase.rootdir", "hdfs://192.168.56.101:/hbase");
		configuration.set("hbase.zookeeper.quorum", "centos");

		HTable hTable = new HTable(configuration, "tablee");
		// set rowkey
		Put put = new Put("rowkey3".getBytes());
		// set column family (列族名，列族标识符，值)
		put.add("familyy".getBytes(), "age".getBytes(), "25".getBytes());
		hTable.put(put);
		hTable.close();
	}</code></pre>
<p> 查询一条记录</p>
<pre><code class="language-java">@Test
	public void getRecordDataTest() throws Exception {
		Configuration configuration = HBaseConfiguration.create();
		configuration.set("hbase.rootdir", "hdfs://192.168.56.101:/hbase");
		configuration.set("hbase.zookeeper.quorum", "centos");

		HTable hTable = new HTable(configuration, "tablee");
		Get get = new Get("rowkey1".getBytes());
		Result result = hTable.get(get);
		System.out.println(result);
		// get column family
		byte[] ageValue = result.getValue("familyy".getBytes(), "age".getBytes());
		System.out.println(new String(ageValue));
		hTable.close();
	}</code></pre>
<p> 查询全表数据</p>
<pre><code class="language-java">@Test
	public void findAllDataTest() throws Exception {
		Configuration configuration = HBaseConfiguration.create();
		configuration.set("hbase.rootdir", "hdfs://192.168.56.101:/hbase");
		configuration.set("hbase.zookeeper.quorum", "centos");

		HTable hTable = new HTable(configuration, "tablee");
		ResultScanner resultScanner = hTable.getScanner(new Scan());
		for (Result result : resultScanner) {
			byte[] row = result.getRow();
			byte[] ageValue = result.getValue("familyy".getBytes(), "age".getBytes());
			System.out.println(new String(row) + "\t" + new String(ageValue));
		}
		hTable.close();
	}</code></pre>
<p> </p>
<p> </p>
<p> </p>
<p> </p>
<p> </p>
<p> </p>
<p> </p>
</div>            </div>
                </div>