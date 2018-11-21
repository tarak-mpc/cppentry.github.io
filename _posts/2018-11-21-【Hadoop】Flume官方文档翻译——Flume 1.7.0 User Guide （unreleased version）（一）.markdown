---
layout:     post
title:      【Hadoop】Flume官方文档翻译——Flume 1.7.0 User Guide （unreleased version）（一）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<p align="left" style="text-align:center;"><strong><span style="font-size:18pt;">Flume 1.7.0 User Guide</span></strong></p>
<p align="left" style="text-align:left;"></p>
<ul style="margin-left:0px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;"><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#introduction" rel="nofollow">Introduction</a>（简介）
<ul style="margin-left:0px;"><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#overview" rel="nofollow">Overview</a>（综述）</li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#system-requirements" rel="nofollow">System Requirements</a>（系统需求）</li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#architecture" rel="nofollow">Architecture</a>（架构）
<ul style="margin-left:0px;"><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#data-flow-model" rel="nofollow">Data flow model</a>（数据流模型）</li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#complex-flows" rel="nofollow">Complex flows</a>（复杂流）</li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#reliability" rel="nofollow">Reliability</a>（可靠性）</li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#recoverability" rel="nofollow">Recoverability</a>（可恢复性）</li></ul></li></ul></li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#setup" rel="nofollow">Setup</a>（配置）<a href="http://flume.apache.org/FlumeUserGuide.html#configuration" rel="nofollow">Configuration</a>（配置
<ul style="margin-left:0px;"><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#setting-up-an-agent" rel="nofollow">Setting up an agent</a>（设置一个agent）
<ul style="margin-left:0px;"><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#configuring-individual-components" rel="nofollow">Configuring individual components</a>（配置单个组件）</li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#wiring-the-pieces-together" rel="nofollow">Wiring the pieces together</a>（碎片聚集）</li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#starting-an-agent" rel="nofollow">Starting an agent</a>（开始一个agent）</li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#a-simple-example" rel="nofollow">A simple example</a>（一个简单的例子）</li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#logging-raw-data" rel="nofollow">Logging raw data</a>（记录原始数据）</li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#zookeeper-based-configuration" rel="nofollow">Zookeeper based Configuration</a>（ZooKeeper的基础配置）</li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#installing-third-party-plugins" rel="nofollow">Installing third-party plugins</a>（安装第三方插件）
<ul style="margin-left:0px;"><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#the-plugins-d-directory" rel="nofollow">The plugins.d directory</a>（插件目录）</li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#directory-layout-for-plugins" rel="nofollow">Directory layout for plugins</a>（用于插件的目录布局）</li></ul></li></ul></li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#data-ingestion" rel="nofollow">Data ingestion</a>（数据获取）
<ul style="margin-left:0px;"><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#rpc" rel="nofollow">RPC</a>（远程调用）</li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#executing-commands" rel="nofollow">Executing commands</a>（执行命令）</li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#network-streams" rel="nofollow">Network streams</a>（网络流）</li></ul></li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#setting-multi-agent-flow" rel="nofollow">Setting multi-agent flow</a>（设置多个agent流）</li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#consolidation" rel="nofollow">Consolidation</a>（合并）</li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#multiplexing-the-flow" rel="nofollow">Multiplexing the flow</a>（多路复用流）</li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#defining-the-flow" rel="nofollow">Defining the flow</a>（定义一个流）</li></ul></li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#configuration" rel="nofollow">Configuration</a>
<ul style="margin-left:0px;"><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#defining-the-flow" rel="nofollow">Defining the flow</a></li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#id1" rel="nofollow">Configuring individual components</a>（配置单个组件）</li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#adding-multiple-flows-in-an-agent" rel="nofollow">Adding multiple flows in an agent</a>（一个agent中增加多个流）</li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#configuring-a-multi-agent-flow" rel="nofollow">Configuring a multi agent flow</a>（配置一个多agent流）</li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#fan-out-flow" rel="nofollow">Fan out flow</a>（扇出流）</li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#flume-sources" rel="nofollow">Flume Sources</a>（各种Source）<br><ul style="margin-left:0px;"><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#avro-source" rel="nofollow">Avro Source</a></li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#thrift-source" rel="nofollow">Thrift Source</a></li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#exec-source" rel="nofollow">Exec Source</a></li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#jms-source" rel="nofollow">JMS Source</a>
<ul style="margin-left:0px;"><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#converter" rel="nofollow">Converter</a>（转换器）</li></ul></li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#spooling-directory-source" rel="nofollow">Spooling Directory Source</a>
<ul style="margin-left:0px;"><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#event-deserializers" rel="nofollow">Event Deserializers</a>
<ul style="margin-left:0px;"><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#line" rel="nofollow">LINE</a></li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#avro" rel="nofollow">AVRO</a></li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#blobdeserializer" rel="nofollow">BlobDeserializer</a></li></ul></li></ul></li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#taildir-source" rel="nofollow">Taildir Source</a></li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#twitter-1-firehose-source-experimental" rel="nofollow">Twitter 1% firehose Source (experimental)</a></li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#kafka-source" rel="nofollow">Kafka Source</a></li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#netcat-source" rel="nofollow">NetCat Source</a></li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#sequence-generator-source" rel="nofollow">Sequence Generator Source</a></li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#syslog-sources" rel="nofollow">Syslog Sources</a>
<ul style="margin-left:0px;"><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#syslog-tcp-source" rel="nofollow">Syslog TCP Source</a></li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#multiport-syslog-tcp-source" rel="nofollow">Multiport Syslog TCP Source</a></li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#syslog-udp-source" rel="nofollow">Syslog UDP Source</a></li></ul></li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#http-source" rel="nofollow">HTTP Source</a>
<ul style="margin-left:0px;"><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#jsonhandler" rel="nofollow">JSONHandler</a></li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#blobhandler" rel="nofollow">BlobHandler</a></li></ul></li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#stress-source" rel="nofollow">Stress Source</a></li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#legacy-sources" rel="nofollow">Legacy Sources</a>
<ul style="margin-left:0px;"><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#avro-legacy-source" rel="nofollow">Avro Legacy Source</a></li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#thrift-legacy-source" rel="nofollow">Thrift Legacy Source</a></li></ul></li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#custom-source" rel="nofollow">Custom Source</a></li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#scribe-source" rel="nofollow">Scribe Source</a></li></ul></li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#flume-sinks" rel="nofollow">Flume Sinks</a>（各种Sink）
<ul style="margin-left:0px;"><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#hdfs-sink" rel="nofollow">HDFS Sink</a></li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#hive-sink" rel="nofollow">Hive Sink</a></li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#logger-sink" rel="nofollow">Logger Sink</a></li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#avro-sink" rel="nofollow">Avro Sink</a></li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#thrift-sink" rel="nofollow">Thrift Sink</a></li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#irc-sink" rel="nofollow">IRC Sink</a></li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#file-roll-sink" rel="nofollow">File Roll Sink</a></li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#null-sink" rel="nofollow">Null Sink</a></li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#hbasesinks" rel="nofollow">HBaseSinks</a>
<ul style="margin-left:0px;"><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#hbasesink" rel="nofollow">HBaseSink</a></li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#asynchbasesink" rel="nofollow">AsyncHBaseSink</a></li></ul></li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#morphlinesolrsink" rel="nofollow">MorphlineSolrSink</a></li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#elasticsearchsink" rel="nofollow">ElasticSearchSink</a></li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#kite-dataset-sink" rel="nofollow">Kite Dataset Sink</a></li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#kafka-sink" rel="nofollow">Kafka Sink</a></li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#custom-sink" rel="nofollow">Custom Sink</a></li></ul></li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#flume-channels" rel="nofollow">Flume Channels</a>（各种Channel）
<ul style="margin-left:0px;"><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#memory-channel" rel="nofollow">Memory Channel</a></li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#jdbc-channel" rel="nofollow">JDBC Channel</a></li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#kafka-channel" rel="nofollow">Kafka Channel</a></li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#file-channel" rel="nofollow">File Channel</a></li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#spillable-memory-channel" rel="nofollow">Spillable Memory Channel</a></li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#pseudo-transaction-channel" rel="nofollow">Pseudo Transaction Channel</a></li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#custom-channel" rel="nofollow">Custom Channel</a></li></ul></li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#flume-channel-selectors" rel="nofollow">Flume Channel Selectors</a>（Channel选择器）
<ul style="margin-left:0px;"><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#replicating-channel-selector-default" rel="nofollow">Replicating Channel Selector (default)</a></li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#multiplexing-channel-selector" rel="nofollow">Multiplexing Channel Selector</a></li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#custom-channel-selector" rel="nofollow">Custom Channel Selector</a></li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#flume-sink-processors" rel="nofollow">Flume Sink Processors</a>（执行器）</li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#default-sink-processor" rel="nofollow">Default Sink Processor</a></li><li style="margin-left:0px;">
<p style="display:inline !important;"><a href="http://flume.apache.org/FlumeUserGuide.html#failover-sink-processor" rel="nofollow">Failover Sink Processor</a></p>
</li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#load-balancing-sink-processor" rel="nofollow">Load balancing Sink Processor</a></li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#custom-sink-processor" rel="nofollow">Custom Sink Processor</a></li></ul></li></ul></li></ul><ul style="margin-left:0px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;"><li style="margin-left:0px;list-style-type:none;">
<ul style="margin-left:0px;"><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#event-serializers" rel="nofollow">Event Serializers</a>（序列化器）
<ul style="margin-left:0px;"><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#body-text-serializer" rel="nofollow">Body Text Serializer</a></li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#flume-event-avro-event-serializer" rel="nofollow">“Flume Event” Avro Event Serializer</a></li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#avro-event-serializer" rel="nofollow">Avro Event Serializer</a></li></ul></li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#flume-interceptors" rel="nofollow">Flume Interceptors</a>（拦截器）
<ul style="margin-left:0px;"><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#timestamp-interceptor" rel="nofollow">Timestamp Interceptor</a></li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#host-interceptor" rel="nofollow">Host Interceptor</a></li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#static-interceptor" rel="nofollow">Static Interceptor</a></li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#uuid-interceptor" rel="nofollow">UUID Interceptor</a></li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#morphline-interceptor" rel="nofollow">Morphline Interceptor</a></li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#search-and-replace-interceptor" rel="nofollow">Search and Replace Interceptor</a></li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#regex-filtering-interceptor" rel="nofollow">Regex Filtering Interceptor</a></li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#regex-extractor-interceptor" rel="nofollow">Regex Extractor Interceptor</a></li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#example-1" rel="nofollow">Example 1:</a></li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#example-2" rel="nofollow">Example 2:</a></li></ul></li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#flume-properties" rel="nofollow">Flume Properties</a>（属性）
<ul style="margin-left:0px;"><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#property-flume-called-from-service" rel="nofollow">Property: flume.called.from.service</a></li></ul></li></ul></li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#log4j-appender" rel="nofollow">Log4J Appender</a>（日志存储器）</li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#load-balancing-log4j-appender" rel="nofollow">Load Balancing Log4J Appender</a>（负载均衡的日志存储器）</li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#security" rel="nofollow">Security</a>（安全性）</li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#monitoring" rel="nofollow">Monitoring</a>（监控）
<ul style="margin-left:0px;"><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#jmx-reporting" rel="nofollow">JMX Reporting</a></li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#ganglia-reporting" rel="nofollow">Ganglia Reporting</a></li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#json-reporting" rel="nofollow">JSON Reporting</a></li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#custom-reporting" rel="nofollow">Custom Reporting</a></li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#reporting-metrics-from-custom-components" rel="nofollow">Reporting metrics from custom components</a></li></ul></li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#tools" rel="nofollow">Tools</a>（工具）
<ul style="margin-left:0px;"><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#file-channel-integrity-tool" rel="nofollow">File Channel Integrity Tool</a></li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#event-validator-tool" rel="nofollow">Event Validator Tool</a></li></ul></li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#topology-design-considerations" rel="nofollow">Topology Design Considerations</a>（拓扑结构设计考虑）
<ul style="margin-left:0px;"><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#is-flume-a-good-fit-for-your-problem" rel="nofollow">Is Flume a good fit for your problem?</a></li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#flow-reliability-in-flume" rel="nofollow">Flow reliability in Flume</a></li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#flume-topology-design" rel="nofollow">Flume topology design</a></li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#sizing-a-flume-deployment" rel="nofollow">Sizing a Flume deployment</a></li></ul></li><li style="margin-left:0px;">
<p style="display:inline !important;"><a href="http://flume.apache.org/FlumeUserGuide.html#troubleshooting" rel="nofollow">Troubleshooting</a>（故障排除）</p>
<ul style="margin-left:0px;"><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#handling-agent-failures" rel="nofollow">Handling agent failures</a></li><li style="margin-left:0px;list-style-type:none;"><a href="http://flume.apache.org/FlumeUserGuide.html#compatibility" rel="nofollow">Compatibility</a>
<ul style="margin-left:0px;"><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#hdfs" rel="nofollow">HDFS</a></li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#id14" rel="nofollow">AVRO</a></li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#additional-version-requirements" rel="nofollow">Additional version requirements</a></li></ul></li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#tracing" rel="nofollow">Tracing</a></li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#more-sample-configs" rel="nofollow">More Sample Configs</a></li></ul></li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#component-summary" rel="nofollow">Component Summary</a>（组件总结）</li><li style="margin-left:0px;"><a href="http://flume.apache.org/FlumeUserGuide.html#alias-conventions" rel="nofollow">Alias Conventions</a>（别名约定）</li></ul><br><p></p>
<h1 align="left" style="margin-left:30px;">Introduction（简介）</h1>
<h2 align="left" style="margin-left:30px;">Overview（综述）</h2>
</div>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
Apache Flume is a distributed, reliable, and available system for efficiently collecting, aggregating and moving large amounts of log data from many different sources to a centralized data store.</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
The use of Apache Flume is not only restricted to log data aggregation. Since data sources are customizable, Flume can be used to transport massive quantities of event data including but not limited to network traffic data, social-media-generated data, email
 messages and pretty much any data source possible.</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
