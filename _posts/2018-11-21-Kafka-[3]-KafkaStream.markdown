---
layout:     post
title:      Kafka-[3]-KafkaStream
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/hjw199089/article/details/71331605				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h4 id="id-[4]Kafka-Stream-Step8:UseKafkaStreamstoprocessdata" style="color:rgb(51,51,51);font-size:14px;line-height:1.42857;font-family:Arial, sans-serif;">
<a href="http://kafka.apache.org/documentation/#quickstart_kafkastreams" rel="nofollow" class="external-link" style="color:rgb(53,114,176);">Step 8: Use Kafka Streams to process data</a></h4>
<p style="color:rgb(51,51,51);font-family:Arial, sans-serif;font-size:14px;">
Kafka Streams is a client library of Kafka for real-time stream processing and analyzing data stored in Kafka brokers. </p>
<p style="color:rgb(51,51,51);font-family:Arial, sans-serif;font-size:14px;">
</p>
<h4 style="color:rgb(51,51,51);font-size:14px;line-height:1.42857;font-family:Arial, sans-serif;">
<a href="http://kafka.apache.org/documentation/#producerapi" rel="nofollow" style="color:rgb(53,114,176);">Producer API</a></h4>
<p></p>
<p style="color:rgb(51,51,51);font-family:Arial, sans-serif;font-size:14px;">
<span style="color:rgb(0,0,0);">The Producer API allows applications to send streams of data to topics in the Kafka cluster.</span></p>
<p style="color:rgb(51,51,51);font-family:Arial, sans-serif;font-size:14px;">
Examples showing how to use the producer are given in the <a title="Kafka 0.10.2 Javadoc" href="http://kafka.apache.org/0102/javadoc/index.html?org/apache/kafka/clients/producer/KafkaProducer.html" rel="nofollow" style="color:rgb(53,114,176);">javadocs</a>.<span style="color:rgb(0,0,0);"> </span></p>
<p style="color:rgb(51,51,51);font-family:Arial, sans-serif;font-size:14px;">
To use the producer, you can use the following maven dependency:</p>
<pre style="color:rgb(51,51,51);font-size:14px;">		&lt;dependency&gt;
			&lt;groupId&gt;org.apache.kafka&lt;/groupId&gt;
			&lt;artifactId&gt;kafka-clients&lt;/artifactId&gt;
			&lt;version&gt;0.10.2.0&lt;/version&gt;
		&lt;/dependency&gt;</pre>
<h4 style="color:rgb(51,51,51);font-size:14px;line-height:1.42857;font-family:Arial, sans-serif;">
<a href="http://kafka.apache.org/documentation/#consumerapi" rel="nofollow" style="color:rgb(53,114,176);">Consumer API</a></h4>
<p style="color:rgb(51,51,51);font-family:Arial, sans-serif;font-size:14px;">
<span style="color:rgb(0,0,0);">The Consumer API allows applications to read streams of data from topics in the Kafka cluster.</span></p>
<p style="color:rgb(51,51,51);font-family:Arial, sans-serif;font-size:14px;">
Examples showing how to use the consumer are given in the <a title="Kafka 0.10.2 Javadoc" href="http://kafka.apache.org/0102/javadoc/index.html?org/apache/kafka/clients/consumer/KafkaConsumer.html" rel="nofollow" style="color:rgb(53,114,176);">javadocs</a>.</p>
<p style="color:rgb(51,51,51);font-family:Arial, sans-serif;font-size:14px;">
To use the consumer, you can use the following maven dependency:</p>
<pre style="color:rgb(51,51,51);font-size:14px;">		&lt;dependency&gt;
			&lt;groupId&gt;org.apache.kafka&lt;/groupId&gt;
			&lt;artifactId&gt;kafka-clients&lt;/artifactId&gt;
			&lt;version&gt;0.10.2.0&lt;/version&gt;
		&lt;/dependency&gt; </pre>
