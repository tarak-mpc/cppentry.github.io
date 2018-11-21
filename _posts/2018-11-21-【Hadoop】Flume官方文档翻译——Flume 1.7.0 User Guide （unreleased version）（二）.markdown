---
layout:     post
title:      【Hadoop】Flume官方文档翻译——Flume 1.7.0 User Guide （unreleased version）（二）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h3 style="font-family:verdana, Arial, Helvetica, sans-serif;margin-left:30px;"><a href="http://blog.csdn.net/u011812294/article/details/53466941" rel="nofollow">Flume官方文档翻译——Flume 1.7.0 User Guide （unreleased version）（一）</a><br></h3>
<h3 style="font-family:verdana, Arial, Helvetica, sans-serif;margin-left:30px;">Logging raw data（记录原始数据）</h3>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
Logging the raw stream of data flowing through the ingest pipeline is not desired behaviour in many production environments because this may result in leaking sensitive data or security related configurations, such as secret keys, to Flume log files. By default,
 Flume will not log such information. On the other hand, if the data pipeline is broken, Flume will attempt to provide clues for debugging the problem.</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
One way to debug problems with event pipelines is to set up an additional <a href="http://www.cnblogs.com/#memory-channel" rel="nofollow"><span>Memory Channel</span></a> connected to a <a href="http://www.cnblogs.com/#logger-sink" rel="nofollow"><span>Logger
 Sink</span></a>, which will output all event data to the Flume logs. In some situations, however, this approach is insufficient.</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
In order to enable logging of event- and configuration-related data, some Java system properties must be set in addition to log4j properties.</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
To enable configuration-related logging, set the Java system property -Dorg.apache.flume.log.printconfig=true. This can either be passed on the command line or by setting this in the JAVA_OPTS variable in <em>flume-env.sh</em>.</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
To enable data logging, set the Java system property -Dorg.apache.flume.log.rawdata=true in the same way described above. For most components, the log4j logging level must also be set to DEBUG or TRACE to make event-specific logging appear in the Flume logs.</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
Here is an example of enabling both configuration logging and raw data logging while also setting the Log4j loglevel to DEBUG for console output:</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<span style="font-family:'宋体';">通过摄取管道获取记录到</span>Flume log文件的原始数据流大多不会描述生产环境的行为因为数据里面可能包含敏感数据或者安全相关的配置，例如安全密钥。默认情况，Flume不会记录这些信息。另一方面，如果数据管道损坏，Flume会试图提供一些线索来调试问题。</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<span style="font-family:'宋体';">一个调试事件管道的方法是设置一个额外的内存</span>channel来连接Logger Sink，用来将所有事件数据都记录到Flume log。然而，在一些情况之下，这种方法还是不足以解决问题。</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<span style="font-family:'宋体';">为了能够记录配置相关的日志，设置</span>-Dorg.apache.flume.log.printconfig=true。这个也可以通过命令行或者在flume-env.sh设置JAVA_OPTS属性。</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
为了能够记录数据，通过跟上面相同的方式来设置-Dorg.apache.flume.log.rawdata=true <span style="font-family:'宋体';">。对于大部分组件来说，</span>log4j的打印级别必须设置为DEBUG或者TRACE来让指定事件的日志信息出现在Flume log中。</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<span style="font-family:'宋体';">下面是一个例子能够保证将配置信息和原始数据在</span>log4j的打印级别设置在DEBUG的情况下输出到控制台：</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
</p>
<pre><code class="language-plain">$ bin/flume-ng agent --conf conf --conf-file example.conf --name a1 -Dflume.root.logger=DEBUG,console
 -Dorg.apache.flume.log.printconfig=true
-Dorg.apache.flume.log.rawdata=true <span style="font-family:verdana, Arial, Helvetica, sans-serif;background-color:rgb(255,255,255);">                  </span></code></pre>
<p></p>
<h3 style="font-family:verdana, Arial, Helvetica, sans-serif;margin-left:30px;">Zookeeper based Configuration<span style="font-family:'宋体';">（</span>Zookeeper基础配置）</h3>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
Flume supports Agent configurations via Zookeeper. <em>This is an experimental feature.</em> The configuration file needs to be uploaded in the Zookeeper, under a configurable prefix. The configuration file is stored in Zookeeper Node data. Following is how
 the Zookeeper Node tree would look like for agents a1 and a2</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
