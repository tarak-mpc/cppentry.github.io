---
layout:     post
title:      spark-sql部署实现与Hive交互
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p style="color:rgb(51,51,51);font-size:14px;font-family:Arial, sans-serif;line-height:18.1818px;">
spark-sql部署</p>
<p style="color:rgb(51,51,51);font-size:14px;font-family:Arial, sans-serif;line-height:18.1818px;">
版本</p>
<p style="color:rgb(51,51,51);font-size:14px;font-family:Arial, sans-serif;line-height:18.1818px;">
Hadoop-2.5.0-cdh5.3.2 </p>
<p style="color:rgb(51,51,51);font-size:14px;font-family:Arial, sans-serif;line-height:18.1818px;">
Hive-0.13.1-cdh5.3.2</p>
<p style="color:rgb(51,51,51);font-size:14px;font-family:Arial, sans-serif;line-height:18.1818px;">
Spark-1.5.1</p>
<p style="color:rgb(51,51,51);font-size:14px;font-family:Arial, sans-serif;line-height:18.1818px;">
以CNSH001节点为例</p>
<p style="color:rgb(51,51,51);font-size:14px;font-family:Arial, sans-serif;line-height:18.1818px;">
spark master在<span style="line-height:18.1818px;">CNSH001上：<a href="http://blog.csdn.net/developerinit/article/details/50032913" rel="nofollow" style="color:rgb(59,115,175);text-decoration:none;line-height:18.1818px;">spark://</a><a href="http://cnsh045663.app.paft.com.cn/" rel="nofollow" class="external-link" style="color:rgb(59,115,175);text-decoration:none;line-height:18.1818px;"><span style="color:rgb(51,51,51);line-height:18.1818px;">CNSH001</span></a>:7077</span></p>
<p style="color:rgb(51,51,51);font-size:14px;font-family:Arial, sans-serif;line-height:18.1818px;">
<span style="line-height:18.1818px;">spark HistoryServer在<span style="line-height:18.1818px;">CNSH001上：<a href="http://cnsh045663.app.paft.com.cn/" rel="nofollow" class="external-link" style="color:rgb(59,115,175);text-decoration:none;line-height:18.1818px;"><span style="color:rgb(51,51,51);line-height:18.1818px;">CNSH001</span></a><span style="line-height:18.1818px;">:8032</span></span></span></p>
<p style="color:rgb(51,51,51);font-size:14px;font-family:Arial, sans-serif;line-height:18.1818px;">
<span style="line-height:18.1818px;"><span style="line-height:18.1818px;"><span style="line-height:18.1818px;">spark eventLog在hdfs上：<a href="http://blog.csdn.net/developerinit/article/details/50032913" rel="nofollow" style="color:rgb(59,115,175);text-decoration:none;line-height:18.1818px;">hdfs://testenv/spark/eventLog</a></span></span></span></p>
<h2 id="SparkSQLOnHive部署-分步指南" style="color:rgb(51,51,51);font-size:20px;font-weight:normal;line-height:1.5;border-bottom-color:rgb(204,204,204);font-family:Arial, sans-serif;">
分步指南</h2>
<p style="color:rgb(51,51,51);font-size:14px;font-family:Arial, sans-serif;line-height:18.1818px;">
  </p>
<p style="color:rgb(51,51,51);font-size:14px;font-family:Arial, sans-serif;line-height:18.1818px;">
1. 拷贝$HIVE_HOME/conf/hive-site.xml, hive-log4j.properties 到 $SPARK_HOME/conf/目录</p>
<p style="color:rgb(51,51,51);font-size:14px;font-family:Arial, sans-serif;line-height:18.1818px;">
  </p>
