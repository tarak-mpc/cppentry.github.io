---
layout:     post
title:      HBase之java api接口调用与mapreduce整合即从hdfs中通过mapreduce来导入数据到hbase中
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/sun_wangdong/article/details/75646329				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-size:14px;">此篇分为两部分来探讨，第一部分是hbase的java api接口，第二部分是hbase与mapreduce整合</span></p>
<p><span style="font-size:14px;">一、hbase之java api接口</span></p>
<p><span style="font-size:14px;">hbase是基于java写的，所以当然可以调用java api一样通过java代码来操纵hbase，可以实现基本的查询hbase表，向hbase表上传某一条记录等操作。那么首先需要依赖一些基本的hbase的maven的jar包，lz是在idea的ide中操作，具体需要添加的jar包如下：</span></p>
<p><span style="font-size:14px;"></span></p>
<pre><code class="language-plain">&lt;?xml version="1.0" encoding="UTF-8"?&gt;
&lt;project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd"&gt;
    &lt;modelVersion&gt;4.0.0&lt;/modelVersion&gt;

    &lt;groupId&gt;com.sunwangdong.hadoop.test&lt;/groupId&gt;
    &lt;artifactId&gt;jkxy&lt;/artifactId&gt;
    &lt;version&gt;1.0-SNAPSHOT&lt;/version&gt;
    &lt;dependencies&gt;
        &lt;dependency&gt;
            &lt;groupId&gt;org.apache.hadoop&lt;/groupId&gt;
            &lt;artifactId&gt;hadoop-common&lt;/artifactId&gt;
            &lt;version&gt;2.7.1&lt;/version&gt;
        &lt;/dependency&gt;
        &lt;dependency&gt;
            &lt;groupId&gt;org.apache.hadoop&lt;/groupId&gt;
            &lt;artifactId&gt;hadoop-hdfs&lt;/artifactId&gt;
            &lt;version&gt;2.7.1&lt;/version&gt;
        &lt;/dependency&gt;
        &lt;dependency&gt;
            &lt;groupId&gt;org.apache.hadoop&lt;/groupId&gt;
            &lt;artifactId&gt;hadoop-mapreduce-client-core&lt;/artifactId&gt;
            &lt;version&gt;2.7.1&lt;/version&gt;
        &lt;/dependency&gt;
        &lt;dependency&gt;
            &lt;groupId&gt;org.apache.hadoop&lt;/groupId&gt;
            &lt;artifactId&gt;hadoop-mapreduce-client-common&lt;/artifactId&gt;
            &lt;version&gt;2.7.1&lt;/version&gt;
        &lt;/dependency&gt;
        &lt;dependency&gt;
            &lt;groupId&gt;org.apache.hadoop&lt;/groupId&gt;
            &lt;artifactId&gt;hadoop-mapreduce-client-jobclient&lt;/artifactId&gt;
            &lt;version&gt;2.7.1&lt;/version&gt;
        &lt;/dependency&gt;
        &lt;dependency&gt;
            &lt;groupId&gt;org.apache.hbase&lt;/groupId&gt;   //hbase的jar包，主要有hbase，hbase-client、hbase-server三个
            &lt;artifactId&gt;hbase&lt;/artifactId&gt;
            &lt;version&gt;1.2.6&lt;/version&gt;
        &lt;/dependency&gt;
        &lt;dependency&gt;
            &lt;groupId&gt;org.apache.hbase&lt;/groupId&gt;
            &lt;artifactId&gt;hbase-client&lt;/artifactId&gt;
            &lt;version&gt;1.2.6&lt;/version&gt;
        &lt;/dependency&gt;
        &lt;dependency&gt;
            &lt;groupId&gt;org.apache.hbase&lt;/groupId&gt;
            &lt;artifactId&gt;hbase-server&lt;/artifactId&gt;
            &lt;version&gt;1.2.6&lt;/version&gt;
        &lt;/dependency&gt;
    &lt;/dependencies&gt;
