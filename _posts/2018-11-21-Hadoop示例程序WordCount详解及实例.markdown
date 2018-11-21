---
layout:     post
title:      Hadoop示例程序WordCount详解及实例
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h2 style="color:rgb(51,51,51);">1.图解MapReduce</h2>
<p style="color:rgb(51,51,51);"><img src="http://hi.csdn.net/attachment/201101/4/0_12941441301s90.gif" alt=""></p>
<p style="color:rgb(51,51,51);"> </p>
<p style="color:rgb(51,51,51);"><img src="http://hi.csdn.net/attachment/201101/4/0_12941441348CAe.gif" alt=""></p>
<p style="color:rgb(51,51,51);"> </p>
<p style="color:rgb(51,51,51);"><img src="http://hi.csdn.net/attachment/201101/4/0_129414413795ZO.gif" alt=""></p>
<p style="color:rgb(51,51,51);"> </p>
<p style="color:rgb(51,51,51);"><img src="http://hi.csdn.net/attachment/201101/4/0_1294144140gAGH.gif" alt=""></p>
<h2 style="color:rgb(51,51,51);"><a name="t1" style="color:rgb(51,102,153);"></a>2.简历过程：</h2>
<p style="color:rgb(51,51,51);"><strong>Input:</strong></p>
<p style="color:rgb(51,51,51);">Hello World Bye World</p>
<p style="color:rgb(51,51,51);">Hello Hadoop Bye Hadoop</p>
<p style="color:rgb(51,51,51);">Bye Hadoop Hello Hadoop</p>
<p style="color:rgb(51,51,51);"><strong>Map:</strong></p>
<p style="color:rgb(51,51,51);">&lt;Hello,1&gt;</p>
<p style="color:rgb(51,51,51);">&lt;World,1&gt;</p>
<p style="color:rgb(51,51,51);">&lt;Bye,1&gt;</p>
<p style="color:rgb(51,51,51);">&lt;World,1&gt;</p>
<p style="color:rgb(51,51,51);">&lt;Hello,1&gt;</p>
<p style="color:rgb(51,51,51);">&lt;Hadoop,1&gt;</p>
<p style="color:rgb(51,51,51);">&lt;Bye,1&gt;</p>
<p style="color:rgb(51,51,51);">&lt;Hadoop,1&gt;</p>
<p style="color:rgb(51,51,51);">&lt;Bye,1&gt;</p>
<p style="color:rgb(51,51,51);">&lt;Hadoop,1&gt;</p>
<p style="color:rgb(51,51,51);">&lt;Hello,1&gt;</p>
<p style="color:rgb(51,51,51);">&lt;Hadoop,1&gt;</p>
<p style="color:rgb(51,51,51);"><strong>Sort:</strong></p>
<p style="color:rgb(51,51,51);">&lt;Bye,1&gt;</p>
<p style="color:rgb(51,51,51);">&lt;Bye,1&gt;</p>
<p style="color:rgb(51,51,51);">&lt;Bye,1&gt;</p>
<p style="color:rgb(51,51,51);">&lt;Hadoop,1&gt;</p>
<p style="color:rgb(51,51,51);">&lt;Hadoop,1&gt;</p>
<p style="color:rgb(51,51,51);">&lt;Hadoop,1&gt;</p>
<p style="color:rgb(51,51,51);">&lt;Hadoop,1&gt;</p>
<p style="color:rgb(51,51,51);">&lt;Hello,1&gt;</p>
<p style="color:rgb(51,51,51);">&lt;Hello,1&gt;</p>
<p style="color:rgb(51,51,51);">&lt;Hello,1&gt;</p>
<p style="color:rgb(51,51,51);">&lt;World,1&gt;</p>
<p style="color:rgb(51,51,51);">&lt;World,1&gt;</p>
<p style="color:rgb(51,51,51);"><strong>Combine:</strong></p>
<p style="color:rgb(51,51,51);">&lt;Bye,1,1,1&gt;</p>
<p style="color:rgb(51,51,51);">&lt;Hadoop,1,1,1,1&gt;</p>
<p style="color:rgb(51,51,51);">&lt;Hello,1,1,1&gt;</p>
<p style="color:rgb(51,51,51);">&lt;World,1,1&gt;</p>
<p style="color:rgb(51,51,51);"><strong>Reduce:</strong></p>
<p style="color:rgb(51,51,51);">&lt;Bye,3&gt;</p>
<p style="color:rgb(51,51,51);">&lt;Hadoop,4&gt;</p>
<p style="color:rgb(51,51,51);">&lt;Hello,3&gt;</p>
<p style="color:rgb(51,51,51);">&lt;World,2&gt;</p>
<h2 style="color:rgb(51,51,51);"><a name="t2" style="color:rgb(51,102,153);"></a>3.代码实例：</h2>
<div>
<div>
<div style="color:#C0C0C0;"><pre><code class="language-java">package com.felix;
import java.io.IOException;
import java.util.Iterator;
import java.util.StringTokenizer;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.LongWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapred.FileInputFormat;
import org.apache.hadoop.mapred.FileOutputFormat;
import org.apache.hadoop.mapred.JobClient;
import org.apache.hadoop.mapred.JobConf;
import org.apache.hadoop.mapred.MapReduceBase;
import org.apache.hadoop.mapred.Mapper;
import org.apache.hadoop.mapred.OutputCollector;
import org.apache.hadoop.mapred.Reducer;
import org.apache.hadoop.mapred.Reporter;
import org.apache.hadoop.mapred.TextInputFormat;
import org.apache.hadoop.mapred.TextOutputFormat;
/**
 * 
 * 描述：WordCount explains by Felix
 * @author Hadoop Dev Group
 */
