---
layout:     post
title:      使用Apache  Flume抓取数据（1）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p align="center"><span style="font-size:18px;"><br></span></p>
<p><span style="font-size:18px;">使用Apache Flume抓取数据，怎么来抓取呢？不过，在了解这个问题之前，我们必须明确ApacheFlume是什么？</span></p>
<p><strong><span style="font-size:18px;">一、什么是Apache Flume</span></strong></p>
<p><span style="font-size:18px;">Apache Flume是用于数据采集的高性能系统 ，名字来源于原始的近乎实时的日志数据采集工具，现在广泛用于任何流事件数据的采集，支持从很多数据源聚合数据到HDFS。</span></p>
<p><span style="font-size:18px;">最初由Cloudera开发 ，在2011年贡献给了Apache基金会 ，在2012年变成了Apache的顶级项目，Flume OG升级换代成了Flume NG。</span></p>
<p><span style="font-size:18px;">Flume具有横向扩展、延展性、可靠性的优势</span></p>
<p><strong><span style="font-size:18px;">二、Flume 体系结构</span></strong></p>
<p><span style="font-size:18px;">Source：接受外部系统生成event</span></p>
<p><span style="font-size:18px;">Sink：发送event到指定的目的地 </span></p>
<p><span style="font-size:18px;">Channel：从Source缓存event，直到Sink把event取走 </span></p>
<p><span style="font-size:18px;">Agent：一个独立的Flume进程，包含了source，channel和sink组件 </span>
</p>
<p><strong><span style="font-size:18px;">三、Flume设计目标：可靠性</span></strong></p>
<p><span style="font-size:18px;">Channels提供了Flume可靠性保障 ,那么它通过什么样的方式来保障呢？默认的模式就是Memory Channel，Memory Channel就是内存，所有的数据存放在内存当中。那么，这里就会存在一个问题？如果Channel的节点出现断电，数据就会丢失。为解决这一问题，这里有另外一种模式，就是基于磁盘的Channel，基于磁盘的队列确保出现断电时数据不丢失 。</span></p>
<p><span style="font-size:18px;">另外，Agent和Channel之间的数据传输是事务性的 ，传输给下游agent失败的数据会回滚和重试 。相同的任务可以配置多个Agent，</span></p>
<p><span style="font-size:18px;">比如，两个agent完成一个数据采集作业，如果一个agent失败，则上游的agent会失败切换到另一个。
</span></p>
<p><strong><span style="font-size:18px;">四、Flume设计目标：扩展性</span></strong></p>
<p><span style="font-size:18px;">当我们采集的数据特别多的时候，可以通过添加更多的系统资源从而线性地增加系统性能。而且Flume可横向的扩展规模 ，随着复杂增加，可以添加更多的机器到配置当中 。</span></p>
<p><strong><span style="font-size:18px;">五、Flume设计目标：延展性</span></strong></p>
<p><span style="font-size:18px;">延展性就是能够添加新的功能到系统中。Flume通过添加Sources和Sinks到现有的存储层或数据平台，常见的Sources包括files、syslog和任何linux进程的标准输出的数据；常用Sinks包括本地文件系统或HDFS，开发员可以写自己的Sources或Sinks。</span></p>
<p><strong><span style="font-size:18px;">六、常见的Flume数据源</span></strong></p>
<p><strong><span style="font-size:18px;"><img src="https://img-blog.csdn.net/20170118111855310?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvamlzaHV4aWFvemk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></strong></p>
<p></p>
<p><strong><span style="font-size:18px;">七、大规模部署实例</span></strong></p>
<p><span style="font-size:18px;">Flume使用agents收集数据 ，Agents可以从很多源接收数据，包括其他agents。大规模的部署使用多层来实现扩展性和可靠，Flume支持传输中数据的检查和修改。</span></p>
<p><span style="font-size:18px;"><img src="https://img-blog.csdn.net/20170118111913264?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvamlzaHV4aWFvemk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></p>
<p></p>
<p><span style="font-size:18px;">以上就是关于Apache Flume的部分详情介绍，后续将会继续分享。大数据将会是未来的风口，要想很好的站在风口上，就要持续不断地学习和努力，这里推荐大家关注一个微信公众号“大数据cn ”,里面有很多关于大数据知识的介绍，对于想要了解和学习大数据的人是一个很好的平台。</span></p>
<p><span style="font-size:18px;"> <strong>友情推荐：</strong><a href="http://mp.weixin.qq.com/s/JAa659xXHvRlytIjplgehA" rel="nofollow">解密数据科学</a></span></p>
<p><span style="font-size:18px;"> </span></p>
            </div>
                </div>