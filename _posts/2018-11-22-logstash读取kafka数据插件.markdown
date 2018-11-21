---
layout:     post
title:      logstash读取kafka数据插件
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-size:18px;">       最近公司做日志检索和计数日志不同维度统计，选用了ELK，我们的日志已经上传到Kafka中，Logstash需要从Kafka读取数据，下面是Logstash的简化配置：</span></p>
<p><span style="font-size:18px;"><br></span></p>
<p></p>
<pre><code class="language-java">&lt;span style="font-size:18px;"&gt;input {
  kafka {
        #Kafka topic
        topic_id =&gt; "test_count_log"
        #消费者组 
        group_id =&gt; "logstash_data_access_event"
        #zk的链接地址 
        zk_connect =&gt; "10.0.171.202:2181,10.0.171.203:2181,10.0.171.204:2181fka_msg"
        queue_size =&gt; 80000
        consumer_threads =&gt; 4
        fetch_message_max_bytes =&gt; 10485760
  }
}

output {
  elasticsearch {
        hosts =&gt; ["10.35.129.3:9200","10.35.129.4:9200","10.35.129.5:9200","10.35.129.6:9200","10.35.129.7:9200"]
        workers =&gt; 4
        codec =&gt; "json"
        index =&gt; "kafka_test_count_log-%{+YYYY.MM.dd}"
  }
}&lt;/span&gt;</code></pre><span style="font-size:18px;"><br>
       想要使用多个 logstash 端协同消费同一个 topic 的话，那么需要把两个或是多个 logstash 消费端配置成相同的 group_id 和 topic_id ， 但是前提是要把 相应的 topic 分多个 partitions (区) ，多个消费者消费是无法保证消息的消费顺序性的。这里解释下，为什么要分多个 partitions(区) ， kafka 的消息模型是对 topic 分区以达到分布式效果。每个 topic 下的不同的 partitions (区) 只能有一个 Owner
 去消费。所以只有多个分区后才能启动多个消费者，对应不同的区去消费。其中协调消费部分是由 server 端协调而成。不必使用者考虑太多。只是 消息的消费则是无序的 。<br><br>
总结:保证消息的顺序，那就用一个 partition 。 kafka 的每个 partition 只能同时被同一个 group 中的一个 consumer 消费 。<br></span>
<p></p>
<p><br></p>
            </div>
                </div>