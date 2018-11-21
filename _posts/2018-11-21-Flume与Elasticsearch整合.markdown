---
layout:     post
title:      Flume与Elasticsearch整合
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/u010022051/article/details/50515725				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>在Flume1.6中的ElasticSearchSink支持Flume与Elasticsearch的整合，可以将Flume采集的数据传输到Elasticsearch中，其主要配置项如下：<br></p>
<p><img src="https://img-blog.csdn.net/20160114111646133?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p>实现过程：</p>
<p>JDK版本：1.7.0_79</p>
<p>Elasticsearch版本：2.1.1</p>
<p>Flume版本：1.6</p>
<p>在flume的配置文件目录下添加如下配置文件</p>
<p>vim es_log.conf</p>
<p>agent.sources = tail<br><br></p>
<p>agent.channels = memoryChannel<br>
agent.channels.memoryChannel.type = memory<br><br>
agent.sources.tail.channels = memoryChannel<br>
agent.sources.tail.type = spooldir<br>
agent.sources.tail.spoolDir = /home/elk/es_log.log<br>
agent.sources.tail.fileHeader = true<br><br>
com.frontier45.flume.sink.elasticsearch2.ElasticSearchSink<br>
agent.sinks = elasticsearch<br>
agent.sinks.elasticsearch.channel = memoryChannel<br>
agent.sinks.elasticsearch.type=org.apache.flume.sink.elasticsearch.ElasticSearchSink<br>
agent.sinks.elasticsearch.batchSize=100<br>
agent.sinks.elasticsearch.hostNames=172.26.40.74:9300,172.26.40.75:9300,172.26.40.76:9300,172.27.40.77:9300,172.28.40.78:9300<br>
agent.sinks.k1.indexType = bar_type<br>
agent.sinks.elasticsearch.indexName=logstash<br>
agent.sinks.elasticsearch.clusterName=elk<br>
agent.sinks.elasticsearch.serializer=org.apache.flume.sink.elasticsearch.ElasticSearchLogStashEventSerializer<br>
com.frontier45.flume.sink.elasticsearch2.ElasticSearchDynamicSerializer<br></p>
<p><br></p>
<p>新建数据文件目录</p>
<p>在Flume安装目录下执行如下命令启动Flume</p>
<p>bin/flume-ng agent -c /home/elk/apache-flume-1.6.0-bin/conf -f /home/elk/apache-flume-1.6.0-bin/conf/es_log.conf -n agent -Dflume.root.logger=INFO,console<br></p>
<p>错误一：<br>
2016-01-11 14:46:32,260 (conf-file-poller-0) [ERROR - org.apache.flume.sink.elasticsearch.ElasticSearchSink.configure(ElasticSearchSink.java:302)] Could not instantiate event serializer.<br>
java.lang.ClassNotFoundException: org.apache.flume.sink.elasticsearch.ElasticSearchLogStashEventSerializer<br>
        at java.net.URLClassLoader$1.run(URLClassLoader.java:366)<br>
        at java.net.URLClassLoader$1.run(URLClassLoader.java:355)<br>
        at java.security.AccessController.doPrivileged(Native Method)<br>
        at java.net.URLClassLoader.findClass(URLClassLoader.java:354)<br>
        at java.lang.ClassLoader.loadClass(ClassLoader.java:425)<br>
        at sun.misc.Launcher$AppClassLoader.loadClass(Launcher.java:308)<br>
        at java.lang.ClassLoader.loadClass(ClassLoader.java:358)<br>
        at java.lang.Class.forName0(Native Method)<br>
        at java.lang.Class.forName(Class.java:191)<br>
        at org.apache.flume.sink.elasticsearch.ElasticSearchSink.configure(ElasticSearchSink.java:286)<br>
        at org.apache.flume.conf.Configurables.configure(Configurables.java:41)<br>
        at org.apache.flume.node.AbstractConfigurationProvider.loadSinks(AbstractConfigurationProvider.java:413)<br>
        at org.apache.flume.node.AbstractConfigurationProvider.getConfiguration(AbstractConfigurationProvider.java:98)<br>
        at org.apache.flume.node.PollingPropertiesFileConfigurationProvider$FileWatcherRunnable.run(PollingPropertiesFileConfigurationProvider.java:140)<br>
        at java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:471)<br>
        at java.util.concurrent.FutureTask.runAndReset(FutureTask.java:304)<br>
        at java.util.concurrent.ScheduledThreadPoolExecutor$ScheduledFutureTask.access$301(ScheduledThreadPoolExecutor.java:178)<br>
        at java.util.concurrent.ScheduledThreadPoolExecutor$ScheduledFutureTask.run(ScheduledThreadPoolExecutor.java:293)<br>
        at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1145)<br>
        at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)<br>
        at java.lang.Thread.run(Thread.java:745)<br><span></span><br>
