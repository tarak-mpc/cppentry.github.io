---
layout:     post
title:      Kafka总结（八）：KafKa与ELK整合应用
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p><a href="https://mp.csdn.net/postedit/81283142" rel="nofollow">Kafka总结（一）：Kafka概述</a></p>

<p><a href="https://mp.csdn.net/postedit/81283229" rel="nofollow">Kafka总结（二）：Kafka核心组件</a></p>

<p><a href="https://mp.csdn.net/postedit/81283286" rel="nofollow">Kafka总结（三）：Kafka核心流程分析</a></p>

<p><a href="https://mp.csdn.net/postedit/81283397" rel="nofollow">Kafka总结（四）：Kafka命令操作</a></p>

<p><a href="https://mp.csdn.net/postedit/81283460" rel="nofollow">Kafka总结（五）：API编程详解</a></p>

<p><a href="https://mp.csdn.net/postedit/81283491" rel="nofollow">Kafka总结（六）：Kafka Stream详解</a></p>

<p><a href="https://mp.csdn.net/postedit/81283546" rel="nofollow">Kafka总结（七）：数据采集应用</a></p>

<p><a href="https://mp.csdn.net/postedit/81283568" rel="nofollow">Kafka总结（八）：KafKa与ELK整合应用</a></p>

<p><a href="https://mp.csdn.net/postedit/81283606" rel="nofollow">Kafka总结（九）：KafKa 与Spark整合应用</a></p>

<p style="margin-left:0cm;">日志对于任何的系统来讲都是极其重要的组成部分，通常日志分散在不同的设备上，对于一个大型应用来讲，应用程序有可能分布在几十甚至上百台应用服务器上。如果对应用日志不进行统一的收集，对于这种分布式部署的应用日志就非常不方便开发人员或者运维人员查询，同时分散的日志也不便于管理，因此构建一个统一的日志管理系统就十分有必要。</p>

<p style="margin-left:0cm;">通常一个日志管理系统包括日志采集、日子传输、日志存储、日志搜索、日志分析和日志监控以及报警等模块；</p>

<p style="margin-left:0cm;">ELK就是一款非常优秀的、开源的、用于搭建实时日志分析平台的组件。ELK是Elasticsearch、Logstash和Kibana这三款开源框架首字母的缩写；</p>

<ol><li>Elasticsearch：</li>
</ol><p style="margin-left:0cm;">是一个实时的分布式搜索和分析引擎，建立在全文搜索引擎Apache Lucene基础之上，使用Java语言编写，具有分布式、高可用性、易扩展、具有副本和索引自动分片动能、提供基于HTTP协议以JSON为数据交互格式的REST风格API、多数据源、实时分析存储特点；</p>

<p style="margin-left:0cm;"> </p>

<ol><li>Logstash：</li>
</ol><p style="margin-left:0cm;">其功能类似于Flume，用于对日志进行收集、过滤，对数据进行格式化处理，并将所搜集的日志传输到相关系统进行存储，如存储到HDFS、Kafka等。Logstash是用Ruby语言开发的，由数据输入端、过滤器和输出端3部分组成。其中数据输入端可以从数据源采集数据，常见的数据源包括文件、Syslog、Kafka等；过滤器是数据处理层，包括对数据进行格式化处理、数据类型转换、数据过滤等，支持正则表达式；数据输出端是将Logstash收集的数据经由过滤器处理之后输出到其他系统，如Kafka、HDFS、Elasticsearch等。</p>

<ol><li>Kibana：</li>
</ol><p style="margin-left:0cm;">是一款针对Elasticsearch开源分析以及可视化平台，使用node.js开发，可以用来搜索，展示存储在Elasticsearch中的数据。同时提供了丰富的图标模板，只需要通过简单的配置就可以方便的进行高级数据分析和绘制各种图表。在Kibana界面我们可以通过拖拽各个图表进行排版，同时Kibana也支持条件查询、过滤检索等，还支持导入相应插件的仪表盘，如Metricbeat仪表盘；</p>

<p style="margin-left:0cm;"><img alt="" class="has" height="272" src="https://img-blog.csdn.net/20180730133526421?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2ODA3ODYy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="700"></p>

<p style="margin-left:0cm;"><strong>由ELK</strong><strong>构建的应用系统架构</strong></p>

<h2 style="margin-left:0cm;">1.ELK环境搭建</h2>

<h3 style="margin-left:0cm;">1.Elasticsearch安装配置</h3>

<ol><li>安装以及配置：执行tar -xzvf Elasticsearch-5.3.0.tar.gz解压安装包。修改Elasticsearch.ym配置文件；</li>
	<li>在config目录下有一个日志配置的log4j.properties文件，Elasticsearch使用Log4j来记录日志</li>
	<li>启动运行。进入到bin目录下执行  ./elasticsearch，启动的时候可以使用参数-d,如果启动的时候加上参数-d表示以后台服务线程的方式运行。由于Elasticsearch是运行在JVM之上的，因此在启动的时候还可以指定JVM相关参数设置。</li>
