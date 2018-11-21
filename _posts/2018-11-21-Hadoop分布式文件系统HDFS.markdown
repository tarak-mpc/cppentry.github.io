---
layout:     post
title:      Hadoop分布式文件系统HDFS
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：原创博客,微信:18518215883					https://blog.csdn.net/G290095142/article/details/78255086				</div>
								            <div id="content_views" class="markdown_views prism-tomorrow-night">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2 id="参考博客"><strong>参考博客</strong></h2>

<p><a href="http://developer.51cto.com/art/201603/507440.htm" rel="nofollow" target="_blank">还不懂HDFS的工作原理？快来扫扫盲</a> <br>
<a href="http://developer.51cto.com/art/201301/376472.htm" rel="nofollow" target="_blank">HDFS中的Namenode和Datanode</a> <br>
<a href="http://www.cnblogs.com/Dhouse/p/6901028.html" rel="nofollow" target="_blank">HDFS概述（2）————Block块大小设置</a> <br>
<a href="https://my.oschina.net/crxy/blog/348868" rel="nofollow" target="_blank">HDFS详解</a></p>



<h2 id="目录"><strong>目录</strong></h2>

<ul>
<li>HDFS体系结构的组成</li>
<li>HDFS特点</li>
<li>HDFS存储原理</li>
<li>常用命令</li>
<li>需要注意的问题</li>
</ul>

<h2 id="hdfs结构的组成">HDFS结构的组成</h2>

<p>HDFS体系结构中有两类节点，一类是NameNode，又叫”元数据节点”；另一类是DataNode，又叫”数据节点”。这两类节点分别承担Master和Worker具体任务的执行节点。</p>

<ul>
<li>NameNode，元数据节点</li>
</ul>

<blockquote>
  <p>元数据节点用来管理文件系统的命名空间 <br>
  其将所有的文件和文件夹的元数据保存在一个文件系统树中。 <br>
  这些信息也会在硬盘上保存成以下文件：命名空间镜像(namespace image)及修改日志(edit log)</p>
  
  <p>其还保存了一个文件包括哪些数据块，分布在哪些数据节点上。然而这些信息并不存储在硬盘上，而是在系统启动的时候从数据节点收集而成的。 <br>
  Namenode记录着每个文件中各个块所在的数据节点的位置信息，但是他并不持久化存储这些信息，因为这些信息会在系统启动时从数据节点重建。</p>
</blockquote>

<p>Namenode结构图课抽象为如图： <br>
<img src="http://static.oschina.net/uploads/img/201411/26103128_VcEb.png" alt="这里写图片描述" title=""> <br>
VERSION文件是java properties文件，保存了HDFS的版本号。</p>

<ul>
<li>Datanode，数据节点</li>
</ul>

<blockquote>
  <p>数据节点是文件系统中真正存储数据的地方。</p>
  
  <p>客户端(client)或者元数据信息(namenode)可以向数据节点请求写入或者读出数据块。</p>
  
  <p>其周期性的向元数据节点回报其存储的数据块(block)信息。 </p>
</blockquote>

<p>数据节点的目录结构： <br>
<img src="http://static.oschina.net/uploads/img/201411/26103128_G3Ch.png" alt="这里写图片描述" title=""></p>

<ul>
<li>secondary namenode，从元数据节点</li>
</ul>

<blockquote>
  <p>从元数据节点并不是元数据节点出现问题时候的备用节点，它和元数据节点负责不同的事情。</p>
  
  <p>其主要功能就是周期性将元数据节点的命名空间镜像文件和修改日志合并，以防日志文件过大。这点在下面会相信叙述。</p>
  
  <p>合并过后的命名空间镜像文件也在从元数据节点保存了一份，以防元数据节点失败的时候，可以恢复。</p>
</blockquote>

<p>文件系统命名空间映像文件及修改日志： <br>
 <img src="https://img-blog.csdn.net/20171016222855107?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvRzI5MDA5NTE0Mg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>



<h2 id="特点">特点</h2>

