---
layout:     post
title:      Hadoop HDFS原理
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/usher_ou/article/details/79178264				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2 id="hdfs-架构">HDFS 架构</h2>

<p><img src="http://ov1nop9io.bkt.clouddn.com/hdfs-architecture.png" alt="http://ov1nop9io.bkt.clouddn.com/hdfs-architecture.png" title=""></p>



<h3 id="hdfs">HDFS</h3>

<blockquote>
  <p>对外部客户机而言，HDFS就像一个传统的分级文件系统。可以创建、删除、移动或重命名文件，等等。但是 HDFS 的架构是基于一组特定的节点构建的，这是由它自身的特点决定的。这些节点包括 NameNode（仅一个），它在 HDFS 内部提供元数据服务；DataNode，它为 HDFS 提供存储块。由于仅存在一个 NameNode，因此这是 HDFS 的一个缺点（单点失败）。</p>
  
  <p>存储在 HDFS 中的文件被分成块，然后将这些块复制到多个计算机中（DataNode）。这与传统的 RAID 架构大不相同。块的大小（通常为 64MB）和复制的块数量在创建文件时由客户机决定。NameNode 可以控制所有文件操作。HDFS 内部的所有通信都基于标准的 TCP/IP 协议。</p>
  
  <p>HDFS和现有的分布式文件系统有很多共同点。但同时，它和其他的分布式文件系统的区别也是很明显的。HDFS是一个高度容错性的系统，适合部署在廉价的机器上。HDFS能提供高吞吐量的数据访问，非常适合大规模数据集上的应用。HDFS放宽了一部分POSIX约束，来实现流式读取文件系统数据的目的。HDFS在最开始是作为Apache Nutch搜索引擎项目的基础架构而开发的。HDFS是Apache Hadoop Core项目的一部分。</p>
</blockquote>



<h3 id="namenode">NameNode</h3>

<blockquote>
  <p>存储文件的metadata，运行时所有数据都保存到内存，整个HDFS可存储的文件数受限于NameNode的内存大小</p>
  
  <p>一个Block在NameNode中对应一条记录（一般一个block占用150字节），如果是大量的小文件，会消耗大量内存。同时map task的数量是由splits来决定的，所以用MapReduce处理大量的小文件时，就会产生过多的map task，线程管理开销将会增加作业时间。处理大量小文件的速度远远小于处理同等大小的大文件的速度。因此Hadoop建议存储大文件</p>
  
  <p>数据会定时保存到本地磁盘，但不保存block的位置信息，而是由DataNode注册时上报和运行时维护（NameNode中与DataNode相关的信息并不保存到NameNode的文件系统中，而是NameNode每次重启后，动态重建）</p>
  
  <p>NameNode失效则整个HDFS都失效了，所以要保证NameNode的可用性</p>
</blockquote>



<h3 id="secondary-namenode">Secondary NameNode</h3>

<blockquote>
  <p>定时与NameNode进行同步（定期合并文件系统镜像和编辑，然后把合并后的传给NameNode，替换其镜像，并清空编辑日志，类似于CheckPoint机制），但NameNode失效后仍需要手工将其设置成主机</p>
</blockquote>



<h3 id="datanode">DataNode</h3>

<blockquote>
  <p>DataNode 也是一个通常在 HDFS实例中的单独机器上运行的软件。Hadoop 集群包含一个 NameNode 和大量 DataNode。DataNode通常以机架的形式组织，机架通过一个交换机将所有系统连接起来。Hadoop 的一个假设是：机架内部节点之间的传输速度快于机架间节点的传输速度。</p>
  
  <p>DataNode 响应来自 HDFS 客户机的读写请求。它们还响应来自 NameNode 的创建、删除和复制块的命令。NameNode 依赖来自每个 DataNode 的定期心跳（heartbeat）消息。每条消息都包含一个块报告，NameNode 可以根据这个报告验证块映射和其他文件系统元数据。如果 DataNode 不能发送心跳消息，NameNode 将采取修复措施，重新复制在该节点上丢失的块。</p>
  
  <p>即保存具体的block数据</p>
  
  <p>负责数据的读写操作和复制操作</p>
  
  <p>DataNode启动时会向NameNode报告当前存储的数据块信息，后续也会定时报告修改信息</p>
  
  <p>DataNode之间会进行通信，复制数据块，保证数据的冗余性</p>
