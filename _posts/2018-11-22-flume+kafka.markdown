---
layout:     post
title:      flume+kafka
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/leveretz/article/details/53303053				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p align="left"><span style="color:#000000;">Flume </span><span style="color:#000000;">是一个分布式、可靠、和高可用的海量日志聚合的系统，支持在系统中定制各类数据发送方，用于收集数据；同时，</span><span style="color:#000000;">Flume
</span><span style="color:#000000;">提供对数据进行简单处理，并写到各种数据接受方</span><span style="color:#000000;">(</span><span style="color:#000000;">可定制</span><span style="color:#000000;">)</span><span style="color:#000000;">的能力。</span></p>
<p align="left"><span style="color:#000000;">Flume </span><span style="color:#000000;">的特点是可以通过手工配置，可以自动收集日志文件，在大数据处理及各种复杂的情况下，</span><span style="color:#000000;">flume
</span><span style="color:#000000;">经常被用来作为数据处理的工具，</span><span style="color:#000000;">Flume
</span><span style="color:#000000;">搜集日志的方式多种多样，比如可以检测文件夹的变化，可以监测端口信息等等，</span><span style="color:#000000;">检测文件夹变化的方式来实时收集信息。</span></p>
<p align="left"><span style="color:#000000;">source</span><span style="color:#000000;">：顾名思义，就是数据的来源，这个来源可以来自</span><span style="color:#000000;">Web Service
</span><span style="color:#000000;">中封装的客户端</span><span style="color:#000000;">(AVRO </span>
<span style="color:#000000;">客户端</span><span style="color:#000000;">)</span><span style="color:#000000;">，可以是</span><span style="color:#000000;">NETCAT
</span><span style="color:#000000;">服务，也可以是一个不断增长的日志文件</span><span style="color:#000000;">(tail -f)</span><span style="color:#000000;">。</span></p>
<p align="left"><span style="color:#000000;">channel</span><span style="color:#000000;">：提供了一层缓冲机制，来实现数据的事务性传输，最大限度保证数据的安全传输。这层缓冲可以在内存中，可以在文件中、数据库中，当然也可以是自定义的实现。</span></p>
<p align="left"><span style="color:#000000;">sink</span><span style="color:#000000;">：将数据转发到目的地，或者继续将数据转发到另外一个</span><span style="color:#000000;">source</span><span style="color:#000000;">，实现接力传输。可以通过</span><span style="color:#000000;">AVRO Sink</span><span style="color:#000000;">来实现。</span></p>
<p align="left"><span style="color:#000000;">通常处理离线业务，可以把收集来的数据放在</span><span style="color:#000000;">HDFS
</span><span style="color:#000000;">上，如果要实时的计算数据流就把数据收集在</span><span style="color:#000000;">Kafka
</span><span style="color:#000000;">中。使用的是对文件夹中文件变化进行监测的</span><span style="color:#000000;">Spooling DirectorySource</span><span style="color:#000000;">，</span><span style="color:#000000;">channel
</span><span style="color:#000000;">是用的内存空间，</span><span style="color:#000000;">sink </span>
<span style="color:#000000;">是自定义的</span><span style="color:#000000;">kafkasink</span><span style="color:#000000;">，用于向</span><span style="color:#000000;">kafka
</span><span style="color:#000000;">发送数据。</span></p>
<p align="left"><span style="color:#000000;">Flume </span><span style="color:#000000;">的可靠性是指</span><span style="color:#000000;">Flume
</span><span style="color:#000000;">在数据的传输过程中，保证</span><span style="color:#000000;">Events
</span><span style="color:#000000;">的可靠专递。为了保证</span><span style="color:#000000;">Event
</span><span style="color:#000000;">在数据流点对点传输中是可靠地。这里要注意</span><span style="color:#000000;">Channel
</span><span style="color:#000000;">的选择，</span><span style="color:#000000;">MemoryChannel</span><span style="color:#000000;">：所有的</span><span style="color:#000000;">events
</span><span style="color:#000000;">被保存在内存中。优点是高吞吐。缺点是容量有限并且</span><span style="color:#000000;">Agent
</span><span style="color:#000000;">死掉时会丢失内存中的数据。</span><span style="color:#000000;">FileChannel</span><span style="color:#000000;">：</span><span style="color:#000000;">所有的</span><span style="color:#000000;">Events
</span><span style="color:#000000;">被保存在文件中。优点是容量较大且死掉时数据可恢复。缺点是速度较慢。</span></p>
<p align="left"><span style="color:#000000;">Kafka </span><span style="color:#000000;">是一个开源的分布式消息系统，由</span><span style="color:#000000;">LinkedIn
</span><span style="color:#000000;">开发，主要用于处理</span><span style="color:#000000;">LinkedIn
</span><span style="color:#000000;">的活跃数据。活跃数据主要包括页面浏览量</span><span style="color:#000000;">(PV)</span><span style="color:#000000;">、用户行为</span><span style="color:#000000;">(</span><span style="color:#000000;">登陆、浏览、搜索、分享、点击</span><span style="color:#000000;">)</span><span style="color:#000000;">、系统运行日志</span><span style="color:#000000;">(CPU</span><span style="color:#000000;">、内存、磁盘、进程、网络</span><span style="color:#000000;">)</span><span style="color:#000000;">等方面的数据。这些数据通常以日志的形式进行存储，然后周期性的对这些数据进行统计分析。传统的日志分析系统主要用于处理离线数据，对于实时数据的处理，有较大的延迟性。现有的消息队列系统可以很好的用于日志分析系统对于实时数据的处理，但通常未被处理的数据不会写到磁盘上，这样不利于</span><span style="color:#000000;">Hadoop
</span><span style="color:#000000;">这样的离线分析系统的使用。</span></p>
<p align="left"><span style="color:#000000;">Kafka </span><span style="color:#000000;">可以很好的解决以上问题，能够很好的为离线和实时的分析系统提供服务。</span><span style="color:#000000;">kafka
</span><span style="color:#000000;">是一种高吞吐量的分布式发布订阅消息系统，</span><span style="color:#000000;">它有如下特点：数据在磁盘上存取代价为</span><span style="color:#000000;">O(1)</span><span style="color:#000000;">一般数据在磁盘上是使用</span><span style="color:#000000;">BTree
</span><span style="color:#000000;">存储的，存取代价为</span><span style="color:#000000;">O(lgn)</span><span style="color:#000000;">；高吞吐率，即使在普通的节点上每秒钟也能处理成百上千的</span><span style="color:#000000;">message</span><span style="color:#000000;">；显式分布式，即所有的</span><span style="color:#000000;">producer</span><span style="color:#000000;">、</span><span style="color:#000000;">broker
</span><span style="color:#000000;">和</span><span style="color:#000000;">consumer </span>
<span style="color:#000000;">都会有多个，均为分布式的；支持数据并行加载到</span><span style="color:#000000;">Hadoop
</span><span style="color:#000000;">中。</span></p>
<p align="left"><span style="color:#000000;">Kafka </span><span style="color:#000000;">结构中</span><span style="color:#000000;">Producer
</span><span style="color:#000000;">用于收集数据，</span><span style="color:#000000;">Broker </span>
<span style="color:#000000;">用于数据的中间存储，</span><span style="color:#000000;">consumer </span>
<span style="color:#000000;">用于数据的订阅，</span><span style="color:#000000;">Kafka </span><span style="color:#000000;">是显式分布式架构，</span><span style="color:#000000;">producer</span><span style="color:#000000;">、</span><span style="color:#000000;">broker(kafka)</span><span style="color:#000000;">和</span><span style="color:#000000;">consumer
</span><span style="color:#000000;">都可以有多个。</span><span style="color:#000000;">Kafka </span>
<span style="color:#000000;">的作用类似于缓存，即活跃的数据和离线处理系统之间的缓存。多个</span><span style="color:#000000;">broker
</span><span style="color:#000000;">协同合作，</span><span style="color:#000000;">producer </span>
<span style="color:#000000;">和</span><span style="color:#000000;">consumer </span><span style="color:#000000;">部署在各个业务逻辑中被频繁的调用，三者通过</span><span style="color:#000000;">zookeeper
</span><span style="color:#000000;">管理协调请求和转发。这样一个高性能的分布式消息发布与订阅系统就完成了。</span></p>
<p align="left"><span style="color:#000000;">Kafka </span><span style="color:#000000;">源于</span><span style="color:#000000;">LinkedIn</span><span style="color:#000000;">，</span><span style="color:#000000;">LinkedIn</span><span style="color:#000000;">于</span><span style="color:#000000;">2010
</span><span style="color:#000000;">年</span><span style="color:#000000;">12 </span><span style="color:#000000;">月开源，是一种高吞吐量的分布式发布订阅消息系统，有如下特性：通过</span><span style="color:#000000;">O(1)
</span><span style="color:#000000;">的磁盘数据结构提供消息的持久化，这种结构对于即使数以</span><span style="color:#000000;">TB
</span><span style="color:#000000;">的消息存储也能够保持长时间的稳定性能。高吞吐量：即使是普通的硬件</span><span style="color:#000000;">Kafka
</span><span style="color:#000000;">也可以支持每秒数十万的消息；支持通过</span><span style="color:#000000;">Kafka
</span><span style="color:#000000;">服务器和消费机集群来分区消息；支持</span><span style="color:#000000;">HDFS
</span><span style="color:#000000;">并行数据加载。</span></p>
<p align="left"><span style="color:#000000;">Kafka </span><span style="color:#000000;">的目的是提供一个发布订阅解决方案，它可以处理消费者规模的网站中的所有动作流数据。</span><span style="color:#000000;">Kafka
</span><span style="color:#000000;">的目的是通过</span><span style="color:#000000;">HDFS </span>
<span style="color:#000000;">的并行加载机制来统一线上和离线的消息处理，也是为了通过集群机来提供实时的消费。</span><span style="color:#000000;">Producer
</span><span style="color:#000000;">主要功能是向</span><span style="color:#000000;">Broker </span>
<span style="color:#000000;">同步或者异步的发送消息，可以通过任意的一个</span><span style="color:#000000;">Broker
</span><span style="color:#000000;">发现其他的</span><span style="color:#000000;">Broker </span>
<span style="color:#000000;">位置信息；</span><span style="color:#000000;">broker </span><span style="color:#000000;">是</span><span style="color:#000000;">Producer
</span><span style="color:#000000;">和</span><span style="color:#000000;">Consumer </span>
<span style="color:#000000;">之间的桥梁，可以将信息可靠地缓存一段时间，这个我们可以定义；</span><span style="color:#000000;">Kafka
</span><span style="color:#000000;">的</span><span style="color:#000000;">Consumer</span><span style="color:#000000;">主要是用户应用程序，负责从</span><span style="color:#000000;">Kafka
</span><span style="color:#000000;">中读取数据，并行进行处理。</span></p>
            </div>
                </div>