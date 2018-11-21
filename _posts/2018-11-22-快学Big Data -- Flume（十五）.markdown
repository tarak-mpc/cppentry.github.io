---
layout:     post
title:      快学Big Data -- Flume（十五）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/xfg0218/article/details/82343547				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h1><strong><a name="_Toc14160"></a><strong><strong><a name="_Toc32134">F</a></strong></strong><strong><strong>lume 总结</strong></strong></strong></h1>

<p style="margin-left:0pt;"><img alt="" class="has" height="406" src="https://img-blog.csdn.net/20180903083420115?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3hmZzAyMTg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="734"></p>

<p style="margin-left:0pt;">官网：<a href="http://flume.apache.org/FlumeUserGuide.html" rel="nofollow"><u><span style="color:#0000ff;"><u>http://flume.apache.org/FlumeUserGuide.html</u></span></u></a></p>

<p style="margin-left:0pt;"> </p>

<h2><strong><a name="_Toc5771"></a><strong><strong><a name="_Toc14262">概述</a></strong></strong></strong></h2>

<p style="margin-left:0pt;">Flume是一个分布式、可靠、和高可用的海量日志采集、聚合和传输的系统。</p>

<p style="margin-left:0pt;">Flume可以采集文件，socket数据包等各种形式源数据，又可以将采集到的数据输出到HDFS、hbase、hive、kafka等众多外部存储系统中</p>

<p style="margin-left:0pt;">一般的采集需求，通过对flume的简单配置即可实现</p>

<p style="margin-left:0pt;">Flume针对特殊场景也具备良好的自定义扩展能力，因此，flume可以适用于大部分的日常数据采集场景</p>

<h2><strong><a name="_Toc25787"></a><strong><strong><a name="_Toc19530">运行机制</a></strong></strong></strong></h2>

<p style="margin-left:0pt;">1、Flume分布式系统中最核心的角色是agent，flume采集系统就是由一个个agent所连接起来形成</p>

<p style="margin-left:0pt;">2、每一个agent相当于一个数据传递员，内部有三个组件：</p>

<p style="margin-left:0pt;">a)Source：采集源，用于跟数据源对接，以获取数据</p>

<p style="margin-left:0pt;">b)Sink：下沉地，采集数据的传送目的，用于往下一级agent传递数据或者往最终存储系统传递数据</p>

<p style="margin-left:0pt;">c)Channel：angent内部的数据传输通道，用于从source将数据传递到sink</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;">注：</p>

<p style="margin-left:0pt;">Source 到 Channel 到 Sink之间传递数据的形式是Event事件；Event事件是一个数据流单元。</p>

<p style="margin-left:0pt;">Source  +  sink   +  channel   =  Agent  </p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;">单个Agent采集</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;">多个Agent的实例</p>

<p style="margin-left:0pt;"></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;">多路（<span style="color:#000000;">Multiplexing</span>）Agent</p>

<p style="margin-left:0pt;"></p>

<h2><strong><a name="_Toc32346"></a><strong><strong><a name="_Toc16814">架构设计要点</a></strong></strong></strong></h2>

<p style="margin-left:0pt;"><span style="color:#000000;">Flume的架构主要有一下几个核心概念：</span></p>

<p style="margin-left:0pt;"><span style="color:#000000;">Event：一个数据单元，带有一个可选的消息头</span></p>

<p style="margin-left:0pt;"><span style="color:#000000;">Flow：Event从源点到达目的点的迁移的抽象</span></p>

<p style="margin-left:0pt;"><span style="color:#000000;">Client：操作位于源点处的Event，将其发送到Flume Agent</span></p>

<p style="margin-left:0pt;"><span style="color:#000000;">Agent：一个独立的Flume进程，包含组件Source、Channel、Sink</span></p>

<p style="margin-left:0pt;"><span style="color:#000000;">Source：用来消费传递到该组件的Event</span></p>

<p style="margin-left:0pt;"><span style="color:#000000;">Channel：中转Event的一个临时存储，保存有Source组件传递过来的Event</span></p>

<p style="margin-left:0pt;"><span style="color:#000000;">Sink：从Channel中读取并移除Event，将Event传递到Flow Pipeline中的下一个Agent（如果有的话）</span></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"> </p>

<h2><strong><a name="_Toc29990"></a><strong><strong><a name="_Toc10958">Flume安装</a></strong></strong></strong></h2>

<p style="margin-left:0pt;">安装包下载：</p>

<p style="margin-left:0pt;">链接：http://pan.baidu.com/s/1miycTfU 密码：2ea4 如果无法下载请联系作者。</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"> </p>

<h3><strong><a name="_Toc11259"></a><strong><strong><a name="_Toc5309">1-1）、安装</a></strong></strong></strong></h3>

<p style="margin-left:0pt;">[root@hadoop1 /]#  tar -zxvf apache-flume-1.6.0-bin.tar.gz</p>

<p style="margin-left:0pt;">[root@hadoop1 /]#  mv apache-flume-1.6.0-bin flume</p>

<p style="margin-left:0pt;">[root@hadoop1 /]#  cd /flume/conf/</p>

<p style="margin-left:0pt;">[root@hadoop1 /]#  mv flume-env.sh.template  flume-env.sh</p>

<h3><strong><a name="_Toc27000"></a><strong><strong><a name="_Toc32053">1-2）、修改配置文件</a></strong></strong></strong></h3>

<p style="margin-left:0pt;">[root@hadoop1 /]#  vi flume-env.sh    </p>

<p style="margin-left:0pt;"> ---- 修改JAVA_HOME的路径</p>

<p style="margin-left:0pt;"> </p>

<h3><strong><a name="_Toc18910"></a><strong><strong><a name="_Toc13441">1-3）、添加快捷方式</a></strong></strong></strong></h3>

<p style="margin-left:0pt;">[root@hadoop1 /]#  Vi /etc/profile</p>

<p style="margin-left:0pt;">export FLUME_HOME=/usr/local/flume</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;">[root@hadoop1 ~]# source /etc/profile</p>

<h3><strong><a name="_Toc2629"></a><strong><strong><a name="_Toc10547">1-4）、常见的命令</a></strong></strong></strong></h3>

<p style="margin-left:0pt;">[root@hadoop1 flume]# flume-ng -help</p>

<p style="margin-left:0pt;">Error: Unknown or unspecified command '-help'</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;">Usage: /usr/local/flume/bin/flume-ng &lt;command&gt; [options]...</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;">commands:</p>

<p style="margin-left:0pt;">  help                      display this help text</p>

<p style="margin-left:0pt;">  agent                     run a Flume agent</p>

<p style="margin-left:0pt;">  avro-client               run an avro Flume client</p>

<p style="margin-left:0pt;">  version                   show Flume version info</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;">global options:</p>

<p style="margin-left:0pt;">  --conf,-c &lt;conf&gt;          use configs in &lt;conf&gt; directory</p>

<p style="margin-left:0pt;">  --classpath,-C &lt;cp&gt;       append to the classpath</p>

<p style="margin-left:0pt;">  --dryrun,-d               do not actually start Flume, just print the command</p>

<p style="margin-left:0pt;">  --plugins-path &lt;dirs&gt;     colon-separated list of plugins.d directories. See the</p>

<p style="margin-left:0pt;">                            plugins.d section in the user guide for more details.</p>

<p style="margin-left:0pt;">                            Default: $FLUME_HOME/plugins.d</p>

<p style="margin-left:0pt;">  -Dproperty=value          sets a Java system property value</p>

<p style="margin-left:0pt;">  -Xproperty=value          sets a Java -X option</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;">agent options:</p>

<p style="margin-left:0pt;">  --name,-n &lt;name&gt;          the name of this agent (required)</p>

<p style="margin-left:0pt;">  --conf-file,-f &lt;file&gt;     specify a config file (required if -z missing)</p>

<p style="margin-left:0pt;">  --zkConnString,-z &lt;str&gt;   specify the ZooKeeper connection to use (required if -f missing)</p>

<p style="margin-left:0pt;">  --zkBasePath,-p &lt;path&gt;    specify the base path in ZooKeeper for agent configs</p>

<p style="margin-left:0pt;">  --no-reload-conf          do not reload config file if changed</p>

<p style="margin-left:0pt;">  --help,-h                 display help text</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;">avro-client options:</p>

<p style="margin-left:0pt;">  --rpcProps,-P &lt;file&gt;   RPC client properties file with server connection params</p>

<p style="margin-left:0pt;">  --host,-H &lt;host&gt;       hostname to which events will be sent</p>

<p style="margin-left:0pt;">  --port,-p &lt;port&gt;       port of the avro source</p>

<p style="margin-left:0pt;">  --dirname &lt;dir&gt;        directory to stream to avro source</p>

<p style="margin-left:0pt;">  --filename,-F &lt;file&gt;   text file to stream to avro source (default: std input)</p>

