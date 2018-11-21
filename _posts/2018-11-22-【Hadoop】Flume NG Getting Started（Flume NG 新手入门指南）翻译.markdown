---
layout:     post
title:      【Hadoop】Flume NG Getting Started（Flume NG 新手入门指南）翻译
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h1 style="font-family:verdana,Arial,Helvetica,sans-serif">新手入门</h1>
<ul style="margin-left:0px; padding-left:40px; font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
<li style="margin-left:0px; padding-left:0px; list-style-type:none">
<ul style="margin-left:0px; padding-left:40px">
<li style="margin-left:0px; padding-left:0px">Flume NG是什么？
<ul style="margin-left:0px; padding-left:40px">
<li style="margin-left:0px; padding-left:0px">有什么改变？</li></ul>
</li><li style="margin-left:0px; padding-left:0px">获得Flume NG</li></ul>
</li><li style="margin-left:0px; padding-left:0px; list-style-type:none">
<ul style="margin-left:0px; padding-left:40px">
<li style="margin-left:0px; padding-left:0px; list-style-type:none">
<ul style="margin-left:0px; padding-left:40px">
<li style="margin-left:0px; padding-left:0px">从源码构建</li></ul>
</li><li class="_mce_act_on" style="margin-left:0px; padding-left:0px">配置</li></ul>
<ul style="margin-left:0px; padding-left:40px">
<li style="margin-left:0px; padding-left:0px; list-style-type:none">
<ul style="margin-left:0px; padding-left:40px">
<li style="margin-left:0px; padding-left:0px">flume-ng全局选项</li><li style="margin-left:0px; padding-left:0px">flume-ng agent选项</li><li style="margin-left:0px; padding-left:0px">flume-ng avro-client 选项</li></ul>
</li><li style="margin-left:0px; padding-left:0px">提供反馈</li></ul>
</li></ul>
<h1 style="font-family:verdana,Arial,Helvetica,sans-serif">Flume NG是什么？</h1>
<p style="margin:10px auto; font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
Flume NG的目标是比Flume OG在简单性，大小和容易部署上有显著性地提高。为了实现这个目标，Flume NG将不会兼容Flume OG.我们目前在征求那些对测试Flume NG正确性、是否易用以及与其他系统的融合度有兴趣的用户的反馈信息。</p>
<h2 style="font-family:verdana,Arial,Helvetica,sans-serif">有什么改变？</h2>
<p style="margin:10px auto; font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
Flume NG（下一代）是在与Flume OG基础理念相同的情况下的一种大相径庭的实现。如果你已经对Flume很熟悉了，那么下面就是你需要知道内容。</p>
<ul style="margin-left:0px; padding-left:40px; font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
<li style="margin-left:0px; padding-left:0px">你仍然需要有sources和sinks组件来做相同的事，只是现在用channels来连接。</li><li style="margin-left:0px; padding-left:0px">Channels是可插拔的和持久的。Flume NG中in-memory channel处理速度快但不支持持久化而file-based channel支持event持久化。</li><li style="margin-left:0px; padding-left:0px">There's no more logical or physical nodes. We call all physical nodes <em>agents</em> and agents can run zero or more sources and sinks.现在没有逻辑节点或者物理节点。我们称物理节点为agents而agents中可以运行0至多个sources和sinks。</li><li style="margin-left:0px; padding-left:0px">没有master和不再依赖ZooKeeper。现在，Flume是依赖于一个简单的基于文件配置配置系统。</li><li style="margin-left:0px; padding-left:0px">所有的都是插件，一些面向用户，一些针对工具和系统开发者。插件化组件包括channels、sources、sinks、interceptors、sink processors和event serializeres。</li></ul>
<p style="margin:10px auto; font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
请自行阅读<a target="_blank" href="https://issues.apache.org/jira/browse/FLUME" rel="nofollow">JIRAs</a>文件寻找你认为重要的特性。</p>
<h1 style="font-family:verdana,Arial,Helvetica,sans-serif">获得Flume NG</h1>
<p style="margin:10px auto; font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
你可以在Flume官网中 <a target="_blank" href="http://flume.apache.org/download.html" rel="nofollow">Downloads</a> 下载源码。如果你不打算为Flume打补丁的话，那么使用二进制文件将是简单的方式。</p>
<h2 style="font-family:verdana,Arial,Helvetica,sans-serif">用源码构建</h2>
<p style="margin:10px auto; font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
为了用源码搭建Flume NG，你必须要有git、Sun JDK1.6，Apache Maven3.x，大概90MB的磁盘空间和网络连接。</p>
<p style="margin:10px auto; font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
1. 检查资源</p>
<p style="margin:10px auto; font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
</p>
<pre><code class="language-plain">$ git clone https://git-wip-us.apache.org/repos/asf/flume.git flume
$ cd flume
$ git checkout trunk</code></pre>
<p></p>
<p style="margin:10px auto; font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
2.编译项目</p>
<p style="margin:10px auto; font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
Apache Flume搭建时需要比默认配置更多的内存，我们建议你做以下的Maven 选项：</p>
<p style="margin:10px auto; font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
</p>
<pre><code class="language-plain">export MAVEN_OPTS="-Xms512m -Xmx1024m -XX:PermSize=256m -XX:MaxPermSize=512m"
# Build the code and run the tests (note: use mvn install, not mvn package, since we deploy Jenkins SNAPSHOT jars daily, and Flume is a multi-module project)
$ mvn install
# ...or build the code without running the tests
$ mvn install -DskipTests</code></pre>
<p></p>
<p style="margin:10px auto; font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
（需要说明的是Flume需要在构建路径下放置Google Protocol Buffers compiler来保证成功率。你可以根据<a target="_blank" href="https://developers.google.com/protocol-buffers/" rel="nofollow">here</a>里面的介绍来下载和安装。）</p>
<p style="margin:10px auto; font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
这将在flume-ng-dist/target目录下生成两种类型的包，它们是</p>
<ul style="margin-left:0px; padding-left:40px; font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
<li style="margin-left:0px; padding-left:0px">apache-flume-ng-dist-1.4.0-SNAPSHOT-bin.tar.gz - A binary distribution of Flume, ready to run.</li><li style="margin-left:0px; padding-left:0px">apache-flume-ng-dist-1.4.0-SNAPSHOT-src.tar.gz - A source-only distribution of Flume.</li></ul>
<p style="margin:10px auto; font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
如果你只是一个想要运行Flume的用户，你大概只需要-bin 版本。将它复制并解压，你就可以使用Flume了。</p>
<p style="margin:10px auto; font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
</p>
<pre><code class="language-plain">$ cp flume-ng-dist/target/apache-flume-1.4.0-SNAPSHOT-bin.tar.gz .
$ tar -zxvf apache-flume-1.4.0-SNAPSHOT-bin.tar.gz
$ cd apache-flume-1.4.0-SNAPSHOT-bin</code></pre>
<p></p>
<p style="margin:10px auto; font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
3.按照工作模板创建一个你自己的属性文档（或者从头开始创建一个）</p>
<p style="margin:10px auto; font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
</p>
<pre><code class="language-plain">$ cp conf/flume-conf.properties.template conf/flume.conf</code></pre>
<p></p>
<p style="margin:10px auto; font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
4.（可选）按照模板创建一个你自己的flume-env.sh文档（或者从头开始创建一个）。如果命令行中通过 –conf/-c指定了conf目录的话，那么fluem-ng会在该路径下搜寻“flume-env.sh”文档。一个使用flume-env.sh的情况是当你使用你自定义的Flume NG组件进行开发时通过JAVA_OPTS来指定调试或者评测选项。</p>
<p style="margin:10px auto; font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
</p>
<pre><code class="language-plain">$ cp conf/flume-env.sh.template conf/flume-env.sh</code></pre><br>
<p></p>
<p style="margin:10px auto; font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
5.配置和运行Flume NG</p>
<p style="margin:10px auto; font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
在你完成 Flume NG的配置之后，你可以通过 bin/flume-ng 可执行文件来运行它。这个脚本拥有一系列参数和模式。</p>
<h1 style="font-family:verdana,Arial,Helvetica,sans-serif">配置</h1>
<p style="margin:10px auto; font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
Flume使用基于Java属性文件格式的配置文档。运行agent时需要通过 –f&lt;file&gt;选项来告诉Flume。这个文件可以放在任意位置，但从历史和未来的角度来看，这个conf目录将会是配置文件的正确位置。</p>
<p style="margin:10px auto; font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
让我们从一个基础的例子开始吧。将下面复制并粘贴到conf/flume.conf中：</p>
<p style="margin:10px auto; font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
</p>
<pre><code class="language-plain"># Define a memory channel called ch1 on agent1
agent1.channels.ch1.type = memory
 
# Define an Avro source called avro-source1 on agent1 and tell it
# to bind to 0.0.0.0:41414. Connect it to channel ch1.
agent1.sources.avro-source1.channels = ch1
agent1.sources.avro-source1.type = avro
agent1.sources.avro-source1.bind = 0.0.0.0
agent1.sources.avro-source1.port = 41414
 
