---
layout:     post
title:      记一次由于__consumer_offset导致的Kafka故障
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/huochen1994/article/details/80511038				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="记一次kafka故障">记一次Kafka故障</h1>

<hr>

<blockquote>
  <p>2018.05.30 <br>
  Kafka 0.10.0.10</p>
</blockquote>



<h2 id="背景介绍">背景介绍</h2>

<p>因Kafka集群中一个节点宕机，因集群高可用机制集群工作正常，但是发现部分消费者无法读取到数据</p>



<h2 id="故障原因">故障原因</h2>

<p>Kafka将直连Kafka的消费信息记录到了__consumer_offset这个topic中，这个topic在我们的集群中复制因子为1，并且所有的Partition落在了宕机的节点上，导致这个topic信息丢失，消费偏移量记录在这个topic中的消费者无法获取以及记录信息。</p>



<h2 id="如何避免">如何避免</h2>

<p>Kafka Broker Config中针对这个offset topic有两个相关的配置</p>

<table>
<thead>
<tr>
  <th align="center">name</th>
  <th align="center">default</th>
</tr>
</thead>
<tbody><tr>
  <td align="center">offsets.topic.replication.factor</td>
  <td align="center">3</td>
</tr>
<tr>
  <td align="center">offsets.topic.num.partitions</td>
  <td align="center">50</td>
</tr>
</tbody></table>


<p>其中对<code>offsets.topic.replication.factor</code>的解释是：</p>

<blockquote>
  <p>The replication factor for the offsets topic (set higher to ensure availability). To ensure that the effective replication factor of the offsets topic is the configured value, the number of alive brokers has to be at least the replication factor at the time of the first request for the offsets topic. If not, either the offsets topic creation will fail or it will get a replication factor of min(alive brokers, configured replication factor)</p>
</blockquote>

<p>意为记录偏移量的topic默认的复制因子是3，为了保证这个配置生效，需要确保在第一个消费者到来的时候集群中至少有与复制因子相同个数的节点数。否则，这个topic可能会创建失败，或者取存活节点数与配置复制因子的最小值</p>

<p>在<strong>0.11.X</strong>之后的版本对这个参数的解释变成了：</p>

<blockquote>
  <p>The replication factor for the offsets topic (set higher to ensure availability). Internal topic creation will fail until the cluster size meets this replication factor requirement.</p>
</blockquote>

<p>意为仅当集群节点个数大于等于配置的复制因子个数时，topic才能创建成功</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>