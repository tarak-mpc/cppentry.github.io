---
layout:     post
title:      Apache Kafka 入门 - 基本配置和运行
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：版权归博主所有，转载请带上本文链接！联系方式：abel533@gmail.com					https://blog.csdn.net/isea533/article/details/73611035				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-light">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="apache-kafka-入门">Apache Kafka 入门</h1>

<p>为了方便以后用的到，记录下自己学习的过程。由于没有生产环节使用的经验，希望有经验的朋友可以留言指导。</p>

<p>Apache Kafka 入门大概分为5篇博客，内容都比较基础，计划包含以下内容：</p>

<ul>
<li>Kafka的基本配置和运行</li>
<li>Kafka命令详细介绍</li>
<li>Kafka-manager的基本配置和运行</li>
<li>Kafka API 简单用法</li>
<li>Spring Boot 集成Kafka</li>
</ul>

<p>Kafka支持Linux和WIndows环境，本文运行环境使用Linux（CentOS）。</p>

<p>本篇为第一篇。</p>

<h2 id="kafka的基本配置和运行">Kafka的基本配置和运行</h2>

<p>Kafka官方地址为：<a href="https://kafka.apache.org" rel="nofollow">https://kafka.apache.org</a></p>

<p>Kafka官方文档非常的详细，提供的快速入门也很友好，虽然是英文的，但是看命令就能明白，快速入门地址为：<a href="https://kafka.apache.org/quickstart" rel="nofollow">https://kafka.apache.org/quickstart</a>。下面内容基本摘自官方快速入门，建议对比英文原版了解更多。</p>



<h3 id="1-下载代码">1. 下载代码</h3>

<p>首先从下面地址下载 Kafka，当前最新的版本为 0.10.2.1。</p>

<blockquote>
  <p><a href="https://kafka.apache.org/downloads" rel="nofollow">https://kafka.apache.org/downloads</a></p>
</blockquote>

<p>下载完成后，解压压缩包并进入Kafka目录：</p>



<pre class="prettyprint"><code class="language-shell hljs markdown"><span class="hljs-blockquote">&gt; tar -xzf kafka_2.11-0.10.2.1.tgz</span>
<span class="hljs-blockquote">&gt; cd kafka_2.11-0.10.2.1</span></code></pre>



<h3 id="2-启动服务器">2. 启动服务器</h3>



<h4 id="1启动zookeeper">（1）启动ZooKeeper</h4>

<p>Kafka使用ZooKeeper，所以您需要先启动一个ZooKeeper服务器，如果您还没有。您可以使用随Kafka一起打包的便捷脚本来获取一个快速但是比较粗糙的单节点ZooKeeper实例。</p>



<pre class="prettyprint"><code class="language-shell hljs avrasm">&gt; bin/zookeeper-server-start<span class="hljs-preprocessor">.sh</span> config/zookeeper<span class="hljs-preprocessor">.properties</span></code></pre>

<p>这个 zookeeper中主要就3个配置：</p>



<pre class="prettyprint"><code class="language-properties hljs vala"><span class="hljs-preprocessor"># the directory where the snapshot is stored.</span>
dataDir=/tmp/zookeeper
<span class="hljs-preprocessor"># the port at which the clients will connect</span>
clientPort=<span class="hljs-number">2181</span>
<span class="hljs-preprocessor"># disable the per-ip limit on the number of connections since this is a non-production config</span>
maxClientCnxns=<span class="hljs-number">0</span></code></pre>

<p>我们需要记住zookeeper的端口 2181，在后面会用到。</p>



<h4 id="2kafka基本配置">（2）Kafka基本配置</h4>

<p>Kafka在config目录下提供了一个基本的配置文件。为了保证可以远程访问Kafka，我们需要修改两处配置。</p>

<p>打开<code>config/server.properties</code>文件，在很靠前的位置有<code>listeners</code>和 <code>advertised.listeners</code>两处配置的注释，去掉这两个注释，并且根据当前服务器的IP修改如下：</p>



<pre class="prettyprint"><code class="language-properties hljs vala"><span class="hljs-preprocessor"># The address the socket server listens on. It will get the value returned from </span>
<span class="hljs-preprocessor"># java.net.InetAddress.getCanonicalHostName() if not configured.</span>
<span class="hljs-preprocessor">#   FORMAT:</span>
<span class="hljs-preprocessor">#     listeners = listener_name://host_name:port</span>
<span class="hljs-preprocessor">#   EXAMPLE:</span>
<span class="hljs-preprocessor">#     listeners = PLAINTEXT://your.host.name:9092</span>
listeners=PLAINTEXT:<span class="hljs-comment">//:9092</span>

