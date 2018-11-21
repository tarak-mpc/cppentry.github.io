---
layout:     post
title:      一、MapReduce已死，Spark称霸
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div class="brief bgF8F8F8" style="width:597px;border-top-color:rgb(216,217,217);border-right-color:rgb(216,217,217);border-left-color:rgb(216,217,217);border-top-width:1px;border-right-width:1px;border-left-width:1px;border-style:solid solid none;color:rgb(51,51,51);font-family:'宋体';line-height:18px;background:rgb(248,248,248);">
<h1 style="color:rgb(0,0,0);font-size:22px;line-height:42px;text-align:center;border-bottom-color:rgb(216,217,217);border-bottom-width:1px;border-bottom-style:solid;background-color:transparent;">
一、MapReduce已死，Spark称霸</h1>
<div class="msg" style="color:rgb(105,105,105);line-height:36px;text-align:center;">
<div>2014-09-17 11:20 王家林 Spark亚太研究院 字号：<span class="f14-b" style="font-family:arial;font-weight:bold;font-size:14px;"><a style="color:rgb(0,66,118);">T</a></span> | <span class="f16-b" style="font-family:arial;font-weight:bold;font-size:16px;"><a style="color:rgb(0,66,118);">T</a></span></div>
<div class="artHd" id="books" style="line-height:30px;border-top-width:1px;border-top-style:dashed;border-top-color:rgb(204,204,204);color:rgb(50,91,10);">
<p class="artHd_l" style="color:rgb(51,51,51);background-color:transparent;">
<span class="item1" style="width:62px;color:rgb(0,66,118);">综合评级：</span><span class="xx5 item2" style="width:70px;overflow:hidden;display:inline;"></span></p>
<p class="artHd_r" style="color:rgb(51,51,51);background-color:transparent;">
<a href="http://home.51cto.com/apps/book/index.php?s=/Index/book/bid/4336/status/0" rel="nofollow" style="color:rgb(0,66,118);">想读(35)</a>  <a href="http://home.51cto.com/apps/book/index.php?s=/Index/book/bid/4336/status/1" rel="nofollow" style="color:rgb(0,66,118);">在读(13)</a>  <a href="http://home.51cto.com/apps/book/index.php?s=/Index/book/bid/4336/status/2" rel="nofollow" style="color:rgb(0,66,118);">已读(2)</a>   <a href="http://home.51cto.com/apps/book/index.php?s=/Index/book/bid/4336/good/1" rel="nofollow" style="color:rgb(0,66,118);">品书斋鉴(0)</a>   <a href="http://home.51cto.com/apps/book/index.php?s=/Index/book/bid/4336" rel="nofollow" style="color:rgb(0,66,118);">已有50人发表书评</a></p>
</div>
<div class="fav" style="width:49px;">
<a title="一键收藏，随时查看，分享好友！" style="color:rgb(0,66,118);"><img src="http://images.51cto.com/images/art/newart1012/images/Fav.gif" alt="一键收藏，随时查看，分享好友！" border="0" style="border:medium none;"></a></div>
</div>
<div class="brieftext" style="border:1px dashed rgb(115,160,207);line-height:26px;background:rgb(255,255,255);">
<p class="f14 green" style="color:rgb(0,102,0);font-size:14px;background-color:transparent;">
《Spark亚太研究院系列丛书——Spark实战高手之路 从零开始》本书通过Spark的shell测试Spark的工作；使用Spark的cache机制观察一下效率的提升构建Spark的IDE开发环境；通过Spark的IDE搭建Spark开发环境；测试Spark IDE开发环境等等。本节为大家介绍MapReduce已死，Spark称霸。</p>
<p class="ad" style="text-align:right;background-color:transparent;">
AD：<a href="http://mobile.51cto.com/mobile/mdsa12/" rel="nofollow" style="color:#FF0000;text-decoration:none;">51CTO 网+ 第十二期沙龙：大话数据之美_如何用数据驱动用户体验</a></p>
</div>
</div>
<div class="content bgF8F8F8 f14" style="font-size:14px;border-right-color:rgb(216,217,217);border-bottom-color:rgb(216,217,217);border-left-color:rgb(216,217,217);border-right-width:1px;border-bottom-width:1px;border-left-width:1px;border-style:none solid solid;line-height:28px;min-height:500px;overflow:hidden;color:rgb(51,51,51);font-family:'宋体';background:rgb(248,248,248);">
<div id="content">
<p style="text-indent:28px;background-color:transparent;">
</p>
<p style="text-indent:28px;background-color:transparent;">
<span style="text-align:center;">一、MapReduce已死，Spark称霸</span></p>
<p style="text-indent:28px;background-color:transparent;">
由于Hadoop的MapReduce高延迟的死穴，导致Hadoop无力处理很多对时间有要求的场景，人们对其批评越来越多，Hadoop无力改变现在而导致正在死亡。正如任何领域一样，死亡是一个过程，Hadoop正在示例这样的一个过程，Hadoop的死亡过程在2012年已经开始</p>
<p style="text-indent:28px;background-color:transparent;">
1，原先支持Hadoop的四大商业机构纷纷宣布支持Spark；</p>
<p style="text-indent:28px;background-color:transparent;">
2，Mahout前一阶段表示从现在起他们将不再接受任何形式的以MapReduce形式实现的算法，另外一方面，Mahout宣布新的算法基于Spark；</p>
<p style="text-indent:28px;background-color:transparent;">
3，Cloudera的机器学习框架Oryx的执行引擎也将由Hadoop的MapReduce替换成Spark；</p>
<p style="text-indent:28px;background-color:transparent;">
4，Google已经开始将负载从MapReduce转移到Pregel和Dremel上；</p>
<p style="text-indent:28px;background-color:transparent;">
5，FaceBook则将负载转移到Presto上；</p>
<p style="text-indent:28px;background-color:transparent;">
现在很多原来使用深度使用Hadoop的公司都在纷纷转向Spark，国内的淘宝是典型的案例。在此，我们以使用世界上使用Hadoop最典型的公司Yahoo！为例，大家可以看一下其数据处理的架构图：<br style="clear:both;width:0px;">
 </p>
