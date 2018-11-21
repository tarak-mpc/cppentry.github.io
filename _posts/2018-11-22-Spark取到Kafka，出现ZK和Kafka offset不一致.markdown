---
layout:     post
title:      Spark取到Kafka，出现ZK和Kafka offset不一致
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>在项目中用到Spark Streaming读取Kafka，应用的是Kafka的low level的API因此手动的把Offset存储到ZK（每次执行成功后，才更新zk中的offset信息）当中，但是如果出现Kafka出现网络问题或者ZK没有写入到情况就会出现ZK的offset和Kafka的offset不一致。此时就要对比Kafka和ZK中的Offset</p>

<p>PS：另外spark中也可以做checkpoint来保存state</p>

<ul>
<li>Using checkpoints</li>
<li>Keeping track of the offsets that have been processed. <br>
另外it takes time for Spark to prepare them and store them <br>
checkpoint比较耗时（平均时间3S做checkpoint) <br>
墙裂推荐：<a href="http://aseigneurin.github.io/2016/05/07/spark-kafka-achieving-zero-data-loss.html" rel="nofollow">http://aseigneurin.github.io/2016/05/07/spark-kafka-achieving-zero-data-loss.html</a></li>
</ul>

<p><strong>逻辑：</strong> <br>
如果ZK中的offset小于 EarliestOffset 大于LastestOffset说明ZK中的offset已经失效，把ZK中的offset更新为EarliestOffset；如果ZK的offset在 EarliestOffset 大于LastestOffset之间那么以ZK的offset为准</p>

<p><strong>KafkaUtil （SimpleConsumer从Kafka读取offset）</strong></p>

