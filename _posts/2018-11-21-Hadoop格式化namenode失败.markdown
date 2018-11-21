---
layout:     post
title:      Hadoop格式化namenode失败
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/qq_26840065/article/details/50982959				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>hdfs namenode -format 时候提示 n1 s1连接不上去,</p>
<p>检查hosts配置正确</p>
<p>于是我怀疑防火墙没有关,然而防火墙也关闭了.</p>
<p>于是我看了下日志才发现tmp在hdfs-site.xml中 namenode和datanode临时存放的文件目录没有创建,但是hadoop格式化会自动创建的但是,看到提示于是我创建了 tmp的dfs目录,在执行一遍 -format 就ok了</p>
<p>提示错误</p>
<p>16/03/25 10:57:39 WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable<br>
16/03/25 10:57:42 INFO ipc.Client: Retrying connect to server: n1/192.168.253.130:9000. Already tried 0 time(s); retry policy is RetryUpToMaximumCountWithFixedSleep(maxRetries=10, sleepTime=1000 MILLISECONDS)<br>
16/03/25 10:57:43 INFO ipc.Client: Retrying connect to server: n1/192.168.253.130:9000. Already tried 1 time(s); retry policy is RetryUpToMaximumCountWithFixedSleep(maxRetries=10, sleepTime=1000 MILLISECONDS)<br>
16/03/25 10:57:44 INFO ipc.Client: Retrying connect to server: n1/192.168.253.130:9000. Already tried 2 time(s); retry policy is RetryUpToMaximumCountWithFixedSleep(maxRetries=10, sleepTime=1000 MILLISECONDS)<br>
16/03/25 10:57:45 INFO ipc.Client: Retrying connect to server: n1/192.168.253.130:9000. Already tried 3 time(s); retry policy is RetryUpToMaximumCountWithFixedSleep(maxRetries=10, sleepTime=1000 MILLISECONDS)<br>
16/03/25 10:57:46 INFO ipc.Client: Retrying connect to server: n1/192.168.253.130:9000. Already tried 4 time(s); retry policy is RetryUpToMaximumCountWithFixedSleep(maxRetries=10, sleepTime=1000 MILLISECONDS)<br>
16/03/25 10:57:47 INFO ipc.Client: Retrying connect to server: n1/192.168.253.130:9000. Already tried 5 time(s); retry policy is RetryUpToMaximumCountWithFixedSleep(maxRetries=10, sleepTime=1000 MILLISECONDS)<br>
16/03/25 10:57:48 INFO ipc.Client: Retrying connect to server: n1/192.168.253.130:9000. Already tried 6 time(s); retry policy is RetryUpToMaximumCountWithFixedSleep(maxRetries=10, sleepTime=1000 MILLISECONDS)<br>
16/03/25 10:57:49 INFO ipc.Client: Retrying connect to server: n1/192.168.253.130:9000. Already tried 7 time(s); retry policy is RetryUpToMaximumCountWithFixedSleep(maxRetries=10, sleepTime=1000 MILLISECONDS)<br></p>
<p><br></p>
<p><img src="" alt=""></p>
<p>查看日志发现 需要mkdir  tmp/dfs 就ok了</p>
<p>一下是自动部署HA脚本</p>
<p>#!/bin/sh<br><br><br>
#ssh s1 'rm -rf /opt/src/*'<br>
#ssh s2 'rm -rf /opt/src/*'<br>
#isynchronize all config files<br>
#ssh n1 'scp -r /opt/src/hadoop/ s1:/opt/src/'<br>
#ssh n1 'scp -r /opt/src/hadoop/  s2:/opt/src/'<br><br><br>
#stop all daemons<br>
ssh n1 '/opt/src/hadoop/sbin/stop-all.sh'<br><br><br>
#remove all files<br>
ssh n1 'rm -rf /opt/src/hadoop/tmp'<br>
ssh n1 'rm -rf /opt/src/hadoop/logs'<br>
ssh s1 'rm -rf /opt/src/hadoop/tmp'<br>
ssh s1 'rm -rf /opt/src/hadoop/logs'<br>
ssh s2 'rm -rf /opt/src/hadoop/tmp'<br>
ssh s2 'rm -rf /opt/src/hadoop/logs'<br><br><br><br><br>
#start journalnodes cluster<br>
ssh n1 '/opt/src/hadoop/sbin/hadoop-daemon.sh start journalnode'<br>
ssh s1 '/opt/src/hadoop/sbin/hadoop-daemon.sh start journalnode'<br>
ssh s2 '/opt/src/hadoop/sbin/hadoop-daemon.sh start journalnode'<br><br><br>
#format one namenode<br>
ssh n1 '/opt/src/hadoop/bin/hdfs namenode -format -clusterId mycluster'<br>
ssh n1 '/opt/src/hadoop/sbin/hadoop-daemon.sh start namenode'<br><br><br>
#format another namenode<br>
ssh s1 '/opt/src/hadoop/bin/hdfs namenode -bootstrapStandby'<br>
sleep 10<br>
ssh s1 '/opt/src/hadoop/sbin/hadoop-daemon.sh start namenode'<br>
sleep 10<br><br><br>
#trigger n1 active<br>
ssh n1 '/opt/src/hadoop/bin/hdfs haadmin -failover --forceactive s1 n1'<br><br><br>
#start all datanodes<br>
ssh n1 '/opt/src/hadoop/sbin/hadoop-daemons.sh start datanode'<br></p>
            </div>
                </div>