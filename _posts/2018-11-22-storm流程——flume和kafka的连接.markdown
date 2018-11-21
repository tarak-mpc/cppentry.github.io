---
layout:     post
title:      storm流程——flume和kafka的连接
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：欢迎转载					https://blog.csdn.net/Levi_moon/article/details/51637938				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>flume和kafka的连接参考博客：<a href="http://www.aboutyun.com/thread-6855-1-1.html" rel="nofollow">flume，kafka，storm，mysql的整合</a> <br>
相关资源在这<a href="https://pan.baidu.com/s/1i5MzLLN" rel="nofollow">flume2kafka相关jar包及配置文件</a> <br>
若想连接起flume和kafka，需要在flume/conf目录下，创建一个.conf文件，在lib目录下添加相关jar包。 <br>
步骤： <br>
1.在flume/conf目录下创建相关.conf文件， <br>
（1）创建flume2kafka.conf文件</p>



<pre class="prettyprint"><code class=" hljs avrasm">vi flume2kafka<span class="hljs-preprocessor">.conf</span></code></pre>

<p>（2）在flume2kafka.conf文件中添加相关内容</p>



<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-preprocessor">############################################</span>
<span class="hljs-preprocessor">#  producer config</span>
<span class="hljs-preprocessor">###########################################</span>

<span class="hljs-preprocessor">#agent section</span>
producer<span class="hljs-preprocessor">.sources</span> = s
producer<span class="hljs-preprocessor">.channels</span> = c
producer<span class="hljs-preprocessor">.sinks</span> = r

<span class="hljs-preprocessor">#source section</span>
<span class="hljs-preprocessor">#设置读取方式</span>
producer<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.s</span><span class="hljs-preprocessor">.type</span> = exec
<span class="hljs-preprocessor">#设置读取数据的地址及命令（tail -f -n+1 /home/storm/work/access.log）</span>
producer<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.s</span><span class="hljs-preprocessor">.command</span> = tail -f -n+<span class="hljs-number">1</span> /home/storm/work/access<span class="hljs-preprocessor">.log</span>
producer<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.s</span><span class="hljs-preprocessor">.channels</span> = c

<span class="hljs-preprocessor"># Each sink's type must be defined</span>
producer<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.r</span><span class="hljs-preprocessor">.type</span> = org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.plugins</span><span class="hljs-preprocessor">.KafkaSink</span>
producer<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.r</span><span class="hljs-preprocessor">.metadata</span><span class="hljs-preprocessor">.broker</span><span class="hljs-preprocessor">.list</span>=master:<span class="hljs-number">9092</span>
producer<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.r</span><span class="hljs-preprocessor">.partition</span><span class="hljs-preprocessor">.key</span>=<span class="hljs-number">0</span>
producer<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.r</span><span class="hljs-preprocessor">.partitioner</span><span class="hljs-preprocessor">.class</span>=org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.plugins</span><span class="hljs-preprocessor">.SinglePartition</span>
producer<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.r</span><span class="hljs-preprocessor">.serializer</span><span class="hljs-preprocessor">.class</span>=kafka<span class="hljs-preprocessor">.serializer</span><span class="hljs-preprocessor">.StringEncoder</span>
producer<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.r</span><span class="hljs-preprocessor">.request</span><span class="hljs-preprocessor">.required</span><span class="hljs-preprocessor">.acks</span>=<span class="hljs-number">0</span>
producer<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.r</span><span class="hljs-preprocessor">.max</span><span class="hljs-preprocessor">.message</span><span class="hljs-preprocessor">.size</span>=<span class="hljs-number">1000000</span>
producer<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.r</span><span class="hljs-preprocessor">.producer</span><span class="hljs-preprocessor">.type</span>=sync
producer<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.r</span><span class="hljs-preprocessor">.custom</span><span class="hljs-preprocessor">.encoding</span>=UTF-<span class="hljs-number">8</span>
<span class="hljs-preprocessor">#设置kafka的topic为：flume2kafka</span>
producer<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.r</span><span class="hljs-preprocessor">.custom</span><span class="hljs-preprocessor">.topic</span><span class="hljs-preprocessor">.name</span>=flume2kafka

<span class="hljs-preprocessor">#Specify the channel the sink should use</span>
producer<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.r</span><span class="hljs-preprocessor">.channel</span> = c

<span class="hljs-preprocessor"># Each channel's type is defined.</span>
producer<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span><span class="hljs-preprocessor">.type</span> = memory
producer<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span><span class="hljs-preprocessor">.capacity</span> = <span class="hljs-number">1000</span></code></pre>

<p>2.在lib目录下添加相关jar包</p>



<pre class="prettyprint"><code class=" hljs avrasm">kafka_2<span class="hljs-number">.9</span><span class="hljs-number">.2</span>-<span class="hljs-number">0.8</span><span class="hljs-number">.0</span>-beta1<span class="hljs-preprocessor">.jar</span>
metrics-annotation-<span class="hljs-number">2.2</span><span class="hljs-number">.0</span><span class="hljs-preprocessor">.jar</span>
scala-compiler-<span class="hljs-number">2.9</span><span class="hljs-number">.2</span><span class="hljs-preprocessor">.jar</span></code></pre>

<p>3.启动该flume任务</p>



<pre class="prettyprint"><code class=" hljs lasso">bin/flume<span class="hljs-attribute">-ng</span> agent <span class="hljs-attribute">-n</span> producer <span class="hljs-attribute">-f</span> conf/flume2kafka<span class="hljs-built_in">.</span>conf  <span class="hljs-attribute">-Dflume</span><span class="hljs-built_in">.</span>root<span class="hljs-built_in">.</span>logger<span class="hljs-subst">=</span>INFO,console <span class="hljs-subst">&gt;&gt;</span>logs/flume<span class="hljs-built_in">.</span><span class="hljs-keyword">log</span> <span class="hljs-number">2</span><span class="hljs-subst">&gt;&amp;</span><span class="hljs-number">1</span> <span class="hljs-subst">&amp;</span></code></pre>

<p>4.启动kafka及kafka的consumer任务（查看是否有数据传输） <br>
（1）启动kafka</p>



<pre class="prettyprint"><code class=" hljs sql">sbin/<span class="hljs-operator"><span class="hljs-keyword">start</span>-kafka.sh</span></code></pre>

<p>（2）启动consumer任务</p>



<pre class="prettyprint"><code class=" hljs brainfuck"><span class="hljs-comment">bin/kafka</span><span class="hljs-literal">-</span><span class="hljs-comment">console</span><span class="hljs-literal">-</span><span class="hljs-comment">consumer</span><span class="hljs-string">.</span><span class="hljs-comment">sh</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">zookeeper</span> <span class="hljs-comment">master:2181</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">topic</span> <span class="hljs-comment">flume2kafka</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">from</span><span class="hljs-literal">-</span><span class="hljs-comment">beginning</span></code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>