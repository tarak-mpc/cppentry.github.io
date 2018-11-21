---
layout:     post
title:      flume-ng demo单机配置详解
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p><a href="http://rjhym.blog.163.com/blog/static/28130232201263042013972/" rel="nofollow">http://rjhym.blog.163.com/blog/static/28130232201263042013972/</a></p>

<p><a href="https://cwiki.apache.org/confluence/display/FLUME/Getting+Started" rel="nofollow">https://cwiki.apache.org/confluence/display/FLUME/Getting+Started</a></p>

<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-preprocessor"># example.conf: A single-node Flume configuration</span>

<span class="hljs-preprocessor"># Name the components on this agent</span>
agent1<span class="hljs-preprocessor">.sources</span> = source1
agent1<span class="hljs-preprocessor">.sinks</span> = sink1
agent1<span class="hljs-preprocessor">.channels</span> = channel1

<span class="hljs-preprocessor"># Describe/configure source1</span>
agent1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.source</span>1<span class="hljs-preprocessor">.type</span> = netcat
agent1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.source</span>1<span class="hljs-preprocessor">.bind</span> = localhost
agent1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.source</span>1<span class="hljs-preprocessor">.port</span> = <span class="hljs-number">44444</span>

<span class="hljs-preprocessor"># Describe sink1</span>
agent1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.sink</span>1<span class="hljs-preprocessor">.type</span> = logger

<span class="hljs-preprocessor"># Use a channel which buffers events in memory</span>
agent1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.channel</span>1<span class="hljs-preprocessor">.type</span> = memory
agent1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.channel</span>1<span class="hljs-preprocessor">.capacity</span> = <span class="hljs-number">1000</span>
agent1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.channel</span>1<span class="hljs-preprocessor">.transactionCapactiy</span> = <span class="hljs-number">100</span>

<span class="hljs-preprocessor"># Bind the source and sink to the channel</span>
agent1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.source</span>1<span class="hljs-preprocessor">.channels</span> = channel1
agent1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.sink</span>1<span class="hljs-preprocessor">.channel</span> = channel1
</code></pre>

<p>启动命令：</p>



<pre class="prettyprint"><code class=" hljs lasso">bin/flume<span class="hljs-attribute">-ng</span> agent <span class="hljs-subst">--</span>conf <span class="hljs-built_in">.</span>/conf<span class="hljs-subst">/</span> <span class="hljs-attribute">-f</span> conf/flume<span class="hljs-built_in">.</span>conf <span class="hljs-attribute">-Dflume</span><span class="hljs-built_in">.</span>root<span class="hljs-built_in">.</span>logger<span class="hljs-subst">=</span>DEBUG,console <span class="hljs-attribute">-n</span> agent1</code></pre>

<p>启动后： <br>
<img src="https://img-blog.csdn.net/20160702094335783" alt="这里写图片描述" title=""> <br>
另起一个窗口 <br>
<img src="https://img-blog.csdn.net/20160702094346517" alt="这里写图片描述" title=""> <br>
<img src="https://img-blog.csdn.net/20160702094405252" alt="这里写图片描述" title=""></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>