---
layout:     post
title:      hadoop spark环境搭建及idea scala maven集成开发spark任务
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h1><strong>1.项目背景</strong></h1>
<h1><span style="font-family:'KaiTi_GB2312';font-size:12px;"><span style="font-weight:normal;">公司目前已经部署了分布式文件存储和计算平台（</span><span style="font-weight:normal;">已经上线hadoop，spark，hbase等），业务数据已经以文件的形式存储在hdfs中，业务部门经常提出各种数据需求，因此需要基于spark计算平台开发各种计算任务。公司目前的分布式平台只有一套线上环境，为了避免在学习、测试spark开发的过程中对线上环境造成污染，经过运维同事的规划，提供一台配置较高的pc用于搭建单机（standalone模式）的hadoop
 + spark测试环境，希望通过该环境 了解和熟悉 hadoop,spark的运行模块，机制，流程以及常用操作命令;通过ide开发spark的经典例子WordCount向spark平台提交计算和查看结果。（个人qq：498676231欢迎同仁交流和指正下面博客内容中的问题和错误）</span></span></h1>
<h1>2.主要技术和工具清单 <span style="font-family:'KaiTi_GB2312';font-size:14px;">ref-software-list.1</span></h1>
<h1><span style="font-weight:normal;"><a href="https://www.centos.org/" rel="nofollow"><span style="font-family:'KaiTi_GB2312';font-size:12px;">os：Linux-centos-6.5</span></a></span></h1>
<h1><span style="font-weight:normal;"><a href="http://maven.apache.org/" rel="nofollow"><span style="font-family:'KaiTi_GB2312';font-size:12px;">maven：ver-3.1.0</span></a></span></h1>
<h1><span style="font-weight:normal;"><span style="font-family:'KaiTi_GB2312';font-size:12px;"><a href="https://www.java.com/zh_CN/" rel="nofollow">jvm：ver-jdk-1.7.0_7</a>9（不要使用1.8.x，会导致编译失败）</span></span></h1>
<h1><span style="font-weight:normal;"><span style="font-family:'KaiTi_GB2312';font-size:12px;"><a href="http://www.scala-lang.org/" rel="nofollow">scala：ver-2.10.5</a>  </span></span></h1>
<h1><span style="font-weight:normal;"><span style="font-family:'KaiTi_GB2312';font-size:12px;"><a href="http://hadoop.apache.org/" rel="nofollow">hadoop：ver-2.6.0-cdh5.12.0</a><span><a href="http://hadoop.apache.org/" rel="nofollow"></a></span></span></span></h1>
<h1><span style="font-weight:normal;"><a href="http://spark.apache.org/" rel="nofollow"><span style="font-family:'KaiTi_GB2312';font-size:12px;">spark：ver-1.6.0</span></a></span></h1>
<h1><span style="font-weight:normal;"><span style="font-family:'KaiTi_GB2312';font-size:12px;"><a href="https://www.jetbrains.com/idea/" rel="nofollow">idea：ver-2016.3</a></span></span></h1>
<h1><strong>3.hadoop + spark 环境搭建简单说明</strong></h1>
<h1><span style="font-family:'KaiTi_GB2312';font-size:12px;"><span style="font-weight:normal;">安装过程中的工具清单见 ref-software-list.1，maven,jdk,scala的安装过程比较简单，去各自官网下载对应版本的软件然后解压并配置环境变量即可正常使用了。下面</span><span style="font-weight:normal;">简单介绍一下hadoop，spark的standalone模式的安装（安装在单台主机，伪集群模式），安装方式一般有两种：</span></span></h1>
<div><span style="font-weight:normal;"><span style="font-family:'KaiTi_GB2312';font-size:12px;">1.去官网下载编译好的对应版本的软件，解压到指定目录下，配置环境变量和各自的配置文件，然后启动相应的服务即可。</span></span></div>
<div><span style="font-weight:normal;"><span style="font-family:'KaiTi_GB2312';font-size:12px;">2.通过linux自带的安装助手工具安装，以本次centos中的实际操作为例，通过</span></span></div>
<div><span style="font-weight:normal;"><span style="font-family:'KaiTi_GB2312';font-size:12px;">yum install softname1 softname2 ... -y #如果不是root用户sudo yum install softname1 softname2 ... -y</span></span></div>
<div><span style="font-weight:normal;"><span style="font-family:'KaiTi_GB2312';font-size:12px;">安装相关的平台组件，详细的帖子可以参考</span></span></div>
<div><span style="font-family:'KaiTi_GB2312';font-size:12px;">(1)<a href="http://abcve.com/use-cdh5-install-hadoop-cluster/" rel="nofollow">使用CDH5安装Hadoop集群</a><br></span></div>
<div><span style="font-family:'KaiTi_GB2312';font-size:12px;">(2)<a href="http://blog.javachen.com/2013/04/06/install-cloudera-cdh-by-yum" rel="nofollow">使用yum源安装CDH Hadoop集群</a></span></div>
<div><span style="font-family:'KaiTi_GB2312';font-size:12px;">(3)<a href="http://dataunion.org/10345.html" rel="nofollow">Spark安装和使用</a></span></div>
<div><span style="font-family:'KaiTi_GB2312';font-size:12px;">(4)<a href="http://spark.apache.org/docs/latest/monitoring.html#monitoring-and-instrumentation" rel="nofollow">Monitoring and Instrumentation</a></span></div>
<div><span style="font-family:'KaiTi_GB2312';font-size:12px;">(5)<a href="http://www.cnblogs.com/luogankun/p/3981645.html" rel="nofollow">Spark history Server产生背景</a></span></div>
<div><span style="font-family:'KaiTi_GB2312';font-size:12px;">这里补充几个问题点：</span></div>
<div><span style="font-family:'KaiTi_GB2312';font-size:12px;">q1.  yum install 命令安装软件时，命令行中没有显示指定软件版本， ref-software-list.1中我们要安装指定版本的hadoop和yarm该如何控制版本信息呢</span></div>
<div><span style="font-family:'KaiTi_GB2312';font-size:12px;">a1.  </span></div>
<div><span style="font-family:'KaiTi_GB2312';font-size:12px;">通过配置yum源指定hadoop的版本为cdh5, yum install 命令会根据配置的<span style="line-height:21px;">baseurl自动去适配hadoop的版本，因spark的安装依赖hadoop，hadoop安装完成（版本确定），安装spark时会根据已有的hadoop适配其自身的版本。</span></span></div>
<div><span style="line-height:21px;"><span style="font-family:'KaiTi_GB2312';font-size:12px;">本次安装hadoop yum源配置如下，在<span style="line-height:21px;">/etc/yum.repos.d/</span><span style="line-height:1.5;">cdh5.repo （如果没有对应的文件或者目录需要自己创建）添加如下内容：</span></span></span></div>
<div><span style="line-height:21px;"><span style="font-family:'KaiTi_GB2312';font-size:12px;"><span style="line-height:1.5;"></span></span></span>
<div style="line-height:21px;">
<div style="background-color:inherit;"><span style="font-family:'KaiTi_GB2312';font-size:12px;"># Packages for Cloudera's Distribution for Hadoop, Version 5, on RedHat    or CentOS 6 x86_64</span></div>
<div style="background-color:inherit;"><span style="font-family:'KaiTi_GB2312';font-size:12px;">name=Cloudera's Distribution for Hadoop, Version 5</span></div>
<div style="background-color:inherit;"><span style="font-family:'KaiTi_GB2312';font-size:12px;">baseurl=<a href="https://archive.cloudera.com/cdh5/redhat/6/x86_64/cdh/5/" rel="nofollow" style="background-color:inherit;">https://archive.cloudera.com/cdh5/redhat/6/x86_64/cdh/5/</a></span></div>
<div style="background-color:inherit;"><span style="font-family:'KaiTi_GB2312';font-size:12px;">gpgkey =<a href="https://archive.cloudera.com/cdh5/redhat/6/x86_64/cdh/RPM-GPG-KEY-cloudera" rel="nofollow" style="background-color:inherit;">https://archive.cloudera.com/cdh5/redhat/6/x86_64/cdh/RPM-GPG-KEY-cloudera</a>    </span></div>
<div style="background-color:inherit;"><span style="font-family:'KaiTi_GB2312';font-size:12px;">gpgcheck = 1</span></div>
<div style="background-color:inherit;"><span style="font-family:'KaiTi_GB2312';font-size:12px;"><br></span></div>
<div style="background-color:inherit;"><span style="font-family:'KaiTi_GB2312';font-size:12px;">q2.  本次单机模式安装的组件</span></div>
<div><span style="font-family:'KaiTi_GB2312';font-size:12px;">a2.  </span></div>
<div><span style="font-family:'KaiTi_GB2312';font-size:12px;">hadoop-hdfs hadoop-client hadoop-hdfs-namenode hadoop-hdfs-datanode  spark-master spark-worker spark-history-server</span></div>
<div><span style="font-family:'KaiTi_GB2312';font-size:12px;">安装命令如下：</span></div>
<div><span style="font-family:'KaiTi_GB2312';font-size:12px;">hadoop-hdfs 相关安装命令</span></div>
<div><span style="font-family:'KaiTi_GB2312';font-size:12px;">yum install hadoop hadoop-hdfs hadoop-client hadoop-doc hadoop-debuginfo hadoop-hdfs-namenode<br></span></div>
<div><span style="font-family:'KaiTi_GB2312';font-size:12px;">yum install hadoop-hdfs-datanode -y<br></span></div>
<div><span style="font-family:'KaiTi_GB2312';font-size:12px;">spark 相关安装命令</span></div>
</div>
<span style="font-family:'KaiTi_GB2312';font-size:12px;">yum install spark-core spark-master spark-worker spark-history-server -y<br></span></div>
<div><span style="line-height:21px;"><span style="line-height:1.5;"><span style="font-family:'KaiTi_GB2312';font-size:12px;"><br></span></span></span></div>
<p><span style="line-height:21px;"><span style="line-height:1.5;"><span style="font-family:'KaiTi_GB2312';font-size:12px;">q3.  需要修改的相关配置文件</span></span></span></p>
<p><span style="line-height:21px;"><span style="line-height:1.5;"><span style="font-family:'KaiTi_GB2312';font-size:12px;">a3.  </span></span></span></p>
<p><span style="line-height:21px;"><span style="line-height:1.5;"><span style="font-family:'KaiTi_GB2312';font-size:12px;"><span></span>/etc/hosts 中追加  172.168.xx.xx <span></span>cdh1     #<span style="line-height:21px;">172.168.xx.xx是当前安装主机的ip，cdh1是hostname产生的网络名称</span></span></span></span></p>
<p><span style="line-height:21px;"><span style="line-height:1.5;"><span style="line-height:21px;"><span style="font-family:'KaiTi_GB2312';font-size:12px;"><span></span>/etc/sysconfig/network 中内容如下：</span></span></span></span></p>
<p><span style="line-height:21px;"><span style="line-height:1.5;"><span style="line-height:21px;"><span style="font-family:'KaiTi_GB2312';font-size:12px;"><span></span>NETWORKING=YES<br></span></span></span></span></p>
<p><span style="line-height:21px;"><span style="line-height:1.5;"><span style="line-height:21px;"><span style="font-family:'KaiTi_GB2312';font-size:12px;"><span></span>HOSTNAME=cdh1</span></span></span></span></p>
<p><span style="line-height:21px;"><span style="line-height:1.5;"><span style="line-height:21px;"><span style="font-family:'KaiTi_GB2312';font-size:12px;"><span></span>GATEWAY=172.168.xx.xx</span></span></span></span></p>
<p><span style="line-height:21px;"><span style="line-height:1.5;"><span style="line-height:21px;"><span style="font-family:'KaiTi_GB2312';font-size:12px;"><span></span>/etc/hadoop/conf 目录下hdfs相关配置文件如下：</span></span></span></span></p>
<p><span style="line-height:21px;"><span style="line-height:1.5;"><span style="line-height:21px;"><span style="font-family:'KaiTi_GB2312';font-size:12px;"><span></span>core-site.xml</span></span></span></span></p>
<pre><code class="language-html"><span style="font-family:'KaiTi_GB2312';font-size:12px;">&lt;?xml version="1.0"?&gt;
&lt;?xml-stylesheet type="text/xsl" href="configuration.xsl"?&gt;
&lt;configuration&gt;
    &lt;property&gt;
	&lt;name&gt;fs.defaultFS&lt;/name&gt;
	&lt;value&gt;hdfs://cdh1:8020&lt;/value&gt;
    &lt;/property&gt;
    &lt;!--
    &lt;property&gt;
   	&lt;name&gt;fs.default.name&lt;/name&gt;
    	&lt;value&gt;hdfs://</span><span style="font-family:'KaiTi_GB2312';">cdh1</span><span style="font-family:'KaiTi_GB2312';">:8020/&lt;/value&gt;</span><span style="font-family:'KaiTi_GB2312';font-size:12px;">
    &lt;/property&gt;
    --&gt;