<p style="margin-left:0pt;">  --headerFile,-R &lt;file&gt; File containing event headers as key/value pairs on each new line</p>

<p style="margin-left:0pt;">  --help,-h              display help text</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;">  Either --rpcProps or both --host and --port must be specified.</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;">Note that if &lt;conf&gt; directory is specified, then it is always included first</p>

<p style="margin-left:0pt;">in the classpath.</p>

<p style="margin-left:0pt;"> </p>

<h3><strong><a name="_Toc30027"></a><strong><strong><a name="_Toc24505">1-5）、启动程序</a></strong></strong></strong></h3>

<p><strong><a name="_Toc10516"></a><strong><strong><a name="_Toc25767">A）、前段启动</a></strong></strong></strong></p>

<p style="margin-left:0pt;">flume-ng agent -c conf -f  netcat-logger.conf  -n a1  -Dflume.root.logger=INFO,console</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;">-flume-ng agent    run a Flume agent</p>

<p style="margin-left:0pt;">-c conf   指定flume自身的配置文件所在目录</p>

<p style="margin-left:0pt;">-f conf/netcat-logger.con  指定我们所描述的采集方案</p>

<p style="margin-left:0pt;">-n a1  指定我们这个agent的名字</p>

<p style="margin-left:0pt;">-Dflume.root.logger=INFO,console 显示日志打印的方式</p>

<p style="margin-left:0pt;"> </p>

<p><strong><a name="_Toc1737"></a><strong><strong><a name="_Toc9236">B）、后端启动</a></strong></strong></strong></p>

<p style="margin-left:0pt;">flume-ng agent -c conf -f  netcat-logger.conf  -n a1</p>

<h2><strong><a name="_Toc15243"></a><strong><strong><a name="_Toc18181">Flume 运行实例</a></strong></strong></strong></h2>

<h3><strong><a name="_Toc10260"></a><strong><strong><a name="_Toc530">1-1）、本地控制台案例</a></strong></strong></strong></h3>

<p style="margin-left:0pt;">Flume支持众多的source和sink类型，详细手册可参考官方文档</p>

<p style="margin-left:0pt;"><a href="http://flume.apache.org/FlumeUserGuide.html" rel="nofollow"><u><span style="color:#0000ff;"><u>http://flume.apache.org/FlumeUserGuide.html</u></span></u></a></p>

<p><strong><a name="_Toc21701"></a><strong><strong><a name="_Toc12783">A）、配置</a></strong></strong></strong></p>

<p style="margin-left:0pt;"> 可以现在conf的目录下新建文件nicate-logger.conf</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"> [root@hadoop1 conf]#  vi  nicate-logger.conf</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># 定义这个agent中各组件的名字</p>

<p style="margin-left:0pt;">a1.sources = r1  </p>

<p style="margin-left:0pt;">a1.sinks = k1</p>

<p style="margin-left:0pt;">a1.channels = c1  </p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># 描述和配置source组件：r1</p>

<p style="margin-left:0pt;">a1.sources.r1.type = netcat    </p>

<p style="margin-left:0pt;">a1.sources.r1.bind = localhost</p>

<p style="margin-left:0pt;">a1.sources.r1.port = 44444</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># 描述和配置sink组件：k1</p>

<p style="margin-left:0pt;">a1.sinks.k1.type = logger</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># 描述和配置channel组件，此处使用是内存缓存的方式</p>

<p style="margin-left:0pt;">a1.channels.c1.type = memory</p>

<p style="margin-left:0pt;">a1.channels.c1.capacity = 1000</p>

<p style="margin-left:0pt;">a1.channels.c1.transactionCapacity = 100</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># 描述和配置source  channel   sink之间的连接关系</p>

<p style="margin-left:0pt;">a1.sources.r1.channels = c1</p>

<p style="margin-left:0pt;">a1.sinks.k1.channel = c1</p>

<p><strong><a name="_Toc11206"></a><strong><strong><a name="_Toc11576">B）、启动flume-ng</a></strong></strong></strong></p>

<p style="margin-left:0pt;">[root@hadoop1 conf]# flume-ng agent -c conf -f /usr/local/flume/conf/nicate-logger.conf   -n a1  -Dflume.root.logger=INFO,console</p>

<p style="margin-left:0pt;">Info: <span style="color:#ff0000;">Including Hadoop libraries found via (/usr/local/hadoop-2.6.4/bin/hadoop) for HDFS access</span></p>

<p style="margin-left:0pt;">Info: Excluding /usr/local/hadoop-2.6.4/share/hadoop/common/lib/slf4j-api-1.7.5.jar from classpath</p>

<p style="margin-left:0pt;">Info: Excluding /usr/local/hadoop-2.6.4/share/hadoop/common/lib/slf4j-log4j12-1.7.5.jar from classpath</p>

<p style="margin-left:0pt;">Info: Including Hive libraries found via () for Hive access</p>

<p style="margin-left:0pt;">+ exec <span style="color:#ff0000;">/home/jdk1.7/bin/java -Xmx20m -Dflume.root.logger=INFO,</span>console -cp 'conf:/usr/local/flume/lib/*:/usr/local/hadoop-2.6.4/etc/hadoop:/usr/local/hadoop-2.6.4/share/hadoop/common/lib/activation-1.1.jar:/usr/local/hadoop-2.6.4/share/hadoop/common/lib/apacheds-i18n-2.0.0-M15.jar:/usr/local/hadoop-2.6.4/share/hadoop/common/lib/apacheds-kerberos...................省略其他的加载配置............usr/local/hadoop-2.6.4/share/hadoop/mapreduce/lib:/usr/local/hadoop-2.6.4/share/hadoop/mapreduce/lib-examples:/usr/local/hadoop-2.6.4/share/hadoop/mapreduce/sources:/usr/local/hadoop-2.6.4/contrib/capacity-scheduler/*.jar:/lib/*' -Djava.library.path=:/usr/local/hadoop-2.6.4/lib/native <span style="color:#ff0000;">org.apache.flume.node.Application -f /usr/local/flume/conf/nicate-logger.conf -n a1</span></p>

<p style="margin-left:0pt;">16/09/25 10:53:34 INFO node.PollingPropertiesFileConfigurationProvider: Configuration provider starting</p>

<p style="margin-left:0pt;"><span style="color:#ff0000;">16/09/25 10:53:34 INFO node.PollingPropertiesFileConfigurationProvider: Reloading configuration file:/usr/local/flume/conf/nicate-logger.conf</span></p>

<p style="margin-left:0pt;"><span style="color:#ff0000;">16/09/25 10:53:34 INFO conf.FlumeConfiguration: Added sinks: k1 Agent: a1</span></p>

<p style="margin-left:0pt;"><span style="color:#ff0000;">16/09/25 10:53:34 INFO conf.FlumeConfiguration: Processing:k1</span></p>

<p style="margin-left:0pt;"><span style="color:#ff0000;">16/09/25 10:53:34 INFO conf.FlumeConfiguration: Processing:k1</span></p>

<p style="margin-left:0pt;">16/09/25 10:53:34 INFO conf.FlumeConfiguration: Post-validation flume configuration contains configuration for agents: [a1]</p>

<p style="margin-left:0pt;">16/09/25 10:53:34 INFO node.AbstractConfigurationProvider: Creating channels</p>

<p style="margin-left:0pt;">16/09/25 10:53:34 INFO channel.DefaultChannelFactory: Creating instance of channel c1 type memory</p>

<p style="margin-left:0pt;">16/09/25 10:53:34 INFO node.AbstractConfigurationProvider: Created channel c1</p>

<p style="margin-left:0pt;">16/09/25 10:53:34 INFO source.DefaultSourceFactory: Creating instance of source r1, type netcat</p>

<p style="margin-left:0pt;">16/09/25 10:53:34 INFO sink.DefaultSinkFactory: Creating instance of sink: k1, type: logger</p>

<p style="margin-left:0pt;">16/09/25 10:53:34 INFO node.AbstractConfigurationProvider: Channel c1 connected to [r1, k1]</p>

<p style="margin-left:0pt;">16/09/25 10:53:34 INFO node.Application: Starting new configuration:{ sourceRunners:{r1=EventDrivenSourceRunner: { source:org.apache.flume.source.NetcatSource{name:r1,state:IDLE} }} sinkRunners:{k1=SinkRunner: { policy:org.apache.flume.sink.DefaultSinkProcessor@6c62aa33 counterGroup:{ name:null counters:{} } }} channels:{c1=org.apache.flume.channel.MemoryChannel{name: c1}} }</p>

<p style="margin-left:0pt;">16/09/25 10:53:34 INFO node.Application: Starting Channel c1</p>

<p style="margin-left:0pt;">16/09/25 10:53:34 INFO instrumentation.MonitoredCounterGroup: Monitored counter group for type: CHANNEL, name: c1: Successfully registered new MBean.</p>

