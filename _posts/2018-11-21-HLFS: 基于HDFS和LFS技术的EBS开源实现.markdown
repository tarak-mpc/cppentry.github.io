---
layout:     post
title:      HLFS: 基于HDFS和LFS技术的EBS开源实现
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<span><span>HLFS（<span>HDFS Log Structured FileSystem）</span>是一个开源EBS系统，隶属于《谁来拯救云计算》一文作者康华所发起的cloudxy项目。 HDFS最大的特色是结合了LFS和HDFS， HDFS提供了可靠、随时可扩展的文件服务，而LFS弥补了HDFS不能随机更新的缺憾。 HDFS一个较为成熟的项目，为HLFS提供可靠的、可扩展的存储服务，  大大简化了HLFS的设计。在HLFS中，虚拟磁盘对应一个文件，
 文件长度能够超过TB级别，客户端支持Linux和Xen，其中linux基于NBD实现， xen基于blktap2实现，客户端通过类posix接口libHLFS与服务端通讯。  HLFS主要特性有： 支持多副本，支持动态扩容，支持故障透明处理，支持快照。<br style="line-height:28px;"></span><br style="line-height:28px;"><span style="line-height:37px;font-size:21px;"><strong>数据分片和定位</strong><br></span>每个虚拟磁盘对应一个HLFS文件，HLFS文件由多个段组成，段是HDFS独立文件默认为64M。 <span><span>数据分配和定位由HDFS提供，系统</span>本身不需要设计数据分片和定位相关算法。</span></span>
<div style="line-height:28px;color:rgb(51,51,51);font-family:'微软雅黑';font-size:16px;">
<br><div><span style="line-height:37px;font-size:21px;"><strong>数据存储</strong></span>
<div><span style="font-family:'微软雅黑';">HLFS中，文件即日志，日志即文件，文件的更新全是已追加方式记录到日志末尾。</span></div>
从物理上看，日志由多个固定长度的段组成， 这些段头尾相连组成一个线性的log，段是HDFS中的独立文件，文件名是segno.seg，其中segno是从0开始递增的编号。
<div><span style="font-family:'微软雅黑';">从逻辑上看，日志由一些列日志项组成，日志项不能跨段。 除了日志项的头部之外， 日志项的格式包括 A (data）| B (meta-data block log) | C (inode log) | D(inode_map log)， 其中data是被修改的数据块，如果data包含多个数据块，那么这些数据块必须连续， meta-data block log是由于被修改的索引块， inode log是新的inode， inode_map记录inode位置信息（只包括一个inode）。
 inode_map是定位数据块和inode的关键数据结构， HLFS打开文件时必须得到最新的inode_map信息。定位inode_map的方法如下：</span> HLFS从日志末尾（最后一个段的末尾）读取inode和inode_map。与经典LFS系统不同， inode_map仅包含一个inode， 而从日志末尾可直接读到inode， 因此inode_map结构其实没有存在的必要， 留着inode_map是便于将来扩展成单个日志文件支持多文件。  </div>
<div>LFS系统中的一个重要的问题是定位日志末尾的算法， 最简单的办法是根据文件长度确定日志末尾， 但是这种方法无法跳过最后一个不完整的日志项（日志项可能只写入了一半）。这个问题通用的解决办法是增加检查点机制，  从检查点顺序扫描日志， 遇到不完整的日志（通过checksum、日志项中的其它元数据判断）就认为日志结束，并且将不完成的日志truncate掉。</div>
<div><span style="font-family:'微软雅黑';"><br></span></div>
<div>垃圾回收由用户手工发起， 具体分为两个步骤，首先分析段的利用率（即数据块存活率），写入segment_usage。 分析利用率有两种方法，一种是客户端计算，另外一种map/reduce计算。 如果段利用率低于某以阈值， 则启动真正的垃圾回收。 垃圾回收是将段中有效的日志项重新写入到日志末尾。 一个日志项中可能包含有效或者失效的数据块，必须严格区分这两者，只能将日志项中的有效数据重新写入日志末尾，否则可能导致丢失更新。</div>
<div><br></div>
<div>LFS类系统非常容易实现快照功能——只需将当时的inode位置记录到snapshot文件。</div>
<div><br></div>
<div><strong><span>集群成员关系</span><br style="line-height:37px;font-size:21px;"></strong><span>HDFS维护集群成员关系，处理节点退出和加入。</span></div>
<div><br></div>
<div><span style="line-height:37px;font-size:21px;"><strong>复制和一致性</strong></span></div>
<div>HDFS保证复制和一致性， HDFS的append操作比GFS复杂， 能够保证一致性。
<div><br></div>
<div><br></div>
<strong><span>性能</span><br style="line-height:37px;font-size:21px;"></strong>
<div><span style="font-family:'微软雅黑';"><br></span></div>
<div>
<div><span><span style="font-family:'微软雅黑';line-height:28px;"> EBS的主要应用是RDS数据库应用，而HLFS的关注点是中小型个人host服务， 性能不是HLFS目前的主要关注点。</span></span></div>
<div><span style="font-family:'微软雅黑';"><span><span style="font-family:'微软雅黑';line-height:28px;"><br></span></span></span></div>
<div><span style="font-family:'微软雅黑';"><span><span style="font-family:'微软雅黑';line-height:28px;">读写IO随机化仍然严重： 首先， 非原地更新必然导致数据块在物理上非连续存放，因此读IO比较随机， 顺序读性能下降。 其次，虽然从单个文件角度看来， 写IO是顺序的，但是在HDFS的chunk server服务了多个HLFS文件， 因此从它的角度来看， IO仍然是随机的。同样构建在HDFS之上的HBase是怎么解决随机IO问题的？
  HBase每个region server负责多个tablet， 每个tablet都需要记录日志到HDFS， 为了提高日志效率， 每个regioin server上的tablet合用一个日志文件。 或许HLFS也借鉴HBase做法进行优化。</span></span></span></div>
<div><span style="font-family:'微软雅黑';"><span><span style="font-family:'微软雅黑';line-height:28px;"><span>写延迟问题， HDFS面向大文件设计， 小文件写延时不够优化。  </span></span></span></span></div>
<div><span style="font-family:'微软雅黑';"><span>垃圾回收的影响， HLFS写日志线程发现没有请求时，会启动垃圾回收， 垃圾回收需要读取和写入大量数据， 对正常写操作造成较大影响。 此外，按照目前实现，相同段上的垃圾回收和读写请求不能并发，  垃圾回收算法对正常操作的干扰较大。</span></span></div>
<div>写日志性能需要进一步优化。  一次只往日志写入一个日志项，一个日志项只能包含连续数据块， 写日志的吞吐率不是很好。</div>
<div><br></div>
</div>
</div>
<div><br></div>
<div><span style="line-height:37px;font-size:21px;"><strong>总结</strong></span></div>
<div>HLFS是基于HDFS和LFS实现的一个开源EBS系统， 得益于HDFS，HLFS的实现难度降低，并拥有三高特性： 高可靠、高可用、高可扩展。只要HLFS能够解决好性能问题， 相信会更加完美。</div>
</div>
<div><br></div>
<div>原文：<a href="http://peterylh.blog.163.com/blog/static/120332012226104116710/" rel="nofollow">http://peterylh.blog.163.com/blog/static/120332012226104116710/</a></div>
</div>
            </div>
                </div>