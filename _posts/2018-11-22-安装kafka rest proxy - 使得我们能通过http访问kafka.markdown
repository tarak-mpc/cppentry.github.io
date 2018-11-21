---
layout:     post
title:      安装kafka rest proxy - 使得我们能通过http访问kafka
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>安装kafka rest proxy - 使得我们能通过http访问kafka，这对于那些没用对应kafka客户端的语言尤其有用，比如php。</p>
<p><br></p>
<p><span style="color:rgb(64,64,64);font-family:Lato, 'proxima-nova', 'Helvetica Neue', Arial, sans-serif;font-size:16px;line-height:24px;background-color:rgb(252,252,252);">The Kafka REST Proxy provides a RESTful interface to a Kafka cluster. It makes it easy
 to produce and consume messages, view the state of the cluster, and perform administrative actions without using the native Kafka protocol or clients. Examples of use cases include reporting data to Kafka from any frontend app built in any language, ingesting
 messages into a stream processing framework that doesn’t yet support Kafka, and scripting administrative actions.</span><br></p>
<p><br></p>
<h1>对应kafka0.9版本</h1>
<h2>安装方法1：</h2>
<div>wget http://packages.confluent.io/archive/2.0/confluent-2.0.1-2.11.7.zip<br></div>
<div>unzip confluent-2.0.1-2.11.7.zip<br>
cd confluent-2.0.1<br></div>
<div><br></div>
<div>vi etc/schema-registry/schema-registry.properties 修改其中的kafka的zookeeper路径（同kafka配置文件中的zookeeper路径）  可以用port设置端口</div>
<div><span></span>port=8081 schema-registry的端口，后面会用到<br><span></span>kafkastore.connection.url=localhost:2181/kafka   kafka的zookeeper路径（同kafka配置文件中的zookeeper路径，包括/chroot）<br><span></span>kafkastore.topic=_schemas   使用默认即可<br><span></span>debug=false<br></div>
<div><br></div>
<div><br></div>
<div><br></div>
<div><br>
./bin/schema-registry-start etc/schema-registry/schema-registry.properties<span></span>启动注册节点<br><br><br>
vi etc/kafka-rest/kafka-rest.properties 可以用port设置端口</div>
<div><span></span>port=8082 rest端口，通过此端口访问kafka的功能，如发送消息等<br></div>
<div><span></span>id=kafka-rest-test-server<br><span></span>schema.registry.url=http://localhost:8081<span></span>  schema-registry的ip:端口<br><span></span>zookeeper.connect=localhost:2181/kafka<span></span>  kafka的zookeeper路径（同kafka配置文件中的zookeeper路径，包括/chroot）</div>
<div><br></div>
<div><br></div>
<div><br></div>
<div><br>
./bin/kafka-rest-start etc/kafka-rest/kafka-rest.properties<span></span>启动rest服务器<br></div>
<div><br></div>
<div><br></div>
<h2>安装方法2</h2>
<div>配置文件修改参看安装方法1</div>
<div><br></div>
<div>rpm --import http://packages.confluent.io/rpm/2.0/archive.key<br>
vi /etc/yum.repos.d/confluent2.0.repo</div>
<div><span>[confluent-2.0]<br><span></span>name=Confluent repository for 2.0.x packages<br><span></span>baseurl=http://packages.confluent.io/rpm/2.0<br><span></span>gpgcheck=1<br><span></span>gpgkey=http://packages.confluent.io/rpm/2.0/archive.key<br><span></span>enabled=1</span></div>
<div><span><br></span></div>
<div><span><br></span></div>
<div><span><br></span></div>
<div><span><br></span>yum install confluent-common-2.0.1<br>
yum install confluent-rest-utils-2.0.1<br>
yum install confluent-kafka-rest-2.0.1<br>
yum install confluent-schema-registry-2.0.1</div>
<div>vi /etc/schema-registry/schema-registry.properties</div>
<div>vi /etc/kafka-rest/kafka-rest.properties<br>
schema-registry-start /etc/schema-registry/schema-registry.properties<br>
kafka-rest-start /etc/kafka-rest/kafka-rest.properties<br></div>
<div><br></div>
<div><br></div>
<div><br></div>
<div><br></div>
<div><br></div>
<div>
<h1>对应kafka0.8版本</h1>
<h2>安装方法1：</h2>
<div>wget http://packages.confluent.io/archive/1.0/confluent-1.0.1-2.10.4.zip<br></div>
<div>unzip confluent-1.0.1-2.10.4.zip<br>
cd confluent-1.0.1<br></div>
<div><br></div>
<div>vi etc/schema-registry/schema-registry.properties 修改其中的kafka的zookeeper路径（同kafka配置文件中的zookeeper路径）  可以用port设置端口</div>
<div><span></span>port=8081 schema-registry的端口，后面会用到<br><span></span>kafkastore.connection.url=localhost:2181/kafka   kafka的zookeeper路径（同kafka配置文件中的zookeeper路径，包括/chroot）<br><span></span>kafkastore.topic=_schemas   使用默认即可<br><span></span>debug=false<br></div>
<div><br></div>
<div><br></div>
<div><br></div>
<div><br></div>
<div><br>
./bin/schema-registry-start etc/schema-registry/schema-registry.properties<span></span>启动注册节点<br><br><br>
vi etc/kafka-rest/kafka-rest.properties 可以用port设置端口</div>
<div><span></span>port=8082 rest端口，通过此端口访问kafka的功能，如发送消息等<br></div>
<div><span></span>id=kafka-rest-test-server<br><span></span>schema.registry.url=http://localhost:8081<span></span>  schema-registry的ip:端口<br><span></span>zookeeper.connect=localhost:2181/kafka<span></span>  kafka的zookeeper路径（同kafka配置文件中的zookeeper路径，包括/chroot）</div>
<div><br></div>
<div><br></div>
<div><br></div>
<div><br></div>
<div><br>
./bin/kafka-rest-start etc/kafka-rest/kafka-rest.properties<span></span>启动rest服务器<br></div>
<div><br></div>
<div><br></div>
<div><br></div>
<div><br></div>
<div><br></div>
<div><br></div>
<h2>安装方法2</h2>
<div>配置文件修改参看安装方法1</div>
<div><br></div>
<div>rpm --import http://packages.confluent.io/rpm/1.0/archive.key<br>
vi /etc/yum.repos.d/confluent1.0.repo</div>
<div><span><span></span>[confluent-1.0]<br><span></span>name=Confluent repository for 1.0.x packages<br><span></span>baseurl=http://packages.confluent.io/rpm/1.0<br><span></span>gpgcheck=1<br><span></span>gpgkey=http://packages.confluent.io/rpm/1.0/archive.key<br><span></span>enabled=1</span></div>
<div><span><br></span></div>
<div><span><br></span></div>
<div><span><br></span></div>
<div><span><br></span></div>
<div><span><br></span></div>
<div><span><br></span>yum install confluent-common-1.0.1<br>
yum install confluent-rest-utils-1.0.1<br>
yum install confluent-kafka-rest-1.0.1<br>
yum install confluent-schema-registry-1.0.1</div>
<div>vi /etc/schema-registry/schema-registry.properties</div>
<div>vi /etc/kafka-rest/kafka-rest.properties<br>
schema-registry-start /etc/schema-registry/schema-registry.properties<br>
kafka-rest-start /etc/kafka-rest/kafka-rest.properties<br></div>
<div><br></div>
<br></div>
<div><br></div>
<div><br></div>
<h1>调用kafka功能</h1>
<div>对于kafka 0.8，可参看文档：http://docs.confluent.io/1.0.1/kafka-rest/docs/intro.html</div>
<div>对于kafka 0.9，可参看文档：http://docs.confluent.io/2.0.0/kafka-rest/docs/intro.html<br></div>
<div><br></div>
<div><br></div>
<div>POST http://10.255.xx.xx:8082/topics/test HTTP/1.1<br>
Host: 10.255.xx.xx:8082<br>
Content-Type: application/vnd.kafka.binary.v1+json<br>
Content-Length: 34<br><br><br>
{"records":[{"value":"5Lit5paHIGhlbGxvIHdvcmQ="}]}<br></div>
<div><br></div>
<div>此方法将发送消息到kafka的名字叫“test”的topic。</div>
<div>发送的消息内容用Base64编码，放到json（格式为“{"records":[{"value":"消息内容的Base64编码后的字符串"}]}”）中的value字段</div>
<div><br></div>
<div><br></div>
<div><br></div>
<div><br></div>
<div><br></div>
<div><br></div>
            </div>
                </div>