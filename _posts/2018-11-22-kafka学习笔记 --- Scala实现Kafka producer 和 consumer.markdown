---
layout:     post
title:      kafka学习笔记 --- Scala实现Kafka producer 和 consumer
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：学习交流为主，未经博主同意禁止转载，禁止用于商用。					https://blog.csdn.net/u012965373/article/details/74548388				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-size:18px;">共两种实现方式：</span></p>
<p><span style="font-size:18px;">One:</span></p>
<p><span style="font-size:18px;"></span></p>
<pre style="background-color:#2b2b2b;color:#a9b7c6;font-family:'Source Code Pro';font-size:11.3pt;"><span style="color:#cc7832;"><strong>package </strong></span>zetdata

<span style="color:#cc7832;"><strong>import </strong></span>java.util.Properties

<span style="color:#cc7832;"><strong>import </strong></span>org.apache.kafka.clients.producer.{KafkaProducer<span style="color:#cc7832;">, </span>ProducerRecord}

<span style="color:#629755;"><em>/**
</em></span><span style="color:#629755;"><em>  * Created by ryan on 17-7-6.
</em></span><span style="color:#629755;"><em>  */
</em></span><span style="color:#cc7832;"><strong>object </strong></span>ScalaProducerExample <span style="color:#cc7832;"><strong>extends  </strong></span>App{
  <span style="color:#cc7832;"><strong>val </strong></span><span style="color:#9876aa;"><em>topic </em></span>= <span style="color:#6a8759;">"app"
</span><span style="color:#6a8759;">  </span><span style="color:#cc7832;"><strong>val </strong></span><span style="color:#9876aa;"><em>brokers </em></span>= <span style="color:#6a8759;">"192.168.1.81:6667"
</span><span style="color:#6a8759;">  </span><span style="color:#cc7832;"><strong>val </strong></span><span style="color:#9876aa;"><em>props </em></span>= <span style="color:#cc7832;"><strong>new </strong></span>Properties()
  <span style="color:#9876aa;"><em>props</em></span>.put(<span style="color:#6a8759;">"bootstrap"</span><span style="color:#cc7832;">, </span><span style="color:#9876aa;"><em>brokers</em></span>)
  <span style="color:#9876aa;"><em>props</em></span>.put(<span style="color:#6a8759;">"client.id"</span><span style="color:#cc7832;">, </span><span style="color:#6a8759;">"ScalaProducerExample"</span>)
  <span style="color:#9876aa;"><em>props</em></span>.put(<span style="color:#6a8759;">"key.serializer"</span><span style="color:#cc7832;">,</span>org.apache.kafka.common.serialization.StringSerializer)
  <span style="color:#9876aa;"><em>props</em></span>.put(<span style="color:#6a8759;">"value.serializer"</span><span style="color:#cc7832;">, </span>org.apache.kafka.common.serialization.StringDeserializer)

  <span style="color:#cc7832;"><strong>val </strong></span><span style="color:#9876aa;"><em>producer </em></span>= <span style="color:#cc7832;"><strong>new </strong></span>KafkaProducer[<span style="color:#4e807d;">String</span><span style="color:#cc7832;">, </span><span style="color:#4e807d;">String</span>](<span style="color:#9876aa;"><em>props</em></span>)
  <span style="color:#cc7832;"><strong>val </strong></span><span style="color:#9876aa;"><em>t </em></span>= System.<span style="font-style:italic;">currentTimeMillis</span>()
  <span style="color:#cc7832;"><strong>val </strong></span><span style="color:#9876aa;"><em>msg </em></span>= <span style="color:#6a8759;">"hello, I'm test message!"
</span><span style="color:#6a8759;">  </span><span style="color:#cc7832;"><strong>val </strong></span><span style="color:#9876aa;"><em>record </em></span>= <span style="color:#cc7832;"><strong>new </strong></span>ProducerRecord[<span style="color:#4e807d;">String</span><span style="color:#cc7832;">, </span><span style="color:#4e807d;">String</span>](<span style="color:#9876aa;"><em>topic</em></span><span style="color:#cc7832;">, </span><span style="color:#6a8759;">"key"</span><span style="color:#cc7832;">, </span><span style="color:#9876aa;"><em>msg</em></span>)
  <span style="color:#9876aa;"><em>producer</em></span>.send(<span style="color:#9876aa;"><em>record</em></span>)
  <span style="color:#9876aa;"><em>producer</em></span>.close()
}
</pre>
<br><p></p>
<p><span style="font-size:18px;"></span></p>
<pre style="background-color:#2b2b2b;color:#a9b7c6;font-family:'Source Code Pro';font-size:11.3pt;"><span style="color:#cc7832;"><strong>package </strong></span>zetdata

