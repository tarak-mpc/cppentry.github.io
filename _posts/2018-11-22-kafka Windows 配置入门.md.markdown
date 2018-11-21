---
layout:     post
title:      kafka Windows 配置入门.md
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/zsf211/article/details/82933496				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>#kafka Windows 配置 入门</p>
<p>##<strong>1、 简介</strong><br>
Kafka是一种高吞吐量的分布式发布订阅消息系统。<br>
<strong>##2、 kafka环境搭建</strong><br>
###2.1 配置依赖环境<br>
1、kafka依赖于JDK运行环境，因此需要配置java的运行环境<br>
安装jdk环境，这个教程是在太多了，不在累述。</p>
<p>2、下载kafka安装包<br>
<a href="http://kafka.apache.org/downloads" rel="nofollow">http://kafka.apache.org/downloads</a><br>
建议使用bin的运行包，src也可以使用，只是相对复杂了些。</p>
<p><strong>3、配置zookeeper</strong><br>
打开config/zookeeper.properties中添加：不需要另外下载zookeeper，在kafka文件内部配置了zookeeper。</p>
<pre><code>dataDir=F:/MQ/kafka/kafka_2.12-2.0.0bin/zookeeper/data
dataLogDir=F:/MQ/kafka/kafka_2.12-2.0.0bin/zookeeper/log
</code></pre>
<p>配置以上两个目录：需要注意路径“/”或者“\”</p>
<p>配置以上两个目录：需要注意路径“/”或者“\”</p>
<p><strong>4、配置kafka</strong><br>
打开config/server.properties中添加：</p>
<pre><code>log.dirs=F:/MQ/kafka/kafka_2.12-2.0.0bin/kafka-logs
</code></pre>
<p>配置以上两个目录：需要注意路径“/”或者“\”</p>
<p>配置以上两个目录：需要注意路径“/”或者“\”</p>
<p>其中：zookeeper.connect=localhost:2181 链接zookeeper。</p>
<p>###2.1 启动kafka<br>
启动kafka前，需要先启动zookeeper。进入zookeeper目录：建立批处理命令<br>
1、启动zookeeper<br>
建立zookeeper批处理命令Run zookeeper.bat</p>
<pre><code>bin\windows\zookeeper-server-start.bat config\zookeeper.properties
</code></pre>
<p>运行Run zookeeper.bat</p>
<p>2、启动kafka<br>
建立kafka批处理命令Run Kafka.bat</p>
<pre><code>.\bin\windows\kafka-server-start.bat .\config\server.properties
</code></pre>
<p>运行Run Kafka.bat<br>
<img src="https://img-blog.csdn.net/20181003195446372?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3pzZjIxMQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="在这里插入图片描述"><br>
##3、 测试kafka</p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>