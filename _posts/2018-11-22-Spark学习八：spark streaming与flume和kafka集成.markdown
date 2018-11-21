---
layout:     post
title:      Spark学习八：spark streaming与flume和kafka集成
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="spark学习八spark-streaming与flume和kafka集成">Spark学习八：spark streaming与flume和kafka集成</h1>

<p>标签（空格分隔）： Spark</p>

<hr>

<p></p><div class="toc">
<ul>
<li><a href="#spark%E5%AD%A6%E4%B9%A0%E5%85%ABspark-streaming%E4%B8%8Eflume%E5%92%8Ckafka%E9%9B%86%E6%88%90" rel="nofollow">Spark学习八spark streaming与flume和kafka集成</a><ul>
<li><a href="#%E4%B8%80kafka" rel="nofollow">一Kafka</a></li>
<li><a href="#%E4%BA%8Cflume%E5%92%8Ckafka%E7%9A%84%E9%9B%86%E6%88%90" rel="nofollow">二flume和kafka的集成</a></li>
<li><a href="#%E4%B8%89kafka%E5%92%8Cspark-streaming%E7%9A%84%E9%9B%86%E6%88%90%E6%96%B9%E5%BC%8F%E4%B8%80kafka%E6%8E%A8%E9%80%81" rel="nofollow">三kafka和spark streaming的集成方式一kafka推送</a></li>
<li><a href="#%E5%9B%9Bkafka%E5%92%8Cspark-streaming%E7%9A%84%E9%9B%86%E6%88%90%E6%96%B9%E5%BC%8F%E4%B8%80spark-streaam%E4%B8%BB%E5%8A%A8%E8%8E%B7%E5%8F%96" rel="nofollow">四kafka和spark streaming的集成方式一spark streaam主动获取</a></li>
<li><a href="#%E4%BA%94spark-stream%E7%9A%84%E9%AB%98%E7%BA%A7%E5%BA%94%E7%94%A8updatestatebykey%E5%AE%9E%E7%8E%B0%E7%B4%AF%E5%8A%A0%E5%8A%9F%E8%83%BD" rel="nofollow">五spark stream的高级应用updateStateByKey实现累加功能</a></li>
<li><a href="#%E5%85%ADspark-stream%E7%9A%84%E9%AB%98%E7%BA%A7%E5%BA%94%E7%94%A8%E7%AA%97%E5%8F%A3%E5%87%BD%E6%95%B0" rel="nofollow">六spark stream的高级应用窗口函数</a></li>
</ul>
</li>
</ul>
</div>




<h2 id="一kafka">一，Kafka</h2>

<p>1，概述 <br>
<img src="http://static.zybuluo.com/forrestxing/9bxxrizrt2cfij45t628czpx/001.PNG" alt="001.PNG-190.4kB" title=""></p>

<p><img src="http://static.zybuluo.com/forrestxing/b4n5e0nbupmmr1qedkbo3mu1/002.PNG" alt="002.PNG-43.3kB" title=""></p>

<p><img src="http://static.zybuluo.com/forrestxing/t5r3n4ljcc0zepfbnrqgsocf/003.PNG" alt="003.PNG-18.8kB" title=""></p>

<p><img src="http://static.zybuluo.com/forrestxing/u3pn0mbrlcphpe1abgyrl9il/004.PNG" alt="004.PNG-125.8kB" title=""></p>

<p><img src="http://static.zybuluo.com/forrestxing/29gk5achtqmpclc70byuhug3/005.PNG" alt="005.PNG-136.4kB" title=""></p>

<p>2,kafka的安装</p>

<p>解压安装文件</p>

<p>修改server.properties文件：</p>



<pre class="prettyprint"><code class=" hljs coffeescript"><span class="hljs-comment">######</span><span class="hljs-comment">######</span><span class="hljs-comment">######</span><span class="hljs-comment">######</span><span class="hljs-comment">##### Log Basics ###</span><span class="hljs-comment">######</span><span class="hljs-comment">######</span><span class="hljs-comment">######</span><span class="hljs-comment">######</span><span class="hljs-comment">##</span>

<span class="hljs-comment"># A comma seperated list of directories under which to store log files</span>
log.dirs=/opt/app/kafka_2<span class="hljs-number">.10</span>-<span class="hljs-number">0.8</span><span class="hljs-number">.2</span><span class="hljs-number">.1</span>/ka-logs

<span class="hljs-comment">######</span><span class="hljs-comment">######</span><span class="hljs-comment">######</span><span class="hljs-comment">######</span><span class="hljs-comment">##### Zookeeper ###</span><span class="hljs-comment">######</span><span class="hljs-comment">######</span><span class="hljs-comment">######</span><span class="hljs-comment">######</span><span class="hljs-comment">##</span>

<span class="hljs-comment"># Zookeeper connection string (see zookeeper docs for details).</span>
<span class="hljs-comment"># This is a comma separated host:port pairs, each corresponding to a zk</span>
<span class="hljs-comment"># server. e.g. "127.0.0.1:3000,127.0.0.1:3001,127.0.0.1:3002".</span>
<span class="hljs-comment"># You can also append an optional chroot string to the urls to specify the</span>
<span class="hljs-comment"># root directory for all kafka znodes.</span>
zookeeper.connect=study.com.<span class="hljs-attribute">cn</span>:<span class="hljs-number">2181</span></code></pre>

<p>启动zookeeper:</p>



