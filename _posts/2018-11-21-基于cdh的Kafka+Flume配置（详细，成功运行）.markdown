---
layout:     post
title:      基于cdh的Kafka+Flume配置（详细，成功运行）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h1><a name="_Toc1041_WPSOffice_Type2"></a><strong><strong>目</strong></strong><strong> </strong><strong><strong>录</strong></strong></h1>

<p><a href="#_Toc6356_WPSOffice_Level1" rel="nofollow"><strong><strong>一  Kafka</strong></strong><strong> </strong><strong><strong>1</strong></strong></a></p>

<p><a href="#_Toc9241_WPSOffice_Level2" rel="nofollow">1 版本选择 1</a></p>

<p><a href="#_Toc5954_WPSOffice_Level2" rel="nofollow">2 集群搭建规划 1</a></p>

<p><a href="#_Toc10251_WPSOffice_Level2" rel="nofollow">3 修改Kafka相关配置文件 1</a></p>

<p><a href="#_Toc31565_WPSOffice_Level2" rel="nofollow">4 配置全局变量 1</a></p>

<p><a href="#_Toc25441_WPSOffice_Level2" rel="nofollow">5 启动Kafka集群 2</a></p>

<p><a href="#_Toc28042_WPSOffice_Level2" rel="nofollow">6 测试数据 3</a></p>

<p><a href="#_Toc13045_WPSOffice_Level1" rel="nofollow"><strong><strong>二 Flume</strong></strong><strong> </strong><strong><strong>5</strong></strong></a></p>

<p><a href="#_Toc20015_WPSOffice_Level2" rel="nofollow">1 版本选择 5</a></p>

<p><a href="#_Toc27454_WPSOffice_Level2" rel="nofollow">2修改Flume相关配置文件 5</a></p>

<p><a href="#_Toc29247_WPSOffice_Level2" rel="nofollow">3 配置全局变量 6</a></p>

<p><a href="#_Toc26709_WPSOffice_Level2" rel="nofollow">4 启动Flume 6</a></p>

<p><a href="#_Toc18727_WPSOffice_Level2" rel="nofollow">5 测试数据 7</a></p>

<p><a href="#_Toc1632_WPSOffice_Level1" rel="nofollow"><strong><strong>三 Flume + Kafka测试启动命令</strong></strong><strong> </strong><strong><strong>10</strong></strong></a></p>

<p><a href="#_Toc29910_WPSOffice_Level2" rel="nofollow">1 Flume启动测试命令 10</a></p>

<p><a href="#_Toc10111_WPSOffice_Level2" rel="nofollow">2 Kafaka启动测试命令 10</a></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><strong><a name="_Toc6356_WPSOffice_Level1"></a><strong><strong>一  Kafka</strong></strong></strong></p>

<h3><strong><a name="_Toc9241_WPSOffice_Level2"></a><strong><strong>1 版本选择</strong></strong></strong></h3>

<p style="margin-left:0pt;">CDH-3.7.5的组件版本：KAFKA-3.1.0-1.3.1.0.p0.35</p>

<h3><strong><a name="_Toc5954_WPSOffice_Level2"></a><strong><strong>2 集群搭建规划</strong></strong></strong></h3>

<table border="1" cellspacing="0" style="width:426.1pt;"><tbody><tr><td style="vertical-align:top;width:106.5pt;">
			<p style="margin-left:0pt;"> </p>
			</td>
			<td style="vertical-align:top;width:106.5pt;">
			<p style="margin-left:0pt;">Manager</p>
			</td>
			<td style="vertical-align:top;width:106.55pt;">
			<p style="margin-left:0pt;">Namenode</p>
			</td>
			<td style="vertical-align:top;width:106.55pt;">
			<p style="margin-left:0pt;">Datanode</p>
			</td>
		</tr><tr><td style="vertical-align:top;width:106.5pt;">
			<p style="margin-left:0pt;">ZooKeeper</p>
			</td>
			<td style="vertical-align:top;width:106.5pt;">
			<p style="margin-left:0pt;">是</p>
			</td>
			<td style="vertical-align:top;width:106.55pt;">
			<p style="margin-left:0pt;">是</p>
			</td>
			<td style="vertical-align:top;width:106.55pt;">
			<p style="margin-left:0pt;">是</p>
			</td>
		</tr><tr><td style="vertical-align:top;width:106.5pt;">
			<p style="margin-left:0pt;">Kafka(broker)</p>
			</td>
			<td style="vertical-align:top;width:106.5pt;">
			<p style="margin-left:0pt;">是</p>
			</td>
			<td style="vertical-align:top;width:106.55pt;">
			<p style="margin-left:0pt;">是</p>
			</td>
			<td style="vertical-align:top;width:106.55pt;">
			<p style="margin-left:0pt;">是</p>
			</td>
		</tr><tr><td style="vertical-align:top;width:106.5pt;">
			<p style="margin-left:0pt;">Flume(agent)</p>
			</td>
			<td style="vertical-align:top;width:106.5pt;">
			<p style="margin-left:0pt;">是</p>
			</td>
			<td style="vertical-align:top;width:106.55pt;">
			<p style="margin-left:0pt;">---</p>
			</td>
			<td style="vertical-align:top;width:106.55pt;">
			<p style="margin-left:0pt;">---</p>
			</td>
		</tr></tbody></table><p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><em><span style="color:#ff0000;"><em>【注意：必须配置好主机（免密登录、修改host主机名）】</em></span></em></p>

