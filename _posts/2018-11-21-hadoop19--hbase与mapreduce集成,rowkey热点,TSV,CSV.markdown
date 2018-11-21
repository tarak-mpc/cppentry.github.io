---
layout:     post
title:      hadoop19--hbase与mapreduce集成,rowkey热点,TSV,CSV
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，转载请标明出处。https://blog.csdn.net/kXYOnA63Ag9zqtXx0/article/details/82954503					https://blog.csdn.net/forever428/article/details/84204017				</div>
								            <div id="content_views" class="markdown_views prism-github-gist">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p></p><div class="toc"><h3>文章目录</h3><ul><ul><ul><li><a href="#hbaseMapreduce_1" rel="nofollow">hbase与Mapreduce集成整合</a></li><ul><li><a href="#hadoopjar_15" rel="nofollow">在hadoop中运行jar包</a></li><li><a href="#_104" rel="nofollow">小案例</a></li></ul><li><a href="#hbase_import_TSV_235" rel="nofollow">hbase import TSV</a></li><li><a href="#hbase_import_CSV_277" rel="nofollow">hbase import CSV</a></li><li><a href="#rowkey_285" rel="nofollow">rowkey的热点与表的设计原则</a></li><ul><li><a href="#_287" rel="nofollow">热点原理</a></li><li><a href="#rowkey_293" rel="nofollow">rowkey长度限制</a></li><li><a href="#rowkey_300" rel="nofollow">rowkey的设计原则</a></li></ul></ul></ul></ul></div><p></p>
<h3><a id="hbaseMapreduce_1"></a>hbase与Mapreduce集成整合</h3>
<p>在公司的实际开发中, 在多数情况下, 都是Mapreduce与Hbase联合使用, 在Hbase中对于Hbase来说, 就是读和写的操作</p>
<p>HBASE与hadoop内部已经做好了集成的封装, 对于用户来说只是调用即可</p>
<p>集成的模式</p>
<ol>
<li>从HBASE中读取数据, HBASE中的数据可以作为map的输入, 输出可以进行任意的指定</li>
<li>将数据写入HBASE, 将HBASE作为reduce的输出</li>
<li>从HBASE读出, 再写入HBASE, 用作数据迁移</li>
</ol>
<p>在HBASE中已经封装好了一个jar包, 该jar包的名为<mark>hbase-server-1.3.1.jar</mark>, 可以在hadoop中运行</p>
<h4><a id="hadoopjar_15"></a>在hadoop中运行jar包</h4>
<p>在hadoop中运行hbase/lib /hbase-server-1.3.1.jar</p>
<pre><code>bin/yarn jar /opt/app/hbase-1.3.1/lib/hbase-server-1.3.1.jar
</code></pre>
<p>在运行以上的命令出现了下面的错误</p>
<pre><code>Exception in thread "main" java.lang.NoClassDefFoundError: org/apache/hadoop/hbase/filter/Filter
</code></pre>
<p>如上的问题出现的主要原因是 在hadoop环境中运行了hbase中的jar包, 而该jar包在运行的过程中需要别的jar包的依赖</p>
<p>解决方案:</p>
<p>需要清楚的知道, 在运行该jar包的时候需要哪些jar包的支持, 如果将jar包全部拷贝过来, 有可能会产生jar包冲突, 通过如下命令, 可以查找到hadoop中需要支持的jar包</p>
<pre><code>[hadoop@hadoop03 hbase-1.3.1]$ bin/hbase mapredcp.
---------------------------------------------------------------
2018-11-15 05:27:43,625 WARN  [main] util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
/opt/app/hbase-1.3.1/lib/hbase-hadoop-compat-1.3.1.jar:/opt/app/hbase-1.3.1/lib/hbase-common-1.3.1.jar:/opt/app/hbase-1.3.1/lib/htrace-core-3.1.0-incubating.jar:/opt/app/hbase-1.3.1/lib/hbase-client-1.3.1.jar:/opt/app/hbase-1.3.1/lib/zookeeper-3.4.5.jar:/opt/app/hbase-1.3.1/lib/netty-all-4.0.23.Final.jar:/opt/app/hbase-1.3.1/lib/protobuf-java-2.5.0.jar:/opt/app/hbase-1.3.1/lib/guava-12.0.1.jar:/opt/app/hbase-1.3.1/lib/metrics-core-2.2.0.jar:/opt/app/hbase-1.3.1/lib/hbase-prefix-tree-1.3.1.jar:/opt/app/hbase-1.3.1/lib/hbase-protocol-1.3.1.jar:/opt/app/hbase-1.3.1/lib/hbase-server-1.3.1.jar
</code></pre>
<p>把hadoop中需要运行的jar包放到hadoop的运行环境中, 两种方式</p>
<ol>
<li><a href="http://xn--jarhadoopclasspathhadoop-env-x048b389dg7cl6d4v0rpksdip0cka.sh" rel="nofollow">把jar包添加到hadoop的classpath中的hadoop-env.sh</a>, 这种方式配置完成之后需要重启hadoop</li>
<li>把hadoop中需要运行的jar包放置到/etc/profile 全局变量中, source 刷新即可</li>
</ol>
<p>这里采取第二种方式</p>
<p>​	(1)配置环境变量</p>
<pre><code>vi /etc/profile
----------------------------------
#HBase And Mapreduce
export HBASE_HOME=/opt/app/hbase-1.3.1
export HADOOP_CLASSPATH=$HADOOP_CLASSPATH:`$HBASE_HOME/bin/hbase mapredcp`
</code></pre>
<p>​	(2)分发到其他机器</p>
<pre><code>sudo scp  /etc/profile hadoop03:/etc/
</code></pre>
<p>​	(3)刷新(需要root用户)</p>
<pre><code>source /etc/profile
</code></pre>
<p>​	(4)hadoop根目录下再次执行jar程序</p>
<pre><code>[hadoop@hadoop01 hadoop-2.7.2]$ bin/yarn jar /opt/app/hbase-1.3.1/lib/hbase-server-1.3.1.jar
---------------------------------------------------------------
An example program must be given as the first argument.
Valid program names are:
  CellCounter: Count cells in HBase table.
  WALPlayer: Replay WAL files.
  completebulkload: Complete a bulk data load.
  copytable: Export a table from local cluster to peer cluster.
  export: Write table data to HDFS.
  exportsnapshot: Export the specific snapshot to a given FileSystem.
  import: Import data written by Export.
  importtsv: Import data in TSV format.
  rowcounter: Count rows in HBase table.
  verifyrep: Compare the data from tables in two different clusters. WARNING: It doesn't work for incrementColumnValues'd cells since the timestamp is changed after being appended to the log.
