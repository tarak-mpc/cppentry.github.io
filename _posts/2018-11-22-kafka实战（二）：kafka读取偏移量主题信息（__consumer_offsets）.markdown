---
layout:     post
title:      kafka实战（二）：kafka读取偏移量主题信息（__consumer_offsets）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本博客所有文章均为原创，如有转载，请注明原文地址，谢谢！----桃花惜春风					https://blog.csdn.net/xiaoyu_BD/article/details/81782418				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>Kafka 的新版本已经支持将 group 组的偏移量信息存储在 kafka 中，而且由于之前存储在 zookeeper 中，而 zookeeper 并不适合大批量的频繁写入操作，官网也是建议使用 kafka 来进 行存储。<br>
那么问题来了，我们要怎样查看在 kafka 中的 metadata 信息呢?</p>

<p>Metadata 是以 topic 的形式存储在 kafka 中的，topic 名为:__consumer_offsets。并且也提供 了 kafka_consumer_groups.sh 脚本来帮助我们查看偏移量情况。但这还是不满足我们的需求， 我们可能需要查看__consumer_offsets 中的所有信息和信息结构，这里我们介绍如何查看 __consumer_offsets 中的信息。</p>

<p>查看__consumer_offsets 中的所有信息<br>
需要在 consumer.properties 中设置 exclude.internal.topics=false</p>

<pre class="has">
<code>bin/kafka-console-consumer.sh --topic __consumer_offsets --zookeeper localhost:2181 -- formatter "kafka.coordinator.GroupMetadataManager\$OffsetsMessageFormatter" -- consumer.config config/consumer.properties --from-beginning</code></pre>

<p>查看某个 group 在__consumer_offsets 的信息：<br>
1. 计算要查询的 groupID 在__consumer_offsets 中所在的分区，对应的分区 =Math.abs("gropuID".hashCode()) % 50<br>
2. 获取信息<br>
 </p>

<pre class="has">
<code>bin/kafka-simple-consumer-shell.sh --topic __consumer_offsets --partition 指定分区 --broker-list localhost:9092 --formatter "kafka.coordinator.GroupMetadataManager\$OffsetsMessageFormatter" </code></pre>

<p>可以看到__consumer_offsets topic 的每一日志项的格式都是:<br>
[Group, Topic, Partition]::[OffsetMetadata[Offset, Metadata], CommitTime, ExpirationTime]</p>

<p>更多：<a href="https://blog.csdn.net/column/details/26247.html" rel="nofollow">kafka深入理解专栏</a></p>

<p>——————————————————————————————————</p>

<p>作者：桃花惜春风</p>

<p>转载请标明出处，原文地址：  </p>

<p><a href="https://blog.csdn.net/xiaoyu_BD/article/details/81782418" rel="nofollow">https://blog.csdn.net/xiaoyu_BD/article/details/81782418</a></p>

<p>如果感觉本文对您有帮助，请留下您的赞，您的支持是我坚持写作最大的动力，谢谢！</p>            </div>
                </div>