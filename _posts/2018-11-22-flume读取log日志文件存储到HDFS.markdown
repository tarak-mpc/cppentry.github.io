---
layout:     post
title:      flume读取log日志文件存储到HDFS
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p><a href="https://blog.csdn.net/chy2z/article/details/80484848" rel="nofollow">配置hadoop环境</a></p>

<p><a href="https://blog.csdn.net/chy2z/article/details/81184247" rel="nofollow">配置flume环境</a></p>

<p>配置flume文件</p>

<p>D:\Soft\apache-flume-1.8.0-bin\conf </p>

<p>将 flume-conf.properties.template 重新命名为  hdfs.properties</p>

<pre class="has">
<code class="hljs language-bash"># 组装 agent
a1.sources = s1
a1.channels = c1
a1.sinks = k1

# 配置source：从目录中读取文件
a1.sources.s1.type = spooldir
a1.sources.s1.channels = c1
a1.sources.s1.spoolDir = E:\log2s
# 包括所有日志文件
a1.sources.s1.includePattern=^.*$
# 忽略当前正在写入的日志文件
a1.sources.s1.ignorePattern=^.*log$
a1.sources.s1.deletePolicy=never
a1.sources.s1.fileHeader = true
## 增加时间header
a1.sources.s1.interceptors=i1
a1.sources.s1.interceptors.i1.type=timestamp

# 配置channel:缓存到文件中
a1.channels.c1.type = memory
a1.channels.c1.capacity = 10000
a1.channels.c1.transactionCapacity = 1000

# 配置sink:保存到hdfs中
a1.sinks.k1.channel=c1
a1.sinks.k1.type=hdfs
a1.sinks.k1.hdfs.path=hdfs://127.0.0.1:9000/flume/accesslog/%Y-%m-%d
a1.sinks.k1.hdfs.filePrefix=logs
a1.sinks.k1.hdfs.rollInterval=10
a1.sinks.k1.hdfs.rollSize=0
a1.sinks.k1.hdfs.rollCount=0
a1.sinks.k1.hdfs.batchSize=100
a1.sinks.k1.hdfs.writeFormat=Text
a1.sinks.k1.hdfs.minBlockReplicas=1</code></pre>

<p>flume启动命令</p>

<pre class="has">
<code class="hljs">flume-ng agent --conf conf --conf-file ../conf/hdfs.properties --name a1</code></pre>

<p>编写日志java程序</p>

