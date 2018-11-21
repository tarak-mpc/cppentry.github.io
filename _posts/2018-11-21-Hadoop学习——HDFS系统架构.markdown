---
layout:     post
title:      Hadoop学习——HDFS系统架构
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>Hadoop的分布式文件系统HDFS主要是借鉴了Google发表的论文：<a href="http://research.google.com/archive/gfs.html" rel="nofollow"></a><a href="http://research.google.com/archive/gfs.html" rel="nofollow">The Google File System</a>。该文件系统最大的优点是可以利用很多低配的计算机搭建高扩展和高容错性的分布式文件系统，另外，HDFS放宽了POSIX关于I/O的规定，因为HDFS需要解决的是<span>write-once-read-many</span>问题，所以串行化和流处理技术被应用到HDFS中。</p>
<p>HDFS的设计目的主要是下面几个方面：</p>
<ol><li>硬件故障：由于HDFS是由很多低配的计算机构成，所以每个节点发生故障的概率很大，HDFS需要能够保证在硬件发生故障时不会影响数据的正常操作，并且故障节点可以自动恢复；</li><li>流数据处理：主要目的是实现高吞吐率的数据访问；</li><li>大数据集：为了处理大数据集，HDFS需要提供很高的数据访问带宽以及高扩展性；</li><li>简单的一致性模型（coherency model）：只需要满足<span style="font-family:Verdana, Helvetica, sans-serif;color:#000000;font-size:12.7273px;font-style:normal;font-variant:normal;font-weight:normal;letter-spacing:normal;line-height:14.5455px;text-align:left;text-indent:0px;text-transform:none;word-spacing:0px;background-color:rgb(255,255,255);display:inline !important;">write-once-read-many的数据访问模型即可，不需要过多考虑写入操作带来的多节点一致性问题；</span></li><li><span style="font-family:Verdana, Helvetica, sans-serif;color:#000000;font-size:12.7273px;font-style:normal;font-variant:normal;font-weight:normal;letter-spacing:normal;line-height:14.5455px;text-align:left;text-indent:0px;text-transform:none;word-spacing:0px;background-color:rgb(255,255,255);display:inline !important;">迁移计算：HDFS尽可能保证运算与数据处于同一个节点上面，所以当需要处理的数据和计算不在同一个节点时，会将计算迁移而非数据，这也是考虑到数据规模太大；</span></li><li><span style="font-family:Verdana, Helvetica, sans-serif;color:#000000;font-size:12.7273px;font-style:normal;font-variant:normal;font-weight:normal;letter-spacing:normal;line-height:14.5455px;text-align:left;text-indent:0px;text-transform:none;word-spacing:0px;background-color:rgb(255,255,255);display:inline !important;">可移植性：可以为多种应用提供底层存储。<br></span></li></ol><p>在对HDFS的学习中，主要从系统架构、文件系统命名空间、数据备份等等几个方面着手，今天这篇文章的内容是系统架构。</p>
<h1>一、业界调研<br></h1>
<p>在介绍HDFS系统架构之前，我们先来了解一些现有的分布式文件系统架构：</p>
<h2>1. GFS</h2>
<p><img src="http://upload.wikimedia.org/wikipedia/commons/thumb/c/c3/GoogleFileSystemGFS.svg/1000px-GoogleFileSystemGFS.svg.png" alt="" height="274" width="461"></p>
<p>GFS(Google File System)是HDFS的原型，所以在架构设计上很相似。如上图所示，Master和Chunk Server就相当于HDFS总的Name Node和Data Node，详见HDFS的介绍。</p>
<h2>2. Ceph<br></h2>
<p><img src="http://ceph.com/docs/master/_images/stack.png" alt="../_images/stack.png" height="333" width="477"></p>
<p>Ceph文件系统是建立在RADOS基础上的，RADOS本身是一个对象存储系统（<a href="http://ceph.com/papers/weil-rados-pdsw07.pdf" rel="nofollow">RADOS - A Scalable, Reliable Storage Service for Petabyte-scale Storage Clusters</a>），上图展示了Ceph整体的系统结构，Ceph FS相当于在RADOS建立的一个VFS。</p>
<p>对于一个文件，其存储过程如下（参见：<a href="https://www.google.com/url?sa=t&amp;rct=j&amp;q=&amp;esrc=s&amp;source=web&amp;cd=1&amp;cad=rja&amp;sqi=2&amp;ved=0CDAQFjAA&amp;url=http%3A%2F%2Fwww.ssrc.ucsc.edu%2Fpapers%2Fweil-osdi06.pdf&amp;ei=VxHWUfOLPIn0lAWqyYC4Dg&amp;usg=AFQjCNEtT2AFj5U00qGdJTVxCCUOA7dUmw&amp;sig2=NNn9P3A2gx8Ap7pSqWfp2g&amp;bvm=bv.48705608,d.dGI" rel="nofollow">Ceph:
 A Scalable, High-Performance Distributed File System</a>）：</p>
