---
layout:     post
title:      apache kafka系列之broker内部架构
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/lizhitao/article/details/38352947				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-size:14px;">下面介绍kafka broker的主要子模块，帮助您更好地学习并理解kafka源代码和架构。</span></p>
<p><span style="font-size:14px;">如下介绍几个子模块：</span></p>
<p></p>
<ul><li><span style="font-size:12px;">Kafka API layer</span></li><li><span style="font-size:12px;">LogManager and Log</span></li><li><span style="font-size:12px;">ReplicaManager</span></li><li><span style="font-size:12px;">ZookeeperConsumerConnector</span></li><li><span style="font-size:12px;">service Schedule</span></li></ul><div><span style="font-size:12px;">如下是系统几个模块如何组成到一起架构图：</span></div>
<div><img src="https://img-blog.csdn.net/20140803144704215?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGl6aGl0YW8=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br></div>
<div><span style="font-size:14px;">启动过程<br>
zkClient 初始化=&gt;logManager  start()=&gt; socketServer start()=&gt;requestHandlerPool 初始化=&gt;replicaManager start() =&gt; <br>
kafkaController start() =&gt;topicConfigManager start() =&gt; kafkaHealthcheck start()<br>
Broker的核心IO处理在requestHandlerPool  这里面<br><br>
zkClient初始化<br>
利用第三方的zkclient初始与zk之间的链接，zkclient负责与zk的connection，session的维护。<br><br>
logManager 初始化<br>
所有的读写请求都将实例化各自的log实例对象。同时，后台的log线程将负责维护partitions与log segment。<br><br>
socketServer初始化<br>
NIO socket server<br>
*   1 Acceptor thread 负责接收请求，并转给Processor threads 进行处理<br>
*   N Processor threads 负责每个socket请求的实际处理<br><br><br>
•M Handler threads 负责事件机制催每个Processor 的请求与product请求的消息回复。<br><br>
replicaManager初始化<br>
•负责该broker的partition副本的管理工作<br><br>
kafkaController初始化<br>
负责该broker的Controller状态的管理工作<br><br>
topicConfigManager初始化<br>
负责该broker的topic的状态管理工作<br><br>
kafkaHealthcheck初始化<br>
负责该broker的状态在zk的维护工作<br>
requestHandlerPool初始化<br>
核心IO处理中心，通过事件驱动模式进行处理<br><br>
维护2个队列，<br>
一个是request队列<br>
一个是response队列<br>
通过，KafkaApis调用(关于请查看<span style="font-size:14px;">KafkaApis详解</span>)handler的事件类型，进行相关的事件的处理。<br>
请求的事件类型共有ProduceKey， FetchKey， OffsetsKey， MetadataKey， LeaderAndIsrKey， StopReplicaKey， <br>
UpdateMetadataKey， ControlledShutdownKey， OffsetCommitKey， OffsetFetchKey</span></div>
<div><span style="font-size:14px;"><span style="color:rgb(51,51,51);font-family:Georgia, 'Bitstream Charter', serif;font-size:16px;line-height:24px;"><br></span></span></div>
<div><span style="font-size:14px;">请注明转载自：http://blog.csdn.net/lizhitao/article/details/38352947</span></div>
<p></p>
            </div>
                </div>