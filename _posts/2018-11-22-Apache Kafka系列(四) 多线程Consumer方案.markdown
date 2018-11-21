---
layout:     post
title:      Apache Kafka系列(四) 多线程Consumer方案
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p><a href="http://www.cnblogs.com/qizhelongdeyang/p/7355309.html" rel="nofollow">http://www.cnblogs.com/qizhelongdeyang/p/7355309.html</a></p>

<ul><li><a href="http://www.cnblogs.com/qizhelongdeyang/p/7341954.html" rel="nofollow">Apache Kafka系列(一) 起步</a></li>
	<li><a href="http://www.cnblogs.com/qizhelongdeyang/p/7354315.html" rel="nofollow">Apache Kafka系列(二) 命令行工具（CLI）</a></li>
	<li><a href="http://www.cnblogs.com/qizhelongdeyang/p/7354183.html" rel="nofollow">Apache Kafka系列(三) Java API使用</a></li>
	<li><a href="http://www.cnblogs.com/qizhelongdeyang/p/7355309.html" rel="nofollow">Apache Kafka系列(四) 多线程Consumer方案</a></li>
	<li><a href="http://www.cnblogs.com/qizhelongdeyang/p/7407273.html" rel="nofollow">Apache Kafka系列(五) Kafka Connect及FileConnector示例</a></li>
</ul><p><strong>本文的图片是通过<a href="http://files.cnblogs.com/files/qizhelongdeyang/kafka.pptx" rel="nofollow">PPT</a>截图出的，读者如果修改意见请联系我</strong></p>

<h3>一、Consumer为何需要实现多线程</h3>

<p>　　假设我们正在开发一个消息通知模块，该模块允许用户订阅其他用户发送的通知/消息。该消息通知模块采用Apache Kafka，那么整个架构应该是消息的发布者通过Producer调用API写入消息到Kafka Cluster中，然后消息的订阅者通过Consumer读取消息，刚开始的时候系统架构图如下：</p>

<p><img alt="" class="has" src="https://images2017.cnblogs.com/blog/314515/201708/314515-20170813232834617-1579907180.png"></p>

<p>          但是，随着用户数量的增多，通知的数据也会对应的增长。总会达到一个阈值，在这个点上，Producer产生的数量大于Consumer能够消费的数量。那么Broker中未消费的消息就会逐渐增多。即使Kafka使用了优秀的消息持久化机制来保存未被消费的消息，但是<em>Kafka的消息保留机制限制</em>（时间，分区大小，消息Key）也会使得始终未被消费的Message被永久性的删除。另一方面从业务上讲，一个消息通知系统的高延迟几乎算作是废物了。所以多线程的Consumer模型是非常有必要的。</p>

<h3>二、多线程的Kafka Consumer 模型类别</h3>

<p>　　基于Consumer的多线程模型有两种类型：</p>

<ul><li>模型一：多个Consumer且每一个Consumer有自己的线程，对应的架构图如下：</li>
</ul><p>                         <img alt="" class="has" src="https://images2017.cnblogs.com/blog/314515/201708/314515-20170814000037960-579515983.png"></p>

<p> </p>

<ul><li>模型二：一个Consumer且有多个Worker线程</li>
</ul><p>                         <img alt="" class="has" src="https://images2017.cnblogs.com/blog/314515/201708/314515-20170814000045617-792211891.png"></p>

<p>   　　两种实现方式的优点/缺点比较如下：</p>

<table border="0"><tbody><tr><th>名称</th>
			<th>优点</th>
			<th>缺点</th>
		</tr><tr><td>模型一</td>
			<td>
			<p>1.Consumer Group容易实现</p>

			<p>2.各个Partition的顺序实现更容易</p>
			</td>
			<td>
			<p>1.Consumer的数量不能超过Partition的数量，否则多出的Consumer永远不会被使用到</p>

			<p>2.因没个Consumer都需要一个TCP链接，会造成大量的系统性能损耗</p>
			</td>
		</tr><tr><td>模型二</td>
			<td>1.由于通过线程池实现了Consumer，横向扩展更方便</td>
			<td>
			<p>1.在每个Partition上实现顺序处理更困难。</p>

			<p>例如：同一个Partition上有两个待处理的Message需要被线程池中的2个线程消费掉，那这两个线程必须实现同步</p>
			</td>
		</tr></tbody></table><h3>三、代码实现</h3>

<p>3.1 前提</p>

<ul><li>
	<ul><li>Kafka Broker 0.11.0</li>
		<li>JDK1.8</li>
		<li>IDEA</li>
		<li>Maven3</li>
		<li>Kafka环境搭建及Topic创建修改等请参照本系列的前几篇文章。</li>
	</ul></li>
</ul><p> 3.2 源码结构</p>

