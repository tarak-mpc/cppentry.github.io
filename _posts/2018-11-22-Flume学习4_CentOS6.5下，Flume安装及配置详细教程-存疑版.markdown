---
layout:     post
title:      Flume学习4_CentOS6.5下，Flume安装及配置详细教程-存疑版
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p></p>
<p>CentOS6.5下，Flume安装及配置详细教程</p>
<p> </p>
<p>下载地址：</p>
<p><a href="http://mirror.bit.edu.cn/apache/flume/1.5.0.1/apache-flume-1.5.0.1-bin.tar.gz" rel="nofollow"><span style="color:rgb(16,50,107);">http://mirror.bit.edu.cn/apache/flume/1.5.0.1/apache-flume-1.5.0.1-bin.tar.gz</span></a></p>
<p> </p>
<p>安装：</p>
<p>cd /usr/local</p>
<p>打开local目录</p>
<p>wget http://mirror.bit.edu.cn/apache/flume/1.5.0.1/apache-flume-1.5.0.1-bin.tar.gz</p>
<p>使用wget命令，安装软件（适合知道下载链接时）</p>
<p>tar xvzf apache-flume-1.5.0.1-bin.tar.gz</p>
<p>解压</p>
<p>mv apache-flume-1.5.0.1-bin flume</p>
<p>改成一个简单的名字</p>
<p>cd flume</p>
<p>打开flume</p>
<p> </p>
<p>修改 flume-env.sh 配置文件:</p>
<p>cd /usr/local/flume</p>
<p>打开flume目录</p>
<p>cp conf/flume-env.sh.template conf/flume-env.sh</p>
<p>复制一个，作为模板</p>
<p> vim conf/flume-env.sh</p>
<p>编辑，目的是加上JAVA_HOME，编辑的结果如下所示：</p>
<p>JAVA_HOME=/usr/local/java/jdk1.7.0_51</p>
<p>/usr/local/flume/bin/flume-ng version</p>
<p>检验</p>
<p> </p>
<p>显示如下信息：</p>
<p>[root@Master flume]# /usr/local/flume/bin/flume-ng version</p>
<p>Flume 1.5.0.1</p>
<p>Source code repository: https://git-wip-us.apache.org/repos/asf/flume.git</p>
<p>Revision: ceda6aa1126a01370641caf729d8b1dd6d80aa61</p>
<p>Compiled by hshreedharan on Tue Jun 10 15:12:04 PDT 2014</p>
<p>From source with checksum 9500a0fd0084429fe7b0ecf18132be2b</p>
<p>[root@Master flume]# </p>
<p>表示成功</p>
<p> </p>
<p> </p>
<p>通过一些实际的配置实例，来了解Flume的使用：</p>
<p> </p>
<p>案例一：</p>
<p>Avro Source+Memory Channel+Logger Sink:</p>
<p> </p>
<p>使用apache-flume-1.5.0.1自带的例子，使用Avro Source接收外部数据源，Logger作为sink，即通过Avro RPC调用，将数据缓存在channel中，然后通过Logger打印出调用发送的数据。</p>
<p> </p>
<p>配置Agent，修改配置文件conf/flume-conf.properties，内容如下：</p>
<p>cd /usr/local/flume/conf</p>
<p>打开文件所在目录</p>
<p>vim flume-conf.properties</p>
<p>编辑文件</p>
<p> </p>
<p>内容编辑为：</p>
<p># Define a memory channel called ch1 on agent1</p>
<p>agent1.channels.ch1.type = memory</p>
<p> </p>
<p># Define an Avro source called avro-source1 on agent1 and tell it</p>
<p># to bind to 0.0.0.0:41414. Connect it to channel ch1.</p>
<p>agent1.sources.avro-source1.channels = ch1</p>
<p>agent1.sources.avro-source1.type = avro</p>
<p>agent1.sources.avro-source1.bind = 0.0.0.0</p>
<p>agent1.sources.avro-source1.port = 41414</p>
<p> </p>
<p># Define a logger sink that simply logs all events it receives</p>
<p># and connect it to the other end of the same channel.</p>
<p>agent1.sinks.log-sink1.channel = ch1</p>
<p>agent1.sinks.log-sink1.type = logger</p>
<p> </p>
<p># Finally, now that we've defined all of our components, tell</p>
<p># agent1 which ones we want to activate.</p>
<p>agent1.channels = ch1</p>
<p>agent1.channels.ch1.capacity = 1000</p>
<p>agent1.sources = avro-source1</p>
<p>agent1.sinks = log-sink1</p>
<p> </p>
<p>启动Agent进程：</p>
<p>cd /usr/local/flume</p>
<p>打开<span style="font-family:'Times New Roman';">flume</span><span style="font-family:'宋体';">目录</span></p>
<p>bin/flume-ng agent -c ./conf/ -f conf/flume-conf.properties -Dflume.root.logger=DEBUG,console -n agent1</p>
<p> </p>
<p>报错：</p>
<p>Failed to bind to :/0.0.0.0:41414</p>
<p> </p>
<p>启动Avro Client，发送数据：</p>
<p>cd /usr/local/flume</p>
<p>打开<span style="font-family:'Times New Roman';">flume</span><span style="font-family:'宋体';">目录</span></p>
<p>bin/flume-ng avro-client -c ./conf/ -H 0.0.0.0 -p 41414 -F<span style="color:#ff0000;"> /usr/local/programs/logs/sync.log </span>-Dflume.root.logger=DEBUG,console</p>
<p> </p>
<p>报错：</p>
<p>2015-09-13 23:04:20,532 (main) [ERROR - org.apache.flume.client.avro.AvroCLIClient.main(AvroCLIClient.java:77)] Unable to send data to Flume. Exception follows.</p>
<p>java.io.FileNotFoundException: <span style="color:#ff0000;">/usr/local/programs/logs/sync.log</span> (No such file or directory)</p>
<p><br></p>
<p><span style="color:#ff0000;">这个文件不存在，需要自己再配置！</span></p>
<p> </p>
<p>案例二：</p>
<p>案例：Avro</p>
<p>cd /usr/local/flume</p>
<p>打开flume目录</p>
<p>vim conf/avro.conf</p>
<p>编辑文件</p>
<p>加入以下内容：</p>
<p> </p>
<p>a1.sources = r1</p>
<p>a1.sinks = k1</p>
<p>a1.channels = c1</p>
<p>  </p>
<p># Describe/configure the source</p>
<p>a1.sources.r1.type = avro</p>
<p>a1.sources.r1.channels = c1</p>
<p>a1.sources.r1.bind = 0.0.0.0</p>
<p>a1.sources.r1.port = 4141</p>
<p>  </p>
<p># Describe the sink</p>
<p>a1.sinks.k1.type = logger</p>
<p>  </p>
<p># Use a channel which buffers events in memory</p>
<p>a1.channels.c1.type = memory</p>
<p>a1.channels.c1.capacity = 1000</p>
<p>a1.channels.c1.transactionCapacity = 100</p>
<p>  </p>
<p># Bind the source and sink to the channel</p>
<p>a1.sources.r1.channels = c1</p>
<p>a1.sinks.k1.channel = c1</p>
<p> </p>
<p>启动flume agent a1：</p>
<p>/usr/local/flume/bin/flume-ng agent -c . -f /usr/local/flume/conf/avro.conf -n a1 -Dflume.root.logger=INFO,console</p>
<p> </p>
<p>创建指定文件：</p>
<p>echo "hello world" &gt; /usr/local/flume/log.00</p>
<p> </p>
<p>使用avro-client发送文件：</p>
<p>/usr/local/flume/bin/flume-ng avro-client -c . -H m1 -p 4141 -F /usr/local/flume/log.00</p>
<p> </p>
<p>报错：</p>
<p>15/09/14 05:45:54 WARN api.NettyAvroRpcClient: Using default maxIOWorkers</p>
<p>15/09/14 05:45:55 ERROR avro.AvroCLIClient: Unable to open connection to Flume. Exception follows.</p>
<p>org.apache.flume.FlumeException: NettyAvroRpcClient { host: m1, port: 4141 }: RPC connection error</p>
<p><br></p>
<p> </p>
<p>Avro Source+Memory Channel+HDFS Sink:</p>
<p> </p>
<p>Spooling Directory Source+Memory Channel+HDFS Sink:</p>
<p> </p>
<p>Exec Source+Memory Channel+File Roll Sink:</p>
<p> </p>
<p>这三个的步骤也是：</p>
<p>配置Agent，启动Agent进程，（启动Avro Client），具体配置信息不进行列举。</p>
<br>            </div>
                </div>