---
layout:     post
title:      kafka数据保存时间问题与kafka的性能测试
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>查看kafka的topic情况，bin/kafka-topics.sh --describe --zookeeper hadoop234:2182 --topic test5</p>
<p><img src="https://img-blog.csdn.net/20151015185059083?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p>可以查看replicationfactor的leader，如果broker list中某个节点断掉，则会重选leader</p>
<p>Partition： 分区<br>
Leader ： 负责读写指定分区的节点<br>
Replicas ： 复制该分区log的节点列表<br>
Isr ： "in-sync" replicas，当前活跃的副本列表（是一个子集），并且可能成为Leader。<br><br></p>
<p>在producer发送消息的过程中，broker list的某个节点断掉，不会影响消息的发送。</p>
<p><br></p>
<p></p>
<p><strong> kafka数据保存时间问题。</strong></p>
<p>   Kafka删除检查主要有两种，任一达到要求即执行。</p>
<p>（1）      按时间粒度，可设置分钟或者小时。达到时间进行处理。</p>
<p>（2）      按文件大小限制，设置最大文件大小，达到上限即进行处理。</p>
可设置文件大小检查周期。<br><p><strong><br></strong></p>
<p><strong>kafka的性能测试</strong><br></p>
<p></p>
<p>kafka中有自带的性能测试代码，测试结果均来自kafka自带的测试代码 ，位于bin/kafka-producer-perf-test.sh。</p>
<p>         主要参数有以下四个，</p>
<p>messages                生产者发送总的消息数量</p>
<p>message-size             每条消息大小（单位为b）</p>
<p>batch-size                每次批量发送消息的数量</p>
<p>topics                   生产者发送的topic</p>
threads                  生产者使用几个线程同时发送<br><p>例如bin/kafka-producer-perf-test.sh --messages 100000 --message-size 1000  --batch-size 10000 --topics test4 --threads 4 --broker-list hadoop234:9092,hadoop237:9092,hadoop238:9092</p>
<p>返回信息为</p>
<p>start.time, end.time, compression, message.size, batch.size, total.data.sent.in.MB, MB.sec, total.data.sent.in.nMsg, nMsg.sec<br></p>
<p>2015-10-15 18:56:27:542, 2015-10-15 18:56:30:880, 0, 1000, 10000, 95.37, 28.5702, 100000, 29958.0587</p>
<p>可以通过调节参数进行性能测试。</p>
<p><br></p>
<p><br></p>
<p><br></p>
<p><br></p>
<p><br></p>
            </div>
                </div>