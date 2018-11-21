---
layout:     post
title:      Spark入门，概述，部署，以及学习（Spark是一种快速、通用、可扩展的大数据分析引擎）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/Biexiansheng/article/details/78973394				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-size:18px;">1：Spark的官方网址：<a href="http://spark.apache.org/" rel="nofollow">http://spark.apache.org/</a></span><br></p>
<div class="cnblogs_code">
<pre><span style="color:#008080;">1</span> Spark生态系统已经发展成为一个包含多个子项目的集合，其中包含SparkSQL、Spark Streaming、GraphX、MLlib等子项目，Spark是基于内存计算的大数据并行计算框架。Spark基于内存计算，提高了在大数据环境下数据处理的实时性，同时保证了高容错性和高可伸缩性，允许用户将Spark部署在大量廉价硬件之上，形成集群。
2 Spark是MapReduce的替代方案，而且兼容HDFS、Hive，可融入Hadoop的生态系统，以弥补MapReduce的不足。</pre>
</div>
<p><span style="font-size:18px;"> 2：Spark特点：</span></p>
<div class="cnblogs_code">
<pre><span style="color:#008080;">1</span> <span style="color:#800080;">1</span><span style="color:#000000;">：特点一:快
</span><span style="color:#008080;">2</span> <span style="color:#000000;">    与Hadoop的MapReduce相比，Spark基于内存的运算要快100倍以上，基于硬盘的运算也要快10倍以上。Spark实现了高效的DAG执行引擎，可以通过基于内存来高效处理数据流。
</span><span style="color:#008080;">3</span> <span style="color:#800080;">2</span><span style="color:#000000;">：特点二:易用
</span><span style="color:#008080;">4</span> <span style="color:#000000;">    Spark支持Java、Python和Scala的API，还支持超过80种高级算法，使用户可以快速构建不同的应用。而且Spark支持交互式的Python和Scala的shell，可以非常方便地在这些shell中使用Spark集群来验证解决问题的方法。
</span><span style="color:#008080;">5</span> <span style="color:#800080;">3</span><span style="color:#000000;">：特点三:通用
</span><span style="color:#008080;">6</span> <span style="color:#000000;">    Spark提供了统一的解决方案。Spark可以用于批处理、交互式查询（Spark SQL）、实时流处理（Spark Streaming）、机器学习（Spark MLlib）和图计算（GraphX）。这些不同类型的处理都可以在同一个应用中无缝使用。Spark统一的解决方案非常具有吸引力，毕竟任何公司都想用统一的平台去处理遇到的问题，减少开发和维护的人力成本和部署平台的物力成本。
</span><span style="color:#008080;">7</span> <span style="color:#800080;">4</span><span style="color:#000000;">：特点四:兼容性    
</span><span style="color:#008080;">8</span>     Spark可以非常方便地与其他的开源产品进行融合。比如，Spark可以使用Hadoop的YARN和Apache Mesos作为它的资源管理和调度器，器，并且可以处理所有Hadoop支持的数据，包括HDFS、HBase和Cassandra等。这对于已经部署Hadoop集群的用户特别重要，因为不需要做任何数据迁移就可以使用Spark的强大处理能力。Spark也可以不依赖于第三方的资源管理和调度器，它实现了Standalone作为其内置的资源管理和调度框架，这样进一步降低了Spark的使用门槛，使得所有人都可以非常容易地部署和使用Spark。此外，Spark还提供了在EC2上部署Standalone的Spark集群的工具。</pre>
</div>
<p><span style="font-size:18px;">3：Spark的部署安装（上传jar，过程省略，记得安装好jdk。）：</span></p>
<p><span style="font-size:18px;">下载网址：<a href="http://www.apache.org/dyn/closer.lua/spark/" rel="nofollow">http://www.apache.org/dyn/closer.lua/spark/</a>或者
<a href="http://spark.apache.org/downloads.html" rel="nofollow">
http://spark.apache.org/downloads.html</a></span><br></p>
<p><span style="font-size:18px;">Spark的解压缩操作，如下所示：</span></p>
<p><img src="http://images2017.cnblogs.com/blog/1002211/201801/1002211-20180102190247815-838797485.png" alt=""></p>
<p><span style="font-size:18px;">哈哈哈，犯了一个低级错误，千万记得加-C，解压安装包到指定位置。是大写的哦；</span></p>
<p><img src="http://images2017.cnblogs.com/blog/1002211/201801/1002211-20180102190424909-1253291725.png" alt=""></p>
<p><span style="font-size:18px;">然后呢，进入到Spark安装目录，进入conf目录并重命名并修改spark-env.sh.template文件，操作如下所示：</span></p>
<p><img src="http://images2017.cnblogs.com/blog/1002211/201801/1002211-20180102190627534-1238248248.png" alt=""></p>
<p><span style="font-size:18px;">将spark-env.sh.template 名称修改为spark-env.sh，然后在该配置文件中添加如下配置，之后保存退出：</span></p>
<div class="cnblogs_code">
<pre><span style="color:#008080;">1</span> [root@localhost conf]# mv spark-env.sh.template spark-env.sh</pre>
</div>
<p><span style="font-size:18px;">具体操作如下所示：<br></span></p>
<p><img src="http://images2017.cnblogs.com/blog/1002211/201801/1002211-20180102191555049-1762751065.png" alt=""></p>
<p><span style="font-size:18px;">然后呢，重命名并修改slaves.template文件，如下所示：</span></p>
<div class="cnblogs_code">
<pre><span style="color:#008080;">1</span> [root@localhost conf]# mv slaves.template slaves</pre>
</div>
<p><span style="font-size:18px;">在该文件中添加子节点所在的位置（Worker节点），操作如下所示，然后保存退出：</span></p>
<p><img src="http://images2017.cnblogs.com/blog/1002211/201801/1002211-20180102192145831-1325797972.png" alt=""></p>
<p><span style="font-size:18px;">将配置好的Spark拷贝到其他节点上：</span></p>
<div class="cnblogs_code">
<pre><span style="color:#008080;">1</span> [root@localhost hadoop]# scp -r spark-<span style="color:#800080;">1.6</span>.<span style="color:#800080;">1</span>-bin-hadoop2.<span style="color:#800080;">6</span>/ slaver1:/home/hadoop/
<span style="color:#008080;">2</span> [root@localhost hadoop]# scp -r spark-<span style="color:#800080;">1.6</span>.<span style="color:#800080;">1</span>-bin-hadoop2.<span style="color:#800080;">6</span>/ slaver2:/home/hadoop/</pre>
</div>
<p><span style="font-size:18px;">Spark集群配置完毕，目前是1个Master，2个Work(可以是多个Work)，在master节点上启动Spark集群：</span></p>
<p><img src="http://images2017.cnblogs.com/blog/1002211/201801/1002211-20180102192438456-789417496.png" alt=""></p>
<p><span style="font-size:18px;">启动后执行jps命令，主节点上有Master进程，其他子节点上有Work进行，登录Spark管理界面查看集群状态（主节点）：http://master:8080/：</span></p>
<p><span style="font-size:18px;">可以查看一下是否启动起来，如下所示：</span></p>
<p><span style="font-size:18px;"><img src="http://images2017.cnblogs.com/blog/1002211/201801/1002211-20180102192749034-605831453.png" alt=""></span></p>
<p><img src="http://images2017.cnblogs.com/blog/1002211/201801/1002211-20180102192806940-143860135.png" alt=""></p>
<p><img src="http://images2017.cnblogs.com/blog/1002211/201801/1002211-20180102192824018-595890675.png" alt=""></p>
<p><span style="font-size:18px;">然后在页面可以查看信息，如下所示，如果浏览器一直加载不出来，可能是防火墙没关闭（service iptables stop暂时关闭，chkconfig iptables off永久关闭）：</span></p>
<p><img src="http://images2017.cnblogs.com/blog/1002211/201801/1002211-20180102193002956-1455843982.png" alt=""></p>
<p><span style="font-size:18px;">到此为止，Spark集群安装完毕。</span></p>
<div class="cnblogs_code">
<pre><span style="color:#008080;">1</span> <span style="color:#000000;">但是有一个很大的问题，那就是Master节点存在单点故障，要解决此问题，就要借助zookeeper，并且启动至少两个Master节点来实现高可靠，配置方式比较简单，如下所示：
</span><span style="color:#008080;">2</span> <span style="color:#000000;">Spark集群规划：node1，node2是Master；node3，node4，node5是Worker
</span><span style="color:#008080;">3</span> 安装配置zk集群，并启动zk集群，然后呢，停止spark所有服务，修改配置文件spark-<span style="color:#000000;">env.sh,
</span><span style="color:#008080;">4</span> <span style="color:#000000;">在该配置文件中删掉SPARK_MASTER_IP并添加如下配置:
</span><span style="color:#008080;">5</span> export SPARK_DAEMON_JAVA_OPTS=<span style="color:#800000;">"</span><span style="color:#800000;">-Dspark.deploy.recoveryMode=ZOOKEEPER -Dspark.deploy.zookeeper.url=zk1,zk2,zk3 -Dspark.deploy.zookeeper.dir=/spark</span><span style="color:#800000;">"</span>
<span style="color:#008080;">6</span> <span style="color:#800080;">1</span><span style="color:#000000;">.在node1节点上修改slaves配置文件内容指定worker节点
</span><span style="color:#008080;">7</span> <span style="color:#800080;">2</span>.在node1上执行sbin/start-all.sh脚本，然后在node2上执行sbin/start-master.sh启动第二个Master</pre>
</div>
<p> <span style="font-size:18px;">4：执行Spark程序（执行第一个spark程序，如下所示）：</span></p>
<p><span style="font-size:18px;">执行如下所示，然后就报了一大推错误，由于错误过多就隐藏了，方便以后脑补：</span></p>
<div class="cnblogs_code">
<pre><span style="color:#008080;">1</span> [root@master bin]# ./spark-<span style="color:#000000;">submit \
</span><span style="color:#008080;">2</span> &gt; --<span style="color:#0000ff;">class</span><span style="color:#000000;"> org.apache.spark.examples.SparkPi \
</span><span style="color:#008080;">3</span> &gt; --master spark:<span style="color:#008000;">//</span><span style="color:#008000;">master:7077 \</span>
<span style="color:#008080;">4</span> &gt; --executor-<span style="color:#000000;">memory 1G \
</span><span style="color:#008080;">5</span> &gt; --total-executor-cores 2<span style="color:#000000;"> \
</span><span style="color:#008080;">6</span> &gt; /home/hadoop/spark-1.6.1-bin-hadoop2.6/<span style="color:#000000;">l
</span><span style="color:#008080;">7</span> lib/      licenses/ logs/     
<span style="color:#008080;">8</span> &gt; /home/hadoop/spark-1.6.1-bin-hadoop2.6/lib/spark-examples-1.6.1-hadoop2.6.0<span style="color:#000000;">.jar \
</span><span style="color:#008080;">9</span> &gt; 100

