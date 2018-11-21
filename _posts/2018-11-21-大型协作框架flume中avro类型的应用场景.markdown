---
layout:     post
title:      大型协作框架flume中avro类型的应用场景
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="color:rgb(51,51,51);font-family:arial, '宋体', sans-serif;font-size:14px;text-indent:28px;"><span></span>Flume是Cloudera提供的一个高可用的，高可靠的，分布式的海量日志采集、聚合和传输的系统，Flume支持在日志系统中定制各类数据发送方，用于收集数据；同时，Flume提供对数据进行简单处理，并写到各种数据接受方（可定制）的能力。</span></p>
<p><span style="font-family:arial, '宋体', sans-serif;font-size:14px;text-indent:28px;"><span><span style="color:#333333;"></span></span></span></p>
<div class="para-title level-2" style="color:rgb(51,51,51);clear:both;overflow:hidden;border-left:12px solid rgb(79,156,238);line-height:24px;font-size:22px;font-family:'Microsoft YaHei', SimHei, Verdana;">
<h2 class="title-text" style="font-size:22px;color:rgb(0,0,0);line-height:24px;font-weight:400;">
系统功能</h2>
<a class="edit-icon j-edit-link" style="color:rgb(136,136,136);display:block;line-height:22px;font-size:12px;font-family:SimSun;"><span class="cmn-icon wiki-lemma-icons wiki-lemma-icons_edit-lemma"></span>编辑</a></div>
<div class="anchor-list" style="color:rgb(51,51,51);font-family:arial, '宋体', sans-serif;">
<a name="1_1" class="lemma-anchor para-title" style="color:rgb(19,110,194);"></a><a name="sub9600530_1_1" class="lemma-anchor" style="color:rgb(19,110,194);"></a><a name="%E6%97%A5%E5%BF%97%E6%94%B6%E9%9B%86" class="lemma-anchor" style="color:rgb(19,110,194);"></a></div>
<div class="para-title level-3" style="color:rgb(51,51,51);clear:both;line-height:20px;font-size:18px;font-family:'Microsoft YaHei', SimHei, Verdana;">
<h3 class="title-text" style="font-size:18px;font-weight:400;">
日志收集</h3>
</div>
<div class="para" style="color:rgb(51,51,51);font-size:14px;text-indent:2em;line-height:24px;font-family:arial, '宋体', sans-serif;">
Flume最早是Cloudera提供的日志收集系统，目前是Apache下的一个孵化项目，Flume支持在日志系统中定制各类数据发送方，用于收集数据。</div>
<div class="anchor-list" style="color:rgb(51,51,51);font-family:arial, '宋体', sans-serif;">
<a name="1_2" class="lemma-anchor para-title" style="color:rgb(19,110,194);"></a><a name="sub9600530_1_2" class="lemma-anchor" style="color:rgb(19,110,194);"></a><a name="%E6%95%B0%E6%8D%AE%E5%A4%84%E7%90%86" class="lemma-anchor" style="color:rgb(19,110,194);"></a></div>
<div class="para-title level-3" style="color:rgb(51,51,51);clear:both;line-height:20px;font-size:18px;font-family:'Microsoft YaHei', SimHei, Verdana;">
<h3 class="title-text" style="font-size:18px;font-weight:400;">
数据处理</h3>
</div>
<div class="para" style="color:rgb(51,51,51);font-size:14px;text-indent:2em;line-height:24px;font-family:arial, '宋体', sans-serif;">
Flume提供对数据进行简单处理，并写到各种数据接受方（可定制）的能力 。Flume提供了从console（控制台）、RPC（Thrift-RPC）、text（文件）、tail（UNIX tail）、syslog（syslog日志系统），支持TCP和UDP等2种模式），exec（命令执行）等数据源上收集数据的能力。</div>
<div class="anchor-list" style="color:rgb(51,51,51);font-family:arial, '宋体', sans-serif;">
<a name="2" class="lemma-anchor para-title" style="color:rgb(19,110,194);"></a><a name="sub9600530_2" class="lemma-anchor" style="color:rgb(19,110,194);"></a><a name="%E5%B7%A5%E4%BD%9C%E6%96%B9%E5%BC%8F" class="lemma-anchor" style="color:rgb(19,110,194);"></a></div>
<div class="para-title level-2" style="color:rgb(51,51,51);clear:both;overflow:hidden;border-left:12px solid rgb(79,156,238);line-height:24px;font-size:22px;font-family:'Microsoft YaHei', SimHei, Verdana;">
<h2 class="title-text" style="font-size:22px;color:rgb(0,0,0);line-height:24px;font-weight:400;">
工作方式</h2>
<a class="edit-icon j-edit-link" style="color:rgb(136,136,136);display:block;line-height:22px;font-size:12px;font-family:SimSun;"><span class="cmn-icon wiki-lemma-icons wiki-lemma-icons_edit-lemma"></span>编辑</a></div>
<div class="para" style="color:rgb(51,51,51);font-size:14px;text-indent:2em;line-height:24px;font-family:arial, '宋体', sans-serif;">
Flume-og采用了多Master的方式。为了保证配置数据的一致性，Flume引入了ZooKeeper，用于保存配置数据，ZooKeeper本身可保证配置数据的一致性和高可用，另外，在配置数据发生变化时，ZooKeeper可以通知Flume Master节点。Flume Master间使用gossip协议同步数据。</div>
<div class="para" style="color:rgb(51,51,51);font-size:14px;text-indent:2em;line-height:24px;font-family:arial, '宋体', sans-serif;">
Flume-ng最明显的改动就是取消了集中管理配置的 Master 和 Zookeeper，变为一个纯粹的传输工具。Flume-ng另一个主要的不同点是读入数据和写出数据现在由不同的工作线程处理（称为 Runner）。 在 Flume-og 中，读入线程同样做写出工作（除了故障重试）。如果写出慢的话（不是完全失败），它将阻塞 Flume 接收数据的能力。这种异步的设计使读入线程可以顺畅的工作而无需关注下游的任何问题。</div>
<div class="para" style="font-size:14px;text-indent:2em;line-height:24px;font-family:arial, '宋体', sans-serif;">
<h2 class="title-text" style="color:rgb(51,51,51);font-family:'Microsoft YaHei', SimHei, Verdana;text-indent:28px;font-size:22px;line-height:24px;font-weight:400;">
source的类型：</h2>
<div style="color:rgb(51,51,51);">
<p style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;">
 1、avro source</p>
