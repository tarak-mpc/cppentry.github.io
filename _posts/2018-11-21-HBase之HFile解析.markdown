---
layout:     post
title:      HBase之HFile解析
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h1 style="margin-left:0cm;"><strong>HBase</strong><strong>之HFile解析</strong></h1>

<p style="margin-left:0cm;">参考网址：</p>

<p style="margin-left:0cm;"><span style="color:#0563c1;"><u><a href="http://www.thebigdata.cn/HBase/29513.html" rel="nofollow">http://www.thebigdata.cn/HBase/29513.html</a></u></span></p>

<p style="margin-left:0cm;"><span style="color:#0563c1;"><u><a href="http://www.thebigdata.cn/HBase/29564.html" rel="nofollow">http://www.thebigdata.cn/HBase/29564.html</a></u></span></p>

<h1>一.HFile介绍：（前言）</h1>

<p style="margin-left:0cm;">HFile是HBase中重要的一个存在，可以说是HBase架构中最小的结构，HBase的数据都在HFile中。我们知道HBase隶属于Hadoop生态系统，HFile从根本上来说是hdfs中的文件，只是他有自己特殊的格式。从下面这张图中，我们可以看到HFile在整个HBase中的位置以及其与Hadoop的关系。</p>

<p style="margin-left:0cm;">   </p>

<p style="margin-left:0cm;"><img alt="" class="has" height="386" src="https://img-blog.csdn.net/20180814201734998?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3l1bGluX0h1/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="651"></p>

<p style="margin-left:0cm;"> </p>

<p style="margin-left:0cm;">一般来说，我们都是通过HBase的Client来读写HBase，可是当我们要写大量的数据以及我们需要读取整个HFile的数据的时候，再通过Client就会极大地增加HBase的压力。此时如果我们直接操作HFile，不仅会快，也降低了HBase的压力。</p>

<p style="margin-left:0cm;">   本文主要是对HFile做一个简介，可能偏理论一点，中间也会配合一点源码的说明，了解了HFile的结构之后会对HBase的存储有更加深入的认识，之后我们也会对如何直接操作HFile做一个说明。</p>

<h1 style="margin-left:0cm;">二. HFile的变迁及逻辑结构：</h1>

<p style="margin-left:0cm;">从HBase开始到现在，HFile经历了三个版本，其中V2在0.92引入，V3在0.98引入。HFileV1版本的在实际使用过程中发现它占用内存多，HFile V2版本针对此进行了优化，HFile V3版本基本和V2版本相同，只是在cell层面添加了Tag数组的支持。</p>

<p style="margin-left:0cm;">HFile V1的逻辑数据组织格式如下图，DataBlock区域、MetaBlock(bloomfilter) 与FileInfo、DataBlockIndex、MetaBlockIndex、Trailer分离。 HFileV1版本的在实际使用过程中发现它占用内存多，并且Bloom File和Block Index会变的很大，而引起启动时间变长。其中每个HFile的Bloom Filter可以增长到100MB，这在查询时会引起性能问题，因为每次查询时需要加载并查询Bloom Filter，100MB的Bloom Filer会引起很大的延迟；另一个，Block Index在一个HRegionServer可能会增长到总共6GB，HRegionServer在启动时需要先加载所有这些Block Index，因而增加了启动时间。为了解决这些问题，在0.92版本中引入HFileV2版本：</p>

<p style="margin-left:0cm;"><img alt="" class="has" height="416" src="https://img-blog.csdn.net/2018081420173565?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3l1bGluX0h1/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="781"></p>

<p style="margin-left:0cm;">图 1 hfileV1</p>

<p style="margin-left:0cm;">HFile V2的逻辑数据组织格式如下图：文件主要分为四个部分：Scanned block section，Non-scanned block section，Opening-time data section和Trailer。</p>

<p style="margin-left:0cm;">Scanned block section：表示顺序扫描HFile时（<span style="color:#ff0000;">包含所有需要被读取的数据</span>）所有的数据块将会被读取，包括Leaf Index Block和Bloom Block；</p>

<p style="margin-left:0cm;">Non-scanned block section：HFile顺序扫描的时候该部分数据不会被读取，主要包括Meta Block和Intermediate Level Data Index Blocks两部分；</p>

