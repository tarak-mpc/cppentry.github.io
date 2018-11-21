---
layout:     post
title:      spark基础知识之 spark RDD 
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div style="text-align:center;"><span style="color:rgb(51,51,51);font-family:'宋体';"><span style="font-size:18px;"></span></span></div>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';background-color:rgb(249,249,249);">
<span style="font-size:14px;">                         </span><strong><span style="font-size:14px;">    spark基础知识之 spark RDD &lt;四&gt;</span></strong></p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
本期内容：</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
1.RDD：基于工作集的应用抽象</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
2.RDD内幕解密</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
3.RDD思考</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
精通了RDD，学习Spark的时间大大缩短。解决问题能力大大提高，</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
彻底把精力聚集在RDD的理解上，SparkStreaming、SparkSQL、SparkML底层封装的都是RDD。</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
RDD是spark的基石，</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
1)RDD提供了通用的抽象</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
2)现在Spark有5个子框架SparkStreaming、SparkSQL、SparkML、GraphX、SparkR，可以根据自己从事的领域如医疗等建模后建立另外的库。</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
所有顶级spark高手：</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
1解决bug，性能调优。包括框架的BUG及对框架的修改。</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
2.拿spark就是做修改的，以配合自己从事的具体业务</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
1.<a class="replace_word" href="http://lib.csdn.net/base/20" rel="nofollow" title="Hadoop知识库" style="color:rgb(51,51,51);text-decoration:none;">Hadoop</a>的mr是基于数据集的处理</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
基于工作集＆基于数据集的共同特征：位置感知，容错，负载均衡，</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
基于数据集的处理工作方式是从物理存储设备上加载数据，操作数据，写入物理存储设备。</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
spark也是mr的一种方式，只是更细致更高效。</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
其实基于数据集的方式也是一张有向无环图。但与基于工作集不同。</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
基于数据集的方式每次都从物理存储读取数据操作数据然后写回物理设备。</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
hadoop的mr的劣势、不适用的场景：</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
1.含有大量迭代<a class="replace_word" href="http://lib.csdn.net/base/31" rel="nofollow" title="算法与数据结构知识库" style="color:rgb(51,51,51);text-decoration:none;">算法</a>。</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
2.交互式查询。重点是：基于数据流的方式不能复用曾经的结果或中间计算结果。</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
假设有数千人并发数据仓库，假设100人的查询完全相同，那么每个人都需要重新查询。Spark就可以避免，因为可以复用。</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
spark会对结果进行重用</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
假如有一千人查询同一个数据仓库</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
spark的话，如果第一个人计算过的步骤，其他人都可以复用。</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
RDD是基于工作集的，除了有共同特点外，还增加了resillientDistributedDataset</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
RDD弹性：</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
1：自动的进行内存和磁盘数据存储的切换</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
2.基于lineage的高效容错</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
3.task失败会自动进行特定次数的重试</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
4.stage如果失败会自动进行特定次数的重试而且重试时只会试算失败的分片。</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
5.checkpoint和persist，是效率和容错的延伸。</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
6.数据调度弹性：DAGTASK和资源管理无关</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
7.数据分片的高度弹性</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
计算过程中有很多数据碎片，那么Partition就会非常小，每个Partition都会由一个线程处理，就会降低处理效率。这时就要考虑把小文件合并成一个大文件。</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
另外一个方面，如果内存不多，而每个Partition比较大（数据Block大），就要考虑变成更小的分片，Sparke有更多的处理批次但不会出现OOM。</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
所以说根据数据分片的大小来提高并行度或降低并行度也是Spark高度弹性的表现。同时需要指出的是，不管是提高并行度还是降低并行度，仍具有数据本地性。当然，提高并行度还是降低度行度都是人工通过代码来调整的。</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
假设有一百万个数据分片，每个数据分片都非常小（1K或10KB），如果要把数据分片调整为一万个，如果使用repartition，就需要Shuffle。</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
从<a href="http://www.2cto.com/ym/" rel="nofollow" class="keylink" style="color:rgb(51,51,51);text-decoration:none;">源码</a>RDD.scala中的repartition方法可以看到内部调用的是coalesce，传入的参数是shuffle并设置为true。源码如下：</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
defrepartition(numPartitions:Int)(implicitord:Ordering[T]=null):RDD[T]=withScope{</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
coalesce(numPartitions,shuffle=true)</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
}</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
所以，如果要把很多小的数据分片合并成大的数据分片的话千万不要直接调用repartition，而要调用coalesce，coalesce默认的shuffle为false。coalesce的源码如下：</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
/**</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
*ReturnanewRDDthatisreducedinto`numPartitions`partitions.</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
*</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
*Thisresultsinanarrowdependency,e.g.ifyougofrom1000partitions</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
*to100partitions,therewillnotbeashuffle,insteadeachofthe100</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
*newpartitionswillclaim10ofthecurrentpartitions.</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
*</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
*However,ifyou'redoingadrasticcoalesce,e.g.tonumPartitions=1,</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
*thismayresultinyourcomputationtakingplaceonfewernodesthan</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
*youlike(e.g.onenodeintheca<a href="http://zz.2cto.com/seo/" rel="nofollow" class="keylink" style="color:rgb(51,51,51);text-decoration:none;">seo</a>fnumPartitions=1).Toavoidthis,</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
*youcanpassshuffle=true.Thiswilladdashufflestep,butmeansthe</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
*currentupstreampartitionswillbeexecutedinparallel(perwhatever</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
*thecurrentpartitioningis).</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
*</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
*Note:Withshuffle=true,youcanactuallycoalescetoalargernumber</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
*ofpartitions.Thisisusefulifyouhaveasmallnumberofpartitions,</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
*say100,potentiallywithafewpartitionsbeingabnormallylarge.Calling</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
*coalesce(1000,shuffle=true)willresultin1000partitionswiththe</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
*datadistributedusingahashpartitioner.</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
*/</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
defcoalesce(numPartitions:Int,shuffle:Boolean=false)(implicitord:Ordering[T]=null)</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
:RDD[T]=withScope{</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
if(shuffle){</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
/**Distributeselementsevenlyacrossoutputpartitions,startingfromarandompartition.*/</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
valdistributePartition=(index:Int,items:Iterator[T])=&gt;{</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
varposition=(newRandom(index)).nextInt(numPartitions)</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
items.map{t=&gt;</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
//Notethatthehashcodeofthekeywilljustbethekeyitself.TheHashPartitioner</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
//willmoditwiththenumberoftotalpartitions.</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
position=position+1</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
(position,t)</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
}</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
}:Iterator[(Int,T)]</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
 </p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
//includeashufflestepsothatourupstreamtasksarestilldistributed</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
newCoalescedRDD(</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
newShuffledRDD[Int,T,T](mapPartitionsWithIndex(distributePartition),</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
newHashPartitioner(numPartitions)),</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
numPartitions).values</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
}else{</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
newCoalescedRDD(this,numPartitions)</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
}</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
}</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
如果要把一万个数据分片变成一百万个的话，可以用shuffle，也可以不用shuffle。</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
RDD允许用户在执行多个查询时显式地将工作集缓存在内存中，如果有其他人执行同样的查询的话就可以存入工作集，这就极大地提高了效率。特别是在数据仓库中，如果有一千人做同样的查询，第一个人在查询之后，每二个人查询时就可以直接从缓存中取结果。退一步说，如果一千人执行的查询只有前10个步骤是一样的，如果第一个人计算完成后，后面的人的前10步就不需要再计算了。这就极大地提升了查询速度。</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
如果是Hadoop的话一千个人执行同样的查询，就需要重复计算一千次。</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
缓存随时可以清理掉，如果内存或磁盘不足就需要根据优先度将不常使用的缓存内容清理掉。</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
RDD的cache是直接放在内存中的。RDD的cache通过checkpoint来清除。但checkpoint是重量级的。</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
SparkStreaming经常进行Checkpoint，原因是经常要用到以前的内容。假设要统计一段时间的内容，那就需要以前的数据。</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
如果Spark的一个Stage中有一千个步骤的话，默认只会产生一次结果。如果是HadoopMR就会产生999次中间结果，如果数据量很大的话，内存和磁盘都可能存不下。</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
Spark本身就是RDD的内容，RDD是只读分区的集合。RDD是数据集合，可以简单理解为List或Array。</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
用一句话概括：RDD是分布式函数式<a href="http://www.2cto.com/kf" rel="nofollow" class="keylink" style="color:rgb(51,51,51);text-decoration:none;">编程</a>的抽象。基于RDD的编程一般都是通过高阶函数的方式，原因是函数里传函数要对当前的Map函数作用的数据集进行记录的明细化操作。</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
Spark的每一步操作都是对RDD进行操作，而RDD是只读分区的集合。</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
由于每一次操作都是只读的，而操作会改变数据，那么产生中间结果怎么办？</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
=&gt;不能立即计算。这就是lazy：不用时不算，用时才计算，所以不会产生中间结果</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
RDD的核心之一就是它的Lazy，因为这不计算，开始时只是对数据处理进行标记而已。例如WordCount中的map、flatmap其实并不计算数据，只是对数据操作的标记而已。</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
flatMap的源码如下：</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
/**</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
*ReturnanewRDDbyfirstapplyingafunctiontoallelementsofthis</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
*RDD,andthenflatteningtheresults.</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
*/</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
defflatMap[U:ClassTag](f:T=&gt;TraversableOnce[U]):RDD[U]=withScope{</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
valcleanF=sc.clean(f)</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
newMapPartitionsRDD[U,T](this,(context,pid,iter)=&gt;iter.flatMap(cleanF))</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
}</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
可以看出在flatMap中创建了一个MapPartitionsRDD，但第一个参数是this，这个this是指它依赖的父RDD。每次创建新的RDD都会把父RDD作为第一个参数传入。而这只是对数据处理的标记而已。这让我们联想到一种操作：</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
f(x)=x+2</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
x=y+1</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
y=z+3</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
执行时只是函数展开。</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
所以RDD每次构建对象都依赖于父RDD，最后就是函数展开。所以如果一个Stage中有一千个步骤的话，不会产生999次中间结果。</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
<br></p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
<br></p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
以前有人说spark不适合大规模计算，当时确实有其道理，主要有两点原因：</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
1）Spark一直基于内存迭代会消耗大量内存。例如如一千个步骤虽然不产生中间结果，但如果要复用别人的结果时就需要手动persist或cache，这确实非常消耗内存。</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
2）主要是因为shuffle机制。但现在Shuffle支持很多种机制，如hashShuffle、sortBasedShuffle、钨丝计划等，而且现在Shuffle只是一个接口，一个插件，可以自定义，所以可以应对任意规模的数据处理。</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
Spark1.2以前确实有规模的限制，是由其Shuffle机制导致的，但现在没有了。现在生产环境spark最低要1.3版本，因为Spark1.3引入了dataframe，这是里程碑式的。推荐使用Spark1.6</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
由于RDD是只读的，为了应对计算模型，RDD又是lazy级别的。每次操作都会产生RDD，每次构建新的RDD都是把父RDD作为第一个参数传入，这就构成了一个链条。在最后Action时才触发，这就构成了一个从后往前回溯的过程，其实就是函数展开的过程。</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
由于这种从后往前的回溯机制，Spark的容错的开销会非常低。</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
常规容错方式：</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
1）数据检查点checkpoint</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
2）记录数据的更新</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
数据检查点非常致命。数据检查点的工作方式是通过数据中心的网络连接不同的机器，每次操作时都要复制整个数据集，就相当于每次都有一个拷贝，拷贝是要通过网络的，而网络带宽就是分布式<a href="http://www.2cto.com/os/" rel="nofollow" class="keylink" style="color:rgb(51,51,51);text-decoration:none;">系统</a>的瓶颈。同时因为要拷贝，就需要重组资源，这也是对性能的非常大的消耗。</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
记录数据的更新：每次数据变化时作记录，不需要拷贝，但1）比较复杂，而且每次更新数据需要权限，容易失控。2）耗性能。</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
spark的RDD就是记录数据更新的方式，但为何高效？</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
1）RDD是不可变的而且是lazy的。由于RDD是不可变的，所以每次操作时就要产生新的RDD，新的RDD将父RDD作为第一个参数传入，所以不存在全局修改的问题，控制难度就有极大的下降。计算时每次都是从后往前回溯，不会产生中间结果。在此基础上还有计算链条，出错可以从中间开始恢复。</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
恢复点要么是checkpoint要么是前一个stage的结果（因为Stage结束时会自动写磁盘）</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
2）如果每次对数据进行很小的修都要记录，那代价很大。RDD是粗粒度的操作：原因是为了效率为了简化。粗粒度就是每次操作时作用的都是所有的数据集合。细粒度代价太大。</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
对RDD的具体的数据的改变操作都是粗粒度的。－－RDD的写操作是粗粒度的。但RDD的读操作既可以是粗粒度的又可以是细粒度的。</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
RDD的粗粒度的写操作限制了RDD的应用场景。例如网络爬虫就不适合sparkRDD。但现实中<a class="replace_word" href="http://lib.csdn.net/base/20" rel="nofollow" title="Hadoop知识库" style="color:rgb(51,51,51);text-decoration:none;">大数据</a>处理场景大部分都是粗粒度的。特别是支持数据并行批处理的应用，例如<a class="replace_word" href="http://lib.csdn.net/base/2" rel="nofollow" title="机器学习知识库" style="color:rgb(51,51,51);text-decoration:none;">机器学习</a>，图计算，数据挖掘，都是在很多记录上进行相应操作，都是粗粒度的表现。</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
RDD不适合做细粒度和异步更新的应用。</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
如果想让Spark直接操作<a class="replace_word" href="http://lib.csdn.net/base/14" rel="nofollow" title="MySQL知识库" style="color:rgb(51,51,51);text-decoration:none;">MySQL</a>的数据或者操作HBASE数据就需要复写RDD。</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
RDD的数据分片上运行的计算逻辑都是一样的。对于每个计算逻辑都有计算函数compute</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
defcompute(split:Partition,context:TaskContext):Iterator[T]</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
所有的RDD操作返回的都是一个迭代器。如Map/flatMap等。这样的好处：假设用SparkSQL提取到了数据，产生了新的RDD，机器学习去访问这个RDD，但根本不需要知道这是来自于SparkSQL。这就可以让所有的框架无缝集成。结果就是机器学习可以直接调用SparkSQL，流处理也可以用机器学习进行训练。因为无论是什么操作返回的都是Iterator。所以就可以用hasNext来看看有没有下一个元素，然后通过Next读取下一个元素。Next具体怎么读取下一个元素和具体RDD实现有关。</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
Iterator的部分源码(scala.collection.Iterator):</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
traitIterator[+A]extendsTraversableOnce[A]{<br>
self=&gt;<br><br>
defseq:Iterator[A]=this<br><br>
/**Testswhetherthisiteratorcanprovideanotherelement.<br>
*<br>
*@return`true`ifasubsequentcallto`next`willyieldanelement,<br>
*`false`otherwise.<br>
*@noteReuse:$preservesIterator<br>
*/<br>
defhasNext:Boolean<br><br>
/**Producesthenextelementofthisiterator.<br>
*<br>
*@returnthenextelementofthisiterator,if`hasNext`is`true`,<br>
*undefinedbehaviorotherwise.<br>
*@noteReuse:$preservesIterator<br>
*/<br>
defnext():A<br><br>
/**Testswhetherthisiteratorisempty.<br>
*<br>
*@return`true`ifhasNextisfalse,`false`otherwise.<br>
*@noteReuse:$preservesIterator<br>
*/<br>
defisEmpty:Boolean=!hasNext</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
无论是什么操作，返回的结果都是Iterator接口，面向接口编程时能不能操作SparkSQL/RDD的子类的方法？</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
=&gt;Spark可以，<a class="replace_word" href="http://lib.csdn.net/base/17" rel="nofollow" title="Java EE知识库" style="color:rgb(51,51,51);text-decoration:none;"></a><a href="http://www.2cto.com/kf/ware/Java/" rel="nofollow" class="keylink" style="color:rgb(51,51,51);text-decoration:none;">Java</a>不可。原因是有this.type</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
从Java的角度讲，面向接口编程不能调用子类的方法，但如果是this.type有运行时的支持，this.type会指向具体的子类，这样就可以调用子类的方法。SparkStreaming可以调用ML的子类进行训练，RDD本身是个abstractclass，与机器学习等的算法无关。但由于有了this.type可以通过runtime把实例赋值给RDD，这样就可以操作了。</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
如果开发了一个自己领域的子框架，例如金融领域，这个子框架就可以直接在代码中调用机器学习，调用图计算进行风险预测、个性化分析、行为模式分析等，也可以调用SparkSQL用于数据挖掘。同时机器学习也可以调用金融框架。</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
又例如开发一个电商框架，那么用户支付时又可以直接调用金融框架。</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
就是说每增加一个功能就会让所有的功能都增强。每提出一个新的框架都可以使用其他所有的功能。这是核聚变级别的。</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
Spark的所有子框架都是基于RDD的，只不过是子类而已。</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
下面再看一下RDD.scala类中的preferredLocation的源码：</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
finaldefpreferredLocations(split:Partition):Seq[String]={<br>
checkpointRDD.map(_.getPreferredLocations(split)).getOrElse{<br>
getPreferredLocations(split)<br>
}<br>
}</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
分布式大数据计算时优先考虑数据不动代码动，由于有了preferredLocation，可以说spark不仅可以处理大数据，Spark可以处理一切数据。可以处理数据的数据，可以处理普通文件系统上的数据，可以在<a href="http://www.2cto.com/os/linux/" rel="nofollow" class="keylink" style="color:rgb(51,51,51);text-decoration:none;">Linux</a>上运行，也可以在Windows上运行，可以运行一切文件格式。</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
由于preferredLocation，所以每次计算都符合完美的数据本地性。Spark要做一体化多元化的数据通用处理框架，兼容一切文件系统一切操作系统一切文件格式。</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
Spark计算更快，算子更丰富，使用更简单，一统数据处理天下。</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
IBM在2016年6月16日宣布承诺大力推进ApacheSpark项目，并称该项目为：在以数据为主导的，未来十年最为重要的新的开源项目。这一承诺的核心是将Spark嵌入IBM业内领先的分析和商务平台，并将Spark作为一项服务，在IBMBluemix平台上提供给客户。IBM还将投入超过3500名研究和开发人员在全球十余个实验室开展与Spark相关的项目，并将为Spark开源生态系统无偿提供突破性的机器学习技术——IBMSystemML，同时，IBM还将培养超过100万名Spark数据科学家和数据工程师。</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
因为Spark是运行在JVM上的，一切能运行在JVM上的数据Spark都能处理。</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
只有一点：spark替代不了实时事务处理。如银行转帐等，因为Spark反应还不够快，而且实时事务性处理控制难度比较大。</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
Spark完全可以做实时处理。SparkStreaming可以达到1ms内的响应速度（官方200ms)。</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
spark要统一数据计算领域除了实时事务性处理。</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
下面再看一下RDD.scala中的dependencies的源码：</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
/**<br>
*GetthelistofdependenciesofthisRDD,takingintoaccountwhetherthe<br>
*RDDischeckpointedornot.<br>
*/<br>
finaldefdependencies:Seq[Dependency[_]]={<br>
checkpointRDD.map(r=&gt;List(newOneToOneDependency(r))).getOrElse{<br>
if(dependencies_==null){<br>
dependencies_=getDependencies<br>
}<br>
dependencies_<br>
}<br>
}</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
后面的RDD对前面的RDD都有依赖，所以容错性非常好。</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
下面再看一下RDD.scla中的partitions的源码：</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
/**<br>
*GetthearrayofpartitionsofthisRDD,takingintoaccountwhetherthe<br>
*RDDischeckpointedornot.<br>
*/<br>
finaldefpartitions:Array[Partition]={<br>
checkpointRDD.map(_.partitions).getOrElse{<br>
if(partitions_==null){<br>
partitions_=getPartitions<br>
}<br>
partitions_<br>
}<br>
}</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
进行下一步操作时可以改变并行度。并行度是弹性的一部分。</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
RDD的缺陷：</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
不支持细粒度的更新操作和增量迭代计算（如网络爬虫）</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
增量迭代时每次可能只迭代一部分数据，但RDD是粗粒度的，无法考虑是不是只是一部分数据。</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
Jstorm支持增量迭代计算，是用Java的方式重构的Storm（由阿里开发）</p>
<p style="border-width:0px;list-style:none;text-indent:2em;color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);">
真相给你自由，人的一切痛苦都源于不了解真相。所以必须了解Spark的真相，在工作时才能自由。</p>
<br><span style="color:rgb(51,51,51);font-family:'宋体';font-size:14px;background-color:rgb(249,249,249);"></span>
            </div>
                </div>