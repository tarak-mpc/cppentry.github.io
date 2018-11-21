---
layout:     post
title:      hadoop学习笔记-HDFS原理
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>文章内容摘自书籍，互联网的博客的一些集合和个人的理解。</p>
<p><br></p>
<p><span style="font-size:18px;">HDFS原理</span></p>
<p><span style="font-size:14px;">hadoop提供了可靠的共享存储和分析的系统，HDFS实现存储，MapReduce实现分析处理，这两部分是hadoop的核心，</span><span style="font-size:14px;">由于HDFS是为了高数据吞吐量而优化的，是以高时间延迟为代价，所以要求低延迟的数据访问应用不适合在HDFS上运行。</span></p>
<p><span style="font-size:14px;"><br></span></p>
<p><span style="font-size:14px;">概念：</span></p>
<p><span style="font-size:14px;">HDFS和操作系统一样，也是按块来存储的，但块会比操作系统的的大的多，默认为64MB作为独立的存储单元，也可以自行设置（例如128MB），但和其他操作系统不同的是，小于一个块大小的文件不会占据整个块的空间，块的大小的设置影响寻址的时间。</span></p>
<p><span style="font-size:14px;"><br></span></p>
<p><span style="font-size:14px;">在一般情况下HDFS cluster主要包括一个NameNode和多个DataNode组成，大多数情况下，会有单独的一台机运行namenode实例，客户端通过一个可配置的TCP端口链接到NameNode，NameNode,dataNode之间通过远程过程调用RPC进行通信。</span></p>
<p style="text-align:center;"><span style="font-size:14px;"><img src="https://img-blog.csdn.net/20131016222429218?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc2hhbmd3ZW5f/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br></span></p>
<p style="text-align:center;"><span style="font-size:14px;">本图来自IBM</span></p>
<p><span style="font-size:14px;">在各节点的通信中，NameNode可以看作是datanode的管理者和仲裁者，它不会主动发起请求（防止负载过大），而是对客户端或者dataNode发起的请求进行响应，每个datanode都会维护一个开放的socket，支持客户端或者其他datanode进行读写操作，namenode知道该socket和主机的位置和端口。</span></p>
<p><br></p>
<p><span style="font-size:14px;">现在来解释每个名词的的作用</span></p>
<p><span style="font-size:14px;">NameNode：作用是管理文件系统的命名空间</span></p>
<p><span style="font-size:14px;">--将所有的文件和文件夹的数据保存在文件系统系统树中。</span></p>
<p><span style="font-size:14px;">--这些信息在硬盘上保存：命名空间镜像（fsimage 它是一种序列化的文件格式，不支持硬盘的直接修改）和修改日志（editlog）</span></p>
<p><span style="font-size:14px;">--保存文件包含的哪些数据块，分布在哪些节点。这些数据是从文件系统启动的时候，通过从数据节点向namenode发送的数据包获取的。</span></p>
<p><span style="font-size:14px;"><br></span></p>
<p><span style="font-size:14px;">DataNode：真正存储数据的地方</span></p>
<p><span style="font-size:14px;">--client可以向数据节点请求写入或者读取出数据块</span></p>
<p><span style="font-size:14px;">--周期性的向NameNode回报存储数据块的信息。</span></p>
<p><span style="font-size:14px;"><br></span></p>
<p><span style="font-size:14px;">secondary namenode（不是元数据出现问题的备用节点）</span></p>
<p><span style="font-size:14px;">--主要功能是周期性的将NameNode的fsimage和editlog进行合并，以防止日志文件过大</span></p>
<p><span style="font-size:14px;">--保留合并后的命名空间镜像文件，以防止数据节点失败的时候可以恢复。</span></p>
<p><span style="font-size:14px;"><br></span></p>
<p><span style="font-size:14px;">fsimage和editlog</span></p>
<p><span style="font-size:14px;">当文件系统客户端（client）进行写的操作的时候，首先会把它记录在修改它日志（editlog）的文件中</span></p>
<p><span style="font-size:14px;">在记录了修改日志后，NameNode节点文件系统那个内存中的数据结构</span></p>
<p><span style="font-size:14px;">每次写操作成功之前，修改日志都会同步到文件系统中</span></p>
<p><span style="font-size:14px;">当namenode数据节点失败后，最新的checkpoint会将fsimage加载到内存中，然后执行editlog的操作</span></p>
<p><span style="font-size:14px;">checkpoint的过程如下：</span></p>
<p><span style="font-size:14px;">-SNN通知NN生成新的日志文件，并把日志都写到editlog中</span></p>
<p><span style="font-size:14px;">-SNN用HTTP GET 的方法从NN那里获得faimage文件和editlog文件</span></p>
<p><span style="font-size:14px;">-SNN把fsimage文件加载到内存中，执行日志文件的操作，生成新的fsimage</span></p>
<p><span style="font-size:14px;">-SNN把用HTTP POST把fsimage传回NN中</span></p>
<p><span style="font-size:14px;">-NN提换fsimage，更新fstime文件，写入checkpoint时间</span></p>
<p><span style="font-size:14px;">这样NN的日志文件就不会很大了。</span></p>
<p><span style="font-size:14px;"><br></span></p>
<p><span style="font-size:14px;">了解完这些之后，来讲一下关于client如何写入数据和读取数据</span></p>
<p style="text-align:center;"><span style="font-size:14px;"><img src="https://img-blog.csdn.net/20131017003913875?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc2hhbmd3ZW5f/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br></span></p>
<p style="text-align:left;"><span style="font-size:14px;">这里只说说具体的流程（详细的流程博客在以下链接）：</span></p>
<p style="text-align:left;"><span style="font-size:14px;">关于写入数据：</span></p>
<p style="text-align:left;"><span style="font-size:14px;">-客户端首先会将数据进行分块，然后会向NN发送请求，询问数据块应该存放的位置</span></p>
<p style="text-align:left;"><span style="font-size:14px;">-NN就会返回一些每个数据块应该存放的列表，在默认情况下，数据会被复制三份，一份放在本机架上，另外的两份会放在另外的机架上（这里还分两种情况，不详细谈）</span></p>
<p style="text-align:left;"><span style="font-size:14px;">-在client获取到要存放机器的位置的列表后，就通过RPC和datanode相连，并向第一个DataNode写入数据</span></p>
<p style="text-align:left;"><span style="font-size:14px;">-写完数据后，第一个DataNode就会与第二个DataNode进行通信并写入，再由第二个写入到第三个</span></p>
<p style="text-align:left;"><span style="font-size:14px;">-client收到写入成功的消息后并通知NN</span></p>
<p style="text-align:left;"><span style="font-size:14px;">在这里1TB File = 3TB storage 3TB network traffic </span></p>
<p style="text-align:left;"><span style="font-size:14px;">DataNode会发送心跳包去告诉NN关于本台机上数据的状态</span></p>
<p style="text-align:left;"><span style="font-size:14px;">NN从这里报告中去建立数据块的元信息</span></p>
<p style="text-align:left;"><span style="font-size:14px;">如果在这时候，NN挂了，意味着HDFS也挂了</span></p>
<p style="text-align:left;"><span style="font-size:14px;"><br></span></p>
<p style="text-align:left;"><span style="font-size:14px;">关于读取数据：</span></p>
<p style="text-align:left;"><span style="font-size:14px;">client向NN获取每个数据块的DataNode的列表，并挑选每个列表的第一个DataNode</span></p>
<p style="text-align:left;"><span style="font-size:14px;">然后就读取数据</span></p>
<p style="text-align:left;"><span style="font-size:14px;"><br></span></p>
<p style="text-align:left;"><span style="font-size:14px;">关于client：</span></p>
<p style="text-align:left;"><span style="font-size:14px;">当客户端创建文件的时候，并没有立即将其发送给NN，而是将文件存储在本地的临时文件中，这个操作对用户来说是透明的</span></p>
<p style="text-align:left;"><span style="font-size:14px;">，当临时文件累计到达一个数据块的大小后，才会联系NN，NN才会将文件名插入到文件系统的层次结构中，然后分配一个数据块给它，返回DataNode的标志符给客户端，然后客户端才把数据写入到datanode上，当文件关闭的时候也会将剩余部分的文件上传到datanode上，告诉NN文件已将关闭，这是NN才将文件的创建操作提交到了HDFS，这种方式能够很好的减少网络的请求，避免了网络的拥堵，提高了吞吐量</span></p>
<p style="text-align:center;"><img src="https://img-blog.csdn.net/20131017012227937?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc2hhbmd3ZW5f/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br></p>
<p style="text-align:left;">最后修改日期：2013/10/16</p>
            </div>
                </div>