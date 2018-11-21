---
layout:     post
title:      大数据时代之hadoop(四)：hadoop 分布式文件系统（HDFS)
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div class="article_title" style="font-size:20px;line-height:30px;font-family:'Microsoft YaHei';">
<span class="ico ico_type_Original" style="display:inline-block;width:19px;vertical-align:middle;"></span> 
<h1 style="display:inline;font-weight:normal;font-size:20px;vertical-align:middle;">
<span class="link_title"><a href="http://blog.csdn.net/chaofanwei/article/details/40209527" rel="nofollow" style="color:rgb(0,0,0);text-decoration:none;"><span style="color:#FF0000;">[置顶]</span> 大数据时代之hadoop(四)：hadoop 分布式文件系统（HDFS)</a></span></h1>
</div>
<div class="article_manage" style="color:rgb(153,153,153);line-height:24px;font-family:Arial;text-align:right;">
<span class="link_categories">分类： <a href="http://blog.csdn.net/MINEZHANGHAO/article/category/2593909" rel="nofollow" style="color:rgb(202,0,0);text-decoration:none;">hadoop</a> <a href="http://blog.csdn.net/MINEZHANGHAO/article/category/2582075" rel="nofollow" style="color:rgb(202,0,0);text-decoration:none;">大数据</a></span><span class="link_postdate">2014-10-20
 09:46</span> <span class="link_view" title="阅读次数">676人阅读</span> <span class="link_comments" title="评论次数"><a href="http://blog.csdn.net/chaofanwei/article/details/40209527#comments" rel="nofollow" style="color:rgb(202,0,0);text-decoration:none;">评论</a>(1)</span> <span class="link_collect"><a title="收藏" style="color:rgb(202,0,0);">收藏</a></span> <span class="link_report"><a href="http://blog.csdn.net/chaofanwei/article/details/40209527#report" rel="nofollow" title="举报" style="color:rgb(202,0,0);text-decoration:none;">举报</a></span></div>
<div class="tag2box" style="font-family:Arial, Console, Verdana, 'Courier New';">
<a href="http://www.csdn.net/tag/hadoop" rel="nofollow" style="color:rgb(202,0,0);text-decoration:none;display:inline-block;line-height:12px;border:1px solid rgb(238,238,238);background-color:rgb(238,238,238);">hadoop</a><a href="http://www.csdn.net/tag/%E6%96%87%E4%BB%B6%E7%B3%BB%E7%BB%9F" rel="nofollow" style="color:rgb(202,0,0);text-decoration:none;display:inline-block;line-height:12px;border:1px solid rgb(238,238,238);background-color:rgb(238,238,238);">文件系统</a><a href="http://www.csdn.net/tag/hdfs" rel="nofollow" style="color:rgb(202,0,0);text-decoration:none;display:inline-block;line-height:12px;border:1px solid rgb(238,238,238);background-color:rgb(238,238,238);">hdfs</a><a href="http://www.csdn.net/tag/%E5%88%86%E5%B8%83%E5%BC%8F%E6%96%87%E4%BB%B6%E7%B3%BB%E7%BB%9F" rel="nofollow" style="color:rgb(202,0,0);text-decoration:none;display:inline-block;line-height:12px;border:1px solid rgb(238,238,238);background-color:rgb(238,238,238);">分布式文件系统</a></div>
<div style="font-family:Arial, Console, Verdana, 'Courier New';clear:both;"></div>
<div style="font-family:Arial, Console, Verdana, 'Courier New';border:1px solid rgb(204,204,204);background-color:rgb(238,238,238);min-width:200px;">
<p style="text-align:right;">
<span>目录<a href="http://blog.csdn.net/chaofanwei/article/details/40209527#" rel="nofollow" title="系统根据文章中H1到H6标签自动生成文章目录" style="color:rgb(202,0,0);text-decoration:none;">(?)</a></span><a href="http://blog.csdn.net/chaofanwei/article/details/40209527#" rel="nofollow" title="展开" style="color:rgb(202,0,0);text-decoration:none;">[+]</a></p>
</div>
<div style="font-family:Arial, Console, Verdana, 'Courier New';clear:both;"></div>
<div id="article_content" class="article_content" style="font-size:14px;line-height:26px;font-family:Arial;">
<p>
 </p>
