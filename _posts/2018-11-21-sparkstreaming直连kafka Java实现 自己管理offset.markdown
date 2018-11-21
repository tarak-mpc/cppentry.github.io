---
layout:     post
title:      sparkstreaming直连kafka Java实现 自己管理offset
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/mengxb12138/article/details/77036924				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<pre><code>import java.util.Arrays;
import java.util.HashMap;
import java.util.HashSet;
import java.util.Map;

import org.apache.spark.SparkConf;
import org.apache.spark.api.java.JavaRDD;
import org.apache.spark.api.java.function.Function;
import org.apache.spark.api.java.function.VoidFunction;
import org.apache.spark.streaming.Durations;
import org.apache.spark.streaming.api.java.JavaInputDStream;
import org.apache.spark.streaming.api.java.JavaStreamingContext;
import org.apache.spark.streaming.kafka.HasOffsetRanges;
import org.apache.spark.streaming.kafka.KafkaCluster;
import org.apache.spark.streaming.kafka.KafkaUtils;
import org.apache.spark.streaming.kafka.OffsetRange;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import kafka.common.TopicAndPartition;
import kafka.message.MessageAndMetadata;
import kafka.serializer.StringDecoder;
import scala.Predef;
import scala.Tuple2;
import scala.collection.JavaConversions;

/**
 * spark streaming使用direct方式读取kafka数据，并存储每个partition读取的offset
 */
public final class JavaDirectKafkaWordCount {

    private static final Logger LOG = LoggerFactory.getLogger(JavaDirectKafkaWordCount.class);

    public static void main(String[] args) {

        if (args.length &lt; 2) {
            System.err.println("Usage: JavaDirectKafkaWordCount &lt;brokers&gt; &lt;topics&gt;\n" +
                    "  &lt;brokers&gt; is a list of one or more Kafka brokers\n" +
                    "  &lt;topics&gt; is a list of one or more kafka topics to consume from\n\n");
            System.exit(1);
        }

        //StreamingExamples.setStreamingLogLevels();

        String brokers = args[0]; // kafka brokers
        String topics = args[1]; // 主题
        long seconds = 10; // 批次时间（单位：秒）

        // Create context with a 2 seconds batch interval
        SparkConf sparkConf = new SparkConf().setAppName("JavaDirectKafkaWordCount");
        JavaStreamingContext jssc = new JavaStreamingContext(sparkConf, Durations.seconds(seconds));

        // 设置kafkaParams
        HashSet&lt;String&gt; topicsSet = new HashSet&lt;&gt;(Arrays.asList(topics.split(",")));
        HashMap&lt;String, String&gt; kafkaParams = new HashMap&lt;&gt;();
        kafkaParams.put("metadata.broker.list", brokers);
        final String groupId = kafkaParams.get("group.id");

        // 创建kafka管理对象
        final KafkaCluster kafkaCluster = getKafkaCluster(kafkaParams);

        // 初始化offsets
        Map&lt;TopicAndPartition, Long&gt; fromOffsets = fromOffsets(topicsSet, kafkaParams, groupId, kafkaCluster, null);

        // 创建kafkaStream
        JavaInputDStream&lt;String&gt; stream = KafkaUtils.createDirectStream(jssc,
                String.class, String.class, StringDecoder.class,
                StringDecoder.class, String.class, kafkaParams,
                fromOffsets,
                new Function&lt;MessageAndMetadata&lt;String, String&gt;, String&gt;() {
                    /**
                     * 
                     */
                    private static final long serialVersionUID = 1L;

                    public String call(MessageAndMetadata&lt;String, String&gt; v1)
                            throws Exception {
                        return v1.message();
                    }
                });


        // print
        stream.print();

        // 存储offsets
        storeConsumerOffsets(groupId, kafkaCluster, stream);

        // Start the computation
        jssc.start();
        jssc.awaitTermination();
    }

