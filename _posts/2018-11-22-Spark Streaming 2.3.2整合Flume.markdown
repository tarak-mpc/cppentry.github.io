---
layout:     post
title:      Spark Streaming 2.3.2整合Flume
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：我这里没啥原创的，都是找大佬们或者官方文档学习的，和大家分享，谢谢					https://blog.csdn.net/qq_33396608/article/details/83410009				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p><strong>导读</strong>：在Spark Streaming整合Flume文档中，官方提供两种方式，push和pull</p>
<p>Flume是Spark Streaming的高级数据源之一<a href="http://spark.apache.org/docs/latest/streaming-programming-guide.html#advanced-sources" rel="nofollow">直达车</a><br>
Spark Streaming整合Flume官方文档<a href="http://spark.apache.org/docs/latest/streaming-flume-integration.html" rel="nofollow">直达车</a><br>
如果你对Flume不熟悉，这里是我记录的Flume的基本教程<a href="https://www.bblog.vip/label/27" rel="nofollow">直达车</a>，欢迎到访<br>
该文实例代码我的码云<a href="https://gitee.com/yy1122/spark-streaming" rel="nofollow">直达车</a></p>
<p></p><div class="toc"><h3>文章目录</h3><ul><ul><li><a href="#_8" rel="nofollow">一、概述</a></li><li><a href="#Flumestyle_Pushbased_Approach_14" rel="nofollow">二、Flume-style Push-based Approach</a></li><li><a href="#Pullbased_Approach_using_a_Custom_Sink_117" rel="nofollow">三、Pull-based Approach using a Custom Sink</a></li></ul></ul></div><p></p>
<h2><a id="_8"></a>一、概述</h2>
<p>Apache Flume是一种分布式，可靠且可用的服务，用于高效收集，聚合和移动大量日志数据。在这里，我们将解释如何配置Flume和Spark Streaming以从Flume接收数据。这有两种方法。</p>
<p><strong>注意：从Spark 2.3.0开始，不推荐使用Flume支持。</strong></p>
<h2><a id="Flumestyle_Pushbased_Approach_14"></a>二、Flume-style Push-based Approach</h2>
<p><a href="http://spark.apache.org/docs/latest/streaming-flume-integration.html#approach-1-flume-style-push-based-approach" rel="nofollow">直达车</a><br>
Flume旨在推动Flume agent之间的数据。在这种方法中，Spark Streaming基本上设置了一个接收器，它作为Flume的Avro代理，Flume可以将数据推送过来。</p>
<p>由于是<strong>推送模型</strong>，<strong>Spark Streaming应用程序需要先启动</strong>，接收器在所选端口上进行调度和监听，以便Flume能够推送数据。</p>
<p><strong>本地开发测试</strong></p>
<p>1）conf配置，<a href="http://spark.apache.org/docs/latest/streaming-flume-integration.html#configuring-flume" rel="nofollow">直达车</a>，这里我配置的用户本地测试的，后面会提到如何跑在Spark上</p>
<p><code>cd $FLUME_HOME/conf</code>  编辑  <code>vim flume-push-streaming.conf</code></p>
<pre><code>flume-push-streaming.sources  =  netcat-source
flume-push-streaming.sinks  =  avro-sink
flume-push-streaming.channels  =  memory-channel

flume-push-streaming.sources.netcat-source.type  =  netcat
flume-push-streaming.sources.netcat-source.bind = hadoop000
flume-push-streaming.sources.netcat-source.port = 44444

flume-push-streaming.sinks.avro-sink.type  =  avro
flume-push-streaming.sinks.avro-sink.hostname  =  192.168.31.31
flume-push-streaming.sinks.avro-sink.port  =  44445

flume-push-streaming.channels.memory-channel.type  =  memory