<pre class="prettyprint"><code class=" hljs sql">bin/zkServer.sh <span class="hljs-operator"><span class="hljs-keyword">start</span></span></code></pre>

<p>启动kafka:</p>



<pre class="prettyprint"><code class=" hljs lasso">bin/kafka<span class="hljs-attribute">-server</span><span class="hljs-attribute">-start</span><span class="hljs-built_in">.</span>sh config/server<span class="hljs-built_in">.</span>properties

bin/kafka<span class="hljs-attribute">-server</span><span class="hljs-attribute">-start</span><span class="hljs-built_in">.</span>sh <span class="hljs-attribute">-daemon</span> config/server<span class="hljs-built_in">.</span>properties   <span class="hljs-comment">//运行在后台</span></code></pre>

<p><img src="http://static.zybuluo.com/forrestxing/v0o9scyw6ooqgsn9ls2kobws/001.PNG" alt="001.PNG-142.6kB" title=""></p>

<p>创建topic</p>



<pre class="prettyprint"><code class=" hljs brainfuck"><span class="hljs-comment">bin/kafka</span><span class="hljs-literal">-</span><span class="hljs-comment">topics</span><span class="hljs-string">.</span><span class="hljs-comment">sh</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">create</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">zookeeper</span> <span class="hljs-comment">study</span><span class="hljs-string">.</span><span class="hljs-comment">com</span><span class="hljs-string">.</span><span class="hljs-comment">cn:2181</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">replication</span><span class="hljs-literal">-</span><span class="hljs-comment">factor</span> <span class="hljs-comment">1</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">partitions</span> <span class="hljs-comment">1</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">topic</span> <span class="hljs-comment">topicTest</span>

<span class="hljs-comment">bin/kafka</span><span class="hljs-literal">-</span><span class="hljs-comment">topics</span><span class="hljs-string">.</span><span class="hljs-comment">sh</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">list</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">zookeeper</span> <span class="hljs-comment">study</span><span class="hljs-string">.</span><span class="hljs-comment">com</span><span class="hljs-string">.</span><span class="hljs-comment">cn:2181</span></code></pre>

<p><img src="http://static.zybuluo.com/forrestxing/yx1epowialdi4a85v6w4vt24/002.PNG" alt="002.PNG-47.7kB" title=""></p>

<p>推送信息(生产者)：</p>



<pre class="prettyprint"><code class=" hljs lasso">bin/kafka<span class="hljs-attribute">-console</span><span class="hljs-attribute">-producer</span><span class="hljs-built_in">.</span>sh <span class="hljs-subst">--</span>broker<span class="hljs-attribute">-list</span> study<span class="hljs-built_in">.</span>com<span class="hljs-built_in">.</span><span class="hljs-literal">cn</span>:<span class="hljs-number">9092</span> <span class="hljs-subst">--</span>topic topicTest
eeeeeeeeeeeeeeeeeeeeeeeeeeee</code></pre>

<p>接受信息（消费者）：</p>



<pre class="prettyprint"><code class=" hljs lasso">bin/kafka<span class="hljs-attribute">-console</span><span class="hljs-attribute">-consumer</span><span class="hljs-built_in">.</span>sh <span class="hljs-subst">--</span>zookeeper study<span class="hljs-built_in">.</span>com<span class="hljs-built_in">.</span><span class="hljs-literal">cn</span>:<span class="hljs-number">2181</span> <span class="hljs-subst">--</span>topic topicTest <span class="hljs-subst">--</span>from<span class="hljs-attribute">-beginning</span></code></pre>

<p><img src="http://static.zybuluo.com/forrestxing/g2wn2vi5xg1760beova8865u/001.PNG" alt="001.PNG-24.8kB" title=""></p>



<h2 id="二flume和kafka的集成">二，flume和kafka的集成</h2>

<p>创建topicFlume:</p>



<pre class="prettyprint"><code class=" hljs brainfuck"><span class="hljs-comment">bin/kafka</span><span class="hljs-literal">-</span><span class="hljs-comment">topics</span><span class="hljs-string">.</span><span class="hljs-comment">sh</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">create</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">zookeeper</span> <span class="hljs-comment">study</span><span class="hljs-string">.</span><span class="hljs-comment">com</span><span class="hljs-string">.</span><span class="hljs-comment">cn:2181</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">replication</span><span class="hljs-literal">-</span><span class="hljs-comment">factor</span> <span class="hljs-comment">1</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">partitions</span> <span class="hljs-comment">1</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">topic</span> <span class="hljs-comment">topicFlume</span>

<span class="hljs-comment">bin/kafka</span><span class="hljs-literal">-</span><span class="hljs-comment">topics</span><span class="hljs-string">.</span><span class="hljs-comment">sh</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">list</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">zookeeper</span> <span class="hljs-comment">study</span><span class="hljs-string">.</span><span class="hljs-comment">com</span><span class="hljs-string">.</span><span class="hljs-comment">cn:2181</span></code></pre>

<p>修改flume-kafka.xml的配置：</p>



<pre class="prettyprint"><code class=" hljs avrasm">agent002<span class="hljs-preprocessor">.sources</span> = sources002
agent002<span class="hljs-preprocessor">.channels</span> = channels002
agent002<span class="hljs-preprocessor">.sinks</span> = sinks002


<span class="hljs-preprocessor">## define sources</span>
agent002<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.sources</span>002<span class="hljs-preprocessor">.type</span> = exec
agent002<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.sources</span>002<span class="hljs-preprocessor">.command</span> = tail -F /opt/app/apache-flume-<span class="hljs-number">1.5</span><span class="hljs-number">.0</span>-bin/monitor/log<span class="hljs-preprocessor">.input</span>


