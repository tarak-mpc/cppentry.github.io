---
layout:     post
title:      hive与HBase整合
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/u010022051/article/details/51924229				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-size:18px;">用hbase做数据库,但由于hbase没有类sql查询方式,所以操作和计算数据非常不方便,于是整合hive,让hive支撑在hbase数据库层面进行hql查询。让Hbase和Hive能互相访问，让Hadoop/Hbase/Hive协同工作，合为一体。 </span></p>
<p><span style="font-size:18px;"></span></p>
<p>首先需要把hive/lib下的hbase包替换成安装的hbase的，需要如下几下：</p>
<p>hbase-client-1.2.0.jar</p>
<p>hbase-common-1.2.0.jar</p>
<p>hbase-common-1.2.0-tests.jar<br></p>
<p>hbase-protocol-1.2.0.jar<br></p>
<p>hbase-server-1.2.0.jar<br></p>
<p>htrace-core-3.1.0-incubating.jar<br></p>
<p><br></p>
<p>在hive-site.xml中添加如下内容：</p>
<p>&lt;property&gt;<br><span></span>&lt;name&gt;hbase.zookeeper.quorum&lt;/name&gt;<br><span></span>&lt;value&gt;Master1,Master2,Slave1,Slave2,Slave3&lt;/value&gt;<br>
&lt;/property&gt;<br></p>
<p>如果不加入这个配置，hive中select * from table可以查到数据，但是其它的HQL一走MR，就查不到了，显然是在查HDFS的时候出现了问题，所以这里必须配置上这个<br></p>
<p><br></p>
<p>用关联的方式启动hive</p>
<p>[hadoop@Master1 apache-hive-1.2.1-bin]$ cd bin/<br>
[hadoop@Master1 bin]$ ./hive -hiveconf hbase.master=Master1:6000<br><br><br>
Logging initialized using configuration in jar:file:/home/hadoop/apache-hive-1.2.1-bin/lib/hive-common-1.2.1.jar!/hive-log4j.properties<br>
hive&gt; <br></p>
<p>进入hive控制台建立关联表</p>
<p><br></p>
<p>。。。。。</p>
<p>暂时出现错误未解决</p>
<p><br></p>
<p>hive&gt; CREATE TABLE hbase_hive_1(key int, value string) <br>
    &gt; STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'<br>
    &gt; WITH SERDEPROPERTIES ("hbase.columns.mapping" = ":key,cf1:val")<br>
    &gt; TBLPROPERTIES ("hbase.table.name" = "xyz");<br>
FAILED: Execution Error, return code 1 from org.apache.hadoop.hive.ql.exec.DDLTask. MetaException(message:MetaException(message:java.io.IOException: java.lang.reflect.InvocationTargetException<br>
        at org.apache.hadoop.hbase.client.ConnectionFactory.createConnection(ConnectionFactory.java:240)<br>
        at org.apache.hadoop.hbase.client.ConnectionManager.createConnection(ConnectionManager.java:431)<br>
        at org.apache.hadoop.hbase.client.ConnectionManager.createConnection(ConnectionManager.java:424)<br>
        at org.apache.hadoop.hbase.client.ConnectionManager.getConnectionInternal(ConnectionManager.java:302)<br>
        at org.apache.hadoop.hbase.client.HBaseAdmin.&lt;init&gt;(HBaseAdmin.java:232)<br>
        at org.apache.hadoop.hive.hbase.HBaseStorageHandler.getHBaseAdmin(HBaseStorageHandler.java:120)<br>
        at org.apache.hadoop.hive.hbase.HBaseStorageHandler.preCreateTable(HBaseStorageHandler.java:200)<br>
        at org.apache.hadoop.hive.metastore.HiveMetaStoreClient.createTable(HiveMetaStoreClient.java:664)<br>
        at org.apache.hadoop.hive.metastore.HiveMetaStoreClient.createTable(HiveMetaStoreClient.java:657)<br>
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)<br>
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:57)<br>
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)<br>
        at java.lang.reflect.Method.invoke(Method.java:606)<br>
        at org.apache.hadoop.hive.metastore.RetryingMetaStoreClient.invoke(RetryingMetaStoreClient.java:156)<br>
        at com.sun.proxy.$Proxy8.createTable(Unknown Source)<br>
        at org.apache.hadoop.hive.ql.metadata.Hive.createTable(Hive.java:714)<br>
        at org.apache.hadoop.hive.ql.exec.DDLTask.createTable(DDLTask.java:4135)<br>
        at org.apache.hadoop.hive.ql.exec.DDLTask.execute(DDLTask.java:306)<br>
        at org.apache.hadoop.hive.ql.exec.Task.executeTask(Task.java:160)<br>
        at org.apache.hadoop.hive.ql.exec.TaskRunner.runSequential(TaskRunner.java:88)<br>
        at org.apache.hadoop.hive.ql.Driver.launchTask(Driver.java:1653)<br>
        at org.apache.hadoop.hive.ql.Driver.execute(Driver.java:1412)<br>
        at org.apache.hadoop.hive.ql.Driver.runInternal(Driver.java:1195)<br>
        at org.apache.hadoop.hive.ql.Driver.run(Driver.java:1059)<br>
        at org.apache.hadoop.hive.ql.Driver.run(Driver.java:1049)<br>
        at org.apache.hadoop.hive.cli.CliDriver.processLocalCmd(CliDriver.java:213)<br>
        at org.apache.hadoop.hive.cli.CliDriver.processCmd(CliDriver.java:165)<br>
        at org.apache.hadoop.hive.cli.CliDriver.processLine(CliDriver.java:376)<br>
        at org.apache.hadoop.hive.cli.CliDriver.executeDriver(CliDriver.java:736)<br>
        at org.apache.hadoop.hive.cli.CliDriver.run(CliDriver.java:681)<br>
        at org.apache.hadoop.hive.cli.CliDriver.main(CliDriver.java:621)<br>
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)<br>
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:57)<br>
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)<br>
        at java.lang.reflect.Method.invoke(Method.java:606)<br>
        at org.apache.hadoop.util.RunJar.run(RunJar.java:221)<br>
        at org.apache.hadoop.util.RunJar.main(RunJar.java:136)<br>
