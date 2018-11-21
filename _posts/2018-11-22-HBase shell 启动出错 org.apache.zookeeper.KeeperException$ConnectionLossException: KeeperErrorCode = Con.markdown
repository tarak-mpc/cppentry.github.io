---
layout:     post
title:      HBase shell 启动出错 org.apache.zookeeper.KeeperException$ConnectionLossException: KeeperErrorCode = Con
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>一般发生在长期使用hbase，后面再安装之后直接使用hbase shell 想要进入hbase界面，却没有启动start-hbase.sh而导致的。<br></p>
<p><br></p>
<p>[root@master local]# hbase shell</p>
SLF4J: Class path contains multiple SLF4J bindings.<br>
SLF4J: Found binding in [jar:file:/usr/local/hbase/lib/slf4j-log4j12-1.7.7.jar!/org/slf4j/impl/StaticLoggerBinder.class]<br>
SLF4J: Found binding in [jar:file:/usr/local/hadoop2/share/hadoop/common/lib/slf4j-log4j12-1.7.10.jar!/org/slf4j/impl/StaticLoggerBinder.class]<br>
SLF4J: See http://www.slf4j.org/codes.html#multiple_bindings for an explanation.<br>
SLF4J: Actual binding is of type [org.slf4j.impl.Log4jLoggerFactory]<br>
2015-05-17 23:11:37,482 ERROR [main] zookeeper.RecoverableZooKeeper: ZooKeeper exists failed after 4 attempts<br>
2015-05-17 23:11:37,484 WARN  [main] zookeeper.ZKUtil: hconnection-0x1623e7c40x0, quorum=master:2181, baseZNode=/hbase Unable to set watcher on znode (/hbase/hbaseid)<br>
org.apache.zookeeper.KeeperException$ConnectionLossException: KeeperErrorCode = ConnectionLoss for /hbase/hbaseid<br>
    at org.apache.zookeeper.KeeperException.create(KeeperException.java:99)<br>
    at org.apache.zookeeper.KeeperException.create(KeeperException.java:51)<br>
    at org.apache.zookeeper.ZooKeeper.exists(ZooKeeper.java:1045)<br>
    at org.apache.hadoop.hbase.zookeeper.RecoverableZooKeeper.exists(RecoverableZooKeeper.java:222)<br>
    at org.apache.hadoop.hbase.zookeeper.ZKUtil.checkExists(ZKUtil.java:481)<br>
    at org.apache.hadoop.hbase.zookeeper.ZKClusterId.readClusterIdZNode(ZKClusterId.java:65)<br>
    at org.apache.hadoop.hbase.client.ZooKeeperRegistry.getClusterId(ZooKeeperRegistry.java:86)<br>
    at org.apache.hadoop.hbase.client.ConnectionManager$HConnectionImplementation.retrieveClusterId(ConnectionManager.java:833)<br>
    at org.apache.hadoop.hbase.client.ConnectionManager$HConnectionImplementation.&lt;init&gt;(ConnectionManager.java:623)<br>
    at sun.reflect.NativeConstructorAccessorImpl.newInstance0(Native Method)<br>
    at sun.reflect.NativeConstructorAccessorImpl.newInstance(NativeConstructorAccessorImpl.java:57)<br>
    at sun.reflect.DelegatingConstructorAccessorImpl.newInstance(DelegatingConstructorAccessorImpl.java:45)<br>
    at java.lang.reflect.Constructor.newInstance(Constructor.java:526)<br>
    at org.apache.hadoop.hbase.client.ConnectionFactory.createConnection(ConnectionFactory.java:238)<br>
    at org.apache.hadoop.hbase.client.ConnectionFactory.createConnection(ConnectionFactory.java:218)<br>
    at org.apache.hadoop.hbase.client.ConnectionFactory.createConnection(ConnectionFactory.java:119)<br>
    at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)<br>
    at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:57)<br>
    at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)<br>
    at java.lang.reflect.Method.invoke(Method.java:606)<br>
    at org.jruby.javasupport.JavaMethod.invokeDirectWithExceptionHandling(JavaMethod.java:450)<br>
    at org.jruby.javasupport.JavaMethod.invokeStaticDirect(JavaMethod.java:362)<br>
    at org.jruby.java.invokers.StaticMethodInvoker.call(StaticMethodInvoker.java:58)<br>
    at org.jruby.runtime.callsite.CachingCallSite.cacheAndCall(CachingCallSite.java:312)<br>
    at org.jruby.runtime.callsite.CachingCallSite.call(CachingCallSite.java:169)<br>
    at org.jruby.ast.CallOneArgNode.interpret(CallOneArgNode.java:57)<br>
    at org.jruby.ast.InstAsgnNode.interpret(InstAsgnNode.java:95)<br>
    at org.jruby.ast.NewlineNode.interpret(NewlineNode.java:104)<br>
    at org.jruby.ast.BlockNode.interpret(BlockNode.java:71)<br>
    at org.jruby.evaluator.ASTInterpreter.INTERPRET_METHOD(ASTInterpreter.java:74)<br>
    at org.jruby.internal.runtime.methods.InterpretedMethod.call(InterpretedMethod.java:169)<br>
    at org.jruby.internal.runtime.methods.DefaultMethod.call(DefaultMethod.java:191)<br>
    at org.jruby.runtime.callsite.CachingCallSite.cacheAndCall(CachingCallSite.java:302)<br>
    at org.jruby.runtime.callsite.CachingCallSite.callBlock(CachingCallSite.java:144)<br>
    at org.jruby.runtime.callsite.CachingCallSite.call(CachingCallSite.java:148)<br>
    at org.jruby.RubyClass.newInstance(RubyClass.java:822)<br>
    at org.jruby.RubyClass$i$newInstance.call(RubyClass$i$newInstance.gen:65535)<br>
    at org.jruby.internal.runtime.methods.JavaMethod$JavaMethodZeroOrNBlock.call(JavaMethod.java:249)<br>
    at org.jruby.runtime.callsite.CachingCallSite.cacheAndCall(CachingCallSite.java:292)<br>
    at org.jruby.runtime.callsite.CachingCallSite.call(CachingCallSite.java:135)<br>
    at usr.local.hbase.bin.hirb.__file__(/usr/local/hbase/bin/hirb.rb:118)<br>
    at usr.local.hbase.bin.hirb.load(/usr/local/hbase/bin/hirb.rb)<br>
    at org.jruby.Ruby.runScript(Ruby.java:697)<br>
    at org.jruby.Ruby.runScript(Ruby.java:690)<br>
    at org.jruby.Ruby.runNormally(Ruby.java:597)<br>
    at org.jruby.Ruby.runFromMain(Ruby.java:446)<br>
    at org.jruby.Main.doRunFromMain(Main.java:369)<br>
    at org.jruby.Main.internalRun(Main.java:258)<br>
    at org.jruby.Main.run(Main.java:224)<br>
    at org.jruby.Main.run(Main.java:208)<br>
    at org.jruby.Main.main(Main.java:188)<br>
