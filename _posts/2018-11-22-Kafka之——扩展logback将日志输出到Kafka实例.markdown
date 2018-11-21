---
layout:     post
title:      Kafka之——扩展logback将日志输出到Kafka实例
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/l1028386804/article/details/79135948				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><a href="http://blog.csdn.net/l1028386804/article/details/79135948" rel="nofollow">转载请注明出处：http://blog.csdn.net/l1028386804/article/details/79135948</a><br></p>
<h3>一、Kafka的安装</h3>
<p>请参见博文《<a href="http://blog.csdn.net/l1028386804/article/details/78348367" rel="nofollow">Kafka之——安装并（单节点）配置启动Kafka</a>》、《<a href="http://blog.csdn.net/l1028386804/article/details/78374786" rel="nofollow">Kafka之——单机多broker实例集群搭建</a>》、《<a href="http://blog.csdn.net/l1028386804/article/details/78374836" rel="nofollow">Kafka之——集群搭建</a>》。</p>
<p><span style="color:#FF0000;"><strong>注意：本文是基于kafka_2.11-0.9.0.0.tgz、zookeeper-3.4.9.tar.gz 实现的。</strong></span><br></p>
<h3>二、实现</h3>
<h4>1、创建Formatter接口</h4>
<p>主要作用是定义一个format方法，接收ILoggingEvent对象，返回字符串。具体代码如下：</p>
<p></p>
<pre><code class="language-java">package com.lyz.storm.formatter;

import ch.qos.logback.classic.spi.ILoggingEvent;

/**
 * 定义格式化接口
 * @author liuyazhuang
 *
 */
public interface Formatter {
	
	String format(ILoggingEvent event);
}
</code></pre>
<p></p>
<h4>2、创建MessageFormatter类</h4>
<p>主要是实现Formatter接口，主要代码如下：</p>
<p></p>
<pre><code class="language-java">package com.lyz.storm.formatter;

import ch.qos.logback.classic.spi.ILoggingEvent;

/**
 * 实现Formatter接口，接收ILoggingEvent对象返回一个字符串供消费者处理
 * @author liuyazhuang
 *
 */
public class MessageFormatter implements Formatter {

	@Override
	public String format(ILoggingEvent event) {
		return event.getFormattedMessage();
	}

}
</code></pre>
<p></p>
<h4>3、自定义KafkaAppender类</h4>
<p>主要的作用就是讲日志重定向输出到Kafka队列中，主要代码如下：</p>
<p></p>
<pre><code class="language-java">package com.lyz.storm.appender;

import java.util.Properties;

import com.lyz.storm.formatter.Formatter;
import com.lyz.storm.formatter.MessageFormatter;

import ch.qos.logback.classic.spi.ILoggingEvent;
import ch.qos.logback.core.AppenderBase;
import kafka.javaapi.producer.Producer;
import kafka.producer.KeyedMessage;
import kafka.producer.ProducerConfig;

/**
 * 自定义KafkaAppender将日志输出到Kafka中
 * @author liuyazhuang
 *
 */
public class KafkaAppender extends AppenderBase&lt;ILoggingEvent&gt; {
	
	private String topic;
	private String zookeeperHost;
	private Producer&lt;String, String&gt; producer;
	private Formatter formatter;
	private String brokerList;
	
	public String getTopic() {
		return topic;
	}

	public void setTopic(String topic) {
		this.topic = topic;
	}

	public String getZookeeperHost() {
		return zookeeperHost;
	}

	public void setZookeeperHost(String zookeeperHost) {
		this.zookeeperHost = zookeeperHost;
	}

	public Formatter getFormatter() {
		return formatter;
	}

	public void setFormatter(Formatter formatter) {
		this.formatter = formatter;
	}

	public String getBrokerList() {
		return brokerList;
	}

	public void setBrokerList(String brokerList) {
		this.brokerList = brokerList;
	}

	@Override
	public void start() {
		if(this.formatter == null){
			this.formatter = new MessageFormatter();
		}
		super.start();
		Properties props = new Properties();
		props.put("zk.connect", this.zookeeperHost);
	    props.put("serializer.class", "kafka.serializer.StringEncoder");
	    props.put("metadata.broker.list", this.brokerList);
	    ProducerConfig config = new ProducerConfig(props);
	    this.producer = new Producer&lt;String, String&gt;(config);
	}
	
