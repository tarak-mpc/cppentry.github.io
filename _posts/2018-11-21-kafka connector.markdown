---
layout:     post
title:      kafka connector
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/jansony1/article/details/51548191				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p style="line-height:25.2px;color:rgb(64,64,64);font-family:'Microsoft YaHei', Verdana, sans-serif, SimSun;font-size:14px;">
转载+修改 来自： <a href="http://colobu.com/2016/02/24/kafka-connect/" rel="nofollow" style="color:rgb(0,95,169);text-decoration:none;background-color:transparent;">http://colobu.com/2016/02/24/kafka-connect/</a>  </p>
<div style="line-height:25.2px;color:rgb(64,64,64);font-family:'Microsoft YaHei', Verdana, sans-serif, SimSun;font-size:14px;">
<p style="line-height:25.2px;">Kafka 0.9+增加了一个新的特性 <span style="font-weight:700;">Kafka Connect</span> ,可以更方便的创建和管理数据流管道。它为Kafka和其它系统创建规模可扩展的、可信赖的流数据提供了一个简单的模型，通过 <span style="font-weight:700;">connectors</span> 可以将大数据从其它系统导入到Kafka中，也可以从Kafka中导出到其它系统。Kafka
 Connect可以将完整的数据库注入到Kafka的Topic中，或者将服务器的系统监控指标注入到Kafka，然后像正常的Kafka流处理机制一样进行数据流处理。而导出工作则是将数据从Kafka Topic中导出到其它数据存储系统、查询系统或者离线分析系统等，比如数据库、 <a href="https://www.elastic.co/" rel="nofollow" style="color:rgb(0,95,169);text-decoration:none;background-color:transparent;">Elastic
 Search</a> 、<a href="https://ignite.apache.org/" rel="nofollow" style="color:rgb(0,95,169);text-decoration:none;background-color:transparent;">Apache Ignite</a> 等。</p>
<p style="line-height:25.2px;">Kafka Connect特性包括：</p>
<ul style="font-size:16px;"><li style="line-height:28.8px;">Kafka connector通用框架,提供统一的集成API</li><li style="line-height:28.8px;">同时支持分布式模式和单机模式</li><li style="line-height:28.8px;">REST 接口，用来查看和管理Kafka connectors</li><li style="line-height:28.8px;">自动化的offset管理，开发人员不必担心错误处理的影响</li><li style="line-height:28.8px;">分布式、可扩展</li><li style="line-height:28.8px;">流/批处理集成</li></ul><h2 id="articleHeader0" style="font-family:inherit;font-weight:500;line-height:1.1;color:inherit;font-size:1.75em;border-bottom-width:1px;border-bottom-style:solid;border-bottom-color:rgb(238,238,238);">
流数据平台</h2>
<p style="line-height:25.2px;"><img src="http://static.open-open.com/lib/uploadImg/20160224/20160224154902_420.png" class="alignCenter" width="380" height="285" alt="" style="border:0px;vertical-align:middle;"></p>
<p style="line-height:25.2px;">基于Kafka， LinkedIn等一些大公司已经建立起架构类似的、可扩展的流数据平台。它包含两个功能，数据集成和流处理。Kafka Connect则是为数据集成而生。</p>
<p style="line-height:25.2px;"><img src="http://static.open-open.com/lib/uploadImg/20160224/20160224154902_194.png" class="alignCenter" width="195" height="197" alt="" style="border:0px;vertical-align:middle;"></p>
<p style="line-height:25.2px;">多年来，Kafka已经成为处理大数据流的平台标准， <a href="https://cwiki.apache.org/confluence/display/KAFKA/Powered+By" rel="nofollow" style="color:rgb(0,95,169);text-decoration:none;background-color:transparent;">成千上万的公司在使用它</a> 。程序员在构建它们的平台的时候也遇到一些问题：</p>
<ul style="font-size:16px;"><li style="line-height:28.8px;">Schema管理</li><li style="line-height:28.8px;">容错</li><li style="line-height:28.8px;">并行化</li><li style="line-height:28.8px;">数据延迟</li><li style="line-height:28.8px;">分发担保</li><li style="line-height:28.8px;">运营与监控</li></ul><div style="line-height:25.2px;">
<p style="line-height:25.2px;">这些棘手的问题都要程序员去处理，如果有一个统一的框架去完成这些事情，将可以大大减少程序员的工作量，因此Kafka 0.9中提供了这一特性，负责处理这些问题。</p>
<img src="http://cdn2.hubspot.net/hub/540072/hubfs/blog-files/Kafka_and_the_age_of_streaming_data_integration/Kafka_and_the_age_of_streaming_data_integration_image_kafka_connect.png?t=1455819300250&amp;width=580&amp;height=435" class="alignCenter" alt="" style="border:0px;vertical-align:middle;"></div>
<p style="line-height:25.2px;">Kafka背后的公司confluent鼓励社区创建更多的开源的connector，将Kafka生态圈壮大起来，促进Kafka Connnect的应用。</p>
<div style="line-height:25.2px;">
<p style="line-height:25.2px;">Kafka Connnect有两个核心概念：Source和Sink。 Source负责导入数据到Kafka，Sink负责从Kafka导出数据，它们都被称为Connector。</p>
<p style="line-height:25.2px;">在这里原作没有提出一个核心的观点，也就是数据仓库转移（ETL）。</p>
<p style="line-height:25.2px;"><strong><span style="color:rgb(51,51,51);font-family:'Source Sans Pro', sans-serif;font-size:17.3333px;line-height:30.3333px;">Kafka Connect is a tool for scalably and reliably streaming data
 between Apache Kafka and other systems. It makes it simple to quickly define </span><span style="color:rgb(51,51,51);font-family:'Source Sans Pro', sans-serif;font-size:17.3333px;line-height:30.3333px;">connectors</span><span style="color:rgb(51,51,51);font-family:'Source Sans Pro', sans-serif;font-size:17.3333px;line-height:30.3333px;"> that
 move large collections of data into and out of Kafka. Kafka Connect can ingest entire databases or collect metrics from all your application servers into Kafka topics, making the data available for stream processing with low latency. An export job can deliver
 data from Kafka topics into secondary storage and query systems or into batch systems for offline analysis. Kafka Connect features include:</span></strong><br></p>