&lt;/configuration&gt;</span></code></pre>
<p></p>
<p style="text-align:left;"><span style="line-height:21px;"><span style="line-height:1.5;"><span style="line-height:21px;"><span style="font-family:'KaiTi_GB2312';"><span></span>hdfs-site.xml</span></span></span></span></p>
<pre><code class="language-html"><span style="font-family:'KaiTi_GB2312';font-size:12px;">&lt;?xml version="1.0"?&gt;
&lt;?xml-stylesheet type="text/xsl" href="configuration.xsl"?&gt;
&lt;configuration&gt;
  &lt;property&gt;
     &lt;name&gt;dfs.namenode.name.dir&lt;/name&gt;
     &lt;value&gt;file:///var/lib/hadoop-hdfs/cache/hdfs/dfs/name&lt;/value&gt;
  &lt;/property&gt;
  &lt;property&gt;
     &lt;name&gt;dfs.datanode.data.dir&lt;/name&gt;
     &lt;value&gt;file:///var/lib/hadoop-hdfs/cache/hdfs/dfs/data&lt;/value&gt;
  &lt;/property&gt;
  &lt;property&gt;
     &lt;name&gt;dfs.permissions.superusergroup&lt;/name&gt;
     &lt;value&gt;hadoop&lt;/value&gt;
  &lt;/property&gt;
  &lt;property&gt;
	&lt;name&gt;dfs.replication&lt;/name&gt;
	&lt;value&gt;1&lt;/value&gt;
  &lt;/property&gt;