<p style="color:rgb(51,51,51);font-size:14px;font-family:Arial, sans-serif;line-height:18.1818px;">
2.修改spark-defaults.conf</p>
<p style="color:rgb(51,51,51);font-size:14px;font-family:Arial, sans-serif;line-height:18.1818px;">
</p>
<div class="dp-highlighter bg_plain" style="font-family:Consolas, 'Courier New', Courier, mono, serif;overflow:auto;color:rgb(51,51,51);line-height:26px;background-color:rgb(231,229,220);">
<div class="bar">
<div class="tools" style="font-size:9px;line-height:normal;font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;color:#C0C0C0;border-left-width:3px;border-left-style:solid;border-left-color:rgb(108,226,108);background-color:rgb(248,248,248);">
<strong>[plain]</strong> <a href="http://blog.csdn.net/developerinit/article/details/50032913#" rel="nofollow" class="ViewSource" title="view plain" style="color:rgb(160,160,160);text-decoration:none;border:none;font-size:9px;display:inline-block;width:16px;text-indent:-2000px;background-color:inherit;">view
 plain</a><span class="tracking-ad"> <a href="http://blog.csdn.net/developerinit/article/details/50032913#" rel="nofollow" class="CopyToClipboard" title="copy" style="color:rgb(160,160,160);text-decoration:none;border:none;font-size:9px;display:inline-block;width:16px;text-indent:-2000px;background-color:inherit;">copy</a></span>
