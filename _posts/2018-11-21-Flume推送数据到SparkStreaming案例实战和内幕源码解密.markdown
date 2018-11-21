---
layout:     post
title:      Flume推送数据到SparkStreaming案例实战和内幕源码解密
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/erfucun/article/details/52262416				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>本博文内容主要包含以下几个方面：</p>

<ol>
<li>Flume on HDFS案例回顾</li>
<li>Flume推送数据到Spark Streaming实战</li>
<li>原理绘图剖析</li>
</ol>

<p>一：Flume on HDFS 案例回顾： <br>
文件配置在上篇博文中已经详细介绍，接下来的操作基于已经成功安装完成Flume：</p>

<p>拷贝conf/flume-conf.properties.template，更名为conf/flume-cong.properties，只写以下内容：</p>



<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-preprocessor">#agent1表示代理名称</span>

agent1<span class="hljs-preprocessor">.sources</span>=source1

agent1<span class="hljs-preprocessor">.sinks</span>=sink1

agent1<span class="hljs-preprocessor">.channels</span>=channel1

<span class="hljs-preprocessor">#配置source1</span>

agent1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.source</span>1<span class="hljs-preprocessor">.type</span>=spooldir

agent1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.source</span>1<span class="hljs-preprocessor">.spoolDir</span>=/usr/local/flume/tmp/TestDir

agent1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.source</span>1<span class="hljs-preprocessor">.channels</span>=channel1

agent1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.source</span>1<span class="hljs-preprocessor">.fileHeader</span> = false

agent1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.source</span>1<span class="hljs-preprocessor">.interceptors</span> = i1

agent1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.source</span>1<span class="hljs-preprocessor">.interceptors</span><span class="hljs-preprocessor">.i</span>1<span class="hljs-preprocessor">.type</span> = timestamp

<span class="hljs-preprocessor">#配置sink1</span>

agent1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.sink</span>1<span class="hljs-preprocessor">.type</span>=hdfs

agent1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.sink</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.path</span>=hdfs://master:<span class="hljs-number">9000</span>/library/flume

agent1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.sink</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.fileType</span>=DataStream

agent1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.sink</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.writeFormat</span>=TEXT

agent1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.sink</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.rollInterval</span>=<span class="hljs-number">1</span>

agent1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.sink</span>1<span class="hljs-preprocessor">.channel</span>=channel1

agent1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.sink</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.filePrefix</span>=%<span class="hljs-built_in">Y</span>-%m-%d

<span class="hljs-preprocessor">#agent1.sinks.sink1.type=avro</span>

<span class="hljs-preprocessor">#agent1.sinks.sink1.channel=channel1</span>

<span class="hljs-preprocessor">#agent1.sinks.sink1.hostname=Master</span>

<span class="hljs-preprocessor">#agent1.sinks.sink1.port=9999</span>

<span class="hljs-preprocessor">#配置channel1</span>

agent1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.channel</span>1<span class="hljs-preprocessor">.type</span>=file

agent1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.channel</span>1<span class="hljs-preprocessor">.checkpointDir</span>=/usr/local/flume/tmp/checkpointDir

agent1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.channel</span>1<span class="hljs-preprocessor">.dataDirs</span>=/usr/local/flume/tmp/dataDirs

flume-env<span class="hljs-preprocessor">.sh</span>配置:

<span class="hljs-preprocessor"># export JAVA_HOME=/usr/lib/jvm/java-6-sun</span>

export JAVA_HOME=/usr/local/jdk/jdk1<span class="hljs-number">.8</span><span class="hljs-number">.0</span>_60

<span class="hljs-preprocessor"># Give Flume more memory and pre-allocate, enable remote monitoring via JMX</span>

<span class="hljs-preprocessor"># export JAVA_OPTS="-Xms100m -Xmx2000m -Dcom.sun.management.jmxremote"</span>

export JAVA_OPTS=<span class="hljs-string">"-Xms100m -Xmx2000m -Dcom.sun.management.jmxremote"</span>

建立文件夹 /usr/local/flume/tmp/TestDir。

在hdfs上建立/library/flume文件夹。

flume的bin文件夹下启动Flume:

./flume-ng agent -n agent1 -c conf -f /usr/local/flume/conf/flume-conf<span class="hljs-preprocessor">.properties</span> -Dflume<span class="hljs-preprocessor">.root</span><span class="hljs-preprocessor">.logger</span>=DEBUG,console

在/usr/local/flume/tmp/TestDir下，拷入测试用的文件，比如：NOTICE

flume 控制台会有一些相关信息：

<span class="hljs-number">16</span>/<span class="hljs-number">04</span>/<span class="hljs-number">22</span> <span class="hljs-number">11</span>:<span class="hljs-number">03</span>:<span class="hljs-number">49</span> INFO avro<span class="hljs-preprocessor">.ReliableSpoolingFileEventReader</span>: Preparing to move file /usr/local/flume/tmp/TestDir/NOTICE to /usr/local/flume/tmp/TestDir/NOTICE<span class="hljs-preprocessor">.COMPLETED</span>

