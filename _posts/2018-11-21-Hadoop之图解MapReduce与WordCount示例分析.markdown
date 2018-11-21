---
layout:     post
title:      Hadoop之图解MapReduce与WordCount示例分析
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/shujuelin/article/details/79119214				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
Hadoop的框架最核心的设计就是：HDFS和MapReduce。HDFS为海量的数据提供了存储，MapReduce则为海量的数据提供了计算。
<div class="para">　　HDFS是Google File System（GFS）的开源实现，MapReduce是Google MapReduce的开源实现。</div>
<div class="para">　　HDFS和MapReduce实现是完全分离的，并不是没有HDFS就不能MapReduce运算。</div>
<div class="para">本文主要参考了以下三篇博客学习整理而成。</div>
<div class="para">　　1、<a id="cb_post_title_url" class="postTitle2" href="http://www.cnblogs.com/xwdreamer/archive/2011/01/04/2297049.html" rel="nofollow"><span class="postTitle2">Hadoop示例程序WordCount详解及实例</span></a></div>
<div class="para">　　2、<a class="postTitle2" href="http://www.cnblogs.com/sharpxiajun/p/3151395.html" rel="nofollow"><span class="postTitle2">hadoop 学习笔记：mapreduce框架详解</span></a></div>
<div class="para">　　3、<a class="postTitle2" href="http://www.cnblogs.com/york-hust/archive/2011/07/19/2110967.html" rel="nofollow"><span class="postTitle2">hadoop示例程序wordcount分析</span></a></div>
<div class="para"><a class="postTitle2" href="http://www.cnblogs.com/york-hust/archive/2011/07/19/2110967.html" rel="nofollow"><span class="postTitle2">       4，初学hadoop之图解MapReduce与WordCount示例分析</span></a><a class="postTitle2" href="http://www.cnblogs.com/york-hust/archive/2011/07/19/2110967.html" rel="nofollow"><span class="postTitle2"></span></a>
<h1 class="postTitle"><a class="postTitle2" href="http://www.cnblogs.com/york-hust/archive/2011/07/19/2110967.html" rel="nofollow"><span class="postTitle2"></span></a><a class="postTitle2" href="http://www.cnblogs.com/york-hust/archive/2011/07/19/2110967.html" rel="nofollow"><span class="postTitle2"></span></a><a class="postTitle2" href="http://www.cnblogs.com/york-hust/archive/2011/07/19/2110967.html" rel="nofollow"><span class="postTitle2"></span></a><span class="postTitle2"></span><span style="font-family:'宋体';">1、MapReduce整体流程</span></h1>
</div>
<div class="para">　　最简单的MapReduce应用程序至少包含 3 个部分：一个 Map 函数、一个 Reduce 函数和一个 main 函数。在运行一个mapreduce计算任务时候，任务过程被分为两个阶段：map阶段和reduce阶段，每个阶段都是用键值对（key/value）作为输入（input）和输出（output）。main 函数将作业控制和文件输入/输出结合起来。</div>
<div class="para">　　<img src="https://images0.cnblogs.com/blog2015/595707/201505/071105197671454.gif" alt=""></div>
<div class="para">
<ul><li>并行读取文本中的内容，然后进行MapReduce操作。</li></ul></div>
<div class="para">　　<img src="https://images0.cnblogs.com/blog2015/595707/201505/071112028606040.gif" alt=""></div>
<div class="para">
<ul><li>Map过程：并行读取文本，对读取的单词进行map操作，每个词都以&lt;key,value&gt;形式生成。</li></ul><p>我的理解：</p>
<p>　　一个有三行文本的文件进行MapReduce操作。</p>
<p>　　读取第一行Hello World Bye World ，分割单词形成Map。</p>
<p>　　&lt;Hello,1&gt; &lt;World,1&gt; &lt;Bye,1&gt; &lt;World,1&gt;</p>
<p>　　读取第二行Hello Hadoop Bye Hadoop ，分割单词形成Map。</p>
<p>　　&lt;Hello,1&gt; &lt;Hadoop,1&gt; &lt;Bye,1&gt; &lt;Hadoop,1&gt;</p>
<p>　　读取第三行Bye Hadoop Hello Hadoop，分割单词形成Map。</p>
<p>　　&lt;Bye,1&gt; &lt;Hadoop,1&gt; &lt;Hello,1&gt; &lt;Hadoop,1&gt;</p>
</div>
<div class="para">　　<img src="https://images0.cnblogs.com/blog2015/595707/201505/071112176881743.gif" alt=""></div>
<div class="para">
<ul><li>Reduce操作是对map的结果进行排序，合并，最后得出词频。</li></ul><p>我的理解：</p>
<p>　　经过进一步处理(combiner),将形成的Map根据相同的key组合成value数组。</p>
<p>　　&lt;Bye,1,1,1&gt; &lt;Hadoop,1,1,1,1&gt; &lt;Hello,1,1,1&gt; &lt;World,1,1&gt;</p>
<p>　　循环执行Reduce(K,V[])，分别统计每个单词出现的次数。</p>
<p>　　&lt;Bye,3&gt; &lt;Hadoop,4&gt; &lt;Hello,3&gt; &lt;World,2&gt;</p>
</div>
<div class="para">　　<img src="https://images0.cnblogs.com/blog2015/595707/201505/071112274075808.gif" alt=""></div>
<div style="text-align:right;"><a href="https://www.cnblogs.com/hehaiyang/p/4484442.html#_labelTop" rel="nofollow">回到目录</a><a name="label_1"></a></div>
<h3><span style="font-family:'宋体';">2、WordCount源码</span></h3>
<div class="para">
<div class="cnblogs_code">
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a title="复制代码"><img src="https://common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color:#0000ff;">package</span><span style="color:#000000;"> org.apache.hadoop.examples;

