---
layout:     post
title:      Flume结合Kafka简单实例
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/weixin_35852328/article/details/79939447				</div>
								            <div id="content_views" class="markdown_views prism-dracula">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>默认已经安装好了flume，zk集群，kafka集群 <br>
使用flume读取linux的文本文件，将文本文件中的数据发送到kafka</p>



<h2 id="1创建flume配置文件">1.创建flume配置文件</h2>

<p>vim $flume_home/conf/exec.conf </p>



<pre class="prettyprint"><code class=" hljs avrasm">a1<span class="hljs-preprocessor">.sources</span> = <span class="hljs-built_in">r1</span>
a1<span class="hljs-preprocessor">.channels</span> = c1
a1<span class="hljs-preprocessor">.sinks</span> = k1

a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.type</span> = exec
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.command</span> = tail -F /export/data/flume_sources/click_log/<span class="hljs-number">1.</span>log
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.channels</span> = c1

a1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.type</span>=memory
a1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.capacity</span>=<span class="hljs-number">10000</span>
a1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.transactionCapacity</span>=<span class="hljs-number">100</span>

a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.type</span> = org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.sink</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.KafkaSink</span>
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.topic</span> = test
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.brokerList</span> = kafka01:<span class="hljs-number">9092</span>
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.requiredAcks</span> = <span class="hljs-number">1</span>
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.batchSize</span> = <span class="hljs-number">20</span>
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.channel</span> = c1
</code></pre>



<h2 id="2准备目标数据的目录">2.准备目标数据的目录</h2>

<p>mkdir -p /export/data/flume_sources/click_log</p>



<h2 id="3通过脚本创建目标文件并生产数据">3.通过脚本创建目标文件并生产数据</h2>

<p>click_log_out.sh</p>

<pre class="prettyprint"><code class=" hljs bash"><span class="hljs-keyword">for</span>((i=<span class="hljs-number">0</span>;i&lt;=<span class="hljs-number">50000</span>;i++));
<span class="hljs-keyword">do</span> <span class="hljs-built_in">echo</span> <span class="hljs-string">"message-"</span>+<span class="hljs-variable">$i</span> &gt;&gt;/<span class="hljs-keyword">export</span>/data/flume_sources/click_log/<span class="hljs-number">1</span>.log;
<span class="hljs-keyword">done</span></code></pre>

<p>注意：需要使用root用户赋权。 chmod +x click_log_out.sh</p>



<h2 id="4启动流程">4.启动流程</h2>

<p>第一步：启动kafka集群（每台kafka集群机器）</p>

<pre class="prettyprint"><code class=" hljs lasso">    kafka<span class="hljs-attribute">-server</span><span class="hljs-attribute">-start</span><span class="hljs-built_in">.</span>sh <span class="hljs-variable">$kafka_home</span>/config/server<span class="hljs-built_in">.</span>properties </code></pre>

<p>第二步：创建一个topic并开启consumer </p>



<pre class="prettyprint"><code class=" hljs brainfuck"><span class="hljs-comment">kafka</span><span class="hljs-literal">-</span><span class="hljs-comment">console</span><span class="hljs-literal">-</span><span class="hljs-comment">consumer</span><span class="hljs-string">.</span><span class="hljs-comment">sh</span>  <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">topic=test</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">zookeeper</span> <span class="hljs-comment">hadoop1:2181</span></code></pre>

<p>第三步：执行数据生产的脚本</p>



<pre class="prettyprint"><code class=" hljs avrasm">sh click_log_out<span class="hljs-preprocessor">.sh</span></code></pre>

<p>第四步：启动flume客户端</p>



<pre class="prettyprint"><code class=" hljs lasso"><span class="hljs-variable">$flume_home</span>/bin/flume<span class="hljs-attribute">-ng</span> agent <span class="hljs-attribute">-n</span> a1 <span class="hljs-attribute">-c</span> conf <span class="hljs-attribute">-f</span> conf/myconf/exec<span class="hljs-built_in">.</span>conf <span class="hljs-attribute">-Dflume</span><span class="hljs-built_in">.</span>root<span class="hljs-built_in">.</span>logger<span class="hljs-subst">=</span>INFO,console</code></pre>

<p>第五步：在第三步启动的kafka consumer窗口查看输出</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>