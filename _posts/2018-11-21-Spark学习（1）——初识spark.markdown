---
layout:     post
title:      Spark学习（1）——初识spark
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h1>Spark是什么？</h1>

<p>  Spark是一种通用的大数据计算框架，就像传统的大数据技术Hadoop的MapReduce，Hive以及Storm流式实时计算引擎等。</p>

<p>  Spark包含了大数据领域常见的各种计算框架；比如Spark Core勇于离线计算，Spark SQL用于交互式查询，Spark Streaming用于实时流式计算，Spark MLlib 用于机器学习，Spark GraphX用于图计算。</p>

<p>Spark主要用于大数据的计算，Hadoop主要用于后续大数据的存储，（比如Hdfs，Hive，Hbase）以及资源调度Yarn。</p>

<p>Spark与Hadoop关系如下图</p>

<p><img alt="" class="has" src="https://img-blog.csdn.net/20180713171943577?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dhbmd6cTIyMTM=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></p>

<h1>Spark介绍</h1>

<p>Spark是一种大数据计算框架，期望使用一种技术栈就完美解决大数据领域的各种计算任务，Apache对Spark的定义就是通用的大数据快速处理引擎。</p>

<p>Spark使用Spark RDD，Spark SQL、Spark Streaming、MLlib、GraphX成功解决了大数据领域中，离线批处理，交互式查询、实时流计算、机器学习和图计算等重要的任务和问题。</p>

<p>Spark的一个主要特定就是基于内存计算的，从而让它的速度可以达到MapReduce、Hive的数倍甚至数十倍。</p>

<p>Spark与Mapreduce计算过程如下：</p>

<p><img alt="" class="has" src="https://img-blog.csdn.net/20180713174954525?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dhbmd6cTIyMTM=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></p>

<p> </p>

<p>mapreduce 首先要将数据写入磁盘，然后通过网络从磁盘读取大量的数据，通过网络传输，每一个reduce节点需要从map节点拉取属于它的数据，这个过程就是MapReduce的shuffle，这个就决定了MapReduce只适合处理对数据不敏感的离线批处理的任务。</p>

<p>Spark 基于内存，每个节点是任务节点，代表一个任务，同时代表了一份数据，整个计算过程可能在一个物理节点的内存中进行的，这就是spark的计算模型与MapReduce完全不同的，spark是主要是基于内存的计算框架，(有时也会用磁盘，spark shuffle)，但是很多操作，比如说单纯的map操作，没有reduce操作，或者filter类的操作，都是可以直接基于内存计算的。</p>

<p>MapReduce的计算模型非常固定，必须基于磁盘，以及大量的网络传输，所以spark的速度可以比MapReduce；Hive，速度快出数倍，甚至数十倍，上百倍。</p>

<h1>Spark的整体架构</h1>

<p> </p>

<p> </p>

<p><img alt="" class="has" src="https://img-blog.csdn.net/20180713175549606?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dhbmd6cTIyMTM=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></p>

<h1>Spark目前的应用案例</h1>

<p>目前已经有很多公司正在使用Spark作为大数据的计算框架，包括，eBay，BAT，网易、京东、华为、大众点评、优酷、土豆、搜狗等。</p>

<p>Spark同时也获得了多个世界顶级的IT厂商的支持，包括IBM，Intel等。</p>

<h1>Spark SQL 与 Hive比较</h1>

<p>Spark SQL不能完全替代Hive，Hive是一种基于HDFS的数据仓库，并且提供了基于SQL模型的，针对存了大数据的数据仓库，进行分布式交互查询的查询引擎。</p>

<p>严格的来说，Spark SQL能够替代的是Hive的查询引擎，而不是Hive本身，实际上即使在生产环境下，Spark SQL也是针对Hive数据仓库中的数据进行查询，Spark自己本身不提供存储，自然也不可能替代Hive作为数据仓库的这个功能。</p>

<p>Spark SQL 的一个优点，相较于Hive查询引擎来说，就是速度快，同样的SQL语句，使用Hive的查询引擎，由于其底层是基于MapReduce，必须经过shuffle过程走磁盘，一次速度是非常缓慢的，很多复杂的sql语句，在Hive中执行都需要1个小时以上的时间，而Spark SQL 由于其底层基于Spark自身的基于内存的特点，因此速度达到了Hive查询引擎的数倍以上。</p>

<p>但是Spark SQL还不够完善，有一些Hive支持的高级特性，Spark SQL不支持，导致Spark SQL还不能完全替代Hive的查询引擎，而只能在部分Spark SQL 功能特性满足需求的场景下，进行使用。</p>

<p>Spark SQL另一个优点就是支持大量不同的数据源，包括Hive，json，jdbc等，Spark SQL支持可以直接针对hdfs文件执行sql语句。</p>

<h1>Spark Streaming 与 Storm比较</h1>

<p>Spark Streaming 与Storm都可以用于实时流计算，但是两者区别是非常大的，区别1，Spark Streaming和Storm的计算类型不一样，Spark Streaming是基于RDD的,因此需要将一小段时间内的，比如1s内的数据，收集起来，作为一个RDD,然后针对这个batch的数据进行处理，而Storm却可以做到每来一条数据，就可以立即进行处理和计算，因此，Spark Streaming实际上严格意义上来说，只能称作准实时的流计算框架，而Storm是真正意义上的实时的计算框架。</p>

<p>此外，Storm支持的一项高级特性，是Spark Streaming暂时不具备的，即Storm支持在分布式流式计算程序在运行过程中，可以动态的调整并行度，从而动态提高并发处理能力，而Spark Streaming是无法动态调整并行度的，</p>

<p>但是Spark Streaming也有其优点，首先Spark Streaming 由于是基于batch处理的，因此相较于Storm基于单条数据进行处理，具有数倍甚至数十倍的吞吐量。</p>

<p>此外Spark Streaming 也身处Spark生态圈，因此Spark Streaming可以与Spark其他组件进行无缝整合，流式处理完的数据，可以立即进行 map  reduce操作，可以进行sql进行查询，甚至可以立即使用machine learning或者图计算算法进行处理，这种一站式的大数据处理功能和优势，是Storm无法匹敌的。</p>

<p>因此，通常对实时性要求特别高，可以选择使用Storm，但是如果对实时性要求一般，允许1s内的准实时处理，而且不要求动态调整并行度的化，选择Spark Streaming是更好的选择。</p>

<p> </p>

<p> </p>            </div>
                </div>