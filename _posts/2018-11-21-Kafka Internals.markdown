---
layout:     post
title:      Kafka Internals
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/yueyang131417/article/details/78922910				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2 id="主题">主题</h2>

<ul>
<li>kafka 副本集</li>
<li>kafka 如何处理生成者(producers)和消费者(consumers)的请求的</li>
<li>kafka 如何管理数据存储的(如file format、indexes)</li>
</ul>

<hr>



<h2 id="基本概念">基本概念</h2>

<p>Kafka集群由多个消息代理服务器组成(broker server)，发布到集群的消息都有一个类别主题（topic）。</p>

<p>Kafka集群为每个主题维护了分布式的分区日志文件（partition）。</p>

<p>为了故障容错，每个分区partition都会复制多个副本到其他broker上，其中一个作为主副本(Leader)，其他的作为从副本(Follower)。主副本负责所有客户端的读写操作，备份副本仅仅从主副本同步数据。</p>

<p><img src="https://img-blog.csdn.net/20180104143401179?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveXVleWFuZzEzMTQxNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="kafka topic partition" title=""></p>

<hr>

<h2 id="集群与控制器">集群与控制器</h2>



<h3 id="1-集群cluster">1. 集群(Cluster)</h3>

<p>kafka借用<strong>zookeeper</strong>来管理集群的broker列表。broker启动时都会将自己独有的brokerId信息添加kafka的<strong>/brokers/ids</strong>下，作为<strong>临时节点</strong>。</p>

<p>当该broker与zookeeper的连接断开时，会自动从 <strong>/brokers/ids</strong> 节点下移除其brokerId信息，其他broker的该/brokers/ids节点的监听器会实时获取这些变更信息。</p>

<p>当一个已注册的brokerId再次被注册时，会报异常。 <br>
当一个已注册的broker1下线时，其他一个broker2可以使用其brokerId1注册，并继承其分区副本的分配。(更换集群主机时可用)</p>

<h3 id="2-控制器controller">2. 控制器(Controller)</h3>

<p>kafka集群中只有一个broker可以成为主控制器(controller)，其他brokers只能在主控制器出现故障或会话失效时参与选举成为主controller。</p>

<p>Kafka利用zookeeper实现选举：每个broker启动时向<strong>zookeeper</strong>服务器创建 <strong>/controller</strong> <strong>临时节点</strong>，最终只有一个broker可以成功创建/controller节点，成为<strong>主controller</strong>；主controller故障或会话失效时，临时节点自动被删除，同时被其他brokers的监听器收到，这时其他的brokers都会尝试重新创建/controller节点，选举出新的主控制器。</p>

<p><img src="https://img-blog.csdn.net/20180104153400271?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveXVleWFuZzEzMTQxNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="控制器选举" title=""></p>

<p><strong>主控制器功能：</strong>    </p>

<ul>
<li>分区partition的主从副本在brokers间的分配</li>
<li>分区主副本的选择</li>
<li>broker启动或下线时，处理代理节点的故障转移</li>
<li>topic增删、partition增加时，处理partition的重新分配</li>
<li>管理所有分区和副本的状态机，处理状态机的变化事件</li>
</ul>

<hr>

<h2 id="副本管理replica">副本管理(Replica)</h2>

<p>副本是kafka很重要的一个架构设计，是kafka可用性和稳定性的保障。</p>

<p>kafka的数据通过topics划分，每个topic可以有多个分区partitions，每个partition又应该存在多个副本replicas。replicas存储在brokers节点，每个broker可以存储成百上千的不同partitions的副本。</p>

<p>replicas有两种：</p>

<ul>
<li><p>Leader replica 主副本 <br>
每个partition一定有一个主副本，为保证一致性，生产者和消费者只与主副本进行数据请求。</p></li>
<li><p>Follower replica 从副本 <br>
每个partition主副本之外的副本都是从副本，从副本可以有0个或多个（要小于集群中brokers的数量）。从副本始终尽量保持与主副本的数据同步。从副本从主副本消费消息的方式和普通消费者一样，只不过将数据运用到自己本地的日志文件。当主副本失效时，从副本中的一个将被controller指定为新的主副本。</p></li>
</ul>

<p>分布式系统处理故障容错时，需要明确定义节点是否处于存活状态。Kafka对节点的存活定义有两个条件：</p>

<ul>
<li>节点必须和zookeeper保持会话(/brokers/ids)</li>
<li>如果该节点是某个partition的备份副本，它必须对分区主副本的写操作进行复制，并且复制的进度不能落后太多。</li>
</ul>

<p>满足这两个条件，叫作 <strong>正在同步中</strong> (<strong>in-sync</strong>)。每个partition的主副本会跟踪正在同步中的从副本节点(<em>In Sync Replicas</em>, 即<strong>ISR</strong>)。如果一个从副本挂掉、没有响应或者落后太多，主副本就会将其从ISR集合中移除。如果从副本重新赶上主副本进度，它就会加入到主副本的同步集合ISR中。</p>

