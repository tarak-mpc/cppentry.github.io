---
layout:     post
title:      kafka + zookeeper 单节点配置
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="kafka-zookeeper-单节点配置">kafka + zookeeper 单节点配置</h1>

<p></p><div class="toc"><div class="toc">
<ul>
<li><a href="#kafka-zookeeper-%E5%8D%95%E8%8A%82%E7%82%B9%E9%85%8D%E7%BD%AE" rel="nofollow">kafka  zookeeper 单节点配置</a><ul>
<li><a href="#kafka%E5%88%9D%E5%A7%8B" rel="nofollow">kafka初始</a><ul>
<li><a href="#1%E5%AE%9A%E4%B9%89" rel="nofollow">定义</a></li>
<li><a href="#2%E4%BD%BF%E7%94%A8%E5%9C%BA%E6%99%AF" rel="nofollow">使用场景</a></li>
<li><a href="#3kafka%E5%92%8C%E5%85%B6%E4%BB%96%E4%B8%BB%E6%B5%81%E5%88%86%E5%B8%83%E5%BC%8F%E6%B6%88%E6%81%AF%E7%B3%BB%E7%BB%9F%E7%9A%84%E5%AF%B9%E6%AF%94" rel="nofollow">Kafka和其他主流分布式消息系统的对比</a></li>
</ul>
</li>
<li><a href="#kafka%E7%9B%B8%E5%85%B3%E6%A6%82%E5%BF%B5" rel="nofollow">kafka相关概念</a><ul>
<li><a href="#1amqp%E5%8D%8F%E8%AE%AE" rel="nofollow">AMQP协议</a></li>
<li><a href="#2%E5%9F%BA%E6%9C%AC%E6%A6%82%E5%BF%B5" rel="nofollow">基本概念</a></li>
<li><a href="#3kafka%E6%9E%B6%E6%9E%84" rel="nofollow">Kafka架构</a></li>
</ul>
</li>
<li><a href="#zookeeper%E6%90%AD%E5%BB%BA" rel="nofollow">zookeeper搭建</a><ul>
<li><a href="#1%E5%AE%89%E8%A3%85java" rel="nofollow">安装java</a></li>
<li><a href="#2%E4%B8%8B%E8%BD%BDzookeeper" rel="nofollow">下载zookeeper</a></li>
<li><a href="#3%E4%BF%AE%E6%94%B9%E9%85%8D%E7%BD%AE%E6%96%87%E4%BB%B6" rel="nofollow">修改配置文件</a></li>
</ul>
</li>
</ul>
</li>
</ul>
</div>
</div>


<hr>



<h2 id="kafka初始"><strong>kafka初始</strong></h2>



<h3 id="1定义">1.定义</h3>

<blockquote>
  <p>它是一个分布式消息系统，由linkedin使用scala编写，用作LinkedIn的活动流（Activity Stream）和运营数据处理管道（Pipeline）的基础。具有高水平扩展和高吞吐量。</p>
</blockquote>



<h3 id="2使用场景">2.使用场景</h3>

<p>在我们大量使用分布式数据库、分布式计算集群的时候，是否会遇到这样的一些问题：</p>

<ul>
<li>我们想分析下用户行为（pageviews），以便我们设计出更好的广告位</li>
<li>我想对用户的搜索关键词进行统计，分析出当前的流行趋势</li>
<li>有些数据，存储数据库浪费，直接存储硬盘效率又低</li>
</ul>



<h3 id="3kafka和其他主流分布式消息系统的对比">3.Kafka和其他主流分布式消息系统的对比</h3>

<p><img src="https://img-blog.csdn.net/20171206135925886?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd2VpeGluXzM5NTgxMjQ4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<hr>



<h2 id="kafka相关概念"><strong>kafka相关概念</strong></h2>



<h3 id="1amqp协议">1.AMQP协议</h3>

<blockquote>
  <p>The Advanced Message Queuing Protocol (高级消息队列协议)：是一个标准开放的应用层的消息中间件（Message Oriented Middleware）协议。AMQP定义了通过网络发送的字节流的数据格式。因此兼容性非常好，任何实现AMQP协议的程序都可以和与AMQP协议兼容的其他程序交互，可以很容易做到跨语言，跨平台。</p>
</blockquote>

<p>上面说的3种比较流行的消息队列协议，要么支持AMQP协议，要么借鉴了AMQP协议的思想进行了开发、实现、设计。</p>



<h3 id="2基本概念">2.基本概念</h3>

<ul>
<li>消费者：（Consumer）：从消息队列中请求消息的客户端应用程序</li>
<li>生产者：（Producer）  ：向broker发布消息的应用程序</li>
<li>AMQP服务端（broker）：用来接收生产者发送的消息并将这些消息路由给服务器中的队列，便于fafka将生产者发送的消息，动态的添加到磁盘并给每一条消息一个偏移量，所以对于kafka一个broker就是一个应用程序的实例</li>
</ul>