<h3><strong><a name="_Toc10251_WPSOffice_Level2"></a><strong><strong>3 修改Kafka相关配置文件</strong></strong></strong></h3>

<p style="margin-left:0pt;"><strong><strong>第一步</strong></strong>：</p>

<p style="margin-left:0pt;">cd /opt/cloudera/parcels/KAFKA-3.1.0-1.3.1.0.p0.35/etc/kafka/conf.dist</p>

<p style="margin-left:0pt;"><strong><strong>第二步</strong></strong>：</p>

<p style="margin-left:0pt;">vi  server.properties</p>

<p style="margin-left:0pt;"><strong><strong>第三步</strong></strong>：(主要修改其中的6个参数)</p>

<p style="margin-left:0pt;">broker.id=0  <span style="color:#ff0000;">//标示符（三台主机的id分别为0,1,2）</span></p>

<p style="margin-left:0pt;">  host.name=manager <span style="color:#ff0000;">//绑定的主机</span></p>

<p style="margin-left:0pt;">  log.dirs=/usr/local/soft/kafka/kafka-logs  <span style="color:#ff0000;">//数据保存的位置</span></p>

<p style="margin-left:0pt;">  log.retention.hours=168  //<span style="color:#ff0000;">数据的保留时间</span><span style="color:#ff0000;">(168 hours=7天)</span></p>

<p style="margin-left:0pt;">  zookeeper.connect=manager:2181,namenode:2181,datanode:2181</p>

<p style="margin-left:0pt;">  delete.topic.enable=true //<span style="color:#ff0000;">可以删除已创建主题</span></p>

<h3><strong><a name="_Toc31565_WPSOffice_Level2"></a><strong><strong>4 配置全局变量</strong></strong></strong></h3>

<p style="margin-left:0pt;">（1）vi .bashrc</p>

<p style="margin-left:0pt;">（2）添加配置</p>

<p style="margin-left:0pt;">export KAFKA_HOME=/opt/cloudera/parcels/KAFKA-3.1.0-1.3.1.0.p0.35</p>

<p style="margin-left:0pt;">export PATH=$PATH:$KAFKA_HOME/bin</p>

<ol><li>：退出保存，执行以下命令</li>
	<li>：将配置好的.bashrc文件分发给Namenode、DataNode主机</li>
</ol><p style="margin-left:0pt;">scp -r .bashrc namenode:$PWD</p>

<p style="margin-left:0pt;">scp -r .bashrc datanode:$PWD</p>

<h3><strong><a name="_Toc25441_WPSOffice_Level2"></a><strong><strong>5 启动Kafka集群</strong></strong></strong></h3>

<p style="margin-left:0pt;">（1）启动命令：</p>

<p style="margin-left:0pt;">kafka-server-start.sh /opt/cloudera/parcels/KAFKA-3.1.0-1.3.1.0.p0.35/etc/kafka/conf.dist/server.properties</p>

<p style="margin-left:0pt;">（2）启动成功：</p>

<ol><li>使用jps命令查看</li>
</ol><h3><strong><a name="_Toc28042_WPSOffice_Level2"></a><strong><strong>6 测试数据</strong></strong></strong></h3>

<ol><li>查看当前有哪些主题命令(此处选择manager主机的zk)：</li>
</ol><p style="margin-left:0pt;">  kafka-topics.sh --list --zookeeper manager:2181</p>

<p>      2.创建新的主题命令（设置为3个副本,1个分区）：</p>

<p style="margin-left:0pt;">kafka-topics.sh --create --zookeeper manager:2181,namenode:2181,datanode:2181 --replication-factor 2 --partitions 1 --topic lujuhui</p>

<p style="margin-left:0pt;">    在manager上模拟，往kafka的lujuhui主题里面发送数据，然后datanode上去消费这个数据:</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><a name="_Toc19315_WPSOffice_Level2"></a><strong><strong><a name="_Toc8286_WPSOffice_Level2"></a><a name="_Toc4115_WPSOffice_Level2">Manager发送数据命令:</a></strong></strong></p>