<p style="margin-left:0pt;">16/09/25 10:53:34 INFO instrumentation.MonitoredCounterGroup: Component type: CHANNEL, name: c1 started</p>

<p style="margin-left:0pt;"><span style="color:#ff0000;">16/09/25 10:53:34 INFO node.Application: Starting Sink k1</span></p>

<p style="margin-left:0pt;"><span style="color:#ff0000;">16/09/25 10:53:34 INFO node.Application: Starting Source r1</span></p>

<p style="margin-left:0pt;">16/09/25 10:53:34 INFO source.NetcatSource: Source starting</p>

<p style="margin-left:0pt;"><span style="color:#ff0000;">16/09/25 10:53:35 INFO source.NetcatSource: Created serverSocket:sun.nio.ch.ServerSocketChannelImpl[/127.0.0.1:44444]</span></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;">之所以特出来启动信息，为了详细的显示了加载配置的信息。</p>

<p><strong><a name="_Toc20733"></a><strong><strong><a name="_Toc13224">C）、使用telnet 测试</a></strong></strong></strong></p>

<p style="margin-left:0pt;">安装telnet ：</p>

<p style="margin-left:0pt;">[root@hadoop1 flume]# yum list | grep telnet</p>

<p style="margin-left:0pt;">telnet.x86_64                              1:0.17-48.el6                 base   </p>

<p style="margin-left:0pt;">telnet-server.x86_64                       1:0.17-48.el6                 base</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;">[root@hadoop1 flume]# yum install telnet.x86_64</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;">测试：</p>

<p style="margin-left:0pt;">[root@hadoop1 ~]# telnet 127.0.0.1 44444</p>

<p style="margin-left:0pt;">Trying 127.0.0.1...</p>

<p style="margin-left:0pt;">Connected to 127.0.0.1.</p>

<p style="margin-left:0pt;">Escape character is '^]'.</p>

<p style="margin-left:0pt;">xiaozhang</p>

<p style="margin-left:0pt;">OK</p>

<p style="margin-left:0pt;">xiaowang</p>

<p style="margin-left:0pt;">OK</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;">服务器端打印的日志信息：</p>

<p style="margin-left:0pt;">16/09/25 10:53:34 INFO node.Application: Starting Sink k1</p>

<p style="margin-left:0pt;">16/09/25 10:53:34 INFO node.Application: Starting Source r1</p>

<p style="margin-left:0pt;">16/09/25 10:53:34 INFO source.NetcatSource: Source starting</p>

<p style="margin-left:0pt;">16/09/25 10:53:35 INFO source.NetcatSource: Created <span style="color:#ff0000;">serverSocket:sun.nio.ch.ServerSocketChannelImpl[/127.0.0.1:44444]</span></p>

<p style="margin-left:0pt;"><span style="color:#ff0000;">16/09/25 11:16:29 INFO sink.LoggerSink: Event: { headers:{} body: 78 69 61 6F 7A 68 61 6E 67 0D                   xiaozhang. }</span></p>

<p style="margin-left:0pt;"><span style="color:#ff0000;">16/09/25 11:16:31 INFO sink.LoggerSink: Event: { headers:{} body: 78 69 61 6F 77 61 6E 67 0D                      xiaowang. }</span></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><span style="color:#ff0000;">注意空格问题：</span></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;">在配置时要有空格如：</p>

<p style="margin-left:0pt;">a1.sources = r1  </p>

<p style="margin-left:0pt;">连接时：[root@hadoop2 ~]# telnet 127.0.0.1 44444 也要有空格</p>

<p style="margin-left:0pt;">在打印日志是显示<span style="color:#000000;">headers /  body (分为编码与内容)</span></p>

<p style="margin-left:0pt;"> </p>

<h3><strong><a name="_Toc17467"></a><strong><strong><a name="_Toc18114">1-2）、本地单机HDFS测试案例</a></strong></strong></strong></h3>

<p><strong><a name="_Toc12428"></a><strong><strong><a name="_Toc22756">A）、配置</a></strong></strong></strong></p>

<p style="margin-left:0pt;">[root@hadoop1 conf]#  vi  tail-hdfs.conf</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># tail-hdfs.conf</p>

<p style="margin-left:0pt;"># 用tail命令获取数据，下沉到hdfs</p>

<p style="margin-left:0pt;"># 启动命令：</p>

<p style="margin-left:0pt;"># bin/flume-ng agent -c conf -f conf/tail-hdfs.conf -n a1</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Name the components on this agent</p>

<p style="margin-left:0pt;">a1.sources = r1</p>

<p style="margin-left:0pt;">a1.sinks = k1</p>

<p style="margin-left:0pt;">a1.channels = c1</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Describe/configure the source</p>

<p style="margin-left:0pt;">a1.sources.r1.type = exec</p>

<p style="margin-left:0pt;"><span style="color:#ff0000;">a1.sources.r1.command = tail -F /root/logs/test.log</span></p>

<p style="margin-left:0pt;">a1.sources.r1.channels = c1</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Describe the sink</p>

<p style="margin-left:0pt;">a1.sinks.k1.type = hdfs</p>

<p style="margin-left:0pt;">a1.sinks.k1.channel = c1</p>

<p style="margin-left:0pt;"><span style="color:#ff0000;">a1.sinks.k1.hdfs.path = /flume/tailout/%y-%m-%d/%H%M/</span></p>

<p style="margin-left:0pt;">a1.sinks.k1.hdfs.filePrefix = events-</p>

<p style="margin-left:0pt;">a1.sinks.k1.hdfs.round = true</p>

<p style="margin-left:0pt;">a1.sinks.k1.hdfs.roundValue = 10</p>

<p style="margin-left:0pt;">a1.sinks.k1.hdfs.roundUnit = minute</p>

<p style="margin-left:0pt;">a1.sinks.k1.hdfs.rollInterval = 3</p>

<p style="margin-left:0pt;">a1.sinks.k1.hdfs.rollSize = 20</p>

<p style="margin-left:0pt;">a1.sinks.k1.hdfs.rollCount = 5</p>

<p style="margin-left:0pt;">a1.sinks.k1.hdfs.batchSize = 1</p>

<p style="margin-left:0pt;">a1.sinks.k1.hdfs.useLocalTimeStamp = true</p>

<p style="margin-left:0pt;">#生成的文件类型，默认是Sequencefile，可用DataStream，则为普通文本</p>

<p style="margin-left:0pt;">a1.sinks.k1.hdfs.fileType = DataStream</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Use a channel which buffers events in memory</p>

<p style="margin-left:0pt;">a1.channels.c1.type = memory</p>

<p style="margin-left:0pt;">a1.channels.c1.capacity = 1000</p>

<p style="margin-left:0pt;">a1.channels.c1.transactionCapacity = 100</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Bind the source and sink to the channel</p>

<p style="margin-left:0pt;">a1.sources.r1.channels = c1</p>

<p style="margin-left:0pt;">a1.sinks.k1.channel = c1</p>

<p><strong><a name="_Toc7447"></a><strong><strong><a name="_Toc13419">B）、测试</a></strong></strong></strong></p>

<p style="margin-left:0pt;"> [root@hadoop1 conf]#  flume-ng agent -c conf -f /use/local/flume/conf/tail-hdfs.conf   -n agnet1  -Dflume.root.logger=INFO,console</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;">16/07/30 20:29:28 INFO node.Application: Starting Channel c1</p>

<p style="margin-left:0pt;">16/07/30 20:29:28 INFO node.Application: Waiting for channel: c1 to start. Sleeping for 500 ms</p>

<p style="margin-left:0pt;">16/07/30 20:29:28 INFO instrumentation.MonitoredCounterGroup: Monitored counter group for type: CHANNEL, name: c1: Successfully registered new MBean.</p>

<p style="margin-left:0pt;">16/07/30 20:29:28 INFO instrumentation.MonitoredCounterGroup: Component type: CHANNEL, name: c1 started</p>

<p style="margin-left:0pt;">16/07/30 20:29:28 INFO node.Application: Starting Sink k1</p>

<p style="margin-left:0pt;">16/07/30 20:29:28 INFO node.Application: Starting Source r1</p>

<p style="margin-left:0pt;">16/07/30 20:29:28 INFO source.ExecSource: Exec source starting with command:tail -F /home/flumeLog/test.log</p>

<p style="margin-left:0pt;">16/07/30 20:29:28 INFO instrumentation.MonitoredCounterGroup: Monitored counter group for type: SINK, name: k1: Successfully registered new MBean.</p>

<p style="margin-left:0pt;">16/07/30 20:29:28 INFO instrumentation.MonitoredCounterGroup: Component type: SINK, name: k1 started</p>

<p style="margin-left:0pt;">16/07/30 20:29:28 INFO instrumentation.MonitoredCounterGroup: Monitored counter group for type: SOURCE, name: r1: Successfully registered new MBean.</p>

