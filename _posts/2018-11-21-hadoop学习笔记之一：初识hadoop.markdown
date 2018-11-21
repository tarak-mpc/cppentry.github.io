---
layout:     post
title:      hadoop学习笔记之一：初识hadoop
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h3 class="article-title" style="font-size:20px;color:rgb(32,120,169);">
hadoop学习笔记之一：初识hadoop</h3>
<div class="article-info" style="color:rgb(153,153,153);">
<br></div>
<div class="article-body cssbase" style="line-height:1.8em;vertical-align:top;">
<span style="color:rgb(51,51,51);font-family:'微软雅黑', Verdana, Arial, Helvetica, sans-serif;font-size:13px;line-height:23.383333206176758px;">引言</span><span style="color:rgb(51,51,51);font-family:'微软雅黑', Verdana, Arial, Helvetica, sans-serif;font-size:13px;line-height:23.383333206176758px;"></span>
<div class="qa-gap" style="clear:both;line-height:1px;color:rgb(51,51,51);font-family:'微软雅黑', Verdana, Arial, Helvetica, sans-serif;font-size:13px;">
</div>
<span style="color:rgb(51,51,51);font-family:'微软雅黑', Verdana, Arial, Helvetica, sans-serif;font-size:13px;line-height:23.383333206176758px;">最近了解到淘宝使用hadoop的项目多了起来，hadoop对于许多测试人员来说或许是个新鲜玩儿，因此，把自己之前整理的学习笔记整理发上来，希望通过此系列文章快速了解Hadoop的基本概念和架构原理，从而助于hadoop相关项目的测试理解和沟通。</span>
<div class="qa-gap" style="clear:both;line-height:1px;color:rgb(51,51,51);font-family:'微软雅黑', Verdana, Arial, Helvetica, sans-serif;font-size:13px;">
</div>
<span style="color:rgb(51,51,51);font-family:'微软雅黑', Verdana, Arial, Helvetica, sans-serif;font-size:13px;line-height:23.383333206176758px;">Hadoop简介</span><span style="color:rgb(51,51,51);font-family:'微软雅黑', Verdana, Arial, Helvetica, sans-serif;font-size:13px;line-height:23.383333206176758px;"></span>
<div class="qa-gap" style="clear:both;line-height:1px;color:rgb(51,51,51);font-family:'微软雅黑', Verdana, Arial, Helvetica, sans-serif;font-size:13px;">
</div>
<span style="color:rgb(51,51,51);font-family:'微软雅黑', Verdana, Arial, Helvetica, sans-serif;font-size:13px;line-height:23.383333206176758px;">Hadoop 是一个实现了 MapReduce 计算模型的开源分布式并行编程框架,借助于 Hadoop, 程序员可以轻松地编写分布式并行程序，将其运行于计算机集群上，完成海量数据的计算。</span>
<div class="qa-gap" style="clear:both;line-height:1px;color:rgb(51,51,51);font-family:'微软雅黑', Verdana, Arial, Helvetica, sans-serif;font-size:13px;">
</div>
<span style="color:rgb(51,51,51);font-family:'微软雅黑', Verdana, Arial, Helvetica, sans-serif;font-size:13px;line-height:23.383333206176758px;">如果你对Hadoop不熟悉，下面这些名词你或许耳熟能详，Google File System、Google Map/Reduce模型、lucene 、Nutch、阿里云梯、云计算等，他们都与Hadoop有着或深或浅的关系，原来Hadoop离我们如此之近，那么花些时间继续了解它吧。</span>
<div class="qa-gap" style="clear:both;line-height:1px;color:rgb(51,51,51);font-family:'微软雅黑', Verdana, Arial, Helvetica, sans-serif;font-size:13px;">
</div>
<span style="color:rgb(51,51,51);font-family:'微软雅黑', Verdana, Arial, Helvetica, sans-serif;font-size:13px;line-height:23.383333206176758px;">Hadoop的架构</span><span style="color:rgb(51,51,51);font-family:'微软雅黑', Verdana, Arial, Helvetica, sans-serif;font-size:13px;line-height:23.383333206176758px;"></span>
<div class="qa-gap" style="clear:both;line-height:1px;color:rgb(51,51,51);font-family:'微软雅黑', Verdana, Arial, Helvetica, sans-serif;font-size:13px;">
</div>
<span style="color:rgb(51,51,51);font-family:'微软雅黑', Verdana, Arial, Helvetica, sans-serif;font-size:13px;line-height:23.383333206176758px;">先抛开Hadoop，简单地想想看，假设我们需要读一个10TB的数据集，怎么办?在传统的系统上，这需要很长时间，因为硬盘的传输速度是受限的。一个简单的办法是将数据存储在多个磁盘上，同时从多个磁盘并行读取数据，这将大大减少读取时间。</span>
<div class="qa-gap" style="clear:both;line-height:1px;color:rgb(51,51,51);font-family:'微软雅黑', Verdana, Arial, Helvetica, sans-serif;font-size:13px;">
</div>
<a href="http://qa.taobao.com/wp-content/uploads/2010/12/image001.jpg" rel="nofollow" style="color:rgb(19,85,135);font-family:'微软雅黑', Verdana, Arial, Helvetica, sans-serif;font-size:13px;line-height:23.383333206176758px;"><img src="http://qa.taobao.com/wp-content/uploads/2010/12/image001.jpg" alt="" width="544" height="435" class="alignnone size-full wp-image-10515"></a><span style="color:rgb(51,51,51);font-family:'微软雅黑', Verdana, Arial, Helvetica, sans-serif;font-size:13px;line-height:23.383333206176758px;"></span>
<div class="qa-gap" style="clear:both;line-height:1px;color:rgb(51,51,51);font-family:'微软雅黑', Verdana, Arial, Helvetica, sans-serif;font-size:13px;">
</div>
<span style="color:rgb(51,51,51);font-family:'微软雅黑', Verdana, Arial, Helvetica, sans-serif;font-size:13px;line-height:23.383333206176758px;">上面的方案需要解决两个主要问题，一个是硬件故障，要保证其中一个硬件坏了但数据仍然完整，Hadoop的文件系统HDFS（Hadoop Distributed Filesystem）提供了一种解决方式。另外一个问题是如何并行读取数据并合并保证正确性，通过MapReduce的编程模型可以简化这个问题。简而言之，Hadoop提供了一个稳定的共享存储和分析系统，存储由HDFS实现，分析由MapReduce实现，这两者构成了Hadoop的核心功能。</span>
<div class="qa-gap" style="clear:both;line-height:1px;color:rgb(51,51,51);font-family:'微软雅黑', Verdana, Arial, Helvetica, sans-serif;font-size:13px;">
</div>
<span style="color:rgb(51,51,51);font-family:'微软雅黑', Verdana, Arial, Helvetica, sans-serif;font-size:13px;line-height:23.383333206176758px;"></span>
HDFS<span style="color:rgb(51,51,51);font-family:'微软雅黑', Verdana, Arial, Helvetica, sans-serif;font-size:13px;line-height:23.383333206176758px;"></span>
<div class="qa-gap" style="clear:both;line-height:1px;color:rgb(51,51,51);font-family:'微软雅黑', Verdana, Arial, Helvetica, sans-serif;font-size:13px;">
</div>
<span style="color:rgb(51,51,51);font-family:'微软雅黑', Verdana, Arial, Helvetica, sans-serif;font-size:13px;line-height:23.383333206176758px;">HDFS集群有两种节点，以管理者-工作者模式运行，即1个名称节点（NameNode）和N个数据节点（DataNode）。其底层实现是将文件切割成块，然后将这些块存储在不同的DataNode上。为了容错容灾，每个块还被复制多份存储在不同的DataNode上。NameNode管理文件系统的命名空间，记录每个文件被切割成了多少块，这些块可以从哪些DataNode上获得，以及各个DataNode的状态信息等。下图是Hadoop集群的简化视图</span>
<div class="qa-gap" style="clear:both;line-height:1px;color:rgb(51,51,51);font-family:'微软雅黑', Verdana, Arial, Helvetica, sans-serif;font-size:13px;">
</div>
<a href="http://qa.taobao.com/wp-content/uploads/2010/12/image002.jpg" rel="nofollow" style="color:rgb(19,85,135);font-family:'微软雅黑', Verdana, Arial, Helvetica, sans-serif;font-size:13px;line-height:23.383333206176758px;"><img src="http://qa.taobao.com/wp-content/uploads/2010/12/image002.jpg" alt="" width="381" height="215" class="alignnone size-full wp-image-10516"></a><span style="color:rgb(51,51,51);font-family:'微软雅黑', Verdana, Arial, Helvetica, sans-serif;font-size:13px;line-height:23.383333206176758px;"></span>
<div class="qa-gap" style="clear:both;line-height:1px;color:rgb(51,51,51);font-family:'微软雅黑', Verdana, Arial, Helvetica, sans-serif;font-size:13px;">
</div>
<span style="color:rgb(51,51,51);font-family:'微软雅黑', Verdana, Arial, Helvetica, sans-serif;font-size:13px;line-height:23.383333206176758px;">HDFS内部通信都是基于标准的TCP/IP协议，NameNode 依赖来自每个 DataNode 的定期心跳（heartbeat）消息。每条消息都包含一个块报告，NameNode 可以根据这个报告验证块映射和其他文件系统元数据。如果 DataNode
 不能发送心跳消息，NameNode 将采取修复措施，重新复制在该节点上丢失的块。更多HDFS的架构和设计请阅读http://hadoop.apache.org/common/docs/current/cn/hdfs_design.html</span>