原因：<br>
缺少Elasticsearch中的依赖包<br><br><br>
解决方案：<br>
1. 将Elasticsearch中lib下的jar包导入到Flume的lib下<br>
2. 在环境变量中引入Elasticsearch的依赖：<br><span></span>FLUME_CLASSPATH="/home/elk/elasticsearch-2.1.1/lib/*"<br></p>
<p><br></p>
<p><br></p>
<p><br></p>
<p>错误二：<br>
2016-01-11 14:52:29,899 (lifecycleSupervisor-1-3) [ERROR - org.apache.flume.lifecycle.LifecycleSupervisor$MonitorRunnable.run(LifecycleSupervisor.java:253)] Unable to start SinkRunner: { policy:org.apache.flume.sink.DefaultSinkProcessor@1c9f6ece counterGroup:{
 name:null counters:{} } } - Exception follows.<br>
java.lang.NoSuchMethodError: org.elasticsearch.common.transport.InetSocketTransportAddress.&lt;init&gt;(Ljava/lang/String;I)V<br>
        at org.apache.flume.sink.elasticsearch.client.ElasticSearchTransportClient.configureHostnames(ElasticSearchTransportClient.java:143)<br>
        at org.apache.flume.sink.elasticsearch.client.ElasticSearchTransportClient.&lt;init&gt;(ElasticSearchTransportClient.java:77)<br>
        at org.apache.flume.sink.elasticsearch.client.ElasticSearchClientFactory.getClient(ElasticSearchClientFactory.java:48)<br>
        at org.apache.flume.sink.elasticsearch.ElasticSearchSink.start(ElasticSearchSink.java:357)<br>
        at org.apache.flume.sink.DefaultSinkProcessor.start(DefaultSinkProcessor.java:46)<br>
        at org.apache.flume.SinkRunner.start(SinkRunner.java:79)<br>
        at org.apache.flume.lifecycle.LifecycleSupervisor$MonitorRunnable.run(LifecycleSupervisor.java:251)<br>
        at java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:471)<br>
        at java.util.concurrent.FutureTask.runAndReset(FutureTask.java:304)<br>
        at java.util.concurrent.ScheduledThreadPoolExecutor$ScheduledFutureTask.access$301(ScheduledThreadPoolExecutor.java:178)<br>
        at java.util.concurrent.ScheduledThreadPoolExecutor$ScheduledFutureTask.run(ScheduledThreadPoolExecutor.java:293)<br>
        at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1145)<br>
        at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)<br>
        at java.lang.Thread.run(Thread.java:745)<br></p>
<p>原因：</p>
<p>Elasticsearch的版本过高，导致Flume的jar包与Elasticsearch不兼容</p>
<p>解决方案：重置Elasticsearch版本至1.7.1</p>
<p>也可参照如下两篇文章的解决方案：</p>
<p>http://stackoverflow.com/questions/33732193/configure-sink-elasticsearch-apache-flume<br></p>
<p>https://github.com/elastic/elasticsearch/issues/14187<br></p>
<p><br></p>
<p><br></p>
            </div>
                </div>