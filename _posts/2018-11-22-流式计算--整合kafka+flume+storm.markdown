---
layout:     post
title:      流式计算--整合kafka+flume+storm
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>本篇博客基于之前搭建的<a href="https://blog.csdn.net/weixin_37650458/article/details/84189844" rel="nofollow">kafka集群</a>和<a href="https://blog.csdn.net/weixin_37650458/article/details/84112280" rel="nofollow">storm集群</a></p>

<h1>1.数据流向</h1>

<p>     日志系统=&gt;flume=&gt;kafka=&gt;storm</p>

<h1> 2.安装flume</h1>

<p>      1.我们在storm01上安装flume1.6.0,上传安装包 <img alt="" class="has" height="114" src="https://img-blog.csdnimg.cn/2018111816080731.png" width="1046"></p>

<p>      2.解压到  /export/servers/flume，首先创建文件夹flume</p>

<p style="margin-left:0cm;">         命令：  sudo tar -zxvf apache-flume-1.6.0-bin.tar.gz  -C /export/servers/flume/</p>

<p style="margin-left:0cm;">                <img alt="" class="has" height="311" src="https://img-blog.csdnimg.cn/20181118160931563.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNzY1MDQ1OA==,size_16,color_FFFFFF,t_70" width="582"></p>

<p style="margin-left:0cm;">      3.配置采集文件，在conf目录下创建一个myconf文件夹</p>

<p style="margin-left:0cm;">              <img alt="" class="has" height="194" src="https://img-blog.csdnimg.cn/20181118161019884.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNzY1MDQ1OA==,size_16,color_FFFFFF,t_70" width="600"></p>

<p style="margin-left:0cm;">       4.继续在myconf文件夹下创建配置文件</p>

<p style="margin-left:0cm;">              <img alt="" class="has" height="97" src="https://img-blog.csdnimg.cn/20181118161104284.png" width="466"></p>

<p style="margin-left:0cm;">          配置文件的内容：主要是监听日志文件</p>

<pre class="has">
<code>agent.sinks = k1
agent.sources = s1
agent.sources = s1                         
agent.channels = c1
agent.sinks = k1      
agent.sources.s1.type=exec        
agent.sources.s1.command=tail -F /export/data/flume_source/click_log/1.log
agent.sources.s1.channels=c1
agent.channels.c1.type=memory
agent.channels.c1.capacity=10000
agent.channels.c1.transactionCapacity=100
#设置Kafka接收器
agent.sinks.k1.type= org.apache.flume.sink.kafka.KafkaSink
#设置Kafka的broker地址和端口
agent.sinks.k1.brokerList=kafka01:9092
#设置Kafka的Topic
agent.sinks.k1.topic=orderMq
#设置序列化方式
agent.sinks.k1.serializer.class=kafka.serializer.StringEncoder
agent.sinks.k1.channel=c1
</code></pre>

<p style="margin-left:0cm;">   准备监听的目录：<br>
       <span style="color:#ff0000;">/export/data/flume_source/click_log</span></p>

<p style="margin-left:0cm;"><span style="color:#ff0000;">  5.</span>启动命令：</p>

<pre class="has">
<code>bin/flume-ng agent -n agent  -c ./conf -f ./conf/myconf/exec.conf -Dflume.root.logger=INFO,console</code></pre>

<p>     <img alt="" class="has" height="195" src="https://img-blog.csdnimg.cn/20181118161408207.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNzY1MDQ1OA==,size_16,color_FFFFFF,t_70" width="462"></p>

<p>     <img alt="" class="has" height="106" src="https://img-blog.csdnimg.cn/2018111816142873.png" width="433"></p>

<p>   6. 测试数据从flume到kafka是否正确：</p>

<p style="margin-left:0cm;">    Kafka的shell消费：<br>
    bin/kafka-console-consumer.sh --zookeeper zk01:2181 <span style="color:#ff0000;">--from-beginning</span> --topic orderMq 不从开始消费而是从最大开始消费：</p>

<p style="margin-left:0cm;"><img alt="" class="has" height="72" src="https://img-blog.csdnimg.cn/20181118161612753.png" width="1002"></p>

<p style="margin-left:0cm;">   编写模拟生产日志数据的脚本文件：</p>

<p style="margin-left:0cm;">   vi  click_log_out.sh</p>

<pre class="has">
<code>for((i=0;i&lt;50000;i++));
do echo "message-"+$i &gt;&gt;/export/data/flume_source/click_log/1.log;
done
</code></pre>

<p>   Sudo chmod u+x  click_log_out.sh</p>