<span class="hljs-preprocessor">## define channels</span>
agent002<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.channels</span>002<span class="hljs-preprocessor">.type</span> = memory
agent002<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.channels</span>002<span class="hljs-preprocessor">.capacity</span> = <span class="hljs-number">10000</span>
agent002<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.channels</span>002<span class="hljs-preprocessor">.transactionCapacity</span> = <span class="hljs-number">10000</span>
agent002<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.channels</span>002<span class="hljs-preprocessor">.byteCapacityBufferPercentage</span> = <span class="hljs-number">20</span>
agent002<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.channels</span>002<span class="hljs-preprocessor">.byteCapacity</span> = <span class="hljs-number">800000</span>


<span class="hljs-preprocessor">##define sinks</span>
agent002<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.sinks</span>002<span class="hljs-preprocessor">.type</span> =org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.sink</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.KafkaSink</span>
agent002<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.sinks</span>002<span class="hljs-preprocessor">.brokerList</span>=study<span class="hljs-preprocessor">.com</span><span class="hljs-preprocessor">.cn</span>:<span class="hljs-number">9092</span>
agent002<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.sinks</span>002<span class="hljs-preprocessor">.topic</span>=topicFlume


<span class="hljs-preprocessor">##relationship</span>
agent002<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.sources</span>002<span class="hljs-preprocessor">.channels</span> = channels002
agent002<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.sinks</span>002<span class="hljs-preprocessor">.channel</span> = channels002</code></pre>

<p>启动flume:</p>



<pre class="prettyprint"><code class=" hljs lasso">bin/flume<span class="hljs-attribute">-ng</span> agent <span class="hljs-subst">--</span>conf conf <span class="hljs-subst">--</span>name agent002 <span class="hljs-subst">--</span>conf<span class="hljs-attribute">-file</span> conf/flume<span class="hljs-attribute">-kafka001</span><span class="hljs-built_in">.</span>properties <span class="hljs-attribute">-Dflume</span><span class="hljs-built_in">.</span>root<span class="hljs-built_in">.</span>logger<span class="hljs-subst">=</span>INFO,console
</code></pre>

<p>启动kafka消费者：</p>



<pre class="prettyprint"><code class=" hljs lasso">bin/kafka<span class="hljs-attribute">-console</span><span class="hljs-attribute">-consumer</span><span class="hljs-built_in">.</span>sh <span class="hljs-subst">--</span>zookeeper study<span class="hljs-built_in">.</span>com<span class="hljs-built_in">.</span><span class="hljs-literal">cn</span>:<span class="hljs-number">2181</span> <span class="hljs-subst">--</span>topic topicFlume <span class="hljs-subst">--</span>from<span class="hljs-attribute">-beginning</span></code></pre>

<p>测试：</p>



<pre class="prettyprint"><code class=" hljs bash"><span class="hljs-built_in">echo</span> <span class="hljs-string">"ddddddddddddddddddddd"</span> &gt;&gt;log.input</code></pre>



<h2 id="三kafka和spark-streaming的集成方式一kafka推送">三，kafka和spark streaming的集成方式一（kafka推送）</h2>

<p>创建topicFlume:</p>



<pre class="prettyprint"><code class=" hljs brainfuck"><span class="hljs-comment">bin/kafka</span><span class="hljs-literal">-</span><span class="hljs-comment">topics</span><span class="hljs-string">.</span><span class="hljs-comment">sh</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">create</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">zookeeper</span> <span class="hljs-comment">study</span><span class="hljs-string">.</span><span class="hljs-comment">com</span><span class="hljs-string">.</span><span class="hljs-comment">cn:2181</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">replication</span><span class="hljs-literal">-</span><span class="hljs-comment">factor</span> <span class="hljs-comment">1</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">partitions</span> <span class="hljs-comment">1</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">topic</span> <span class="hljs-comment">topicSpark</span>

<span class="hljs-comment">bin/kafka</span><span class="hljs-literal">-</span><span class="hljs-comment">topics</span><span class="hljs-string">.</span><span class="hljs-comment">sh</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">list</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">zookeeper</span> <span class="hljs-comment">study</span><span class="hljs-string">.</span><span class="hljs-comment">com</span><span class="hljs-string">.</span><span class="hljs-comment">cn:2181</span></code></pre>

<p>准备需要的jar包： <br>
<img src="http://static.zybuluo.com/forrestxing/vaxqya87yl0j5nkwv4t54f68/001.PNG" alt="001.PNG-29.1kB" title=""></p>

<p>启动spark本地应用：</p>



