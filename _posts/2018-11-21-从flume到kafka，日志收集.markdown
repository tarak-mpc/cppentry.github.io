---
layout:     post
title:      从flume到kafka，日志收集
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/GG584741/article/details/51539991				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                  实时日志分析：
<p><img src="http://images2015.cnblogs.com/blog/751312/201602/751312-20160229112556517-1598213272.png" alt=""></p>
<p> </p>
<p>本篇文章主要测试 从flume到kafka的日志收集，storm日志分析，学习中！</p>
<p> </p>
<p><strong>flume 配置文件</strong></p>
<div class="cnblogs_code">
<div class="cnblogs_code">
<pre><span style="color:#000000;">#collector
collector.sources</span>=<span style="color:#000000;">cs
collector.sinks</span>=<span style="color:#000000;">ck hbaseSink
collector.channels</span>=<span style="color:#0000ff;">cc</span><span style="color:#000000;"> hbaseChannel

collector.sources.cs.type </span>=<span style="color:#000000;"> exec
collector.sources.cs.command </span>= <span style="color:#0000ff;">tail</span> -F /data/hudonglogs/self/<span style="color:#000000;">channel.log
collector.sources.cs.channels</span>=<span style="color:#0000ff;">cc</span><span style="color:#000000;"> hbaseChannel

collector.channels.</span><span style="color:#0000ff;">cc</span>.type =<span style="color:#000000;"> memory
collector.channels.</span><span style="color:#0000ff;">cc</span>.capacity = <span style="color:#800080;">1000</span><span style="color:#000000;">
collector.channels.</span><span style="color:#0000ff;">cc</span>.transactionCapacity = <span style="color:#800080;">100</span><span style="color:#000000;">

collector.channels.hbaseChannel.type </span>=<span style="color:#000000;"> memory
collector.channels.hbaseChannel.capacity </span>= <span style="color:#800080;">1000</span><span style="color:#000000;">
collector.channels.hbaseChannel.transactionCapacity </span>= <span style="color:#800080;">100</span><span style="color:#000000;">

#sink kafka
collector.sinks.ck.type </span>=<span style="color:#000000;"> org.apache.flume.sink.kafka.KafkaSink
collector.sinks.ck.topic </span>=<span style="color:#000000;"> logs
collector.sinks.ck.brokerList </span>= localhost:<span style="color:#800080;">9092</span><span style="color:#000000;">
collector.sinks.ck.requiredAcks </span>= <span style="color:#800080;">1</span><span style="color:#000000;">
collector.sinks.ck.batchSize </span>= <span style="color:#800080;">20</span><span style="color:#000000;">
collector.sinks.ck.channel </span>= <span style="color:#0000ff;">cc</span><span style="color:#000000;">

#hbase sink
collector.sinks.hbaseSink.type </span>=<span style="color:#000000;"> asynchbase
collector.sinks.hbaseSink.channel </span>=<span style="color:#000000;"> hbaseChannel
collector.sinks.hbaseSink.table </span>=<span style="color:#000000;"> logs
collector.sinks.hbaseSink.columnFamily </span>=<span style="color:#000000;"> content
collector.sinks.hbaseSink.batchSize </span>= <span style="color:#800080;">5</span></pre>
</div>
<pre></pre>
</div>
<p> 注意： flume中每一个source可以有多个channel，但是一个sink只能对应一个channel。</p>
<p><strong>kafka consumer</strong></p>
<div class="cnblogs_code">
<pre><span style="color:#0000ff;">public</span> <span style="color:#0000ff;">class</span> KafkaConsumer <span style="color:#0000ff;">extends</span><span style="color:#000000;"> Thread {

    ConsumerConnector connector;

    </span><span style="color:#0000ff;">private</span><span style="color:#000000;"> String topic;

    </span><span style="color:#0000ff;">public</span><span style="color:#000000;"> KafkaConsumer(String topic) {
        </span><span style="color:#0000ff;">this</span>.topic =<span style="color:#000000;"> topic;
        </span><span style="color:#0000ff;">this</span>.connector =<span style="color:#000000;"> Consumer.createJavaConsumerConnector(createConsumerConfig());
    }

    </span><span style="color:#0000ff;">private</span><span style="color:#000000;"> ConsumerConfig createConsumerConfig() {
        Properties props </span>= <span style="color:#0000ff;">new</span><span style="color:#000000;"> Properties();
        props.put(</span>"zookeeper.connect"<span style="color:#000000;">, KafkaConfig.zkConnect);
        props.put(</span>"group.id"<span style="color:#000000;">, KafkaConfig.groupId);
        props.put(</span>"zookeeper.session.timeout.ms", "40000"<span style="color:#000000;">);
        props.put(</span>"zookeeper.sync.time.ms", "200"<span style="color:#000000;">);
        props.put(</span>"auto.commit.interval.ms", "1000"<span style="color:#000000;">);
        </span><span style="color:#0000ff;">return</span> <span style="color:#0000ff;">new</span><span style="color:#000000;"> ConsumerConfig(props);
    }

    @Override
    </span><span style="color:#0000ff;">public</span> <span style="color:#0000ff;">void</span><span style="color:#000000;"> run() {
        Map</span>&lt;String, Integer&gt; topicCountMap = <span style="color:#0000ff;">new</span> HashMap&lt;String, Integer&gt;<span style="color:#000000;">();
        topicCountMap.put(topic, </span><span style="color:#0000ff;">new</span> Integer(1<span style="color:#000000;">));
        Map</span>&lt;String, List&lt;KafkaStream&lt;<span style="color:#0000ff;">byte</span>[], <span style="color:#0000ff;">byte</span>[]&gt;&gt;&gt; consumerMap =<span style="color:#000000;"> connector.createMessageStreams(topicCountMap);
        KafkaStream</span>&lt;<span style="color:#0000ff;">byte</span>[], <span style="color:#0000ff;">byte</span>[]&gt; stream = consumerMap.get(topic).get(0<span style="color:#000000;">);
        ConsumerIterator</span>&lt;<span style="color:#0000ff;">byte</span>[], <span style="color:#0000ff;">byte</span>[]&gt; it =<span style="color:#000000;"> stream.iterator();
        </span><span style="color:#0000ff;">while</span><span style="color:#000000;"> (it.hasNext()) {
            System.out.println(</span>"receive：" + <span style="color:#0000ff;">new</span><span style="color:#000000;"> String(it.next().message()));
            </span><span style="color:#0000ff;">try</span><span style="color:#000000;"> {
                sleep(</span>3000<span style="color:#000000;">);
            } </span><span style="color:#0000ff;">catch</span><span style="color:#000000;"> (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}</span></pre>
</div>
<p> </p>
<p>启动 kafka集群， 然后启动producer，启动flume</p>
<p><img src="http://images2015.cnblogs.com/blog/751312/201602/751312-20160229113227142-421706054.png" alt=""></p>
<p> </p>
<p>hbase查看：</p>
<p><img src="http://images2015.cnblogs.com/blog/751312/201602/751312-20160229152306455-1320053565.png" alt=""></p>
<p> </p>
<p>以上所有环境都是单节点部署！</p>
<p> </p>            </div>
                </div>