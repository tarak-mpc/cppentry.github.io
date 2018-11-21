---
layout:     post
title:      flume kafka spark streaming
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>flume kafka spark streaming <br>
安装flume 目前1.6 可能不支持Taildir（猜测） ，下载1.7/1.8版本下载地址 <br>
<a href="http://www.apache.org/dyn/closer.lua/flume/1.8.0/apache-flume-1.8.0-bin.tar.gz" rel="nofollow" target="_blank">http://www.apache.org/dyn/closer.lua/flume/1.8.0/apache-flume-1.8.0-bin.tar.gz</a> <br>
去官网自己找吧。 <br>
1.先搭建flume <br>
将下载的包解压 tar -zxvf <em>**</em> <br>
1）配置conf <br>
cp flume-env.sh.template flume-env.sh <br>
配置java home（也可以不配置 环境变量配置的详细就可以省略） <br>
这里我就没有配置，贴出/etc/profile 内容</p>



<pre class="prettyprint"><code class=" hljs bash"><span class="hljs-keyword">export</span> JAVA_HOME=/usr/local/soft/jdk/jdk1.<span class="hljs-number">8.0</span>_45
<span class="hljs-keyword">export</span> JRE_HOME=<span class="hljs-variable">${JAVA_HOME}</span>/jre 
<span class="hljs-keyword">export</span> CLASSPATH=.:<span class="hljs-variable">${JAVA_HOME}</span>/lib:<span class="hljs-variable">${JRE_HOME}</span>/lib 
<span class="hljs-keyword">export</span>  FLUME_HOME=/usr/local/soft/apache-flume-<span class="hljs-number">1.8</span>.<span class="hljs-number">0</span>-bin
<span class="hljs-keyword">export</span>  FLUME_CONF_DIR=<span class="hljs-variable">$FLUME_HOME</span>/conf
<span class="hljs-keyword">export</span>  FLUME_PATH=<span class="hljs-variable">$FLUME_HOME</span>/bin
<span class="hljs-keyword">export</span> PATH=<span class="hljs-variable">$SCALA_HOME</span>/bin:<span class="hljs-variable">$SPARK_HOME</span>/bin:<span class="hljs-variable">$HADOOP_HOME</span>/bin:<span class="hljs-variable">${JAVA_HOME}</span>/bin:<span class="hljs-variable">$FLUME_HOME</span>/bin:<span class="hljs-variable">$PATH</span></code></pre>

<p>2）关键配置 <br>
kafkanode.conf 配置如下</p>

<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-preprocessor">#Agent</span>
flumeAgent<span class="hljs-preprocessor">.channels</span> = c1
flumeAgent<span class="hljs-preprocessor">.sources</span>  = s1
flumeAgent<span class="hljs-preprocessor">.sinks</span>    = k1 
<span class="hljs-preprocessor">#flumeAgent Spooling Directory Source</span>
<span class="hljs-preprocessor">#注(1)</span>
flumeAgent<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.s</span>1<span class="hljs-preprocessor">.type</span> = TAILDIR
flumeAgent<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.s</span>1<span class="hljs-preprocessor">.positionFile</span> = /opt/apps/log4j/taildir_position<span class="hljs-preprocessor">.json</span>
flumeAgent<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.s</span>1<span class="hljs-preprocessor">.fileHeader</span> = true
<span class="hljs-preprocessor">#flumeAgent.sources.s1.deletePolicy =immediate</span>
<span class="hljs-preprocessor">#flumeAgent.sources.s1.batchSize =1000</span>
flumeAgent<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.s</span>1<span class="hljs-preprocessor">.channels</span> =c1
flumeAgent<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.s</span>1<span class="hljs-preprocessor">.filegroups</span> = f1 f2
flumeAgent<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.s</span>1<span class="hljs-preprocessor">.filegroups</span><span class="hljs-preprocessor">.f</span>1=/usr/logs/.*log.*
flumeAgent<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.s</span>1<span class="hljs-preprocessor">.filegroups</span><span class="hljs-preprocessor">.f</span>2=/logs/.*log.*
<span class="hljs-preprocessor">#flumeAgent.sources.s1.deserializer.maxLineLength =1048576</span>
<span class="hljs-preprocessor">#flumeAgent FileChannel</span>
<span class="hljs-preprocessor">#注(2)</span>
flumeAgent<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.type</span> = file
flumeAgent<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.checkpointDir</span> = /var/flume/spool/checkpoint
flumeAgent<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.dataDirs</span> = /var/flume/spool/data
flumeAgent<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.capacity</span> = <span class="hljs-number">200000000</span>
flumeAgent<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.keep</span>-alive = <span class="hljs-number">30</span>
flumeAgent<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.write</span>-timeout = <span class="hljs-number">30</span>
flumeAgent<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.checkpoint</span>-timeout=<span class="hljs-number">600</span>
<span class="hljs-preprocessor"># flumeAgent Sinks</span>
<span class="hljs-preprocessor">#注(3)</span>
flumeAgent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.channel</span> = c1
flumeAgent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.type</span> = avro
<span class="hljs-preprocessor"># connect to CollectorMainAgent</span>
flumeAgent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hostname</span> = data17<span class="hljs-preprocessor">.Hadoop</span>
flumeAgent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.port</span> = <span class="hljs-number">44444</span>
</code></pre>