Apache Flume is a top level project at the Apache Software Foundation.</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
There are currently two release code lines available, versions 0.9.x and 1.x.</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
Documentation for the 0.9.x track is available at <a href="http://archive.cloudera.com/cdh/3/flume/UserGuide/" rel="nofollow">the Flume 0.9.x User Guide</a>.</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
This documentation applies to the 1.4.x track.</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
New and existing users are encouraged to use the 1.x releases so as to leverage the performance improvements and configuration flexibilities available in the latest architecture.</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
Apache Flume是一个分布式、高可靠和高可用的收集、集合和将大量来自不同来源的日志数据移动到一个中央数据仓库。</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
Apache Flume不仅局限于数据的聚集。因为数据是可定制的，所以Flume可以用于运输大量时间数据包括不限于网络传输数据，社交媒体产生的数据，电子邮件信息和几乎任何数据源。</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
Apache Flume是Apache软件基金会的顶级项目。</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
目前有两个可用的发布版本，0.9.x和1.x。</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
我们鼓励新老用户使用1.x发布版本来提高性能和利用新结构的配置灵活性。</p>
<h2 style="font-family:verdana, Arial, Helvetica, sans-serif;margin-left:30px;">System Requirements（系统需求）</h2>
<ol start="1" style="margin-left:0px;font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;"><li style="margin-left:0px;list-style-type:none;">
<ol style="margin-left:0px;"><li style="margin-left:0px;">Java Runtime Environment - Java 1.7 or later（Java运行环境-Java1.7或者以后的版本）</li><li style="margin-left:0px;">Memory - Sufficient memory for configurations used by sources, channels or sinks（内存——足够的内存来配置souuces，channels和sinks）</li><li style="margin-left:0px;">Disk Space - Sufficient disk space for configurations used by channels or sinks（磁盘空间-足够的磁盘空间来配置channels或者sinks）</li><li style="margin-left:0px;">Directory Permissions - Read/Write permissions for directories used by agent（目录权限-代理所使用的目录读/写权限）</li></ol></li></ol><div style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;margin-left:30px;">
<h2 align="left">Architecture（架构）</h2>
<h3 align="left">Data flow model（数据流动模型）</h3>
</div>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
A Flume event is defined as a unit of data flow having a byte payload and an optional set of string attributes. A Flume agent is a (JVM) process that hosts the components through which events flow from an external source to the next destination (hop).</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
一个Flume event被定义为拥有一个字节的有效负载的一个数据流单元和一个可选的字符串属性配置。Flume agent是一个JVM进程来控制组件完成事件流从一个外部来源传输到下一个目的地。</p>
<p align="center" style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<img src="http://images2015.cnblogs.com/blog/1045352/201612/1045352-20161205161152069-1543512127.png" alt="" style="border:0px;"> </p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
A Flume source consumes events delivered to it by an external source like a web server. The external source sends events to Flume in a format that is recognized by the target Flume source. For example, an Avro Flume source can be used to receive Avro events
 from Avro clients or other Flume agents in the flow that send events from an Avro sink. A similar flow can be defined using a Thrift Flume Source to receive events from a Thrift Sink or a Flume Thrift Rpc Client or Thrift clients written in any language generated
 from the Flume thrift protocol.When a Flume source receives an event, it stores it into one or more channels. The channel is a passive store that keeps the event until it’s consumed by a Flume sink. The file channel is one example – it is backed by the local
 filesystem. The sink removes the event from the channel and puts it into an external repository like HDFS (via Flume HDFS sink) or forwards it to the Flume source of the next Flume agent (next hop) in the flow. The source and sink within the given agent run
 asynchronously with the events staged in the channel.</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
