---
layout:     post
title:      HBase笔记一：HBase的集群环境配置
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div><span style="font-family:'Courier New';font-size:18px;background-color:inherit;"><strong style="background-color:inherit;"><u style="background-color:inherit;">Hbase安装说明：</u></strong></span></div>
<div>
<h3 style="color:rgb(51,51,51);background-color:rgb(255,255,255);"><span style="font-family:'Courier New';background-color:inherit;">下载解压最新版本</span></h3>
<h3 style="color:rgb(51,51,51);background-color:rgb(255,255,255);"><span style="font-family:'Courier New';background-color:inherit;">选择一个 <a href="http://www.apache.org/dyn/closer.cgi/hbase/" rel="nofollow" style="color:rgb(51,102,153);background-color:inherit;">Apache
 下载镜像</a>：<a href="http://www.apache.org/dyn/closer.cgi/hbase/" rel="nofollow" style="color:rgb(51,102,153);background-color:inherit;">http://www.apache.org/dyn/closer.cgi/hbase/</a>，下载 <em style="background-color:inherit;">HBase Releases</em>.
 点击 stable目录，然后下载后缀为 .tar.gz 的文件; 例如 hbase-0.90.4.tar.gz.</span></h3>
<div style="color:rgb(51,51,51);background-color:rgb(255,255,255);">
<p style="background-color:inherit;"><strong style="background-color:inherit;"><span style="background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">后面需要安装集群，整合到hadoop，所以注意选择与hadoop对应的版本：</span></span></strong></p>
<p style="background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">选择 Hadoop 版本对HBase部署很关键。下表显示不同HBase支持的Hadoop版本信息。基于HBase版本，应该选择合适的Hadoop版本。我们没有绑定 Hadoop 发行版选择。可以从Apache使用 Hadoop 发行版，或了解一下Hadoop发行商产品： <a href="http://wiki.apache.org/hadoop/Distributions%20and%20Commercial%20Support" rel="nofollow" style="color:rgb(51,102,153);background-color:inherit;">http://wiki.apache.org/hadoop/Distributions%20and%20Commercial%20Support</a></span></p>
<div style="background-color:inherit;">
<p style="background-color:inherit;"><strong style="background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">Table 2.1. Hadoop version support matrix</span></strong></p>
<div style="background-color:inherit;">
<table border="1" cellspacing="0" cellpadding="2" style="display:table;border-collapse:collapse;background-color:inherit;"><thead style="background-color:inherit;"><tr style="background-color:inherit;"><th>
<div style="background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"><br style="background-color:inherit;"></span></div>
</th>
<th><span style="font-family:'Courier New';background-color:inherit;">HBase-0.92.x</span></th>
<th><span style="font-family:'Courier New';background-color:inherit;">HBase-0.94.x</span></th>
<th><span style="font-family:'Courier New';background-color:inherit;">HBase-0.96</span></th>
</tr></thead><tbody style="background-color:inherit;"><tr style="background-color:inherit;"><td><span style="font-family:'Courier New';background-color:inherit;">Hadoop-0.20.205</span></td>
<td><span style="font-family:'Courier New';background-color:inherit;">S</span></td>
<td><span style="font-family:'Courier New';background-color:inherit;">X</span></td>
<td><span style="font-family:'Courier New';background-color:inherit;">X</span></td>
</tr><tr style="background-color:inherit;"><td><span style="font-family:'Courier New';background-color:inherit;">Hadoop-0.22.x</span></td>
<td><span style="font-family:'Courier New';background-color:inherit;">S</span></td>
<td><span style="font-family:'Courier New';background-color:inherit;">X</span></td>
<td><span style="font-family:'Courier New';background-color:inherit;">X</span></td>
</tr><tr style="background-color:inherit;"><td><span style="font-family:'Courier New';background-color:inherit;">Hadoop-1.0.x</span></td>
<td><span style="font-family:'Courier New';background-color:inherit;">S</span></td>
<td><span style="font-family:'Courier New';background-color:inherit;">S</span></td>
<td><span style="font-family:'Courier New';background-color:inherit;">S</span></td>
</tr><tr style="background-color:inherit;"><td><span style="font-family:'Courier New';background-color:inherit;">Hadoop-1.1.x</span></td>
<td><span style="font-family:'Courier New';background-color:inherit;">NT</span></td>
<td><span style="font-family:'Courier New';background-color:inherit;">S</span></td>
<td><span style="font-family:'Courier New';background-color:inherit;">S</span></td>
</tr><tr style="background-color:inherit;"><td><span style="font-family:'Courier New';background-color:inherit;">Hadoop-0.23.x</span></td>
<td><span style="font-family:'Courier New';background-color:inherit;">X</span></td>
<td><span style="font-family:'Courier New';background-color:inherit;">S</span></td>
<td><span style="font-family:'Courier New';background-color:inherit;">NT</span></td>
</tr><tr style="background-color:inherit;"><td><span style="font-family:'Courier New';background-color:inherit;">Hadoop-2.x</span></td>
<td><span style="font-family:'Courier New';background-color:inherit;">X</span></td>
<td><span style="font-family:'Courier New';background-color:inherit;">S</span></td>
<td><span style="font-family:'Courier New';background-color:inherit;">S</span></td>
</tr></tbody></table></div>
</div>
<p style="background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"><br style="background-color:inherit;"></span></p>
<table border="1" cellspacing="0" cellpadding="2" style="display:table;border-collapse:collapse;background-color:inherit;"><tbody style="background-color:inherit;"><tr style="background-color:inherit;"><td><span style="font-family:'Courier New';background-color:inherit;">S = supported and tested,支持</span></td>
</tr><tr style="background-color:inherit;"><td><span style="font-family:'Courier New';background-color:inherit;">X = not supported,不支持</span></td>
</tr><tr style="background-color:inherit;"><td><span style="font-family:'Courier New';background-color:inherit;">NT = not tested enough.可以运行但测试不充分</span></td>
</tr></tbody></table><p style="background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">由于 HBase 依赖 Hadoop，它配套发布了一个Hadoop jar 文件在它的 lib 下。该套装jar仅用于独立模式。在分布式模式下，Hadoop版本必须和HBase下的版本一致。用你运行的分布式Hadoop版本jar文件替换HBase lib目录下的Hadoop jar文件，以避免版本不匹配问题。确认替换了集群中所有HBase下的jar文件。Hadoop版本不匹配问题有不同表现，但看起来都像挂掉了。</span></p>
<p style="background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"><br style="background-color:inherit;"></span></p>
<p style="background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"><br style="background-color:inherit;"></span></p>
<p style="background-color:inherit;"><strong style="background-color:inherit;"><u style="background-color:inherit;"><span style="font-family:'Courier New';font-size:18px;background-color:inherit;">HBase的集群安装：</span></u></strong></p>
<p style="background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"><br style="background-color:inherit;"></span></p>
<p style="background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">集群机器：hadoopmaster（Namenode） hadoopslaver（Datanode）</span></p>
<p style="background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"><strong>第一步：下载HBase包  tar -zxvf Hbase*.tar.gz  解压HBase</strong></span></p>
<p style="background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"><strong>第二步：<span style="background-color:rgb(255,255,255);">修改conf目录下的配置文件：</span></strong></span></p>
<p style="background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"><span style="background-color:rgb(255,255,255);">（1）</span><span style="background-color:rgb(255,255,255);">hbase-env.sh</span></span></p>
<table border="1" cellspacing="0" cellpadding="2" style="display:table;border-collapse:collapse;background-color:inherit;"><tbody style="background-color:inherit;"><tr style="background-color:inherit;"><td valign="top">
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"># <br style="background-color:inherit;">
#/** <br style="background-color:inherit;">
# * Copyright 2007 The Apache Software Foundation <br style="background-color:inherit;">
# * <br style="background-color:inherit;">
# * Licensed to the Apache Software Foundation (ASF) under one <br style="background-color:inherit;">
# * or more contributor license agreements. See the NOTICE file <br style="background-color:inherit;">
# * distributed with this work for additional information <br style="background-color:inherit;">
# * regarding copyright ownership. The ASF licenses this file <br style="background-color:inherit;">
# * to you under the Apache License, Version 2.0 (the <br style="background-color:inherit;">
# * "License"); you may not use this file except in compliance <br style="background-color:inherit;">
# * with the License. You may obtain a copy of the License at <br style="background-color:inherit;">
# * <br style="background-color:inherit;">
# * <a href="http://www.apache.org/licenses/LICENSE-2.0" rel="nofollow" style="background-color:inherit;">http://www.apache.org/licenses/LICENSE-2.0</a> <br style="background-color:inherit;">
# * <br style="background-color:inherit;">
# * Unless required by applicable law or agreed to in writing, software <br style="background-color:inherit;">
# * distributed under the License is distributed on an "AS IS" BASIS, <br style="background-color:inherit;">
# * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. <br style="background-color:inherit;">
# * See the License for the specific language governing permissions and <br style="background-color:inherit;">
# * limitations under the License. <br style="background-color:inherit;">
# */ <br style="background-color:inherit;"><br style="background-color:inherit;">
# Set environment variables here. <br style="background-color:inherit;"><br style="background-color:inherit;">
# The java implementation to use. Java 1.6 required. <br style="background-color:inherit;"><span style="color:#e60013;background-color:inherit;">export JAVA_HOME=/usr/devesoftware/java </span><br style="background-color:inherit;"><br style="background-color:inherit;">
# Extra Java CLASSPATH elements. Optional. <br style="background-color:inherit;"><span style="color:#e60013;background-color:inherit;">export HBASE_CLASSPATH=/usr/devesoftware/hadoop/conf </span><br style="background-color:inherit;"><br style="background-color:inherit;">
# The maximum amount of heap to use, in MB. Default is 1000. <br style="background-color:inherit;">
# export HBASE_HEAPSIZE=1000 <br style="background-color:inherit;"><br style="background-color:inherit;">
# Extra Java runtime options. <br style="background-color:inherit;">
# Below are what we set by default. May only work with SUN JVM. <br style="background-color:inherit;">
# For more on why as well as other possible settings, <br style="background-color:inherit;">
# see <a href="http://wiki.apache.org/hadoop/PerformanceTuning" rel="nofollow" style="background-color:inherit;">http://wiki.apache.org/hadoop/PerformanceTuning</a> <br style="background-color:inherit;">
export HBASE_OPTS="$HBASE_OPTS -XX:+UseConcMarkSweepGC" <br style="background-color:inherit;"><br style="background-color:inherit;">
# Uncomment below to enable java garbage collection logging in the .out file. <br style="background-color:inherit;">
# export HBASE_OPTS="$HBASE_OPTS -verbose:gc -XX:+PrintGCDetails -XX:+PrintGCDateStamps $HBASE_GC_OPTS" <br style="background-color:inherit;"><br style="background-color:inherit;">
# Uncomment below (along with above GC logging) to put GC information in its own logfile (will set HBASE_GC_OPTS) <br style="background-color:inherit;">
# export HBASE_USE_GC_LOGFILE=true <br style="background-color:inherit;"><br style="background-color:inherit;"><br style="background-color:inherit;">
# Uncomment below if you intend to use the EXPERIMENTAL off heap cache. <br style="background-color:inherit;">
# export HBASE_OPTS="$HBASE_OPTS -XX:MaxDirectMemorySize=" <br style="background-color:inherit;">
# Set hbase.offheapcache.percentage in hbase-site.xml to a nonzero value. <br style="background-color:inherit;"><br style="background-color:inherit;"><br style="background-color:inherit;">
# Uncomment and adjust to enable JMX exporting <br style="background-color:inherit;">
# See jmxremote.password and jmxremote.access in $JRE_HOME/lib/management to configure remote password access. <br style="background-color:inherit;">
# More details at: <a href="http://java.sun.com/javase/6/docs/technotes/guides/management/agent.html" rel="nofollow" style="background-color:inherit;">http://java.sun.com/javase/6/docs/technotes/guides/management/agent.html</a> <br style="background-color:inherit;">
# <br style="background-color:inherit;">
# export HBASE_JMX_BASE="-Dcom.sun.management.jmxremote.ssl=false -Dcom.sun.management.jmxremote.authenticate=false" <br style="background-color:inherit;">
# export HBASE_MASTER_OPTS="$HBASE_MASTER_OPTS $HBASE_JMX_BASE -Dcom.sun.management.jmxremote.port=10101" <br style="background-color:inherit;">
# export HBASE_REGIONSERVER_OPTS="$HBASE_REGIONSERVER_OPTS $HBASE_JMX_BASE -Dcom.sun.management.jmxremote.port=10102" <br style="background-color:inherit;">
# export HBASE_THRIFT_OPTS="$HBASE_THRIFT_OPTS $HBASE_JMX_BASE -Dcom.sun.management.jmxremote.port=10103" <br style="background-color:inherit;">
# export HBASE_ZOOKEEPER_OPTS="$HBASE_ZOOKEEPER_OPTS $HBASE_JMX_BASE -Dcom.sun.management.jmxremote.port=10104" <br style="background-color:inherit;"><br style="background-color:inherit;">
# File naming hosts on which HRegionServers will run. $HBASE_HOME/conf/regionservers by default. <br style="background-color:inherit;">
# export HBASE_REGIONSERVERS=${HBASE_HOME}/conf/regionservers <br style="background-color:inherit;"><br style="background-color:inherit;">
# File naming hosts on which backup HMaster will run. $HBASE_HOME/conf/backup-masters by default. <br style="background-color:inherit;">
# export HBASE_BACKUP_MASTERS=${HBASE_HOME}/conf/backup-masters <br style="background-color:inherit;"><br style="background-color:inherit;">
# Extra ssh options. Empty by default. <br style="background-color:inherit;">
# export HBASE_SSH_OPTS="-o ConnectTimeout=1 -o SendEnv=HBASE_CONF_DIR" <br style="background-color:inherit;"><br style="background-color:inherit;">
# Where log files are stored. $HBASE_HOME/logs by default. <br style="background-color:inherit;">
# export HBASE_LOG_DIR=${HBASE_HOME}/logs <br style="background-color:inherit;"><br style="background-color:inherit;">
# Enable remote JDWP debugging of major HBase processes. Meant for Core Developers <br style="background-color:inherit;">
# export HBASE_MASTER_OPTS="$HBASE_MASTER_OPTS -Xdebug -Xrunjdwp:transport=dt_socket,server=y,suspend=n,address=8070" <br style="background-color:inherit;">
# export HBASE_REGIONSERVER_OPTS="$HBASE_REGIONSERVER_OPTS -Xdebug -Xrunjdwp:transport=dt_socket,server=y,suspend=n,address=8071" <br style="background-color:inherit;">
# export HBASE_THRIFT_OPTS="$HBASE_THRIFT_OPTS -Xdebug -Xrunjdwp:transport=dt_socket,server=y,suspend=n,address=8072" <br style="background-color:inherit;">
# export HBASE_ZOOKEEPER_OPTS="$HBASE_ZOOKEEPER_OPTS -Xdebug -Xrunjdwp:transport=dt_socket,server=y,suspend=n,address=8073" <br style="background-color:inherit;"><br style="background-color:inherit;">
# A string representing this instance of hbase. $USER by default. <br style="background-color:inherit;">
# export HBASE_IDENT_STRING=$USER <br style="background-color:inherit;"><br style="background-color:inherit;">
# The scheduling priority for daemon processes. See 'man nice'. <br style="background-color:inherit;">
# export HBASE_NICENESS=10 <br style="background-color:inherit;"><br style="background-color:inherit;">
# The directory where pid files are stored. /tmp by default. <br style="background-color:inherit;">
# export HBASE_PID_DIR=/var/hadoop/pids <br style="background-color:inherit;"><br style="background-color:inherit;">
# Seconds to sleep between slave commands. Unset by default. This <br style="background-color:inherit;">
# can be useful in large clusters, where, e.g., slave rsyncs can <br style="background-color:inherit;">
# otherwise arrive faster than the master can service them. <br style="background-color:inherit;">
# export HBASE_SLAVE_SLEEP=0.1 <br style="background-color:inherit;"><br style="background-color:inherit;">
# Tell HBase whether it should manage it's own instance of Zookeeper or not. <br style="background-color:inherit;"><span style="color:#e60013;background-color:inherit;">export HBASE_MANAGES_ZK=true</span></span></div>
</td>
</tr></tbody></table><p style="background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">（2）</span><span style="background-color:rgb(255,255,255);">hbase-site.xml</span></p>
<table border="1" cellspacing="0" cellpadding="2" style="display:table;border-collapse:collapse;background-color:inherit;"><tbody style="background-color:inherit;"><tr style="background-color:inherit;"><td valign="top">
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">/** <br style="background-color:inherit;">
* Copyright 2010 The Apache Software Foundation <br style="background-color:inherit;">
* <br style="background-color:inherit;">
* Licensed to the Apache Software Foundation (ASF) under one <br style="background-color:inherit;">
* or more contributor license agreements. See the NOTICE file <br style="background-color:inherit;">
* distributed with this work for additional information <br style="background-color:inherit;">
* regarding copyright ownership. The ASF licenses this file <br style="background-color:inherit;">
* to you under the Apache License, Version 2.0 (the <br style="background-color:inherit;">
* "License"); you may not use this file except in compliance <br style="background-color:inherit;">
* with the License. You may obtain a copy of the License at <br style="background-color:inherit;">
* <br style="background-color:inherit;">
* <a href="http://www.apache.org/licenses/LICENSE-2.0" rel="nofollow" style="background-color:inherit;">http://www.apache.org/licenses/LICENSE-2.0</a> <br style="background-color:inherit;">
* <br style="background-color:inherit;">
* Unless required by applicable law or agreed to in writing, software <br style="background-color:inherit;">
* distributed under the License is distributed on an "AS IS" BASIS, <br style="background-color:inherit;">
* WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. <br style="background-color:inherit;">
* See the License for the specific language governing permissions and <br style="background-color:inherit;">
* limitations under the License. <br style="background-color:inherit;">
*/ <br style="background-color:inherit;">
--&gt; <br style="background-color:inherit;"><span style="color:#e60013;background-color:inherit;">&lt;configuration&gt; <br style="background-color:inherit;">
&lt;property&gt;</span></span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"><span style="color:#e60013;background-color:inherit;">#</span></span><span style="font-family:'Courier New';background-color:rgb(255,255,255);">hbase.rootdir
 hbase在hdfs上的数据存储目录</span><span style="font-family:'Courier New';background-color:inherit;"><span style="color:#e60013;background-color:inherit;"><br style="background-color:inherit;">
