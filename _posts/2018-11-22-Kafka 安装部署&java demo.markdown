---
layout:     post
title:      Kafka 安装部署&java demo
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><br></p>
<p>kafka英文网站：http://kafka.apache.org/documentation/</p>
<p>kafka中文教程：http://orchome.com/kafka/index  （翻译官网）</p>
<p>视频学习：http://edu.csdn.net/course/detail/5823 </p>
<p><br></p>
<h1>下载安装</h1>
<div>参考文章：</div>
<div>http://czj4451.iteye.com/blog/2041096</div>
<div>http://www.cnblogs.com/wangyangliuping/p/5546465.html<br></div>
<div><br></div>
<div>本项目，官网下载了，版本：kafka_2.11-0.11.0.1.tgz</div>
<div>下载页面：http://kafka.apache.org/downloads.html <span style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;"> </span></div>
<div>解压：tar zxvf kafka_2.11-0.11.0.1.tgz</div>
<div>直接在 linux 终端下载（没有测试）： </div>
<div><pre><code class="language-html">wget -q http://apache.fayea.com/apache-mirror/kafka/0.8.1/kafka_2.8.0-0.8.1.tgz </code></pre>或者：</div>
<div><pre><code class="language-html">curl -L -O http://mirrors.cnnic.cn/apache/kafka/0.9.0.0/kafka_2.10-0.9.0.0.tgz </code></pre>下载解压之后</div>
<div>进行启动停止和测试：</div>
<div>在 kafka 的目录下执行，cd /home/itweb/kafka_2.11-0.11.0.1</div>
<div><br></div>
<div>启动 <span style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">Zookeeper server linux命令： </span></div>
<div><span style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;"></span><pre><code class="language-html">bin/zookeeper-server-start.sh config/zookeeper.properties &amp;  </code></pre>&amp;是为了能退出命令行</div>
<div><span style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;"><br></span></div>
<div><span style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">启动 kafka server：</span></div>
<div><span style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;"></span><pre><code class="language-html">bin/kafka-server-start.sh config/server.properties &amp;  </code></pre>
<div><span style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;"><br></span></div>
停止 kafka server：</div>
<div><span style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;"></span><pre><code class="language-html">bin/kafka-server-stop.sh  </code></pre><br>
停止 zookeeper server：</div>
<div><span style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;"></span><pre><code class="language-html">bin/zookeeper-server-stop.sh </code></pre><br>
单机连通性测试：</div>
<div>运行 producer:</div>
<div><span style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;"></span><pre><code class="language-html">bin/kafka-console-producer.sh --broker-list localhost:9092 --topic test</code></pre><br></div>
<div>
<div>最后在另一个窗口，运行 consumer：</div>
<div><pre><code class="language-html">bin/kafka-console-consumer.sh --zookeeper localhost:2181 --topic test --from-beginning  </code></pre><br></div>
在运行 producer 的窗口输入任意字符，在 consumer 的窗口，可以看到你输入的字符。</div>
<div><br></div>
<div>删除 topic 的数据：</div>
<div>1.将 kafka 启动时加载的配置文件 server.properties 的配置delete.topic.enable = true</div>
<div>2.执行：</div>
<div><pre><code class="language-html">bin/kafka-topics.sh -delete --zookeeper localhost:2181 --topic test </code></pre></div>
<div><span style="background-color:rgb(250,250,250);font-size:1em;font-family:Monaco, 'DejaVu Sans Mono', 'Bitstream Vera Sans Mono', Consolas, 'Courier New', monospace;"><br></span></div>
<div><span style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">server.properties 指明连接 zookeeper 的地址：</span></div>
<div><span style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;"></span><pre><code class="language-html">zookeeper.connect=localhost:2181</code></pre><br>
producer.properties 指明 bootstrap 的连接地址</div>
<div><span style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;"></span><pre><code class="language-html">bootstrap.servers=localhost:9092</code></pre><br>
consumer.properties 指明 zookeeper 的连接地址：<br><pre><code class="language-html">zookeeper.connect=127.0.0.1:2181</code></pre><br>
kafka 的 zookeeper.properties 指明 zookeeper 的端口：</div>
<div><span style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;"></span><pre><code class="language-html">clientPort=2181</code></pre><br>
检查 2181 和 9092 的端口使用情况：</div>
<div><span style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;"></span><pre><code class="language-html">netstat -tunlp|egrep "(2181|9092)"</code></pre><br><h1><span style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">java 编码 demo</span></h1>
</div>
<div><span style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">参考文章：</span></div>
<div><span style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">http://blog.csdn.net/qq_20641565/article/details/56277537<br></span></div>
<div><span style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">http://blog.sina.com.cn/s/blog_998c49430102x49o.html<br></span></div>
<div><span style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;"><br></span></div>
<div><span style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">添加 maven 依赖：</span></div>
<div><span style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;"></span><pre><code class="language-html">	&lt;dependency&gt;
            &lt;groupId&gt;org.apache.kafka&lt;/groupId&gt;
            &lt;artifactId&gt;kafka_2.11&lt;/artifactId&gt;
            &lt;version&gt;0.11.0.1&lt;/version&gt;
        &lt;/dependency&gt;</code></pre>
