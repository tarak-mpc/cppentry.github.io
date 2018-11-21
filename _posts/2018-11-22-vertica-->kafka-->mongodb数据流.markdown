---
layout:     post
title:      vertica-->kafka-->mongodb数据流
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/u010522235/article/details/52121726				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-family:'微软雅黑';font-size:14px;line-height:21px;">此连接件为confluent修改过的jar包，这里不提供下载（涉及公司机密！！）</span></p>
<p><span style="font-family:'微软雅黑';font-size:14px;line-height:21px;">Kafka Connnect有两个核心概念：Source和Sink。 Source负责导入数据到Kafka，Sink负责从Kafka导出数据，它们都被称为Connector。</span></p>
<p><span style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><img src="https://img-blog.csdn.net/20160804182510466?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></p>
<p><span style="font-family:'微软雅黑';font-size:14px;line-height:21px;"></span></p>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">由于这里是kafka到mongo，所以这里使用Sink Connector</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><br style="background-color:inherit;"></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">
<div style="background-color:inherit;">首先安装kafka</div>
<div style="background-color:inherit;">1，Kafka是一种高吞吐量的分布式发布订阅消息系统，它可以处理消费者规模的网站中的所有动作流数据。</div>
<div style="background-color:inherit;">Kafka拓扑结构：</div>
</div>
<img src="https://img-blog.csdn.net/20160804182525544?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br><p></p>
<p><span style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><br></span></p>
<p><span style="font-family:'微软雅黑';font-size:14px;line-height:21px;"> Broker<br>
Kafka集群包含一个或多个服务器，这种服务器被称为broker<br>
 Topic<br>
每条发布到Kafka集群的消息都有一个类别，这个类别被称为Topic。（物理上不同Topic的消息分开存储，逻辑上一个Topic的消息虽然保存于一个或多个broker上但用户只需指定消息的Topic即可生产或消费数据而不必关心数据存于何处）<br>
 Partition<br>
Parition是物理上的概念，每个Topic包含一个或多个Partition.<br>
 Producer<br>
负责发布消息到Kafka broker<br>
 Consumer<br>
消息消费者，向Kafka broker读取消息的客户端。<br>
 Consumer Group<br>
每个Consumer属于一个特定的Consumer Group（可为每个Consumer指定group name，若不指定group name则属于默认的group）。<br>
安装zookeeper<br>
配置环境变量，/etc/profile添加以下内容：<br>
[root@dba06 kafka]# export ZOOKEEPER_HOME=/opt/app/kafka/zookeeper-3.4.6<br>
[root@dba06 kafka]# export PATH=$PATH:$ZOOKEEPER_HOME/bin<br>
[root@dba06 kafka]# cd zookeeper-3.4.6/conf/<br>
[root@dba06 conf]# ls<br>
configuration.xsl  log4j.properties  zoo_sample.cfg<br>
[root@dba06 conf]# cat zoo_sample.cfg | grep -v '#' &gt; zoo.cfg <br>
[root@dba06 conf]# cat zoo.cfg <br>
tickTime=2000<br>
initLimit=10<br>
syncLimit=5<br>
dataDir=/tmp/zookeeper<br>
clientPort=2181<br>
[root@dba06 conf]# ../bin/zkServer.sh start<br>
JMX enabled by default<br>
Using config: /opt/app/kafka/zookeeper-3.4.6/bin/../conf/zoo.cfg<br>
Starting zookeeper ... STARTED<br>
查看是否启动<br>
[root@dba06 conf]# ../bin/zkServer.sh status<br>
JMX enabled by default<br>
Using config: /opt/app/kafka/zookeeper-3.4.6/bin/../conf/zoo.cfg<br>
Mode: standalone<br>
Server启动之后, 就可以启动client连接server了, 执行脚本:<br>
[root@dba06 conf]# ../bin/zkCli.sh -server localhost:2181<br><br><br>
安装kafka<br>
安装kafka server之前需要单独安装zookeeper server，而且需要修改config/server.properties里面的IP信息<br>
[root@dba06 kafka]# cd kafka_2.11-0.9.0.0<br>
[root@dba06 kafka_2.11-0.9.0.0]# cd config/<br>
[root@dba06 config]# vi server.properties <br>
zookeeper.connect=localhost:2181<br>
这里需要修改默认zookeeper.properties配置<br>
[root@dba06 config]# vi zookeeper.properties <br>
保持默认设置<br>
先启动zookeeper，启动前先kill掉之前的zkServer.sh启动的zookeeper服务<br>
[root@dba06 bin]# ./zookeeper-server-start.sh ../config/zookeeper.properties &amp;<br>
启动kafka服务<br>
[root@dba06 bin]# ./kafka-server-start.sh ../config/server.properties  &amp;<br>
查看kafka进程是否启动：<br>
[root@dba06 bin]# find /. -name 'java'<br>
重新安装Java<br>
[root@dba06 bin]# yum install java-1.7.0<br>
[root@dba06 bin]# yum install java-1.7.0-openjdk  java-1.7.0-openjdk-devel<br>
配置环境变量<br>
[root@dba06 bin]# vi ~/.bash_profile<br>
export PATH<br>
export JAVA_HOME=/usr/lib/jvm/java-1.7.0-openjdk-1.7.0.111.x86_64/jre<br>
export CLASSPATH=.:$JAVA_HOME/jre/lib/rt.jar:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar<br>
export PATH=$PATH:$JAVA_HOME/bin<br>
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/lib/jvm/java-1.7.0-openjdk-1.7.0.111.x86_64/jre/lib/amd64/libjsig.so:/usr/lib/jvm/java-1.7.0-openjdk-1.7.0.111.x86_64/jre/lib/amd64/server/libjvm.so:/usr/lib/jvm/java-1.7.0-openjdk-1.7.0.111.x86_64/jre/lib/amd64/server:/usr/lib/jvm/java-1.7.0-openjdk-1.7.0.111.x86_64/jre/lib/amd64<br>
[root@dba06 bin]# source ~/.bash_profile<br>
[root@dba06 bin]# jps<br>
835 Kafka<br>
803 QuorumPeerMain<br>
1679 Jps<br>
咱们先看看vertica，推数据到kafka<br>
dbadmin=&gt; select version();<br>
              version               <br>
