---
layout:     post
title:      Using Kafka with Spark Streaming
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>For information on how to configure Spark Streaming to receive data from Kafka, see the <a href="http://spark.apache.org/docs/1.5.0/streaming-kafka-integration.html" rel="nofollow">Spark Streaming + Kafka Integration Guide</a>.</p>

<p>In CDH 5.7 and higher, the Spark connector to Kafka only works with Kafka 2.0 and higher.</p>



<h2 id="validating-kafka-integration-with-spark-streaming">Validating Kafka Integration with Spark Streaming</h2>

<p>To validate your Kafka integration with Spark Streaming, run the <code>KafkaWordCount</code> example. <br>
If you installed Spark using parcels, use the following command: </p>



<pre class="prettyprint"><code class=" hljs xml">/opt/cloudera/parcels/CDH/lib/spark/bin/run-example streaming.KafkaWordCount <span class="hljs-tag">&lt;<span class="hljs-title">zkQuorum</span>&gt;</span> <span class="hljs-tag">&lt;<span class="hljs-title">group</span>&gt;</span> <span class="hljs-tag">&lt;<span class="hljs-title">topics</span>&gt;</span> <span class="hljs-tag">&lt;<span class="hljs-title">numThreads</span>&gt;</span></code></pre>

<p>for Spark:</p>



<pre class="prettyprint"><code class=" hljs profile">/opt/cloudera/parcels/CDH/lib/spark/bin/run-example <span class="hljs-filename">streaming.KafkaWordCount master</span>:<span class="hljs-number">2181</span> groupName topicName <span class="hljs-number">2</span></code></pre>

<p>for Spark2</p>



<pre class="prettyprint"><code class=" hljs profile">/opt/cloudera/parcels/SPARK2/lib/spark2/bin/run-example <span class="hljs-filename">streaming.KafkaWordCount master</span>:<span class="hljs-number">2181</span> groupName topicName <span class="hljs-number">2</span></code></pre>

<p>If you installed Spark using packages, use the following command:</p>



<pre class="prettyprint"><code class=" hljs xml">/usr/lib/spark/bin/run-example streaming.KafkaWordCount <span class="hljs-tag">&lt;<span class="hljs-title">zkQuorum</span>&gt;</span> <span class="hljs-tag">&lt;<span class="hljs-title">group</span>&gt;</span> <span class="hljs-tag">&lt;<span class="hljs-title">topics</span>&gt;</span><span class="hljs-tag">&lt;<span class="hljs-title">numThreads</span>&gt;</span></code></pre>

<p>Replace the variables as follows:</p>

<ul>
<li><code>&lt;zkQuorum&gt;</code> - ZooKeeper quorum URI used by Kafka (for example, zk01.example.com:2181,zk02.example.com:2181,zk03.example.com:2181).</li>
<li><code>&lt;group&gt;</code> - Consumer group used by the application.</li>
<li><code>&lt;topic&gt;</code> - Kafka topic containing the data for the application.</li>
<li><code>&lt;numThreads&gt;</code> - Number of consumer threads reading the data. If this is higher than the number of partitions in the Kafka topic, some threads will be idle.</li>
</ul>

<blockquote>
  <p>Note: If multiple applications use the same group and topic, each application receives a subset of the data.</p>
</blockquote>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>