<div class="qa-gap" style="clear:both;line-height:1px;color:rgb(51,51,51);font-family:'微软雅黑', Verdana, Arial, Helvetica, sans-serif;font-size:13px;">
</div>
<div class="qa-gap" style="clear:both;line-height:1px;color:rgb(51,51,51);font-family:'微软雅黑', Verdana, Arial, Helvetica, sans-serif;font-size:13px;">
</div>

<strong>MapReduce</strong>
<div class="qa-gap" style="clear:both;line-height:1px;color:rgb(51,51,51);font-family:'微软雅黑', Verdana, Arial, Helvetica, sans-serif;font-size:13px;">
</div>
<a href="http://qa.taobao.com/wp-content/uploads/2010/12/image003.jpg" rel="nofollow" style="color:rgb(19,85,135);font-family:'微软雅黑', Verdana, Arial, Helvetica, sans-serif;font-size:13px;line-height:23.383333206176758px;"><img src="http://qa.taobao.com/wp-content/uploads/2010/12/image003.jpg" alt="" width="536" height="369" class="alignnone size-full wp-image-10517"></a><span style="color:rgb(51,51,51);font-family:'微软雅黑', Verdana, Arial, Helvetica, sans-serif;font-size:13px;line-height:23.383333206176758px;"></span>
<div class="qa-gap" style="clear:both;line-height:1px;color:rgb(51,51,51);font-family:'微软雅黑', Verdana, Arial, Helvetica, sans-serif;font-size:13px;">
</div>
<div class="qa-gap" style="clear:both;line-height:1px;color:rgb(51,51,51);font-family:'微软雅黑', Verdana, Arial, Helvetica, sans-serif;font-size:13px;">
</div>
<span style="color:rgb(51,51,51);font-family:'微软雅黑', Verdana, Arial, Helvetica, sans-serif;font-size:13px;line-height:23.383333206176758px;">上图说明了用 MapReduce 来处理大数据集的过程, 这个 MapReduce 的计算过程简而言之，就是将大数据集分解为成若干个小数据集，每个(或若干个)数据集分别由集群中的一个结点(一般就是一台普通的计算机)进行处理并生成中间结果，然后这些中间结果又由大量的结点进行合并,
 形成最终结果。</span>