<p style="margin-left:0pt;">16/07/30 20:29:28 INFO instrumentation.MonitoredCounterGroup: Component type: SOURCE, name: r1 started</p>

<p style="margin-left:0pt;">16/07/30 20:29:32 INFO hdfs.HDFSDataStream: Serializer = TEXT, UseRawLocalFileSystem = false</p>

<p style="margin-left:0pt;">16/07/30 20:29:33 INFO hdfs.BucketWriter: Creating /flume/tailout/16-07-30/2020//events-.1469881772754.tmp</p>

<p style="margin-left:0pt;">16/07/30 20:29:38 WARN hdfs.BucketWriter: Block Under-replication detected. Rotating file.</p>

<p style="margin-left:0pt;">16/07/30 20:29:38 INFO hdfs.BucketWriter: Closing /flume/tailout/16-07-30/2020//events-.1469881772754.tmp</p>

<p style="margin-left:0pt;">16/07/30 20:29:38 INFO hdfs.BucketWriter: Renaming /flume/tailout/16-07-30/2020/events-.1469881772754.tmp to /flume/tailout/16-07-30/2020/events-.1469881772754</p>

<p style="margin-left:0pt;">16/07/30 20:29:38 INFO hdfs.BucketWriter: Creating /flume/tailout/16-07-30/2020//events-.1469881772755.tmp</p>

<p style="margin-left:0pt;">  16/07/30 20:50:22 INFO hdfs.BucketWriter: Closing <span style="color:#ff0000;">/flume/tailout/16-07-30/2050//events-.1469883004544.tmp</span></p>

<p style="margin-left:0pt;"><span style="color:#ff0000;">16/07/30 20:50:23 INFO hdfs.BucketWriter: Renaming /flume/tailout/16-07-30/2050/events-.1469883004544.tmp to /flume/tailout/16-07-30/2050/events-.1469883004544</span></p>

<p style="margin-left:0pt;"><span style="color:#ff0000;">16/07/30 20:50:25 INFO hdfs.BucketWriter: Creating /flume/tailout/16-07-30/2050//events-.1469883004545.tmp</span></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;">查看HDFS的动态信息：</p>

<p style="margin-left:0pt;">[root@hadoop1 ~]# hadoop fs -ls -R   /flume/tailout/16-07-30/</p>

<p style="margin-left:0pt;">-rw-r--r--   3 root supergroup          0 2016-07-30 20:50 /flume/tailout/16-07-30/<span style="color:#ff0000;">2050</span>/events-.1469883004544.tmp</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;">HDFS 文件信息：</p>

<p style="margin-left:0pt;">[root@hadoop1~]# hadoop fs -cat  /flume/tailout/16-07-30/2020/events-.1469881772754</p>

<p style="margin-left:0pt;">Sat Jul 30 20:23:09 CST 2016</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;">[root@hadoop1 ~]# hadoop fs -cat  /flume/tailout/16-07-30/2030/events-.1469882131920</p>

<p style="margin-left:0pt;">Sat Jul 30 20:35:45 CST 2016</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;">注意标红的信息：</p>

<p style="margin-left:0pt;">1 、<span style="color:#000000;">观察日志会发现同时会处理三条信息，神不知道，哪位大神指导，求指教（我靠发现是先往文件中写入数据，再上传到HDFS上只不过时间太短了，有做好了下一个写入的准备，下一个的写入准备正好是上一个的准备的事件）。</span></p>

<p style="margin-left:0pt;"><span style="color:#000000;">2、2050 会截取上一个整数的事件点，来获取数据</span></p>

<ol><li><span style="color:#000000;">往HDFS写入数据需要在运行在HDFS环境中运行，也就是说只要运行在HDFS红就可以啦。多方便啊!!!!!!</span></li>
</ol><h3><strong><a name="_Toc2533"></a><strong><strong><a name="_Toc15752">1-3）、采集文件目录</a></strong></strong></strong></h3>

<p><strong><a name="_Toc22429"></a><strong><strong><a name="_Toc23482">A）、配置</a></strong></strong></strong></p>

<p style="margin-left:0pt;">[root@hadoop1 ~]# cd /usr/local/flume/</p>

<p style="margin-left:0pt;">[root@hadoop1 flume]# mkdir testData</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;">[root@hadoop1 conf]# vi flume_dir.conf</p>

<p style="margin-left:0pt;"># Name the components on this agent</p>

<p style="margin-left:0pt;">a1.sources = r1</p>

<p style="margin-left:0pt;">a1.sinks = k1</p>

<p style="margin-left:0pt;">a1.channels = c1</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Describe/configure the source</p>

<p style="margin-left:0pt;">a1.sources.r1.type = spooldir</p>

<p style="margin-left:0pt;">a1.sources.r1.spoolDir = /usr/local/flume/testData</p>

<p style="margin-left:0pt;">a1.sources.r1.fileHeader = true</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Describe the sink</p>

<p style="margin-left:0pt;">a1.sinks.k1.type = hdfs</p>

<p style="margin-left:0pt;">a1.sinks.k1.channel = c1</p>

<p style="margin-left:0pt;">a1.sinks.k1.hdfs.path = /flume/events/%y-%m-%d/%H%M/</p>

<p style="margin-left:0pt;">a1.sinks.k1.hdfs.filePrefix = events-</p>

<p style="margin-left:0pt;">a1.sinks.k1.hdfs.round = true</p>

<p style="margin-left:0pt;">a1.sinks.k1.hdfs.roundValue = 10</p>

<p style="margin-left:0pt;">a1.sinks.k1.hdfs.roundUnit = minute</p>

<p style="margin-left:0pt;">a1.sinks.k1.hdfs.rollInterval = 3</p>

<p style="margin-left:0pt;"># 可以调文件的大小</p>

<p style="margin-left:0pt;"><span style="color:#ff0000;">a1.sinks.k1.hdfs.rollSize = 20</span></p>

<p style="margin-left:0pt;">a1.sinks.k1.hdfs.rollCount = 5</p>

<p style="margin-left:0pt;">a1.sinks.k1.hdfs.batchSize = 1</p>

<p style="margin-left:0pt;">a1.sinks.k1.hdfs.useLocalTimeStamp = true</p>

<p style="margin-left:0pt;">a1.sinks.k1.hdfs.fileType = DataStream</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Use a channel which buffers events in memory</p>

<p style="margin-left:0pt;">a1.channels.c1.type = memory</p>

<p style="margin-left:0pt;">a1.channels.c1.capacity = 1000</p>

<p style="margin-left:0pt;">a1.channels.c1.transactionCapacity = 100</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Bind the source and sink to the channel</p>

<p style="margin-left:0pt;">a1.sources.r1.channels = c1</p>

<p style="margin-left:0pt;">a1.sinks.k1.channel = c1</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;">[root@hadoop1 flume]# vi a.text</p>

<p style="margin-left:0pt;">[root@hadoop1 flume]# vi b.text</p>

<p style="margin-left:0pt;">[root@hadoop1 flume]# mv a.text  b.text  testData/</p>

<p style="margin-left:0pt;">[root@hadoop1 flume]# cd testData/</p>

<p style="margin-left:0pt;">[root@hadoop1 testData]# ls</p>

<p style="margin-left:0pt;">a.text.COMPLETED  b.text.COMPLETED</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;">注意：不能再监控的目录下创建目录，如果创建目录会不采集，也不能放重复的文件，如果重复Flume会停止运行，目的为不重复采集，后缀为COMPLETED则表示已经采集</p>

<p style="margin-left:0pt;"> </p>

<p><strong><a name="_Toc30291"></a><strong><strong><a name="_Toc4147">B）、启动</a></strong></strong></strong></p>

<p style="margin-left:0pt;">[root@hadoop1 conf]# flume-ng agent -c conf -f  ../conf/flume_dir.conf  -n a1  -Dflume.root.logger=INFO,console</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;">16/09/26 00:28:47 INFO hdfs.BucketWriter: Renaming /flume/events/16-09-26/0020/events-.1474874885229.tmp to /flume/events/16-09-26/0020/events-.1474874885229</p>

<p style="margin-left:0pt;">16/09/26 00:28:47 INFO hdfs.BucketWriter: Creating /flume/events/16-09-26/0020//events-.1474874885230.tmp</p>

<p style="margin-left:0pt;">16/09/26 00:28:47 INFO hdfs.BucketWriter: Closing /flume/events/16-09-26/0020//events-.1474874885230.tmp</p>

<p style="margin-left:0pt;">16/09/26 00:28:47 INFO hdfs.BucketWriter: Renaming /flume/events/16-09-26/0020/events-.1474874885230.tmp to /flume/events/16-09-26/0020/events-.1474874885230</p>

<p style="margin-left:0pt;">16/09/26 00:28:47 INFO hdfs.BucketWriter: Creating /flume/events/16-09-26/0020//events-.1474874885231.tmp</p>

