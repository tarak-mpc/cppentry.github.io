---
layout:     post
title:      Flume与Kafka整合
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/shen_jz2012/article/details/43483587				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<span style="font-family:'黑体';font-size:19px;">     1、构造</span>
<div style="font-family:Tahoma;font-size:19px;"><span style="font-family:'黑体';">     Flume与Kafka整合就是接口的实现，将Kafka的producer API实现为Flume的sink。简单理解就是将Flume的输出（sinks）作为Kafka的输入（producer）。</span></div>
<div style="font-family:Tahoma;font-size:19px;"><span style="font-family:'黑体';">     </span></div>
<div style="font-family:Tahoma;font-size:19px;"><span style="font-family:'黑体';">     2、准备工作</span></div>
<div style="font-family:Tahoma;font-size:19px;"><span style="font-family:'黑体';">     1）将Kafka libs下所有的包都拷贝到Flume的lib目录下，这样运行基本不会有问题。</span></div>
<div style="font-family:Tahoma;font-size:19px;"><span style="font-family:'黑体';">     2）做了第一步工作之后，在Flume的lib目录下找到如下这些包，在接下来的eclipse开发中需要这些包：</span></div>
<div style="font-family:Tahoma;font-size:19px;"><span style="font-family:'黑体';"><br></span></div>
<div style="text-align:center;font-family:Tahoma;font-size:19px;">
<span style="font-family:'黑体';"><img src="https://img-blog.csdn.net/20150204111317925?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc2hlbl9qejIwMTI=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></div>
<div style="font-family:Tahoma;font-size:19px;"><span style="font-family:'黑体';"><br></span></div>
<div style="font-family:Tahoma;font-size:19px;"><span style="font-family:'黑体';">     3、代码实现</span></div>
<blockquote style="font-family:Tahoma;font-size:19px;border:none;">
<pre><code class="language-java">package com.mrshen.cvlab.scut;

import java.util.Properties;

import kafka.javaapi.producer.Producer;
import kafka.producer.KeyedMessage;
import kafka.producer.ProducerConfig;

import org.apache.commons.logging.Log;
import org.apache.commons.logging.LogFactory;
import org.apache.flume.Channel;
import org.apache.flume.Context;
import org.apache.flume.Event;
import org.apache.flume.EventDeliveryException;
import org.apache.flume.Transaction;
import org.apache.flume.conf.Configurable;
import org.apache.flume.sink.AbstractSink;

public class KafkaSink extends AbstractSink implements Configurable {

	private static final Log logger = LogFactory.getLog(KafkaSink.class);
	
	private String topic;
	private Producer&lt;String, String&gt;producer;
	
	@Override
	public Status process() throws EventDeliveryException {
		// TODO Auto-generated method stub
		Channel channel = getChannel();
		Transaction transaction = channel.getTransaction();
		
		try {
			transaction.begin();
			Event event = channel.take();
			if (event == null ) {
				transaction.rollback();
				return Status.BACKOFF;
			}
			
			KeyedMessage&lt;String, String&gt;data = new KeyedMessage&lt;String, String&gt;(
					topic, new String(event.getBody()));
			producer.send(data);
			logger.info("flume2kafka: " + new String(event.getBody()));
			transaction.commit();
			return Status.READY;
			
		} catch (Exception e) {
			// TODO: handle exception
			logger.error("Flume KafkaSinkException", e);
			transaction.rollback();
			return Status.BACKOFF;
		} finally {
			transaction.close();
		}
	}

	@Override
	public void configure(Context arg0) {
		// TODO Auto-generated method stub
		topic = "kafkaTopic";
		
		Properties properties = new Properties();
		properties.setProperty("metadata.broker.list", "storm:9092");
		properties.setProperty("serializer.class", "kafka.serializer.StringEncoder");
		properties.setProperty("num.partitions", "4");
		
/**/		properties.put("partitioner.class", "kafka.producer.DefaultPartitioner");
/**/		properties.put("zookeeper.connect", "storm:2181");
		properties.put("request.required.acks", "1");
		
		ProducerConfig pConfig = new ProducerConfig(properties);
		producer = new Producer&lt;String, String&gt;(pConfig);
		logger.info("KafkaSink init successfully!");

	}
	
}
</code></pre><br></blockquote>
<div style="font-family:Tahoma;font-size:19px;"><span style="font-family:Consolas;font-family:Consolas;">     </span><span style="font-family:'黑体';">PS：实现的configure抽象方法中参数（key）的设置是根据Kafka的producer API来填充的，wiki</span><span style="font-family:Consolas;font-family:Consolas;">：</span><a href="https://cwiki.apache.org/confluence/display/KAFKA/0.8.0+Producer+Example" rel="nofollow" style="font-family:Consolas;">https://cwiki.apache.org/confluence/display/KAFKA/0.8.0+Producer+Example</a><span style="font-family:'黑体';">，而参数中的值（value）用到时官网默认的值，在文档 </span><a href="http://kafka.apache.org/082/documentation.html#producerconfigs" rel="nofollow" style="font-family:Consolas;">http://kafka.apache.org/082/documentation.html#producerconfigs</a><span style="font-family:Consolas;"> </span><span style="font-family:'黑体';">的3.3
 Producer Configs 小节。</span></div>
