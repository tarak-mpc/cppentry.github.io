---
layout:     post
title:      hadoop-(2)wordcount运行
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/szh1124/article/details/50420103				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">1、linux本地创建input、output文件夹</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><img src="https://img-blog.csdn.net/20151228170508640?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">2、将本地input文件夹中的所有内容拷贝到 Hadoop的/in文件夹下</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">首先hadoop创建in文件夹<span style="color:rgb(255,0,0);background-color:inherit;">b</span><span style="color:rgb(255,0,0);background-color:inherit;">in/hadoop fs -mkdir  /in</span></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><span style="background-color:inherit;"><span style="color:#ff0000;background-color:inherit;">b</span><span style="color:#ff0000;background-color:inherit;">in/hadoop fs -put ../input/*  /in</span></span></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><span style="background-color:inherit;"><span style="color:#ff0000;background-color:inherit;"><img src="https://img-blog.csdn.net/20151228170512098?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></span></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><strong><span style="color:#ff0000;background-color:inherit;"><span style="color:rgb(0,0,0);font-weight:normal;">3、查看Hadoop文件系统/in文件夹下的文件</span><br style="background-color:inherit;"></span></strong></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><strong><span style="color:#ff0000;background-color:inherit;">b</span><span style="color:#ff0000;background-color:inherit;">in/hadoop fs -ls   /in</span></strong><br style="background-color:inherit;"></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><img src="https://img-blog.csdn.net/20151228170515888?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><img src="https://img-blog.csdn.net/20151228170518810?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">运行<span style="color:rgb(51,51,51);font-family:'Microsoft YaHei', Verdana, sans-serif, SimSun;font-size:13px;background-color:rgb(250,250,250);">wordcount：</span></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><span style="font-family:'Microsoft YaHei', Verdana, sans-serif, SimSun;font-size:13px;background-color:rgb(250,250,250);"><span style="color:#ff0000;background-color:inherit;"> bin/hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-2.6.3.jar
  wordcount /in/ /output/wordcount1</span></span></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><span style="font-family:'Microsoft YaHei', Verdana, sans-serif, SimSun;font-size:13px;background-color:rgb(250,250,250);"><span style="color:#ff0000;background-color:inherit;"><img src="https://img-blog.csdn.net/20151228170521503?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><img src="https://img-blog.csdn.net/20151228170525026?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br style="background-color:inherit;"></span></span></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><span style="font-family:'Microsoft YaHei', Verdana, sans-serif, SimSun;font-size:13px;background-color:rgb(250,250,250);"><span style="color:#ff0000;background-color:inherit;"><br></span></span></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><img src="https://img-blog.csdn.net/20151228170847419?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">查看执行结果</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><span style="background-color:inherit;"><span style="color:#ff0000;background-color:inherit;">hadoop fs -cat /output/wordcount1/*</span></span></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><img src="https://img-blog.csdn.net/20151228170528047?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br style="background-color:inherit;"></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><br></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">————————————————————————————————————————————</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">自己编写mapreduce程序运行，基于hadoop2.6.3版本</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">
