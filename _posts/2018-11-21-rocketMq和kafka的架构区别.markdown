---
layout:     post
title:      rocketMq和kafka的架构区别
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h2>概述</h2>

<p>    在网上看到一篇主要讲清楚kafka和rocketMq的两个不同点的文章；1、rocketMq的namesvr和kafka的zookeeper对比；2、kafka为什么比rocketMq有更大的吞吐量。所以摘录下来学习一下。</p>

<h2>namesrv VS zk</h2>

<p>    1、我们可以对比下kafka和rocketMq在协调节点选择上的差异，<span style="color:#f33b45;">kafka通过zookeeper来进行协调</span>，而<span style="color:#f33b45;">rocketMq通过自身的namesrv进行协调</span>。</p>

<p>    2、kafka在具备选举功能，在Kafka里面，Master/Slave的选举，有2步：第1步，先通过ZK在所有机器中，选举出一个<span style="color:#f33b45;">KafkaController</span>；第2步，再由这个Controller，决定每个partition的<span style="color:#f33b45;">Master</span>是谁，<span style="color:#f33b45;">Slave</span>是谁。因为有了选举功能，所以kafka某个partition的master挂了，该partition对应的某个slave会<span style="color:#f33b45;">升级</span>为主对外提供服务。</p>

<p>    3、rocketMQ不具备选举，Master/Slave的角色也是固定的（<span style="color:#f33b45;">集群中有写磁盘和内存</span>）。当一个Master挂了之后，你<span style="color:#f33b45;">可以写到其他Master上</span>，但<span style="color:#f33b45;">不能</span>让一个<span style="color:#f33b45;">Slave切换成Master</span>。那么rocketMq是如何实现高可用的呢，其实很简单，rocketMq的所有broker节点的<span style="color:#f33b45;">角色都是一样</span>，上面分配的topic和对应的queue的数量也是一样的（<strong><span style="color:#f33b45;">镜像同步</span></strong>），Mq只能保证当一个broker挂了，把原本写到这个broker的请求迁移到其他broker上面，而并不是这个broker对应的slave升级为主。</p>

<p>    4、rocketMq在协调节点的设计上显得更加轻量，用了另外一种方式解决高可用的问题，思路也是可以借鉴的。</p>

<p><img alt="" class="has" height="526" src="https://img-blog.csdnimg.cn/20181030180804181.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2ExMjM0NTY3OG4=,size_16,color_FFFFFF,t_70" width="652"></p>

<p><img alt="" class="has" height="435" src="https://img-blog.csdnimg.cn/20181030180743844.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2ExMjM0NTY3OG4=,size_16,color_FFFFFF,t_70" width="657"></p>

<h2>关于吞吐量</h2>

<p>1、首先说明下面的几张图片来自于互联网共享，也就是我后面参考文章里面的列出的文章。</p>

<p>2、kafka在消息存储过程中会根据topic和partition的数量创建物理文件，也就是说我们创建一个topic并指定了3个partition，那么就会有3个物理文件目录，也就说说partition的数量和对应的物理文件是一一对应的。</p>

<p>3、rocketMq在消息存储方式就一个物流问题，也就说传说中的commitLog，rocketMq的queue的数量其实是在consumeQueue里面体现的，在真正存储消息的commitLog其实就只有一个物理文件。</p>

<p>4、kafka的多文件并发写入 VS rocketMq的单文件写入，性能差异kafka完胜可想而知。</p>

<p>5、kafka的大量文件存储会导致一个问题，也就说在partition特别多的时候，磁盘的访问会发生很大的瓶颈，毕竟单个文件看着是append操作，但是多个文件之间必然会导致磁盘的寻道。</p>

<p><img alt="" class="has" height="739" src="https://img-blog.csdnimg.cn/20181030180905964.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2ExMjM0NTY3OG4=,size_16,color_FFFFFF,t_70" width="654"></p>            </div>
                </div>