或者如下所示也可：
[root@master spark-1.6.1-bin-hadoop2.6]# bin/spark-submit --class org.apache.spark.examples.SparkPi --master spark://master:7077 --executor-memory 512M --total-executor-cores 2 /home/hadoop/spark-1.6.1-bin-hadoop2.6/lib/spark-examples-1.6.1-hadoop2.6.0.jar 10</pre>
</div>
<p> <span style="font-size:18px;">错误如下所示，由于太长了就折叠起来了：</span></p>
<div class="cnblogs_code"><img id="code_img_closed_45f365c0-2f24-4382-8b12-539cc8ed1b93" class="code_img_closed" src="http://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif" alt=""><span class="cnblogs_code_collapse">View Code</span></div>
<p><span style="font-size:18px;">由于之前学习hadoop，虚拟机内存才设置512M了，Spark是在内存中进行运算的，所以学习Spark一定要设置好内存啊，关闭虚拟机，将内存设置为1G，给Spark设置800M的内存，所以spark-env.sh配置，多添加了：</span></p>
<div class="cnblogs_code">
<pre>export SPARK_WORKER_MEMORY=800M</pre>
</div>
<p> <span style="font-size:18px;">如下所示：</span></p>
<p> <img src="http://images2017.cnblogs.com/blog/1002211/201801/1002211-20180103130549034-406813429.png" alt=""></p>
<p><span style="font-size:18px;"> 然后执行，如下所示命令：</span></p>
<div class="cnblogs_code">
<pre><span style="color:#008080;">1</span> [root@master spark-1.6.1-bin-hadoop2.6]# bin/spark-<span style="color:#000000;">submit \
</span><span style="color:#008080;">2</span> &gt; --<span style="color:#0000ff;">class</span><span style="color:#000000;"> org.apache.spark.examples.SparkPi \
</span><span style="color:#008080;">3</span> &gt; --master spark:<span style="color:#008000;">//</span><span style="color:#008000;">master:7077 \</span>
<span style="color:#008080;">4</span> &gt; --executor-<span style="color:#000000;">memory 512M \
</span><span style="color:#008080;">5</span> &gt; --total-executor-cores 2<span style="color:#000000;"> \
</span><span style="color:#008080;">6</span> &gt; /home/hadoop/spark-1.6.1-bin-hadoop2.6/lib/spark-examples-1.6.1-hadoop2.6.0<span style="color:#000000;">.jar \
</span><span style="color:#008080;">7</span> &gt; 100</pre>
</div>
<p><span style="font-size:18px;">5：启动Spark Shell：</span></p>
<p><span style="font-size:18px;">　　spark-shell是Spark自带的交互式Shell程序，方便用户进行交互式编程，用户可以在该命令行下用scala编写spark程序。</span></p>
<p><span style="font-size:18px;">启动spark shell，如下所示：</span></p>
<div class="cnblogs_code">
<pre><span style="color:#008080;">1</span> [root@master spark-1.6.1-bin-hadoop2.6]# bin/spark-<span style="color:#000000;">shell \
</span><span style="color:#008080;">2</span> &gt; --master spark:<span style="color:#008000;">//</span><span style="color:#008000;">master:7077 \</span>
<span style="color:#008080;">3</span> &gt; --executor-<span style="color:#000000;">memory 512M \
</span><span style="color:#008080;">4</span> &gt; --total-executor-cores 2
<span style="color:#008080;">5</span> 
<span style="color:#008080;">6</span> <span style="color:#000000;">参数说明：
</span><span style="color:#008080;">7</span> --master spark:<span style="color:#008000;">//</span><span style="color:#008000;">master:7077 指定Master的地址</span>
<span style="color:#008080;">8</span> --executor-<span style="color:#000000;">memory 512M 指定每个worker可用内存为512M 
</span><span style="color:#008080;">9</span> --total-executor-cores 2 指定整个集群使用的cup核数为2个</pre>
</div>
<p><span style="font-size:18px;">注意：</span><br><span style="font-size:18px;">    如果启动spark shell时没有指定master地址，但是也可以正常启动spark shell和执行spark shell中的程序，其实是启动了spark的local模式，该模式仅在本机启动一个进程，没有与集群建立联系。</span><br><span style="font-size:18px;">    Spark Shell中已经默认将SparkContext类初始化为对象sc。用户代码如果需要用到，则直接应用sc即可;</span></p>
<p><span style="font-size:18px;">操作如下所示：</span></p>
<p><span style="font-size:18px;"><img src="http://images2017.cnblogs.com/blog/1002211/201801/1002211-20180103131835315-1986294621.png" alt=""></span></p>
<p><span style="font-size:18px;">退出使用命令exit即可；</span></p>
<p><span style="font-size:18px;">贴一下日了狗了的报错，没有接受指令超过一定时间就报错了，如下所示，按Enter又回到<span style="color:#ff0000;">scala&gt;</span> 等待命令键入：</span></p>
<div class="cnblogs_code">
<pre>scala&gt; <span style="color:#800080;">18</span>/<span style="color:#800080;">01</span>/<span style="color:#800080;">03</span> <span style="color:#800080;">02</span>:<span style="color:#800080;">37</span>:<span style="color:#800080;">36</span> WARN NettyRpcEndpointRef: Error sending message [message = RemoveExecutor(<span style="color:#800080;">0</span>,Command exited with code <span style="color:#800080;">1</span>)] <span style="color:#0000ff;">in</span> <span style="color:#800080;">1</span><span style="color:#000000;"> attempts
org.apache.spark.rpc.RpcTimeoutException: Cannot receive any reply </span><span style="color:#0000ff;">in</span> <span style="color:#800080;">120</span> seconds. This timeout <span style="color:#0000ff;">is</span><span style="color:#000000;"> controlled by spark.rpc.askTimeout
    at org.apache.spark.rpc.RpcTimeout.org$apache$spark$rpc$RpcTimeout$$createRpcTimeoutException(RpcTimeout.scala:</span><span style="color:#800080;">48</span><span style="color:#000000;">)
    at org.apache.spark.rpc.RpcTimeout$$anonfun$addMessageIfTimeout$</span><span style="color:#800080;">1</span>.applyOrElse(RpcTimeout.scala:<span style="color:#800080;">63</span><span style="color:#000000;">)
    at org.apache.spark.rpc.RpcTimeout$$anonfun$addMessageIfTimeout$</span><span style="color:#800080;">1</span>.applyOrElse(RpcTimeout.scala:<span style="color:#800080;">59</span><span style="color:#000000;">)
    at scala.runtime.AbstractPartialFunction.apply(AbstractPartialFunction.scala:</span><span style="color:#800080;">33</span><span style="color:#000000;">)
    at scala.util.Failure$$anonfun$recover$</span><span style="color:#800080;">1</span>.apply(Try.scala:<span style="color:#800080;">185</span><span style="color:#000000;">)
    at scala.util.Try$.apply(Try.scala:</span><span style="color:#800080;">161</span><span style="color:#000000;">)
    at scala.util.Failure.recover(Try.scala:</span><span style="color:#800080;">185</span><span style="color:#000000;">)
    at scala.concurrent.Future$$anonfun$recover$</span><span style="color:#800080;">1</span>.apply(Future.scala:<span style="color:#800080;">324</span><span style="color:#000000;">)
    at scala.concurrent.Future$$anonfun$recover$</span><span style="color:#800080;">1</span>.apply(Future.scala:<span style="color:#800080;">324</span><span style="color:#000000;">)
    at scala.concurrent.impl.CallbackRunnable.run(Promise.scala:</span><span style="color:#800080;">32</span><span style="color:#000000;">)
    at org.spark</span>-project.guava.util.concurrent.MoreExecutors$SameThreadExecutorService.execute(MoreExecutors.java:<span style="color:#800080;">293</span><span style="color:#000000;">)
    at scala.concurrent.impl.ExecutionContextImpl$$anon$</span><span style="color:#800080;">1</span>.execute(ExecutionContextImpl.scala:<span style="color:#800080;">133</span><span style="color:#000000;">)
    at scala.concurrent.impl.CallbackRunnable.executeWithValue(Promise.scala:</span><span style="color:#800080;">40</span><span style="color:#000000;">)
    at scala.concurrent.impl.Promise$DefaultPromise.tryComplete(Promise.scala:</span><span style="color:#800080;">248</span><span style="color:#000000;">)
    at scala.concurrent.Promise$</span><span style="color:#0000ff;">class</span>.complete(Promise.scala:<span style="color:#800080;">55</span><span style="color:#000000;">)
    at scala.concurrent.impl.Promise$DefaultPromise.complete(Promise.scala:</span><span style="color:#800080;">153</span><span style="color:#000000;">)
    at scala.concurrent.Future$$anonfun$map$</span><span style="color:#800080;">1</span>.apply(Future.scala:<span style="color:#800080;">235</span><span style="color:#000000;">)
    at scala.concurrent.Future$$anonfun$map$</span><span style="color:#800080;">1</span>.apply(Future.scala:<span style="color:#800080;">235</span><span style="color:#000000;">)
    at scala.concurrent.impl.CallbackRunnable.run(Promise.scala:</span><span style="color:#800080;">32</span><span style="color:#000000;">)
    at scala.concurrent.Future$InternalCallbackExecutor$Batch$$anonfun$run$</span><span style="color:#800080;">1</span>.processBatch$<span style="color:#800080;">1</span>(Future.scala:<span style="color:#800080;">643</span><span style="color:#000000;">)
    at scala.concurrent.Future$InternalCallbackExecutor$Batch$$anonfun$run$</span><span style="color:#800080;">1</span>.apply$mcV$sp(Future.scala:<span style="color:#800080;">658</span><span style="color:#000000;">)
    at scala.concurrent.Future$InternalCallbackExecutor$Batch$$anonfun$run$</span><span style="color:#800080;">1</span>.apply(Future.scala:<span style="color:#800080;">635</span><span style="color:#000000;">)
    at scala.concurrent.Future$InternalCallbackExecutor$Batch$$anonfun$run$</span><span style="color:#800080;">1</span>.apply(Future.scala:<span style="color:#800080;">635</span><span style="color:#000000;">)
    at scala.concurrent.BlockContext$.withBlockContext(BlockContext.scala:</span><span style="color:#800080;">72</span><span style="color:#000000;">)
    at scala.concurrent.Future$InternalCallbackExecutor$Batch.run(Future.scala:</span><span style="color:#800080;">634</span><span style="color:#000000;">)
    at scala.concurrent.Future$InternalCallbackExecutor$.scala$concurrent$Future$InternalCallbackExecutor$$unbatchedExecute(Future.scala:</span><span style="color:#800080;">694</span><span style="color:#000000;">)
    at scala.concurrent.Future$InternalCallbackExecutor$.execute(Future.scala:</span><span style="color:#800080;">685</span><span style="color:#000000;">)
    at scala.concurrent.impl.CallbackRunnable.executeWithValue(Promise.scala:</span><span style="color:#800080;">40</span><span style="color:#000000;">)
    at scala.concurrent.impl.Promise$DefaultPromise.tryComplete(Promise.scala:</span><span style="color:#800080;">248</span><span style="color:#000000;">)
    at scala.concurrent.Promise$</span><span style="color:#0000ff;">class</span>.tryFailure(Promise.scala:<span style="color:#800080;">112</span><span style="color:#000000;">)
    at scala.concurrent.impl.Promise$DefaultPromise.tryFailure(Promise.scala:</span><span style="color:#800080;">153</span><span style="color:#000000;">)
    at org.apache.spark.rpc.netty.NettyRpcEnv$$anon$</span><span style="color:#800080;">1</span>.run(NettyRpcEnv.scala:<span style="color:#800080;">241</span><span style="color:#000000;">)
    at java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:</span><span style="color:#800080;">471</span><span style="color:#000000;">)
    at java.util.concurrent.FutureTask.run(FutureTask.java:</span><span style="color:#800080;">262</span><span style="color:#000000;">)
    at java.util.concurrent.ScheduledThreadPoolExecutor$ScheduledFutureTask.access$</span><span style="color:#800080;">201</span>(ScheduledThreadPoolExecutor.java:<span style="color:#800080;">178</span><span style="color:#000000;">)
    at java.util.concurrent.ScheduledThreadPoolExecutor$ScheduledFutureTask.run(ScheduledThreadPoolExecutor.java:</span><span style="color:#800080;">292</span><span style="color:#000000;">)
    at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:</span><span style="color:#800080;">1145</span><span style="color:#000000;">)
    at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:</span><span style="color:#800080;">615</span><span style="color:#000000;">)
    at java.lang.Thread.run(Thread.java:</span><span style="color:#800080;">745</span><span style="color:#000000;">)
