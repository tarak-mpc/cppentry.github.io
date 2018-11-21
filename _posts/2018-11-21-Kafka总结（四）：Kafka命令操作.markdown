---
layout:     post
title:      Kafka总结（四）：Kafka命令操作
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p><a href="https://mp.csdn.net/postedit/81283142" rel="nofollow">Kafka总结（一）：Kafka概述</a></p>

<p><a href="https://mp.csdn.net/postedit/81283229" rel="nofollow">Kafka总结（二）：Kafka核心组件</a></p>

<p><a href="https://mp.csdn.net/postedit/81283286" rel="nofollow">Kafka总结（三）：Kafka核心流程分析</a></p>

<p><a href="https://mp.csdn.net/postedit/81283397" rel="nofollow">Kafka总结（四）：Kafka命令操作</a></p>

<p><a href="https://mp.csdn.net/postedit/81283460" rel="nofollow">Kafka总结（五）：API编程详解</a></p>

<p><a href="https://mp.csdn.net/postedit/81283491" rel="nofollow">Kafka总结（六）：Kafka Stream详解</a></p>

<p><a href="https://mp.csdn.net/postedit/81283546" rel="nofollow">Kafka总结（七）：数据采集应用</a></p>

<p><a href="https://mp.csdn.net/postedit/81283568" rel="nofollow">Kafka总结（八）：KafKa与ELK整合应用</a></p>

<p><a href="https://mp.csdn.net/postedit/81283606" rel="nofollow">Kafka总结（九）：KafKa 与Spark整合应用</a></p>

<h2 style="margin-left:0cm;">1.KafKaServer管理</h2>

<h3 style="margin-left:0cm;">1.启动KafKa单个节点</h3>

<p style="margin-left:0cm;">kafka-server-start.sh</p>

<h3 style="margin-left:0cm;">2. 启动KafKa集群</h3>

<p style="margin-left:0cm;">自定义脚本启动kafka集群；</p>

<p style="margin-left:0cm;">遍历brokers指定的代理列表取出每个节点，通过SSH方式登录该节点，执行kafka-server-start.sh脚本，启动Kafka。前提，需要确保安装配置了SSH；</p>

<p style="margin-left:0cm;">对自定义脚本赋予执行权限。因为Kafka运行在JVM之上，因此会依赖相应的系统环境配置，为了保证各环境配置在执行该脚本的时候已经生效，在启动命令的时候可以加入，source/etc/profile命令；</p>

<h3 style="margin-left:0cm;">3.关闭KafKa单个节点</h3>

<p style="margin-left:0cm;">kafka-server-stop.sh脚本</p>

<h3 style="margin-left:0cm;">4.关闭KafKa集群</h3>

<p style="margin-left:0cm;">自定义脚本，通过遍历调用kafka-server-stop.sh脚本实现</p>

<h2 style="margin-left:0cm;">2.主题管理</h2>

<p style="margin-left:0cm;">Kafka提供了一个kafka-topic.sh工具脚本用于对主题相关的操作，如创建主题、删除主题、修改主题分区数和副本分配以及修改主题级别的配置信息，查看主题信息等操作；</p>

<h3 style="margin-left:0cm;">1.创建主题</h3>

<p style="margin-left:0cm;">Kafka提供以下两种方式创建主题：</p>

<ol><li>方式一：如果代理设置了auto.create.topics.enable=true，则当生产者向一个还未创建主题发送消息的时候，会自动创建一个拥有${num.partitions}个分区和${default.replication.factor}个副本的主题；</li>
	<li>方式二：客户端通过执行kafka-topics.sh脚本创建一个主题</li>
</ol><h3 style="margin-left:0cm;">2.删除主题</h3>

