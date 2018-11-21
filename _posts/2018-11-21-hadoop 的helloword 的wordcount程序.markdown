---
layout:     post
title:      hadoop 的helloword 的wordcount程序
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p style="font-size:16px;line-height:1.7em;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
<a href="http://www.tuicool.com/articles/v6VNza" rel="nofollow">http://www.tuicool.com/articles/v6VNza</a><br></p>
<p style="font-size:16px;line-height:1.7em;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
Hadoop的框架最核心的设计就是：HDFS和MapReduce。HDFS为海量的数据提供了存储，MapReduce则为海量的数据提供了计算。</p>
<p style="font-size:16px;line-height:1.7em;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
HDFS是Google File System（GFS）的开源实现。</p>
<p style="font-size:16px;line-height:1.7em;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
MapReduce是Google MapReduce的开源实现。</p>
<p style="font-size:16px;line-height:1.7em;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
HDFS和MapReduce实现是完全分离的，并不是没有HDFS就不能MapReduce运算。</p>
<p style="font-size:16px;line-height:1.7em;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
本文主要参考了以下三篇博客学习整理而成。</p>
<p style="font-size:16px;line-height:1.7em;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
1、 <a href="http://www.cnblogs.com/xwdreamer/archive/2011/01/04/2297049.html" rel="nofollow">Hadoop示例程序WordCount详解及实例</a></p>
<p style="font-size:16px;line-height:1.7em;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
2、 <a href="http://www.cnblogs.com/sharpxiajun/p/3151395.html" rel="nofollow">hadoop 学习笔记：mapreduce框架详解</a></p>
<p style="font-size:16px;line-height:1.7em;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
3、 <a href="http://www.cnblogs.com/york-hust/archive/2011/07/19/2110967.html" rel="nofollow">hadoop示例程序wordcount分析</a></p>
<h3 style="font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;line-height:1.6em;color:rgb(51,51,51);font-size:18px;text-indent:1em;">
1、MapReduce整体流程</h3>
<p style="font-size:16px;line-height:1.7em;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
最简单的MapReduce应用程序至少包含 3 个部分：一个 Map 函数、一个 Reduce 函数和一个 main 函数。在运行一个mapreduce计算任务时候，任务过程被分为两个阶段：map阶段和reduce阶段，每个阶段都是用键值对（key/value）作为输入（input）和输出（output）。main 函数将作业控制和文件输入/输出结合起来。</p>
<p style="font-size:16px;line-height:1.7em;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
<img src="http://img1.tuicool.com/IbMBb2.jpg" class="alignCenter" alt="" style="vertical-align:middle;border:0px none;text-align:center;display:block;width:550px;"></p>
<ul style="list-style-type:none;font-size:16px;line-height:1.7em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;"><li style="line-height:1.7em;list-style-type:disc;">并行读取文本中的内容，然后进行MapReduce操作。</li></ul><p style="font-size:16px;line-height:1.7em;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
<img src="http://img2.tuicool.com/NjUVra7.jpg" class="alignCenter" alt="" style="vertical-align:middle;border:0px none;text-align:center;display:block;width:550px;"></p>
<div style="font-size:16px;line-height:1.7em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
<ul style="list-style-type:none;line-height:1.7em;"><li style="line-height:1.7em;list-style-type:disc;">Map过程：并行读取文本，对读取的单词进行map操作，每个词都以&lt;key,value&gt;形式生成。</li></ul><p style="line-height:1.7em;text-indent:1em;">
我的理解：</p>
<p style="line-height:1.7em;text-indent:1em;">
一个有三行文本的文件进行MapReduce操作。</p>
<p style="line-height:1.7em;text-indent:1em;">
读取第一行Hello World Bye World ，分割单词形成Map。</p>
<p style="line-height:1.7em;text-indent:1em;">
&lt;Hello,1&gt; &lt;World,1&gt; &lt;Bye,1&gt; &lt;World,1&gt;</p>
<p style="line-height:1.7em;text-indent:1em;">
读取第二行Hello Hadoop Bye Hadoop ，分割单词形成Map。</p>
<p style="line-height:1.7em;text-indent:1em;">
&lt;Hello,1&gt; &lt;Hadoop,1&gt; &lt;Bye,1&gt; &lt;Hadoop,1&gt;</p>
<p style="line-height:1.7em;text-indent:1em;">
读取第三行Bye Hadoop Hello Hadoop，分割单词形成Map。</p>
<p style="line-height:1.7em;text-indent:1em;">
&lt;Bye,1&gt; &lt;Hadoop,1&gt; &lt;Hello,1&gt; &lt;Hadoop,1&gt;</p>
</div>
<p style="font-size:16px;line-height:1.7em;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
<img src="http://img1.tuicool.com/jiiama.jpg" class="alignCenter" alt="" style="vertical-align:middle;border:0px none;text-align:center;display:block;width:550px;"></p>
<div style="font-size:16px;line-height:1.7em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
<ul style="list-style-type:none;line-height:1.7em;"><li style="line-height:1.7em;list-style-type:disc;">Reduce操作是对map的结果进行排序，合并，最后得出词频。</li></ul><p style="line-height:1.7em;text-indent:1em;">
我的理解：</p>
<p style="line-height:1.7em;text-indent:1em;">
经过进一步处理(combiner),将形成的Map根据相同的key组合成value数组。</p>
<p style="line-height:1.7em;text-indent:1em;">
&lt;Bye,1,1,1&gt; &lt;Hadoop,1,1,1,1&gt; &lt;Hello,1,1,1&gt; &lt;World,1,1&gt;</p>
<p style="line-height:1.7em;text-indent:1em;">
循环执行Reduce(K,V[])，分别统计每个单词出现的次数。</p>
<p style="line-height:1.7em;text-indent:1em;">
&lt;Bye,3&gt; &lt;Hadoop,4&gt; &lt;Hello,3&gt; &lt;World,2&gt;</p>
</div>
<p style="font-size:16px;line-height:1.7em;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
<img src="http://img2.tuicool.com/yyQNRn.jpg" class="alignCenter" alt="" style="vertical-align:middle;border:0px none;text-align:center;display:block;width:550px;"></p>
<h3 style="font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;line-height:1.6em;color:rgb(51,51,51);font-size:18px;text-indent:1em;">
2、WordCount源码</h3>
<pre class="prettyprint hljs swift" style="font-family:Menlo, Monaco, Consolas, 'Courier New', monospace;color:rgb(68,68,68);font-size:14px;line-height:1.5em;background-color:rgb(246,246,246);border:none;"><span style="color:rgb(0,0,255);">package</span><span style="color:rgb(0,0,0);"> org.apache.hadoop.examples;