<pre class="prettyprint"><code class=" hljs lasso">bin/spark<span class="hljs-attribute">-shell</span> <span class="hljs-subst">\</span>
<span class="hljs-subst">--</span>jars /opt/app/spark<span class="hljs-subst">-</span><span class="hljs-number">1.3</span><span class="hljs-number">.0</span><span class="hljs-attribute">-bin</span><span class="hljs-subst">-</span><span class="hljs-number">2.5</span><span class="hljs-number">.0</span>/externaljars/kafka_2<span class="hljs-number">.10</span><span class="hljs-subst">-</span><span class="hljs-number">0.8</span><span class="hljs-number">.2</span><span class="hljs-number">.1</span><span class="hljs-built_in">.</span>jar,<span class="hljs-subst">\</span>
/opt/app/spark<span class="hljs-subst">-</span><span class="hljs-number">1.3</span><span class="hljs-number">.0</span><span class="hljs-attribute">-bin</span><span class="hljs-subst">-</span><span class="hljs-number">2.5</span><span class="hljs-number">.0</span>/externaljars/kafka<span class="hljs-attribute">-clients</span><span class="hljs-subst">-</span><span class="hljs-number">0.8</span><span class="hljs-number">.2</span><span class="hljs-number">.1</span><span class="hljs-built_in">.</span>jar,<span class="hljs-subst">\</span>
/opt/app/spark<span class="hljs-subst">-</span><span class="hljs-number">1.3</span><span class="hljs-number">.0</span><span class="hljs-attribute">-bin</span><span class="hljs-subst">-</span><span class="hljs-number">2.5</span><span class="hljs-number">.0</span>/externaljars/metrics<span class="hljs-attribute">-core</span><span class="hljs-subst">-</span><span class="hljs-number">2.2</span><span class="hljs-number">.0</span><span class="hljs-built_in">.</span>jar,<span class="hljs-subst">\</span>
/opt/app/spark<span class="hljs-subst">-</span><span class="hljs-number">1.3</span><span class="hljs-number">.0</span><span class="hljs-attribute">-bin</span><span class="hljs-subst">-</span><span class="hljs-number">2.5</span><span class="hljs-number">.0</span>/externaljars/spark<span class="hljs-attribute">-streaming</span><span class="hljs-attribute">-kafka_2</span><span class="hljs-number">.10</span><span class="hljs-subst">-</span><span class="hljs-number">1.3</span><span class="hljs-number">.0</span><span class="hljs-built_in">.</span>jar,<span class="hljs-subst">\</span>
/opt/app/spark<span class="hljs-subst">-</span><span class="hljs-number">1.3</span><span class="hljs-number">.0</span><span class="hljs-attribute">-bin</span><span class="hljs-subst">-</span><span class="hljs-number">2.5</span><span class="hljs-number">.0</span>/externaljars/zkclient<span class="hljs-subst">-</span><span class="hljs-number">0.3</span><span class="hljs-built_in">.</span>jar <span class="hljs-subst">\</span>
<span class="hljs-subst">--</span>master <span class="hljs-built_in">local</span><span class="hljs-preprocessor">[</span><span class="hljs-number">2</span><span class="hljs-preprocessor">]</span><span class="hljs-markup"></span></code></pre>

<p>spark源码:</p>



<pre class="prettyprint"><code class=" hljs avrasm">import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span>._
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.streaming</span>._
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.streaming</span><span class="hljs-preprocessor">.StreamingContext</span>._
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.streaming</span><span class="hljs-preprocessor">.kafka</span>._

val ssc = new StreamingContext(sc, Seconds(<span class="hljs-number">30</span>))

val topicMap = Map(<span class="hljs-string">"topicSpark"</span> -&gt; <span class="hljs-number">1</span>)
val kafkaStream = KafkaUtils<span class="hljs-preprocessor">.createStream</span>(ssc, <span class="hljs-string">"study.com.cn:2181"</span>, <span class="hljs-string">"topicGroup"</span>, topicMap)<span class="hljs-preprocessor">.map</span>(_._2)

val wordCountDStream = kafkaStream<span class="hljs-preprocessor">.flatMap</span>(_<span class="hljs-preprocessor">.split</span>(<span class="hljs-string">" "</span>))<span class="hljs-preprocessor">.map</span>((_, <span class="hljs-number">1</span>))<span class="hljs-preprocessor">.reduceByKey</span>(_ + _)

wordCountDStream<span class="hljs-preprocessor">.print</span>()

ssc<span class="hljs-preprocessor">.start</span>()
ssc<span class="hljs-preprocessor">.awaitTermination</span>()</code></pre>

<p>测试:</p>



<pre class="prettyprint"><code class=" hljs lasso">bin/kafka<span class="hljs-attribute">-console</span><span class="hljs-attribute">-producer</span><span class="hljs-built_in">.</span>sh <span class="hljs-subst">--</span>broker<span class="hljs-attribute">-list</span> study<span class="hljs-built_in">.</span>com<span class="hljs-built_in">.</span><span class="hljs-literal">cn</span>:<span class="hljs-number">9092</span> <span class="hljs-subst">--</span>topic topicSpark
eeeeee eeeeeeeeeeeeeeeeeeeee hadoop</code></pre>

<p><img src="http://static.zybuluo.com/forrestxing/wnxient934cfqcns540mucwu/001.PNG" alt="001.PNG-79kB" title=""></p>



<h2 id="四kafka和spark-streaming的集成方式一spark-streaam主动获取">四，kafka和spark streaming的集成方式一（spark streaam主动获取）</h2>

<p>创建topicFlume:</p>



<pre class="prettyprint"><code class=" hljs brainfuck"><span class="hljs-comment">bin/kafka</span><span class="hljs-literal">-</span><span class="hljs-comment">topics</span><span class="hljs-string">.</span><span class="hljs-comment">sh</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">create</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">zookeeper</span> <span class="hljs-comment">study</span><span class="hljs-string">.</span><span class="hljs-comment">com</span><span class="hljs-string">.</span><span class="hljs-comment">cn:2181</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">replication</span><span class="hljs-literal">-</span><span class="hljs-comment">factor</span> <span class="hljs-comment">1</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">partitions</span> <span class="hljs-comment">1</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">topic</span> <span class="hljs-comment">topicSpark</span>

