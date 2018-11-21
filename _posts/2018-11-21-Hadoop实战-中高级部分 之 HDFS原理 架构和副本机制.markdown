---
layout:     post
title:      Hadoop实战-中高级部分 之 HDFS原理 架构和副本机制
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<div><a href="http://sishuok.com/forum/blogPost/list/5833.html" rel="nofollow">Hadoop RestFul</a></div>
<div><a href="http://sishuok.com/forum/blogPost/list/5936.html" rel="nofollow">Hadoop HDFS原理1</a></div>
<div><a href="http://sishuok.com/forum/blogPost/list/5937.html" rel="nofollow">Hadoop HDFS原理2</a></div>
<div><a href="http://sishuok.com/forum/blogPost/list/5938.html" rel="nofollow">Hadoop作业调优参数调整及原理</a></div>
<div><a href="http://sishuok.com/forum/blogPost/list/5939.html" rel="nofollow">Hadoop HA</a></div>
<div><a href="http://sishuok.com/forum/blogPost/list/5961.html" rel="nofollow">Hadoop MapReduce高级编程</a></div>
<div><a href="http://sishuok.com/forum/blogPost/list/5963.html" rel="nofollow">Hadoop IO</a></div>
<div><a href="http://sishuok.com/forum/blogPost/list/5965.html" rel="nofollow">Hadoop MapReduce工作原理</a></div>
<div><a href="http://sishuok.com/forum/blogPost/list/5966.html" rel="nofollow">Hadoop 管理</a></div>
<div><a href="http://sishuok.com/forum/blogPost/list/5969.html" rel="nofollow">Hadoop 集群安装</a></div>
<div><a href="http://sishuok.com/forum/blogPost/list/5968.html" rel="nofollow">Hadoop RPC</a></div>
<div> </div>
</div>
<div style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;"><span class="bold" style="font-weight:bold;">第一部分：当前HDFS架构详尽分析</span> <br>
  <img src="http://sishuok.com/forum/upload/2012/9/4/ab905f0a30663acc8b302415875fa069__1.JPG" alt="" style="border:0px;"></div>