&lt;name&gt;hbase.rootdir&lt;/name&gt; <br style="background-color:inherit;">
&lt;value&gt;hdfs://hadoopmaster:9000/hbase&lt;/value&gt; <br style="background-color:inherit;">
&lt;/property&gt;</span></span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"><span style="color:#e60013;background-color:inherit;">#</span></span><span style="font-family:'Courier New';background-color:rgb(255,255,255);">hbase.cluster.distributed指定是否是完全分布式，如果是单机模式或者伪分布式，则设置为false</span><span style="font-family:'Courier New';background-color:inherit;"><span style="color:#e60013;background-color:inherit;"><br style="background-color:inherit;">
&lt;property&gt; <br style="background-color:inherit;">
&lt;name&gt;hbase.cluster.distributed&lt;/name&gt; <br style="background-color:inherit;">
&lt;value&gt;true&lt;/value&gt; <br style="background-color:inherit;">
&lt;/property&gt; </span></span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"><span style="color:#e60013;background-color:inherit;">#</span></span><span style="font-family:'Courier New';background-color:rgb(255,255,255);">hbase.master指定master的位置</span><span style="font-family:'Courier New';background-color:inherit;"><span style="color:#e60013;background-color:inherit;"><br style="background-color:inherit;">
&lt;property&gt; <br style="background-color:inherit;">
&lt;name&gt;hbase.master&lt;/name&gt; <br style="background-color:inherit;">
&lt;value&gt;hdfs://hadoopmaster:60000&lt;/value&gt; <br style="background-color:inherit;">
&lt;/property&gt; </span></span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"><span style="color:#e60013;background-color:inherit;">#</span></span><span style="font-family:'Courier New';background-color:rgb(255,255,255);">hbase.zookeeper.quorum指定zookeeper的分布式部署机器没，多个以逗号分隔</span><span style="font-family:'Courier New';background-color:inherit;"><span style="color:#e60013;background-color:inherit;"><br style="background-color:inherit;">
&lt;property&gt; <br style="background-color:inherit;">
&lt;name&gt;hbase.zookeeper.quorum&lt;/name&gt; <br style="background-color:inherit;">
&lt;value&gt;hadoopmaster,hadoopslaver&lt;/value&gt; <br style="background-color:inherit;">
&lt;/property&gt; <br style="background-color:inherit;">
&lt;/configuration&gt;</span></span></div>
</td>
</tr></tbody></table>
    
