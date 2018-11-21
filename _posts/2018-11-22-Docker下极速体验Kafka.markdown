---
layout:     post
title:      Docker下极速体验Kafka
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h3 id="快速体验kafka">快速体验Kafka</h3>

<p>Kafka是一种高吞吐量的分布式发布订阅消息系统，从本章开始我们先极速体验，再实战docker下搭建kafka环境，最后开发一个java web应用来体验kafka服务。</p>

<p>我们一起用最快的速度体验kafka的消息发布订阅功能：</p>

<p>新建一个docker-compose.yml文件，内容如下：</p>

<pre class="prettyprint"><code class=" hljs haml">version: '2'
services:
  zk_server: 
    image: daocloud.io/library/zookeeper:3.3.6
    restart: always
  kafka_server: 
    image: bolingcavalry/kafka:0.0.1
    links: 
      -<span class="ruby"> <span class="hljs-symbol">zk_server:</span>zkhost
</span>    command: /bin/sh -c '/usr/local/work/start_server.sh'
    restart: always
  message_producer: 
    image: bolingcavalry/kafka:0.0.1
    links: 
      -<span class="ruby"> <span class="hljs-symbol">zk_server:</span>zkhost
</span>      -<span class="ruby"> <span class="hljs-symbol">kafka_server:</span>kafkahost
</span>    restart: always
  message_consumer: 
    image: bolingcavalry/kafka:0.0.1
    links: 
      -<span class="ruby"> <span class="hljs-symbol">zk_server:</span>zkhost
</span>    restart: always</code></pre>

<p>打开终端，在docker-compose.yml所在目录下执行docker-compose up -d，由于需要从hub.docker.com下载镜像，所以需要等待几分钟，下载结束后会自动启动四个容器，如下图： <br>
<img src="https://img-blog.csdn.net/20170510235610115?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYm9saW5nX2NhdmFscnk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="" title=""> <br>
为了更好的观察效果，我们要打开三个终端对不同的容器做操作，在后面我们用编号来表达不同的终端，编号对应如下：</p>

<ul>
<li>1号 : kafka server  </li>
<li>2号 : 消息生产者(producer)  </li>
<li>3号 : 消息消费者(consumer)</li>
</ul>

<p>1号终端中输入以下命令，即可看到kafka server的实时输出信息：</p>

<pre class="prettyprint"><code class=" hljs bash">docker logs <span class="hljs-operator">-f</span> dockerkafka_kafka_server_1</code></pre>

<p>输入命令后看到的信息如下图，与zookeeper连接成功，启动成功: <br>
<img src="https://img-blog.csdn.net/20170511000234483?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYm9saW5nX2NhdmFscnk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="" title=""> <br>
2号终端中输入以下命令，进入消息生产者容器：</p>

<pre class="prettyprint"><code class=" hljs bash">docker <span class="hljs-keyword">exec</span> -it dockerkafka_message_producer_1 /bin/bash</code></pre>

<p>再执行以下命令创建一个主题：</p>



<pre class="prettyprint"><code class=" hljs brainfuck"><span class="hljs-comment">$WORK_PATH/$KAFKA_PACKAGE_NAME/bin/kafka</span><span class="hljs-literal">-</span><span class="hljs-comment">topics</span><span class="hljs-string">.</span><span class="hljs-comment">sh</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">create</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">zookeeper</span> <span class="hljs-comment">zkhost:2181</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">replication</span><span class="hljs-literal">-</span><span class="hljs-comment">factor</span> <span class="hljs-comment">1</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">partitions</span> <span class="hljs-comment">1</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">topic</span> <span class="hljs-comment">test001</span></code></pre>

<p>3号终端中输入以下命令，进入消息消费者容器：</p>



<pre class="prettyprint"><code class=" hljs bash">docker <span class="hljs-keyword">exec</span> -it dockerkafka_message_consumer_1 /bin/bash</code></pre>

<p>执行以下命令可以查看当前kafka sever上的所有主题：</p>



<pre class="prettyprint"><code class=" hljs lasso"><span class="hljs-variable">$WORK_PATH</span><span class="hljs-subst">/</span><span class="hljs-variable">$KAFKA_PACKAGE_NAME</span>/bin/kafka<span class="hljs-attribute">-topics</span><span class="hljs-built_in">.</span>sh <span class="hljs-subst">--</span><span class="hljs-built_in">list</span> <span class="hljs-subst">--</span>zookeeper zkhost:<span class="hljs-number">2181</span></code></pre>

<p>可以看到刚才在2号终端创建的主题，如下图： <br>
<img src="https://img-blog.csdn.net/20170511001957882?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYm9saW5nX2NhdmFscnk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="" title=""> <br>
继续在3号终端上执行以下命令，即可订阅主题为test001的消息：</p>