<p style="margin-left:0pt;">16/09/26 00:28:49 INFO hdfs.BucketWriter: Closing /flume/events/16-09-26/0020//events-.1474874885231.tmp</p>

<p style="margin-left:0pt;">16/09/26 00:28:49 INFO hdfs.BucketWriter: Renaming /flume/events/16-09-26/0020/events-.1474874885231.tmp to /flume/events/16-09-26/0020/events-.1474874885231</p>

<p style="margin-left:0pt;">16/09/26 00:28:49 INFO hdfs.BucketWriter: Creating /flume/events/16-09-26/0020//events-.1474874885232.tmp</p>

<p style="margin-left:0pt;">16/09/26 00:28:52 INFO hdfs.BucketWriter: Closing /flume/events/16-09-26/0020//events-.1474874885232.tmp</p>

<p style="margin-left:0pt;">16/09/26 00:28:52 INFO hdfs.BucketWriter: Renaming /flume/events/16-09-26/0020/events-.1474874885232.tmp to /flume/events/16-09-26/0020/events-.1474874885232</p>

<p style="margin-left:0pt;">16/09/26 00:28:52 INFO hdfs.HDFSEventSink: Writer callback called.</p>

<p style="margin-left:0pt;"> </p>

<p><strong><a name="_Toc25024"></a><strong><strong><a name="_Toc23756">C）、查看效果</a></strong></strong></strong></p>

<p style="margin-left:0pt;">[root@hadoop1 testData]# hadoop fs -ls /flume/events/16-09-26/0020</p>

<p style="margin-left:0pt;">Found 4 items</p>

<p style="margin-left:0pt;">-rw-r--r--   3 root supergroup         18 2016-09-26 00:28 /flume/events/16-09-26/0020/events-.1474874885229</p>

<p style="margin-left:0pt;">-rw-r--r--   3 root supergroup         12 2016-09-26 00:28 /flume/events/16-09-26/0020/events-.1474874885230</p>

<p style="margin-left:0pt;">-rw-r--r--   3 root supergroup         13 2016-09-26 00:28 /flume/events/16-09-26/0020/events-.1474874885231</p>

<p style="margin-left:0pt;">-rw-r--r--   3 root supergroup         11 2016-09-26 00:28 /flume/events/16-09-26/0020/events-.1474874885232</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"></p>

<p style="margin-left:0pt;"> </p>

<h3><strong><a name="_Toc16677"></a><strong><strong><a name="_Toc11303">1-4）、两个机器连接</a></strong></strong></strong></h3>

<p style="margin-left:0pt;">Hadoop1 当source ，hadoop2当sink(控制台显示)</p>

<p><strong><a name="_Toc23840"></a><strong><strong><a name="_Toc31025">A）、hadoop1配置</a></strong></strong></strong></p>

<p style="margin-left:0pt;">[root@hadoop1 flume]#  mkdir agentConf</p>

<p style="margin-left:0pt;">[root@hadoop1 flume]# vi tail-avro.properties</p>

<p style="margin-left:0pt;">a1.sources = r1</p>

<p style="margin-left:0pt;">a1.sinks = k1</p>

<p style="margin-left:0pt;">a1.channels = c1</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Describe/configure the source</p>

<p style="margin-left:0pt;">a1.sources.r1.type = exec</p>

<p style="margin-left:0pt;"><span style="color:#ff0000;">a1.sources.r1.command = tail -F /root/log/test.log</span></p>

<p style="margin-left:0pt;">a1.sources.r1.channels = c1</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Describe the sink</p>

<p style="margin-left:0pt;">##sink端的avro是一个数据发送者</p>

<p style="margin-left:0pt;">a1.sinks = k1</p>

<p style="margin-left:0pt;"><span style="color:#ff0000;">a1.sinks.k1.type = avro</span></p>

<p style="margin-left:0pt;">a1.sinks.k1.channel = c1</p>

<p style="margin-left:0pt;"><span style="color:#ff0000;">a1.sinks.k1.hostname = hadoop2</span></p>

<p style="margin-left:0pt;">a1.sinks.k1.port = 8888</p>

<p style="margin-left:0pt;">a1.sinks.k1.batch-size = 2</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Use a channel which buffers events in memory</p>

<p style="margin-left:0pt;">a1.channels.c1.type = memory</p>

<p style="margin-left:0pt;">a1.channels.c1.capacity = 1000</p>

<p style="margin-left:0pt;">a1.channels.c1.transactionCapacity = 100</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Bind the source and sink to the channel</p>

<p style="margin-left:0pt;">a1.sources.r1.channels = c1</p>

<p style="margin-left:0pt;">a1.sinks.k1.channel = c1</p>

<p><strong><a name="_Toc13188"></a><strong><strong><a name="_Toc30668">B）、hadoop2配置</a></strong></strong></strong></p>

<p style="margin-left:0pt;">[root@hadoop2 flume]#  mkdir agentConf</p>

<p style="margin-left:0pt;">[root@hadoop2 flume]#  vi avro-hdfs.properties</p>

<p style="margin-left:0pt;"># Name the components on this agent</p>

<p style="margin-left:0pt;">a1.sources = r1</p>

<p style="margin-left:0pt;">a1.sinks = k1</p>

<p style="margin-left:0pt;">a1.channels = c1</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Describe/configure the source</p>

<p style="margin-left:0pt;">a1.sources.r1.type = avro</p>

<p style="margin-left:0pt;">a1.sources.r1.channels = c1</p>

<p style="margin-left:0pt;">a1.sources.r1.bind = 0.0.0.0</p>

<p style="margin-left:0pt;">a1.sources.r1.port = 8888</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Describe the sink</p>

<p style="margin-left:0pt;">a1.sinks.k1.type = logger</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Use a channel which buffers events in memory</p>

<p style="margin-left:0pt;">a1.channels.c1.type = memory</p>

<p style="margin-left:0pt;">a1.channels.c1.capacity = 1000</p>

<p style="margin-left:0pt;">a1.channels.c1.transactionCapacity = 100</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Bind the source and sink to the channel</p>

<p style="margin-left:0pt;">a1.sources.r1.channels = c1</p>

<p style="margin-left:0pt;">a1.sinks.k1.channel = c1</p>

<p style="margin-left:0pt;"> </p>

<p><strong><a name="_Toc12484"></a><strong><strong><a name="_Toc16099">C）、测试</a></strong></strong></strong></p>

<p style="margin-left:0pt;">先启动hadoop2:</p>

<p style="margin-left:0pt;"> flume-ng agent -c conf -f avro-hdfs.conf   -n a1 -Dflume.root.logger=INFO,console</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;">再启动hadoop1:</p>

<p style="margin-left:0pt;">flume-ng agent -c conf -f  agentConf/tail-avro.properties  -n a1  -Dflume.root.logger=INFO,console</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;">写入数据：</p>

<p style="margin-left:0pt;">[root@hadoop1 log]# while true; do date &gt;&gt; test.log ; sleep 1; done;</p>

<p style="margin-left:0pt;">[root@hadoop1 log]# tail -f test.log</p>

<p style="margin-left:0pt;">Mon Sep 26 02:07:29 PDT 2016</p>

<p style="margin-left:0pt;">Mon Sep 26 02:07:30 PDT 2016</p>

<p style="margin-left:0pt;">Mon Sep 26 02:07:31 PDT 2016</p>

<p style="margin-left:0pt;">Mon Sep 26 02:07:32 PDT 2016</p>

<p style="margin-left:0pt;">Mon Sep 26 02:07:33 PDT 2016</p>

<p style="margin-left:0pt;">Mon Sep 26 02:07:34 PDT 2016</p>

<p style="margin-left:0pt;">Mon Sep 26 02:07:35 PDT 2016</p>

<p style="margin-left:0pt;">Mon Sep 26 02:07:36 PDT 2016</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;">Hadoop1日志：</p>

<p style="margin-left:0pt;">*******</p>

<p style="margin-left:0pt;">16/09/26 02:07:04 INFO instrumentation.MonitoredCounterGroup: Component type: SINK, name: k1 started</p>

<p style="margin-left:0pt;">16/09/26 02:07:04 INFO sink.AbstractRpcSink: Rpc sink k1: Building RpcClient with hostname: hadoop2, port: 4141</p>

<p style="margin-left:0pt;">16/09/26 02:07:04 INFO sink.AvroSink: Attempting to create Avro Rpc client.</p>

<p style="margin-left:0pt;">16/09/26 02:07:04 INFO instrumentation.MonitoredCounterGroup: Monitored counter group for type: SOURCE, name: r1: Successfully registered new MBean.</p>

<p style="margin-left:0pt;">16/09/26 02:07:04 INFO instrumentation.MonitoredCounterGroup: Component type: SOURCE, name: r1 started</p>

<p style="margin-left:0pt;">16/09/26 02:07:04 WARN api.NettyAvroRpcClient: Using default maxIOWorkers</p>