Flume source消费外部来源像web server传输给他的事件。外部来源发送以目标Flume source定义好的格式的event给Flume。例如，Avro Flume source用于接收Avro客户端或者流中的其他Flume中Avro sink发来的Avro events。一个相似的流可以用Thrift Flume Source 来接收来自Flume sink或者FluemThrift Rpc客户端或者一个用任何语言写的遵守Flume Thrift 协议的Thrift客户端的事件。当一个Flume
 Source接收一个事件时，它将事件存储在一个或者多个Cannel中。Channel是一个被动仓库用来保存事件直到它被Flume Sink消费掉。File channel就是个例子-它背靠着本地的文件系统。Sink将事件从Channel中移除并且将事件放到一个外部的仓库像HDFS（通过Flume HDFS sink）或者向前传输到流中另一个Flume Agent。Agent中Source和Sink异步地执行Channel中events。</p>
<div style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;margin-left:30px;">
<h3 align="left">Complex flows（复杂流）</h3>
</div>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
Flume allows a user to build multi-hop flows where events travel through multiple agents before reaching the final destination. It also allows fan-in and fan-out flows, contextual routing and backup routes (fail-over) for failed hops.</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
Flume允许一些用户建立multi-hop流当事件在到达最终目的地时要经过多个Agent。它也允许扇入和扇出流，上下文路由和失效hop的恢复路由。</p>
<div style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;margin-left:30px;">
<h3 align="left">Reliability（可靠性）</h3>
</div>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
The events are staged in a channel on each agent. The events are then delivered to the next agent or terminal repository (like HDFS) in the flow. The events are removed from a channel only after they are stored in the channel of next agent or in the terminal
 repository. This is a how the single-hop message delivery semantics in Flume provide end-to-end reliability of the flow.</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