<p style="margin-left:0cm;">Load-on-open-section：这部分数据在HBase的region server启动时，需要加载到内存中。包括FileInfo、Bloom filter block、data block index和meta block index；</p>

<p style="margin-left:0cm;">   Trailer：这部分主要记录了HFile的基本信息、各个部分的偏移值和寻址信息。</p>

<p style="margin-left:0cm;"><img alt="" class="has" height="408" src="https://img-blog.csdn.net/2018081420173574?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3l1bGluX0h1/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="547"></p>

<p style="margin-left:0cm;">图 2 hfileV2</p>

<p style="margin-left:0cm;"> </p>

<p style="margin-left:0cm;">Scanned block section: 即存储数据block部分</p>

<p style="margin-left:0cm;">Non-scanned block section:元数据block部分，主要存放meta信息，即BloomFilter信息。</p>

<p style="margin-left:0cm;">Load-on-open-section:这部分数据在RegionServer启动时，实例化Region并创建HStore时会将所有StoreFile的Load-on-open-section加载进内存，主要存放了Root Data Index,meta Index,File Info及BooleamFilter的metadata等。除了Fields for midkey外，每部分都是一个HFileBlock.下面会详细去讲这块。</p>

<p style="margin-left:0cm;">Trailer:文件尾,主要记录version版本，不同的版本Trailer的字段不一样，及Trailer的字段相关信息。</p>

<p style="margin-left:0cm;"> RegionServer托管着0...n个Region,Region管理着一个或多个HStore,其中HStore就管理着一个MemStore及多个StoreFile.</p>

<p style="margin-left:0cm;"> 所在RegionServer启动时,会扫描所有StoreFile，加载StoreFile的相关信息到内存，而这部分内容就是Load-on-open-section，主要包括 Root数据索引，miidKyes(optional),Meta索引,File Info,及BloomFilter metadata等。</p>

<p style="margin-left:0cm;"> 数据索引:数据索引是分层的，可以1-3层，其中第一层，即Root level Data Index，这部分数据是处放在内存区的。一开始，文件比较小，只有single-level,rootIndex直接定位到了DataBlock。当StoreFile变大时，rootIndex越来越大，随之所耗内存增大，会以多层结构存储数据索引.当采用multi-level方式，level=2时，使用root index和leaf index chunk,即内存区的rootIndex定位到的是 leafIndex,再由leafIndex定位到Datablock。当一个文件的datablock非常多，采用的是三级索引，即rootIndex定位到intermediate index,再由intermediate index定位到leaf index,最后定位到data block.可以看看上面图1所示，各个level的index都是分布在不同的区域的。但每部分index是以HFileBlock格式存放的，后面会比较详细地讲HFileBlock,说白了，就是HFile中的一个块。</p>

<p style="margin-left:0cm;">Fileds for midKey:这部分数据是Optional的，保存了一些midKey信息，可以快速地定位到midKey,常常在HFileSplit的时候非常有用。</p>

<p style="margin-left:0cm;">MetaIndex:即meta的索引数据，和data index类似，但是meta存放的是BloomFilter的信息，关于BloomFilter由于篇幅就不深入讨论了.</p>

<p style="margin-left:0cm;">FileInfo:保存了一些文件的信息，如lastKey,avgKeylen,avgValueLen等等，一会我们将会写程序将这部分内容解析出来并打印看看是什么东西。同样,FileInfo使用了Protobuf来进行序列化。</p>

<p style="margin-left:0cm;">Bloom filter metadata:分为GENERAL_BLOOM_META及DELETE_FAMILY_BLOOM_META二种。</p>

<p style="margin-left:0cm;"> </p>

<p style="margin-left:0cm;">FileV3版本基本和V2版本相比，并没有太大的改变，它在KeyValue(Cell)层面上添加了Tag数组的支持；并在FileInfo结构中添加了和Tag相关的两个字段。</p>

<p style="margin-left:0cm;"><strong>我们主要对HFile V2版本进行介绍，这个过程中也会响应说明V1之所以被替换掉的部分原因。</strong></p>

<h1>三. HFile的物理结构解析：</h1>

