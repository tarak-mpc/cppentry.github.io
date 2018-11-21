---
layout:     post
title:      基于Flume+Log4j+Kafka的日志采集架构方案
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p> </p>

<p>本文将会介绍如何使用 Flume、log4j、Kafka进行规范的日志采集。</p>

<p><img alt="" class="has" src="https://www.linuxidc.com/upload/2016_05/160516101069387.png"></p>

<h2>Flume 基本概念</h2>

<p>Flume是一个完善、强大的日志采集工具，关于它的配置，在网上有很多现成的例子和资料，这里仅做简单说明不再详细赘述。<br>
Flume包含Source、Channel、Sink三个最基本的概念：</p>

<p><img alt="" class="has" src="https://www.linuxidc.com/upload/2016_05/160516101069382.png"></p>

<p>Source——日志来源，其中包括：Avro Source、Thrift Source、Exec Source、JMS Source、Spooling Directory Source、Kafka Source、NetCat Source、Sequence Generator Source、Syslog Source、HTTP Source、Stress Source、Legacy Source、Custom Source、Scribe Source以及Twitter 1% firehose Source。</p>

<p>Channel——日志管道，所有从Source过来的日志数据都会以队列的形式存放在里面，它包括：Memory Channel、JDBC Channel、Kafka Channel、File Channel、Spillable Memory Channel、Pseudo Transaction Channel、Custom Channel。</p>

<p>Sink——日志出口，日志将通过Sink向外发射，它包括：HDFS Sink、Hive Sink、Logger Sink、Avro Sink、Thrift Sink、IRC Sink、File Roll Sink、Null Sink、HBase Sink、Async HBase Sink、Morphline Solr Sink、Elastic Search Sink、Kite Dataset Sink、Kafka Sink、Custom Sink。</p>

<p>基于Flume的日志采集是灵活的，我们可以看到既有Avro Sink也有Avro Source，既有Thrift Sink也有Thrift Source，这意味着我们可以将多个管道处理串联起来，如下图所示：</p>

<p><img alt="" class="has" src="https://www.linuxidc.com/upload/2016_05/160516101069383.png"></p>

<p>串联的意义在于，我们可以将多个管道合并到一个管道中最终输出到同一个Sink中去，如下图：</p>

<p><img alt="" class="has" src="https://www.linuxidc.com/upload/2016_05/160516101069384.png"></p>

<p>上面讲述了Source和Sink的作用，而Channel的作用在于处理不同的Sink，假设我们一个Source要对应多个Sink，则只需要为一个Source建立多个Channel即可，如下所示：</p>

<p><img alt="" class="has" src="https://www.linuxidc.com/upload/2016_05/160516101069385.png"></p>

<p>一个Source如果想要输出到多个Sink中去，就需要建立多个Channel进行介入并最终输出，通过上面这几张图，我们可以很好的理解Flume的运行机制，我们在这里也就点到为止，详细的配置可以在官网或者在网上搜索到、查看到。</p>

<p>一般情况下，我们使用 Exec Source对log文件进行监控，这样做确实是比较简单，但是并不方便，我们需要在每一台要监控的服务器上部署Flume，对运维来讲万一目标日志文件发生IO异常（例如格式改变、文件名改变、文件被锁），也是很痛苦的，因此我们最好能让日志直接通过Socket发送出去，而不是存放在本地，这样一来，不仅降低了目标服务器的磁盘占用，还能够有效的防止文件IO异常，而Kafka就是一个比较好的解决方案，具体的架构如下图所示：</p>

<p><img alt="" class="has" src="https://www.linuxidc.com/upload/2016_05/160516101069386.png" width="600"></p>

<p> </p>

<p>由上图可以看到，日志最终流向了两个地方：HBase Persistence和Realtime Processor，而至于为什么不用Kafka直接与Storm进行通信的原因是为了将Sotrm逻辑和日志源通过Flume进行隔离，在Storm中对日志进行简单的分析后，将结果扔进 Rabbit MQ 中供 WEB APP消费。</p>