Flume uses a transactional approach to guarantee the reliable delivery of the events. The sources and sinks encapsulate in a transaction the storage/retrieval, respectively, of the events placed in or provided by a transaction provided by the channel. This
 ensures that the set of events are reliably passed from point to point in the flow. In the case of a multi-hop flow, the sink from the previous hop and the source from the next hop both have their transactions running to ensure that the data is safely stored
 in the channel of the next hop.</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
事件都是（存储）在每个agent中的Channel。事件会被传送到下一个Agent或者流中的最终目的地如HDFS。事件会在被储存在另一个Agent的Channel中或者终点仓库之后从原来的Agent中移除。这是一个单hop在流中信息传输定义，以此提供了端对端的流的可靠性。</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
Flume用一个事务性方案来保证事件传递的可靠性。source、sink和channel分别提供不同的事务机制，source和sink是将event的存储/恢复封装在在一个事务机制中，而channel是将事件的存储和提供封装在一个事务机制中。这个保证了事件集合可靠地从流中的一个点传到另一个点。在多个hop的流中，前一个hop的sink和后一个hop的source都有其事务机制来保证数据能够安全得存储在下一个hop中。</p>
<div style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;margin-left:30px;">
<h3 align="left">Recoverability（可恢复性）</h3>
</div>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
The events are staged in the channel, which manages recovery from failure. Flume supports a durable file channel which is backed by the local file system. There’s also a memory channel which simply stores the events in an in-memory queue, which is faster but
 any events still left in the memory channel when an agent process dies can’t be recovered.</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