<p style="margin-left:0cm;"><img alt="" class="has" height="453" src="https://img-blog.csdn.net/2018081420173584?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3l1bGluX0h1/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="616"></p>

<p style="margin-left:0cm;">如上图所示， HFile会被切分为多个大小相等的block块（注意并不是所有的部分都被抽象成了HFileBlock），每个block的大小可以在创建表列簇的时候通过参数blocksize =&gt; ‘65535’进行指定，默认为64k，大号的Block有利于顺序Scan，小号Block利于随机查询，因而需要权衡。</p>

<p style="margin-left:0cm;">在类HFile中，我们可以看到对HFile的介绍：</p>

<p style="margin-left:0cm;"><img alt="" class="has" height="338" src="https://img-blog.csdn.net/20180814201735103?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3l1bGluX0h1/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="857"></p>

<p style="margin-left:0cm;"> </p>

<p style="margin-left:0cm;">同时在这个类中，我们可以得到HFile的存储路径为：ROOT_DIR/TABLE_NAME/REGION_NAME/CF_NAME/HFILE</p>

<p style="margin-left:0cm;">FileInfo是HFile的一个静态内部类，实现可SortedMap接口。存储的是HFile的文件信息。</p>

<p style="margin-left:0cm;"> </p>

<p style="margin-left:0cm;">如下图左侧所示，HBase将block块抽象为一个统一的HFileBlock；从上图中可以看到多种block都抽象为了HFileBlock。HFileBlock支持两种类型，一种类型不支持checksum，一种支持（在HFileContext类中usesHBaseChecksum属性决定）。为方便讲解，下图选用不支持checksum的HFileBlock内部结构：</p>

<p style="margin-left:0cm;"><img alt="" class="has" height="229" src="https://img-blog.csdn.net/2018081420173588?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3l1bGluX0h1/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="624"></p>

<p style="margin-left:0cm;">从类org.apache.hadoop.hbase.io.hfile.HFileBlock的类说明中，我们可以得到一个Block的结构如下：（上图只是一个简单的表示）</p>

<p style="margin-left:0cm;">Header：header total size is HFILEBLOCK_HEADER_SIZE</p>

<ol><li>blockType：</li>
	<li>onDiskSizeWithoutHeader：</li>
	<li>uncompressedSizeWithoutHeader：</li>
	<li>prevBlockOffset：The offset of the previous block of the same type (8 bytes). This is used to navigate to the previous block without having to go to the block index。</li>
	<li>onDiskDataSizeWithHeader：the size of data 'on disk', including header, excluding checksums (4 bytes)</li>
</ol><p style="margin-left:0cm;">Raw/Compressed/Encrypted/Encoded data: The compression algorithm is the same for all the blocks in an HFile。If compression is NONE, this is just raw, serialized Cells.</p>

<p style="margin-left:0cm;">类中其他主要属性：</p>

<ol><li>HFileContext fileContext 包含了这个hfileblock的元数据信息，比如采用的压缩算法，hfile的名字，创建时间，是否使用checksum等。</li>
	<li>long offset：The offset of this block in the file. Populated by the reader forconvenience of access. This offset is not part of the block header.</li>
	<li>ByteBuffer buf：（这个buf是主要存储数据的），这个buf里面存储的数据应该是包含了所有的数据（header+data+checksum），通过offset来读取。在方法allocateBuffer，这个buf的size为：headerSize + uncompressedSizeWithoutHeader + checksumBytes。而且该类几乎所有的方法操作这个buf都是在其副本上进行操作。</li>
