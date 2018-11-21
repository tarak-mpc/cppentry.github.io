---
layout:     post
title:      MR(MapReduce)查询hbase数据－用到TableMapper和Scan
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p></p>
<div class="titlepage" style="font-family:Simsun;font-size:14px;line-height:19px;">
<div>
<div>
<h2 class="title" style="color:rgb(153,0,0);line-height:1.3;clear:both;">
7.2. HBase MapReduce Examples</h2>
</div>
</div>
</div>
<div class="section" title="7.2.1. HBase MapReduce Read Example" style="font-family:Simsun;font-size:14px;line-height:19px;">
<div class="titlepage">
<div>
<div>
<h3 class="title" style="color:rgb(153,0,0);line-height:1.3;">
<a name="mapreduce.example.read"></a>7.2.1. HBase MapReduce Read Example</h3>
</div>
</div>
</div>
<p>The following is an example of using HBase as a MapReduce source in read-only manner. Specifically, there is a Mapper instance but no Reducer, and nothing is being emitted from the Mapper. There job would be defined as follows...</p>
<pre class="programlisting" style="line-height:1;background-color:rgb(238,238,238);border:1px solid rgb(204,204,204);">Configuration config = HBaseConfiguration.create();
Job job = new Job(config, "ExampleRead");
job.setJarByClass(MyReadJob.class);     // class that contains mapper

Scan scan = new Scan();
scan.setCaching(500);        // 1 is the default in Scan, which will be bad for MapReduce jobs
scan.setCacheBlocks(false);  // don't set to true for MR jobs
// set other scan attrs
...

TableMapReduceUtil.initTableMapperJob(
  tableName,        // input HBase table name
  scan,             // Scan instance to control CF and attribute selection
  MyMapper.class,   // mapper
  null,             // mapper output key
  null,             // mapper output value
  job);
job.setOutputFormatClass(NullOutputFormat.class);   // because we aren't emitting anything from mapper

boolean b = job.waitForCompletion(true);
if (!b) {
  throw new IOException("error with job!");
}
  </pre>
<p>...and the mapper instance would extend <a class="link" href="http://hbase.apache.org/apidocs/org/apache/hadoop/hbase/mapreduce/TableMapper.html" rel="nofollow">TableMapper</a>...</p>
<pre class="programlisting" style="line-height:1;background-color:rgb(238,238,238);border:1px solid rgb(204,204,204);">public static class MyMapper extends TableMapper&lt;Text, Text&gt; {

  public void map(ImmutableBytesWritable row, Result value, Context context) throws InterruptedException, IOException {
    // process data for the row from the Result instance.
   }
}
    </pre>
<p></p>
</div>
<div class="section" title="7.2.2. HBase MapReduce Read/Write Example" style="font-family:Simsun;font-size:14px;line-height:19px;">
<div class="titlepage">
<div>
<div>
<h3 class="title" style="color:rgb(153,0,0);line-height:1.3;">
<a name="mapreduce.example.readwrite"></a>7.2.2. HBase MapReduce Read/Write Example</h3>
</div>
</div>
</div>
<p>The following is an example of using HBase both as a source and as a sink with MapReduce. This example will simply copy data from one table to another.</p>
<pre class="programlisting" style="line-height:1;background-color:rgb(238,238,238);border:1px solid rgb(204,204,204);">Configuration config = HBaseConfiguration.create();
Job job = new Job(config,"ExampleReadWrite");
job.setJarByClass(MyReadWriteJob.class);    // class that contains mapper

Scan scan = new Scan();
scan.setCaching(500);        // 1 is the default in Scan, which will be bad for MapReduce jobs
scan.setCacheBlocks(false);  // don't set to true for MR jobs
// set other scan attrs

TableMapReduceUtil.initTableMapperJob(
	sourceTable,      // input table
	scan,	          // Scan instance to control CF and attribute selection
	MyMapper.class,   // mapper class
	null,	          // mapper output key
	null,	          // mapper output value
	job);
TableMapReduceUtil.initTableReducerJob(
	targetTable,      // output table
	null,             // reducer class
	job);
job.setNumReduceTasks(0);

boolean b = job.waitForCompletion(true);
if (!b) {
    throw new IOException("error with job!");
}
    </pre>
<p>An explanation is required of what <code class="classname">TableMapReduceUtil</code> is doing, especially with the reducer. <a class="link" href="http://hbase.apache.org/apidocs/org/apache/hadoop/hbase/mapreduce/TableOutputFormat.html" rel="nofollow">TableOutputFormat</a> is
 being used as the outputFormat class, and several parameters are being set on the config (e.g., TableOutputFormat.OUTPUT_TABLE), as well as setting the reducer output key to <code class="classname">ImmutableBytesWritable</code> and reducer value to <code class="classname">Writable</code>.
 These could be set by the programmer on the job and conf, but <code class="classname">TableMapReduceUtil</code> tries to make things easier.</p>
<p>The following is the example mapper, which will create a <code class="classname">Put</code> and matching the input <code class="classname">Result</code> and emit it. Note: this is what the CopyTable utility does.</p>
<p></p>
<pre class="programlisting" style="line-height:1;background-color:rgb(238,238,238);border:1px solid rgb(204,204,204);">public static class MyMapper extends TableMapper&lt;ImmutableBytesWritable, Put&gt;  {

	public void map(ImmutableBytesWritable row, Result value, Context context) throws IOException, InterruptedException {
		// this example is just copying the data from the source table...
   		context.write(row, resultToPut(row,value));
   	}

  	private static Put resultToPut(ImmutableBytesWritable key, Result result) throws IOException {
  		Put put = new Put(key.get());
 		for (KeyValue kv : result.raw()) {
			put.add(kv);
		}
		return put;
   	}
}
    </pre>
