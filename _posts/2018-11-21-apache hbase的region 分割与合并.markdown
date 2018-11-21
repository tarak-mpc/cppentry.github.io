---
layout:     post
title:      apache hbase的region 分割与合并
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>原文地址：<a href="https://hortonworks.com/blog/apache-hbase-region-splitting-and-merging/" rel="nofollow">APACHE HBASE REGION SPLITTING AND MERGING</a> <br>
本文我们将深入探讨hbase的核心领域之一：region分割与合并。  具体来说，我们将详细讨论hbase如何在region间做负载均衡，以及如何管理region分割。hbase的数据以行为单位存储在hbase表中。hbase表按多行被分割成多个region。这些region分布在hbase集群中，并且由region server进程负责将这些region提供给client访问。一个region中，rowkey是一个连续的范围，也就是说表中的记录是在一个region 中是按照startkey到rowkey排序存储的。region之间的数据是正交的，即一条记录在同一时刻只会在一个region中，一个region同一时刻也只会在一个region server中，这也是为什么hbase能够狗保证单条数据记录的强一致性的原因。-ROOT-和 .META. regions一起，能够有效地从一个3级的B-Tree中快速定位一个表中的一行记录。 <br>
一个region包含多个“Store”，Store对应列族。一个Store包括多个memstore和0个到若干个存储文件。每个列族的数据都是分开存储和访问的。 <br>
通常一个hbase表由多个region构成，这些region分布在多个regionserver上。也就是说，region是在regionserver中插入和查询数据时负载均衡的物理机制。一张hbase表在刚刚创建的时候，默认只有一个region。所以多有关于这张表的请求都被路由到同一个region server，不管集群中有多少region server。这也就是为什么hbase表在刚刚创建的阶段不能充分利用整个集群的吞吐量的原因。</p>

<h1 id="pre-splitting">PRE-SPLITTING</h1>

<p>hbase表在创建之初只有一个region的原因是因为hbase本身不知道如何在rowkey集中创建分割点。分割点高度依赖于数据rowkey的分别。Hbase提供了client端工具用于region分割，而不是让用户猜测或者导致故障。用户可以通过pre-splitting的流程，可以创建一个已经有多个region的新表。因为pre-splitting可以让你在表创建之初可以在集群中进行负载均衡，所以如果你已经知道插入数据的rowkey分布，就应该应用它。但是，pre-splitting创建region也有风险：由于数据分布不均匀，或者存在热点行或者较大的行，不能均匀地分担负载。如果最初的一组分割点选择得不好，那么最终可能会出现异构的负载分布，这反过来又会限制集群性能。 <br>
Pre-splitting的一个问题就是如何计算分割点。你可以使用RegionSplitter实用工具。RegionSplitter通过SplitAlgorithm算法创建计算分割点。HexStringSplit和UniformSplit是两个预置的算法。如果rowkey具有十六进制字符串的前缀（如果使用散列作为前缀），则可以使用HexStringSplit，如果假设rowkey是随机的字节序列，则可以使用UniformSplit计算分割点。当然， 你也可以实现自定义的SplitAlgorithm算法并在RegionSplitter中调用它。</p>



<pre class="prettyprint"><code class=" hljs avrasm">$ hbase org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.util</span><span class="hljs-preprocessor">.RegionSplitter</span> test_table HexStringSplit -c <span class="hljs-number">10</span> -f f1</code></pre>

<p>其中-c 10指定所请求创建的region数量为10，-f指定列族，用“：”分隔。 该工具将创建一个名为“test_table”的表，其中包含10个region：</p>



<pre class="prettyprint"><code class=" hljs r"><span class="hljs-number">13</span>/<span class="hljs-number">01</span>/<span class="hljs-number">18</span> <span class="hljs-number">18</span>:<span class="hljs-number">49</span>:<span class="hljs-number">32</span> DEBUG hbase.HRegionInfo: Current INFO from scan results = {NAME =&gt; <span class="hljs-string">'test_table,,1358563771069.acc1ad1b7962564fc3a43e5907e8db33.'</span>, STARTKEY =&gt; <span class="hljs-string">''</span>, ENDKEY =&gt; <span class="hljs-string">'19999999'</span>, ENCODED =&gt; acc1ad1b7962564fc3a43e5907e8db33,}
<span class="hljs-number">13</span>/<span class="hljs-number">01</span>/<span class="hljs-number">18</span> <span class="hljs-number">18</span>:<span class="hljs-number">49</span>:<span class="hljs-number">32</span> DEBUG hbase.HRegionInfo: Current INFO from scan results = {NAME =&gt; <span class="hljs-string">'test_table,19999999,1358563771096.37ec12df6bd0078f5573565af415c91b.'</span>, STARTKEY =&gt; <span class="hljs-string">'19999999'</span>, ENDKEY =&gt; <span class="hljs-string">'33333332'</span>, ENCODED =&gt; 37ec12df6bd0078f5573565af415c91b,}
<span class="hljs-keyword">...</span></code></pre>