Flume支持通过ZooKeeper来配置Agent。这是个实验性的特性。配置文档必须上传到ZooKeeper中，（在一个可配置的前缀下）。这个配置文档存储在ZooKeeper节点数据下。下面是ZooKeeper下的节点树结构：</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
</p>
<pre><code class="language-plain">- /flume

 |- /a1 [Agent config file]

 |- /a2 [Agent config file]</code></pre>
<p></p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
Once the configuration file is uploaded, start the agent with following options</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<span style="font-family:'宋体';">一旦配置文档上传完成，通过下面选项来启动</span>agent</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
$ bin/flume-ng agent –conf conf -z zkhost:2181,zkhost1:2181 -p /flume –name a1 -Dflume.root.logger=INFO,console</p>
<table class="mceItemTable" style="border:1px dashed rgb(187,187,187);border-collapse:collapse;font-family:verdana, Arial, Helvetica, sans-serif;"><tbody style="margin-left:30px;"><tr style="margin-left:30px;"><td width="117" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:10px;border:1px dashed rgb(187,187,187);border-collapse:collapse;">
<p><strong>Argument Name</strong></p>
</td>
<td width="69" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:10px;border:1px dashed rgb(187,187,187);border-collapse:collapse;">
<p><strong>Default</strong></p>
</td>
<td width="541" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:10px;border:1px dashed rgb(187,187,187);border-collapse:collapse;">
<p><strong>Description</strong></p>
</td>
</tr><tr style="margin-left:30px;"><td width="117" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:10px;border:1px dashed rgb(187,187,187);border-collapse:collapse;">
<p><strong>z</strong></p>
</td>
<td width="69" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:10px;border:1px dashed rgb(187,187,187);border-collapse:collapse;">
<p>–</p>
</td>
<td width="541" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:10px;border:1px dashed rgb(187,187,187);border-collapse:collapse;">
<p>Zookeeper connection string. Comma separated list of hostname:port</p>
</td>
</tr><tr style="margin-left:30px;"><td width="117" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:10px;border:1px dashed rgb(187,187,187);border-collapse:collapse;">
<p><strong>p</strong></p>
</td>
<td width="69" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:10px;border:1px dashed rgb(187,187,187);border-collapse:collapse;">
<p>/flume</p>
</td>
<td width="541" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:10px;border:1px dashed rgb(187,187,187);border-collapse:collapse;">
<p>Base Path in Zookeeper to store Agent configurations</p>
</td>
</tr></tbody></table><h3 style="font-family:verdana, Arial, Helvetica, sans-serif;margin-left:30px;">Installing third-party plugins（安装第三方插件）</h3>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
Flume has a fully plugin-based architecture. While Flume ships with many out-of-the-box sources, channels, sinks, serializers, and the like, many implementations exist which ship separately from Flume.</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
While it has always been possible to include custom Flume components by adding their jars to the FLUME_CLASSPATH variable in the flume-env.sh file, Flume now supports a special directory called plugins.d which automatically picks up plugins that are packaged
 in a specific format. This allows for easier management of plugin packaging issues as well as simpler debugging and troubleshooting of several classes of issues, especially library dependency conflicts.</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
Flume拥有一个全备的插件架构。虽然Flume自带许多开箱即用的sources、channel，sinks和serializers等，同时也存在许多跟Flume之外的实现。</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
Flume曾经支持在flume-env.sh中的FLUME_CLASSPATH中添加一些自定义的Flume组件，现在Flume支持一个特殊路径plugins.d自动地安装那些按照指定格式存储的插件。</p>
<h4 style="font-size:14px;font-family:verdana, Arial, Helvetica, sans-serif;margin-left:30px;">
The plugins.d directory（插件目录）</h4>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
The plugins.d directory is located at $FLUME_HOME/plugins.d. At startup time, the flume-ng start script looks in the plugins.d directory for plugins that conform to the below format and includes them in proper paths when starting up java.</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<span style="font-family:'宋体';">这个</span>plugins.d目录位于$FLUME_HOME/plugins.d。在启动的时候，flume-ng启动脚本扫描plugins.d目录下的遵循格式的插件并在启动java时将它们放在合适的路径。</p>
<h4 style="font-size:14px;font-family:verdana, Arial, Helvetica, sans-serif;margin-left:30px;">
Directory layout for plugins（插件的底层目录）</h4>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
Each plugin (subdirectory) within plugins.d can have up to three sub-directories:</p>
<ol style="margin-left:0px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;"><li style="margin-left:0px;list-style-type:none;">
<ol style="margin-left:0px;"><li style="margin-left:0px;">lib - the plugin’s jar(s)</li><li style="margin-left:0px;">libext - the plugin’s dependency jar(s)</li><li style="margin-left:0px;">native - any required native libraries, such as .so files</li></ol></li></ol><p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
Example of two plugins within the plugins.d directory:</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<span style="font-family:'宋体';">在</span>plugins.d下的每个插件（子目录）包含三个子目录：</p>
<ol style="margin-left:0px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;"><li style="margin-left:0px;list-style-type:none;">
<ol style="margin-left:0px;"><li style="margin-left:0px;">lib – <span style="font-family:'宋体';">插件的</span>jar包</li><li style="margin-left:0px;">libext – <span style="font-family:'宋体';">插件的依赖</span>jar包</li><li style="margin-left:0px;">native – <span style="font-family:'宋体';">任何需要的本地库，例如</span>.so文档。</li></ol></li></ol><p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
plugins.d目录下的两个插件：</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
</p>
<pre><code class="language-plain">plugins.d/