</span><span style="color:rgb(0,0,255);"><span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">import</span></span><span style="color:rgb(0,0,0);"> java.io.IOException;
</span><span style="color:rgb(0,0,255);"><span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">import</span></span><span style="color:rgb(0,0,0);"> java.util.StringTokenizer;

</span><span style="color:rgb(0,0,255);"><span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">import</span></span><span style="color:rgb(0,0,0);"> org.apache.hadoop.conf.Configuration;
</span><span style="color:rgb(0,0,255);"><span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">import</span></span><span style="color:rgb(0,0,0);"> org.apache.hadoop.fs.Path;
</span><span style="color:rgb(0,0,255);"><span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">import</span></span><span style="color:rgb(0,0,0);"> org.apache.hadoop.io.IntWritable;
</span><span style="color:rgb(0,0,255);"><span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">import</span></span><span style="color:rgb(0,0,0);"> org.apache.hadoop.io.Text;
</span><span style="color:rgb(0,0,255);"><span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">import</span></span><span style="color:rgb(0,0,0);"> org.apache.hadoop.mapreduce.Job;
</span><span style="color:rgb(0,0,255);"><span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">import</span></span><span style="color:rgb(0,0,0);"> org.apache.hadoop.mapreduce.Mapper;
</span><span style="color:rgb(0,0,255);"><span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">import</span></span><span style="color:rgb(0,0,0);"> org.apache.hadoop.mapreduce.Reducer;
</span><span style="color:rgb(0,0,255);"><span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">import</span></span><span style="color:rgb(0,0,0);"> org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
</span><span style="color:rgb(0,0,255);"><span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">import</span></span><span style="color:rgb(0,0,0);"> org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
</span><span style="color:rgb(0,0,255);"><span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">import</span></span><span style="color:rgb(0,0,0);"> org.apache.hadoop.util.GenericOptionsParser;
</span><span style="color:rgb(0,128,0);"><span class="hljs-comment" style="color:rgb(136,136,136);">/**</span></span><span style="color:rgb(0,128,0);"><span class="hljs-comment" style="color:rgb(136,136,136);">
 * 
 * 描述：WordCount explains by York
  * </span></span><span style="color:rgb(128,128,128);"><span class="hljs-comment" style="color:rgb(136,136,136);">@author</span></span><span style="color:rgb(0,128,0);"><span class="hljs-comment" style="color:rgb(136,136,136);"> Hadoop Dev Group
 </span></span><span style="color:rgb(0,128,0);"><span class="hljs-comment" style="color:rgb(136,136,136);">*/</span></span><span style="color:rgb(0,0,0);">
