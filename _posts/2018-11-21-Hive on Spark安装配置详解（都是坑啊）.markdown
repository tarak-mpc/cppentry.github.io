---
layout:     post
title:      Hive on Spark安装配置详解（都是坑啊）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h1 class="postTitle" style="font-size:28px;font-weight:400;line-height:1.8;color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;">
</h1><h2 id="简介" style="font-size:21px;line-height:1.5;color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;">
简介</h2>
<p style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
本文主要记录如何安装配置Hive on Spark，在执行以下步骤之前，请先确保已经安装Hadoop集群，Hive，MySQL，JDK，Scala，具体安装步骤不再赘述。</p>
<h2 id="背景" style="font-size:21px;line-height:1.5;color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;">
背景</h2>
<p style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
Hive默认使用MapReduce作为执行引擎，即Hive on mr。实际上，Hive还可以使用Tez和Spark作为其执行引擎，分别为Hive on Tez和Hive on Spark。由于MapReduce中间计算均需要写入磁盘，而Spark是放在内存中，所以总体来讲Spark比MapReduce快很多。因此，Hive on Spark也会比Hive on mr快。为了对比Hive on Spark和Hive on mr的速度，需要在已经安装了Hadoop集群的机器上安装Spark集群（Spark集群是建立在Hadoop集群之上的，也就是需要先装Hadoop集群，再装Spark集群，因为Spark用了Hadoop的HDFS、YARN等），然后把Hive的执行引擎设置为Spark。</p>
<p style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
Spark运行模式分为三种1、Spark on YARN 2、Standalone Mode 3、Spark on Mesos。<br>
Hive on Spark默认支持Spark on YARN模式，因此我们选择Spark on YARN模式。Spark on YARN就是使用YARN作为Spark的资源管理器。分为Cluster和Client两种模式。</p>
<h2 id="一环境说明" style="font-size:21px;line-height:1.5;color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;">
一、环境说明</h2>
<p style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
本教程Hadoop相关软件全部基于CDH5.5.1，用yum安装，系统环境如下：</p>
<ul style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;"><li style="list-style-type:disc;">操作系统：CentOS 7.2</li><li style="list-style-type:disc;">Hadoop 2.6.0</li><li style="list-style-type:disc;">Hive1.1.0</li><li style="list-style-type:disc;">Spark1.5.0</li><li style="list-style-type:disc;">MySQL 5.6</li><li style="list-style-type:disc;">JDK 1.8</li><li style="list-style-type:disc;">Maven 3.3.3</li><li style="list-style-type:disc;">Scala 2.10</li></ul><p style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
各节点规划如下：</p>
<pre style="color:rgb(51,51,51);background-color:rgb(255,255,255);"><code class="hljs css" style="vertical-align:middle;background-color:rgb(255,255,255);color:rgb(0,0,0);line-height:1.5 !important;font-family:'Courier New', sans-serif !important;font-size:12px !important;border:1px solid rgb(204,204,204) !important;">192<span class="hljs-selector-class">.168</span><span class="hljs-selector-class">.117</span><span class="hljs-selector-class">.51</span>     <span class="hljs-selector-tag" style="color:rgb(0,0,255);">Goblin01</span>           <span class="hljs-selector-tag" style="color:rgb(0,0,255);">nn1</span>  <span class="hljs-selector-tag" style="color:rgb(0,0,255);">jn1</span>  <span class="hljs-selector-tag" style="color:rgb(0,0,255);">rm1</span>  <span class="hljs-selector-tag" style="color:rgb(0,0,255);">worker</span>  <span class="hljs-selector-tag" style="color:rgb(0,0,255);">master</span>  <span class="hljs-selector-tag" style="color:rgb(0,0,255);">hive</span>  <span class="hljs-selector-tag" style="color:rgb(0,0,255);">metastore</span>  <span class="hljs-selector-tag" style="color:rgb(0,0,255);">mysql</span>
192<span class="hljs-selector-class">.168</span><span class="hljs-selector-class">.117</span><span class="hljs-selector-class">.52</span>     <span class="hljs-selector-tag" style="color:rgb(0,0,255);">Goblin02</span>    <span class="hljs-selector-tag" style="color:rgb(0,0,255);">zk2</span>    <span class="hljs-selector-tag" style="color:rgb(0,0,255);">nn2</span>  <span class="hljs-selector-tag" style="color:rgb(0,0,255);">jn2</span>  <span class="hljs-selector-tag" style="color:rgb(0,0,255);">rm2</span>  <span class="hljs-selector-tag" style="color:rgb(0,0,255);">worker</span>          <span class="hljs-selector-tag" style="color:rgb(0,0,255);">hive</span>
192<span class="hljs-selector-class">.168</span><span class="hljs-selector-class">.117</span><span class="hljs-selector-class">.53</span>     <span class="hljs-selector-tag" style="color:rgb(0,0,255);">Goblin03</span>    <span class="hljs-selector-tag" style="color:rgb(0,0,255);">zk3</span>    <span class="hljs-selector-tag" style="color:rgb(0,0,255);">dn1</span>  <span class="hljs-selector-tag" style="color:rgb(0,0,255);">jn3</span>       <span class="hljs-selector-tag" style="color:rgb(0,0,255);">worker</span>          <span class="hljs-selector-tag" style="color:rgb(0,0,255);">hive</span>
192<span class="hljs-selector-class">.168</span><span class="hljs-selector-class">.117</span><span class="hljs-selector-class">.54</span>     <span class="hljs-selector-tag" style="color:rgb(0,0,255);">Goblin04</span>    <span class="hljs-selector-tag" style="color:rgb(0,0,255);">zk4</span>    <span class="hljs-selector-tag" style="color:rgb(0,0,255);">dn2</span>            <span class="hljs-selector-tag" style="color:rgb(0,0,255);">worker</span>          <span class="hljs-selector-tag" style="color:rgb(0,0,255);">hive</span></code></pre>
<p style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
说明：Goblin01~04是每台机器的hostname，zk代表zookeeper，nn代表hadoop的namenode，dn代表datanode，jn代表journalnode，rm代表resourcemanager，worker代表Spark的slaves，master代表Spark的master</p>
<h2 id="二编译和安装sparkspark-on-yarn" style="font-size:21px;line-height:1.5;color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;">
二、编译和安装Spark（Spark on YARN）</h2>
<h3 id="编译spark源码" style="font-size:16px;line-height:1.5;color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;">
2.1 编译Spark源码</h3>
<p style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
要使用Hive on Spark，所用的Spark版本必须不包含Hive的相关jar包，hive on spark 的官网上说“Note that you must have a version of Spark which does not include the Hive jars”。在spark官网下载的编译的Spark都是有集成Hive的，因此需要自己下载源码来编译，并且编译的时候不指定Hive。</p>
<p style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
我们这里用的Spark源码是spark-1.5.0-cdh5.5.1版本,下载地址如下：</p>
<p style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<a href="http://archive.cloudera.com/cdh5/cdh/5/spark-1.5.0-cdh5.5.1-src.tar.gz" rel="nofollow" class="uri" style="color:rgb(0,0,0);">http://archive.cloudera.com/cdh5/cdh/5/spark-1.5.0-cdh5.5.1-src.tar.gz</a></p>
<p style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
下载完后用 tar xzvf 命令解压，进入解压完的文件夹，准备编译。</p>
<p style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<span>注意：编译前请确保已经安装JDK、Maven和Scala，maven为3.3.3及以上版本，并在/etc/profile里配置环境变量。</span></p>
<p style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
命令行进入在源码根目录下，执行</p>
<pre style="color:rgb(51,51,51);background-color:rgb(255,255,255);"><code class="hljs go" style="vertical-align:middle;background-color:rgb(255,255,255);color:rgb(0,0,0);line-height:1.5 !important;font-family:'Courier New', sans-serif !important;font-size:12px !important;border:1px solid rgb(204,204,204) !important;">  ./<span class="hljs-built_in" style="color:rgb(0,0,255);">make</span>-distribution.sh --name <span class="hljs-string" style="color:rgb(163,21,21);">"hadoop2-without-hive"</span> --tgz <span class="hljs-string" style="color:rgb(163,21,21);">"-Pyarn,hadoop-provided,hadoop-2.6,parquet-provided"</span></code></pre>
<p style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
若编译过程出现内存不足的情况，需要在运行编译命令之前先运行：</p>
<pre style="color:rgb(51,51,51);background-color:rgb(255,255,255);"><code class="hljs objectivec" style="vertical-align:middle;background-color:rgb(255,255,255);color:rgb(0,0,0);line-height:1.5 !important;font-family:'Courier New', sans-serif !important;font-size:12px !important;border:1px solid rgb(204,204,204) !important;"><span class="hljs-keyword" style="color:rgb(0,0,255);">export</span> M<span class="hljs-built_in" style="color:rgb(0,0,255);">AVEN_OPTS</span>=<span class="hljs-string" style="color:rgb(163,21,21);">"-Xmx2g -XX:MaxPermSize=512M -XX:ReservedCodeCacheSize=512m"</span></code></pre>
<p style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
来设置Maven的内存。</p>
<p style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
编译过程由于要下载很多Maven依赖的jar包，需要时间较长（大概一两个小时），要保证网络状况良好，不然很容易编译失败。若出现以下结果，则编译成功：</p>
<p style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<img src="https://images2015.cnblogs.com/blog/860266/201608/860266-20160826100741351-432763457.jpg" alt="" style="margin-left:0px;border:none;"></p>
<p style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
编译成功后，会在源码根目录下多出一个文件(红色部分）：</p>
<p style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
spark-1.5.0-cdh5.5.1-bin-hadoop2-without-hive.tgz</p>
<p style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<img src="https://images2015.cnblogs.com/blog/860266/201608/860266-20160826100811351-962892885.png" alt="" style="margin-left:0px;border:none;"></p>
<h3 id="安装spark" style="font-size:16px;line-height:1.5;color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;">
2.2 安装Spark</h3>
<ul style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;"><li style="list-style-type:disc;">
<p>将编译完生成的spark-1.5.0-cdh5.5.1-bin-hadoop2-without-hive.tgz拷贝到Spark的安装路径，并用 tar -xzvf 命令解压</p>
</li><li style="list-style-type:disc;">
<p>配置环境变量</p>
</li></ul><p style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<code style="line-height:1.8;vertical-align:middle;display:inline-block;font-family:'Courier New', sans-serif !important;font-size:12px !important;background-color:rgb(245,245,245) !important;border:1px solid rgb(204,204,204) !important;">$vim
 /etc/profile export SPARK_HOME=spark安装路径 $source /etc/profile</code></p>
<h3 id="配置spark" style="font-size:16px;line-height:1.5;color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;">
2.3 配置Spark</h3>
<p style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
配置spark-env.sh、slaves和spark-defaults.conf三个文件</p>
<ul style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;"><li style="list-style-type:disc;">spark-env.sh</li></ul><p style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
主要配置JAVA_HOME、SCALA_HOME、HADOOP_HOME、HADOOP_CONF_DIR、SPARK_MASTER_IP等</p>
<pre style="color:rgb(51,51,51);background-color:rgb(255,255,255);"><code class="hljs javascript" style="vertical-align:middle;background-color:rgb(255,255,255);color:rgb(0,0,0);line-height:1.5 !important;font-family:'Courier New', sans-serif !important;font-size:12px !important;border:1px solid rgb(204,204,204) !important;"><span class="hljs-keyword" style="color:rgb(0,0,255);">export</span> JAVA_HOME=<span class="hljs-regexp">/usr/</span>lib/jvm/java
<span class="hljs-keyword" style="color:rgb(0,0,255);">export</span> SCALA_HOME=<span class="hljs-regexp">/root/</span>scala
<span class="hljs-keyword" style="color:rgb(0,0,255);">export</span> HADOOP_HOME=<span class="hljs-regexp">/usr/</span>lib/hadoop
<span class="hljs-keyword" style="color:rgb(0,0,255);">export</span> HADOOP_CONF_DIR=$HADOOP_HOME/etc/hadoop 
<span class="hljs-keyword" style="color:rgb(0,0,255);">export</span> YARN_CONF_DIR=$HADOOP_HOME/etc/hadoop
<span class="hljs-keyword" style="color:rgb(0,0,255);">export</span> SPARK_LAUNCH_WITH_SCALA=<span class="hljs-number">0</span>
<span class="hljs-keyword" style="color:rgb(0,0,255);">export</span> SPARK_WORKER_MEMORY=<span class="hljs-number">1</span>g
<span class="hljs-keyword" style="color:rgb(0,0,255);">export</span> SPARK_DRIVER_MEMORY=<span class="hljs-number">1</span>g
<span class="hljs-keyword" style="color:rgb(0,0,255);">export</span> SPARK_MASTER_IP=<span class="hljs-number">192.168</span><span class="hljs-number">.117</span><span class="hljs-number">.51</span>
<span class="hljs-keyword" style="color:rgb(0,0,255);">export</span> SPARK_LIBRARY_PATH=<span class="hljs-regexp">/root/</span>spark-without-hive/lib
<span class="hljs-keyword" style="color:rgb(0,0,255);">export</span> SPARK_MASTER_WEBUI_PORT=<span class="hljs-number">18080</span>
<span class="hljs-keyword" style="color:rgb(0,0,255);">export</span> SPARK_WORKER_DIR=<span class="hljs-regexp">/root/</span>spark-without-hive/work
<span class="hljs-keyword" style="color:rgb(0,0,255);">export</span> SPARK_MASTER_PORT=<span class="hljs-number">7077</span>
<span class="hljs-keyword" style="color:rgb(0,0,255);">export</span> SPARK_WORKER_PORT=<span class="hljs-number">7078</span>
<span class="hljs-keyword" style="color:rgb(0,0,255);">export</span> SPARK_LOG_DIR=<span class="hljs-regexp">/root/</span>spark-without-hive/log
<span class="hljs-keyword" style="color:rgb(0,0,255);">export</span> SPARK_PID_DIR=<span class="hljs-string" style="color:rgb(163,21,21);">'/root/spark-without-hive/run'</span></code></pre>
<ul style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;"><li style="list-style-type:disc;">slaves（将所有节点都加入，master节点同时也是worker节点）</li></ul><pre style="color:rgb(51,51,51);background-color:rgb(255,255,255);"><code class="hljs nginx" style="vertical-align:middle;background-color:rgb(255,255,255);color:rgb(0,0,0);line-height:1.5 !important;font-family:'Courier New', sans-serif !important;font-size:12px !important;border:1px solid rgb(204,204,204) !important;"><span class="hljs-attribute" style="color:rgb(163,21,21);">Goblin01</span>
Goblin02
Goblin03
Goblin04</code></pre>
<ul style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;"><li style="list-style-type:disc;">spark-defaults.conf</li></ul><pre style="color:rgb(51,51,51);background-color:rgb(255,255,255);"><code class="hljs groovy" style="vertical-align:middle;background-color:rgb(255,255,255);color:rgb(0,0,0);line-height:1.5 !important;font-family:'Courier New', sans-serif !important;font-size:12px !important;border:1px solid rgb(204,204,204) !important;"> spark.master                     yarn-cluster
 spark.home                       <span class="hljs-regexp">/root/</span>spark-without-hive
 spark.eventLog.enabled           <span class="hljs-literal" style="color:rgb(163,21,21);">true</span>
 spark.eventLog.dir               <span class="hljs-string" style="color:rgb(163,21,21);">hdfs:</span><span class="hljs-comment" style="color:#008000;">//Goblin01:8020/spark-log</span>
 spark.serializer                 org.apache.spark.serializer.KryoSerializer
 spark.executor.memory            <span class="hljs-number">1</span>g
 spark.driver.memory              <span class="hljs-number">1</span>g
 spark.executor.extraJavaOptions  -<span class="hljs-string" style="color:rgb(163,21,21);">XX:</span>+PrintGCDetails -Dkey=value -Dnumbers=<span class="hljs-string" style="color:rgb(163,21,21);">"one two three"</span></code></pre>
<p style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
spark.master指定Spark运行模式，可以是yarn-client、yarn-cluster...</p>
<p style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
spark.home指定SPARK_HOME路径</p>
<p style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
spark.eventLog.enabled需要设为true</p>
<p style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
spark.eventLog.dir指定路径，放在master节点的hdfs中，端口要跟hdfs设置的端口一致（默认为8020），否则会报错</p>
<p style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
spark.executor.memory和spark.driver.memory指定executor和dirver的内存，512m或1g，既不能太大也不能太小，因为太小运行不了，太大又会影响其他服务</p>
<h2 id="三配置yarn" style="font-size:21px;line-height:1.5;color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;">
三、配置YARN</h2>
<p style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
配置yarn-site.xml，跟hdfs-site.xml在同一个路径下（$HADOOP_HOME/etc/hadoop)</p>
<pre style="color:rgb(51,51,51);background-color:rgb(255,255,255);"><code class="hljs django" style="vertical-align:middle;background-color:rgb(255,255,255);color:rgb(0,0,0);line-height:1.5 !important;font-family:'Courier New', sans-serif !important;font-size:12px !important;border:1px solid rgb(204,204,204) !important;"><span class="xml"><span class="hljs-tag" style="color:rgb(0,0,255);">&lt;<span class="hljs-name">property</span>&gt;</span>
  <span class="hljs-tag" style="color:rgb(0,0,255);">&lt;<span class="hljs-name">name</span>&gt;</span>yarn.resourcemanager.scheduler.class<span class="hljs-tag" style="color:rgb(0,0,255);">&lt;/<span class="hljs-name">name</span>&gt;</span>
  <span class="hljs-tag" style="color:rgb(0,0,255);">&lt;<span class="hljs-name">value</span>&gt;</span>org.apache.hadoop.yarn.server.resourcemanager.scheduler.fair.FairScheduler<span class="hljs-tag" style="color:rgb(0,0,255);">&lt;/<span class="hljs-name">value</span>&gt;</span>