plugins.d/custom-source-1/

plugins.d/custom-source-1/lib/my-source.jar

plugins.d/custom-source-1/libext/spring-core-2.5.6.jar

plugins.d/custom-source-2/

plugins.d/custom-source-2/lib/custom.jar

plugins.d/custom-source-2/native/gettext.so</code></pre>
<p></p>
<h2 style="font-family:verdana, Arial, Helvetica, sans-serif;margin-left:30px;">Data ingestion（数据获取）</h2>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
Flume supports a number of mechanisms to ingest data from external sources.</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
Flume支持从外部来源获取数据的一系列机制。</p>
<h3 style="font-family:verdana, Arial, Helvetica, sans-serif;margin-left:30px;">RPC</h3>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
An Avro client included in the Flume distribution can send a given file to Flume Avro source using avro RPC mechanism:</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
Flume 中的Avro client 可以用avro RPC 机制来发送一个给定文档给Flume Avro source。</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
</p>
<pre><code class="language-plain">$ bin/flume-ng avro-client -H localhost -p 41414 -F /usr/logs/log.10</code></pre>
<p></p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
The above command will send the contents of /usr/logs/log.10 to to the Flume source listening on that ports.</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<span style="font-family:'宋体';">上面的命令行发送</span>/usr/logs/log.10的内容给监听在那个端口的Flume source。</p>
<h3 style="font-family:verdana, Arial, Helvetica, sans-serif;margin-left:30px;">Executing commands（执行命令行）</h3>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
There’s an exec source that executes a given command and consumes the output. A single ‘line’ of output ie. text followed by carriage return (‘\r’) or line feed (‘\n’) or both together.</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<span style="font-family:'宋体';">有一个执行</span>source来执行给出的命令和消费输出。输出的一行文本带着(‘\r’)或者(‘\n’)或者两者皆有。</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<strong>Note : </strong>Flume does not support tail as a source. One can wrap the tail command in an exec source to stream the file.</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
（<span style="font-family:'宋体';">说明：</span>Fluem不支持一个结尾符作为一个资源。所以可以用一个可执行的源码来包装结尾命令输出文件。）</p>
<h3 style="font-family:verdana, Arial, Helvetica, sans-serif;margin-left:30px;">Network streams</h3>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
Flume supports the following mechanisms to read data from popular log stream types, such as:</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
Flume支持下面的机制来读取受欢迎的日志类型，例如：</p>
<ol style="margin-left:0px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;"><li style="margin-left:0px;list-style-type:none;">
<ol style="margin-left:0px;"><li style="margin-left:0px;">Avro</li><li style="margin-left:0px;">Thrift</li><li style="margin-left:0px;">Syslog</li><li style="margin-left:0px;">Netcat</li></ol></li></ol><h2 style="font-family:verdana, Arial, Helvetica, sans-serif;margin-left:30px;">Setting multi-agent flow<span style="font-family:'宋体';">（设置多个</span>agent流）</h2>
<p align="center" style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<img src="http://images2015.cnblogs.com/blog/1045352/201612/1045352-20161206192357897-1087127995.png" alt="" style="border:0px;"> </p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
In order to flow the data across multiple agents or hops, the sink of the previous agent and source of the current hop need to be avro type with the sink pointing to the hostname (or IP address) and port of the source.</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<span style="font-family:'宋体';">为了让数据可以流过多个</span>agents或者hops，前面那个agent的sink和当前的hop的source都必须是avro类型并且sink还要指向source的主机名（IP地址）和端口。</p>
<h2 style="font-family:verdana, Arial, Helvetica, sans-serif;margin-left:30px;">Consolidation（结合）</h2>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
A very common scenario in log collection is a large number of log producing clients sending data to a few consumer agents that are attached to the storage subsystem. For example, logs collected from hundreds of web servers sent to a dozen of agents that write
 to HDFS cluster.</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<span style="font-family:'宋体';">一个日志收集中非常常见的情形是大量日志发送到一些消费数据的绑定到子存储系统的</span>agent上。例如，从上百个web 服务器日志收集而来日志发送到一打agents写到HDFS 集群</p>