<p>如果你已经有了分割点，则可以使用hbase shell创建预region分割的表。</p>



<pre class="prettyprint"><code class=" hljs php">hbase(main):<span class="hljs-number">015</span>:<span class="hljs-number">0</span>&gt; create <span class="hljs-string">'test_table'</span>, <span class="hljs-string">'f1'</span>, SPLITS=&gt; [<span class="hljs-string">'a'</span>, <span class="hljs-string">'b'</span>, <span class="hljs-string">'c'</span>]</code></pre>

<p>或者</p>



<pre class="prettyprint"><code class=" hljs bash">$ <span class="hljs-built_in">echo</span> <span class="hljs-operator">-e</span>  <span class="hljs-string">"anbnc"</span> &gt;/tmp/splits
hbase(main):<span class="hljs-number">015</span>:<span class="hljs-number">0</span>&gt; create <span class="hljs-string">'test_table'</span>, <span class="hljs-string">'f1'</span>, SPLITSFILE=&gt;<span class="hljs-string">'/tmp/splits'</span></code></pre>

<p>为了获得最佳负载均衡效果，用户在选择正确的分割算法或者分割点时需要考虑其数据模型、主键分布等因素。无论选择何种预region分割方法，一旦开始向表中写入数据，就可以看到数据被负载到集群的各个region上。也可以通过数据让hbase表自动分割region，然后持续监控这张表的所有region。</p>



<h1 id="auto-splitting">Auto splitting</h1>

<p>无论是否使用pre-splitting特效，一旦某个region大小到达一个最大值，就会自动分割成两个region。对于0.94版本的hbase，可以通过可选的RegionSplitPolicy API配置决定region何时分割、如何确定分割点。hbase有几个预置的region分割策略：ConstantSizeRegionSplitPolicy, IncreasingToUpperBoundRegionSplitPolicy, 和KeyPrefixRegionSplitPolicy. <br>
ConstantSizeRegionSplitPolicy策略适用于版本号小于0.94的hbase，当region中的某一个store文件大小大于配置的hbase.hregion.max.filesize值时，这个region就会被分割， hbase.hregion.max.filesize的值默认大小为10GB， 这种region拆分策略非常适用于已经做过pre-splitting的情况，并且希望每个regionserver上的region数目不要太多。 <br>
hbase0.94和之后的版本默认的region分割策略是IncreasingToUpperBoundRegionSplitPolicy，它根据同一region server中托管的region数木进行更有效地分割。分割策略按照公式：<code>Min (R^2 * “hbase.hregion.memstore.flush.size”, “hbase.hregion.max.filesize”)</code>计算最大存储文件大小，其中，<code>R</code>是同一个region server上托管的同一张表的region的数目。例如，如果默认的memstore刷盘门限大小是128MB，存储文件大小最大为10GB，则region server中第一个将会在region达到128M时就被分割。随着region server托管的region数目增加，region拆分的门限尺寸也将依次增加为：512MB，1152MB，2GB，3.2GB，4.6GB，6.2GB等。直到这个region server中托管的region数目达到9个后，此时的拆分尺寸已经超过配置的hbase.hregion.max.filesize的大小，即10G，那么之后的拆分尺寸会一直使用10G。对于这两种算法，无论何时拆分，所使用的分割点都是对应于最大存储文件的“块索引”中间点的rowkey。 <br>
KeyPrefixRegionSplitPolicy是hbase工具集的一个重要补充。你可以配置rowkey前缀的长度来对rowkey进行分组，KeyPrefixRegionSplitPolicy策略可以确保一组region中没有包含相同前缀rowkey的记录。如果你的rowkey已经有了前缀，那么就可以使用这个策略，以确保有相同前缀的rowkey会落在同一个region中。这种记录的分组称为“Entity Groups”或者“Row Groups”。如果你的应用在设计时考虑到本地事务，则这个策略是一个重要的功能。 <br>
可以通过设置hbase.regionserver.region.split.policy”配置项或者hbase表描述，来确定你的默认region拆分策略。当然，如果你有信心，也可以使用自定义的region分割策略， 并在创建hbase表的时候使用自定义策略，或者修改现有的表：</p>