<span class="hljs-number">16</span>/<span class="hljs-number">04</span>/<span class="hljs-number">22</span> <span class="hljs-number">11</span>:<span class="hljs-number">03</span>:<span class="hljs-number">51</span> INFO hdfs<span class="hljs-preprocessor">.HDFSDataStream</span>: Serializer = TEXT, UseRawLocalFileSystem = false

<span class="hljs-number">16</span>/<span class="hljs-number">04</span>/<span class="hljs-number">22</span> <span class="hljs-number">11</span>:<span class="hljs-number">03</span>:<span class="hljs-number">51</span> INFO hdfs<span class="hljs-preprocessor">.BucketWriter</span>: Creating hdfs://master:<span class="hljs-number">9000</span>/library/flume/<span class="hljs-number">2016</span>-<span class="hljs-number">04</span>-<span class="hljs-number">22.1461294231806</span><span class="hljs-preprocessor">.tmp</span>

<span class="hljs-number">16</span>/<span class="hljs-number">04</span>/<span class="hljs-number">22</span> <span class="hljs-number">11</span>:<span class="hljs-number">03</span>:<span class="hljs-number">52</span> INFO hdfs<span class="hljs-preprocessor">.BucketWriter</span>: Closing hdfs://master:<span class="hljs-number">9000</span>/library/flume/<span class="hljs-number">2016</span>-<span class="hljs-number">04</span>-<span class="hljs-number">22.1461294231806</span><span class="hljs-preprocessor">.tmp</span>

<span class="hljs-number">16</span>/<span class="hljs-number">04</span>/<span class="hljs-number">22</span> <span class="hljs-number">11</span>:<span class="hljs-number">03</span>:<span class="hljs-number">52</span> INFO hdfs<span class="hljs-preprocessor">.BucketWriter</span>: Renaming hdfs://master:<span class="hljs-number">9000</span>/library/flume/<span class="hljs-number">2016</span>-<span class="hljs-number">04</span>-<span class="hljs-number">22.1461294231806</span><span class="hljs-preprocessor">.tmp</span> to hdfs://master:<span class="hljs-number">9000</span>/library/flume/<span class="hljs-number">2016</span>-<span class="hljs-number">04</span>-<span class="hljs-number">22.1461294231806</span>
</code></pre>

<p>可以发现本地的NOTICE文件更名为NOTICE.COMPLETED。 <br>
同时在Web50070界面上可看到Flume把NOTICE文件传送到 HDFS的/library/flume下，说明当Flume指定的源文件夹中有新文件时，Flume会自动将此文件，导入到Flume配置时指定的HDFS文件夹中。</p>

<p><strong>补充说明：</strong> <br>
    一般正常业务情况下，应该是把Flume的数据放到Kafka中，然后让不同的数据消费者去消费数据。如果要在Flume和Kafka两者间做选择的话，则要看业务中数据是否持续不断的产生。如果是这样，则应该选择Kafka。如果产生的数据时大时小，甚至有些时间没有数据，则没必要用Kafka，可以节省资源。</p>

<h2 id="二-flume推送数据到spark-streaming实战">二， Flume推送数据到Spark Streaming实战</h2>

<p>现在我们不把Flume的数据导入到HDFS中，而是把数据推送到Spark Streaming中。</p>

<p>修改conf/flume-cong.properties文件，改导入到HDFS，变为推送到Spark Streaming。</p>



<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-preprocessor">#配置sink1</span>

<span class="hljs-preprocessor">#agent1.sinks.sink1.type=hdfs</span>

<span class="hljs-preprocessor">#agent1.sinks.sink1.hdfs.path=hdfs://master:9000/library/flume</span>

<span class="hljs-preprocessor">#agent1.sinks.sink1.hdfs.fileType=DataStream</span>

<span class="hljs-preprocessor">#agent1.sinks.sink1.hdfs.writeFormat=TEXT</span>

<span class="hljs-preprocessor">#agent1.sinks.sink1.hdfs.rollInterval=1</span>

<span class="hljs-preprocessor">#agent1.sinks.sink1.channel=channel1</span>

<span class="hljs-preprocessor">#agent1.sinks.sink1.hdfs.filePrefix=%Y-%m-%d</span>

agent1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.sink</span>1<span class="hljs-preprocessor">.type</span>=avro

agent1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.sink</span>1<span class="hljs-preprocessor">.channel</span>=channel1

agent1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.sink</span>1<span class="hljs-preprocessor">.hostname</span>=Master

agent1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.sink</span>1<span class="hljs-preprocessor">.port</span>=<span class="hljs-number">9999</span></code></pre>

<p>编写Spark Streaming应用的Java程序：</p>

