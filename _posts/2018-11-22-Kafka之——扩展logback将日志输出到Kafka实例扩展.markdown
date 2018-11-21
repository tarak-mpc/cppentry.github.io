---
layout:     post
title:      Kafka之——扩展logback将日志输出到Kafka实例扩展
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/l1028386804/article/details/79136841				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><a href="http://blog.csdn.net/l1028386804/article/details/79136841" rel="nofollow">转载请注明出处：http://blog.csdn.net/l1028386804/article/details/79136841</a><br></p>
<h3>一、概述</h3>
<p>本文基于《<a href="http://blog.csdn.net/l1028386804/article/details/79135948" rel="nofollow">Kafka之——扩展logback将日志输出到Kafka实例</a>》，请先阅读《<a href="http://blog.csdn.net/l1028386804/article/details/79135948" rel="nofollow">Kafka之——扩展logback将日志输出到Kafka实例</a>》。</p>
<h3>二、扩展</h3>
<p>《<a href="http://blog.csdn.net/l1028386804/article/details/79135948" rel="nofollow">Kafka之——扩展logback将日志输出到Kafka实例</a>》一文中，只能输出单个字符串到Kafka，本文对上一篇博文《<a href="http://blog.csdn.net/l1028386804/article/details/79135948" rel="nofollow">Kafka之——扩展logback将日志输出到Kafka实例</a>》做了相应的扩展，能够输出Json字符串到Kafka。主要变动如下：</p>
<h4>1、新增JsonFormatter类</h4>
<p>主要的作用是接收ILoggingEvent返回Json字符串，代码如下：</p>
<p></p>
<pre><code class="language-java">package com.lyz.storm.formatter.impl;

import com.lyz.storm.formatter.Formatter;

import ch.qos.logback.classic.spi.ILoggingEvent;

/**
 * Json格式的Formatter
 * @author liuyazhuang
 *
 */
public class JsonFormatter implements Formatter {
	
	private static final String QUOTE = "\"";
	private static final String COLON = ":";
	private static final String COMMA = ",";
	
	private boolean expectJson = false;
	

	@Override
	public String format(ILoggingEvent event) {
		StringBuilder sb = new StringBuilder();
		sb.append("{");
		fieldName("level", sb);
		quote(event.getLevel().levelStr, sb);
		sb.append(COMMA);
		fieldName("logger", sb);
		quote(event.getLoggerName(), sb);
		sb.append(COMMA);
		fieldName("timestamp", sb);
		sb.append(event.getTimeStamp());
		sb.append(COMMA);
		fieldName("message", sb);
		if(this.expectJson){
			sb.append(event.getFormattedMessage());
		}else{
			quote(event.getFormattedMessage(), sb);
		}
		sb.append("}");
		return sb.toString();
	}
	
	private void fieldName(String name, StringBuilder sb){
		quote(name, sb);
		sb.append(COLON);
	}
	
	private void quote(String value, StringBuilder sb){
		sb.append(QUOTE);
		sb.append(value);
		sb.append(QUOTE);
	}

	public boolean isExpectJson() {
		return expectJson;
	}

	public void setExpectJson(boolean expectJson) {
		this.expectJson = expectJson;
	}
}
</code></pre>
<h4>2、修改logback.xml如下：</h4>
<p></p>
<pre><code class="language-html">&lt;?xml version="1.0" encoding="UTF-8"?&gt;
&lt;configuration&gt;
   &lt;appender name="KAFKA" class="com.lyz.storm.appender.KafkaAppender"&gt;
   		&lt;topic&gt;foo&lt;/topic&gt;
   		&lt;zookeeperHost&gt;192.168.209.121:2181&lt;/zookeeperHost&gt;
   		&lt;brokerList&gt;192.168.209.121:9092&lt;/brokerList&gt;
   		&lt;formatter class="com.lyz.storm.formatter.impl.JsonFormatter"&gt;
   			&lt;expectJson&gt;false&lt;/expectJson&gt;
   		&lt;/formatter&gt;
   &lt;/appender&gt;
   &lt;root level="debug"&gt;
   		&lt;appender-ref ref="KAFKA"/&gt;
   &lt;/root&gt;
&lt;/configuration&gt;</code></pre>
<h3>三、测试</h3>
<p>在Kafka服务器命令行输入如下命令：</p>
<p></p><pre><code class="language-plain">kafka-console-consumer.sh --zookeeper localhost:2181 --topic foo --from-beginning </code></pre>监听日志输出。
<p>此时，我们运行RogueApplication类，监听到的日志如下：</p>
<p></p><pre><code class="language-plain">[root@liuyazhuang121 local]# kafka-console-consumer.sh --zookeeper localhost:2181 --topic foo --from-beginning      
{"level":"WARN","logger":"com.lyz.storm.trend.RogueApplication","timestamp":1516674702158,"message":"This is a warning (slow state)."}
{"level":"WARN","logger":"com.lyz.storm.trend.RogueApplication","timestamp":1516674707699,"message":"This is a warning (slow state)."}
{"level":"WARN","logger":"com.lyz.storm.trend.RogueApplication","timestamp":1516674712700,"message":"This is a warning (slow state)."}</code></pre>如下图所示：
<p><img src="https://img-blog.csdn.net/20180123104701056?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbDEwMjgzODY4MDQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br></p>
<h3>四、温馨提示</h3>
<p><span style="font-size:18px;">大家可以到链接<a href="http://download.csdn.net/download/l1028386804/10219227" rel="nofollow">http://download.csdn.net/download/l1028386804/10219227</a>下载完整扩展源码</span><br></p>
<p><br></p>
<p></p>
            </div>
                </div>