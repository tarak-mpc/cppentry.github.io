---
layout:     post
title:      flume学习03-flume收集log4j日志
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/JThink_/article/details/43796017				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>前几篇已经介绍了flume相关的知识，包括flume架构、如何配置启动以及暂停，接下去主要说说flume接受log4j日志。 <br>
前面介绍过了log4j需要用avro的方式讲日志传递到flume，所以我们的配置都是基于avro的source</p>



<h1 id="log4j日志输出到flume的console">log4j日志输出到flume的console</h1>

<ul>
<li>修改配置文件log4j-agent.properties</li>
</ul>



<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-preprocessor"># distributed with this work for additional information</span>
<span class="hljs-preprocessor"># regarding copyright ownership.  The ASF licenses this file</span>
<span class="hljs-preprocessor"># to you under the Apache License, Version 2.0 (the</span>
<span class="hljs-preprocessor"># "License"); you may not use this file except in compliance</span>
<span class="hljs-preprocessor"># with the License.  You may obtain a copy of the License at</span>
<span class="hljs-preprocessor">#</span>
<span class="hljs-preprocessor">#  http://www.apache.org/licenses/LICENSE-2.0</span>
<span class="hljs-preprocessor">#</span>
<span class="hljs-preprocessor"># Unless required by applicable law or agreed to in writing,</span>
<span class="hljs-preprocessor"># software distributed under the License is distributed on an</span>
<span class="hljs-preprocessor"># "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY</span>
<span class="hljs-preprocessor"># KIND, either express or implied.  See the License for the</span>
<span class="hljs-preprocessor"># specific language governing permissions and limitations</span>
<span class="hljs-preprocessor"># under the License.</span>


<span class="hljs-preprocessor"># The configuration file needs to define the sources, </span>
<span class="hljs-preprocessor"># the channels and the sinks.</span>
<span class="hljs-preprocessor"># Sources, channels and sinks are defined per agent, </span>
<span class="hljs-preprocessor"># in this case called 'agent'</span>

agent<span class="hljs-preprocessor">.sources</span> = so1
agent<span class="hljs-preprocessor">.channels</span> = c1
agent<span class="hljs-preprocessor">.sinks</span> = s1

<span class="hljs-preprocessor"># For each one of the sources, the type is defined</span>
agent<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.so</span>1<span class="hljs-preprocessor">.type</span> = avro
agent<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.so</span>1<span class="hljs-preprocessor">.bind</span> = <span class="hljs-number">0.0</span><span class="hljs-number">.0</span><span class="hljs-number">.0</span>
agent<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.so</span>1<span class="hljs-preprocessor">.port</span> = <span class="hljs-number">44444</span>
tier1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.channel</span>1<span class="hljs-preprocessor">.keep</span>-alive=<span class="hljs-number">30</span>

<span class="hljs-preprocessor"># The channel can be defined as follows.</span>
<span class="hljs-preprocessor"># agent.sources.seqGenSrc.channels = memoryChannel</span>

<span class="hljs-preprocessor"># Each sink's type must be defined</span>
agent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.s</span>1<span class="hljs-preprocessor">.type</span> = logger

<span class="hljs-preprocessor">#Specify the channel the sink should use</span>
<span class="hljs-preprocessor"># agent.sinks.loggerSink.channel = memoryChannel</span>

<span class="hljs-preprocessor"># Each channel's type is defined.</span>
agent<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.type</span> = memory
agent<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.capacity</span> = <span class="hljs-number">1000</span>
agent<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.transactionCapacity</span> = <span class="hljs-number">100</span>

<span class="hljs-preprocessor"># Bind the source and sink to the channel</span>
agent<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.so</span>1<span class="hljs-preprocessor">.channels</span> = c1
agent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.s</span>1<span class="hljs-preprocessor">.channel</span> = c1


<span class="hljs-preprocessor"># Other config values specific to each type of channel(sink or source)</span>
<span class="hljs-preprocessor"># can be defined as well</span>
<span class="hljs-preprocessor"># In this case, it specifies the capacity of the memory channel</span>
<span class="hljs-preprocessor"># agent.channels.memoryChannel.capacity = 100</span>
</code></pre>

<ul>
<li>启动./start.sh log4j-agent.properties agent</li>
<li>写一段简单的代码用log4j输出log，代码如下：</li>
</ul>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">import</span> java.util.Date;

