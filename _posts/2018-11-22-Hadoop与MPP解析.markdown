---
layout:     post
title:      Hadoop与MPP解析
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/qichangjian/article/details/77837476				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>    其实MPP（大规模并行处理计算机）架构的关系型数据库与Hadoop的理论基础是很相似的，都是<font color="green">将运算分布到节点中独立运算后进行结果合并</font>。</p>

<h2 id="hadoop与mpp的区别">Hadoop与MPP的区别：</h2>

<p><strong>1.底层数据库：</strong> <br>
    MPP跑的是SQL,而Hadoop底层处理是MapReduce程序。 <br>
<strong>2.扩展程度</strong> <br>
    MPP虽然是宣称可以横向扩展Scale OUT，但是这种扩展一般是扩展到100左右，而Hadoop一般可以扩展1000+。</p>

<p>这是因为什么呢？其实可以从<font color="red">CAP理论</font>上来找原因。</p>

<pre><code>在理论计算机科学中，CAP定理（CAP theorem），又被称作布鲁尔定理（Brewer's theorem），它指出对于一个分布式计算系统来说，不可能同时满足以下三点：
    一致性（Consistency)：
同一个数据在集群中的所有节点，同一时刻是否都是同样的值。
    可用性（Availability）：
集群中一部分节点故障后，集群整体是否还能处理客户端的更新请求。
    分区容忍性（Partition tolerance）：
是否允许数据的分区，分区的意思是指是否允许集群中的节点之间无法通信。
</code></pre>

<p>因为MPP始终还是DB,一定要考虑到C(Consistency),其次考虑A(Availability),最后才在可能的情况下尽量做好P(Partition-tolerance)。而Hadoop就是为了并行处理和存储设计的，所以数据都是以文件存储，所以有限考虑的是P,然后是A,最后再考虑C.所以后者的可靠型当然好于前者。</p>

<p><strong>以下几个方面制约了MPP数据库的扩展</strong>： <br>
<strong>1.高可用</strong>：MPP DB是通过Hash计算来确定数据行所在的物理机器（而Hadoop不需要此操作），对存储位置的不透明导致MPP的高可用很难办。 <br>
<strong>2.并行任务</strong>：数据是按照Hash来切分的，但是任务没有。每个任务，无论大小都要到每个节点上走一圈。 <br>
<strong>3.文件系统</strong>：数据切分了，但是文件数没有变少，每个表在节点上一定有一个到多个文件。同时节点数越多，存储的表越多，导致每个文件系统有上万甚至十万多个文件。 <br>
<strong>4.网络瓶颈</strong>：MPP强调对等的网络，点对点的连接也消耗了大量的网络宽带，限制了网络上的线性扩展（想象以下一台机器要给1000台机器发送信息）。等多的节点并没有提供更高的宽带，反而导致每个组节点间平均宽带降低。 <br>
<strong>5.其他关系数据库的枷锁</strong>：比如锁，日志，权限，管理节点瓶颈等均限制了MPP规模的扩大。</p>

<h2 id="hadoop和mpp的应用场景">Hadoop和MPP的应用场景</h2>

<p>    <font color="green">MPP数据库有对SQL的完整兼容和一些事务的处理能力</font>，对于用户来说，在实际的使用场景中，如果数据扩展需求不是特别大，需要的处理节点不多，数据都是结构化的数据，习惯使用传统的RDBMS的很多特性的场景，可以考虑MPP,例如Greenplum/Gbase等。 <br>
    如果<font color="green">有很多非结构化数据，或者数据量巨大，有需要扩展到成百上千个数据节点需求的</font>，这个时候Hadoop是更好的选择。</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>