<span class="hljs-tag" style="color:rgb(0,0,255);">&lt;/<span class="hljs-name">property</span>&gt;</span></span></code></pre>
<h2 id="四配置hive" style="font-size:21px;line-height:1.5;color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;">
四、配置Hive</h2>
<ul style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;"><li style="list-style-type:disc;">添加spark依赖到hive(将spark-assembly-1.5.0-cdh5.5.1-hadoop2.6.0.jar拷贝到$HIVE_HOME/lib目录下）</li></ul><p style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
进入SPARK_HOME</p>
<pre style="color:rgb(51,51,51);background-color:rgb(255,255,255);"><code class="hljs vbnet" style="vertical-align:middle;background-color:rgb(255,255,255);color:rgb(0,0,0);line-height:1.5 !important;font-family:'Courier New', sans-serif !important;font-size:12px !important;border:1px solid rgb(204,204,204) !important;">cp spark-<span class="hljs-keyword" style="color:rgb(0,0,255);">assembly</span><span class="hljs-number">-1.5</span><span class="hljs-number">.0</span>-cdh5<span class="hljs-number">.5</span><span class="hljs-number">.1</span>-hadoop2<span class="hljs-number">.6</span><span class="hljs-number">.0</span>.jar /usr/<span class="hljs-keyword" style="color:rgb(0,0,255);">lib</span>/hive/<span class="hljs-keyword" style="color:rgb(0,0,255);">lib</span></code></pre>
<ul style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;"><li style="list-style-type:disc;">配置hive-site.xml</li></ul><p style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
配置的内容与spark-defaults.conf相同，只是形式不一样,以下内容是追加到hive-site.xml文件中的,并且注意前两个配置，如果不设置hive的spark引擎用不了，在后面会有详细的错误说明。</p>
<pre style="color:rgb(51,51,51);background-color:rgb(255,255,255);"><code class="hljs dts" style="vertical-align:middle;background-color:rgb(255,255,255);color:rgb(0,0,0);line-height:1.5 !important;font-family:'Courier New', sans-serif !important;font-size:12px !important;border:1px solid rgb(204,204,204) !important;">
<span class="hljs-params">&lt;property&gt;</span>
  <span class="hljs-params">&lt;name&gt;</span>hive.execution.engine<span class="hljs-params">&lt;/name&gt;</span>
  <span class="hljs-params">&lt;value&gt;</span>spark<span class="hljs-params">&lt;/value&gt;</span>