<pre class="has">
<code class="hljs language-java">public class App 
{
    protected static final Logger logger = Logger.getLogger(App.class);


    public static void main( String[] args )
    {
        while (true) {
            logger.info("hello world:"+ String.valueOf(new Date().getTime()));
            try {
                Thread.sleep(500);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}</code></pre>

<p>log4j配置</p>

<pre class="has">
<code class="hljs language-java">### set log levels ###
log4j.rootLogger=INFO, stdout, file

### stdout ###
log4j.appender.stdout=org.apache.log4j.ConsoleAppender
log4j.appender.stdout.Threshold=INFO
log4j.appender.stdout.Target=System.out
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern=%d{yyyy-MM-dd HH:mm:ss} %c{1} [%p] %m%n

### file ###
log4j.appender.file=org.apache.log4j.DailyRollingFileAppender
# 日志路径
log4j.appender.file.file=E:/log2s/log.log
log4j.appender.file.Threshold=INFO
log4j.appender.file.Append=true
# 每分钟生成1个新文件
log4j.appender.file.DatePattern='.'yyyy-MM-dd-HH-mm
log4j.appender.file.layout=org.apache.log4j.PatternLayout
log4j.appender.file.layout.ConversionPattern=%d{yyyy-MM-dd HH:mm:ss} %c{1} [%p] %m%n</code></pre>

<p>启动java程序生成日志</p>

<p><img alt="" class="has" src="https://img-blog.csdn.net/2018072510452736?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NoeTJ6/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></p>

<p>flume执行结果</p>

<pre class="has">
<code class="language-bash">07/24 17:19:27 INFO node.Application: Starting Channel c1
07/24 17:19:27 INFO instrumentation.MonitoredCounterGroup: Monitored counter group for type: CHANNEL, name: c1: Successfully registered new MBean.
07/24 17:19:27 INFO instrumentation.MonitoredCounterGroup: Component type: CHANNEL, name: c1 started
07/24 17:19:27 INFO node.Application: Starting Sink k1
07/24 17:19:27 INFO node.Application: Starting Source s1
07/24 17:19:27 INFO instrumentation.MonitoredCounterGroup: Monitored counter group for type: SINK, name: k1: Successfully registered new MBean.
07/24 17:19:27 INFO source.SpoolDirectorySource: SpoolDirectorySource source starting with directory: E:log2s
07/24 17:19:27 INFO instrumentation.MonitoredCounterGroup: Component type: SINK, name: k1 started
07/24 17:19:27 INFO instrumentation.MonitoredCounterGroup: Monitored counter group for type: SOURCE, name: s1: Successfully registered new MBean.
07/24 17:19:27 INFO instrumentation.MonitoredCounterGroup: Component type: SOURCE, name: s1 started
07/24 17:19:28 INFO avro.ReliableSpoolingFileEventReader: Last read took us just up to a file boundary. Rolling to the next file, if there is one.
07/24 17:19:28 INFO avro.ReliableSpoolingFileEventReader: Preparing to move file E:\log2s\log.log.2018-07-24-16-46 to E:\log2s\log.log.2018-07-24-16-46.COMPLETED
07/24 17:19:28 INFO avro.ReliableSpoolingFileEventReader: Last read took us just up to a file boundary. Rolling to the next file, if there is one.
07/24 17:19:28 INFO avro.ReliableSpoolingFileEventReader: Preparing to move file E:\log2s\log.log.2018-07-24-16-47 to E:\log2s\log.log.2018-07-24-16-47.COMPLETED
07/24 17:19:28 INFO hdfs.HDFSSequenceFile: writeFormat = Text, UseRawLocalFileSystem = false
07/24 17:19:28 INFO hdfs.BucketWriter: Creating hdfs://127.0.0.1:9000/flume/accesslog/2018-07-24/logs.1532423968027.tmp
07/24 17:19:39 INFO hdfs.BucketWriter: Closing hdfs://127.0.0.1:9000/flume/accesslog/2018-07-24/logs.1532423968027.tmp
07/24 17:19:39 INFO hdfs.BucketWriter: Renaming hdfs://127.0.0.1:9000/flume/accesslog/2018-07-24/logs.1532423968027.tmp to hdfs://127.0.0.1:9000/flume/accesslog/2018-07-24/logs.1532423968027
07/24 17:19:39 INFO hdfs.HDFSEventSink: Writer callback called.
07/24 17:19:59 INFO avro.ReliableSpoolingFileEventReader: Last read took us just up to a file boundary. Rolling to the next file, if there is one.
07/24 17:19:59 INFO avro.ReliableSpoolingFileEventReader: Preparing to move file E:\log2s\log.log.2018-07-24-16-48 to E:\log2s\log.log.2018-07-24-16-48.COMPLETED
07/24 17:20:00 INFO avro.ReliableSpoolingFileEventReader: Last read took us just up to a file boundary. Rolling to the next file, if there is one.
07/24 17:20:00 INFO avro.ReliableSpoolingFileEventReader: Preparing to move file E:\log2s\log.log.2018-07-24-17-19 to E:\log2s\log.log.2018-07-24-17-19.COMPLETED
07/24 17:20:02 INFO hdfs.HDFSSequenceFile: writeFormat = Text, UseRawLocalFileSystem = false
07/24 17:20:02 INFO hdfs.BucketWriter: Creating hdfs://127.0.0.1:9000/flume/accesslog/2018-07-24/logs.1532424002903.tmp
07/24 17:20:13 INFO hdfs.BucketWriter: Closing hdfs://127.0.0.1:9000/flume/accesslog/2018-07-24/logs.1532424002903.tmp
07/24 17:20:13 INFO hdfs.BucketWriter: Renaming hdfs://127.0.0.1:9000/flume/accesslog/2018-07-24/logs.1532424002903.tmp to hdfs://127.0.0.1:9000/flume/accesslog/2018-07-24/logs.1532424002903
07/24 17:20:13 INFO hdfs.HDFSEventSink: Writer callback called.
07/24 17:21:00 INFO hdfs.HDFSSequenceFile: writeFormat = Text, UseRawLocalFileSystem = false
07/24 17:21:00 INFO avro.ReliableSpoolingFileEventReader: Last read took us just up to a file boundary. Rolling to the next file, if there is one.
07/24 17:21:00 INFO avro.ReliableSpoolingFileEventReader: Preparing to move file E:\log2s\log.log.2018-07-24-17-20 to E:\log2s\log.log.2018-07-24-17-20.COMPLETED
07/24 17:21:00 INFO hdfs.BucketWriter: Creating hdfs://127.0.0.1:9000/flume/accesslog/2018-07-24/logs.1532424060382.tmp
07/24 17:21:10 INFO hdfs.BucketWriter: Closing hdfs://127.0.0.1:9000/flume/accesslog/2018-07-24/logs.1532424060382.tmp
07/24 17:21:10 INFO hdfs.BucketWriter: Renaming hdfs://127.0.0.1:9000/flume/accesslog/2018-07-24/logs.1532424060382.tmp to hdfs://127.0.0.1:9000/flume/accesslog/2018-07-24/logs.1532424060382
07/24 17:21:10 INFO hdfs.HDFSEventSink: Writer callback called.</code></pre>

<p>HDFS目录</p>

<p><img alt="" class="has" src="https://img-blog.csdn.net/20180725104936555?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NoeTJ6/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></p>            </div>
                </div>