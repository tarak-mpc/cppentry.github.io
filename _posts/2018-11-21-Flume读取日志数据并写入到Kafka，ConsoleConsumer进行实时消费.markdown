---
layout:     post
title:      Flume读取日志数据并写入到Kafka，ConsoleConsumer进行实时消费
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/qq_17776287/article/details/77972711				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-size:18px;">最近大数据学习使用了Flume、Kafka等，今天就实现一下Flume实时读取日志数据并写入到Kafka中，同时，让Kafka的ConsoleConsumer对日志数据进行消费。</span></p>
<p><span style="font-size:18px;"><br></span></p>
<p><span style="font-size:18px;"><strong>1、Flume</strong></span></p>
<p><span style="font-size:18px;"><span style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;text-align:justify;background-color:rgb(250,250,252);">Flume是一个完善、强大的日志采集工具，关于它的配置，在网上有很多现成的例子和资料，这里仅做简单说明不再详细赘述。</span><br></span></p>
<p><span style="font-size:18px;"><span style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;text-align:justify;background-color:rgb(250,250,252);">Flume包含Source、Channel、Sink三个最基本的概念，其相应关系如下图所示：</span></span></p>
<p><span style="font-size:18px;"><span style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;text-align:justify;background-color:rgb(250,250,252);"><img src="https://img-blog.csdn.net/20170914000948767?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMTc3NzYyODc=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></span></p>
<p><span style="font-size:18px;"><span style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;text-align:justify;background-color:rgb(250,250,252);"><strong>注：</strong></span></span></p>
<p><span style="font-size:18px;"><span style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;text-align:justify;background-color:rgb(250,250,252);"><span style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;text-align:justify;background-color:rgb(250,250,252);"><strong>Source——日志来源，</strong><span style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;text-align:justify;background-color:rgb(250,250,252);">其中包括：Avro
 Source、Thrift Source、Exec Source、JMS Source、Spooling Directory Source、Kafka Source、NetCat Source、Sequence Generator Source、Syslog Source、HTTP Source、Stress Source、Legacy Source、Custom Source、Scribe Source以及Twitter 1% firehose Source。</span></span></span></span></p>
<p><span style="font-size:18px;"><span style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;text-align:justify;background-color:rgb(250,250,252);"><span style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;text-align:justify;background-color:rgb(250,250,252);"><span style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;text-align:justify;background-color:rgb(250,250,252);"><strong>Channel——日志管道，</strong>所有从Source过来的日志数据都会以队列的形式存放在里面，它包括：Memory
 Channel、JDBC Channel、Kafka Channel、File Channel、Spillable Memory Channel、Pseudo Transaction Channel、Custom Channel。</span><br></span></span></span></p>
<p><span style="font-size:18px;"><span style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;text-align:justify;background-color:rgb(250,250,252);"><span style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;text-align:justify;background-color:rgb(250,250,252);"><strong>Sink——日志出口，</strong>日志将通过Sink向外发射，它包括：HDFS
 Sink、Hive Sink、Logger Sink、Avro Sink、Thrift Sink、IRC Sink、File Roll Sink、Null Sink、HBase Sink、Async HBase Sink、Morphline Solr Sink、Elastic Search Sink、Kite Dataset Sink、Kafka Sink、Custom Sink。</span></span></span></p>
<p><span style="font-size:18px;"><span style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;text-align:justify;background-color:rgb(250,250,252);"><span style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;text-align:justify;background-color:rgb(250,250,252);"><strong>基于Flume的日志采集是灵活的，即，我们可以将多个管道处理串联起来。</strong></span><br></span></span></p>
<p><span style="font-size:18px;"><span style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;text-align:justify;background-color:rgb(250,250,252);"><br></span></span></p>
<p><span style="font-size:18px;"><span style="color:rgb(51,51,51);font-family:tahoma, '宋体';text-align:justify;background-color:rgb(250,250,252);"><strong>2、前期准备</strong></span></span></p>
<p><span style="font-size:14px;"><strong>2.1 软件安装位置</strong></span></p>
<p><span style="font-size:14px;">flume:  /usr/local/share/applications/apache-flume-1.7.0-bin</span></p>
<p><span style="font-size:14px;">kafka:  /usr/local/share/applications/kafka_2.10-0.10.1.1</span></p>
<p><span style="font-size:14px;">（节点数这个无所谓，最少两个吧，master &amp; slave，博主有5个结点，哈哈）</span></p>
<p><span style="font-size:14px;"><br></span></p>
<p><span style="font-size:14px;"><strong>2.2 编写用于实时产生日志的shell文件</strong></span></p>
<p><span style="font-size:14px;">在/usr/local/share/applications/目录下执行如下操作：</span></p>
<p><span style="font-size:14px;"><span style="font-size:14px;">创建一个临时存放日志文件的目录:</span><br></span></p>
<p><span style="font-size:14px;"></span></p><pre><code class="language-plain">mkdir testdata</code></pre><pre><code class="language-plain">mkdir -p tmp/flumetokafka/logs/</code></pre><br><p><span style="font-size:14px;">接下来开始编写shell文件：</span></p>
<p><span style="font-size:14px;"></span></p><pre><code class="language-plain">vim output.sh</code></pre>在output.sh文件中，首先通过shift + : + i进入编辑模式，编写如下代码：<br><br><pre><code class="language-plain">for((i=5612; i&lt;6000; i++));
do
  touch $PWD/testdata/20170913-jangzhangz-$i.log
  echo 'When we will see you again. Put a little sunshine in your life.----'+$i &gt;&gt; $PWD/testdata/20170913-jangzhangz-$i.log
  mv $PWD/testdata/20170913-jangzhangz-$i.log $PWD/tmp/flumetokafka/logs/