<p><strong>replica.lag.time.max.ms</strong>， 从副本失活时间超过这个配置时，被认为不在同步中，不再属于ISR。</p>

<p>只有ISR集合中的从副本可能成为新的主副本。</p>

<p>因为所有的读写请求总是路由到分区的主副本，所以为避免数据热点，从负载均衡的角度考虑，所以的主副本应该均匀的分布在集群的brokers上。</p>

<p><img src="https://img-blog.csdn.net/20180104172054289?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveXVleWFuZzEzMTQxNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p><strong>首选主副本</strong>(preferred leader)，每个partition都有一个preferred leader，即partition创建时选为leader的副本。因为partition创建时，主副本的分布已经考虑了集群负载均衡的问题，所以preferred leader最好一直是真正的主副本。</p>

<p>一个partition的replicas列表中的第一个replica总会是preferred leader。所以手动指定replicas列表时，要注意这一点，尽量避免负载不均衡。</p>

<p>若参数 <strong>auto.leader.rebalance.enable=true</strong>，若preferred leader在ISR集合中，且并不是主副本时，将自动重新指定preferred leader为主副本。</p>

<hr>

<h2 id="请求管理">请求管理</h2>

<p>Kafka broker主要工作就是处理来自客户端、从副本、控制器发给主副本的请求。一个客户端的所有请求会被broker依次执行以保证数据的有序。</p>

<p>所有请求标准的Headers包含：</p>

<ul>
<li>请求类型 (也称 <strong>API key</strong>)</li>
<li>请求版本 (据此brokers可以服务不同版本的客户端并做出对应的回复)</li>
<li>Correlation ID，每次请求的唯一id，排错时用</li>
<li>Client ID</li>
</ul>

<p>客户端的所有读写请求须partition的主副本处理。如果一个partition的访问请求没有发送主副本所在的broker，将收到异常返回“Not a Leader for Partition.”。</p>

<p>客户端怎么知道partition主副本位置呢？ <br>
客户端使用 <strong>metadata request</strong> 请求任何一个活跃broker就会得到关注的topics分布(即<strong>metadata</strong>)，然后自己缓存这些metadata。 <br>
参数 <strong>metadata.max.age.ms</strong> 可以配置客户端刷新metadata的时间间隔。当收到 “Not a Leader”异常时，客户端也会通过metadata request刷新本地的metadata。</p>

<p><img src="https://img-blog.csdn.net/20180104185031677?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveXVleWFuZzEzMTQxNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<h3 id="1-produce-requests">1. Produce requests</h3>

<p>由生产者发送给brokers的数据写请求。</p>

<p>参数 <strong>acks</strong> 代表收到写请求成功前，集群中需要确认数据写入完成的brokers的数量：</p>

<ul>
<li>acks=1 只等待主副本写入完成</li>
<li>acks=all 等待集群中所有ISR集合中的brokers写入完成</li>
<li>acks=0 不等待，不关心写入结果</li>
</ul>

<p>当主副本收到对应写请求写入数据前，要做以下验证：</p>

<ul>
<li>该用户是否有该topic的写权限</li>
<li>acks参数的值是否有效 (只允许是 0, 1 或 “all” )</li>
<li>如果acks值 “all”，是否同步集合ISR中的节点数足够（可以设置一个最小同步节点数，当ISR中节点数小于该配置值时，拒绝写入）</li>
</ul>

<h3 id="2-fetch-requests">2. Fetch requests</h3>

<p>由消费者或从副本发送给brokers的数据读请求。</p>

<p>请求参数：</p>

<ul>
<li>topic</li>
<li>partition</li>
<li>offset</li>
<li>limit data size (upper/lower boundary)</li>
<li>timeout</li>
</ul>

<p><img src="https://img-blog.csdn.net/20180104193624084?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveXVleWFuZzEzMTQxNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p>主副本的数据对消费者来说并非全部可用，最新数据需要同步完成后才对消费者可见。同步完成前，消费者尝试获取这些数据会得到空的返回，而不是error。</p>

<p><img src="https://img-blog.csdn.net/20180104194457880?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveXVleWFuZzEzMTQxNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<h3 id="3-other-requests">3. Other requests</h3>

<p>The Kafka protocol currently handles 20 different request types, and more will be added.  <br>
(Metadata, Produce, and Fetch. )</p>

<hr>

<h2 id="数据存储">数据存储</h2>

<p>The basic storage unit of Kafka is a partition replica.  <br>
Partitions cannot be split between multiple brokers and not even between multiple disks on the same broker.</p>

<p><strong>log.dirs</strong> 参数，指定partitions数据的存储目录。</p>



<h3 id="1-分区partition的分配">1. 分区partition的分配</h3>