<span class="hljs-comment">bin/kafka</span><span class="hljs-literal">-</span><span class="hljs-comment">topics</span><span class="hljs-string">.</span><span class="hljs-comment">sh</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">list</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">zookeeper</span> <span class="hljs-comment">study</span><span class="hljs-string">.</span><span class="hljs-comment">com</span><span class="hljs-string">.</span><span class="hljs-comment">cn:2181</span></code></pre>

<p>准备需要的jar包： <br>
<img src="http://static.zybuluo.com/forrestxing/vaxqya87yl0j5nkwv4t54f68/001.PNG" alt="001.PNG-29.1kB" title=""></p>

<p>启动spark本地应用：</p>



<pre class="prettyprint"><code class=" hljs lasso">bin/spark<span class="hljs-attribute">-shell</span> <span class="hljs-subst">\</span>
<span class="hljs-subst">--</span>jars /opt/app/spark<span class="hljs-subst">-</span><span class="hljs-number">1.3</span><span class="hljs-number">.0</span><span class="hljs-attribute">-bin</span><span class="hljs-subst">-</span><span class="hljs-number">2.5</span><span class="hljs-number">.0</span>/externaljars/kafka_2<span class="hljs-number">.10</span><span class="hljs-subst">-</span><span class="hljs-number">0.8</span><span class="hljs-number">.2</span><span class="hljs-number">.1</span><span class="hljs-built_in">.</span>jar,<span class="hljs-subst">\</span>
/opt/app/spark<span class="hljs-subst">-</span><span class="hljs-number">1.3</span><span class="hljs-number">.0</span><span class="hljs-attribute">-bin</span><span class="hljs-subst">-</span><span class="hljs-number">2.5</span><span class="hljs-number">.0</span>/externaljars/kafka<span class="hljs-attribute">-clients</span><span class="hljs-subst">-</span><span class="hljs-number">0.8</span><span class="hljs-number">.2</span><span class="hljs-number">.1</span><span class="hljs-built_in">.</span>jar,<span class="hljs-subst">\</span>
/opt/app/spark<span class="hljs-subst">-</span><span class="hljs-number">1.3</span><span class="hljs-number">.0</span><span class="hljs-attribute">-bin</span><span class="hljs-subst">-</span><span class="hljs-number">2.5</span><span class="hljs-number">.0</span>/externaljars/metrics<span class="hljs-attribute">-core</span><span class="hljs-subst">-</span><span class="hljs-number">2.2</span><span class="hljs-number">.0</span><span class="hljs-built_in">.</span>jar,<span class="hljs-subst">\</span>
/opt/app/spark<span class="hljs-subst">-</span><span class="hljs-number">1.3</span><span class="hljs-number">.0</span><span class="hljs-attribute">-bin</span><span class="hljs-subst">-</span><span class="hljs-number">2.5</span><span class="hljs-number">.0</span>/externaljars/spark<span class="hljs-attribute">-streaming</span><span class="hljs-attribute">-kafka_2</span><span class="hljs-number">.10</span><span class="hljs-subst">-</span><span class="hljs-number">1.3</span><span class="hljs-number">.0</span><span class="hljs-built_in">.</span>jar,<span class="hljs-subst">\</span>
/opt/app/spark<span class="hljs-subst">-</span><span class="hljs-number">1.3</span><span class="hljs-number">.0</span><span class="hljs-attribute">-bin</span><span class="hljs-subst">-</span><span class="hljs-number">2.5</span><span class="hljs-number">.0</span>/externaljars/zkclient<span class="hljs-subst">-</span><span class="hljs-number">0.3</span><span class="hljs-built_in">.</span>jar <span class="hljs-subst">\</span>
<span class="hljs-subst">--</span>master <span class="hljs-built_in">local</span><span class="hljs-preprocessor">[</span><span class="hljs-number">2</span><span class="hljs-preprocessor">]</span><span class="hljs-markup"></span></code></pre>

<p>spark源码:</p>



<pre class="prettyprint"><code class=" hljs avrasm">import kafka<span class="hljs-preprocessor">.serializer</span><span class="hljs-preprocessor">.StringDecoder</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span>._
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.streaming</span>._
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.streaming</span><span class="hljs-preprocessor">.StreamingContext</span>._
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.streaming</span><span class="hljs-preprocessor">.kafka</span>._

val ssc = new StreamingContext(sc, Seconds(<span class="hljs-number">30</span>))

// kafkaParams: Map[String, String]
val kafkaParams = Map(<span class="hljs-string">"metadata.broker.list"</span> -&gt; <span class="hljs-string">"study.com.cn:9092"</span>)
// topics: <span class="hljs-keyword">Set</span>[String]
val topics = <span class="hljs-keyword">Set</span>(<span class="hljs-string">"topicSpark"</span>)
val kafkaStream = KafkaUtils<span class="hljs-preprocessor">.createDirectStream</span>[String, String, StringDecoder, StringDecoder](ssc, kafkaParams, topics)<span class="hljs-preprocessor">.map</span>(_._2)



val wordCountDStream = kafkaStream<span class="hljs-preprocessor">.flatMap</span>(_<span class="hljs-preprocessor">.split</span>(<span class="hljs-string">" "</span>))<span class="hljs-preprocessor">.map</span>((_, <span class="hljs-number">1</span>))<span class="hljs-preprocessor">.reduceByKey</span>(_ + _)