</span><span style="color:#0000ff;">import</span><span style="color:#000000;"> java.io.IOException;
</span><span style="color:#0000ff;">import</span><span style="color:#000000;"> java.util.StringTokenizer;

</span><span style="color:#0000ff;">import</span><span style="color:#000000;"> org.apache.hadoop.conf.Configuration;
</span><span style="color:#0000ff;">import</span><span style="color:#000000;"> org.apache.hadoop.fs.Path;
</span><span style="color:#0000ff;">import</span><span style="color:#000000;"> org.apache.hadoop.io.IntWritable;
</span><span style="color:#0000ff;">import</span><span style="color:#000000;"> org.apache.hadoop.io.Text;
</span><span style="color:#0000ff;">import</span><span style="color:#000000;"> org.apache.hadoop.mapreduce.Job;
</span><span style="color:#0000ff;">import</span><span style="color:#000000;"> org.apache.hadoop.mapreduce.Mapper;
</span><span style="color:#0000ff;">import</span><span style="color:#000000;"> org.apache.hadoop.mapreduce.Reducer;
</span><span style="color:#0000ff;">import</span><span style="color:#000000;"> org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
</span><span style="color:#0000ff;">import</span><span style="color:#000000;"> org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
</span><span style="color:#0000ff;">import</span><span style="color:#000000;"> org.apache.hadoop.util.GenericOptionsParser;
</span><span style="color:#008000;">/**</span><span style="color:#008000;">
 * 
 * 描述：WordCount explains by York
  * </span><span style="color:#808080;">@author</span><span style="color:#008000;"> Hadoop Dev Group
 </span><span style="color:#008000;">*/</span><span style="color:#000000;">