&lt;/project&gt;</code></pre>添加完依赖包后，依次来写一些基本的操作，首先是建hbase表的操作，如下：
<p></p>
<p><span style="font-size:14px;"></span></p>
<pre><code class="language-java">private static void createTable(HBaseAdmin hBaseAdmin) throws IOException
    {
        if(!hBaseAdmin.tableExists(TABLE_NAME))  //判断是否存在以"hello"为表名的表
        {
            HTableDescriptor hTableDescriptor = new HTableDescriptor(TABLE_NAME); //表名
            HColumnDescriptor hColumnDescriptor = new HColumnDescriptor(FAMILY_NAME); //列族
            hTableDescriptor.addFamily(hColumnDescriptor);  //表添加列族
            hBaseAdmin.createTable(hTableDescriptor);    //添加表


        }
    }</code></pre>这里主要通过基本类，分别是HTableDescriptor,这个类表示的是表名，可以通过它的构造函数，为它添加具体的表名。然后是HColumnDescriptor类，这个类表示的是列族，通过HTableDescriptor.addFamily(HColumnDescriptor)来为表添加具体的列族。此外还有一个HBaseAdmin接口，这个接口是用来管理hbase数据库的表信息，提供的方法有：创建表、删除表、列出表项等操作，可以通过HBaseAdmin.createTable(HTableDescriptor)来创建一张表。
<p></p>
<p><span style="font-size:14px;">然后是删除表：</span></p>
<p><span style="font-size:14px;"></span></p>
<pre><code class="language-java">private static void dropTable(HBaseAdmin hBaseAdmin) throws IOException
    {
        if(hBaseAdmin.tableExists(TABLE_NAME))  //是否存在改表
        {
            hBaseAdmin.disableTable(TABLE_NAME);   //先要disable表
            hBaseAdmin.deleteTable(TABLE_NAME);    //然后再delete表
        }
    }</code></pre>先判断要删除的表是否在hbase数据库中，如果存在，那么调用deleteTable实现删除操作，当然在执行删除操作之前，往往需要先disable那张表，用于使那张表先失效，然后再删除。<br><p></p>
<p><span style="font-size:14px;">接下来是查看数据，具体有两种方法，分别是scan和get方法，分别对应hbase的scan和get方法。</span></p>
<p><span style="font-size:14px;"></span></p>
<pre><code class="language-java">private static void scanTable(HTable hTable) throws IOException
    {
        System.out.printf("遍历表结果如下：");
        Scan scan = new Scan();
        ResultScanner results = hTable.getScanner(scan);
        for(Result result : results)
        {
            byte[] value = result.getValue(FAMILY_NAME.getBytes(),COLUMN_NAME.getBytes());
            System.out.println(new String(value));
        }
    }</code></pre>注意到，因为scan是扫描整张表，所以会得到很多行的数据，所以这里用到了一个ResultScanner的结果集来表示，我们可以通过一个HTable类的getScanner方法来得到。注意：<span style="font-size:14px;">HTable类是用来与hbase表进行通信，但是此方法是线程不安全的，如果有多个线程尝试与单个<span style="font-size:14px;">HTable实例进行通信，则写缓冲器就会失效。注意，每次获取得到的信息都是bytes类型的，所以都需要转化为string类型即可。</span></span><br><p></p>
<p><span style="font-size:14px;">然后是get方法，与scan有点类似，不过get方法只得到某个rowkey下的数据，而不是整张表的数据。</span></p>
<p><span style="font-size:14px;"></span></p>
<pre><code class="language-java">private static void getRecord(HTable hTable) throws IOException
    {
        Get get = new Get(ROW_KEY.getBytes());
        Result result = hTable.get(get);
        byte[] value = result.getValue(FAMILY_NAME.getBytes(),COLUMN_NAME.getBytes());
        System.out.println("查询结果为:" + new String(value));
    }</code></pre>这里需要指定某一个rowkey的某一列的内容，当然如果有时间戳，那么还需要指明，因为hbase中的一条数据是由四个因素确定的，分别是rowkey,列族，列名和value。结果是一个result，那么同样是bytes数组，然后输出时，需要转化成string类型。
<p></p>
<p><span style="font-size:14px;">最后是put，也就是向hbase上传、更新某一条记录。</span></p>
<p><span style="font-size:14px;"></span></p>
<pre><code class="language-java">private static void putRecord(HTable hTable) throws IOException
    {
        Put put = new Put(ROW_KEY.getBytes());
        put.add(FAMILY_NAME.getBytes(),COLUMN_NAME.getBytes(),"25".getBytes());
        hTable.put(put);
        System.out.println("insert a record!");
    }</code></pre>这里用到了put类，它的作用是用来对单个行执行添加操作。当然上传一条记录，同样需要四个信息，行键，列族，列和具体的value值，有时候还需要指定时间戳。
