---
layout:     post
title:      linux集成 kafka数据通过flume发送到hadoop
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，转载请注明原地址					https://blog.csdn.net/a123demi/article/details/72643897				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div style="text-indent:28px;line-height:1.75;"><span style="font-family:'Microsoft YaHei';font-size:18px;">上一篇文章《<a href="http://blog.csdn.net/a123demi/article/details/72637847" rel="nofollow"> linux安装flume和集成kafka测试</a>》<span style="color:rgb(57,57,57);">，我们介绍了flume安装和集成数据到kafka，本篇文章我们将集成kafka,flume,hadoop，通过flume发送kafka数据到hadoop的hdfs文件。</span></span></div>
<h1 style="line-height:1.75;"><span style="font-family:'Microsoft YaHei';font-size:18px;">一.前期准备</span></h1>
<h3 style="line-height:1.75;"><span style="font-family:'Microsoft YaHei';font-size:18px;">1.1 hadoop安装</span></h3>
<div style="text-indent:28px;line-height:1.75;"><span style="font-family:'Microsoft YaHei';font-size:18px;">版本：Hadoop 2.6.5</span></div>
<div style="text-indent:28px;line-height:1.75;"><span style="font-family:'Microsoft YaHei';font-size:18px;">安装：<a href="http://blog.csdn.net/a123demi/article/details/70652959" rel="nofollow"> linux hadoop完全分布式集群搭建图文详解</a></span></div>
<h3 style="line-height:1.75;"><span style="font-family:'Microsoft YaHei';font-size:18px;">1.2 zookeeper安装</span></h3>
<div style="text-indent:28px;line-height:1.75;"><span style="font-family:'Microsoft YaHei';font-size:18px;"><span style="color:rgb(57,57,57);">版本：</span><span style="color:rgb(47,47,47);">zookeeper-3.4.9</span></span></div>
<div style="text-indent:28px;line-height:1.75;"><span style="color:rgb(57,57,57);"><span style="font-family:'Microsoft YaHei';font-size:18px;">安装：<a href="http://blog.csdn.net/a123demi/article/details/70267294" rel="nofollow">
 linux zookeeper3.4.9集群搭建图文详解</a></span></span></div>
<h3 style="line-height:1.75;"><span style="font-family:'Microsoft YaHei';font-size:18px;">1.3 kafka安装</span></h3>
<div style="text-indent:28px;line-height:1.75;"><span style="font-family:'Microsoft YaHei';font-size:18px;"><span style="color:rgb(57,57,57);">版本：</span><span style="color:rgb(47,47,47);">kafka_2.10-0.10.1.0</span></span></div>
<div style="text-indent:28px;line-height:1.75;"><span style="color:rgb(57,57,57);"><span style="font-family:'Microsoft YaHei';font-size:18px;">安装：<a href="http://blog.csdn.net/a123demi/article/details/70279296" rel="nofollow">
 linux kafka集群配置和测试图文详解</a></span></span></div>
<h3 style="line-height:1.75;"><span style="font-family:'Microsoft YaHei';font-size:18px;">1.4 flume安装</span></h3>
<div style="text-indent:28px;line-height:1.75;"><span style="font-family:'Microsoft YaHei';font-size:18px;"><span style="color:rgb(57,57,57);">版本：</span>apache-flume-1.7.0</span></div>
<div style="text-indent:28px;line-height:1.75;"><span style="color:rgb(57,57,57);"><span style="font-family:'Microsoft YaHei';font-size:18px;">安装：<a href="http://blog.csdn.net/a123demi/article/details/72637847" rel="nofollow">
 linux安装flume和集成kafka测试</a></span></span></div>
<div style="text-indent:28px;line-height:1.75;"><span style="font-family:'Microsoft YaHei';font-size:18px;"><br></span></div>
<h1 style="line-height:1.75;"><span style="font-family:'Microsoft YaHei';font-size:18px;">二.配置flume-conf</span></h1>
<div style="text-indent:28px;line-height:1.75;"><pre><code class="language-html">#source的名字
agent.sources = kafkaSource
# channels的名字，建议按照type来命名
agent.channels = memoryChannel
# sink的名字，建议按照目标来命名
agent.sinks = hdfsSink

