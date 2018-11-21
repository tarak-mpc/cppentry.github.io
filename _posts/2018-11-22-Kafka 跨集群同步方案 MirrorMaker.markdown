---
layout:     post
title:      Kafka 跨集群同步方案 MirrorMaker
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div class="exp-content-block" style="color:rgb(51,51,51);font-size:16px;line-height:28px;">
<div class="exp-content-body exp-brief-step" style="text-align:justify;">
<div class="exp-content-listblock">
<div class="content-listblock-text">
<p>
该方案解决Kafka跨集群同步、创建Kafka集群镜像等相关问题，主要使用Kafka内置的MirrorMaker工具实现。</p>
<p>
</p>
<p>
Kafka镜像即已有Kafka集群的副本。下图展示如何使用MirrorMaker工具创建从源Kafka集群（source cluster）到目标Kafka集群（target cluster）的镜像。该工具通过Kafka consumer从源Kafka集群消费数据，然后通过一个内置的Kafka producer将数据重新推送到目标Kafka集群。</p>
<p>
</p>
</div>
<div class="content-listblock-media">
<div class="content-listblock-image" style="text-align:center;"><img class="exp-image-default" alt="Kafka 跨集群同步方案——MirrorMaker" src="http://d.hiphotos.baidu.com/exp/w=500/sign=d958b0e7d4ca7bcb7d7bc72f8e096b3f/cb8065380cd791233c8b1d50ae345982b2b780af.jpg" style="border:0px;display:block;"></div>
<div class="exp-brief-clear" style="clear:both;overflow:hidden;"></div>
</div>
</div>
</div>
</div>
<div class="exp-content-block" style="color:rgb(51,51,51);font-size:16px;line-height:28px;">
<h2 class="exp-content-head" style="font-size:22px;font-weight:400;line-height:24px;font-family:'微软雅黑';border-bottom-width:1px;border-bottom-style:solid;border-bottom-color:rgb(214,214,216);">
<a name="section-2" style="color:rgb(45,100,179);"></a>方法/步骤</h2>
<div class="exp-content-body" style="text-align:justify;">
<ul class="exp-content-unorderlist unorder-list-ui" style="list-style:none;clear:both;border-left-width:2px;border-left-style:dotted;border-left-color:rgb(228,228,228);"><li class="exp-content-list list-item-1" style="display:block;min-height:31px;background-color:transparent;">
<div class="content-list-text">
<p>
<strong>如何创建镜像</strong></p>
<p>
使用MirrorMaker创建镜像是比较简单的，搭建好目标Kafka集群后，只需要启动mirror-maker程序即可。其中，一个或多个consumer配置文件、一个producer配置文件是必须的，whitelist、blacklist是可选的。在consumer的配置中指定源Kafka集群的Zookeeper，在producer的配置中指定目标集群的Zookeeper（或者broker.list）。</p>
<p>
</p>
<p>
</p>
<p>
<em>kafka-run-class.sh kafka.tools.MirrorMaker --consumer.config sourceCluster1Consumer.config --consumer.config sourceCluster2Consumer.config --num.streams 2 --producer.config targetClusterProducer.config --whitelist=".*"</em></p>
<p>
</p>
<p>
例如，你需要创建S集群的镜像，目标集群T已经搭建好，简单的做法如下：</p>
<p>
</p>
<p>
1. 创建consumer配置文件：sourceClusterConsumer.config</p>
<p>
</p>
<p>
</p>
<p>
</p>
<p>
<em>zk.connect=szk0:2181,szk1:2181,szk2:2181</em></p>
<p>
<em>groupid=test-mirror-consumer-group</em></p>
<p>
<em></em></p>
<p>
</p>
<p>
</p>
<p>
</p>
<p>
</p>
<p>
 2. 创建producer配置文件：targetClusterProducer.config</p>
<p>
</p>
<p>
</p>
<p>
</p>
<p>
<em>zk.connect=tzk0:2181,tzk1:2181</em></p>
<p>
<em></em></p>
<p>
</p>
<p>
</p>
<p>
</p>
<p>
</p>
<p>
 3. 创建启动脚本：start.sh</p>