<p style="margin-left:0pt;">16/09/26 02:07:05 INFO sink.AbstractRpcSink: Rpc sink k1 started.</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;">Hadoop2 日志：</p>

<p style="margin-left:0pt;">******</p>

<p style="margin-left:0pt;">16/09/26 02:07:37 INFO sink.LoggerSink: Event: { headers:{} body: 4D 6F 6E 20 53 65 70 20 32 36 20 30 32 3A 30 37 Mon Sep 26 02:07 }</p>

<p style="margin-left:0pt;">16/09/26 02:07:39 INFO sink.LoggerSink: Event: { headers:{} body: 4D 6F 6E 20 53 65 70 20 32 36 20 30 32 3A 30 37 Mon Sep 26 02:07 }</p>

<p style="margin-left:0pt;">16/09/26 02:07:39 INFO sink.LoggerSink: Event: { headers:{} body: 4D 6F 6E 20 53 65 70 20 32 36 20 30 32 3A 30 37 Mon Sep 26 02:07 }</p>

<p style="margin-left:0pt;">16/09/26 02:07:39 INFO sink.LoggerSink: Event: { headers:{} body: 4D 6F 6E 20 53 65 70 20 32 36 20 30 32 3A 30 37 Mon Sep 26 02:07 }</p>

<p style="margin-left:0pt;">16/09/26 02:07:39 INFO sink.LoggerSink: Event: { headers:{} body: 4D 6F 6E 20 53 65 70 20 32 36 20 30 32 3A 30 37 Mon Sep 26 02:07 }</p>

<p style="margin-left:0pt;">******</p>

<h3><strong><a name="_Toc17138"></a><strong><strong><a name="_Toc15224">1-5）、多机测试实例（高可用配置）</a></strong></strong></strong></h3>

<p style="margin-left:0pt;">  Hadoo1 做sources ，hadoop2于hadoop3做sinks的写入的HDFS数据</p>

<p><strong><a name="_Toc19094"></a><strong><strong><a name="_Toc10644">A）、配置</a></strong></strong></strong></p>

<p><strong><strong><strong>1-1）、Hadoop1 配置</strong></strong></strong></p>

<p style="margin-left:0pt;">   # 从tail命令获取数据发送到avro端口</p>

<p style="margin-left:0pt;"># 另一个节点可配置一个avro源来中继数据，发送外部存储</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;">[root@hadoop1 conf]#  vi  sources-hdfs.conf</p>

<p style="margin-left:0pt;">#agent1 name</p>

<p style="margin-left:0pt;">agent1.channels = c1</p>

<p style="margin-left:0pt;">agent1.sources = r1</p>

<p style="margin-left:0pt;">agent1.sinks = k1 k2</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;">#set gruop</p>

<p style="margin-left:0pt;">agent1.sinkgroups = g1</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;">#set channel</p>

<p style="margin-left:0pt;">agent1.channels.c1.type = memory</p>

<p style="margin-left:0pt;">agent1.channels.c1.capacity = 1000</p>

<p style="margin-left:0pt;">agent1.channels.c1.transactionCapacity = 100</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;">agent1.sources.r1.channels = c1</p>

<p style="margin-left:0pt;">agent1.sources.r1.type = exec</p>

<p style="margin-left:0pt;">agent1.sources.r1.command = tail -F /root/log/test.log</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># set  interceptors</p>

<p style="margin-left:0pt;">agent1.sources.r1.interceptors = i1 i2</p>

<p style="margin-left:0pt;">agent1.sources.r1.interceptors.i1.type = static</p>

<p style="margin-left:0pt;">agent1.sources.r1.interceptors.i1.key = Type</p>

<p style="margin-left:0pt;">agent1.sources.r1.interceptors.i1.value = LOGIN</p>

<p style="margin-left:0pt;">agent1.sources.r1.interceptors.i2.type = timestamp</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># set sink1</p>

<p style="margin-left:0pt;">agent1.sinks.k1.channel = c1</p>

<p style="margin-left:0pt;">agent1.sinks.k1.type = avro</p>

<p style="margin-left:0pt;">agent1.sinks.k1.hostname = hadoop2</p>

<p style="margin-left:0pt;">agent1.sinks.k1.port = 52020</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># set sink2</p>

<p style="margin-left:0pt;">agent1.sinks.k2.channel = c1</p>

<p style="margin-left:0pt;">agent1.sinks.k2.type = avro</p>

<p style="margin-left:0pt;">agent1.sinks.k2.hostname = hadoop3</p>

<p style="margin-left:0pt;">agent1.sinks.k2.port = 52020</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;">#set sink group</p>

<p style="margin-left:0pt;">agent1.sinkgroups.g1.sinks = k1 k2</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;">#set failover</p>

<p style="margin-left:0pt;">agent1.sinkgroups.g1.processor.type = failover</p>

<p style="margin-left:0pt;">agent1.sinkgroups.g1.processor.priority.k1 = 10</p>

<p style="margin-left:0pt;">agent1.sinkgroups.g1.processor.priority.k2 = 1</p>

<p style="margin-left:0pt;">agent1.sinkgroups.g1.processor.maxpenalty = 10000</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;">创建文件：</p>

<p style="margin-left:0pt;">[root@hadoop1 ~]# mkdir -p /root/log/</p>

<p style="margin-left:0pt;">[root@hadoop1 log]# touch test.log</p>

<p><strong><strong><strong>1-2）、Hadoop2 配制</strong></strong></strong></p>

<p style="margin-left:0pt;"># 采集配置文件，</p>

<p style="margin-left:0pt;">[root@hadoop2 conf]#  vi sinks-hdfs.conf</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;">#set Agent name</p>

<p style="margin-left:0pt;">a1.sources = r1</p>

<p style="margin-left:0pt;">a1.channels = c1</p>

<p style="margin-left:0pt;">a1.sinks = k1</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;">#set channel</p>

<p style="margin-left:0pt;">a1.channels.c1.type = memory</p>

<p style="margin-left:0pt;">a1.channels.c1.capacity = 1000</p>

<p style="margin-left:0pt;">a1.channels.c1.transactionCapacity = 100</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># other node,nna to nns</p>

<p style="margin-left:0pt;">a1.sources.r1.type = avro</p>

<p style="margin-left:0pt;">a1.sources.r1.bind = hadoop2</p>

<p style="margin-left:0pt;">a1.sources.r1.port = 52020</p>

<p style="margin-left:0pt;">a1.sources.r1.interceptors = i1</p>

<p style="margin-left:0pt;">a1.sources.r1.interceptors.i1.type = static</p>

<p style="margin-left:0pt;">a1.sources.r1.interceptors.i1.key = Collector</p>

<p style="margin-left:0pt;">a1.sources.r1.interceptors.i1.value = hadoop2</p>

<p style="margin-left:0pt;">a1.sources.r1.channels = c1</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;">#set sink to hdfs</p>

<p style="margin-left:0pt;">a1.sinks.k1.type=hdfs</p>

<p style="margin-left:0pt;">a1.sinks.k1.hdfs.path=/home/hdfs/flume/logdfs</p>

<p style="margin-left:0pt;">a1.sinks.k1.hdfs.fileType=DataStream</p>

<p style="margin-left:0pt;">a1.sinks.k1.hdfs.writeFormat=TEXT</p>

<p style="margin-left:0pt;">a1.sinks.k1.hdfs.rollInterval=10</p>

<p style="margin-left:0pt;">a1.sinks.k1.channel=c1</p>

<p style="margin-left:0pt;">a1.sinks.k1.hdfs.filePrefix=%Y-%m-%d</p>

<p style="margin-left:0pt;"># 发送数据：</p>

<p style="margin-left:0pt;"># $ bin/flume-ng avro-client -H localhost -p 4141 -F /usr/logs/log.10</p>

<p><strong><strong><strong>1-3）、Hadoop3配置</strong></strong></strong></p>

<p style="margin-left:0pt;"># 采集配置文件，</p>

<p style="margin-left:0pt;">[root@hadoop3 conf]#  vi sinks-hdfs.conf</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;">#set Agent name</p>

<p style="margin-left:0pt;">a1.sources = r1</p>

<p style="margin-left:0pt;">a1.channels = c1</p>

<p style="margin-left:0pt;">a1.sinks = k1</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;">#set channel</p>

<p style="margin-left:0pt;">a1.channels.c1.type = memory</p>

<p style="margin-left:0pt;">a1.channels.c1.capacity = 1000</p>

<p style="margin-left:0pt;">a1.channels.c1.transactionCapacity = 100</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># other node,nna to nns</p>

<p style="margin-left:0pt;">a1.sources.r1.type = avro</p>

<p style="margin-left:0pt;">a1.sources.r1.bind = hadoop3</p>

<p style="margin-left:0pt;">a1.sources.r1.port = 52020</p>

<p style="margin-left:0pt;">a1.sources.r1.interceptors = i1</p>

<p style="margin-left:0pt;">a1.sources.r1.interceptors.i1.type = static</p>

