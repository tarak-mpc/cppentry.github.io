---
layout:     post
title:      二、HBase深入使用(1)—HBase数据存储
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/accptanggang/article/details/72478559				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h1><strong><span style="font-size:18px;"><span style="font-family:'宋体';">一、</span>HBase<span style="font-family:'宋体';">存储概述</span></span></strong></h1>
<p><span style="font-size:18px;">    HBase<span style="font-family:'宋体';">架构图</span></span></p>
<p><span style="font-size:18px;"> <img src="https://img-blog.csdn.net/20170518175331412?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYWNjcHRhbmdnYW5n/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></p>
<p><span style="font-size:18px;"> </span></p>
<p><span style="font-size:18px;">zkCli.sh</span></p>
<p><span style="font-size:18px;">ls /</span></p>
<p><span style="font-size:18px;">ls /ls/hbase</span></p>
<p><span style="font-size:18px;">get /ls/hbase/meta-region-server</span></p>
<p><span style="font-size:18px;">=================================</span></p>
<p><span style="font-size:18px;">user-table</span></p>
<p><span style="font-size:18px;">user</span></p>
<p><span style="font-size:18px;">regions(startkey,endkey)</span></p>
<p><span style="font-size:18px;">user,region-01,regionserver-03</span></p>
<p><span style="font-size:18px;">HBase<span style="font-family:'宋体';">新版本中，有类似于</span><span style="font-family:Calibri;">RDBMS</span><span style="font-family:'宋体';">中</span><span style="font-family:Calibri;">DataBase</span><span style="font-family:'宋体';">的概念—————命名空间。</span></span></p>
<p><span style="font-size:18px;"><span style="font-family:'宋体';">用户自定义的表，默认情况下命名空间为</span>default<span style="font-family:'宋体';">。</span></span></p>
<p><span style="font-size:18px;"><span style="font-family:'宋体';">系统自带的元数据表的命名空间为</span>hbase<span style="font-family:'宋体';">。</span></span></p>
<p><span style="font-size:18px;">list_namespace_tables 'hbase'</span></p>
<p><span style="font-size:18px;">&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;&gt;</span></p>
<p><span style="font-size:18px;">user-table<span style="font-family:'宋体';">用户表，表中有很多的</span><span style="font-family:Calibri;">region</span><span style="font-family:'宋体';">，其中</span><span style="font-family:Calibri;">region</span><span style="font-family:'宋体';">的信息保存在</span><span style="font-family:Calibri;">hbase:meta(RegionServer)</span><span style="font-family:'宋体';">中</span>。</span></p>
<p><span style="font-size:18px;"> </span></p>
<p><span style="font-size:18px;"><span style="font-family:'宋体';">客户端不管是读数据，还是写数据，都要首先连接</span>Zookeeper<span style="font-family:'宋体';">，从</span><span style="font-family:Calibri;">Zookeeper</span><span style="font-family:'宋体';">上得到</span><span style="font-family:Calibri;">hbase:meta</span><span style="font-family:'宋体';">表的</span><span style="font-family:Calibri;">region</span><span style="font-family:'宋体';">所在的</span><span style="font-family:Calibri;">RegionServer</span><span style="font-family:'宋体';">。</span></span></p>
<p><span style="font-size:18px;">RegionServer<span style="font-family:'宋体';">是用来管理</span><span style="font-family:Calibri;">Region</span><span style="font-family:'宋体';">的。</span></span></p>
<p><span style="font-size:18px;">client  -&gt;  zookeeper  -&gt;  hbase:meta(RegionServer) -&gt;  uesr-table(RegionServer)  -&gt;  put/get/scan</span></p>
<p><span style="font-size:18px;">get /ls/hbase/meta-region-server</span></p>
<p><span style="font-size:18px;">===========================================</span></p>
<p><span style="font-size:18px;">HMaster<span style="font-family:'宋体';">也要去连接</span><span style="font-family:Calibri;">Zookeeper</span><span style="font-family:'宋体';">，它需要知道哪些</span><span style="font-family:Calibri;">RegionServer</span><span style="font-family:'宋体';">是活着的。</span></span></p>
<p><span style="font-size:18px;">ls /hbase/rs(regionserver)</span></p>
<p><span style="font-size:18px;">get /hbase/rs</span></p>
<p><span style="font-size:18px;"> </span></p>
<p><span style="font-size:18px;"><span style="font-family:'宋体';">表中有几个列簇，就有几个</span>Store<span style="font-family:'宋体';">。</span></span></p>
<p><span style="font-size:18px;">Store<span style="font-family:'宋体';">中有一个</span><span style="font-family:Calibri;">MemStore</span><span style="font-family:'宋体';">，有多个</span><span style="font-family:Calibri;">StoreFile</span><span style="font-family:'宋体';">。</span></span></p>
<p><span style="font-size:18px;">MemStore<span style="font-family:'宋体';">是对应于内存的</span></span></p>
<p><span style="font-size:18px;">StoreFile<span style="font-family:'宋体';">是实际的文件，是对</span><span style="font-family:Calibri;">HFile</span><span style="font-family:'宋体';">的封装，其中</span><span style="font-family:Calibri;">H</span><span style="font-family:'宋体';">表示</span><span style="font-family:Calibri;">HDFS</span><span style="font-family:'宋体';">。</span></span></p>
<p><span style="font-size:18px;">==============================================================</span></p>
<h1><strong><span style="font-size:18px;">二、HBase<span style="font-family:'宋体';">数据存储</span></span></strong></h1>
<p><span style="font-size:18px;">  HBase<span style="font-family:'宋体';">中的所有数据文件都存储在</span><span style="font-family:Calibri;">Hadoop HDFS</span><span style="font-family:'宋体';">文件系统上，主要包括上述提出的两种文件类型：</span></span></p>
<p><span style="font-size:18px;">    * HFile<span style="font-family:'宋体';">：</span><span style="font-family:Calibri;">HBase</span><span style="font-family:'宋体';">中</span><span style="font-family:Calibri;">KeyValue</span><span style="font-family:'宋体';">数据的存储格式，</span><span style="font-family:Calibri;">HFile</span><span style="font-family:'宋体';">是</span><span style="font-family:Calibri;">Hadoop</span><span style="font-family:'宋体';">的二进制格式文件，</span></span></p>
<p><span style="font-size:18px;"><span style="font-family:'宋体';">实际上</span>StoreFile<span style="font-family:'宋体';">就是对</span><span style="font-family:Calibri;">HFile</span><span style="font-family:'宋体';">做了轻量级包装，进行数据的存储。</span></span></p>
<p><span style="font-size:18px;">* HLogFile<span style="font-family:'宋体';">：</span><span style="font-family:Calibri;">HBase</span><span style="font-family:'宋体';">中</span><span style="font-family:Calibri;">WAL</span><span style="font-family:'宋体';">（</span><span style="font-family:Calibri;">Write
 Ahead Log</span><span style="font-family:'宋体';">）的存储格式，物理上是</span><span style="font-family:Calibri;">Hadoop</span><span style="font-family:'宋体';">的</span><span style="font-family:Calibri;">Sequence File</span><span style="font-family:'宋体';">。</span></span></p>