<p>
 </p>
<p>
<span class="link_title"><a href="http://blog.csdn.net/chaofanwei/article/details/39553621" rel="nofollow" style="color:rgb(202,0,0);text-decoration:none;">大数据时代之hadoop(一)：hadoop安装</a></span></p>
<p>
<span class="link_title"><a href="http://blog.csdn.net/chaofanwei/article/details/39641361" rel="nofollow" style="color:rgb(202,0,0);text-decoration:none;">大数据时代之hadoop(二)：hadoop脚本解析</a></span></p>
<p>
<span class="link_title"><a href="http://blog.csdn.net/chaofanwei/article/details/39695743" rel="nofollow" style="color:rgb(202,0,0);text-decoration:none;">大数据时代之hadoop(三)：hadoop数据流（生命周期）</a></span></p>
<p>
</p>
<p>
 </p>
<p>
<span style="font-size:18px;">        分布式文件系统即是网络中多台计算机组合在一起提供一个统一存储及管理的系统。Hadoop提供了一个文件系统接口和多个分布式文件系统实现，其中比较重要的就是HDFS（Hadoop Distributed Filesystem）了。Hadoop是一个综合性的文件系统抽象，因此它也可以集成其他文件系统的实现，如本地文件系统和Amazon S3系统及淘宝 TFS等。</span></p>
<p>
<span style="font-size:18px;"></span> </p>
<p>
 </p>
<p>
<br></p>
<h1><a name="t0" style="color:rgb(202,0,0);"></a>1、概念模型</h1>
<p>
<span style="font-size:18px;">         HDFS以流式数据访问模式来存储超大文件，运行于商业硬件集群上。</span></p>
<p>
<span style="font-size:18px;"></span> </p>
<p>
<span style="font-size:18px;">         HDFS实现下来，分为两类节点，一<strong>个是namenode及secondarynode</strong>，主要目的是用于存储系统镜像及备份。</span></p>
<p>
<span style="font-size:18px;"></span> </p>
<p>
<span style="font-size:18px;">         其中namenode将文件系统的元数据存储在内存中（因此该文件系统所能存储的文件总数受制于namenode的内存容量）。它维护着文件系统树及树内的所有文件和目录，这些信息同时以两个文件（命名空间镜像文件和编辑日志文件）的形式永久保存在本地磁盘上。namenode也记录着每个文件的各个块所在的数据节点信息（会在系统启动时有数据节点重建）。</span></p>
<p>
<span style="font-size:18px;"></span> </p>
<p>
<span style="font-size:18px;">         secondarynode的作用是定期通过编辑日志文件合并命名空间镜像，以防止编辑日志过大和namenode发生故障时启用并作为新的namenode，但secondarynamenode状态总是滞后于主节点的。</span></p>
<p>
<span style="font-size:18px;"></span> </p>
<p>
<span style="font-size:18px;">         另一类是datanode，其会有多个，目标就是实现具体的存储，及把文件的每个块给保存到本地磁盘上，和namenode关联起来，共同组成存储。<br></span>  </p>
<p>
<span style="font-size:18px;">       HDFS在存储数据时用的块的概念，<strong>默认每个块大小为64M</strong>（目的是为了最小化磁盘寻址时间）。HDFS上的文件被划分为块大小的多个<strong>分块</strong>（chunk），作为独立的存储单元，但HDFS中小于一个块大小的文件不会占据整个块的空间。因为是分布式文件系统，HDFS也提供了备份的功能，即把文件的每个块都会复制到多个机器上（默认为3个）。</span></p>
<p>
<span style="font-size:18px;"></span> </p>
<h1><a name="t1" style="color:rgb(202,0,0);"></a>2、hadoop文件系统</h1>
<span style="font-size:18px;"></span>
<p>
       <span style="font-size:18px;"> Hadoop有一个抽象的文件系统概念，HDFS只是其中的一个实现。抽象类<strong>org.apache.hadoop.fs.FileSystem</strong>定义了一个文件系统接口，并且该抽象类有几个具体实现。</span></p>
<p>
 </p>
