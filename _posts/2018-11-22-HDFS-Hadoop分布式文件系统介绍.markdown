---
layout:     post
title:      HDFS-Hadoop分布式文件系统介绍
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/mickeyty/article/details/70228988				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>一． 概要</p>
<p>Hadoop分布式文件系统，简称HDFS。是Apache Hadoop Core项目的一部分。适合运行在通用的硬件上的分布式文件系统。所谓通用的硬件也就是相对廉价的机器。一般没有什么特殊要求。HDFS能提供高吞吐量的数据访问，非常适合大规模数据集上的应用。而且HDFS是一个高度容错性的系统。</p>
<p>下面是HDFS的结构图。</p>
<p><img src="https://img-blog.csdn.net/20170418153816795?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbWlja2V5dHk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p></p>
<p>从上图可以看出， HDFS也是基于主从结构（master/slaver）构件，主要有以下几个部分组成。</p>
<p>-NameNode</p>
<p>-DataNode</p>
<p>-Sencondary NameNode</p>
<p>1.Namenode是一个中心服务器，负责管理文件系统的名字空间(namespace)以及客户端对文件的访问。一个HDFS系统只有一个NameNode。是所有HDFS元数据的仲裁者和管理者。</p>
<p>2.DataNode就是集群中的节点。负责管理它所在节点上的存储。HDFS暴露了文件系统的名字空间，用户能够以文件的形式在上面存储数据。从内部看，一个文件其实被分成一个或多个数据块，这些块存储在一组Datanode上。Namenode执行文件系统的名字空间操作，比如打开、关闭、重命名文件或目录。它也负责确定数据块到具体Datanode节点的映射。Datanode负责处理文件系统客户端的读写请求。在Namenode的统一调度下进行数据块的创建、删除和复制。</p>
<p>3.Sencondary NameNode是辅助NameNode工作的。NameNode将对文件系统的改动追加保存到本地文件系统上的一个日志文件（edits）。当一个NameNode启动时，它首先从一个映像文件（fsimage）中读取HDFS的状态，接着应用日志文件中的edits操作。然后它将新的HDFS状态写入（fsimage）中，并使用一个空的edits文件开始正常操作。因为NameNode只有在启动阶段才合并fsimage和edits，所以久而久之日志文件可能会变得非常庞大，特别是对大型的集群。日志文件太大的另一个副作用是下一次NameNode启动会花很长时间。
 Secondary NameNode定期合并fsimage和edits日志，将edits日志文件大小控制在一个限度下。因为内存需求和NameNode在一个数量级上，所以通常secondaryNameNode和NameNode运行在不同的机器上。</p>