<p>kafka.conf 配置如下</p>



<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-preprocessor">#flumeConsolidationAgent</span>
flumeConsolidationAgent<span class="hljs-preprocessor">.channels</span> = c1
flumeConsolidationAgent<span class="hljs-preprocessor">.sources</span>  = s1
flumeConsolidationAgent<span class="hljs-preprocessor">.sinks</span>    = k1 

<span class="hljs-preprocessor">#flumeConsolidationAgent Avro Source</span>
<span class="hljs-preprocessor">#注(4)</span>
flumeConsolidationAgent<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.s</span>1<span class="hljs-preprocessor">.type</span> = avro
flumeConsolidationAgent<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.s</span>1<span class="hljs-preprocessor">.channels</span> = c1
flumeConsolidationAgent<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.s</span>1<span class="hljs-preprocessor">.bind</span> = data17<span class="hljs-preprocessor">.Hadoop</span>
flumeConsolidationAgent<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.s</span>1<span class="hljs-preprocessor">.port</span> = <span class="hljs-number">44444</span>
flumeConsolidationAgent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.type</span> = org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.sink</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.KafkaSink</span>  
flumeConsolidationAgent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.topic</span> = myflume
flumeConsolidationAgent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.brokerList</span> = data14<span class="hljs-preprocessor">.Hadoop</span>:<span class="hljs-number">9092</span>,data15<span class="hljs-preprocessor">.Hadoop</span>:<span class="hljs-number">9092</span>,data16<span class="hljs-preprocessor">.Hadoop</span>:<span class="hljs-number">9092</span>
flumeConsolidationAgent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.requiredAcks</span> = <span class="hljs-number">1</span>  
flumeConsolidationAgent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.batchSize</span> = <span class="hljs-number">20</span>  
flumeConsolidationAgent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.channel</span> = c1
flumeConsolidationAgent<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.type</span> = file
flumeConsolidationAgent<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.checkpointDir</span> = /var/flume/spool/checkpoint
flumeConsolidationAgent<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.dataDirs</span> = /var/flume/spool/data
flumeConsolidationAgent<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.capacity</span> = <span class="hljs-number">200000000</span>
flumeConsolidationAgent<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.keep</span>-alive = <span class="hljs-number">30</span>
flumeConsolidationAgent<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.write</span>-timeout = <span class="hljs-number">30</span>
flumeConsolidationAgent<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.checkpoint</span>-timeout=<span class="hljs-number">600</span>

</code></pre>

<p>kafka.conf 是主节点 启动命令</p>

<pre class="prettyprint"><code class=" hljs brainfuck"><span class="hljs-comment">bin/flume</span><span class="hljs-literal">-</span><span class="hljs-comment">ng</span> <span class="hljs-comment">agent</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">conf</span> <span class="hljs-comment">conf</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">conf</span><span class="hljs-literal">-</span><span class="hljs-comment">file</span> <span class="hljs-comment">conf/kafka</span><span class="hljs-string">.</span><span class="hljs-comment">conf</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">name</span> <span class="hljs-comment">flumeConsolidationAgent</span> <span class="hljs-literal">-</span><span class="hljs-comment">Dflume</span><span class="hljs-string">.</span><span class="hljs-comment">root</span><span class="hljs-string">.</span><span class="hljs-comment">logger=DEBUG</span><span class="hljs-string">,</span><span class="hljs-comment">console</span></code></pre>

<p>kafkanode.conf 是副节点  可以有多个 启动命令</p>



<pre class="prettyprint"><code class=" hljs brainfuck"><span class="hljs-comment">bin/flume</span><span class="hljs-literal">-</span><span class="hljs-comment">ng</span> <span class="hljs-comment">agent</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">conf</span> <span class="hljs-comment">conf</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">conf</span><span class="hljs-literal">-</span><span class="hljs-comment">file</span> <span class="hljs-comment">conf/kafkanode</span><span class="hljs-string">.</span><span class="hljs-comment">conf</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">name</span> <span class="hljs-comment">flumeAgent</span> <span class="hljs-literal">-</span><span class="hljs-comment">Dflume</span><span class="hljs-string">.</span><span class="hljs-comment">root</span><span class="hljs-string">.</span><span class="hljs-comment">logger=DEBUG</span><span class="hljs-string">,</span><span class="hljs-comment">console</span></code></pre>

