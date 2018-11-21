---
layout:     post
title:      通过flume把日志文件内容写入kafka主题
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/m0_37786447/article/details/80577798				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>首先自行安装flume和 kafka当然还要jdk，我flume版本是1.6的kafka版本2.11，jdk1.8。</p><p>首先在路径flume下的conf里面创建一个logtokafka.conf文件进行配置配置内容如下。</p><pre><code class="language-html">agent.sources=r1
agent.sinks=k1
agent.channels=c1

agent.sources.r1.type=exec
agent.sources.r1.command=tail -F /opt/log.log
agent.sources.r1.restart=true
agent.sources.r1.batchSize=1000
agent.sources.r1.batchTimeout=3000
agent.sources.r1.channels=c1

agent.channels.c1.type=memory
agent.channels.c1.capacity=102400
agent.channels.c1.transactionCapacity=1000

agent.channels.c1.byteCapacity=134217728
agent.channels.c1.byteCapacityBufferPercentage=80

agent.sinks.k1.channel=c1
agent.sinks.k1.type=org.apache.flume.sink.kafka.KafkaSink
agent.sinks.k1.kafka.topic=logtokafka123
agent.sinks.k1.kafka.bootstrap.servers=192.168.72.129:9092,192.168.72.130:9092,192.168.72.131:9092
agent.sinks.k1.serializer.class=kafka.serializer.StringEncoder
agent.sinks.k1.flumeBatchSize=1000
agent.sinks.k1.useFlumeEventFormat=true</code></pre><br><pre><code class="language-html"></code></pre><p>配置conf 中的flume-env.sh 文件，没有的cp 一下flume-env.sh .template 。</p><pre class="html">export JAVA_HOME=/opt/jdk1.8.0_161/</pre><p>启动flume-agent </p><p><span style="font-family:SimSun;"><span style="color:rgb(153,153,153);font-size:14px;background-color:rgb(238,240,244);">./bin/flume-ng agent -c conf -f conf/logtokafka.conf -n agent -Dflume.root.logger=INFO,console </span><br></span></p><p><span style="color:rgb(153,153,153);"><span style="font-size:14px;background-color:rgb(238,240,244);"><span style="font-family:SimSun;">最后可以通过以下命令在kafka下进行查看主题中是否我们日志里面的内容：</span></span></span></p><p><span style="font-family:'-apple-system', 'SF UI Text', Arial, 'PingFang SC', 'Hiragino Sans GB', 'Microsoft YaHei', 'WenQuanYi Micro Hei', sans-serif, SimHei, SimSun;color:#999999;"><span style="font-size:14px;background-color:rgb(238,240,244);"><span style="font-family:Helvetica, Tahoma, Arial, sans-serif;background-color:rgb(255,255,255);"> ./kafka-console-consumer.sh  --zookeeper localhost--topic logtokafka --from-beginning</span><br></span></span></p><p><br></p>            </div>
                </div>