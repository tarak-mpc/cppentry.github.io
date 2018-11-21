---
layout:     post
title:      Flume 与 Kafka整合案例
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/qq_34834325/article/details/79175764				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>Flume集群和Kafka集群安装请参考其他文章</p>
<p><br></p>
<p>A<span style="font-family:'等线';">、启动</span>Kafka集群</p>
<p>bin/kafka-server-start.sh config/server.properties</p>
<p>B<span style="font-family:'等线';">、配置</span>Flume集群，并启动Flume集群。</p>
<p>bin/flume-ng agent -n a1 -c conf -f conf/fk.conf -Dflume.root.logger=DEBUG,console</p>
<p><br></p>
<p></p>
<p><span style="font-family:'等线';">其中，</span>Flume配置文件fk.conf内容如下：</p>
<pre><code class="language-java">a1.sources = r1
a1.sinks = k1
a1.channels = c1

# Describe/configure the source
a1.sources.r1.type = avro
a1.sources.r1.bind = node1
a1.sources.r1.port = 41414

# Describe the sink
a1.sinks.k1.type = org.apache.flume.sink.kafka.KafkaSink
a1.sinks.k1.topic = testflume
a1.sinks.k1.brokerList = node1:9092,node2:9092,node3:9092
a1.sinks.k1.requiredAcks = 1
a1.sinks.k1.batchSize = 20
a1.sinks.k1.channel = c1

# Use a channel which buffers events in memory
a1.channels.c1.type = memory
a1.channels.c1.capacity = 1000000
a1.channels.c1.transactionCapacity = 10000

# Bind the source and sink to the channel
a1.sources.r1.channels = c1
a1.sinks.k1.channel = c1</code></pre><br><h3><strong>3、测试</strong></h3>
<p>Ø <span style="font-family:'等线';">分别启动</span>Zookeeper、Kafka、Flume集群。</p>
<p>Ø <span style="font-family:'等线';">创建</span>topic：</p>
<p>bin/kafka-topics.sh --zookeeper node1:2181,node2:2181,node3:2181 --create --replication-factor 2 --partitions 3 --topic testflume</p>
<p>bin/kafka-topics.sh --zookeeper node1:2181,node2:2181,node3:2181 --list</p>
<p><br></p>
<p>Ø 启动消费者：</p>
<p>bin/kafka-console-consumer.sh --zookeeper node1:2181,node2:2181,node3:2181 --from-beginning --topic testflume</p>
<p><br></p>
<p>Ø <span style="font-family:'等线';">运行</span>“RpcClientDemo”代码，通过rpc请求发送数据到Flume集群。</p>
<p><br></p>
<p>Flume中source类型为AVRO类型，此时通过Java发送rpc请求，测试数据是否传入Kafka。</p>
<p><span style="font-family:'等线';">其中，</span>Java发送Rpc请求Flume代码示例如下：</p>
<p><span style="font-family:'等线';">（参考</span>Flume官方文档：<a href="http://flume.apache.org/FlumeDeveloperGuide.html" rel="nofollow"><u><span style="color:rgb(5,99,193);">http://flume.apache.org/FlumeDeveloperGuide.html</span></u></a>）</p>
<p></p><pre><code class="language-java">import org.apache.flume.Event;
import org.apache.flume.EventDeliveryException;
import org.apache.flume.api.RpcClient;
import org.apache.flume.api.RpcClientFactory;
import org.apache.flume.event.EventBuilder;
import java.nio.charset.Charset;

/**
 * Flume官网案例
 * http://flume.apache.org/FlumeDeveloperGuide.html 
 * @author root
 */
public class RpcClientDemo {
	
	public static void main(String[] args) {
		MyRpcClientFacade client = new MyRpcClientFacade();
		// Initialize client with the remote Flume agent's host and port
		client.init("node1", 41414);

		// Send 10 events to the remote Flume agent. That agent should be
		// configured to listen with an AvroSource.
		String sampleData = "Hello Flume!";
		for (int i = 0; i &lt; 10; i++) {
			client.sendDataToFlume(sampleData);
			System.out.println("发送数据：" + sampleData);
		}

		client.cleanUp();
	}
}

class MyRpcClientFacade {
	private RpcClient client;
	private String hostname;
	private int port;

	public void init(String hostname, int port) {
		// Setup the RPC connection
		this.hostname = hostname;
		this.port = port;
		this.client = RpcClientFactory.getDefaultInstance(hostname, port);
		// Use the following method to create a thrift client (instead of the
		// above line):
		// this.client = RpcClientFactory.getThriftInstance(hostname, port);
	}

	public void sendDataToFlume(String data) {
		// Create a Flume Event object that encapsulates the sample data
		Event event = EventBuilder.withBody(data, Charset.forName("UTF-8"));

		// Send the event
		try {
			client.append(event);
		} catch (EventDeliveryException e) {
			// clean up and recreate the client
			client.close();
			client = null;
			client = RpcClientFactory.getDefaultInstance(hostname, port);
			// Use the following method to create a thrift client (instead of
			// the above line):
			// this.client = RpcClientFactory.getThriftInstance(hostname, port);
		}
	}

	public void cleanUp() {
		// Close the RPC connection
		client.close();
	}
}</code></pre><br><br><br>            </div>
                </div>