<p></p>
<h2><strong><span style="font-size:18px;">1<span style="font-family:'黑体';">、</span>HRegion Server</span></strong></h2>
<p><span style="font-size:18px;">* HRegionServer<span style="font-family:'宋体';">内部管理了一系列</span><span style="font-family:Calibri;">HRegion</span><span style="font-family:'宋体';">对象，每个</span><span style="font-family:Calibri;">HRegion</span><span style="font-family:'宋体';">对应了</span><span style="font-family:Calibri;">table</span><span style="font-family:'宋体';">中的一个</span><span style="font-family:Calibri;">region</span><span style="font-family:'宋体';">，</span></span></p>
<p><span style="font-size:18px;">HRegion<span style="font-family:'宋体';">中由多个</span><span style="font-family:Calibri;">HStore</span><span style="font-family:'宋体';">组成。每个</span><span style="font-family:Calibri;">HStore</span><span style="font-family:'宋体';">对应了</span><span style="font-family:Calibri;">Table</span><span style="font-family:'宋体';">中的一个</span><span style="font-family:Calibri;">column
 family</span><span style="font-family:'宋体';">的存储，可以看出每个</span><span style="font-family:Calibri;">column family</span><span style="font-family:'宋体';">其实就是一个集中的存储单元，因此最好将具备共同特性的</span><span style="font-family:Calibri;">column</span><span style="font-family:'宋体';">放在一个</span><span style="font-family:Calibri;">column
 family</span><span style="font-family:'宋体';">中，这样最高效。</span></span></p>
