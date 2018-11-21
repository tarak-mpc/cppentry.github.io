---
layout:     post
title:      hadoop深入研究:(一)——hdfs介绍
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div class="article_title"><span class="ico ico_type_Original"></span>
<h3><span class="link_title"><a href="http://blog.csdn.net/lastsweetop/article/details/8992505" rel="nofollow">hadoop深入研究:(一)——hdfs介绍</a></span></h3>
</div>
<div style="clear:both;"></div>
<div style="border-bottom:#ccc 1px solid;min-width:200px;border-left:#ccc 1px solid;background:#eee;border-top:#ccc 1px solid;border-right:#ccc 1px solid;">
<p style="text-align:right;"><span>目录<a title="系统根据文章中H1到H6标签自动生成文章目录" href="http://blog.csdn.net/lastsweetop/article/details/8992505#" rel="nofollow">(?)</a></span><a title="展开" href="http://blog.csdn.net/lastsweetop/article/details/8992505#" rel="nofollow">[+]</a></p>
<ol style="line-height:160%;margin-left:14px;"><li><a href="http://blog.csdn.net/lastsweetop/article/details/8992505#t0" rel="nofollow">hdfs设计原则</a></li><li><a href="http://blog.csdn.net/lastsweetop/article/details/8992505#t1" rel="nofollow">hdfs不适用的场景</a></li><li><a href="http://blog.csdn.net/lastsweetop/article/details/8992505#t2" rel="nofollow">hdfs block</a></li><li><a href="http://blog.csdn.net/lastsweetop/article/details/8992505#t3" rel="nofollow">namenodes和datanodes</a></li><li><a href="http://blog.csdn.net/lastsweetop/article/details/8992505#t4" rel="nofollow">hdfs Federation</a></li><li><a href="http://blog.csdn.net/lastsweetop/article/details/8992505#t5" rel="nofollow">hdfs的高可用</a></li><li><a href="http://blog.csdn.net/lastsweetop/article/details/8992505#t6" rel="nofollow">failover和fencing</a></li></ol></div>
<div style="clear:both;"></div>
<div id="article_content" class="article_content">
<p>转载请注明出处：<a href="http://blog.csdn.net/lastsweetop/article/details/8992505" rel="nofollow">http://blog.csdn.net/lastsweetop/article/details/8992505</a></p>
<h1><a name="t0"></a>hdfs设计原则</h1>
<p><span style="font-family:'Times New Roman';font-size:12px;">1.非常大的文件：</span></p>
<p><span style="font-family:'Times New Roman';font-size:12px;">这里的非常大是指几百MB,GB,TB.雅虎的hadoop集群已经可以存储PB级别的数据</span></p>
<p><span style="font-family:'Times New Roman';font-size:12px;">2.流式数据访问：</span></p>
<p><span style="font-family:'Times New Roman';font-size:12px;">基于一次写，多次读。</span></p>
<p><span style="font-family:'Times New Roman';font-size:12px;">3.商用硬件：      </span></p>
<p><span style="font-family:'Times New Roman';font-size:12px;"> hdfs的高可用是用软件来解决，因此不需要昂贵的硬件来保障高可用性，各个生产商售卖的pc或者虚拟机即可。</span></p>
<h1><a name="t1"></a>hdfs不适用的场景</h1>
<div><span style="font-family:'Times New Roman';font-size:12px;">1.低延迟的数据访问   </span></div>
<div><span style="font-family:'Times New Roman';font-size:12px;">hdfs的强项在于大量的数据传输，递延迟不适合他，10毫秒以下的访问可以无视hdfs，不过hbase可以弥补这个缺陷。</span></div>
<div><span style="font-family:'Times New Roman';font-size:12px;"><br></span></div>
<div><span style="font-family:'Times New Roman';font-size:12px;">2.太多小文件              </span></div>
<div><span style="font-family:'Times New Roman';font-size:12px;"> namenode节点在内存中hold住了整个文件系统的元数据，因此文件的数量就会受到限制，每个文件的元数据大约150字节</span></div>
<div><span style="font-family:'Times New Roman';font-size:12px;"> 1百万个文件，每个文件只占一个block，那么就需要300MB内存。你的服务器可以hold住多少呢，你可以自己算算</span></div>
<div><span style="font-family:'Times New Roman';font-size:12px;"><br></span></div>
<div><span style="font-family:'Times New Roman';font-size:12px;">3.多处写和随机修改   </span></div>
<div><span style="font-family:'Times New Roman';font-size:12px;">目前还不支持多处写入以及通过偏移量随机修改</span></div>
<h1><a name="t2"></a>hdfs block</h1>
<div><span style="font-family:'Times New Roman';"><span style="font-size:12px;">为了最小化查找时间比例，hdfs的块要比磁盘的块大很多。</span>hdfs块的大小默认为64MB，和文件系统的块不同，</span></div>
<div><span style="font-family:'Times New Roman';font-size:12px;">hdfs的文件可以小于块大小，并且不会占满整个块大小。</span></div>
<div><span style="font-size:12px;"><span style="font-family:'Times New Roman';">查找时间在10ms左右，数据传输几率在100MB/s,为了使查找时间是传输时间的1%，块的大小必须在100MB左右</span></span></div>
<div><span style="font-family:'Times New Roman';font-size:12px;">一般都会设置为128MB</span></div>
<div><br></div>
<div><span style="font-family:'Times New Roman';font-size:12px;">有了块的抽象之后，hdfs有了三个优点：</span></div>
<div><span style="font-family:'Times New Roman';font-size:12px;"><br></span></div>
<div><span style="font-family:'Times New Roman';font-size:12px;">1.可以存储比单个磁盘更大的文件</span></div>
<div><span style="font-family:'Times New Roman';font-size:12px;">2.存储块比存储文件更加简单，每个块的大小都基本相同</span></div>
<div><span style="font-family:'Times New Roman';font-size:12px;">3.使用块比文件更适合做容错性和高可用</span></div>
<h1><a name="t3"></a><span style="font-family:'Times New Roman';">namenodes和datanodes</span></h1>
<div><span style="font-family:'Times New Roman';">hdfs集群有两种类型的节点，一种为master及namenode，另一种为worker及datanodes。</span></div>
<div><span style="font-family:'Times New Roman';font-weight:normal;"><br></span></div>
<div><span style="font-family:'Times New Roman';font-weight:normal;">namenode节点管理文件</span><span style="font-family:'Times New Roman';font-weight:normal;">系统的命名空间。</span><span style="font-family:'Times New Roman';font-weight:normal;">它包含一个文件系统的树，所有文件和目录</span><span style="font-family:'Times New Roman';font-weight:normal;">的原数据都在这个树上，这些</span></div>
<div><span style="font-family:'Times New Roman';font-weight:normal;">信息被存储在本地</span><span style="font-family:'Times New Roman';font-weight:normal;">磁盘的两个文件中，image文件和edit </span><span style="font-family:'Times New Roman';font-weight:normal;">log文件。文件相关的块存在哪个块中，块在哪个地方，这些</span></div>
<div><span style="font-family:'Times New Roman';font-weight:normal;">信息都是在</span><span style="font-family:'Times New Roman';font-weight:normal;">系统启动的时</span><span style="font-family:'Times New Roman';font-weight:normal;">候加载到namenode的内存中，并不会存储在磁盘中。</span></div>
<div><span style="font-family:'Times New Roman';font-weight:normal;"><br></span></div>
<div><span style="font-family:'Times New Roman';"><span style="font-weight:normal;">datanode节点在文件系统中充当的角色就是苦力，按照namenode和client的指令进行存储或者检索block，并且周期性</span></span></div>
<div><span style="font-family:'Times New Roman';"><span style="font-weight:normal;">的向namenode节点报告它存了哪些文件的block</span></span></div>
<div><span style="font-family:'Times New Roman';"><span style="font-weight:normal;"><br></span></span></div>
<div><span style="font-family:'Times New Roman';"><span style="font-weight:normal;">namenode节点如果不能使用了，那么整个hdfs就玩完了。为了防止这种情况，有两种方式可供选择</span></span></div>
<div><span style="font-family:'Times New Roman';"><span style="font-weight:normal;">1.namenode通过配置元数据可以写到多个磁盘中，最好是独立的磁盘，或者NFS.</span></span></div>
<div><span style="font-family:'Times New Roman';"><span style="font-weight:normal;">2.使用第二namenode节点，第二namenode节点平时并不作为namenode节点工作，它的主要工作内容就是定期根据编辑</span></span></div>
<div><span style="font-family:'Times New Roman';"><span style="font-weight:normal;">日志（edit log）</span></span><span style="font-family:'Times New Roman';"><span style="font-weight:normal;">合并命名</span></span><span style="font-family:'Times New Roman';font-weight:normal;">空间的镜像(namespace
 image),防止编辑日志过大，合并后的image它自己也保留一份，等着</span></div>
