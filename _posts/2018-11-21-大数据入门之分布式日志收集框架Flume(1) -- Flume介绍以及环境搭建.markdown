---
layout:     post
title:      大数据入门之分布式日志收集框架Flume(1) -- Flume介绍以及环境搭建
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h1>1.Flume概述</h1>

<p style="text-indent:50px;">Flume是Cloudera提供的一个高可用的，高可靠的，分布式的海量日志采集、聚合和传输的系统，Flume支持在日志系统中定制各类数据发送方，用于收集数据；同时，Flume提供对数据进行简单处理，并写到各种数据接受方（可定制）的能力。</p>

<h1>2.Flume架构及核心组件</h1>

<p><img alt="" class="has" src="https://img-blog.csdn.net/20180822141112375?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0tlbmFuXzA3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></p>

<p>Flume里面有三大组件：Source、Channel、Sink。</p>

<p>Source：收集数据（比如从Web服务器上收集产生的日志信息等），传到Channel中。</p>

<p>Channel：聚集数据，暂时性地把数据存进一个地方（内存、文件等），类似数据缓冲的地方，传到Sink中。</p>

<p>Sink：把数据写到某个地方去（HDFS、KAFKA等）</p>

<h1>3.环境搭建</h1>

<h3>前置要求</h3>

<p style="text-indent:50px;">JDK1.8</p>

<h3>下载安装</h3>

<p style="text-indent:50px;">下载Flume，<a href="http://archive.cloudera.com/cdh5/cdh/5/flume-ng-1.6.0-cdh5.7.0.tar.gz" rel="nofollow">http://archive.cloudera.com/cdh5/cdh/5/flume-ng-1.6.0-cdh5.7.0.tar.gz</a>。</p>

<p>解压，并配置到环境变量中。</p>

<h3>文件配置</h3>

<p style="text-indent:50px;">到flume的conf目录下，执行"cp flume-env.sh.template flume-env.sh"，然后“vi flume-env.sh”，修改如下。只需要配一下自己的JAVA_HOEM的路径即可。</p>

<pre class="has">
<code>export JAVA_HOME=/usr/java/jdk1.8.0_171</code></pre>

<h3>检测</h3>

<p>进入flume的bin目录下，执行“flume-ng version”，如果能正确出来版本信息即配置成功。</p>

<p> </p>

<p>想了解更多Flume的知识，请移步<a href="http://flume.apache.org/" rel="nofollow">Flume官方文档</a>。</p>            </div>
                </div>