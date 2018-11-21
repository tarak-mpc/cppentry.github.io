---
layout:     post
title:      (一) Hadoop之HDFS
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/zmj132113/article/details/48392801				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
Hadoop是Google的集群系统的开源实现<br>
  -- Google集群系统：GFS（Google File System），MapReduce，BigTable<br>
  -- Hadoop主要由HDFS(Hadoop Distributed File System Hadoop分布式文件系统) MapReduce和HBase组成<br>
  -- Hadoop的初衷是为了解决Nutch的海量数据爬取和存储需要<br>
  -- Hadoop与2005年秋天作为Lucene的子项目Nutch的一部分正式引入Apache基金会<br><br><br>
适用范围<br>
  -- 数据仓库和离线数据分析MPP Hadoop/HBase<br>
  -- 大规模在线实时应用（单行事务处理能满足的场景）（HBase）<br><br><br><strong>NameNode（NN）</strong><br>
  -- NameNode主要功能：接受客户端的读写服务<br>
  -- NameNode保存metadate信息包括<br>
  * 文件ownership和permission<br>
  * 文件包含哪些块<br>
  * Block保存在哪个DataNode（由DataNode启动时上报）<br>
  -- NameNode的metadate信息在启动后会加载到内存<br>
  * metadata存储到磁盘文件名为fsimage<br>
  * Block的位置信息不会保存到fsimage，会保存到内存中<br>
  * edits记录对metadata的操作日志<br><br><br><strong>SecondaryNameNode(SNN)</strong><br>
  -- 他不是NN的备份（但可以做备份），它的主要工作是帮助NN合并edits log，减少NN启动时间<br>
  -- SNN执行合并的时机<br><span></span>* 根据配置文件设计的时间间隔fs.checkpoint.period 默认3600秒<br><span></span>* 根据配置文件eidits log大小fs.checkpoint.size规定edits文件的最大值默认是64MB<br>
 <span> </span>* 合并流程<br><strong>DataNode(DN)</strong><br>
  -- 存储数据<br>
  -- 启动DN线程的时候会向NN汇报block信息<br>
  -- 通过向NN发送心跳保持与其联系（3秒一次），如果NN 10 分钟没有收到DN的心跳，则认为是lost，<br>
     并copy其上的block到其它的DN<br><strong>Block的副本放置策略</strong><br>
  -- 第一个副本：放置在上传文件的DN如果是集群外提交，则随机挑选一台磁盘不太满，CPU不太忙的节点<br>
  -- 第二个副本：放置在与第一个副本不同的机架上的及节点<br>
  -- 第三个副本：放置在同第二个副本相同机架上的节点<br><br><br><strong>HDFS读流程</strong><br>
  -- 文字描述<br><span></span>HDFS Client 通过DistributeFileSystem的open方法去访问NameNode，NameNode通过getBlockLocations<br><span></span>获取得到文件的位置信息。当HDFS Client获取得到block存在于哪一台服务器上的时候，就通过FSDataInputStream<br><span></span>从DataNode上读取block的数据，最后读完数据，FSDataInputStream会close，关闭IO流。<br><strong>HDFS写流程</strong><br>
  -- 文字描述<br><span></span>HDFS Client通过DsitributeFileSystem的create方法去告诉NameNode，该文件名是什么，文件大小是多少，拥有者<br><span></span>是谁。NameNode会根据文件的大小来计算，应该切多少个block。然后，NameNode就会将这些数据信息返回。<br><span></span>接着，HDFS Client就会调用FSDataOutuputStream的weite方法在其中的一个DataNode上写入一个block，然后，DataNode<br><span></span>会根据block的存放的策略，开启一个线程在其他的一个DataNode复制，完成写的操作之后，就会执行流的关闭。<br><span></span>最后DistributedFileSystem会调用complete函数，告诉NamaNode已经完成工作。<br><br><br><strong>HDFS文件权限</strong><br>
  -- 与Linux文件权限类似<br><span></span>r--read; w--write;x--execute,权限对于文件忽略，对于文件夹表示是否允许访问其内容。<br>
  -- 如果Linux系统用户zhangsan使用hadoop命令创建一个文件，那么这个文件在HDFS中owner就是zhangsan。<br>
  -- HDFS的权限目的：阻止好人做错事，而不是阻止坏人做坏事。HDFS相信，你告诉我你是谁，我就认为你是谁。<br>
     说白了，就是不做密码的认证。<br><br><br><strong>安全模式</strong><br>
  -- 当Hadoop处于安全模式的时候，是不能对文件进行操作的，包括读取数据也是不行的。只能够查看目录。 上传文件也不行。<br>
  -- 一旦在内存中成功建立文件系统元数据的映射，则创建一个新的fsimage文件（这个操作不需要SecondaryNameNode）<br>
     和一个空的编辑日志<br>
  -- 此刻NameNode运行在安全模式，即namenode的文件系统对于客服务器端来说是只读的。<br>
     （显示目录，显示文件内容等。写，删除，重命名都会失败）。<br>
  -- 在此阶段NameNode收集各个datanode的报告，当数据块达到最小副本数以上时，会被认为是安全的，在一定的比例的数据块<br>
     被确定为安全后，再过若干时间，安全模式结束。<br>
  -- 当检测到副本数不足的数据块时，该快会被复制知道达到最小副本数，系统中数据块的位置并不是nanenode维护的，<br>
     而是以块列表形式存储在datanade中。<br><p>  -- 一般启动Hadoop或者重启Hadoop的时候就会进入安全模式。不建议强行退出Hadoop的安全模式。</p>
<p><br></p>
<p><br></p>
<p><br></p>
            </div>
                </div>