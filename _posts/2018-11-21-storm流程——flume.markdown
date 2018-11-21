---
layout:     post
title:      storm流程——flume
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：欢迎转载					https://blog.csdn.net/Levi_moon/article/details/51637119				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>flume：实时日志收集系统，是日志收集之王。 <br>
flume的特性： <br>
（1）可靠性 <br>
（2）可伸缩性 <br>
（3）高性能 <br>
（4）可延展性 <br>
（5）开源社区的支持 <br>
flume的概念： <br>
（1）agent——&gt;使用JVM 运行flume，每台机器运行一个agent，但是可以在一个 agent中包含多个sources和sinks。 <br>
（2）client——&gt;生产数据，运行在一个独立的线程。 <br>
（3）source——&gt;从client收集数据，传递给channel。 <br>
（4）channel——&gt;连接 sources 和 sinks 。 <br>
（5）sink——&gt;从channel收集数据，运行在一个独立线程。 <br>
（6）events——&gt;是flume的数据处理单元，可以是日志，avro，spool等。 <br>
flume的数据流图： <br>
<img src="https://img-blog.csdn.net/20160611170028680" alt="流程图" title=""> <br>
<strong>flume的例子</strong> <br>
（以exec为例） <br>
1.（1）先在flume的conf目录下创建一个exec.conf文件，</p>

<pre class="prettyprint"><code class=" hljs python">vi conf/<span class="hljs-keyword">exec</span>.conf</code></pre>

<p>（2）在exec.conf文件中输入以下内容：</p>

<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-preprocessor"># Name the components on this agent</span>
agent1<span class="hljs-preprocessor">.sources</span> = <span class="hljs-built_in">r1</span>
agent1<span class="hljs-preprocessor">.sinks</span> = k1
agent1<span class="hljs-preprocessor">.channels</span> = c1

<span class="hljs-preprocessor">#define source monitor a file</span>
agent1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.type</span> = exec
<span class="hljs-preprocessor">#（定义数据源的地址及查看数据源的方式为：tail  -n +0 -F /home/storm/work/access.log ）</span>
agent1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.command</span> = tail  -n +<span class="hljs-number">0</span> -F /home/storm/work/access<span class="hljs-preprocessor">.log</span> 
agent1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.channels</span> = c1

<span class="hljs-preprocessor"># Describe the sink</span>

<span class="hljs-preprocessor"># Each sink's type must be defined</span>
agent1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.type</span> = file_roll
<span class="hljs-preprocessor">#（定义flume的输出地址为：/home/storm/flume/data）</span>
agent1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.sink</span><span class="hljs-preprocessor">.directory</span>=/home/storm/flume/data
agent1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.sink</span><span class="hljs-preprocessor">.serializer</span> = text
agent1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.sink</span><span class="hljs-preprocessor">.serializer</span><span class="hljs-preprocessor">.appendNewline</span> = true

<span class="hljs-preprocessor">#a21.sinks.r2.sink.rollInterval = 3600</span>
agent1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.sink</span><span class="hljs-preprocessor">.rollInterval</span> = <span class="hljs-number">43200</span>
agent1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.channel</span> = c1

<span class="hljs-preprocessor"># Use a channel which buffers events in memory</span>
agent1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.type</span> = memory
agent1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.capacity</span> = <span class="hljs-number">1000</span>
agent1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.transactionCapacity</span> = <span class="hljs-number">100</span></code></pre>

<p>2.启动flume</p>

<pre class="prettyprint"><code class=" hljs lasso">bin/flume<span class="hljs-attribute">-ng</span> agent <span class="hljs-attribute">-c</span> conf <span class="hljs-attribute">-n</span> agent1 <span class="hljs-attribute">-f</span> conf/exec<span class="hljs-built_in">.</span>conf  <span class="hljs-attribute">-Dflume</span><span class="hljs-built_in">.</span>root<span class="hljs-built_in">.</span>logger<span class="hljs-subst">=</span>INFO,console <span class="hljs-subst">&gt;&gt;</span> logs/flume<span class="hljs-built_in">.</span><span class="hljs-keyword">log</span> <span class="hljs-number">2</span><span class="hljs-subst">&gt;&amp;</span><span class="hljs-number">1</span> <span class="hljs-subst">&amp;</span></code></pre>

<p>参数说明： <br>
-c  conf 指定配置目录为conf。 <br>
-f conf/exec.conf指定配置文件为conf/avro.conf     (注意路径，当前路径与配置所指的路径)。 <br>
-n agent1 指定agent名字为agent1,需要与exec.conf中的一致。 <br>
-Dflume.root.logger=INFO,console 指定DEBUF模式在console输出INFO信息。 <br>
“&gt;&gt;logs/flume.log 2&gt;&amp;1 &amp; “指的是把输出的日志信息追加到logs/flume.log。</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>