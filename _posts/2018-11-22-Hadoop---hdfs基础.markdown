---
layout:     post
title:      Hadoop---hdfs基础
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p id="main-toc"><strong>目录</strong></p>

<p id="1%E3%80%81%E8%AE%BE%E8%AE%A1%E5%89%8D%E6%8F%90%E4%B8%8E%E7%9B%AE%E6%A0%87-toc" style="margin-left:0px;"><a href="#1%E3%80%81%E8%AE%BE%E8%AE%A1%E5%89%8D%E6%8F%90%E4%B8%8E%E7%9B%AE%E6%A0%87" rel="nofollow">1、设计前提与目标</a></p>

<p id="2%E3%80%81HDFS%E7%9A%84%E4%BD%93%E7%B3%BB%E7%BB%93%E6%9E%84-toc" style="margin-left:0px;"><a href="#2%E3%80%81HDFS%E7%9A%84%E4%BD%93%E7%B3%BB%E7%BB%93%E6%9E%84" rel="nofollow">2、HDFS的体系结构</a></p>

<p id="1%EF%BC%89%E3%80%81HDFS%E7%9A%84%E8%AF%BB%E6%93%8D%E4%BD%9C-toc" style="margin-left:40px;"><a href="#1%EF%BC%89%E3%80%81HDFS%E7%9A%84%E8%AF%BB%E6%93%8D%E4%BD%9C" rel="nofollow">1）、HDFS的读操作</a></p>

<p id="2%EF%BC%89%E3%80%81HDFS%E7%9A%84%E5%86%99%E6%93%8D%E4%BD%9C-toc" style="margin-left:40px;"><a href="#2%EF%BC%89%E3%80%81HDFS%E7%9A%84%E5%86%99%E6%93%8D%E4%BD%9C" rel="nofollow">2）、HDFS的写操作</a></p>

<p id="3%E3%80%81%E4%BF%9D%E9%9A%9CHDFS%E7%9A%84%E5%8F%AF%E9%9D%A0%E6%80%A7%E6%8E%AA%E6%96%BD-toc" style="margin-left:0px;"><a href="#3%E3%80%81%E4%BF%9D%E9%9A%9CHDFS%E7%9A%84%E5%8F%AF%E9%9D%A0%E6%80%A7%E6%8E%AA%E6%96%BD" rel="nofollow">3、保障HDFS的可靠性措施</a></p>

<p id="4%E3%80%81HDFS%E5%B8%B8%E7%94%A8Shell%E6%93%8D%E4%BD%9C-toc" style="margin-left:0px;"><a href="#4%E3%80%81HDFS%E5%B8%B8%E7%94%A8Shell%E6%93%8D%E4%BD%9C" rel="nofollow">4、HDFS常用Shell操作</a></p>

<p id="%E4%BA%94%E3%80%81%E4%BD%BF%E7%94%A8Java%20API%E6%93%8D%E4%BD%9CHDFS-toc" style="margin-left:0px;"><a href="#%E4%BA%94%E3%80%81%E4%BD%BF%E7%94%A8Java%20API%E6%93%8D%E4%BD%9CHDFS" rel="nofollow">5、使用Java API操作HDFS</a></p>

<hr id="hr-toc"><p>HDFS（Hadoop Distributed File System，Hadoop分布式文件系统）是分布式计算中数据存储管理的基础。它所具有的高容错、高可靠、高可扩展性、高吞吐率等特性为海量数据提供了不怕故障的存储，也为超大规模数据集（Large Data Set）的应用处理带来了很多便利。</p>

<h1 id="1%E3%80%81%E8%AE%BE%E8%AE%A1%E5%89%8D%E6%8F%90%E4%B8%8E%E7%9B%AE%E6%A0%87">1、设计前提与目标</h1>

<p>　　（1）硬件错误是常态而不是异常；（最核心的设计目标—&gt;HDFS被设计为运行在众多的普通硬件上，所以硬件故障是很正常的。因此，错误检测并快速恢复是HDFS最核心的设计目标）</p>

<p>　　（2）流式数据访问；（HDFS更关注数据访问的高吞吐量）</p>

<p>　　（3）大规模数据集；（HDFS的典型文件大小大多都在GB甚至TB级别）</p>

<p>　　（4）简单一致性模型；（一次写入，多次读取的访问模式）</p>