<p><img src="" alt="" height="207" width="431"></p>
<p>对于任意文件，Ceph都会将其切分为多个Object，然后通过Hash分配到PG（Placement Group）中，最后由CRUSH算法将PG分配到一组OSD（Object Storage Disk）上面。</p>
<h2>3.  FhGFS</h2>
<p>FhGFS是由德国ITWM（<span><span> </span></span>the Fraunhofer Institute for Industrial Mathematics）开发的并行文件系统，重点关注的是数据吞吐率。下图展示了它的系统架构（引自：<a href="http://www.fhgfs.com/wiki/wikka.php?wakka=SystemArchitecture" rel="nofollow">http://www.fhgfs.com/wiki/wikka.php?wakka=SystemArchitecture</a>）：</p>
<p><img src="http://www.fhgfs.com/wiki/images/sysarch2.png" alt="FhGFS System Architecture"></p>
<p>主要包括三个部分：Client、Metadata Servers、Storage Servers。注意这三个部分可能并不是相互独立的节点，同一个节点上面可以同时运行两个或三个角色。除了这三个角色以外，还包括Management Serveic和可选的<span>administration and monitoring service</span>，前者负责管理全局的配置信息，后者负责为系统安装和监控提供可视化的前台图形展示。<br></p>
<h2>4. GlusterFS</h2>
<p>Gluster和Ceph很相似，都是开源系统，都是基于低配计算机搭建，节点间都可以相互备份，并且都是通过算法确定数据的存储位置。但它们也有区别，主要包含以下几点，比如GlusterFS使用环形一致性哈希策略，而Ceph使用CRUSH算法；Gulster只包含一种类型的服务节点，而Ceph有两种。这里不对GulsterFS做过多介绍。</p>
<h2>5. Lustre<br></h2>
<p>Lutre常用在超级计算机中，比如著名的泰坦（前不久刚被中国的天河二号打败），它具有很高的扩展性，它可以由上万节点的多个集群构成，存储数据可以达到数十PB级，吞吐率可以达到每秒TB级。下图展示了其系统结构：</p>
<p><img src="https://img-blog.csdn.net/20130705203458531?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveWFuZ25pbmc1ODUw/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="" height="438" width="582"><br></p>
<h1>二、HDFS<br></h1>
<p>HDFS跟Google的DFS很像，见下图：</p>
<p><img src="https://img-blog.csdn.net/20130705203624187?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveWFuZ25pbmc1ODUw/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="" height="361" width="553"><br></p>
<p>可以看出DFS中的Master和Chunk Server对应这里的Namenode和Datanode，Namenode负责维护这个系统的元数据信息，包括命名空间、数据块的位置信息等等；Datanode主要负责存储数据和备份其它Datanode的数据，关于数据一致性维护和备份机制在后面的文章中进行总结。</p>
<p>HDFS采取这种Namenode控制全局元数据信息，主要目的是为了实现数据的高可用性和一致性，但同时增加了SPoF（Single Point of Failure）风险的，所以一般情况下还会增加SecondNamenode，作为Namenode的备份。</p>
<p>另外，上图展示的Namenode和Datanode以及SecondNamenode都不一定对应一个节点，它们其实是不同的系统进程，很多时候可以共用一个节点，尤其是搭建伪分布环境。</p>
<p>对于任何数据，HDFS都会将其分割成固定大小的块（默认64M）进行存储，这样的好处一方面是为了避免操作系统对于大文件的限制，另一方面也是考虑到数据的迁移和备份的方便，便于对数据进行并行处理，这也是HDFS支持MapReduce的优势所在。</p>
<p>今天先写到这，待续。。。<br></p>
<p><br></p>
            </div>
                </div>