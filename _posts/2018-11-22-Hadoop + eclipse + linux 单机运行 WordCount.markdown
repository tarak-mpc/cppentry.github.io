---
layout:     post
title:      Hadoop + eclipse + linux 单机运行 WordCount
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>Eclipse版本3.5， Hadoop版本hadoop-0.20.2</p>
<p> </p>
<p><strong>1. 安装Hadoop</strong></p>
<p>    下载hadoop-0.20.2, <a href="http://www.apache.org/dyn/closer.cgi/hadoop/core/" rel="nofollow">http://www.apache.org/dyn/closer.cgi/hadoop/core/</a></p>
<p>    解压Hadoop并配置Hadoop home 环境变量<span style="background-color:#fafafa;">$ gzip -d hadoop-0.20.2.tar.gz. </span>切换到Hadoop用户(不切换也可)</p>
<pre><code class="language-java">$ tar xvf hadoop-0.20.2.tar 
$ vi .bash_profile
$ export JAVA_HOME=~/software/jdk1.6.0_11
# 添加 HADOOP=~/software/hadoop-0.20.2
# 添加PATH=$HADOOP/bin:$PATH
$ export PATH
$ source .bash_profile</code></pre>
<p> </p>
<p>    编辑 conf/hadoop-env.sh文件，将JAVA_HOME设置为Java安装根路径。</p>
<p> </p>
<pre><code class="language-java">export JAVA_HOME=~/software/jdk1.6.0_11</code></pre>
 
<p> </p>
<p>    修改Hadoop配置文件：[其中ip地址是我的local地址]</p>
<p>    conf/ hdfs-site.xml</p>
<p> </p>
<pre class="xml">&lt;configuration&gt;
&lt;property&gt;
    &lt;name&gt;dfs.replication&lt;/name&gt;
    &lt;value&gt;1&lt;/value&gt;
  &lt;/property&gt;
&lt;/configuration&gt;</pre>
<p> </p>
<p> </p>
<p>    conf/core-site.xml:</p>
<p> </p>
<pre class="xml">&lt;configuration&gt;
&lt;property&gt;
    &lt;name&gt;fs.default.name&lt;/name&gt;
    &lt;value&gt;hdfs://192.168.13.171:9000&lt;/value&gt;
  &lt;/property&gt;
&lt;/configuration&gt;
</pre>
<p> </p>
<p> </p>
<p>    conf/mapred-site.xml</p>
<p> </p>
<pre><code class="language-java">&lt;configuration&gt;
&lt;property&gt;
    &lt;name&gt;mapred.job.tracker&lt;/name&gt;
    &lt;value&gt;192.168.13.171:9001&lt;/value&gt;
  &lt;/property&gt;
&lt;/configuration&gt;</code></pre>
<p> </p>
<p> </p>
<p>    启动Hadoop:</p>
<p> </p>
<pre><code class="language-java">$ bin/start-all.sh</code></pre>
 
<p> </p>
<p>    查看是否成功</p>
<p>    http://ubuntu:50030　可以查看JobTracker的运行状态：</p>
<p>    http://ubuntu:50070　可以查看NameNode及整个分布式文件系统的状态等：</p>
<p> </p>
<p>    #如不成功，则配置有问题。我遇见的情况是java远程调试端口占用了9000，所以NameNode启动不起来</p>
<p> </p>
<p><strong>2. Eclipse 开发Hadoop</strong></p>
<p> </p>
<p>    下载hadoop-eclipse-plugin-0.20.3-SNAPSHOT.jar</p>
<p><a href="https://issues.apache.org/jira/secure/attachment/12460491/hadoop-eclipse-plugin-0.20.3-SNAPSHOT.jar" rel="nofollow">https://issues.apache.org/jira/secure/attachment/12460491/hadoop-eclipse-plugin-0.20.3-SNAPSHOT.jar</a></p>
<p> </p>
<p>    注意不要用hadoop-0.20.2下自带的eclise插件，那个是坑爹的。否则会导致run on Hadoop 无反应。这个插件的版本是配合eclipse3.5的。下载后将其重命名为hadoop-0.20.2-eclipse-plugin.jar，并放入到eclipse插件目录中。</p>
<p> </p>
<p>    启动eclipse：打开windows-&gt;open perspective-&gt;other-&gt;map/reduce 可以看到map/reduce开发视图。设置Hadoop location.</p>
<p> </p>
<p>    打开windows-&gt;show view-&gt;other-&gt; map/reduce Locations视图，在点击大象后【new Hadoop location】弹出的对话框（General tab）进行参数的添加：</p>
<p>    Location name: 任意</p>
<p>    map/reduce master： 与mapred-site.xml里面mapred.job.tracker设置一致。</p>
<p>    DFS master：与core-site.xml里fs.default.name设置一致。</p>
<p>    User name: 服务器上运行hadoop服务的用户名。</p>
<p> </p>
<p>    这些已经足够用。当然，如果你需要特殊设置，那么打开Advanced parameters设置面板，查看当前的选项以及修改其值.</p>
<p> </p>
<p>    创建Map/Reduce Project。运行最简单的WordCount程序。首先准备2个文件</p>
<p> </p>
<pre><code class="language-java">$ bin/hadoop dfs -mkdir /tmp/input 
$ bin/hadoop dfs -put file1.txt input
$ bin/hadoop dfs -put file2.txt input</code></pre>
<p> </p>
<pre><code class="language-java">import java.util.StringTokenizer;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.Mapper;
import org.apache.hadoop.mapreduce.Reducer;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
import org.apache.hadoop.util.GenericOptionsParser;
import org.apache.log4j.Logger;

