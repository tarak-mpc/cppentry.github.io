---
layout:     post
title:      cdh_集群spark2_中_kafka_版本配置问题
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，转载请 注上 http://blog.csdn.net/wzhwei1987					https://blog.csdn.net/wzhwei1987/article/details/83388079				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>CDH 5.14.2 集群中安装spark2.3.0 后的默认安装的 Kafka 依赖包有两个版本，分别是 kafka 0.9 以及 kafka 0.10 集群默认选中的是使用 kafka 0.9。</p>

<p>当使用 kafka 0.10 时需进入 spark 配置页面进行修改，否则报错如下：</p>

<p> </p>

<p>Exception in thread “streaming-start” java.lang.NoSuchMethodError: org.apache.kafka.clients.consumer.KafkaConsumer.subscribe(Ljava/util/Collection;)V</p>

<pre>
at org.apache.spark.streaming.kafka010.Subscribe.onStart(ConsumerStrategy.scala:85)
at org.apache.spark.streaming.kafka010.DirectKafkaInputDStream.consumer(DirectKafkaInputDStream.scala:70)
at org.apache.spark.streaming.kafka010.DirectKafkaInputDStream.start(DirectKafkaInputDStream.scala:240)
at org.apache.spark.streaming.DStreamGraph$$anonfun$start$7.apply(DStreamGraph.scala:54)
at org.apache.spark.streaming.DStreamGraph$$anonfun$start$7.apply(DStreamGraph.scala:54)
at scala.collection.parallel.mutable.ParArray$ParArrayIterator.foreach_quick(ParArray.scala:143)
at scala.collection.parallel.mutable.ParArray$ParArrayIterator.foreach(ParArray.scala:136)
at scala.collection.parallel.ParIterableLike$Foreach.leaf(ParIterableLike.scala:972)
at scala.collection.parallel.Task$$anonfun$tryLeaf$1.apply$mcV$sp(Tasks.scala:49)
at scala.collection.parallel.Task$$anonfun$tryLeaf$1.apply(Tasks.scala:48)
at scala.collection.parallel.Task$$anonfun$tryLeaf$1.apply(Tasks.scala:48)
at scala.collection.parallel.Task$class.tryLeaf(Tasks.scala:51)
at scala.collection.parallel.ParIterableLike$Foreach.tryLeaf(ParIterableLike.scala:969)
at scala.collection.parallel.AdaptiveWorkStealingTasks$WrappedTask$class.compute(Tasks.scala:152)
at scala.collection.parallel.AdaptiveWorkStealingForkJoinTasks$WrappedTask.compute(Tasks.scala:443)
at scala.concurrent.forkjoin.RecursiveAction.exec(RecursiveAction.java:160)
at scala.concurrent.forkjoin.ForkJoinTask.doExec(ForkJoinTask.java:260)
at scala.concurrent.forkjoin.ForkJoinPool$WorkQueue.runTask(ForkJoinPool.java:1339)
at scala.concurrent.forkjoin.ForkJoinPool.runWorker(ForkJoinPool.java:1979)
at scala.concurrent.forkjoin.ForkJoinWorkerThread.run(ForkJoinWorkerThread.java:107)</pre>            </div>
                </div>