<span class="hljs-params">&lt;/property&gt;</span>

<span class="hljs-params">&lt;property&gt;</span>
  <span class="hljs-params">&lt;name&gt;</span>hive.enable.spark.execution.engine<span class="hljs-params">&lt;/name&gt;</span>
  <span class="hljs-params">&lt;value&gt;</span>true<span class="hljs-params">&lt;/value&gt;</span>
<span class="hljs-params">&lt;/property&gt;</span>

<span class="hljs-params">&lt;property&gt;</span>
  <span class="hljs-params">&lt;name&gt;</span>spark.home<span class="hljs-params">&lt;/name&gt;</span>
  <span class="hljs-params">&lt;value&gt;</span><span class="hljs-meta-keyword">/root/</span>spark-without-hive<span class="hljs-params">&lt;/value&gt;</span>
<span class="hljs-params">&lt;/property&gt;</span>
<span class="hljs-params">&lt;property&gt;</span>
  <span class="hljs-params">&lt;name&gt;</span>spark.master<span class="hljs-params">&lt;/name&gt;</span>
  <span class="hljs-params">&lt;value&gt;</span>yarn-client<span class="hljs-params">&lt;/value&gt;</span>
<span class="hljs-params">&lt;/property&gt;</span>
<span class="hljs-params">&lt;property&gt;</span>
  <span class="hljs-params">&lt;name&gt;</span>spark.enentLog.enabled<span class="hljs-params">&lt;/name&gt;</span>
  <span class="hljs-params">&lt;value&gt;</span>true<span class="hljs-params">&lt;/value&gt;</span>