wordCountDStream<span class="hljs-preprocessor">.print</span>()

ssc<span class="hljs-preprocessor">.start</span>()
ssc<span class="hljs-preprocessor">.awaitTermination</span>()</code></pre>

<p>测试:</p>



<pre class="prettyprint"><code class=" hljs lasso">bin/kafka<span class="hljs-attribute">-console</span><span class="hljs-attribute">-producer</span><span class="hljs-built_in">.</span>sh <span class="hljs-subst">--</span>broker<span class="hljs-attribute">-list</span> study<span class="hljs-built_in">.</span>com<span class="hljs-built_in">.</span><span class="hljs-literal">cn</span>:<span class="hljs-number">9092</span> <span class="hljs-subst">--</span>topic topicSpark
eeeeee eeeeeeeeeeeeeeeeeeeee hadoop</code></pre>

<p><img src="http://static.zybuluo.com/forrestxing/dl1mat7g9bj8zwp5p1u0v75g/001.PNG" alt="001.PNG-147.6kB" title=""></p>



<h2 id="五spark-stream的高级应用updatestatebykey实现累加功能">五，spark stream的高级应用updateStateByKey实现累加功能</h2>

<p>创建topicFlume:</p>



<pre class="prettyprint"><code class=" hljs brainfuck"><span class="hljs-comment">bin/kafka</span><span class="hljs-literal">-</span><span class="hljs-comment">topics</span><span class="hljs-string">.</span><span class="hljs-comment">sh</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">create</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">zookeeper</span> <span class="hljs-comment">study</span><span class="hljs-string">.</span><span class="hljs-comment">com</span><span class="hljs-string">.</span><span class="hljs-comment">cn:2181</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">replication</span><span class="hljs-literal">-</span><span class="hljs-comment">factor</span> <span class="hljs-comment">1</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">partitions</span> <span class="hljs-comment">1</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">topic</span> <span class="hljs-comment">topicSpark</span>

<span class="hljs-comment">bin/kafka</span><span class="hljs-literal">-</span><span class="hljs-comment">topics</span><span class="hljs-string">.</span><span class="hljs-comment">sh</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">list</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">zookeeper</span> <span class="hljs-comment">study</span><span class="hljs-string">.</span><span class="hljs-comment">com</span><span class="hljs-string">.</span><span class="hljs-comment">cn:2181</span></code></pre>

<p>准备需要的jar包： <br>
<img src="http://static.zybuluo.com/forrestxing/vaxqya87yl0j5nkwv4t54f68/001.PNG" alt="001.PNG-29.1kB" title=""></p>

<p>启动spark本地应用：</p>



<pre class="prettyprint"><code class=" hljs lasso">bin/spark<span class="hljs-attribute">-shell</span> <span class="hljs-subst">\</span>
<span class="hljs-subst">--</span>jars /opt/app/spark<span class="hljs-subst">-</span><span class="hljs-number">1.3</span><span class="hljs-number">.0</span><span class="hljs-attribute">-bin</span><span class="hljs-subst">-</span><span class="hljs-number">2.5</span><span class="hljs-number">.0</span>/externaljars/kafka_2<span class="hljs-number">.10</span><span class="hljs-subst">-</span><span class="hljs-number">0.8</span><span class="hljs-number">.2</span><span class="hljs-number">.1</span><span class="hljs-built_in">.</span>jar,<span class="hljs-subst">\</span>
/opt/app/spark<span class="hljs-subst">-</span><span class="hljs-number">1.3</span><span class="hljs-number">.0</span><span class="hljs-attribute">-bin</span><span class="hljs-subst">-</span><span class="hljs-number">2.5</span><span class="hljs-number">.0</span>/externaljars/kafka<span class="hljs-attribute">-clients</span><span class="hljs-subst">-</span><span class="hljs-number">0.8</span><span class="hljs-number">.2</span><span class="hljs-number">.1</span><span class="hljs-built_in">.</span>jar,<span class="hljs-subst">\</span>
/opt/app/spark<span class="hljs-subst">-</span><span class="hljs-number">1.3</span><span class="hljs-number">.0</span><span class="hljs-attribute">-bin</span><span class="hljs-subst">-</span><span class="hljs-number">2.5</span><span class="hljs-number">.0</span>/externaljars/metrics<span class="hljs-attribute">-core</span><span class="hljs-subst">-</span><span class="hljs-number">2.2</span><span class="hljs-number">.0</span><span class="hljs-built_in">.</span>jar,<span class="hljs-subst">\</span>
/opt/app/spark<span class="hljs-subst">-</span><span class="hljs-number">1.3</span><span class="hljs-number">.0</span><span class="hljs-attribute">-bin</span><span class="hljs-subst">-</span><span class="hljs-number">2.5</span><span class="hljs-number">.0</span>/externaljars/spark<span class="hljs-attribute">-streaming</span><span class="hljs-attribute">-kafka_2</span><span class="hljs-number">.10</span><span class="hljs-subst">-</span><span class="hljs-number">1.3</span><span class="hljs-number">.0</span><span class="hljs-built_in">.</span>jar,<span class="hljs-subst">\</span>
/opt/app/spark<span class="hljs-subst">-</span><span class="hljs-number">1.3</span><span class="hljs-number">.0</span><span class="hljs-attribute">-bin</span><span class="hljs-subst">-</span><span class="hljs-number">2.5</span><span class="hljs-number">.0</span>/externaljars/zkclient<span class="hljs-subst">-</span><span class="hljs-number">0.3</span><span class="hljs-built_in">.</span>jar <span class="hljs-subst">\</span>
<span class="hljs-subst">--</span>master <span class="hljs-built_in">local</span><span class="hljs-preprocessor">[</span><span class="hljs-number">2</span><span class="hljs-preprocessor">]</span><span class="hljs-markup"></span></code></pre>

