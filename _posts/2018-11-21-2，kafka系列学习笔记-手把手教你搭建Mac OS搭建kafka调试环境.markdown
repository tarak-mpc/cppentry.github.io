---
layout:     post
title:      2，kafka系列学习笔记-手把手教你搭建Mac OS搭建kafka调试环境
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/u010970014/article/details/82977347				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>1，kafka install。  这里采用mac下的homebrew方式安装。<br>
<code>brew install kafka</code></p>
<p>2，通过命令行安装之后，查看一下kafka的安装位置<br>
<code>//安装目录位置</code><br>
<code>/usr/local/Cellar/kafka</code><br>
<code>//zookeeper和kafka启动位置</code><br>
<code>/usr/local/etc/kafka</code></p>
<p>3，启动流程<br>
先进入到/usr/local/etc/kafka目录下<br>
启动Zookeeper<br>
<code>zookeeper-server-start /usr/local/etc/kafka/zookeeper.properties</code></p>
<p>在开启一个窗口进入到同样目录下启动kafka<br>
<code>kafka-server-start /usr/local/etc/kafka/server.properties</code></p>
<p>4，创建topic</p>
<ol>
<li>
<p>测试kafka与Zookeeper是否启动<br>
<code>jps</code></p>
</li>
<li>
<p>创建单分区只有一个副本，创建一个test的主题<br>
<code>kafka-topics --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic test</code></p>
</li>
</ol>
<p>5，开启消费者窗口<br>
先进入到/usr/local/Cellar/kafka目录下</p>
<pre><code>`kafka-console-consumer --bootstrap-server localhost:9092 --topic test --from-beginning`
</code></pre>
<p>6，执行生产者<br>
开启另外一个窗口，并进入到/usr/local/Cellar/kafka目录下</p>
<p><code>kafka-console-producer --broker-list localhost:9092 --topic test</code></p>
<p>通过上面的步骤就可以进行kafka简单的调试啦，下一篇我们来学习，kafka在java api中的使用。</p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>