---
layout:     post
title:      【HBase】HBase各功能组件、集成MapReduce的方式及数据迁移
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，转载请注明出处。					https://blog.csdn.net/gongxifacai_believe/article/details/81365736				</div>
								            <div id="content_views" class="markdown_views prism-atelier-sulphurpool-light">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2><a id="1HBase_0"></a>1、HBase体系架构</h2>
<p><img src="https://img-blog.csdn.net/20180802180930951?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2dvbmd4aWZhY2FpX2JlbGlldmU=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt=""><br>
各个功能组件阐述如下：<br>
<strong>（1）Client</strong></p>
<ol>
<li>整个HBase集群的访问入口；</li>
<li>使用HBase RPC机制与HMaster和HRegionServer进行通信；</li>
<li>与HMaster进行通信进行管理类操作；</li>
<li>与HRegionServer进行数据读写类操作；</li>
<li>包含访问HBase的接口，并维护cache来加快对HBase的访问。</li>
</ol>
<p><strong>（2）Zookeeper</strong></p>
<ol>
<li>保证任何时候，集群中只有一个HMaster；</li>
<li>存贮所有HRegion的寻址入口；</li>
<li>实时监控HRegion Server的上线和下线信息，并实时通知给HMaster；</li>
<li>存储HBase的schema和table元数据；</li>
<li>Zookeeper Quorum存储Meta表地址、HMaster地址。</li>
</ol>
<p><strong>（3）HMaster</strong></p>
<ol>
<li>HMaster没有单点故障问题，HBase中可以启动多个HMaster，通过Zookeeper的Master Election机制保证总有一个Master在运行，主要负责Table和Region的管理工作。</li>
<li>管理用户对table的增删改查操作；</li>
<li>管理HRegionServer的负载均衡，调整Region分布；</li>
<li>Region Split后，负责新Region的分布；</li>
<li>在HRegionServer停机后，负责失效HRegionServer上Region迁移工作。</li>
</ol>
<p><strong>（4）HRegion Server</strong></p>
<ol>
<li>维护HRegion，处理对这些HRegion的IO请求，向HDFS文件系统中读写数据；</li>
<li>负责切分在运行过程中变得过大的HRegion；</li>
<li>Client访问hbase上数据的过程并不需要master参与（寻址访问Zookeeper和HRegion Server，数据读写访问HRegione Server），HMaster仅仅维护者table和Region的元数据信息，负载很低。</li>
</ol>
<p><strong>（5）ZooKeeper</strong></p>
<ol>
<li>HBase 依赖ZooKeeper；</li>
<li>默认情况下，HBase 管理 ZooKeeper 实例，比如， 启动或者停止ZooKeeper；</li>
<li>HMaster与HRegionServers 启动时会向ZooKeeper注册；</li>
<li>Zookeeper的引入使得HMaster不再是单点故障。</li>
</ol>
<h2><a id="2HBaseMapReduce_42"></a>2、HBase集成MapReduce</h2>
<p>（1）添加环境变量</p>
<pre><code>export HBASE_HOME=/opt/modules/hbase-0.98.6-hadoop2
export HADOOP_HOME=/opt/modules/hadoop-2.5.0
HADOOP_CLASSPATH=`${HBASE_HOME}/bin/hbase mapredcp` $HADOOP_HOME/bin/yarn jar $HADOOP_HOME/jars/hbase-mr-user2basic.jar
</code></pre>
<p>（2）例：HBase集成MapReduce，将user表中的部分数据导出到basic表中。</p>
<p>User2BasicMapReduce.java</p>
<pre><code>package com.beifeng.senior.hadoop.hbase;

import java.io.IOException;
import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.conf.Configured;
import org.apache.hadoop.hbase.Cell;
import org.apache.hadoop.hbase.CellUtil;
import org.apache.hadoop.hbase.HBaseConfiguration;
import org.apache.hadoop.hbase.client.Mutation;
import org.apache.hadoop.hbase.client.Put;
import org.apache.hadoop.hbase.client.Result;
import org.apache.hadoop.hbase.client.Scan;
import org.apache.hadoop.hbase.io.ImmutableBytesWritable;
import org.apache.hadoop.hbase.mapreduce.TableMapReduceUtil;
import org.apache.hadoop.hbase.mapreduce.TableMapper;
import org.apache.hadoop.hbase.mapreduce.TableReducer;
import org.apache.hadoop.hbase.util.Bytes;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.Mapper;
import org.apache.hadoop.mapreduce.Reducer;
import org.apache.hadoop.util.Tool;
import org.apache.hadoop.util.ToolRunner;

public class User2BasicMapReduce extends Configured implements Tool {
	
	// Mapper Class
	public static class ReadUserMapper extends TableMapper&lt;Text, Put&gt; {

		private Text mapOutputKey = new Text();

