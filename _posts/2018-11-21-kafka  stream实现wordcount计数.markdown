---
layout:     post
title:      kafka  stream实现wordcount计数
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/wslyk606/article/details/78673888				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-size:18px;">kafka不同版本的差别还是挺大的，参考了github上kafka的代码，实现了kafka对消息的word 计数。</span></p>
<p><span style="font-size:18px;"><br></span></p>
<p><span style="font-size:18px;">1  参考<a href="http://blog.csdn.net/wslyk606/article/details/78672107" rel="nofollow">http://blog.csdn.net/wslyk606/article/details/78672107</a> 的producer，运行producer，产生kafka消息</span></p>
<p><span style="font-size:18px;"><br></span></p>
<p><span style="font-size:18px;">2  kafka stream消费kafka消息并计数，对应的maven  pom.xml文件也参考上一篇文章</span></p>
<p><span style="font-size:18px;"><br></span></p>
<p></p><pre><code class="language-java"><span style="font-size:18px;">import org.apache.kafka.clients.consumer.ConsumerConfig;
import org.apache.kafka.common.serialization.Serdes;
import org.apache.kafka.streams.KeyValue;
import org.apache.kafka.streams.StreamsConfig;
import org.apache.kafka.streams.KafkaStreams;
import org.apache.kafka.streams.processor.Processor;
import org.apache.kafka.streams.processor.ProcessorContext;
import org.apache.kafka.streams.processor.ProcessorSupplier;
import org.apache.kafka.streams.processor.TopologyBuilder;
import org.apache.kafka.streams.state.KeyValueIterator;
import org.apache.kafka.streams.state.KeyValueStore;
import org.apache.kafka.streams.state.Stores;

import java.util.Locale;
import java.util.Properties;

public class WordCount {

    private static class MyProcessorSupplier implements ProcessorSupplier&lt;String, String&gt; {

        @Override
        public Processor&lt;String, String&gt; get() {
            return new Processor&lt;String, String&gt;() {
                private ProcessorContext context;
                private KeyValueStore&lt;String, Integer&gt; kvStore;

                @Override
                @SuppressWarnings("unchecked")
                public void init(ProcessorContext context) {
                    this.context = context;
                    this.context.schedule(1000);
                    this.kvStore = (KeyValueStore&lt;String, Integer&gt;) context.getStateStore("Counts");
                }

                @Override
                public void process(String dummy, String line) {
                    String[] words = line.toLowerCase(Locale.getDefault()).split(" ");

                    for (String word : words) {
                        Integer oldValue = this.kvStore.get(word);

                        if (oldValue == null) {
                            this.kvStore.put(word, 1);
                        } else {
                            this.kvStore.put(word, oldValue + 1);
                        }
                    }

                    context.commit();
                }

                @Override
                public void punctuate(long timestamp) {
                    try (KeyValueIterator&lt;String, Integer&gt; iter = this.kvStore.all()) {
                        System.out.println("----------- " + timestamp + " ----------- ");

                        while (iter.hasNext()) {
                            KeyValue&lt;String, Integer&gt; entry = iter.next();

                            System.out.println("[" + entry.key + ", " + entry.value + "]");

                            context.forward(entry.key, entry.value.toString());
                        }
                    }
                }

                @Override
                public void close() {
                    this.kvStore.close();
                }
            };
        }
    }

    public static void main(String[] args) throws Exception {
        Properties props = new Properties();
        props.put(StreamsConfig.APPLICATION_ID_CONFIG, "streams-count-processor");
        props.put(StreamsConfig.BOOTSTRAP_SERVERS_CONFIG, "localhost:9092");
        props.put(StreamsConfig.ZOOKEEPER_CONNECT_CONFIG, "localhost:2181");
        props.put(StreamsConfig.KEY_SERDE_CLASS_CONFIG, Serdes.String().getClass());
        props.put(StreamsConfig.VALUE_SERDE_CLASS_CONFIG, Serdes.String().getClass());

        // setting offset reset to earliest so that we can re-run the demo code with the same pre-loaded data
        props.put(ConsumerConfig.AUTO_OFFSET_RESET_CONFIG, "earliest");

        TopologyBuilder builder = new TopologyBuilder();

        builder.addSource("Source", "test-topic");

        builder.addProcessor("Process", new MyProcessorSupplier(), "Source");
        builder.addStateStore(Stores.create("Counts").withStringKeys().withIntegerValues().inMemory().build(), "Process");

        builder.addSink("Sink", "streams-wordcount-processor-output", "Process");

        KafkaStreams streams = new KafkaStreams(builder, props);
        streams.start();

        // usually the stream application would be running forever,
        // in this example we just let it run for some time and stop since the input data is finite.
        Thread.sleep(5000L);

        streams.close();
    }
}</span></code></pre><span style="font-size:18px;"><br>
运行即可。</span>
            </div>
                </div>