<p></p>
<p>There isn't actually a reducer step, so <code class="classname">TableOutputFormat</code> takes care of sending the <code class="classname">Put</code> to the target table.</p>
<p></p>
<p>This is just an example, developers could choose not to use <code class="classname">TableOutputFormat</code> and connect to the target table themselves.</p>
<p></p>
</div>
<div class="section" title="7.2.3. HBase MapReduce Read/Write Example With Multi-Table Output" style="font-family:Simsun;font-size:14px;line-height:19px;">
<div class="titlepage">
<div>
<div>
<h3 class="title" style="color:rgb(153,0,0);line-height:1.3;">
<a name="mapreduce.example.readwrite.multi"></a>7.2.3. HBase MapReduce Read/Write Example With Multi-Table Output</h3>
</div>
</div>
</div>
<p>TODO: example for <code class="classname">MultiTableOutputFormat</code>.</p>
</div>
<div class="section" title="7.2.4. HBase MapReduce Summary to HBase Example" style="font-family:Simsun;font-size:14px;line-height:19px;">
<div class="titlepage">
<div>
<div>
<h3 class="title" style="color:rgb(153,0,0);line-height:1.3;">
<a name="mapreduce.example.summary"></a>7.2.4. HBase MapReduce Summary to HBase Example</h3>
</div>
</div>
</div>
<p>The following example uses HBase as a MapReduce source and sink with a summarization step. This example will count the number of distinct instances of a value in a table and write those summarized counts in another table.</p>
<pre class="programlisting" style="line-height:1;background-color:rgb(238,238,238);border:1px solid rgb(204,204,204);">Configuration config = HBaseConfiguration.create();
Job job = new Job(config,"ExampleSummary");
job.setJarByClass(MySummaryJob.class);     // class that contains mapper and reducer

Scan scan = new Scan();
scan.setCaching(500);        // 1 is the default in Scan, which will be bad for MapReduce jobs
scan.setCacheBlocks(false);  // don't set to true for MR jobs
// set other scan attrs

TableMapReduceUtil.initTableMapperJob(
	sourceTable,        // input table
	scan,               // Scan instance to control CF and attribute selection
	MyMapper.class,     // mapper class
	Text.class,         // mapper output key
	IntWritable.class,  // mapper output value
	job);
TableMapReduceUtil.initTableReducerJob(
	targetTable,        // output table
	MyTableReducer.class,    // reducer class
	job);
job.setNumReduceTasks(1);   // at least one, adjust as required

boolean b = job.waitForCompletion(true);
if (!b) {
	throw new IOException("error with job!");
}
    </pre>
<p>In this example mapper a column with a String-value is chosen as the value to summarize upon. This value is used as the key to emit from the mapper, and an <code class="classname">IntWritable</code> represents an instance counter.</p>
<pre class="programlisting" style="line-height:1;background-color:rgb(238,238,238);border:1px solid rgb(204,204,204);">public static class MyMapper extends TableMapper&lt;Text, IntWritable&gt;  {
	public static final byte[] CF = "cf".getBytes();
	public static final byte[] ATTR1 = "attr1".getBytes();

	private final IntWritable ONE = new IntWritable(1);
   	private Text text = new Text();

   	public void map(ImmutableBytesWritable row, Result value, Context context) throws IOException, InterruptedException {
        	String val = new String(value.getValue(CF, ATTR1));
          	text.set(val);     // we can only emit Writables...

        	context.write(text, ONE);
   	}
}
    </pre>
<p>In the reducer, the "ones" are counted (just like any other MR example that does this), and then emits a <code class="classname">Put</code>.</p>
<pre class="programlisting" style="line-height:1;background-color:rgb(238,238,238);border:1px solid rgb(204,204,204);">public static class MyTableReducer extends TableReducer&lt;Text, IntWritable, ImmutableBytesWritable&gt;  {
	public static final byte[] CF = "cf".getBytes();
	public static final byte[] COUNT = "count".getBytes();

 	public void reduce(Text key, Iterable&lt;IntWritable&gt; values, Context context) throws IOException, InterruptedException {
    		int i = 0;
    		for (IntWritable val : values) {
    			i += val.get();
    		}
    		Put put = new Put(Bytes.toBytes(key.toString()));
    		put.add(CF, COUNT, Bytes.toBytes(i));

    		context.write(null, put);
   	}
}
    </pre>
<p></p>
</div>
<div class="section" title="7.2.5. HBase MapReduce Summary to File Example" style="font-family:Simsun;font-size:14px;line-height:19px;">
<div class="titlepage">
<div>
<div>
<h3 class="title" style="color:rgb(153,0,0);line-height:1.3;">
<a name="mapreduce.example.summary.file"></a>7.2.5. HBase MapReduce Summary to File Example</h3>
</div>
</div>
</div>
<p>This very similar to the summary example above, with exception that this is using HBase as a MapReduce source but HDFS as the sink. The differences are in the job setup and in the reducer. The mapper remains the same.</p>
<pre class="programlisting" style="line-height:1;background-color:rgb(238,238,238);border:1px solid rgb(204,204,204);">Configuration config = HBaseConfiguration.create();
Job job = new Job(config,"ExampleSummaryToFile");
job.setJarByClass(MySummaryFileJob.class);     // class that contains mapper and reducer

Scan scan = new Scan();
scan.setCaching(500);        // 1 is the default in Scan, which will be bad for MapReduce jobs
scan.setCacheBlocks(false);  // don't set to true for MR jobs
// set other scan attrs

