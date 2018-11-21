---
layout:     post
title:      【Python3实战Spark大数据分析及调度】Spark Streaming 课程笔记
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h2 style="text-indent:0px;">Spark Streaming概述</h2>

<p><a href="https://spark.apache.org/docs/latest/streaming-programming-guide.html" rel="nofollow">https://spark.apache.org/docs/latest/streaming-programming-guide.html</a></p>

<p>Spark Streaming is <span style="color:#f33b45;">an extension of the core Spark API</span> that enables <span style="color:#f33b45;">scalable, high-throughput, fault-tolerant stream processing</span> of live data streams. Data can be ingested from many sources like <span style="color:#f33b45;">Kafka</span>, Flume, Kinesis, or TCP sockets, and can be processed using complex algorithms expressed with high-level functions like <code>map</code>, <code>reduce</code>, <code>join</code> and <strong><span style="color:#f33b45;"><code>window</code></span></strong>. Finally, processed data can be pushed out to filesystems, databases, and live dashboards. In fact, you can apply Spark’s <a href="https://spark.apache.org/docs/latest/ml-guide.html" rel="nofollow">machine learning</a> and <a href="https://spark.apache.org/docs/latest/graphx-programming-guide.html" rel="nofollow">graph processing</a> algorithms on data streams.</p>

<p><img alt="" class="has" src="https://spark.apache.org/docs/latest/img/streaming-arch.png"></p>

<p>安装完spark就可以用 spark streaming啦</p>

<p> </p>

<h2>实时流处理框架对比</h2>

<p>Storm： 真正的实时流处理 java</p>

<p>Spark Streaming： 并不是真正的实时流处理，而是一个mini batch操作 （按时间间隔把数据拆发成一个个批次）</p>

<p>scala、python、java， 使用spark一栈式解决，有延迟</p>

<p>Flink：  和spark streaming逻辑相反，做流处理</p>

<p> </p>

<h2>Spark Streaming执行原理     </h2>

<p>Internally, it works as follows. Spark Streaming receives live input data streams and divides the data into <span style="color:#f33b45;">batches</span>, which are then processed by the Spark engine to generate the final stream of results in batches.</p>

<p><img alt="" class="has" src="https://spark.apache.org/docs/latest/img/streaming-flow.png"></p>

<p>Spark Streaming provides a high-level abstraction called <em>discretized stream</em> or <span style="color:#f33b45;"><strong><em>DStream</em></strong></span>, which represents a <span style="color:#f33b45;">continuous stream of data</span>. DStreams can be created <span style="color:#f33b45;">（1）</span>either from input data streams from sources such as Kafka, Flume, and Kinesis, <span style="color:#f33b45;">（2）</span>or by applying high-level operations on other DStreams. Internally, <span style="color:#f33b45;">a DStream is represented as a sequence of </span><a href="https://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.rdd.RDD" rel="nofollow"><span style="color:#f33b45;">RDDs</span></a><span style="color:#f33b45;">.</span></p>

<p> </p>

<p><span style="color:#f33b45;">未完待续。。。</span></p>            </div>
                </div>