&lt;/configuration&gt;</span></code></pre>
<p></p>
<p><span style="line-height:21px;"><span style="line-height:1.5;"><span style="line-height:21px;"><span style="font-family:'KaiTi_GB2312';font-size:12px;"><span></span>mapred-site.xml</span></span></span></span></p>
<pre><code class="language-html"><span style="font-family:'KaiTi_GB2312';font-size:12px;">&lt;?xml version="1.0"?&gt;
&lt;?xml-stylesheet type="text/xsl" href="configuration.xsl"?&gt;
&lt;configuration&gt;
    &lt;property&gt;
	&lt;name&gt;mapred.job.tracker&lt;/name&gt;
	&lt;value&gt;cdh1:9001&lt;/value&gt;
    &lt;/property&gt;
&lt;/configuration&gt;</span></code></pre><span style="font-family:'KaiTi_GB2312';font-size:12px;"><br><span></span>/etc/spark/conf 目录下相关配置文件</span>
<p><span style="line-height:21px;"><span style="line-height:1.5;"><span style="line-height:21px;"><span style="font-family:'KaiTi_GB2312';font-size:12px;"><span></span>spark-defaults.conf</span></span></span></span></p>
<pre><code class="language-html"><span style="font-family:'KaiTi_GB2312';font-size:12px;">spark.master                     spark://cdh1:7077
spark.eventLog.enabled           true
spark.eventLog.dir               /user/spark/applicationHistory
spark.history.provider		   org.apache.spark.deploy.history.FsHistoryProvider
spark.yarn.historyServer.address	http://cdh1:18080</span></code></pre>
<p><span style="font-family:'KaiTi_GB2312';font-size:12px;">spark-env.sh中添加修改：</span></p>
<pre><code class="language-html"><span style="font-family:'KaiTi_GB2312';font-size:12px;">export SPARK_HISTORY_OPTS="-Dspark.history.ui.port=18080 -Dspark.history.retainedApplications=10 -Dspark.history.fs.logDirectory=/user/spark/applicationHistory"
</span></code></pre><span style="font-family:'KaiTi_GB2312';font-size:12px;"><br>
/etc/profile中添加spark的环境变量</span><pre><code class="language-html"><span style="font-family:'KaiTi_GB2312';font-size:12px;">export SPARK_HOME=/usr/lib/spark</span></code></pre><span style="font-family:'KaiTi_GB2312';font-size:12px;"><br>
q4.  启动相关的组件服务</span>
<p></p>
<p><span style="line-height:21px;"><span style="line-height:1.5;"><span style="line-height:21px;"><span style="font-family:'KaiTi_GB2312';font-size:12px;">a4.  </span></span></span></span></p>
<p><span style="line-height:21px;"><span style="line-height:1.5;"><span style="line-height:21px;"><span style="font-family:'KaiTi_GB2312';font-size:12px;">首先启动hdfs的namenode服务，（第一次）启动前先格式化namenode</span></span></span></span></p>
<p><span style="line-height:21px;"><span style="line-height:1.5;"><span style="line-height:21px;"><span style="font-family:'KaiTi_GB2312';font-size:12px;">hadoop namenode -format</span></span></span></span></p>
<p><span style="line-height:21px;"><span style="line-height:1.5;"><span style="line-height:21px;"><span style="font-family:'KaiTi_GB2312';font-size:12px;">然后执行启动hdfs namenode的命令</span></span></span></span></p>
<p><span style="font-family:'KaiTi_GB2312';line-height:21px;">/etc/init.d/hadoop-hdfs-namenode restart </span></p>
<p><span style="font-family:'KaiTi_GB2312';line-height:21px;">#抛出/var/lib/hadoop-hdfs/cache/hdfs/dfs/name/current/VERSION 权限不够的问题</span></p>
<p><span style="font-family:'KaiTi_GB2312';line-height:21px;">#因为hadoop安装完成之后，hdfs对文件的处理是hdfs用户，该用户没有</span><span style="font-family:'KaiTi_GB2312';line-height:21px;">/var/lib/hadoop-hdfs目录的操作权限</span></p>
<p><span style="font-family:'KaiTi_GB2312';line-height:21px;">执行如下命令给hdfs分配</span><span style="font-family:'KaiTi_GB2312';line-height:21px;">/var/lib/hadoop-hdfs目录的操作权限</span></p>
<p><span style="font-family:'KaiTi_GB2312';line-height:21px;">chown hdfs.hdfs  </span><span style="font-family:'KaiTi_GB2312';line-height:21px;">/var/lib/hadoop-hdfs -R</span></p>
<p><span style="line-height:21px;"><span style="line-height:1.5;"><span style="line-height:21px;"><span><span style="line-height:21px;"><span style="line-height:21px;"><span style="line-height:21px;"><span><span style="line-height:21px;"><span style="font-family:'KaiTi_GB2312';"><span></span><span></span>依次执行以下命令：</span></span></span></span></span></span></span></span></span></span></p>
<p><span style="line-height:21px;"><span style="line-height:1.5;"><span style="line-height:21px;"><span><span style="line-height:21px;"><span style="line-height:21px;"><span style="line-height:21px;"><span><span style="line-height:21px;"><span style="line-height:21px;"><span style="font-family:'KaiTi_GB2312';font-size:12px;"><span></span><span></span>/etc/init.d/hadoop-hdfs-namenode
 restart </span></span></span></span></span></span></span></span></span></span></span></p>