<p>　　（5）移动计算比移动数据更为划算；（对于大文件来说，移动计算比移动数据的代价要低）</p>

<h1 id="2%E3%80%81HDFS%E7%9A%84%E4%BD%93%E7%B3%BB%E7%BB%93%E6%9E%84">2、HDFS的体系结构</h1>

<p>　HDFS是一个<strong>主/从（Master/Slave）式</strong>的结构，如下图所示。</p>

<p><img alt="" class="has" src="https://images0.cnblogs.com/blog/381412/201402/060036521686325.png"></p>

<p>从最终用户的角度来看，它就像传统的文件系统一样，可以通过目录路径对文件执行CRUD（增删查改）操作。但由于分布式存储的性质，HDFS拥有一个NameNode和一些DataNodes。NameNode管理文件系统的元数据，DataNode存储实际的数据。客户端通过同NameNode和DataNode的交互访问文件系统→客户端联系NameNode以获取文件的元数据，而真正的I/O操作是直接和DataNode进行交互的。下面我们再来看看HDFS的读操作和写操作的流程：</p>

<h2 id="1%EF%BC%89%E3%80%81HDFS%E7%9A%84%E8%AF%BB%E6%93%8D%E4%BD%9C">1）、HDFS的读操作</h2>

<p><img alt="" class="has" src="https://images0.cnblogs.com/blog/381412/201312/21154137-ce339dff13c14cc5985e65671d38c499.png"></p>

<p> 客户端要访问一个文件，首先，客户端从NameNode中获得组成该文件数据块位置列表，即知道数据块被存储在哪几个DataNode上；然后，客户端直接从DataNode上读取文件数据。在此过程中，NameNode不参与文件的传输。</p>

<h2 id="2%EF%BC%89%E3%80%81HDFS%E7%9A%84%E5%86%99%E6%93%8D%E4%BD%9C">2）、HDFS的写操作</h2>

<p><img alt="" class="has" src="https://images0.cnblogs.com/blog/381412/201312/21154221-09d86b8e276648db95675b4d91914119.png"></p>

<p>客户端首先需要向NameNode发起写请求，NameNode会根据文件大小和文件块配置情况，返回给Client它所管理部分DataNode的信息。最后，Client（开发库）将文件划分为多个文件块，根据DataNode的地址信息，按顺序写入到每一个DataNode块中。</p>

<p>下面我们看看NameNode和DataNode扮演什么角色，有什么具体的作用：<br><strong>（1）NameNode</strong><br>
　　NameNode的作用是管理文件目录结构，是管理数据节点的。NameNode维护两套数据：一套是文件目录与数据块之间的关系，另一套是数据块与节点间的关系。前一套是静态的，是存放在磁盘上的，通过fsimage和edits文件来维护；后一套数据时动态的，不持久化到磁盘，每当集群启动的时候，会自动建立这些信息。<br><strong>（2）DataNode</strong><br>
　　毫无疑问，DataNode是HDFS中真正存储数据的。这里要提到一点，就是Block（数据块）。假设文件大小是100GB，从字节位置0开始，每64MB字节划分为一个Block，以此类推，可以划分出很多的Block。每个Block就是64MB（也可以自定义设置Block大小）。<br><strong>（3）典型部署</strong><br>
　　HDFS的一个典型部署是在一个专门的机器上运行NameNode，集群中的其他机器各运行一个DataNode。（当然，也可以在运行NameNode的机器上同时运行DataNode，或者一个机器上运行多个DataNode）一个集群中只有一个NameNode（但是单NameNode存在单点问题，在Hadoop 2.x版本之后解决了这个问题）的设计大大简化了系统架构。</p>

<h1 id="3%E3%80%81%E4%BF%9D%E9%9A%9CHDFS%E7%9A%84%E5%8F%AF%E9%9D%A0%E6%80%A7%E6%8E%AA%E6%96%BD">3、保障HDFS的可靠性措施</h1>

