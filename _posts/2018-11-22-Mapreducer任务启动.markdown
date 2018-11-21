---
layout:     post
title:      Mapreducer任务启动
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-size:18px;color:#ff0000;"><strong>一、Streaming</strong></span></p>
<p>创建start.sh文件，内容如下</p>
<p></p>
<p>#!/bin/bash</p>
<p>hadoop fs -rm -r/data/apps/zhangwenchao/mapreduce/streaming/wordcount/output</p>
<p> </p>
<p>hadoop jar /data/tools/hadoop/hadoop-2.6.2/share/hadoop/tools/lib/hadoop-streaming-2.6.2.jar\</p>
<p>       -input /data/apps/zhangwenchao/mapreduce/streaming/wordcount/input \</p>
<p>       -output /data/apps/zhangwenchao/mapreduce/streaming/wordcount/output \</p>
<p>       -mapper "sh -x mapper.sh" \</p>
<p>       -reducer "sh -x reducer.sh" \</p>
<p>       -file mapper.sh \</p>
<p>       -file reducer.sh \</p>
<p>       -jobconf mapred.job.name=wordcount \</p>
<p>       -jobconf mapred.job.tasks=5 \</p>
<p>       -jobconf mapred.reduce.tasks=3</p>
<p><img src="https://img-blog.csdn.net/20161022100220841?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
注意：输出目录不能存在，故启动任务前，删除。mapper.sh，reducer.sh文件与start.sh文件同属于当前目录下<br><p><br></p>
<p><strong><span style="font-size:18px;color:#ff0000;">二、Python</span></strong></p>
<p>创建start.sh文件，内容如下<br></p>
<p>#!/bin/bash<br><br>
hadoop fs -rm -r /data/apps/zhangwenchao/mapreduce/python/wordcount/output<br><br>
hadoop jar /data/tools/hadoop/hadoop-2.6.2/share/hadoop/tools/lib/hadoop-streaming-2.6.2.jar \<br>
        -input /data/apps/zhangwenchao/mapreduce/python/wordcount/input \<br>
        -output /data/apps/zhangwenchao/mapreduce/python/wordcount/output \<br>
        -mapper "mapper.py" \<br>
        -reducer "reducer.py" \<br>
        -file mapper.py \<br>
        -file reducer.py \<br>
        -jobconf mapred.job.name=wordcount \<br>
        -jobconf mapred.job.tasks=5 \<br>
        -jobconf mapred.reduce.tasks=3<br></p>
<p><img src="https://img-blog.csdn.net/20161022100715458?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p>注意：输出目录不能存在，故启动任务前，删除。mapper.sh，reducer.sh文件与start.sh文件同属于当前目录下<br></p>
<p><br></p>
<p><span style="font-size:18px;color:#ff0000;"><strong>三、Java</strong></span></p>
<p></p>
<div align="left" style="color:rgb(68,68,68);line-height:21px;">
<span style="color:rgb(0,0,0);"><span style="font-family:SimSun;"><span style="font-size:14px;">hadoop jar x.jar 是运行包的一种方式，完成的方式为</span></span></span></div>
<div align="left" style="color:rgb(68,68,68);line-height:21px;">
<span style="color:#000;"><span style="font-family:SimSun;"><span style="font-size:14px;">hadoop jar  x.jar  ×××.MainClassName inputPath outputPath</span></span></span></div>
<div align="left" style="color:rgb(68,68,68);line-height:21px;">
<span style="color:#000;"><span style="font-family:SimSun;"><span style="font-size:14px;">同时解释一下：</span></span></span></div>
<div align="left" style="color:rgb(68,68,68);line-height:21px;">
<span style="color:#000;"><span style="font-family:SimSun;"><span style="font-size:14px;">x.jar为包的名称，包括路径，直接写包名称，则为默认路径</span></span></span></div>
<div align="left" style="color:rgb(68,68,68);line-height:21px;">
<span style="color:#000;"><span style="font-family:SimSun;"><span style="font-size:14px;">×××.MainClassName为运行的类名称</span></span></span></div>
<div align="left" style="color:rgb(68,68,68);line-height:21px;">
<span style="color:#000;"><span style="font-family:SimSun;"><span style="font-size:14px;">inputPath为输入路径</span></span></span></div>
<div align="left" style="color:rgb(68,68,68);line-height:21px;">
<span style="color:#000;"><span style="font-family:SimSun;"><span style="font-size:14px;">outputPath为输出路径。</span></span></span></div>
<div align="left" style="color:rgb(68,68,68);line-height:21px;">
<span style="color:#000000;"><span style="font-family:SimSun;"><span style="font-size:14px;">这里需要注意的是：</span></span></span></div>
<div align="left" style="color:rgb(68,68,68);line-height:21px;">
<span style="color:rgb(0,0,0);"><span style="font-family:SimSun;"><span style="font-size:14px;">输入路径和输出路径，一定要对应。</span></span></span></div>
<span style="font-size:14px;"><br></span>
<p></p>
<p><span style="font-size:14px;">我们也可以在java代码里指定输入输出路径，这里启动mapreduce任务就可以不用指定路径了</span></p>
<p><span style="font-size:14px;">如hadoop jar wordcount.jar Main</span></p>
<p><span style="font-size:14px;">Main函数如下：</span></p>
<p><span style="font-size:14px;">import org.apache.hadoop.conf.Configuration;<br>
import org.apache.hadoop.fs.Path;<br>
import org.apache.hadoop.io.IntWritable;<br>
import org.apache.hadoop.io.Text;<br>
import org.apache.hadoop.mapreduce.Job;<br>
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;<br>
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;<br><br>
public class Main {<br><span></span>public static void main(String[] args) throws Exception {<br><br><span></span>String input = "/test/zhang*/mapreduce/java/wordcount/input";<br><span></span>String output = "/test/zhang*/mapreduce/java/wordcount/output";<br><span></span>Configuration conf = new Configuration();<br><br><span></span>Job job = new Job(conf);<br><span></span>job.setJobName("test");<br><span></span>job.setJarByClass(Main.class);<br><br><span></span>FileInputFormat.addInputPath(job, new Path(input));<br><span></span>FileOutputFormat.setOutputPath(job, new Path(output));<br><br><span></span>job.setMapperClass(MyMap.class);<br><span></span>job.setReducerClass(MyReduce.class);<br><br><span></span>job.setOutputKeyClass(Text.class);<br><span></span>job.setOutputValueClass(IntWritable.class);<br><br><span></span>job.setCombinerClass(MyReduce.class);<br><br><span></span>job.setNumReduceTasks(3);<br><br><span></span>job.waitForCompletion(true);<br><span></span>}<br>
}<br></span></p>
            </div>
                </div>