<p>HBase Persistence就是将原始的日志记录在HBase中以便回档查询，而Realtime Processor则包含了实时的日志统计以及错误异常邮件提醒等功能。</p>

<p>为了能够准确的捕获到异常数据，我们还需要对程序进行一些规范化的改造，例如提供统一的异常处理句柄等等。</p>

<h2>日志输出格式</h2>

<p>既然打算要对日志进行统一处理，一个统一、规范的日志格式就是非常重要的，而我们以往使用的 PatternLayout 对于最终字段的切分非常的不方便，如下所示：</p>

<blockquote>
<p>2016-05-08 19:32:55,572 [INFO ] [main] - [com.banksteel.log.demo.log4j.Demo.main(Demo.java:13)] 输出信息……<br>
2016-05-08 19:32:55,766 [DEBUG] [main] - [com.banksteel.log.demo.log4j.Demo.main(Demo.java:15)] 调试信息……<br>
2016-05-08 19:32:55,775 [WARN ] [main] - [com.banksteel.log.demo.log4j.Demo.main(Demo.java:16)] 警告信息……<br>
2016-05-08 19:32:55,783 [ERROR] [main] - [com.banksteel.log.demo.log4j.Demo.main(Demo.java:20)] 处理业务逻辑的时候发生一个错误……<br>
java.lang.Exception: 错误消息啊<br>
at com.banksteel.log.demo.log4j.Demo.main(Demo.java:18)<br>
at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)<br>
at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:57)<br>
at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)<br>
at java.lang.reflect.Method.invoke(Method.java:606)<br>
at com.intellij.rt.execution.application.AppMain.main(AppMain.java:144)</p>
</blockquote>

<p>如何去解析这个日志，是个非常头疼的地方，万一某个系统的开发人员输出的日志不符合既定规范的 PatternLayout 就会引发异常。</p>

<p>为了能够一劳永逸的解决格式问题，我们采用 JsonLayout 就能很好的规范日志输出，例如LOG4J 2.X 版本中提供的 JsonLayout 输出的格式如下所示：</p>

<pre>
{
  "timeMillis" : 1462712870612,
  "thread" : "main",
  "level" : "FATAL",
  "loggerName" : "com.banksteel.log.demo.log4j2.Demo",
  "message" : "发生了一个可能会影响程序继续运行下去的异常！",
  "thrown" : {
    "commonElementCount" : 0,
    "localizedMessage" : "错误消息啊",
    "message" : "错误消息啊",
    "name" : "java.lang.Exception",
    "extendedStackTrace" : [ {
      "class" : "com.banksteel.log.demo.log4j2.Demo",
      "method" : "main",
      "file" : "Demo.java",
      "line" : 20,
      "exact" : true,
      "location" : "classes/",
      "version" : "?"
    }, {
      "class" : "sun.reflect.NativeMethodAccessorImpl",
      "method" : "invoke0",
      "file" : "NativeMethodAccessorImpl.java",
      "line" : -2,
      "exact" : false,
      "location" : "?",
      "version" : "1.7.0_80"
    }, {
      "class" : "sun.reflect.NativeMethodAccessorImpl",
      "method" : "invoke",
      "file" : "NativeMethodAccessorImpl.java",
      "line" : 57,
      "exact" : false,
      "location" : "?",
      "version" : "1.7.0_80"
    }, {
      "class" : "sun.reflect.DelegatingMethodAccessorImpl",
      "method" : "invoke",
      "file" : "DelegatingMethodAccessorImpl.java",
      "line" : 43,
      "exact" : false,
      "location" : "?",
      "version" : "1.7.0_80"
    }, {
      "class" : "java.lang.reflect.Method",
      "method" : "invoke",
      "file" : "Method.java",
      "line" : 606,
      "exact" : false,
      "location" : "?",
      "version" : "1.7.0_80"
    }, {
      "class" : "com.intellij.rt.execution.application.AppMain",
      "method" : "main",
      "file" : "AppMain.java",
      "line" : 144,
      "exact" : true,
      "location" : "idea_rt.jar",
      "version" : "?"
    } ]
  },
  "endOfBatch" : false,
  "loggerFqcn" : "org.apache.logging.log4j.spi.AbstractLogger",
  "source" : {
    "class" : "com.banksteel.log.demo.log4j2.Demo",
    "method" : "main",
    "file" : "Demo.java",
    "line" : 23
  }
} </pre>