<div><span style="font-family:'Times New Roman';"><span style="font-weight:normal;">namenode节点挂掉，然后它可以转正，由于不是实时的，有数据上的损失是很可能发生的。</span></span></div>
<div><span style="font-family:'Times New Roman';"><span style="font-weight:normal;"><br></span></span></div>
<h1><a name="t4"></a><span style="font-family:'Times New Roman';">hdfs Federation</span></h1>
<div><span style="font-weight:normal;"><span style="font-family:'Times New Roman';">namenode节点保持所有的文件和块的引用在内存中，这就意味着在一个拥有很多很多文件的很大的集群中，内存就成为了一个</span></span></div>
<div><span style="font-family:'Times New Roman';"><span style="font-weight:normal;">限制的条件，hdfs federation在hadoop 2.x的被实现了，允许hdfs有多个namenode节点，每个管hdfs的一部分，比如一个管/usr，</span></span></div>
<div><span style="font-family:'Times New Roman';"><span style="font-weight:normal;">另一个管/home，每个namenode节点是相互隔离的，一个挂掉不会影响另外一个。</span></span></div>
<div><span style="font-family:'Times New Roman';"><span style="font-weight:normal;"><br></span></span></div>
<h1><a name="t5"></a>hdfs的高可用</h1>
<div><span style="font-weight:normal;"><span style="font-family:'Times New Roman';">不管namenode节点的备份还是第二namenode节点都只能保证数据的恢复，并不能保证hdfs的高可用性，一旦namenode节点挂掉</span></span></div>
<div><span style="font-family:'Times New Roman';"><span style="font-weight:normal;">就会产生单点故障，这时候要手动去数据备份恢复，或者启用第二节点，新的namenode节点在对外服务器要做三件事：</span></span></div>
<div><span style="font-family:'Times New Roman';"><span style="font-weight:normal;">1.把命名空间的镜像加载到内存中</span></span></div>
<div><span style="font-family:'Times New Roman';"><span style="font-weight:normal;">2.重新运行编辑日志</span></span></div>
<div><span style="font-family:'Times New Roman';"><span style="font-weight:normal;">3.接受各个datanode节点的block报告</span></span></div>
<div><span style="font-family:'Times New Roman';"><span style="font-weight:normal;">在一个大型一点的hdfs系统中，等这些做完需要30分钟左右。</span></span></div>
<div><span style="font-family:'Times New Roman';"><span style="font-weight:normal;"><br></span></span></div>
<div><span style="font-family:'Times New Roman';"><span style="font-weight:normal;">2.x已经支持了高可用性(HA)，通过一对namenode热备来实现，一台挂掉，备机马上提供无中断服务</span></span></div>
<div><span style="font-family:'Times New Roman';"><span style="font-weight:normal;">要实现HA,要做三点微调：</span></span></div>
<div><span style="font-family:'Times New Roman';"><span style="font-weight:normal;">1.namenode节点必须使用高可用的共享存储。</span></span></div>
<div><span style="font-family:'Times New Roman';"><span style="font-weight:normal;">2.datanode节点必须象两个namenode节点发送block报告</span></span></div>
<div><span style="font-family:'Times New Roman';"><span style="font-weight:normal;">3.客户端做改动可以在故障时切换到可用的namenode节点上，而且要对用户是无感知的</span></span></div>
<div><span style="font-family:'Times New Roman';"><span style="font-weight:normal;"><br></span></span></div>
<h1><a name="t6"></a><span style="font-family:'Times New Roman';">failover和fencing</span></h1>
<div><span style="font-family:'Times New Roman';font-weight:normal;">将备份namenode激活的过程就叫failover，管理激活备份namenode的系统叫做failover controller，</span></div>
<div><span style="font-family:'Times New Roman';font-weight:normal;">zookeeper就可以担当这样的角色，可以保证只有一个节点处于激活状态。</span></div>
<div><span style="font-family:'Times New Roman';font-weight:normal;">必须确认原来的namenode已经真的挂掉了，很多时候只是网络延迟，如果备份节点已经激活了，</span></div>
<div><span style="font-family:'Times New Roman';"><span style="font-weight:normal;">原来的节点又可以提供服务了，这样是不行的，防止原来namenode活过来的过程就叫fencing。</span></span></div>
<div><span style="font-family:'Times New Roman';"><span style="font-weight:normal;">可以用STONITH实现， STONITH可以做到直接断电把原namenode节点fencing掉</span></span></div>
<div><br></div>
<div><span style="font-family:'Times New Roman';font-size:12px;"><br></span></div>
<div><span style="font-family:'Times New Roman';">感谢Tom White,此文章大部分来自于大神的definitive guide，奈何中文版翻译太烂，就在英文原版的基础上和官方的一些文档加入一些自己的理解。</span></div>
<div><span style="font-family:'Times New Roman';">全当是读书笔记吧，画蛇添足之举。</span></div>
</div>
            </div>
                </div>