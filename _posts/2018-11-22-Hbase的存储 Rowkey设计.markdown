---
layout:     post
title:      Hbase的存储 Rowkey设计
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h3 style="font-family:'Microsoft YaHei';color:rgb(63,63,63);line-height:30px;">
Hbase在生态系统中的位置</h3>
<h3 style="font-family:'Microsoft YaHei';color:rgb(63,63,63);line-height:30px;">
Hbase存储的逻辑视图</h3>
<h3 style="font-family:'Microsoft YaHei';color:rgb(63,63,63);line-height:30px;">
<span>Hbase的存储格式</span></h3>
<h3 style="font-family:'Microsoft YaHei';color:rgb(63,63,63);line-height:30px;">
<span>Hbase写数据流程</span></h3>
<h3 style="font-family:'Microsoft YaHei';color:rgb(63,63,63);line-height:30px;">
<span>Hbase快速响应数据</span></h3>
<p style="font-family:'Microsoft YaHei';font-size:14px;color:rgb(63,63,63);line-height:30px;">
 </p>
<h3 style="font-family:'Microsoft YaHei';color:rgb(63,63,63);line-height:30px;">
Hbase在生态系统中的位置</h3>
<p style="font-family:'Microsoft YaHei';font-size:14px;color:rgb(63,63,63);line-height:30px;">
　　HBase位于结构化存储层，Hadoop HDFS为HBase提供了高可靠性的底层存储支持，Hadoop MapReduce为HBase提供了高性能的计算能力，Zookeeper为HBase提供了稳定服务和failover机制。</p>
<p style="font-family:'Microsoft YaHei';font-size:14px;color:rgb(63,63,63);line-height:30px;">
<img alt="技术分享" src="http://a.hiphotos.baidu.com/baike/c0%3Dbaike80%2C5%2C5%2C80%2C26/sign=0a7ec454738da9775a228e79d138937c/b3b7d0a20cf431ad8b01445e4b36acaf2fdd9881.jpg" style="overflow:hidden;"></p>
<h3 style="font-family:'Microsoft YaHei';color:rgb(63,63,63);line-height:30px;">
<span style="color:rgb(0,0,0);">Hbase存储的逻辑视图</span></h3>
<p style="font-family:'Microsoft YaHei';font-size:14px;color:rgb(63,63,63);line-height:30px;">
<img alt="技术分享" src="http://static.oschina.net/uploads/img/201306/03150325_jHzd.jpg" style="overflow:hidden;"></p>
<p style="font-family:'Microsoft YaHei';font-size:14px;color:rgb(63,63,63);line-height:30px;">
<span>1）行键(RowKey)</span></p>
<p style="font-family:'Microsoft YaHei';font-size:14px;color:rgb(63,63,63);line-height:30px;">
-- 行键是字节数组, 任何字符串都可以作为行键；<br>
-- 表中的行根据行键进行排序，数据按照Row key的字节序(byte order)排序存储；<br>
-- 所有对表的访问都要通过行键 （单个RowKey访问，或RowKey范围访问，或全表扫描) <span style="color:rgb(255,0,0);">（二级索引）</span></p>
<p style="font-family:'Microsoft YaHei';font-size:14px;color:rgb(63,63,63);line-height:30px;">
<span>2）列族（ColumnFamily）</span></p>
<p style="font-family:'Microsoft YaHei';font-size:14px;color:rgb(63,63,63);line-height:30px;">
-- CF必须在表定义时给出</p>
<p style="font-family:'Microsoft YaHei';font-size:14px;color:rgb(63,63,63);line-height:30px;">
-- 每个CF可以有一个或多个列成员(ColumnQualifier)，列成员不需要在表定义时给出，新的列族成员可以随后按需、动态加入</p>
<p style="font-family:'Microsoft YaHei';font-size:14px;color:rgb(63,63,63);line-height:30px;">
-- 数据按CF分开存储，HBase所谓的列式存储就是根据CF分开存储（每个CF对应一个Store），这种设计非常适合于数据分析的情形</p>
<p style="font-family:'Microsoft YaHei';font-size:14px;color:rgb(63,63,63);line-height:30px;">
<span>3）时间戳（TimeStamp）</span></p>
<p style="font-family:'Microsoft YaHei';font-size:14px;color:rgb(63,63,63);line-height:30px;">
-- 每个Cell可能又多个版本，它们之间用时间戳区分</p>
<p style="font-family:'Microsoft YaHei';font-size:14px;color:rgb(63,63,63);line-height:30px;">
<span>4）单元格（Cell）</span></p>
<p style="font-family:'Microsoft YaHei';font-size:14px;color:rgb(63,63,63);line-height:30px;">
-- Cell 由行键，列族:限定符，时间戳唯一决定，数据全部以字节码形式存储</p>
<p style="font-family:'Microsoft YaHei';font-size:14px;color:rgb(63,63,63);line-height:30px;">
<span>5）区域(Region）</span></p>
<p style="font-family:'Microsoft YaHei';font-size:14px;color:rgb(63,63,63);line-height:30px;">
-- HBase自动把表水平（按Row）划分成多个区域(region)，每个region会保存一个表里面某段连续的数据；<br>
-- 每个表一开始只有一个region，随着数据不断插入表，region不断增大，当增大到一个阀值的时候，region就会等分会两个新的region；<br>
-- 当table中的行不断增多，就会有越来越多的region。这样一张完整的表被保存在多个Region 上。</p>
<p style="font-family:'Microsoft YaHei';font-size:14px;color:rgb(63,63,63);line-height:30px;">
-- HRegion是HBase中分布式存储和负载均衡的最小单元（默认256M）。最小单元表示不同的HRegion可以分布在不同的HRegionServer上。但一个HRegion不会拆分到多个server上。</p>
<p style="font-family:'Microsoft YaHei';font-size:14px;color:rgb(63,63,63);line-height:30px;">
特点：</p>
<p style="font-family:'Microsoft YaHei';font-size:14px;color:rgb(63,63,63);line-height:30px;">
无模式：<span style="color:rgb(255,0,0);">每行都有一个可排序的主键和任意多的列，列可以根据需要动态的增加，同一张表中不同的行可以有截然不同的列</span>；</p>
<p style="font-family:'Microsoft YaHei';font-size:14px;color:rgb(63,63,63);line-height:30px;">
面向列：<span style="color:rgb(255,0,0);">面向列（族）的存储和权限控制，列（族）独立检索；</span><br>
稀疏：<span style="color:rgb(255,0,0);">空（null）列并不占用存储空间</span>，表可以设计的非常稀疏；</p>
<p style="font-family:'Microsoft YaHei';font-size:14px;color:rgb(63,63,63);line-height:30px;">
 </p>
<h3 style="font-family:'Microsoft YaHei';color:rgb(63,63,63);line-height:30px;">
<span>Hbase的存储格式</span></h3>
<div class="para" style="font-family:'Microsoft YaHei';font-size:14px;color:rgb(63,63,63);line-height:30px;">
<p>　　HBase中的每张表都通过<span style="color:rgb(255,0,0);">行键按照一定的范围被分割成多个子表（HRegion）</span>，默认一个HRegion超过256M就要被分割成两个，由HRegionServer管理，管理哪些HRegion由HMaster分配。</p>
<p>　　HRegionServer存取一个子表时，会创建一个HRegion对象，然后对<span style="color:rgb(255,0,0);">表的每个<span>列族</span>(Column Family)创建一个Store实例</span>，每个Store都会有0个或多个StoreFile与之对应，每个StoreFile都会对应一个HFile， HFile就是实际的存储文件。因此，<span style="color:rgb(255,0,0);">一个HRegion有多少个列族就有多少个Store</span>。另外，<span style="color:rgb(255,0,0);">每个HRegion还拥有一个MemStore实例</span>。memStore存储在内存中，StoreFile存储在HDFS上。</p>
<p><span>　　</span></p>
<p><img alt="技术分享" src="https://img-blog.csdn.net/20131226173700718?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd29zaGl3YW54aW4xMDIyMTM=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" style="overflow:hidden;"></p>
<p>　　Region虽然是分布式存储的最小单元，但并不是存储的最小单元。<span style="color:rgb(255,0,0);">Region由一个或者多个Store组成，每个store保存一个columns family</span>；<span style="color:rgb(255,0,0);">每个Store又由一个memStore和0至多个StoreFile组成，StoreFile包含HFile；memStore存储在内存中，StoreFile存储在HDFS上。</span></p>
<p> </p>
<p>　　HBase是基于BigTable的面向列的分布式存储系统，其存储设计是基于Memtable / SSTable设计的，主要分为两部分，一部分为内存中的MemStore (Memtable)，另外一部分为磁盘(这里是HDFS)上的HFile (SSTable)。还有就是存储WAL的log，主要实现类为HLog.</p>
<p>　　本质上MemStore就是一个内存里放着一个保存KEY/VALUE的MAP，当MemStore（默认64MB）写满之后，会开始刷磁盘操作。 </p>
<div class="para">HBase存储在HDFS上的主要包含两种文件类型：</div>
<div class="para">　　1.<span style="color:rgb(255,0,0);"> HFile</span>， HBase中KeyValue数据的存储格式，HFile是Hadoop的二进制格式文件，实际上StoreFile就是对HFile做了轻量级包装，即StoreFile底层就是HFile</div>
<div class="para">　　2. <span style="color:rgb(255,0,0);">HLog File</span>，HBase中WAL（Write Ahead Log） 的存储格式，物理上是Hadoop的Sequence File</div>
<p><span>HFile结构：</span></p>
<p><span>　　　　 <img alt="技术分享" src="http://asyty.iteye.com/upload/picture/pic/102176/e80f8b5d-f93e-3c8e-ba2d-1a153b802022.jpg" width="559" height="164" style="overflow:hidden;"></span></p>
<p>Data Block：保存表中的数据，这部分可以被压缩</p>
<p>Meta Block：（可选）保存用户自定义的kv对，可以被压缩。</p>
<p>File Info ：Hfile的meta元信息，不被压缩，定长。</p>
<p>Data Block Index ：Data Block的索引。每个Data块的起始点。</p>
<p>Meta Block Index：（可选的）Meta Block的索引，Meta块的起始点。</p>
<p>Trailer： 定长。保存了每一段的偏移量，读取一个HFile时，会首先读取Trailer，Trailer有指针指向其他数据块的起始点，保存了每个段的起始位置(段的Magic Number用来做安全check)，然后，DataBlock Index会被读取到内存中，这样，当检索某个key时，不需要扫描整个HFile，而只需从内存中找到key所在的block，通过一次磁盘io将整个block读取到内存中，再找到需要的key。DataBlock
 Index采用LRU机制淘汰。</p>
<p>HFile的Data Block，Meta Block通常采用压缩方式存储。Data Block是HBase I/O的基本单元，为了提高效率，HRegionServer中有基于LRU的Block Cache机制。每个Data块的大小可以在创建一个Table的时候通过参数指定，大号的Block有利于顺序Scan，小号Block利于随机查询。每个Data块除了开头的Magic以外就是一个个KeyValue对拼接而成, Magic内容就是一些随机数字，目的是防止数据损坏。</p>
<p> </p>
<p><span>HFile中的Key-Value结构</span></p>
<p>HFile中的每个Key-Value对就是一个简单的byte数组。但这个byte数组包含了很多项信息，并含有固定的结构。（有点类似数据流）</p>
<p><img alt="技术分享" class="aligncenter size-full wp-image-631" title="HFile中的Key-Value" src="http://www.linuxidc.com/upload/2012_07/120707112147242.jpg" width="553" height="93" style="overflow:hidden;"></p>
<p>　　开始是两个长度固定的数值，分别表示Key的长度和Value的长度。紧接着是Key，开始是固定长度的数值，表示RowKey的长度，紧接着是RowKey，然后是固定长度的数值，表示Family的长度，然后是Family（列族），接着是Qualifier（小列），然后是两个固定长度的数值，表示Time Stamp和Key Type（Put/Delete）。Value部分则相对简单，是纯粹的二进制数据。</p>
<p>　　HBase 为每个值维护了多级索引，即：&lt;key, column family, column name（qualifer）, timestamp&gt;</p>
<p> </p>
<h3><span>Hbase写数据流程</span></h3>
<p>a)  Client发起了一个HTable.put(Put)请求给HRegionServer</p>
<p>b)  HRegionServer会将请求匹配到某个具体的HRegion上面</p>
<p>c)  决定是否写WAL log。WAL log文件是一个标准的Hadoop SequenceFile，文件中存储了HLogKey，这些Keys包含了和实际数据对应的序列号，主要用于崩溃恢复。</p>
<p>d)<span style="color:rgb(255,0,0);"> Put数据保存到MemStore中，同时检查MemStore状态，如果满了，则触发Flush to Disk请求</span>。</p>
<p>e)  HRegionServer处理Flush to Disk的请求，将数据写成HFile文件并存到HDFS上，并且存储最后写入的数据序列号，这样就可以知道哪些数据已经存入了永久存储的HDFS中。</p>
<p>由于不同的列族会共享region，所以有可能出现，一个列族已经有1000万行，而另外一个才100行。当一个要求region分割的时候，会导致100行的列会同样分布到多个region中。所以，一般建议不要设置多个列族。</p>
</div>
<div class="para" style="font-family:'Microsoft YaHei';font-size:14px;color:rgb(63,63,63);line-height:30px;">
<div class="para"> </div>
<h3><span>Hbase快速响应数据</span></h3>
<p>　　hbase上的数据是以storefile(HFile)二进制流的形式存储在HDFS上block块中；但是HDFS并不知道的hbase存的是什么，它只把存储文件是为二进制文件，也就是说，hbase的存储数据对于HDFS文件系统是透明的。  </p>
<p><img alt="技术分享" src="http://img.07net01.com/upload/images/2015/02/15/1456277150630111.jpg" style="overflow:hidden;"></p>
<p><span>　　HBase HRegion servers集群中的所有的region的数据在服务器启动时都是被打开的，并且在内冲初始化一些memstore，相应的这就在一定程度上加快系统响应；而Hadoop中的block中的数据文件默认是关闭的，只有在需要的时候才打开，处理完数据后就关闭，这在一定程度上就增加了响应时间。 <br>
     从根本上说，<span style="color:rgb(255,0,0);">HBase能提供实时计算服务主要原因是由其架构和底层的数据结构决定的，即由<span>LSM-Tree</span> +<span> HTable(region分区)</span> + Cache决定——客户端可以直接定位到要查数据所在的HRegion server服务器，然后直接在服务器的一个region上查找要匹配的数据，并且这些数据部分是经过cache缓存的。</span></span></p>
