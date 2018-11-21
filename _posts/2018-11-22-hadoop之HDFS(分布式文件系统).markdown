---
layout:     post
title:      hadoop之HDFS(分布式文件系统)
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>hadoop最自豪的两大支柱hdfs和map-reduce,让我们来了解hdfs。</p>

<p>Hadoop分布式文件系统(HDFS)被设计成适合运行在通用硬件上的分布式文件系统。它和现有的分布式文件系统有很多共同点。但同时，它和其他的分布式文件系统的区别也是很明显的。HDFS是一个高度容错性的系统，适合部署在廉价的机器上。HDFS能提供高吞吐量的数据访问，非常适合大规模数据集上的应用    。</p>

<p><strong><em><strong>**HDFS（分布式文件系统）设计基础与目标:**</strong></em></strong></p>

<p>a: 硬件错误是常态。因此需要冗余</p>

<pre><code>                                                                  冗余对hadoop来说不是额外功能，而是本身就需要的一个功能,这个很好理解,就是因为各个节点容易断掉,为了数据的安全性和可维护性,故需要冗余。
</code></pre>

<p>b:内存的不稳定，cpu过热，硬盘损坏等等，节点duan掉。 <br>
 c:流式数据访问。即数据批量读取而非随机读写，Hadoop擅长做的是数据分析，而不是事务处理</p>

<pre><code>hadoop很少处理oltp(偶尔随机性的读写一些数据)
</code></pre>

<p>d:大规模数据集 <br>
 e:简单一致性模型。为了降低系统复杂度，对文件采用一次性写多次读的逻辑设计，即文件时一经写入，关闭，就再也不能修改了</p>

<pre><code>              如果要修改,只能把原来的数据删除了,再插入。
</code></pre>

<p>f: 程序采用数据就近原则分配节点执行</p>

<p>(2)在全分布式的集群中，运行hadoop后，在网络分布的不同服务器上运行着一组守护进程，包括：</p>

<p>NameNode:名称节点</p>

<pre><code>      namenode节点非常的重要,它维护着文件系统树(filesystem tree)以及文件树中所有的文件和文件夹的元数据(metadata)
      a:管理文件系统的命名空间(最重要的工作)文件的名字，大小，元数据，文件在节点的情况
      b:记录每个文件数据块在各个Datanode的位置和副本信息
   当有节点要访问某个文件的时候，它会先访问namenode，获取文件的位置信息，然后和dataNode直接通讯获取数据块，（类似目录的作用）
      c:协调客户端对文件的访问
      d:记录命名空间内的改动或空间本身属性的改动
      e:Namenode使用事务日志记录HDFS元数据的变化，使用映像文件存储系统的命名空间，包括文件映射，文件属性等
事务日志用来记录hdfs元数据的变化
映像文件：存储文件系统的命名空间，包括数据块的映射
</code></pre>

<p>DataNode:数据节点</p>

<pre><code>      a:负责所在的物理节点的存储管理
      b:一次写入，多次读取(不修改)
      c:文件由数据块组成，典型的块的大小是64M
      d:数据块尽量散在集群各个节点以实现冗余的效果
</code></pre>

<p>Secondary NameNode:次名字节点</p>

<pre><code>     它是namenode的一个快照，会根据configuration中设置的值来决定多少时间周期性的去spap一下namenode，记录namenode中的metadata及其它数据,防止数据丢失而造成的影响。
</code></pre>

<p>JobTracker:作业跟踪节点</p>

<pre><code> JobTracker启动之后，会一直监听并接收来自各个TaskTracker发送的心跳信息，包括资源使用情况和任务运行情况等信息，JobTracker的主要功能：
 1.作业控制：在hadoop中每个应用程序被表示成一个作业，每个作业又被分成多个任务，JobTracker的作业控制模块则负责作业的分解和状态监控。*最重要的是状态监控：主要包括TaskTracker状态监控、作业状态监控和任务状态监控。主要作用：容错和为任务调度提供决策依据。2.资源管理。
</code></pre>

<p>TaskTraker:任务跟踪节点</p>

<pre><code>      TaskTracker是JobTracker和Task之间的桥梁：一方面，从JobTracker接收并执行各种命令：运行任务、提交任务、杀死任务等；另一方面，将本地节点上各个任务的状态通过心跳周期性汇报给JobTracker。TaskTracker与JobTracker和Task之间采用了RPC协议进行通信。主要的功能:汇报心跳,机器级别的信息,任务级别的信息
</code></pre>

<p><strong><em>HDFS的文件读取:</em></strong> <br>
<img src="https://img-blog.csdn.net/20150809150229470" alt="这里写图片描述" title=""> <br>
客户端（也就是集群中的某一个节点）要访问HDFS中的一个文件 <br>
        首先namenode收到请求，在自己的元数据中找到文件对应的位置，列成清单，往客户端中回送,当客户端拿到namenode回送回来的信息，eg：文件在节点1xx块，节点2xxx块，然后此客户端分别和datanode通讯请求数据块，获得数据。</p>