publicclass WordCount {
    </span><span style="color:#008000;">/**</span><span style="color:#008000;">
     * 建立Mapper类TokenizerMapper继承自泛型类Mapper
     * Mapper类:实现了Map功能基类
     * Mapper接口：
     * WritableComparable接口：实现WritableComparable的类可以相互比较。所有被用作key的类应该实现此接口。
     * Reporter 则可用于报告整个应用的运行进度，本例中未使用。 
     * 
     </span><span style="color:#008000;">*/</span><span style="color:#000000;">
  publicstaticclass TokenizerMapper 
       </span><span style="color:#0000ff;">extends</span> Mapper&lt;Object, Text, Text, IntWritable&gt;<span style="color:#000000;">{
        </span><span style="color:#008000;">/**</span><span style="color:#008000;">
         * IntWritable, Text 均是 Hadoop 中实现的用于封装 Java 数据类型的类，这些类实现了WritableComparable接口，
         * 都能够被串行化从而便于在分布式环境中进行数据交换，你可以将它们分别视为int,String 的替代品。
     * 声明one常量和word用于存放单词的变量
         </span><span style="color:#008000;">*/</span><span style="color:#000000;">
    privatefinalstatic IntWritable one </span>=<span style="color:#0000ff;">new</span> IntWritable(1<span style="color:#000000;">);
    </span><span style="color:#0000ff;">private</span> Text word =<span style="color:#0000ff;">new</span><span style="color:#000000;"> Text();
    </span><span style="color:#008000;">/**</span><span style="color:#008000;">
         * Mapper中的map方法：
         * void map(K1 key, V1 value, Context context)
         * 映射一个单个的输入k/v对到一个中间的k/v对
         * 输出对不需要和输入对是相同的类型，输入对可以映射到0个或多个输出对。
         * Context：收集Mapper输出的&lt;k,v&gt;对。
         * Context的write(k, v)方法:增加一个(k,v)对到context
         * 程序员主要编写Map和Reduce函数.这个Map函数使用StringTokenizer函数对字符串进行分隔,通过write方法把单词存入word中
     * write方法存入(单词,1)这样的二元组到context中
     </span><span style="color:#008000;">*/</span><span style="color:#000000;">  
    publicvoid map(Object key, Text value, Context context
                    ) </span><span style="color:#0000ff;">throws</span><span style="color:#000000;"> IOException, InterruptedException {
      StringTokenizer itr </span>=<span style="color:#0000ff;">new</span><span style="color:#000000;"> StringTokenizer(value.toString());
      </span><span style="color:#0000ff;">while</span><span style="color:#000000;"> (itr.hasMoreTokens()) {
        word.set(itr.nextToken());
        context.write(word, one);
      }
    }
  }
  
  publicstaticclass IntSumReducer 
       </span><span style="color:#0000ff;">extends</span> Reducer&lt;Text,IntWritable,Text,IntWritable&gt;<span style="color:#000000;"> {
    </span><span style="color:#0000ff;">private</span> IntWritable result =<span style="color:#0000ff;">new</span><span style="color:#000000;"> IntWritable();
    </span><span style="color:#008000;">/**</span><span style="color:#008000;">
         * Reducer类中的reduce方法：
      * void reduce(Text key, Iterable&lt;IntWritable&gt; values, Context context)
         * 中k/v来自于map函数中的context,可能经过了进一步处理(combiner),同样通过context输出           
         </span><span style="color:#008000;">*/</span><span style="color:#000000;">
    publicvoid reduce(Text key, Iterable</span>&lt;IntWritable&gt;<span style="color:#000000;"> values, 
                       Context context
                       ) </span><span style="color:#0000ff;">throws</span><span style="color:#000000;"> IOException, InterruptedException {
      </span><span style="color:#0000ff;">int</span> sum =0<span style="color:#000000;">;
      </span><span style="color:#0000ff;">for</span><span style="color:#000000;"> (IntWritable val : values) {
        sum </span>+=<span style="color:#000000;"> val.get();
      }
      result.set(sum);
      context.write(key, result);
    }
  }

  publicstaticvoid main(String[] args) </span><span style="color:#0000ff;">throws</span><span style="color:#000000;"> Exception {
        </span><span style="color:#008000;">/**</span><span style="color:#008000;">
         * Configuration：map/reduce的j配置类，向hadoop框架描述map-reduce执行的工作
         </span><span style="color:#008000;">*/</span><span style="color:#000000;">
    Configuration conf </span>=<span style="color:#0000ff;">new</span><span style="color:#000000;"> Configuration();
    String[] otherArgs </span>=<span style="color:#0000ff;">new</span><span style="color:#000000;"> GenericOptionsParser(conf, args).getRemainingArgs();
    </span><span style="color:#0000ff;">if</span> (otherArgs.length !=2<span style="color:#000000;">) {
      System.err.println(</span>"Usage: wordcount &lt;in&gt; &lt;out&gt;"<span style="color:#000000;">);
      System.exit(</span>2<span style="color:#000000;">);
    }
    Job job </span>=<span style="color:#0000ff;">new</span> Job(conf, "word count");    <span style="color:#008000;">//</span><span style="color:#008000;">设置一个用户定义的job名称</span>
    job.setJarByClass(WordCount.<span style="color:#0000ff;">class</span><span style="color:#000000;">);
    job.setMapperClass(TokenizerMapper.</span><span style="color:#0000ff;">class</span>);    <span style="color:#008000;">//</span><span style="color:#008000;">为job设置Mapper类</span>
    job.setCombinerClass(IntSumReducer.<span style="color:#0000ff;">class</span>);    <span style="color:#008000;">//</span><span style="color:#008000;">为job设置Combiner类</span>
    job.setReducerClass(IntSumReducer.<span style="color:#0000ff;">class</span>);    <span style="color:#008000;">//</span><span style="color:#008000;">为job设置Reducer类</span>
    job.setOutputKeyClass(Text.<span style="color:#0000ff;">class</span>);        <span style="color:#008000;">//</span><span style="color:#008000;">为job的输出数据设置Key类</span>
    job.setOutputValueClass(IntWritable.<span style="color:#0000ff;">class</span>);    <span style="color:#008000;">//</span><span style="color:#008000;">为job输出设置value类</span>
    FileInputFormat.addInputPath(job, <span style="color:#0000ff;">new</span> Path(otherArgs[0]));    <span style="color:#008000;">//</span><span style="color:#008000;">为job设置输入路径</span>
    FileOutputFormat.setOutputPath(job, <span style="color:#0000ff;">new</span> Path(otherArgs[1]));<span style="color:#008000;">//</span><span style="color:#008000;">为job设置输出路径</span>
    System.exit(job.waitForCompletion(<span style="color:#0000ff;">true</span>) ?0 : 1);        <span style="color:#008000;">//</span><span style="color:#008000;">运行job</span>
<span style="color:#000000;">  }
}</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a title="复制代码"><img src="https://common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
</div>
</div>
<div style="text-align:right;"><a href="https://www.cnblogs.com/hehaiyang/p/4484442.html#_labelTop" rel="nofollow">回到目录</a><a name="label_2"></a></div>
<h3 class="para"><span style="font-family:'宋体';">3、WordCount逐行解析</span></h3>
<ul><li><span style="font-family:'宋体';">对于map函数的方法。</span></li></ul><div class="para">
<div class="cnblogs_code">
<pre>public void map(Object key, Text value, Context context) throws IOException, InterruptedException {…}</pre>
</div>
<p>　　这里有三个参数，前面两个Object key, Text value就是输入的key和value，第三个参数Context context这是可以记录输入的key和value，例如：context.write(word, one);此外context还会记录map运算的状态。</p>
<ul><li>对于reduce函数的方法。</li></ul><div class="cnblogs_code">
<pre>public void reduce(Text key, Iterable&lt;IntWritable&gt; values, Context context) throws IOException, InterruptedException {…}</pre>
</div>
<p>　　reduce函数的输入也是一个key/value的形式，不过它的value是一个迭代器的形式Iterable&lt;IntWritable&gt; values，也就是说reduce的输入是一个key对应一组的值的value，reduce也有context和map的context作用一致。</p>
<p>　　至于计算的逻辑则需要程序员编码实现。</p>
<ul><li>对于main函数的调用。</li></ul><p>　　首先是：</p>
<div class="cnblogs_code">
<pre>Configuration conf = new Configuration();</pre>
</div>
<p>　　运行MapReduce程序前都要初始化Configuration，该类主要是读取MapReduce系统配置信息，这些信息包括hdfs还有MapReduce，也就是安装hadoop时候的配置文件例如：core-site.xml、hdfs-site.xml和mapred-site.xml等等文件里的信息，有些童鞋不理解为啥要这么做，这个是没有深入思考MapReduce计算框架造成，我们程序员开发MapReduce时候只是在填空，在map函数和reduce函数里编写实际进行的业务逻辑，其它的工作都是交给MapReduce框架自己操作的，但是至少我们要告诉它怎么操作啊，比如hdfs在哪里，MapReduce的jobstracker在哪里，而这些信息就在conf包下的配置文件里。</p>
<p>　　接下来的代码是：</p>
<div class="cnblogs_code">
<pre>    String[] otherArgs = new GenericOptionsParser(conf, args).getRemainingArgs();
    if (otherArgs.length != 2) {
      System.err.println("Usage: wordcount &lt;in&gt; &lt;out&gt;");
      System.exit(2);
    }</pre>