<h4 style="color:rgb(51,51,51);font-size:14px;line-height:1.42857;font-family:Arial, sans-serif;">
<a href="http://kafka.apache.org/documentation/#streamsapi" rel="nofollow" style="color:rgb(53,114,176);">Streams API</a></h4>
<p style="color:rgb(51,51,51);font-family:Arial, sans-serif;font-size:14px;">
<span style="color:rgb(0,0,0);">The <a href="http://kafka.apache.org/documentation/#streamsapi" rel="nofollow" style="color:rgb(53,114,176);">Streams</a> API allows transforming streams of data from input topics to output topics.</span></p>
<p style="color:rgb(51,51,51);font-family:Arial, sans-serif;font-size:14px;">
Examples showing how to use this library are given in the <a title="Kafka 0.10.2 Javadoc" href="http://kafka.apache.org/0102/javadoc/index.html?org/apache/kafka/streams/KafkaStreams.html" rel="nofollow" style="color:rgb(53,114,176);">javadocs</a></p>
<p style="color:rgb(51,51,51);font-family:Arial, sans-serif;font-size:14px;">
Additional documentation on using the Streams API is available <a href="http://kafka.apache.org/0102/documentation/streams" rel="nofollow" style="color:rgb(53,114,176);">here</a>.</p>
<p style="color:rgb(51,51,51);font-family:Arial, sans-serif;font-size:14px;">
To use Kafka Streams you can use the following maven dependency<span style="color:rgb(0,0,0);"> </span></p>
<pre style="color:rgb(51,51,51);font-size:14px;">		&lt;dependency&gt;
			&lt;groupId&gt;org.apache.kafka&lt;/groupId&gt;
			&lt;artifactId&gt;kafka-streams&lt;/artifactId&gt;
			&lt;version&gt;0.10.2.0&lt;/version&gt;
		&lt;/dependency&gt;</pre>
<h4 style="color:rgb(51,51,51);font-size:14px;line-height:1.42857;font-family:Arial, sans-serif;">
<a href="http://kafka.apache.org/documentation/#connectapi" rel="nofollow" style="color:rgb(53,114,176);">Connect API</a></h4>
<p style="color:rgb(51,51,51);font-family:Arial, sans-serif;font-size:14px;">
<span style="color:rgb(0,0,0);">The Connect API allows implementing connectors that continually pull from some source data system into Kafka or push from Kafka into some sink data system.</span></p>
<p style="color:rgb(51,51,51);font-family:Arial, sans-serif;font-size:14px;">
Many users of Connect won't need to use this API directly, though, they can use pre-built connectors without needing to write any code. Additional information on using Connect is available <a href="http://kafka.apache.org/documentation.html#connect" rel="nofollow" style="color:rgb(53,114,176);">here</a>.</p>
<p style="color:rgb(51,51,51);font-family:Arial, sans-serif;font-size:14px;">
Those who want to implement custom connectors can see the <a title="Kafka 0.10.2 Javadoc" href="http://kafka.apache.org/0102/javadoc/index.html?org/apache/kafka/connect" rel="nofollow" style="color:rgb(53,114,176);">javadoc</a>.</p>
<br><p style="color:rgb(51,51,51);font-family:Arial, sans-serif;font-size:14px;">
</p>
<pre><code class="language-java">/**
 * Licensed to the Apache Software Foundation (ASF) under one or more
 * contributor license agreements.  See the NOTICE file distributed with
 * this work for additional information regarding copyright ownership.
 * The ASF licenses this file to You under the Apache License, Version 2.0
 * (the "License"); you may not use this file except in compliance with
 * the License.  You may obtain a copy of the License at
 *
 *    http://www.apache.org/licenses/LICENSE-2.0
 *
 * Unless required by applicable law or agreed to in writing, software
 * distributed under the License is distributed on an "AS IS" BASIS,
 * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
 * See the License for the specific language governing permissions and
 * limitations under the License.
 */
 
package streams.examples.wordcount;
 
import org.apache.kafka.clients.consumer.ConsumerConfig;
import org.apache.kafka.common.serialization.Serdes;
import org.apache.kafka.streams.KafkaStreams;
import org.apache.kafka.streams.KeyValue;
import org.apache.kafka.streams.StreamsConfig;
import org.apache.kafka.streams.kstream.KStreamBuilder;
import org.apache.kafka.streams.kstream.KStream;
import org.apache.kafka.streams.kstream.KTable;
import org.apache.kafka.streams.kstream.KeyValueMapper;
import org.apache.kafka.streams.kstream.ValueMapper;
 
import java.util.Arrays;
import java.util.Locale;
import java.util.Properties;
 