<p>我们看到，这种格式，无论用什么语言都能轻松解析了。</p>

<h2>日志框架的Kafka集成</h2>

<p>我们这里只用log4j 1.x 和 log4j 2.x 进行示例。</p>

<h3>log4j 1.x 与 Kafka 集成</h3>

<p>首先POM.xml的内容如下：</p>

<pre>
&lt;dependencies&gt;
    &lt;dependency&gt;
        &lt;groupId&gt;log4j&lt;/groupId&gt;
        &lt;artifactId&gt;log4j&lt;/artifactId&gt;
        &lt;version&gt;1.2.17&lt;/version&gt;
    &lt;/dependency&gt;
    &lt;dependency&gt;
        &lt;groupId&gt;com.fasterxml.jackson.core&lt;/groupId&gt;
        &lt;artifactId&gt;jackson-core&lt;/artifactId&gt;
        &lt;version&gt;2.7.4&lt;/version&gt;
    &lt;/dependency&gt;
    &lt;dependency&gt;
        &lt;groupId&gt;com.fasterxml.jackson.core&lt;/groupId&gt;
        &lt;artifactId&gt;jackson-databind&lt;/artifactId&gt;
        &lt;version&gt;2.7.4&lt;/version&gt;
    &lt;/dependency&gt;
    &lt;dependency&gt;
        &lt;groupId&gt;com.fasterxml.jackson.core&lt;/groupId&gt;
        &lt;artifactId&gt;jackson-annotations&lt;/artifactId&gt;
        &lt;version&gt;2.7.4&lt;/version&gt;
    &lt;/dependency&gt;
    &lt;dependency&gt;
        &lt;groupId&gt;org.apache.kafka&lt;/groupId&gt;
        &lt;artifactId&gt;kafka-clients&lt;/artifactId&gt;
        &lt;version&gt;0.8.2.1&lt;/version&gt;
    &lt;/dependency&gt;
    &lt;dependency&gt;
        &lt;groupId&gt;org.apache.kafka&lt;/groupId&gt;
        &lt;artifactId&gt;kafka_2.11&lt;/artifactId&gt;
        &lt;version&gt;0.8.2.1&lt;/version&gt;
    &lt;/dependency&gt;
&lt;/dependencies&gt; </pre>

<p>注意，我们这里使用的Kafka版本号是0.8.2.1，但是对应0.9.0.1是可以使用的并且0.9.0.1也只能用0.8.2.1才不会发生异常（具体异常可以自己尝试一下）。</p>

<p>而log4j 1.x 本身是没有 JsonLayout 可用的，因此我们需要自己实现一个类，如下所示：</p>

<pre>
package com.banksteel.log.demo.log4j;

import com.fasterxml.jackson.core.JsonProcessingException;
import com.fasterxml.jackson.databind.ObjectMapper;
import org.apache.log4j.Layout;
import org.apache.log4j.spi.LoggingEvent;

import java.util.LinkedHashMap;
import java.util.LinkedList;
import java.util.List;
import java.util.Map;

/**
 * 扩展Log4j 1.x，使其支持 JsonLayout，与 log4j2.x 一样是基于Jackson进行解析，其格式也是完全参考 Log4J 2.x实现的。
 *
 * @author 热血BUG男
 * @version 1.0.0
 * @since Created by gebug on 2016/5/8.
 */
public class JsonLayout extends Layout {

    private final ObjectMapper mapper = new ObjectMapper();