2015-05-17 23:11:37,492 ERROR [main] zookeeper.ZooKeeperWatcher: hconnection-0x1623e7c40x0, quorum=master:2181, baseZNode=/hbase Received unexpected KeeperException, re-throwing exception<br>
org.apache.zookeeper.KeeperException$ConnectionLossException: KeeperErrorCode = ConnectionLoss for /hbase/hbaseid<br>
    at org.apache.zookeeper.KeeperException.create(KeeperException.java:99)<br>
    at org.apache.zookeeper.KeeperException.create(KeeperException.java:51)<br>
    at org.apache.zookeeper.ZooKeeper.exists(ZooKeeper.java:1045)<br>
    at org.apache.hadoop.hbase.zookeeper.RecoverableZooKeeper.exists(RecoverableZooKeeper.java:222)<br>
    at org.apache.hadoop.hbase.zookeeper.ZKUtil.checkExists(ZKUtil.java:481)<br>
    at org.apache.hadoop.hbase.zookeeper.ZKClusterId.readClusterIdZNode(ZKClusterId.java:65)<br>
    at org.apache.hadoop.hbase.client.ZooKeeperRegistry.getClusterId(ZooKeeperRegistry.java:86)<br>
    at org.apache.hadoop.hbase.client.ConnectionManager$HConnectionImplementation.retrieveClusterId(ConnectionManager.java:833)<br>
    at org.apache.hadoop.hbase.client.ConnectionManager$HConnectionImplementation.&lt;init&gt;(ConnectionManager.java:623)<br>
    at sun.reflect.NativeConstructorAccessorImpl.newInstance0(Native Method)<br>
    at sun.reflect.NativeConstructorAccessorImpl.newInstance(NativeConstructorAccessorImpl.java:57)<br>
    at sun.reflect.DelegatingConstructorAccessorImpl.newInstance(DelegatingConstructorAccessorImpl.java:45)<br>
    at java.lang.reflect.Constructor.newInstance(Constructor.java:526)<br>
    at org.apache.hadoop.hbase.client.ConnectionFactory.createConnection(ConnectionFactory.java:238)<br>
    at org.apache.hadoop.hbase.client.ConnectionFactory.createConnection(ConnectionFactory.java:218)<br>
    at org.apache.hadoop.hbase.client.ConnectionFactory.createConnection(ConnectionFactory.java:119)<br>
    at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)<br>
    at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:57)<br>
    at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)<br>
    at java.lang.reflect.Method.invoke(Method.java:606)<br>
    at org.jruby.javasupport.JavaMethod.invokeDirectWithExceptionHandling(JavaMethod.java:450)<br>
    at org.jruby.javasupport.JavaMethod.invokeStaticDirect(JavaMethod.java:362)<br>
    at org.jruby.java.invokers.StaticMethodInvoker.call(StaticMethodInvoker.java:58)<br>
    at org.jruby.runtime.callsite.CachingCallSite.cacheAndCall(CachingCallSite.java:312)<br>
    at org.jruby.runtime.callsite.CachingCallSite.call(CachingCallSite.java:169)<br>
    at org.jruby.ast.CallOneArgNode.interpret(CallOneArgNode.java:57)<br>
    at org.jruby.ast.InstAsgnNode.interpret(InstAsgnNode.java:95)<br>
    at org.jruby.ast.NewlineNode.interpret(NewlineNode.java:104)<br>
    at org.jruby.ast.BlockNode.interpret(BlockNode.java:71)<br>
    at org.jruby.evaluator.ASTInterpreter.INTERPRET_METHOD(ASTInterpreter.java:74)<br>
    at org.jruby.internal.runtime.methods.InterpretedMethod.call(InterpretedMethod.java:169)<br>
    at org.jruby.internal.runtime.methods.DefaultMethod.call(DefaultMethod.java:191)<br>
    at org.jruby.runtime.callsite.CachingCallSite.cacheAndCall(CachingCallSite.java:302)<br>
    at org.jruby.runtime.callsite.CachingCallSite.callBlock(CachingCallSite.java:144)<br>
    at org.jruby.runtime.callsite.CachingCallSite.call(CachingCallSite.java:148)<br>
    at org.jruby.RubyClass.newInstance(RubyClass.java:822)<br>
    at org.jruby.RubyClass$i$newInstance.call(RubyClass$i$newInstance.gen:65535)<br>
    at org.jruby.internal.runtime.methods.JavaMethod$JavaMethodZeroOrNBlock.call(JavaMethod.java:249)<br>
    at org.jruby.runtime.callsite.CachingCallSite.cacheAndCall(CachingCallSite.java:292)<br>
    at org.jruby.runtime.callsite.CachingCallSite.call(CachingCallSite.java:135)<br>
    at usr.local.hbase.bin.hirb.__file__(/usr/local/hbase/bin/hirb.rb:118)<br>
    at usr.local.hbase.bin.hirb.load(/usr/local/hbase/bin/hirb.rb)<br>
    at org.jruby.Ruby.runScript(Ruby.java:697)<br>
    at org.jruby.Ruby.runScript(Ruby.java:690)<br>
    at org.jruby.Ruby.runNormally(Ruby.java:597)<br>
    at org.jruby.Ruby.runFromMain(Ruby.java:446)<br>
    at org.jruby.Main.doRunFromMain(Main.java:369)<br>
    at org.jruby.Main.internalRun(Main.java:258)<br>
    at org.jruby.Main.run(Main.java:224)<br>
    at org.jruby.Main.run(Main.java:208)<br>
    at org.jruby.Main.main(Main.java:188)<br>
HBase Shell; enter 'help&lt;RETURN&gt;' for list of supported commands.<br>
Type "exit&lt;RETURN&gt;" to leave the HBase Shell<br><p>Version 1.0.1, r66a93c09df3b12ff7b86c39bc8475c60e15af82d, Fri Apr 17 22:14:06 PDT 2015</p>
<p><br></p>
<p><br></p>
<p>原因 没有启动HBase 需要运行 start-hbase.sh 才可以。<br></p>
            </div>
                </div>