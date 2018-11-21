---
layout:     post
title:      hadoop的dw-hive安装配置---hadoop安装系列之三
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/firehadoop/article/details/69075792				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><strong>一、hive相关知识复习</strong></p>
<p><strong>复习一下hadoop的数据仓库hive，它的元数据存储在第三方数据库中，实际数据存储在hadoop的hdfs上，所以hive不能脱离hadoop与第三方数据库单独存在，我们前面已经安装好了hadoop2.7.3版本，只要安装一个第三方数据库即可满足hive安装的依赖环境，这里我选择安装的是mysql5.5.46，hive选择安装的是1.2.1版本。</strong></p>
<p>hive将结构化的数据文件映射为数据库表（元数据保存在mysql中），数据文件在hdfs上存储，通过将sql语句解析转换为mapreduce任务进行分布式计算运行。</p>
<p>hive官网对安装准备有如下描述;</p>
<p></p>
<p style="color:rgb(51,51,51);font-family:Arial, sans-serif;font-size:14px;">
Hive installation has these requirements:</p>
<ul style="color:rgb(51,51,51);font-family:Arial, sans-serif;font-size:14px;"><li>Java 1.7 (preferred).<br><em>Note:</em>  Hive versions <a class="external-link" href="https://issues.apache.org/jira/browse/HIVE/fixforversion/12329345/?selectedTab=com.atlassian.jira.jira-projects-plugin:version-summary-panel" rel="nofollow" style="color:rgb(53,114,176);text-decoration:none;">1.2</a> onward
 require Java 1.7 or newer. Hive versions 0.14 to 1.1 work with Java 1.6, but prefer 1.7. Users are strongly advised to start moving to Java 1.8 (see <a href="https://issues.apache.org/jira/browse/HIVE-8607" rel="nofollow" class="external-link" style="color:rgb(53,114,176);text-decoration:none;">HIVE-8607</a>). </li><li>Hadoop 2.x (preferred), 1.x (not supported by Hive 2.0.0 onward).<br>
Hive versions up to 0.13 also supported Hadoop 0.20.x, 0.23.x.</li><li>Hive is commonly used in production Linux and Windows environment. Mac is a commonly used development environment. The instructions in this document are applicable to Linux and Mac. Using it on Windows would require slightly different steps.  </li></ul><br><p>二、安装文件下载链接</p>
<p>hive：</p>
<p>http://apache.fayea.com/hive/hive-1.2.1/apache-hive-1.2.1-bin.tar.gz<br></p>
<p>mysql：</p>
<p>https://downloads.mysql.com/archives/get/file/MySQL-server-5.5.46-1.el7.x86_64.rpm<br></p>
<p>https://downloads.mysql.com/archives/get/file/MySQL-client-5.5.46-1.el7.x86_64.rpm<br></p>
<p>三、安装步骤</p>
<p><strong>1、卸载操作系统中预安装的mysql依赖库</strong></p>
<p>--下面操作均需要在root权限下操作</p>
<p>#yum remove mysql-libs <br></p>
<p><strong>2、安装mysql客户端</strong></p>
<p>[root@master hadoop]#  rpm -ivh MySQL-client-5.5.46-1.el7.x86_64.rpm <br></p>
<p>Preparing...                          ################################# [100%]<br>
Updating / installing...<br>
1:MySQL-client-5.5.46-1.el7        ################################# [100%]<br></p>
<p>--slave01上也安装mysql的客户端</p>
<p></p>
<p>[root@slave01 hadoop]#  rpm -ivh MySQL-client-5.5.46-1.el7.x86_64.rpm <br></p>
<p>Preparing...                          ################################# [100%]<br>
Updating / installing...<br>
1:MySQL-client-5.5.46-1.el7        ################################# [100%]</p>
<br><p><strong>3、安装mysql服务器端</strong></p>
<p>[root@master hadoop]# rpm -ivh MySQL-server-5.5.46-1.el7.x86_64.rpm <br>
warning: MySQL-server-5.5.46-1.el7.x86_64.rpm: Header V3 DSA/SHA1 Signature, key ID 5072e1f5: NOKEY<br>
Preparing...                          ################################# [100%]<br>
Updating / installing...<br>
   1:MySQL-server-5.5.46-1.el7        ################################# [100%]<br>