<pre class="prettyprint"><code class=" hljs lasso"><span class="hljs-variable">$WORK_PATH</span><span class="hljs-subst">/</span><span class="hljs-variable">$KAFKA_PACKAGE_NAME</span>/bin/kafka<span class="hljs-attribute">-console</span><span class="hljs-attribute">-consumer</span><span class="hljs-built_in">.</span>sh <span class="hljs-subst">--</span>zookeeper zkhost:<span class="hljs-number">2181</span> <span class="hljs-subst">--</span>topic test001 <span class="hljs-subst">--</span>from<span class="hljs-attribute">-beginning</span></code></pre>

<p>此时回到1号终端，可以看到消息订阅者容器的连接信息，如下图： <br>
<img src="https://img-blog.csdn.net/20170511002401953?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYm9saW5nX2NhdmFscnk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="" title=""> <br>
现在主题也创建了，订阅也添加了，可以测试消息的发送和接收了：</p>

<p>在2号终端，执行以下命令，就可以进入发送消息的交互模式：</p>

<pre class="prettyprint"><code class=" hljs lasso"><span class="hljs-variable">$WORK_PATH</span><span class="hljs-subst">/</span><span class="hljs-variable">$KAFKA_PACKAGE_NAME</span>/bin/kafka<span class="hljs-attribute">-console</span><span class="hljs-attribute">-producer</span><span class="hljs-built_in">.</span>sh <span class="hljs-subst">--</span>broker<span class="hljs-attribute">-list</span> kafkahost:<span class="hljs-number">9092</span> <span class="hljs-subst">--</span>topic test001</code></pre>

<p>此时已经进入了交互模式，继续输入一个字符串，例如hello world!，再输入回车键，即可发送内容为”hello world!”的消息，如下图： <br>
<img src="https://img-blog.csdn.net/20170511003028474?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYm9saW5nX2NhdmFscnk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="" title=""> <br>
现在回到3号终端，可以看到已经接收到了最新的消息，内容被打印出来了，如下图： <br>
<img src="https://img-blog.csdn.net/20170511003129241?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYm9saW5nX2NhdmFscnk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="" title=""> <br>
最后我们回到1号终端，能看到消息生产者的连接信息，如下图： <br>
<img src="https://img-blog.csdn.net/20170511003530364?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYm9saW5nX2NhdmFscnk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="" title=""> <br>
至此，我们快速体验了kafka的消息发布订阅功能，下一节我们再细说docker下kafka环境的搭建过程。</p>

<h3 id="本地环境搭建">本地环境搭建</h3>

<p>上节我们快速体验了kafka的消息分发和订阅功能，但是对环境搭建的印象仅仅是执行了几个命令和脚本，本章我们通过实战来学习如何编写这些脚本，搭建本地kafka环境；</p>

<p>本次实践会制作docker镜像，所用的材料请在此获取：<a href="https://github.com/zq2599/docker_kafka" rel="nofollow">https://github.com/zq2599/docker_kafka</a></p>

<p>整个环境涉及到多个容器，我们先把它们全部列出来，再梳理一下之间的关系，如下图：</p>

<p><img src="https://img-blog.csdn.net/20170510203141928?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYm9saW5nX2NhdmFscnk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="" title=""></p>

<ul>
<li>kafka sever提供消息服务;  </li>
<li>message producer的作用是产生执行主题的消息;  </li>
<li>message consumer的作用是订阅指定主题的消息并消费掉。</li>
</ul>

<p>zookeeper <br>
zookeeper使用单机版，没什么需要定制的，因此直接使用官方镜像即可，daocloud.io/library/zookeeper:3.3.6</p>

<p>kafka sever <br>
去hub.docker.com上搜索kafka，没看到官方标志的镜像，还是自己做一个吧，写Dockerfile之前先准备两个材料：kafka安装包和启动kafka的shell脚本；</p>

<p>kafka安装包用的是2.9.2-0.8.1版本，在  <br>
git@github.com:zq2599/docker_kafka.git中，请clone获取；</p>

<p>启动kafka server的shell脚本内容如下，很简单，在kafka的bin目录下执行脚本启动server即可：</p>

<pre class="prettyprint"><code class=" hljs bash"><span class="hljs-shebang">#!/bin/bash</span>
<span class="hljs-variable">$WORK_PATH</span>/<span class="hljs-variable">$KAFKA_PACKAGE_NAME</span>/bin/kafka-server-start.sh <span class="hljs-variable">$WORK_PATH</span>/<span class="hljs-variable">$KAFKA_PACKAGE_NAME</span>/config/server.properties</code></pre>

<p>接下来可以编写Dockerfile了，如下：</p>



<pre class="prettyprint"><code class=" hljs perl"><span class="hljs-comment"># Docker image of kafka</span>
<span class="hljs-comment"># VERSION 0.0.1</span>
<span class="hljs-comment"># Author: bolingcavalry</span>

<span class="hljs-comment">#基础镜像使用tomcat，这样可以免于设置java环境</span>
FROM daocloud.io/library/tomcat:<span class="hljs-number">7.0</span>.<span class="hljs-number">77</span>-jre8

<span class="hljs-comment">#作者</span>
MAINTAINER BolingCavalry &lt;zq2599<span class="hljs-variable">@gmail</span>.com&gt;

