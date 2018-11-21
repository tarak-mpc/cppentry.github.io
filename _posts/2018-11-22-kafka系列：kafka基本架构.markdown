---
layout:     post
title:      kafka系列：kafka基本架构
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/qq_37095882/article/details/81024048				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p><strong><span style="font-family:SimSun;">1、拓扑结构图</span></strong></p><p><img src="https://img-blog.csdn.net/2018071223260269?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3MDk1ODgy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt=""><br></p><p><strong><span style="font-family:SimSun;">2、kafka中相关组件的解释</span></strong></p><p>    （1）producer：消息生产者，发布消息到 kafka 集群的终端或服务。<br></p><p>    （2）broker：kafka 集群中包含的服务器。<br>    （3）topic：每条发布到 kafka 集群的消息属于的类别，即 kafka 是面向 topic 的。<br>    （4）partition：partition 是物理上的概念，每个 topic 包含一个或多个 partition。kafka 分配的单位是 partition。<br>    （5）consumer：从 kafka 集群中消费消息的终端或服务。<br>   （6）Consumer group：high-level consumer API 中，每个 consumer 都属于一个 consumer group，每条消息只能被 consumer group 中的一个 Consumer 消费，但可以被多个 consumer group 消费。<br>    （7）replica：partition 的副本，保障 partition 的高可用。<br>    （8）leader：replica 中的一个角色， producer 和 consumer 只跟 leader 交互。<br>    （9）follower：replica 中的一个角色，从 leader 中复制数据。<br>    （10）controller：kafka 集群中的其中一个服务器，用来进行 leader election 以及 各种 failover。<br></p><p>    （11）zookeeper：kafka 通过 zookeeper 来存储集群的 meta 信息。</p><p>3、Kafka在zk上的节点树</p><p><img src="https://img-blog.csdn.net/20180712233818716?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3MDk1ODgy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt=""><br></p>            </div>
                </div>