170404  1:49:51 [Note] /usr/sbin/mysqld (mysqld 5.5.46) starting as process 33406 ...<br>
170404  1:49:51 [Note] /usr/sbin/mysqld (mysqld 5.5.46) starting as process 33413 ...<br><br>
--mysql安装成功后输出信息，提示要首先启动服务为mysql的root用户设置密码<br><strong><span style="color:#ff0000;">PLEASE REMEMBER TO SET A PASSWORD FOR THE MySQL root USER !</span></strong><br>
To do so, start the server, then issue the following commands:<br><br><strong><span style="color:#ff0000;">--mysql怕我不知道怎么为root设置密码，还非常慷慨的给出了实例</span></strong><br>
/usr/bin/mysqladmin -u root password 'new-password'<br>
/usr/bin/mysqladmin -u root -h master password 'new-password'<br><br><strong><span style="color:#ff0000;">--如果上面给出的更改密码方法你不爽的话，mysql还提供了套餐服务</span></strong><br>
Alternatively you can run:<br>
/usr/bin/mysql_secure_installation<br><strong>--下面还非常细心的建议在生产数据库中删除test库等必要的安全措施</strong><br>
which will also give you the option of removing the test<br>
databases and anonymous user created by default.  This is<br>
strongly recommended for production servers.<br><br><br>
See the manual for more instructions.<br>
Please report any problems at http://bugs.mysql.com/<br></p>
<p><strong>4、启动mysql数据库服务</strong></p>
<p>[root@master hadoop]# service mysql start<br></p>
<p>Starting MySQL... SUCCESS! <br></p>
<p><strong>5、设置root用户的登陆密码,这一步开始不需要在root权限下运行了</strong></p>
<p>[hadoop@master ~]$ /usr/bin/mysqladmin -u root password 'mysql123'<br></p>
<p><em>--我的密码是为我这个老年痴呆症患者设计的<img alt="偷笑" src="http://static.blog.csdn.net/xheditor/xheditor_emot/default/titter.gif"></em></p>
<p><strong>6、用root用户测试进入mysql数据库</strong></p>
<p>[hadoop@master ~]$ mysql -uroot -pmysql123<br>
Welcome to the MySQL monitor.  Commands end with ; or \g.<br>
Your MySQL connection id is 2<br>
Server version: 5.5.46 MySQL Community Server (GPL)<br><br>
Oracle is a registered trademark of Oracle Corporation and/or its<br>
affiliates. Other names may be trademarks of their respective<br>
owners.<br><br><br>
Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.<br><br>
mysql&gt; show databases;<br>
+--------------------+<br>
| Database           |<br>
+--------------------+<br>
| information_schema |<br>
| mysql              |<br>
| performance_schema |<br>
| test               |<br>
+--------------------+<br>
4 rows in set (0.02 sec)<br></p>
<p><strong>7、创建hive元数据存储数据库，并授权hive用户访问</strong></p>
<p>--创建数据库hivemeta</p>
<p>mysql&gt; create database hivemeta;<br>
Query OK, 1 row affected (0.05 sec)<br><br></p>
<p>--老版本的mysql安装成hive的元数据存储必须要设置数据库字符集为latin1，否则在hive中使用时会报错</p>
<p>mysql&gt; alter database hivemeta character set latin1;<br>
Query OK, 1 row affected (0.00 sec)<br><br>
--创建mysql数据库登陆hive用户</p>
<p></p>
<p>mysql&gt; create user 'hive'@'%' identified by 'mysql123';</p>
<p>Query OK, 0 rows affected (0.05 sec)</p>
<br>
--设置权限<br>
mysql&gt; grant all privileges on *.* to 'hive'@'%' identified by 'mysql123' with grant option;
<p>Query OK, 0 rows affected (0.05 sec)</p>
<p><strong><span style="color:#ff0000;">--下面的重点让我跳了2晚上的坑，就是因为对mysql的用户登陆授权机制搞清晰；</span></strong></p>
<p><strong><span style="color:#ff0000;">grant all privileges on *.* to 'hive'@'localhost' identified by 'mysql123' with grant option;<br></span></strong></p>
<p><strong><span style="color:#ff0000;">grant all privileges on *.* to 'hive'@'master' identified by 'mysql123' with grant option;<br></span></strong></p>
<p><strong><span style="color:#666666;">正常情况下使用%授权应该指的是对所有主机授权，但是查询mysql的官方文档说明，%并不包括localhost和其他的域名，如果使用域名登陆必须要对域名进行单独授权；</span></strong></p>
<p><strong><span style="color:#666666;">%实际值得应该是IP地址。</span></strong></p>
<p><span style="color:#666666;"><strong>验证测试：</strong></span></p>
<p><span style="color:#666666;"><strong>mysql -h localhost -u hive -p</strong></span></p>
<p><span style="color:#666666;"><strong>mysql -h master -u hive -p</strong></span></p>
<p><span style="color:#666666;"><strong>如果不加上上面的授权，mysql登陆就会自己报错，hive其实是通过jdbc调用，如果使用mysql命令行都会报错，hive肯定也会调用失败。</strong></span></p>
<p>--刷新权限到数据库中，设置生效</p>
<p>mysql&gt; flush privileges;<br>
Query OK, 0 rows affected (0.03 sec)<br></p>
<p>--验证hive用户授权情况，应该出现允许%，localhost，master三条记录</p>
<p>mysql&gt; select user,host from mysql.user;<br>
+------+-----------+<br>
| user | host      |<br>
+------+-----------+<br>
| hive | %         |<br>
| root | 127.0.0.1 |<br>
| root | ::1       |<br>
|      | localhost |<br>
| hive | localhost |<br>
| root | localhost |<br>
|      | master    |<br>
| hive | master    |<br>
| root | master    |<br>
+------+-----------+<br>
9 rows in set (0.10 sec)<br></p>
<p>8、配置集群hive的操作系统环境变量</p>
<p>vim /home/hadoop/.bashrc</p>
<p>export JAVA_HOME=/usr/java/jdk1.8.0_121<br>
export HADOOP_HOME=/home/hadoop/bigdata/hadoop<br>
export HADOOP_USER_NAME=hadoop<br>
export HIVE_HOME=/home/hadoop/bigdata/hive<br>
export PATH=$JAVA_HOME/bin:$HADOOP_HOME/bin:$HADOOP_HOME/sbin:$HIVE_HOME/bin:$PATH<br></p>
<p>--导入环境变量文件</p>
<p>source /home/hadoop/.bashrc</p>
<p>--将环境配置文件拷贝到slave01、slave02的对应目录下</p>
<p>[hadoop@master ~]$ scp .bashrc hadoop@slave01:/home/hadoop/<br>
.bashrc                                       100%  477     0.5KB/s   00:00    <br>
[hadoop@master ~]$ scp .bashrc hadoop@slave02:/home/hadoop/<br>
.bashrc                                       100%  477     0.5KB/s   00:00 <br></p>
<p><br></p>
<p>9、解压缩hive的安装文件，开始hive的解压安装</p>
<p>cd /home/hadoop/bigdata/</p>
<p>tar -zxf apache-hive-1.2.1-bin.tar.gz</p>
<p>mv apache-hive-1.2.1-bin hive</p>
<p>cd /home/hadoop/bigdata/hive/conf</p>
<p>--根据配置文件模板创建hive运行配置文件方便下一步填充必要内容</p>
<p>cp hive-default.xml.template hive-site.xml</p>
<p>cp hive-env.sh.template hive-env.sh</p>
<p>cp hive-log4j.properties.template hive-log4j.properties</p>
<p><br></p>
<p>10、复制mysql的jdbc驱动JAR包文件到/home/hadoop/bigdata/hive/lib目录下</p>
<p>[hadoop@master lib]$ ls -la mysql-connector-java-5.1.7-bin.jar <br>
-rw-rw-r-- 1 hadoop hadoop 709922 Apr  5 06:12 mysql-connector-java-5.1.7-bin.jar<br></p>
<p>--因为mysql安装时默认不带这个jar包文件，所以需要单独从oracle的网站上去下载，没有这个JAR包hive无法正常调用mysql根本无法启动。</p>
<p><br></p>
<p>11、修改hive的配置文件</p>
<p>--配置hive运行环境</p>
<p>vim /home/hadoop/bigdata/hive/conf/hive-env.sh</p>
<p>export HADOOP_HOME=/home/hadoop/bigdata/hadoop<br>
export HIVE_CONF_DIR=/home/hadoop/bigdata/hive/conf<br></p>
<p>--配置hive日志log4j框架的日志存储路径</p>
<p>vim /home/hadoop/bigdata/hive/conf/hive-log4j.properties</p>
<p>hive.log.threshold=ALL<br>
hive.root.logger=INFO,DRFA<br>
hive.log.dir=/home/hadoop/bigdata/hive/log<br>
hive.log.file=hive.log<br></p>
<p></p>
<p>--配置hive运行主配置文件</p>
<p>vim /home/hadoop/bigdata/hive/conf/hive-site.xml<br></p>
<p><br></p>
<p> &lt;property&gt;<br>
    &lt;name&gt;hive.metastore.warehouse.dir&lt;/name&gt;<br>
    &lt;value&gt;hdfs://master:9000/hive/warehouse&lt;/value&gt;<br>
    &lt;description&gt;location of default database for the warehouse&lt;/description&gt;<br>
  &lt;/property&gt;<br>
  &lt;property&gt;<br>
    &lt;name&gt;hive.metastore.uris&lt;/name&gt;<br>
    &lt;value&gt;thrift://master:9083&lt;/value&gt;<br>
    &lt;description&gt;Thrift URI for the remote metastore. Used by metastore client to connect to remote metastore.&lt;/description&gt;<br>
  &lt;/property&gt;<br></p>