<p style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;">
   侦听Avro端口并从外部Avro客户端流接收事件。 当与另一个（上一跳）Flume代理上的内置Avro Sink配对时，它可以创建分层集合拓扑。 </p>
<span><span style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;"> 2、thrif source</span></span>
<p style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;">
</p>
<p style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;">
        监听Thrift端口并从外部Thrift客户端流接收事件。 当与另一（前一跳）Flume代理上的内置ThriftSink配对时，它可以创建分层集合拓扑。 Thrift源可以配置为通过启用kerberos身份验证在安全模式下启动。 agent-principal和agent-keytab是Thrift源用来向kerberos KDC进行身份验证的属性。</p>
</div>
<span><span style="color:#333333;"></span></span>
<p style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;">
 3、exec  source</p>
<p style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;">
       Exec源在启动时运行给定的Unix命令，并期望该进程在标准输出上连续产生数据（除非属性logStdErr设置为true，否则stderr将被丢弃）。 如果进程由于任何原因退出，源也会退出，并且不会产生进一步的数据。 这意味着诸如cat [named pipe]或tail -F [file]之类的配置将产生期望的结果，其中日期可能不会 - 前两个命令产生数据流，其中后者产生单个事件并退出。</p>
<p style="font-family:'microsoft yahei';font-size:15px;">
</p>
<p style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;">
  4、spooling directory source</p>
<p style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;">
       此源允许您通过将要提取的文件放入磁盘上的“spooling”目录中来提取数据。此源将监视新文件的指定目录，并在新文件显示时解析新文件中的事件。事件解析逻辑是可插入的。在给定文件被完全读入通道之后，它被重命名以指示完成（或可选地被删除）。<br>
与Exec源不同，该源是可靠的，并且不会错过数据，即使Flume被重新启动或被杀死。为了换取这种可靠性，只有不可变，唯一命名的文件必须放入spooling目录中。 Flume尝试检测这些问题条件，如果违反则会大声失败：<br>
如果在放入spooling目录后写入文件，Flume将在其日志文件中打印一个错误并停止处理。<br>
如果以后重新使用文件名，Flume会在其日志文件中打印一个错误并停止处理。</p>
<span><span style="color:#555555;"></span><span style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;">  5、kafka source</span></span>
<p style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;">
       Kafka Source是一个从Kafka主题读取消息的Apache Kafka消费者。 如果您有多个Kafka源运行，您可以使用相同的Consumer Group配置它们，这样每个都将为主题读取一组唯一的分区。</p>
<p style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;">
下面是笔者在实际应用中用到的一种avro类型resource：</p>
<p style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;">
需要配置两个配置文件：</p>
<p style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;">
1.</p>
<p style="font-family:'microsoft yahei';font-size:15px;">
<span style="color:#555555;">2.</span><span style="color:rgb(85,85,85);"></span></p>
<div><span style="color:#555555;">-----------------------------------</span><span style="color:#ff0000;"></span><span style="color:#555555;">
 ------------------------</span></div>
