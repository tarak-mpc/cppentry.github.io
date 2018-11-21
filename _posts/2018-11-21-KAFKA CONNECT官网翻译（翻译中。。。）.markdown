---
layout:     post
title:      KAFKA CONNECT官网翻译（翻译中。。。）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h1><strong>8. KAFKA CONNECT</strong></h1>

<h2><strong>8.1 概述</strong></h2>

<p>Kafka Connect是用于连接Kafka和其他系统的工具，它具有很强的可靠性可扩展性。它可以很轻松的定义连接器，使之可以将大量数据输入或输出Kafka。Kafka Connect可以将你的程序的数据库实体或指标导入Kafka主题中，使数据可以以低延迟的流方式处理。输出的工作可以将Kafka主题的数据分发到次级存储和查询系统中，或者用作离线分析的一批系统中。</p>

<p>Kafka Connect具有以下特点：<br><strong>Kafka连接器的通用框架</strong> - Kafka Connect标准化了Kafka与其他数据系统的整合方式，简化连接器开发、部署和管理。<br><strong>分布式和单机模式</strong> - 可以扩展成为一个巨大的中央管理服务支持整个组织，也可以缩小到开发、测试和小型产品部署。<br><strong>REST接口</strong> - 通过简单的REST API提交和管理Kafka Connect集群的连接器。<br><strong>自动化偏移量管理</strong> - 通过少量连接器的信息，Kafka Connect可以自动化管理偏移量的提交，因此连接器开发者不需要担心这些容易出错的连接器开发问题。<br><strong>流/批量集成</strong> - 利用Kafka已有的能力，Kafka Connect是一种连接流和批量数据系统的理想方案</p>

<h1><strong>8.2 用户指南</strong></h1>

<p>快速入门介绍了一个简单的例子，如何运行单机版Kafka Connect。这一节将讲述更多关于配置、运行和管理Kafka<br>
Connect的细节。</p>

<h3><strong>运行Kafka Connect</strong></h3>

<p>Kafka Connect目前支持两种运行模式：单机（单进程）和分布式。</p>

<p>在单机模式中，所有工作都在一个进程中。这种配置易于安装和启动，适用于只有单个worker的情况。但这种方式发<br>
挥不了Kafka的一些特点，例如容错性。可以通过以下命令启动单机模式进程：</p>

<p>bin/connect-standalone.sh config/connect-standalone.properties connector1.properties [connector2.properties ...]</p>

<p>第一个参数是worker的配置。其中包括了设置Kafka连接参数、序列化格式、提交偏移量的频率等。自带的例子可以在<br>
配置文件为config/server.properties的本地集群中运行。对不同的配置和产品部署只需要少量调整。所有worker（包括单机和分布式）都必须配置这些信息：</p>

<p><strong>bootstrap.servers</strong> - 用于引导Kafka连接的Kafka服务器列表<br><strong>key.converter</strong> - 转换类用于转换从Kafka Connect到Kafka的序列化格式。它控制读写Kafka消息中的键的格式。由于与连接器无关，因此连接器都可以和任意序列化格式工作。常用的格式包括JSON和Avro。</p>

<p>单机模式中重要的配置选项：<br><strong>offset.storage.file.filename</strong> - 存储偏移量的文件。</p>

<p>这些参数的配置是给Kafka Connect的生产者和消费者用来访问配置、偏移量和主题状态的。当配置Kafka source和Kafka sink task，可以使用同样的参数，但要分别加上consumer. 和 producer. 前缀。唯一继承worker配置的参数是bootstrap.servers。值得注意的例外是安全集群，它需要额外的参数来允许连接。这些参数需要在worker配置中设置三次，一次用于管理访问，一次用于Kafka sink，一次用于Kafka source。</p>

<p>其余的参数是连接器配置文件。可以有多个，它们都会在同一进程不同线程中执行。</p>

<p>分布式模式处理负载均衡的工作，允许你动态扩容缩容，并且为活动任务、配置和偏移量提交提供容错机制。执行的方式与单机模式类似：</p>

