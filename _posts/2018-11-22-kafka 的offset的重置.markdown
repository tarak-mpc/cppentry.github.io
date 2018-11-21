---
layout:     post
title:      kafka 的offset的重置
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>最近在spark读取kafka消息时，每次读取都会从kafka最新的offset读取。但是如果数据丢失，如果在使用Kafka来分发消息，在数据处理的过程中可能会出现处理程序出异常或者是其它的错误，会造成数据丢失或不一致。这个时候你也许会想要通过kafka把数据从新处理一遍，或者指定kafka的offset读取。kafka默认会在磁盘上保存到7天的数据，你只需要把kafka的某个topic的consumer的offset设置为某个值或者是最小值，就可以使该consumer从你设置的那个点开始消费。这就需要从zk里面修改offset的值。</p>
 <br>
查询topic的offset的范围<br>
用下面命令可以查询到topic:DynamicRange broker:SparkMaster:9092的offset的最小值：<br><br><br>
bin/kafka-run-class.sh kafka.tools.GetOffsetShell --broker-list slave6:9092 -topic videoplay --time -2<br><br><br>
输出<br><br><br>
DynamicRange:0:1288<br><br><br>
查询offset的最大值：<br><br><br>
bin/kafka-run-class.sh kafka.tools.GetOffsetShell --broker-list slave6:9092 -topic videoplay --time -1<br><br><br>
输出<br><br><br>
DynamicRange:0:7885<br><br><br>
从上面的输出可以看出topic:DynamicRange只有一个partition:0 offset范围为:[1288,7885]<br>
 <br>
设置consumer group的offset<br>
启动zookeeper client<br><br><br>
/zookeeper/bin/zkCli.sh<br><br><br>
通过下面命令设置consumer group:DynamicRangeGroup topic:DynamicRange partition:0的offset为1288:<br><br><br>
set /consumers/DynamicRangeGroup/offsets/DynamicRange/0 1288<br><br><br>
注意如果你的kafka设置了zookeeper root，比如为/kafka，那么命令应该改为：<br><br><br>
set /kafka/consumers/DynamicRangeGroup/offsets/DynamicRange/0 1288<br><br><br>
 <br>
生效 <br>
重启相关的应用程序，就可以从设置的offset开始读数据了。 <br>
 <br><p>参考：https://metabroadcast.com/blog/resetting-kafka-offsets</p>
<p><br></p>
<p>转自：http://www.cnblogs.com/hd-zg/p/5831219.html<br></p>
            </div>
                </div>