<p>同时，Sencondary NameNode也可以作为NameNode的冷备份。</p>
<p><br></p>
<p>二．HDFS和传统的集中式数据库比较</p>
<p>主要有以下几点不同</p>
<pre>1.HDFS支持海量数据量存储，HDFS上的一个典型文件大小一般都在G字节至T字节。如果是RDBMS，当数据量达到一定限度之后查询处理速度会变得很慢  且对机器性能要求很高。</pre>
<pre>2.HDFS采用的是流式数据读取模式，而流式数据读取模式主要取决于数据的传输速率。RDBMS采用的传统的B树，RDBMS中使用的B树结构，受限制于寻址的比例。一般来说，对于大数据量的读取流式模式的效率要远远高于B树。</pre>
<pre>3.HDFS适合以批处理的方式处理需要分析的整个数据集。它适合一次性写入，多次的读取的应用。而且不支持事务。RDBMS适用于point query（点查询）和更新，数据集被建立索引后，数据库能够提供快速的数据检索和少量的数据更新。它更适合持续更新的数据集。</pre>
<pre> </pre>
<pre>三．HDFS的主要功能</pre>
<pre>1. 文件系统的名字空间 (namespace)</pre>
<pre>HDFS支持传统的层次型文件组织结构。用户或者应用程序可以创建目录，然后将文件保存在这些目录里。文件系统名字空间的层次结构和大多数现有的文件系统类似：用户可以创建、删除、移动或重命名文件。</pre>
<pre>Namenode负责维护文件系统的名字空间，任何对文件系统名字空间或属性的修改都将被Namenode记录下来。应用程序可以设置HDFS保存的文件的副本数目。文件副本的数目称为文件的副本系数，这个信息也是由Namenode保存的。
</pre>
<pre><code class="language-html">例如
$ bin/hadoop fs -mkdir -p /user/data/input →在HDFS上创建目录
$ bin/hadoop fs -put &lt;localfile path&gt; /user/data/input →拷贝文件到HDFS
$ bin/hadoop fs -cat /user/data/output/* →查看HDFS上的文件
</code></pre>
<pre> </pre>
<p align="left"></p>
<pre>2. 数据复制</pre>
<p></p>
<p align="left">HDFS被设计成能够在一个大集群中跨机器可靠地存储超大文件。它将每个文件存储成一系列的数据块，除了最后一个，所有的数据块都是同样大小的。为了容错，文件的所有数据块都会有副本。每个文件的数据块大小和副本系数都是可配置的。应用程序可以指定某个文件的副本数目。副本系数可以在文件创建的时候指定，也可以在之后改变。HDFS中的文件都是一次性写入的，并且严格要求在任何时候只能有一个写入者。</p>
<p align="left">Namenode全权管理数据块的复制，它周期性地从集群中的每个Datanode接收心跳信号和块状态报告(Blockreport)。接收到心跳信号意味着该Datanode节点工作正常。块状态报告包含了一个该Datanode上所有数据块的列表。</p>
<p><img src="https://img-blog.csdn.net/20170418154049626?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbWlja2V5dHk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p><br></p>
<p></p>
<p align="left"> </p>
<p align="left">☆副本存放策略</p>
<p>HDFS采用一种称为机架感知(rack-aware)的策略来改进数据的可靠性、可用性和网络带宽的利用率。大型HDFS实例一般运行在跨越多个机架的计算机组成的集群上，不同机架上的两台机器之间的通讯需要经过交换机。在大多数情况下，同一个机架内的两台机器间的带宽会比不同机架的两台机器间的带宽大。通过一个<a href="http://hadoop.apache.org/docs/r1.0.4/cn/cluster_setup.html#Hadoop%E7%9A%84%E6%9C%BA%E6%9E%B6%E6%84%9F%E7%9F%A5" rel="nofollow">机架感知</a>的过程，Namenode可以确定每个Datanode所属的机架id。一个简单没有优化的策略就是将副本存放在不同的机架上。这样可以有效防止当整个机架失效时数据的丢失，并且允许读数据的时候充分利用多个机架的带宽。这种策略设置可以将副本均匀分布在集群中，有利于当组件失效情况下的负载均衡。但是，因为这种策略的一个写操作需要传输数据块到多个机架，这增加了写的代价。HDFS的默认的存放策略是将一个副本存放在本地机架的节点上，一个副本放在同一机架的另一个节点上，最后一个副本放在不同机架的节点上。这种策略减少了机架间的数据传输，这就提高了写操作的效率。而且，为了降低整体的带宽消耗和读取延时，HDFS会尽量让读取程序读取离它最近的副本。如果在读取程序的同一个机架上有一个副本，那么就读取该副本。</p>
<p> </p>
<p></p>
<pre>3. 容错机制</pre>
<p></p>
<p>HDFS的主要目标就是即使在出错的情况下也要保证数据存储的可靠性。常见的三种出错情况是：Namenode出错, Datanode出错和网络割裂(network partitions)。</p>
<p>☆Datanode出错和网络割裂</p>
<p>每个Datanode节点周期性地向Namenode发送心跳信号。Datanode出错和网络割裂可能导致一部分Datanode跟Namenode失去联系。Namenode通过心跳信号的缺失来检测这一情况，并将这些近期不再发送心跳信号Datanode标记为宕机，不会再将新的<acronym>IO</acronym>请求发给它们。任何存储在宕机Datanode上的数据将不再有效。Datanode的宕机可能会引起一些数据块的副本系数低于指定值，Namenode不断地检测这些需要复制的数据块，一旦发现就启动复制操作。在下列情况下，可能需要重新复制：</p>
<p>　　　　◎某个Datanode节点失效</p>
<p>　　　　◎某个副本遭到损坏</p>
<p>　　　　◎文件的副本系数增大。</p>
<p>而且，从某个Datanode获取的数据块有可能是损坏的，损坏可能是由Datanode的存储设备错误、网络错误或者软件bug造成的。HDFS客户端软件实现了对HDFS文件内容的校验和(checksum)检查。当创建一个新的HDFS文件，会计算这个文件每个数据块的校验和，并将校验和作为一个单独的隐藏文件保存在同一个HDFS名字空间下。当客户端获取文件内容后，它会检验从Datanode获取的数据跟相应的校验和文件中的校验和是否匹配，如果不匹配，客户端可以选择从其他Datanode获取该数据块的副本。</p>
<p>☆Namenode出错</p>
<p>FsImage和Editlog是HDFS的核心数据结构。如果这些文件损坏了，整个HDFS实例都将失效。因而，Namenode可以配置成支持维护多个FsImage和Editlog的副本。任何对FsImage或者Editlog的修改，都将同步到它们的副本上。当Namenode重启的时候，它会选取最近的完整的FsImage和Editlog来使用。</p>
<p> </p>
<p>四．      HDFS的工作流程</p>
<p>分为写操作和读操作。先来看一下写操作</p>
写操作
<p><img src="https://img-blog.csdn.net/20170418154158422?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbWlja2V5dHk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p></p>
<p>客户端有一个FileA文件要上传到HDFS上。HDFS的副本系数为3.HDFS分布在3个机架上。</p>
<p> </p>
详细步骤分析<br><p>　　　　1.Client将FileA按默认块大小（64M）。分成两块，block1和Block2;<br>
　　　　2.向namenode通信请求上传文件FileA，namenode检查目标文件是否已存在，父目录是否存在<br>
　　　　3.namenode返回是否可以上传<br>
　　　　4.client请求第一个 block1该传输到哪些datanode服务器上<br>
　　　　5.namenode返回3个datanode服务器<br>
    　　　　Block1: host1,host4,host5<br>
　　　　6.client开始往host1上传第一个block（先从磁盘读取数据放到一个本地内存缓存），以packet为单位。<br>
　　　　7.host1接收完后，将第一个package发送给host4，同时client向host1发送第二个package；<br>
　　　　8.host4接收完第一个package后，发送给host5，同时接收host1发来的第二个package。<br>
　　　　9.以此类推，如图红线所示，直到将block1发送完毕。<br>
　　　　10.Host1,host4,host5向NameNode，host1向Client发送通知，说“消息发送完了”。如图黄线所示。<br>
　　　　11.client收到host1发来的消息后，向namenode发送消息，通知Block1操作完成。如图蓝线所示<br>
　　　　12.当一个block传输完成之后，client再次请求namenode上传block2。<br>
　　　　13.namenode返回3个datanode服务器<br>
    　　　　Block2: host3,host9,host8<br>
　　　　14.client开始往host3，host9和host8上传数据。流程同Block1.<br></p>
<p align="left">2.  读操作</p>
<img src="https://img-blog.csdn.net/20170418154300063?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbWlja2V5dHk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><p></p>
<p align="left">详细步骤分析</p>
<ol start="1" type="1"><li>跟namenode通信查询元数据，找到文件块所在的datanode服务器</li><li>挑选一台datanode（就近原则，然后随机）服务器，请求建立socket流</li><li>datanode开始发送数据</li><li>客户端以packet为单位接收，先在本地缓存，然后写入目标文件</li></ol><p align="left">五.应用场景</p>
<p>☆HDFS 提供高吞吐量应用程序数据访问功能，适合带有大型数据集的应用程序，以下是一些常用的应用场景。 </p>
<p>1.数据密集型并行计算：数据量极大，但是计算相对简单的并行处理，如大规模Web信息搜索； </p>
<p>2.计算密集型并行计算：数据量相对不是很大，但是计算较为复杂的并行计算，如3D建模与渲染、气象预报和科学计算； </p>
<p>3.数据密集与计算密集混合型的并行计算，如3D电影的渲染。 </p>
<p>☆HDFS在使用过程中有以下限制： </p>
<p>1.HDFS不适合大量小文件的存储，因NameNode将文件系统的元数据存放在内存中，因此存储的文件数目受限于NameNode的内存大小； </p>
<p>2.HDFS适用于高吞吐量，而不适合低时间延迟的访问； </p>
<p>3.流式读取的方式，不适合多用户写入一个文件（一个文件同时只能被一个客户端写），以及任意位置写入（不支持随机写）； </p>
4.HDFS更加适合写入一次，读取多次的应用场景。<br><br>            </div>
                </div>