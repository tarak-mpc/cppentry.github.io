---
layout:     post
title:      linux 下搭建kafka集群
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/u011747152/article/details/78971754				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>1.先下载zookerper,kafka  包   tar -zxvf  解压    到   /opt/kafka     和  /opt/kafka/zookeeper目录下</p>



<pre class="prettyprint"><code class=" hljs bash"><span class="hljs-built_in">sudo</span> vim /etc/profile 
在末尾附加：

<span class="hljs-keyword">export</span> PATH=/opt/kafka/zookeeper/bin:<span class="hljs-variable">$PATH</span>

最后 <span class="hljs-built_in">source</span> /etc/profile</code></pre>

<p>2.需先搭建zookeeper环境</p>

<blockquote>
  <p>cp    zoo_sample.cfg   zoo.cfg           在 conf里面 <br>
  vim  zoo.cfg   </p>
</blockquote>

<p>根据需要改  zoo.cfg    如果不做集群  ，只按原来的就好了</p>

<pre class="prettyprint"><code class=" hljs vala"><span class="hljs-preprocessor"># The number of milliseconds of each tick</span>
tickTime=<span class="hljs-number">2000</span>
<span class="hljs-preprocessor"># The number of ticks that the initial </span>
<span class="hljs-preprocessor"># synchronization phase can take</span>
initLimit=<span class="hljs-number">10</span>
<span class="hljs-preprocessor"># The number of ticks that can pass between </span>
<span class="hljs-preprocessor"># sending a request and getting an acknowledgement</span>
syncLimit=<span class="hljs-number">5</span>
<span class="hljs-preprocessor"># the directory where the snapshot is stored.</span>
<span class="hljs-preprocessor"># do not use /tmp for storage, /tmp here is just </span>
<span class="hljs-preprocessor"># example sakes.</span>
dataDir=/opt/kafka/zookeeper/data
<span class="hljs-preprocessor"># the port at which the clients will connect</span>
clientPort=<span class="hljs-number">2181</span>
initLimit=<span class="hljs-number">5</span>
syncLimit=<span class="hljs-number">2</span>
server<span class="hljs-number">.1</span>=<span class="hljs-number">192.168</span><span class="hljs-number">.195</span><span class="hljs-number">.149</span>:<span class="hljs-number">2888</span>:<span class="hljs-number">3888</span>
server<span class="hljs-number">.2</span>=<span class="hljs-number">192.168</span><span class="hljs-number">.195</span><span class="hljs-number">.150</span>:<span class="hljs-number">2888</span>:<span class="hljs-number">3888</span>
server<span class="hljs-number">.3</span>=<span class="hljs-number">192.168</span><span class="hljs-number">.195</span><span class="hljs-number">.151</span>:<span class="hljs-number">2888</span>:<span class="hljs-number">3888</span>
</code></pre>

<p>然后在dataDir 里面 （我的例子是data目录下）   新建一个 zookeeper_server.pid    </p>

<p>然后再搭建kafka集群</p>

<p>bin/zkServer.sh start 在三台机器上启动 <br>
bin/zkServer.sh status  查看状态 <br>
bin/kafka-server-start.sh  config/server.properties 在三台机器上启动</p>

<p>bin/kafka-topics.sh –describe –zookeeper 192.168.195.128:2181, –topic test <br>
   bin/kafka-console-producer.sh –broker-list master:9092 –topic test <br>
bin/kafka-console-consumer.sh –zookeeper master:2181 –topic test –from-beginning</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>