    public String format(LoggingEvent loggingEvent) {
        String json;
        Map&lt;String, Object&gt; map = new LinkedHashMap&lt;String, Object&gt;(0);
        Map&lt;String, Object&gt; source = new LinkedHashMap&lt;String, Object&gt;(0);
        source.put("method", loggingEvent.getLocationInformation().getMethodName());
        source.put("class", loggingEvent.getLocationInformation().getClassName());
        source.put("file", loggingEvent.getLocationInformation().getFileName());
        source.put("line", safeParse(loggingEvent.getLocationInformation().getLineNumber()));

        map.put("timeMillis", loggingEvent.getTimeStamp());
        map.put("thread", loggingEvent.getThreadName());
        map.put("level", loggingEvent.getLevel().toString());
        map.put("loggerName", loggingEvent.getLocationInformation().getClassName());
        map.put("source", source);
        map.put("endOfBatch", false);
        map.put("loggerFqcn", loggingEvent.getFQNOfLoggerClass());


        map.put("message", safeToString(loggingEvent.getMessage()));
        map.put("thrown", formatThrowable(loggingEvent));
        try {
            json = mapper.writeValueAsString(map);
        } catch (JsonProcessingException e) {
            return e.getMessage();
        }
        return json;
    }

    private List&lt;Map&lt;String, Object&gt;&gt; formatThrowable(LoggingEvent le) {
        if (le.getThrowableInformation() == null ||
                le.getThrowableInformation().getThrowable() == null)
            return null;
        List&lt;Map&lt;String, Object&gt;&gt; traces = new LinkedList&lt;Map&lt;String, Object&gt;&gt;();
        Map&lt;String, Object&gt; throwableMap = new LinkedHashMap&lt;String, Object&gt;(0);
        StackTraceElement[] stackTraceElements = le.getThrowableInformation().getThrowable().getStackTrace();
        for (StackTraceElement stackTraceElement : stackTraceElements) {
            throwableMap.put("class", stackTraceElement.getClassName());
            throwableMap.put("file", stackTraceElement.getFileName());
            throwableMap.put("line", stackTraceElement.getLineNumber());
            throwableMap.put("method", stackTraceElement.getMethodName());
            throwableMap.put("location", "?");
            throwableMap.put("version", "?");
            traces.add(throwableMap);
        }
        return traces;
    }

    private static String safeToString(Object obj) {
        if (obj == null) return null;
        try {
            return obj.toString();
        } catch (Throwable t) {
            return "Error getting message: " + t.getMessage();
        }
    }

    private static Integer safeParse(String obj) {
        try {
            return Integer.parseInt(obj.toString());
        } catch (NumberFormatException t) {
            return null;
        }
    }

    public boolean ignoresThrowable() {
        return false;
    }

    public void activateOptions() {

    }
}</pre>

<p>其实并不复杂，注意其中有一些获取不到的信息，用？代替了，保留字段的目的在于与log4j 2.x 的日志格式完全一致，配置log4j.properties如下对接 Kafka：</p>

<pre>
log4j.rootLogger=INFO,console
log4j.logger.com.banksteel.log.demo.log4j=DEBUG,kafka
log4j.appender.kafka=kafka.producer.KafkaLog4jAppender
log4j.appender.kafka.topic=server_log
log4j.appender.kafka.brokerList=Kafka-01:9092,Kafka-02:9092,Kafka-03:9092
log4j.appender.kafka.compressionType=none
log4j.appender.kafka.syncSend=true
log4j.appender.kafka.layout=com.banksteel.log.demo.log4j.JsonLayout

# appender console
log4j.appender.console=org.apache.log4j.ConsoleAppender
log4j.appender.console.target=System.out
log4j.appender.console.layout=org.apache.log4j.PatternLayout
log4j.appender.console.layout.ConversionPattern=%d [%-5p] [%t] - [%l] %m%n </pre>

<p>通过打印日志我们可以看到其输出的最终格式如下：</p>