		@Override
		public void map(ImmutableBytesWritable key, Result value,
				Mapper&lt;ImmutableBytesWritable, Result, Text, Put&gt;.Context context)
						throws IOException, InterruptedException {
			// get rowkey
			String rowkey = Bytes.toString(key.get());

			// set
			mapOutputKey.set(rowkey);

			// --------------------------------------------------------
			Put put = new Put(key.get());

			// iterator
			for (Cell cell : value.rawCells()) {
				// add family : info
				if ("info".equals(Bytes.toString(CellUtil.cloneFamily(cell)))) {
					// add column: name
					if ("name".equals(Bytes.toString(CellUtil.cloneQualifier(cell)))) {
						put.add(cell);
					}
					// add column : age
					if ("age".equals(Bytes.toString(CellUtil.cloneQualifier(cell)))) {
						put.add(cell);
					}
				}
			}

			// context write
			context.write(mapOutputKey, put);
		}
	}

	// Reducer Class
	public static class WriteBasicReducer extends TableReducer&lt;Text, Put, //
	ImmutableBytesWritable&gt; {

		@Override
		public void reduce(Text key, Iterable&lt;Put&gt; values,
				Reducer&lt;Text, Put, ImmutableBytesWritable, Mutation&gt;.Context context)
						throws IOException, InterruptedException {
			for(Put put: values){
				context.write(null, put);
			}
		}
	}

	// Driver
	public int run(String[] args) throws Exception {
		
		// create job
		Job job = Job.getInstance(this.getConf(), this.getClass().getSimpleName());
		
		// set run job class
		job.setJarByClass(this.getClass());
		
		// set job
		Scan scan = new Scan();
		scan.setCaching(500);        // 1 is the default in Scan, which will be bad for MapReduce jobs
		scan.setCacheBlocks(false);  // don't set to true for MR jobs
		// set other scan attrs

		// set input and set mapper
		TableMapReduceUtil.initTableMapperJob(
		  "user",        // input table
		  scan,               // Scan instance to control CF and attribute selection
		  ReadUserMapper.class,     // mapper class
		  Text.class,         // mapper output key
		  Put.class,  // mapper output value
		  job //
		 );
		
		// set reducer and output
		TableMapReduceUtil.initTableReducerJob(
		  "basic",        // output table
		  WriteBasicReducer.class,    // reducer class
		  job//
		 );
		
		job.setNumReduceTasks(1);   // at least one, adjust as required
		
		// submit job
		boolean isSuccess = job.waitForCompletion(true) ;
		
		return isSuccess ? 0 : 1;
	}
	
	public static void main(String[] args) throws Exception {
		// get configuration
		Configuration configuration = HBaseConfiguration.create();
		
		// submit job
		int status = ToolRunner.run(configuration,new User2BasicMapReduce(),args) ;
		
		// exit program
		System.exit(status);
	}

}

</code></pre>
<h2><a id="3HBase_189"></a>3、将数据迁移进HBase</h2>
<blockquote>
<ol>
<li>使用HBase Put API</li>
</ol>
</blockquote>
<ol start="2">
<li>使用HBase bulk load tool</li>
<li>使用自定义的MapReduce任务</li>
</ol>
<p><strong>（1）HBase Bulk Load工具</strong><br>
通常 MapReduce 在写HBase时使用的是 TableOutputFormat 方式，在reduce中直接生成put对象写入HBase，该方式在大数据量写入时效率低下（HBase会block写入，频繁进行flush，split，compact等大量IO操作），并对HBase节点的稳定性造成一定的影响（GC时间过长，响应变慢，导致节点超时退出，并引起一系列连锁反应）。<br>
HBase支持 bulk load 的入库方式，它是利用hbase的数据信息按照特定格式存储在hdfs内这一原理，直接在HDFS中生成持久化的HFile数据格式文件，然后上传至合适位置，即完成巨量数据快速入库的办法。配合mapreduce完成，高效便捷，而且不占用region资源，增添负载，在大数据量写入时能极大的提高写入效率，并降低对HBase节点的写入压力。<br>
通过使用先生成HFile，然后再BulkLoad到Hbase的方式来替代之前直接调用HTableOutputFormat的方法有如下的好处：</p>
<ol>
<li>消除了对HBase集群的插入压力；</li>
<li>提高了Job的运行速度，降低了Job的执行时间。</li>
</ol>
<p><strong>Bulk Load的工作流程：</strong></p>
<blockquote>
<ol>
<li>mapreduce将*.cvs文件转换为hfile文件；</li>
<li>bulk loada将hfile文件加载进HBase表中。</li>
</ol>
</blockquote>
<p><strong>执行命令如下：</strong></p>
<pre><code>export HBASE_HOME=/opt/modules/hbase-0.98.6-hadoop2
export HADOOP_HOME=/opt/modules/hadoop-2.5.0
HADOOP_CLASSPATH=`${HBASE_HOME}/bin/hbase mapredcp`:${HBASE_HOME}/conf \
	${HADOOP_HOME}/bin/yarn jar \
	${HBASE_HOME}/lib/hbase-server-0.98.6-hadoop2.jar importtsv \
	-Dimporttsv.columns=HBASE_ROW_KEY,\
	info:name,info:age,info:sex,info:address,info:phone \
	student \
	hdfs://hadoop-senior.ibeifeng.com:8020/user/beifeng/hbase/importtsv
