---
layout:     post
title:      安装hive后使用mysql作为数据库无法正常启动问题
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>安装了mysql，并且成功创建hive用户和hive数据库后</p>
<p>下载hive apache-hive-0.13.1-bin.tar.gz 版本，解压，并配置hive-site.xml和hive-env.xml文件后，启动hive，发现hive无法启动，报错如下：</p>
<p>Exception in thread "main" java.lang.RuntimeException: java.lang.RuntimeException: Unable to instantiate org.apache.hadoop.hive.metastore.HiveMetaStoreClient<br>
        at org.apache.hadoop.hive.ql.session.SessionState.start(SessionState.java:346)<br>
        at org.apache.hadoop.hive.cli.CliDriver.run(CliDriver.java:681)<br>
        at org.apache.hadoop.hive.cli.CliDriver.main(CliDriver.java:625)<br>
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)<br>
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:57)<br>
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)<br>
        at java.lang.reflect.Method.invoke(Method.java:606)<br>
        at org.apache.hadoop.util.RunJar.main(RunJar.java:212)<br>
Caused by: java.lang.RuntimeException: Unable to instantiate org.apache.hadoop.hive.metastore.HiveMetaStoreClient<br>
        at org.apache.hadoop.hive.metastore.MetaStoreUtils.newInstance(MetaStoreUtils.java:1412)<br>
        at org.apache.hadoop.hive.metastore.RetryingMetaStoreClient.&lt;init&gt;(RetryingMetaStoreClient.java:62)<br>
        at org.apache.hadoop.hive.metastore.RetryingMetaStoreClient.getProxy(RetryingMetaStoreClient.java:72)<br>
        at org.apache.hadoop.hive.ql.metadata.Hive.createMetaStoreClient(Hive.java:2453)<br>
        at org.apache.hadoop.hive.ql.metadata.Hive.getMSC(Hive.java:2465)<br>
        at org.apache.hadoop.hive.ql.session.SessionState.start(SessionState.java:340)<br>
        ... 7 more<br>
Caused by: java.lang.reflect.InvocationTargetException<br>
        at sun.reflect.NativeConstructorAccessorImpl.newInstance0(Native Method)<br>
        at sun.reflect.NativeConstructorAccessorImpl.newInstance(NativeConstructorAccessorImpl.java:57)<br>
        at sun.reflect.DelegatingConstructorAccessorImpl.newInstance(DelegatingConstructorAccessorImpl.java:45)<br>
        at java.lang.reflect.Constructor.newInstance(Constructor.java:526)<br>
        at org.apache.hadoop.hive.metastore.MetaStoreUtils.newInstance(MetaStoreUtils.java:1410)<br>
        ... 12 more<br>
Caused by: javax.jdo.JDOFatalInternalException: Error creating transactional connection factory<br>
NestedThrowables:<br>
java.lang.reflect.InvocationTargetException<br>
        at org.datanucleus.api.jdo.NucleusJDOHelper.getJDOExceptionForNucleusException(NucleusJDOHelper.java:587)<br>
        at org.datanucleus.api.jdo.JDOPersistenceManagerFactory.freezeConfiguration(JDOPersistenceManagerFactory.java:788)<br>
        at org.datanucleus.api.jdo.JDOPersistenceManagerFactory.createPersistenceManagerFactory(JDOPersistenceManagerFactory.java:333)<br>
        at org.datanucleus.api.jdo.JDOPersistenceManagerFactory.getPersistenceManagerFactory(JDOPersistenceManagerFactory.java:202)<br>
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)<br>
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:57)<br>
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)<br>
        at java.lang.reflect.Method.invoke(Method.java:606)<br>
        at javax.jdo.JDOHelper$16.run(JDOHelper.java:1965)<br>
        at java.security.AccessController.doPrivileged(Native Method)<br>
        at javax.jdo.JDOHelper.invoke(JDOHelper.java:1960)<br>
        at javax.jdo.JDOHelper.invokeGetPersistenceManagerFactoryOnImplementation(JDOHelper.java:1166)<br>
        at javax.jdo.JDOHelper.getPersistenceManagerFactory(JDOHelper.java:808)<br>
        at javax.jdo.JDOHelper.getPersistenceManagerFactory(JDOHelper.java:701)<br>
        at org.apache.hadoop.hive.metastore.ObjectStore.getPMF(ObjectStore.java:310)<br>
        at org.apache.hadoop.hive.metastore.ObjectStore.getPersistenceManager(ObjectStore.java:339)<br>
        at org.apache.hadoop.hive.metastore.ObjectStore.initialize(ObjectStore.java:248)<br>
        at org.apache.hadoop.hive.metastore.ObjectStore.setConf(ObjectStore.java:223)<br>
        at org.apache.hadoop.util.ReflectionUtils.setConf(ReflectionUtils.java:73)<br>
        at org.apache.hadoop.util.ReflectionUtils.newInstance(ReflectionUtils.java:133)<br>
        at org.apache.hadoop.hive.metastore.RawStoreProxy.&lt;init&gt;(RawStoreProxy.java:58)<br>
        at org.apache.hadoop.hive.metastore.RawStoreProxy.getProxy(RawStoreProxy.java:67)<br>
        at org.apache.hadoop.hive.metastore.HiveMetaStore$HMSHandler.newRawStore(HiveMetaStore.java:497)<br>
        at org.apache.hadoop.hive.metastore.HiveMetaStore$HMSHandler.getMS(HiveMetaStore.java:475)<br>
        at org.apache.hadoop.hive.metastore.HiveMetaStore$HMSHandler.createDefaultDB(HiveMetaStore.java:523)<br>
        at org.apache.hadoop.hive.metastore.HiveMetaStore$HMSHandler.init(HiveMetaStore.java:397)<br>
        at org.apache.hadoop.hive.metastore.HiveMetaStore$HMSHandler.&lt;init&gt;(HiveMetaStore.java:356)<br>
        at org.apache.hadoop.hive.metastore.RetryingHMSHandler.&lt;init&gt;(RetryingHMSHandler.java:54)<br>
        at org.apache.hadoop.hive.metastore.RetryingHMSHandler.getProxy(RetryingHMSHandler.java:59)<br>
        at org.apache.hadoop.hive.metastore.HiveMetaStore.newHMSHandler(HiveMetaStore.java:4944)<br>
        at org.apache.hadoop.hive.metastore.HiveMetaStoreClient.&lt;init&gt;(HiveMetaStoreClient.java:171)<br></p>
<p><br></p>
<p>错误代码意思是链接client错误，下载<span style="color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;background-color:rgb(245,245,245);">mysql-connector-java-5.1.25-bin.jar包放置hive/lib目录下，重启hive，问题解决</span></p>
<p><span style="color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;background-color:rgb(245,245,245);"><span style="color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;background-color:rgb(245,245,245);">mysql-connector-java-5.1.25-bin.jar这个jar包是mysql的jdbc驱动</span><br></span></p>
            </div>
                </div>