---
layout:     post
title:      【Apache Flume系列】Flume-ng failover 以及Load balance测试及注意事项
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/WeiJonathan/article/details/38557959				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>好久没写博客了。最近在研究storm、flume和kafka。今天给大伙写下我测试flume failover以及load balance的场景以及一些结论；</p>
<p>测试环境包含5个配置文件，也就是5个agent。</p>
<p>一个主的配置文件，也就是我们配置failover以及load balance关系的配置文件（flume-sink.properties），这个文件在下面的场景</p>
<p>会变动，所以这里就不列举出来了，会在具体的场景中写明；</p>
<p>其他4个配置文件类似：</p>
<p></p>
<pre><code class="language-html">#Name the compents on this agent
a2.sources = r1
a2.sinks = k1
a2.channels = c1

#Describe/configure the source
a2.sources.r1.type = avro
a2.sources.r1.channels = c1
a2.sources.r1.bind = 192.168.220.159
a2.sources.r1.port = 44411

#Describe the sink
a2.sinks.k1.type = logger
a2.sinks.k1.channel = c1

#Use a channel which buffers events in memory
a2.channels.c1.type = memory
a2.channels.c1.capacity = 1000
a2.channels.c1.transactionCapacity = 100</code></pre>另外三个只需修改agent名称即可。
<p></p>
<p>场景1：---想法（flume failover以及load balance同时应用，兼顾负载和容错，使环境更加有保障）</p>
<p>配置文件（flume-sink.properties）：</p>
<p></p>
<pre><code class="language-plain">#Name the compents on this agent
a1.sources = r1
a1.sinks = k1 k2 k3
a1.channels = c1

#Describe the sinkgroups
a1.sinkgroups = g1 g2
a1.sinkgroups.g1.sinks = k1 k2
a1.sinkgroups.g1.processor.type = failover
a1.sinkgroups.g1.processor.priority.k1 = 10
a1.sinkgroups.g1.processor.priority.k2 = 5
a1.sinkgroups.g1.processor.maxpenalty = 10000

a1.sinkgroups.g2.sinks = k1 k3
a1.sinkgroups.g2.processor.type = load_balance
a1.sinkgroups.g2.processor.backoff = true
a1.sinkgroups.g2.processor.selector = round_robin

#Describe/config the source
a1.sources.r1.type = syslogtcp
a1.sources.r1.port = 5140
a1.sources.r1.host = localhost
a1.sources.r1.channels = c1

#Describe the sink
a1.sinks.k1.type = avro
a1.sinks.k1.channel = c1
a1.sinks.k1.hostname = 192.168.220.159
a1.sinks.k1.port = 44411

a1.sinks.k2.type = avro
a1.sinks.k2.channel = c1
a1.sinks.k2.hostname = 192.168.220.159
a1.sinks.k2.port = 44422

a1.sinks.k4.type = avro
a1.sinks.k4.channel = c1</code></pre>测试结果：
<p></p>
<p><img src="https://img-blog.csdn.net/20140814143923844?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvV2VpSm9uYXRoYW4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br></p>
<p>发现K1，K2，K3都可以接收到数据，但是这里K2的优先级别是最低的，原本不应该获得数据的；除非K1挂掉了。<br></p>
<p>这个测试之前最开始的时候我测试了，但是忘记配置k3的声明了，结果给我报了好多错误，让我以为sinkgroups的sink不能共享，</p>
<p></p>
<p class="p0"><img src="https://img-blog.csdn.net/20140814144046171?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvV2VpSm9uYXRoYW4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br></p>
<p class="p0"><img src="https://img-blog.csdn.net/20140814144303212?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvV2VpSm9uYXRoYW4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br></p>
<p class="p0">然后我就跑去社区发邮件问，是不是sinkgroups的sink不能共享，结果有个哥们跟我说是。现在知道我和那哥们都错了。哈哈</p>
<p class="p0">社区邮件地址：http://mail-archives.apache.org/mod_mbox/flume-user/201408.mbox/browser</p>
<p class="p0">其实上面我还少配置了一个东西，大伙可以看上面的配置文件，K1做了容错，但是K3没有。所以这里需要添加以下K3的容错节点K4。</p>
<p class="p0">这点是@晨色星空跟我说的，我一下明白了，哈；</p>
<p class="p0"></p>
<pre><code class="language-html">#Describe the sinkgroups
a1.sinkgroups = g1 g2 g3
a1.sinkgroups.g1.sinks = k1 k2
a1.sinkgroups.g1.processor.type = failover
a1.sinkgroups.g1.processor.priority.k1 = 10
a1.sinkgroups.g1.processor.priority.k2 = 5
a1.sinkgroups.g1.processor.maxpenalty = 10000