<p style="text-indent:28px;background-color:transparent;">
<a href="http://s7.51cto.com/wyfs02/M02/49/B0/wKioL1QY_nDjyvRPAABo-11YXBU341.jpg" rel="nofollow" style="color:rgb(0,66,118);"><img class="fit-image" border="0" alt="" src="http://s7.51cto.com/wyfs02/M02/49/B0/wKioL1QY_nDjyvRPAABo-11YXBU341.jpg" width="498" height="304" style="border:0px;text-align:center;"></a></p>
<p style="text-indent:28px;background-color:transparent;">
而使用Spark后的架构如下：<br style="clear:both;width:0px;">
 </p>
<p style="text-indent:28px;background-color:transparent;">
<a href="http://s2.51cto.com/wyfs02/M00/49/B0/wKioL1QY_nqRX93FAABzYVZmAvw916.jpg" rel="nofollow" style="color:rgb(0,66,118);"><img class="fit-image" border="0" alt="" src="http://s2.51cto.com/wyfs02/M00/49/B0/wKioL1QY_nqRX93FAABzYVZmAvw916.jpg" width="498" height="289" style="border:0px;text-align:center;"></a></p>
<p style="text-indent:28px;background-color:transparent;">
大家可以看出，现阶段的Yahoo！是使用Hadoop和Spark并存的架构，而随着时间的推进和Spark本身流处理、图技术、机器学习、NoSQL查询的出色特性，最终Yahoo！可能会完成Spark全面取代Hadoop，而这也代表了所有做云计算大数据公司的趋势。</p>
<p style="text-indent:28px;background-color:transparent;">
或许有朋友会问，Hadoop为何不改进自己？</p>
<p style="text-indent:28px;background-color:transparent;">
其实，Hadoop社区一直在改进Hadoop本身，但事实是无力回天：</p>
<p style="text-indent:28px;background-color:transparent;">
1，Hadoop的改进基本停留在代码层次，也就是修修补补的事情，这就导致了Hadoop现在具有深度的“技术债务”，负载累累；</p>
<p style="text-indent:28px;background-color:transparent;">
2，Hadoop本身的计算模型决定了Hadoop上的所有工作都要转化成Map、Shuffle和Reduce等核心阶段，由于每次计算都要从磁盘读或者写数据，同时真个计算模型需要网络传输，这就导致了越来越不能忍受的延迟性，同时在前一个任务运行完之前，任何一个任务都不可以运行，这直接导致了其无力支持交互式应用；</p>
<p style="text-indent:28px;background-color:transparent;">
那么，为什么不全部重新写一个更好的Hadoop呢?答案是Spark的出现使得没有必要这样做了。</p>
<p style="text-indent:28px;background-color:transparent;">
Spark是继Hadoop之后，成为替代Hadoop的下一代云计算大数据核心技术，目前SPARK已经构建了自己的整个大数据处理生态系统，如流处理、图技术、机器学习、NoSQL查询等方面都有自己的技术，并且是Apache顶级Project，可以预计的是2014年下半年到2015年在社区和商业应用上会有爆发式的增长。</p>
<p style="text-indent:28px;background-color:transparent;">
国外一些大型互联网公司已经部署了Spark。甚至连Hadoop的早期主要贡献者Yahoo现在也在多个项目中部署使用Spark；国内的淘宝、优酷土豆、网易、Baidu、腾讯等已经使用Spark技术用于自己的商业生产系统中，国内外的应用开始越来越广泛。Spark正在逐渐走向成熟，并在这个领域扮演更加重要的角色。<br style="clear:both;width:0px;">
 </p>
<p style="text-indent:28px;background-color:transparent;">
<span style="text-align:center;">喜欢的朋友可以添加我们的微信账号：</span></p>
<p style="text-indent:28px;background-color:transparent;">
<span style="text-align:center;">51CTO读书频道二维码</span></p>
<p style="text-indent:28px;background-color:transparent;">
<a href="http://s5.51cto.com/wyfs02/M02/49/B0/wKioL1QY_pTyt0b3AAAvkB-v6SM900.jpg" rel="nofollow" style="color:rgb(0,66,118);"><img class="fit-image" border="0" alt="" src="http://s5.51cto.com/wyfs02/M02/49/B0/wKioL1QY_pTyt0b3AAAvkB-v6SM900.jpg" width="173" height="173" style="border:0px;text-align:center;"></a></p>
<p style="text-indent:28px;background-color:transparent;">
<span style="text-align:center;"><br style="clear:both;width:0px;">
51CTO读书频道活动讨论群：342347198</span></p>
</div>
</div>
            </div>
                </div>