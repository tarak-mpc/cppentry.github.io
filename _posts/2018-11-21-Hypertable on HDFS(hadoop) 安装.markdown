---
layout:     post
title:      Hypertable on HDFS(hadoop) 安装
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div class="titlepage" style="font-family:'Times New Roman';font-size:14px;line-height:19px;">
<h3 class="title" style="font-size:1.2em;line-height:1.5em;">
Hypertable on HDFS(hadoop) 安装</h3>
</div>
<p style="text-indent:2em;font-family:'Times New Roman';font-size:14px;line-height:19px;">
<a class="ulink" href="http://netkiller.github.io/storage/hdfs.html" rel="nofollow" style="color:rgb(233,101,14);">Hadoop - HDFS 安装指南</a></p>
<div class="procedure" title="过程 4.2. Hypertable on HDFS" style="font-family:'Times New Roman';font-size:14px;line-height:19px;">
<a style="color:rgb(233,101,14);text-decoration:underline;"></a>
<p class="title" style="text-indent:2em;">
<strong>过程 4.2. Hypertable on HDFS</strong></p>
<ol class="procedure" style="font-size:1em;line-height:1.4em;"><li class="step" title="步骤 1" style="font-size:1em;">
<p style="text-indent:0em;">
创建工作目录</p>
<pre class="screen" style="font-size:1em;">$ hadoop fs -mkdir /hypertable
$ hadoop fs -chmod 777 /hypertable
					</pre>
</li><li class="step" title="步骤 2" style="font-size:1em;">
<p style="text-indent:0em;">
安装 Java 运行环境</p>
<pre class="screen" style="font-size:1em;">yum install java-1.7.0-openjdk
yum localinstall http://ftp.cuhk.edu.hk/pub/packages/apache.org/hadoop/common/hadoop-1.1.2/hadoop-1.1.2-1.x86_64.rpm
					</pre>
</li><li class="step" title="步骤 3" style="font-size:1em;">
<p style="text-indent:0em;">
修改 jrun bug</p>
<pre class="screen" style="font-size:1em;">cp /opt/hypertable/current/bin/jrun /opt/hypertable/current/bin/jrun.old

vim /opt/hypertable/current/bin/jrun
#HT_JAR=`ls -1 /opt/hypertable/doug/current/lib/java/*.jar | grep "hypertable-[^-]*.jar" | awk 'BEGIN {FS="/"} {print $NF}'`
HT_JAR=`ls -1 /opt/hypertable/current/lib/java/*.jar | grep "hypertable-[^-]*.jar" | awk 'BEGIN {FS="/"} {print $NF}'`
					</pre>
<pre class="screen" style="font-size:1em;"> export JAVA_HOME=/usr
 export HADOOP_HOME=/usr
 export HYPERTABLE_HOME=/opt/hypertable/current
					</pre>
</li><li class="step" title="步骤 4" style="font-size:1em;">
<p style="text-indent:0em;">
hypertable.cfg</p>
<pre class="screen" style="font-size:1em;"># cat conf/hypertable.cfg
#
# hypertable.cfg
#

# HDFS Broker
#HdfsBroker.Hadoop.ConfDir=/etc/hadoop/conf
HdfsBroker.Hadoop.ConfDir=/etc/hadoop

# Ceph Broker
CephBroker.MonAddr=192.168.6.25:6789

# Local Broker
DfsBroker.Local.Root=fs/local

# DFS Broker - for clients
DfsBroker.Port=38030

# Hyperspace
Hyperspace.Replica.Host=localhost
Hyperspace.Replica.Port=38040
Hyperspace.Replica.Dir=hyperspace

# Hypertable.Master
#Hypertable.Master.Host=localhost
Hypertable.Master.Port=38050

# Hypertable.RangeServer
Hypertable.RangeServer.Port=38060

Hyperspace.KeepAlive.Interval=30000
Hyperspace.Lease.Interval=1000000
Hyperspace.GracePeriod=200000

# ThriftBroker
ThriftBroker.Port=38080
					</pre>
<p style="text-indent:0em;">
/etc/hadoop/hdfs-site.xml</p>
<pre class="screen" style="font-size:1em;">					
# cat /etc/hadoop/hdfs-site.xml
&lt;?xml version="1.0"?&gt;
&lt;?xml-stylesheet type="text/xsl" href="configuration.xsl"?&gt;

&lt;!-- Put site-specific property overrides in this file. --&gt;

&lt;configuration&gt;
    &lt;property&gt;
        &lt;name&gt;dfs.name.dir&lt;/name&gt;
        &lt;value&gt;/var/hadoop/name1&lt;/value&gt;
        &lt;description&gt;  &lt;/description&gt;
    &lt;/property&gt;
    &lt;property&gt;
        &lt;name&gt;dfs.data.dir&lt;/name&gt;
        &lt;value&gt;/var/hadoop/hdfs/data1&lt;/value&gt;
        &lt;description&gt; &lt;/description&gt;
    &lt;/property&gt;
    &lt;property&gt;
        &lt;name&gt;dfs.replication&lt;/name&gt;
        &lt;value&gt;2&lt;/value&gt;
    &lt;/property&gt;
&lt;/configuration&gt;
					
					</pre>
</li><li class="step" title="步骤 5" style="font-size:1em;">
<p style="text-indent:0em;">
启动 dfsbroker</p>
<pre class="screen" style="font-size:1em;"># /opt/hypertable/current/bin/set-hadoop-distro.sh cdh4
Hypertable successfully configured for Hadoop cdh4
					</pre>
<pre class="screen" style="font-size:1em;"># /opt/hypertable/current/bin/start-dfsbroker.sh hadoop
DFS broker: available file descriptors: 1024
Started DFS Broker (hadoop)
					</pre>
<p style="text-indent:0em;">
查看启动日志</p>
<pre class="screen" style="font-size:1em;"># tail -f /opt/hypertable/current/log/DfsBroker.hadoop.log
log4j:WARN No appenders could be found for logger (org.apache.hadoop.conf.Configuration).
log4j:WARN Please initialize the log4j system properly.
HdfsBroker.dfs.client.read.shortcircuit=false
HdfsBroker.dfs.replication=2
HdfsBroker.Server.fs.default.name=hdfs://namenode.example.com:9000
Apr 23, 2013 6:43:18 PM org.hypertable.AsyncComm.IOHandler DeliverEvent
INFO: [/192.168.6.25:53556 ; Tue Apr 23 18:43:18 HKT 2013] Connection Established
Apr 23, 2013 6:43:18 PM org.hypertable.DfsBroker.hadoop.ConnectionHandler handle
INFO: [/192.168.6.25:53556 ; Tue Apr 23 18:43:18 HKT 2013] Disconnect - COMM broken connection : Closing all open handles from /192.168.6.25:53556
Closed 0 input streams and 0 output streams for client connection /192.168.6.25:53556</pre>
</li></ol></div>
            </div>
                </div>