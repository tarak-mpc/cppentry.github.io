---
layout:     post
title:      centos(vm)下安装hive2.1.1单机模式(x86)
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>1.vm安装(略)</p>
<p>2.jdk安装(略)</p>
<p>3.hive安装</p>
<p>a.下载hive</p>
<p>http://mirrors.cnnic.cn/apache/hive/hive-2.1.1/</p>
<p>cd hive/</p>
<p>tar -zxvf apache-hive-2.1.1-bin.tar.gz</p>
<p>[root@namenode hive]# cd apache-hive-2.1.1-bin/</p>
<p>[root@namenode apache-hive-2.1.1-bin]# ll</p>
<p>b.修改环境变量<br></p>
<p>[root@namenode ~]# vi /etc/profile</p>
<p>#HIVE<br>
export HIVE_HOME=/root/hive/apache-hive-2.1.1-bin<br>
export PATH=$PATH:$HIVE_HOME/bin<br>
"/etc/profile" 90L, 2123C written</p>
<p>wq!</p>
<p>[root@namenode ~]# source /etc/profile</p>
<p>3.修改配置文件</p>
<p>[root@namenode apache-hive-2.1.1-bin]# cd conf/</p>
<p><br>
[root@namenode conf]# vi hive-site.xml<br><br>
&lt;?xml version="1.0" encoding="UTF-8" standalone="no"?&gt;<br>
&lt;?xml-stylesheet type="text/xsl" href="configuration.xsl"?&gt;<br>
&lt;configuration&gt;<br>
&lt;property&gt;<br>
    &lt;name&gt;hive.metastore.warehouse.dir&lt;/name&gt;<br>
    &lt;value&gt;/root/hive/apache-hive-2.1.1-bin/warehouse&lt;/value&gt;<br>
    &lt;description&gt;location of default database for the warehouse&lt;/description&gt;<br>
&lt;/property&gt;<br>
&lt;property&gt;<br>
   &lt;name&gt;javax.jdo.option.ConnectionURL&lt;/name&gt;<br>
   &lt;value&gt;jdbc:derby:/root/hive/apache-hive-2.1.1-bin/metastore_db;create=true&lt;/value&gt;<br>
   &lt;description&gt;JDBC connect string for a JDBC metastore&lt;/description&gt;<br>
&lt;/property&gt;<br>
&lt;/configuration&gt;</p>
<p><br></p>
<p>4.初始化数据库</p>
<p>[root@namenode conf]# schematool -initSchema -dbType derby</p>
<p>which: no hbase in (.:/root/hadoop/hadoop-2.7.2/bin:/root/java/jdk1.8.0_77/bin:.:/root/hadoop/hadoop-2.7.2/bin:/root/java/jdk1.8.0_77/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/root/java/jdk1.8.0_77/bin:/root/bin:/root/java/jdk1.8.0_77/bin:/root/hive/apache-hive-2.1.1-bin/bin)<br>
SLF4J: Class path contains multiple SLF4J bindings.<br>
SLF4J: Found binding in [jar:file:/root/hive/apache-hive-2.1.1-bin/lib/log4j-slf4j-impl-2.4.1.jar!/org/slf4j/impl/StaticLoggerBinder.class]<br>
SLF4J: Found binding in [jar:file:/root/hadoop/hadoop-2.7.2/share/hadoop/common/lib/slf4j-log4j12-1.7.10.jar!/org/slf4j/impl/StaticLoggerBinder.class]<br>
SLF4J: See http://www.slf4j.org/codes.html#multiple_bindings for an explanation.<br>
SLF4J: Actual binding is of type [org.apache.logging.slf4j.Log4jLoggerFactory]<br>
Metastore connection URL:        jdbc:derby:/root/hive/apache-hive-2.1.1-bin/metastore_db;create=true<br>
Metastore Connection Driver :    org.apache.derby.jdbc.EmbeddedDriver<br>
Metastore connection User:       APP<br>
Starting metastore schema initialization to 2.1.0<br>
Initialization script hive-schema-2.1.0.derby.sql<br>
Initialization script completed</p>
<p>5.启动程序</p>
<p>[root@namenode conf]# mkdir -p /opt/hive-2.0.0/warehouse <br>
[root@namenode conf]# chmod a+rwx /opt/hive-2.0.0/warehouse<br>
[root@namenode conf]# hive<br>
which: no hbase in (.:/root/hadoop/hadoop-2.7.2/bin:/root/java/jdk1.8.0_77/bin:.:/root/hadoop/hadoop-2.7.2/bin:/root/java/jdk1.8.0_77/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/root/java/jdk1.8.0_77/bin:/root/bin:/root/java/jdk1.8.0_77/bin:/root/hive/apache-hive-2.1.1-bin/bin)<br>
SLF4J: Class path contains multiple SLF4J bindings.<br>
SLF4J: Found binding in [jar:file:/root/hive/apache-hive-2.1.1-bin/lib/log4j-slf4j-impl-2.4.1.jar!/org/slf4j/impl/StaticLoggerBinder.class]<br>
SLF4J: Found binding in [jar:file:/root/hadoop/hadoop-2.7.2/share/hadoop/common/lib/slf4j-log4j12-1.7.10.jar!/org/slf4j/impl/StaticLoggerBinder.class]<br>
SLF4J: See http://www.slf4j.org/codes.html#multiple_bindings for an explanation.<br>
SLF4J: Actual binding is of type [org.apache.logging.slf4j.Log4jLoggerFactory]<br><br>
Logging initialized using configuration in jar:file:/root/hive/apache-hive-2.1.1-bin/lib/hive-common-2.1.1.jar!/hive-log4j2.properties Async: true<br><br>
Hive-on-MR is deprecated in Hive 2 and may not be available in the future versions. Consider using a different execution engine (i.e. spark, tez) or using Hive 1.X releases.<br>
hive&gt; <br>
    &gt; <br>
    &gt; <br>
    &gt; <br>
    &gt; <br>
    &gt; show databases;<br>
OK<br>
default<br>
Time taken: 6.81 seconds, Fetched: 1 row(s)</p>
<p><br></p>
<p><br></p>
<p><br></p>
            </div>
                </div>