</code></pre>
<p>使用rowcounter</p>
<pre><code>bin/yarn jar /opt/app/hbase-1.3.1/lib/hbase-server-1.3.1.jar rowcounter hadoop
--------------------------------------------------------------
... ...
                ROWS=5
        File Input Format Counters
                Bytes Read=0
        File Output Format Counters
                Bytes Written=0

</code></pre>
<h4><a id="_104"></a>小案例</h4>
<p>把HBASE中的数据通过mapreduce读取出来, 之后再写入HBASE中</p>
<ol>
<li>修改maven的配置文件</li>
</ol>
<pre><code>&lt;!-- https://mvnrepository.com/artifact/org.apache.hadoop/hadoop-client --&gt;
&lt;dependency&gt;
    &lt;groupId&gt;org.apache.hadoop&lt;/groupId&gt;
    &lt;artifactId&gt;hadoop-client&lt;/artifactId&gt;
    &lt;version&gt;2.7.2&lt;/version&gt;
&lt;/dependency&gt;
</code></pre>
<ol start="2">
<li>创建一张信息表</li>
</ol>
<pre><code>create 'stuinfo','f1','f2','f3'
</code></pre>
<ol start="3">
<li>添加测试数据</li>
</ol>
<pre><code>put 'stuinfo','1001','f1:name','zhangsan'
put 'stuinfo','1002','f1:name','lisi'
put 'stuinfo','1003','f1:name','wangwu'
</code></pre>
<ol start="4">
<li>创建要拷贝数据的空表</li>
</ol>
<pre><code>create 'stuinfocp' , 'info'
</code></pre>
<ol start="5">
<li>map端代码</li>
</ol>
<pre><code>package com.wch.hbase2hbase;

import java.io.IOException;

import org.apache.hadoop.hbase.Cell;
import org.apache.hadoop.hbase.CellUtil;
import org.apache.hadoop.hbase.client.Put;
import org.apache.hadoop.hbase.client.Result;
import org.apache.hadoop.hbase.io.ImmutableBytesWritable;
import org.apache.hadoop.hbase.mapreduce.TableMapper;
import org.apache.hadoop.hbase.util.Bytes;

public class ToHbaseMap extends TableMapper&lt;ImmutableBytesWritable, Put&gt; {
	@Override
	protected void map(ImmutableBytesWritable key, Result value, Context context)
			throws IOException, InterruptedException {
		// TODO Auto-generated method stub
		super.map(key, value, context);

		Put put = new Put(key.get());

		Cell[] cells = value.rawCells();

		for (Cell cell : cells) {
			if ("info".equals(Bytes.toString(CellUtil.cloneFamily(cell)))) {
				if ("name".equals(Bytes.toString(CellUtil.cloneQualifier(cell)))) {
					put.add(cell);
				}
			}

		}

		context.write(key, put);
	}
}
</code></pre>
<ol start="6">
<li>driver主函数代码</li>
</ol>
<pre><code>package com.wch.hbase2hbase;

import java.io.IOException;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.hbase.HBaseConfiguration;
import org.apache.hadoop.hbase.client.Put;
import org.apache.hadoop.hbase.client.Scan;
import org.apache.hadoop.hbase.io.ImmutableBytesWritable;
import org.apache.hadoop.hbase.mapreduce.TableMapReduceUtil;
import org.apache.hadoop.mapreduce.Job;

