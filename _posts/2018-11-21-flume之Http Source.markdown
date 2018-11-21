---
layout:     post
title:      flume之Http Source
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h1 id="h1_0">一、介绍</h1>
<ol><li>
<p>flume自带的Http Source可以通过Http Post接收事件。</p>
</li><li>
<p>场景：对于有些应用程序环境，它可能不能部署Flume SDK及其依赖项，或客户端代码倾向于通过HTTP而不是Flume的PRC发送数据的情况，此时HTTP SOURCE可以用来将数据接收到Flume中。</p>
</li><li>
<p>从客户端的角度看，HTTP SOURCE表现的像web服务器一样能接收flume事件</p>
</li></ol><div><span style="font-family:'Pingfang SC', STHeiti, 'Lantinghei SC', 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', 'WenQuanYi Micro Hei', SimSun, sans-serif;color:#3d464d;"><span style="font-size:16px;"></span></span>
<h1 id="h1_1">二、参数</h1>
<table><tbody><tr><td>配置参数</td>
<td>默认值</td>
<td>描述</td>
</tr><tr><td>type</td>
<td><br></td>
<td>http (org.apache.fluem.source.httpSource）</td>
</tr><tr><td>bind</td>
<td><br></td>
<td>绑定的IP地址或主机名</td>
</tr><tr><td>port</td>
<td><br></td>
<td>绑定的端口号</td>
</tr><tr><td>enableSSL</td>
<td>false</td>
<td><br></td>
</tr><tr><td>keystore</td>
<td><br></td>
<td>使用的keystore文件的路径</td>
</tr><tr><td>keystorePassword</td>
<td><br></td>
<td>能够进入keystore的密码</td>
</tr><tr><td>handler</td>
<td>JSONHandler</td>
<td>HTTP SOURCE使用的处理程序类</td>
</tr><tr><td>handler.*</td>
<td><br></td>
<td>传给处理程序类的任何参数 可以 通过使用此参数（*）配置传入</td>
</tr></tbody></table><ol><li>
<p>为了安全传输，http source也支持SSL，SSL支持的相关例子可以参见<a href="http://my.oschina.net/pengqiang/blog/535365" rel="nofollow">我的关于flume之Avro Source博客</a></p>
</li><li>
<p>Flume 事件使用一个可插拔的“handler”程序来实现转换，它必须实现的<span>HTTPSourceHandler</span>接口。此处理程序需要一个HttpServletRequest和返回一个flume 事件列表。默认是：JSONHandler。</p>
<p>例如：xxx.handler=org.pq.demo.HTTPSourceXmlHandler</p>
</li><li>
<p>自定义的handler如果想传入参数，可以使用handler.*配置</p>
<p>如：xxx.handler.myparam=zhangsan</p>
</li><li>
<p>如果配置中没有指定处理程序，HTTP SOURCE将使用与Flume绑定的处理程序，即:<span>JSONHandler</span>,它能处理JSON格式的事件。每个事件可以包含包装为数组的几个事件，尽管Source写入的管道可能有限制的事务能力。</p>
<p>处理程序接受UTF-8,UTF-16,UTF-32编码的JSON格式的数据，并且将它转换成一个列表的事件。</p>
<p>格式：</p>
<table><tbody><tr><td>[ { "headers":{"":"","":""<br>
                 },<br>
     "body":"the first event"<br>
   },<br>
   { "headers":{"":"","":""<br>
                 },<br>
     "body":"the second event"<br>
   }<br>
   <br>
]</td>
</tr></tbody></table></li></ol><span id="OSC_h1_3"></span><span style="color:rgb(61,70,77);font-family:'Pingfang SC', STHeiti, 'Lantinghei SC', 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', 'WenQuanYi Micro Hei', SimSun, sans-serif;font-size:16px;background-color:rgb(248,248,248);"></span>
<h1 id="h1_2">三、应用</h1>
<span id="OSC_h2_4"></span><span style="color:rgb(61,70,77);font-family:'Pingfang SC', STHeiti, 'Lantinghei SC', 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', 'WenQuanYi Micro Hei', SimSun, sans-serif;font-size:16px;background-color:rgb(248,248,248);"></span>
<h2 id="h2_3">1.http source简单例子</h2>
<p>1）在conf文件下添加http_test.conf文件</p>
<p></p><pre><code class="language-html">a1.sources=r1
 a1.sinks=k1
 a1.channels=c1
 
 a1.sources.r1.type=http
 a1.sources.r1.bind=192.168.1.102
 a1.sources.r1.port=50000
 a1.soces.r1.channels=c1
  
 a1.sinks.k1.type=logger
 a1.sinks.k1.channel=c1
 
 a1.channels.c1.type=memory
 a1.channels.c1.cap</code></pre><br><p>2)启动服务：<br></p>