<p>
</p>
<table border="1" cellspacing="0" cellpadding="0"><tbody><tr><td>
<p align="center">
<strong>文件系统</strong></p>
</td>
<td>
<p align="center">
<strong>URI方案</strong></p>
</td>
<td>
<p align="center">
<strong>Java实现</strong></p>
<p align="center">
<strong>（org.apache.hadoop）</strong></p>
</td>
<td>
<p align="center">
<strong>定义</strong></p>
</td>
</tr><tr><td>
<p align="left">
Local</p>
</td>
<td>
<p>
file</p>
</td>
<td>
<p>
fs.LocalFileSystem</p>
</td>
<td>
<p>
支持有客户端校验和本地文件系统。带有校验和的本地系统文件在fs.RawLocalFileSystem中实现。</p>
</td>
</tr><tr><td>
<p align="left">
HDFS</p>
</td>
<td>
<p>
hdfs</p>
</td>
<td>
<p>
hdfs.DistributionFileSystem</p>
</td>
<td>
<p>
Hadoop的分布式文件系统。</p>
</td>
</tr><tr><td>
<p align="left">
HFTP</p>
</td>
<td>
<p>
hftp</p>
</td>
<td>
<p>
hdfs.HftpFileSystem</p>
</td>
<td>
<p>
支持通过<strong>HTTP</strong>方式以<strong>只读</strong>的方式访问HDFS，distcp经常用在<strong>不同</strong>的HDFS<strong>集群间</strong>复制数据。</p>
</td>
</tr><tr><td>
<p align="left">
HSFTP</p>
</td>
<td>
<p>
hsftp</p>
</td>
<td>
<p>
hdfs.HsftpFileSystem</p>
</td>
<td>
<p>
支持通过<strong>HTTPS</strong>方式以<strong>只读</strong>的方式访问HDFS。</p>
</td>
</tr><tr><td>
<p align="left">
HAR</p>
</td>
<td>
<p>
har</p>
</td>
<td>
<p>
fs.HarFileSystem</p>
</td>
<td>
<p>
构建在Hadoop文件系统之上，对文件进行归档。Hadoop归档文件主要用来<strong>减少</strong>NameNode的<strong>内存使用</strong>。</p>
</td>
</tr><tr><td>
<p align="left">
KFS</p>
</td>
<td>
<p>
kfs</p>
</td>
<td>
<p>
fs.kfs.KosmosFileSystem</p>
</td>
<td>
<p>
Cloudstore（其前身是Kosmos文件系统）文件系统是<strong>类似于</strong>HDFS和Google的GFS文件系统，使用C++编写。</p>
</td>
</tr><tr><td>
<p align="left">
FTP</p>
</td>
<td>
<p>
ftp</p>
</td>
<td>
<p>
fs.ftp.FtpFileSystem</p>
</td>
<td>
<p>
由FTP服务器支持的文件系统。</p>
</td>
</tr><tr><td>
<p align="left">
S3（本地）</p>
</td>
<td>
<p>
s3n</p>
</td>
<td>
<p>
fs.s3native.NativeS3FileSystem</p>
</td>
<td>
<p>
基于Amazon S3的文件系统。</p>
</td>
</tr><tr><td>
<p align="left">
S3（基于块）</p>
</td>
<td>
<p>
s3 </p>
</td>
<td>
<p>
fs.s3.NativeS3FileSystem</p>
</td>
<td>
<p>
基于Amazon S3的文件系统，以块格式存储解决了S3的5GB文件大小的限制。</p>
</td>
</tr></tbody></table><p>
</p>
<p>
<span style="font-size:18px;">Hadoop对文件系统提供了许多接口，它一般使用<strong>URI方案来选取合适的文件系统实例</strong>进行交互。</span></p>
<p>
 </p>
<h1><a name="t2" style="color:rgb(202,0,0);"></a>3、HDFS实现</h1>
<p>
<span style="font-size:18px;">        要想实现一个文件系统，除了简单的增删改查文件以及文件夹等简单的操作之外，还需要考虑的有很多，如<strong>数据的完整性、数据是否需要压缩存储，以及如何压缩、存储对象时使用的序列化及反序列化框架</strong>等很多复杂的问题。</span></p>
<p>
<span style="font-size:18px;"></span> </p>
<h2><a name="t3" style="color:rgb(202,0,0);"></a>3.1、数据的完整性</h2>
<span style="font-size:18px;"></span>
<p>
          <span style="font-size:18px;">数据的完整性即指在从文件系统中读取数据时需要和当初存入到文件系统的数据保障一致，是否损坏，而不是残缺的数据。常见的错误检测门是CRC-32(循环冗余检验)，任何大小的数据输入都会计算出一个32位的数据校验和。</span></p>
