---
layout:     post
title:      Kafka 与 Flume 对比
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p style="line-height:22.5px;letter-spacing:.5px;font-size:13px;color:rgb(51,51,51);font-family:'Microsoft YaHei', Verdana, sans-serif, '宋体';">
<span style="font-family:'宋体';">采集层</span> <span style="font-family:'宋体';">主要可以使用</span>Flume, Kafka<span style="font-family:'宋体';">两种技术。</span></p>
<p style="line-height:22.5px;letter-spacing:.5px;font-size:13px;color:rgb(51,51,51);font-family:'Microsoft YaHei', Verdana, sans-serif, '宋体';">
Flume<span style="font-family:'宋体';">：</span>Flume <span style="font-family:'宋体';">是管道流方式，提供了很多的默认实现，让用户通过参数部署，及扩展</span>API.</p>
<p style="line-height:22.5px;letter-spacing:.5px;font-size:13px;color:rgb(51,51,51);font-family:'Microsoft YaHei', Verdana, sans-serif, '宋体';">
Kafka<span style="font-family:'宋体';">：</span>Kafka<span style="font-family:'宋体';">是一个可持久化的分布式的消息队列。</span></p>
<ul style="line-height:22.5px;color:rgb(51,51,51);font-family:'Microsoft YaHei', Verdana, sans-serif, '宋体';font-size:13px;"><li>
<p style="letter-spacing:.5px;font-size:12.5px;">
Kafka <span style="font-family:'宋体';">是一个非常通用的系统。你可以有许多生产者和很多的消费者共享多个主题</span>Topics<span style="font-family:'宋体';">。相比之下</span>,Flume<span style="font-family:'宋体';">是一个专用工具被设计为旨在往</span>HDFS,HBase<span style="font-family:'宋体';">发送数据。它对</span>HDFS<span style="font-family:'宋体';">有特殊的优化，并且集成了</span>Hadoop<span style="font-family:'宋体';">的安全特性。所以，</span>Cloudera <span style="font-family:'宋体';">建议如果数据被多个系统消费的话，使用</span>kafka<span style="font-family:'宋体';">；如果数据被设计给</span>Hadoop<span style="font-family:'宋体';">使用，使用</span>Flume<span style="font-family:'宋体';">。</span></p>
</li></ul><p style="line-height:22.5px;letter-spacing:.5px;font-size:13px;color:rgb(51,51,51);font-family:'Microsoft YaHei', Verdana, sans-serif, '宋体';">
 </p>
<ul style="line-height:22.5px;color:rgb(51,51,51);font-family:'Microsoft YaHei', Verdana, sans-serif, '宋体';font-size:13px;"><li>
<p style="letter-spacing:.5px;font-size:12.5px;">
<span style="font-family:'宋体';">正如你们所知</span>Flume<span style="font-family:'宋体';">内置很多的</span>source<span style="font-family:'宋体';">和</span>sink<span style="font-family:'宋体';">组件。然而，</span>Kafka<span style="font-family:'宋体';">明显有一个更小的生产消费者生态系统，并且</span>Kafka<span style="font-family:'宋体';">的社区支持不好。希望将来这种情况会得到改善，但是目前：使用</span>Kafka<span style="font-family:'宋体';">意味着你准备好了编写你自己的生产者和消费者代码。如果已经存在的</span>Flume
 Sources<span style="font-family:'宋体';">和</span>Sinks<span style="font-family:'宋体';">满足你的需求，并且你更喜欢不需要任何开发的系统，请使用</span>Flume<span style="font-family:'宋体';">。</span></p>
</li></ul><p style="line-height:22.5px;letter-spacing:.5px;font-size:13px;color:rgb(51,51,51);font-family:'Microsoft YaHei', Verdana, sans-serif, '宋体';">
 </p>
