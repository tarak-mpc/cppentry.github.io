---
layout:     post
title:      kafka与flume的连接、kafka的python开发
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>kafka与flume的连接：</p><p>修改flume的配置文件，将sinks的配置修改为：</p><p>看flume的官网：</p><p>http://flume.apache.org/FlumeUserGuide.html#kafka-sink</p><p><br></p><p>#定义gent的source channel sinks<br>a5.sources = sr1<br>a5.channels = ch1<br>a5.sinks = log1<br>#监听目录<br>a5.sources.sr1.type=spooldir<br>a5.sources.sr1.spoolDir=/home/hadoop/hahaha_test1<br>#设置channel的参数<br>a5.channels.ch1.type=memory<br>#设置sink参数<br>a5.sinks.log1.type = org.apache.flume.sink.kafka.KafkaSink<br>a5.sinks.log1.kafka.topic = cctv3      #修改<br>a5.sinks.log1.kafka.bootstrap.servers = localhost:9092<br>a5.sinks.log1.kafka.flumeBatchSize = 20<br>a5.sinks.log1.kafka.producer.acks = 1<br>#连接sink和source<br>a5.sources.sr1.channels=ch1<br></p><p>a5.sinks.log1.channel=ch1</p><p><br></p><p>启动flume。  连接上kafka</p><p><br></p><p>2.kafka的python开发</p><p>pycharm和linux都安装kafka        pip install kafka</p><p>用pycharm编写生产者：</p><p></p><pre style="background-color:rgb(255,255,255);font-family:'宋体';font-size:15pt;"><span style="color:#000080;"><strong>from </strong></span>kafka <span style="color:#000080;"><strong>import  </strong></span>KafkaProducer
producer=KafkaProducer(<span style="color:#660099;">bootstrap_servers</span>=<span style="color:#008080;"><strong>'localhost:9092'</strong></span>)
<span style="color:#000080;"><strong>while True</strong></span>:
    msg=<span style="color:#000080;">input</span>(<span style="color:#008080;"><strong>'输入消息或bye推出'</strong></span>)
    <span style="color:#000080;"><strong>if  </strong></span><span style="color:#008080;"><strong>'bye'</strong></span>==msg:
        <span style="color:#000080;">print</span>(<span style="color:#008080;"><strong>'欢迎使用'</strong></span>)
        <span style="color:#000080;"><strong>break
</strong></span><span style="color:#000080;"><strong>    </strong></span>producer.send(<span style="color:#008080;"><strong>'cctv3'</strong></span>,msg.encode())
    producer.flush()</pre><p>编写消费者：</p><p></p><pre style="background-color:rgb(255,255,255);font-family:'宋体';font-size:15pt;"><span style="color:#000080;"><strong>from </strong></span>kafka <span style="color:#000080;"><strong>import  </strong></span>KafkaConsumer
consumer=KafkaConsumer(<span style="color:#008080;"><strong>'cctv3'</strong></span>,<span style="color:#660099;">bootstrap_servers</span>=<span style="color:#008080;"><strong>'localhost:9092'</strong></span>)
<span style="color:#000080;"><strong>for </strong></span>msg <span style="color:#000080;"><strong>in </strong></span>consumer:
    <span style="color:#000080;">print</span>(msg.value.decode())</pre><p>用xftp当作桥梁将py文件传到hadoop下：</p><p>执行python3 producerdemo命令  启动生产者      同样启动消费者</p><p>python也可以直接连接数据库，直接调取数据，分析</p>            </div>
                </div>