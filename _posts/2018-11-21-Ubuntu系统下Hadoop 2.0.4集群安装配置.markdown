---
layout:     post
title:      Ubuntu系统下Hadoop 2.0.4集群安装配置
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div><span style="font-family:Tahoma;font-size:14px;">Hadoop 2已经将HDFS和YARN分开管理，这样分开管理，可以是HDFS更方便地进行HA或Federation，实现HDFS的线性扩展（Scale out），从而保证HDFS集群的高可用性。从另一个方面们来说，HDFS可以作为一个通用的分布式存储系统，而为第三方的分布式计算框架提供方便，就像类似YARN的计算框架，其他的如，Spark等等。YARN就是MapReduce
 V2，将原来Hadoop 1.x中的JobTracker拆分为两部分：一部分是负责资源的管理（Resource Manager），另一部分负责任务的调度（Scheduler）。</span>
<div style="font-family:Tahoma;font-size:14px;"><br></div>
<div style="font-family:Tahoma;font-size:14px;"><span style="font-size:23px;"><strong>安装配置</strong><br><br></span></div>
<div style="font-family:Tahoma;font-size:14px;"><strong>1、目录结构 </strong></div>
<div style="font-family:Tahoma;font-size:14px;"><br></div>
<div style="font-family:Tahoma;font-size:14px;">下载hadoop-2.0.4软件包，解压缩后，可以看到如下目录结构：</div>
</div>
<div style="font-family:Tahoma;font-size:14px;"><pre><code class="language-plain">shirdrn@master:~/cloud/hadoop2/hadoop-2.0.4-alpha$ ls
bin  etc  include  lib  libexec  LICENSE.txt  logs  NOTICE.txt  README.txt  sbin  share</code></pre>
<div>
<div style="font-family:Tahoma;font-size:14px;">
<ul><li>etc目录</li></ul></div>
<div style="font-family:Tahoma;font-size:14px;">HDFS和YARN的配置文件，都存放在etc/hadoop目录下面，可以多各个文件进行配置：</div>
</div>
<pre><code class="language-plain">shirdrn@master:~/cloud/hadoop2/hadoop-2.0.4-alpha$ ls etc/hadoop/
capacity-scheduler.xml      hadoop-metrics.properties  httpfs-site.xml             ssl-client.xml.example
configuration.xsl           hadoop-policy.xml          log4j.properties            ssl-server.xml.example
container-executor.cfg      hdfs-site.xml              mapred-env.sh               yarn-env.sh
core-site.xml               httpfs-env.sh              mapred-queues.xml.template  yarn-site.xml
hadoop-env.sh               httpfs-log4j.properties    mapred-site.xml.template
hadoop-metrics2.properties  httpfs-signature.secret    slaves</code></pre>
<div>
<div style="font-family:Tahoma;font-size:14px;">
<ul><li>bin目录</li></ul></div>
<div style="font-family:Tahoma;font-size:14px;">bin目录下是用来管理HDFS、YARN的工具，如下所示：</div>
</div>
<pre><code class="language-plain">shirdrn@master:~/cloud/hadoop2/hadoop-2.0.4-alpha$ ls bin
container-executor  hadoop  hdfs  mapred  rcc  test-container-executor  yarn</code></pre>
<div><span style="font-family:Tahoma;font-size:14px;">下面，对Hadoop进行配置，Hadoop 2.x已经没有了mapred-site.xml配置文件，完全由yarn-site.xml取代。</span></div>
<div style="font-family:Tahoma;font-size:14px;"><br></div>
<div>
<div style="font-family:Tahoma;font-size:14px;"><strong>2、HDFS安装配置</strong></div>
<div style="font-family:Tahoma;font-size:14px;">
<div><br></div>
<div>配置<span style="color:#FF0000;">etc/hadoop/core-site.xml</span>文件内容：</div>
</div>
</div>
<pre><code class="language-html">&lt;?xml version="1.0" encoding="UTF-8"?&gt;
&lt;?xml-stylesheet type="text/xsl" href="configuration.xsl"?&gt;

