---
layout:     post
title:      SpringBoot框架集成开发
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="springboot-集成消息中间件kafka篇">SpringBoot 集成消息中间件(kafka篇)</h1>

<p>本文为使用SpringBoot集成kafka，其中包括 [ SpringBoot 的搭建，kafka + zookeeper 安装、使用，以及最后如何使用SpringBoot来集成kafka]：</p>

<ul>
<li>如何搭建SpringBoot项目以及环境</li>
<li>下载kafka、zookeeper在服务器进行安装</li>
<li>如何使用SpringBoot进行集成kafka，进行消息通讯</li>
</ul>

<hr>



<h3 id="下载idea编程工具">下载IDEA编程工具</h3>

<pre><code>1.IDEA旗舰版分享（百度网盘分享）

    地址：http://pan.baidu.com/s/1sl38o5f

说明：其中包含IDEA安装文件，注册码破解.txt，本地licenseServer搭建


2.如何使用licenseServer进行注册破解

    a.  ![解压该文件](&lt;img src="https://img-blog.csdn.net/20170718204626768?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMzQyMTkxMjg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast"&gt;)

    b.根据自身电脑位数选择使用的文件32 / 64

    c.运行破解文件后，双击打开已经安装好的IDEA，选择licenseServer

        在license Server 中输入 127.0.0.7:1071

    d.这个时候就已经完成破解了；（部分人）

    e.有部分人电脑可能每次都要打开破解文件才能破解（可使用文件启动程序）

        **注意：（下面 // 注释在使用时请删除）**
        echo start   //开始
        echo IDEA_LINCENSE START  //开始启动破解文件：搭建本地服务
        //进行启动：指向破解文件
        start /d "E:\IDEA\activate" IntelliJIDEALicenseServer_windows_amd64.exe

        echo IDEA START  //开始启动IDEA
        //进行启动：指向IDEA的启动文件
        start /d "E:\IDEA\IntelliJ IDEA 2016.3.1\bin" idea64.exe

        echo IDEA_LICENSE打开完成！  
        //将本地服务启动延迟4秒（因为有可能IDEA启动没那么快）
        ping 127.0.0.1 -n 4 
        //关闭破解文件，进行关闭服务
        taskkill /f /im IntelliJIDEALicenseServer_windows_amd64.exe

        echo 延时4秒关闭！ 

        goto openie 

        exit   //退出
</code></pre>

<h3 id="搭建springboot项目">搭建SpringBoot项目</h3>

