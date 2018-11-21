---
layout:     post
title:      Apache Kafka 编程实战-java客户端开发例子（入门教程轻松学）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：欢迎朋友转载，但请注明作者和原文链接。谢谢合作！					https://blog.csdn.net/liyiming2017/article/details/82986606				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>本入门教程，涵盖Kafka核心内容，通过实例和大量图表，帮助学习者理解，任何问题欢迎留言。</p>

<p>目录：</p>

<ul><li><a href="https://blog.csdn.net/liyiming2017/article/details/82790040" rel="nofollow">kafka简介</a></li>
	<li><a href="https://blog.csdn.net/liyiming2017/article/details/82790574" rel="nofollow">kafka安装和使用</a></li>
	<li><a href="https://blog.csdn.net/liyiming2017/article/details/82805479" rel="nofollow">kafka核心概念</a></li>
	<li><a href="https://blog.csdn.net/liyiming2017/article/details/82843036" rel="nofollow">kafka核心组件和流程--控制器</a></li>
	<li><a href="https://blog.csdn.net/liyiming2017/article/details/82867765" rel="nofollow">kafka核心组件和流程--协调器</a></li>
	<li><a href="https://blog.csdn.net/liyiming2017/article/details/82968550" rel="nofollow">kafka核心组件和流程--日志管理器</a></li>
	<li><a href="https://blog.csdn.net/liyiming2017/article/details/82984638" rel="nofollow">kafka核心组件和流程--副本管理器</a></li>
	<li><a href="https://blog.csdn.net/liyiming2017/article/details/82986606" rel="nofollow">kafka编程实战</a></li>
</ul><p>本章通过实际例子，讲解了如何使用java进行kafka开发。</p>

<p> </p>

<h1>准备</h1>

<p>添加依赖：</p>

<pre class="has">
<code>&lt;dependency&gt;
    &lt;groupId&gt;org.apache.kafka&lt;/groupId&gt;
    &lt;artifactId&gt;kafka-clients&lt;/artifactId&gt;
    &lt;version&gt;2.0.0&lt;/version&gt;
&lt;/dependency&gt;</code></pre>

<h1>创建主题</h1>

<p>下面是创建主题的代码：</p>

<pre class="has">
<code class="language-java">public class TopicProcessor {
    private static final String ZK_CONNECT="localhost:2181";
    private static final int SESSION_TIME_OUT=30000;
    private static final int CONNECT_OUT=30000;

    public static void createTopic(String topicName,int partitionNumber,int replicaNumber,Properties properties){
        ZkUtils zkUtils = null;
        try{
            zkUtils=ZkUtils.apply(ZK_CONNECT,SESSION_TIME_OUT,CONNECT_OUT, JaasUtils.isZkSecurityEnabled());
            if(!AdminUtils.topicExists(zkUtils,topicName)){
             AdminUtils.createTopic(zkUtils,topicName,partitionNumber,replicaNumber,properties,AdminUtils.createTopic$default$6());
            }
        }catch (Exception e){
            e.printStackTrace();
        }finally {
            zkUtils.close();
        }
    }

    public static void main(String[] args){
        createTopic("javatopic",1,1,new Properties());
    }
}</code></pre>

<p>首先定义了zookeeper相关连接信息。然后在createTopic中，先初始化ZkUtils，和zookeeper交互依赖于它。然后通过AdminUtils先判断是否存在你要创建的主题，如果不存在，则通过createTopic方法进行创建。传入参数包括主题名称，分区数量，副本数量等。</p>

<h1> </h1>

<h1>生产者生产消息</h1>

<p>生产者生产消息代码如下：</p>

<pre class="has">
<code class="language-java">public class MessageProducer {
    private static final String TOPIC="education-info";
    private static final String BROKER_LIST="localhost:9092";
    private static KafkaProducer&lt;String,String&gt; producer = null;

    static{
        Properties configs = initConfig();
        producer = new KafkaProducer&lt;String, String&gt;(configs);
    }

    private static Properties initConfig(){
        Properties properties = new Properties();
        properties.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG,BROKER_LIST);
        properties.put(ProducerConfig.ACKS_CONFIG,"all");
        properties.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, StringSerializer.class.getName());
        properties.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG,StringSerializer.class.getName());
        return properties;
    }

    public static void main(String[] args){
        try{
            String message = "hello world";
            ProducerRecord&lt;String,String&gt; record = new ProducerRecord&lt;String,String&gt;(TOPIC,message);
            producer.send(record, new Callback() {
                @Override
                public void onCompletion(RecordMetadata metadata, Exception exception) {
                    if(null==exception){
                        System.out.println("perfect!");
                    }
                    if(null!=metadata){
                        System.out.print("offset:"+metadata.offset()+";partition:"+metadata.partition());
                    }
                }
            }).get();
        }catch (Exception e){
            e.printStackTrace();
        }finally {
            producer.close();
        }
    }
}</code></pre>

<p>1、首先初始化KafkaProducer对象。</p>

<pre class="has">
<code class="language-java">producer = new KafkaProducer&lt;String, String&gt;(configs);</code></pre>

<p>2、创建要发送的消息对象。</p>

<pre class="has">
<code class="language-java">ProducerRecord&lt;String,String&gt; record = new ProducerRecord&lt;String,String&gt;(TOPIC,message);</code></pre>

<p>3、通过producer的send方法，发送消息</p>

<p>4、发送消息时，可以通过回调函数，取得消息发送的结果。异常发生时，对异常进行处理。</p>

<p>初始化producer时候,需要注意下面属性设置：</p>