<p align="center" style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<img src="http://images2015.cnblogs.com/blog/1045352/201612/1045352-20161206192408132-2045979391.png" alt="" style="border:0px;"> </p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
This can be achieved in Flume by configuring a number of first tier agents with an avro sink, all pointing to an avro source of single agent (Again you could use the thrift sources/sinks/clients in such a scenario). This source on the second tier agent consolidates
 the received events into a single channel which is consumed by a sink to its final destination.</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<span style="font-family:'宋体';">这个可以在</span>Flume中配置第一层包含avro sink的agents，所有的sink都执行一个单独的拥有avro source的agent（你也可以在这样的情形下使用thrift sources/sinks/cleints）。这个在第二层agent中的source将接收到的数据存储在一个channel中用来给sink输入到最终的目的。</p>
<h2 style="font-family:verdana, Arial, Helvetica, sans-serif;margin-left:30px;">Multiplexing the flow（选择分流）</h2>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
Flume supports multiplexing the event flow to one or more destinations. This is achieved by defining a flow multiplexer that can replicate or selectively route an event to one or more channels.</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
Flume支持将事件流向一个或者多个目的地。这个可以通过定义一个流的能够复制或者可选路径的多路选择器来将事件导向一个或者多个Channel来实现。</p>
<p align="center" style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<img src="http://images2015.cnblogs.com/blog/1045352/201612/1045352-20161206192417647-1599493658.png" alt="" style="border:0px;"> </p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
The above example shows a source from agent “foo” fanning out the flow to three different channels. This fan out can be replicating or multiplexing. In case of replicating flow, each event is sent to all three channels. For the multiplexing case, an event is
 delivered to a subset of available channels when an event’s attribute matches a preconfigured value. For example, if an event attribute called “txnType” is set to “customer”, then it should go to channel1 and channel3, if it’s “vendor” then it should go to
 channel2, otherwise channel3. The mapping can be set in the agent’s configuration file.</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<span style="font-family:'宋体';">上面那个例子展示</span>“foo”agent中的source将事件流分到三个不同的Channel。这个分流可以是复制或者多路复用。在复制流的情况下，每个实践都会被发送到三个channel中。对于分流的情况，一个事件将会匹配与配置好的value来发送到可达的channel中。例如，假如一个事件的属性“txnType”设为“customer”，那么它将被发送到channel1和channel3，如果值为“vendor”，那么会被送到channel2，否则就去channel3。这个映射关系可以在agent的配置文档中设置。</p>
<h1 style="font-family:verdana, Arial, Helvetica, sans-serif;margin-left:30px;">Configuration（配置）</h1>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
As mentioned in the earlier section, Flume agent configuration is read from a file that resembles a Java property file format with hierarchical property settings.</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<span style="font-family:'宋体';">正如在前面部分所提到的，</span>Flume agent配置是从一个类似于Java属性文件格式和层级属性设置的文档中读取的。</p>
<h2 style="font-family:verdana, Arial, Helvetica, sans-serif;margin-left:30px;">Defining the flow（定义流）</h2>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
To define the flow within a single agent, you need to link the sources and sinks via a channel. You need to list the sources, sinks and channels for the given agent, and then point the source and sink to a channel. A source instance can specify multiple channels,
 but a sink instance can only specify one channel. The format is as follows:</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<span style="font-family:'宋体';">在一个单点</span>agent中定义流。你必须通过一个channel来连接source和sink。你必须列出给定的agent的sources，sinks和channel，然后指出source和sink所指定的channel。一个source实例可以指定多个channel，但是一个sink实例只能指定一个channel。格式如下：</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
</p>
<pre><code class="language-plain"># list the sources, sinks and channels for the agent

&lt;Agent&gt;.sources = &lt;Source&gt;

&lt;Agent&gt;.sinks = &lt;Sink&gt;

&lt;Agent&gt;.channels = &lt;Channel1&gt; &lt;Channel2&gt;

 

