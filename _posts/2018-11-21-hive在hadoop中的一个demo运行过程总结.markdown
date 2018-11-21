---
layout:     post
title:      hive在hadoop中的一个demo运行过程总结
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>参照http://blog.csdn.net/linghe301/article/details/9196713    这里的过程执行一个gis 的demo程序（依托hadoop和hive和mysql）</p>
<p>在hadoop和hive 上执行gis 的一个程序。</p>
<p>下载demo：</p>
<p></p>
<p style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:35px;">
<span style="font-family:'Microsoft YaHei';font-size:18px;">Esri/gis-tools-for-hadoop：<a href="https://github.com/Esri/gis-tools-for-hadoop" rel="nofollow" style="text-decoration:none;color:rgb(12,137,207);">https://github.com/Esri/gis-tools-for-hadoop</a></span></p>
<p style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:35px;">
</p>
<ul style="font-size:15px;color:rgb(51,51,51);font-family:Helvetica, arial, freesans, clean, sans-serif;line-height:25px;"><li>Sample tools that demonstrate full stack implementations of all the resources provided to solve GIS problems using Hadoop</li><li>Templates for building custom tools that solve specific problems</li></ul>
只保留部分有用的数据，其他删除，做好demo演示的前期工作。。。。。。。。。。。。
<p>在此之前已经建立好hadoop环境，mysql环境。</p>
<p>启动mysql建立hive数据库，建立用户名和密码hive_user，hive_pass。导入hive中scripts/metastore/upgrade/mysql/中的hive-schema-2.1.0.mysql文件到hive数据库中。</p>
<p>前期准备好，之后建立hive环境：</p>
<p>下载，解压。。。。</p>
<p>可以export hive路径，也可以不用这个不是必须.</p>
<p>在conf下面建立hive-ste.xml</p>
<p>&lt;configuration&gt;<br>
    &lt;!--以下是MySQL连接信息--&gt;<br>
    &lt;property&gt;<br>
        &lt;name&gt;javax.jdo.option.ConnectionURL&lt;/name&gt;<br>
        &lt;value&gt;jdbc:mysql://mysql主机名:3306/hive?createDatabaseIfNotExist=true&lt;/value&gt;<br>
    &lt;/property&gt;<br>
    &lt;property&gt;<br>
        &lt;name&gt;javax.jdo.option.ConnectionDriverName&lt;/name&gt;<br>
        &lt;value&gt;com.mysql.jdbc.Driver&lt;/value&gt;<br>
    &lt;/property&gt;<br>
    &lt;property&gt;<br>
        &lt;name&gt;javax.jdo.option.ConnectionUserName&lt;/name&gt;<br>
        &lt;value&gt;hive_user&lt;/value&gt;<br>
    &lt;/property&gt;<br>
    &lt;property&gt;<br>
        &lt;name&gt;javax.jdo.option.ConnectionPassword&lt;/name&gt;<br>
        &lt;value&gt;hive_pass&lt;/value&gt;<br>
    &lt;/property&gt;<br>
&lt;/configuration&gt;<br></p>
<p>这里没有弄明白metastore是不是存储到了mysql，这里有疑问？？？、！！！！</p>
<p>这里大概配置了，应该还有配置信息没配好，通过查找资料知道以下内容（大概的意思2启用了新的CLI：Beelin客户端和HiveServer2服务端）：</p>
<p></p>
<h1 id="HiveServer2Clients-Beeline–NewCommandLineShell" style="border-bottom-color:rgb(204,204,204);">
<span style="font-weight:normal;">Beeline – 一个新的命令行Shell</span></h1>
<p></p>
<p>
HiveServer2 supports a new command shell Beeline that works with HiveServer2. It's a JDBC client that is based on the SQLLine CLI (<a href="http://sqlline.sourceforge.net/" rel="nofollow" class="external-link">http://sqlline.sourceforge.net/</a>).
 There’s detailed <a href="http://sqlline.sourceforge.net/#manual" rel="nofollow" class="external-link">documentation</a> of SQLLine which is applicable to Beeline as well.</p>
