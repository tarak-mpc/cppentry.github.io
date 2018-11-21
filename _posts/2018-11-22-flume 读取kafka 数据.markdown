---
layout:     post
title:      flume 读取kafka 数据
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>本文介绍flume读取kafka数据的方法</p>
<p>代码：</p>
<p><br></p>
<p>/*******************************************************************************<br>
 * Licensed to the Apache Software Foundation (ASF) under one<br>
 * or more contributor license agreements.  See the NOTICE file<br>
 * distributed with this work for additional information<br>
 * regarding copyright ownership.  The ASF licenses this file<br>
 * to you under the Apache License, Version 2.0 (the<br>
 * "License"); you may not use this file except in compliance<br>
 * with the License.  You may obtain a copy of the License at<br>
 *  <br>
 * http://www.apache.org/licenses/LICENSE-2.0<br>
 *  <br>
 * Unless required by applicable law or agreed to in writing,<br>
 * software distributed under the License is distributed on an<br>
 * "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY<br>
 * KIND, either express or implied.  See the License for the<br>
 * specific language governing permissions and limitations<br>
 * under the License.<br>
 *******************************************************************************/<br>
package org.apache.flume.source.kafka;<br><br>
import java.io.IOException;<br>
import java.nio.ByteBuffer;<br>
import java.util.ArrayList;<br>
import java.util.HashMap;<br>
import java.util.List;<br>
import java.util.Map;<br><br>
import kafka.consumer.ConsumerIterator;<br>
import kafka.consumer.KafkaStream;<br>
import kafka.javaapi.consumer.ConsumerConnector;<br>
import kafka.message.Message;<br><br>
import kafka.message.MessageAndMetadata;<br>
import org.apache.flume.*;<br>
import org.apache.flume.conf.Configurable;<br>
import org.apache.flume.conf.ConfigurationException;<br>
import org.apache.flume.event.SimpleEvent;<br>
import org.apache.flume.source.AbstractSource;<br>
import org.apache.flume.source.SyslogParser;<br>
import org.slf4j.Logger;<br>
import org.slf4j.LoggerFactory;<br><br><br>
/**<br>
 * A Source for Kafka which reads messages from kafka. I use this in company production environment
<br>
 * and its performance is good. Over 100k messages per second can be read from kafka in one source.&lt;p&gt;<br>
 * &lt;tt&gt;zookeeper.connect: &lt;/tt&gt; the zookeeper ip kafka use.&lt;p&gt;<br>
 * &lt;tt&gt;topic: &lt;/tt&gt; the topic to read from kafka.&lt;p&gt;<br>
 * &lt;tt&gt;group.id: &lt;/tt&gt; the groupid of consumer group.&lt;p&gt;<br>
 */<br>
public class KafkaSource extends AbstractSource implements Configurable, PollableSource {<br>
    private static final Logger log = LoggerFactory.getLogger(KafkaSource.class);<br>
    private ConsumerConnector consumer;<br>
    private ConsumerIterator&lt;byte[], byte[]&gt; it;<br>
    private String topic;<br>
    <br>
    public Status process() throws EventDeliveryException {<br>
        List&lt;Event&gt; eventList = new ArrayList&lt;Event&gt;();<br>
        MessageAndMetadata&lt;byte[],byte[]&gt; message;<br>
        Event event;<br>
        Map&lt;String, String&gt; headers;<br>
        String strMessage;<br>
        try {<br>
            if(it.hasNext()) {<br>
                message = it.next();<br>
                event = new SimpleEvent();<br>
                headers = new HashMap&lt;String, String&gt;();<br>
                headers.put("timestamp", String.valueOf(System.currentTimeMillis()));<br><br>
               <span style="color:#FF0000;"> strMessage =  String.valueOf(System.currentTimeMillis()) + "|" + new String(message.message());</span><br>
                log.debug("Message: {}", strMessage);<br><br>
                event.setBody(strMessage.getBytes());<br>
                //event.setBody(message.message());<br>
                event.setHeaders(headers);<br>
                eventList.add(event);<br>
            }<br>
            getChannelProcessor().processEventBatch(eventList);<br>
            return Status.READY;<br>
        } catch (Exception e) {<br>
            log.error("KafkaSource EXCEPTION, {}", e.getMessage());<br>
            return Status.BACKOFF;<br>
        }<br>
    }<br><br>
    public void configure(Context context) {<br>
        topic = context.getString("topic");<br>
        if(topic == null) {<br>
            throw new ConfigurationException("Kafka topic must be specified.");<br>
        }<br>
        try {<br>
            this.consumer = KafkaSourceUtil.getConsumer(context);<br>
        } catch (IOException e) {<br>
            log.error("IOException occur, {}", e.getMessage());<br>
        } catch (InterruptedException e) {<br>
            log.error("InterruptedException occur, {}", e.getMessage());<br>
        }<br>
        Map&lt;String, Integer&gt; topicCountMap = new HashMap&lt;String, Integer&gt;();<br>
        topicCountMap.put(topic, new Integer(1));<br>
        Map&lt;String, List&lt;KafkaStream&lt;byte[], byte[]&gt;&gt;&gt; consumerMap = consumer.createMessageStreams(topicCountMap);<br>
        if(consumerMap == null) {<br>
            throw new ConfigurationException("topicCountMap is null");<br>
        }<br>
        List&lt;KafkaStream&lt;byte[], byte[]&gt;&gt; topicList = consumerMap.get(topic);<br>
        if(topicList == null || topicList.isEmpty()) {<br>
            throw new ConfigurationException("topicList is null or empty");<br>
        }<br>
        KafkaStream&lt;byte[], byte[]&gt; stream =  topicList.get(0);<br>
        it = stream.iterator();<br>
    }<br><br>
    @Override<br>
    public synchronized void stop() {<br>
        consumer.shutdown();<br>
        super.stop();<br>
    }<br><br>
}<br><br>
/*******************************************************************************<br>
 * Licensed to the Apache Software Foundation (ASF) under one<br>
 * or more contributor license agreements.  See the NOTICE file<br>
 * distributed with this work for additional information<br>
 * regarding copyright ownership.  The ASF licenses this file<br>
 * to you under the Apache License, Version 2.0 (the<br>
 * "License"); you may not use this file except in compliance<br>
 * with the License.  You may obtain a copy of the License at<br>
 *  <br>
 * http://www.apache.org/licenses/LICENSE-2.0<br>
 *  <br>
 * Unless required by applicable law or agreed to in writing,<br>
 * software distributed under the License is distributed on an<br>
 * "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY<br>
 * KIND, either express or implied.  See the License for the<br>
 * specific language governing permissions and limitations<br>
 * under the License.<br>
 *******************************************************************************/<br>