<p>当我们创建一个topic时，kafka首先要确定怎么在brokers之间分配这些partitions。如果有6台brokers，你想要创建一个有10个partitions的topic，每个partition有3个副本，那么就要30个副本要分布在这6台brokers上，该怎么分配：</p>

<ul>
<li>To spread replicas evenly among brokers—in our example, to make sure we allocate 5 replicas per broker.</li>
<li>To make sure that for each partition, each replica is on a different broker. If partition 0 has the leader on broker 2, we can place the followers on brokers 3 and 4, but not on 2 and not both on 3.</li>
<li>If the brokers have rack information (available in Kafka release 0.10.0 and higher), then assign the replicas for each partition to different racks if possible. This ensures that an event that causes downtime for an entire rack does not cause complete unavailability for partitions.</li>
</ul>

<p><img src="https://img-blog.csdn.net/20180105091219448?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveXVleWFuZzEzMTQxNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p>这些新的partitions的存储目录怎么分配，规则很简单：我们计算每个备选目录中partitions的<strong>数量</strong>，然后将新的partition置于最少的那个目录。这样当你添加一个新的硬盘目录时，新的partition就会添加到这个新目录里。</p>

<h3 id="2-文件管理">2. 文件管理</h3>

<p>kafka并不能将数据永远保持，管理员要设置 <strong>过期时间</strong> 或 <strong>最大数据量</strong> 的限制，来自动清理数据。</p>

<p>为了方便日志管理，kafka将每个partition的数据分片(segments)。默认每个分片(segment)最多存储1GB或者1周的数据，当写操作时，先验证是否达到这个限制，是否创建新的segment。</p>

<p>当前正在写入的segment称作 <strong>active segment</strong>，是不会被删除的。</p>

<p>第二章有介绍，broker会为每个segment维持一个文件打开句柄(open file handle)，这就导致经常需要一个比较大的文件打开数，注意做适当调整。</p>

<h3 id="3-file-format">3. File Format</h3>

<p>日志文件中每条message包含以下信息： <br>
    <strong>key</strong>, <strong>value</strong>, <strong>offset</strong>, <strong>size</strong>,  <strong>checksum code</strong>, <strong>magic</strong> (indicates the version of the message format), <strong>compression codec</strong>(Snappy, GZip, or LZ4), <strong>timestamp</strong>(added in release 0.10.0) <br>
    (The timestamp is given either by the producer when the message was sent or by the broker when the message arrived—depending on configuration.)</p>

<p>磁盘中日志保存的message的格式，和与客户端传输时的一致，这样kafka就可以使用零拷贝(zero-copy)的优化，也可以避免再解压和压缩生产者已经压缩过的message。</p>

<p><img src="https://img-blog.csdn.net/20180104150254730?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveXVleWFuZzEzMTQxNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="zero-copy" title=""></p>

<p>生产者发送的 <strong>wrapper message</strong>，是将一批次的一般格式的消息压缩，将压缩后的数据作为一条消息的 “value”来使用。其中这批次的消息有自己的时间戳和偏移，不受kafka管理。同时消费者也要以对应的方式去解压。使用有些不便，但利于存储和传输。</p>

<p><img src="https://img-blog.csdn.net/20180105100226067?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveXVleWFuZzEzMTQxNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p><strong>DumpLogSegment</strong>工具，让你可以查看segment中每条message的具体内容：</p>

<blockquote>
  <p>bin/kafka-run-class.sh kafka.tools.DumpLogSegments</p>
</blockquote>

<p>加上 <strong>–deep-iteration</strong>参数，可以查看 <strong>wrapper message</strong> 压缩过消息内容。</p>

<h3 id="4-indexes">4. Indexes</h3>

<p>为了可以尽快定位到指定offset的消息，Kafka会为每个partition维持一个index，这个index也会同数据文件一样分片，和数据文件放在同目录下。</p>

<p>index文件如果出错，kafka可以读取数据文件内容自动重建。所以有必要可以删除一些index文件。</p>

<h3 id="5-compaction">5. Compaction</h3>

<p><img src="https://img-blog.csdn.net/20180105141233950?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveXVleWFuZzEzMTQxNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p>压缩仅仅保存topic中每个key最新的值。若一个topic的key为null，则无法压缩。</p>

<p><strong>log.cleaner.enabled</strong> 配置，可以指定是否开启压缩功能。</p>

<p>当我们想删除一个压缩的key，不再保存该key最后的值时，需要再写入一个该key的消息，值为null。下一次压缩执行时，该key的最新值更新成null，代表该key准备删除。这条特别的消息(tombstone)会被保留一段时间(可配置)，来让消费者知晓，做好同步。</p>

<p>当前写入的segment不会被删除，同样也不会被压缩。</p>

<p>在0.10.0版本之后，为了不使压缩太频繁影响读写性能，一般会在冗余数据超过50%时才启动压缩。</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>