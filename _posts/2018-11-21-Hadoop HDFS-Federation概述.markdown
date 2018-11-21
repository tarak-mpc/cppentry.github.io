---
layout:     post
title:      Hadoop HDFS-Federation概述
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<strong>概述:</strong>HDFS  单Namenode节点的设计大大的简化了HDFS的实现, HDFSFederation 提出一种基于现有架构的Namenode水平扩展解决方案。
<p><strong>HDFS存储架构:</strong>整体上看HDFS主要由两层架构组成,HDFS  Federation 支持为一个HDFS集群配置多个Namenodes/namespaces</p>
<p align="center"></p>
<p> <img src="https://img-blog.csdn.net/20170806110655868?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMDgyMDcwMg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p>l  NameSpace</p>
<p>n  由目录、文件和多个块组成</p>
<p>n  支持所有与名称空间相关的所有操作:创建、删除、修改、文件列表和目录</p>
<p>l  Block Storage 由以下两部分构成</p>
<p>n  Block Management[块管理服务],在NameNode中运行</p>
<p>u  通过接收DataNode注册信息、周期性的心跳信息看,提供DataNode集群成员信息.</p>
<p>u  处理块报告以及维护数据块位置</p>
<p>u  支持块相关操作:创建、删除、修改和获取块位置</p>
<p>u  管理副本位置、进行块复制、删除冗余数据块</p>
<p>n  Storage [存储]</p>
<p>u  DataNode 提供,在本地文件系统读写文件。</p>
<p><strong>现有架构弊端</strong></p>
<p>l  Namenode 与Block Management高度耦合,通过增加Namenode并不能增加HDFS集群的吞吐量,而受限于单个Namenode的性能瓶颈</p>
<p>l  多用户场景下不能做到对用户或者应用程序的隔离</p>
<p>l  无限扩容Datanode有可能使得Namenode过载</p>
<p> </p>
<p><strong>多个Namenodes/Namespaces:</strong> Federation 使用多个独立的Namenodes/namespaces,多个Namenode之间是互相独立的不需要其它节点的协调,Datanodes 被所有的Nagenode所共享,每一个Datanode被注册到集群中的所有Namenodes中,Datanode周期性向所有Namenode发送心跳信息和块报告,Datanode同时响应来自不同Namenode的命令.</p>
<p>l  Block Pool,一个Block Pool是一个独立名称空间中的一组块的集合,Datanodes 为集群中所有的Block Pool存储数据块,每一个块池是独立管理的,允许一个namespaces生成块id而不需要其它namespaces的协助。一个Namenode失败不影响Datanode服务其它的Namenodes.</p>
<p>namespaces 和自己的block pool一起被称作Namespace Volume[卷名称空间],是一个独立的管理单元,当一个namespaces被删除则与之关联的块池在Datanode中也被删除.集群升级期间每个命名空间都作为一个独立的单元在集群中升级.</p>
<p>l  Cluster ID ,一个Cluster ID用来标识集群中的所有节点,当一个Namenode被格式化时候可以自动生成或者提供一个集群ID值,格式化其它Namenode时提供Cluster ID值使得当前Namenode加入指定集群.</p>
<img src="https://img-blog.csdn.net/20170806110704401?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMDgyMDcwMg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><p align="center"><strong></strong></p>
<p><strong>获益点</strong></p>
<p>l  名称空间的可伸缩性,水平扩展Namenode,大型集群或者大量小文件的部署可以从中受益允许将多个namespaces添加的集群中。</p>
<p>l  性能,文件系统的吞吐量不受单个Namenode节点的限制,向集群中添加更多的Namenode可以增加系统的吞吐量</p>
<p>l  独立,单个Namenode在多用户环境下不提供隔离,通过使用不同的namespaces可以将不同类别的应用程序和用户隔离到不同的namespace中</p>
            </div>
                </div>