<ol><li>方式一：手动删除各节点${log.dir} 目录下该主题分区文件夹，同时登录ZooKeeper客户端删除主题对应的节点，主题元素据保存在/brokers/topics和/config/topics目录下</li>
	<li>方式二：执行kafka-topics.sh脚本进行删除，如果希望通过该脚本彻底删除主题，则需要保证在启动Kafka的时候所加载的server.properties文件中配置delete.topic.enable=true，该配置默认为false，否则执行该脚本并不是真正删除主题，只是在ZooKeeper的/admin/delete_topics目录下创建一个与待删除主题同名的节点，将该主题标记为删除状态；</li>
</ol><h3 style="margin-left:0cm;">3.查看主题</h3>

<p style="margin-left:0cm;">Kafka提供了list和describe两个命令查看主题信息，其中list参数列出Kafka所有的主题名，describe参数可以查看所有的主题或者某个特定主题的信息；</p>

<ol><li>查看所有主题</li>
</ol><p style="margin-left:0cm;">kafka-topic.sh --list --ZooKeeper server-1:2181,server-2:2181</p>

<ol><li>查看某个特定主题信息</li>
</ol><p style="margin-left:0cm;">kafka-topic.sh --describe --ZooKeeper server-1:2181,server-2:2181</p>

<p style="margin-left:0cm;">可以通过指定topic参数查看特定的主题的信息；</p>

<ol><li>查看正在同步的主题</li>
</ol><p style="margin-left:0cm;">通过describe与under-replicated-partitions命令组合使用</p>

<ol><li>查看没有Leader的分区</li>
</ol><p style="margin-left:0cm;">通过describe与unavailable-partitions命令组合使用</p>

<ol><li>查看主题覆盖的配置</li>
</ol><p style="margin-left:0cm;">通过describe与topic-with-overrides命令组合使用</p>

<h3 style="margin-left:0cm;">4.修改主题</h3>

<p style="margin-left:0cm;">当创建一个主题之后，可以通过alter命令对主题进行修改，包括修改主题级别的配置、增加主题分区、修改副本分配方案、修改主题Offset等</p>

<p style="margin-left:0cm;"> </p>

<ol><li>修改主题级别配置</li>
</ol><p style="margin-left:0cm;">在创建主题的时候，可以通过config参数覆盖主题级别的默认配置，当主题创建之后可以通过alter与config参数组合使用，修改或者增加新的配置以覆盖相应配置原来的值，或者通过alter与delete-config删除相关配置使其恢复默认值。</p>

<p style="margin-left:0cm;">Kafka提供了一个kafka-configs.sh的脚本，专门用来对配置进行操作；</p>

<p>       2.增加分区</p>

<p style="margin-left:0cm;">Kafka并不支持减少分区的操作，只能为一个主题增加分区；</p>

<h2 style="margin-left:0cm;">3.生产者基本操作</h2>

<p style="margin-left:0cm;">Kafka自带了一个在终端演示生产者发布消息的脚本kafka-console-producer.sh；</p>

<h3 style="margin-left:0cm;">1.启动生产者</h3>

<p style="margin-left:0cm;">Kafka自带了一个kafka-console-producer.sh，通过执行该脚本可以在终端调用Kafka生产者向Kafka发送消息。该脚本需要指定broker-list参数：用于指定Kafka的代理地址列表，topic参数：指定消息被发送的目标主题；</p>

<h3 style="margin-left:0cm;">2.创建主题</h3>

<p style="margin-left:0cm;">如果开启了自动创建主题的配置项，auto.create.topics.enable=true，当生产者向一个还不存在的主题发送消息的时候，Kafka会自动的创建该主题；</p>

<h3 style="margin-left:0cm;">3.查看消息</h3>

<p style="margin-left:0cm;">Kafka生产的消息是以二进制的形式存放在文件中，为了便于查看内容，Kafka提供了一个查看日志文件的工具类DumpLogSegments，通过kafka-run-class.sh，可以直接在终端运行该工具，files是必传的参数，用于指定要转储文件的路径，可以同时指定多个文件；</p>