<p></p>
<p><span style="font-size:14px;">完整的代码如下：</span></p>
<p><span style="font-size:14px;"></span></p>
<pre><code class="language-plain">ackage com.hbase;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.hbase.HBaseConfiguration;
import org.apache.hadoop.hbase.HColumnDescriptor;
import org.apache.hadoop.hbase.HTableDescriptor;
import org.apache.hadoop.hbase.client.*;

import java.io.IOException;

/**
 * Created by sunwangdong on 2017/7/20.
 */
public class HbaseTest
{

    //public HBaseAdmin admin = null;

    public static final String TABLE_NAME="hello";   //表名
    public static final String FAMILY_NAME="info";   //列族
    public static final String COLUMN_NAME="age";    //列名
    public static final String ROW_KEY="xiaoming";   //行键rowkey

    public static void main(String[] args) throws IOException
    {
        Configuration conf = HBaseConfiguration.create();  //创建一个configuration
        conf.set("hbase.rootdir","hdfs://localhost:9000/hbase");
        //conf.set("hbase.zookeeper.quorum","");
        HBaseAdmin hBaseAdmin = new HBaseAdmin(conf);
        //createTable(hBaseAdmin);  //创建表
        HTable hTable = new HTable(conf,TABLE_NAME.getBytes());
        //putRecord(hTable);   //插入数据
        putRecord2(hTable,"88");
        getRecord(hTable);   //取出数据

        scanTable(hTable);   //遍历表结果

        //dropTable(hBaseAdmin);  //删除表

    }

    private static void scanTable(HTable hTable) throws IOException
    {
        System.out.printf("遍历表结果如下：");
        Scan scan = new Scan();
        ResultScanner results = hTable.getScanner(scan);
        for(Result result : results)
        {
            byte[] value = result.getValue(FAMILY_NAME.getBytes(),COLUMN_NAME.getBytes());
            System.out.println(new String(value));
        }
    }

    private static void getRecord(HTable hTable) throws IOException
    {
        Get get = new Get(ROW_KEY.getBytes());
        Result result = hTable.get(get);
        byte[] value = result.getValue(FAMILY_NAME.getBytes(),COLUMN_NAME.getBytes());
        System.out.println("查询结果为:" + new String(value));
    }

    private static void putRecord(HTable hTable) throws IOException
    {
        Put put = new Put(ROW_KEY.getBytes());
        put.add(FAMILY_NAME.getBytes(),COLUMN_NAME.getBytes(),"25".getBytes());
        hTable.put(put);
        System.out.println("insert a record!");
    }

    private static void putRecord2(HTable hTable,String value) throws IOException
    {
        Put put = new Put(ROW_KEY.getBytes());
        put.add(FAMILY_NAME.getBytes(),COLUMN_NAME.getBytes(),value.getBytes());
        hTable.put(put);
        System.out.println("insert or update a record!");
    }


    private static void createTable(HBaseAdmin hBaseAdmin) throws IOException
    {
        if(!hBaseAdmin.tableExists(TABLE_NAME))  //判断是否存在以"hello"为表名的表
        {
            HTableDescriptor hTableDescriptor = new HTableDescriptor(TABLE_NAME); //表名
            HColumnDescriptor hColumnDescriptor = new HColumnDescriptor(FAMILY_NAME); //列族
            hTableDescriptor.addFamily(hColumnDescriptor);  //表添加列族
            hBaseAdmin.createTable(hTableDescriptor);    //添加表


        }
    }

