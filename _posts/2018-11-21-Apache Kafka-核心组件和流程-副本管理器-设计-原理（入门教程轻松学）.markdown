---
layout:     post
title:      Apache Kafka-核心组件和流程-副本管理器-设计-原理（入门教程轻松学）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：欢迎朋友转载，但请注明作者和原文链接。谢谢合作！					https://blog.csdn.net/liyiming2017/article/details/82984638				</div>
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
</ul><p>本章简单介绍了副本管理器，副本管理器负责分区及其副本的管理。副本管理器具体的工作流程可以参考牟大恩所著的《Kafka入门与实践》。</p>

<h1>副本管理器</h1>

<p>副本机制使得kafka整个集群中，只要有一个代理存活，就可以保证集群正常运行。这大大提高了Kafka的可靠性和稳定性。</p>

<p>Kafka中代理的存活，需要满足以下两个条件：</p>

<ol><li>存活的节点要维持和zookeeper的session连接，通过zookeeper的心跳机制实现</li>
	<li>Follower副本要与leader副本保持同步，不能落后太多。</li>
</ol><p>满足以上条件的节点在ISR中，一旦宕机，或者中断时间太长，Leader就会把同步副本从ISR中踢出。</p>

<p>所有节点中，leader节点负责接收客户端的读写操作，follower节点从leader复制数据。</p>

<p>副本管理器负责对副本管理。由于副本是分区的副本，所以对副本的管理体现在对分区的管理。</p>

<p>在第三章已经对分区和副本有了详细的讲解，这里再介绍两个重要的概念，LEO和HW。</p>

<ol><li>LEO是Log End Offset缩写。表示每个分区副本的最后一条消息的位置，也就是说每个副本都有LEO。</li>
	<li>HW是Hight Watermark缩写，他是一个分区所有副本中，最小的那个LEO。</li>
</ol><p>看下图：</p>

<p><img alt="" class="has" src="https://img-blog.csdn.net/20181009163900728?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpeWltaW5nMjAxNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></p>

<p>分区test-0有三个副本，每个副本的LEO就是自己最后一条消息的offset。可以看到最小的LEO是Replica2的，等于3，也就是说HW=3。这代表offset=4的消息还没有被所有副本复制，是无法被消费的。而offset&lt;=3的数据已经被所有副本复制，是可以被消费的。</p>

<p> </p>

<p>副本管理器所承担的职责如下：</p>

<ol><li>副本过期检查</li>
	<li>追加消息</li>
	<li>拉取消息</li>
	<li>副本同步过程</li>
	<li>副本角色转换</li>
	<li>关闭副本</li>
</ol><p>再此就不再一一讲解了，详情可以参考牟大恩所著的《Kafka入门与实践》。</p>

<p>下一步：开始<a href="https://blog.csdn.net/liyiming2017/article/details/82867765" rel="nofollow">《</a><a href="https://blog.csdn.net/liyiming2017/article/details/82986606" rel="nofollow">kafka编程实战</a><a href="https://blog.csdn.net/liyiming2017/article/details/82867765" rel="nofollow">》</a>的学习</p>            </div>
                </div>