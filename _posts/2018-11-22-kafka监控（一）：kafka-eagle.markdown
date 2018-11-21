---
layout:     post
title:      kafka监控（一）：kafka-eagle
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本博客所有文章均为原创，如有转载，请注明原文地址，谢谢！----桃花惜春风					https://blog.csdn.net/xiaoyu_BD/article/details/81700536				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>kafka-eagle是一款kafka监控软件，是笔者在接触kafka这几年来，使用的最好的一款开源监控工具。它可以同时监控多个集群，监控 Kafka 集群中 Topic 被消费的情况。包含 Lag 的产生，Offset 的变动，Partition 的分布。并且包含KafkaMonitor 和 Kafka Manager 的相关功能。可以说是既可以管理集群，又可以监控kafka的性能和消费情况，同时又支持sql查询。居家旅行必备。</p>

<p>kafka-eagle官方文档：<a href="https://ke.smartloli.org/3.Manuals/9.KafkaSQL.html" rel="nofollow">https://ke.smartloli.org/3.Manuals/9.KafkaSQL.html</a></p>

<p>kafka-eagle官方下载：<a href="http://download.smartloli.org/" rel="nofollow">http://download.smartloli.org/</a></p>

<p style="margin-left:0cm;">kafka-eagle安装</p>

<p style="margin-left:0cm;">解压</p>

<blockquote>
<p style="margin-left:0cm;">tar -zxvf kafka-eagle-${version}-bin.tar.gz</p>
</blockquote>

<p>配置环境变量</p>

<blockquote>
<p>vi /etc/profile</p>

<p>export KE_HOME=/data/soft/new/kafka-eagle<br>
export PATH=$PATH:$KE_HOME/bin</p>
</blockquote>

<p>修改配置文件</p>

<blockquote>
<p>cd ${KE_HOME}/conf<br>
vi system-config.properties</p>

<p># Multi zookeeper&amp;kafka cluster list -- The client connection address of the Zookeeper cluster is set here</p>

<p>#可以配置多个集群，单集群这里可以把cluster2注释掉<br>
kafka.eagle.zk.cluster.alias=cluster1,cluster2<br>
cluster1.zk.list=tdn1:2181,tdn2:2181,tdn3:2181<br>
cluster2.zk.list=xdn1:2181,xdn2:2181,xdn3:2181</p>

<p># Zkcli limit -- Zookeeper cluster allows the number of clients to connect to</p>

<p>#zookeeper线程数<br>
kafka.zk.limit.size=25</p>

<p># Kafka Eagle webui port -- WebConsole port access address</p>

<p>#kafka-eagle页面访问端口<br>
kafka.eagle.webui.port=8048</p>

<p># Kafka offset storage -- Offset stored in a Kafka cluster, if stored in the zookeeper, you can not use this option</p>

<p>#配置存储偏移量位置，注意低版本的api存储的还是zookeeper。不同集群可以分开配置<br>
cluster1.kafka.eagle.offset.storage=kafka<br>
cluster2.kafka.eagle.offset.storage=kafka</p>

<p># Whether the Kafka performance monitoring diagram is enabled</p>

<p>#是否启用监控图表<br>
kafka.eagle.metrics.charts=false</p>

<p># If offset is out of range occurs, enable this property -- Only suitable for kafka sql</p>

<p>#使用kafka-sql时可以考虑开启<br>
kafka.eagle.sql.fix.error=false</p>

<p># Delete kafka topic token -- Set to delete the topic token, so that administrators can have the right to delete</p>

<p>#删除topic操作超级管理员token<br>
kafka.eagle.topic.token=keadmin</p>

<p># kafka sasl authenticate, current support SASL_PLAINTEXT</p>

<p>#是否启用SASL协议<br>
kafka.eagle.sasl.enable=false<br>
kafka.eagle.sasl.protocol=SASL_PLAINTEXT<br>
kafka.eagle.sasl.mechanism=PLAIN<br>
kafka.eagle.sasl.client=&lt;kafka_client_jaas.conf file path&gt;</p>

<p>#邮件服务，用户报警发送</p>

<p>kafka.eagle.mail.enable=true<br>
kafka.eagle.mail.sa=alert_sa<br>
kafka.eagle.mail.username=alert_sa@163.com<br>
kafka.eagle.mail.password=mqslimczkdqabbbh<br>
kafka.eagle.mail.server.host=smtp.163.com<br>
kafka.eagle.mail.server.port=25</p>

<p>#kafka-eagle默认存储在sqlite，注意配置路径</p>

<p># Default use sqlite to store data<br>
kafka.eagle.driver=org.sqlite.JDBC<br>
# It is important to note that the '/hadoop/kafka-eagle/db' path must exist.<br>
kafka.eagle.url=jdbc:sqlite:/{安装路径}/db/ke.db<br>
kafka.eagle.username=root<br>
kafka.eagle.password=smartloli</p>

<p># &lt;Optional&gt; set mysql address</p>

<p>#也可以存储在mysql中。<br>
#kafka.eagle.driver=com.mysql.jdbc.Driver<br>
#kafka.eagle.url=jdbc:mysql://127.0.0.1:3306/ke?useUnicode=true&amp;characterEncoding=UTF-8&amp;zeroDateTimeBehavior=convertToNull<br>
#kafka.eagle.username=root<br>
#kafka.eagle.password=smartloli</p>
</blockquote>

<p>启动</p>

<blockquote>
<p>cd ${KE_HOME}/bin<br>
chmod +x ke.sh<br>
./ke.sh start</p>
</blockquote>

<p>——————————————————————————————————</p>

<p>作者：桃花惜春风<br>
转载请标明出处，原文地址：  <br><a href="https://blog.csdn.net/xiaoyu_BD/article/details/81700536" rel="nofollow">https://blog.csdn.net/xiaoyu_BD/article/details/81700536</a></p>

<p>如果感觉本文对您有帮助，请留下您的赞，您的支持是我坚持写作最大的动力，谢谢！</p>            </div>
                </div>