<p>【副节点配置都一样】 <br>
2.kafka 配置 <br>
这里就不说了因为没有什么特殊的配置 <br>
用原有的kafka就行，新建一个topic 就行了 命令</p>



<pre class="prettyprint"><code class=" hljs brainfuck"><span class="hljs-comment">/usr/local/soft/kafka_2</span><span class="hljs-string">.</span><span class="hljs-comment">11</span><span class="hljs-literal">-</span><span class="hljs-comment">0</span><span class="hljs-string">.</span><span class="hljs-comment">9</span><span class="hljs-string">.</span><span class="hljs-comment">0</span><span class="hljs-string">.</span><span class="hljs-comment">1/bin/kafka</span><span class="hljs-literal">-</span><span class="hljs-comment">topics</span><span class="hljs-string">.</span><span class="hljs-comment">sh</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">create</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">topic</span> <span class="hljs-comment">myflume</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">replication</span><span class="hljs-literal">-</span><span class="hljs-comment">factor</span> <span class="hljs-comment">2</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">partitions</span> <span class="hljs-comment">5</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">zookeeper</span> <span class="hljs-comment">data4</span><span class="hljs-string">.</span><span class="hljs-comment">Hadoop:2181</span></code></pre>

<p>3.spark streaming </p>



<pre class="prettyprint"><code class=" hljs avrasm">import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.SparkConf</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.SparkContext</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.streaming</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.KafkaUtils</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.streaming</span><span class="hljs-preprocessor">.Seconds</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.streaming</span><span class="hljs-preprocessor">.StreamingContext</span>
import kafka<span class="hljs-preprocessor">.serializer</span><span class="hljs-preprocessor">.StringDecoder</span>
import scala<span class="hljs-preprocessor">.collection</span><span class="hljs-preprocessor">.immutable</span><span class="hljs-preprocessor">.HashMap</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.log</span>4j<span class="hljs-preprocessor">.Level</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.log</span>4j<span class="hljs-preprocessor">.Logger</span>

object RealTimeMonitorStart extends Serializable {
  def main(args: Array[String]): Unit = {
    Logger<span class="hljs-preprocessor">.getLogger</span>(<span class="hljs-string">"org.apache.spark"</span>)<span class="hljs-preprocessor">.setLevel</span>(Level<span class="hljs-preprocessor">.WARN</span>)<span class="hljs-comment">;</span>
    Logger<span class="hljs-preprocessor">.getLogger</span>(<span class="hljs-string">"org.eclipse.jetty.server"</span>)<span class="hljs-preprocessor">.setLevel</span>(Level<span class="hljs-preprocessor">.ERROR</span>)<span class="hljs-comment">;</span>

    val conf = new SparkConf()<span class="hljs-preprocessor">.setAppName</span>(<span class="hljs-string">"stocker"</span>)<span class="hljs-preprocessor">.setMaster</span>(<span class="hljs-string">"local[2]"</span>)
    val sc = new SparkContext(conf)

    val ssc = new StreamingContext(sc, Seconds(<span class="hljs-number">1</span>))

    // Kafka configurations



    val topicMap =  <span class="hljs-string">"myflume"</span><span class="hljs-preprocessor">.split</span>(<span class="hljs-string">","</span>)<span class="hljs-preprocessor">.map</span>((_, <span class="hljs-number">3</span>))<span class="hljs-preprocessor">.toMap</span>
    print(topicMap)
    val kafkaStrings = KafkaUtils<span class="hljs-preprocessor">.createStream</span>(ssc,<span class="hljs-string">"data4.Hadoop:2181,data5.Hadoop:2181,data6.Hadoop:2181"</span>,<span class="hljs-string">"myflumegroup"</span>,topicMap)
    val urlClickLogPairsDStream = kafkaStrings<span class="hljs-preprocessor">.flatMap</span>(_._2<span class="hljs-preprocessor">.split</span>(<span class="hljs-string">" "</span>))<span class="hljs-preprocessor">.map</span>((_, <span class="hljs-number">1</span>))

    val urlClickCountDaysDStream = urlClickLogPairsDStream<span class="hljs-preprocessor">.reduceByKeyAndWindow</span>(
      (v1: Int, v2: Int) =&gt; {
        v1 + v2
      },
      Seconds(<span class="hljs-number">60</span>),
      Seconds(<span class="hljs-number">5</span>))<span class="hljs-comment">;</span>

    urlClickCountDaysDStream<span class="hljs-preprocessor">.print</span>()<span class="hljs-comment">;</span>

    ssc<span class="hljs-preprocessor">.start</span>()
    ssc<span class="hljs-preprocessor">.awaitTermination</span>()
  }
}</code></pre>

<p>目前就这些，有疑问评论留言。</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>