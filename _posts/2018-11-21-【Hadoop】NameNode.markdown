---
layout:     post
title:      【Hadoop】NameNode
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：原创文章，转载请注明出处					https://blog.csdn.net/dream361/article/details/60769452				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><strong><span style="font-size:18px;">一、背景介绍</span></strong></p>
<p>当数据集的大小超过一台独立的物理计算机的存储能力时，就有必要对它进行分区（partition)并存储到多台单独的计算机上。管理网络中跨多台计算机存储的文件系统称为分布式文件系统（distributed filesystem)。该系统架构于网络之上，势必引入网络编程的复杂性，因此分布式文件系统比普通文件系统更为复杂。Hadoop有一个称为HDFS的分布式文件系统，在非正式或旧文档中也会简称DFS。HDFS是Hadoop的旗舰级文件系统。</p>
<strong>HDFS特点</strong><br><p>以流式数据访问来存储超大文件，运行于商用硬件集群上。<br>
为优化高数据量访问，以高时间延迟为代价。<br>
对于低延迟的访问，HBase是更好的选择。<br>
文件系统的元数据存储在内存，记录在edits,映射在fsimage文件中,通过secondaryNameNode提高NameNode可靠性。<br>
不支持多个写入者的操作，也不支持在文件的任意位置修改。<br></p>
<p><br></p>
<p><strong><span style="font-size:18px;">二、NameNode</span></strong></p>
<p>整个文件系统的管理节点。它维护着整个文件系统的文件目录树，文件/目录的元信息和每个文件对应的数据块列表。接收用户的操作请求。<br>
文件包括<br>
fsimage:元数据镜像文件。存储某一时段NameNode内存元数据信息<br>
edits:操作日志文件<br>
fstime:保存最近一次checkpoint的时间<br>
这些文件保存在磁盘的文件系统中</p>
<p><strong>元数据管理</strong></p>
<p>在内存中保存metedata，用于处理“读请求”。到有“写请求”到来时，namenode会首先写editlog到磁盘，即向edits文件中写日志，成功返回后，才会修改内存，并且向客户端返回。Hadoop会维护一个fsimage文件，也就是namenode中metedata的镜像，但是fsimage不会随时与namenode内存中的metedata保持一致，而是每隔一段时间通过合并edits文件来更新内容。SecondaryNameNode就是用来合并fsimage和edits文件来更新NameNode的metedata的。</p>
<p><strong><span style="font-size:18px;">三、SecondaryNameNode</span></strong></p>
<p><strong>实现意义</strong><br>
HA的一个方案。但不支持热备。配置即可。默认在安装在NameNode节点上（不安全）。<br>
在检查点任务到来时，通知NameNode切换edits,然后从NameNode上下载元数据信息（fsimage,edits），然后把二者合并，生成新的fsimage，在本地保存，并将其推送到NameNode，替换旧的fsimage.<br></p>
<p><strong>检查点checkpoint设置</strong><br>
fs.checkpoint.period 指定两次checkpoint的最大时间间隔，默认3600秒。<br>
fs.checkpoint.size    规定edits文件的最大值，一旦超过这个值则强制checkpoint，不管是否到达最大时间间隔。默认大小是64M。<br></p>
<p><br></p>
<p><br></p>
<p><br></p>
<p><br></p>
<p><br></p>
<p><br></p>
<p><br></p>
            </div>
                </div>