</code></pre>
<h2><a id="4HBase_222"></a>4、HBase表的增删改查操作示例</h2>
<p>HBaseOperation.java</p>
<pre><code>package com.beifeng.senior.hadoop.hbase;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.hbase.Cell;
import org.apache.hadoop.hbase.CellUtil;
import org.apache.hadoop.hbase.HBaseConfiguration;
import org.apache.hadoop.hbase.client.Delete;
import org.apache.hadoop.hbase.client.Get;
import org.apache.hadoop.hbase.client.HTable;
import org.apache.hadoop.hbase.client.Put;
import org.apache.hadoop.hbase.client.Result;
import org.apache.hadoop.hbase.client.ResultScanner;
import org.apache.hadoop.hbase.client.Scan;
import org.apache.hadoop.hbase.util.Bytes;
import org.apache.hadoop.io.IOUtils;

/**
 * CRUD Operations
 * 
 */
public class HBaseOperation {

	public static HTable getHTableByTableName(String tableName) throws Exception {
		// Get instance of Default Configuration
		Configuration configuration = HBaseConfiguration.create();

		// Get table instance
		HTable table = new HTable(configuration, tableName);

		return table;

	}

	public void getData() throws Exception {
		String tableName = "user"; // default.user / hbase:meta

		HTable table = getHTableByTableName(tableName);

		// Create Get with rowkey
		Get get = new Get(Bytes.toBytes("10002")); // "10002".toBytes()

		// ==========================================================================
		// add column
		get.addColumn(//
				Bytes.toBytes("info"), //
				Bytes.toBytes("name"));
		get.addColumn(//
				Bytes.toBytes("info"), //
				Bytes.toBytes("age"));

		// Get Data
		Result result = table.get(get);

		// Key : rowkey + cf + c + version
		// Value: value
		for (Cell cell : result.rawCells()) {
			System.out.println(//
					Bytes.toString(CellUtil.cloneFamily(cell)) + ":" //
							+ Bytes.toString(CellUtil.cloneQualifier(cell)) + " -&gt;" //
							+ Bytes.toString(CellUtil.cloneValue(cell)));
		}

		// Table Close
		table.close();

	}

	/**
	 * 建议 tablename &amp; column family -&gt; 常量 , HBaseTableContent
	 * Map&lt;String,Obejct&gt;
	 * @throws Exception
	 */
	public void putData() throws Exception {
		String tableName = "user"; // default.user / hbase:meta

		HTable table = getHTableByTableName(tableName);

		Put put = new Put(Bytes.toBytes("10004"));

		// Add a column with value
		put.add(//
				Bytes.toBytes("info"), //
				Bytes.toBytes("name"), //
				Bytes.toBytes("zhaoliu")//
		);
		put.add(//
				Bytes.toBytes("info"), //
				Bytes.toBytes("age"), //
				Bytes.toBytes(25)//
		);
		put.add(//
				Bytes.toBytes("info"), //
				Bytes.toBytes("address"), //
				Bytes.toBytes("shanghai")//
		);

		table.put(put);

		table.close();
	}

	public void delete() throws Exception {
		String tableName = "user"; // default.user / hbase:meta

		HTable table = getHTableByTableName(tableName);

		Delete delete = new Delete(Bytes.toBytes("10004"));
		/*
		 * delete.deleteColumn(Bytes.toBytes("info"),//
		 * Bytes.toBytes("address"));
		 */
		delete.deleteFamily(Bytes.toBytes("info"));

		table.delete(delete);

		table.close();
	}

	public static void main(String[] args) throws Exception {
		String tableName = "user"; // default.user / hbase:meta

		HTable table = null;
		ResultScanner resultScanner = null;

		try {
			table = getHTableByTableName(tableName);

			Scan scan = new Scan();
			
			// Range
			scan.setStartRow(Bytes.toBytes("10001"));
			scan.setStopRow(Bytes.toBytes("10003")) ;
			
			// Scan scan2 = new Scan(Bytes.toBytes("10001"),Bytes.toBytes("10003"));
			
			// PrefixFilter
			// PageFilter
			// scan.setFilter(filter) ;
						
			// scan.setCacheBlocks(cacheBlocks);
			// scan.setCaching(caching);
						
			// scan.addColumn(family, qualifier)
			// scan.addFamily(family)

			resultScanner = table.getScanner(scan);

			for (Result result : resultScanner) {
				System.out.println(Bytes.toString(result.getRow()));
			//	System.out.println(result);
				for (Cell cell : result.rawCells()) {
					System.out.println(//
							Bytes.toString(CellUtil.cloneFamily(cell)) + ":" //
									+ Bytes.toString(CellUtil.cloneQualifier(cell)) + " -&gt;" //
									+ Bytes.toString(CellUtil.cloneValue(cell)));
				}
				System.out.println("---------------------------------------");
			}

		} catch (Exception e) {
			e.printStackTrace();
		} finally {
			IOUtils.closeStream(resultScanner);
			IOUtils.closeStream(table);
		}
	}
}

</code></pre>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>