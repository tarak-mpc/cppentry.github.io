---
layout:     post
title:      基于MapReduce的HBase开发
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
       在伪分布式模式和全分布式模式下 HBase 是架构在 HDFS 上的，因此完全可以将MapReduce 编程框架和 HBase 结合起来使用。也就是说，将 HBase 作为底层“存储结构”，MapReduce 调用 HBase 进行特殊的处理，这样能够充分结合 HBase 分布式大型数据库和MapReduce 并行计算的优点。 
<h1>相对应MapReduce的hbase实现类：</h1>
<p>  1）InputFormat 类：HBase 实现了 TableInputFormatBase 类，该类提供了对表数据的大部分操作，其子类 TableInputFormat 则提供了完整的实现，用于处理表数据并生成键值对。TableInputFormat 类将数据表按照 Region 分割成 split，既有多少个 Regions 就有多个splits。然后将 Region 按行键分成&lt;key,value&gt;对，key 值对应与行健，value 值为该行所包含的数据。 <br>
  2）Mapper 类和 Reducer 类：HBase 实现了 TableMapper 类和 TableReducer 类，其中TableMapper 类并没有具体的功能，只是将输入的&lt;key,value&gt;对的类型分别限定为 Result 和ImmutableBytesWritable。IdentityTableMapper 类和 IdentityTableReducer 类则是上述两个类的具体实现，其和 Mapper 类和 Reducer 类一样，只是简单地将&lt;key,value&gt;对输出到下一个阶段。 <br></p>
<p>3）OutputFormat 类：HBase 实现的 TableOutputFormat 将输出的&lt;key,value&gt;对写到指定的 HBase 表中，该类不会对 WAL（Write-Ahead  Log）进行操作，即如果服务器发生<br>
故障将面临丢失数据的风险。可以使用 MultipleTableOutputFormat 类解决这个问题，该类可以对是否写入 WAL 进行设置。<br></p>
<p>代码：</p>
<p></p>
<pre><code class="language-java">import java.io.IOException; 
import java.util.Iterator; 
import java.util.StringTokenizer; 
 
import org.apache.hadoop.conf.Configuration; 
import org.apache.hadoop.fs.Path; 
import org.apache.hadoop.hbase.HBaseConfiguration; 
import org.apache.hadoop.hbase.HColumnDescriptor; 
import org.apache.hadoop.hbase.HTableDescriptor; 
import org.apache.hadoop.hbase.client.HBaseAdmin; 
import org.apache.hadoop.hbase.client.Put; 
import org.apache.hadoop.hbase.mapreduce.TableOutputFormat; 
import org.apache.hadoop.hbase.mapreduce.TableReducer; 
import org.apache.hadoop.hbase.util.Bytes; 
import org.apache.hadoop.io.IntWritable; 
import org.apache.hadoop.io.LongWritable; 
import org.apache.hadoop.io.Text; 
import org.apache.hadoop.io.NullWritable; 
import org.apache.hadoop.mapreduce.Job; 
import org.apache.hadoop.mapreduce.Mapper; 
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat; 
import org.apache.hadoop.mapreduce.lib.input.TextInputFormat; 
 
public class WordCountHBase { 
 
  // 实现 Map 类 
  public static class Map extends 
      Mapper&lt;LongWritable, Text, Text, IntWritable&gt; { 
    private final static IntWritable one = new IntWritable(1); 
    private Text word = new Text(); 
 
    public void map(LongWritable key, Text value, Context context) 
        throws IOException, InterruptedException { 
      StringTokenizer itr = new StringTokenizer(value.toString()); 
      while (itr.hasMoreTokens()) { 
        word.set(itr.nextToken()); 
        context.write(word, one); 
      } 
    } 
  } 
 
  // 实现 Reduce 类 
  public static class Reduce extends 
      TableReducer&lt;Text, IntWritable, NullWritable&gt; { 
 
    public void reduce(Text key, Iterable&lt;IntWritable&gt; values, 
        Context context) throws IOException, InterruptedException { 
 
      int sum = 0; 
 
      Iterator&lt;IntWritable&gt; iterator = values.iterator(); 
      while (iterator.hasNext()) { 
        sum += iterator.next().get(); 
      } 
 
      // Put 实例化，每个词存一行 
      Put put = new Put(Bytes.toBytes(key.toString())); 
      // 列族为 content，列修饰符为 count，列值为数目 
      put.add(Bytes.toBytes("content"), Bytes.toBytes("count"), 
          Bytes.toBytes(String.valueOf(sum))); 
 
      context.write(NullWritable.get(), put); 
    } 
  } 
 