<p><img alt="技术分享" src="https://img-blog.csdn.net/20131226174043000?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd29zaGl3YW54aW4xMDIyMTM=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" width="429" height="245" style="overflow:hidden;"></p>
<div class="para">
<div class="para">不同的region会被Master分配给相应的RegionServer进行管理：</div>
<div class="para">HBase中有两张特殊的Table，-ROOT-和.META.</div>
<div class="para"><span style="color:rgb(255,0,0);">.META.：记录了用户表的Region信息，.META.可以有多个regoin，以及RegionServer的服务器地址。</span></div>
<div class="para"><span style="color:rgb(255,0,0);">-ROOT-：记录了.META.表的Region信息，-ROOT-只有一个region</span></div>
<div class="para">&amp;Oslash; Zookeeper中记录了-ROOT-表的location</div>
<div class="para"> </div>
</div>
<p><span>　　<span style="color:rgb(255,0,0);">Client访问用户数据之前需要首先访问zookeeper，然后访问-ROOT-表，接着访问.META.表，最后才能找到用户数据的位置去访问，中间需要多次网络操作，不过client端会做cache缓存。</span></span></p>
<p><span>　　<img alt="技术分享" src="http://img.07net01.com/upload/images/2015/02/15/1456277150630112.jpg" style="overflow:hidden;"><br>
    1、Client会通过内部缓存的相关的-ROOT-中的信息和.META.中的信息直接连接与请求数据匹配的HRegion server； <br>
    2、然后直接定位到该服务器上与客户请求对应的region，客户请求首先会查询该region在内存中的缓存——memstore(memstore是是一个按key排序的树形结构的缓冲区)； <br>
    3、如果在memstore中查到结果则直接将结果返回给client； <br>
    4、在memstore中没有查到匹配的数据，接下来会读已持久化的storefile文件中的数据。storefile也是按key排序的树形结构的文件——并且是特别为范围查询或block查询优化过的，；另外hbase读取磁盘文件是按其基本I/O单元(即 hbase block)读数据的。具体就是过程就是： <br>
    如果在BlockCache中能查到要造的数据则这届返回结果，否则就读去相应的storefile文件中读取一block的数据，如果还没有读到要查的数据，就将该数据block放到HRegion Server的blockcache中，然后接着读下一block块儿的数据，一直到这样循环的block数据直到找到要请求的数据并返回结果；如果将该region中的数据都没有查到要找的数据，最后接直接返回null，表示没有找的匹配的数据。当然blockcache会在其大小大于一的阀值（heapsize * hfile.block.cache.size
 * 0.85）后启动基于LRU算法的淘汰机制，将最老最不常用的block删除。 </span></p>