<div><br></div>
编写 kafka 的生产者类，KafkaProduce：</div>
<div><span style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;"></span><pre><code class="language-html">import java.io.File;
import java.io.FileInputStream;
import java.util.Properties;

import org.apache.kafka.clients.producer.Callback;
import org.apache.kafka.clients.producer.KafkaProducer;
import org.apache.kafka.clients.producer.ProducerRecord;
import org.apache.kafka.clients.producer.RecordMetadata;

import com.alibaba.fastjson.JSON;

public class KafkaProduce {
    private static Properties properties;

    static {
        properties = new Properties();
        String path = KafkaProduce.class.getResource("/").getFile().toString() + "kafka.properties";
        try {
            FileInputStream fis = new FileInputStream(new File(path));
            properties.load(fis);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    /**
     * 发送消息
     * 
     * @param topic
     * @param key
     * @param value
     */
    public void sendMsg(String topic, String key, String value) {

        System.err.println("properties:" + JSON.toJSONString(properties));
        // 实例化produce
        KafkaProducer&lt;String, String&gt; kp = new KafkaProducer&lt;String, String&gt;(properties);
        System.err.println("kp:" + JSON.toJSONString(kp));

        // 消息封装
        ProducerRecord&lt;String, String&gt; pr = new ProducerRecord&lt;String, String&gt;(topic, key, value);

        // 发送数据
        //        kp.send(pr);
        kp.send(pr, new Callback() {
            // 回调函数
            @Override
            public void onCompletion(RecordMetadata metadata, Exception exception) {
                if (null != exception) {
                    System.out.println("记录的offset在:" + metadata.offset());
                    System.out.println(exception.getMessage() + exception);
                }
            }
        });

        // 关闭produce
        kp.close();
    }
}</code></pre>kafka.properties 的目录放在 resources 目录下：</div>
<div><span style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;"></span><pre><code class="language-html"></code></pre><pre class="html">##produce
bootstrap.servers=10.20.135.32:9092
producer.type=sync
request.required.acks=1
serializer.class=kafka.serializer.DefaultEncoder
key.serializer=org.apache.kafka.common.serialization.StringSerializer
value.serializer=org.apache.kafka.common.serialization.StringSerializer
bak.partitioner.class=kafka.producer.DefaultPartitioner
bak.key.serializer=org.apache.kafka.common.serialization.StringSerializer
bak.value.serializer=org.apache.kafka.common.serialization.StringSerializer

##consume
zookeeper.connect=10.20.135.32:2181
group.id=test-consumer-group
zookeeper.session.timeout.ms=4000
zookeeper.sync.time.ms=200
auto.commit.interval.ms=1000
auto.offset.reset=smallest
serializer.class=kafka.serializer.StringEncoder</pre>
<pre></pre>
<pre></pre>
<pre></pre>
<pre></pre>
</div>
<div>
<div><br></div>
<div><br></div>
<div><br></div>
<div>group.id=test-consumer-group 我配置的为 kafka 的 config 目录下 consumer.properties 文件中的配置，但是貌似并不影响测试<br>
保证 topic 一致就行<br>
单元测试类：<br></div>
<div><br></div>
<div><pre><code class="language-java">import org.junit.Test;
import org.springframework.beans.factory.annotation.Autowired;

import com.hundsun.cloudtrade.match.dto.req.ClearDataReq;
import com.hundsun.cloudtrade.match.kafka.KafkaProduce;
import com.hundsun.cloudtrade.match.service.InitSystemService;
import com.hundsun.cloudtrade.test.BaseTest;

public class KafkaTest extends BaseTest {

    @Autowired
    private InitSystemService aInitSystemService;

    @Test
    public void kafkaTest() throws Exception {
        ClearDataReq domain = new ClearDataReq();
        domain.setFirm_account(0);
        new KafkaProduce().sendMsg("test", "key", "lsn-20171024");
    }

}</code></pre><br><br></div>
<div><br></div>
<div><br></div>
BaseTest 类：</div>
<div><span style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;"></span><pre><code class="language-html">import org.junit.runner.RunWith;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;

import com.alibaba.fastjson.JSON;

/**
 * 单元测试 .
 */
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations = { "classpath:conf/spring/*-beans.xml" })
public class BaseTest {

