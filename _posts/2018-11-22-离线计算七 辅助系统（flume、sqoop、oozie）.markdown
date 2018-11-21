---
layout:     post
title:      离线计算七 辅助系统（flume、sqoop、oozie）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>课程大纲（辅助系统）</p>
<p>离线辅助系统 数据接入 Flume介绍<br>
Flume组件<br>
Flume实战案例<br>
任务调度 调度器基础<br>
市面上调度工具<br>
Oozie的使用<br>
Oozie的流程定义详解<br>
数据导出 sqoop基础知识<br>
sqoop实战及原理<br>
Sqoop数据导入实战<br>
Sqoop数据导出实战<br>
Sqoop作业操作<br>
Sqoop的原理</p>
<p>学习目标：<br>
1、理解flume、sqoop、oozie的应用场景<br>
2、理解flume、sqoop、oozie的基本原理<br>
3、掌握flume、sqoop、oozie的使用方法</p>
<p>前言<br>
在一个完整的大数据处理系统中，除了hdfs+mapreduce+hive组成分析系统的核心之外，还需要数据采集、结果数据导出、任务调度等不可或缺的辅助系统，而这些辅助工具在hadoop生态体系中都有便捷的开源框架，如图所示：</p>
<ol>
<li>日志采集框架Flume<br>
1.1 Flume介绍<br>
1.1.1 概述<br>
Flume是一个分布式、可靠、和高可用的海量日志采集、聚合和传输的系统。<br>
Flume可以采集文件，socket数据包等各种形式源数据，又可以将采集到的数据输出到HDFS、hbase、hive、kafka等众多外部存储系统中<br>
一般的采集需求，通过对flume的简单配置即可实现<br>
Flume针对特殊场景也具备良好的自定义扩展能力，因此，flume可以适用于大部分的日常数据采集场景</li>
</ol>
<p>1.1.2 运行机制<br>
Flume分布式系统中最核心的角色是agent，flume采集系统就是由一个个agent所连接起来形成<br>
每一个agent相当于一个数据传递员，内部有三个组件：<br>
Source：采集源，用于跟数据源对接，以获取数据<br>
Sink：下沉地，采集数据的传送目的，用于往下一级agent传递数据或者往最终存储系统传递数据<br>
Channel：angent内部的数据传输通道，用于从source将数据传递到sink</p>
<p>1.1.4 Flume采集系统结构图</p>
<ol>
<li>
<p>简单结构<br>
单个agent采集数据</p>
</li>
<li>
<p>复杂结构<br>
多级agent之间串联</p>
</li>
</ol>
<p>1.2 Flume实战案例<br>
1.2.1 Flume的安装部署<br>
Flume的安装非常简单，只需要解压即可，当然，前提是已有hadoop环境<br>
上传安装包到数据源所在节点上<br>
然后解压  tar -zxvf apache-flume-1.6.0-bin.tar.gz<br>
然后进入flume的目录，<a href="http://xn--confflume-env-s40un3t5v9gop1c.sh" rel="nofollow">修改conf下的flume-env.sh</a>，在里面配置JAVA_HOME</p>
<p>2、根据数据采集的需求配置采集方案，描述在配置文件中(文件名可任意自定义)<br>
3、指定采集方案配置文件，在相应的节点上启动flume agent</p>
<p>先用一个最简单的例子来测试一下程序环境是否正常<br>
先在flume的conf目录下新建一个文件<br>
vi   netcat-logger.conf</p>
<h1><a id="agenta1sources__r1a1sinks__k1a1channels__c1_sourcer1a1sourcesr1type__netcata1sourcesr1bind__localhosta1sourcesr1port__44444_sinkk1a1sinksk1type__logger_channela1channelsc1type__memorya1channelsc1capacity__1000a1channelsc1transactionCapacity__100_source__channel___sinka1sourcesr1channels__c1a1sinksk1channel__c1_84"></a>定义这个agent中各组件的名字a1.sources = r1a1.sinks = k1a1.channels = c1# 描述和配置source组件：r1a1.sources.r1.type = netcata1.sources.r1.bind = localhosta1.sources.r1.port = 44444# 描述和配置sink组件：k1a1.sinks.k1.type = logger# 描述和配置channel组件，此处使用是内存缓存的方式a1.channels.c1.type = memorya1.channels.c1.capacity = 1000a1.channels.c1.transactionCapacity = 100# 描述和配置source  channel   sink之间的连接关系a1.sources.r1.channels = c1a1.sinks.k1.channel = c1</h1>
<p>启动agent去采集数据<br>
bin/flume-ng agent -c conf -f conf/netcat-logger.conf -n a1  -Dflume.root.logger=INFO,console<br>
-c conf   指定flume自身的配置文件所在目录<br>
-f conf/netcat-logger.con  指定我们所描述的采集方案<br>
-n a1  指定我们这个agent的名字<br>
测试<br>
先要往agent采集监听的端口上发送数据，让agent有数据可采<br>
随便在一个能跟agent节点联网的机器上<br>
telnet anget-hostname  port   （telnet localhost 44444）</p>
<p>1.2.2 采集案例<br>
1、采集目录到HDFS<br>
采集需求：某服务器的某特定目录下，会不断产生新的文件，每当有新文件出现，就需要把文件采集到HDFS中去<br>
根据需求，首先定义以下3大要素<br>
采集源，即source——监控文件目录 :  spooldir<br>
下沉目标，即sink——HDFS文件系统  :  hdfs sink<br>
source和sink之间的传递通道——channel，可用file channel 也可以用内存channel</p>
<p>配置文件编写：<br>
#定义三大组件的名称agent1.sources = source1agent1.sinks = sink1agent1.channels = channel1# 配置source组件agent1.sources.source1.type = spooldiragent1.sources.source1.spoolDir = /home/hadoop/logs/agent1.sources.source1.fileHeader = false#配置拦截器agent1.sources.source1.interceptors = i1agent1.sources.source1.interceptors.i1.type = hostagent1.sources.source1.interceptors.i1.hostHeader = hostname# 配置sink组件agent1.sinks.sink1.type = hdfsagent1.sinks.sink1.hdfs.path =hdfs://hdp-node-01:9000/weblog/flume-collection/%y-%m-%d/%H-%Magent1.sinks.sink1.hdfs.filePrefix = access_logagent1.sinks.sink1.hdfs.maxOpenFiles = 5000agent1.sinks.sink1.hdfs.batchSize= 100agent1.sinks.sink1.hdfs.fileType = DataStreamagent1.sinks.sink1.hdfs.writeFormat =Textagent1.sinks.sink1.hdfs.rollSize = 102400agent1.sinks.sink1.hdfs.rollCount = 1000000agent1.sinks.sink1.hdfs.rollInterval = 60#agent1.sinks.sink1.hdfs.round = true#agent1.sinks.sink1.hdfs.roundValue = 10#agent1.sinks.sink1.hdfs.roundUnit = minuteagent1.sinks.sink1.hdfs.useLocalTimeStamp = true# Use a channel which buffers events in memoryagent1.channels.channel1.type = memoryagent1.channels.channel1.keep-alive = 120agent1.channels.channel1.capacity = 500000agent1.channels.channel1.transactionCapacity = 600# Bind the source and sink to the channelagent1.sources.source1.channels = channel1agent1.sinks.sink1.channel = channel1</p>
<p>Channel参数解释：<br>
capacity：默认该通道中最大的可以存储的event数量<br>
trasactionCapacity：每次最大可以从source中拿到或者送到sink中的event数量<br>
keep-alive：event添加到通道中或者移出的允许时间</p>
<p>2、采集文件到HDFS<br>
采集需求：比如业务系统使用log4j生成的日志，日志内容不断增加，需要把追加到日志文件中的数据实时采集到hdfs</p>
<p>根据需求，首先定义以下3大要素<br>
采集源，即source——监控文件内容更新 :  exec  ‘tail -F file’<br>
下沉目标，即sink——HDFS文件系统  :  hdfs sink<br>
Source和sink之间的传递通道——channel，可用file channel 也可以用 内存channel</p>
<p>配置文件编写：<br>
agent1.sources = source1agent1.sinks = sink1agent1.channels = channel1# Describe/configure tail -F source1agent1.sources.source1.type = execagent1.sources.source1.command = tail -F /home/hadoop/logs/access_logagent1.sources.source1.channels = channel1#configure host for sourceagent1.sources.source1.interceptors = i1agent1.sources.source1.interceptors.i1.type = hostagent1.sources.source1.interceptors.i1.hostHeader = hostname# Describe sink1agent1.sinks.sink1.type = hdfs#a1.sinks.k1.channel = c1agent1.sinks.sink1.hdfs.path =hdfs://hdp-node-01:9000/weblog/flume-collection/%y-%m-%d/%H-%Magent1.sinks.sink1.hdfs.filePrefix = access_logagent1.sinks.sink1.hdfs.maxOpenFiles = 5000agent1.sinks.sink1.hdfs.batchSize= 100agent1.sinks.sink1.hdfs.fileType = DataStreamagent1.sinks.sink1.hdfs.writeFormat =Textagent1.sinks.sink1.hdfs.rollSize = 102400agent1.sinks.sink1.hdfs.rollCount = 1000000agent1.sinks.sink1.hdfs.rollInterval = 60agent1.sinks.sink1.hdfs.round = trueagent1.sinks.sink1.hdfs.roundValue = 10agent1.sinks.sink1.hdfs.roundUnit = minuteagent1.sinks.sink1.hdfs.useLocalTimeStamp = true# Use a channel which buffers events in memoryagent1.channels.channel1.type = memoryagent1.channels.channel1.keep-alive = 120agent1.channels.channel1.capacity = 500000agent1.channels.channel1.transactionCapacity = 600# Bind the source and sink to the channelagent1.sources.source1.channels = channel1agent1.sinks.sink1.channel = channel1</p>
<p>1.3 更多source和sink组件<br>
Flume支持众多的source和sink类型，详细手册可参考官方文档<br>
<a href="http://flume.apache.org/FlumeUserGuide.html" rel="nofollow">http://flume.apache.org/FlumeUserGuide.html</a><br>
2. 工作流调度器azkaban<br>
2.1 概述<br>
2.1.1为什么需要工作流调度系统<br>
一个完整的数据分析系统通常都是由大量任务单元组成：<br>
shell脚本程序，java程序，mapreduce程序、hive脚本等<br>
各任务单元之间存在时间先后及前后依赖关系<br>
为了很好地组织起这样的复杂执行计划，需要一个工作流调度系统来调度执行；</p>
<p>例如，我们可能有这样一个需求，某个业务系统每天产生20G原始数据，我们每天都要对其进行处理，处理步骤如下所示：<br>
通过Hadoop先将原始数据同步到HDFS上；<br>
借助MapReduce计算框架对原始数据进行转换，生成的数据以分区表的形式存储到多张Hive表中；<br>
需要对Hive中多个表的数据进行JOIN处理，得到一个明细数据Hive大表；<br>
将明细数据进行复杂的统计分析，得到结果报表信息；<br>
需要将统计分析得到的结果数据同步到业务系统中，供业务调用使用。</p>
<p>2.1.2 工作流调度实现方式<br>
简单的任务调度：直接使用linux的crontab来定义；<br>
复杂的任务调度：开发调度平台<br>
或使用现成的开源调度系统，比如ooize、azkaban等</p>
<p>2.1.3 常见工作流调度系统<br>
市面上目前有许多工作流调度器<br>
在hadoop领域，常见的工作流调度器有Oozie, Azkaban,Cascading,Hamake等</p>
<p>2.1.4 各种调度工具特性对比<br>
下面的表格对上述四种hadoop工作流调度器的关键特性进行了比较，尽管这些工作流调度器能够解决的需求场景基本一致，但在设计理念，目标用户，应用场景等方面还是存在显著的区别，在做技术选型的时候，可以提供参考<br>
特性 Hamake Oozie Azkaban Cascading<br>
工作流描述语言 XML XML (xPDL based) text file with key/value pairs Java API<br>
依赖机制 data-driven explicit explicit explicit<br>
是否要web容器 No Yes Yes No<br>
进度跟踪 console/log messages web page web page Java API<br>
Hadoop job调度支持 no yes yes yes<br>
运行模式 command line utility daemon daemon API<br>
Pig支持 yes yes yes yes<br>
事件通知 no no no yes<br>
需要安装 no yes yes no<br>
支持的hadoop版本 0.18+ 0.20+ currently unknown 0.18+<br>
重试支持 no workflownode evel yes yes<br>
运行任意命令 yes yes yes yes<br>
Amazon EMR支持 yes no currently unknown yes<br>
2.1.5 Azkaban与Oozie对比<br>
对市面上最流行的两种调度器，给出以下详细对比，以供技术选型参考。总体来说，ooize相比azkaban是一个重量级的任务调度系统，功能全面，但配置使用也更复杂。如果可以不在意某些功能的缺失，轻量级调度器azkaban是很不错的候选对象。<br>
详情如下：<br>
功能<br>
两者均可以调度mapreduce,pig,java,脚本工作流任务<br>
两者均可以定时执行工作流任务</p>
<p>工作流定义<br>
Azkaban使用Properties文件定义工作流<br>
Oozie使用XML文件定义工作流</p>
<p>工作流传参<br>
Azkaban支持直接传参，例如<span class="katex--inline"><span class="katex"><span class="katex-mathml"><math><semantics><mrow><mrow><mi>i</mi><mi>n</mi><mi>p</mi><mi>u</mi><mi>t</mi></mrow><mi>O</mi><mi>o</mi><mi>z</mi><mi>i</mi><mi>e</mi><mi mathvariant="normal">支</mi><mi mathvariant="normal">持</mi><mi mathvariant="normal">参</mi><mi mathvariant="normal">数</mi><mi mathvariant="normal">和</mi><mi>E</mi><mi>L</mi><mi mathvariant="normal">表</mi><mi mathvariant="normal">达</mi><mi mathvariant="normal">式</mi><mi mathvariant="normal">，</mi><mi mathvariant="normal">例</mi><mi mathvariant="normal">如</mi></mrow><annotation encoding="application/x-tex">{input}
Oozie支持参数和EL表达式，例如</annotation></semantics></math></span><span class="katex-html" aria-hidden="true"><span class="base"><span class="strut" style="height: 0.87777em; vertical-align: -0.19444em;"></span><span class="mord"><span class="mord mathit">i</span><span class="mord mathit">n</span><span class="mord mathit">p</span><span class="mord mathit">u</span><span class="mord mathit">t</span></span><span class="mord mathit" style="margin-right: 0.02778em;">O</span><span class="mord mathit">o</span><span class="mord mathit" style="margin-right: 0.04398em;">z</span><span class="mord mathit">i</span><span class="mord mathit">e</span><span class="mord cjk_fallback">支</span><span class="mord cjk_fallback">持</span><span class="mord cjk_fallback">参</span><span class="mord cjk_fallback">数</span><span class="mord cjk_fallback">和</span><span class="mord mathit" style="margin-right: 0.05764em;">E</span><span class="mord mathit">L</span><span class="mord cjk_fallback">表</span><span class="mord cjk_fallback">达</span><span class="mord cjk_fallback">式</span><span class="mord cjk_fallback">，</span><span class="mord cjk_fallback">例</span><span class="mord cjk_fallback">如</span></span></span></span></span>{fs:dirSize(myInputDir)}</p>
<p>定时执行<br>
Azkaban的定时执行任务是基于时间的<br>
Oozie的定时执行任务基于时间和输入数据</p>
<p>资源管理<br>
Azkaban有较严格的权限控制，如用户对工作流进行读/写/执行等操作<br>
Oozie暂无严格的权限控制</p>
<p>工作流执行<br>
Azkaban有两种运行模式，分别是solo server mode(executor server和web server部署在同一台节点)和multi server mode(executor server和web server可以部署在不同节点)<br>
Oozie作为工作流服务器运行，支持多用户和多工作流</p>
<p>工作流管理<br>
Azkaban支持浏览器以及ajax方式操作工作流<br>
Oozie支持命令行、HTTP REST、Java API、浏览器操作工作流</p>
<p>2.2 Azkaban介绍<br>
Azkaban是由Linkedin开源的一个批量工作流任务调度器。用于在一个工作流内以一个特定的顺序运行一组工作和流程。Azkaban定义了一种KV文件格式来建立任务之间的依赖关系，并提供一个易于使用的web用户界面维护和跟踪你的工作流。<br>
它有如下功能特点：<br>
Web用户界面<br>
方便上传工作流<br>
方便设置任务之间的关系<br>
调度工作流<br>
认证/授权(权限的工作)<br>
能够杀死并重新启动工作流<br>
模块化和可插拔的插件机制<br>
项目工作区<br>
工作流和任务的日志记录和审计</p>
<ol start="2">
<li>3 Azkaban安装部署<br>
准备工作<br>
Azkaban Web服务器<br>
azkaban-web-server-2.5.0.tar.gz<br>
Azkaban执行服务器<br>
azkaban-executor-server-2.5.0.tar.gz</li>
</ol>
<p>MySQL<br>
目前azkaban只支持 mysql,需安装mysql服务器,本文档中默认已安装好mysql服务器,并建立了 root用户,密码 root.</p>
<p>下载地址:<a href="http://azkaban.github.io/downloads.html" rel="nofollow">http://azkaban.github.io/downloads.html</a></p>
<p>安装<br>
将安装文件上传到集群,最好上传到安装 hive、sqoop的机器上,方便命令的执行<br>
在当前用户目录下新建 azkabantools目录,用于存放源安装文件.新建azkaban目录,用于存放azkaban运行程序<br>
azkaban web服务器安装<br>
解压azkaban-web-server-2.5.0.tar.gz<br>
命令: tar –zxvf azkaban-web-server-2.5.0.tar.gz<br>
将解压后的azkaban-web-server-2.5.0 移动到 azkaban目录中,并重新命名 webserver<br>
命令: mv azkaban-web-server-2.5.0 …/azkaban<br>
cd …/azkaban<br>
mv azkaban-web-server-2.5.0  server</p>
<p>azkaban 执行服器安装<br>
解压azkaban-executor-server-2.5.0.tar.gz<br>
命令:tar –zxvf azkaban-executor-server-2.5.0.tar.gz<br>
将解压后的azkaban-executor-server-2.5.0 移动到 azkaban目录中,并重新命名 executor<br>
命令:mv azkaban-executor-server-2.5.0  …/azkaban<br>
cd …/azkaban<br>
mv azkaban-executor-server-2.5.0  executor</p>
<p>azkaban脚本导入<br>
解压: azkaban-sql-script-2.5.0.tar.gz<br>
命令:tar –zxvf azkaban-sql-script-2.5.0.tar.gz<br>
将解压后的mysql 脚本,导入到mysql中:<br>
进入mysql<br>
mysql&gt; create database azkaban;<br>
mysql&gt; use azkaban;<br>
Database changed<br>
mysql&gt; source /home/hadoop/azkaban-2.5.0/create-all-sql-2.5.0.sql;</p>
<p>创建SSL配置<br>
参考地址: <a href="http://docs.codehaus.org/display/JETTY/How+to+configure+SSL" rel="nofollow">http://docs.codehaus.org/display/JETTY/How+to+configure+SSL</a><br>
命令: keytool -keystore keystore -alias jetty -genkey -keyalg RSA<br>
运行此命令后,会提示输入当前生成 keystor的密码及相应信息,输入的密码请劳记,信息如下:</p>
<p>输入keystore密码：<br>
再次输入新密码:<br>
您的名字与姓氏是什么？<br>
[Unknown]：<br>
您的组织单位名称是什么？<br>
[Unknown]：<br>
您的组织名称是什么？<br>
[Unknown]：<br>
您所在的城市或区域名称是什么？<br>
[Unknown]：<br>
您所在的州或省份名称是什么？<br>
[Unknown]：<br>
该单位的两字母国家代码是什么<br>
[Unknown]：  CN<br>
CN=Unknown, OU=Unknown, O=Unknown, L=Unknown, ST=Unknown, C=CN 正确吗？<br>
[否]：  y</p>
<p>输入的主密码<br>
（如果和 keystore 密码相同，按回车）：<br>
再次输入新密码:<br>
完成上述工作后,将在当前目录生成 keystore 证书文件,将keystore 考贝到 azkaban web服务器根目录中.如:cp keystore azkaban/webserver</p>
<p>配置文件<br>
注：先配置好服务器节点上的时区<br>
先生成时区配置文件Asia/Shanghai，用交互式命令 tzselect 即可<br>
拷贝该时区文件，覆盖系统本地时区配置<br>
cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime</p>
<p>azkaban web服务器配置<br>
进入azkaban web服务器安装目录 conf目录</p>
<p>修改azkaban.properties文件<br>
命令vi azkaban.properties<br>
内容说明如下:<br>
#Azkaban Personalization Settingsazkaban.name=Test                           #服务器UI名称,用于服务器上方显示的名字azkaban.label=My Local Azkaban                               #描述azkaban.color=#FF3601                                                 #UI颜色azkaban.default.servlet.path=/index                         #web.resource.dir=web/                                                 #默认根web目录default.timezone.id=Asia/Shanghai                           #默认时区,已改为亚洲/上海 默认为美国 #Azkaban UserManager classuser.manager.class=azkaban.user.XmlUserManager   #用户权限管理默认类user.manager.xml.file=conf/azkaban-users.xml              #用户配置,具体配置参加下文 #Loader for projectsexecutor.global.properties=conf/global.properties    # global配置文件所在位置azkaban.project.dir=projects                                                # database.type=mysql                                                              #数据库类型mysql.port=3306                                                                       #端口号mysql.host=hadoop03                                                      #数据库连接IPmysql.database=azkaban                                                       #数据库实例名mysql.user=root                                                                 #数据库用户名mysql.password=root                                                          #数据库密码mysql.numconnections=100                                                  #最大连接数 # Velocity dev modevelocity.dev.mode=false# Jetty服务器属性.jetty.maxThreads=25                                                               #最大线程数jetty.ssl.port=8443                                                                   #Jetty SSL端口jetty.port=8081                                                                         #Jetty端口jetty.keystore=keystore                                                          #SSL文件名jetty.password=123456                                                             #SSL文件密码jetty.keypassword=123456                                                      #Jetty主密码 与 keystore文件相同jetty.truststore=keystore                                                                #SSL文件名jetty.trustpassword=123456                                                   # SSL文件密码 # 执行服务器属性executor.port=12321                                                               #执行服务器端口 # 邮件设置mail.sender=xxxxxxxx@163.com                                       #<a href="http://xn--mail-9b1g352s8y3ateb.host=smtp.163.com" rel="nofollow">发送邮箱mail.host=smtp.163.com</a>                                                       #发送邮箱smtp地址mail.user=xxxxxxxx                                       #发送邮件时显示的名称mail.password=**********                                                 #邮箱密码job.failure.email=xxxxxxxx@163.com                              #任务失败时发送邮件的地址job.success.email=xxxxxxxx@163.com                            #任务成功时发送邮件的地址lockdown.create.projects=false                                           #cache.directory=cache                                                            #缓存目录</p>
<p>azkaban 执行服务器配置<br>
进入执行服务器安装目录conf,修改azkaban.properties<br>
vi azkaban.properties<br>
#Azkabandefault.timezone.id=Asia/Shanghai                                              #时区 # Azkaban JobTypes 插件配置azkaban.jobtype.plugin.dir=plugins/jobtypes                   #jobtype 插件所在位置 #Loader for projectsexecutor.global.properties=conf/global.propertiesazkaban.project.dir=projects #数据库设置database.type=mysql                                                                       #数据库类型(目前只支持mysql)mysql.port=3306                                                                                #数据库端口号mysql.host=192.168.20.200                                                           #数据库IP地址mysql.database=azkaban                                                                #数据库实例名mysql.user=azkaban                                                                         #数据库用户名mysql.password=oracle                                                                   #数据库密码mysql.numconnections=100                                                           #最大连接数 # 执行服务器配置executor.maxThreads=50                                                                #最大线程数executor.port=12321                                                               #端口号(如修改,请与web服务中一致)executor.flow.threads=30                                                                #线程数</p>
<p>用户配置<br>
进入azkaban web服务器conf目录,修改azkaban-users.xml<br>
vi azkaban-users.xml 增加 管理员用户<br>
                                        </p>