<blockquote>
  <p>4、HDFS的优缺点</p>
  
  <p>4.1 HDFS的优点</p>
  
  <p>　　1）处理超大文件</p>
  
  <p>　　这里的超大文件通常是指百MB、设置数百TB大小的文件。目前在实际应用中，HDFS已经能用来存储管理PB级的数据了。</p>
  
  <p>　　2）流式的访问数据</p>
  
  <p>　　HDFS的设计建立在更多地响应”一次写入、多次读写”任务的基础上。这意味着一个数据集一旦由数据源生成，就会被复制分发到不同的存储节点中，然后响应各种各样的数据分析任务请求。在多数情况下，分析任务都会涉及数据集中的大部分数据，也就是说，对HDFS来说，请求读取整个数据集要比读取一条记录更加高效。</p>
  
  <p>　　3）运行于廉价的商用机器集群上</p>
  
  <p>　　Hadoop设计对硬件需求比较低，只须运行在低廉的商用硬件集群上，而无需昂贵的高可用性机器上。廉价的商用机也就意味着大型集群中出现节点故障情况的概率非常高。这就要求设计HDFS时要充分考虑数据的可靠性，安全性及高可用性。</p>
  
  <p>4.2 HDFS的缺点</p>
  
  <p>　　1）不适合低延迟数据访问</p>
  
  <p>　　如果要处理一些用户要求时间比较短的低延迟应用请求，则HDFS不适合。HDFS是为了处理大型数据集分析任务的，主要是为达到高的数据吞吐量而设计的，这就可能要求以高延迟作为代价。</p>
  
  <p>　　改进策略：对于那些有低延时要求的应用程序，HBase是一个更好的选择。通过上层数据管理项目来尽可能地弥补这个不足。在性能上有了很大的提升，它的口号就是goes <br>
  real <br>
  time。使用缓存或多master设计可以降低client的数据请求压力，以减少延时。还有就是对HDFS系统内部的修改，这就得权衡大吞吐量与低延时了，HDFS不是万能的银弹。</p>
  
  <p>　　2）无法高效存储大量小文件</p>
  
  <p>　　因为Namenode把文件系统的元数据放置在内存中，所以文件系统所能容纳的文件数目是由Namenode的内存大小来决定。一般来说，每一个文件、文件夹和Block需要占据150字节左右的空间，所以，如果你有100万个文件，每一个占据一个Block，你就至少需要300MB内存。当前来说，数百万的文件还是可行的，当扩展到数十亿时，对于当前的硬件水平来说就没法实现了。还有一个问题就是，因为Map <br>
  task的数量是由splits来决定的，所以用MR处理大量的小文件时，就会产生过多的Maptask，线程管理开销将会增加作业时间。举个例子，处理10000M的文件，若每个split为1M，那就会有10000个Maptasks，会有很大的线程开销；若每个split为100M，则只有100个Maptasks，每个Maptask将会有更多的事情做，而线程的管理开销也将减小很多。</p>
  
  <p>　　改进策略：要想让HDFS能处理好小文件，有不少方法。</p>
  
  <p>　　3）不支持多用户写入及任意修改文件</p>
  
  <p>　　在HDFS的一个文件中只有一个写入者，而且写操作只能在文件末尾完成，即只能执行追加操作。目前HDFS还不支持多个用户对同一文件的写操作，以及在文件任意位置进行修改。</p>
</blockquote>



<h2 id="hdfs运行的体系结构"><strong>HDFS运行的体系结构</strong></h2>

<p>HDFS是一个主/从（Mater/Slave）体系结构，从最终用户的角度来看，它就像传统的文件系统一样，可以通过目录路径对文件执行CRUD（Create、Read、Update和Delete）操作。但由于分布式存储的性质，HDFS集群拥有一个NameNode和一些DataNode。NameNode管理文件系统的元数据，DataNode存储实际的数据。客户端通过同NameNode和DataNodes的交互访问文件系统。客户端联系NameNode以获取文件的元数据，而真正的文件I/O操作是直接和DataNode进行交互的。 <br>
<img src="http://static.oschina.net/uploads/img/201411/26103129_oAFO.jpg" alt="这里写图片描述" title=""></p>

<p>HFDS写过程示意图： <br>
<img src="http://s4.51cto.com/wyfs02/M00/7D/84/wKiom1bqDtGDu-V_AAA_3_RNbC0257.jpg-s_3456254316.jpg" alt="这里写图片描述" title=""></p>

<blockquote>
  <p>第一步：客户端发消息给NameNode，说要将“zhou.log”文件写入。(如图1中的①) <br>
  第二步：NameNode发消息给客户端，叫客户端写到DataNode A、B和D，并直接联系DataNode B。(如图1中的②) <br>
  第三步：客户端发消息给DataNode B，叫它保存一份“zhou.log”文件，并且发送一份副本给DataNode A和DataNode <br>
  D。(如图1中的③) 第四步：DataNode B发消息给DataNode <br>
  A，叫它保存一份“zhou.log”文件，并且发送一份副本给DataNode D。(如图1中的④) 第五步：DataNode <br>
  A发消息给DataNode D，叫它保存一份“zhou.log”文件。(如图1中的⑤) 第六步：DataNode <br>
  D发确认消息给DataNode A。(如图1中的⑤) 第七步：DataNode A发确认消息给DataNode B。(如图1中的④) <br>
  第八步：DataNode B发确认消息给客户端，表示写入完成。(如图1中的⑥)</p>
</blockquote>