<p>   执行脚本：sh click_log_out.sh  另外一边的customer就在开始采集数据了:</p>

<p>           <img alt="" class="has" height="338" src="https://img-blog.csdnimg.cn/20181118161846600.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNzY1MDQ1OA==,size_16,color_FFFFFF,t_70" width="496"></p>

<p>  说明数据从flume到kafka是没有问题的</p>

<h1> 3.数据从kafka到Storm</h1>

<p>     新建maven工程：添加依赖</p>

<pre class="has">
<code>&lt;?xml version="1.0" encoding="UTF-8"?&gt;
&lt;project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd"&gt;
    &lt;modelVersion&gt;4.0.0&lt;/modelVersion&gt;

    &lt;groupId&gt;com.wx&lt;/groupId&gt;
    &lt;artifactId&gt;stormkafka&lt;/artifactId&gt;
    &lt;version&gt;1.0-SNAPSHOT&lt;/version&gt;
    &lt;dependencies&gt;
        &lt;!--storm的以来包--&gt;
        &lt;dependency&gt;
            &lt;groupId&gt;org.apache.storm&lt;/groupId&gt;
            &lt;artifactId&gt;storm-core&lt;/artifactId&gt;
            &lt;version&gt;0.9.5&lt;/version&gt;
            &lt;!--&lt;scope&gt;provided&lt;/scope&gt;--&gt;
        &lt;/dependency&gt;
        &lt;!--KafkaSpout的依赖包，这个就可以把kafka的数据流到storm--&gt;
        &lt;dependency&gt;
            &lt;groupId&gt;org.apache.storm&lt;/groupId&gt;
            &lt;artifactId&gt;storm-kafka&lt;/artifactId&gt;
            &lt;version&gt;0.9.5&lt;/version&gt;
           &lt;!-- &lt;exclusions&gt;
                &lt;exclusion&gt;
                    &lt;groupId&gt;org.slf4j&lt;/groupId&gt;
                    &lt;artifactId&gt;slf4j-log4j12&lt;/artifactId&gt;
                &lt;/exclusion&gt;
                &lt;exclusion&gt;
                    &lt;groupId&gt;org.slf4j&lt;/groupId&gt;
                    &lt;artifactId&gt;slf4j-api&lt;/artifactId&gt;
                &lt;/exclusion&gt;
            &lt;/exclusions&gt;--&gt;
        &lt;/dependency&gt;

        &lt;dependency&gt;
            &lt;groupId&gt;org.clojure&lt;/groupId&gt;
            &lt;artifactId&gt;clojure&lt;/artifactId&gt;
            &lt;version&gt;1.5.1&lt;/version&gt;
        &lt;/dependency&gt;
        &lt;!--kafka的依赖包--&gt;
        &lt;dependency&gt;
            &lt;groupId&gt;org.apache.kafka&lt;/groupId&gt;
            &lt;artifactId&gt;kafka_2.8.2&lt;/artifactId&gt;
            &lt;version&gt;0.8.1&lt;/version&gt;
            &lt;exclusions&gt;
                &lt;exclusion&gt;
                    &lt;artifactId&gt;jmxtools&lt;/artifactId&gt;
                    &lt;groupId&gt;com.sun.jdmk&lt;/groupId&gt;
                &lt;/exclusion&gt;
                &lt;exclusion&gt;
                    &lt;artifactId&gt;jmxri&lt;/artifactId&gt;
                    &lt;groupId&gt;com.sun.jmx&lt;/groupId&gt;
                &lt;/exclusion&gt;
                &lt;exclusion&gt;
                    &lt;artifactId&gt;jms&lt;/artifactId&gt;
                    &lt;groupId&gt;javax.jms&lt;/groupId&gt;
                &lt;/exclusion&gt;
                &lt;exclusion&gt;
                    &lt;groupId&gt;org.apache.zookeeper&lt;/groupId&gt;
                    &lt;artifactId&gt;zookeeper&lt;/artifactId&gt;
                &lt;/exclusion&gt;
                &lt;exclusion&gt;
                    &lt;groupId&gt;org.slf4j&lt;/groupId&gt;
                    &lt;artifactId&gt;slf4j-log4j12&lt;/artifactId&gt;
                &lt;/exclusion&gt;
                &lt;exclusion&gt;
                    &lt;groupId&gt;org.slf4j&lt;/groupId&gt;
                    &lt;artifactId&gt;slf4j-api&lt;/artifactId&gt;
                &lt;/exclusion&gt;
            &lt;/exclusions&gt;
        &lt;/dependency&gt;
       &lt;!-- &lt;dependency&gt;
            &lt;groupId&gt;com.google.code.gson&lt;/groupId&gt;
            &lt;artifactId&gt;gson&lt;/artifactId&gt;
            &lt;version&gt;2.4&lt;/version&gt;
        &lt;/dependency&gt;
        &lt;dependency&gt;
            &lt;groupId&gt;redis.clients&lt;/groupId&gt;
            &lt;artifactId&gt;jedis&lt;/artifactId&gt;
            &lt;version&gt;2.7.3&lt;/version&gt;
        &lt;/dependency&gt;--&gt;
    &lt;/dependencies&gt;

    &lt;build&gt;
        &lt;plugins&gt;
            &lt;plugin&gt;
                &lt;!--打包的时候把项目其他依赖的一些jar,一些类打成一个整体--&gt;
                &lt;artifactId&gt;maven-assembly-plugin&lt;/artifactId&gt;
                &lt;configuration&gt;
                    &lt;descriptorRefs&gt;
                        &lt;descriptorRef&gt;jar-with-dependencies&lt;/descriptorRef&gt;
                    &lt;/descriptorRefs&gt;
                    &lt;archive&gt;
                        &lt;manifest&gt;
                            &lt;mainClass&gt;cn.itcast.bigdata.hadoop.mapreduce.wordcount.WordCount&lt;/mainClass&gt;
                        &lt;/manifest&gt;
                    &lt;/archive&gt;
                &lt;/configuration&gt;
                &lt;executions&gt;
                    &lt;execution&gt;
                        &lt;id&gt;make-assembly&lt;/id&gt;
                        &lt;phase&gt;package&lt;/phase&gt;
                        &lt;goals&gt;
                            &lt;goal&gt;single&lt;/goal&gt;
                        &lt;/goals&gt;
                    &lt;/execution&gt;
                &lt;/executions&gt;
            &lt;/plugin&gt;
            &lt;plugin&gt;
                &lt;groupId&gt;org.apache.maven.plugins&lt;/groupId&gt;
                &lt;artifactId&gt;maven-compiler-plugin&lt;/artifactId&gt;
                &lt;configuration&gt;
                    &lt;source&gt;1.7&lt;/source&gt;
                    &lt;target&gt;1.7&lt;/target&gt;
                &lt;/configuration&gt;
            &lt;/plugin&gt;
        &lt;/plugins&gt;
    &lt;/build&gt;


