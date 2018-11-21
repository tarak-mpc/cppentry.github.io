---
layout:     post
title:      那些年踏过的Apache Flume之路
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p align="center" style="text-align:left;"><span style="font-family:SimSun;font-size:18px;">Flume作为日志采集系统，有着独特的应用和优势，那么Flume在实际的应用和实践中到底是怎样的呢？让我们一起踏上Flume之路。</span></p>
<p><span style="font-family:SimSun;font-size:18px;"><a href="https://jq.qq.com/?_wv=1027&amp;k=42koW38" rel="nofollow"><strong>1、什么是Apache Flume</strong></a></span></p>
<p><span style="font-family:SimSun;font-size:18px;">（1）Apache Flume简单来讲是高性能、分布式的日志采集系统，和sqoop同属于数据采集系统组件，但是sqoop用来采集关系型数据库数据，而Flume用来采集流动型数据。</span></p>
<p><span style="font-family:SimSun;font-size:18px;">（2）Flume名字来源于原始的近乎实时的日志数据采集工具,现在被广泛用于任何流事件数据的采集,它支持从很多数据源聚合数据到HDFS。</span></p>
<p><span style="font-family:SimSun;font-size:18px;">（3）Flume最初由Cloudera开发，在2011年贡献给了Apache基金会，2012年变成了Apache的顶级项目。Flume OG是Flume最初的版本，后升级换代成了Flume NG</span></p>
<p><span style="font-family:SimSun;font-size:18px;">（4）Flume的优势：可横向扩展、延展性、可靠性</span></p>
<p><span style="font-family:SimSun;font-size:18px;"><a href="https://jq.qq.com/?_wv=1027&amp;k=42koW38" rel="nofollow"><strong>2、Flume体系结构</strong></a></span></p>
<p><span style="font-family:SimSun;font-size:18px;">（1）核心模块Agent：一个独立的Flume进程，包含了source，channel和sink组件：</span></p>
<p><span style="font-family:SimSun;font-size:18px;">a)Source是决定数据从哪里来以及以什么样的方式来进行采集的组件，它会把接受数据转化成event，event就是一个事件，这个事件有它的事件头，事件体（数据内容）。</span></p>
<p><span style="font-family:SimSun;font-size:18px;">b)Sink就是把event发送到指定的目的地，数据是写到HDFS，Hive还是Hbase，这些参数和类型选择都要用sink来完成。</span></p>
<p><span style="font-family:SimSun;font-size:18px;">c)Channel介于Source和Sink之间，从Source缓存event，直到Sink把event取走</span></p>
<p><span style="font-family:SimSun;font-size:18px;"><a href="https://jq.qq.com/?_wv=1027&amp;k=42koW38" rel="nofollow"><strong>3、Flume设计目标</strong></a></span></p>
<p><span style="font-family:SimSun;font-size:18px;"><a href="https://jq.qq.com/?_wv=1027&amp;k=42koW38" rel="nofollow"><strong>（1） 可靠性</strong></a></span></p>
<p><span style="font-family:SimSun;font-size:18px;">a)Channels提供了Flume可靠性保障，默认Channels的保障模式为Memory Channel，MemoryChannel就是内存，将所有的数据存放在里面，但是它本身存在缺陷，如果断电数据将会丢失。<strong>那怎么解决这个问题呢？</strong> Channels还有另外一种模式，就是基于磁盘的Channels，基于磁盘的队列确保当出现断电时数据不丢失，但是在这里我们必须明确Memory的性能是比磁盘高的。</span></p>
<p><span style="font-family:SimSun;font-size:18px;">b)Agent和Channel之间的数据传输是事务性的，就是在传输数据的过程中如果出现了故障，失败的数据会回滚和重试，不会丢失。事务就是保证我们的源到目标整体是完整的，要么一起成功，要么一起失败。                           </span></p>
<p><span style="font-family:SimSun;font-size:18px;">c)相同的任务可以配置多个Agent。比如，两个agent完成一个数据采集作业，如果一个agent失败，则上游的agent会失败切换到另一个。</span></p>
<p><span style="font-family:SimSun;font-size:18px;"><a href="https://jq.qq.com/?_wv=1027&amp;k=42koW38" rel="nofollow"><strong>（2）扩展性</strong></a></span></p>
<p><span style="font-family:SimSun;font-size:18px;">当采集的数据过多的时候，Flume既可横向的扩展，也可以纵向扩展更多的agent来线性地提升系统性能。</span></p>
<p><span style="font-family:SimSun;font-size:18px;"><a href="https://jq.qq.com/?_wv=1027&amp;k=42koW38" rel="nofollow"><strong>（3）延展性</strong></a></span></p>
<p><span style="font-family:SimSun;font-size:18px;"><strong><a href="https://jq.qq.com/?_wv=1027&amp;k=42koW38" rel="nofollow">延展性就是Flume到底可以采集什么样的数据呢？</a></strong>在Flume中源是由Sources决定的，输出是由Sinks决定的，Sources和Sinks在Flume当中提供了一些非常丰富的实现。</span></p>
<p><span style="font-family:SimSun;font-size:18px;">例如：常见的Sources包括files、syslog和任何linux进程的标准输出的数据；常用Sinks包括本地文件系统或HDFS，也可以与kafka实现兼容、集成；更灵活的在于开发员可以写自己的Sources或Sinks。所以Flume既提供了内置的丰富的实现，也可以满足于针对一些特殊的数据源进行定制。</span></p>
<p><strong><span style="font-family:SimSun;font-size:18px;"><a href="https://jq.qq.com/?_wv=1027&amp;k=42koW38" rel="nofollow">4、常见的Flume数据源：</a></span></strong></p>
<p><strong><span style="font-family:SimSun;font-size:18px;"><a href="https://jq.qq.com/?_wv=1027&amp;k=42koW38" rel="nofollow"><img src="https://img-blog.csdn.net/20161222133326408?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZGFzaHVqdTIwMTY=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></a><br></span></strong></p>
<p></p>
<p><strong><span style="font-family:SimSun;font-size:18px;"><a href="https://jq.qq.com/?_wv=1027&amp;k=42koW38" rel="nofollow">5、大规模部署实例：</a></span></strong></p>
<p><span style="font-family:SimSun;font-size:18px;">（1）Flume使用agents收集数据：</span></p>
<p><span style="font-family:SimSun;font-size:18px;">a)Agents可以从很多源接收数据，包括其他agents</span></p>
<p><span style="font-family:SimSun;font-size:18px;">b)大规模的部署使用多层来实现扩展性和可靠性</span></p>
<p><span style="font-family:SimSun;font-size:18px;">c)Flume支持传输中数据的检查和修改</span></p>
<p><span style="font-family:SimSun;font-size:18px;"><a href="https://jq.qq.com/?_wv=1027&amp;k=42koW38" rel="nofollow"><img src="https://img-blog.csdn.net/20161222133344143?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZGFzaHVqdTIwMTY=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></a><br></span></p>
<p></p>
<p><span style="font-family:SimSun;font-size:18px;">对于Flume,我们从整体上进行了一个概括和认知解读，那么Sources、Sinks、Channels这些具体构件以及Flume的配置会是什么样的呢？后续我也会做相应的分享和解答，加QQ群：</span><span style="font-family:SimSun;font-size:18px;">317070397 </span></p>
<p><span style="font-family:SimSun;font-size:18px;"><br></span></p>
<p><span style="font-family:SimSun;font-size:18px;">友情推荐：<a href="https://jq.qq.com/?_wv=1027&amp;k=42koW38" rel="nofollow">《大数据基础MySQL篇》2016年12月22日晚8:30直播</a></span></p>
<p><span style="font-family:SimSun;font-size:18px;"> </span></p>
<p><strong><span style="font-family:SimSun;font-size:18px;"> </span></strong></p>
<p><span style="font-family:SimSun;font-size:18px;"> </span></p>
<p><span style="font-family:SimSun;font-size:18px;"> </span></p>
<p><span style="font-family:SimSun;font-size:18px;"> </span></p>
<p><span style="font-family:SimSun;font-size:18px;"> </span></p>
<p><span style="font-family:SimSun;font-size:18px;"> </span></p>
<p><span style="font-family:SimSun;font-size:18px;"> </span></p>
<p><span style="font-family:SimSun;font-size:18px;"> </span></p>
            </div>
                </div>