Channel中存储着event，并且负责失效恢复。Flume支持一个持久的依赖于本地文件系统的文件Channel。同样支持一个内存Channel简单地将事件存储在一个内存队列，处理速度快但当Agent挂掉时内存中存留的事件将会丢失并且没办法恢复。</p>
<div style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;margin-left:30px;">
<h2 align="left">Setup（设置）</h2>
<h3 align="left">Setting up an agent（设置Agent）</h3>
</div>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
Flume agent configuration is stored in a local configuration file. This is a text file that follows the Java properties file format. Configurations for one or more agents can be specified in the same configuration file. The configuration file includes properties
 of each source, sink and channel in an agent and how they are wired together to form data flows.</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
Flume agent配置存储在一个本地配置文件中。这是一个跟Java 属性文件格式一样的文本文件。一个或者多个agent可以指定同一个配置文件来进行配置。配置文件包括每个source的属性，agent中的sink和channel以及它们是如何连接构成数据流。</p>
<div style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;margin-left:30px;">
<h3 align="left">Configuring individual components（单个组件的配件）</h3>
</div>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
Each component (source, sink or channel) in the flow has a name, type, and set of properties that are specific to the type and instantiation. For example, an Avro source needs a hostname (or IP address) and a port number to receive data from. A memory channel
 can have max queue size (“capacity”), and an HDFS sink needs to know the file system URI, path to create files, frequency of file rotation (“hdfs.rollInterval”) etc. All such attributes of a component needs to be set in the properties file of the hosting Flume
 agent.</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
