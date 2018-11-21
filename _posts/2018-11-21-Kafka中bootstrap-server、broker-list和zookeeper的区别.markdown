---
layout:     post
title:      Kafka中bootstrap-server、broker-list和zookeeper的区别
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>参考 <a href="https://stackoverflow.com/questions/41774446/kafka-bootstrap-servers-vs-zookeeper-in-kafka-console-consumer" rel="nofollow">Kafka bootstrap-servers vs zookeeper in kafka-console-consumer</a>  中说建议使用新版的 --bootstrap-server</p>

<p> </p>

<p>Kafka专业术语，参考 <a href="https://www.ibm.com/developerworks/cn/opensource/os-cn-kafka/index.html" rel="nofollow">Apache kafka 工作原理介绍</a></p>

<p><strong>Broker</strong>：Kafka 集群包含一个或多个服务器，这种服务器被称为 broker。</p>

<p><strong>Topic</strong>：每条发布到 Kafka 集群的消息都有一个类别，这个类别被称为 Topic。（物理上不同 Topic 的消息分开存储，逻辑上一个 Topic 的消息虽然保存于一个或多个 broker 上，但用户只需指定消息的 Topic 即可生产或消费数据而不必关心数据存于何处）。</p>

<p><strong>Partition</strong>：Partition 是物理上的概念，每个 Topic 包含一个或多个 Partition。</p>

<p><strong>Producer</strong>：负责发布消息到 Kafka broker。</p>

<p><strong>Consumer</strong>：消息消费者，向 Kafka broker 读取消息的客户端。</p>

<p><strong>Consumer Group</strong>：每个 Consumer 属于一个特定的 Consumer Group（可为每个 Consumer 指定 group name，若不指定 group name 则属于默认的 group）。</p>

<p> </p>

<p>在<strong>《Kafka权威指南》</strong>中是这样描述的</p>

<p><img alt="" class="has" src="https://images2018.cnblogs.com/blog/517519/201804/517519-20180425152328157-954538404.png"></p>

<p> </p>

<p>对于<strong>消费者</strong>，kafka中有两个设置的地方：对于老的消费者，由<strong>--zookeeper参数</strong>设置；对于新的消费者，由<strong>--bootstrap-server参数</strong>设置</p>

<p><img alt="" class="has" src="https://images2018.cnblogs.com/blog/517519/201804/517519-20180425153405277-265739183.png"></p>

<p> </p>

<p>对于<strong>console生产者</strong>，<strong>--broker-list参数</strong>指定了所使用的broker</p>

<p> </p>

<p>文章转载至：</p>

<p><a href="https://www.cnblogs.com/tonglin0325/p/8810313.html" rel="nofollow">https://www.cnblogs.com/tonglin0325/p/8810313.html</a></p>            </div>
                </div>