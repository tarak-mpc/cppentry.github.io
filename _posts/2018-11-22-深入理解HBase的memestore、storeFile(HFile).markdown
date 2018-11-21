---
layout:     post
title:      深入理解HBase的memestore、storeFile(HFile)
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>原文出处：http://shitouer.cn/2013/02/configuring-hbase-memstore-what-you-should-know</p>
<p> </p>
<p>MemStore是HBase非常重要的组成部分，深入理解MemStore的运行机制、工作原理、相关配置，对HBase集群管理以及性能调优有非常重要的帮助。</p>
<h1><a name="t0"></a>HBase Memstore</h1>
<p>首先通过简单介绍HBase的读写过程来理解一下MemStore到底是什么，在何处发挥作用，如何使用到以及为什么要用MemStore。</p>
<p style="text-align:center;"><img class="aligncenter" alt="" src="http://images.cnblogs.com/cnblogs_com/shitouer/247860/o_hbase_read_write_path2_small.png" width="441" height="271"></p>
<p style="text-align:center;">图一：Memstore Usage in HBase Read/Write Paths</p>
<p style="text-align:left;">当RegionServer(RS)收到写请求的时候(write request)，RS会将请求转至相应的Region。每一个Region都存储着一些列(a set of rows)。根据其列族的不同，将这些列数据存储在相应的列族中(Column Family，简写CF)。不同的CFs中的数据存储在各自的HStore中，HStore由一个Memstore及一系列HFile组成。Memstore位于RS的主内存中，而HFiles被写入到HDFS中。当RS处理写请求的时候，数据首先写入到Memstore，然后当到达一定的阀值的时候，Memstore中的数据会被刷到HFile中。</p>
<p style="text-align:left;">用到Memstore最主要的原因是：存储在HDFS上的数据需要按照row key 排序。而HDFS本身被设计为顺序读写(sequential reads/writes)，不允许修改。这样的话，HBase就不能够高效的写数据，因为要写入到HBase的数据不会被排序，这也就意味着没有为将来的检索优化。为了解决这个问题，HBase将最近接收到的数据缓存在内存中(in Memstore)，在持久化到HDFS之前完成排序，然后再快速的顺序写入HDFS。需要注意的一点是实际的HFile中，不仅仅只是简单地排序的列数据的列表，详见<a href="http://blog.cloudera.com/blog/2012/06/hbase-io-hfile-input-output/" rel="nofollow">Apache
 HBase I/O – HFile</a>。</p>