&lt;/project&gt;</code></pre>

<p>     编写一个topology:</p>

<pre class="has">
<code class="language-java">package com.wx.kafkaandstorm;
import backtype.storm.Config;
import backtype.storm.LocalCluster;
import backtype.storm.StormSubmitter;
import backtype.storm.topology.TopologyBuilder;
import storm.kafka.KafkaSpout;
import storm.kafka.SpoutConfig;
import storm.kafka.ZkHosts;

public class KafkaAndStormTopologyMain {
    public static void main(String[] args) throws Exception{
        TopologyBuilder topologyBuilder = new TopologyBuilder();
        topologyBuilder.setSpout("kafkaSpout",
                new KafkaSpout(new SpoutConfig(
                        new ZkHosts("192.168.25.130:2181,192.168.25.131:2181,192.168.25.132:2181"),
                        "orderMq",
                        "/myKafka",
                        "kafkaSpout")),1);
        topologyBuilder.setBolt("mybolt1",new ParserOrderMqBolt(),1).shuffleGrouping("kafkaSpout");

        Config config = new Config();
        config.setNumWorkers(1);

        //3、提交任务  -----两种模式 本地模式和集群模式
        if (args.length&gt;0) {
            StormSubmitter.submitTopology(args[0], config, topologyBuilder.createTopology());
        }else {
            LocalCluster localCluster = new LocalCluster();
            localCluster.submitTopology("storm2kafka", config, topologyBuilder.createTopology());
        }

    }
}</code></pre>

<p>  编写一个Bolt接收来自kafka的数据</p>

<p> </p>

<pre class="has">
<code class="language-java">package com.wx.kafkaandstorm;

import backtype.storm.task.OutputCollector;
import backtype.storm.task.TopologyContext;
import backtype.storm.topology.OutputFieldsDeclarer;
import backtype.storm.topology.base.BaseRichBolt;
import backtype.storm.tuple.Tuple;

import java.util.Map;

public class ParserOrderMqBolt extends BaseRichBolt {
    @Override
    public void prepare(Map map, TopologyContext topologyContext, OutputCollector outputCollector) {

    }
    @Override
    public void execute(Tuple tuple) {
      Object o=tuple.getValue(0);
      System.out.printf(new String((byte[]) tuple.getValue(0))+"\n\t");
    }