# 指定source使用的channel名字
agent.sources.kafkaSource.channels = memoryChannel
# 指定sink需要使用的channel的名字,注意这里是channel
agent.sinks.hdfsSink.channel = memoryChannel

#-------- kafkaSource相关配置-----------------
# 定义消息源类型
agent.sources.kafkaSource.type = org.apache.flume.source.kafka.KafkaSource
# 定义kafka所在zk的地址
agent.sources.kafkaSource.zookeeperConnect = 192.168.32.128:2181,192.168.32.131:2181,192.168.32.132:2181
# 配置消费的kafka topic
agent.sources.kafkaSource.topic=flumeTest2
# 配置消费者组的id
agent.sources.kafkaSource.groupId = flume
# 消费超时时间,参照如下写法可以配置其他所有kafka的consumer选项。注意格式从kafka.xxx开始是consumer的配置属性
#agent.sources.kafkaSource.kafka.consumer.timeout.ms = 100

#------- memoryChannel相关配置-------------------------
# channel类型
agent.channels.memoryChannel.type = memory
# channel存储的事件容量
agent.channels.memoryChannel.capacity=1000
# 事务容量
agent.channels.memoryChannel.transactionCapacity=100

#---------hdfsSink 相关配置------------------
agent.sinks.hdfsSink.type = hdfs
agent.sinks.hdfsSink.hdfs.path = hdfs://Master:9000/usr/hive/warehouse/test</code></pre><br></div>
<h1 style="line-height:1.75;"><span style="font-family:'Microsoft YaHei';font-size:18px;">三.kafka,flume,hadoop集成测试</span></h1>
<h3 style="line-height:1.75;"><span style="font-family:'Microsoft YaHei';font-size:18px;">3.1 hadoop操作</span></h3>
<h4 style="line-height:1.75;"><span style="font-family:'Microsoft YaHei';font-size:18px;">3.1.1 启动dfs+yarn</span></h4>
<div style="line-height:1.75;"><pre><code class="language-html">sbin/start-all.sh</code></pre></div>
<h4 style="line-height:1.75;"><span style="font-family:'Microsoft YaHei';font-size:18px;">3.1.2 新建/usr/hive/warehouse/test文件并赋予权限</span></h4>
<div style="line-height:1.75;"><pre><code class="language-html">hadoop fs -mkdir /usr/hive/warehouse/test
hadoop dfs -chmod g+w /usr/hive/warehouse/test</code></pre></div>
<h4 style="line-height:1.75;"><span style="font-family:'Microsoft YaHei';font-size:18px;">3.1.3 查看创建文件</span></h4>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;"></span></div>
<div style="line-height:1.75;"><span style="font-family:'Microsoft YaHei';font-size:18px;"><img src="https://img-blog.csdn.net/20170523160347616?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYTEyM2RlbWk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></div>
<h3 style="line-height:1.75;"><span style="font-family:'Microsoft YaHei';font-size:18px;">3.2 kafka操作</span></h3>
<h4 style="line-height:1.75;"><span style="font-family:'Microsoft YaHei';font-size:18px;">3.2.1 创建主题flumeTest2</span></h4>
<div style="line-height:1.75;"><pre><code class="language-html">bin/kafka-topics.sh --create --zookeeper 192.168.32.128:2181,192.168.32.131:2181,192.168.32.132:2181 --replication-factor 3 --partitions 3 --topic flumeTest2</code></pre></div>
<h4 style="line-height:1.75;"><span style="font-family:'Microsoft YaHei';font-size:18px;">3.2.2 kafka生产者</span></h4>
<div style="line-height:1.75;"><pre><code class="language-html">bin/kafka-console-producer.sh --broker-list 192.168.32.128:9092,192.168.32.131:9092,192.168.32.132:9092 --topic flumeTest2</code></pre></div>
<h4 style="line-height:1.75;"><span style="font-family:'Microsoft YaHei';font-size:18px;">3.2.3 kafka消费者</span></h4>
<div style="line-height:1.75;"><pre><code class="language-html">bin/kafka-console-consumer.sh --zookeeper 192.168.32.128:2181,192.168.32.131:2181,192.168.32.132:2181 --topic flumeTest2 --from-beginning</code></pre></div>
<h3 style="line-height:1.75;"><span style="font-family:'Microsoft YaHei';font-size:18px;">3.3 flume操作</span></h3>
<div style="line-height:1.75;"><pre><code class="language-html">bin/flume-ng agent -n agent -c conf -f conf/flume-conf.properties -Dflume.root.logger=INFO,console</code></pre><br></div>
<div style="line-height:1.75;"><span style="font-family:'Microsoft YaHei';font-size:18px;"><img src="https://img-blog.csdn.net/20170523160354277?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYTEyM2RlbWk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;"></span></div>
<h3 style="line-height:1.75;"><span style="font-family:'Microsoft YaHei';font-size:18px;">3.4 测试结果</span></h3>
<div style="line-height:1.75;"><span style="font-family:'Microsoft YaHei';font-size:18px;"><img src="https://img-blog.csdn.net/20170523160358433?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYTEyM2RlbWk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></div>
<div style="line-height:1.75;">
<h1 style="line-height:1.75;"><span style="font-family:'Microsoft YaHei';font-size:18px;">四. 字符串输出实现集成</span></h1>
<div style="line-height:1.75;"><span style="font-family:'Microsoft YaHei';font-size:18px;">上节中实现集成的方式，输出的结果有乱码，类似二进制输出</span></div>
<div style="line-height:1.75;"><span style="font-family:'Microsoft YaHei';font-size:18px;"><img src="https://img-blog.csdn.net/20170523170350571?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYTEyM2RlbWk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;"></span></div>
<div style="line-height:1.75;"><span style="font-family:'Microsoft YaHei';font-size:18px;">本节将换一种方式实现flume,kafka,hadoop集成。</span></div>
<h3 style="line-height:1.75;"><span style="font-family:'Microsoft YaHei';font-size:18px;">4.1 flume-conf 配置</span></h3>
<div style="line-height:1.75;"><span style="font-family:'Microsoft YaHei';font-size:18px;"></span><pre><code class="language-html">tier1.sources  = source1
 tier1.channels = channel1
 tier1.sinks = sink1
 
 tier1.sources.source1.type = org.apache.flume.source.kafka.KafkaSource
 tier1.sources.source1.zookeeperConnect = 192.168.32.128:2181,192.168.32.131:2181,192.168.32.132:2181
 tier1.sources.source1.topic = flumeTest2
 tier1.sources.source1.groupId = flume
 tier1.sources.source1.channels = channel1
 tier1.sources.source1.interceptors = i1
 tier1.sources.source1.interceptors.i1.type = timestamp
 tier1.sources.source1.kafka.consumer.timeout.ms = 1000
 
 tier1.channels.channel1.type = memory
 tier1.channels.channel1.capacity = 10000
 tier1.channels.channel1.transactionCapacity = 1000
 
 tier1.sinks.sink1.type = hdfs
 tier1.sinks.sink1.hdfs.path = /tmp/kafka/%{topic}/%y-%m-%d
 tier1.sinks.sink1.hdfs.rollInterval = 5
 tier1.sinks.sink1.hdfs.rollSize = 0
 tier1.sinks.sink1.hdfs.rollCount = 0
 tier1.sinks.sink1.hdfs.fileType = DataStream
 tier1.sinks.sink1.channel = channel1</code></pre></div>
<h3 style="line-height:1.75;"><span style="font-family:'Microsoft YaHei';font-size:18px;">4.2 启动flume</span></h3>
<div style="line-height:1.75;"><pre><code class="language-html">bin/flume-ng agent -n tier1 -c conf -f conf/flume-conf.properties -Dflume.root.logger=INFO,console</code></pre></div>
<h3 style="line-height:1.75;"><span style="font-family:'Microsoft YaHei';font-size:18px;">4.3 测试结果</span></h3>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;"><img src="https://img-blog.csdn.net/20170523170355431?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYTEyM2RlbWk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;"><img src="https://img-blog.csdn.net/20170523171050658?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYTEyM2RlbWk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;"></span></div>
<div style="font-family:'Microsoft YaHei';font-size:14px;line-height:1.75;">
<br></div>
<div style="font-family:'Microsoft YaHei';font-size:18px;"></div>
<div style="font-family:'Microsoft YaHei';font-size:14px;line-height:1.75;">
<br></div>
<span style="font-family:'Microsoft YaHei';font-size:18px;"><br></span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;"></span></div>
<div style="line-height:1.75;font-size:14px;"><br></div>
            </div>
                </div>