---
layout:     post
title:      Kafka和flume整合------flume的配置文件
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：请注明出处：https://www.cnblogs.com/meiLinYa/					https://blog.csdn.net/meiLin_Ya/article/details/82449552				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>首先说明一点我的kafka在整合中是接受生产的，也就是flume生产数据，kafka用sink接受数据</p>

<p>flume配置如下</p>

<pre class="has">
<code>agent.sources = s1                                                                                                                  
agent.channels = c1                                                                                                                 
agent.sinks = k1                                                                                                                                                                                                                                                      
agent.sources.s1.type=exec                                                                                                          
agent.sources.s1.command=tail -F /home/data/log.txt                                                                              
agent.sources.s1.channels=c1                                                                                                        
agent.channels.c1.type=memory                                                                                                       
agent.channels.c1.capacity=10000                                                                                                    
agent.channels.c1.transactionCapacity=100                                                                                                                                                                                                                            
#设置Kafka接收器                                                                                                                    
agent.sinks.k1.type= org.apache.flume.sink.kafka.KafkaSink                                                                          
#设置Kafka的broker地址和端口号                                                                                                      
agent.sinks.k1.brokerList=hu-hadoop1:9091                                                                                              
#设置Kafka的Topic                                                                                                                   
agent.sinks.k1.topic=kafkatest                                                                                                      
#设置序列化方式                                                                                                                     
agent.sinks.k1.serializer.class=kafka.serializer.StringEncoder                                                                         agent.sinks.k1.requiredAcks=1
agent.sinks.k1.batchSize=20                                                                                                            agent.sinks.k1.channel=c1</code></pre>

<p> </p>            </div>
                </div>