    @Override
    public void declareOutputFields(OutputFieldsDeclarer outputFieldsDeclarer) {

    }
}
</code></pre>

<p>启动：<br><img alt="" class="has" height="402" src="https://img-blog.csdnimg.cn/20181118162520550.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNzY1MDQ1OA==,size_16,color_FFFFFF,t_70" width="784"></p>

<h1>4.联合测试：</h1>

<p>   启动日志生产脚本：</p>

<p>  <img alt="" class="has" height="150" src="https://img-blog.csdnimg.cn/20181118162623579.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNzY1MDQ1OA==,size_16,color_FFFFFF,t_70" width="622"></p>

<p> kafka可以消费到数据：<br><img alt="" class="has" height="224" src="https://img-blog.csdnimg.cn/20181118162710897.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNzY1MDQ1OA==,size_16,color_FFFFFF,t_70" width="558"></p>

<p>数据也可以从kafka流到storm:</p>

<p><img alt="" class="has" height="272" src="https://img-blog.csdnimg.cn/20181118162752628.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNzY1MDQ1OA==,size_16,color_FFFFFF,t_70" width="293"></p>

<p>本地运行没有问题，但是把他打包上传到集群上去跑就出现问题了，报了个错：<a href="https://www.cnblogs.com/biehongli/p/8316885.html" rel="nofollow" id="cb_post_title_url">backtype.storm.topology.IRichSpout</a></p>

<p>这个错表示要么少包要么包冲突，本地能运行成功，应该不可能少包，应该是包冲突，到底是哪个包冲突了呢？集群的storm版本是1.0.6而本地运行的storm jar的版本是0.9.5，所以果断把本地的storm包换成1.0.6版本的，但是还是运行不成功，原因是clojure包的版本太低，storm1.0以上的需要clojure包的版本是1.7，所以改变包的版本，本地测试能通过，集群测试也能通过</p>

<p>贴出最终pom文件：</p>

