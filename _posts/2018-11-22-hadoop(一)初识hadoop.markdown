---
layout:     post
title:      hadoop(一)初识hadoop
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-size:14px;"><img src="https://img-blog.csdn.net/20141030170531059?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbmlja2ZvdmVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" width="437" height="280" alt=""></span></p>
<p><span style="font-size:14px;">       HDFS:Hadoop Distributed File System,分布式文件系统,HDFS是一个高度容错性的系统，适合部署在普通的电脑上。HDFS能提供高吞吐量的数据访问，适合大规模数据集应用。<span style="color:rgb(51,51,51);font-family:'宋体';line-height:28px;text-indent:28px;background-color:rgb(248,248,248);">HDFS设计理念之一就是让它能运行在普通的硬件之上，即便硬件出现故障，也可以通过容错策略来保证数据的高可用。</span></span></p>
<p><span style="font-size:14px;"><br>
       Hadoop的核心机制是通过HDFS文件系统和MapReduce算法进行存储资源、内存和程序的有效利用与管理。通过Hadoop，可以轻易的将多台普通的或低性能的服务器组合成分布式的运算-存储集群，提供大数据量的存储和处理能力。<br><br><br>
MapReduce<br>
        Google提出此架构，MapReduce通过Map（映射）和Reduce（化简）来实现大规模数据（TB级）的并行计算。可以简单理解为，通过Map（映射）函数，把一组键值对映射成一组新的键值对；指定并发的Reduce（化简）函数，用来保证所有映射的键值对中的每一个共享相同的键组。<br>
       使用大量服务器来执行Map程序，并将待处理的庞大数据切割成很多的小份数据，由每台服务器分别执行Map程序来处理分配到的那一小段数据，接着再将每一个Map程序分析出来的结果，透过Reduce程序进行合并，最后则汇整出完整的结果。<br>
       MapReduce是Hadoop分布式计算的关键技术，MapReduce程序的执行过程如下： <br></span></p>
<p><span style="font-size:14px;"><img src="https://img-blog.csdn.net/20141030173731142?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbmlja2ZvdmVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" width="710" height="348" alt=""><br></span></p>
<p><span style="font-size:14px;">        MapReduce的运作方式就像快递公司一样。物流部门会将发往各地的包裹先运送到各地的物流分站，再由分站派出进行派送；快递员等每个包裹的用户签单后将数据反馈给系统汇总，完成整个快递流程。在这里，每个快递员都会负责配送，所执行的动作大致相同，且只负责少量的包裹，最后由物流公司的系统进行汇总。<br></span></p>
<p><span style="font-size:14px;">        在Hadoop集群架构中，服务器依据用途可分成Master节点和Worker节点，Master负责分配任务，而Worker负责执行任务。</span></p>
<p><span style="font-size:14px;">Hadoop中的Master与Worker(slave)<br><br><br>
Hadoop运算集群中的服务器依用途分成Master节点和Worker节点。</span></p>
<p><span style="font-size:14px;">         Master节点中含有JobTracker、NameNode、TaskTracker和DataNode程序；Worker节点含有TaskTracker和DataNode。<br></span></p>
<p><span style="font-size:14px;">        在Master节点的服务器中会执行两套程序：一个是负责安排MapReduce运算层任务的JobTracker，以及负责管理HDFS数据层的NameNode程序。<br>
        在Worker节点的服务器中也有两套程序：接受JobTracker指挥，负责执行运算层任务的是TaskTracker程序，与NameNode对应的则是DataNode程序，负责执行数据读写操作以及执行NameNode的副本策略。</span></p>
<p><span style="font-size:14px;"><img src="https://img-blog.csdn.net/20141031092926758?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbmlja2ZvdmVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></p>
<p><span style="font-size:14px;"><br>
        Master节点上的JobTracker程序会将Map和Reduce程序的执行工作指派给Worker服务器上的TaskTracker程序，由TaskTracker负责执行Map和Reduce工作，并将运算结果回复给Master节点上的JobTracker。<br>
        在HDFS数据层上，NameNode负责管理和维护HDFS的名称空间、并且控制档案的任何读写动作，同时NameNode会将要处理的数据切割成一个个档案区块（Block），每个区块是64MB，例如1GB的数据就会切割成16个档案区块。NameNode还会决定每一份档案区块要建立多少个副本，一般来说，一个档案区块总共会复制成3份，并且会分散储存到3个不同Worker服务器的DataNode程序中管理，只要其中任何一份档案区块遗失或损坏，NameNode会自动寻找位于其他DataNode上的副本来回复，维持3份的副本策略。</span><br></p>
<p><span style="font-size:14px;"><img src="https://img-blog.csdn.net/20141031092941952?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbmlja2ZvdmVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></p>
<p><span style="font-size:14px;">namenode、jobtracker可以与secondary namenode不在同一个节点上，但namenode和jobtracker是在同一台机器上。</span></p>
<p><span style="font-size:14px;"><br></span></p>
<p><span style="font-size:14px;">           在一套Hadoop集群中，分配MapReduce任务的JobTracker只有1个，而TaskTracker可以有很多个。同样地，负责管理HDFS文件系统的NameNode也只有一个，和JobTracker同样位于Master节点中，而DataNode可以有很多个。Master节点中除了有JobTracker和NameNode以外，也会有TaskTracker和DataNode程序，也就是说Master节点的服务器也可以在本地端扮演Worker角色的工作。</span></p>
<p><br></p>
            </div>
                </div>