done</code></pre><br>
这里需要说明一下：tmp/flumetokafka/logs/就是flume进行监听日志文件的数据目录
<p><span style="font-size:14px;"><br></span></p>
<p><strong><span style="font-size:18px;">3、为flume构建agent</span></strong></p>
<p><span style="font-size:14px;">首先进入flume所在目录下的conf目录，编写构建agent的配置文件(flume2kafka.properties):</span></p>
<p><span style="font-size:14px;">通过vim flume2kafka.properties命令编辑文件,同样通过shift + : + i进入编辑模式，在文件中写入如下配置信息：</span></p>
<p><span style="font-size:14px;"></span></p><pre><code class="language-plain">Flume2KafkaAgent.sources=mysource
Flume2KafkaAgent.channels=mychannel
Flume2KafkaAgent.sinks=mysink

Flume2KafkaAgent.sources.mysource.type=spooldir
Flume2KafkaAgent.sources.mysource.channels=mychannel
Flume2KafkaAgent.sources.mysource.spoolDir=/usr/local/share/applications/tmp/flumetokafka/logs

Flume2KafkaAgent.sinks.mysink.channel=mychannel
Flume2KafkaAgent.sinks.mysink.type=org.apache.flume.sink.kafka.KafkaSink
Flume2KafkaAgent.sinks.mysink.kafka.bootstrap.servers=master:9092,slave1:9092,slave2:9092,slave3:9092
Flume2KafkaAgent.sinks.mysink.kafka.topic=flume-data
Flume2KafkaAgent.sinks.mysink.kafka.batchSize=20
Flume2KafkaAgent.sinks.mysink.kafka.producer.requiredAcks=1

Flume2KafkaAgent.channels.mychannel.type=memory
Flume2KafkaAgent.channels.mychannel.capacity=30000
Flume2KafkaAgent.channels.mychannel.transactionCapacity=100
</code></pre><br>
然后通过shift + : wq保存文件。
<p><span style="font-size:14px;"><br></span></p>
<p><strong><span style="font-size:18px;">4、求证成果</span></strong></p>
<p><span style="font-size:14px;"><strong>4.1 启动flume agent</strong></span></p>
<p><span style="font-size:14px;">在flume根目录下：</span></p>
<p><span style="font-size:14px;"></span></p><pre><code class="language-plain">bin/flume-ng agent -c conf -f conf/flume2kafka.properties -n Flume2KafkaAgent -Dflume.root.logger=INFO,console
</code></pre><br><strong>4.2 启动kafka消费者</strong>
<p><span style="font-size:14px;">在kafka根目录下执行：</span></p>
<p><span style="font-size:14px;"></span></p><pre><code class="language-plain">bin/kafka-console-consumer.sh --zookeeper master:2181 --topic flume-data --from-beginning</code></pre><br>
或者在kafka的bin目录下执行：
<p><span style="font-size:14px;"></span></p><pre><code class="language-plain">./kafka-console-consumer.sh --zookeeper master:2181 --topic flume-data --from-beginning</code></pre>
<p><span style="font-size:14px;"><br></span></p>
<p><span style="font-size:14px;"><strong>4.3 生成日志</strong></span></p>
<p><span style="font-size:14px;">在/usr/local/share/applications/下，执行如下命令：</span></p>
<p><span style="font-size:14px;"></span></p><pre><code class="language-plain">./output.sh</code></pre>
<br><strong>4.4 查看terminal显示的消费记录情况</strong>
<p><span style="font-size:14px;">正常执行后，结果会出现类似情况：</span></p>
<p><span style="font-size:14px;"><img src="https://img-blog.csdn.net/20170914003408055?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMTc3NzYyODc=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></p>
<p><span style="font-size:14px;"><br></span></p>
<p><strong><span style="font-size:18px;">5、踩坑记录</span></strong></p>
<p><span style="font-size:14px;">针对flume的配置文件，如果要实现flume读取的日志数据写入kafka，则必须要配置Bootstrap Server信息。</span></p>
<p><span style="font-size:14px;"><br></span></p>
<p><span style="font-size:18px;"><strong>总结</strong></span></p>
<p><span style="font-size:18px;">1、了解如何构建flume读取日志数据并能够写入kafka的架构</span></p>
<p><span style="font-size:18px;">2、能够借助于shell文件的编写，从而简化工作量，并能友好的监测到实时数据</span></p>
<p><span style="font-size:18px;">3、flume配置文件的编写，source —— channel —— sink</span></p>
<p><span style="font-size:18px;">4、启动服务，进行成果检测</span></p>
<p><span style="font-size:18px;">5、踩坑记录</span></p>
<p><span style="font-size:18px;"><br></span></p>
<p><span style="font-size:18px;">希望对各位有帮助，博主设计，仅供参考！又到这个时间点了，睡觉了，晚安！</span></p>
            </div>
                </div>