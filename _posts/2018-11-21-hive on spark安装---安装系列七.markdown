---
layout:     post
title:      hive on spark安装---安装系列七
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/qq_19968255/article/details/71023759				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p><span style="color:#ff0000;"><strong><u>承接安装系列环境</u></strong></span></p>

<p> </p>

<p>背景：Hive默认使用MapReduce作为执行引擎，即Hive on mr。实际上，Hive还可以使用Tez和Spark作为其执行引擎，分别为Hive on Tez和Hive on Spark。由于MapReduce中间计算均需要写入磁盘，而Spark是放在内存中，所以总体来讲Spark比MapReduce快很多。因此，Hive on Spark也会比Hive on mr快。为了对比Hive on Spark和Hive on mr的速度，需要在已经安装了Hadoop集群的机器上安装Spark集群（Spark集群是建立在Hadoop集群之上的，也就是需要先装Hadoop集群，再装Spark集群，因为Spark用了Hadoop的HDFS、YARN等），然后把Hive的执行引擎设置为Spark。</p>

<p> </p>

<p>Spark运行模式分为三种1、Spark on YARN 2、Standalone Mode 3、Spark on Mesos。</p>

<p>Hive on Spark默认支持Spark on YARN模式，因此我们选择Spark on YARN模式。Spark on YARN就是使用YARN作为Spark的资源管理器。分为Cluster和Client两种模式。</p>

<p> </p>

<p>环境说明(要么所有安装包基于cdh,要么都不基于)</p>

<p>操作系统：CentOS 7</p>

<p>Hadoop 2.6.0(按照原先文档安装)</p>

<p>Zookeeper3.4.5(按照原先文档安装)</p>

<p>Hbase1.1.4(按照原先文档安装)</p>

<p>Hive2.0(按照原先文档安装)</p>

<p>Spark1.5.0</p>

<p>MySQL 5.1(按照原先文档安装)</p>

<p>JDK 1.7(按照原先文档安装)</p>

<p>Maven 3.3.9(自行安装)</p>

<p>Scala 2.11(Scala 2.12仅支持jdk8,自行安装)</p>

<p> </p>

<p>编译和安装Spark</p>

<p>用spark-1.5.0-cdh5.5.1-src.tar.gz</p>

<p> 解压：tar -xzvf spark-1.5.0-cdh5.5.1-src.tar.gz  -C /itcast/</p>

<p>注意：编译前请确保已经安装JDK、Maven和Scala，maven为3.3.3及以上版本，并在/etc/profile里配置环境变量。</p>

<p> </p>

<p>编译： ./make-distribution.sh --name "hadoop2-without-hive" --tgz "-Pyarn,hadoop-provided,hadoop-2.6,parquet-provided"</p>

<p> </p>

<p>第二种编译方法：</p>

<p>Mvn命令</p>

<p>./build/mvn -Pyarn -Phadoop-2.6 -Dhadoop.version=2.6.0 -DskipTests clean package</p>

<p> </p>

<p>若编译过程出现内存不足的情况，需要在运行编译命令之前先运行：</p>

<p>export MAVEN_OPTS="-Xmx2g -XX:MaxPermSize=1024M -XX:ReservedCodeCacheSize=1024m"</p>

<p> </p>

<p>编译成功：spark-1.5.0-cdh5.5.1-bin-hadoop2-without-hive.tgz</p>

<p> </p>

<p>解压：spark-1.5.0-cdh5.5.1-bin-hadoop2-without-hive.tgz</p>

<p>tar -xzvf spark-1.5.0-cdh5.5.1-bin-hadoop2-without-hive.tgz  -C /itcast/</p>

<p> </p>

<p>配置环境变量/etc/profile</p>

<p> </p>

<p>配置Spark：</p>

<p>配置spark-env.sh、slaves和spark-defaults.conf三个文件</p>

<p>配置spark-env.sh</p>

<p>export JAVA_HOME=/usr/java/jdk1.7.0_45</p>

<p>export SCALA_HOME=/usr/scala/scala-2.12.1</p>

<p>export HADOOP_HOME=/itcast/hadoop-2.6.0</p>

<p>export HADOOP_CONF_DIR=$HADOOP_HOME/etc/hadoop</p>

<p>export YARN_CONF_DIR=$HADOOP_HOME/etc/hadoop</p>

<p>export SPARK_LAUNCH_WITH_SCALA=0</p>

<p>export SPARK_WORKER_MEMORY=1g</p>

<p>export SPARK_DRIVER_MEMORY=1g</p>

<p>export SPARK_MASTER_IP=centos1</p>

<p>export SPARK_LIBRARY_PATH=/itcast/spark-1.5/jars</p>

<p>export SPARK_MASTER_WEBUI_PORT=18080</p>

<p>export SPARK_WORKER_DIR=/itcast/spark-1.5/work</p>

<p>export SPARK_MASTER_PORT=7077</p>

<p>export SPARK_WORKER_PORT=7078</p>

<p>#Export SPARK_LOG_DIR=/itcast/spark-1.5/log</p>

<p>export SPARK_DIST_CLASSPATH=$(/itcast/hadoop-2.6.0/bin/hadoop classpath)</p>

<p>export SPARK_MASTER_IP=centos1</p>

<p>export SPARK_PID_DIR=/itcast/spark-2.0.1/pid</p>

<p>配置slaves：</p>

<p>centos1</p>

<p>centos2</p>

<p>centos3</p>

<p> </p>

<p>配置 spark-defaults.conf：</p>

<p> spark.master                     yarn-cluster</p>