	@Override
	public void stop() {
		super.stop();
		this.producer.close();
	}
	@Override
	protected void append(ILoggingEvent event) {
		String payload = this.formatter.format(event);
		KeyedMessage&lt;String, String&gt; data = new KeyedMessage&lt;String, String&gt;(this.topic, payload);
		this.producer.send(data);
	}

}
</code></pre>
<p></p>
<h4>4、创建RogueApplication类</h4>
<p>主要的作用就是模拟打印日志，主要代码如下：</p>
<p></p>
<pre><code class="language-java">package com.lyz.storm.trend;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

/**
 * 模拟打印日志
 * @author liuyazhuang
 *
 */
public class RogueApplication {
    private static final Logger LOG = LoggerFactory.getLogger("com.lyz.storm.trend.RogueApplication");
    public static void main(String[] args) throws Exception {
        int slowCount = 6;
        int fastCount = 15;
        while (true)        {
            for(int i = 0; i &lt; slowCount; i++){
                LOG.warn("This is a warning (slow state).");
                Thread.sleep(5000);
            }
            for(int i = 0; i &lt; fastCount; i++){
                LOG.warn("This is a warning (rapid state).");
                Thread.sleep(1000);
            }
            for(int i = 0; i &lt; slowCount; i++){
                LOG.warn("This is a warning (slow state).");
                Thread.sleep(5000);
            }
        }

    }
}
</code></pre>
<p></p>
<h4>5、创建logback.xml文件</h4>
<p>我们在项目的classpath目录下创建logback.xml文件，内容如下：</p>
<p></p>
<pre><code class="language-html">&lt;?xml version="1.0" encoding="UTF-8"?&gt;
&lt;configuration&gt;
   &lt;appender name="KAFKA" class="com.lyz.storm.appender.KafkaAppender"&gt;
   		&lt;topic&gt;test&lt;/topic&gt;
   		&lt;zookeeperHost&gt;192.168.209.121:2181&lt;/zookeeperHost&gt;
   		&lt;brokerList&gt;192.168.209.121:9092&lt;/brokerList&gt;
   &lt;/appender&gt;
   &lt;root level="debug"&gt;
   		&lt;appender-ref ref="KAFKA"/&gt;
   &lt;/root&gt;
