---
layout:     post
title:      hadoop-mapreduce-examples
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/hzdxw/article/details/51656981				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<pre><code class="language-sql"> hadoop jar ./share/hadoop/mapreduce/hadoop-mapreduce-examples-2.3.0-cdh5.1.0.jar pi 2 5

[root@name01 cmake-2.8.12.2]# hadoop version
Hadoop 2.3.0-cdh5.1.0
Subversion git://github.sf.cloudera.com/CDH/cdh.git -r 8e266e052e423af592871e2dfe09d54c03f6a0e8
Compiled by jenkins on 2014-07-12T13:48Z
Compiled with protoc 2.5.0
From source with checksum 7ec68264497939dee7ab5b91250cbd9
This command was run using /usr/local/hadoop/share/hadoop/common/hadoop-common-2.3.0-cdh5.1.0.jar
[root@name01 cmake-2.8.12.2]# pwd
/usr/local/cmake/cmake-2.8.12.2
[root@name01 cmake-2.8.12.2]# cd /usr/local/hadoop/share/
[root@name01 share]# ls -l
total 8
drwxr-xr-x 38 root root 4096 Jul 12  2014 doc
drwxr-xr-x  9 root root 4096 Jul 12  2014 hadoop
[root@name01 share]# cd  hadoop/
[root@name01 hadoop]# ls -l
total 28
drwxr-xr-x 6 root root 4096 Jul 12  2014 common
drwxr-xr-x 7 root root 4096 Jul 12  2014 hdfs
drwxr-xr-x 3 root root 4096 Jul 12  2014 httpfs
lrwxrwxrwx 1 root root   10 Jun 13  2015 mapreduce -&gt; mapreduce2
drwxr-xr-x 5 root root 4096 Jul 12  2014 mapreduce1
drwxr-xr-x 5 root root 4096 Jul 12  2014 mapreduce2
drwxr-xr-x 5 root root 4096 Jul 12  2014 tools
drwxr-xr-x 5 root root 4096 Jul 12  2014 yarn
[root@name01 hadoop]# pwd
/usr/local/hadoop/share/hadoop
[root@name01 hadoop]# cd mapreduce2/
[root@name01 mapreduce2]# ls
hadoop-mapreduce-client-app-2.3.0-cdh5.1.0.jar     hadoop-mapreduce-client-hs-plugins-2.3.0-cdh5.1.0.jar       hadoop-mapreduce-examples-2.3.0-cdh5.1.0.jar
hadoop-mapreduce-client-common-2.3.0-cdh5.1.0.jar  hadoop-mapreduce-client-jobclient-2.3.0-cdh5.1.0.jar        lib
hadoop-mapreduce-client-core-2.3.0-cdh5.1.0.jar    hadoop-mapreduce-client-jobclient-2.3.0-cdh5.1.0-tests.jar  lib-examples
hadoop-mapreduce-client-hs-2.3.0-cdh5.1.0.jar      hadoop-mapreduce-client-shuffle-2.3.0-cdh5.1.0.jar          sources
[root@name01 mapreduce2]# ls -l
total 4668
-rw-r--r-- 1 root root  494195 Jul 12  2014 hadoop-mapreduce-client-app-2.3.0-cdh5.1.0.jar
-rw-r--r-- 1 root root  668009 Jul 12  2014 hadoop-mapreduce-client-common-2.3.0-cdh5.1.0.jar
-rw-r--r-- 1 root root 1491204 Jul 12  2014 hadoop-mapreduce-client-core-2.3.0-cdh5.1.0.jar
-rw-r--r-- 1 root root  247423 Jul 12  2014 hadoop-mapreduce-client-hs-2.3.0-cdh5.1.0.jar
-rw-r--r-- 1 root root   10373 Jul 12  2014 hadoop-mapreduce-client-hs-plugins-2.3.0-cdh5.1.0.jar
-rw-r--r-- 1 root root   41769 Jul 12  2014 hadoop-mapreduce-client-jobclient-2.3.0-cdh5.1.0.jar
-rw-r--r-- 1 root root 1488980 Jul 12  2014 hadoop-mapreduce-client-jobclient-2.3.0-cdh5.1.0-tests.jar
-rw-r--r-- 1 root root   28243 Jul 12  2014 hadoop-mapreduce-client-shuffle-2.3.0-cdh5.1.0.jar
-rw-r--r-- 1 root root  275826 Jul 12  2014 hadoop-mapreduce-examples-2.3.0-cdh5.1.0.jar
drwxr-xr-x 2 root root    4096 Jul 12  2014 lib
drwxr-xr-x 2 root root    4096 Jul 12  2014 lib-examples
drwxr-xr-x 2 root root    4096 Jul 12  2014 sources
[root@name01 mapreduce2]# pwd
/usr/local/hadoop/share/hadoop/mapreduce2
[root@name01 mapreduce2]# cd ..
[root@name01 hadoop]# pwd
/usr/local/hadoop/share/hadoop
[root@name01 hadoop]# cd ..
[root@name01 share]# pwd
/usr/local/hadoop/share
[root@name01 share]# cd ..
[root@name01 hadoop]# ls
bin  bin-mapreduce1  cloudera  data  etc  examples  examples-mapreduce1  hadoop-2.3.0-cdh5.1.0.txt  include  lib  libexec  logs  sbin  share  src  test_hd
[root@name01 hadoop]# ls -l
total 64
drwxr-xr-x  2 root root 4096 Jul 12  2014 bin
drwxr-xr-x  2 root root 4096 Jul 12  2014 bin-mapreduce1
drwxr-xr-x  3 root root 4096 Jul 12  2014 cloudera
drwxr-xr-x  4 root root 4096 Jun 11 04:45 data
drwxr-xr-x  7 root root 4096 Jul  3  2015 etc
drwxr-xr-x  5 root root 4096 Jul 12  2014 examples
drwxr-xr-x  3 root root 4096 Jul 12  2014 examples-mapreduce1
-rw-r--r--  1 root root   22 Jul  3  2015 hadoop-2.3.0-cdh5.1.0.txt
drwxr-xr-x  2 root root 4096 Jul 12  2014 include
drwxr-xr-x  3 root root 4096 Jul 12  2014 lib
drwxr-xr-x  2 root root 4096 Jul 12  2014 libexec
drwxr-xr-x  3 root root 4096 Jun 12 18:33 logs
drwxr-xr-x  3 root root 4096 Jul 12  2014 sbin
drwxr-xr-x  4 root root 4096 Jul 12  2014 share
drwxr-xr-x 17 root root 4096 Jun  9  2015 src
drwxr-xr-x  2 root root 4096 Jun 11 04:14 test_hd
[root@name01 hadoop]# pwd
/usr/local/hadoop
[root@name01 hadoop]#  hadoop jar ./share/hadoop/mapreduce/hadoop-mapreduce-examples-2.3.0-cdh5.1.0.jar pi 2 5
Number of Maps  = 2
Samples per Map = 5
Java HotSpot(TM) Client VM warning: You have loaded library /usr/local/hadoop/lib/native/libhadoop.so which might have disabled stack guard. The VM will try to fix the stack guard now.
It's highly recommended that you fix the library with 'execstack -c &lt;libfile&gt;', or link it with '-z noexecstack'.
16/06/12 20:55:15 WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
16/06/12 20:55:22 WARN hdfs.DFSClient: DataStreamer Exception
org.apache.hadoop.ipc.RemoteException(java.io.IOException): File /user/root/QuasiMonteCarlo_1465790114449_539534031/in/part0 could only be replicated to 0 nodes instead of minReplication (=1).  There are 1 datanode(s) running and no node(s) are excluded in this operation.
	at org.apache.hadoop.hdfs.server.blockmanagement.BlockManager.chooseTarget(BlockManager.java:1447)
	at org.apache.hadoop.hdfs.server.namenode.FSNamesystem.getAdditionalBlock(FSNamesystem.java:2703)
	at org.apache.hadoop.hdfs.server.namenode.NameNodeRpcServer.addBlock(NameNodeRpcServer.java:569)
	at org.apache.hadoop.hdfs.protocolPB.ClientNamenodeProtocolServerSideTranslatorPB.addBlock(ClientNamenodeProtocolServerSideTranslatorPB.java:440)
	at org.apache.hadoop.hdfs.protocol.proto.ClientNamenodeProtocolProtos$ClientNamenodeProtocol$2.callBlockingMethod(ClientNamenodeProtocolProtos.java)
	at org.apache.hadoop.ipc.ProtobufRpcEngine$Server$ProtoBufRpcInvoker.call(ProtobufRpcEngine.java:585)
	at org.apache.hadoop.ipc.RPC$Server.call(RPC.java:1026)
	at org.apache.hadoop.ipc.Server$Handler$1.run(Server.java:1986)
	at org.apache.hadoop.ipc.Server$Handler$1.run(Server.java:1982)
	at java.security.AccessController.doPrivileged(Native Method)
	at javax.security.auth.Subject.doAs(Subject.java:422)
	at org.apache.hadoop.security.UserGroupInformation.doAs(UserGroupInformation.java:1554)
	at org.apache.hadoop.ipc.Server$Handler.run(Server.java:1980)

	at org.apache.hadoop.ipc.Client.call(Client.java:1409)
	at org.apache.hadoop.ipc.Client.call(Client.java:1362)
	at org.apache.hadoop.ipc.ProtobufRpcEngine$Invoker.invoke(ProtobufRpcEngine.java:206)
	at com.sun.proxy.$Proxy7.addBlock(Unknown Source)
	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.lang.reflect.Method.invoke(Method.java:483)
	at org.apache.hadoop.io.retry.RetryInvocationHandler.invokeMethod(RetryInvocationHandler.java:186)
	at org.apache.hadoop.io.retry.RetryInvocationHandler.invoke(RetryInvocationHandler.java:102)
	at com.sun.proxy.$Proxy7.addBlock(Unknown Source)
	at org.apache.hadoop.hdfs.protocolPB.ClientNamenodeProtocolTranslatorPB.addBlock(ClientNamenodeProtocolTranslatorPB.java:362)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.locateFollowingBlock(DFSOutputStream.java:1438)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.nextBlockOutputStream(DFSOutputStream.java:1260)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.run(DFSOutputStream.java:525)
