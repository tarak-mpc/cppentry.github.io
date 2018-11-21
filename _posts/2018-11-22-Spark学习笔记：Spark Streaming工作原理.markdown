---
layout:     post
title:      Spark学习笔记：Spark Streaming工作原理
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p id="main-toc"><strong>目录</strong></p>

<p id="-toc" style="margin-left:0px;"> </p>

<p id="Spark%20Streaming%E6%A6%82%E8%BF%B0-toc" style="margin-left:0px;"><a href="#Spark%20Streaming%E6%A6%82%E8%BF%B0" rel="nofollow">Spark Streaming概述</a></p>

<p id="%E4%B8%80%E3%80%81Apache%20Spark-toc" style="margin-left:40px;"><a href="#%E4%B8%80%E3%80%81Apache%20Spark" rel="nofollow">一、Apache Spark</a></p>

<p id="Spark%20Core-toc" style="margin-left:80px;"><a href="#Spark%20Core" rel="nofollow">Spark Core</a></p>

<p id="Spark%20SQL-toc" style="margin-left:80px;"><a href="#Spark%20SQL" rel="nofollow">Spark SQL</a></p>

<p id="Spark%20Streaming-toc" style="margin-left:80px;"><a href="#Spark%20Streaming" rel="nofollow">Spark Streaming</a></p>

<p id="%E4%BA%8C%E3%80%81Spark%20Streaming%E5%A4%84%E7%90%86%E6%95%B0%E6%8D%AE%E7%9A%84%E6%B5%81%E7%A8%8B-toc" style="margin-left:40px;"><a href="#%E4%BA%8C%E3%80%81Spark%20Streaming%E5%A4%84%E7%90%86%E6%95%B0%E6%8D%AE%E7%9A%84%E6%B5%81%E7%A8%8B" rel="nofollow">二、Spark Streaming处理数据的流程</a></p>

<p id="1.%E6%95%B0%E6%8D%AE%E6%BA%90-toc" style="margin-left:80px;"><a href="#1.%E6%95%B0%E6%8D%AE%E6%BA%90" rel="nofollow">1.数据源</a></p>

<p id="2.%E6%95%B0%E6%8D%AE%E5%A4%84%E7%90%86-toc" style="margin-left:80px;"><a href="#2.%E6%95%B0%E6%8D%AE%E5%A4%84%E7%90%86" rel="nofollow">2.数据处理</a></p>

<p id="3.%E5%AD%98%E5%82%A8%E7%BB%93%E6%9E%9C-toc" style="margin-left:80px;"><a href="#3.%E5%AD%98%E5%82%A8%E7%BB%93%E6%9E%9C" rel="nofollow">3.存储结果</a></p>

<p id="%E4%B8%89%E3%80%81Spark%20Streaming%E5%B7%A5%E4%BD%9C%E5%8E%9F%E7%90%86-toc" style="margin-left:40px;"><a href="#%E4%B8%89%E3%80%81Spark%20Streaming%E5%B7%A5%E4%BD%9C%E5%8E%9F%E7%90%86" rel="nofollow">三、Spark Streaming工作原理</a></p>

<hr id="hr-toc"><h1 id="Spark%20Streaming%E6%A6%82%E8%BF%B0" style="margin-left:0cm;">Spark Streaming概述</h1>

<p style="margin-left:0cm;">目前对于流式数据实时处理方案主要有两种：</p>

<p style="margin-left:0cm;">一条一条数据的处理，它的实时性很高，亚秒级别，延迟性非常的低，比如：Storm、JStorm<br>
一批次一批次数据的处理，按时间间隔进行划分，时间间隔比较短，运行延迟性在毫秒到秒级别，比如SparkStreaming</p>

<h2 id="%E4%B8%80%E3%80%81Apache%20Spark" style="margin-left:0cm;">一、Apache Spark</h2>

<p style="margin-left:0cm;">集批处理、交互式处理和流式处理，一栈式大数据解决方案</p>

<h3 id="Spark%20Core" style="margin-left:0cm;">Spark Core</h3>

<p style="margin-left:0cm;">Spark Core主要是作为离线批处理（Batch Processing），每次处理的数据都是一个固定的数据集，而不是变化的</p>

<p style="margin-left:0cm;"><strong>相关概念</strong></p>

<p style="margin-left:0cm;">RDD：弹性分布式数据集<br>
Spark Context：Spark的上下文，它负责与程序和spark集群进行交互，包括申请集群资源、创建RDD、accumulators及广播变量等。</p>