<p>--Hive使用此目录来存储用于查询的不同map/reduce阶段的计划以及存储这些阶段的中间输出，这里是hdfs的目的地。<br></p>
<p>  &lt;property&gt;<br>
    &lt;name&gt;hive.exec.scratchdir&lt;/name&gt;<br>
    &lt;value&gt;hdfs://master:9000/hive/scratchdir&lt;/value&gt;<br>
    &lt;description&gt;HDFS root scratch dir for Hive jobs which gets created with write all (733) permission. For each connecting user, an HDFS scratch dir: ${hive.exec.scratchdir}/&amp;lt;username&amp;gt; is created, with ${hive.scratch.dir.permission}.&lt;/description&gt;<br>
  &lt;/property&gt;<br></p>
<p>  &lt;property&gt;<br>
    &lt;name&gt;hive.querylog.location&lt;/name&gt;<br>
    &lt;value&gt;/home/hadoop/bigdata/hive/logs&lt;/value&gt;<br>
    &lt;description&gt;Location of Hive run time structured log file&lt;/description&gt;<br>
  &lt;/property&gt;<br></p>
<p>&lt;property&gt;<br>
    &lt;name&gt;javax.jdo.option.ConnectionURL&lt;/name&gt;<br>
    &lt;value&gt;jdbc:mysql://master:3306/hivemeta?createDatabaseIfNotExist=true&lt;/value&gt;<br>
    &lt;description&gt;JDBC connect string for a JDBC metastore&lt;/description&gt;<br>
  &lt;/property&gt;<br></p>