    public static Logger LOG = LoggerFactory.getLogger(BaseTest.class);

    public void log(Object obj, @SuppressWarnings("rawtypes") Class clazz) {
        if (null != clazz) {
            LOG = LoggerFactory.getLogger(clazz);
        }
        LOG.info(JSON.toJSONString(obj));
    }

    public void log(Object obj) {
        LOG.info(JSON.toJSONString(obj));
    }

}</code></pre>运行测试，测试结果如果报错：</div>
<div><span style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">Error connecting to node 0 at test.com:9092:</span></div>
<div><span style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">说明，不能解析 test.com <br>
kafka 连接原理<br>
首先连接 10.20.135.32:9092<br>
再连接返回的host.name = test.com,<br>
最后继续连接advertised.host.name=test.com<br>
解决办法<br>
添加window解析<br>
hosts 文件增加 <br>
10.20.135.32 test.com<br>
用cmd ping test.com 试试如果可以ping通即可。</span></div>
<div><span style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;"><br></span></div>
<div><span style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">再测试，然后在 linux 端，依照上面的启动 consumer ，便可以看到测试发的消息<br><br>
再编写 consumer 类：</span></div>
<div><span style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;"></span><pre><code class="language-html">import java.io.File;
import java.io.FileInputStream;
import java.util.HashMap;
import java.util.List;
import java.util.Map;
import java.util.Properties;

import kafka.consumer.ConsumerConfig;
import kafka.consumer.ConsumerIterator;
import kafka.consumer.KafkaStream;
import kafka.javaapi.consumer.ConsumerConnector;
import kafka.serializer.StringDecoder;
import kafka.utils.VerifiableProperties;

public class KafkaConsume {

    private final static String TOPIC = "test";

    private static Properties properties;

    static {
        properties = new Properties();
        String path = KafkaConsume.class.getResource("/").getFile().toString() + "kafka.properties";
        System.err.println("path:" + path);
        try {
            FileInputStream fis = new FileInputStream(new File(path));
            properties.load(fis);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    /**
     * 获取消息
     * 
     * @throws Exception
     */
    public static void getMsg() throws Exception {
        ConsumerConfig config = new ConsumerConfig(properties);

        ConsumerConnector consumer = kafka.consumer.Consumer.createJavaConsumerConnector(config);

        Map&lt;String, Integer&gt; topicCountMap = new HashMap&lt;String, Integer&gt;();

        topicCountMap.put(TOPIC, new Integer(1));

        StringDecoder keyDecoder = new StringDecoder(new VerifiableProperties());

        StringDecoder valueDecoder = new StringDecoder(new VerifiableProperties());

        Map&lt;String, List&lt;KafkaStream&lt;String, String&gt;&gt;&gt; consumerMap = consumer.createMessageStreams(topicCountMap, keyDecoder, valueDecoder);

        KafkaStream&lt;String, String&gt; stream = consumerMap.get(TOPIC).get(0);

        ConsumerIterator&lt;String, String&gt; it = stream.iterator();

        while (it.hasNext()) {
            String json = it.next().message();
            System.err.println(json);
        }
    }
}</code></pre>然后单元测试调用，跑起来，在 linux 端，启动 producer 输入一个 value 值，便可以看到在程序打印出来的 value 信息。</div>
<div><br></div>
<div><span style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;"><br><br></span></div>
<div><span style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;"><br></span></div>
<div><span style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;"><br></span></div>
<div><span style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;"><br></span></div>
<div><span style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;"><br><br></span></div>
<div><span style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;"><br></span></div>
<div><br><br><br></div>
<div><br></div>
<div><br><pre style="font-size:13px;background-color:rgb(255,255,255);"><span style="line-height:1.5;font-family:'微软雅黑';font-size:14px;">
</span></pre>
</div>
<div><br></div>
            </div>
                </div>