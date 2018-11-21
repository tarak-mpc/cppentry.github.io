---
layout:     post
title:      flume日志写入hbase问题记录
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="flume日志写入hbase问题记录">flume日志写入hbase问题记录</h1>

<p>标签（空格分隔）： flume</p>

<hr>

<blockquote>
  <p>flume使用kafkachannel，source为avro，应用通过logback写入</p>
</blockquote>



<h2 id="logback-flume-appender数据发送body内容异常">logback-flume-appender数据发送body内容异常</h2>

<blockquote>
  <p>这个问题在单进程写日志的时候不会出现，在2个以上的进程时出现event的body数据异常，导致flume的自定义sink中无法解析数据 <br>
  原因是body中在正常的数据末尾，出现很多空串或者其他的数据，导致解析失败。 <br>
  分析日志后，发现header中的message数据正常，但body出现问题。怀疑是layout格式化时出现的问题，去掉logback.xml中的layout节点后，数据不再出现异常。</p>
</blockquote>



<h2 id="kafka未自动删除队列数据">kafka未自动删除队列数据</h2>

<blockquote>
  <p>原设置时间太长kafka修改配置log.retention.hours=2</p>
</blockquote>



<h2 id="haproxy占用率提升不上去">haproxy占用率提升不上去</h2>

<blockquote>
  <p>haproxy的cpu占用率一直卡在25左右上不去，因为haproxy默认是单核启动 <br>
  根据内核数量加上全局配置nbproc 4后解决</p>
</blockquote>



<h2 id="flume日志报错-commit-cannot-be-completed-due-to-group-rebalance">flume日志报错 Commit cannot be completed due to group rebalance</h2>

<blockquote>
  <p><a href="http://flume.cn/2017/03/23/flume1-7%E4%BD%BF%E7%94%A8KafkaSource%E9%87%87%E9%9B%86%E5%A4%A7%E9%87%8F%E6%95%B0%E6%8D%AE/" rel="nofollow">解决办法</a> <br>
  <a href="http://orchome.com/537" rel="nofollow">kafka参数说明</a> <br>
  flume消费者配置了两个参数</p>
</blockquote>



<pre class="prettyprint"><code class=" hljs avrasm">heartbeat<span class="hljs-preprocessor">.interval</span><span class="hljs-preprocessor">.ms</span>=<span class="hljs-number">10000</span>
session<span class="hljs-preprocessor">.timeout</span><span class="hljs-preprocessor">.ms</span>=<span class="hljs-number">30000</span></code></pre>

<blockquote>
  <p>flume中：</p>
</blockquote>



<pre class="prettyprint"><code class=" hljs avrasm">bft-agent<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.bft</span>-channel<span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.consumer</span><span class="hljs-preprocessor">.heartbeat</span><span class="hljs-preprocessor">.interval</span><span class="hljs-preprocessor">.ms</span> = <span class="hljs-number">10000</span>
<span class="hljs-preprocessor">#不能小于group.min.session.timeout.ms，不能大于group.max.session.timeout.ms</span>
bft-agent<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.bft</span>-channel<span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.consumer</span><span class="hljs-preprocessor">.session</span><span class="hljs-preprocessor">.timeout</span><span class="hljs-preprocessor">.ms</span> = <span class="hljs-number">30000</span></code></pre>

<blockquote>
  <p>kafka配置文件server.properties中配置</p>
</blockquote>



<pre class="prettyprint"><code class=" hljs avrasm">group<span class="hljs-preprocessor">.max</span><span class="hljs-preprocessor">.session</span><span class="hljs-preprocessor">.timeout</span><span class="hljs-preprocessor">.ms</span>=<span class="hljs-number">30000</span></code></pre>



<h2 id="flume集成hdfs-ha">flume集成hdfs ha</h2>

<blockquote>
  <p>将hadoop集群的core-site.xml和hdfs-site.xml放在flume的conf目录下</p>
</blockquote>



<h2 id="缺包问题">缺包问题</h2>

<blockquote>
  <p>flume单独部署时，可能会缺少hadoop和hbase相关的包，可以拷贝hadoop和hbase的文件到目录中，并配置环境变量解决</p>
</blockquote>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>