<p style="line-height:25.2px;">另外，现在有些公司提供了更为细度的集群管理。 以往，我们consumer 的所有group都是有zookeeper 来控制，那么完全不相干的group之间的其中一个consumer有一些变化，整个consumer集群变回由zookeeper来进行re-balance。现如今更细度的管理，使用kafka connect可以仅对某一个group中的consumer进行re-balance。
 （此处 关于是针对所有consumer来进行re-balance还是group内 可以依据项目 来定）</p>
<img src="http://cdn2.hubspot.net/hub/540072/hubfs/blog-files/Kafka_and_the_age_of_streaming_data_integration/Kafka_and_the_age_of_streaming_data_integration_image_sources_sinks.png?t=1455819300250&amp;width=580&amp;height=435" class="alignCenter" alt="" style="border:0px;vertical-align:middle;"></div>
<p style="line-height:25.2px;">当前Kafka Connect支持两种分发担保：at least once (至少一次) 和 at most once(至多一次)，exactly once将在未来支持。</p>
<p style="line-height:25.2px;">当前已有的Connectors包括：</p>
<table class="ke-zeroborder" style="border-spacing:0px;border-collapse:collapse;background-color:transparent;"><tbody><tr><td width="31%"><span style="font-weight:700;color:rgb(64,64,64);">Connector Name</span></td>
<td width="41%"><span style="font-weight:700;color:rgb(64,64,64);">Owner</span></td>
<td width="28%"><span style="font-weight:700;color:rgb(64,64,64);">Status</span></td>
</tr><tr><td width="31%"><a href="http://docs.confluent.io/2.0.0/connect/connect-hdfs/docs/index.html" rel="nofollow" style="color:rgb(0,95,169);text-decoration:none;background-color:transparent;">HDFS</a></td>
<td width="41%"><a href="mailto:confluent-platform@googlegroups.com" rel="nofollow" style="color:rgb(0,95,169);text-decoration:none;background-color:transparent;">confluent-platform@googlegroups.com</a></td>
<td width="28%"><a href="http://www.confluent.io/developer#download" rel="nofollow" style="color:rgb(0,95,169);text-decoration:none;background-color:transparent;">Confluent supported</a></td>
</tr><tr><td width="31%"><a href="http://docs.confluent.io/2.0.0/connect/connect-jdbc/docs/index.html" rel="nofollow" style="color:rgb(0,95,169);text-decoration:none;background-color:transparent;">JDBC</a></td>
<td width="41%"><a href="mailto:confluent-platform@googlegroups.com" rel="nofollow" style="color:rgb(0,95,169);text-decoration:none;background-color:transparent;">confluent-platform@googlegroups.com</a></td>
<td width="28%"><a href="http://www.confluent.io/developer#download" rel="nofollow" style="color:rgb(0,95,169);text-decoration:none;background-color:transparent;">Confluent supported</a></td>
</tr><tr><td width="31%"><a href="https://github.com/debezium" rel="nofollow" style="color:rgb(0,95,169);text-decoration:none;background-color:transparent;">Debezium - CDC Sources</a></td>
<td width="41%"><a href="mailto:debezium@gmail.com" rel="nofollow" style="color:rgb(0,95,169);text-decoration:none;background-color:transparent;">debezium@gmail.com</a></td>
<td width="28%">Community project</td>
</tr><tr><td width="31%"><a href="https://github.com/DataReply/kafka-connect-mongodb" rel="nofollow" style="color:rgb(0,95,169);text-decoration:none;background-color:transparent;">MongoDB Source</a></td>
<td width="41%"><a href="mailto:a.patelli@reply.de" rel="nofollow" style="color:rgb(0,95,169);text-decoration:none;background-color:transparent;">a.patelli@reply.de</a>   <a href="mailto:a.topchyan@reply.de" rel="nofollow" style="color:rgb(0,95,169);text-decoration:none;background-color:transparent;">a.topchyan@reply.de</a></td>
<td width="28%">In progress</td>
</tr><tr><td width="31%"><a href="https://github.com/evokly/kafka-connect-mqtt" rel="nofollow" style="color:rgb(0,95,169);text-decoration:none;background-color:transparent;">MQTT Source</a></td>
<td width="41%"><a href="mailto:tomasz.pietrzak@evok.ly" rel="nofollow" style="color:rgb(0,95,169);text-decoration:none;background-color:transparent;">tomasz.pietrzak@evok.ly</a></td>
<td width="28%">Community project</td>
</tr><tr><td width="31%"><a href="https://github.com/wushujames/kafka-mysql-connector" rel="nofollow" style="color:rgb(0,95,169);text-decoration:none;background-color:transparent;">MySQL Binlog Source</a></td>
<td width="41%"><a href="mailto:wushujames@gmail.com" rel="nofollow" style="color:rgb(0,95,169);text-decoration:none;background-color:transparent;">wushujames@gmail.com</a></td>
<td width="28%">In progress</td>
</tr><tr><td width="31%"><a href="https://github.com/rollulus/twitter-kafka-connect" rel="nofollow" style="color:rgb(0,95,169);text-decoration:none;background-color:transparent;">Twitter Source</a></td>
<td width="41%"><a href="mailto:rollulus@xs4all.nl" rel="nofollow" style="color:rgb(0,95,169);text-decoration:none;background-color:transparent;">rollulus@xs4all.nl</a></td>
<td width="28%">In progress</td>
</tr><tr><td width="31%"><a href="https://github.com/andrewstevenson/stream-reactor/tree/master/kafka-connect-cassandra" rel="nofollow" style="color:rgb(0,95,169);text-decoration:none;background-color:transparent;">Cassandra
 Sink</a></td>