public class WordCount {

	public static Logger loger = Logger.getLogger(WordCount.class);

	public static class TokenizerMapper extends
			Mapper&lt;Object, Text, Text, IntWritable&gt; {

		private final static IntWritable one = new IntWritable(1);
		private Text word = new Text();

		public void map(Object key, Text value, Context context)
				throws IOException, InterruptedException {
			loger.info("Map &lt;key&gt;" + key + "&lt;/key&gt;");
			loger.info("Map &lt;value&gt;" + value + "&lt;/key&gt;");
			StringTokenizer itr = new StringTokenizer(value.toString());
			while (itr.hasMoreTokens()) {
				String wordstr = itr.nextToken();
				word.set(wordstr);
				loger.info("Map &lt;word&gt;" + wordstr + "&lt;/word&gt;");
				context.write(word, one);
			}
		}
	}

	public static class IntSumReducer extends
			Reducer&lt;Text, IntWritable, Text, IntWritable&gt; {
		private IntWritable result = new IntWritable();

		public void reduce(Text key, Iterable&lt;IntWritable&gt; values,
				Context context) throws IOException, InterruptedException {
			loger.info("Reduce &lt;key&gt;" + key + "&lt;/key&gt;");
			loger.info("Reduce &lt;value&gt;" + values + "&lt;/key&gt;");
			int sum = 0;
			for (IntWritable val : values) {
				sum += val.get();
			}
			result.set(sum);
			loger.info("Reduce &lt;sum&gt;" + sum + "&lt;/sum&gt;");
			context.write(key, result);
		}
	}

	public static void main(String[] args) throws Exception {
		Configuration conf = new Configuration();
		String[] otherArgs = new GenericOptionsParser(conf, args)
				.getRemainingArgs();

		if (otherArgs.length != 2) {
			// System.err.println("Usage: wordcount &lt;in&gt; &lt;out&gt;");
			// System.exit(2);
		}
		Job job = new Job(conf, "word count");
		job.setJarByClass(WordCount.class);// ??
		job.setMapperClass(TokenizerMapper.class);// mapper
		job.setCombinerClass(IntSumReducer.class);
		job.setReducerClass(IntSumReducer.class);// reducer
		job.setOutputKeyClass(Text.class);
		job.setOutputValueClass(IntWritable.class);
		FileInputFormat.addInputPath(job, new Path(otherArgs[0]));</code></pre>
<pre><code class="language-java">		FileOutputFormat.setOutputPath(job, new Path(otherArgs[1]));</code></pre>
<pre><code class="language-java">		System.exit(job.waitForCompletion(true) ? 0 : 1);
	}
}
</code></pre>
 
<p>    点击Run on hadoop, 运行程序。你可以看到在output文件夹中出现文本段.</p>
<p>    运行时参数设置为：hdfs://localhost:9000/user/hadoop/in/*.txt  /tmp/out [因为eclipse是远程访问的，所以要将其写为远程地址。第二个参数是保存在本地。当然也可以保存在hdfs上，写成和第一个参数类似的格式]</p>
<p> </p>
<p>    如果参数设置为/in/*.txt /tmp/out，则需要在job中手动指定conf，否则job只会去加载defalut的conf，而不是hadoop/conf下的配置文件，因此程序运行会抛出异常。</p>
<p>
</p>
<p><span>	</span>Configuration conf = new Configuration();</p>
<p><span>	</span>conf.addResource("conf/core-site.xml");</p>
<p><span>	</span>Job job = new Job(conf);</p>

<p> </p>
<p>    运行时可能会出现内存溢出的异常：</p>
<p> </p>
<pre><code class="language-java">java.lang.OutOfMemoryError: Java heap space
 at org.apache.hadoop.mapred.MapTask$MapOutputBuffer.&lt;init&gt;(MapTask.java:781)
 at org.apache.hadoop.mapred.MapTask.runOldMapper(MapTask.java:350)
 at org.apache.hadoop.mapred.MapTask.run(MapTask.java:307)
 at org.apache.hadoop.mapred.LocalJobRunner$Job.run(LocalJobRunner.java:177)
11/08/16 10:31:39 INFO mapred.JobClient:  map 0% reduce 0%
11/08/16 10:31:39 INFO mapred.JobClient: Job complete: job_local_0001
11/08/16 10:31:39 INFO mapred.JobClient: Counters: 0
Exception in thread "main" java.io.IOException: Job failed!
 at org.apache.hadoop.mapred.JobClient.runJob(JobClient.java:1252)
 at mapred.WordCount.main(WordCount.java:110)</code></pre>
 
<p>    原因是客户端程序运行时java虚拟机内存分配不够，修改run configuration（run as &gt;run configurations），选择Arguments选项卡，在VM arguments栏中加入-Xmx1024m，保存后执行没有问题（即将执行时虚拟机内存调大）</p>
<p> </p>
<p>3. 多台机器运行Hadoop</p>
<p>    由于环境有限，且属于学习性质，所以没太研究。主要是要保证各机器之间无密码ssh</p>
<p>    在master节点上生成密钥对：</p>
<p>    $ ssh-keygen –t rsa</p>
<p>    生成密钥对保存在.ssh/id_rsa文件中。</p>
<p>    然后执行命令：</p>
<p>    <span style="background-color:#fafafa;">$ ssh ~/.ssh</span></p>
<pre><code class="language-java">$ cp id_rsa.pub authorized_keys
$ scp authorized_keys slave1:/home/hadoop/.ssh
$ scp authorized_keys slave2:/home/hadoop/.ssh</code></pre>
 
<p> </p>            </div>
                </div>