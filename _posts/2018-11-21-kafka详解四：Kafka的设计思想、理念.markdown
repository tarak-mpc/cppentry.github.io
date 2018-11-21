---
layout:     post
title:      kafka详解四：Kafka的设计思想、理念
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/suifeng3051/article/details/37606001				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div>     本节主要从整体角度介绍Kafka的设计思想，其中的每个理念都可以深入研究，以后我可能会发专题文章做深入介绍，在这里只做较概括的描述以便大家更好的理解Kafka的独特之处。本节主要涉及到如下主要内容：</div>
<div>
<ul><li>Kafka设计基本思想</li><li>Kafka中的数据压缩</li><li>Kafka消息转运过程中的可靠性</li><li>Kafka集群镜像复制</li><li>Kafka 备份机制</li></ul></div>
<strong>一、kafka由来</strong>
<div>     由于对JMS日常管理的过度开支和传统JMS可扩展性方面的局限，<a href="www.linkedin.com" rel="nofollow">LinkedIn(www.linkedin.com)</a>开发了Kafka以满足他们对实时数据流的监控以及对CPU、IO利用率等指标的高要求。在Linkedin开发Kafka之初，把关注重点集中在了这几个方面：</div>
<div>
<ul style="font-family:Tahoma;font-size:14px;"><li>为生产者和消费者提供一个通用的API</li><li>消息的持久化</li><li>高吞吐量，可以满足百万级别消息处理</li><li>对分布式和高扩展性的支持</li></ul><div style="font-family:Tahoma;"><strong>二、基本思想</strong></div>
<div style="font-family:Tahoma;font-size:14px;">     一个最基本的架构是生产者发布一个消息到Kafka的一个主题（topic），这个主题即是由扮演KafkaServer角色的broker提供，消费者订阅这个主题，然后从中获取消息，下面这个图可以更直观的描述这个场景：</div>
<div style="font-family:Tahoma;font-size:14px;">     <img src="https://img-blog.csdn.net/20140709200548420?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3VpZmVuZzMwNTE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""></div>
<div style="font-family:Tahoma;font-size:14px;">     <span style="font-size:13px;"><span style="color:#464646;">上图所示的架构分为三部分：Producers、Kafka broker、consumers，它们分别运行在不同的节点。</span></span></div>
<div style="font-family:Tahoma;font-size:14px;"><span style="color:#464646;">     下面概括介绍一下Kafka一些设计思想：</span></div>
<div style="font-family:Tahoma;font-size:14px;">   <strong>  consumer group：</strong>各个consumer可以组成一个组，每个消息只能被组中的一个consumer消费，如果一个消息可以被多个consumer消费的话，那么这些consumer必须在不同的组。</div>
<div style="font-family:Tahoma;font-size:14px;">    <strong> 消息状态：</strong>在Kafka中，消息的状态被保存在consumer中，broker不会关心哪个消息被消费了被谁消费了，只记录一个offset值（指向partition中下一个要被消费的消息位置），这就意味着如果consumer处理不好的话，broker上的一个消息可能会被消费多次。</div>
<div style="font-family:Tahoma;font-size:14px;">    <strong> 消息持久化：</strong>Kafka中会把消息持久化到本地文件系统中，并且保持极高的效率。</div>
<div style="font-family:Tahoma;font-size:14px;">   <strong>  消息有效期：</strong>Kafka会长久保留其中的消息，以便consumer可以多次消费，当然其中很多细节是可配置的。</div>
<div style="font-family:Tahoma;font-size:14px;">     <strong>批量发送：</strong>Kafka支持以消息集合为单位进行批量发送，以提高push效率。</div>
<div style="font-family:Tahoma;font-size:14px;">    <strong> push-and-pull:</strong>Kafka中的Producer和consumer采用的是push-and-pull模式，即Producer只管向broker push消息，consumer只管从broker pull消息，两者对消息的生产和消费是异步的。</div>
<div style="font-family:Tahoma;font-size:14px;">     <strong>Kafka集群中broker之间的关系：</strong>不是主从关系，各个broker在集群中地位一样，我们可以随意的增加或删除任何一个broker节点。</div>
<div style="font-family:Tahoma;font-size:14px;">     <strong>负载均衡方面：</strong>Kafka提供了一个 metadata API来管理broker之间的负载（对Kafka0.8.x而言，对于0.7.x主要靠zookeeper来实现负载均衡）。</div>
<div style="font-family:Tahoma;font-size:14px;">    <strong> 同步异步：</strong><span style="font-size:14px;">Producer采用异步push方式，极大提高Kafka系统的吞吐率（可以通过参数控制是采用同步还是异步方式）。</span></div>
<div style="font-family:Tahoma;font-size:14px;">     <strong>分区机制partition：</strong>Kafka的broker端支持消息分区，Producer可以决定把消息发到哪个分区，在一个分区中消息的顺序就是Producer发送消息的顺序，一个主题中可以有多个分区，具体分区的数量是可配置的。分区的意义很重大，后面的内容会逐渐体现。</div>
<div style="font-family:Tahoma;font-size:14px;">     <strong>离线数据装载：</strong>Kafka由于对可拓展的数据持久化的支持，它也非常适合向Hadoop或者数据仓库中进行数据装载。</div>
<div style="font-family:Tahoma;font-size:14px;">    <strong> 插件支持：</strong>现在不少活跃的社区已经开发出不少插件来拓展Kafka的功能，如用来配合Storm、Hadoop、flume相关的插件。</div>
<div style="font-family:Tahoma;"><strong>三、消息压缩</strong></div>
<div style="font-family:Tahoma;font-size:14px;">     我们上面已经知道了Kafka支持以集合为单位发送消息，在此基础上，Kafka还支持对消息集合进行压缩，Producer端可以通过GZIP或Snappy格式对消息集合进行压缩。Producer端进行压缩之后，在Consumer端需进行解压。压缩的好处就是减少传输的数据量，减轻对网络传输的压力，在对大数据处理上，瓶颈往往体现在网络上而不是CPU（压缩和解压会耗掉部分CPU资源）。</div>
<div style="font-family:Tahoma;font-size:14px;">     那么如何区分消息是压缩的还是未压缩的呢，Kafka在消息头部添加了一个<strong><em>描述压缩属性字节</em></strong>，这个字节的后两位表示消息的压缩采用的编码，如果后两位为0，则表示消息未被压缩。</div>
<div style="font-family:Tahoma;font-size:14px;">     具体细节请参考：  <a href="https://cwiki.apache.org/confluence/display/KAFKA/Compression" rel="nofollow">https://cwiki.apache.org/confluence/display/KAFKA/Compression</a> </div>
<div style="font-family:Tahoma;"><strong>四、消息转运过程中的可靠性</strong></div>
<div style="font-family:Tahoma;font-size:14px;">     在消息系统中，保证消息在生产和消费过程中的可靠性是十分重要的，在实际消息传递过程中，可能会出现如下三中情况：</div>
<div style="font-family:Tahoma;font-size:14px;">
<ol><li>一个消息发送失败</li><li>一个消息被发送多次</li><li>最理想的情况：exactly-once ,一个消息发送成功且仅发送了一次                   </li></ol></div>
<div style="font-family:Tahoma;font-size:14px;"><span style="font-size:16px;"><strong>   </strong></span> 有许多系统声称它们实现了exactly-once，但是它们其实忽略了生产者或消费者在生产和消费过程中有可能失败的情况。比如虽然一个Producer成功发送一个消息，但是消息在发送途中丢失，或者成功发送到broker，也被consumer成功取走，但是这个consumer在处理取过来的消息时失败了。</div>
<div style="font-family:Tahoma;font-size:14px;">     从Producer端看：Kafka是这么处理的，当一个消息被发送后，Producer会等待broker成功接收到消息的反馈（可通过参数控制等待时间），如果消息在途中丢失或是其中一个broker挂掉，Producer会重新发送（我们知道Kafka有备份机制，可以通过参数控制是否等待所有备份节点都收到消息）。</div>
<div style="font-family:Tahoma;font-size:14px;">     从Consumer端看：前面讲到过partition，broker端记录了partition中的一个offset值，这个值指向Consumer下一个即将消费message。当Consumer收到了消息，但却在处理过程中挂掉，此时Consumer可以通过这个offset值重新找到上一个消息再进行处理。Consumer还有权限控制这个offset值，对持久化到broker端的消息做任意处理。</div>
<div style="font-family:Tahoma;font-size:14px;">     </div>
<div style="font-family:Tahoma;"><strong>五、mirror一个Kafka集群</strong></div>
<div style="font-family:Tahoma;font-size:14px;">     关于Kafka集群的mirror，参考下面这幅图：</div>
<div style="font-family:Tahoma;font-size:14px;">     <img src="https://img-blog.csdn.net/20140709200647716?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3VpZmVuZzMwNTE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""></div>
<div style="font-family:Tahoma;font-size:14px;">     具体细节请参考：<a href="https://cwiki.apache.org/confluence/display/KAFKA/Kafka+mirroring" rel="nofollow">https://cwiki.apache.org/confluence/display/KAFKA/Kafka+mirroring</a></div>
<div style="font-family:Tahoma;font-size:14px;">     </div>
<div style="font-family:Tahoma;"><strong>六、备份机制</strong></div>
<div style="font-family:Tahoma;font-size:14px;">          备份机制是Kafka0.8版本的新特性，备份机制的出现大大提高了Kafka集群的可靠性、稳定性。有了备份机制后，Kafka允许集群中的节点挂掉后而不影响整个集群工作。一个备份数量为n的集群允许n-1个节点失败。在所有备份节点中，有一个节点作为lead节点，这个节点保存了其它备份节点列表，并维持各个备份间的状体同步。下面这幅图解释了Kafka的备份机制：</div>
<div style="font-family:Tahoma;font-size:14px;">     <img src="https://img-blog.csdn.net/20140709200710601?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3VpZmVuZzMwNTE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""></div>
<div style="font-family:Tahoma;font-size:14px;"><br></div>
<div style="font-family:Tahoma;font-size:14px;">     具体细节请参考：<a href="https://cwiki.apache.org/confluence/display/KAFKA/Kafka+Replication" rel="nofollow">https://cwiki.apache.org/confluence/display/KAFKA/Kafka+Replication</a>
  </div>
<div style="font-family:Tahoma;font-size:14px;"><br></div>
<div style="font-family:Tahoma;font-size:14px;"><br></div>
<div style="font-family:Tahoma;font-size:14px;"><br></div>
<div style="font-family:Tahoma;font-size:14px;">     想更深入的了解Kafka请参阅我的另一篇文章：<a href="http://blog.csdn.net/suifeng3051/article/details/48053965" rel="nofollow">《Kafka设计与原理详解》</a></div>
<div style="text-align:justify;"><span style="font-family:Simsun;font-size:14px;"><span style="line-height:20.796875px;">   </span></span></div>
<div style="text-align:justify;"><span style="font-family:Simsun;font-size:14px;"><span style="line-height:20.796875px;">   转发请说明出处，原文链接：http://blog.csdn.net/suifeng3051/article/details/37606001  </span></span></div>
<div style="text-align:justify;"><span style="font-family:Simsun;font-size:14px;"><span style="line-height:20.796875px;">   </span></span></div>
<div style="font-family:Tahoma;font-size:14px;"><span style="font-family:Simsun;font-size:16px;line-height:20.799999237060547px;text-align:justify;"><br></span></div>
<br></div>
            </div>
                </div>