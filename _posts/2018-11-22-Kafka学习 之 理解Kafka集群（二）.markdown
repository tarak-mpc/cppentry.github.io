---
layout:     post
title:      Kafka学习 之 理解Kafka集群（二）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/qq_33394088/article/details/81001909				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>在学习之前，已经假设已经成功搭建了Kafka集群，开始下面的学习：</p>



<h3 id="1-理论学习">1. 理论学习</h3>

<ul>
<li>broker：生产环境中，一台服务器上只会安装一个 Kafka 软件，这台服务器就是一个 Kafka Server，此时 Kafka Server 称为 broker；在我们的实验环境中，一个一台服务器上安装了三个 broker。</li>
<li>topic：发布到 Kafka 集群上的消息都属于某一个主题，这个主题称为 topic，topic 存储在 broker 中;</li>
<li><p>partion：一个 topic 包括多个 partition(分区)，每个 partition 可以有多个 replica(副本); producer 发送的消息存储在 partition 中。</p></li>
<li><p>producer: producer 是用来产生消息的，负责将消息发送到 Kafka broker 的 topic 上;</p></li>
<li>consumer: consumer 是用来消费消息的，用于从 Kafka broker 上的 topic 中读取消息;</li>
<li>broker 和 consumer 使用 Zookeeper 管理状态信息</li>
</ul>

<p><img src="https://doc.shiyanlou.com/document-uid370453labid3125timestamp1500692161348.png/wm" alt="这里写图片描述" title=""></p>

<h3 id="2-创建topic参数介绍">2. 创建Topic参数介绍</h3>

<p>在上一节中，我们使用如下命令创建了一个 topic：</p>



<pre class="prettyprint"><code class=" hljs haml">./kafka-topics.sh --create 
-<span class="ruby">-zookeeper <span class="hljs-symbol">localhost:</span><span class="hljs-number">2181</span> 
</span>-<span class="ruby">-replication-factor <span class="hljs-number">1</span> 
</span>-<span class="ruby">-partitions <span class="hljs-number">1</span>
</span>-<span class="ruby">-topic myFirstTopic</span></code></pre>

<p>上述命令详细解释如下:</p>

<ol>
<li>kafka-topics.sh：Kafka 提供的一个 shell 脚本文件(位于 bin 目录中)，用于创建或查看 topic 信息。 </li>
<li>–create：shell 脚本的参数，告诉 shell 脚本：要创建一个 topic。</li>
<li>–zookeeper localhost:2181：shell 脚本的参数，告诉 shell 脚本 Zookeeper 的地址，用于保存 topic 元数据信息。 </li>
<li>–partitions 1：shell 脚本参数，告诉 shell 脚本：所创建的这个 topic 的 partition 个数为1 </li>
<li>–replication-factor 1：shell脚本参数，告诉 shell 脚本：每个 partition 的副本数为1 </li>
<li>–topic myFirstTopic：shell 脚本参数，告诉 shell 脚本：创建的 topic 的名称为 myFirstTopic。</li>
</ol>

<h3 id="3-查看-topic-的-partition-数量和副本数量">3. 查看 topic 的 partition 数量和副本数量</h3>



<pre class="prettyprint"><code class=" hljs vala"><span class="hljs-preprocessor">#切换到 /opt/kafka/bin 目录。如果当前目录就是bin目录，则不需要运行此命令。</span>
cd /opt/kafka/bin
<span class="hljs-preprocessor">#运行查看topic信息的shell脚本</span>
./kafka-topics.sh --zookeeper localhost:<span class="hljs-number">2181</span> --describe --topic myFirstTopic</code></pre>

<p><img src="https://img-blog.csdn.net/20180711155001964?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMzk0MDg4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<ul>
<li><p>结果的第一行：topic 信息概述：Topic 名称为 myFirstTopic，partition 数量为1，副本数为1，这个 partition 的详细配置信息见下面三行。</p></li>
<li><p>紧接每一行描述了一个 partition 的信息，后面的 leader、replicas和ISR 涉及到 kafka 内核结构，后续单独开辟实验详细讲解，此时你只需要知道，这些参数保证了 kafka 集群的健壮性，保证消息不丢失，保证消息的高吞吐量。</p></li>
</ul>

<h3 id="4-查看-zookeeper-上的元数据信息">4. 查看 Zookeeper 上的元数据信息</h3>



<pre class="prettyprint"><code class=" hljs vala"><span class="hljs-preprocessor">#切换到 Kafka bin 目录，查看 Zookeeper 上元数据信息的命令在这个目录下</span>
cd /opt/kafka/bin     
<span class="hljs-preprocessor">#运行zookeeper shell命令,进入Zookeeper shell命令模式</span>
./zookeeper-shell.sh localhost:<span class="hljs-number">2181</span> </code></pre>

<p><img src="https://doc.shiyanlou.com/document-uid370453labid3125timestamp1498744973637.png/wm" alt="image" title=""></p>

<p>接着运行命令： </p>



<pre class="prettyprint"><code class=" hljs mel"><span class="hljs-keyword">ls</span> /</code></pre>

<p>从结果可以看出 cluster、brokers和consumers 等节点，其中存储了 Kafka 相关的元数据信息,接下来可以去自行查看这些元数据信息。</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>