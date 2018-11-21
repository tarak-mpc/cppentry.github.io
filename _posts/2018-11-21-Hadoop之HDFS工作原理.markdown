---
layout:     post
title:      Hadoop之HDFS工作原理
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h1>HDFS特点</h1>
<p>HDFS（Hadoop Distributed File System ）Hadoop分布式文件系统。<br></p>
<p>HDFS有如下特点：<br></p>
<ol><li>保存多个副本，且提供容错机制，副本丢失或宕机自动恢复。默认存3份。</li><li>运行在廉价的机器上。</li><li>适合大数据的处理。HDFS默认会将文件分割成block，64M为1个block。然后将block按键值对存储在HDFS上，并将键值对的映射存到内存中。如果小文件太多，那内存的负担会很重。</li><li>硬件错误是常态，而非异常情况， HDFS可能是有成百上千的 server组成，任何一个组件都有可能一直失效，因此错误检测和快速、自动的恢复是 HDFS的核心架构目标。</li><li>跑在 HDFS上的应用与一般的应用不同，它们主要是以流式读为主，做批量处理；比之关注数据访问的低延迟问题，更关键的在于数据访问的高吞吐量。 </li><li>HDFS 以支持大数据集合为目标，一个存储在上面的典型文件大小一般都在千兆至 T字节，一个单一 HDFS实例应该能支撑数以千万计的文件。</li><li>HDFS 应用对文件要求的是 write-one-read-many访问模型。一个文件经过创建、写，关闭之后就不需要改变。这一假设简化了数据一致性问题，使高吞吐量的数据访问成为可能。典型的如 MapReduce框架，或者一个 web crawler应用都很适合这个模型。 </li><li>移动计算的代价比之移动数据的代价低。一个应用请求的计算，离它操作的数据越近就越高效，这在数据达到海量级别的时候更是如此。将计算移动到数据附近，比之将数据移动到应用所在显然更好， HDFS提供给应用这样的接口。<br></li></ol><p></p>
<h1>名词解译</h1>
<p style="text-align:center;"><img src="https://img-blog.csdn.net/20170620140414334?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbHh5X3Rz/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p>如上图所示，HDFS也是按照Master和Slave的结构。分NameNode、SecondaryNameNode、DataNode这几个角色。<br>
NameNode：是Master节点，管理数据块映射（文件目录、文件和block的对应关系以及block和datanode的对应关系）；处理客户端的读写请求；配置副本策略；管理HDFS的名称空间。<br>
SecondaryNameNode：分担namenode的工作量，是NameNode的冷备份；合并fsimage和fsedits然后再发给namenode。<br>
热备份：b是a的热备份，如果a坏掉。那么b马上运行代替a的工作。<br>
冷备份：b是a的冷备份，如果a坏掉。那么b不能马上代替a工作。但是b上存储a的一些信息，减少a坏掉之后的损失。<br>
fsimage:元数据镜像文件（文件系统的目录树。）<br>
edits：元数据的操作日志（针对文件系统做的修改操作记录）<br>
namenode内存中存储的是=fsimage+edits。<br>
SecondaryNameNode负责定时默认1小时，从namenode上，获取fsimage和edits来进行合并，然后再发送给namenode。减少namenode的工作量。<br></p>
<p>DataNode：Slave节点，负责存储client发来的数据块block；执行数据块的读写操作，大部分容错机制都是在datanode上实现的。<br></p>
<p>block：数据块，大文件会被分割成多个block进行存储，block大小默认为64MB。每一个block会在多个datanode上存储多份副本，默认是3份。<br></p>
<h1>工作原理</h1>
<h2>写操作</h2>
<div style="text-align:center;"><img src="https://img-blog.csdn.net/20170620141051436?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbHh5X3Rz/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></div>
<div>HDFS运行在跨越大量机架的集群之上。两个不同机架上的节点是通过交换机实现通信的，在大多数情况下，相同机架上机器间的网络带宽优于在不同机架上的机器。</div>
<div>默认的HDFS block放置策略在最小化写开销和最大化数据可靠性、可用性以及总体读取带宽之间进行了一些折中。一般情况下复制因子为3，HDFS的副本放置策略是将第一个副本放在本地节点，将第二个副本放到本地机架上的另外一个节点而将第三个副本放到不同机架上的节点。<br></div>
<div>HDFS所有的通信协议都是在TCP/IP协议之上构建的。一个客户端和指定TCP配置端口的名字节点建立连接之后，它和名字节点之间通信的协议是Client Protocal。数据节点和名字节点之间通过Datanode Protocol通信。<br></div>
<div><br></div>
<div>如上图所示，有一个文件FileA，100M大小。Client将FileA写入到HDFS上，HDFS按默认配置。<br>
HDFS分布在三个机架上Rack1，Rack2，Rack3。<br></div>
<div><br></div>
<div>a. Client将FileA按64M分块。分成两块，block1和Block2;<br>
b. Client向nameNode发送写数据请求，如图<span style="background-color:rgb(51,102,255);"><span style="color:#ffffff;">蓝色虚线</span></span>①------&gt;。<br>
c. NameNode节点，记录block信息。并返回可用的DataNode，如<span style="color:#ffffff;background-color:rgb(255,102,102);">粉色虚线</span>②---------&gt;。<br>
    Block1: host2,host1,host3<br>
    Block2: host7,host8,host4<br></div>
