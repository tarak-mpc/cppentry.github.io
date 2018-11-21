---
layout:     post
title:      Hive On Spark
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-size:14px;">之前数据仓库使用的是hive新版本2.3.0,  hive以MapReduce程序跑在yarn集群上。</span></p>
<p><span style="font-size:14px;">hive shell常提示：</span></p>
<p><span style="font-size:14px;">Hive-on-MR is deprecated in Hive 2 and may not be available in the future versions. Consider using a different execution engine (i.e. spark, tez) or using Hive 1.X releases.    </span></p>
<p><span style="font-size:14px;">意思是或者使用Hive1.x/ 或者给hive换个引擎。正好最近需要搭建Spark集群，正好把hive的执行引擎给换掉。</span><br></p>
<p><span style="font-size:14px;">hive 官方文档：</span><span style="font-size:14px;color:rgb(0,56,132);"><a href="https://cwiki.apache.org/confluence/display/Hive/Hive+on+Spark:+Getting+Started" rel="nofollow" style="font-size:14px;">https://cwiki.apache.org/confluence/display/Hive/Hive+on+Spark:+Getting+Started</a></span></p>
<p>网上查说坑很多，但其实文档写的很清楚。主要注意以下两点：</p>
<p>1）默认spark预发布版本里有hive的jar包(大概是SparkSQL访问Hive用的)，要想使用hive on spark 就需要去掉这些spark访问hive 的jar包。 所以推荐使用spark源码自行编译spark.</p>
<p>2) 一定要注意hive版本与spark版本的兼容性。 hive源码的pom.xml 里 <span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;">spark.version 定义了hive支持的spark版本。</span></p>
<p><span style="color:rgb(51,51,51);font-family:Arial;"><span style="font-size:14px;">hive2.3.0支持 spark 2.0.0， 很坑，用spark2.2.0试过，不好使。</span></span></p>
<p><span style="background-color:rgb(255,204,0);">详述安装配置过程：</span></p>
<p><span style="font-size:14px;">java 1.8.0</span></p>
<p><span style="font-size:14px;">hadoop 2.7.4</span></p>
<p><span style="font-size:14px;">hive 2.3.0</span></p>
<p><span style="font-size:14px;">spark 2.0.2  注：spark 使用yarn进行调度，官方文档有一句：<span style="font-family:Arial;color:rgb(51,51,51);background-color:rgb(255,255,255);">Hive on Spark supports </span><a href="http://spark.apache.org/docs/latest/running-on-yarn.html" rel="nofollow"><span style="font-family:Arial;color:rgb(53,114,176);">Spark
 on YARN</span></a><span style="font-family:Arial;color:rgb(51,51,51);background-color:rgb(255,255,255);"> mode as default.</span></span></p>
