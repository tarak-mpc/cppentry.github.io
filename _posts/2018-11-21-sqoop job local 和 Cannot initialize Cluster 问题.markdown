---
layout:     post
title:      sqoop job local 和 Cannot initialize Cluster 问题
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>hadoop版本：Hadoop 2.3.0-cdh5.0.0</p>
<p>sqoop版本：Sqoop 1.4.4-cdh5.0.0</p>
<p>配置好sqooop-env.xml:</p>
<p><br><br>
#Set path to where bin/hadoop is available<br>
export HADOOP_COMMON_HOME=/my/hadoop<br><br><br>
#Set path to where hadoop-*-core.jar is available<br>
export HADOOP_MAPRED_HOME=/my/hadoop/share/hadoop/mapreduce1<br><br><br>
#set the path to where bin/hbase is available<br>
#export HBASE_HOME=<br><br><br>
#Set the path to where bin/hive is available<br>
export HIVE_HOME=/my/hive<br><br><br>
#Set the path for where zookeper config dir is<br>
export ZOOCFGDIR=/my/zookeeper<br></p>
<p>然后拷贝${hadoop_home}/share/hadoop/mapreduce1/hadoop-*core*.jar到 ${sqoop_home}/lib下</p>
<p>然后可以正常导出hdfs数据到oracle，但是日志却显示：mapred.JobClient: Running job: job_local1950934197_0001，Task 'attempt_local1950934197_0001_m_000077_0'等等，很明显mapreduce任务是在本地运行的，虽然这样可以运行但是有两个问题：不能利用集群的优势和无法根据job_id杀死任务。因为hadoop job -list找不到job_local的任务。然后发现hadoop-*core*.jar是mr1的jar,而集群用的是mr2的jar包，于是想着可能是这个原因，就把export
 HADOOP_MAPRED_HOME=/e3base/hadoop/share/hadoop/mapreduce1改成HADOOP_MAPRED_HOME=/my/hadoop/share/hadoop/mapreduce,因为这个路径下的jar包是mr2的。同时将${sqoop_home}/lib下的hadoop-*core*.jar删除，将mapreduce目录下的hadoop-*core*.jar拷贝到lib目录下，再次提交export脚本报错了：</p>
<p>15/02/08 19:53:08 WARN security.UserGroupInformation: PriviledgedActionException as:e3base (auth:SIMPLE) c<br>
ause:java.io.IOException: Cannot initialize Cluster. Please check your configuration for mapreduce.framewo<br>
rk.name and the correspond server addresses.<br>
15/02/08 19:53:08 ERROR tool.ExportTool: Encountered IOException running export job: java.io.IOException: <br>
Cannot initialize Cluster. Please check your configuration for mapreduce.framework.name and the correspond<br>
 server addresses.</p>
<p>不能初始化集群，于是我在想是不是sqoop版本和hadoop版本不协调呢，于是去sqoop官网上看了下发现1.44支持hadoop2.0啊。搜索其他的sqoop安装指南，发现确实是拷贝hadoop-*core*.jar没错。终于找到有用的了http://www.tuicool.com/articles/3YNVvuv，这网址是说是可能缺少jar包的问题，好吧，试下，我把mapreduce目录下的hadoop-mapreduce-client-common-2.3.0-cdh5.0.0.jar拷贝到lib目录下再次运行，还是报错：Cannot
 initialize Cluster.。于是想着可能还缺jar包，就把mapreduce目录下的hadoop-mapreduce-client-jobclient-2.3.0-cdh5.0.0.jar 拷贝到lib目录下再次运行这次运行成功了：Submitting tokens for job: job_1416981396834_2297，sqoop提交mapreduce到集群运行了。然后又把lib下的hadoop-mapreduce-client-common-2.3.0-cdh5.0.0.jar删除运行看汇报什么错呢：</p>
<p>java.lang.ClassNotFoundException: org.apache.hadoop.mapreduce.v2.util.MRApps。</p>
            </div>
                </div>