<div style="width:18px;z-index:99;">
</div>
<span class="tracking-ad"></span></div>
</div>
<ol start="1" style="border:none;color:rgb(92,92,92);background-color:rgb(255,255,255);"><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span style="border:none;background-color:inherit;">spark.eventLog.enabled true  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">spark.eventLog.dir hdfs://testenv/spark/eventLog  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">spark.eventLog.compress true  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">spark.yarn.historyServer.address=CNSH001:8032  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">spark.sql.hive.metastore.version=0.13.1  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">spark.port.maxRetries=100  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">spark.sql.hive.metastore.jars=/opt/apps/hadoop/share/hadoop/mapreduce/*:/opt/apps/hadoop/share/hadoop/mapreduce/lib/*:/opt/apps/hadoop/share/hadoop/common/*:/opt/apps/hadoop/share/hadoop/common/lib/*:/opt/apps/hadoop/share/hadoop/hdfs/*  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">:/opt/apps/hadoop/share/hadoop/hdfs/lib/*:/opt/apps/hadoop/share/hadoop/yarn/*:/opt/apps/hadoop/share/hadoop/yarn/lib/*:/opt/apps/hive/lib/*:/opt/apps/spark/lib/*  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">spark.driver.extraLibraryPath=/opt/apps/hadoop/share/hadoop/mapreduce/*:/opt/apps/hadoop/share/hadoop/mapreduce/lib/*:/opt/apps/hadoop/share/hadoop/common/*:/opt/apps/hadoop/share/hadoop/common/lib/*:/opt/apps/hadoop/share/hadoop/hdfs/*  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">:/opt/apps/hadoop/share/hadoop/hdfs/lib/*:/opt/apps/hadoop/share/hadoop/yarn/*:/opt/apps/hadoop/share/hadoop/yarn/lib/*:/opt/apps/hive/lib/*:/opt/apps/spark/lib/*  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">spark.executor.extraLibraryPath=/opt/apps/hadoop/share/hadoop/mapreduce/*:/opt/apps/hadoop/share/hadoop/mapreduce/lib/*:/opt/apps/hadoop/share/hadoop/common/*:/opt/apps/hadoop/share/hadoop/common/lib/*:/opt/apps/hadoop/share/hadoop/hdfs  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">/*:/opt/apps/hadoop/share/hadoop/hdfs/lib/*:/opt/apps/hadoop/share/hadoop/yarn/*:/opt/apps/hadoop/share/hadoop/yarn/lib/*:/opt/apps/hive/lib/*:/opt/apps/spark/lib/*  </span></li></ol></div>
<br style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;"><br style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;"><p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
</p>
<p style="color:rgb(51,51,51);font-size:14px;font-family:Arial, sans-serif;line-height:18.1818px;">
 </p>
<p style="color:rgb(51,51,51);font-size:14px;font-family:Arial, sans-serif;line-height:18.1818px;">
3.修改spark-env.sh</p>
<p style="color:rgb(51,51,51);font-size:14px;font-family:Arial, sans-serif;line-height:18.1818px;">
</p>
<div class="dp-highlighter bg_plain" style="font-family:Consolas, 'Courier New', Courier, mono, serif;overflow:auto;color:rgb(51,51,51);line-height:26px;background-color:rgb(231,229,220);">
<div class="bar">
<div class="tools" style="font-size:9px;line-height:normal;font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;color:#C0C0C0;border-left-width:3px;border-left-style:solid;border-left-color:rgb(108,226,108);background-color:rgb(248,248,248);">
<strong>[plain]</strong> <a href="http://blog.csdn.net/developerinit/article/details/50032913#" rel="nofollow" class="ViewSource" title="view plain" style="color:rgb(160,160,160);text-decoration:none;border:none;font-size:9px;display:inline-block;width:16px;text-indent:-2000px;background-color:inherit;">view
 plain</a><span class="tracking-ad"> <a href="http://blog.csdn.net/developerinit/article/details/50032913#" rel="nofollow" class="CopyToClipboard" title="copy" style="color:rgb(160,160,160);text-decoration:none;border:none;font-size:9px;display:inline-block;width:16px;text-indent:-2000px;background-color:inherit;">copy</a></span>
<div style="width:18px;z-index:99;">
</div>
<span class="tracking-ad"></span></div>
</div>
<ol start="1" style="border:none;color:rgb(92,92,92);background-color:rgb(255,255,255);"><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span style="border:none;background-color:inherit;">#set Hadoop path  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">export HDFS_YARN_LOGS_DIR=/data1/hadooplogs  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">export HADOOP_PREFIX=/opt/apps/hadoop  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">export HADOOP_HOME=$HADOOP_PREFIX  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">export HADOOP_COMMON_HOME=$HADOOP_HOME  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">export HADOOP_HDFS_HOME=$HADOOP_HOME  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">export HADOOP_MAPRED_HOME=$HADOOP_HOME  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">export HADOOP_MAPRED_PID_DIR=$HADOOP_HOME/pids  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">export HADOOP_YARN_HOME=$HADOOP_HOME  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">export HADOOP_CONF_DIR=$HADOOP_HOME/etc/hadoop  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">export HDFS_CONF_DIR=$HADOOP_HOME/etc/hadoop  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">export HADOOP_LOG_DIR=$HDFS_YARN_LOGS_DIR/logs  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">export HADOOP_PID_DIR=$HADOOP_HOME/pids  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">export HADOOP_SECURE_DN_PID_DIR=$HADOOP_PID_DIR  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">export YARN_HOME=$HADOOP_HOME  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">export YARN_CONF_DIR=$HADOOP_HOME/etc/hadoop  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">export YARN_LOG_DIR=$HDFS_YARN_LOGS_DIR/logs  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">export YARN_PID_DIR=$HADOOP_HOME/pids  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">export PATH=$PATH:$HADOOP_HOME/bin:$HADOOP_HOME/sbin  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">export HADOOP_CLASSPATH=$HADOOP_CLASSPATH:$HADOOP_CONF_DIR:$HADOOP_MAPRED_HOME/share/hadoop/mapreduce/*:$HADOOP_MAPRED_HOME/share/hadoop/mapreduce/lib/*:$HADOOP_COMMON_HOME/share/hadoop/common/*:$HADOOP_COMMON_HOME/share/hadoop/common/lib/*:$HADOOP_HDFS_HOME/share/hadoop/hdfs/*:$HADOOP_HDFS_HOME/share/hadoop/hdfs/lib/*:$HADOOP_YARN_HOME/share/hadoop/yarn/*:$HADOOP_YARN_HOME/share/hadoop/yarn/lib/*  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">export CLASSPATH=$HADOOP_CLASSPATH:$CLASSPATH  </span></li></ol></div>
<div class="dp-highlighter bg_plain" style="font-family:Consolas, 'Courier New', Courier, mono, serif;overflow:auto;color:rgb(51,51,51);line-height:26px;background-color:rgb(231,229,220);">
<div class="bar">
<div class="tools" style="font-size:9px;line-height:normal;font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;color:#C0C0C0;border-left-width:3px;border-left-style:solid;border-left-color:rgb(108,226,108);background-color:rgb(248,248,248);">
<strong>[plain]</strong> <a href="http://blog.csdn.net/developerinit/article/details/50032913#" rel="nofollow" class="ViewSource" title="view plain" style="color:rgb(160,160,160);text-decoration:none;border:none;font-size:9px;display:inline-block;width:16px;text-indent:-2000px;background-color:inherit;">view
 plain</a><span class="tracking-ad"> <a href="http://blog.csdn.net/developerinit/article/details/50032913#" rel="nofollow" class="CopyToClipboard" title="copy" style="color:rgb(160,160,160);text-decoration:none;border:none;font-size:9px;display:inline-block;width:16px;text-indent:-2000px;background-color:inherit;">copy</a></span>
<div style="width:18px;z-index:99;">
</div>
<span class="tracking-ad"></span></div>
</div>
<ol start="1" style="border:none;color:rgb(92,92,92);background-color:rgb(255,255,255);"><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span style="border:none;background-color:inherit;">### sparkSQL and hive  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">export HIVE_HOME=/opt/apps/hive  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">export SPARK_CLASSPATH=$SPARK_HOME/lib:$HIVE_HOME/lib:$HADOOP_CLASSPATH  </span></li></ol></div>
<br style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;"><p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
</p>
<p style="color:rgb(51,51,51);font-size:14px;font-family:Arial, sans-serif;line-height:18.1818px;">
4.[ERROR] Terminal initialization failed; falling back to unsupported<br><span style="line-height:18.1818px;">解决<span style="line-height:18.1818px;">问题</span></span>java.lang.IncompatibleClassChangeError: Found class jline.Terminal, but interface was expected</p>
<p style="color:rgb(51,51,51);font-size:14px;font-family:Arial, sans-serif;line-height:18.1818px;">
需要<span style="line-height:18.1818px;">删除/opt/apps/hadoop/share/hadoop/yarn/lib/jline-0.9.94.jar文件，在/etc/profile中配置export HADOOP_USER_CLASSPATH_FIRST=true 并source操作</span></p>
<p style="color:rgb(51,51,51);font-size:14px;font-family:Arial, sans-serif;line-height:18.1818px;">
见：<a href="https://cwiki.apache.org/confluence/display/Hive/Hive+on+Spark:+Getting+Started" rel="nofollow" class="external-link" style="color:rgb(59,115,175);text-decoration:none;">https://cwiki.apache.org/confluence/display/Hive/Hive+on+Spark:+Getting+Started</a></p>
<p style="color:rgb(51,51,51);font-size:14px;font-family:Arial, sans-serif;line-height:18.1818px;">
 </p>
<p style="color:rgb(51,51,51);font-size:14px;font-family:Arial, sans-serif;line-height:18.1818px;">
5.使用spark-shell</p>
<p style="color:rgb(51,51,51);font-size:14px;font-family:Arial, sans-serif;line-height:18.1818px;">
</p>
<div class="dp-highlighter bg_plain" style="font-family:Consolas, 'Courier New', Courier, mono, serif;overflow:auto;color:rgb(51,51,51);line-height:26px;background-color:rgb(231,229,220);">
<div class="bar">
<div class="tools" style="font-size:9px;line-height:normal;font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;color:#C0C0C0;border-left-width:3px;border-left-style:solid;border-left-color:rgb(108,226,108);background-color:rgb(248,248,248);">
<strong>[plain]</strong> <a href="http://blog.csdn.net/developerinit/article/details/50032913#" rel="nofollow" class="ViewSource" title="view plain" style="color:rgb(160,160,160);text-decoration:none;border:none;font-size:9px;display:inline-block;width:16px;text-indent:-2000px;background-color:inherit;">view
 plain</a><span class="tracking-ad"> <a href="http://blog.csdn.net/developerinit/article/details/50032913#" rel="nofollow" class="CopyToClipboard" title="copy" style="color:rgb(160,160,160);text-decoration:none;border:none;font-size:9px;display:inline-block;width:16px;text-indent:-2000px;background-color:inherit;">copy</a></span>
<div style="width:18px;z-index:99;">
</div>
<span class="tracking-ad"></span></div>
</div>
<ol start="1" style="border:none;color:rgb(92,92,92);background-color:rgb(255,255,255);"><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span style="border:none;background-color:inherit;">val sqlContext = new org.apache.spark.sql.hive.HiveContext(sc)  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">sqlContext.sql("select * from test limit 2 ").collect().foreach(println)  </span></li></ol></div>
<br style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;"><p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
</p>
<p style="color:rgb(51,51,51);font-size:14px;font-family:Arial, sans-serif;line-height:18.1818px;">
6.使用spark-sql启动local模式</p>
<p style="color:rgb(51,51,51);font-size:14px;font-family:Arial, sans-serif;line-height:18.1818px;">
里面可以直接输入HiveQL语句执行</p>
<p style="color:rgb(51,51,51);font-size:14px;font-family:Arial, sans-serif;line-height:18.1818px;">
<br></p>
<p style="color:rgb(51,51,51);font-size:14px;font-family:Arial, sans-serif;line-height:18.1818px;">
7. 启动standalone集群模式的spark-sql </p>
<p style="color:rgb(51,51,51);font-size:14px;font-family:Arial, sans-serif;line-height:18.1818px;">
</p>
<div class="dp-highlighter bg_plain" style="font-family:Consolas, 'Courier New', Courier, mono, serif;overflow:auto;color:rgb(51,51,51);line-height:26px;background-color:rgb(231,229,220);">
<div class="bar">
<div class="tools" style="font-size:9px;line-height:normal;font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;color:#C0C0C0;border-left-width:3px;border-left-style:solid;border-left-color:rgb(108,226,108);background-color:rgb(248,248,248);">
<strong>[plain]</strong> <a href="http://blog.csdn.net/developerinit/article/details/50032913#" rel="nofollow" class="ViewSource" title="view plain" style="color:rgb(160,160,160);text-decoration:none;border:none;font-size:9px;display:inline-block;width:16px;text-indent:-2000px;background-color:inherit;">view
 plain</a><span class="tracking-ad"> <a href="http://blog.csdn.net/developerinit/article/details/50032913#" rel="nofollow" class="CopyToClipboard" title="copy" style="color:rgb(160,160,160);text-decoration:none;border:none;font-size:9px;display:inline-block;width:16px;text-indent:-2000px;background-color:inherit;">copy</a></span>
<div style="width:18px;z-index:99;">
</div>
<span class="tracking-ad"></span></div>
</div>
<ol start="1" style="border:none;color:rgb(92,92,92);background-color:rgb(255,255,255);"><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span style="border:none;background-color:inherit;">spark-sql --master spark://CNSH001:7077    </span></span></li></ol></div>
<br style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;"><br style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;"><p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
</p>
<p style="color:rgb(51,51,51);font-size:14px;font-family:Arial, sans-serif;line-height:18.1818px;">
（该主机地址应该对应spark的ALIVE节点）</p>
<p style="color:rgb(51,51,51);font-size:14px;font-family:Arial, sans-serif;line-height:18.1818px;">
<br></p>
<p style="color:rgb(51,51,51);font-size:14px;font-family:Arial, sans-serif;line-height:18.1818px;">
8.启动spark on yarn模式的spark-sql</p>
<p style="color:rgb(51,51,51);font-size:14px;font-family:Arial, sans-serif;line-height:18.1818px;">
</p>
<div class="dp-highlighter bg_plain" style="font-family:Consolas, 'Courier New', Courier, mono, serif;overflow:auto;color:rgb(51,51,51);line-height:26px;background-color:rgb(231,229,220);">
<div class="bar">
<div class="tools" style="font-size:9px;line-height:normal;font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;color:#C0C0C0;border-left-width:3px;border-left-style:solid;border-left-color:rgb(108,226,108);background-color:rgb(248,248,248);">
<strong>[plain]</strong> <a href="http://blog.csdn.net/developerinit/article/details/50032913#" rel="nofollow" class="ViewSource" title="view plain" style="color:rgb(160,160,160);text-decoration:none;border:none;font-size:9px;display:inline-block;width:16px;text-indent:-2000px;background-color:inherit;">view
 plain</a><span class="tracking-ad"> <a href="http://blog.csdn.net/developerinit/article/details/50032913#" rel="nofollow" class="CopyToClipboard" title="copy" style="color:rgb(160,160,160);text-decoration:none;border:none;font-size:9px;display:inline-block;width:16px;text-indent:-2000px;background-color:inherit;">copy</a></span>
<div style="width:18px;z-index:99;">
</div>
<span class="tracking-ad"></span></div>
</div>
<ol start="1" style="border:none;color:rgb(92,92,92);background-color:rgb(255,255,255);"><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span style="border:none;background-color:inherit;">spark-sql --master yarn-client   </span></span></li></ol></div>
<div class="dp-highlighter bg_plain" style="font-family:Consolas, 'Courier New', Courier, mono, serif;overflow:auto;color:rgb(51,51,51);line-height:26px;background-color:rgb(231,229,220);">
<div class="bar">
<div class="tools" style="font-size:9px;line-height:normal;font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;color:#C0C0C0;border-left-width:3px;border-left-style:solid;border-left-color:rgb(108,226,108);background-color:rgb(248,248,248);">
<strong>[plain]</strong> <a href="http://blog.csdn.net/developerinit/article/details/50032913#" rel="nofollow" class="ViewSource" title="view plain" style="color:rgb(160,160,160);text-decoration:none;border:none;font-size:9px;display:inline-block;width:16px;text-indent:-2000px;background-color:inherit;">view
 plain</a><span class="tracking-ad"> <a href="http://blog.csdn.net/developerinit/article/details/50032913#" rel="nofollow" class="CopyToClipboard" title="copy" style="color:rgb(160,160,160);text-decoration:none;border:none;font-size:9px;display:inline-block;width:16px;text-indent:-2000px;background-color:inherit;">copy</a></span>
<div style="width:18px;z-index:99;">
</div>
<span class="tracking-ad"></span></div>
</div>
<ol start="1" style="border:none;color:rgb(92,92,92);background-color:rgb(255,255,255);"><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span style="border:none;background-color:inherit;">或者   </span></span></li></ol></div>
<div class="dp-highlighter bg_plain" style="font-family:Consolas, 'Courier New', Courier, mono, serif;overflow:auto;color:rgb(51,51,51);line-height:26px;background-color:rgb(231,229,220);">
<div class="bar">
<div class="tools" style="font-size:9px;line-height:normal;font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;color:#C0C0C0;border-left-width:3px;border-left-style:solid;border-left-color:rgb(108,226,108);background-color:rgb(248,248,248);">
<strong>[plain]</strong> <a href="http://blog.csdn.net/developerinit/article/details/50032913#" rel="nofollow" class="ViewSource" title="view plain" style="color:rgb(160,160,160);text-decoration:none;border:none;font-size:9px;display:inline-block;width:16px;text-indent:-2000px;background-color:inherit;">view
 plain</a><span class="tracking-ad"> <a href="http://blog.csdn.net/developerinit/article/details/50032913#" rel="nofollow" class="CopyToClipboard" title="copy" style="color:rgb(160,160,160);text-decoration:none;border:none;font-size:9px;display:inline-block;width:16px;text-indent:-2000px;background-color:inherit;">copy</a></span>
<div style="width:18px;z-index:99;">
</div>
<span class="tracking-ad"></span></div>
</div>
<ol start="1" style="border:none;color:rgb(92,92,92);background-color:rgb(255,255,255);"><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span style="border:none;background-color:inherit;">spark-sql --master yarn-cluster(暂时还不支持)  </span></span></li><li></ol></div>
            </div>
                </div>