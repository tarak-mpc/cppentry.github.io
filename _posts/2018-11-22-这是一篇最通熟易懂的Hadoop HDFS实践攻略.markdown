---
layout:     post
title:      这是一篇最通熟易懂的Hadoop HDFS实践攻略
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
本文主要帮您解决一下几个问题：</p>
<ul class="list-paddingleft-2" style="margin:0px; padding:0px 0px 0px 30px; max-width:100%; word-wrap:break-word!important">
<li style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; word-wrap:break-word!important">
HDFS是用来解决什么问题？怎么解决的？</p>
</li><li style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; word-wrap:break-word!important">
如何在命令行下操作HDFS？</p>
</li><li style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; word-wrap:break-word!important">
如何使用Java API来操作HDFS？</p>
</li><li style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; word-wrap:break-word!important">
在了解基本思路和操作方法后，进一步深究HDFS具体的读写数据流程</p>
</li></ul>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
学习并实践本文教程后，可以对HDFS有比较清晰的认识，并可以进行熟练操作，为后续学习Hadoop体系打好基础。</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">目录：</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; line-height:25.6px; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important"><span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:16px">理论部分</span></span></p>
<ol class="list-paddingleft-2" style="margin:0px; padding:0px 0px 0px 30px; max-width:100%; word-wrap:break-word!important; width:438.891px; line-height:25.6px">
<li style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:16px">HDFS 基本原理</span></p>
</li><li style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:16px">文件读取、写入机制</span></p>
</li><li style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:16px">元数据管理思路</span></p>
</li></ol>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; line-height:25.6px; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important"><span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:16px">实践部分</span></span></p>
<ol class="list-paddingleft-2" style="margin:0px; padding:0px 0px 0px 30px; max-width:100%; word-wrap:break-word!important; width:438.891px; line-height:25.6px">
<li style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:16px">安装实践环境</span></p>
</li><li style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:16px">Shell 命令行操作方式</span></p>
</li><li style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:16px">Java API操作方式</span></p>
</li></ol>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; font-size:20px; line-height:20px; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:16px"><span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">一、HDFS基本原理</span></span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
HDFS（Hadoop Distribute File System）是一个分布式文件系统，是Hadoop的重要成员。</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; color:rgb(30,155,232)"><span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">1、文件系统的问题</span></span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
文件系统是操作系统提供的磁盘空间管理服务，只需要我们制定把文件放到哪儿，从哪个路径读取文件就可以了，不用关心文件在磁盘上是如何存放的。</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
当文件所需空间大于本机磁盘空间时，如何处理呢？</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
一是加磁盘，但是加到一定程度就有限制了；二是加机器，用远程共享目录的方式提供网络化的存储，这种方式可以理解为分布式文件系统的雏形，可以把不同文件放入不同的机器中，空间不足了可继续加机器，突破了存储空间的限制。但这个方式有多个问题：</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<ul class="list-paddingleft-2" style="margin:0px; padding:0px 0px 0px 30px; max-width:100%; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<li style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">单机负载可能极高</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; word-wrap:break-word!important">
例如某个文件是热门，很多用户经常读取这个文件，就使得次文件所在机器的访问压力极高。</p>
</li></ul>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<ul class="list-paddingleft-2" style="margin:0px; padding:0px 0px 0px 30px; max-width:100%; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<li style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important"><span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; line-height:1.6">数据不安全</span></span><br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; word-wrap:break-word!important">
如果某个文件所在的机器出现故障，这个文件就不能访问了，可靠性很差。</p>
</li></ul>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<ul class="list-paddingleft-2" style="margin:0px; padding:0px 0px 0px 30px; max-width:100%; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<li style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important"><span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; line-height:1.6">文件整理困难</span></span><br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; word-wrap:break-word!important">
例如想把一些文件的存储位置进行调整，就需要看目标机器的空间是否够用，并且需要自己维护文件位置，如果机器非常多，操作就极为复杂。</p>
</li></ul>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; color:rgb(30,155,232)"><span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">2、HDFS的解决思路</span></span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
HDFS是个抽象层，底层依赖很多独立的服务器，对外提供统一的文件管理功能，对于用户来讲，感觉就像在操作一台机器，感受不到HDFS下面的多台服务器。</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
例如用户访问HDFS中的/a/b/c.mpg这个文件，HDFS负责从底层相应服务器中读取，然后返回给用户，这样用户只需和HDFS打交道，不关心这个文件是怎么存储的。</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<img class=" " src="http://mmbiz.qpic.cn/mmbiz_jpg/tibrg3AoIJTuicmcP1xkgIrtfIx3tfhFl8SgQSk5LFH32KEIquPSMt2VFX4vvgX2dzKibbicaI8x0iaiaiaN5wSjBfGNw/640?wx_fmt=jpeg&amp;tp=webp&amp;wxfrom=5&amp;wx_lazy=1" alt="" style="margin:0px; padding:0px; height:auto!important; max-width:100%; word-wrap:break-word!important; width:auto!important; visibility:visible!important"></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
例如用户需要保存一个文件/a/b/xxx.avi。</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
HDFS首先会把这个文件进行分割，例如分为4块，然后分别放到不同服务器上。</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<img class=" " src="http://mmbiz.qpic.cn/mmbiz_jpg/tibrg3AoIJTuicmcP1xkgIrtfIx3tfhFl8nIB7YlVFPicJyuZnFKg3waShSUPZbqibvBNr3zHH21wiaEVykSYOia7FGg/640?wx_fmt=jpeg&amp;tp=webp&amp;wxfrom=5&amp;wx_lazy=1" alt="" style="margin:0px; padding:0px; height:auto!important; max-width:100%; word-wrap:break-word!important; width:auto!important; visibility:visible!important"></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
这样做有个好处，不怕文件太大，并且读文件的压力不会全部集中在一台服务器上。但如果某台服务器坏了，文件就读不全了。</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
HDFS为保证文件可靠性，会把每个文件块进行多个备份：</p>
<ul class="list-paddingleft-2" style="margin:0px; padding:0px 0px 0px 30px; max-width:100%; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<li style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; word-wrap:break-word!important">
块1：A B C</p>
</li><li style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; word-wrap:break-word!important">
块2：A B D</p>
</li><li style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; word-wrap:break-word!important">
块3：B C D</p>
</li><li style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; word-wrap:break-word!important">
块4：A C D</p>
</li></ul>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<img class=" " src="http://mmbiz.qpic.cn/mmbiz_jpg/tibrg3AoIJTuicmcP1xkgIrtfIx3tfhFl89KTdYeyWRSfiaAvnBtvR18iaGzcCCf94IV4F5jVzYpfRGEweKEdibLsiaQ/640?wx_fmt=jpeg&amp;tp=webp&amp;wxfrom=5&amp;wx_lazy=1" alt="" style="margin:0px; padding:0px; height:auto!important; max-width:100%; word-wrap:break-word!important; width:auto!important; visibility:visible!important"></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
这样文件的可靠性就大大增强了，即使某个服务器坏了，也可以完整读取文件。</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
同时还带来一个很大的好处，就是增加了文件的并发访问能力，比如多个用户读取这个文件时，都要读块1，HDFS可以根据服务器的繁忙程度，选择从那台服务器读块1。</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; color:rgb(30,155,232)"><span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">3、元数据的管理</span></span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; color:rgb(30,155,232)"><span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important"><br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</span></span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
HDFS中存了哪些文件？</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
文件被分成了哪些块？</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
每个块被放在哪台服务器上？</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
……</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
这些都叫做元数据，这些元数据被抽象为一个目录树，记录了这些复杂的对应关系。这些元数据由一个单独的模块进行管理，这个模块叫做NameNode。存放文件块的真实服务器叫做DataNode，所以用户访问HDFS的过程可以理解为：</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; line-height:25.6px; white-space:pre-wrap"></span><span style="margin:0px; padding:0px; max-width:100%; line-height:25.6px; font-size:14px; word-wrap:break-word!important">用户-&gt; HDFS -&gt; NameNode -&gt; DataNode</span><span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; line-height:25.6px; white-space:pre-wrap"></span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; color:rgb(30,155,232)"><span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">4、HDFS优点</span></span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; color:rgb(30,155,232)"><span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important"><br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</span></span></p>
<ul class="list-paddingleft-2" style="margin:0px; padding:0px 0px 0px 30px; max-width:100%; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<li style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; word-wrap:break-word!important">
容量可以线性扩展</p>
</li><li style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; word-wrap:break-word!important">
有副本机制，存储可靠性高，吞吐量增大</p>
</li><li style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; word-wrap:break-word!important">
有了NameNode后，用户访问文件只需指定HDFS上的路径</p>
</li></ul>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; font-size:20px; line-height:20px; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important"><span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:16px">二、HDFS实践</span></span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
经过上面介绍，可以对HDFS有个基本的了解，下面开始进行实际操作，在实践中更好的认识HDFS。</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; color:rgb(30,155,232)"><span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">1、安装实践环境</span></span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; color:rgb(30,155,232)"><span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important"><br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</span></span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
您可以选择自己搭建环境，也可以使用打包好的Hadoop环境（版本2.7.3）</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
这个Hadoop环境实际上是一个虚机镜像，所以需要安装virtualbox虚拟机、vagrant镜像管理工具，和我的Hadoop镜像，然后用这个镜像启动虚机就可以了，下面是具体操作步骤：</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
1）安装virtualbox</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; line-height:25.6px; white-space:pre-wrap; font-size:14px">下载地址：https://www.virtualbox.org/wiki/Downloads</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
2）安装vagrant</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
因为官网下载较慢，我上传到了云盘</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
Windows版</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:14px"><span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; line-height:25.6px; white-space:pre-wrap">链接: </span>https://pan.baidu.com/s/1pKKQGHl </span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:14px"><span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; line-height:25.6px; white-space:pre-wrap">密码: eykr</span></span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
Mac版</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:14px"><span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; line-height:25.6px; white-space:pre-wrap">链接: </span>https://pan.baidu.com/s/1slts9yt </span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:14px"><span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; line-height:25.6px; white-space:pre-wrap">密码: aig4</span></span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
安装完成后，在命令行终端下就可以使用vagrant命令。</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
3）下载Hadoop镜像</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:14px"><span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; line-height:25.6px; white-space:pre-wrap">链接:</span> </span><a target="_blank" style="margin:0px; padding:0px; color:rgb(0,0,0); max-width:100%; font-size:14px; word-wrap:break-word!important">https://pan.baidu.com/s/1bpaisnd </a></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:14px; line-height:25.6px; white-space:pre-wrap">密码: pn6c</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
4）启动</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
加载Hadoop镜像</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; line-height:25.6px; white-space:pre-wrap; font-size:14px">vagrant <span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">box add </span>{自定义镜像名称} {镜像所在路径}</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
例如您想命名为Hadoop，镜像下载后的路径为d:\hadoop.box，加载命令就是这样：</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; line-height:25.6px; white-space:pre-wrap; font-size:14px">vagrant <span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">box add</span> hadoop d:\hadoop<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">.box</span></span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
创建工作目录，例如d:\hdfstest。</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
进入此目录，初始化</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; line-height:25.6px; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:14px">cd d:\hdfstest</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; line-height:25.6px; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:14px">vagrant init hadoop</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
启动虚机</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; line-height:25.6px; white-space:pre-wrap; font-size:14px">vagrant up</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
启动完成后，就可以使用SSH客户端登录虚机了</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; line-height:25.6px; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:14px">IP   127.0.0.1</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; line-height:25.6px; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:14px">端口 2222</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; line-height:25.6px; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:14px">用户名 root</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; line-height:25.6px; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:14px">密码 vagrant</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
登录后使用命令ifconfig 查看本虚机的IP（如<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; letter-spacing:0px">192.168.31.239</span>），可以使用此IP和端口22登录了</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; line-height:25.6px; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:14px">IP   192.168.31.239</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; line-height:25.6px; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:14px">端口 22</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; line-height:25.6px; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:14px">用户名 root</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; line-height:25.6px; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:14px">密码 vagrant</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
Hadoop服务器环境搭建完成。</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; color:rgb(30,155,232)"><span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">2、Shell命令行操作</span></span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; color:rgb(30,155,232)"><span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important"><br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</span></span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
登录Hadoop服务器后，先启动HDFS，执行命令：</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; line-height:26px; text-indent:17px; white-space:pre-wrap; font-size:14px">start-dfs.sh</span></p>
<p style="margin-top:0px; margin-bottom:0px; margin-left:4px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; text-indent:17px; line-height:26px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<ul class="list-paddingleft-2" style="margin:0px; padding:0px 0px 0px 30px; max-width:100%; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<li style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; word-wrap:break-word!important">
查看帮助</p>
</li></ul>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; line-height:25.6px; white-space:pre-wrap; font-size:14px">hdfs dfs –help</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
显示目录信息</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
-ls 后面是要查看的目录路径</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
创建目录</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
创建目录/test</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; line-height:25.6px; white-space:pre-wrap; font-size:14px">hdfs dfs -mkdir /test</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
一次创建多级目录/aa/bb</p>
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:14px">hdfs dfs -mkdir -p /aa/bb</span>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<ul class="list-paddingleft-2" style="margin:0px; padding:0px 0px 0px 30px; max-width:100%; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<li style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; word-wrap:break-word!important">
上传文件</p>
</li></ul>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
形式</p>
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:14px">hdfs dfs -put {本地路径} {hdfs中的路径}</span>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
实例（先创建好一个测试文件<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; letter-spacing:0px">mytest.txt，内容随意，然后上传到/test</span>）</p>
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:14px">hadoop fs    -put       ~/mytest.txt /test</span>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<ul class="list-paddingleft-2" style="margin:0px; padding:0px 0px 0px 30px; max-width:100%; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<li style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; word-wrap:break-word!important">
显示文件内容</p>
</li></ul>
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:14px">hdfs dfs -cat /test/mytest.txt</span>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<ul class="list-paddingleft-2" style="margin:0px; padding:0px 0px 0px 30px; max-width:100%; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<li style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; word-wrap:break-word!important">
下载文件</p>
</li></ul>
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:14px">hdfs dfs -get /test/mytest.txt ./mytest2.txt</span>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<ul class="list-paddingleft-2" style="margin:0px; padding:0px 0px 0px 30px; max-width:100%; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<li style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; word-wrap:break-word!important">
合并下载</p>
</li></ul>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
先创建2个测试文件（log.access, log.error），内容随意，使用-put上传到<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; letter-spacing:0px">/test目录下</span></p>
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:14px">hdfs dfs -put log.* /test</span>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
然后把2个log文件合并下载到一个文件中</p>
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:14px">hdfs dfs -getmerge /test/log.* ./log</span>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
查看本地log文件内容，应该包含<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; letter-spacing:0px">log.access</span>与<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; letter-spacing:0px">log.error两个文件的内容。</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; letter-spacing:0px"><br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</span></p>
<ul class="list-paddingleft-2" style="margin:0px; padding:0px 0px 0px 30px; max-width:100%; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<li style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; letter-spacing:0px">复制</span></p>
</li></ul>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
从HDFS的一个路径拷贝HDFS的另一个路径</p>
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:14px">hdfs dfs -cp /test/mytest.txt /aa/mytest.txt.2</span>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
验证</p>
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:14px">hdfs dfs -ls /aa</span>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<ul class="list-paddingleft-2" style="margin:0px; padding:0px 0px 0px 30px; max-width:100%; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<li style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; word-wrap:break-word!important">
移动文件</p>
</li></ul>
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:14px">hdfs dfs -mv /aa/mytest.txt.2 /aa/bb</span>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
验证</p>
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:14px">hdfs dfs -ls /aa/bb</span>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
应列出mytest.txt.2。</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<ul class="list-paddingleft-2" style="margin:0px; padding:0px 0px 0px 30px; max-width:100%; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<li style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; word-wrap:break-word!important">
删除</p>
</li></ul>
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:14px">hdfs dfs -rm -r /aa/bb/mytest.txt.2</span>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
使用-r参数可以一次删除多级目录。</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
验证</p>
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:14px">hdfs dfs -ls /aa/bb</span>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; line-height:1.6">应为空</span><br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; line-height:1.6"><br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</span></p>
<ul class="list-paddingleft-2" style="margin:0px; padding:0px 0px 0px 30px; max-width:100%; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<li style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; word-wrap:break-word!important">
修改文件权限</p>
</li></ul>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
与Linux文件系统中的用法一样，修改文件所属权限</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:14px">-chgrp</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:14px">-chmod</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:14px">-chown</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
示例</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:14px">hdfs dfs -chmod 666 /test/mytest.txt</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:14px">hdfs dfs -chown someuser:somegrp /test/mytest.txt</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<ul class="list-paddingleft-2" style="margin:0px; padding:0px 0px 0px 30px; max-width:100%; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<li style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; word-wrap:break-word!important">
统计文件系统的可用空间</p>
</li></ul>
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:14px">hdfs dfs -df -h /</span>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<ul class="list-paddingleft-2" style="margin:0px; padding:0px 0px 0px 30px; max-width:100%; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<li style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; word-wrap:break-word!important">
统计文件夹的大小</p>
</li></ul>
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:14px">hdfs dfs -du -s -h /test</span>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; color:rgb(30,155,232)"><span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">3、Java API操作</span></span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; color:rgb(30,155,232)"><span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important"><br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</span></span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; color:rgb(30,155,232)"><span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">（1）环境配置</span></span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; color:rgb(30,155,232)"><span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important"><br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</span></span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
因为需要在本机链接Hadoop虚机服务器，所以需要配置Hadoop，使其可以被外部访问。</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
先登录Hadoop虚机服务器，然后：</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
1）查看本机IP</p>
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:14px">ip address</span>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
例如IP为：192.168.31.239</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
2）修改文件：</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; line-height:25.6px; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:14px">vi /usr/local/hadoop-2.7.3/etc/hadoop/core-site.xml</span></p>
<p style="margin-top:0px; margin-bottom:0px; margin-left:4px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; text-indent:17px; line-height:17px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; line-height:25.6px; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:14px">&lt;property&gt;</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; line-height:25.6px; text-indent:24px; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:14px">&lt;name&gt;fs.defaultFS&lt;/name&gt;</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; line-height:25.6px; text-indent:24px; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:14px">&lt;value&gt;hdfs://localhost:9000&lt;/value&gt;</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; line-height:25.6px; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:14px">&lt;/property&gt;</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
把其中的localhost:9000修改为本机IP 192.168.31.239:9000</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
3）重新启动HDFS</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; line-height:25.6px; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:14px">#停止</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; line-height:25.6px; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:14px">stop-dfs.sh</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; line-height:25.6px; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:14px">#启动</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; line-height:25.6px; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:14px">start-dfs.sh</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; color:rgb(30,155,232)"><span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">（2）搭建开发环境</span></span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; color:rgb(30,155,232)"><span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important"><br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</span></span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
1）新建项目目录hdfstest</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
2）在项目目录下创建pom.xml</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
内容：</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<img class=" img_loading" src="" alt="" style="margin:0px; padding:0px; height:114px!important; max-width:100%; word-wrap:break-word!important; background-color:rgb(238,237,235); border:1px solid rgb(238,237,235); width:521px!important"></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<img class=" " src="http://mmbiz.qpic.cn/mmbiz_png/tibrg3AoIJTuicmcP1xkgIrtfIx3tfhFl8yfvwqEJRaKxuTu0ZAUfjGdyjEpbnIPSjRGiayfSnJwfOmU9jXLY1CrQ/640?wx_fmt=png&amp;tp=webp&amp;wxfrom=5&amp;wx_lazy=1" alt="" style="margin:0px; padding:0px; height:auto!important; max-width:100%; word-wrap:break-word!important; width:auto!important; visibility:visible!important"></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
3）创建源码目录<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; color:rgb(51,51,51)">src/main/java</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; color:rgb(51,51,51)"><br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
现在项目目录结构</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; line-height:25.6px; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:14px">├── pom.xml</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; line-height:25.6px; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:14px">!"" src</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; line-height:25.6px; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:14px">│     └── main</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; line-height:25.6px; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:14px">│     └── java</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; color:rgb(30,155,232)"><span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">（3）示例代码</span></span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; color:rgb(30,155,232)"><span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">查看文件列表ls</span></span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; color:rgb(30,155,232)"><span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important"><br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</span></span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
1）新建文件src/main/java/Ls.java</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
列出/下的文件列表，及递归获取所有文件</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<img class=" " src="http://mmbiz.qpic.cn/mmbiz_png/tibrg3AoIJTuicmcP1xkgIrtfIx3tfhFl80dFs2A33kJjRWkjdibGGicPY0zed7PxP9tenNkbLIZY1f9gakoAcIJYQ/640?wx_fmt=png&amp;tp=webp&amp;wxfrom=5&amp;wx_lazy=1" alt="" style="margin:0px; padding:0px; height:auto!important; max-width:100%; word-wrap:break-word!important; width:auto!important; visibility:visible!important"></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<img class=" " src="http://mmbiz.qpic.cn/mmbiz_png/tibrg3AoIJTuicmcP1xkgIrtfIx3tfhFl8ePM0L0SlnPOeia0xhxt8cZT4jjibQI3ZJUcWBN4ZL4fn9JRPbdFLR1Uw/640?wx_fmt=png&amp;tp=webp&amp;wxfrom=5&amp;wx_lazy=1" alt="" style="margin:0px; padding:0px; height:auto!important; max-width:100%; word-wrap:break-word!important; width:auto!important; visibility:visible!important"></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
2）编译执行</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; line-height:25.6px; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:14px">mvn compile</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; line-height:25.6px; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:14px">mvn exec:java -Dexec.mainClass="Ls" -Dexec.cleanupDaemonThreads</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; line-height:25.6px; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:14px">=false</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; color:rgb(30,155,232)"><span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">创建目录mkdir</span></span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; color:rgb(30,155,232)"><span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important"><br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</span></span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
在HDFS中创建目录/mkdir/a/b</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
1）新建文件</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
src/main/java/Mkdir.java</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<img class=" img_loading" src="" alt="" style="margin:0px; padding:0px; height:121px!important; max-width:100%; word-wrap:break-word!important; background-color:rgb(238,237,235); border:1px solid rgb(238,237,235); width:516px!important"></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<img class=" img_loading" src="" alt="" style="margin:0px; padding:0px; height:206px!important; max-width:100%; word-wrap:break-word!important; background-color:rgb(238,237,235); border:1px solid rgb(238,237,235); width:524px!important"></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
2）编译执行</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; line-height:25.6px; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:14px">mvn compile</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; line-height:25.6px; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:14px">mvn exec:java -Dexec.mainClass="Mkdir" -Dexec.cleanupDaemonThre</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; line-height:25.6px; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:14px">ads=false</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
3）在服务器中使用HDFS命令验证</p>
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:14px">hdfs dfs -ls /mkdir</span>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; color:rgb(30,155,232)"><span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">上传文件put</span></span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; color:rgb(30,155,232)"><span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important"><br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</span></span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
在当前项目目录下新建测试文件，上传到HDFS中的/mkdir</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
1）在项目目录下创建测试文件testfile.txt，内容随意</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
2）新建文件src/main/java/Put.java</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<img class=" img_loading" src="" alt="" style="margin:0px; padding:0px; height:249px!important; max-width:100%; word-wrap:break-word!important; background-color:rgb(238,237,235); border:1px solid rgb(238,237,235); width:518px!important"></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<img class=" img_loading" src="" alt="" style="margin:0px; padding:0px; height:134px!important; max-width:100%; word-wrap:break-word!important; background-color:rgb(238,237,235); border:1px solid rgb(238,237,235); width:515px!important"></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
3）编译执行</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; line-height:25.6px; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:14px">mvn compile</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; line-height:25.6px; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:14px">mvn exec:java -Dexec.mainClass="Put" -Dexec.cleanupDaemonThread</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; line-height:25.6px; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:14px">s=false</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
4）在服务器中使用HDFS命令验证</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:14px">hdfs dfs -ls /mkdir</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:14px">hdfs dfs -cat /mkdir/testfile.txt</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; color:rgb(30,155,232)"><span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">下载文件get</span></span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; color:rgb(30,155,232)"><span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important"><br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</span></span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
1）新建文件src/main/java/Get.java</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
把HDFS中/mkdir/testﬁle.txt下载到当前项目目录下</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<img class=" img_loading" src="" alt="" style="margin:0px; padding:0px; height:331px!important; max-width:100%; word-wrap:break-word!important; background-color:rgb(238,237,235); border:1px solid rgb(238,237,235); width:518px!important"></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
2）编译执行</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:14px">mvn compile</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:14px">mvn exec:java -Dexec.mainClass="Get" -Dexec.cleanupDaemonThread</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:14px">s=false</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
3）查看项目目录下是否存在<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; color:rgb(51,51,51)">testﬁle2.txt及其内容</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; color:rgb(30,155,232)"><span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">删除文件delete</span></span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; color:rgb(30,155,232)"><span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important"><br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</span></span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
删除HDFS上之前上传的/mkdir/testﬁle.txt</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
1）新建文件src/main/java/Del.java</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<img class=" img_loading" src="" alt="" style="margin:0px; padding:0px; height:346px!important; max-width:100%; word-wrap:break-word!important; background-color:rgb(238,237,235); border:1px solid rgb(238,237,235); width:520px!important"></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
2）编译执行</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:14px">mvn compile</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:14px">mvn exec:java -Dexec.mainClass="Del" -Dexec.cleanupDaemonThread</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:14px">s=false</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
3）在服务器中使用HDFS命令验证，检查testﬁle.txt是否被删除</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:14px"></span><span style="margin:0px; padding:0px; max-width:100%; line-height:25.6px; font-size:14px; word-wrap:break-word!important">hdfs dfs -ls /mkdir</span><span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:14px"></span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; color:rgb(30,155,232)"><span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">重命名rename</span></span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; color:rgb(30,155,232)"><span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important"><br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</span></span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
把HDFS中的/mkdir/a重命名为/mkdir/a2</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
1）新建文件src/main/java/Rename.java</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<img class=" " src="http://mmbiz.qpic.cn/mmbiz_png/tibrg3AoIJTuicmcP1xkgIrtfIx3tfhFl8gKb9pDuTCYVbKDyph5RKvCkvgmQhSlcIn4FibK9LUHxLj4wocbUPe1w/640?wx_fmt=png&amp;tp=webp&amp;wxfrom=5&amp;wx_lazy=1" alt="" style="margin:0px; padding:0px; height:auto!important; max-width:100%; word-wrap:break-word!important; width:auto!important; visibility:visible!important"></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
2）编译执行</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:14px">mvn compile</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:14px">mvn exec:java -Dexec.mainClass="Rename" -Dexec.cleanupDaemonThr</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:14px">eads=false</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
3）在服务器中使用HDFS命令验证</p>
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:14px">hdfs dfs -ls /mkdir</span>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
流方式读取文件部分内容</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
上传一个文本文件，然后使用流方式读取部分内容保存到当前项目目录。</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
1）在服务器中创建一个测试文件test.txt，内容：</p>
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:14px">123456789abcdefghijklmn</span>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
上传到HDFS</p>
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:14px">hdfs dfs -put test.txt /</span>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
2）在本地项目中新建文件src/main/java/StreamGet.java</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<img class=" " src="http://mmbiz.qpic.cn/mmbiz_png/tibrg3AoIJTuicmcP1xkgIrtfIx3tfhFl81mLcabCM7ESDviaRBkibsU6NahiaLjQKDppGOxqc8JfEJcZuBMFwGp4CQ/640?wx_fmt=png&amp;tp=webp&amp;wxfrom=5&amp;wx_lazy=1" alt="" style="margin:0px; padding:0px; height:auto!important; max-width:100%; word-wrap:break-word!important; width:auto!important; visibility:visible!important"></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
2）编译执行</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:14px">mvn compile</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:14px">mvn exec:java -Dexec.mainClass="StreamGet" -Dexec.cleanupDaemon</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:14px">Threads=false</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
3）执行后查看项目目录下的test.txt.part2</p>
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:14px">6789abcdefghijklmn</span>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
前面的12345已经被略过</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; font-size:20px; line-height:20px; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important"><span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:16px">三、深入了解</span></span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; color:rgb(30,155,232)"><span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">1、写入机制</span></span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; color:rgb(30,155,232)"><span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important"><br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</span></span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
向HDFS中写入文件时，是按照块儿为单位的，client会根据配置中设置的块儿的大小把目标文件切为多块，例如文件是300M ,配置中块大小值为128M，那么就分为3块儿。</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">具体写入流程：</span></p>
<ol class="list-paddingleft-2" style="margin:0px; padding:0px 0px 0px 30px; max-width:100%; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<li style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; word-wrap:break-word!important">
client向namenode发请求，说想要上传文件</p>
</li><li style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; word-wrap:break-word!important">
namenode会检查目标文件是否存在、父目录是否存在，检查没有问题后返回确认信息</p>
</li><li style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; word-wrap:break-word!important">
client再发请求，问第一个block应该传到哪些datanode上</p>
</li><li style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; word-wrap:break-word!important">
namenode经过衡量，返回3个可用的datanode（A，B，C）</p>
</li><li style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; word-wrap:break-word!important">
client与A建立连接，A与B建立连接，B与C建立连接，形成一个pipeline</p>
</li><li style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; word-wrap:break-word!important">
传输管道建立完成后，client开始向A发送数据包，此数据包会经过管道一次传递到B和C</p>
</li><li style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; word-wrap:break-word!important">
当第一个block的数据都传完以后，client再向namenode请求第二个block上传到哪些datanode，然后建立传输管道发送数据</p>
</li><li style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; word-wrap:break-word!important">
就这样，直到client把文件全部上传完成</p>
</li></ol>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<img class=" " src="http://mmbiz.qpic.cn/mmbiz_jpg/tibrg3AoIJTuicmcP1xkgIrtfIx3tfhFl807iarFMQlicEg8NzH2hibbkI3FibeTxo8mqDpxC7JQ3jMrsYWUk6eNN8gA/640?wx_fmt=jpeg&amp;tp=webp&amp;wxfrom=5&amp;wx_lazy=1" alt="" style="margin:0px; padding:0px; height:auto!important; max-width:100%; word-wrap:break-word!important; width:auto!important; visibility:visible!important"></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; color:rgb(30,155,232)"><span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">2、读取机制</span></span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<ol class="list-paddingleft-2" style="margin:0px; padding:0px 0px 0px 30px; max-width:100%; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<li style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; word-wrap:break-word!important">
Client把要读取的文件路径发给namenode，查询元数据，找到文件块所在的datanode服务器</p>
</li><li style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; word-wrap:break-word!important">
Client直到了文件包含哪几块儿、每一块儿在哪些datanode上，就选择那些离自己进的datanode（在同一机房，如果有多个离着近的，就随机选择），请求简历socket流</p>
</li><li style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; word-wrap:break-word!important">
从datanode获取数据</p>
</li><li style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; word-wrap:break-word!important">
Client接收数据包，先本地缓存，然后写入目标文件</p>
</li><li style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; word-wrap:break-word!important">
直到文件读取完成</p>
</li></ol>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<img class=" img_loading" src="" alt="" style="margin:0px; padding:0px; height:330px!important; max-width:100%; word-wrap:break-word!important; background-color:rgb(238,237,235); border:1px solid rgb(238,237,235); width:609px!important"></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important"><span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; color:rgb(30,155,232)">3、NameNode机制</span></span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important"><span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; color:rgb(30,155,232)"><br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</span></span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
通过对HDFS读写流程的了解，可以发现namenode是一个很重要的部分，它记录着整个HDFS系统的元数据，这些元数据是需要持久化的，要保存到文件中。</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
Namenode还要承受巨大的访问量，client读写文件时都需要请求namenode，写文件时要修改元数据，读文件时要查询元数据。</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
为了提高效率，namenode便将元数据加载到内存中，每次修改时，直接修改内存，而不是直接修改文件，同时会记录下操作日志，供后期修改文件时使用。</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
这样，namenode对数据的管理就涉及到了3种存储形式：</p>
<ol class="list-paddingleft-2" style="margin:0px; padding:0px 0px 0px 30px; max-width:100%; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<li style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; word-wrap:break-word!important">
内存数据</p>
</li><li style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; word-wrap:break-word!important">
元数据文件</p>
</li><li style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; word-wrap:break-word!important">
操作日志文件</p>
</li></ol>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
namenode需要定期对元数据文件和日志文件进行整合，以保证文件中数据是新的，但这个过程很消耗性能，namenode需要快速地响应client的大量请求，很难去完成文件整合操作，这时就引入了一个小助手secondnamenode。</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
secondnamenode会定期从namenode中下载元数据文件和操作日志，进行整合，形成新的数据文件，然后传回namenode，并替换掉之前的旧文件。</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
secondnamenode是namenode的好帮手，替namenode完成了这个重体力活儿，并且还可以作为namenode的一个防灾备份，当namenode数据丢失时，secondnamenode上有最近一次整理好的数据文件，可以传给namenode进行加载，这样可以保证最少的数据丢失。</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<img class=" " src="http://mmbiz.qpic.cn/mmbiz_jpg/tibrg3AoIJTuicmcP1xkgIrtfIx3tfhFl8GS8VG55b76eQ3WRfuWibEiahdoCibuZicibfXLekeWjJx4MAQYJNY1n3upA/640?wx_fmt=jpeg&amp;tp=webp&amp;wxfrom=5&amp;wx_lazy=1" alt="" style="margin:0px; padding:0px; height:auto!important; max-width:100%; word-wrap:break-word!important; width:auto!important; visibility:visible!important"></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<img class=" " src="http://mmbiz.qpic.cn/mmbiz_jpg/tibrg3AoIJTuicmcP1xkgIrtfIx3tfhFl87nvrFoibEu7klUr0j5kibVRqjYRmxPZEbFWslPQfGDeZQxuknGUOBUWQ/640?wx_fmt=jpeg&amp;tp=webp&amp;wxfrom=5&amp;wx_lazy=1" alt="" style="margin:0px; padding:0px; height:auto!important; max-width:100%; word-wrap:break-word!important; width:auto!important; visibility:visible!important"></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; font-size:20px; line-height:20px; word-wrap:break-word!important">
<span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important"><span style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important; font-size:16px">小结</span></span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
<br style="margin:0px; padding:0px; max-width:100%; word-wrap:break-word!important">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; max-width:100%; clear:both; min-height:1em; color:rgb(62,62,62); font-family:微软雅黑; font-size:16px; word-wrap:break-word!important">
HDFS的基础内容介绍完了，希望可以帮助您快速熟悉HDFS的思路和使用方式。如有批评与建议（例如内容有误、不足的地方、改进建议等），欢迎留言讨论。</p>
<div><br>
</div>
            </div>
                </div>