<p>
</p>
<p>
</p>
<p>
</p>
<p>
<em>$KAFKA_HOME/bin/kafka-run-class.sh kafka.tools.MirrorMaker --consumer.config sourceClusterConsumer.config --num.streams 2 --producer.config targetClusterProducer.config --whitelist=".*"</em></p>
<p>
<em></em></p>
<p>
</p>
<p>
</p>
<p>
</p>
<p>
</p>
<p>
 4. 执行脚本</p>
<p>
执行start.sh通过日志信息查看运行状况，到目标Kafka集群的log.dir中即可看到同步过来的数据。</p>
<p>
</p>
</div>
</li><li class="exp-content-list list-item-2" style="display:block;min-height:31px;background-color:transparent;">
<div class="content-list-text">
<p>
<strong>MirrorMaker的参数说明</strong></p>
<p>
</p>
<p>
</p>
<p>
<em></em></p>
<p>
<em>$KAFKA_HOME/bin/kafka-run-class.sh kafka.tools.MirrorMaker --help</em></p>
<p>
<em></em></p>
<p>
</p>
<p>
</p>
<p>
执行上面的命令就可以看到各个参数的说明：</p>
<p>
</p>
<p>
1. 白名单(whitelist) 黑名单(blacklist)</p>
<p>
mirror-maker接受精确指定同步topic的白名单和黑名单。使用java标准的正则表达式，为了方便，逗号(‘,’)被编译为java正则中的(‘|’)。</p>
<p>
</p>
<p>
2. Producer timeout</p>
<p>
为了支持高吞吐量，你最好使用异步的内置producer，并将内置producer设置为阻塞模式（queue.enqueueTimeout.ms=-1）。这样可以保证数据(messages)不会丢失。否则，异步producer默认的 enqueueTimeout是0，如果producer内部的队列满了，数据(messages)会被丢弃，并抛出QueueFullExceptions异常。而对于阻塞模式的producer，如果内部队列满了就会一直等待，从而有效的节制内置consumer的消费速度。你可以打开producer的的trace
 logging，随时查看内部队列剩余的量。如果producer的内部队列长时间处于满的状态，这说明对于mirror-maker来说，将消息重新推到目标Kafka集群或者将消息写入磁盘是瓶颈。</p>
<p>
对于kafka的producer同步异步的详细配置请参考$KAFKA_HOME/config/producer.properties文件。关注其中的producer.type和queue.enqueueTimeout.ms这两个字段。</p>
<p>
</p>
<p>
3. Producer 重试次数（retries）</p>
<p>
如果你在producer的配置中使用broker.list，你可以设置当发布数据失败时候的重试次数。retry参数只在使用broker.list的时候使用，因为在重试的时候会重新选择broker。</p>
<p>
</p>
<p>
4. Producer 数量</p>
<p>
通过设置—num.producers参数，可以使用一个producer池来提高mirror maker的吞吐量。在接受数据(messages)的broker上的producer是只使用单个线程来处理的。就算你有多个消费流，吞吐量也会在producer处理请求的时候被限制。</p>
<p>
</p>
<p>
5. 消费流（consumption streams）数量</p>
<p>
使用—num.streams可以指定consumer的线程数。请注意，如果你启动多个mirror maker进程，你可能需要看看其在源Kafka集群partitions的分布情况。如果在每个mirror maker进程上的消费流（consumption streams）数量太多，某些消费进程如果不拥有任何分区的消费权限会被置于空闲状态，主要原因在于consumer的负载均衡算法。</p>
<p>
</p>
<p>
6. 浅迭代（Shallow iteration）与producer压缩</p>
<p>
我们建议在mirror maker的consumer中开启浅迭代（shallow iteration)。意思就是mirror maker的consumer不对已经压缩的消息集（message-sets）进行解压，只是直接将获取到的消息集数据同步到producer中。</p>
<p>
如果你开启浅迭代（shallow iteration)，那么你必须关闭mirror maker中producer的压缩功能，否则消息集（message-sets）会被重复压缩。</p>
<p>
</p>
<p>
7. Consumer 和 源Kafka集群（source cluster）的 socket buffer sizes</p>
<p>
镜像经常用在跨集群场景中，你可能希望通过一些配置选项来优化内部集群的通信延迟和特定硬件性能瓶颈。一般来说，你应该对mirror-maker中consumer的socket.buffersize 和源集群broker的socket.send.buffer设定一个高的值。此外，mirror-maker中消费者（consumer）的fetch.size应该设定比socket.buffersize更高的值。注意，套接字缓冲区大小（socket buffer size）是操作系统网络层的参数。如果你启用trace级别的日志，你可以检查实际接收的缓冲区大小（buffer
 size），以确定是否调整操作系统的网络层。</p>
