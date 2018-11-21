---
layout:     post
title:      flume在windows下的启动脚本
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p></p>
<p>windows下flume启动脚本</p>
<p><br></p>
<p>::@echo off</p>
::USAGE: <span> </span>apache-flume-1.5.0.1-bin&gt;call bin/flume-win*.bat<br>
::AUTHOER:<span> </span>Ning Guo of CIB<br>
::TIME:<span> </span>2014/11/28  12:55<br>
::set java home<br>
set JAVA_HOME="C:\Java\jdk1.7.0_67"<br>
::set configuration file<br>
set CONF_FILE=testconsole.conf<br><br><br><br><br>
::set DEBUG="-Xdebug -Xnoagent -Djava.compiler=NONE -Xrunjdwp:transport=dt_socket,server=y,suspend=n,address=2345"<br><p>::java -agentlib:jdwp=transport=dt_socket,address=8000,server=y,suspend=n zhc_application</p>
<p>::set java remote testing</p>
set DEBUG="-agentlib:jdwp=transport=dt_socket,address=2345,server=y,suspend=y"<br>
set JVM_OPTS="%DEBUG%"<br>
::set agent name <br>
set AGENT_NAME=agent<br>
::retrieve the parent directory<br>
setlocal<br>
for %%i in ("%~dp0..") do set "folder=%%~fi"<br>
set FLUME_HOME="%folder%"<br>
%JAVA_HOME%\bin\java.exe -Xms128m -Xmx512m %JVM_OPTS% -Dlog4j.configuration=file:///%FLUME_HOME%\conf\log4j.properties -cp %FLUME_HOME%\lib\* org.apache.flume.node.Application -f %FLUME_HOME%\conf\%CONF_FILE% -n %AGENT_NAME%<br>
pause
            </div>
                </div>