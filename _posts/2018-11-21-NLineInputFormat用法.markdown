---
layout:     post
title:      NLineInputFormat用法
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：分享的快乐。。					https://blog.csdn.net/baolibin528/article/details/45075585				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><br></p>
<p>输入的数据：</p>
<p></p><pre><code class="language-java">[root@i-love-you hadoop]# bin/hdfs dfs -text /input/hehe
hadoop hello
hadoop me
hadoop java
</code></pre><br><br><p>代码：</p>
<p></p><pre><code class="language-java">package inputformat;

import java.io.IOException;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.LongWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.Mapper;
import org.apache.hadoop.mapreduce.Reducer;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.input.NLineInputFormat;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
import org.apache.hadoop.mapreduce.lib.output.TextOutputFormat;
import org.apache.hadoop.mapreduce.lib.partition.HashPartitioner;

public class NLineInputFormatTest {
	public static class MyMapper extends
			Mapper&lt;LongWritable, Text, Text, LongWritable&gt; {
		final Text k2 = new Text();
		final LongWritable v2 = new LongWritable();

		protected void map(LongWritable key, Text value,
				Mapper&lt;LongWritable, Text, Text, LongWritable&gt;.Context context)
				throws InterruptedException, IOException {
			final String line = value.toString();
			final String[] splited = line.split("\\s");
			for (String word : splited) {
				k2.set(word);
				v2.set(1);
				context.write(k2, v2);
			}
		}
	}

	public static class MyReducer extends
			Reducer&lt;Text, LongWritable, Text, LongWritable&gt; {
		LongWritable v3 = new LongWritable();

		protected void reduce(Text k2, Iterable&lt;LongWritable&gt; v2s,
				Reducer&lt;Text, LongWritable, Text, LongWritable&gt;.Context context)
				throws IOException, InterruptedException {
			long count = 0L;
			for (LongWritable v2 : v2s) {
				count += v2.get();
			}
			v3.set(count);
			context.write(k2, v3);
		}
	}