</ol><p style="margin-left:0cm;"><span style="color:#333333;">HFileBlock</span><span style="color:#333333;">主要包括两部分：</span><span style="color:#333333;">BlockHeader</span><span style="color:#333333;">和</span><span style="color:#333333;">BlockData</span><span style="color:#333333;">。其中</span><span style="color:#333333;">BlockHeader</span><span style="color:#333333;">主要存储</span><span style="color:#333333;">block</span><span style="color:#333333;">元数据，</span><span style="color:#333333;">BlockData</span><span style="color:#333333;">用来存储具体数据。</span><span style="color:#333333;">block</span><span style="color:#333333;">元数据中最核心的字段是</span><span style="color:#333333;">BlockType</span><span style="color:#333333;">字段，用来标示该</span><span style="color:#333333;">block</span><span style="color:#333333;">块的类型，</span><span style="color:#333333;">HBase</span><span style="color:#333333;">中定义了多种</span><span style="color:#333333;">BlockType</span><span style="color:#333333;">，每种</span><span style="color:#333333;">BlockType</span><span style="color:#333333;">对应的</span><span style="color:#333333;">block</span><span style="color:#333333;">都存储不同的数据内容，有的存储用户数据，有的存储索引数据，有的存储</span><span style="color:#333333;">meta</span><span style="color:#333333;">元数据。<strong>对于任意一种类型的</strong></span><strong><span style="color:#333333;">HFileBlock</span></strong><strong><span style="color:#333333;">，都拥有相同结构的</span></strong><strong><span style="color:#333333;">BlockHeader</span></strong><strong><span style="color:#333333;">，但是</span></strong><strong><span style="color:#333333;">BlockData</span></strong><strong><span style="color:#333333;">结构却不相同。整个</span></strong><strong><span style="color:#333333;">HFileBlock</span></strong><strong><span style="color:#333333;">的结构我们在上面已经给出了。</span></strong></p>

<p style="margin-left:0cm;"> </p>

<p style="margin-left:0cm;"><span style="color:#333333;">通过前面的介绍，我们已经知道了</span><span style="color:#333333;">HFileBlock</span><span style="color:#333333;">通过</span><span style="color:#333333;">BlockType</span><span style="color:#333333;">这一关键的属性来决定</span><span style="color:#333333;">HFileBlock</span><span style="color:#333333;">的类型。下面我们将就不同的</span><span style="color:#333333;">type</span><span style="color:#333333;">分别进行描述。</span></p>

<p style="margin-left:0cm;"> </p>

<h1><span style="color:#333333;">四. 各种</span><span style="color:#333333;">HFileBlock</span><span style="color:#333333;">的解析</span></h1>

<p style="margin-left:0cm;"> </p>

<p style="margin-left:0cm;"><span style="color:#333333;">下面通过一张表简单罗列最核心的几种</span><span style="color:#333333;">BlockType</span><span style="color:#333333;">：</span></p>

<p style="margin-left:0cm;"><img alt="" class="has" height="243" src="https://img-blog.csdn.net/20180814201735494?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3l1bGluX0h1/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="568"></p>

<p style="margin-left:0cm;"> </p>

<h3 style="margin-left:0cm;">Trailer Block</h3>

<p style="margin-left:0cm;">主要记录了HFile的基本信息、各个部分的偏移值和寻址信息，下图为Trailer内存和磁盘中的数据结构，其中只显示了部分核心字段：</p>

<p style="margin-left:0cm;"><img alt="" class="has" height="340" src="https://img-blog.csdn.net/20180814201735520?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3l1bGluX0h1/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="432"></p>

<p style="margin-left:0cm;">File在读取的时候首先会解析Trailer Block并加载到内存，然后再进一步加载LoadOnOpen区的数据，具体步骤如下：</p>

<p style="margin-left:0cm;">　　1. 首先加载version版本信息，HBase中version包含majorVersion和minorVersion两部分，前者决定了HFile的主版本： V1、V2 还是V3;后者在主版本确定的基础上决定是否支持一些微小修正，比如是否支持checksum等。不同的版本决定了使用不同的Reader对象对HFile进行读取解析</p>

<p style="margin-left:0cm;">　　2. 根据Version信息获取trailer的长度(不同version的trailer长度不同)，再根据trailer长度加载整个HFileTrailer Block</p>

<p style="margin-left:0cm;">　　3. 最后加载load-on-open部分到内存中，起始偏移地址是trailer中的LoadOnOpenDataOffset字段，load-on-open部分的结束偏移量为HFile长度减去Trailer长度，load-on-open部分主要包括索引树的根节点以及FileInfo两个重要模块，FileInfo是固定长度的块，它纪录了文件的一些Meta信息，例如：AVG_KEY_LEN, AVG_VALUE_LEN, LAST_KEY, COMPARATOR, MAX_SEQ_ID_KEY等;</p>