# set channel for source

&lt;Agent&gt;.sources.&lt;Source&gt;.channels = &lt;Channel1&gt; &lt;Channel2&gt; ...

 

# set channel for sink

&lt;Agent&gt;.sinks.&lt;Sink&gt;.channel = &lt;Channel1&gt;

For example, an agent named agent_foo is reading data from an external avro client and sending it to HDFS via a memory channel. The config file weblog.config could look like:

例如，一个agent命名为agent_foo从一个外部的avro客户端读取数据通过一个内存channel发送到HDFS。配置文件可以如下：

# list the sources, sinks and channels for the agent

agent_foo.sources = avro-appserver-src-1

agent_foo.sinks = hdfs-sink-1

agent_foo.channels = mem-channel-1

 

# set channel for source

agent_foo.sources.avro-appserver-src-1.channels = mem-channel-1

 

# set channel for sink

agent_foo.sinks.hdfs-sink-1.channel = mem-channel-1</code></pre>
<p></p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
This will make the events flow from avro-AppSrv-source to hdfs-Cluster1-sink through the memory channel mem-channel-1. When the agent is started with the weblog.config as its config file, it will instantiate that flow.</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<span style="font-family:'宋体';">这样就可以使得事件流从</span>avro-AppSrv-source通过内存channel mem-channel-1流向hdfs-Cluster1-sink。当agent将weblog.config作为他的配置文件启动时，就会实例化这样一个流。</p>
<h2 style="font-family:verdana, Arial, Helvetica, sans-serif;margin-left:30px;">Configuring individual components（配置单个组件）</h2>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
After defining the flow, you need to set properties of each source, sink and channel. This is done in the same hierarchical namespace fashion where you set the component type and other values for the properties specific to each component:</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<span style="font-family:'宋体';">定义好一个流之后，你必须为每个</span>source、sink和channel配置属性。这跟你为每个组件设置组件类型和其他属性时使用的命名空间层级格式是一样的。</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
</p>
<pre><code class="language-sql"># properties for sources

&lt;Agent&gt;.sources.&lt;Source&gt;.&lt;someProperty&gt; = &lt;someValue&gt;

 

# properties for channels

&lt;Agent&gt;.channel.&lt;Channel&gt;.&lt;someProperty&gt; = &lt;someValue&gt;

 

# properties for sinks

&lt;Agent&gt;.sources.&lt;Sink&gt;.&lt;someProperty&gt; = &lt;someValue&gt;</code></pre>
<p></p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
The property “type” needs to be set for each component for Flume to understand what kind of object it needs to be. Each source, sink and channel type has its own set of properties required for it to function as intended. All those need to be set as needed.
 In the previous example, we have a flow from avro-AppSrv-source to hdfs-Cluster1-sink through the memory channel mem-channel-1. Here’s an example that shows configuration of each of those components:</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<span style="font-family:'宋体';">每个组件的</span>“type”属性是必须设置的，以保证Flume框架能够知道他们是哪种类型的。每个source、sink和channel类型都有它们被设计的预期功能而自己独有的属性。所有这些都必须设置。在前面的例子当中。我们拥有一个avro-AppSrv-source通过内存channel mem-channel-1连接hdfs-Cluster1-sink的流。下面将展示这些组件的配置情况</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
</p>
<pre><code class="language-plain">agent_foo.sources = avro-AppSrv-source

agent_foo.sinks = hdfs-Cluster1-sink

agent_foo.channels = mem-channel-1

 

# set channel for sources, sinks

 

# properties of avro-AppSrv-source

agent_foo.sources.avro-AppSrv-source.type = avro

agent_foo.sources.avro-AppSrv-source.bind = localhost

agent_foo.sources.avro-AppSrv-source.port = 10000

 

# properties of mem-channel-1

agent_foo.channels.mem-channel-1.type = memory

agent_foo.channels.mem-channel-1.capacity = 1000

agent_foo.channels.mem-channel-1.transactionCapacity = 100

 

# properties of hdfs-Cluster1-sink

agent_foo.sinks.hdfs-Cluster1-sink.type = hdfs

agent_foo.sinks.hdfs-Cluster1-sink.hdfs.path = hdfs://namenode/flume/webdata

 

