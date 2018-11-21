---
layout:     post
title:      Logstash output Kafka with Kerberos学习
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：希望本文能对您有所帮助！欢迎指导、勘误、讨论及转载，转载请注明出处。					https://blog.csdn.net/Remoa_Dengqinyi/article/details/77870430				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p align="center"><strong><span style="font-family:'Times New Roman';font-size:14px;">Logstash output Kafka with Kerberos学习</span></strong></p>
<p><strong><span style="font-family:'Times New Roman';font-size:14px;">目录：</span></strong></p>
<p><strong><span style="font-family:'Times New Roman';font-size:14px;"><strong>1、Kafka中的一些术语：</strong><br></span></strong></p>
<p><strong><strong><span style="font-family:'Times New Roman';font-size:14px;"><strong>2、ELK流程图：</strong><br></span></strong></strong></p>
<p><strong><strong><strong><span style="font-family:'Times New Roman';font-size:14px;"><strong>3、Kafka发布、订阅信息的流程：</strong><br></span></strong></strong></strong></p>
<p><strong><span style="font-weight:bold;"><strong><strong><span style="font-family:'Times New Roman';font-size:14px;"><strong>4、通过Logstash收集日志到Kafka：</strong><br></span></strong></strong></span></strong></p>
<p><strong><strong><span style="font-family:'Times New Roman';font-size:14px;"><br></span></strong></strong></p>
<p><strong><span style="font-family:'Times New Roman';font-size:14px;">1、Kafka中的一些术语：</span></strong></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">（1）Topic：话题，Kafka将消息种子（Feed）进行分类，每一类的消息称为话题。</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">（2）Producer：生产者，发布消息的对象称为话题生产者。</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">（3）Consumer：消费者，订阅消息并处理发布的消息种子的对象称为话题消费者。</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">（4）Broker：服务器，已发布的消息保存在一组服务器中称之为Kafka集群。集群中的每一个服务器都是一个代理（Broker），一个Broker可以容纳多个Topic，消费者可以订阅一个或多个话题并从Broker拉数据从而消费这些已发布的消息。</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">（5）Partition：分区，每个Topic包含一个或多个Partition，Kafka分配的单位是Partition。</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;"> </span></p>
<p><strong><span style="font-family:'Times New Roman';font-size:14px;">2、ELK流程图：</span></strong></p>
<p style="text-align:center;"><span style="font-family:'Times New Roman';font-size:14px;"> <img src="https://img-blog.csdn.net/20170906193439075?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmVtb2FfRGVuZ3Fpbnlp/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></p>
<p style="text-align:center;"><span style="font-family:'Times New Roman';font-size:14px;">图2.1 ELK流程图</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">ELK日志分析系统的架构图的说明如下：</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">（1）Filebeat：</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">日志采集器，负责将数据tail到logstash；</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">（2）第一个Logstash：</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">data shipper，作为数据入kafka的适配器；</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">（3）Kafka：</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">消息队列，起消峰解耦的作用；</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">（4）Zookeeper：</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">分布式配置和同步服务，是Kafka代理和消费者之间的协调接口，Kafka服务器通过Zookeeper集群共享信息。</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">Kafka在Zookeeper中存储基本元数据，例如Topic，代理，消费者便宜等信息。</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">（5）第二个Logstash：</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">data indexer，负责数据解析和ElasticSearch入库；</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">（6）Elasticsearch：</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">存储，搜索和分析，用于存储所有的日志。</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">（7）Kerberos：一种认证机制，用于Kafka Broker服务器的认证。</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">查看ELK的中文文档：<a href="https://kibana.logstash.es/content/" rel="nofollow"><u><span style="color:rgb(0,0,255);">https://kibana.logstash.es/content/</span></u></a></span></p>
<p style="text-align:center;"><span style="font-family:'Times New Roman';font-size:14px;"> <img src="https://img-blog.csdn.net/20170906193547588?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmVtb2FfRGVuZ3Fpbnlp/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></p>
<p style="text-align:center;"><span style="font-family:'Times New Roman';font-size:14px;">图2.2 logstash架构图</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">Shipper：日志收集者，负责监控本地日志文件的变化，及时把日志文件的最新内容收集起来，输出到Redis暂存。</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">Indexer：日志存储者，负责从Redis接收日志，写入到Elasticsearch中。</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">Broker：日志中转站，用来连接多个Shipper和多个Indexer。</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;"> </span></p>
<p><strong><span style="font-family:'Times New Roman';font-size:14px;">3、Kafka发布、订阅信息的流程：</span></strong></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">①生产者定期向主题发送消息。</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">②Kafka代理存储为该特定主题配置的分区中的所有消息。 它确保消息在分区之间平等共享。 如果生产者发送两个消息并且有两个分区，Kafka将在第一分区中存储一个消息，在第二分区中存储第二消息。</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">③消费者订阅特定主题。</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">④一旦消费者订阅主题，Kafka将向消费者提供主题的当前偏移，并且还将偏移保存在Zookeeper系综中。</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">⑤消费者将定期请求Kafka(如100 Ms)新消息。</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">⑥一旦Kafka收到来自生产者的消息，它将这些消息转发给消费者。</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">⑦消费者将收到消息并进行处理。</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">⑧一旦消息被处理，消费者将向Kafka代理发送确认。</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">⑨一旦Kafka收到确认，它将偏移更改为新值，并在Zookeeper中更新它。 由于偏移在Zookeeper中维护，消费者可以正确地读取下一封邮件，即使在服务器处于峰值期间。</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">以上流程将重复，直到消费者停止请求。</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">消费者可以随时回退/跳到所需的主题偏移量，并阅读所有后续消息。</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;"> </span></p>
<p><strong><span style="font-family:'Times New Roman';font-size:14px;">4、通过Logstash收集日志到Kafka：</span></strong></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">（1）在/opt/package/kafka_2.10-0.10.1.0/config目录下查看kafka中producer.properties文件：</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">查看broker服务器位置。</span></p>
<p style="text-align:center;"><span style="font-family:'Times New Roman';font-size:14px;"> <img src="https://img-blog.csdn.net/20170906193647009?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmVtb2FfRGVuZ3Fpbnlp/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></p>
<p style="text-align:center;"><span style="font-family:'Times New Roman';font-size:14px;">图4.1 截图1</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">（2）在/tmp目录下查看kafka_jaas.conf文件：</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">查看serverName=”kafka”</span></p>
<p style="text-align:center;"><span style="font-family:'Times New Roman';font-size:14px;"> <img src="https://img-blog.csdn.net/20170906193705214?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmVtb2FfRGVuZ3Fpbnlp/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></p>
<p style="text-align:center;"><span style="font-family:'Times New Roman';font-size:14px;"><span style="text-align:center;">图4.2 截图2</span><br></span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">（3）因为logstash的版本较高，需要1.7版本以上的环境，因此运行logstash脚本前首先将jdk版本从1.7转换为1.8，否则会出现如下报错信息：</span></p>
<p></p><pre><code class="language-plain"><span style="font-family:'Times New Roman';font-size:14px;">LoadError: JRuby ext built for wrong Java version in `com.purbon.jrmonitor.JRMonitorService': java.lang.UnsupportedClassVersionError: com/purbon/jrmonitor/JRMonitorService : Unsupported major.minor version 52.0
  require at org/jruby/RubyKernel.java:1040
   (root) at /opt/package/logstash-5.2.2/vendor/bundle/jruby/1.9/gems/jrmonitor-0.4.2/lib/jrmonitor.rb:4
  require at org/jruby/RubyKernel.java:1040
   (root) at /opt/package/logstash-5.2.2/logstash-core/lib/logstash/instrument/periodic_poller/jvm.rb:1
  require at org/jruby/RubyKernel.java:1040
   (root) at /opt/package/logstash-5.2.2/logstash-core/lib/logstash/instrument/periodic_poller/jvm.rb:5
  require at org/jruby/RubyKernel.java:1040
   (root) at /opt/package/logstash-5.2.2/logstash-core/lib/logstash/instrument/periodic_pollers.rb:1
  require at org/jruby/RubyKernel.java:1040
   (root) at /opt/package/logstash-5.2.2/logstash-core/lib/logstash/instrument/periodic_pollers.rb:3
  require at org/jruby/RubyKernel.java:1040
   (root) at /opt/package/logstash-5.2.2/lib/bootstrap/environment.rb:70</span></code></pre>
<p><span style="font-family:'Times New Roman';font-size:14px;">①查看jdk当前版本：</span></p>
<p style="text-align:center;"><span style="font-family:'Times New Roman';font-size:14px;"> <img src="https://img-blog.csdn.net/20170906193718064?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmVtb2FfRGVuZ3Fpbnlp/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></p>
<p style="text-align:center;"><span style="font-family:'Times New Roman';font-size:14px;"><span style="text-align:center;">图4.3 截图3</span><br></span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">②新建shell：reload_path.sh</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">export JAVA_HOME=/opt/package/jdk1.8.0_112</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">export PATH=/opt/package/jdk1.8.0_112/bin:$PATH</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">③执行reload_path.sh</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">source reload_path.sh</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">④再查看jdk版本：</span></p>
<p style="text-align:center;"><span style="font-family:'Times New Roman';font-size:14px;"> <img src="https://img-blog.csdn.net/20170906193734125?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmVtb2FfRGVuZ3Fpbnlp/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></p>
<p style="text-align:center;"><span style="font-family:'Times New Roman';font-size:14px;"><span style="text-align:center;">图4.4 截图4</span><br></span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">（4）编写脚本remoatest1.sh：</span></p>
<p></p><pre><code class="language-plain"><span style="font-family:'Times New Roman';font-size:14px;">input { stdin { } }
output {
    stdout{codec =&gt; rubydebug}
    kafka{
                topic_id =&gt; "mytopic"
                bootstrap_servers =&gt; "hdp1.example.com:9092"
                security_protocol =&gt; "SASL_PLAINTEXT"
                sasl_kerberos_service_name =&gt; "kafka"
                jaas_path =&gt; "/tmp/kafka_jaas.conf.demouser"
                kerberos_config =&gt; "/etc/krb5.conf"
                compression_type =&gt; "none"
                acks =&gt; "1"
        }
}</span></code></pre>
<p><span style="font-family:'Times New Roman';font-size:14px;">Kafka output configuration Options的说明可以参考官网：</span></p>
<p><a href="https://www.elastic.co/guide/en/logstash/current/plugins-outputs-kafka.html" rel="nofollow"><u><span style="color:rgb(0,0,255);"><span style="font-family:'Times New Roman';font-size:14px;">https://www.elastic.co/guide/en/logstash/current/plugins-outputs-kafka.html</span></span></u></a></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">这里用到的参数说明：</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">①topic_id：必填项，指定消费话题，指定某个topic，实际上就是订阅某个主题，然后去消费。</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">②bootstrap_servers：用于建立与集群的初始连接的URL列表，即Kafka的接入地址（生产者）</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">③jass_path：Java Authentication and Authorization Service API（JAVA认证和授权服务）的缩写，为Kafka提供用户认证和授权服务，设置jass文件的路径。</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">④security_protocol：使用的安全协议，可以为plaintext、ssl、sasl_plaintext、sasl_ssl等等。</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">⑤kerberos_config：Keberos配置文件的路径。</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">⑥sasl_kerberos_service_name：Kafka服务器运行的Kerberos主题名称。其可以在Kafka的jaas配置中定义。</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">⑦codec：用于输入数据的编码/解码器。</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">⑧compression_type：压缩方式，默认是none，其它可选的是gzip和snappy</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">（5）运行脚本：</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">bash ../bin/logstash -f remoatest1.conf</span></p>
<p style="text-align:center;"><span style="font-family:'Times New Roman';font-size:14px;"> <img src="https://img-blog.csdn.net/20170906193745955?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmVtb2FfRGVuZ3Fpbnlp/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></p>
<p style="text-align:center;"><span style="font-family:'Times New Roman';font-size:14px;"><span style="text-align:center;">图4.5 截图5</span><br></span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">输入hello和remoa，生产者向主题mytopic发送消息成功。</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">（6）查看Kafka消费者是否接收到消息：</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">KAFKA_HEAP_OPTS="-Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/config/kafka_client_jaas.conf -Xmx512M"  /opt/package/kafka_2.10-0.10.1.0/bin/kafka-console-consumer.sh --topic mytopic
 --bootstrap-server hdp1.example.com:9092  --from-beginning --consumer.config /opt/package/kafka_2.10-0.10.1.0/config/consumer.properties</span></p>
<p style="text-align:center;"><span style="font-family:'Times New Roman';font-size:14px;"> <img src="https://img-blog.csdn.net/20170906193755345?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmVtb2FfRGVuZ3Fpbnlp/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></p>
<p style="text-align:center;"><span style="font-family:'Times New Roman';font-size:14px;"><span style="text-align:center;">图4.6 截图6</span><br></span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">查看到消费者接收到消息并进行处理。</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">（7）将一个日志文件信息通过logstash给Kafka：</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">输入指令：cat install.log.syslog | bash ../bin/logstash -f remoatest1.conf</span></p>
<p style="text-align:center;"><span style="font-family:'Times New Roman';font-size:14px;"> <img src="https://img-blog.csdn.net/20170906193805011?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmVtb2FfRGVuZ3Fpbnlp/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></p>
<p style="text-align:center;"><span style="text-align:center;"><span style="font-family:'Times New Roman';font-size:14px;">图4.7 截图7</span></span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">查看消费者接收处理结果如下：</span></p>
<p style="text-align:center;"><span style="font-family:'Times New Roman';font-size:14px;"> <img src="https://img-blog.csdn.net/20170906193824713?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmVtb2FfRGVuZ3Fpbnlp/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></p>
<p style="text-align:center;"><span style="text-align:center;"><span style="font-family:'Times New Roman';font-size:14px;">图4.8 截图8</span></span></p>
<p style="text-align:center;"><span style="text-align:center;"><br></span></p>
            </div>
                </div>