Caused by: java.util.concurrent.TimeoutException: Cannot receive any reply </span><span style="color:#0000ff;">in</span> <span style="color:#800080;">120</span><span style="color:#000000;"> seconds
    at org.apache.spark.rpc.netty.NettyRpcEnv$$anon$</span><span style="color:#800080;">1</span>.run(NettyRpcEnv.scala:<span style="color:#800080;">242</span><span style="color:#000000;">)
    ... </span><span style="color:#800080;">7</span><span style="color:#000000;"> more
</span><span style="color:#800080;">18</span>/<span style="color:#800080;">01</span>/<span style="color:#800080;">03</span> <span style="color:#800080;">02</span>:<span style="color:#800080;">39</span>:<span style="color:#800080;">39</span> WARN NettyRpcEndpointRef: Error sending message [message = RemoveExecutor(<span style="color:#800080;">0</span>,Command exited with code <span style="color:#800080;">1</span>)] <span style="color:#0000ff;">in</span> <span style="color:#800080;">2</span><span style="color:#000000;"> attempts
org.apache.spark.rpc.RpcTimeoutException: Cannot receive any reply </span><span style="color:#0000ff;">in</span> <span style="color:#800080;">120</span> seconds. This timeout <span style="color:#0000ff;">is</span><span style="color:#000000;"> controlled by spark.rpc.askTimeout
    at org.apache.spark.rpc.RpcTimeout.org$apache$spark$rpc$RpcTimeout$$createRpcTimeoutException(RpcTimeout.scala:</span><span style="color:#800080;">48</span><span style="color:#000000;">)
    at org.apache.spark.rpc.RpcTimeout$$anonfun$addMessageIfTimeout$</span><span style="color:#800080;">1</span>.applyOrElse(RpcTimeout.scala:<span style="color:#800080;">63</span><span style="color:#000000;">)
    at org.apache.spark.rpc.RpcTimeout$$anonfun$addMessageIfTimeout$</span><span style="color:#800080;">1</span>.applyOrElse(RpcTimeout.scala:<span style="color:#800080;">59</span><span style="color:#000000;">)
    at scala.runtime.AbstractPartialFunction.apply(AbstractPartialFunction.scala:</span><span style="color:#800080;">33</span><span style="color:#000000;">)
    at scala.util.Failure$$anonfun$recover$</span><span style="color:#800080;">1</span>.apply(Try.scala:<span style="color:#800080;">185</span><span style="color:#000000;">)
    at scala.util.Try$.apply(Try.scala:</span><span style="color:#800080;">161</span><span style="color:#000000;">)
    at scala.util.Failure.recover(Try.scala:</span><span style="color:#800080;">185</span><span style="color:#000000;">)
    at scala.concurrent.Future$$anonfun$recover$</span><span style="color:#800080;">1</span>.apply(Future.scala:<span style="color:#800080;">324</span><span style="color:#000000;">)
    at scala.concurrent.Future$$anonfun$recover$</span><span style="color:#800080;">1</span>.apply(Future.scala:<span style="color:#800080;">324</span><span style="color:#000000;">)
    at scala.concurrent.impl.CallbackRunnable.run(Promise.scala:</span><span style="color:#800080;">32</span><span style="color:#000000;">)
    at org.spark</span>-project.guava.util.concurrent.MoreExecutors$SameThreadExecutorService.execute(MoreExecutors.java:<span style="color:#800080;">293</span><span style="color:#000000;">)
    at scala.concurrent.impl.ExecutionContextImpl$$anon$</span><span style="color:#800080;">1</span>.execute(ExecutionContextImpl.scala:<span style="color:#800080;">133</span><span style="color:#000000;">)
    at scala.concurrent.impl.CallbackRunnable.executeWithValue(Promise.scala:</span><span style="color:#800080;">40</span><span style="color:#000000;">)
    at scala.concurrent.impl.Promise$DefaultPromise.tryComplete(Promise.scala:</span><span style="color:#800080;">248</span><span style="color:#000000;">)
    at scala.concurrent.Promise$</span><span style="color:#0000ff;">class</span>.complete(Promise.scala:<span style="color:#800080;">55</span><span style="color:#000000;">)
    at scala.concurrent.impl.Promise$DefaultPromise.complete(Promise.scala:</span><span style="color:#800080;">153</span><span style="color:#000000;">)
    at scala.concurrent.Future$$anonfun$map$</span><span style="color:#800080;">1</span>.apply(Future.scala:<span style="color:#800080;">235</span><span style="color:#000000;">)
    at scala.concurrent.Future$$anonfun$map$</span><span style="color:#800080;">1</span>.apply(Future.scala:<span style="color:#800080;">235</span><span style="color:#000000;">)
    at scala.concurrent.impl.CallbackRunnable.run(Promise.scala:</span><span style="color:#800080;">32</span><span style="color:#000000;">)
    at scala.concurrent.Future$InternalCallbackExecutor$Batch$$anonfun$run$</span><span style="color:#800080;">1</span>.processBatch$<span style="color:#800080;">1</span>(Future.scala:<span style="color:#800080;">643</span><span style="color:#000000;">)
    at scala.concurrent.Future$InternalCallbackExecutor$Batch$$anonfun$run$</span><span style="color:#800080;">1</span>.apply$mcV$sp(Future.scala:<span style="color:#800080;">658</span><span style="color:#000000;">)
    at scala.concurrent.Future$InternalCallbackExecutor$Batch$$anonfun$run$</span><span style="color:#800080;">1</span>.apply(Future.scala:<span style="color:#800080;">635</span><span style="color:#000000;">)
    at scala.concurrent.Future$InternalCallbackExecutor$Batch$$anonfun$run$</span><span style="color:#800080;">1</span>.apply(Future.scala:<span style="color:#800080;">635</span><span style="color:#000000;">)
    at scala.concurrent.BlockContext$.withBlockContext(BlockContext.scala:</span><span style="color:#800080;">72</span><span style="color:#000000;">)
    at scala.concurrent.Future$InternalCallbackExecutor$Batch.run(Future.scala:</span><span style="color:#800080;">634</span><span style="color:#000000;">)
    at scala.concurrent.Future$InternalCallbackExecutor$.scala$concurrent$Future$InternalCallbackExecutor$$unbatchedExecute(Future.scala:</span><span style="color:#800080;">694</span><span style="color:#000000;">)
    at scala.concurrent.Future$InternalCallbackExecutor$.execute(Future.scala:</span><span style="color:#800080;">685</span><span style="color:#000000;">)
    at scala.concurrent.impl.CallbackRunnable.executeWithValue(Promise.scala:</span><span style="color:#800080;">40</span><span style="color:#000000;">)
    at scala.concurrent.impl.Promise$DefaultPromise.tryComplete(Promise.scala:</span><span style="color:#800080;">248</span><span style="color:#000000;">)
    at scala.concurrent.Promise$</span><span style="color:#0000ff;">class</span>.tryFailure(Promise.scala:<span style="color:#800080;">112</span><span style="color:#000000;">)
    at scala.concurrent.impl.Promise$DefaultPromise.tryFailure(Promise.scala:</span><span style="color:#800080;">153</span><span style="color:#000000;">)
    at org.apache.spark.rpc.netty.NettyRpcEnv$$anon$</span><span style="color:#800080;">1</span>.run(NettyRpcEnv.scala:<span style="color:#800080;">241</span><span style="color:#000000;">)
    at java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:</span><span style="color:#800080;">471</span><span style="color:#000000;">)
    at java.util.concurrent.FutureTask.run(FutureTask.java:</span><span style="color:#800080;">262</span><span style="color:#000000;">)
    at java.util.concurrent.ScheduledThreadPoolExecutor$ScheduledFutureTask.access$</span><span style="color:#800080;">201</span>(ScheduledThreadPoolExecutor.java:<span style="color:#800080;">178</span><span style="color:#000000;">)
    at java.util.concurrent.ScheduledThreadPoolExecutor$ScheduledFutureTask.run(ScheduledThreadPoolExecutor.java:</span><span style="color:#800080;">292</span><span style="color:#000000;">)
    at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:</span><span style="color:#800080;">1145</span><span style="color:#000000;">)
    at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:</span><span style="color:#800080;">615</span><span style="color:#000000;">)
    at java.lang.Thread.run(Thread.java:</span><span style="color:#800080;">745</span><span style="color:#000000;">)
Caused by: java.util.concurrent.TimeoutException: Cannot receive any reply </span><span style="color:#0000ff;">in</span> <span style="color:#800080;">120</span><span style="color:#000000;"> seconds
    at org.apache.spark.rpc.netty.NettyRpcEnv$$anon$</span><span style="color:#800080;">1</span>.run(NettyRpcEnv.scala:<span style="color:#800080;">242</span><span style="color:#000000;">)
    ... </span><span style="color:#800080;">7</span> more</pre>
</div>
<br>            </div>
                </div>