<h3 id="Spark%20SQL" style="margin-left:0cm;">Spark SQL</h3>

<p style="margin-left:0cm;">Spark SQL用于交互式处理（interactive Processing），同样的，每次处理的数据都是一个固定的数据集，而不是变化的</p>

<p style="margin-left:0cm;"><strong>相关概念</strong></p>

<p style="margin-left:0cm;">DataFrame=RDD+Schema<br>
DataSet：相当于一个Row类型的DataFrame，在Spark 2.x之后推荐使用DataSet<br>
SQLContext：SQL的上下文</p>

<h3 id="Spark%20Streaming" style="margin-left:0cm;">Spark Streaming</h3>

<p style="margin-left:0cm;">Spark Streaming是一个流式数据处理（Stream Processing）的框架，要处理的数据就像流水一样源源不断的产生，就需要实时处理。在Spark Streaming中，对于Spark Core进行了API的封装和扩展，将流式的数据切分为小批次（batch，称之为微批，按照时间间隔切分）进行处理，可以用于进行大规模、高吞吐量、容错的实时数据流的处理。<br>
支持从很多种数据源中读取数据，使用算子来进行数据处理，处理后的数据可以被保存到文件系统、数据库等存储中</p>

<p style="margin-left:0cm;"><strong>相关概念</strong></p>

<p style="margin-left:0cm;">DStream：离散流，相当于是一个数据的集合<br>
StreamingContext：在创建StreamingContext的时候，会自动的创建SparkContext对象</p>

<p style="margin-left:0cm;">对于电商来说，每时每刻都会产生数据（如订单，网页的浏览数据等），这些数据就需要实时的数据处理</p>

<p style="margin-left:0cm;">​<img alt="" class="has" height="151" src="https://img-blog.csdn.net/20180914094724217?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xyeGNtd3ky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="748"><br>
将源源不断产生的数据实时收集并实时计算，尽可能快的得到计算结果并展示</p>

<h2 id="%E4%BA%8C%E3%80%81Spark%20Streaming%E5%A4%84%E7%90%86%E6%95%B0%E6%8D%AE%E7%9A%84%E6%B5%81%E7%A8%8B" style="margin-left:0cm;">二、Spark Streaming处理数据的流程</h2>

<h3 id="1.%E6%95%B0%E6%8D%AE%E6%BA%90" style="margin-left:0cm;">1.数据源</h3>

<p style="margin-left:0cm;">大多情况从Kafka中获取数据，还可以从Flume中直接获取，还能从TCP Socket中获取数据（一般用于开发测试）</p>

<h3 id="2.%E6%95%B0%E6%8D%AE%E5%A4%84%E7%90%86" style="margin-left:0cm;">2.数据处理</h3>

<p style="margin-left:0cm;">主要通过DStream针对不同的业务需求使用不同的方法（算子）对数据进行相关操作，企业中最多的两种类型统计：实时累加统计（如统计某电商销售额）会用到DStream中的算子updateStateBykey、实时统计某段时间内的数据（如对趋势进行统计分析，实时查看最近20分钟内各个省份用户点击广告的流量统计）会用到reduceByKeyAndWindow这个算子。</p>

<h3 id="3.%E5%AD%98%E5%82%A8%E7%BB%93%E6%9E%9C" style="margin-left:0cm;">3.存储结果</h3>

<p style="margin-left:0cm;">调用RDD中的API将数据进行存储，因为Spark Streaming是将数据分为微批处理的，所以每一批次就相当于一个RDD，可以把结果存储到Console（控制台打印，开发测试）、Redis（基于内存的分布式Key-Value数据库）、HBase（分布式列式数据库）、RDBMS（关系型数据库，如MySQL，通过JDBC）</p>

<h2 id="%E4%B8%89%E3%80%81Spark%20Streaming%E5%B7%A5%E4%BD%9C%E5%8E%9F%E7%90%86" style="margin-left:0cm;">三、Spark Streaming工作原理</h2>

<p style="margin-left:0cm;">Spark Streaming的工作原理是，先接收实时输入的数据流，然后将数据拆分成多个batch（批），比如每收集1秒的数据封装为一个batch，然后将每个batch交给Spark的计算引擎进行处理，最后会生产出一个结果数据流，其中的数据，也是由一个一个的batch所组成的</p>

<p style="margin-left:0cm;"> </p>            </div>
                </div>