---
layout:     post
title:      kafka基本操作(一) linux环境下一个broker
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/csdn565973850/article/details/79994696				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="kafka基本操作一-linux环境下一个broker">kafka基本操作(一) linux环境下一个broker</h1>

<p><strong>1.下载：</strong> <br>
在kafka官网 <a href="http://kafka.apache.org/downloads" rel="nofollow">http://kafka.apache.org/downloads</a>下载到最新的kafka安装包</p>

<p><strong>2.启动服务：</strong></p>

<ul>
<li>首先启动zookeeper服务：</li>
</ul>



<pre class="prettyprint"><code class=" hljs bash">//<span class="hljs-built_in">cd</span>进入kafka解压目录，输入
[root@localhost kafka_2.<span class="hljs-number">11</span>-<span class="hljs-number">1.1</span>.<span class="hljs-number">0</span>]<span class="hljs-comment">#./bin/zookeeper-server-start.sh config/zookeeper.properties</span></code></pre>

<ul>
<li>启动kafka:</li>
</ul>



<pre class="prettyprint"><code class=" hljs bash">//<span class="hljs-built_in">cd</span>进入kafka解压目录，输入
[root@localhost kafka_2.<span class="hljs-number">11</span>-<span class="hljs-number">1.1</span>.<span class="hljs-number">0</span>]<span class="hljs-comment">#./bin/kafka-server-start.sh config/server.properties</span></code></pre>

<p>Kafka通过topic对同一类的数据进行管理，同一类的数据使用同一个topic可以在处理数据时更加方便快捷。</p>

<p><strong>3.新建一个topic:</strong></p>



<pre class="prettyprint"><code class=" hljs brainfuck"><span class="hljs-comment">//cd进入kafka解压目录，输入</span>
<span class="hljs-title">[</span><span class="hljs-comment">root@localhost</span> <span class="hljs-comment">kafka_2</span><span class="hljs-string">.</span><span class="hljs-comment">11</span><span class="hljs-literal">-</span><span class="hljs-comment">1</span><span class="hljs-string">.</span><span class="hljs-comment">1</span><span class="hljs-string">.</span><span class="hljs-comment">0</span><span class="hljs-title">]</span><span class="hljs-comment">#</span><span class="hljs-string">.</span><span class="hljs-comment">/bin/kafka</span><span class="hljs-literal">-</span><span class="hljs-comment">topics</span><span class="hljs-string">.</span><span class="hljs-comment">sh</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">create</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">zookeeper</span> <span class="hljs-comment">localhost:2181</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">replication</span><span class="hljs-literal">-</span><span class="hljs-comment">factor</span> <span class="hljs-comment">1</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">partitions</span> <span class="hljs-comment">1</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">topic</span> <span class="hljs-comment">test01</span></code></pre>

<p><strong>4.查看已经存在的Topic列表:</strong></p>



<pre class="prettyprint"><code class=" hljs bash">//<span class="hljs-built_in">cd</span>进入kafka解压目录，输入
[root@localhost kafka_2.<span class="hljs-number">11</span>-<span class="hljs-number">1.1</span>.<span class="hljs-number">0</span>]<span class="hljs-comment">#./bin/kafka-topics.sh --list --zookeeper localhost:2181</span></code></pre>

<p><strong>5.新建一个消息生产者:</strong></p>



<pre class="prettyprint"><code class=" hljs bash">//<span class="hljs-built_in">cd</span>进入kafka解压目录，输入
[root@localhost kafka_2.<span class="hljs-number">11</span>-<span class="hljs-number">1.1</span>.<span class="hljs-number">0</span>]<span class="hljs-comment">#./bin/kafka-console-producer.sh --broker-list localhost:9092 --topic test01</span></code></pre>

<p><strong>6.新建一个消息消费者：</strong></p>



<pre class="prettyprint"><code class=" hljs brainfuck"><span class="hljs-comment">//cd进入kafka解压目录，输入</span>
<span class="hljs-title">[</span><span class="hljs-comment">root@localhost</span> <span class="hljs-comment">kafka_2</span><span class="hljs-string">.</span><span class="hljs-comment">11</span><span class="hljs-literal">-</span><span class="hljs-comment">1</span><span class="hljs-string">.</span><span class="hljs-comment">1</span><span class="hljs-string">.</span><span class="hljs-comment">0</span><span class="hljs-title">]</span><span class="hljs-comment">#</span><span class="hljs-string">.</span><span class="hljs-comment">/bin/kafka</span><span class="hljs-literal">-</span><span class="hljs-comment">console</span><span class="hljs-literal">-</span><span class="hljs-comment">consumer</span><span class="hljs-string">.</span><span class="hljs-comment">sh</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">bootstrap</span><span class="hljs-literal">-</span><span class="hljs-comment">server</span> <span class="hljs-comment">localhost:9092</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">topic</span> <span class="hljs-comment">test01</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">from</span><span class="hljs-literal">-</span><span class="hljs-comment">beginning</span></code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>