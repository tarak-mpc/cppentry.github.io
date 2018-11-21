---
layout:     post
title:      Spark大数据学习笔记_第5篇_kafka集群环境搭建
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：士，不可以不弘毅，任重而道远					https://blog.csdn.net/superbeyone/article/details/82593365				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2 id="spark大数据学习笔记第5篇kafka集群环境搭建">Spark大数据学习笔记_第5篇_kafka集群环境搭建</h2>

<hr>

<p></p><div class="toc"><div class="toc">
<ul>
<li><ul>
<li><a href="#spark%E5%A4%A7%E6%95%B0%E6%8D%AE%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0%E7%AC%AC5%E7%AF%87kafka%E9%9B%86%E7%BE%A4%E7%8E%AF%E5%A2%83%E6%90%AD%E5%BB%BA" rel="nofollow">Spark大数据学习笔记_第5篇_kafka集群环境搭建</a><ul>
<li><a href="#1-%E5%AE%89%E8%A3%85kafka%E5%8C%85" rel="nofollow">1. 安装Kafka包</a></li>
<li><a href="#2-%E6%90%AD%E5%BB%BAkafka%E9%9B%86%E7%BE%A4" rel="nofollow">2. 搭建kafka集群</a></li>
<li><a href="#3-%E5%90%AF%E5%8A%A8kafka%E9%9B%86%E7%BE%A4" rel="nofollow">3. 启动kafka集群</a></li>
<li><a href="#4-%E6%B5%8B%E8%AF%95kafka%E9%9B%86%E7%BE%A4" rel="nofollow">4. 测试kafka集群</a></li>
</ul>
</li>
</ul>
</li>
</ul>
</div>
</div>


<hr>



<h3 id="1-安装kafka包">1. 安装Kafka包</h3>

<p>1、将kafka_2.9.2-0.8.1.tgz使用WinSCP拷贝到sparkproject1的/usr/local目录下：          <a href="http://kafka.apache.org/downloads" rel="nofollow">下载地址</a> <br>
2、对kafka_2.9.2-0.8.1.tgz进行解压缩：</p>

<pre class="prettyprint"><code class="language-ssh hljs autohotkey">tar -zxvf kafk<span class="hljs-built_in">a_2</span>.<span class="hljs-number">9.2</span>-<span class="hljs-number">0.8</span>.<span class="hljs-number">1</span>.tgz</code></pre>

<p>3、对kafka目录进行改名：</p>



<pre class="prettyprint"><code class="language-ssh hljs autohotkey">mv kafk<span class="hljs-built_in">a_2</span>.<span class="hljs-number">9.2</span>-<span class="hljs-number">0.8</span>.<span class="hljs-number">1</span> kafka</code></pre>

<p>4、配置kafka</p>



<pre class="prettyprint"><code class="language-ssh hljs lua">vi /usr/<span class="hljs-keyword">local</span>/kafka/config/server.properties</code></pre>

<p>broker.id：依次增长的整数，0、1、2，集群中Broker的唯一id <br>
zookeeper.connect=192.168.1.105:2181,192.168.1.106:2181,192.168.1.107:2181 <br>
5、安装slf4j <br>
将slf4j-1.7.6.zip上传到/usr/local目录下</p>



<pre class="prettyprint"><code class="language-ssh hljs avrasm">unzip slf4j-<span class="hljs-number">1.7</span><span class="hljs-number">.6</span><span class="hljs-preprocessor">.zip</span></code></pre>

<p>把slf4j中的slf4j-nop-1.7.6.jar复制到kafka的libs目录下面</p>



<h3 id="2-搭建kafka集群">2. 搭建kafka集群</h3>

<p>1、按照上述步骤在另外两台机器分别安装kafka。用scp把kafka拷贝到sparkproject2和sparkproject3即可。 <br>
2、唯一区别的，就是server.properties中的broker.id，要设置为1和2</p>



<h3 id="3-启动kafka集群">3. 启动kafka集群</h3>

<p>1、解决kafka Unrecognized VM option ‘UseCompressedOops’问题</p>



<pre class="prettyprint"><code class="language-ssh hljs applescript">vi /usr/<span class="hljs-keyword">local</span>/kafka/bin/kafka-<span class="hljs-command">run</span>-<span class="hljs-type">class</span>.sh 
<span class="hljs-keyword">if</span> [ -z <span class="hljs-string">"$KAFKA_JVM_PERFORMANCE_OPTS"</span> ]; <span class="hljs-keyword">then</span>
 KAFKA_JVM_PERFORMANCE_OPTS=<span class="hljs-string">"-server  -XX:+UseCompressedOops -XX:+UseParNewGC -XX:+UseConcMarkSweepGC -XX:+CMSClassUnloadingEnabled -XX:+CMSScavengeBeforeRemark -XX:+DisableExplicitGC -Djava.awt.headless=true"</span></code></pre>