<p style="margin-left:0cm;"> </p>

<p style="margin-left:0cm;">在类FixedFileTrailer我们可以看到其对trailer的一个解释：</p>

<p style="margin-left:0cm;"><img alt="" class="has" height="374" src="https://img-blog.csdn.net/20180814201735559?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3l1bGluX0h1/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="799"></p>

<p style="margin-left:0cm;"> </p>

<p style="margin-left:0cm;">在这个类中我们可以看到它记录了各种Offset的值。以下是这个类的全部属性</p>

<p style="margin-left:0cm;"><img alt="" class="has" height="474" src="https://img-blog.csdn.net/20180814201735563?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3l1bGluX0h1/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="396"></p>

<p style="margin-left:0cm;"> </p>

<h3>  Data Block:</h3>

<p style="margin-left:0cm;">DataBlock是HBase中数据存储的最小单元。DataBlock中主要存储用户的KeyValue数据(KeyValue后面一般会跟一个timestamp，图中未标出)，而KeyValue结构是HBase存储的核心，每个数据都是以KeyValue结构在HBase中进行存储。KeyValue结构在内存和磁盘中可以表示为：</p>

<p style="margin-left:0cm;"><img alt="" class="has" height="274" src="https://img-blog.csdn.net/20180814201735629?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3l1bGluX0h1/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="571"></p>

<p style="margin-left:0cm;">每个数据块中KeyValue之间是按Key升序排列的,这里的Key不等同于RowKey。如下：</p>

<p style="margin-left:0cm;">&lt; rowKey,列族名称,列名=&gt;列值&gt;&lt;/br&gt;</p>

<p style="margin-left:0cm;">&lt; zhh2009,用户基本信息,职业=&gt;码农&gt;&lt;/br&gt;</p>

<p style="margin-left:0cm;">&lt; zhh2009,用户基本信息,性别=&gt;男&gt;&lt;/br&gt;</p>

<p style="margin-left:0cm;">每一行在HBase中对应一个KeyValue，“=&gt;”左边的是KeyValue中的”Key”，”=&gt;”右边对应KeyValue中的”Value”。</p>

<h3 style="margin-left:0cm;">布隆过滤器相关块：Bloom Index Block 和 Bloom Block</h3>

<p style="margin-left:0cm;">BloomFilter对于HBase的随机读性能至关重要，对于get操作以及部分scan操作可以剔除掉不会用到的HFile文件，减少实际IO次数，提高随机读性能。这里不对布隆过滤器的原理做介绍（不是本文要点）。</p>

<p style="margin-left:0cm;">Bloom Filter使用位数组来实现过滤，HBase中每个HFile都有对应的位数组，KeyValue在写入HFile时会先经过几个hash函数的映射，映射后将对应的数组位改为1，get请求进来之后再进行hash映射，如果在对应数组位上存在0，说明该get请求查询的数据不在该HFile中。</p>

<p style="margin-left:0cm;">HFile中的位数组就是上述Bloom Block中存储的值，可以想象，一个HFile文件越大，里面存储的KeyValue值越多，位数组就会相应越大。<strong>一旦太大就不适合直接加载到内存了，因此HFile V2在设计上将位数组进行了拆分，拆成了多个独立的位数组(根据Key进行拆分，一部分连续的Key使用一个位数组)<span style="color:#ff0000;"> HFile V1 </span><span style="color:#ff0000;">改变到V2的原因之一，V1是全部加载到内存中，极大地占用了内存</span></strong>。这样一个HFile中就会包含多个位数组，根据Key进行查询，首先会定位到具体的某个位数组，只需要加载此位数组到内存进行过滤即可，减少了内存开支。</p>

<p style="margin-left:0cm;">在结构上每个位数组对应HFile中一个Bloom Block，为了方便根据Key定位具体需要加载哪个位数组，HFile V2又设计了对应的索引Bloom Index Block，对应的内存和逻辑结构图如下：</p>

<p style="margin-left:0cm;"><img alt="" class="has" height="444" src="https://img-blog.csdn.net/20180814201735650?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3l1bGluX0h1/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="567"></p>

