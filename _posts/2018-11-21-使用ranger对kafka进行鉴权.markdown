---
layout:     post
title:      使用ranger对kafka进行鉴权
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="使用ranger对kafka进行鉴权">使用ranger对kafka进行鉴权</h1>



<h4 id="测试环境ranger-kafka-plugin为063版本kafka版本为kafka210-01011且kafka-broker为一个节点">测试环境：ranger-kafka-plugin为0.6.3版本，kafka版本为kafka_2.10-0.10.1.1，且kafka broker为一个节点。</h4>



<h4 id="一ranger对kafka进行权限控制前提需要kafka开启kerberos认证注意若kafka不开启kerberos的话ranger无法获取kafka相关操作的用户进而无法完成鉴权操作">一、Ranger对kafka进行权限控制，前提需要kafka开启kerberos认证（注意：若kafka不开启kerberos的话Ranger无法获取kafka相关操作的用户，进而无法完成鉴权操作）</h4>



<h4 id="二开启kerberos认证">二、开启kerberos认证</h4>



<h5 id="包括zookeeper开启kerberos认证kafka开启zookeeper认证">包括zookeeper开启kerberos认证，kafka开启zookeeper认证。</h5>



<h5 id="1安装配置kerberos-server">1、安装配置Kerberos Server</h5>

<p>（1）安装server：  <br>
     yum install krb5-libs krb5-server krb5-workstation <br>
     具体配置略.</p>

<p>（2）安装client：yum install krb5-libs krb5-workstation</p>



<h5 id="2开启zookeeper-kerberos认证">2、开启zookeeper kerberos认证</h5>

<p>由于kafka依赖zookeeper，所以zookeeper也需要开启kerberos认证，这部分内容可参考zookeeper官网进行配置。</p>



<h5 id="3开启kafka-kerberos认证">3、开启kafka kerberos认证</h5>

<p>（1） 编辑kafka_server_jaas.conf文件：内容如下：</p>

<p>KafkaServer { <br>
     com.sun.security.auth.module.Krb5LoginModule required <br>
     useKeyTab=true <br>
     keyTab=”/etc/security/keytabs/kafka.keytab” <br>
     storeKey=true <br>
     useTicketCache=false <br>
     serviceName=”kafka” <br>
     principal=”kafka/xhhdev@HHKDC”; <br>
  };</p>

<p>KafkaClient { <br>
   com.sun.security.auth.module.Krb5LoginModule required <br>
   useTicketCache=true <br>
   renewTicket=true <br>
   serviceName=”kafka”; <br>
  };</p>

<p>Client { <br>
   com.sun.security.auth.module.Krb5LoginModule required <br>
   useKeyTab=false <br>
   useTicketCache=true; <br>
  }; </p>

<p>（2）加入jvm参数 <br>
 -Djava.security.krb5.conf=/etc/krb5.conf -Djava.security.auth.login.config=/usr/local/soft/kafka/config/kafka_server_jaas.conf”</p>

<p>在下面脚本中加入： <br>
vim kafka-run-class.sh</p>

<p>if [ -z “$KAFKA_JVM_PERFORMANCE_OPTS” ]; then <br>
  KAFKA_JVM_PERFORMANCE_OPTS=”-server -XX:+UseG1GC -XX:MaxGCPauseMillis=20 -XX:InitiatingHeapOccupancyPercent=35 -XX:+DisableExplicitGC -Djava.awt.headless=true -Djava.security.krb5.conf=/etc/krb5.conf -Djava.security.auth.login.config=/usr/local/soft/kafka/config/kafka_server_jaas.conf” <br>
fi</p>

<p>（3）server.properties中加入如下配置</p>

<p>listeners=SASL_PLAINTEXT://xhhdev:6667</p>

<p>security.inter.broker.protocol=SASL_PLAINTEXT</p>

<p>sasl.mechanism.inter.broker.protocol=GSSAPI</p>

<p>sasl.enabled.mechanisms=GSSAPI</p>

