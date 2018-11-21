---
layout:     post
title:      Hadoop学习笔记（一）之示例程序：计算每年的最高温度MaxTemperature
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p style="font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:13px;line-height:19px;background-color:rgb(245,245,245);">
　本《hadoop学习笔记》系列是在《hadoop: the definitive guide 3th》的基础上通过网上额外搜集资料和查看hadoop的API再加上自己的<span>实践方面</span>的理解编写而成的，主要针对hadoop的特性和功能学习以及Hadoop生态圈中的其他工具（如Pig，Hive，Hbase，Avro等等）。另外设计到hadoop编程方面的请查阅另一个笔记系列：《<span style="color:rgb(255,0,0);"><a href="http://www.cnblogs.com/beanmoon/archive/2012/12/06/2804594.html" rel="nofollow" style="color:rgb(0,105,214);font-weight:inherit;line-height:inherit;"><span style="color:rgb(255,0,0);">Hadoop编程笔记</span></a></span>》。如果有同学同时也在研究这本书，欢迎沟通交流，在下能力有限，还望各路大神看到有不对的地方加以指正~~（本系列学习笔记还正在整理中，以后会陆续发布）。<br><br></p>
<p style="font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:13px;line-height:19px;background-color:rgb(245,245,245);">
　　本书第二章以一个很浅显的例子为大家提供了hadoop入门，那就是从气象站中保存的历史气象信息里面提取出每年的最高气温来。其大致过程如下（注意在不同阶段的数据形式）：</p>
<p style="font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:13px;line-height:19px;background-color:rgb(245,245,245);">
　　<img src="http://pic002.cnblogs.com/images/2012/402771/2012120523090681.png" alt="" style="border:0px;"></p>
<p style="font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:13px;line-height:19px;background-color:rgb(245,245,245);">
　　注意：shuffle阶段是把map的输出传输到reduce任务的阶段，其中包括了键值对的排序和分组。</p>
<h3 style="font-size:16px;font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;background-color:rgb(245,245,245);">
　　1.Map阶段</h3>
<p style="font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:13px;line-height:19px;background-color:rgb(245,245,245);">
　　Map任务是十分简单的，我们只需要从输入文件中提取出年份和对应的温度值即可，同时过滤掉坏的记录。这里我们选择Text输入格式（默认的），其中数据集的每一行作为map任务输入中的key-value pair中的value值，key值是对应行在输入文件中的位移（以字节为单位），但我们并不需要key的值，所以忽略之。下面我们来看一下map任务的java表示：</p>
<div class="cnblogs_code" style="background-color:rgb(245,245,245);border:1px solid rgb(204,204,204);overflow:auto;font-family:'Courier New' !important;">
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy" style="line-height:1.5 !important;"><a title="复制代码" style="color:rgb(0,105,214);font-weight:inherit;line-height:inherit;text-decoration:underline;border:none !important;"><img src="http://common.cnblogs.com/images/copycode.gif" alt="复制代码" style="border:none !important;"></a></span></div>
<pre style="font-family:'Courier New' !important;"><span style="line-height:1.5 !important;"> 1</span> <span style="line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> java.io.IOException;
</span><span style="line-height:1.5 !important;"> 2</span> <span style="line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.hadoop.io.IntWritable;
</span><span style="line-height:1.5 !important;"> 3</span> <span style="line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.hadoop.io.LongWritable;
</span><span style="line-height:1.5 !important;"> 4</span> <span style="line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.hadoop.io.Text;
</span><span style="line-height:1.5 !important;"> 5</span> <span style="line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.hadoop.mapreduce.Mapper;
</span><span style="line-height:1.5 !important;"> 6</span> 
<span style="line-height:1.5 !important;"> 7</span> <span style="line-height:1.5 !important;">public</span> <span style="line-height:1.5 !important;">class</span><span style="line-height:1.5 !important;"> MaxTemperatureMapper
</span><span style="line-height:1.5 !important;"> 8</span>   <span style="line-height:1.5 !important;">extends</span> Mapper&lt;LongWritable, Text, Text, IntWritable&gt;<span style="line-height:1.5 !important;"> {  //注1
</span><span style="line-height:1.5 !important;"> 9</span>   <span style="line-height:1.5 !important;">private</span> <span style="line-height:1.5 !important;">static</span> <span style="line-height:1.5 !important;">final</span> <span style="line-height:1.5 !important;">int</span> MISSING = 9999<span style="line-height:1.5 !important;">;
</span><span style="line-height:1.5 !important;">10</span> <span style="line-height:1.5 !important;">  @Override
</span><span style="line-height:1.5 !important;">11</span>   <span style="line-height:1.5 !important;">public</span> <span style="line-height:1.5 !important;">void</span><span style="line-height:1.5 !important;"> map(LongWritable key, Text value, Context context)
</span><span style="line-height:1.5 !important;">12</span>       <span style="line-height:1.5 !important;">throws</span><span style="line-height:1.5 !important;"> IOException, InterruptedException {
</span><span style="line-height:1.5 !important;">13</span>     String line =<span style="line-height:1.5 !important;"> value.toString();
</span><span style="line-height:1.5 !important;">14</span>     String year = line.substring(15, 19<span style="line-height:1.5 !important;">);
</span><span style="line-height:1.5 !important;">15</span>     <span style="line-height:1.5 !important;">int</span><span style="line-height:1.5 !important;"> airTemperature;
</span><span style="line-height:1.5 !important;">16</span>     <span style="line-height:1.5 !important;">if</span> (line.charAt(87) == '+') { <span style="line-height:1.5 !important;">//</span><span style="line-height:1.5 !important;"> parseInt doesn't like leading plus signs</span>
<span style="line-height:1.5 !important;">17</span>       airTemperature = Integer.parseInt(line.substring(88, 92<span style="line-height:1.5 !important;">));
</span><span style="line-height:1.5 !important;">18</span>     } <span style="line-height:1.5 !important;">else</span><span style="line-height:1.5 !important;"> {
</span><span style="line-height:1.5 !important;">19</span>       airTemperature = Integer.parseInt(line.substring(87, 92<span style="line-height:1.5 !important;">));
</span><span style="line-height:1.5 !important;">20</span> <span style="line-height:1.5 !important;">    }
</span><span style="line-height:1.5 !important;">21</span>     String quality = line.substring(92, 93<span style="line-height:1.5 !important;">);
</span><span style="line-height:1.5 !important;">22</span>     <span style="line-height:1.5 !important;">if</span> (airTemperature != MISSING &amp;&amp; quality.matches("[01459]"<span style="line-height:1.5 !important;">)) {
</span><span style="line-height:1.5 !important;">23</span>       context.write(<span style="line-height:1.5 !important;">new</span> Text(year), <span style="line-height:1.5 !important;">new</span><span style="line-height:1.5 !important;"> IntWritable(airTemperature));
</span><span style="line-height:1.5 !important;">24</span> <span style="line-height:1.5 !important;">    }
</span><span style="line-height:1.5 !important;">25</span> <span style="line-height:1.5 !important;">  }
</span><span style="line-height:1.5 !important;">26</span> }</pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy" style="line-height:1.5 !important;"><a title="复制代码" style="color:rgb(0,105,214);font-weight:inherit;line-height:inherit;text-decoration:underline;border:none !important;"><img src="http://common.cnblogs.com/images/copycode.gif" alt="复制代码" style="border:none !important;"></a></span></div>
</div>
<p style="font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:13px;line-height:19px;background-color:rgb(245,245,245);">
 注1：Mapper类是一个泛型，其中的四个类型参数（LongWritable, Text, Text, IntWritable）指明了Mapper任务的输入（键，值）类型和输出（键，值）类型。其中LongWritable相当于java中的long类型，类似的，Text~String，IntWritable~int，只不过前者在网络传输时序列化操作方面做了优化。</p>