  // 创建 HBase 数据表 
  public static void createHBaseTable(String tableName)  
throws IOException { 
    // 创建表描述 
    HTableDescriptor htd = new HTableDescriptor(tableName); 
    // 创建列族描述 
    HColumnDescriptor col = new HColumnDescriptor("content"); 
    htd.addFamily(col); 
 
    // 配置 HBase 
    Configuration conf = HBaseConfiguration.create(); 
 
    conf.set("hbase.zookeeper.quorum","master"); 
    conf.set("hbase.zookeeper.property.clientPort", "2181"); 
    HBaseAdmin hAdmin = new HBaseAdmin(conf); 
 
    if (hAdmin.tableExists(tableName)) { 
      System.out.println("该数据表已经存在，正在重新创建。"); 
      hAdmin.disableTable(tableName); 
      hAdmin.deleteTable(tableName); 
    } 
 
    System.out.println("创建表：" + tableName); 
    hAdmin.createTable(htd); 
  } 
 
  public static void main(String[] args) throws Exception { 
    String tableName = "wordcount"; 
    // 第一步：创建数据库表 
    WordCountHBase.createHBaseTable(tableName); 
 
    // 第二步：进行 MapReduce 处理 
    // 配置 MapReduce 
    Configuration conf = new Configuration(); 
    // 这几句话很关键 
    conf.set("mapred.job.tracker", "master:9001"); 
    conf.set("hbase.zookeeper.quorum","master"); 
    conf.set("hbase.zookeeper.property.clientPort", "2181"); 
    conf.set(TableOutputFormat.OUTPUT_TABLE, tableName); 
 
    Job job = new Job(conf, "New Word Count"); 
    job.setJarByClass(WordCountHBase.class); 
 
    // 设置 Map 和 Reduce 处理类 
    job.setMapperClass(Map.class); 
    job.setReducerClass(Reduce.class); 
 
    // 设置输出类型 
    job.setMapOutputKeyClass(Text.class); 
    job.setMapOutputValueClass(IntWritable.class); 
 
    // 设置输入和输出格式 
    job.setInputFormatClass(TextInputFormat.class); 
    job.setOutputFormatClass(TableOutputFormat.class); 
 
    // 设置输入目录 
    FileInputFormat.addInputPath(job, new Path("hdfs://master:9000/in/")); 
    System.exit(job.waitForCompletion(true) ? 0 : 1); 
 
  } 
} </code></pre>
<p></p>
<h1>常见错误及解决方法：</h1>
<p></p>
<h2>1、java.lang.RuntimeException: java.lang.ClassNotFoundException: org.apache.hadoop.hbase.mapreduce.TableOutputFormat</h2>
<h3>错误输出节选：</h3>
<p></p>
<pre><code class="language-java">13/09/10 21:14:01 INFO mapred.JobClient: Running job: job_201308101437_0016
13/09/10 21:14:02 INFO mapred.JobClient:  map 0% reduce 0%
13/09/10 21:14:16 INFO mapred.JobClient: Task Id : attempt_201308101437_0016_m_000007_0, Status : FAILED
java.lang.RuntimeException: java.lang.ClassNotFoundException: org.apache.hadoop.hbase.mapreduce.TableOutputFormat
	at org.apache.hadoop.conf.Configuration.getClass(Configuration.java:849)
	at org.apache.hadoop.mapreduce.JobContext.getOutputFormatClass(JobContext.java:235)
	at org.apache.hadoop.mapred.Task.initialize(Task.java:513)
	at org.apache.hadoop.mapred.MapTask.run(MapTask.java:353)
	at org.apache.hadoop.mapred.Child$4.run(Child.java:255)
	at java.security.AccessController.doPrivileged(Native Method)
	at javax.security.auth.Subject.doAs(Subject.java:396)
	at org.apache.hadoop.security.UserGroupInformation.doAs(UserGroupInformation.java:1149)
	at org.apache.hadoop.mapred.Child.main(Child.java:249)
