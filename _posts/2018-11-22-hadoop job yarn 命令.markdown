---
layout:     post
title:      hadoop job yarn 命令
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/mlljava1111/article/details/48250439				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div><span style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;"><strong>hadoop命令行 与job相关的：</strong></span><br style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;"><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">命令行工具 • </span><br style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;"><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">1.查看 Job 信息：</span><br style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;"><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">hadoop job -list </span><br style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;"><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">2.杀掉 Job： </span><br style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;"><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">hadoop  job –kill  job_id</span><br style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;"><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">3.指定路径下查看历史日志汇总：</span><br style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;"><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">hadoop job -history output-dir </span><br style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;"><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">4.作业的更多细节： </span><br style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;"><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">hadoop job -history all output-dir </span><br style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;"><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">5.打印map和reduce完成百分比和所有计数器：</span><br style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;"><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">hadoop job –status job_id </span><br style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;"><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">6.杀死任务。被杀死的任务不会不利于失败尝试：</span><br style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;"><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">hadoop jab -kill-task &lt;task-id&gt; </span><br style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;"><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">7.使任务失败。被失败的任务会对失败尝试不利：</span><br style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;"></span>
<p><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">hadoop job  -fail-task &lt;task-id&gt;</span></p>
<p><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;"><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;"><strong><span style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">YARN</span>命令行：</strong></span><br></span></p>
<p><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;"><span style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;line-height:21px;">YARN命令是调用bin/yarn脚本文件，如果运行yarn脚本没有带任何参数，则会打印yarn所有命令的描述。</span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;line-height:21px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;line-height:21px;"><span style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;line-height:21px;">使用: yarn [--config confdir] COMMAND [--loglevel loglevel] [GENERIC_OPTIONS] [COMMAND_OPTIONS]</span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;line-height:21px;"><span style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;line-height:21px;">YARN有一个参数解析框架，采用解析泛型参数以及运行类。</span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;line-height:21px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;line-height:21px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;line-height:21px;"></span></p>
<table cellspacing="0" class="t_table" style="border-style:solid;border-color:rgb(227,237,245);border-collapse:collapse;table-layout:auto;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><tbody><tr><td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
命令参数</td>
<td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
描述</td>
</tr><tr><td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
--config confdir</td>
<td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
指定一个默认的配置文件目录，默认值是： ${HADOOP_PREFIX}/conf.</td>
</tr><tr><td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
--loglevel loglevel</td>
<td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
重载Log级别。有效的日志级别包含：FATAL, ERROR, WARN, INFO, DEBUG, and TRACE。默认是INFO。</td>
</tr><tr><td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
GENERIC_OPTIONS</td>
<td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
YARN支持表A的通用命令项。</td>
</tr><tr><td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
COMMAND COMMAND_OPTIONS</td>
<td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
YARN分为用户命令和管理员命令。</td>
</tr></tbody></table><p><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">表A：</span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"></p>
<table cellspacing="0" class="t_table" style="border-style:solid;border-color:rgb(227,237,245);border-collapse:collapse;table-layout:auto;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><tbody><tr><td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
通用项</td>
<td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
Description</td>
</tr><tr><td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
-archives &lt;comma separated list of archives&gt;</td>
<td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
用逗号分隔计算中未归档的文件。 仅仅针对JOB。</td>
</tr><tr><td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
-conf &lt;configuration file&gt;</td>
<td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
制定应用程序的配置文件。</td>
</tr><tr><td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
-D &lt;property&gt;=&lt;value&gt;</td>
<td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
使用给定的属性值。</td>
</tr><tr><td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
-files &lt;comma separated list of files&gt;</td>
<td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
用逗号分隔的文件,拷贝到Map reduce机器，仅仅针对JOB</td>
</tr><tr><td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
-jt &lt;local&gt; or &lt;resourcemanager:port&gt;</td>
<td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
指定一个ResourceManager. 仅仅针对JOB。</td>
</tr><tr><td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
-libjars &lt;comma seperated list of jars&gt;</td>
<td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
将用逗号分隔的jar路径包含到classpath中去，仅仅针对JOB。</td>
</tr></tbody></table><p><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="font-size:18px;">用户命令：</span></span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">对于Hadoop集群用户很有用的命令：</span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">application<br></span><span style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">使用: yarn application [options]</span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"></p>
<table cellspacing="0" class="t_table" style="border-style:solid;border-color:rgb(227,237,245);border-collapse:collapse;table-layout:auto;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><tbody><tr><td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
命令选项</td>
<td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
描述</td>
</tr><tr><td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
-appStates &lt;States&gt;</td>
<td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
使用-list命令，基于应用程序的状态来过滤应用程序。如果应用程序的状态有多个，用逗号分隔。 有效的应用程序状态包含<br>
如下： ALL, NEW, NEW_SAVING, SUBMITTED, ACCEPTED, RUNNING, FINISHED, FAILED, KILLED</td>
</tr><tr><td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
-appTypes &lt;Types&gt;</td>
<td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
使用-list命令，基于应用程序类型来过滤应用程序。如果应用程序的类型有多个，用逗号分隔。</td>
</tr><tr><td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
-list</td>
<td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
从RM返回的应用程序列表，使用-appTypes参数，支持基于应用程序类型的过滤，使用-appStates参数，支持对应用程序状态的过滤。</td>
</tr><tr><td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
-kill &lt;ApplicationId&gt;</td>
<td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
kill掉指定的应用程序。</td>
</tr><tr><td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
-status &lt;ApplicationId&gt;</td>
<td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
打印应用程序的状态。</td>
</tr></tbody></table><p><span style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">示例1：</span></p>
<p><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;"></span></p>
<pre><code class="language-html">[hduser@hadoop0 bin]$ ./yarn application -list -appStates ACCEPTED
15/08/10 11:48:43 INFO client.RMProxy: Connecting to ResourceManager at hadoop1/10.0.1.41:8032
Total number of applications (application-types: [] and states: [ACCEPTED]):1
Application-Id                  Application-Name Application-Type User   Queue   State    Final-State Progress Tracking-URL
application_1438998625140_1703  MAC_STATUS   MAPREDUCE    hduser default ACCEPTED UNDEFINED   0%       N/A</code></pre><br><span style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">示例2：</span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><pre><code class="language-html">[hduser@hadoop0 bin]$ ./yarn application -list
15/08/10 11:43:01 INFO client.RMProxy: Connecting to ResourceManager at hadoop1/10.0.1.41:8032
Total number of applications (application-types: [] and states: [SUBMITTED, ACCEPTED, RUNNING]):1
Application-Id                 Application-Name Application-Type  User   Queue   State    Final-State   Progress Tracking-URL
application_1438998625140_1701 MAC_STATUS   MAPREDUCE     hduser default ACCEPTED UNDEFINED 0%   N/A</code></pre><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">示例3：</span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><pre><code class="language-html">[hduser@hadoop0 bin]$ ./yarn application -kill application_1438998625140_1705
15/08/10 11:57:41 INFO client.RMProxy: Connecting to ResourceManager at hadoop1/10.0.1.41:8032
Killing application application_1438998625140_1705
15/08/10 11:57:42 INFO impl.YarnClientImpl: Killed application application_1438998625140_1705</code></pre><br><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">applicationattempt</span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">使用: yarn applicationattempt [options]</span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><table cellspacing="0" class="t_table" style="border-style:solid;border-color:rgb(227,237,245);border-collapse:collapse;table-layout:auto;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><tbody><tr><td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
命令选项</td>
<td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
描述</td>
</tr><tr><td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
-help</td>
<td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
帮助</td>
</tr><tr><td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
-list &lt;ApplicationId&gt;</td>
<td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
获取到应用程序尝试的列表，其返回值ApplicationAttempt-Id 等于 &lt;Application Attempt Id&gt;</td>
</tr><tr><td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
-status &lt;Application Attempt Id&gt;</td>
<td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
打印应用程序尝试的状态。</td>
</tr></tbody></table><span style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">打印应用程序尝试的报告。</span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">示例1：<br></span><pre><code class="language-html">[hadoop@hadoopcluster78 bin]$ yarn applicationattempt -list application_1437364567082_0106
15/08/10 20:58:28 INFO client.RMProxy: Connecting to ResourceManager at hadoopcluster79/10.0.1.79:8032
Total number of application attempts :1
ApplicationAttempt-Id                  State    AM-Container-Id                        Tracking-URL
appattempt_1437364567082_0106_000001   RUNNING  container_1437364567082_0106_01_000001 http://hadoopcluster79:8088/proxy/application_1437364567082_0106/</code></pre><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">示例2：<br></span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><pre><code class="language-html">[hadoop@hadoopcluster78 bin]$ yarn applicationattempt -list application_1437364567082_0106
15/08/10 20:58:28 INFO client.RMProxy: Connecting to ResourceManager at hadoopcluster79/10.0.1.79:8032
Total number of application attempts :1
ApplicationAttempt-Id                  State    AM-Container-Id                        Tracking-URL
appattempt_1437364567082_0106_000001   RUNNING  container_1437364567082_0106_01_000001 http://hadoopcluster79:8088/proxy/application_1437364567082_0106/</code></pre><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">classpath</span><span style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">使用:
 yarn classpath</span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">打印需要得到Hadoop的jar和所需要的lib包路径</span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><pre><code class="language-html">[hadoop@hadoopcluster78 bin]$ yarn classpath