<p>
<br><span style="font-size:18px;">       HDFS会对写入的所有数据计算校验和，并在读取时验证校验和。它针对每个由io.bytes.per.checksum指定的数据计算校验和。默认情况下为512个字节，由于CRC-32校验和是4个字节，所以存储校验和的额外开销低于1%。</span></p>
<p>
<span style="font-size:18px;"> </span></p>
<p>
<span style="font-size:18px;">       hadoop提供了一个ChecksumFileSystem，这个类继承自FileSystem类，它的主要作用就是通过使用装饰模式为其他文件系统加入校验和模块。</span></p>
<p>
 </p>
<h2><a name="t4" style="color:rgb(202,0,0);"></a>3.2、压缩</h2>
<p>
<span style="font-size:18px;">       文件压缩有两大好处：减少存储文件所需要的磁盘空间；加速数据在网络和磁盘上的传输时间。常见的压缩算法如下：</span></p>
<p>
<span style="font-size:18px;"></span> </p>
<p>
<span style="font-size:18px;"></span></p>
<table border="1" cellspacing="0" cellpadding="0" align="left"><tbody><tr><td>
<p align="left">
<span style="font-size:14px;">压缩格式</span></p>
</td>
<td>
<p align="left">
<span style="font-size:14px;"> 工具</span></p>
</td>
<td>
<p align="left">
<span style="font-size:14px;"> 算法</span></p>
</td>
<td>
<p align="left">
<span style="font-size:14px;"> 文件扩展名</span></p>
</td>
<td>
<p align="left">
<span style="font-size:14px;"> 多文件</span></p>
</td>
<td>
<p align="left">
<span style="font-size:14px;"> 可分割性</span></p>
</td>
</tr><tr><td>
<p align="left">
<span style="font-size:14px;"> DEFLATE</span></p>
</td>
<td>
<p align="left">
<span style="font-size:14px;"> 无</span></p>
</td>
<td>
<p align="left">
<span style="font-size:14px;"> DEFLATE</span></p>
</td>
<td>
<p align="left">
<span style="font-size:14px;"> .deflate</span></p>
</td>
<td>
<p align="left">
<span style="font-size:14px;"> 不</span></p>
</td>
<td>
<p align="left">
<span style="font-size:14px;"> 不</span></p>
</td>
</tr><tr><td>
<p align="left">
<span style="font-size:14px;"> gzip</span></p>
</td>
<td>
<p align="left">
<span style="font-size:14px;"> gzip</span></p>
</td>
<td>
<p align="left">
<span style="font-size:14px;"> DEFLATE</span></p>
</td>
<td>
<p align="left">
<span style="font-size:14px;"> .gz</span></p>
</td>
<td>
<p align="left">
<span style="font-size:14px;"> 不</span></p>
</td>
<td>
<p align="left">
<span style="font-size:14px;"> 不</span></p>
</td>
</tr><tr><td>
<p align="left">
<span style="font-size:14px;"> ZIP</span></p>
</td>
<td>
<p align="left">
<span style="font-size:14px;"> zip</span></p>
</td>
<td>
<p align="left">
<span style="font-size:14px;"> DEFLATE</span></p>
</td>
<td>
<p align="left">
<span style="font-size:14px;"> .zip</span></p>
</td>
<td>
<p align="left">
<span style="font-size:14px;"> 是</span></p>
</td>
<td>
<p align="left">
<span style="font-size:14px;"> 是，在文件范围内</span></p>
</td>
</tr><tr><td>
<p align="left">
<span style="font-size:14px;"> bzip2</span></p>
</td>
<td>
<p align="left">
<span style="font-size:14px;"> bzip2</span></p>
</td>
<td>
<p align="left">
<span style="font-size:14px;"> bzip2</span></p>
</td>
<td>
<p align="left">
<span style="font-size:14px;"> .bz2</span></p>
</td>
<td>
<p align="left">
<span style="font-size:14px;"> 不</span></p>
</td>
<td>
<p align="left">
<span style="font-size:14px;"> 是</span></p>
</td>
</tr><tr><td>
<p align="left">
<span style="font-size:14px;"> LZO</span></p>
</td>
<td>
<p align="left">
<span style="font-size:14px;"> lzop</span></p>
</td>
<td>
<p align="left">
<span style="font-size:14px;"> LZO</span></p>
</td>
<td>
<p align="left">
<span style="font-size:14px;"> .lzo</span></p>
</td>
<td>
<p align="left">
<span style="font-size:14px;"> 不</span></p>
</td>
<td>
<p align="left">
<span style="font-size:14px;"> 是</span></p>
</td>
</tr></tbody></table><p>
</p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
    <em></em></p>