<div style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;"> </div>
<div style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<div class="O"><span class="bold" lang="en-us" style="font-weight:bold;" xml:lang="en-us">HDFS架构</span></div>
<div class="O">
<div>•NameNode</div>
<div>•DataNode</div>
<div>•Sencondary NameNode</div>
<div> </div>
<div>
<div class="O"><span class="bold" style="font-weight:bold;">数据存储细节</span></div>
<div class="O"><img src="http://sishuok.com/forum/upload/2012/9/4/de4875ed05759a5e72fa05ad8a5cf4a6__2.JPG" alt="" style="border:0px;"><br></div>
</div>
</div>
<div class="O"><span class="bold" lang="en-us" style="font-weight:bold;" xml:lang="en-us">NameNode 目录结构</span></div>
<div class="O">
<div><span lang="en-us" xml:lang="en-us">Namenode</span> 的目录结构：</div>
<div>           ${ <span lang="en-us" xml:lang="en-us">dfs.name.dir}/current /VERSION<br></span><span lang="en-us" xml:lang="en-us">                                                  /edits<br></span><span lang="en-us" xml:lang="en-us">                                                  /fsimage<br></span><span lang="en-us" xml:lang="en-us">                                                  /fstime</span></div>
<div><span lang="en-us" xml:lang="en-us">    dfs.name.dir</span> 是 <span lang="en-us" xml:lang="en-us">hdfs-site.xml</span> 里配置的若干个目录组成的列表。</div>
<div>
<div class="O"><span class="bold" lang="en-us" style="font-weight:bold;" xml:lang="en-us">NameNode</span></div>
</div>
<div>
<div class="O">
<div><span lang="en-us" xml:lang="en-us">             Namenode</span> 上保存着 <span lang="en-us" xml:lang="en-us">HDFS</span> 的名字空间。对于任何对文件系统元数据产生修改的操作， <span lang="en-us" xml:lang="en-us">Namenode</span> 都会使用一种称为 <span lang="en-us" xml:lang="en-us">EditLog</span> 的事务日志记录下来。例如，在 <span lang="en-us" xml:lang="en-us">HDFS</span> 中创建一个文件， <span lang="en-us" xml:lang="en-us">Namenode</span> 就会在 <span lang="en-us" xml:lang="en-us">Editlog</span> 中插入一条记录来表示；同样地，修改文件的副本系数也将往 <span lang="en-us" xml:lang="en-us">Editlog</span> 插入一条记录。 <span lang="en-us" xml:lang="en-us">Namenode</span> 在本地操作系统的文件系统中存储这个 <span lang="en-us" xml:lang="en-us">Editlog</span> 。整个文件系统的名 字空间，包括数据块到文件的映射、文件的属性等，都存储在一个称为 <span lang="en-us" xml:lang="en-us">FsImage</span> 的文件中，这 个文件也是放在 <span lang="en-us" xml:lang="en-us">Namenode</span> 所在的本地文件系统上。</div>
<div><span lang="en-us" xml:lang="en-us">              Namenode</span> 在内存中保存着整个文件系统的名字空间和文件数据块映射<span lang="en-us" xml:lang="en-us">(Blockmap)</span> 的映像 。这个关键的元数据结构设计得很紧凑，因而一个有 <span lang="en-us" xml:lang="en-us">4G</span> 内存的<span lang="en-us" xml:lang="en-us">Namenode</span> 足够支撑大量的文件 和目录。当 <span lang="en-us" xml:lang="en-us">Namenode</span> 启动时，它从硬盘中读取<span lang="en-us" xml:lang="en-us">Editlog</span> 和 <span lang="en-us" xml:lang="en-us">FsImage</span> ，将所有 <span lang="en-us" xml:lang="en-us">Editlog</span> 中的事务作 用在内存中的 <span lang="en-us" xml:lang="en-us">FsImage</span> 上，并将这个新版本的 <span lang="en-us" xml:lang="en-us">FsImage</span> 从内存中保存到本地磁盘上，然后删除 旧的 <span lang="en-us" xml:lang="en-us">Editlog</span> ，因为这个旧的 <span lang="en-us" xml:lang="en-us">Editlog</span> 的事务都已经作用在 <span lang="en-us" xml:lang="en-us">FsImage</span> 上了。这个过程称为一个检查 点<span lang="en-us" xml:lang="en-us">(checkpoint)</span> 。在当前实现中，检查点只发生在 <span lang="en-us" xml:lang="en-us">Namenode</span> 启动时，在不久的将来将实现支持 周期性的检查点。</div>
<div> </div>
<div> 
<div class="O"><span class="bold" lang="en-us" style="font-weight:bold;" xml:lang="en-us">HDFS NameSpace</span></div>
</div>
</div>
<div class="O">
<div>            <span lang="en-us" xml:lang="en-us">HDFS</span> 支持传统的层次型文件组织结构。用户或者应用程序可以创建目录，然后将文件保存在这些目录里。文件系统名字空间的层次结构和大多数 现有的文件系统类似：用户可以创建、删除、移动或重命名文件。当前， <span lang="en-us" xml:lang="en-us">HDFS</span> 不支持用户磁盘配额和访问权限控制，也不支持硬链接和软链接。但 是 <span lang="en-us" xml:lang="en-us">HDFS</span> 架构并不妨碍实现这些特性。</div>
<div><span lang="en-us" xml:lang="en-us">             Namenode</span> 负责维护文件系统命名空间，任何对文件系统名字空间或属 性的修改都将被 <span lang="en-us" xml:lang="en-us">Namenode</span> 记录下来。应用程序可以设置 <span lang="en-us" xml:lang="en-us">HDFS</span> 保存的文件 的副本数目。文件副本的数目称为文件的副本系数，这个信息也是由 <span lang="en-us" xml:lang="en-us">Namenode</span> 保存的。</div>
<div><br></div>
<div>
<div class="O"><span class="bold" lang="en-us" style="font-weight:bold;" xml:lang="en-us">DataNode</span></div>
<div class="O">
<div><span lang="en-us" xml:lang="en-us">               Datanode</span> 将 <span lang="en-us" xml:lang="en-us">HDFS</span> 数据以文件的形式存储在本地的文件系统中，它并不知道有 关 <span lang="en-us" xml:lang="en-us">HDFS</span> 文件的信息。它把每个 <span lang="en-us" xml:lang="en-us">HDFS</span> 数据块存储在本地文件系统的一个单独的文件 中。 <span lang="en-us" xml:lang="en-us">Datanode</span> 并不在同一个目录创建所有的文件，实际上，它用试探的方法来确定 每个目录的最佳文件数目，并且在适当的时候创建子目录。在同一个目录中创建所 有的本地文件并不是最优的选择，这是因为本地文件系统可能无法高效地在单个目 录中支持大量的文件。</div>
<div><span lang="en-us" xml:lang="en-us">            </span>当一个 <span lang="en-us" xml:lang="en-us">Datanode</span> 启动时，它会扫描本地文件系统，产生一个这些本地文件对应 的所有 <span lang="en-us" xml:lang="en-us">HDFS</span> 数据块的列表，然后作为报告发送到 <span lang="en-us" xml:lang="en-us">Namenode</span> ，这个报告就是块状态 报告。</div>
<div>
<div class="O"><span class="bold" lang="en-us" style="font-weight:bold;" xml:lang="en-us">Secondary NameNode</span>      </div>
</div>
<div>
<div class="O">
<div><span lang="en-us" xml:lang="en-us">        Secondary NameNode</span> 定期合并 <span lang="en-us" xml:lang="en-us">fsimage</span> 和 <span lang="en-us" xml:lang="en-us">edits</span> 日志，将 <span lang="en-us" xml:lang="en-us">edits</span> 日志文件大小控制在一个限度下。</div>
<div><br></div>
<div>
<div class="O"><span class="bold" style="font-weight:bold;">配置Secondary NameNode</span></div>
<div class="O">
<div>• conf/masters文件指定的为Secondary NameNode节点</div>
<div>•修改在masters文件中配置了的机器上的conf/hdfs-site.xml文件，加上如下选项：</div>
<div>       &lt;property&gt; &lt;name&gt;dfs.http.address&lt;/name&gt; &lt;value&gt;namenode.hadoop-host.com:50070&lt;/value&gt; &lt;/property&gt;</div>
<div> </div>
<div>•core-site.xml：这里有2个参数可配置，但一般来说我们不做修改。fs.checkpoint.period表示多长时间记录一次hdfs的镜像。默认是1小时。fs.checkpoint.size表示一次记录多大的size，默认64M。</div>
<div>      &lt;property&gt; &lt;name&gt;fs.checkpoint.period&lt;/name&gt; &lt;value&gt;3600&lt;/value&gt; &lt;description&gt;The number of seconds between two periodic checkpoints. &lt;/description&gt; &lt;/property&gt;</div>
<div>        &lt;property&gt; &lt;name&gt;fs.checkpoint.size&lt;/name&gt; &lt;value&gt;67108864&lt;/value&gt; &lt;description&gt;The size of the current edit log (in bytes) that triggers a periodic checkpoint even if the fs.checkpoint.period hasn't expired. &lt;/description&gt; &lt;/property&gt;</div>
<div>
<div class="O"><span class="bold" lang="en-us" style="font-weight:bold;" xml:lang="en-us">Secondary NameNode</span></div>
  <img src="http://sishuok.com/forum/upload/2012/9/4/1d1fb437a9c11991d210637691daafd3__3.JPG" alt="" style="border:0px;"></div>