org.apache.hadoop.ipc.RemoteException(java.io.IOException): File /user/root/QuasiMonteCarlo_1465790114449_539534031/in/part0 could only be replicated to 0 nodes instead of minReplication (=1).  There are 1 datanode(s) running and no node(s) are excluded in this operation.
	at org.apache.hadoop.hdfs.server.blockmanagement.BlockManager.chooseTarget(BlockManager.java:1447)
	at org.apache.hadoop.hdfs.server.namenode.FSNamesystem.getAdditionalBlock(FSNamesystem.java:2703)
	at org.apache.hadoop.hdfs.server.namenode.NameNodeRpcServer.addBlock(NameNodeRpcServer.java:569)
	at org.apache.hadoop.hdfs.protocolPB.ClientNamenodeProtocolServerSideTranslatorPB.addBlock(ClientNamenodeProtocolServerSideTranslatorPB.java:440)
	at org.apache.hadoop.hdfs.protocol.proto.ClientNamenodeProtocolProtos$ClientNamenodeProtocol$2.callBlockingMethod(ClientNamenodeProtocolProtos.java)
	at org.apache.hadoop.ipc.ProtobufRpcEngine$Server$ProtoBufRpcInvoker.call(ProtobufRpcEngine.java:585)
	at org.apache.hadoop.ipc.RPC$Server.call(RPC.java:1026)
	at org.apache.hadoop.ipc.Server$Handler$1.run(Server.java:1986)
	at org.apache.hadoop.ipc.Server$Handler$1.run(Server.java:1982)
	at java.security.AccessController.doPrivileged(Native Method)
	at javax.security.auth.Subject.doAs(Subject.java:422)
	at org.apache.hadoop.security.UserGroupInformation.doAs(UserGroupInformation.java:1554)
	at org.apache.hadoop.ipc.Server$Handler.run(Server.java:1980)

	at org.apache.hadoop.ipc.Client.call(Client.java:1409)
	at org.apache.hadoop.ipc.Client.call(Client.java:1362)
	at org.apache.hadoop.ipc.ProtobufRpcEngine$Invoker.invoke(ProtobufRpcEngine.java:206)
	at com.sun.proxy.$Proxy7.addBlock(Unknown Source)
	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.lang.reflect.Method.invoke(Method.java:483)
	at org.apache.hadoop.io.retry.RetryInvocationHandler.invokeMethod(RetryInvocationHandler.java:186)
	at org.apache.hadoop.io.retry.RetryInvocationHandler.invoke(RetryInvocationHandler.java:102)
	at com.sun.proxy.$Proxy7.addBlock(Unknown Source)
	at org.apache.hadoop.hdfs.protocolPB.ClientNamenodeProtocolTranslatorPB.addBlock(ClientNamenodeProtocolTranslatorPB.java:362)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.locateFollowingBlock(DFSOutputStream.java:1438)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.nextBlockOutputStream(DFSOutputStream.java:1260)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.run(DFSOutputStream.java:525)