<span style="color:#cc7832;"><strong>import </strong></span>java.util.Collections
<span style="color:#cc7832;"><strong>import </strong></span>java.util.Properties

<span style="color:#cc7832;"><strong>import </strong></span>org.apache.kafka.clients.consumer.KafkaConsumer

<span style="color:#629755;"><em>/**
</em></span><span style="color:#629755;"><em>  * Created by ryan on 17-7-6.
</em></span><span style="color:#629755;"><em>  */
</em></span><span style="color:#629755;"><em>
</em></span><span style="color:#cc7832;"><strong>object </strong></span>ScalaComsumerExample <span style="color:#cc7832;"><strong>extends </strong></span>App {
  <span style="color:#cc7832;"><strong>val </strong></span><span style="color:#9876aa;"><em>topic </em></span>= <span style="color:#6a8759;">"app"
</span><span style="color:#6a8759;">  </span><span style="color:#cc7832;"><strong>val </strong></span><span style="color:#9876aa;"><em>brokers </em></span>= <span style="color:#6a8759;">"192.168.1.81:6667"
</span><span style="color:#6a8759;">  </span><span style="color:#cc7832;"><strong>val </strong></span><span style="color:#9876aa;"><em>props </em></span>= <span style="color:#cc7832;"><strong>new </strong></span>Properties()
  <span style="color:#9876aa;"><em>props</em></span>.put(<span style="color:#6a8759;">"bootstrap"</span><span style="color:#cc7832;">, </span><span style="color:#9876aa;"><em>brokers</em></span>)
  <span style="color:#9876aa;"><em>props</em></span>.put(<span style="color:#6a8759;">"client.id"</span><span style="color:#cc7832;">, </span><span style="color:#6a8759;">"ScalaProducerExample"</span>)
  <span style="color:#9876aa;"><em>props</em></span>.put(<span style="color:#6a8759;">"key.serializer"</span><span style="color:#cc7832;">, </span>org.apache.kafka.common.serialization.StringSerializer)
  <span style="color:#9876aa;"><em>props</em></span>.put(<span style="color:#6a8759;">"value.serializer"</span><span style="color:#cc7832;">, </span>org.apache.kafka.common.serialization.StringDeserializer)
  <span style="color:#cc7832;"><strong>val </strong></span><span style="color:#9876aa;"><em>consumer </em></span>= <span style="color:#cc7832;"><strong>new </strong></span>KafkaConsumer[<span style="color:#4e807d;">String</span><span style="color:#cc7832;">, </span><span style="color:#4e807d;">String</span>](<span style="color:#9876aa;"><em>props</em></span>)
  <span style="color:#9876aa;"><em>consumer</em></span>.subscribe(Collections.<span style="font-style:italic;">singleton</span>(<span style="color:#9876aa;"><em>topic</em></span>))
  <span style="color:#cc7832;"><strong>val </strong></span><span style="color:#9876aa;"><em>records </em></span>= <span style="color:#9876aa;"><em>consumer</em></span>.poll(<span style="color:#6897bb;">1000</span>)
  <span style="color:#cc7832;"><strong>for </strong></span>(record &lt;- <span style="color:#9876aa;"><em>records</em></span>){
    <span style="font-style:italic;">println</span>(record)
  }
  <span style="color:#9876aa;"><em>consumer</em></span>.close()
}

</pre>
<br><p></p>
<p><span style="font-size:18px;">Two:</span></p>
<p><span style="font-size:18px;"></span></p>
<pre style="background-color:#2b2b2b;color:#a9b7c6;font-family:'Source Code Pro';font-size:11.3pt;"><span style="color:#cc7832;"><strong>package </strong></span>zetdata

