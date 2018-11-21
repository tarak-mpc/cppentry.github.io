---
layout:     post
title:      flume ng arc and configuration
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <div class="iteye-blog-content-contain">
<p style="font-size:14px;">Please ref flume user guide first</p>
<p style="font-size:14px;"><a href="http://flume.apache.org/FlumeUserGuide.html" rel="nofollow">http://flume.apache.org/FlumeUserGuide.html</a></p>
<p style="font-size:14px;">And the Cloudera flume blogs</p>
<p style="font-size:14px;"><a href="http://blog.cloudera.com/blog/category/flume/" rel="nofollow">http://blog.cloudera.com/blog/category/flume/</a></p>
<p style="font-size:14px;"> </p>
<p class="MsoListParagraph"><strong>How to define JAVA_HOME, java options and add our customized lib into flume-ng.</strong></p>
<p class="MsoListParagraph">All these information will be defined in FLUME_CONFI_DIR/flume-env.sh</p>
<p class="MsoListParagraph">Example like below.</p>
<p class="MsoListParagraph">JAVA_HOME=/opt/java </p>
<p class="MsoListParagraph">JAVA_OPTS="-Xms200m -Xmx200m -Dcom.sun.management.jmxremote -Dcom.sun.management.jmxremote.ssl=false -Dcom.sun.management.jmxremote.authenticate=false -Dcom.sun.management.jmxremote.port=3669 -Dflume.called.from.service" </p>
<p class="MsoListParagraph">FLUME_CLASSPATH=/opt/sponge/flume/lib/*</p>
<p class="MsoListParagraph"> </p>
<p class="MsoListParagraph"><strong>How start flume-ng as agent</strong></p>
<p class="MsoListParagraph">Please note we should name the flume collector name to hostname_agent and this name will be used in the flume-conf-agent.properties</p>
<p class="MsoListParagraph">$/usr/lib/flume/bin/flume-ng agent --conf /opt/sponge/flume/config/   --conf-file /opt/sponge/flume/conf/flume-conf-agent.properties  --name hostname_agent &amp;</p>
<p class="MsoListParagraph"> </p>
<p class="MsoListParagraph"><strong>How to start flume-en as collector</strong></p>
<p class="MsoListParagraph">Please note we should name the flume collector name to hostname_collector and this name will be used in the flume-conf-collector.properties</p>
<p class="MsoListParagraph">     $/usr/lib/flume/bin/flume-ng agent --conf /opt/sponge/flume/config/   --conf-file /opt/sponge/flume/conf/flume-conf-collector.properties  --name hostname_collector &amp;</p>
<p class="MsoListParagraph"> </p>
<p class="MsoListParagraph"><strong>How to define the flume agent and flume collector property file.</strong></p>
<p class="MsoListParagraph">I’ve already committed 2 different property files to https://svn.nam.nsroot.net:9050/svn/153299/elf/sponge-branches/2013-03-14-FlumeNG/sponge/myflumeng/config</p>
<p class="MsoListParagraph">Please ref flume-conf-agent.properties and flume-conf-collector.properties.</p>
<p class="MsoListParagraph">The basic name convention are</p>
<p class="MsoListParagraph">1)each agent name will be set as hostname_agent</p>
<p class="MsoListParagraph">2)each collector name will be set as hostname_collector</p>
<p class="MsoListParagraph">3)the source names will be source1, source2,source3…..</p>
<p class="MsoListParagraph">4)the sink name will be avroSink1, avroSink2, avroSink3….</p>
<p class="MsoListParagraph">5)each sink’s interceptor will be set as interceptor1, interceptor2, interceptor3 ….</p>
<p class="MsoListParagraph">6)all agent sinks will be AVRO sink.</p>
<p class="MsoListParagraph">7)the default collector source is AVRO source</p>
<p class="MsoListParagraph">8)agent sinks are load balanced as round robin</p>
<p class="MsoListParagraph">9)file channel is default for both agent and collector</p>
<p class="MsoListParagraph"> </p>
<p class="MsoListParagraph"><strong>flume-conf-agent.properties</strong></p>
<p class="MsoListParagraph">hostname_agent.sources = source1, source2</p>
<p class="MsoListParagraph">hostname_agent.channels = fileChannel</p>
<p class="MsoListParagraph">hostname_agent.sinks = avroSink1, avroSink2</p>
<p class="MsoListParagraph"> </p>
<p class="MsoListParagraph"># For each one of the sources, the type is defined</p>
<p class="MsoListParagraph">hostname_agent.sources.source1.type = exec</p>
<p class="MsoListParagraph">hostname_agent.sources.source1.command = tail -F /var/log/audit/audit.log</p>
<p class="MsoListParagraph">hostname_agent.sources.source1.channels = fileChannel</p>
<p class="MsoListParagraph">hostname_agent.sources.source1.batchSize=10</p>
<p class="MsoListParagraph"> </p>
<p class="MsoListParagraph">hostname_agent.sources.source2.type = exec</p>
<p class="MsoListParagraph">hostname_agent.sources.source2.command = tail -F /var/log/flume/flume.log</p>
<p class="MsoListParagraph">hostname_agent.sources.source2.channels = fileChannel</p>
<p class="MsoListParagraph">hostname_agent.sources.source2.batchSize=10</p>
<p class="MsoListParagraph"> </p>
<p class="MsoListParagraph"># For each one of the sources, the log interceptor is defined</p>
<p class="MsoListParagraph">hostname_agent.sources.source1.interceptors = logIntercept1</p>
<p class="MsoListParagraph">hostname_agent.sources.source1.interceptors.logIntercept1.type = com.citi.sponge.flume.sink.LogInterceptor$Builder</p>
<p class="MsoListParagraph">hostname_agent.sources.source1.interceptors.logIntercept1.preserveExisting = false</p>
<p class="MsoListParagraph">hostname_agent.sources.source1.interceptors.logIntercept1.hostName = hostname</p>
<p class="MsoListParagraph">hostname_agent.sources.source1.interceptors.logIntercept1.env = PROD</p>
<p class="MsoListParagraph">hostname_agent.sources.source1.interceptors.logIntercept1.logType = AUDIT_LOG</p>
<p class="MsoListParagraph">hostname_agent.sources.source1.interceptors.logIntercept1.appId = 111111</p>
<p class="MsoListParagraph">hostname_agent.sources.source1.interceptors.logIntercept1.logFilePath = /var/log/audit</p>
<p class="MsoListParagraph">hostname_agent.sources.source1.interceptors.logIntercept1.logFileName = audit.log</p>
<p class="MsoListParagraph">  </p>
<p class="MsoListParagraph">hostname_agent.sources.source2.interceptors = logIntercept2</p>
<p class="MsoListParagraph">hostname_agent.sources.source2.interceptors.logIntercept2.type = com.citi.sponge.flume.sink.LogInterceptor$Builder</p>
<p class="MsoListParagraph">hostname_agent.sources.source2.interceptors.logIntercept2.preserveExisting = false</p>
<p class="MsoListParagraph">hostname_agent.sources.source2.interceptors.logIntercept2.hostName = hostname</p>
<p class="MsoListParagraph">hostname_agent.sources.source2.interceptors.logIntercept2.env = PROD</p>
<p class="MsoListParagraph">hostname_agent.sources.source2.interceptors.logIntercept2.logType = FLUME</p>
<p class="MsoListParagraph">hostname_agent.sources.source2.interceptors.logIntercept2.appId = 111111</p>
<p class="MsoListParagraph">hostname_agent.sources.source2.interceptors.logIntercept2.logFilePath = /var/log/flume</p>
<p class="MsoListParagraph">hostname_agent.sources.source2.interceptors.logIntercept2.logFileName = flume.log</p>
<p class="MsoListParagraph"> </p>
<p class="MsoListParagraph"> </p>
<p class="MsoListParagraph">#for each of the sink, type is defined</p>
<p class="MsoListParagraph">hostname_agent.sinks.avroSink1.type = avro</p>
<p class="MsoListParagraph">hostname_agent.sinks.avroSink1.hostname=collector1</p>
<p class="MsoListParagraph">hostname_agent.sinks.avroSink1.port=1442</p>
<p class="MsoListParagraph">hostname_agent.sinks.avroSink1.batchSize=10 </p>
<p class="MsoListParagraph">hostname_agent.sinks.avroSink1.channel = fileChannel </p>
<p class="MsoListParagraph"> </p>
<p class="MsoListParagraph">hostname_agent.sinks.avroSink2.type = avro</p>
<p class="MsoListParagraph">hostname_agent.sinks.avroSink2.hostname=collector2</p>
<p class="MsoListParagraph">hostname_agent.sinks.avroSink2.port=1442</p>
<p class="MsoListParagraph">hostname_agent.sinks.avroSink2.batchSize=10 </p>
<p class="MsoListParagraph">hostname_agent.sinks.avroSink2.channel = fileChannel </p>
<p class="MsoListParagraph"> </p>
<p class="MsoListParagraph"> </p>
<p class="MsoListParagraph">#Specify the load balance configurations for sinks</p>
<p class="MsoListParagraph">agent.sinkgroups = sinkGroup</p>
<p class="MsoListParagraph">agent.sinkgroups.sinkGroup.sinks = avroSink1 avroSink2</p>
<p class="MsoListParagraph">agent.sinkgroups.sinkGroup.processor.type = load_balance</p>
<p class="MsoListParagraph">agent.sinkgroups.sinkGroup.processor.backoff = true</p>
<p class="MsoListParagraph">agent.sinkgroups.sinkGroup.processor.selector = round_robin</p>
<p class="MsoListParagraph">agent.sinkgroups.sinkGroup.processor.selector.maxBackoffMillis=30000</p>
<p class="MsoListParagraph"> </p>
<p class="MsoListParagraph"> </p>
<p class="MsoListParagraph"># Each channel's type is defined.</p>
<p class="MsoListParagraph">hostname_agent.channels.fileChannel.type = file</p>
<p class="MsoListParagraph">hostname_agent.channels.fileChannel.checkpointDir = /opt/sponge/file-channel/checkpoint</p>
<p class="MsoListParagraph">hostname_agent.channels.fileChannel.dataDirs = /opt/sponge/file-channel/dataDirs</p>
<p class="MsoListParagraph">hostname_agent.channels.fileChannel.transactionCapacity = 1000</p>
<p class="MsoListParagraph">hostname_agent.channels.fileChannel.checkpointInterval = 30000</p>
<p class="MsoListParagraph">hostname_agent.channels.fileChannel.maxFileSize = 2146435071</p>
<p class="MsoListParagraph">hostname_agent.channels.fileChannel.minimumRequiredSpace = 524288000</p>
<p class="MsoListParagraph">hostname_agent.channels.fileChannel.keep-alive = 5</p>
<p class="MsoListParagraph">hostname_agent.channels.fileChannel.write-timeout = 5</p>
<p class="MsoListParagraph">hostname_agent.channels.fileChannel.checkpoint-timeout = 600</p>
<p class="MsoListParagraph"> </p>
<p class="MsoListParagraph"><strong>flume-collector.properties</strong></p>
<p class="MsoListParagraph">hostname_collector.sources = source</p>
<p class="MsoListParagraph">hostname_collector.channels = fileChannel</p>
<p class="MsoListParagraph">hostname_collector.sinks = hbaseSink</p>
<p class="MsoListParagraph"> </p>
<p class="MsoListParagraph"> </p>
<p class="MsoListParagraph"># For each one of the sources, the type is defined</p>
<p class="MsoListParagraph">hostname_collector.sources.avroSource.channels = fileChannel</p>
<p class="MsoListParagraph">hostname_collector.sources.avroSource.type = avro</p>
<p class="MsoListParagraph">hostname_collector.sources.avroSource.bind = hostname</p>
<p class="MsoListParagraph">hostname_collector.sources.avroSource.port = 1442 </p>
<p class="MsoListParagraph"> </p>
<p class="MsoListParagraph">hostname_collector.sinks.hbaseSink.type=org.apache.flume.sink.hbase.HBaseSink</p>
<p class="MsoListParagraph">hostname_collector.sinks.hbaseSink.table=spong_flumeng_log2</p>
<p class="MsoListParagraph">hostname_collector.sinks.hbaseSink.columnFamily=content</p>
<p class="MsoListParagraph">hostname_collector.sinks.hbaseSink.serializer=com.citi.sponge.flume.sink.LogHbaseEventSerializer</p>
<p class="MsoListParagraph">hostname_collector.sinks.hbaseSink.timeout=120</p>
<p class="MsoListParagraph">hostname_collector.sinks.hbaseSink.column=log</p>
<p class="MsoListParagraph">hostname_collector.sinks.hbaseSink.batchSize=2</p>
<p class="MsoListParagraph">hostname_collector.sinks.hbaseSink.channel=fileChannel</p>
<p class="MsoListParagraph"> </p>
<p class="MsoListParagraph"> </p>
<p class="MsoListParagraph"># Each channel's type is defined.</p>
<p class="MsoListParagraph">hostname_collector.channels.fileChannel.type = file</p>
<p class="MsoListParagraph">hostname_collector.channels.fileChannel.checkpointDir = /opt/sponge/file-channel/checkpoint</p>
<p class="MsoListParagraph">hostname_collector.channels.fileChannel.dataDirs = /opt/sponge/file-channel/dataDirs</p>
<p class="MsoListParagraph">hostname_collector.channels.fileChannel.transactionCapacity = 1000</p>
<p class="MsoListParagraph">hostname_collector.channels.fileChannel.checkpointInterval = 30000</p>
<p class="MsoListParagraph">hostname_collector.channels.fileChannel.maxFileSize = 2146435071</p>
<p class="MsoListParagraph">hostname_collector.channels.fileChannel.minimumRequiredSpace = 524288000</p>
<p class="MsoListParagraph">hostname_collector.channels.fileChannel.keep-alive = 5</p>
<p class="MsoListParagraph">hostname_collector.channels.fileChannel.write-timeout = 5</p>
<p class="MsoListParagraph">hostname_collector.channels.fileChannel.checkpoint-timeout = 600</p>
</div>            </div>
                </div>