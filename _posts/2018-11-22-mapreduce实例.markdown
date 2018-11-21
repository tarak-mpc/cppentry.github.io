---
layout:     post
title:      mapreduce实例
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/qq_32632603/article/details/76350758				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-family:'FangSong_GB2312';font-size:14px;">1.统计单词个数</span></p>
<p><span style="font-family:'FangSong_GB2312';font-size:14px;">（1）统计样本：word.txt（hadoop fs -put word.txt /word）</span></p>
<p><span style="font-family:'FangSong_GB2312';font-size:14px;">number linux hadoop word count<br>
linux number hadoop word count<br>
linux hadoop eclipse word count<br>
linux hadoop word java count<br>
eclipse<br>
java map reduce<br></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">(2)WordMap.java</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">package com.test.org;</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">import java.io.IOException;</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">import org.apache.hadoop.io.IntWritable;</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">import org.apache.hadoop.io.Text;</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">import org.apache.hadoop.mapreduce.Mapper;</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;"><br></span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;"><span style="color:#33cc00;">//例:number linux hadoop word count</span></span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">public class WordMap extends Mapper&lt;Object, Text, Text, IntWritable&gt;{</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;"><span style="color:#33cc00;">//每次传入一行数据，输入类型为object,text--&gt;0，</span><span style="color:rgb(51,204,0);">number linux hadoop word count</span></span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;"> @Override</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">protected void map(Object key, Text value,Context context)</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">throws IOException, InterruptedException {</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;color:#33cc00;">//以空格“ ”切分字符，{number，linux，hadoop，word，count}==&gt;lines</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">String[] lines=value.toString().split(" ");</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">for (String word : lines) {</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">context.write(new Text(word), new IntWritable(1));</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;color:#33cc00;">/*输出类型为text，intwritable</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;color:#33cc00;">*(number,1)</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;color:#33cc00;">*(linux,1)</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;color:#33cc00;">*(hadoop,1)</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;color:#33cc00;">*(word,1)</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;color:#33cc00;">*count,1)</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;color:#33cc00;">*/</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">}</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">}</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">}</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">(2)WordReduce.java</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">package com.test.org;</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;"> </span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">import java.io.IOException;</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;"> </span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">import org.apache.hadoop.io.IntWritable;</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">import org.apache.hadoop.io.Text;</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">import org.apache.hadoop.mapreduce.Reducer;</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;color:#33cc00;"> //&lt;linux,{1,1,1,1,}&gt;</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">public class WordReduce extends Reducer&lt;Text, IntWritable, Text,IntWritable&gt;{</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">@Override</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">protected void reduce(Text key, Iterable&lt;IntWritable&gt; values,</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">Context context) throws IOException, InterruptedException {</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">int sum=0;  <span style="color:#33cc00;">//计数器</span></span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">for (IntWritable count : values) {</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">sum+=count.get();</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">}</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">context.write(key, new IntWritable(sum));
<span style="color:#009900;"> //&lt;linux,4&gt;</span></span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;"> }</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">}</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;"> </span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;"> </span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">(3)WordCount.java</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">package com.test.org;</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;"> </span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">import java.io.IOException;</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;"> </span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">import org.apache.hadoop.conf.Configuration;</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">import org.apache.hadoop.fs.Path;</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">import org.apache.hadoop.io.IntWritable;</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">import org.apache.hadoop.io.Text;</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">import org.apache.hadoop.mapreduce.Job;</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;"> </span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">public class WordCount {</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">public static void main(String[] args) throws IOException, ClassNotFoundException, InterruptedException{</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;color:#33cc00;">//if语句判断输入输出路径是否正确</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">if(args.length !=2||args ==null){</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">System.out.println("input error");</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">System.exit(0);</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">}</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">Configuration conf=new Configuration();</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">@SuppressWarnings("deprecation")</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">Job job=new Job(conf, "WordCount");</span></span></p>
<p></p>
<p><span style="font-family:'FangSong_GB2312';font-size:14px;color:#33cc00;">//设置主类及mapper、reducer的业务实现类</span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">job.setJarByClass(WordCount.class);</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">job.setMapperClass(WordMap.class);</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">job.setReducerClass(WordReduce.class);</span></span></p>
<p></p>
<p><span style="font-family:'FangSong_GB2312';font-size:14px;color:#33cc00;">//设置输入输出路径</span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">FileInputFormat.setInputPaths(job, new Path(args[0]));</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">FileOutputFormat.setOutputPath(job, new Path(args[1]));</span></span></p>
<p><span style="font-family:'FangSong_GB2312';font-size:14px;"><br></span></p>
<p><span style="font-family:'FangSong_GB2312';font-size:14px;color:#33cc00;">//设置输出的key与value的类型</span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">job.setOutputKeyClass(Text.class);</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">job.setOutputValueClass(IntWritable.class);</span></span></p>
<p></p>
<p><span style="font-family:'FangSong_GB2312';font-size:14px;color:#33cc00;">//判断任务是否完成</span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">job.waitForCompletion(true);</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">}</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">}</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;"> <img src="https://img-blog.csdn.net/20170729192823321?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMzI2MzI2MDM=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""></span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">2.以“序号 单词”输出</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">package com.test.word;</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;"> </span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">import java.io.IOException;</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;"> </span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">import org.apache.hadoop.conf.Configuration;</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">import org.apache.hadoop.fs.Path;</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">import <span>org.apache.hadoop.io.IntWritable</span>;</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">import org.apache.hadoop.io.Text;</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">import org.apache.hadoop.mapreduce.Job;</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">import org.apache.hadoop.mapreduce.Mapper;</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">import org.apache.hadoop.mapreduce.Reducer;</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;"> </span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">public class SingleNum {</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">public static class NumMap extends Mapper&lt;Object, Text , Text,
<span>IntWritable</span>&gt;{</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">@Override</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">protected void map(Object key, Text value, Context context)</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">throws IOException, InterruptedException {</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">String[] lines=value.toString().split(" ");</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">for (String word : lines) {</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;color:#990000;">//不需要计数，故不设置value值</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">context.write(new Text(word),<span style="color:#cc0000;">new <span>IntWritable</span>()</span>);</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">}</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">}</span></span></p>
<p><span style="font-family:'FangSong_GB2312';font-size:14px;color:#990000;">//设置输出的&lt;key，value&gt;的数据类型为intwritable,text</span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">public static class NumReduce extends Reducer&lt;Text,
<span>IntWritable</span>, <span style="color:#cc0000;"><span>IntWritable</span>,Text</span>&gt;{</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;color:#cc0000;">int number=1;</span><span style="font-family:'FangSong_GB2312';font-size:14px;color:#990000;">//序号值</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">@Override</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">protected void reduce(Text key, Iterable&lt;<span>IntWritable</span>&gt; values,</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">Context context ) throws IOException, InterruptedException {</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">context.write(new <span>IntWritable</span>(number), key);</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">number++;</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">}</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">}</span></span></p>
<p></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">public static void main(String []args) throws IOException, ClassNotFoundException, InterruptedException{</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">if (args.length!=2||args==null) {</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">System.<em>out</em>.println("error!");</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">System.<em>exit</em>(0);</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">}</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">Configuration configuration=new Configuration();</span></span></p>
<p></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">@SuppressWarnings("deprecation")</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">Job job=new <del>Job</del>(configuration,"SingleNum");</span></span></p>
<p></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">job.setJarByClass(SingleNum.class);</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">job.setMapperClass(NumMap.class);</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">job.setReducerClass(NumReduce.class);</span></span></p>
<p></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">FileInputFormat.<em>setInputPaths</em>(job, new Path(args[0]));</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">FileOutputFormat.<em>setOutputPath</em>(job, new Path(args[1]));</span></span></p>
<p><span style="font-family:'FangSong_GB2312';font-size:14px;"><br></span></p>
<p><span style="font-family:'FangSong_GB2312';font-size:14px;color:#990000;">//因为mapper输出的数据类型与reduce的数据类型不同，故需要设置mapper的key与value值</span></p>
<p></p>
<p><span style="font-family:'FangSong_GB2312';font-size:14px;color:#cc0000;background-color:rgb(255,255,255);">job.setMapOutputKeyClass(Text.class);</span></p>
<p><span style="font-family:'FangSong_GB2312';font-size:14px;color:#cc0000;background-color:rgb(255,255,255);">job.setMapOutputValueClass(<span>IntWritable</span>.class);</span></p>
<p></p>
<p><span style="font-family:'FangSong_GB2312';font-size:14px;color:#cc0000;background-color:rgb(255,255,255);">job.setOutputKeyClass(<span>IntWritable</span>.class);</span></p>
<p><span style="font-family:'FangSong_GB2312';font-size:14px;color:#cc0000;background-color:rgb(255,255,255);">job.setOutputValueClass(Text.class);</span></p>
<p></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">job.waitForCompletion(true);</span></span></p>
<p></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">}</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">}</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">}</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;"> <img src="https://img-blog.csdn.net/20170729192829256?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMzI2MzI2MDM=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""></span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">3.计算平均分</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">（1）样本：grade.txt</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">小红    89<br>
小明    78<br>
小明    90<br>
小红    79<br>
小明    98<br>
小明    84<br>
灵儿    86<br>
灵儿    78<br>
灵儿    96 </span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;"><br></span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">（2）AvgGrade.java</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">package com.test.word;</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;"> </span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">import java.io.IOException;</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;"> </span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">import org.apache.hadoop.conf.Configuration;</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">import org.apache.hadoop.fs.Path;</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">import org.apache.hadoop.io.DoubleWritable;</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">import org.apache.hadoop.io.IntWritable;</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">import org.apache.hadoop.io.Text;</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">import org.apache.hadoop.mapreduce.Job;</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">import org.apache.hadoop.mapreduce.Mapper;</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">import org.apache.hadoop.mapreduce.Reducer;</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;"> </span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">public class AvgGrade {</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">public static class AvgMap extends Mapper&lt;Object, Text, Text, IntWritable&gt;{</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">@Override</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">protected void map(Object key, Text value, Context context)</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">throws IOException, InterruptedException {</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;color:#993399;">//按Tab键切分数据</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">String[] lines=value.toString().split(<span style="color:#6600cc;">"\t"</span>);</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;color:#6600cc;">//lines[0]代表姓名，lines[1]代表各科成绩</span></span></p>
<p><span style="font-family:'FangSong_GB2312';font-size:14px;background-color:rgb(255,255,255);">int grade=Integer.<em>parseInt</em>(<span style="color:#6600cc;">lines[1]</span>);</span></p>
<p><span style="font-family:'FangSong_GB2312';font-size:14px;"><span style="background-color:rgb(255,255,255);">context.write(new Text(<span style="color:#6600cc;">lines[0]</span>),new IntWritable(grade));</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">}</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">}</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;color:#993399;">//输出的数据类型为text，doublewritable</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">public static class AvgReduce extends Reducer&lt;Text, IntWritable,
<span style="color:#6600cc;">Text, DoubleWritable</span>&gt;{</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">@Override</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">protected void reduce(Text key, Iterable&lt;IntWritable&gt; values,</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">Context context) throws IOException, InterruptedException {</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">double sum=0;<span style="color:#993399;">//计算总成绩</span></span></span></p>
<p><span style="font-family:'FangSong_GB2312';font-size:14px;"><span style="background-color:rgb(255,255,255);">int count=0;<span style="color:#993399;">//计数器</span></span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">for (IntWritable word : values) {</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">sum+=word.get();</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">count++;</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">}</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">context.write(key,
<span style="color:#6600cc;">new DoubleWritable(sum/count)</span>);</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">}</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">}</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">public static void main(String[] args) throws IOException, ClassNotFoundException, InterruptedException{</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">if(args.length!=2||args==null){</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">System.<em>out</em>.println("error");</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">System.<em>exit</em>(0);</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">}</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">Configuration configuration=new Configuration();</span></span></p>
<p></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">@SuppressWarnings("deprecation")</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">Job job=new <del>Job</del>(configuration, "AvgGrade");</span></span></p>
<p></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">job.setJarByClass(AvgGrade.class);</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">job.setMapperClass(AvgMap.class);</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">job.setReducerClass(AvgReduce.class);</span></span></p>
<p></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">FileInputFormat.<em>setInputPaths</em>(job,new Path(args[0]));</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">FileOutputFormat.<em>setOutputPath</em>(job, new Path(args[1]));</span></span></p>
<p></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;color:#6600cc;">job.setMapOutputKeyClass(Text.class);</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;color:#6600cc;">job.setMapOutputValueClass(IntWritable.class);</span></span></p>
<p></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;color:#6600cc;">job.setOutputKeyClass(Text.class);</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;color:#6600cc;">job.setOutputValueClass(DoubleWritable.class);</span></span></p>
<p></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">job.waitForCompletion(true);</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">}</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;">}</span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'FangSong_GB2312';font-size:14px;"> <img src="https://img-blog.csdn.net/20170729192836076?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMzI2MzI2MDM=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""></span></span></p>
            </div>
                </div>