---
layout:     post
title:      Centons7下安装配置Kafka、Linux下安装配置Kafka
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>本篇文章主要介绍在windows下使用vmware虚拟机中的Linux(Centons7)下配置安装Kafka。</p>

<h2>简要步骤：</h2>

<p>①、下载Kafka解压包</p>

<p>②、解压Kafka</p>

<p>③、修改配置文件</p>

<p>④、启动Kafka</p>

<p>⑤、创建topic</p>

<p> </p>

<p></p>

<h2>详细步骤：</h2>

<h3><a name="t2"></a>一、Kafka下载</h3>

<p><a href="http://archive.apache.org/dist/kafka/2.0.0/" rel="nofollow">http://archive.apache.org/dist/kafka/2.0.0/</a></p>

<p></p>

<p><img alt="" class="has" src="https://img-blog.csdnimg.cn/20181117175343227.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3pqaF83NDYxNDAxMjk=,size_16,color_FFFFFF,t_70"></p>

<h3>二、解压</h3>

<p></p>

<pre class="has">
<code>tar -zxvf kafka_2.11-2.0.0.tgz</code></pre>

<p><img alt="" class="has" src="https://img-blog.csdnimg.cn/20181117175353955.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3pqaF83NDYxNDAxMjk=,size_16,color_FFFFFF,t_70"></p>

<h3>三、配置</h3>

<p>进入kafka的config目录下，有一个server.properties，添加如下配置</p>

<p>kafka配置文件说明：<a href="http://www.cnblogs.com/yinchengzhe/p/5111635.html" rel="nofollow">http://www.cnblogs.com/yinchengzhe/p/5111635.html</a></p>

<p></p>

<pre class="has">
<code>vim server.properties</code></pre>

<p><img alt="" class="has" src="https://img-blog.csdnimg.cn/20181117175405888.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3pqaF83NDYxNDAxMjk=,size_16,color_FFFFFF,t_70"></p>

<pre class="has">
<code># broker的全局唯一编号，不能重复
broker.id=0
# 监听
listeners=PLAINTEXT://:9092
port=9092
# 日志目录
log.dirs=/home/hadoop/kafka-logs
# 配置zookeeper的连接（如果不是本机，需要该为ip或主机名）
zookeeper.connect=localhost:2181</code></pre>

<p></p>

<p></p>

<p><img alt="" class="has" src="https://img-blog.csdnimg.cn/20181117175416112.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3pqaF83NDYxNDAxMjk=,size_16,color_FFFFFF,t_70"></p>

<h3>四、启动</h3>

<p>进入到kafka目录</p>

<p></p>

<pre class="has">
<code>bin/kafka-server-start.sh config/server.properties </code></pre>

<p><img alt="" class="has" src="https://img-blog.csdnimg.cn/20181117175431516.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3pqaF83NDYxNDAxMjk=,size_16,color_FFFFFF,t_70"></p>

<p>查看是否成功</p>

<p></p>

<p><img alt="" class="has" src="https://img-blog.csdnimg.cn/20181117175441710.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3pqaF83NDYxNDAxMjk=,size_16,color_FFFFFF,t_70"></p>

<p> </p>

<h3>五、创建topic，进入bin目录</h3>

<p>①创建topic</p>

<p></p>

<pre class="has">
<code>./kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic test</code></pre>

<p>参数说明： </p>

<p>–zookeeper：指定kafka连接zk的连接url，该值和server.properties文件中的配置项{zookeeper.connect}一样 </p>

<p>–replication-factor：指定副本数量 </p>

<p>–partitions：指定分区数量 </p>

<p>–topic：主题名称 </p>

<p></p>

<p><img alt="" class="has" src="https://img-blog.csdnimg.cn/20181117175538908.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3pqaF83NDYxNDAxMjk=,size_16,color_FFFFFF,t_70"></p>

<p>②查看所有的topic信息</p>

<pre class="has">
<code>./kafka-topics.sh --list --zookeeper localhost:2181</code></pre>

<p></p>

<p><img alt="" class="has" src="https://img-blog.csdnimg.cn/20181117175548715.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3pqaF83NDYxNDAxMjk=,size_16,color_FFFFFF,t_70"></p>

<p>③启动生产者</p>

<pre class="has">
<code>./kafka-console-producer.sh --broker-list localhost:9092 --topic test</code></pre>

<p></p>

<p><img alt="" class="has" src="https://img-blog.csdnimg.cn/20181117175600494.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3pqaF83NDYxNDAxMjk=,size_16,color_FFFFFF,t_70"></p>

<p>④启动消费者</p>

<pre class="has">
<code>./kafka-console-consumer.sh --zookeeper localhost:2181 --topic test --from-beginning

./kafka-console-consumer.sh --bootstrap-server localhost:2181 --topic test --from-beginning

./kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic test --from-beginning</code></pre>

<p>⑤查看消费者生产者 </p>

<p></p>

<p><img alt="" class="has" src="https://img-blog.csdnimg.cn/20181117175609549.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3pqaF83NDYxNDAxMjk=,size_16,color_FFFFFF,t_70"></p>

<p></p>

<p><img alt="" class="has" src="https://img-blog.csdnimg.cn/20181117175622230.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3pqaF83NDYxNDAxMjk=,size_16,color_FFFFFF,t_70"></p>

<p> </p>

<p>⑥删除topic</p>

<pre class="has">
<code>./kafka-topics.sh --delete --zookeeper localhost:2181 --topic log4jtest</code></pre>

<div> </div>

<p><img alt="" class="has" src="https://img-blog.csdnimg.cn/20181117175630797.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3pqaF83NDYxNDAxMjk=,size_16,color_FFFFFF,t_70"></p>            </div>
                </div>