<p><span style="font-size:18px;">* HStore<span style="font-family:'宋体';">存储是</span><span style="font-family:Calibri;">HBase</span><span style="font-family:'宋体';">存储的核心，由两部分组成，一部分是</span><span style="font-family:Calibri;">MemStore</span><span style="font-family:'宋体';">，一部分是</span><span style="font-family:Calibri;">StoreFile</span><span style="font-family:'宋体';">。</span><span style="font-family:Calibri;">MemStore</span><span style="font-family:'宋体';">是</span><span style="font-family:Calibri;">Sorted
 Memory Buffer</span><span style="font-family:'宋体';">，用户写入的数据首先会放入</span><span style="font-family:Calibri;">MemStore</span><span style="font-family:'宋体';">，当</span><span style="font-family:Calibri;">MemStore</span><span style="font-family:'宋体';">满了以后会</span><span style="font-family:Calibri;">Flush</span><span style="font-family:'宋体';">成一个</span><span style="font-family:Calibri;">StoreFile</span><span style="font-family:'宋体';">（底层实现是</span><span style="font-family:Calibri;">HFile</span><span style="font-family:'宋体';">）。</span></span></p>
<p><span style="font-size:18px;"> </span></p>
<h2><strong><span style="font-size:18px;">2<span style="font-family:'黑体';">、</span>Memstore &amp; StoreFile</span></strong></h2>
<p><span style="font-size:18px;">* Client<span style="font-family:'宋体';">写入</span><span style="font-family:Calibri;">-&gt;</span><span style="font-family:'宋体';">存入</span><span style="font-family:Calibri;">MemStore</span><span style="font-family:'宋体';">，一直到</span><span style="font-family:Calibri;">MemStore</span><span style="font-family:'宋体';">满</span><span style="font-family:Calibri;">-&gt;
 Flush</span><span style="font-family:'宋体';">成一个</span><span style="font-family:Calibri;">StoreFile</span><span style="font-family:'宋体';">，直至增长到一定阀值</span><span style="font-family:Calibri;">-&gt;
</span><span style="font-family:'宋体';">触 发</span><span style="font-family:Calibri;">Compact</span><span style="font-family:'宋体';">合并操作</span><span style="font-family:Calibri;">-&gt;
</span><span style="font-family:'宋体';">多个</span><span style="font-family:Calibri;">StoreFile Compact</span><span style="font-family:'宋体';">后，逐步形成越来越大的</span><span style="font-family:Calibri;">StoreFile -&gt;</span><span style="font-family:'宋体';">单个</span><span style="font-family:Calibri;">StoreFile</span><span style="font-family:'宋体';">大小超过一定阀值后，触发</span><span style="font-family:Calibri;">Split</span><span style="font-family:'宋体';">操作，把当前</span><span style="font-family:Calibri;">Region
 Split</span><span style="font-family:'宋体';">成</span><span style="font-family:Calibri;">2</span><span style="font-family:'宋体';">个</span><span style="font-family:Calibri;">Region</span><span style="font-family:'宋体';">，当前</span><span style="font-family:Calibri;">Region</span><span style="font-family:'宋体';">会下线，新</span><span style="font-family:Calibri;">Split</span><span style="font-family:'宋体';">出的</span><span style="font-family:Calibri;">2</span><span style="font-family:'宋体';">个孩子</span><span style="font-family:Calibri;">Region</span><span style="font-family:'宋体';">会被</span><span style="font-family:Calibri;">HMaster</span><span style="font-family:'宋体';">分配到相应的</span><span style="font-family:Calibri;">HRegionServer</span><span style="font-family:'宋体';">上，使得原先</span><span style="font-family:Calibri;">1</span><span style="font-family:'宋体';">个</span><span style="font-family:Calibri;">Region</span><span style="font-family:'宋体';">的压力得以分流到</span><span style="font-family:Calibri;">2</span><span style="font-family:'宋体';">个</span><span style="font-family:Calibri;">Region</span><span style="font-family:'宋体';">上。</span></span></p>