<p>
<em><span style="font-size:18px;">DEFLATE是一个标准压缩算法，该算法的标准实现是zlib。由于没有可用于生成DEFLATE文件的常用命令行工具，因此常用gzip格式。gzip文件格式只是在DEFLATE格式上增加了文件头和文件尾。</span></em></p>
<p>
<span style="font-size:18px;"> </span></p>
<p>
<span style="font-size:18px;">既然常用的压缩算法挺多，但既然是压缩算法，肯定需要关注每个压缩算法的磁盘空间压缩比，压缩时间和解压缩时间，下面摘自网上的一个测试。</span></p>
<p>
</p>
<table border="1" cellpadding="0" align="left"><tbody><tr><td>
<p align="left">
<span style="font-size:14px;">压缩算法</span></p>
</td>
<td>
<p align="left">
<span style="font-size:14px;">原始文件大小</span></p>
</td>
<td>
<p align="left">
<span style="font-size:14px;">压缩后的文件大小</span></p>
</td>
<td>
<p align="left">
<span style="font-size:14px;">压缩速度</span></p>
</td>
<td>
<p align="left">
<span style="font-size:14px;">解压缩速度</span></p>
</td>
</tr><tr><td>
<p align="left">
<span style="font-size:14px;">gzip</span></p>
</td>
<td>
<p align="left">
<span style="font-size:14px;">8.3GB</span></p>
</td>
<td>
<p align="left">
<span style="font-size:14px;">1.8GB</span></p>
</td>
<td>
<p align="left">
<span style="font-size:14px;">17.5MB/s</span></p>
</td>
<td>
<p align="left">
<span style="font-size:14px;">58MB/s</span></p>
</td>
</tr><tr><td>
<p align="left">
<span style="font-size:14px;">bzip2</span></p>
</td>
<td>
<p align="left">
<span style="font-size:14px;">8.3GB</span></p>
</td>
<td>
<p align="left">
<span style="font-size:14px;">1.1GB</span></p>
</td>
<td>
<p align="left">
<span style="font-size:14px;">2.4MB/s</span></p>
</td>
<td>
<p align="left">
<span style="font-size:14px;">9.5MB/s</span></p>
</td>
</tr><tr><td>
<p align="left">
<span style="font-size:14px;">LZO-bset</span></p>
</td>
<td>
<p align="left">
<span style="font-size:14px;">8.3GB</span></p>
</td>
<td>
<p align="left">
<span style="font-size:14px;">2GB</span></p>
</td>
<td>
<p align="left">
<span style="font-size:14px;">4MB/s</span></p>
</td>
<td>
<p align="left">
<span style="font-size:14px;">60.6MB/s</span></p>
</td>
</tr><tr><td>
<p align="left">
<span style="font-size:14px;">LZO</span></p>
</td>
<td>
<p align="left">
<span style="font-size:14px;">8.3GB</span></p>
</td>
<td>
<p align="left">
<span style="font-size:14px;">2.9GB</span></p>
</td>
<td>
<p align="left">
<span style="font-size:14px;">49.3MB/S</span></p>
</td>
<td>
<p align="left">
<span style="font-size:14px;">74.6MB/s</span></p>
</td>
</tr></tbody></table><p>
</p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
<span style="font-size:18px;"> </span></p>
<p>
<span style="font-size:18px;">在hadoop中，codec代表一种压缩-解压缩算法，一个对CompressionCodec接口的实现代表一个codec。下表列举了hadoop的实现的codec。</span></p>
<span style="font-size:18px;"></span>
<p>
</p>
<table border="1" cellspacing="0" cellpadding="0"><tbody><tr><td valign="top">
<p>
<span style="font-size:14px;">压缩格式</span></p>
</td>
<td valign="top">
<p>
<span style="font-family:Calibri;font-size:14px;">Hadoop compression codec</span></p>
</td>
</tr><tr><td valign="top">
<p>
<span style="font-family:Calibri;font-size:14px;">DEFLATE</span></p>
</td>
<td valign="top">
<p>
<span style="font-family:Calibri;font-size:14px;">org.apache.hadoop.io.compress.DefaultCodec</span></p>
</td>
</tr><tr><td valign="top">
<p>
<span style="font-family:Calibri;font-size:14px;">gzip</span></p>
</td>
<td valign="top">
<p>
<span style="font-family:Calibri;font-size:14px;">org.apache.hadoop.io.compress.GzipCodec</span></p>
</td>
</tr><tr><td valign="top">
<p>
<span style="font-family:Calibri;font-size:14px;">bzip2</span></p>
</td>
<td valign="top">
<p>
<span style="font-family:Calibri;font-size:14px;">org.apache.hadoop.io.compress.Bzip2Codec</span></p>
</td>
</tr><tr><td valign="top">
<p>
<span style="font-family:Calibri;font-size:14px;">LZO</span></p>
</td>
<td valign="top">
<p>
<span style="font-family:Calibri;font-size:14px;">com.hadoop.compression.lzo.LzoCodec</span></p>
</td>
</tr></tbody></table><p>
</p>
<p>
<span style="font-size:18px;">CompressionCodec包含两个函数，可以轻松用于压缩和解压缩数据，分别为createOutputStream(OutputStream out)方法和createInputStream(InputStream in)方法。</span><br></p>
<h2><a name="t5" style="color:rgb(202,0,0);"></a>3.3、压缩与输入分片</h2>
<p>
<span style="font-size:18px;"></span> </p>
<p>
<span style="font-size:18px;">      上表中“是否可切分”这一列，表示该压缩算法是否支持<strong>切分</strong>（splitable），也就是说是否<strong>可以搜索数据流的任意位置并进一步往下读取数据</strong>。可切分压缩格式尤其适合mapreduce（不支持也可以，不过效率慢，整体作为一个map任务的数据）。</span></p>
<p>
<span style="font-size:18px;"></span> </p>
<p>
<span style="font-size:18px;">      在考虑如何压缩将有mapreduce处理的数据时，理解这些压缩格式是否支持切分是非常重要的。</span></p>
<p>
<br><span style="font-size:18px;">      在一个存储在HDFS文件系统上的且压缩前大小为1G的文件为例。如果HDFS块的大小为默认64M，那么该文件将被存储在16个块中，把这个文件作为输入数据的mapreduce作业，</span><strong><span style="font-size:18px;">如果文件支持切分的话，那么将创建16个数据块，其中每个数据块作为一个map任务的输入；如果文件不支持切发的话，那么一个map任务处理16个HDFS(整体作为输入)。<br></span></strong></p>
<p>
<span style="font-size:18px;">通常在hadoop中，对于巨大的、没有存储边界的文件，如日志文件，可以考虑如下选项：</span></p>
<ul><li><span style="font-size:18px;">存储未经压缩的文件</span></li><li><span style="font-size:18px;">使用支持切分的压缩格式，如bzip2</span></li><li><span style="font-size:18px;">使用顺序文件（sequence File），它支持压缩和切分</span></li><li><span style="font-size:18px;">使用一个Avro数据文件，该文件支持压缩和切分，就想顺序文件一样，但增加了许多编程语言都可读写的优势</span></li></ul><p>
<span style="font-size:18px;"></span> </p>
<h2><a name="t6" style="color:rgb(202,0,0);"></a>3.4、hadoop序列化</h2>
<p>
<span style="font-size:18px;">序列化：是指将结构化对象转化为字节流，以便在网络上传输或写到磁盘上进行永久存储。</span></p>
<p>
<span style="font-size:18px;">反序列化：是指将字节流转向结构化对象的逆过程。</span></p>
<p>
<span style="font-size:18px;"></span> </p>
<p>
<span style="font-size:18px;">序列化在分布式数据处理量大领域经常出现：<strong>进程通信和永久存储</strong>。</span></p>
<p>
<span style="font-size:18px;"></span> </p>
<p>
<span style="font-size:18px;">Hadoop中，各个节点的通信是通过远程调用（RPC）实现的，RPC将数据序列化成二进制后发送给远程节点，远程节点收到数据后将二进制字节流反序列化为原始数据。序列化在RPC应用中有着自己的特点，RPC序列化的特点是：</span></p>
<p>
<br><span style="font-size:18px;">Hadoop使用自己的序列化格式Writable，它格式紧凑，速度快，但是很难用java以外的语言进行扩展和使用。因为Writable是hadoop的核心（大多数mapreduce程序都会为键和值使用它）。</span></p>
<p>
<span style="font-size:18px;"></span> </p>
<p>
<span style="font-size:18px;">hadoop提供了大多数java基本类型的writable封装器，使其可以在底层处理序列化数据。</span></p>
<p>
<span style="font-size:18px;"></span> </p>
<h3><a name="t7" style="color:rgb(202,0,0);"></a>3.4.1序列化框架</h3>
<p>
<span style="font-size:18px;">       在hadoop中，提供了一个可以替换的序列化框架的API。一个<strong>序列化框架</strong>用一个Serialization（在org.apache.hadoop.io.serializer包中）实现来表示。例如WritableSerialization类是对Writable类型的Serialization实现。</span></p>
<span style="font-size:18px;"></span>
<p>
<br><span style="font-size:18px;">Serialization对象定义了从类型到Serializer（对象到字节流）和Deserializer（字节流到对象）实例的映射方式。</span></p>
<p>
 </p>
