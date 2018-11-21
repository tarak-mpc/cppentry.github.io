---
layout:     post
title:      apache flume collector 安装
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/chen978616649/article/details/51372427				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h1 id="H23001FlumeCollector5B8988C5" style="font-size:28px;font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-weight:400;line-height:1.1;color:rgb(51,51,51);">
<span>2、Flume Collector安装（通过extends AbstractSink implements Configurable，直接写入数据库）</span></h1>
<h2 id="H2.15B8988C573AF5883" style="font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-weight:400;line-height:1.1;color:rgb(51,51,51);font-size:25px;">
<span>2.1 安装环境</span></h2>
<p style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:20px;">
系统:</p>
<p style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:20px;">
CentOS release 6.6</p>
<p style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:20px;">
软件:</p>
<p style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:20px;">
flume-collector.tar.gz</p>
<p style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:20px;">
 </p>
<p style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:20px;">
 </p>
<h2 id="H2.25B8988C56B659AA4" style="font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-weight:400;line-height:1.1;color:rgb(51,51,51);font-size:25px;">
<span>2.2 安装步骤</span></h2>
<h3 id="H2.2.190E87F72FlumeCollector" style="font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-weight:400;line-height:1.1;color:rgb(51,51,51);font-size:22px;">
<span>2.2.1 部署Flume Collector</span></h3>
<p style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:20px;">
<span>具体脚本（jyapp用户）：</span></p>
<table style="border-collapse:collapse;border-spacing:0px;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:20px;"><tbody><tr><td style="line-height:1.42857143;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
<div>
<p>cd  /home/jyapp</p>
<p>tar  -zxvf  flume-collector.tar.gz</p>
<div class="wikimodel-emptyline"></div>
<p>cd  flume-collector</p>
<p> </p>
<p><span style="color:rgb(0,0,255);">#修改flume环境变量</span></p>
<p>vi  conf/flume-env.sh   </p>
<p><span>具体配置解析：（注意：红色需修改）</span></p>
<p>------------------------------------------------------</p>
<p>export JAVA_OPTS="-Xms1000m -Xmx1000m  -Dflume.root.logger=INFO,LOGFILE -Dflume.monitoring.type=http  -Dflume.monitoring.port=34545  -Dcom.sun.management.jmxremote -Djava.rmi.server.hostname=<span style="color:rgb(255,0,0);">本机IP</span>  -Dcom.sun.management.jmxremote.port=9999
 -Dcom.sun.management.jmxremote.authenticate=false  -Dcom.sun.management.jmxremote.ssl=false"</p>