<pre>
{
  "timeMillis": 1462713132695,
  "thread": "main",
  "level": "ERROR",
  "loggerName": "com.banksteel.log.demo.log4j.Demo",
  "source": {
    "method": "main",
    "class": "com.banksteel.log.demo.log4j.Demo",
    "file": "Demo.java",
    "line": 20
  },
  "endOfBatch": false,
  "loggerFqcn": "org.slf4j.impl.Log4jLoggerAdapter",
  "message": "处理业务逻辑的时候发生一个错误……",
  "thrown": [
    {
      "class": "com.intellij.rt.execution.application.AppMain",
      "file": "AppMain.java",
      "line": 144,
      "method": "main",
      "location": "?",
      "version": "?"
    },
    {
      "class": "com.intellij.rt.execution.application.AppMain",
      "file": "AppMain.java",
      "line": 144,
      "method": "main",
      "location": "?",
      "version": "?"
    },
    {
      "class": "com.intellij.rt.execution.application.AppMain",
      "file": "AppMain.java",
      "line": 144,
      "method": "main",
      "location": "?",
      "version": "?"
    },
    {
      "class": "com.intellij.rt.execution.application.AppMain",
      "file": "AppMain.java",
      "line": 144,
      "method": "main",
      "location": "?",
      "version": "?"
    },
    {
      "class": "com.intellij.rt.execution.application.AppMain",
      "file": "AppMain.java",
      "line": 144,
      "method": "main",
      "location": "?",
      "version": "?"
    },
    {
      "class": "com.intellij.rt.execution.application.AppMain",
      "file": "AppMain.java",
      "line": 144,
      "method": "main",
      "location": "?",
      "version": "?"
    }
  ]
}</pre>

<p><a><img alt="复制代码" class="has" src="https://www.linuxidc.com/upload/2016_05/160516101069381.gif"></a></p>

<p>测试类：</p>

<p><a><img alt="复制代码" class="has" src="https://www.linuxidc.com/upload/2016_05/160516101069381.gif"></a></p>

<pre>
package com.banksteel.log.demo.log4j;


import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

/**
 * @author 热血BUG男
 * @version 1.0.0
 * @since Created by gebug on 2016/5/8.
 */
public class Demo {
    private static final Logger logger = LoggerFactory.getLogger(Demo.class);

    public static void main(String[] args) {
        logger.info("输出信息……");
        logger.trace("随意打印……");
        logger.debug("调试信息……");
        logger.warn("警告信息……");
        try {
            throw new Exception("错误消息啊");
        } catch (Exception e) {
            logger.error("处理业务逻辑的时候发生一个错误……", e);
        }
    }
} </pre>

<h3>log4j 2.x 与 Kafka 集成</h3>

<p>log4j 2.x 天生支持 JsonLayout，并且与 Kafka 集成方便，我们只需要按部就班的配置一下就好了，POM.xml如下：</p>

<pre>
&lt;dependencies&gt;
  &lt;dependency&gt;
      &lt;groupId&gt;org.apache.logging.log4j&lt;/groupId&gt;
      &lt;artifactId&gt;log4j-api&lt;/artifactId&gt;
      &lt;version&gt;2.5&lt;/version&gt;
  &lt;/dependency&gt;
  &lt;dependency&gt;
      &lt;groupId&gt;org.apache.logging.log4j&lt;/groupId&gt;
      &lt;artifactId&gt;log4j-core&lt;/artifactId&gt;
      &lt;version&gt;2.5&lt;/version&gt;
  &lt;/dependency&gt;
  &lt;dependency&gt;
      &lt;groupId&gt;com.fasterxml.jackson.core&lt;/groupId&gt;
      &lt;artifactId&gt;jackson-core&lt;/artifactId&gt;
      &lt;version&gt;2.7.4&lt;/version&gt;
  &lt;/dependency&gt;
  &lt;dependency&gt;
      &lt;groupId&gt;com.fasterxml.jackson.core&lt;/groupId&gt;
      &lt;artifactId&gt;jackson-databind&lt;/artifactId&gt;
      &lt;version&gt;2.7.4&lt;/version&gt;
  &lt;/dependency&gt;
  &lt;dependency&gt;
      &lt;groupId&gt;com.fasterxml.jackson.core&lt;/groupId&gt;
      &lt;artifactId&gt;jackson-annotations&lt;/artifactId&gt;
      &lt;version&gt;2.7.4&lt;/version&gt;
  &lt;/dependency&gt;
  &lt;dependency&gt;
      &lt;groupId&gt;org.apache.kafka&lt;/groupId&gt;
      &lt;artifactId&gt;kafka_2.11&lt;/artifactId&gt;
      &lt;version&gt;0.9.0.1&lt;/version&gt;
  &lt;/dependency&gt;
