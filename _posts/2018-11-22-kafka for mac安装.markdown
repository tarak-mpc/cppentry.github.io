---
layout:     post
title:      kafka for mac安装
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：刘三洋					https://blog.csdn.net/qq_37133717/article/details/83822180				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2><a id="mac_kafka__0"></a>mac kafka 环境搭建:</h2>
<blockquote>
<p>$ brew install kafka</p>
</blockquote>
<h2><a id="_4"></a>安装目录：</h2>
<p>/usr/local/Cellar/kafka/2.0.0</p>
<h2><a id="_8"></a>配置文件目录：</h2>
<p>/usr/local/etc/kafka/server.properties<br>
/usr/local/etc/kafka/zookeeper.properties</p>
<h2><a id="_12"></a>操作命令：</h2>
<p><strong>首先启动zookeeper：</strong></p>
<blockquote>
<p>$ zookeeper-server-start /usr/local/etc/kafka/zookeeper.properties &amp;</p>
</blockquote>
<p><strong>然后启动 kafka 服务：</strong></p>
<blockquote>
<p>$ kafka-server-start /usr/local/etc/kafka/server.properties &amp;</p>
</blockquote>
<p><strong>创建topic:</strong><br>
让我们使用单个分区和只有一个副本创建一个名为“test”的主题</p>
<blockquote>
<p>$ kafka-topics --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic test</p>
</blockquote>
<blockquote>
<p>Created topic “test”.</p>
</blockquote>
<p><strong>查看创建的topic:</strong><br>
我们现在可以看到该主题，如果我们运行list topic命令：</p>
<blockquote>
<p>$ kafka-topics --list --zookeeper localhost:2181</p>
</blockquote>
<blockquote>
<p>test</p>
</blockquote>
<p><strong>发送一些消息:</strong><br>
Kafka提供了一个命令行客户端，它将从文件或标准输入接收输入，并将其作为消息发送到Kafka集群。默认情况下，每行都将作为单独的消息发送。</p>
<p>运行生产者，然后在控制台中键入一些消息发送到服务器。</p>
<blockquote>
<p>$ kafka-console-producer --broker-list localhost:9092 --topic test<br>
第一条消息<br>
第二条消息</p>
</blockquote>
<p><strong>消费消息</strong><br>
Kafka还有一个命令行消费者，将消息转储到标准输出。</p>
<blockquote>
<p>$ kafka-console-consumer --bootstrap-server localhost:9092 --topic test --from-beginning<br>
第一条消息<br>
第二条消息</p>
</blockquote>
<p>如果你有上面的每个命令运行在不同的终端，那么你现在应该能够输入消息到生产者终端，看到他们出现在消费者终端。</p>
<p>所有命令行工具都有其他选项; 运行没有参数的命令将显示详细记录它们的使用信息。</p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>