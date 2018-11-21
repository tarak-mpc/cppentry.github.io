---
layout:     post
title:      Apache Kafka简介（入门教程轻松学）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：欢迎朋友转载，但请注明作者和原文链接。谢谢合作！					https://blog.csdn.net/liyiming2017/article/details/82790040				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>本入门教程，涵盖Kafka核心内容，通过实例和大量图表，帮助学习者理解，任何问题欢迎留言。</p>

<p>目录：</p>

<ul><li><a href="https://blog.csdn.net/liyiming2017/article/details/82790040" rel="nofollow">kafka简介</a></li>
	<li><a href="https://blog.csdn.net/liyiming2017/article/details/82790574" rel="nofollow">kafka安装和使用</a></li>
	<li><a href="https://blog.csdn.net/liyiming2017/article/details/82805479" rel="nofollow">kafka核心概念</a></li>
	<li><a href="https://blog.csdn.net/liyiming2017/article/details/82843036" rel="nofollow">kafka核心组件和流程--控制器</a></li>
	<li><a href="https://blog.csdn.net/liyiming2017/article/details/82867765" rel="nofollow">kafka核心组件和流程--协调器</a></li>
	<li><a href="https://blog.csdn.net/liyiming2017/article/details/82968550" rel="nofollow">kafka核心组件和流程--日志管理器</a></li>
	<li><a href="https://blog.csdn.net/liyiming2017/article/details/82984638" rel="nofollow">kafka核心组件和流程--副本管理器</a></li>
	<li><a href="https://blog.csdn.net/liyiming2017/article/details/82986606" rel="nofollow">kafka编程实战</a></li>
</ul><h1>第一章 Kafka简介</h1>

<p><strong>kafka的定位</strong></p>

<p>提到kafka，不太熟悉或者稍有接触的开发人员，第一想法可能会觉得它是一个消息系统。其实Kafka的定位并不止于此。</p>

<p>Kafka官方文档介绍说，Apache Kafka是一个分布式流平台，并给出了如下解释：</p>

<p>流平台有三个关键的能力：</p>

<ul><li>发布订阅记录流，和消息队列或者企业新消息系统类似。</li>
	<li>以可容错、持久的方式保存记录流</li>
	<li>当记录流产生时就进行处理</li>
</ul><p>Kafka通常用于应用中的两种广播类型：</p>

<ul><li>在系统和应用间建立实时的数据管道，能够可信赖的获取数据。</li>
	<li>建立实时的流应用，可以处理或者响应数据流。</li>
</ul><p>由此可见，kafka给自身的定位并不只是一个消息系统，而是通过发布订阅消息这种机制实现了流平台。</p>

<p>其实不管kafka给自己的定位如何，他都逃脱不了发布订阅消息的底层机制。本文讲解的重点，也是kafka发布订阅消息的特性。</p>

<p>Kafka和大多数消息系统一样，搭建好kafka集群后，生产者向特定的topic生产消息，而消费者通过订阅topic，能够准实时的拉取到该topic新消息，进行消费。如下图：</p>

<p><img alt="" class="has" src="https://img-blog.csdn.net/20180920164824479?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpeWltaW5nMjAxNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></p>

<p><strong>Kafka特性</strong></p>

<p>kafka和有以下主要的特性：</p>

<ol><li>消息持久话</li>
	<li>搞吞吐量</li>
	<li>可扩展性</li>
</ol><p>尤其是高吞吐量，是他的最大卖点。kafka之所以能够实现高吞吐量，是基于他自身优良的设计，及集群的可扩展性。后面章节会展开来分析。</p>

<p><strong>Kafka应用场景</strong></p>

<ol><li>消息系统</li>
	<li>日志系统</li>
	<li>流处理</li>
</ol><p>小结：通过本章学习，可以掌握kafka的定位及其特性，了解消息系统的基本运作方式。以及kafka的应用场景。下面一章我们将通过安装和使用kafka，对kafka有近一步直观的认知。</p>

<p>下一步：开始<a href="https://blog.csdn.net/liyiming2017/article/details/82790574" rel="nofollow">《kafka安装和使用》</a>的学习</p>            </div>
                </div>