&lt;/configuration&gt;</code></pre>
<p></p>
<h4>6、pom.xml</h4>
<p>最后附上我们的pom.xml</p>
<p></p>
<pre><code class="language-html">&lt;?xml version="1.0" encoding="UTF-8"?&gt;
&lt;project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd"&gt;
    &lt;modelVersion&gt;4.0.0&lt;/modelVersion&gt;

    &lt;groupId&gt;com.lyz&lt;/groupId&gt;
    &lt;artifactId&gt;storm-chapter04&lt;/artifactId&gt;
    &lt;version&gt;1.0&lt;/version&gt;
    &lt;name&gt;storm-chapter04&lt;/name&gt;


    &lt;properties&gt;
        &lt;project.build.sourceEncoding&gt;UTF-8&lt;/project.build.sourceEncoding&gt;
    &lt;/properties&gt;
    &lt;repositories&gt;
        &lt;repository&gt;
            &lt;id&gt;clojars.org&lt;/id&gt;
            &lt;url&gt;http://clojars.org/repo&lt;/url&gt;
        &lt;/repository&gt;
    &lt;/repositories&gt;
    &lt;dependencies&gt;

        &lt;!-- test --&gt;
        &lt;dependency&gt;
            &lt;groupId&gt;junit&lt;/groupId&gt;
            &lt;artifactId&gt;junit&lt;/artifactId&gt;
            &lt;version&gt;4.8.2&lt;/version&gt;
        &lt;/dependency&gt;
        &lt;dependency&gt;
            &lt;groupId&gt;org.apache.storm&lt;/groupId&gt;
            &lt;artifactId&gt;storm-core&lt;/artifactId&gt;
            &lt;version&gt;0.9.2-incubating&lt;/version&gt;
            &lt;exclusions&gt;
                &lt;exclusion&gt;
                    &lt;groupId&gt;org.slf4j&lt;/groupId&gt;
                    &lt;artifactId&gt;slf4j-log4j12&lt;/artifactId&gt;
                &lt;/exclusion&gt;
                &lt;exclusion&gt;
                    &lt;groupId&gt;log4j&lt;/groupId&gt;
                    &lt;artifactId&gt;log4j&lt;/artifactId&gt;
                &lt;/exclusion&gt;
            &lt;/exclusions&gt;
        &lt;/dependency&gt;
     &lt;dependency&gt;
		    &lt;groupId&gt;org.apache.kafka&lt;/groupId&gt;
		    &lt;artifactId&gt;kafka_2.11&lt;/artifactId&gt;
		    &lt;version&gt;0.9.0.0&lt;/version&gt;
		       &lt;exclusions&gt;  
                &lt;exclusion&gt;  
                    &lt;groupId&gt;org.apache.zookeeper&lt;/groupId&gt;  
                    &lt;artifactId&gt;zookeeper&lt;/artifactId&gt;  
                &lt;/exclusion&gt;  
                &lt;exclusion&gt;  
                    &lt;groupId&gt;log4j&lt;/groupId&gt;  
                    &lt;artifactId&gt;log4j&lt;/artifactId&gt;  
                &lt;/exclusion&gt;  
                &lt;exclusion&gt;  
                    &lt;groupId&gt;org.slf4j&lt;/groupId&gt;  
                    &lt;artifactId&gt;slf4j-log4j12&lt;/artifactId&gt;  
                &lt;/exclusion&gt;  
            &lt;/exclusions&gt; 
		&lt;/dependency&gt;
        &lt;dependency&gt;
            &lt;groupId&gt;storm&lt;/groupId&gt;
            &lt;artifactId&gt;storm-kafka&lt;/artifactId&gt;
            &lt;version&gt;0.9.0-wip16a-scala292&lt;/version&gt;
        &lt;/dependency&gt;
        &lt;dependency&gt;
            &lt;groupId&gt;xmlpull&lt;/groupId&gt;
            &lt;artifactId&gt;xmlpull&lt;/artifactId&gt;
            &lt;version&gt;1.1.3.4a&lt;/version&gt;
        &lt;/dependency&gt;

        &lt;dependency&gt;
            &lt;groupId&gt;org.igniterealtime.smack&lt;/groupId&gt;
            &lt;artifactId&gt;smack&lt;/artifactId&gt;
            &lt;version&gt;3.2.1&lt;/version&gt;
        &lt;/dependency&gt;


        &lt;dependency&gt;
            &lt;groupId&gt;org.igniterealtime.smack&lt;/groupId&gt;
            &lt;artifactId&gt;smackx&lt;/artifactId&gt;
            &lt;version&gt;3.2.1&lt;/version&gt;
        &lt;/dependency&gt;
        
        &lt;dependency&gt;
		    &lt;groupId&gt;org.apache.hadoop&lt;/groupId&gt;
		    &lt;artifactId&gt;hadoop-hdfs&lt;/artifactId&gt;
		    &lt;version&gt;2.9.0&lt;/version&gt;
		     &lt;exclusions&gt;
                &lt;exclusion&gt;
                    &lt;groupId&gt;org.slf4j&lt;/groupId&gt;
                    &lt;artifactId&gt;slf4j-log4j12&lt;/artifactId&gt;
                &lt;/exclusion&gt;
                &lt;exclusion&gt;
                    &lt;groupId&gt;log4j&lt;/groupId&gt;
                    &lt;artifactId&gt;log4j&lt;/artifactId&gt;
                &lt;/exclusion&gt;
            &lt;/exclusions&gt;
		&lt;/dependency&gt;
   		&lt;dependency&gt;
		    &lt;groupId&gt;org.apache.hadoop&lt;/groupId&gt;
		    &lt;artifactId&gt;hadoop-client&lt;/artifactId&gt;
		    &lt;version&gt;2.9.0&lt;/version&gt;
		     &lt;exclusions&gt;
                &lt;exclusion&gt;
                    &lt;groupId&gt;org.slf4j&lt;/groupId&gt;
                    &lt;artifactId&gt;slf4j-log4j12&lt;/artifactId&gt;
                &lt;/exclusion&gt;
                &lt;exclusion&gt;
                    &lt;groupId&gt;log4j&lt;/groupId&gt;
                    &lt;artifactId&gt;log4j&lt;/artifactId&gt;
                &lt;/exclusion&gt;
            &lt;/exclusions&gt;
		&lt;/dependency&gt;
        
        &lt;dependency&gt;
            &lt;groupId&gt;com.fasterxml.jackson.core&lt;/groupId&gt;
            &lt;artifactId&gt;jackson-annotations&lt;/artifactId&gt;
            &lt;version&gt;2.2.2&lt;/version&gt;
        &lt;/dependency&gt;
        &lt;dependency&gt;
            &lt;groupId&gt;com.fasterxml.jackson.core&lt;/groupId&gt;
            &lt;artifactId&gt;jackson-databind&lt;/artifactId&gt;
            &lt;version&gt;2.2.2&lt;/version&gt;
        &lt;/dependency&gt;
        
		&lt;dependency&gt;
			&lt;groupId&gt;com.metamx&lt;/groupId&gt;
			&lt;artifactId&gt;java-util&lt;/artifactId&gt;
			&lt;version&gt;0.26.12&lt;/version&gt;
		&lt;/dependency&gt;
    &lt;/dependencies&gt;

   &lt;build&gt;
		&lt;finalName&gt;storm-chapter04&lt;/finalName&gt;
		&lt;resources&gt;
			&lt;resource&gt;
				&lt;targetPath&gt;${project.build.directory}/classes&lt;/targetPath&gt;
				&lt;directory&gt;src/main/resources&lt;/directory&gt;
				&lt;filtering&gt;true&lt;/filtering&gt;
				&lt;includes&gt;
					&lt;include&gt;**/*.xml&lt;/include&gt;
					&lt;include&gt;**/*.properties&lt;/include&gt;
				&lt;/includes&gt;
			&lt;/resource&gt;
			&lt;!-- 结合com.alibaba.dubbo.container.Main --&gt;
			&lt;resource&gt;
				&lt;targetPath&gt;${project.build.directory}/classes/META-INF/spring&lt;/targetPath&gt;
				&lt;directory&gt;src/main/resources/spring&lt;/directory&gt;
				&lt;filtering&gt;true&lt;/filtering&gt;
				&lt;includes&gt;
					&lt;include&gt;spring-context.xml&lt;/include&gt;
				&lt;/includes&gt;
			&lt;/resource&gt;
		&lt;/resources&gt;
		
		&lt;pluginManagement&gt;
			&lt;plugins&gt;
				&lt;!-- 解决Maven插件在Eclipse内执行了一系列的生命周期引起冲突 --&gt;
				&lt;plugin&gt;
					&lt;groupId&gt;org.eclipse.m2e&lt;/groupId&gt;
					&lt;artifactId&gt;lifecycle-mapping&lt;/artifactId&gt;
					&lt;version&gt;1.0.0&lt;/version&gt;
					&lt;configuration&gt;
						&lt;lifecycleMappingMetadata&gt;
							&lt;pluginExecutions&gt;
								&lt;pluginExecution&gt;
									&lt;pluginExecutionFilter&gt;
										&lt;groupId&gt;org.apache.maven.plugins&lt;/groupId&gt;
										&lt;artifactId&gt;maven-dependency-plugin&lt;/artifactId&gt;
										  &lt;versionRange&gt;[2.0,)&lt;/versionRange&gt;  
										&lt;goals&gt;
											&lt;goal&gt;copy-dependencies&lt;/goal&gt;
										&lt;/goals&gt;
									&lt;/pluginExecutionFilter&gt;
									&lt;action&gt;
										&lt;ignore /&gt;
									&lt;/action&gt;
								&lt;/pluginExecution&gt;
							&lt;/pluginExecutions&gt;
						&lt;/lifecycleMappingMetadata&gt;
					&lt;/configuration&gt;
				&lt;/plugin&gt;
			&lt;/plugins&gt;
		&lt;/pluginManagement&gt;
		&lt;plugins&gt;
			&lt;!-- 打包jar文件时，配置manifest文件，加入lib包的jar依赖 --&gt;
			&lt;plugin&gt;
				&lt;groupId&gt;org.apache.maven.plugins&lt;/groupId&gt;
				&lt;artifactId&gt;maven-jar-plugin&lt;/artifactId&gt;
				&lt;configuration&gt;
					&lt;classesDirectory&gt;target/classes/&lt;/classesDirectory&gt;
					&lt;archive&gt;
						&lt;manifest&gt;
							&lt;mainClass&gt;com.lyz.chapter1.main.WordCountTopology&lt;/mainClass&gt;
							&lt;!-- 打包时 MANIFEST.MF文件不记录的时间戳版本 --&gt;
							&lt;useUniqueVersions&gt;false&lt;/useUniqueVersions&gt;
							&lt;addClasspath&gt;true&lt;/addClasspath&gt;
							&lt;classpathPrefix&gt;lib/&lt;/classpathPrefix&gt;
						&lt;/manifest&gt;
						&lt;manifestEntries&gt;
							&lt;Class-Path&gt;.&lt;/Class-Path&gt;
						&lt;/manifestEntries&gt;
					&lt;/archive&gt;
				&lt;/configuration&gt;
			&lt;/plugin&gt;
			&lt;plugin&gt;
				&lt;groupId&gt;org.apache.maven.plugins&lt;/groupId&gt;
				&lt;artifactId&gt;maven-dependency-plugin&lt;/artifactId&gt;
				&lt;executions&gt;
					&lt;execution&gt;
						&lt;id&gt;copy-dependencies&lt;/id&gt;
						&lt;phase&gt;package&lt;/phase&gt;
						&lt;goals&gt;
							&lt;goal&gt;copy-dependencies&lt;/goal&gt;
						&lt;/goals&gt;
						&lt;configuration&gt;
							&lt;type&gt;jar&lt;/type&gt;
							&lt;includeTypes&gt;jar&lt;/includeTypes&gt;
							&lt;useUniqueVersions&gt;false&lt;/useUniqueVersions&gt;
							&lt;outputDirectory&gt;
								${project.build.directory}/lib
							&lt;/outputDirectory&gt;
						&lt;/configuration&gt;
					&lt;/execution&gt;
				&lt;/executions&gt;
			&lt;/plugin&gt;
			
			 &lt;plugin&gt;
                &lt;groupId&gt;org.apache.maven.plugins&lt;/groupId&gt;
                &lt;artifactId&gt;maven-compiler-plugin&lt;/artifactId&gt;
                &lt;version&gt;3.1&lt;/version&gt;
                &lt;configuration&gt;
                    &lt;source&gt;1.7&lt;/source&gt;
                    &lt;target&gt;1.7&lt;/target&gt;
                &lt;/configuration&gt;
            &lt;/plugin&gt;
			
		&lt;/plugins&gt;

