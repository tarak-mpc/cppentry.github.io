---
layout:     post
title:      Flume复习之旅之Flume 配置案例
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：					https://blog.csdn.net/KamRoseLee/article/details/81319071				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p style="margin-left:0pt;">Flume 配置案例</p>

<p style="margin-left:0pt;"><strong><strong>案例 1：start case （single-node configuration）</strong></strong></p>

<p style="margin-left:0pt;"><strong><strong>#文件名：</strong></strong>case1_example.conf</p>

<p style="margin-left:0pt;"><strong><strong>#配置内容：</strong></strong></p>

<p style="margin-left:0pt;"># case1_example.conf: A single-node Flume configuration</p>

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

<p style="margin-left:0pt;">a1.sinks.k1.channel = c1</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><strong><strong>#开始命令</strong></strong></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">flume-ng agent -c conf -f conf/</span><span style="color:#00b050;">case1_</span><span style="color:#00b050;">example.conf -n a1 -Dflume.root.logger=INFO,console</span></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><strong><strong>#启动参数说明</strong></strong></p>

<p style="margin-left:0pt;"><span style="color:#00b0f0;">-c conf </span><span style="color:#00b0f0;">指定配置目录为</span><span style="color:#00b0f0;">conf</span></p>

<p style="margin-left:0pt;"><span style="color:#00b0f0;">-f conf/</span><span style="color:#00b0f0;">case1_</span><span style="color:#00b0f0;">example.conf </span><span style="color:#00b0f0;">指定配置文件为</span><span style="color:#00b0f0;">conf/</span><span style="color:#00b0f0;">case1_</span><span style="color:#00b0f0;">example.conf</span></p>

<p style="margin-left:0pt;"><span style="color:#00b0f0;">-n a1 </span><span style="color:#00b0f0;">指定</span><span style="color:#00b0f0;">agent</span><span style="color:#00b0f0;">名字为</span><span style="color:#00b0f0;">a1,</span><span style="color:#00b0f0;">需要与case1_</span><span style="color:#00b0f0;">example.conf</span><span style="color:#00b0f0;">中的一致</span></p>

<p style="margin-left:0pt;"><span style="color:#00b0f0;">-Dflume.root.logger=INFO,console </span><span style="color:#00b0f0;">指定</span><span style="color:#00b0f0;">DEBUF</span><span style="color:#00b0f0;">模式在</span><span style="color:#00b0f0;">console</span><span style="color:#00b0f0;">输出</span><span style="color:#00b0f0;">INFO</span><span style="color:#00b0f0;">信息</span></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><strong><strong>#</strong></strong><strong><strong>在另一个终端进行测试</strong></strong></p>

<p style="margin-left:0pt;"><span style="color:#ff0000;">telnet 127.0.0.1 44444</span></p>

<p style="margin-left:0pt;">Trying 127.0.0.1...</p>

<p style="margin-left:0pt;">Connected to localhost.localdomain (127.0.0.1).</p>

<p style="margin-left:0pt;">Escape character is '^]'.</p>

<p style="margin-left:0pt;"><span style="color:#ff0000;">hello world! </span></p>

<p style="margin-left:0pt;">OK</p>

<p style="margin-left:0pt;"><strong><strong>#</strong></strong><strong><strong>在启动的终端查看</strong></strong><strong><strong>console</strong></strong><strong><strong>输出</strong></strong></p>

<p style="margin-left:0pt;">2013-05-24 00:00:24,306 (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org.apache.flume.sink.LoggerSink.process(LoggerSink.java:70)] Event: { headers:{} body: 68 65 6C 6C 6F 20 77 6F 72 6C 64 21 0D <span style="color:#ff0000;">hello world!</span>. }</p>

<p style="margin-left:0pt;"><strong><strong>案例2：Test Avro Source</strong></strong></p>

<p style="margin-left:0pt;"><strong><strong>#文件名：</strong></strong>case2_avro.conf</p>

<p style="margin-left:0pt;"><strong><strong>#配置内容：</strong></strong></p>

<p style="margin-left:0pt;"># Name the components on this agent</p>

<p style="margin-left:0pt;">a1.sources = r1</p>

<p style="margin-left:0pt;">a1.sinks = k1</p>

<p style="margin-left:0pt;">a1.channels = c1</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Describe/configure the source</p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sources.r1.type = avro</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sources.r1.channels = c1</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sources.r1.bind = 0.0.0.0</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sources.r1.port = 4141</span></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Describe the sink</p>

<p style="margin-left:0pt;">a1.sinks.k1.type = logger</p>

<p style="margin-left:0pt;"> a1.sinks.k1.channel = c1</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Use a channel which buffers events in memory</p>

<p style="margin-left:0pt;">a1.channels.c1.type = memory</p>

<p style="margin-left:0pt;">a1.channels.c1.capacity = 1000</p>

<p style="margin-left:0pt;">a1.channels.c1.transactionCapacity = 100</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><strong><strong>#Start flume agent a1</strong></strong></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">flume-ng agent -c . -f </span><span style="color:#00b050;">case2_</span><span style="color:#00b050;">avro.conf -n a1 -Dflume.root.logger=INFO,console</span></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><strong><strong>#</strong></strong><strong><strong>创建指定文件</strong></strong></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">echo "</span><span style="color:#ff0000;">hello world</span><span style="color:#00b050;">" &gt; /usr/logs/log.10</span></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><strong><strong>#</strong></strong><strong><strong>使用</strong></strong><strong><strong>avro-client</strong></strong><strong><strong>发送文件</strong></strong></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">flume-ng avro-client -c . -H localhost -p 4141 -F /usr/logs/log.10</span></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><strong><strong>#</strong></strong><strong><strong>在启动的终端查看</strong></strong><strong><strong>console</strong></strong><strong><strong>输出</strong></strong></p>

<p style="margin-left:0pt;">2013-05-27 01:11:45,852 (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org.apache.flume.sink.LoggerSink.process(LoggerSink.java:70)] Event: { headers:{} body: 68 65 6C 6C 6F 20 77 6F 72 6C 64<span style="color:#ff0000;"> hello world</span> }</p>

<p style="margin-left:0pt;"><strong><strong>案例3：Test Exec Source</strong></strong></p>

<p style="margin-left:0pt;"><strong><strong>#文件名：</strong></strong>case3_exec.conf</p>

<p style="margin-left:0pt;"><strong><strong>#配置内容：</strong></strong></p>

<p style="margin-left:0pt;"># Name the components on this agent</p>

<p style="margin-left:0pt;">a1.sources = r1</p>

<p style="margin-left:0pt;">a1.sinks = k1</p>

<p style="margin-left:0pt;">a1.channels = c1</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Describe/configure the source</p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sources.r1.type = exec</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sources.r1.command = cat /usr/logs/log.10</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sources.r1.channels = c1</span></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Describe the sink</p>

<p style="margin-left:0pt;">a1.sinks.k1.type = logger</p>

<p style="margin-left:0pt;"> a1.sinks.k1.channel = c1</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Use a channel which buffers events in memory</p>

<p style="margin-left:0pt;">a1.channels.c1.type = memory</p>

<p style="margin-left:0pt;">a1.channels.c1.capacity = 1000</p>

<p style="margin-left:0pt;">a1.channels.c1.transactionCapacity = 100</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><strong><strong>#</strong></strong><strong><strong>启动</strong></strong><strong><strong>flume agent a1</strong></strong></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">flume-ng agent -c . -f </span><span style="color:#00b050;">case3_</span><span style="color:#00b050;">exec.conf -n a1 -Dflume.root.logger=INFO,console</span></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><strong><strong>#</strong></strong><strong><strong>追加内容到文件</strong></strong></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">echo "</span><span style="color:#ff0000;">exec test</span><span style="color:#00b050;">" &gt;&gt; /usr/logs/log.10</span></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><strong><strong>#</strong></strong><strong><strong>在启动的终端查看</strong></strong><strong><strong>console</strong></strong><strong><strong>输出</strong></strong></p>

<p style="margin-left:0pt;">2013-05-27 01:50:12,825 (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org.apache.flume.sink.LoggerSink.process(LoggerSink.java:70)] Event: { headers:{} body: 68 65 6C 6C 6F 20 77 6F 72 6C 64 hello world }</p>

<p style="margin-left:0pt;">2013-05-27 01:50:12,826 (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org.apache.flume.sink.LoggerSink.process(LoggerSink.java:70)] Event: { headers:{} body: 65 78 65 63 20 74 65 73 74 <span style="color:#ff0000;">exec test</span> }</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><strong><strong>#</strong></strong><strong><strong>如果要使用</strong></strong><strong><strong>tail</strong></strong><strong><strong>命令，必选使得</strong></strong><strong><strong>file</strong></strong><strong><strong>足够大才能看到输出内容</strong></strong></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sources.r1.command = tail -F /usr/logs/log.10</span></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><strong><strong>#</strong></strong><strong><strong>生成足够多的内容在文件里</strong></strong></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">for i in {1..100};do echo "</span><span style="color:#ff0000;">exec test$i</span><span style="color:#00b050;">" &gt;&gt; /usr/logs/log.10;echo $i;done</span></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><strong><strong>#</strong></strong><strong><strong>可以在</strong></strong><strong><strong>console</strong></strong><strong><strong>看到</strong></strong><strong><strong>output</strong></strong></p>

<p style="margin-left:0pt;">2013-05-27 19:17:18,157 (lifecycleSupervisor-1-1) [INFO - org.apache.flume.source.ExecSource.start(ExecSource.java:155)] Exec source starting with command:tail -n 5 -F /usr/logs/log.10</p>

<p style="margin-left:0pt;">2013-05-27 19:19:50,334 (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org.apache.flume.sink.LoggerSink.process(LoggerSink.java:70)] Event: { headers:{} body: 65 78 65 63 20 74 65 73 74 37 <span style="color:#ff0000;">exec test7</span> }</p>

<p style="margin-left:0pt;"><strong><strong>案例4：Test spool Source</strong></strong></p>

<p style="margin-left:0pt;"><strong><strong>#文件名：</strong></strong>case4_spool.conf</p>

<p style="margin-left:0pt;"><strong><strong>#配置内容：</strong></strong></p>

<p style="margin-left:0pt;"># Name the components on this agent</p>

<p style="margin-left:0pt;">a1.sources = r1</p>

<p style="margin-left:0pt;">a1.sinks = k1</p>

<p style="margin-left:0pt;">a1.channels = c1</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Describe/configure the source</p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sources.r1.type = spooldir</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sources.r1.spoolDir = /usr/logs/flumeSpool</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sources.r1.fileHeader = true</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sources.r1.channels = c1</span></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Describe the sink</p>