/home/hadoop/apache/hadoop-2.4.1/etc/hadoop:/home/hadoop/apache/hadoop-2.4.1/etc/hadoop:/home/hadoop/apache/hadoop-2.4.1/etc/hadoop:/home/hadoop/apache/hadoop-2.4.1/share/hadoop/common/lib/*:/home/hadoop/apache/hadoop-2.4.1/share/hadoop/common/*:/home/hadoop/apache/hadoop-2.4.1/share/hadoop/hdfs:/home/hadoop/apache/hadoop-2.4.1/share/hadoop/hdfs/lib/*:/home/hadoop/apache/hadoop-2.4.1/share/hadoop/hdfs/*:/home/hadoop/apache/hadoop-2.4.1/share/hadoop/yarn/lib/*:/home/hadoop/apache/hadoop-2.4.1/share/hadoop/yarn/*:/home/hadoop/apache/hadoop-2.4.1/share/hadoop/mapreduce/lib/*:/home/hadoop/apache/hadoop-2.4.1/share/hadoop/mapreduce/*:/home/hadoop/apache/hadoop-2.4.1/contrib/capacity-scheduler/*.jar:/home/hadoop/apache/hadoop-2.4.1/share/hadoop/yarn/*:/home/hadoop/apache/hadoop-2.4.1/share/hadoop/yarn/lib/*</code></pre><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">container<br></span><span style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">使用: yarn container [options]</span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><table cellspacing="0" class="t_table" style="border-style:solid;border-color:rgb(227,237,245);border-collapse:collapse;table-layout:auto;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><tbody><tr><td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
<div align="left">命令选项</div>
</td>
<td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
<div align="left">描述</div>
</td>
</tr><tr><td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
<div align="left">-help</div>
</td>
<td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
<div align="left">帮助</div>
</td>
</tr><tr><td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
<div align="left">-list &lt;Application Attempt Id&gt;</div>
</td>
<td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
<div align="left">应用程序尝试的Containers列表</div>
</td>
</tr><tr><td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
<div align="left">-status &lt;ContainerId&gt;</div>
</td>
<td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
<div align="left">打印Container的状态</div>
</td>
</tr></tbody></table><span style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">打印container(s)的报告</span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">示例1：</span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><pre><code class="language-html">[hadoop@hadoopcluster78 bin]$ yarn container -list appattempt_1437364567082_0106_01
15/08/10 20:45:45 INFO client.RMProxy: Connecting to ResourceManager at hadoopcluster79/10.0.1.79:8032
Total number of containers :25
                  Container-Id            Start Time             Finish Time                   State                    Host                                LOG-URL
container_1437364567082_0106_01_000028         1439210458659                       0                 RUNNING    hadoopcluster83:37140   //hadoopcluster83:8042/node/containerlogs/container_1437364567082_0106_01_000028/hadoop
container_1437364567082_0106_01_000016         1439210314436                       0                 RUNNING    hadoopcluster84:43818   //hadoopcluster84:8042/node/containerlogs/container_1437364567082_0106_01_000016/hadoop
container_1437364567082_0106_01_000019         1439210338598                       0                 RUNNING    hadoopcluster83:37140   //hadoopcluster83:8042/node/containerlogs/container_1437364567082_0106_01_000019/hadoop
container_1437364567082_0106_01_000004         1439210314130                       0                 RUNNING    hadoopcluster82:48622   //hadoopcluster82:8042/node/containerlogs/container_1437364567082_0106_01_000004/hadoop
container_1437364567082_0106_01_000008         1439210314130                       0                 RUNNING    hadoopcluster82:48622   //hadoopcluster82:8042/node/containerlogs/container_1437364567082_0106_01_000008/hadoop
container_1437364567082_0106_01_000031         1439210718604                       0                 RUNNING    hadoopcluster83:37140   //hadoopcluster83:8042/node/containerlogs/container_1437364567082_0106_01_000031/hadoop
container_1437364567082_0106_01_000020         1439210339601                       0                 RUNNING    hadoopcluster83:37140   //hadoopcluster83:8042/node/containerlogs/container_1437364567082_0106_01_000020/hadoop
container_1437364567082_0106_01_000005         1439210314130                       0                 RUNNING    hadoopcluster82:48622   //hadoopcluster82:8042/node/containerlogs/container_1437364567082_0106_01_000005/hadoop
container_1437364567082_0106_01_000013         1439210314435                       0                 RUNNING    hadoopcluster84:43818   //hadoopcluster84:8042/node/containerlogs/container_1437364567082_0106_01_000013/hadoop
container_1437364567082_0106_01_000022         1439210368679                       0                 RUNNING    hadoopcluster84:43818   //hadoopcluster84:8042/node/containerlogs/container_1437364567082_0106_01_000022/hadoop
container_1437364567082_0106_01_000021         1439210353626                       0                 RUNNING    hadoopcluster83:37140   //hadoopcluster83:8042/node/containerlogs/container_1437364567082_0106_01_000021/hadoop
container_1437364567082_0106_01_000014         1439210314435                       0                 RUNNING    hadoopcluster84:43818   //hadoopcluster84:8042/node/containerlogs/container_1437364567082_0106_01_000014/hadoop
container_1437364567082_0106_01_000029         1439210473726                       0                 RUNNING    hadoopcluster80:42366   //hadoopcluster80:8042/node/containerlogs/container_1437364567082_0106_01_000029/hadoop
container_1437364567082_0106_01_000006         1439210314130                       0                 RUNNING    hadoopcluster82:48622   //hadoopcluster82:8042/node/containerlogs/container_1437364567082_0106_01_000006/hadoop
container_1437364567082_0106_01_000003         1439210314129                       0                 RUNNING    hadoopcluster82:48622   //hadoopcluster82:8042/node/containerlogs/container_1437364567082_0106_01_000003/hadoop
container_1437364567082_0106_01_000015         1439210314436                       0                 RUNNING    hadoopcluster84:43818   //hadoopcluster84:8042/node/containerlogs/container_1437364567082_0106_01_000015/hadoop
container_1437364567082_0106_01_000009         1439210314130                       0                 RUNNING    hadoopcluster82:48622   //hadoopcluster82:8042/node/containerlogs/container_1437364567082_0106_01_000009/hadoop
container_1437364567082_0106_01_000030         1439210708467                       0                 RUNNING    hadoopcluster83:37140   //hadoopcluster83:8042/node/containerlogs/container_1437364567082_0106_01_000030/hadoop
container_1437364567082_0106_01_000012         1439210314435                       0                 RUNNING    hadoopcluster84:43818   //hadoopcluster84:8042/node/containerlogs/container_1437364567082_0106_01_000012/hadoop
container_1437364567082_0106_01_000027         1439210444354                       0                 RUNNING    hadoopcluster84:43818   //hadoopcluster84:8042/node/containerlogs/container_1437364567082_0106_01_000027/hadoop
container_1437364567082_0106_01_000026         1439210428514                       0                 RUNNING    hadoopcluster83:37140   //hadoopcluster83:8042/node/containerlogs/container_1437364567082_0106_01_000026/hadoop
container_1437364567082_0106_01_000017         1439210314436                       0                 RUNNING    hadoopcluster84:43818   //hadoopcluster84:8042/node/containerlogs/container_1437364567082_0106_01_000017/hadoop
container_1437364567082_0106_01_000001         1439210306902                       0                 RUNNING    hadoopcluster80:42366   //hadoopcluster80:8042/node/containerlogs/container_1437364567082_0106_01_000001/hadoop
container_1437364567082_0106_01_000002         1439210314129                       0                 RUNNING    hadoopcluster82:48622   //hadoopcluster82:8042/node/containerlogs/container_1437364567082_0106_01_000002/hadoop
container_1437364567082_0106_01_000025         1439210414171                       0                 RUNNING    hadoopcluster83:37140   //hadoopcluster83:8042/node/containerlogs/container_1437364567082_0106_01_000025/hadoop</code></pre><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">示例2：</span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><pre><code class="language-html">[hadoop@hadoopcluster78 bin]$ yarn container -status container_1437364567082_0105_01_000020
15/08/10 20:28:00 INFO client.RMProxy: Connecting to ResourceManager at hadoopcluster79/10.0.1.79:8032
Container Report :
    Container-Id : container_1437364567082_0105_01_000020
    Start-Time : 1439208779842
    Finish-Time : 0
    State : RUNNING
    LOG-URL : //hadoopcluster83:8042/node/containerlogs/container_1437364567082_0105_01_000020/hadoop
    Host : hadoopcluster83:37140
    Diagnostics : null
</code></pre><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">jar</span><span style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">使用:
 yarn jar &lt;jar&gt; [mainClass] args...</span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">运行jar文件，用户可以将写好的YARN代码打包成jar文件，用这个命令去运行它。</span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">logs</span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">使用: yarn logs -applicationId &lt;application ID&gt; [options]</span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">注：应用程序没有完成，该命令是不能打印日志的。</span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><table cellspacing="0" class="t_table" style="border-style:solid;border-color:rgb(227,237,245);border-collapse:collapse;table-layout:auto;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><tbody><tr><td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
<div align="left">命令选项</div>
</td>
<td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
<div align="left">描述</div>
</td>
</tr><tr><td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
<div align="left">-applicationId &lt;application ID&gt;</div>
</td>
<td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
<div align="left">指定应用程序ID，应用程序的ID可以在yarn.resourcemanager.webapp.address配置的路径查看（即：ID）</div>
</td>
</tr><tr><td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
<div align="left">-appOwner &lt;AppOwner&gt;</div>
</td>
<td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
<div align="left">应用的所有者（如果没有指定就是当前用户）应用程序的ID可以在yarn.resourcemanager.webapp.address配置的路径查看（即：User）</div>
</td>
</tr><tr><td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
<div align="left">-containerId &lt;ContainerId&gt;</div>
</td>
<td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
<div align="left">Container Id</div>
</td>
</tr><tr><td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
<div align="left">-help</div>
</td>
<td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
<div align="left">帮助</div>
</td>
</tr><tr><td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
<div align="left">-nodeAddress &lt;NodeAddress&gt;</div>
</td>
<td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
<div align="left">节点地址的格式：nodename:port （端口是配置文件中:yarn.nodemanager.webapp.address参数指定）</div>
</td>
</tr></tbody></table><span style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">转存container的日志。</span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">示例：</span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><div style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<div style="font-size:12px;"><pre><code class="language-html">[hadoop@hadoopcluster78 bin]$ yarn logs -applicationId application_1437364567082_0104  -appOwner hadoop
15/08/10 17:59:19 INFO client.RMProxy: Connecting to ResourceManager at hadoopcluster79/10.0.1.79:8032
Container: container_1437364567082_0104_01_000003 on hadoopcluster82_48622
============================================================================
LogType: stderr
LogLength: 0
Log Contents:
LogType: stdout
LogLength: 0
Log Contents:
LogType: syslog
LogLength: 3673
Log Contents:
2015-08-10 17:24:01,565 WARN [main] org.apache.hadoop.conf.Configuration: job.xml:an attempt to override final parameter: mapreduce.job.end-notification.max.retry.interval;  Ignoring.
2015-08-10 17:24:01,580 WARN [main] org.apache.hadoop.conf.Configuration: job.xml:an attempt to override final parameter: mapreduce.job.end-notification.max.attempts;  Ignoring.
。。。。。。此处省略N万个字符
// 下面的命令，根据APP的所有者查看LOG日志，因为application_1437364567082_0104任务我是用hadoop用户启动的，所以打印的是如下信息：
[hadoop@hadoopcluster78 bin]$ yarn logs -applicationId application_1437364567082_0104  -appOwner root
15/08/10 17:59:25 INFO client.RMProxy: Connecting to ResourceManager at hadoopcluster79/10.0.1.79:8032
Logs not available at /tmp/logs/root/logs/application_1437364567082_0104
Log aggregation has not completed or is not enabled.

</code></pre><br></div>
</div>
<span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">node</span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">使用: yarn node [options]</span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><table cellspacing="0" class="t_table" style="border-style:solid;border-color:rgb(227,237,245);border-collapse:collapse;table-layout:auto;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><tbody><tr><td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
<div align="left">命令选项</div>
</td>
<td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
<div align="left">描述</div>
</td>
</tr><tr><td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
<div align="left">-all</div>
</td>
<td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
<div align="left">所有的节点，不管是什么状态的。</div>
</td>
</tr><tr><td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
<div align="left">-list</div>
</td>
<td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
<div align="left">列出所有RUNNING状态的节点。支持-states选项过滤指定的状态，节点的状态包<br>
含：NEW，RUNNING，UNHEALTHY，DECOMMISSIONED，LOST，REBOOTED。支持--all显示所有的节点。</div>
</td>
</tr><tr><td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
<div align="left">-states &lt;States&gt;</div>
</td>
<td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
<div align="left">和-list配合使用，用逗号分隔节点状态，只显示这些状态的节点信息。</div>
</td>
</tr><tr><td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
<div align="left">-status &lt;NodeId&gt;</div>
</td>
<td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
<div align="left">打印指定节点的状态。</div>
</td>
</tr></tbody></table><span style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">示例1：</span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><pre><code class="language-html">[hadoop@hadoopcluster78 bin]$ ./yarn node -list -all
15/08/10 17:34:17 INFO client.RMProxy: Connecting to ResourceManager at hadoopcluster79/10.0.1.79:8032
Total Nodes:4
         Node-Id         Node-State Node-Http-Address   Number-of-Running-Containers
hadoopcluster82:48622           RUNNING hadoopcluster82:8042                               0
hadoopcluster84:43818           RUNNING hadoopcluster84:8042                               0
hadoopcluster83:37140           RUNNING hadoopcluster83:8042                               0
hadoopcluster80:42366           RUNNING hadoopcluster80:8042                               0
</code></pre><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">示例2：<br></span><pre><code class="language-html">[hadoop@hadoopcluster78 bin]$ ./yarn node -list -states RUNNING
15/08/10 17:39:55 INFO client.RMProxy: Connecting to ResourceManager at hadoopcluster79/10.0.1.79:8032
Total Nodes:4
         Node-Id         Node-State Node-Http-Address   Number-of-Running-Containers
hadoopcluster82:48622           RUNNING hadoopcluster82:8042                               0
hadoopcluster84:43818           RUNNING hadoopcluster84:8042                               0
hadoopcluster83:37140           RUNNING hadoopcluster83:8042                               0
hadoopcluster80:42366           RUNNING hadoopcluster80:8042                               0</code></pre><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><p><span style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">示例3：</span></p>
<p></p>
<pre><code class="language-html">[hadoop@hadoopcluster78 bin]$ ./yarn node -status hadoopcluster82:48622
15/08/10 17:52:52 INFO client.RMProxy: Connecting to ResourceManager at hadoopcluster79/10.0.1.79:8032
Node Report :
    Node-Id : hadoopcluster82:48622
    Rack : /default-rack
    Node-State : RUNNING
    Node-Http-Address : hadoopcluster82:8042
    Last-Health-Update : 星期一 10/八月/15 05:52:09:601CST
    Health-Report :
    Containers : 0
    Memory-Used : 0MB
    Memory-Capacity : 10240MB
    CPU-Used : 0 vcores
    CPU-Capacity : 8 vcores</code></pre><br><p></p>
<span style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">打印节点的报告。</span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">queue</span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">使用: yarn queue [options]</span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><table cellspacing="0" class="t_table" style="border-style:solid;border-color:rgb(227,237,245);border-collapse:collapse;table-layout:auto;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><tbody><tr><td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
<div align="left">命令选项</div>
</td>
<td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
<div align="left">描述</div>
</td>
</tr><tr><td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
<div align="left">-help</div>
</td>
<td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
<div align="left">帮助</div>
</td>
</tr><tr><td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
<div align="left">-status &lt;QueueName&gt;</div>
</td>
<td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
<div align="left">打印队列的状态</div>
</td>
</tr></tbody></table><span style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">打印队列信息。</span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">version</span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">使用: yarn version</span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">打印hadoop的版本。</span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="font-size:18px;">管理员命令：</span></span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">下列这些命令对hadoop集群的管理员是非常有用的。</span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">daemonlog</span><span style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">使用:</span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">   yarn daemonlog -getlevel &lt;host:httpport&gt; &lt;classname&gt;    yarn daemonlog -setlevel &lt;host:httpport&gt; &lt;classname&gt; &lt;level&gt;</span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><table cellspacing="0" class="t_table" style="border-style:solid;border-color:rgb(227,237,245);border-collapse:collapse;table-layout:auto;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><tbody><tr><td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
<div align="left">参数选项</div>
</td>
<td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
<div align="left">描述</div>
</td>
</tr><tr><td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
<div align="left">-getlevel &lt;host:httpport&gt; &lt;classname&gt;</div>
</td>
<td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
<div align="left">打印运行在&lt;host:port&gt;的守护进程的日志级别。这个命令内部会连接http://&lt;host:port&gt;/logLevel?log=&lt;name&gt;</div>
</td>
</tr><tr><td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
<div align="left">-setlevel &lt;host:httpport&gt; &lt;classname&gt; &lt;level&gt;</div>
</td>
<td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
<div align="left">设置运行在&lt;host:port&gt;的守护进程的日志级别。这个命令内部会连接http://&lt;host:port&gt;/logLevel?log=&lt;name&gt;</div>
</td>
</tr></tbody></table><span style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">针对指定的守护进程，获取/设置日志级别.</span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">示例1：</span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><div style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<div style="font-size:12px;"><pre><code class="language-html">[root@hadoopcluster78 ~]# hadoop daemonlog -getlevel hadoopcluster82:50075 org.apache.hadoop.hdfs.server.datanode.DataNode
Connecting to http://hadoopcluster82:50075/logLevel?log=org.apache.hadoop.hdfs.server.datanode.DataNode
Submitted Log Name: org.apache.hadoop.hdfs.server.datanode.DataNode
Log Class: org.apache.commons.logging.impl.Log4JLogger
Effective level: INFO
[root@hadoopcluster78 ~]# yarn daemonlog -getlevel hadoopcluster79:8088 org.apache.hadoop.yarn.server.resourcemanager.rmapp.RMAppImpl
Connecting to http://hadoopcluster79:8088/logLevel?log=org.apache.hadoop.yarn.server.resourcemanager.rmapp.RMAppImpl
Submitted Log Name: org.apache.hadoop.yarn.server.resourcemanager.rmapp.RMAppImpl
Log Class: org.apache.commons.logging.impl.Log4JLogger
Effective level: INFO
[root@hadoopcluster78 ~]# yarn daemonlog -getlevel hadoopcluster78:19888 org.apache.hadoop.mapreduce.v2.hs.JobHistory
Connecting to http://hadoopcluster78:19888/logLevel?log=org.apache.hadoop.mapreduce.v2.hs.JobHistory
Submitted Log Name: org.apache.hadoop.mapreduce.v2.hs.JobHistory
Log Class: org.apache.commons.logging.impl.Log4JLogger
Effective level: INFO</code></pre></div>
</div>
<span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">nodemanager</span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">使用: yarn nodemanager</span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">启动NodeManager</span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">proxyserver</span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">使用: yarn proxyserver</span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">启动web proxy server</span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">resourcemanager</span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">使用: yarn resourcemanager [-format-state-store]</span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><table cellspacing="0" class="t_table" style="border-style:solid;border-color:rgb(227,237,245);border-collapse:collapse;table-layout:auto;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><tbody><tr><td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
<div align="left">参数选项</div>
</td>
<td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
<div align="left">描述</div>
</td>
</tr><tr><td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
<div align="left">-format-state-store</div>
</td>
<td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
<div align="left">RMStateStore的格式. 如果过去的应用程序不再需要，则清理RMStateStore， RMStateStore仅仅在ResourceManager没有运行的时候，才运行RMStateStore</div>
</td>
</tr></tbody></table><span style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">启动ResourceManager</span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">rmadmin</span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">使用:</span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">  yarn rmadmin [-refreshQueues]               [-refreshNodes]               [-refreshUserToGroupsMapping]                [-refreshSuperUserGroupsConfiguration] 
              [-refreshAdminAcls]                [-refreshServiceAcl]               [-getGroups [username]]               [-transitionToActive [--forceactive] [--forcemanual] &lt;serviceId&gt;]               [-transitionToStandby [--forcemanual] &lt;serviceId&gt;]    
           [-failover [--forcefence] [--forceactive] &lt;serviceId1&gt; &lt;serviceId2&gt;]               [-getServiceState &lt;serviceId&gt;]               [-checkHealth &lt;serviceId&gt;]               [-help [cmd]]</span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><table cellspacing="0" class="t_table" style="border-style:solid;border-color:rgb(227,237,245);border-collapse:collapse;table-layout:auto;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><tbody><tr><td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
<div align="left">参数选项</div>
</td>
<td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
<div align="left">描述</div>
</td>
</tr><tr><td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
<div align="left">-refreshQueues</div>
</td>
<td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
<div align="left">重载队列的ACL，状态和调度器特定的属性，ResourceManager将重载mapred-queues配置文件</div>
</td>
</tr><tr><td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
<div align="left">-refreshNodes</div>
</td>
<td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
<div align="left">动态刷新dfs.hosts和dfs.hosts.exclude配置，无需重启NameNode。<br>
dfs.hosts：列出了允许连入NameNode的datanode清单（IP或者机器名）<br>
dfs.hosts.exclude：列出了禁止连入NameNode的datanode清单（IP或者机器名）<br>
重新读取hosts和exclude文件，更新允许连到Namenode的或那些需要退出或入编的Datanode的集合。</div>
</td>
</tr><tr><td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
<div align="left">-refreshUserToGroupsMappings</div>
</td>
<td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
<div align="left">刷新用户到组的映射。</div>
</td>
</tr><tr><td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
<div align="left">-refreshSuperUserGroupsConfiguration</div>
</td>
<td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
<div align="left">刷新用户组的配置</div>
</td>
</tr><tr><td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
<div align="left">-refreshAdminAcls</div>
</td>
<td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
<div align="left">刷新ResourceManager的ACL管理</div>
</td>
</tr><tr><td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
<div align="left">-refreshServiceAcl</div>
</td>
<td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
<div align="left">ResourceManager重载服务级别的授权文件。</div>
</td>
</tr><tr><td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
<div align="left">-getGroups [username]</div>
</td>
<td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
<div align="left">获取指定用户所属的组。</div>
</td>
</tr><tr><td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
<div align="left">-transitionToActive [–forceactive] [–forcemanual] &lt;serviceId&gt;</div>
</td>
<td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
<div align="left">尝试将目标服务转为 Active 状态。如果使用了–forceactive选项，不需要核对非Active节点。如果采用了自动故障转移，这个命令不能使用。虽然你可以重写–forcemanual选项，你需要谨慎。</div>
</td>
</tr><tr><td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
<div align="left">-transitionToStandby [–forcemanual] &lt;serviceId&gt;</div>
</td>
<td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
<div align="left">将服务转为 Standby 状态. 如果采用了自动故障转移，这个命令不能使用。虽然你可以重写–forcemanual选项，你需要谨慎。</div>
</td>
</tr><tr><td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
<div align="left">-failover [–forceactive] &lt;serviceId1&gt; &lt;serviceId2&gt;</div>
</td>
<td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
<div align="left">启动从serviceId1 到 serviceId2的故障转移。如果使用了-forceactive选项，即使服务没有准备，也会尝试故障转移到目标服务。如果采用了自动故障转移，这个命令不能使用。</div>
</td>
</tr><tr><td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
<div align="left">-getServiceState &lt;serviceId&gt;</div>
</td>
<td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
<div align="left">返回服务的状态。（注：ResourceManager不是HA的时候，时不能运行该命令的）</div>
</td>
</tr><tr><td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
<div align="left">-checkHealth &lt;serviceId&gt;</div>
</td>
<td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
<div align="left">请求服务器执行健康检查，如果检查失败，RMAdmin将用一个非零标示退出。（注：ResourceManager不是HA的时候，时不能运行该命令的）</div>
</td>
</tr><tr><td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
<div align="left">-help [cmd]</div>
</td>
<td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
<div align="left">显示指定命令的帮助，如果没有指定，则显示命令的帮助。</div>
</td>
</tr></tbody></table><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">scmadmin</span><span style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">使用:
 yarn scmadmin [options]</span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><table cellspacing="0" class="t_table" style="border-style:solid;border-color:rgb(227,237,245);border-collapse:collapse;table-layout:auto;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><tbody><tr><td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
<div align="left">参数选项</div>
</td>
<td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
<div align="left">描述</div>
</td>
</tr><tr><td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
<div align="left">-help</div>
</td>
<td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
<div align="left">Help</div>
</td>
</tr><tr><td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
<div align="left">-runCleanerTask</div>
</td>
<td style="font-size:14px;border-style:solid;border-color:rgb(227,237,245);overflow:hidden;">
<div align="left">Runs the cleaner task</div>
</td>
</tr></tbody></table><span style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">Runs Shared Cache Manager admin client</span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">sharedcachemanager</span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">使用: yarn sharedcachemanager</span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">启动Shared Cache Manager</span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">timelineserver</span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">之前yarn运行框架只有Job history server，这是hadoop2.4版本之后加的通用Job History Server，命令为Application Timeline Server，详情请看：The YARN Timeline Server</span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">使用: yarn timelineserver</span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">启动TimeLineServer</span><br><br></div>
<div><span style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><br></span></div>
            </div>
                </div>