<span class="hljs-params">&lt;/property&gt;</span>
<span class="hljs-params">&lt;property&gt;</span>
  <span class="hljs-params">&lt;name&gt;</span>spark.enentLog.dir<span class="hljs-params">&lt;/name&gt;</span>
  <span class="hljs-params">&lt;value&gt;</span>hdfs:<span class="hljs-comment" style="color:#008000;">//Goblin01:8020/spark-log&lt;/value&gt;</span>
<span class="hljs-params">&lt;/property&gt;</span>
<span class="hljs-params">&lt;property&gt;</span>
  <span class="hljs-params">&lt;name&gt;</span>spark.serializer<span class="hljs-params">&lt;/name&gt;</span>
  <span class="hljs-params">&lt;value&gt;</span>org.apache.spark.serializer.KryoSerializer<span class="hljs-params">&lt;/value&gt;</span>
<span class="hljs-params">&lt;/property&gt;</span>
<span class="hljs-params">&lt;property&gt;</span>
  <span class="hljs-params">&lt;name&gt;</span>spark.executor.memeory<span class="hljs-params">&lt;/name&gt;</span>
  <span class="hljs-params">&lt;value&gt;</span><span class="hljs-number">1</span>g<span class="hljs-params">&lt;/value&gt;</span>
<span class="hljs-params">&lt;/property&gt;</span>
<span class="hljs-params">&lt;property&gt;</span>
  <span class="hljs-params">&lt;name&gt;</span>spark.driver.memeory<span class="hljs-params">&lt;/name&gt;</span>
  <span class="hljs-params">&lt;value&gt;</span><span class="hljs-number">1</span>g<span class="hljs-params">&lt;/value&gt;</span>