&lt;/dependencies&gt;</pre>

<p>log4j2.xml配置文件如下所示：</p>

<pre>
&lt;?xml version="1.0" encoding="UTF-8"?&gt;
&lt;!-- Log4j2 的配置文件 --&gt;
&lt;Configuration status="DEBUG" strict="true" name="LOG4J2_DEMO" packages="com.banksteel.log.demo.log4j2"&gt;
    &lt;properties&gt;
        &lt;property name="logPath"&gt;log&lt;/property&gt;
    &lt;/properties&gt;

    &lt;Appenders&gt;
        &lt;!--配置控制台输出样式--&gt;
        &lt;Console name="Console" target="SYSTEM_OUT"&gt;
            &lt;PatternLayout pattern="%highlight{%d{yyyy-MM-dd HH:mm:ss} %d{UNIX_MILLIS} [%t] %-5p %C{1.}:%L - %msg%n}"/&gt;
        &lt;/Console&gt;
        &lt;!-- 配置Kafka日志主动采集，Storm会将日志解析成字段存放在HBase中。 --&gt;
        &lt;Kafka name="Kafka" topic="server_log"&gt;
            &lt;!--使用JSON传输日志文件--&gt;
            &lt;JsonLayout complete="true" locationInfo="true"/&gt;
            &lt;!--Kafka集群配置，需要在本机配置Hosts文件，或者通过Nginx配置--&gt;
            &lt;Property name="bootstrap.servers"&gt;Kafka-01:9092,Kafka-02:9092,Kafka-03:9092&lt;/Property&gt;
        &lt;/Kafka&gt;
    &lt;/Appenders&gt;
    &lt;Loggers&gt;
        &lt;Root level="DEBUG"&gt;
            &lt;!--启用控制台输出日志--&gt;
            &lt;AppenderRef ref="Console"/&gt;
            &lt;!--启用Kafka采集日志--&gt;
            &lt;AppenderRef ref="Kafka"/&gt;
        &lt;/Root&gt;
    &lt;/Loggers&gt;
&lt;/Configuration&gt;</pre>

<p>这样就Okay了，我们可以在Kafka中看到完整的输出：</p>

<pre>
{
  "timeMillis" : 1462712870591,
  "thread" : "main",
  "level" : "ERROR",
  "loggerName" : "com.banksteel.log.demo.log4j2.Demo",
  "message" : "处理业务逻辑的时候发生一个错误……",
  "thrown" : {
    "commonElementCount" : 0,
    "localizedMessage" : "错误消息啊",
    "message" : "错误消息啊",
    "name" : "java.lang.Exception",
    "extendedStackTrace" : [ {
      "class" : "com.banksteel.log.demo.log4j2.Demo",
      "method" : "main",
      "file" : "Demo.java",
      "line" : 20,
      "exact" : true,
      "location" : "classes/",
      "version" : "?"
    }, {
      "class" : "sun.reflect.NativeMethodAccessorImpl",
      "method" : "invoke0",
      "file" : "NativeMethodAccessorImpl.java",
      "line" : -2,
      "exact" : false,
      "location" : "?",
      "version" : "1.7.0_80"
    }, {
      "class" : "sun.reflect.NativeMethodAccessorImpl",
      "method" : "invoke",
      "file" : "NativeMethodAccessorImpl.java",
      "line" : 57,
      "exact" : false,
      "location" : "?",
      "version" : "1.7.0_80"
    }, {
      "class" : "sun.reflect.DelegatingMethodAccessorImpl",
      "method" : "invoke",
      "file" : "DelegatingMethodAccessorImpl.java",
      "line" : 43,
      "exact" : false,
      "location" : "?",
      "version" : "1.7.0_80"
    }, {
      "class" : "java.lang.reflect.Method",
      "method" : "invoke",
      "file" : "Method.java",
      "line" : 606,
      "exact" : false,
      "location" : "?",
      "version" : "1.7.0_80"
    }, {
      "class" : "com.intellij.rt.execution.application.AppMain",
      "method" : "main",
      "file" : "AppMain.java",
      "line" : 144,
      "exact" : true,
      "location" : "idea_rt.jar",
      "version" : "?"
    } ]
  },
  "endOfBatch" : false,
  "loggerFqcn" : "org.apache.logging.log4j.spi.AbstractLogger",
  "source" : {
    "class" : "com.banksteel.log.demo.log4j2.Demo",
    "method" : "main",
    "file" : "Demo.java",
    "line" : 22
  }
}</pre>