    private static void dropTable(HBaseAdmin hBaseAdmin) throws IOException
    {
        if(hBaseAdmin.tableExists(TABLE_NAME))  //是否存在改表
        {
            hBaseAdmin.disableTable(TABLE_NAME);   //先要disable表
            hBaseAdmin.deleteTable(TABLE_NAME);    //然后再delete表
        }
    }
}</code></pre>注意，main函数里需要新建一个configuration类，且此类中需要指定hbase.rootdir的路径，这个需要与hbase-site.xml中配置的路径一致，否则将无法运行。<br><br><p></p>
<p><span style="font-size:14px;">二、hbase与mapreduce整合</span></p>
<p><span style="font-size:14px;">hbase和mapreduce的整合，其实是将hdfs中的数据导入到hbase的数据库中去，目前主要有两种方式来实现，其中一种就是利用mapreduce程序将hdfs中的数据按行划分成列输入到hbase中去，看以下的这个例子，现在在hdfs中有如下的数据表，其中这张数据表存放在hdfs中，具体的路径为：/t1/t1</span></p>
<p><span style="font-size:14px;"></span></p><pre><code class="language-plain">1    zhangsan    10    male    NULL
2    lisi    NULL    NULL    NULL
3    wangwu    NULL    NULL    NULL
4    zhaoliu    NULL    NULL    1993</code></pre>其中每一行中的每一列数据由"\t"来分隔，然后我们通过自定义mapreduce来实现：
<p><span style="font-size:14px;">首先是map函数</span></p>
<p><span style="font-size:14px;"></span></p><pre><code class="language-java">public static class HdfsToHBaseMapper extends Mapper&lt;LongWritable,Text,Text,Text&gt;
    {
        private Text outKey = new Text();
        private Text outValue = new Text();
        public void map(LongWritable key,Text value,Context context) throws IOException, InterruptedException
        {
            String[] splits = value.toString().split("\t");
            outKey.set(splits[0]);
            outValue.set(splits[1] + "\t" + splits[2] + "\t" + splits[3] + "\t" + splits[4]);
            context.write(outKey,outValue);
        }
    }</code></pre><br>
是将输入的数据按照行中的"\t"分隔符来分隔，然后我们通过按照行键和内容的形式输出。
<p><span style="font-size:14px;">然后是汇总的reduce函数</span></p>
<p><span style="font-size:14px;"></span></p><pre><code class="language-java">public static class HdfsToHBaseReducer extends TableReducer&lt;Text,Text,NullWritable&gt;
    {
        public void reduce(Text k2, Iterable&lt;Text&gt; v2s,Context context) throws IOException, InterruptedException
        {
            Put put = new Put(k2.getBytes());
            for(Text v2 : v2s)
            {
                String[] splis = v2.toString().split("\t");
                if(splis[0] != null &amp;&amp; !"NULL".equals(splis[0]))
                {
                    put.addColumn("f1".getBytes(),"name".getBytes(),splis[0].getBytes());
                }
                if(splis[1] != null &amp;&amp; !"NULL".equals(splis[1]))
                {
                    put.addColumn("f1".getBytes(),"age".getBytes(),splis[1].getBytes());
                }
                if(splis[2] != null &amp;&amp; !"NULL".equals(splis[2]))
                {
                    put.addColumn("f1".getBytes(),"gender".getBytes(),splis[2].getBytes());
                }
                if(splis[3] != null &amp;&amp; !"NULL".equals(splis[3]))
                {
                    put.addColumn("f1".getBytes(),"birthday".getBytes(),splis[3].getBytes());
                }
            }
            context.write(NullWritable.get(),put);
        }
    }</code></pre><br>
注意，这个reduce继承自TableReducer，这个类来自于org.apache.hbase.client的jar包中，一开始，lz因为没有通过maven添加此jar包而出错！！！而且这个reduce的泛型约束也与一般的reducer不一样，只有三个，且最后一个是NullWritable，前两个当然是一样的，都是map的输出格式。最后的context的输出形式也不一样，第一个key的输出格式是 NullWritable.get()，而第二个value是put。
<p><span style="font-size:14px;">最后完整的代码如下：</span></p>
<p><span style="font-size:14px;"></span></p><pre><code class="language-java">package com.hbase;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.hbase.HBaseConfiguration;
import org.apache.hadoop.hbase.client.Put;
import org.apache.hadoop.hbase.mapred.TableOutputFormat;
import org.apache.hadoop.hbase.mapreduce.TableMapReduceUtil;
import org.apache.hadoop.hbase.mapreduce.TableReducer;
import org.apache.hadoop.io.LongWritable;
import org.apache.hadoop.io.NullWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.Mapper;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;

import java.io.IOException;


/**
 * Created by sunwangdong on 2017/7/23.
 */