<div><span style="color:rgb(85,85,85);text-indent:2em;">## define agent source、 channel、 sinks、name</span></div>
<div><span style="color:#555555;">a1.sources = s1<br>
a1.channels = c1<br>
a1.sinks = k1<br>
## define sources<br>
a1.sources.s1.type = exec<br>
a1.sources.s1.command = tail -F /usr/local/nginx/datalog/access.log<br>
a1.sources.s1.shell = /bin/bash -c<br><br>
## define channels<br>
a1.channels.c1.type = memory<br>
a1.channels.c1.capacity = 1000<br>
a1.channels.c1.transactionCapacity = 100<br><br>
## define sinks<br>
a1.sinks.k1.type = avro<br>
a1.sinks.k1.hostname = 192.168.134.101<br>
a1.sinks.k1.port = 4545<br><br>
## 关联 sources和 sinks<br>
a1.sources.s1.channels = c1<br>
a1.sinks.k1.channel = c1<br></span></div>
<div><span style="color:#555555;">--------------------------------</span><span style="color:#ff0000;"></span><span style="color:#555555;">
 ------------------------------------</span></div>
<div><span style="color:#555555;">## define agent source、 channel、 sinks、name<br>
a2.sources = s2<br>
a2.channels = c2<br>
a2.sinks = k2<br><br>
## define sources<br>
a2.sources.s2.type = avro<br>
a2.sources.s2.bind = 192.168.134.101<br>
a2.sources.s2.port = 4545<br><br>
## define channels<br>
a2.channels.c2.type = memory<br>
a2.channels.c2.capacity = 1000<br>
a2.channels.c2.transactionCapacity = 100<br><br>
## define sinks<br>
a2.sinks.k2.type = hdfs<br>
a2.sinks.k2.hdfs.path = hdfs://192.168.134.101:8020/flume/%Y%m%d<br>
a2.sinks.k2.hdfs.filePrefix = access_log<br><br>
## 设置目录按照年/月/日进行回滚<br>
a2.sinks.k2.hdfs.round = true<br>
a2.sinks.k2.hdfs.roundValue = 1<br>
a2.sinks.k2.hdfs.roundUnit = day<br><br>
## 解决生产很多小文件,设置文件回滚的条件<br>
a2.sinks.k2.hdfs.rollInterval = 0<br>
a2.sinks.k2.hdfs.rollSize = 134217728<br>
a2.sinks.k2.hdfs.rollCount = 0<br><br>
## 这个参数必须设置，不然上面的回滚设置不生效<br>
a2.sinks.k2.hdfs.minBlockReplicas = 1<br><br>
a2.sinks.k2.hdfs.batchSize = 1000<br>
a2.sinks.k2.hdfs.fileType = DataStream<br>
a2.sinks.k2.hdfs.writeFormat = Text<br>
a2.sinks.k2.hdfs.useLocalTimeStamp = true <br><br>
## 关联 sources和 sinks<br>
a2.sources.s2.channels = c2<br>
a2.sinks.k2.channel = c2<br></span></div>
<div><span style="color:#555555;">---------------------------------------------------------------------------</span></div>
<div><span style="color:#555555;"></span>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:22px;">两个文档分别是</span><span style="font-size:22px;color:rgb(223,64,42);">flume</span><span style="font-size:22px;">执行时所需的配置文件，文档一收集的日志放到</span><span style="font-size:22px;color:rgb(223,64,42);">4545</span><span style="font-size:22px;">端口（该端口为自定义端口），做为文档2的</span><span style="font-size:22px;color:rgb(223,64,42);">source</span><span style="font-size:22px;">，文档2收集的数据在存放在</span><span style="font-size:22px;color:rgb(223,64,42);">hdfs</span><span style="font-size:22px;">上面。</span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:22px;">应用场景：</span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:22px;">一共五台服务器，每台机子上面都有</span><span style="font-size:22px;color:rgb(223,64,42);">flume</span><span style="font-size:22px;">、</span><span style="font-size:22px;color:rgb(223,64,42);">nginx</span><span style="font-size:22px;">；这五台服务器的</span><span style="font-size:22px;color:rgb(223,64,42);">nginx</span><span style="font-size:22px;">服务器产生的日志都回桶过文档一的配置收集在一起，第二个配置在这五台中的任意一台启动</span><span style="font-size:22px;color:rgb(223,64,42);">application</span><span style="font-size:22px;">，负责收集4545端口的数据（既五台</span><span style="font-size:22px;color:rgb(223,64,42);">agent</span><span style="font-size:22px;">收集到一起的数据），作为</span><span style="font-size:22px;color:rgb(223,64,42);">source</span><span style="font-size:22px;">，最后存放在hdfs上面。</span></div>
<br></div>
<p></p>
</div>
<p></p>
            </div>
                </div>