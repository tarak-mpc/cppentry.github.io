---
layout:     post
title:      kafka中topic基本操作
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：版权所有，转载请注明来源与地址！@CopyRight CarolLiu					https://blog.csdn.net/carolcoral/article/details/81026189				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="topic基本操作">Topic基本操作</h1>



<h3 id="在linux服务器上查找kafka的安装位置">在linux服务器上查找kafka的安装位置</h3>

<blockquote>
  <p>locate kafka-topics.sh</p>
</blockquote>

<ul>
<li><p>kafka配置文件在kafka的安装文件夹下的 ./config/service.properties 中</p></li>
<li><p>partitions指定topic分区数，replication-factor指定topic每个分区的副本数</p>

<ul><li><p>partitions分区数:</p>

<ul><li>partitions ：分区数，控制topic将分片成多少个log。可以显示指定，如果不指定则会使用broker(server.properties)中的num.partitions配置的数量</li>
<li>虽然增加分区数可以提供kafka集群的吞吐量、但是过多的分区数或者或是单台服务器上的分区数过多，会增加不可用及延迟的风险。因为多的分区数，意味着需要打开更多的文件句柄、增加点到点的延时、增加客户端的内存消耗。</li>
<li>分区数也限制了consumer的并行度，即限制了并行consumer消息的线程数不能大于分区数</li>
<li>分区数也限制了producer发送消息是指定的分区。如创建topic时分区设置为1，producer发送消息时通过自定义的分区方法指定分区为2或以上的数都会出错的；这种情况可以通过alter –partitions 来增加分区数。</li></ul></li>
<li>replication-factor副本 <br>
<ul><li>replication factor 控制消息保存在几个broker(服务器)上，一般情况下等于broker的个数。</li>
<li>如果没有在创建时显示指定或通过API向一个不存在的topic生产消息时会使用broker(server.properties)中的default.replication.factor配置的数量</li></ul></li></ul></li>
</ul>

<h3 id="创建kafka-topic">创建kafka topic</h3>

<blockquote>
  <p>bin/kafka-topics.sh –create –topic topicname –replication-factor 1 –partitions 1 –zookeeper localhost:2181</p>
</blockquote>

<p>1.topicname 是你要创建的topic的名称</p>

<p>2.–replication-factor 指定partition的replicas数，建议设置为2；</p>

<p>3.–partitions 指定分区数，这个参数需要根据broker数和数据量决定，正常情况下，每个broker上两个partition最好； </p>



<h3 id="查看kafka-topic">查看kafka topic</h3>

<blockquote>
  <p>bin/kafka-topics.sh –zookeeper localhost:2181 –list</p>
</blockquote>

<ul>
<li>其中zookeeper后面的 localhost:2181 是zookeeper的地址和端口</li>
</ul>



<h3 id="向kafka-topic-中生产数据生产数据">向kafka topic 中生产数据（生产数据）</h3>

<blockquote>
  <p>bin/kafka-console-producer.sh –broker-list localhost:9092 –topic t_cdr</p>
</blockquote>



<h3 id="向kafka-topic-中消费数据查看数据">向kafka topic 中消费数据（查看数据）</h3>

<blockquote>
  <p>bin/kafka-console-consumer.sh  –zookeeper node01:2181  –topic t_cdr –from-beginning</p>
</blockquote>



<h3 id="查看topic某分区偏移量最大小值">查看topic某分区偏移量最大（小）值</h3>

<blockquote>
  <p>bin/kafka-run-class.sh kafka.tools.GetOffsetShell –topic hive-mdatabase-hostsltable  –time -1 –broker-list node86:9092 –partitions 0</p>
</blockquote>

<ul>
<li>time为-1时表示最大值，time为-2时表示最小值</li>
</ul>



<h3 id="增加topic分区数">增加topic分区数</h3>

<ul>
<li>为topic t_cdr 增加10个分区</li>
</ul>

<blockquote>
  <p>bin/kafka-topics.sh –zookeeper node01:2181  –alter –topic t_cdr –partitions 10</p>
</blockquote>



<h3 id="删除topic慎用只会删除zookeeper中的元数据消息文件须手动删除">删除topic，慎用，只会删除zookeeper中的元数据，消息文件须手动删除</h3>

<blockquote>
  <p>bin/kafka-run-class.sh kafka.admin.DeleteTopicCommand –zookeeper node01:2181 –topic t_cdr</p>
</blockquote>



<h3 id="查看topic消费进度">查看topic消费进度</h3>

<ul>
<li>这个会显示出consumer group的offset情况， 必须参数为–group， 不指定–topic，默认为所有topic</li>
</ul>

<blockquote>
  <p>Displays the: Consumer Group, Topic, Partitions, Offset, logSize, Lag, Owner for the specified set of Topics and Consumer Group</p>
</blockquote>

<pre>
        bin/kafka-run-class.sh kafka.tools.ConsumerOffsetChecker

        required argument: [group] 
        Option Description 
        ------ ----------- 
        --broker-info Print broker info 
        --group Consumer group. 
        --help Print this message. 
        --topic Comma-separated list of consumer 
              topics (all topics if absent). 
        --zkconnect ZooKeeper connect string. (default: localhost:2181)

        Example,

        bin/kafka-run-class.sh kafka.tools.ConsumerOffsetChecker --group pv

        Group           Topic              Pid Offset   logSize    Lag    Owner 
        pv              page_visits        0   21       21         0      none 
        pv              page_visits        1   19       19         0      none 
        pv              page_visits        2   20       20         0      none
</pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>