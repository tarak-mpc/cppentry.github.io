---
layout:     post
title:      JDBC连接Hive（包括Maven配置和本地jar配置）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2 id="查看服务器hive和hadoop版本">查看服务器hive和hadoop版本</h2>

<p>可以使用hadoop version和hive命令查看hadoop版本</p>

<pre>[gc-user@hadoop-hive01 hadoop]$ hadoop version
Hadoop 2.6.0-cdh5.9.0
Subversion http://github.com/cloudera/hadoop -r 1c8ae0d951319fea693402c9f82449447fd27b07
Compiled by jenkins on 2016-10-21T08:10Z
Compiled with protoc 2.5.0
From source with checksum 5448863f1e597b97d9464796b0a451
This command was run using /usr/lib/hadoop/hadoop-common-2.6.0-cdh5.9.0.jar
[gc-user@hadoop-hive01 hadoop]$ hive
2017-08-01 16:43:28,717 WARN  [main] mapreduce.TableMapReduceUtil: The hbase-prefix-tree module jar containing PrefixTreeCodec is not present.  Continuing without it.

Logging initialized using configuration in jar:file:/usr/lib/hive/lib/hive-exec-1.1.0-cdh5.9.0.jar!/hive-log4j.properties
WARNING: Hive CLI is deprecated and migration to Beeline is recommended.
hive&gt; 
</pre>



<h2 id="jar依赖包">jar依赖包</h2>

<p>所依赖的jar包，一共有以下几个</p>

<pre>$HADOOP_HOME/hadoop-common-${hadoop_version}.jar
$HIVE_HOME/lib/hive-exec-${hive_version}.jar 
$HIVE_HOME/lib/hive-jdbc-${hive_version}.jar 
$HIVE_HOME/lib/hive-metastore-${hive_version}.jar  
$HIVE_HOME/lib/hive-service-${hive_version}.jar   
$HIVE_HOME/lib/libfb303-0.9.3.jar  
$HIVE_HOME/lib/commons-logging-1.1.3.jar 
httpcore-4.2.5.jar
httpclient-4.2.5.jar
slf4j-api-1.6.1.jar
</pre>



<h2 id="maven配置">maven配置</h2>



<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.apache.hadoop<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>hadoop-common<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>2.6.0<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>

        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.apache.hive<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>hive-jdbc<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>1.1.0<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span></code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>