<p>为了减少日志对空间的占用，我们通常还会设置JSONLayout的compact属性为true，这样在kafka中获得的日志将会排除掉空格和换行符。 </p>

<h2>最后</h2>

<p>由于在实际开发中，我们会引入多个第三方依赖，这些依赖往往也会依赖无数的log日志框架，为了保证测试通过，请认清本文例子中的包名以及版本号，log4j 1.x 的 Json 输出是为了完全模拟 2.x 的字段，因此部分字段用？代替，如果想要完美，请自行解决。</p>

<p>随便解释一下日志级别，以便建立规范：</p>

<p>log.error 错误信息，通常写在 catch 中，可以使用 log.error("发生了一个错误",e) 来记录详细的异常堆栈</p>

<p>log.fatal 严重错误，该级别的错误用来记录会导致程序异常退出的错误日志。</p>

<p>log.warn 警告</p>

<p>log.info 信息</p>

<p>log.trace 简单输出文字</p>

<p>log.debug 调试信息</p>

<p>Log4j配置详解 <a href="https://www.linuxidc.com/Linux/2014-10/108401.htm" rel="nofollow">http://www.linuxidc.com/Linux/2014-10/108401.htm</a><br><br>
Apache Log4j 2 更多内容请看： <a href="http://logging.apache.org/log4j/2.x/" rel="nofollow">http://logging.apache.org/log4j/2.x/</a></p>

<p>Log4j入门使用教程 <a href="https://www.linuxidc.com/Linux/2013-06/85223.htm" rel="nofollow">http://www.linuxidc.com/Linux/2013-06/85223.htm</a></p>

<p>Log4j 日志详细用法 <a href="https://www.linuxidc.com/Linux/2014-09/107303.htm" rel="nofollow">http://www.linuxidc.com/Linux/2014-09/107303.htm</a></p>

<p>Hibernate配置Log4j显示SQL参数 <a href="https://www.linuxidc.com/Linux/2013-03/81870.htm" rel="nofollow">http://www.linuxidc.com/Linux/2013-03/81870.htm</a></p>

<p>Log4j学习笔记(1)_Log4j 基础&amp;配置项解析 <a href="https://www.linuxidc.com/Linux/2013-03/80586.htm" rel="nofollow">http://www.linuxidc.com/Linux/2013-03/80586.htm</a></p>

<p>Log4j学习笔记(2)_Log4j配置示例&amp;Spring集成Log4j <a href="https://www.linuxidc.com/Linux/2013-03/80587.htm" rel="nofollow">http://www.linuxidc.com/Linux/2013-03/80587.htm</a></p>

<p><strong>Log4j 的详细介绍</strong>：<a href="https://www.linuxidc.com/Linux/2012-09/70119.htm" rel="nofollow">请点这里</a><br><strong>Log4j 的下载地址</strong>：<a href="https://www.linuxidc.com/down.aspx?id=784" rel="nofollow">请点这里</a></p>

<p><strong>本文永久更新链接地址</strong>：<a href="https://www.linuxidc.com/Linux/2016-05/131402.htm" rel="nofollow">http://www.linuxidc.com/Linux/2016-05/131402.htm</a></p>

<p> </p>

<p> </p>

<p> </p>            </div>
                </div>