<p style="font-family:Simsun;background-color:inherit;">（3）<span style="font-family:'微软雅黑';background-color:rgb(255,255,255);">regionservers</span></p>
<p style="font-family:Simsun;background-color:inherit;"><span style="font-family:'微软雅黑';background-color:rgb(255,255,255);"><br style="background-color:inherit;"></span></p>
<table border="1" cellspacing="0" cellpadding="2" style="display:table;border-collapse:collapse;background-color:inherit;"><tbody style="background-color:inherit;"><tr style="background-color:inherit;"><td valign="top">
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';color:#e60013;background-color:inherit;">hadoopmaster</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';color:#e60013;background-color:inherit;">hadoopslaver</span></div>
</td>
</tr></tbody></table><br style="background-color:inherit;"></div>
</div>
<div>（4）最后修改Hadoop hdfs-site.xml</div>
<div><br style="background-color:inherit;"></div>
<div>
<table border="1" cellspacing="0" cellpadding="2" style="display:table;border-collapse:collapse;background-color:inherit;"><tbody style="background-color:inherit;"><tr style="background-color:inherit;"><td valign="top">
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">&lt;?xml version="1.0"?&gt;</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">&lt;?xml-stylesheet type="text/xsl" href="configuration.xsl"?&gt;</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"></span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">&lt;!-- Put site-specific property overrides in this file. --&gt;</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"></span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">&lt;configuration&gt;</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">    &lt;property&gt;</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">        &lt;name&gt;dfs.replication&lt;/name&gt;</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">        &lt;value&gt;1&lt;/value&gt;</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">    &lt;/property&gt;</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">    &lt;property&gt;</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">        &lt;name&gt;dfs.name.dir&lt;/name&gt;</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">        &lt;value&gt;/home/${user.name}/dfs/name&lt;/value&gt;</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">    &lt;/property&gt;</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">    &lt;property&gt;</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">        &lt;name&gt;dfs.data.dir&lt;/name&gt;</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">        &lt;value&gt;/home/${user.name}/dfs/data&lt;/value&gt;</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">    &lt;/property&gt;</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">    <span style="color:#e60013;background-color:inherit;">#</span></span><span style="background-color:rgb(255,255,255);"><span style="color:#e60013;background-color:inherit;">该参数限制了datanode所允许同时执行的发送和接受任务的数量，缺省为256，hadoop-defaults.xml中通常不设置这个参数。这个限制看来实际有些偏小</span></span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';color:#e60013;background-color:inherit;">    &lt;property&gt;</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';color:#e60013;background-color:inherit;">        &lt;name&gt;dfs.datanode.max.xcievers&lt;/name&gt;</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';color:#e60013;background-color:inherit;">        &lt;value&gt;4096&lt;/value&gt;</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"><span style="color:#e60013;background-color:inherit;">    &lt;/property&gt;</span></span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">&lt;/configuration&gt;</span></div>
</td>
</tr></tbody></table><br style="background-color:inherit;"></div>
<div><strong>第三步：拷贝hbase到所有的节点上，按照实际机器路径配置</strong></div>
<div><strong><br style="background-color:inherit;"></strong></div>
<div><strong>第四步：修改etc/profile 增加HBASE_HOME</strong></div>
<div><br style="background-color:inherit;"></div>
<div>
<table border="1" cellspacing="0" cellpadding="2" style="display:table;border-collapse:collapse;background-color:inherit;"><tbody style="background-color:inherit;"><tr style="background-color:inherit;"><td valign="top">
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';color:#262626;background-color:inherit;">#set java path</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';color:#262626;background-color:inherit;">JAVA_HOME=/usr/devesoftware/java</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';color:#262626;background-color:inherit;">#set hadoop path</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';color:#262626;background-color:inherit;">HADOOP_HOME=/usr/devesoftware/hadoop</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';color:#e60013;background-color:inherit;">#set hbase path</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';color:#e60013;background-color:inherit;">HBASE_HOME=/usr/devesoftware/hbase</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';color:#262626;background-color:inherit;">CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';color:#262626;background-color:inherit;">PATH=$HBASE_HOME/bin:$HADOOP_HOME/bin:$JAVA_HOME/bin:$PATH</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';color:#262626;background-color:inherit;">export HBASE_HOME HADOOP_HOME JAVA_HOME CLASSPATH PATH</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';color:#262626;background-color:inherit;">export HADOOP_HOME_WARN_SUPPRESS=1</span></div>
</td>
</tr></tbody></table></div>
<div><br style="background-color:inherit;"></div>
<div><strong>第五步：在NameNode上启动</strong></div>
<div><br style="background-color:inherit;"></div>
<div>启动顺序：先启动hadoop，再次启动hbase</div>
<div><br style="background-color:inherit;"></div>
<div>
<table border="1" cellspacing="0" cellpadding="2" style="display:table;border-collapse:collapse;background-color:inherit;"><tbody style="background-color:inherit;"><tr style="background-color:inherit;"><td valign="top">
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';color:#e60013;background-color:inherit;">[<a href="mailto:hadoop@hadoopmaster" rel="nofollow" style="background-color:inherit;">hadoop@hadoopmaster</a> ~]$
 start-all.sh</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';color:#e60013;background-color:inherit;">[<a href="mailto:hadoop@hadoopmaster" rel="nofollow" style="background-color:inherit;">hadoop@hadoopmaster</a> ~]$
 start-hbase.sh</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';color:#e60013;background-color:inherit;">[<a href="mailto:hadoop@hadoopmaster" rel="nofollow" style="background-color:inherit;">hadoop@hadoopmaster</a> ~]$
 jps <br style="background-color:inherit;">