<pre class="has">
<code class="language-java">&lt;?xml version="1.0" encoding="UTF-8"?&gt;
&lt;project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd"&gt;
    &lt;modelVersion&gt;4.0.0&lt;/modelVersion&gt;

    &lt;groupId&gt;com.wx&lt;/groupId&gt;
    &lt;artifactId&gt;stormkafka&lt;/artifactId&gt;
    &lt;version&gt;1.0-SNAPSHOT&lt;/version&gt;
    &lt;dependencies&gt;
              &lt;!--storm的以来包--&gt;
        &lt;dependency&gt;
            &lt;groupId&gt;org.apache.storm&lt;/groupId&gt;
            &lt;artifactId&gt;storm-core&lt;/artifactId&gt;
            &lt;version&gt;1.0.6&lt;/version&gt;
            &lt;scope&gt;provided&lt;/scope&gt;
        &lt;/dependency&gt;
        &lt;!--KafkaSpout的依赖包，这个就可以把kafka的数据流到storm--&gt;
        &lt;dependency&gt;
            &lt;groupId&gt;org.apache.storm&lt;/groupId&gt;
            &lt;artifactId&gt;storm-kafka&lt;/artifactId&gt;
            &lt;version&gt;1.0.6&lt;/version&gt;
           &lt;!-- &lt;exclusions&gt;
                &lt;exclusion&gt;
                    &lt;groupId&gt;org.slf4j&lt;/groupId&gt;
                    &lt;artifactId&gt;slf4j-log4j12&lt;/artifactId&gt;
                &lt;/exclusion&gt;
                &lt;exclusion&gt;
                    &lt;groupId&gt;org.slf4j&lt;/groupId&gt;
                    &lt;artifactId&gt;slf4j-api&lt;/artifactId&gt;
                &lt;/exclusion&gt;
            &lt;/exclusions&gt;--&gt;
        &lt;/dependency&gt;

        &lt;dependency&gt;
            &lt;groupId&gt;org.clojure&lt;/groupId&gt;
            &lt;artifactId&gt;clojure&lt;/artifactId&gt;
            &lt;version&gt;1.7.0&lt;/version&gt;
        &lt;/dependency&gt;
        &lt;!--kafka的依赖包--&gt;
        &lt;dependency&gt;
            &lt;groupId&gt;org.apache.kafka&lt;/groupId&gt;
            &lt;artifactId&gt;kafka_2.8.2&lt;/artifactId&gt;
            &lt;version&gt;0.8.1&lt;/version&gt;
            &lt;exclusions&gt;
                &lt;exclusion&gt;
                    &lt;artifactId&gt;jmxtools&lt;/artifactId&gt;
                    &lt;groupId&gt;com.sun.jdmk&lt;/groupId&gt;
                &lt;/exclusion&gt;
                &lt;exclusion&gt;
                    &lt;artifactId&gt;jmxri&lt;/artifactId&gt;
                    &lt;groupId&gt;com.sun.jmx&lt;/groupId&gt;
                &lt;/exclusion&gt;
                &lt;exclusion&gt;
                    &lt;artifactId&gt;jms&lt;/artifactId&gt;
                    &lt;groupId&gt;javax.jms&lt;/groupId&gt;
                &lt;/exclusion&gt;
                &lt;exclusion&gt;
                    &lt;groupId&gt;org.apache.zookeeper&lt;/groupId&gt;
                    &lt;artifactId&gt;zookeeper&lt;/artifactId&gt;
                &lt;/exclusion&gt;
                &lt;exclusion&gt;
                    &lt;groupId&gt;org.slf4j&lt;/groupId&gt;
                    &lt;artifactId&gt;slf4j-log4j12&lt;/artifactId&gt;
                &lt;/exclusion&gt;
                &lt;exclusion&gt;
                    &lt;groupId&gt;org.slf4j&lt;/groupId&gt;
                    &lt;artifactId&gt;slf4j-api&lt;/artifactId&gt;
                &lt;/exclusion&gt;
            &lt;/exclusions&gt;
        &lt;/dependency&gt;
       &lt;!-- &lt;dependency&gt;
            &lt;groupId&gt;com.google.code.gson&lt;/groupId&gt;
            &lt;artifactId&gt;gson&lt;/artifactId&gt;
            &lt;version&gt;2.4&lt;/version&gt;
        &lt;/dependency&gt;
        &lt;dependency&gt;
            &lt;groupId&gt;redis.clients&lt;/groupId&gt;
            &lt;artifactId&gt;jedis&lt;/artifactId&gt;
            &lt;version&gt;2.7.3&lt;/version&gt;
        &lt;/dependency&gt;--&gt;
    &lt;/dependencies&gt;

    &lt;build&gt;
        &lt;plugins&gt;
            &lt;plugin&gt;
                &lt;!--打包的时候把项目其他依赖的一些jar,一些类打成一个整体--&gt;
                &lt;artifactId&gt;maven-assembly-plugin&lt;/artifactId&gt;
                &lt;configuration&gt;
                    &lt;descriptorRefs&gt;
                        &lt;descriptorRef&gt;jar-with-dependencies&lt;/descriptorRef&gt;
                    &lt;/descriptorRefs&gt;
                    &lt;archive&gt;
                        &lt;manifest&gt;
                            &lt;mainClass&gt;com.wx.kafkaandstorm.KafkaAndStormTopologyMain&lt;/mainClass&gt;
                        &lt;/manifest&gt;
                    &lt;/archive&gt;
                &lt;/configuration&gt;
                &lt;executions&gt;
                    &lt;execution&gt;
                        &lt;id&gt;make-assembly&lt;/id&gt;
                        &lt;phase&gt;package&lt;/phase&gt;
                        &lt;goals&gt;
                            &lt;goal&gt;single&lt;/goal&gt;
                        &lt;/goals&gt;
                    &lt;/execution&gt;
                &lt;/executions&gt;
            &lt;/plugin&gt;
            &lt;plugin&gt;
                &lt;groupId&gt;org.apache.maven.plugins&lt;/groupId&gt;
                &lt;artifactId&gt;maven-compiler-plugin&lt;/artifactId&gt;
                &lt;configuration&gt;
                    &lt;source&gt;1.7&lt;/source&gt;
                    &lt;target&gt;1.7&lt;/target&gt;
                &lt;/configuration&gt;
            &lt;/plugin&gt;
        &lt;/plugins&gt;
    &lt;/build&gt;
&lt;/project&gt;</code></pre>

<p> 打包上传运行：<br><img alt="" class="has" height="315" src="https://img-blog.csdnimg.cn/20181119220047945.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNzY1MDQ1OA==,size_16,color_FFFFFF,t_70" width="777"></p>

<p><img alt="" class="has" height="582" src="https://img-blog.csdnimg.cn/20181119220112980.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNzY1MDQ1OA==,size_16,color_FFFFFF,t_70" width="703"></p>

<p>启动生产数据的脚本：<br><img alt="" class="has" height="461" src="https://img-blog.csdnimg.cn/20181119220148712.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNzY1MDQ1OA==,size_16,color_FFFFFF,t_70" width="615"></p>            </div>
                </div>