<p>spark源码:</p>



<pre class="prettyprint"><code class=" hljs avrasm">import kafka<span class="hljs-preprocessor">.serializer</span><span class="hljs-preprocessor">.StringDecoder</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span>._
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.streaming</span>._
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.streaming</span><span class="hljs-preprocessor">.StreamingContext</span>._
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.streaming</span><span class="hljs-preprocessor">.kafka</span>._

val ssc = new StreamingContext(sc, Seconds(<span class="hljs-number">30</span>))

ssc<span class="hljs-preprocessor">.checkpoint</span>(<span class="hljs-string">"."</span>)
val kafkaParams = Map(<span class="hljs-string">"metadata.broker.list"</span> -&gt; <span class="hljs-string">"study.com.cn:9092"</span>)
// topics: <span class="hljs-keyword">Set</span>[String]
val topics = <span class="hljs-keyword">Set</span>(<span class="hljs-string">"topicSpark"</span>)

val kafkaStream = KafkaUtils<span class="hljs-preprocessor">.createDirectStream</span>[String, String, StringDecoder, StringDecoder](ssc, kafkaParams, topics)<span class="hljs-preprocessor">.map</span>(_._2)

val wordDStream = kafkaStream<span class="hljs-preprocessor">.flatMap</span>(_<span class="hljs-preprocessor">.split</span>(<span class="hljs-string">" "</span>))<span class="hljs-preprocessor">.map</span>((_, <span class="hljs-number">1</span>))

// definition ,Option[Int]
// SessionInfo: sessionId, ip, count
val updateFunc = (values: Seq[Int], state: Option[Int]) =&gt; {
  val currentCount = values<span class="hljs-preprocessor">.sum</span>
  val previousCount = state<span class="hljs-preprocessor">.getOrElse</span>(<span class="hljs-number">0</span>)
  Some(currentCount + previousCount)
}

// wordDStream[(K, V)]
val wordCountDStream = wordDStream<span class="hljs-preprocessor">.updateStateByKey</span>(updateFunc)

wordCountDStream<span class="hljs-preprocessor">.print</span>()

ssc<span class="hljs-preprocessor">.start</span>()
ssc<span class="hljs-preprocessor">.awaitTermination</span>()</code></pre>

<p>测试:</p>



<pre class="prettyprint"><code class=" hljs lasso">bin/kafka<span class="hljs-attribute">-console</span><span class="hljs-attribute">-producer</span><span class="hljs-built_in">.</span>sh <span class="hljs-subst">--</span>broker<span class="hljs-attribute">-list</span> study<span class="hljs-built_in">.</span>com<span class="hljs-built_in">.</span><span class="hljs-literal">cn</span>:<span class="hljs-number">9092</span> <span class="hljs-subst">--</span>topic topicSpark
eeeeee eeeeeeeeeeeeeeeeeeeee hadoop</code></pre>

<p><img src="http://static.zybuluo.com/forrestxing/jwvjlq4t0e70qje5inou3fx0/001.PNG" alt="001.PNG-78.2kB" title=""></p>

<p><img src="http://static.zybuluo.com/forrestxing/uqv15ao7qsegl2z4348xtmav/002.PNG" alt="002.PNG-101.7kB" title=""></p>



<h2 id="六spark-stream的高级应用窗口函数">六，spark stream的高级应用窗口函数</h2>

<p>创建topicFlume:</p>



<pre class="prettyprint"><code class=" hljs brainfuck"><span class="hljs-comment">bin/kafka</span><span class="hljs-literal">-</span><span class="hljs-comment">topics</span><span class="hljs-string">.</span><span class="hljs-comment">sh</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">create</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">zookeeper</span> <span class="hljs-comment">study</span><span class="hljs-string">.</span><span class="hljs-comment">com</span><span class="hljs-string">.</span><span class="hljs-comment">cn:2181</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">replication</span><span class="hljs-literal">-</span><span class="hljs-comment">factor</span> <span class="hljs-comment">1</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">partitions</span> <span class="hljs-comment">1</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">topic</span> <span class="hljs-comment">topicSpark</span>

<span class="hljs-comment">bin/kafka</span><span class="hljs-literal">-</span><span class="hljs-comment">topics</span><span class="hljs-string">.</span><span class="hljs-comment">sh</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">list</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">zookeeper</span> <span class="hljs-comment">study</span><span class="hljs-string">.</span><span class="hljs-comment">com</span><span class="hljs-string">.</span><span class="hljs-comment">cn:2181</span></code></pre>

<p>准备需要的jar包： <br>
<img src="http://static.zybuluo.com/forrestxing/vaxqya87yl0j5nkwv4t54f68/001.PNG" alt="001.PNG-29.1kB" title=""></p>

<p>启动spark本地应用：</p>



