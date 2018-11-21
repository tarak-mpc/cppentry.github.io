---
layout:     post
title:      FATAL Fatal error during KafkaServerStartable startup. java.lang.OutOfMemoryError: Java heap space
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>今天搭建了kafka单机版用于测试，配置好以后启动kafka时出现了内存溢出的问题，如下： </p>
<p></p><pre><code class="language-java">[2016-10-22 10:39:39,112] INFO [Kafka Server 0], shut down completed (kafka.server.KafkaServer)
[2016-10-22 10:39:39,113] FATAL Fatal error during KafkaServerStartable startup. Prepare to shutdown (kafka.server.KafkaServerStartable)
java.lang.OutOfMemoryError: Java heap space
	at java.nio.HeapByteBuffer.&lt;init&gt;(HeapByteBuffer.java:57)
	at java.nio.ByteBuffer.allocate(ByteBuffer.java:335)
	at kafka.log.SkimpyOffsetMap.&lt;init&gt;(OffsetMap.scala:42)
	at kafka.log.LogCleaner$CleanerThread.&lt;init&gt;(LogCleaner.scala:177)
	at kafka.log.LogCleaner$$anonfun$1.apply(LogCleaner.scala:86)
	at kafka.log.LogCleaner$$anonfun$1.apply(LogCleaner.scala:86)
	at scala.collection.TraversableLike$$anonfun$map$1.apply(TraversableLike.scala:244)
	at scala.collection.TraversableLike$$anonfun$map$1.apply(TraversableLike.scala:244)
	at scala.collection.immutable.Range.foreach(Range.scala:141)
	at scala.collection.TraversableLike$class.map(TraversableLike.scala:244)
	at scala.collection.AbstractTraversable.map(Traversable.scala:105)
	at kafka.log.LogCleaner.&lt;init&gt;(LogCleaner.scala:86)
	at kafka.log.LogManager.&lt;init&gt;(LogManager.scala:64)
	at kafka.server.KafkaServer.createLogManager(KafkaServer.scala:335)
	at kafka.server.KafkaServer.startup(KafkaServer.scala:85)
	at kafka.server.KafkaServerStartable.startup(KafkaServerStartable.scala:29)
	at kafka.Kafka$.main(Kafka.scala:46)
	at kafka.Kafka.main(Kafka.scala)
[2016-10-22 10:39:39,114] INFO EventThread shut down (org.apache.zookeeper.ClientCnxn)
[2016-10-22 10:39:39,116] INFO [Kafka Server 0], shutting down (kafka.server.KafkaServer)</code></pre><br><p>解决方法：</p>
<p>编辑 kafka/kafka-server-start.sh 这个启动文件，修改 KAFKA_HEAP_OPTS 这个参数，默认的参数是 export KAFKA_HEAP_OPTS="-Xmx512m -Xms512m"，增大内存即可，export KAFKA_HEAP_OPTS="-Xmx1g -Xms1g"，再次启动问题解决。</p>
            </div>
                </div>