<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">KafkaUtil</span> <span class="hljs-keyword">implements</span> <span class="hljs-title">Serializable</span> {</span>

    <span class="hljs-keyword">private</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">final</span> <span class="hljs-keyword">long</span> serialVersionUID = -<span class="hljs-number">7708717328840</span>L;

    <span class="hljs-keyword">private</span> <span class="hljs-keyword">static</span> KafkaUtil kafkaUtil = <span class="hljs-keyword">null</span>;

    <span class="hljs-keyword">private</span> <span class="hljs-title">KafkaUtil</span>() {
    }

    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> KafkaUtil <span class="hljs-title">getInstance</span>() {
        <span class="hljs-keyword">if</span> (kafkaUtil == <span class="hljs-keyword">null</span>) {
            <span class="hljs-keyword">synchronized</span> (KafkaUtil.class) {
                <span class="hljs-keyword">if</span> (kafkaUtil == <span class="hljs-keyword">null</span>) {
                    kafkaUtil = <span class="hljs-keyword">new</span> KafkaUtil();
                }
            }
        }
        <span class="hljs-keyword">return</span> kafkaUtil;
    }

    <span class="hljs-javadoc">/**
     * 从brokerList中获取host
     *
     *<span class="hljs-javadoctag"> @param</span> brokerList
     *<span class="hljs-javadoctag"> @return</span>
     */</span>
    <span class="hljs-keyword">public</span> String[] <span class="hljs-title">getHostFromBrokerList</span>(String brokerList) {
        String[] brokers = brokerList.split(<span class="hljs-string">","</span>);
        <span class="hljs-keyword">for</span> (<span class="hljs-keyword">int</span> i = <span class="hljs-number">0</span>; i &lt; brokers.length; i++) {
            brokers[i] = brokers[i].split(<span class="hljs-string">":"</span>)[<span class="hljs-number">0</span>];
        }
        <span class="hljs-keyword">return</span> brokers;
    }

    <span class="hljs-javadoc">/**
     * 从brokerList中获取port
     *
     *<span class="hljs-javadoctag"> @param</span> brokerList
     *<span class="hljs-javadoctag"> @return</span>
     */</span>
    <span class="hljs-keyword">public</span> Map&lt;String, Integer&gt; <span class="hljs-title">getPortFromBrokerList</span>(String brokerList) {
        Map&lt;String, Integer&gt; portMap = <span class="hljs-keyword">new</span> HashMap&lt;String, Integer&gt;();
        String[] brokers = brokerList.split(<span class="hljs-string">","</span>);
        <span class="hljs-keyword">for</span> (<span class="hljs-keyword">int</span> i = <span class="hljs-number">0</span>; i &lt; brokers.length; i++) {
            String host = brokers[i].split(<span class="hljs-string">":"</span>)[<span class="hljs-number">0</span>];
            Integer port = Integer.valueOf(brokers[i].split(<span class="hljs-string">":"</span>)[<span class="hljs-number">1</span>]);
            portMap.put(host, port);
        }
        <span class="hljs-keyword">return</span> portMap;
    }

    <span class="hljs-keyword">public</span> KafkaTopicOffset <span class="hljs-title">topicAndMetadataRequest</span>(String brokerList, String topic) {
        List&lt;String&gt; topics = Collections.singletonList(topic);
        TopicMetadataRequest topicMetadataRequest = <span class="hljs-keyword">new</span> TopicMetadataRequest(topics);
        KafkaTopicOffset kafkaTopicOffset = <span class="hljs-keyword">new</span> KafkaTopicOffset(topic);
        String[] hosts = getHostFromBrokerList(brokerList);
        Map&lt;String, Integer&gt; portMap = getPortFromBrokerList(brokerList);

        <span class="hljs-keyword">for</span> (String host : hosts) {
            SimpleConsumer simpleConsumer = <span class="hljs-keyword">null</span>;
            <span class="hljs-keyword">try</span> {
                simpleConsumer = <span class="hljs-keyword">new</span> SimpleConsumer(host, portMap.get(host), Constant.TIME_OUT, Constant.BUFFERSIZE, Constant.groupId);
                kafka.javaapi.TopicMetadataResponse response = simpleConsumer.send(topicMetadataRequest);
                List&lt;TopicMetadata&gt; topicMetadatas = response.topicsMetadata();
                <span class="hljs-keyword">for</span> (TopicMetadata metadata : topicMetadatas) {
                    <span class="hljs-keyword">for</span> (PartitionMetadata partitionMetadata : metadata.partitionsMetadata()) {
                        kafkaTopicOffset.getLeaderList().put(partitionMetadata.partitionId(), partitionMetadata.leader().host());
                        kafkaTopicOffset.getOffsetList().put(partitionMetadata.partitionId(), <span class="hljs-number">0</span>L);
                    }
                }
            } <span class="hljs-keyword">catch</span> (Exception e) {
                e.printStackTrace();
            } <span class="hljs-keyword">finally</span> {
                <span class="hljs-keyword">if</span> (simpleConsumer != <span class="hljs-keyword">null</span>) {
                    simpleConsumer.close();
                }

            }
        }

        <span class="hljs-keyword">return</span> kafkaTopicOffset;
    }

    <span class="hljs-javadoc">/**
     * 从Kafka取出某个topic中某个partition的最小或者最大offset
     *
     *<span class="hljs-javadoctag"> @param</span> brokerList
     *<span class="hljs-javadoctag"> @param</span> topic
     *<span class="hljs-javadoctag"> @return</span>
     */</span>
    <span class="hljs-keyword">public</span> KafkaTopicOffset <span class="hljs-title">getOffset</span>(String brokerList, String topic, String flag) {
        KafkaTopicOffset kafkaTopicOffset = topicAndMetadataRequest(brokerList, topic);
        String[] hosts = getHostFromBrokerList(brokerList);
        Map&lt;String, Integer&gt; portMap = getPortFromBrokerList(brokerList);

        <span class="hljs-keyword">for</span> (String host : hosts) {
            Iterator iterator = kafkaTopicOffset.getOffsetList().entrySet().iterator();
            SimpleConsumer simpleConsumer = <span class="hljs-keyword">null</span>;
            <span class="hljs-keyword">try</span> {
                simpleConsumer = <span class="hljs-keyword">new</span> SimpleConsumer(host, portMap.get(host), Constant.TIME_OUT, Constant.BUFFERSIZE, Constant.groupId);
                <span class="hljs-keyword">while</span> (iterator.hasNext()) {
                    Map.Entry&lt;Integer, Long&gt; entry = (Map.Entry&lt;Integer, Long&gt;) iterator.next();
                    <span class="hljs-keyword">int</span> partitionId = entry.getKey();
                    <span class="hljs-comment">//判断当前的host是否为leader</span>
                    <span class="hljs-keyword">if</span> (!kafkaTopicOffset.getLeaderList().get(partitionId).equals(partitionId)) {
                        <span class="hljs-keyword">continue</span>;
                    }

                    TopicAndPartition topicAndPartition = <span class="hljs-keyword">new</span> TopicAndPartition(topic, partitionId);
                    Map&lt;TopicAndPartition, PartitionOffsetRequestInfo&gt; requestInfoMap = <span class="hljs-keyword">new</span> HashMap&lt;TopicAndPartition, PartitionOffsetRequestInfo&gt;();

                    <span class="hljs-keyword">if</span> (flag.equals(Constant.EARLIEST_OFFSET)) {
                        requestInfoMap.put(topicAndPartition, <span class="hljs-keyword">new</span> PartitionOffsetRequestInfo(kafka.api.OffsetRequest.EarliestTime(), <span class="hljs-number">1</span>));
                    } <span class="hljs-keyword">else</span> <span class="hljs-keyword">if</span> (flag.equals(Constant.LATEST_OFFSET)) {
                        requestInfoMap.put(topicAndPartition, <span class="hljs-keyword">new</span> PartitionOffsetRequestInfo(kafka.api.OffsetRequest.LatestTime(), <span class="hljs-number">1</span>));
                    }

                    OffsetRequest offsetRequest = <span class="hljs-keyword">new</span> OffsetRequest(requestInfoMap, kafka.api.OffsetRequest.CurrentVersion(), Constant.groupId);
                    OffsetResponse offsetResponse = simpleConsumer.getOffsetsBefore(offsetRequest);

                    <span class="hljs-keyword">long</span>[] offset = offsetResponse.offsets(topic, partitionId);
                    <span class="hljs-keyword">if</span> (offset.length &gt; <span class="hljs-number">0</span>) {
                        kafkaTopicOffset.getOffsetList().put(partitionId, offset[<span class="hljs-number">0</span>]);
                    }
                }
            } <span class="hljs-keyword">catch</span> (Exception e) {
                e.printStackTrace();
            } <span class="hljs-keyword">finally</span> {
                <span class="hljs-keyword">if</span> (simpleConsumer != <span class="hljs-keyword">null</span>) {
                    simpleConsumer.close();
                }
            }
        }

        <span class="hljs-keyword">return</span> kafkaTopicOffset;

    }


}
</code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>