<span class="hljs-comment">#定义工作目录</span>
ENV WORK_PATH /usr/<span class="hljs-keyword">local</span>/work

<span class="hljs-comment">#定义kafka文件夹名称</span>
ENV KAFKA_PACKAGE_NAME kafka_2.<span class="hljs-number">9.2</span>-<span class="hljs-number">0</span>.<span class="hljs-number">8.1</span>

<span class="hljs-comment">#创建工作目录</span>
RUN <span class="hljs-keyword">mkdir</span> -p <span class="hljs-variable">$WORK_PATH</span>

<span class="hljs-comment">#把启动server的shell复制到工作目录</span>
COPY ./start_server.sh <span class="hljs-variable">$WORK_PATH</span>/

<span class="hljs-comment">#把kafka压缩文件复制到工作目录</span>
COPY ./<span class="hljs-variable">$KAFKA_PACKAGE_NAME</span>.tgz <span class="hljs-variable">$WORK_PATH</span>/

<span class="hljs-comment">#解压缩</span>
RUN tar -xvf <span class="hljs-variable">$WORK_PATH</span>/<span class="hljs-variable">$KAFKA_PACKAGE_NAME</span>.tgz -C <span class="hljs-variable">$WORK_PATH</span>/

<span class="hljs-comment">#删除压缩文件</span>
RUN rm <span class="hljs-variable">$WORK_PATH</span>/<span class="hljs-variable">$KAFKA_PACKAGE_NAME</span>.tgz

<span class="hljs-comment">#执行sed命令修改文件，将连接zk的ip改为link参数对应的zookeeper容器的别名</span>
RUN sed -i <span class="hljs-string">'s/zookeeper.connect=localhost:2181/zookeeper.connect=zkhost:2181/g'</span> <span class="hljs-variable">$WORK_PATH</span>/<span class="hljs-variable">$KAFKA_PACKAGE_NAME</span>/config/server.properties

<span class="hljs-comment">#给shell赋予执行权限</span>
RUN <span class="hljs-keyword">chmod</span> a+<span class="hljs-keyword">x</span> <span class="hljs-variable">$WORK_PATH</span>/start_server.sh</code></pre>

<p>如脚本所示，操作并不复杂，复制解压kafka安装包，启动shell脚本，再把配置文件中zookeeper的ip改成link时zookeeper的别名；</p>

<p>Dockerfile编写完成后，和kafka_2.9.2-0.8.1.tgz以及start_server.sh放在同一个目录下，用控制台在此目录下执行：</p>



<pre class="prettyprint"><code class=" hljs lasso">docker build <span class="hljs-attribute">-t</span> bolingcavalry/kafka:<span class="hljs-number">0.0</span><span class="hljs-number">.1</span> <span class="hljs-built_in">.</span></code></pre>

<p>镜像构建成功后，新建一个目录编写docker-compose.yml脚本，如下：</p>



<pre class="prettyprint"><code class=" hljs haml">version: '2'
services:
  zk_server: 
    image: daocloud.io/library/zookeeper:3.3.6
    restart: always
  kafka_server: 
    image: bolingcavalry/kafka:0.0.1
    links: 
      -<span class="ruby"> <span class="hljs-symbol">zk_server:</span>zkhost
</span>    command: /bin/sh -c '/usr/local/work/start_server.sh'
    restart: always
  message_producer: 
    image: bolingcavalry/kafka:0.0.1
    links: 
      -<span class="ruby"> <span class="hljs-symbol">zk_server:</span>zkhost
</span>      -<span class="ruby"> <span class="hljs-symbol">kafka_server:</span>kafkahost
</span>    restart: always
  message_consumer: 
    image: bolingcavalry/kafka:0.0.1
    links: 
      -<span class="ruby"> <span class="hljs-symbol">zk_server:</span>zkhost
</span>    restart: always</code></pre>

<p>docker-compose.yml中配置了四个容器：  <br>
1. zookeeper是官方的;  <br>
2. 其他三个都是用刚刚制作的bolingcavalry/kafka做镜像生成的;  <br>
3. kafka_server在启动时执行了start_server.sh脚本把服务启动起来了;  <br>
4. message_producer和message_consumer都仅仅是将kafka环境安装好了，以便于通过命令行发送或者订阅消息，但是这两个容器本身并未启动server；  <br>
5. kafka_server，message_producer，message_consumer都通过link参数连接到了zookeeper容器，并且message_producer还连接到了kafka server，因为发送消息的时候会用到kafka server的ip地址；</p>

<p>现在打开终端，在docker-compose.yml所在目录下执行docker-compose up -d，即可启动所有容器；</p>

<p>至此，本地环境搭建已经成功了，我们可以通过命令行体验kafka的消息发布订阅服务，具体的命令可以参考上一节。</p>

<p>以上就是本地搭建kafka的全过程。</p>

<p>文章转自：   <a href="https://blog.csdn.net/boling_cavalry/article/details/71576775" rel="nofollow">https://blog.csdn.net/boling_cavalry/article/details/71576775</a></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>