------------------------------------<br>
 Vertica Analytic Database v7.2.3-0<br>
(1 row)<br><br><br>
dbadmin=&gt; \q<br>
这里采用7.2.3版本<br>
dbadmin=&gt; SELECT KafkaExport(0, '1', '{"EUTIME":"'||"EUTIME"||'"}' USING PARAMETERS brokers='172.16.20.207:9092',topic='fafatest') OVER (PARTITION BEST) FROM tmp.DP_TRADE_PVCOUNT ;<br>
partition | key | message | failure_reason <br>
-----------+-----+---------+----------------<br>
(0 rows)<br>
说明，这里无需在对方创建topic，只需对方有zookeeper进程和kafka进程即可。<br>
报错详解：<br>
dbadmin=&gt; SELECT KafkaExport(0, '1', '{"EUTIME":"'||"STATDATE"||'"}' USING PARAMETERS brokers='172.16.57.55:9092',topic='fafa01') OVER (PARTITION BEST) FROM tmp.DP_TRADE_PVCOUNT ;<br>
ERROR 5861:  Error calling processPartition() in User Function KafkaExport at [src/KafkaExport.cpp:187], error code: 0, message: Error reading topic metadata for topic fafa01: Broker: Leader not available<br>
dbadmin=&gt; SELECT KafkaExport(0, '1', '{"EUTIME":"'||"STATDATE"||'"}' USING PARAMETERS brokers='172.16.57.55:9092',topic='fafa01') OVER () FROM tmp.DP_TRADE_PVCOUNT ;<br>
ERROR 5861:  Error calling processPartition() in User Function KafkaExport at [src/KafkaExport.cpp:323], error code: 0, message: Exception while processing partition row: [0] : [Fatal kafka error: T2:9092/0: Failed to resolve 'T2:9092': Temporary failure in
 name resolution]<br>