<p><span style="font-size:14px;">一) <span>安装JDK/maven/scala/</span></span></p>
<p><span style="font-size:14px;color:rgb(63,63,63);">安装maven是为了编译spark。</span></p>
<p><span style="font-size:14px;color:rgb(63,63,63);">下载scala版本2.11.12 ，spark从2.X版本使用scala的2.11.X版本</span></p>
<p><span style="font-size:14px;color:rgb(63,63,63);">安装scala:</span></p>
<div style="line-height:1.75;"><span style="font-size:14px;">rpm -Uvh scala-2.11.12.rpm</span></div>
<div style="line-height:1.75;"><span style="font-size:14px;"><br></span></div>
<div style="line-height:1.75;"><span style="font-size:14px;">二）spark编译</span></div>
<div style="line-height:1.75;"><span style="font-size:14px;">tar -zxvf spark-2.0.2.tgz -C /root/</span></div>
<div style="line-height:1.75;"><span style="font-size:14px;">cd 到spark的目录执行</span></div>
<div style="line-height:1.75;"><span style="font-size:12px;">./dev/make-distribution.sh --name "hadoop2-without-hive" --tgz "-Pyarn,hadoop-provided,hadoop-2.7,parquet-provided"</span></div>
<p></p>
<p><span style="font-size:14px;">编译运行大概几十分钟，最后绿色success，编译成功。</span></p>
<p><span style="font-size:14px;">三）将编译好的spark-2.0.2-bin-hadoop2-without-hive.tgz tar -zxvf 到目录</span></p>
<p><span style="font-size:14px;">在/etc/profile里配置好 $SPARK_HOME环境变量，并 . /etc/profile使环境变量生效。</span></p>
<p><span style="font-size:14px;">接下来配置hive/spark/yarn</span></p>
<p></p>
<div style="line-height:1.75;"><span style="font-size:14px;">1) 配置hive</span></div>
<div style="text-indent:28px;line-height:1.75;"><span style="font-size:14px;">1.拷贝spark下的jar包到hive的lib下</span></div>
<ul><li style="text-align:left;line-height:1.75;list-style-position:inside;list-style-type:disc;font-family:'Microsoft YaHei', STXihei;color:rgb(0,0,0);">
<span style="font-size:14px;">cp scala-library-2.11.8.jar /usr/share/hive-2.3.0/lib/</span></li><li style="text-align:left;line-height:1.75;list-style-position:inside;list-style-type:disc;font-family:'Microsoft YaHei', STXihei;">
<span style="font-size:14px;"><span style="color:#000000;">cp spark-core_2.11-2.0.2.jar /usr/share/hive-2.3.0/lib/</span></span></li><li style="text-align:left;line-height:1.75;list-style-position:inside;list-style-type:disc;font-family:'Microsoft YaHei', STXihei;color:rgb(0,0,0);">
<span style="font-size:14px;">cp spark-network-common_2.11-2.0.2.jar /usr/share/hive-2.3.0/lib/</span></li></ul><div style="text-indent:28px;line-height:1.75;"><span style="font-size:14px;color:#000000;">2.在hive的conf下建立文件<span style="background-color:rgb(255,255,255);">spark-defaults.conf</span></span></div>
<div style="text-indent:28px;line-height:1.75;"><span style="font-size:14px;color:#000000;"><span style="background-color:rgb(255,255,255);"><br></span></span></div>
<div style="line-height:1.5;text-indent:28px;"><span style="font-size:14px;color:#000000;"><span style="font-family:'Courier New';background-color:rgb(255,255,255);">set </span><span style="font-family:'Courier New';background-color:rgb(255,255,255);">hive.execution.engine</span><span style="font-family:'Courier New';background-color:rgb(255,255,255);">=</span><span style="font-family:'Courier New';background-color:rgb(255,255,255);">spark</span><span style="font-family:'Courier New';background-color:rgb(255,255,255);">;  </span></span></div>
<div style="line-height:1.5;text-indent:28px;"><span style="font-size:14px;color:#000000;"><span style="font-family:'Courier New';background-color:rgb(248,248,248);">set </span><span style="font-family:'Courier New';background-color:rgb(248,248,248);">spark.master</span><span style="font-family:'Courier New';background-color:rgb(248,248,248);">=</span><span style="font-family:'Courier New';background-color:rgb(248,248,248);">yarn</span><span style="font-family:'Courier New';background-color:rgb(248,248,248);">;  </span></span></div>
<div style="line-height:1.5;text-indent:28px;"><span style="font-size:14px;color:#000000;"><span style="font-family:'Courier New';background-color:rgb(255,255,255);">set </span><span style="font-family:'Courier New';background-color:rgb(255,255,255);">spark.submit.deployMode</span><span style="font-family:'Courier New';background-color:rgb(255,255,255);">=</span><span style="font-family:'Courier New';background-color:rgb(255,255,255);">client</span><span style="font-family:'Courier New';background-color:rgb(255,255,255);">;  </span></span></div>
<div style="line-height:1.5;text-indent:28px;"><span style="font-size:14px;color:#000000;"><span style="font-family:'Courier New';background-color:rgb(248,248,248);">set </span><span style="font-family:'Courier New';background-color:rgb(248,248,248);">spark.eventLog.enabled</span><span style="font-family:'Courier New';background-color:rgb(248,248,248);">=</span><span style="font-family:'Courier New';background-color:rgb(248,248,248);">true</span><span style="font-family:'Courier New';background-color:rgb(248,248,248);">;  </span></span></div>
<div style="line-height:1.5;text-indent:28px;"><span style="color:#000000;"><span style="font-size:14px;font-family:'Courier New';background-color:rgb(255,255,255);">set </span><span style="font-size:14px;font-family:'Courier New';background-color:rgb(255,255,255);">spark.eventLog.dir</span><span style="font-size:14px;font-family:'Courier New';background-color:rgb(255,255,255);">=</span><span style="font-size:14px;font-family:'Courier New';background-color:rgb(255,255,255);">hdfs</span><span style="font-family:'Courier New';background-color:rgb(255,255,255);"><span style="font-size:14px;">://bi/spark-logs;  
</span><span style="font-size:12px;">// 这个是HA的hdfs地址，在hadoop的core-site.xml里可以找到。</span></span></span></div>
<div style="line-height:1.5;text-indent:28px;"><span style="font-size:14px;color:#000000;"><span style="font-family:'Courier New';background-color:rgb(248,248,248);">set </span><span style="font-family:'Courier New';background-color:rgb(248,248,248);">spark.executor.memory</span><span style="font-family:'Courier New';background-color:rgb(248,248,248);">=</span><span style="font-family:'Courier New';background-color:rgb(248,248,248);">1024m</span><span style="font-family:'Courier New';background-color:rgb(248,248,248);">;  </span></span></div>
<div style="line-height:1.5;text-indent:28px;"><span style="font-size:14px;color:#000000;"><span style="font-family:'Courier New';background-color:rgb(255,255,255);">set </span><span style="font-family:'Courier New';background-color:rgb(255,255,255);">spark.serializer</span><span style="font-family:'Courier New';background-color:rgb(255,255,255);">=</span><span style="font-family:'Courier New';background-color:rgb(255,255,255);">org</span><span style="font-family:'Courier New';background-color:rgb(255,255,255);">.apache.spark.serializer.KryoSerializer;</span></span></div>
<div style="line-height:1.75;"><span style="font-size:14px;"><br></span></div>
<div style="text-indent:28px;line-height:1.75;"><span style="font-size:14px;">3. 修改hive-site.xml,增加</span></div>
<div style="text-indent:28px;line-height:1.75;"><span style="font-size:14px;"><br></span></div>
<div style="text-indent:28px;line-height:1.75;"><span style="font-family:Arial;color:rgb(51,51,51);background-color:rgb(255,255,255);"><span style="font-size:14px;">目的：允许yarn缓存spark依赖的一些jar包到各个nodeManager节点上，避免每次应用运行频繁分发。</span></span></div>
<div style="text-indent:28px;line-height:1.75;"><span style="font-family:Arial;color:rgb(51,51,51);background-color:rgb(255,255,255);"><span style="font-size:14px;">Allow Yarn to cache necessary spark dependency jars on nodes so that it
 does not need to be distributed each time when an application runs.</span></span></div>