<p><span style="line-height:21px;"><span style="line-height:1.5;"><span style="line-height:21px;"><span><span style="line-height:21px;"><span style="line-height:21px;"><span style="line-height:21px;"><span><span style="line-height:21px;"><span style="line-height:21px;"><span><span style="line-height:21px;"><span style="font-family:'KaiTi_GB2312';font-size:12px;"><span></span>/etc/init.d/hadoop-hdfs-datanode
 restart</span></span></span></span></span></span></span></span></span></span></span></span></span></p>
<p><span style="line-height:21px;"><span style="line-height:1.5;"><span style="line-height:21px;"><span><span style="line-height:21px;"><span style="line-height:21px;"><span style="line-height:21px;"><span><span style="line-height:21px;"><span style="line-height:21px;"><span><span style="line-height:21px;"><span><span style="line-height:21px;"><span style="font-family:'KaiTi_GB2312';font-size:12px;"><span></span>/etc/init.d/spark-master
 restart</span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></p>
<p><span style="line-height:21px;"><span style="line-height:1.5;"><span style="line-height:21px;"><span><span style="line-height:21px;"><span style="line-height:21px;"><span style="line-height:21px;"><span><span style="line-height:21px;"><span style="line-height:21px;"><span><span style="line-height:21px;"><span><span style="line-height:21px;"><span><span style="line-height:21px;"><span style="font-family:'KaiTi_GB2312';font-size:12px;"><span></span>/etc/init.d/spark-worker
 restart</span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></p>
<p><span style="line-height:21px;"><span style="line-height:1.5;"><span style="line-height:21px;"><span><span style="line-height:21px;"><span style="line-height:21px;"><span style="line-height:21px;"><span><span style="line-height:21px;"><span style="line-height:21px;"><span><span style="line-height:21px;"><span><span style="line-height:21px;"><span><span style="line-height:21px;"><span><span style="line-height:21px;"><span style="font-family:'KaiTi_GB2312';font-size:12px;"><span></span>/etc/init.d/spark-history-server
 restart</span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></p>
