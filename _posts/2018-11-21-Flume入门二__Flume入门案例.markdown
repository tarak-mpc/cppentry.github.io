---
layout:     post
title:      Flume入门二__Flume入门案例
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h4 id="1-下载并解压flume">1. 下载并解压Flume</h4>

<pre class="prettyprint"><code class=" hljs ruby">[root<span class="hljs-variable">@langzi01</span> ~]<span class="hljs-comment"># wget http://www.apache.org/dyn/closer.lua/flume/1.8.0/apache-flume-1.8.0-bin.tar.gz</span>
[root<span class="hljs-variable">@langzi01</span> ~]<span class="hljs-comment"># tar -zxvf apache-flume-1.8.0-bin.tar.gz</span>
<span class="hljs-comment">#重命名</span>
[root<span class="hljs-variable">@langzi01</span> ~]<span class="hljs-comment"># mv apache-flume-1.8.0-bin /usr/soft/flume</span></code></pre>

<h4 id="2-修改配置文件">2. 修改配置文件</h4>



<pre class="prettyprint"><code class=" hljs ruby">[hadoop<span class="hljs-variable">@langzi01</span> conf]<span class="hljs-variable">$ </span>pwd
<span class="hljs-comment">#/usr/soft/flume/conf</span>
[hadoop<span class="hljs-variable">@langzi01</span> conf]<span class="hljs-variable">$ </span>cp flume-conf.properties.template flume.conf
[hadoop<span class="hljs-variable">@langzi01</span> conf]<span class="hljs-variable">$ </span>vim flume.conf </code></pre>

<p>内容如下：</p>

<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-preprocessor"># example.conf: A single-node Flume configuration</span>

<span class="hljs-preprocessor"># Name the components on this agent</span>
a1<span class="hljs-preprocessor">.sources</span> = <span class="hljs-built_in">r1</span>
a1<span class="hljs-preprocessor">.sinks</span> = k1
a1<span class="hljs-preprocessor">.channels</span> = c1

<span class="hljs-preprocessor"># Describe/configure the source</span>
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.type</span> = netcat
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.bind</span> = localhost
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.port</span> = <span class="hljs-number">44444</span>

<span class="hljs-preprocessor"># Describe the sink</span>
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.type</span> = logger

<span class="hljs-preprocessor"># Use a channel which buffers events in memory</span>
a1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.type</span> = memory
a1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.capacity</span> = <span class="hljs-number">1000</span>
a1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.transactionCapacity</span> = <span class="hljs-number">100</span>

<span class="hljs-preprocessor"># Bind the source and sink to the channel</span>
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.channels</span> = c1
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.channel</span> = c1</code></pre>

<h4 id="3-启动flume">3. 启动flume</h4>



<pre class="prettyprint"><code class=" hljs brainfuck"><span class="hljs-title">[</span><span class="hljs-comment">hadoop@langzi01</span> <span class="hljs-comment">conf</span><span class="hljs-title">]</span><span class="hljs-comment">$</span> <span class="hljs-string">.</span><span class="hljs-string">.</span><span class="hljs-comment">/bin/flume</span><span class="hljs-literal">-</span><span class="hljs-comment">ng</span> <span class="hljs-comment">agent</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">conf</span> <span class="hljs-comment">conf</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">conf</span><span class="hljs-literal">-</span><span class="hljs-comment">file</span> <span class="hljs-comment">flume</span><span class="hljs-literal">-</span><span class="hljs-comment">conf</span><span class="hljs-string">.</span><span class="hljs-comment">properties</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">name</span> <span class="hljs-comment">a1</span> <span class="hljs-literal">-</span><span class="hljs-comment">Dflume</span><span class="hljs-string">.</span><span class="hljs-comment">root</span><span class="hljs-string">.</span><span class="hljs-comment">logger=INFO</span><span class="hljs-string">,</span><span class="hljs-comment">console</span></code></pre>

<h4 id="4-链接flume">4. 链接flume</h4>



<pre class="prettyprint"><code class=" hljs r">$ telnet localhost <span class="hljs-number">44444</span>
Trying <span class="hljs-number">127.0</span><span class="hljs-number">.0</span><span class="hljs-number">.1</span><span class="hljs-keyword">...</span>
Connected to localhost.localdomain (<span class="hljs-number">127.0</span><span class="hljs-number">.0</span><span class="hljs-number">.1</span>).
Escape character is <span class="hljs-string">'^]'</span>.
Hello world! &lt;ENTER&gt;
OK</code></pre>

<p><img src="https://img-blog.csdn.net/2018091011475714?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2d1bzIwMDgyMjAw/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<h4 id="5-flume接收的消息">5. flume接收的消息</h4>

<p><img src="https://img-blog.csdn.net/20180910114705602?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2d1bzIwMDgyMjAw/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>