&lt;configuration&gt;
     &lt;property&gt;
                &lt;name&gt;fs.defaultFS&lt;/name&gt;
                &lt;value&gt;hdfs://master:9000/&lt;/value&gt;
                &lt;description&gt;The name of the default file system. A URI whose scheme and authority determine the FileSystem implementation. The uri's scheme determines the config property (fs.SCHEME.impl) naming the FileSystem implementation class. The uri's authority is used to determine the host, port, etc. for a filesystem.&lt;/description&gt;
        &lt;/property&gt;
        &lt;property&gt;
                &lt;name&gt;dfs.replication&lt;/name&gt;
                &lt;value&gt;3&lt;/value&gt;
        &lt;/property&gt;
        &lt;property&gt;
                &lt;name&gt;hadoop.tmp.dir&lt;/name&gt;
                &lt;value&gt;/tmp/hadoop-${user.name}&lt;/value&gt;
                &lt;description&gt;&lt;/description&gt;
        &lt;/property&gt;
&lt;/configuration&gt;</code></pre>
<div><span style="font-family:Tahoma;font-size:14px;">配置</span><span style="color:#FF0000;font-family:Tahoma;font-size:14px;">etc/hadoop/hdfs-site.xml</span><span style="font-family:Tahoma;font-size:14px;">文件内容：</span></div>
<pre><code class="language-html">&lt;?xml version="1.0" encoding="UTF-8"?&gt;
&lt;?xml-stylesheet type="text/xsl" href="configuration.xsl"?&gt;

&lt;configuration&gt;
     &lt;property&gt;
                &lt;name&gt;dfs.namenode.name.dir&lt;/name&gt;
                &lt;value&gt;/home/shirdrn/storage/hadoop2/hdfs/name&lt;/value&gt;
                &lt;description&gt;Path on the local filesystem where the NameNode stores the namespace and transactions logs persistently.&lt;/description&gt;
        &lt;/property&gt;
        &lt;property&gt;
                &lt;name&gt;dfs.datanode.data.dir&lt;/name&gt;
                &lt;value&gt;/home/shirdrn/storage/hadoop2/hdfs/data1,/home/shirdrn/storage/hadoop2/hdfs/data2,/home/shirdrn/storage/hadoop2/hdfs/data3&lt;/value&gt;
                &lt;description&gt;Comma separated list of paths on the local filesystem of a DataNode where it should store its blocks.&lt;/description&gt;
        &lt;/property&gt;
     &lt;property&gt;
                &lt;name&gt;hadoop.tmp.dir&lt;/name&gt;
                &lt;value&gt;/home/shirdrn/storage/hadoop2/hdfs/tmp/hadoop-${user.name}&lt;/value&gt;
                &lt;description&gt;A base for other temporary directories.&lt;/description&gt;
        &lt;/property&gt;
&lt;/configuration&gt;</code></pre>
<div>
<div style="font-family:Tahoma;font-size:14px;"><strong><br>
3、YARN安装配置</strong></div>
<div style="font-family:Tahoma;font-size:14px;"><br></div>
<div style="font-family:Tahoma;font-size:14px;">配置<span style="color:#FF0000;">etc/hadoop/yarn-site.xml</span>文件内容：</div>
</div>
<pre><code class="language-html">&lt;?xml version="1.0"?&gt;

