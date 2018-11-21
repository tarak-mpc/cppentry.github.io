---
layout:     post
title:      Kafka Stream 类库的使用入门
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h3>一，Kafka Stream简介</h3><p><span style="color:rgb(63,63,63);">Kafka Streams。Apache Kafka开源项目的一个组成部分。是一个功能强大，易于使用的库。用于在Kafka上构建高可分布式、拓展性，容错的应用程序。它建立在流处理的一系列重要功能基础之上，比如正确区分事件事件和处理时间，处理迟到数据以及高效的应用程序状态管理。总而言之，Kafka Stream 并不是像Hadoop spark等一样的框架，而仅仅是一个类库而已。</span></p><p></p><h3>二，Kafka Stream常用API</h3><div>      1，builder.stream(String topic, Consumed&lt;K, V&gt; consumed)</div><div>            用于读取kafka里的数据。  </div><div><img src="https://img-blog.csdn.net/20180206165129657?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUG9wcHlfRXZhbg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br></div><div>      2，<span style="background-color:rgb(255,255,255);color:rgb(0,0,0);font-family:'宋体';font-size:12pt;">builder.addStateStore()</span></div><div><span style="background-color:rgb(255,255,255);color:rgb(0,0,0);font-family:'宋体';font-size:12pt;">       创建容器用来存储你的数据</span></div><p>    </p><div>     3，<span style="background-color:rgb(255,255,255);color:rgb(0,0,0);font-family:'宋体';font-size:12pt;">context.getStateStore()</span></div><div><span style="background-color:rgb(255,255,255);color:rgb(0,0,0);font-family:'宋体';font-size:12pt;">       获取存储的数据信息</span></div><p><br></p><p>    4，<span style="background-color:rgb(255,255,255);text-align:left;">context().commit</span><br></p><p>       提交当前的处理进度</p><p></p><div>    5，<span style="background-color:rgb(255,255,255);color:rgb(0,0,0);font-family:'宋体';font-size:12pt;">context.getStateStore()</span></div><div><span style="background-color:rgb(255,255,255);color:rgb(0,0,0);font-family:'宋体';font-size:12pt;">       获取存储的数据信息（存储本地的）</span></div><p>         </p><h3>三，Kafka Stream类库的基础知识</h3><div>      1，process方法：每读取到一条数据，这个方法都会执行一遍</div><div>      2，punctuate方法：周期性的执行该方法，周期时间在init方法中调用schedule方法设置。</div><div> <img src="https://img-blog.csdn.net/20180206182333229?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUG9wcHlfRXZhbg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""></div><div>      3，Kstream:事件流，数据的记录的方式是追加（insert），后面的数据会追加到之前的数据里。可以理解为你的Kafka Stream读取topic数据时，就是存储在这             里的。</div><div>      4，Ktable：changelog流，数据的记录的方式是更新（update），相同的key后面的数据会覆盖掉前面的数据。</div><div><span style="background-color:rgb(204,204,204);">PS:由于Kstream和Ktable这两种特性，我们可以知道Kstream是不安全的，因为一旦日志数据出现压缩了，之前的key值就被删除了。这样进入的数据方式就变成了更新。</span></div><h3>四，程序的代码框架</h3><div>      第一部分，拓扑(Topology)</div><p>           整个框架的程序的入口，main方法也是写在这里。   </p><p></p><div>     第二部分，processor</div><p>           相当于storm中的spout和bolt角色。   </p><p></p><div>     第三部分，自己的实现层</div><p>           具体指标的实现的地方。</p><p>ps:</p><p>1，下面是官方给出的wordcount案例，大家可以参考下（这个例子用了lambda表达式，JAVA8的新特性）</p><p></p><pre><code class="language-html">import org.apache.kafka.common.serialization.Serdes;
import org.apache.kafka.streams.KafkaStreams;
import org.apache.kafka.streams.StreamsBuilder;
import org.apache.kafka.streams.StreamsConfig;
import org.apache.kafka.streams.Topology;
import org.apache.kafka.streams.kstream.Materialized;
import org.apache.kafka.streams.kstream.Produced;
import org.apache.kafka.streams.state.KeyValueStore;
        
import java.util.Arrays;
import java.util.Properties;
        
public class WordCountApplication {
        
    public static void main(final String[] args) throws Exception {
        Properties config = new Properties();
        config.put(StreamsConfig.APPLICATION_ID_CONFIG, "wordcount-application");
        config.put(StreamsConfig.BOOTSTRAP_SERVERS_CONFIG, "kafka-broker1:9092");
        config.put(StreamsConfig.DEFAULT_KEY_SERDE_CLASS_CONFIG, Serdes.String().getClass());
        config.put(StreamsConfig.DEFAULT_VALUE_SERDE_CLASS_CONFIG, Serdes.String().getClass());
        
        StreamsBuilder builder = new StreamsBuilder();
        KStream&lt;String, String&gt; textLines = builder.stream("TextLinesTopic");
        KTable&lt;String, Long&gt; wordCounts = textLines
            .flatMapValues(textLine -&gt; Arrays.asList(textLine.toLowerCase().split("\\W+")))
            .groupBy((key, word) -&gt; word)
            .count(Materialized.&lt;String, Long, KeyValueStore&lt;Bytes, byte[]&gt;&gt;as("counts-store"));
        wordCounts.toStream().to("WordsWithCountsTopic", Produced.with(Serdes.String(), Serdes.Long()));
        
        KafkaStreams streams = new KafkaStreams(builder.build(), config);
        streams.start();
    }
        
}</code></pre><p>2，下面这个是以Kafka Stream的processor方式计算wordcount的代码</p><p></p><pre><code class="language-html">public class WordCountProcessor implements Processor&lt;String, String&gt; {

  private ProcessorContext context;
  private KeyValueStore&lt;String, Integer&gt; kvStore;

  @SuppressWarnings("unchecked")
  @Override
  public void init(ProcessorContext context) {
    this.context = context;
    this.context.schedule(1000);
    this.kvStore = (KeyValueStore&lt;String, Integer&gt;) context.getStateStore("Counts");
  }

  @Override
  public void process(String key, String value) {
    Stream.of(value.toLowerCase().split(" ")).forEach((String word) -&gt; {
      Optional&lt;Integer&gt; counts = Optional.ofNullable(kvStore.get(word));
      int count = counts.map(wordcount -&gt; wordcount + 1).orElse(1);
      kvStore.put(word, count);
    });
  }

  @Override
  public void punctuate(long timestamp) {
    KeyValueIterator&lt;String, Integer&gt; iterator = this.kvStore.all();
    iterator.forEachRemaining(entry -&gt; {
      context.forward(entry.key, entry.value);
      this.kvStore.delete(entry.key);
    });
    context.commit();
  }

  @Override
  public void close() {
    this.kvStore.close();
  }

}</code></pre><br><br><br><div>          </div>            </div>
                </div>