<span class="hljs-keyword">import</span> org.apache.commons.logging.Log;
<span class="hljs-keyword">import</span> org.apache.commons.logging.LogFactory;

<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">WriteLog</span> {</span>

    <span class="hljs-keyword">protected</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">final</span> Log logger = LogFactory.getLog(<span class="hljs-string">"xxx"</span>);

    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">main</span>(String[] args) <span class="hljs-keyword">throws</span> InterruptedException {
        <span class="hljs-keyword">while</span> (<span class="hljs-keyword">true</span>) {
            <span class="hljs-comment">// 每隔两秒log输出一下当前系统时间戳</span>
            logger.info(<span class="hljs-keyword">new</span> Date().getTime());
            Thread.sleep(<span class="hljs-number">1000</span>);
        }
    }
}
</code></pre>

<p>那么我们该如何配置log4j.xml呢，其实只要加一个appender</p>



<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-tag">&lt;<span class="hljs-title">appender</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"flume-avro"</span>
    <span class="hljs-attribute">class</span>=<span class="hljs-value">"org.apache.flume.clients.log4jappender.Log4jAppender"</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">param</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"Hostname"</span> <span class="hljs-attribute">value</span>=<span class="hljs-value">"localhost"</span> /&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">param</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"Port"</span> <span class="hljs-attribute">value</span>=<span class="hljs-value">"44444"</span> /&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">param</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"UnsafeMode"</span> <span class="hljs-attribute">value</span>=<span class="hljs-value">"true"</span> /&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">layout</span> <span class="hljs-attribute">class</span>=<span class="hljs-value">"org.apache.log4j.PatternLayout"</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">param</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"ConversionPattern"</span> <span class="hljs-attribute">value</span>=<span class="hljs-value">"%d{yyyy-MM-dd HH:mm:ss} [%l] [%rms] %m"</span> /&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">layout</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">appender</span>&gt;</span></code></pre>

<ul>
<li>接下去运行那么main函数，看flume的输出</li>
</ul>



