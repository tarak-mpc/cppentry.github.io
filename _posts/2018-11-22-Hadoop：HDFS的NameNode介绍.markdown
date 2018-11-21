---
layout:     post
title:      Hadoop：HDFS的NameNode介绍
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p style="text-indent:24px;font-family:Simsun;font-size:13px;">
Hadoop入门教程：HDFS的NameNode介绍，HDFS采用Master/Slave架构。NameNode就是HDFS的Master架构。HDFS系统包括一个NameNode组件，主要负责HDFS文件系统的管理工作，具体包括名称空间(namespace)管理，文件Block管理。NameNode提供的是始终被动接收服务的server，主要有三类协议接口：</p>
<p style="text-indent:24px;font-family:Simsun;font-size:13px;">
ClientProtocol接口，提供给客户端，用于访问NameNode。它包含了文件的HDFS功能。和GFS一样，HDFS不提供POSIX形式的接口，而使用了一个私有接口。</p>
<p style="text-indent:24px;font-family:Simsun;font-size:13px;">
DataNodeProtocol接口，用于DataNode向NameNode通信。</p>
<p style="text-indent:24px;font-family:Simsun;font-size:13px;">
NameNodeProtocol接口，用于从NameNode到NameNode的通信。</p>
<p style="text-indent:24px;font-family:Simsun;font-size:13px;">
在HDFS内部，一个文件被分成一个或多个Block，这些Block存储在DataNode集合里，NameNode就负责管理文件Block的所有元数据信息，这些元数据信息主要为：</p>
<p style="text-indent:24px;font-family:Simsun;font-size:13px;">
“文件名数据块”映射</p>
<p style="text-indent:24px;font-family:Simsun;font-size:13px;">
“数据块DataNode列表”映射</p>
<p style="text-indent:24px;font-family:Simsun;font-size:13px;">
其中，“文件名数据块”保存在磁盘上进行持久化存储，需要注意的是NameNode上不保存“数据块DataNode列表”映射，该列表是通过DataNode上报给NameNode建立起来的。</p>
<p style="text-indent:24px;font-family:Simsun;font-size:13px;">
NameNode执行文件系统的名称空间(namespace)操作，例如打开、关闭、重命名文件和目录，同时决定文件数据块到具体DataNode节点的映射。</p>
<p style="text-indent:24px;font-family:Simsun;font-size:13px;">
和NameNode最相关的还有一个概念就是Secondary NameNode，其主要是定时对NameNode的数据snapshots进行备份，这样可尽量降低NameNode崩溃之后导致数据丢失的风险，其所做的工作就是从NameNode获得fsimage和edits后把两者重新合并发给NameNode，这样，既能减轻NameNode的负担又能安全地备份，一旦HDFS的Master架构失效，就可以借助Secondary NameNode进行数据恢复。<span style="font-size:12px;">来源：</span><a href="http://www.cuug.com.cn/" rel="nofollow" style="font-size:12px;">CUUG官网</a></p>
            </div>
                </div>