流中的每个组件（source，sink或者channel）都有名字，类型和用来指定类型的属性集和实例化。例如，一个avro source需要一个主机名（或者IP地址）和端口来接收数据，内存channel有最大队列值（“capacity”），和HDFS sink需要知道文件系统的URI,来创建路径，轮询文件的频率（hdfs.roollInterval）等.组件的所有属性都必须在Flume agetnt的属性文件里配置。</p>
<div style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;margin-left:30px;">
<h3 align="left">Wiring the pieces together（碎片集合）</h3>
</div>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
The agent needs to know what individual components to load and how they are connected in order to constitute the flow. This is done by listing the names of each of the sources, sinks and channels in the agent, and then specifying the connecting channel for
 each sink and source. For example, an agent flows events from an Avro source called avroWeb to HDFS sink hdfs-cluster1 via a file channel called file-channel. The configuration file will contain names of these components and file-channel as a shared channel
 for both avroWeb source and hdfs-cluster1 sink.</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
agent需要知道每个组件加载什么和它们是怎样连接构成流。这通过列出agent中每个source、sink和channel和指定每个sink和source连接的channel。例如，一个agent流事件从一个称为avroWeb的Avro sources通过一个称为file-channel的文件channel流向一个称为hdfs-cluster1的HDFS sink。配置文档将包含这些组件的名字和avroWeb source和hdfs-cluster1 sink中间共享的file-channel。</p>
<div style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;margin-left:30px;">
<h3 align="left">Starting an agent（开始一个agent）</h3>
</div>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
An agent is started using a shell script called flume-ng which is located in the bin directory of the Flume distribution. You need to specify the agent name, the config directory, and the config file on the command line:</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
agent通过一个称为flume-ngshell位于Flume项目中bin目录下的脚本来启动。你必须在命令行中指定一个agent名字，配置目录和配置文档</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
</p>
<pre><code class="language-java">$ bin/flume-ng agent -n $agent_name -c conf -f conf/flume-conf.properties.template</code></pre>
<p></p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
Now the agent will start running source and sinks configured in the given properties file.</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
现在agent将会开始运行给定的属性文档中的cource和sink。</p>
<div style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;margin-left:30px;">
<h3 align="left">A simple example（一个简单的例子）</h3>
</div>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
Here, we give an example configuration file, describing a single-node Flume deployment. This configuration lets a user generate events and subsequently logs them to the console.</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
这里我们给出一个配置文件的例子，阐述一个单点Flume的部署，这个配置让一个用户产生一个事件和随后把事件打印在控制台。</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
</p>
<pre><code class="language-java"># example.conf: A single-node Flume configuration

 