<p>去掉  b-XX:+UseCompressedOops 即可</p>

<p>2、在三台机器上的kafka目录下，分别执行以下命令：</p>



<pre class="prettyprint"><code class="language-ssh hljs axapta">nohup bin/kafka-<span class="hljs-keyword">server</span>-start.sh config/<span class="hljs-keyword">server</span>.properties &amp;</code></pre>

<p>3、使用jps检查启动是否成功</p>



<h3 id="4-测试kafka集群">4. 测试kafka集群</h3>

<p>使用基本命令检查kafka是否搭建成功</p>



<pre class="prettyprint"><code class="language-ssh hljs brainfuck"><span class="hljs-comment">bin/kafka</span><span class="hljs-literal">-</span><span class="hljs-comment">topics</span><span class="hljs-string">.</span><span class="hljs-comment">sh</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">zookeeper</span> <span class="hljs-comment">192</span><span class="hljs-string">.</span><span class="hljs-comment">168</span><span class="hljs-string">.</span><span class="hljs-comment">1</span><span class="hljs-string">.</span><span class="hljs-comment">105:2181</span><span class="hljs-string">,</span><span class="hljs-comment">192</span><span class="hljs-string">.</span><span class="hljs-comment">168</span><span class="hljs-string">.</span><span class="hljs-comment">1</span><span class="hljs-string">.</span><span class="hljs-comment">106:2181</span><span class="hljs-string">,</span><span class="hljs-comment">192</span><span class="hljs-string">.</span><span class="hljs-comment">168</span><span class="hljs-string">.</span><span class="hljs-comment">1</span><span class="hljs-string">.</span><span class="hljs-comment">107:2181</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">topic</span> <span class="hljs-comment">TestTopic</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">replication</span><span class="hljs-literal">-</span><span class="hljs-comment">factor</span> <span class="hljs-comment">1</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">partitions</span> <span class="hljs-comment">1</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">create</span>

<span class="hljs-comment">bin/kafka</span><span class="hljs-literal">-</span><span class="hljs-comment">console</span><span class="hljs-literal">-</span><span class="hljs-comment">producer</span><span class="hljs-string">.</span><span class="hljs-comment">sh</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">broker</span><span class="hljs-literal">-</span><span class="hljs-comment">list</span> <span class="hljs-comment">192</span><span class="hljs-string">.</span><span class="hljs-comment">168</span><span class="hljs-string">.</span><span class="hljs-comment">1</span><span class="hljs-string">.</span><span class="hljs-comment">105:9092</span><span class="hljs-string">,</span><span class="hljs-comment">192</span><span class="hljs-string">.</span><span class="hljs-comment">168</span><span class="hljs-string">.</span><span class="hljs-comment">1</span><span class="hljs-string">.</span><span class="hljs-comment">106:9092</span><span class="hljs-string">,</span><span class="hljs-comment">192</span><span class="hljs-string">.</span><span class="hljs-comment">168</span><span class="hljs-string">.</span><span class="hljs-comment">1</span><span class="hljs-string">.</span><span class="hljs-comment">107:9092</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">topic</span> <span class="hljs-comment">TestTopic</span>

<span class="hljs-comment">bin/kafka</span><span class="hljs-literal">-</span><span class="hljs-comment">console</span><span class="hljs-literal">-</span><span class="hljs-comment">consumer</span><span class="hljs-string">.</span><span class="hljs-comment">sh</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">zookeeper</span> <span class="hljs-comment">192</span><span class="hljs-string">.</span><span class="hljs-comment">168</span><span class="hljs-string">.</span><span class="hljs-comment">1</span><span class="hljs-string">.</span><span class="hljs-comment">105:2181</span><span class="hljs-string">,</span><span class="hljs-comment">192</span><span class="hljs-string">.</span><span class="hljs-comment">168</span><span class="hljs-string">.</span><span class="hljs-comment">1</span><span class="hljs-string">.</span><span class="hljs-comment">106:2181</span><span class="hljs-string">,</span><span class="hljs-comment">192</span><span class="hljs-string">.</span><span class="hljs-comment">168</span><span class="hljs-string">.</span><span class="hljs-comment">1</span><span class="hljs-string">.</span><span class="hljs-comment">107:2181</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">topic</span> <span class="hljs-comment">TestTopic</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">from</span><span class="hljs-literal">-</span><span class="hljs-comment">beginning</span></code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>