<p>sasl.kerberos.service.name=kafka</p>

<p>（4）创建文件producer.properties 及consumer.properties 且加入如下配置：</p>

<p>security.protocol=SASL_PLAINTEXT</p>

<p>sasl.mechanism=GSSAPI</p>

<p>sasl.kerberos.service.name=kafka</p>



<h4 id="三安装ranger-kafka-plugin">三、安装ranger-kafka-plugin</h4>

<p>1、解压包：tar –zxvf ranger-0.6.3-kafka-plugin.tar.gz</p>

<p>2、修改install.properties文件</p>

<p>POLICY_MGR_URL=<a href="http://xhhtest:6080" rel="nofollow" target="_blank">http://xhhtest:6080</a>  （Ranger-admin 访问地址） <br>
REPOSITORY_NAME=kafkadev   （服务名称） <br>
XAAUDIT.DB.IS_ENABLED=true <br>
XAAUDIT.DB.FLAVOUR=MYSQL <br>
XAAUDIT.DB.HOSTNAME= xhhtest <br>
XAAUDIT.DB.DATABASE_NAME=ranger_audit <br>
XAAUDIT.DB.USER_NAME=root <br>
XAAUDIT.DB.PASSWORD=123456</p>

<p>3、添加软连接</p>

<p>假设kafka_2.10-0.10.1.1安装目录为：/usr/local/soft/kafka/  <br>
ranger-0.6.3-kafka-plugin 安装目录为：/usr/local/ranger-0.6.3-kafka-plugin <br>
则执行： <br>
ln -s   /usr/local/soft/kafka/conf/   /usr/local/kafka <br>
ln -s   /usr/local/soft/kafka/lib/   /usr/local/kafka</p>

<p>4、执行 ./enable-kafka-plugin.sh</p>

<p>之所以执行软连接，是因为执行此脚本时需要知道kafka_2.10-0.10.1.1的conf 和lib所在位置。</p>

<p>5、重启kafka服务</p>



<h4 id="四鉴权测试">四、鉴权测试</h4>

<p>1、在ranger-admin界面添加kafka service （name为kafkadev） <br>
2、查看插件是否成功，audit-&gt;plugins显示 <br>
3、测试 <br>
（1）执行kinit -kt /etc/security/keytabs/kafka.service.keytab kafka/xhhdev@HHKDC <br>
（2）创建topic：./kafka-topics.sh –zookeeper xhhdev:2181 –create –topic topic_test –partitions 1 –replication-factor 1,此时可以创建成功。 <br>
（3）生产：切换到用户xhhtest， <br>
执行kinit -kt /etc/security/keytabs/xhhtest.service.keytab xhhtest/xhhdev@HHKDC</p>

<p>./kafka-console-producer.sh –broker-list xhhdev:6667 –topic topic_test –producer.config producer.properties，此时报错如下：</p>

<p>WARN Error while fetching metadata with correlation id 0 : {topic_test=UNKNOWN_TOPIC_OR_PARTITION} (org.apache.kafka.clients.NetworkClient)</p>

<p>原因是:xhhtest没有权限</p>

<p>（4）设置policy，增加权限，再次执行ok。</p>

<p>参考文献： <br>
<a href="http://ranger.apache.org/" rel="nofollow">http://ranger.apache.org/</a> <br>
<a href="http://kafka.apache.org/documentation/#security_sasl" rel="nofollow">http://kafka.apache.org/documentation/#security_sasl</a> <br>
<a href="https://www.ibm.com/support/knowledgecenter/en/SSPT3X_4.2.5/com.ibm.swg.im.infosphere.biginsights.admin.doc/doc/admin_ranger_plugin_kafka.html" rel="nofollow">https://www.ibm.com/support/knowledgecenter/en/SSPT3X_4.2.5/com.ibm.swg.im.infosphere.biginsights.admin.doc/doc/admin_ranger_plugin_kafka.html</a></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>