# Name the components on this agent

a1.sources = r1

a1.sinks = k1

a1.channels = c1

 

# Describe/configure the source

a1.sources.r1.type = netcat

a1.sources.r1.bind = localhost

a1.sources.r1.port = 44444

 

# Describe the sink

a1.sinks.k1.type = logger

 

# Use a channel which buffers events in memory

a1.channels.c1.type = memory

a1.channels.c1.capacity = 1000

a1.channels.c1.transactionCapacity = 100

 

# Bind the source and sink to the channel

a1.sources.r1.channels = c1

a1.sinks.k1.channel = c1</code></pre>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
This configuration defines a single agent named a1. a1 has a source that listens for data on port 44444, a channel that buffers event data in memory, and a sink that logs event data to the console. The configuration file names the various components, then describes
 their types and configuration parameters. A given configuration file might define several named agents; when a given Flume process is launched a flag is passed telling it which named agent to manifest.</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
Given this configuration file, we can start Flume as follows:</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
这个配置信息定义了一个名字为a1的单点agent。a1拥有一个监听数据端口为44444的source，一个内存channel和一个将事件打印在控制台的sink。配置文档给多个组件命名，并且描述它们的类型和配置参数。一个给定的配置文档可以定义多个agent；当一个给定的Flume进程加载时，一个标志会传递告诉他具体运行哪个agent。</p>
<pre><code class="language-html">$ bin/flume-ng agent --conf conf --conf-file example.conf --name a1 -Dflume.root.logger=INFO,console</code></pre>
<p></p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
Note that in a full deployment we would typically include one more option: --conf=&lt;conf-dir&gt;. The &lt;conf-dir&gt; directory would include a shell script <em>flume-env.sh</em> and potentially a log4j properties file. In this example, we pass a Java option to force
 Flume to log to the console and we go without a custom environment script.</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
需要说明的是在一个完整的部署中我们应该通常会包含多一个选项：--conf=&lt;conf-dir&gt;.&lt;conf-dir&gt;目录包含一个shell脚本 flume-env.sh和一个潜在的log4j属性文档。在这个例子中，我们通过一个Java选项来强制Flume打印信息到控制台和没有自定义一个环境脚本。</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
From a separate terminal, we can then telnet port 44444 and send Flume an event:</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
通过一个独立的终端，我们可以telnet 端口4444和发送一个事件：</p>
<pre><code class="language-java">$ telnet localhost 44444

Trying 127.0.0.1...

Connected to localhost.localdomain (127.0.0.1).

Escape character is '^]'.

Hello world! &lt;ENTER&gt;

OK</code></pre>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
The original Flume terminal will output the event in a log message.</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
原来的Flume终端将会在控制台将事件打印出来：</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
</p>
<pre><code class="language-java">12/06/19 15:32:19 INFO source.NetcatSource: Source starting

12/06/19 15:32:19 INFO source.NetcatSource: Created serverSocket:sun.nio.ch.ServerSocketChannelImpl[/127.0.0.1:44444]

12/06/19 15:32:34 INFO sink.LoggerSink: Event: { headers:{} body: 48 65 6C 6C 6F 20 77 6F 72 6C 64 21 0D          Hello world!. }</code></pre>
<p></p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
Congratulations - you’ve successfully configured and deployed a Flume agent! Subsequent sections cover agent configuration in much more detail.</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
恭喜-你已经成功配置和部署了一个Flume agent！接下来的部分会覆盖agent配置的更多细节。</p>
            </div>
                </div>