<h3 style="margin-left:0cm;">4.生产者性能测试工具</h3>

<p style="margin-left:0cm;">Kafka提供了一个用来测试生产者性能的工具脚本kafka-producer-perf-test.sh，通过该工具可以对生产者性能进行调优，通过优化不同的配置来提升生产者的效率，从而得到一组最优的参数配置，提高吞吐量；</p>

<h2 style="margin-left:0cm;">4.消费者基本操作</h2>

<h3 style="margin-left:0cm;">1.消费信息</h3>

<p style="margin-left:0cm;">Kafka采用消费组的模式，同一个消费组下的各消费者在消费消息的时候是互斥的，也就是说，对于一条消息而言，就同一个消费组下的消费者来讲，只能被同组下的一个消费者消费，但不同的消费组的消费者能够消费同一条消息。如此，我们可以通过消费组来实现消息的单播和广播；</p>

<ol><li>旧版高级消费者</li>
</ol><p style="margin-left:0cm;">在kafka-console-consumer.sh脚本通过是否指定zookeeper参数区分是旧版高级消费者还是新版消费者，因为新版消费者已经不依赖于ZooKeeper；</p>

<ol><li>旧版低级消费者</li>
</ol><p style="margin-left:0cm;">kafka-simple-consumer-shell.sh</p>

<p>      2.新版消费者</p>

<p style="margin-left:0cm;">kafka-console-consumer.sh</p>

<p style="margin-left:0cm;">【4】消费多主体</p>

<p style="margin-left:0cm;">kafka-console.consumer.sh的topic参数不能指定多主体，可以通过whitelist同时指定多个主题；</p>

<h3 style="margin-left:0cm;">2.单播与多播</h3>

<p style="margin-left:0cm;">Kafka引入了消费组，每个消费者都是与一个特定的消费组，通过消费组就可以实现消息的单播和多播；</p>

<ol><li>单播</li>
</ol><p style="margin-left:0cm;">一条消息只能被某个消费者消费；实现方式：让这些消费者属于同一个消费组即可；</p>

<p>       2.多播：一条消息能够被多个消费者消费的模式</p>

<p style="margin-left:0cm;">实现多播，保证这些消费者属于不同的消费组即可；</p>

<h3 style="margin-left:0cm;">3.查看消费偏移量</h3>

<p style="margin-left:0cm;">Kafka提供了一个查看某个消费组消费偏移量的kafka-consumer-offset-checker.sh脚本。通过该脚本可以查看某个消费组消费信息的情况；</p>

<p style="margin-left:0cm;">在0.9版本之后建议使用kafka-consumer-groups.sh</p>

<h3 style="margin-left:0cm;">4.消费者性能测试工具</h3>

<p style="margin-left:0cm;">kafka-consumer-perf-test.sh</p>

<h2 style="margin-left:0cm;">5.配置管理</h2>

<p style="margin-left:0cm;">Kafka提供了kafka-configs.sh脚本用于对配置进行管理操作，支持修改配置（--alter）和查看配置（--describe）两个基本操作；</p>

<h3 style="margin-left:0cm;">1.主题级别配置</h3>

<p style="margin-left:0cm;">kafka-configs.sh   ---entity-type topics表示操作的配置类型为主题级别，--entity-name指定待操作的主题名</p>

<h3 style="margin-left:0cm;">2.代理级别设置</h3>

<p style="margin-left:0cm;">在分区迁移的时候通过临时修改如下两个配置，通过对复制流量的合理的控制，可以实现数据之间的平滑迁移；</p>

<p style="margin-left:0cm;">follower.replication.throttled.rate：设置Follower复制的速率，单位为B/S</p>

<p style="margin-left:0cm;">leader.replication.throttled.rate：设置Leader节点传输速率，单位为B/S</p>

<h3 style="margin-left:0cm;">3.客户端/用户级别配置</h3>

