---
layout:     post
title:      Spark大数据学习笔记_第6篇_flume安装
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：士，不可以不弘毅，任重而道远					https://blog.csdn.net/superbeyone/article/details/82697667				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2 id="spark大数据学习笔记第6篇flume安装">Spark大数据学习笔记_第6篇_flume安装</h2>

<hr>

<p></p><div class="toc"><div class="toc">
<ul>
<li><ul>
<li><a href="#spark%E5%A4%A7%E6%95%B0%E6%8D%AE%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0%E7%AC%AC6%E7%AF%87flume%E5%AE%89%E8%A3%85" rel="nofollow">Spark大数据学习笔记_第6篇_flume安装</a><ul>
<li><a href="#1%E5%AE%89%E8%A3%85flume" rel="nofollow">1.安装flume</a></li>
<li><a href="#2%E4%BF%AE%E6%94%B9flume%E9%85%8D%E7%BD%AE%E6%96%87%E4%BB%B6" rel="nofollow">2.修改flume配置文件</a></li>
<li><a href="#3-%E5%88%9B%E5%BB%BA%E9%9C%80%E8%A6%81%E7%9A%84%E6%96%87%E4%BB%B6%E5%A4%B9" rel="nofollow">3. 创建需要的文件夹</a></li>
<li><a href="#4%E5%90%AF%E5%8A%A8flume-agent" rel="nofollow">4.启动flume-agent</a></li>
<li><a href="#5%E6%B5%8B%E8%AF%95flume" rel="nofollow">5.测试flume</a></li>
</ul>
</li>
</ul>
</li>
</ul>
</div>
</div>


<hr>

<h3 id="1安装flume">1.安装flume</h3>

<p>1、将flume-ng-1.5.0-cdh5.3.6.tar.gz使用WinSCP拷贝到sparkproject1的/usr/local目录下。 <br>
2、对flume进行解压缩：</p>



<pre class="prettyprint"><code class="language-ssh hljs lasso">tar <span class="hljs-attribute">-zxvf</span> flume<span class="hljs-attribute">-ng</span><span class="hljs-subst">-</span><span class="hljs-number">1.5</span><span class="hljs-number">.0</span><span class="hljs-attribute">-cdh5</span><span class="hljs-number">.3</span><span class="hljs-number">.6</span><span class="hljs-built_in">.</span>tar<span class="hljs-built_in">.</span>gz</code></pre>

<p>3、对flume目录进行重命名：</p>



<pre class="prettyprint"><code class="language-ssh hljs lasso">mv apache<span class="hljs-attribute">-flume</span><span class="hljs-subst">-</span><span class="hljs-number">1.5</span><span class="hljs-number">.0</span><span class="hljs-attribute">-cdh5</span><span class="hljs-number">.3</span><span class="hljs-number">.6</span><span class="hljs-attribute">-bin</span> flume</code></pre>

<p>4、配置scala相关的环境变量</p>



<pre class="prettyprint"><code class="language-ssh hljs bash">vi ~/.bashrc
<span class="hljs-keyword">export</span> FLUME_HOME=/usr/local/flume
<span class="hljs-keyword">export</span> FLUME_CONF_DIR=<span class="hljs-variable">$FLUME_HOME</span>/conf
<span class="hljs-keyword">export</span> PATH=<span class="hljs-variable">$FLUME_HOME</span>/bin
<span class="hljs-built_in">source</span> ~/.bashrc</code></pre>



<h3 id="2修改flume配置文件">2.修改flume配置文件</h3>



<pre class="prettyprint"><code class="language-ssh hljs avrasm">vi /usr/local/flume/conf/flume-conf<span class="hljs-preprocessor">.properties</span>

<span class="hljs-preprocessor">#agent1表示代理名称</span>
agent1<span class="hljs-preprocessor">.sources</span>=source1
agent1<span class="hljs-preprocessor">.sinks</span>=sink1
agent1<span class="hljs-preprocessor">.channels</span>=channel1
<span class="hljs-preprocessor">#配置source1</span>
agent1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.source</span>1<span class="hljs-preprocessor">.type</span>=spooldir
agent1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.source</span>1<span class="hljs-preprocessor">.spoolDir</span>=/usr/local/logs
agent1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.source</span>1<span class="hljs-preprocessor">.channels</span>=channel1
agent1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.source</span>1<span class="hljs-preprocessor">.fileHeader</span> = false
agent1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.source</span>1<span class="hljs-preprocessor">.interceptors</span> = i1
agent1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.source</span>1<span class="hljs-preprocessor">.interceptors</span><span class="hljs-preprocessor">.i</span>1<span class="hljs-preprocessor">.type</span> = timestamp
<span class="hljs-preprocessor">#配置channel1</span>
agent1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.channel</span>1<span class="hljs-preprocessor">.type</span>=file
agent1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.channel</span>1<span class="hljs-preprocessor">.checkpointDir</span>=/usr/local/logs_tmp_cp
agent1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.channel</span>1<span class="hljs-preprocessor">.dataDirs</span>=/usr/local/logs_tmp
<span class="hljs-preprocessor">#配置sink1</span>
agent1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.sink</span>1<span class="hljs-preprocessor">.type</span>=hdfs
agent1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.sink</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.path</span>=hdfs://sparkproject1:<span class="hljs-number">9000</span>/logs
agent1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.sink</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.fileType</span>=DataStream
agent1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.sink</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.writeFormat</span>=TEXT
agent1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.sink</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.rollInterval</span>=<span class="hljs-number">1</span>
agent1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.sink</span>1<span class="hljs-preprocessor">.channel</span>=channel1
agent1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.sink</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.filePrefix</span>=%<span class="hljs-built_in">Y</span>-%m-%d</code></pre>

<h3 id="3-创建需要的文件夹">3. 创建需要的文件夹</h3>

<ul>
<li>本地文件夹：</li>
</ul>

<pre class="prettyprint"><code class="language-ssh hljs perl"><span class="hljs-keyword">mkdir</span> /usr/<span class="hljs-keyword">local</span>/logs</code></pre>

<ul>
<li>HDFS文件夹：</li>
</ul>

<pre class="prettyprint"><code class="language-ssh hljs perl">hdfs dfs -<span class="hljs-keyword">mkdir</span> /logs</code></pre>



<h3 id="4启动flume-agent">4.启动flume-agent</h3>



<pre class="prettyprint"><code class="language-ssh hljs lasso">flume<span class="hljs-attribute">-ng</span> agent <span class="hljs-attribute">-n</span> agent1 <span class="hljs-attribute">-c</span> conf <span class="hljs-attribute">-f</span> /usr/<span class="hljs-built_in">local</span>/flume/conf/flume<span class="hljs-attribute">-conf</span><span class="hljs-built_in">.</span>properties <span class="hljs-attribute">-Dflume</span><span class="hljs-built_in">.</span>root<span class="hljs-built_in">.</span>logger<span class="hljs-subst">=</span>DEBUG,console</code></pre>

<h3 id="5测试flume">5.测试flume</h3>

<ul>
<li>新建一份文件，移动到/usr/local/logs目录下，flume就会自动上传到HDFS的/logs目录中</li>
</ul>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>