<p>-------------------------------------------------------------------------------------</p>
<p> </p>
<p> </p>
<p><span style="color:rgb(0,0,255);">#修改Collector脚本</span></p>
<p>vi  conf/collector.conf  </p>
<p><span>具体配置解析：（注意：红色需修改）</span></p>
<p>------------------------------------------------------</p>
<p>collector.sources = r1</p>
<p>collector.channels = c1 c2</p>
<p>collector.sinks = k1 k2 k3 k4 k5 k6</p>
<p> </p>
<p> </p>
<p>collector.sources.r1.type = avro</p>
<p>collector.sources.r1.bind = <span style="color:rgb(255,0,0);">收集端IP   #本机IP</span></p>
<p>collector.sources.r1.port = <span style="color:rgb(255,0,0);">收集端绑定端口  #本机接口消息端口</span></p>
<p> </p>
<p>collector.channels.c1.type = memory</p>
<p>collector.channels.c1.capacity = 10000</p>
<p>collector.channels.c1.transactionCapacity = 10000</p>
<p>collector.channels.c1.byteCapacityBufferPercentage = 30</p>
<p>collector.channels.c1.byteCapacity = 1048576000</p>
<p> </p>
<p>collector.channels.c2.type = memory</p>
<p>collector.channels.c2.capacity = 10000</p>
<p>collector.channels.c2.transactionCapacity = 10000</p>
<p>collector.channels.c2.byteCapacityBufferPercentage = 30</p>
<p>collector.channels.c2.byteCapacity = 1048576000</p>
<p> </p>
<p>collector.sinks.k1.type = com.jy.modules.platform.flume.sink.OracleJdbcSink</p>
<p>collector.sinks.k1.url =<span style="color:rgb(255,0,0);"> jdbc:oracle:thin:@IP:PORT:数据库名</span></p>
<p>collector.sinks.k1.user =<span style="color:rgb(255,0,0);">用户名</span></p>
<p>collector.sinks.k1.password =<span style="color:rgb(255,0,0);">密码</span></p>
<p> </p>
<p>collector.sinks.k2.type = com.jy.modules.platform.flume.sink.OracleJdbcSink</p>
<p>collector.sinks.k2.url = <span style="color:rgb(255,0,0);">jdbc:oracle:thin:@IP:PORT:数据库名</span></p>
<p>collector.sinks.k2.user =<span style="color:rgb(255,0,0);">用户名</span></p>
<p>collector.sinks.k2.password =<span style="color:rgb(255,0,0);">密码</span></p>
<p> </p>
<p>collector.sinks.k3.type = com.jy.modules.platform.flume.sink.OracleJdbcSink</p>
<p>collector.sinks.k3.url = <span style="color:rgb(255,0,0);">jdbc:oracle:thin:@IP:PORT:数据库名</span></p>
<p>collector.sinks.k3.user =<span style="color:rgb(255,0,0);">用户名</span></p>
<p>collector.sinks.k3.password =<span style="color:rgb(255,0,0);">密码</span></p>
<p> </p>
<p>collector.sinks.k4.type = com.jy.modules.platform.flume.sink.OracleJdbcSink</p>
<p>collector.sinks.k4.url = <span style="color:rgb(255,0,0);">jdbc:oracle:thin:@IP:PORT:数据库名</span></p>
<p>collector.sinks.k4.user =<span style="color:rgb(255,0,0);">用户名</span></p>
<p>collector.sinks.k4.password =<span style="color:rgb(255,0,0);">密码</span></p>
<p> </p>
<p>collector.sinks.k5.type = com.jy.modules.platform.flume.sink.OracleJdbcSink</p>
<p>collector.sinks.k5.url = <span style="color:rgb(255,0,0);">jdbc:oracle:thin:@IP:PORT:数据库名</span></p>
<p>collector.sinks.k5.user =<span style="color:rgb(255,0,0);">用户名</span></p>
<p>collector.sinks.k5.password =<span style="color:rgb(255,0,0);">密码</span></p>
<p> </p>
<p>collector.sinks.k6.type = com.jy.modules.platform.flume.sink.OracleJdbcSink</p>
<p>collector.sinks.k6.url =<span style="color:rgb(255,0,0);"> jdbc:oracle:thin:@IP:PORT:数据库名</span></p>
<p>collector.sinks.k6.user =<span style="color:rgb(255,0,0);">用户名</span></p>
<p>collector.sinks.k6.password =<span style="color:rgb(255,0,0);">密码</span></p>
<p> </p>
<p>collector.sources.r1.channels = c1 c2</p>
<p>collector.sinks.k1.channel = c1</p>
<p>collector.sinks.k2.channel = c1</p>
<p>collector.sinks.k3.channel = c1</p>
<p>collector.sinks.k4.channel = c2</p>
<p>collector.sinks.k5.channel = c2</p>
<p>collector.sinks.k6.channel = c2</p>
<p> </p>
<p>collector.sources.r1.selector.type = multiplexing</p>
<p>collector.sources.r1.selector.header = flume.client.system.flag</p>
<p>collector.sources.r1.selector.mapping.S000 = c1</p>
<p>collector.sources.r1.selector.mapping.S001 = c2</p>
<p>collector.sources.r1.selector.mapping.S002 = c1</p>
<p>collector.sources.r1.selector.mapping.S003 = c2</p>
<p>collector.sources.r1.selector.mapping.S004 = c1</p>
<p>collector.sources.r1.selector.mapping.S005 = c2</p>
<p>collector.sources.r1.selector.mapping.S006 = c1</p>
<p>collector.sources.r1.selector.mapping.S007 = c2</p>
<p>collector.sources.r1.selector.mapping.S008 = c1</p>
<p>collector.sources.r1.selector.mapping.S009 = c2</p>
<p>collector.sources.r1.selector.default = c1</p>
<p> </p>
<p>collector.sinkgroups = g1 g2</p>
<p>collector.sinkgroups.g1.sinks = k1 k2 k3</p>
<p>collector.sinkgroups.g1.processor.type = load_balance</p>
<p>collector.sinkgroups.g1.processor.backoff = true</p>
<p>collector.sinkgroups.g1.processor.selector = round_robin</p>
<p> </p>
<p>collector.sinkgroups.g2.sinks = k4 k5 k6</p>
<p>collector.sinkgroups.g2.processor.type = load_balance</p>
<p>collector.sinkgroups.g2.processor.backoff = true</p>
<p>collector.sinkgroups.g2.processor.selector = round_robin</p>
<p>-------------------------------------------------------------------------------------</p>
<p> </p>
<p><span style="color:rgb(0,0,255);">后台启动collector</span></p>
<p>cd  /home/jyapp/flume-collector</p>
<p>bin/flume-ng  agent  --conf conf  --conf-file  conf/collector.conf  --name  collector   &amp;</p>
<p> </p>
<p><span style="color:rgb(0,0,255);">停止agent，使用kill命令</span></p>
<p> </p>
</div>
</td>
</tr></tbody></table>            </div>
                </div>