<span class="hljs-preprocessor"># Hostname and port the broker will advertise to producers and consumers. If not set, </span>
<span class="hljs-preprocessor"># it uses the value for "listeners" if configured.  Otherwise, it will use the value</span>
<span class="hljs-preprocessor"># returned from java.net.InetAddress.getCanonicalHostName().</span>
advertised.listeners=PLAINTEXT:<span class="hljs-comment">//192.168.16.150:9092</span></code></pre>

<blockquote>
  <p>当前服务器IP为192.168.16.150，你需要修改为外网或局域网可以访问到的服务器IP。</p>
</blockquote>



<h4 id="3启动kafka">（3）启动Kafka</h4>

<p>接下来启动Kafka服务：</p>



<pre class="prettyprint"><code class="language-shell hljs axapta">&gt; bin/kafka-<span class="hljs-keyword">server</span>-start.sh config/<span class="hljs-keyword">server</span>.properties</code></pre>

<blockquote>
  <p>如果你在启动时出现 <code>java.lang.OutOfMemoryError: Map failed</code></p>
  
  <p>打开 bin/kafka-run-class.sh 文件，</p>
  
  <p>搜索<code>-XX:+DisableExplicitGC</code>，</p>
  
  <p>将这个参数替换为 <code>-XX:+ExplicitGCInvokesConcurrent</code>。</p>
  
  <p>具体原因可以参考： <a href="http://blog.csdn.net/xieyuooo/article/details/7547435" rel="nofollow">http://blog.csdn.net/xieyuooo/article/details/7547435</a> 。</p>
</blockquote>



<h3 id="创建-topic">创建 Topic</h3>

<p>使用下面的命令创建 Topic。</p>



<pre class="prettyprint"><code class="language-shell hljs brainfuck">&gt; <span class="hljs-comment">bin/kafka</span><span class="hljs-literal">-</span><span class="hljs-comment">topics</span><span class="hljs-string">.</span><span class="hljs-comment">sh</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">create</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">zookeeper</span> <span class="hljs-comment">localhost:2181</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">replication</span><span class="hljs-literal">-</span><span class="hljs-comment">factor</span> <span class="hljs-comment">1</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">partitions</span> <span class="hljs-comment">1</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">topic</span> <span class="hljs-comment">test</span></code></pre>

<p>除了命令方式外，使用后面介绍的Kafka-manager可以更方便的创建。</p>



<h3 id="启动一个消费者">启动一个消费者</h3>

<p>在一个新的终端执行下面的命令。</p>



<pre class="prettyprint"><code class="language-shell hljs lasso"><span class="hljs-subst">&gt;</span> bin/kafka<span class="hljs-attribute">-console</span><span class="hljs-attribute">-consumer</span><span class="hljs-built_in">.</span>sh <span class="hljs-subst">--</span>bootstrap<span class="hljs-attribute">-server</span> localhost:<span class="hljs-number">9092</span> <span class="hljs-subst">--</span>topic test <span class="hljs-subst">--</span>from<span class="hljs-attribute">-beginning</span></code></pre>

<p>这个消费者就是简单的将消息输出到控制台。</p>



<h3 id="启动生产者">启动生产者</h3>



<pre class="prettyprint"><code class="language-shell hljs lasso"><span class="hljs-subst">&gt;</span> bin/kafka<span class="hljs-attribute">-console</span><span class="hljs-attribute">-producer</span><span class="hljs-built_in">.</span>sh <span class="hljs-subst">--</span>broker<span class="hljs-attribute">-list</span> localhost:<span class="hljs-number">9092</span> <span class="hljs-subst">--</span>topic test</code></pre>

<p>启动后，可以输入内容，然后回车。</p>

<p>此时你应该可以在上一个消费者中看到有消息输出。</p>



<h3 id="显示效果如下">显示效果如下</h3>

<p>下图上方为消费者，只是简单的将消息输出到控制台。下方为生产者，回车时产生消息。</p>

<p><img src="https://img-blog.csdn.net/20170622211449550?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaXNlYTUzMw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="显示效果" title=""></p>

<p>到此关于Kafka的基本命令就结束了，接下来是Kafka命令行详细介绍。</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>