/**
 * Demonstrates, using the high-level KStream DSL, how to implement the WordCount program
 * that computes a simple word occurrence histogram from an input text.
 *
 * In this example, the input stream reads from a topic named "streams-file-input", where the values of messages
 * represent lines of text; and the histogram output is written to topic "streams-wordcount-output" where each record
 * is an updated count of a single word.
 *
 * Before running this example you must create the input topic and the output topic (e.g. via
 * bin/kafka-topics.sh --create ...), and write some data to the input topic (e.g. via
 * bin/kafka-console-producer.sh). Otherwise you won't see any data arriving in the output topic.
 */
public class WordCountDemo {
 
    public static void main(String[] args) throws Exception {
        Properties props = new Properties();
        props.put(StreamsConfig.APPLICATION_ID_CONFIG, "streams-wordcount");
        props.put(StreamsConfig.BOOTSTRAP_SERVERS_CONFIG, "localhost:9092");
        props.put(StreamsConfig.KEY_SERDE_CLASS_CONFIG, Serdes.String().getClass().getName());
        props.put(StreamsConfig.VALUE_SERDE_CLASS_CONFIG, Serdes.String().getClass().getName());
 
        // setting offset reset to earliest so that we can re-run the demo code with the same pre-loaded data
        // Note: To re-run the demo, you need to use the offset reset tool:
        // https://cwiki.apache.org/confluence/display/KAFKA/Kafka+Streams+Application+Reset+Tool
        props.put(ConsumerConfig.AUTO_OFFSET_RESET_CONFIG, "earliest");
 
        KStreamBuilder builder = new KStreamBuilder();
 
        KStream&lt;String, String&gt; source = builder.stream("streams-file-input");
 
        KTable&lt;String, Long&gt; counts = source
                .flatMapValues(new ValueMapper&lt;String, Iterable&lt;String&gt;&gt;() {
                    @Override
                    public Iterable&lt;String&gt; apply(String value) {
                        return Arrays.asList(value.toLowerCase(Locale.getDefault()).split(" "));
                    }
                }).map(new KeyValueMapper&lt;String, String, KeyValue&lt;String, String&gt;&gt;() {
                    @Override
                    public KeyValue&lt;String, String&gt; apply(String key, String value) {
                        return new KeyValue&lt;&gt;(value, value);
                    }
                })
                .groupByKey()
                .count("Counts");
 
        // need to override value serde to Long type
        counts.to(Serdes.String(), Serdes.Long(), "streams-wordcount-output");
 
        KafkaStreams streams = new KafkaStreams(builder, props);
        streams.start();
 
        // usually the stream application would be running forever,
        // in this example we just let it run for some time and stop since the input data is finite.
        Thread.sleep(5000L);
 
        streams.close();
    }
}</code></pre><br><p></p>
<div id="main-content" class="wiki-content" style="color:rgb(51,51,51);font-family:Arial, sans-serif;font-size:14px;">
<pre style="font-family:ConfluenceInstalledFont, monospace;">It implements the WordCount algorithm, which computes a word occurrence histogram from the input text. However, unlike other WordCount examples you might have seen before that operate on bounded data, the WordCount demo application behaves slightly differently because it is designed to operate on an <span style="font-family:Arial, sans-serif;">infinite, unbounded stream</span> of data. Similar to the bounded variant, it is a stateful algorithm that tracks and updates the counts of words. However, since it must assume potentially unbounded input data, it will periodically output its current state and results while continuing to process more data because it cannot know when it has processed "all" the input data.</pre>
<p>
As the first step, we will prepare input data to a Kafka topic, which will subsequently be processed by a Kafka Streams application.</p>
<pre style="font-family:ConfluenceInstalledFont, monospace;">&gt; <strong>echo -e "all streams lead to kafka\nhello kafka streams\njoin kafka summit" &gt; file-input.txt</strong>
</pre>
<p>
Next, we send this input data to the input topic named <strong>streams-file-input</strong> using the console producer, which reads the data from STDIN line-by-line, and publishes each line as a separate Kafka message with null key and value encoded a string
 to the topic (in practice, stream data will likely be flowing continuously into Kafka where the application will be up and running):</p>
<pre style="font-family:ConfluenceInstalledFont, monospace;">&gt; <strong>bin/kafka-topics.sh --create \
            --zookeeper localhost:2181 \
            --replication-factor 1 \
            --partitions 1 \
            --topic streams-file-input</strong>