<p>                 <img alt="" class="has" height="495" src="https://images2017.cnblogs.com/blog/314515/201708/314515-20170820165717006-858215211.png" width="350"></p>

<p>       其中，consumergroup包下面对应的是模型一的代码，consumerthread包下是模型二的代码。ProducerThread是生产者代码。</p>

<p> 3.3 pom.xml</p>

<p><a><img alt="复制代码" class="has" src="http://common.cnblogs.com/images/copycode.gif"></a></p>

<pre>
&lt;project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd"&gt;
  &lt;modelVersion&gt;4.0.0&lt;/modelVersion&gt;
  &lt;groupId&gt;com.randy&lt;/groupId&gt;
  &lt;artifactId&gt;kafka_multithread_consumer_model&lt;/artifactId&gt;
  &lt;packaging&gt;war&lt;/packaging&gt;
  &lt;version&gt;1.0-SNAPSHOT&lt;/version&gt;
  &lt;name&gt;kafka_multithread_consumer_model Maven Webapp&lt;/name&gt;
  &lt;url&gt;http://maven.apache.org&lt;/url&gt;


  &lt;properties&gt;
    &lt;project.build.sourceEncoding&gt;UTF-8&lt;/project.build.sourceEncoding&gt;
    &lt;maven.compiler.source&gt;1.8&lt;/maven.compiler.source&gt;
    &lt;maven.compiler.target&gt;1.8&lt;/maven.compiler.target&gt;
  &lt;/properties&gt;

  &lt;dependencies&gt;
    &lt;dependency&gt;
      &lt;groupId&gt;org.apache.kafka&lt;/groupId&gt;
      &lt;artifactId&gt;kafka-clients&lt;/artifactId&gt;
      &lt;version&gt;0.11.0.0&lt;/version&gt;
    &lt;/dependency&gt;
  &lt;/dependencies&gt;

  &lt;build&gt;
    &lt;finalName&gt;kafka_multithread_consumer_model&lt;/finalName&gt;
  &lt;/build&gt;
&lt;/project&gt;</pre>

<p><a><img alt="复制代码" class="has" src="http://common.cnblogs.com/images/copycode.gif"></a></p>

<p> 3.4 方案一：Consumer Group</p>

<p>　　ProducerThread.java是一个生产者线程，发送消息到Broker</p>

<p>　　ConsumerThread.java是一个消费者线程，由于消费消息</p>

<p>　　ConsumerGroup.java用于产生一组消费者线程</p>

<p>　　ConsumerGroupMain.java是入口类     </p>

<p><strong>3.4.1 ProducerThread.java　</strong></p>

<p><img alt="" class="has" id="code_img_closed_b3fa77fd-7b94-4ed3-9388-411883945090" src="https://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif"> View Code</p>

<p> <strong>3.4.2 ConsumerThread.java</strong></p>

<p><img alt="" class="has" id="code_img_closed_50bf2bb9-4cf5-4001-bf9a-d089734c04ca" src="https://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif"> View Code</p>

<p><strong>3.4.3 ConsumerGroup.java</strong></p>

<p><img alt="" class="has" id="code_img_closed_23d6e338-7a27-4a31-9282-26301d7882e5" src="https://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif"> View Code</p>

<p><strong>3.4.4 ConsumerGroupMain.java　</strong>　</p>

<p><img alt="" class="has" id="code_img_closed_1435cace-31af-41dc-ac3b-9afc5ef31b9d" src="https://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif"> View Code</p>

<h3>3.5 方案二：多线程的Consumer</h3>

<p>　　ConsumerThreadHandler.java用于处理发送到消费者的消息</p>

<p>　　ConsumerThread.java是消费者使用线程池的方式初始化消费者线程</p>

<p>　　ConsumerThreadMain.java是入口类</p>

<p><strong>3.5.1 ConsumerThreadHandler.java</strong></p>

<p><img alt="" class="has" id="code_img_closed_10a52ce3-599d-4567-b8d5-b31ecd5a8aa1" src="https://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif"> View Code</p>

<p><strong>3.5.2 ConsumerThread.java</strong></p>

<p><img alt="" class="has" id="code_img_closed_39fdebad-d9ff-431e-85e6-675e4a25d87a" src="https://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif"> View Code</p>

<p><strong>3.5.3 ConsumerThreadMain.java</strong></p>

<p><img alt="" class="has" id="code_img_closed_72df60b7-edc5-4f5b-a3c6-701775e2e1b0" src="https://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif"> View Code</p>

<h3>四. 总结</h3>

<p>　　本篇文章列举了两种不同的消费者模式。两者各有利弊。所有代码都上传到了https://github.com/qizhelongdeyang/kafka_multithread_consumer_model.git ，如有疑问或者错误请指正</p>

<p> </p>

<p> </p>            </div>
                </div>