<td width="41%"><a href="https://github.com/andrewstevenson/stream-reactor/tree/master/kafka-connect-cassandra" rel="nofollow" style="color:rgb(0,95,169);text-decoration:none;background-color:transparent;">Cassandra
 Sink</a></td>
<td width="28%">Community project</td>
</tr><tr><td width="31%"><a href="https://github.com/ksenji/kafka-connect-es" rel="nofollow" style="color:rgb(0,95,169);text-decoration:none;background-color:transparent;">Elastic Search Sink</a></td>
<td width="41%"><a href="mailto:ksenji@gmail.com" rel="nofollow" style="color:rgb(0,95,169);text-decoration:none;background-color:transparent;">ksenji@gmail.com</a></td>
<td width="28%">Community project</td>
</tr><tr><td width="31%"><a href="https://github.com/hannesstockner/kafka-connect-elasticsearch" rel="nofollow" style="color:rgb(0,95,169);text-decoration:none;background-color:transparent;">Elastic Search
 Sink</a></td>
<td width="41%"><a href="mailto:hannes.stockner@gmail.com" rel="nofollow" style="color:rgb(0,95,169);text-decoration:none;background-color:transparent;">hannes.stockner@gmail.com</a></td>
<td width="28%">In progress</td>
</tr><tr><td width="31%"><a href="https://github.com/DataReply/kafka-connect-elastic-search-sink" rel="nofollow" style="color:rgb(0,95,169);text-decoration:none;background-color:transparent;">Elastic Search
 Sink</a></td>
<td width="41%"><a href="mailto:a.patelli@reply.de" rel="nofollow" style="color:rgb(0,95,169);text-decoration:none;background-color:transparent;">a.patelli@reply.de</a>   <a href="mailto:a.topchyan@reply.de" rel="nofollow" style="color:rgb(0,95,169);text-decoration:none;background-color:transparent;">a.topchyan@reply.de</a></td>
<td width="28%">In progress</td>
</tr><tr><td width="31%"><a href="https://github.com/apache/ignite/tree/master/modules/kafka" rel="nofollow" style="color:rgb(0,95,169);text-decoration:none;background-color:transparent;">Apache Ignite Sink</a></td>
<td width="41%"><a href="https://ignite.apache.org/" rel="nofollow" style="color:rgb(0,95,169);text-decoration:none;background-color:transparent;">Apache Ignite Project</a></td>
<td width="28%">
<p style="line-height:25.2px;color:rgb(64,64,64);">
Community project</p>
<p style="line-height:25.2px;color:rgb(64,64,64);">
(Planned for Apache Ignite 1.6 Release)</p>
</td>
</tr></tbody></table><p style="line-height:25.2px;">Connectors的发布和开发可以参照 <a href="http://kafka.apache.org/documentation.html#connect_user" rel="nofollow" style="color:rgb(0,95,169);text-decoration:none;background-color:transparent;">官方文档</a> 。如果以前你通过producer
 API/consumer API写了一些导入导出的功能，不妨尝试一下换成Kafka Connect，看看是否简化了你的代码，提高了应用可扩展和容错的能力。</p>
<p style="line-height:25.2px;">以下部分为个人原创</p>
<p style="line-height:25.2px;"><br></p>
<p style="line-height:25.2px;"><br></p>
</div>
            </div>
                </div>