---
layout:     post
title:      Flume 流入远程 hbase
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/zhanghui_hn/article/details/38372179				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p><span style="font-size:14px;">在测试flume的过程中，一直是将hbase和flume在同一台机器上做测试，由于flume默认是插入本地的hbase中，所以一直没有问题。</span></p><p><span style="font-size:14px;">当真正部署到实际环境中是发现f需要再做设置才能流入远程hbase数据库中，<span style="color:#ff0000;">flume端要做配置，hbase主机中的hosts文件也做修改</span></span></p><p><span style="font-size:14px;">在此贴出配置文件</span></p><h2><span style="font-size:14px;">flume的conf文件</span></h2><p><span style="font-size:14px;"></span></p><pre><code class="language-html">#Name the  components on this agent
a1.sources  = r1
a1.sinks =  k1
a1.channels  = c1

#  Describe/configure the source
a1.sources.r1.type  = spooldir
a1.sources.r1.spoolDir  = /home/scut/Downloads/testFlume

# Describe  the sink
a1.sinks.k1.type  = org.apache.flume.sink.hbase.AsyncHBaseSink
a1.sinks.k1.zookeeperQuorum = master,slave1,slave2
a1.sinks.k1.table = Router
a1.sinks.k1.columnFamily = log 
a1.sinks.k1.serializer.payloadColumn=serviceTime,browerOS,clientTime,screenHeight,screenWidth,url,userAgent,mobileDevice,gwId,mac
a1.sinks.k1.serializer = org.apache.flume.sink.hbase.BaimiAsyncHbaseEventSerializer

# Use a  channel which buffers events in memory
a1.channels.c1.type  = memory
a1.channels.c1.capacity  = 1000
a1.channels.c1.transactionCapacity  = 100

# Bind the  source and sink to the channel
a1.sources.r1.channels  = c1
a1.sinks.k1.channel  = c1
</code></pre><br><p></p><h2><span style="font-size:14px;">hbase master的 /etc/hosts文件</span></h2><p></p><p><span style="font-size:14px;"></span></p><pre><code class="language-java">192.168.100.1	master hbase localhost ubuntu #将192.168.100.1映射为master
192.168.100.2	slave1                        #将192.168.100.2映射为slave1
192.168.100.3	slave2                        #将192.168.100.3映射为slave2

# The following lines are desirable for IPv6 capable hosts
::1     ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters</code></pre><p></p><p>主要的不同是删除127.0.0.1/127.0.1.1个localhost和主机名的映射关系</p><p><span style="color:#ff0000;">第一行中，192.168.100.1映射为 master，hbase， localhost， ubuntu。ubuntu是主机名，hbase是为了让flume能够流入远程hbase，具体原理是什么，我也不清楚，只是需要建立这种映射关系。</span></p><p><br></p><h2><span style="font-size:14px;">hbase 的slave的/etc/hosts文件</span></h2><p><span style="font-size:14px;"></span></p><pre><code class="language-java">&lt;pre name="code" class="java"&gt;
192.168.100.1	master 
192.168.100.2	slave1 localhost ubuntu
192.168.100.3	slave2</code></pre># The following lines are desirable for IPv6 capable hosts::1 ip6-localhost ip6-loopbackfe00::0 ip6-localnetff00::0 ip6-mcastprefixff02::1 ip6-allnodesff02::2 ip6-allrouters<p></p><pre></pre><br>hbase中的slave节点的/etc/hosts配置相似，删除127.0.0.1/127.0.1.1和localhost于主机名之间的映射关系。<p></p><p><span style="font-size:14px;">而是将localhost/ubuntu(主机名)直接映射为本地IP地址</span></p><p><span style="font-size:14px;"><br></span></p>            </div>
                </div>