<span style="color:#cc7832;"><strong>import </strong></span>java.util.Properties

<span style="color:#cc7832;"><strong>import </strong></span>org.apache.kafka.clients.producer._

<span style="color:#629755;"><em>/**
</em></span><span style="color:#629755;"><em>  * Created by ryan on 17-7-5.
</em></span><span style="color:#629755;"><em>  */
</em></span><span style="color:#cc7832;"><strong>object </strong></span>ProduceExample <span style="color:#cc7832;"><strong>extends </strong></span>App{
  <span style="color:#cc7832;"><strong>val </strong></span><span style="color:#9876aa;"><em>props </em></span>= <span style="color:#cc7832;"><strong>new </strong></span>Properties()
  <span style="color:#cc7832;"><strong>val </strong></span><span style="color:#9876aa;"><em>TOPIC </em></span>= <span style="color:#6a8759;">"yangxin_test1"
</span><span style="color:#6a8759;">  </span><span style="color:#9876aa;"><em>props</em></span>.put(<span style="color:#6a8759;">"bootstrap.servers"</span><span style="color:#cc7832;">, </span><span style="color:#6a8759;">"192.168.1.81:6667"</span>)
  <span style="color:#9876aa;"><em>props</em></span>.put(<span style="color:#6a8759;">"key.serializer"</span><span style="color:#cc7832;">, </span><span style="color:#6a8759;">"org.apache.kafka.common.serialization.StringSerializer"</span>)
  <span style="color:#9876aa;"><em>props</em></span>.put(<span style="color:#6a8759;">"value.serializer"</span><span style="color:#cc7832;">, </span><span style="color:#6a8759;">"org.apache.kafka.common.serialization.StringSerializer"</span>)
  <span style="color:#9876aa;"><em>props</em></span>.put(<span style="color:#6a8759;">"metadata.broker.list"</span><span style="color:#cc7832;">, </span><span style="color:#6a8759;">"192.168.1.84:6667"</span>)
  <span style="color:#9876aa;"><em>props</em></span>.put(<span style="color:#6a8759;">"group.id"</span><span style="color:#cc7832;">, </span><span style="color:#6a8759;">"something"</span>)
  <span style="color:#cc7832;"><strong>val </strong></span><span style="color:#9876aa;"><em>producer </em></span>= <span style="color:#cc7832;"><strong>new </strong></span>KafkaProducer[<span style="color:#4e807d;">String</span><span style="color:#cc7832;">, </span><span style="color:#4e807d;">String</span>](<span style="color:#9876aa;"><em>props</em></span>)
  <span style="color:#cc7832;"><strong>for </strong></span>( i &lt;- <span style="color:#6897bb;">1 </span>to <span style="color:#6897bb;">50</span>){
      <span style="color:#cc7832;"><strong>val </strong></span>record = <span style="color:#cc7832;"><strong>new </strong></span>ProducerRecord(<span style="color:#9876aa;"><em>TOPIC</em></span><span style="color:#cc7832;">, </span><span style="color:#6a8759;">"key"</span><span style="color:#cc7832;">, </span><span style="color:#6a8759;">s"hello </span><span style="color:#00b8bb;"><strong>$</strong></span>i<span style="color:#6a8759;">"</span>)
      <span style="font-style:italic;">println</span>(record)
      <span style="color:#9876aa;"><em>producer</em></span>.send(record)
  }
  <span style="color:#cc7832;"><strong>val </strong></span><span style="color:#9876aa;"><em>record </em></span>= <span style="color:#cc7832;"><strong>new </strong></span>ProducerRecord(<span style="color:#9876aa;"><em>TOPIC</em></span><span style="color:#cc7832;">, </span><span style="color:#6a8759;">"key"</span><span style="color:#cc7832;">, </span><span style="color:#6a8759;">"the end " </span>+ <span style="color:#cc7832;"><strong>new </strong></span>java.util.Date)
  <span style="color:#9876aa;"><em>producer</em></span>.send(<span style="color:#9876aa;"><em>record</em></span>)
  <span style="font-style:italic;">println</span>(<span style="color:#9876aa;"><em>record</em></span>)
  <span style="color:#9876aa;"><em>producer</em></span>.close()
}
</pre>
<pre style="background-color:#2b2b2b;color:#a9b7c6;font-family:'Source Code Pro';font-size:11.3pt;"><span style="color:#cc7832;"><strong>package </strong></span>zetdata

<span style="color:#cc7832;"><strong>import </strong></span>java.util
<span style="color:#cc7832;"><strong>import </strong></span>java.util.Properties

<span style="color:#cc7832;"><strong>import </strong></span>scala.collection.JavaConverters._
<span style="color:#cc7832;"><strong>import </strong></span>org.apache.kafka.clients.consumer.KafkaConsumer

<span style="color:#629755;"><em>/**
</em></span><span style="color:#629755;"><em>  * Created by ryan on 17-7-5.
</em></span><span style="color:#629755;"><em>  */
</em></span><span style="color:#cc7832;"><strong>object </strong></span>ConsumerExample <span style="color:#cc7832;"><strong>extends </strong></span>App{
  <span style="color:#cc7832;"><strong>val </strong></span><span style="color:#9876aa;"><em>TOPIC </em></span>= <span style="color:#6a8759;">"yangxin_test1"
</span><span style="color:#6a8759;">  </span><span style="color:#cc7832;"><strong>val </strong></span><span style="color:#9876aa;"><em>props </em></span>= <span style="color:#cc7832;"><strong>new </strong></span>Properties()
  <span style="color:#9876aa;"><em>props</em></span>.put(<span style="color:#6a8759;">"bootstrap.servers"</span><span style="color:#cc7832;">, </span><span style="color:#6a8759;">"192.168.1.81:6667"</span>)
  <span style="color:#9876aa;"><em>props</em></span>.put(<span style="color:#6a8759;">"key.deserializer"</span><span style="color:#cc7832;">, </span><span style="color:#6a8759;">"org.apache.kafka.common.serialization.StringDeserializer"</span>)
  <span style="color:#9876aa;"><em>props</em></span>.put(<span style="color:#6a8759;">"value.deserializer"</span><span style="color:#cc7832;">, </span><span style="color:#6a8759;">"org.apache.kafka.common.serialization.StringDeserializer"</span>)
  <span style="color:#9876aa;"><em>props</em></span>.put(<span style="color:#6a8759;">"group.id"</span><span style="color:#cc7832;">, </span><span style="color:#6a8759;">"something"</span>)
  <span style="color:#cc7832;"><strong>val </strong></span><span style="color:#9876aa;"><em>consumer </em></span>= <span style="color:#cc7832;"><strong>new </strong></span>KafkaConsumer[<span style="color:#4e807d;">String</span><span style="color:#cc7832;">, </span><span style="color:#4e807d;">String</span>](<span style="color:#9876aa;"><em>props</em></span>)
  <span style="color:#9876aa;"><em>consumer</em></span>.subscribe(util.Collections.<span style="font-style:italic;">singletonList</span>(<span style="color:#9876aa;"><em>TOPIC</em></span>))
  <span style="color:#cc7832;"><strong>while</strong></span>(<span style="color:#cc7832;"><strong>true</strong></span>) {
    <span style="color:#cc7832;"><strong>val </strong></span>records = <span style="color:#9876aa;"><em>consumer</em></span>.poll(<span style="color:#6897bb;">100</span>)
    <span style="color:#cc7832;"><strong>for </strong></span>(record &lt;- records.asScala) {
      <span style="font-style:italic;">println</span>(record)
    }
  }
}
</pre>
<br><p></p>
<p><span style="font-size:18px;">具体运行可在编辑器中</span></p>
<p><span style="font-size:18px;">分别运行 producer 与consumer。</span></p>
<p><span style="font-size:18px;">或者在linux环境，</span></p>
<p><span style="font-size:18px;">利用mavern </span></p>
<p><span style="font-size:18px;">mvn package将程序打包然后：</span></p>
<p><span style="font-size:18px;"><br></span></p>
<p><span style="font-size:18px;"></span></p>
<pre>java -cp kafka_example-0.1.0-SNAPSHOT.jar zetdata.ScalaProducerExample 10000 test_topic localhost:6667</pre>
<p></p>
<p><br></p>
            </div>
                </div>