public class Driver {
	public static void main(String[] args) throws IOException, ClassNotFoundException, InterruptedException {
		Configuration conf = HBaseConfiguration.create();

		Job job = Job.getInstance(conf);

		job.setJarByClass(Driver.class);

		Scan scan = new Scan();

		TableMapReduceUtil.initTableMapperJob("stuinfo", // input table
				scan, // Scan instance to control CF and attribute selection
				ToHbaseMap.class, // mapper class
				ImmutableBytesWritable.class, // mapper output key
				Put.class, // mapper output value
				job);
		TableMapReduceUtil.initTableReducerJob("stuinfocp", // output table
				null, // reducer class
				job);
		job.setNumReduceTasks(1);

		boolean b = job.waitForCompletion(true);
		System.exit(b?0:1);

	}
}
</code></pre>
<ol start="7">
<li>上传至Linux中</li>
<li>在hadoop根目录下运行</li>
</ol>
<pre><code>bin/yarn jar /data/HB2HB.jar
</code></pre>
<ol start="9">
<li>查看 stuinfocp 表</li>
</ol>
<pre><code>实参 'stuinfocp' 
</code></pre>
<h3><a id="hbase_import_TSV_235"></a>hbase import TSV</h3>
<p>TSV就是一个固定文件的导入工具, 将按照\t分割的文件导入到hbase中</p>
<ol>
<li>创建一个表, 用来接收导入的数据</li>
</ol>
<pre><code>create 'importtsv','info','f1'
</code></pre>
<ol start="2">
<li>准备要准备的数据</li>
</ol>
<pre><code>vi importtsv.tsv
-------------------------------
123     zhangsan        henan   18
456     lisi    shanxi  20
789     wangwu  beijing 28
</code></pre>
<ol start="3">
<li>查看使用方式</li>
</ol>
<pre><code>bin/yarn jar /opt/app/hbase-1.3.1/lib/hbase-server-1.3.1.jar importtsv
</code></pre>
<ol start="4">
<li>上传至HDFS</li>
</ol>
<pre><code>bin/hdfs dfs -put /data/importtsv.tsv /importtsv.tsv
</code></pre>
<ol start="5">
<li>导入数据</li>
</ol>
<pre><code>bin/yarn jar /opt/app/hbase-1.3.1/lib/hbase-server-1.3.1.jar importtsv -Dimporttsv.columns=HBASE_ROW_KEY,info:name,f1:location,info:age importtsv /importtsv.tsv
</code></pre>
<p>​	HBASE_ROW_KEY : rowkey</p>
<p>​	剩下的数据要和列簇一一对应, 给定一个</p>
<h3><a id="hbase_import_CSV_277"></a>hbase import CSV</h3>
<p>CSV格式的文件时按照 " , " 进行分割的</p>
<pre><code>bin/yarn jar /opt/app/hbase-1.3.1/lib/hbase-server-1.3.1.jar importtsv '-Dimporttsv.sperator=,' -Dimporttsv.columns=HBASE_ROW_KEY,info:name,f1:location,info:age importtsv /importcsv
</code></pre>
<h3><a id="rowkey_285"></a>rowkey的热点与表的设计原则</h3>
<h4><a id="_287"></a>热点原理</h4>
<p>假设一个region最多存储2000条记录, 如果rowkey设置成自增长的话, 如果数据存满, 就会触发split操作, 那么0-2000的数据就会分成两个操作, 那么region1里边存放的就是0-1000, region2里边存放的是1001-2000, 继续想表中添加数据, 因为rowkey是自增长的, 所以新来的数据的rowkey是2001, 这条数据会直接存放在region2中, 直到再次触发split操作, 分成region2-1, region2-2, 这样一直执行的话, 前边的region就会闲下来, 造成了资源的浪费.</p>
<p>如果rowkey设计不当, 就会造成大量的region浪费, 这就是rowkey热点. 为了避免热点的情况, 数据应该被写到多个region中, 而不是一个region</p>
<h4><a id="rowkey_293"></a>rowkey长度限制</h4>
<p>rowkey的长度限制, 二进制数据, 可以是任意字符, 最多给64kb, 建议10-100个字节, 但是越短越好, 最好不要超过16个字节. 有两个原因</p>
<ol>
<li>数据都是存储到hbase中的, 按照keyvalue进行存储的, 如果rowkey过长, 超过了100个字节, 假设有1000万条那么1000万条数据中rowkey的字节=100*1000万≈1G, 这样的话, rowkey就占用了很大的空间, 极大浪费了HFile资源</li>
<li>memstore 将缓存数据到内存总, 如果rowkey过大, 内存的利用率就会降低, 将无法缓存更多的数据</li>
</ol>
<h4><a id="rowkey_300"></a>rowkey的设计原则</h4>
<ol>
<li>利用Hash散列将rowkey打乱</li>
<li>UUID+时间戳的方式(联通采用的设计)
<ul>
<li>UUID : 手机号</li>
<li>时间戳: 通话记录</li>
<li>通过建立索引表, 然后将时间戳+UUID反过来</li>
</ul>
</li>
<li>如果hbase就是作为海量存储的, 不是频繁查询的话, rowkey可以采用随机数的方式, 或者是MD5</li>
</ol>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>