</div>
<p>　　If的语句好理解，就是运行WordCount程序时候一定是两个参数，如果不是就会报错退出。至于第一句里的GenericOptionsParser类，它是用来解释常用hadoop命令，并根据需要为Configuration对象设置相应的值，其实平时开发里我们不太常用它，而是让类实现Tool接口，然后再main函数里使用ToolRunner运行程序，而ToolRunner内部会调用GenericOptionsParser。</p>
<p>　　接下来的代码是：</p>
<div class="cnblogs_code">
<pre>    Job job = new Job(conf, "word count");
    job.setJarByClass(WordCount.class);
    job.setMapperClass(TokenizerMapper.class);
    job.setCombinerClass(IntSumReducer.class);
    job.setReducerClass(IntSumReducer.class);</pre>
</div>
<p>　　第一行就是在构建一个job，在mapreduce框架里一个mapreduce任务也叫mapreduce作业也叫做一个mapreduce的job，而具体的map和reduce运算就是task了，这里我们构建一个job，构建时候有两个参数，一个是conf这个就不累述了，一个是这个job的名称。</p>
<p>　　第二行就是装载程序员编写好的计算程序，例如我们的程序类名就是WordCount了。这里我要做下纠正，虽然我们编写mapreduce程序只需要实现map函数和reduce函数，但是实际开发我们要实现三个类，第三个类是为了配置mapreduce如何运行map和reduce函数，准确的说就是构建一个mapreduce能执行的job了，例如WordCount类。</p>
<p>　　第三行和第五行就是装载map函数和reduce函数实现类了，这里多了个第四行，这个是装载Combiner类，这个类和mapreduce运行机制有关，其实本例去掉第四行也没有关系，但是使用了第四行理论上运行效率会更好。</p>
<p>　　接下来的代码：</p>
<div class="cnblogs_code">
<pre>    job.setOutputKeyClass(Text.class);
    job.setOutputValueClass(IntWritable.class);</pre>