# Define a logger sink that simply logs all events it receives
# and connect it to the other end of the same channel.
agent1.sinks.log-sink1.channel = ch1
agent1.sinks.log-sink1.type = logger
 
# Finally, now that we've defined all of our components, tell
# agent1 which ones we want to activate.
agent1.channels = ch1
agent1.sources = avro-source1
agent1.sinks = log-sink1</code></pre>
<p></p>
<p style="margin:10px auto; font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
这个例子创建了内存channel（一个不可靠和高效传输）将一个Avro RPC source,和一个logger sink连接在一起。Avro source接收到的任何event都会按照规划的路线传到ch1 channel中然后传递给logger sink。需要重点说明的是定义组件只是配置Flume的前半部分工作；它们必须配置在&lt;agent&gt;中被激活。多个source、channel和sink是可以被配置的，用空格隔开每个组件就可以。</p>
<p style="margin:10px auto; font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
至于所有的鞋机，请自行查看Javadoc中的<code>org.apache.flume.conf.properties.PropertiesFileConfigurationProvider类。</code></p>
<p style="margin:10px auto; font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
<code>这是当前已经实现的channels、sinks和sources的列表。每个插件都有其自己的选项和需要配置的属性，请自行阅读javadoc。</code></p>
<table border="1" cellpadding="0" style="margin:0px auto; border:1px solid rgb(192,192,192); border-collapse:collapse; font-family:verdana,Arial,Helvetica,sans-serif; width:924px; height:3524px">
<tbody>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p align="center" style="margin:10px auto"><strong>Component</strong></p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p align="center" style="margin:10px auto"><strong>Type</strong></p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p align="center" style="margin:10px auto"><strong>Description</strong></p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p align="center" style="margin:10px auto"><strong>Implementation Class</strong></p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Channel</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">memory</p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">In-memory, fast, non-durable event transport</p>
<p style="margin:10px auto">一个将event存储在内容中，快速传输但无法持久化的channel。</p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">MemoryChannel</p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Channel</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">file</p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">A channel for reading, writing, mapping, and manipulating a file</p>
<p style="margin:10px auto">一个对文件进行读、写、映射和操作的channel</p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">FileChannel</p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Channel</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">jdbc</p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">JDBC-based, durable event transport (Derby-based)</p>
<p style="margin:10px auto">基于JDBC，支持持久化的channel</p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">JDBCChannel</p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Channel</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">recoverablememory</p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">A durable channel implementation that uses the local file system for its storage</p>
<p style="margin:10px auto">一个使用本地文件系统实现持久化的channel</p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">RecoverableMemoryChannel</p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Channel</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">org.apache.flume.channel.PseudoTxnMemoryChannel</p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Mainly for testing purposes. Not meant for production use.</p>
<p style="margin:10px auto">用于测试，不用于生产</p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">PseudoTxnMemoryChannel</p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Channel</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto"><em>(custom type as FQCN)</em></p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Your own Channel impl.</p>
<p style="margin:10px auto">自定义channel</p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto"><em>(custom FQCN)</em></p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Source</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">avro</p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Avro Netty RPC event source</p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">AvroSource</p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Source</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">exec</p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Execute a long-lived Unix process and read from stdout</p>
<p style="margin:10px auto">执行一个长连接Unix进程并从标准输出设备读取数据</p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">ExecSource</p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Source</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">netcat</p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Netcat style TCP event source</p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">NetcatSource</p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Source</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">seq</p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Monotonically incrementing sequence generator event source</p>
<p style="margin:10px auto">单调递增序列发生器的事件source</p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">SequenceGeneratorSource</p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Source</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">org.apache.flume.source.StressSource</p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Mainly for testing purposes. Not meant for production use. Serves as a continuous source of events where each event has the same payload. The payload consists of some number of bytes (specified by <em>size</em> property, defaults
 to 500) where each byte has the signed value Byte.MAX_VALUE (0x7F, or 127).</p>