<p style="margin-left:0pt;">a1.sinks.k1.type = logger</p>

<p style="margin-left:0pt;"> a1.sinks.k1.channel = c1</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Use a channel which buffers events in memory</p>

<p style="margin-left:0pt;">a1.channels.c1.type = memory</p>

<p style="margin-left:0pt;">a1.channels.c1.capacity = 1000</p>

<p style="margin-left:0pt;">a1.channels.c1.transactionCapacity = 100</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><strong><strong>#</strong></strong><strong><strong>启动</strong></strong><strong><strong>flume agent a1</strong></strong></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">flume-ng agent -c . -f </span><span style="color:#00b050;">case4_</span><span style="color:#00b050;">spool.conf -n a1 -Dflume.root.logger=INFO,console</span></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><strong><strong>#</strong></strong><strong><strong>追加内容到</strong></strong><strong><strong>spool</strong></strong><strong><strong>目录</strong></strong></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">echo "</span><span style="color:#ff0000;">spool test1</span><span style="color:#00b050;">" &gt; /usr/logs/flumeSpool/spool1.log</span></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><strong><strong>#</strong></strong><strong><strong>在启动的终端查看</strong></strong><strong><strong>console</strong></strong><strong><strong>输出</strong></strong></p>

<p style="margin-left:0pt;">2013-05-27 22:49:06,098 (pool-4-thread-1) [INFO - org.apache.flume.client.avro.SpoolingFileLineReader.retireCurrentFile(SpoolingFileLineReader.java:229)] Preparing to move file /usr/logs/flumeSpool/spool1.log to /usr/logs/flumeSpool/<span style="color:#ff0000;">spool1.log.COMPLETED</span></p>

<p style="margin-left:0pt;">2013-05-27 22:49:06,101 (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org.apache.flume.sink.LoggerSink.process(LoggerSink.java:70)] Event: { headers:{file=/usr/logs/flumeSpool/spool1.log} body: 73 70 6F 6F 6C 20 74 65 73 74 31 <span style="color:#ff0000;">spool test1 </span>}</p>

<p style="margin-left:0pt;"><strong><strong>案例5：Test </strong></strong><strong><strong>Syslog tcp source</strong></strong></p>

<p style="margin-left:0pt;"><strong><strong>#文件名：</strong></strong>case5_syslog.conf</p>

<p style="margin-left:0pt;"><strong><strong>#配置内容：</strong></strong></p>

<p style="margin-left:0pt;"># Name the components on this agent</p>

<p style="margin-left:0pt;">a1.sources = r1</p>

<p style="margin-left:0pt;">a1.sinks = k1</p>

<p style="margin-left:0pt;">a1.channels = c1</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Describe/configure the source</p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sources.r1.type = syslogtcp</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sources.r1.port = 5140</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sources.r1.host = localhost</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sources.r1.channels = c1</span></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Describe the sink</p>

<p style="margin-left:0pt;">a1.sinks.k1.type = logger</p>

<p style="margin-left:0pt;"> a1.sinks.k1.channel = c1</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Use a channel which buffers events in memory</p>

<p style="margin-left:0pt;">a1.channels.c1.type = memory</p>

<p style="margin-left:0pt;">a1.channels.c1.capacity = 1000</p>

<p style="margin-left:0pt;">a1.channels.c1.transactionCapacity = 100</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><strong><strong>#</strong></strong><strong><strong>启动</strong></strong><strong><strong>flume agent a1</strong></strong></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">flume-ng agent -c . -f </span><span style="color:#00b050;">case5_</span><span style="color:#00b050;">syslog.conf -n a1 -Dflume.root.logger=INFO,console</span></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><strong><strong>#</strong></strong><strong><strong>测试产生</strong></strong><strong><strong>syslog, &lt;37&gt;</strong></strong><strong><strong>因为需要</strong></strong><strong><strong>wire format</strong></strong><strong><strong>数据，否则会报错</strong></strong><strong><strong>” Failed to extract syslog wire entry”</strong></strong></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">echo "</span><span style="color:#ff0000;">&lt;37&gt;hello via syslog</span><span style="color:#00b050;">" | nc localhost 5140</span></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><strong><strong>#</strong></strong><strong><strong>在启动的终端查看</strong></strong><strong><strong>console</strong></strong><strong><strong>输出</strong></strong></p>

<p style="margin-left:0pt;">2013-05-27 23:39:10,755 (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org.apache.flume.sink.LoggerSink.process(LoggerSink.java:70)] Event: { headers:{Severity=5, Facility=4} body: 68 65 6C 6C 6F 20 76 69 61 20 73 79 73 6C 6F 67<span style="color:#ff0000;"> hello via syslog</span> }</p>

<p style="margin-left:0pt;"><strong><strong>案例6：Test </strong></strong><strong><strong>Syslog </strong></strong><strong><strong>udp</strong></strong><strong><strong> source</strong></strong></p>

<p style="margin-left:0pt;"><strong><strong>#文件名：</strong></strong>case6_syslogudp.conf</p>

<p style="margin-left:0pt;"><strong><strong>#配置内容：</strong></strong></p>

<p style="margin-left:0pt;"># Name the components on this agent</p>

<p style="margin-left:0pt;">a1.sources = r1</p>

<p style="margin-left:0pt;">a1.sinks = k1</p>

<p style="margin-left:0pt;">a1.channels = c1</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Describe/configure the source</p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sources.r1.type = syslog</span><span style="color:#00b050;">udp</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sources.r1.port = 5140</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sources.r1.host = localhost</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sources.r1.channels = c1</span></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Describe the sink</p>

<p style="margin-left:0pt;">a1.sinks.k1.type = logger</p>

<p style="margin-left:0pt;"> a1.sinks.k1.channel = c1</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Use a channel which buffers events in memory</p>

<p style="margin-left:0pt;">a1.channels.c1.type = memory</p>

<p style="margin-left:0pt;">a1.channels.c1.capacity = 1000</p>

<p style="margin-left:0pt;">a1.channels.c1.transactionCapacity = 100</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><strong><strong>#</strong></strong><strong><strong>启动</strong></strong><strong><strong>flume agent a1</strong></strong></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">flume-ng agent -c . -f </span><span style="color:#00b050;">case6_</span><span style="color:#00b050;">syslog</span><span style="color:#00b050;">udp</span><span style="color:#00b050;">.conf -n a1 -Dflume.root.logger=INFO,console</span></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><strong><strong>#</strong></strong><strong><strong>测试产生</strong></strong><strong><strong>syslog</strong></strong></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">echo "</span><span style="color:#ff0000;">&lt;37&gt;hello via syslog</span><span style="color:#ff0000;">udp</span><span style="color:#00b050;">" | nc -u localhost 5140</span></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><strong><strong>#</strong></strong><strong><strong>在启动的终端查看</strong></strong><strong><strong>console</strong></strong><strong><strong>输出</strong></strong></p>

<p style="margin-left:0pt;">2013-05-27 23:39:10,755 (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org.apache.flume.sink.LoggerSink.process(LoggerSink.java:70)] Event: { headers:{Severity=5, Facility=4} body: 68 65 6C 6C 6F 20 76 69 61 20 73 79 73 6C 6F 67<span style="color:#ff0000;"> hello via syslog</span><span style="color:#ff0000;">udp</span> }</p>

<p style="margin-left:0pt;"><strong><strong>案例7：Test </strong></strong><strong><strong>HTTP source JSONHandler</strong></strong></p>

<p style="margin-left:0pt;"><strong><strong>#文件名：</strong></strong>case7_httppost.conf</p>

<p style="margin-left:0pt;"><strong><strong>#配置内容：</strong></strong></p>

<p style="margin-left:0pt;"># Name the components on this agent</p>

<p style="margin-left:0pt;">a1.sources = r1</p>

<p style="margin-left:0pt;">a1.sinks = k1</p>

<p style="margin-left:0pt;">a1.channels = c1</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Describe/configure the source</p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sources.r1.type = org.apache.flume.source.http.HTTPSource</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sources.r1.port = 5140</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sources.r1.channels = c1</span></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Describe the sink</p>

<p style="margin-left:0pt;">a1.sinks.k1.type = logger</p>

<p style="margin-left:0pt;"> a1.sinks.k1.channel = c1</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Use a channel which buffers events in memory</p>

<p style="margin-left:0pt;">a1.channels.c1.type = memory</p>

<p style="margin-left:0pt;">a1.channels.c1.capacity = 1000</p>

<p style="margin-left:0pt;">a1.channels.c1.transactionCapacity = 100</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><strong><strong>#</strong></strong><strong><strong>启动</strong></strong><strong><strong>flume agent a1</strong></strong></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">flume-ng agent -c . -f </span><span style="color:#00b050;">case7_http</span><span style="color:#00b050;">post.conf -n a1 -Dflume.root.logger=INFO,console</span></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><strong><strong>#</strong></strong><strong><strong>生成</strong></strong><strong><strong>JSON </strong></strong><strong><strong>格式的</strong></strong><strong><strong>POST request</strong></strong></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">curl -X POST -d '[{ "headers" :{"namenode" : "namenode.example.com","datanode" : "random_datanode.example.com"},"body" : "</span><span style="color:#ff0000;">really_random_body</span><span style="color:#00b050;">"}]' http://localhost:5140</span></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><strong><strong>#</strong></strong><strong><strong>在启动的终端查看</strong></strong><strong><strong>console</strong></strong><strong><strong>输出</strong></strong></p>