<p style="margin-left:0cm;">当前版本的Kafka在客户端级别以及用户级别支持配置生产者每秒最多写入消息的字节数（producer_byte_rate）和消费者每秒拉取的字节数（consumer_byte_rate），我们简称为流控设置。</p>

<h2 style="margin-left:0cm;">6.分区操作</h2>

<h3 style="margin-left:0cm;">1.分区Leader平衡</h3>

<p style="margin-left:0cm;">Kafka提供了两种方法重新选择优先副本作为分区Leader的方法，使集群负载重新达到平衡。</p>

<ol><li>自动平衡：</li>
</ol><p style="margin-left:0cm;">auto.leader.rebalance.enable=true</p>

<p>      2.手动平衡：</p>

<p style="margin-left:0cm;">Kafka提供了一个对分区Leader进行重新平衡的工具脚本kafka-preferred-replica-election.sh，通过该工具将优先副本选举为Leader，从而重新让集群分区达到平衡；</p>

<h3 style="margin-left:0cm;">2.分区迁移</h3>

<p style="margin-left:0cm;">kafka-reassign-partitions.sh脚本的用法，高脚本在集群扩容，节点下线等场景时对分区迁移操作，从而使得集群负载达到均衡；</p>

<p style="margin-left:0cm;">当下线一个节点的时候，需要将该节点上的分区副本迁移到其他可用的节点上，Kafka并不会自动的进行分区副本迁移，如果不进行手动的重新发分配，就会导致某些主题数据丢失和不可用的情况；</p>

<p style="margin-left:0cm;">当新增加节点的时候，也只有新创建的主题才会分配到新的节点上，而之前的主题的分区并不会自动分配到新加入的节点上，因为在主题创建的时候，该主题的AR列表中并没有新加入的节点；</p>

<p style="margin-left:0cm;">为了解决这些问题，就需要对分区副本重新进行合理的分配；</p>

<h3 style="margin-left:0cm;">3.增加分区</h3>

<p style="margin-left:0cm;">Kafka自带的kafka-topics.sh脚本可以很方便的对某个主题的分区数进行修改；</p>

<h3 style="margin-left:0cm;">4.增加副本</h3>

<p style="margin-left:0cm;">增加副本的操作是分区迁移的一个特例，本质上也是分区副本的重分配操作；</p>

<h2 style="margin-left:0cm;">7.连接器基本操作</h2>

<p style="margin-left:0cm;">Kafka自带了对连接器应用的脚本，用于将数据从外部系统导入到Kafka或者从Kafka中导出到外部系统。</p>

<p style="margin-left:0cm;">Kafka连接器有两种模式：</p>

<ol><li>独立模式（standalone）：</li>
</ol><p style="margin-left:0cm;">connect-standalone.sh</p>

<p>      2.分布式模式（distributed）：</p>

<p style="margin-left:0cm;">connect-distributed.sh</p>

<p style="margin-left:0cm;"> </p>

<h3 style="margin-left:0cm;">1.独立模式</h3>

<p style="margin-left:0cm;">Kafka自带脚本connect-standlone.sh用以独立模式启动Kafka连接器。</p>

<p style="margin-left:0cm;">执行该脚本需要指定两个配置文件，一个是worker运行时候相关配置的配置文件，称为WorkConfig，在该文件中指定与Kafka建立连接的配置bootstrap.servers，数据格式转换类key.converter/value.converter、保存偏移量的文件路径（offset.storage.file.filename）、提交偏移量的频率（offset.flush.interval.ms）等</p>

<p style="margin-left:0cm;">另外需要指定是导入数据连接器或者是导出数据连接器</p>

<p style="margin-left:0cm;">【将数据从外部导入到Kafka相应主题的数据连接器：Source连接器】</p>

<p style="margin-left:0cm;">Kafka自带的connect-file-source.properties文件配置了一个读取文件的Source连接器</p>

