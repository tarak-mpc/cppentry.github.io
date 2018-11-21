---
layout:     post
title:      flume使用之httpSource
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/liuxiao723846/article/details/78131732				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>flume自带很长多的source，如：exe、kafka...其中有一个非常简单的source——httpsource，使用httpSource，flume启动后会拉起一个web服务来监听指定的ip和port。常用的使用场景：对于有些应用环境中，不能部署Flume SDK及其依赖项，可以在代码中通过HTTP而不是Flume的PRC发送数据的情况，此时HTTP SOURCE可以用来将数据接收到Flume中。</p>

<p>1、httpsource 参数：</p>

<p> </p>

<table><tbody><tr><td>配置参数</td>
			<td>默认值</td>
			<td>描述</td>
		</tr><tr><td>type</td>
			<td> </td>
			<td>http (org.apache.fluem.source.httpSource）</td>
		</tr><tr><td>bind</td>
			<td> </td>
			<td>绑定的IP地址或主机名</td>
		</tr><tr><td>port</td>
			<td> </td>
			<td>绑定的端口号</td>
		</tr><tr><td>enableSSL</td>
			<td>false</td>
			<td> </td>
		</tr><tr><td>keystore</td>
			<td> </td>
			<td>使用的keystore文件的路径</td>
		</tr><tr><td>keystorePassword</td>
			<td> </td>
			<td>能够进入keystore的密码</td>
		</tr><tr><td>handler</td>
			<td>JSONHandler</td>
			<td>HTTP SOURCE使用的处理程序类</td>
		</tr><tr><td>handler.*</td>
			<td> </td>
			<td>传给处理程序类的任何参数 可以 通过使用此参数（*）配置传入</td>
		</tr></tbody></table><p>1）handler：</p>

<p>Flume使用一个可插拔的“handler”程序来实现转换，如果不指定默认是：JSONHandler，它能处理JSON格式的事件，格式如下。此外用户可以自定义handler，必须实现HTTPSourceHandler接口。<br><br>
json数据格式：</p>

<p> </p>

<pre class="has">
<code class="language-html">[ { "headers":{"":"","":""
                 },
     "body":"the first event"
   },
   { "headers":{"":"","":""
                 },
     "body":"the second event"
   }
   
]</code></pre>

<p><br>
2、简单介绍一下flume的logger sink：</p>

<p> </p>

<p>记录INFO级别的日志，一般用于调试。本文将使用这种类型的sink，配置的属性：</p>

<ul><li>type  logger</li>
	<li>maxBytesToLog    16    Maximum number of bytes of the Event body to log</li>
</ul><p>注意：要求必须在 --conf 参数指定的目录下有 log4j的配置文件，可以通过-Dflume.root.logger=INFO,console在命令启动时手动指定log4j参数。</p>

<p> </p>

<p> </p>

<p>3、简单的httpSource实例：</p>

<p>1）下载flume、解压：</p>

<p> </p>

<pre class="has">
<code class="language-html">cd /usr/local/
wget http://mirror.bit.edu.cn/apache/flume/1.7.0/apache-flume-1.7.0-bin.tar.gz
tar -xvzf apache-flume-1.7.9-bin.tar.gz</code></pre>

<p> </p>

<p>配置flume的环境变量：</p>

<p> </p>

<pre class="has">
<code class="language-html">vim /etc/profile

export PS1="[\u@`/sbin/ifconfig eth0|grep 'inet '|awk -F'[: ]+' '{print $4}'` \W]"'$ '
export FLUME_HOME=/usr/local/apache-flume-1.6.0-bin
export PATH=$PATH:$FLUME_HOME/bin
</code></pre>

<p> </p>

<p><br>
2）安装jdk、配置环境变量；</p>

<p>3）配置flume：</p>

<p> </p>

<pre class="has">
<code class="language-html">cd /usr/local/flume/conf
vim flume-env.sh</code></pre>

<p>指定java_home，同时放入如下log4j.properties</p>

<p> </p>

<p> </p>

<pre class="has">
<code class="language-html">### set log levels ###
log4j.rootLogger = info,stdout ,  D ,  E

###
log4j.appender.stdout = org.apache.log4j.ConsoleAppender
log4j.appender.stdout.Target = System.out
log4j.appender.stdout.layout = org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern =  [%d{MM-dd HH:mm:ss}] [%p] [%c:%L] %m%n
 
### 输出到日志文件 ###
log4j.appender.D = org.apache.log4j.DailyRollingFileAppender
log4j.appender.D.File = /data/logs/flume/flume.log
log4j.appender.D.Append = true
log4j.appender.D.Threshold = info
log4j.appender.D.layout = org.apache.log4j.PatternLayout
log4j.appender.D.layout.ConversionPattern = [%d{MM-dd HH:mm:ss}] [%p] [%c:%L] %m%n
 
### 保存异常信息到单独文件 ###
log4j.appender.E = org.apache.log4j.DailyRollingFileAppender
log4j.appender.E.File =/data/logs/flume/flume_error.log
log4j.appender.E.Append = true
log4j.appender.E.Threshold = ERROR
log4j.appender.E.layout = org.apache.log4j.PatternLayout
log4j.appender.E.layout.ConversionPattern = [%d{MM-dd HH:mm:ss}] [%p] [%c:%L] %m%n

### sink
log4j.logger.com.abc.ttbrain.log.flume.sink.MysqlSink= INFO, F, EE
log4j.additivity.com.abc.ttbrain.log.flume.sink.MysqlSink = false
log4j.appender.F= org.apache.log4j.DailyRollingFileAppender
log4j.appender.F.File=/data/logs/flume/flume_sink.log
log4j.appender.F.Append = true
log4j.appender.F.Threshold = info
log4j.appender.F.layout=org.apache.log4j.PatternLayout  
log4j.appender.F.layout.ConversionPattern= [%d{MM-dd HH:mm:ss}] [%p] [%c:%L] %m%n

log4j.appender.EE= org.apache.log4j.DailyRollingFileAppender
log4j.appender.EE.File=/data/logs/flume/flume_sink_error.log
log4j.appender.EE.Append = true
log4j.appender.EE.Threshold = ERROR
log4j.appender.EE.layout=org.apache.log4j.PatternLayout  
log4j.appender.EE.layout.ConversionPattern= [%d{MM-dd HH:mm:ss}] [%p] [%c:%L] %m%n</code></pre>

<p> </p>

<p> </p>

<p> </p>

<p>4）配置httpSource：</p>

<p> </p>

<pre class="has">
<code class="language-html">cd /usr/local/flume/conf
vim http_test.conf

a1.sources=r1
a1.sinks=k1
a1.channels=c1

a1.sources.r1.type=http
a1.sources.r1.bind=localhost
a1.sources.r1.port=50000
a1.sources.r1.channels=c1

a1.sinks.k1.type=logger
a1.sinks.k1.channel=c1

a1.channels.c1.type=memory
a1.channels.c1.capacity=1000
a1.channels.c1.transactionCapacity=100
</code></pre>

<p><br>
5）启动flume：</p>

<p> </p>

<p> </p>

<pre class="has">
<code class="language-html">flume-ng agent -c /usr/local/flume/conf/ -f /usr/local/flume/conf/http_test.conf -n a1
</code></pre>

<p><br>
6）测试：</p>

<p> </p>

<p>开一个shell窗口，输入命令：</p>

<p> </p>

<pre class="has">
<code class="language-html">curl -X POST -d'[{"headers":{"h1":"v1","h2":"v2"},"body":"hello body"}]'  http://localhost:50000</code></pre>

<p> </p>

<p>在/data/log/flume/flume.log 文件中可以看到：</p>

<p> </p>

<pre class="has">
<code class="language-html">[09-29 10:31:12] [INFO] [org.apache.flume.sink.LoggerSink:94] Event: { headers:{h1=v1, h2=v2} body: 68 65 6C 6C 6F 20 62 6F 64 79                   hello body }
</code></pre>

<p><br>
4、自定义handler：</p>

<p> </p>

<p>假定xml请求格式，期望格式如下：</p>

<p> </p>

<pre class="has">
<code class="language-html">&lt;events&gt;
 &lt;event&gt;
     &lt;headers&gt;&lt;header1&gt;value1&lt;/header1&gt;&lt;/headers&gt;
     &lt;body&gt;test&lt;/body&gt;
 &lt;/event&gt;
 &lt;event&gt;
    &lt;headers&gt;&lt;header1&gt;value1&lt;/header1&gt;&lt;/headers&gt;
    &lt;body&gt;test2&lt;/body&gt;
  &lt;/event&gt;
 &lt;/events&gt;</code></pre>

<p><br>
1）pom.xml</p>

<p> </p>

<p> </p>

<pre class="has">
<code class="language-html">&lt;project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd"&gt;
  &lt;modelVersion&gt;4.0.0&lt;/modelVersion&gt;
  &lt;groupId&gt;org.pq&lt;/groupId&gt;
  &lt;artifactId&gt;flume-demo&lt;/artifactId&gt;
  &lt;packaging&gt;jar&lt;/packaging&gt;
  &lt;version&gt;1.0&lt;/version&gt;
  &lt;name&gt;flume-demo Maven jar&lt;/name&gt;
  &lt;url&gt;http://maven.apache.org&lt;/url&gt;
  &lt;dependencies&gt;
    &lt;dependency&gt;
      &lt;groupId&gt;junit&lt;/groupId&gt;
      &lt;artifactId&gt;junit&lt;/artifactId&gt;
      &lt;version&gt;4.8.2&lt;/version&gt;
      &lt;scope&gt;test&lt;/scope&gt;
    &lt;/dependency&gt;
    &lt;dependency&gt;
      &lt;groupId&gt;org.slf4j&lt;/groupId&gt;
      &lt;artifactId&gt;slf4j-log4j12&lt;/artifactId&gt;
      &lt;version&gt;1.7.7&lt;/version&gt;
      &lt;scope&gt;compile&lt;/scope&gt;
    &lt;/dependency&gt;
    &lt;dependency&gt;
      &lt;groupId&gt;org.apache.flume&lt;/groupId&gt;
      &lt;artifactId&gt;flume-ng-core&lt;/artifactId&gt;
      &lt;version&gt;1.6.0&lt;/version&gt;
      &lt;scope&gt;compile&lt;/scope&gt;
    &lt;/dependency&gt;
  &lt;/dependencies&gt;
  &lt;build&gt;
    &lt;finalName&gt;flume-demo&lt;/finalName&gt;
  &lt;/build&gt;
&lt;/project&gt;</code></pre>

<p>2）自定义handler：</p>

<p> </p>

<p> </p>

<pre class="has">
<code class="language-html">package org.pq.flumeDemo.sources;
import com.google.common.base.Preconditions;
import org.apache.flume.Context;
import org.apache.flume.Event;
import org.apache.flume.event.EventBuilder;
import org.apache.flume.source.http.HTTPBadRequestException;
import org.apache.flume.source.http.HTTPSourceHandler;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.w3c.dom.Document;
import org.w3c.dom.Element;
import org.w3c.dom.Node;
import org.w3c.dom.NodeList;
import org.xml.sax.SAXException;

import javax.servlet.http.HttpServletRequest;
import javax.xml.parsers.DocumentBuilder;
import javax.xml.parsers.DocumentBuilderFactory;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

public class HTTPSourceXMLHandler implements HTTPSourceHandler {
    private final String ROOT = "events";
    private final String EVENT_TAG = "event";
    private final String HEADERS_TAG = "headers";
    private final String BODY_TAG = "body";

    private final String CONF_INSERT_TIMESTAMP = "insertTimestamp";
    private final String TIMESTAMP_HEADER = "timestamp";
    private final DocumentBuilderFactory documentBuilderFactory
            = DocumentBuilderFactory.newInstance();

    // Document builders are not thread-safe.
    // So make sure we have one for each thread.
    private final ThreadLocal&lt;DocumentBuilder&gt; docBuilder
            = new ThreadLocal&lt;DocumentBuilder&gt;();

    private boolean insertTimestamp;
    private static final Logger LOG = LoggerFactory.getLogger(HTTPSourceXMLHandler.class);


    public List&lt;Event&gt; getEvents(HttpServletRequest httpServletRequest) throws HTTPBadRequestException, Exception {
        if (docBuilder.get() == null) {
            docBuilder.set(documentBuilderFactory.newDocumentBuilder());
        }
        Document doc;
        final List&lt;Event&gt; events;
        try {
            doc = docBuilder.get().parse(httpServletRequest.getInputStream());            
            Element root = doc.getDocumentElement();        

            root.normalize();
            // Verify that the root element is "events"
            Preconditions.checkState(
                    ROOT.equalsIgnoreCase(root.getTagName()));

            NodeList nodes = root.getElementsByTagName(EVENT_TAG);
            LOG.info("get nodes={}",nodes);

            int eventCount = nodes.getLength();
            events = new ArrayList&lt;Event&gt;(eventCount);
            for (int i = 0; i &lt; eventCount; i++) {
                Element event = (Element) nodes.item(i);
                // Get all headers. If there are multiple header sections,
                // combine them.
                NodeList headerNodes
                        = event.getElementsByTagName(HEADERS_TAG);
                Map&lt;String, String&gt; eventHeaders
                        = new HashMap&lt;String, String&gt;();
                for (int j = 0; j &lt; headerNodes.getLength(); j++) {
                    Node headerNode = headerNodes.item(j);
                    NodeList headers = headerNode.getChildNodes();
                    for (int k = 0; k &lt; headers.getLength(); k++) {
                        Node header = headers.item(k);

                        // Read only element nodes
                        if (header.getNodeType() != Node.ELEMENT_NODE) {
                            continue;
                        }
                        // Make sure a header is inserted only once,
                        // else the event is malformed
                        Preconditions.checkState(
                                !eventHeaders.containsKey(header.getNodeName()),
                                "Header expected only once " + header.getNodeName());
                        eventHeaders.put(
                                header.getNodeName(), header.getTextContent());
                    }
                }
                Node body = event.getElementsByTagName(BODY_TAG).item(0);
                if (insertTimestamp) {
                    eventHeaders.put(TIMESTAMP_HEADER, String.valueOf(System
                            .currentTimeMillis()));
                }
                events.add(EventBuilder.withBody(
                        body.getTextContent().getBytes(
                                httpServletRequest.getCharacterEncoding()),
                        eventHeaders));
            }
        } catch (SAXException ex) {
            throw new HTTPBadRequestException(
                    "Request could not be parsed into valid XML", ex);
        } catch (Exception ex) {
            throw new HTTPBadRequestException(
                    "Request is not in expected format. " +
                            "Please refer documentation for expected format.", ex);
        }
        return events;
    }

    public void configure(Context context) {
        insertTimestamp = context.getBoolean(CONF_INSERT_TIMESTAMP,
                false);
    }
}</code></pre>

<p>打包成dependency，然后放到flume的lib下。</p>

<p> </p>

<p>3）flume配置文件：</p>

<p> </p>

<pre class="has">
<code class="language-html">a1.sources=r1
a1.sinks=k1
a1.channels=c1

a1.sources.r1.type=http
a1.sources.r1.bind=localhost
a1.sources.r1.port=50000
a1.sources.r1.channels=c1
a1.sources.r1.handler=org.pq.flumeDemo.sources.HTTPSourceXMLHandler
a1.sources.r1.insertTimestamp=true

a1.sinks.k1.type=logger
a1.sinks.k1.channel=c1

a1.channels.c1.type=memory
a1.channels.c1.capacity=1000
a1.channels.c1.transactionCapacity=100</code></pre>

<p>4）启动：</p>

<p> </p>

<p> </p>

<pre class="has">
<code class="language-html">$ bin/flume-ng agent -c conf -f conf/http_test.conf  -n a1 -Dflume.root.logger=INFO,console</code></pre>

<p> </p>

<p> </p>

<p> </p>

<p> </p>

<p> </p>            </div>
                </div>