<p>　　HDFS具备了较为完善的冗余备份和故障恢复机制，可以实现在集群中可靠地存储海量文件。<br>
　　（1）冗余备份：HDFS将每个文件存储成一系列的数据块（Block），<span style="color:#f33b45;"><strong>默认块大小为64MB（可以自定义配置）</strong></span>。为了容错，文件的所有数据块都可以有副本（默认为3个，可以自定义配置）。当DataNode启动的时候，它会遍历本地文件系统，产生一份HDFS数据块和本地文件对应关系的列表，并把这个报告发送给NameNode，这就是报告块（BlockReport），报告块上包含了DataNode上所有块的列表。<br>
　　（2）副本存放：HDFS集群一般运行在多个机架上，不同机架上机器的通信需要通过交换机。通常情况下，副本的存放策略很关键，机架内节点之间的带宽比跨机架节点之间的带宽要大，它能影响HDFS的可靠性和性能。HDFS采用一种称为机架感知（Rack-aware）的策略来改进数据的可靠性、可用性和网络带宽的利用率。在大多数情况下，HDFS副本系数是默认为3，HDFS的存放策略是将一个副本存放在本地机架节点上，一个副本存放在同一个机架的另一个节点上，最后一个副本放在不同机架的节点上。这种策略减少了机架间的数据传输，提高了写操作的效率。机架的错误远远比节点的错误少，所以这种策略不会影响到数据的可靠性和可用性。</p>

<p><img alt="" class="has" src="https://images0.cnblogs.com/blog/381412/201402/060114284564820.jpg"></p>

<p>（3）心跳检测：NameNode周期性地从集群中的每个DataNode接受心跳包和块报告，NameNode可以根据这个报告验证映射和其他文件系统元数据。收到心跳包，说明该DataNode工作正常。如果DataNode不能发送心跳信息，NameNode会标记最近没有心跳的DataNode为宕机，并且不会给他们发送任何I/O请求。<br>
（4）安全模式<br>
（5）数据完整性检测<br>
（6）空间回收<br>
（7）元数据磁盘失效<br>
（8）快照（HDFS目前还不支持）</p>

<h1 id="4%E3%80%81HDFS%E5%B8%B8%E7%94%A8Shell%E6%93%8D%E4%BD%9C">4、HDFS常用Shell操作</h1>

<p>（1）列出文件目录</p>

<p>hadoop fs -ls 目录路径<br>
查看HDFS根目录下的目录：hadoop fs -ls / <br>
递归查看HDFS根目录下的目录：hadoop fs -lsr /</p>

<p><br>
（2）在HDFS中创建文件夹</p>

<p>hadoop fs -mkdir 文件夹名称<br>
在根目录下创建一个名称为di的文件夹</p>

<p><br>
（3）上传文件到HDFS中</p>

<p>hadoop fs -put 本地源路径 目标存放路径<br>
将本地系统中的一个log文件上传到di文件夹中：hadoop fs -put test.log /di</p>

<p>备注：我们通过Hadoop Shell上传的文件是存放在DataNode的Block（数据块）中的，通过Linux Shell是看不到文件的，只能看到Block。因此，可以用一句话来描述HDFS：把客户端的大文件存放在很多节点的数据块中。</p>

<p><br>
（4）从HDFS中下载文件</p>

<p>hadoop fs -get HDFS文件路径 本地存放路径<br>
将刚刚上传的test.log下载到本地的Desktop文件夹中：hadoop fs -get /di/test.log /home/hadoop/Desktop</p>

<p>（5）直接在HDFS中查看某个文件</p>

<p>hadoop fs -text(-cat) 文件存放路径<br>
在HDFS查看刚刚上传的test.log文件：hadoop fs -text /di/test.log</p>

<p>（6）删除在HDFS中的某个文件(夹)</p>

<p>hadoop fs -rm(r) 文件存放路径<br>
删除刚刚上传的test.log文件：hadoop fs -rm /di/test.log<br>
删除HDFS中的di文件夹：hadoop fs -rmr /di</p>

<p>（7）善用help命令求帮助：hadoop fs -help 命令<br>
查看ls命令的帮助：hadoop fs -help ls</p>

<h1 id="%E4%BA%94%E3%80%81%E4%BD%BF%E7%94%A8Java%20API%E6%93%8D%E4%BD%9CHDFS">5、使用Java API操作HDFS</h1>

<p> </p>

<p> </p>

<p>参考：</p>

<p><a href="http://www.cnblogs.com/edisonchou/p/3538524.html" rel="nofollow">http://www.cnblogs.com/edisonchou/p/3538524.html</a></p>            </div>
                </div>