</blockquote>



<h3 id="hdfswriteoperation">HDFSWriteOperation</h3>

<p><img src="http://ov1nop9io.bkt.clouddn.com/63503acbjw1f5w48p90chj20k20bzdgr.jpg" alt="http://ov1nop9io.bkt.clouddn.com/63503acbjw1f5w48p90chj20k20bzdgr.jpg" title=""></p>

<blockquote>
  <p>在分布式文件系统中，需要确保数据的一致性。对于HDFS来说，直到所有要保存数据的DataNodes确认它们都有文件的副本时，数据才被认为写入完成。因此，数据一致性是在写的阶段完成的。一个客户端无论选择从哪个DataNode读取，都将得到相同的数据。</p>
  
  <p>1.客户端将文件写入本地磁盘的N#x4E34;时文件中</p>
  
  <p>2.当临时文件大小达到一个block大小时，HDFS client通知NameNode，申请写入文件</p>
  
  <p>3.NameNode在HDFS的文件系统中创建一个文件，并把该block id和要写入的DataNode的列表返回给客户端</p>
  
  <p>4.客户端收到这些信息后，将临时文件写入DataNodes</p>
  
  <ul>
  <li>4.1 客户端将文件内容写入第一个DataNode（一般以4kb为单位进行传输）</li>
  <li>4.2 第一个DataNode接收后，将数据写入本地磁盘，同时也传输给第二个DataNode</li>
  <li>4.3 依此类推到最后一个DataNode，数据在DataNode之间是通过pipeline的方式进行复制的</li>
  <li>4.4 后面的DataNode接收完数据后，都会发送一个确认给前一个DataNode，最终第一个DataNode返回确认给客户端</li>
  <li>4.5 当客户端接收到整个block的确认后，会向NameNode发送一个最终的确认信息</li>
  <li>4.6 如果写入某个DataNode失败，数据会继续写入其他的DataNode。然后NameNode会找另外一个好的DataNode继续复制，以保证冗余性</li>
  <li><p>4.7 每个block都会有一个校验码，并存放到独立的文件中，以便读的时候来验证其完整性</p>
  
  <p>5.文件写完后（客户端关闭），NameNode提交文件（这时文件才可见，֘#x5982;果提交前，NameNode垮掉，那文件也就丢失了。fsync：只保证数据的信息写到NameNode上，但并不保证数据已经被写到DataNode中）</p>
  
  <p><strong>Rack aware（机架感知）</strong></p>
  
  <p>通过配置文件指定机架名和DNS的对应关系</p>
  
  <p>假设复制参数是3，在写入文件时，会在本地的机架保存一份数据，然后在另外一个机架内保存两份数据（同机架内的传输速度快，从而提高性能）</p>
  
  <p>整个HDFS的集群，最好是负载平衡的，这样才能尽量利用集群的优势</p></li>
  </ul>
</blockquote>



<h3 id="hdfsreadoperation">HDFSReadOperation</h3>

<p><img src="http://ov1nop9io.bkt.clouddn.com/63503acbjw1f5w48ovukqj20jm0ermy3.jpg" alt="http://ov1nop9io.bkt.clouddn.com/63503acbjw1f5w48ovukqj20jm0ermy3.jpg" title=""></p>

<blockquote>
  <ol>
  <li>客户端请求NameNode,表示读取文件。</li>
  <li>NameNode响应客户端,将block(数据块)的信息发送给客户端。</li>
  <li>客户端检查数据块信息,连接相关的DataNode。</li>
  <li>DataNodeA将block1发送给客户端。</li>
  <li>DataNodeB将block2发送给客户端。</li>
  <li>拼接数据,读取完成。</li>
  </ol>
</blockquote>



<h3 id="hdfsshell命令">HDFSShell命令</h3>

<table>
<thead>
<tr>
  <th>命令名</th>
  <th>格式</th>
  <th>含义</th>
