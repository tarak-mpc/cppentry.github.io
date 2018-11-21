---
layout:     post
title:      centos 7中docker 部署zookeeper和kafka(单节点适合开发环境)
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>网上看了一些教程,发现多数有坑,现在自己写一篇,本人亲测可用 <br>
各个软件版本:</p>

<p><strong>centos 7.2</strong> <br>
<strong>kafka 2.11-2.0.0</strong> <br>
<strong>docker 18.06.0-ce</strong>(这个安装参考我的另外一篇博客<a href="https://blog.csdn.net/qq_16563637/article/details/81699251" rel="nofollow">https://blog.csdn.net/qq_16563637/article/details/81699251</a>)</p>

<p>下载zookeeper和kafka镜像</p>

<pre class="prettyprint"><code class=" hljs ">docker pull wurstmeister/zookeeper
docker pull wurstmeister/kafka</code></pre>

<p>分别启动zookeeper和kafka</p>



<pre class="prettyprint"><code class=" hljs lasso">docker run <span class="hljs-attribute">-p</span> <span class="hljs-number">2181</span>:<span class="hljs-number">2181</span> <span class="hljs-attribute">-v</span> <span class="hljs-variable">$PWD</span>/<span class="hljs-built_in">data</span>:/<span class="hljs-built_in">data</span>  <span class="hljs-attribute">-d</span> zookeeper:latest
docker run <span class="hljs-attribute">-d</span> <span class="hljs-subst">--</span>name kafka <span class="hljs-subst">--</span>publish <span class="hljs-number">9092</span>:<span class="hljs-number">9092</span> <span class="hljs-subst">--</span><span class="hljs-keyword">link</span> zookeeper <span class="hljs-subst">--</span>env KAFKA_ZOOKEEPER_CONNECT<span class="hljs-subst">=</span>zookeeper:<span class="hljs-number">2181</span> <span class="hljs-subst">--</span>env KAFKA_ADVERTISED_HOST_NAME<span class="hljs-subst">=</span><span class="hljs-number">192.168</span><span class="hljs-number">.0</span><span class="hljs-number">.157</span> <span class="hljs-subst">--</span>env KAFKA_ADVERTISED_PORT<span class="hljs-subst">=</span><span class="hljs-number">9092</span> <span class="hljs-subst">--</span>volume /etc/localtime:/etc/localtime wurstmeister/kafka:latest</code></pre>

<p>克隆一个会话(就是再开一个ssh远程命令行界面)</p>

<p>docker ps</p>

<p>在两个会话中执行找到kafka的Container ID，进入容器内部</p>



<pre class="prettyprint"><code class=" hljs bash">docker <span class="hljs-keyword">exec</span> -it <span class="hljs-variable">${CONTAINER ID}</span> /bin/bash</code></pre>



<pre class="prettyprint"><code class=" hljs bash"><span class="hljs-built_in">cd</span> opt
<span class="hljs-built_in">cd</span> kafka_2.<span class="hljs-number">11</span>-<span class="hljs-number">2.0</span>.<span class="hljs-number">0</span></code></pre>

<p><strong>(下面内容需要在两个会话中分别执行)</strong> <br>
启动消息生产者</p>



<pre class="prettyprint"><code class=" hljs lasso">bin/kafka<span class="hljs-attribute">-console</span><span class="hljs-attribute">-producer</span><span class="hljs-built_in">.</span>sh <span class="hljs-subst">--</span>broker<span class="hljs-attribute">-list</span> localhost:<span class="hljs-number">9092</span> <span class="hljs-subst">--</span>topic test3</code></pre>

<p>启动消息消费者</p>



<pre class="prettyprint"><code class=" hljs brainfuck"><span class="hljs-comment">bin/kafka</span><span class="hljs-literal">-</span><span class="hljs-comment">console</span><span class="hljs-literal">-</span><span class="hljs-comment">consumer</span><span class="hljs-string">.</span><span class="hljs-comment">sh</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">zookeeper</span> <span class="hljs-comment">zookeeper:2181</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">topic</span> <span class="hljs-comment">test3</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">from</span><span class="hljs-literal">-</span><span class="hljs-comment">beginning</span> </code></pre>

<p>发现报错</p>



<pre class="prettyprint"><code class=" hljs vbnet">zookeeper <span class="hljs-keyword">is</span> <span class="hljs-keyword">not</span> a recognized <span class="hljs-keyword">option</span></code></pre>

<p>查了好久发现 <br>
这个命令已经过时 <br>
0.90版本之后启动消费者的方法</p>



<pre class="prettyprint"><code class=" hljs lasso">bin/kafka<span class="hljs-attribute">-console</span><span class="hljs-attribute">-consumer</span><span class="hljs-built_in">.</span>sh <span class="hljs-subst">--</span>bootstrap<span class="hljs-attribute">-server</span> localhost:<span class="hljs-number">9092</span> <span class="hljs-subst">--</span>topic test3 <span class="hljs-subst">--</span>from<span class="hljs-attribute">-beginning</span></code></pre>

<p>在producer中输入内容aaa <br>
发现consumer能够收到</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>