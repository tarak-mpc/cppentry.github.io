---
layout:     post
title:      Hadoop平台Hdfs分布式文件系统
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>HDFS（分布式文件系统）</p>
<p></p>
<p>1 架构简介</p>
<p>hdfs采用的是master/slave模型。</p>
<p>一个hdfs cluster包含一个NameNode和一些列的DataNode。</p>
<p>NameNode充当的是master的角色，主要负责管理hdfs文件系统，接受来自客户端的请求；</p>
<p>DataNode主要是用来存储数据文件，hdfs将一个文件分割成一个多这是多个的block，这些block可能存储在一个DataNode上或者是多个DataNode上。</p>
<p><img src="http://images.cnblogs.com/cnblogs_com/xuqiang/hadoop/hdfsarchitecture.gif" alt="" height="414" width="600"><br></p>
<p>基于上面的架构需求，hadoop采用了这种master/slave的架构，具体来说私有一下的几部分组成：</p>
<p>1. NameNode：基本上等同于Master的地位，复制控制底层文件的io操作，处理mapreduce任务等。</p>
<p>2. DataNode： 在slave机器上运行，负责实际的底层的文件的读写。如果客户端client程序发起了读hdfs上的文件的命令的话，那么首先将这些文件分成所谓的block，然后NameNode将告知client这些block数据是存储在那些DataNode上的，之后，client将直接和DataNode交互。</p>
<p>3. Secondary NameNode：该部分主要是定时对NameNode进行数据snapshots进行备份，这样尽量降低NameNode崩溃之后，导致数据的丢失。</p>
<p>4. JobTracker：该部分相当于在client program和hadoop之间的桥梁，在整个的hadoop系统中仅仅存在一个JobTracker的实例。<br></p>
<p>5. TaskTracker：TaskTracker主要是负责的是每个具体的任务task<br><br></p>
<p><img src="http://images.cnblogs.com/cnblogs_com/xuqiang/hadoop/hadoop_jobtracker_arch.png" alt="" height="364" width="462"> </p>
<p>2文件系统命名空间File System Namespace</p>
<p>hdfs支持传统文件系统的目录结构，应用程序能够创佳目录directory，在这些目录中存储文件，创建文件，移动文件remove file，重命名文件，但是不支持硬链接和软连接。 </p>
<p>3 数据复制<em>Data Replication</em></p>
<p>hdfs将一个发文件分割成block，然后将这些block存储到不同的DataNode中，那么如何保证如果一个DataNode死掉，保证数据的完整性，通常的技术就是进行数据的备份，hdfs同样使用的是这一策略。</p>
<p><img src="http://images.cnblogs.com/cnblogs_com/xuqiang/hadoop/hdfsdatanodes.gif" alt="" height="367" width="600"><br></p>
<div style="display:inline-block;">
<div>我们现在考虑系统启动时，NameNode首先进入SafeMode，在这种模式下是不进行数据的备份（拷贝的）的，DataNode向NameNode发送Heartbeat和Blockreport，从而使得NameNode得到在每个DataNode上存储的数据文件，然后NameNode检查那些block的备份镜像数量还未达到所需备份数量，那么NameNode将对这些blocks。</div>
</div>
<p><br></p>
<p>4 元数据持久化</p>
<p>hdfs使用日志机制将对文件系统的操作全部存储在一个日志文件中，同时将整个文件系统信息（<em>the mapping of blocks to files and file system properties</em>）映射成一个FsImage文件，该文件存储在NameNode主机的本地文件系统上。同时FsImage和Log支持multiple copies，这些hdfs保证这些备份文件的一致性。</p>
<p>5 信息交换协议</p>
<p>上面讲到“DataNode向NameNode发送Heartbeat和Blockreport”，这其中显然涉及到协议的问题，hdfs communication协议是构建在tcp/ip协议上的。客户端通过ClientProtocol协议和NameNode交换信息，NameNode通过DataNode Procotol协议和DataNode交换信息。</p>
<br><br>            </div>
                </div>