TableMapReduceUtil.initTableMapperJob(
	sourceTable,        // input table
	scan,               // Scan instance to control CF and attribute selection
	MyMapper.class,     // mapper class
	Text.class,         // mapper output key
	IntWritable.class,  // mapper output value
	job);
job.setReducerClass(MyReducer.class);    // reducer class
job.setNumReduceTasks(1);    // at least one, adjust as required
FileOutputFormat.setOutputPath(job, new Path("/tmp/mr/mySummaryFile"));  // adjust directories as required

boolean b = job.waitForCompletion(true);
if (!b) {
	throw new IOException("error with job!");
}
    </pre>
As stated above, the previous Mapper can run unchanged with this example. As for the Reducer, it is a "generic" Reducer instead of extending TableMapper and emitting Puts.
<pre class="programlisting" style="line-height:1;background-color:rgb(238,238,238);border:1px solid rgb(204,204,204);"> public static class MyReducer extends Reducer&lt;Text, IntWritable, Text, IntWritable&gt;  {

	public void reduce(Text key, Iterable&lt;IntWritable&gt; values, Context context) throws IOException, InterruptedException {
		int i = 0;
		for (IntWritable val : values) {
			i += val.get();
		}
		context.write(key, new IntWritable(i));
	}
}
    </pre>
</div>
<div class="section" title="7.2.6. HBase MapReduce Summary to HBase Without Reducer" style="font-family:Simsun;font-size:14px;line-height:19px;">
<div class="titlepage">
<div>
<div>
<h3 class="title" style="color:rgb(153,0,0);line-height:1.3;">
<a name="mapreduce.example.summary.noreducer"></a>7.2.6. HBase MapReduce Summary to HBase Without Reducer</h3>
</div>
</div>
</div>
<p>It is also possible to perform summaries without a reducer - if you use HBase as the reducer.</p>
<p>An HBase target table would need to exist for the job summary. The HTable method <code class="code">incrementColumnValue</code> would be used to atomically increment values. From a performance perspective, it might make sense to keep a Map of values with
 their values to be incremeneted for each map-task, and make one update per key at during the <code class="code">cleanup</code> method of the mapper. However, your milage may vary depending on the number of rows to be processed and unique keys.</p>
<p>In the end, the summary results are in HBase.</p>
</div>
<div class="section" title="7.2.7. HBase MapReduce Summary to RDBMS" style="font-family:Simsun;font-size:14px;line-height:19px;">
<div class="titlepage">
<div>
<div>
<h3 class="title" style="color:rgb(153,0,0);line-height:1.3;">
<a name="mapreduce.example.summary.rdbms"></a>7.2.7. HBase MapReduce Summary to RDBMS</h3>
</div>
</div>
</div>
<p>Sometimes it is more appropriate to generate summaries to an RDBMS. For these cases, it is possible to generate summaries directly to an RDBMS via a custom reducer. The <code class="code">setup</code> method can connect to an RDBMS (the connection information
 can be passed via custom parameters in the context) and the cleanup method can close the connection.</p>
<p>It is critical to understand that number of reducers for the job affects the summarization implementation, and you'll have to design this into your reducer. Specifically, whether it is designed to run as a singleton (one reducer) or multiple reducers. Neither
 is right or wrong, it depends on your use-case. Recognize that the more reducers that are assigned to the job, the more simultaneous connections to the RDBMS will be created - this will scale, but only to a point.</p>
<pre class="programlisting" style="line-height:1;background-color:rgb(238,238,238);border:1px solid rgb(204,204,204);"> public static class MyRdbmsReducer extends Reducer&lt;Text, IntWritable, Text, IntWritable&gt;  {

	private Connection c = null;

	public void setup(Context context) {
  		// create DB connection...
  	}

	public void reduce(Text key, Iterable&lt;IntWritable&gt; values, Context context) throws IOException, InterruptedException {
		// do summarization
		// in this example the keys are Text, but this is just an example
	}

	public void cleanup(Context context) {
  		// close db connection
  	}

}
    </pre>
<p>In the end, the summary results are written to your RDBMS table/s.</p>
</div>
<br><p></p>
<p></p>
<p style="font-family:Arial;font-size:14px;line-height:26px;">
首先，可以设置scan的startRow, stopRow, filter等属性。于是两种方案：</p>
<p style="font-family:Arial;font-size:14px;line-height:26px;">
1.设置scan的filter，然后执行mapper，再reducer成一份结果</p>
<p style="font-family:Arial;font-size:14px;line-height:26px;">
2.不用filter过滤，将filter做的事传给mapper做</p>
<p style="font-family:Arial;font-size:14px;line-height:26px;">
进行了测试，前者在执行较少量scan记录的时候效率较后者高，但是执行的scan数量多了，便容易导致超时无返回而退出的情况。而为了实现后者，学会了如何向mapper任务中传递参数，走了一点弯路。</p>
<p style="font-family:Arial;font-size:14px;line-height:26px;">
最后的一点思考是，用后者效率仍然不高，即便可用前者时效率也不高，因为默认的tablemapper是将对一个region的scan任务放在了一个mapper里，而我一个region有2G多，而我查的数据只占七八个region。于是，想能不能不以region为单位算做mapper，如果不能改，那只有用MR直接操作HBase底层HDFS文件了，这个，…，待研究。</p>
<p style="font-family:Arial;font-size:14px;line-height:26px;">
上代码（为了保密，将表名啊，列名列族名啊都改了一下，有改漏的，大家当做没看见啊，另：主要供大家参考下方法，即用mr来查询海量hbase数据，还有如何向mapper传参数）：</p>
<div class="dp-highlighter bg_java" style="font-family:Consolas, 'Courier New', Courier, mono, serif;background-color:rgb(231,229,220);overflow:auto;line-height:26px;">
<div class="bar">
<div class="tools" style="font-size:9px;line-height:normal;font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;color:#C0C0C0;background-color:rgb(248,248,248);border-left-width:3px;border-left-style:solid;border-left-color:rgb(108,226,108);">
<strong>[java]</strong> <a href="http://blog.csdn.net/liuxingjiaofu/article/details/7197245#" rel="nofollow" class="ViewSource" title="view plain" style="color:rgb(160,160,160);text-decoration:none;background-color:inherit;border:none;font-size:9px;display:inline-block;width:16px;text-indent:-2000px;">view
 plain</a><a href="http://blog.csdn.net/liuxingjiaofu/article/details/7197245#" rel="nofollow" class="CopyToClipboard" title="copy" style="color:rgb(160,160,160);text-decoration:none;background-color:inherit;border:none;font-size:9px;display:inline-block;width:16px;text-indent:-2000px;">copy</a>
