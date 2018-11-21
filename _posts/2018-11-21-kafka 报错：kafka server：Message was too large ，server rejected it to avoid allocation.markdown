---
layout:     post
title:      kafka 报错：kafka server：Message was too large ，server rejected it to avoid allocation
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章,转载请注明出处					https://blog.csdn.net/vkingnew/article/details/82254384				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <pre class="has">
<code>kafka报错信息：
kafka server：Message was too large ，server rejected it to avoid allocation 
kafka版本：
kafka 1.1.0版本。

原因分析：
查看kafka配置，默认单条消息最大为1M，当单条消息长度超过1M时，就会出现发送到broker
失败，从而导致消息在producer的队列中一直累积，直到撑爆生产者的内存。

于是赶紧修改kafka配置，解决问题。主要修改步骤如下：
1.修改kafka的broker配置：message.max.bytes（默认:1000000B），这个参数表示单条
消息的最大长度。在使用kafka的时候，应该预估单条消息的最大长度，不然导致发送失败。
2.修改kafka的broker配置：replica.fetch.max.bytes (默认: 1MB)，broker可复制的
消息的最大字节数。这个值应该比message.max.bytes大，否则broker会接收此消息，但无
法将此消息复制出去，从而造成数据丢失。
3.修改消费者程序端配置：fetch.message.max.bytes (默认 1MB) – 消费者能读取的最大
消息。这个值应该大于或等于message.max.bytes。如果不调节这个参数，就会导致消费者无
法消费到消息，并且不会爆出异常或者警告，导致消息在broker中累积，此处要注意。
需要考虑的问题：
 1.从性能上考虑：通过性能测试，kafka在消息为10K时吞吐量达到最大，更大的消息会降低
吞吐量，在设计集群的容量时，尤其要考虑这点。
2.可用的内存和分区数：Brokers会为每个分区分配replica.fetch.max.bytes参数指定的
内存空间，假设replica.fetch.max.bytes=1M，且有1000个分区，则需要差不多1G的内存，
确保 分区数*最大的消息不会超过服务器的内存，否则会报OOM错误。同样地，消费端
的fetch.message.max.bytes指定了最大消息需要的内存空间，同样，分区数*最大需要内存
空间不能超过服务器的内存。所以，如果你有大的消息要传送，则在内存一定的情况下，只
能使用较少的分区数或者使用更大内存的服务器。
3.垃圾回收：更大的消息会让GC的时间更长（因为broker需要分配更大的块），随时关注GC的
日志和服务器的日志信息。如果长时间的GC导致kafka丢失了zookeeper的会话，则需要配
置zookeeper.session.timeout.ms参数为更大的超时时间。


解决办法：

在kafka的文件server.properties中添加如下三个个配置项，均设置为标准的2G：

#broker能接收消息的最大字节数
message.max.bytes=2147483648
#broker可复制的消息的最大字节数
replica.fetch.max.bytes=2147483648
#消费者端的可读取的最大消息
fetch.message.max.bytes=2147483648

--上述设置会报错：
org.apache.kafka.common.config.ConfigException: Invalid value 2147483648 for configuration message.max.bytes: Not a number of type INT
at org.apache.kafka.common.config.ConfigDef.parseType(ConfigDef.java:722)
at org.apache.kafka.common.config.ConfigDef.parseValue(ConfigDef.java:469)
at org.apache.kafka.common.config.ConfigDef.parse(ConfigDef.java:462)
at org.apache.kafka.common.config.AbstractConfig.&lt;init&gt;(AbstractConfig.java:62)
at kafka.server.KafkaConfig.&lt;init&gt;(KafkaConfig.scala:986)
at kafka.server.KafkaConfig.&lt;init&gt;(KafkaConfig.scala:989)
at kafka.server.KafkaConfig$.fromProps(KafkaConfig.scala:969)
at kafka.server.KafkaServerStartable$.fromProps(KafkaServerStartable.scala:28)
at kafka.Kafka$.main(Kafka.scala:82)
at kafka.Kafka.main(Kafka.scala)

--正确的设置：
#broker能接收消息的最大字节数
message.max.bytes=200000000
#broker可复制的消息的最大字节数
replica.fetch.max.bytes=204857600
#消费者端的可读取的最大消息
fetch.message.max.bytes=204857600


或者在启动的时候加入到参数后面：
bin/kafka-topics.sh --zookeeper localhost:2181 --alter --topic my-topic --config max.message.bytes=128000


参考：
http://kafka.apache.org/11/documentation.html#brokerconfigs
</code></pre>

<p> </p>            </div>
                </div>