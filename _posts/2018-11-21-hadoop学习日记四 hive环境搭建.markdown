---
layout:     post
title:      hadoop学习日记四 hive环境搭建
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>搭建hive环境</p>
<p> </p>
<p>从官网<a href="http://hive.apache.org/" rel="nofollow">http://hive.apache.org/</a>下载hive-0.8.1-bin.tar.gz，下载完成后copy hive-0.8.1-bin.tar.gz到服务器目录下</p>
<p> </p>
<p>执行tar -zxvf hive-0.8.1-bin.tar.gz 解压hive。</p>
<p> </p>
<p>将hive加入环境变量，主要是方便hive命令的执行，命令如下</p>
<p> </p>
<p> </p>
<pre><code class="language-java">exprot HIVE_HOME=/home/hive-0.8.1
exprot PATH=$HIVE_HOME:$PATH</code></pre>
<p> </p>
<p>将conf下面的template文件copy一份，便于个性化配置</p>
<p> </p>
<p> </p>
<pre><code class="language-java">cp hive-default.xml.template hive-default.xml 
cp hive-default.xml.template hive-site.xml
cp hive-env.sh.template hive-env.sh
cp hive-log4j.properties.template hive-log4j.properties
</code></pre>
<p> </p>
<p>在hive-env.sh中添加HADOOP_HOME的安装目录地址</p>
<p>在hive-log4j.properties中将log4j.appender.EventCounter的值修改为org.apache.hadoop.log.metrics.EventCounter，这样就不会报WARNING: org.apache.hadoop.metrics.jvm.EventCounter is deprecated. Please use org.apache.hadoop.log.metrics.EventCounter in all the log4j.properties files.的警告了。</p>
<p> </p>
<p>以上一切完成后，执行</p>
<p> </p>
<p> </p>
<pre><code class="language-java">root@wenbo00:/home/hive-0.8.1-bin/conf# hive</code></pre>
 
<p>进入hive命令行模式，然后执行</p>
<p> </p>
<p> </p>
<pre><code class="language-java">hive&gt; show tables;
OK
Time taken: 6.909 seconds
</code></pre>
 
<p>出现以上结果表示安装成功。</p>
<p> </p>
<p>执行建表语句</p>
<p> </p>
<p> </p>
<pre><code class="language-java">hive&gt; create table invites(<span style="background-color:#ffffff;font-family:'Courier New', Courier, monospace;font-size:13px;line-height:1.3;text-align:left;">foo INT, bar STRING</span>) partitioned by (ds STRING);
OK
Time taken: 5.918 seconds
</code></pre>
<p> </p>
<p>查看结果</p>
<p> </p>
<pre><code class="language-java">hive&gt; show tables;
OK
invites
Time taken: 0.246 seconds </code></pre>
<p> </p>
<p>加载数据</p>
<p> </p>
<p> </p>
<pre><code class="language-java"> hive&gt; LOAD DATA LOCAL INPATH '/home/wenbo/kv2.txt' OVERWRITE INTO TABLE invites PARTITION (ds='2012-03-16 17:56:25');
 hive&gt; LOAD DATA LOCAL INPATH '/home/wenbo/kv3.txt' OVERWRITE INTO TABLE invites PARTITION (ds='2012-03-16 17:57:25');</code></pre>
<p> </p>
<p>查询数据</p>
<p> </p>
<p>
</p>
<pre><code class="language-java">hive&gt; select count(*) from invites;
Total MapReduce jobs = 1
Launching Job 1 out of 1
Number of reduce tasks determined at compile time: 1
In order to change the average load for a reducer (in bytes):
  set hive.exec.reducers.bytes.per.reducer=&lt;number&gt;
In order to limit the maximum number of reducers:
  set hive.exec.reducers.max=&lt;number&gt;
In order to set a constant number of reducers:
  set mapred.reduce.tasks=&lt;number&gt;
Starting Job = job_201203160053_0004, Tracking URL = http://wenbo00:50030/jobdetails.jsp?jobid=job_201203160053_0004
Kill Command = /home/hadoop-1.0.1/libexec/../bin/hadoop job  -Dmapred.job.tracker=wenbo00:9001 -kill job_201203160053_0004
Hadoop job information for Stage-1: number of mappers: 1; number of reducers: 1
2012-03-16 03:03:25,739 Stage-1 map = 0%,  reduce = 0%
2012-03-16 03:03:37,819 Stage-1 map = 100%,  reduce = 0%, Cumulative CPU 6.29 sec
2012-03-16 03:03:38,825 Stage-1 map = 100%,  reduce = 0%, Cumulative CPU 6.29 sec
2012-03-16 03:03:39,837 Stage-1 map = 100%,  reduce = 0%, Cumulative CPU 6.29 sec
2012-03-16 03:03:40,852 Stage-1 map = 100%,  reduce = 0%, Cumulative CPU 6.29 sec
2012-03-16 03:03:41,870 Stage-1 map = 100%,  reduce = 0%, Cumulative CPU 6.29 sec
2012-03-16 03:03:42,879 Stage-1 map = 100%,  reduce = 0%, Cumulative CPU 6.29 sec
2012-03-16 03:03:43,885 Stage-1 map = 100%,  reduce = 0%, Cumulative CPU 6.29 sec
2012-03-16 03:03:44,898 Stage-1 map = 100%,  reduce = 0%, Cumulative CPU 6.29 sec
2012-03-16 03:03:45,907 Stage-1 map = 100%,  reduce = 0%, Cumulative CPU 6.29 sec
2012-03-16 03:03:46,914 Stage-1 map = 100%,  reduce = 0%, Cumulative CPU 6.29 sec
2012-03-16 03:03:47,926 Stage-1 map = 100%,  reduce = 0%, Cumulative CPU 6.29 sec
2012-03-16 03:03:48,933 Stage-1 map = 100%,  reduce = 0%, Cumulative CPU 6.29 sec
2012-03-16 03:03:49,949 Stage-1 map = 100%,  reduce = 100%, Cumulative CPU 9.74 sec
2012-03-16 03:03:50,958 Stage-1 map = 100%,  reduce = 100%, Cumulative CPU 9.74 sec
2012-03-16 03:03:51,964 Stage-1 map = 100%,  reduce = 100%, Cumulative CPU 9.74 sec
2012-03-16 03:03:52,978 Stage-1 map = 100%,  reduce = 100%, Cumulative CPU 9.74 sec
2012-03-16 03:03:53,997 Stage-1 map = 100%,  reduce = 100%, Cumulative CPU 9.74 sec
2012-03-16 03:03:55,016 Stage-1 map = 100%,  reduce = 100%, Cumulative CPU 9.74 sec
2012-03-16 03:03:56,029 Stage-1 map = 100%,  reduce = 100%, Cumulative CPU 9.74 sec
MapReduce Total cumulative CPU time: 9 seconds 740 msec
Ended Job = job_201203160053_0004
MapReduce Jobs Launched:
Job 0: Map: 1  Reduce: 1   Accumulative CPU: 9.74 sec   HDFS Read: 6342 HDFS Write: 4 SUCESS
Total MapReduce CPU Time Spent: 9 seconds 740 msec
OK
525
Time taken: 42.688 seconds
</code></pre>

<p> </p>
<p><span>	</span>查询的时候利用mapreduce创建任务执行，可惜我这里的环境是三台虚拟机运行在一台windows7上面，无法发挥集群优势，才导致一个简单的查询就耗费了将近43秒。</p>
<p> </p>            </div>
                </div>