<p>kafka支持的客户端语言：Kafka客户端支持当前大部分主流语言，包括：C、C++、Erlang、Java、.net、perl、PHP、Python、Ruby、Go、Javascript，可以使用以上任何一种语言和kafka服务器进行通信（即辨析自己的consumer从kafka集群订阅消息也可以自己写producer程序） </p>



<h3 id="3kafka架构">3.Kafka架构</h3>

<p>生产者生产消息、kafka集群、消费者获取消息这样一种架构，如下图： <br>
<img src="https://img-blog.csdn.net/20171206141028057?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd2VpeGluXzM5NTgxMjQ4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p>kafka集群中的消息，是通过Topic（主题）来进行组织的，如下图： <br>
<img src="https://img-blog.csdn.net/20171206141043262?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd2VpeGluXzM5NTgxMjQ4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p>一些基本的概念：</p>

<ul>
<li>主题（Topic）：一个主题类似新闻中的体育、娱乐、教育等分类概念，在实际工程中通常一个业务一个主题。</li>
<li>分区（Partition）：一个Topic中的消息数据按照多个分区组织，分区是kafka消息队列组织的最小单位，一个分区可以看作是一个FIFO（ First Input First Output的缩写，先入先出队列）的队列。</li>
<li>备份（Replication）：为了保证分布式可靠性，kafka0.8开始对每个分区的数据进行备份（不同的Broker上），防止其中一个Broker宕机造成分区上的数据不可用。 <br>
kafka0.7是一个很大的改变：1、增加了备份2、增加了控制借点概念，增加了集群领导者选举 。</li>
</ul>

<p>kafka分区是提高kafka性能的关键所在，当你发现你的集群性能不高时，常用手段就是增加Topic的分区，分区里面的消息是按照从新到老的顺序进行组织，消费者从队列头订阅消息，生产者从队列尾添加消息。</p>

<p>工作图如下： <br>
<img src="https://img-blog.csdn.net/20171206141238514?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd2VpeGluXzM5NTgxMjQ4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<hr>



<h2 id="zookeeper搭建"><strong>zookeeper搭建</strong></h2>

<blockquote>
  <p>zookeeper版本:3.4.11</p>
</blockquote>



<h3 id="1安装java">1.安装java</h3>



<pre class="prettyprint"><code class="language-shell hljs lasso">yum <span class="hljs-built_in">list</span> java<span class="hljs-subst">*</span>
yum <span class="hljs-attribute">-y</span> install java<span class="hljs-subst">-</span><span class="hljs-number">1.7</span><span class="hljs-number">.0</span><span class="hljs-attribute">-openjdk</span><span class="hljs-subst">*</span></code></pre>



<h3 id="2下载zookeeper">2.下载zookeeper</h3>



<pre class="prettyprint"><code class="language-shell hljs avrasm"><span class="hljs-preprocessor">#下载软件</span>
wget https://mirrors<span class="hljs-preprocessor">.cnnic</span><span class="hljs-preprocessor">.cn</span>/apache/zookeeper/zookeeper-<span class="hljs-number">3.4</span><span class="hljs-number">.11</span>/zookeeper-<span class="hljs-number">3.4</span><span class="hljs-number">.11</span><span class="hljs-preprocessor">.tar</span><span class="hljs-preprocessor">.gz</span>

<span class="hljs-preprocessor">#解压软件</span>
tar -zxvf zookeeper-<span class="hljs-number">3.4</span><span class="hljs-number">.11</span><span class="hljs-preprocessor">.tar</span><span class="hljs-preprocessor">.gz</span></code></pre>



<h3 id="3修改配置文件">3.修改配置文件</h3>

<p>进入到解压好的目录里面的conf目录中，查看相关文件 <br>
zoo_sample.cfg 这个文件是官方给我们的zookeeper的样板文件，给他复制一份命名为<strong>zoo.cfg</strong>，<strong>zoo.cfg</strong>是官方指定的文件命名规则。 <br>
<strong>（１）修改zoo.cfg配置文件</strong></p>

<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-preprocessor">#下载软件</span>
wget https://mirrors<span class="hljs-preprocessor">.cnnic</span><span class="hljs-preprocessor">.cn</span>/apache/zookeeper/zookeeper-<span class="hljs-number">3.4</span><span class="hljs-number">.11</span>/zookeeper-<span class="hljs-number">3.4</span><span class="hljs-number">.11</span><span class="hljs-preprocessor">.tar</span><span class="hljs-preprocessor">.gz</span>

<span class="hljs-preprocessor">#解压软件</span>
tar -zxvf zookeeper-<span class="hljs-number">3.4</span><span class="hljs-number">.11</span><span class="hljs-preprocessor">.tar</span><span class="hljs-preprocessor">.gz</span></code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>