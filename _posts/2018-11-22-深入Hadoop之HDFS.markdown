---
layout:     post
title:      深入Hadoop之HDFS
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/Angelo_wzp/article/details/74444941				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p><strong>HDFS来源</strong> <br>
    HDFS源自于Google的GFS论文，发表于2003年10月。HDFS是GFS的克隆版。 <br>
<strong>HDFS有以下特点</strong> <br>
    易于扩展的分布式文件系统 <br>
    运行在大量的普通廉价的机器上，提供容错机制 <br>
    文件分块存储，将一个完整的大文件平均分块存储到不同计算器上 <br>
    为大量的用户提供性能不错的文件存取服务 <br>
    流式数据访问 <br>
<strong>HDFS架构</strong> <br>
    <img src="https://img-blog.csdn.net/20170705145211092?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQW5nZWxvX3d6cA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""> <br>
<strong>NameNode</strong> <br>
    一个HDFS集群是由一个Namenode和一定数目的Datanodes组成。Namenode是一个中心服务器，负责管理文件系统的名字空间(namespace)以及客户端对文件的访问。 <br>
    NameNode是所有HDFS元数据的仲裁者和管理者。用户数据永远不会流过NameNode，只会询问NameNode用户数据和哪个DataNode联系。 <br>
    文件副本存放在哪个DataNode上有NameNode控制，NameNode会根据全局情况来做出块放置 决定，读取文件时NameNode尽量让用户先读取最近副本，减低块的消耗和读取延时。 <br>
    NameNode全权管理数据块的复制，它周期性地从集群的每个DataNode收集心跳信号和块状态报告。块状态报告包含了一个该DataNode上所有数据块的列表。 <br>
<strong>DataNode</strong> <br>
    集群中的Datanode一般是一个节点一个，负责管理它所在节点上的存储，包括两个文件，一个是数据本身，一个元数据包括数据的长度、块数据的校验和、及时间戳。 <br>
    DataNode启动后向NameNode注册，通过后，周期性地向NameNode上报所有的块信息。 <br>
    心跳没3秒一次，心跳返回结果带有NameNode给该DataNode的命令。如果10分钟没有收到某个DataNode的心跳，则认为该节点不可用。 <br>
    集群运行中可以安全加入退出一些机器。 <br>
<strong>NameNode和DataNode启动过程</strong> <br>
    启动NameNode。 <br>
    读取fsimage元数据镜像文件，加载到内存中。读取editlog日志文件，加载到内存中，使当前内存中元数据信息与上次关闭系统时保持一致。然后在磁盘上生成一份同内存中元数据镜像相同的fsimage文件，同时生成一个新的null的editlog文件用于记录以后的hdfs文件系统的更改。 <br>
    启动DataNode。 <br>
    DataNode向NameNode注册。 <br>
    向NameNode发送blockreport。 <br>
    启动成功后，client可以对HDFS进行目录创建、文件上传、下载、查看、重命名等操作，更改namespace的操作将被记录在editlog文件中。 <br>
    <img src="https://img-blog.csdn.net/20170705154137121?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQW5nZWxvX3d6cA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="NameNode启动过程" title=""></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>