<pre class="has">
<code class="language-java">properties.put(ProducerConfig.ACKS_CONFIG,"all");</code></pre>

<p>这里有三种值可供选择：</p>

<ul><li>0，不等服务器响应，直接返回发送成功。速度最快，但是丢了消息是无法知道的</li>
	<li>1，leader副本收到消息后返回成功</li>
	<li>all，所有参与的副本都复制完成后返回成功。这样最安全，但是延迟最高。</li>
</ul><h1> </h1>

<h1>消费者消费消息</h1>

<p>我们直接看代码</p>

<pre class="has">
<code class="language-java">public class MessageConsumer {

    private static final String TOPIC="education-info";
    private static final String BROKER_LIST="localhost:9092";
    private static KafkaConsumer&lt;String,String&gt; kafkaConsumer = null;

    static {
        Properties properties = initConfig();
        kafkaConsumer = new KafkaConsumer&lt;String, String&gt;(properties);
        kafkaConsumer.subscribe(Arrays.asList(TOPIC));
    }

    private static Properties initConfig(){
        Properties properties = new Properties();
        properties.put(ConsumerConfig.BOOTSTRAP_SERVERS_CONFIG,BROKER_LIST);
        properties.put(ConsumerConfig.GROUP_ID_CONFIG,"test");
        properties.put(ConsumerConfig.CLIENT_ID_CONFIG,"test");
        properties.put(ConsumerConfig.KEY_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class.getName());
        properties.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG,StringDeserializer.class.getName());
        return properties;
    }

    public static void main(String[] args){
        try{
            while(true){
                ConsumerRecords&lt;String,String&gt; records = kafkaConsumer.poll(100);
                for(ConsumerRecord record:records){
                    try{
                        System.out.println(record.value());
                    }catch(Exception e){
                        e.printStackTrace();
                    }
                }
            }

        }catch(Exception e){
            e.printStackTrace();
        }finally {
            kafkaConsumer.close();
        }
    }
}</code></pre>

<p>代码逻辑如下：</p>

<p>1、初始化消费者KafkaConsumer，并订阅主题。</p>

<pre class="has">
<code class="language-java">kafkaConsumer = new KafkaConsumer&lt;String, String&gt;(properties);
kafkaConsumer.subscribe(Arrays.asList(TOPIC));</code></pre>

<p>2、循环拉取消息</p>

<pre class="has">
<code class="language-java">ConsumerRecords&lt;String,String&gt; records = kafkaConsumer.poll(100);</code></pre>

<p>poll方法传入的参数100，是等待broker返回数据的时间，如果超过100ms没有响应，则不再等待。</p>

<p>3、拉取回消息后，循环处理。</p>

<pre class="has">
<code class="language-java">for(ConsumerRecord record:records){
     try{
            System.out.println(record.value());
        }catch(Exception e){
             e.printStackTrace();
        }
}</code></pre>

<p>消费相关代码比较简单，不过这个版本没有处理偏移量提交。学习过<a href="https://blog.csdn.net/liyiming2017/article/details/82867765" rel="nofollow">第四章-协调器</a>相关的同学应该还记得偏移量提交的问题。我曾说过最佳实践是同步和异步提交相结合，同时在特定的时间点，比如再均衡前进行手动提交。</p>

<p>加入偏移量提交，需要做如下修改：</p>

<p>1、enable.auto.commit设置为false</p>

<p>2、消费代码如下：</p>

<pre class="has">
<code class="language-java">public static void main(String[] args){
    try{
        while(true){
            ConsumerRecords&lt;String,String&gt; records =
                    kafkaConsumer.poll(100);
            for(ConsumerRecord record:records){
                try{
                    System.out.println(record.value());
                }catch(Exception e){
                    e.printStackTrace();
                }
            }
            kafkaConsumer.commitAsync();
        }

    }catch(Exception e){
        e.printStackTrace();
    }finally {
        try{
            kafkaConsumer.commitSync();
        }finally {
            kafkaConsumer.close();
        }
    }
}</code></pre>

<p>3、订阅消息时，实现再均衡的回调方法，在此方法中手动提交偏移量</p>

<pre class="has">
<code class="language-java">kafkaConsumer.subscribe(Arrays.asList(TOPIC), new ConsumerRebalanceListener() {
            @Override
            public void onPartitionsRevoked(Collection&lt;TopicPartition&gt; partitions) {
                //再均衡之前和消费者停止读取消息之后调用
                kafkaConsumer.commitSync(currentOffsets);
            }
   });</code></pre>

<p>通过以上三步，我们把自动提交偏移量改为了手动提交。正常消费时，异步提交kafkaConsumer.commitAsync()。即使偶尔失败，也会被后续成功的提交覆盖掉。而在发生异常的时候，手动提交 kafkaConsumer.commitSync()。此外在步骤3中，我们通过实现再均衡时的回调方法，手动同步提交偏移量，确保了再均衡前偏移量提交成功。</p>

<p>以上面的最佳实践提交偏移量，既能保证消费时较高的效率，又能够尽量避免重复消费。不过由于重复消费无法100%避免，消费逻辑需要自己处理重复消费的判断。</p>

<p><strong><em>至此，本系列kafka轻松学教程也就完结了。教程涵盖了Kafka大部分的内容，但没有涉及到流相关的内容。Kafka实现的部分细节也没有做过多的讲解。学习完本教程，如果你能够对kafka的原理有较为深刻的理解，并且能够上手开发程序，目的就已经达到了。如果想继续探寻Kafka工作的细节，可以再看更为深入的资料。相信通过此教程打好基础，再深入学习kafka也会更为容易！</em></strong></p>            </div>
                </div>