Caused by: java.lang.ClassNotFoundException: org.apache.hadoop.hbase.mapreduce.TableOutputFormat
	at java.net.URLClassLoader$1.run(URLClassLoader.java:202)
	at java.security.AccessController.doPrivileged(Native Method)
	at java.net.URLClassLoader.findClass(URLClassLoader.java:190)
	at java.lang.ClassLoader.loadClass(ClassLoader.java:306)
	at sun.misc.Launcher$AppClassLoader.loadClass(Launcher.java:301)
	at java.lang.ClassLoader.loadClass(ClassLoader.java:247)
	at java.lang.Class.forName0(Native Method)
	at java.lang.Class.forName(Class.java:249)
	at org.apache.hadoop.conf.Configuration.getClassByName(Configuration.java:802)
	at org.apache.hadoop.conf.Configuration.getClass(Configuration.java:847)
	... 8 more</code></pre>
<p></p>
<h3>错误原因：</h3>
<p></p>
<p>相关的类文件没有引入到 Hadoop 集群上。</p>
<h3>解决步骤：</h3>
<p>步骤一、停止HBase数据库：</p>
<p></p>
<pre><code class="language-java">[hadoop@master bin]$ stop-hbase.sh 
stopping hbase............
master: stopping zookeeper.
[hadoop@master bin]$ jps
16186 Jps
26186 DataNode
26443 TaskTracker
26331 JobTracker
26063 NameNode</code></pre>
   停止Hadoop集群：<br><p></p>
<p></p>
<pre><code class="language-java">[hadoop@master bin]$ stop-all.sh 
Warning: $HADOOP_HOME is deprecated.

stopping jobtracker
master: Warning: $HADOOP_HOME is deprecated.
master: 
master: stopping tasktracker
node1: Warning: $HADOOP_HOME is deprecated.
node1: 
node1: stopping tasktracker
stopping namenode
master: Warning: $HADOOP_HOME is deprecated.
master: 
master: stopping datanode
node1: Warning: $HADOOP_HOME is deprecated.
node1: stopping datanode
node1: 
node1: Warning: $HADOOP_HOME is deprecated.
node1: 
node1: stopping secondarynamenode
[hadoop@master bin]$ jps
16531 Jps</code></pre>
<p></p>
<p>步骤二、<span style="color:#ff0000;">需要配置 Hadoop 集群中每台机器</span>，在 hadoop 目录的 conf 子目录中，找 hadoop-env.sh文件，并添加如下内容：</p>
<p></p>
<pre><code class="language-java"># set hbase environment
export HBASE_HOME=/opt/modules/hadoop/hbase/hbase-0.94.11-security
export HADOOP_CLASSPATH=$HBASE_HOME/hbase-0.94.11-security.jar:$HBASE_HOME/hbase-0.94.11-security-tests.jar:$HBASE_HOME/conf:$HBASE_HOME/lib/zookeeper-3.4.5.jar</code></pre>
步骤三、重新启动集群和hbase数据库。<br><h2>2、Error: java.lang.ClassNotFoundException: com.google.protobuf.Message</h2>
<h3>错误输出节选：</h3>
<p></p>
<pre><code class="language-java">2013-09-12 12:38:57,833 INFO  mapred.JobClient (JobClient.java:monitorAndPrintJob(1363)) -  map 0% reduce 0%
2013-09-12 12:39:12,490 INFO  mapred.JobClient (JobClient.java:monitorAndPrintJob(1392)) - Task Id : attempt_201309121232_0001_m_000007_0, Status : FAILED
Error: java.lang.ClassNotFoundException: com.google.protobuf.Message
	at java.net.URLClassLoader$1.run(URLClassLoader.java:202)
	at java.security.AccessController.doPrivileged(Native Method)
	at java.net.URLClassLoader.findClass(URLClassLoader.java:190)
	at java.lang.ClassLoader.loadClass(ClassLoader.java:306)
	at sun.misc.Launcher$AppClassLoader.loadClass(Launcher.java:301)</code></pre>
错误原因：
<p></p>
<p>明显，没找到protobuf-java-2.4.0a.jar包，将该包路径加入hadoop-env.sh中。<br><br></p>
<p><br></p>
<p><br></p>
            </div>
                </div>