<p>  &lt;property&gt;<br>
    &lt;name&gt;javax.jdo.option.ConnectionDriverName&lt;/name&gt;<br>
    &lt;value&gt;com.mysql.jdbc.Driver&lt;/value&gt;<br>
    &lt;description&gt;Driver class name for a JDBC metastore&lt;/description&gt;<br>
  &lt;/property&gt;<br></p>
<p>  &lt;property&gt;<br>
    &lt;name&gt;javax.jdo.option.ConnectionUserName&lt;/name&gt;<br>
    &lt;value&gt;hive&lt;/value&gt;<br>
    &lt;description&gt;Username to use against metastore database&lt;/description&gt;<br>
  &lt;/property&gt;<br></p>
<p> &lt;property&gt;<br>
    &lt;name&gt;javax.jdo.option.ConnectionPassword&lt;/name&gt;<br>
    &lt;value&gt;mysql123&lt;/value&gt;<br>
    &lt;description&gt;password to use against metastore database&lt;/description&gt;<br>
  &lt;/property&gt;<br></p>
<p>--当Hive以本地模式运行时，此目录用于临时文件<br></p>
<p>&lt;property&gt;</p>
<p>    &lt;name&gt;hive.exec.local.scratchdir&lt;/name&gt;</p>
<p>    &lt;value&gt;/home/hadoop/bigdata/hive/logs&lt;/value&gt;<br>
    &lt;description&gt;Local scratch space for Hive jobs&lt;/description&gt;<br>
  &lt;/property&gt;<br>
  &lt;property&gt;<br>
    &lt;name&gt;hive.downloaded.resources.dir&lt;/name&gt;<br>
    &lt;value&gt;/home/hadoop/bigdata/hive/logs&lt;/value&gt;<br>
    &lt;description&gt;Temporary local directory for added resources in the remote file system.&lt;/description&gt;<br>
  &lt;/property&gt;<br></p>
