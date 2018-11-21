---
layout:     post
title:      Spark Streaming（01）——Spark Streaming概述
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p></p><div class="toc"><h3>Spark Streaming</h3><ul><ul><li><a href="#1Spark_Streaming_1" rel="nofollow">1、Spark Streaming是什么？</a></li><li><a href="#2Spark_Streaming_9" rel="nofollow">2、Spark Streaming特点</a></li><ul><ul><li><a href="#_11" rel="nofollow">易用</a></li><li><a href="#_13" rel="nofollow">容错</a></li><li><a href="#_17" rel="nofollow">易整合</a></li></ul></ul><li><a href="#3SparkStreamingStorm_20" rel="nofollow">3、SparkStreaming与Storm的对比</a></li></ul></ul></div><p></p>
<h2><a id="1Spark_Streaming_1"></a>1、Spark Streaming是什么？</h2>
<p>Spark Streaming类似于Apache Storm，用于流式数据的处理。<br>
Spark Streaming有高吞吐量和容错能力强等特点。且支持的数据源有很多，例如：Kafka、Flume、Twitter、ZeroMQ和简单的TCP套接字等等。<br>
数据输入后可以用Spark的高度抽象操作如：map、reduce、join、window等进行运算。而结果也能保存在很多地方，如HDFS，数据库等。另外Spark Streaming也能和MLlib（机器学习）以及Graphx完美融合。<br>
<img src="https://img-blog.csdn.net/20181003111000525?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0ZlbmdnbXM=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="在这里插入图片描述"></p>
<p>官方地址：<a href="http://spark.apache.org/streaming/" rel="nofollow">http://spark.apache.org/streaming/</a></p>
<h2><a id="2Spark_Streaming_9"></a>2、Spark Streaming特点</h2>
<h4><a id="_11"></a>易用</h4>
<p>可以像编写离线批处理一样去编写流式程序，支持java/scala/python语言。</p>
<h4><a id="_13"></a>容错</h4>
<p>SparkStreaming在没有额外代码和配置的情况下可以恢复丢失的工作。<br>
它的容错性依赖于底层的rdd的容错机制。<br>
<img src="https://img-blog.csdn.net/20181003113340191?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0ZlbmdnbXM=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="在这里插入图片描述"></p>
<h4><a id="_17"></a>易整合</h4>
<p>Spark Streaming 易整合到Spark体系。它可以将流式处理与批处理和交互式查询相结合。</p>
<h2><a id="3SparkStreamingStorm_20"></a>3、SparkStreaming与Storm的对比</h2>
<p>开发语言不同：spark Streaming的开发语言是Scala,Storm 的开发语言是Clojure.<br>
编程模型不同：Spark Streaming的编程模型是Dstream,Storm的编程模型是Spout/Bolt<br>
实时性不同：Spark Streaming是某一时间间隔的流式批量处理。实时性没有storm高，准实时处理。Storm是来一条数据处理一条，实时性比较高。</p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>