<p><strong><em>HDFS中写入文件的流程</em></strong> <br>
<img src="https://img-blog.csdn.net/20150809151954594" alt="这里写图片描述" title=""> <br>
客户端通知NameNode进行写入文件的操作，文件被拆分成块 <br>
（64MB），NameNode找到集群中在线的且有足够空间的DataNode的信息返回，然后客户端根据DataNode列表信息将数据块写入DataNode，写入一个数据块后进行流水线复制操作，DataNode通过复制管道将数据块复制2份到其他DataNode中去，实现数据冗余，复制完成后，更新NameNode中元数据信息，即DataNode告诉NameNode已完成数据块的创建工作，接着进行迭代过程直到所有数据块信息全部写入完毕。</p>

<p><strong><em>我想对于一个集群来说,它的可靠性才是人们首先关心的,毕竟如果可靠性很低的话,客户并不会冒这个风险去使用,稳定,在企业里才是放在第一位的.HDFS的可靠性非常的高,8大策略保证了数据的安全性和系统的可靠性。</em></strong></p>

<p><strong><em>a:冗余副本策略</em></strong></p>

<pre><code>    可以在hdfs-site.xml中设置复制因子指定副本数量
    所有数据库都有副本
    Datanode启动时，遍历本地文件系统，产生一份hdsf数据库和本地文件的对应关系列表(blockreport)汇报给namednode，namenode会将列表和自己的元数据记录的数据是否相符做出措施
   (冗余的数量过小，难保证数据安全，备份过多，会导致空间的利用率过低，影响速度，降低性能)
</code></pre>

<p><strong><em>b:机架策略</em></strong></p>

<pre><code>机架一般放在不同的机架上，机架间带宽要比机架内带宽要小.
HDFS的"机架感知",交换机之间的通讯问题。
节点间传递信息包，以感知是否存在同一个机架上。
一般在本地机架上存放一个副本，在其它机架上再存放别的副本，这样可以防止机架失效时丢失数据，也可以提高带宽利用率(冗余策略)。
这样的话防止整个机架失败的时候，数据找不到冗余了隐患。
</code></pre>

<p><strong><em>c:心跳机制</em></strong></p>

<pre><code>    Namenode周期性的从datanode接收心跳信号和块报告。
    数据节点会周期性的向Namenode发送blockreport，向它在本地文件系统中数据块和文件的对应关系。
    Namenode会根据接收到的心跳信号和快报告验证元数据有没失效，如果不一致，则进行相应的修正和冗余的策略。
    Namenode根据块报告验证元数据。
    没有按时发送心跳的datanode会被标记为宕机，不会再给它任何的I/O请求。
    如果datanode失效造成的副本数量下降，并且低于预先设计的阀值（在那个地方设置的阈值），Namenode会检测出这些数据块，并在合适的时机进行重新复制。
    引发重新复制的原因还包括数据副本本身损坏，磁盘错误，复制因子被增大等。
</code></pre>

<p><strong><em>d:安全模式</em></strong></p>

<pre><code>    Namenode启动时会先经过一个"安全模式"阶段。
    安全模式阶段不会产生数据写。
    在此阶段Namenode手机各个datanode的报告(blockreport)，当数据块达到最小副本以上时，会被认为是"安全"的。
    在一定比例(可设置)的数据块被确定为"安全"后，再过若干时间，安全模式结束。
    当检测到副本数不足数据块，该快会被直接复制到达最小副本数。
</code></pre>

<p><strong><em>e:校验和</em></strong></p>

<pre><code>    在文件创立时，每个数据块产生校验和。
    校验和会作为单独的一个隐藏文件保存在命名空间下。
    客户端获取数据时可以检查校验是否相同，从而发现数据块是否损坏。
    如果正在读取的数据块损坏，则可以继续读取其它的副本如果发现错误，则在快报告中向namenode汇报，并做出相应的措施
</code></pre>

<p><strong><em>f:回收站</em></strong></p>

<pre><code>    删除文件时，其实是放入了回收站/trash。
    回收站里的文件可以快速恢复。
    可以设置一个时间阀值，当回收站里文件的存放时间超过这个阀值，就会被彻底的删除，并且释放占用的数据块。
</code></pre>

<p><strong><em>g:元数据保护</em></strong></p>

<pre><code>    映像文件和事务日志是namenode的核心数据，可以配置为拥有多个副本。
    副本会降低Namenode的处理速度，但增加安全性。
    Namenode依然是单点，如果法神故障要手工切换。
</code></pre>

<p><strong>h:快照机制</strong></p>

<p>可能随着数据越来越大,现有的节点已经不能满足当前需求,需要新增加节点以扩充加快的速度,那么怎么增加新的点呢,只能去停掉整个集群然后操作吗？那其实也可以的,只要你的费用支持的起。 <br>
<strong><em>一般来说直接冲新的PC机器上完成以下步骤就好</em></strong></p>

<pre><code>a:在新节点上安装好hadoop
b:把namenode的有关配置文件复制到该节点
c:修改master和slaves文件，增加节点(每一个节点都要改，采用脚本技术)
d:设置ssh免密码登陆该节点
e:单独启动该节点上的datanode和tasktracker(hadoop-daemon.sh start datanode/tasktracker)
f:运行start-balancer.sh进行数据负载均衡
</code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>