<ul style="line-height:22.5px;color:rgb(51,51,51);font-family:'Microsoft YaHei', Verdana, sans-serif, '宋体';font-size:13px;"><li>
<p style="letter-spacing:.5px;font-size:12.5px;">
Flume<span style="font-family:'宋体';">可以使用拦截器实时处理数据。这些对数据屏蔽或者过量是很有用的。</span>Kafka<span style="font-family:'宋体';">需要外部的流处理系统才能做到。</span></p>
</li></ul><p style="line-height:22.5px;letter-spacing:.5px;font-size:13px;color:rgb(51,51,51);font-family:'Microsoft YaHei', Verdana, sans-serif, '宋体';">
 </p>
<ul style="line-height:22.5px;color:rgb(51,51,51);font-family:'Microsoft YaHei', Verdana, sans-serif, '宋体';font-size:13px;"><li>
<p style="letter-spacing:.5px;font-size:12.5px;">
Kafka<span style="font-family:'宋体';">和</span>Flume<span style="font-family:'宋体';">都是可靠的系统</span>,<span style="font-family:'宋体';">通过适当的配置能保证零数据丢失。然而，</span>Flume<span style="font-family:'宋体';">不支持副本事件。于是，如果</span>Flume<span style="font-family:'宋体';">代理的一个节点奔溃了，即使使用了可靠的文件管道方式，你也将丢失这些事件直到你恢复这些磁盘。如果你需要一个高可靠行的管道，那么使用</span>Kafka<span style="font-family:'宋体';">是个更好的选择。</span></p>
</li></ul><p style="line-height:22.5px;letter-spacing:.5px;font-size:13px;color:rgb(51,51,51);font-family:'Microsoft YaHei', Verdana, sans-serif, '宋体';">
 </p>
<ul style="line-height:22.5px;color:rgb(51,51,51);font-family:'Microsoft YaHei', Verdana, sans-serif, '宋体';font-size:13px;"><li>
<p style="letter-spacing:.5px;font-size:12.5px;">
Flume<span style="font-family:'宋体';">和</span>Kafka<span style="font-family:'宋体';">可以很好地结合起来使用。如果你的设计需要从</span>Kafka<span style="font-family:'宋体';">到</span>Hadoop<span style="font-family:'宋体';">的流数据，使用</span>Flume<span style="font-family:'宋体';">代理并配置</span>Kafka<span style="font-family:'宋体';">的</span>Source<span style="font-family:'宋体';">读取数据也是可行的：你没有必要实现自己的消费者。你可以直接利用</span>Flume<span style="font-family:'宋体';">与</span>HDFS<span style="font-family:'宋体';">及</span>HBase<span style="font-family:'宋体';">的结合的所有好处。你可以使用</span>Cloudera
 Manager<span style="font-family:'宋体';">对消费者的监控，并且你甚至可以添加拦截器进行一些流处理。</span></p>
</li></ul><p style="line-height:22.5px;letter-spacing:.5px;font-size:13px;color:rgb(51,51,51);font-family:'Microsoft YaHei', Verdana, sans-serif, '宋体';">
<span style="font-size:14px;font-family:Calibri, sans-serif;">Flume</span><span style="font-size:14px;font-family:'宋体';">和</span><span style="font-size:14px;font-family:Calibri, sans-serif;">Kafka</span><span style="font-size:14px;font-family:'宋体';">可以结合起来使用。通常会使用</span><span style="font-size:14px;font-family:Calibri, sans-serif;">Flume
 + Kafka</span><span style="font-size:14px;font-family:'宋体';">的方式。其实如果为了利用</span><span style="font-size:14px;font-family:Calibri, sans-serif;">Flume</span><span style="font-size:14px;font-family:'宋体';">已有的写</span><span style="font-size:14px;font-family:Calibri, sans-serif;">HDFS</span><span style="font-size:14px;font-family:'宋体';">功能，也可以使用</span><span style="font-size:14px;font-family:Calibri, sans-serif;">Kafka
 + Flume</span><span style="font-size:14px;font-family:'宋体';">的方式。</span></p>
            </div>
                </div>