publicclass <span class="hljs-type" style="color:rgb(136,0,0);">WordCount</span> {
    </span><span style="color:rgb(0,128,0);"><span class="hljs-comment" style="color:rgb(136,136,136);">/**</span></span><span style="color:rgb(0,128,0);"><span class="hljs-comment" style="color:rgb(136,136,136);">
     * 建立Mapper类TokenizerMapper继承自泛型类Mapper
     * Mapper类:实现了Map功能基类
     * Mapper接口：
     * WritableComparable接口：实现WritableComparable的类可以相互比较。所有被用作key的类应该实现此接口。
     * Reporter 则可用于报告整个应用的运行进度，本例中未使用。 
     * 
     </span></span><span style="color:rgb(0,128,0);"><span class="hljs-comment" style="color:rgb(136,136,136);">*/</span></span><span style="color:rgb(0,0,0);">
  publicstaticclass <span class="hljs-type" style="color:rgb(136,0,0);">TokenizerMapper</span> 
       </span><span style="color:rgb(0,0,255);">extends</span> <span class="hljs-type" style="color:rgb(136,0,0);">Mapper</span>&lt;<span class="hljs-type" style="color:rgb(136,0,0);">Object</span>, <span class="hljs-type" style="color:rgb(136,0,0);">Text</span>, <span class="hljs-type" style="color:rgb(136,0,0);">Text</span>, <span class="hljs-type" style="color:rgb(136,0,0);">IntWritable</span>&gt;<span style="color:rgb(0,0,0);">{
        </span><span style="color:rgb(0,128,0);"><span class="hljs-comment" style="color:rgb(136,136,136);">/**</span></span><span style="color:rgb(0,128,0);"><span class="hljs-comment" style="color:rgb(136,136,136);">
         * IntWritable, Text 均是 Hadoop 中实现的用于封装 Java 数据类型的类，这些类实现了WritableComparable接口，
         * 都能够被串行化从而便于在分布式环境中进行数据交换，你可以将它们分别视为int,String 的替代品。
     * 声明one常量和word用于存放单词的变量
         </span></span><span style="color:rgb(0,128,0);"><span class="hljs-comment" style="color:rgb(136,136,136);">*/</span></span><span style="color:rgb(0,0,0);">
    privatefinalstatic <span class="hljs-type" style="color:rgb(136,0,0);">IntWritable</span> one </span>=<span style="color:rgb(0,0,255);">new</span> <span class="hljs-type" style="color:rgb(136,0,0);">IntWritable</span>(<span class="hljs-number" style="color:rgb(136,0,0);">1</span><span style="color:rgb(0,0,0);">);
    </span><span style="color:rgb(0,0,255);"><span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">private</span></span> <span class="hljs-type" style="color:rgb(136,0,0);">Text</span> word =<span style="color:rgb(0,0,255);">new</span><span style="color:rgb(0,0,0);"> <span class="hljs-type" style="color:rgb(136,0,0);">Text</span>();
    </span><span style="color:rgb(0,128,0);"><span class="hljs-comment" style="color:rgb(136,136,136);">/**</span></span><span style="color:rgb(0,128,0);"><span class="hljs-comment" style="color:rgb(136,136,136);">
         * Mapper中的map方法：
         * void map(K1 key, V1 value, Context context)
         * 映射一个单个的输入k/v对到一个中间的k/v对
         * 输出对不需要和输入对是相同的类型，输入对可以映射到0个或多个输出对。
         * Context：收集Mapper输出的&lt;k,v&gt;对。
         * Context的write(k, v)方法:增加一个(k,v)对到context
         * 程序员主要编写Map和Reduce函数.这个Map函数使用StringTokenizer函数对字符串进行分隔,通过write方法把单词存入word中
     * write方法存入(单词,1)这样的二元组到context中
     </span></span><span style="color:rgb(0,128,0);"><span class="hljs-comment" style="color:rgb(136,136,136);">*/</span></span><span style="color:rgb(0,0,0);">  
    publicvoid <span class="hljs-built_in" style="color:rgb(57,115,0);">map</span>(<span class="hljs-type" style="color:rgb(136,0,0);">Object</span> key, <span class="hljs-type" style="color:rgb(136,0,0);">Text</span> value, <span class="hljs-type" style="color:rgb(136,0,0);">Context</span> context
                    ) </span><span style="color:rgb(0,0,255);"><span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">throws</span></span><span style="color:rgb(0,0,0);"> <span class="hljs-type" style="color:rgb(136,0,0);">IOException</span>, <span class="hljs-type" style="color:rgb(136,0,0);">InterruptedException</span> {
      <span class="hljs-type" style="color:rgb(136,0,0);">StringTokenizer</span> itr </span>=<span style="color:rgb(0,0,255);">new</span><span style="color:rgb(0,0,0);"> <span class="hljs-type" style="color:rgb(136,0,0);">StringTokenizer</span>(value.<span class="hljs-built_in" style="color:rgb(57,115,0);">toString</span>());
      </span><span style="color:rgb(0,0,255);"><span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">while</span></span><span style="color:rgb(0,0,0);"> (itr.hasMoreTokens()) {
        word.<span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">set</span>(itr.nextToken());
        context.write(word, one);
      }
    }
  }
  
  publicstaticclass <span class="hljs-type" style="color:rgb(136,0,0);">IntSumReducer</span> 
       </span><span style="color:rgb(0,0,255);">extends</span> <span class="hljs-type" style="color:rgb(136,0,0);">Reducer</span>&lt;<span class="hljs-type" style="color:rgb(136,0,0);">Text</span>,<span class="hljs-type" style="color:rgb(136,0,0);">IntWritable</span>,<span class="hljs-type" style="color:rgb(136,0,0);">Text</span>,<span class="hljs-type" style="color:rgb(136,0,0);">IntWritable</span>&gt;<span style="color:rgb(0,0,0);"> {
    </span><span style="color:rgb(0,0,255);"><span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">private</span></span> <span class="hljs-type" style="color:rgb(136,0,0);">IntWritable</span> result =<span style="color:rgb(0,0,255);">new</span><span style="color:rgb(0,0,0);"> <span class="hljs-type" style="color:rgb(136,0,0);">IntWritable</span>();
    </span><span style="color:rgb(0,128,0);"><span class="hljs-comment" style="color:rgb(136,136,136);">/**</span></span><span style="color:rgb(0,128,0);"><span class="hljs-comment" style="color:rgb(136,136,136);">
         * Reducer类中的reduce方法：
      * void reduce(Text key, Iterable&lt;IntWritable&gt; values, Context context)
         * 中k/v来自于map函数中的context,可能经过了进一步处理(combiner),同样通过context输出           
         </span></span><span style="color:rgb(0,128,0);"><span class="hljs-comment" style="color:rgb(136,136,136);">*/</span></span><span style="color:rgb(0,0,0);">
    publicvoid <span class="hljs-built_in" style="color:rgb(57,115,0);">reduce</span>(<span class="hljs-type" style="color:rgb(136,0,0);">Text</span> key, <span class="hljs-type" style="color:rgb(136,0,0);">Iterable</span></span>&lt;<span class="hljs-type" style="color:rgb(136,0,0);">IntWritable</span>&gt;<span style="color:rgb(0,0,0);"> values, 
                       <span class="hljs-type" style="color:rgb(136,0,0);">Context</span> context
                       ) </span><span style="color:rgb(0,0,255);"><span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">throws</span></span><span style="color:rgb(0,0,0);"> <span class="hljs-type" style="color:rgb(136,0,0);">IOException</span>, <span class="hljs-type" style="color:rgb(136,0,0);">InterruptedException</span> {
      </span><span style="color:rgb(0,0,255);">int</span> sum =<span class="hljs-number" style="color:rgb(136,0,0);">0</span><span style="color:rgb(0,0,0);">;
      </span><span style="color:rgb(0,0,255);"><span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">for</span></span><span style="color:rgb(0,0,0);"> (<span class="hljs-type" style="color:rgb(136,0,0);">IntWritable</span> val : values) {
        sum </span>+=<span style="color:rgb(0,0,0);"> val.<span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">get</span>();
      }
      result.<span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">set</span>(sum);
      context.write(key, result);
    }
  }

  publicstaticvoid main(<span class="hljs-type" style="color:rgb(136,0,0);">String</span>[] args) </span><span style="color:rgb(0,0,255);"><span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">throws</span></span><span style="color:rgb(0,0,0);"> <span class="hljs-type" style="color:rgb(136,0,0);">Exception</span> {
        </span><span style="color:rgb(0,128,0);"><span class="hljs-comment" style="color:rgb(136,136,136);">/**</span></span><span style="color:rgb(0,128,0);"><span class="hljs-comment" style="color:rgb(136,136,136);">
         * Configuration：map/reduce的j配置类，向hadoop框架描述map-reduce执行的工作
         </span></span><span style="color:rgb(0,128,0);"><span class="hljs-comment" style="color:rgb(136,136,136);">*/</span></span><span style="color:rgb(0,0,0);">
    <span class="hljs-type" style="color:rgb(136,0,0);">Configuration</span> conf </span>=<span style="color:rgb(0,0,255);">new</span><span style="color:rgb(0,0,0);"> <span class="hljs-type" style="color:rgb(136,0,0);">Configuration</span>();
    <span class="hljs-type" style="color:rgb(136,0,0);">String</span>[] otherArgs </span>=<span style="color:rgb(0,0,255);">new</span><span style="color:rgb(0,0,0);"> <span class="hljs-type" style="color:rgb(136,0,0);">GenericOptionsParser</span>(conf, args).getRemainingArgs();
    </span><span style="color:rgb(0,0,255);"><span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">if</span></span> (otherArgs.length !=<span class="hljs-number" style="color:rgb(136,0,0);">2</span><span style="color:rgb(0,0,0);">) {
      <span class="hljs-type" style="color:rgb(136,0,0);">System</span>.err.<span class="hljs-built_in" style="color:rgb(57,115,0);">println</span>(</span><span class="hljs-string" style="color:rgb(136,0,0);">"Usage: wordcount &lt;in&gt; &lt;out&gt;"</span><span style="color:rgb(0,0,0);">);
      <span class="hljs-type" style="color:rgb(136,0,0);">System</span>.exit(</span><span class="hljs-number" style="color:rgb(136,0,0);">2</span><span style="color:rgb(0,0,0);">);
    }
    <span class="hljs-type" style="color:rgb(136,0,0);">Job</span> job </span>=<span style="color:rgb(0,0,255);">new</span> <span class="hljs-type" style="color:rgb(136,0,0);">Job</span>(conf, <span class="hljs-string" style="color:rgb(136,0,0);">"word count"</span>);    <span style="color:rgb(0,128,0);"><span class="hljs-comment" style="color:rgb(136,136,136);">//</span></span><span style="color:rgb(0,128,0);"><span class="hljs-comment" style="color:rgb(136,136,136);">设置一个用户定义的job名称</span></span>
    job.setJarByClass(<span class="hljs-type" style="color:rgb(136,0,0);">WordCount</span>.<span style="color:rgb(0,0,255);"><span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">class</span></span><span style="color:rgb(0,0,0);">);
    job.setMapperClass(<span class="hljs-type" style="color:rgb(136,0,0);">TokenizerMapper</span>.</span><span style="color:rgb(0,0,255);"><span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">class</span></span>);    <span style="color:rgb(0,128,0);"><span class="hljs-comment" style="color:rgb(136,136,136);">//</span></span><span style="color:rgb(0,128,0);"><span class="hljs-comment" style="color:rgb(136,136,136);">为job设置Mapper类</span></span>
    job.setCombinerClass(<span class="hljs-type" style="color:rgb(136,0,0);">IntSumReducer</span>.<span style="color:rgb(0,0,255);"><span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">class</span></span>);    <span style="color:rgb(0,128,0);"><span class="hljs-comment" style="color:rgb(136,136,136);">//</span></span><span style="color:rgb(0,128,0);"><span class="hljs-comment" style="color:rgb(136,136,136);">为job设置Combiner类</span></span>
    job.setReducerClass(<span class="hljs-type" style="color:rgb(136,0,0);">IntSumReducer</span>.<span style="color:rgb(0,0,255);"><span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">class</span></span>);    <span style="color:rgb(0,128,0);"><span class="hljs-comment" style="color:rgb(136,136,136);">//</span></span><span style="color:rgb(0,128,0);"><span class="hljs-comment" style="color:rgb(136,136,136);">为job设置Reducer类</span></span>
    job.setOutputKeyClass(<span class="hljs-type" style="color:rgb(136,0,0);">Text</span>.<span style="color:rgb(0,0,255);"><span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">class</span></span>);        <span style="color:rgb(0,128,0);"><span class="hljs-comment" style="color:rgb(136,136,136);">//</span></span><span style="color:rgb(0,128,0);"><span class="hljs-comment" style="color:rgb(136,136,136);">为job的输出数据设置Key类</span></span>
    job.setOutputValueClass(<span class="hljs-type" style="color:rgb(136,0,0);">IntWritable</span>.<span style="color:rgb(0,0,255);"><span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">class</span></span>);    <span style="color:rgb(0,128,0);"><span class="hljs-comment" style="color:rgb(136,136,136);">//</span></span><span style="color:rgb(0,128,0);"><span class="hljs-comment" style="color:rgb(136,136,136);">为job输出设置value类</span></span>
    <span class="hljs-type" style="color:rgb(136,0,0);">FileInputFormat</span>.addInputPath(job, <span style="color:rgb(0,0,255);">new</span> <span class="hljs-type" style="color:rgb(136,0,0);">Path</span>(otherArgs[<span class="hljs-number" style="color:rgb(136,0,0);">0</span>]));    <span style="color:rgb(0,128,0);"><span class="hljs-comment" style="color:rgb(136,136,136);">//</span></span><span style="color:rgb(0,128,0);"><span class="hljs-comment" style="color:rgb(136,136,136);">为job设置输入路径</span></span>
    <span class="hljs-type" style="color:rgb(136,0,0);">FileOutputFormat</span>.setOutputPath(job, <span style="color:rgb(0,0,255);">new</span> <span class="hljs-type" style="color:rgb(136,0,0);">Path</span>(otherArgs[<span class="hljs-number" style="color:rgb(136,0,0);">1</span>]));<span style="color:rgb(0,128,0);"><span class="hljs-comment" style="color:rgb(136,136,136);">//</span></span><span style="color:rgb(0,128,0);"><span class="hljs-comment" style="color:rgb(136,136,136);">为job设置输出路径</span></span>
    <span class="hljs-type" style="color:rgb(136,0,0);">System</span>.exit(job.waitForCompletion(<span style="color:rgb(0,0,255);"><span class="hljs-literal" style="color:rgb(120,169,96);">true</span></span>) ?<span class="hljs-number" style="color:rgb(136,0,0);">0</span> : <span class="hljs-number" style="color:rgb(136,0,0);">1</span>);        <span style="color:rgb(0,128,0);"><span class="hljs-comment" style="color:rgb(136,136,136);">//</span></span><span style="color:rgb(0,128,0);"><span class="hljs-comment" style="color:rgb(136,136,136);">运行job</span></span>
<span style="color:rgb(0,0,0);">  }
}</span></pre>
<h3 style="font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;line-height:1.6em;color:rgb(51,51,51);font-size:18px;text-indent:1em;">
3、WordCount逐行解析</h3>
<ul style="list-style-type:none;font-size:16px;line-height:1.7em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;"><li style="line-height:1.7em;list-style-type:disc;">对于map函数的方法。</li></ul><div style="font-size:16px;line-height:1.7em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
<pre><code class="language-java"><span class="hljs-function"><span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">public</span> <span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">void</span> <span class="hljs-title" style="color:rgb(136,0,0);font-weight:bold;">map</span><span class="hljs-params">(Object key, Text value, Context context)</span> <span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">throws</span> IOException, InterruptedException </span>{…}</code></pre>
<p style="line-height:1.7em;text-indent:1em;">
这里有三个参数，前面两个Object key, Text value就是输入的key和value，第三个参数Context context这是可以记录输入的key和value，例如：context.write(word, one);此外context还会记录map运算的状态。</p>
<ul style="list-style-type:none;line-height:1.7em;"><li style="line-height:1.7em;list-style-type:disc;">对于reduce函数的方法。</li></ul><pre><code class="language-java"><span class="hljs-function"><span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">public</span> <span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">void</span> <span class="hljs-title" style="color:rgb(136,0,0);font-weight:bold;">reduce</span><span class="hljs-params">(Text key, Iterable&lt;IntWritable&gt; values, Context context)</span> <span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">throws</span> IOException, InterruptedException </span>{…}</code></pre>
<p style="line-height:1.7em;text-indent:1em;">
reduce函数的输入也是一个key/value的形式，不过它的value是一个迭代器的形式Iterable&lt;IntWritable&gt; values，也就是说reduce的输入是一个key对应一组的值的value，reduce也有context和map的context作用一致。</p>
<p style="line-height:1.7em;text-indent:1em;">
至于计算的逻辑则需要程序员编码实现。</p>
<ul style="list-style-type:none;line-height:1.7em;"><li style="line-height:1.7em;list-style-type:disc;">对于main函数的调用。</li></ul><p style="line-height:1.7em;text-indent:1em;">
首先是：</p>
<pre class="hljs nginx" style="font-family:Menlo, Monaco, Consolas, 'Courier New', monospace;color:rgb(68,68,68);font-size:14px;line-height:1.5em;background-color:rgb(246,246,246);border:none;"><span class="hljs-attribute" style="color:rgb(51,51,51);font-weight:700;">Configuration</span> conf = new Configuration();</pre>
<p style="line-height:1.7em;text-indent:1em;">
运行MapReduce程序前都要初始化Configuration，该类主要是读取MapReduce系统配置信息，这些信息包括hdfs还有MapReduce，也就是安装hadoop时候的配置文件例如：core-site.xml、hdfs-site.xml和mapred-site.xml等等文件里的信息，有些童鞋不理解为啥要这么做，这个是没有深入思考MapReduce计算框架造成，我们程序员开发MapReduce时候只是在填空，在map函数和reduce函数里编写实际进行的业务逻辑，其它的工作都是交给MapReduce框架自己操作的，但是至少我们要告诉它怎么操作啊，比如hdfs在哪里，MapReduce的jobstracker在哪里，而这些信息就在conf包下的配置文件里。</p>
<p style="line-height:1.7em;text-indent:1em;">
接下来的代码是：</p>
<pre class="prettyprint hljs awk" style="font-family:Menlo, Monaco, Consolas, 'Courier New', monospace;color:rgb(68,68,68);font-size:14px;line-height:1.5em;background-color:rgb(246,246,246);border:none;">String[] otherArgs = new GenericOptionsParser(conf, args).getRemainingArgs();
    <span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">if</span> (otherArgs.length != <span class="hljs-number" style="color:rgb(136,0,0);">2</span>) {
      System.err.println(<span class="hljs-string" style="color:rgb(136,0,0);">"Usage: wordcount &lt;in&gt; &lt;out&gt;"</span>);
      System.<span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">exit</span>(<span class="hljs-number" style="color:rgb(136,0,0);">2</span>);
    }</pre>
<p style="line-height:1.7em;text-indent:1em;">
If的语句好理解，就是运行WordCount程序时候一定是两个参数，如果不是就会报错退出。至于第一句里的GenericOptionsParser类，它是用来解释常用hadoop命令，并根据需要为Configuration对象设置相应的值，其实平时开发里我们不太常用它，而是让类实现Tool接口，然后再main函数里使用ToolRunner运行程序，而ToolRunner内部会调用GenericOptionsParser。</p>
<p style="line-height:1.7em;text-indent:1em;">
接下来的代码是：</p>
<pre class="prettyprint hljs fsharp" style="font-family:Menlo, Monaco, Consolas, 'Courier New', monospace;color:rgb(68,68,68);font-size:14px;line-height:1.5em;background-color:rgb(246,246,246);border:none;">Job job = <span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">new</span> Job(conf, <span class="hljs-string" style="color:rgb(136,0,0);">"word count"</span>);
    job.setJarByClass(WordCount.<span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">class</span>);
    job.setMapperClass(TokenizerMapper.<span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">class</span>);
    job.setCombinerClass(IntSumReducer.<span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">class</span>);
    job.setReducerClass(IntSumReducer.<span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">class</span>);</pre>
<p style="line-height:1.7em;text-indent:1em;">
第一行就是在构建一个job，在mapreduce框架里一个mapreduce任务也叫mapreduce作业也叫做一个mapreduce的job，而具体的map和reduce运算就是task了，这里我们构建一个job，构建时候有两个参数，一个是conf这个就不累述了，一个是这个job的名称。</p>
<p style="line-height:1.7em;text-indent:1em;">
第二行就是装载程序员编写好的计算程序，例如我们的程序类名就是WordCount了。这里我要做下纠正，虽然我们编写mapreduce程序只需要实现map函数和reduce函数，但是实际开发我们要实现三个类，第三个类是为了配置mapreduce如何运行map和reduce函数，准确的说就是构建一个mapreduce能执行的job了，例如WordCount类。</p>
<p style="line-height:1.7em;text-indent:1em;">
第三行和第五行就是装载map函数和reduce函数实现类了，这里多了个第四行，这个是装载Combiner类，这个类和mapreduce运行机制有关，其实本例去掉第四行也没有关系，但是使用了第四行理论上运行效率会更好。</p>
<p style="line-height:1.7em;text-indent:1em;">
接下来的代码：</p>
<pre><code class="language-css"><span class="hljs-selector-tag" style="color:rgb(51,51,51);font-weight:700;">job</span><span class="hljs-selector-class" style="color:rgb(136,0,0);">.setOutputKeyClass</span>(<span class="hljs-selector-tag" style="color:rgb(51,51,51);font-weight:700;">Text</span><span class="hljs-selector-class" style="color:rgb(136,0,0);">.class</span>);
    <span class="hljs-selector-tag" style="color:rgb(51,51,51);font-weight:700;">job</span><span class="hljs-selector-class" style="color:rgb(136,0,0);">.setOutputValueClass</span>(<span class="hljs-selector-tag" style="color:rgb(51,51,51);font-weight:700;">IntWritable</span><span class="hljs-selector-class" style="color:rgb(136,0,0);">.class</span>);</code></pre>
<p style="line-height:1.7em;text-indent:1em;">
这个是定义输出的key/value的类型，也就是最终存储在hdfs上结果文件的key/value的类型。</p>
<p style="line-height:1.7em;text-indent:1em;">
最后的代码是：</p>
<pre class="prettyprint hljs awk" style="font-family:Menlo, Monaco, Consolas, 'Courier New', monospace;color:rgb(68,68,68);font-size:14px;line-height:1.5em;background-color:rgb(246,246,246);border:none;">FileInputFormat.addInputPath(job, new Path(otherArgs[<span class="hljs-number" style="color:rgb(136,0,0);">0</span>]));
    FileOutputFormat.setOutputPath(job, new Path(otherArgs[<span class="hljs-number" style="color:rgb(136,0,0);">1</span>]));
    System.<span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">exit</span>(job.waitForCompletion(true) ? <span class="hljs-number" style="color:rgb(136,0,0);">0</span> : <span class="hljs-number" style="color:rgb(136,0,0);">1</span>);</pre>
<p style="line-height:1.7em;text-indent:1em;">
第一行就是构建输入的数据文件，第二行是构建输出的数据文件，最后一行如果job运行成功了，我们的程序就会正常退出。</p>
</div>
            </div>
                </div>