<pre class="prettyprint"><code class=" hljs avrasm">HTableDescriptor tableDesc = new HTableDescriptor(<span class="hljs-string">"example-table"</span>)<span class="hljs-comment">;</span>
tableDesc<span class="hljs-preprocessor">.setValue</span>(HTableDescriptor<span class="hljs-preprocessor">.SPLIT</span>_POLICY, AwesomeSplitPolicy<span class="hljs-preprocessor">.class</span><span class="hljs-preprocessor">.getName</span>())<span class="hljs-comment">;</span>
//<span class="hljs-keyword">add</span> columns etc
admin<span class="hljs-preprocessor">.createTable</span>(tableDesc)<span class="hljs-comment">;</span></code></pre>

<p>如果你需要执行pre-splitting，并且希望手动管理region拆分，可以通过设置“hbase.hregion.max.filesize”为一个较高的值，并且将拆分策略设置为ConstantSizeRegionSplitPolicy来禁用region拆分。但是为了保证region不会超出regionserver的限制，你需要为“hbase.hregion.max.filesize”设置一个安全值，例如100G。可以通过禁用自动的region拆分策略，只依靠pre-splitting在创建时拆分region（例如，使用统一散列作为rowkey前缀），这样可以保证读写负载可以很好的负载到每个region，而且region的大小也比较均匀。</p>



<h1 id="forced-splits">Forced Splits</h1>

<p>hbase也允许客户端对数据表在线强制拆分。例如，hbase shell可以用来拆分一个hbase表的所有region，只需要提供一个分割点。</p>



<pre class="prettyprint"><code class=" hljs livecodeserver">hbase(main):<span class="hljs-number">024</span>:<span class="hljs-number">0</span>&gt; <span class="hljs-built_in">split</span> <span class="hljs-string">'b07d0034cbe72cb040ae9cf66300a10c'</span>, <span class="hljs-string">'b'</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.1620</span> <span class="hljs-built_in">seconds</span></code></pre>

<p>通过仔细监控HBase负载分布，如果您发现某些region负载不均衡，则可以考虑手动拆分这些region，以平衡负载，提高吞吐量。 可能想要进行手动拆分的另一个原因是，当您看到该region的初始拆分结果不理想，并且您已禁用自动拆分。 例如，如果数据分布随时间变化，可能会发生这种情况。</p>



<h1 id="region拆分的实现原理">region拆分的实现原理</h1>

<p>写请求被region server处理，region server会先将数据写到称为“memstore”的内存存储系统中。一旦memstore写满，就会被写到磁盘上的存储文件中，这个过程称为“memstore flush”。随着存储文件的积累，region server会将它们合并成一个更大文件。随着每一次写磁盘和合并操作完成，如果region拆分策略任务这个region需要被拆分，则会发起region拆分请求。由于hbase所有的数据文件是不可变的，进行region拆分时，一个region被拆分为两个region时，两个新创建的region不会重写所有的数据到新的文件，而是创建小的sym-link文件，sym-link文件也叫reference文件。reference文件根据分割点，指向父存储文件的顶部或底部。reference文件的使用和常规数据文件一样，但它只有一半的记录。如果reference文件不再有对父region的不可变数据文件的引用，则这个region不可以再拆分。这些reference文件通过合并逐渐清理，以便该region停止引用其父文件，并可以进一步拆分。 <br>
尽管region拆分是由region server本地进行决策的，但是拆分过程却有很多参与者。region server在region拆分前后都会通知Master进程更新.META.表， 以便client可以找到拆分出来的新的两个region。还需要重新排列目录结构以及HDFS中的数据文件。region拆分过程有多个任务完成。为了在发生错误时可以回滚，regionserver会在内存中保存执行状态日志。图1显示了Region Server执行拆分的步骤。每个步骤都标有其步骤编号。来自Region Servers或Master的操作显示为红色，而来自客户端的操作显示为绿色。 <br>
<img src="https://2xbbhjxc6wk3v21p62t8n4d4-wpengine.netdna-ssl.com/wp-content/uploads/2013/02/hbase.jpg" alt="这里写图片描述" title=""></p>

