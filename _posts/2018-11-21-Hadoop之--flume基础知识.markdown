---
layout:     post
title:      Hadoop之--flume基础知识
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div><br></div>
<div style="background-color:rgb(251,250,248);">
<div><span style="background-color:rgb(255,250,165);color:rgb(255,0,0);font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;">Flume：</span></div>
<div><span style="background-color:rgb(255,250,165);color:rgb(51,51,51);font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;">是Cloudera提供的一个高可用的，高可靠的，分布式的海量日志采集、聚合和传输的系统，Flume支持在日志系统中定制各类数据发送方，用于收集数据；Flume提供对数据进行简单处理，并写到各种数据接受方（可定制）的能力。</span></div>
</div>
<div><br></div>
<div style="background-color:rgb(251,250,248);">
<div><span style="color:rgb(51,51,51);font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;">当前Flume有两个版本Flume 0.9X版本的统称Flume-og，Flume1.X版本的统称Flume-ng。</span></div>
<div><span style="color:rgb(51,51,51);font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;">由于Flume-ng经过重大重构，与Flume-og有很大不同，使用时请注意区分。</span></div>
<div><span style="background-color:rgb(255,250,165);color:rgb(51,51,51);font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;">注意：og和ng的区别 old和new（Flume-og已经不再更新了）</span></div>
<div><span style="color:rgb(51,51,51);font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;">简单概述一下Fulme OG基本架构</span></div>
<div><span style="background-color:rgb(255,250,165);color:rgb(51,51,51);font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;">Flume-og使用了两个组件：Master(主节点)和Node(子节点)</span></div>
<div><span style="background-color:rgb(255,250,165);color:rgb(51,51,51);font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;">master(配置管理和协调)</span></div>
<div><span style="background-color:rgb(255,250,165);color:rgb(51,51,51);font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;">{</span></div>
<div><span style="background-color:rgb(255,250,165);color:rgb(51,51,51);font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;">agent(负责数据源的采集)--&gt;collector(负责收集汇总agent采集的数据) --&gt;storage(选择写入本地文件系统还是HDFS)</span></div>
<div><span style="background-color:rgb(255,250,165);color:rgb(51,51,51);font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;">}</span></div>
</div>
<div><br></div>
<div><span style="font-size:12pt;color:#FF0000;">Fulme NG</span><span style="font-size:12pt;color:#FF0000;">基本架构</span><span style="font-size:12pt;">（</span><span style="font-size:12pt;color:#FF0000;">必须安装JDK6.0以上的版本，并且只支持Linux系统中的脚本，没有Windows环境的启动脚本</span><span style="font-size:12pt;">）</span></div>
<div><span style="font-size:12pt;">核心组件：1、</span><span style="font-size:19px;background-color:rgb(255,250,165);color:rgb(255,0,0);"><strong>source</strong></span>
<span style="font-size:16px;">2、</span><span style="font-size:12pt;background-color:rgb(255,250,165);color:#FF0000;"><strong>channel</strong></span>
<span style="font-size:12pt;">3、</span><span style="background-color:rgb(255,250,165);font-size:19px;color:#FF0000;"><strong>sink</strong></span></div>
<div><br></div>
<div style="background-color:rgb(251,250,248);">
<div><span style="font-size:19px;background-color:rgb(255,250,165);color:rgb(255,0,0);font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;"><strong>source</strong></span><span style="font-size:19px;color:rgb(51,51,51);font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;">：</span></div>
<div><span style="color:rgb(51,51,51);font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;">对现有程序改动最小的方式是使用是直接读取程序原来记录的日志文件，基本可以实现无缝介入，不需要对现有程序进行任何改动，直接读取文件</span></div>
<div><br></div>
<div><span style="color:rgb(51,51,51);font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;">Source有两种方式：1：</span><span style="background-color:rgb(255,250,165);color:rgb(51,51,51);font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;">Exec Source</span><span style="color:rgb(51,51,51);font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;"> 2：</span><span style="background-color:rgb(255,250,165);color:rgb(51,51,51);font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;">SpoolSource</span></div>
<div><span style="background-color:rgb(255,250,165);color:rgb(51,51,51);font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;">Exec Source</span><span style="color:rgb(51,51,51);font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;">：</span></div>
<div><span style="color:rgb(51,51,51);font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;">以运行Linux命令的方式，持续的输出最新的数据，如 tail -f 文件名称指令，在这种方式下，取的文件名必须是指定的。Spool Source：是扫描指定目录下新增的文件，并将文件中的数据读取出来。</span></div>
<div><br></div>
<div><span style="color:rgb(51,51,51);font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;">使用</span><span style="background-color:rgb(255,250,165);color:rgb(51,51,51);font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;">SpoolSource</span><span style="color:rgb(51,51,51);font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;">需要注意：</span></div>
<div><span style="color:rgb(51,51,51);font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;">1：拷贝到spool目录下的文件不可以再打开编辑。</span></div>
<div><span style="color:rgb(51,51,51);font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;">2：spool目录下不可以包含相应的子目录。</span></div>
<div><span style="color:rgb(51,51,51);font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;">   SpoolSource在实际使用过程中，可以结合log4j使用，使用log4j的时候，将log4j的文件分割机制设为1分钟一次，将文件拷贝到 spool的监控目录。log4j有个TimeRolling的插件，可以把log4j分割的文件到spool目录。基本实现了实时的监控。Flume在 传完文件之后，将会修改文件的后缀，变为.COMPLETED（后缀也可以在配制文件中灵活制定）。</span></div>
<div><span style="color:rgb(51,51,51);font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;">Exec Source和Spool Source比较</span></div>
<div><span style="color:rgb(51,51,51);font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;">1：</span><span style="background-color:rgb(255,250,165);color:rgb(51,51,51);font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;">Exec Source可以实现对日志的实时收集，但是存在Flume不允许或者指令执行出错时，将无法收集到日志数据，无法何证数据的完整性</span><span style="color:rgb(51,51,51);font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;">。</span></div>
<div><span style="color:rgb(51,51,51);font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;">2：</span><span style="background-color:rgb(255,250,165);color:rgb(51,51,51);font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;">SpoolSource虽然无法实现实时的收集数据，但是可以使用以分钟的方式分割文件，趋近于实时。</span></div>
<div><span style="background-color:rgb(255,250,165);color:rgb(51,51,51);font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;">总结：如果是应用无法实现以分钟切割日志文件的话，可以两种收集方式结合使用</span><span style="color:rgb(51,51,51);font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;">。</span></div>
</div>
<div><br></div>
<div style="background-color:rgb(251,250,248);">
<div><span style="background-color:rgb(255,250,165);font-size:19px;color:rgb(255,0,0);font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;"><strong>channel</strong></span></div>
<div><span style="background-color:rgb(255,250,165);color:rgb(51,51,51);font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;">FlumeChannel主要提供一个队列的功能，对source提供中的数据进行简单的缓存。</span></div>
<div><span style="color:rgb(51,51,51);font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;">Flume对于Channel，则提供了MemoryChannel、JDBCChannel、FileChannel，etc。</span></div>
<div><span style="color:rgb(51,51,51);font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;">1：MemoryChannel可以实现高速的吞吐，但是无法保证数据的完整性。</span></div>
<div><span style="color:rgb(51,51,51);font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;">2：MemoryRecoverChannel在官方文档的建议上已经建议使用FileChannel来替换。</span></div>
<div><span style="color:rgb(51,51,51);font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;">3：FileChannel保证数据的完整性与一致性。在具体配置不现的FileChannel时，建议FileChannel设置的目录和程序日志文件的目录设置成不同的磁盘，以便提高效率。</span></div>
</div>
<div><span style="font-size:12pt;font-family:'宋体';"> </span></div>
<div style="background-color:rgb(251,250,248);">
<div><span style="background-color:rgb(255,250,165);font-size:19px;color:rgb(255,0,0);font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;"><strong>sink</strong></span></div>
<div><span style="background-color:rgb(255,250,165);color:rgb(51,51,51);font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;">Flume Sink取出Channel中的数据，进行相应的存储文件系统，数据库，或者提交到远程服务器。</span></div>
<div><span style="color:rgb(51,51,51);font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;">Flume也提供了各种sink的实现，包括HDFSsink、loggersink、Avrosink、FileRollsink、Nullsink、HBasesink，etc。</span></div>
<div><span style="color:rgb(51,51,51);font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;">Flume Sink在设置存储数据时，可以向文件系统中，数据库中，hadoop中村塾数据，在日志数据较少时，可以将数据存储在文件系统中，并且设定一定的时间间 隔保存数据。在日志数据较多时，可以将相应的日志数据存储到Hadoop中，便于日后进行相应的数据分析。</span></div>
<div><span style="color:rgb(51,51,51);font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;">   Flume Source完成对日志数据的收集，分成transtion和event打入到channel中。</span></div>
<div><span style="color:rgb(51,51,51);font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;">   Flume 提供了各种source的实现，包括AvroSource、ExceSource、SpoolingDirectorySource、 NetCatSource、SyslogSource、SyslogTcpSource、syslogUDPSource、HTTPSource、 HDFSSource、etc。</span></div>
</div>
<div></div>
            </div>
                </div>