<pre class="prettyprint"><code class=" hljs java">
<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">FlumePushData2SparkStreaming</span> {</span>

    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">main</span>(String[] args) {

        SparkConf conf = <span class="hljs-keyword">new</span> SparkConf().setMaster(<span class="hljs-string">"local[4]"</span>).setAppName(<span class="hljs-string">"FlumePushDate2SparkStreaming"</span>);

        JavaStreamingContext jsc = <span class="hljs-keyword">new</span> JavaStreamingContext(conf, Durations.seconds(<span class="hljs-number">30</span>));

        JavaReceiverInputDStream lines = FlumeUtils.createStream(jsc,<span class="hljs-string">"Master"</span>, <span class="hljs-number">9999</span>);

        <span class="hljs-comment">// 注意此处输入的event类型是SparkFlumeEvent类型。</span>

        JavaDStream&lt;String&gt; words = lines.flatMap(<span class="hljs-keyword">new</span> FlatMapFunction&lt;SparkFlumeEvent, String&gt;() {

            <span class="hljs-annotation">@Override</span>

            <span class="hljs-keyword">public</span> Iterable&lt;String&gt; <span class="hljs-title">call</span>(SparkFlumeEvent event) <span class="hljs-keyword">throws</span> Exception {

                String line = <span class="hljs-keyword">new</span> String(event.event().getBody().array());

                <span class="hljs-keyword">return</span> Arrays.asList(line.split(<span class="hljs-string">" "</span>));

            }

        });

        JavaPairDStream&lt;String, Integer&gt; pairs = words.mapToPair(<span class="hljs-keyword">new</span> PairFunction&lt;String, String, Integer&gt;() {

            <span class="hljs-annotation">@Override</span>

            <span class="hljs-keyword">public</span> Tuple2&lt;String, Integer&gt; <span class="hljs-title">call</span>(String word) <span class="hljs-keyword">throws</span> Exception {

                <span class="hljs-keyword">return</span> <span class="hljs-keyword">new</span> Tuple2&lt;String, Integer&gt;(word, <span class="hljs-number">1</span>);

            }

        });

        JavaPairDStream&lt;String, Integer&gt; wordsCount = pairs.reduceByKey(<span class="hljs-keyword">new</span> Function2&lt;Integer, Integer, Integer&gt;() {

            <span class="hljs-annotation">@Override</span>

            <span class="hljs-keyword">public</span> Integer <span class="hljs-title">call</span>(Integer v1, Integer v2) <span class="hljs-keyword">throws</span> Exception {

                <span class="hljs-keyword">return</span> v1 + v2;

            }

        });

        wordsCount.print();

        jsc.start();

        jsc.awaitTermination();

        jsc.close();

    }

}</code></pre>

<p>代码中用到了FlumeUtils。我们剖析一下代码中用到的FlumeUtils。 <br>
以上代码中FlumeUtil的方法createStream：</p>

<p>实际是调用以下createStream方法： <br>
<img src="https://img-blog.csdn.net/20160819203631486" alt="这里写图片描述" title=""> <br>
可以看到流处理默认的存储方式是，既在内存，又在磁盘中，同时做序列化，而且用两台机器。</p>

<p>继续看调用的createStream方法： <br>
<img src="https://img-blog.csdn.net/20160819203652373" alt="这里写图片描述" title=""></p>

<p>实际上返回的是FlumeInputDStream对象，而且事件是Flume所定义的事件SparkFlumeEvent。所以要注意，在以上Java代码做flatMap时，FlatMapFunction的输入类型必须是SparkFlumeEvent类型。</p>

<p>再看看FlumeInputDStream的代码： <br>
<img src="https://img-blog.csdn.net/20160819203719639" alt="这里写图片描述" title=""></p>

<p>可以看到getReceiver返回的是用于接收数据的FlumeReceiver对象。再看FlumeReceiver： <br>
<img src="https://img-blog.csdn.net/20160819203740034" alt="这里写图片描述" title=""></p>

<p>把以上的应用Spark Streaming的Java程序运行起来。确认Flume也在运行。</p>

<p>我们找若干文件拷入TestDir文件夹，比如：flume下的若干文本文件。那么在Java运行的控制台，可以发现以下信息：</p>

<p>说明Flume推送数据到了Spark Streaming，Spark Streaming对数据及时进行了处理。</p>



<h2 id="三-原理绘图剖析">三、 原理绘图剖析</h2>

<p><img src="https://img-blog.csdn.net/20160820171220659" alt="这里写图片描述" title=""></p>

<p>补充说明： <br>
使用Spark Streaming可以处理各种数据来源类型，如：数据库、HDFS，服务器log日志、网络流，其强大超越了你想象不到的场景，只是很多时候大家不会用，其真正原因是对Spark、spark streaming本身不了解。</p>

<p>博文内容源自DT大数据梦工厂Spark课程。相关课程内容视频可以参考：  <br>
百度网盘链接：<a href="http://pan.baidu.com/s/1slvODe1" rel="nofollow">http://pan.baidu.com/s/1slvODe1</a>（如果链接失效或需要后续的更多资源，请联系QQ460507491或者微信号：DT1219477246 获取上述资料）。</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>