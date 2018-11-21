---
layout:     post
title:      Apache Kafka安装和使用（入门教程轻松学）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：欢迎朋友转载，但请注明作者和原文链接。谢谢合作！					https://blog.csdn.net/liyiming2017/article/details/82790574				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>本入门教程，涵盖Kafka核心内容，通过实例和大量图表，帮助学习者理解，任何问题欢迎留言。</p>

<p>目录：</p>

<ul><li><a href="https://blog.csdn.net/liyiming2017/article/details/82790040" rel="nofollow">kafka简介</a></li>
	<li><a href="https://blog.csdn.net/liyiming2017/article/details/82790574" rel="nofollow">kafka安装和使用</a></li>
	<li><a href="https://blog.csdn.net/liyiming2017/article/details/82805479" rel="nofollow">kafka核心概念</a></li>
	<li><a href="https://blog.csdn.net/liyiming2017/article/details/82843036" rel="nofollow">kafka核心组件和流程--控制器</a></li>
	<li><a href="https://blog.csdn.net/liyiming2017/article/details/82867765" rel="nofollow">kafka核心组件和流程--协调器</a></li>
	<li><a href="https://blog.csdn.net/liyiming2017/article/details/82968550" rel="nofollow">kafka核心组件和流程--日志管理器</a></li>
	<li><a href="https://blog.csdn.net/liyiming2017/article/details/82984638" rel="nofollow">kafka核心组件和流程--副本管理器</a></li>
	<li><a href="https://blog.csdn.net/liyiming2017/article/details/82986606" rel="nofollow">kafka编程实战</a></li>
</ul><h1>第二章 Kafka安装和使用</h1>

<p> </p>

<h3><strong>单机环境</strong></h3>

<p>官方建议使用JDK 1.8版本，因此本文使用的环境都是JDK1.8。关于JDK的安装，本文不再详述，默认Java环境已经具备。</p>

<p>由于Kafka依赖zookeeper，kafka通过zookeeper现实分布式系统的协调，所以我们需要先安装zookeeper。</p>

<p>接下来我们按照如下步骤，一步步来安装kafka：</p>

<p>1、下载zookeeper，解压。</p>

<p>下载地址：<a href="https://zookeeper.apache.org/releases.html#download" rel="nofollow">https://zookeeper.apache.org/releases.html#download</a></p>

<p>2、创建zookeeper配置文件</p>

<p>在zookeeper解压后的目录下找到conf文件夹，进入后，复制文件zoo_sample.cfg，并命名为zoo.cfg</p>

<p>zoo.cfg中一共四个配置项，可以使用默认配置。</p>

<p>3、启动zookeeper。</p>

<p>进入zookeeper根目录执行 <span style="color:#7c79e5;">bin/zkServer.sh start</span></p>

<p><img alt="" class="has" src="https://img-blog.csdn.net/20180920170538669?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpeWltaW5nMjAxNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></p>

<p>4、下载kafka，解压。</p>

<p>kafka 2.0版本下载地址：</p>

<p><a href="https://www.apache.org/dyn/closer.cgi?path=/kafka/2.0.0/kafka-2.0.0-src.tgz" rel="nofollow">https://www.apache.org/dyn/closer.cgi?path=/kafka/2.0.0/kafka-2.0.0-src.tgz</a></p>

<p>5、修改kafka的配置文件</p>

<p>进入kafka根目录下的config文件夹下，打开server.properties,修改如下配置项</p>

<p><span style="color:#7c79e5;">zookeeper.connect=localhost:2181</span></p>

<p><span style="color:#7c79e5;">broker.id=0</span></p>

<p><span style="color:#7c79e5;">log.dirs=/tmp/kafka-logs</span></p>

<p>zookeeper.connect是zookeeper的链接信息，broker.id是当前kafka实例的id，log.dirs是kafka存储消息内容的路径。</p>

<p>6、启动kafka</p>

<p>进入kafka根目录执行 <span style="color:#7c79e5;">bin/kafka-server-start.sh config/server.properties</span></p>

<p>此命令告诉kaka启动时使用config/server.properties配置项</p>

<p><img alt="" class="has" src="https://img-blog.csdn.net/20180920170740348?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpeWltaW5nMjAxNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></p>

<p>启动kafka后，如果控制台没有报错信息，那么kafka应该已经启动成功了，我们可以通过查看zookeeper中相关节点值来确认。步骤如下：</p>

<p>1、启动zookeeper的client</p>

<p>进入zookeeper根目录下，执行 <span style="color:#7c79e5;">bin/zkCli.sh -server 127.0.0.1:2181</span>。启动成功后如下图</p>