<p style="margin-left:0pt;">kafka-console-producer.sh --broker-list</p>

<p style="margin-left:0pt;">manager:9092,namenode:9092,datanode:9092  --topic lujuhui</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><a name="_Toc9502_WPSOffice_Level2"></a><strong><strong><a name="_Toc25746_WPSOffice_Level2"></a><a name="_Toc3295_WPSOffice_Level2">Datanode消费数据命令：</a></strong></strong></p>

<p style="margin-left:0pt;">kafka-console-consumer.sh --zookeeper datanode:2181 --from-beginning --topic lujuhui</p>

<p style="margin-left:0pt;"></p>

<p style="margin-left:0pt;"></p>

<p style="margin-left:0pt;">（4）删除主题命令：</p>

<p style="margin-left:0pt;">kafka-topics --delete --topic realtime --zookeeper manager:2181</p>

<p style="margin-left:0pt;"></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"> </p>

<h2><strong><a name="_Toc13045_WPSOffice_Level1"></a><strong><strong>二 Flume</strong></strong></strong></h2>

<h3><strong><a href="#_Toc4613_WPSOffice_Level2" rel="nofollow"><strong><strong>1 版本选择</strong></strong></a></strong></h3>

<p style="margin-left:0pt;">CDH-3.7.5的组件版本</p>

<h3><strong><a href="#_Toc5788_WPSOffice_Level2" rel="nofollow"><strong><strong>2</strong></strong><strong><strong>修改</strong></strong><strong><strong>Flume</strong></strong><strong><strong>相关配置文件</strong></strong></a></strong></h3>

<ol><li><a name="_Toc20956_WPSOffice_Level2"></a><a name="_Toc16972_WPSOffice_Level2">首先，进入flume的配置目录：</a></li>
</ol><p style="margin-left:0pt;">/opt/cloudera/parcels/CDH-5.7.5-1.cdh5.7.5.p0.3/etc/flume-ng/conf.empty</p>

<ol><li><a name="_Toc442_WPSOffice_Level2"></a><a name="_Toc798_WPSOffice_Level2">其次，新建一个文件：</a></li>
</ol><p style="margin-left:0pt;"><span style="color:#2207ed;">flume-conf</span><span style="color:#018b3e;">.properties</span></p>

<p style="margin-left:0pt;">[<span style="color:#ff0000;">注意：如果文件中有flume.conf,可以直接删除，新建的文件</span><span style="color:#2207ed;">名字</span><span style="color:#ff0000;">可以随意取，但是后缀必须是</span><span style="color:#018b3e;">.properties</span>]</p>

<ol><li><a name="_Toc24835_WPSOffice_Level2"></a><a name="_Toc15527_WPSOffice_Level2">在flume-conf.properties文件中添加链接kafka的配置：</a></li>
</ol><table border="1" cellspacing="0" style="width:472.8pt;"><tbody><tr><td style="background-color:#d7e7dd;width:472.8pt;">
			<p style="margin-left:0pt;">a1.sources = r1</p>

			<p style="margin-left:0pt;">a1.sinks = s1</p>

			<p style="margin-left:0pt;">a1.channels = c1</p>

			<p style="margin-left:0pt;"> </p>

			<p style="margin-left:0pt;"><strong><strong>#sources 消息生产</strong></strong></p>

			<p style="margin-left:0pt;">a1.sources.r1.type = spooldir   </p>

			<p style="margin-left:0pt;">a1.sources.r1.channels = c1</p>

			<p style="margin-left:0pt;">a1.sources.r1.spoolDir = /usr/local/soft/flume/flume_dir <span style="color:#ff0000;">//用于存放收集的日志</span></p>

			<p style="margin-left:0pt;">a1.sources.r1.fileHeader = false</p>

			<p style="margin-left:0pt;">a1.sources.r1.interceptors = i1</p>

			<p style="margin-left:0pt;">a1.sources.r1.interceptors.i1.type = timestamp</p>

			<p style="margin-left:0pt;"> </p>

			<p style="margin-left:0pt;"><strong><strong>#channels 消息传递</strong></strong></p>

			<p style="margin-left:0pt;">a1.channels.c1.type = memory</p>

			<p style="margin-left:0pt;">a1.channels.c1.capacity = 1000</p>

			<p style="margin-left:0pt;">a1.channels.c1.transactionCapacity = 100</p>

			<p style="margin-left:0pt;"> </p>

			<p style="margin-left:0pt;"><strong><strong>#sinks 消息消费</strong></strong></p>

			<p style="margin-left:0pt;">a1.sinks.s1.type = org.apache.flume.sink.kafka.KafkaSink</p>

			<p style="margin-left:0pt;">a1.sinks.s1.brokerList = manager:9092,namenode:9092,datanode:9092 <span style="color:#ff0000;">//链接kafka</span></p>

			<p style="margin-left:0pt;">a1.sinks.s1.topics = lujuhui<span style="color:#ff0000;">//flume收集的日志分发给kafka的对应主题名称</span></p>

			<p style="margin-left:0pt;">a1.sinks.s1.requiredAcks = 1</p>

			<p style="margin-left:0pt;">a1.sinks.s1.batchSize = 20</p>

			<p style="margin-left:0pt;">a1.sinks.s1.channel = c1  <span style="color:#ff0000;">//注意这里是channel不是channels</span></p>
			</td>
		</tr></tbody></table><h3><strong><a name="_Toc29247_WPSOffice_Level2"></a><strong><strong>3</strong></strong><a href="#_Toc26854_WPSOffice_Level2" rel="nofollow"><strong><strong> 配置全局变量</strong></strong></a></strong></h3>