</div>
<p>　　这个是定义输出的key/value的类型，也就是最终存储在hdfs上结果文件的key/value的类型。</p>
<p>　　最后的代码是：</p>
<div class="cnblogs_code">
<pre>    FileInputFormat.addInputPath(job, new Path(otherArgs[0]));
    FileOutputFormat.setOutputPath(job, new Path(otherArgs[1]));
    System.exit(job.waitForCompletion(true) ? 0 : 1);</pre>
</div>
<p>　　第一行就是构建输入的数据文件，第二行是构建输出的数据文件，最后一行如果job运行成功了，我们的程序就会正常退出。</p>
<p></p>
<h3>Mapreduce运行机制</h3>
<img src="https://img-blog.csdn.net/20180121123737767?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc2h1anVlbGlu/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><p></p>
<p>MapReduce主要包括JobClient, JobTracker, TaskTracker , HDFS  4个独立的部分</p>
<p></p>
<p>谈mapreduce运行机制，可以从很多不同的角度来描述，比如说从mapreduce运行流程来讲解，也可以从计算模型的逻辑流程来进行讲解，也许有些深入理解了mapreduce运行机制还会从更好的角度来描述，但是将mapreduce运行机制有些东西是避免不了的，就是一个个参入的实例对象，一个就是计算模型的逻辑定义阶段，我这里讲解不从什么流程出发，就从这些一个个牵涉的对象，不管是物理实体还是逻辑实体。</p>
<p>首先讲讲物理实体，参入mapreduce作业执行涉及4个独立的实体：</p>
<ol><li>客户端（client）：编写mapreduce程序，配置作业，提交作业，这就是程序员完成的工作；</li><li>JobTracker：初始化作业，分配作业，与TaskTracker通信，协调整个作业的执行；</li><li>TaskTracker：保持与JobTracker的通信，在分配的数据片段上执行Map或Reduce任务，TaskTracker和JobTracker的不同有个很重要的方面，就是在执行任务时候TaskTracker可以有n多个，JobTracker则只会有一个（JobTracker只能有一个就和hdfs里namenode一样存在单点故障，我会在后面的mapreduce的相关问题里讲到这个问题的）</li><li>Hdfs：保存作业的数据、配置信息等等，最后的结果也是保存在hdfs上面</li></ol><h3><span style="color:#ff0000;">那么mapreduce到底是如何运行的呢？</span></h3>
<p></p>
<p>首先是客户端要编写好mapreduce程序，配置好mapreduce的作业也就是job，接下来就是提交job了，提交job是提交到JobTracker上的，这个时候JobTracker就会构建这个job，具体就是分配一个新的job任务的ID值，接下来它会做检查操作，这个检查就是确定输出目录是否存在，如果存在那么job就不能正常运行下去，JobTracker会抛出错误给客户端，接下来还要检查输入目录是否存在，如果不存在同样抛出错误，如果存在JobTracker会根据输入计算输入分片（Input Split），如果分片计算不出来也会抛出错误，至于输入分片我后面会做讲解的，这些都做好了JobTracker就会配置Job需要的资源了。分配好资源后，JobTracker就会初始化作业，初始化主要做的是将Job放入一个内部的队列，让配置好的作业调度器能调度到这个作业，作业调度器会初始化这个job，初始化就是创建一个正在运行的job对象（封装任务和记录信息），以便JobTracker跟踪job的状态和进程。</p>
<p>初始化完毕后，作业调度器会获取输入分片信息（input split），每个分片创建一个map任务。接下来就是任务分配了，这个时候tasktracker会运行一个简单的循环机制定期发送心跳给jobtracker，心跳间隔是5秒，程序员可以配置这个时间，心跳就是jobtracker和tasktracker沟通的桥梁，通过心跳，jobtracker可以监控tasktracker是否存活，也可以获取tasktracker处理的状态和问题，同时tasktracker也可以通过心跳里的返回值获取jobtracker给它的操作指令。任务分配好后就是执行任务了。在任务执行时候jobtracker可以通过心跳机制监控tasktracker的状态和进度，同时也能计算出整个job的状态和进度，而tasktracker也可以本地监控自己的状态和进度。当jobtracker获得了最后一个完成指定任务的tasktracker操作成功的通知时候，jobtracker会把整个job状态置为成功，然后当客户端查询job运行状态时候（注意：这个是异步操作），客户端会查到job完成的通知的。如果job中途失败，mapreduce也会有相应机制处理，一般而言如果不是程序员程序本身有bug，mapreduce错误处理机制都能保证提交的job能正常完成。</p>
<p>下面我从逻辑实体的角度讲解mapreduce运行机制，这些按照时间顺序包括：<strong>输入分片（input split）、map阶段、combiner阶段、shuffle阶段和reduce阶段</strong>。</p>
<p><strong style="color:#333333;font-style:normal;">1. 输入分片（input split）：</strong><span style="color:#333333;font-style:normal;">在进行map计算之前，mapreduce会根据输入文件计算输入分片（input split），每个输入分片（input split）针对一个map任务，输入分片（input split）存储的并非数据本身，而是一个分片长度和一个记录数据的位置的数组，输入分片（input
 split）往往和hdfs的block（块）关系很密切，假如我们设定hdfs的块的大小是64mb，如果我们输入有三个文件，大小分别是3mb、65mb和127mb，那么mapreduce会把3mb文件分为一个输入分片（input split），65mb则是两个输入分片（input split）而127mb也是两个输入分片（input split），换句话说我们如果在map计算前做输入分片调整，例如合并小文件，那么就会有5个map任务将执行，而且每个map执行的数据大小不均，这个也是mapreduce优化计算的一个关键点。</span></p>