<p><span style="line-height:21px;"><span style="line-height:1.5;"><span style="line-height:21px;"><span><span style="line-height:21px;"><span style="line-height:21px;"><span style="line-height:21px;"><span><span style="line-height:21px;"><span style="line-height:21px;"><span><span style="line-height:21px;"><span><span style="line-height:21px;"><span><span style="line-height:21px;"><span style="font-family:'KaiTi_GB2312';font-size:12px;"><span></span><span></span>所有服务启动之后，执行jps，查看所有相关的已经正常启动的服务</span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></p>
<p><span style="line-height:21px;"><span style="line-height:1.5;"><span style="line-height:21px;"><span><span style="line-height:21px;"><span style="line-height:21px;"><span style="line-height:21px;"><span><span style="line-height:21px;"><span style="line-height:21px;"><span><span style="line-height:21px;"><span><span style="line-height:21px;"><span><span style="line-height:21px;"><span style="font-family:'KaiTi_GB2312';font-size:12px;"><img src="" alt=""><img src="" alt=""><img src="https://img-blog.csdn.net/20170807201515859" alt=""><br></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></p>
<p><span style="line-height:21px;"><span style="font-family:'KaiTi_GB2312';font-size:12px;"><span></span>运行一个spark自带的例子检验一切是ok的。</span></span></p>
<p><span style="line-height:21px;"><span style="font-family:'KaiTi_GB2312';font-size:12px;"><span></span>$SPARK_HOME/bin/run-example SparkPi 10，输出如下（无异常抛出）：</span></span></p>
<p><img src="https://img-blog.csdn.net/20170807201711402" alt=""><span style="line-height:21px;"><span style="line-height:1.5;"><span style="line-height:21px;"><span><span style="line-height:21px;"><span style="line-height:21px;"><span style="line-height:21px;"><span><span style="line-height:21px;"><span style="line-height:21px;"><span><span style="line-height:21px;"><span><span style="line-height:21px;"><span><span style="line-height:21px;"><span><span style="font-family:'KaiTi_GB2312';font-size:12px;"><img src="" alt=""></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></p>
<p><span style="line-height:21px;"><span style="font-family:'KaiTi_GB2312';font-size:12px;">到目前为止hadoop和spark的安装基本完成！！！</span></span></p>
<h1>4.scala + spark 项目集成与打包</h1>
<h1><span style="font-family:'KaiTi_GB2312';font-size:12px;"><span style="font-weight:normal;">hadoop，spark搭建完成之后，接下来就是基于spark（目前支持java,scala,python,r等开发语言</span><span style="font-weight:normal;">）写一个"hello world"了。选择scala作为开发语言（spark是基于scala开发的开源框架，先前版本似乎是不支持java的，最近两年发布的版本已经提供了相关的java
 api开发接口）。使用scala开发spark 官网推荐的标配是<a href="https://www.jetbrains.com/idea/" rel="nofollow">idea</a>+<a href="http://www.scala-sbt.org/download.html" rel="nofollow">sbt</a>（个人之前一直习惯使用eclipse + maven开发，做这个例子的时候，开始使用的ide是<a href="http://scala-ide.org/" rel="nofollow">scala-IDE</a>，就是普通eclipse中安装了一个开发scala的插件。在本地安装完scala之后配置scala-IDE的全局scala-library,工程里面的代码通过按住ctrl链接过去的源码是乱的，另外工程中常报一些奇怪的错误，其原因要么是个人配置有问题要么是eclipse
 和 scala-plugin的兼容性不好，于是放弃了</span><span style="font-weight:normal;">scala-IDE；另外maven是良好支持scala相关的包资源管理的，因此个人还是选择了idea + maven 作为开发环境</span><span style="font-weight:normal;">），示例选择了idea + maven，idea里不直接支持scala的开发，也需要安装插件。</span></span></h1>
<h1><span style="font-family:'KaiTi_GB2312';font-size:12px;font-weight:normal;">idea + scala + maven 环境的配置参考 </span><a href="http://www.jianshu.com/p/ecc6eb298b8f" rel="nofollow" style="font-family:'KaiTi_GB2312';font-size:12px;"><span style="color:rgb(255,0,0);">利用IntelliJ
 IDEA与Maven开始你的Scala之旅</span></a></h1>
<h1><span style="font-family:'KaiTi_GB2312';font-size:12px;">两点容易出问题的地方：</span></h1>
<div><span style="font-family:'KaiTi_GB2312';font-size:12px;">a1. 配置scala自身的sdk的时候按照 Configure -&gt;Projects Default -&gt;Project Structure -&gt;Global Libraries -&gt;+ -&gt;Scala SDK 没有弹框反应，这应该是版本兼容性的问题（我之前的idea是ver-2017.2,后来改成ver-2016.3）</span></div>
<div><span style="font-family:'KaiTi_GB2312';font-size:12px;"><br></span></div>
<div><span style="font-family:'KaiTi_GB2312';font-size:12px;">a2. 新建基于maven的scala工程时不要勾选 Create from archetype 然后使用 scala-archetype-simple框架自动生成工程，idea会自动导入scala的相关类库和已经指定的类库冲突；按照文档要求生成空的maven工程，删除自动产生的目录在main目录下手工创建scala,test，resources等目录,将scala框架添加到这个工程（在工程上面右键选择
 Add Framework Support...），将scala,test，resources 标记为资源的根目录在它们各自上面右键Mark Directory as -&gt; Sources Root。</span></div>
<div><span style="font-family:'KaiTi_GB2312';font-size:12px;">到此一个新的基于maven的scala空工程已经建立完成，接下来可以往这个工程中新建类，添加相关依赖和打包执行。为了稍微增加一点演示的复杂度，从github上找了一个演示例子<span style="font-family:'KaiTi_GB2312';"><a href="https://github.com/databricks/learning-spark" rel="nofollow">learning-spark</a>，</span></span><span style="font-family:'KaiTi_GB2312';">工程中同时包含java和scala的代码，希望能同时编译java和scala代码并将相关的依赖jar打包并最终生成一个可执行的jar（fat
 编译模式），另外还有thin 编译模式（执行jar和依赖jar分开，依赖jar放在服务器端，每次只需要在本地编译执行jar并上传到服务器，避免频繁传输过大的jar造成网络负载重），参考 </span><span style="font-family:'KaiTi_GB2312';"><strong><a href="https://wenku.baidu.com/view/66546b62f12d2af90342e612.html?re=view" rel="nofollow"><span style="color:#ff0000;">Scala
 + Spark +Maven之Helloworld</span></a></strong></span></div>