4129 TaskTracker <br style="background-color:inherit;">
3906 SecondaryNameNode <br style="background-color:inherit;"><strong style="background-color:inherit;">4675 HRegionServer </strong><br style="background-color:inherit;">
4003 JobTracker <br style="background-color:inherit;">
3668 NameNode <br style="background-color:inherit;"><strong style="background-color:inherit;">4468 HQuorumPeer </strong><br style="background-color:inherit;">
3784 DataNode <br style="background-color:inherit;">
4745 Jps <br style="background-color:inherit;"><strong style="background-color:inherit;">4524 HMaster</strong></span></div>
</td>
</tr></tbody></table></div>
<div><br style="background-color:inherit;"></div>
<div>查看DataNode节点进程启动情况：</div>
<div><br style="background-color:inherit;"></div>
<div>
<table border="1" cellspacing="0" cellpadding="2" style="display:table;border-collapse:collapse;background-color:inherit;"><tbody style="background-color:inherit;"><tr style="background-color:inherit;"><td valign="top">
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';color:#e60013;background-color:inherit;">[<a href="mailto:hadoop@hadoopslaver" rel="nofollow" style="background-color:inherit;">hadoop@hadoopslaver</a> ~]$
 jps <br style="background-color:inherit;">
4016 Jps <br style="background-color:inherit;"><strong style="background-color:inherit;">3636 HRegionServer </strong><br style="background-color:inherit;">
3461 TaskTracker <br style="background-color:inherit;">
3358 DataNode <br style="background-color:inherit;"><strong style="background-color:inherit;">3551 HQuorumPeer</strong></span></div>
</td>
</tr></tbody></table></div>
<div><br style="background-color:inherit;"></div>
<div><strong>第六步：进入hbase shll客户端</strong></div>
<div><br style="background-color:inherit;"></div>
<div>
<table border="1" cellspacing="0" cellpadding="2" style="display:table;border-collapse:collapse;background-color:inherit;"><tbody style="background-color:inherit;"><tr style="background-color:inherit;"><td valign="top">
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';color:#e60013;background-color:inherit;">[<a href="mailto:hadoop@hadoopmaster" rel="nofollow" style="background-color:inherit;">hadoop@hadoopmaster</a> conf]$
 hbase shell <br style="background-color:inherit;">