<p><br></p>
<p>12、将hive安装文件拷贝到slave01上，使得slave01可以使用hive</p>
<p>scp -r /home/hadoop/bigdata/hive hadoop@slave01:/home/hadoop/bigdata/<br></p>
<p><br></p>
<p>13、在master上启动metastore服务</p>
<p>[hadoop@master ~]$ hive --service metastore<br>
Starting Hive Metastore Server<br></p>
<p>--另起一个终端窗口验证hive的metastore服务是否启动成功</p>
<p>[hadoop@master lib]$ ps -ef | grep hive<br>
hadoop    29327  27579  0 04:17 pts/1    00:00:24 /usr/java/jdk1.8.0_121/bin/java -Xmx256m -Djava.net.preferIPv4Stack=true -Dhadoop.log.dir=/home/hadoop/bigdata/hadoop/logs -Dhadoop.log.file=hadoop.log -Dhadoop.home.dir=/home/hadoop/bigdata/hadoop -Dhadoop.id.str=hadoop
 -Dhadoop.root.logger=INFO,console -Djava.library.path=/home/hadoop/bigdata/hadoop/lib/native -Dhadoop.policy.file=hadoop-policy.xml -Djava.net.preferIPv4Stack=true -Xmx512m -Dhadoop.security.logger=INFO,NullAppender org.apache.hadoop.util.RunJar /home/hadoop/bigdata/hive/lib/hive-service-1.2.1.jar
 org.apache.hadoop.hive.metastore.HiveMetaStore<br></p>
