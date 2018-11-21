---
layout:     post
title:      Logstash from Kafka to Elasticsearch学习
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：希望本文能对您有所帮助！欢迎指导、勘误、讨论及转载，转载请注明出处。					https://blog.csdn.net/Remoa_Dengqinyi/article/details/77895931				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p align="center"><strong><span style="font-family:'Times New Roman';font-size:14px;">Logstash from Kafka to Elasticsearch学习</span></strong></p>
<p align="justify"><strong><span style="font-family:'Times New Roman';font-size:14px;">目录：</span></strong></p>
<p align="justify"><span style="font-family:'Times New Roman';font-size:14px;">1、<strong>Logstash input Kafka配置：</strong></span></p>
<p align="justify"><span style="font-family:'Times New Roman';font-size:14px;">2、<strong>Logstash output Elasticsearch：</strong></span></p>
<p align="justify"><span style="font-family:'Times New Roman';font-size:14px;">3、<strong>Logstash from Kafka to Elasticsearch：</strong></span></p>
<p align="justify"><span style="font-family:'Times New Roman';font-size:14px;"> </span></p>
<p align="justify"><strong><span style="font-family:'Times New Roman';font-size:14px;">1、Logstash input Kafka配置：</span></strong></p>
<p align="justify"><span style="font-family:'Times New Roman';font-size:14px;">（1）查看/opt/package/kafka_2.10-0.10.1.0/config中的server.properties，查看到生产者的地址：</span></p>
<p align="justify" style="text-align:center;"><span style="font-family:'Times New Roman';font-size:14px;"> <img src="https://img-blog.csdn.net/20170908161008584?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmVtb2FfRGVuZ3Fpbnlp/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></p>
<p align="justify" style="text-align:center;"><span style="font-family:'Times New Roman';font-size:14px;">图1.1 截图1</span></p>
<p align="justify"><span style="font-family:'Times New Roman';font-size:14px;">（2）配置remoatest3.conf的input：</span></p>
<p align="justify"></p><pre><code class="language-plain"><span style="font-family:'Times New Roman';font-size:14px;">input{
        kafka{
                bootstrap_servers =&gt; "hdp1.example.com:9092"
                security_protocol =&gt; "SASL_PLAINTEXT"
                sasl_kerberos_service_name =&gt; "kafka"
                jaas_path =&gt; "/tmp/kafka_jaas.conf.demouser"
                kerberos_config =&gt; "/etc/krb5.conf"
                topics =&gt; ["mytopic"]
        }
}</span></code></pre>
<p align="justify"><span style="font-family:'Times New Roman';font-size:14px;">配置参数参考官网地址：</span></p>
<p align="justify"><a href="https://www.elastic.co/guide/en/logstash/current/plugins-inputs-kafka.html" rel="nofollow"><u><span style="color:rgb(0,0,255);"><span style="font-family:'Times New Roman';font-size:14px;">https://www.elastic.co/guide/en/logstash/current/plugins-inputs-kafka.html</span></span></u></a></p>
<p align="justify"><span style="font-family:'Times New Roman';font-size:14px;">①bootstrap_servers：用于建立与集群的初始连接的URL列表，即Kafka的接入地址（生产者）。</span></p>
<p align="justify"><span style="font-family:'Times New Roman';font-size:14px;">②security_protocol：使用的安全协议，可以为plaintext、ssl、sasl_plaintext、sasl_ssl等等。</span></p>
<p align="justify"><span style="font-family:'Times New Roman';font-size:14px;">③sasl_kerberos_service_name：Kafka服务器运行的Kerberos主题名称。其可以在Kafka的jaas配置中定义。</span></p>
<p align="justify"><span style="font-family:'Times New Roman';font-size:14px;">④jaas_path：Java Authentication and Authorization Service API（JAVA认证和授权服务）的缩写，为Kafka提供用户认证和授权服务，设置jass文件的路径。</span></p>
<p align="justify"><span style="font-family:'Times New Roman';font-size:14px;">⑤kerberos_config：Keberos配置文件的路径。</span></p>
<p align="justify"><span style="font-family:'Times New Roman';font-size:14px;">⑥topics：要订阅的主题列表，默认为“logstash”。</span></p>
<p align="justify"><span style="font-family:'Times New Roman';font-size:14px;"> </span></p>
<p align="justify"><strong><span style="font-family:'Times New Roman';font-size:14px;">2、Logstash output Elasticsearch：</span></strong></p>
<p align="justify"><span style="font-family:'Times New Roman';font-size:14px;">（1）在/opt/package/elasticsearch-5.2.2/config目录下查看elasticsearch.yml文件中的host的ip地址及运行HTTP服务的端口，端口注释即使用默认端口9200。</span></p>
<p align="justify" style="text-align:center;"><span style="font-family:'Times New Roman';font-size:14px;"><img src="https://img-blog.csdn.net/20170908161120469?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmVtb2FfRGVuZ3Fpbnlp/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""> </span></p>
<p align="justify" style="text-align:center;"><span style="font-family:'Times New Roman';font-size:14px;"><span style="text-align:center;">图2.1 截图2</span><br></span></p>
<p align="justify"><span style="font-family:'Times New Roman';font-size:14px;">（2）查看keys文件位置：</span></p>
<p align="justify"><span style="font-family:'Times New Roman';font-size:14px;">这里采用了elasticsearch的一款插件search-guard，提供加密、身份验证和授权，基于search guard SSL，也可以提供可插入的身份验证/授权模块。其功能特性：</span></p>
<p align="justify"><span style="font-family:'Times New Roman';font-size:14px;">l 基于用户和角色的权限控制</span></p>
<p align="justify"><span style="font-family:'Times New Roman';font-size:14px;">l 支持SSL和TL方式安全认证</span></p>
<p align="justify"><span style="font-family:'Times New Roman';font-size:14px;">l 支持LDAP认证</span></p>
<p align="justify"><span style="font-family:'Times New Roman';font-size:14px;">Truststore和Keystore文件用来提供客户端与服务器之间的安全数据传输。</span></p>
<p align="justify" style="text-align:center;"><span style="font-family:'Times New Roman';font-size:14px;"> <img src="https://img-blog.csdn.net/20170908161138106?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmVtb2FfRGVuZ3Fpbnlp/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></p>
<p align="justify" style="text-align:center;"><span style="font-family:'Times New Roman';font-size:14px;"><span style="text-align:center;">图2.2 截图3</span><br></span></p>
<p align="justify"><span style="font-family:'Times New Roman';font-size:14px;">（3）配置remoatest3.conf的output：</span></p>
<p align="justify"></p><pre><code class="language-plain"><span style="font-family:'Times New Roman';font-size:14px;">output{
        stdout{
                codec =&gt; rubydebug
        }
        elasticsearch{
                hosts =&gt; ["kdc1.example.com:9200","kdc2.example.com:9200"]
                user =&gt; logstash
                password =&gt; logstash
                action =&gt; "index"
                index =&gt; "logstash-remoatest3-%{+YYYY.MM.dd}"
                truststore =&gt; "/opt/package/logstash-5.2.2/config/keys/truststore.jks"
                truststore_password =&gt; whoami
                ssl =&gt; true
                ssl_certificate_verification =&gt; true
                codec =&gt; "json"
        }
}</span></code></pre>
<p align="justify"><span style="font-family:'Times New Roman';font-size:14px;">配置参数参考官网地址：</span></p>
<p align="justify"><a href="https://www.elastic.co/guide/en/logstash/current/plugins-outputs-elasticsearch.html" rel="nofollow"><u><span style="color:rgb(0,0,255);"><span style="font-family:'Times New Roman';font-size:14px;">https://www.elastic.co/guide/en/logstash/current/plugins-outputs-elasticsearch.html</span></span></u></a></p>
<p align="justify"><span style="font-family:'Times New Roman';font-size:14px;">①host：elasticsearch集群中任意节点的主机名</span></p>
<p align="justify"><span style="font-family:'Times New Roman';font-size:14px;">②user：连接elasticsearch集群的安全认证的用户名</span></p>
<p align="justify"><span style="font-family:'Times New Roman';font-size:14px;">③password：连接elasticsearch集群的安全认证的密码</span></p>
<p align="justify"><span style="font-family:'Times New Roman';font-size:14px;">④action：默认值是index；</span></p>
<p align="justify"><span style="font-family:'Times New Roman';font-size:14px;">index是来自logstash的事件，索引文档；</span></p>
<p align="justify"><span style="font-family:'Times New Roman';font-size:14px;">delete是按id删除文档（此操作需要一个id）；</span></p>
<p align="justify"><span style="font-family:'Times New Roman';font-size:14px;">create是索引文档，如果索引中已存在该id的文档，则失败；</span></p>
<p align="justify"><span style="font-family:'Times New Roman';font-size:14px;">update是按id更新文档。</span></p>
<p align="justify"><span style="font-family:'Times New Roman';font-size:14px;">⑤index：将事件写入的索引名字。可以使用%{}进行动态化。</span></p>
<p align="justify"><span style="font-family:'Times New Roman';font-size:14px;">⑥truststore：JKS（JAVA Keytool Keystore）信任库验证服务器的证书。</span></p>
<p align="justify"><span style="font-family:'Times New Roman';font-size:14px;">⑦truststore_password：证书密码</span></p>
<p align="justify"><span style="font-family:'Times New Roman';font-size:14px;">⑧ssl：启用与Elasticsearch集群的SSL / TLS安全通信。</span></p>
<p align="justify"><span style="font-family:'Times New Roman';font-size:14px;">⑨ssl_certificate_verification：验证服务器证书的选项</span></p>
<p align="justify"><span style="font-family:'Times New Roman';font-size:14px;">⑩codec：用于数据的编码/解码器。</span></p>
<p align="justify"><span style="font-family:'Times New Roman';font-size:14px;"> </span></p>
<p align="justify"><strong><span style="font-family:'Times New Roman';font-size:14px;">3、Logstash from Kafka to Elasticsearch：</span></strong></p>
<p align="justify"><span style="font-family:'Times New Roman';font-size:14px;">（1）首先启动生产者</span></p>
<p align="justify"><span style="font-family:'Times New Roman';font-size:14px;">KAFKA_HEAP_OPTS="-Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/config/kafka_client_jaas.conf -Xmx512M" /opt/package/kafka_2.10-0.10.1.0/bin/kafka-console-producer.sh
 --topic mytopic --broker-list hdp1.example.com:9092 --producer.config /opt/package/kafka_2.10-0.10.1.0/config/producer.properties</span></p>