flume-push-streaming.sources.netcat-source.channels  =  memory-channel
flume-push-streaming.sinks.avro-sink.channel  =  memory-channel
</code></pre>
<p><strong>注意</strong>：hadoop000是我linux系统的hostname，192.168.31.31是我windows的ip</p>
<p>2）代码，<a href="http://spark.apache.org/docs/latest/streaming-flume-integration.html#configuring-spark-streaming-application" rel="nofollow">直达车</a></p>
<pre><code>object FlumePushWordCountTest {

  def main(args: Array[String]): Unit = {

    if (args.length != 2) {
      System.err.println("Usage: FlumePushWordCountTest &lt;hostname&gt; &lt;port&gt;")
      System.exit(1)
    }

    val Array(hostname, port) = args

    val sparkConf = new SparkConf().setMaster("local[2]").setAppName("FlumePushWordCountTest")
    val ssc = new StreamingContext(sparkConf, Seconds(5))
    ssc.sparkContext.setLogLevel("ERROR")

    val flumeStream = FlumeUtils.createStream(ssc, hostname, port.toInt)

    flumeStream.map(x =&gt; new String(x.event.getBody.array()).trim)
      .flatMap(_.split(" "))
      .map((_, 1))
      .reduceByKey(_ + _)
      .print()

    ssc.start()
    ssc.awaitTermination()
  }
}
</code></pre>
<p>3）本地运行Spark Streaming，运行参数为：<code>0.0.0.0</code> 和 <code>44445</code>，因为上面的conf中配置了sink到我的windows的44445端口<br>
4）之后linux服务器运行Flume，如果你对Flume不熟悉，这里是我记录的Flume的基本教程<a href="https://www.bblog.vip/label/27" rel="nofollow">直达车</a>，欢迎到访</p>
<pre><code>flume-ng agent \
--name flume-push-streaming \
--conf $FLUME_HOME/conf \
--conf-file $FLUME_HOME/conf/flume-push-streaming.conf \
-Dflume.root.logger=INFO,console
</code></pre>
<p>5）启动   <code>telnet hadoop000 44444</code>    输入数据，查看结果<br>
<img src="https://triedblog.oss-cn-beijing.aliyuncs.com/web/2018-10-25/flume-push-streamin.png" alt="在这里插入图片描述"></p>
<p><strong>服务器环境</strong></p>
<p>1）部署到服务器环境运行（先运行jar包，再运行flume，和上面的本地操作流程一样）</p>
<p><strong>前提</strong>：上面是本地测试的，sink到的是我windows的ip。所以上面的conf文件中sink得改一下</p>
<pre><code>flume-push-streaming.sinks.avro-sink.hostname  =  hadoop000
</code></pre>
<p>2）打包，<code>mvn clean package -DskipTests</code></p>
<p>3）上传jar包到服务器</p>
<p>4）运行jar包命令</p>
<pre><code>./bin/spark-submit \
--class com.imooc.spark.streaming.flume.FlumePushWordCountTest \
--name FlumePushWordCountTest \
--master local[2] \
--packages org.apache.spark:spark-streaming-flume_2.11:2.3.2 \
/root/lib/spark-sql-1.0-jar-with-dependencies.jar \
hadoop000 44445
</code></pre>
<p>由于上面打包依赖没有打进去，这里指定一下(–packages)就可以了，运行的时候会自动帮你下载依赖，注意联网。<strong>注意jar包路径</strong><br>
提示：使用 <code>maven-assembly-plugin</code>  插件可以把自己想要的包打进去。</p>
<h2><a id="Pullbased_Approach_using_a_Custom_Sink_117"></a>三、Pull-based Approach using a Custom Sink</h2>
<p><a href="http://spark.apache.org/docs/latest/streaming-flume-integration.html#approach-2-pull-based-approach-using-a-custom-sink" rel="nofollow">直达车</a><br>
和第一种不一样，Flume不将数据直接推送到Spark Streaming。</p>
<p>而是</p>
<p>Flume将数据推入接收器，数据保持缓冲 ，Spark Streaming使用可靠的Flume接收器 和事务从接收器中提取数据。只有在Spark Streaming接收和复制数据后，事务才会成功 。（我们自己取拿数据处理）</p>
<p>和第一种push相比，这一种具有更强的可靠性和容错性</p>
<p>1）conf配置，<a href="http://spark.apache.org/docs/latest/streaming-flume-integration.html#configuring-flume-1" rel="nofollow">直达车</a>，这里我配置的用户本地测试的，如何跑在Spark上和第一种方式一模一样</p>
<p><code>cd $FLUME_HOME/conf</code>   <code>vim flume-pull-streaming.conf</code></p>
<pre><code>flume-pull-streaming.sources  =  netcat-source
flume-pull-streaming.sinks  =  spark-sink
flume-pull-streaming.channels  =  memory-channel

flume-pull-streaming.sources.netcat-source.type  =  netcat
flume-pull-streaming.sources.netcat-source.bind = hadoop000
flume-pull-streaming.sources.netcat-source.port = 44444

flume-pull-streaming.sinks.spark-sink.type  =  org.apache.spark.streaming.flume.sink.SparkSink
flume-pull-streaming.sinks.spark-sink.hostname  =  hadoop000
flume-pull-streaming.sinks.spark-sink.port  =  44445

flume-pull-streaming.channels.memory-channel.type  =  memory

flume-pull-streaming.sources.netcat-source.channels  =  memory-channel
flume-pull-streaming.sinks.spark-sink.channel  =  memory-channel
</code></pre>
<p><strong>注意</strong>：hadoop000是我linux的hostname</p>
<p>2）代码，<a href="http://spark.apache.org/docs/latest/streaming-flume-integration.html#configuring-spark-streaming-application-1" rel="nofollow">直达车</a></p>
<pre><code>object FlumePullWordCountTest {

  def main(args: Array[String]): Unit = {

    if (args.length != 2) {
      System.err.println("Usage: FlumePushWordCountTest &lt;hostname&gt; &lt;port&gt;")
      System.exit(1)
    }

    val Array(hostname, port) = args

    val sparkConf = new SparkConf().setMaster("local[2]").setAppName("FlumePullWordCountTest")
    val ssc = new StreamingContext(sparkConf, Seconds(5))
    ssc.sparkContext.setLogLevel("ERROR")

    val flumeStream = FlumeUtils.createPollingStream(ssc, hostname, port.toInt)

    flumeStream.map(x =&gt; new String(x.event.getBody.array()).trim)
      .flatMap(_.split(" "))
      .map((_, 1))
      .reduceByKey(_ + _)
      .print()

    ssc.start()
    ssc.awaitTermination()
  }
}
</code></pre>
<p>3）<strong>先启动flume，后启动spark streaming应用</strong></p>
<p>4）运行flume</p>
<pre><code>flume-ng agent \
--name flume-pull-streaming \
--conf $FLUME_HOME/conf \
--conf-file $FLUME_HOME/conf/flume-pull-streaming.conf \
-Dflume.root.logger=INFO,console
</code></pre>
<p>5）本地运行Spark Streaming，运行参数为  <code>192.168.31.30</code>  <code>44445</code> ，分别是我linux的ip和端口</p>
<p>6）启动  <code>telnet hadoop000 44444</code>   输入数据，查看结果<br>
<img src="https://triedblog.oss-cn-beijing.aliyuncs.com/web/2018-10-26/flume-pull-streaming.png" alt="在这里插入图片描述"></p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>