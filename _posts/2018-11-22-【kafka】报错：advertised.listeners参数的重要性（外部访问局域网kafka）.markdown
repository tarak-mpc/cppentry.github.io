---
layout:     post
title:      【kafka】报错：advertised.listeners参数的重要性（外部访问局域网kafka）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h2>嗯，菜鸡一个，写个帖子记录下解决的一个kafka的问题。</h2>

<p>关于kafka的另外两篇文章！</p>

<p>【kafka】一、kafka框架介绍（生产者篇）：<a href="https://blog.csdn.net/lsr40/article/details/84029034" rel="nofollow">https://blog.csdn.net/lsr40/article/details/84029034</a></p>

<p>【kafka】二、kafka框架介绍（消费者篇）：<a href="https://blog.csdn.net/lsr40/article/details/84034394" rel="nofollow">https://blog.csdn.net/lsr40/article/details/84034394</a></p>

<h2>背景：</h2>

<p>看图吧！</p>

<p><img alt="" class="has" height="525" src="https://img-blog.csdnimg.cn/20181116105449529.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xzcjQw,size_16,color_FFFFFF,t_70" width="1047"></p>

<p>文字说明：</p>

<p>消费者通过内网ip与kafka集群交互，但是因为服务器101是外网环境，是不可能通过内网ip跟kafka交互的，所以必须做端口映射。</p>

<p>也就是说把：</p>

<p><span style="color:#7c79e5;">局域网ip-1:局域网端口-1，局域网ip-2:局域网端口-2，局域网ip-3:局域网端口-3</span></p>

<p>映射成</p>

<p><span style="color:#3399ea;">外部可以访问的3个ip+3个端口</span></p>

<p>接着生产者连接kafka的broker-list参数就写上：<span style="color:#3399ea;">外部可以访问的3个ip+3个端口（用逗号分隔）</span>就可以了。</p>

<h2>正文：</h2>

<p>但是我确认了在网络和各种环境都没有问题的情况下，就是发送不了数据。</p>

<p>具体情况是：<strong><span style="color:#f33b45;">我能够拿得到broker上有几个topic，但是我却不能把数据发送出去！</span></strong></p>

<p>因此我确认了，应该是kafka集群搭建的问题！经过一番挣扎，解决了这个问题！</p>

<p>如图（这图是CM平台上kafka的配置）：</p>

<p>在每一个kafka的broker上添加上这两条配置，第一台就外网ip1:外网端口1，第二台就外网ip2:外网端口2，以此类推</p>

<p><img alt="" class="has" height="156" src="https://img-blog.csdnimg.cn/20181116110221920.png" width="753"></p>

<p><img alt="" class="has" height="156" src="https://img-blog.csdnimg.cn/20181116110231375.png" width="707"></p>

<p>配置好了，重启kafka服务就可以了！</p>

<p>具体原因可以参考：</p>

<p>kafka的server.properties</p>

<p><img alt="" class="has" height="469" src="https://img-blog.csdnimg.cn/2018111611050721.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xzcjQw,size_16,color_FFFFFF,t_70" width="916"></p>

<p>官网也有对这几个参数做介绍：<a href="http://kafka.apache.org/0100/documentation.html#brokerconfigs" rel="nofollow">http://kafka.apache.org/0100/documentation.html#brokerconfigs</a></p>

<p>主要看如下参数的官方解释：</p>

<p>参数1：advertised.host.name</p>

<p>参数2：advertised.listeners</p>

<p>参数3：advertised.port</p>

<p>参数4：host.name</p>

<p>参数5：java.net.InetAddress.getCanonicalHostName().</p>

<p><strong><span style="color:#f33b45;">自己翻译的中文解释：</span></strong>不推荐直接使用参数1，请用参数2加上参数3的方式来配置，这三个参数的作用就是用client连接zk的时候能够获得主机名端口自等信息，如果在IaaS的环境下（<strong>IaaS 是云服务的最底层，相当于只提供了基础服务器</strong>），可能要为broker配置不同的接口绑定（以免端口冲突等问题），如果是这种情况，配置参数4是生效的，否则，kafka会自己通过调用java的方法来获取主机名！</p>

<p><strong><span style="color:#f33b45;">英文原文：</span></strong>DEPRECATED: only used when `advertised.listeners` or `listeners` are not set. Use `advertised.listeners` instead. Hostname to publish to ZooKeeper for clients to use. In IaaS environments, this may need to be different from the interface to which the broker binds. If this is not set, it will use the value for `host.name` if configured. Otherwise it will use the value returned from java.net.InetAddress.getCanonicalHostName().</p>

<p>关于IaaS和其他两个（PaaS，SaaS）的介绍：<a href="http://www.ruanyifeng.com/blog/2017/07/iaas-paas-saas.html" rel="nofollow">http://www.ruanyifeng.com/blog/2017/07/iaas-paas-saas.html</a></p>

<p><strong><span style="color:#f33b45;">报错的原因（大概是这样）：</span></strong>连接上了broker，找到了zookeeper上对应的kafka的topic信息，接下来要往topic上发送数据的时候，zk上注册的topic对应的信息是局域网的机器名称和ip，因为从集群外自然访问不了局域网，所以导致了这个错误，在此记录下。</p>

<h2>发这篇文章的时候还是命途多舛，两度被删，原因不可描述~</h2>

<h2>如果有什么说错的地方还请大家批评指出！</h2>            </div>
                </div>