<pre style="font-family:'Courier New', Arial;font-size:13px;background-color:rgb(255,255,255);"></pre><div style="color:rgb(51,51,51);background-color:inherit;">reduce开始之前，map必须完成</div><div style="color:rgb(51,51,51);background-color:inherit;">每读一行数据，调用一次Mapper类的map方法。</div><div style="color:rgb(51,51,51);background-color:inherit;"><br style="background-color:inherit;"></div><div style="background-color:inherit;color:rgb(51,51,51);"><span style="background-color:inherit;">map    输入：key——&gt;起始偏移量，Long；value——&gt;一行数据，String；</span></div><div style="background-color:inherit;color:rgb(51,51,51);"><span style="background-color:inherit;">       输出：key——&gt;一个单词，String；value——&gt;数量1，<strong>Long</strong>；</span></div><div style="background-color:inherit;"><div style="background-color:inherit;"><span style="color:#ff0000;background-color:inherit;">public class WordCountMapper extends Mapper&lt;Long, String, String, Long&gt; {</span></div></div><div style="background-color:inherit;color:rgb(51,51,51);">但是这些基本类型是不能直接用的，输入输出都是经过网络传递的，有序列化的过程，String、Long都是实现了java序列化接口的，可以在网上传，但是jdk的序列化机制在Hadoop下效率不是很高。Hadoop把这些序列化机制重新实现了一套，String、Long这些类型不符合Hadoop序列化机制。</div><div style="background-color:inherit;color:rgb(51,51,51);">Long——&gt;LongWritable</div><div style="background-color:inherit;color:rgb(51,51,51);">String——&gt;Text</div><div style="background-color:inherit;"><div style="background-color:inherit;"><span style="color:#ff0000;background-color:inherit;">public class WordCountMapper extends Mapper&lt;LongWritable, Text, Text, LongWritable&gt;</span></div></div>
<pre style="font-family:'Courier New', Arial;color:rgb(51,51,51);font-size:13px;line-height:19px;background-color:rgb(255,255,255);"></pre><div style="background-color:inherit;"><span style="background-color:inherit;">reduce 输入：<span style="color:rgb(51,51,51);font-family:'Courier New', Arial;font-size:13px;line-height:21px;">key——&gt;一个单词，String；value——&gt;数量1，</span><span style="color:rgb(51,51,51);font-family:'Courier New', Arial;font-size:13px;line-height:21px;"><strong>Long</strong></span><span style="color:rgb(51,51,51);font-family:'Courier New', Arial;font-size:13px;line-height:21px;">；</span></span></div><div style="background-color:inherit;"><span style="background-color:inherit;">       输出：key——&gt;一个单词，String；value——&gt;数量n，<span style="color:rgb(51,51,51);font-family:'Courier New', Arial;font-size:13px;line-height:21px;"><strong>Long</strong></span>；</span></div>
</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><span style="color:rgb(255,0,0);font-family:'Courier New', Arial;font-size:13px;line-height:19px;">public class WordCountReduce extends Reducer&lt;Text, LongWritable, Text, LongWritable&gt; {</span><br style="background-color:inherit;"></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><br style="background-color:inherit;"></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><strong>1、编写程序</strong></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><pre><code class="language-java">package jvm.hadoop.starter;

import java.io.IOException;
import java.util.StringTokenizer;

import org.apache.hadoop.io.LongWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Mapper;

public class WordCountMapper extends Mapper&lt;LongWritable, Text, Text, LongWritable&gt; {
	private static final LongWritable one = new LongWritable(1);
	private Text word = new Text();

	@Override
	protected void map(LongWritable key, Text value,
			Mapper&lt;LongWritable, Text, Text, LongWritable&gt;.Context context)
					throws IOException, InterruptedException {

		// 获取到一行文件的内容
		String line = value.toString();
		// 切分这一行的内容为一个单词数组
		StringTokenizer itr = new StringTokenizer(line, " ");// 行数据，分隔符
		// 遍历输出&lt;word，1&gt;
		while (itr.hasMoreTokens()) {
			this.word.set(itr.nextToken());
			context.write(this.word, one);// map的输出也是&lt;K,V&gt;形式
		}
	}
}
</code></pre><br><pre><code class="language-java">package jvm.hadoop.starter;

import java.io.IOException;
import java.util.StringTokenizer;

import org.apache.hadoop.io.LongWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Reducer;

// key:hello, values:{1,1,1。。。。}
public class WordCountReduce extends Reducer&lt;Text, LongWritable, Text, LongWritable&gt; {
	@Override
	protected void reduce(Text key, Iterable&lt;LongWritable&gt; values,
			Reducer&lt;Text, LongWritable, Text, LongWritable&gt;.Context context)
					throws IOException, InterruptedException {
		// 定义一个累加计数器
		long count = 0;
		for (LongWritable value : values) {
			count += value.get();
		}
		// 输出&lt;单词：count&gt;键值对
		context.write(key, new LongWritable(count));
	}

	public static void main(String[] args) {
		StringTokenizer st = new StringTokenizer("this，is.a test", "，| ");
		while (st.hasMoreTokens()) {
			System.out.println(st.nextToken());
		}
	}

}
</code></pre><br><pre><code class="language-java">package jvm.hadoop.starter;

import java.io.IOException;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.LongWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;

/**
 * 用来描述一个作业job（使用哪个Mapper类，哪个reducer类，输入文件在哪，输出结果放哪） ； 然后提交这个job给Hadoop集群
 *
 */
// jvm.hadoop.starter.WordCountRunner
public class WordCountRunner {
	public static void main(String[] args)
			throws IOException, ClassNotFoundException, InterruptedException {
		// 用静态方法产生实例
		Configuration conf = new Configuration();
		Job wordcount = Job.getInstance(conf);

		// 当前job资源所在jar包：main方法所在的类
		wordcount.setJarByClass(WordCountRunner.class);

		// wordcount要使用哪个Mapper类、Reducer类
		wordcount.setMapperClass(WordCountMapper.class);
		wordcount.setReducerClass(WordCountReduce.class);

		// wordcount的maper类输出的kv数据类型,reducer类的输出类型没写，代表跟mapper一样
		// wordcount.setMapOutputKeyClass(Text.class);
		// wordcount.setMapOutputKeyClass(LongWritable.class);

		// wordcount的reducer类输出的kv数据类型
		wordcount.setOutputKeyClass(Text.class);
		wordcount.setOutputValueClass(LongWritable.class);

		// 指定要处理的原始数据所存放的路径,都是文件夹级别的
		FileInputFormat.setInputPaths(wordcount, "hdfs://master:9000/wc/srcdata");

		// 指定处理之后的结果输出到哪个路径
		FileOutputFormat.setOutputPath(wordcount, new Path("hdfs://master:9000/wc/output"));

		boolean res = wordcount.waitForCompletion(true);
		System.exit(res ? 0 : 1);

	}

}
</code></pre>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><br></div>
2、打成jar包，命名为wordcount.jar，把jar包放在/opt/workspace目录下</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">3、hdfs新建/wc/srcdata目录，并在该目录下上传一个要进行wordcount的文件</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">hadoop fs -mkdir /wc/srcdata</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><span style="color:rgb(51,51,51);font-family:'Times New Roman';font-size:16px;line-height:24px;">hadoop fs –put /opt/file.txt  /wc/srcdata</span></div>
<div><span style="font-family:'Times New Roman';font-size:14px;color:#333333;"><span style="line-height:24px;">4、执行命令运行</span></span></div>
<div><span style="font-family:'Times New Roman';font-size:14px;color:#333333;"><span style="line-height:24px;">在/opt/workspace目录下执行hadoop jar wordcount.jar jvm.hadoop.starter.WordCountRunner<br></span></span><br style="background-color:inherit;"></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><br style="background-color:inherit;"></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">期间遇到的问题：<span style="font-family:arial, 'courier new', courier, '宋体', monospace, 'Microsoft YaHei';font-size:14px;">运行hadoop fs -put命令报no root to host</span></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">发现是从节点的防火墙没有关闭，关闭防火墙即可</div>
            </div>
                </div>