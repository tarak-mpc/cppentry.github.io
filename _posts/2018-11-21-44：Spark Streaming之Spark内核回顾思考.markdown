---
layout:     post
title:      44：Spark Streaming之Spark内核回顾思考
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/andyshar/article/details/52144108				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h2 style="font-family:'微软雅黑';font-size:21px;"><span style="font-family:'Microsoft Yahei';background-color:inherit;"><span style="font-family:'Microsoft Yahei';background-color:inherit;"><span style="font-size:14px;background-color:inherit;">本期内容：</span></span></span></h2>
<span style="font-family:'Microsoft Yahei';font-size:14px;line-height:21px;">1. Spark Streaming与Spark Core</span>
<p style="font-family:'微软雅黑';font-size:14px;line-height:21px;">
</p>
<p style="font-family:'微软雅黑';font-size:14px;line-height:21px;">
<span style="font-family:'Microsoft Yahei';background-color:inherit;">2. Spark Core思考</span></p>
<p style="font-family:'微软雅黑';font-size:14px;line-height:21px;">
<span style="font-family:'Microsoft Yahei';background-color:inherit;"><br style="background-color:inherit;"></span></p>
<p style="font-family:'微软雅黑';font-size:14px;line-height:21px;">
<span style="font-family:'Microsoft Yahei';background-color:inherit;">Spark上的子框架，都是后来加上去的。都是在Spark core上完成的，所有框架一切的实现最终还是由Spark core来做的。抛开任何具体的东西，现在考虑下Spark core是个什么东西。</span></p>
<h3 style="font-family:'微软雅黑';font-size:16px;"><span style="font-family:'Microsoft Yahei';background-color:inherit;">解析rdd</span></h3>
<p style="font-family:'微软雅黑';font-size:14px;line-height:21px;">
<span style="font-family:'Microsoft Yahei';background-color:inherit;">程序就是数据+代码。所以首先，我们需要考虑spark core由什么数据结构构成，一共就三种：rdd,broadcast,accumulator，最重要、最核心的是rdd。</span></p>
<p style="font-family:'微软雅黑';font-size:14px;line-height:21px;">
<span style="font-family:'Microsoft Yahei';background-color:inherit;">rdd可以简单的认为是一个数组，只不过是一个分布式的数组。具备弹性、分布式的特性。我们一般传统的数据结构list、set等都是在一台机器，而spark的内核提供的最基础、最核心、最重要的数据结构是rdd，rdd是平铺在分布式的机器。<br style="background-color:inherit;">
我们看这里发生了什么变化。实质上我们想，数据平铺在不同的机器，也就是数据的不同部分分布在不同的机器上，为了获得不同部分的数据，也要获得指针(地址)。执政会包含具体在什么机器上，范围是什么，和数据的下标相比只不过是加上了一个机器的维度。这个和在一台机器有没有本质的区别。因为都是地址的定位，只不过rdd有一套自己的定位机制。所以我们不应该认为rdd学习比数组学习更复杂。</span></p>
<h3 style="font-family:'微软雅黑';font-size:16px;"><span style="font-family:'Microsoft Yahei';background-color:inherit;">rdd的调度</span></h3>
<p style="font-family:'微软雅黑';font-size:14px;line-height:21px;">
<span style="font-family:'Microsoft Yahei';background-color:inherit;">基于rdd的编程和调度，我们也同样拿数组的编程调度来类比。数组操作是在一个进程中，而rdd中，需要在不同的机器上操作，就构成了数据不同代码动的理念。如果要操作数据，比如map，要找到数据所在的机器上，这个是rdd的perferedLocation来告诉你的。原先单机计算的时候，也需要知道数据在哪里，现在知道数据在哪里后，需要把代码传到那里(通过Http等方式），但是也只是把代码传过去而已。所以说分布式计算和单机计算也没有本质区别。</span></p>
<p style="font-family:'微软雅黑';font-size:14px;line-height:21px;">
<span style="font-family:'Microsoft Yahei';background-color:inherit;">由于要跑到其他机器上，就有了管理的过程,怎么进行管理，就是driver负责的事情。数组的操作不需要怎么管理，因为没有到那么多机器上去。</span></p>
<p style="font-family:'微软雅黑';font-size:14px;line-height:21px;">
<span style="font-family:'Microsoft Yahei';background-color:inherit;">管理主要是管理计算，这里延伸出了一些东西：</span></p>
<p style="font-family:'微软雅黑';font-size:14px;line-height:21px;">
<span style="font-family:'Microsoft Yahei';background-color:inherit;">第一个层面就是要知道怎么确定到不同的机器上，这个就是任务调度。<br style="background-color:inherit;">
第二个层面，由于你是在不同的机器上，计算的时候出现了容错的方式，怎么进行容错。</span></p>
<h3 style="font-family:'微软雅黑';font-size:16px;"><span style="font-family:'Microsoft Yahei';background-color:inherit;">容错</span></h3>
<p style="font-family:'微软雅黑';font-size:14px;line-height:21px;">
<span style="font-family:'Microsoft Yahei';background-color:inherit;">对数组操作出错，如果业务很复杂，也肯定会有容错的方式，至于rdd的操作也会更加慎重的考虑容错，因为分布式一旦出错的话，位于不同的机器上，代价比一台机器上更大。</span></p>
<p style="font-family:'微软雅黑';font-size:14px;line-height:21px;">
<span style="font-family:'Microsoft Yahei';background-color:inherit;">业界容错的经典方式进行加检查点(加快照)并放在其他机器上，这种方式比较消耗时间，因为有网络通信等操作。</span></p>
<p style="font-family:'微软雅黑';font-size:14px;line-height:21px;">
<span style="font-family:'Microsoft Yahei';background-color:inherit;">rdd的方式是每次操作都会产生新的rdd，它记住的是算子之间的依赖关系，而不是从数据角度考虑容错，算子之间的关系进行记录是从数据产生的角度考虑容错。算子只是表达数据怎么产生这件事，rdd强大之处在于，既能够表达算子之间的依赖关系，还包涵了数据在哪里。</span></p>
<p style="font-family:'微软雅黑';font-size:14px;line-height:21px;">
<span style="font-family:'Microsoft Yahei';background-color:inherit;">从容错角度看，由于rdd包涵了数据在哪里，在容错时再次获得数据的时候，它包含了数据本身和计算过程，所以可以部分的恢复数据，而不是全部的。依旧，无论是数据还是计算关系都是在rdd中，这是个非常轻量级的过程，可以避免直接说数据本身。这是一个非常强悍的地方。</span></p>
<h3 style="font-family:'微软雅黑';font-size:16px;"><span style="font-family:'Microsoft Yahei';background-color:inherit;">shuffle</span></h3>
<p style="font-family:'微软雅黑';font-size:14px;line-height:21px;">
<span style="font-family:'Microsoft Yahei';background-color:inherit;">在分布式计算的时候会有一些自己规则，比较典型的就是机器间要传数据的时候，搞了一个shuffle。</span></p>
<p style="font-family:'微软雅黑';font-size:14px;line-height:21px;">
<span style="font-family:'Microsoft Yahei';background-color:inherit;">因为分布式的时候进行计算，计算时候有个很大的问题，就是不同的机器之间，需要通过数据级别的通讯。比如数组的计算，可能有全局级别的计算，比如各个item出现的次数。从分布式角度，group、reduce这种就是全局级别。</span></p>
<p style="font-family:'微软雅黑';font-size:14px;line-height:21px;">
<span style="font-family:'Microsoft Yahei';background-color:inherit;">考虑全局级别就是整个分布式的关键之所在，这个时候就像我们对一个数组进行整体的操作，对rdd全局级别的操作，由于数据分布在不同的机器上，这个时候全局级别的操作，要加上ip、端口和具体数据存在什么地方，而且要在机器之间传数据。</span></p>
<p style="font-family:'微软雅黑';font-size:14px;line-height:21px;">
<span style="font-family:'Microsoft Yahei';background-color:inherit;">机器之间传数据，代价比较大，所以当发生时，spark会把计算结果先保存在本地磁盘上，免得进行全局级别操作出现故障时，重新计算代码太大，这是一种用空间换时间的方式。计算成果放在本地磁盘上，整个驱动的调动程序会知道，方便告诉后面的计算数据到底在哪里。在在一个阶段计算时，会问driver数据在哪里，driver告诉之后，就会去上个阶段保存的地方去拿自己想要而数据，由于是分布式的，有自己的一套规则，对数据进行分门别类。</span></p>
<p style="font-family:'微软雅黑';font-size:14px;line-height:21px;">
<span style="font-family:'Microsoft Yahei';background-color:inherit;">shuffle的过程就是对全局数据进行分类整理的过程，因为任何一台机器都有可能包含想要的数据，所以会依赖所有的数据来源。整理抓到我们想要的数据之后，就继续进行计算，而由于进行网络的传输，都会有一些调用逻辑，从spark角度讲就变成了stage。</span></p>
<h3 style="font-family:'微软雅黑';font-size:16px;"><span style="font-family:'Microsoft Yahei';background-color:inherit;">小结</span></h3>
<p style="font-family:'微软雅黑';font-size:14px;line-height:21px;">
<span style="font-family:'Microsoft Yahei';background-color:inherit;">rdd就是个大的分布式数组，计算时有时是自己单纯的计算，有时是全局的计算，和对数组操作没有任何区别。</span></p>
<p style="font-family:'微软雅黑';font-size:14px;line-height:21px;">
<span style="font-family:'Microsoft Yahei';background-color:inherit;">不同语言都有一套语法规则，但是核心是思考如何处理数据。spark是运行在jvm上的，jvm并不知道spark这个东西，所以一个事实是，jvm只有一些基本的数据结构和语法的支持，spark只不过是在jvm基础上，构造了rdd这个数据结构而已。所以精通spark和精通其他java程序没有区别。因为jvm本身就屏蔽掉了不同机器之间的区别，只需要进行地址定位就可以，这就是jvm的伟大之处，分布式和单机处理没有本质区别。</span></p>
<p style="font-family:'微软雅黑';font-size:14px;line-height:21px;">
<br></p>
            </div>
                </div>