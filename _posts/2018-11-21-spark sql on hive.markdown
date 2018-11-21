---
layout:     post
title:      spark sql on hive
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/vfgbv/article/details/51271656				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p align="left"><span style="color:#333333;"><a href="http://lib.csdn.net/base/10" rel="nofollow" title="Apache Spark知识库"><strong><span style="color:#DF3434;">Spark</span></strong></a>-sql</span><span style="color:#333333;">部署</span></p>
<p align="left"><span style="color:#333333;">版本</span></p>
<p align="left"><span style="color:#333333;"><a href="http://lib.csdn.net/base/20" rel="nofollow" title="Hadoop知识库"><strong><span style="color:#DF3434;">Hadoop</span></strong></a>-2.5.0-cdh5.3.2 </span></p>
<p align="left"><span style="color:#333333;">Hive-1.2.1-cdh5.3.2</span></p>
<p align="left"><span style="color:#333333;">Spark-1.5.0</span></p>
<p align="left"><span style="color:#333333;">以</span><span style="color:#333333;">bihdp01</span><span style="color:#333333;">节点为例</span></p>
<p align="left"><span style="color:#333333;">spark master</span><span style="color:#333333;">在</span><span style="color:#333333;">bihdp01</span><span style="color:#333333;">上：</span><span style="color:#333333;"><a href="http://blog.csdn.net/developerinit/article/details/50032913" rel="nofollow"><span style="color:#3B73AF;">spark://</span></a><a href="http://cnsh045663.app.paft.com.cn/" rel="nofollow"><span style="color:#333333;">bihdp01</span></a>:7077</span></p>
<p align="left"><span style="color:#333333;">spark HistoryServer</span><span style="color:#333333;">在</span><span style="color:#333333;">bihdp01</span><span style="color:#333333;">上：</span><span style="color:#333333;"><a href="http://cnsh045663.app.paft.com.cn/" rel="nofollow"><span style="color:#333333;">bihdp01</span></a>:8032</span></p>
<p align="left"><span style="color:#333333;">spark eventLog</span><span style="color:#333333;">在</span><span style="color:#333333;">hdfs</span><span style="color:#333333;">上：</span><span style="color:#333333;"><a href="http://blog.csdn.net/developerinit/article/details/50032913" rel="nofollow"><span style="color:#3B73AF;">hdfs://testenv/spark/eventLog</span></a></span></p>
<p align="left"><span style="color:#333333;">分步指南</span></p>
<p align="left"><span style="color:#333333;">  </span></p>
<p align="left"><span style="color:#333333;">1.   </span><span style="color:#333333;">拷贝</span><span style="color:#333333;">$HIVE_HOME/conf/hive-site.xml, hive-log4j.properties</span><span style="color:#333333;">到</span><span style="color:#333333;"> $SPARK_HOME/conf/</span><span style="color:#333333;">目录</span></p>
<p align="left"><span style="color:#333333;">拷贝</span><span style="color:#333333;">mysql-connector-java-5.1.37-bin.jar</span><span style="color:#333333;">包到</span><span style="color:#333333;">spark</span><span style="color:#333333;">的</span><span style="color:#333333;">lib</span><span style="color:#333333;">下</span></p>
<p align="left"><span style="color:#333333;">  hive-site</span><span style="color:#333333;">配置：</span></p>
<table border="1" cellspacing="0" cellpadding="0"><tbody><tr><td valign="top">
<p align="left"><span style="color:#333333;">&lt;!--</span><span style="color:#333333;">在</span><span style="color:#333333;">hdfs</span><span style="color:#333333;">上</span><span style="color:#333333;">hive</span><span style="color:#333333;">数据存放目录，启动</span><span style="color:#333333;">hadoop</span><span style="color:#333333;">后需要在</span><span style="color:#333333;">hdfs</span><span style="color:#333333;">上手动创建</span></p>
<p align="left"><span style="color:#333333;">       </span></p>
<p align="left"><span style="color:#333333;">       &lt;property&gt;  </span></p>
<p align="left"><span style="color:#333333;">          &lt;name&gt;hive.metastore.schema.verification&lt;/name&gt; </span></p>
<p align="left"><span style="color:#333333;">          &lt;value&gt;false&lt;/value&gt;  </span>
</p>
<p align="left"><span style="color:#333333;">       &lt;/property&gt; --&gt;</span></p>
<p align="left"><span style="color:#333333;">       </span></p>
<p align="left"><span style="color:#333333;">       &lt;!--</span><span style="color:#333333;">默认</span><span style="color:#333333;"> metastore</span><span style="color:#333333;">在本地，添加配置改为非本地</span></p>
<p align="left"><span style="color:#333333;">       &lt;property&gt;</span></p>
<p align="left"><span style="color:#333333;">              &lt;name&gt;hive.metastore.local&lt;/name&gt;</span></p>
<p align="left"><span style="color:#333333;">              &lt;value&gt;false&lt;/value&gt;</span></p>
<p align="left"><span style="color:#333333;">       &lt;/property&gt;--&gt; </span></p>
<p align="left"><span style="color:#333333;">       </span></p>
<p align="left"><span style="color:#333333;">       &lt;property&gt;</span></p>
<p align="left"><span style="color:#333333;">              &lt;name&gt;hive.metastore.uris&lt;/name&gt;</span></p>
<p align="left"><span style="color:#333333;">              &lt;value&gt;thrift://bihdp01:9083&lt;/value&gt;</span></p>
<p align="left"><span style="color:#333333;">              &lt;description&gt;Thrift uri for the remote metastore. Used by metastore client to connect to remote metastore.&lt;/description&gt;</span></p>
<p align="left"><span style="color:#333333;">       &lt;/property&gt;</span></p>
<p align="left"><span style="color:#333333;">       </span></p>
<p align="left"><span style="color:#333333;">       </span></p>
<p align="left"><span style="color:#333333;">       &lt;property&gt;</span></p>
<p align="left"><span style="color:#333333;">              &lt;name&gt;hive.metastore.warehouse.dir&lt;/name&gt;</span></p>
<p align="left"><span style="color:#333333;">              &lt;value&gt;/hive/warehouse&lt;/value&gt;</span></p>
<p align="left"><span style="color:#333333;">       &lt;/property&gt;</span></p>
<p align="left"><span style="color:#333333;">       &lt;!--</span><span style="color:#333333;">通过</span><span style="color:#333333;">jdbc</span><span style="color:#333333;">协议连接</span><span style="color:#333333;">mysql</span><span style="color:#333333;">的</span><span style="color:#333333;">hive</span><span style="color:#333333;">库</span><span style="color:#333333;">
 --&gt;</span></p>