<p>bin/connect-distributed.sh config/connect-distributed.properties</p>

<p>不同之处在于启动执行的脚本和配置参数。配置参数决定配置数据存放的位置，如何协同工作，偏移量存放的位置和任务状态。在分布式模式中，Kafka Connect把偏移量、配置和任务状态存放在Kafka主题中。推荐为偏移量、配置和任务状态手动创建主题，设置所需的分区数量和复制因子。如果Kafka Connect启动前没创建主题，则会根据默认的分区数和复制因子自动创建主题，但这个队列可能不是最合适。</p>

<p>一般来说，以下配置，以及上述的提到的一般设置，最好在启动集群前配好：<br>
group.id（默认为connect-cluster） - 集群的唯一名称。用于表明属于哪个Kafka Connect集群组。需要注意的是不要与消费者组ID冲突。<br>
config.storage.topic（默认connect-configs） - 用于存储连接器和任务配置的主题。注意这个队列应该是单分区、highly replicated、compacted的队列。你可以手动穿件队列确保配置正确。而自动创建的主题可能是多分区，并且自动配置为deletion而不是compaction。（PS：不知道怎么翻译……）<br>
offset.storage.topic（默认connect-offsets） - 用于存储偏移量的主题。这个队列应该包含多个分区，多复制，并配置适用于compaction。<br>
status.storage.topic（默认connect-status） - 用于存储状态的主题。该主题应该是多个分区，多复制，并配置适用于compaction。</p>

<p>注意在分布式模式下连接器配置不是通过命令行传递，而是通过REST API创建、修改和删除。</p>

<p> </p>

<h3><strong>配置连接器</strong></h3>

<p>连接器配置通过简单的key-value形式。在单机模式中这些参数配置在属性文件里，通过命令行传递给Kafka Connect进程。在分布式模式中，它们会包含在JSON里，通过请求创建或修改连接器</p>

<p>大部分配置与连接器相关，所以这里无法全部概述，以下是一些常用的配置：<br>
name - 连接器的独一无二名称。<br>
connector.class - 连接器的JAVA类。<br>
task.max - 该连接器需要创建的最大任务数。连接器可能会创建少于并行所需的任务数。<br>
key.converter - （可选）worker会覆盖默认的键转换集。<br>
value.converter - （可选）worker会覆盖默认的值转换集。</p>

<p>connector.class可以支持几种格式：该连接器的类的全名或别名。如果连接器是org.apache.kafka.connect.file.FileStreamSinkConnector，你可以指定全名，也可以用FileStreamSink或FileStreamSinkConnector使配置简短一些。</p>

<p>出口连接器还有一些额外的选项控制输入。每个出口连接器必须设置以下其中一个配置：<br>
topics - 通过逗号分隔的主题列表，表示该连接器的输入。<br>
topics.regex - JAVA正则表达式的形式，表示该连接器的输入。</p>

<p>还有一些其他选项，可以查询连接器的文档</p>

<p> </p>

<h3>转换器</h3>

<p>连接器可以配置轻量级的一次性转换的转换器，使数据消息和事件路由变得更友好。</p>

<p>一个转换器链可以在连接器配置中指定。</p>

<p>transforms - 转换器的别名列表，可以指定转换器的顺序。<br>
transforms.$alias.type - 转换器的全名<br>
transforms.$alias.$transformationSpecificConfig - 配置转换器的属性</p>

<p>举个例子，使用内置文件输入连接器和一个转换器增加一个静态字段。</p>

<p>在这个例子中，我们会使用无模式的JSON数据格式。要使用无模式格式，需要改变connect-standalone.properties中的两行：<br>
key.converter.schemas.enable<br>
value.converter.schemas.enable</p>

<p>文件输入连接器已字符串方式读取每一行。我们把每一行封装到一个Map里面，然后增加第二个字段用于识别时间的起源。为此我们使用两个转换器：</p>

<p>HoistField 用于把输入行放入Map。<br>
InsertField用于添加到静态字段中。在这个例子中我们假设数据来自一个文件连接器。</p>