&lt;configuration&gt;
&lt;property&gt;
    &lt;name&gt;yarn.resourcemanager.resource-tracker.address&lt;/name&gt;
    &lt;value&gt;master:8031&lt;/value&gt;
    &lt;description&gt;host is the hostname of the resource manager and
    port is the port on which the NodeManagers contact the Resource Manager.
    &lt;/description&gt;
  &lt;/property&gt;

  &lt;property&gt;
    &lt;name&gt;yarn.resourcemanager.scheduler.address&lt;/name&gt;
    &lt;value&gt;master:8030&lt;/value&gt;
    &lt;description&gt;host is the hostname of the resourcemanager and port is the port
    on which the Applications in the cluster talk to the Resource Manager.
    &lt;/description&gt;
  &lt;/property&gt;

  &lt;property&gt;
    &lt;name&gt;yarn.resourcemanager.scheduler.class&lt;/name&gt;
    &lt;value&gt;org.apache.hadoop.yarn.server.resourcemanager.scheduler.capacity.CapacityScheduler&lt;/value&gt;
    &lt;description&gt;In case you do not want to use the default scheduler&lt;/description&gt;
  &lt;/property&gt;

  &lt;property&gt;
    &lt;name&gt;yarn.resourcemanager.address&lt;/name&gt;
    &lt;value&gt;master:8032&lt;/value&gt;
    &lt;description&gt;the host is the hostname of the ResourceManager and the port is the port on
    which the clients can talk to the Resource Manager. &lt;/description&gt;
  &lt;/property&gt;

  &lt;property&gt;
    &lt;name&gt;yarn.nodemanager.local-dirs&lt;/name&gt;
    &lt;value&gt;${hadoop.tmp.dir}/nodemanager/local&lt;/value&gt;
    &lt;description&gt;the local directories used by the nodemanager&lt;/description&gt;
  &lt;/property&gt;

  &lt;property&gt;
    &lt;name&gt;yarn.nodemanager.address&lt;/name&gt;
    &lt;value&gt;0.0.0.0:8034&lt;/value&gt;
    &lt;description&gt;the nodemanagers bind to this port&lt;/description&gt;
  &lt;/property&gt; 

  &lt;property&gt;
    &lt;name&gt;yarn.nodemanager.resource.memory-mb&lt;/name&gt;
    &lt;value&gt;10240&lt;/value&gt;
    &lt;description&gt;the amount of memory on the NodeManager in GB&lt;/description&gt;
  &lt;/property&gt;

  &lt;property&gt;
    &lt;name&gt;yarn.nodemanager.remote-app-log-dir&lt;/name&gt;
    &lt;value&gt;${hadoop.tmp.dir}/nodemanager/remote&lt;/value&gt;
    &lt;description&gt;directory on hdfs where the application logs are moved to &lt;/description&gt;
  &lt;/property&gt;

   &lt;property&gt;
    &lt;name&gt;yarn.nodemanager.log-dirs&lt;/name&gt;
    &lt;value&gt;${hadoop.tmp.dir}/nodemanager/logs&lt;/value&gt;
    &lt;description&gt;the directories used by Nodemanagers as log directories&lt;/description&gt;
  &lt;/property&gt;

  &lt;property&gt;
    &lt;name&gt;yarn.nodemanager.aux-services&lt;/name&gt;
    &lt;value&gt;mapreduce.shuffle&lt;/value&gt;
    &lt;description&gt;shuffle service that needs to be set for Map Reduce to run &lt;/description&gt;
  &lt;/property&gt;
&lt;/configuration&gt;</code></pre>
<div>
<div style="font-family:Tahoma;font-size:14px;"><br><br></div>
<div style="font-family:Tahoma;font-size:23px;"><strong>启动集群</strong></div>
<div style="font-family:Tahoma;font-size:14px;"><br></div>
<div style="font-family:Tahoma;font-size:14px;">
<ul><li><strong>启动HDFS集群</strong></li></ul></div>
<div style="font-family:Tahoma;font-size:14px;">首先，需要格式化HDFS，执行如下命令：</div>
</div>
<pre><code class="language-plain">shirdrn@master:~/cloud/hadoop2/hadoop-2.0.4-alpha$ bin/hdfs namenode -format</code></pre>
<div>
<div style="font-family:Tahoma;font-size:14px;">如果格式化正常，日志中不会出现异常信息，可以继续启动集群相关服务。</div>
<div style="font-family:Tahoma;font-size:14px;">启动HDFS集群，执行如下命令：</div>
</div>
<pre><code class="language-plain">shirdrn@master:~/cloud/hadoop2/hadoop-2.0.4-alpha$ sbin/start-dfs.sh</code></pre>
<div><span style="font-family:Tahoma;font-size:14px;">可以在master结点上看到如下几个进程：</span></div>
<pre><code class="language-plain">shirdrn@master:~/cloud/hadoop2/hadoop-2.0.4-alpha$ jps
17238 Jps
16845 NameNode
17128 SecondaryNameNode</code></pre>
<div><span style="font-family:Tahoma;font-size:14px;">而在slave结点上看到如下进程：</span></div>
<pre><code class="language-plain">shirdrn@slave01:~/programs$ jps
4865 Jps
4753 DataNode

shirdrn@slave02:~/programs$ jps
4867 DataNode
4971 Jps</code></pre>
<div>
<div style="font-family:Tahoma;font-size:14px;">
<ul><li><strong>启动YARN集群</strong></li></ul><div>如果配置完成以后，启动YARN集群非常容易，只需要执行几个脚本就可以。</div>
</div>
<div style="font-family:Tahoma;font-size:14px;">启动ResourceManager，执行如下命令：</div>
</div>
<pre><code class="language-plain">shirdrn@master:~/cloud/hadoop2/hadoop-2.0.4-alpha$ sbin/yarn-daemon.sh start resourcemanager</code></pre>
<div><span style="font-family:Tahoma;font-size:14px;">可以看到，多了一个ResourceManager进程：</span></div>
<pre><code class="language-plain">shirdrn@master:~/cloud/hadoop2/hadoop-2.0.4-alpha$ jps
16845 NameNode
17128 SecondaryNameNode
17490 Jps
17284 ResourceManager</code></pre>
<div><span style="font-family:Tahoma;font-size:14px;">然后，在slave结点上启动NodeManager进程，执行如下命令：</span></div>
<pre><code class="language-plain">shirdrn@slave01:~/programs/hadoop2/hadoop-2.0.4-alpha$ sbin/yarn-daemon.sh start nodemanager
shirdrn@slave02:~/programs/hadoop2/hadoop-2.0.4-alpha$ sbin/yarn-daemon.sh start nodemanager
</code></pre>
<div><span style="font-family:Tahoma;font-size:14px;">这时通过jps命令可以看到，各个slave结点上又多了一个NodeManager进程：</span></div>
<pre><code class="language-plain">shirdrn@slave01:~/programs/hadoop2/hadoop-2.0.4-alpha$ jps
5544 DataNode
5735 NodeManager
5904 Jps

