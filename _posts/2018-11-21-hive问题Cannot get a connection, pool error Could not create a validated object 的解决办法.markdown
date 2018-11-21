---
layout:     post
title:      hive问题Cannot get a connection, pool error Could not create a validated object 的解决办法
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/lxpbs8851/article/details/8313686				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
[hadoop@master hive_dat]$ hive <br>
Logging initialized using configuration in file:/app/hive/conf/hive-log4j.properties<br>
Hive history file=/tmp/hadoop/hive_job_log_hadoop_201212181216_2004188466.txt<br>
hive&gt; show tables;<br>
FAILED: Error in metadata: javax.jdo.JDOFatalDataStoreException: Cannot get a connection, pool error Could not create a validated object, cause: A read-only user or a user in a read-only database is not permitted to disable read-only mode on a connection.<br>
NestedThrowables:<br>
org.apache.commons.dbcp.SQLNestedException: Cannot get a connection, pool error Could not create a validated object, cause: A read-only user or a user in a read-only database is not permitted to disable read-only mode on a connection.<br>
FAILED: Execution Error, return code 1 from org.apache.hadoop.hive.ql.exec.DDLTask<br>
hive&gt; select * from report_im_push_msg_log limit 10;<br>
FAILED: Error in semantic analysis: Unable to fetch table report_im_push_msg_log<br>
hive&gt; exit;<br>
[hadoop@master hive_dat]$ <br>
[hadoop@master hive_dat]$ l;l<br>
-bash: l: command not found<br>
-bash: l: command not found<br>
[hadoop@master hive_dat]$ ll<br>
total 2892096<br>
-rw-r--r-- 1 hadoop hadoop     195602 Dec 14 00:01 197_rp-login-20121214-1.log<br>
-rw-r--r-- 1 hadoop hadoop    1857051 Dec 14 00:18 199_rp-login-20121214-1.log<br>
-rw-r--r-- 1 hadoop hadoop 2947758976 Aug 27 15:12 applist_20120802.txt<br>
-rw-rw-r-- 1 hadoop hadoop        357 Dec 18 12:16 derby.log<br>
drwxrwxr-x 2 hadoop hadoop      36864 Dec 18 12:06 getui<br>
drwxrwxr-x 2 hadoop hadoop       4096 Dec 14 15:56 local_out.dat<br>
drwxrwxr-x 5 hadoop hadoop       4096 Dec 18 12:16 metastore_db<br>
drwxrwxr-x 2 hadoop hadoop       4096 Dec 14 17:55 package_name<br>
-rw-rw-r-- 1 hadoop hadoop    8728523 Dec 17 11:11 test.dat<br>
[hadoop@master hive_dat]$ <br><span style="color:#ff0000;">[hadoop@master hive_dat]$ cd metastore_db/</span><br>
[hadoop@master metastore_db]$ ll<br>
total 24<br><span style="color:#ff0000;">-rw-rw-r-- 1 hadoop hadoop    4 Dec 18 12:10 dbex.lck<br>
-rw-r--r-- 1 root   root     38 Dec 18 12:10 db.lck</span><br>
drwxrwxr-x 2 hadoop hadoop 4096 Dec 14 15:30 log<br>
drwxrwxr-x 2 hadoop hadoop 4096 Dec 14 15:34 seg0<br>
-rw-rw-r-- 1 hadoop hadoop  846 Dec 14 15:30 service.properties<br>
drwxrwxr-x 2 hadoop hadoop 4096 Dec 18 12:16 tmp<br><span style="color:#ff0000;">[hadoop@master metastore_db]$ mv dbex.lck 1218dbex.lck<br>
[hadoop@master metastore_db]$ mv db.lck 1218db.lck</span><br>
[hadoop@master metastore_db]$ cd ..<br>
[hadoop@master hive_dat]$ hive<br>
Logging initialized using configuration in file:/app/hive/conf/hive-log4j.properties<br>
Hive history file=/tmp/hadoop/hive_job_log_hadoop_201212181217_369609033.txt<br>
hive&gt; show tables;<br>
OK<br>
app_list<br>
report_cm_login_log<br>
report_im_push_msg_log<br>
Time taken: 4.26 seconds<br>
hive&gt; select * from report_im_push_msg_log limit 10;<br>
OK
            </div>
                </div>