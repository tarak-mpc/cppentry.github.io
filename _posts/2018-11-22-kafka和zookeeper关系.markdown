---
layout:     post
title:      kafka和zookeeper关系
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>kafka使用ZooKeeper用于管理、协调代理。每个Kafka代理通过Zookeeper协调其他Kafka代理。</p>

<p>当Kafka系统中新增了代理或某个代理失效时，Zookeeper服务将通知生产者和消费者。生产者与消费者据此开始与其他代理协调工作。</p>

<p>Zookeeper在Kakfa中扮演的角色：Kafka将元数据信息保存在Zookeeper中，但是发送给Topic本身的数据是不会发到Zk上的<br>
 </p>

<p>·        kafka使用zookeeper来实现动态的集群扩展，不需要更改客户端（producer和consumer）的配置。broker会在zookeeper注册并保持相关的元数据（topic，partition信息等）更新。</p>

<p>·        而客户端会在zookeeper上注册相关的watcher。一旦zookeeper发生变化，客户端能及时感知并作出相应调整。这样就保证了添加或去除broker时，各broker间仍能自动实现负载均衡。这里的客户端指的是Kafka的消息生产端(Producer)和消息消费端(Consumer)</p>

<p>·        Broker端使用zookeeper来注册broker信息,以及监测partitionleader存活性.</p>

<p>·        Consumer端使用zookeeper用来注册consumer信息,其中包括consumer消费的partition列表等,同时也用来发现broker列表,并和partitionleader建立socket连接,并获取消息.</p>

<p>·        Zookeer和Producer没有建立关系，只和Brokers、Consumers建立关系以实现负载均衡，即同一个ConsumerGroup中的Consumers可以实现负载均衡（因为Producer是瞬态的，可以发送后关闭，无需直接等待）</p>

<p> </p>

<p>源自：<a href="https://blog.csdn.net/tanggao1314/article/details/51932329" rel="nofollow">https://blog.csdn.net/tanggao1314/article/details/51932329</a></p>            </div>
                </div>