<pre class="prettyprint"><code class=" hljs lasso">bin/spark<span class="hljs-attribute">-shell</span> <span class="hljs-subst">\</span>
<span class="hljs-subst">--</span>jars /opt/app/spark<span class="hljs-subst">-</span><span class="hljs-number">1.3</span><span class="hljs-number">.0</span><span class="hljs-attribute">-bin</span><span class="hljs-subst">-</span><span class="hljs-number">2.5</span><span class="hljs-number">.0</span>/externaljars/kafka_2<span class="hljs-number">.10</span><span class="hljs-subst">-</span><span class="hljs-number">0.8</span><span class="hljs-number">.2</span><span class="hljs-number">.1</span><span class="hljs-built_in">.</span>jar,<span class="hljs-subst">\</span>
/opt/app/spark<span class="hljs-subst">-</span><span class="hljs-number">1.3</span><span class="hljs-number">.0</span><span class="hljs-attribute">-bin</span><span class="hljs-subst">-</span><span class="hljs-number">2.5</span><span class="hljs-number">.0</span>/externaljars/kafka<span class="hljs-attribute">-clients</span><span class="hljs-subst">-</span><span class="hljs-number">0.8</span><span class="hljs-number">.2</span><span class="hljs-number">.1</span><span class="hljs-built_in">.</span>jar,<span class="hljs-subst">\</span>
/opt/app/spark<span class="hljs-subst">-</span><span class="hljs-number">1.3</span><span class="hljs-number">.0</span><span class="hljs-attribute">-bin</span><span class="hljs-subst">-</span><span class="hljs-number">2.5</span><span class="hljs-number">.0</span>/externaljars/metrics<span class="hljs-attribute">-core</span><span class="hljs-subst">-</span><span class="hljs-number">2.2</span><span class="hljs-number">.0</span><span class="hljs-built_in">.</span>jar,<span class="hljs-subst">\</span>
/opt/app/spark<span class="hljs-subst">-</span><span class="hljs-number">1.3</span><span class="hljs-number">.0</span><span class="hljs-attribute">-bin</span><span class="hljs-subst">-</span><span class="hljs-number">2.5</span><span class="hljs-number">.0</span>/externaljars/spark<span class="hljs-attribute">-streaming</span><span class="hljs-attribute">-kafka_2</span><span class="hljs-number">.10</span><span class="hljs-subst">-</span><span class="hljs-number">1.3</span><span class="hljs-number">.0</span><span class="hljs-built_in">.</span>jar,<span class="hljs-subst">\</span>
/opt/app/spark<span class="hljs-subst">-</span><span class="hljs-number">1.3</span><span class="hljs-number">.0</span><span class="hljs-attribute">-bin</span><span class="hljs-subst">-</span><span class="hljs-number">2.5</span><span class="hljs-number">.0</span>/externaljars/zkclient<span class="hljs-subst">-</span><span class="hljs-number">0.3</span><span class="hljs-built_in">.</span>jar <span class="hljs-subst">\</span>
<span class="hljs-subst">--</span>master <span class="hljs-built_in">local</span><span class="hljs-preprocessor">[</span><span class="hljs-number">2</span><span class="hljs-preprocessor">]</span><span class="hljs-markup"></span></code></pre>

<p>spark源码:</p>



<pre class="prettyprint"><code class=" hljs avrasm">import kafka<span class="hljs-preprocessor">.serializer</span><span class="hljs-preprocessor">.StringDecoder</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span>._
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.streaming</span>._
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.streaming</span><span class="hljs-preprocessor">.StreamingContext</span>._
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.streaming</span><span class="hljs-preprocessor">.kafka</span>._

val ssc = new StreamingContext(sc, Seconds(<span class="hljs-number">5</span>))

// kafkaParams: Map[String, String]
val kafkaParams = Map(<span class="hljs-string">"metadata.broker.list"</span> -&gt; <span class="hljs-string">"bigdata-senior01.ibeifeng.com:9092"</span>)
// topics: <span class="hljs-keyword">Set</span>[String]
val topics = <span class="hljs-keyword">Set</span>(<span class="hljs-string">"sparkTopic"</span>)
val kafkaStream = KafkaUtils<span class="hljs-preprocessor">.createDirectStream</span>[String, String, StringDecoder, StringDecoder](ssc, kafkaParams, topics)<span class="hljs-preprocessor">.map</span>(_._2)

// val wordCountDStream = kafkaStream<span class="hljs-preprocessor">.flatMap</span>(_<span class="hljs-preprocessor">.split</span>(<span class="hljs-string">" "</span>))<span class="hljs-preprocessor">.map</span>((_, <span class="hljs-number">1</span>))<span class="hljs-preprocessor">.reduceByKey</span>(_ + _)
// 每隔十五秒，统计最近五分钟之内的情况
val wordDStream = kafkaStream<span class="hljs-preprocessor">.flatMap</span>(_<span class="hljs-preprocessor">.split</span>(<span class="hljs-string">" "</span>))<span class="hljs-preprocessor">.map</span>((_, <span class="hljs-number">1</span>))
// 
val reduceFunc = (v1: Int, v1: Int) =&gt;{
  v1 + v2
}
val wordCountDStream = wordDStream<span class="hljs-preprocessor">.reduceByKeyAndWindow</span>(reduceFunc, Seconds(<span class="hljs-number">5</span> * <span class="hljs-number">12</span> * <span class="hljs-number">5</span>), Seconds(<span class="hljs-number">3</span> * <span class="hljs-number">5</span>))

wordCountDStream<span class="hljs-preprocessor">.print</span>()

ssc<span class="hljs-preprocessor">.start</span>()
ssc<span class="hljs-preprocessor">.awaitTermination</span>()
</code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>