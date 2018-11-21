---
layout:     post
title:      Windows server 2008 部署Kafka Zookeeper小结
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/BAStriver/article/details/83792880				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>1.首先先下载Zookeeper ： <a href="https://www.apache.org/dyn/closer.cgi/zookeeper/" rel="nofollow">Zookeeper下载链接</a></p>

<p>2.下载Kafka ：<a href="http://kafka.apache.org/downloads" rel="nofollow">Kafka下载链接</a> ，注意：代码中使用Producer的话，要注意jar包要和安装的ban版本一致</p>

<p>3.安装Kafka ：</p>

<p>解压Kafka，注意<strong>安装目录不能有空格</strong>，建一个log文件夹给kafka作为日志存储路径，假设这里是：C:\\administrator\\software\\kafka\\log</p>

<p>打开Kafka的安装目录下的config/server.properites，加入：log.dirs=C:\\sunrun\\software\\kafka\\log</p>

<p>如图：</p>

<p><img alt="" class="has" height="230" src="https://img-blog.csdnimg.cn/20181106194509222.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0JBU3RyaXZlcg==,size_16,color_FFFFFF,t_70" width="1013"></p>

<p>然后修改C:\\sunrun\\software\\kafka_2.12-2.0.0\\bin\\windows的kafka-run-class.bat</p>

<p>直接复制吧（主要是%JAVA_HOME%两边加上"）：</p>

<pre class="has">
<code>@echo off
rem Licensed to the Apache Software Foundation (ASF) under one or more
rem contributor license agreements.  See the NOTICE file distributed with
rem this work for additional information regarding copyright ownership.
rem The ASF licenses this file to You under the Apache License, Version 2.0
rem (the "License"); you may not use this file except in compliance with
rem the License.  You may obtain a copy of the License at
rem
rem     http://www.apache.org/licenses/LICENSE-2.0
rem
rem Unless required by applicable law or agreed to in writing, software
rem distributed under the License is distributed on an "AS IS" BASIS,
rem WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
rem See the License for the specific language governing permissions and
rem limitations under the License.

setlocal enabledelayedexpansion

IF [%1] EQU [] (
	echo USAGE: %0 classname [opts]
	EXIT /B 1
)

rem Using pushd popd to set BASE_DIR to the absolute path
pushd %~dp0..\..
set BASE_DIR=%CD%
popd

IF ["%SCALA_VERSION%"] EQU [""] (
  set SCALA_VERSION=2.11.12
)

IF ["%SCALA_BINARY_VERSION%"] EQU [""] (
  for /f "tokens=1,2 delims=." %%a in ("%SCALA_VERSION%") do (
    set FIRST=%%a
    set SECOND=%%b
    if ["!SECOND!"] EQU [""] (
      set SCALA_BINARY_VERSION=!FIRST!
    ) else (
      set SCALA_BINARY_VERSION=!FIRST!.!SECOND!
    )
  )
)

rem Classpath addition for kafka-core dependencies
for %%i in ("%BASE_DIR%\core\build\dependant-libs-%SCALA_VERSION%\*.jar") do (
	call :concat "%%i"
)

rem Classpath addition for kafka-examples
for %%i in ("%BASE_DIR%\examples\build\libs\kafka-examples*.jar") do (
	call :concat "%%i"
)

rem Classpath addition for kafka-clients
for %%i in ("%BASE_DIR%\clients\build\libs\kafka-clients*.jar") do (
	call :concat "%%i"
)

rem Classpath addition for kafka-streams
for %%i in ("%BASE_DIR%\streams\build\libs\kafka-streams*.jar") do (
	call :concat "%%i"
)

rem Classpath addition for kafka-streams-examples
for %%i in ("%BASE_DIR%\streams\examples\build\libs\kafka-streams-examples*.jar") do (
	call :concat "%%i"
)

for %%i in ("%BASE_DIR%\streams\build\dependant-libs-%SCALA_VERSION%\rocksdb*.jar") do (
	call :concat "%%i"
)

rem Classpath addition for kafka tools
for %%i in ("%BASE_DIR%\tools\build\libs\kafka-tools*.jar") do (
	call :concat "%%i"
)

for %%i in ("%BASE_DIR%\tools\build\dependant-libs-%SCALA_VERSION%\*.jar") do (
	call :concat "%%i"
)