<p>--如果验证正常就可以使用下面的方法在后台启动服务</p>
<p>nohup hive -service metastore&amp;</p>
<p><br></p>
<p>14、在slave01上启动Hiveserver2服务</p>
<p>[hadoop@slave01 ~]$ hive --service hiveserver2<br></p>
<p>--另起一个终端窗口验证hive的hiveserver2服务是否启动成功</p>
<p></p>
<p>[hadoop@slave01 bin]$ ps -ef | grep hive<br>
hadoop    18552  16412  2 07:08 pts/2    00:00:07 /usr/java/jdk1.8.0_121/bin/java -Xmx256m -Djava.net.preferIPv4Stack=true -Dhadoop.log.dir=/home/hadoop/bigdata/hadoop/logs -Dhadoop.log.file=hadoop.log -Dhadoop.home.dir=/home/hadoop/bigdata/hadoop -Dhadoop.id.str=hadoop
 -Dhadoop.root.logger=INFO,console -Djava.library.path=/home/hadoop/bigdata/hadoop/lib/native -Dhadoop.policy.file=hadoop-policy.xml -Djava.net.preferIPv4Stack=true -Xmx512m -Dhadoop.security.logger=INFO,NullAppender org.apache.hadoop.util.RunJar /home/hadoop/bigdata/hive/lib/hive-service-1.2.1.jar
 org.apache.hive.service.server.HiveServer2<br></p>
