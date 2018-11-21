---
layout:     post
title:      Kafka stream实现word count
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/OiteBody/article/details/80613474				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>kafka stream实现kafka数据流的word count示例。</p><pre><code class="language-java">import org.apache.kafka.clients.consumer.ConsumerConfig;
import org.apache.kafka.common.serialization.Serdes;
import org.apache.kafka.streams.KafkaStreams;
import org.apache.kafka.streams.KeyValue;
import org.apache.kafka.streams.StreamsConfig;
import org.apache.kafka.streams.kstream.*;

import java.util.Arrays;
import java.util.Properties;

/**
 * @author feng.wei
 */
public class WordCountDemo {

    public static Properties getProperties() {
        Properties props = new Properties();
        props.put(StreamsConfig.APPLICATION_ID_CONFIG, "streams-WordCountDemo");
        props.put(StreamsConfig.BOOTSTRAP_SERVERS_CONFIG, "${kafka.bootstrap.servers}");
        props.put(StreamsConfig.ZOOKEEPER_CONNECT_CONFIG, "${zk.servers}");
        props.put(StreamsConfig.KEY_SERDE_CLASS_CONFIG, Serdes.String().getClass().getName());
        props.put(StreamsConfig.VALUE_SERDE_CLASS_CONFIG, Serdes.String().getClass().getName());
        props.put(ConsumerConfig.AUTO_OFFSET_RESET_CONFIG, "earliest");
        return props;
    }

    public static void main(String[] args) {
	// 窗口大小
        Long windowSize = 1000 * 10L;
	// 前后窗口的时间间隔
        Long interval = windowSize;
        KStreamBuilder builder = new KStreamBuilder();
	// 指定topics，多个topic逗号分隔
        KStream&lt;String, String&gt; source = builder.stream("test");
        KTable&lt;Windowed&lt;String&gt;, Long&gt; countTable = 
		// 对每一行按照空格做切割
		source.flatMapValues(new ValueMapper&lt;String, Iterable&lt;String&gt;&gt;() { 
            @Override
            public Iterable&lt;String&gt; apply(String value) {
                return Arrays.asList(value.split(" "));
            }
        })
		// 对每个kv元素做变换
		.map(new KeyValueMapper&lt;String, String, KeyValue&lt;String, String&gt;&gt;() { 
            @Override
            public KeyValue&lt;String, String&gt; apply(String key, String value) {
                return new KeyValue&lt;&gt;(value, value);
            }
        })
		// 对每个时间窗口进行wordcount
		.countByKey(TimeWindows.of("window-wordcount", windowSize).advanceBy(interval)); // 

        countTable.foreach(new ForeachAction&lt;Windowed&lt;String&gt;, Long&gt;() {
            @Override
            public void apply(Windowed&lt;String&gt; key, Long value) {
                System.out.println("key:" + key.key() + ", value:" + value);
            }
        });

	// 实例化KafkaStreams
        KafkaStreams streams = new KafkaStreams(builder, getProperties());
        streams.start();
        //streams.close();
    }
}
</code></pre><br><p><br></p>            </div>
                </div>