</div>
 </div>
</div>
 
<div class="O"><span class="bold" lang="en-us" style="font-weight:bold;" xml:lang="en-us">Secondary NameNode处理流程</span></div>
</div>
<div>
<div class="O">
<div><span lang="en-us" xml:lang="en-us">   </span><span lang="en-us" xml:lang="en-us">(1)</span> <span lang="en-us" xml:lang="en-us">、</span> <span lang="en-us" xml:lang="en-us">namenode</span> 响应 <span lang="en-us" xml:lang="en-us">Secondary namenode</span> 请求，将 <span lang="en-us" xml:lang="en-us">edit log</span> 推送给<span lang="en-us" xml:lang="en-us">Secondary namenode</span> <span lang="en-us" xml:lang="en-us">，</span> 开始重新写一个新的 <span lang="en-us" xml:lang="en-us">edit
 log</span> <span lang="en-us" xml:lang="en-us">。</span></div>
<div><span lang="en-us" xml:lang="en-us">   </span> <span lang="en-us" xml:lang="en-us">(2)</span> <span lang="en-us" xml:lang="en-us">、</span> <span lang="en-us" xml:lang="en-us">Secondary namenode</span> 收到来自 <span lang="en-us" xml:lang="en-us">namenode</span> 的 <span lang="en-us" xml:lang="en-us">fsimage</span> 文件和 <span lang="en-us" xml:lang="en-us">edit log</span> <span lang="en-us" xml:lang="en-us">。</span></div>
<div><span lang="en-us" xml:lang="en-us">   </span> <span lang="en-us" xml:lang="en-us">(3)</span> <span lang="en-us" xml:lang="en-us">、</span> <span lang="en-us" xml:lang="en-us">Secondary namenode</span> 将 <span lang="en-us" xml:lang="en-us">fsimage</span> 加载到内存，应用 <span lang="en-us" xml:lang="en-us">edit log</span> <span lang="en-us" xml:lang="en-us">，</span> 并生成一 个新的 <span lang="en-us" xml:lang="en-us">fsimage</span> 文件。</div>
<div>    (4) 、 <span lang="en-us" xml:lang="en-us">Secondary namenode</span> 将新的 <span lang="en-us" xml:lang="en-us">fsimage</span> 推送给 <span lang="en-us" xml:lang="en-us">Namenode</span> <span lang="en-us" xml:lang="en-us">。</span></div>
<div><span lang="en-us" xml:lang="en-us">    </span> <span lang="en-us" xml:lang="en-us">(5)</span> <span lang="en-us" xml:lang="en-us">、</span> <span lang="en-us" xml:lang="en-us">Namenode</span> 用新的 <span lang="en-us" xml:lang="en-us">fsimage</span> 取代旧的 <span lang="en-us" xml:lang="en-us">fsimage</span> <span lang="en-us" xml:lang="en-us">，</span> 在 <span lang="en-us" xml:lang="en-us">fstime</span> 文件中记下检查 点发生的时</div>
<div>
<div class="O"><span class="bold" lang="en-us" style="font-weight:bold;" xml:lang="en-us">HDFS通信协议</span></div>
</div>
<div>
<div class="O">
<div>            所有的 <span lang="en-us" xml:lang="en-us">HDFS</span> 通讯协议都是构建在 <span lang="en-us" xml:lang="en-us">TCP/IP</span> 协议上。客户端通过一个可 配置的端口连接到 <span lang="en-us" xml:lang="en-us">Namenode</span> <span lang="en-us" xml:lang="en-us">，</span> 通过 <span lang="en-us" xml:lang="en-us">ClientProtocol</span> 与 <span lang="en-us" xml:lang="en-us">Namenode</span> 交互。而<span lang="en-us" xml:lang="en-us">Datanode</span> 是使用 <span lang="en-us" xml:lang="en-us">DatanodeProtocol</span> 与 <span lang="en-us" xml:lang="en-us">Namenode</span> 交互。再设计上，<span lang="en-us" xml:lang="en-us">DataNode</span> 通过周期性的向 <span lang="en-us" xml:lang="en-us">NameNode</span> 发送心跳和数据块来保持和 <span lang="en-us" xml:lang="en-us">NameNode</span> 的通信，数据块报告的信息包括数据块的属性，即数据块属于哪 个文件，数据块 <span lang="en-us" xml:lang="en-us">ID</span> ，修改时间等， <span lang="en-us" xml:lang="en-us">NameNode</span> 的 <span lang="en-us" xml:lang="en-us">DataNode</span> 和数据块的映射 关系就是通过系统启动时<span lang="en-us" xml:lang="en-us">DataNode</span> 的数据块报告建立的。从 <span lang="en-us" xml:lang="en-us">ClientProtocol</span> 和 <span lang="en-us" xml:lang="en-us">Datanodeprotocol</span> 抽象出一个远程调用 <span lang="en-us" xml:lang="en-us">(</span> <span lang="en-us" xml:lang="en-us">RPC</span> <span lang="en-us" xml:lang="en-us">），</span> 在设计上， <span lang="en-us" xml:lang="en-us">Namenode</span> 不会主动发起 <span lang="en-us" xml:lang="en-us">RPC</span> <span lang="en-us" xml:lang="en-us">，</span> 而是是响应来自客户端和 <span lang="en-us" xml:lang="en-us">Datanode </span>的 <span lang="en-us" xml:lang="en-us">RPC</span> 请求。</div>
<div>
<div class="O"><span class="bold" lang="en-us" style="font-weight:bold;" xml:lang="en-us">HDFS的安全模式</span></div>
</div>
<div>
<div class="O">
<div><span lang="en-us" xml:lang="en-us">           Namenode</span> 启动后会进入一个称为安全模式的特殊状态。处于安全模式 的<span lang="en-us" xml:lang="en-us">Namenode</span> 是不会进行数据块的复制的。 <span lang="en-us" xml:lang="en-us">Namenode</span> 从所有的 Datanode 接收心跳信号和块状态报告。块状态报告包括了某个 <span lang="en-us" xml:lang="en-us">Datanode</span> 所有的数据 块列表。每个数据块都有一个指定的最小副本数。当 <span lang="en-us" xml:lang="en-us">Namenode</span> 检测确认某 个数据块的副本数目达到这个最小值，那么该数据块就会被认为是副本安全 <span lang="en-us" xml:lang="en-us">(safely
 replicated)</span> 的；在一定百分比（这个参数可配置）的数据块被 <span lang="en-us" xml:lang="en-us">Namenode</span> 检测确认是安全之后（加上一个额外的 <span lang="en-us" xml:lang="en-us">30</span> 秒等待时间）， <span lang="en-us" xml:lang="en-us">Namenode</span> 将退出安全模式状态。接下来它会确定还有哪些数据块的副本没 有达到指定数目，并将这些数据块复制到其他 <span lang="en-us" xml:lang="en-us">Datanode</span>上。</div>
<div> </div>
</div>
<div>
<div class="O">
<div> </div>
<div> </div>
<div><span class="bold" style="font-weight:bold;">第二部分：HDFS文件读取的解析</span></div>
<div>
<div class="O"><span class="bold" style="font-weight:bold;">文件读取流程</span></div>
  <img src="http://sishuok.com/forum/upload/2012/9/4/f4376c477c75ec2e909b14e0ea89b4f1__4.JPG" alt="" style="border:0px;"></div>
<div> 
<div class="O"><span class="bold" style="font-weight:bold;">流程分析</span></div>
</div>
<div>
<div class="O">
<div>•使用HDFS提供的客户端开发库Client，向远程的Namenode发起RPC请求；</div>
<div>• Namenode会视情况返回文件的部分或者全部block列表，对于每个block，Namenode都会返回有该block拷贝的DataNode地址；</div>
<div>•客户端开发库Client会选取离客户端最接近的DataNode来读取block；如果客户端本身就是DataNode,那么将从本地直接获取数据.</div>
<div>•读取完当前block的数据后，关闭与当前的DataNode连接，并为读取下一个block寻找最佳的DataNode；</div>
<div>•当读完列表的block后，且文件读取还没有结束，客户端开发库会继续向Namenode获取下一批的block列表。</div>
<div>•读取完一个block都会进行checksum验证，如果读取datanode时出现错误，客户端会通知Namenode，然后再从下一个拥有该block拷贝的datanode继续读。</div>
<div> </div>
<div> </div>
</div>
 </div>
</div>
<div class="O">
<div><span class="bold" style="font-weight:bold;">第三部分：HDFS文件写入的解析</span></div>
<div>
<div class="O"><span class="bold" style="font-weight:bold;">文件写入流程</span></div>
  <img src="http://sishuok.com/forum/upload/2012/9/4/ec5bcdd0adff253bac03d901efa20a1c__5.JPG" alt="" style="border:0px;"></div>
</div>
<div class="O"><span class="bold" style="font-weight:bold;">流程分析</span></div>
<div class="O">
<div>•使用HDFS提供的客户端开发库Client，向远程的Namenode发起RPC请求；</div>
<div>•Namenode会检查要创建的文件是否已经存在，创建者是否有权限进行操作，成功则会为文件 <strong>创建一个记录</strong>，否则会让客户端抛出异常；</div>
<div>•当客户端开始写入文件的时候，会将文件切分成多个packets，并在内部以数据队列"data queue"的形式管理这些packets，并向Namenode申请新的blocks，获取用来存储replicas的合适的datanodes列表，列表的大小根据在Namenode中对replication的设置而定。</div>
<div>•开始以pipeline（管道）的形式将packet写入所有的replicas中。把packet以流的方式写入第一个datanode，该datanode把该packet存储之后，再将其传递给在此pipeline中的下一个datanode，直到最后一个datanode，这种写数据的方式呈流水线的形式。</div>
<div>•最后一个datanode成功存储之后会返回一个ack packet，在pipeline里传递至客户端，在客户端的开发库内部维护着"ack queue"，成功收到datanode返回的ack packet后会从"ack queue"移除相应的packet。</div>
<div>•如果传输过程中，有某个datanode出现了故障，那么当前的pipeline会被关闭，出现故障的datanode会从当前的pipeline中移除，剩余的block会继续剩下的datanode中继续以pipeline的形式传输，同时Namenode会分配一个新的datanode，保持replicas设定的数量。</div>
<div> </div>
<div> 
<div class="O"><span class="bold" style="font-weight:bold;">流水线复制</span></div>
</div>
</div>
<div class="O">
<div>               当客户端向 <span lang="en-us" xml:lang="en-us">HDFS</span> 文件写入数据的时候，一开始是写到本地临时文件中。假设该文件的副 本系数设置为 <span lang="en-us" xml:lang="en-us">3</span> ，当本地临时文件累积到一个数据块的大小时，客户端会从 <span lang="en-us" xml:lang="en-us">Namenode</span> 获取一个 <span lang="en-us" xml:lang="en-us">Datanode</span> 列表用于存放副本。然后客户端开始向第一个 <span lang="en-us" xml:lang="en-us">Datanode</span> 传输数据，第一个 <span lang="en-us" xml:lang="en-us">Datanode</span> 一小部分一小部分 <span lang="en-us" xml:lang="en-us">(4
 KB)</span> 地接收数据，将每一部分写入本地仓库，并同时传输该部分到列表中 第二个 <span lang="en-us" xml:lang="en-us">Datanode</span>节点。第二个 <span lang="en-us" xml:lang="en-us">Datanode</span> 也是这样，一小部分一小部分地接收数据，写入本地 仓库，并同时传给第三个 <span lang="en-us" xml:lang="en-us">Datanode</span> 。最后，第三个 <span lang="en-us" xml:lang="en-us">Datanode</span> 接收数据并存储在本地。因此， <span lang="en-us" xml:lang="en-us">Datanode</span> 能流水线式地从前一个节点接收数据，并在同时转发给下一个节点，数据以流水线的 方式从前一个 <span lang="en-us" xml:lang="en-us">Datanode</span> 复制到下一个</div>
<div> </div>
<div> 
<div class="O"><span class="bold" style="font-weight:bold;">更细节的原理</span></div>
</div>
</div>
<div class="O">
<div>           客户端创建文件的请求其实并没有立即发送给 <span lang="en-us" xml:lang="en-us">Namenode</span> ，事实上，在刚开始阶 段 <span lang="en-us" xml:lang="en-us">HDFS</span> 客户端会先将文件数据缓存到本地的一个临时文件。应用程序的写操作被透 明地重定向到这个临时文件。当这个临时文件累积的数据量超过一个数据块的大小 ，客户端才会联系 <span lang="en-us" xml:lang="en-us">Namenode</span> 。 <span lang="en-us" xml:lang="en-us">Namenode</span> 将文件名插入文件系统的层次结构中，并 且分配一个数据块给它。然后返回 <span lang="en-us" xml:lang="en-us">Datanode</span> 的标识符和目标数据块给客户端。接着 客户端将这块数据从本地临时文件上传到指定的 <span lang="en-us" xml:lang="en-us">Datanode</span> 上。当文件关闭时，在临 时文件中剩余的没有上传的数据也会传输到指定的 <span lang="en-us" xml:lang="en-us">Datanode</span> 上。然后客户端告诉 <span lang="en-us" xml:lang="en-us">Namenode</span> 文件已经关闭。此时 <span lang="en-us" xml:lang="en-us">Namenode</span> 才将文件创建操作提交到日志里进行存储 。如果 <span lang="en-us" xml:lang="en-us">Namenode</span> 在文件关闭前宕机了，则该文件将丢失。</div>
<div>
<div class="O">
<div> </div>
<div><span class="bold" style="font-weight:bold;">第四部分：副本机制</span></div>
<div>
<div class="O"><span class="bold" style="font-weight:bold;">特点</span></div>
<div class="O">
<div><span lang="en-us" xml:lang="en-us">1.</span> 数据类型单一</div>
<div><span lang="en-us" xml:lang="en-us">2.</span> 副本数比较多</div>
<div><span lang="en-us" xml:lang="en-us">3.</span> 写文件时副本的放置方法</div>
<div><span lang="en-us" xml:lang="en-us">4.</span> 动态的副本创建策略</div>
<div><span lang="en-us" xml:lang="en-us">5.</span> 弱化的副本一致性要求</div>
<div> </div>
<div>
<div class="O"><span class="bold" style="font-weight:bold;">副本摆放策略</span></div>
  <img src="http://sishuok.com/forum/upload/2012/9/4/10469c4e358f1544db4663c342baf464__6.JPG" alt="" style="border:0px;"></div>
</div>
 </div>
<div> 
<div class="O"><span class="bold" style="font-weight:bold;">修改副本数</span></div>
</div>
</div>
</div>
<div>
<div class="O">
<div>1.集群只有三个Datanode，hadoop系统replication=4时，会出现什么情况？</div>
<div>        对于上传文件到hdfs上时，当时hadoop的副本系数是几，这个文件的块数副本数就会有几份，无论以后你怎么更改系统副本系统，这个文件的副本数都不会改变，也就说上传到分布式系统上的文件副本数由当时的系统副本数决定，不会受replication的更改而变化，除非用命令来更改文件的副本数。因为dfs.replication实质上是client参数，在create文件时可以指定具体replication，属性dfs.replication是不指定具体replication时的采用默认备份数。文件上传后，备份数已定，修改dfs.replication是不会影响以前的文件的，也不会影响后面指定备份数的文件。只影响后面采用默认备份数的文件。但可以利用hadoop提供的命令后期改某文件的备份数：hadoop
 fs -setrep -R 1。如果你是在hdfs-site.xml设置了dfs.replication，这并一定就得了，因为你可能没把conf文件夹加入到你的 project的classpath里，你的程序运行时取的dfs.replication可能是hdfs-default.xml里的 dfs.replication，默认是3。可能这个就是造成你为什么dfs.replication老是3的原因。你可以试试在创建文件时，显式设定replication。replication一般到3就可以了，大了意义也不大。</div>
<div> </div>
<div>转载请注明出处【 <a href="http://sishuok.com/forum/blogPost/list/5936.html#19653" rel="nofollow">http://sishuok.com/forum/blogPost/list/5936.html#19653</a>】</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
            </div>
                </div>