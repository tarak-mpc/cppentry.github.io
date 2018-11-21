---
layout:     post
title:      mac环境kafka启动时报No such file or directory解决办法
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>1、今天尝试了一下kafka的安装与配置，因为之前一直用RocketMQ，所以没用过kafka，今天准备花点时间学一下kafka。</p>
<p>首先去官网下载kafka的最新版本，下载链接：</p>
<p><a href="https://www.apache.org/dyn/closer.cgi?path=/kafka/0.11.0.0/kafka_2.11-0.11.0.0.tgz" rel="nofollow" class="external-link" style="color:rgb(53,114,176);text-decoration:none;">https://www.apache.org/dyn/closer.cgi?path=/kafka/0.11.0.0/kafka_2.11-0.11.0.0.tgz</a></p>
<p><a href="https://www.apache.org/dyn/closer.cgi?path=/kafka/0.11.0.0/kafka_2.11-0.11.0.0.tgz" rel="nofollow" class="external-link" style="color:rgb(53,114,176);text-decoration:none;"><br></a></p>
<p><span>2、下载后，解压，如图：<br></span></p>
<p><img src="https://img-blog.csdn.net/20171122204357264?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveWFuZ2JvMTk4OTE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p></p>
<p>跟很多中间件一下，有bin和config等文件夹，bin里存放的是一些kafka的命令，config存放的是一些配置文件。</p>
<p>考虑到kafka启动要用到zookeeper，所以我事先在本地打开了zookeeper的服务：</p>
<p><img src="https://img-blog.csdn.net/20171122204502749?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveWFuZ2JvMTk4OTE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p></p>
<p>启动之后，用telnet命令测试一下2181（默认配置，在zoo.cfg里，可以自己修改）端口。</p>
<p>3、cd到kafka的bin文件路径，找到kafka-server-start.sh这个命令，很显然，这个是启动命令，我们执行它：</p>
<p>./kafka-server-start.sh start，结果发现竟然报错（找不到文件，也没说找不到什么文件）：</p>
<p><img src="https://img-blog.csdn.net/20171122204600623?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveWFuZ2JvMTk4OTE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p>4、这个问题百度找了很久，都没找到相关的解决办法，那看来一定是我忘记什么操作了，因为是第一次用，所以就回忆了一下以前刚开始用中间件的时候，都有哪些步骤，是不是kafka也要有这样的步骤。</p>
<p>然后想到了，是不是kafka也要依赖某个配置文件启动呢？接下来我就做了个以下尝试：</p>
<p>执行命令：./kafka-server-start.sh ../config/server.properties，且先不管这个server.properties里面什么样，单从文件名称来看，就很重要。然后如下图，启动成功了：</p>
<p><img src="https://img-blog.csdn.net/20171122204656566?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveWFuZ2JvMTk4OTE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<img src="https://img-blog.csdn.net/20171122204741098?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveWFuZ2JvMTk4OTE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><p></p>
<p>5、启动成功了，做个简单的例子，生产一个HelloWorld的topic：</p>
<p>./kafka-console-producer.sh --zookeeper localhost:2181 --topic HelloWorld</p>
<p><img src="https://img-blog.csdn.net/20171122204835713?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveWFuZ2JvMTk4OTE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p>发现报异常，那么我不用zookeeper，用broker-list试试：</p>
<p>./kafka-console-producer.sh --broker-list localhost:9092 --topic HelloWorld</p>
<p><img src="https://img-blog.csdn.net/20171122204915501?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveWFuZ2JvMTk4OTE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p></p>
<p>看样子是可以了，新打开一个终端，输入：</p>
<p>./kafka-console-consumer.sh --zookeeper localhost:2181 --topic HelloWorld --from-beginning</p>
<p>然后在原来终端随便输入点什么，发现可以接收到消息了：</p>
<p><img src="https://img-blog.csdn.net/20171122205325196?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveWFuZ2JvMTk4OTE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><img src="https://img-blog.csdn.net/20171122205336418?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveWFuZ2JvMTk4OTE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<br><span></span>
            </div>
                </div>