ERROR 5861问题是因为在双方的/etc/hosts下面要配置互相的ip，例如此时：<br>
172.16.57.55 T2<br>
172.16.57.208 bd-dev-vertica2-208 localhost<br>
在kafka上消费测试一下<br>
[root@dba06 bin]# ./kafka-console-consumer.sh --zookeeper localhost:2181 --from-beginning --topic fafa01<br>
{"EUTIME":"2016-04-30"}<br>
{"EUTIME":"2016-04-30"}<br>
{"EUTIME":"2016-05-01"}<br>
{"EUTIME":"2016-05-02"}<br>
{"EUTIME":"2016-05-02"}<br>
{"EUTIME":"2016-05-02"}<br>
....................<br>
....................<br>
可以看到json格式数据已经消费。<br><br><br>
下面尝试kafka到mongo连接<br>
下载kafka到mongo连接件，这里下载的连接件需要再使用confluect上面的kafka才能使用<br>
http://www.confluent.io/product/connectors<br>
这里使用改写的单向的版本，使用普通的kafka<br>
[root@dba06 libs]# rz<br>
?z waiting to receive.**B0100000023be50<br>
即<br>
connect-mongodb-1.0-jar-with-dependencies.jar<br>
将jar报放在lib下面<br>
将配置文件放在config下面<br>
[root@dba06 ~]# cd /opt/app/kafka/kafka_2.11-0.9.0.0/config/<br>
connect-mongo160-sink.properties<br>
connect-standalone.properties<br>
配置kafka到mongo连接件<br>
[root@T2 config]# vi connect-mongo160-sink.properties <br><br><br>
name=mongodb-sink-connector<br>
connector.class=org.apache.kafka.connect.mongodb.MongodbSinkConnector<br>
tasks.max=1<br>
host=172.16.57.160<br>
port=12001<br>
bulk.size=100<br>
mongodb.database=backbone<br>
mongodb.authorized.database=backbone<br>
skip.message.on.error=true<br>
mongodb.collections=test<br>
mongodb.user=backbone<br>
mongodb.password=Password$1<br>
topics=fafa01<br><br><br>
启动服务，连接服务<br>
这里先不管格式将connect-standalone.properties<br>
[root@T2 bin]#  vi /opt/app/software/kafka_2.11-0.10.0.0/config/connect-standalone.properties<br>
key.converter.schemas.enable=false<br>
value.converter.schemas.enable=false<br>
暂时关闭格式控制<br><br><br>
[root@T2 bin]# ./connect-standalone.sh ../config/connect-standalone.properties ../config/connect-mongo160-sink.properties<br><br><br><br><br>
查看mongo端<br>
mongos&gt; db.test.find()<br>
{ "_id" : NumberLong(2), "message" : "great", "EUTIME" : "2015-10-28" }<br>
{ "_id" : NumberLong(3), "haoshibu" : "4", "EUTIME" : "2015-10-29" }<br>
{ "_id" : NumberLong(4), "haoshibu" : "9", "EUTIME" : "2015-10-31" }<br>
{ "_id" : NumberLong(5), "haoshibu" : "6", "EUTIME" : "2015-11-12" }<br>
{ "_id" : NumberLong(6), "haoshibu" : "10", "EUTIME" : "2015-11-14" }<br>
{ "_id" : NumberLong(7), "haoshibu" : "2", "EUTIME" : "2015-11-17" }<br>
{ "_id" : NumberLong(8), "haoshibu" : "3", "EUTIME" : "2015-11-18" }<br>
{ "_id" : NumberLong(9), "haoshibu" : "1", "EUTIME" : "2015-11-19" }<br>
{ "_id" : NumberLong(10), "haoshibu" : "7", "EUTIME" : "2015-11-24" }<br>
{ "_id" : NumberLong(11), "haoshibu" : "8", "EUTIME" : "2015-11-27" }<br>
{ "_id" : NumberLong(12), "haoshibu" : "5", "EUTIME" : "2015-12-03" }<br>
{ "_id" : NumberLong(13), "haoshibu" : "4", "EUTIME" : "2015-12-09" }<br>
{ "_id" : NumberLong(14), "haoshibu" : "9", "EUTIME" : "2015-12-10" }<br>
{ "_id" : NumberLong(15), "haoshibu" : "6", "EUTIME" : "2015-12-11" }<br>
{ "_id" : NumberLong(16), "haoshibu" : "10", "EUTIME" : "2015-12-18" }<br>
{ "_id" : NumberLong(17), "haoshibu" : "2", "EUTIME" : "2015-12-20" }<br>
{ "_id" : NumberLong(18), "haoshibu" : "3", "EUTIME" : "2015-12-26" }<br>
{ "_id" : NumberLong(19), "haoshibu" : "1", "EUTIME" : "2015-12-27" }<br>
{ "_id" : NumberLong(20), "haoshibu" : "7", "EUTIME" : "2016-01-01" }<br>
{ "_id" : NumberLong(21), "haoshibu" : "8", "EUTIME" : "2016-01-03" }<br>
成功！<br></span></p>
<p><span style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><br></span></p>
            </div>
                </div>