<p style="margin:10px auto">主要用于测试，不适合用于生产。用于接收每个拥有相同的有效负载的event。那有效负载包含一组字节（通过 size属性指定，默认为500）每个字节都是最大值（Byte.MAX_VALUE(0X7F或者127)）</p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">org.apache.flume.source.StressSource</p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Source</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">syslogtcp</p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto"> </p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">SyslogTcpSource</p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Source</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">syslogudp</p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto"> </p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">SyslogUDPSource</p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Source</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">org.apache.flume.source.avroLegacy.AvroLegacySource</p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto"> </p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">AvroLegacySource</p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Source</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">org.apache.flume.source.thriftLegacy.ThriftLegacySource</p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto"> </p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">ThriftLegacySource</p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Source</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">org.apache.flume.source.scribe.ScribeSource</p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto"> </p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">ScribeSource</p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Source</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto"><em>(custom type as FQCN)</em></p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Your own Source impl.</p>
<p style="margin:10px auto">自定义Source</p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto"><em>(custom FQCN)</em></p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Sink</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">hdfs</p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Writes all events received to HDFS (with support for rolling, bucketing, HDFS-200 append, and more)</p>
<p style="margin:10px auto">将所有接收到events写到HDFS（支持回滚，桶装和追加以及其他）</p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">HDFSEventSink</p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Sink</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">org.apache.flume.sink.hbase.HBaseSink</p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">A simple sink that reads events from a channel and writes them to HBase.</p>
<p style="margin:10px auto">一个简单的sink用于将从channel读到的数据写到HBase</p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">org.apache.flume.sink.hbase.HBaseSink</p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Sink</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">org.apache.flume.sink.hbase.AsyncHBaseSink</p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto"> </p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">org.apache.flume.sink.hbase.AsyncHBaseSink</p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Sink</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">logger</p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Log events at INFO level via configured logging subsystem (log4j by default)</p>
<p style="margin:10px auto">通过配置日志子系统将INFO级别的events打印出来。</p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">LoggerSink</p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Sink</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">avro</p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Sink that invokes a pre-defined Avro protocol method for all events it receives (when paired with an avro source, forms tiered collection)</p>
<p style="margin:10px auto">一个调用预先定义好的Avro protocol方法来处理接收的所有event的sink（与avro source配对，形成分层收集）</p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">AvroSink</p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Sink</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">file_roll</p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto"> </p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">RollingFileSink</p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Sink</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">irc</p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto"> </p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">IRCSink</p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Sink</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">null</p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">/dev/null for Flume - blackhole all events received</p>
<p style="margin:10px auto">event黑洞，有来无回</p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">NullSink</p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Sink</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto"><em>(custom type as FQCN)</em></p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Your own Sink impl.</p>
<p style="margin:10px auto">自定义sink</p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto"><em>(custom FQCN)</em></p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">ChannelSelector</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">replicating</p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto"> </p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">ReplicatingChannelSelector</p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">ChannelSelector</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">multiplexing</p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto"> </p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">MultiplexingChannelSelector</p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">ChannelSelector</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">(custom type)</p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Your own ChannelSelector impl.</p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto"><em>(custom FQCN)</em></p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">SinkProcessor</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">default</p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto"> </p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">DefaultSinkProcessor</p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">SinkProcessor</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">failover</p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto"> </p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">FailoverSinkProcessor</p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">SinkProcessor</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">load_balance</p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Provides the ability to load-balance flow over multiple sinks.</p>
<p style="margin:10px auto">当存在多个sink时实现负载均衡</p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">LoadBalancingSinkProcessor</p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">SinkProcessor</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto"><em>(custom type as FQCN)</em></p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Your own SinkProcessor impl.</p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto"><em>(custom FQCN)</em></p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Interceptor$Builder</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">host</p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto"> </p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">HostInterceptor$Builder</p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Interceptor$Builder</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">timestamp</p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">TimestampInterceptor</p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">TimestampInterceptor$Builder</p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Interceptor$Builder</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">static</p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto"> </p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">StaticInterceptor$Builder</p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Interceptor$Builder</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">regex_filter</p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto"> </p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">RegexFilteringInterceptor$Builder</p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Interceptor$Builder</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto"><em>(custom type as FQCN)</em></p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Your own Interceptor$Builder impl.</p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto"><em>(custom FQCN)</em></p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">EventSerializer$Builder</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">text</p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto"> </p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">BodyTextEventSerializer$Builder</p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">EventSerializer$Builder</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">avro_event</p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto"> </p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">FlumeEventAvroEventSerializer$Builder</p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">EventSerializer</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">org.apache.flume.sink.hbase.SimpleHbaseEventSerializer</p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto"> </p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">SimpleHbaseEventSerializer</p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">EventSerializer</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">org.apache.flume.sink.hbase.SimpleAsyncHbaseEventSerializer</p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto"> </p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">SimpleAsyncHbaseEventSerializer</p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">EventSerializer</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">org.apache.flume.sink.hbase.RegexHbaseEventSerializer</p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto"> </p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">RegexHbaseEventSerializer</p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">HbaseEventSerializer</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Custom implementation of serializer for HBaseSink. <br>
<em>(custom type as FQCN)</em></p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Your own HbaseEventSerializer impl. </p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto"><em>(custom FQCN)</em></p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">AsyncHbaseEventSerializer</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Custom implementation of serializer for AsyncHbase sink. <br>
<em>(custom type as FQCN)</em></p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Your own AsyncHbaseEventSerializer impl.</p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto"><em>(custom FQCN)</em></p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">EventSerializer$Builder</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Custom implementation of serializer for all sinks except for HBaseSink and AsyncHBaseSink. <br>
<em>(custom type as FQCN)</em></p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Your own EventSerializer$Builder impl.</p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto"><em>(custom FQCN)</em></p>
</td>
</tr>
</tbody>
</table>
<p style="margin:10px auto; font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
flume-ng可执行可以让你运行一个Flume NG agent或者一个 Avro 客户端用于测试和实验。无论怎样，你必须指定一个命令（例如 agent或者avro-client）和一个conf目录（--conf&lt;conf dir&gt;）。所有其他的选项都可以用命令行指定。</p>
<p style="margin:10px auto; font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
使用上面的fiume.conf来启动flume服务器</p>
<p style="margin:10px auto; font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
</p>
<pre><code class="language-plain">bin/flume-ng agent --conf ./conf/ -f conf/flume.conf -Dflume.root.logger=DEBUG,console -n agent1</code></pre>
<p></p>
<p style="margin:10px auto; font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
需要注意的是agent 的名字是通过 –n agent1来指定的并且必须和-conf/flume.conf中给定的名字相匹配。</p>
<p style="margin:10px auto; font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
你的输出应该是这样的：</p>
<p style="margin:10px auto; font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
</p>
<pre><code class="language-plain">$ bin/flume-ng agent --conf conf/ -f conf/flume.conf -n agent1
2012-03-16 16:36:11,918 (main) [INFO - org.apache.flume.lifecycle.LifecycleSupervisor.start(LifecycleSupervisor.java:58)] Starting lifecycle supervisor 1
2012-03-16 16:36:11,921 (main) [INFO - org.apache.flume.node.FlumeNode.start(FlumeNode.java:54)] Flume node starting - agent1
2012-03-16 16:36:11,926 (lifecycleSupervisor-1-0) [INFO - org.apache.flume.node.nodemanager.DefaultLogicalNodeManager.start(DefaultLogicalNodeManager.java:110)] Node manager starting
2012-03-16 16:36:11,928 (lifecycleSupervisor-1-0) [INFO - org.apache.flume.lifecycle.LifecycleSupervisor.start(LifecycleSupervisor.java:58)] Starting lifecycle supervisor 10
2012-03-16 16:36:11,929 (lifecycleSupervisor-1-0) [DEBUG - org.apache.flume.node.nodemanager.DefaultLogicalNodeManager.start(DefaultLogicalNodeManager.java:114)] Node manager started
2012-03-16 16:36:11,926 (lifecycleSupervisor-1-1) [INFO - org.apache.flume.conf.file.AbstractFileConfigurationProvider.start(AbstractFileConfigurationProvider.java:67)] Configuration provider starting
2012-03-16 16:36:11,930 (lifecycleSupervisor-1-1) [DEBUG - org.apache.flume.conf.file.AbstractFileConfigurationProvider.start(AbstractFileConfigurationProvider.java:87)] Configuration provider started
2012-03-16 16:36:11,930 (conf-file-poller-0) [DEBUG - org.apache.flume.conf.file.AbstractFileConfigurationProvider$FileWatcherRunnable.run(AbstractFileConfigurationProvider.java:189)] Checking file:conf/flume.conf for changes
2012-03-16 16:36:11,931 (conf-file-poller-0) [INFO - org.apache.flume.conf.file.AbstractFileConfigurationProvider$FileWatcherRunnable.run(AbstractFileConfigurationProvider.java:196)] Reloading configuration file:conf/flume.conf
2012-03-16 16:36:11,936 (conf-file-poller-0) [DEBUG - org.apache.flume.conf.properties.FlumeConfiguration$AgentConfiguration.isValid(FlumeConfiguration.java:225)] Starting validation of configuration for agent: agent1, initial-configuration: AgentConfiguration[agent1]
SOURCES: {avro-source1=ComponentConfiguration[avro-source1]
  CONFIG: {port=41414, channels=ch1, type=avro, bind=0.0.0.0}
  RUNNER:   ComponentConfiguration[runner]
    CONFIG: {}
 
 
}
CHANNELS: {ch1=ComponentConfiguration[ch1]
  CONFIG: {type=memory}
 
}
SINKS: {log-sink1=ComponentConfiguration[log-sink1]
  CONFIG: {type=logger, channel=ch1}
  RUNNER:   ComponentConfiguration[runner]
    CONFIG: {}
 
 
}
2012-03-16 16:36:11,936 (conf-file-poller-0) [INFO - org.apache.flume.conf.properties.FlumeConfiguration.validateConfiguration(FlumeConfiguration.java:119)] Post-validation flume configuration contains configuation  for agents: [agent1]
2012-03-16 16:36:11,937 (conf-file-poller-0) [DEBUG - org.apache.flume.channel.DefaultChannelFactory.create(DefaultChannelFactory.java:67)] Creating instance of channel ch1 type memory
2012-03-16 16:36:11,944 (conf-file-poller-0) [DEBUG - org.apache.flume.source.DefaultSourceFactory.create(DefaultSourceFactory.java:73)] Creating instance of source avro-source1, type avro
2012-03-16 16:36:11,957 (conf-file-poller-0) [INFO - org.apache.flume.sink.DefaultSinkFactory.create(DefaultSinkFactory.java:69)] Creating instance of sink log-sink1 typelogger
2012-03-16 16:36:11,963 (conf-file-poller-0) [INFO - org.apache.flume.node.nodemanager.DefaultLogicalNodeManager.onNodeConfigurationChanged(DefaultLogicalNodeManager.java:52)] Node configuration change:{ sourceRunners:{avro-source1=EventDrivenSourceRunner: { source:AvroSource: { bindAddress:0.0.0.0 port:41414 } }} sinkRunners:{log-sink1=SinkRunner: { policy:org.apache.flume.sink.DefaultSinkProcessor@79f6f296 counterGroup:{ name:null counters:{} } }} channels:{ch1=org.apache.flume.channel.MemoryChannel@43b09468} }
2012-03-16 16:36:11,974 (lifecycleSupervisor-1-1) [INFO - org.apache.flume.source.AvroSource.start(AvroSource.java:122)] Avro source starting:AvroSource: { bindAddress:0.0.0.0 port:41414 }
2012-03-16 16:36:11,975 (Thread-1) [DEBUG - org.apache.flume.SinkRunner$PollingRunner.run(SinkRunner.java:123)] Polling sink runner starting
2012-03-16 16:36:12,352 (lifecycleSupervisor-1-1) [DEBUG - org.apache.flume.source.AvroSource.start(AvroSource.java:132)] Avro source started

