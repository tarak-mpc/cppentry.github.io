---
layout:     post
title:      使用confluent本地安装和使用kafka
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/q383965374/article/details/83419245				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>转载请注明出处:<a href="http://www.525.life/article?id=1510739742415" rel="nofollow" title="使用confluent本地安装和使用kafka">使用confluent本地安装和使用kafka</a></p>
<h1><a id="confluent_4"></a>confluent简介</h1>
<p>confluent是平台化的工具，封装了kafka，让我们可以更方便的安装和使用监控kafka，作用类似于CDH对于Hadoop。</p>
<p>confluent是由LinkedIn开发出Apache Kafka的团队成员，基于这项技术创立了新公司Confluent，Confluent的产品也是围绕着Kafka做的。基本架构如下：<br>
<img src="http://image.525.life/Fup15G4DXA2MDPyTIRixfjUiEn_0" alt=""></p>
<p>官网<br>
<a href="https://www.confluent.io" rel="nofollow">https://www.confluent.io</a></p>
<p>下载地址<br>
<a href="https://www.confluent.io/download/" rel="nofollow">https://www.confluent.io/download/</a></p>
<p>物理机安装参考<br>
<a href="https://docs.confluent.io/current/quickstart/cos-quickstart.html#cos-quickstart" rel="nofollow" title="Confluent Open Source Quick Start (Local)">Confluent Open Source Quick Start (Local)</a></p>
<p>docker安装参考<br>
<a href="https://docs.confluent.io/current/quickstart/cos-docker-quickstart.html#cos-docker-quickstart" rel="nofollow" title="Confluent Open Source Quick Start (Docker)">Confluent Open Source Quick Start (Docker)</a></p>
<p>对比之后感觉比原生的kafka安装简单很多，容器是docker容器的版本，对于我们在k8s中使用很方便。</p>
<h1><a id="Confluent_24"></a>Confluent的组件</h1>
<p>Confluent Platform 包括更多的工具和服务，使构建和管理数据流平台更加容易。<br>
Confluent Control Center（闭源）。管理和监控Kafka最全面的GUI驱动系统。<br>
Confluent Kafka Connectors（开源）。连接SQL数据库/Hadoop/Hive<br>
Confluent Kafka Clients（开源）。对于其他编程语言，包括C/C++,Python<br>
Confluent Kafka REST Proxy（开源）。允许一些系统通过HTTP和kafka之间发送和接收消息。<br>
Confluent Schema Registry（开源）。帮助确定每一个应用使用正确的schema当写数据或者读数据到kafka中。</p>
<h1><a id="Confluent_36"></a>Confluent的安装</h1>
<p>下载地址：<br>
<a href="http://www.confluent.io/download" rel="nofollow">http://www.confluent.io/download</a><br>
打开后，显示最新版本，在右边填写信息后，点击Download下载。</p>
<p>本次我们主要使用REST Proxy，当然底层的broker也是使用confluent的kafka组件，下面简述安装步骤：</p>
<p>下载confluent4.0.0</p>
<pre><code>wget http://packages.confluent.io/archive/4.0/confluent-oss-4.0.0-2.11.tar.gz
tar  xvf   confluent-oss-4.0.0-2.11.tar.gz
</code></pre>
<p>解压到指定目录下<br>
通过查看目录的内容，能够发现，confluent里面是含有kafka的，也就是说，如果你没有安装kafka，那么可以通过confluent直接对kafka进行安装。如果已经安装了kafka，可以使用confluent提供的插件。</p>
<p>转载请注明出处:<a href="http://www.525.life/article?id=1510739742415" rel="nofollow" title="使用confluent本地安装和使用kafka">使用confluent本地安装和使用kafka</a></p>
<h1><a id="_57"></a>自定义配置</h1>
<p>我们可以配置自己需要的和对应配置信息</p>
<p>进入解压出来的confluent-4.0.0</p>
<pre><code>cd confluent-4.0.0
</code></pre>
<p>配置zookeeper</p>
<pre><code>vi  etc/kafka/zookeeper.properties
</code></pre>
<p>内容如下：</p>
<pre><code>dataDir=/var/lib/zookeeper
clientPort=2181
maxClientCnxns=0
</code></pre>
<p>配置kafka的broker</p>
<pre><code>vi etc/kafka/server.properties
</code></pre>
<p>内容如下：</p>
<pre><code>broker.id=50
delete.topic.enable=true
listeners=PLAINTEXT://192.168.11.91:9092
num.network.threads=3
num.io.threads=8
socket.send.buffer.bytes=102400
socket.receive.buffer.bytes=102400
socket.request.max.bytes=104857600
log.dirs=/var/lib/kafka
num.partitions=1
num.recovery.threads.per.data.dir=1
log.retention.hours=168
log.segment.bytes=1073741824
log.retention.check.interval.ms=300000
zookeeper.connect=192.168.11.91:2181
zookeeper.connection.timeout.ms=6000
confluent.support.metrics.enable=true
confluent.support.customer.id=anonymous
</code></pre>
<p>配置rest proxy</p>
<pre><code>vi  etc/kafka-rest/kafka-rest.properties
</code></pre>
<p>内容如下：</p>
<pre><code>id=kafka-rest-server
#zookeeper.connect=192.168.11.91:2181
bootstrap.servers=PLAINTEXT://localhost:9092
</code></pre>
<p>配置schema registry</p>
<pre><code>vi  etc/schema-registry/schema-registry.properties
</code></pre>
<p>内容如下：</p>
<pre><code>listeners=http://0.0.0.0:8081
kafkastore.connection.url=192.168.11.91:2181
kafkastore.topic=_schemas
debug=false
</code></pre>
<h1><a id="_127"></a>启动服务</h1>
<p>启动kafka-rest</p>
<pre><code>bin/kafka-rest-start   etc/kafka-rest/kafka-rest.properties
</code></pre>
<p>上面的这种方式是前台启动，也可以以后台方式启动。</p>
<pre><code>nohup bin/kafka-rest-start   etc/kafka-rest/kafka-rest.properties &amp;
</code></pre>
<p>启动zookeeper</p>
<pre><code>bin/zookeeper-server-start -daemon etc/kafka/zookeeper.properties 
</code></pre>
<p>启动kafka broker</p>
<pre><code>bin/kafka-server-start -daemon  etc/kafka/server.properties 
</code></pre>
<p>启动schema registry</p>
<pre><code>bin/schema-registry-start -daemon  etc/schema-registry/schema-registry.properties 
</code></pre>
<h1><a id="_157"></a>测试使用</h1>
<p>查看topics<br>
浏览器访问或者curl都可以</p>
<pre><code>http://192.168.11.91:8082/topics
</code></pre>
<p>查看集群的brokers</p>
<pre><code>curl http://192.168.11.91:8082/brokers
</code></pre>
<p>注册consumer group</p>
<pre><code>curl -X POST -H "Content-Type: application/vnd.kafka.v2+json" -H "Accept: application/vnd.kafka.v2+json"   --data '{"name": "my_consumer_instance", "format": "json", "auto.offset.reset": "earliest"}'   http://localhost:8082/consumers/my_test_consumer
</code></pre>
<p>把topic和消费者my_consumer关联起来</p>
<pre><code>curl -X POST -H "Content-Type: application/vnd.kafka.v2+json" --data '{"topics":["bear"]}'   http://localhost:8082/consumers/my_test_consumer/instances/my_consumer_instance/subscription
</code></pre>
<p>通过rest接口向bear push数据</p>
<pre><code>curl -X POST -H "Content-Type: application/vnd.kafka.json.v2+json"           --data '{"records":[{"value":{"name": "testUser"}}]}'     "http://localhost:8082/topics/bear"
</code></pre>
<p>通过rest接口消费数据</p>
<pre><code>curl -X GET -H "Accept: application/vnd.kafka.json.v2+json"    http://localhost:8082/consumers/my_test_consumer/instances/my_consumer_instance/records
</code></pre>
<p>删除注册的consumer实例：</p>
<pre><code>curl -X DELETE -H "Accept: application/vnd.kafka.v2+json"     http://localhost:8082/consumers/my_test_consumer/instances/my_consumer_instance
</code></pre>
<p>转载请注明出处:<a href="http://www.525.life/article?id=1510739742415" rel="nofollow" title="使用confluent本地安装和使用kafka">使用confluent本地安装和使用kafka</a></p>
<p>更多信息参考<br>
<a href="https://github.com/confluentinc/kafka-rest" rel="nofollow">https://github.com/confluentinc/kafka-rest</a></p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>