---
layout:     post
title:      kafka在kubernets上部署时生产消费问题
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/scylhy/article/details/82558184				</div>
								            <div id="content_views" class="markdown_views prism-github-gist">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2 id="kafka在kubernets上部署时生产消费问题">kafka在kubernets上部署时生产消费问题</h2>

<pre><code>  在kubernetes上部署fabric遇阻，定位到oderer和kafka之间有问题，详查之后，  
发现kafka是有问题的.问题表现在produce/consume异常上.
  查询资料发现，原来是kafka容器部署上的问题，该问题是对kafka和docker理解不  
透彻造成的.
</code></pre>

<ul>
<li><p>部署结构图(from github) <br>
<img src="https://img-blog.csdn.net/20180909143849317?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NjeWxoeQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="docker_kafka_from github" title=""></p></li>
<li><p>该问题，表现在kafka docker集群对外表示是一个ip和端口</p></li>
</ul>

<pre class="prettyprint"><code class="language-bash hljs ">$ docker ps
CONTAINER ID        IMAGE                      PORTS                                                NAMES
<span class="hljs-number">2</span>c3fe5e651bf        kafkadocker_kafka          <span class="hljs-number">0.0</span>.<span class="hljs-number">0.0</span>:<span class="hljs-number">32000</span>-&gt;<span class="hljs-number">9092</span>/tcp                              kafkadocker_kafka_2
<span class="hljs-number">4</span>e22d3d715ec        kafkadocker_kafka          <span class="hljs-number">0.0</span>.<span class="hljs-number">0.0</span>:<span class="hljs-number">32001</span>-&gt;<span class="hljs-number">9092</span>/tcp                              kafkadocker_kafka_1
bfb5545efe6b        wurstmeister/zookeeper     <span class="hljs-number">22</span>/tcp, <span class="hljs-number">2888</span>/tcp, <span class="hljs-number">3888</span>/tcp, <span class="hljs-number">0.0</span>.<span class="hljs-number">0.0</span>:<span class="hljs-number">2181</span>-&gt;<span class="hljs-number">2181</span>/tcp   kafkadocker_zookeeper_1
</code></pre>

<ul>
<li>解决：加上KAFKA_ADVERTISED_HOST_NAME 环境变量</li>
<li><p>结果：</p>

<pre><code>可以正常的消费生产了
</code></pre></li>
<li><p>参考:</p>

<ul><li><a href="https://github.com/wurstmeister/kafka-docker/wiki/Connectivity" rel="nofollow">Kafka connectivity</a></li></ul></li>
<li><p>待完善</p></li>
</ul>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>