&lt;/build&gt;
&lt;/project&gt;
</code></pre>
<h3>三、测试</h3>
<p>在Kafka所在服务器命令行输入：</p>
<p></p>
<pre><code class="language-plain"> kafka-console-consumer.sh --zookeeper localhost:2181 --topic test --from-beginning </code></pre>监听日志输出。
<p></p>
<p>此时我们运行RogueApplication类，可以看到打印出如下日志：</p>
<p></p>
<pre><code class="language-plain">[root@liuyazhuang121 local]# kafka-console-consumer.sh --zookeeper localhost:2181 --topic test --from-beginning  
test
abcmytest
abcmytest
abc你好
abc你好
This is a warning (slow state).
This is a warning (slow state).
This is a warning (slow state).
This is a warning (slow state).
This is a warning (slow state).
This is a warning (slow state).
This is a warning (rapid state).
This is a warning (rapid state).
This is a warning (rapid state).
This is a warning (rapid state).
This is a warning (rapid state).
This is a warning (rapid state).
This is a warning (slow state).
This is a warning (slow state).
This is a warning (slow state).
This is a warning (slow state).</code></pre>如下图：
<p></p>
<p><img src="https://img-blog.csdn.net/20180123100455369?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbDEwMjgzODY4MDQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br></p>
<h3>四、温馨提示</h3>
<p><span style="font-size:18px;">大家可以到链接<a href="http://download.csdn.net/download/l1028386804/10219010" rel="nofollow">http://download.csdn.net/download/l1028386804/10219010</a>下载Kafka扩展logback将日志输出到Kafka实例源码</span><br></p>
<p><br></p>
            </div>
                </div>