<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-number">2015</span>-<span class="hljs-number">02</span>-<span class="hljs-number">13</span> <span class="hljs-number">16</span>:<span class="hljs-number">40</span>:<span class="hljs-number">33</span>,<span class="hljs-number">027</span> (New I/O  worker <span class="hljs-preprocessor">#1) [DEBUG - org.apache.flume.source.AvroSource.append(AvroSource.java:347)] Avro source so1: Received avro event: {"headers": {"flume.client.log4j.log.level": "20000", "flume.client.log4j.logger.name": "xxx", "flume.client.log4j.message.encoding": "UTF8", "flume.client.log4j.timestamp": "1423816832971"}, "body": {"bytes": "2015-02-13 16:40:32 [XXX.flume.test.log.WriteLog.main(WriteLog.java:15)] [289ms] 1423816832971"}}</span>
<span class="hljs-number">2015</span>-<span class="hljs-number">02</span>-<span class="hljs-number">13</span> <span class="hljs-number">16</span>:<span class="hljs-number">40</span>:<span class="hljs-number">33</span>,<span class="hljs-number">031</span> (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.sink</span><span class="hljs-preprocessor">.LoggerSink</span><span class="hljs-preprocessor">.process</span>(LoggerSink<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">70</span>)] Event: { headers:{flume<span class="hljs-preprocessor">.client</span><span class="hljs-preprocessor">.log</span>4j<span class="hljs-preprocessor">.log</span><span class="hljs-preprocessor">.level</span>=<span class="hljs-number">20000</span>, flume<span class="hljs-preprocessor">.client</span><span class="hljs-preprocessor">.log</span>4j<span class="hljs-preprocessor">.message</span><span class="hljs-preprocessor">.encoding</span>=UTF8, flume<span class="hljs-preprocessor">.client</span><span class="hljs-preprocessor">.log</span>4j<span class="hljs-preprocessor">.logger</span><span class="hljs-preprocessor">.name</span>=xxx, flume<span class="hljs-preprocessor">.client</span><span class="hljs-preprocessor">.log</span>4j<span class="hljs-preprocessor">.timestamp</span>=<span class="hljs-number">1423816832971</span>} body: <span class="hljs-number">32</span> <span class="hljs-number">30</span> <span class="hljs-number">31</span> <span class="hljs-number">35</span> <span class="hljs-number">2</span>D <span class="hljs-number">30</span> <span class="hljs-number">32</span> <span class="hljs-number">2</span>D <span class="hljs-number">31</span> <span class="hljs-number">33</span> <span class="hljs-number">20</span> <span class="hljs-number">31</span> <span class="hljs-number">36</span> <span class="hljs-number">3</span>A <span class="hljs-number">34</span> <span class="hljs-number">30</span> <span class="hljs-number">2015</span>-<span class="hljs-number">02</span>-<span class="hljs-number">13</span> <span class="hljs-number">16</span>:<span class="hljs-number">40</span> }
<span class="hljs-number">2015</span>-<span class="hljs-number">02</span>-<span class="hljs-number">13</span> <span class="hljs-number">16</span>:<span class="hljs-number">40</span>:<span class="hljs-number">34</span>,<span class="hljs-number">052</span> (New I/O  worker <span class="hljs-preprocessor">#1) [DEBUG - org.apache.flume.source.AvroSource.append(AvroSource.java:347)] Avro source so1: Received avro event: {"headers": {"flume.client.log4j.log.level": "20000", "flume.client.log4j.logger.name": "xxx", "flume.client.log4j.message.encoding": "UTF8", "flume.client.log4j.timestamp": "1423816834050"}, "body": {"bytes": "2015-02-13 16:40:34 [XXX.flume.test.log.WriteLog.main(WriteLog.java:15)] [1368ms] 1423816834050"}}</span>
<span class="hljs-number">2015</span>-<span class="hljs-number">02</span>-<span class="hljs-number">13</span> <span class="hljs-number">16</span>:<span class="hljs-number">40</span>:<span class="hljs-number">34</span>,<span class="hljs-number">053</span> (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.sink</span><span class="hljs-preprocessor">.LoggerSink</span><span class="hljs-preprocessor">.process</span>(LoggerSink<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">70</span>)] Event: { headers:{flume<span class="hljs-preprocessor">.client</span><span class="hljs-preprocessor">.log</span>4j<span class="hljs-preprocessor">.log</span><span class="hljs-preprocessor">.level</span>=<span class="hljs-number">20000</span>, flume<span class="hljs-preprocessor">.client</span><span class="hljs-preprocessor">.log</span>4j<span class="hljs-preprocessor">.message</span><span class="hljs-preprocessor">.encoding</span>=UTF8, flume<span class="hljs-preprocessor">.client</span><span class="hljs-preprocessor">.log</span>4j<span class="hljs-preprocessor">.logger</span><span class="hljs-preprocessor">.name</span>=xxx, flume<span class="hljs-preprocessor">.client</span><span class="hljs-preprocessor">.log</span>4j<span class="hljs-preprocessor">.timestamp</span>=<span class="hljs-number">1423816834050</span>} body: <span class="hljs-number">32</span> <span class="hljs-number">30</span> <span class="hljs-number">31</span> <span class="hljs-number">35</span> <span class="hljs-number">2</span>D <span class="hljs-number">30</span> <span class="hljs-number">32</span> <span class="hljs-number">2</span>D <span class="hljs-number">31</span> <span class="hljs-number">33</span> <span class="hljs-number">20</span> <span class="hljs-number">31</span> <span class="hljs-number">36</span> <span class="hljs-number">3</span>A <span class="hljs-number">34</span> <span class="hljs-number">30</span> <span class="hljs-number">2015</span>-<span class="hljs-number">02</span>-<span class="hljs-number">13</span> <span class="hljs-number">16</span>:<span class="hljs-number">40</span> }
<span class="hljs-number">2015</span>-<span class="hljs-number">02</span>-<span class="hljs-number">13</span> <span class="hljs-number">16</span>:<span class="hljs-number">40</span>:<span class="hljs-number">35</span>,<span class="hljs-number">056</span> (New I/O  worker <span class="hljs-preprocessor">#1) [DEBUG - org.apache.flume.source.AvroSource.append(AvroSource.java:347)] Avro source so1: Received avro event: {"headers": {"flume.client.log4j.log.level": "20000", "flume.client.log4j.logger.name": "xxx", "flume.client.log4j.message.encoding": "UTF8", "flume.client.log4j.timestamp": "1423816835053"}, "body": {"bytes": "2015-02-13 16:40:35 [XXX.flume.test.log.WriteLog.main(WriteLog.java:15)] [2371ms] 1423816835053"}}</span>
<span class="hljs-number">2015</span>-<span class="hljs-number">02</span>-<span class="hljs-number">13</span> <span class="hljs-number">16</span>:<span class="hljs-number">40</span>:<span class="hljs-number">35</span>,<span class="hljs-number">056</span> (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.sink</span><span class="hljs-preprocessor">.LoggerSink</span><span class="hljs-preprocessor">.process</span>(LoggerSink<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">70</span>)] Event: { headers:{flume<span class="hljs-preprocessor">.client</span><span class="hljs-preprocessor">.log</span>4j<span class="hljs-preprocessor">.log</span><span class="hljs-preprocessor">.level</span>=<span class="hljs-number">20000</span>, flume<span class="hljs-preprocessor">.client</span><span class="hljs-preprocessor">.log</span>4j<span class="hljs-preprocessor">.message</span><span class="hljs-preprocessor">.encoding</span>=UTF8, flume<span class="hljs-preprocessor">.client</span><span class="hljs-preprocessor">.log</span>4j<span class="hljs-preprocessor">.logger</span><span class="hljs-preprocessor">.name</span>=xxx, flume<span class="hljs-preprocessor">.client</span><span class="hljs-preprocessor">.log</span>4j<span class="hljs-preprocessor">.timestamp</span>=<span class="hljs-number">1423816835053</span>} body: <span class="hljs-number">32</span> <span class="hljs-number">30</span> <span class="hljs-number">31</span> <span class="hljs-number">35</span> <span class="hljs-number">2</span>D <span class="hljs-number">30</span> <span class="hljs-number">32</span> <span class="hljs-number">2</span>D <span class="hljs-number">31</span> <span class="hljs-number">33</span> <span class="hljs-number">20</span> <span class="hljs-number">31</span> <span class="hljs-number">36</span> <span class="hljs-number">3</span>A <span class="hljs-number">34</span> <span class="hljs-number">30</span> <span class="hljs-number">2015</span>-<span class="hljs-number">02</span>-<span class="hljs-number">13</span> <span class="hljs-number">16</span>:<span class="hljs-number">40</span> }
<span class="hljs-number">2015</span>-<span class="hljs-number">02</span>-<span class="hljs-number">13</span> <span class="hljs-number">16</span>:<span class="hljs-number">40</span>:<span class="hljs-number">36</span>,<span class="hljs-number">059</span> (New I/O  worker <span class="hljs-preprocessor">#1) [DEBUG - org.apache.flume.source.AvroSource.append(AvroSource.java:347)] Avro source so1: Received avro event: {"headers": {"flume.client.log4j.log.level": "20000", "flume.client.log4j.logger.name": "xxx", "flume.client.log4j.message.encoding": "UTF8", "flume.client.log4j.timestamp": "1423816836057"}, "body": {"bytes": "2015-02-13 16:40:36 [XXX.flume.test.log.WriteLog.main(WriteLog.java:15)] [3375ms] 1423816836057"}}</span>
<span class="hljs-number">2015</span>-<span class="hljs-number">02</span>-<span class="hljs-number">13</span> <span class="hljs-number">16</span>:<span class="hljs-number">40</span>:<span class="hljs-number">36</span>,<span class="hljs-number">059</span> (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.sink</span><span class="hljs-preprocessor">.LoggerSink</span><span class="hljs-preprocessor">.process</span>(LoggerSink<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">70</span>)] Event: { headers:{flume<span class="hljs-preprocessor">.client</span><span class="hljs-preprocessor">.log</span>4j<span class="hljs-preprocessor">.log</span><span class="hljs-preprocessor">.level</span>=<span class="hljs-number">20000</span>, flume<span class="hljs-preprocessor">.client</span><span class="hljs-preprocessor">.log</span>4j<span class="hljs-preprocessor">.message</span><span class="hljs-preprocessor">.encoding</span>=UTF8, flume<span class="hljs-preprocessor">.client</span><span class="hljs-preprocessor">.log</span>4j<span class="hljs-preprocessor">.logger</span><span class="hljs-preprocessor">.name</span>=xxx, flume<span class="hljs-preprocessor">.client</span><span class="hljs-preprocessor">.log</span>4j<span class="hljs-preprocessor">.timestamp</span>=<span class="hljs-number">1423816836057</span>} body: <span class="hljs-number">32</span> <span class="hljs-number">30</span> <span class="hljs-number">31</span> <span class="hljs-number">35</span> <span class="hljs-number">2</span>D <span class="hljs-number">30</span> <span class="hljs-number">32</span> <span class="hljs-number">2</span>D <span class="hljs-number">31</span> <span class="hljs-number">33</span> <span class="hljs-number">20</span> <span class="hljs-number">31</span> <span class="hljs-number">36</span> <span class="hljs-number">3</span>A <span class="hljs-number">34</span> <span class="hljs-number">30</span> <span class="hljs-number">2015</span>-<span class="hljs-number">02</span>-<span class="hljs-number">13</span> <span class="hljs-number">16</span>:<span class="hljs-number">40</span> }
<span class="hljs-number">2015</span>-<span class="hljs-number">02</span>-<span class="hljs-number">13</span> <span class="hljs-number">16</span>:<span class="hljs-number">40</span>:<span class="hljs-number">37</span>,<span class="hljs-number">064</span> (New I/O  worker <span class="hljs-preprocessor">#1) [DEBUG - org.apache.flume.source.AvroSource.append(AvroSource.java:347)] Avro source so1: Received avro event: {"headers": {"flume.client.log4j.log.level": "20000", "flume.client.log4j.logger.name": "xxx", "flume.client.log4j.message.encoding": "UTF8", "flume.client.log4j.timestamp": "1423816837062"}, "body": {"bytes": "2015-02-13 16:40:37 [XXX.flume.test.log.WriteLog.main(WriteLog.java:15)] [4380ms] 1423816837062"}}</span></code></pre>

<p>这就是将log4j的日志输出到了flume的console中了</p>



<h1 id="flume多节点输出">flume多节点输出</h1>

<p>之前说的都是flume单机，那么我们如果日志量比较大，或者很多系统产生日志，那么flume这一层我们就需要多个节点，其实比较简单，找多台机器安装flume，然后只要配置下log4j.xml</p>



<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-tag">&lt;<span class="hljs-title">appender</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"flume-load-balancing-avro"</span>
    <span class="hljs-attribute">class</span>=<span class="hljs-value">"org.apache.flume.clients.log4jappender.LoadBalancingLog4jAppender"</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">param</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"Hosts"</span>
        <span class="hljs-attribute">value</span>=<span class="hljs-value">"xxx:12306 xxx:12306 xxx:12306"</span> /&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">param</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"Selector"</span> <span class="hljs-attribute">value</span>=<span class="hljs-value">"ROUND_ROBIN"</span> /&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">param</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"MaxBackoff"</span> <span class="hljs-attribute">value</span>=<span class="hljs-value">"2000"</span> /&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">layout</span> <span class="hljs-attribute">class</span>=<span class="hljs-value">"org.apache.log4j.PatternLayout"</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">param</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"ConversionPattern"</span> <span class="hljs-attribute">value</span>=<span class="hljs-value">"%d{yyyy-MM-dd HH:mm:ss} [%l] [%rms] %m"</span> /&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">layout</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">appender</span>&gt;</span>

<span class="hljs-tag">&lt;<span class="hljs-title">appender</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"flume-async"</span> <span class="hljs-attribute">class</span>=<span class="hljs-value">"org.apache.log4j.AsyncAppender"</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">param</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"BufferSize"</span> <span class="hljs-attribute">value</span>=<span class="hljs-value">"256"</span> /&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">appender-ref</span> <span class="hljs-attribute">ref</span>=<span class="hljs-value">"flume-load-balancing-avro"</span> /&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">appender</span>&gt;</span></code></pre>

<p>可以自己测试是不是多节点在工作，这里其实有很多种算法可选，具体可以看我<a href="http://blog.csdn.net/jthink_/article/details/43488889" rel="nofollow">flume学习01-flume介绍</a></p>



<h1 id="log4j日志输出到flume存入hbase">log4j日志输出到flume存入hbase</h1>

<ul>
<li>这里只说多节点的输出</li>
<li>修改配置文件</li>
</ul>



<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-preprocessor"># distributed with this work for additional information</span>
<span class="hljs-preprocessor"># regarding copyright ownership.  The ASF licenses this file</span>
<span class="hljs-preprocessor"># to you under the Apache License, Version 2.0 (the</span>
<span class="hljs-preprocessor"># "License"); you may not use this file except in compliance</span>
<span class="hljs-preprocessor"># with the License.  You may obtain a copy of the License at</span>
<span class="hljs-preprocessor">#</span>
<span class="hljs-preprocessor">#  http://www.apache.org/licenses/LICENSE-2.0</span>
<span class="hljs-preprocessor">#</span>
<span class="hljs-preprocessor"># Unless required by applicable law or agreed to in writing,</span>
<span class="hljs-preprocessor"># software distributed under the License is distributed on an</span>
<span class="hljs-preprocessor"># "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY</span>
<span class="hljs-preprocessor"># KIND, either express or implied.  See the License for the</span>
<span class="hljs-preprocessor"># specific language governing permissions and limitations</span>
<span class="hljs-preprocessor"># under the License.</span>


<span class="hljs-preprocessor"># The configuration file needs to define the sources, </span>
<span class="hljs-preprocessor"># the channels and the sinks.</span>
<span class="hljs-preprocessor"># Sources, channels and sinks are defined per agent, </span>
<span class="hljs-preprocessor"># in this case called 'agent'</span>

agent<span class="hljs-preprocessor">.sources</span> = avroSource
agent<span class="hljs-preprocessor">.channels</span> = memChannel
agent<span class="hljs-preprocessor">.sinks</span> = hbaseSink

<span class="hljs-preprocessor"># For each one of the sources, the type is defined</span>
agent<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.avroSource</span><span class="hljs-preprocessor">.type</span> = avro
agent<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.avroSource</span><span class="hljs-preprocessor">.bind</span> = <span class="hljs-number">0.0</span><span class="hljs-number">.0</span><span class="hljs-number">.0</span>
agent<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.avroSource</span><span class="hljs-preprocessor">.port</span> = <span class="hljs-number">12306</span>

<span class="hljs-preprocessor"># The channel can be defined as follows.</span>
<span class="hljs-preprocessor"># agent.sources.seqGenSrc.channels = memoryChannel</span>

<span class="hljs-preprocessor"># Each sink's type must be defined</span>
agent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.hbaseSink</span><span class="hljs-preprocessor">.type</span> = hbase
agent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.hbaseSink</span><span class="hljs-preprocessor">.table</span> = flume
agent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.hbaseSink</span><span class="hljs-preprocessor">.columnFamily</span> = cf
agent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.hbaseSink</span><span class="hljs-preprocessor">.serializer</span> = org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.sink</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.SimpleHbaseEventSerializer</span>

<span class="hljs-preprocessor">#Specify the channel the sink should use</span>
<span class="hljs-preprocessor"># agent.sinks.loggerSink.channel = memoryChannel</span>

<span class="hljs-preprocessor"># Each channel's type is defined.</span>
agent<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.memChannel</span><span class="hljs-preprocessor">.type</span> = memory
agent<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.memChannel</span><span class="hljs-preprocessor">.capacity</span> = <span class="hljs-number">1000</span>
agent<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.memChannel</span><span class="hljs-preprocessor">.transactionCapacity</span> = <span class="hljs-number">100</span>

<span class="hljs-preprocessor"># Bind the source and sink to the channel</span>
agent<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.avroSource</span><span class="hljs-preprocessor">.channels</span> = memChannel
agent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.hbaseSink</span><span class="hljs-preprocessor">.channel</span> = memChannel</code></pre>

<ul>
<li>在多个节点上同样这么配置然后启动</li>
<li>log4j.xml</li>
</ul>



<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-tag">&lt;<span class="hljs-title">appender</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"flume-load-balancing-avro"</span>
        <span class="hljs-attribute">class</span>=<span class="hljs-value">"org.apache.flume.clients.log4jappender.LoadBalancingLog4jAppender"</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">param</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"Hosts"</span>
            <span class="hljs-attribute">value</span>=<span class="hljs-value">"xxx:12306 xxx:12306 xxx:12306"</span> /&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">param</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"Selector"</span> <span class="hljs-attribute">value</span>=<span class="hljs-value">"ROUND_ROBIN"</span> /&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">param</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"MaxBackoff"</span> <span class="hljs-attribute">value</span>=<span class="hljs-value">"2000"</span> /&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">layout</span> <span class="hljs-attribute">class</span>=<span class="hljs-value">"org.apache.log4j.PatternLayout"</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">param</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"ConversionPattern"</span> <span class="hljs-attribute">value</span>=<span class="hljs-value">"%d{yyyy-MM-dd HH:mm:ss} [%l] [%rms] %m"</span> /&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">layout</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">appender</span>&gt;</span>

    <span class="hljs-tag">&lt;<span class="hljs-title">appender</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"flume-async"</span> <span class="hljs-attribute">class</span>=<span class="hljs-value">"org.apache.log4j.AsyncAppender"</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">param</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"BufferSize"</span> <span class="hljs-attribute">value</span>=<span class="hljs-value">"256"</span> /&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">appender-ref</span> <span class="hljs-attribute">ref</span>=<span class="hljs-value">"flume-load-balancing-avro"</span> /&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">appender</span>&gt;</span></code></pre>

<ul>
<li>运行刚刚那个java main函数就可以看到日志输出到hbase中了</li>
<li>如果想要进入hbase格式按照自己定义的，那么需要自定义HbaseEventSerializer，这里给个简单例子</li>
</ul>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">XXXHbaseEventSerializer</span> <span class="hljs-keyword">implements</span> <span class="hljs-title">HbaseEventSerializer</span> {</span>

    <span class="hljs-keyword">private</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">final</span> Map&lt;String, <span class="hljs-keyword">byte</span>[]&gt; LOG_LEVEL = <span class="hljs-keyword">new</span> HashMap&lt;String, <span class="hljs-keyword">byte</span>[]&gt;();
    <span class="hljs-keyword">static</span> {
        LOG_LEVEL.put(<span class="hljs-string">"10000"</span>, <span class="hljs-string">"DEBUG"</span>.getBytes(Charsets.UTF_8));
        LOG_LEVEL.put(<span class="hljs-string">"20000"</span>, <span class="hljs-string">"INFO"</span>.getBytes(Charsets.UTF_8));
        LOG_LEVEL.put(<span class="hljs-string">"30000"</span>, <span class="hljs-string">"WARN"</span>.getBytes(Charsets.UTF_8));
        LOG_LEVEL.put(<span class="hljs-string">"40000"</span>, <span class="hljs-string">"ERROR"</span>.getBytes(Charsets.UTF_8));
        LOG_LEVEL.put(<span class="hljs-string">"50000"</span>, <span class="hljs-string">"FATAL"</span>.getBytes(Charsets.UTF_8));
    }

    <span class="hljs-comment">// 列族</span>
    <span class="hljs-keyword">private</span> <span class="hljs-keyword">byte</span>[] cf;
    <span class="hljs-comment">// 消息等级列名</span>
    <span class="hljs-keyword">private</span> <span class="hljs-keyword">byte</span>[] levelCol;
    <span class="hljs-comment">// 消息内容列名</span>
    <span class="hljs-keyword">private</span> <span class="hljs-keyword">byte</span>[] plCol;
    <span class="hljs-comment">// 消息内容</span>
    <span class="hljs-keyword">private</span> <span class="hljs-keyword">byte</span>[] payload;
    <span class="hljs-comment">// 日志级别</span>
    <span class="hljs-keyword">private</span> <span class="hljs-keyword">byte</span>[] level;
    <span class="hljs-comment">// 系统标示</span>
    <span class="hljs-keyword">private</span> String systemId;
    <span class="hljs-comment">// 日志时间</span>
    <span class="hljs-keyword">private</span> String timestamp;

    <span class="hljs-keyword">private</span> <span class="hljs-keyword">byte</span>[] incCol;

    <span class="hljs-keyword">private</span> <span class="hljs-keyword">byte</span>[] incrementRow;

    <span class="hljs-keyword">public</span> <span class="hljs-title">XXXHbaseEventSerializer</span>() {

    }

    <span class="hljs-annotation">@Override</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">configure</span>(Context context) {
        <span class="hljs-keyword">this</span>.levelCol = <span class="hljs-string">"l"</span>.getBytes(Charsets.UTF_8);
        <span class="hljs-keyword">this</span>.plCol = <span class="hljs-string">"m"</span>.getBytes(Charsets.UTF_8);
        <span class="hljs-keyword">this</span>.incCol = <span class="hljs-string">"iCol"</span>.getBytes(Charsets.UTF_8);
        incrementRow = <span class="hljs-string">"incRow"</span>.getBytes(Charsets.UTF_8);
    }

    <span class="hljs-annotation">@Override</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">configure</span>(ComponentConfiguration conf) {
    }

    <span class="hljs-annotation">@Override</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">initialize</span>(Event event, <span class="hljs-keyword">byte</span>[] cf) {
        <span class="hljs-keyword">this</span>.cf = cf;

        Map&lt;String, String&gt; headers = event.getHeaders();
        <span class="hljs-javadoc">/**
         * 日志级别，10000表示DEBUG；20000表示INFO；30000表示WARN；40000表示ERROR
         */</span>
        <span class="hljs-keyword">this</span>.level = LOG_LEVEL.get(headers.get(<span class="hljs-string">"flume.client.log4j.log.level"</span>));
        <span class="hljs-javadoc">/**
         * 系统ID
         */</span>
        <span class="hljs-keyword">this</span>.systemId = headers.get(<span class="hljs-string">"flume.client.log4j.logger.name"</span>);
        <span class="hljs-javadoc">/**
         * 时间戳，格式：1421995677371
         */</span>
        <span class="hljs-keyword">this</span>.timestamp = headers.get(<span class="hljs-string">"flume.client.log4j.timestamp"</span>);
        <span class="hljs-javadoc">/**
         * 日志内容
         */</span>
        <span class="hljs-keyword">this</span>.payload = event.getBody();

<span class="hljs-comment">//      /**</span>
<span class="hljs-comment">//       * 转换成String对象后，希望得到的消息结构：&lt;systemId&gt;\t&lt;日志内容&gt;</span>
<span class="hljs-comment">//       */</span>
<span class="hljs-comment">//      String body = new String(event.getBody(), Charsets.UTF_8);</span>
<span class="hljs-comment">//      int index = body.indexOf("\t");</span>
<span class="hljs-comment">//      </span>
<span class="hljs-comment">//      if (index == -1) {</span>
<span class="hljs-comment">//          this.systemId = "default";</span>
<span class="hljs-comment">//          this.payload = body.getBytes(Charsets.UTF_8);</span>
<span class="hljs-comment">//      } else {</span>
<span class="hljs-comment">//          this.systemId = body.substring(0, index);</span>
<span class="hljs-comment">//          this.payload = body.substring(index + 1).getBytes(Charsets.UTF_8);</span>
<span class="hljs-comment">//      }</span>
    }

    <span class="hljs-annotation">@Override</span>
    <span class="hljs-keyword">public</span> List&lt;Row&gt; <span class="hljs-title">getActions</span>() <span class="hljs-keyword">throws</span> FlumeException {
        List&lt;Row&gt; actions = <span class="hljs-keyword">new</span> LinkedList&lt;Row&gt;();
        <span class="hljs-keyword">if</span> (plCol != <span class="hljs-keyword">null</span>) {
            <span class="hljs-keyword">byte</span>[] rowKey;
            <span class="hljs-keyword">try</span> {
                rowKey = XXXRowKeyGenerator.getRowKey(systemId, timestamp);
                Put put = <span class="hljs-keyword">new</span> Put(rowKey);
                put.add(cf, plCol, payload);
                put.add(cf, levelCol, level);
                actions.add(put);
            } <span class="hljs-keyword">catch</span> (Exception e) {
                <span class="hljs-keyword">throw</span> <span class="hljs-keyword">new</span> FlumeException(<span class="hljs-string">"Could not get row key!"</span>, e);
            }

        }
        <span class="hljs-keyword">return</span> actions;
    }

    <span class="hljs-annotation">@Override</span>
    <span class="hljs-keyword">public</span> List&lt;Increment&gt; <span class="hljs-title">getIncrements</span>() {
        List&lt;Increment&gt; incs = <span class="hljs-keyword">new</span> LinkedList&lt;Increment&gt;();
        <span class="hljs-keyword">if</span> (incCol != <span class="hljs-keyword">null</span>) {
            Increment inc = <span class="hljs-keyword">new</span> Increment(incrementRow);
            inc.addColumn(cf, incCol, <span class="hljs-number">1</span>);
            incs.add(inc);
        }
        <span class="hljs-keyword">return</span> incs;
    }

    <span class="hljs-annotation">@Override</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">close</span>() {
    }</code></pre>

<p>配置文件修改一行：</p>



<pre class="prettyprint"><code class=" hljs avrasm">up-agent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.hbaseSink</span><span class="hljs-preprocessor">.serializer</span> = xxx<span class="hljs-preprocessor">.xxx</span><span class="hljs-preprocessor">.xxx</span><span class="hljs-preprocessor">.xxx</span><span class="hljs-preprocessor">.XXXHbaseEventSerializer</span></code></pre>

<p>说明：</p>

<ul>
<li>根据自己的需求配置flume的配置文件，可以多查查，自己先想好自己的flume拓扑结构，根据这个去配置flume</li>
<li>flume配置比较多，功能也比较强大，可以参照flume的用户手册</li>
</ul>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>