<div style="width:18px;z-index:99;">
</div>
</div>
</div>
<ol start="1" class="dp-j" style="border:none;background-color:rgb(255,255,255);color:rgb(92,92,92);"><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">package</span><span style="border:none;background-color:inherit;"> mapreduce.hbase;  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">import</span><span style="border:none;background-color:inherit;"> java.io.IOException;  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">import</span><span style="border:none;background-color:inherit;"> mapreduce.HDFS_File;  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">import</span><span style="border:none;background-color:inherit;"> org.apache.commons.logging.Log;  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">import</span><span style="border:none;background-color:inherit;"> org.apache.commons.logging.LogFactory;  </span></span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">import</span><span style="border:none;background-color:inherit;"> org.apache.hadoop.conf.Configuration;  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">import</span><span style="border:none;background-color:inherit;"> org.apache.hadoop.fs.Path;  </span></span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">import</span><span style="border:none;background-color:inherit;"> org.apache.hadoop.hbase.HBaseConfiguration;  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">import</span><span style="border:none;background-color:inherit;"> org.apache.hadoop.hbase.client.Result;  </span></span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">import</span><span style="border:none;background-color:inherit;"> org.apache.hadoop.hbase.client.Scan;  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">import</span><span style="border:none;background-color:inherit;"> org.apache.hadoop.hbase.filter.Filter;  </span></span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">import</span><span style="border:none;background-color:inherit;"> org.apache.hadoop.hbase.filter.FilterList;  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">import</span><span style="border:none;background-color:inherit;"> org.apache.hadoop.hbase.filter.SingleColumnValueFilter;  </span></span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">import</span><span style="border:none;background-color:inherit;"> org.apache.hadoop.hbase.filter.CompareFilter.CompareOp;  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">import</span><span style="border:none;background-color:inherit;"> org.apache.hadoop.hbase.io.ImmutableBytesWritable;  </span></span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">import</span><span style="border:none;background-color:inherit;"> org.apache.hadoop.hbase.mapreduce.TableMapReduceUtil;  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">import</span><span style="border:none;background-color:inherit;"> org.apache.hadoop.hbase.mapreduce.TableMapper;  </span></span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">import</span><span style="border:none;background-color:inherit;"> org.apache.hadoop.hbase.util.Bytes;  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">import</span><span style="border:none;background-color:inherit;"> org.apache.hadoop.io.Text;  </span></span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">import</span><span style="border:none;background-color:inherit;"> org.apache.hadoop.mapreduce.Job;  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">import</span><span style="border:none;background-color:inherit;"> org.apache.hadoop.mapreduce.Mapper.Context;  </span></span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">import</span><span style="border:none;background-color:inherit;"> org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;">/**</span> </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;"> * 用MR对HBase进行查找，给出Scan的条件诸如startkey endkey;以及filters用来过滤掉不符合条件的记录 LicenseTable</span> </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;"> * 的 RowKey 201101010000000095\xE5\xAE\x81WDTLBZ</span> </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;"> * </span> </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;"> * @author Wallace</span> </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;"> * </span> </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;"> */</span><span style="border:none;background-color:inherit;">  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span class="annotation" style="border:none;color:rgb(100,100,100);background-color:inherit;">@SuppressWarnings</span><span style="border:none;background-color:inherit;">(</span><span class="string" style="border:none;color:#0000FF;background-color:inherit;">"unused"</span><span style="border:none;background-color:inherit;">)  </span></span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">public</span><span style="border:none;background-color:inherit;"> </span><span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">class</span><span style="border:none;background-color:inherit;"> MRSearchAuto {  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">    <span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">private</span><span style="border:none;background-color:inherit;"> </span><span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">static</span><span style="border:none;background-color:inherit;"> </span><span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">final</span><span style="border:none;background-color:inherit;"> Log LOG = LogFactory.getLog(MRSearchAuto.</span><span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">class</span><span style="border:none;background-color:inherit;">);  </span></span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">    <span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">private</span><span style="border:none;background-color:inherit;"> </span><span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">static</span><span style="border:none;background-color:inherit;"> String TABLE_NAME = </span><span class="string" style="border:none;color:#0000FF;background-color:inherit;">"tablename"</span><span style="border:none;background-color:inherit;">;  </span></span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">    <span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">private</span><span style="border:none;background-color:inherit;"> </span><span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">static</span><span style="border:none;background-color:inherit;"> </span><span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">byte</span><span style="border:none;background-color:inherit;">[] FAMILY_NAME = Bytes.toBytes(</span><span class="string" style="border:none;color:#0000FF;background-color:inherit;">"cfname"</span><span style="border:none;background-color:inherit;">);  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">    <span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">private</span><span style="border:none;background-color:inherit;"> </span><span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">static</span><span style="border:none;background-color:inherit;"> </span><span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">byte</span><span style="border:none;background-color:inherit;">[][] QUALIFIER_NAME = { Bytes.toBytes(</span><span class="string" style="border:none;color:#0000FF;background-color:inherit;">"col1"</span><span style="border:none;background-color:inherit;">),  </span></span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">            Bytes.toBytes(<span class="string" style="border:none;color:#0000FF;background-color:inherit;">"col2"</span><span style="border:none;background-color:inherit;">), Bytes.toBytes(</span><span class="string" style="border:none;color:#0000FF;background-color:inherit;">"col3"</span><span style="border:none;background-color:inherit;">) };  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">    <span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">public</span><span style="border:none;background-color:inherit;"> </span><span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">static</span><span style="border:none;background-color:inherit;"> </span><span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">class</span><span style="border:none;background-color:inherit;"> SearchMapper </span><span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">extends</span><span style="border:none;background-color:inherit;">  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">            TableMapper&lt;ImmutableBytesWritable, Text&gt; {  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">        <span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">private</span><span style="border:none;background-color:inherit;"> </span><span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">int</span><span style="border:none;background-color:inherit;"> numOfFilter = </span><span class="number" style="border:none;color:rgb(192,0,0);background-color:inherit;">0</span><span style="border:none;background-color:inherit;">;  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">        <span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">private</span><span style="border:none;background-color:inherit;"> Text word = </span><span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">new</span><span style="border:none;background-color:inherit;"> Text();  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">        String[] strConditionStrings = <span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">new</span><span style="border:none;background-color:inherit;"> String[]{</span><span class="string" style="border:none;color:#0000FF;background-color:inherit;">""</span><span style="border:none;background-color:inherit;">,</span><span class="string" style="border:none;color:#0000FF;background-color:inherit;">""</span><span style="border:none;background-color:inherit;">,</span><span class="string" style="border:none;color:#0000FF;background-color:inherit;">""</span><span style="border:none;background-color:inherit;">}</span><span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;">/* { "新C87310", "10", "2" } */</span><span style="border:none;background-color:inherit;">;  </span></span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">        <span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;">/*</span> </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;">         * private void init(Configuration conf) throws IOException,</span> </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;">         * InterruptedException { strConditionStrings[0] =</span> </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;">         * conf.get("search.license").trim(); strConditionStrings[1] =</span> </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;">         * conf.get("search.carColor").trim(); strConditionStrings[2] =</span> </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;">         * conf.get("search.direction").trim(); LOG.info("license: " +</span> </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;">         * strConditionStrings[0]); }</span> </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;">         */</span><span style="border:none;background-color:inherit;">  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">        <span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">protected</span><span style="border:none;background-color:inherit;"> </span><span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">void</span><span style="border:none;background-color:inherit;"> setup(Context context) </span><span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">throws</span><span style="border:none;background-color:inherit;"> IOException,  </span></span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">                InterruptedException {  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">            strConditionStrings[<span class="number" style="border:none;color:rgb(192,0,0);background-color:inherit;">0</span><span style="border:none;background-color:inherit;">] = context.getConfiguration().get(</span><span class="string" style="border:none;color:#0000FF;background-color:inherit;">"search.license"</span><span style="border:none;background-color:inherit;">).trim();  </span></span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">            strConditionStrings[<span class="number" style="border:none;color:rgb(192,0,0);background-color:inherit;">1</span><span style="border:none;background-color:inherit;">] = context.getConfiguration().get(</span><span class="string" style="border:none;color:#0000FF;background-color:inherit;">"search.color"</span><span style="border:none;background-color:inherit;">).trim();  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">            strConditionStrings[<span class="number" style="border:none;color:rgb(192,0,0);background-color:inherit;">2</span><span style="border:none;background-color:inherit;">] = context.getConfiguration().get(</span><span class="string" style="border:none;color:#0000FF;background-color:inherit;">"search.direction"</span><span style="border:none;background-color:inherit;">).trim();  </span></span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">        }  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">        <span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">protected</span><span style="border:none;background-color:inherit;"> </span><span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">void</span><span style="border:none;background-color:inherit;"> map(ImmutableBytesWritable key, Result value,  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">                Context context) <span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">throws</span><span style="border:none;background-color:inherit;"> InterruptedException, IOException {  </span></span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">            String string = <span class="string" style="border:none;color:#0000FF;background-color:inherit;">""</span><span style="border:none;background-color:inherit;">;  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">            String tempString;  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">            <span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;">/**/</span><span style="border:none;background-color:inherit;">  </span></span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">            <span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">for</span><span style="border:none;background-color:inherit;"> (</span><span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">int</span><span style="border:none;background-color:inherit;"> i = </span><span class="number" style="border:none;color:rgb(192,0,0);background-color:inherit;">0</span><span style="border:none;background-color:inherit;">; i &lt; </span><span class="number" style="border:none;color:rgb(192,0,0);background-color:inherit;">1</span><span style="border:none;background-color:inherit;">; i++) {  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">                <span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;">// /在此map里进行filter的功能</span><span style="border:none;background-color:inherit;">  </span></span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">                tempString = Text.decode(value.getValue(FAMILY_NAME,  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">                        QUALIFIER_NAME[i]));  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">                <span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">if</span><span style="border:none;background-color:inherit;"> (tempString.equals(</span><span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;">/* strConditionStrings[i] */</span><span class="string" style="border:none;color:#0000FF;background-color:inherit;">"新C87310"</span><span style="border:none;background-color:inherit;">)) {  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">                    LOG.info(<span class="string" style="border:none;color:#0000FF;background-color:inherit;">"新C87310. conf: "</span><span style="border:none;background-color:inherit;"> + strConditionStrings[</span><span class="number" style="border:none;color:rgb(192,0,0);background-color:inherit;">0</span><span style="border:none;background-color:inherit;">]);  </span></span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">                    <span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">if</span><span style="border:none;background-color:inherit;"> (tempString.equals(strConditionStrings[i])) {  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">                        string = string + tempString + <span class="string" style="border:none;color:#0000FF;background-color:inherit;">" "</span><span style="border:none;background-color:inherit;">;  </span></span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">                    } <span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">else</span><span style="border:none;background-color:inherit;"> {  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">                        <span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">return</span><span style="border:none;background-color:inherit;">;  </span></span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">                    }  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">                }  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">                <span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">else</span><span style="border:none;background-color:inherit;"> {  </span></span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">                    <span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">return</span><span style="border:none;background-color:inherit;">;  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">                }  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">            }  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">            word.set(string);  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">            context.write(<span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">null</span><span style="border:none;background-color:inherit;">, word);  </span></span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">        }  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">    }  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">    <span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">public</span><span style="border:none;background-color:inherit;"> </span><span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">void</span><span style="border:none;background-color:inherit;"> searchHBase(</span><span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">int</span><span style="border:none;background-color:inherit;"> numOfDays) </span><span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">throws</span><span style="border:none;background-color:inherit;"> IOException,  </span></span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">            InterruptedException, ClassNotFoundException {  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">        <span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">long</span><span style="border:none;background-color:inherit;"> startTime;  </span></span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">        <span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">long</span><span style="border:none;background-color:inherit;"> endTime;  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">        Configuration conf = HBaseConfiguration.create();  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">        conf.set(<span class="string" style="border:none;color:#0000FF;background-color:inherit;">"hbase.zookeeper.quorum"</span><span style="border:none;background-color:inherit;">, </span><span class="string" style="border:none;color:#0000FF;background-color:inherit;">"node2,node3,node4"</span><span style="border:none;background-color:inherit;">);  </span></span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">        conf.set(<span class="string" style="border:none;color:#0000FF;background-color:inherit;">"fs.default.name"</span><span style="border:none;background-color:inherit;">, </span><span class="string" style="border:none;color:#0000FF;background-color:inherit;">"hdfs://node1"</span><span style="border:none;background-color:inherit;">);  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">        conf.set(<span class="string" style="border:none;color:#0000FF;background-color:inherit;">"mapred.job.tracker"</span><span style="border:none;background-color:inherit;">, </span><span class="string" style="border:none;color:#0000FF;background-color:inherit;">"node1:54311"</span><span style="border:none;background-color:inherit;">);  </span></span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">        <span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;">/*</span> </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;">         * 传递参数给map</span> </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;">         */</span><span style="border:none;background-color:inherit;">  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">        conf.set(<span class="string" style="border:none;color:#0000FF;background-color:inherit;">"search.license"</span><span style="border:none;background-color:inherit;">, </span><span class="string" style="border:none;color:#0000FF;background-color:inherit;">"新C87310"</span><span style="border:none;background-color:inherit;">);  </span></span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">        conf.set(<span class="string" style="border:none;color:#0000FF;background-color:inherit;">"search.color"</span><span style="border:none;background-color:inherit;">, </span><span class="string" style="border:none;color:#0000FF;background-color:inherit;">"10"</span><span style="border:none;background-color:inherit;">);  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">        conf.set(<span class="string" style="border:none;color:#0000FF;background-color:inherit;">"search.direction"</span><span style="border:none;background-color:inherit;">, </span><span class="string" style="border:none;color:#0000FF;background-color:inherit;">"2"</span><span style="border:none;background-color:inherit;">);  </span></span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">        Job job = <span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">new</span><span style="border:none;background-color:inherit;"> Job(conf, </span><span class="string" style="border:none;color:#0000FF;background-color:inherit;">"MRSearchHBase"</span><span style="border:none;background-color:inherit;">);  </span></span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">        System.out.println(<span class="string" style="border:none;color:#0000FF;background-color:inherit;">"search.license: "</span><span style="border:none;background-color:inherit;"> + conf.get(</span><span class="string" style="border:none;color:#0000FF;background-color:inherit;">"search.license"</span><span style="border:none;background-color:inherit;">));  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">        job.setNumReduceTasks(<span class="number" style="border:none;color:rgb(192,0,0);background-color:inherit;">0</span><span style="border:none;background-color:inherit;">);  </span></span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">        job.setJarByClass(MRSearchAuto.<span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">class</span><span style="border:none;background-color:inherit;">);  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">        Scan scan = <span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">new</span><span style="border:none;background-color:inherit;"> Scan();  </span></span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">        scan.addFamily(FAMILY_NAME);  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">        <span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">byte</span><span style="border:none;background-color:inherit;">[] startRow = Bytes.toBytes(</span><span class="string" style="border:none;color:#0000FF;background-color:inherit;">"2011010100000"</span><span style="border:none;background-color:inherit;">);  </span></span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">        <span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">byte</span><span style="border:none;background-color:inherit;">[] stopRow;  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">        <span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">switch</span><span style="border:none;background-color:inherit;"> (numOfDays) {  </span></span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">        <span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">case</span><span style="border:none;background-color:inherit;"> </span><span class="number" style="border:none;color:rgb(192,0,0);background-color:inherit;">1</span><span style="border:none;background-color:inherit;">:  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">            stopRow = Bytes.toBytes(<span class="string" style="border:none;color:#0000FF;background-color:inherit;">"2011010200000"</span><span style="border:none;background-color:inherit;">);  </span></span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">            <span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">break</span><span style="border:none;background-color:inherit;">;  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">        <span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">case</span><span style="border:none;background-color:inherit;"> </span><span class="number" style="border:none;color:rgb(192,0,0);background-color:inherit;">10</span><span style="border:none;background-color:inherit;">:  </span></span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">            stopRow = Bytes.toBytes(<span class="string" style="border:none;color:#0000FF;background-color:inherit;">"2011011100000"</span><span style="border:none;background-color:inherit;">);  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">            <span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">break</span><span style="border:none;background-color:inherit;">;  </span></span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">        <span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">case</span><span style="border:none;background-color:inherit;"> </span><span class="number" style="border:none;color:rgb(192,0,0);background-color:inherit;">30</span><span style="border:none;background-color:inherit;">:  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">            stopRow = Bytes.toBytes(<span class="string" style="border:none;color:#0000FF;background-color:inherit;">"2011020100000"</span><span style="border:none;background-color:inherit;">);  </span></span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">            <span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">break</span><span style="border:none;background-color:inherit;">;  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">        <span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">case</span><span style="border:none;background-color:inherit;"> </span><span class="number" style="border:none;color:rgb(192,0,0);background-color:inherit;">365</span><span style="border:none;background-color:inherit;">:  </span></span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">            stopRow = Bytes.toBytes(<span class="string" style="border:none;color:#0000FF;background-color:inherit;">"2012010100000"</span><span style="border:none;background-color:inherit;">);  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">            <span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">break</span><span style="border:none;background-color:inherit;">;  </span></span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">        <span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">default</span><span style="border:none;background-color:inherit;">:  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">            stopRow = Bytes.toBytes(<span class="string" style="border:none;color:#0000FF;background-color:inherit;">"2011010101000"</span><span style="border:none;background-color:inherit;">);  </span></span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">        }  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">        <span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;">// 设置开始和结束key</span><span style="border:none;background-color:inherit;">  </span></span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">        scan.setStartRow(startRow);  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">        scan.setStopRow(stopRow);  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">        TableMapReduceUtil.initTableMapperJob(TABLE_NAME, scan,  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">                SearchMapper.<span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">class</span><span style="border:none;background-color:inherit;">, ImmutableBytesWritable.</span><span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">class</span><span style="border:none;background-color:inherit;">, Text.</span><span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">class</span><span style="border:none;background-color:inherit;">,  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">                job);  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">        Path outPath = <span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">new</span><span style="border:none;background-color:inherit;"> Path(</span><span class="string" style="border:none;color:#0000FF;background-color:inherit;">"searchresult"</span><span style="border:none;background-color:inherit;">);  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">        HDFS_File file = <span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">new</span><span style="border:none;background-color:inherit;"> HDFS_File();  </span></span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">        file.DelFile(conf, outPath.getName(), <span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">true</span><span style="border:none;background-color:inherit;">); </span><span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;">// 若已存在，则先删除</span><span style="border:none;background-color:inherit;">  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">        FileOutputFormat.setOutputPath(job, outPath);<span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;">// 输出结果</span><span style="border:none;background-color:inherit;">  </span></span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">        startTime = System.currentTimeMillis();  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">        job.waitForCompletion(<span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">true</span><span style="border:none;background-color:inherit;">);  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">        endTime = System.currentTimeMillis();  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">        System.out.println(<span class="string" style="border:none;color:#0000FF;background-color:inherit;">"Time used: "</span><span style="border:none;background-color:inherit;"> + (endTime - startTime));  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">        System.out.println(<span class="string" style="border:none;color:#0000FF;background-color:inherit;">"startRow:"</span><span style="border:none;background-color:inherit;"> + Text.decode(startRow));  </span></span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">        System.out.println(<span class="string" style="border:none;color:#0000FF;background-color:inherit;">"stopRow: "</span><span style="border:none;background-color:inherit;"> + Text.decode(stopRow));  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">    }  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">    <span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">public</span><span style="border:none;background-color:inherit;"> </span><span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">static</span><span style="border:none;background-color:inherit;"> </span><span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">void</span><span style="border:none;background-color:inherit;"> main(String args[]) </span><span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">throws</span><span style="border:none;background-color:inherit;"> IOException,  </span></span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">            InterruptedException, ClassNotFoundException {  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">        MRSearchAuto mrSearchAuto = <span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">new</span><span style="border:none;background-color:inherit;"> MRSearchAuto();  </span></span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">        <span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">int</span><span style="border:none;background-color:inherit;"> numOfDays = </span><span class="number" style="border:none;color:rgb(192,0,0);background-color:inherit;">1</span><span style="border:none;background-color:inherit;">;  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">        <span class="keyword" style="border:none;color:rgb(0,102,153);background-color:inherit;font-weight:bold;">if</span><span style="border:none;background-color:inherit;"> (args.length == </span><span class="number" style="border:none;color:rgb(192,0,0);background-color:inherit;">1</span><span style="border:none;background-color:inherit;">)  </span></span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">            numOfDays = Integer.valueOf(args[<span class="number" style="border:none;color:rgb(192,0,0);background-color:inherit;">0</span><span style="border:none;background-color:inherit;">]);  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">        System.out.println(<span class="string" style="border:none;color:#0000FF;background-color:inherit;">"Num of days: "</span><span style="border:none;background-color:inherit;"> + numOfDays);  </span></span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">        mrSearchAuto.searchHBase(numOfDays);  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">    }  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">}  </span></li></ol></div>
<p style="font-family:Arial;font-size:14px;line-height:26px;">
开始时，我是在外面conf.set了传入的参数，而在mapper的init(Configuration)里get参数并赋给mapper对象。</p>
<p style="font-family:Arial;font-size:14px;line-height:26px;">
将参数传给map运行时结果不对<br>
for (int i = 0; i &lt; 1; i++) {<br>
    // /在此map里进行filter的功能<br>
    tempString = Text.decode(value.getValue(FAMILY_NAME,<br>
      QUALIFIER_NAME[i]));<br>
    if (tempString.equals(/*strConditionStrings[i]*/"新C87310"))<br>
     string = string + tempString + " ";<br>
    else {<br>
     return;<br>
    }<br>
   }<br>