a1.sinkgroups.g2.sinks = k1 k3
a1.sinkgroups.g2.processor.type = load_balance
a1.sinkgroups.g2.processor.backoff = true
a1.sinkgroups.g2.processor.selector = round_robin

a1.sinkgroups.g3.sinks = k3 k4
a1.sinkgroups.g3.processor.type = failover
a1.sinkgroups.g3.processor.priority.k3 = 10
a1.sinkgroups.g3.processor.priority.k4 = 5
a1.sinkgroups.g3.processor.maxpenalty = 10000</code></pre>
<p></p>
<p class="p0">那我们把g3和k4加上，然后发送数据看怎么样；</p>
<p class="p0"><img src="https://img-blog.csdn.net/20140814151512139?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvV2VpSm9uYXRoYW4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br></p>
<p class="p0">好把。竟然4个都接收到了。</p>
接下来我们把K1断掉，看什么结果；
<p class="p0"><img src="https://img-blog.csdn.net/20140814150209907?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvV2VpSm9uYXRoYW4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br></p>
<p class="p0">结果这里K2,K3,K4都接收到了数据，真的有点诡异；K4是做K3的容错的不应该接收到数据才对。K2接收到数据倒是在理；</p>
<p class="p0">我们再把K1启起来。然后我们来断掉K3看看。</p>
<p class="p0"><img src="https://img-blog.csdn.net/20140814145342875?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvV2VpSm9uYXRoYW4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br></p>
<p class="p0">结果和上面的情况类似；我只能说测试结果有点奇怪；哈哈</p>
<p class="p0"><br></p>
<p class="p0">场景2：---想法（failover和load balance分开不同sink）</p>
<p class="p0">配置文件（flume-sink.properties）：</p>
<p class="p0"></p>
<pre><code class="language-plain">#Name the compents on this agent
a1.sources = r1
a1.sinks = k1 k2 k3 k4
a1.channels = c1

#Describe the sinkgroups
a1.sinkgroups = g1 g2
a1.sinkgroups.g1.sinks = k1 k2
a1.sinkgroups.g1.processor.type = failover
a1.sinkgroups.g1.processor.priority.k1 = 10
a1.sinkgroups.g1.processor.priority.k2 = 5
a1.sinkgroups.g1.processor.maxpenalty = 10000

a1.sinkgroups.g2.sinks = k3 k4
a1.sinkgroups.g2.processor.type = load_balance
a1.sinkgroups.g2.processor.backoff = true
a1.sinkgroups.g2.processor.selector = round_robin

#Describe/config the source
a1.sources.r1.type = syslogtcp
a1.sources.r1.port = 5140
a1.sources.r1.host = localhost
a1.sources.r1.channels = c1

#Describe the sink
a1.sinks.k1.type = avro
a1.sinks.k1.channel = c1
a1.sinks.k1.hostname = 192.168.220.159
a1.sinks.k1.port = 44411

a1.sinks.k2.type = avro
a1.sinks.k2.channel = c1
a1.sinks.k2.hostname = 192.168.220.159
a1.sinks.k2.port = 44422

a1.sinks.k3.type = avro
a1.sinks.k3.channel = c1
a1.sinks.k3.hostname = 192.168.220.159
a1.sinks.k3.port = 44433