<div class="qa-gap" style="clear:both;line-height:1px;color:rgb(51,51,51);font-family:'微软雅黑', Verdana, Arial, Helvetica, sans-serif;font-size:13px;">
</div>
<span style="color:rgb(51,51,51);font-family:'微软雅黑', Verdana, Arial, Helvetica, sans-serif;font-size:13px;line-height:23.383333206176758px;">计算模型的核心是 Map 和 Reduce 两个函数，这两个函数由用户负责实现，功能是按一定的映射规则将输入的 对转换成另一个或一批 对输出。下图是一个简单的MapReduce示例，实现字数统计功能。</span>
<div class="qa-gap" style="clear:both;line-height:1px;color:rgb(51,51,51);font-family:'微软雅黑', Verdana, Arial, Helvetica, sans-serif;font-size:13px;">
</div>
<a href="http://qa.taobao.com/wp-content/uploads/2010/12/image004.jpg" rel="nofollow" style="color:rgb(19,85,135);font-family:'微软雅黑', Verdana, Arial, Helvetica, sans-serif;font-size:13px;line-height:23.383333206176758px;"><img src="http://qa.taobao.com/wp-content/uploads/2010/12/image004.jpg" alt="" width="441" height="610" class="alignnone size-full wp-image-10518"></a><span style="color:rgb(51,51,51);font-family:'微软雅黑', Verdana, Arial, Helvetica, sans-serif;font-size:13px;line-height:23.383333206176758px;"></span>
<div class="qa-gap" style="clear:both;line-height:1px;color:rgb(51,51,51);font-family:'微软雅黑', Verdana, Arial, Helvetica, sans-serif;font-size:13px;">
</div>
<div class="qa-gap" style="clear:both;line-height:1px;color:rgb(51,51,51);font-family:'微软雅黑', Verdana, Arial, Helvetica, sans-serif;font-size:13px;">
</div>
<span style="color:rgb(51,51,51);font-family:'微软雅黑', Verdana, Arial, Helvetica, sans-serif;font-size:13px;line-height:23.383333206176758px;"></span>分布式并行运算<span style="color:rgb(51,51,51);font-family:'微软雅黑', Verdana, Arial, Helvetica, sans-serif;font-size:13px;line-height:23.383333206176758px;"></span>
<div class="qa-gap" style="clear:both;line-height:1px;color:rgb(51,51,51);font-family:'微软雅黑', Verdana, Arial, Helvetica, sans-serif;font-size:13px;">
</div>
<span style="color:rgb(51,51,51);font-family:'微软雅黑', Verdana, Arial, Helvetica, sans-serif;font-size:13px;line-height:23.383333206176758px;">Hadoop 的分布式并行运算有一个作为主控的JobTracker，用于调度和管理其它的 TaskTracker, JobTracker 可以运行于集群中任一台计算机上。TaskTracker负责执行任务，必须运行于 DataNode 上，即
 DataNode 既是数据存储结点，也是计算结点，这样可以减少数据在网络上的传输，降低对网络带宽的需求。 JobTracker 将 Map 任务和 Reduce 任务分发给空闲的 TaskTracker, 让这些任务并行运行，并负责监控任务的运行情况。如果某一个 TaskTracker 出故障了，JobTracker 会将其负责的任务转交给另一个空闲的 TaskTracker 重新运行。</span>
