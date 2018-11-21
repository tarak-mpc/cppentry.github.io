---
layout:     post
title:      Spark Streaming整合flume实战（一）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/qq_41455420/article/details/79518890				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2 id="spark-streaming从flume-中拉取数据">Spark Streaming从flume 中拉取数据</h2>

<p>Spark Streaming对接Flume有两种方式</p>

<ul>
<li>Poll：Spark Streaming从flume 中拉取数据</li>
<li>Push：Flume将消息Push推给Spark Streaming</li>
</ul>



<h3 id="1安装flume16以上">1、安装flume1.6以上</h3>



<h3 id="2下载依赖包">2、下载依赖包</h3>

<p>spark-streaming-flume-sink_2.11-2.0.2.jar放入到flume的lib目录下</p>



<h3 id="3生成数据">3、生成数据</h3>

<p>服务器上的 /root/data目录下准备数据文件data.txt</p>



<pre class="prettyprint"><code class=" hljs haskell"><span class="hljs-title">vi</span> <span class="hljs-typedef"><span class="hljs-keyword">data</span>.txt</span>

<span class="hljs-title">hadoop</span> spark hive spark
<span class="hljs-title">hadoop</span> sqoop flume redis flume hadoop
<span class="hljs-title">solr</span> kafka solr hadoop</code></pre>



<h3 id="4配置采集方案">4、配置采集方案</h3>



<pre class="prettyprint"><code class=" hljs avrasm">vi flume-poll<span class="hljs-preprocessor">.conf</span>

a1<span class="hljs-preprocessor">.sources</span> = <span class="hljs-built_in">r1</span>
a1<span class="hljs-preprocessor">.sinks</span> = k1
a1<span class="hljs-preprocessor">.channels</span> = c1
<span class="hljs-preprocessor">#source</span>
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.channels</span> = c1
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.type</span> = spooldir
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.spoolDir</span> = /root/data
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.fileHeader</span> = true
<span class="hljs-preprocessor">#channel</span>
a1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.type</span> =memory
a1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.capacity</span> = <span class="hljs-number">20000</span>
a1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.transactionCapacity</span>=<span class="hljs-number">5000</span>
<span class="hljs-preprocessor">#sinks</span>
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.channel</span> = c1
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.type</span> = org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.streaming</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.sink</span><span class="hljs-preprocessor">.SparkSink</span>
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hostname</span>=hdp-node-<span class="hljs-number">01</span>
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.port</span> = <span class="hljs-number">8888</span>
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.batchSize</span>= <span class="hljs-number">2000</span>   </code></pre>



<h3 id="5添加依赖">5、添加依赖</h3>



<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.apache.spark<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>spark-streaming-flume_2.10<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>2.0.2<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span></code></pre>



<h3 id="6代码实现">6、代码实现</h3>



<pre class="prettyprint"><code class=" hljs scala"><span class="hljs-keyword">package</span> cn.cheng.spark
<span class="hljs-keyword">import</span> java.net.InetSocketAddress
<span class="hljs-keyword">import</span> org.apache.spark.storage.StorageLevel
<span class="hljs-keyword">import</span> org.apache.spark.streaming.dstream.{DStream, ReceiverInputDStream}
<span class="hljs-keyword">import</span> org.apache.spark.streaming.flume.{FlumeUtils, SparkFlumeEvent}
<span class="hljs-keyword">import</span> org.apache.spark.streaming.{Seconds, StreamingContext}
<span class="hljs-keyword">import</span> org.apache.spark.{SparkConf, SparkContext}

<span class="hljs-javadoc">/**
  * sparkStreaming整合flume 拉模式Poll

  */</span>