a1.sinks.k4.type = avro
a1.sinks.k4.channel = c1
a1.sinks.k4.hostname = 192.168.220.159
a1.sinks.k4.port = 44444
#Use a channel which butters events in memory
a1.channels.c1.type = memory
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 100</code></pre>测试结果：
<p></p>
<p class="p0"><img src="https://img-blog.csdn.net/20140814145342875?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvV2VpSm9uYXRoYW4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""></p>
<p class="p0">这里可以看到K1，K3，K4是可以接收到数据的，这个还是正常的。这里相当于K2做的容错，K1，K3，K4做的负载均衡分担下发的数据。</p>
<p class="p0">然后接下来我们把K1断开，再测试看看是什么情况。</p>
<p class="p0">这里主agent会报错，原因是因为我们把K1断开了。</p>
<p class="p0"><img src="https://img-blog.csdn.net/20140814150051205?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvV2VpSm9uYXRoYW4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br></p>
<p class="p0">我们再来看接收情况</p>
<p class="p0"><img src="https://img-blog.csdn.net/20140814150209907?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvV2VpSm9uYXRoYW4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br></p>
<p class="p0">可以看到，这里K2，K3，K4做了负载。K1挂掉之后，K2和K3，K4一起分担数据。</p>
<p class="p0"><img src="https://img-blog.csdn.net/20140814151926404?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvV2VpSm9uYXRoYW4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br></p>
<p class="p0">测试结果正常。K1，K4可以接收到数据，K3断开了之后，K1，K4做了负载</p>
<p class="p0">场景3：---想法（单独使用failover配置，多个同级别高级的sink，一个低级的sink）</p>
<p class="p0">配置文件（flume-sink.properties）：</p>
<p class="p0"></p>
<pre><code class="language-plain">#Name the compents on this agent
a1.sources = r1
a1.sinks = k1 k2 k3 k4
a1.channels = c1

#Describe the sinkgroups
a1.sinkgroups = g1 g2
a1.sinkgroups.g1.sinks = k1 k2 k3 k4
a1.sinkgroups.g1.processor.type = failover
a1.sinkgroups.g1.processor.priority.k1 = 10
a1.sinkgroups.g1.processor.priority.k3 = 10
a1.sinkgroups.g1.processor.priority.k4 = 10
a1.sinkgroups.g1.processor.priority.k2 = 5
a1.sinkgroups.g1.processor.maxpenalty = 10000

#Describe/config the source
a1.sources.r1.type = syslogtcp
a1.sources.r1.port = 5140
a1.sources.r1.host = localhost
a1.sources.r1.channels = c1

#Describe the sink
a1.sinks.k1.type = avro
a1.sinks.k1.channel = c1
a1.sinks.k1.hostname = 192.168.220.159
a1.sinks.k1.port = 44411

a1.sinks.k2.type = avro
a1.sinks.k2.channel = c1
a1.sinks.k2.hostname = 192.168.220.159
a1.sinks.k2.port = 44422

a1.sinks.k3.type = avro
a1.sinks.k3.channel = c1
a1.sinks.k3.hostname = 192.168.220.159
a1.sinks.k3.port = 44433

a1.sinks.k4.type = avro
a1.sinks.k4.channel = c1
a1.sinks.k4.hostname = 192.168.220.159
a1.sinks.k4.port = 44444
#Use a channel which butters events in memory
a1.channels.c1.type = memory
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 100</code></pre>我们来发数据看看。<br><img src="https://img-blog.csdn.net/20140814152306053?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvV2VpSm9uYXRoYW4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><p></p>
<p class="p0">这里我们可以看到K3接收到了数据，但是我不管发多少数据都被K3接收了。原本的想法是想多个同等级的sink是否会做负载，结果测试失败，不行；</p>
<p class="p0">那么接下来我们把K3断掉看怎么样；<br><img src="https://img-blog.csdn.net/20140814152648462?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvV2VpSm9uYXRoYW4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""></p>
<p class="p0">K3断掉了结果K2接收到了数据，K2的级别是最低的；如果这样的话我们来把K2断掉看看。</p>
<p class="p0">结果K1，K4都接收不到数据了。我们来看下日志</p>
<p class="p0"></p>
<pre><code class="language-html">org.apache.flume.EventDeliveryException: All sinks failed to process, nothing left to failover to
        at org.apache.flume.sink.FailoverSinkProcessor.process(FailoverSinkProcessor.java:191)
        at org.apache.flume.SinkRunner$PollingRunner.run(SinkRunner.java:147)
        at java.lang.Thread.run(Thread.java:745)