<p>
</p>
</div>
<div class="content-list-media">
<div class="content-list-image"><img class="exp-image-default" alt="Kafka 跨集群同步方案——MirrorMaker" src="http://c.hiphotos.baidu.com/exp/w=500/sign=2306893ec55c1038247ecec28211931c/d4628535e5dde7115ab9cad2a4efce1b9d1661af.jpg" style="border:0px;display:block;"></div>
</div>
</li><li class="exp-content-list list-item-3" style="display:block;min-height:31px;background-color:transparent;">
<div class="content-list-text" style="font-family:'Microsoft Yahei', '微软雅黑', arial, '宋体', sans-serif;">
<p>
<strong>如何检验MirrorMaker运行状况</strong></p>
<p>
Consumer offset checker工具可以用来检查镜像对源集群的消费进度。例如：</p>
<p>
</p>
<p>
<em>bin/kafka-run-class.sh kafka.tools.ConsumerOffsetChecker --group KafkaMirror --zkconnect localhost:2181 --topic test-topic</em></p>
<p>
<em>KafkaMirror,topic1,0-0 (Group,Topic,BrokerId-PartitionId)</em></p>
<p>
<em>            Owner = KafkaMirror_jkoshy-ld-1320972386342-beb4bfc9-0</em></p>
<p>
<em>  Consumer offset = 561154288</em></p>
<p>
<em>                  = 561,154,288 (0.52G)</em></p>
<p>
<em>         Log size = 2231392259</em></p>
<p>
<em>                  = 2,231,392,259 (2.08G)</em></p>
<p>
<em>     Consumer lag = 1670237971</em></p>
<p>
<em>                  = 1,670,237,971 (1.56G)</em></p>
<p>
<em>BROKER INFO</em></p>
<p>
<em>0 -&gt; 127.0.0.1:9092</em></p>
<p>
</p>
<p>
注意，–zkconnect参数需要指定到源集群的Zookeeper。另外，如果指定topic没有指定，则打印当前消费者group下所有topic的信息。</p>
</div>
<div class="last-item" style="font-family:'Microsoft Yahei', '微软雅黑', arial, '宋体', sans-serif;">
</div>
</li><li class="exp-content-list list-item-3" style="display:block;min-height:31px;background-color:transparent;">
<p>
官网资料</p>
</li><li class="exp-content-list list-item-3" style="display:block;min-height:31px;background-color:transparent;">
<p>
<span style="color:rgb(51,51,51);font-size:16px;line-height:28px;text-align:justify;"><a href="https://cwiki.apache.org/confluence/display/KAFKA/Kafka+mirroring" rel="nofollow">https://cwiki.apache.org/confluence/display/KAFKA/Kafka+mirroring</a></span></p>
</li><li class="exp-content-list list-item-3" style="display:block;min-height:31px;background-color:transparent;">
<p>
<a href="https://cwiki.apache.org/confluence/display/KAFKA/Kafka+mirroring+(MirrorMaker)#Kafkamirroring(MirrorMaker)-Whitelistorblacklist" rel="nofollow">https://cwiki.apache.org/confluence/display/KAFKA/Kafka+mirroring+(MirrorMaker)#Kafkamirroring(MirrorMaker)-Whitelistorblacklist</a></p>
</li><li class="exp-content-list list-item-3" style="display:block;min-height:31px;background-color:transparent;">
<p>
<br></p>
</li></ul></div>
</div>
            </div>
                </div>