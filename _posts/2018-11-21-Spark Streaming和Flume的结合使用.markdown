---
layout:     post
title:      Spark Streaming和Flume的结合使用
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div style="line-height:1.875;font-size:14px;">首先在IDEA里面导入依赖包</div>
<div style="line-height:1.875;font-size:14px;">&lt;dependency&gt;</div>
<div style="line-height:1.875;font-size:14px;">&lt;groupId&gt;org.apache.spark&lt;/groupId&gt;</div>
<div style="line-height:1.875;font-size:14px;">&lt;artifactId&gt;spark-streaming-flume_2.10&lt;/artifactId&gt;<br></div>
<div style="line-height:1.875;font-size:14px;">&lt;version&gt;${spark.version}&lt;/version&gt;</div>
<div style="line-height:1.875;font-size:14px;">&lt;/dependency&gt;</div>
<div style="line-height:1.875;font-size:14px;"><br></div>
<div style="line-height:1.875;font-size:14px;">在linux下安装flume，减压flume包，然后到conf里面复制flume-env.sh，修改里面的JavaHOME安装目录就好了</div>
<div style="line-height:1.875;">
<div style="line-height:1.875;"><strong><span style="font-size:18px;">1、 Flume主动向Streaming推送数据</span></strong></div>
<div style="line-height:1.875;"><pre><code class="language-plain">object FlumePushDemo {
  def main(args: Array[String]): Unit = {
    Logger.getLogger("org").setLevel(Level.WARN)
    //local[2]这里必须是2个或2个以上的线程，一个负责接收数据，一个负责将接收的数据下发到worker上执行
    val config = new SparkConf().setAppName("FlumePushDemo").setMaster("local[2]")
    val sc = new SparkContext(config)
    val ssc = new StreamingContext(sc, Seconds(2))
    //这个地址是spark程序启动时所在节点的地址
    val flumeStream = FlumeUtils.createStream(ssc, "192.168.10.11", 8008)
    flumeStream.flatMap(x =&gt; new String(x.event.getBody.array()).split(" ")).map((_, 1)).reduceByKey(_ + _)
      .print()
    ssc.start()
    ssc.awaitTermination()
  }
}</code></pre><br></div>
<div style="line-height:1.875;"><span style="font-size:18px;">配置flume文件</span></div>
<div style="line-height:1.875;"><pre><code class="language-plain"><span style="font-size:14px;"># 这个是启动命令，到flume的安装路径
# bin/flume-ng agent -n a1 -c conf/ -f config/flume-push.conf  -Dflume.root.logger=INFO,console
# flume 主动推送数据到spark上
# Name the components on this agent
a1.sources = r1
a1.sinks = k1 k2
a1.channels = c1

# source
a1.sources.r1.type = exec
# 监控linux目录下的文件
a1.sources.r1.command = tail -F /home/hadoop/access.log
a1.sources.r1.channels = c1

# Describe the sink
# avro绑定一个端口
a1.sinks.k1.type = avro
a1.sinks.k1.hostname = 192.168.10.11
a1.sinks.k1.port = 8008
#在控制台打印信息
a1.sinks.k2.type = logger

# Use a channel which buffers events in memory
a1.channels.c1.type = memory
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 100

# Bind the source and sink to the channel
a1.sources.r1.channels = c1
a1.sinks.k1.channel = c1
a1.sinks.k2.channel = c1</span></code></pre><span style="font-size:18px;"><br></span><span style="font-size:14px;">我去，弄了好几次了Flume配置文件开头总是显示<span style="font-family:Consolas, 'Courier New', Courier, mono, serif;">&lt;span style="font-size:14px;"&gt;，结尾显示<span style="font-family:Consolas, 'Courier New', Courier, mono, serif;">&lt;/span&gt;,大家在使用的时候注意，把这些去掉。</span></span></span></div>
<div style="line-height:1.875;"><span style="font-size:18px;"><span style="font-family:Consolas, 'Courier New', Courier, mono, serif;font-size:13.3333px;"></span></span></div>
<div style="line-height:1.875;"><span style="font-size:18px;">2、Streaming主动向Flume拉取数据（这个要优于上面的，可以根据处理数据的能力去拉取数据）<br></span><span style="font-size:14px;">第一，拷贝三个jar包放到flume的lib目录下<br>
spark-streaming-flume-sink_2.10-1.6.1.jar<br>
scala-library-2.10.5.jar<br>
commons-lang3-3.3.2.jar<br>
第二，使用创建FlumeUtils.createPollingStream 的dstream</span></div>
<div style="line-height:1.875;"><span style="font-size:14px;"></span><pre><code class="language-plain">object FlumePullDemo {
  def main(args: Array[String]): Unit = {
    Logger.getLogger("org").setLevel(Level.WARN)
    //local[2]这里必须是2个或2个以上的线程，一个负责接收数据，一个负责将接收的数据下发到worker上执行
    val config = new SparkConf().setAppName("FlumePullDemo").setMaster("local[2]")
    val sc = new SparkContext(config)
    val ssc = new StreamingContext(sc, Seconds(2))
    //这个地址是spark程序启动时所在节点的地址，后面可以添加多个地址
    val addresses: Seq[InetSocketAddress] = Seq(new InetSocketAddress("192.168.10.11", 8008))
    val flumeStream = FlumeUtils.createPollingStream(ssc, addresses, StorageLevel.MEMORY_ONLY)
    flumeStream.flatMap(x =&gt; new String(x.event.getBody.array()).split(" ")).map((_, 1)).reduceByKey(_ + _)
      .print()
    ssc.start()
    ssc.awaitTermination()
  }
}</code></pre><br><span style="font-size:18px;">配置flume文件</span></div>
<div style="line-height:1.875;"><pre><code class="language-plain"><span style="font-size:14px;"># 执行代码
# bin/flume-ng agent -n a1 -c conf/ -f config/flume-pull.conf  -Dflume.root.logger=INFO,console
# spark 主动到flume上拉取数据
# Name the components on this agent
a1.sources = r1
a1.sinks = k1 k2
a1.channels = c1

# source
a1.sources.r1.type = exec
a1.sources.r1.command = tail -F /home/hadoop/access.log
a1.sources.r1.channels = c1

# Describe the sink
# 告诉flume下沉到spark编写好的组件中
a1.sinks.k1.type = org.apache.spark.streaming.flume.sink.SparkSink
a1.sinks.k1.hostname = 192.168.10.11
a1.sinks.k1.port = 8008
# 控制台打印数据信息
a1.sinks.k2.type = logger

# Use a channel which buffers events in memory
a1.channels.c1.type = memory
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 1000

# Bind the source and sink to the channel
a1.sources.r1.channels = c1
a1.sinks.k1.channel = c1
a1.sinks.k2.channel = c1</span></code></pre><span style="font-size:18px;"><br></span></div>
</div>
            </div>
                </div>