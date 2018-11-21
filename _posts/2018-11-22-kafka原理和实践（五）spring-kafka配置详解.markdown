---
layout:     post
title:      kafka原理和实践（五）spring-kafka配置详解
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <div class="blogpost-body"><a></a><div><p style="font-size:18px;"><b>目录</b></p><ul><li><a href="#_label0" rel="nofollow">一、官方配置</a></li><li><a href="#_label1" rel="nofollow">二、实践中的配置</a><ul><li><a href="#_label1_0" rel="nofollow">1、生产者配置</a></li><li><a href="#_label1_1" rel="nofollow">2、消费者配置</a></li><li><a href="#_label1_2" rel="nofollow">3. 使用规范</a></li></ul></li></ul></div><p> </p><p style="font-size:18px;"><b>正文</b></p><h4>系列目录</h4>
<p><a class="singleposttitle" href="http://www.cnblogs.com/dennyzhangdd/p/7759869.html" rel="nofollow">kafka原理和实践（一）原理：10分钟入门</a></p>
<p><a href="http://www.cnblogs.com/dennyzhangdd/p/7759875.html" rel="nofollow">kafka原理和实践（二）spring-kafka简单实践</a></p>
<p><a class="singleposttitle" href="http://www.cnblogs.com/dennyzhangdd/p/7827564.html" rel="nofollow">kafka原理和实践（三）spring-kafka生产者源码</a></p>
<p><a href="http://www.cnblogs.com/dennyzhangdd/p/7759876.html" rel="nofollow">kafka原理和实践（四）spring-kafka消费者源码</a></p>
<p><a class="singleposttitle" href="http://www.cnblogs.com/dennyzhangdd/p/7834143.html" rel="nofollow">kafka原理和实践（五）spring-kafka配置详解</a></p>
<p><a href="http://www.cnblogs.com/dennyzhangdd/p/7759878.html" rel="nofollow">kafka原理和实践（六）总结升华</a></p>
<div style="text-align:right;"><a href="#_labelTop" rel="nofollow">回到顶部</a><a></a></div><h2>一、官方配置</h2>
<p><a href="http://kafka.apache.org/documentation.html#configuration" rel="nofollow">官方配置文档飞机票</a>建议看Importance=medium以上的，即重要性为中级以上的，其他的用到了再说。</p>
<div style="text-align:right;"><a href="#_labelTop" rel="nofollow">回到顶部</a><a></a></div><h2>二、实践中的配置</h2>
<p>properties配置如下：</p>
<p>bootstrap.servers=192.168.49.206:9092,192.168.49.205:9092,192.168.49.204:9092 brokers集群<br>acks=all     即所有副本都同步到数据时send方法才返回, 以此来完全判断数据是否发送成功, 理论上来讲数据不会丢失.        <br>retries=10 发送失败重试次数<br>batch.size=1638 批处理条数：当多个记录被发送到同一个分区时，生产者会尝试将记录合并到更少的请求中。这有助于客户端和服务器的性能。<br>linger.ms=1 批处理延迟时间上限：即1ms过后，不管是否达到批处理数，都直接发送一次请求<br>buffer.memory=33554432 即32MB的批处理缓冲区<br><br>group.id=order-beta  消费者群组ID，发布-订阅模式，即如果一个生产者，多个消费者都要消费，那么需要定义自己的群组，同一群组内的消费者只有一个能消费到消息<br>enable.auto.commit=true 如果为true，消费者的偏移量将在后台定期提交。<br>auto.commit.interval.ms=1000 如何设置为自动提交（enable.auto.commit=true），这里设置自动提交周期<br>session.timeout.ms=15000 在使用Kafka的组管理时，用于检测消费者故障的超时<br>concurrency = 3 消费监听器容器并发数</p>
<a></a><h3>1、生产者配置</h3>
<p>具体对应第二章中xml配置：</p>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color:#008080;"> 1</span> &lt;bean id="producerProperties" <span style="color:#0000ff;">class</span>="java.util.HashMap"&gt;
<span style="color:#008080;"> 2</span>         &lt;constructor-arg&gt;
<span style="color:#008080;"> 3</span>             &lt;map&gt;
<span style="color:#008080;"> 4</span>                 &lt;entry key="bootstrap.servers" value="${bootstrap.servers}" /&gt;
<span style="color:#008080;"> 6</span>                 &lt;entry key="retries" value="${retries}" /&gt;
<span style="color:#008080;"> 7</span>                 &lt;entry key="batch.size" value="${batch.size}" /&gt;
<span style="color:#008080;"> 8</span>                 &lt;entry key="linger.ms" value="${linger.ms}" /&gt;
<span style="color:#008080;"> 9</span>                 &lt;entry key="buffer.memory" value="${buffer.memory}" /&gt;
<span style="color:#008080;">11</span>                 &lt;entry key="acks" value="${acks}" /&gt;   
<span style="color:#008080;">13</span>                 &lt;entry key="key.serializer" value="org.apache.kafka.common.serialization.StringSerializer" /&gt;源码预制的UTF8字符串反序列化实现类  byte[]-》String
<span style="color:#008080;">15</span>                 &lt;entry key="value.serializer" value="org.apache.kafka.common.serialization.StringSerializer" /&gt;
<span style="color:#008080;">17</span>             &lt;/map&gt;
<span style="color:#008080;">18</span>         &lt;/constructor-arg&gt;
<span style="color:#008080;">19</span>     &lt;/bean&gt;</pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<a></a><h3>2、消费者配置</h3>
<p>具体对应第二章中xml配置：</p>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color:#008080;"> 1</span> &lt;!-- 定义consumer的参数 --&gt;
<span style="color:#008080;"> 2</span>     &lt;bean id="consumerProperties" <span style="color:#0000ff;">class</span>="java.util.HashMap"&gt;
<span style="color:#008080;"> 3</span>         &lt;constructor-arg&gt;
<span style="color:#008080;"> 4</span>             &lt;map&gt;
<span style="color:#008080;"> 5</span>                 &lt;entry key="bootstrap.servers" value="${bootstrap.servers}" /&gt;
<span style="color:#008080;"> 6</span>                 &lt;entry key="group.id" value="${group.id}" /&gt;
<span style="color:#008080;"> 7</span>                 &lt;entry key="enable.auto.commit" value="${enable.auto.commit}" /&gt;
<span style="color:#008080;"> 8</span>                 &lt;entry key="session.timeout.ms" value="${session.timeout.ms}" /&gt;
<span style="color:#008080;"> 9</span>                 &lt;entry key="key.deserializer"
<span style="color:#008080;">10</span>                     value="org.apache.kafka.common.serialization.StringDeserializer" /&gt;
<span style="color:#008080;">11</span>                 &lt;entry key="value.deserializer"
<span style="color:#008080;">12</span>                     value="org.apache.kafka.common.serialization.StringDeserializer" /&gt;
<span style="color:#008080;">13</span>             &lt;/map&gt;
<span style="color:#008080;">14</span>         &lt;/constructor-arg&gt;
<span style="color:#008080;">15</span>     &lt;/bean&gt;</pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<div class="cnblogs_code">
<pre><span style="color:#008080;">1</span> &lt;bean id="messageListenerContainer" <span style="color:#0000ff;">class</span>="org.springframework.kafka.listener.ConcurrentMessageListenerContainer" init-method="doStart" &gt;
<span style="color:#008080;">2</span>         &lt;constructor-arg ref="consumerFactory" /&gt;
<span style="color:#008080;">4</span>         &lt;property name="<span style="color:#ff0000;">concurrency</span>" value="${concurrency}" /&gt;消费监听器容器并发数
<span style="color:#008080;">5 </span>&lt;/bean&gt;</pre>
</div>
<p> </p>
<a></a><h3>3. 使用规范</h3>
<p>这里发布一个真实的公司要求的使用规范，当然比较简单哈，但贵在真实：</p>
<p style="font-family:Menlo;line-height:1.5;">        a: Producer 部分参数设定:</p>
<p style="font-family:Menlo;line-height:1.5;">       　　1: acks 设置为 "all" 即所有副本都同步到数据时send方法才返回, 以此来完全判断数据是否发送成功, 理论上来讲数据不会丢失.           </p>
<p style="font-family:Menlo;line-height:1.5;">　　　　2: retries = MAX 无限重试，直到你意识到出现了问题.</p>
<p style="font-family:Menlo;line-height:1.5;">　　　　3: 使用 callback 来处理消息失败发送逻辑.</p>
<p style="font-family:Menlo;line-height:1.5;">　　　　4: min.insync.replicas &gt; 1 消息至少要被写入到这么多副本才算成功，也是提升数据持久性的一个参数。与acks配合使用.</p>
<p style="font-family:Menlo;line-height:1.5;">　　　　5: 其他一些超时参数: reconnect.backoff.ms, retry.backoff.ms , linger.ms 结合 batch.size 等.    </p>
<p style="font-family:Menlo;line-height:1.5;">       </p>
<p style="font-family:Menlo;line-height:1.5;">       </p>
<p style="font-family:Menlo;line-height:1.5;">        b: Consumer 部分参数设定:</p>
<p style="font-family:Menlo;line-height:1.5;">            1: auto.offset.reset 设置为 "earliest" 避免 offset 丢失时跳过未消费的消息. 目前消息存储不统一, 部分使用 zookeeper, 部分使用 kafka topic.          </p>
<p style="font-family:Menlo;line-height:1.5;">            2: enable.auto.commit=false  关闭自动提交位移, 在消息被完整处理之后再手动提交位移.</p>
<p style="font-family:Menlo;line-height:1.5;">            3: consumer 的并发受 partition 的限制. 如果消息处理量比较大的情况请提前与运维联系, 增加 partition 数量应对消费端并发. 默认topic partition 为6-8个.</p>
<p style="font-family:Menlo;line-height:1.5;">               partition 也不是越多越好. 首先会增加 file 和 memory, 其次会延长选举时间, 并且会延长 offset 的查询时间.  partition可以扩容但无法缩减.</p>
<p style="font-family:Menlo;line-height:1.5;">       </p>
<p style="font-family:Menlo;line-height:1.5;">       </p>
<p style="font-family:Menlo;line-height:1.5;">    极限情况的数据丢失现象.</p>
<p style="font-family:Menlo;line-height:1.5;">        a: 即使将 ack 设置为 "all" 也会在一定情况下丢失消息. 因为 kafka 的高性能特性, 消息在写入 kafka 时并没有落盘 而是写入了 OS buffer 中. 使用 OS 的脏页刷新策略周期性落盘, 就算落盘 仍然会有 raid buffer. 前者机器宕机数据丢失, 后者机器跳电数据丢失.</p>
<p style="font-family:Menlo;line-height:1.5;">        b: 对数据可靠性较高的场景建议 offset 手动提交. 自动提交当遇到业务系统上线被关闭时, 消息读取并且 offset 已经提交, 但是数据没有存储或者仍没来得及消费时, 消息状态在内存中无法保留, 重启应用会跳过消息 致使消息丢失.</p></div>            </div>
                </div>