</pre>
<pre style="font-family:ConfluenceInstalledFont, monospace;">&gt; <strong>bin/kafka-console-producer.sh --broker-list localhost:9092 --topic streams-file-input &lt; file-input.txt</strong>
</pre>
<p>
We can now run the WordCount demo application to process the input data:</p>
<pre style="font-family:ConfluenceInstalledFont, monospace;">&gt; <strong>bin/kafka-run-class.sh org.apache.kafka.streams.examples.wordcount.WordCountDemo</strong>
</pre>
<p>
The demo application will read from the input topic <strong>streams-file-input</strong>, perform the computations of the WordCount algorithm on each of the read messages, and continuously write its current results to the output topic <strong>streams-wordcount-output</strong>.
 Hence there won't be any STDOUT output except log entries as the results are written back into in Kafka. The demo will run for a few seconds and then, unlike typical stream processing applications, terminate automatically.</p>
<p>
We can now inspect the output of the WordCount demo application by reading from its output topic:</p>
<pre style="font-family:ConfluenceInstalledFont, monospace;">&gt; <strong>bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 \
            --topic streams-wordcount-output \
            --from-beginning \
            --formatter kafka.tools.DefaultMessageFormatter \
            --property print.key=true \
            --property print.value=true \
            --property key.deserializer=org.apache.kafka.common.serialization.StringDeserializer \
            --property value.deserializer=org.apache.kafka.common.serialization.LongDeserializer</strong>
</pre>
<p>
with the following output data being printed to the console:</p>
<pre style="font-family:ConfluenceInstalledFont, monospace;">all     1
lead    1
to      1
hello   1
streams 2
join    1
kafka   3
summit  1
</pre>
<p>
Here, the first column is the Kafka message key in <code>java.lang.String</code> format, and the second column is the message value in <code>java.lang.Long</code> format. Note that the output is actually a continuous stream of updates, where each data record
 (i.e. each line in the original output above) is an updated count of a single word, aka record key such as "kafka". For multiple records with the same key, each later record is an update of the previous one.</p>
<p>
The two diagrams below illustrate what is essentially happening behind the scenes. The first column shows the evolution of the current state of the <code>KTable&lt;String, Long&gt;</code> that is counting word occurrences for <code>count</code>. The second column
 shows the change records that result from state updates to the KTable and that are being sent to the output Kafka topic <strong>streams-wordcount-output</strong>.</p>
<p>
<img src="https://img-blog.csdn.net/20171103153226044?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaGp3MTk5MDg5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" width="200" alt=""><img src="https://img-blog.csdn.net/20171103153304380?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaGp3MTk5MDg5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" width="200" alt=""><br></p>
<p>
<span class="confluence-embedded-file-wrapper confluence-embedded-manual-size" style="display:inline-block;"></span><span class="confluence-embedded-file-wrapper confluence-embedded-manual-size" style="display:inline-block;"><br></span></p>
<p>
First the text line “all streams lead to kafka” is being processed. The <code>KTable</code> is being built up as each new word results in a new table entry (highlighted with a green background), and a corresponding change record is sent to the downstream <code>KStream</code>.</p>
<p>
When the second text line “hello kafka streams” is processed, we observe, for the first time, that existing entries in the <code>KTable</code> are being updated (here: for the words “kafka” and for “streams”). And again, change records are being sent to the
 output topic.</p>
<p>
And so on (we skip the illustration of how the third line is being processed). This explains why the output topic has the contents we showed above, because it contains the full record of changes.</p>
<p>
Looking beyond the scope of this concrete example, what Kafka Streams is doing here is to leverage the duality between a table and a changelog stream (here: table = the KTable, changelog stream = the downstream KStream): you can publish every change of the
 table to a stream, and if you consume the entire changelog stream from beginning to end, you can reconstruct the contents of the table.</p>
<p>
Now you can write more input messages to the <strong>streams-file-input</strong> topic and observe additional messages added to <strong>streams-wordcount-output</strong> topic, reflecting updated word counts (e.g., using the console producer and the console
 consumer, as described above).</p>
<p>
You can stop the console consumer via <strong>Ctrl-C</strong>.</p>
</div>
<div id="likes-and-labels-container" style="overflow:hidden;clear:both;color:rgb(51,51,51);font-family:Arial, sans-serif;font-size:14px;">
<div id="likes-section" style="font-size:13px;width:470.391px;">
</div>
</div>
<br><p></p>
            </div>
                </div>