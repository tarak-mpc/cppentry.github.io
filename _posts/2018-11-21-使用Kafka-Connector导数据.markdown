---
layout:     post
title:      使用Kafka-Connector导数据
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/m1361459098/article/details/79813838				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h1>从mysql导数据到kafka</h1><h2>1、kafka confluent </h2><p>  介绍  <a href="https://blog.csdn.net/xianzhen376/article/details/51281420?t=1466961306713" rel="nofollow" style="font-size:16px;font-weight:normal;">link</a></p><p>     。。。</p><h2>2、kafka connector-jdbc </h2><p>  介绍 <a href="https://www.jianshu.com/p/70712aea94fc" rel="nofollow">link</a></p><p>    先安装kafka，然后下载<a href="http://packages.confluent.io/archive/4.0/confluent-oss-4.0.0-2.11.zip" rel="nofollow">confluent的包</a>，默认这个安装包中已经包含了kafka、zookeeper等一些列kafka相关的东西。看实际情况。我这里是已经自己安装了apache kafka 。</p><p>  启动 <span style="background-color:rgb(240,240,240);color:rgb(0,0,0);font-family:Consolas, Inconsolata, Courier, monospace;font-size:12px;">schema.registry服务</span></p><p><span style="background-color:rgb(240,240,240);color:rgb(0,0,0);font-family:Consolas, Inconsolata, Courier, monospace;font-size:12px;"> 通过standalone模式启动jdbc-Connector</span></p><pre><code class="language-javascript"> bin/connect-standalone  etc/schema-registry/connect-avro-standalone.properties etc/kafka-connect-jdbc/quickstart-mysql.properties </code></pre> <pre><code class="language-javascript"> vi etc/kafka-connect-jdbc/quickstart-mysql.properties 
name=test-mysql-jdbc-autoincrement
connector.class=io.confluent.connect.jdbc.JdbcSourceConnector
tasks.max=1
connection.url=jdbc:mysql://192.168.0.35:3306/test?user=root&amp;password=
mode=incrementing
incrementing.column.name=id
topics=t_resources
topic.prefix=test-mysql
table.whitelist=t_resources</code></pre><br><h2>3、通过java读取 Connector-jdbc写入的数据</h2><p>  需要confluent的jar。</p><p>        &lt;dependency&gt;<br><span>		</span>  &lt;groupId&gt;io.confluent&lt;/groupId&gt;<br><span>		</span>  &lt;artifactId&gt;kafka-avro-serializer&lt;/artifactId&gt;<br><span>		</span>  &lt;version&gt;3.1.1&lt;/version&gt;<br><span>	</span>&lt;/dependency&gt;<br></p><pre><code class="language-javascript">package com.test.kafka;

import java.util.Collections;
import java.util.Properties;

import org.apache.avro.generic.GenericData;
import org.apache.kafka.clients.consumer.ConsumerRecord;
import org.apache.kafka.clients.consumer.ConsumerRecords;
import org.apache.kafka.clients.consumer.KafkaConsumer;
import org.apache.kafka.common.TopicPartition;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
 
/**
 * reference https://www.iteblog.com on 2017-09-20.
 */
public class AvroKafkaConsumer09 {
    public static void main(String[] args) {
        Logger logger = LoggerFactory.getLogger("AvroKafkaConsumer");
        Properties props = new Properties();
        props.put("bootstrap.servers", "192.168.0.83:9092");
        props.put("group.id", "testgroup");
        props.put("key.deserializer", "org.apache.kafka.common.serialization.StringDeserializer");
       // props.put("value.deserializer", "org.apache.kafka.common.serialization.StringDeserializer");
        props.put("value.deserializer", "io.confluent.kafka.serializers.KafkaAvroDeserializer");
        props.put("schema.registry.url", "http://192.168.0.83:8081");
       
        KafkaConsumer&lt;String, GenericData.Record&gt; consumer = new KafkaConsumer&lt;&gt;(props);
        String topic = "test-mysqlt_resources";
       // consumer.subscribe(Collections.singletonList(topic));
//        Schema.Parser parser = new Schema.Parser();
//        Schema schema = parser.parse(AvroKafkaProducter.USER_SCHEMA);
//        Injection&lt;GenericRecord, byte[]&gt; recordInjection = GenericAvroCodecs.toBinary(schema);
// 
        try {
        	consumer.assign(Collections.singletonList( new TopicPartition( topic, 0) ) );
        	consumer.seek( new TopicPartition( topic, 0), 0L ); //add
            while (true) {
                ConsumerRecords&lt;String,  GenericData.Record&gt; records = consumer.poll(1000);
                for (ConsumerRecord&lt;String, GenericData.Record&gt; record : records) {
                	
                	logger.info(  record.key()+ "&gt;Tostring:"+  record.value().toString() +"\n"  );
                	
//                    GenericRecord genericRecord = recordInjection.invert(record.value()).get();
//                    logger.info(   "Tostring:"+genericRecord.toString()+"\n"  );
                }
            }
        } finally {
            consumer.close();
        }
    }
}</code></pre><h2>4、通过distributed模式运行connect</h2><p>   </p><p>1、connect-avro-distributed.properties</p><p> 注意配置项：</p><pre><code class="language-javascript">plugin.path=/root/kafka/tools/confluent-4.0.0/share/java #绝对路径，/root/kafka/tools/confluent-4.0.0 为confluent安装目录</code></pre><p>2、启动connect-distributed </p><p>cd confluent安装目录</p><pre><code class="language-javascript">[root@server confluent-4.0.0]# nohup ./bin/connect-distributed ./etc/schema-registry/connect-avro-distributed.properties &gt; /tmp/kafka-cluster-connect.log &amp;</code></pre><p>3、REST-API管理connector </p><p>  </p><pre><code class="language-javascript">curl -X POST -H "Content-Type: application/json" --data '{"name": "test-mysql-jdbc-autoincrement","config": {"connector.class": "JdbcSourceConnector","connection.url": "jdbc:mysql://192.168.0.15:3306/test?user=root&amp;password=","tasks.max": "1","mode": "incrementing","incrementing.column.name": "id","topic.prefix": "t_resources","table.whitelist": "test-mysql","topics": "connect-test"}}' http://localhost:8083/connectors</code></pre><br><p>详细 <a href="https://docs.confluent.io/current/connect/managing.html#connect-managing-rest-examples" rel="nofollow">link</a> <br></p><p><br></p>            </div>
                </div>