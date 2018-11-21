---
layout:     post
title:      【kafka】一、kafka框架介绍（生产者篇）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h1>嗯，菜鸡是我，骄傲在败坏以先，狂心在跌倒之前。所以有什么说错的地方，还请大家指出批评！</h1>

<p>继爬虫之后，kafka的介绍。</p>

<p>该系列文章暂时更新两篇：</p>

<p>【kafka】一、kafka框架介绍（消费者篇）：<a href="https://blog.csdn.net/lsr40/article/details/84029034" rel="nofollow">https://blog.csdn.net/lsr40/article/details/84029034</a></p>

<p>【kafka】二、kafka框架介绍（消费者篇）：<a href="https://blog.csdn.net/lsr40/article/details/84034394" rel="nofollow">https://blog.csdn.net/lsr40/article/details/84034394</a></p>

<p>另一篇解决报错的文章：</p>

<p>【kafka】报错：advertised.listeners参数的重要性（外部访问局域网kafka）<a href="https://blog.csdn.net/lsr40/article/details/84135959" rel="nofollow">https://blog.csdn.net/lsr40/article/details/84135959</a></p>

<p>隔了一段时间才来写这篇文章，因为实在不知道该怎么来给这篇文章定义，感觉想说的东西需要用到一些基础知识，但是如果把这些基础知识都讲了，需要很大的篇幅又不太合理，所以我就不写基础概念了，就写我觉得需要关注的一些点吧。</p>

<p>简单定义：</p>

<p><strong><span style="color:#7c79e5;">kafka能干啥？</span></strong>（官网的图）</p>

<p><img alt="" class="has" height="484" src="https://img-blog.csdnimg.cn/20181113155428572.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xzcjQw,size_16,color_FFFFFF,t_70" width="582"></p>

<p>有4个东西跟kafka集群（多个<strong><span style="color:#f33b45;">brokers</span></strong>组成kafkacluster）交互，<span style="color:#f33b45;"><strong>Producers（生产者）</strong></span>，<strong><span style="color:#f33b45;">Consumers（消费者）</span></strong>，<strong><span style="color:#f33b45;">Connectors（我也不知道这个中文要叫什么，连接器？）</span></strong>，<strong><span style="color:#f33b45;">Stream Processors（流程序）</span></strong>，箭头代表交互关系（就是数据从哪来到哪去）。</p>

<p>kafka中数据已<span style="color:#f33b45;"><strong>topic（主题）</strong></span>来维护，一般一个业务会有一个topic，一个topic存的数据格式应该都是一样的。</p>

<p><span style="color:#7c79e5;">1、分区数、副本数</span></p>

<p>每个topic可以设置分区数量和副本数量，一般是这样，分区数越多，性能越好（但是也不是写个几百几万性能就上天了，想想也是不可能的），副本数越多，性能越差，但是安全性越好（不容易丢数据）。</p>

<p>创建分区为10个，副本数为1个的topic：</p>

<pre class="has">
<code class="language-bash">bin/kafka-topics.sh --create \ 
--topic program_version_1 \ 
--zookeeper 第一台zk:2181,第二台zk:2181,第三台zk:2181/zk上的路径 \
--replication-factor 1 --partitions 10  </code></pre>

<p><strong><span style="color:#3399ea;">提醒：zk上的路径最好要给定，否则kafka就在zk的根目录下生成一大堆文件夹，不便于管理，看起来也难受</span></strong></p>

<p><span style="color:#3399ea;"><strong>建议：将kafka可以配置的参数都看一遍，重点要理解kafka可以设置哪些东西，而不要把重心放在某一个框架的api上，因为不同的api可能提供的设置相同参数方式都不太一样！！</strong></span></p>

<p><span style="color:#7c79e5;">2、生产者发数据的配置</span></p>

<p>官网配置：<a href="http://kafka.apache.org/documentation/#producerconfigs" rel="nofollow">http://kafka.apache.org/documentation/#producerconfigs</a></p>

<p>提几个比较需要关注的：</p>

<p><span style="color:#f33b45;"><strong>-1.acks</strong></span>：发送消息是否等待brokers的返回消息，值可以是[all, -1, 0, 1]（all和-1是效果是一样的）</p>

<ul><li>acks=0是不等待任何返回消息，就是说发送了就算收不到也无所谓，性能最好，最不安全</li>
	<li>acks=1等待主节点（leader）的响应，但是如果主节点响应完，马上挂了，那数据还是会丢的，性能中等，安全中等</li>
	<li>acks=-1或者acks=all相当于要等所有的备份节点都返回接受到的消息，才会继续发下一批数据，性能最差，安全最好</li>
</ul><p><span style="color:#f33b45;"><strong>-2.retries</strong></span>：发送数据失败的重试次数，如果设置了重试次数，该max.in.flight.requests.per.connection参数为1的情况下才能继续保证分区内有顺序（kafka的topic在分区内数据是时间先后顺序的，但是同一个topic不同分区中的数据就无序了）</p>

<p><span style="color:#f33b45;"><strong>-3.retry.backoff.ms</strong></span>：数据发送失败，重试的时间间隔</p>

<p><span style="color:#f33b45;"><strong>-4.compression.type</strong></span>：压缩，提供了none，gzip，snappy，lz4。（压缩和解压缩是在生产和消费的时候处理的，kafka并不处理）</p>

<p><span style="color:#f33b45;"><strong>-5.batch.size</strong></span>：当数据达到这个大小（以字节为单位），生产者会把相同分区的数据一次性发送，如果数据量一直没有达到批次大小，生产者也会触发某些机制自动发送数据，如果使用压缩，那可以适当调大这个值，当然这个值越大，会越消耗内存</p>

<p><span style="color:#f33b45;"><strong>-6.max.request.size</strong></span>：一个批次数据量大小，可以通过该参数来限制每个批次最大发送的数据条数，该值越大越消耗内存</p>

<p>最后，kafka自带测试生产者：</p>

<pre class="has">
<code class="language-bash">bin/kafka-console-producer.sh --broker-list broker1:端口1,broker2:端口2,broker3:端口3 --topic 发送到哪个topic上</code></pre>

<h3>总结：考虑同步发数据还是异步（acks参数），失败是否重试，数据是否压缩，是否批次发送。</h3>

<h1><span style="color:#7c79e5;">未完：消费者我会在下一篇文章中整理，怕文章太长没耐心看</span></h1>

<p>请回顶部，点击阅读：<span style="color:#f33b45;">二、kafka框架介绍（消费者篇）</span></p>            </div>
                </div>