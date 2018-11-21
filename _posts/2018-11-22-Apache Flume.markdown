---
layout:     post
title:      Apache Flume
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="apache-flume">Apache Flume</h1>

<p>来自Flume 1.6.0 User Guide</p>

<p>A Flume agent 包括 Source Channel Sink</p>



<h2 id="source">source</h2>

<p>Flume sourcec 消费外部传来的事件，根据消费的事件类型区分不同的source</p>

<ul>
<li>Avro source （Avro是一个基于二进制数据传输高性能的中间 <a href="http://www.open-open.com/lib/view/open1369363962228.html" rel="nofollow">http://www.open-open.com/lib/view/open1369363962228.html</a></li>
<li>Thrift Source (FaceBook开发的远程服务调用框架 <a href="http://www.ibm.com/developerworks/cn/java/j-lo-apachethrift/" rel="nofollow">http://www.ibm.com/developerworks/cn/java/j-lo-apachethrift/</a></li>
<li>Exec Source ： Unix命令输出</li>
<li>JMS Source</li>
<li>Kafka Source</li>
<li>HTTP Source</li>
</ul>



<h2 id="channel">channel</h2>

<p>event放在channel中 source往其中添加event，sink消费event</p>



<h2 id="sink">sink</h2>

<p>消费channel中规定event并向外推送</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>