	public static void main(String[] args) throws Exception {
		final Configuration conf = new Configuration();
		final Job job = Job.getInstance(conf, NLineInputFormatTest.class.getSimpleName());
		// 1.1
		FileInputFormat.setInputPaths(job,
				"hdfs://192.168.1.10:9000/input/hehe");
		
		//这里改了一下，把TextInputFormat改成了NLineInputFormat
		NLineInputFormat.setNumLinesPerSplit(job, Integer.parseInt("2"));
		//NLineInputFormat.setNumLinesPerSplit(job, Integer.parseInt(args[0]));
		job.setInputFormatClass(NLineInputFormat.class);
		
		
		// 1.2
		job.setMapperClass(MyMapper.class);
		job.setMapOutputKeyClass(Text.class);
		job.setMapOutputValueClass(LongWritable.class);
		// 1.3 默认只有一个分区
		job.setPartitionerClass(HashPartitioner.class);
		job.setNumReduceTasks(1);
		// 1.4省略不写
		// 1.5省略不写

		// 2.2
		job.setReducerClass(MyReducer.class);
		job.setOutputKeyClass(Text.class);
		job.setOutputValueClass(LongWritable.class);
		// 2.3
		FileOutputFormat.setOutputPath(job, new Path(
				"hdfs://192.168.1.10:9000/out1"));
		job.setOutputFormatClass(TextOutputFormat.class);
		// 执行打成jar包的程序时，必须调用下面的方法
		job.setJarByClass(NLineInputFormatTest.class);
		job.waitForCompletion(true);
	}
}
</code></pre><br><br><p>结果：</p>
<p></p><pre><code class="language-java">[root@i-love-you hadoop]# bin/hadoop jar data/nline.jar
15/04/16 15:04:48 INFO client.RMProxy: Connecting to ResourceManager at /0.0.0.0:8032
15/04/16 15:04:50 WARN mapreduce.JobSubmitter: Hadoop command-line option parsing not performed. Implement the Tool interface and execute your application with ToolRunner to remedy this.
15/04/16 15:04:51 INFO input.FileInputFormat: Total input paths to process : 1
15/04/16 15:04:52 INFO mapreduce.JobSubmitter: number of splits:2
15/04/16 15:04:53 INFO mapreduce.JobSubmitter: Submitting tokens for job: job_1429167587909_0001
15/04/16 15:04:54 INFO impl.YarnClientImpl: Submitted application application_1429167587909_0001
15/04/16 15:04:54 INFO mapreduce.Job: The url to track the job: http://i-love-you:8088/proxy/application_1429167587909_0001/
15/04/16 15:04:54 INFO mapreduce.Job: Running job: job_1429167587909_0001
15/04/16 15:05:38 INFO mapreduce.Job: Job job_1429167587909_0001 running in uber mode : false
15/04/16 15:05:38 INFO mapreduce.Job:  map 0% reduce 0%
15/04/16 15:08:31 INFO mapreduce.Job:  map 100% reduce 0%
15/04/16 15:10:03 INFO mapreduce.Job:  map 100% reduce 100%
15/04/16 15:10:07 INFO mapreduce.Job: Job job_1429167587909_0001 completed successfully
15/04/16 15:10:08 INFO mapreduce.Job: Counters: 49
        File System Counters
                FILE: Number of bytes read=101
                FILE: Number of bytes written=318117
                FILE: Number of read operations=0
                FILE: Number of large read operations=0
                FILE: Number of write operations=0
                HDFS: Number of bytes read=248
                HDFS: Number of bytes written=29
                HDFS: Number of read operations=9
                HDFS: Number of large read operations=0
                HDFS: Number of write operations=2
        Job Counters
                Launched map tasks=2
                Launched reduce tasks=1
                Other local map tasks=2
                Total time spent by all maps in occupied slots (ms)=356494
                Total time spent by all reduces in occupied slots (ms)=63006
                Total time spent by all map tasks (ms)=356494
                Total time spent by all reduce tasks (ms)=63006
                Total vcore-seconds taken by all map tasks=356494
                Total vcore-seconds taken by all reduce tasks=63006
                Total megabyte-seconds taken by all map tasks=365049856
                Total megabyte-seconds taken by all reduce tasks=64518144
        Map-Reduce Framework
                Map input records=3
                Map output records=6
                Map output bytes=83
                Map output materialized bytes=107
                Input split bytes=200
                Combine input records=0
                Combine output records=0
                Reduce input groups=4
                Reduce shuffle bytes=107
                Reduce input records=6
                Reduce output records=4
                Spilled Records=12
                Shuffled Maps =2
                Failed Shuffles=0
                Merged Map outputs=2
                GC time elapsed (ms)=16068
                CPU time spent (ms)=7560
                Physical memory (bytes) snapshot=356200448
                Virtual memory (bytes) snapshot=2527195136
                Total committed heap usage (bytes)=257171456
        Shuffle Errors
                BAD_ID=0
                CONNECTION=0
                IO_ERROR=0
                WRONG_LENGTH=0
                WRONG_MAP=0
                WRONG_REDUCE=0
        File Input Format Counters
                Bytes Read=48
        File Output Format Counters
                Bytes Written=29
</code></pre><br><br><p><br></p>
<p><br></p>
<p>解析：</p>
<p>我设置每2行为一个InputSplit：</p>
<p></p><pre><code class="language-java">NLineInputFormat.setNumLinesPerSplit(job, Integer.parseInt("2"));</code></pre><br><p>在输出中可以看到splits的个数：</p>
<p></p><pre><code class="language-java">15/04/16 15:04:52 INFO mapreduce.JobSubmitter: number of splits:2</code></pre><br><p><br></p>
<p><br></p>
            </div>
                </div>