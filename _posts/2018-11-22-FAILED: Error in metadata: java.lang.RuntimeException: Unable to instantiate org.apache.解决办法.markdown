---
layout:     post
title:      FAILED: Error in metadata: java.lang.RuntimeException: Unable to instantiate org.apache.解决办法
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
安装之后 把访问权限都配置好 ：</div>
<div style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
GRANT ALL PRIVILEGES ON*.* TO <a href="mailto:'%20rel=" nofollow style="color:rgb(51,102,153);text-decoration:none;">'hive'@'%'</a> Identified by 'hive';  <br>
GRANT ALL PRIVILEGES ON*.* TO <a href="mailto:'%20rel=" nofollow style="color:rgb(51,102,153);text-decoration:none;">'hive'@'localhost'</a> Identified by 'hive';  <br>
GRANT ALL PRIVILEGES ON*.* TO <a href="mailto:'%20rel=" nofollow style="color:rgb(51,102,153);text-decoration:none;">'hive'@'127.0.0.1'</a> Identified by 'hive';  <br>
本机地址： 192.168.103.43  机器名字：<u><span style="color:rgb(0,102,204);">192-168-103-43</span></u><br>
flush privileges;</div>
<div style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
启动hive 发生下面的错误：</div>
<div style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
 </div>
<div style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<p>hive&gt; show tables;</p>
</div>
<div style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
FAILED: Error in metadata: java.lang.RuntimeException: Unable to instantiate org.apache.hadoop.hive.metastore.HiveMetaStoreClient</div>
<div id="_mcePaste" style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
FAILED: Execution Error, return code 1 from org.apache.hadoop.hive.ql.exec.DDLTask</div>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
FAILED: Error in metadata: java.lang.RuntimeException: Unable to instantiate org.apache.hadoop.hive.metastore.HiveMetaStoreClientFAILED: Execution Error, return code 1 from org.apache.hadoop.hive.ql.exec.DDLTask</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
 </p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
cd ${HIVE_HOME}/bin</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="color:rgb(255,0,0);">./hive -hiveconf hive.root.logger=DEBUG,console</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
hive&gt; show tables;</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
得到如下的错误信息(当然 不同的问题所产生的日志是不同的)：</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
Caused by: javax.jdo.JDOFatalDataStoreException: Access denied for user <a href="mailto:'%20rel=" nofollow style="color:rgb(51,102,153);text-decoration:none;">'hive'@'192-168-103-43'</a> (using password: YES)<br>
NestedThrowables:<br>
java.sql.SQLException: Access denied for user <a href="mailto:'%20rel=" nofollow style="color:rgb(51,102,153);text-decoration:none;">'hive'@'192-168-103-43'</a> (using password: YES)<br>
        at org.datanucleus.jdo.NucleusJDOHelper.getJDOExceptionForNucleusException(NucleusJDOHelper.java:298)<br>
        at org.datanucleus.jdo.JDOPersistenceManagerFactory.freezeConfiguration(JDOPersistenceManagerFactory.java:601)<br>
        at org.datanucleus.jdo.JDOPersistenceManagerFactory.createPersistenceManagerFactory(JDOPersistenceManagerFactory.java:286)<br>
        at org.datanucleus.jdo.JDOPersistenceManagerFactory.getPersistenceManagerFactory(JDOPersistenceManagerFactory.java:182)<br>
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)<br>
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:39)<br>
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:25)<br>
        at java.lang.reflect.Method.invoke(Method.java:597)<br>
        at javax.jdo.JDOHelper$16.run(JDOHelper.java:1958)<br>
        at java.security.AccessController.doPrivileged(Native Method)<br>
        at javax.jdo.JDOHelper.invoke(JDOHelper.java:1953)<br>
        at javax.jdo.JDOHelper.invokeGetPersistenceManagerFactoryOnImplementation(JDOHelper.java:1159)<br>
        at javax.jdo.JDOHelper.getPersistenceManagerFactory(JDOHelper.java:803)<br>
        at javax.jdo.JDOHelper.getPersistenceManagerFactory(JDOHelper.java:698)<br>
        at org.apache.hadoop.hive.metastore.ObjectStore.getPMF(ObjectStore.java:262)<br>
        at org.apache.hadoop.hive.metastore.ObjectStore.getPersistenceManager(ObjectStore.java:291)<br>
        at org.apache.hadoop.hive.metastore.ObjectStore.initialize(ObjectStore.java:224)<br>
        at org.apache.hadoop.hive.metastore.ObjectStore.setConf(ObjectStore.java:199)<br>
        at org.apache.hadoop.util.ReflectionUtils.setConf(ReflectionUtils.java:62)<br>
        at org.apache.hadoop.util.ReflectionUtils.newInstance(ReflectionUtils.java:117)<br>
        at org.apache.hadoop.hive.metastore.RetryingRawStore.&lt;init&gt;(RetryingRawStore.java:62)<br>
        at org.apache.hadoop.hive.metastore.RetryingRawStore.getProxy(RetryingRawStore.java:71)<br>
        at org.apache.hadoop.hive.metastore.HiveMetaStore$HMSHandler.newRawStore(HiveMetaStore.java:413)<br>
        at org.apache.hadoop.hive.metastore.HiveMetaStore$HMSHandler.getMS(HiveMetaStore.java:401)<br>
        at org.apache.hadoop.hive.metastore.HiveMetaStore$HMSHandler.createDefaultDB(HiveMetaStore.java:439)<br>
        at org.apache.hadoop.hive.metastore.HiveMetaStore$HMSHandler.init(HiveMetaStore.java:325)<br>
        at org.apache.hadoop.hive.metastore.HiveMetaStore$HMSHandler.&lt;init&gt;(HiveMetaStore.java:285)<br>
        at org.apache.hadoop.hive.metastore.RetryingHMSHandler.&lt;init&gt;(RetryingHMSHandler.java:53)<br>
        at org.apache.hadoop.hive.metastore.RetryingHMSHandler.getProxy(RetryingHMSHandler.java:58)<br>
        at org.apache.hadoop.hive.metastore.HiveMetaStore.newHMSHandler(HiveMetaStore.java:4102)<br>
        at org.apache.hadoop.hive.metastore.HiveMetaStoreClient.&lt;init&gt;(HiveMetaStoreClient.java:121)<br>
        ... 28 more</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
 </p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
发现数据库的权限  HIVE需要的是</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<a href="mailto:'%20rel=" nofollow style="color:rgb(51,102,153);text-decoration:none;">'hive'@'192-168-103-43'</a> 这个IP地址</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
然后试着在mysql中加上权限：</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
GRANT ALL PRIVILEGES ON*.* TO <a href="mailto:'%20rel=" nofollow style="color:rgb(51,102,153);text-decoration:none;">'hive'@'192-168-103-43'</a> Identified by 'hive'; </p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
flush privileges;</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
 </p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
再次登录hive</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
hive&gt; show tables;<br>
OK</p>
            </div>
                </div>