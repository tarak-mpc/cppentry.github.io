---
layout:     post
title:      Flume和Kafka结合使用的分析
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p><strong>转载地址：</strong> <br>
 <a href="https://www.zhihu.com/question/36688175/answer/68692597" rel="nofollow">https://www.zhihu.com/question/36688175/answer/68692597</a> <br>
<a href="http://blog.csdn.net/crazyhacking/article/details/45746191" rel="nofollow">http://blog.csdn.net/crazyhacking/article/details/45746191</a></p>

<p><strong>采集层 主要可以使用Flume, Kafka两种技术：</strong> <br>
Flume：Flume 是管道流方式，提供了很多的默认实现，让用户通过参数部署，及扩展API。 <br>
Kafka：Kafka是一个可持久化的分布式的消息队列。</p>

<pre><code>Kafka 是一个非常通用的系统。你可以有许多生产者和很多的消费者共享多个主题Topics。相比之下,Flume是一个专用工具被设计为旨在往HDFS,Hbase发送数据。它对HDFS有特殊的优化，并且集成了Hadoop的安全特性。所以，Cloudera 建议如果数据被多个系统消费的话，使用kafka；如果数据被设计给Hadoop使用，使用Flume。

正如你们所知Flume内置很多的source和sink组件。然而，Kafka明显有一个更小的生产消费者生态系统，并且Kafka的社区支持不好。希望将来这种情况会得到改善，但是目前：使用Kafka意味着你准备好了编写你自己的生产者和消费者代码。如果已经存在的Flume Sources和Sinks满足你的需求，并且你更喜欢不需要任何开发的系统，请使用Flume。

Flume可以使用拦截器实时处理数据。这些对数据屏蔽或者过量是很有用的。Kafka需要外部的流处理系统才能做到。

Kafka和Flume都是可靠的系统,通过适当的配置能保证零数据丢失。然而，Flume不支持副本事件。于是，如果Flume代理的一个节点奔溃了，即使使用了可靠的文件管道方式，你也将丢失这些事件直到你恢复这些磁盘。如果你需要一个高可靠行的管道，那么使用Kafka是个更好的选择。

Flume和Kafka可以很好地结合起来使用。如果你的设计需要从Kafka到Hadoop的流数据，使用Flume代理并配置Kafka的Source读取数据也是可行的：你没有必要实现自己的消费者。你可以直接利用Flume与HDFS及HBase的结合的所有好处。你可以使用Cloudera Manager对消费者的监控，并且你甚至可以添加拦截器进行一些流处理。
</code></pre>

<p>Flume和Kafka可以结合起来使用。通常会使用Flume + Kafka的方式。其实如果为了利用Flume已有的写HDFS功能，也可以使用Kafka + Flume的方式。 <br>
<img src="https://img-blog.csdn.net/20170404170911714?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3VwZXJtYW5feHh4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>