<p><img alt="" class="has" src="https://img-blog.csdn.net/2018092017091225?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpeWltaW5nMjAxNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></p>

<p>2、输入命令 <span style="color:#7c79e5;">ls /brokers</span>，回车，可以看到如下信息：</p>

<p><img alt="" class="has" src="https://img-blog.csdn.net/20180920170942747?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpeWltaW5nMjAxNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></p>

<p>这些子节点存储的就是kafka集群管理的数据。broker是kafka的一个服务单元实例</p>

<p>3、我看看一下ids这个节点下的数据，输入命令 <span style="color:#7c79e5;">ls /brokers/ids</span>，可以看到如下信息：</p>

<p><img alt="" class="has" src="https://img-blog.csdn.net/20180920171211499?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpeWltaW5nMjAxNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></p>

<p>还记得我们在配置单机环境时，修改的kafka配置项broker.id=0 吗？这里的0就是表示那个kafka的实例已经加入了kafka集群。</p>

<h3> </h3>

<h3><strong>集群环境</strong></h3>

<p>集群环境的搭建也很简单，在单机环境的基础上，让多个单机连接到同一个zookeeper即可。需要注意两点：</p>

<p>1、每个实例设置不同的broker.id。</p>

<p>2、如果多个实例部署在同一台服务器，还要注意修改log.dirs为不同目录，确保消息存储时不会有冲突。</p>

<p>集群环境的具体搭建，在此精简教程中不再做详细讨论。</p>

<p> </p>

<h3>发出你的第一条kafka消息</h3>

<p>我们通过kafka带的工具来创建一个topic，然后尝试发送和消费一个消息，直观的去感受下kafka。</p>

<p>1、创建topic</p>

<p>进入kafka根目录，执行如下命令：</p>

<p><span style="color:#7c79e5;">bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic study</span></p>

<p>执行成功后，创建了study这个topic，如下图所示：</p>

<p><img alt="" class="has" src="https://img-blog.csdn.net/20180920171419964?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpeWltaW5nMjAxNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></p>

<p>此命令行有几个参数，分别指明了zookeeper的链接信息，分区和副本的数量等。关于分区和副本后续会仔细讲解，现在不用过多关注。</p>

<p>2、启动消费者</p>

<p>我们开启一个消费者并且订阅study这个topic，执行如下命令:</p>

<p><span style="color:#7c79e5;">bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic study --from-beginning</span></p>

<p>看到如下图，光标停留在最前面，没有任何信息输出，说明启动消费者成功，此时在等待新的消息。</p>

<p><img alt="" class="has" src="https://img-blog.csdn.net/20180920171524900?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpeWltaW5nMjAxNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></p>

<p>3、开启生产者</p>

<p>新打开一个命令窗口，输入命令</p>

<p><span style="color:#7c79e5;">bin/kafka-console-producer.sh --broker-list localhost:9092 --topic study</span></p>

<p>启动成功后，如下图，等待你输入新的消息。</p>

<p><img alt="" class="has" src="https://img-blog.csdn.net/20180920171733357?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpeWltaW5nMjAxNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></p>

<p>4、发送你的第一条消息</p>

<p>在上面生产者的窗口输入一条消息 hello kafka,点击回车，如下图：</p>

<p><img alt="" class="has" src="https://img-blog.csdn.net/2018092017182745?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpeWltaW5nMjAxNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70">此时切换到消费者的窗口，可以看到消费者已经消费到这条消息，在窗口中打印了出来。</p>

<p><img alt="" class="has" src="https://img-blog.csdn.net/20180920171938464?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpeWltaW5nMjAxNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></p>

<p>至此我们走完了一个发送消息的流程，可以看到我们经历了创建topic、启动生产者、消费者、生产者生产消息、消费者消费消息，这几个步骤。</p>

<p>小结：通过本章节学习，相信你已经能够成功搭建起kafka单机环境，甚至集群环境。然后通过kafka自带的工具，直观的感受了kafka运转的整个过程。接下来的章节我们将会进入kafka的核心领域，也是本教程的重点章节，只有理解了kafka内在的设计理念和原理，才能做到活学活用。</p>

<p>下一步：开始<a href="https://blog.csdn.net/liyiming2017/article/details/82805479" rel="nofollow">《</a><a href="https://blog.csdn.net/liyiming2017/article/details/82805479" rel="nofollow">kafka核心概念</a><a href="https://blog.csdn.net/liyiming2017/article/details/82790574" rel="nofollow">》</a>的学习</p>            </div>
                </div>