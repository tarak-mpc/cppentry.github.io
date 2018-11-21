---
layout:     post
title:      【三】Spark Streaming理论
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：转载注明出处					https://blog.csdn.net/jy02268879/article/details/81059100				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h1><strong>概述</strong></h1>

<p><a href="http://spark.apache.org/docs/latest/streaming-programming-guide.html" rel="nofollow">官网文档</a></p>

<h1>Spark生态圈</h1>

<p><img alt="" class="has" height="370" src="https://img-blog.csdn.net/2018072417344717?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2p5MDIyNjg4Nzk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="769"></p>

<h1>Spark生态圈和Hadoop生态圈对比</h1>

<p><img alt="" class="has" height="352" src="https://img-blog.csdn.net/2018072417385743?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2p5MDIyNjg4Nzk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="712"></p>

<h1>Spark和Hadoop的对比</h1>

<p><img alt="" class="has" height="379" src="https://img-blog.csdn.net/20180724174437422?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2p5MDIyNjg4Nzk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="673"></p>

<h1>Spark和MapReduce的对比</h1>

<p><img alt="" class="has" height="361" src="https://img-blog.csdn.net/20180724174622910?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2p5MDIyNjg4Nzk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="708"><br>
       </p>

<h1>集成Spark生态系统的其他框架综合使用</h1>

<p><img alt="" class="has" src="https://img-blog.csdn.net/20180716060854882?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2p5MDIyNjg4Nzk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></p>

<h2>Spark Core与Spark Streaming集成</h2>

<p>把静态数据集转成数据流</p>

<p><img alt="" class="has" src="https://img-blog.csdn.net/20180716060915936?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2p5MDIyNjg4Nzk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></p>

<p> </p>

<h2>Spark Core与MLlib集成</h2>

<p>数据流中每个event可以使用训练完成后的模型进行操作</p>

<p><img alt="" class="has" src="https://img-blog.csdn.net/20180716061204455?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2p5MDIyNjg4Nzk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></p>

<p> </p>

<p> </p>

<h2>Spark Core与Spark SQL集成</h2>

<p>流数据交互式数据流查询，比如Kafka过来的流数据可以注册成一张临时表然后直接用Spark SQL进行SQL查询。</p>

<p><img alt="" class="has" src="https://img-blog.csdn.net/20180716061356279?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2p5MDIyNjg4Nzk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></p>

<h1>Spark Streaming</h1>

<p>它是一个基于Spark Core的扩展，它是大规模、可扩展、高吞吐量、有容错性的、作用于实时数据流上的一个流式处理。数据能够通过多种数据源被采集，比如Kafka、Flume、Kinesis、TCP Socket。它能够通过高级函数处理复杂的算法，比如map reduce join 和窗口函数。处理完后的数据能够写入文件系统、数据库、Live dashboards。还能在数据流上使用Spark的机器学习（ML）和图计算（graph processing）。</p>

<p><img alt="" class="has" src="https://img-blog.csdn.net/20180716055407994?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2p5MDIyNjg4Nzk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></p>

<p>事实上，它的工作原理是：Spark Streaming接收实时进来的数据，把数据拆分成一些批次，这些批次会被spark引擎处理，处理结果以批次返回。</p>

<p><img alt="" class="has" src="https://img-blog.csdn.net/20180716055739202?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2p5MDIyNjg4Nzk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></p>

<h1>工作原理</h1>

<p>粗粒度：Spark Streaming 接收到实时数据流，把数据按照指定的时间（比如每1秒切一次）切成一片片小的数据块，然后把小的数据块传给Spark Engine处理。</p>

<p><img alt="" class="has" height="398" src="https://img-blog.csdn.net/20180716172118228?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2p5MDIyNjg4Nzk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="608"></p>

<p>细粒度：Spark应用程序运行在Driver端。Receiver是运行在Executor里面的，Driver运行一个Receiver作为一个Task。数据流进入Receiver，Receiver把数据流拆成小块放在内存中。被拆分后的数据会备份到另外一台机器的内存中。然后Receiver向Streaming Context汇报Block信息。Streaming Context会把数据做成RDD，然后在Spark Context上启动jobs。Spark Context会把作业提交到各个Executor中运行。</p>

<p><img alt="" class="has" height="501" src="https://img-blog.csdn.net/20180716184219255?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2p5MDIyNjg4Nzk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="765"></p>

<h1>Spark Streaming核心概念</h1>

<h2>StreamingContext:</h2>

<p>为了初始化Spark Streaming应用程序，必须要创建一个StreamingContext对象，它是所有Spark Streaming的主入口。</p>