<p>添加转换器后，connect-file-source.properties文件大概如下：</p>

<p>name=local-file-source<br>
connector.class=FileStreamSource<br>
tasks.max=1<br>
file=test.txt<br>
topic=connect-test<br>
transforms=MakeMap, InsertSource<br>
transforms.MakeMap.type=org.apache.kafka.connect.transforms.HoistField$Value<br>
transforms.MakeMap.field=line<br>
transforms.InsertSource.type=org.apache.kafka.connect.transforms.InsertField$Value<br>
transforms.InsertSource.static.field=data_source<br>
transforms.InsertSource.static.value=test-file-source</p>

<p>每行以transforms开头的是转换器的配置。可以看到创建了两个转换器：“InsertSource”和“MakeMap”这两个别名。转换器的类型基于内置转换器。每个转换器类型都有额外的配置：HoistField需要一个“field”的配置，作为map的键。InsertField转换器可以指定field名和值</p>

<p>当不使用转换器运行例子的时候，通过kafka-console-consumer.sh读取消息，结果是：<br>
"foo"<br>
"bar"<br>
"hello world"</p>

<p>当我们把转换器加入配置文件时，这次读取的结果是：<br>
{"line":"foo","data_source":"test-file-source"}<br>
{"line":"bar","data_source":"test-file-source"}<br>
{"line":"hello world","data_source":"test-file-source"}</p>

<p>可以看到我们读取的行是一个JSON map，另外还有我们指定的额外字段。这只是展示转换器可以做些什么的一个例子。</p>

<p>一些广泛应用的数据和路由转换器包含在Kafka Connect中：<br>
InsertField - 增加一个使用静态数据或记录元数据的字段。<br>
ReplaceField - 过滤或重命名字段。<br>
MaskField - 替换字段的有效null值（0，空字符串等）。<br>
ValueToKey<br>
HoistField - 把整个事件封装到一个单独的Map或结构体里<br>
ExtractField - 从结构体或Map或只包含该字段的结果集中，提取一个指定的字段<br>
SetSchemaMetadata - 改变模式名称或版本<br>
TimestampRouter - 改变基于原生主题或时间戳的记录的主题。当需要基于时间戳写不同表或索引时很有用<br>
RegexRouter - 改变基于原生主题、替换字符串和正则表达式的记录的主题</p>

<h3>REST API</h3>

<p>由于Kafka Connect更适合作为一个服务，因此它提供了REST API来管理连接器。REST API服务器可以通过listeners参数进行配置。这项参数应该包含监听器的列表，格式如：protocol://host:port,protocol2://host2:port2。目前支持的协议有http和https。例如：</p>

<p>listeners=http://localhost:8080,https://localhost:8443</p>

<p>默认情况下，如果没有指定listeners，REST服务器会通过HTTP协议运行于8083端口。当使用HTTPS时，必须包含SSL配置。默认情况下，会使用ssl.*设定。如果需要为REST API使用不同的配置而不是连接到Kafka broker，则这个字段可以以listeners.https为前缀。当使用前缀时，只有添加了前缀的选项会生效，而没有前缀的ssl.*选项会被忽略。以下字段可以用来配置REST API的HTTPS：</p>

<p>ssl.keystore.location<br>
ssl.keystore.password<br>
ssl.keystore.type<br>
ssl.key.password<br>
ssl.truststore.location<br>
ssl.truststore.password<br>
ssl.truststore.type<br>
ssl.enabled.protocols<br>
ssl.provider<br>
ssl.protocol<br>
ssl.cipher.suites<br>
ssl.keymanager.algorithm<br>
ssl.secure.random.implementation<br>
ssl.trustmanager.algorithm<br>
ssl.endpoint.identification.algorithm<br>
ssl.client.auth</p>