<p><span style="font-size:18px;">* HBase<span style="font-family:'宋体';">只是增加数据，所有的更新和删除操作，都是在</span><span style="font-family:Calibri;">Compact</span><span style="font-family:'宋体';">阶段做的，所以，用户写操作只需要进入到内存即可立即返回，从而保证</span><span style="font-family:Calibri;">I/O</span><span style="font-family:'宋体';">高性能。</span></span></p>
<p></p>
<h2><strong><span style="font-size:18px;">3<span style="font-family:'黑体';">、</span>HLog<span style="font-family:'黑体';">文件结构</span></span></strong></h2>
<p><span style="font-size:18px;">* WAL<span style="font-family:'宋体';">意为</span><span style="font-family:Calibri;">Write ahead log</span><span style="font-family:'宋体';">，类似于</span><span style="font-family:Calibri;">MySQL</span><span style="font-family:'宋体';">中的</span><span style="font-family:Calibri;">binlog</span><span style="font-family:'宋体';">，用来做灾难恢复。</span></span></p>
<p><span style="font-size:18px;">* Hlog<span style="font-family:'宋体';">记录数据的所有变更，一旦数据修改，就可以从</span><span style="font-family:Calibri;">log</span><span style="font-family:'宋体';">中进行恢复。每个</span><span style="font-family:Calibri;">HRegionServer</span><span style="font-family:'宋体';">维护一个</span><span style="font-family:Calibri;">HLog</span><span style="font-family:'宋体';">，</span></span></p>
<p><span style="font-size:18px;"><span style="font-family:'宋体';">而不是每个</span>HRegion<span style="font-family:'宋体';">一个。</span></span></p>
<p><span style="font-size:18px;"><span style="font-family:'宋体';">这样不同</span>Region<span style="font-family:'宋体';">（来自不同</span><span style="font-family:Calibri;">table</span><span style="font-family:'宋体';">）的日志会混在一起，</span></span></p>
<p><span style="font-size:18px;">这样做的目的是不断追加单个文件，相对于同时写多个文件而言，</span></p>
<p><span style="font-size:18px;"><span style="font-family:'宋体';">可以减少磁盘寻址次数，因此可以提高对</span>table<span style="font-family:'宋体';">的写性能。</span></span></p>
<p><span style="font-size:18px;"><span style="font-family:'宋体';">带来的麻烦是，如果一台</span>HRegionServer<span style="font-family:'宋体';">下线，为了恢复其上的</span><span style="font-family:Calibri;">region</span><span style="font-family:'宋体';">，</span></span></p>
<p><span style="font-size:18px;"><span style="font-family:'宋体';">需要将</span>HRegionServer<span style="font-family:'宋体';">上的</span><span style="font-family:Calibri;">log</span><span style="font-family:'宋体';">进行拆分，然后分发到其它</span><span style="font-family:Calibri;">HRegionServer</span><span style="font-family:'宋体';">上进行恢复。</span></span></p>
            </div>
                </div>