<p>下面代码中</p>

<p>Seconds(1)的意思是1秒做一次批次切分。即是批次切分间隔时间。根据应用程序需求的延迟要求和集群可用资源来设置。</p>

<p>appName是应用程序的名字，该名字会在集群的UI中展示。</p>

<p>master可以是Spark、Mesos、YARN 集群的URL，或者指定local[*]运行本地模式。master参数在实际用的时候不要写死了，最好在提交的时候通过spark-submit写这个参数。</p>

<p><img alt="" class="has" height="120" src="https://img-blog.csdn.net/20180716184928604?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2p5MDIyNjg4Nzk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="491"></p>

<p>StreamingContext被定义好后可以做以下事情：</p>

<p>1.定义输入数据源、创建input DStreams。</p>

<p>2.定义流计算，使用transformation和输出作用到DStreams。</p>

<p>3.开始接收和处理数据用StreamingContext.start()。</p>

<p>4.等待数据处理被StreamingContext.awaitTermination停止（手工停止、因为错误而停止）</p>

<p>5.手工停止处理用StreamingContext.stop()。</p>

<p>注意项：</p>

<p>一旦Context被启动了，新的streaming计算功能就不能设置或者添加。</p>

<p>一旦Context停止后，就不能重启。(start方法不能写在stop之后)</p>

<p>同一时间，只有一个StreamingContext存活在一个JVM中。</p>

<p>stop()方法停止StreamingContext时也会停止SparkContext。如果只想停止StreamingContext，可以在使用stop()方法的时候将参数stopSparkContext设置为false。</p>

<p>一个SparkContext能够被用来创建多个StreamingContext，只要前一个StreamingContext停止了。</p>

<h2>DStream</h2>

<p>Discretized Stream是SparkStreaming提供的最基础的抽象。它代表一个持续化的数据流，它可以是从source中接收到的，也可以是一个DStream转换成另外一个DStream。本质上一个DStream代表的是一系列的持续不断的RDD。RDD是Spark里面的、不可变的、分布式数据集的抽象。DStream中的每个RDD包含的数据就是指定拆分批次的间隔时间内拆出来的数据。</p>

<p><img alt="" class="has" height="133" src="https://img-blog.csdn.net/20180716194033583?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2p5MDIyNjg4Nzk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="695"></p>

<p>DStream任何相关的操作其实底层都是基于RDD在操作。例如对DStream做flatmap操作，其实底层是对这个DStream中每个RDD都做flatmap操作。</p>

<p><img alt="" class="has" height="198" src="https://img-blog.csdn.net/20180716194633309?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2p5MDIyNjg4Nzk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="639"></p>

<h2>Input DStreams and Receivers</h2>

<p>input DStreams 是一个DStream，它代表从streaming source中接收到输入数据的流。</p>

<p>每一个input DStreams（除了从文件系统过来的数据）关联一个Receiver对象，Receiver从源头接收数据然后把数据存到内存中以供后续处理。</p>

<p>Spark Streaming中提供了两大类的内置Streaming source：</p>

<p>基础源Basic source，在StreamingContext API中直接可用这个source，如file system、soceket connection。</p>

<p>高级源Advanced source，比如Kafka、Flume、Kinesis。</p>

<p>注意</p>

<p>当运行Spark Streaming应用程序的时候如果使用的Local模式，不要使用local或者local[1]作为master的URL。因为这种写法意味着仅仅只有一个线程能被使用，如果使用基于Receiver的input DStream（如果用的HDFS上面的文件就可以用local[1]或local），Receiver就已经占用了线程了，主流程就处理不了数据了。所以要用local[n]，n&gt;Receiver的数量。</p>

<h2>Transformation and Output Operations</h2>

<p> 和RDD的操作很类似，transformation允许修改input DStream过来的数据，修改以后得到一个新的DStream。</p>

<p>常用transformation如下</p>

<p><img alt="" class="has" height="538" src="https://img-blog.csdn.net/20180716212208423?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2p5MDIyNjg4Nzk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="947"></p>

<p><img alt="" class="has" height="252" src="https://img-blog.csdn.net/20180716212221291?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2p5MDIyNjg4Nzk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="944"></p>

<p>Output Operation允许DStream里面的数据push到外部系统中，比如数据库，文件系统。</p>

<p>常用Output Operation如下</p>

<p><img alt="" class="has" height="450" src="https://img-blog.csdn.net/20180716212544606?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2p5MDIyNjg4Nzk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="949"></p>            </div>
                </div>