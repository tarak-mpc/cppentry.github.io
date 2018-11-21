---
layout:     post
title:      一些基于Kafka Connect的开源连接器
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>在Apache Kafka 0.9版本中，Kafka Connect特性被添加让Kafka可以建立可扩展和安全的流数据管道。下表中，<a href="http://www.confluent.io/developers/connectors?utm_content=buffer1d107&amp;utm_medium=social&amp;utm_source=twitter.com&amp;utm_campaign=buffer" rel="nofollow">Kafka Connector Hub</a>列出了一些基于Kafka Connect建立的connectors。如果你发现新的，也可以联系confluent-platform@googlegroups.com添加。</p><table border="1" width="777"><tbody><tr><td><strong>Connector Name</strong></td>
<td><strong>Owner</strong></td>
<td><strong>Status</strong></td>
</tr><tr><td> <a href="http://docs.confluent.io/2.0.0/connect/connect-hdfs/docs/index.html" rel="nofollow">HDFS</a></td>
<td><a href="mailto:confluent-platform@googlegroups.com" rel="nofollow">confluent-platform@googlegroups.com</a></td>
<td><a href="http://www.confluent.io/developer#download" rel="nofollow">Confluentsupported</a></td>
</tr><tr><td><a href="http://docs.confluent.io/2.0.0/connect/connect-jdbc/docs/index.html" rel="nofollow">JDBC</a></td>
<td><a href="mailto:confluent-platform@googlegroups.com" rel="nofollow">confluent-platform@googlegroups.com</a></td>
<td><a href="http://www.confluent.io/developer#download" rel="nofollow"><span>Confluentsupported</span></a></td>
</tr><tr><td><a href="https://github.com/debezium" rel="nofollow">Debezium - CDC Sources</a></td>
<td><a href="mailto:debezium@gmail.com" rel="nofollow">debezium@gmail.com</a></td>
<td><span>Community project</span></td>
</tr><tr><td><a href="https://github.com/DataReply/kafka-connect-mongodb" rel="nofollow">MongoDB Source</a></td>
<td><a href="mailto:a.patelli@reply.de" rel="nofollow">a.patelli@reply.de</a><span><br></span><a href="mailto:a.topchyan@reply.de" rel="nofollow">a.topchyan@reply.de</a></td>
<td>In progress</td>
</tr><tr><td><a href="https://github.com/evokly/kafka-connect-mqtt" rel="nofollow"><span>MQTT Source</span></a></td>
<td><a href="mailto:tomasz.pietrzak@evok.ly" rel="nofollow">tomasz.pietrzak@evok.ly</a></td>
<td>Community project</td>
</tr><tr><td><a href="https://github.com/wushujames/kafka-mysql-connector" rel="nofollow"><span>MySQL Binlog Source</span></a></td>
<td><a href="mailto:wushujames@gmail.com" rel="nofollow" class="external-link">wushujames@gmail.com</a></td>
<td>In progress</td>
</tr><tr><td><a href="https://github.com/rollulus/twitter-kafka-connect" rel="nofollow"><span>Twitter Source</span></a></td>
<td><a href="mailto:rollulus@xs4all.nl" rel="nofollow">rollulus@xs4all.nl</a></td>
<td>In progress</td>
</tr><tr><td><span></span><a href="https://github.com/andrewstevenson/stream-reactor/tree/master/kafka-connect-cassandra" rel="nofollow">Cassandra Sink</a></td>
<td><span></span><a href="https://github.com/andrewstevenson/stream-reactor/tree/master/kafka-connect-cassandra" rel="nofollow">Cassandra Sink</a></td>
<td>Community project</td>
</tr><tr><td><a href="https://github.com/ksenji/kafka-connect-es" rel="nofollow">Elastic Search Sink</a></td>
<td><span><a href="mailto:ksenji@gmail.com" rel="nofollow">ksenji@gmail.com</a></span></td>
<td>Community project</td>
</tr><tr><td><a href="https://github.com/hannesstockner/kafka-connect-elasticsearch" rel="nofollow">Elastic Search Sink</a></td>
<td><span><a href="mailto:hannes.stockner@gmail.com" rel="nofollow" class="external-link">hannes.stockner@gmail.com</a></span></td>
<td>In progress</td>
</tr><tr><td><a href="https://github.com/DataReply/kafka-connect-elastic-search-sink" rel="nofollow">Elastic Search Sink</a></td>
<td><span><a href="mailto:a.patelli@reply.de" rel="nofollow">a.patelli@reply.de</a><span><br></span><a href="mailto:a.topchyan@reply.de" rel="nofollow">a.topchyan@reply.de</a></span></td>
<td>In progress</td>
</tr></tbody></table><p>责编/仲浩</p><blockquote>
  <p>2016年3月18日-19日，由CSDN重磅打造的<a href="http://bss.csdn.net/m/topic/sdcc_invite" rel="nofollow">数据库核心技术与实战应用峰会、互联网应用架构实战峰会</a>将在上海举行。这两场峰会将邀请业内顶尖的架构师和技术专家，共同探讨<strong>高可用/高并发系统架构设计、新技术应用、移动应用架构、微服务、智能硬件架构、云数据库实战、新一代数据库平台、产品选型、性能调优、大数据应用实战</strong>等领域的热点话题与技术。</p>
  
  <p><strong>2月29日24点前</strong>仍处于<a href="http://bss.csdn.net/m/topic/sdcc_invite" rel="nofollow">最低六折优惠票价</a>阶段，单场峰会（含餐）门票只需799元，5人以上团购或者购买两场峰会通票更有特惠，限量供应，预购从速。（<a href="http://bss.csdn.net/m/topic/sdcc_invite" rel="nofollow">票务详情链接</a>）。</p>
</blockquote>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>