2014-07-08 12:12:26,574 (SinkRunner-PollingRunner-FailoverSinkProcessor) [INFO - org.apache.flume.sink.AbstractRpcSink.createConnection(AbstractRpcSink.java:206)] Rpc sink k2: Building RpcClient with hostname: 192.168.220.159, port: 44422
2014-07-08 12:12:26,575 (SinkRunner-PollingRunner-FailoverSinkProcessor) [INFO - org.apache.flume.sink.AvroSink.initializeRpcClient(AvroSink.java:126)] Attempting to create Avro Rpc client.
2014-07-08 12:12:26,577 (SinkRunner-PollingRunner-FailoverSinkProcessor) [WARN - org.apache.flume.api.NettyAvroRpcClient.configure(NettyAvroRpcClient.java:620)] Using default maxIOWorkers
2014-07-08 12:12:26,595 (SinkRunner-PollingRunner-FailoverSinkProcessor) [ERROR - org.apache.flume.SinkRunner$PollingRunner.run(SinkRunner.java:160)] Unable to deliver event. Exception follows.</code></pre>这里写了，All sinks failed to process。从上面的测试可以表明，在配置failover的时候，sink的级别不能配置相同；如果配置多个相同级别的sink，只有一个生效；
<p></p>
<p class="p0">我发了一封邮件到社区，邮件地址是：http://mail-archives.apache.org/mod_mbox/flume-user/201408.mbox/browser，大家可以去看看。</p>
<p class="p0"></p>
<pre><code class="language-html">Each sink needs to have a different priority. If multiple sinks have 
same priority, only one of them will be used.</code></pre>这是一个叫cloudera的外国哥们回答的。意思是说我配置多个sinks的时候，sink等级必须不同；
<p></p>
<p class="p0">测试4：--想法：测试下上面那哥们说的，哈哈</p>
<p class="p0">配置文件（flume-sink.properties）<br></p>
<pre><code class="language-plain">#Name the compents on this agent
a1.sources = r1
a1.sinks = k1 k2 k3 k4
a1.channels = c1

#Describe the sinkgroups
a1.sinkgroups = g1 g2
a1.sinkgroups.g1.sinks = k1 k2 k3 k4
a1.sinkgroups.g1.processor.type = failover
a1.sinkgroups.g1.processor.priority.k1 = 10
a1.sinkgroups.g1.processor.priority.k3 = 9
a1.sinkgroups.g1.processor.priority.k4 = 9
a1.sinkgroups.g1.processor.priority.k2 = 5
a1.sinkgroups.g1.processor.maxpenalty = 10000

#Describe/config the source
a1.sources.r1.type = syslogtcp
a1.sources.r1.port = 5140
a1.sources.r1.host = localhost
a1.sources.r1.channels = c1

#Describe the sink
a1.sinks.k1.type = avro
a1.sinks.k1.channel = c1
a1.sinks.k1.hostname = 192.168.220.159
a1.sinks.k1.port = 44411

a1.sinks.k2.type = avro
a1.sinks.k2.channel = c1
a1.sinks.k2.hostname = 192.168.220.159
a1.sinks.k2.port = 44422

a1.sinks.k3.type = avro
a1.sinks.k3.channel = c1
a1.sinks.k3.hostname = 192.168.220.159
a1.sinks.k3.port = 44433

a1.sinks.k4.type = avro
a1.sinks.k4.channel = c1
a1.sinks.k4.hostname = 192.168.220.159
a1.sinks.k4.port = 44444
#Use a channel which butters events in memory
a1.channels.c1.type = memory
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 100</code></pre>第四个场景就留给大家去测试吧。测试结果正常。
<p></p>
<p class="p0">消息由高级别的sink接收。依次关闭，其次等级接收；<br></p>
<p class="p0"><br></p>
<p class="p0">转载请写明源地址：</p>
<p class="p0">http://blog.csdn.net/weijonathan/article/details/38557959</p>
<p class="p0">http://www.51studyit.com/html/notes/20140814/1055.html<br></p>
<p class="p0"><br></p>
            </div>
                </div>