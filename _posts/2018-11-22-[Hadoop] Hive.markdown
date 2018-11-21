---
layout:     post
title:      [Hadoop] Hive
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h2>HIVE</h2>
<p><a href="http://hadoop.apache.org/hive/" rel="nofollow">http://hadoop.apache.org/hive/</a></p>
<p><a href="http://wiki.apache.org/hadoop/Hive/GettingStarted" rel="nofollow">http://wiki.apache.org/hadoop/Hive/GettingStarted</a></p>
<p> </p>
<p>下载</p>
<p>wget <a href="http://labs.renren.com/apache-mirror/hadoop/hive/hive-0.5.0/hive-0.5.0-bin.tar.gz" rel="nofollow">http://labs.renren.com/apache-mirror/hadoop/hive/hive-0.5.0/hive-0.5.0-bin.tar.gz</a></p>
<p> </p>
<p>Cloudera版本的Hadoop和Hive</p>
<p><a href="http://archive.cloudera.com/cdh" rel="nofollow">http://archive.cloudera.com/cdh</a></p>
<p> </p>
<p>安装步骤： </p>
<p>hive-0.5.0-bin\README.txt</p>
<p> </p>
<p>gzip -d hive-0.5.0-bin.tar.gz</p>
<p>tar xvf hive-0.5.0-bin.tar </p>
<p>cd hive-0.5.0-bin</p>
<p>chmod 700 bin/*</p>
<p> </p>
<p class="MsoNormal"><span lang="en-us" xml:lang="en-us"><span style="font-size:small;font-family:Calibri;">vi bin/hive-config.sh</span></span></p>
<p class="MsoNormal"><span lang="en-us" xml:lang="en-us">export HIVE_HOME=/home/iic/hive-0.5.0-bin</span></p>
<p class="MsoNormal"><span lang="en-us" xml:lang="en-us"><span style="font-size:small;font-family:Calibri;">export HADOOP_HOME=/home/iic/hadoop-0.20.2</span></span></p>
<p class="MsoNormal"><span lang="en-us" xml:lang="en-us"><span style="font-size:small;font-family:Calibri;">export JAVA_HOME=/home/bmb/jdk1.6.0_16</span></span></p>
<p> </p>
<p> </p>
<p>bin/hadoop fs -mkdir       /tmp<br>bin/hadoop fs -mkdir       /user/hive/warehouse<br>bin/hadoop fs -chmod g+w   /tmp<br>bin/hadoop fs -chmod g+w   /user/hive/warehouse</p>
<p> </p>
<p> </p>
<p>启动</p>
<p>bin/hive</p>
<p> </p>
<p>出错：</p>
<p>Exception in thread "main" java.lang.NoClassDefFoundError: org/apache/hadoop/hive/conf/HiveConf</p>
<p>原因：Hadoop的配置文件，被增加了<span lang="en-us" xml:lang="en-us"><span style="font-family:Calibri;">hadoop-env.sh</span></span><span>里面被增加了</span><span lang="en-us" xml:lang="en-us"><span style="font-family:Calibri;">HADOOP_CLASSPATH</span></span><span>的设置</span></p>
<p><span>原来是：</span></p>
<p><span>export HADOOP_CLASSPATH=$HBASE_HOME/hbase-0.20.3.jar:$HBASE_HOME/hbase-0.20.3-test.jar:$HBASE_HOME/conf:${HBASE_HOME}/lib/zookeeper-3.3.0.jar</span></p>
<p><span>修改成：</span></p>
<p><span>export HADOOP_CLASSPATH=<span style="color:#ff0000;"><span style="font-family:Calibri;">$HADOOP_CLASSPATH:</span></span>$HBASE_HOME/hbase-0.20.3.jar:$HBASE_HOME/hbase-0.20.3-test.jar:$HBASE_HOME/conf:${HBASE_HOME}/lib/zookeeper-3.3.0.jar</span></p>
<p> </p>
<p><a href="http://www.wangzhongyuan.com/archives/807.html" rel="nofollow">http://www.wangzhongyuan.com/archives/807.html</a></p>
<p> </p>
<p>tail -f /tmp/iic/hive.log </p>
<p> </p>
<h2><span style="font-size:large;"><span lang="en-us" xml:lang="en-us"><span style="font-family:Cambria;">Hive Web</span></span><span>管理页面</span></span></h2>
<p class="MsoNormal"><span lang="en-us" xml:lang="en-us"><span style="font-size:small;font-family:Calibri;">bin/hive --service hwi</span></span></p>
<p class="MsoNormal"><span lang="en-us" xml:lang="en-us"><a href="http://192.168.2.79:9999/hwi/" rel="nofollow">http://192.168.2.79:9999/hwi/</a></span></p>
<p class="MsoNormal"> </p>
<p class="MsoNormal"><span lang="en-us" xml:lang="en-us"><span style="font-size:small;font-family:Calibri;">lib/hive_hwi.war</span></span> </p>
<p> </p>
<p>异常处理</p>
<p>FAILED: Error in metadata: javax.jdo.JDOFatalDataStoreException: Failed to start database 'metastore_db', see the next exception for details.</p>
<p> </p>
<p class="MsoNormal"><span style="font-size:small;"><span>原因，默认的“<span lang="en-us" xml:lang="en-us">metastore_db</span>”，同一时候，只能有一个进程对</span><span lang="en-us" xml:lang="en-us"><span style="font-family:Calibri;">Hive</span></span><span>进行操作，</span><span lang="en-us" xml:lang="en-us"><span style="font-family:Calibri;">ps</span></span><span>一下所有的</span><span lang="en-us" xml:lang="en-us"><span style="font-family:Calibri;">hive,Kill</span></span><span>掉他们。</span></span></p>
<p> </p>
<p>  </p>
<h2><span><span style="font-size:large;">批量删除进程</span></span></h2>
<p class="MsoNormal"><span lang="en-us" xml:lang="en-us"><span style="font-size:small;">kill -9 `ps -ef |grep hive |awk '{print $2}' `</span></span></p>
<p> </p>
<p> </p>
<h2><span lang="en-us" xml:lang="en-us"><span style="font-size:large;font-family:Cambria;">Oracle metastore</span></span></h2>
<p class="MsoNormal"><span lang="en-us" xml:lang="en-us"><span style="font-size:small;font-family:Calibri;">javax.jdo.option.ConnectionDriverName</span></span></p>
<p class="MsoNormal"><span lang="en-us" xml:lang="en-us"><span style="font-size:small;font-family:Calibri;">javax.jdo.option.ConnectionURL</span></span></p>
<p class="MsoNormal"><span lang="en-us" xml:lang="en-us"><span style="font-size:small;font-family:Calibri;">javax.jdo.option.ConnectionUserName</span></span></p>
<p class="MsoNormal"><span lang="en-us" xml:lang="en-us"><span style="font-size:small;font-family:Calibri;">javax.jdo.option.ConnectionPassword</span></span></p>
<p class="MsoNormal"><span lang="en-us" xml:lang="en-us"><span style="font-size:small;font-family:Calibri;"> </span></span></p>
<p class="MsoNormal"><span style="font-size:small;"><span>以下内容不能有空格，需要</span><span lang="en-us" xml:lang="en-us"><span style="font-family:Calibri;">4</span></span><span>个都替换</span></span></p>
<p class="MsoNormal"><span lang="en-us" xml:lang="en-us"><span style="font-size:small;font-family:Calibri;">&lt;property&gt;</span></span></p>
<p class="MsoNormal"><span lang="en-us" xml:lang="en-us"><span style="font-size:small;"><span style="font-family:Calibri;"><span>  </span>&lt;name&gt;javax.jdo.option.ConnectionURL&lt;/name&gt;</span></span></span></p>
<p class="MsoNormal"><span lang="en-us" xml:lang="en-us"><span style="font-size:small;"><span style="font-family:Calibri;"><span>  </span>&lt;value&gt;jdbc:oracle:thin:@192.168.2.82:1521:iic&lt;/value&gt;</span></span></span></p>
<p class="MsoNormal"><span lang="en-us" xml:lang="en-us"><span style="font-size:small;"><span style="font-family:Calibri;"><span> </span>&lt;/property&gt;</span></span></span></p>
<p class="MsoNormal"><span lang="en-us" xml:lang="en-us"><span style="font-size:small;font-family:Calibri;">&lt;property&gt;</span></span></p>
<p class="MsoNormal"><span lang="en-us" xml:lang="en-us"><span style="font-size:small;"><span style="font-family:Calibri;"><span>  </span>&lt;name&gt;javax.jdo.option.ConnectionDriverName&lt;/name&gt;</span></span></span></p>
<p class="MsoNormal"><span lang="en-us" xml:lang="en-us"><span style="font-size:small;"><span style="font-family:Calibri;"><span>  </span>&lt;value&gt;oracle.jdbc.driver.OracleDriver&lt;/value&gt;</span></span></span></p>
<p class="MsoNormal"><span lang="en-us" xml:lang="en-us"><span style="font-size:small;font-family:Calibri;">&lt;/property&gt;</span></span></p>
<p class="MsoNormal"><span lang="en-us" xml:lang="en-us"><span style="font-size:small;font-family:Calibri;">&lt;property&gt;</span></span></p>
<p class="MsoNormal"><span lang="en-us" xml:lang="en-us"><span style="font-size:small;"><span style="font-family:Calibri;"><span>  </span>&lt;name&gt;javax.jdo.option.ConnectionUserName&lt;/name&gt;</span></span></span></p>
<p class="MsoNormal"><span lang="en-us" xml:lang="en-us"><span style="font-size:small;"><span style="font-family:Calibri;"><span>  </span>&lt;value&gt;iic&lt;/value&gt;</span></span></span></p>
<p class="MsoNormal"><span lang="en-us" xml:lang="en-us"><span style="font-size:small;"><span style="font-family:Calibri;"><span> </span>&lt;/property&gt;</span></span></span></p>
<p class="MsoNormal"><span lang="en-us" xml:lang="en-us"><span style="font-size:small;font-family:Calibri;">&lt;property&gt;</span></span></p>
<p class="MsoNormal"><span lang="en-us" xml:lang="en-us"><span style="font-size:small;"><span style="font-family:Calibri;"><span>  </span>&lt;name&gt;javax.jdo.option.ConnectionPassword&lt;/name&gt;</span></span></span></p>
<p class="MsoNormal"><span lang="en-us" xml:lang="en-us"><span style="font-size:small;"><span style="font-family:Calibri;"><span>  </span>&lt;value&gt;iic&lt;/value&gt;</span></span></span></p>
<p class="MsoNormal"><span lang="en-us" xml:lang="en-us"><span style="font-size:small;font-family:Calibri;">&lt;/property&gt;</span></span></p>
<p> </p>
<h2><span style="font-size:large;"><span lang="en-us" xml:lang="en-us"><span style="font-family:Cambria;">MetaStore</span></span><span>相关表</span></span></h2>
<p class="MsoNormal"><span lang="en-us" xml:lang="en-us"><span style="font-size:small;font-family:Calibri;">\hive\src\metastore\src\model\package.jdo</span></span></p>
<p class="MsoNormal"><span style="font-size:small;"><span>修改</span><span lang="en-us" xml:lang="en-us"><span style="font-family:Calibri;">jdo,</span></span><span>可以修改相关的表</span></span></p>
<p class="MsoNormal"><span lang="en-us" xml:lang="en-us"><span style="font-size:small;font-family:Calibri;">DBS</span></span></p>
<p class="MsoNormal"><span lang="en-us" xml:lang="en-us"><span style="font-size:small;font-family:Calibri;">TYPE_FIELDS</span></span></p>
<p class="MsoNormal"><span lang="en-us" xml:lang="en-us"><span style="font-size:small;font-family:Calibri;">TYPES</span></span></p>
<p class="MsoNormal"><span lang="en-us" xml:lang="en-us"><span style="font-size:small;font-family:Calibri;">TYPE_FIELDS</span></span></p>
<p class="MsoNormal"><span lang="en-us" xml:lang="en-us"><span style="font-size:small;font-family:Calibri;">TBLS</span></span></p>
<p class="MsoNormal"><span lang="en-us" xml:lang="en-us"><span style="font-size:small;font-family:Calibri;">PARTITION_KEYS</span></span></p>
<p class="MsoNormal"><span lang="en-us" xml:lang="en-us"><span style="font-size:small;font-family:Calibri;">TABLE_PARAMS</span></span></p>
<p class="MsoNormal"><span lang="en-us" xml:lang="en-us"><span style="font-size:small;font-family:Calibri;">SERDES</span></span></p>
<p class="MsoNormal"><span lang="en-us" xml:lang="en-us"><span style="font-size:small;font-family:Calibri;">SERDE_PARAMS</span></span></p>
<p class="MsoNormal"><span lang="en-us" xml:lang="en-us"><span style="font-size:small;font-family:Calibri;">SORT_ORDER</span></span></p>
<p class="MsoNormal"><span lang="en-us" xml:lang="en-us"><span style="font-size:small;font-family:Calibri;">SDS</span></span></p>
<p class="MsoNormal"><span lang="en-us" xml:lang="en-us"><span style="font-size:small;font-family:Calibri;">COLUMNS</span></span></p>
<p class="MsoNormal"><span lang="en-us" xml:lang="en-us"><span style="font-size:small;font-family:Calibri;">BUCKETING_COLS</span></span></p>
<p class="MsoNormal"><span lang="en-us" xml:lang="en-us"><span style="font-size:small;font-family:Calibri;">SORT_COLS</span></span></p>
<p class="MsoNormal"><span lang="en-us" xml:lang="en-us"><span style="font-size:small;font-family:Calibri;">SD_PARAMS</span></span></p>
<p class="MsoNormal"><span lang="en-us" xml:lang="en-us"><span style="font-size:small;font-family:Calibri;">PARTITIONS</span></span></p>
<p class="MsoNormal"><span lang="en-us" xml:lang="en-us"><span style="font-size:small;font-family:Calibri;">PARTITION_KEY_VALS</span></span></p>
<p class="MsoNormal"><span lang="en-us" xml:lang="en-us"><span style="font-size:small;font-family:Calibri;">PARTITION_PARAMS</span></span></p>
<p> </p>            </div>
                </div>