---
layout:     post
title:      Hadoop(十一)flume把数据写入kafka
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/mingyunxiaohai/article/details/80857439				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>使用flume把数据写入kafka，需要改变flume配置文件中的sinks属性</p>

<p>进入flume安装文件的conf下创建一个<code>.properties</code>文件 这里创建 c.properties，并对其进行配置</p>



<pre class="prettyprint"><code class=" hljs avrasm">ak<span class="hljs-preprocessor">.sources</span> = mysource
ak<span class="hljs-preprocessor">.channels</span> = mychannel
ak<span class="hljs-preprocessor">.sinks</span> = mysink

ak<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.mysource</span><span class="hljs-preprocessor">.type</span> = spooldir
ak<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.mysource</span><span class="hljs-preprocessor">.channels</span> = mychannel
ak<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.mysource</span><span class="hljs-preprocessor">.spoolDir</span> =/tmp/logs
ak<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.mysink</span><span class="hljs-preprocessor">.channel</span> = mychannel


ak<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.mysink</span><span class="hljs-preprocessor">.channel</span> = mychannel
ak<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.mysink</span><span class="hljs-preprocessor">.type</span> = org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.sink</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.KafkaSink</span>
ak<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.mysink</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.topic</span> = flume-data
ak<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.mysink</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.bootstrap</span><span class="hljs-preprocessor">.servers</span> = localhost:<span class="hljs-number">9092</span>
ak<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.mysink</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.flumeBatchSize</span> = <span class="hljs-number">20</span>
ak<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.mysink</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.producer</span><span class="hljs-preprocessor">.acks</span> = <span class="hljs-number">1</span>
ak<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.mysink</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.producer</span><span class="hljs-preprocessor">.linger</span><span class="hljs-preprocessor">.ms</span> = <span class="hljs-number">1</span>
ak<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.mysink</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.producer</span><span class="hljs-preprocessor">.compression</span><span class="hljs-preprocessor">.type</span> = snappy


ak<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.mychannel</span><span class="hljs-preprocessor">.type</span> = memory
ak<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.mychannel</span><span class="hljs-preprocessor">.capacity</span> = <span class="hljs-number">10000</span>
ak<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.mychannel</span><span class="hljs-preprocessor">.transactionCapacity</span> = <span class="hljs-number">100</span></code></pre>

<p>配置完之后进入flume安装目录启动flume</p>



<pre class="prettyprint"><code class=" hljs brainfuck"><span class="hljs-comment">bin/flume</span><span class="hljs-literal">-</span><span class="hljs-comment">ng</span> <span class="hljs-comment">agent</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">conf</span> <span class="hljs-comment">conf</span>  <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">conf</span><span class="hljs-literal">-</span><span class="hljs-comment">file</span> <span class="hljs-comment">conf/c</span><span class="hljs-string">.</span><span class="hljs-comment">properties</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">name</span> <span class="hljs-comment">ak</span> <span class="hljs-literal">-</span><span class="hljs-comment">Dflume</span><span class="hljs-string">.</span><span class="hljs-comment">root</span><span class="hljs-string">.</span><span class="hljs-comment">logger=DEBUG</span><span class="hljs-string">,</span><span class="hljs-comment">console</span></code></pre>

<p>进入kafka安装目录启动kafka</p>



<pre class="prettyprint"><code class=" hljs lasso"><span class="hljs-comment">//先启动 Zookeeper</span>
bin/zookeeper<span class="hljs-attribute">-server</span><span class="hljs-attribute">-start</span><span class="hljs-built_in">.</span>sh <span class="hljs-attribute">-daemon</span> config/zookeeper<span class="hljs-built_in">.</span>properties
<span class="hljs-comment">//在启动 kafka</span>
bin/kafka<span class="hljs-attribute">-server</span><span class="hljs-attribute">-start</span><span class="hljs-built_in">.</span>sh <span class="hljs-attribute">-daemon</span> config/server<span class="hljs-built_in">.</span>properties</code></pre>

<p>创建toplic</p>

<pre class="prettyprint"><code class=" hljs brainfuck"><span class="hljs-comment">bin/kafka</span><span class="hljs-literal">-</span><span class="hljs-comment">topics</span><span class="hljs-string">.</span><span class="hljs-comment">sh</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">create</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">zookeeper</span> <span class="hljs-comment">localhost:2181</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">replication</span><span class="hljs-literal">-</span><span class="hljs-comment">factor</span> <span class="hljs-comment">1</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">partitions</span> <span class="hljs-comment">1</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">topic</span> <span class="hljs-comment">flume</span><span class="hljs-literal">-</span><span class="hljs-comment">data</span></code></pre>

<p>新建一个窗口开启消费者等待数据</p>

<pre class="prettyprint"><code class=" hljs lasso">bin/kafka<span class="hljs-attribute">-console</span><span class="hljs-attribute">-consumer</span><span class="hljs-built_in">.</span>sh <span class="hljs-subst">--</span>bootstrap<span class="hljs-attribute">-server</span> localhost:<span class="hljs-number">9092</span> <span class="hljs-subst">--</span>topic flume<span class="hljs-attribute">-data</span> <span class="hljs-subst">--</span>from<span class="hljs-attribute">-beginning</span></code></pre>

<p>上面的flume 配置的是监听 /tmp/logs中的数据，所以我们新建一个文件复制到/tmp/logs中</p>

<p>新建test文件 里面写点数据</p>

<pre class="prettyprint"><code class=" hljs ">hadoop ni hao
hello java
hello flume
hello kafka </code></pre>

<p>把test复制到/tmp/logs中</p>

<p>去消费者窗口查看可以看到</p>



<pre class="prettyprint"><code class=" hljs brainfuck"><span class="hljs-title">[</span><span class="hljs-comment">chs@master</span> <span class="hljs-comment">kafka_2</span><span class="hljs-string">.</span><span class="hljs-comment">11</span><span class="hljs-literal">-</span><span class="hljs-comment">1</span><span class="hljs-string">.</span><span class="hljs-comment">1</span><span class="hljs-string">.</span><span class="hljs-comment">0</span><span class="hljs-title">]</span><span class="hljs-comment">$</span> <span class="hljs-comment">bin/kafka</span><span class="hljs-literal">-</span><span class="hljs-comment">console</span><span class="hljs-literal">-</span><span class="hljs-comment">consumer</span><span class="hljs-string">.</span><span class="hljs-comment">sh</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">bootstrap</span><span class="hljs-literal">-</span><span class="hljs-comment">server</span> <span class="hljs-comment">localhost:9092</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">topic</span> <span class="hljs-comment">flume</span><span class="hljs-literal">-</span><span class="hljs-comment">data</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">from</span><span class="hljs-literal">-</span><span class="hljs-comment">beginning</span>
<span class="hljs-comment">hadoop</span> <span class="hljs-comment">ni</span> <span class="hljs-comment">hao</span> 
<span class="hljs-comment">hadoop</span> <span class="hljs-comment">ni</span> <span class="hljs-comment">hao</span>
<span class="hljs-comment">hello</span> <span class="hljs-comment">java</span>
<span class="hljs-comment">hello</span> <span class="hljs-comment">flume</span>
<span class="hljs-comment">hello</span> <span class="hljs-comment">kafka</span> 
</code></pre>

<p>OK成功</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>