<p>--同样如果启动正常可以使用下面的后台启动方法</p>
<p>nohup hive  --service hiveserver2&amp;</p>
<p><br></p>
<p>15、在master或slave01上进入hive客户端验证</p>
<p>hive</p>
<p>hive&gt; create database test;<br>
OK<br>
Time taken: 0.392 seconds<br>
hive&gt; show databases;<br>
OK<br>
default<br>
test<br>
Time taken: 0.023 seconds, Fetched: 2 row(s)<br></p>
<p><br></p>
<p>三、故障排错</p>
<p>--在master主机上使用master域名无法登陆，还是mysql授权问题导致</p>
<p>[hadoop@master ~]$ mysql -h master -u hive -pmysql123<br>
ERROR 1045 (28000): Access denied for user 'hive'@'master' (using password: YES)<br></p>
<p>--mysql对用户授权出错导致hive启动报错，解决方案参见第7步</p>
<p>Exception in thread "main" java.lang.RuntimeException: java.net.ConnectException: Call From slave01/192.168.10.167 to master:9000 failed on connection exception: java.net.ConnectException: Connection refused; For more details see:  http://wiki.apache.org/hadoop/ConnectionRefused<br><span></span>at org.apache.hadoop.hive.ql.session.SessionState.start(SessionState.java:522)<br><span></span>at org.apache.hadoop.hive.cli.CliDriver.run(CliDriver.java:677)<br><span></span>at org.apache.hadoop.hive.cli.CliDriver.main(CliDriver.java:621)<br><span></span>at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)<br><span></span>at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)<br><span></span>at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)<br><span></span>at java.lang.reflect.Method.invoke(Method.java:498)<br><span></span>at org.apache.hadoop.util.RunJar.run(RunJar.java:221)<br><span></span>at org.apache.hadoop.util.RunJar.main(RunJar.java:136)<br>
Caused by: java.net.ConnectException: Call From slave01/192.168.10.167 to master:9000 failed on connection exception: java.net.ConnectException: Connection refused; For more details see:  http://wiki.apache.org/hadoop/ConnectionRefused<br><span></span>at sun.reflect.NativeConstructorAccessorImpl.newInstance0(Native Method)<br><span></span>at sun.reflect.NativeConstructorAccessorImpl.newInstance(NativeConstructorAccessorImpl.java:62)<br><span></span>at sun.reflect.DelegatingConstructorAccessorImpl.newInstance(DelegatingConstructorAccessorImpl.java:45)<br><span></span>at java.lang.reflect.Constructor.newInstance(Constructor.java:423)<br><span></span>at org.apache.hadoop.net.NetUtils.wrapWithMessage(NetUtils.java:792)<br><span></span>at org.apache.hadoop.net.NetUtils.wrapException(NetUtils.java:732)<br><span></span>at org.apache.hadoop.ipc.Client.call(Client.java:1479)<br><span></span>at org.apache.hadoop.ipc.Client.call(Client.java:1412)<br><span></span>at org.apache.hadoop.ipc.ProtobufRpcEngine$Invoker.invoke(ProtobufRpcEngine.java:229)<br><span></span>at com.sun.proxy.$Proxy12.getFileInfo(Unknown Source)<br><span></span>at org.apache.hadoop.hdfs.protocolPB.ClientNamenodeProtocolTranslatorPB.getFileInfo(ClientNamenodeProtocolTranslatorPB.java:771)<br><span></span>at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)<br><span></span>at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)<br><span></span>at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)<br><span></span>at java.lang.reflect.Method.invoke(Method.java:498)<br></p>
<p>--没有配置hive-site.xml文件中的hive.exec.local.scratchdir与hive.downloaded.resources.dir导致hive启动报错，参见第11步；</p>
<p>Logging initialized using configuration in file:/home/hadoop/bigdata/hive/conf/hive-log4j.properties<br>
Exception in thread "main" java.lang.RuntimeException: java.lang.IllegalArgumentException: java.net.URISyntaxException: Relative path in absolute URI: ${system:java.io.tmpdir%7D/$%7Bsystem:user.name%7D<br><span></span>at org.apache.hadoop.hive.ql.session.SessionState.start(SessionState.java:522)<br><span></span>at org.apache.hadoop.hive.cli.CliDriver.run(CliDriver.java:677)<br><span></span>at org.apache.hadoop.hive.cli.CliDriver.main(CliDriver.java:621)<br><span></span>at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)<br><span></span>at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)<br><span></span>at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)<br><span></span>at java.lang.reflect.Method.invoke(Method.java:498)<br><span></span>at org.apache.hadoop.util.RunJar.run(RunJar.java:221)<br><span></span>at org.apache.hadoop.util.RunJar.main(RunJar.java:136)<br>
Caused by: java.lang.IllegalArgumentException: java.net.URISyntaxException: Relative path in absolute URI: ${system:java.io.tmpdir%7D/$%7Bsystem:user.name%7D<br><span></span>at org.apache.hadoop.fs.Path.initialize(Path.java:205)<br><span></span>at org.apache.hadoop.fs.Path.&lt;init&gt;(Path.java:171)<br><span></span>at org.apache.hadoop.hive.ql.session.SessionState.createSessionDirs(SessionState.java:563)<br><span></span>at org.apache.hadoop.hive.ql.session.SessionState.start(SessionState.java:508)<br><span></span>... 8 more<br>
Caused by: java.net.URISyntaxException: Relative path in absolute URI: ${system:java.io.tmpdir%7D/$%7Bsystem:user.name%7D<br><span></span>at java.net.URI.checkPath(URI.java:1823)<br><span></span>at java.net.URI.&lt;init&gt;(URI.java:745)<br><span></span>at org.apache.hadoop.fs.Path.initialize(Path.java:202)<br><span></span>... 11 more<br></p>
<p>四、总结</p>
<p>       hive的安装与第三方的环境结合太过紧密，尤其是mysql，如果对mysql的安装授权不太清楚会走很多弯路，首先在master节点上解压安装配置好hive，并且不要忘记下载mysql的jdbc驱动文件并把文件放置在hive的lib目录下，不要着急启动hive的服务，首先检查mysql的用户授权是否完整，并在客户端下测试使用主机名是否可以正常登陆，登陆后是否可以打开hivemeta数据库，这一步测试完成后再开始在master节点上启动hive的metastore服务，一开始不要使用后台启动方法，如果出错可以直接看到抛出的异常方便查找故障，所有的安装调试过程需要对每一步的原理与细节弄得非常清晰否则到处都是坑。</p>
<p><br></p>
<p><br></p>
<p><br></p>
            </div>
                </div>