<p style="margin-left:0cm;">Bloom Index Block结构中totalByteSize表示位数组的bit数，numChunks表示Bloom Block的个数，hashCount表示hash函数的个数，hashType表示hash函数的类型，totalKeyCount表示bloom filter当前已经包含的key的数目，totalMaxKeys表示bloom filter当前最多包含的key的数目, Bloom Index Entry对应每一个bloom filter block的索引条目，作为索引分别指向’scanned block section’部分的Bloom Block，Bloom Block中就存储了对应的位数组。</p>

<p style="margin-left:0cm;">Bloom Index Entry的结构见上图左边所示，BlockOffset表示对应Bloom Block在HFile中的偏移量，FirstKey表示对应BloomBlock的第一个Key。根据上文所说，一次get请求进来，首先会根据key在所有的索引条目中进行二分查找，查找到对应的Bloom Index Entry，就可以定位到该key对应的位数组，加载到内存进行过滤判断。也就是说加载到内存中的并不是一个HFile中全部的位数组，减少了内存的占用量。</p>

<p style="margin-left:0cm;">在trailer中并没有布隆过滤器的偏移，如何读到Bloom Index Block的？在trailer中有一个loadOnOpen区，即这个HFile一开始就会加载到内存的区域，对应在HfileReader中维持了一个List&lt;<a name="_Hlk499108824">HFileBlock</a>&gt; loadOnOpenBlocks = new ArrayList&lt;HFileBlock&gt;();  而这个HFileBlock的list中有布隆过滤器的meta 数据信息。</p>

<p style="margin-left:0cm;">到此，HFile的逻辑结构和物理存储结构基本阐述完毕，并且将HFile从逻辑上分解为各种类型的Block，再接着从微观的视角分别对Trailer Block、Data Block在结构上进行了解析：通过对trailer block的解析，可以获取hfile的版本以及hfile中其他几个部分的偏移量，在读取的时候可以直接通过偏移量对其进行加载;而对data block的解析可以知道用户数据在hdfs中是如何实际存储的;最后通过介绍Bloom Filter的相关的Block块了解HFile中Bloom Filter的存储结构。接下来会以上文为基础，分析HFile中索引块的结构以及相应的索引机制。</p>

<p style="margin-left:0cm;"> </p>

<h1>五. HFile索引块及索引机制</h1>

<p style="margin-left:0cm;">    前面我们简单地提到了Bloom Index Block，下面我们主要<strong>说明一下HFile中Data Block的索引。</strong></p>

<p style="margin-left:0cm;">HFile中索引结构根据索引层级的不同分为两种：single-level和mutil-level，前者表示单层索引，后者表示多级索引，一般为两级或三级。HFile V1版本中只有single-level一种索引结构，V2版本中引入多级索引。之所以引入多级索引，是因为随着HFile文件越来越大，Data Block越来越多，索引数据也越来越大，已经无法全部加载到内存中(V1版本中一个Region Server的索引数据加载到内存会占用几乎6G空间)，多级索引可以只加载部分索引，降低内存使用空间。<strong><span style="color:#ff0000;">HFile V1 </span><span style="color:#ff0000;">改变到V2的另一原因，</span></strong>上文我们提到Bloom Filter内存使用问题是促使V1版本升级到V2版本的一个原因，再加上这个原因，这两个原因就是V1版本升级到V2版本最重要的两个因素。</p>

<p style="margin-left:0cm;">V2版本Index Block有两类：Root Index Block和NonRoot Index Block，其中NonRoot Index Block又分为Intermediate Index Block和Leaf Index Block两种。HFile中索引结构类似于一棵树，Root Index Block表示索引数根节点，Intermediate Index Block表示中间节点，Leaf Index block表示叶子节点，叶子节点直接指向实际数据块。</p>

<p style="margin-left:0cm;">HFile中除了Data Block需要索引之外，上文提到过Bloom Block也需要索引，索引结构实际上就是采用了single-level结构，文中Bloom Index Block就是一种Root Index Block。</p>