<p style="font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:13px;line-height:19px;background-color:rgb(245,245,245);">
<span>　　2. Reduce阶段</span></p>
<p style="font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:13px;line-height:19px;background-color:rgb(245,245,245);">
　　同样的，Reducer类的四个类型参数也指明了Reducer任务的输入（键，值）类型和输出（键，值）类型。其输入类型必须和Mapper任务的输出类型匹配（在这个例子中为（Text，IntWritable））。</p>
<div class="cnblogs_code" style="background-color:rgb(245,245,245);border:1px solid rgb(204,204,204);overflow:auto;font-family:'Courier New' !important;">
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy" style="line-height:1.5 !important;"><a title="复制代码" style="color:rgb(0,105,214);font-weight:inherit;line-height:inherit;text-decoration:underline;border:none !important;"><img src="http://common.cnblogs.com/images/copycode.gif" alt="复制代码" style="border:none !important;"></a></span></div>
<pre style="font-family:'Courier New' !important;"><span style="line-height:1.5 !important;"> 1</span> <span style="line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> java.io.IOException;
</span><span style="line-height:1.5 !important;"> 2</span> <span style="line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.hadoop.io.IntWritable;
</span><span style="line-height:1.5 !important;"> 3</span> <span style="line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.hadoop.io.Text;
</span><span style="line-height:1.5 !important;"> 4</span> <span style="line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.hadoop.mapreduce.Reducer;
</span><span style="line-height:1.5 !important;"> 5</span> 
<span style="line-height:1.5 !important;"> 6</span> <span style="line-height:1.5 !important;">public</span> <span style="line-height:1.5 !important;">class</span><span style="line-height:1.5 !important;"> MaxTemperatureReducer
</span><span style="line-height:1.5 !important;"> 7</span>   <span style="line-height:1.5 !important;">extends</span> Reducer&lt;Text, IntWritable, Text, IntWritable&gt;<span style="line-height:1.5 !important;"> {
</span><span style="line-height:1.5 !important;"> 8</span> <span style="line-height:1.5 !important;">  @Override
</span><span style="line-height:1.5 !important;"> 9</span>   <span style="line-height:1.5 !important;">public</span> <span style="line-height:1.5 !important;">void</span> reduce(Text key, Iterable&lt;IntWritable&gt;<span style="line-height:1.5 !important;"> values,
</span><span style="line-height:1.5 !important;">10</span> <span style="line-height:1.5 !important;">      Context context)
</span><span style="line-height:1.5 !important;">11</span>       <span style="line-height:1.5 !important;">throws</span><span style="line-height:1.5 !important;"> IOException, InterruptedException {
</span><span style="line-height:1.5 !important;">12</span>     <span style="line-height:1.5 !important;">int</span> maxValue =<span style="line-height:1.5 !important;"> Integer.MIN_VALUE;
</span><span style="line-height:1.5 !important;">13</span>     <span style="line-height:1.5 !important;">for</span><span style="line-height:1.5 !important;"> (IntWritable value : values) {
</span><span style="line-height:1.5 !important;">14</span>       maxValue =<span style="line-height:1.5 !important;"> Math.max(maxValue, value.get());
</span><span style="line-height:1.5 !important;">15</span> <span style="line-height:1.5 !important;">    }
</span><span style="line-height:1.5 !important;">16</span>     context.write(key, <span style="line-height:1.5 !important;">new</span><span style="line-height:1.5 !important;"> IntWritable(maxValue));
</span><span style="line-height:1.5 !important;">17</span> <span style="line-height:1.5 !important;">  }
</span><span style="line-height:1.5 !important;">18</span> }　　</pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy" style="line-height:1.5 !important;"><a title="复制代码" style="color:rgb(0,105,214);font-weight:inherit;line-height:inherit;text-decoration:underline;border:none !important;"><img src="http://common.cnblogs.com/images/copycode.gif" alt="复制代码" style="border:none !important;"></a></span></div>
</div>
<h3 style="font-size:16px;font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;background-color:rgb(245,245,245);">
　　3. 下面我们总体查看一下作业的运行代码：</h3>
<div class="cnblogs_code" style="background-color:rgb(245,245,245);border:1px solid rgb(204,204,204);overflow:auto;font-family:'Courier New' !important;">
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy" style="line-height:1.5 !important;"><a title="复制代码" style="color:rgb(0,105,214);font-weight:inherit;line-height:inherit;text-decoration:underline;border:none !important;"><img src="http://common.cnblogs.com/images/copycode.gif" alt="复制代码" style="border:none !important;"></a></span></div>
<pre style="font-family:'Courier New' !important;"><span style="line-height:1.5 !important;"> 1</span> <span style="line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.hadoop.fs.Path;
</span><span style="line-height:1.5 !important;"> 2</span> <span style="line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.hadoop.io.IntWritable;
</span><span style="line-height:1.5 !important;"> 3</span> <span style="line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.hadoop.io.Text;
</span><span style="line-height:1.5 !important;"> 4</span> <span style="line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.hadoop.mapreduce.Job;
</span><span style="line-height:1.5 !important;"> 5</span> <span style="line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
</span><span style="line-height:1.5 !important;"> 6</span> <span style="line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
</span><span style="line-height:1.5 !important;"> 7</span> 
<span style="line-height:1.5 !important;"> 8</span> <span style="line-height:1.5 !important;">public</span> <span style="line-height:1.5 !important;">class</span><span style="line-height:1.5 !important;"> MaxTemperature {
</span><span style="line-height:1.5 !important;"> 9</span>   <span style="line-height:1.5 !important;">public</span> <span style="line-height:1.5 !important;">static</span> <span style="line-height:1.5 !important;">void</span> main(String[] args) <span style="line-height:1.5 !important;">throws</span><span style="line-height:1.5 !important;"> Exception {
</span><span style="line-height:1.5 !important;">10</span>     <span style="line-height:1.5 !important;">if</span> (args.length != 2<span style="line-height:1.5 !important;">) {
</span><span style="line-height:1.5 !important;">11</span>       System.err.println("Usage: MaxTemperature &lt;input path&gt; &lt;output path&gt;"<span style="line-height:1.5 !important;">);
</span><span style="line-height:1.5 !important;">12</span>       System.exit(-1<span style="line-height:1.5 !important;">);
</span><span style="line-height:1.5 !important;">13</span> <span style="line-height:1.5 !important;">    }
</span><span style="line-height:1.5 !important;">14</span>     Job job = <span style="line-height:1.5 !important;">new</span><span style="line-height:1.5 !important;"> Job();
</span><span style="line-height:1.5 !important;">15</span>     job.setJarByClass(MaxTemperature.<span style="line-height:1.5 !important;">class</span><span style="line-height:1.5 !important;">);
</span><span style="line-height:1.5 !important;">16</span>     job.setJobName("Max temperature"<span style="line-height:1.5 !important;">);
</span><span style="line-height:1.5 !important;">17</span> 
<span style="line-height:1.5 !important;">18</span>     FileInputFormat.addInputPath(job, <span style="line-height:1.5 !important;">new</span> Path(args[0<span style="line-height:1.5 !important;">]));
</span><span style="line-height:1.5 !important;">19</span>     FileOutputFormat.setOutputPath(job, <span style="line-height:1.5 !important;">new</span> Path(args[1<span style="line-height:1.5 !important;">]));
</span><span style="line-height:1.5 !important;">20</span> 
<span style="line-height:1.5 !important;">21</span>     job.setMapperClass(MaxTemperatureMapper.<span style="line-height:1.5 !important;">class</span><span style="line-height:1.5 !important;">);
</span><span style="line-height:1.5 !important;">22</span>     job.setReducerClass(MaxTemperatureReducer.<span style="line-height:1.5 !important;">class</span><span style="line-height:1.5 !important;">);
</span><span style="line-height:1.5 !important;">23</span> 
<span style="line-height:1.5 !important;">24</span>     job.setOutputKeyClass(Text.<span style="line-height:1.5 !important;">class</span><span style="line-height:1.5 !important;">);              //注1
</span><span style="line-height:1.5 !important;">25</span>     job.setOutputValueClass(IntWritable.<span style="line-height:1.5 !important;">class</span><span style="line-height:1.5 !important;">);
</span><span style="line-height:1.5 !important;">26</span> 
<span style="line-height:1.5 !important;">27</span>     System.exit(job.waitForCompletion(<span style="line-height:1.5 !important;">true</span>) ? 0 : 1<span style="line-height:1.5 !important;">);
</span><span style="line-height:1.5 !important;">28</span> <span style="line-height:1.5 !important;">  }
</span><span style="line-height:1.5 !important;">29</span> }</pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy" style="line-height:1.5 !important;"><a title="复制代码" style="color:rgb(0,105,214);font-weight:inherit;line-height:inherit;text-decoration:underline;border:none !important;"><img src="http://common.cnblogs.com/images/copycode.gif" alt="复制代码" style="border:none !important;"></a></span></div>
</div>
<p style="font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:13px;line-height:19px;background-color:rgb(245,245,245);">
 注1：setOutputKeyClass()和setOutputValueClass()控制map任务和reduce任务的输出（两者输出类型相同的情况下），如果他们不一样，那么map的输出就要通过setMapOutputKeyClass()和setMapOutputValueClass()来设定了（reduce任务的输出设定之手前两者的影响）。</p>
