---
layout:     post
title:      日志收集系统Flume调研笔记第2篇 - Flume配置及使用实例
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-size:14px;"><a href="http://blog.csdn.net/slvher/article/details/45370193" rel="nofollow">上篇笔记</a>对Flume的使用场景和系统架构做了介绍，本篇笔记以实例说明Flume的配置方法。下面开始正文。</span></p>
<p><span style="font-size:14px;"><strong>1. Flume使用实例<br>
1.1<span> </span>配置</strong><br>
Flume agent的3个组件及其拓扑关系是在配置文件中指定的，总的原则是必须列出source/channel/sink的name/type等重要的配置项，并通过channel将source(s)和sink(s)连接起来，此外，<span style="background-color:rgb(255,255,51);">1个source可以指定多个channel，而1个sink只能接收来自1个channel的数据</span>。<br>
这里给出的是部署1套含1个source、1个channel、1个sink的Flume agent的配置示例，更多的配置规则可以参考Flume User Guide的<a href="https://flume.apache.org/FlumeUserGuide.html#configuration" rel="nofollow">Configuration</a>部分。<br>
假设外部数据源是nginx的access_log，为保证数据不丢失（通过ExecSource执行tail –F /path/to/access_log更简单，但在flume-agent进程异常时会丢失数据），我们选用<a href="https://flume.apache.org/FlumeUserGuide.html#spooling-directory-source" rel="nofollow">Spooling Directory Source</a>类型的source，选用memory类型的channel，选用file_roll类型的sink以便调试。<br>
此外，我们借助logrotate工具定时切分nginx的access_log并将切分后的文件移入spooling directory source目录，以便Flume的Source检测并处理该文件。<br>
满足上述需求的配置文件如下所示。<br></span></p>
<pre><code class="language-plain">agent_test.sources = spool-src
agent_test.channels = mem-chan
agent_test.sinks = logger-sink

agent_test.sources.spool-src.type = spooldir
agent_test.sources.spool-src.channels = mem-chan
agent_test.sources.spool-src.spoolDir = /home/slvher/tools/nginx-logs.bak

agent_test.sinks.logger-sink.type = file_roll
agent_test.sinks.logger-sink.channel = mem-chan
agent_test.sinks.logger-sink.sink.directory = /home/slvher/tools/flume-1.5.2/apache-flume-1.5.2-bin/flume_sink.dump
agent_test.sinks.logger-sink.sink.rollInterval = 3600

agent_test.channels.mem-chan.type = memory
agent_test.channels.mem-chan.capacity = 10000</code></pre>备注：logrotate工具的用法之前的<a href="http://blog.csdn.net/slvher/article/details/44200613" rel="nofollow">一篇笔记</a>提到过，这里略过。
<p></p>
<p><span style="font-size:14px;"><strong>1.2<span> </span>
测试</strong><br>
配置完成后，启动Flume，参考命令如下：<br></span></p>
<pre><code class="language-plain">export CLASSPATH=/home/slvher/tools/flume-1.5.2/apache-flume-1.5.2-bin/lib/*.jar:/home/slvher/tools/hadoop-client/java6/lib/*.jar
nohup ./bin/flume-ng agent -n agent_test -c conf -f conf/flume-conf.properties -f conf/log4j.properties &gt; logs/start.log 2&gt;&amp;1 &amp;</code></pre><strong style="background-color:rgb(255,255,51);">注意：</strong>如果在通过./bin/flume-ng脚本启动Flume时遇到如下shell语法错误<br><pre><code class="language-plain">bin/flume-ng: line 102: syntax error in conditional expression: unexpected token `('
bin/flume-ng: line 102: syntax error near `slf4j-(a'
bin/flume-ng: line 102: `if [[ $PIECE =~ slf4j-(api|log4j12).*\.jar ]]; then'</code></pre>则需要修改该提示行处的if条件表达式（<strong>将正则表达式用双引号括起来</strong>），即将原来的<br>
if [[ $PIECE =~ slf4j-(api|log4j12).*\.jar ]]; then<br>
修改为<br>
if [[ $PIECE =~ <span style="background-color:rgb(255,255,51);">"</span>slf4j-(api|log4j12).*\.jar<span style="background-color:rgb(255,255,51);">"</span> ]]; then<br>
启动完成后，可以查看logs/flume.log日志，如果没有WARN或ERROR，则表明Flume启动成功。<br>
我们将logrotate对nginx access_log日志的切分动作设置为周期5minutes的定时任务后，每隔5分钟，nginx-logs.bak目录会生成1个切分文件，而这个文件会被Flume的Source定时扫描并处理，处理完成后，会为原文件追加” .COMPLETED”后缀。<br>
由于我们对sink配置的类型是file_roll，所以，日志通过source-channel-sink传递后，会dump到flume_sink.dump目录下，可在该目录下验证dump是否成功。<br>
在实际业务中，可以根据需求将sink的类型配置为hdfs或其它存储系统对应的sink，此时，sink会将event data写入对应的存储系统中，这里不再赘述。
<p></p>
<p><span style="font-size:14px;"><strong>2. 监控</strong><br></span><span style="font-size:14px;">根据Flume文档关于<a href="https://flume.apache.org/FlumeUserGuide.html#monitoring" rel="nofollow">Monitoring</a>的说明，其监控机制仍在完善中，目前可用的机制是在启动Flume agent时通过-D指定监控的server:port，细节可去查看文档。</span></p>
<p><span style="font-size:14px;">【参考资料】<br>
1. Flume User Guide - <a href="https://flume.apache.org/FlumeUserGuide.html#configuration" rel="nofollow">Configuration</a><br>
2. Flume User Guide - <a href="https://flume.apache.org/FlumeUserGuide.html#monitoring" rel="nofollow">
Monitoring</a></span></p>
<p><span style="font-size:14px;">========================= EOF =========================</span></p>
<p><br></p>
            </div>
                </div>