#...</code></pre>
<p></p>
<h2 style="font-family:verdana, Arial, Helvetica, sans-serif;margin-left:30px;">Adding multiple flows in an agent<span style="font-family:'宋体';">（一个</span>Agent多个流）</h2>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
A single Flume agent can contain several independent flows. You can list multiple sources, sinks and channels in a config. These components can be linked to form multiple flows:</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<span style="font-family:'宋体';">单个</span>Flume agent可以包含多个独立的流。你可以在一个配置文件中列出多个sources、sinks和channels。这些组件将连接组成多个流。</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
</p>
<pre><code class="language-plain"># list the sources, sinks and channels for the agent

&lt;Agent&gt;.sources = &lt;Source1&gt; &lt;Source2&gt;

&lt;Agent&gt;.sinks = &lt;Sink1&gt; &lt;Sink2&gt;

&lt;Agent&gt;.channels = &lt;Channel1&gt; &lt;Channel2&gt;</code></pre>
<p></p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
Then you can link the sources and sinks to their corresponding channels (for sources) of channel (for sinks) to setup two different flows. For example, if you need to setup two flows in an agent, one going from an external avro client to external HDFS and another
 from output of a tail to avro sink, then here’s a config to do that:</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<span style="font-family:'宋体';">然后你可以将</span>sources和sink是通过相应的channels连接来配置两个不同的流。例如，你必须在一个agent中配置两个流，一个是从外部avro客户端到外部HDFS和另一个是从一个avro sink获取数据，以下配置可达到这个目标：</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
</p>
<pre><code class="language-plain"># list the sources, sinks and channels in the agent

agent_foo.sources = avro-AppSrv-source1 exec-tail-source2

agent_foo.sinks = hdfs-Cluster1-sink1 avro-forward-sink2

agent_foo.channels = mem-channel-1 file-channel-2

 

# flow #1 configuration

agent_foo.sources.avro-AppSrv-source1.channels = mem-channel-1

agent_foo.sinks.hdfs-Cluster1-sink1.channel = mem-channel-1

 

# flow #2 configuration

agent_foo.sources.exec-tail-source2.channels = file-channel-2

agent_foo.sinks.avro-forward-sink2.channel = file-channel-2

Configuring a multi agent flow（配置一个多agent流）</code></pre>
<p></p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
To setup a multi-tier flow, you need to have an avro/thrift sink of first hop pointing to avro/thrift source of the next hop. This will result in the first Flume agent forwarding events to the next Flume agent. For example, if you are periodically sending files
 (1 file per event) using avro client to a local Flume agent, then this local agent can forward it to another agent that has the mounted for storage.</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<span style="font-family:'宋体';">为了配置一个多层的流，你必须要有一个</span>avro/thriftsink 指向下一个hop的avro/thrift source。这将会使得第一个Flume agent将events传给下一个Flume agent。例如，如果你用avro client周期性地向一个本地的Flume agent发送数据，这个本地的Flume agent将events传到另外一个挂载内存的agent。</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
Weblog agent config:</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
</p>
<pre><code class="language-plain"># list sources, sinks and channels in the agent

agent_foo.sources = avro-AppSrv-source

agent_foo.sinks = avro-forward-sink

agent_foo.channels = file-channel

 

# define the flow

agent_foo.sources.avro-AppSrv-source.channels = file-channel

agent_foo.sinks.avro-forward-sink.channel = file-channel

 

# avro sink properties

agent_foo.sources.avro-forward-sink.type = avro

agent_foo.sources.avro-forward-sink.hostname = 10.1.1.100

agent_foo.sources.avro-forward-sink.port = 10000

 

# configure other pieces

#...

HDFS agent config:

# list sources, sinks and channels in the agent

agent_foo.sources = avro-collection-source

agent_foo.sinks = hdfs-sink

agent_foo.channels = mem-channel

 

# define the flow

agent_foo.sources.avro-collection-source.channels = mem-channel

agent_foo.sinks.hdfs-sink.channel = mem-channel

 

# avro sink properties

agent_foo.sources.avro-collection-source.type = avro

agent_foo.sources.avro-collection-source.bind = 10.1.1.100

agent_foo.sources.avro-collection-source.port = 10000

 

# configure other pieces