<p>启动<br>
web服务器<br>
在azkaban web服务器目录下执行启动命令<br>
bin/azkaban-web-start.sh<br>
注:在web服务器根目录运行</p>
<p>执行服务器<br>
在执行服务器目录下执行启动命令<br>
bin/azkaban-executor-start.sh ./<br>
注:只能要执行服务器根目录运行</p>
<p>启动完成后,在浏览器(建议使用谷歌浏览器)中输入https://服务器IP地址:8443 ,即可访问azkaban服务了.在登录中输入刚才新的户用名及密码,点击 login.</p>
<p>2.4 Azkaban实战<br>
Azkaba内置的任务类型支持command、java</p>
<p>Command类型单一job示例<br>
创建job描述文件<br>
vi command.job<br>
#command.jobtype=command                                                    command=echo ‘hello’</p>
<p>将job资源文件打包成zip文件<br>
zip command.job</p>
<p>通过azkaban的web管理平台创建project并上传job压缩包<br>
首先创建project</p>
<p>上传zip包</p>
<p>4、启动执行该job</p>
<p>Command类型多job工作流flow<br>
创建有依赖关系的多个job描述<br>
第一个job：foo.job</p>
<h1><a id="foojobtypecommandcommandecho_foo_362"></a>foo.jobtype=commandcommand=echo foo</h1>
<p>第二个job：bar.job依赖foo.job</p>
<h1><a id="barjobtypecommanddependenciesfoocommandecho_bar_364"></a>bar.jobtype=commanddependencies=foocommand=echo bar</h1>
<p>将所有job资源文件打到一个zip包中</p>
<p>在azkaban的web管理界面创建工程并上传zip包<br>
启动工作流flow</p>
<p>HDFS操作任务<br>
创建job描述文件</p>
<h1><a id="fsjobtypecommandcommandhomehadoopappshadoop261binhadoop_fs_mkdir_azaz_374"></a>fs.jobtype=commandcommand=/home/hadoop/apps/hadoop-2.6.1/bin/hadoop fs -mkdir /azaz</h1>
<p>将job资源文件打包成zip文件</p>
<p>3、通过azkaban的web管理平台创建project并上传job压缩包<br>
4、启动执行该job</p>
<p>MAPREDUCE任务<br>
Mr任务依然可以使用command的job类型来执行<br>
创建job描述文件，及mr程序jar包（示例中直接使用hadoop自带的example jar）</p>
<h1><a id="mrwcjobtypecommandcommandhomehadoopappshadoop261binhadoop__jar_hadoopmapreduceexamples261jar_wordcount_wordcountinput_wordcountazout_386"></a>mrwc.jobtype=commandcommand=/home/hadoop/apps/hadoop-2.6.1/bin/hadoop  jar hadoop-mapreduce-examples-2.6.1.jar wordcount /wordcount/input /wordcount/azout</h1>
<p>将所有job资源文件打到一个zip包中</p>
<p>3、在azkaban的web管理界面创建工程并上传zip包<br>
4、启动job<br>
HIVE脚本任务<br>
创建job描述文件和hive脚本<br>
Hive脚本： test.sql<br>
use default;drop table aztest;create table aztest(id int,name string) row format delimited fields terminated by ‘,’;load data inpath ‘/aztest/hiveinput’ into table aztest;create table azres as select * from aztest;insert overwrite directory ‘/aztest/hiveoutput’ select count(1) from aztest;<br>
Job描述文件：hivef.job</p>
<h1><a id="hivefjobtypecommandcommandhomehadoopappshivebinhive_f_testsql_399"></a>hivef.jobtype=commandcommand=/home/hadoop/apps/hive/bin/hive -f ‘test.sql’</h1>
<p>2、将所有job资源文件打到一个zip包中<br>
3、在azkaban的web管理界面创建工程并上传zip包<br>
4、启动job</p>
<ol start="3">
<li>sqoop数据迁移<br>
3.1 概述<br>
sqoop是apache旗下一款“Hadoop和关系数据库服务器之间传送数据”的工具。<br>
导入数据：MySQL，Oracle导入数据到Hadoop的HDFS、HIVE、HBASE等数据存储系统；<br>
导出数据：从Hadoop的文件系统中导出数据到关系数据库</li>
</ol>
<p>3.2 工作机制<br>
将导入或导出命令翻译成mapreduce程序来实现<br>
在翻译出的mapreduce中主要是对inputformat和outputformat进行定制</p>
<p>3.3 sqoop实战及原理<br>
3.3.1 sqoop安装<br>
安装sqoop的前提是已经具备java和hadoop的环境<br>
1、下载并解压<br>
最新版下载地址http://ftp.wayne.edu/apache/sqoop/1.4.6/</p>
<p>2、修改配置文件<br>
$ cd $SQOOP_HOME/conf<br>
$ mv <a href="http://sqoop-env-template.sh" rel="nofollow">sqoop-env-template.sh</a> <a href="http://sqoop-env.sh" rel="nofollow">sqoop-env.sh</a><br>
打开sqoop-env.sh并编辑下面几行：<br>
export HADOOP_COMMON_HOME=/home/hadoop/apps/hadoop-2.6.1/<br>
export HADOOP_MAPRED_HOME=/home/hadoop/apps/hadoop-2.6.1/<br>
export HIVE_HOME=/home/hadoop/apps/hive-1.2.1</p>
<p>加入mysql的jdbc驱动包<br>
cp  ~/app/hive/lib/mysql-connector-java-5.1.28.jar   $SQOOP_HOME/lib/<br>
4、验证启动<br>
$ cd $SQOOP_HOME/bin<br>
$ sqoop-version<br>
预期的输出：<br>
15/12/17 14:52:32 INFO sqoop.Sqoop: Running Sqoop version: 1.4.6<br>
Sqoop 1.4.6 git commit id 5b34accaca7de251fc91161733f906af2eddbe83<br>
Compiled by abe on Fri Aug 1 11:19:26 PDT 2015<br>
到这里，整个Sqoop安装工作完成。</p>
<p>3.4 Sqoop的数据导入<br>
“导入工具”导入单个表从RDBMS到HDFS。表中的每一行被视为HDFS的记录。所有记录都存储为文本文件的文本数据（或者Avro、sequence文件等二进制数据）<br>
3.4.1 语法<br>
下面的语法用于将数据导入HDFS。<br>
$ sqoop import (generic-args) (import-args)</p>
<p>3.4.2 示例<br>
表数据<br>
在mysql中有一个库userdb中三个表：emp, emp_add和emp_contact<br>
表emp:<br>
id name deg salary dept<br>
1201 gopal manager 50,000 TP<br>
1202 manisha Proof reader 50,000 TP<br>
1203 khalil php dev 30,000 AC<br>
1204 prasanth php dev 30,000 AC<br>
1205 kranthi admin 20,000 TP<br>
表emp_add:<br>
id hno street city<br>
1201 288A vgiri jublee<br>
1202 108I aoc sec-bad<br>
1203 144Z pgutta hyd<br>
1204 78B old city sec-bad<br>
1205 720X hitec sec-bad<br>
表emp_conn:</p>
<p>id phno email<br>
1201 2356742 <a href="mailto:gopal@tp.com" rel="nofollow">gopal@tp.com</a><br>
1202 1661663 <a href="mailto:manisha@tp.com" rel="nofollow">manisha@tp.com</a><br>
1203 8887776 <a href="mailto:khalil@ac.com" rel="nofollow">khalil@ac.com</a><br>
1204 9988774 <a href="mailto:prasanth@ac.com" rel="nofollow">prasanth@ac.com</a><br>
1205 1231231 <a href="mailto:kranthi@tp.com" rel="nofollow">kranthi@tp.com</a><br>
导入表表数据到HDFS<br>
下面的命令用于从MySQL数据库服务器中的emp表导入HDFS。<br>
$bin/sqoop import --connect jdbc:mysql://hdp-node-01:3306/test --username root --password root --table emp --m 1</p>
<p>如果成功执行，那么会得到下面的输出。<br>
14/12/22 15:24:54 INFO sqoop.Sqoop: Running Sqoop version: 1.4.514/12/22 15:24:56 INFO manager.MySQLManager: Preparing to use a MySQL streaming <a href="http://resultset.INFO" rel="nofollow">resultset.INFO</a> orm.CompilationManager: Writing jar file: /tmp/sqoop-hadoop/compile/cebe706d23ebb1fd99c1f063ad51ebd7/emp.jar-----------------------------------------------------O mapreduce.Job: map 0% reduce 0%14/12/22 15:28:08 INFO mapreduce.Job: map 100% reduce 0%14/12/22 15:28:16 INFO mapreduce.Job: Job job_1419242001831_0001 completed successfully----------------------------------------------------------------------------------------------------------14/12/22 15:28:17 INFO mapreduce.ImportJobBase: Transferred 145 bytes in 177.5849 seconds (0.8165 bytes/sec)14/12/22 15:28:17 INFO mapreduce.ImportJobBase: Retrieved 5 records.</p>
<p>为了验证在HDFS导入的数据，请使用以下命令查看导入的数据<br>
$ $HADOOP_HOME/bin/hadoop fs -cat /user/hadoop/emp/part-m-00000</p>
<p>emp表的数据和字段之间用逗号(,)表示。<br>
1201, gopal,    manager, 50000, TP1202, manisha,  preader, 50000, TP1203, kalil,    php dev, 30000, AC1204, prasanth, php dev, 30000, AC1205, kranthi,  admin,   20000, TP</p>
<p>导入关系表到HIVE<br>
bin/sqoop import --connect jdbc:mysql://hdp-node-01:3306/test --username root --password root --table emp --hive-import --m 1<br>
导入到HDFS指定目录<br>
在导入表数据到HDFS使用Sqoop导入工具，我们可以指定目标目录。<br>
以下是指定目标目录选项的Sqoop导入命令的语法。<br>
–target-dir </p>
<p>下面的命令是用来导入emp_add表数据到’/queryresult’目录。<br>
bin/sqoop import --connect jdbc:mysql://hdp-node-01:3306/test --username root --password root --target-dir /queryresult --table emp --m 1</p>
<p>下面的命令是用来验证 /queryresult 目录中 emp_add表导入的数据形式。<br>
$HADOOP_HOME/bin/hadoop fs -cat /queryresult/part-m-*</p>
<p>它会用逗号（，）分隔emp_add表的数据和字段。<br>
1201, 288A, vgiri,   jublee1202, 108I, aoc,     sec-bad1203, 144Z, pgutta,  hyd1204, 78B,  oldcity, sec-bad1205, 720C, hitech,  sec-bad</p>
<p>导入表数据子集<br>
我们可以导入表的使用Sqoop导入工具，"where"子句的一个子集。它执行在各自的数据库服务器相应的SQL查询，并将结果存储在HDFS的目标目录。<br>
where子句的语法如下。<br>
–where </p>
<p>下面的命令用来导入emp_add表数据的子集。子集查询检索员工ID和地址，居住城市为：Secunderabad<br>
bin/sqoop import --connect jdbc:mysql://hdp-node-01:3306/test --username root --password root --where “city =‘sec-bad’” --target-dir /wherequery --table emp_add --m 1</p>
<p>下面的命令用来验证数据从emp_add表导入/wherequery目录<br>
$HADOOP_HOME/bin/hadoop fs -cat /wherequery/part-m-*</p>
<p>它用逗号（，）分隔 emp_add表数据和字段。<br>
1202, 108I, aoc, sec-bad1204, 78B, oldcity, sec-bad1205, 720C, hitech, sec-bad</p>
<p>增量导入<br>
增量导入是仅导入新添加的表中的行的技术。<br>
它需要添加‘incremental’, ‘check-column’, 和 ‘last-value’选项来执行增量导入。<br>
下面的语法用于Sqoop导入命令增量选项。<br>
–incremental –check-column –last value </p>
<p>假设新添加的数据转换成emp表如下：<br>
1206, satish p, grp des, 20000, GR<br>
下面的命令用于在EMP表执行增量导入。<br>
bin/sqoop import --connect jdbc:mysql://hdp-node-01:3306/test --username root --password root --table emp --m 1 --incremental append --check-column id --last-value 1205</p>
<p>以下命令用于从emp表导入HDFS emp/ 目录的数据验证。<br>
$ $HADOOP_HOME/bin/hadoop fs -cat /user/hadoop/emp/part-m-*它用逗号（，）分隔 emp_add表数据和字段。1201, gopal,    manager, 50000, TP1202, manisha,  preader, 50000, TP1203, kalil,    php dev, 30000, AC1204, prasanth, php dev, 30000, AC1205, kranthi,  admin,   20000, TP1206, satish p, grp des, 20000, GR</p>
<p>下面的命令是从表emp 用来查看修改或新添加的行<br>
$ $HADOOP_HOME/bin/hadoop fs -cat /emp/part-m-*1这表示新添加的行用逗号（，）分隔emp表的字段。 1206, satish p, grp des, 20000, GR</p>
<p>3.5 Sqoop的数据导出<br>
将数据从HDFS导出到RDBMS数据库<br>
导出前，目标表必须存在于目标数据库中。<br>
默认操作是从将文件中的数据使用INSERT语句插入到表中<br>
更新模式下，是生成UPDATE语句更新表数据<br>
语法<br>
以下是export命令语法。<br>
$ sqoop export (generic-args) (export-args)</p>
<p>示例<br>
数据是在HDFS 中“EMP/”目录的emp_data文件中。所述emp_data如下：<br>
1201, gopal,     manager, 50000, TP1202, manisha,   preader, 50000, TP1203, kalil,     php dev, 30000, AC1204, prasanth,  php dev, 30000, AC1205, kranthi,   admin,   20000, TP1206, satish p,  grp des, 20000, GR</p>
<p>1、首先需要手动创建mysql中的目标表<br>
$ mysqlmysql&gt; USE db;mysql&gt; CREATE TABLE employee (    id INT NOT NULL PRIMARY KEY,    name VARCHAR(20),    deg VARCHAR(20),   salary INT,   dept VARCHAR(10));</p>
<p>2、然后执行导出命令<br>
bin/sqoop export --connect jdbc:mysql://hdp-node-01:3306/test --username root --password root --table emp2 --export-dir /user/hadoop/emp/</p>
<p>3、验证表mysql命令行。<br>
mysql&gt;select * from employee;如果给定的数据存储成功，那么可以找到数据在如下的employee表。±-----±-------------±------------±------------------±-------+| Id   | Name         | Designation | Salary            | Dept   |±-----±-------------±------------±------------------±-------+| 1201 | gopal        | manager     | 50000             | TP     || 1202 | manisha      | preader     | 50000             | TP     || 1203 | kalil        | php dev     | 30000             | AC     || 1204 | prasanth     | php dev     | 30000             | AC     || 1205 | kranthi      | admin       | 20000             | TP     || 1206 | satish p     | grp des     | 20000             | GR     |±-----±-------------±------------±------------------±-------+</p>
<p>3.6 Sqoop作业<br>
注：Sqoop作业——将事先定义好的数据导入导出任务按照指定流程运行<br>
语法<br>
以下是创建Sqoop作业的语法。<br>
$ sqoop job (generic-args) (job-args)   [-- [subtool-name] (subtool-args)]$ sqoop-job (generic-args) (job-args)   [-- [subtool-name] (subtool-args)]</p>
<p>创建作业(–create)<br>
在这里，我们创建一个名为myjob，这可以从RDBMS表的数据导入到HDFS作业。</p>
<p>bin/sqoop job --create myimportjob – import --connect jdbc:mysql://hdp-node-01:3306/test --username root --password root --table emp --m 1<br>
该命令创建了一个从db库的employee表导入到HDFS文件的作业。</p>
<p>验证作业 (–list)<br>
‘–list’ 参数是用来验证保存的作业。下面的命令用来验证保存Sqoop作业的列表。<br>
$ sqoop job --list<br>
它显示了保存作业列表。<br>
Available jobs:<br>
myjob<br>
检查作业(–show)<br>
‘–show’ 参数用于检查或验证特定的工作，及其详细信息。以下命令和样本输出用来验证一个名为myjob的作业。<br>
$ sqoop job --show myjob<br>
它显示了工具和它们的选择，这是使用在myjob中作业情况。<br>
Job: myjob  Tool: import Options: ----------------------------  direct.import = true codegen.input.delimiters.record = 0 hdfs.append.dir = false  db.table = employee … incremental.last.value = 1206 …</p>
<p>执行作业 (–exec)<br>
‘–exec’ 选项用于执行保存的作业。下面的命令用于执行保存的作业称为myjob。<br>
$ sqoop job --exec myjob它会显示下面的输出。10/08/19 13:08:45 INFO tool.CodeGenTool: Beginning code generation …</p>
<p>3.7 Sqoop的原理<br>
概述<br>
Sqoop的原理其实就是将导入导出命令转化为mapreduce程序来执行，sqoop在接收到命令后，都要生成mapreduce程序</p>
<p>使用sqoop的代码生成工具可以方便查看到sqoop所生成的java代码，并可在此基础之上进行深入定制开发</p>
<p>代码定制<br>
以下是Sqoop代码生成命令的语法：<br>
$ sqoop-codegen (generic-args) (codegen-args) $ sqoop-codegen (generic-args) (codegen-args)</p>
<p>示例：以USERDB数据库中的表emp来生成Java代码为例。<br>
下面的命令用来生成导入<br>
$ sqoop-codegen --import–connect jdbc:mysql://localhost/userdb --username root \ --table emp</p>
<p>如果命令成功执行，那么它就会产生如下的输出。<br>
14/12/23 02:34:40 INFO sqoop.Sqoop: Running Sqoop version: 1.4.514/12/23 02:34:41 INFO tool.CodeGenTool: Beginning code generation……………….14/12/23 02:34:42 INFO orm.CompilationManager: HADOOP_MAPRED_HOME is /usr/local/hadoopNote: /tmp/sqoop-hadoop/compile/9a300a1f94899df4a9b10f9935ed9f91/emp.java uses or overrides a deprecated API.Note: Recompile with -Xlint:deprecation for details.14/12/23 02:34:47 INFO orm.CompilationManager: Writing jar file: /tmp/sqoop-hadoop/compile/9a300a1f94899df4a9b10f9935ed9f91/emp.jar</p>
<p>验证: 查看输出目录下的文件<br>
$ cd /tmp/sqoop-hadoop/compile/9a300a1f94899df4a9b10f9935ed9f91/$ lsemp.classemp.jaremp.java<br>
如果想做深入定制导出，则可修改上述代码文件</p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>