<p style="margin-left:0cm;">对于Data Block，由于HFile刚开始数据量较小，索引采用single-level结构，只有Root Index一层索引，直接指向数据块。当数据量慢慢变大，Root Index Block满了之后，索引就会变为mutil-level结构，由一层索引变为两层，根节点指向叶子节点，叶子节点指向实际数据块。如果数据量再变大，索引层级就会变为三层。</p>

<p style="margin-left:0cm;">下面就针对Root index Block和NonRoot index Block两种结构进行解析，因为Root Index Block已经在上文分析过，此处简单带过，重点介绍NonRoot Index Block结构(InterMediate Index Block和Ieaf Index Block在内存和磁盘中存储格式相同，都为NonRoot Index Block格式)。</p>

<h3>Root Index Block</h3>

<p style="margin-left:0cm;">Root Index Block表示索引树根节点索引块，可以作为bloom的直接索引，也可以作为data索引的根索引。而且对于single-level和mutil-level两种索引结构对应的Root Index Block略有不同，本文以mutil-level索引结构为例进行分析(single-level索引结构是mutual-level的一种简化场景)，在内存和磁盘中的格式如下图所示：</p>

<p style="margin-left:0cm;"><img alt="" class="has" height="183" src="https://img-blog.csdn.net/20180814201735843?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3l1bGluX0h1/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="308"></p>

<p style="margin-left:0cm;">其中Index Entry表示具体的索引对象，每个索引对象由3个字段组成，Block Offset表示索引指向数据块的偏移量，BlockDataSize表示索引指向数据块在磁盘上的大小，BlockKey表示索引指向数据块中的第一个key。除此之外，还有另外3个字段用来记录MidKey的相关信息，MidKey表示HFile所有Data Block中中间的一个Data Block，用于在对HFile进行split操作时，快速定位HFile的中间位置（HFile过大会进行Split）。需要注意的是single-level索引结构和mutil-level结构相比，就只缺少MidKey这三个字段。</p>

<p style="margin-left:0cm;">Root Index Block会在<strong>HFile解析的时候直接加载到内存中，也就是我们前面提到的List&lt;HFileBlock&gt; loadOnOpenBlocks = new ArrayList&lt;HFileBlock&gt;();中</strong>，此处需要注意在Trailer Block中有一个字段为dataIndexCount，就表示此处Index Entry的个数。因为Index Entry并不定长，只有知道Entry的个数才能正确的将所有Index Entry加载到内存。</p>

<h3 style="margin-left:0cm;">NonRoot Index Block</h3>

<p style="margin-left:0cm;">当HFile中Data Block越来越多，single-level结构的索引已经不足以支撑所有数据都加载到内存，需要分化为mutil-level结构。mutil-level结构中NonRoot Index Block作为中间层节点或者叶子节点存在，无论是中间节点还是叶子节点，其都拥有相同的结构，如下图所示：</p>

<p style="margin-left:0cm;"><img alt="" class="has" height="216" src="https://img-blog.csdn.net/20180814201735900?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3l1bGluX0h1/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="343"></p>

<p style="margin-left:0cm;">和Root Index Block相同，NonRoot Index Block中最核心的字段也是Index Entry，用于指向叶子节点块或者数据块。不同的是，NonRoot Index Block结构中增加了block块的内部索引entry Offset字段，entry Offset表示index Entry在该block中的相对偏移量(相对于第一个index Entry)，用于实现block内的二分查找。所有非根节点索引块，包括Intermediate index block和leaf index block，在其内部定位一个key的具体索引并不是通过遍历实现，而是使用二分查找算法，这样可以更加高效快速地定位到待查找key。</p>

<p style="margin-left:0cm;"> </p>

<p style="margin-left:0cm;">类HFileBlockIndex分析：该类应该是代表了RootIndex</p>

<p style="margin-left:0cm;">类中说明，root level index会加载到内存中，同时root-level index 也应该有一个entries个数numEntries，在Root Index Block的图示中并没有给出。</p>

<p style="margin-left:0cm;">类中有一个静态类BlockIndexReader，其中有一个searchTreeLevel属性，The number of levels in the block index tree. One if there is only root level, two for root and leaf levels, etc。</p>

<p style="margin-left:0cm;">在BlockIndexReader中，还有以下属性：</p>