<div class="qa-gap" style="clear:both;line-height:1px;color:rgb(51,51,51);font-family:'微软雅黑', Verdana, Arial, Helvetica, sans-serif;font-size:13px;">
</div>
<div class="qa-gap" style="clear:both;line-height:1px;color:rgb(51,51,51);font-family:'微软雅黑', Verdana, Arial, Helvetica, sans-serif;font-size:13px;">
</div>
<span style="color:rgb(51,51,51);font-family:'微软雅黑', Verdana, Arial, Helvetica, sans-serif;font-size:13px;line-height:23.383333206176758px;"></span>Hadoop的其他子项目<span style="color:rgb(51,51,51);font-family:'微软雅黑', Verdana, Arial, Helvetica, sans-serif;font-size:13px;line-height:23.383333206176758px;"></span>
<div class="qa-gap" style="clear:both;line-height:1px;color:rgb(51,51,51);font-family:'微软雅黑', Verdana, Arial, Helvetica, sans-serif;font-size:13px;">
</div>
<span style="color:rgb(51,51,51);font-family:'微软雅黑', Verdana, Arial, Helvetica, sans-serif;font-size:13px;line-height:23.383333206176758px;">上面介绍的MapReduce、HDFS、分布式并行运算是Hadoop最核心的功能。还有一些子项目提供补充性服务。如hive（提供基于sql的查询语言查询存储在HDFS中的数据）、Hbase（一种分布式、列存储数据库，适用于需要实时读写、随机访问超大数据集的场景）、Zookeeper（一个分布式、高可用性的协调服务，提供分布式锁之类的基本服务）、pig（一种数据流语言和运行环境，用以检索非常大的数据集，使程序员能专注于数据而不是执行本质）等。</span>
<div class="qa-gap" style="clear:both;line-height:1px;color:rgb(51,51,51);font-family:'微软雅黑', Verdana, Arial, Helvetica, sans-serif;font-size:13px;">
</div>
<div class="qa-gap" style="clear:both;line-height:1px;color:rgb(51,51,51);font-family:'微软雅黑', Verdana, Arial, Helvetica, sans-serif;font-size:13px;">
</div>
<span style="color:rgb(51,51,51);font-family:'微软雅黑', Verdana, Arial, Helvetica, sans-serif;font-size:13px;line-height:23.383333206176758px;">小结</span><span style="color:rgb(51,51,51);font-family:'微软雅黑', Verdana, Arial, Helvetica, sans-serif;font-size:13px;line-height:23.383333206176758px;"></span>
<div class="qa-gap" style="clear:both;line-height:1px;color:rgb(51,51,51);font-family:'微软雅黑', Verdana, Arial, Helvetica, sans-serif;font-size:13px;">
</div>
<span style="color:rgb(51,51,51);font-family:'微软雅黑', Verdana, Arial, Helvetica, sans-serif;font-size:13px;line-height:23.383333206176758px;">如果你坚持看到这里，或许会觉得失望，介绍了一堆的概念和名词，貌似对你的帮助不大，不要沮丧，第一次接触分布式知识的人大都如此，至少你跟开发又多了些共同语言，下次那个哥们冲你说pig（猪）时，你可以认为他说的是pig（一种数据流语言和运行环境，用以检索非常大的数据集，使程序员能专注于数据而不是执行本质）。</span></div>
<div><span style="color:rgb(51,51,51);font-family:'微软雅黑', Verdana, Arial, Helvetica, sans-serif;font-size:13px;line-height:23.383333206176758px;"><br></span></div>
            </div>
                </div>