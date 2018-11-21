---
layout:     post
title:      kafka在linux下安装教程及其消费者项目开发搭建教程
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/qq_19524879/article/details/82021344				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>1.下载kafka的 没有的可以到我百度网盘<a href="https://pan.baidu.com/s/1pSExYqT45TjbYV5c4OFl-Q" rel="nofollow">下载</a></p>

<p>2.下载的文件上传至linux系统下面新建在根目录新建个文件夹下面（JDK别忘记安装）</p>

<pre class="has">
<code>cd / --进入根目录
mkdir kafka --创建kafka目录
cd kafka --进入kafka目录</code></pre>

<p>3.解压kafka文件</p>

<pre class="has">
<code>tar -zxf kafka_2.11-2.0.0.tgz --解压安装</code></pre>

<p>4.进入目录下面新建 zookeeper 数据存放目录（如果外部zookeeper可以忽略本步骤）和创建kafka日志文件目录</p>

<pre class="has">
<code>mkdir zdata --创建zookeeper存放数据目录
mkdir logs --创建kafka日志存放目录
</code></pre>

<p>5.修改zookeeper配置文件信息</p>

<pre class="has">
<code>cd kafka_2.11-2.0.0/config/ --进入目录
vim zookeeper.properties --修改配置文件信息</code></pre>

<p><img alt="" class="has" height="73" src="https://img-blog.csdn.net/20180824165234203?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzE5NTI0ODc5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="287">红框中改成步骤4所创建的目录路径</p>

<p>6.修改kafka配置文件</p>

<pre class="has">
<code>vim server.properties </code></pre>

<p>log.dirs=/kafka/logs 日志存放路径<br>
port=9092 端口<br>
host.name=192.168.163.131 IP地址</p>

<p>zookeeper.connect=localhost:2181 zookeeper的地址本机的就localhost 其他服务器的用IP加端口就可以了</p>

<p>7.启动zookeeper服务</p>

<pre class="has">
<code>cd kafka_2.11-2.0.0/ --进入目录
bin/zookeeper-server-start.sh config/zookeeper.properties --启动服务
</code></pre>

<p><img alt="" class="has" height="573" src="https://img-blog.csdn.net/20180824165739283?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzE5NTI0ODc5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="780"></p>

<p>8.启动kafka服务</p>

<pre class="has">
<code>bin/kafka-server-start.sh config/server.properties</code></pre>

<p>9.下面就是创建topic  名为test</p>

<pre class="has">
<code>bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 3 --partitions 3 --topic test</code></pre>

<p>10.启动生产主体</p>

<pre class="has">
<code>bin/kafka-console-producer.sh --broker-list 192.168.163.131:9092 --topic test</code></pre>

<p><img alt="" class="has" height="45" src="https://img-blog.csdn.net/20180824170402341?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzE5NTI0ODc5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="745"></p>

<p>写内容回车就可以发送信息了。</p>

<p> </p>

<p><strong>好了上面安装讲完了，下面开始说下消费者项目搭建</strong></p>

<p><strong>项目采用的是springboot</strong></p>

<p><strong>1.在pom.xml加入引用</strong></p>

<pre class="has">
<code> &lt;dependency&gt;
            &lt;groupId&gt;org.springframework.kafka&lt;/groupId&gt;
            &lt;artifactId&gt;spring-kafka&lt;/artifactId&gt;
        &lt;/dependency&gt;</code></pre>

<p>2.在application.yml 文件中加上</p>

<pre class="has">
<code>spring:
  kafka:
    consumer:
      enable-auto-commit: true
      group-id: applog
      auto-offset-reset: latest
      bootstrap-servers: 192.168.163.131:9092 //kafka的地址</code></pre>

<p>3.创建个监听类</p>

<pre class="has">
<code>package com.netintech.kafka.utils;
import com.netintech.kafka.bean.Test;
import com.netintech.kafka.mapper.TestRepository;
import org.apache.kafka.clients.consumer.ConsumerRecord;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.kafka.annotation.KafkaListener;
import org.springframework.stereotype.Component;

@Component
public class Listener {
   
    protected final Logger logger = LoggerFactory.getLogger(this.getClass());

    @KafkaListener(topics = {"test"})
    public void receive(String message){
        System.out.println("test1--消费消息:" + message);
       
    }
   
}
</code></pre>

<p>启动项目开始测试 </p>

<p>在服务器使用生产者发送消息</p>

<p><img alt="" class="has" height="743" src="https://img-blog.csdn.net/20180824171138974?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzE5NTI0ODc5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="1200"></p>            </div>
                </div>