    /**
     * @param groupId      消费者 组id
     * @param kafkaCluster kafka管理对象
     * @param stream       kafkaStreamRdd
     */
    private static &lt;T&gt; void storeConsumerOffsets(final String groupId, final KafkaCluster kafkaCluster, JavaInputDStream&lt;T&gt; stream) {

        long l = System.currentTimeMillis();

        stream.foreachRDD(new VoidFunction&lt;JavaRDD&lt;T&gt;&gt;() {
            /**
             * 
             */
            private static final long serialVersionUID = 1L;

            @Override
            public void call(JavaRDD&lt;T&gt; javaRDD) throws Exception {

                // 根据group.id 存储每个partition消费的位置
                OffsetRange[] offsets = ((HasOffsetRanges) javaRDD.rdd()).offsetRanges();
                for (OffsetRange o : offsets) {
                    // 封装topic.partition 与 offset对应关系 java Map
                    TopicAndPartition topicAndPartition = new TopicAndPartition(o.topic(), o.partition());
                    Map&lt;TopicAndPartition, Object&gt; topicAndPartitionObjectMap = new HashMap&lt;&gt;();
                    topicAndPartitionObjectMap.put(topicAndPartition, o.untilOffset());

                    // 转换java map to scala immutable.map
                    scala.collection.immutable.Map&lt;TopicAndPartition, Object&gt; scalaTopicAndPartitionObjectMap =
                            JavaConversions.mapAsScalaMap(topicAndPartitionObjectMap).toMap(new Predef.$less$colon$less&lt;Tuple2&lt;TopicAndPartition, Object&gt;, Tuple2&lt;TopicAndPartition, Object&gt;&gt;() {
                                /**
                                 * 
                                 */
                                private static final long serialVersionUID = 1L;

                                public Tuple2&lt;TopicAndPartition, Object&gt; apply(Tuple2&lt;TopicAndPartition, Object&gt; v1) {
                                    return v1;
                                }
                            });

                    // 更新offset到kafkaCluster
                    kafkaCluster.setConsumerOffsets(groupId, scalaTopicAndPartitionObjectMap);
                }
            }
        });

        // 记录处理时间
        LOG.info("storeConsumerOffsets time:" + (System.currentTimeMillis() - l));
    }

    /**
     * 获取partition信息，并设置各分区的offsets
     *
     * @param topicsSet    所有topic
     * @param kafkaParams  kafka参数配置
     * @param groupId      消费者 组id
     * @param kafkaCluster kafka管理对象
     * @param offset       自定义offset
     * @return offsets
     */
    private static Map&lt;TopicAndPartition, Long&gt; fromOffsets(HashSet&lt;String&gt; topicsSet, HashMap&lt;String, String&gt; kafkaParams, String groupId, KafkaCluster kafkaCluster, Long offset) {

        long l = System.currentTimeMillis();

        // 所有partition offset
        Map&lt;TopicAndPartition, Long&gt; fromOffsets = new HashMap&lt;&gt;();

        // util.set 转 scala.set
        scala.collection.immutable.Set&lt;String&gt; immutableTopics = JavaConversions
                .asScalaSet(topicsSet)
                .toSet();

        // 获取topic分区信息
        scala.collection.immutable.Set&lt;TopicAndPartition&gt; scalaTopicAndPartitionSet = kafkaCluster
                .getPartitions(immutableTopics)
                .right()
                .get();

        if (offset != null || kafkaCluster.getConsumerOffsets(kafkaParams.get("group.id"),
                scalaTopicAndPartitionSet).isLeft()) {

            // 等于空则设置为0
            offset = (offset == null ? 0L : offset);

            // 设置每个分区的offset
            scala.collection.Iterator&lt;TopicAndPartition&gt; iterator = scalaTopicAndPartitionSet.iterator();
            while (iterator.hasNext()) {
                fromOffsets.put(iterator.next(), offset);
            }
        } else {
            // 往后继续读取
            scala.collection.Map&lt;TopicAndPartition, Object&gt; consumerOffsets = kafkaCluster
                    .getConsumerOffsets(groupId,
                            scalaTopicAndPartitionSet).right().get();

            scala.collection.Iterator&lt;Tuple2&lt;TopicAndPartition, Object&gt;&gt; iterator = consumerOffsets.iterator();
            while (iterator.hasNext()) {
                Tuple2&lt;TopicAndPartition, Object&gt; next = iterator.next();
                offset = (long) next._2();
                fromOffsets.put(next._1(), offset);
            }
        }

        // 记录处理时间
        LOG.info("fromOffsets time:" + (System.currentTimeMillis() - l));

        return fromOffsets;
    }

    /**
     * 将kafkaParams转换成scala map，用于创建kafkaCluster
     *
     * @param kafkaParams kafka参数配置
     * @return kafkaCluster管理工具类
     */
    private static KafkaCluster getKafkaCluster(HashMap&lt;String, String&gt; kafkaParams) {
        // 类型转换
        scala.collection.immutable.Map&lt;String, String&gt; immutableKafkaParam = JavaConversions
                .mapAsScalaMap(kafkaParams)
                .toMap(new Predef.$less$colon$less&lt;Tuple2&lt;String, String&gt;, Tuple2&lt;String, String&gt;&gt;() {
                    /**
                     * 
                     */
                    private static final long serialVersionUID = 1L;

                    public Tuple2&lt;String, String&gt; apply(
                            Tuple2&lt;String, String&gt; v1) {
                        return v1;
                    }
                });

        return new KafkaCluster(immutableKafkaParam);
    }
}
</code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>