<p>
<span style="font-size:18px;">将io.serizalizations属性设置为一个由句点分割的类名列表，即可注册Serialization实现。它的<strong>默认值是org.apache.hadoop.io.serializer.WritableSerializationg，这意味着只有Writable对象才可以在外部序列化和反序列化</strong>。</span></p>
<span style="font-size:18px;"></span>
<p>
<br><span style="font-size:18px;">虽然在hadoop中默认的序列化框架为WritableSerialization，但hadoop还是提供了java本身自带的JavaSerialization类的框架，该类使用java Object Serialization。</span></p>
<p>
 </p>
<h3><a name="t8" style="color:rgb(202,0,0);"></a>3.4.2、arvo<br></h3>
<p>
<span style="font-size:18px;">apache avro是一个<strong>独立于编程语言</strong>的<strong>数据序列化系统</strong>，该项目是由<strong>hadoop之父创建</strong>的，旨在解决hadoop中Writable类型的不足：缺乏语言的可移植性。</span></p>
<p>
<span style="font-size:18px;"><br>
avro可以被多种语言（c，c++,java）处理的数据格式，具有丰富的数据类型和模式，主要包括<strong>avro模式（定义数据结构）和avro对象容器文件（存储数据）。</strong></span><br></p>
<h3><a name="t9" style="color:rgb(202,0,0);"></a>3.4.3、SequenceFile</h3>
<p>
<span style="font-size:18px;">        hadoop提供了一种顺序文件类型即<strong>SequnceFile，里面存放的其实是<span style="color:rgb(255,0,0);">键值对数据类型</span></strong>，但这里的键值对都可用二进制数据来表示，因此SequenceFile对于处理二进制数据非常合适。</span></p>
<span style="font-size:18px;"></span>
<p>
<br><span style="font-size:18px;"> <strong>SequenceFile同样也可以作为小文件的容器</strong>，即key保存文件名，value存储文件内容，这样可以把许多小文件合并到一个大文件中，尤其适合hadoop处理大文件。</span></p>
<p>
<br>
 <span style="font-size:18px;">同样提供了<strong>MapFile，其实就是已经排序的SequenceFile</strong>，并且加入用于搜索键的索引。可以将MapFile视为java.util.Map的持久化形式。MapFile在保存到磁盘上后，会有两个文件，一个数据原数据文件，另一个是index索引文件。</span></p>
<p>
<span style="font-size:18px;"><strong> </strong></span></p>
</div>
            </div>
                </div>