<p style="text-align:left;">除了解决“无序”问题外，Memstore还有一些其他的好处，例如：</p>
<ul><li><span style="line-height:13px;">作为一个内存级缓存，缓存最近增加数据。一种显而易见的场合是，新插入数据总是比老数据频繁使用。</span></li><li>在持久化写入之前，在内存中对Rows/Cells可以做某些优化。比如，当数据的version被设为1的时候，对于某些CF的一些数据，Memstore缓存了数个对该Cell的更新，在写入HFile的时候，仅需要保存一个最新的版本就好了，其他的都可以直接抛弃。</li></ul><p>有一点需要特别注意：<strong>每一次Memstore的flush，会为每一个CF创建一个新的HFile。</strong> 在读方面相对来说就会简单一些：HBase首先检查请求的数据是否在Memstore，不在的话就到HFile中查找，最终返回merged的一个结果给用户。</p>
<h1><a name="t1"></a>HBase Memstore关注要点</h1>
<p>迫于以下几个原因，HBase用户或者管理员需要关注Memstore并且要熟悉它是如何被使用的：</p>
<ul><li><span style="line-height:13px;">Memstore有许多配置可以调整以取得好的性能和避免一些问题。HBase不会根据用户自己的使用模式来调整这些配置，你需要自己来调整。</span></li><li>频繁的Memstore flush会严重影响HBase集群读性能，并有可能带来一些额外的负载。</li><li>Memstore flush的方式有可能影响你的HBase schema设计</li></ul><p>接下来详细讨论一下这些要点：</p>
<h2><a name="t2"></a>Configuring Memstore Flushes</h2>
<p>对Memstore Flush来说，主要有两组配置项：</p>
<ul><li><span style="line-height:13px;">决定Flush触发时机</span></li><li>决定Flush何时触发并且<span style="color:#0000ff;">在Flush时候更新被阻断(block)</span></li></ul><p>第一组是关于触发“普通”flush，这类flush发生时，并不影响并行的写请求。该类型flush的配置项有：</p>
<ul><li>hbase.hregion.memstore.flush.size</li></ul><div>
<div id="highlighter_954535" class="syntaxhighlighter notranslate xml ie">
<div class="toolbar"><span><a class="toolbar_item command_help help" href="http://shitouer.cn/2013/02/configuring-hbase-memstore-what-you-should-know/#" rel="nofollow">?</a></span></div>
<table cellspacing="0" cellpadding="0" border="0"><tbody><tr><td class="gutter">
<div class="line number1 index0 alt2">1</div>
<div class="line number2 index1 alt1">2</div>
<div class="line number3 index2 alt2">3</div>
<div class="line number4 index3 alt1">4</div>
<div class="line number5 index4 alt2">5</div>
<div class="line number6 index5 alt1">6</div>
<div class="line number7 index6 alt2">7</div>
<div class="line number8 index7 alt1">8</div>
<div class="line number9 index8 alt2">9</div>
</td>
<td class="code">
<div>
<div class="line number1 index0 alt2"><code class="xml plain">&lt;</code><code class="xml keyword">property</code><code class="xml plain">&gt;</code></div>
<div class="line number2 index1 alt1"><code class="xml spaces"> </code><code class="xml plain">&lt;</code><code class="xml keyword">name</code><code class="xml plain">&gt;hbase.hregion.memstore.flush.size&lt;/</code><code class="xml keyword">name</code><code class="xml plain">&gt;</code></div>
<div class="line number3 index2 alt2"><code class="xml spaces"> </code><code class="xml plain">&lt;</code><code class="xml keyword">value</code><code class="xml plain">&gt;134217728&lt;/</code><code class="xml keyword">value</code><code class="xml plain">&gt;</code></div>
<div class="line number4 index3 alt1"><code class="xml spaces"> </code><code class="xml plain">&lt;</code><code class="xml keyword">description</code><code class="xml plain">&gt;</code></div>
<div class="line number5 index4 alt2"><code class="xml spaces"> </code><code class="xml plain">Memstore will be flushed to disk if size of the memstore</code></div>
<div class="line number6 index5 alt1"><code class="xml spaces"> </code><code class="xml plain">exceeds this number of bytes. Value is checked by a thread that runs</code></div>
<div class="line number7 index6 alt2"><code class="xml spaces"> </code><code class="xml plain">every hbase.server.thread.wakefrequency.</code></div>
<div class="line number8 index7 alt1"><code class="xml spaces"> </code><code class="xml plain">&lt;/</code><code class="xml keyword">description</code><code class="xml plain">&gt;</code></div>
<div class="line number9 index8 alt2"><code class="xml plain">&lt;/</code><code class="xml keyword">property</code><code class="xml plain">&gt;</code></div>
</div>
</td>
</tr></tbody></table></div>
</div>
<ul><li>base.regionserver.global.memstore.lowerLimit</li></ul><div>
<div id="highlighter_918900" class="syntaxhighlighter notranslate xml ie">
<div class="toolbar"><span><a class="toolbar_item command_help help" href="http://shitouer.cn/2013/02/configuring-hbase-memstore-what-you-should-know/#" rel="nofollow">?</a></span></div>
<table cellspacing="0" cellpadding="0" border="0"><tbody><tr><td class="gutter">
<div class="line number1 index0 alt2">1</div>
<div class="line number2 index1 alt1">2</div>
<div class="line number3 index2 alt2">3</div>
<div class="line number4 index3 alt1">4</div>
<div class="line number5 index4 alt2">5</div>
<div class="line number6 index5 alt1">6</div>
<div class="line number7 index6 alt2">7</div>
<div class="line number8 index7 alt1">8</div>
<div class="line number9 index8 alt2">9</div>
<div class="line number10 index9 alt1">10</div>
</td>
<td class="code">
<div>
<div class="line number1 index0 alt2"><code class="xml plain">&lt;</code><code class="xml keyword">property</code><code class="xml plain">&gt;</code></div>
<div class="line number2 index1 alt1"><code class="xml spaces"> </code><code class="xml plain">&lt;</code><code class="xml keyword">name</code><code class="xml plain">&gt;hbase.regionserver.global.memstore.lowerLimit&lt;/</code><code class="xml keyword">name</code><code class="xml plain">&gt;</code></div>
<div class="line number3 index2 alt2"><code class="xml spaces"> </code><code class="xml plain">&lt;</code><code class="xml keyword">value</code><code class="xml plain">&gt;0.35&lt;/</code><code class="xml keyword">value</code><code class="xml plain">&gt;</code></div>
<div class="line number4 index3 alt1"><code class="xml spaces"> </code><code class="xml plain">&lt;</code><code class="xml keyword">description</code><code class="xml plain">&gt;Maximum size of all memstores in a region server before</code></div>
<div class="line number5 index4 alt2"><code class="xml spaces"> </code><code class="xml plain">flushes are forced. Defaults to 35% of heap.</code></div>
<div class="line number6 index5 alt1"><code class="xml spaces"> </code><code class="xml plain">This value equal to hbase.regionserver.global.memstore.upperLimit causes</code></div>
<div class="line number7 index6 alt2"><code class="xml spaces"> </code><code class="xml plain">the minimum possible flushing to occur when updates are blocked due to</code></div>
<div class="line number8 index7 alt1"><code class="xml spaces"> </code><code class="xml plain">memstore limiting.</code></div>
<div class="line number9 index8 alt2"><code class="xml spaces"> </code><code class="xml plain">&lt;/</code><code class="xml keyword">description</code><code class="xml plain">&gt;</code></div>
<div class="line number10 index9 alt1"><code class="xml plain">&lt;/</code><code class="xml keyword">property</code><code class="xml plain">&gt;</code></div>
</div>
</td>
</tr></tbody></table></div>
</div>
<p>需要注意的是第一个设置是每个Memstore的大小，当你设置该配置项时，你需要考虑一下每台RS承载的region总量。可能一开始你设置的该值比较小，后来随着region增多，那么就有可能因为第二个设置原因Memstore的flush触发会变早许多。</p>
<p>第二组设置主要是出于安全考虑：有时候集群的“写负载”非常高，写入量一直超过flush的量，这时，我们就希望memstore不要超过一定的安全设置。在这种情况下，写操作就要被阻止(blocked)一直到memstore恢复到一个“可管理”(manageable)的大小。该类型flush配置项有：</p>
<ul><li>hbase.regionserver.global.memstore.upperLimit</li></ul><div>
<div id="highlighter_689405" class="syntaxhighlighter notranslate xml ie">
<div class="toolbar"><span><a class="toolbar_item command_help help" href="http://shitouer.cn/2013/02/configuring-hbase-memstore-what-you-should-know/#" rel="nofollow">?</a></span></div>
<table cellspacing="0" cellpadding="0" border="0"><tbody><tr><td class="gutter">
<div class="line number1 index0 alt2">1</div>
<div class="line number2 index1 alt1">2</div>
<div class="line number3 index2 alt2">3</div>
<div class="line number4 index3 alt1">4</div>
<div class="line number5 index4 alt2">5</div>
<div class="line number6 index5 alt1">6</div>
<div class="line number7 index6 alt2">7</div>
<div class="line number8 index7 alt1">8</div>
<div class="line number9 index8 alt2">9</div>
</td>
<td class="code">
<div>
<div class="line number1 index0 alt2"><code class="xml plain">&lt;</code><code class="xml keyword">property</code><code class="xml plain">&gt;</code></div>
<div class="line number2 index1 alt1"><code class="xml spaces"> </code><code class="xml plain">&lt;</code><code class="xml keyword">name</code><code class="xml plain">&gt;hbase.regionserver.global.memstore.upperLimit&lt;/</code><code class="xml keyword">name</code><code class="xml plain">&gt;</code></div>
<div class="line number3 index2 alt2"><code class="xml spaces"> </code><code class="xml plain">&lt;</code><code class="xml keyword">value</code><code class="xml plain">&gt;0.4&lt;/</code><code class="xml keyword">value</code><code class="xml plain">&gt;</code></div>
<div class="line number4 index3 alt1"><code class="xml spaces"> </code><code class="xml plain">&lt;</code><code class="xml keyword">description</code><code class="xml plain">&gt;Maximum size of all memstores in a region server before new</code></div>
<div class="line number5 index4 alt2"><code class="xml spaces"> </code><code class="xml plain">updates are blocked and flushes are forced. Defaults to 40% of heap.</code></div>
<div class="line number6 index5 alt1"><code class="xml spaces"> </code><code class="xml plain">Updates are blocked and flushes are forced until size of all memstores</code></div>
<div class="line number7 index6 alt2"><code class="xml spaces"> </code><code class="xml plain">in a region server hits hbase.regionserver.global.memstore.lowerLimit.</code></div>
<div class="line number8 index7 alt1"><code class="xml spaces"> </code><code class="xml plain">&lt;/</code><code class="xml keyword">description</code><code class="xml plain">&gt;</code></div>
<div class="line number9 index8 alt2"><code class="xml plain">&lt;/</code><code class="xml keyword">property</code><code class="xml plain">&gt;</code></div>
</div>
</td>
</tr></tbody></table></div>
</div>
<ul><li> hbase.hregion.memstore.block.multiplier</li></ul><div>
<div id="highlighter_661493" class="syntaxhighlighter notranslate xml ie">
<div class="toolbar"><span><a class="toolbar_item command_help help" href="http://shitouer.cn/2013/02/configuring-hbase-memstore-what-you-should-know/#" rel="nofollow">?</a></span></div>
<table cellspacing="0" cellpadding="0" border="0"><tbody><tr><td class="gutter">
<div class="line number1 index0 alt2">1</div>
<div class="line number2 index1 alt1">2</div>
<div class="line number3 index2 alt2">3</div>
<div class="line number4 index3 alt1">4</div>
<div class="line number5 index4 alt2">5</div>
<div class="line number6 index5 alt1">6</div>
<div class="line number7 index6 alt2">7</div>
<div class="line number8 index7 alt1">8</div>
<div class="line number9 index8 alt2">9</div>
<div class="line number10 index9 alt1">10</div>
<div class="line number11 index10 alt2">11</div>
<div class="line number12 index11 alt1">12</div>
</td>
<td class="code">
<div>
<div class="line number1 index0 alt2"><code class="xml plain">&lt;</code><code class="xml keyword">property</code><code class="xml plain">&gt;</code></div>
<div class="line number2 index1 alt1"><code class="xml spaces"> </code><code class="xml plain">&lt;</code><code class="xml keyword">name</code><code class="xml plain">&gt;hbase.hregion.memstore.block.multiplier&lt;/</code><code class="xml keyword">name</code><code class="xml plain">&gt;</code></div>
<div class="line number3 index2 alt2"><code class="xml spaces"> </code><code class="xml plain">&lt;</code><code class="xml keyword">value</code><code class="xml plain">&gt;2&lt;/</code><code class="xml keyword">value</code><code class="xml plain">&gt;</code></div>
<div class="line number4 index3 alt1"><code class="xml spaces"> </code><code class="xml plain">&lt;</code><code class="xml keyword">description</code><code class="xml plain">&gt;</code></div>
<div class="line number5 index4 alt2"><code class="xml spaces"> </code><code class="xml plain">Block updates if memstore has hbase.hregion.block.memstore</code></div>
<div class="line number6 index5 alt1"><code class="xml spaces"> </code><code class="xml plain">time hbase.hregion.flush.size bytes. Useful preventing</code></div>
<div class="line number7 index6 alt2"><code class="xml spaces"> </code><code class="xml plain">runaway memstore during spikes in update traffic. Without an</code></div>
<div class="line number8 index7 alt1"><code class="xml spaces"> </code><code class="xml plain">upper-bound, memstore fills such that when it flushes the</code></div>
<div class="line number9 index8 alt2"><code class="xml spaces"> </code><code class="xml plain">resultant flush files take a long time to compact or split, or</code></div>
<div class="line number10 index9 alt1"><code class="xml spaces"> </code><code class="xml plain">worse, we OOME.</code></div>
<div class="line number11 index10 alt2"><code class="xml spaces"> </code><code class="xml plain">&lt;/</code><code class="xml keyword">description</code><code class="xml plain">&gt;</code></div>
<div class="line number12 index11 alt1"><code class="xml plain">&lt;/</code><code class="xml keyword">property</code><code class="xml plain">&gt;</code></div>
</div>
</td>
</tr></tbody></table></div>
</div>
<p>某个节点“写阻塞”对该节点来说影响很大，但是对于整个集群的影响更大。HBase设计为：每个Region仅属于一个RS但是“写负载”是均匀分布于整个集群(所有Region上)。有一个如此“慢”的节点，将会使得整个集群都会变慢(最明显的是反映在速度上)。</p>
<p><strong>提示</strong>：<span style="color:#808000;">严重关切Memstore的大小和Memstore Flush Queue的大小</span>。理想情况下，Memstore的大小不应该达到hbase.regionserver.global.memstore.upperLimit的设置，Memstore Flush Queue 的size不能持续增长。</p>
<h2><a name="t3"></a>频繁的Memstore Flushes</h2>
<p>要避免“写阻塞”，貌似让Flush操作尽量的早于达到触发“写操作”的阈值为宜。但是，这将导致频繁的Flush操作，而由此带来的后果便是读性能下降以及额外的负载。</p>
<p>每次的Memstore Flush都会为每个CF创建一个HFile。频繁的Flush就会创建大量的HFile。这样HBase在检索的时候，就不得不读取大量的HFile，读性能会受很大影响。</p>
<p>为预防打开过多HFile及避免读性能恶化，HBase有专门的HFile合并处理(HFile Compaction Process)。HBase会周期性的合并数个小HFile为一个大的HFile。明显的，有Memstore Flush产生的HFile越多，集群系统就要做更多的合并操作(额外负载)。更糟糕的是：Compaction处理是跟集群上的其他请求并行进行的。当HBase不能够跟上Compaction的时候(同样有阈值设置项)，会在RS上出现“写阻塞”。像上面说到的，这是最最不希望的。</p>
<p><strong>提示</strong>：<span style="color:#808000;">严重关切RS上Compaction Queue 的siz</span>e。要在其引起问题前，阻止其持续增大。</p>
<p>想了解更多HFile 创建和合并，可参看 <a href="http://www.ngdata.com/site/74-ng.html" rel="nofollow">Visualizing HBase Flushes And Compactions</a>。</p>
<p>理想情况下，在不超过hbase.regionserver.global.memstore.upperLimit的情况下，Memstore应该尽可能多的使用内存(配置给Memstore部分的，而不是真个Heap的)。下图展示了一张“较好”的情况：</p>
<p style="text-align:center;"><img class="aligncenter" alt="" src="http://images.cnblogs.com/cnblogs_com/shitouer/247860/o_memstore_size.png" width="863" height="374"></p>
<p> “Somewhat”, because we could configure lower limit to be closer to upper, since we barely ever go over it.</p>
<p>说是“较好”，是因为我们可以将“Lower limit”配置的更接近于“Upper limit”，我们几乎很少有超过它。</p>
<h2><a name="t4"></a>Multiple Column Families &amp; Memstore Flush</h2>
<p>每次Memstore Flush，会为每个CF都创建一个新的HFile。这样，不同CF中数据量的不均衡将会导致产生过多HFile：当其中一个CF的Memstore达到阈值flush时，所有其他CF的也会被flush。如上所述，太频繁的flush以及过多的HFile将会影响集群性能。</p>
<p><strong>提示</strong>：<span style="color:#808000;">很多情况下，一个CF是最好的设计</span>。</p>
<h2><a name="t5"></a>HLog (WAL) Size &amp; Memstore Flush</h2>
<p>第一张HBase Read/Write path图中，你可能已经注意到当数据被写入时会默认先写入Write-ahead Log(WAL)。WAL中包含了所有已经写入Memstore但还未Flush到HFile的更改(edits)。在Memstore中数据还没有持久化，当RegionSever宕掉的时候，可以使用WAL恢复数据。</p>
<p>当WAL(在HBase中成为HLog)变得很大的时候，在恢复的时候就需要很长的时间。因此，对WAL的大小也有一些限制，当达到这些限制的时候，就会触发Memstore的flush。Memstore flush会使WAL 减少，因为数据持久化之后(写入到HFile)，就没有必要在WAL中再保存这些修改。有两个属性可以配置：</p>
<ul><li><span style="line-height:13px;"> hbase.regionserver.hlog.blocksize<br></span></li><li>hbase.regionserver.maxlogs</li></ul><p>你可能已经发现，WAL的最大值由hbase.regionserver.maxlogs * hbase.regionserver.hlog.blocksize (2GB by default)决定。一旦达到这个值，Memstore flush就会被触发。所以，当你增加Memstore的大小以及调整其他的Memstore的设置项时，你也需要去调整HLog的配置项。否则，WAL的大小限制可能会首先被触发，因而，你将利用不到其他专门为Memstore而设计的优化。抛开这些不说，通过WAL限制来触发Memstore的flush并非最佳方式，这样做可能会会一次flush很多Region，尽管“写数据”是很好的分布于整个集群，进而很有可能会引发flush“大风暴”。</p>
<p><strong>提示</strong>：最好将hbase.regionserver.hlog.blocksize * hbase.regionserver.maxlogs 设置为稍微大于hbase.regionserver.global.memstore.lowerLimit * HBASE_HEAPSIZE.</p>
<h2><a name="t6"></a>Compression &amp; Memstore Flush</h2>
<p>HBase建议压缩存储在HDFS上的数据(比如HFiles)。除了节省硬盘空间，同样也会显著地减少硬盘和网络IO。使用压缩，当Memstore flush并将数据写入HDFS时候，数据会被压缩。压缩不会减慢多少flush的处理过程，却会大大减少以上所述问题，例如因为Memstore变大(超过 upper limit)而引起的“写阻塞”等等。</p>
            </div>
                </div>