16/06/12 20:55:23 ERROR hdfs.DFSClient: Failed to close inode 16597
org.apache.hadoop.ipc.RemoteException(java.io.IOException): File /user/root/QuasiMonteCarlo_1465790114449_539534031/in/part0 could only be replicated to 0 nodes instead of minReplication (=1).  There are 1 datanode(s) running and no node(s) are excluded in this operation.
	at org.apache.hadoop.hdfs.server.blockmanagement.BlockManager.chooseTarget(BlockManager.java:1447)
	at org.apache.hadoop.hdfs.server.namenode.FSNamesystem.getAdditionalBlock(FSNamesystem.java:2703)
	at org.apache.hadoop.hdfs.server.namenode.NameNodeRpcServer.addBlock(NameNodeRpcServer.java:569)
	at org.apache.hadoop.hdfs.protocolPB.ClientNamenodeProtocolServerSideTranslatorPB.addBlock(ClientNamenodeProtocolServerSideTranslatorPB.java:440)
	at org.apache.hadoop.hdfs.protocol.proto.ClientNamenodeProtocolProtos$ClientNamenodeProtocol$2.callBlockingMethod(ClientNamenodeProtocolProtos.java)
	at org.apache.hadoop.ipc.ProtobufRpcEngine$Server$ProtoBufRpcInvoker.call(ProtobufRpcEngine.java:585)
	at org.apache.hadoop.ipc.RPC$Server.call(RPC.java:1026)
	at org.apache.hadoop.ipc.Server$Handler$1.run(Server.java:1986)
	at org.apache.hadoop.ipc.Server$Handler$1.run(Server.java:1982)
	at java.security.AccessController.doPrivileged(Native Method)
	at javax.security.auth.Subject.doAs(Subject.java:422)
	at org.apache.hadoop.security.UserGroupInformation.doAs(UserGroupInformation.java:1554)
	at org.apache.hadoop.ipc.Server$Handler.run(Server.java:1980)

	at org.apache.hadoop.ipc.Client.call(Client.java:1409)
	at org.apache.hadoop.ipc.Client.call(Client.java:1362)
	at org.apache.hadoop.ipc.ProtobufRpcEngine$Invoker.invoke(ProtobufRpcEngine.java:206)
	at com.sun.proxy.$Proxy7.addBlock(Unknown Source)
	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.lang.reflect.Method.invoke(Method.java:483)
	at org.apache.hadoop.io.retry.RetryInvocationHandler.invokeMethod(RetryInvocationHandler.java:186)
	at org.apache.hadoop.io.retry.RetryInvocationHandler.invoke(RetryInvocationHandler.java:102)
	at com.sun.proxy.$Proxy7.addBlock(Unknown Source)
	at org.apache.hadoop.hdfs.protocolPB.ClientNamenodeProtocolTranslatorPB.addBlock(ClientNamenodeProtocolTranslatorPB.java:362)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.locateFollowingBlock(DFSOutputStream.java:1438)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.nextBlockOutputStream(DFSOutputStream.java:1260)
	at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer.run(DFSOutputStream.java:525)
[root@name01 hadoop]# 
</code></pre><br>            </div>
                </div>