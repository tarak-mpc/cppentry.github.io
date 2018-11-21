---
layout:     post
title:      Flume：spark-project项目的flume配置
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/weixin_38750084/article/details/82762592				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>1、将flume-ng-1.5.0-cdh5.3.6.tar.gz使用WinSCP拷贝到sparkproject1的/usr/local目录下。</p>

<p>2、对flume进行解压缩：tar -zxvf flume-ng-1.5.0-cdh5.3.6.tar.gz</p>

<p>3、对flume目录进行重命名：mv apache-flume-1.5.0-cdh5.3.6-bin flume</p>

<p>4、配置scala相关的环境变量</p>

<p>vi ~/.bashrc</p>

<p>export FLUME_HOME=/usr/local/flume</p>

<p>export FLUME_CONF_DIR=$FLUME_HOME/conf</p>

<p>export PATH=$FLUME_HOME/bin</p>

<p>source ~/.bashrc</p>

<p> </p>

<table border="1" cellpadding="1" cellspacing="1"><tbody><tr><td>
			<p>vi /usr/local/flume/conf/flume-conf.properties</p>

			<p>#agent1表示代理名称</p>

			<p>agent1.sources=source1</p>

			<p>agent1.sinks=sink1</p>

			<p>agent1.channels=channel1</p>

			<p>#配置source1 agent1.sources.source1.type=spooldir</p>

			<p><span style="color:#f33b45;">agent1.sources.source1.spoolDir=/usr/local/logs     //设置监控的文件夹 </span></p>

			<p>agent1.sources.source1.channels=channel1</p>

			<p>agent1.sources.source1.fileHeader = false</p>

			<p>agent1.sources.source1.interceptors = i1</p>

			<p>agent1.sources.source1.interceptors.i1.type = timestamp</p>

			<p>#配置channel1</p>

			<p>agent1.channels.channel1.type=file</p>

			<p>agent1.channels.channel1.checkpointDir=/usr/local/logs_tmp_cp</p>

			<p>agent1.channels.channel1.dataDirs=/usr/local/logs_tmp</p>

			<p>#配置sink1</p>

			<p>agent1.sinks.sink1.type=hdfs</p>

			<p><span style="color:#f33b45;">agent1.sinks.sink1.hdfs.path=hdfs://sparkproject1:9000/logs  //监控日志写入到hdfs </span></p>

			<p>agent1.sinks.sink1.hdfs.fileType=DataStream</p>

			<p>agent1.sinks.sink1.hdfs.writeFormat=TEXT</p>

			<p>agent1.sinks.sink1.hdfs.rollInterval=1</p>

			<p>agent1.sinks.sink1.channel=channel1</p>

			<p>agent1.sinks.sink1.hdfs.filePrefix=%Y-%m-%d</p>

			<p> </p>

			<p> </p>
			</td>
		</tr></tbody></table><p> </p>            </div>
                </div>