<p align="justify"><span style="font-family:'Times New Roman';font-size:14px;">（2）启动remoatest3.conf脚本</span></p>
<p align="justify"><span style="font-family:'Times New Roman';font-size:14px;">bash bin/logstash -f config/remoatest3.conf</span></p>
<p align="justify"><span style="font-family:'Times New Roman';font-size:14px;">（3）启动消费者</span></p>
<p align="justify"><span style="font-family:'Times New Roman';font-size:14px;">KAFKA_HEAP_OPTS="-Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/config/kafka_client_jaas.conf -Xmx512M"  /opt/package/kafka_2.10-0.10.1.0/bin/kafka-console-consumer.sh
 --topic mytopic --bootstrap-server hdp1.example.com:9092  --from-beginning --consumer.config /opt/package/kafka_2.10-0.10.1.0/config/consumer.properties</span></p>
<p align="justify"><span style="font-family:'Times New Roman';font-size:14px;">（4）生产者生产三条消息：</span></p>
<p align="justify" style="text-align:center;"><span style="font-family:'Times New Roman';font-size:14px;"> <img src="https://img-blog.csdn.net/20170908161348698?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmVtb2FfRGVuZ3Fpbnlp/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></p>
<p align="justify" style="text-align:center;"><span style="font-family:'Times New Roman';font-size:14px;"><span style="text-align:center;">图3.1 截图4</span><br></span></p>
<p align="justify"><span style="font-family:'Times New Roman';font-size:14px;">（5）查看remoatest3.conf脚本启动后信息，根据脚本中配置的output{stdout{codec=&gt;rubydebug}}输出了这三条消息：</span></p>
<p align="justify" style="text-align:center;"><span style="font-family:'Times New Roman';font-size:14px;"> <img src="https://img-blog.csdn.net/20170908161401557?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmVtb2FfRGVuZ3Fpbnlp/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></p>
<p align="justify" style="text-align:center;"><span style="font-family:'Times New Roman';font-size:14px;"><span style="text-align:center;">图3.2 截图5</span><br></span></p>
<p align="justify"><span style="font-family:'Times New Roman';font-size:14px;">（6）查看消费者是否进行处理消息：</span></p>
<p align="justify" style="text-align:center;"><span style="font-family:'Times New Roman';font-size:14px;"> <img src="https://img-blog.csdn.net/20170908161412469?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmVtb2FfRGVuZ3Fpbnlp/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></p>
<p align="justify" style="text-align:center;"><span style="font-family:'Times New Roman';font-size:14px;"><span style="text-align:center;">图3.3 截图6</span><br></span></p>
<p align="justify"><span style="font-family:'Times New Roman';font-size:14px;">（7）在Kibana中查看Elasticsearch中是否存储了这些消息：</span></p>
<p align="justify"><span style="font-family:'Times New Roman';font-size:14px;">①查看Kibana中host及port参数：</span></p>
<p align="justify" style="text-align:center;"><span style="font-family:'Times New Roman';font-size:14px;"> <img src="https://img-blog.csdn.net/20170908161420750?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmVtb2FfRGVuZ3Fpbnlp/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></p>
<p align="justify" style="text-align:center;"><span style="font-family:'Times New Roman';font-size:14px;"><span style="text-align:center;">图3.4 截图7</span><br></span></p>
<p align="justify"><span style="font-family:'Times New Roman';font-size:14px;">②登录进入：http://kdc1.example.com:5601，在左边导航栏中选择Dev Tools，在控制台中输入GET _cat/indices，找到这条index信息。其中简单的CRUD操作包括PUT：添加，GET：查询，POST：修改，DELETE：删除。</span></p>
<p align="justify" style="text-align:center;"><span style="font-family:'Times New Roman';font-size:14px;"> <img src="https://img-blog.csdn.net/20170908161428239?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmVtb2FfRGVuZ3Fpbnlp/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></p>
<p align="justify" style="text-align:center;"><span style="font-family:'Times New Roman';font-size:14px;"><span style="text-align:center;">图3.5 截图8</span><br></span></p>
<p align="justify"><span style="font-family:'Times New Roman';font-size:14px;">查看到health的健康状态为green，green表示一切正常；yellow表示所有数据都可用，但某些副本尚未分配；red表示某些数据因为某些原因不可用。</span></p>
<p align="justify"><span style="font-family:'Times New Roman';font-size:14px;">（8）在Kibana查看具体内容：</span></p>
<p align="justify"><span style="font-family:'Times New Roman';font-size:14px;">GET logstash-remoatest3-2017.09.08/_search</span></p>
<p align="justify" style="text-align:center;"><span style="font-family:'Times New Roman';font-size:14px;"> <img src="https://img-blog.csdn.net/20170908161436078?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUmVtb2FfRGVuZ3Fpbnlp/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></p>
<p align="justify" style="text-align:center;"><span style="font-family:'Times New Roman';font-size:14px;"><span style="text-align:center;">图3.6 截图9</span><br></span></p>
<p align="justify"><span style="font-family:'Times New Roman';font-size:14px;"> </span></p>
            </div>
                </div>