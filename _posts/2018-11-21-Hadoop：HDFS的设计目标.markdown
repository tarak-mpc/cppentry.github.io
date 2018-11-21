---
layout:     post
title:      Hadoop：HDFS的设计目标
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p style="text-indent:24px;font-family:Simsun;font-size:13px;">
Hadoop培训内容：HDFS的设计目标，HDFS作为Hadoop的分布式文件存储系统和传统的分布式文件系统有很多相同的设计目标。例如，在可伸缩性及可用性上。但是HDFS的设计前提是假设和较早的文件系统有着明显的不同之处。下面简述HDFS的设计思路和目标。<span style="font-size:12px;">来源：</span><a href="http://www.cuug.com.cn/" rel="nofollow" style="font-size:12px;">CUUG官网</a></p>
<p style="text-indent:24px;font-family:Simsun;font-size:13px;">
<span>1.硬件错误</span></p>
<p style="text-indent:24px;font-family:Simsun;font-size:13px;">
硬件组件错误是常态，而非异常情况。HDFS可能由成百上千的服务器组成，每一个服务器都是廉价通用的普通硬件，任何一个组件都有可能一直失效，因此错误检测和快速、自动恢复是HDFS的核心架构目标，同时能够通过自身持续的状态监控快速检测冗余并回复失效的组件。</p>
<p style="text-indent:24px;font-family:Simsun;font-size:13px;">
<span>2.流式数据访问</span></p>
<p style="text-indent:24px;font-family:Simsun;font-size:13px;">
运行在HDFS上的应用和普通的应用不同，需要流式访问它们的数据集。HDFS的设计中更多考虑到了数据批处理，而不是用户交互处理。相比数据访问的低延迟，HDFS应用要求能够高速率、大批量地处理数据，极少有程序对单一的读写操作有严格的响应时间要求，更关键的问题在于数据访问的高吞吐量。POSIX标准设置的很多硬性约束对HDFS应用系统不是必需的。为了提高数据的吞吐量，在一些关键方面对POSIX的语义做了一些修改。</p>
<p style="text-indent:24px;font-family:Simsun;font-size:13px;">
<span>3.大规模数据集</span></p>
<p style="text-indent:24px;font-family:Simsun;font-size:13px;">
运行在HDFS上的应用具有很大的数据集。HDFS上的一个典型文件，大小一般都在GB至TB。因此，需要调节HDFS以支持大文件存储。HDFS应该能提供整体较高的数据传输带宽，能在一个集群里扩展到数百个节点。一个单一的HDFS实例应该能支撑千万计的文件。</p>
<p style="text-indent:24px;font-family:Simsun;font-size:13px;">
<span>4.简化一致性模型</span></p>
<p style="text-indent:24px;font-family:Simsun;font-size:13px;">
HDFS应用需要一个“一次写入多次读取”的文件访问模型。一个文件经过创建、写入和关闭之后就不需要改变了。这一假设简化了数据一致性问题，并且使高吞吐量的数据访问成为可能。MapReduce应用或网络爬虫应用都非常适合这个模型。目前还有计划在将来扩充这个模型，使之支持文件的附加写操作。</p>
<p style="text-indent:24px;font-family:Simsun;font-size:13px;">
<span>5.移动计算代价比移动数据代价低</span></p>
<p style="text-indent:24px;font-family:Simsun;font-size:13px;">
一个应用请求的计算，离它操作的数据越近就越高效，这在数据达到海量级别的时候更是如此。将计算移动到数据附近，比之将数据移动到应用所在之处显然更好，HDFS提供给应用这样的接口。</p>
<p style="text-indent:24px;font-family:Simsun;font-size:13px;">
<span>6.可移植性</span></p>
<p style="text-indent:24px;font-family:Simsun;font-size:13px;">
HDFS在设计时就考虑到平台的可移植性，这种特性方便了HDFS作为大规模数据应用平台的推广。</p>
            </div>
                </div>