<div><span style="font-family:'KaiTi_GB2312';font-size:12px;">将该工程down下来之后，找到mini-complete-example目录(该目录下包含一个精简版的实例)，删除除src和pom.xml以外的所有文件，</span></div>
<div><span style="font-family:'KaiTi_GB2312';font-size:12px;"><img src="https://img-blog.csdn.net/20170808105146372" alt=""><br></span></div>
<div><span style="font-family:'KaiTi_GB2312';font-size:12px;"><br></span></div>
<div><span style="font-family:'KaiTi_GB2312';">将该工程导入idea中，修改pom文件内容<strong>（特别注意网上有些讲解scala + spark + maven工程打包的时候在pom.xml文件的dependency中引入了scala-library,scala-compiler等包，而客户端开发环境本身就安装了scala，自然包含了这些包，因此这些依赖引用都要移除掉，本工程中只需要引入spark-core这个依赖包）</strong>，完整的pom.xml文件如下：</span></div>
<div><pre><code class="language-html"><span style="font-family:'KaiTi_GB2312';font-size:12px;">&lt;project&gt;
  &lt;groupId&gt;com.oreilly.learningsparkexamples.mini&lt;/groupId&gt;
  &lt;artifactId&gt;learning-spark-mini-example&lt;/artifactId&gt;
  &lt;modelVersion&gt;4.0.0&lt;/modelVersion&gt;
  &lt;name&gt;example&lt;/name&gt;
  &lt;packaging&gt;jar&lt;/packaging&gt;
  &lt;version&gt;0.0.1&lt;/version&gt;
	&lt;repositories&gt;
		&lt;repository&gt;
			&lt;id&gt;jboss&lt;/id&gt;
			&lt;name&gt;Maven of jboss.org&lt;/name&gt;
			&lt;url&gt;http://repository.jboss.org/nexus/content/groups/public/&lt;/url&gt;
			&lt;releases&gt;
				&lt;enabled&gt;true&lt;/enabled&gt;
			&lt;/releases&gt;
			&lt;snapshots&gt;
				&lt;enabled&gt;true&lt;/enabled&gt;
			&lt;/snapshots&gt;
			&lt;layout&gt;default&lt;/layout&gt;
		&lt;/repository&gt;

		&lt;repository&gt;
			&lt;id&gt;oschina&lt;/id&gt;
			&lt;name&gt;Maven of oschina.net&lt;/name&gt;
			&lt;url&gt;http://maven.oschina.net/content/groups/public/&lt;/url&gt;
			&lt;releases&gt;
				&lt;enabled&gt;true&lt;/enabled&gt;
			&lt;/releases&gt;
			&lt;snapshots&gt;
				&lt;enabled&gt;true&lt;/enabled&gt;
			&lt;/snapshots&gt;
			&lt;layout&gt;default&lt;/layout&gt;
		&lt;/repository&gt;

		&lt;repository&gt;
			&lt;id&gt;spring-libs-snapshot&lt;/id&gt;
			&lt;url&gt;http://repo.spring.io/libs-snapshot&lt;/url&gt;
			&lt;snapshots&gt;
				&lt;enabled&gt;true&lt;/enabled&gt;
			&lt;/snapshots&gt;
		&lt;/repository&gt;

		&lt;repository&gt;
			&lt;id&gt;Akka repository&lt;/id&gt;
			&lt;url&gt;http://repo.akka.io/releases&lt;/url&gt;
		&lt;/repository&gt;
		&lt;repository&gt;
			&lt;id&gt;scala-tools&lt;/id&gt;
			&lt;url&gt;https://oss.sonatype.org/content/groups/scala-tools&lt;/url&gt;
		&lt;/repository&gt;
		&lt;repository&gt;
			&lt;id&gt;apache&lt;/id&gt;
			&lt;url&gt;https://repository.apache.org/content/repositories/releases&lt;/url&gt;
		&lt;/repository&gt;
		&lt;repository&gt;
			&lt;id&gt;twitter&lt;/id&gt;
			&lt;url&gt;http://maven.twttr.com/&lt;/url&gt;
		&lt;/repository&gt;
		&lt;repository&gt;
			&lt;id&gt;central2&lt;/id&gt;
			&lt;url&gt;http://central.maven.org/maven2/&lt;/url&gt;
		&lt;/repository&gt;
		&lt;repository&gt;
			&lt;id&gt;central&lt;/id&gt;
			&lt;name&gt;Maven Repository Switchboard&lt;/name&gt;
			&lt;layout&gt;default&lt;/layout&gt;
			&lt;url&gt;http://repo2.maven.org/maven2&lt;/url&gt;
			&lt;snapshots&gt;
				&lt;enabled&gt;false&lt;/enabled&gt;
			&lt;/snapshots&gt;
		&lt;/repository&gt;
	&lt;/repositories&gt;

	&lt;properties&gt;
		&lt;project.build.sourceEncoding&gt;UTF-8&lt;/project.build.sourceEncoding&gt;
		&lt;spark.version&gt;1.6.3&lt;/spark.version&gt;
		&lt;scala.version&gt;2.10&lt;/scala.version&gt;
		&lt;hadoop.version&gt;2.6.0&lt;/hadoop.version&gt;
	&lt;/properties&gt;

	&lt;dependencies&gt;
		&lt;dependency&gt;
			&lt;groupId&gt;org.apache.spark&lt;/groupId&gt;
			&lt;artifactId&gt;spark-core_2.10&lt;/artifactId&gt;
			&lt;version&gt;1.6.3&lt;/version&gt;
		&lt;/dependency&gt;
	&lt;/dependencies&gt;

	&lt;build&gt;
		&lt;sourceDirectory&gt;src/main&lt;/sourceDirectory&gt;
			&lt;resources&gt;
				&lt;resource&gt;
					&lt;filtering&gt;true&lt;/filtering&gt;
					&lt;directory&gt;src/main/java&lt;/directory&gt;
					&lt;includes&gt;
						&lt;include&gt;*.*&lt;/include&gt;
					&lt;/includes&gt;
				&lt;/resource&gt;
				&lt;resource&gt;
					&lt;filtering&gt;true&lt;/filtering&gt;
					&lt;directory&gt;src/main/scala&lt;/directory&gt;
					&lt;includes&gt;
						&lt;include&gt;*.*&lt;/include&gt;
					&lt;/includes&gt;
				&lt;/resource&gt;
			&lt;/resources&gt;
		&lt;plugins&gt;
			&lt;plugin&gt;
				&lt;!-- 这是个编译java代码的 --&gt;
				&lt;groupId&gt;org.apache.maven.plugins&lt;/groupId&gt;
				&lt;artifactId&gt;maven-compiler-plugin&lt;/artifactId&gt;
				&lt;version&gt;3.2&lt;/version&gt;
				&lt;configuration&gt;
					&lt;source&gt;1.7&lt;/source&gt;
					&lt;target&gt;1.7&lt;/target&gt;
					&lt;encoding&gt;UTF-8&lt;/encoding&gt;
				&lt;/configuration&gt;
				&lt;executions&gt;
					&lt;execution&gt;
						&lt;phase&gt;compile&lt;/phase&gt;
						&lt;goals&gt;
							&lt;goal&gt;compile&lt;/goal&gt;
						&lt;/goals&gt;
					&lt;/execution&gt;
				&lt;/executions&gt;
			&lt;/plugin&gt;
			&lt;plugin&gt;
				&lt;!-- 这是个编译scala代码的 --&gt;
				&lt;groupId&gt;net.alchim31.maven&lt;/groupId&gt;
				&lt;artifactId&gt;scala-maven-plugin&lt;/artifactId&gt;
				&lt;version&gt;3.2.1&lt;/version&gt;
				&lt;executions&gt;
					&lt;execution&gt;
						&lt;id&gt;scala-compile-first&lt;/id&gt;
						&lt;phase&gt;process-resources&lt;/phase&gt;
						&lt;goals&gt;
							&lt;goal&gt;add-source&lt;/goal&gt;
							&lt;goal&gt;compile&lt;/goal&gt;
						&lt;/goals&gt;
					&lt;/execution&gt;
				&lt;/executions&gt;
			&lt;/plugin&gt;

			&lt;!--
			&lt;plugin&gt;
				&lt;groupId&gt;org.apache.maven.plugins&lt;/groupId&gt;
				&lt;artifactId&gt;maven-shade-plugin&lt;/artifactId&gt;
				&lt;version&gt;3.0.0&lt;/version&gt;
				&lt;configuration&gt;
				&lt;/configuration&gt;
				&lt;executions&gt;
					&lt;execution&gt;
						&lt;phase&gt;package&lt;/phase&gt;
						&lt;goals&gt;
							&lt;goal&gt;shade&lt;/goal&gt;
						&lt;/goals&gt;
					&lt;/execution&gt;
				&lt;/executions&gt;
			&lt;/plugin&gt;
			--&gt;
			&lt;plugin&gt;
				&lt;groupId&gt;org.apache.maven.plugins&lt;/groupId&gt;
				&lt;artifactId&gt;maven-shade-plugin&lt;/artifactId&gt;
				&lt;version&gt;1.4&lt;/version&gt;
				&lt;executions&gt;
					&lt;execution&gt;
						&lt;phase&gt;package&lt;/phase&gt;
						&lt;goals&gt;
							&lt;goal&gt;shade&lt;/goal&gt;
						&lt;/goals&gt;
						&lt;configuration&gt;
							&lt;filters&gt;
								&lt;filter&gt;
									&lt;artifact&gt;*:*&lt;/artifact&gt;
									&lt;excludes&gt;
										&lt;exclude&gt;META-INF/*.SF&lt;/exclude&gt;
										&lt;exclude&gt;META-INF/*.DSA&lt;/exclude&gt;
										&lt;exclude&gt;META-INF/*.RSA&lt;/exclude&gt;
									&lt;/excludes&gt;
								&lt;/filter&gt;
							&lt;/filters&gt;
							&lt;transformers&gt;
								&lt;transformer
										implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer"&gt;
									&lt;mainClass&gt;com.oreilly.learningsparkexamples.mini.scala.WordCount&lt;/mainClass&gt;
								&lt;/transformer&gt;
								&lt;!--
								&lt;transformer
										implementation="org.apache.maven.plugins.shade.resource.AppendingTransformer"&gt;
									&lt;resource&gt;META-INF/spring.handlers&lt;/resource&gt;
								&lt;/transformer&gt;
								&lt;transformer
										implementation="org.apache.maven.plugins.shade.resource.AppendingTransformer"&gt;
									&lt;resource&gt;META-INF/spring.schemas&lt;/resource&gt;
								&lt;/transformer&gt;
								--&gt;
							&lt;/transformers&gt;
						&lt;/configuration&gt;
					&lt;/execution&gt;
				&lt;/executions&gt;
			&lt;/plugin&gt;

		&lt;/plugins&gt;
	&lt;/build&gt;
&lt;/project&gt;
</span></code></pre><span style="font-family:'KaiTi_GB2312';font-size:12px;"><br>
在该工程的根目录下执行</span></div>
<div><span style="font-family:'KaiTi_GB2312';font-size:12px;"><span style="line-height:21px;">mvn clean scala:compile compile package -DskipTests</span><br></span></div>
<div><span style="line-height:21px;"><span style="font-family:'KaiTi_GB2312';font-size:12px;">将在target目录中生成一个learning-spark-mini-example-0.0.1.jar文件 (大约88M)，该jar可以提交到spark平台执行</span></span></div>
<div><span style="line-height:21px;"><span style="font-family:'KaiTi_GB2312';font-size:12px;"><br></span></span></div>
<h1>5.向spark 提交任务与执行</h1>
<h1><span style="font-weight:normal;"><span style="font-family:'KaiTi_GB2312';font-size:12px;">演示程序是通过map-reduce的方式统计文本中的word数量，为此先用一个数据生产程序生成包含若干数据行的若干文件（本例中产生100个文件每个文件1000000。以目前spark的计算能力，测试集完全可以更大，考虑是单机伪集群，这个测试集合只作为展示，不考察其实际计算能力）。文件集合在本地，需要上传到hadoop-hdfs中，在hadoop-hdfs中构建如下目录：</span></span></h1>
<h1><span style="line-height:21px;font-weight:normal;"><span style="font-family:'KaiTi_GB2312';font-size:12px;">hdfs dfs -mkdir -p /user/hadoop/data    <span></span>#构建hdfs内部文件目录</span></span></h1>
<div><span style="font-family:'KaiTi_GB2312';font-size:12px;"><span style="line-height:21px;font-weight:normal;"></span></span>
<h1><span style="line-height:21px;font-weight:normal;"><span style="font-family:'KaiTi_GB2312';font-size:12px;">hdfs dfs -mkdir -p /user/spark<span></span><span></span>#spark相关的路径<span></span>spart-history-server
 中eventLog.dir配置该目录的applicationHistory中</span></span></h1>
</div>
<div><span style="line-height:21px;"><span style="font-family:'KaiTi_GB2312';font-size:12px;"><span style="line-height:21px;">hdfs dfs -put datadir/*.txt  /user/hadoop/data<span></span><span></span>#<span style="line-height:21px;">datadir/*.txt
 是本地数据文件路径    <span style="line-height:21px;">/user/hadoop/data 是hdfs中的文件路径</span></span></span><br></span></span></div>
<h1><span style="font-weight:normal;"><span style="font-family:'KaiTi_GB2312';font-size:12px;">将<span style="line-height:21px;">learning-spark-mini-example-0.0.1.jar拷贝到服务器，通过spark-submit命令提交任务：</span></span></span></h1>
<div><span style="font-weight:normal;"><span style="line-height:21px;"><span style="font-family:'KaiTi_GB2312';font-size:12px;">来自官网的使用说明：</span></span></span></div>
<div><span style="font-weight:normal;"><span style="font-family:'KaiTi_GB2312';font-size:12px;"><span style="line-height:21px;"></span></span></span>
<pre style="color:rgb(51,51,51);line-height:20px;background-color:rgb(245,245,245);"><code class="language-bash" style="color:inherit;border:0px;background:transparent;"><span style="font-family:'KaiTi_GB2312';font-size:12px;">./bin/spark-submit <span class="se" style="color:rgb(64,112,160);font-weight:bold;">\</span>
  --class &lt;main-class&gt; <span class="se" style="color:rgb(64,112,160);font-weight:bold;">\</span>
  --master &lt;master-url&gt; <span class="se" style="color:rgb(64,112,160);font-weight:bold;">\</span>
  --deploy-mode &lt;deploy-mode&gt; <span class="se" style="color:rgb(64,112,160);font-weight:bold;">\</span>
  --conf &lt;key&gt;<span class="o" style="color:rgb(102,102,102);">=</span>&lt;value&gt; <span class="se" style="color:rgb(64,112,160);font-weight:bold;">\</span>
  ... <span class="c" style="color:rgb(96,160,176);font-style:italic;"># other options</span>
  &lt;application-jar&gt; <span class="se" style="color:rgb(64,112,160);font-weight:bold;">\</span>
  <span class="o" style="color:rgb(102,102,102);">[</span>application-arguments<span class="o" style="color:rgb(102,102,102);">]</span></span></code></pre>
<span style="font-family:'KaiTi_GB2312';font-size:12px;">本例中的执行实例：</span></div>
<h1></h1><pre><code class="language-html"><span style="font-family:'KaiTi_GB2312';font-size:12px;">spark-submit --class com.oreilly.learningsparkexamples.mini.scala.WordCount </span><span style="font-family:'KaiTi_GB2312';font-size:12px;">--master spark://cdh1:7077 /user/spark/learning-spark-mini-example-0.0.1.jar </span><span style="font-family:'KaiTi_GB2312';font-size:12px;">hdfs://cdh1:8020/user/hadoop/data/sample_age_data1*.txt /user/spark/stat_rs.txt</span></code></pre><span style="font-weight:normal;"><span style="font-family:'KaiTi_GB2312';font-size:12px;">执行截图（没有异常抛出，执行成功）</span></span>
<h1><span style="font-weight:normal;"><span style="font-family:'KaiTi_GB2312';font-size:12px;"><img src="" alt=""><img src="" alt=""><img src="https://img-blog.csdn.net/20170807202014950" alt=""><br>
stat_rs.txt文件输出结果</span></span></h1>
<div><span style="font-weight:normal;"><span style="font-family:'KaiTi_GB2312';font-size:12px;"><img src="" alt=""><img src="https://img-blog.csdn.net/20170807202159205" alt=""><br></span></span></div>
<h1><img src="" alt=""></h1>
<h1><span style="font-weight:normal;"><span style="font-family:'KaiTi_GB2312';font-size:12px;">spark监控页面的历史任务提交记录（spark-history-server的配置是有效的，能记录到历史的任务提交记录）</span></span></h1>
<div><img src="https://img-blog.csdn.net/20170807202259010" alt=""><br></div>
<h1>6.参考文章</h1>
<div><span style="font-family:'KaiTi_GB2312';font-size:12px;">1. http://www.scala-lang.org/</span></div>
<div><span style="font-family:'KaiTi_GB2312';font-size:12px;">2. http://hadoop.apache.org/</span></div>
<div><span style="font-family:'KaiTi_GB2312';font-size:12px;">3. http://spark.apache.org/</span></div>
<div><span style="font-family:'KaiTi_GB2312';font-size:12px;">4. http://maven.apache.org/plugins/maven-shade-plugin/</span></div>
<div><span style="font-family:'KaiTi_GB2312';font-size:12px;">5. http://maven.apache.org/plugins/maven-resources-plugin/</span></div>
<div><span style="font-family:'KaiTi_GB2312';font-size:12px;">6. https://www.ibm.com/developerworks/cn/opensource/os-cn-spark-practice1/index.html</span></div>
<div><span style="font-family:'KaiTi_GB2312';font-size:12px;">7. http://www.jianshu.com/p/ecc6eb298b8f</span></div>
<div>
<div style="line-height:21px;">
<div style="background-color:inherit;"><span style="font-family:'KaiTi_GB2312';font-size:12px;">8. http://abcve.com/use-cdh5-install-hadoop-cluster/<br>
9. https://www.zybuluo.com/sasaki/note/242142<br></span></div>
<div style="background-color:inherit;"><span style="font-family:'KaiTi_GB2312';font-size:12px;">10.https://www.cloudera.com/documentation/enterprise/5-4-x/topics/cdh_qs_mrv1_pseudo.html</span></div>
<div style="background-color:inherit;"><span style="font-family:'KaiTi_GB2312';font-size:12px;">11.http://blog.javachen.com/2013/04/06/install-cloudera-cdh-by-yum<br>
12.http://dataunion.org/10345.html</span><br></div>
</div>
<br></div>
<div><br></div>
<div><br></div>
<div><br></div>
<div><br></div>
<div><br></div>
            </div>
                </div>