<p><strong>2. map阶段：</strong>就是程序员编写好的map函数了，因此map函数效率相对好控制，而且一般map操作都是本地化操作也就是在数据存储节点上进行；</p>
<p><strong>3. combiner阶段：c</strong>ombiner阶段是程序员可以选择的，combiner其实也是一种reduce操作，因此我们看见WordCount类里是用reduce进行加载的。Combiner是一个本地化的reduce操作，它是map运算的后续操作，主要是在map计算出中间文件前做一个简单的合并重复key值的操作，例如我们对文件里的单词频率做统计，map计算时候如果碰到一个hadoop的单词就会记录为1，但是这篇文章里hadoop可能会出现n多次，那么map输出文件冗余就会很多，因此在reduce计算前对相同的key做一个合并操作，那么文件会变小，这样就提高了宽带的传输效率，毕竟hadoop计算力宽带资源往往是计算的瓶颈也是最为宝贵的资源，但是combiner操作是有风险的，使用它的原则是combiner的输入不会影响到reduce计算的最终输入，例如：如果计算只是求总数，最大值，最小值可以使用combiner，但是做平均值计算使用combiner的话，最终的reduce计算结果就会出错。<br><strong></strong></p>
<p><strong>4. shuffle阶段：</strong>将map的输出作为reduce的输入的过程就是shuffle了，这个是mapreduce优化的重点地方。这里我不讲怎么优化shuffle阶段，讲讲shuffle阶段的原理，因为大部分的书籍里都没讲清楚shuffle阶段。Shuffle一开始就是map阶段做输出操作，一般mapreduce计算的都是海量数据，map输出时候不可能把所有文件都放到内存操作，因此map写入磁盘的过程十分的复杂，更何况map输出时候要对结果进行排序，内存开销是很大的，map在做输出时候会在内存里开启一个环形内存缓冲区，这个缓冲区专门用来输出的，默认大小是100mb，并且在配置文件里为这个缓冲区设定了一个阀值，默认是0.80（这个大小和阀值都是可以在配置文件里进行配置的），同时map还会为输出操作启动一个守护线程，如果缓冲区的内存达到了阀值的80%时候，这个守护线程就会把内容写到磁盘上，这个过程叫spill，另外的20%内存可以继续写入要写进磁盘的数据，写入磁盘和写入内存操作是互不干扰的，如果缓存区被撑满了，那么map就会阻塞写入内存的操作，让写入磁盘操作完成后再继续执行写入内存操作，前面我讲到写入磁盘前会有个排序操作，这个是在写入磁盘操作时候进行，不是在写入内存时候进行的，如果我们定义了combiner函数，那么排序前还会执行combiner操作。</p>
<p>每次spill操作也就是写入磁盘操作时候就会写一个溢出文件，也就是说在做map输出有几次spill就会产生多少个溢出文件，等map输出全部做完后，map会合并这些输出文件。这个过程里还会有一个Partitioner操作，对于这个操作很多人都很迷糊，其实Partitioner操作和map阶段的输入分片（Input split）很像，一个Partitioner对应一个reduce作业，如果我们mapreduce操作只有一个reduce操作，那么Partitioner就只有一个，如果我们有多个reduce操作，那么Partitioner对应的就会有多个，Partitioner因此就是reduce的输入分片，这个程序员可以编程控制，主要是根据实际key和value的值，根据实际业务类型或者为了更好的reduce负载均衡要求进行，这是提高reduce效率的一个关键所在。到了reduce阶段就是合并map输出文件了，Partitioner会找到对应的map输出文件，然后进行复制操作，复制操作时reduce会开启几个复制线程，这些线程默认个数是5个，程序员也可以在配置文件更改复制线程的个数，这个复制过程和map写入磁盘过程类似，也有阀值和内存大小，阀值一样可以在配置文件里配置，而内存大小是直接使用reduce的tasktracker的内存大小，复制时候reduce还会进行排序操作和合并文件操作，这些操作完了就会进行reduce计算了。</p>
<p><strong>5. reduce阶段</strong>：和map函数一样也是程序员编写的，最终结果是存储在hdfs上的。</p>
<br><br></div>
            </div>
                </div>