<p>
The Beeline shell works in both embedded mode as well as remote mode. In the embedded mode, it runs an embedded Hive (similar to <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Cli" rel="nofollow">Hive CLI</a>) whereas
 remote mode is for connecting to a separate HiveServer2 process over Thrift. Starting in<a href="https://issues.apache.org/jira/browse/HIVE-7615" rel="nofollow" class="external-link">Hive 0.14</a>, when Beeline is used with
 HiveServer2, it also prints the log messages from HiveServer2 for queries it executes to STDERR.</p>
<p>
</p>
<div class="aui-message problem shadowed information-macro"><span class="aui-icon icon-problem">Icon</span>
<div class="message-content">
<p>
In remote mode HiveServer2 only accepts valid Thrift calls – even in HTTP mode, the message body contains Thrift payloads.</p>
</div>
</div>
<br><p>
</p>
<p>
Beeline 要与HiveServer2配合使用，支持嵌入模式和远程模式</p>
<p>
启动HiverServer2 , ./bin/hiveserver2 </p>
<p>
启动Beeline </p>
<p>
$ ./bin/beeline <br></p>
<p>
beeline&gt; !connect jdbc:hive2://localhost:10000</p>
<p>
</p>
<p style="color:rgb(51,51,51);font-family:Arial, sans-serif;font-size:14px;line-height:20px;">
https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients<br></p>
<p style="color:rgb(51,51,51);font-family:Arial, sans-serif;font-size:14px;line-height:20px;">
http://blog.cloudera.com/blog/2014/02/migrating-from-hive-cli-to-beeline-a-primer/</p>
<br>
----------------------------------------------------------------------------------------------------------------------------------------------------
<p>至此大概就配好了hive，还有很多东西和概念不知道，例如hive ql语句.可以参照：</p>
<p>http://www.cnblogs.com/HondaHsu/p/4346354.html<br></p>
<p>这里执行hive出现命令提示符，表示可以运行了。</p>
<p>将demo解压，将数据放入到/gis/data（这里是hdfs中的路径，需要hdfs dfs -put .........上传到hdfs中）中，其中有2个文件夹，一个是json格式，一个是cvs格式</p>
<p>
</p><table class="table" style="border-collapse:collapse;border-spacing:0px;color:rgb(51,51,51);font-family:sans-serif;font-size:14px;line-height:20px;"><thead><tr><th style="line-height:1.42857;vertical-align:bottom;border-top-width:0px;border-bottom-width:2px;border-bottom-style:solid;border-bottom-color:rgb(221,221,221);">
Permission</th>
<th style="line-height:1.42857;vertical-align:bottom;border-top-width:0px;border-bottom-width:2px;border-bottom-style:solid;border-bottom-color:rgb(221,221,221);">
Owner</th>
<th style="line-height:1.42857;vertical-align:bottom;border-top-width:0px;border-bottom-width:2px;border-bottom-style:solid;border-bottom-color:rgb(221,221,221);">
Group</th>
<th style="line-height:1.42857;vertical-align:bottom;border-top-width:0px;border-bottom-width:2px;border-bottom-style:solid;border-bottom-color:rgb(221,221,221);">
Size</th>
<th style="line-height:1.42857;vertical-align:bottom;border-top-width:0px;border-bottom-width:2px;border-bottom-style:solid;border-bottom-color:rgb(221,221,221);">
Last Modified</th>
<th style="line-height:1.42857;vertical-align:bottom;border-top-width:0px;border-bottom-width:2px;border-bottom-style:solid;border-bottom-color:rgb(221,221,221);">
Replication</th>
<th style="line-height:1.42857;vertical-align:bottom;border-top-width:0px;border-bottom-width:2px;border-bottom-style:solid;border-bottom-color:rgb(221,221,221);">
Block Size</th>
<th style="line-height:1.42857;vertical-align:bottom;border-top-width:0px;border-bottom-width:2px;border-bottom-style:solid;border-bottom-color:rgb(221,221,221);">
Name</th>
</tr></thead><tbody><tr><td style="line-height:1.42857;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
drwxr-xr-x</td>
<td style="line-height:1.42857;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
hadoop</td>
<td style="line-height:1.42857;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
supergroup</td>
<td style="line-height:1.42857;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
0 B</td>
<td style="line-height:1.42857;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
2016/7/3 下午5:43:00</td>
<td style="line-height:1.42857;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
0</td>
<td style="line-height:1.42857;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
0 B</td>
<td style="line-height:1.42857;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
<span style="color:#428bca;"><span>counties-data</span></span></td>
</tr><tr><td style="line-height:1.42857;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
drwxr-xr-x</td>
<td style="line-height:1.42857;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
hadoop</td>
<td style="line-height:1.42857;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
supergroup</td>
<td style="line-height:1.42857;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
0 B</td>
<td style="line-height:1.42857;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
2016/7/3 下午5:43:02</td>
<td style="line-height:1.42857;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
0</td>
<td style="line-height:1.42857;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
0 B</td>
<td style="line-height:1.42857;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
<span style="color:#428bca;"><span>earthquake-data</span></span></td>
</tr></tbody></table><p>修改demo中的</p>
<p>add jar<br>
  /home/hadoop/gis-for-hadoop/lib/esri-geometry-api.jar                     #这里是通过hive中的add jar 将包加入到环境中。home/hadoop/gis-for-had........这个是ubuntu里的物理路径。具体讲jar加入到哪里了，还不清楚，<br>
  /home/hadoop/gis-for-hadoop/lib/spatial-sdk-hadoop.jar;                 #不过，在运行的过程中，程序去hdfs中找这个两个文件，所以需要将这两个jar 传入到hdfs中，具体原因不知道。也许是程序代码的缘故，也许是<br>
                                                                                                                           #这里add jar，命令使用不得当的原因，具体问题在学习代码和hive命令之后才知道！！<br><br>
create temporary function ST_Point as 'com.esri.hadoop.hive.ST_Point';<br>
create temporary function ST_Contains as 'com.esri.hadoop.hive.ST_Contains';<br><br>
#将hdfs文件的信息存入表中，这里出现问题,localhost开始是用master的主机名代替的，可是不行，提示拒绝连接后来查找这里</p>
<p>#http://wiki.apache.org/hadoop/ConnectionRefused</p>
<p>#http://blog.csdn.net/z363115269/article/details/39048589</p>
<p>#http://www.iteblog.com/archives/802</p>
<p>#知道<span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">查询hive下的DBS表DB_LOCATION_URI列：select DB_LOCATION_URI from DBS; 这里是用了localhost的缘故。所有下面用了localhost。具体原因，不知道！！！这里需要进行学习！！！！</span><br>
CREATE EXTERNAL TABLE IF NOT EXISTS earthquakes (earthquake_date STRING, latitude DOUBLE, longitude DOUBLE, depth DOUBLE, magnitude DOUBLE,<br>
    magtype string, mbstations string, gap string, distance string, rms string, source string, eventid string)<br>
ROW FORMAT DELIMITED FIELDS TERMINATED BY ','<br>
LOCATION 'hdfs://localhost:9000/gis/data/earthquake-data';<br><br>
#导入另外一个文件到表中！！<br>
CREATE EXTERNAL TABLE IF NOT EXISTS counties (Area string, Perimeter string, State string, County string, Name string, BoundaryShape binary)                                         <br>
ROW FORMAT SERDE 'com.esri.hadoop.hive.serde.JsonSerde'              <br>
STORED AS INPUTFORMAT 'com.esri.json.hadoop.EnclosedJsonInputFormat'<br>
OUTPUTFORMAT 'org.apache.hadoop.hive.ql.io.HiveIgnoreKeyTextOutputFormat'<br>
LOCATION 'hdfs://localhost:9000/gis/data/counties-data';<br><br>
#这里是关键，hive的精华！！！我的理解用表管理用的的数据文件，然后通过hive sql 将任务分解为mapreduce的任务。依托hdfs实现文件管理和文件访问。用hive实现 将hive sql的语句转化成MR过程来执行，降低入门门槛</p>
<p>#用sql这样的句子代替编程！优点是对静态文件访问和MR过程变得简单！</p>
<p>SELECT counties.name, count(*) cnt FROM counties<br>
JOIN earthquakes<br>
WHERE ST_Contains(counties.boundaryshape, ST_Point(earthquakes.longitude, earthquakes.latitude))<br>
GROUP BY counties.name<br>
ORDER BY cnt desc;<br></p>
<p><br></p>
<p>进入hive,执行下面hive ql，看到下面的结果：</p>
<p>以下为运行结果：</p>
<p>hive&gt; SELECT counties.name, count(*) cnt FROM counties<br>
    &gt; JOIN earthquakes<br>
    &gt; WHERE ST_Contains(counties.boundaryshape, ST_Point(earthquakes.longitude, earthquakes.latitude))<br>
    &gt; GROUP BY counties.name<br>
    &gt; ORDER BY cnt desc;<br>
Warning: Map Join MAPJOIN[20][bigTable=?] in task 'Stage-2:MAPRED' is a cross product<br>
WARNING: Hive-on-MR is deprecated in Hive 2 and may not be available in the future versions. Consider using a different execution engine (i.e. tez, spark) or using Hive 1.X releases.<br>
Query ID = hadoop_20160703034243_ce75c555-bf04-48f9-9b5a-a6466a70a9e1<br>
Total jobs = 2<br>
SLF4J: Class path contains multiple SLF4J bindings.<br>
SLF4J: Found binding in [jar:file:/home/hadoop/apache-hive-2.1.0/lib/log4j-slf4j-impl-2.4.1.jar!/org/slf4j/impl/StaticLoggerBinder.class]<br>
SLF4J: Found binding in [jar:file:/home/hadoop/hadoop-2.7.0/share/hadoop/common/lib/slf4j-log4j12-1.7.10.jar!/org/slf4j/impl/StaticLoggerBinder.class]<br>
SLF4J: See http://www.slf4j.org/codes.html#multiple_bindings for an explanation.<br>
SLF4J: Actual binding is of type [org.apache.logging.slf4j.Log4jLoggerFactory]<br>
2016-07-03 03:42:58<span> </span>Starting to launch local task to process map join;<span></span>maximum memory = 518979584<br>
2016-07-03 03:43:02<span> </span>Dump the side-table for tag: 0 with group count: 1 into file: file:/tmp/hadoop/3e288758-d62b-4417-93e8-f379f4969ea2/hive_2016-07-03_03-42-43_138_2061802838610441568-1/-local-10006/HashTable-Stage-2/MapJoin-mapfile20--.hashtable<br>
2016-07-03 03:43:02<span> </span>Uploaded 1 File to: file:/tmp/hadoop/3e288758-d62b-4417-93e8-f379f4969ea2/hive_2016-07-03_03-42-43_138_2061802838610441568-1/-local-10006/HashTable-Stage-2/MapJoin-mapfile20--.hashtable (181836 bytes)<br>
2016-07-03 03:43:02<span> </span>End of local task; Time Taken: 4.391 sec.<br>
Execution completed successfully<br>
MapredLocal task succeeded<br>
Launching Job 1 out of 2<br>
Number of reduce tasks not specified. Estimated from input data size: 1<br>
In order to change the average load for a reducer (in bytes):<br>
  set hive.exec.reducers.bytes.per.reducer=&lt;number&gt;<br>
In order to limit the maximum number of reducers:<br>
  set hive.exec.reducers.max=&lt;number&gt;<br>
In order to set a constant number of reducers:<br>
  set mapreduce.job.reduces=&lt;number&gt;<br>
Job running in-process (local Hadoop)<br>
2016-07-03 03:43:10,532 Stage-2 map = 0%,  reduce = 0%<br>
2016-07-03 03:43:22,064 Stage-2 map = 100%,  reduce = 0%<br>
2016-07-03 03:43:23,077 Stage-2 map = 100%,  reduce = 100%<br>
Ended Job = job_local718067817_0003<br>
Launching Job 2 out of 2<br>
Number of reduce tasks determined at compile time: 1<br>
In order to change the average load for a reducer (in bytes):<br>
  set hive.exec.reducers.bytes.per.reducer=&lt;number&gt;<br>
In order to limit the maximum number of reducers:<br>
  set hive.exec.reducers.max=&lt;number&gt;<br>
In order to set a constant number of reducers:<br>
  set mapreduce.job.reduces=&lt;number&gt;<br>
Job running in-process (local Hadoop)<br>
2016-07-03 03:43:26,449 Stage-3 map = 100%,  reduce = 100%<br>
Ended Job = job_local1888560612_0004<br>
MapReduce Jobs Launched: <br>
Stage-Stage-2:  HDFS Read: 13516872 HDFS Write: 0 SUCCESS<br>
Stage-Stage-3:  HDFS Read: 15548312 HDFS Write: 0 SUCCESS<br>
Total MapReduce CPU Time Spent: 0 msec<br>
OK<br>
Kern<span> </span>36<br>
San Bernardino<span> </span>35<br>
Imperial<span> </span>28<br>
Inyo<span> </span>20<br>
Los Angeles<span> </span>18<br>
Riverside<span> </span>14<br>
Monterey<span> </span>14<br>
Santa Clara<span> </span>12<br>
Fresno<span> </span>11<br>
San Benito<span> </span>11<br>
San Diego<span> </span>7<br>
Santa Cruz<span> </span>5<br>
San Luis Obispo<span> </span>3<br>
Ventura<span> </span>3<br>
Orange<span> </span>2<br>
San Mateo<span> </span>1<br>
Time taken: 43.319 seconds, Fetched: 16 row(s)<br>
hive&gt; <br></p>
            </div>
                </div>