Caused by: java.lang.reflect.InvocationTargetException<br>
        at sun.reflect.NativeConstructorAccessorImpl.newInstance0(Native Method)<br>
        at sun.reflect.NativeConstructorAccessorImpl.newInstance(NativeConstructorAccessorImpl.java:57)<br>
        at sun.reflect.DelegatingConstructorAccessorImpl.newInstance(DelegatingConstructorAccessorImpl.java:45)<br>
        at java.lang.reflect.Constructor.newInstance(Constructor.java:526)<br>
        at org.apache.hadoop.hbase.client.ConnectionFactory.createConnection(ConnectionFactory.java:238)<br>
        ... 36 more<br>
Caused by: java.lang.NoClassDefFoundError: io/netty/channel/EventLoopGroup<br>
        at java.lang.Class.forName0(Native Method)<br>
        at java.lang.Class.forName(Class.java:274)<br>
        at org.apache.hadoop.conf.Configuration.getClassByNameOrNull(Configuration.java:2093)<br>
        at org.apache.hadoop.conf.Configuration.getClassByName(Configuration.java:2058)<br>
        at org.apache.hadoop.conf.Configuration.getClass(Configuration.java:2152)<br>
        at org.apache.hadoop.conf.Configuration.getClass(Configuration.java:2178)<br>
        at org.apache.hadoop.hbase.client.ConnectionManager$HConnectionImplementation.&lt;init&gt;(ConnectionManager.java:656)<br>
        ... 41 more<br>
Caused by: java.lang.ClassNotFoundException: io.netty.channel.EventLoopGroup<br>
        at java.net.URLClassLoader$1.run(URLClassLoader.java:366)<br>
        at java.net.URLClassLoader$1.run(URLClassLoader.java:355)<br>
        at java.security.AccessController.doPrivileged(Native Method)<br>
        at java.net.URLClassLoader.findClass(URLClassLoader.java:354)<br>
        at java.lang.ClassLoader.loadClass(ClassLoader.java:425)<br>
        at sun.misc.Launcher$AppClassLoader.loadClass(Launcher.java:308)<br>
        at java.lang.ClassLoader.loadClass(ClassLoader.java:358)<br>
        ... 48 more<br>
)<br>
        at org.apache.hadoop.hive.hbase.HBaseStorageHandler.getHBaseAdmin(HBaseStorageHandler.java:124)<br>
        at org.apache.hadoop.hive.hbase.HBaseStorageHandler.preCreateTable(HBaseStorageHandler.java:200)<br>
        at org.apache.hadoop.hive.metastore.HiveMetaStoreClient.createTable(HiveMetaStoreClient.java:664)<br>
        at org.apache.hadoop.hive.metastore.HiveMetaStoreClient.createTable(HiveMetaStoreClient.java:657)<br>
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)<br>
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:57)<br>
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)<br>
        at java.lang.reflect.Method.invoke(Method.java:606)<br>
        at org.apache.hadoop.hive.metastore.RetryingMetaStoreClient.invoke(RetryingMetaStoreClient.java:156)<br>
        at com.sun.proxy.$Proxy8.createTable(Unknown Source)<br>
        at org.apache.hadoop.hive.ql.metadata.Hive.createTable(Hive.java:714)<br>
        at org.apache.hadoop.hive.ql.exec.DDLTask.createTable(DDLTask.java:4135)<br>
        at org.apache.hadoop.hive.ql.exec.DDLTask.execute(DDLTask.java:306)<br>
        at org.apache.hadoop.hive.ql.exec.Task.executeTask(Task.java:160)<br>
        at org.apache.hadoop.hive.ql.exec.TaskRunner.runSequential(TaskRunner.java:88)<br>
        at org.apache.hadoop.hive.ql.Driver.launchTask(Driver.java:1653)<br>
        at org.apache.hadoop.hive.ql.Driver.execute(Driver.java:1412)<br>
        at org.apache.hadoop.hive.ql.Driver.runInternal(Driver.java:1195)<br>
        at org.apache.hadoop.hive.ql.Driver.run(Driver.java:1059)<br>
        at org.apache.hadoop.hive.ql.Driver.run(Driver.java:1049)<br>
        at org.apache.hadoop.hive.cli.CliDriver.processLocalCmd(CliDriver.java:213)<br>
        at org.apache.hadoop.hive.cli.CliDriver.processCmd(CliDriver.java:165)<br>
        at org.apache.hadoop.hive.cli.CliDriver.processLine(CliDriver.java:376)<br>
        at org.apache.hadoop.hive.cli.CliDriver.executeDriver(CliDriver.java:736)<br>
        at org.apache.hadoop.hive.cli.CliDriver.run(CliDriver.java:681)<br>
        at org.apache.hadoop.hive.cli.CliDriver.main(CliDriver.java:621)<br>
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)<br>
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:57)<br>
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)<br>
        at java.lang.reflect.Method.invoke(Method.java:606)<br>
        at org.apache.hadoop.util.RunJar.run(RunJar.java:221)<br>
        at org.apache.hadoop.util.RunJar.main(RunJar.java:136)<br>
)<br></p>
<br>            </div>
                </div>