---
layout:     post
title:      Spark Streaming介绍以及简单使用
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-light">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2><a id="Spark_Streaming_0"></a>一、Spark Streaming介绍</h2>
<p>Spark Streaming is an extension of the core Spark API that enables scalable, high-throughput, fault-tolerant stream processing of live data streams.<br>
Spark Streaming是Spark core的扩展，有以下特点：高可扩展、高吞吐量的、容错的流式程序作用在数据流上<br>
Data can be ingested from many sources like Kafka, Flume, Kinesis, or TCP sockets, and can be processed using complex algorithms<br>
expressed with high-level functions like map, reduce, join and window.<br>
数据能够被采集，从Kafka, Flume, Kinesis, or TCP sockets中，并且可以被处理，使用复杂的算法通过高级别得方法表达，比如：map, reduce, join and window<br>
Finally, processed data can be pushed out to filesystems, databases, and live dashboards.<br>
最终，处理过的数据能够被推到文件系统、数据库以及在线的dashboards（仪表板）。<br>
In fact, you can apply Spark’s machine learning and graph processing algorithms on data streams.<br>
事实上，你可以在实时的数据流上应用Spark的机器学习和图形处理算法<br>
<img src="https://img-blog.csdn.net/20181010225958746?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTM0MjkwMTA=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="在这里插入图片描述"></p>
<h5><a id="1_12"></a>1、总结：</h5>
<p>将不同数据源的数据经过Spark Streaming处理之后将结果输出到外部文件系统</p>
<h5><a id="2_14"></a>2、特点：</h5>
<p>1）低延迟<br>
2）能从错误高效的恢复：fault-tolerant<br>
3）能运行在成百上千的节点上<br>
4）能够将批处理、机器学习、图处理等子框架和Spark Streaming综合起来使用</p>
<h5><a id="3_20"></a>3、工作原理：</h5>
<h6><a id="_21"></a>粗粒度</h6>
<p>数据流进入，Spark Streaming接收到实时数据流，把数据按照指定的时间段切成一片一片小的数据块，然后把小的数据块传给 Spark Engine处理后批次输出<br>
<img src="https://img-blog.csdn.net/20181010230023188?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTM0MjkwMTA=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="在这里插入图片描述"></p>
<h2><a id="Spark_Streaming_25"></a>二、Spark Streaming使用</h2>
<h5><a id="1Github_27"></a>1、Github</h5>
<p><a href="https://github.com/apache/spark/" rel="nofollow">https://github.com/apache/spark/</a></p>
<h5><a id="2nc_29"></a>2、启动nc</h5>
<pre><code class="prism language-xml">yum install -y nc
nc -lk 9999
</code></pre>
<h5><a id="3sparksubmit_34"></a>3、spark-submit使用</h5>
<pre><code class="prism language-xml">$SPARK_HOME/bin/spark-submit --master local[2] \
--class org.apache.spark.examples.streaming.NetworkWordCount \
--name NetworkWordCount \
/app/cdh/spark-2.3.0-bin-hadoop2.6/examples/jars/spark-examples_2.11-2.3.0.jar dev2 9999
</code></pre>
<p>–class 使用包名org.apache.spark.examples.streaming + 类名<br>
<img src="https://img-blog.csdn.net/20181010230140874?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTM0MjkwMTA=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="在这里插入图片描述"></p>
<h5><a id="4sparkshell_43"></a>4、如何使用spark-shell测试</h5>
<p><code>$SPARK_HOME/bin/spark-shell --master local[2]</code></p>
<p>#由于sc在spark-shell中已经自带，我们直接使用就可以了</p>
<pre><code class="prism language-zml">import org.apache.spark.storage.StorageLevel
import org.apache.spark.streaming.{Seconds, StreamingContext}
val ssc = new StreamingContext(sc, Seconds(1))
val lines = ssc.socketTextStream("dev2", 9999, StorageLevel.MEMORY_AND_DISK_SER)
val words = lines.flatMap(_.split(" "))
val wordCounts = words.map(x =&gt; (x, 1)).reduceByKey(_ + _)
wordCounts.print()
ssc.start()
</code></pre>
<p>#StorageLevel.MEMORY_AND_DISK_SER这个参数是设置spark处理数据的存储方式，如果数据量少，我们一般使用内存</p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>