<p style="margin-left:0pt;">a1.sources.r1.interceptors.i1.key = Collector</p>

<p style="margin-left:0pt;">a1.sources.r1.interceptors.i1.value = hadoop3</p>

<p style="margin-left:0pt;">a1.sources.r1.channels = c1</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;">#set sink to hdfs</p>

<p style="margin-left:0pt;">a1.sinks.k1.type=hdfs</p>

<p style="margin-left:0pt;">a1.sinks.k1.hdfs.path=/home/hdfs/flume/logdfs</p>

<p style="margin-left:0pt;">a1.sinks.k1.hdfs.fileType=DataStream</p>

<p style="margin-left:0pt;">a1.sinks.k1.hdfs.writeFormat=TEXT</p>

<p style="margin-left:0pt;">a1.sinks.k1.hdfs.rollInterval=10</p>

<p style="margin-left:0pt;">a1.sinks.k1.channel=c1</p>

<p style="margin-left:0pt;">a1.sinks.k1.hdfs.filePrefix=%Y-%m-%d</p>

<p style="margin-left:0pt;"># 发送数据：</p>

<p style="margin-left:0pt;"># $ bin/flume-ng avro-client -H localhost -p 4141 -F /usr/logs/log.10</p>

<p style="margin-left:0pt;"> </p>

<p><strong><a name="_Toc24407"></a><strong><strong><a name="_Toc15325">B）、启动</a></strong></strong></strong></p>

<p><strong><strong><strong>1-1）、Hadoop3 日志</strong></strong></strong></p>

<p style="margin-left:0pt;">[root@hadoop3 agentConf]# flume-ng agent -c conf -f collector.properties   -n a1 -Dflume.root.logger=INFO,console</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;">*******</p>

<p style="margin-left:0pt;">16/09/26 02:55:55 INFO node.Application: Starting Channel c1</p>

<p style="margin-left:0pt;">16/09/26 02:55:55 INFO instrumentation.MonitoredCounterGroup: Monitored counter group for type: CHANNEL, name: c1: Successfully registered new MBean.</p>

<p style="margin-left:0pt;">16/09/26 02:55:55 INFO instrumentation.MonitoredCounterGroup: Component type: CHANNEL, name: c1 started</p>

<p style="margin-left:0pt;">16/09/26 02:55:55 INFO node.Application: Starting Sink k1</p>

<p style="margin-left:0pt;">16/09/26 02:55:55 INFO node.Application: Starting Source r1</p>

<p style="margin-left:0pt;">16/09/26 02:55:55 INFO source.AvroSource: Starting Avro source r1: { bindAddress: hadoop3, port: 52020 }...</p>

<p style="margin-left:0pt;">16/09/26 02:55:55 INFO instrumentation.MonitoredCounterGroup: Monitored counter group for type: SINK, name: k1: Successfully registered new MBean.</p>

<p style="margin-left:0pt;">16/09/26 02:55:55 INFO instrumentation.MonitoredCounterGroup: Component type: SINK, name: k1 started</p>

<p style="margin-left:0pt;">16/09/26 02:55:55 INFO instrumentation.MonitoredCounterGroup: Monitored counter group for type: SOURCE, name: r1: Successfully registered new MBean.</p>

<p style="margin-left:0pt;">16/09/26 02:55:55 INFO instrumentation.MonitoredCounterGroup: Component type: SOURCE, name: r1 started</p>

<p style="margin-left:0pt;">******</p>

<p><strong><strong><strong>1-2）、Hadoop2日志</strong></strong></strong></p>

<p style="margin-left:0pt;">[root@hadoop2 agentConf]# flume-ng agent -c conf -f collector.properties   -n a1 -Dflume.root.logger=INFO,console</p>

<p style="margin-left:0pt;">******</p>

<p style="margin-left:0pt;">{k1=SinkRunner: { policy:org.apache.flume.sink.DefaultSinkProcessor@687090a8 counterGroup:{ name:null counters:{} } }} channels:{c1=org.apache.flume.channel.MemoryChannel{name: c1}} }</p>

<p style="margin-left:0pt;">16/09/26 02:55:50 INFO node.Application: Starting Channel c1</p>

<p style="margin-left:0pt;">16/09/26 02:55:50 INFO instrumentation.MonitoredCounterGroup: Monitored counter group for type: CHANNEL, name: c1: Successfully registered new MBean.</p>

<p style="margin-left:0pt;">16/09/26 02:55:50 INFO instrumentation.MonitoredCounterGroup: Component type: CHANNEL, name: c1 started</p>

<p style="margin-left:0pt;">16/09/26 02:55:50 INFO node.Application: Starting Sink k1</p>

<p style="margin-left:0pt;">16/09/26 02:55:50 INFO node.Application: Starting Source r1</p>

<p style="margin-left:0pt;">16/09/26 02:55:50 INFO source.AvroSource: Starting Avro source r1: { bindAddress: hadoop2, port: 52020 }...</p>

<p style="margin-left:0pt;">16/09/26 02:55:50 INFO instrumentation.MonitoredCounterGroup: Monitored counter group for type: SINK, name: k1: Successfully registered new MBean.</p>

<p style="margin-left:0pt;">16/09/26 02:55:50 INFO instrumentation.MonitoredCounterGroup: Component type: SINK, name: k1 started</p>

<p style="margin-left:0pt;">16/09/26 02:55:50 INFO instrumentation.MonitoredCounterGroup: Monitored counter group for type: SOURCE, name: r1: Successfully registered new MBean.</p>

<p style="margin-left:0pt;">16/09/26 02:55:50 INFO instrumentation.MonitoredCounterGroup: Component type: SOURCE, name: r1 started</p>

<p style="margin-left:0pt;">*******</p>

<p><strong><strong><strong>1-3）、Hadoop1日志</strong></strong></strong></p>

<p style="margin-left:0pt;">[root@hadoop1 agentConf]# flume-ng agent -c conf -f agent.properties  -n  agent1  -Dflume.root.logger=INFO,console</p>

<p style="margin-left:0pt;">*******</p>

<p style="margin-left:0pt;">16/09/26 02:56:03 INFO conf.FlumeConfiguration: Processing:k2</p>

<p style="margin-left:0pt;">16/09/26 02:56:03 INFO conf.FlumeConfiguration: Post-validation flume configuration contains configuration for agents: [agent1]</p>

<p style="margin-left:0pt;">16/09/26 02:56:03 INFO node.AbstractConfigurationProvider: Creating channels</p>

<p style="margin-left:0pt;">16/09/26 02:56:03 INFO channel.DefaultChannelFactory: Creating instance of channel c1 type memory</p>

<p style="margin-left:0pt;">16/09/26 02:56:03 INFO node.AbstractConfigurationProvider: Created channel c1</p>

<p style="margin-left:0pt;">16/09/26 02:56:03 INFO source.DefaultSourceFactory: Creating instance of source r1, type exec</p>

<p style="margin-left:0pt;">16/09/26 02:56:03 INFO interceptor.StaticInterceptor: Creating StaticInterceptor: preserveExisting=true,key=Type,value=LOGIN</p>

<p style="margin-left:0pt;">16/09/26 02:56:03 INFO sink.DefaultSinkFactory: Creating instance of sink: k1, type: avro</p>

<p style="margin-left:0pt;">16/09/26 02:56:03 INFO sink.AbstractRpcSink: Connection reset is set to 0. Will not reset connection to next hop</p>

<p style="margin-left:0pt;">16/09/26 02:56:03 INFO sink.DefaultSinkFactory: Creating instance of sink: k2, type: avro</p>

<p style="margin-left:0pt;">16/09/26 02:56:03 INFO sink.AbstractRpcSink: Connection reset is set to 0. Will not reset connection to next hop</p>

<p style="margin-left:0pt;">16/09/26 02:56:03 INFO node.AbstractConfigurationProvider: Channel c1 connected to [r1, k1, k2]</p>

<p style="margin-left:0pt;">*********</p>

<p><strong><a name="_Toc10420"></a><strong><strong><a name="_Toc6507">C）、测试</a></strong></strong></strong></p>

<p><strong><strong><strong>1-1）、写入数据</strong></strong></strong></p>

<p style="margin-left:0pt;">[root@hadoop1 log]# while true; do date &gt;&gt; test.log ; sleep 1; done;</p>

<p style="margin-left:0pt;">[root@hadoop1 log]# tail -f test.log</p>

<p style="margin-left:0pt;">Mon Sep 26 02:57:13 PDT 2016</p>

<p style="margin-left:0pt;">Mon Sep 26 02:57:14 PDT 2016</p>

<p style="margin-left:0pt;">Mon Sep 26 02:57:15 PDT 2016</p>

<p style="margin-left:0pt;">Mon Sep 26 02:57:16 PDT 2016</p>

<p style="margin-left:0pt;">Mon Sep 26 02:57:17 PDT 2016</p>

