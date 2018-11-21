---
layout:     post
title:      Zookeeper在Kafka中的应用
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/qq_24505127/article/details/81321495				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h1>简介</h1>

<p>Kafka使用zookeeper作为其分布式协调框架，很好的将消息生产、消息存储、消息消费的过程结合在一起。同时借助zookeeper，kafka能够生产者、消费者和broker在内的所以组件在无状态的情况下，建立起生产者和消费者的订阅关系，并实现生产者与消费者的负载均衡。</p>

<p> </p>

<h2>从拓扑结构看kafka与zookeeper的关系：</h2>

<p>在典型的Kafka集群中, Kafka通过Zookeeper管理集群配置，选举leader，以及在Consumer Group发生变化时进行rebalance。Producer使用push模式将消息发布到broker，Consumer使用pull模式从broker订阅并消费消息.</p>

<p> </p>

<h2>broker在zookeeper中的注册</h2>

<p>为了记录broker的注册信息，在zookeeper上，专门创建了属于kafka的一个节点，其路径为/brokers，如：</p>

<table><tbody><tr><td>
			<p>[zk: localhost:2181(CONNECTED) 1] ls /brokers</p>

			<p>[ids, topics]</p>
			</td>
		</tr></tbody></table><p> </p>

<p>Kafka的每个broker启动时，都会到zookeeper中进行注册，告诉zookeeper其<a href="http://broker.id/" rel="nofollow">broker.id</a>， 在整个集群中，<a href="http://broker.id/" rel="nofollow">broker.id</a>应该全局唯一，并在zookeeper上创建其属于自己的节点，其节点路径为/brokers/ids/{broker.id}. 如：</p>

<table><tbody><tr><td>
			<p>[zk: localhost:2181(CONNECTED) 2] ls /brokers/ids</p>

			<p>[102, 103]</p>
			</td>
		</tr></tbody></table><p> </p>

<p>创建完节点后，kafka会将该broker的<a href="http://broker.name/" rel="nofollow">broker.name</a>及端口号记录到改节点，如</p>

<table><tbody><tr><td>
			<p>[zk: localhost:2181(CONNECTED) 4] get /brokers/ids/102</p>

			<p>{"jmx_port":-1,"timestamp":"1433209686575","host":"host102","version":1,"port":9092}</p>
			</td>
		</tr></tbody></table><p> </p>

<p>另外，改broker节点属性为临时节点，当broker会话失效时，zookeeper会删除该节点，这样，我们就可以很方便的监控到broker节点的变化，及时调整负载均衡等。</p>

<p> </p>

<h2>Topic在zookeeper中的注册</h2>

<p>在kafka中，用户可以自定义多个topic，每个topic又可以划分为多个分区，一半情况下，每个分区存储在一个独立的broker上。所有这些topic与broker的对应关系都有zookeeper进行维护。</p>

<p> </p>

<p>在zookeeper中，建立专门的节点来记录这些信息，其节点路径为/brokers/topics/{topic_name},如：</p>

<table><tbody><tr><td>
			<p>[zk: localhost:2181(CONNECTED) 6] ls /brokers/topics </p>

			<p>[toptic_t, test, my-replicated-topic, mykafka, mykafka6, mykafka5, mykafka4, test6, mykafka3, test7, mykafka2]</p>
			</td>
		</tr></tbody></table><p> </p>

<table><tbody><tr><td>
			<p>[zk: localhost:2181(CONNECTED) 17] get /brokers/topics/mykafka4   </p>

			<p>{"version":1,"partitions":{"1":[102,103,104],"2":[103,104,102],"0":[104,102,103]}}</p>
			</td>
		</tr></tbody></table><p>针对topic 的每一个分区与broker的对应关系，zookeeper通过节点 /brokers/topics/topic.name来记录，如：</p>

<p> </p>

<p>当broker启动时，会到对应topic节点下注册自己的<a href="http://broker.id/" rel="nofollow">broker.id</a>到对应分区的isr列表中，如：</p>

<table><tbody><tr><td>
			<p>[zk: localhost:2181(CONNECTED) 23] get /brokers/topics/mykafka4/partitions/1/state</p>

			<p>{"controller_epoch":15,"leader":102,"version":1,"leader_epoch":2,"isr":[102,103,104]}</p>
			</td>
		</tr></tbody></table><p> </p>

<p>同样的，当broker退出数，也会触发zookeeper更新其对应topic分区的isr列表，并决定是否需要做消费者的负载均衡。</p>

<p> </p>

<p> </p>

<h2>consumer在zookeeper中的注册</h2>

<p>l 注册新的消费者分组</p>

<p>当新的消费者组注册到zookeeper中时，zookeeper会创建专用的节点来保存相关信息，其节点路径为ls /consumers/{group_id}，其节点下有三个子节点，分别为[ids, owners, offsets]。</p>

<p>Ø ids节点：记录该消费组中当前正在消费的消费者；</p>

<p>Ø owners节点：记录该消费组消费的topic信息；</p>

<p>Ø offsets节点：记录每个topic的每个分区的offset，如：</p>

<table><tbody><tr><td>
			<p>[zk: localhost:2181(CONNECTED) 54] get /consumers/test-consumer2-group/offsets/mykafka4/0</p>

			<p>142</p>
			</td>
		</tr></tbody></table><p> </p>

<p>l 注册新的消费者</p>

<p>当新的消费者注册到kafka中时，会在/consumers/{group_id}/ids节点下创建临时子节点，并记录相关信息，如:</p>

<table><tbody><tr><td>
			<p>[zk: localhost:2181(CONNECTED) 57] ls /consumers/test-consumer2-group/ids/test-consumer2-group_dev103-1433562901087-7b517b97</p>

			<p>[]</p>

			<p>[zk: localhost:2181(CONNECTED) 58] get /consumers/test-consumer2-group/ids/test-consumer2-group_dev103-1433562901087-7b517b97</p>

			<p>{"version":1,"subscription":{"mykafka5":1},"pattern":"white_list","timestamp":"1433562901290"}</p>
			</td>
		</tr></tbody></table><p> </p>

<p>l 监听消费者分组中消费者的变化</p>

<p>每个消费者都要关注其所属消费者组中消费者数目的变化，即监听/consumers/{group_id}/ids下子节点的变化。一单发现消费者新增或减少，就会触发消费者的负载均衡。</p>            </div>
                </div>