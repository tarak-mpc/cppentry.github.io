---
layout:     post
title:      hadoop-impala十大优化之（8）—impala优化之HDFS缓存最佳实践
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/seeyouc/article/details/53782539				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h2><a name="_Toc469313434">1.1  Hadoop-impala</a>十大优化之（8）—impala优化之HDFS缓存最佳实践</h2>
<p><img src="https://img-blog.csdn.net/20161221114037859?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc2VleW91Yw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p>1)       HDFS缓存的Impala的概述 </p>
<p>2)       设置缓存为HDFS的Impala </p>
<p>3)       使用HDFS的Impala表和分区缓存 </p>
<p>4)       加载和HDFS启用缓存删除数据 </p>
<p>5)       HDFS的缓存管理和Impala </p>
<p>6)       HDFS的缓存与Impala性能考虑</p>
<p>背景资料：如何建立和管理一个CDH HDFS集群缓存，看CDH文档。</p>
<h3><a name="_Toc469313435">1.1.1  HDFS</a>缓存的impala的概述</h3>
<p> CDH 5.1高，Impala可以使用缓存功能更有效的利用内存的HDFS，这样反复查询可以利用数据“钉”在记忆中无论多少数据进行整体。HDFS的缓存功能允许您指定的一个子集的频繁访问的数据被永久的记忆，其余的在多个查询缓存不被驱逐。该技术适用于表或分区是经常访问的，小到可以完全在HDFS存储缓存。例如，您可以指定要在缓存中固定的几个维度表，以加快引用它们的许多不同的联接查询。或在一个分区表，你可能销分区保存数据，从最近一段时间因为数据将查询集中；然后下一组数据时，你可以脱离以前的分区和分区的新数据持销。</p>
<p> 因为这辆车的性能特征依赖于HDFS的基础设施，它只适用于Impala表使用HDFS的数据文件。HDFS缓存Impala不适用于HBase表，S3表、Kudu表，或 Isilon 表。</p>
<h3><a name="_Toc469313436">1.1.2  设置缓存为HDFS</a>的Impala
</h3>
<p>使用HDFS的缓存与Impala，首先建立你的CDH聚类特征： </p>
<p> 决定把每个主机上的HDFS的缓存内存的多少。请记住，可用的缓存数据的总内存是所有主机上的缓存大小的总和。默认情况下，任何数据块只缓存在一个主机上，虽然您可以通过增加复制因子来缓存一个跨多个主机的块。</p>
<p> 问题cacheadmin HDFS命令来设置一个或多个缓冲池，由同一用户为impalad守护进程（通常是Impala）。例如:</p>
<div style="background:#F0F0F0;">
<p align="left"><span style="color:#444444;">hdfscacheadmin -addPool four_gig_pool -owner impala -limit 4000000000</span></p>
</div>
<p>关于HDFS cacheadmin命令的详细信息，参见CDH文档。 </p>
<p> 一旦HDFS启用高速缓存和一个或多个池，看到使HDFS的Impala表和分区缓存如何选择Impala的数据加载到HDFS的缓存。在Impala的一面，你指定的缓冲池的名字在Impala的DDL语句使HDFS为表或分区缓存HDFScacheadmin命令定义，如创建表…在池或更改表中缓存…集合缓存池。</p>
<p> </p>
<h3><a name="_Toc469313437">1.1.3  使用HDFS</a>的Impala表和分区缓存</h3>
<p>首先通过选择要缓存的表或分区。例如，这些可能是由许多不同的连接查询访问的查找表，或对应于由不同的报告或临时查询分析的最新的时间段的分区。</p>
<p> 在你的SQL语句，您指定的逻辑分区如表和分区缓存。Impala将这些请求到HDFS级指令适用于特定的目录和文件。例如，给定一个分区键列的分区表普查，您可以选择缓存所有或部分数据如下：</p>
<p> 在Impala 2.2 /CDH5.4高，可选复制条款创建表和修改表允许您指定一个复制因子，缓存相同的数据块上的主机的数量。当Impala过程缓存的数据块，其中缓存复制因子大于1，Impala随机选择一个主机，一个数据块的缓存副本。这种优化避免过多的在同一个主机上的多个处理器的使用，当相同的缓存数据块被处理的倍数。Cloudera建议指定一个值大于或等于HDFS块复制因子。</p>
<div style="background:#F0F0F0;">
<p align="left"><span style="color:#444444;">-- Cachethe entire table (all partitions).</span></p>
<p align="left"><span style="color:#444444;">alter tablecensus set cached in '</span><em><span style="color:#444444;">pool_name</span></em><span style="color:#444444;">';</span></p>
<p align="left"><span style="color:#444444;"> </span></p>
<p align="left"><span style="color:#444444;">-- Removethe entire table from the cache.</span></p>
<p align="left"><span style="color:#444444;">alter tablecensus set uncached;</span></p>
<p align="left"><span style="color:#444444;"> </span></p>
<p align="left"><span style="color:#444444;">-- Cache aportion of the table (a single partition).</span></p>
<p align="left"><span style="color:#444444;">-- If thetable is partitioned by multiple columns (such as year, month, day),</span></p>
<p align="left"><span style="color:#444444;">-- theALTER TABLE command must specify values for all those columns.</span></p>
<p align="left"><span style="color:#444444;">alter tablecensus partition (year=1960) set cached in '</span><em><span style="color:#444444;">pool_name</span></em><span style="color:#444444;">';</span></p>
<p align="left"><span style="color:#444444;"> </span></p>
<p align="left"><span style="color:#444444;">-- Cache the data from one partition on up to 4 hosts, tominimize CPU load on any</span></p>
<p align="left"><span style="color:#444444;">-- single host when the same data block is processedmultiple times.</span></p>
<p align="left"><span style="color:#444444;">alter table census partition (year=1970)</span></p>
<p align="left"><span style="color:#444444;">  set cached in '<em>pool_name</em>'with replication = 4;</span></p>
<p align="left"><span style="color:#444444;"> </span></p>
<p align="left"><span style="color:#444444;">-- At eachstage, check the volume of cached data.</span></p>
<p align="left"><span style="color:#444444;">-- Forlarge tables or partitions, the background loading might take some time,</span></p>
<p align="left"><span style="color:#444444;">-- so youmight have to wait and reissue the statement until all the data</span></p>
<p align="left"><span style="color:#444444;">-- hasfinished being loaded into the cache.</span></p>
<p align="left"><span style="color:#444444;">show tablestats census;</span></p>
<p align="left"><span style="color:#444444;">+-------+-------+--------+------+--------------+--------+</span></p>
<p align="left"><span style="color:#444444;">| year  | #Rows | #Files | Size | Bytes Cached |Format |</span></p>
<p align="left"><span style="color:#444444;">+-------+-------+--------+------+--------------+--------+</span></p>
<p align="left"><span style="color:#444444;">| 1900  | -1   | 1      | 11B  | NOT CACHED  | TEXT   |</span></p>
<p align="left"><span style="color:#444444;">| 1940  | -1   | 1      | 11B  | NOT CACHED  | TEXT   |</span></p>
<p align="left"><span style="color:#444444;">| 1960  | -1   | 1      | 11B  | 11B         | TEXT   |</span></p>
<p align="left"><span style="color:#444444;">| 1970  | -1   | 1      | 11B  | NOT CACHED  | TEXT   |</span></p>
<p align="left"><span style="color:#444444;">| Total |-1    | 4      | 44B | 11B          |        |</span></p>
<p align="left"><span style="color:#444444;">+-------+-------+--------+------+--------------+--------+</span></p>
</div>
<p>创建表的考虑：</p>
<p> HDFS缓存功能影响Impala创建表的语句如下： </p>
<p> 你可以把一个缓存的pool_name”条款和可选的复制= number_of_hosts条款在CREATE TABLE语句自动缓存表的全部内容，包括任何分区的后面添加。是的pool_name池以前设置了HDFS cacheadmin命令。</p>
<p> 一旦一个表指定缓存通过HDFS创建表的语句，如果新分区添加后通过修改表…添加分区语句，将这些新分区中的数据自动缓存在同一个池中。</p>
<p> 如果你想在从一个大的表格数据的子集进行重复查询，而不是对HDFS缓存指定整个表或特定的分区实用，你可以创建一个新的缓存表是数据的一个子集，利用创建表…缓存在pool_name”选择…哪里....当您完成从这个子集的数据生成报告，删除表和数据文件和缓存在内存中的数据被自动删除。</p>
<p>其他内存考虑：</p>
<p> 某些DDL操作，如修改表…设置位置，而底层的HDFS的目录包含缓存文件受阻。你必须uncache文件第一，改变位置之前，删除表，等等。</p>
<p> 当请求被固定在内存中时，该进程发生在后台，而不阻塞访问数据，而缓存正在进行中。从磁盘加载数据可能需要一段时间。ImpalaHDFS数据块从内存中读取每一个如果已经把已经，或从磁盘如果没有寄托呢。当文件被添加到一个表或分区的内容进行缓存，impala自动检测这些变化和执行自动刷新一次相关数据缓存。</p>
<p> 你可以销每个节点通过缓存机制是受配额是由底层的HDFS服务执行HDFS的数据量。在请求一个内存中的impala表或分区销，检查它的大小不超过此限额。 </p>
<p> 注：由于HDFS缓存由组合的记忆从集群中所有的数据节点，缓存表或分区可以大于HDFS缓存在任何单一主机的数量。</p>
<h3><a name="_Toc469313438">1.1.4  加载和HDFS</a>启用缓存删除数据</h3>
<p>当HDFS缓存启用，额外的处理发生在的背景，当你添加或删除数据通过报表，如插入和删除表。</p>
<p> 插入或加载数据： </p>
<p> Impala执行表或分区缓存插入或加载数据表时，新的数据文件自动缓存和Impala自动承认事实。 </p>
<p> 如果你执行插入或加载数据通过hive，一如既往，Impala只承认新的数据文件后刷新在Impala table_name声明。 </p>
<p> 如果缓存池完全是满的，或已满之前，所有请求的数据可以缓存，Impala的DDL语句将返回一个错误。这是为了避免情况下，只有一些所请求的数据可以被缓存。 </p>
<p> 当HDFS缓存是一个表或分区启用，新的数据文件缓存时自动添加到HDFS相应的目录，无需刷新语句在Impala的需要。Impala自动执行刷新一次新的数据加载到HDFS的缓存。</p>
<p>丢弃表、分区或缓存池：</p>
<p> HDFS缓存功能的Impala表相互作用并改变表…删除分区语句如下： </p>
<p> 当你发出了一个表，完全缓存表，或有一些分区缓存，删除表成功，所有的缓存指令提交表Impala从HDFS系统缓存删除。 </p>
<p> 同样适用于修改表…删除分区。操作成功，并删除任何缓存指令。</p>
<p> 和总是一样，如果删除表是一个内部表，或者删除的分区在其内部表的默认位置，则删除基本的数据文件。如果删除表是一个外部表，或如果丢弃的分区在非默认位置，则单独留下数据文件。</p>
<p> 如果你指定的数据文件缓存通过HDFS cacheadmin命令和数据文件在以前的项目描述留守，保持缓存数据文件。Impala只删除提交的Impala通过创建表的缓存指令或ALTER TABLE语句。可以有多个冗余的高速缓存指令属于同一个文件；指令都有独特的身份标识和所有者，使系统可以告诉他们分开。</p>
<p> 如果你把一个HDFS的缓存池通过HDFScacheadmin命令，所有的Impala的数据文件保存，只是不再缓存。随后刷新后，显示表的统计报告0字节缓存每个相关的Impala表或分区。</p>
<p> 将一个表或分区： </p>
<p> HDFS缓存功能与Impala的交互修改表…设置位置语句如下： </p>
<p> 如果您指定了一个表或分区，通过创建表或更改表语句缓存，随后试图通过一个更改表重新定位表或分区…设置位置语句将失败。您必须发出一个更改表…设置被声明为表或分区第一。否则，会失去一些Impala缓存数据文件，没有办法uncache以后。</p>
<h3><a name="_Toc469313439">1.1.5  HDFS</a>的缓存管理和Impala
</h3>
<p>HDFS的缓存管理和Impala</p>
<p>这是指导方针和步骤来检查或更改HDFS数据缓存状态的Impala：</p>
<p>HDFScacheadmin命令：</p>
<p>如果你把一个缓冲池与HDFS cacheadmin命令，对相关的数据文件的Impala查询仍然会工作，通过落回从磁盘中读取文件。执行刷新后放在桌上，Impala报道字节缓存0所有相关表和分区数。</p>
<p>你可以使用HDFS cacheadmin得到一个现有的缓存池，该池或详细信息，如下：</p>
<div style="background:#F0F0F0;">
<p align="left"><span style="color:#444444;">hdfscacheadmin -listDirectives         #Basic info</span></p>
<p align="left"><span style="color:#444444;">Found 122entries</span></p>
<p align="left"><span style="color:#444444;">  ID POOL      REPL EXPIRY  PATH</span></p>
<p align="left"><span style="color:#444444;"> 123 testPool      1 never  /user/hive/warehouse/tpcds.store_sales</span></p>
<p align="left"><span style="color:#444444;"> 124 testPool      1 never  /user/hive/warehouse/tpcds.store_sales/ss_date=1998-01-15</span></p>
<p align="left"><span style="color:#444444;"> 125 testPool      1 never  /user/hive/warehouse/tpcds.store_sales/ss_date=1998-02-01</span></p>
<p align="left"><span style="color:#444444;">...</span></p>
<p align="left"><span style="color:#444444;"> </span></p>
<p align="left"><span style="color:#444444;">hdfscacheadmin -listDirectives -stats  # Moredetails</span></p>
<p align="left"><span style="color:#444444;">Found 122entries</span></p>
<p align="left"><span style="color:#444444;">  ID POOL      REPL EXPIRY  PATH                                                       BYTES_NEEDED  BYTES_CACHED  FILES_NEEDED FILES_CACHED</span></p>
<p align="left"><span style="color:#444444;"> 123 testPool      1 never  /user/hive/warehouse/tpcds.store_sales                                 0             0             0             0</span></p>
<p align="left"><span style="color:#444444;"> 124 testPool      1 never  /user/hive/warehouse/tpcds.store_sales/ss_date=1998-01-15         143169        143169             1             1</span></p>
<p align="left"><span style="color:#444444;"> 125 testPool      1 never  /user/hive/warehouse/tpcds.store_sales/ss_date=1998-02-01         112447        112447             1             1</span></p>
<p align="left"><span style="color:#444444;">...</span></p>
</div>
<p>ImpalaSHOW 语句： </p>
<p> 每个表或分区，显示表数据或显示分区表显示当前缓存的字节缓存功能的HDFS的数量。如果没有为该表或分区放置的缓存指令，则不会缓存结果集显示的结果集。一个值为0，或一个较小的数字比表或分区的整体大小，表明缓存请求已提交，但数据还没有完全加载到内存中。查看显示详细信息。</p>
<p> Cloudera管理：</p>
<p> 您可以启用或禁用缓存通过HDFS Cloudera管理，使用配置设置最大内存用于HDFS的缓存服务。这种控制集dfs_datanode_max_locked_memoryHDFS配置参数，它指定缓存大小对HDFS的每个节点的上限。</p>
<p> 所有的缓存设置HDFS的其他操作，如文件的缓存，通过命令行完成，无论是Impala的DDL语句或Linux HDFS cacheadmin命令。 </p>
<p> Impala的内存限制：</p>
<p> Impala HDFS缓存功能与Impala的内存限制为相互作用如下： </p>
<p> 每个HDFS的缓存池的最大大小是指定外部的Impala，通过HDFScacheadmin命令。 </p>
<p> 所有的内存用于缓存从HDFS impalad守护进程地址空间的分离与不计入的mem_limit启动选项的限制，mem_limit查询选项，或进一步限制通过纱线资源管理或Linux cgroups机制。</p>
<p> 因为访问HDFS的缓存数据避免了内存到内存复制操作，包括缓存数据的查询需要在Impala边记忆比缓存数据的等效查询不。除了在一个单一的用户环境中的任何性能优势，减少内存有助于提高高并发工作负载下的可扩展性。</p>
<h3><a name="_Toc469313440">1.1.6  HDFS</a>的缓存与Impala性能考虑</h3>
<p>在 Impala 1.4.0及更高的版本， Impala 支持高效的读取，被固定在内存中缓存数据通过HDFS。 Impala 利用HDFS API和从存储器读取数据而不是从磁盘的数据文件是否在使用Impala DDL语句，或使用命令行机制您指定HDFS路径。</p>
<p> 当你检查impala-shell 汇总命令的输出，或期待中的impalad守护进程的报告，你看到多少字节从HDFS缓存读取。例如，这是从查询资料说明，所有的数据读取一个特定的查询阶段来自HDFS的缓存，因为bytesread和bytesreaddatanodecache值是相同的。</p>
<div style="background:#F0F0F0;">
<p align="left"><span style="color:#444444;">HDFS_SCAN_NODE(id=0):(Total: 11s114ms, non-child: 11s114ms, % non-child: 100.00%)</span></p>
<p align="left"><span style="color:#444444;">        - AverageHdfsReadThreadConcurrency:0.00</span></p>
<p align="left"><span style="color:#444444;">        - AverageScannerThreadConcurrency:32.75</span></p>
<p align="left"><strong><span style="color:#444444;">       - BytesRead: 10.47 GB (11240756479)</span></strong></p>
<p align="left"><strong><span style="color:#444444;">       - BytesReadDataNodeCache: 10.47 GB (11240756479)</span></strong></p>
<p align="left"><span style="color:#444444;">        - BytesReadLocal: 10.47 GB(11240756479)</span></p>
<p align="left"><span style="color:#444444;">        - BytesReadShortCircuit: 10.47 GB(11240756479)</span></p>
<p align="left"><span style="color:#444444;">        - DecompressionTime: 27s572ms</span></p>
</div>
<p>对于涉及较小的数据查询，或在单用户的工作负载，您可能没有注意到一个与或查询的响应时间差异没有HDFS缓存。即使HDFS缓存关闭，对于查询的数据可能仍然在Linux操作系统的缓存。的好处变得更清晰的数据量的增加，特别是随着系统处理更多的并发查询。HDFS的缓存可以提高整体系统的可扩展性。那就是，它可以防止查询性能下降时的工作量超过了Linux操作系统的缓存容量。</p>
<p> 由于HDFS的局限，零拷贝读取不支持加密。不建议使用HDFS Cloudera在加密区Impala数据文件缓存。查询在查询执行过程中返回到正常的读取路径，这可能会导致一些性能开销。</p>
<p>选择的考虑：</p>
<p>ImpalaHDFS缓存功能与SELECT语句和查询性能如下：</p>
<p>Impala自动从内存读取任何数据，已被指定为缓存和实际加载到HDFS的缓存。（它可能需要一段时间后，初始请求完全填充缓存的表与大尺寸或多个分区）的加速比来自两个方面：从内存读取，而不是磁盘，并访问数据直接从高速缓存区，而不是从一个内存区复制到另一个。这第二个方面产生进一步的性能改进的标准的操作系统缓存机制，这仍然会导致内存复制缓存数据的内存。</p>
<p>对于少量的数据，查询加速可能不明显，在墙上的时钟时间。的性能可能与HDFS的缓存打开或关闭大致相同，由于最近使用的数据是在Linux操作系统的缓存举行。差异更为明显：</p>
<p>数据卷（对于同时运行的所有查询）超过了超高速缓存的大小。</p>
<p>一个繁忙的集群运行许多并发查询，其中在内存中的复制和整体内存使用的内存减少在查询结果中更大的可扩展性和吞吐量。</p>
<p>因此，要真正在开发环境中练习和基准此功能，您可能需要模拟现实的工作负载和与您的生产环境相匹配的并发查询。</p>
<p>在轻负载模拟系统的工作量的方法之一是冲洗操作系统缓冲区高速缓存（每个DataNode）对相同的表或分区之间的迭代查询：</p>
<div style="background:#F0F0F0;">
<p align="left"><span style="color:#444444;">$ sync</span></p>
<p align="left"><span style="color:#444444;">$ echo 1&gt; /proc/sys/vm/drop_caches</span></p>
</div>
<p>Impala的查询利用HDFS的缓存数据无论是否缓存指令是由Impala或外部通过HDFS cacheadmin发出的命令，例如外部表的缓存数据文件可能是由几个不同的Hadoop组件访问。</p>
<p>如果您的查询返回一个大的结果集，则报告查询的时间可能会被打印在屏幕上的结果所需的时间所占。为了衡量标的查询时间，查询的结果集的count()大，不一样的处理只能打印一条直线到屏幕。</p>
            </div>
                </div>