<span class="hljs-params">&lt;/property&gt;</span>
<span class="hljs-params">&lt;property&gt;</span>
  <span class="hljs-params">&lt;name&gt;</span>spark.executor.extraJavaOptions<span class="hljs-params">&lt;/name&gt;</span>
  <span class="hljs-params">&lt;value&gt;</span>-XX:+PrintGCDetails -Dkey=value -Dnumbers=<span class="hljs-string" style="color:rgb(163,21,21);">"one two three"</span><span class="hljs-params">&lt;/value&gt;</span>
<span class="hljs-params">&lt;/property&gt;</span></code></pre>
<h2 id="五验证是否安装配置成功" style="font-size:21px;line-height:1.5;color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;">
五、验证是否安装配置成功</h2>
<h3 id="验证spark集群" style="font-size:16px;line-height:1.5;color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;">
1.验证Spark集群</h3>
<p style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<span>注意：在启动Spark集群之前，要确保Hadoop集群和YARN均已启动</span></p>
<ul style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;"><li style="list-style-type:disc;">进入$SPARK_HOME目录，执行：</li></ul><pre style="color:rgb(51,51,51);background-color:rgb(255,255,255);"><code class="hljs fortran" style="vertical-align:middle;background-color:rgb(255,255,255);color:rgb(0,0,0);line-height:1.5 !important;font-family:'Courier New', sans-serif !important;font-size:12px !important;border:1px solid rgb(204,204,204) !important;">./sbin/start-<span class="hljs-built_in" style="color:rgb(0,0,255);">all</span>.sh</code></pre>
<p style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
用jps命令查看51节点上的master和worker，52、53、54节点上的worker是否都启动了</p>
<ul style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;"><li style="list-style-type:disc;">同样在$SPARK_HOME目录下，提交计算Pi的任务，验证Spark集群是否能正常工作，运行如下命令</li></ul><pre style="color:rgb(51,51,51);background-color:rgb(255,255,255);"><code class="hljs groovy" style="vertical-align:middle;background-color:rgb(255,255,255);color:rgb(0,0,0);line-height:1.5 !important;font-family:'Courier New', sans-serif !important;font-size:12px !important;border:1px solid rgb(204,204,204) !important;">.<span class="hljs-regexp">/bin/</span>spark-submit --<span class="hljs-class"><span class="hljs-keyword" style="color:rgb(0,0,255);">class</span> <span class="hljs-title" style="color:rgb(163,21,21);">org</span>.<span class="hljs-title" style="color:rgb(163,21,21);">apache</span>.<span class="hljs-title" style="color:rgb(163,21,21);">spark</span>.<span class="hljs-title" style="color:rgb(163,21,21);">examples</span>.<span class="hljs-title" style="color:rgb(163,21,21);">SparkPi</span> --<span class="hljs-title" style="color:rgb(163,21,21);">master</span> <span class="hljs-title" style="color:rgb(163,21,21);">yarn</span> --<span class="hljs-title" style="color:rgb(163,21,21);">deploy</span>-<span class="hljs-title" style="color:rgb(163,21,21);">mode</span> <span class="hljs-title" style="color:rgb(163,21,21);">client</span> <span class="hljs-title" style="color:rgb(163,21,21);">lib</span>/<span class="hljs-title" style="color:rgb(163,21,21);">spark</span>-<span class="hljs-title" style="color:rgb(163,21,21);">examples</span>-1.5.0-<span class="hljs-title" style="color:rgb(163,21,21);">cdh5</span>.5.1-<span class="hljs-title" style="color:rgb(163,21,21);">hadoop2</span>.6.0.<span class="hljs-title" style="color:rgb(163,21,21);">jar</span> 10</span></code></pre>
<p style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
若无报错，并且算出Pi的值，说明Spark集群能正常工作</p>
<h3 id="验证hive-on-spark是否可用" style="font-size:16px;line-height:1.5;color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;">
2.验证Hive on Spark是否可用</h3>
<ul style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;"><li style="list-style-type:disc;">命令行输入 hive，进入hive CLI</li><li style="list-style-type:disc;">set hive.execution.engine=spark; (将执行引擎设为Spark，默认是mr，退出hive CLI后，回到默认设置。若想让引擎默认为Spark，需要在hive-site.xml里设置）</li><li style="list-style-type:disc;">create table test(ts BIGINT,line STRING); (创建表）</li><li style="list-style-type:disc;">select count(*) from test;</li><li style="list-style-type:disc;">若整个过程没有报错，并出现正确结果，则Hive on Spark配置成功。</li></ul><h2 id="六遇到的问题" style="font-size:21px;line-height:1.5;color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;">
六、遇到的问题</h2>
<h3 id="section" style="font-size:16px;line-height:1.5;color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;">
0</h3>
<p style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
编译spark基于maven有两种方式</p>
<ul style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;"><li style="list-style-type:disc;">用mvn 命令编译</li></ul><pre style="color:rgb(51,51,51);background-color:rgb(255,255,255);"><code class="hljs groovy" style="vertical-align:middle;background-color:rgb(255,255,255);color:rgb(0,0,0);line-height:1.5 !important;font-family:'Courier New', sans-serif !important;font-size:12px !important;border:1px solid rgb(204,204,204) !important;">.<span class="hljs-regexp">/build/</span>mvn -Pyarn -Phadoop<span class="hljs-number">-2.6</span> -Dhadoop.version=<span class="hljs-number">2.6</span><span class="hljs-number">.0</span> -DskipTests clean <span class="hljs-keyword" style="color:rgb(0,0,255);">package</span> </code></pre>
<p style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
编译到倒数MQTT模块一直报错，而且编译出的文件比较大，不适合安装集群，因此不推荐。使用Intellij IDEA maven 插件报错如下：</p>
<p style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<img src="https://images2015.cnblogs.com/blog/860266/201608/860266-20160826100851335-1817140507.png" alt="" style="margin-left:0px;border:none;"></p>
<ul style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;"><li style="list-style-type:disc;">使用spark提供的预编译脚本，网络状况稳定，会编译出需要的安装版本，<span><span>推荐</span></span>。命令</li></ul><pre style="color:rgb(51,51,51);background-color:rgb(255,255,255);"><code class="hljs go" style="vertical-align:middle;background-color:rgb(255,255,255);color:rgb(0,0,0);line-height:1.5 !important;font-family:'Courier New', sans-serif !important;font-size:12px !important;border:1px solid rgb(204,204,204) !important;">./<span class="hljs-built_in" style="color:rgb(0,0,255);">make</span>-distribution.sh --name <span class="hljs-string" style="color:rgb(163,21,21);">"hadoop2-without-hive"</span> --tgz <span class="hljs-string" style="color:rgb(163,21,21);">"-Pyarn,hadoop-provided,hadoop-2.6,parquet-provided"</span></code></pre>
<p style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
结果如上文所述。</p>
<h3 id="section-1" style="font-size:16px;line-height:1.5;color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;">
1</h3>
<p style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
运行：</p>
<pre style="color:rgb(51,51,51);background-color:rgb(255,255,255);"><code class="hljs groovy" style="vertical-align:middle;background-color:rgb(255,255,255);color:rgb(0,0,0);line-height:1.5 !important;font-family:'Courier New', sans-serif !important;font-size:12px !important;border:1px solid rgb(204,204,204) !important;">.<span class="hljs-regexp">/bin/</span>spark-submit --<span class="hljs-class"><span class="hljs-keyword" style="color:rgb(0,0,255);">class</span> <span class="hljs-title" style="color:rgb(163,21,21);">org</span>.<span class="hljs-title" style="color:rgb(163,21,21);">apache</span>.<span class="hljs-title" style="color:rgb(163,21,21);">spark</span>.<span class="hljs-title" style="color:rgb(163,21,21);">examples</span>.<span class="hljs-title" style="color:rgb(163,21,21);">SparkPi</span>  --<span class="hljs-title" style="color:rgb(163,21,21);">master</span> <span class="hljs-title" style="color:rgb(163,21,21);">yarn</span>  <span class="hljs-title" style="color:rgb(163,21,21);">lib</span>/<span class="hljs-title" style="color:rgb(163,21,21);">spark</span>-<span class="hljs-title" style="color:rgb(163,21,21);">examples</span>-1.5.0-<span class="hljs-title" style="color:rgb(163,21,21);">cdh5</span>.5.1-<span class="hljs-title" style="color:rgb(163,21,21);">hadoop2</span>.6.0.<span class="hljs-title" style="color:rgb(163,21,21);">jar</span> 10</span></code></pre>
<p style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
报错：</p>
<p style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<img src="https://images2015.cnblogs.com/blog/860266/201608/860266-20160826100936272-905348525.png" alt="" style="margin-left:0px;border:none;"></p>
<p style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
原因：</p>
<p style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
hdfs的默认端口为8020 ，而我们在spark-default.conf中配置成了8021端口，导致连接不上HDFS报错</p>
<pre style="color:rgb(51,51,51);background-color:rgb(255,255,255);"><code class="hljs groovy" style="vertical-align:middle;background-color:rgb(255,255,255);color:rgb(0,0,0);line-height:1.5 !important;font-family:'Courier New', sans-serif !important;font-size:12px !important;border:1px solid rgb(204,204,204) !important;">spark.eventLog.enabled           <span class="hljs-literal" style="color:rgb(163,21,21);">true</span>
spark.eventLog.dir              <span class="hljs-string" style="color:rgb(163,21,21);">hdfs:</span><span class="hljs-comment" style="color:#008000;">//Goblin01:8021/spark-log</span></code></pre>
<p style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
解决：</p>
<p style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
配置spark-default.conf中的spark.eventLog.dir 为本地路径，也就是不持久化日志到hdfs上，也就没有和hdfs的通行</p>
<p style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
or</p>
<p style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
spark-default.conf 注释掉 spark.eventLog.enabled   true</p>
<p style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
or</p>
<p style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
在spark-default.conf里配置的eventLog端口跟hdfs的默认端口（8020）一致</p>
<p style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
or</p>
<p style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
由于配置的hdfs是高可用的，51,52都可以作为namenode,我们的spark集群的主节点在51上，当51上的namenode变成standby，导致无法访问hdfs的8020端口（hdfs默认端口），也就是说在51上读不出hdfs上spark-log的内容，在spark-default.conf中配置为spark.eventLog.dir hdfs://Goblin01:8021/spark-log，如果发生这种情况，直接kill掉52，让namenode只在51上运行。（这个后面要搭建spark的高可用模式解决）</p>
<h3 id="section-2" style="font-size:16px;line-height:1.5;color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;">
2</h3>
<p style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
运行：</p>
<p style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
在hive里设置引擎为spark，执行select count(*) from a; </p>
<p style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
报错：</p>
<pre style="color:rgb(51,51,51);background-color:rgb(255,255,255);"><code class="hljs sql" style="vertical-align:middle;background-color:rgb(255,255,255);color:rgb(0,0,0);line-height:1.5 !important;font-family:'Courier New', sans-serif !important;font-size:12px !important;border:1px solid rgb(204,204,204) !important;">Failed to <span class="hljs-keyword" style="color:rgb(0,0,255);">execute</span> spark task, <span class="hljs-keyword" style="color:rgb(0,0,255);">with</span> <span class="hljs-keyword" style="color:rgb(0,0,255);">exception</span> <span class="hljs-string" style="color:rgb(163,21,21);">'org.apache.hadoop.hive.ql.metadata.HiveException(Unsupported execution engine: Spark. Please set hive.execution.engine=mr)'</span></code></pre>
<p style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
解决：</p>
<p style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
这是因为CDH版的Hive默认运行支持Hive on Spark（By default, Hive on Spark is not enabled）.</p>
<p style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
需要用cloudera manager（cloudera官网给的的方法，但是要装cloudera manager，比较麻烦，不建议）</p>
<pre style="color:rgb(51,51,51);background-color:rgb(255,255,255);"><code class="hljs sql" style="vertical-align:middle;background-color:rgb(255,255,255);color:rgb(0,0,0);line-height:1.5 !important;font-family:'Courier New', sans-serif !important;font-size:12px !important;border:1px solid rgb(204,204,204) !important;">Go to the Hive service.
Click the Configuration tab.
Enter Enable Hive on Sparkin the Search field.
<span class="hljs-keyword" style="color:rgb(0,0,255);">Check</span> the box <span class="hljs-keyword" style="color:rgb(0,0,255);">for</span> <span class="hljs-keyword" style="color:rgb(0,0,255);">Enable</span> Hive <span class="hljs-keyword" style="color:rgb(0,0,255);">on</span> Spark (Unsupported).
<span class="hljs-keyword" style="color:rgb(0,0,255);">Locate</span> the Spark <span class="hljs-keyword" style="color:rgb(0,0,255);">On</span> YARN Service <span class="hljs-keyword" style="color:rgb(0,0,255);">and</span> click SPARK_ON_YARN.
Click <span class="hljs-keyword" style="color:rgb(0,0,255);">Save</span> Changes <span class="hljs-keyword" style="color:rgb(0,0,255);">to</span> <span class="hljs-keyword" style="color:rgb(0,0,255);">commit</span> the changes.</code></pre>
<p style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
或者</p>
<p style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
在hive-site.xml添加配置(简单、推荐）</p>
<pre style="color:rgb(51,51,51);background-color:rgb(255,255,255);"><code class="hljs django" style="vertical-align:middle;background-color:rgb(255,255,255);color:rgb(0,0,0);line-height:1.5 !important;font-family:'Courier New', sans-serif !important;font-size:12px !important;border:1px solid rgb(204,204,204) !important;"><span class="xml"><span class="hljs-tag" style="color:rgb(0,0,255);">&lt;<span class="hljs-name">property</span>&gt;</span>
<span class="hljs-tag" style="color:rgb(0,0,255);">&lt;<span class="hljs-name">name</span>&gt;</span>hive.enable.spark.execution.engine<span class="hljs-tag" style="color:rgb(0,0,255);">&lt;/<span class="hljs-name">name</span>&gt;</span>
<span class="hljs-tag" style="color:rgb(0,0,255);">&lt;<span class="hljs-name">value</span>&gt;</span>true<span class="hljs-tag" style="color:rgb(0,0,255);">&lt;/<span class="hljs-name">value</span>&gt;</span>
<span class="hljs-tag" style="color:rgb(0,0,255);">&lt;/<span class="hljs-name">property</span>&gt;</span></span></code></pre>
<h3 id="section-3" style="font-size:16px;line-height:1.5;color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;">
3</h3>
<p style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
终端输入hive无法启动hive CLI</p>
<p style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
原因：namenode挂了</p>
<p style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
解决：重启namenode</p>
<h3 id="section-4" style="font-size:16px;line-height:1.5;color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;">
4</h3>
<p style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
运行：</p>
<pre style="color:rgb(51,51,51);background-color:rgb(255,255,255);"><code class="hljs groovy" style="vertical-align:middle;background-color:rgb(255,255,255);color:rgb(0,0,0);line-height:1.5 !important;font-family:'Courier New', sans-serif !important;font-size:12px !important;border:1px solid rgb(204,204,204) !important;">.<span class="hljs-regexp">/bin/</span>spark-submit --<span class="hljs-class"><span class="hljs-keyword" style="color:rgb(0,0,255);">class</span> <span class="hljs-title" style="color:rgb(163,21,21);">org</span>.<span class="hljs-title" style="color:rgb(163,21,21);">apache</span>.<span class="hljs-title" style="color:rgb(163,21,21);">spark</span>.<span class="hljs-title" style="color:rgb(163,21,21);">examples</span>.<span class="hljs-title" style="color:rgb(163,21,21);">SparkPi</span> --<span class="hljs-title" style="color:rgb(163,21,21);">master</span> <span class="hljs-title" style="color:rgb(163,21,21);">yarn</span> --<span class="hljs-title" style="color:rgb(163,21,21);">deploy</span>-<span class="hljs-title" style="color:rgb(163,21,21);">mode</span> <span class="hljs-title" style="color:rgb(163,21,21);">client</span> <span class="hljs-title" style="color:rgb(163,21,21);">lib</span>/<span class="hljs-title" style="color:rgb(163,21,21);">spark</span>-<span class="hljs-title" style="color:rgb(163,21,21);">examples</span>-1.5.0-<span class="hljs-title" style="color:rgb(163,21,21);">cdh5</span>.5.1-<span class="hljs-title" style="color:rgb(163,21,21);">hadoop2</span>.6.0.<span class="hljs-title" style="color:rgb(163,21,21);">jar</span> 10</span></code></pre>
<p style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
问题：</p>
<p style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
没有报错，但是出现以下情况，停不下来</p>
<p style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<img src="https://images2015.cnblogs.com/blog/860266/201608/860266-20160826100956804-461630238.png" alt="" style="margin-left:0px;border:none;"></p>
<p style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
原因：</p>
<ol style="margin-left:40px;color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;"><li style="list-style-type:decimal;">ResourceManager或者NodeManager挂掉，一直没有NodeManager响应，任务无法执行，所有停不下来。</li><li style="list-style-type:decimal;">还有一种情况是spark有别的application在运行，导致本次spark任务的等待或者失败</li></ol><p style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
解决：</p>
<ol style="margin-left:40px;color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;"><li style="list-style-type:decimal;">对于原因1，重启ResourceManager和NodeManager。</li></ol><pre style="color:rgb(51,51,51);background-color:rgb(255,255,255);"><code class="hljs sql" style="vertical-align:middle;background-color:rgb(255,255,255);color:rgb(0,0,0);line-height:1.5 !important;font-family:'Courier New', sans-serif !important;font-size:12px !important;border:1px solid rgb(204,204,204) !important;">service hadoop-yarn-resourcemanager <span class="hljs-keyword" style="color:rgb(0,0,255);">start</span>;
service hadoop-yarn-nodemanager <span class="hljs-keyword" style="color:rgb(0,0,255);">start</span>;</code></pre>
<ol style="margin-left:40px;color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;"><li style="list-style-type:decimal;">对于原因2，解决办法是在hadoop配置文件中设置yarn的并行度，在<code style="line-height:1.8;vertical-align:middle;display:inline-block;font-family:'Courier New', sans-serif !important;font-size:12px !important;background-color:rgb(245,245,245) !important;border:1px solid rgb(204,204,204) !important;">/etc/hadoop/conf/capacity-scheduler.xml</code>文件中配置yarn.scheduler.capacity.maximum-am-resource-percent
 from 0.1 to 0.5</li></ol><pre style="color:rgb(51,51,51);background-color:rgb(255,255,255);"><code class="hljs django" style="vertical-align:middle;background-color:rgb(255,255,255);color:rgb(0,0,0);line-height:1.5 !important;font-family:'Courier New', sans-serif !important;font-size:12px !important;border:1px solid rgb(204,204,204) !important;"><span class="xml"> <span class="hljs-tag" style="color:rgb(0,0,255);">&lt;<span class="hljs-name">property</span>&gt;</span>
    <span class="hljs-tag" style="color:rgb(0,0,255);">&lt;<span class="hljs-name">name</span>&gt;</span>yarn.scheduler.capacity.maximum-am-resource-percent<span class="hljs-tag" style="color:rgb(0,0,255);">&lt;/<span class="hljs-name">name</span>&gt;</span>
    <span class="hljs-tag" style="color:rgb(0,0,255);">&lt;<span class="hljs-name">value</span>&gt;</span>0.5<span class="hljs-tag" style="color:rgb(0,0,255);">&lt;/<span class="hljs-name">value</span>&gt;</span>
    <span class="hljs-tag" style="color:rgb(0,0,255);">&lt;<span class="hljs-name">description</span>&gt;</span>
      Maximum percent of resources in the cluster which can be used to run
      application masters i.e. controls number of concurrent running
      applications.
    <span class="hljs-tag" style="color:rgb(0,0,255);">&lt;/<span class="hljs-name">description</span>&gt;</span>
  <span class="hljs-tag" style="color:rgb(0,0,255);">&lt;/<span class="hljs-name">property</span>&gt;</span></span></code></pre>
<br>            </div>
                </div>