public class HdfsToHBase
{
    public static void main(String[] args) throws IOException, ClassNotFoundException, InterruptedException
    {
        Configuration conf = HBaseConfiguration.create();
        conf.set("hbase.rootdir","hdfs://localhost:9000/hbase");  //约束hbase.root的路径，与hadoop的配置文件一致
        conf.set(TableOutputFormat.OUTPUT_TABLE ,args[1]);
        Job job = Job.getInstance(conf,HdfsToHBase.class.getSimpleName());  //对job的约束
        TableMapReduceUtil.addDependencyJars(job);
        job.setJarByClass(HdfsToHBase.class);

        job.setMapperClass(HdfsToHBaseMapper.class);
        job.setMapOutputKeyClass(Text.class);
        job.setMapOutputValueClass(Text.class);

        job.setReducerClass(HdfsToHBaseReducer.class);

        FileInputFormat.addInputPath(job,new Path(args[0]));
        job.setOutputFormatClass(org.apache.hadoop.hbase.mapreduce.TableOutputFormat.class); //这里设置的格式特别

        Boolean b = job.waitForCompletion(true);
        if(!b)
        {
            System.err.println("failed");
        }
        else
            System.out.println("finished!");
    }

    public static class HdfsToHBaseMapper extends Mapper&lt;LongWritable,Text,Text,Text&gt;
    {
        private Text outKey = new Text();
        private Text outValue = new Text();
        public void map(LongWritable key,Text value,Context context) throws IOException, InterruptedException
        {
            String[] splits = value.toString().split("\t");
            outKey.set(splits[0]);
            outValue.set(splits[1] + "\t" + splits[2] + "\t" + splits[3] + "\t" + splits[4]);
            context.write(outKey,outValue);
        }
    }

    public static class HdfsToHBaseReducer extends TableReducer&lt;Text,Text,NullWritable&gt;
    {
        public void reduce(Text k2, Iterable&lt;Text&gt; v2s,Context context) throws IOException, InterruptedException
        {
            Put put = new Put(k2.getBytes());
            for(Text v2 : v2s)
            {
                String[] splis = v2.toString().split("\t");
                if(splis[0] != null &amp;&amp; !"NULL".equals(splis[0]))
                {
                    put.addColumn("f1".getBytes(),"name".getBytes(),splis[0].getBytes());
                }
                if(splis[1] != null &amp;&amp; !"NULL".equals(splis[1]))
                {
                    put.addColumn("f1".getBytes(),"age".getBytes(),splis[1].getBytes());
                }
                if(splis[2] != null &amp;&amp; !"NULL".equals(splis[2]))
                {
                    put.addColumn("f1".getBytes(),"gender".getBytes(),splis[2].getBytes());
                }
                if(splis[3] != null &amp;&amp; !"NULL".equals(splis[3]))
                {
                    put.addColumn("f1".getBytes(),"birthday".getBytes(),splis[3].getBytes());
                }
            }
            context.write(NullWritable.get(),put);
        }
    }
}
</code></pre>然后需要将此代码打成jar包，注意，运行hbase一定要用hadoop jar的形式，因为lz是在idea中来编译的，所以直接通过idea来编译生成jar包即可，注意，生成jar包，需要删除里面的META-INF/LICENSE.
<p><span style="font-size:14px;">zip -d ****.jar META-INF/LICENSE<br>
当然因为上述代码中并没有直接写在hbase中的create创建表格的代码，所以我们需要事先在hbase中创建表格，即：</span></p>
<p><span style="font-size:14px;"></span></p><pre><code class="language-plain">hbase(main):006:0&gt; create 'table1','f1'
0 row(s) in 1.4270 seconds