<p align="left"><span style="color:#333333;">       &lt;property&gt;</span></p>
<p align="left"><span style="color:#333333;">              &lt;name&gt;javax.jdo.option.ConnectionURL&lt;/name&gt;</span></p>
<p align="left"><span style="color:#333333;">              &lt;value&gt;jdbc:mysql://bihdp01:3306/hiveto?createDatabaseIfNotExist=true&lt;/value&gt;</span></p>
<p align="left"><span style="color:#333333;">              &lt;description&gt;JDBC connect string for a JDBC metastore&lt;/description&gt;</span></p>
<p align="left"><span style="color:#333333;">       &lt;/property&gt;</span></p>
<p align="left"><span style="color:#333333;">       &lt;!--jdbc</span><span style="color:#333333;">的</span><span style="color:#333333;">mysql</span><span style="color:#333333;">驱动</span><span style="color:#333333;"> --&gt;</span></p>
<p align="left"><span style="color:#333333;">       &lt;property&gt;</span></p>
<p align="left"><span style="color:#333333;">              &lt;name&gt;javax.jdo.option.ConnectionDriverName&lt;/name&gt;</span></p>
<p align="left"><span style="color:#333333;">              &lt;value&gt;com.mysql.jdbc.Driver&lt;/value&gt;</span></p>
<p align="left"><span style="color:#333333;">              &lt;description&gt;Driver class name for a JDBC metastore&lt;/description&gt;</span></p>
<p align="left"><span style="color:#333333;">       &lt;/property&gt;</span></p>
<p align="left"><span style="color:#333333;">       &lt;!--mysql</span><span style="color:#333333;">用户名</span><span style="color:#333333;"> --&gt;</span></p>
<p align="left"><span style="color:#333333;">       &lt;property&gt;</span></p>
<p align="left"><span style="color:#333333;">              &lt;name&gt;javax.jdo.option.ConnectionUserName&lt;/name&gt;</span></p>
<p align="left"><span style="color:#333333;">              &lt;value&gt;root&lt;/value&gt;</span></p>
<p align="left"><span style="color:#333333;">              &lt;description&gt;username to use against metastore database&lt;/description&gt;</span></p>
<p align="left"><span style="color:#333333;">       &lt;/property&gt;</span></p>
<p align="left"><span style="color:#333333;">       &lt;!--mysql</span><span style="color:#333333;">用户密码</span><span style="color:#333333;"> --&gt;</span></p>
<p align="left"><span style="color:#333333;">       &lt;property&gt;</span></p>
<p align="left"><span style="color:#333333;">              &lt;name&gt;javax.jdo.option.ConnectionPassword&lt;/name&gt;</span></p>
<p align="left"><span style="color:#333333;">              &lt;value&gt;*****&lt;/value&gt;</span></p>
<p align="left"><span style="color:#333333;">              &lt;description&gt;password to use against metastore database&lt;/description&gt;</span></p>
<p align="left"><span style="color:#333333;">       &lt;/property&gt;</span></p>
<p align="left"><span style="color:#333333;">       </span></p>
<p align="left"><span style="color:#333333;">       </span></p>
<p align="left"><span style="color:#333333;">       </span></p>
<p align="left"><span style="color:#333333;">       &lt;!-- </span><span style="color:#333333;">设置为</span><span style="color:#333333;">false</span><span style="color:#333333;">，查询将以运行</span><span style="color:#333333;">hiveserver2</span><span style="color:#333333;">进程的用户运行</span><span style="color:#333333;">--&gt;</span></p>
<p align="left"><span style="color:#333333;">       </span></p>
<p align="left"><span style="color:#333333;">       &lt;property&gt;</span></p>
<p align="left"><span style="color:#333333;">              &lt;name&gt;hive.server2.enable.doAs&lt;/name&gt;</span></p>
<p align="left"><span style="color:#333333;">              &lt;value&gt;ture&lt;/value&gt;</span></p>
<p align="left"><span style="color:#333333;">       &lt;/property&gt;</span></p>
<p align="left"><span style="color:#333333;">       &lt;property&gt;</span></p>
<p align="left"><span style="color:#333333;">              &lt;name&gt;hive.server2.thrift.bind.host&lt;/name&gt;</span></p>
<p align="left"><span style="color:#333333;">              &lt;value&gt;bihdp01&lt;/value&gt;</span></p>
<p align="left"><span style="color:#333333;">       &lt;/property&gt;</span></p>
<p align="left"><span style="color:#333333;">       &lt;property&gt;</span></p>
<p align="left"><span style="color:#333333;">              &lt;name&gt;hive.server2.thrift.port&lt;/name&gt;</span></p>
<p align="left"><span style="color:#333333;">              &lt;value&gt;10000&lt;/value&gt;</span></p>
<p align="left"><span style="color:#333333;">       &lt;/property&gt;</span></p>
<p align="left"><span style="color:#333333;">       </span></p>
<p align="left"><span style="color:#333333;">       </span></p>
<p align="left"><span style="color:#333333;">       &lt;property&gt;</span></p>
<p align="left"><span style="color:#333333;">              &lt;name&gt;hive.exec.parallel&lt;/name&gt;</span></p>
<p align="left"><span style="color:#333333;">              &lt;value&gt;true&lt;/value&gt;</span></p>
<p align="left"><span style="color:#333333;">       &lt;/property&gt;</span></p>
<p align="left"><span style="color:#333333;">       &lt;property&gt;</span></p>
<p align="left"><span style="color:#333333;">              &lt;name&gt;hive.exec.dynamic.partition.mode&lt;/name&gt;</span></p>
<p align="left"><span style="color:#333333;">              &lt;value&gt;strict&lt;/value&gt;</span></p>
<p align="left"><span style="color:#333333;">       &lt;/property&gt;</span></p>
<p align="left"><span style="color:#333333;">       &lt;property&gt;</span></p>
<p align="left"><span style="color:#333333;">              &lt;name&gt;hive.exec.compress.intermediate&lt;/name&gt;</span></p>
<p align="left"><span style="color:#333333;">              &lt;value&gt;true&lt;/value&gt;</span></p>
<p align="left"><span style="color:#333333;">       &lt;/property&gt;</span></p>
<p align="left"><span style="color:#333333;">       &lt;!-- </span><span style="color:#333333;">配置</span><span style="color:#333333;">hive</span><span style="color:#333333;">的</span><span style="color:#333333;">web
</span><span style="color:#333333;">页面访问的接口</span><span style="color:#333333;">hwi ,
</span><span style="color:#333333;">主机</span><span style="color:#333333;">端口</span><span style="color:#333333;">  war</span><span style="color:#333333;">包的路径</span><span style="color:#333333;">--&gt;</span></p>
<p align="left"><span style="color:#333333;">       &lt;property&gt;</span></p>
<p align="left"><span style="color:#333333;">    &lt;name&gt;hive.hwi.listen.host&lt;/name&gt;</span></p>
<p align="left"><span style="color:#333333;">    &lt;value&gt;bihdp01&lt;/value&gt;</span></p>
<p align="left"><span style="color:#333333;">  &lt;/property&gt;</span></p>
<p align="left"><span style="color:#333333;">  </span></p>
<p align="left"><span style="color:#333333;">  &lt;property&gt;</span></p>
<p align="left"><span style="color:#333333;">    &lt;name&gt;hive.hwi.listen.port&lt;/name&gt;</span></p>
<p align="left"><span style="color:#333333;">    &lt;value&gt;9999&lt;/value&gt;</span></p>
<p align="left"><span style="color:#333333;">  &lt;/property&gt;</span></p>
<p align="left"><span style="color:#333333;">  &lt;property&gt;</span></p>
<p align="left"><span style="color:#333333;">    &lt;name&gt;hive.hwi.war.file&lt;/name&gt;</span></p>
<p align="left"><span style="color:#333333;">    &lt;value&gt;lib/hive-hwi-1.2.1.war&lt;/value&gt;</span></p>
<p align="left"><span style="color:#333333;">  &lt;/property&gt;</span></p>
</td>
</tr></tbody></table><p align="left"><span style="color:#333333;"> </span></p>
<p align="left"><span style="color:#333333;">2.</span><span style="color:#333333;">修改</span><span style="color:#333333;">spark-defaults.conf</span></p>
<table border="1" cellspacing="0" cellpadding="0"><tbody><tr><td valign="top">
<p align="left"><span style="color:#333333;">spark.eventLog.enabled true</span></p>
<p align="left"><span style="color:#333333;">spark.eventLog.dir hdfs://testenv/spark/eventLog</span></p>
<p align="left"><span style="color:#333333;">spark.eventLog.compress true</span></p>
<p align="left"><span style="color:#333333;">spark.yarn.historyServer.address=bihdp01:8032</span></p>
<p align="left"><span style="color:#333333;">spark.sql.hive.metastore.version=1.2.1</span></p>
<p align="left"><span style="color:#333333;">spark.port.maxRetries=100</span></p>
<p align="left"><span style="color:#333333;"> </span></p>
<p align="left"><span style="color:#333333;">spark.sql.hive.metastore.jars=/opt/apps/hadoop/share/hadoop/mapreduce/*:/opt/apps/hadoop/share/hadoop/mapreduce/lib/*:/opt/apps/hadoop/share/hadoop/common/*:/opt/apps/hadoop/share/hadoop/common/lib/*:/opt/apps/hadoop/share/hadoop/hdfs/*</span></p>
<p align="left"><span style="color:#333333;">:/opt/apps/hadoop/share/hadoop/hdfs/lib/*:/opt/apps/hadoop/share/hadoop/yarn/*:/opt/apps/hadoop/share/hadoop/yarn/lib/*:/opt/apps/hive/lib/*:/opt/apps/spark/lib/*</span></p>
<p align="left"><span style="color:#333333;">spark.driver.extraLibraryPath=/opt/apps/hadoop/share/hadoop/mapreduce/*:/opt/apps/hadoop/share/hadoop/mapreduce/lib/*:/opt/apps/hadoop/share/hadoop/common/*:/opt/apps/hadoop/share/hadoop/common/lib/*:/opt/apps/hadoop/share/hadoop/hdfs/*</span></p>
<p align="left"><span style="color:#333333;">:/opt/apps/hadoop/share/hadoop/hdfs/lib/*:/opt/apps/hadoop/share/hadoop/yarn/*:/opt/apps/hadoop/share/hadoop/yarn/lib/*:/opt/apps/hive/lib/*:/opt/apps/spark/lib/*</span></p>
<p align="left"><span style="color:#333333;">spark.executor.extraLibraryPath=/opt/apps/hadoop/share/hadoop/mapreduce/*:/opt/apps/hadoop/share/hadoop/mapreduce/lib/*:/opt/apps/hadoop/share/hadoop/common/*:/opt/apps/hadoop/share/hadoop/common/lib/*:/opt/apps/hadoop/share/hadoop/hdfs</span></p>
<p align="left"><span style="color:#333333;">/*:/opt/apps/hadoop/share/hadoop/hdfs/lib/*:/opt/apps/hadoop/share/hadoop/yarn/*:/opt/apps/hadoop/share/hadoop/yarn/lib/*:/opt/apps/hive/lib/*:/opt/apps/spark/lib/*</span></p>
</td>
</tr></tbody></table><p align="left"><span style="color:#333333;"> </span></p>
<p align="left"><span style="color:#333333;">3.</span><span style="color:#333333;">修改</span><span style="color:#333333;">spark-env.sh</span></p>
<table border="1" cellspacing="0" cellpadding="0"><tbody><tr><td valign="top">
<p align="left">export SPARK_WORKER_DIR=/data/cdhdata/spark/work</p>
<p align="left">export JAVA_HOME=/usr/java/jdk1.7.0_79</p>
<p align="left">#export HADOOP_HOME=/home/hadoop/app/hadoop-2.6.0-cdh5.6.0</p>
<p align="left">export SCALA_HOME=/home/hadoop/app/scala-2.10.4</p>
<p align="left">#export HADOOP_CONF_DIR=/home/hadoop/app/hadoop-2.6.0-cdh5.6.0/etc/hadoop</p>
<p align="left">export JAVA_OPTS="-verbose:gc -XX:+PrintGCDetails -XX:+PrintGCTimeStamps"</p>
<p align="left">#export HIVE_HOME=/home/hadoop/app/apache-hive-1.2.1-bin</p>
<p align="left">#export SPARK_MASTER_IP=</p>
<p align="left">export SPARK_DAEMON_MEMORY=512m</p>
<p align="left">export SPARK_WORKER_MEMORY=1024M</p>
<p align="left">export SPARK_DAEMON_JAVA_OPTS="-Dspark.deploy.recoveryMode=ZOOKEEPER -Dspark.deploy.zookeeper.url=bihdp01:2181,bihdp02:2181,bihdp03:2181 -Dspark.deploy.zookeeper.dir=/localspark"</p>
<p align="left">export SPARK_HISTORY_OPTS="-Dspark.history.ui.port=18080 -Dspark.history.retainedApplications=3 -Dspark.history.fs.logDirectory=/data/cdhdata/sparklocallogs"</p>
<p align="left">#set Hadoop path</p>
<p align="left">export HDFS_YARN_LOGS_DIR=/data1/hadooplogs</p>
<p align="left">export HADOOP_PREFIX=/opt/apps/hadoop</p>
<p align="left">export HADOOP_HOME=$HADOOP_PREFIX</p>
<p align="left">export HADOOP_COMMON_HOME=$HADOOP_HOME</p>
<p align="left">export HADOOP_HDFS_HOME=$HADOOP_HOME</p>
<p align="left">export HADOOP_MAPRED_HOME=$HADOOP_HOME</p>
<p align="left">export HADOOP_MAPRED_PID_DIR=$HADOOP_HOME/pids</p>
<p align="left">export HADOOP_YARN_HOME=$HADOOP_HOME</p>
<p align="left">export HADOOP_CONF_DIR=$HADOOP_HOME/etc/hadoop</p>
<p align="left">export HDFS_CONF_DIR=$HADOOP_HOME/etc/hadoop</p>
<p align="left">export HADOOP_LOG_DIR=$HDFS_YARN_LOGS_DIR/logs</p>
<p align="left">export HADOOP_PID_DIR=$HADOOP_HOME/pids</p>
<p align="left">export HADOOP_SECURE_DN_PID_DIR=$HADOOP_PID_DIR</p>
<p align="left">export YARN_HOME=$HADOOP_HOME</p>
<p align="left">export YARN_CONF_DIR=$HADOOP_HOME/etc/hadoop</p>
<p align="left">export YARN_LOG_DIR=$HDFS_YARN_LOGS_DIR/logs</p>
<p align="left">export YARN_PID_DIR=$HADOOP_HOME/pids</p>
<p align="left">export PATH=$PATH:$HADOOP_HOME/bin:$HADOOP_HOME/sbin</p>
<p align="left"> </p>
<p align="left">export HADOOP_CLASSPATH=$HADOOP_CLASSPATH:$HADOOP_CONF_DIR:$HADOOP_MAPRED_HOME/share/hadoop/mapreduce/*:$HADOOP_MAPRED_HOME/share/hadoop/mapreduce/lib/*:$HADOOP_COMMON_HOME/share/hadoop/common/*:$HADOOP_COMMON_HOME/share/hadoop/common/lib/*:$HADOOP_HDFS_HOME/share/hadoop/hdfs/*:$HADOOP_HDFS_HOME/share/hadoop/hdfs/lib/*:$HADOOP_YARN_HOME/share/hadoop/yarn/*:$HADOOP_YARN_HOME/share/hadoop/yarn/lib/*</p>
<p align="left">export CLASSPATH=$HADOOP_CLASSPATH:$CLASSPATH</p>
</td>
</tr></tbody></table><p align="left"> </p>
<p align="left"><span style="color:#333333;"> </span></p>
<p align="left"><span style="color:#333333;">4.[ERROR] Terminal initializationfailed; falling back to unsupported<br></span><span style="color:#333333;">解决问题</span><span style="color:#333333;"><a href="http://lib.csdn.net/base/17" rel="nofollow" title="Java EE知识库"><strong><span style="color:#DF3434;">Java</span></strong></a>.lang.IncompatibleClassChangeError:Found
 class jline.Terminal, but interface was expected</span></p>
<p align="left"><span style="color:#333333;">需要删除</span><span style="color:#333333;">/opt/apps/hadoop/share/hadoop/yarn/lib/jline-0.9.94.jar</span><span style="color:#333333;">文件，在</span><span style="color:#333333;">/etc/profile</span><span style="color:#333333;">中配置</span><span style="color:#333333;">exportHADOOP_USER_CLASSPATH_FIRST=true</span><span style="color:#333333;">并</span><span style="color:#333333;">source</span><span style="color:#333333;">操作</span></p>
<p align="left"><span style="color:#333333;">见：</span><span style="color:#333333;"><a href="https://cwiki.apache.org/confluence/display/Hive/Hive+on+Spark:+Getting+Started" rel="nofollow"><span style="color:#3B73AF;">https://cwiki.apache.org/confluence/display/Hive/Hive+on+Spark:+Getting+Started</span></a></span></p>
<p align="left"><span style="color:#333333;"> </span></p>
<p align="left"><span style="color:#333333;">5.</span><span style="color:#333333;">使用</span><span style="color:#333333;">spark-shell</span></p>
<p align="left" style="background:rgb(248,248,248);"><strong><span style="color:#C0C0C0;">[plain]</span></strong><span style="color:#C0C0C0;"> <a href="http://blog.csdn.net/developerinit/article/details/50032913" rel="nofollow" title="view plain"><span style="color:#A0A0A0;">view
 plain</span></a> <a href="http://blog.csdn.net/developerinit/article/details/50032913" rel="nofollow" title="copy"><span style="color:#A0A0A0;">copy</span></a></span></p>
<div>
<p align="left"><span style="color:#5C5C5C;">1.  </span>val sqlContext = new org.apache.spark.sql.hive.HiveContext(sc)  </p>
<p align="left" style="background:rgb(248,248,248);"><span style="color:#5C5C5C;">2. </span>sqlContext.sql("select * from test limit 2 ").collect().foreach(println)  </p>
</div>
<p align="left"><span style="color:#333333;"> </span></p>
<p align="left"><span style="color:#333333;">6.</span><span style="color:#333333;">使用</span><span style="color:#333333;">spark-sql</span><span style="color:#333333;">启动</span><span style="color:#333333;">local</span><span style="color:#333333;">模式</span></p>
<p align="left"><span style="color:#333333;">里面可以直接输入</span><span style="color:#333333;">HiveQL</span><span style="color:#333333;">语句执行</span></p>
<p align="left"><span style="color:#333333;"> </span></p>
<p align="left"><span style="color:#333333;">7. </span><span style="color:#333333;">启动</span><span style="color:#333333;">standalone</span><span style="color:#333333;">集群模式的</span><span style="color:#333333;">spark-sql </span></p>
<p align="left" style="background:rgb(248,248,248);"><strong><span style="color:#C0C0C0;">[plain]</span></strong><span style="color:#C0C0C0;"> <a href="http://blog.csdn.net/developerinit/article/details/50032913" rel="nofollow" title="view plain"><span style="color:#A0A0A0;">view
 plain</span></a> <a href="http://blog.csdn.net/developerinit/article/details/50032913" rel="nofollow" title="copy"><span style="color:#A0A0A0;">copy</span></a></span></p>
<div>
<p align="left"><span style="color:#5C5C5C;">1.  </span>spark-sql --master spark://bihdp01:7077    </p>
</div>
<p align="left"><span style="color:#333333;"> </span></p>
<p align="left"><span style="color:#333333;">（该主机地址应该对应</span><span style="color:#333333;">spark</span><span style="color:#333333;">的</span><span style="color:#333333;">ALIVE</span><span style="color:#333333;">节点）</span></p>
<p align="left"><span style="color:#333333;"> </span></p>
<p align="left"><span style="color:#333333;">8.</span><span style="color:#333333;">启动</span><span style="color:#333333;">spark on yarn</span><span style="color:#333333;">模式的</span><span style="color:#333333;">spark-sql</span></p>
<p align="left" style="background:rgb(248,248,248);"><strong><span style="color:#C0C0C0;">[plain]</span></strong><span style="color:#C0C0C0;"> <a href="http://blog.csdn.net/developerinit/article/details/50032913" rel="nofollow" title="view plain"><span style="color:#A0A0A0;">view
 plain</span></a> <a href="http://blog.csdn.net/developerinit/article/details/50032913" rel="nofollow" title="copy"><span style="color:#A0A0A0;">copy</span></a></span></p>
<div>
<p align="left"><span style="color:#5C5C5C;">1.  </span>spark-sql --master yarn-client   </p>
</div>
<p align="left" style="background:rgb(248,248,248);"><strong><span style="color:#C0C0C0;">[plain]</span></strong><span style="color:#C0C0C0;"> <a href="http://blog.csdn.net/developerinit/article/details/50032913" rel="nofollow" title="view plain"><span style="color:#A0A0A0;">view
 plain</span></a> <a href="http://blog.csdn.net/developerinit/article/details/50032913" rel="nofollow" title="copy"><span style="color:#A0A0A0;">copy</span></a></span></p>
<div>
<p align="left"><span style="color:#5C5C5C;">1.  </span>或者   </p>
</div>
<p align="left" style="background:rgb(248,248,248);"><strong><span style="color:#C0C0C0;">[plain]</span></strong><span style="color:#C0C0C0;"> <a href="http://blog.csdn.net/developerinit/article/details/50032913" rel="nofollow" title="view plain"><span style="color:#A0A0A0;">view
 plain</span></a> <a href="http://blog.csdn.net/developerinit/article/details/50032913" rel="nofollow" title="copy"><span style="color:#A0A0A0;">copy</span></a></span></p>
<div>
<p align="left"><span style="color:#5C5C5C;">1.  </span>spark-sql --master yarn-cluster(暂时还不支持)  </p>
</div>
<p> </p>
            </div>
                </div>