</code></pre>
<p></p>
<h2 style="font-family:verdana,Arial,Helvetica,sans-serif">flume-ng 全局选项</h2>
<table border="1" cellpadding="0" style="margin:0px auto; border:1px solid rgb(192,192,192); border-collapse:collapse; font-family:verdana,Arial,Helvetica,sans-serif">
<tbody>
<tr>
<td width="190" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p align="center" style="margin:10px auto"><strong>Option</strong></p>
</td>
<td width="498" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p align="center" style="margin:10px auto"><strong>Description</strong></p>
</td>
</tr>
<tr>
<td width="190" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">--conf,-c &lt;conf&gt;</p>
</td>
<td width="498" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Use configs in &lt;conf&gt; directory</p>
</td>
</tr>
<tr>
<td width="190" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">--classpath,-C &lt;cp&gt;</p>
</td>
<td width="498" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Append to the classpath</p>
</td>
</tr>
<tr>
<td width="190" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">--dryrun,-d</p>
</td>
<td width="498" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Do not actually start Flume, just print the command</p>
</td>
</tr>
<tr>
<td width="190" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">-Dproperty=value</p>
</td>
<td width="498" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Sets a JDK system property value</p>
</td>
</tr>
</tbody>
</table>
<h2 style="font-family:verdana,Arial,Helvetica,sans-serif">flume-ng agent选项</h2>
<p style="margin:10px auto; font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
当给一个agent命令时，Flume NG agent将会根据一个给定的配置文件进行启动。</p>
<table border="1" cellpadding="0" style="margin:0px auto; border:1px solid rgb(192,192,192); border-collapse:collapse; font-family:verdana,Arial,Helvetica,sans-serif">
<tbody>
<tr>
<td width="190" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p align="center" style="margin:10px auto"><strong>Option</strong></p>
</td>
<td width="526" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p align="center" style="margin:10px auto"><strong>Description</strong></p>
</td>
</tr>
<tr>
<td width="190" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">--conf-file,-f &lt;file&gt;</p>
</td>
<td width="526" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Indicates which configuration file you want to run with (required)</p>
</td>
</tr>
<tr>
<td width="190" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">--name,-n &lt;agentname&gt;</p>
</td>
<td width="526" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Indicates the name of agent on which we're running (required)</p>
</td>
</tr>
</tbody>
</table>
<h2 style="font-family:verdana,Arial,Helvetica,sans-serif">flume-ng avro-client 选项</h2>
<p style="margin:10px auto; font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
运行一个Avro client从标准输入发送数据或文件到一个Flume NG Avro Source所监听的主机和端口上。</p>
<table border="1" cellpadding="0" style="margin:0px auto; border:1px solid rgb(192,192,192); border-collapse:collapse; font-family:verdana,Arial,Helvetica,sans-serif">
<tbody>
<tr>
<td style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p align="center" style="margin:10px auto"><strong>Option</strong></p>
</td>
<td style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p align="center" style="margin:10px auto"><strong>Description</strong></p>
</td>
</tr>
<tr>
<td style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">--host,-H &lt;hostname&gt;</p>
</td>
<td style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Specifies the hostname of the Flume agent (may be localhost)</p>
</td>
</tr>
<tr>
<td style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">--port,-p &lt;port&gt;</p>
</td>
<td style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Specifies the port on which the Avro source is listening</p>
</td>
</tr>
<tr>
<td style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">--filename,-F &lt;filename&gt;</p>
</td>
<td style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Sends each line of &lt;filename&gt; to Flume (optional)</p>
</td>
</tr>
<tr>
<td style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">--headerFile,-F &lt;file&gt;</p>
</td>
<td style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Header file containing headers as key/value pairs on each new line</p>
</td>
</tr>
</tbody>
</table>
<p style="margin:10px auto; font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
Avroclient将每一行以<code>\n</code>, <code>\r</code>, or <code>\r\n</code><code>结尾的数据当做一个event。把avro-clinet 命令当做Flume中cat命令。例如，下面的命令创建了一个event并将它发送到Flume’avro source所监听的端口41414。</code></p>
<p style="margin:10px auto; font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
<code>在一个新的窗口输入以下内容：</code></p>
<p style="margin:10px auto; font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
<code></code></p>
<pre><code class="language-plain">$ bin/flume-ng avro-client --conf conf -H localhost -p 41414 -F /etc/passwd -Dflume.root.logger=DEBUG,console</code></pre>
<p></p>
<p style="margin:10px auto; font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
你能看到以下信息：</p>
<p style="margin:10px auto; font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
</p>
<pre><code class="language-plain">2012-03-16 16:39:17,124 (main) [DEBUG - org.apache.flume.client.avro.AvroCLIClient.run(AvroCLIClient.java:175)] Finished
2012-03-16 16:39:17,127 (main) [DEBUG - org.apache.flume.client.avro.AvroCLIClient.run(AvroCLIClient.java:178)] Closing reader
2012-03-16 16:39:17,127 (main) [DEBUG - org.apache.flume.client.avro.AvroCLIClient.run(AvroCLIClient.java:183)] Closing transceiver
2012-03-16 16:39:17,129 (main) [DEBUG - org.apache.flume.client.avro.AvroCLIClient.main(AvroCLIClient.java:73)] Exiting</code></pre>
<p></p>
<p style="margin:10px auto; font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
服务器所运行的第一个窗口会打印以下内容：</p>
<p style="margin:10px auto; font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
</p>
<pre><code class="language-plain">2012-03-16 16:39:16,738 (New I/O server boss #1 ([id: 0x49e808ca, /0:0:0:0:0:0:0:0:41414])) [INFO - org.apache.avro.ipc.NettyServer$NettyServerAvroHandler.handleUpstream(NettyServer.java:123)] [id: 0x0b92a848, /1
27.0.0.1:39577 =&gt; /127.0.0.1:41414] OPEN
2012-03-16 16:39:16,742 (New I/O server worker #1-1) [INFO - org.apache.avro.ipc.NettyServer$NettyServerAvroHandler.handleUpstream(NettyServer.java:123)] [id: 0x0b92a848, /127.0.0.1:39577 =&gt; /127.0.0.1:41414] BOU
ND: /127.0.0.1:41414
2012-03-16 16:39:16,742 (New I/O server worker #1-1) [INFO - org.apache.avro.ipc.NettyServer$NettyServerAvroHandler.handleUpstream(NettyServer.java:123)] [id: 0x0b92a848, /127.0.0.1:39577 =&gt; /127.0.0.1:41414] CON
NECTED: /127.0.0.1:39577
2012-03-16 16:39:17,129 (New I/O server worker #1-1) [INFO - org.apache.avro.ipc.NettyServer$NettyServerAvroHandler.handleUpstream(NettyServer.java:123)] [id: 0x0b92a848, /127.0.0.1:39577 :&gt; /127.0.0.1:41414] DISCONNECTED
2012-03-16 16:39:17,129 (New I/O server worker #1-1) [INFO - org.apache.avro.ipc.NettyServer$NettyServerAvroHandler.handleUpstream(NettyServer.java:123)] [id: 0x0b92a848, /127.0.0.1:39577 :&gt; /127.0.0.1:41414] UNBOUND
2012-03-16 16:39:17,129 (New I/O server worker #1-1) [INFO - org.apache.avro.ipc.NettyServer$NettyServerAvroHandler.handleUpstream(NettyServer.java:123)] [id: 0x0b92a848, /127.0.0.1:39577 :&gt; /127.0.0.1:41414] CLOSED
2012-03-16 16:39:17,302 (Thread-1) [INFO - org.apache.flume.sink.LoggerSink.process(LoggerSink.java:68)] Event: { headers:{} body:[B@5c1ae90c }
2012-03-16 16:39:17,302 (Thread-1) [INFO - org.apache.flume.sink.LoggerSink.process(LoggerSink.java:68)] Event: { headers:{} body:[B@6aba4211 }
2012-03-16 16:39:17,302 (Thread-1) [INFO - org.apache.flume.sink.LoggerSink.process(LoggerSink.java:68)] Event: { headers:{} body:[B@6a47a0d4 }
2012-03-16 16:39:17,302 (Thread-1) [INFO - org.apache.flume.sink.LoggerSink.process(LoggerSink.java:68)] Event: { headers:{} body:[B@48ff4cf }
...</code></pre>
<p></p>
<p style="margin:10px auto; font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
恭喜！你的Apache Flume已经成功运行了。</p>
<h1 style="font-family:verdana,Arial,Helvetica,sans-serif">提供反馈</h1>
<p style="margin:10px auto; font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
对于帮助构建、配置和运行Flume来说，最好的地方就是用户的邮件列表。发送邮件到<a target="_blank" href="mailto:user-subscribe@flume.apache.org" rel="nofollow">user-subscribe@flume.apache.org</a> 进行订阅和一旦你订阅之后<a target="_blank" href="mailto:user@flume.apache.org" rel="nofollow">user@flume.apache.org</a> 会发送订阅信息给你。档案信息可以在 <a target="_blank" href="http://mail-archives.apache.org/mod_mbox/incubator-flume-user/" rel="nofollow">http://mail-archives.apache.org/mod_mbox/incubator-flume-user/</a> (2012/7建立)
 and <a target="_blank" href="http://mail-archives.apache.org/mod_mbox/incubator-flume-user/" rel="nofollow">http://mail-archives.apache.org/mod_mbox/incubator-flume-user/</a><a target="_blank" href="http://mail-archives.apache.org/mod_mbox/flume-user/" rel="nofollow">http://mail-archives.apache.org/mod_mbox/flume-user/</a> 得到。</p>
<p style="margin:10px auto; font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
如果你确认你发现一个bug或者需要一个特性或者提升，请不要害羞。去这个 <a target="_blank" href="https://issues.apache.org/jira/browse/FLUME" rel="nofollow">https://issues.apache.org/jira/browse/FLUME</a> 网站为该版本的Flume提一个JIRA.对于NG版本，请为合适的里程碑/发布留下“影响版本”的标识。可以只留下你对于未达之处的无法确定的任何想法。当我们需要的时候会像你征求细节。需要说明的是你必须创建一个Apache
 JIRA账户以致你可以提出问题。</p>
<p style="margin:10px auto; font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
 </p>
<p style="margin:10px auto; font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
<em>下面为原文</em></p>
<hr style="font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
<p style="margin:10px auto; font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
 </p>
<p style="margin:10px auto; font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
 </p>
<h1 style="font-family:verdana,Arial,Helvetica,sans-serif">Getting Started</h1>
<ul style="margin-left:0px; padding-left:40px; font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
<li style="margin-left:0px; padding-left:0px"><a target="_blank" href="file:///D:/BigData/%E5%AD%A6%E4%B9%A0%E8%B5%84%E6%96%99/Flume/Getting%20Started.docx#GettingStarted-WhatisFlumeNG?" rel="nofollow">What is Flume NG?</a>
<ul style="margin-left:0px; padding-left:40px">
<li style="margin-left:0px; padding-left:0px"><a target="_blank" href="file:///D:/BigData/%E5%AD%A6%E4%B9%A0%E8%B5%84%E6%96%99/Flume/Getting%20Started.docx#GettingStarted-What'%20rel=" nofollow>What's Changed?</a></li></ul>
</li><li style="margin-left:0px; padding-left:0px"><a target="_blank" href="file:///D:/BigData/%E5%AD%A6%E4%B9%A0%E8%B5%84%E6%96%99/Flume/Getting%20Started.docx#GettingStarted-GettingFlumeNG" rel="nofollow">Getting Flume NG</a>
<ul style="margin-left:0px; padding-left:40px">
<li style="margin-left:0px; padding-left:0px"><a target="_blank" href="file:///D:/BigData/%E5%AD%A6%E4%B9%A0%E8%B5%84%E6%96%99/Flume/Getting%20Started.docx#GettingStarted-BuildingFromSource" rel="nofollow">Building From Source</a></li></ul>
</li><li style="margin-left:0px; padding-left:0px"><a target="_blank" href="file:///D:/BigData/%E5%AD%A6%E4%B9%A0%E8%B5%84%E6%96%99/Flume/Getting%20Started.docx#GettingStarted-Configuration" rel="nofollow">Configuration</a> （
<ul style="margin-left:0px; padding-left:40px">
<li style="margin-left:0px; padding-left:0px"><a target="_blank" href="file:///D:/BigData/%E5%AD%A6%E4%B9%A0%E8%B5%84%E6%96%99/Flume/Getting%20Started.docx#GettingStarted-flume-ngglobaloptions" rel="nofollow">flume-ng global options</a></li><li style="margin-left:0px; padding-left:0px"><a target="_blank" href="file:///D:/BigData/%E5%AD%A6%E4%B9%A0%E8%B5%84%E6%96%99/Flume/Getting%20Started.docx#GettingStarted-flume-ngagentoptions" rel="nofollow">flume-ng agent options</a>（</li><li style="margin-left:0px; padding-left:0px"><a target="_blank" href="file:///D:/BigData/%E5%AD%A6%E4%B9%A0%E8%B5%84%E6%96%99/Flume/Getting%20Started.docx#GettingStarted-flume-ngavro-clientoptio" rel="nofollow">flume-ng avro-client options</a></li></ul>
</li><li style="margin-left:0px; padding-left:0px"><a target="_blank" href="file:///D:/BigData/%E5%AD%A6%E4%B9%A0%E8%B5%84%E6%96%99/Flume/Getting%20Started.docx#GettingStarted-ProvidingFeedback" rel="nofollow">Providing Feedback</a></li></ul>
<h1 style="font-family:verdana,Arial,Helvetica,sans-serif">What is Flume NG?</h1>
<p style="margin:10px auto; font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
Flume NG aims to be significantly simpler, smaller, and easier to deploy than Flume OG. In doing so, we do not commit to maintaining backward compatibility of Flume NG with Flume OG. We're currently soliciting feedback from those who are interested in testing
 Flume NG for correctness, ease of use, and potential integration with other systems.</p>
<h2 style="font-family:verdana,Arial,Helvetica,sans-serif">What's Changed?</h2>
<p style="margin:10px auto; font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
Flume NG (Next Generation) is a huge departure from Flume OG (Original Generation) in its implementation although many of the original concepts are the same. If you're already familiar with Flume, here's what you need to know.</p>
<ul style="margin-left:0px; padding-left:40px; font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
<li style="margin-left:0px; padding-left:0px">You still have <em>sources</em> and <em>sinks</em> and they still do the same thing. They are now connected by <em>channels</em>.</li><li style="margin-left:0px; padding-left:0px">Channels are pluggable and dictate durability. Flume NG ships with an in-memory channel for fast, but non-durable event delivery and a file-based channel for durable event delivery.</li><li style="margin-left:0px; padding-left:0px">There's no more logical or physical nodes. We call all physical nodes <em>agents</em> and agents can run zero or more sources and sinks.</li><li style="margin-left:0px; padding-left:0px">There's no master and no ZooKeeper dependency anymore. At this time, Flume runs with a simple file-based configuration system.</li><li style="margin-left:0px; padding-left:0px">Just about everything is a plugin, some end user facing, some for tool and system developers. Pluggable components include channels, sources, sinks, interceptors, sink processors, and event serializers.</li></ul>
<p style="margin:10px auto; font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
Please file <a target="_blank" href="https://issues.apache.org/jira/browse/FLUME" rel="nofollow">JIRAs</a> and/or vote for features you feel are important.</p>
<h1 style="font-family:verdana,Arial,Helvetica,sans-serif">Getting Flume NG</h1>
<p style="margin:10px auto; font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
Flume is available as a source tarball and binary on the <a target="_blank" href="http://flume.apache.org/download.html" rel="nofollow">Downloads</a> section of the Flume Website. If you are not planning on creating patches for Flume, the binary is likely
 the easiest way to get started.</p>
<h2 style="font-family:verdana,Arial,Helvetica,sans-serif">Building From Source</h2>
<p style="margin:10px auto; font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
To build Flume NG from source, you'll need git, the Sun JDK 1.6, Apache Maven 3.x, about 90MB of local disk space and an Internet connection.</p>
<p style="margin:10px auto; font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
1. Check out the source</p>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'>$ git clone https://git-wip-us.apache.org/repos/asf/flume.git flume</pre>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'>$ cd flume</pre>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'>$ git checkout trunk</pre>
<p style="margin:10px auto; font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
2. Compile the project</p>
<p style="margin:10px auto; font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
The Apache Flume build requires more memory than the default configuration. We recommend you set the following Maven options:</p>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'>export MAVEN_OPTS="-Xms512m -Xmx1024m -XX:PermSize=256m -XX:MaxPermSize=512m"</pre>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'> </pre>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'># Build the code and run the tests (note: use mvn install, not mvn package, since we deploy Jenkins SNAPSHOT jars daily, and Flume is a multi-module project)</pre>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'>$ mvn install</pre>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'># ...or build the code without running the tests</pre>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'>$ mvn install -DskipTests</pre>
<p style="margin:10px auto; font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
(Please note that Flume requires that Google Protocol Buffers compiler be in the path for the build to be successful. You download and install it by following the instructions <a target="_blank" href="https://developers.google.com/protocol-buffers/" rel="nofollow">here</a>.)</p>
<p style="margin:10px auto; font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
This produces two types of packages in flume-ng-dist/target. They are:</p>
<ul style="margin-left:0px; padding-left:40px; font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
<li style="margin-left:0px; padding-left:0px">apache-flume-ng-dist-1.4.0-SNAPSHOT-bin.tar.gz - A binary distribution of Flume, ready to run.</li><li style="margin-left:0px; padding-left:0px">apache-flume-ng-dist-1.4.0-SNAPSHOT-src.tar.gz - A source-only distribution of Flume.</li></ul>
<p style="margin:10px auto; font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
If you're a user and you just want to run Flume, you probably want the -bin version. Copy one out, decompress it, and you're ready to go.</p>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'> </pre>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'>$ cp flume-ng-dist/target/apache-flume-1.4.0-SNAPSHOT-bin.tar.gz .</pre>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'>$ tar -zxvf apache-flume-1.4.0-SNAPSHOT-bin.tar.gz</pre>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'>$ cd apache-flume-1.4.0-SNAPSHOT-bin</pre>
<p style="margin:10px auto; font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
3. Create your own properties file based on the working template (or create one from scratch)</p>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'> </pre>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'>$ cp conf/flume-conf.properties.template conf/flume.conf</pre>
<p style="margin:10px auto; font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
4. (Optional) Create your flume-env.sh file based on the template (or create one from scratch). The flume-ng executable looks for and sources a file named "flume-env.sh" in the conf directory specified by the --conf/-c commandline option. One use case for using
 flume-env.sh would be to specify debugging or profiling options via JAVA_OPTS when developing your own custom Flume NG components such as sources and sinks.</p>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'> </pre>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'>$ cp conf/flume-env.sh.template conf/flume-env.sh</pre>
<p style="margin:10px auto; font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
5. Configure and Run Flume NG</p>
<p style="margin:10px auto; font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
After you've configured Flume NG (see below), you can run it with the <code>bin/flume-ng</code> executable. This script has a number of arguments and modes.</p>
<h1 style="font-family:verdana,Arial,Helvetica,sans-serif">Configuration</h1>
<p style="margin:10px auto; font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
Flume uses a Java property file based configuration format. It is required that you tell Flume which file to use by way of the <code>-f &lt;file&gt;</code> option (see above) when running an agent. The file can live anywhere, but historically - and in the future
 - the <code>conf</code> directory will be the correct place for config files.</p>
<p style="margin:10px auto; font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
Let's start with a basic example. Copy and paste this into <code>conf/flume.conf</code>:</p>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'># Define a memory channel called ch1 on agent1</pre>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'>agent1.channels.ch1.type = memory</pre>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'> </pre>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'># Define an Avro source called avro-source1 on agent1 and tell it</pre>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'># to bind to 0.0.0.0:41414. Connect it to channel ch1.</pre>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'>agent1.sources.avro-source1.channels = ch1</pre>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'>agent1.sources.avro-source1.type = avro</pre>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'>agent1.sources.avro-source1.bind = 0.0.0.0</pre>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'>agent1.sources.avro-source1.port = 41414</pre>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'> </pre>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'># Define a logger sink that simply logs all events it receives</pre>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'># and connect it to the other end of the same channel.</pre>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'>agent1.sinks.log-sink1.channel = ch1</pre>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'>agent1.sinks.log-sink1.type = logger</pre>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'> </pre>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'># Finally, now that we've defined all of our components, tell</pre>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'># agent1 which ones we want to activate.</pre>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'>agent1.channels = ch1</pre>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'>agent1.sources = avro-source1</pre>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'>agent1.sinks = log-sink1</pre>
<p style="margin:10px auto; font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
This example creates a memory channel (i.e. an unreliable or "best effort" transport), an Avro RPC source, and a logger sink and connects them together. Any events received by the Avro source are routed to the channel <code>ch1</code> and delivered to the logger
 sink. It's important to note that defining components is the first half of configuring Flume; they must be activated by listing them in the <code>&lt;agent&gt;.channels,</code> <code>&lt;agent&gt;.sources</code>, <code>and</code> sections. Multiple sources, sinks, and
 channels may be listed, separated by a space.</p>
<p style="margin:10px auto; font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
For full details, please see the javadoc for the <code>org.apache.flume.conf.properties.PropertiesFileConfigurationProvider</code> class.</p>
<p style="margin:10px auto; font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
This is a listing of the implemented sources, sinks, and channels at this time. Each plugin has its own optional and required configuration properties so <strong>please</strong> see the javadocs (for now).</p>
<table border="1" cellpadding="0" style="margin:0px auto; border:1px solid rgb(192,192,192); border-collapse:collapse; font-family:verdana,Arial,Helvetica,sans-serif; width:909px; height:3164px">
<tbody>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p align="center" style="margin:10px auto"><strong>Component</strong></p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p align="center" style="margin:10px auto"><strong>Type</strong></p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p align="center" style="margin:10px auto"><strong>Description</strong></p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p align="center" style="margin:10px auto"><strong>Implementation Class</strong></p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Channel</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">memory</p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">In-memory, fast, non-durable event transport</p>
<p style="margin:10px auto">一个将event存储在内容中，快速传输但无法持久化的channel。</p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">MemoryChannel</p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Channel</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">file</p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">A channel for reading, writing, mapping, and manipulating a file</p>
<p style="margin:10px auto">一个对文件进行读、写、映射和操作的channel</p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">FileChannel</p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Channel</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">jdbc</p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">JDBC-based, durable event transport (Derby-based)</p>
<p style="margin:10px auto">基于JDBC，支持持久化的channel</p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">JDBCChannel</p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Channel</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">recoverablememory</p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">A durable channel implementation that uses the local file system for its storage</p>
<p style="margin:10px auto">一个使用本地文件系统实现持久化的channel</p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">RecoverableMemoryChannel</p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Channel</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">org.apache.flume.channel.PseudoTxnMemoryChannel</p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Mainly for testing purposes. Not meant for production use.</p>
<p style="margin:10px auto">用于测试，不用于生产</p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">PseudoTxnMemoryChannel</p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Channel</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto"><em>(custom type as FQCN)</em></p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Your own Channel impl.</p>
<p style="margin:10px auto">自定义channel</p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto"><em>(custom FQCN)</em></p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Source</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">avro</p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Avro Netty RPC event source</p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">AvroSource</p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Source</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">exec</p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Execute a long-lived Unix process and read from stdout</p>
<p style="margin:10px auto">执行一个长连接Unix进程并从标准输出设备读取数据</p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">ExecSource</p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Source</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">netcat</p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Netcat style TCP event source</p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">NetcatSource</p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Source</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">seq</p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Monotonically incrementing sequence generator event source</p>
<p style="margin:10px auto">单调递增序列发生器的事件source</p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">SequenceGeneratorSource</p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Source</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">org.apache.flume.source.StressSource</p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Mainly for testing purposes. Not meant for production use. Serves as a continuous source of events where each event has the same payload. The payload consists of some number of bytes (specified by <em>size</em> property, defaults
 to 500) where each byte has the signed value Byte.MAX_VALUE (0x7F, or 127).</p>
<p style="margin:10px auto">主要用于测试，不适合用于生产。用于接收每个拥有相同的有效负载的event。那有效负载包含一组字节（通过 size属性指定，默认为500）每个字节都是最大值（Byte.MAX_VALUE(0X7F或者127)）</p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">org.apache.flume.source.StressSource</p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Source</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">syslogtcp</p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto"> </p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">SyslogTcpSource</p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Source</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">syslogudp</p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto"> </p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">SyslogUDPSource</p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Source</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">org.apache.flume.source.avroLegacy.AvroLegacySource</p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto"> </p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">AvroLegacySource</p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Source</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">org.apache.flume.source.thriftLegacy.ThriftLegacySource</p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto"> </p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">ThriftLegacySource</p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Source</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">org.apache.flume.source.scribe.ScribeSource</p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto"> </p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">ScribeSource</p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Source</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto"><em>(custom type as FQCN)</em></p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Your own Source impl.</p>
<p style="margin:10px auto">自定义Source</p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto"><em>(custom FQCN)</em></p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Sink</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">hdfs</p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Writes all events received to HDFS (with support for rolling, bucketing, HDFS-200 append, and more)</p>
<p style="margin:10px auto">将所有接收到events写到HDFS（支持回滚，桶装和追加以及其他）</p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">HDFSEventSink</p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Sink</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">org.apache.flume.sink.hbase.HBaseSink</p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">A simple sink that reads events from a channel and writes them to HBase.</p>
<p style="margin:10px auto">一个简单的sink用于将从channel读到的数据写到HBase</p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">org.apache.flume.sink.hbase.HBaseSink</p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Sink</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">org.apache.flume.sink.hbase.AsyncHBaseSink</p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto"> </p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">org.apache.flume.sink.hbase.AsyncHBaseSink</p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Sink</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">logger</p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Log events at INFO level via configured logging subsystem (log4j by default)</p>
<p style="margin:10px auto">通过配置日志子系统将INFO级别的events打印出来。</p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">LoggerSink</p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Sink</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">avro</p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Sink that invokes a pre-defined Avro protocol method for all events it receives (when paired with an avro source, forms tiered collection)</p>
<p style="margin:10px auto">一个调用预先定义好的Avro protocol方法来处理接收的所有event的sink（与avro source配对，形成分层收集）</p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">AvroSink</p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Sink</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">file_roll</p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto"> </p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">RollingFileSink</p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Sink</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">irc</p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto"> </p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">IRCSink</p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Sink</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">null</p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">/dev/null for Flume - blackhole all events received</p>
<p style="margin:10px auto">event黑洞，有来无回</p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">NullSink</p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Sink</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto"><em>(custom type as FQCN)</em></p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Your own Sink impl.</p>
<p style="margin:10px auto">自定义sink</p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto"><em>(custom FQCN)</em></p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">ChannelSelector</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">replicating</p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto"> </p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">ReplicatingChannelSelector</p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">ChannelSelector</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">multiplexing</p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto"> </p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">MultiplexingChannelSelector</p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">ChannelSelector</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">(custom type)</p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Your own ChannelSelector impl.</p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto"><em>(custom FQCN)</em></p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">SinkProcessor</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">default</p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto"> </p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">DefaultSinkProcessor</p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">SinkProcessor</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">failover</p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto"> </p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">FailoverSinkProcessor</p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">SinkProcessor</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">load_balance</p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Provides the ability to load-balance flow over multiple sinks.</p>
<p style="margin:10px auto">当存在多个sink时实现负载均衡</p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">LoadBalancingSinkProcessor</p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">SinkProcessor</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto"><em>(custom type as FQCN)</em></p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Your own SinkProcessor impl.</p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto"><em>(custom FQCN)</em></p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Interceptor$Builder</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">host</p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto"> </p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">HostInterceptor$Builder</p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Interceptor$Builder</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">timestamp</p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">TimestampInterceptor</p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">TimestampInterceptor$Builder</p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Interceptor$Builder</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">static</p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto"> </p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">StaticInterceptor$Builder</p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Interceptor$Builder</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">regex_filter</p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto"> </p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">RegexFilteringInterceptor$Builder</p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Interceptor$Builder</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto"><em>(custom type as FQCN)</em></p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Your own Interceptor$Builder impl.</p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto"><em>(custom FQCN)</em></p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">EventSerializer$Builder</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">text</p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto"> </p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">BodyTextEventSerializer$Builder</p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">EventSerializer$Builder</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">avro_event</p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto"> </p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">FlumeEventAvroEventSerializer$Builder</p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">EventSerializer</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">org.apache.flume.sink.hbase.SimpleHbaseEventSerializer</p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto"> </p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">SimpleHbaseEventSerializer</p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">EventSerializer</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">org.apache.flume.sink.hbase.SimpleAsyncHbaseEventSerializer</p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto"> </p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">SimpleAsyncHbaseEventSerializer</p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">EventSerializer</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">org.apache.flume.sink.hbase.RegexHbaseEventSerializer</p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto"> </p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">RegexHbaseEventSerializer</p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">HbaseEventSerializer</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Custom implementation of serializer for HBaseSink. <br>
<em>(custom type as FQCN)</em></p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Your own HbaseEventSerializer impl. </p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto"><em>(custom FQCN)</em></p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">AsyncHbaseEventSerializer</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Custom implementation of serializer for AsyncHbase sink. <br>
<em>(custom type as FQCN)</em></p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Your own AsyncHbaseEventSerializer impl.</p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto"><em>(custom FQCN)</em></p>
</td>
</tr>
<tr>
<td width="98" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">EventSerializer$Builder</p>
</td>
<td width="88" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Custom implementation of serializer for all sinks except for HBaseSink and AsyncHBaseSink. <br>
<em>(custom type as FQCN)</em></p>
</td>
<td width="561" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Your own EventSerializer$Builder impl.</p>
</td>
<td width="281" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto"><em>(custom FQCN)</em></p>
</td>
</tr>
</tbody>
</table>
<p style="margin:10px auto; font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
The flume-ng executable lets you run a Flume NG agent or an Avro client which is useful for testing and experiments. No matter what, you'll need to specify a command (e.g. <code>agent</code> or <code>avro-client</code>) and a conf directory (<code>--conf &lt;conf
 dir&gt;</code>). All other options are command-specific.</p>
<p style="margin:10px auto; font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
To start the flume server using the flume.conf above:</p>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'>bin/flume-ng agent --conf ./conf/ -f conf/flume.conf -Dflume.root.logger=DEBUG,console -n agent1</pre>
<p style="margin:10px auto; font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
Notice that the agent name is specified by <code>-n agent1</code> and must match a agent name given in <code>-f conf/flume.conf</code></p>
<p style="margin:10px auto; font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
Your output should look something like this:</p>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'>$ bin/flume-ng agent --conf conf/ -f conf/flume.conf -n agent1</pre>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'>2012-03-16 16:36:11,918 (main) [INFO - org.apache.flume.lifecycle.LifecycleSupervisor.start(LifecycleSupervisor.java:58)] Starting lifecycle supervisor 1</pre>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'>2012-03-16 16:36:11,921 (main) [INFO - org.apache.flume.node.FlumeNode.start(FlumeNode.java:54)] Flume node starting - agent1</pre>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'>2012-03-16 16:36:11,926 (lifecycleSupervisor-1-0) [INFO - org.apache.flume.node.nodemanager.DefaultLogicalNodeManager.start(DefaultLogicalNodeManager.java:110)] Node manager starting</pre>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'>2012-03-16 16:36:11,928 (lifecycleSupervisor-1-0) [INFO - org.apache.flume.lifecycle.LifecycleSupervisor.start(LifecycleSupervisor.java:58)] Starting lifecycle supervisor 10</pre>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'>2012-03-16 16:36:11,929 (lifecycleSupervisor-1-0) [DEBUG - org.apache.flume.node.nodemanager.DefaultLogicalNodeManager.start(DefaultLogicalNodeManager.java:114)] Node manager started</pre>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'>2012-03-16 16:36:11,926 (lifecycleSupervisor-1-1) [INFO - org.apache.flume.conf.file.AbstractFileConfigurationProvider.start(AbstractFileConfigurationProvider.java:67)] Configuration provider starting</pre>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'>2012-03-16 16:36:11,930 (lifecycleSupervisor-1-1) [DEBUG - org.apache.flume.conf.file.AbstractFileConfigurationProvider.start(AbstractFileConfigurationProvider.java:87)] Configuration provider started</pre>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'>2012-03-16 16:36:11,930 (conf-file-poller-0) [DEBUG - org.apache.flume.conf.file.AbstractFileConfigurationProvider$FileWatcherRunnable.run(AbstractFileConfigurationProvider.java:189)] Checking file:conf/flume.conf for changes</pre>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'>2012-03-16 16:36:11,931 (conf-file-poller-0) [INFO - org.apache.flume.conf.file.AbstractFileConfigurationProvider$FileWatcherRunnable.run(AbstractFileConfigurationProvider.java:196)] Reloading configuration file:conf/flume.conf</pre>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'>2012-03-16 16:36:11,936 (conf-file-poller-0) [DEBUG - org.apache.flume.conf.properties.FlumeConfiguration$AgentConfiguration.isValid(FlumeConfiguration.java:225)] Starting validation of configuration for agent: agent1, initial-configuration: AgentConfiguration[agent1]</pre>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'>SOURCES: {avro-source1=ComponentConfiguration[avro-source1]</pre>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'>  CONFIG: {port=41414, channels=ch1, type=avro, bind=0.0.0.0}</pre>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'>  RUNNER:   ComponentConfiguration[runner]</pre>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'>    CONFIG: {}</pre>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'> </pre>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'> </pre>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'>}</pre>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'>CHANNELS: {ch1=ComponentConfiguration[ch1]</pre>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'>  CONFIG: {type=memory}</pre>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'> </pre>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'>}</pre>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'>SINKS: {log-sink1=ComponentConfiguration[log-sink1]</pre>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'>  CONFIG: {type=logger, channel=ch1}</pre>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'>  RUNNER:   ComponentConfiguration[runner]</pre>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'>    CONFIG: {}</pre>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'> </pre>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'> </pre>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'>}</pre>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'>2012-03-16 16:36:11,936 (conf-file-poller-0) [INFO - org.apache.flume.conf.properties.FlumeConfiguration.validateConfiguration(FlumeConfiguration.java:119)] Post-validation flume configuration contains configuation  for agents: [agent1]</pre>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'>2012-03-16 16:36:11,937 (conf-file-poller-0) [DEBUG - org.apache.flume.channel.DefaultChannelFactory.create(DefaultChannelFactory.java:67)] Creating instance of channel ch1 type memory</pre>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'>2012-03-16 16:36:11,944 (conf-file-poller-0) [DEBUG - org.apache.flume.source.DefaultSourceFactory.create(DefaultSourceFactory.java:73)] Creating instance of source avro-source1, type avro</pre>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'>2012-03-16 16:36:11,957 (conf-file-poller-0) [INFO - org.apache.flume.sink.DefaultSinkFactory.create(DefaultSinkFactory.java:69)] Creating instance of sink log-sink1 typelogger</pre>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'>2012-03-16 16:36:11,963 (conf-file-poller-0) [INFO - org.apache.flume.node.nodemanager.DefaultLogicalNodeManager.onNodeConfigurationChanged(DefaultLogicalNodeManager.java:52)] Node configuration change:{ sourceRunners:{avro-source1=EventDrivenSourceRunner: { source:AvroSource: { bindAddress:0.0.0.0 port:41414 } }} sinkRunners:{log-sink1=SinkRunner: { policy:org.apache.flume.sink.DefaultSinkProcessor@79f6f296 counterGroup:{ name:null counters:{} } }} channels:{ch1=org.apache.flume.channel.MemoryChannel@43b09468} }</pre>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'>2012-03-16 16:36:11,974 (lifecycleSupervisor-1-1) [INFO - org.apache.flume.source.AvroSource.start(AvroSource.java:122)] Avro source starting:AvroSource: { bindAddress:0.0.0.0 port:41414 }</pre>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'>2012-03-16 16:36:11,975 (Thread-1) [DEBUG - org.apache.flume.SinkRunner$PollingRunner.run(SinkRunner.java:123)] Polling sink runner starting</pre>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'>2012-03-16 16:36:12,352 (lifecycleSupervisor-1-1) [DEBUG - org.apache.flume.source.AvroSource.start(AvroSource.java:132)] Avro source started</pre>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'> </pre>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'> </pre>
<h2 style="font-family:verdana,Arial,Helvetica,sans-serif">flume-ng global options</h2>
<table border="1" cellpadding="0" style="margin:0px auto; border:1px solid rgb(192,192,192); border-collapse:collapse; font-family:verdana,Arial,Helvetica,sans-serif">
<tbody>
<tr>
<td width="190" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p align="center" style="margin:10px auto"><strong>Option</strong></p>
</td>
<td width="498" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p align="center" style="margin:10px auto"><strong>Description</strong></p>
</td>
</tr>
<tr>
<td width="190" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">--conf,-c &lt;conf&gt;</p>
</td>
<td width="498" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Use configs in &lt;conf&gt; directory</p>
</td>
</tr>
<tr>
<td width="190" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">--classpath,-C &lt;cp&gt;</p>
</td>
<td width="498" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Append to the classpath</p>
</td>
</tr>
<tr>
<td width="190" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">--dryrun,-d</p>
</td>
<td width="498" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Do not actually start Flume, just print the command</p>
</td>
</tr>
<tr>
<td width="190" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">-Dproperty=value</p>
</td>
<td width="498" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Sets a JDK system property value</p>
</td>
</tr>
</tbody>
</table>
<h2 style="font-family:verdana,Arial,Helvetica,sans-serif">flume-ng agent options</h2>
<p style="margin:10px auto; font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
When given the agent command, a Flume NG agent will be started with a given configuration file (required).</p>
<p style="margin:10px auto; font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
 </p>
<table border="1" cellpadding="0" style="margin:0px auto; border:1px solid rgb(192,192,192); border-collapse:collapse; font-family:verdana,Arial,Helvetica,sans-serif">
<tbody>
<tr>
<td width="190" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p align="center" style="margin:10px auto"><strong>Option</strong></p>
</td>
<td width="526" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p align="center" style="margin:10px auto"><strong>Description</strong></p>
</td>
</tr>
<tr>
<td width="190" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">--conf-file,-f &lt;file&gt;</p>
</td>
<td width="526" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Indicates which configuration file you want to run with (required)</p>
</td>
</tr>
<tr>
<td width="190" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">--name,-n &lt;agentname&gt;</p>
</td>
<td width="526" style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Indicates the name of agent on which we're running (required)</p>
</td>
</tr>
</tbody>
</table>
<h2 style="font-family:verdana,Arial,Helvetica,sans-serif">flume-ng avro-client options（flume-ng avro-client 选项）</h2>
<p style="margin:10px auto; font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
Run an Avro client that sends either a file or data from stdin to a specified host and port where a Flume NG Avro Source is listening.</p>
<table border="1" cellpadding="0" style="margin:0px auto; border:1px solid rgb(192,192,192); border-collapse:collapse; font-family:verdana,Arial,Helvetica,sans-serif">
<tbody>
<tr>
<td style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p align="center" style="margin:10px auto"><strong>Option</strong></p>
</td>
<td style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p align="center" style="margin:10px auto"><strong>Description</strong></p>
</td>
</tr>
<tr>
<td style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">--host,-H &lt;hostname&gt;</p>
</td>
<td style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Specifies the hostname of the Flume agent (may be localhost)</p>
</td>
</tr>
<tr>
<td style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">--port,-p &lt;port&gt;</p>
</td>
<td style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Specifies the port on which the Avro source is listening</p>
</td>
</tr>
<tr>
<td style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">--filename,-F &lt;filename&gt;</p>
</td>
<td style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Sends each line of &lt;filename&gt; to Flume (optional)</p>
</td>
</tr>
<tr>
<td style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">--headerFile,-F &lt;file&gt;</p>
</td>
<td style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:10px; margin:8px; border:1px solid rgb(192,192,192); border-collapse:collapse; padding:3px">
<p style="margin:10px auto">Header file containing headers as key/value pairs on each new line</p>
</td>
</tr>
</tbody>
</table>
<p style="margin:10px auto; font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
The Avro client treats each line (terminated by <code>\n</code>, <code>\r</code>, or <code>\r\n</code>) as an event. Think of the <code>avro-client</code> command as <code>cat</code> for Flume. For instance, the following creates one event per Linux user and
 sends it to Flume's avro source on localhost:41414.</p>
<p style="margin:10px auto; font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
In a new window type the following:</p>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'>$ bin/flume-ng avro-client --conf conf -H localhost -p 41414 -F /etc/passwd -Dflume.root.logger=DEBUG,console</pre>
<p style="margin:10px auto; font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
You should see something like this:</p>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'>2012-03-16 16:39:17,124 (main) [DEBUG - org.apache.flume.client.avro.AvroCLIClient.run(AvroCLIClient.java:175)] Finished</pre>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'>2012-03-16 16:39:17,127 (main) [DEBUG - org.apache.flume.client.avro.AvroCLIClient.run(AvroCLIClient.java:178)] Closing reader</pre>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'>2012-03-16 16:39:17,127 (main) [DEBUG - org.apache.flume.client.avro.AvroCLIClient.run(AvroCLIClient.java:183)] Closing transceiver</pre>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'>2012-03-16 16:39:17,129 (main) [DEBUG - org.apache.flume.client.avro.AvroCLIClient.main(AvroCLIClient.java:73)] Exiting</pre>
<p style="margin:10px auto; font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
And in your first window, where the server is running:</p>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'>2012-03-16 16:39:16,738 (New I/O server boss #1 ([id: 0x49e808ca, /0:0:0:0:0:0:0:0:41414])) [INFO - org.apache.avro.ipc.NettyServer$NettyServerAvroHandler.handleUpstream(NettyServer.java:123)] [id: 0x0b92a848, /1</pre>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'>27.0.0.1:39577 =&gt; /127.0.0.1:41414] OPEN</pre>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'>2012-03-16 16:39:16,742 (New I/O server worker #1-1) [INFO - org.apache.avro.ipc.NettyServer$NettyServerAvroHandler.handleUpstream(NettyServer.java:123)] [id: 0x0b92a848, /127.0.0.1:39577 =&gt; /127.0.0.1:41414] BOU</pre>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'>ND: /127.0.0.1:41414</pre>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'>2012-03-16 16:39:16,742 (New I/O server worker #1-1) [INFO - org.apache.avro.ipc.NettyServer$NettyServerAvroHandler.handleUpstream(NettyServer.java:123)] [id: 0x0b92a848, /127.0.0.1:39577 =&gt; /127.0.0.1:41414] CON</pre>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'>NECTED: /127.0.0.1:39577</pre>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'>2012-03-16 16:39:17,129 (New I/O server worker #1-1) [INFO - org.apache.avro.ipc.NettyServer$NettyServerAvroHandler.handleUpstream(NettyServer.java:123)] [id: 0x0b92a848, /127.0.0.1:39577 :&gt; /127.0.0.1:41414] DISCONNECTED</pre>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'>2012-03-16 16:39:17,129 (New I/O server worker #1-1) [INFO - org.apache.avro.ipc.NettyServer$NettyServerAvroHandler.handleUpstream(NettyServer.java:123)] [id: 0x0b92a848, /127.0.0.1:39577 :&gt; /127.0.0.1:41414] UNBOUND</pre>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'>2012-03-16 16:39:17,129 (New I/O server worker #1-1) [INFO - org.apache.avro.ipc.NettyServer$NettyServerAvroHandler.handleUpstream(NettyServer.java:123)] [id: 0x0b92a848, /127.0.0.1:39577 :&gt; /127.0.0.1:41414] CLOSED</pre>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'>2012-03-16 16:39:17,302 (Thread-1) [INFO - org.apache.flume.sink.LoggerSink.process(LoggerSink.java:68)] Event: { headers:{} body:[B@5c1ae90c }</pre>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'>2012-03-16 16:39:17,302 (Thread-1) [INFO - org.apache.flume.sink.LoggerSink.process(LoggerSink.java:68)] Event: { headers:{} body:[B@6aba4211 }</pre>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'>2012-03-16 16:39:17,302 (Thread-1) [INFO - org.apache.flume.sink.LoggerSink.process(LoggerSink.java:68)] Event: { headers:{} body:[B@6a47a0d4 }</pre>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'>2012-03-16 16:39:17,302 (Thread-1) [INFO - org.apache.flume.sink.LoggerSink.process(LoggerSink.java:68)] Event: { headers:{} body:[B@48ff4cf }</pre>
<pre style='font-family:"Courier New"; margin:5px 8px; padding:5px'>...</pre>
<p style="margin:10px auto; font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
Congratulations! You have Apache Flume running!</p>
<h1 style="font-family:verdana,Arial,Helvetica,sans-serif">Providing Feedback</h1>
<p style="margin:10px auto; font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
For help building, configuring, and running Flume (NG or otherwise), the best place is always the user mailing list. Send an email to <a target="_blank" href="mailto:user-subscribe@flume.apache.org" rel="nofollow">user-subscribe@flume.apache.org</a> to subscribe
 and <a target="_blank" href="mailto:user@flume.apache.org" rel="nofollow">user@flume.apache.org</a> to post once you've subscribed. The archives are available at <a target="_blank" href="http://mail-archives.apache.org/mod_mbox/incubator-flume-user/" rel="nofollow">http://mail-archives.apache.org/mod_mbox/incubator-flume-user/</a> (up
 through part of July 2012) and <a target="_blank" href="http://mail-archives.apache.org/mod_mbox/incubator-flume-user/" rel="nofollow">http://mail-archives.apache.org/mod_mbox/incubator-flume-user/</a><a target="_blank" href="http://mail-archives.apache.org/mod_mbox/flume-user/" rel="nofollow">http://mail-archives.apache.org/mod_mbox/flume-user/</a> (starting
 through part of July 2012 onwards).</p>
<p style="margin:10px auto; font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
If you believe you've found a bug or wish to file a feature request or improvement, don't be shy. Go to <a target="_blank" href="https://issues.apache.org/jira/browse/FLUME" rel="nofollow">https://issues.apache.org/jira/browse/FLUME</a> and file a JIRA for
 the version of Flume. For NG, please set the "Affects Version" to the appropriate milestone / release. Just leave any field you're not sure about blank. We'll bug you for details if we need them. Note that you must create an Apache JIRA account and log in
 before you can file issues.</p>
<p style="margin:10px auto; font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
 </p>
<p style="margin:10px auto; font-family:verdana,Arial,Helvetica,sans-serif; font-size:14px">
因个人能力实在有限，难免会出现这样那样的话，希望大家不吝指教。</p>
            </div>
                </div>