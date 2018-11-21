---
layout:     post
title:      hive中运行 select count(1) from test报错
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div>在cygwin模拟环境下hive中运行 select count(1) from test报错，但在linux环境中不出错</div>
<div><br></div>
<div><br></div>
<div>﻿﻿</div>
$ hive -e "select count(1) from info";<br>
Logging initialized using configuration in file:/C:/cygwin/usr/local/hive/conf/hive-log4j.properties<br>
Hive history file=/cygwin/usr/local/hive/tmp/hive_job_log_administrator_201310251451_1174374758.txt<br>
Total MapReduce jobs = 1<br>
Launching Job 1 out of 1<br>
Number of reduce tasks determined at compile time: 1<br>
In order to change the average load for a reducer (in bytes):<br>
  set hive.exec.reducers.bytes.per.reducer=&lt;number&gt;<br>
In order to limit the maximum number of reducers:<br>
  set hive.exec.reducers.max=&lt;number&gt;<br>
In order to set a constant number of reducers:<br>
  set mapred.reduce.tasks=&lt;number&gt;<br>
Starting Job = job_201310251450_0001, Tracking URL = <a href="http://localhost:50030/jobdetails.jsp?jobid=job_201310251450_0001" rel="nofollow">
http://localhost:50030/jobdetails.jsp?jobid=job_201310251450_0001</a><br>
Kill Command = C:\cygwin\usr\local\hadoop\\bin\hadoop.cmd job  -kill job_201310251450_0001<br>
Hadoop job information for Stage-1: number of mappers: 1; number of reducers: 1<br>
2013-10-25 14:51:50,576 Stage-1 map = 100%,  reduce = 100%<br>
Ended Job = job_201310251450_0001 with errors<br>
Error during job, obtaining debugging information...<br>
Job Tracking URL: <a href="http://localhost:50030/jobdetails.jsp?jobid=job_201310251450_0001" rel="nofollow">
http://localhost:50030/jobdetails.jsp?jobid=job_201310251450_0001</a><br>
Examining task ID: task_201310251450_0001_m_000002 (and more) from job job_201310251450_0001<br>
Exception in thread "Thread-21" java.lang.RuntimeException: Error while reading from task log url<br>
        at org.apache.hadoop.hive.ql.exec.errors.TaskLogProcessor.getStackTraces(TaskLogProcessor.java:247)<br>
        at org.apache.hadoop.hive.ql.exec.JobDebugger.showJobFailDebugInfo(JobDebugger.java:285)<br>
        at org.apache.hadoop.hive.ql.exec.JobDebugger.run(JobDebugger.java:118)<br>
        at java.lang.Thread.run(Thread.java:662)<br>
Caused by: java.io.FileNotFoundException: <a href="http://localhost:50060/tasklog?attemptid=attempt_201310251450_0001_m_000001_0" rel="nofollow">
http://localhost:50060/tasklog?attemptid=attempt_201310251450_0001_m_000001_0</a><br>
        at sun.net.www.protocol.http.HttpURLConnection.getInputStream(HttpURLConnection.java:1434)<br>
        at java.net.URL.openStream(URL.java:1010)<br>
        at org.apache.hadoop.hive.ql.exec.errors.TaskLogProcessor.getStackTraces(TaskLogProcessor.java:198)<br>
        ... 3 more<br>
FAILED: Execution Error, return code 2 from org.apache.hadoop.hive.ql.exec.MapRedTask<br>
MapReduce Jobs Launched:<br>
Job 0: Map: 1  Reduce: 1   HDFS Read: 0 HDFS Write: 0 FAIL<br>
Total MapReduce CPU Time Spent: 0 msec<br><div>﻿﻿</div>
            </div>
                </div>