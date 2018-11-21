---
layout:     post
title:      spark和hadoop的比较
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h3>Spark与Hadoop的对比</h3>
Spark的中间数据放到内存中，对于迭代运算效率更高。Spark更适合于迭代运算比较多的ML和DM运算。因为在Spark里面，有RDD的抽象概念。Spark比Hadoop更通用。Spark提供的数据集操作类型有很多种，不像Hadoop只提供了Map和Reduce两种操作。比如map, filter, flatMap, sample, groupByKey, reduceByKey, union, join, cogroup, mapValues, sort,partionBy等多种操作类型，Spark把这些操作称为Trans<a href="http://www.07net01.com/program/" rel="nofollow"><u>for</u></a>mations。同时还提供Count,
 collect, reduce, lookup, save等多种actions操作。这些多种多样的数据集操作类型，给给<a href="http://www.07net01.com/program/" rel="nofollow"><u>开发</u></a>上层应用的用户提供了方便。各个处理节点之间的通信模型不再像Hadoop那样就是唯一的Data Shuffle一种模式。用户可以命名，物化，控制中间结果的存储、分区等。可以说<a href="http://www.07net01.com/program/" rel="nofollow"><u>编程</u></a>模型比Hadoop更灵活。不过由于RDD的特性，Spark不适用那种异步细粒度更新状态的应用，例如web服务的存储或者是增量的web爬虫和索引。就是对于那种增量修改的应用模型不适合。容错性。在分布式数据集计算时通过checkpoint来实现容错，而checkpoint有两种方式，一个是checkpoint
 data，一个是logging the updates。用户可以控制采用哪种方式来实现容错。可用性。Spark通过提供丰富的Scala, Java，Python API及交互式来提高可用性。
<h3 align="left">Spark与Hadoop的结合</h3>
Spark可以直接对HDFS进行数据的读写RDD定义了各种操作，不同类型的数据由不同的RDD类抽象表示，不同的操作也由RDD进行抽实现
<h3>RDD的生成</h3>
RDD有两种创建方式：<br>
1、从Hadoop文件系统（或与Hadoop兼容的其它存储系统）输入（例如HDFS）创建。<br>
2、从父RDD转换得到新RDD。
<h3>RDD的转换与操作</h3>
对于RDD可以有两种计算方式：转换（返回值还是一个RDD）与操作（返回值不是一个RDD）。转换(Transformations) (如：map, filter, groupBy, join等)，Transformations操作是Lazy的，也就是说从一个RDD转换生成另一个RDD的操作不是马上执行，Spark在遇到Transformations操作时只会记录需要这样的操作，并不会去执行，需要等到有Actions操作的时候才会真正启动计算过程进行计算。操作(Actions) (如：count, collect,
 save等)，Actions操作会返回结果或把RDD数据写到存储系统中。Actions是触发Spark启动计算的动因。
<h3 align="left">Lineage（血统）</h3>
<p>    利用内存加快数据加载,在众多的其它的In-Memory类数据库或Cache类系统中也有实现，Spark的主要区别在于它处理分布式运算环境下的数据容错性（节点实效/数据丢失）问题时采用的方案。为了保证RDD中数据的鲁棒性，RDD数据集通过所谓的血统关系(Lineage)记住了它是如何从其它RDD中演变过来的。相比其它系统的细颗粒度的内存数据更新级别的备份或者LOG机制，RDD的Lineage记录的是粗颗粒度的特定数据转换（Transformation）操作（filter, map, join etc.)行为。当这个RDD的部分分区数据丢失时，它可以通过Lineage获取足够的信息来重新运算和恢复丢失的数据分区。这种粗颗粒的数据模型，限制了Spark的运用场合，但同时相比细颗粒度的数据模型，也带来了性能的提升。<br>
    RDD在Lineage依赖方面分为两种Narrow Dependencies与Wide Dependencies用来解决数据容错的高效性。Narrow Dependencies是指父RDD的每一个分区最多被一个子RDD的分区所用，表现为一个父RDD的分区对应于一个子RDD的分区或多个父RDD的分区对应于一个子RDD的分区，也就是说一个父RDD的一个分区不可能对应一个子RDD的多个分区。Wide Dependencies是指子RDD的分区依赖于父RDD的多个分区或所有分区，也就是说存在一个父RDD的一个分区对应一个子RDD的多个分区。对与Wide
 Dependencies，这种计算的输入和输出在不同的节点上，lineage方法对与输入节点完好，而输出节点宕机时，通过重新计算，这种情况下，这种方法容错是有效的，否则无效，因为无法重试，需要向上其祖先追溯看是否可以重试（这就是lineage，血统的意思），Narrow Dependencies对于数据的重算开销要远小于Wide Dependencies的数据重算开销。</p>
<p><br></p>
<p></p>
<h3>容错</h3>
在RDD计算，通过checkpint进行容错，做checkpoint有两种方式，一个是checkpoint data，一个是logging the updates。用户可以控制采用哪种方式来实现容错，默认是logging the updates方式，通过记录跟踪所有生成RDD的转换（transformations）也就是记录每个RDD的lineage（血统）来重新计算生成丢失的分区数据。
<p></p>
<p></p>
<h3>资源管理与作业调度</h3>
    Spark对于资源管理与作业调度可以使用Standalone(独立模式)，Apache Mesos及Hadoop YARN来实现。<br>
    让Spark运行于YARN上与Hadoop共用集群资源可以提高资源利用率。
<p></p>
<p>来源：http://tech.uc.cn/?p=2116<br></p>
<p>来源：http://www.07net01.com/blog/Spark_yigegaoxiaodefenbushijisuanxitong_581817_1378867183.html<br></p>
            </div>
                </div>