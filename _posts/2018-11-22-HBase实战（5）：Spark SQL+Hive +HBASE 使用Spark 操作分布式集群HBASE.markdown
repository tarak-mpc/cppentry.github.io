---
layout:     post
title:      HBase实战（5）：Spark SQL+Hive +HBASE 使用Spark 操作分布式集群HBASE
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：王家林大咖2018年新书《SPARK大数据商业实战三部曲》清华大学出版，清华大学出版社官方旗舰店（天猫）https://qhdx.tmall.com/?spm=a220o.1000855.1997427721.d4918089.4b2a2e5dT6bUsM					https://blog.csdn.net/duan_zhihua/article/details/80689075				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>HBase实战（5）：Spark SQL+Hive +HBASE 使用Spark 操作分布式集群HBASE</p><p>本文的操作是使用spark 自带的spark sql工具 通过Hive去操作Hbase的数据。</p><p>在spark 集群中提交spark sql运行语句。分别使用了本地模式、集群模式提交，遇到的一些报错是JAR</p><p>包没加载全，提交过程中加载HBASE的相关Jar包运行就可以。</p><pre><code class="language-html">root@master:~# spark-sql  --master spark://192.168.189.1:7077 --driver-class-path /usr/local/apache-hive-1.2.1/lib/mysql-connector-java-5.1.13-bin.jar --jars /usr/local/apache-hive-1.2.1/lib/mysql-connector-java-5.1.13-bin.jar,/usr/local/apache-hive-1.2.1/lib/hive-hbase-handler-1.2.1.jar,/usr/local/hbase-1.2.0/lib/hbase-client-1.2.0.jar,/usr/local/hbase-1.2.0/lib/hbase-common-1.2.0.jar,/usr/local/hbase-1.2.0/lib/hbase-protocol-1.2.0.jar,/usr/local/hbase-1.2.0/lib/hbase-server-1.2.0.jar,/usr/local/hbase-1.2.0/lib/htrace-core-3.1.0-incubating.jar,/usr/local/hbase-1.2.0/lib/metrics-core-2.2.0.jar,/usr/local/hbase-1.2.0/lib/hbase-hadoop2-compat-1.2.0.jar,/usr/local/hbase-1.2.0/lib/guava-12.0.1.jar,/usr/local/hbase-1.2.0/lib/protobuf-java-2.5.0.jar  --executor-memory 512m  --total-executor-cores 4  </code></pre><p>操作步骤：</p><p>1，基于spark 2.3.0 本地模式直接进入spark sql 客户端，提示出错，找不到“com.mysql.jdbc.Driver”</p><pre><code class="language-html">root@master:/usr/local/spark-2.3.0-bin-hadoop2.6/bin# spark-sql
SLF4J: Class path contains multiple SLF4J bindings.
SLF4J: Found binding in [jar:file:/usr/local/alluxio-1.7.0-hadoop-2.6/client/alluxio-1.7.0-client.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: Found binding in [jar:file:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/slf4j-log4j12-1.7.16.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: See http://www.slf4j.org/codes.html#multiple_bindings for an explanation.
SLF4J: Actual binding is of type [org.slf4j.impl.Log4jLoggerFactory]
2018-06-14 10:22:53 WARN  NativeCodeLoader:62 - Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
2018-06-14 10:22:57 WARN  HiveConf:2753 - HiveConf of name hive.server2.http.endpoint does not exist
2018-06-14 10:22:59 INFO  HiveMetaStore:589 - 0: Opening raw store with implemenation class:org.apache.hadoop.hive.metastore.ObjectStore
2018-06-14 10:22:59 INFO  ObjectStore:289 - ObjectStore, initialize called
2018-06-14 10:23:00 INFO  Persistence:77 - Property hive.metastore.integral.jdo.pushdown unknown - will be ignored
2018-06-14 10:23:00 INFO  Persistence:77 - Property datanucleus.cache.level2 unknown - will be ignored
2018-06-14 10:23:00 WARN  HiveMetaStore:622 - Retrying creating default database after error: Error creating transactional connection factory
javax.jdo.JDOFatalInternalException: Error creating transactional connection factory
        at org.datanucleus.api.jdo.NucleusJDOHelper.getJDOExceptionForNucleusException(NucleusJDOHelper.java:587)
        at org.datanucleus.api.jdo.JDOPersistenceManagerFactory.freezeConfiguration(JDOPersistenceManagerFactory.java:788)</code></pre><pre><code class="language-html">        ... 52 more
Caused by: org.datanucleus.exceptions.NucleusException: Attempt to invoke the "BONECP" plugin to create a ConnectionPool gave an error : The specified datastore driver ("com.mysql.jdbc.Driver") was not found in the CLASSPATH. Please check your CLASSPATH specification, and the name of the driver.
        at org.datanucleus.store.rdbms.ConnectionFactoryImpl.generateDataSources(ConnectionFactoryImpl.java:259)
        at org.datanucleus.store.rdbms.ConnectionFactoryImpl.initialiseDataSources(ConnectionFactoryImpl.java:131)
        at org.datanucleus.store.rdbms.ConnectionFactoryImpl.&lt;init&gt;(ConnectionFactoryImpl.java:85)
        ... 70 more
Caused by: org.datanucleus.store.rdbms.connectionpool.DatastoreDriverNotFoundException: The specified datastore driver ("com.mysql.jdbc.Driver") was not found in the CLASSPATH. Please check your CLASSPATH specification, and the name of the driver.
        at org.datanucleus.store.rdbms.connectionpool.AbstractConnectionPoolFactory.loadDriver(AbstractConnectionPoolFactory.java:58)
        at org.datanucleus.store.rdbms.connectionpool.BoneCPConnectionPoolFactory.createConnectionPool(BoneCPConnectionPoolFactory.java:54)
        at org.datanucleus.store.rdbms.ConnectionFactoryImpl.generateDataSources(ConnectionFactoryImpl.java:238)
        ... 72 more
2018-06-14 10:23:01 INFO  ShutdownHookManager:54 - Shutdown hook called
2018-06-14 10:23:01 INFO  ShutdownHookManager:54 - Deleting directory /tmp/spark-2fa09172-d8d5-4f4d-ba5f-84faee7c7d52
root@master:/usr/local/spark-2.3.0-bin-hadoop2.6/bin# </code></pre><p>2，启动Spark集群。</p><pre><code class="language-html">root@master:~# /usr/local/spark-2.3.0-bin-hadoop2.6/sbin/start-all.sh
starting org.apache.spark.deploy.master.Master, logging to /usr/local/spark-2.3.0-bin-hadoop2.6/logs/spark-root-org.apache.spark.deploy.master.Master-1-master.out
worker2: starting org.apache.spark.deploy.worker.Worker, logging to /usr/local/spark-2.3.0-bin-hadoop2.6/logs/spark-root-org.apache.spark.deploy.worker.Worker-1-worker2.out
worker1: starting org.apache.spark.deploy.worker.Worker, logging to /usr/local/spark-2.3.0-bin-hadoop2.6/logs/spark-root-org.apache.spark.deploy.worker.Worker-1-worker1.out
worker1: failed to launch: nice -n 0 /usr/local/spark-2.3.0-bin-hadoop2.6/bin/spark-class org.apache.spark.deploy.worker.Worker --webui-port 8081 spark://master:7077
worker1: full log in /usr/local/spark-2.3.0-bin-hadoop2.6/logs/spark-root-org.apache.spark.deploy.worker.Worker-1-worker1.out
worker3: starting org.apache.spark.deploy.worker.Worker, logging to /usr/local/spark-2.3.0-bin-hadoop2.6/logs/spark-root-org.apache.spark.deploy.worker.Worker-1-worker3.out
root@master:~# jps
3075 ResourceManager
3715 HRegionServer
3395 QuorumPeerMain
2932 SecondaryNameNode
4613 Jps
3543 HMaster
2715 NameNode
3823 RunJar
4543 Master
root@master:~# </code></pre><p>3，在spark集群中使用spark-sql。</p><pre><code class="language-html">root@master:~#  spark-sql  --master spark://192.168.189.1:7077 --driver-class-path /usr/local/apache-hive-1.2.1/lib/mysql-connector-java-5.1.13-bin.jar  --executor-memory 512m  --total-executor-cores 4

</code></pre><pre><code class="language-html">2018-06-14 10:31:36 WARN  NativeCodeLoader:62 - Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
2018-06-14 10:31:37 WARN  HiveConf:2753 - HiveConf of name hive.server2.http.endpoint does not exist
2018-06-14 10:31:38 INFO  HiveMetaStore:589 - 0: Opening raw store with implemenation class:org.apache.hadoop.hive.metastore.ObjectStore
2018-06-14 10:31:38 INFO  ObjectStore:289 - ObjectStore, initialize called
2018-06-14 10:31:39 INFO  Persistence:77 - Property hive.metastore.integral.jdo.pushdown unknown - will be ignored
2018-06-14 10:31:39 INFO  Persistence:77 - Property datanucleus.cache.level2 unknown - will be ignored
2018-06-14 10:31:43 WARN  HiveConf:2753 - HiveConf of name hive.server2.http.endpoint does not exist
2018-06-14 10:31:43 INFO  ObjectStore:370 - Setting MetaStore object pin classes with hive.metastore.cache.pinobjtypes="Table,StorageDescriptor,SerDeInfo,Partition,Database,Type,FieldSchema,Order"
2018-06-14 10:31:45 INFO  Datastore:77 - The class "org.apache.hadoop.hive.metastore.model.MFieldSchema" is tagged as "embedded-only" so does not have its own datastore table.
2018-06-14 10:31:45 INFO  Datastore:77 - The class "org.apache.hadoop.hive.metastore.model.MOrder" is tagged as "embedded-only" so does not have its own datastore table.
2018-06-14 10:31:49 INFO  Datastore:77 - The class "org.apache.hadoop.hive.metastore.model.MFieldSchema" is tagged as "embedded-only" so does not have its own datastore table.
2018-06-14 10:31:49 INFO  Datastore:77 - The class "org.apache.hadoop.hive.metastore.model.MOrder" is tagged as "embedded-only" so does not have its own datastore table.
2018-06-14 10:31:50 INFO  Query:77 - Reading in results for query "org.datanucleus.store.rdbms.query.SQLQuery@0" since the connection used is closing
2018-06-14 10:31:50 INFO  MetaStoreDirectSql:139 - Using direct SQL, underlying DB is MYSQL
2018-06-14 10:31:51 INFO  ObjectStore:272 - Initialized ObjectStore
2018-06-14 10:31:51 INFO  HiveMetaStore:663 - Added admin role in metastore
2018-06-14 10:31:51 INFO  HiveMetaStore:672 - Added public role in metastore
2018-06-14 10:31:51 INFO  HiveMetaStore:712 - No user is added in admin role, since config is empty
2018-06-14 10:31:52 INFO  HiveMetaStore:746 - 0: get_all_databases
2018-06-14 10:31:52 INFO  audit:371 - ugi=root  ip=unknown-ip-addr      cmd=get_all_databases
2018-06-14 10:31:52 INFO  HiveMetaStore:746 - 0: get_functions: db=default pat=*
2018-06-14 10:31:52 INFO  audit:371 - ugi=root  ip=unknown-ip-addr      cmd=get_functions: db=default pat=*
2018-06-14 10:31:52 INFO  Datastore:77 - The class "org.apache.hadoop.hive.metastore.model.MResourceUri" is tagged as "embedded-only" so does not have its own datastore table.
2018-06-14 10:31:52 INFO  HiveMetaStore:746 - 0: get_functions: db=hive pat=*
2018-06-14 10:31:52 INFO  audit:371 - ugi=root  ip=unknown-ip-addr      cmd=get_functions: db=hive pat=*
2018-06-14 10:31:52 INFO  HiveMetaStore:746 - 0: get_functions: db=hivestudy pat=*
2018-06-14 10:31:52 INFO  audit:371 - ugi=root  ip=unknown-ip-addr      cmd=get_functions: db=hivestudy pat=*
2018-06-14 10:31:52 INFO  HiveMetaStore:746 - 0: get_functions: db=hrs pat=*
2018-06-14 10:31:52 INFO  audit:371 - ugi=root  ip=unknown-ip-addr      cmd=get_functions: db=hrs pat=*
2018-06-14 10:31:55 INFO  SessionState:641 - Created local directory: /tmp/999d5941-24ad-4579-a61c-ba719868dc9b_resources
2018-06-14 10:31:55 INFO  SessionState:641 - Created HDFS directory: /tmp/hive/root/999d5941-24ad-4579-a61c-ba719868dc9b
2018-06-14 10:31:55 INFO  SessionState:641 - Created local directory: /tmp/root/999d5941-24ad-4579-a61c-ba719868dc9b
2018-06-14 10:31:55 INFO  SessionState:641 - Created HDFS directory: /tmp/hive/root/999d5941-24ad-4579-a61c-ba719868dc9b/_tmp_space.db
2018-06-14 10:31:56 INFO  SparkContext:54 - Running Spark version 2.3.0
2018-06-14 10:31:56 INFO  SparkContext:54 - Submitted application: SparkSQL::master
2018-06-14 10:31:57 INFO  SecurityManager:54 - Changing view acls to: root
2018-06-14 10:31:57 INFO  SecurityManager:54 - Changing modify acls to: root
2018-06-14 10:31:57 INFO  SecurityManager:54 - Changing view acls groups to: 
2018-06-14 10:31:57 INFO  SecurityManager:54 - Changing modify acls groups to: 
2018-06-14 10:31:57 INFO  SecurityManager:54 - SecurityManager: authentication disabled; ui acls disabled; users  with view permissions: Set(root); groups with view permissions: Set(); users  with modify permissions: Set(root); groups with modify permissions: Set()
2018-06-14 10:31:58 INFO  Utils:54 - Successfully started service 'sparkDriver' on port 49760.
2018-06-14 10:31:58 INFO  SparkEnv:54 - Registering MapOutputTracker
2018-06-14 10:31:59 INFO  SparkEnv:54 - Registering BlockManagerMaster
2018-06-14 10:31:59 INFO  BlockManagerMasterEndpoint:54 - Using org.apache.spark.storage.DefaultTopologyMapper for getting topology information
2018-06-14 10:31:59 INFO  BlockManagerMasterEndpoint:54 - BlockManagerMasterEndpoint up
2018-06-14 10:31:59 INFO  DiskBlockManager:54 - Created local directory at /tmp/blockmgr-e9250684-2aee-4f04-8466-28c951fa52cf
2018-06-14 10:31:59 INFO  MemoryStore:54 - MemoryStore started with capacity 413.9 MB
2018-06-14 10:31:59 INFO  SparkEnv:54 - Registering OutputCommitCoordinator
2018-06-14 10:32:00 INFO  log:192 - Logging initialized @26885ms
2018-06-14 10:32:00 INFO  Server:346 - jetty-9.3.z-SNAPSHOT
2018-06-14 10:32:00 INFO  Server:414 - Started @27370ms
2018-06-14 10:32:01 INFO  AbstractConnector:278 - Started ServerConnector@4aa31ffc{HTTP/1.1,[http/1.1]}{0.0.0.0:4040}
2018-06-14 10:32:01 INFO  Utils:54 - Successfully started service 'SparkUI' on port 4040.
2018-06-14 10:32:01 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@4a9860{/jobs,null,AVAILABLE,@Spark}
2018-06-14 10:32:01 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@7f85217c{/jobs/json,null,AVAILABLE,@Spark}
2018-06-14 10:32:01 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@1fd7a37{/jobs/job,null,AVAILABLE,@Spark}
2018-06-14 10:32:01 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@58a84a12{/jobs/job/json,null,AVAILABLE,@Spark}
2018-06-14 10:32:01 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@e700eba{/stages,null,AVAILABLE,@Spark}
2018-06-14 10:32:01 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@6436e181{/stages/json,null,AVAILABLE,@Spark}
2018-06-14 10:32:01 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@7186b202{/stages/stage,null,AVAILABLE,@Spark}
2018-06-14 10:32:01 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@36068727{/stages/stage/json,null,AVAILABLE,@Spark}
2018-06-14 10:32:01 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@72543547{/stages/pool,null,AVAILABLE,@Spark}
2018-06-14 10:32:01 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@3d88e6b9{/stages/pool/json,null,AVAILABLE,@Spark}
2018-06-14 10:32:01 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@22bf9122{/storage,null,AVAILABLE,@Spark}
2018-06-14 10:32:01 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@208205ed{/storage/json,null,AVAILABLE,@Spark}
2018-06-14 10:32:01 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@73afe2b7{/storage/rdd,null,AVAILABLE,@Spark}
2018-06-14 10:32:01 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@9687f55{/storage/rdd/json,null,AVAILABLE,@Spark}
2018-06-14 10:32:01 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@5700c9db{/environment,null,AVAILABLE,@Spark}
2018-06-14 10:32:01 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@671d03bb{/environment/json,null,AVAILABLE,@Spark}
2018-06-14 10:32:01 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@6babffb5{/executors,null,AVAILABLE,@Spark}
2018-06-14 10:32:01 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@2173a742{/executors/json,null,AVAILABLE,@Spark}
2018-06-14 10:32:01 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@706ceca6{/executors/threadDump,null,AVAILABLE,@Spark}
2018-06-14 10:32:01 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@7f6329cb{/executors/threadDump/json,null,AVAILABLE,@Spark}
2018-06-14 10:32:01 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@4b8137c5{/static,null,AVAILABLE,@Spark}
2018-06-14 10:32:01 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@31973858{/,null,AVAILABLE,@Spark}
2018-06-14 10:32:01 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@65514add{/api,null,AVAILABLE,@Spark}
2018-06-14 10:32:01 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@10850d17{/jobs/job/kill,null,AVAILABLE,@Spark}
2018-06-14 10:32:01 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@51e754e1{/stages/stage/kill,null,AVAILABLE,@Spark}
2018-06-14 10:32:01 INFO  SparkUI:54 - Bound SparkUI to 0.0.0.0, and started at http://master:4040
2018-06-14 10:32:02 INFO  StandaloneAppClient$ClientEndpoint:54 - Connecting to master spark://192.168.189.1:7077...
2018-06-14 10:32:02 INFO  TransportClientFactory:267 - Successfully created connection to /192.168.189.1:7077 after 164 ms (0 ms spent in bootstraps)
2018-06-14 10:32:03 INFO  StandaloneSchedulerBackend:54 - Connected to Spark cluster with app ID app-20180614103203-0000
2018-06-14 10:32:03 INFO  Utils:54 - Successfully started service 'org.apache.spark.network.netty.NettyBlockTransferService' on port 35493.
2018-06-14 10:32:03 INFO  NettyBlockTransferService:54 - Server created on master:35493
2018-06-14 10:32:03 INFO  BlockManager:54 - Using org.apache.spark.storage.RandomBlockReplicationPolicy for block replication policy
2018-06-14 10:32:03 INFO  BlockManagerMaster:54 - Registering BlockManager BlockManagerId(driver, master, 35493, None)
2018-06-14 10:32:03 INFO  BlockManagerMasterEndpoint:54 - Registering block manager master:35493 with 413.9 MB RAM, BlockManagerId(driver, master, 35493, None)
2018-06-14 10:32:03 INFO  BlockManagerMaster:54 - Registered BlockManager BlockManagerId(driver, master, 35493, None)
2018-06-14 10:32:03 INFO  BlockManager:54 - Initialized BlockManager: BlockManagerId(driver, master, 35493, None)
2018-06-14 10:32:04 INFO  StandaloneAppClient$ClientEndpoint:54 - Executor added: app-20180614103203-0000/0 on worker-20180614103000-worker1-36930 (worker1:36930) with 1 core(s)
2018-06-14 10:32:04 INFO  StandaloneSchedulerBackend:54 - Granted executor ID app-20180614103203-0000/0 on hostPort worker1:36930 with 1 core(s), 512.0 MB RAM
2018-06-14 10:32:04 INFO  StandaloneAppClient$ClientEndpoint:54 - Executor added: app-20180614103203-0000/1 on worker-20180614103020-worker3-48018 (worker3:48018) with 1 core(s)
2018-06-14 10:32:04 INFO  StandaloneSchedulerBackend:54 - Granted executor ID app-20180614103203-0000/1 on hostPort worker3:48018 with 1 core(s), 512.0 MB RAM
2018-06-14 10:32:04 INFO  StandaloneAppClient$ClientEndpoint:54 - Executor added: app-20180614103203-0000/2 on worker-20180614103016-worker2-53644 (worker2:53644) with 1 core(s)
2018-06-14 10:32:04 INFO  StandaloneSchedulerBackend:54 - Granted executor ID app-20180614103203-0000/2 on hostPort worker2:53644 with 1 core(s), 512.0 MB RAM
2018-06-14 10:32:05 INFO  StandaloneAppClient$ClientEndpoint:54 - Executor updated: app-20180614103203-0000/2 is now RUNNING
2018-06-14 10:32:05 INFO  StandaloneAppClient$ClientEndpoint:54 - Executor updated: app-20180614103203-0000/0 is now RUNNING
2018-06-14 10:32:05 INFO  StandaloneAppClient$ClientEndpoint:54 - Executor updated: app-20180614103203-0000/1 is now RUNNING
2018-06-14 10:32:07 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@644d1b61{/metrics/json,null,AVAILABLE,@Spark}
2018-06-14 10:32:07 INFO  StandaloneSchedulerBackend:54 - SchedulerBackend is ready for scheduling beginning after reached minRegisteredResourcesRatio: 0.0
2018-06-14 10:32:10 INFO  SharedState:54 - loading hive config file: file:/usr/local/spark-2.3.0-bin-hadoop2.6/conf/hive-site.xml
2018-06-14 10:32:10 INFO  SharedState:54 - spark.sql.warehouse.dir is not set, but hive.metastore.warehouse.dir is set. Setting spark.sql.warehouse.dir to the value of hive.metastore.warehouse.dir ('/user/hive/warehouse').
2018-06-14 10:32:10 INFO  SharedState:54 - Warehouse path is '/user/hive/warehouse'.
2018-06-14 10:32:10 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@46a795de{/SQL,null,AVAILABLE,@Spark}
2018-06-14 10:32:10 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@256a0d95{/SQL/json,null,AVAILABLE,@Spark}
2018-06-14 10:32:10 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@44f0ff2b{/SQL/execution,null,AVAILABLE,@Spark}
2018-06-14 10:32:10 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@22ead351{/SQL/execution/json,null,AVAILABLE,@Spark}
2018-06-14 10:32:10 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@601eb4af{/static/sql,null,AVAILABLE,@Spark}
2018-06-14 10:32:13 INFO  HiveUtils:54 - Initializing HiveMetastoreConnection version 1.2.1 using Spark classes.
2018-06-14 10:32:15 INFO  HiveClientImpl:54 - Warehouse location for Hive client (version 1.2.2) is /user/hive/warehouse
2018-06-14 10:32:16 INFO  HiveMetaStore:746 - 0: get_database: default
2018-06-14 10:32:16 INFO  audit:371 - ugi=root  ip=unknown-ip-addr      cmd=get_database: default
2018-06-14 10:32:37 INFO  StateStoreCoordinatorRef:54 - Registered StateStoreCoordinator endpoint
spark-sql&gt; 2018-06-14 10:32:53 INFO  CoarseGrainedSchedulerBackend$DriverEndpoint:54 - Registered executor NettyRpcEndpointRef(spark-client://Executor) (192.168.189.4:56970) with ID 1
2018-06-14 10:32:55 INFO  CoarseGrainedSchedulerBackend$DriverEndpoint:54 - Registered executor NettyRpcEndpointRef(spark-client://Executor) (192.168.189.3:50799) with ID 2
2018-06-14 10:32:56 INFO  BlockManagerMasterEndpoint:54 - Registering block manager worker2:53191 with 117.0 MB RAM, BlockManagerId(2, worker2, 53191, None)
2018-06-14 10:33:06 INFO  BlockManagerMasterEndpoint:54 - Registering block manager worker3:47807 with 117.0 MB RAM, BlockManagerId(1, worker3, 47807, None)
</code></pre><pre><code class="language-html">spark-sql&gt; show databases;
2018-06-14 10:39:20 INFO  HiveMetaStore:746 - 0: get_database: global_temp
2018-06-14 10:39:20 INFO  audit:371 - ugi=root  ip=unknown-ip-addr      cmd=get_database: global_temp
2018-06-14 10:39:21 WARN  ObjectStore:568 - Failed to get database global_temp, returning NoSuchObjectException
2018-06-14 10:39:30 INFO  HiveMetaStore:746 - 0: get_databases: *
2018-06-14 10:39:30 INFO  audit:371 - ugi=root  ip=unknown-ip-addr      cmd=get_databases: *
2018-06-14 10:39:33 INFO  CodeGenerator:54 - Code generated in 525.870215 ms
default
hive
hivestudy
hrs
Time taken: 12.771 seconds, Fetched 4 row(s)
2018-06-14 10:39:33 INFO  SparkSQLCLIDriver:951 - Time taken: 12.771 seconds, Fetched 4 row(s)</code></pre><pre><code class="language-html">spark-sql&gt; use default;
2018-06-14 10:39:41 INFO  HiveMetaStore:746 - 0: get_database: default
2018-06-14 10:39:41 INFO  audit:371 - ugi=root  ip=unknown-ip-addr      cmd=get_database: default
Time taken: 0.102 seconds
2018-06-14 10:39:41 INFO  SparkSQLCLIDriver:951 - Time taken: 0.102 seconds
spark-sql&gt; show tables;
2018-06-14 10:39:46 INFO  HiveMetaStore:746 - 0: get_database: default
2018-06-14 10:39:46 INFO  audit:371 - ugi=root  ip=unknown-ip-addr      cmd=get_database: default
2018-06-14 10:39:46 INFO  HiveMetaStore:746 - 0: get_database: default
2018-06-14 10:39:46 INFO  audit:371 - ugi=root  ip=unknown-ip-addr      cmd=get_database: default
2018-06-14 10:39:46 INFO  HiveMetaStore:746 - 0: get_tables: db=default pat=*
2018-06-14 10:39:46 INFO  audit:371 - ugi=root  ip=unknown-ip-addr      cmd=get_tables: db=default pat=*
2018-06-14 10:39:47 INFO  ContextCleaner:54 - Cleaned accumulator 0
2018-06-14 10:39:47 INFO  ContextCleaner:54 - Cleaned accumulator 1
2018-06-14 10:39:47 INFO  CodeGenerator:54 - Code generated in 34.691478 ms
default booktable       false
default hbase_hive.....operation     false
default partition_test  false
default peopledepljion  false
default pokes   false
default src     false
Time taken: 0.442 seconds, Fetched 6 row(s)
2018-06-14 10:39:47 INFO  SparkSQLCLIDriver:951 - Time taken: 0.442 seconds, Fetched 6 row(s)</code></pre>spark sql通过hive查询hbase，提示出错。 找不到“org.apache.hadoop.hive.hbase.HBaseStorageHandler”<br><pre><code class="language-html">spark-sql&gt; select * from hbase_hive_wtb_ow_operation;
2018-06-14 10:42:38 INFO  HiveMetaStore:746 - 0: get_table : db=default tbl=hbase_hive_wtb_ow_operation
2018-06-14 10:42:38 INFO  audit:371 - ugi=root  ip=unknown-ip-addr      cmd=get_table : db=default tbl=hbase_hive_wtb_ow_operation
2018-06-14 10:42:39 ERROR log:397 - error in initSerDe: java.lang.ClassNotFoundException Class org.apache.hadoop.hive.hbase.HBaseSerDe not found
java.lang.ClassNotFoundException: Class org.apache.hadoop.hive.hbase.HBaseSerDe not found
        at org.apache.hadoop.conf.Configuration.getClassByName(Configuration.java:2060)
        at org.apache.hadoop.hive.metastore.MetaStoreUtils.getDeserializer(MetaStoreUtils.java:385)
        at org.apache.hadoop.hive.ql.metadata.Table.getDeserializerFromMetaStore(Table.java:276)
        at org.apache.hadoop.hive.ql.metadata.Table.getDeserializer(Table.java:258)
        at org.apache.hadoop.hive.ql.metadata.Table.getCols(Table.java:605)
        at org.apache.spark.sql.hive.client.HiveClientImpl$$anonfun$getTableOption$1$$anonfun$apply$7.apply(HiveClientImpl.scala:358)
        at org.apache.spark.sql.hive.client.HiveClientImpl$$anonfun$getTableOption$1$$anonfun$apply$7.apply(HiveClientImpl.scala:355)
        at scala.Option.map(Option.scala:146)
        at org.apache.spark.sql.hive.client.HiveClientImpl$$anonfun$getTableOption$1.apply(HiveClientImpl.scala:355)
        at org.apache.spark.sql.hive.client.HiveClientImpl$$anonfun$getTableOption$1.apply(HiveClientImpl.scala:353)
        at org.apache.spark.sql.hive.client.HiveClientImpl$$anonfun$withHiveState$1.apply(HiveClientImpl.scala:272)
        at org.apache.spark.sql.hive.client.HiveClientImpl.liftedTree1$1(HiveClientImpl.scala:210)
        at org.apache.spark.sql.hive.client.HiveClientImpl.retryLocked(HiveClientImpl.scala:209)
        at org.apache.spark.sql.hive.client.HiveClientImpl.withHiveState(HiveClientImpl.scala:255)
        at org.apache.spark.sql.hive.client.HiveClientImpl.getTableOption(HiveClientImpl.scala:353)
        at org.apache.spark.sql.hive.client.HiveClient$class.getTable(HiveClient.scala:81)
        at org.apache.spark.sql.hive.client.HiveClientImpl.getTable(HiveClientImpl.scala:83)
        at org.apache.spark.sql.hive.HiveExternalCatalog$$anonfun$getRawTable$1.apply(HiveExternalCatalog.scala:118)
        at org.apache.spark.sql.hive.HiveExternalCatalog$$anonfun$getRawTable$1.apply(HiveExternalCatalog.scala:118)
        at org.apache.spark.sql.hive.HiveExternalCatalog.withClient(HiveExternalCatalog.scala:97)
        at org.apache.spark.sql.hive.HiveExternalCatalog.getRawTable(HiveExternalCatalog.scala:117)
        at org.apache.spark.sql.hive.HiveExternalCatalog$$anonfun$getTable$1.apply(HiveExternalCatalog.scala:684)
        at org.apache.spark.sql.hive.HiveExternalCatalog$$anonfun$getTable$1.apply(HiveExternalCatalog.scala:684)
        at org.apache.spark.sql.hive.HiveExternalCatalog.withClient(HiveExternalCatalog.scala:97)
        at org.apache.spark.sql.hive.HiveExternalCatalog.getTable(HiveExternalCatalog.scala:683)
        at org.apache.spark.sql.catalyst.catalog.SessionCatalog.lookupRelation(SessionCatalog.scala:669)
        at org.apache.spark.sql.catalyst.analysis.Analyzer$ResolveRelations$.org$apache$spark$sql$catalyst$analysis$Analyzer$ResolveRelations$$lookupTableFromCatalog(Analyzer.scala:660)
        at org.apache.spark.sql.catalyst.analysis.Analyzer$ResolveRelations$.resolveRelation(Analyzer.scala:615)
        at org.apache.spark.sql.catalyst.analysis.Analyzer$ResolveRelations$$anonfun$apply$8.applyOrElse(Analyzer.scala:645)
        at org.apache.spark.sql.catalyst.analysis.Analyzer$ResolveRelations$$anonfun$apply$8.applyOrElse(Analyzer.scala:638)
        at org.apache.spark.sql.catalyst.trees.TreeNode$$anonfun$transformUp$1.apply(TreeNode.scala:289)
        at org.apache.spark.sql.catalyst.trees.TreeNode$$anonfun$transformUp$1.apply(TreeNode.scala:289)
        at org.apache.spark.sql.catalyst.trees.CurrentOrigin$.withOrigin(TreeNode.scala:70)
        at org.apache.spark.sql.catalyst.trees.TreeNode.transformUp(TreeNode.scala:288)
        at org.apache.spark.sql.catalyst.trees.TreeNode$$anonfun$3.apply(TreeNode.scala:286)
        at org.apache.spark.sql.catalyst.trees.TreeNode$$anonfun$3.apply(TreeNode.scala:286)
        at org.apache.spark.sql.catalyst.trees.TreeNode$$anonfun$4.apply(TreeNode.scala:306)
        at org.apache.spark.sql.catalyst.trees.TreeNode.mapProductIterator(TreeNode.scala:187)
        at org.apache.spark.sql.catalyst.trees.TreeNode.mapChildren(TreeNode.scala:304)
        at org.apache.spark.sql.catalyst.trees.TreeNode.transformUp(TreeNode.scala:286)
        at org.apache.spark.sql.catalyst.analysis.Analyzer$ResolveRelations$.apply(Analyzer.scala:638)
        at org.apache.spark.sql.catalyst.analysis.Analyzer$ResolveRelations$.apply(Analyzer.scala:584)
        at org.apache.spark.sql.catalyst.rules.RuleExecutor$$anonfun$execute$1$$anonfun$apply$1.apply(RuleExecutor.scala:87)
        at org.apache.spark.sql.catalyst.rules.RuleExecutor$$anonfun$execute$1$$anonfun$apply$1.apply(RuleExecutor.scala:84)
        at scala.collection.LinearSeqOptimized$class.foldLeft(LinearSeqOptimized.scala:124)
        at scala.collection.immutable.List.foldLeft(List.scala:84)
        at org.apache.spark.sql.catalyst.rules.RuleExecutor$$anonfun$execute$1.apply(RuleExecutor.scala:84)
        at org.apache.spark.sql.catalyst.rules.RuleExecutor$$anonfun$execute$1.apply(RuleExecutor.scala:76)
        at scala.collection.immutable.List.foreach(List.scala:381)
        at org.apache.spark.sql.catalyst.rules.RuleExecutor.execute(RuleExecutor.scala:76)
        at org.apache.spark.sql.catalyst.analysis.Analyzer.org$apache$spark$sql$catalyst$analysis$Analyzer$$executeSameContext(Analyzer.scala:123)
        at org.apache.spark.sql.catalyst.analysis.Analyzer.execute(Analyzer.scala:117)
        at org.apache.spark.sql.catalyst.analysis.Analyzer.executeAndCheck(Analyzer.scala:102)
        at org.apache.spark.sql.execution.QueryExecution.analyzed$lzycompute(QueryExecution.scala:57)
        at org.apache.spark.sql.execution.QueryExecution.analyzed(QueryExecution.scala:55)
        at org.apache.spark.sql.execution.QueryExecution.assertAnalyzed(QueryExecution.scala:47)
        at org.apache.spark.sql.Dataset$.ofRows(Dataset.scala:74)
        at org.apache.spark.sql.SparkSession.sql(SparkSession.scala:638)
        at org.apache.spark.sql.SQLContext.sql(SQLContext.scala:694)
        at org.apache.spark.sql.hive.thriftserver.SparkSQLDriver.run(SparkSQLDriver.scala:62)
        at org.apache.spark.sql.hive.thriftserver.SparkSQLCLIDriver.processCmd(SparkSQLCLIDriver.scala:355)
        at org.apache.hadoop.hive.cli.CliDriver.processLine(CliDriver.java:376)
        at org.apache.spark.sql.hive.thriftserver.SparkSQLCLIDriver$.main(SparkSQLCLIDriver.scala:263)
        at org.apache.spark.sql.hive.thriftserver.SparkSQLCLIDriver.main(SparkSQLCLIDriver.scala)
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
        at java.lang.reflect.Method.invoke(Method.java:497)
        at org.apache.spark.deploy.JavaMainApplication.start(SparkApplication.scala:52)
        at org.apache.spark.deploy.SparkSubmit$.org$apache$spark$deploy$SparkSubmit$$runMain(SparkSubmit.scala:879)
        at org.apache.spark.deploy.SparkSubmit$.doRunMain$1(SparkSubmit.scala:197)
        at org.apache.spark.deploy.SparkSubmit$.submit(SparkSubmit.scala:227)
        at org.apache.spark.deploy.SparkSubmit$.main(SparkSubmit.scala:136)
        at org.apache.spark.deploy.SparkSubmit.main(SparkSubmit.scala)
2018-06-14 10:42:39 ERROR Table:608 - Unable to get field from serde: org.apache.hadoop.hive.hbase.HBaseSerDe
java.lang.RuntimeException: MetaException(message:java.lang.ClassNotFoundException Class org.apache.hadoop.hive.hbase.HBaseSerDe not found)
        at org.apache.hadoop.hive.ql.metadata.Table.getDeserializerFromMetaStore(Table.java:278)
        at org.apache.hadoop.hive.ql.metadata.Table.getDeserializer(Table.java:258)
        at org.apache.hadoop.hive.ql.metadata.Table.getCols(Table.java:605)
        at org.apache.spark.sql.hive.client.HiveClientImpl$$anonfun$getTableOption$1$$anonfun$apply$7.apply(HiveClientImpl.scala:358)
        at org.apache.spark.sql.hive.client.HiveClientImpl$$anonfun$getTableOption$1$$anonfun$apply$7.apply(HiveClientImpl.scala:355)
        at scala.Option.map(Option.scala:146)
        at org.apache.spark.sql.hive.client.HiveClientImpl$$anonfun$getTableOption$1.apply(HiveClientImpl.scala:355)
        at org.apache.spark.sql.hive.client.HiveClientImpl$$anonfun$getTableOption$1.apply(HiveClientImpl.scala:353)
        at org.apache.spark.sql.hive.client.HiveClientImpl$$anonfun$withHiveState$1.apply(HiveClientImpl.scala:272)
        at org.apache.spark.sql.hive.client.HiveClientImpl.liftedTree1$1(HiveClientImpl.scala:210)
        at org.apache.spark.sql.hive.client.HiveClientImpl.retryLocked(HiveClientImpl.scala:209)
        at org.apache.spark.sql.hive.client.HiveClientImpl.withHiveState(HiveClientImpl.scala:255)
        at org.apache.spark.sql.hive.client.HiveClientImpl.getTableOption(HiveClientImpl.scala:353)
        at org.apache.spark.sql.hive.client.HiveClient$class.getTable(HiveClient.scala:81)
        at org.apache.spark.sql.hive.client.HiveClientImpl.getTable(HiveClientImpl.scala:83)
        at org.apache.spark.sql.hive.HiveExternalCatalog$$anonfun$getRawTable$1.apply(HiveExternalCatalog.scala:118)
        at org.apache.spark.sql.hive.HiveExternalCatalog$$anonfun$getRawTable$1.apply(HiveExternalCatalog.scala:118)
        at org.apache.spark.sql.hive.HiveExternalCatalog.withClient(HiveExternalCatalog.scala:97)
        at org.apache.spark.sql.hive.HiveExternalCatalog.getRawTable(HiveExternalCatalog.scala:117)
        at org.apache.spark.sql.hive.HiveExternalCatalog$$anonfun$getTable$1.apply(HiveExternalCatalog.scala:684)
        at org.apache.spark.sql.hive.HiveExternalCatalog$$anonfun$getTable$1.apply(HiveExternalCatalog.scala:684)
        at org.apache.spark.sql.hive.HiveExternalCatalog.withClient(HiveExternalCatalog.scala:97)
        at org.apache.spark.sql.hive.HiveExternalCatalog.getTable(HiveExternalCatalog.scala:683)
        at org.apache.spark.sql.catalyst.catalog.SessionCatalog.lookupRelation(SessionCatalog.scala:669)
        at org.apache.spark.sql.catalyst.analysis.Analyzer$ResolveRelations$.org$apache$spark$sql$catalyst$analysis$Analyzer$ResolveRelations$$lookupTableFromCatalog(Analyzer.scala:660)
        at org.apache.spark.sql.catalyst.analysis.Analyzer$ResolveRelations$.resolveRelation(Analyzer.scala:615)
        at org.apache.spark.sql.catalyst.analysis.Analyzer$ResolveRelations$$anonfun$apply$8.applyOrElse(Analyzer.scala:645)
        at org.apache.spark.sql.catalyst.analysis.Analyzer$ResolveRelations$$anonfun$apply$8.applyOrElse(Analyzer.scala:638)
        at org.apache.spark.sql.catalyst.trees.TreeNode$$anonfun$transformUp$1.apply(TreeNode.scala:289)
        at org.apache.spark.sql.catalyst.trees.TreeNode$$anonfun$transformUp$1.apply(TreeNode.scala:289)
        at org.apache.spark.sql.catalyst.trees.CurrentOrigin$.withOrigin(TreeNode.scala:70)
        at org.apache.spark.sql.catalyst.trees.TreeNode.transformUp(TreeNode.scala:288)
        at org.apache.spark.sql.catalyst.trees.TreeNode$$anonfun$3.apply(TreeNode.scala:286)
        at org.apache.spark.sql.catalyst.trees.TreeNode$$anonfun$3.apply(TreeNode.scala:286)
        at org.apache.spark.sql.catalyst.trees.TreeNode$$anonfun$4.apply(TreeNode.scala:306)
        at org.apache.spark.sql.catalyst.trees.TreeNode.mapProductIterator(TreeNode.scala:187)
        at org.apache.spark.sql.catalyst.trees.TreeNode.mapChildren(TreeNode.scala:304)
        at org.apache.spark.sql.catalyst.trees.TreeNode.transformUp(TreeNode.scala:286)
        at org.apache.spark.sql.catalyst.analysis.Analyzer$ResolveRelations$.apply(Analyzer.scala:638)
        at org.apache.spark.sql.catalyst.analysis.Analyzer$ResolveRelations$.apply(Analyzer.scala:584)
        at org.apache.spark.sql.catalyst.rules.RuleExecutor$$anonfun$execute$1$$anonfun$apply$1.apply(RuleExecutor.scala:87)
        at org.apache.spark.sql.catalyst.rules.RuleExecutor$$anonfun$execute$1$$anonfun$apply$1.apply(RuleExecutor.scala:84)
        at scala.collection.LinearSeqOptimized$class.foldLeft(LinearSeqOptimized.scala:124)
        at scala.collection.immutable.List.foldLeft(List.scala:84)
        at org.apache.spark.sql.catalyst.rules.RuleExecutor$$anonfun$execute$1.apply(RuleExecutor.scala:84)
        at org.apache.spark.sql.catalyst.rules.RuleExecutor$$anonfun$execute$1.apply(RuleExecutor.scala:76)
        at scala.collection.immutable.List.foreach(List.scala:381)
        at org.apache.spark.sql.catalyst.rules.RuleExecutor.execute(RuleExecutor.scala:76)
        at org.apache.spark.sql.catalyst.analysis.Analyzer.org$apache$spark$sql$catalyst$analysis$Analyzer$$executeSameContext(Analyzer.scala:123)
        at org.apache.spark.sql.catalyst.analysis.Analyzer.execute(Analyzer.scala:117)
        at org.apache.spark.sql.catalyst.analysis.Analyzer.executeAndCheck(Analyzer.scala:102)
        at org.apache.spark.sql.execution.QueryExecution.analyzed$lzycompute(QueryExecution.scala:57)
        at org.apache.spark.sql.execution.QueryExecution.analyzed(QueryExecution.scala:55)
        at org.apache.spark.sql.execution.QueryExecution.assertAnalyzed(QueryExecution.scala:47)
        at org.apache.spark.sql.Dataset$.ofRows(Dataset.scala:74)
        at org.apache.spark.sql.SparkSession.sql(SparkSession.scala:638)
        at org.apache.spark.sql.SQLContext.sql(SQLContext.scala:694)
        at org.apache.spark.sql.hive.thriftserver.SparkSQLDriver.run(SparkSQLDriver.scala:62)
        at org.apache.spark.sql.hive.thriftserver.SparkSQLCLIDriver.processCmd(SparkSQLCLIDriver.scala:355)
        at org.apache.hadoop.hive.cli.CliDriver.processLine(CliDriver.java:376)
        at org.apache.spark.sql.hive.thriftserver.SparkSQLCLIDriver$.main(SparkSQLCLIDriver.scala:263)
        at org.apache.spark.sql.hive.thriftserver.SparkSQLCLIDriver.main(SparkSQLCLIDriver.scala)
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
        at java.lang.reflect.Method.invoke(Method.java:497)
        at org.apache.spark.deploy.JavaMainApplication.start(SparkApplication.scala:52)
        at org.apache.spark.deploy.SparkSubmit$.org$apache$spark$deploy$SparkSubmit$$runMain(SparkSubmit.scala:879)
        at org.apache.spark.deploy.SparkSubmit$.doRunMain$1(SparkSubmit.scala:197)
        at org.apache.spark.deploy.SparkSubmit$.submit(SparkSubmit.scala:227)
        at org.apache.spark.deploy.SparkSubmit$.main(SparkSubmit.scala:136)
        at org.apache.spark.deploy.SparkSubmit.main(SparkSubmit.scala)
Caused by: MetaException(message:java.lang.ClassNotFoundException Class org.apache.hadoop.hive.hbase.HBaseSerDe not found)
        at org.apache.hadoop.hive.metastore.MetaStoreUtils.getDeserializer(MetaStoreUtils.java:399)
        at org.apache.hadoop.hive.ql.metadata.Table.getDeserializerFromMetaStore(Table.java:276)
        ... 71 more
2018-06-14 10:42:39 ERROR SparkSQLDriver:91 - Failed in [select * from hbase_hive_wtb_ow_operation]
java.lang.RuntimeException: org.apache.hadoop.hive.ql.metadata.HiveException: Error in loading storage handler.org.apache.hadoop.hive.hbase.HBaseStorageHandler
        at org.apache.hadoop.hive.ql.metadata.Table.getStorageHandler(Table.java:292)
        at org.apache.spark.sql.hive.client.HiveClientImpl$$anonfun$getTableOption$1$$anonfun$apply$7.apply(HiveClientImpl.scala:388)
        at org.apache.spark.sql.hive.client.HiveClientImpl$$anonfun$getTableOption$1$$anonfun$apply$7.apply(HiveClientImpl.scala:355)
        at scala.Option.map(Option.scala:146)
        at org.apache.spark.sql.hive.client.HiveClientImpl$$anonfun$getTableOption$1.apply(HiveClientImpl.scala:355)
        at org.apache.spark.sql.hive.client.HiveClientImpl$$anonfun$getTableOption$1.apply(HiveClientImpl.scala:353)
        at org.apache.spark.sql.hive.client.HiveClientImpl$$anonfun$withHiveState$1.apply(HiveClientImpl.scala:272)
        at org.apache.spark.sql.hive.client.HiveClientImpl.liftedTree1$1(HiveClientImpl.scala:210)
        at org.apache.spark.sql.hive.client.HiveClientImpl.retryLocked(HiveClientImpl.scala:209)
        at org.apache.spark.sql.hive.client.HiveClientImpl.withHiveState(HiveClientImpl.scala:255)
        at org.apache.spark.sql.hive.client.HiveClientImpl.getTableOption(HiveClientImpl.scala:353)
        at org.apache.spark.sql.hive.client.HiveClient$class.getTable(HiveClient.scala:81)
        at org.apache.spark.sql.hive.client.HiveClientImpl.getTable(HiveClientImpl.scala:83)
        at org.apache.spark.sql.hive.HiveExternalCatalog$$anonfun$getRawTable$1.apply(HiveExternalCatalog.scala:118)
        at org.apache.spark.sql.hive.HiveExternalCatalog$$anonfun$getRawTable$1.apply(HiveExternalCatalog.scala:118)
        at org.apache.spark.sql.hive.HiveExternalCatalog.withClient(HiveExternalCatalog.scala:97)
        at org.apache.spark.sql.hive.HiveExternalCatalog.getRawTable(HiveExternalCatalog.scala:117)
        at org.apache.spark.sql.hive.HiveExternalCatalog$$anonfun$getTable$1.apply(HiveExternalCatalog.scala:684)
        at org.apache.spark.sql.hive.HiveExternalCatalog$$anonfun$getTable$1.apply(HiveExternalCatalog.scala:684)
        at org.apache.spark.sql.hive.HiveExternalCatalog.withClient(HiveExternalCatalog.scala:97)
        at org.apache.spark.sql.hive.HiveExternalCatalog.getTable(HiveExternalCatalog.scala:683)
        at org.apache.spark.sql.catalyst.catalog.SessionCatalog.lookupRelation(SessionCatalog.scala:669)
        at org.apache.spark.sql.catalyst.analysis.Analyzer$ResolveRelations$.org$apache$spark$sql$catalyst$analysis$Analyzer$ResolveRelations$$lookupTableFromCatalog(Analyzer.scala:660)
        at org.apache.spark.sql.catalyst.analysis.Analyzer$ResolveRelations$.resolveRelation(Analyzer.scala:615)
        at org.apache.spark.sql.catalyst.analysis.Analyzer$ResolveRelations$$anonfun$apply$8.applyOrElse(Analyzer.scala:645)
        at org.apache.spark.sql.catalyst.analysis.Analyzer$ResolveRelations$$anonfun$apply$8.applyOrElse(Analyzer.scala:638)
        at org.apache.spark.sql.catalyst.trees.TreeNode$$anonfun$transformUp$1.apply(TreeNode.scala:289)
        at org.apache.spark.sql.catalyst.trees.TreeNode$$anonfun$transformUp$1.apply(TreeNode.scala:289)
        at org.apache.spark.sql.catalyst.trees.CurrentOrigin$.withOrigin(TreeNode.scala:70)
        at org.apache.spark.sql.catalyst.trees.TreeNode.transformUp(TreeNode.scala:288)
        at org.apache.spark.sql.catalyst.trees.TreeNode$$anonfun$3.apply(TreeNode.scala:286)
        at org.apache.spark.sql.catalyst.trees.TreeNode$$anonfun$3.apply(TreeNode.scala:286)
        at org.apache.spark.sql.catalyst.trees.TreeNode$$anonfun$4.apply(TreeNode.scala:306)
        at org.apache.spark.sql.catalyst.trees.TreeNode.mapProductIterator(TreeNode.scala:187)
        at org.apache.spark.sql.catalyst.trees.TreeNode.mapChildren(TreeNode.scala:304)
        at org.apache.spark.sql.catalyst.trees.TreeNode.transformUp(TreeNode.scala:286)
        at org.apache.spark.sql.catalyst.analysis.Analyzer$ResolveRelations$.apply(Analyzer.scala:638)
        at org.apache.spark.sql.catalyst.analysis.Analyzer$ResolveRelations$.apply(Analyzer.scala:584)
        at org.apache.spark.sql.catalyst.rules.RuleExecutor$$anonfun$execute$1$$anonfun$apply$1.apply(RuleExecutor.scala:87)
        at org.apache.spark.sql.catalyst.rules.RuleExecutor$$anonfun$execute$1$$anonfun$apply$1.apply(RuleExecutor.scala:84)
        at scala.collection.LinearSeqOptimized$class.foldLeft(LinearSeqOptimized.scala:124)
        at scala.collection.immutable.List.foldLeft(List.scala:84)
        at org.apache.spark.sql.catalyst.rules.RuleExecutor$$anonfun$execute$1.apply(RuleExecutor.scala:84)
        at org.apache.spark.sql.catalyst.rules.RuleExecutor$$anonfun$execute$1.apply(RuleExecutor.scala:76)
        at scala.collection.immutable.List.foreach(List.scala:381)
        at org.apache.spark.sql.catalyst.rules.RuleExecutor.execute(RuleExecutor.scala:76)
        at org.apache.spark.sql.catalyst.analysis.Analyzer.org$apache$spark$sql$catalyst$analysis$Analyzer$$executeSameContext(Analyzer.scala:123)
        at org.apache.spark.sql.catalyst.analysis.Analyzer.execute(Analyzer.scala:117)
        at org.apache.spark.sql.catalyst.analysis.Analyzer.executeAndCheck(Analyzer.scala:102)
        at org.apache.spark.sql.execution.QueryExecution.analyzed$lzycompute(QueryExecution.scala:57)
        at org.apache.spark.sql.execution.QueryExecution.analyzed(QueryExecution.scala:55)
        at org.apache.spark.sql.execution.QueryExecution.assertAnalyzed(QueryExecution.scala:47)
        at org.apache.spark.sql.Dataset$.ofRows(Dataset.scala:74)
        at org.apache.spark.sql.SparkSession.sql(SparkSession.scala:638)
        at org.apache.spark.sql.SQLContext.sql(SQLContext.scala:694)
        at org.apache.spark.sql.hive.thriftserver.SparkSQLDriver.run(SparkSQLDriver.scala:62)
        at org.apache.spark.sql.hive.thriftserver.SparkSQLCLIDriver.processCmd(SparkSQLCLIDriver.scala:355)
        at org.apache.hadoop.hive.cli.CliDriver.processLine(CliDriver.java:376)
        at org.apache.spark.sql.hive.thriftserver.SparkSQLCLIDriver$.main(SparkSQLCLIDriver.scala:263)
        at org.apache.spark.sql.hive.thriftserver.SparkSQLCLIDriver.main(SparkSQLCLIDriver.scala)
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
        at java.lang.reflect.Method.invoke(Method.java:497)
        at org.apache.spark.deploy.JavaMainApplication.start(SparkApplication.scala:52)
        at org.apache.spark.deploy.SparkSubmit$.org$apache$spark$deploy$SparkSubmit$$runMain(SparkSubmit.scala:879)
        at org.apache.spark.deploy.SparkSubmit$.doRunMain$1(SparkSubmit.scala:197)
        at org.apache.spark.deploy.SparkSubmit$.submit(SparkSubmit.scala:227)
        at org.apache.spark.deploy.SparkSubmit$.main(SparkSubmit.scala:136)
        at org.apache.spark.deploy.SparkSubmit.main(SparkSubmit.scala)
Caused by: org.apache.hadoop.hive.ql.metadata.HiveException: Error in loading storage handler.org.apache.hadoop.hive.hbase.HBaseStorageHandler
        at org.apache.hadoop.hive.ql.metadata.HiveUtils.getStorageHandler(HiveUtils.java:315)
        at org.apache.hadoop.hive.ql.metadata.Table.getStorageHandler(Table.java:287)
        ... 69 more
Caused by: java.lang.ClassNotFoundException: org.apache.hadoop.hive.hbase.HBaseStorageHandler
        at java.net.URLClassLoader.findClass(URLClassLoader.java:381)
        at java.lang.ClassLoader.loadClass(ClassLoader.java:424)
        at java.lang.ClassLoader.loadClass(ClassLoader.java:357)
        at java.lang.Class.forName0(Native Method)
        at java.lang.Class.forName(Class.java:348)
        at org.apache.hadoop.hive.ql.metadata.HiveUtils.getStorageHandler(HiveUtils.java:309)
        ... 70 more
java.lang.RuntimeException: org.apache.hadoop.hive.ql.metadata.HiveException: Error in loading storage handler.org.apache.hadoop.hive.hbase.HBaseStorageHandler
        at org.apache.hadoop.hive.ql.metadata.Table.getStorageHandler(Table.java:292)
        at org.apache.spark.sql.hive.client.HiveClientImpl$$anonfun$getTableOption$1$$anonfun$apply$7.apply(HiveClientImpl.scala:388)
        at org.apache.spark.sql.hive.client.HiveClientImpl$$anonfun$getTableOption$1$$anonfun$apply$7.apply(HiveClientImpl.scala:355)
        at scala.Option.map(Option.scala:146)
        at org.apache.spark.sql.hive.client.HiveClientImpl$$anonfun$getTableOption$1.apply(HiveClientImpl.scala:355)
        at org.apache.spark.sql.hive.client.HiveClientImpl$$anonfun$getTableOption$1.apply(HiveClientImpl.scala:353)
        at org.apache.spark.sql.hive.client.HiveClientImpl$$anonfun$withHiveState$1.apply(HiveClientImpl.scala:272)
        at org.apache.spark.sql.hive.client.HiveClientImpl.liftedTree1$1(HiveClientImpl.scala:210)
        at org.apache.spark.sql.hive.client.HiveClientImpl.retryLocked(HiveClientImpl.scala:209)
        at org.apache.spark.sql.hive.client.HiveClientImpl.withHiveState(HiveClientImpl.scala:255)
        at org.apache.spark.sql.hive.client.HiveClientImpl.getTableOption(HiveClientImpl.scala:353)
        at org.apache.spark.sql.hive.client.HiveClient$class.getTable(HiveClient.scala:81)
        at org.apache.spark.sql.hive.client.HiveClientImpl.getTable(HiveClientImpl.scala:83)
        at org.apache.spark.sql.hive.HiveExternalCatalog$$anonfun$getRawTable$1.apply(HiveExternalCatalog.scala:118)
        at org.apache.spark.sql.hive.HiveExternalCatalog$$anonfun$getRawTable$1.apply(HiveExternalCatalog.scala:118)
        at org.apache.spark.sql.hive.HiveExternalCatalog.withClient(HiveExternalCatalog.scala:97)
        at org.apache.spark.sql.hive.HiveExternalCatalog.getRawTable(HiveExternalCatalog.scala:117)
        at org.apache.spark.sql.hive.HiveExternalCatalog$$anonfun$getTable$1.apply(HiveExternalCatalog.scala:684)
        at org.apache.spark.sql.hive.HiveExternalCatalog$$anonfun$getTable$1.apply(HiveExternalCatalog.scala:684)
        at org.apache.spark.sql.hive.HiveExternalCatalog.withClient(HiveExternalCatalog.scala:97)
        at org.apache.spark.sql.hive.HiveExternalCatalog.getTable(HiveExternalCatalog.scala:683)
        at org.apache.spark.sql.catalyst.catalog.SessionCatalog.lookupRelation(SessionCatalog.scala:669)
        at org.apache.spark.sql.catalyst.analysis.Analyzer$ResolveRelations$.org$apache$spark$sql$catalyst$analysis$Analyzer$ResolveRelations$$lookupTableFromCatalog(Analyzer.scala:660)
        at org.apache.spark.sql.catalyst.analysis.Analyzer$ResolveRelations$.resolveRelation(Analyzer.scala:615)
        at org.apache.spark.sql.catalyst.analysis.Analyzer$ResolveRelations$$anonfun$apply$8.applyOrElse(Analyzer.scala:645)
        at org.apache.spark.sql.catalyst.analysis.Analyzer$ResolveRelations$$anonfun$apply$8.applyOrElse(Analyzer.scala:638)
        at org.apache.spark.sql.catalyst.trees.TreeNode$$anonfun$transformUp$1.apply(TreeNode.scala:289)
        at org.apache.spark.sql.catalyst.trees.TreeNode$$anonfun$transformUp$1.apply(TreeNode.scala:289)
        at org.apache.spark.sql.catalyst.trees.CurrentOrigin$.withOrigin(TreeNode.scala:70)
        at org.apache.spark.sql.catalyst.trees.TreeNode.transformUp(TreeNode.scala:288)
        at org.apache.spark.sql.catalyst.trees.TreeNode$$anonfun$3.apply(TreeNode.scala:286)
        at org.apache.spark.sql.catalyst.trees.TreeNode$$anonfun$3.apply(TreeNode.scala:286)
        at org.apache.spark.sql.catalyst.trees.TreeNode$$anonfun$4.apply(TreeNode.scala:306)
        at org.apache.spark.sql.catalyst.trees.TreeNode.mapProductIterator(TreeNode.scala:187)
        at org.apache.spark.sql.catalyst.trees.TreeNode.mapChildren(TreeNode.scala:304)
        at org.apache.spark.sql.catalyst.trees.TreeNode.transformUp(TreeNode.scala:286)
        at org.apache.spark.sql.catalyst.analysis.Analyzer$ResolveRelations$.apply(Analyzer.scala:638)
        at org.apache.spark.sql.catalyst.analysis.Analyzer$ResolveRelations$.apply(Analyzer.scala:584)
        at org.apache.spark.sql.catalyst.rules.RuleExecutor$$anonfun$execute$1$$anonfun$apply$1.apply(RuleExecutor.scala:87)
        at org.apache.spark.sql.catalyst.rules.RuleExecutor$$anonfun$execute$1$$anonfun$apply$1.apply(RuleExecutor.scala:84)
        at scala.collection.LinearSeqOptimized$class.foldLeft(LinearSeqOptimized.scala:124)
        at scala.collection.immutable.List.foldLeft(List.scala:84)
        at org.apache.spark.sql.catalyst.rules.RuleExecutor$$anonfun$execute$1.apply(RuleExecutor.scala:84)
        at org.apache.spark.sql.catalyst.rules.RuleExecutor$$anonfun$execute$1.apply(RuleExecutor.scala:76)
        at scala.collection.immutable.List.foreach(List.scala:381)
        at org.apache.spark.sql.catalyst.rules.RuleExecutor.execute(RuleExecutor.scala:76)
        at org.apache.spark.sql.catalyst.analysis.Analyzer.org$apache$spark$sql$catalyst$analysis$Analyzer$$executeSameContext(Analyzer.scala:123)
        at org.apache.spark.sql.catalyst.analysis.Analyzer.execute(Analyzer.scala:117)
        at org.apache.spark.sql.catalyst.analysis.Analyzer.executeAndCheck(Analyzer.scala:102)
        at org.apache.spark.sql.execution.QueryExecution.analyzed$lzycompute(QueryExecution.scala:57)
        at org.apache.spark.sql.execution.QueryExecution.analyzed(QueryExecution.scala:55)
        at org.apache.spark.sql.execution.QueryExecution.assertAnalyzed(QueryExecution.scala:47)
        at org.apache.spark.sql.Dataset$.ofRows(Dataset.scala:74)
        at org.apache.spark.sql.SparkSession.sql(SparkSession.scala:638)
        at org.apache.spark.sql.SQLContext.sql(SQLContext.scala:694)
        at org.apache.spark.sql.hive.thriftserver.SparkSQLDriver.run(SparkSQLDriver.scala:62)
        at org.apache.spark.sql.hive.thriftserver.SparkSQLCLIDriver.processCmd(SparkSQLCLIDriver.scala:355)
        at org.apache.hadoop.hive.cli.CliDriver.processLine(CliDriver.java:376)
        at org.apache.spark.sql.hive.thriftserver.SparkSQLCLIDriver$.main(SparkSQLCLIDriver.scala:263)
        at org.apache.spark.sql.hive.thriftserver.SparkSQLCLIDriver.main(SparkSQLCLIDriver.scala)
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
        at java.lang.reflect.Method.invoke(Method.java:497)
        at org.apache.spark.deploy.JavaMainApplication.start(SparkApplication.scala:52)
        at org.apache.spark.deploy.SparkSubmit$.org$apache$spark$deploy$SparkSubmit$$runMain(SparkSubmit.scala:879)
        at org.apache.spark.deploy.SparkSubmit$.doRunMain$1(SparkSubmit.scala:197)
        at org.apache.spark.deploy.SparkSubmit$.submit(SparkSubmit.scala:227)
        at org.apache.spark.deploy.SparkSubmit$.main(SparkSubmit.scala:136)
        at org.apache.spark.deploy.SparkSubmit.main(SparkSubmit.scala)
Caused by: org.apache.hadoop.hive.ql.metadata.HiveException: Error in loading storage handler.org.apache.hadoop.hive.hbase.HBaseStorageHandler
        at org.apache.hadoop.hive.ql.metadata.HiveUtils.getStorageHandler(HiveUtils.java:315)
        at org.apache.hadoop.hive.ql.metadata.Table.getStorageHandler(Table.java:287)
        ... 69 more
Caused by: java.lang.ClassNotFoundException: org.apache.hadoop.hive.hbase.HBaseStorageHandler
        at java.net.URLClassLoader.findClass(URLClassLoader.java:381)
        at java.lang.ClassLoader.loadClass(ClassLoader.java:424)
        at java.lang.ClassLoader.loadClass(ClassLoader.java:357)
        at java.lang.Class.forName0(Native Method)
        at java.lang.Class.forName(Class.java:348)
        at org.apache.hadoop.hive.ql.metadata.HiveUtils.getStorageHandler(HiveUtils.java:309)
        ... 70 more

spark-sql&gt; </code></pre><p>检查hive-hbase-handler-1.2.1.jar路径：</p><pre><code class="language-html">root@master:/usr/local/apache-hive-1.2.1/lib# ls
accumulo-core-1.6.0.jar               commons-httpclient-3.0.1.jar            hive-hbase-handler-1.2.1.jar          libfb303-0.9.2.jar
accumulo-fate-1.6.0.jar               commons-io-2.4.jar                      hive-hwi-1.2.1.jar                    libthrift-0.9.2.jar
accumulo-start-1.6.0.jar              commons-lang-2.6.jar                    hive-jdbc-1.2.1.jar                   log4j-1.2.16.jar
accumulo-trace-1.6.0.jar              commons-logging-1.1.3.jar               hive-jdbc-1.2.1-standalone.jar        mail-1.4.1.jar
activation-1.1.jar                    commons-math-2.1.jar                    hive-metastore-1.2.1.jar              maven-scm-api-1.4.jar
ant-1.9.1.jar                         commons-pool-1.5.4.jar                  hive-serde-1.2.1.jar                  maven-scm-provider-svn-commons-1.4.jar
ant-launcher-1.9.1.jar                commons-vfs2-2.0.jar                    hive-service-1.2.1.jar                maven-scm-provider-svnexe-1.4.jar
antlr-2.7.7.jar                       curator-client-2.6.0.jar                hive-shims-0.20S-1.2.1.jar            mysql-connector-java-5.1.13-bin.jar
antlr-runtime-3.4.jar                 curator-framework-2.6.0.jar             hive-shims-0.23-1.2.1.jar             netty-3.7.0.Final.jar
apache-curator-2.6.0.pom              curator-recipes-2.6.0.jar               hive-shims-1.2.1.jar                  opencsv-2.3.jar
apache-log4j-extras-1.2.17.jar        datanucleus-api-jdo-3.2.6.jar           hive-shims-common-1.2.1.jar           oro-2.0.8.jar
asm-commons-3.1.jar                   datanucleus-core-3.2.10.jar             hive-shims-scheduler-1.2.1.jar        paranamer-2.3.jar
asm-tree-3.1.jar                      datanucleus-rdbms-3.2.9.jar             hive-testutils-1.2.1.jar              parquet-hadoop-bundle-1.6.0.jar
avro-1.7.5.jar                        derby-10.10.2.0.jar                     httpclient-4.4.jar                    pentaho-aggdesigner-algorithm-5.1.5-jhyde.jar
bonecp-0.8.0.RELEASE.jar              eigenbase-properties-1.1.5.jar          httpcore-4.4.jar                      php
calcite-avatica-1.2.0-incubating.jar  geronimo-annotation_1.0_spec-1.1.1.jar  ivy-2.4.0.jar                         plexus-utils-1.5.6.jar
calcite-core-1.2.0-incubating.jar     geronimo-jaspic_1.0_spec-1.0.jar        janino-2.7.6.jar                      py
calcite-linq4j-1.2.0-incubating.jar   geronimo-jta_1.1_spec-1.1.1.jar         jcommander-1.32.jar                   regexp-1.3.jar
commons-beanutils-1.7.0.jar           groovy-all-2.1.6.jar                    jdo-api-3.0.1.jar                     servlet-api-2.5.jar
commons-beanutils-core-1.8.0.jar      guava-14.0.1.jar                        jetty-all-7.6.0.v20120127.jar         snappy-java-1.0.5.jar
commons-cli-1.2.jar                   hamcrest-core-1.1.jar                   jetty-all-server-7.6.0.v20120127.jar  ST4-4.0.4.jar
commons-codec-1.4.jar                 hive-accumulo-handler-1.2.1.jar         jline-2.12.jar                        stax-api-1.0.1.jar
commons-collections-3.2.1.jar         hive-ant-1.2.1.jar                      joda-time-2.5.jar                     stringtemplate-3.2.1.jar
commons-compiler-2.7.6.jar            hive-beeline-1.2.1.jar                  jpam-1.1.jar                          super-csv-2.2.0.jar
commons-compress-1.4.1.jar            hive-cli-1.2.1.jar                      json-20090211.jar                     tempus-fugit-1.1.jar
commons-configuration-1.6.jar         hive-common-1.2.1.jar                   jsr305-3.0.0.jar                      velocity-1.5.jar
commons-dbcp-1.4.jar                  hive-contrib-1.2.1.jar                  jta-1.1.jar                           xz-1.0.jar
commons-digester-1.8.jar              hive-exec-1.2.1.jar                     junit-4.11.jar                        zookeeper-3.4.6.jar
root@master:/usr/local/apache-hive-1.2.1/lib# ls | grep hbase
hive-hbase-handler-1.2.1.jar
root@master:/usr/local/apache-hive-1.2.1/lib# </code></pre><p>加载hive-hbase-handler-1.2.1.jar的路径，重新在spark集群提交spark sql。</p><pre><code class="language-html">root@master:~# spark-sql  --master spark://192.168.189.1:7077 --driver-class-path /usr/local/apache-hive-1.2.1/lib/mysql-connector-java-5.1.13-bin.jar:/usr/local/apache-hive-1.2.1/lib/hive-hbase-handler-1.2.1.jar  --executor-memory 512m  --total-executor-cores 4

</code></pre><pre><code class="language-html">2018-06-14 13:58:33 WARN  NativeCodeLoader:62 - Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
2018-06-14 13:58:36 WARN  HiveConf:2753 - HiveConf of name hive.server2.http.endpoint does not exist
2018-06-14 13:58:36 INFO  HiveMetaStore:589 - 0: Opening raw store with implemenation class:org.apache.hadoop.hive.metastore.ObjectStore
2018-06-14 13:58:36 INFO  ObjectStore:289 - ObjectStore, initialize called
2018-06-14 13:58:37 INFO  Persistence:77 - Property hive.metastore.integral.jdo.pushdown unknown - will be ignored
2018-06-14 13:58:37 INFO  Persistence:77 - Property datanucleus.cache.level2 unknown - will be ignored
2018-06-14 13:58:39 WARN  HiveConf:2753 - HiveConf of name hive.server2.http.endpoint does not exist
2018-06-14 13:58:39 INFO  ObjectStore:370 - Setting MetaStore object pin classes with hive.metastore.cache.pinobjtypes="Table,StorageDescriptor,SerDeInfo,Partition,Database,Type,FieldSchema,Order"
2018-06-14 13:58:40 INFO  Datastore:77 - The class "org.apache.hadoop.hive.metastore.model.MFieldSchema" is tagged as "embedded-only" so does not have its own datastore table.
2018-06-14 13:58:40 INFO  Datastore:77 - The class "org.apache.hadoop.hive.metastore.model.MOrder" is tagged as "embedded-only" so does not have its own datastore table.
2018-06-14 13:58:41 INFO  Datastore:77 - The class "org.apache.hadoop.hive.metastore.model.MFieldSchema" is tagged as "embedded-only" so does not have its own datastore table.
2018-06-14 13:58:41 INFO  Datastore:77 - The class "org.apache.hadoop.hive.metastore.model.MOrder" is tagged as "embedded-only" so does not have its own datastore table.
2018-06-14 13:58:42 INFO  Query:77 - Reading in results for query "org.datanucleus.store.rdbms.query.SQLQuery@0" since the connection used is closing
2018-06-14 13:58:42 INFO  MetaStoreDirectSql:139 - Using direct SQL, underlying DB is MYSQL
2018-06-14 13:58:42 INFO  ObjectStore:272 - Initialized ObjectStore
2018-06-14 13:58:42 INFO  HiveMetaStore:663 - Added admin role in metastore
2018-06-14 13:58:42 INFO  HiveMetaStore:672 - Added public role in metastore
2018-06-14 13:58:42 INFO  HiveMetaStore:712 - No user is added in admin role, since config is empty
2018-06-14 13:58:43 INFO  HiveMetaStore:746 - 0: get_all_databases
2018-06-14 13:58:43 INFO  audit:371 - ugi=root  ip=unknown-ip-addr      cmd=get_all_databases
2018-06-14 13:58:43 INFO  HiveMetaStore:746 - 0: get_functions: db=default pat=*
2018-06-14 13:58:43 INFO  audit:371 - ugi=root  ip=unknown-ip-addr      cmd=get_functions: db=default pat=*
2018-06-14 13:58:43 INFO  Datastore:77 - The class "org.apache.hadoop.hive.metastore.model.MResourceUri" is tagged as "embedded-only" so does not have its own datastore table.
2018-06-14 13:58:43 INFO  HiveMetaStore:746 - 0: get_functions: db=hive pat=*
2018-06-14 13:58:43 INFO  audit:371 - ugi=root  ip=unknown-ip-addr      cmd=get_functions: db=hive pat=*
2018-06-14 13:58:43 INFO  HiveMetaStore:746 - 0: get_functions: db=hivestudy pat=*
2018-06-14 13:58:43 INFO  audit:371 - ugi=root  ip=unknown-ip-addr      cmd=get_functions: db=hivestudy pat=*
2018-06-14 13:58:43 INFO  HiveMetaStore:746 - 0: get_functions: db=hrs pat=*
2018-06-14 13:58:43 INFO  audit:371 - ugi=root  ip=unknown-ip-addr      cmd=get_functions: db=hrs pat=*
2018-06-14 13:58:44 INFO  SessionState:641 - Created local directory: /tmp/54a94cff-e4a8-4500-bd56-7eda47f5b72f_resources
2018-06-14 13:58:45 INFO  SessionState:641 - Created HDFS directory: /tmp/hive/root/54a94cff-e4a8-4500-bd56-7eda47f5b72f
2018-06-14 13:58:45 INFO  SessionState:641 - Created local directory: /tmp/root/54a94cff-e4a8-4500-bd56-7eda47f5b72f
2018-06-14 13:58:45 INFO  SessionState:641 - Created HDFS directory: /tmp/hive/root/54a94cff-e4a8-4500-bd56-7eda47f5b72f/_tmp_space.db
2018-06-14 13:58:45 INFO  SparkContext:54 - Running Spark version 2.3.0
2018-06-14 13:58:45 INFO  SparkContext:54 - Submitted application: SparkSQL::master
2018-06-14 13:58:46 INFO  SecurityManager:54 - Changing view acls to: root
2018-06-14 13:58:46 INFO  SecurityManager:54 - Changing modify acls to: root
2018-06-14 13:58:46 INFO  SecurityManager:54 - Changing view acls groups to: 
2018-06-14 13:58:46 INFO  SecurityManager:54 - Changing modify acls groups to: 
2018-06-14 13:58:46 INFO  SecurityManager:54 - SecurityManager: authentication disabled; ui acls disabled; users  with view permissions: Set(root); groups with view permissions: Set(); users  with modify permissions: Set(root); groups with modify permissions: Set()
2018-06-14 13:58:46 INFO  Utils:54 - Successfully started service 'sparkDriver' on port 35632.
2018-06-14 13:58:47 INFO  SparkEnv:54 - Registering MapOutputTracker
2018-06-14 13:58:47 INFO  SparkEnv:54 - Registering BlockManagerMaster
2018-06-14 13:58:47 INFO  BlockManagerMasterEndpoint:54 - Using org.apache.spark.storage.DefaultTopologyMapper for getting topology information
2018-06-14 13:58:47 INFO  BlockManagerMasterEndpoint:54 - BlockManagerMasterEndpoint up
2018-06-14 13:58:47 INFO  DiskBlockManager:54 - Created local directory at /tmp/blockmgr-4040f00b-d75a-4d79-be9c-fdd17fc20bae
2018-06-14 13:58:47 INFO  MemoryStore:54 - MemoryStore started with capacity 413.9 MB
2018-06-14 13:58:47 INFO  SparkEnv:54 - Registering OutputCommitCoordinator
2018-06-14 13:58:47 INFO  log:192 - Logging initialized @23015ms
2018-06-14 13:58:48 INFO  Server:346 - jetty-9.3.z-SNAPSHOT
2018-06-14 13:58:48 INFO  Server:414 - Started @23287ms
2018-06-14 13:58:48 INFO  AbstractConnector:278 - Started ServerConnector@230fbd5{HTTP/1.1,[http/1.1]}{0.0.0.0:4040}
2018-06-14 13:58:48 INFO  Utils:54 - Successfully started service 'SparkUI' on port 4040.
2018-06-14 13:58:48 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@4ac8768e{/jobs,null,AVAILABLE,@Spark}
2018-06-14 13:58:48 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@58a84a12{/jobs/json,null,AVAILABLE,@Spark}
2018-06-14 13:58:48 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@e700eba{/jobs/job,null,AVAILABLE,@Spark}
2018-06-14 13:58:48 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@7186b202{/jobs/job/json,null,AVAILABLE,@Spark}
2018-06-14 13:58:48 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@6b649efa{/stages,null,AVAILABLE,@Spark}
2018-06-14 13:58:48 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@65ef48f2{/stages/json,null,AVAILABLE,@Spark}
2018-06-14 13:58:48 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@36068727{/stages/stage,null,AVAILABLE,@Spark}
2018-06-14 13:58:48 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@22bf9122{/stages/stage/json,null,AVAILABLE,@Spark}
2018-06-14 13:58:48 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@208205ed{/stages/pool,null,AVAILABLE,@Spark}
2018-06-14 13:58:48 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@73afe2b7{/stages/pool/json,null,AVAILABLE,@Spark}
2018-06-14 13:58:48 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@9687f55{/storage,null,AVAILABLE,@Spark}
2018-06-14 13:58:48 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@5700c9db{/storage/json,null,AVAILABLE,@Spark}
2018-06-14 13:58:48 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@671d03bb{/storage/rdd,null,AVAILABLE,@Spark}
2018-06-14 13:58:48 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@6babffb5{/storage/rdd/json,null,AVAILABLE,@Spark}
2018-06-14 13:58:48 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@2173a742{/environment,null,AVAILABLE,@Spark}
2018-06-14 13:58:48 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@706ceca6{/environment/json,null,AVAILABLE,@Spark}
2018-06-14 13:58:48 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@7f6329cb{/executors,null,AVAILABLE,@Spark}
2018-06-14 13:58:48 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@4b8137c5{/executors/json,null,AVAILABLE,@Spark}
2018-06-14 13:58:48 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@77f4c040{/executors/threadDump,null,AVAILABLE,@Spark}
2018-06-14 13:58:48 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@606a1bc4{/executors/threadDump/json,null,AVAILABLE,@Spark}
2018-06-14 13:58:48 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@6a15b73{/static,null,AVAILABLE,@Spark}
2018-06-14 13:58:48 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@773014d3{/,null,AVAILABLE,@Spark}
2018-06-14 13:58:48 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@7fedb795{/api,null,AVAILABLE,@Spark}
2018-06-14 13:58:48 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@34451ed8{/jobs/job/kill,null,AVAILABLE,@Spark}
2018-06-14 13:58:48 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@c1050f2{/stages/stage/kill,null,AVAILABLE,@Spark}
2018-06-14 13:58:48 INFO  SparkUI:54 - Bound SparkUI to 0.0.0.0, and started at http://master:4040
2018-06-14 13:58:48 INFO  StandaloneAppClient$ClientEndpoint:54 - Connecting to master spark://192.168.189.1:7077...
2018-06-14 13:58:49 INFO  TransportClientFactory:267 - Successfully created connection to /192.168.189.1:7077 after 183 ms (0 ms spent in bootstraps)
2018-06-14 13:58:49 INFO  StandaloneSchedulerBackend:54 - Connected to Spark cluster with app ID app-20180614135849-0001
2018-06-14 13:58:49 INFO  StandaloneAppClient$ClientEndpoint:54 - Executor added: app-20180614135849-0001/0 on worker-20180614135333-worker2-34319 (worker2:34319) with 1 core(s)
2018-06-14 13:58:49 INFO  StandaloneSchedulerBackend:54 - Granted executor ID app-20180614135849-0001/0 on hostPort worker2:34319 with 1 core(s), 512.0 MB RAM
2018-06-14 13:58:49 INFO  StandaloneAppClient$ClientEndpoint:54 - Executor added: app-20180614135849-0001/1 on worker-20180614135333-worker3-40134 (worker3:40134) with 1 core(s)
2018-06-14 13:58:49 INFO  StandaloneSchedulerBackend:54 - Granted executor ID app-20180614135849-0001/1 on hostPort worker3:40134 with 1 core(s), 512.0 MB RAM
2018-06-14 13:58:49 INFO  StandaloneAppClient$ClientEndpoint:54 - Executor added: app-20180614135849-0001/2 on worker-20180614135333-worker1-35690 (worker1:35690) with 1 core(s)
2018-06-14 13:58:49 INFO  StandaloneSchedulerBackend:54 - Granted executor ID app-20180614135849-0001/2 on hostPort worker1:35690 with 1 core(s), 512.0 MB RAM
2018-06-14 13:58:49 INFO  Utils:54 - Successfully started service 'org.apache.spark.network.netty.NettyBlockTransferService' on port 49678.
2018-06-14 13:58:49 INFO  NettyBlockTransferService:54 - Server created on master:49678
2018-06-14 13:58:49 INFO  BlockManager:54 - Using org.apache.spark.storage.RandomBlockReplicationPolicy for block replication policy
2018-06-14 13:58:49 INFO  StandaloneAppClient$ClientEndpoint:54 - Executor updated: app-20180614135849-0001/2 is now RUNNING
2018-06-14 13:58:49 INFO  StandaloneAppClient$ClientEndpoint:54 - Executor updated: app-20180614135849-0001/1 is now RUNNING
2018-06-14 13:58:49 INFO  StandaloneAppClient$ClientEndpoint:54 - Executor updated: app-20180614135849-0001/0 is now RUNNING
2018-06-14 13:58:49 INFO  BlockManagerMaster:54 - Registering BlockManager BlockManagerId(driver, master, 49678, None)
2018-06-14 13:58:49 INFO  BlockManagerMasterEndpoint:54 - Registering block manager master:49678 with 413.9 MB RAM, BlockManagerId(driver, master, 49678, None)
2018-06-14 13:58:49 INFO  BlockManagerMaster:54 - Registered BlockManager BlockManagerId(driver, master, 49678, None)
2018-06-14 13:58:49 INFO  BlockManager:54 - Initialized BlockManager: BlockManagerId(driver, master, 49678, None)
2018-06-14 13:58:51 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@30bbcf91{/metrics/json,null,AVAILABLE,@Spark}
2018-06-14 13:58:51 INFO  StandaloneSchedulerBackend:54 - SchedulerBackend is ready for scheduling beginning after reached minRegisteredResourcesRatio: 0.0
2018-06-14 13:58:52 INFO  SharedState:54 - loading hive config file: file:/usr/local/spark-2.3.0-bin-hadoop2.6/conf/hive-site.xml
2018-06-14 13:58:52 INFO  SharedState:54 - spark.sql.warehouse.dir is not set, but hive.metastore.warehouse.dir is set. Setting spark.sql.warehouse.dir to the value of hive.metastore.warehouse.dir ('/user/hive/warehouse').
2018-06-14 13:58:52 INFO  SharedState:54 - Warehouse path is '/user/hive/warehouse'.
2018-06-14 13:58:52 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@256a0d95{/SQL,null,AVAILABLE,@Spark}
2018-06-14 13:58:52 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@2f3928ac{/SQL/json,null,AVAILABLE,@Spark}
2018-06-14 13:58:52 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@22ead351{/SQL/execution,null,AVAILABLE,@Spark}
2018-06-14 13:58:52 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@68af87ad{/SQL/execution/json,null,AVAILABLE,@Spark}
2018-06-14 13:58:52 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@11ede87f{/static/sql,null,AVAILABLE,@Spark}
2018-06-14 13:58:52 INFO  HiveUtils:54 - Initializing HiveMetastoreConnection version 1.2.1 using Spark classes.
2018-06-14 13:58:52 INFO  HiveClientImpl:54 - Warehouse location for Hive client (version 1.2.2) is /user/hive/warehouse
2018-06-14 13:58:52 INFO  HiveMetaStore:746 - 0: get_database: default
2018-06-14 13:58:52 INFO  audit:371 - ugi=root  ip=unknown-ip-addr      cmd=get_database: default
2018-06-14 13:58:55 INFO  CoarseGrainedSchedulerBackend$DriverEndpoint:54 - Registered executor NettyRpcEndpointRef(spark-client://Executor) (192.168.189.4:48078) with ID 1
2018-06-14 13:58:55 INFO  BlockManagerMasterEndpoint:54 - Registering block manager worker3:52725 with 117.0 MB RAM, BlockManagerId(1, worker3, 52725, None)
2018-06-14 13:59:00 INFO  StateStoreCoordinatorRef:54 - Registered StateStoreCoordinator endpoint
spark-sql&gt; 2018-06-14 13:59:11 INFO  CoarseGrainedSchedulerBackend$DriverEndpoint:54 - Registered executor NettyRpcEndpointRef(spark-client://Executor) (192.168.189.2:47331) with ID 2
2018-06-14 13:59:11 INFO  BlockManagerMasterEndpoint:54 - Registering block manager worker1:45606 with 117.0 MB RAM, BlockManagerId(2, worker1, 45606, None)
2018-06-14 13:59:15 INFO  CoarseGrainedSchedulerBackend$DriverEndpoint:54 - Registered executor NettyRpcEndpointRef(spark-client://Executor) (192.168.189.3:46036) with ID 0
2018-06-14 13:59:15 INFO  BlockManagerMasterEndpoint:54 - Registering block manager worker2:55353 with 117.0 MB RAM, BlockManagerId(0, worker2, 55353, None)</code></pre><br><p>继续报错：Caused by: java.lang.ClassNotFoundException: org.apache.hadoop.hbase.util.Bytes</p><pre><code class="language-html">         &gt; 
         &gt;  select * from hbase_hive_wtb_ow_operation;
2018-06-14 14:00:25 INFO  HiveMetaStore:746 - 0: get_table : db=default tbl=hbase_hive_wtb_ow_operation
2018-06-14 14:00:25 INFO  audit:371 - ugi=root  ip=unknown-ip-addr      cmd=get_table : db=default tbl=hbase_hive_wtb_ow_operation
2018-06-14 14:00:27 ERROR SparkSQLDriver:91 - Failed in [ select * from hbase_hive_wtb_ow_operation]
java.lang.NoClassDefFoundError: org/apache/hadoop/hbase/util/Bytes
        at org.apache.hadoop.hive.hbase.HBaseSerDe.parseColumnsMapping(HBaseSerDe.java:184)
        at org.apache.hadoop.hive.hbase.HBaseSerDeParameters.&lt;init&gt;(HBaseSerDeParameters.java:73)
        at org.apache.hadoop.hive.hbase.HBaseSerDe.initialize(HBaseSerDe.java:117)
        at org.apache.hadoop.hive.serde2.AbstractSerDe.initialize(AbstractSerDe.java:53)
        at org.apache.hadoop.hive.serde2.SerDeUtils.initializeSerDe(SerDeUtils.java:521)
        at org.apache.hadoop.hive.metastore.MetaStoreUtils.getDeserializer(MetaStoreUtils.java:391)
        at org.apache.hadoop.hive.ql.metadata.Table.getDeserializerFromMetaStore(Table.java:276)
        at org.apache.hadoop.hive.ql.metadata.Table.getDeserializer(Table.java:258)
        at org.apache.hadoop.hive.ql.metadata.Table.getCols(Table.java:605)
        at org.apache.spark.sql.hive.client.HiveClientImpl$$anonfun$getTableOption$1$$anonfun$apply$7.apply(HiveClientImpl.scala:358)
        at org.apache.spark.sql.hive.client.HiveClientImpl$$anonfun$getTableOption$1$$anonfun$apply$7.apply(HiveClientImpl.scala:355)
        at scala.Option.map(Option.scala:146)
        at org.apache.spark.sql.hive.client.HiveClientImpl$$anonfun$getTableOption$1.apply(HiveClientImpl.scala:355)
        at org.apache.spark.sql.hive.client.HiveClientImpl$$anonfun$getTableOption$1.apply(HiveClientImpl.scala:353)
        at org.apache.spark.sql.hive.client.HiveClientImpl$$anonfun$withHiveState$1.apply(HiveClientImpl.scala:272)
        at org.apache.spark.sql.hive.client.HiveClientImpl.liftedTree1$1(HiveClientImpl.scala:210)
        at org.apache.spark.sql.hive.client.HiveClientImpl.retryLocked(HiveClientImpl.scala:209)
        at org.apache.spark.sql.hive.client.HiveClientImpl.withHiveState(HiveClientImpl.scala:255)
        at org.apache.spark.sql.hive.client.HiveClientImpl.getTableOption(HiveClientImpl.scala:353)
        at org.apache.spark.sql.hive.client.HiveClient$class.getTable(HiveClient.scala:81)
        at org.apache.spark.sql.hive.client.HiveClientImpl.getTable(HiveClientImpl.scala:83)
        at org.apache.spark.sql.hive.HiveExternalCatalog$$anonfun$getRawTable$1.apply(HiveExternalCatalog.scala:118)
        at org.apache.spark.sql.hive.HiveExternalCatalog$$anonfun$getRawTable$1.apply(HiveExternalCatalog.scala:118)
        at org.apache.spark.sql.hive.HiveExternalCatalog.withClient(HiveExternalCatalog.scala:97)
        at org.apache.spark.sql.hive.HiveExternalCatalog.getRawTable(HiveExternalCatalog.scala:117)
        at org.apache.spark.sql.hive.HiveExternalCatalog$$anonfun$getTable$1.apply(HiveExternalCatalog.scala:684)
        at org.apache.spark.sql.hive.HiveExternalCatalog$$anonfun$getTable$1.apply(HiveExternalCatalog.scala:684)
        at org.apache.spark.sql.hive.HiveExternalCatalog.withClient(HiveExternalCatalog.scala:97)
        at org.apache.spark.sql.hive.HiveExternalCatalog.getTable(HiveExternalCatalog.scala:683)
        at org.apache.spark.sql.catalyst.catalog.SessionCatalog.lookupRelation(SessionCatalog.scala:669)
        at org.apache.spark.sql.catalyst.analysis.Analyzer$ResolveRelations$.org$apache$spark$sql$catalyst$analysis$Analyzer$ResolveRelations$$lookupTableFromCatalog(Analyzer.scala:660)
        at org.apache.spark.sql.catalyst.analysis.Analyzer$ResolveRelations$.resolveRelation(Analyzer.scala:615)
        at org.apache.spark.sql.catalyst.analysis.Analyzer$ResolveRelations$$anonfun$apply$8.applyOrElse(Analyzer.scala:645)
        at org.apache.spark.sql.catalyst.analysis.Analyzer$ResolveRelations$$anonfun$apply$8.applyOrElse(Analyzer.scala:638)
        at org.apache.spark.sql.catalyst.trees.TreeNode$$anonfun$transformUp$1.apply(TreeNode.scala:289)
        at org.apache.spark.sql.catalyst.trees.TreeNode$$anonfun$transformUp$1.apply(TreeNode.scala:289)
        at org.apache.spark.sql.catalyst.trees.CurrentOrigin$.withOrigin(TreeNode.scala:70)
        at org.apache.spark.sql.catalyst.trees.TreeNode.transformUp(TreeNode.scala:288)
        at org.apache.spark.sql.catalyst.trees.TreeNode$$anonfun$3.apply(TreeNode.scala:286)
        at org.apache.spark.sql.catalyst.trees.TreeNode$$anonfun$3.apply(TreeNode.scala:286)
        at org.apache.spark.sql.catalyst.trees.TreeNode$$anonfun$4.apply(TreeNode.scala:306)
        at org.apache.spark.sql.catalyst.trees.TreeNode.mapProductIterator(TreeNode.scala:187)
        at org.apache.spark.sql.catalyst.trees.TreeNode.mapChildren(TreeNode.scala:304)
        at org.apache.spark.sql.catalyst.trees.TreeNode.transformUp(TreeNode.scala:286)
        at org.apache.spark.sql.catalyst.analysis.Analyzer$ResolveRelations$.apply(Analyzer.scala:638)
        at org.apache.spark.sql.catalyst.analysis.Analyzer$ResolveRelations$.apply(Analyzer.scala:584)
        at org.apache.spark.sql.catalyst.rules.RuleExecutor$$anonfun$execute$1$$anonfun$apply$1.apply(RuleExecutor.scala:87)
        at org.apache.spark.sql.catalyst.rules.RuleExecutor$$anonfun$execute$1$$anonfun$apply$1.apply(RuleExecutor.scala:84)
        at scala.collection.LinearSeqOptimized$class.foldLeft(LinearSeqOptimized.scala:124)
        at scala.collection.immutable.List.foldLeft(List.scala:84)
        at org.apache.spark.sql.catalyst.rules.RuleExecutor$$anonfun$execute$1.apply(RuleExecutor.scala:84)
        at org.apache.spark.sql.catalyst.rules.RuleExecutor$$anonfun$execute$1.apply(RuleExecutor.scala:76)
        at scala.collection.immutable.List.foreach(List.scala:381)
        at org.apache.spark.sql.catalyst.rules.RuleExecutor.execute(RuleExecutor.scala:76)
        at org.apache.spark.sql.catalyst.analysis.Analyzer.org$apache$spark$sql$catalyst$analysis$Analyzer$$executeSameContext(Analyzer.scala:123)
        at org.apache.spark.sql.catalyst.analysis.Analyzer.execute(Analyzer.scala:117)
        at org.apache.spark.sql.catalyst.analysis.Analyzer.executeAndCheck(Analyzer.scala:102)
        at org.apache.spark.sql.execution.QueryExecution.analyzed$lzycompute(QueryExecution.scala:57)
        at org.apache.spark.sql.execution.QueryExecution.analyzed(QueryExecution.scala:55)
        at org.apache.spark.sql.execution.QueryExecution.assertAnalyzed(QueryExecution.scala:47)
        at org.apache.spark.sql.Dataset$.ofRows(Dataset.scala:74)
        at org.apache.spark.sql.SparkSession.sql(SparkSession.scala:638)
        at org.apache.spark.sql.SQLContext.sql(SQLContext.scala:694)
        at org.apache.spark.sql.hive.thriftserver.SparkSQLDriver.run(SparkSQLDriver.scala:62)
        at org.apache.spark.sql.hive.thriftserver.SparkSQLCLIDriver.processCmd(SparkSQLCLIDriver.scala:355)
        at org.apache.hadoop.hive.cli.CliDriver.processLine(CliDriver.java:376)
        at org.apache.spark.sql.hive.thriftserver.SparkSQLCLIDriver$.main(SparkSQLCLIDriver.scala:263)
        at org.apache.spark.sql.hive.thriftserver.SparkSQLCLIDriver.main(SparkSQLCLIDriver.scala)
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
        at java.lang.reflect.Method.invoke(Method.java:497)
        at org.apache.spark.deploy.JavaMainApplication.start(SparkApplication.scala:52)
        at org.apache.spark.deploy.SparkSubmit$.org$apache$spark$deploy$SparkSubmit$$runMain(SparkSubmit.scala:879)
        at org.apache.spark.deploy.SparkSubmit$.doRunMain$1(SparkSubmit.scala:197)
        at org.apache.spark.deploy.SparkSubmit$.submit(SparkSubmit.scala:227)
        at org.apache.spark.deploy.SparkSubmit$.main(SparkSubmit.scala:136)
        at org.apache.spark.deploy.SparkSubmit.main(SparkSubmit.scala)
Caused by: java.lang.ClassNotFoundException: org.apache.hadoop.hbase.util.Bytes
        at java.net.URLClassLoader.findClass(URLClassLoader.java:381)
        at java.lang.ClassLoader.loadClass(ClassLoader.java:424)
        at sun.misc.Launcher$AppClassLoader.loadClass(Launcher.java:331)
        at java.lang.ClassLoader.loadClass(ClassLoader.java:357)
        ... 78 more
java.lang.NoClassDefFoundError: org/apache/hadoop/hbase/util/Bytes
        at org.apache.hadoop.hive.hbase.HBaseSerDe.parseColumnsMapping(HBaseSerDe.java:184)
        at org.apache.hadoop.hive.hbase.HBaseSerDeParameters.&lt;init&gt;(HBaseSerDeParameters.java:73)
        at org.apache.hadoop.hive.hbase.HBaseSerDe.initialize(HBaseSerDe.java:117)
        at org.apache.hadoop.hive.serde2.AbstractSerDe.initialize(AbstractSerDe.java:53)
        at org.apache.hadoop.hive.serde2.SerDeUtils.initializeSerDe(SerDeUtils.java:521)
        at org.apache.hadoop.hive.metastore.MetaStoreUtils.getDeserializer(MetaStoreUtils.java:391)
        at org.apache.hadoop.hive.ql.metadata.Table.getDeserializerFromMetaStore(Table.java:276)
        at org.apache.hadoop.hive.ql.metadata.Table.getDeserializer(Table.java:258)
        at org.apache.hadoop.hive.ql.metadata.Table.getCols(Table.java:605)
        at org.apache.spark.sql.hive.client.HiveClientImpl$$anonfun$getTableOption$1$$anonfun$apply$7.apply(HiveClientImpl.scala:358)
        at org.apache.spark.sql.hive.client.HiveClientImpl$$anonfun$getTableOption$1$$anonfun$apply$7.apply(HiveClientImpl.scala:355)
        at scala.Option.map(Option.scala:146)
        at org.apache.spark.sql.hive.client.HiveClientImpl$$anonfun$getTableOption$1.apply(HiveClientImpl.scala:355)
        at org.apache.spark.sql.hive.client.HiveClientImpl$$anonfun$getTableOption$1.apply(HiveClientImpl.scala:353)
        at org.apache.spark.sql.hive.client.HiveClientImpl$$anonfun$withHiveState$1.apply(HiveClientImpl.scala:272)
        at org.apache.spark.sql.hive.client.HiveClientImpl.liftedTree1$1(HiveClientImpl.scala:210)
        at org.apache.spark.sql.hive.client.HiveClientImpl.retryLocked(HiveClientImpl.scala:209)
        at org.apache.spark.sql.hive.client.HiveClientImpl.withHiveState(HiveClientImpl.scala:255)
        at org.apache.spark.sql.hive.client.HiveClientImpl.getTableOption(HiveClientImpl.scala:353)
        at org.apache.spark.sql.hive.client.HiveClient$class.getTable(HiveClient.scala:81)
        at org.apache.spark.sql.hive.client.HiveClientImpl.getTable(HiveClientImpl.scala:83)
        at org.apache.spark.sql.hive.HiveExternalCatalog$$anonfun$getRawTable$1.apply(HiveExternalCatalog.scala:118)
        at org.apache.spark.sql.hive.HiveExternalCatalog$$anonfun$getRawTable$1.apply(HiveExternalCatalog.scala:118)
        at org.apache.spark.sql.hive.HiveExternalCatalog.withClient(HiveExternalCatalog.scala:97)
        at org.apache.spark.sql.hive.HiveExternalCatalog.getRawTable(HiveExternalCatalog.scala:117)
        at org.apache.spark.sql.hive.HiveExternalCatalog$$anonfun$getTable$1.apply(HiveExternalCatalog.scala:684)
        at org.apache.spark.sql.hive.HiveExternalCatalog$$anonfun$getTable$1.apply(HiveExternalCatalog.scala:684)
        at org.apache.spark.sql.hive.HiveExternalCatalog.withClient(HiveExternalCatalog.scala:97)
        at org.apache.spark.sql.hive.HiveExternalCatalog.getTable(HiveExternalCatalog.scala:683)
        at org.apache.spark.sql.catalyst.catalog.SessionCatalog.lookupRelation(SessionCatalog.scala:669)
        at org.apache.spark.sql.catalyst.analysis.Analyzer$ResolveRelations$.org$apache$spark$sql$catalyst$analysis$Analyzer$ResolveRelations$$lookupTableFromCatalog(Analyzer.scala:660)
        at org.apache.spark.sql.catalyst.analysis.Analyzer$ResolveRelations$.resolveRelation(Analyzer.scala:615)
        at org.apache.spark.sql.catalyst.analysis.Analyzer$ResolveRelations$$anonfun$apply$8.applyOrElse(Analyzer.scala:645)
        at org.apache.spark.sql.catalyst.analysis.Analyzer$ResolveRelations$$anonfun$apply$8.applyOrElse(Analyzer.scala:638)
        at org.apache.spark.sql.catalyst.trees.TreeNode$$anonfun$transformUp$1.apply(TreeNode.scala:289)
        at org.apache.spark.sql.catalyst.trees.TreeNode$$anonfun$transformUp$1.apply(TreeNode.scala:289)
        at org.apache.spark.sql.catalyst.trees.CurrentOrigin$.withOrigin(TreeNode.scala:70)
        at org.apache.spark.sql.catalyst.trees.TreeNode.transformUp(TreeNode.scala:288)
        at org.apache.spark.sql.catalyst.trees.TreeNode$$anonfun$3.apply(TreeNode.scala:286)
        at org.apache.spark.sql.catalyst.trees.TreeNode$$anonfun$3.apply(TreeNode.scala:286)
        at org.apache.spark.sql.catalyst.trees.TreeNode$$anonfun$4.apply(TreeNode.scala:306)
        at org.apache.spark.sql.catalyst.trees.TreeNode.mapProductIterator(TreeNode.scala:187)
        at org.apache.spark.sql.catalyst.trees.TreeNode.mapChildren(TreeNode.scala:304)
        at org.apache.spark.sql.catalyst.trees.TreeNode.transformUp(TreeNode.scala:286)
        at org.apache.spark.sql.catalyst.analysis.Analyzer$ResolveRelations$.apply(Analyzer.scala:638)
        at org.apache.spark.sql.catalyst.analysis.Analyzer$ResolveRelations$.apply(Analyzer.scala:584)
        at org.apache.spark.sql.catalyst.rules.RuleExecutor$$anonfun$execute$1$$anonfun$apply$1.apply(RuleExecutor.scala:87)
        at org.apache.spark.sql.catalyst.rules.RuleExecutor$$anonfun$execute$1$$anonfun$apply$1.apply(RuleExecutor.scala:84)
        at scala.collection.LinearSeqOptimized$class.foldLeft(LinearSeqOptimized.scala:124)
        at scala.collection.immutable.List.foldLeft(List.scala:84)
        at org.apache.spark.sql.catalyst.rules.RuleExecutor$$anonfun$execute$1.apply(RuleExecutor.scala:84)
        at org.apache.spark.sql.catalyst.rules.RuleExecutor$$anonfun$execute$1.apply(RuleExecutor.scala:76)
        at scala.collection.immutable.List.foreach(List.scala:381)
        at org.apache.spark.sql.catalyst.rules.RuleExecutor.execute(RuleExecutor.scala:76)
        at org.apache.spark.sql.catalyst.analysis.Analyzer.org$apache$spark$sql$catalyst$analysis$Analyzer$$executeSameContext(Analyzer.scala:123)
        at org.apache.spark.sql.catalyst.analysis.Analyzer.execute(Analyzer.scala:117)
        at org.apache.spark.sql.catalyst.analysis.Analyzer.executeAndCheck(Analyzer.scala:102)
        at org.apache.spark.sql.execution.QueryExecution.analyzed$lzycompute(QueryExecution.scala:57)
        at org.apache.spark.sql.execution.QueryExecution.analyzed(QueryExecution.scala:55)
        at org.apache.spark.sql.execution.QueryExecution.assertAnalyzed(QueryExecution.scala:47)
        at org.apache.spark.sql.Dataset$.ofRows(Dataset.scala:74)
        at org.apache.spark.sql.SparkSession.sql(SparkSession.scala:638)
        at org.apache.spark.sql.SQLContext.sql(SQLContext.scala:694)
        at org.apache.spark.sql.hive.thriftserver.SparkSQLDriver.run(SparkSQLDriver.scala:62)
        at org.apache.spark.sql.hive.thriftserver.SparkSQLCLIDriver.processCmd(SparkSQLCLIDriver.scala:355)
        at org.apache.hadoop.hive.cli.CliDriver.processLine(CliDriver.java:376)
        at org.apache.spark.sql.hive.thriftserver.SparkSQLCLIDriver$.main(SparkSQLCLIDriver.scala:263)
        at org.apache.spark.sql.hive.thriftserver.SparkSQLCLIDriver.main(SparkSQLCLIDriver.scala)
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
        at java.lang.reflect.Method.invoke(Method.java:497)
        at org.apache.spark.deploy.JavaMainApplication.start(SparkApplication.scala:52)
        at org.apache.spark.deploy.SparkSubmit$.org$apache$spark$deploy$SparkSubmit$$runMain(SparkSubmit.scala:879)
        at org.apache.spark.deploy.SparkSubmit$.doRunMain$1(SparkSubmit.scala:197)
        at org.apache.spark.deploy.SparkSubmit$.submit(SparkSubmit.scala:227)
        at org.apache.spark.deploy.SparkSubmit$.main(SparkSubmit.scala:136)
        at org.apache.spark.deploy.SparkSubmit.main(SparkSubmit.scala)
Caused by: java.lang.ClassNotFoundException: org.apache.hadoop.hbase.util.Bytes
        at java.net.URLClassLoader.findClass(URLClassLoader.java:381)
        at java.lang.ClassLoader.loadClass(ClassLoader.java:424)
        at sun.misc.Launcher$AppClassLoader.loadClass(Launcher.java:331)
        at java.lang.ClassLoader.loadClass(ClassLoader.java:357)
        ... 78 more</code></pre><br><p>将相关的hbase的jar包都加上，然后在spark集群中运行：</p><pre><code class="language-html">root@master:/usr/local/hbase-1.2.0/lib# pwd
/usr/local/hbase-1.2.0/lib 
root@master:/usr/local/hbase-1.2.0/lib# ls | grep hbase-client
hbase-client-1.2.0.jar
root@master:/usr/local/hbase-1.2.0/lib# ls | grep hbase-common
hbase-common-1.2.0.jar
hbase-common-1.2.0-tests.jar
root@master:/usr/local/hbase-1.2.0/lib# ls | grep hbase-protocol
hbase-protocol-1.2.0.jar
root@master:/usr/local/hbase-1.2.0/lib# ls | grep hbase-server
hbase-server-1.2.0.jar
hbase-server-1.2.0-tests.jar
root@master:/usr/local/hbase-1.2.0/lib# ls | grep htrace-core
htrace-core-3.1.0-incubating.jar</code></pre><pre><code class="language-html">root@master:~# spark-sql  --master spark://192.168.189.1:7077 --driver-class-path /usr/local/apache-hive-1.2.1/lib/mysql-connector-java-5.1.13-bin.jar:/usr/local/apache-hive-1.2.1/lib/hive-hbase-handler-1.2.1.jar:/usr/local/hbase-1.2.0/lib/hbase-client-1.2.0.jar:/usr/local/hbase-1.2.0/lib/hbase-common-1.2.0.jar:/usr/local/hbase-1.2.0/lib/hbase-protocol-1.2.0.jar:/usr/local/hbase-1.2.0/lib/hbase-server-1.2.0.jar:/usr/local/hbase-1.2.0/lib/htrace-core-3.1.0-incubating.jar  --executor-memory 512m  --total-executor-cores 4

</code></pre><pre><code class="language-html">2018-06-14 14:06:09 WARN  NativeCodeLoader:62 - Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
2018-06-14 14:06:13 WARN  HiveConf:2753 - HiveConf of name hive.server2.http.endpoint does not exist
2018-06-14 14:06:14 INFO  HiveMetaStore:589 - 0: Opening raw store with implemenation class:org.apache.hadoop.hive.metastore.ObjectStore
2018-06-14 14:06:14 INFO  ObjectStore:289 - ObjectStore, initialize called
2018-06-14 14:06:14 INFO  Persistence:77 - Property hive.metastore.integral.jdo.pushdown unknown - will be ignored
2018-06-14 14:06:14 INFO  Persistence:77 - Property datanucleus.cache.level2 unknown - will be ignored
2018-06-14 14:06:16 WARN  HiveConf:2753 - HiveConf of name hive.server2.http.endpoint does not exist
2018-06-14 14:06:16 INFO  ObjectStore:370 - Setting MetaStore object pin classes with hive.metastore.cache.pinobjtypes="Table,StorageDescriptor,SerDeInfo,Partition,Database,Type,FieldSchema,Order"
2018-06-14 14:06:18 INFO  Datastore:77 - The class "org.apache.hadoop.hive.metastore.model.MFieldSchema" is tagged as "embedded-only" so does not have its own datastore table.
2018-06-14 14:06:18 INFO  Datastore:77 - The class "org.apache.hadoop.hive.metastore.model.MOrder" is tagged as "embedded-only" so does not have its own datastore table.
2018-06-14 14:06:19 INFO  Datastore:77 - The class "org.apache.hadoop.hive.metastore.model.MFieldSchema" is tagged as "embedded-only" so does not have its own datastore table.
2018-06-14 14:06:19 INFO  Datastore:77 - The class "org.apache.hadoop.hive.metastore.model.MOrder" is tagged as "embedded-only" so does not have its own datastore table.
2018-06-14 14:06:19 INFO  Query:77 - Reading in results for query "org.datanucleus.store.rdbms.query.SQLQuery@0" since the connection used is closing
2018-06-14 14:06:19 INFO  MetaStoreDirectSql:139 - Using direct SQL, underlying DB is MYSQL
2018-06-14 14:06:19 INFO  ObjectStore:272 - Initialized ObjectStore
2018-06-14 14:06:20 INFO  HiveMetaStore:663 - Added admin role in metastore
2018-06-14 14:06:20 INFO  HiveMetaStore:672 - Added public role in metastore
2018-06-14 14:06:20 INFO  HiveMetaStore:712 - No user is added in admin role, since config is empty
2018-06-14 14:06:20 INFO  HiveMetaStore:746 - 0: get_all_databases
2018-06-14 14:06:20 INFO  audit:371 - ugi=root  ip=unknown-ip-addr      cmd=get_all_databases
2018-06-14 14:06:20 INFO  HiveMetaStore:746 - 0: get_functions: db=default pat=*
2018-06-14 14:06:20 INFO  audit:371 - ugi=root  ip=unknown-ip-addr      cmd=get_functions: db=default pat=*
2018-06-14 14:06:20 INFO  Datastore:77 - The class "org.apache.hadoop.hive.metastore.model.MResourceUri" is tagged as "embedded-only" so does not have its own datastore table.
2018-06-14 14:06:20 INFO  HiveMetaStore:746 - 0: get_functions: db=hive pat=*
2018-06-14 14:06:20 INFO  audit:371 - ugi=root  ip=unknown-ip-addr      cmd=get_functions: db=hive pat=*
2018-06-14 14:06:20 INFO  HiveMetaStore:746 - 0: get_functions: db=hivestudy pat=*
2018-06-14 14:06:20 INFO  audit:371 - ugi=root  ip=unknown-ip-addr      cmd=get_functions: db=hivestudy pat=*
2018-06-14 14:06:20 INFO  HiveMetaStore:746 - 0: get_functions: db=hrs pat=*
2018-06-14 14:06:20 INFO  audit:371 - ugi=root  ip=unknown-ip-addr      cmd=get_functions: db=hrs pat=*
2018-06-14 14:06:22 INFO  SessionState:641 - Created local directory: /tmp/c24fa8ce-c4af-46fd-a257-e5f52ab06aa3_resources
2018-06-14 14:06:22 INFO  SessionState:641 - Created HDFS directory: /tmp/hive/root/c24fa8ce-c4af-46fd-a257-e5f52ab06aa3
2018-06-14 14:06:22 INFO  SessionState:641 - Created local directory: /tmp/root/c24fa8ce-c4af-46fd-a257-e5f52ab06aa3
2018-06-14 14:06:22 INFO  SessionState:641 - Created HDFS directory: /tmp/hive/root/c24fa8ce-c4af-46fd-a257-e5f52ab06aa3/_tmp_space.db
2018-06-14 14:06:23 INFO  SparkContext:54 - Running Spark version 2.3.0
2018-06-14 14:06:23 INFO  SparkContext:54 - Submitted application: SparkSQL::master
2018-06-14 14:06:23 INFO  SecurityManager:54 - Changing view acls to: root
2018-06-14 14:06:23 INFO  SecurityManager:54 - Changing modify acls to: root
2018-06-14 14:06:23 INFO  SecurityManager:54 - Changing view acls groups to: 
2018-06-14 14:06:23 INFO  SecurityManager:54 - Changing modify acls groups to: 
2018-06-14 14:06:23 INFO  SecurityManager:54 - SecurityManager: authentication disabled; ui acls disabled; users  with view permissions: Set(root); groups with view permissions: Set(); users  with modify permissions: Set(root); groups with modify permissions: Set()
2018-06-14 14:06:24 INFO  Utils:54 - Successfully started service 'sparkDriver' on port 49479.
2018-06-14 14:06:24 INFO  SparkEnv:54 - Registering MapOutputTracker
2018-06-14 14:06:24 INFO  SparkEnv:54 - Registering BlockManagerMaster
2018-06-14 14:06:24 INFO  BlockManagerMasterEndpoint:54 - Using org.apache.spark.storage.DefaultTopologyMapper for getting topology information
2018-06-14 14:06:24 INFO  BlockManagerMasterEndpoint:54 - BlockManagerMasterEndpoint up
2018-06-14 14:06:25 INFO  DiskBlockManager:54 - Created local directory at /tmp/blockmgr-65298030-2240-49f0-b134-b18836a6af8b
2018-06-14 14:06:25 INFO  MemoryStore:54 - MemoryStore started with capacity 413.9 MB
2018-06-14 14:06:25 INFO  SparkEnv:54 - Registering OutputCommitCoordinator
2018-06-14 14:06:25 INFO  log:192 - Logging initialized @24245ms
2018-06-14 14:06:25 INFO  Server:346 - jetty-9.3.z-SNAPSHOT
2018-06-14 14:06:25 INFO  Server:414 - Started @24612ms
2018-06-14 14:06:25 INFO  AbstractConnector:278 - Started ServerConnector@6041203b{HTTP/1.1,[http/1.1]}{0.0.0.0:4040}
2018-06-14 14:06:25 INFO  Utils:54 - Successfully started service 'SparkUI' on port 4040.
2018-06-14 14:06:26 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@29a33620{/jobs,null,AVAILABLE,@Spark}
2018-06-14 14:06:26 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@208205ed{/jobs/json,null,AVAILABLE,@Spark}
2018-06-14 14:06:26 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@73afe2b7{/jobs/job,null,AVAILABLE,@Spark}
2018-06-14 14:06:26 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@5700c9db{/jobs/job/json,null,AVAILABLE,@Spark}
2018-06-14 14:06:26 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@671d03bb{/stages,null,AVAILABLE,@Spark}
2018-06-14 14:06:26 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@6babffb5{/stages/json,null,AVAILABLE,@Spark}
2018-06-14 14:06:26 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@2173a742{/stages/stage,null,AVAILABLE,@Spark}
2018-06-14 14:06:26 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@4b8137c5{/stages/stage/json,null,AVAILABLE,@Spark}
2018-06-14 14:06:26 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@77f4c040{/stages/pool,null,AVAILABLE,@Spark}
2018-06-14 14:06:26 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@606a1bc4{/stages/pool/json,null,AVAILABLE,@Spark}
2018-06-14 14:06:26 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@6a15b73{/storage,null,AVAILABLE,@Spark}
2018-06-14 14:06:26 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@44dc7b7d{/storage/json,null,AVAILABLE,@Spark}
2018-06-14 14:06:26 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@15fb4566{/storage/rdd,null,AVAILABLE,@Spark}
2018-06-14 14:06:26 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@25ffd826{/storage/rdd/json,null,AVAILABLE,@Spark}
2018-06-14 14:06:26 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@29896529{/environment,null,AVAILABLE,@Spark}
2018-06-14 14:06:26 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@e29f4f6{/environment/json,null,AVAILABLE,@Spark}
2018-06-14 14:06:26 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@41b64020{/executors,null,AVAILABLE,@Spark}
2018-06-14 14:06:26 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@1a538ed8{/executors/json,null,AVAILABLE,@Spark}
2018-06-14 14:06:26 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@78910096{/executors/threadDump,null,AVAILABLE,@Spark}
2018-06-14 14:06:26 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@39dec536{/executors/threadDump/json,null,AVAILABLE,@Spark}
2018-06-14 14:06:26 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@4a1a256d{/static,null,AVAILABLE,@Spark}
2018-06-14 14:06:26 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@61bb1e4d{/,null,AVAILABLE,@Spark}
2018-06-14 14:06:26 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@5c53f292{/api,null,AVAILABLE,@Spark}
2018-06-14 14:06:26 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@41f4039e{/jobs/job/kill,null,AVAILABLE,@Spark}
2018-06-14 14:06:26 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@5ff00507{/stages/stage/kill,null,AVAILABLE,@Spark}
2018-06-14 14:06:26 INFO  SparkUI:54 - Bound SparkUI to 0.0.0.0, and started at http://master:4040
2018-06-14 14:06:27 INFO  StandaloneAppClient$ClientEndpoint:54 - Connecting to master spark://192.168.189.1:7077...
2018-06-14 14:06:27 INFO  TransportClientFactory:267 - Successfully created connection to /192.168.189.1:7077 after 56 ms (0 ms spent in bootstraps)
2018-06-14 14:06:27 INFO  StandaloneSchedulerBackend:54 - Connected to Spark cluster with app ID app-20180614140627-0002
2018-06-14 14:06:27 INFO  StandaloneAppClient$ClientEndpoint:54 - Executor added: app-20180614140627-0002/0 on worker-20180614135333-worker2-34319 (worker2:34319) with 1 core(s)
2018-06-14 14:06:27 INFO  Utils:54 - Successfully started service 'org.apache.spark.network.netty.NettyBlockTransferService' on port 44524.
2018-06-14 14:06:27 INFO  StandaloneSchedulerBackend:54 - Granted executor ID app-20180614140627-0002/0 on hostPort worker2:34319 with 1 core(s), 512.0 MB RAM
2018-06-14 14:06:27 INFO  StandaloneAppClient$ClientEndpoint:54 - Executor added: app-20180614140627-0002/1 on worker-20180614135333-worker3-40134 (worker3:40134) with 1 core(s)
2018-06-14 14:06:27 INFO  StandaloneSchedulerBackend:54 - Granted executor ID app-20180614140627-0002/1 on hostPort worker3:40134 with 1 core(s), 512.0 MB RAM
2018-06-14 14:06:27 INFO  NettyBlockTransferService:54 - Server created on master:44524
2018-06-14 14:06:27 INFO  StandaloneAppClient$ClientEndpoint:54 - Executor added: app-20180614140627-0002/2 on worker-20180614135333-worker1-35690 (worker1:35690) with 1 core(s)
2018-06-14 14:06:27 INFO  BlockManager:54 - Using org.apache.spark.storage.RandomBlockReplicationPolicy for block replication policy
2018-06-14 14:06:27 INFO  StandaloneSchedulerBackend:54 - Granted executor ID app-20180614140627-0002/2 on hostPort worker1:35690 with 1 core(s), 512.0 MB RAM
2018-06-14 14:06:27 INFO  BlockManagerMaster:54 - Registering BlockManager BlockManagerId(driver, master, 44524, None)
2018-06-14 14:06:27 INFO  StandaloneAppClient$ClientEndpoint:54 - Executor updated: app-20180614140627-0002/0 is now RUNNING
2018-06-14 14:06:27 INFO  StandaloneAppClient$ClientEndpoint:54 - Executor updated: app-20180614140627-0002/1 is now RUNNING
2018-06-14 14:06:27 INFO  StandaloneAppClient$ClientEndpoint:54 - Executor updated: app-20180614140627-0002/2 is now RUNNING
2018-06-14 14:06:27 INFO  BlockManagerMasterEndpoint:54 - Registering block manager master:44524 with 413.9 MB RAM, BlockManagerId(driver, master, 44524, None)
2018-06-14 14:06:27 INFO  BlockManagerMaster:54 - Registered BlockManager BlockManagerId(driver, master, 44524, None)
2018-06-14 14:06:27 INFO  BlockManager:54 - Initialized BlockManager: BlockManagerId(driver, master, 44524, None)
2018-06-14 14:06:29 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@eed890d{/metrics/json,null,AVAILABLE,@Spark}
2018-06-14 14:06:29 INFO  StandaloneSchedulerBackend:54 - SchedulerBackend is ready for scheduling beginning after reached minRegisteredResourcesRatio: 0.0
2018-06-14 14:06:29 INFO  SharedState:54 - loading hive config file: file:/usr/local/spark-2.3.0-bin-hadoop2.6/conf/hive-site.xml
2018-06-14 14:06:29 INFO  SharedState:54 - spark.sql.warehouse.dir is not set, but hive.metastore.warehouse.dir is set. Setting spark.sql.warehouse.dir to the value of hive.metastore.warehouse.dir ('/user/hive/warehouse').
2018-06-14 14:06:29 INFO  SharedState:54 - Warehouse path is '/user/hive/warehouse'.
2018-06-14 14:06:29 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@601eb4af{/SQL,null,AVAILABLE,@Spark}
2018-06-14 14:06:30 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@11ede87f{/SQL/json,null,AVAILABLE,@Spark}
2018-06-14 14:06:30 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@2f9a10df{/SQL/execution,null,AVAILABLE,@Spark}
2018-06-14 14:06:30 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@773c2214{/SQL/execution/json,null,AVAILABLE,@Spark}
2018-06-14 14:06:30 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@626e0c86{/static/sql,null,AVAILABLE,@Spark}
2018-06-14 14:06:30 INFO  HiveUtils:54 - Initializing HiveMetastoreConnection version 1.2.1 using Spark classes.
2018-06-14 14:06:30 INFO  HiveClientImpl:54 - Warehouse location for Hive client (version 1.2.2) is /user/hive/warehouse
2018-06-14 14:06:30 INFO  HiveMetaStore:746 - 0: get_database: default
2018-06-14 14:06:30 INFO  audit:371 - ugi=root  ip=unknown-ip-addr      cmd=get_database: default
2018-06-14 14:06:32 INFO  CoarseGrainedSchedulerBackend$DriverEndpoint:54 - Registered executor NettyRpcEndpointRef(spark-client://Executor) (192.168.189.4:46864) with ID 1
2018-06-14 14:06:33 INFO  BlockManagerMasterEndpoint:54 - Registering block manager worker3:36552 with 117.0 MB RAM, BlockManagerId(1, worker3, 36552, None)
2018-06-14 14:06:35 INFO  CoarseGrainedSchedulerBackend$DriverEndpoint:54 - Registered executor NettyRpcEndpointRef(spark-client://Executor) (192.168.189.3:34013) with ID 0
2018-06-14 14:06:35 INFO  BlockManagerMasterEndpoint:54 - Registering block manager worker2:60815 with 117.0 MB RAM, BlockManagerId(0, worker2, 60815, None)
2018-06-14 14:06:36 INFO  StateStoreCoordinatorRef:54 - Registered StateStoreCoordinator endpoint
spark-sql&gt; 2018-06-14 14:06:40 INFO  CoarseGrainedSchedulerBackend$DriverEndpoint:54 - Registered executor NettyRpcEndpointRef(spark-client://Executor) (192.168.189.2:40473) with ID 2
2018-06-14 14:06:40 INFO  BlockManagerMasterEndpoint:54 - Registering block manager worker1:60898 with 117.0 MB RAM, BlockManagerId(2, worker1, 60898, None)
</code></pre><br><p>继续报错：</p><pre><code class="language-html">2018-06-14 14:07:45 INFO  ClientCnxn:975 - Opening socket connection to server localhost/127.0.0.1:2181. Will not attempt to authenticate using SASL (unknown error)
2018-06-14 14:07:45 INFO  ClientCnxn:852 - Socket connection established to localhost/127.0.0.1:2181, initiating session
2018-06-14 14:07:45 INFO  ClientCnxn:1235 - Session establishment complete on server localhost/127.0.0.1:2181, sessionid = 0x163fcd5cd600003, negotiated timeout = 40000
2018-06-14 14:07:46 INFO  RegionSizeCalculator:91 - Calculating region sizes for table "db_res:wtb_ow_operation".
2018-06-14 14:08:34 ERROR SparkSQLDriver:91 - Failed in [ select * from hbase_hive_wtb_ow_operation]
org.apache.hadoop.hbase.client.RetriesExhaustedException: Failed after attempts=36, exceptions:
Thu Jun 14 14:08:34 CST 2018, null, java.net.SocketTimeoutException: callTimeout=60000, callDuration=68735: row 'db_res:wtb_ow_operation,,00000000000000' on table 'hbase:meta' at region=hbase:meta,,1.1588230740, hostname=master,16020,1528955336185, seqNum=0

        at org.apache.hadoop.hbase.client.RpcRetryingCallerWithReadReplicas.throwEnrichedException(RpcRetryingCallerWithReadReplicas.java:276)
        at org.apache.hadoop.hbase.client.ScannerCallableWithReplicas.call(ScannerCallableWithReplicas.java:207)
        at org.apache.hadoop.hbase.client.ScannerCallableWithReplicas.call(ScannerCallableWithReplicas.java:60)
        at org.apache.hadoop.hbase.client.RpcRetryingCaller.callWithoutRetries(RpcRetryingCaller.java:200)
        at org.apache.hadoop.hbase.client.ClientScanner.call(ClientScanner.java:320)
        at org.apache.hadoop.hbase.client.ClientScanner.nextScanner(ClientScanner.java:295)
        at org.apache.hadoop.hbase.client.ClientScanner.initializeScannerInConstruction(ClientScanner.java:160)
        at org.apache.hadoop.hbase.client.ClientScanner.&lt;init&gt;(ClientScanner.java:155)
        at org.apache.hadoop.hbase.client.HTable.getScanner(HTable.java:802)
        at org.apache.hadoop.hbase.client.MetaScanner.metaScan(MetaScanner.java:193)
        at org.apache.hadoop.hbase.client.MetaScanner.metaScan(MetaScanner.java:89)
        at org.apache.hadoop.hbase.client.MetaScanner.allTableRegions(MetaScanner.java:324)
        at org.apache.hadoop.hbase.client.HRegionLocator.getAllRegionLocations(HRegionLocator.java:88)
        at org.apache.hadoop.hbase.util.RegionSizeCalculator.init(RegionSizeCalculator.java:94)
        at org.apache.hadoop.hbase.util.RegionSizeCalculator.&lt;init&gt;(RegionSizeCalculator.java:81)
        at org.apache.hadoop.hbase.mapreduce.TableInputFormatBase.getSplits(TableInputFormatBase.java:256)
        at org.apache.hadoop.hive.hbase.HiveHBaseTableInputFormat.getSplitsInternal(HiveHBaseTableInputFormat.java:499)
        at org.apache.hadoop.hive.hbase.HiveHBaseTableInputFormat.getSplits(HiveHBaseTableInputFormat.java:432)
        at org.apache.spark.rdd.HadoopRDD.getPartitions(HadoopRDD.scala:200)
        at org.apache.spark.rdd.RDD$$anonfun$partitions$2.apply(RDD.scala:253)
        at org.apache.spark.rdd.RDD$$anonfun$partitions$2.apply(RDD.scala:251)
        at scala.Option.getOrElse(Option.scala:121)
        at org.apache.spark.rdd.RDD.partitions(RDD.scala:251)
        at org.apache.spark.rdd.MapPartitionsRDD.getPartitions(MapPartitionsRDD.scala:35)
        at org.apache.spark.rdd.RDD$$anonfun$partitions$2.apply(RDD.scala:253)
        at org.apache.spark.rdd.RDD$$anonfun$partitions$2.apply(RDD.scala:251)
        at scala.Option.getOrElse(Option.scala:121)
        at org.apache.spark.rdd.RDD.partitions(RDD.scala:251)
        at org.apache.spark.rdd.MapPartitionsRDD.getPartitions(MapPartitionsRDD.scala:35)
        at org.apache.spark.rdd.RDD$$anonfun$partitions$2.apply(RDD.scala:253)
        at org.apache.spark.rdd.RDD$$anonfun$partitions$2.apply(RDD.scala:251)
        at scala.Option.getOrElse(Option.scala:121)
        at org.apache.spark.rdd.RDD.partitions(RDD.scala:251)
        at org.apache.spark.rdd.MapPartitionsRDD.getPartitions(MapPartitionsRDD.scala:35)
        at org.apache.spark.rdd.RDD$$anonfun$partitions$2.apply(RDD.scala:253)
        at org.apache.spark.rdd.RDD$$anonfun$partitions$2.apply(RDD.scala:251)
        at scala.Option.getOrElse(Option.scala:121)
        at org.apache.spark.rdd.RDD.partitions(RDD.scala:251)
        at org.apache.spark.rdd.MapPartitionsRDD.getPartitions(MapPartitionsRDD.scala:35)
        at org.apache.spark.rdd.RDD$$anonfun$partitions$2.apply(RDD.scala:253)
        at org.apache.spark.rdd.RDD$$anonfun$partitions$2.apply(RDD.scala:251)
        at scala.Option.getOrElse(Option.scala:121)
        at org.apache.spark.rdd.RDD.partitions(RDD.scala:251)
        at org.apache.spark.SparkContext.runJob(SparkContext.scala:2092)
        at org.apache.spark.rdd.RDD$$anonfun$collect$1.apply(RDD.scala:939)
        at org.apache.spark.rdd.RDDOperationScope$.withScope(RDDOperationScope.scala:151)
        at org.apache.spark.rdd.RDDOperationScope$.withScope(RDDOperationScope.scala:112)
        at org.apache.spark.rdd.RDD.withScope(RDD.scala:363)
        at org.apache.spark.rdd.RDD.collect(RDD.scala:938)
        at org.apache.spark.sql.execution.SparkPlan.executeCollect(SparkPlan.scala:297)
        at org.apache.spark.sql.execution.SparkPlan.executeCollectPublic(SparkPlan.scala:324)
        at org.apache.spark.sql.execution.QueryExecution.hiveResultString(QueryExecution.scala:122)
        at org.apache.spark.sql.hive.thriftserver.SparkSQLDriver$$anonfun$run$1.apply(SparkSQLDriver.scala:64)
        at org.apache.spark.sql.hive.thriftserver.SparkSQLDriver$$anonfun$run$1.apply(SparkSQLDriver.scala:64)
        at org.apache.spark.sql.execution.SQLExecution$.withNewExecutionId(SQLExecution.scala:77)
        at org.apache.spark.sql.hive.thriftserver.SparkSQLDriver.run(SparkSQLDriver.scala:63)
        at org.apache.spark.sql.hive.thriftserver.SparkSQLCLIDriver.processCmd(SparkSQLCLIDriver.scala:355)
        at org.apache.hadoop.hive.cli.CliDriver.processLine(CliDriver.java:376)
        at org.apache.spark.sql.hive.thriftserver.SparkSQLCLIDriver$.main(SparkSQLCLIDriver.scala:263)
        at org.apache.spark.sql.hive.thriftserver.SparkSQLCLIDriver.main(SparkSQLCLIDriver.scala)
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
        at java.lang.reflect.Method.invoke(Method.java:497)
        at org.apache.spark.deploy.JavaMainApplication.start(SparkApplication.scala:52)
        at org.apache.spark.deploy.SparkSubmit$.org$apache$spark$deploy$SparkSubmit$$runMain(SparkSubmit.scala:879)
        at org.apache.spark.deploy.SparkSubmit$.doRunMain$1(SparkSubmit.scala:197)
        at org.apache.spark.deploy.SparkSubmit$.submit(SparkSubmit.scala:227)
        at org.apache.spark.deploy.SparkSubmit$.main(SparkSubmit.scala:136)
        at org.apache.spark.deploy.SparkSubmit.main(SparkSubmit.scala)
Caused by: java.net.SocketTimeoutException: callTimeout=60000, callDuration=68735: row 'db_res:wtb_ow_operation,,00000000000000' on table 'hbase:meta' at region=hbase:meta,,1.1588230740, hostname=master,16020,1528955336185, seqNum=0
        at org.apache.hadoop.hbase.client.RpcRetryingCaller.callWithRetries(RpcRetryingCaller.java:159)
        at org.apache.hadoop.hbase.client.ResultBoundedCompletionService$QueueingFuture.run(ResultBoundedCompletionService.java:65)
        at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1142)
        at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:617)
        at java.lang.Thread.run(Thread.java:745)
Caused by: java.io.IOException: com.google.protobuf.ServiceException: java.lang.NoClassDefFoundError: com/yammer/metrics/core/Gauge
        at org.apache.hadoop.hbase.protobuf.ProtobufUtil.getRemoteException(ProtobufUtil.java:330)
        at org.apache.hadoop.hbase.client.ScannerCallable.openScanner(ScannerCallable.java:402)
        at org.apache.hadoop.hbase.client.ScannerCallable.call(ScannerCallable.java:203)
        at org.apache.hadoop.hbase.client.ScannerCallable.call(ScannerCallable.java:64)
        at org.apache.hadoop.hbase.client.RpcRetryingCaller.callWithoutRetries(RpcRetryingCaller.java:200)
        at org.apache.hadoop.hbase.client.ScannerCallableWithReplicas$RetryingRPC.call(ScannerCallableWithReplicas.java:360)
        at org.apache.hadoop.hbase.client.ScannerCallableWithReplicas$RetryingRPC.call(ScannerCallableWithReplicas.java:334)
        at org.apache.hadoop.hbase.client.RpcRetryingCaller.callWithRetries(RpcRetryingCaller.java:126)
        ... 4 more
Caused by: com.google.protobuf.ServiceException: java.lang.NoClassDefFoundError: com/yammer/metrics/core/Gauge
        at org.apache.hadoop.hbase.ipc.AbstractRpcClient.callBlockingMethod(AbstractRpcClient.java:239)
        at org.apache.hadoop.hbase.ipc.AbstractRpcClient$BlockingRpcChannelImplementation.callBlockingMethod(AbstractRpcClient.java:331)
        at org.apache.hadoop.hbase.protobuf.generated.ClientProtos$ClientService$BlockingStub.scan(ClientProtos.java:34094)
        at org.apache.hadoop.hbase.client.ScannerCallable.openScanner(ScannerCallable.java:394)
        ... 10 more
Caused by: java.lang.NoClassDefFoundError: com/yammer/metrics/core/Gauge
        at org.apache.hadoop.hbase.ipc.AbstractRpcClient.callBlockingMethod(AbstractRpcClient.java:224)
        ... 13 more
Caused by: java.lang.ClassNotFoundException: com.yammer.metrics.core.Gauge
        at java.net.URLClassLoader.findClass(URLClassLoader.java:381)
        at java.lang.ClassLoader.loadClass(ClassLoader.java:424)
        at sun.misc.Launcher$AppClassLoader.loadClass(Launcher.java:331)
        at java.lang.ClassLoader.loadClass(ClassLoader.java:357)
        ... 14 more
org.apache.hadoop.hbase.client.RetriesExhaustedException: Failed after attempts=36, exceptions:
Thu Jun 14 14:08:34 CST 2018, null, java.net.SocketTimeoutException: callTimeout=60000, callDuration=68735: row 'db_res:wtb_ow_operation,,00000000000000' on table 'hbase:meta' at region=hbase:meta,,1.1588230740, hostname=master,16020,1528955336185, seqNum=0

        at org.apache.hadoop.hbase.client.RpcRetryingCallerWithReadReplicas.throwEnrichedException(RpcRetryingCallerWithReadReplicas.java:276)
        at org.apache.hadoop.hbase.client.ScannerCallableWithReplicas.call(ScannerCallableWithReplicas.java:207)
        at org.apache.hadoop.hbase.client.ScannerCallableWithReplicas.call(ScannerCallableWithReplicas.java:60)
        at org.apache.hadoop.hbase.client.RpcRetryingCaller.callWithoutRetries(RpcRetryingCaller.java:200)
        at org.apache.hadoop.hbase.client.ClientScanner.call(ClientScanner.java:320)
        at org.apache.hadoop.hbase.client.ClientScanner.nextScanner(ClientScanner.java:295)
        at org.apache.hadoop.hbase.client.ClientScanner.initializeScannerInConstruction(ClientScanner.java:160)
        at org.apache.hadoop.hbase.client.ClientScanner.&lt;init&gt;(ClientScanner.java:155)
        at org.apache.hadoop.hbase.client.HTable.getScanner(HTable.java:802)
        at org.apache.hadoop.hbase.client.MetaScanner.metaScan(MetaScanner.java:193)
        at org.apache.hadoop.hbase.client.MetaScanner.metaScan(MetaScanner.java:89)
        at org.apache.hadoop.hbase.client.MetaScanner.allTableRegions(MetaScanner.java:324)
        at org.apache.hadoop.hbase.client.HRegionLocator.getAllRegionLocations(HRegionLocator.java:88)
        at org.apache.hadoop.hbase.util.RegionSizeCalculator.init(RegionSizeCalculator.java:94)
        at org.apache.hadoop.hbase.util.RegionSizeCalculator.&lt;init&gt;(RegionSizeCalculator.java:81)
        at org.apache.hadoop.hbase.mapreduce.TableInputFormatBase.getSplits(TableInputFormatBase.java:256)
        at org.apache.hadoop.hive.hbase.HiveHBaseTableInputFormat.getSplitsInternal(HiveHBaseTableInputFormat.java:499)
        at org.apache.hadoop.hive.hbase.HiveHBaseTableInputFormat.getSplits(HiveHBaseTableInputFormat.java:432)
        at org.apache.spark.rdd.HadoopRDD.getPartitions(HadoopRDD.scala:200)
        at org.apache.spark.rdd.RDD$$anonfun$partitions$2.apply(RDD.scala:253)
        at org.apache.spark.rdd.RDD$$anonfun$partitions$2.apply(RDD.scala:251)
        at scala.Option.getOrElse(Option.scala:121)
        at org.apache.spark.rdd.RDD.partitions(RDD.scala:251)
        at org.apache.spark.rdd.MapPartitionsRDD.getPartitions(MapPartitionsRDD.scala:35)
        at org.apache.spark.rdd.RDD$$anonfun$partitions$2.apply(RDD.scala:253)
        at org.apache.spark.rdd.RDD$$anonfun$partitions$2.apply(RDD.scala:251)
        at scala.Option.getOrElse(Option.scala:121)
        at org.apache.spark.rdd.RDD.partitions(RDD.scala:251)
        at org.apache.spark.rdd.MapPartitionsRDD.getPartitions(MapPartitionsRDD.scala:35)
        at org.apache.spark.rdd.RDD$$anonfun$partitions$2.apply(RDD.scala:253)
        at org.apache.spark.rdd.RDD$$anonfun$partitions$2.apply(RDD.scala:251)
        at scala.Option.getOrElse(Option.scala:121)
        at org.apache.spark.rdd.RDD.partitions(RDD.scala:251)
        at org.apache.spark.rdd.MapPartitionsRDD.getPartitions(MapPartitionsRDD.scala:35)
        at org.apache.spark.rdd.RDD$$anonfun$partitions$2.apply(RDD.scala:253)
        at org.apache.spark.rdd.RDD$$anonfun$partitions$2.apply(RDD.scala:251)
        at scala.Option.getOrElse(Option.scala:121)
        at org.apache.spark.rdd.RDD.partitions(RDD.scala:251)
        at org.apache.spark.rdd.MapPartitionsRDD.getPartitions(MapPartitionsRDD.scala:35)
        at org.apache.spark.rdd.RDD$$anonfun$partitions$2.apply(RDD.scala:253)
        at org.apache.spark.rdd.RDD$$anonfun$partitions$2.apply(RDD.scala:251)
        at scala.Option.getOrElse(Option.scala:121)
        at org.apache.spark.rdd.RDD.partitions(RDD.scala:251)
        at org.apache.spark.SparkContext.runJob(SparkContext.scala:2092)
        at org.apache.spark.rdd.RDD$$anonfun$collect$1.apply(RDD.scala:939)
        at org.apache.spark.rdd.RDDOperationScope$.withScope(RDDOperationScope.scala:151)
        at org.apache.spark.rdd.RDDOperationScope$.withScope(RDDOperationScope.scala:112)
        at org.apache.spark.rdd.RDD.withScope(RDD.scala:363)
        at org.apache.spark.rdd.RDD.collect(RDD.scala:938)
        at org.apache.spark.sql.execution.SparkPlan.executeCollect(SparkPlan.scala:297)
        at org.apache.spark.sql.execution.SparkPlan.executeCollectPublic(SparkPlan.scala:324)
        at org.apache.spark.sql.execution.QueryExecution.hiveResultString(QueryExecution.scala:122)
        at org.apache.spark.sql.hive.thriftserver.SparkSQLDriver$$anonfun$run$1.apply(SparkSQLDriver.scala:64)
        at org.apache.spark.sql.hive.thriftserver.SparkSQLDriver$$anonfun$run$1.apply(SparkSQLDriver.scala:64)
        at org.apache.spark.sql.execution.SQLExecution$.withNewExecutionId(SQLExecution.scala:77)
        at org.apache.spark.sql.hive.thriftserver.SparkSQLDriver.run(SparkSQLDriver.scala:63)
        at org.apache.spark.sql.hive.thriftserver.SparkSQLCLIDriver.processCmd(SparkSQLCLIDriver.scala:355)
        at org.apache.hadoop.hive.cli.CliDriver.processLine(CliDriver.java:376)
        at org.apache.spark.sql.hive.thriftserver.SparkSQLCLIDriver$.main(SparkSQLCLIDriver.scala:263)
        at org.apache.spark.sql.hive.thriftserver.SparkSQLCLIDriver.main(SparkSQLCLIDriver.scala)
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
        at java.lang.reflect.Method.invoke(Method.java:497)
        at org.apache.spark.deploy.JavaMainApplication.start(SparkApplication.scala:52)
        at org.apache.spark.deploy.SparkSubmit$.org$apache$spark$deploy$SparkSubmit$$runMain(SparkSubmit.scala:879)
        at org.apache.spark.deploy.SparkSubmit$.doRunMain$1(SparkSubmit.scala:197)
        at org.apache.spark.deploy.SparkSubmit$.submit(SparkSubmit.scala:227)
        at org.apache.spark.deploy.SparkSubmit$.main(SparkSubmit.scala:136)
        at org.apache.spark.deploy.SparkSubmit.main(SparkSubmit.scala)
Caused by: java.net.SocketTimeoutException: callTimeout=60000, callDuration=68735: row 'db_res:wtb_ow_operation,,00000000000000' on table 'hbase:meta' at region=hbase:meta,,1.1588230740, hostname=master,16020,1528955336185, seqNum=0
        at org.apache.hadoop.hbase.client.RpcRetryingCaller.callWithRetries(RpcRetryingCaller.java:159)
        at org.apache.hadoop.hbase.client.ResultBoundedCompletionService$QueueingFuture.run(ResultBoundedCompletionService.java:65)
        at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1142)
        at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:617)
        at java.lang.Thread.run(Thread.java:745)
Caused by: java.io.IOException: com.google.protobuf.ServiceException: java.lang.NoClassDefFoundError: com/yammer/metrics/core/Gauge
        at org.apache.hadoop.hbase.protobuf.ProtobufUtil.getRemoteException(ProtobufUtil.java:330)
        at org.apache.hadoop.hbase.client.ScannerCallable.openScanner(ScannerCallable.java:402)
        at org.apache.hadoop.hbase.client.ScannerCallable.call(ScannerCallable.java:203)
        at org.apache.hadoop.hbase.client.ScannerCallable.call(ScannerCallable.java:64)
        at org.apache.hadoop.hbase.client.RpcRetryingCaller.callWithoutRetries(RpcRetryingCaller.java:200)
        at org.apache.hadoop.hbase.client.ScannerCallableWithReplicas$RetryingRPC.call(ScannerCallableWithReplicas.java:360)
        at org.apache.hadoop.hbase.client.ScannerCallableWithReplicas$RetryingRPC.call(ScannerCallableWithReplicas.java:334)
        at org.apache.hadoop.hbase.client.RpcRetryingCaller.callWithRetries(RpcRetryingCaller.java:126)
        ... 4 more
Caused by: com.google.protobuf.ServiceException: java.lang.NoClassDefFoundError: com/yammer/metrics/core/Gauge
        at org.apache.hadoop.hbase.ipc.AbstractRpcClient.callBlockingMethod(AbstractRpcClient.java:239)
        at org.apache.hadoop.hbase.ipc.AbstractRpcClient$BlockingRpcChannelImplementation.callBlockingMethod(AbstractRpcClient.java:331)
        at org.apache.hadoop.hbase.protobuf.generated.ClientProtos$ClientService$BlockingStub.scan(ClientProtos.java:34094)
        at org.apache.hadoop.hbase.client.ScannerCallable.openScanner(ScannerCallable.java:394)
        ... 10 more
Caused by: java.lang.NoClassDefFoundError: com/yammer/metrics/core/Gauge
        at org.apache.hadoop.hbase.ipc.AbstractRpcClient.callBlockingMethod(AbstractRpcClient.java:224)
        ... 13 more
Caused by: java.lang.ClassNotFoundException: com.yammer.metrics.core.Gauge
        at java.net.URLClassLoader.findClass(URLClassLoader.java:381)
        at java.lang.ClassLoader.loadClass(ClassLoader.java:424)
        at sun.misc.Launcher$AppClassLoader.loadClass(Launcher.java:331)
        at java.lang.ClassLoader.loadClass(ClassLoader.java:357)
        ... 14 more

spark-sql&gt; </code></pre><br><p>加上metrics-core-2.2.0.jar，继续运行</p><pre><code class="language-html">root@master:~# spark-sql  --master spark://192.168.189.1:7077 --driver-class-path /usr/local/apache-hive-1.2.1/lib/mysql-connector-java-5.1.13-bin.jar:/usr/local/apache-hive-1.2.1/lib/hive-hbase-handler-1.2.1.jar:/usr/local/hbase-1.2.0/lib/hbase-client-1.2.0.jar:/usr/local/hbase-1.2.0/lib/hbase-common-1.2.0.jar:/usr/local/hbase-1.2.0/lib/hbase-protocol-1.2.0.jar:/usr/local/hbase-1.2.0/lib/hbase-server-1.2.0.jar:/usr/local/hbase-1.2.0/lib/htrace-core-3.1.0-incubating.jar:/usr/local/hbase-1.2.0/lib/metrics-core-2.2.0.jar  --executor-memory 512m  --total-executor-cores 4

</code></pre><pre><code class="language-html">2018-06-14 14:12:06 WARN  NativeCodeLoader:62 - Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
2018-06-14 14:12:07 WARN  HiveConf:2753 - HiveConf of name hive.server2.http.endpoint does not exist
2018-06-14 14:12:08 INFO  HiveMetaStore:589 - 0: Opening raw store with implemenation class:org.apache.hadoop.hive.metastore.ObjectStore
2018-06-14 14:12:08 INFO  ObjectStore:289 - ObjectStore, initialize called
2018-06-14 14:12:08 INFO  Persistence:77 - Property hive.metastore.integral.jdo.pushdown unknown - will be ignored
2018-06-14 14:12:08 INFO  Persistence:77 - Property datanucleus.cache.level2 unknown - will be ignored
2018-06-14 14:12:11 WARN  HiveConf:2753 - HiveConf of name hive.server2.http.endpoint does not exist
2018-06-14 14:12:11 INFO  ObjectStore:370 - Setting MetaStore object pin classes with hive.metastore.cache.pinobjtypes="Table,StorageDescriptor,SerDeInfo,Partition,Database,Type,FieldSchema,Order"
2018-06-14 14:12:12 INFO  Datastore:77 - The class "org.apache.hadoop.hive.metastore.model.MFieldSchema" is tagged as "embedded-only" so does not have its own datastore table.
2018-06-14 14:12:12 INFO  Datastore:77 - The class "org.apache.hadoop.hive.metastore.model.MOrder" is tagged as "embedded-only" so does not have its own datastore table.
2018-06-14 14:12:13 INFO  Datastore:77 - The class "org.apache.hadoop.hive.metastore.model.MFieldSchema" is tagged as "embedded-only" so does not have its own datastore table.
2018-06-14 14:12:13 INFO  Datastore:77 - The class "org.apache.hadoop.hive.metastore.model.MOrder" is tagged as "embedded-only" so does not have its own datastore table.
2018-06-14 14:12:14 INFO  Query:77 - Reading in results for query "org.datanucleus.store.rdbms.query.SQLQuery@0" since the connection used is closing
2018-06-14 14:12:14 INFO  MetaStoreDirectSql:139 - Using direct SQL, underlying DB is MYSQL
2018-06-14 14:12:14 INFO  ObjectStore:272 - Initialized ObjectStore
2018-06-14 14:12:14 INFO  HiveMetaStore:663 - Added admin role in metastore
2018-06-14 14:12:14 INFO  HiveMetaStore:672 - Added public role in metastore
2018-06-14 14:12:14 INFO  HiveMetaStore:712 - No user is added in admin role, since config is empty
2018-06-14 14:12:14 INFO  HiveMetaStore:746 - 0: get_all_databases
2018-06-14 14:12:14 INFO  audit:371 - ugi=root  ip=unknown-ip-addr      cmd=get_all_databases
2018-06-14 14:12:14 INFO  HiveMetaStore:746 - 0: get_functions: db=default pat=*
2018-06-14 14:12:14 INFO  audit:371 - ugi=root  ip=unknown-ip-addr      cmd=get_functions: db=default pat=*
2018-06-14 14:12:14 INFO  Datastore:77 - The class "org.apache.hadoop.hive.metastore.model.MResourceUri" is tagged as "embedded-only" so does not have its own datastore table.
2018-06-14 14:12:14 INFO  HiveMetaStore:746 - 0: get_functions: db=hive pat=*
2018-06-14 14:12:14 INFO  audit:371 - ugi=root  ip=unknown-ip-addr      cmd=get_functions: db=hive pat=*
2018-06-14 14:12:14 INFO  HiveMetaStore:746 - 0: get_functions: db=hivestudy pat=*
2018-06-14 14:12:14 INFO  audit:371 - ugi=root  ip=unknown-ip-addr      cmd=get_functions: db=hivestudy pat=*
2018-06-14 14:12:14 INFO  HiveMetaStore:746 - 0: get_functions: db=hrs pat=*
2018-06-14 14:12:14 INFO  audit:371 - ugi=root  ip=unknown-ip-addr      cmd=get_functions: db=hrs pat=*
2018-06-14 14:12:16 INFO  SessionState:641 - Created local directory: /tmp/7fc6b345-06df-495a-bbdb-8a42fefbabe5_resources
2018-06-14 14:12:16 INFO  SessionState:641 - Created HDFS directory: /tmp/hive/root/7fc6b345-06df-495a-bbdb-8a42fefbabe5
2018-06-14 14:12:16 INFO  SessionState:641 - Created local directory: /tmp/root/7fc6b345-06df-495a-bbdb-8a42fefbabe5
2018-06-14 14:12:16 INFO  SessionState:641 - Created HDFS directory: /tmp/hive/root/7fc6b345-06df-495a-bbdb-8a42fefbabe5/_tmp_space.db
2018-06-14 14:12:17 INFO  SparkContext:54 - Running Spark version 2.3.0
2018-06-14 14:12:17 INFO  SparkContext:54 - Submitted application: SparkSQL::master
2018-06-14 14:12:17 INFO  SecurityManager:54 - Changing view acls to: root
2018-06-14 14:12:17 INFO  SecurityManager:54 - Changing modify acls to: root
2018-06-14 14:12:17 INFO  SecurityManager:54 - Changing view acls groups to: 
2018-06-14 14:12:17 INFO  SecurityManager:54 - Changing modify acls groups to: 
2018-06-14 14:12:17 INFO  SecurityManager:54 - SecurityManager: authentication disabled; ui acls disabled; users  with view permissions: Set(root); groups with view permissions: Set(); users  with modify permissions: Set(root); groups with modify permissions: Set()
2018-06-14 14:12:18 INFO  Utils:54 - Successfully started service 'sparkDriver' on port 38605.
2018-06-14 14:12:18 INFO  SparkEnv:54 - Registering MapOutputTracker
2018-06-14 14:12:18 INFO  SparkEnv:54 - Registering BlockManagerMaster
2018-06-14 14:12:18 INFO  BlockManagerMasterEndpoint:54 - Using org.apache.spark.storage.DefaultTopologyMapper for getting topology information
2018-06-14 14:12:18 INFO  BlockManagerMasterEndpoint:54 - BlockManagerMasterEndpoint up
2018-06-14 14:12:18 INFO  DiskBlockManager:54 - Created local directory at /tmp/blockmgr-6428cebd-4b58-47c0-a96c-b36cff9d8123
2018-06-14 14:12:18 INFO  MemoryStore:54 - MemoryStore started with capacity 413.9 MB
2018-06-14 14:12:18 INFO  SparkEnv:54 - Registering OutputCommitCoordinator
2018-06-14 14:12:19 INFO  log:192 - Logging initialized @21860ms
2018-06-14 14:12:19 INFO  Server:346 - jetty-9.3.z-SNAPSHOT
2018-06-14 14:12:19 INFO  Server:414 - Started @22168ms
2018-06-14 14:12:19 INFO  AbstractConnector:278 - Started ServerConnector@5c09ddc8{HTTP/1.1,[http/1.1]}{0.0.0.0:4040}
2018-06-14 14:12:19 INFO  Utils:54 - Successfully started service 'SparkUI' on port 4040.
2018-06-14 14:12:19 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@29a33620{/jobs,null,AVAILABLE,@Spark}
2018-06-14 14:12:19 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@208205ed{/jobs/json,null,AVAILABLE,@Spark}
2018-06-14 14:12:19 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@73afe2b7{/jobs/job,null,AVAILABLE,@Spark}
2018-06-14 14:12:19 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@5700c9db{/jobs/job/json,null,AVAILABLE,@Spark}
2018-06-14 14:12:19 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@671d03bb{/stages,null,AVAILABLE,@Spark}
2018-06-14 14:12:19 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@6babffb5{/stages/json,null,AVAILABLE,@Spark}
2018-06-14 14:12:19 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@2173a742{/stages/stage,null,AVAILABLE,@Spark}
2018-06-14 14:12:19 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@4b8137c5{/stages/stage/json,null,AVAILABLE,@Spark}
2018-06-14 14:12:19 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@77f4c040{/stages/pool,null,AVAILABLE,@Spark}
2018-06-14 14:12:19 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@606a1bc4{/stages/pool/json,null,AVAILABLE,@Spark}
2018-06-14 14:12:19 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@6a15b73{/storage,null,AVAILABLE,@Spark}
2018-06-14 14:12:19 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@44dc7b7d{/storage/json,null,AVAILABLE,@Spark}
2018-06-14 14:12:19 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@15fb4566{/storage/rdd,null,AVAILABLE,@Spark}
2018-06-14 14:12:19 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@25ffd826{/storage/rdd/json,null,AVAILABLE,@Spark}
2018-06-14 14:12:19 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@29896529{/environment,null,AVAILABLE,@Spark}
2018-06-14 14:12:19 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@e29f4f6{/environment/json,null,AVAILABLE,@Spark}
2018-06-14 14:12:19 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@41b64020{/executors,null,AVAILABLE,@Spark}
2018-06-14 14:12:19 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@1a538ed8{/executors/json,null,AVAILABLE,@Spark}
2018-06-14 14:12:19 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@78910096{/executors/threadDump,null,AVAILABLE,@Spark}
2018-06-14 14:12:19 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@39dec536{/executors/threadDump/json,null,AVAILABLE,@Spark}
2018-06-14 14:12:19 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@4a1a256d{/static,null,AVAILABLE,@Spark}
2018-06-14 14:12:19 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@61bb1e4d{/,null,AVAILABLE,@Spark}
2018-06-14 14:12:19 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@5c53f292{/api,null,AVAILABLE,@Spark}
2018-06-14 14:12:19 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@41f4039e{/jobs/job/kill,null,AVAILABLE,@Spark}
2018-06-14 14:12:19 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@5ff00507{/stages/stage/kill,null,AVAILABLE,@Spark}
2018-06-14 14:12:19 INFO  SparkUI:54 - Bound SparkUI to 0.0.0.0, and started at http://master:4040
2018-06-14 14:12:20 INFO  StandaloneAppClient$ClientEndpoint:54 - Connecting to master spark://192.168.189.1:7077...
2018-06-14 14:12:20 INFO  TransportClientFactory:267 - Successfully created connection to /192.168.189.1:7077 after 55 ms (0 ms spent in bootstraps)
2018-06-14 14:12:20 INFO  StandaloneSchedulerBackend:54 - Connected to Spark cluster with app ID app-20180614141220-0003
2018-06-14 14:12:21 INFO  StandaloneAppClient$ClientEndpoint:54 - Executor added: app-20180614141220-0003/0 on worker-20180614135333-worker2-34319 (worker2:34319) with 1 core(s)
2018-06-14 14:12:21 INFO  StandaloneSchedulerBackend:54 - Granted executor ID app-20180614141220-0003/0 on hostPort worker2:34319 with 1 core(s), 512.0 MB RAM
2018-06-14 14:12:21 INFO  StandaloneAppClient$ClientEndpoint:54 - Executor added: app-20180614141220-0003/1 on worker-20180614135333-worker3-40134 (worker3:40134) with 1 core(s)
2018-06-14 14:12:21 INFO  StandaloneSchedulerBackend:54 - Granted executor ID app-20180614141220-0003/1 on hostPort worker3:40134 with 1 core(s), 512.0 MB RAM
2018-06-14 14:12:21 INFO  StandaloneAppClient$ClientEndpoint:54 - Executor added: app-20180614141220-0003/2 on worker-20180614135333-worker1-35690 (worker1:35690) with 1 core(s)
2018-06-14 14:12:21 INFO  StandaloneSchedulerBackend:54 - Granted executor ID app-20180614141220-0003/2 on hostPort worker1:35690 with 1 core(s), 512.0 MB RAM
2018-06-14 14:12:21 INFO  Utils:54 - Successfully started service 'org.apache.spark.network.netty.NettyBlockTransferService' on port 45270.
2018-06-14 14:12:21 INFO  NettyBlockTransferService:54 - Server created on master:45270
2018-06-14 14:12:21 INFO  BlockManager:54 - Using org.apache.spark.storage.RandomBlockReplicationPolicy for block replication policy
2018-06-14 14:12:21 INFO  StandaloneAppClient$ClientEndpoint:54 - Executor updated: app-20180614141220-0003/0 is now RUNNING
2018-06-14 14:12:21 INFO  StandaloneAppClient$ClientEndpoint:54 - Executor updated: app-20180614141220-0003/1 is now RUNNING
2018-06-14 14:12:21 INFO  StandaloneAppClient$ClientEndpoint:54 - Executor updated: app-20180614141220-0003/2 is now RUNNING
2018-06-14 14:12:21 INFO  BlockManagerMaster:54 - Registering BlockManager BlockManagerId(driver, master, 45270, None)
2018-06-14 14:12:21 INFO  BlockManagerMasterEndpoint:54 - Registering block manager master:45270 with 413.9 MB RAM, BlockManagerId(driver, master, 45270, None)
2018-06-14 14:12:21 INFO  BlockManagerMaster:54 - Registered BlockManager BlockManagerId(driver, master, 45270, None)
2018-06-14 14:12:21 INFO  BlockManager:54 - Initialized BlockManager: BlockManagerId(driver, master, 45270, None)
2018-06-14 14:12:21 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@6097fca9{/metrics/json,null,AVAILABLE,@Spark}
2018-06-14 14:12:21 INFO  StandaloneSchedulerBackend:54 - SchedulerBackend is ready for scheduling beginning after reached minRegisteredResourcesRatio: 0.0
2018-06-14 14:12:22 INFO  SharedState:54 - loading hive config file: file:/usr/local/spark-2.3.0-bin-hadoop2.6/conf/hive-site.xml
2018-06-14 14:12:22 INFO  SharedState:54 - spark.sql.warehouse.dir is not set, but hive.metastore.warehouse.dir is set. Setting spark.sql.warehouse.dir to the value of hive.metastore.warehouse.dir ('/user/hive/warehouse').
2018-06-14 14:12:22 INFO  SharedState:54 - Warehouse path is '/user/hive/warehouse'.
2018-06-14 14:12:22 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@7675c171{/SQL,null,AVAILABLE,@Spark}
2018-06-14 14:12:22 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@44e4cb76{/SQL/json,null,AVAILABLE,@Spark}
2018-06-14 14:12:22 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@15e1f8fe{/SQL/execution,null,AVAILABLE,@Spark}
2018-06-14 14:12:22 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@110b7837{/SQL/execution/json,null,AVAILABLE,@Spark}
2018-06-14 14:12:22 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@2dd1086{/static/sql,null,AVAILABLE,@Spark}
2018-06-14 14:12:22 INFO  HiveUtils:54 - Initializing HiveMetastoreConnection version 1.2.1 using Spark classes.
2018-06-14 14:12:22 INFO  HiveClientImpl:54 - Warehouse location for Hive client (version 1.2.2) is /user/hive/warehouse
2018-06-14 14:12:22 INFO  HiveMetaStore:746 - 0: get_database: default
2018-06-14 14:12:22 INFO  audit:371 - ugi=root  ip=unknown-ip-addr      cmd=get_database: default
2018-06-14 14:12:25 INFO  StateStoreCoordinatorRef:54 - Registered StateStoreCoordinator endpoint
2018-06-14 14:12:26 INFO  CoarseGrainedSchedulerBackend$DriverEndpoint:54 - Registered executor NettyRpcEndpointRef(spark-client://Executor) (192.168.189.4:48260) with ID 1
2018-06-14 14:12:26 INFO  CoarseGrainedSchedulerBackend$DriverEndpoint:54 - Registered executor NettyRpcEndpointRef(spark-client://Executor) (192.168.189.3:46519) with ID 0
2018-06-14 14:12:27 INFO  BlockManagerMasterEndpoint:54 - Registering block manager worker3:40925 with 117.0 MB RAM, BlockManagerId(1, worker3, 40925, None)
2018-06-14 14:12:27 INFO  BlockManagerMasterEndpoint:54 - Registering block manager worker2:51051 with 117.0 MB RAM, BlockManagerId(0, worker2, 51051, None)
2018-06-14 14:12:27 INFO  CoarseGrainedSchedulerBackend$DriverEndpoint:54 - Registered executor NettyRpcEndpointRef(spark-client://Executor) (192.168.189.2:42532) with ID 2
2018-06-14 14:12:28 INFO  BlockManagerMasterEndpoint:54 - Registering block manager worker1:51809 with 117.0 MB RAM, BlockManagerId(2, worker1, 51809, None)
spark-sql&gt; </code></pre><p>继续报错：</p><pre><code class="language-html">        at org.apache.spark.deploy.SparkSubmit.main(SparkSubmit.scala)
Caused by: java.lang.IllegalStateException: unread block data
        at java.io.ObjectInputStream$BlockDataInputStream.setBlockDataMode(ObjectInputStream.java:2431)
        at java.io.ObjectInputStream.readObject0(ObjectInputStream.java:1383)
        at java.io.ObjectInputStream.defaultReadFields(ObjectInputStream.java:2000)
        at java.io.ObjectInputStream.readSerialData(ObjectInputStream.java:1924)
        at java.io.ObjectInputStream.readOrdinaryObject(ObjectInputStream.java:1801)
        at java.io.ObjectInputStream.readObject0(ObjectInputStream.java:1351)
        at java.io.ObjectInputStream.readObject(ObjectInputStream.java:371)
        at org.apache.spark.serializer.JavaDeserializationStream.readObject(JavaSerializer.scala:75)
        at org.apache.spark.serializer.JavaSerializerInstance.deserialize(JavaSerializer.scala:114)
        at org.apache.spark.executor.Executor$TaskRunner.run(Executor.scala:313)
        at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1142)
        at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:617)
        at java.lang.Thread.run(Thread.java:745)
org.apache.spark.SparkException: Job aborted due to stage failure: Task 0 in stage 0.0 failed 4 times, most recent failure: Lost task 0.3 in stage 0.0 (TID 3, worker1, executor 2): java.lang.IllegalStateException: unread block data
        at java.io.ObjectInputStream$BlockDataInputStream.setBlockDataMode(ObjectInputStream.java:2431)
        at java.io.ObjectInputStream.readObject0(ObjectInputStream.java:1383)
        at java.io.ObjectInputStream.defaultReadFields(ObjectInputStream.java:2000)
        at java.io.ObjectInputStream.readSerialData(ObjectInputStream.java:1924)
        at java.io.ObjectInputStream.readOrdinaryObject(ObjectInputStream.java:1801)
        at java.io.ObjectInputStream.readObject0(ObjectInputStream.java:1351)
        at java.io.ObjectInputStream.readObject(ObjectInputStream.java:371)
        at org.apache.spark.serializer.JavaDeserializationStream.readObject(JavaSerializer.scala:75)
        at org.apache.spark.serializer.JavaSerializerInstance.deserialize(JavaSerializer.scala:114)
        at org.apache.spark.executor.Executor$TaskRunner.run(Executor.scala:313)
        at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1142)
        at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:617)
        at java.lang.Thread.run(Thread.java:745)

Driver stacktrace:
        at org.apache.spark.scheduler.DAGScheduler.org$apache$spark$scheduler$DAGScheduler$$failJobAndIndependentStages(DAGScheduler.scala:1599)
        at org.apache.spark.scheduler.DAGScheduler$$anonfun$abortStage$1.apply(DAGScheduler.scala:1587)
        at org.apache.spark.scheduler.DAGScheduler$$anonfun$abortStage$1.apply(DAGScheduler.scala:1586)
        at scala.collection.mutable.ResizableArray$class.foreach(ResizableArray.scala:59)
        at scala.collection.mutable.ArrayBuffer.foreach(ArrayBuffer.scala:48)
        at org.apache.spark.scheduler.DAGScheduler.abortStage(DAGScheduler.scala:1586)
        at org.apache.spark.scheduler.DAGScheduler$$anonfun$handleTaskSetFailed$1.apply(DAGScheduler.scala:831)
        at org.apache.spark.scheduler.DAGScheduler$$anonfun$handleTaskSetFailed$1.apply(DAGScheduler.scala:831)
        at scala.Option.foreach(Option.scala:257)
        at org.apache.spark.scheduler.DAGScheduler.handleTaskSetFailed(DAGScheduler.scala:831)
        at org.apache.spark.scheduler.DAGSchedulerEventProcessLoop.doOnReceive(DAGScheduler.scala:1820)
        at org.apache.spark.scheduler.DAGSchedulerEventProcessLoop.onReceive(DAGScheduler.scala:1769)
        at org.apache.spark.scheduler.DAGSchedulerEventProcessLoop.onReceive(DAGScheduler.scala:1758)
        at org.apache.spark.util.EventLoop$$anon$1.run(EventLoop.scala:48)
        at org.apache.spark.scheduler.DAGScheduler.runJob(DAGScheduler.scala:642)
        at org.apache.spark.SparkContext.runJob(SparkContext.scala:2027)
        at org.apache.spark.SparkContext.runJob(SparkContext.scala:2048)
        at org.apache.spark.SparkContext.runJob(SparkContext.scala:2067)
        at org.apache.spark.SparkContext.runJob(SparkContext.scala:2092)
        at org.apache.spark.rdd.RDD$$anonfun$collect$1.apply(RDD.scala:939)
        at org.apache.spark.rdd.RDDOperationScope$.withScope(RDDOperationScope.scala:151)
        at org.apache.spark.rdd.RDDOperationScope$.withScope(RDDOperationScope.scala:112)
        at org.apache.spark.rdd.RDD.withScope(RDD.scala:363)
        at org.apache.spark.rdd.RDD.collect(RDD.scala:938)
        at org.apache.spark.sql.execution.SparkPlan.executeCollect(SparkPlan.scala:297)
        at org.apache.spark.sql.execution.SparkPlan.executeCollectPublic(SparkPlan.scala:324)
        at org.apache.spark.sql.execution.QueryExecution.hiveResultString(QueryExecution.scala:122)
        at org.apache.spark.sql.hive.thriftserver.SparkSQLDriver$$anonfun$run$1.apply(SparkSQLDriver.scala:64)
        at org.apache.spark.sql.hive.thriftserver.SparkSQLDriver$$anonfun$run$1.apply(SparkSQLDriver.scala:64)
        at org.apache.spark.sql.execution.SQLExecution$.withNewExecutionId(SQLExecution.scala:77)
        at org.apache.spark.sql.hive.thriftserver.SparkSQLDriver.run(SparkSQLDriver.scala:63)
        at org.apache.spark.sql.hive.thriftserver.SparkSQLCLIDriver.processCmd(SparkSQLCLIDriver.scala:355)
        at org.apache.hadoop.hive.cli.CliDriver.processLine(CliDriver.java:376)
        at org.apache.spark.sql.hive.thriftserver.SparkSQLCLIDriver$.main(SparkSQLCLIDriver.scala:263)
        at org.apache.spark.sql.hive.thriftserver.SparkSQLCLIDriver.main(SparkSQLCLIDriver.scala)
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
        at java.lang.reflect.Method.invoke(Method.java:497)
        at org.apache.spark.deploy.JavaMainApplication.start(SparkApplication.scala:52)
        at org.apache.spark.deploy.SparkSubmit$.org$apache$spark$deploy$SparkSubmit$$runMain(SparkSubmit.scala:879)
        at org.apache.spark.deploy.SparkSubmit$.doRunMain$1(SparkSubmit.scala:197)
        at org.apache.spark.deploy.SparkSubmit$.submit(SparkSubmit.scala:227)
        at org.apache.spark.deploy.SparkSubmit$.main(SparkSubmit.scala:136)
        at org.apache.spark.deploy.SparkSubmit.main(SparkSubmit.scala)
Caused by: java.lang.IllegalStateException: unread block data
        at java.io.ObjectInputStream$BlockDataInputStream.setBlockDataMode(ObjectInputStream.java:2431)
        at java.io.ObjectInputStream.readObject0(ObjectInputStream.java:1383)
        at java.io.ObjectInputStream.defaultReadFields(ObjectInputStream.java:2000)
        at java.io.ObjectInputStream.readSerialData(ObjectInputStream.java:1924)
        at java.io.ObjectInputStream.readOrdinaryObject(ObjectInputStream.java:1801)
        at java.io.ObjectInputStream.readObject0(ObjectInputStream.java:1351)
        at java.io.ObjectInputStream.readObject(ObjectInputStream.java:371)
        at org.apache.spark.serializer.JavaDeserializationStream.readObject(JavaSerializer.scala:75)
        at org.apache.spark.serializer.JavaSerializerInstance.deserialize(JavaSerializer.scala:114)
        at org.apache.spark.executor.Executor$TaskRunner.run(Executor.scala:313)
        at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1142)
        at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:617)
        at java.lang.Thread.run(Thread.java:745)

spark-sql&gt; </code></pre><p>加上jar包</p><pre><code class="language-html">root@master:/usr/local/hbase-1.2.0/lib# ls | grep compat
hbase-hadoop2-compat-1.2.0.jar
hbase-hadoop-compat-1.2.0.jar
root@master:/usr/local/hbase-1.2.0/lib# ls | grep guava
guava-12.0.1.jar
root@master:/usr/local/hbase-1.2.0/lib# ls | grep protobuf
protobuf-java-2.5.0.jar
root@master:/usr/local/hbase-1.2.0/lib# </code></pre><p>继续在spark集群上运行</p><pre><code class="language-html">root@master:~# spark-sql  --master spark://192.168.189.1:7077 --driver-class-path /usr/local/apache-hive-1.2.1/lib/mysql-connector-java-5.1.13-bin.jar:/usr/local/apache-hive-1.2.1/lib/hive-hbase-handler-1.2.1.jar:/usr/local/hbase-1.2.0/lib/hbase-client-1.2.0.jar:/usr/local/hbase-1.2.0/lib/hbase-common-1.2.0.jar:/usr/local/hbase-1.2.0/lib/hbase-protocol-1.2.0.jar:/usr/local/hbase-1.2.0/lib/hbase-server-1.2.0.jar:/usr/local/hbase-1.2.0/lib/htrace-core-3.1.0-incubating.jar:/usr/local/hbase-1.2.0/lib/metrics-core-2.2.0.jar:/usr/local/hbase-1.2.0/lib/hbase-hadoop2-compat-1.2.0.jar:/usr/local/hbase-1.2.0/lib/guava-12.0.1.jar:/usr/local/hbase-1.2.0/lib/protobuf-java-2.5.0.jar  --executor-memory 512m  --total-executor-cores 4

</code></pre><pre><code class="language-html">2018-06-14 14:20:13 WARN  NativeCodeLoader:62 - Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
2018-06-14 14:20:15 WARN  HiveConf:2753 - HiveConf of name hive.server2.http.endpoint does not exist
2018-06-14 14:20:16 INFO  HiveMetaStore:589 - 0: Opening raw store with implemenation class:org.apache.hadoop.hive.metastore.ObjectStore
2018-06-14 14:20:16 INFO  ObjectStore:289 - ObjectStore, initialize called
2018-06-14 14:20:16 INFO  Persistence:77 - Property hive.metastore.integral.jdo.pushdown unknown - will be ignored
2018-06-14 14:20:16 INFO  Persistence:77 - Property datanucleus.cache.level2 unknown - will be ignored
2018-06-14 14:20:18 WARN  HiveConf:2753 - HiveConf of name hive.server2.http.endpoint does not exist
2018-06-14 14:20:18 INFO  ObjectStore:370 - Setting MetaStore object pin classes with hive.metastore.cache.pinobjtypes="Table,StorageDescriptor,SerDeInfo,Partition,Database,Type,FieldSchema,Order"
2018-06-14 14:20:20 INFO  Datastore:77 - The class "org.apache.hadoop.hive.metastore.model.MFieldSchema" is tagged as "embedded-only" so does not have its own datastore table.
2018-06-14 14:20:20 INFO  Datastore:77 - The class "org.apache.hadoop.hive.metastore.model.MOrder" is tagged as "embedded-only" so does not have its own datastore table.
2018-06-14 14:20:21 INFO  Datastore:77 - The class "org.apache.hadoop.hive.metastore.model.MFieldSchema" is tagged as "embedded-only" so does not have its own datastore table.
2018-06-14 14:20:21 INFO  Datastore:77 - The class "org.apache.hadoop.hive.metastore.model.MOrder" is tagged as "embedded-only" so does not have its own datastore table.
2018-06-14 14:20:21 INFO  Query:77 - Reading in results for query "org.datanucleus.store.rdbms.query.SQLQuery@0" since the connection used is closing
2018-06-14 14:20:21 INFO  MetaStoreDirectSql:139 - Using direct SQL, underlying DB is MYSQL
2018-06-14 14:20:21 INFO  ObjectStore:272 - Initialized ObjectStore
2018-06-14 14:20:22 INFO  HiveMetaStore:663 - Added admin role in metastore
2018-06-14 14:20:22 INFO  HiveMetaStore:672 - Added public role in metastore
2018-06-14 14:20:22 INFO  HiveMetaStore:712 - No user is added in admin role, since config is empty
2018-06-14 14:20:22 INFO  HiveMetaStore:746 - 0: get_all_databases
2018-06-14 14:20:22 INFO  audit:371 - ugi=root  ip=unknown-ip-addr      cmd=get_all_databases
2018-06-14 14:20:22 INFO  HiveMetaStore:746 - 0: get_functions: db=default pat=*
2018-06-14 14:20:22 INFO  audit:371 - ugi=root  ip=unknown-ip-addr      cmd=get_functions: db=default pat=*
2018-06-14 14:20:22 INFO  Datastore:77 - The class "org.apache.hadoop.hive.metastore.model.MResourceUri" is tagged as "embedded-only" so does not have its own datastore table.
2018-06-14 14:20:22 INFO  HiveMetaStore:746 - 0: get_functions: db=hive pat=*
2018-06-14 14:20:22 INFO  audit:371 - ugi=root  ip=unknown-ip-addr      cmd=get_functions: db=hive pat=*
2018-06-14 14:20:22 INFO  HiveMetaStore:746 - 0: get_functions: db=hivestudy pat=*
2018-06-14 14:20:22 INFO  audit:371 - ugi=root  ip=unknown-ip-addr      cmd=get_functions: db=hivestudy pat=*
2018-06-14 14:20:22 INFO  HiveMetaStore:746 - 0: get_functions: db=hrs pat=*
2018-06-14 14:20:22 INFO  audit:371 - ugi=root  ip=unknown-ip-addr      cmd=get_functions: db=hrs pat=*
2018-06-14 14:20:24 INFO  SessionState:641 - Created local directory: /tmp/677867b5-b0c3-4955-b8ac-d8f79b7ea29a_resources
2018-06-14 14:20:24 INFO  SessionState:641 - Created HDFS directory: /tmp/hive/root/677867b5-b0c3-4955-b8ac-d8f79b7ea29a
2018-06-14 14:20:24 INFO  SessionState:641 - Created local directory: /tmp/root/677867b5-b0c3-4955-b8ac-d8f79b7ea29a
2018-06-14 14:20:24 INFO  SessionState:641 - Created HDFS directory: /tmp/hive/root/677867b5-b0c3-4955-b8ac-d8f79b7ea29a/_tmp_space.db
2018-06-14 14:20:24 INFO  SparkContext:54 - Running Spark version 2.3.0
2018-06-14 14:20:24 INFO  SparkContext:54 - Submitted application: SparkSQL::master
2018-06-14 14:20:25 INFO  SecurityManager:54 - Changing view acls to: root
2018-06-14 14:20:25 INFO  SecurityManager:54 - Changing modify acls to: root
2018-06-14 14:20:25 INFO  SecurityManager:54 - Changing view acls groups to: 
2018-06-14 14:20:25 INFO  SecurityManager:54 - Changing modify acls groups to: 
2018-06-14 14:20:25 INFO  SecurityManager:54 - SecurityManager: authentication disabled; ui acls disabled; users  with view permissions: Set(root); groups with view permissions: Set(); users  with modify permissions: Set(root); groups with modify permissions: Set()
2018-06-14 14:20:25 INFO  Utils:54 - Successfully started service 'sparkDriver' on port 47983.
2018-06-14 14:20:26 INFO  SparkEnv:54 - Registering MapOutputTracker
2018-06-14 14:20:26 INFO  SparkEnv:54 - Registering BlockManagerMaster
2018-06-14 14:20:26 INFO  BlockManagerMasterEndpoint:54 - Using org.apache.spark.storage.DefaultTopologyMapper for getting topology information
2018-06-14 14:20:26 INFO  BlockManagerMasterEndpoint:54 - BlockManagerMasterEndpoint up
2018-06-14 14:20:26 INFO  DiskBlockManager:54 - Created local directory at /tmp/blockmgr-bd3f29b4-7966-4911-aefc-4172805c99c0
2018-06-14 14:20:26 INFO  MemoryStore:54 - MemoryStore started with capacity 413.9 MB
2018-06-14 14:20:26 INFO  SparkEnv:54 - Registering OutputCommitCoordinator
2018-06-14 14:20:26 INFO  log:192 - Logging initialized @22824ms
2018-06-14 14:20:27 INFO  Server:346 - jetty-9.3.z-SNAPSHOT
2018-06-14 14:20:27 INFO  Server:414 - Started @23183ms
2018-06-14 14:20:27 INFO  AbstractConnector:278 - Started ServerConnector@6b649efa{HTTP/1.1,[http/1.1]}{0.0.0.0:4040}
2018-06-14 14:20:27 INFO  Utils:54 - Successfully started service 'SparkUI' on port 4040.
2018-06-14 14:20:27 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@5c53f292{/jobs,null,AVAILABLE,@Spark}
2018-06-14 14:20:27 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@45117dd{/jobs/json,null,AVAILABLE,@Spark}
2018-06-14 14:20:27 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@7741771e{/jobs/job,null,AVAILABLE,@Spark}
2018-06-14 14:20:27 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@6cae2e4d{/jobs/job/json,null,AVAILABLE,@Spark}
2018-06-14 14:20:27 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@604d23fa{/stages,null,AVAILABLE,@Spark}
2018-06-14 14:20:27 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@1cc8416a{/stages/json,null,AVAILABLE,@Spark}
2018-06-14 14:20:27 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@331ff3ac{/stages/stage,null,AVAILABLE,@Spark}
2018-06-14 14:20:27 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@79980d8d{/stages/stage/json,null,AVAILABLE,@Spark}
2018-06-14 14:20:27 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@35d60381{/stages/pool,null,AVAILABLE,@Spark}
2018-06-14 14:20:27 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@68ab6ab0{/stages/pool/json,null,AVAILABLE,@Spark}
2018-06-14 14:20:27 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@61ffd148{/storage,null,AVAILABLE,@Spark}
2018-06-14 14:20:27 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@58324c9f{/storage/json,null,AVAILABLE,@Spark}
2018-06-14 14:20:27 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@5d7d8613{/storage/rdd,null,AVAILABLE,@Spark}
2018-06-14 14:20:27 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@6eb089e6{/storage/rdd/json,null,AVAILABLE,@Spark}
2018-06-14 14:20:27 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@715a70e9{/environment,null,AVAILABLE,@Spark}
2018-06-14 14:20:27 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@3bc69ce9{/environment/json,null,AVAILABLE,@Spark}
2018-06-14 14:20:27 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@abad89c{/executors,null,AVAILABLE,@Spark}
2018-06-14 14:20:27 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@602f8f94{/executors/json,null,AVAILABLE,@Spark}
2018-06-14 14:20:27 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@42507640{/executors/threadDump,null,AVAILABLE,@Spark}
2018-06-14 14:20:27 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@4dfe8b37{/executors/threadDump/json,null,AVAILABLE,@Spark}
2018-06-14 14:20:27 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@11dcd42c{/static,null,AVAILABLE,@Spark}
2018-06-14 14:20:27 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@11c713b7{/,null,AVAILABLE,@Spark}
2018-06-14 14:20:27 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@2251b3bc{/api,null,AVAILABLE,@Spark}
2018-06-14 14:20:27 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@3ab35b9c{/jobs/job/kill,null,AVAILABLE,@Spark}
2018-06-14 14:20:27 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@7741d346{/stages/stage/kill,null,AVAILABLE,@Spark}
2018-06-14 14:20:27 INFO  SparkUI:54 - Bound SparkUI to 0.0.0.0, and started at http://master:4040
2018-06-14 14:20:27 INFO  StandaloneAppClient$ClientEndpoint:54 - Connecting to master spark://192.168.189.1:7077...
2018-06-14 14:20:28 INFO  TransportClientFactory:267 - Successfully created connection to /192.168.189.1:7077 after 102 ms (0 ms spent in bootstraps)
2018-06-14 14:20:28 INFO  StandaloneSchedulerBackend:54 - Connected to Spark cluster with app ID app-20180614142028-0004
2018-06-14 14:20:28 INFO  Utils:54 - Successfully started service 'org.apache.spark.network.netty.NettyBlockTransferService' on port 54513.
2018-06-14 14:20:28 INFO  NettyBlockTransferService:54 - Server created on master:54513
2018-06-14 14:20:28 INFO  BlockManager:54 - Using org.apache.spark.storage.RandomBlockReplicationPolicy for block replication policy
2018-06-14 14:20:28 INFO  StandaloneAppClient$ClientEndpoint:54 - Executor added: app-20180614142028-0004/0 on worker-20180614135333-worker2-34319 (worker2:34319) with 1 core(s)
2018-06-14 14:20:28 INFO  StandaloneSchedulerBackend:54 - Granted executor ID app-20180614142028-0004/0 on hostPort worker2:34319 with 1 core(s), 512.0 MB RAM
2018-06-14 14:20:28 INFO  StandaloneAppClient$ClientEndpoint:54 - Executor added: app-20180614142028-0004/1 on worker-20180614135333-worker3-40134 (worker3:40134) with 1 core(s)
2018-06-14 14:20:28 INFO  StandaloneSchedulerBackend:54 - Granted executor ID app-20180614142028-0004/1 on hostPort worker3:40134 with 1 core(s), 512.0 MB RAM
2018-06-14 14:20:28 INFO  StandaloneAppClient$ClientEndpoint:54 - Executor added: app-20180614142028-0004/2 on worker-20180614135333-worker1-35690 (worker1:35690) with 1 core(s)
2018-06-14 14:20:28 INFO  StandaloneSchedulerBackend:54 - Granted executor ID app-20180614142028-0004/2 on hostPort worker1:35690 with 1 core(s), 512.0 MB RAM
2018-06-14 14:20:28 INFO  BlockManagerMaster:54 - Registering BlockManager BlockManagerId(driver, master, 54513, None)
2018-06-14 14:20:28 INFO  StandaloneAppClient$ClientEndpoint:54 - Executor updated: app-20180614142028-0004/2 is now RUNNING
2018-06-14 14:20:28 INFO  StandaloneAppClient$ClientEndpoint:54 - Executor updated: app-20180614142028-0004/1 is now RUNNING
2018-06-14 14:20:28 INFO  StandaloneAppClient$ClientEndpoint:54 - Executor updated: app-20180614142028-0004/0 is now RUNNING
2018-06-14 14:20:28 INFO  BlockManagerMasterEndpoint:54 - Registering block manager master:54513 with 413.9 MB RAM, BlockManagerId(driver, master, 54513, None)
2018-06-14 14:20:28 INFO  BlockManagerMaster:54 - Registered BlockManager BlockManagerId(driver, master, 54513, None)
2018-06-14 14:20:28 INFO  BlockManager:54 - Initialized BlockManager: BlockManagerId(driver, master, 54513, None)
2018-06-14 14:20:29 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@6401188a{/metrics/json,null,AVAILABLE,@Spark}
2018-06-14 14:20:29 INFO  StandaloneSchedulerBackend:54 - SchedulerBackend is ready for scheduling beginning after reached minRegisteredResourcesRatio: 0.0
2018-06-14 14:20:29 INFO  SharedState:54 - loading hive config file: file:/usr/local/spark-2.3.0-bin-hadoop2.6/conf/hive-site.xml
2018-06-14 14:20:30 INFO  SharedState:54 - spark.sql.warehouse.dir is not set, but hive.metastore.warehouse.dir is set. Setting spark.sql.warehouse.dir to the value of hive.metastore.warehouse.dir ('/user/hive/warehouse').
2018-06-14 14:20:30 INFO  SharedState:54 - Warehouse path is '/user/hive/warehouse'.
2018-06-14 14:20:30 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@24876a7e{/SQL,null,AVAILABLE,@Spark}
2018-06-14 14:20:30 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@1625789b{/SQL/json,null,AVAILABLE,@Spark}
2018-06-14 14:20:30 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@2eda2062{/SQL/execution,null,AVAILABLE,@Spark}
2018-06-14 14:20:30 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@1a9ec80e{/SQL/execution/json,null,AVAILABLE,@Spark}
2018-06-14 14:20:30 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@14b31e37{/static/sql,null,AVAILABLE,@Spark}
2018-06-14 14:20:30 INFO  HiveUtils:54 - Initializing HiveMetastoreConnection version 1.2.1 using Spark classes.
2018-06-14 14:20:30 INFO  HiveClientImpl:54 - Warehouse location for Hive client (version 1.2.2) is /user/hive/warehouse
2018-06-14 14:20:30 INFO  HiveMetaStore:746 - 0: get_database: default
2018-06-14 14:20:30 INFO  audit:371 - ugi=root  ip=unknown-ip-addr      cmd=get_database: default
2018-06-14 14:20:33 INFO  StateStoreCoordinatorRef:54 - Registered StateStoreCoordinator endpoint
2018-06-14 14:20:35 INFO  CoarseGrainedSchedulerBackend$DriverEndpoint:54 - Registered executor NettyRpcEndpointRef(spark-client://Executor) (192.168.189.2:44278) with ID 2
2018-06-14 14:20:35 INFO  CoarseGrainedSchedulerBackend$DriverEndpoint:54 - Registered executor NettyRpcEndpointRef(spark-client://Executor) (192.168.189.4:37646) with ID 1
2018-06-14 14:20:35 INFO  CoarseGrainedSchedulerBackend$DriverEndpoint:54 - Registered executor NettyRpcEndpointRef(spark-client://Executor) (192.168.189.3:45076) with ID 0
2018-06-14 14:20:35 INFO  BlockManagerMasterEndpoint:54 - Registering block manager worker1:36965 with 117.0 MB RAM, BlockManagerId(2, worker1, 36965, None)
2018-06-14 14:20:35 INFO  BlockManagerMasterEndpoint:54 - Registering block manager worker3:44100 with 117.0 MB RAM, BlockManagerId(1, worker3, 44100, None)
2018-06-14 14:20:35 INFO  BlockManagerMasterEndpoint:54 - Registering block manager worker2:40910 with 117.0 MB RAM, BlockManagerId(0, worker2, 40910, None)
spark-sql&gt; </code></pre><br><pre><code class="language-html">        at org.apache.spark.deploy.SparkSubmit$.org$apache$spark$deploy$SparkSubmit$$runMain(SparkSubmit.scala:879)
        at org.apache.spark.deploy.SparkSubmit$.doRunMain$1(SparkSubmit.scala:197)
        at org.apache.spark.deploy.SparkSubmit$.submit(SparkSubmit.scala:227)
        at org.apache.spark.deploy.SparkSubmit$.main(SparkSubmit.scala:136)
        at org.apache.spark.deploy.SparkSubmit.main(SparkSubmit.scala)
Caused by: java.lang.IllegalStateException: unread block data
        at java.io.ObjectInputStream$BlockDataInputStream.setBlockDataMode(ObjectInputStream.java:2431)
        at java.io.ObjectInputStream.readObject0(ObjectInputStream.java:1383)
        at java.io.ObjectInputStream.defaultReadFields(ObjectInputStream.java:2000)
        at java.io.ObjectInputStream.readSerialData(ObjectInputStream.java:1924)
        at java.io.ObjectInputStream.readOrdinaryObject(ObjectInputStream.java:1801)
        at java.io.ObjectInputStream.readObject0(ObjectInputStream.java:1351)
        at java.io.ObjectInputStream.readObject(ObjectInputStream.java:371)
        at org.apache.spark.serializer.JavaDeserializationStream.readObject(JavaSerializer.scala:75)
        at org.apache.spark.serializer.JavaSerializerInstance.deserialize(JavaSerializer.scala:114)
        at org.apache.spark.executor.Executor$TaskRunner.run(Executor.scala:313)
        at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1142)
        at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:617)
        at java.lang.Thread.run(Thread.java:745)</code></pre>executor也要部署jar包，重新运行<br><pre><code class="language-html">root@master:~# spark-sql  --master spark://192.168.189.1:7077 --driver-class-path /usr/local/apache-hive-1.2.1/lib/mysql-connector-java-5.1.13-bin.jar --jars /usr/local/apache-hive-1.2.1/lib/mysql-connector-java-5.1.13-bin.jar,/usr/local/apache-hive-1.2.1/lib/hive-hbase-handler-1.2.1.jar,/usr/local/hbase-1.2.0/lib/hbase-client-1.2.0.jar,/usr/local/hbase-1.2.0/lib/hbase-common-1.2.0.jar,/usr/local/hbase-1.2.0/lib/hbase-protocol-1.2.0.jar,/usr/local/hbase-1.2.0/lib/hbase-server-1.2.0.jar,/usr/local/hbase-1.2.0/lib/htrace-core-3.1.0-incubating.jar,/usr/local/hbase-1.2.0/lib/metrics-core-2.2.0.jar,/usr/local/hbase-1.2.0/lib/hbase-hadoop2-compat-1.2.0.jar,/usr/local/hbase-1.2.0/lib/guava-12.0.1.jar,/usr/local/hbase-1.2.0/lib/protobuf-java-2.5.0.jar  --executor-memory 512m  --total-executor-cores 4

</code></pre><pre><code class="language-html">2018-06-14 14:29:05 WARN  NativeCodeLoader:62 - Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
2018-06-14 14:29:06 WARN  HiveConf:2753 - HiveConf of name hive.server2.http.endpoint does not exist
2018-06-14 14:29:06 INFO  HiveMetaStore:589 - 0: Opening raw store with implemenation class:org.apache.hadoop.hive.metastore.ObjectStore
2018-06-14 14:29:06 INFO  ObjectStore:289 - ObjectStore, initialize called
2018-06-14 14:29:06 INFO  Persistence:77 - Property hive.metastore.integral.jdo.pushdown unknown - will be ignored
2018-06-14 14:29:06 INFO  Persistence:77 - Property datanucleus.cache.level2 unknown - will be ignored
2018-06-14 14:29:08 WARN  HiveConf:2753 - HiveConf of name hive.server2.http.endpoint does not exist
2018-06-14 14:29:08 INFO  ObjectStore:370 - Setting MetaStore object pin classes with hive.metastore.cache.pinobjtypes="Table,StorageDescriptor,SerDeInfo,Partition,Database,Type,FieldSchema,Order"
2018-06-14 14:29:09 INFO  Datastore:77 - The class "org.apache.hadoop.hive.metastore.model.MFieldSchema" is tagged as "embedded-only" so does not have its own datastore table.
2018-06-14 14:29:09 INFO  Datastore:77 - The class "org.apache.hadoop.hive.metastore.model.MOrder" is tagged as "embedded-only" so does not have its own datastore table.
2018-06-14 14:29:10 INFO  Datastore:77 - The class "org.apache.hadoop.hive.metastore.model.MFieldSchema" is tagged as "embedded-only" so does not have its own datastore table.
2018-06-14 14:29:10 INFO  Datastore:77 - The class "org.apache.hadoop.hive.metastore.model.MOrder" is tagged as "embedded-only" so does not have its own datastore table.
2018-06-14 14:29:10 INFO  Query:77 - Reading in results for query "org.datanucleus.store.rdbms.query.SQLQuery@0" since the connection used is closing
2018-06-14 14:29:10 INFO  MetaStoreDirectSql:139 - Using direct SQL, underlying DB is MYSQL
2018-06-14 14:29:10 INFO  ObjectStore:272 - Initialized ObjectStore
2018-06-14 14:29:11 INFO  HiveMetaStore:663 - Added admin role in metastore
2018-06-14 14:29:11 INFO  HiveMetaStore:672 - Added public role in metastore
2018-06-14 14:29:11 INFO  HiveMetaStore:712 - No user is added in admin role, since config is empty
2018-06-14 14:29:11 INFO  HiveMetaStore:746 - 0: get_all_databases
2018-06-14 14:29:11 INFO  audit:371 - ugi=root  ip=unknown-ip-addr      cmd=get_all_databases
2018-06-14 14:29:11 INFO  HiveMetaStore:746 - 0: get_functions: db=default pat=*
2018-06-14 14:29:11 INFO  audit:371 - ugi=root  ip=unknown-ip-addr      cmd=get_functions: db=default pat=*
2018-06-14 14:29:11 INFO  Datastore:77 - The class "org.apache.hadoop.hive.metastore.model.MResourceUri" is tagged as "embedded-only" so does not have its own datastore table.
2018-06-14 14:29:11 INFO  HiveMetaStore:746 - 0: get_functions: db=hive pat=*
2018-06-14 14:29:11 INFO  audit:371 - ugi=root  ip=unknown-ip-addr      cmd=get_functions: db=hive pat=*
2018-06-14 14:29:11 INFO  HiveMetaStore:746 - 0: get_functions: db=hivestudy pat=*
2018-06-14 14:29:11 INFO  audit:371 - ugi=root  ip=unknown-ip-addr      cmd=get_functions: db=hivestudy pat=*
2018-06-14 14:29:11 INFO  HiveMetaStore:746 - 0: get_functions: db=hrs pat=*
2018-06-14 14:29:11 INFO  audit:371 - ugi=root  ip=unknown-ip-addr      cmd=get_functions: db=hrs pat=*
2018-06-14 14:29:13 INFO  SessionState:641 - Created local directory: /tmp/4fbed167-ab00-414b-bc9d-3c6b99b2f413_resources
2018-06-14 14:29:13 INFO  SessionState:641 - Created HDFS directory: /tmp/hive/root/4fbed167-ab00-414b-bc9d-3c6b99b2f413
2018-06-14 14:29:13 INFO  SessionState:641 - Created local directory: /tmp/root/4fbed167-ab00-414b-bc9d-3c6b99b2f413
2018-06-14 14:29:13 INFO  SessionState:641 - Created HDFS directory: /tmp/hive/root/4fbed167-ab00-414b-bc9d-3c6b99b2f413/_tmp_space.db
2018-06-14 14:29:13 INFO  SparkContext:54 - Running Spark version 2.3.0
2018-06-14 14:29:13 INFO  SparkContext:54 - Submitted application: SparkSQL::master
2018-06-14 14:29:14 INFO  SecurityManager:54 - Changing view acls to: root
2018-06-14 14:29:14 INFO  SecurityManager:54 - Changing modify acls to: root
2018-06-14 14:29:14 INFO  SecurityManager:54 - Changing view acls groups to: 
2018-06-14 14:29:14 INFO  SecurityManager:54 - Changing modify acls groups to: 
2018-06-14 14:29:14 INFO  SecurityManager:54 - SecurityManager: authentication disabled; ui acls disabled; users  with view permissions: Set(root); groups with view permissions: Set(); users  with modify permissions: Set(root); groups with modify permissions: Set()
2018-06-14 14:29:15 INFO  Utils:54 - Successfully started service 'sparkDriver' on port 49272.
2018-06-14 14:29:15 INFO  SparkEnv:54 - Registering MapOutputTracker
2018-06-14 14:29:15 INFO  SparkEnv:54 - Registering BlockManagerMaster
2018-06-14 14:29:15 INFO  BlockManagerMasterEndpoint:54 - Using org.apache.spark.storage.DefaultTopologyMapper for getting topology information
2018-06-14 14:29:15 INFO  BlockManagerMasterEndpoint:54 - BlockManagerMasterEndpoint up
2018-06-14 14:29:15 INFO  DiskBlockManager:54 - Created local directory at /tmp/blockmgr-73bbdb0f-9938-427d-8c50-16e5ebf79421
2018-06-14 14:29:15 INFO  MemoryStore:54 - MemoryStore started with capacity 413.9 MB
2018-06-14 14:29:15 INFO  SparkEnv:54 - Registering OutputCommitCoordinator
2018-06-14 14:29:15 INFO  log:192 - Logging initialized @12155ms
2018-06-14 14:29:16 INFO  Server:346 - jetty-9.3.z-SNAPSHOT
2018-06-14 14:29:16 INFO  Server:414 - Started @12475ms
2018-06-14 14:29:16 INFO  AbstractConnector:278 - Started ServerConnector@6a7031c8{HTTP/1.1,[http/1.1]}{0.0.0.0:4040}
2018-06-14 14:29:16 INFO  Utils:54 - Successfully started service 'SparkUI' on port 4040.
2018-06-14 14:29:16 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@2785db06{/jobs,null,AVAILABLE,@Spark}
2018-06-14 14:29:16 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@7fda2001{/jobs/json,null,AVAILABLE,@Spark}
2018-06-14 14:29:16 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@162c1dfb{/jobs/job,null,AVAILABLE,@Spark}
2018-06-14 14:29:16 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@799f916e{/jobs/job/json,null,AVAILABLE,@Spark}
2018-06-14 14:29:16 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@576b7c74{/stages,null,AVAILABLE,@Spark}
2018-06-14 14:29:16 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@21de60a7{/stages/json,null,AVAILABLE,@Spark}
2018-06-14 14:29:16 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@73894c5a{/stages/stage,null,AVAILABLE,@Spark}
2018-06-14 14:29:16 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@28157173{/stages/stage/json,null,AVAILABLE,@Spark}
2018-06-14 14:29:16 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@11c713b7{/stages/pool,null,AVAILABLE,@Spark}
2018-06-14 14:29:16 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@2251b3bc{/stages/pool/json,null,AVAILABLE,@Spark}
2018-06-14 14:29:16 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@3fbe503c{/storage,null,AVAILABLE,@Spark}
2018-06-14 14:29:16 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@3c232051{/storage/json,null,AVAILABLE,@Spark}
2018-06-14 14:29:16 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@15d0d6c9{/storage/rdd,null,AVAILABLE,@Spark}
2018-06-14 14:29:16 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@3ab35b9c{/storage/rdd/json,null,AVAILABLE,@Spark}
2018-06-14 14:29:16 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@7741d346{/environment,null,AVAILABLE,@Spark}
2018-06-14 14:29:16 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@52454457{/environment/json,null,AVAILABLE,@Spark}
2018-06-14 14:29:16 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@4130a648{/executors,null,AVAILABLE,@Spark}
2018-06-14 14:29:16 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@61ff6a49{/executors/json,null,AVAILABLE,@Spark}
2018-06-14 14:29:16 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@18dd5ed3{/executors/threadDump,null,AVAILABLE,@Spark}
2018-06-14 14:29:16 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@6042d663{/executors/threadDump/json,null,AVAILABLE,@Spark}
2018-06-14 14:29:16 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@24043ec5{/static,null,AVAILABLE,@Spark}
2018-06-14 14:29:16 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@da4cf09{/,null,AVAILABLE,@Spark}
2018-06-14 14:29:16 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@1980a3f{/api,null,AVAILABLE,@Spark}
2018-06-14 14:29:16 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@39941489{/jobs/job/kill,null,AVAILABLE,@Spark}
2018-06-14 14:29:16 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@6f5d0190{/stages/stage/kill,null,AVAILABLE,@Spark}
2018-06-14 14:29:16 INFO  SparkUI:54 - Bound SparkUI to 0.0.0.0, and started at http://master:4040
2018-06-14 14:29:16 INFO  SparkContext:54 - Added JAR file:///usr/local/apache-hive-1.2.1/lib/mysql-connector-java-5.1.13-bin.jar at spark://master:49272/jars/mysql-connector-java-5.1.13-bin.jar with timestamp 1528957756445
2018-06-14 14:29:16 INFO  SparkContext:54 - Added JAR file:///usr/local/apache-hive-1.2.1/lib/hive-hbase-handler-1.2.1.jar at spark://master:49272/jars/hive-hbase-handler-1.2.1.jar with timestamp 1528957756447
2018-06-14 14:29:16 INFO  SparkContext:54 - Added JAR file:///usr/local/hbase-1.2.0/lib/hbase-client-1.2.0.jar at spark://master:49272/jars/hbase-client-1.2.0.jar with timestamp 1528957756448
2018-06-14 14:29:16 INFO  SparkContext:54 - Added JAR file:///usr/local/hbase-1.2.0/lib/hbase-common-1.2.0.jar at spark://master:49272/jars/hbase-common-1.2.0.jar with timestamp 1528957756449
2018-06-14 14:29:16 INFO  SparkContext:54 - Added JAR file:///usr/local/hbase-1.2.0/lib/hbase-protocol-1.2.0.jar at spark://master:49272/jars/hbase-protocol-1.2.0.jar with timestamp 1528957756451
2018-06-14 14:29:16 INFO  SparkContext:54 - Added JAR file:///usr/local/hbase-1.2.0/lib/hbase-server-1.2.0.jar at spark://master:49272/jars/hbase-server-1.2.0.jar with timestamp 1528957756452
2018-06-14 14:29:16 INFO  SparkContext:54 - Added JAR file:///usr/local/hbase-1.2.0/lib/htrace-core-3.1.0-incubating.jar at spark://master:49272/jars/htrace-core-3.1.0-incubating.jar with timestamp 1528957756453
2018-06-14 14:29:16 INFO  SparkContext:54 - Added JAR file:///usr/local/hbase-1.2.0/lib/metrics-core-2.2.0.jar at spark://master:49272/jars/metrics-core-2.2.0.jar with timestamp 1528957756455
2018-06-14 14:29:16 INFO  SparkContext:54 - Added JAR file:///usr/local/hbase-1.2.0/lib/hbase-hadoop2-compat-1.2.0.jar at spark://master:49272/jars/hbase-hadoop2-compat-1.2.0.jar with timestamp 1528957756456
2018-06-14 14:29:16 INFO  SparkContext:54 - Added JAR file:///usr/local/hbase-1.2.0/lib/guava-12.0.1.jar at spark://master:49272/jars/guava-12.0.1.jar with timestamp 1528957756458
2018-06-14 14:29:16 INFO  SparkContext:54 - Added JAR file:///usr/local/hbase-1.2.0/lib/protobuf-java-2.5.0.jar at spark://master:49272/jars/protobuf-java-2.5.0.jar with timestamp 1528957756460
2018-06-14 14:29:16 INFO  StandaloneAppClient$ClientEndpoint:54 - Connecting to master spark://192.168.189.1:7077...
2018-06-14 14:29:16 INFO  TransportClientFactory:267 - Successfully created connection to /192.168.189.1:7077 after 55 ms (0 ms spent in bootstraps)
2018-06-14 14:29:17 INFO  StandaloneSchedulerBackend:54 - Connected to Spark cluster with app ID app-20180614142917-0005
2018-06-14 14:29:17 INFO  StandaloneAppClient$ClientEndpoint:54 - Executor added: app-20180614142917-0005/0 on worker-20180614135333-worker2-34319 (worker2:34319) with 1 core(s)
2018-06-14 14:29:17 INFO  StandaloneSchedulerBackend:54 - Granted executor ID app-20180614142917-0005/0 on hostPort worker2:34319 with 1 core(s), 512.0 MB RAM
2018-06-14 14:29:17 INFO  StandaloneAppClient$ClientEndpoint:54 - Executor added: app-20180614142917-0005/1 on worker-20180614135333-worker3-40134 (worker3:40134) with 1 core(s)
2018-06-14 14:29:17 INFO  StandaloneSchedulerBackend:54 - Granted executor ID app-20180614142917-0005/1 on hostPort worker3:40134 with 1 core(s), 512.0 MB RAM
2018-06-14 14:29:17 INFO  StandaloneAppClient$ClientEndpoint:54 - Executor added: app-20180614142917-0005/2 on worker-20180614135333-worker1-35690 (worker1:35690) with 1 core(s)
2018-06-14 14:29:17 INFO  StandaloneSchedulerBackend:54 - Granted executor ID app-20180614142917-0005/2 on hostPort worker1:35690 with 1 core(s), 512.0 MB RAM
2018-06-14 14:29:17 INFO  Utils:54 - Successfully started service 'org.apache.spark.network.netty.NettyBlockTransferService' on port 44472.
2018-06-14 14:29:17 INFO  NettyBlockTransferService:54 - Server created on master:44472
2018-06-14 14:29:17 INFO  BlockManager:54 - Using org.apache.spark.storage.RandomBlockReplicationPolicy for block replication policy
2018-06-14 14:29:17 INFO  BlockManagerMaster:54 - Registering BlockManager BlockManagerId(driver, master, 44472, None)
2018-06-14 14:29:17 INFO  StandaloneAppClient$ClientEndpoint:54 - Executor updated: app-20180614142917-0005/0 is now RUNNING
2018-06-14 14:29:17 INFO  StandaloneAppClient$ClientEndpoint:54 - Executor updated: app-20180614142917-0005/2 is now RUNNING
2018-06-14 14:29:17 INFO  StandaloneAppClient$ClientEndpoint:54 - Executor updated: app-20180614142917-0005/1 is now RUNNING
2018-06-14 14:29:17 INFO  BlockManagerMasterEndpoint:54 - Registering block manager master:44472 with 413.9 MB RAM, BlockManagerId(driver, master, 44472, None)
2018-06-14 14:29:17 INFO  BlockManagerMaster:54 - Registered BlockManager BlockManagerId(driver, master, 44472, None)
2018-06-14 14:29:17 INFO  BlockManager:54 - Initialized BlockManager: BlockManagerId(driver, master, 44472, None)
2018-06-14 14:29:18 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@4a70d302{/metrics/json,null,AVAILABLE,@Spark}
2018-06-14 14:29:18 INFO  StandaloneSchedulerBackend:54 - SchedulerBackend is ready for scheduling beginning after reached minRegisteredResourcesRatio: 0.0
2018-06-14 14:29:18 INFO  SharedState:54 - loading hive config file: file:/usr/local/spark-2.3.0-bin-hadoop2.6/conf/hive-site.xml
2018-06-14 14:29:18 INFO  SharedState:54 - spark.sql.warehouse.dir is not set, but hive.metastore.warehouse.dir is set. Setting spark.sql.warehouse.dir to the value of hive.metastore.warehouse.dir ('/user/hive/warehouse').
2018-06-14 14:29:18 INFO  SharedState:54 - Warehouse path is '/user/hive/warehouse'.
2018-06-14 14:29:18 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@58278366{/SQL,null,AVAILABLE,@Spark}
2018-06-14 14:29:18 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@7f5fcfe9{/SQL/json,null,AVAILABLE,@Spark}
2018-06-14 14:29:18 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@548e43b1{/SQL/execution,null,AVAILABLE,@Spark}
2018-06-14 14:29:18 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@67688110{/SQL/execution/json,null,AVAILABLE,@Spark}
2018-06-14 14:29:18 INFO  ContextHandler:781 - Started o.s.j.s.ServletContextHandler@66e827a8{/static/sql,null,AVAILABLE,@Spark}
2018-06-14 14:29:19 INFO  HiveUtils:54 - Initializing HiveMetastoreConnection version 1.2.1 using Spark classes.
2018-06-14 14:29:19 INFO  HiveClientImpl:54 - Warehouse location for Hive client (version 1.2.2) is /user/hive/warehouse
2018-06-14 14:29:19 INFO  HiveMetaStore:746 - 0: get_database: default
2018-06-14 14:29:19 INFO  audit:371 - ugi=root  ip=unknown-ip-addr      cmd=get_database: default
2018-06-14 14:29:21 INFO  StateStoreCoordinatorRef:54 - Registered StateStoreCoordinator endpoint
2018-06-14 14:29:23 INFO  CoarseGrainedSchedulerBackend$DriverEndpoint:54 - Registered executor NettyRpcEndpointRef(spark-client://Executor) (192.168.189.4:50383) with ID 1
2018-06-14 14:29:23 INFO  CoarseGrainedSchedulerBackend$DriverEndpoint:54 - Registered executor NettyRpcEndpointRef(spark-client://Executor) (192.168.189.2:47048) with ID 2
2018-06-14 14:29:23 INFO  CoarseGrainedSchedulerBackend$DriverEndpoint:54 - Registered executor NettyRpcEndpointRef(spark-client://Executor) (192.168.189.3:48320) with ID 0
2018-06-14 14:29:23 INFO  BlockManagerMasterEndpoint:54 - Registering block manager worker3:37619 with 117.0 MB RAM, BlockManagerId(1, worker3, 37619, None)
2018-06-14 14:29:23 INFO  BlockManagerMasterEndpoint:54 - Registering block manager worker1:37338 with 117.0 MB RAM, BlockManagerId(2, worker1, 37338, None)
2018-06-14 14:29:23 INFO  BlockManagerMasterEndpoint:54 - Registering block manager worker2:57591 with 117.0 MB RAM, BlockManagerId(0, worker2, 57591, None)
spark-sql&gt; </code></pre><p>执行查询成功！</p><pre><code class="language-html">spark-sql&gt; use default;

</code></pre><pre><code class="language-html">2018-06-14 14:30:19 INFO  HiveMetaStore:746 - 0: get_database: global_temp
2018-06-14 14:30:19 INFO  audit:371 - ugi=root  ip=unknown-ip-addr      cmd=get_database: global_temp
2018-06-14 14:30:19 WARN  ObjectStore:568 - Failed to get database global_temp, returning NoSuchObjectException
2018-06-14 14:30:24 INFO  HiveMetaStore:746 - 0: get_database: default
2018-06-14 14:30:24 INFO  audit:371 - ugi=root  ip=unknown-ip-addr      cmd=get_database: default
Time taken: 5.834 seconds
2018-06-14 14:30:25 INFO  SparkSQLCLIDriver:951 - Time taken: 5.834 seconds

</code></pre><pre><code class="language-html">spark-sql&gt; 
         &gt;  select * from hbase_hive_wtb_ow_operation;

</code></pre><pre><code class="language-html">2018-06-14 14:30:31 INFO  HiveMetaStore:746 - 0: get_table : db=default tbl=hbase_hive_wtb_ow_operation
2018-06-14 14:30:31 INFO  audit:371 - ugi=root  ip=unknown-ip-addr      cmd=get_table : db=default tbl=hbase_hive_wtb_ow_operation
2018-06-14 14:30:33 INFO  ContextCleaner:54 - Cleaned accumulator 0
2018-06-14 14:30:35 INFO  MemoryStore:54 - Block broadcast_0 stored as values in memory (estimated size 237.4 KB, free 413.7 MB)
2018-06-14 14:30:35 INFO  MemoryStore:54 - Block broadcast_0_piece0 stored as bytes in memory (estimated size 22.7 KB, free 413.7 MB)
2018-06-14 14:30:35 INFO  BlockManagerInfo:54 - Added broadcast_0_piece0 in memory on master:44472 (size: 22.7 KB, free: 413.9 MB)
2018-06-14 14:30:35 INFO  SparkContext:54 - Created broadcast 0 from 
2018-06-14 14:30:36 INFO  HBaseStorageHandler:385 - Configuring input job properties
2018-06-14 14:30:37 INFO  RecoverableZooKeeper:120 - Process identifier=hconnection-0x6bd8c398 connecting to ZooKeeper ensemble=localhost:2181
2018-06-14 14:30:37 INFO  ZooKeeper:100 - Client environment:zookeeper.version=3.4.6-1569965, built on 02/20/2014 09:09 GMT
2018-06-14 14:30:37 INFO  ZooKeeper:100 - Client environment:host.name=master
2018-06-14 14:30:37 INFO  ZooKeeper:100 - Client environment:java.version=1.8.0_60
2018-06-14 14:30:37 INFO  ZooKeeper:100 - Client environment:java.vendor=Oracle Corporation
2018-06-14 14:30:37 INFO  ZooKeeper:100 - Client environment:java.home=/usr/local/jdk1.8.0_60/jre
2018-06-14 14:30:37 INFO  ZooKeeper:100 - Client environment:java.class.path=/usr/local/apache-hive-1.2.1/lib/mysql-connector-java-5.1.13-bin.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/conf/:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/jackson-dataformat-yaml-2.6.7.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/snappy-0.2.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/libfb303-0.9.3.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/eigenbase-properties-1.1.5.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/commons-pool-1.5.4.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/hk2-locator-2.4.0-b34.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/hive-beeline-1.2.1.spark2.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/spire_2.11-0.13.0.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/guice-servlet-3.0.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/flatbuffers-1.2.0-3f79e055.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/spark-network-common_2.11-2.3.0.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/leveldbjni-all-1.8.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/json4s-core_2.11-3.2.11.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/bonecp-0.8.0.RELEASE.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/java-xmlbuilder-1.1.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/hive-exec-1.2.1.spark2.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/zjsonpatch-0.3.0.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/metrics-jvm-3.1.5.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/scala-library-2.11.8.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/commons-lang3-3.5.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/spark-sketch_2.11-2.3.0.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/apacheds-i18n-2.0.0-M15.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/orc-core-1.4.1-nohive.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/jackson-core-asl-1.9.13.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/jpam-1.1.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/okio-1.13.0.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/orc-mapreduce-1.4.1-nohive.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/arrow-memory-0.8.0.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/netty-all-4.1.17.Final.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/spark-hive_2.11-2.3.0.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/javax.annotation-api-1.2.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/scala-reflect-2.11.8.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/parquet-format-2.3.1.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/avro-1.7.7.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/api-asn1-api-1.0.0-M20.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/py4j-0.10.6.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/univocity-parsers-2.5.9.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/joda-time-2.9.3.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/spark-streaming_2.11-2.3.0.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/xmlenc-0.52.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/guice-3.0.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/hadoop-yarn-server-web-proxy-2.6.5.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/jackson-xc-1.9.13.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/jaxb-api-2.2.2.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/xbean-asm5-shaded-4.4.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/stream-2.7.0.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/kubernetes-model-2.0.0.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/htrace-core-3.0.4.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/okhttp-3.8.1.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/commons-dbcp-1.4.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/jdo-api-3.0.1.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/spark-mllib_2.11-2.3.0.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/parquet-common-1.8.2.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/chill_2.11-0.8.4.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/javolution-5.5.1.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/commons-io-2.4.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/spark-launcher_2.11-2.3.0.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/stringtemplate-3.2.1.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/parquet-encoding-1.8.2.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/minlog-1.3.0.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/commons-net-2.2.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/httpcore-4.4.8.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/base64-2.3.8.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/kubernetes-client-3.0.0.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/curator-recipes-2.6.0.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/commons-digester-1.8.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/aircompressor-0.8.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/core-1.1.2.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/jodd-core-3.5.2.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/spark-repl_2.11-2.3.0.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/JavaEWAH-0.3.2.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/jul-to-slf4j-1.7.16.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/netty-3.9.9.Final.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/hadoop-mapreduce-client-core-2.6.5.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/jackson-mapper-asl-1.9.13.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/xz-1.0.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/scalap-2.11.8.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/jackson-jaxrs-1.9.13.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/javassist-3.18.1-GA.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/spark-mesos_2.11-2.3.0.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/kryo-shaded-3.0.3.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/httpclient-4.5.4.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/slf4j-api-1.7.16.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/generex-1.0.1.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/scala-parser-combinators_2.11-1.0.4.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/macro-compat_2.11-1.1.1.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/json4s-ast_2.11-3.2.11.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/zookeeper-3.4.6.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/metrics-json-3.1.5.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/gson-2.2.4.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/spark-sql_2.11-2.3.0.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/arrow-format-0.8.0.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/avro-mapred-1.7.7-hadoop2.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/commons-compiler-3.0.8.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/hk2-utils-2.4.0-b34.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/hadoop-mapreduce-client-shuffle-2.6.5.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/curator-client-2.6.0.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/commons-collections-3.2.2.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/hppc-0.7.2.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/aopalliance-1.0.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/stax-api-1.0-2.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/javax.servlet-api-3.1.0.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/hive-metastore-1.2.1.spark2.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/arrow-vector-0.8.0.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/osgi-resource-locator-1.0.1.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/datanucleus-core-3.2.10.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/spark-yarn_2.11-2.3.0.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/parquet-column-1.8.2.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/xercesImpl-2.9.1.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/hadoop-yarn-api-2.6.5.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/calcite-linq4j-1.2.0-incubating.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/lz4-java-1.4.0.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/spark-graphx_2.11-2.3.0.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/jersey-server-2.22.2.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/jackson-module-jaxb-annotations-2.6.7.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/spark-mllib-local_2.11-2.3.0.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/jets3t-0.9.4.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/jackson-module-scala_2.11-2.6.7.1.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/spark-tags_2.11-2.3.0.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/commons-httpclient-3.1.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/hive-cli-1.2.1.spark2.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/jersey-container-servlet-2.22.2.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/opencsv-2.3.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/calcite-avatica-1.2.0-incubating.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/jetty-util-6.1.26.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/spark-catalyst_2.11-2.3.0.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/hadoop-yarn-server-common-2.6.5.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/commons-lang-2.6.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/commons-codec-1.10.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/commons-compress-1.4.1.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/antlr-2.7.7.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/metrics-core-3.1.5.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/commons-math3-3.4.1.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/hadoop-hdfs-2.6.5.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/hadoop-auth-2.6.5.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/parquet-jackson-1.8.2.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/protobuf-java-2.5.0.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/stax-api-1.0.1.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/snakeyaml-1.15.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/jackson-databind-2.6.7.1.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/libthrift-0.9.3.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/breeze_2.11-0.13.2.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/jetty-6.1.26.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/apacheds-kerberos-codec-2.0.0-M15.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/jsr305-1.3.9.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/ivy-2.4.0.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/scala-compiler-2.11.8.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/javax.inject-2.4.0-b34.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/spark-kubernetes_2.11-2.3.0.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/guava-14.0.1.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/oro-2.0.8.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/hadoop-mapreduce-client-app-2.6.5.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/aopalliance-repackaged-2.4.0-b34.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/spark-hive-thriftserver_2.11-2.3.0.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/hadoop-mapreduce-client-common-2.6.5.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/antlr-runtime-3.4.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/spark-unsafe_2.11-2.3.0.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/commons-crypto-1.0.0.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/zstd-jni-1.3.2-2.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/commons-beanutils-1.7.0.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/javax.inject-1.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/commons-logging-1.1.3.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/apache-log4j-extras-1.2.17.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/spire-macros_2.11-0.13.0.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/jersey-media-jaxb-2.22.2.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/bcprov-jdk15on-1.58.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/janino-3.0.8.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/datanucleus-api-jdo-3.2.6.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/jtransforms-2.4.0.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/hk2-api-2.4.0-b34.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/jersey-common-2.22.2.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/commons-beanutils-core-1.8.0.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/hadoop-annotations-2.6.5.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/jackson-core-2.6.7.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/calcite-core-1.2.0-incubating.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/jackson-module-paranamer-2.7.9.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/curator-framework-2.6.0.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/hadoop-client-2.6.5.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/javax.ws.rs-api-2.0.1.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/commons-configuration-1.6.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/jackson-annotations-2.6.7.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/hadoop-common-2.6.5.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/logging-interceptor-3.8.1.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/activation-1.1.1.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/mesos-1.4.0-shaded-protobuf.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/objenesis-2.1.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/jersey-container-servlet-core-2.22.2.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/breeze-macros_2.11-0.13.2.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/antlr4-runtime-4.7.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/slf4j-log4j12-1.7.16.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/snappy-java-1.1.2.6.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/json4s-jackson_2.11-3.2.11.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/datanucleus-rdbms-3.2.9.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/hadoop-yarn-client-2.6.5.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/validation-api-1.1.0.Final.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/api-util-1.0.0-M20.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/commons-cli-1.2.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/arpack_combined_all-0.1.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/ST4-4.0.4.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/hadoop-mapreduce-client-jobclient-2.6.5.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/jcl-over-slf4j-1.7.16.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/pyrolite-4.13.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/spark-network-shuffle_2.11-2.3.0.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/super-csv-2.2.0.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/compress-lzf-1.0.3.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/parquet-hadoop-1.8.2.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/paranamer-2.8.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/avro-ipc-1.7.7.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/jersey-client-2.22.2.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/jta-1.1.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/chill-java-0.8.4.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/RoaringBitmap-0.5.11.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/scala-xml_2.11-1.0.5.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/jersey-guava-2.22.2.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/hive-jdbc-1.2.1.spark2.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/machinist_2.11-0.6.1.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/spark-core_2.11-2.3.0.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/hadoop-yarn-common-2.6.5.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/parquet-hadoop-bundle-1.6.0.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/log4j-1.2.17.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/shapeless_2.11-2.3.2.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/jline-2.12.1.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/metrics-graphite-3.1.5.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/spark-kvstore_2.11-2.3.0.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/automaton-1.11-8.jar:/usr/local/spark-2.3.0-bin-hadoop2.6/jars/derby-10.12.1.1.jar:/usr/local/hadoop-2.6.0/etc/hadoop/
2018-06-14 14:30:37 INFO  ZooKeeper:100 - Client environment:java.library.path=/usr/java/packages/lib/amd64:/usr/lib64:/lib64:/lib:/usr/lib
2018-06-14 14:30:37 INFO  ZooKeeper:100 - Client environment:java.io.tmpdir=/tmp
2018-06-14 14:30:37 INFO  ZooKeeper:100 - Client environment:java.compiler=&lt;NA&gt;
2018-06-14 14:30:37 INFO  ZooKeeper:100 - Client environment:os.name=Linux
2018-06-14 14:30:37 INFO  ZooKeeper:100 - Client environment:os.arch=amd64
2018-06-14 14:30:37 INFO  ZooKeeper:100 - Client environment:os.version=3.16.0-30-generic
2018-06-14 14:30:37 INFO  ZooKeeper:100 - Client environment:user.name=root
2018-06-14 14:30:37 INFO  ZooKeeper:100 - Client environment:user.home=/root
2018-06-14 14:30:37 INFO  ZooKeeper:100 - Client environment:user.dir=/root
2018-06-14 14:30:37 INFO  ZooKeeper:438 - Initiating client connection, connectString=localhost:2181 sessionTimeout=90000 watcher=hconnection-0x6bd8c3980x0, quorum=localhost:2181, baseZNode=/hbase
2018-06-14 14:30:37 INFO  ClientCnxn:975 - Opening socket connection to server localhost/127.0.0.1:2181. Will not attempt to authenticate using SASL (unknown error)
2018-06-14 14:30:37 INFO  ClientCnxn:852 - Socket connection established to localhost/127.0.0.1:2181, initiating session
2018-06-14 14:30:37 INFO  ClientCnxn:1235 - Session establishment complete on server localhost/127.0.0.1:2181, sessionid = 0x163fcd5cd600008, negotiated timeout = 40000
2018-06-14 14:30:38 WARN  TableInputFormatBase:618 - You are using an HTable instance that relies on an HBase-managed Connection. This is usually due to directly creating an HTable, which is deprecated. Instead, you should create a Connection object and then request a Table instance from it. If you don't need the Table instance for your own use, you should instead use the TableInputFormatBase.initalizeTable method directly.
2018-06-14 14:30:38 INFO  TableInputFormatBase:623 - Creating an additional unmanaged connection because user provided one can't be used for administrative actions. We'll close it when we close out the table.
2018-06-14 14:30:38 INFO  RecoverableZooKeeper:120 - Process identifier=hconnection-0x3cc2c4b8 connecting to ZooKeeper ensemble=localhost:2181
2018-06-14 14:30:38 INFO  ZooKeeper:438 - Initiating client connection, connectString=localhost:2181 sessionTimeout=90000 watcher=hconnection-0x3cc2c4b80x0, quorum=localhost:2181, baseZNode=/hbase
2018-06-14 14:30:38 INFO  ClientCnxn:975 - Opening socket connection to server localhost/127.0.0.1:2181. Will not attempt to authenticate using SASL (unknown error)
2018-06-14 14:30:38 INFO  ClientCnxn:852 - Socket connection established to localhost/127.0.0.1:2181, initiating session
2018-06-14 14:30:38 INFO  ClientCnxn:1235 - Session establishment complete on server localhost/127.0.0.1:2181, sessionid = 0x163fcd5cd600009, negotiated timeout = 40000
2018-06-14 14:30:38 INFO  RegionSizeCalculator:91 - Calculating region sizes for table "db_res:wtb_ow_operation".
2018-06-14 14:30:40 INFO  SparkContext:54 - Starting job: processCmd at CliDriver.java:376
2018-06-14 14:30:40 INFO  DAGScheduler:54 - Got job 0 (processCmd at CliDriver.java:376) with 1 output partitions
2018-06-14 14:30:40 INFO  DAGScheduler:54 - Final stage: ResultStage 0 (processCmd at CliDriver.java:376)
2018-06-14 14:30:40 INFO  DAGScheduler:54 - Parents of final stage: List()
2018-06-14 14:30:40 INFO  DAGScheduler:54 - Missing parents: List()
2018-06-14 14:30:40 INFO  DAGScheduler:54 - Submitting ResultStage 0 (MapPartitionsRDD[4] at processCmd at CliDriver.java:376), which has no missing parents
2018-06-14 14:30:41 INFO  MemoryStore:54 - Block broadcast_1 stored as values in memory (estimated size 15.6 KB, free 413.7 MB)
2018-06-14 14:30:41 INFO  MemoryStore:54 - Block broadcast_1_piece0 stored as bytes in memory (estimated size 8.4 KB, free 413.6 MB)
2018-06-14 14:30:41 INFO  BlockManagerInfo:54 - Added broadcast_1_piece0 in memory on master:44472 (size: 8.4 KB, free: 413.9 MB)
2018-06-14 14:30:41 INFO  SparkContext:54 - Created broadcast 1 from broadcast at DAGScheduler.scala:1039
2018-06-14 14:30:41 INFO  DAGScheduler:54 - Submitting 1 missing tasks from ResultStage 0 (MapPartitionsRDD[4] at processCmd at CliDriver.java:376) (first 15 tasks are for partitions Vector(0))
2018-06-14 14:30:41 INFO  TaskSchedulerImpl:54 - Adding task set 0.0 with 1 tasks
2018-06-14 14:30:41 INFO  TaskSetManager:54 - Starting task 0.0 in stage 0.0 (TID 0, worker1, executor 2, partition 0, NODE_LOCAL, 7965 bytes)
2018-06-14 14:30:46 INFO  BlockManagerInfo:54 - Added broadcast_1_piece0 in memory on worker1:37338 (size: 8.4 KB, free: 117.0 MB)
2018-06-14 14:30:48 INFO  BlockManagerInfo:54 - Added broadcast_0_piece0 in memory on worker1:37338 (size: 22.7 KB, free: 116.9 MB)
2018-06-14 14:31:01 INFO  TaskSetManager:54 - Finished task 0.0 in stage 0.0 (TID 0) in 20218 ms on worker1 (executor 2) (1/1)
2018-06-14 14:31:01 INFO  TaskSchedulerImpl:54 - Removed TaskSet 0.0, whose tasks have all completed, from pool 
2018-06-14 14:31:01 INFO  DAGScheduler:54 - ResultStage 0 (processCmd at CliDriver.java:376) finished in 20.873 s

</code></pre><pre><code class="language-html">2018-06-14 14:31:01 INFO  DAGScheduler:54 - Job 0 finished: processCmd at CliDriver.java:376, took 21.528504 s
10200000150470188       {"operate...time":"2017-12-20 13:22:17:17.0","operat..._time":"2017-12-30 15:22:17:17.0"}
Time taken: 31.045 seconds, Fetched 1 row(s)
2018-06-14 14:31:02 INFO  SparkSQLCLIDriver:951 - Time taken: 31.045 seconds, Fetched 1 row(s)
spark-sql&gt; </code></pre><p><span style="color:#ff0000;"><strong>在spark sql中的查询：</strong></span></p><img src="https://img-blog.csdn.net/20180614143438552" alt=""><img src="https://img-blog.csdn.net/20180614143534141" alt=""><br><p><br></p><p><span style="color:#ff0000;"><strong>在hbase中的查询：</strong></span></p><p><span style="color:#ff0000;"><strong><img src="https://img-blog.csdn.net/20180614144404996" alt=""><br></strong></span></p><p><span style="color:#ff0000;"><strong>在HIVE中的查询：</strong></span></p><p><img src="https://img-blog.csdn.net/20180614144243493" alt=""><br></p>            </div>
                </div>