<p style="margin-left:0pt;">2013-05-28 01:17:47,186 (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org.apache.flume.sink.LoggerSink.process(LoggerSink.java:70)] Event: { headers:{namenode=namenode.example.com, datanode=random_datanode.example.com} body: 72 65 61 6C 6C 79 5F 72 61 6E 64 6F 6D 5F 62 6F <span style="color:#ff0000;">random_datanode</span><span style="color:#ff0000;">_body</span></p>

<p style="margin-left:0pt;"><strong><strong>案例8：Test HDFS Sink</strong></strong></p>

<p style="margin-left:0pt;"><strong><strong>#文件名：</strong></strong>case8_hdfs.conf</p>

<p style="margin-left:0pt;">在/usr/local/apache-flume-1.3.1-bin/conf/flume-env.sh加入</p>

<p style="margin-left:0pt;">export HADOOP_HOME=/usr/local/hadoop</p>

<p style="margin-left:0pt;"><strong><strong>#配置内容：</strong></strong></p>

<p style="margin-left:0pt;"># Name the components on this agent</p>

<p style="margin-left:0pt;">a1.sources = r1</p>

<p style="margin-left:0pt;">a1.sinks = k1</p>

<p style="margin-left:0pt;">a1.channels = c1</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Describe/configure the source</p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sources.r1.type = syslogtcp</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sources.r1.bind = 0.0.0.0</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sources.r1.port = 5140</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sources.r1.channels = c1</span></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Describe the sink</p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sinks.k1.type = hdfs</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sinks.k1.channel = c1</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sinks.k1.hdfs.path = hdfs://master:9000/user/hadoop/flume/collected/</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sinks.k1.hdfs.filePrefix = Syslog</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sinks.k1.hdfs.round = true</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sinks.k1.hdfs.roundValue = 10</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sinks.k1.hdfs.roundUnit = minute</span></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Use a channel which buffers events in memory</p>

<p style="margin-left:0pt;">a1.channels.c1.type = memory</p>

<p style="margin-left:0pt;">a1.channels.c1.capacity = 1000</p>

<p style="margin-left:0pt;">a1.channels.c1.transactionCapacity = 100</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><strong><strong>#</strong></strong><strong><strong>启动</strong></strong><strong><strong>flume agent a1</strong></strong></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">flume-ng agent -c . -f </span><span style="color:#00b050;">case8_</span><span style="color:#00b050;">hdfs.conf -n a1 -Dflume.root.logger=INFO,console</span></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><strong><strong>#</strong></strong><strong><strong>测试产生</strong></strong><strong><strong>syslog</strong></strong></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">echo "</span><span style="color:#ff0000;">&lt;37&gt;hello via syslog to hdfs testing one</span><span style="color:#00b050;">" | nc -u localhost 5140</span></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><strong><strong>#</strong></strong><strong><strong>在启动的终端查看</strong></strong><strong><strong>console</strong></strong><strong><strong>输出</strong></strong><strong><strong>,</strong></strong><strong><strong>文件生成成功</strong></strong></p>

<p style="margin-left:0pt;">2013-05-29 00:53:58,078 (hdfs-k1-call-runner-0) [INFO - org.apache.flume.sink.hdfs.BucketWriter.doOpen(BucketWriter.java:208)] Creating hdfs://master:9000/user/hadoop/flume/collected//Syslog.1369814037714.tmp</p>

<p style="margin-left:0pt;">2013-05-29 00:54:28,220 (hdfs-k1-roll-timer-0) [INFO - org.apache.flume.sink.hdfs.BucketWriter.renameBucket(BucketWriter.java:427)] Renaming hdfs://master:9000/user/hadoop/flume/collected/Syslog.1369814037714.tmp to hdfs://master:9000/user/hadoop/flume/collected/Syslog.1369814037714</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><strong><strong>#</strong></strong><strong><strong>在</strong></strong><strong><strong>hadoop</strong></strong><strong><strong>上查看文件</strong></strong></p>

<p style="margin-left:0pt;">./hadoop dfs -cat hdfs://172.25.4.35:9000/user/hadoop/flume/collected/Syslog.1369814037714</p>

<p style="margin-left:0pt;">SEQ!org.apache.hadoop.io.LongWritable"org.apache.hadoop.io.BytesWritable^;&gt;Gv$<span style="color:#ff0000;">hello via syslog to hdfs testing one</span></p>

<p style="margin-left:0pt;"><strong><strong>案例9：Test HDFS Sink</strong></strong></p>

<p style="margin-left:0pt;"><strong><strong>#文件名：</strong></strong>case9_hdfs.conf</p>

<p style="margin-left:0pt;">在/usr/local/apache-flume-1.3.1-bin/conf/flume-env.sh加入</p>

<p style="margin-left:0pt;">export HADOOP_HOME=/usr/local/hadoop</p>

<p style="margin-left:0pt;"><strong><strong>#配置内容：</strong></strong></p>

<p style="margin-left:0pt;"># Name the components on this agent</p>

<p style="margin-left:0pt;">a1.sources = r1</p>

<p style="margin-left:0pt;">a1.sinks = k1</p>

<p style="margin-left:0pt;">a1.channels = c1</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Describe/configure the source</p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sources.r1.type = org.apache.flume.source.http.HTTPSource</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sources.r1.bind = 0.0.0.0</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sources.r1.port = 5140</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sources.r1.channels = c1</span></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Describe the sink</p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sinks.k1.type = hdfs</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sinks.k1.channel = c1</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sinks.k1.hdfs.path</span><span style="color:#00b050;">=</span><span style="color:#00b050;"> = hdfs://master:9000/user/hadoop/flume/collected/%y-%m-%d/%H%M/%S</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sinks.k1.hdfs.filePrefix = Syslog.%{host}</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sinks.k1.hdfs.round = true</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sinks.k1.hdfs.roundValue = 10</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sinks.k1.hdfs.roundUnit = minute</span></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Use a channel which buffers events in memory</p>

<p style="margin-left:0pt;">a1.channels.c1.type = memory</p>

<p style="margin-left:0pt;">a1.channels.c1.capacity = 1000</p>

<p style="margin-left:0pt;">a1.channels.c1.transactionCapacity = 100</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><strong><strong>#</strong></strong><strong><strong>启动</strong></strong><strong><strong>flume agent a1</strong></strong></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">flume-ng agent -c . -f </span><span style="color:#00b050;">case9_</span><span style="color:#00b050;">hdfs.conf -n a1 -Dflume.root.logger=INFO,console</span></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><strong><strong>#</strong></strong><strong><strong>生成</strong></strong><strong><strong>JSON </strong></strong><strong><strong>格式的</strong></strong><strong><strong>POST request</strong></strong>,<span style="color:#ff0000;"> header</span><span style="color:#ff0000;">的</span><span style="color:#ff0000;">timestamp </span><span style="color:#ff0000;">参数如果格式不对则无法解析</span></p>

<p style="margin-left:0pt;"><span style="color:#ff0000;">需要生成</span><span style="color:#ff0000;">13</span><span style="color:#ff0000;">为的</span><span style="color:#ff0000;">timestamp</span><span style="color:#ff0000;">才能解析出正确的时间</span><span style="color:#ff0000;">,</span><span style="color:#ff0000;">包含</span><span style="color:#ff0000;">MilliSec</span></p>

<p style="margin-left:0pt;">#linux生成当前时间10位Unix timestamp</p>

<p style="margin-left:0pt;"><span style="color:#00b050;">date +%s</span></p>

<p style="margin-left:0pt;">#linux生成当前时间13位Unix timestamp</p>

<p style="margin-left:0pt;"><span style="color:#00b050;">date +%s%N|awk '{print substr($0,1,13)}'</span></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><span style="color:#00b050;">curl -X POST -d '[{ "headers":{"timestamp":"1369818213654","host":"cc-staging-loginmgr2"},"body": "hello via post"}]' http://localhost:5140</span></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><strong><strong>#</strong></strong><strong><strong>在启动的终端查看</strong></strong><strong><strong>console</strong></strong><strong><strong>输出</strong></strong><strong><strong>,</strong></strong><strong><strong>文件生成成功</strong></strong></p>

<p style="margin-left:0pt;">2013-05-29 02:03:38,646 (hdfs-k1-call-runner-4) [INFO - org.apache.flume.sink.hdfs.BucketWriter.doOpen(BucketWriter.java:208)] Creating hdfs://master:9000/user/hadoop/flume/collected/2013-05-29/0203/cc-staging-loginmgr2..1369818218614.tmp</p>

<p style="margin-left:0pt;">2013-05-29 02:04:08,714 (hdfs-k1-roll-timer-0) [INFO - org.apache.flume.sink.hdfs.BucketWriter.renameBucket(BucketWriter.java:427)] Renaming hdfs://master:9000/user/hadoop/flume/collected/2013-05-29/0203/cc-staging-loginmgr2..1369818218614.tmp to hdfs://master:9000/user/hadoop/flume/collected/2013-05-29/0203/cc-staging-loginmgr2..1369818218614</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><strong><span style="color:#000000;"><strong>#</strong></span></strong><strong><span style="color:#000000;"><strong>在</strong></span></strong><strong><span style="color:#000000;"><strong>hadoop</strong></span></strong><strong><span style="color:#000000;"><strong>上查看文件</strong></span></strong></p>

<p style="margin-left:0pt;">./hadoop dfs -ls hdfs://172.25.4.35:9000/user/hadoop/flume/collected/<span style="color:#ff0000;">2013-05-29/0203</span></p>

<p style="margin-left:0pt;">Found 1 items</p>

<p style="margin-left:0pt;">-rw-r--r-- 3 root supergroup 129 2013-05-29 02:04 /user/hadoop/flume/collected/2013-05-29/0203/cc-staging-loginmgr2..1369818218614</p>

<p style="margin-left:0pt;"><strong><strong>案例10：Test Avro Sink</strong></strong></p>

<p style="margin-left:0pt;"><strong><strong>#文件名：</strong></strong>case10_avro.conf、case10_avro_sink.conf</p>

<p style="margin-left:0pt;"><strong><strong>#配置内容：</strong></strong></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">#case10_avro.conf</span></p>

<p style="margin-left:0pt;"># Name the components on this agent</p>

<p style="margin-left:0pt;">a1.sources = r1</p>

<p style="margin-left:0pt;">a1.sinks = k1</p>

<p style="margin-left:0pt;">a1.channels = c1</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Describe/configure the source</p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sources.r1.type = avro</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sources.r1.channels = c1</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sources.r1.bind = 0.0.0.0</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sources.r1.port = 4545</span></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Describe the sink</p>

<p style="margin-left:0pt;">a1.sinks.k1.type = logger</p>

<p style="margin-left:0pt;"> a1.sinks.k1.channel = c1</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Use a channel which buffers events in memory</p>

<p style="margin-left:0pt;">a1.channels.c1.type = memory</p>

<p style="margin-left:0pt;">a1.channels.c1.capacity = 1000</p>

<p style="margin-left:0pt;">a1.channels.c1.transactionCapacity = 100</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><span style="color:#00b050;">#case10_avro_sink.conf</span></p>

<p style="margin-left:0pt;"># Name the components on this agent</p>

<p style="margin-left:0pt;">a2.sources = r1</p>

<p style="margin-left:0pt;">a2.sinks = k1</p>

<p style="margin-left:0pt;">a2.channels = c1</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Describe/configure the source</p>

<p style="margin-left:0pt;">a2.sources.r1.type = syslogtcp</p>

<p style="margin-left:0pt;">a2.sources.r1.port = 5140</p>

<p style="margin-left:0pt;">a2.sources.r1.host = localhost</p>

<p style="margin-left:0pt;">a2.sources.r1.channels = c1</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Describe the sink</p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a2</span><span style="color:#00b050;">.sinks.k1.type = avro</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a</span><span style="color:#00b050;">2</span><span style="color:#00b050;">.sinks.k1.channel = c1</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a</span><span style="color:#00b050;">2</span><span style="color:#00b050;">.sinks.k1.hostname = 172.25.4.23</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a</span><span style="color:#00b050;">2</span><span style="color:#00b050;">.sinks.k1.port = 4545</span></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Use a channel which buffers events in memory</p>

<p style="margin-left:0pt;">a2.channels.c1.type = memory</p>

<p style="margin-left:0pt;">a2.channels.c1.capacity = 1000</p>

<p style="margin-left:0pt;">a2.channels.c1.transactionCapacity = 100</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><strong><strong>#</strong></strong><strong><strong>先启动</strong></strong><strong><strong>Avro</strong></strong><strong><strong>的</strong></strong><strong><strong>Source,</strong></strong><strong><strong>监听端口</strong></strong></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">flume-ng agent -c . -f </span><span style="color:#00b050;">case10_</span><span style="color:#00b050;">avro.conf -n a1 -Dflume.root.logger=INFO,console</span></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><strong><strong>#</strong></strong><strong><strong>再启动</strong></strong><strong><strong>Avro</strong></strong><strong><strong>的</strong></strong><strong><strong>Sink</strong></strong></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">flume-ng agent -c . -f </span><span style="color:#00b050;">case10_</span><span style="color:#00b050;">avro</span><span style="color:#00b050;">_</span><span style="color:#00b050;">sink.conf -n a</span><span style="color:#00b050;">2</span><span style="color:#00b050;"> -Dflume.root.logger=INFO,console</span></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;">#可以看到已经建立连接</p>

<p style="margin-left:0pt;">2013-06-02 19:23:00,237 (pool-5-thread-1) [INFO - org.apache.avro.ipc.NettyServer$NettyServerAvroHandler.handleUpstream(NettyServer.java:171)] [id: 0x7a0e28bf, /172.25.4.32:14894 =&gt; /172.25.4.23:4545] CONNECTED: /172.25.4.32:14894</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><strong><strong>#</strong></strong><strong><strong>在</strong></strong><strong><strong>Avro Sink</strong></strong><strong><strong>上生成测试</strong></strong><strong><strong>log</strong></strong></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">echo "</span><span style="color:#ff0000;">&lt;37&gt;hello via avro sink</span><span style="color:#00b050;">" | nc localhost 5140</span></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><strong><strong>#</strong></strong><strong><strong>在</strong></strong><strong><strong>Avro Source</strong></strong><strong><strong>上可以看到</strong></strong><strong><strong>log</strong></strong><strong><strong>已经生成</strong></strong></p>

<p style="margin-left:0pt;">2013-06-02 19:24:13,740 (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org.apache.flume.sink.LoggerSink.process(LoggerSink.java:70)] Event: { headers:{Severity=5, Facility=4} body: 68 65 6C 6C 6F 20 76 69 61 20 61 76 72 6F 20 73 <span style="color:#ff0000;">hello via avro s</span><span style="color:#ff0000;">ink</span> }</p>

<p style="margin-left:0pt;"><strong><strong>案例11：Test File Roll Sink</strong></strong></p>

<p style="margin-left:0pt;"><strong><strong>#文件名：</strong></strong>case11_fileroll.conf</p>

<p style="margin-left:0pt;"><strong><strong>#配置内容：</strong></strong></p>

<p style="margin-left:0pt;"># Name the components on this agent</p>

<p style="margin-left:0pt;">a1.sources = r1</p>

<p style="margin-left:0pt;">a1.sinks = k1</p>

<p style="margin-left:0pt;">a1.channels = c1</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Describe/configure the source</p>

<p style="margin-left:0pt;">a1.sources.r1.type = syslogtcp</p>

<p style="margin-left:0pt;">a1.sources.r1.port = 5140</p>

<p style="margin-left:0pt;">a1.sources.r1.host = localhost</p>

<p style="margin-left:0pt;">a1.sources.r1.channels = c1</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Describe the sink</p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sinks.k1.type = file_roll</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sinks.k1.channel = c1</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sinks.k1.sink.directory = /var/log/flume</span></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Use a channel which buffers events in memory</p>

<p style="margin-left:0pt;">a1.channels.c1.type = memory</p>

<p style="margin-left:0pt;">a1.channels.c1.capacity = 1000</p>

<p style="margin-left:0pt;">a1.channels.c1.transactionCapacity = 100</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><strong><strong>#</strong></strong><strong><strong>启动</strong></strong><strong><strong>file roll </strong></strong><strong><strong>配置文件</strong></strong></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">flume-ng agent -c . -f </span><span style="color:#00b050;">case11_</span><span style="color:#00b050;">fileroll.conf -n a1 -Dflume.root.logger=INFO,console</span></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><strong><strong>#</strong></strong><strong><strong>生成测试</strong></strong><strong><strong>log</strong></strong></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">echo "&lt;37&gt;hello via file roll" | nc localhost 5140</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">echo "&lt;37&gt;hello via file roll 2" | nc localhost 5140</span></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><strong><strong>#</strong></strong><strong><strong>查看</strong></strong><strong><strong>/var/log/flume</strong></strong><strong><strong>下是否生成文件</strong></strong><strong><strong>,</strong></strong><strong><strong>默认每</strong></strong><strong><strong>30</strong></strong><strong><strong>秒生成一个新文件</strong></strong></p>

<p style="margin-left:0pt;">-rw-r--r-- 1 root root 20 Jun 2 19:44 1370227443397-1</p>

<p style="margin-left:0pt;">-rw-r--r-- 1 root root 0 Jun 2 19:44 1370227443397-2</p>

<p style="margin-left:0pt;">-rw-r--r-- 1 root root 22 Jun 2 19:45 1370227443397-3</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><span style="color:#00b050;">cat 1370227443397-1 1370227443397-3</span></p>

<p style="margin-left:0pt;">hello via file roll</p>

<p style="margin-left:0pt;">hello via file roll 2</p>

<p style="margin-left:0pt;"><strong><strong>案例12：Test </strong></strong><strong><span style="color:#000000;"><strong>Replicating Channel Selector</strong></span></strong></p>

<p style="margin-left:0pt;"><strong><strong>#文件名：</strong></strong>case12_replicate_sink.conf、case12_replicate_s1.conf、case12_replicate_s2.conf</p>

<p style="margin-left:0pt;"><strong><strong>#配置内容：</strong></strong></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">case12_replicate_sink.conf</span></p>

<p style="margin-left:0pt;">#2个channel和2个sink的配置文件</p>

<p style="margin-left:0pt;"># Name the components on this agent</p>

<p style="margin-left:0pt;">a1.sources = r1</p>

<p style="margin-left:0pt;">a1.sinks = k1 k2</p>

<p style="margin-left:0pt;">a1.channels = c1 c2</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Describe/configure the source</p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sources.r1.type = syslogtcp</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sources.r1.port = 5140</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sources.r1.host = localhost</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sources.r1.selector.type = replicating</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sources.r1.channels = c1 c2</span></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Describe the sink</p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sinks.k1.type = avro</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sinks.k1.channel = c1</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sinks.k1.hostname = 172.25.4.23</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sinks.k1.port = 4545</span></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sinks.k2.type = avro</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sinks.k2.channel = c2</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sinks.k2.hostname = 172.25.4.33</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sinks.k2.port = 4545</span></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Use a channel which buffers events in memory</p>

<p style="margin-left:0pt;">a1.channels.c1.type = memory</p>

<p style="margin-left:0pt;">a1.channels.c1.capacity = 1000</p>

<p style="margin-left:0pt;">a1.channels.c1.transactionCapacity = 100</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;">a1.channels.c2.type = memory</p>

<p style="margin-left:0pt;">a1.channels.c2.capacity = 1000</p>

<p style="margin-left:0pt;">a1.channels.c2.transactionCapacity = 100</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><span style="color:#00b050;">case12_replicate_s1.conf</span></p>

<p style="margin-left:0pt;"># Name the components on this agent</p>

<p style="margin-left:0pt;">a2.sources = r1</p>

<p style="margin-left:0pt;">a2.sinks = k1</p>

<p style="margin-left:0pt;">a2.channels = c1</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Describe/configure the source</p>

<p style="margin-left:0pt;"><span style="color:#000000;">a2</span><span style="color:#000000;">.sources.r1.type = avro</span></p>

<p style="margin-left:0pt;"><span style="color:#000000;">a</span><span style="color:#000000;">2</span><span style="color:#000000;">.sources.r1.channels = c1</span></p>

<p style="margin-left:0pt;"><span style="color:#000000;">a</span><span style="color:#000000;">2</span><span style="color:#000000;">.sources.r1.bind = 172.25.4.23</span></p>

<p style="margin-left:0pt;"><span style="color:#000000;">a</span><span style="color:#000000;">2</span><span style="color:#000000;">.sources.r1.port = 4545</span></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Describe the sink</p>

<p style="margin-left:0pt;">a2.sinks.k1.type = logger</p>

<p style="margin-left:0pt;"> a2.sinks.k1.channel = c1</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Use a channel which buffers events in memory</p>

<p style="margin-left:0pt;">a2.channels.c1.type = memory</p>

<p style="margin-left:0pt;">a2.channels.c1.capacity = 1000</p>

<p style="margin-left:0pt;">a2.channels.c1.transactionCapacity = 100</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><span style="color:#00b050;">case12_replicate_s2.conf</span></p>

<p style="margin-left:0pt;"># Name the components on this agent</p>

<p style="margin-left:0pt;">a3.sources = r1</p>

<p style="margin-left:0pt;">a3.sinks = k1</p>

<p style="margin-left:0pt;">a3.channels = c1</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Describe/configure the source</p>

<p style="margin-left:0pt;"><span style="color:#000000;">a3</span><span style="color:#000000;">.sources.r1.type = avro</span></p>

<p style="margin-left:0pt;"><span style="color:#000000;">a</span><span style="color:#000000;">3</span><span style="color:#000000;">.sources.r1.channels = c1</span></p>

<p style="margin-left:0pt;"><span style="color:#000000;">a</span><span style="color:#000000;">3</span><span style="color:#000000;">.sources.r1.bind = 172.25.4.</span><span style="color:#000000;">3</span><span style="color:#000000;">3</span></p>

<p style="margin-left:0pt;"><span style="color:#000000;">a</span><span style="color:#000000;">3</span><span style="color:#000000;">.sources.r1.port = 4545</span></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Describe the sink</p>

<p style="margin-left:0pt;">a3.sinks.k1.type = logger</p>

<p style="margin-left:0pt;"> a3.sinks.k1.channel = c1</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Use a channel which buffers events in memory</p>

<p style="margin-left:0pt;">a3.channels.c1.type = memory</p>

<p style="margin-left:0pt;">a3.channels.c1.capacity = 1000</p>

<p style="margin-left:0pt;">a3.channels.c1.transactionCapacity = 100</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><strong><strong>#</strong></strong><strong><strong>先启动</strong></strong><strong><strong>Avro</strong></strong><strong><strong>的</strong></strong><strong><strong>Source,</strong></strong><strong><strong>监听端口</strong></strong></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">flume-ng agent -c . -f </span><span style="color:#00b050;">case12_replicate_s1.conf</span><span style="color:#00b050;"> -n a</span><span style="color:#00b050;">2</span><span style="color:#00b050;"> -Dflume.root.logger=INFO,console</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">flume-ng agent -c . -f </span><span style="color:#00b050;">case12_replicate_s2.conf</span><span style="color:#00b050;"> -n a</span><span style="color:#00b050;">3</span><span style="color:#00b050;"> -Dflume.root.logger=INFO,console</span></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><strong><strong>#</strong></strong><strong><strong>再启动</strong></strong><strong><strong>Avro</strong></strong><strong><strong>的</strong></strong><strong><strong>Sink</strong></strong></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">flume-ng agent -c . -f </span><span style="color:#00b050;">case12_replicate_sink</span><span style="color:#00b050;">.conf -n a</span><span style="color:#00b050;">1</span><span style="color:#00b050;">-Dflume.root.logger=INFO,console</span></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><strong><strong>#</strong></strong><strong><strong>查看是否都建立了连接</strong></strong></p>

<p style="margin-left:0pt;">2013-06-04 00:01:53,467 (pool-5-thread-1) [INFO - org.apache.avro.ipc.NettyServer$NettyServerAvroHandler.handleUpstream(NettyServer.java:171)] [id: 0x122a0fad, /172.25.4.32:55518 =&gt; /172.25.4.23:4545] BOUND: /172.25.4.23:4545</p>

<p style="margin-left:0pt;">2013-06-04 00:01:53,467 (pool-5-thread-1) [INFO - org.apache.avro.ipc.NettyServer$NettyServerAvroHandler.handleUpstream(NettyServer.java:171)] [id: 0x122a0fad, /172.25.4.32:55518 =&gt; /172.25.4.23:4545] CONNECTED: /172.25.4.32:55518</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;">2013-06-04 00:01:53,773 (pool-5-thread-1) [INFO - org.apache.avro.ipc.NettyServer$NettyServerAvroHandler.handleUpstream(NettyServer.java:171)] [id: 0x021881a7, /172.25.4.32:23731 =&gt; /172.25.4.33:4545] BOUND: /172.25.4.33:4545</p>

<p style="margin-left:0pt;">2013-06-04 00:01:53,773 (pool-5-thread-1) [INFO - org.apache.avro.ipc.NettyServer$NettyServerAvroHandler.handleUpstream(NettyServer.java:171)] [id: 0x021881a7, /172.25.4.32:23731 =&gt; /172.25.4.33:4545] CONNECTED: /172.25.4.32:23731</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><strong><strong>#</strong></strong><strong><strong>生成测试</strong></strong><strong><strong>log</strong></strong></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">echo "</span><span style="color:#ff0000;">&lt;37&gt;hello via channel selector</span><span style="color:#00b050;">" | nc localhost 5140</span></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;">#查看2个sink是否得到数据</p>

<p style="margin-left:0pt;">2013-06-04 00:02:06,479 (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org.apache.flume.sink.LoggerSink.process(LoggerSink.java:70)] Event: { headers:{Severity=5, Facility=4} body: 68 65 6C 6C 6F 20 76 69 61 20 63 68 61 6E 6E 65 <span style="color:#ff0000;">hello via channe</span> }</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;">2013-06-04 00:02:09,788 (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org.apache.flume.sink.LoggerSink.process(LoggerSink.java:70)] Event: { headers:{Severity=5, Facility=4} body: 68 65 6C 6C 6F 20 76 69 61 20 63 68 61 6E 6E 65 <span style="color:#ff0000;">hello via channe</span> }</p>

<p style="margin-left:0pt;"><strong><strong>案例13：Test </strong></strong><strong><span style="color:#000000;"><strong>Multiplexing</strong></span></strong> <strong><span style="color:#000000;"><strong>Channel Selector</strong></span></strong></p>

<p style="margin-left:0pt;"><strong><strong>#文件名：</strong></strong>case13_multi_sink.conf、case13_ multi _s1.conf、case13_ multi _s2.conf</p>

<p style="margin-left:0pt;"><strong><strong>#配置内容：</strong></strong></p>

<p style="margin-left:0pt;">#2个channel和2个sink的配置文件</p>

<p style="margin-left:0pt;">a1.sources = r1</p>

<p style="margin-left:0pt;">a1.sinks = k1 k2</p>

<p style="margin-left:0pt;">a1.channels = c1 c2</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Describe/configure the source</p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sources.r1.type = org.apache.flume.source.http.HTTPSource</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sources.r1.port = 5140</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sources.r1.host = 0.0.0.0</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sources.r1.selector.type = multiplexing</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sources.r1.channels = c1 c2</span></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sources.r1.selector.header = state</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sources.r1.selector.mapping.CZ = c1</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sources.r1.selector.mapping.US = c2</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sources.r1.selector.default = c1</span></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Describe the sink</p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sinks.k1.type = avro</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sinks.k1.channel = c1</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sinks.k1.hostname = 172.25.4.23</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sinks.k1.port = 4545</span></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sinks.k2.type = avro</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sinks.k2.channel = c2</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sinks.k2.hostname = 172.25.4.33</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sinks.k2.port = 4545</span></p>

<p style="margin-left:0pt;"># Use a channel which buffers events in memory</p>

<p style="margin-left:0pt;">a1.channels.c1.type = memory</p>

<p style="margin-left:0pt;">a1.channels.c1.capacity = 1000</p>

<p style="margin-left:0pt;">a1.channels.c1.transactionCapacity = 100</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;">a1.channels.c2.type = memory</p>

<p style="margin-left:0pt;">a1.channels.c2.capacity = 1000</p>

<p style="margin-left:0pt;">a1.channels.c2.transactionCapacity = 100</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><span style="color:#00b050;">case13_ multi _s1.conf</span></p>

<p style="margin-left:0pt;"># Name the components on this agent</p>

<p style="margin-left:0pt;">a2.sources = r1</p>

<p style="margin-left:0pt;">a2.sinks = k1</p>

<p style="margin-left:0pt;">a2.channels = c1</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Describe/configure the source</p>

<p style="margin-left:0pt;"><span style="color:#000000;">a2</span><span style="color:#000000;">.sources.r1.type = avro</span></p>

<p style="margin-left:0pt;"><span style="color:#000000;">a</span><span style="color:#000000;">2</span><span style="color:#000000;">.sources.r1.channels = c1</span></p>

<p style="margin-left:0pt;"><span style="color:#000000;">a</span><span style="color:#000000;">2</span><span style="color:#000000;">.sources.r1.bind = 172.25.4.23</span></p>

<p style="margin-left:0pt;"><span style="color:#000000;">a</span><span style="color:#000000;">2</span><span style="color:#000000;">.sources.r1.port = 4545</span></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Describe the sink</p>

<p style="margin-left:0pt;">a2.sinks.k1.type = logger</p>

<p style="margin-left:0pt;"> a2.sinks.k1.channel = c1</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Use a channel which buffers events in memory</p>

<p style="margin-left:0pt;">a2.channels.c1.type = memory</p>

<p style="margin-left:0pt;">a2.channels.c1.capacity = 1000</p>

<p style="margin-left:0pt;">a2.channels.c1.transactionCapacity = 100</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><span style="color:#00b050;">case13_ multi _s2.conf</span></p>

<p style="margin-left:0pt;"># Name the components on this agent</p>

<p style="margin-left:0pt;">a3.sources = r1</p>

<p style="margin-left:0pt;">a3.sinks = k1</p>

<p style="margin-left:0pt;">a3.channels = c1</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Describe/configure the source</p>

<p style="margin-left:0pt;"><span style="color:#000000;">a3</span><span style="color:#000000;">.sources.r1.type = avro</span></p>

<p style="margin-left:0pt;"><span style="color:#000000;">a</span><span style="color:#000000;">3</span><span style="color:#000000;">.sources.r1.channels = c1</span></p>

<p style="margin-left:0pt;"><span style="color:#000000;">a</span><span style="color:#000000;">3</span><span style="color:#000000;">.sources.r1.bind = 172.25.4.</span><span style="color:#000000;">3</span><span style="color:#000000;">3</span></p>

<p style="margin-left:0pt;"><span style="color:#000000;">a</span><span style="color:#000000;">3</span><span style="color:#000000;">.sources.r1.port = 4545</span></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Describe the sink</p>

<p style="margin-left:0pt;">a3.sinks.k1.type = logger</p>

<p style="margin-left:0pt;"> a3.sinks.k1.channel = c1</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Use a channel which buffers events in memory</p>

<p style="margin-left:0pt;">a3.channels.c1.type = memory</p>

<p style="margin-left:0pt;">a3.channels.c1.capacity = 1000</p>

<p style="margin-left:0pt;">a3.channels.c1.transactionCapacity = 100</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><strong><strong>#</strong></strong><strong><strong>先启动</strong></strong><strong><strong>Avro</strong></strong><strong><strong>的</strong></strong><strong><strong>Source,</strong></strong><strong><strong>监听端口</strong></strong></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">flume-ng agent -c . -f </span><span style="color:#00b050;">case13_ multi _s1.conf</span><span style="color:#00b050;"> -n a</span><span style="color:#00b050;">2</span><span style="color:#00b050;"> -Dflume.root.logger=INFO,console</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">flume-ng agent -c . -f </span><span style="color:#00b050;">case13_ multi _s2.conf</span><span style="color:#00b050;"> -n a</span><span style="color:#00b050;">3</span><span style="color:#00b050;"> -Dflume.root.logger=INFO,console</span></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><strong><strong>#</strong></strong><strong><strong>再启动</strong></strong><strong><strong>Avro</strong></strong><strong><strong>的</strong></strong><strong><strong>Sink</strong></strong></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">flume-ng agent -c . -f </span><span style="color:#00b050;">case13_multi_sink</span><span style="color:#00b050;">.conf -n a</span><span style="color:#00b050;">1</span><span style="color:#00b050;">-Dflume.root.logger=INFO,console</span></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><strong><strong>#</strong></strong><strong><strong>根据配置文件生成测试的</strong></strong><strong><strong>header </strong></strong><strong><strong>为</strong></strong><strong><strong>state</strong></strong><strong><strong>的</strong></strong><strong><strong>POST</strong></strong><strong><strong>请求</strong></strong></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">curl -X POST -d '[{ "headers" :{"state" : "CZ"},"body" : "TEST1"}]' http://localhost:5140</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">curl -X POST -d '[{ "headers" :{"state" : "US"},"body" : "TEST2"}]' http://localhost:5140</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">curl -X POST -d '[{ "headers" :{"state" : "SH"},"body" : "TEST3"}]' http://localhost:5140</span></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><strong><strong>#</strong></strong><strong><strong>查看</strong></strong><strong><strong>2</strong></strong><strong><strong>个</strong></strong><strong><strong>sink</strong></strong><strong><strong>得到数据是否和配置文件一致</strong></strong></p>

<p style="margin-left:0pt;">Sink1:</p>

<p style="margin-left:0pt;">2013-06-04 23:45:35,296 (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org.apache.flume.sink.LoggerSink.process(LoggerSink.java:70)] Event: { headers:{<span style="color:#ff0000;">state=CZ</span>} body: 54 45 53 54 31 TEST1 }</p>

<p style="margin-left:0pt;">2013-06-04 23:45:50,309 (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org.apache.flume.sink.LoggerSink.process(LoggerSink.java:70)] Event: { headers:{<span style="color:#ff0000;">state=SH</span>} body: 54 45 53 54 33 TEST3 }</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;">Sink2:</p>

<p style="margin-left:0pt;">2013-06-04 23:45:42,293 (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org.apache.flume.sink.LoggerSink.process(LoggerSink.java:70)] Event: { headers:{<span style="color:#ff0000;">state=US</span>} body: 54 45 53 54 32 TEST2 }</p>

<p style="margin-left:0pt;"><strong><strong>案例14：Test </strong></strong><strong><strong>Failover Sink Processor</strong></strong></p>

<p style="margin-left:0pt;"><strong><strong>#文件名：</strong></strong>case14_failover_sink.conf、case14_ failover _s1.conf、case14_ failover _s2.conf</p>

<p style="margin-left:0pt;"><strong><strong>#配置内容：</strong></strong></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">case14_failover_sink.conf</span></p>

<p style="margin-left:0pt;"># Name the components on this agent</p>

<p style="margin-left:0pt;">a1.sources = r1</p>

<p style="margin-left:0pt;">a1.sinks = k1 k2</p>

<p style="margin-left:0pt;">a1.channels = c1 c2</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sinkgroups = g1</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sinkgroups.g1.sinks = k1 k2</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sinkgroups.g1.processor.type = failover</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sinkgroups.g1.processor.priority.k1 = 5</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sinkgroups.g1.processor.priority.k2 = 10</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sinkgroups.g1.processor.maxpenalty = 10000</span></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Describe/configure the source</p>

<p style="margin-left:0pt;">a1.sources.r1.type = syslogtcp</p>

<p style="margin-left:0pt;">a1.sources.r1.port = 5140</p>

<p style="margin-left:0pt;">a1.sources.r1.host = localhost</p>

<p style="margin-left:0pt;">a1.sources.r1.selector.type = replicating</p>

<p style="margin-left:0pt;">a1.sources.r1.channels = c1 c2</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Describe the sink</p>

<p style="margin-left:0pt;">a1.sinks.k1.type = avro</p>

<p style="margin-left:0pt;">a1.sinks.k1.channel = c1</p>

<p style="margin-left:0pt;">a1.sinks.k1.hostname = 172.25.4.23</p>

<p style="margin-left:0pt;">a1.sinks.k1.port = 4545</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;">a1.sinks.k2.type = avro</p>

<p style="margin-left:0pt;">a1.sinks.k2.channel = c2</p>

<p style="margin-left:0pt;">a1.sinks.k2.hostname = 172.25.4.33</p>

<p style="margin-left:0pt;">a1.sinks.k2.port = 4545</p>

<p style="margin-left:0pt;"># Use a channel which buffers events in memory</p>

<p style="margin-left:0pt;">a1.channels.c1.type = memory</p>

<p style="margin-left:0pt;">a1.channels.c1.capacity = 1000</p>

<p style="margin-left:0pt;">a1.channels.c1.transactionCapacity = 100</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;">a1.channels.c2.type = memory</p>

<p style="margin-left:0pt;">a1.channels.c2.capacity = 1000</p>

<p style="margin-left:0pt;">a1.channels.c2.transactionCapacity = 100</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><span style="color:#00b050;">case14_ failover _s1.conf</span> </p>

<p style="margin-left:0pt;"># Name the components on this agent</p>

<p style="margin-left:0pt;">a2.sources = r1</p>

<p style="margin-left:0pt;">a2.sinks = k1</p>

<p style="margin-left:0pt;">a2.channels = c1</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Describe/configure the source</p>

<p style="margin-left:0pt;"><span style="color:#000000;">a2</span><span style="color:#000000;">.sources.r1.type = avro</span></p>

<p style="margin-left:0pt;"><span style="color:#000000;">a</span><span style="color:#000000;">2</span><span style="color:#000000;">.sources.r1.channels = c1</span></p>

<p style="margin-left:0pt;"><span style="color:#000000;">a</span><span style="color:#000000;">2</span><span style="color:#000000;">.sources.r1.bind = 172.25.4.23</span></p>

<p style="margin-left:0pt;"><span style="color:#000000;">a</span><span style="color:#000000;">2</span><span style="color:#000000;">.sources.r1.port = 4545</span></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Describe the sink</p>

<p style="margin-left:0pt;">a2.sinks.k1.type = logger</p>

<p style="margin-left:0pt;"> a2.sinks.k1.channel = c1</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Use a channel which buffers events in memory</p>

<p style="margin-left:0pt;">a2.channels.c1.type = memory</p>

<p style="margin-left:0pt;">a2.channels.c1.capacity = 1000</p>

<p style="margin-left:0pt;">a2.channels.c1.transactionCapacity = 100</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><span style="color:#00b050;">case14_ failover _s2.conf</span> </p>

<p style="margin-left:0pt;"># Name the components on this agent</p>

<p style="margin-left:0pt;">a3.sources = r1</p>

<p style="margin-left:0pt;">a3.sinks = k1</p>

<p style="margin-left:0pt;">a3.channels = c1</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Describe/configure the source</p>

<p style="margin-left:0pt;"><span style="color:#000000;">a3</span><span style="color:#000000;">.sources.r1.type = avro</span></p>

<p style="margin-left:0pt;"><span style="color:#000000;">a</span><span style="color:#000000;">3</span><span style="color:#000000;">.sources.r1.channels = c1</span></p>

<p style="margin-left:0pt;"><span style="color:#000000;">a</span><span style="color:#000000;">3</span><span style="color:#000000;">.sources.r1.bind = 172.25.4.</span><span style="color:#000000;">3</span><span style="color:#000000;">3</span></p>

<p style="margin-left:0pt;"><span style="color:#000000;">a</span><span style="color:#000000;">3</span><span style="color:#000000;">.sources.r1.port = 4545</span></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Describe the sink</p>

<p style="margin-left:0pt;">a3.sinks.k1.type = logger</p>

<p style="margin-left:0pt;"> a3.sinks.k1.channel = c1</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Use a channel which buffers events in memory</p>

<p style="margin-left:0pt;">a3.channels.c1.type = memory</p>

<p style="margin-left:0pt;">a3.channels.c1.capacity = 1000</p>

<p style="margin-left:0pt;">a3.channels.c1.transactionCapacity = 100</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><strong><strong>#</strong></strong><strong><strong>先启动</strong></strong><strong><strong>Avro</strong></strong><strong><strong>的</strong></strong><strong><strong>Source,</strong></strong><strong><strong>监听端口</strong></strong></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">flume-ng agent -c . -f </span><span style="color:#00b050;">case14_ failover _s1.conf</span><span style="color:#00b050;"> -n a</span><span style="color:#00b050;">2</span><span style="color:#00b050;"> -Dflume.root.logger=INFO,console</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">flume-ng agent -c . -f </span><span style="color:#00b050;">case14_ failover _s2.conf</span><span style="color:#00b050;"> -n a</span><span style="color:#00b050;">3</span><span style="color:#00b050;"> -Dflume.root.logger=INFO,console</span></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><strong><strong>#</strong></strong><strong><strong>再启动</strong></strong><strong><strong>Avro</strong></strong><strong><strong>的</strong></strong><strong><strong>Sink</strong></strong></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">flume-ng agent -c . -f </span><span style="color:#00b050;">case14_ failover _sink</span><span style="color:#00b050;">.conf -n a</span><span style="color:#00b050;">1</span><span style="color:#00b050;">-Dflume.root.logger=INFO,console</span></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><strong><strong>#</strong></strong><strong><strong>生成测试</strong></strong><strong><strong>log</strong></strong></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">echo "&lt;37&gt;test1 failover" | nc localhost 5140</span></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><strong><strong>#</strong></strong><strong><strong>在</strong></strong><strong><strong>sink2</strong></strong><strong><strong>上产生</strong></strong><strong><strong>log</strong></strong><strong><strong>，</strong></strong><strong><strong>sink1</strong></strong><strong><strong>由于优先级小，没有产生</strong></strong></p>

<p style="margin-left:0pt;">2013-06-05 00:10:51,194 (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org.apache.flume.sink.LoggerSink.process(LoggerSink.java:70)] Event: { headers:{Severity=5, Facility=4} body: 74 65 73 74 31 20 66 61 69 6C 6F 76 65 72 <span style="color:#ff0000;">test1 failover</span> }</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><strong><strong>#</strong></strong><strong><strong>主动关闭</strong></strong><strong><strong>sink2,</strong></strong><strong><strong>再次生成测试</strong></strong><strong><strong>log</strong></strong></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">echo "&lt;37&gt;test2 failover" | nc localhost 5140</span></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><strong><strong>#</strong></strong><strong><strong>在</strong></strong><strong><strong>sink1</strong></strong><strong><strong>上会同时生成</strong></strong><strong><strong>test1</strong></strong><strong><strong>和</strong></strong><strong><strong>test2</strong></strong></p>

<p style="margin-left:0pt;">2013-06-05 00:11:14,312 (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org.apache.flume.sink.LoggerSink.process(LoggerSink.java:70)] Event: { headers:{Severity=5, Facility=4} body: 74 65 73 74 31 20 66 61 69 6C 6F 76 65 72 <span style="color:#ff0000;">test1 failover</span> }</p>

<p style="margin-left:0pt;">2013-06-05 00:11:14,312 (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org.apache.flume.sink.LoggerSink.process(LoggerSink.java:70)] Event: { headers:{Severity=5, Facility=4} body: 74 65 73 74 32 20 66 61 69 6C 6F 76 65 72 <span style="color:#ff0000;">test2 failover</span> }</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><strong><strong>#</strong></strong><strong><strong>再次打开</strong></strong><strong><strong>sink2,log</strong></strong><strong><strong>会根据优先级再到</strong></strong><strong><strong>sink2</strong></strong><strong><strong>上</strong></strong></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">echo "&lt;37&gt;test4 failover" | nc localhost 5140</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">echo "&lt;37&gt;test5 failover" | nc localhost 5140</span></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;">2013-06-05 00:12:33,071 (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org.apache.flume.sink.LoggerSink.process(LoggerSink.java:70)] Event: { headers:{Severity=5, Facility=4} body: 74 65 73 74 34 20 66 61 69 6C 6F 76 65 72 <span style="color:#ff0000;">test4 failover </span>}</p>

<p style="margin-left:0pt;">2013-06-05 00:12:55,088 (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org.apache.flume.sink.LoggerSink.process(LoggerSink.java:70)] Event: { headers:{Severity=5, Facility=4} body: 74 65 73 74 35 20 66 61 69 6C 6F 76 65 72 <span style="color:#ff0000;">test5 failover</span> }</p>

<p style="margin-left:0pt;"><strong><strong>案例15：</strong></strong><strong><span style="color:#000000;"><strong>Test </strong></span></strong><strong><span style="color:#000000;"><strong>Load balancing Sink Processor</strong></span></strong></p>

<p style="margin-left:0pt;"><strong><strong>#文件名：</strong></strong>case15_load_sink.conf、case15_ load _s1.conf、case15_ load _s2.conf</p>

<p style="margin-left:0pt;"><strong><strong>#配置内容：</strong></strong></p>

<p style="margin-left:0pt;"><span style="color:#ff0000;">注</span><span style="color:#ff0000;">:load balance type</span><span style="color:#ff0000;">下必须指定同一个</span><span style="color:#ff0000;">channel</span><span style="color:#ff0000;">到不同的</span><span style="color:#ff0000;">sinks,</span><span style="color:#ff0000;">否则不生效</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">case15_load_sink.conf</span></p>

<p style="margin-left:0pt;"># Name the components on this agent</p>

<p style="margin-left:0pt;">a1.sources = r1</p>

<p style="margin-left:0pt;">a1.sinks = k1 k2</p>

<p style="margin-left:0pt;">a1.channels = c1</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sinkgroups = g1</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sinkgroups.g1.sinks = k1 k2</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sinkgroups.g1.processor.type = load_balance</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sinkgroups.g1.processor.backoff = true</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sinkgroups.g1.processor.selector = round_robin</span></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Describe/configure the source</p>

<p style="margin-left:0pt;">a1.sources.r1.type = syslogtcp</p>

<p style="margin-left:0pt;">a1.sources.r1.port = 5140</p>

<p style="margin-left:0pt;">a1.sources.r1.host = localhost</p>

<p style="margin-left:0pt;">a1.sources.r1.channels = c1</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Describe the sink</p>

<p style="margin-left:0pt;">a1.sinks.k1.type = avro</p>

<p style="margin-left:0pt;">a1.sinks.k1.channel = c1</p>

<p style="margin-left:0pt;">a1.sinks.k1.hostname = 172.25.4.23</p>

<p style="margin-left:0pt;">a1.sinks.k1.port = 4545</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;">a1.sinks.k2.type = avro</p>

<p style="margin-left:0pt;">a1.sinks.k2.channel = c1</p>

<p style="margin-left:0pt;">a1.sinks.k2.hostname = 172.25.4.33</p>

<p style="margin-left:0pt;">a1.sinks.k2.port = 4545</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Use a channel which buffers events in memory</p>

<p style="margin-left:0pt;">a1.channels.c1.type = memory</p>

<p style="margin-left:0pt;">a1.channels.c1.capacity = 1000</p>

<p style="margin-left:0pt;">a1.channels.c1.transactionCapacity = 100</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><span style="color:#00b050;">case15_ load _s1.conf</span></p>

<p style="margin-left:0pt;"># Name the components on this agent</p>

<p style="margin-left:0pt;">a2.sources = r1</p>

<p style="margin-left:0pt;">a2.sinks = k1</p>

<p style="margin-left:0pt;">a2.channels = c1</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Describe/configure the source</p>

<p style="margin-left:0pt;"><span style="color:#000000;">a2</span><span style="color:#000000;">.sources.r1.type = avro</span></p>

<p style="margin-left:0pt;"><span style="color:#000000;">a</span><span style="color:#000000;">2</span><span style="color:#000000;">.sources.r1.channels = c1</span></p>

<p style="margin-left:0pt;"><span style="color:#000000;">a</span><span style="color:#000000;">2</span><span style="color:#000000;">.sources.r1.bind = 172.25.4.23</span></p>

<p style="margin-left:0pt;"><span style="color:#000000;">a</span><span style="color:#000000;">2</span><span style="color:#000000;">.sources.r1.port = 4545</span></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Describe the sink</p>

<p style="margin-left:0pt;">a2.sinks.k1.type = logger</p>

<p style="margin-left:0pt;"> a2.sinks.k1.channel = c1</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Use a channel which buffers events in memory</p>

<p style="margin-left:0pt;">a2.channels.c1.type = memory</p>

<p style="margin-left:0pt;">a2.channels.c1.capacity = 1000</p>

<p style="margin-left:0pt;">a2.channels.c1.transactionCapacity = 100</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><span style="color:#00b050;">case15_ load _s2.conf</span> </p>

<p style="margin-left:0pt;"># Name the components on this agent</p>

<p style="margin-left:0pt;">a3.sources = r1</p>

<p style="margin-left:0pt;">a3.sinks = k1</p>

<p style="margin-left:0pt;">a3.channels = c1</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Describe/configure the source</p>

<p style="margin-left:0pt;"><span style="color:#000000;">a3</span><span style="color:#000000;">.sources.r1.type = avro</span></p>

<p style="margin-left:0pt;"><span style="color:#000000;">a</span><span style="color:#000000;">3</span><span style="color:#000000;">.sources.r1.channels = c1</span></p>

<p style="margin-left:0pt;"><span style="color:#000000;">a</span><span style="color:#000000;">3</span><span style="color:#000000;">.sources.r1.bind = 172.25.4.</span><span style="color:#000000;">3</span><span style="color:#000000;">3</span></p>

<p style="margin-left:0pt;"><span style="color:#000000;">a</span><span style="color:#000000;">3</span><span style="color:#000000;">.sources.r1.port = 4545</span></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Describe the sink</p>

<p style="margin-left:0pt;">a3.sinks.k1.type = logger</p>

<p style="margin-left:0pt;"> a3.sinks.k1.channel = c1</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Use a channel which buffers events in memory</p>

<p style="margin-left:0pt;">a3.channels.c1.type = memory</p>

<p style="margin-left:0pt;">a3.channels.c1.capacity = 1000</p>

<p style="margin-left:0pt;">a3.channels.c1.transactionCapacity = 100</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><strong><strong>#</strong></strong><strong><strong>先启动</strong></strong><strong><strong>Avro</strong></strong><strong><strong>的</strong></strong><strong><strong>Source,</strong></strong><strong><strong>监听端口</strong></strong></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">flume-ng agent -c . -f </span><span style="color:#00b050;">case15_ load _s1.conf</span><span style="color:#00b050;"> -n a</span><span style="color:#00b050;">2</span><span style="color:#00b050;"> -Dflume.root.logger=INFO,console</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">flume-ng agent -c . -f </span><span style="color:#00b050;">case15_ load _s2.conf</span><span style="color:#00b050;"> -n a</span><span style="color:#00b050;">3</span><span style="color:#00b050;"> -Dflume.root.logger=INFO,console</span></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><strong><strong>#</strong></strong><strong><strong>再启动</strong></strong><strong><strong>Avro</strong></strong><strong><strong>的</strong></strong><strong><strong>Sink</strong></strong></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">flume-ng agent -c . -f </span><span style="color:#00b050;">case15_ load _sink</span><span style="color:#00b050;">.conf -n a</span><span style="color:#00b050;">1</span><span style="color:#00b050;">-Dflume.root.logger=INFO,console</span></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><strong><strong>#</strong></strong><strong><strong>生成</strong></strong><strong><strong>4</strong></strong><strong><strong>个测试</strong></strong><strong><strong>log</strong></strong></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">echo "</span><span style="color:#ff0000;">&lt;37&gt;test2 loadbalance</span><span style="color:#00b050;">" | nc localhost 5140</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">echo "</span><span style="color:#ff0000;">&lt;37&gt;test3 loadbalance</span><span style="color:#00b050;">" | nc localhost 5140</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">echo "</span><span style="color:#ff0000;">&lt;37&gt;test4 loadbalance</span><span style="color:#00b050;">" | nc localhost 5140</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">echo "</span><span style="color:#ff0000;">&lt;37&gt;test5 loadbalance</span><span style="color:#00b050;">" | nc localhost 5140</span></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><strong><strong>#</strong></strong><strong><strong>查看</strong></strong><strong><strong>sink</strong></strong><strong><strong>输出结果是否为轮询模式</strong></strong></p>

<p style="margin-left:0pt;">Sink1:</p>

<p style="margin-left:0pt;">2013-06-06 01:36:03,516 (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org.apache.flume.sink.LoggerSink.process(LoggerSink.java:70)] Event: { headers:{Severity=5, Facility=4} body: 74 65 73 74 32 20 6C 6F 61 64 62 61 6C 61 6E 63 <span style="color:#ff0000;">test2 loadbalanc</span> }</p>

<p style="margin-left:0pt;">2013-06-06 01:36:09,769 (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org.apache.flume.sink.LoggerSink.process(LoggerSink.java:70)] Event: { headers:{Severity=5, Facility=4} body: 74 65 73 74 34 20 6C 6F 61 64 62 61 6C 61 6E 63 <span style="color:#ff0000;">test4 loadbalanc </span>}</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;">Sink2:</p>

<p style="margin-left:0pt;">2013-06-06 01:36:05,809 (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org.apache.flume.sink.LoggerSink.process(LoggerSink.java:70)] Event: { headers:{Severity=5, Facility=4} body: 74 65 73 74 33 20 6C 6F 61 64 62 61 6C 61 6E 63 <span style="color:#ff0000;">test3 loadbalanc</span> }</p>

<p style="margin-left:0pt;">2013-06-06 01:36:37,057 (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org.apache.flume.sink.LoggerSink.process(LoggerSink.java:70)] Event: { headers:{Severity=5, Facility=4} body: 74 65 73 74 35 20 6C 6F 61 64 62 61 6C 61 6E 63 <span style="color:#ff0000;">test5 loadbalanc</span> }</p>

<p style="margin-left:0pt;"><strong><strong>案例16：</strong></strong><strong><span style="color:#000000;"><strong>Test Body </strong></span></strong><strong><strong>Event Serializers</strong></strong></p>

<p style="margin-left:0pt;"><strong><strong>#文件名：</strong></strong>case16_body.conf</p>

<p style="margin-left:0pt;"><strong><strong>#配置内容：</strong></strong></p>

<p style="margin-left:0pt;">a1.sources.r1.type = org.apache.flume.source.http.HTTPSource</p>

<p style="margin-left:0pt;">a1.sources.r1.port = 5140</p>

<p style="margin-left:0pt;">a1.sources.r1.host = localhost</p>

<p style="margin-left:0pt;">a1.sources.r1.channels = c1</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Describe the sink</p>

<p style="margin-left:0pt;">a1.sinks.k1.type = file_roll</p>

<p style="margin-left:0pt;">a1.sinks.k1.channel = c1</p>

<p style="margin-left:0pt;">a1.sinks.k1.sink.directory = /var/log/flume</p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sinks.k1.sink.serializer = text</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sinks.k1.sink.serializer.appendNewline = false</span></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Use a channel which buffers events in memory</p>

<p style="margin-left:0pt;">a1.channels.c1.type = memory</p>

<p style="margin-left:0pt;">a1.channels.c1.capacity = 1000</p>

<p style="margin-left:0pt;">a1.channels.c1.transactionCapacity = 100</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><strong><strong>#</strong></strong><strong><strong>启动</strong></strong></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">flume-ng agent -c . -f </span><span style="color:#00b050;">case16_body.conf</span><span style="color:#00b050;"> -n a</span><span style="color:#00b050;">1</span><span style="color:#00b050;">-Dflume.root.logger=INFO,console</span></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><strong><strong>#</strong></strong><strong><strong>生成测试</strong></strong><strong><strong>log</strong></strong></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">curl -X POST -d '[{ "headers" :{"host":"cc-staging-loginmgr2"},"body" : "TEST1 BODY TEXT"}]' http://localhost:5140</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">curl -X POST -d '[{ "headers" :{"host":"cc-staging-loginmgr2"},"body" : "TEST2 BODY TEXT"}]' http://localhost:5140</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">curl -X POST -d '[{ "headers" :{"host":"cc-staging-loginmgr2"},"body" : "TEST3 BODY TEXT"}]' http://localhost:5140</span></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><strong><strong>#</strong></strong><strong><strong>查看</strong></strong><strong><strong>file roll </strong></strong><strong><strong>文件中的文本内容</strong></strong></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">cat /var/log/flume/1370675739270-1</span></p>

<p style="margin-left:0pt;"><span style="color:#ff0000;">TEST1 BODY TEXT</span></p>

<p style="margin-left:0pt;"><span style="color:#ff0000;">TEST2 BODY TEXT</span></p>

<p style="margin-left:0pt;"><span style="color:#ff0000;">TEST3 BODY TEXT</span></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;">#Avro Event Serializer</p>

<p style="margin-left:0pt;">Alias: avro_event. This interceptor serializes Flume events into an Avro container file</p>

<p style="margin-left:0pt;">把flume event变成avro 中包含的文件</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><strong><strong>案例17：</strong></strong><strong><span style="color:#000000;"><strong>Test </strong></span></strong><strong><span style="color:#000000;"><strong>Timestamp Interceptor</strong></span></strong></p>

<p style="margin-left:0pt;"><strong><strong>#文件名：</strong></strong>case17_timestamp_hostname.conf</p>

<p style="margin-left:0pt;"><strong><strong>#配置内容：</strong></strong></p>

<p style="margin-left:0pt;"># Name the components on this agent</p>

<p style="margin-left:0pt;">a1.sources = r1</p>

<p style="margin-left:0pt;">a1.sinks = k1</p>

<p style="margin-left:0pt;">a1.channels = c1</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Describe/configure the source</p>

<p style="margin-left:0pt;">a1.sources.r1.type = syslogtcp</p>

<p style="margin-left:0pt;">a1.sources.r1.bind = 0.0.0.0</p>

<p style="margin-left:0pt;">a1.sources.r1.port = 5140</p>

<p style="margin-left:0pt;">a1.sources.r1.channels = c1</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sources.r1.interceptors = i1 i2</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sources.r1.interceptors.i1.preserveExisting = false</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sources.r1.interceptors.i1.type = timestamp</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sources.r1.interceptors.i2.type = host</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sources.r1.interceptors.i2.hostHeader = hostname</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sources.r1.interceptors.i2.useIP = false</span></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Describe the sink</p>

<p style="margin-left:0pt;">a1.sinks.k1.type = hdfs</p>

<p style="margin-left:0pt;">a1.sinks.k1.channel = c1</p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sinks.k1.hdfs.path = hdfs://master:9000/user/</span><a href="http://www.linuxidc.com/topicnews.aspx?tid=13" rel="nofollow"><span style="color:#00b050;">Hadoop</span></a><span style="color:#00b050;">/flume/collected/%Y-%m-%d/%H%M</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sinks.k1.hdfs.filePrefix = %{hostname}.</span></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Use a channel which buffers events in memory</p>

<p style="margin-left:0pt;">a1.channels.c1.type = memory</p>

<p style="margin-left:0pt;">a1.channels.c1.capacity = 1000</p>

<p style="margin-left:0pt;">a1.channels.c1.transactionCapacity = 100</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><strong><strong>#</strong></strong><strong><strong>启动</strong></strong><strong><strong>agent</strong></strong></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">flume-ng agent -c . -f </span><span style="color:#00b050;">case17_timestamp_hostname.conf</span><span style="color:#00b050;"> -n a1 -Dflume.root.logger=INFO,console</span></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><strong><strong>#</strong></strong><strong><strong>生成测试</strong></strong><strong><strong>log</strong></strong></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">echo "</span><span style="color:#ff0000;">&lt;37&gt;test dynamic interceptor</span><span style="color:#00b050;">" | nc localhost 5140</span></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><strong><strong>#</strong></strong><strong><strong>查看</strong></strong><strong><strong>hdfs</strong></strong><strong><strong>生成的文件</strong></strong><strong><strong>,</strong></strong><strong><strong>可以看到</strong></strong><strong><strong>timestamp</strong></strong><strong><strong>和</strong></strong><strong><strong>hostname</strong></strong><strong><strong>都已经生成在</strong></strong><strong><strong>header</strong></strong><strong><strong>里面</strong></strong><strong><strong>,</strong></strong><strong><strong>可以根据自定义的格式生成文件夹</strong></strong></p>

<p style="margin-left:0pt;">./hadoop dfs -ls hdfs://172.25.4.35:9000/user/hadoop/flume/collected/<span style="color:#ff0000;">2013-06-16/2331</span>/</p>

<p style="margin-left:0pt;">Found 1 items</p>

<p style="margin-left:0pt;">-rw-r--r-- 3 root supergroup 140 2013-06-16 23:32 /user/hadoop/flume/collected/2013-06-16/2331/cc-staging-loginmgr2..1371450697118</p>

<p style="margin-left:0pt;"><strong><strong>案例18：</strong></strong><strong><span style="color:#000000;"><strong>Test static</strong></span></strong><strong><span style="color:#000000;"><strong> Interceptor</strong></span></strong></p>

<p style="margin-left:0pt;"><strong><strong>#文件名：</strong></strong>case18_static.conf</p>

<p style="margin-left:0pt;"><strong><strong>#配置内容：</strong></strong></p>

<p style="margin-left:0pt;"># Name the components on this agent</p>

<p style="margin-left:0pt;">a1.sources = r1</p>

<p style="margin-left:0pt;">a1.sinks = k1</p>

<p style="margin-left:0pt;">a1.channels = c1</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"># Describe/configure the source</p>

<p style="margin-left:0pt;">a1.sources.r1.type = syslogtcp</p>

<p style="margin-left:0pt;">a1.sources.r1.port = 5140</p>

<p style="margin-left:0pt;">a1.sources.r1.host = localhost</p>

<p style="margin-left:0pt;">a1.sources.r1.channels = c1</p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sources.r1.interceptors = i1</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sources.r1.interceptors.i1.type = static</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sources.r1.interceptors.i1.key = datacenter</span></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">a1.sources.r1.interceptors.i1.value = NEW_YORK</span></p>

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

<p style="margin-left:0pt;"><strong><strong>#</strong></strong><strong><strong>启动</strong></strong><strong><strong>agent</strong></strong></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">flume-ng agent -c . -f </span><span style="color:#00b050;">case18_static.conf</span><span style="color:#00b050;"> -n a1 -Dflume.root.logger=INFO,console</span></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><strong><strong>#</strong></strong><strong><strong>生成测试</strong></strong><strong><strong>log</strong></strong></p>

<p style="margin-left:0pt;"><span style="color:#00b050;">echo "</span><span style="color:#ff0000;">&lt;37&gt;test1 static interceptor</span><span style="color:#00b050;">" | nc localhost 5140</span></p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"><strong><strong>#</strong></strong><strong><strong>查看</strong></strong><strong><strong>console</strong></strong><strong><strong>输出结果</strong></strong></p>

<p style="margin-left:0pt;">2013-06-17 00:15:38,453 (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org.apache.flume.sink.LoggerSink.process(LoggerSink.java:70)] Event: { headers:{<span style="color:#ff0000;">Severity=5, Facility=4, datacenter=NEW_YORK</span>} body: 74 65 73 74 31 20 73 74 61 74 69 63 20 69 6E 74 <span style="color:#ff0000;">test1 static int</span> }</p>            </div>
                </div>