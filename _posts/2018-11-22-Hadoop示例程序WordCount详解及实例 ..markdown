---
layout:     post
title:      Hadoop示例程序WordCount详解及实例 .
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h2>1.图解MapReduce</h2>
<p><img alt="" src="http://hi.csdn.net/attachment/201101/4/0_12941441301s90.gif"></p>
<p> </p>
<p><img alt="" src="http://hi.csdn.net/attachment/201101/4/0_12941441348CAe.gif"></p>
<p> </p>
<p><img alt="" src="http://hi.csdn.net/attachment/201101/4/0_129414413795ZO.gif"></p>
<p> </p>
<p><img alt="" src="http://hi.csdn.net/attachment/201101/4/0_1294144140gAGH.gif"></p>
<h2><a name="t1"></a>2.简历过程：</h2>
<p></p>
<p><strong>Input:</strong></p>
<p>Hello World Bye World</p>
<p>Hello Hadoop Bye Hadoop</p>
<p>Bye Hadoop Hello Hadoop</p>
<p><strong>Map:</strong></p>
<p>&lt;Hello,1&gt;</p>
<p>&lt;World,1&gt;</p>
<p>&lt;Bye,1&gt;</p>
<p>&lt;World,1&gt;</p>
<p>&lt;Hello,1&gt;</p>
<p>&lt;Hadoop,1&gt;</p>
<p>&lt;Bye,1&gt;</p>
<p>&lt;Hadoop,1&gt;</p>
<p>&lt;Bye,1&gt;</p>
<p>&lt;Hadoop,1&gt;</p>
<p>&lt;Hello,1&gt;</p>
<p>&lt;Hadoop,1&gt;</p>
<p><strong>Sort:</strong></p>
<p>&lt;Bye,1&gt;</p>
<p>&lt;Bye,1&gt;</p>
<p>&lt;Bye,1&gt;</p>
<p>&lt;Hadoop,1&gt;</p>
<p>&lt;Hadoop,1&gt;</p>
<p>&lt;Hadoop,1&gt;</p>
<p>&lt;Hadoop,1&gt;</p>
<p>&lt;Hello,1&gt;</p>
<p>&lt;Hello,1&gt;</p>
<p>&lt;Hello,1&gt;</p>
<p>&lt;World,1&gt;</p>
<p>&lt;World,1&gt;</p>
<p><strong>Combine:</strong></p>
<p>&lt;Bye,1,1,1&gt;</p>
<p>&lt;Hadoop,1,1,1,1&gt;</p>
<p>&lt;Hello,1,1,1&gt;</p>
<p>&lt;World,1,1&gt;</p>
<p><strong>Reduce:</strong></p>
<p>&lt;Bye,3&gt;</p>
<p>&lt;Hadoop,4&gt;</p>
<p>&lt;Hello,3&gt;</p>
<p>&lt;World,2&gt;</p>
<h2><a name="t2"></a>3.代码实例：</h2>
<p></p>
<div>
<div>
<div><strong>[c-sharp]</strong> <a title="view plain" href="http://blog.csdn.net/xw13106209/article/details/6116323#" rel="nofollow">
view plain</a><a title="copy" href="http://blog.csdn.net/xw13106209/article/details/6116323#" rel="nofollow">copy</a><a title="print" href="http://blog.csdn.net/xw13106209/article/details/6116323#" rel="nofollow">print</a><a title="?" href="http://blog.csdn.net/xw13106209/article/details/6116323#" rel="nofollow">?</a></div>
</div>
<ol><li>package com.felix;  </li><li>import java.io.IOException;  </li><li>import java.util.Iterator;  </li><li>import java.util.StringTokenizer;  </li><li>import org.apache.hadoop.fs.Path;  </li><li>import org.apache.hadoop.io.IntWritable;  </li><li>import org.apache.hadoop.io.LongWritable;  </li><li>import org.apache.hadoop.io.Text;  </li><li>import org.apache.hadoop.mapred.FileInputFormat;  </li><li>import org.apache.hadoop.mapred.FileOutputFormat;  </li><li>import org.apache.hadoop.mapred.JobClient;  </li><li>import org.apache.hadoop.mapred.JobConf;  </li><li>import org.apache.hadoop.mapred.MapReduceBase;  </li><li>import org.apache.hadoop.mapred.Mapper;  </li><li>import org.apache.hadoop.mapred.OutputCollector;  </li><li>import org.apache.hadoop.mapred.Reducer;  </li><li>import org.apache.hadoop.mapred.Reporter;  </li><li>import org.apache.hadoop.mapred.TextInputFormat;  </li><li>import org.apache.hadoop.mapred.TextOutputFormat;  </li><li>/** </li><li> *  </li><li> * 描述：WordCount explains by Felix </li><li> * @author Hadoop Dev Group </li><li> */  </li><li>public class WordCount  </li><li>{  </li><li>    /** </li><li>     * MapReduceBase类:实现了Mapper和Reducer接口的基类（其中的方法只是实现接口，而未作任何事情） </li><li>     * Mapper接口： </li><li>     * WritableComparable接口：实现WritableComparable的类可以相互比较。所有被用作key的类应该实现此接口。 </li><li>     * Reporter 则可用于报告整个应用的运行进度，本例中未使用。  </li><li>     *  </li><li>     */  </li><li>    public static class Map extends MapReduceBase implements  </li><li>            Mapper&lt;LongWritable, Text, Text, IntWritable&gt;  </li><li>    {  </li><li>        /** </li><li>         * LongWritable, IntWritable, Text 均是 Hadoop 中实现的用于封装 Java 数据类型的类，这些类实现了WritableComparable接口， </li><li>         * 都能够被串行化从而便于在分布式环境中进行数据交换，你可以将它们分别视为long,int,String 的替代品。 </li><li>         */  </li><li>        private final static IntWritable one = new IntWritable(1);  </li><li>        private Text word = new Text();  </li><li>          </li><li>        /** </li><li>         * Mapper接口中的map方法： </li><li>         * void map(K1 key, V1 value, OutputCollector&lt;K2,V2&gt; output, Reporter reporter) </li><li>         * 映射一个单个的输入k/v对到一个中间的k/v对 </li><li>         * 输出对不需要和输入对是相同的类型，输入对可以映射到0个或多个输出对。 </li><li>         * OutputCollector接口：收集Mapper和Reducer输出的&lt;k,v&gt;对。 </li><li>         * OutputCollector接口的collect(k, v)方法:增加一个(k,v)对到output </li><li>         */  </li><li>        public void map(LongWritable key, Text value,  </li><li>                OutputCollector&lt;Text, IntWritable&gt; output, Reporter reporter)  </li><li>                throws IOException  </li><li>        {  </li><li>            String line = value.toString();  </li><li>            StringTokenizer tokenizer = new StringTokenizer(line);  </li><li>            while (tokenizer.hasMoreTokens())  </li><li>            {  </li><li>                word.set(tokenizer.nextToken());  </li><li>                output.collect(word, one);  </li><li>            }  </li><li>        }  </li><li>    }  </li><li>    public static class Reduce extends MapReduceBase implements  </li><li>            Reducer&lt;Text, IntWritable, Text, IntWritable&gt;  </li><li>    {  </li><li>        public void reduce(Text key, Iterator&lt;IntWritable&gt; values,  </li><li>                OutputCollector&lt;Text, IntWritable&gt; output, Reporter reporter)  </li><li>                throws IOException  </li><li>        {  </li><li>            int sum = 0;  </li><li>            while (values.hasNext())  </li><li>            {  </li><li>                sum += values.next().get();  </li><li>            }  </li><li>            output.collect(key, new IntWritable(sum));  </li><li>        }  </li><li>    }  </li><li>    public static void main(String[] args) throws Exception  </li><li>    {  </li><li>        /** </li><li>         * JobConf：map/reduce的job配置类，向hadoop框架描述map-reduce执行的工作 </li><li>         * 构造方法：JobConf()、JobConf(Class exampleClass)、JobConf(Configuration conf)等 </li><li>         */  </li><li>        JobConf conf = new JobConf(WordCount.class);  </li><li>        conf.setJobName("wordcount");           //设置一个用户定义的job名称   </li><li>        conf.setOutputKeyClass(Text.class);    //为job的输出数据设置Key类   </li><li>        conf.setOutputValueClass(IntWritable.class);   //为job输出设置value类   </li><li>        conf.setMapperClass(Map.class);         //为job设置Mapper类   </li><li>        conf.setCombinerClass(Reduce.class);      //为job设置Combiner类   </li><li>        conf.setReducerClass(Reduce.class);        //为job设置Reduce类   </li><li>        conf.setInputFormat(TextInputFormat.class);    //为map-reduce任务设置InputFormat实现类   </li><li>        conf.setOutputFormat(TextOutputFormat.class);  //为map-reduce任务设置OutputFormat实现类   </li><li>        /** </li><li>         * InputFormat描述map-reduce中对job的输入定义 </li><li>         * setInputPaths():为map-reduce job设置路径数组作为输入列表 </li><li>         * setInputPath()：为map-reduce job设置路径数组作为输出列表 </li><li>         */  </li><li>        FileInputFormat.setInputPaths(conf, new Path(args[0]));  </li><li>        FileOutputFormat.setOutputPath(conf, new Path(args[1]));  </li><li>        JobClient.runJob(conf);         //运行一个job   </li><li>    }  </li><li>}  </li></ol></div>
<p></p>
<p> </p>
<p>转自： <a href="http://blog.csdn.net/xw13106209/article/details/6116323#comments" rel="nofollow">
http://blog.csdn.net/xw13106209/article/details/6116323#comments</a>  感谢原作者</p>
            </div>
                </div>