<p style="font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:13px;line-height:19px;background-color:rgb(245,245,245);">
 附1：map的输入格式由Job的静态方法 public void <span>setInputFormatClass</span>(<a title="class or interface in java.lang" href="http://java.sun.com/javase/6/docs/api/java/lang/Class.html?is-external=true" rel="nofollow" style="color:rgb(0,105,214);font-weight:inherit;line-height:inherit;">Class</a>&lt;?
 extends <a title="class in org.apache.hadoop.mapreduce" href="http://hadoop.apache.org/docs/r1.0.4/api/org/apache/hadoop/mapreduce/InputFormat.html" rel="nofollow" style="color:rgb(0,105,214);font-weight:inherit;line-height:inherit;">InputFormat</a>&gt; cls)来设定，默认为TextInputFormat（在本例中并未显式给出）。</p>
<p style="font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:13px;line-height:19px;background-color:rgb(245,245,245);">
 </p>
<p style="font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:13px;line-height:19px;background-color:rgb(245,245,245);">
 通过以下命令来运行我们的第一个hadoop程序：<br>
% export HADOOP_CLASSPATH=hadoop-examples.jar    //HADOOP_CLASSPATH的具体含义请参看<span style="color:rgb(255,0,0);"><a href="http://www.cnblogs.com/beanmoon/archive/2012/11/07/2759218.html" rel="nofollow" style="color:rgb(0,105,214);font-weight:inherit;line-height:inherit;"><span style="color:rgb(255,0,0);">这里</span></a></span><br>
% hadoop MaxTemperature input/ncdc/sample.txt output</p>
<p style="font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:13px;line-height:19px;background-color:rgb(245,245,245);">
 　　其中sample.txt是我们指定的本地输入文件，output是我们指定的输出文件所在<span>目录</span>，注意此目录在运行前不应该存在，否则程序会报错并停止运行，这样做的目的主要是为了防止被指定的目录如果是另一个已存在的包含大量珍贵数据的目录，那么此目录下的文件就会被覆盖掉从而造成数据损坏。<br>
　　运行成功后就会在output目录下产生part-r-00000文件和_SUCCESS文件，前者对应于reduce任务的输出，每个reducer对应于一个这样的输出文件，以零（00000）开始计数（通过特殊的设置，reducer也可以产生多个输出文件，这个我们在后面再介绍）；后者是一个内容为空的标志文件（marker），表示作业已经成功完成。</p>
<p style="font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:13px;line-height:19px;background-color:rgb(245,245,245);">
转载请注明出处：<span style="color:rgb(255,0,0);"><a id="Editor_Edit_hlEntryLink" title="view: Hadoop学习笔记（一）之示例程序：计算每年的最高温度MaxTemperature" href="http://www.cnblogs.com/beanmoon/archive/2012/12/07/2804183.html" rel="nofollow" style="color:rgb(0,105,214);font-weight:inherit;line-height:inherit;"><span style="color:rgb(255,0,0);">http://www.cnblogs.com/beanmoon/archive/2012/12/07/2804183.html</span></a></span></p>
            </div>
                </div>