<span class="hljs-class"><span class="hljs-keyword">object</span> <span class="hljs-title">SparkStreaming_Flume_Poll</span> {</span>
  <span class="hljs-comment">//newValues 表示当前批次汇总成的(word,1)中相同单词的所有的1</span>
  <span class="hljs-comment">//runningCount 历史的所有相同key的value总和</span>
  <span class="hljs-keyword">def</span> updateFunction(newValues: Seq[Int], runningCount: Option[Int]): Option[Int] = {
    <span class="hljs-keyword">val</span> newCount =runningCount.getOrElse(<span class="hljs-number">0</span>)+newValues.sum
    Some(newCount)
  }


  <span class="hljs-keyword">def</span> main(args: Array[String]): Unit = {
    <span class="hljs-comment">//配置sparkConf参数</span>
    <span class="hljs-keyword">val</span> sparkConf: SparkConf = <span class="hljs-keyword">new</span> SparkConf().setAppName(<span class="hljs-string">"SparkStreaming_Flume_Poll"</span>).setMaster(<span class="hljs-string">"local[2]"</span>)
    <span class="hljs-comment">//构建sparkContext对象</span>
    <span class="hljs-keyword">val</span> sc: SparkContext = <span class="hljs-keyword">new</span> SparkContext(sparkConf)
    <span class="hljs-comment">//构建StreamingContext对象，每个批处理的时间间隔</span>
    <span class="hljs-keyword">val</span> scc: StreamingContext = <span class="hljs-keyword">new</span> StreamingContext(sc, Seconds(<span class="hljs-number">5</span>))
    <span class="hljs-comment">//设置checkpoint</span>
      scc.checkpoint(<span class="hljs-string">"./"</span>)
    <span class="hljs-comment">//设置flume的地址，可以设置多台</span>
    <span class="hljs-keyword">val</span> address=Seq(<span class="hljs-keyword">new</span> InetSocketAddress(<span class="hljs-string">"192.168.200.160"</span>,<span class="hljs-number">8888</span>))
    <span class="hljs-comment">// 从flume中拉取数据</span>
    <span class="hljs-keyword">val</span> flumeStream: ReceiverInputDStream[SparkFlumeEvent] = FlumeUtils.createPollingStream(scc,address,StorageLevel.MEMORY_AND_DISK)

    <span class="hljs-comment">//获取flume中数据，数据存在event的body中，转化为String</span>
    <span class="hljs-keyword">val</span> lineStream: DStream[String] = flumeStream.map(x=&gt;<span class="hljs-keyword">new</span> String(x.event.getBody.array()))
    <span class="hljs-comment">//实现单词汇总</span>
   <span class="hljs-keyword">val</span> result: DStream[(String, Int)] = lineStream.flatMap(_.split(<span class="hljs-string">" "</span>)).map((_,<span class="hljs-number">1</span>)).updateStateByKey(updateFunction)

    result.print()
    scc.start()
    scc.awaitTermination()
  }

}</code></pre>



<h3 id="7启动flume">7、启动flume</h3>



<pre class="prettyprint"><code class=" hljs lasso">flume<span class="hljs-attribute">-ng</span> agent <span class="hljs-attribute">-n</span> a1 <span class="hljs-attribute">-c</span> /opt/bigdata/flume/conf <span class="hljs-attribute">-f</span> /opt/bigdata/flume/conf/flume<span class="hljs-attribute">-poll</span><span class="hljs-built_in">.</span>conf <span class="hljs-attribute">-Dflume</span><span class="hljs-built_in">.</span>root<span class="hljs-built_in">.</span>logger<span class="hljs-subst">=</span>INFO,console</code></pre>



<h3 id="8启动spark-streaming应用程序">8、启动spark-streaming应用程序</h3>



<h3 id="9查看结果">9、查看结果</h3>

<p><img src="https://img-blog.csdn.net/20180311193924486?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfNDE0NTU0MjA=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p>喜欢就点赞评论+关注吧</p>

<p><img src="https://img-blog.csdn.net/20180311153638319?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfNDE0NTU0MjA=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p>感谢阅读，希望能帮助到大家，谢谢大家的支持！</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>