<ol>
<li>region server自身决定region拆分，并准备发起拆分。作为第一步，它将在zookeeper的分区/hbase/region-in-transition/region-name下中创建一个znode。</li>
<li>因为Master是父region-in-transition的znode节点的观察者，所以它知晓这个znode的建立。</li>
<li>region server在HDFS的父region目录下创建一个名为“.splits”的子目录。</li>
<li>region server关闭父region，强制cache刷盘并在本地数据结构中将这个region标记为offline。此时，父region的client请求将抛出NotServingRegionException，client将重试。</li>
<li>region server为子region A和B分别在.splits目录下的region目录，并创建必要的数据结构。然后拆分存储文件，即先在父region中创建每个存储文件两个reference文件。这两个reference文件将指向父region文件。</li>
<li>region server在HDFS中创建实际的region目录，并为每个子region更新相应的reference文件。</li>
<li>region server发起Put请求到.META.表，并在.META.表中将父region设置为offline，表并添加有关子region的信息。此时，.META.表中不会有每个子region的单独的条目。client可以通过scan .META.表来知晓父region正在拆分，但是除非子region信息记录到.META.表，否则client是看不到子region的。如果前面的Put操作成功写入到.META.表，则标志父region拆分完成。如果region server在put操作前返回失败，则Master和打开这个region的region server将会清除region拆分的错误状态，如果.META.表成功更新，则region拆分状态会被Master向前翻。</li>
<li>region server打开子region并行地接受写入请求。</li>
<li>region server将子region A和B，以及它们的承载者信息分别添加到.META.表。之后，client就可以发现新的region，并访问之。client本地缓存.META.表信息，但是当它们访问region server或者.MET.表时，本地缓存失效，client从.META.表获取新的region信息。</li>
<li>region server更新zookeeper的/hbase/region-in-transition/region-name节点中的region状态到SPLIT，以便master感知其状态变化。如果需要的话，负载器可以将子region自由地指定到其它region。</li>
<li>region拆分完成后，其元数据和HDFS仍将包含对父region的引用。这些引用将在子region压缩重写数据文件时被删除。Master的GC任务会定期检查子region是否仍然引用父文件，如果没有，父region将被删除。</li>
</ol>



<h1 id="region合并">REGION合并</h1>

<p>不像region拆分，HBase目前不提供可用的region合并工具。HMerge和Merge工具也并非通用的。HBase当前不支持在线表格和自动合并功能。然而，像OnlineMerge、Master初始化的自动region合并以及Hbase表基于ZK的读写锁等问题一样，我们一直致力于提供更加稳定的region拆分功能，并为region合并提供更好的支持。请持续关注。</p>



<h1 id="结论">结论</h1>

<p>正如您所见，HBase内置了很多管理工具来管理region拆分，以及region自动分片。HBase还提供了有关region管理的必要工具，以便可以管理region拆分过程。还可以通过RegionSplitPolicy精确地控制region拆分的时间和方式。 <br>
表中区域的数量以及这些区域如何分割是理解和调整HBase集群负载的关键因素。如果您可以估算您的密钥分配，则应该使用预分割创建表以获得最佳的初始负载性能。您可以从较低的倍数的区域服务器开始，作为初始区域的起点，并让自动分割接管。如果无法正确估计初始分割点，则最好只创建一个区域的表格，然后使用自动分割开始初始加载，然后使用IncreasingToUpperBoundRegionSplitPolicy。但是，请记住，地区总数会随着时间的推移而趋于稳定，而目前的地区分割点集合将根据表中迄今收到的数据确定。您可能需要始终监控区域内的负荷分配情况，并且如果负荷分配随时间变化，请使用手动拆分或设置更积极的区域拆分大小。最后，您可以尝试即将推出的在线合并功能并贡献您的使用案例。 <br>
一张表的region数目和这张表是如何拆分的是理解和调节HBase负载的两个重要因素。如果可以预估rowkey的分布，可以通过pre-splitting特效创表，以便在开始时就可以有很好的负载效果。可以将pre-splitting预拆分region数目控制为region server数目的低倍数，然后利用region 自动拆分的特性接管region管理。如果不可预估，则最好先不拆分新表，即创建只有一个region的表，然后让region自动拆分特性使用IncreasingToUpperBoundRegionSplitPolicy完成负载均衡。但是，region数目随着时间逐渐趋于稳定，当前的region分割点也只依赖于到目前为止的说接收数据。你可能需要始终监控集群内region的负载分配情况，并且如果负载随时间变化，请使用手动拆分或设置更有效的区域拆分策略。最后，你可以尝试即将推出的在线合并功能并贡献您的案例。</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>