<div><br></div>
<div>    原理：<br>
        NameNode具有RackAware机架感知功能，这个可以配置。<br>
        若client为DataNode节点，那存储block时，规则为：副本1，同client的节点上；副本2，不同机架节点上；副本3，同第二个副本机架的另一个节点上；其他副本随机挑选。<br>
        若client不为DataNode节点，那存储block时，规则为：副本1，随机选择一个节点上；副本2，不同副本1，机架上；副本3，同副本2相同的另一个节点上；其他副本随机挑选。<br></div>
<div><br></div>
<div>d. client向DataNode发送block1；发送过程是以流式写入。</div>
<div><br>
    流式写入过程：<br>
        1&gt;将64M的block1按64k的package划分;<br>
        2&gt;然后将第一个package发送给host2;<br>
        3&gt;host2接收完后，将第一个package发送给host1，同时client想host2发送第二个package；<br>
        4&gt;host1接收完第一个package后，发送给host3，同时接收host2发来的第二个package。<br>
        5&gt;以此类推，如图<span style="background-color:rgb(204,0,0);"><span style="color:#ffffff;">红线实线</span></span>所示，直到将block1发送完毕。<br>
        6&gt;host2,host1,host3向NameNode，host2向Client发送通知，说“消息发送完了”。如图<span style="color:#ffffff;background-color:rgb(204,102,204);">紫色实线</span>所示。<br>
        7&gt;client收到host2发来的消息后，向namenode发送消息，说我写完了。这样就真完成了。如图<span style="color:#ffffff;background-color:rgb(204,153,51);">黄色粗实线</span><br>
        8&gt;发送完block1后，再向host7，host8，host4发送block2，如图<span style="background-color:rgb(51,51,255);"><span style="color:#ffffff;">蓝色实线</span></span>所示。<br>
        9&gt;发送完block2后，host7,host8,host4向NameNode，host7向Client发送通知，如图<span style="color:#ffffff;background-color:rgb(0,153,0);">绿色实线</span>所示。<br>
        10&gt;client向NameNode发送消息，说我写完了，如图<span style="color:#ffffff;background-color:rgb(204,153,51);">黄色粗实线</span>。。。这样就完毕了。<br><br>
分析，通过写过程，我们可以了解到：<br><ol><li>写1T文件，我们需要3T的存储，3T的网络流量带宽。</li><li>在执行读或写的过程中，NameNode和DataNode通过HeartBeat进行保存通信，确定DataNode活着。如果发现DataNode死掉了，就将死掉的DataNode上的数据，放到其他节点去。读取时，要读其他节点去。</li><li>挂掉一个节点，没关系，还有其他节点可以备份；甚至，挂掉某一个机架，也没关系；其他机架上，也有备份。</li></ol></div>
<h2>读操作</h2>
<div style="text-align:center;"><img src="https://img-blog.csdn.net/20170620141635676?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbHh5X3Rz/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></div>
<div><br>
如图所示，client要从datanode上，读取FileA。而FileA由block1和block2组成。  <br><br>
那么，读操作流程为：<br>
a. client向namenode发送读请求。<br>
b. namenode查看Metadata信息，返回fileA的block的位置。<br>
    block1:host2,host1,host3<br>
    block2:host7,host8,host4<br>
c. block的位置是有先后顺序的，先读block1，再读block2。而且block1去host2上读取；然后block2，去host7上读取；<br>
上面例子中，client位于机架外，那么如果client位于机架内某个DataNode上，例如,client是host6。那么读取的时候，遵循的规律是：<strong>优选读取本机架上的数据</strong>。<br></div>
            </div>
                </div>