<div style="text-indent:28px;line-height:1.75;"><span style="font-size:14px;"><span style="font-family:Arial;color:rgb(51,51,51);background-color:rgb(255,255,255);">upload all jars in $SPARK_HOME/jars to hdfs file(for example:</span><span style="font-family:'Courier New';background-color:rgb(255,255,255);">hdfs:</span><span style="font-family:'Courier New';color:rgb(0,130,0);background-color:rgb(255,255,255);">//</span><span style="font-family:'Courier New';background-color:rgb(255,255,255);">bi</span><span style="font-family:'Courier New';color:rgb(0,130,0);background-color:rgb(255,255,255);">/spark-jars/</span><span style="font-family:Arial;color:rgb(57,57,57);">）</span><span style="font-family:Arial;color:rgb(51,51,51);background-color:rgb(255,255,255);"> </span></span></div>
<div style="text-indent:28px;line-height:1.75;"><span style="font-size:14px;"><br></span></div>
<div style="text-indent:28px;line-height:1.75;"><span style="font-size:14px;">1)hdfs dfs -put ../jars /spark-jars //上传spark依赖的jars到hdfs的spark-jars目录里。</span></div>
<div style="text-indent:28px;line-height:1.75;"><span style="font-size:14px;">2)修改hive-site.xml,增加</span></div>
<div style="text-indent:28px;line-height:1.75;"><span style="font-family:'Courier New';background-color:rgb(255,255,255);"><span style="font-size:14px;">&lt;property&gt;</span></span></div>
<div style="line-height:1.42857;"><span style="font-size:14px;"><span style="font-family:'Courier New';color:rgb(51,51,51);background-color:rgb(255,255,255);">  <span>
</span></span><span style="font-family:'Courier New';background-color:rgb(255,255,255);">&lt;name&gt;spark.yarn.jars&lt;/name&gt;</span></span></div>
<div style="line-height:1.42857;"><span style="font-size:14px;"><span style="font-family:'Courier New';color:rgb(51,51,51);background-color:rgb(255,255,255);">  <span>
</span></span><span style="font-family:'Courier New';background-color:rgb(255,255,255);">&lt;value&gt;hdfs:</span><span style="font-family:'Courier New';color:rgb(0,130,0);background-color:rgb(255,255,255);">//</span><span style="font-family:'Courier New';background-color:rgb(255,255,255);">bi</span><span style="font-family:'Courier New';color:rgb(0,130,0);background-color:rgb(255,255,255);">/spark-jars/*&lt;/value&gt;</span></span></div>
<div style="line-height:1.42857;text-indent:28px;"><span style="font-family:'Courier New';background-color:rgb(255,255,255);"><span style="font-size:14px;">&lt;/property&gt;</span></span></div>
<div style="line-height:1.42857;text-indent:28px;"><span style="font-size:14px;"><br></span></div>
<div style="line-height:1.75;"><span style="font-size:14px;">2)配置spark</span></div>
<div style="line-height:1.75;"><span style="font-size:14px;">cp spark-env.sh.template spark-env.sh</span></div>
<div style="line-height:1.75;"><span style="font-size:14px;">配置<span style="font-family:Verdana;color:rgb(51,51,51);background-color:rgb(255,255,255);">spark-env.sh</span></span></div>
<div style="line-height:1.75;"><span style="font-size:14px;"><br></span></div>
<div style="line-height:1.75;"><span style="font-size:14px;">export SPARK_DIST_CLASSPATH=$(/usr/share/hadoop-HA/hadoop-2.7.4/bin/hadoop classpath)</span></div>
<div style="line-height:1.75;"><span style="font-size:14px;">export HADOOP_HOME=/usr/share/hadoop-HA/hadoop-2.7.4</span></div>
<div style="line-height:1.75;"><span style="font-size:14px;">export HADOOP_CONF_DIR=${HADOOP_HOME}/etc/hadoop/</span></div>
<div style="line-height:1.75;"><span style="font-size:14px;">export YARN_CONF_DIR=$HADOOP_HOME/etc/hadoop/</span></div>
<span style="font-size:14px;"><br></span>
<p></p>
<p><span style="font-size:14px;">四）测试</span></p>
<p><span style="font-size:14px;">进入hive shell</span></p>
<p><span style="font-size:14px;">create table test2 as select * from dw_mall1_order_info;<br></span></p>
<p><span style="font-size:14px;">然后进入到yarn调度页面，发现job 类型已经变成spark. 之前是MAPREDUCE.</span></p>
<p><span style="font-size:14px;">在shell里可以使用以下命令切换hive引擎：</span></p>
<p><span style="font-size:14px;">set hive.execution.engine=spark;<br></span></p>
<p><span style="font-size:14px;">set hive.execution.engine=mr;</span><br></p>
<p><br></p>
<p><br></p>
<p><br></p>
            </div>
                </div>