package org.apache.flume.source.kafka;<br><br><br>
import java.io.IOException;<br>
import java.util.Map;<br>
import java.util.Properties;<br><br>
import com.google.common.collect.ImmutableMap;<br>
import kafka.consumer.Consumer;<br>
import kafka.consumer.ConsumerConfig;<br>
import kafka.javaapi.consumer.ConsumerConnector;<br><br>
import org.apache.flume.Context;<br>
import org.slf4j.Logger;<br>
import org.slf4j.LoggerFactory;<br><br><br>
public class KafkaSourceUtil {<br>
    private static final Logger log = LoggerFactory.getLogger(KafkaSourceUtil.class);<br><br>
    public static Properties getKafkaConfigProperties(Context context) {<br>
        log.info("context={}",context.toString());<br>
        Properties props = new Properties();<br>
        ImmutableMap&lt;String, String&gt; contextMap = context.getParameters();<br>
        for (Map.Entry&lt;String,String&gt; entry : contextMap.entrySet()) {<br>
            String key = entry.getKey();<br>
            if (!key.equals("type") &amp;&amp; !key.equals("channel")) {<br>
                props.setProperty(entry.getKey(), entry.getValue());<br>
                log.info("key={},value={}", entry.getKey(), entry.getValue());<br>
            }<br>
        }<br>
        return props;<br>
    }<br>
    public static ConsumerConnector getConsumer(Context context) throws IOException, InterruptedException {<br>
        ConsumerConfig consumerConfig = new ConsumerConfig(getKafkaConfigProperties(context));<br>
        ConsumerConnector consumer = Consumer.createJavaConsumerConnector(consumerConfig);<br>
        return consumer;<br>
    }<br>
}<br><br><br><br>
配置文件：（ /etc/flume/conf/flume-kafka-file.properties）<br></p>
<p>agent_log.sources = kafka0 <br>
agent_log.channels = ch0 <br>
agent_log.sinks = sink0<br><br>
agent_log.sources.kafka0.channels = ch0<br>
agent_log.sinks.sink0.channel = ch0<br><br><br><br>
agent_log.sources.kafka0.type = org.apache.flume.source.kafka.KafkaSource<br>
agent_log.sources.kafka0.zookeeper.connect = node3:2181,node4:2181,node5:2181<br>
agent_log.sources.kafka0.topic = kkt-test-topic<br>
agent_log.sources.kafka0.group.id= test <br><br>
agent_log.channels.ch0.type = memory<br>
agent_log.channels.ch0.capacity = 2048<br>
agent_log.channels.ch0.transactionCapacity = 1000<br><br><br>
agent_log.sinks.sink0.type=file_roll<br>
agent_log.sinks.sink0.sink.directory=/data/flumeng/data/test<br>
agent_log.sinks.sink0.sink.rollInterval=300<br><br></p>
<p>启动脚本：</p>
<p>sudo su  -l -s /bin/bash  flume  -c '/usr/lib/flume/bin/flume-ng agent --conf /etc/flume/conf --conf-file /etc/flume/conf/flume-kafka-file.properties -name agent_log -Dflume.root.logger=INFO,console '<br><br></p>
<p><br>
注意： 红色字体的功能是对原来数据加入时间戳</p>
<p>            版本 flume-1.4.0.2.1.1.0 + kafka2.8.0-0.8.0</p>
<p>            参考资料：https://github.com/baniuyao/flume-kafka<br></p>
<p>             编译用到的库：</p>
<p>            flume-ng-configuration-1.4.0.2.1.1.0-385</p>
<p>            flume-ng-core-1.4.0.2.1.1.0-385</p>
<p>            flume-ng-sdk-1.4.0.2.1.1.0-385</p>
<p>            flume-tools-1.4.0.2.1.1.0-385</p>
<p>            guava-11.0.2</p>
<p>            kafka_2.8.0-0.8.0</p>
<p>            log4j-1.2.15</p>
<p>            scala-compiler</p>
<p>            scala-library</p>
<p>            slf4j-api-1.6.1</p>
<p>            slf4j-log4j12-1.6.1</p>
<p>            zkclient-0.3</p>
<p>            zookeeper-3.3.4<br></p>
<p>                 <br><br><br><br><br><br></p>
            </div>
                </div>