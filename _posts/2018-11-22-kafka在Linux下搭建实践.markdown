---
layout:     post
title:      kafka在Linux下搭建实践
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：欢迎转载，但请注明原出处。技术本无价，分享见真情，希望我整理的知识能够帮助更多的童鞋们！					https://blog.csdn.net/Tech_Salon/article/details/65444303				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>本文根据kafka官网说明，分享kafka环境搭建过程。<a href="https://kafka.apache.org/quickstart" rel="nofollow">官网说明</a> <br>
从官网下载kafka压缩包kafka_2.11-0.10.1.0.tgz，解压后进入主目录：</p>



<pre class="prettyprint"><code class=" hljs ruby">[root<span class="hljs-variable">@centos</span>-<span class="hljs-number">78</span> kafka_2.<span class="hljs-number">11</span>-<span class="hljs-number">0</span>.<span class="hljs-number">10.1</span>.<span class="hljs-number">0</span>]<span class="hljs-comment"># ls</span>
bin config  libs  <span class="hljs-constant">LICENSE</span> logs  <span class="hljs-constant">NOTICE</span>  site-docs</code></pre>

<p>kafka的启动主要是执行脚本，而不是服务方式启动，因为kafka用到zookeeper，所以在启动kafka之前要先启动zookeeper，如果没有安装zookeeper可以使用kafka自带的zookeeper快速启动，也是脚本方式，以配置文件为参数：</p>



<pre class="prettyprint"><code class=" hljs avrasm">bin/zookeeper-server-start<span class="hljs-preprocessor">.sh</span> config/zookeeper<span class="hljs-preprocessor">.properties</span></code></pre>

<p>然后启动kafka：</p>



<pre class="prettyprint"><code class=" hljs axapta">bin/kafka-<span class="hljs-keyword">server</span>-start.sh config/<span class="hljs-keyword">server</span>.properties</code></pre>

<p>kafka中处理消息是以topic分类的，在存储消息时要先创建topic：</p>



<pre class="prettyprint"><code class=" hljs brainfuck"><span class="hljs-comment">bin/kafka</span><span class="hljs-literal">-</span><span class="hljs-comment">topics</span><span class="hljs-string">.</span><span class="hljs-comment">sh</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">create</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">zookeeper</span>  <span class="hljs-comment">192</span><span class="hljs-string">.</span><span class="hljs-comment">168</span><span class="hljs-string">.</span><span class="hljs-comment">1</span><span class="hljs-string">.</span><span class="hljs-comment">100:2181</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">replication</span><span class="hljs-literal">-</span><span class="hljs-comment">factor</span> <span class="hljs-comment">1</span><span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">partitions</span> <span class="hljs-comment">1</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">topic</span> <span class="hljs-comment">it_lv</span></code></pre>

<p>查看topic列表:</p>



<pre class="prettyprint"><code class=" hljs brainfuck"><span class="hljs-comment">bin/kafka</span><span class="hljs-literal">-</span><span class="hljs-comment">topics</span><span class="hljs-string">.</span><span class="hljs-comment">sh</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">list</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">zookeeper</span> <span class="hljs-comment">192</span><span class="hljs-string">.</span><span class="hljs-comment">168</span><span class="hljs-string">.</span><span class="hljs-comment">1</span><span class="hljs-string">.</span><span class="hljs-comment">100:2181</span></code></pre>

<p>向topic里面发送消息:</p>



<pre class="prettyprint"><code class=" hljs lasso">bin/kafka<span class="hljs-attribute">-console</span><span class="hljs-attribute">-producer</span><span class="hljs-built_in">.</span>sh <span class="hljs-subst">--</span>broker<span class="hljs-attribute">-list</span> <span class="hljs-number">192.168</span><span class="hljs-number">.1</span><span class="hljs-number">.100</span>:<span class="hljs-number">9092</span> <span class="hljs-subst">--</span>topic it_lv</code></pre>

<p>然后可以在标准输入中输入消息，再打开终端，使用如下命令查看:</p>



<pre class="prettyprint"><code class=" hljs lasso">bin/kafka<span class="hljs-attribute">-console</span><span class="hljs-attribute">-consumer</span><span class="hljs-built_in">.</span>sh <span class="hljs-subst">--</span>bootstrap<span class="hljs-attribute">-server</span> <span class="hljs-number">192.168</span><span class="hljs-number">.1</span><span class="hljs-number">.100</span>:<span class="hljs-number">9092</span><span class="hljs-subst">--</span>topic it_lv <span class="hljs-subst">--</span>from<span class="hljs-attribute">-beginning</span></code></pre>

<p>可实时显示。 <br>
Kafka命令都是以脚本方式运行，后面接有参数，参数的配置及含义可通单独执行脚本后面不加任何参数，会有相关提示和说明。 <br>
Kafka属于分布式消息处理，所以要以集群方式搭建，broker是kafka中的节点，上述过程中只启动了一个broker，可以通过以下方式扩展为三个broker的集群。</p>



<pre class="prettyprint"><code class=" hljs axapta">cp config/<span class="hljs-keyword">server</span>.properties config/<span class="hljs-keyword">server</span>-<span class="hljs-number">1.</span>properties
cp config/<span class="hljs-keyword">server</span>.properties config/<span class="hljs-keyword">server</span>-<span class="hljs-number">2.</span>properties</code></pre>

<p>分别修改server-1.properties和server-2.properties中的broker.id和log.dir,ZAI 在同一台机器上运行所以listeners只需修改端口号即可。</p>



<pre class="prettyprint"><code class=" hljs ruby">config/server-<span class="hljs-number">1</span>.<span class="hljs-symbol">properties:</span>
    broker.id=<span class="hljs-number">1</span>
    listeners=<span class="hljs-constant">PLAINTEXT</span><span class="hljs-symbol">://</span><span class="hljs-symbol">:</span><span class="hljs-number">9093</span>
    log.dir=<span class="hljs-regexp">/tmp/kafka</span>-logs-<span class="hljs-number">1</span></code></pre>



<pre class="prettyprint"><code class=" hljs ruby">config/server-<span class="hljs-number">2</span>.<span class="hljs-symbol">properties:</span>
    broker.id=<span class="hljs-number">2</span>
    listeners=<span class="hljs-constant">PLAINTEXT</span><span class="hljs-symbol">://</span><span class="hljs-symbol">:</span><span class="hljs-number">9094</span>
    log.dir=<span class="hljs-regexp">/tmp/kafka</span>-logs-<span class="hljs-number">2</span></code></pre>

<p>然后启动这两个broker:</p>



<pre class="prettyprint"><code class=" hljs axapta">bin/kafka-<span class="hljs-keyword">server</span>-start.sh config/<span class="hljs-keyword">server</span>-<span class="hljs-number">1.</span>properties &amp;
bin/kafka-<span class="hljs-keyword">server</span>-start.sh config/<span class="hljs-keyword">server</span>-<span class="hljs-number">2.</span>properties &amp;</code></pre>

<hr>

<p>小笨驴在吃草的时候创建了微信公众号，为方便更多觅食的“小笨驴”，为大家准备了大量的<strong>免费</strong>基础教学资料以及技术解决方案，还会定时发布一些好的技术文章，当然也会扯扯蛋、谈谈人生、呵呵，希望我们这群乐于分享技术的“小笨驴”团队越来越大！ <br>
</p><div align="center"> <br>
<img src="https://img-blog.csdn.net/20170324100935996?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvVGVjaF9TYWxvbg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></div><p></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>