<p>    $ <span>bin/flume-ng agent -c conf -f conf/http_test.conf  -n a1 -Dflume.root.logger=INFO,console</span></p>
<p>3) 测试：</p>
<p>   $ <span>curl -X POST -d'[{"headers":{"h1":"v1","h2":"v2"},"body":"hello body"}]'  http://192.168.1.102:50000</span></p>
<span style="font-family:'Pingfang SC', STHeiti, 'Lantinghei SC', 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', 'WenQuanYi Micro Hei', SimSun, sans-serif;font-size:16px;background-color:rgb(248,248,248);">4) 服务器端结果</span><br><p><span style="font-family:'Pingfang SC', STHeiti, 'Lantinghei SC', 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', 'WenQuanYi Micro Hei', SimSun, sans-serif;font-size:16px;background-color:rgb(248,248,248);"></span></p><pre><code class="language-html">......
2015-11-30 11:34:52,451 (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org.apache.flume.sink.LoggerSink.process(LoggerSink.java:94)] Event: { headers:{} body: }
2015-11-30 11:34:52,452 (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org.apache.flume.sink.LoggerSink.process(LoggerSink.java:94)] Event: { headers:{} body: }
2015-11-30 11:45:14,951 (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org.apache.flume.sink.LoggerSink.process(LoggerSink.java:94)] Event: { headers:{h1=v1, h2=v2} body: 68 65 6C 6C 6F 20 62 6F 64 79                   hello body }</code></pre><br><h2 id="h2_4">2.http source handler自定义例子</h2>
<p></p>
<p>假定xml请求格式，期望格式如下：</p>
<table><tbody><tr><td>&lt;events&gt;<br>
 &lt;event&gt;<br>
     &lt;headers&gt;&lt;header1&gt;value1&lt;/header1&gt;&lt;/headers&gt;<br>
     &lt;body&gt;test&lt;/body&gt;<br>
 &lt;/event&gt;<br>
 &lt;event&gt;<br>
    &lt;headers&gt;&lt;header1&gt;value1&lt;/header1&gt;&lt;/headers&gt;<br>
    &lt;body&gt;test2&lt;/body&gt;<br>
  &lt;/event&gt;<br>
 &lt;/events&gt;<br></td>
</tr></tbody></table><p>现在要求flume http source可以处理这种请求的xml格式</p>
<p>操作步骤如下：<br></p>
<p>1）建立maven工程，pom.xml文件如下</p>
<p></p><pre><code class="language-html">&lt;project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
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
&lt;/project&gt;</code></pre><span style="color:rgb(61,70,77);font-family:'Pingfang SC', STHeiti, 'Lantinghei SC', 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', 'WenQuanYi Micro Hei', SimSun, sans-serif;font-size:16px;background-color:rgb(248,248,248);">2）开发代码 ，自定义handler类</span>
<p></p><pre><code class="language-html">package org.pq.flumeDemo.sources;
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
<p>3）在该工程的flume-demo目录下执行命令mvn package,会将该工程打成jar包，会生产target目录，从中找到flume-demo.jar，将其拷贝到flume的lib目录下</p>
<p>4）flume配置文件：</p>
<p></p><pre><code class="language-html">a1.sources=r1
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
a1.channels.c1.transactionCapacity=100</code></pre><br><p>5）启动服务</p>
<p>   $ <span>bin/flume-ng agent -c conf -f conf/http_test.conf  -n a1 -Dflume.root.logger=INFO,console</span></p>
6）测试：
<p><img src="https://img-blog.csdn.net/20170318231650958?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGl1eGlhbzcyMzg0Ng==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p>7）结果：</p>
<p></p><pre><code class="language-html">.....
2015-12-02 14:35:53,809 (1214826834@qtp-1250857451-0) [INFO - org.pq.flumeDemo.sources.HTTPSourceXMLHan
dler.getEvents(HTTPSourceXMLHandler.java:64)] get nodes=org.apache.xerces.dom.DeepNodeListImpl@1beaed6
2015-12-02 14:35:54,490 (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org.apache.flume.sink.
LoggerSink.process(LoggerSink.java:94)] Event: { headers:{header1=value1} body: 74 65 73 74                  test }
2015-12-02 14:35:54,491 (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org.apache.flume.sink
.LoggerSink.process(LoggerSink.java:94)] Event: { headers:{header1=value1} body: 74 65 73 74 32    </code></pre>
<br></div>
            </div>
                </div>