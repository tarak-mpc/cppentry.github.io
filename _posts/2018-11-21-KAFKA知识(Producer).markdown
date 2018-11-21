---
layout:     post
title:      KAFKA知识(Producer)
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/yl3395017/article/details/76066633				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>构建KAFKA Producer Brokerlist为kafka集群broker地址 可修改为“10.2.1.123,10.2.1.132”格式</p>
<p></p>
<pre><code class="language-java">static {
        Properties props = new Properties();
        // 此处配置的是kafka的端口
        props.put("metadata.broker.list",BROKERlIST);
        // 配置value的序列化类
        props.put("serializer.class", "kafka.serializer.StringEncoder");
        // 配置key的序列化类
        props.put("key.serializer.class", "kafka.serializer.StringEncoder");
        props.put("request.required.acks", "-1");
        
        producer = new Producer&lt;String, String&gt;(new ProducerConfig(props));
        ｝</code></pre><br>
producer
<p></p>
<p></p>
<pre><code class="language-java">private static Producer&lt;String, String&gt; producer;</code></pre><br>
发送消息体 topic为主题  conent为发送内容
<p></p>
<p></p>
<pre><code class="language-java">   producer.send(new KeyedMessage&lt;String, String&gt;(TOPIC, CONTENT));   </code></pre><br><br><p>整体源码：</p>
<p></p>
<pre><code class="language-java">import java.util.Properties;
import kafka.javaapi.producer.Producer;
import kafka.producer.KeyedMessage;
import kafka.producer.ProducerConfig;

public class KafkaProducer {
    
    // 生产者
    private static Producer&lt;String, String&gt; producer;
    
    static {  
        Properties props = new Properties();  
        // 此处配置的是kafka的端口  
        props.put("metadata.broker.list","10.2.4.13:9092,10.2.4.14:9092,10.2.4.12:9092");  
        // 配置value的序列化类  
        props.put("serializer.class", "kafka.serializer.StringEncoder");  
        // 配置key的序列化类  
        props.put("key.serializer.class", "kafka.serializer.StringEncoder");  
        props.put("request.required.acks", "-1");  
          
        producer = new Producer&lt;String, String&gt;(new ProducerConfig(props));
    }
    
    public static void main(String[] args) {
        
        for(int i = 0 ;i&lt;100;i++){
            producer.send(new KeyedMessage&lt;String, String&gt;("test_rce_yjd", "{json:fjdlasfjldsajfoiewjf}"));
            try {
                Thread.sleep(3000);
            } catch (InterruptedException e) {
                // TODO Auto-generated catch block
                e.printStackTrace();
            }
        }
        
    }

}
</code></pre><br><br><p></p>
            </div>
                </div>