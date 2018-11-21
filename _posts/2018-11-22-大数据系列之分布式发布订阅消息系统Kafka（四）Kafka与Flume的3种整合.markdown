---
layout:     post
title:      大数据系列之分布式发布订阅消息系统Kafka（四）Kafka与Flume的3种整合
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/u011444062/article/details/81212104				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>前面我们已经介绍了Flume，现在我们将Kafka与Flume整合</p>

<p>先看一下Flume的结构组成：</p>

<p>          <img alt="" class="has" height="323" src="https://img-blog.csdn.net/20180725232813101?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE0NDQwNjI=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="744"></p>

<p>我们可以发现，将Flume与Kafka进行整合无非3种情况，<span style="color:#f33b45;">Flume作为生产者——Sink输出到Kafka</span>，Flume作为消费者——Source接受Kafka的输出，Flume既做生产者也做消费者，格式为Source接受数据——Kafka(作为Flume的Channel)——Sink输出数据，其中第一种方式最常用<span style="color:#f33b45;">Flume作为生产者——Sink输出到Kafka。</span></p>

<p><span style="color:#f33b45;">1.Flume作为生产者——Sink输出到Kafka（用的最多，基本用的是这种整合方式）</span></p>

<p><img alt="" class="has" height="374" src="https://img-blog.csdn.net/20180725234157803?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE0NDQwNjI=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="1058"></p>

<p><span>a1.sources = r1<br>
a1.sinks = k1<br>
a1.channels = c1</span></p>

<p><span>a1.sources.r1.type=netcat<br>
a1.sources.r1.bind=localhost<br>
a1.sources.r1.port=8888</span></p>

<p><span>a1.sinks.k1.type = org.apache.flume.sink.kafka.KafkaSink<br>
a1.sinks.k1.kafka.topic = test<br>
a1.sinks.k1.kafka.bootstrap.servers = s10:9092<br>
a1.sinks.k1.kafka.flumeBatchSize = 20<br>
a1.sinks.k1.kafka.producer.acks = 1</span></p>

<p><span>a1.channels.c1.type=memory</span></p>

<p><span>a1.sources.r1.channels = c1<br>
a1.sinks.k1.channel = c1</span></p>

<p>                                          <img alt="" class="has" height="104" src="https://img-blog.csdn.net/20180725234002941?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE0NDQwNjI=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="396"></p>

<p>               <img alt="" class="has" height="47" src="https://img-blog.csdn.net/20180725234031689?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE0NDQwNjI=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="701"></p>

<p>2.Flume作为消费者——Source接受Kafka的输出</p>

<p>a1.sources = r1<br>
a1.sinks = k1<br>
a1.channels = c1</p>

<p>a1.sources.r1.type = org.apache.flume.source.kafka.KafkaSource<br>
a1.sources.r1.batchSize = 5000<br>
a1.sources.r1.batchDurationMillis = 2000<br>
a1.sources.r1.kafka.bootstrap.servers = s10:9092<br>
a1.sources.r1.kafka.topics = test<br>
a1.sources.r1.kafka.consumer.group.id = g4</p>

<p>a1.sinks.k1.type = logger</p>

<p>a1.channels.c1.type=memory</p>

<p>a1.sources.r1.channels = c1<br>
a1.sinks.k1.channel = c1<br>
                     </p>

<p>          <img alt="" class="has" height="44" src="https://img-blog.csdn.net/20180725235138524?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE0NDQwNjI=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="630"></p>

<p><img alt="" class="has" height="178" src="https://img-blog.csdn.net/20180725235229196?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE0NDQwNjI=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="1200"></p>

<p>3.Flume既做生产者也做消费者</p>

<p>a1.sources = r1<br>
a1.sinks = k1<br>
a1.channels = c1</p>

<p>a1.sources.r1.type = netcat<br>
a1.sources.r1.bind = localhost<br>
a1.sources.r1.port = 8888</p>

<p>a1.sinks.k1.type = logger</p>

<p>a1.channels.c1.type = org.apache.flume.channel.kafka.KafkaChannel<br>
a1.channels.c1.kafka.bootstrap.servers = s10:9092<br>
a1.channels.c1.kafka.topic = test<br>
a1.channels.c1.kafka.consumer.group.id = g6<br><span style="color:#f33b45;">a1.channels.c1.parseAsFlumeEvent = false</span><br>
a1.sources.r1.channels = c1<br>
a1.sinks.k1.channel = c1</p>

<p>                                                      <img alt="" class="has" height="45" src="https://img-blog.csdn.net/20180726000058152?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE0NDQwNjI=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="363"></p>

<p>                  <img alt="" class="has" height="40" src="https://img-blog.csdn.net/2018072600012034?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE0NDQwNjI=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="690"></p>

<p><img alt="" class="has" height="289" src="https://img-blog.csdn.net/20180726000148326?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE0NDQwNjI=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="1200"></p>            </div>
                </div>