=&gt; Hbase::Table - table1</code></pre><br>
完成上述在hbase中的表格创建，其中表格名字为"table1"，它的其中一个行键是"f1"。
<p><span style="font-size:14px;">那么接下来就可以用hadoop的命令来执行上述jar包了，通过</span></p>
<p><span style="font-size:14px;">hadoop jar ./***.jar com.hbase.HdfsToHBase /t1/t1 table1</span></p>
<p><span style="font-size:14px;">那么可以看到在hadoop中通过mapreduce来执行了：</span></p>
<p><span style="font-size:14px;"></span></p><pre><code class="language-plain">localhost:jkxy_jar12 sunwangdong$ hadoop jar ./jkxy.jar com.hbase.HdfsToHBase /t1/t1 table1
17/07/23 11:40:05 WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
17/07/23 11:40:06 INFO client.RMProxy: Connecting to ResourceManager at /0.0.0.0:8032
17/07/23 11:40:07 WARN mapreduce.JobSubmitter: Hadoop command-line option parsing not performed. Implement the Tool interface and execute your application with ToolRunner to remedy this.
17/07/23 11:40:08 INFO input.FileInputFormat: Total input paths to process : 1
17/07/23 11:40:08 INFO mapreduce.JobSubmitter: number of splits:1
17/07/23 11:40:08 INFO mapreduce.JobSubmitter: Submitting tokens for job: job_1500776717808_0003
17/07/23 11:40:08 INFO impl.YarnClientImpl: Submitted application application_1500776717808_0003
17/07/23 11:40:08 INFO mapreduce.Job: The url to track the job: http://sunwangdongMacBook-Pro.local:8088/proxy/application_1500776717808_0003/
17/07/23 11:40:08 INFO mapreduce.Job: Running job: job_1500776717808_0003
17/07/23 11:40:16 INFO mapreduce.Job: Job job_1500776717808_0003 running in uber mode : false
17/07/23 11:40:16 INFO mapreduce.Job:  map 0% reduce 0%
17/07/23 11:40:22 INFO mapreduce.Job:  map 100% reduce 0%
17/07/23 11:40:29 INFO mapreduce.Job:  map 100% reduce 100%
17/07/23 11:40:29 INFO mapreduce.Job: Job job_1500776717808_0003 completed successfully
17/07/23 11:40:29 INFO mapreduce.Job: Counters: 49
	File System Counters
		FILE: Number of bytes read=109
		FILE: Number of bytes written=250471
		FILE: Number of read operations=0
		FILE: Number of large read operations=0
		FILE: Number of write operations=0
		HDFS: Number of bytes read=187
		HDFS: Number of bytes written=0
		HDFS: Number of read operations=2
		HDFS: Number of large read operations=0
		HDFS: Number of write operations=0
	Job Counters
		Launched map tasks=1
		Launched reduce tasks=1
		Data-local map tasks=1
		Total time spent by all maps in occupied slots (ms)=3069
		Total time spent by all reduces in occupied slots (ms)=3801
		Total time spent by all map tasks (ms)=3069
		Total time spent by all reduce tasks (ms)=3801
		Total vcore-seconds taken by all map tasks=3069
		Total vcore-seconds taken by all reduce tasks=3801
		Total megabyte-seconds taken by all map tasks=3142656
		Total megabyte-seconds taken by all reduce tasks=3892224
	Map-Reduce Framework
		Map input records=4
		Map output records=4
		Map output bytes=95
		Map output materialized bytes=109
		Input split bytes=92
		Combine input records=0
		Combine output records=0
		Reduce input groups=4
		Reduce shuffle bytes=109
		Reduce input records=4
		Reduce output records=4
		Spilled Records=8
		Shuffled Maps =1
		Failed Shuffles=0
		Merged Map outputs=1
		GC time elapsed (ms)=130
		CPU time spent (ms)=0
		Physical memory (bytes) snapshot=0
		Virtual memory (bytes) snapshot=0
		Total committed heap usage (bytes)=347602944
	Shuffle Errors
		BAD_ID=0
		CONNECTION=0
		IO_ERROR=0
		WRONG_LENGTH=0
		WRONG_MAP=0
		WRONG_REDUCE=0
	File Input Format Counters
		Bytes Read=95
	File Output Format Counters
		Bytes Written=0
finished!</code></pre><br>
完成成功，那么可以在hbase中看到，刚才创建的"table1"表格已经有了数据
<p><span style="font-size:14px;"></span></p><pre><code class="language-plain">hbase(main):010:0&gt; scan 'table1'
ROW                           COLUMN+CELL
 1                            column=f1:age, timestamp=1500781227480, value=10
 1                            column=f1:gender, timestamp=1500781227480, value=male
 1                            column=f1:name, timestamp=1500781227480, value=zhangsan
 2                            column=f1:name, timestamp=1500781227480, value=lisi
 3                            column=f1:name, timestamp=1500781227480, value=wangwu
 4                            column=f1:birthday, timestamp=1500781227480, value=1993
 4                            column=f1:name, timestamp=1500781227480, value=zhaoliu
4 row(s) in 0.0750 seconds</code></pre><br>
至此，上述通过mapreduce将hdfs上的表格传递给hbase就成功了！<br><br><p><span style="font-size:14px;"><br></span></p>
<p><span style="font-size:14px;"><br><br></span></p>
            </div>
                </div>