for %%p in (api runtime file json tools) do (
	for %%i in ("%BASE_DIR%\connect\%%p\build\libs\connect-%%p*.jar") do (
		call :concat "%%i"
	)
	if exist "%BASE_DIR%\connect\%%p\build\dependant-libs\*" (
		call :concat "%BASE_DIR%\connect\%%p\build\dependant-libs\*"
	)
)

rem Classpath addition for release
for %%i in ("%BASE_DIR%\libs\*") do (
	call :concat "%%i"
)

rem Classpath addition for core
for %%i in ("%BASE_DIR%\core\build\libs\kafka_%SCALA_BINARY_VERSION%*.jar") do (
	call :concat "%%i"
)

rem JMX settings
IF ["%KAFKA_JMX_OPTS%"] EQU [""] (
	set KAFKA_JMX_OPTS=-Dcom.sun.management.jmxremote -Dcom.sun.management.jmxremote.authenticate=false  -Dcom.sun.management.jmxremote.ssl=false
)

rem JMX port to use
IF ["%JMX_PORT%"] NEQ [""] (
	set KAFKA_JMX_OPTS=%KAFKA_JMX_OPTS% -Dcom.sun.management.jmxremote.port=%JMX_PORT%
)

rem Log directory to use
IF ["%LOG_DIR%"] EQU [""] (
    set LOG_DIR="%BASE_DIR~%/logs"
)

rem Log4j settings
IF ["%KAFKA_LOG4J_OPTS%"] EQU [""] (
	set KAFKA_LOG4J_OPTS=-Dlog4j.configuration=file:%BASE_DIR%/config/tools-log4j.properties
) ELSE (
  rem create logs directory
  IF not exist "%LOG_DIR%" (
      mkdir "%LOG_DIR%"
  )
)

set KAFKA_LOG4J_OPTS=-Dkafka.logs.dir="%LOG_DIR%" "%KAFKA_LOG4J_OPTS%"

rem Generic jvm settings you want to add
IF ["%KAFKA_OPTS%"] EQU [""] (
	set KAFKA_OPTS=
)

set DEFAULT_JAVA_DEBUG_PORT=5005
set DEFAULT_DEBUG_SUSPEND_FLAG=n
rem Set Debug options if enabled
IF ["%KAFKA_DEBUG%"] NEQ [""] (


	IF ["%JAVA_DEBUG_PORT%"] EQU [""] (
		set JAVA_DEBUG_PORT=%DEFAULT_JAVA_DEBUG_PORT%
	)

	IF ["%DEBUG_SUSPEND_FLAG%"] EQU [""] (
		set DEBUG_SUSPEND_FLAG=%DEFAULT_DEBUG_SUSPEND_FLAG%
	)
	set DEFAULT_JAVA_DEBUG_OPTS=-agentlib:jdwp=transport=dt_socket,server=y,suspend=!DEBUG_SUSPEND_FLAG!,address=!JAVA_DEBUG_PORT!

	IF ["%JAVA_DEBUG_OPTS%"] EQU [""] (
		set JAVA_DEBUG_OPTS=!DEFAULT_JAVA_DEBUG_OPTS!
	)

	echo Enabling Java debug options: !JAVA_DEBUG_OPTS!
	set KAFKA_OPTS=!JAVA_DEBUG_OPTS! !KAFKA_OPTS!
)

rem Which java to use
IF ["%JAVA_HOME%"] EQU [""] (
	set JAVA=java
) ELSE (
	set JAVA="%JAVA_HOME%/bin/java"
)

rem Memory options
IF ["%KAFKA_HEAP_OPTS%"] EQU [""] (
	set KAFKA_HEAP_OPTS=-Xmx256M
)

rem JVM performance options
IF ["%KAFKA_JVM_PERFORMANCE_OPTS%"] EQU [""] (
	set KAFKA_JVM_PERFORMANCE_OPTS=-server -XX:+UseG1GC -XX:MaxGCPauseMillis=20 -XX:InitiatingHeapOccupancyPercent=35 -XX:+ExplicitGCInvokesConcurrent -Djava.awt.headless=true
)

IF not defined CLASSPATH (
	echo Classpath is empty. Please build the project first e.g. by running 'gradlew jarAll'
	EXIT /B 2
)

set COMMAND=%JAVA% %KAFKA_HEAP_OPTS% %KAFKA_JVM_PERFORMANCE_OPTS% %KAFKA_JMX_OPTS% %KAFKA_LOG4J_OPTS% -cp "%CLASSPATH%" %KAFKA_OPTS% %*
rem echo.
rem echo %COMMAND%
rem echo.
%COMMAND%