#...</code></pre>
<p></p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
Here we link the avro-forward-sink from the weblog agent to the avro-collection-source of the hdfs agent. This will result in the events coming from the external appserver source eventually getting stored in HDFS.</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<span style="font-family:'宋体';">在这里，我们将</span>weblog agent的avro-forward-sink连到hdfs agent的avro-collection-source。这将使得从外部app服务器来的events最终储存到HDFS中。</p>
<h2 style="font-family:verdana, Arial, Helvetica, sans-serif;margin-left:30px;">Fan out flow（分流）</h2>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
As discussed in previous section, Flume supports fanning out the flow from one source to multiple channels. There are two modes of fan out, replicating and multiplexing. In the replicating flow, the event is sent to all the configured channels. In case of multiplexing,
 the event is sent to only a subset of qualifying channels. To fan out the flow, one needs to specify a list of channels for a source and the policy for the fanning it out. This is done by adding a channel “selector” that can be replicating or multiplexing.
 Then further specify the selection rules if it’s a multiplexer. If you don’t specify a selector, then by default it’s replicating:</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<span style="font-family:'宋体';">正如前面部分所讨论的，</span>Flume支持将来自一个source的events分到多个channels。将有两个模式的分流（暂且叫分流吧），复制流和选择流。在复制流中，所有的events将会发送到所有的channel中。在选择流中，event会被分到特定的channel中。在分流中，必须为source指定一组channel和相应的策略。通过给source增加一个selector.type的属性来选择复制还是选择。如果是选择流,那么就要指定选择规则。如果没有指定的话，默认就是复制流。</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
</p>
<pre><code class="language-plain"># List the sources, sinks and channels for the agent

&lt;Agent&gt;.sources = &lt;Source1&gt;

&lt;Agent&gt;.sinks = &lt;Sink1&gt; &lt;Sink2&gt;

&lt;Agent&gt;.channels = &lt;Channel1&gt; &lt;Channel2&gt;

 

# set list of channels for source (separated by space)

&lt;Agent&gt;.sources.&lt;Source1&gt;.channels = &lt;Channel1&gt; &lt;Channel2&gt;

 

# set channel for sinks

&lt;Agent&gt;.sinks.&lt;Sink1&gt;.channel = &lt;Channel1&gt;

&lt;Agent&gt;.sinks.&lt;Sink2&gt;.channel = &lt;Channel2&gt;

 

&lt;Agent&gt;.sources.&lt;Source1&gt;.selector.type = replicating</code></pre>
<p></p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
The multiplexing select has a further set of properties to bifurcate the flow. This requires specifying a mapping of an event attribute to a set for channel. The selector checks for each configured attribute in the event header. If it matches the specified
 value, then that event is sent to all the channels mapped to that value. If there’s no match, then the event is sent to set of channels configured as default:</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<span style="font-family:'宋体';">选择流拥有一组属性来进行分流。这个需要为事件属性和</span>channel指定一个映射关系。这个选择器检查每个事件的header中的配置属性。如果他匹配到指定的值，该事件将会发送到所有跟指定值存在映射关系的channel。如果没有匹配成功，该event会发送到默认的channel。</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
</p>
<pre><code class="language-plain"># Mapping for multiplexing selector

&lt;Agent&gt;.sources.&lt;Source1&gt;.selector.type = multiplexing

&lt;Agent&gt;.sources.&lt;Source1&gt;.selector.header = &lt;someHeader&gt;

&lt;Agent&gt;.sources.&lt;Source1&gt;.selector.mapping.&lt;Value1&gt; = &lt;Channel1&gt;

&lt;Agent&gt;.sources.&lt;Source1&gt;.selector.mapping.&lt;Value2&gt; = &lt;Channel1&gt; &lt;Channel2&gt;

&lt;Agent&gt;.sources.&lt;Source1&gt;.selector.mapping.&lt;Value3&gt; = &lt;Channel2&gt;

#...

 

&lt;Agent&gt;.sources.&lt;Source1&gt;.selector.default = &lt;Channel2&gt;</code></pre>
<p></p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
The mapping allows overlapping the channels for each value.</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<span style="font-family:'宋体';">该映射允许一个</span>channel对应多个值。</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
The following example has a single flow that multiplexed to two paths. The agent named agent_foo has a single avro source and two channels linked to two sinks:</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<span style="font-family:'宋体';">接下来的例子是一个拥有两条路径的选择流。名字为</span>agent_foo的agent拥有单个avro source和两个channel连接两个sinks。</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
</p>
<pre><code class="language-plain"># list the sources, sinks and channels in the agent

agent_foo.sources = avro-AppSrv-source1

agent_foo.sinks = hdfs-Cluster1-sink1 avro-forward-sink2

agent_foo.channels = mem-channel-1 file-channel-2

 

# set channels for source

agent_foo.sources.avro-AppSrv-source1.channels = mem-channel-1 file-channel-2

 

# set channel for sinks

