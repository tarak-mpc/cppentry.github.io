---
layout:     post
title:      hadoop入门--通过Apache Flume向HDFS存储数据
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。by duchao（QQ：1255604509）					https://blog.csdn.net/u012995888/article/details/79059433				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p><em>本笔记基于Hadoop2.7.3，Apache Flume 1.8.0。其中flume source为netcat，flume channel为memory，flume sink为hdfs。</em></p>

<hr>

<p><strong>1，配置flume代理文件</strong></p>

<p>配置一个flume agent代理,在此名称为shaman。配置文件（netcat-memory-hdfs.conf）如下：</p>



<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-preprocessor"># Identify the components on agent shaman:</span>
shaman<span class="hljs-preprocessor">.sources</span> = netcat_s1
shaman<span class="hljs-preprocessor">.sinks</span> = hdfs_w1
shaman<span class="hljs-preprocessor">.channels</span> = <span class="hljs-keyword">in</span>-mem_c1
<span class="hljs-preprocessor"># Configure the source:</span>
shaman<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.netcat</span>_s1<span class="hljs-preprocessor">.type</span> = netcat
shaman<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.netcat</span>_s1<span class="hljs-preprocessor">.bind</span> = localhost
shaman<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.netcat</span>_s1<span class="hljs-preprocessor">.port</span> = <span class="hljs-number">44444</span>
<span class="hljs-preprocessor"># Describe the sink:</span>
shaman<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.hdfs</span>_w1<span class="hljs-preprocessor">.type</span> = hdfs
shaman<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.hdfs</span>_w1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.path</span> = hdfs://localhost:<span class="hljs-number">8020</span>/user/root/test
shaman<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.hdfs</span>_w1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.writeFormat</span> = Text
shaman<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.hdfs</span>_w1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.fileType</span> = DataStream

<span class="hljs-preprocessor"># Configure a channel that buffers events in memory:</span>
shaman<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.in</span>-mem_c1<span class="hljs-preprocessor">.type</span> = memory
shaman<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.in</span>-mem_c1<span class="hljs-preprocessor">.capacity</span> = <span class="hljs-number">20000</span>
shaman<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.in</span>-mem_c1<span class="hljs-preprocessor">.transactionCapacity</span> = <span class="hljs-number">100</span>
<span class="hljs-preprocessor"># Bind the source and sink to the channel:</span>
shaman<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.netcat</span>_s1<span class="hljs-preprocessor">.channels</span> = <span class="hljs-keyword">in</span>-mem_c1
shaman<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.hdfs</span>_w1<span class="hljs-preprocessor">.channel</span> = <span class="hljs-keyword">in</span>-mem_c1</code></pre>

<p>备注： <br>
hdfs://localhost:8020/user/root/test，其中<code>hdfs://localhost:8020</code>为hadoop配置文件core-site.xml中 <br>
<code>fs.defaultFS</code>属性的值，<code>root</code>为hadoop的登陆用户。</p>

<p><strong>2，启动flume代理</strong></p>



<pre class="prettyprint"><code class=" hljs lasso">bin/flume<span class="hljs-attribute">-ng</span> agent <span class="hljs-attribute">-f</span> agent/netcat<span class="hljs-attribute">-memory</span><span class="hljs-attribute">-hdfs</span><span class="hljs-built_in">.</span>conf <span class="hljs-attribute">-n</span> shaman  <span class="hljs-attribute">-Dflume</span><span class="hljs-built_in">.</span>root<span class="hljs-built_in">.</span>logger<span class="hljs-subst">=</span>DEBUG,console <span class="hljs-attribute">-Dorg</span><span class="hljs-built_in">.</span>apache<span class="hljs-built_in">.</span>flume<span class="hljs-built_in">.</span><span class="hljs-keyword">log</span><span class="hljs-built_in">.</span>printconfig<span class="hljs-subst">=</span><span class="hljs-literal">true</span> <span class="hljs-attribute">-Dorg</span><span class="hljs-built_in">.</span>apache<span class="hljs-built_in">.</span>flume<span class="hljs-built_in">.</span><span class="hljs-keyword">log</span><span class="hljs-built_in">.</span>rawdata<span class="hljs-subst">=</span><span class="hljs-literal">true</span></code></pre>

<p><strong>3，打开telnet客户端，输入字母测试</strong></p>



<pre class="prettyprint"><code class=" hljs ">telnet localhost 44444</code></pre>

<p>然后输入文字</p>

<p><strong>4，查看hdfs test目录</strong></p>



<pre class="prettyprint"><code class=" hljs mel">hdfs dfs -<span class="hljs-keyword">ls</span> /user/root/test</code></pre>

<p>会发现有新的文件出现，文件里面的内容即是通过telent输入的字母。</p>

<hr>

<p>学习资料： <br>
1，《Hadoop For Dummies》 <br>
2，<a href="http://flume.apache.org/FlumeUserGuide.html" rel="nofollow">Flume 1.8.0 User Guide</a></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>