<p style="margin-left:0cm;"><img alt="" class="has" height="324" src="https://img-blog.csdn.net/20180814201735959?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3l1bGluX0h1/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="422"></p>

<p style="margin-left:0cm;">其中blockKey应该是各个entry起始的key（First keys of the key range corresponding to each index entry）。BlockKeys,blockOffsets,blockDataSize三者是一一对应的。</p>

<p style="margin-left:0cm;">在BlockIndexReader中，seekToDataBlock方法用来定位一个给定的key所在的DataBlock。它调用了loadDataBlockWithScanInfo方法，它首先在blockKeys中进行二分查找，得到entry的位置。得到位置之后就可以从blockOffsets中得到这个entry对应的偏移量，从而定位到对应的block。但是这个block可能是中间节点的block（多层索引的时候），也有可能直接就是叶子节点即数据的dataBlock。得到这个block之后，会和当前的block(该方法的参数接受一个HFileBlock)进行比较，避免避免重复读相同的block。得到这个block时，当前的遍历层级lookupLevel为1，根据lookupLevel与searchTreeLevel比较决定得到的这个block具体是什么block。如下：</p>

<p style="margin-left:0cm;"><img alt="" class="has" height="271" src="https://img-blog.csdn.net/20180814201735989?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3l1bGluX0h1/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="519"></p>

<p style="margin-left:0cm;">              最后返回的是HFileBlock。</p>

<p style="margin-left:0cm;"> </p>

<h3>HFile数据完整索引流程图示：</h3>

<p style="margin-left:0cm;">了解了HFile中数据索引块的两种结构之后，就来看看如何使用这些索引数据块进行数据的高效检索。整个索引体系类似于MySQL的B+树结构，但是又有所不同，比B+树简单，并没有复杂的分裂操作。具体见下图所示：</p>

<p style="margin-left:0cm;"><img alt="" class="has" height="283" src="https://img-blog.csdn.net/2018081420173628?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3l1bGluX0h1/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="556"></p>

<p style="margin-left:0cm;">图中上面三层为索引层，在数据量不大的时候只有最上面一层，数据量大了之后开始分裂为多层，最多三层，如图所示。最下面一层为数据层，存储用户的实际keyvalue数据。这个索引树结构类似于InnoSQL的聚集索引，只是HBase并没有辅助索引的概念。</p>

<p style="margin-left:0cm;">图中红线表示一次查询的索引过程(HBase中相关类为HFileBlockIndex和HFileReaderV2)，基本流程可以表示为：</p>

<p style="margin-left:0cm;"> 1. 用户输入rowkey为fb，在root index block中通过二分查找定位到fb在’a’和’m’之间，因此需要访问索引’a’指向的中间节点。因为root index block常驻内存，所以这个过程很快。</p>

<p style="margin-left:0cm;">2. 将索引’a’指向的中间节点索引块加载到内存，然后通过二分查找定位到fb在index ‘d’和’h’之间，接下来访问索引’d’指向的叶子节点。</p>

<p style="margin-left:0cm;">3. 同理，将索引’d’指向的中间节点索引块加载到内存，一样通过二分查找定位找到fb在index ‘f’和’g’之间，最后需要访问索引’f’指向的数据块节点。</p>

<p style="margin-left:0cm;">4. 将索引’f’指向的数据块加载到内存，通过遍历的方式找到对应的keyvalue。</p>

<p style="margin-left:0cm;">上述流程中因为中间节点、叶子节点和数据块都需要加载到内存，所以io次数正常为3次。但是实际上HBase为block提供了缓存机制，可以将频繁使用的block缓存在内存中，可以进一步加快实际读取过程。所以，在HBase中，通常一次随机读请求最多会产生3次io，如果数据量小(只有一层索引)，数据已经缓存到了内存，就不会产生io。</p>

<p style="margin-left:0cm;">索引块的分裂就不再叙述了。</p>

<p style="margin-left:0cm;">总结</p>

<p style="margin-left:0cm;">整个HFile的文章介绍基本到这里，全文对HFile的结构进行了一个梳理，了解之后相信对HFile的底层储存方式有了一个深入的认识，希望对于操作HBase的人来说有一点帮助。</p>

<p style="margin-left:0cm;"> </p>            </div>
                </div>