<pre><code>1.打开已经安装好的IDEA(其中需要JDK，先要安装JDK)
![这里写图片描述](https://img-blog.csdn.net/20170727134938606?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMzQyMTkxMjg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

2.选择下一步（进行填写项目内容）
![这里写图片描述](https://img-blog.csdn.net/20170727135347772?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMzQyMTkxMjg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

3.选择要添加的依赖
![这里写图片描述](https://img-blog.csdn.net/20170727135813139?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMzQyMTkxMjg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

注意：到这里简单的 SpringBoot项目就已经搭建完毕；
</code></pre>



<h3 id="下载kafkazookeeper">下载kafka、zookeeper</h3>

<pre><code>1.下载kafka
    下载地址：http://kafka.apache.org/downloads

2.下载zookeeper
    下载地址：http://apache.fayea.com/zookeeper/
</code></pre>



<h3 id="在服务器安装kafkazookeeper">在服务器安装kafka、zookeeper</h3>

<ol>
<li><p>安装kafka、zookeeper <br>
 a.解压kafka安装包 ：tar -zxvf kafka_2.11-0.11.0.0.tgz</p>

<p>b.进入解压的kafka目录：cd kafka_2.11-0.11.0.0</p>

<p>c.安装zookeeper：<a href="http://blog.csdn.net/lk10207160511/article/details/50526404" rel="nofollow" target="_blank">http://blog.csdn.net/lk10207160511/article/details/50526404</a></p>

<p>d.启动zookeeper：bin/zookeeper-server-start.sh config/zookeeper.properties</p>

<p>e.启动kafka：bin/kafka-server-start.sh config/server.properties</p>

<p>f.后台运行：nohup bin/kafka-server-start.sh config/server.properties  &amp;</p>

<p>注意：e选项中启动非后台启动，只要关闭窗口后kafka也关闭了，f选项为后台运行，关了窗口也会运行；</p></li>
</ol>



<h3 id="使用springboot集成kafka">使用SpringBoot集成kafka</h3>

<pre><code>1.添加SpringBoot跟kafka的依赖

    &lt;dependency&gt;
        &lt;groupId&gt;org.springframework.kafka&lt;/groupId&gt;
        &lt;artifactId&gt;spring-kafka&lt;/artifactId&gt;
        &lt;version&gt;1.1.1.RELEASE&lt;/version&gt;
    &lt;/dependency&gt;

2.编写消费者操作类

    import org.apache.kafka.clients.consumer.ConsumerConfig;
    import org.apache.kafka.common.serialization.ByteArrayDeserializer;
    import org.apache.kafka.common.serialization.BytesDeserializer;
    import org.apache.kafka.common.serialization.StringDeserializer;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.context.annotation.Bean;
    import org.springframework.context.annotation.Configuration;
    import org.springframework.kafka.annotation.EnableKafka;
    import org.springframework.kafka.config.ConcurrentKafkaListenerContainerFactory;
    import org.springframework.kafka.config.KafkaListenerContainerFactory;
    import org.springframework.kafka.core.ConsumerFactory;
    import org.springframework.kafka.core.DefaultKafkaConsumerFactory;
    import org.springframework.kafka.listener.ConcurrentMessageListenerContainer;
    import org.springframework.kafka.listener.KafkaMessageListenerContainer;
    import org.springframework.kafka.listener.config.ContainerProperties;

    import java.util.HashMap;
    import java.util.Map;

    @Configuration
    @EnableKafka
    public class KafkaConsumerConfig {

        /**
         * 监听工厂配置
         * @return
         */
        @Bean
        public KafkaListenerContainerFactory&lt;ConcurrentMessageListenerContainer&lt;String, String&gt;&gt; kafkaListenerContainerFactory() {
            ConcurrentKafkaListenerContainerFactory&lt;String, String&gt; factory = new ConcurrentKafkaListenerContainerFactory&lt;&gt;();
            factory.setConsumerFactory(consumerFactory());
            factory.setConcurrency(3);
            factory.getContainerProperties().setPollTimeout(3000);
            return factory;
        }

        /**
         * 及客户工厂
         * @return
         */
        public ConsumerFactory&lt;String, String&gt; consumerFactory() {
            return new DefaultKafkaConsumerFactory&lt;&gt;(consumerConfigs());
        }


        /**
         * 客户配置
         * @return
         */
        public Map&lt;String, Object&gt; consumerConfigs() {
            Map&lt;String, Object&gt; propsMap = new HashMap&lt;&gt;();
            propsMap.put(ConsumerConfig.BOOTSTRAP_SERVERS_CONFIG, "192.168.6.181:9092");   //消息对列地址：端口
            propsMap.put(ConsumerConfig.ENABLE_AUTO_COMMIT_CONFIG, false);
            propsMap.put(ConsumerConfig.AUTO_COMMIT_INTERVAL_MS_CONFIG, "100");
            propsMap.put(ConsumerConfig.SESSION_TIMEOUT_MS_CONFIG, "15000");   // 超时时间
            propsMap.put(ConsumerConfig.KEY_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class);  //key序列化
            propsMap.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, ByteArrayDeserializer.class);  //value 序列化
            propsMap.put(ConsumerConfig.GROUP_ID_CONFIG, "test-platform");   //用户组
            propsMap.put(ConsumerConfig.AUTO_OFFSET_RESET_CONFIG, "latest");
            return propsMap;
        }


        @Bean
        public Listener listener() {
            return new Listener();
        }

3.编写消费者监听类

    import com.hqweb.base.socket.MyWebSocket;
    import com.hqweb.base.util.LoggerDB;
    import net.sf.json.JSONObject;
    import org.apache.log4j.Logger;
    import org.springframework.kafka.annotation.KafkaListener;

    import javax.annotation.Resource;
    import java.io.IOException;

    /**
     * Created by HayLeung on 2017/4/20.
     * 话题监听器
     */
    public class Listener {


        //日志对象
        private Logger logger = Logger.getLogger(Listener.class);

        /**
         * 使用注解监听话题，多个用逗号隔开
         * @param message ：这里使用的byte[]
         */
        @KafkaListener(topics = {"tp_paa","tp_dm","tp_info","tp_phone","tp_vc","tp_vm"})
        public void receiveVehicleInfo(byte[] message) throws IOException {

            //这里编写监听消息后的处理  

        }
    }

4.编写生产者操作类

    import java.util.HashMap;
    import java.util.Map;

    import org.apache.kafka.clients.producer.ProducerConfig;
    import org.apache.kafka.common.serialization.ByteArrayDeserializer;
    import org.apache.kafka.common.serialization.StringDeserializer;
    import org.springframework.context.annotation.Bean;
    import org.springframework.context.annotation.Configuration;
    import org.springframework.kafka.annotation.EnableKafka;
    import org.springframework.kafka.core.DefaultKafkaProducerFactory;
    import org.springframework.kafka.core.KafkaTemplate;
    import org.springframework.kafka.core.ProducerFactory;
    import org.springframework.stereotype.Component;


    @Configuration
    @EnableKafka
    @Component
    public class KafkaProducerConfig {


        public static Map&lt;String, Object&gt; producerConfigs() {
            Map&lt;String, Object&gt; props = new HashMap&lt;&gt;();
            props.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, "192.168.6.181:9092");
            props.put(ProducerConfig.RETRIES_CONFIG, 0);
            props.put(ProducerConfig.BATCH_SIZE_CONFIG, 4096);
            props.put(ProducerConfig.LINGER_MS_CONFIG, 1);
            props.put(ProducerConfig.BUFFER_MEMORY_CONFIG, 40960);
            props.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, org.apache.kafka.common.serialization.StringSerializer.class);
            props.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, org.apache.kafka.common.serialization.ByteArraySerializer.class);
            return props;
        }

        public static ProducerFactory&lt;String,byte[]&gt; producerFactory() {
            return new DefaultKafkaProducerFactory&lt;String,byte[]&gt;(producerConfigs());
        }

        @Bean
        public static KafkaTemplate&lt;String,byte[]&gt; kafkaTemplate() {
            return new KafkaTemplate&lt;String,byte[]&gt;(producerFactory());
        }

}
</code></pre>

<p>到这里就已经配置完了，可以使用集成的处理类进行连接kafka，自行而是消费、生产话题消息；</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>