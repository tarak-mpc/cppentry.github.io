---
layout:     post
title:      Spark Streaming和Flume集成指南V1.4.1
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div align="left"><span>Apache Flume是一个用来有效地收集，聚集和移动大量日志数据的分布式的，可获得的服务。这里我们解释一下怎样配置Flume和Spark Streaming来从Flume获取数据。</span></div>
<div align="left"><span>这里有两个方法。</span></div>
<p><span><span></span><a class="infotextkey" href="http://www.07net01.com/tags-python-0.html" rel="nofollow">python</a> API：Flume现在还不支持PythonAPI</span></p>
<p> </p>
<p><strong><span>方法1：Flume风格的推方法</span></strong></p>
<p><span><span></span>Flume被设计用来在Flume代理之间推送数据。在这种方法中，Spark Streaming本质上设置了一个接收器作为Flume的一个Avro代理，Flume把数据推送到接收器上。下面是配置的步骤。</span></p>
<p> </p>
<p><strong><span>一般需求</span></strong></p>
<p><span>在你的集群中选择一台机器满足如下条件：</span></p>
<p><span>1.      当你的Flume+Spark Streaming<a class="infotextkey" href="http://www.07net01.com/tags-%E7%A8%8B%E5%BA%8F-0.html" rel="nofollow">程序</a>启动之后，Spark节点之一必须运行在那台机器上。</span></p>
<p><span>2.      Flume可以配置为推送数据到那台机器的端口上。</span></p>
<p><span>根据推<a class="infotextkey" href="http://www.07net01.com/tags-%E6%A8%A1%E5%9E%8B-0.html" rel="nofollow">模型</a>，流程序需要启动，同时接收器按计划运行并监听选择的端口，以让Flume能够推送数据。</span></p>
<p> </p>
<p><strong><span>配置Flume</span></strong></p>
<p><span>配置Flume代理来发送数据到一个Avro池，需要在<a class="infotextkey" href="http://www.07net01.com/tags-%E9%85%8D%E7%BD%AE%E6%96%87%E4%BB%B6-0.html" rel="nofollow">配置文件</a>中加入如下的内容。</span></p>
<p></p>
<p>agent.sinks = avroSink agent.sinks.avroSink.type = avro agent.sinks.avroSink.channel = memoryChannel agent.sinks.avroSink.hostname = &lt;chosen machine's hostname&gt; agent.sinks.avroSink.port = &lt;chosen port on the machine&gt;</p>
<pre></pre>
<p></p>
<p><span>查看Flume文档来获取更多关于配置Flume代理的信息。</span></p>
<p><span></span></p>
<p><strong><span>配置Spark Streaming程序</span></strong></p>
<p><span>1.<strong>连接</strong>：在你的SBT/Maven<a class="infotextkey" href="http://www.07net01.com/tags-%E9%A1%B9%E7%9B%AE-0.html" rel="nofollow">项目</a>定义中，通过下面的内容连接你的流程序（在主<a class="infotextkey" href="http://www.07net01.com/" rel="nofollow">编程</a>指南中的连接章节寻找更多的信息）。</span></p>
<p></p>
<p>groupId = org.apache.spark artifactId = spark-streaming-flume_2.10 <a class="infotextkey" href="http://www.07net01.com/tags-version-0.html" rel="nofollow">
version</a> = 1.4.1</p>
<pre></pre>
<p></p>
<p><span><strong>2.编程</strong>：在流处理程序的代码中，引入FlumeUtils并如下创建一个输入DStream流。</span></p>
<p><strong><span>Scala</span></strong></p>
<p></p>
<p>importorg.apache.spark.streaming.flume._ val flumeStream = FlumeUtils.createStream(streamingContext, [chosenmachine's hostname], [chosen port])</p>
<pre></pre>
<p></p>
<p><strong><span><a class="infotextkey" href="http://www.07net01.com/tags-Java-0.html" rel="nofollow">Java</a></span></strong></p>
<p></p>
<p>import org.apache.spark.streaming.flume.*; JavaReceiverInputDStream&lt;SparkFlumeEvent&gt; flumeStream = FlumeUtils.createStream(streamingContext, [chosen machine'shostname], [chosen port]);</p>
<pre></pre>
<p></p>
<p><span>查看API文档和示例。</span></p>
<p><span>注意，这里的主机名应该和集群中的<a class="infotextkey" href="http://www.07net01.com/tags-%E8%B5%84%E6%BA%90%E7%AE%A1%E7%90%86%E5%99%A8-0.html" rel="nofollow">资源管理器</a>使用的主机名相同（Mesos，YARN或Spark Standalone），这样资源分配可以匹配名字并在正确的机器上启动接收器。</span></p>
<p><span>3.<strong>部署</strong>：将spark-streaming-flume_2.10包和它的依赖（除了由spark-submit提供的spark-core_2.10和spark-streaming_2.10）添加到程序包中。然后使用spark-submit来启动你的<a class="infotextkey" href="http://www.07net01.com/tags-%E5%BA%94%E7%94%A8%E7%A8%8B%E5%BA%8F-0.html" rel="nofollow">应用程序</a>（在主编程指南中查看部署章节）。</span></p>
<p> </p>
<p><strong><span>方法2：使用自定义池的拉方式</span></strong></p>
<p><span>不是Flume直接推送数据到SparkStreaming，这种方法运行了一个如下所示的Flume池。</span></p>
<p><span>1.      Flume将数据推送到池中，然后数据在此处缓存。</span></p>
<p><span>2.      Spark Streaming使用一个可靠的Flume接收器和操作从池中拉取数据。只有在Spark Streaming接收到数据并且把数据复制后才认为操作成功。</span></p>
<p><span>这个方法比前面的方法提供了强大的可靠性和容错保证。然而，这需要配置Flume运行一个自定义池。下面是配置步骤。</span></p>
<p> </p>
<p><strong><span>一般需求</span></strong></p>
<p><span>选择一台在Flume代理中运行自定义池的机器。Flume其余的管道被配置为向那个代理发送数据。Spark集群中的机器都能连接到运行自定义池的那台机器上。</span></p>
<p> </p>
<p><strong><span>配置Flume</span></strong></p>
<p><span>在选定的机器上配置Flume需要如下的两步。</span></p>
<p><span><strong>1.池JAR包</strong>：添加如下的JAR包到要运行自定义池的机器中的Flume的classpath中（查看Flume的文档https://flume.apache.org/documentation.html）。</span></p>
<p><span>         (i)自定义池JAR包：下载与下面内容一致的JAR包（或直接下载的地址https://repo1.maven.org/maven2/org/apache/spark/spark-streaming-flume-sink_2.10/1.4.1/spark-streaming-flume-sink_2.10-1.4.1.jar）</span></p>
<p></p>
<p>groupId= org.apache.spark artifactId =spark-streaming-flume-sink_2.10 version = 1.4.1</p>
<pre></pre>
<p></p>
<p><span>         (ii)Scala库JAR包：下载Scala库2.10.4版本JAR包。它可以用下面的内容找到（或直接在这里下载https://repo1.maven.org/maven2/org/scala-lang/scala-library/2.10.4/scala-library-2.10.4.jar）</span></p>
<p></p>
<p>groupId= org.scala-lang artifactId = scala-library version = 2.10.4</p>
<pre></pre>
<p></p>
<p><span>         (iii)CommonsLang3 JAR包：下载Commons Lang 3 JAR包。它可以用下面的内容找到（或者直接下载https://repo1.maven.org/maven2/org/apache/commons/commons-lang3/3.3.2/commons-lang3-3.3.2.jar）</span></p>
<p></p>
<p>groupId= org.apache.commons artifactId = commons-lang3 version = 3.3.2</p>
<pre></pre>
<p></p>
<p><span><strong>2.配置文件</strong>：在那台机器上，通过下面的配置文件配置Flume代理发送数据到一个Avro池中。</span></p>
<p></p>
<p>agent.sinks = spark agent.sinks.spark.type =org.apache.spark.streaming.flume.sink.SparkSink agent.sinks.spark.hostname = &lt;hostname ofthe local machine&gt; agent.sinks.spark.port = &lt;port to listen onfor connection from Spark&gt; agent.sinks.spark.channel = memoryChannel</p>
<pre></pre>
<p></p>
<p><span>也要确保上行流的Flume管道配置了发送数据到运行这个池的Flume代理。</span></p>
<p><span>查看Flume文档寻找更多关于配置Flume代理的信息。</span></p>
<p> </p>
<p><strong><span>配置Spark Streaming程序</span></strong></p>
<p><span>1.      <strong>连接</strong>：在你的SBT/Maven项目定义中，通过下面的内容连接你的流程序（在主编程指南中的连接章节寻找更多的信息http://spark.apache.org/docs/latest/streaming-programming-guide.html#linking）</span></p>
<p><span>2.      <strong>编程：</strong>在流处理程序的代码中，引入FlumeUtils并如下创建一个输入DStream流。</span></p>
<p><strong><span>Scala</span></strong></p>
<p></p>
<p>importorg.apache.spark.streaming.flume._ val flumeStream =FlumeUtils.createPollingStream(streamingContext, [sink machine hostname], [sinkport])</p>
<pre></pre>
<p></p>
<p></p>
<p><strong><span>Java</span></strong></p>
<p></p>
<p>importorg.apache.spark.streaming.flume.*; JavaReceiverInputDStream&lt;SparkFlumeEvent&gt;flumeStream= FlumeUtils.createPollingStream(streamingContext, [sink machinehostname], [sink port]);</p>
<pre></pre>
<p></p>
<p><span>查看Scala示例https://github.com/apache/spark/tree/master/examples/src/main/scala/org/apache/spark/examples/streaming/FlumePollingEventCount.scala</span></p>
<p><span>注意每个输入DStream可以配置为从多个池中接收数据。</span></p>
<p><span>3.      <strong>部署</strong>：将spark-streaming-flume_2.10包和它的依赖（除了由spark-submit提供的spark-core_2.10和spark-streaming_2.10）添加到程序包中。然后使用spark-submit来启动你的应用程序（在主编程指南中查看部署章节http://spark.apache.org/docs/latest/streaming-programming-guide.html#deploying-applications）。</span></p>
<span class="post-tags">关键词：<a href="http://www.07net01.com/tags-%E9%85%8D%E7%BD%AE%E6%96%87%E4%BB%B6-0.html" rel="nofollow">配置文件</a> <a href="http://www.07net01.com/tags-machine-0.html" rel="nofollow">machine</a> <a href="http://www.07net01.com/tags-%E6%8E%A5%E6%94%B6%E5%99%A8-0.html" rel="nofollow">接收器</a> <a href="http://www.07net01.com/tags-%E7%A8%8B%E5%BA%8F-0.html" rel="nofollow">程序</a> <a href="http://www.07net01.com/tags-%E8%AE%A1%E5%88%92-0.html" rel="nofollow">计划</a>
</span>
            </div>
                </div>