agent_foo.sinks.hdfs-Cluster1-sink1.channel = mem-channel-1

agent_foo.sinks.avro-forward-sink2.channel = file-channel-2

 

# channel selector configuration

agent_foo.sources.avro-AppSrv-source1.selector.type = multiplexing

agent_foo.sources.avro-AppSrv-source1.selector.header = State

agent_foo.sources.avro-AppSrv-source1.selector.mapping.CA = mem-channel-1

agent_foo.sources.avro-AppSrv-source1.selector.mapping.AZ = file-channel-2

agent_foo.sources.avro-AppSrv-source1.selector.mapping.NY = mem-channel-1 file-channel-2

agent_foo.sources.avro-AppSrv-source1.selector.default = mem-channel-1</code></pre>
<p></p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
The selector checks for a header called “State”. If the value is “CA” then its sent to mem-channel-1, if its “AZ” then it goes to file-channel-2 or if its “NY” then both. If the “State” header is not set or doesn’t match any of the three, then it goes to mem-channel-1
 which is designated as ‘default’.</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<span style="font-family:'宋体';">选择器检查名为</span>“State”的header。如果值为“CA”会被送到 mem-channel-1，如果值为“AZ”将会被送file-channel-2或者值为“NY”那么就会被送到两个channel。</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
The selector also supports optional channels. To specify optional channels for a header, the config parameter ‘optional’ is used in the following way:</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<span style="font-family:'宋体';">选择器也支持可选</span>channels。可以为一个header指定可选channel，可按以下方式来使用“optional”配置参数：</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
</p>
<pre><code class="language-plain"># channel selector configuration

agent_foo.sources.avro-AppSrv-source1.selector.type = multiplexing

agent_foo.sources.avro-AppSrv-source1.selector.header = State

agent_foo.sources.avro-AppSrv-source1.selector.mapping.CA = mem-channel-1

agent_foo.sources.avro-AppSrv-source1.selector.mapping.AZ = file-channel-2

agent_foo.sources.avro-AppSrv-source1.selector.mapping.NY = mem-channel-1 file-channel-2

agent_foo.sources.avro-AppSrv-source1.selector.optional.CA = mem-channel-1 file-channel-2

agent_foo.sources.avro-AppSrv-source1.selector.mapping.AZ = file-channel-2

agent_foo.sources.avro-AppSrv-source1.selector.default = mem-channel-1</code></pre>
<p></p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
The selector will attempt to write to the required channels first and will fail the transaction if even one of these channels fails to consume the events. The transaction is reattempted on all of the channels. Once all required channels have consumed the events,
 then the selector will attempt to write to the optional channels. A failure by any of the optional channels to consume the event is simply ignored and not retried.</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
If there is an overlap between the optional channels and required channels for a specific header, the channel is considered to be required, and a failure in the channel will cause the entire set of required channels to be retried. For instance, in the above
 example, for the header “CA” mem-channel-1 is considered to be a required channel even though it is marked both as required and optional, and a failure to write to this channel will cause that event to be retried on all channels configured for the selector.</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
Note that if a header does not have any required channels, then the event will be written to the default channels and will be attempted to be written to the optional channels for that header. Specifying optional channels will still cause the event to be written
 to the default channels, if no required channels are specified. If no channels are designated as default and there are no required, the selector will attempt to write the events to the optional channels. Any failures are simply ignored in that case.</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<span style="font-family:'宋体';">选择器会试图第一时间将数据写到需求</span>channel和当这些channel中某些channel没法消费这些events时会停止这次事务。该事务会重新连接所有channel。一旦所有channel都在消费了所有events，那么选择器会试图将events写到备选channel中。备选channel消费event产生的失效会被简单地忽略和不再重试。</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<span style="font-family:'宋体';">如果对于一个指定的</span>header存在备选channel和需求channel的重叠，那么选择需求channel，并且当一个需求channel发生失效时将会引起所有需求channel的重试。举个例子，在上面的案例中，为header“CA”指定了一个需求channel mem-channel-1，尽管备选channel和需求channel都指定了，但是一旦需求channel发生失效，name会引起该选择器中所有channel的重试。</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<span style="font-family:'宋体';">需要说明的是如果一个</span>header没有指定任何需求channel，那么events会写到默认channel和试图写到备选channel中。如果没有指定需求channel，就算指定了备选channel，events还是会被写到默认channel中。如果没有指定需求channel和默认channel，选择器会说将events写到备选channel。在这些情况中，失效会被忽略。</p>
            </div>
                </div>