</div>
<div class="para" style="font-family:'Microsoft YaHei';font-size:14px;color:rgb(63,63,63);line-height:30px;">
 </div>
<h3 style="font-family:'Microsoft YaHei';color:rgb(63,63,63);line-height:30px;">
<span><span style="color:rgb(0,0,0);">Hbase容错和恢复</span></span></h3>
<div class="para" style="font-family:'Microsoft YaHei';font-size:14px;color:rgb(63,63,63);line-height:30px;">
HLogFile</div>
<div class="para" style="font-family:'Microsoft YaHei';font-size:14px;color:rgb(63,63,63);line-height:30px;">
　　HLog文件就是一个普通的Hadoop Sequence File，Sequence File 的Key是HLogKey对象，<span style="color:rgb(255,0,0);">HLogKey中记录了写入数据的归属信息</span>，除了table和region名字外，同时还包括 sequence number和timestamp，timestamp是“写入时间”，<span style="color:rgb(255,0,0);">sequence
 number的起始值为0</span>，或者是<span style="color:rgb(255,0,0);">最近一次存入文件系统中sequence number</span>。</div>
<div class="para" style="font-family:'Microsoft YaHei';font-size:14px;color:rgb(63,63,63);line-height:30px;">
　　HLog Sequece File的Value是HBase的KeyValue对象，即对应HFile中的KeyValue.</div>
<p style="font-family:'Microsoft YaHei';font-size:14px;color:rgb(63,63,63);line-height:30px;">
该机制用于数据的容错和恢复：</p>
<p style="font-family:'Microsoft YaHei';font-size:14px;color:rgb(63,63,63);line-height:30px;">
　　<span style="color:rgb(255,0,0);">每个HRegionServer中都有一个HLog对象</span>，HLog是一个实现Write Ahead Log的类，在每次用户操作写入MemStore的同时，也会写一份数据到HLog文件中（HLog文件格式见后续），HLog文件定期会滚动出新的，并删除旧的文件（已持久化到StoreFile中的数据）。<span style="color:rgb(255,0,0);">当HRegionServer意外终止后，HMaster会通过Zookeeper感知到，HMaster首先会处理遗留的
 HLog文件，将其中不同Region的Log数据进行拆分，分别放到相应region的目录下，然后再将失效的region重新分配</span>，领取 到这些region的HRegionServer在Load Region的过程中，会发现有历史HLog需要处理，因此会<span style="color:rgb(255,0,0);">Replay HLog中的数据到MemStore中，然后flush到StoreFiles，完成数据恢复</span>。</p>
<p style="font-family:'Microsoft YaHei';font-size:14px;color:rgb(63,63,63);line-height:30px;">
HBase容错性<br><span>Master容错</span>：Zookeeper重新选择一个新的Master<br>
*无Master过程中，数据读取仍照常进行；<br>
*无master过程中，region切分、负载均衡等无法进行；<br><span>RegionServer容错</span>：定时向Zookeeper汇报心跳，如果一旦时间内未出现心跳，Master将该RegionServer上的Region重新分配到其他RegionServer上，失效服务器上“预写”日志由主服务器进行分割并派送给新的RegionServer<br></p>
<p><span>Zookeeper容错</span>：Zookeeper是一个可靠地服务，一般配置3或5个Zookeeper实例</p>
<p>转自：http://www.bubuko.com/infodetail-668824.html</p>
            </div>
                </div>