---
layout:     post
title:      Spark定制班第3课：通过案例对SparkStreaming透彻理解三板斧之三：解密Spark Streaming运行机制和架构进阶之Job和容错
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div>﻿﻿</div>
<span style="font-family:'Microsoft Yahei';line-height:1.5;font-size:13.63px;background-color:inherit;"><strong>本期内容：</strong></span>
<p style="line-height:1.5;font-family:'微软雅黑';font-size:14px;">
<span style="font-family:'Microsoft Yahei';line-height:1.5;">1 解密Spark Streaming Job 架构和运行机制</span></p>
<p style="line-height:1.5;font-family:'微软雅黑';font-size:14px;">
<span style="font-family:'Microsoft Yahei';background-color:inherit;">2 解密Spark Streaming Job 容错架构和运行机制</span></p>
<p style="line-height:1.5;font-family:'微软雅黑';font-size:14px;">
<span style="font-family:'Microsoft Yahei';background-color:inherit;"><br style="background-color:inherit;"></span></p>
<p style="line-height:1.5;font-family:'微软雅黑';font-size:14px;">
<span style="font-family:'Microsoft Yahei';background-color:inherit;"><span style="font-size:13.63px;"><span style="background-color:inherit;"><strong>1 解密Spark Streaming Job 架构和运行机制</strong></span></span><br style="background-color:inherit;"></span></p>
<p style="line-height:1.5;font-family:'微软雅黑';font-size:14px;">
<span style="font-family:'Microsoft Yahei';background-color:inherit;">理解Spark Streaming的Job的整个架构和运行机制对于精通Spark Streaming是至关重要的。</span></p>
<p style="line-height:1.5;font-family:'微软雅黑';font-size:14px;">
<span style="font-family:'Microsoft Yahei';background-color:inherit;">我们运行以下的程序，通过这个程序的运行过程进一步加深理解Spark Streaming流处理的Job的执行的过程，代码如下：</span></p>
<p style="line-height:1.5;font-family:'微软雅黑';font-size:14px;">
<span style="font-family:'Microsoft Yahei';background-color:inherit;"><br></span></p>
<p style="line-height:1.5;font-family:'微软雅黑';font-size:14px;">
<span style="font-family:'Microsoft Yahei';line-height:1.5;"></span></p><pre><code class="language-html">object OnlineForeachRDD2DB {
  def main(args: Array[String]){
    /**
      * 第1步：创建Spark的配置对象SparkConf，设置Spark程序的运行时的配置信息，
      * 例如说通过setMaster来设置程序要链接的Spark集群的Master的URL,如果设置
      * 为local，则代表Spark程序在本地运行，特别适合于机器配置条件非常差（例如
      * 只有1G的内存）的初学者
      */
    val conf = new SparkConf() //创建SparkConf对象
    conf.setAppName("OnlineForeachRDD") //设置应用程序的名称，在程序运行的监控界面可以看到名称
    conf.setMaster("spark://Master:7077") //此时，程序在Spark集群
    conf.setMaster("local[6]")
    //设置batchDuration时间间隔来控制Job生成的频率并且创建Spark Streaming执行的入口
    val ssc = new StreamingContext(conf, Seconds(5))
    val lines = ssc.socketTextStream("Master", 9999)
    val words = lines.flatMap(_.split(" "))
    val wordCounts = words.map(x =&gt; (x, 1)).reduceByKey(_ + _)

    wordCounts.foreachRDD{ rdd =&gt;
      rdd.foreachPartition{ partitionOfRecords =&gt; {
        // ConnectionPool is a static, lazily initialized pool of connections
        val connection = ConnectionPool.getConnection()
        partitionOfRecords.foreach(record =&gt; {
          val sql = "insert into streaming_itemcount(item,count) values('" + record._1 + "'," + record._2 + ")"
          val stmt = connection.createStatement();
          stmt.executeUpdate(sql);
        })
        ConnectionPool.returnConnection(connection)  // return to the pool for future reuse
      }}
    }

    /**
      *  在StreamingContext调用start方法的内部其实是会启动JobScheduler的Start方法，进行消息循环，
      *  在JobScheduler的start内部会构造JobGenerator和ReceiverTacker，并且调用JobGenerator和
      *  ReceiverTacker的start方法：
      *  1，JobGenerator启动后会不断的根据batchDuration生成一个个的Job
      *  2，ReceiverTracker启动后首先在Spark Cluster中启动Receiver（其实是在Executor中先启动
      *  ReceiverSupervisor），在Receiver收到数据后会通过ReceiverSupervisor存储到Executor并且把
      *  数据的Metadata信息发送给Driver中的ReceiverTracker，在ReceiverTracker内部会通过
      *  ReceivedBlockTracker来管理接受到的元数据信息每个BatchInterval会产生一个具体的Job，
      *  其实这里的Job不是Spark Core中所指的Job，它只是基于DStreamGraph而生成的RDD的DAG
      *  而已，从Java角度讲，相当于Runnable接口实例，此时要想运行Job需要提交给JobScheduler，
      *  在JobScheduler中通过线程池的方式找到一个单独的线程来提交Job到集群运行（其实是在线程中
      *  基于RDD的Action触发真正的作业的运行），
      *  为什么使用线程池呢？
      *  1，作业不断生成，所以为了提升效率，我们需要线程池；这和在Executor中通过线程池执行Task
      *  有异曲同工之妙；
      *  2，有可能设置了Job的FAIR公平调度的方式，这个时候也需要多线程的支持。
      */
    ssc.start()
    ssc.awaitTermination()
  }
}
</code></pre><br><p></p>
<p style="line-height:1.5;font-family:'微软雅黑';font-size:14px;">
<span style="font-family:'Microsoft Yahei';line-height:1.5;"><br></span></p>
<p style="line-height:1.5;font-family:'微软雅黑';font-size:14px;">
<span style="font-family:'Microsoft Yahei';background-color:inherit;"><span style="font-size:13.63px;"><span style="background-color:inherit;"><strong>2 解密Spark Streaming Job容错架构和运行机制</strong></span></span><br style="background-color:inherit;"></span></p>
<p><br></p>
<span style="font-family:'Microsoft YaHei';"></span>
<p><span style="font-family:'宋体';font-size:14px;"><span style="font-family:'Microsoft YaHei';">Spark容错分为Driver级别的容错和Executor级别的容错。</span></span></p>
<span style="font-size:14px;"></span>
<p><span style="font-family:'宋体';font-size:14px;"><span style="font-family:'Microsoft YaHei';">- 在Driver级别的容错具体为DAG生成的模板，即DStreamGraph，RecevierTracker中存储的元数据信息和JobScheduler中存储的Job进行的进度情况等信息，只要通过checkpoint就可以了，每个Job生成之前进行checkpoint，在Job生成之后再进行checkpoint，如果出错的话就从checkpoint中恢复。</span></span></p>
<p><span style="font-size:14px;">- 在Executor级别的容错具体为接收数据的安全性和任务执行的安全性。</span><span style="font-size:14px;">在接收数据安全性方面，一种方式是Spark Streaming接收到数据默认为MEMORY_AND_DISK_2的方式，在两台机器的内存中，如果一台机器上的Executor挂了，立即切换到另一台机器上的Executor，这种方式一般情况下非常可靠且没有切换时间。另外一种方式是WAL（Write Ahead
 Log），在数据到来时先通过WAL机制将数据进行日志记录，如果有问题则从日志记录中恢复，然后再把数据存到Executor中，再进行其他副本的复制。WAL这种方式对性能有影响，在生产环境中不常用，一般使用Kafka存储，Spark Streaming接收到数据丢失时可以从Kafka中回放。</span><span style="font-size:14px;">在任务执行的安全性方面，靠</span><span style="font-size:14px;">RDD</span><span style="font-size:14px;">的容错。</span><span style="font-size:14px;"> </span></p>