<p>HDFS读过程图： <br>
<img src="http://s4.51cto.com/wyfs02/M01/7D/81/wKioL1bqD2rgGgcYAAA3T_QyGnU743.jpg-s_178694203.jpg" alt="这里写图片描述" title=""></p>

<blockquote>
  <p>第一步：客户端询问NameNode它应该从哪里读取文件。(如图2中的①) <br>
  第二步：NameNode发送数据块的信息给客户端。(数据块信息包含了保存着文件副本的DataNode的IP地址，以及DataNode在本地硬盘查找数据块所需要的数据块ID。) (如图2中的②) <br>
  第三步：客户端检查数据块信息，联系相关的DataNode，请求数据块。(如图2中的③) <br>
  第四步：DataNode返回文件内容给客户端，然后关闭连接，完成读操作。(如图2中的④)</p>
</blockquote>

<h2 id="hdfs命令">HDFS命令</h2>

<p>先说一下”hadoop fs 和hadoop dfs的区别”，看两本Hadoop书上各有用到，但效果一样，求证与网络发现下面一解释比较中肯。</p>

<p>　　粗略的讲，fs是个比较抽象的层面，在分布式环境中，fs就是dfs，但在本地环境中，fs是local file system，这个时候dfs就不能用。</p>

<ul>
<li>hadoop fs -ls</li>
<li>hadoop fs –ls input</li>
<li>hadoop fs –put ~/file test  <br>
上传文件到HDFS，此命令将/home/hadoop目录的file文件传输到HDFS上并重命名为test</li>
<li>hadoop fs –get output getout <br>
通过”-get 文件1 文件2”命令将HDFS中的”output”文件复制到本地系统并命名为”getout”</li>
<li>hadoop fs –rmr newoutput <br>
通过”-rmr 文件”命令删除HDFS下名为”newoutput”的文档</li>
<li>hadoop fs -cat input/* <br>
通过”-cat 文件”命令查看HDFS下input文件中内容</li>
<li>hadoop dfsadmin -report <br>
通过”-report”命令如何查看HDFS的基本统计信息</li>
<li>hadoop dfsadmin –safemode leave <br>
退出安全模式</li>
<li>hadoop dfsadmin –safemode enter <br>
进入安全模式</li>
</ul>



<h2 id="需要注意的问题">需要注意的问题</h2>

<ul>
<li>Namenode容错机制</li>
</ul>

<blockquote>
  <p>没有Namenode，HDFS就不能工作。事实上，如果运行namenode的机器坏掉的话，系统中的文件将会完全丢失，因为没有其他方法能够将位于不同datanode上的文件块(blocks)重建文件。因此，namenode的容错机制非常重要，Hadoop提供了两种机制。</p>
  
  <p>第一种方式是将持久化存储在本地硬盘的文件系统元数据备份。Hadoop可以通过配置来让Namenode将他的持久化状态文件写到不同的文件系统中。这种写操作是同步并且是原子化的。比较常见的配置是在将持久化状态写到本地硬盘的同时，也写入到一个远程挂载的网络文件系统。</p>
  
  <p>第二种方式是运行一个辅助的Namenode(Secondary Namenode)。 事实上Secondary <br>
  Namenode并不能被用作Namenode它的主要作用是定期的将Namespace镜像与操作日志文件(edit <br>
  log)合并，以防止操作日志文件(edit log)变得过大。通常，Secondary Namenode <br>
  运行在一个单独的物理机上，因为合并操作需要占用大量的CPU时间以及和Namenode相当的内存。辅助Namenode保存着合并后的Namespace镜像的一个备份，万一哪天Namenode宕机了，这个备份就可以用上了。</p>
  
  <p>但是辅助Namenode总是落后于主Namenode，所以在Namenode宕机时，数据丢失是不可避免的。在这种情况下，一般的，要结合第一种方式中提到的远程挂载的网络文件系统(NFS)中的Namenode的元数据文件来使用，把NFS中的Namenode元数据文件，拷贝到辅助Namenode，并把辅助Namenode作为主Namenode来运行。</p>
</blockquote>

<ul>
<li>跨多个DataNode切分文件</li>
</ul>

<blockquote>
  <p>在HDFS里，文件被切分成数据块，通常每个数据块64MB~128MB，然后每个数据块被写入文件系统。同一个文件的不同数据块不一定保存在相同的DataNode上。这样做的好处是，当对这些文件执行运算时，能够通过并行方式读取和处理文件的不同部分。 <br>
  当客户端准备写文件到HDFS并询问NameNode应该把文件写到哪里时，NameNode会告诉客户端，那些可以写入数据块的 <br>
  DataNode。写完一批数据块后，客户端会回到NameNode获取新的DataNode列表，把下一批数据块写到新列表中的DataNode上。</p>
</blockquote>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>