---
layout:     post
title:      Ranger-Kafka插件安装
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
    <div class="show-content-free">
            <p>Ranger-Kafka插件安装,<br>
使用Ranger0.7.0版本，集成Kafka插件到Kafka集群,<br>
Kafka Plugin需要安装到所有的Kafka的集群节点上面。</p>
<h1>1.登陆Kafka的安装用户</h1>
<h1>2.下载插件包</h1>
<p>scp pub@10.43.156.193:/home/pub/ranger/ranger-0.7.0/target/ranger-0.7.0-SNAPSHOT-kafka-plugin.tar.gz .</p>
<h1>3.解压插件包</h1>
<p>tar -zxvf ranger-0.7.0-SNAPSHOT-kafka-plugin.tar.gz</p>
<h1>4.修改install.properties参数如下：</h1>
<pre><code>COMPONENT_INSTALL_DIR_NAME=/home/kafka/kafka_2.10-0.10.1.0
POLICY_MGR_URL=http://zdh-245:6080
SQL_CONNECTOR_JAR=/usr/share/java/mysql-connector-java.jar
REPOSITORY_NAME=kafkadev
CUSTOM_USER=kafka
CUSTOM_GROUP=hadoop
</code></pre>
<h1>5.在kafka的server.properties文件最下面添加如下内容</h1>
<pre><code>authorizer.class.name=org.apache.ranger.authorization.kafka.authorizer.RangerKafkaAuthorizer
</code></pre>
<h1>6.添加config目录到kafka用户的CLASSPATH中</h1>
<p>由于kafka启动时只指定了server.properties，否则会读取不到其他配置文件<br>
在.profile增加如下配置：</p>
<pre><code>export CLASSPATH=/home/kafka/kafka_2.10-0.10.1.0/config
</code></pre>
<p>source .profile</p>
<h1>7.使用root用户执行安装脚本</h1>
<p>./enable-kafka-plugin.sh</p>
<h1>8.创建服务service</h1>
<p>在RangerAdminServer里面Kafka标签下面创建名称为kafkadev的service,<br>
service里面的Zookeeper Connect String填写zookeeper集群的IP:Port,例如：<br>
zdh-237:2181,zdh-238:2181,zdh-239:2181</p>
<h1>9.查看Kafka插件</h1>
<p>在RangerAdminServer的Audit的Plugin页面里面可以看到Kafka插件，<br>
插件kafka@zdh-237-kafkadev对应kafkadev的服务。</p>
<h1>10.将ranger-0.7.0-SNAPSHOT-kafka-plugin拷贝到其他集群</h1>
<p>并且重复上面的5,6,7步骤。</p>
<h1>11.验证ranger的权限控制</h1>
<p>在ranger-admin的users页面新建一个内部用户ANONYMOUS。<br>
关闭all-topic策略<br>
新建newTopic237only策略，配置ANONYMOUS用户(或者内部public组)对newTopic的所有权限，ip为10.43.159.237或者*<br>
创建一个topic:<br>
kafka-topics.sh --create --topic newTopic --replication-factor 3 --partitions 1 --zookeeper zdh-237:2181<br>
开启生产者：<br>
kafka-console-producer.sh --broker-list zdh-237:9092 --sync --topic newTopic<br>
开启消费者：<br>
kafka-console-consumer.sh --zookeeper zdh-237:2181 --topic newTopic --from-beginning<br>
上述操作在10.43.159.237执行成功，在其他IP则执行失败。<br>
由于执行kafka-console-producer.sh时，没有配置鉴权，使用的用户是ANONYMOUS（匿名用户），<br>
会导致RangerKafkaAuthorizerd对其鉴权失败，客户端返回错误码ClusterAuthorizationFailedCode=31。</p>
<h1>12.其他</h1>
<p>kafka非安全模式对Ranger的策略配置要求说明：<br><a href="https://link.jianshu.com?t=https%3A%2F%2Fcommunity.hortonworks.com%2Farticles%2F12699%2Franger-and-kafka-integration-faq.html" rel="nofollow">https://community.hortonworks.com/articles/12699/ranger-and-kafka-integration-faq.html</a><br><a href="https://link.jianshu.com?t=https%3A%2F%2Fcwiki.apache.org%2Fconfluence%2Fdisplay%2FRANGER%2FKafka%2BPlugin" rel="nofollow">https://cwiki.apache.org/confluence/display/RANGER/Kafka+Plugin</a></p>
<p>修改kafka的config目录下zookeeper.properties文件修改内容如下<br>
dataDir=/home/garrison/zookeeper-3.5.1-alpha/tmp</p>
<p>通过log4j.properties开启ranger kafka插件的审计日志<br>
log4j.logger.org.apache.ranger=DEBUG, authorizerAppender</p>

          </div>
              </div>
                </div>