<p style="margin-left:0cm;">【将数据从Kafka导出到系统外部的数据连接器：Sink连接器】</p>

<p style="margin-left:0cm;">Kafka自带脚本connect-console-sink.properties配置了有一个将Kafka中的数据导出到文件的Sink连接器。</p>

<h3 style="margin-left:0cm;">2.REST风格API应用</h3>

<p style="margin-left:0cm;">Kafka提供了一套基于REST风格API接口来管理连接器，默认端口为8083，也可以在启动Kafka连接器前在WorkConfig配置文件中通过rest.port配置端口</p>

<h3 style="margin-left:0cm;">3.分布式模式</h3>

<p style="margin-left:0cm;">Kafka自带的connect-distributed.sh脚本用以分布式模式运行连接器，执行该脚本需要指定一个WorkConfig类型的配置文件，但是以分布式模式启动连接器并不支持在启动的时候通过加载连接器配置文件创建一个连接器，而只能通过访问REST风格接口创建连接器；</p>

<h2 style="margin-left:0cm;">8. KafKa Manager应用</h2>

<p style="margin-left:0cm;">Kafka Manager提供了对Kafka集群进行管理和监控的可视化Web界面，通过Web界面能够方便的对主题进行管理，包括创建主题、删除主题、查询集群的主题、增加分区、分区副本重分配、选择优先副本为Leader，修改主题级别配置。同时还可以监控分区的AR和ISR等信息，代理以及消费者运行情况等</p>

<h2 style="margin-left:0cm;">9. KafKa 安全机制</h2>

<p style="margin-left:0cm;">0.9版本之后，Kafka增加了身份认证和权限控制两种安全机制；</p>

<ol><li>身份认证：</li>
</ol><p style="margin-left:0cm;">指客户端与服务端连接进行身份认证，包括客户端与kafka代理之间的连接认证、代理之间的连接认证、代理与ZooKeeper之间的连接认证。目前支持SSL、SASL/Kerberos、SASL/PLAIN这三种认证机制；</p>

<p>      2.权限控制：</p>

<p style="margin-left:0cm;">权限控制是指对客户端的读写操作进行权限控制，包括对于消息或者Kafka集群操作权限控制。权限控制是可插拔的，并且支持与外部的授权服务进行集成；</p>

<h3 style="margin-left:0cm;">1.利用SASL/PLAIN进行身份认证</h3>

<h3 style="margin-left:0cm;">2.权限控制</h3>

<p style="margin-left:0cm;">kafk-acls.sh脚本支持查询（list）、添加（add）、移除（remove）这三类权限控制的操作。要启用Kafka ACL权限控制，首先需要在server.properties文件中增加权限控制实现类的设置；</p>

<h2 style="margin-left:0cm;">10.镜像操作</h2>

<p style="margin-left:0cm;">Kafka提供了一个镜像操作的工具kafka-mirror-marker.sh，用于将一个集群的数据同步到另外一个集群。通过这个工具可以方便的实现两个集群之间的数据迁移；</p>

<p style="margin-left:0cm;">Kafka镜像工具的本质是创建一个消费者，从源集群中待迁移的主题消费数据，然后创建一个生产者，将消费者从源集群中拉取的数据写入目标集群。</p>

<p style="margin-left:0cm;">【注意】：</p>

<p style="margin-left:0cm;">由于镜像操作的命令是启动一个生产者和一个消费者进行数据镜像操作，因此数据同步完成之后，该命令依然在等待新的数据进行同步，也就是需要客户端自己查看数据是否已经同步完成，在保证数据同步完成之后需要手动关闭该命令。同时客户端可以在目标集群中创建主题，主题的分区以及副本数可以与源集群中该主题对应的分区以及副本数不一致。</p>

<p style="margin-left:0cm;">如果希望镜像操作启动的生产者在写入消息的时候创建主题则需要保证目标集群已经设置auto.create.topics.enable=true</p>            </div>
                </div>