<div style="font-size:19px;font-family:Consolas;"><span style="font-family:Consolas;"><br></span></div>
<span style="font-family:'黑体';font-size:19px;">     4、测试</span>
<div style="font-family:Tahoma;font-size:19px;"><span style="font-family:'黑体';">     1）首先导出上述代码的jar包，放在Flume的lib目录下。</span></div>
<div style="font-family:Tahoma;font-size:19px;"><span style="font-family:'黑体';">     2）配置Flume的agent文件，在本例中，主要修改了sink的type，将其指定为我们写好的KafkaSink类。</span></div>
<div style="font-family:Tahoma;font-size:19px;"><span style="font-family:'黑体';"><br></span></div>
<div style="text-align:center;font-family:Tahoma;font-size:19px;">
<span style="font-family:'黑体';"><img src="https://img-blog.csdn.net/20150204111600977?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc2hlbl9qejIwMTI=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></div>
<div style="font-family:Tahoma;font-size:19px;"><span style="font-family:'黑体';"><br></span><span style="font-family:'黑体';">     3）启动zookeeper和Kafka服务</span></div>
<div style="font-family:Tahoma;font-size:19px;"><span style="font-family:'黑体';">     4）在终端中启动flume agent（截取部分日志）</span></div>
<div style="font-family:Tahoma;font-size:19px;"><span style="font-family:'黑体';"><br></span></div>
<div style="text-align:center;font-family:Tahoma;font-size:19px;">
<span style="font-family:'黑体';"><img src="https://img-blog.csdn.net/20150204111756839?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc2hlbl9qejIwMTI=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></div>
<div style="font-family:Tahoma;font-size:19px;"><span style="font-family:'黑体';"><br></span></div>
<div style="text-align:center;font-family:Tahoma;font-size:19px;">
<span style="font-family:'黑体';"><img src="https://img-blog.csdn.net/20150204112001576?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc2hlbl9qejIwMTI=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></div>
<div style="font-family:Tahoma;font-size:19px;"><span style="font-family:'黑体';"><br></span></div>
<div style="font-family:Tahoma;font-size:19px;"><span style="font-family:'黑体';">     5）在终端中向Flume发送syslog</span></div>
<div style="font-family:Tahoma;font-size:19px;"><span style="font-family:'黑体';color:#E30000;">     echo “test flume 2 kafka” | nc storm 6666</span></div>
<div style="font-family:Tahoma;font-size:19px;"><span style="font-family:'黑体';">     nc后面写上你的主机名和在agent的配置文件中指定的端口号，执行该命令之后可以在启动flume的终端看到该信息</span></div>
<div style="font-family:Tahoma;font-size:19px;"><span style="font-family:'黑体';"><br></span></div>
<div style="text-align:center;font-family:Tahoma;font-size:19px;">
<span style="font-family:'黑体';"><img src="https://img-blog.csdn.net/20150204112404016?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc2hlbl9qejIwMTI=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></div>
<div style="font-family:Tahoma;font-size:19px;"><span style="font-family:'黑体';"><br></span></div>
<div style="font-family:Tahoma;font-size:19px;"><span style="font-family:'黑体';">     6）在另一个终端开启Kafka消费者，订阅在KafkaSink类中实现的topic “kafkaTopic”</span></div>
<div style="font-family:Tahoma;font-size:19px;"><span style="font-family:'黑体';"><br></span></div>
<div style="text-align:center;font-family:Tahoma;font-size:19px;">
<span style="font-family:'黑体';"><img src="https://img-blog.csdn.net/20150204112434982?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc2hlbl9qejIwMTI=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></div>
<div style="font-family:Tahoma;font-size:19px;"><span style="font-family:'黑体';"><br></span></div>
<div style="font-family:Tahoma;font-size:19px;"><span style="font-family:'黑体';">     成功看到Kafka消费者受到Flume端发送来的消息。至此Flume与Kafka的整合完成。</span></div>
            </div>
                </div>