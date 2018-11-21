---
layout:     post
title:      flume同kafka的对接
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>大家：</p>

<p>  好！ flume同kafka的对接，请参考</p>

<p> </p>

<p>本质上是将flume作为kafka的生产者来说的，监控一个目录，kafka消费者显示</p>

<p>第一步：编辑flume_kafka的配置文件，是在flume的conf 目录下flume-kafka.sh（脚本在后面）</p>

<p>说明：此步前提是要先在kafka中创建一个名为kafkatest的topic，</p>

<p>第二步：启动flume脚本</p>

<pre class="has">
<code> bin/flume-ngagent -c conf -f conf/flume-kafka.sh -n agent</code></pre>

<p>说明：后面的-n 是agent的名称，我踩过一次坑</p>

<p>第三步：启动kafka的消费者的程序</p>

<pre class="has">
<code>kafka-console-consumer.sh --topic kafkatest--zookeeper hadoop:2181</code></pre>

<p>第四步：往日志文件中添加数据，观察kafka的客户端是否显示</p>

<p>  往日志文件中增加数据，可以是echo 的命令，也可以用crontab ，也可以用azkaba</p>

<p> </p>

<p> </p>

<p>---flume-kafka.sh的脚本如下所示：</p>

<pre class="has">
<code># 启动flume bin/flume-ng agent -c conf -f conf/flume-kafka.sh -n agent


agent.sources = s1                                                                                                                  
agent.channels = c1                                                                                                                 
agent.sinks = k1                                                                                                                    
                                                                                 
agent.sources.s1.type=exec                                                                                                          
agent.sources.s1.command=tail -F /root/test/abc.log                                                                                
agent.sources.s1.channels=c1                                                                                                        
agent.channels.c1.type=memory                                                                                                       
agent.channels.c1.capacity=10000                                                                                                    
agent.channels.c1.transactionCapacity=100 
#设置一个kafka接收器
agent.sinks.k1.type= org.apache.flume.sink.kafka.KafkaSink
#设置kafka的broker地址和端口号(所有的)
agent.sinks.k1.brokerList=192.168.16.100:9092
#设置kafka的topic
agent.sinks.k1.topic=kafkatest
#设置一个序列化方式
agent.sinks.k1.serializer.class=kafka.serializer.StringEncoder
#组装
agent.sinks.k1.channel=c1</code></pre>

<p> </p>            </div>
                </div>