</tr>
</thead>
<tbody><tr>
  <td>-ls</td>
  <td>-ls&lt;路径&gt;</td>
  <td>查看指定路径的当前目录结构</td>
</tr>
<tr>
  <td>-lsr</td>
  <td>-lsr&lt;路径&gt;</td>
  <td>递归查看指定路径的目录结构</td>
</tr>
<tr>
  <td>-du</td>
  <td>-du&lt;路径&gt;</td>
  <td>统计目录下个文件大小</td>
</tr>
<tr>
  <td>-dus</td>
  <td>-dus&lt;路径&gt;</td>
  <td>汇总统计目录下文件(夹)大小</td>
</tr>
<tr>
  <td>-count</td>
  <td>-count[-q]&lt;路径&gt;</td>
  <td>统计文件(夹)数量</td>
</tr>
<tr>
  <td>-mv</td>
  <td>-mv&lt;源路径&gt;&lt;目的路径&gt;</td>
  <td>移动</td>
</tr>
<tr>
  <td>-cp</td>
  <td>-cp&lt;源路径&gt;&lt;目的路径&gt;</td>
  <td>复制</td>
</tr>
<tr>
  <td>-rm</td>
  <td>-rm[-skipTrash]&lt;路径&gt;</td>
  <td>删除文件/空白文件夹</td>
</tr>
<tr>
  <td>-rmr</td>
  <td>-rmr[-skipTrash]&lt;路径&gt;</td>
  <td>递归删除</td>
</tr>
<tr>
  <td>-put</td>
  <td>-put&lt;多个 linux 上的文件&gt;</td>
  <td>上传文件</td>
</tr>
<tr>
  <td>-copyFromLocal</td>
  <td>-copyFromLocal&lt;多个 linux 上的文件&gt;</td>
  <td>从本地复制</td>
</tr>
<tr>
  <td>-moveFromLocal</td>
  <td>-moveFromLocal&lt;多个 linux 上的文件&gt;</td>
  <td>从本地移动</td>
</tr>
<tr>
  <td>-getmerge</td>
  <td>-getmerge&lt;源路径&gt;</td>
  <td>合并到本地</td>
</tr>
<tr>
  <td>-cat</td>
  <td>-cat</td>
  <td>查看文件内容</td>
</tr>
<tr>
  <td>-text</td>
  <td>-text</td>
  <td>查看文件内容</td>
</tr>
<tr>
  <td>-copyToLocal</td>
  <td>-copyToLocal[-ignoreCrc][-crc][hdfs 源路 径][linux 目的路径]</td>
  <td>复制到本地</td>
</tr>
<tr>
  <td>-moveToLocal</td>
  <td>-moveToLocal[-crc]</td>
  <td>移动到本地</td>
</tr>
<tr>
  <td>-mkdir</td>
  <td>-mkdir</td>
  <td>创建空白文件夹</td>
</tr>
<tr>
  <td>-setrep</td>
  <td>-setrep[-R][-w]&lt;副本数&gt;&lt;路径&gt;</td>
  <td>修改副本数量</td>
</tr>
<tr>
  <td>-touchz</td>
  <td>-touchz&lt;文件路径&gt;</td>
  <td>创建空白文件</td>
</tr>
<tr>
  <td>-stat</td>
  <td>-stat[format]&lt;路径&gt;</td>
  <td>显示文件统计信息</td>
</tr>
<tr>
  <td>-tail</td>
  <td>-tail[-f]&lt;文件&gt;</td>
  <td>查看文件尾部信息</td>
</tr>
<tr>
  <td>-chmod</td>
  <td>-chmod[-R]&lt;权限模式&gt;[路径]</td>
  <td>修改权限</td>
</tr>
<tr>
  <td>-chown</td>
  <td>-chown[-R][属主][:[属组]] 路径</td>
  <td>修改属主</td>
</tr>
<tr>
  <td>-chgrp</td>
  <td>-chgrp[-R] 属组名称 路径</td>
  <td>修改属组</td>
</tr>
<tr>
  <td>-help</td>
  <td>-help[命令选项]</td>
  <td>帮助</td>
</tr>
</tbody></table>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>