<p>REST API不单提供给用户监督和管理Kafka Connect，也可以用于Kafka Connect跨集群通信。从节点REST API收到请求后，会被转发到主节点REST API。如果给定主机可访问的URI与它侦听的URI不同，则配置选项rest.advertised.host.name，rest.advertised.port和rest.advertised.listener，可以改变从节点连接主节点使用的URI。当同时使用HTTP和HTTPS监听器时，rest.advertised.listener选项也可以用来设定跨集群通信的监听者。当使用HTTPS作为节点的通信方式时，ssl.*或listerner.https选项同样用于设定HTTPS客户端</p>

<p>以下是目前支持的REST API端点：</p>

<p>GET /connectors - 返回活动连接器列表<br>
POST /connectors - 创建一个新的连接器；请求体部分应为JSON对象，包含名为name的字符型字段和名为config的对象型字段作为连接器配置参数。<br>
GET /connectors/{name} - 获取指定连接器信息<br>
GET /connectors/{name}/config - 获取指定连接器配置参数<br>
PUT /connectors/{name}/config - 更新指定连接器配置参数<br>
GET /connectors/{name}/status - 获取当前连接器状态，包括它是否在运行，或者失败等，分配给那个worker，错误信息（如果失败）和所有任务的状态<br>
GET /connectors/{name}/tasks - 获取连接器的任务列表<br>
GET /connectors/{name}/tasks/{taskid}/status - 获取当前任务状态，包括它是否在运行，或者失败等，分配给那个worker，错误信息（如果失败）和所有任务的状态<br>
PUT /connectors/{name}/pause - 暂停连接器和它的任务，在连接器再开始前停止处理消息<br>
PUT /connectors/{name}/resume - 重开始一个暂停的连接器（如果连接器没暂停则什么也不做）<br>
POST /connectors/{name}/restart - 重启一个连接器（一般来说是由于它失败了）<br>
POST /connectors/{name}/tasks/{taskId}/restart - 重启一个单独的任务（一般是由于它失败了）<br>
DELETE /connectors/{name} - 删除一个连接器，停止所有任务并删除它的配置</p>

<p>Kafka Connect同样提供REST API来获取连接器插件信息：<br>
GET /connector-plugins - 返回一个安装在Kafka Connect集群里的插件列表。请注意，API仅检查处理请求的worker上的连接器，这意味着您可能会看到不一致的结果，尤其是在滚动升级期间，如果添加新的连接器jar<br>
PUT /connector-plugins/{connector-type}/config/validate - 根据配置定义验证提供的配置值。 此API执行每个配置验证，在验证期间返回建议值和错误消息。</p>

<p> </p>

<h2>8.3 连接器开发指南</h2>

<p>这份指南将告诉程序员如何编写一个Kafka Connect的连接器，用于Kafka和其他系统之间交互数据。首先会简单回顾一些关键概念，然后讲一下如何创建一个简单的连接器</p>

<h3>核心概念和API</h3>

<p><strong>连接器和任务</strong></p>

<p>为了在Kafka和其他系统之间复制数据，用户为需要取数或推送数据的系统创建连接器。连接器有两种原型：SourceConnectors从别的系统导入数据（例如JDBCSourceConnector可以吧关系型数据库的数据导入Kafka）和SinkConnectors输出数据（例如HDFSConnector会吧数据从Kafka主题的内容输出到一个HDFS文件）。</p>

<p>连接器不会自行复制任何数据：他们的配置描述了被复制的数据，连接器负责把作业分解成一组Task，分配给workers。这些Task也分为两种对应的原型：SourceTask和SinkTask。</p>

<p>通过配置，每个Task必须将其数据子集复制到Kafka或从Kafka复制。在Kafka Connect里，始终可以将这些分配框架化为一组输入和输出流，这些流由具有一致模式的记录组成。有时这种映射是显而易见的：一组日志文件中的每个文件都可以被视为一个流，每个解析的行使用相同的模式形成记录，并且偏移存储为文件中的字节偏移。在其他情况下，映射到此模型可能需要更多工作：JDBC连接器可以将每个表映射到流，但偏移量不太清楚。一种可能的映射使用时间戳列来生成以递增方式返回新数据的查询，并且最后查询的时间戳可以用作偏移量。</p>            </div>
                </div>