</ol><p style="margin-left:0cm;"> </p>

<p style="margin-left:0cm;"> </p>

<h3 style="margin-left:0cm;">2.Logstash安装配置</h3>

<p style="margin-left:0cm;"> </p>

<h3 style="margin-left:0cm;">3.Kibana安装配置</h3>

<h2 style="margin-left:0cm;">2.KafKa与Logstash整合</h2>

<h3 style="margin-left:0cm;">1.Logstash收集日志到KafKa</h3>

<p style="margin-left:0cm;">步骤：</p>

<ol><li>编写导入数据的配置。在Logstash安装的目录下，创建一个etc目录，在该目录下创建一个配置文件，在该文件中配置输入input、数据清洗（filter）、输出output。</li>
	<li>启动Logstash，指定配置文件路径；</li>
</ol><p style="margin-left:0cm;">./logstash -f ../etc/logstash_input_kafka</p>

<h3 style="margin-left:0cm;">2. Logstash从KafKa消费日志</h3>

<p style="margin-left:0cm;">此时，Logstash是以消费者的身份从Kafka消费数据，因此一个分区只能被一个消费者消费，同时Logstash可以通过配置group_id来控制多个Logstash客户端消费同一个主题；</p>

<p style="margin-left:0cm;">步骤：</p>

<p style="margin-left:0cm;">编写配置文件；</p>

<p style="margin-left:0cm;">启动并指定编写的配置文件；</p>

<h2 style="margin-left:0cm;">3.日志采集分析系统</h2>

<p style="margin-left:0cm;"><img alt="" class="has" height="436" src="https://img-blog.csdn.net/2018073013352739?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2ODA3ODYy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="614"></p>

<p style="margin-left:0cm;"> </p>

<h3 style="margin-left:0cm;">1.Flume采集日志配置</h3>

<p style="margin-left:0cm;">步骤：</p>

<ol><li>首先需要在Kafka中创建相关的主题；</li>
	<li>为Flume创建相关配置文件从Kafka读取数据</li>
	<li>启动Flume的access-agent代理；</li>
</ol><h3 style="margin-left:0cm;">2.Logstash拉取日志配置</h3>

<p style="margin-left:0cm;">创建一个配置文件用于Logstash从Kafka拉取数据并从存入到Elasticsearch中</p>

<h3 style="margin-left:0cm;">3.Kibana日志展示</h3>

<p style="margin-left:0cm;">登录到Kibana中，创建相关索引，在创建好索引之后，会在该索引对应的Filed中显示我们通过filter正则表达式提取的字段；</p>

<p style="margin-left:0cm;"> </p>

<h2 style="margin-left:0cm;">4.服务器性能监控系统</h2>

<p style="margin-left:0cm;">上节中我们运用Kafka与ELK整合实现了日志收集系统，在实际的工作中，常常会有这样需求：我们不仅要有统一的应用程序运行日志收集系统，而且要能够实时的监控服务器的运行状况。</p>

<p style="margin-left:0cm;">一种快速搭建服务器性能监控的系统的方法，这套系统的核心组件为Metricbeat</p>

<p style="margin-left:0cm;"> </p>

<p style="margin-left:0cm;">metricbeat是一款轻量级的系统级别性能指标采集工具，可以用于采集系统的负载、CPU、磁盘、内存使用情况等信息，还支持采集Docker、Kafka、MySQL、Redis、MongoDB等服务指标。</p>

<p style="margin-left:0cm;">同时支持与ELK无缝对接，协同工作；</p>

<h3 style="margin-left:0cm;">1.Metricbeat安装</h3>

<p style="margin-left:0cm;"> </p>

<h3 style="margin-left:0cm;">2.采集信息存储到Elasticsearch</h3>

<p style="margin-left:0cm;"> </p>

<h3 style="margin-left:0cm;">3.加载beats-dashboards</h3>

<p style="margin-left:0cm;">为了能够在Kibana中将收集到的指标进行统计展示，以各种仪表盘的形式展示，需要加载一个开源的Metricbeat dashboards插件。</p>

<h3 style="margin-left:0cm;">4.服务器性能监控系统具体实现</h3>

<p style="margin-left:0cm;">Metricbeat与Kafka结合实现服务器性能监控系统的具体步骤：</p>

<ol><li>创建主体</li>
	<li>配置Metricbeat输出到Kafka</li>
	<li>配置Logstash拉取指标信息，配置输入为Kafka，输出为Elasticsearch；</li>
	<li>启动Logstash</li>
	<li>启动Metricbeat</li>
</ol>            </div>
                </div>