goto :eof
:concat
IF not defined CLASSPATH (
  set CLASSPATH="%~1"
) ELSE (
  set CLASSPATH=%CLASSPATH%;"%~1"
)
</code></pre>

<p>4.安装Zookeeper ：</p>

<p>复制 zoo_sample.cfg 副本，修改副本名为zoo.cfg，新建data、log文件夹保存日志和数据，然后修改Zookeeperanzh安装目录下的conf\zoo.cfg，如图：</p>

<p><img alt="" class="has" height="151" src="https://img-blog.csdnimg.cn/20181107092828227.png" width="797"></p>

<p>dataDir=C:\\administrator\\software\\zookeeper-3.4.12\\data</p>

<p>dataLogDir=C:\\administrator\\software\\zookeeper-3.4.12\\log</p>

<p>5.好了，配置一般这样就正常了，去到kafka安装的根目录，按住Shift键然后右键，选择在此处打开powershell也就是cmd啦，当然也可以cd过去。</p>

<p>启动kafka，输入：</p>

<pre class="has">
<code>.\bin\windows\kafka-server-start.bat .\config\server.properties</code></pre>

<p>同样的去到zookeeperanzh安装的bin目录，打开cmd然后输入：</p>

<pre class="has">
<code>zkServer.cmd</code></pre>

<p>这样就OK了。</p>

<p>6.遇到的问题总结：</p>

<p>（1）启动kafka显示系统找不到指定路径，好吧，jdk没设置好环境变量，参考配置吧：<a href="https://www.cnblogs.com/smyhvae/p/3788534.html" rel="nofollow">Jdk环境配置</a>；</p>

<p>（2）zookeeper报错： Error:KeeperErrorCode = NoNode for /otter，那么关闭ZooKeeper，删除你自己设定的dataDir路径下的version-2文件夹，然后重启；</p>

<p>（3）kafka报错：WARN org.apache.kafka.clients.NetworkClient - Bootstrap broker 127.0.0.1:9092 disconnected，项目中的jar和安装的版本不一致，建议重装对应版本的kafka吧；</p>

<p>（4）zookeeper报错：WARN org.apache.kafka.clients.NetworkClient - Bootstrap broker 127.0.0.1:9092 disconnected，同理（2）的做法；</p>

<p>（5）zookeeper报错：Opening socket connection to server localhost/0:0:0:0:0:0:0:1:2181. Will not attempt to authenticate，先检查项目的jar和安装的shif是否版本一致，一样的话，打开C:\Windows\System32\drivers\etc的host最后面加入：127.0.0.1  localhost，因为有的新装的服务器127.0.0.1不是对应的localhost的；</p>

<p><img alt="" class="has" height="150" src="https://img-blog.csdnimg.cn/20181107094309813.png" width="435"></p>

<p>（6）kafka报错：错误: 找不到或无法加载主类，那就修改bin\windows\kafka-run-class.bat吧；</p>

<p><img alt="" class="has" height="212" src="https://img-blog.csdnimg.cn/20181107094540178.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0JBU3RyaXZlcg==,size_16,color_FFFFFF,t_70" width="1200"></p>

<p>（7） zookeeper报错：系统找不到指定路径，修改下bin目录下的zkEnv.cmd加上"。</p>

<p><img alt="" class="has" height="96" src="https://img-blog.csdnimg.cn/20181107100028110.png" width="539"></p>

<p> </p>

<p>附kafka常用命令（安装根目录kafka\bin\windows下shift+右键打开cmd运行）：</p>

<pre class="has">
<code>
1、创建Topic：kafka-topics --create --zookeeper localhost:2181  --replication-factor 1 --partitions 1 --topic testTopic

2、查看topic：kafka-topics --list --zookeeper localhost:2181 

3、 启动生产者：kafka-console-producer --broker-list localhost:9092 --topic cango

4、 启动消费者：kafka-console-consumer --zookeeper  localhost:2181 -topic testTopic --from-beginning

5、修改分区数量：kafka-topics --alter --zookeeper localhost:2181  --partitions 3 --topic testTopic

6、查看topic描述：kafka-topics --zookeeper localhost:2181  --describe --topic testTopic</code></pre>

<p> </p>            </div>
                </div>