如果用下面的mapper的init获取conf传来的参数，然后在上面map函数里进行调用，结果便不对了。<br>
直接指定值时和参数传过来相同的值时，其output的结果分别为1条和0条。<br>
  private void init(Configuration conf) throws IOException,<br>
    InterruptedException {<br>
   strConditionStrings[0] = conf.get("search.licenseNumber").trim();<br>
   strConditionStrings[1] = conf.get("search.carColor").trim();<br>
   strConditionStrings[2] = conf.get("search.direction").trim();<br>
  }<br>
加了个日志写<br>
private static final Log LOG = LogFactory.getLog(MRSearchAuto.class);<br>
init()函数里：<br>
LOG.info("license: " + strConditionStrings[0]);<br>
map里<br>
 if (tempString.equals(/* strConditionStrings[i] */"新C87310")) {<br>
  LOG.info("新C87310. conf: " + strConditionStrings[0]);<br>
然后在网页 namenode:50030上看任务，最终定位到哪台机器执行了那个map，然后看日志<br>
mapreduce.hbase.TestMRHBase: 新C87310. conf: null<br>
在conf.set之后我也写了下，那时正常，但是在map里却是null了，而在map类的init函数打印的却没有打印。<br>
因此，问题应该是：<br>
map类的init()函数没有执行到！<br>
于是init()的获取conf中参数值并赋给map里变量的操作便未执行，同时打印日志也未执行。<br>
OK！看怎么解决<br>
放在setup里获取<br>
  protected void setup(Context context) throws IOException,<br>
    InterruptedException {<br>
  // strConditionStrings[0] = context.getConfiguration().get("search.license").trim();<br>
  // strConditionStrings[1] = context.getConfiguration().get("search.color").trim();<br>
  // strConditionStrings[2] = context.getConfiguration().get("search.direction").trim();<br>
  }<br>
报错<br>
12/01/12 11:21:56 INFO mapred.JobClient:  map 0% reduce 0%<br>
12/01/12 11:22:03 INFO mapred.JobClient: Task Id : attempt_201201100941_0071_m_000000_0, Status : FAILED<br>
java.lang.NullPointerException<br>
 at mapreduce.hbase.MRSearchAuto$SearchMapper.setup(MRSearchAuto.java:66)<br>
 at org.apache.hadoop.mapreduce.Mapper.run(Mapper.java:142)<br>
 at org.apache.hadoop.mapred.MapTask.runNewMapper(MapTask.java:656)<br>
 at org.apache.hadoop.mapred.MapTask.run(MapTask.java:325)<br>
 at org.apache.hadoop.mapred.Child$4.run(Child.java:270)<br>
 at java.security.AccessController.doPrivileged(Native Method)<br>
 at javax.security.auth.Subject.doAs(Subject.java:396)<br>
 at org.apache.hadoop.security.UserGroupInformation.doAs(UserGroupInformation.java:1127)<br>
 at org.apache.hadoop.mapred.Child.main(Child.java:264)</p>
<p style="font-family:Arial;font-size:14px;line-height:26px;">
attempt_201201100941_0071_m_000000_0: log4j:WARN No appenders could be found for logger (org.apache.hadoop.hdfs.DFSClient).<br>
attempt_201201100941_0071_m_000000_0: log4j:WARN Please initialize the log4j system properly.<br>
12/01/12 11:22:09 INFO mapred.JobClient: Task Id : attempt_201201100941_0071_m_000000_1, Status : FAILED<br>
java.lang.NullPointerException<br>
 at mapreduce.hbase.MRSearchAuto$SearchMapper.setup(MRSearchAuto.java:66)<br>
 at org.apache.hadoop.mapreduce.Mapper.run(Mapper.java:142)<br>
 at org.apache.hadoop.mapred.MapTask.runNewMapper(MapTask.java:656)<br>
 at org.apache.hadoop.mapred.MapTask.run(MapTask.java:325)<br>
 at org.apache.hadoop.mapred.Child$4.run(Child.java:270)<br>
 at java.security.AccessController.doPrivileged(Native Method)<br>
 at javax.security.auth.Subject.doAs(Subject.java:396)<br>
 at org.apache.hadoop.security.UserGroupInformation.doAs(UserGroupInformation.java:1127)<br>
 at org.apache.hadoop.mapred.Child.main(Child.java:264)<br>
然后将setup里的东西注释掉，无错，错误应该在context上，进一步确认，在里面不用context，直接赋值，有结果，好！<br>
说明是context的事了，NullPointerException,应该是context.getConfiguration().get("search.license")这些中有一个是null的。<br>
突然想起来，改了下get时候的属性，而set时候没改，于是不对应，于是context.getConfiguration().get("search.color")及下面的一项都是null，null.trim()报的异常。<br>
  conf.set("search.license", "新C87310");<br>
  conf.set("search.color", "10");<br>
  conf.set("search.direction", "2");<br>
修改后，问题解决。<br>
实现了向map中传参数</p>
<br><p><br></p>
<p><br></p>
<p></p>
            </div>
                </div>