<ol><li>回到根目录输入命令： </li>
</ol><p style="margin-left:0pt;">vi .bashrc</p>

<ol><li>添加配置：</li>
</ol><p style="margin-left:0pt;">export FLUME_HOME=/opt/cloudera/parcels/CDH-5.7.5-1.cdh5.7.5.p0.3/lib/flume-ng</p>

<p style="margin-left:0pt;">export PATH=$PATH:$FLUME_HOME/bin</p>

<ol><li>保存 --&gt; 退出 --&gt; 更新</li>
</ol><p style="margin-left:0pt;">source .bashrc</p>

<h3><strong><a name="_Toc26709_WPSOffice_Level2"></a><strong><strong>4 </strong></strong><a href="#_Toc18534_WPSOffice_Level2" rel="nofollow"><strong><strong>启动</strong></strong><strong><strong>Flume</strong></strong></a></strong></h3>

<ol><li>分别启动zookeeper集群、kafka集群：</li>
</ol><p style="margin-left:0pt;">因为是在Cloudera Manager上直接添加的服务组件，所以可以直接在CM上启动。</p>

<p style="margin-left:0pt;"></p>

<ol><li>因为是在主机manager上配置的flume，因此启动manager主机上的flume，启动命令如下：</li>
</ol><p style="margin-left:0pt;">1）cd /opt/cloudera/parcels/CDH-5.7.5-1.cdh5.7.5.p0.3</p>

<p style="margin-left:0pt;">2)bin/flume-ng agent --conf conf --conf-file</p>

<p style="margin-left:0pt;">etc/flume-ng/conf.empty/flume-conf.properties --name a1</p>

<p style="margin-left:0pt;">-Dflume.root.logger=INFO,console</p>

<p style="margin-left:0pt;"></p>

<p style="margin-left:0pt;"></p>

<p style="margin-left:0pt;"></p>

<h3><strong><a href="#_Toc26618_WPSOffice_Level2" rel="nofollow"><strong><strong>5</strong></strong><strong><strong> 测试数据</strong></strong><strong> </strong></a></strong></h3>

<p>（1）在namenode主机上添加一个kafka.txt文件，然后发送到manager主机上的/usr/local/soft/flume/flume_dir目录中，这时可以发现manager主机上发生如下变化：</p>

<p style="margin-left:0pt;">（2）随机选择dataNode主机，查看从manager主机上的flume传过来的数据:</p>

<p style="margin-left:0pt;">1）kafka-console-consumer.sh --zookeeper</p>

<p style="margin-left:0pt;">manager:2181,namenode:2181,datanode:2181 --from-beginning --topic lujuhui</p>

<p style="margin-left:0pt;">（3）Cloudera Manager上的监控状态</p>

<p style="margin-left:0pt;"> </p>

<h2><strong><a name="_Toc1632_WPSOffice_Level1"></a><strong><strong>三 Flume + Kafka测试启动命令</strong></strong></strong></h2>

<p style="margin-left:0pt;"><a name="_Toc29910_WPSOffice_Level2"></a><strong><strong>1 F</strong></strong><strong><strong>lume启动测试命令</strong></strong></p>

<p style="margin-left:0pt;">1）cd /opt/cloudera/parcels/CDH-5.7.5-1.cdh5.7.5.p0.3</p>

<p style="margin-left:0pt;">2) bin/flume-ng agent --conf conf --conf-file etc/flume-ng/conf.empty/flume-conf.properties --name</p>

<p style="margin-left:0pt;">a1 -Dflume.root.logger=INFO,console</p>

<h2><strong><a name="_Toc10111_WPSOffice_Level2"></a><strong><strong>2 Kafaka启动测试命令</strong></strong></strong></h2>

<p style="margin-left:0pt;">kafka-console-consumer.sh --zookeeper manager:2181,namenode:2181,datanode:2181 --from-beginning --topic lujuhui</p>

<p style="margin-left:0pt;"> </p>            </div>
                </div>