public class WordCount
{
    /**
     * MapReduceBase类:实现了Mapper和Reducer接口的基类（其中的方法只是实现接口，而未作任何事情）
     * Mapper接口：
     * WritableComparable接口：实现WritableComparable的类可以相互比较。所有被用作key的类应该实现此接口。
     * Reporter 则可用于报告整个应用的运行进度，本例中未使用。 
     * 
     */
    public static class Map extends MapReduceBase implements
            Mapper&lt;LongWritable, Text, Text, IntWritable&gt;
    {
        /**
         * LongWritable, IntWritable, Text 均是 Hadoop 中实现的用于封装 Java 数据类型的类，这些类实现了WritableComparable接口，
         * 都能够被串行化从而便于在分布式环境中进行数据交换，你可以将它们分别视为long,int,String 的替代品。
         */
        private final static IntWritable one = new IntWritable(1);
        private Text word = new Text();
        
        /**
         * Mapper接口中的map方法：
         * void map(K1 key, V1 value, OutputCollector&lt;K2,V2&gt; output, Reporter reporter)
         * 映射一个单个的输入k/v对到一个中间的k/v对
         * 输出对不需要和输入对是相同的类型，输入对可以映射到0个或多个输出对。
         * OutputCollector接口：收集Mapper和Reducer输出的&lt;k,v&gt;对。
         * OutputCollector接口的collect(k, v)方法:增加一个(k,v)对到output
         */
        public void map(LongWritable key, Text value,
                OutputCollector&lt;Text, IntWritable&gt; output, Reporter reporter)
                throws IOException
        {
            String line = value.toString();
            StringTokenizer tokenizer = new StringTokenizer(line);
            while (tokenizer.hasMoreTokens())
            {
                word.set(tokenizer.nextToken());
                output.collect(word, one);
            }
        }
    }
    public static class Reduce extends MapReduceBase implements
            Reducer&lt;Text, IntWritable, Text, IntWritable&gt;
    {
        public void reduce(Text key, Iterator&lt;IntWritable&gt; values,
                OutputCollector&lt;Text, IntWritable&gt; output, Reporter reporter)
                throws IOException
        {
            int sum = 0;
            while (values.hasNext())
            {
                sum += values.next().get();
            }
            output.collect(key, new IntWritable(sum));
        }
    }
    public static void main(String[] args) throws Exception
    {
        /**
         * JobConf：map/reduce的job配置类，向hadoop框架描述map-reduce执行的工作
         * 构造方法：JobConf()、JobConf(Class exampleClass)、JobConf(Configuration conf)等
         */
        JobConf conf = new JobConf(WordCount.class);
        conf.setJobName("wordcount");           //设置一个用户定义的job名称
        conf.setOutputKeyClass(Text.class);    //为job的输出数据设置Key类
        conf.setOutputValueClass(IntWritable.class);   //为job输出设置value类
        conf.setMapperClass(Map.class);         //为job设置Mapper类
        conf.setCombinerClass(Reduce.class);      //为job设置Combiner类
        conf.setReducerClass(Reduce.class);        //为job设置Reduce类
        conf.setInputFormat(TextInputFormat.class);    //为map-reduce任务设置InputFormat实现类
        conf.setOutputFormat(TextOutputFormat.class);  //为map-reduce任务设置OutputFormat实现类
        /**
         * InputFormat描述map-reduce中对job的输入定义
         * setInputPaths():为map-reduce job设置路径数组作为输入列表
         * setInputPath()：为map-reduce job设置路径数组作为输出列表
         */
        FileInputFormat.setInputPaths(conf, new Path(args[0]));
        FileOutputFormat.setOutputPath(conf, new Path(args[1]));
        JobClient.runJob(conf);         //运行一个job
    }
}</code></pre><br><br></div>
</div>
</div>
<div>原文地址：http://blog.csdn.net/xw13106209/article/details/6116323</div>
            </div>
                </div>