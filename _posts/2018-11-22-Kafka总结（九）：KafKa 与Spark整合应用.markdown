---
layout:     post
title:      Kafka总结（九）：KafKa 与Spark整合应用
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

<p style="margin-left:0cm;">当前，Flume，Kafka和Spark已经成为一个比较成熟的构建实时日志采集分析与计算平台组件，例如，通过收集相应数据统计某个应用或者网站的PV/UV信息，统计流量以及用户分布，对访问日志进行实时或者离线分析，以追踪用户行为或者进行系统风险监控等。通常在数据采集的时候会选择将Kafka作为数据采集队列，将采集的数据首先存储到Kafka中，然后用Spark对kafka中读取的数据进行处理。</p>

<h2 style="margin-left:0cm;">1.Spark简介</h2>

<p style="margin-left:0cm;">Spark是一个快速，通用的计算引擎，是Apache一个顶级项目。</p>

<p style="margin-left:0cm;">Spark用Scala语言开发，提供了Java、Scala、Python、R语言相关的API，运行在JVM之上，因此在运行Spark之前需要保证已经安装JDK环境。</p>

<p style="margin-left:0cm;">Spark可以很方便的与大数据处理相关的框架（如Flume、Kafka、HDFS、Hbase等）、工具进行整合应用；</p>

<p style="margin-left:0cm;">通常我们说的Spark，其实是指Spark核心或者Spark生态圈的统称，包括Spark的任务调度、内存管理、容错机制等基本功能。</p>

<p style="margin-left:0cm;">Spark包括以下组件：</p>

<ol><li>Spark SQL：</li>
	<li>Spark Streaming：</li>
	<li>Mlib：</li>
	<li>GraphX：</li>
</ol><h2 style="margin-left:0cm;">2.Spark</h2>

<p style="margin-left:0cm;">所谓的智能投顾，简而言之就是通过机器学习相关的算法基于大数据进行分析处理为用户投资决策提出参考指标甚至自动帮助用户进行投资决策。</p>

<p style="margin-left:0cm;">例如：在证券行业，当前比较热门的“智能选股”就属于“智能投顾”范畴的一类典型应用，金融机构或者第三方根据股票行情、技术指标、财务指标、基本面指标等多种维度和策略进行分析计算，为股民提供各类选股的方案；</p>

<h3 style="margin-left:0cm;">应用描述</h3>

<p style="margin-left:0cm;">实时统计有单时间内用户搜索的关键词，并将搜索次数最高的前10个词输出</p>

<p style="margin-left:0cm;">重点是使用Spark Streaming 与Kafka集成的应用，因此并不关注业务本身的完整性。</p>

<p style="margin-left:0cm;">Spark官方网站关于Spark Streaming与Kafka集成给出了两个依赖版本，一个是基于Kafka0.8之后的版本：spark-streaming-kafka-0-8，一个是基于kafka 0.10之后的版本：spark-streaming-kafka-0-10；</p>

<p style="margin-left:0cm;"> </p>

<p style="margin-left:0cm;"> </p>

<h2 style="margin-left:0cm;">小结</h2>

<p style="margin-left:0cm;">这一章中主要是对流式处理程序进行了讲解，在实际应用中，需要根据实际的业务场景，编写出满足业务场景的流式处理程序；</p>            </div>
                </div>