HBase Shell; enter 'help&lt;RETURN&gt;' for list of supported commands. <br style="background-color:inherit;">
Type "exit&lt;RETURN&gt;" to leave the HBase Shell <br style="background-color:inherit;">
Version 0.94.2, r1395367, Sun Oct 7 19:11:01 UTC 2012 <br style="background-color:inherit;"><br style="background-color:inherit;">
hbase(main):001:0&gt; list <br style="background-color:inherit;">
TABLE <br style="background-color:inherit;">
0 row(s) in 1.0800 seconds <br style="background-color:inherit;"><br style="background-color:inherit;">
hbase(main):002:0&gt;</span></div>
</td>
</tr></tbody></table></div>
<div><br style="background-color:inherit;"></div>
<div>至此，hbase集群安装成功！！！！</div>
<div><br></div>
<div>
<div><br><br style="background-color:inherit;"></div>
<div>如果，整合Mapreduce和Hbase开发，那么需要进入$HADOOP_HOME/conf中，修改hadoop-env.sh文件：</div>
<div>在Hadoop集群中添加hbase引用：</div>
<table border="1" cellspacing="0" cellpadding="2"><tbody style="background-color:inherit;"><tr style="background-color:inherit;"><td valign="top">
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"># Set Hadoop-specific environment variables here.</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"></span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"># The only required environment variable is JAVA_HOME. All others are</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"># optional. When running a distributed configuration it is best to</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"># set JAVA_HOME in this file, so that it is correctly defined on</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"># remote nodes.</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"></span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"># Extra Java CLASSPATH elements. Optional.</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"># export HADOOP_CLASSPATH=</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"></span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"># The maximum amount of heap to use, in MB. Default is 1000.</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"># export HADOOP_HEAPSIZE=2000</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"></span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"># Extra Java runtime options. Empty by default.</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"># export HADOOP_OPTS=-server</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"></span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"># Command specific options appended to HADOOP_OPTS when specified</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"></span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"># The only required environment variable is JAVA_HOME. All others are</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"># optional. When running a distributed configuration it is best to</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"># set JAVA_HOME in this file, so that it is correctly defined on</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"># remote nodes.</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"></span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"># Extra Java CLASSPATH elements. Optional.</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"># export HADOOP_CLASSPATH=</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"></span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"># The maximum amount of heap to use, in MB. Default is 1000.</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"># export HADOOP_HEAPSIZE=2000</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"></span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"># Extra Java runtime options. Empty by default.</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"># export HADOOP_OPTS=-server</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"></span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"># Command specific options appended to HADOOP_OPTS when specified</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"># export HADOOP_TASKTRACKER_OPTS=</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"># The following applies to multiple commands (fs, dfs, fsck, distcp etc)</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"># export HADOOP_CLIENT_OPTS</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"></span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"># Extra ssh options. Empty by default.</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"># export HADOOP_SSH_OPTS="-o ConnectTimeout=1 -o SendEnv=HADOOP_CONF_DIR"</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"></span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"># Where log files are stored. $HADOOP_HOME/logs by default.</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"># export HADOOP_LOG_DIR=${HADOOP_HOME}/logs</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"></span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"># remote nodes.</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"></span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"># The java implementation to use. Required.</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"></span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"></span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">export JAVA_HOME=/usr/devesoftware/java</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">export HADOOP_HEAPSIZE=2000</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">export HADOOP_PID_DIR=/home/$USER/pids</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';color:#ff0000;background-color:inherit;"><strong style="background-color:inherit;">export HBASE_HOME=/usr/devesoftware/hbase</strong></span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';color:#ff0000;background-color:inherit;"><strong style="background-color:inherit;">export HADOOP_CLASSPATH=$HBASE_HOME/hbase-0.94.2.jar:$HBASE_HOME/hbase-0.94.2-t</strong></span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';color:#ff0000;background-color:inherit;"><strong style="background-color:inherit;">ests.jar:$HBASE_HOME/conf:</strong></span><strong style="color:rgb(255,0,0);font-family:'Courier New';background-color:rgb(255,255,255);">$HBASE_HOME/lib:$HBASE_HOME/lib/zookeeper-3.4.5.jar:$HBASE_HOME/lib/protobuf-java-2.4.0a.jar:$HBASE_HOME/lib/guava-11.0.2.jar</strong></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"></span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"></span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"></span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"># Extra Java CLASSPATH elements. Optional.</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"># export HADOOP_CLASSPATH=</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"></span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"># The maximum amount of heap to use, in MB. Default is 1000.</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"># export HADOOP_HEAPSIZE=2000</span></div>
</td>
</tr></tbody></table></div>
<p><br></p>
            </div>
                </div>