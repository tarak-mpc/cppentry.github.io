---
layout:     post
title:      Kafka笔记-2-kafka安装步骤及常用命令
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为个人学习时搜索整理而来，部分内容已不知来源，未标明出处的，在此向原创者致敬！					https://blog.csdn.net/wfzczangpeng/article/details/51790212				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="三kafka安装步骤">三：kafka安装步骤</h1>

<h2 id="31-zookeeper安装"><strong>3.1 zookeeper安装</strong></h2>

<p>kafka依赖zookeeper，先介绍zookeeper安装方法：</p>

<p>以搭建三台节点为例： <br>
下载zookeeper-3.4.6的安装包之后, 解压到合适目录. 进入zookeeper目录下的conf子目录, 创建zoo.cfg文件</p>

<p><img src="https://img-blog.csdn.net/20160630143504857" alt="这里写图片描述" title=""></p>

<p><strong>参数说明:</strong> <br>
tickTime: zookeeper中使用的基本时间单位, 毫秒值. <br>
dataDir: 数据目录. 可以是任意目录. <br>
dataLogDir: log目录, 同样可以是任意目录. 如果没有设置该参数, 将使用和dataDir相同的设置. <br>
clientPort: 监听client连接的端口号. <br>
initLimit: zookeeper集群中的包含多台server, 其中一台为leader, 集群中其余的server为follower. initLimit参数配置初始化连接时, follower和leader之间的最长心跳时间. 此时该参数设置为5, 说明时间限制为5倍tickTime, 即5*10000=50000ms=50s. <br>
syncLimit: 该参数配置leader和follower之间发送消息, 请求和应答的最大时间长度. 此时该参数设置为2, 说明时间限制为2倍tickTime, 即20000ms=20s. <br>
server.X=A:B:C 其中X是一个数字, 表示这是第几号server. <br>
 A是该server所在的IP地址.  <br>
B配置该server和集群中的leader交换消息所使用的端口.  <br>
C配置选举leader时所使用的端口. 如果配置的是伪集群模式, 那么各个server的B, C参数必须不同. <br>
在之前设置的dataDir中新建myid文件, 写入一个数字, 该数字表示这是第几号server. 该数字必须和zoo.cfg文件中的server.X中的X一一对应. <br>
三台zookeeper的zoo.cfg文件相同。 <br>
但是dataDir目录下的myid内容必须不同： <br>
第一台： <br>
dataDir=/opt/zookeeper-3.4.6/data/storage/zookeeper/myid文件中写入1,  <br>
第二台： <br>
dataDir=/opt/zookeeper-3.4.6/data/storage/zookeeper/myid文件中写入2,  <br>
第三台： <br>
dataDir=/opt/zookeeper-3.4.6/data/storage/zookeeper/myid文件中写入3. <br>
分别在三台节点上启动zookeeper： <br>
/opt/zookeeper-3.4.6/bin/zkServer.sh start</p>

<h2 id="32-zookeeper常用命令"><strong>3.2 zookeeper常用命令</strong></h2>

<p><strong>3.2.1启动ZK服务</strong> <br>
bin/zkServer.sh  start <br>
<strong>3.2.2查看ZK服务状态</strong> <br>
bin/zkServer.sh  status <br>
<strong>3.2.3 停止ZK服务</strong> <br>
bin/zkServer.sh  stop <br>
<strong>3.2.4 重启ZK服务</strong> <br>
bin/zkServer.sh  restart</p>

<h2 id="33-kafka安装"><strong>3.3 Kafka安装</strong></h2>

<p>直接下载解压即可： <br>
tar -xzf kafka_2.9.1-0.8.1.2.tgz</p>



<h2 id="34-kafka常用命令">3.4 Kafka常用命令</h2>

<p><strong>3.4.1启动命令</strong> <br>
cd opt/ kafka_2.9.1-0.8.1.2/ <br>
JMX_PORT=9997 nohup bin/kafka-server-start.sh  config/server.properties &amp;</p>

<p><strong>3.4.2 停止服务命令</strong> <br>
/opt/kafka_2.9.1-0.8.2.1/bin/kafka-server-stop.sh</p>

<p><strong>3.4.3 创建topic命令</strong> <br>
/opt/kafka_2.9.1-0.8.2.1/bin/kafka-topics.sh –topic  obd_alarm  –create –partitions 3  –replication-factor 3   –zookeeper ip01:2181 ip02:2181 ip03:2181 <br>
解释： <br>
obd_alarm：是一个topic的名字 <br>
–partitions 3 是指这个topic有3个partition <br>
–replication-factor 3 是指这个topic的所有partition有3个复制因子</p>

<p><strong>3.4.4 Kafka上对一个Topic修改复制因子</strong> <br>
./kafka-reassign-partitions.sh  –zookeeper ip01:2181 ip02:2181 ip03:2181 –reassignment-json-file  ../replics/obd_trip.json  –execute <br>
–reassignment-json-file 是固定写法 <br>
../replics/obd_trip.json 是给定的一个修改replicas的自定义json文件,例如：</p>

<p><strong>3.4.5 删除topic命令</strong> <br>
./kafka-topics.sh –delete    –zookeeper ip01:2181 ip02:2181 ip03:2181  –topic  obd_platform <br>
3.4.6 查看所有topic命令 <br>
/opt/kafka_2.9.1-0.8.2.1/bin/kafka-topics.sh  –describe    –zookeeper ip01:2181  ip02:2181  ip03:2181</p>

<p><strong>本文来自本人学习kafka时从网上的收集总结，向原创致敬！</strong></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>