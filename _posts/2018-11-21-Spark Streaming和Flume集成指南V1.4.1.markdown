---
layout:     post
title:      Spark Streaming和Flume集成指南V1.4.1
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-size:18px;"><span></span>Apache Flume是一个用来有效地收集，聚集和移动大量日志数据的分布式的，有效的服务。这里我们解释一下怎样配置Flume和Spark Streaming来从Flume获取数据。这里有两个方法。</span></p>
<p><span style="font-size:18px;"><span></span>Python API：Flume现在还不支持PythonAPI</span></p>
<p> </p>
<p><strong><span style="font-size:24px;">方法1：Flume风格的推方法</span></strong></p>
<p><span style="font-size:18px;"><span></span>Flume被设计用来在Flume代理之间推送数据。在这种方法中，Spark Streaming本质上设置了一个接收器作为Flume的一个Avro代理，Flume把数据推送到接收器上。下面是配置的步骤。</span></p>
<p> </p>
<p><strong><span style="font-size:18px;">一般需求</span></strong></p>
<p><span style="font-size:14px;">在你的集群中选择一台机器满足如下条件：</span></p>
<p><span style="font-size:14px;">1.      当你的Flume+Spark Streaming程序启动之后，Spark节点之一必须运行在那台机器上。</span></p>
<p><span style="font-size:14px;">2.      Flume可以配置为推送数据到那台机器的端口上。</span></p>
<p><span style="font-size:14px;">根据推模型，流程序需要启动，同时接收器按计划运行并监听选择的端口，以让Flume能够推送数据。</span></p>
<p> </p>
<p><strong><span style="font-size:18px;">配置Flume</span></strong></p>
<p><span style="font-size:18px;">配置Flume代理来发送数据到一个Avro Sink，需要在配置文件中加入如下的内容。</span></p>
<p></p>
<pre><code class="language-html">agent.sinks = avroSink
agent.sinks.avroSink.type = avro
agent.sinks.avroSink.channel = memoryChannel
agent.sinks.avroSink.hostname = &lt;chosen machine's hostname&gt;
agent.sinks.avroSink.port = &lt;chosen port on the machine&gt;</code></pre>
<p></p>
<p><span style="font-size:18px;">查看Flume文档来获取更多关于配置Flume代理的信息。</span></p>
<p><span style="font-size:18px;"><br></span></p>
<p><strong><span style="font-size:18px;">配置Spark Streaming程序</span></strong></p>
<p><span style="font-size:18px;">1.<strong>连接</strong>：在你的SBT/Maven项目定义中，通过下面的内容连接你的流程序（在主编程指南中的连接章节寻找更多的信息）。</span></p>
<p></p>
<pre><code class="language-html">groupId = org.apache.spark
artifactId = spark-streaming-flume_2.10
version = 1.4.1</code></pre>
<p></p>
<p><span style="font-size:18px;"><strong>2.编程</strong>：在流处理程序的代码中，引入FlumeUtils并如下创建一个输入DStream流。</span></p>
<p><strong><span style="font-size:18px;">Scala</span></strong></p>
<p></p>
<pre><code class="language-java">importorg.apache.spark.streaming.flume._
val flumeStream = FlumeUtils.createStream(streamingContext, [chosenmachine's hostname], [chosen port])</code></pre>
<p></p>
<p><strong><span style="font-size:18px;">Java</span></strong></p>
<p></p>
<pre><code class="language-java">import org.apache.spark.streaming.flume.*;
JavaReceiverInputDStream&lt;SparkFlumeEvent&gt; flumeStream =
        FlumeUtils.createStream(streamingContext, [chosen machine'shostname], [chosen port]);</code></pre>
<p></p>
<p><span style="font-size:18px;">查看API文档和示例。</span></p>
<p><span style="font-size:18px;">注意，这里的主机名应该和集群中的资源管理器使用的主机名相同（Mesos，YARN或Spark Standalone），这样资源分配可以匹配名字并在正确的机器上启动接收器。</span></p>
<p><span style="font-size:18px;">3.<strong>部署</strong>：将spark-streaming-flume_2.10包和它的依赖（除了由spark-submit提供的spark-core_2.10和spark-streaming_2.10）添加到程序包中。然后使用spark-submit来启动你的应用程序（在主编程指南中查看部署章节）。</span></p>
<p> </p>
<p><strong><span style="font-size:24px;">方法2：使用自定义Sink的拉方式</span></strong></p>
<p><span style="font-size:18px;">不是Flume直接推送数据到SparkStreaming，这种方法运行了一个如下所示的Flume Sink。</span></p>
<p><span style="font-size:18px;">1.      Flume将数据推送到Sink中，然后数据在此处缓存。</span></p>
<p><span style="font-size:18px;">2.      Spark Streaming使用一个可靠的Flume接收器和操作从Sink中拉取数据。只有在Spark Streaming接收到数据并且把数据复制后才认为操作成功。</span></p>
<p><span style="font-size:18px;">这个方法比前面的方法提供了强大的可靠性和容错保证。然而，这需要配置Flume运行一个自定义Sink。下面是配置步骤。</span></p>
<p> </p>
<p><strong><span style="font-size:18px;">一般需求</span></strong></p>
<p><span style="font-size:18px;">选择一台在Flume代理中运行自定义Sink的机器。Flume其余的管道被配置为向那个代理发送数据。Spark集群中的机器都能连接到运行自定义Sink的那台机器上。</span></p>
<p> </p>
<p><strong><span style="font-size:18px;">配置Flume</span></strong></p>
<p><span style="font-size:18px;">在选定的机器上配置Flume需要如下的两步。</span></p>
<p><span style="font-size:18px;"><strong>1.Sink JAR包</strong>：添加如下的JAR包到要运行自定义Sink的机器中的Flume的classpath中（查看Flume的文档https://flume.apache.org/documentation.html）。</span></p>
<p><span style="font-size:18px;">         (i)自定义Sink JAR包：下载与下面内容一致的JAR包（或直接下载的地址<a href="https://repo1.maven.org/maven2/org/apache/spark/spark-streaming-flume-sink_2.10/1.4.1/spark-streaming-flume-sink_2.10-1.4.1.jar" rel="nofollow">https://repo1.maven.org/maven2/org/apache/spark/spark-streaming-flume-sink_2.10/1.4.1/spark-streaming-flume-sink_2.10-1.4.1.jar</a>）</span></p>
<p></p>
<pre><code class="language-html">        groupId= org.apache.spark
        artifactId =spark-streaming-flume-sink_2.10
        version = 1.4.1</code></pre>
<p></p>
<p><span style="font-size:18px;">         (ii)Scala库JAR包：下载Scala库2.10.4版本JAR包。它可以用下面的内容找到（或直接在这里下载<a href="https://repo1.maven.org/maven2/org/scala-lang/scala-library/2.10.4/scala-library-2.10.4.jar" rel="nofollow">https://repo1.maven.org/maven2/org/scala-lang/scala-library/2.10.4/scala-library-2.10.4.jar</a>）</span></p>
<p></p>
<pre><code class="language-html">        groupId= org.scala-lang
        artifactId = scala-library
        version = 2.10.4</code></pre>
<p></p>
<p><span style="font-size:18px;">         (iii)CommonsLang3 JAR包：下载Commons Lang 3 JAR包。它可以用下面的内容找到（或者直接下载<a href="https://repo1.maven.org/maven2/org/apache/commons/commons-lang3/3.3.2/commons-lang3-3.3.2.jar" rel="nofollow">https://repo1.maven.org/maven2/org/apache/commons/commons-lang3/3.3.2/commons-lang3-3.3.2.jar</a>）</span></p>
<p></p>
<pre><code class="language-html">        groupId= org.apache.commons
        artifactId = commons-lang3
        version = 3.3.2</code></pre>
<p></p>
<p><span style="font-size:18px;"><strong>2.配置文件</strong>：在那台机器上，通过下面的配置文件配置Flume代理发送数据到一个Avro Sink中。</span></p>
<p></p>
<pre><code class="language-html"> agent.sinks = spark
 agent.sinks.spark.type =org.apache.spark.streaming.flume.sink.SparkSink
 agent.sinks.spark.hostname = &lt;hostname ofthe local machine&gt;
 agent.sinks.spark.port = &lt;port to listen onfor connection from Spark&gt;
 agent.sinks.spark.channel = memoryChannel</code></pre>
<p></p>
<p><span style="font-size:18px;">也要确保上行流的Flume管道配置了发送数据到运行这个Sink的Flume代理。</span></p>
<p><span style="font-size:18px;">查看Flume文档寻找更多关于配置Flume代理的信息。</span></p>
<p> </p>
<p><strong><span style="font-size:18px;">配置Spark Streaming程序</span></strong></p>
<p><span style="font-size:18px;">1.      <strong>连接</strong>：在你的SBT/Maven项目定义中，通过下面的内容连接你的流程序（在主编程指南中的连接章节寻找更多的信息<a href="http://spark.apache.org/docs/latest/streaming-programming-guide.html#linking" rel="nofollow">http://spark.apache.org/docs/latest/streaming-programming-guide.html#linking</a>）</span></p>
<p><span style="font-size:18px;">2.      <strong>编程：</strong>在流处理程序的代码中，引入FlumeUtils并如下创建一个输入DStream流。</span></p>
<p><strong><span style="font-size:18px;">Scala</span></strong></p>
<p></p>
<pre><code class="language-java">importorg.apache.spark.streaming.flume._
 
val flumeStream =FlumeUtils.createPollingStream(streamingContext, [sink machine hostname], [sinkport])</code></pre>
<p></p>
<p><br></p>
<p><strong><span style="font-size:18px;">Java</span></strong></p>
<p></p>
<pre><code class="language-java">importorg.apache.spark.streaming.flume.*;
 
JavaReceiverInputDStream&lt;SparkFlumeEvent&gt;flumeStream=
    FlumeUtils.createPollingStream(streamingContext, [sink machinehostname], [sink port]);</code></pre>
<p></p>
<p><span style="font-size:18px;">查看Scala示例<a href="https://github.com/apache/spark/tree/master/examples/src/main/scala/org/apache/spark/examples/streaming/FlumePollingEventCount.scala" rel="nofollow">https://github.com/apache/spark/tree/master/examples/src/main/scala/org/apache/spark/examples/streaming/FlumePollingEventCount.scala</a></span></p>
<p><span style="font-size:18px;">注意每个输入DStream可以配置为从多个Sink中接收数据。</span></p>
<p><span style="font-size:18px;">3.      <strong>部署</strong>：将spark-streaming-flume_2.10包和它的依赖（除了由spark-submit提供的spark-core_2.10和spark-streaming_2.10）添加到程序包中。然后使用spark-submit来启动你的应用程序（在主编程指南中查看部署章节http://spark.apache.org/docs/latest/streaming-programming-guide.html#deploying-applications）。</span></p>
            </div>
                </div>