<p> spark.home                       /itcast/spark-1.5</p>

<p> spark.eventLog.enabled           true</p>

<p> spark.eventLog.dir               hdfs://centos1:8020/spark-log</p>

<p> spark.serializer                 org.apache.spark.serializer.KryoSerializer</p>

<p> spark.executor.memory            1g</p>

<p> spark.driver.memory              1g</p>

<p> spark.executor.extraJavaOptions  -XX:+PrintGCDetails -Dkey=value -Dnumbers="one two three"</p>

<p> </p>

<p>spark.master指定Spark运行模式，可以是yarn-client、yarn-cluster...</p>

<p>spark.home指定SPARK_HOME路径</p>

<p>spark.eventLog.enabled需要设为true</p>

<p>spark.eventLog.dir指定路径，放在master节点的hdfs中，端口要跟hdfs设置的端口一致（默认为8020），否则会报错</p>

<p>spark.executor.memory和spark.driver.memory指定executor和dirver的内存，512m或1g，既不能太大也不能太小，因为太小运行不了，太大又会影响其他服务</p>

<p> </p>

<p>三、配置yarn</p>

<p>配置yarn-site.xml：</p>

<p>&lt;property&gt;</p>

<p>  &lt;name&gt;yarn.resourcemanager.scheduler.class&lt;/name&gt;</p>

<p>  &lt;value&gt;org.apache.hadoop.yarn.server.resourcemanager.scheduler.fair.FairScheduler&lt;/value&gt;</p>

<p>&lt;/property&gt;</p>

<p> </p>

<p>配置hive</p>

<p>添加spark依赖到hive(将spark-assembly-1.5.0-cdh5.5.1-hadoop2.6.0.jar拷贝到$HIVE_HOME/lib目录下）</p>

<p>进入SPARK_HOME</p>

<p> </p>

<p>cp spark-assembly-1.5.0-cdh5.5.1-hadoop2.6.0.jar $HIVE_HOME/lib</p>

<p> </p>

<p>配置hive-site.xml：</p>

<p>追加信息</p>

<p>&lt;property&gt;</p>

<p>  &lt;name&gt;hive.execution.engine&lt;/name&gt;</p>

<p>  &lt;value&gt;spark&lt;/value&gt;</p>

<p>&lt;/property&gt;</p>

<p>&lt;property&gt;</p>

<p>&lt;name&gt;hive.enable.spark.execution.engine&lt;/name&gt;</p>

<p>&lt;value&gt;true&lt;/value&gt;</p>

<p>&lt;description&gt;cdh版本专属&lt;/description&gt;</p>

<p>&lt;/property&gt;</p>

<p>&lt;property&gt;</p>

<p>  &lt;name&gt;spark.home&lt;/name&gt;</p>

<p>  &lt;value&gt;/itcast/spark-1.5&lt;/value&gt;</p>

<p>&lt;/property&gt;</p>

<p>&lt;property&gt;</p>

<p>  &lt;name&gt;spark.master&lt;/name&gt;</p>

<p>  &lt;value&gt;yarn-client&lt;/value&gt;</p>

<p>&lt;/property&gt;</p>

<p>&lt;property&gt;</p>

<p>  &lt;name&gt;spark.enentLog.enabled&lt;/name&gt;</p>

<p>  &lt;value&gt;true&lt;/value&gt;</p>

<p>&lt;/property&gt;</p>

<p>&lt;property&gt;</p>

<p>  &lt;name&gt;spark.enentLog.dir&lt;/name&gt;</p>

<p>  &lt;value&gt;hdfs://centos1:8020/spark-log&lt;/value&gt;</p>

<p>&lt;/property&gt;</p>

<p>&lt;property&gt;</p>

<p>  &lt;name&gt;spark.serializer&lt;/name&gt;</p>

<p>  &lt;value&gt;org.apache.spark.serializer.KryoSerializer&lt;/value&gt;</p>

<p>&lt;/property&gt;</p>

<p>&lt;property&gt;</p>

<p>  &lt;name&gt;spark.executor.memeory&lt;/name&gt;</p>

<p>  &lt;value&gt;1g&lt;/value&gt;</p>

<p>&lt;/property&gt;</p>

<p>&lt;property&gt;</p>

<p>  &lt;name&gt;spark.driver.memeory&lt;/name&gt;</p>

<p>  &lt;value&gt;1g&lt;/value&gt;</p>

<p>&lt;/property&gt;</p>

<p>&lt;property&gt;</p>

<p>  &lt;name&gt;spark.executor.extraJavaOptions&lt;/name&gt;</p>

<p>  &lt;value&gt;-XX:+PrintGCDetails -Dkey=value -Dnumbers="one two three"&lt;/value&gt;</p>

<p>&lt;/property&gt;</p>

<p> </p>

<p>复制文件到节点：</p>

<p>scp -r /itcast/spark-1.5/  centos2:/itcast/</p>

<p>scp -r /itcast/spark-1.5/  centos3:/itcast/</p>

<p> </p>

<p> </p>

<p>验证集群：</p>

<p>运行spark,看进程</p>

<p>./sbin/start-all.sh</p>

<p>提交计算Pi的任务，验证Spark集群是否能正常工作,命令如下：</p>

<p>./bin/spark-submit --class org.apache.spark.examples.SparkPi --master yarn --deploy-mode client lib/spark-examples-1.5.0-cdh5.5.1-hadoop2.6.0.jar 10</p>

<p> </p>

<p>运行hive</p>

<p>./hive --service hiveserver2 &amp;</p>

<p> </p>

<p> </p>

<p> </p>            </div>
                </div>