<span style="font-family:'Microsoft Yahei';background-color:inherit;"><span style="font-size:13.63px;"><span style="background-color:inherit;"></span></span></span>
<p><br style="background-color:inherit;"></p>
<p style="line-height:1.5;font-family:'微软雅黑';font-size:14px;">
<span style="font-family:'Microsoft Yahei';background-color:inherit;">Spark Streaming的容错机制是基于RDD的容错机制。</span></p>
<p style="line-height:1.5;font-family:'微软雅黑';font-size:14px;">
<span style="font-family:'Microsoft Yahei';background-color:inherit;">主要表现为：</span></p>
<p style="line-height:1.5;font-family:'微软雅黑';font-size:14px;">
<span style="font-family:'Microsoft Yahei';background-color:inherit;"><span style="font-size:13.63px;"></span><span style="font-family:'Microsoft Yahei';font-size:14px;line-height:21px;">　　</span>1 checkpoint</span></p>
<p style="line-height:1.5;font-family:'微软雅黑';font-size:14px;">
<span style="font-family:'Microsoft Yahei';background-color:inherit;"><span style="font-size:13.63px;"></span><span style="font-family:'Microsoft Yahei';font-size:14px;line-height:21px;">　　</span>2 基于血统（lineage）的高度容错机制</span></p>
<p style="line-height:1.5;font-family:'微软雅黑';font-size:14px;">
<span style="font-family:'Microsoft Yahei';background-color:inherit;"><span style="font-size:13.63px;"></span><span style="font-family:'Microsoft Yahei';font-size:14px;line-height:21px;">　　</span>3 出错了之后会从出错的位置从新计算，而不会导致重复计算</span></p>
<p style="line-height:1.5;font-family:'微软雅黑';font-size:14px;">
<span style="font-family:'Microsoft Yahei';background-color:inherit;"><br></span></p>
<p style="line-height:1.5;font-family:'微软雅黑';font-size:14px;">
<span style="font-family:'Microsoft Yahei';background-color:inherit;">备注：<br>
资料来源于：DT_大数据<a title="Hadoop知识库" class="replace_word" href="http://lib.csdn.net/base/20" rel="nofollow" style="color:rgb(223,52,52);font-weight:bold;"></a>梦工厂（Spark发行版本定制）<br>
更多私密内容，请关注微信<a title="微信开发知识库" class="replace_word" href="http://lib.csdn.net/base/5" rel="nofollow" style="color:rgb(223,52,52);font-weight:bold;"></a>公众号：DT_Spark<br>
如果您对大数据Spark感兴趣，可以免费听由王家林老师每天晚上20：00开设的Spark永久免费公开课，地址YY房间号：68917580</span></p>
            </div>
                </div>