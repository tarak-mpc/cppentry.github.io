---
layout:     post
title:      【Java】Appache Flume 中文介绍
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div>﻿﻿</div>
<p><strong>Flume 是什么</strong></p>
<p><strong>       </strong>Apache Flume是一个高可靠、高可用的分布式的海量日志收集、聚合、传输系统。它可以从不同的日志源采集数据并集中存储。</p>
<p>Flume也算是Hadoop生态系统的一部分，源于Cloudera，目前是Apache基金会的顶级项目之一。Flume有两条产品线，0.9.x版本和1.x版本。</p>
<p>官网:http://flume.appache.org/</p>
<p></p>
<ul><li>收集、聚合事件流数据的分布式框架</li><li>通常用于log数据</li><li>采用ad-hoc方案，明显优点如下：
<ul><li>可靠的、可伸缩、可管理、可定制、高性能</li><li>声明式配置，可以动态更新配置</li><li>提供上下文路由功能</li><li>支持负载均衡和故障转移</li><li>功能丰富</li><li>完全的可扩展</li></ul></li></ul><strong>核心概念</strong><br><p></p>
<p></p>
<ul><li><strong>Event</strong></li><li><strong>Client</strong></li><li><strong>Agent</strong>
<ul><li><strong>Sources、Channels、Sinks</strong></li><li><strong>其他组件：Interceptors、Channel Selectors、Sink Processor</strong></li></ul></li></ul><strong>核心概念：Event<br></strong>
<p></p>
<blockquote style="border:;">
<p><em>Event是Flume数据传输的基本单元。flume以事件的形式将数据从源头传送到最终的目的。Event由可选的hearders和载有数据的一个byte array构成。</em></p>
</blockquote>
<ul><li>载有的数据对flume是不透明的</li><li>Headers是容纳了key-value字符串对的无序集合，key在集合内是唯一的。</li><li>Headers可以在上下文路由中使用扩展</li></ul><div class="dp-highlighter bg_java">
<div class="bar">
<div class="tools"><strong>[java]</strong> <a title="view plain" class="ViewSource" href="http://blog.csdn.net/xiaochawan/article/details/8986489#" rel="nofollow">
view plain</a><a title="copy" class="CopyToClipboard" href="http://blog.csdn.net/xiaochawan/article/details/8986489#" rel="nofollow">copy</a><a title="print" class="PrintSource" href="http://blog.csdn.net/xiaochawan/article/details/8986489#" rel="nofollow">print</a><a title="?" class="About" href="http://blog.csdn.net/xiaochawan/article/details/8986489#" rel="nofollow">?</a></div>
</div>
<ol class="dp-j"><li class="alt"><span><span class="keyword">public</span><span> </span><span class="keyword">interface</span><span> Event {  </span></span></li><li><span>    <span class="keyword">public</span><span> Map&lt;String, String&gt; getHeaders();  </span></span></li><li class="alt"><span>    <span class="keyword">public</span><span> </span><span class="keyword">void</span><span> setHeaders(Map&lt;String, String&gt; headers);  </span></span></li><li><span>    <span class="keyword">public</span><span> </span><span class="keyword">byte</span><span>[] getBody();  </span></span></li><li class="alt"><span>    <span class="keyword">public</span><span> </span><span class="keyword">void</span><span> setBody(</span><span class="keyword">byte</span><span>[] body);  </span></span></li><li><span>}  </span></li></ol></div>
<pre><code class="language-java">public interface Event {
    public Map&lt;String, String&gt; getHeaders();
    public void setHeaders(Map&lt;String, String&gt; headers);
    public byte[] getBody();
    public void setBody(byte[] body);
}
</code></pre><br><p><strong>核心概念：Client</strong></p>
<blockquote style="border:;">
<p><em>Clinet是一个将原始log包装成events并且发送它们到一个或多个agent的实体。</em></p>
</blockquote>
<ul><li>例如
<ul><li>Flume log4j Appender</li><li>可以使用Client SDK (org.apache.flume.api)定制特定的Client</li></ul></li><li>目的是从数据源系统中解耦Flume</li><li>在flume的拓扑结构中不是必须的</li></ul><p><strong>核心概念：Agent</strong></p>
<blockquote style="border:;">
<p><em>一个Agent包含Sources, Channels, Sinks和其他组件，它利用这些组件将events从一个节点传输到另一个节点或最终目的。</em></p>
</blockquote>
<ul><li>agent是flume流的基础部分。</li><li>flume为这些组件提供了配置、生命周期管理、监控支持。</li></ul><p><strong>核心概念：Source<br></strong></p>
<blockquote style="border:;">
<p><em>Source负责接收events或通过特殊机制产生events，并将events批量的放到一个或多个Channels。有event驱动和轮询2种类型的Source</em></p>
</blockquote>
<p></p>
<ul><li>不同类型的Source:
<ul><li>和众所周知的系统集成的Sources: Syslog, Netcat</li><li>自动生成事件的Sources: Exec, SEQ</li><li>用于Agent和Agent之间通信的IPC Sources: Avro</li></ul></li><li>Source必须至少和一个channel关联</li></ul><strong>核心概念：Channel</strong><br><p></p>
<blockquote style="border:;">
<p><em>Channel位于Source和Sink之间，用于缓存进来的events，当Sink成功的将events发送到下一跳的channel或最终目的，events从Channel移除。</em></p>
</blockquote>
<p></p>
<ul><li>不同的Channels提供的持久化水平也是不一样的:
<ul><li>Memory Channel: volatile</li><li>File Channel: 基于WAL（预写式日志Write-Ahead Logging）实现</li><li>JDBC Channel: 基于嵌入Database实现</li></ul></li><li>Channels支持事务</li><li>提供较弱的顺序保证</li><li>可以和任何数量的Source和Sink工作</li></ul><p></p>
<p><strong>核心概念：Sink<br></strong></p>
<blockquote style="border:;">
<p><em>Sink负责将events传输到下一跳或最终目的，成功完成后将events从channel移除。</em></p>
</blockquote>
<p></p>
<ul><li>不同类型的Sinks:
<ul><li>存储events到最终目的的终端Sink. 比如: HDFS, HBase</li><li>自动消耗的Sinks. 比如: Null Sink</li><li>用于Agent间通信的IPC sink: Avro</li></ul></li><li>必须作用与一个确切的channel</li></ul><strong>Flow可靠性</strong><br><p></p>
<p><strong>                      <img alt="" src="https://img-blog.csdn.net/20130528210847140"><br></strong></p>
<p><strong>               <img alt="" src=""><br></strong></p>
<p></p>
<ul><li>可靠性基于:
<ul><li>Agent间事务的交换</li><li>Flow中，Channel的持久特性</li></ul></li><li>可用性:
<ul><li>内建的Load balancing支持</li><li>内建的Failover支持</li></ul></li></ul><div><img alt="" src="https://img-blog.csdn.net/20130528210839405"><br></div>
<img alt="" src=""><br><p></p>
<p><strong>核心概念：Interceptor</strong><br></p>
<blockquote style="border:;">
<p><em>用于Source的一组Interceptor，按照预设的顺序在必要地方装饰和过滤events。</em></p>
</blockquote>
<p></p>
<ul><li>内建的Interceptors允许增加event的headers比如：时间戳、主机名、静态标记等等</li><li>定制的interceptors可以通过内省event payload（读取原始日志），在必要的地方创建一个特定的headers。</li></ul><strong>核心概念：Channel Selector</strong><br><p></p>
<blockquote style="border:;">
<p><em>Channel Selector允许Source基于预设的标准，从所有Channel中，选择一个或多个Channel</em></p>
</blockquote>
<p></p>
<ul><li>内建的Channel Selectors:
<ul><li>复制Replicating: event被复制到相关的channel</li><li>复用Multiplexing: 基于hearder，event被路由到特定的channel</li></ul></li></ul><div><strong>核心概念：Sink Processor</strong><br></div>
<p></p>
<blockquote style="border:;">
<p></p>
<div><em>多个Sink可以构成一个Sink Group。一个Sink Processor负责从一个指定的Sink Group中激活一个Sink。Sink Processor可以通过组中所有Sink实现负载均衡；也可以在一个Sink失败时转移到另一个。</em></div>
<p></p>
</blockquote>
<p></p>
<ul><li>Flume通过Sink Processor实现负载均衡（Load Balancing）和故障转移（failover）</li><li>内建的Sink Processors:
<ul><li>Load Balancing Sink Processor – 使用RANDOM, ROUND_ROBIN或定制的选择算法</li><li>Failover Sink Processor </li><li>Default Sink Processor（单Sink）</li></ul></li><li>所有的Sink都是采取轮询（polling）的方式从Channel上获取events。这个动作是通过Sink Runner激活的</li><li>Sink Processor充当Sink的一个代理<br><br><img alt="" src=""><br></li></ul><div><strong>总结</strong></div>
<div><img alt="" src=""><br></div>
<p><img width="907" height="377" alt="" src="https://img-blog.csdn.net/20130528210638437"></p>
<p><br></p>
<p><span style="font-family:'Microsoft YaHei';"><img alt="" src="http://dl.iteye.com/upload/attachment/0071/7379/e8fd34bb-0ba3-3a23-9482-804e4e541ecf.png"></span></p>
<p><span style="font-family:'Microsoft YaHei';"><br></span></p>
<p><span style="font-family:'Microsoft YaHei';"><img alt="" src=""><img alt="" src=""><img alt="Two agents communicating over Avro RPC" src="http://flume.apache.org/_images/UserGuide_image03.png"></span></p>
<p><span style="font-family:'Microsoft YaHei';"><br></span></p>
<p><span style="font-family:'Microsoft YaHei';"><img alt="" src=""><img alt="" src=""><img alt="" src=""><img alt="A fan-in flow using Avro RPC to consolidate events in one place" src="http://flume.apache.org/_images/UserGuide_image02.png"></span></p>
<p><span style="font-family:'Microsoft YaHei';"><img alt="" src=""></span></p>
<p><span style="font-family:'Microsoft YaHei';"><br></span></p>
<p>﻿﻿<img src="" alt=""><img alt="" src="https://img-blog.csdn.net/20130528222353365"></p>
            </div>
                </div>