<p style="margin-left:0pt;"> </p>

<p><strong><strong><strong>1-2）、Hadoop3显示</strong></strong></strong></p>

<p style="margin-left:0pt;">16/09/26 02:56:49 INFO instrumentation.MonitoredCounterGroup: Component type: SINK, name: k1 stopped</p>

<p style="margin-left:0pt;">16/09/26 02:56:49 INFO instrumentation.MonitoredCounterGroup: Shutdown Metric for type: SINK, name: k1. sink.start.time == 1474883755212</p>

<p style="margin-left:0pt;">16/09/26 02:56:49 INFO instrumentation.MonitoredCounterGroup: Shutdown Metric for type: SINK, name: k1. sink.stop.time == 1474883809719</p>

<p style="margin-left:0pt;">16/09/26 02:56:49 INFO instrumentation.MonitoredCounterGroup: Shutdown Metric for type: SINK, name: k1. sink.batch.complete == 0</p>

<p style="margin-left:0pt;">16/09/26 02:56:49 INFO instrumentation.MonitoredCounterGroup: Shutdown Metric for type: SINK, name: k1. sink.batch.empty == 9</p>

<p style="margin-left:0pt;">16/09/26 02:56:49 INFO instrumentation.MonitoredCounterGroup: Shutdown Metric for type: SINK, name: k1. sink.batch.underflow == 0</p>

<p style="margin-left:0pt;">16/09/26 02:56:49 INFO instrumentation.MonitoredCounterGroup: Shutdown Metric for type: SINK, name: k1. sink.connection.closed.count == 0</p>

<p style="margin-left:0pt;">16/09/26 02:56:49 INFO instrumentation.MonitoredCounterGroup: Shutdown Metric for type: SINK, name: k1. sink.connection.creation.count == 0</p>

<p style="margin-left:0pt;">16/09/26 02:56:49 INFO instrumentation.MonitoredCounterGroup: Shutdown Metric for type: SINK, name: k1. sink.connection.failed.count == 0</p>

<p style="margin-left:0pt;">16/09/26 02:56:49 INFO instrumentation.MonitoredCounterGroup: Shutdown Metric for type: SINK, name: k1. sink.event.drain.attempt == 0</p>

<p style="margin-left:0pt;">16/09/26 02:56:49 INFO instrumentation.MonitoredCounterGroup: Shutdown Metric for type: SINK, name: k1. sink.event.drain.sucess == 0</p>

<p style="margin-left:0pt;"> </p>

<p><strong><strong><strong>1-3）、Hadoop2显示</strong></strong></strong></p>

<p style="margin-left:0pt;">16/09/26 02:56:12 INFO hdfs.HDFSDataStream: Serializer = TEXT, UseRawLocalFileSystem = false</p>

<p style="margin-left:0pt;">16/09/26 02:56:13 INFO hdfs.BucketWriter: Creating /home/hdfs/flume/logdfs/2016-09-26.1474883772390.tmp</p>

<p style="margin-left:0pt;">16/09/26 02:56:18 INFO hdfs.BucketWriter: Closing /home/hdfs/flume/logdfs/2016-09-26.1474883772390.tmp</p>

<p style="margin-left:0pt;">16/09/26 02:56:18 INFO hdfs.BucketWriter: Renaming /home/hdfs/flume/logdfs/2016-09-26.1474883772390.tmp to /home/hdfs/flume/logdfs/2016-09-26.1474883772390</p>

<p style="margin-left:0pt;">16/09/26 02:56:18 INFO hdfs.BucketWriter: Creating /home/hdfs/flume/logdfs/2016-09-26.1474883772391.tmp</p>

<p style="margin-left:0pt;">16/09/26 02:56:26 INFO hdfs.BucketWriter: Closing /home/hdfs/flume/logdfs/2016-09-26.1474883772391.tmp</p>

<p style="margin-left:0pt;">16/09/26 02:56:26 INFO hdfs.BucketWriter: Renaming /home/hdfs/flume/logdfs/2016-09-26.1474883772391.tmp to /home/hdfs/flume/logdfs/2016-09-26.1474883772391</p>

<p style="margin-left:0pt;">16/09/26 02:56:26 INFO hdfs.BucketWriter: Creating /home/hdfs/flume/logdfs/2016-09-26.1474883772392.tmp</p>

<p style="margin-left:0pt;">16/09/26 02:56:35 INFO hdfs.BucketWriter: Closing /home/hdfs/flume/logdfs/2016-09-26.1474883772392.tmp</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"> </p>

<p><strong><strong><strong>1-4）、Hadoop1显示</strong></strong></strong></p>

<p style="margin-left:0pt;">16/09/26 02:56:04 INFO sink.AbstractRpcSink: Rpc sink k1 started.</p>

<p style="margin-left:0pt;">16/09/26 02:56:04 INFO sink.AbstractRpcSink: Starting RpcSink k2 { host: hadoop3, port: 52020 }...</p>

<p style="margin-left:0pt;">16/09/26 02:56:04 INFO instrumentation.MonitoredCounterGroup: Monitored counter group for type: SINK, name: k2: Successfully registered new MBean.</p>

<p style="margin-left:0pt;">16/09/26 02:56:04 INFO instrumentation.MonitoredCounterGroup: Component type: SINK, name: k2 started</p>

<p style="margin-left:0pt;">16/09/26 02:56:04 INFO sink.AbstractRpcSink: Rpc sink k2: Building RpcClient with hostname: hadoop3, port: 52020</p>

<p style="margin-left:0pt;">16/09/26 02:56:04 INFO sink.AvroSink: Attempting to create Avro Rpc client.</p>

<p style="margin-left:0pt;">16/09/26 02:56:04 WARN api.NettyAvroRpcClient: Using default maxIOWorkers</p>

<p style="margin-left:0pt;">16/09/26 02:56:04 INFO sink.AbstractRpcSink: Rpc sink k2 started.</p>

<p style="margin-left:0pt;">16/09/26 02:57:17 WARN sink.FailoverSinkProcessor: Sink k1 failed and has been sent to failover list</p>

<p style="margin-left:0pt;"> </p>

<h3><strong><a name="_Toc20150"></a><strong><strong><a name="_Toc3054">1-6）、配置详解</a></strong></strong></strong></h3>

<p><strong><a name="_Toc25445"></a><strong><strong><a name="_Toc25023">A）、Exec方式保存到Kafka</a></strong></strong></strong></p>

<p style="margin-left:0pt;">a1.sources = r1</p>

<p style="margin-left:0pt;">a1.channels = c1</p>

<p style="margin-left:0pt;">a1.sinks = k1</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;">a1.sources.r1.type = exec</p>

<p style="margin-left:0pt;">a1.sources.r1.command = tail -F /usr/local/flume/configurationFile/test.text</p>

<p style="margin-left:0pt;">a1.sources.r1.channels = c1</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;">a1.channels.c1.type=memory</p>

<p style="margin-left:0pt;">a1.channels.c1.capacity=10000</p>

<p style="margin-left:0pt;">a1.channels.c1.transactionCapacity=100</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;">a1.sinks.k1.type = org.apache.flume.sink.kafka.KafkaSink</p>

<p style="margin-left:0pt;">a1.sinks.k1.topic = kafkaTest</p>

<p style="margin-left:0pt;">a1.sinks.k1.brokerList = hadoop1:9092</p>

<p style="margin-left:0pt;">a1.sinks.k1.requiredAcks = 1</p>

<p style="margin-left:0pt;">a1.sinks.k1.batchSize = 20</p>

<p style="margin-left:0pt;">a1.sinks.k1.channel = c1</p>

<p><strong><a name="_Toc27008"></a><strong><strong><a name="_Toc27730">B）、Netcat 模式</a></strong></strong></strong></p>

<p style="margin-left:0pt;"># Name the components on this agent</p>

<p style="margin-left:0pt;">a1.sources = r1</p>

<p style="margin-left:0pt;">a1.sinks = k1</p>

<p style="margin-left:0pt;">a1.channels = c1</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Describe/configure the source</p>

<p style="margin-left:0pt;">a1.sources.r1.type = netcat</p>

<p style="margin-left:0pt;">a1.sources.r1.bind = localhost</p>

<p style="margin-left:0pt;">a1.sources.r1.port = 44444</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Describe the sink</p>

<p style="margin-left:0pt;">a1.sinks.k1.type = logger</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Use a channel which buffers events in memory</p>

<p style="margin-left:0pt;">a1.channels.c1.type = memory</p>

<p style="margin-left:0pt;">a1.channels.c1.capacity = 1000</p>

<p style="margin-left:0pt;">a1.channels.c1.transactionCapacity = 100</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Bind the source and sink to the channel</p>

<p style="margin-left:0pt;">a1.sources.r1.channels = c1</p>

<p style="margin-left:0pt;">a1.sinks.k1.channel = c1</p>            </div>
                </div>