shirdrn@slave02:~/programs/hadoop2/hadoop-2.0.4-alpha$ jps
5544 DataNode
5735 NodeManager
5904 Jps</code></pre>
<div><span style="font-family:Tahoma;font-size:14px;">或者，可以查看启动对应进程的日志来确定是否启动成功：</span></div>
<pre><code class="language-plain">shirdrn@slave01:~/programs/hadoop2/hadoop-2.0.4-alpha$  tail -100f /home/shirdrn/programs/hadoop2/hadoop-2.0.4-alpha/logs/yarn-shirdrn-resourcemanager-master.log

shirdrn@slave01:~/programs/hadoop2/hadoop-2.0.4-alpha$  tail -100f /home/shirdrn/programs/hadoop2/hadoop-2.0.4-alpha/logs/yarn-shirdrn-nodemanager-master.log</code></pre>
<div><span style="font-family:Tahoma;font-size:14px;">另外，启动整个Hadoop集群（包括HDFS和YARN），也可以直接执行下面一个脚本，启动全部相关进程，如下所示：</span></div>
<pre><code class="language-plain">shirdrn@master:~/cloud/hadoop2/hadoop-2.0.4-alpha$ sbin/start-all.sh
This script is Deprecated. Instead use start-dfs.sh and start-yarn.sh
Starting namenodes on [master]
master: starting namenode, logging to /home/shirdrn/programs/hadoop2/hadoop-2.0.4-alpha/logs/hadoop-shirdrn-namenode-master.out
slave02: starting datanode, logging to /home/shirdrn/programs/hadoop2/hadoop-2.0.4-alpha/logs/hadoop-shirdrn-datanode-slave02.out
slave01: starting datanode, logging to /home/shirdrn/programs/hadoop2/hadoop-2.0.4-alpha/logs/hadoop-shirdrn-datanode-slave01.out
Starting secondary namenodes [0.0.0.0]
0.0.0.0: starting secondarynamenode, logging to /home/shirdrn/programs/hadoop2/hadoop-2.0.4-alpha/logs/hadoop-shirdrn-secondarynamenode-master.out
starting yarn daemons
starting resourcemanager, logging to /home/shirdrn/programs/hadoop2/hadoop-2.0.4-alpha/logs/yarn-shirdrn-resourcemanager-master.out
slave01: starting nodemanager, logging to /home/shirdrn/programs/hadoop2/hadoop-2.0.4-alpha/logs/yarn-shirdrn-nodemanager-slave01.out
slave02: starting nodemanager, logging to /home/shirdrn/programs/hadoop2/hadoop-2.0.4-alpha/logs/yarn-shirdrn-nodemanager-slave02.out</code></pre>
<div>
<div style="font-family:Tahoma;font-size:23px;"><strong><br>
验证集群</strong></div>
<div style="font-family:Tahoma;font-size:14px;"><br></div>
<div style="font-family:Tahoma;font-size:14px;">最后，验证集群计算，执行Hadoop自带的examples，执行如下命令：</div>
</div>
<pre><code class="language-plain">shirdrn@master:~/cloud/hadoop2/hadoop-2.0.4-alpha$ bin/hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-2.0.4-alpha.jar randomwriter out</code></pre><br><div>
<div style="font-family:Tahoma;font-size:23px;"><strong><br>
参考链接</strong></div>
<div style="font-family:Tahoma;font-size:14px;">
<ul><li><a href="http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/SingleCluster.html" rel="nofollow">http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/SingleCluster.html</a></li><li><a href="http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/ClusterSetup.html" rel="nofollow">http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/ClusterSetup.html</a></li></ul></div>
</div>
<br></div>
            </div>
                </div>