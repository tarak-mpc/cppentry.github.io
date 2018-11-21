---
layout:     post
title:      CDH3 Install Guide
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<span style="color:rgb(51,51,51);font-family:arial, helvetica, tahoma, sans-serif;font-size:14px;line-height:21px;"></span>
<p style="margin-left:0px;border-top-width:0px;border-right-width:0px;border-bottom-width:0px;border-left-width:0px;font-size:14px;vertical-align:baseline;background-color:transparent;">
</p>
<h1 align="center">CDH3 Install Guide</h1>
<p></p>
<p>1       Install Hadoop</p>
<p>1.1     Add user hadoop</p>
<p align="left">[root@gd02 ~]# adduser hadoop</p>
<p>使用vim在/etc/group中将hadoop用户添加到mapred和hdfs组；</p>
<p>将mapred和hdfs用户添加到hadoop组。</p>
<p align="left">hadoop:x:105:mapred,hdfs</p>
<p align="left">hdfs:x:106:hadoop</p>
<p align="left">mapred:x:107:hadoop</p>
<p>1.2     Change hadoop’s privileges ofrelated directories.</p>
<p align="left">chown -R hadoop:hadoop /usr/lib/hadoop-0.20/</p>
<p align="left">chown -R hadoop:hadoop /usr/lib/hadoop-0.20/pids/</p>
<p align="left">chown -R hadoop:hadoop /usr/lib/hadoop-0.20/logs/</p>
<p align="left">chown -R hadoop:hadoop /usr/lib/hadoop-0.20/logs/<strong>*</strong></p>
<p>1.3     Format HDFS</p>
<p align="left">sudo -u hadoop hadoop namenode -format</p>
<p>1.4     Automated scripts</p>
<p>1.4.1 Init.sh</p>
<p align="left">#!/bin/bash</p>
<p align="left">chown -R hadoop:hadoop /usr/lib/hadoop-0.20/</p>
<p align="left">chown -R hadoop:hadoop /usr/lib/hadoop-0.20/pids/</p>
<p align="left">chown -R hadoop:hadoop /usr/lib/hadoop-0.20/logs/</p>
<p align="left"><strong>cd</strong> /usr/lib/hadoop-0.20/logs/</p>
<p align="left">chown -R hadoop:hadoop <strong>*</strong></p>
<p>1.4.2 Start-all.sh</p>
<p align="left">#!/bin/sh</p>
<p align="left">/etc/init.d/hadoop-0.20-namenode start</p>
<p align="left">/etc/init.d/hadoop-0.20-secondarynamenodestart</p>
<p align="left">/etc/init.d/hadoop-0.20-jobtracker start</p>
<p align="left">/etc/init.d/hadoop-zookeeper start</p>
<p align="left">/etc/init.d/hadoop-hbase-master start</p>
<p>1.4.3 Stop-all.sh</p>
<p align="left">#!/bin/sh</p>
<p align="left">/etc/init.d/hadoop-zookeeper stop</p>
<p align="left">/etc/init.d/hadoop-0.20-secondarynamenodestop</p>
<p align="left">/etc/init.d/hadoop-0.20-jobtracker stop</p>
<p align="left">/etc/init.d/hadoop-0.20-namenode stop</p>
<p>2       Install Hbase</p>
<p>2.1     Change hadoop’s privileges ofrelated directories.</p>
<p>修改HBase权限</p>
<p align="left">chown -R hadoop:hadoop /usr/lib/hbase/</p>
<p align="left">chown -R hadoop:hadoop /usr/lib/hbase/logs/ </p>
<p align="left">修改ZooKeeper权限</p>
<p align="left">chown -R hadoop:hadoop /local/zookeeper/</p>
<p>2.2     Automated scrips</p>
<p>3       Sqoop: Import Mysql to Hbase</p>
<p align="left">#/bin/bash</p>
<p align="left"> </p>
<p align="left">MySQL_Server<strong>=</strong>"10.10.97.116"</p>
<p align="left">MySQL_Port<strong>=</strong>"3306"</p>
<p align="left">DataBase<strong>=</strong>"rsearch"</p>
<p align="left"> </p>
<p align="left">sqoop import --connectjdbc:mysql://10.10.97.116:3306/rsearch --table institute --hbase-tableinstitute --column-family institute --hbase-row-key domain --hbase-create-table--username 'root' -P</p>
<p>4       Q&amp;A</p>
<p>4.1     Synctime error</p>
<p>2011-06-21 08:41:10,470 FATALorg.apache.hadoop.hbase.regionserver.HRegionServer: Master rejected startupbecause clock is out of sync<br>
org.apache.hadoop.hbase.ClockOutOfSyncException:org.apache.hadoop.hbase.ClockOutOfSyncException: Servergd03,60020,1308616870092 has been rejected; Reported time is too far out ofsync with master. Time difference of 50375801ms &gt; max allowed of 30000ms<br>
将集群中节点的时间同步</p>
<p>4.2     Permissionerror</p>
<p>2011-06-21 23:14:16,338 WARNorg.apache.hadoop.mapred.JobTracker: Failed to operate on mapred.system.dir(hdfs://gd02:9000/mapred/system) because of permissions.</p>
<p>删除datanode中的mapred.system.dir目录。 </p>
<p>rm –rf /local/dfs</p>
<br>            </div>
                </div>