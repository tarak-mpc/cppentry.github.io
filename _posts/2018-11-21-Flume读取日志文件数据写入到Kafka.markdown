---
layout:     post
title:      Flume读取日志文件数据写入到Kafka
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>只是为了实现从flume采集的数据写到kafka中  所以采集的数据使用伪数据  提前把数据放入到flume监听的文件夹中</p>

<p>前期准备：flume kafka（<strong>kafka要提前启动好</strong>）</p>

<p><strong>一、为flume构建agent</strong></p>

<p>先进去flume下的配文件夹里面  (此处我的配置文件夹名字为：myconf)  编写构建agent的配置文件（命名为：flume2kafka.conf）</p>

<p><strong>flume2kafka.conf</strong></p>

<pre class="has">
<code class="language-bash"># 定义这个agent中各组件的名字
a1.sources = r1
a1.sinks = k1
a1.channels = c1

# 描述和配置source组件：r1
a1.sources.r1.type = spooldir
a1.sources.r1.spoolDir = /root/datas/jsondata
a1.sources.r1.fileHeader = true

# 描述和配置sink组件：k1
a1.sinks.k1.type = org.apache.flume.sink.kafka.KafkaSink
a1.sinks.k1.kafka.topic = jsonTopic
a1.sinks.k1.kafka.bootstrap.servers = hdp001:9092,hdp002:9092,hdp003:9092
a1.sinks.k1.kafka.flumeBatchSize = 20
a1.sinks.k1.kafka.producer.acks = 1
a1.sinks.k1.kafka.producer.linger.ms = 1
a1.sinks.ki.kafka.producer.compression.type = snappy

# 描述和配置channel组件，此处使用是内存缓存的方式
a1.channels.c1.type = memory
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 100

# 描述和配置source  channel   sink之间的连接关系
a1.sources.r1.channels = c1
a1.sinks.k1.channel = c1
</code></pre>

<p><strong>二、启动flume的agent</strong></p>

<pre class="has">
<code class="language-java">bin/flume-ng agent -c conf -f 配置文件夹名/配置文件名 -n a1 -Dflume.root.logger=INFO,console</code></pre>

<p><strong>三、启动kafka的消费者</strong></p>

<pre class="has">
<code>bin/kafka-console-consumer.sh --zookeeper 主机名:2181 --topic jsonTopic --from-beginning</code></pre>

<p>topic 和 配置文件flume2kafka.conf里的sink组件中的topic一致</p>

<p>这样就开启了日志采集  日志采集完毕之后 flume会提示 如下图：</p>

<p><img alt="" class="has" height="180" src="https://img-blog.csdn.net/20181015174930186?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQxNDY3MTgy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="1157"></p>

<p>文件会写入到kafka中 具体路径是kafka配置文件中server.properties里面Log Basics的配置  如下图：</p>

<p><img alt="" class="has" height="129" src="https://img-blog.csdn.net/20181015175138706?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQxNDY3MTgy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="908"></p>

<p><strong>查看文件</strong></p>

<p><img alt="" class="has" height="498" src="https://img-blog.csdn.net/20181015175537380?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQxNDY3MTgy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="927"></p>

<p>数据就写入上图文件中</p>

<p> </p>

<p><strong>数据写入的kafka是随机的 不一定写入本机的kafka中</strong></p>            </div>
                </div>