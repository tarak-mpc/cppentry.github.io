---
layout:     post
title:      Hive-异常处理Hive Schema version 2.3.0 does not match metastore's schema version 1.2.0 Metastore is not
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/u014804456/article/details/77747720				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>在使用Hive shell的时候突然遇到：</p>
<p># hive</p>
<p># use default;</p>
<p>hive&gt; use default;<br>
FAILED: SemanticException org.apache.hadoop.hive.ql.metadata.HiveException: java.lang.RuntimeException: Unable to instantiate org.apache.hadoop.hive.ql.metadata.SessionHiveMetaStoreClient</p>
<p><br></p>
<p>去查看log日志：</p>
<p>hive.log 文件</p>
<p><br></p>
<p>发现如下异常：</p>
<p><br></p>
<p><span></span>at java.lang.reflect.Method.invoke(Method.java:498) ~[?:1.8.0_144]<br><span></span>at org.apache.hadoop.util.RunJar.run(RunJar.java:234) ~[hadoop-common-2.8.0.jar:?]<br><span></span>at org.apache.hadoop.util.RunJar.main(RunJar.java:148) ~[hadoop-common-2.8.0.jar:?]<br><span style="color:#ff0000;">Caused by: java.lang.reflect.InvocationTargetException</span><br><span></span>at sun.reflect.NativeConstructorAccessorImpl.newInstance0(Native Method) ~[?:1.8.0_144]<br><span></span>at sun.reflect.NativeConstructorAccessorImpl.newInstance(NativeConstructorAccessorImpl.java:62) ~[?:1.8.0_144]<br><span></span>at sun.reflect.DelegatingConstructorAccessorImpl.newInstance(DelegatingConstructorAccessorImpl.java:45) ~[?:1.8.0_144]<br><span></span>at java.lang.reflect.Constructor.newInstance(Constructor.java:423) ~[?:1.8.0_144]<br><span></span>at org.apache.hadoop.hive.metastore.MetaStoreUtils.newInstance(MetaStoreUtils.java:1699) ~[hive-exec-2.3.0.jar:2.3.0]<br><span></span>... 32 more<br><span style="color:#ff0000;">Caused by: org.apache.hadoop.hive.metastore.api.MetaException: Hive Schema version 2.3.0 does not match metastore's schema version 1.2.0 Metastore is not upgraded or corrupt</span><br><span></span>at org.apache.hadoop.hive.metastore.RetryingHMSHandler.&lt;init&gt;(RetryingHMSHandler.java:83) ~[hive-exec-2.3.0.jar:2.3.0]<br><span></span>at org.apache.hadoop.hive.metastore.RetryingHMSHandler.getProxy(RetryingHMSHandler.java:92) ~[hive-exec-2.3.0.jar:2.3.0]<br><span></span>at org.apache.hadoop.hive.metastore.HiveMetaStore.newRetryingHMSHandler(HiveMetaStore.java:6889) ~[hive-exec-2.3.0.jar:2.3.0]<br><span></span>at org.apache.hadoop.hive.metastore.HiveMetaStoreClient.&lt;init&gt;(HiveMetaStoreClient.java:159) ~[hive-exec-2.3.0.jar:2.3.0]<br><span></span>at org.apache.hadoop.hive.ql.metadata.SessionHiveMetaStoreClient.&lt;init&gt;(SessionHiveMetaStoreClient.java:70) ~[hive-exec-2.3.0.jar:2.3.0]<br><span></span>at sun.reflect.NativeConstructorAccessorImpl.newInstance0(Native Method) ~[?:1.8.0_144]<br><span></span>at sun.reflect.NativeConstructorAccessorImpl.newInstance(NativeConstructorAccessorImpl.java:62) ~[?:1.8.0_144]<br><span></span>at sun.reflect.DelegatingConstructorAccessorImpl.newInstance(DelegatingConstructorAccessorImpl.java:45) ~[?:1.8.0_144]<br><span></span>at java.lang.reflect.Constructor.newInstance(Constructor.java:423) ~[?:1.8.0_144]<br><span></span>at org.apache.hadoop.hive.metastore.MetaStoreUtils.newInstance(MetaStoreUtils.java:1699) ~[hive-exec-2.3.0.jar:2.3.0]<br><span></span>... 32 more<br><br>
最后在github上找到解决方法：</p>
<p>更新一下：</p>
<p>https://github.com/apache/hive/blob/master/metastore/scripts/upgrade/mssql/upgrade-2.2.0-to-2.3.0.mssql.sql<br></p>
<p><br></p>
<p>mysql -u root -p </p>
<p># input your password</p>
<p><br></p>
<p># SELECT 'Upgrading MetaStore schema from 2.2.0 to 2.3.0' AS MESSAGE;<br><br>
:r 025-HIVE-16399.mssql.sql<br><br><br>
#UPDATE VERSION SET SCHEMA_VERSION='2.3.0', VERSION_COMMENT='Hive release version 2.3.0' where VER_ID=1;<br>
#SELECT 'Finished upgrading MetaStore schema from 2.2.0 to 2.3.0' AS MESSAGE;<br></p>
<p><br></p>
<p><br></p>
<p><br></p>
<p><br></p>
            </div>
                </div>