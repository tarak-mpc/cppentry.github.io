---
layout:     post
title:      ExternalSorter 排序器在Spark Shuffle过程中设计思路剖析-Spark商业环境实战
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本套技术专栏是作者（秦凯新）平时工作的总结和升华，通过从真实商业环境抽取案例进行总结和分享，并给出商业应用的调优建议和集群环境容量规划等内容，请持续关注本套博客。版权声明：禁止转载，欢迎学习。QQ邮箱地址：1120746959@qq.com，如有任何商业交流，可随时联系。					https://blog.csdn.net/shenshouniu/article/details/83687247				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>本套系列博客从真实商业环境抽取案例进行总结和分享，并给出Spark源码解读及商业实战指导，请持续关注本套博客。版权声明：本套Spark源码解读及商业实战归作者（秦凯新）所有，禁止转载，欢迎学习。</p>
<h3><a id="Spark_2"></a>Spark商业环境实战及调优进阶系列</h3>
<ul>
<li><a href="https://juejin.im/post/5bd55b4de51d4579470247eb" rel="nofollow">Spark商业环境实战-Spark内置框架rpc通讯机制及RpcEnv基础设施 </a></li>
<li><a href="https://juejin.im/post/5bd583f0e51d452c296ef5dd" rel="nofollow">Spark商业环境实战-Spark事件监听总线流程分析</a></li>
<li><a href="https://juejin.im/post/5bd854955188255ca65da7ed" rel="nofollow">Spark商业环境实战-Spark存储体系底层架构剖析</a></li>
<li><a href="https://juejin.im/post/5bd8553af265da0adb311f58" rel="nofollow">Spark商业环境实战-Spark底层多个MessageLoop循环线程执行流程分析</a></li>
<li><a href="https://juejin.im/post/5bd88932f265da0ae5056199" rel="nofollow">Spark商业环境实战-Spark二级调度系统Stage划分算法和最佳任务调度细节剖析</a></li>
<li><a href="https://juejin.im/post/5bdc2676f265da611b57cd20" rel="nofollow">Spark商业环境实战-Spark任务延迟调度及调度池Pool架构剖析</a></li>
<li><a href="https://juejin.im/post/5bdd0a87f265da612859937d" rel="nofollow">Spark商业环境实战-Task粒度的缓存聚合排序结构AppendOnlyMap详细剖析</a></li>
<li><a href="https://juejin.im/post/5bdd3ac86fb9a049d7471f52" rel="nofollow">Spark商业环境实战-ExternalSorter 排序器在Spark Shuffle过程中设计思路剖析</a></li>
<li><a href="https://juejin.im/post/5bdb091ff265da392b2c51bf" rel="nofollow">Spark商业环境实战-StreamingContext启动流程及Dtream 模板源码剖析</a></li>
<li><a href="https://juejin.im/post/5bdb0e76e51d456c6f1cc45f" rel="nofollow">Spark商业环境实战-ReceiverTracker与BlockGenerator数据流接收过程剖析</a></li>
</ul>
<h3><a id="1__Shuffle__14"></a>1.  Shuffle 前置数据结构基础</h3>
<h4><a id="11__15"></a>1.1 内存缓冲区的衍生拓扑图</h4>
<p><img src="https://user-gold-cdn.xitu.io/2018/11/3/166d847c18991140?w=826&amp;h=546&amp;f=png&amp;s=57510" alt=""></p>
<p>上面图展示了Spark基于任务缓存的数据结构，如下简要介绍不同数据结构的用途：</p>
<ul>
<li>
<p>AppendOnlyMap:封装了任务Task基于内存进行插入，更新，聚合，排序等基本操作方法。</p>
</li>
<li>
<p>SizeTrackingAppendOnlyMap：以自身的大小进行样本采集和大小估算。</p>
</li>
<li>
<p>PartionedAppendOnlyMap：</p>
<p>（1）重载了特质WritablePartitionedPairCollection的partitionedDestructiveSortedIterator方法，在该虚方法中调用了AppendOnlyMap的destructiveSortedIterator对底层数组进行整理和排序后获得迭代器。</p>
<p>（2）重载了特质WritablePartitionedPairCollection的insert方法，使其插入时，根据分区(partition, key)作为key。</p>
</li>
<li>
<p>PartitionedPairBuffer：内存缓存结构，主要功能是插入值是有顺序的，主要起缓冲作用，只有顺序插入，没有更新和聚合操作。也即没有changeValue(聚合)和update（更新）操作。</p>
</li>
</ul>
<h3><a id="2__ShuffleManager__30"></a>2.  ShuffleManager 的使命</h3>
<h4><a id="21___ShuffleManager__31"></a>2.1   ShuffleManager 逻辑架构设计</h4>
<p>ShuffleManager是一个特质，其唯一的实现类是 SortShuffleManager，2.0移除了HashShuffleManager。那么他的主要功能是什么呢？主要是用来对shuffle进行管理。SortShuffleManager依赖于存储体系，来完成shuffle过程中MapTask任务数据到内存（AppendOnlyMap）到Spill到磁盘的过程，该过程会涉及到排序，聚合等复杂操作。</p>
<p><img src="https://user-gold-cdn.xitu.io/2018/11/3/166d8ab923ec3d98?w=737&amp;h=358&amp;f=png&amp;s=31385" alt=""></p>
<h3><a id="3__ExternalSorter__37"></a>3.  ExternalSorter 架构精妙设计</h3>
<h4><a id="31___ExternalSorter__38"></a>3.1   ExternalSorter 功能分析</h4>
<p>由上图2.1 逻辑架构所示， ExternalSorter 是SortShuffleManager的底层组件之一，提供的主要功能如下；</p>
<ul>
<li>(1) 将Map任务的输出按照分区存储到JVM的堆中，如果指定了聚合函数，则还会对数据进行聚合，</li>
<li>(2) 使用分区计算器将key分组到各个分区，然后使用自定义比较器对每一个分区的键进行可选的排序。</li>
<li>(3) 将每个分区输出到单个文件中，通过索引文件进行检索，通过reduce端的shuffle获取。</li>
</ul>
<h4><a id="32__ExternalSorter_44"></a>3.2  ExternalSorter主要成员</h4>
<ul>
<li>
<p>aggregator ：对map任务的输出数据进行聚合的聚合器。</p>
</li>
<li>
<p>partioner：对map任务的输出数据按照key计算分区的分区计算器Partioner。</p>
</li>
<li>
<p>ordering: 对map任务的输出数据按照key进行排序的实现类</p>
</li>
<li>
<p>map： 发现什么没？聚合时使用。 new PartitionedAppendOnlyMap[K, C]，当设置了聚合器时，map端将中间结果溢出到磁盘前，先利用此数据结构在内存中对中间结果进行聚合。</p>
<pre><code>   Data structures to store in-memory objects before we spill. Depending on whether we have an
   Aggregator set, we either put objects into an AppendOnlyMap where we combine them, or we
   store them in an array buffer.
</code></pre>
</li>
<li>
<p>buffer : 发现什么没？不聚合时使用，new PartitionedPairBuffer[K, C]，当没有设置聚合器时，map端将中间结果溢出到磁盘前，先利用此数据结构将中间结果存储到内存中。</p>
</li>
<li>
<p>keyComparator : 用于在分区内按照key的hash值进行比较的默认比较器。</p>
</li>
<li>
<p>spills :缓存溢出的文件数组，new ArrayBuffer[SpilledFile]，代码段如下：</p>
<pre><code> private[this] case class SpilledFile(
 file: File,
 blockId: BlockId,
 serializerBatchSizes: Array[Long],
 elementsPerPartition: Array[Long])
</code></pre>
</li>
<li>
<p>_peakMemoryUsedBytes : 内存中数据结构大小的峰值，用于maybeSpillCollection判断。</p>
<pre><code>   Peak size of the in-memory data structure observed so far, in bytes
</code></pre>
</li>
<li>
<p>initialMemoryThreshold : 对集合的内存使用进行跟踪的初始内存阈值，由SparkEnv.get.conf.getLong(“spark.shuffle.spill.initialMemoryThreshold”, 5 * 1024 * 1024)来决定。</p>
</li>
<li>
<p>myMemoryThreshold :初始时等于initialMemoryThreshold，用于maybe Spill的判断。</p>
<h4><a id="33_ExternalSorter_71"></a>3.3 ExternalSorter核心设计-内存缓冲</h4>
<p>spark map任务在执行结束后，会将数据写入磁盘，注意最终都会落盘。但在写入磁盘之前，spark会对Map任务的输出在内存中进行排序或者聚合。注意在内存中，不是在磁盘中。看下面源码吧，清晰的说明了一切。</p>
<ul>
<li>
<p>mergeValue 函数作用在于将新的Value值合并到聚合的结果中。</p>
</li>
<li>
<p>createCombiner 函数用于创建聚合的初始值。</p>
</li>
<li>
<p>update 函数作用是当有新的value值时，即（records.hasNext有值时），update函数调用mergeValue将新的value值合并到之前聚合的结果中。否则会调用createCombiner函数以value作为聚合的初始值。</p>
</li>
<li>
<p>将分区索引与key作为调用AppendOnlyMap的changeValue方法的参数可以。</p>
</li>
<li>
<p>maybeSpillCollection函数进行可能的磁盘溢出。</p>
</li>
<li>
<p>insertAll :可谓气绝古今，一气呵成，若想实现内存缓冲，insertAll就是MapTask数据写入入口。</p>
<pre><code>    def insertAll(records: Iterator[Product2[K, V]]): Unit = {
     val shouldCombine = aggregator.isDefined
   if (shouldCombine) {
     // Combine values in-memory first using our AppendOnlyMap
     val mergeValue = aggregator.get.mergeValue
     val createCombiner = aggregator.get.createCombiner
     var kv: Product2[K, V] = null
     val update = (hadValue: Boolean, oldValue: C) =&gt; {
       if (hadValue) mergeValue(oldValue, kv._2) else createCombiner(kv._2)
     }
     while (records.hasNext) {
       addElementsRead()
       kv = records.next()

       map.changeValue((getPartition(kv._1), kv._1), update)    ====&gt;气绝古今的亮点
       
       maybeSpillCollection(usingMap = true)
     }
   } else {
     // Stick values into our buffer
     while (records.hasNext) {
       addElementsRead()
       val kv = records.next()
       
       buffer.insert(getPartition(kv._1), kv._1, kv._2.asInstanceOf[C]) ====&gt;气绝古今的亮点
       
       maybeSpillCollection(usingMap = false)
     }
   }
 }
</code></pre>
</li>
</ul>
<h4><a id="34__ExternalSorter_111"></a>3.4  ExternalSorter核心设计-内存溢出判断</h4>
<ul>
<li>
<p>maybeSpillCollection 用于是否溢出的内存判断，虽然ExternalSorter使用了PartitionedAppendOnlyMap和PartitionedPairBuffer，若数据量较少时不会有问题，一旦数据暴增时，将会引起系统OOM.</p>
</li>
<li>
<p>maybeSpillCollection控制了数据写入磁盘的频率，若shuffle写入磁盘频率过高，容易降低磁盘I/O的效率。</p>
</li>
<li>
<p>使用了内存样本采集和大小估算的PartitionedAppendOnlyMap和PartitionedPairBuffer。</p>
<pre><code>   private def maybeSpillCollection(usingMap: Boolean): Unit = {
    var estimatedSize = 0L
    if (usingMap) {
      estimatedSize = map.estimateSize()
      if (maybeSpill(map, estimatedSize)) {
        map = new PartitionedAppendOnlyMap[K, C]
      }
    } else {
      estimatedSize = buffer.estimateSize()
      if (maybeSpill(buffer, estimatedSize)) {
        buffer = new PartitionedPairBuffer[K, C]
      }
    }

    if (estimatedSize &gt; _peakMemoryUsedBytes) {
      _peakMemoryUsedBytes = estimatedSize
    }
  }
</code></pre>
</li>
</ul>
</li>
<li>
<p>估算超过阈值，开始溢出操作,根据myMemoryThreshold进行判断。</p>
<pre><code>  * Spill some data to disk to release memory, which will be called by TaskMemoryManager
  * when there is not enough memory for the task.

   protected def maybeSpill(collection: C, currentMemory: Long): Boolean = {
     var shouldSpill = false
     if (elementsRead % 32 == 0 &amp;&amp; currentMemory &gt;= myMemoryThreshold) {
       // Claim up to double our current memory from the shuffle memory pool
       val amountToRequest = 2 * currentMemory - myMemoryThreshold
       val granted = acquireMemory(amountToRequest)
       myMemoryThreshold += granted
       // If we were granted too little memory to grow further (either tryToAcquire returned 0,
       // or we already had more memory than myMemoryThreshold), spill the current collection
       shouldSpill = currentMemory &gt;= myMemoryThreshold
     }
     shouldSpill = shouldSpill || _elementsRead &gt; numElementsForceSpillThreshold
     // Actually spill
     if (shouldSpill) {
       _spillCount += 1
       logSpillage(currentMemory)
       
       spill(collection)  ====&gt;气绝古今的亮点
       
       _elementsRead = 0
       _memoryBytesSpilled += currentMemory
       releaseMemory()
     }
     shouldSpill
   }
</code></pre>
</li>
</ul>
<h4><a id="34__ExternalSorter_167"></a>3.4  ExternalSorter核心设计-内存数据溢出落盘</h4>
<h4><a id="sparkCome_on__168"></a>==&gt;接下来的内容会相当精彩，因为我会非常详细的介绍spark在内存使用上的独孤九剑，Come on ：</h4>
<h4><a id="341_PartitionedAppendOnlyMap__169"></a>3.4.1 PartitionedAppendOnlyMap 第一绝招</h4>
<p>因为PartitionedAppendOnlyMap的底层是散列存储，因此溢出过程中：</p>
<ul>
<li>（1）首先会调用partitionedDestructiveSortedIterator方法，实现数组中元素向低索引端整理。</li>
<li>（2）然后根据指定的比较器对元素进行排序，先按照分区ID进行排序，然后会按照key进行排序。</li>
<li>（3）最后返回Iterator数据迭代器，用于内存数据向外迭代。</li>
</ul>
<h4><a id="342_ExternalSorter__175"></a>3.4.2 ExternalSorter 第二绝招</h4>
<p>发现了什么，调用了PartitionedAppendOnlyMap.destructiveSortedWritablePartitionedIterator，用到了独孤九剑第一式。<mark>&gt;整理</mark>&gt;排序==&gt;返回迭代器</p>
<pre><code>      override protected[this] def spill(collection: WritablePartitionedPairCollection[K, C]): Unit = {
        val inMemoryIterator = collection.destructiveSortedWritablePartitionedIterator(comparator)
        val spillFile = spillMemoryIteratorToDisk(inMemoryIterator)
        spills += spillFile
      } 
</code></pre>
<h4><a id="343_spillMemoryIteratorToDisk__183"></a>3.4.3 spillMemoryIteratorToDisk 第三绝招</h4>
<ul>
<li>
<p>（1）迭代写入磁盘</p>
</li>
<li>
<p>（2）创建唯一的blockId和文件，调用diskBlockManager开始写入文件。</p>
</li>
<li>
<p>（3）按照分区顺序排序。</p>
<pre><code>  private[this] def spillMemoryIteratorToDisk(inMemoryIterator: WritablePartitionedIterator)
      : SpilledFile = {
    // Because these files may be read during shuffle, their compression must be controlled by
    // spark.shuffle.compress instead of spark.shuffle.spill.compress, so we need to use
    // createTempShuffleBlock here; see SPARK-3426 for more context.
    val (blockId, file) = diskBlockManager.createTempShuffleBlock()

    // These variables are reset after each flush
    var objectsWritten: Long = 0
    val spillMetrics: ShuffleWriteMetrics = new ShuffleWriteMetrics
    val writer: DiskBlockObjectWriter =
      blockManager.getDiskWriter(blockId, file, serInstance, fileBufferSize, spillMetrics)

    // List of batch sizes (bytes) in the order they are written to disk
    val batchSizes = new ArrayBuffer[Long]

    // How many elements we have in each partition
    val elementsPerPartition = new Array[Long](numPartitions)

    // Flush the disk writer's contents to disk, and update relevant variables.
    // The writer is committed at the end of this process.
    def flush(): Unit = {
      val segment = writer.commitAndGet()
      batchSizes += segment.length
      _diskBytesSpilled += segment.length
      objectsWritten = 0
    }

    var success = false
    try {
      while (inMemoryIterator.hasNext) {

        val partitionId = inMemoryIterator.nextPartition()
        require(partitionId &gt;= 0 &amp;&amp; partitionId &lt; numPartitions,
          s"partition Id: ${partitionId} should be in the range [0, ${numPartitions})")
        inMemoryIterator.writeNext(writer)
        elementsPerPartition(partitionId) += 1
        objectsWritten += 1

        if (objectsWritten == serializerBatchSize) {
          flush()
        }
      }
      if (objectsWritten &gt; 0) {
        flush()
      } else {
        writer.revertPartialWritesAndClose()
      }
      success = true
    } finally {
      if (success) {
        writer.close()
      } else {
        // This code path only happens if an exception was thrown above before we set success;
        // close our stuff and let the exception be thrown further
        writer.revertPartialWritesAndClose()
        if (file.exists()) {
          if (!file.delete()) {
            logWarning(s"Error deleting ${file}")
          }
        }
      }
}

SpilledFile(file, blockId, batchSizes.toArray, elementsPerPartition)
</code></pre>
<p>}</p>
</li>
</ul>
<h3><a id="35__ExternalSorter_255"></a>3.5  ExternalSorter核心设计-内存缓冲与溢出磁盘文件最终合并交付</h3>
<p>ExternalSorter的writePartitionedFile闪亮登场，通过整合内存和多个溢写文件，最终每个MapTask只会生成一份正式的Block文件。</p>
<h4><a id="IDkeyMapTaskSpark16HashSorterMapTaskNbucketreduce_Task_257"></a>注意此时各个分区的数据按照分区ID和key的顺序输出的最终的正式文件中。也即每一个MapTask只会生成一个磁盘文件。在Spark1.6之前的版本，因为HashSorter的存在，一个MapTask会生成N个bucket文件（数量有reduce Task而定）。</h4>
<h4><a id="351_writePartitionedFile_260"></a>3.5.1 我们接下来看看writePartitionedFile的代码片段吧：</h4>
<ul>
<li>
<p>注意磁盘中若没有溢出文件，spills数组为空,则直接按照分区顺序写入磁盘即可。</p>
</li>
<li>
<p>注意磁盘中若有溢出文件，spills数组不为空，则开始读取磁盘文件到内存，进行聚合排序后统一写成正式的Block文件。</p>
<pre><code>   * Write all the data added into this ExternalSorter into a file in the disk store. This is
   * called by the SortShuffleWriter.

     def writePartitionedFile(
          blockId: BlockId,
          outputFile: File): Array[Long] = {
    
        // Track location of each range in the output file
        val lengths = new Array[Long](numPartitions)
        val writer = blockManager.getDiskWriter(blockId, outputFile, serInstance, fileBufferSize,
          context.taskMetrics().shuffleWriteMetrics)
    
        if (spills.isEmpty) {
          // Case where we only have in-memory data
          val collection = if (aggregator.isDefined) map else buffer
          val it = collection.destructiveSortedWritablePartitionedIterator(comparator)
          while (it.hasNext) {
            val partitionId = it.nextPartition()                 ====&gt;气绝古今的亮点
            while (it.hasNext &amp;&amp; it.nextPartition() == partitionId) {
              it.writeNext(writer)
            }
            val segment = writer.commitAndGet()
            lengths(partitionId) = segment.length
          }
        } else {
        
          // We must perform merge-sort; get an iterator by partition and write everything directly.
          
          for ((id, elements) &lt;- this.partitionedIterator) {      ====&gt;气绝古今的亮点
          
            if (elements.hasNext) {
              for (elem &lt;- elements) {
                writer.write(elem._1, elem._2)
              }
              val segment = writer.commitAndGet()
              lengths(id) = segment.length              
            }
          }
        }
    
        writer.close()
        context.taskMetrics().incMemoryBytesSpilled(memoryBytesSpilled)
        context.taskMetrics().incDiskBytesSpilled(diskBytesSpilled)
        context.taskMetrics().incPeakExecutionMemory(peakMemoryUsedBytes)
    
        lengths
      }
</code></pre>
</li>
</ul>
<h4><a id="352_spillspartitionedIteratormergeBlock_313"></a>3.5.2 spills数组不为空，让我们接下来看看partitionedIterator的代码片段吧，气绝古今的亮点在于封装了merge方法，直接给出一个迭代器，根据此迭代器，可以写正式Block文件：</h4>
<p>先看看Spark官方的解释吧，通过groupByPartition会给每一个分区生成一个IteratorForPartition迭代器：</p>
<pre><code>       * Return an iterator over all the data written to this object, grouped by partition and
       * aggregated by the requested aggregator. For each partition we then have an iterator over its
       * contents, and these are expected to be accessed in order (you can't "skip ahead" to one
       * partition without reading the previous one). Guaranteed to return a key-value pair for each
       * partition, in order of partition ID.
       *
       * For now, we just merge all the spilled files in once pass, but this can be modified to
       * support hierarchical merging.
       * Exposed for testing.



        def partitionedIterator: Iterator[(Int, Iterator[Product2[K, C]])] = {
        val usingMap = aggregator.isDefined
        val collection: WritablePartitionedPairCollection[K, C] = if (usingMap) map else buffer
        if (spills.isEmpty) {      ====&gt;气绝古今的亮点，重新判断一次
        
          * Special case: if we have only in-memory data, we don't need to merge streams, 
          *and perhaps we don't even need to sort by anything other than partition ID
          
          if (!ordering.isDefined) {
            // The user hasn't requested sorted keys, so only sort by partition ID, not key
            groupByPartition(destructiveIterator(collection.partitionedDestructiveSortedIterator(None)))
          } else {
            // We do need to sort by both partition ID and key
            groupByPartition(destructiveIterator(                            ====&gt;气绝古今的亮点
              collection.partitionedDestructiveSortedIterator(Some(keyComparator))))
          }
        } else {
          // Merge spilled and in-memory data
          merge(spills, destructiveIterator(
            collection.partitionedDestructiveSortedIterator(comparator)))     ====&gt;气绝古今的亮点
        }
      }
</code></pre>
<p>通过groupByPartition会给每一个分区生成一个IteratorForPartition迭代器，实际上使用的都是相同的数据源，重点在这里IteratorForPartition，成精了：</p>
<pre><code>       * Given a stream of ((partition, key), combiner) pairs 
       * assumed to be sorted by partition ID*,group together the pairs for each partition 
       *  into a sub-iterator.param data an iterator of elements, assumed to already be sorted
       * by partition ID

          private def groupByPartition(data: Iterator[((Int, K), C)])
              : Iterator[(Int, Iterator[Product2[K, C]])] =
          {
            val buffered = data.buffered
            (0 until numPartitions).iterator.map(p =&gt; (p, new IteratorForPartition(p, buffered)))
          }
</code></pre>
<p>IteratorForPartition可谓是煞费苦心，直接通过判断给定的partitionId和数据源中的分区Id是否对应的上，来过滤元数据：</p>
<pre><code>       * An iterator that reads only the elements for a given partition ID from an 
       * underlying buffered，stream, assuming this partition is the next one to be read. Used to
       * make it easier to return partitioned iterators from our in-memory collection.

      private[this] class IteratorForPartition(partitionId: Int, data: BufferedIterator[((Int, K), C)])
        extends Iterator[Product2[K, C]]
      {
        override def hasNext: Boolean = data.hasNext &amp;&amp; data.head._1._1 == partitionId
    
        override def next(): Product2[K, C] = {
          if (!hasNext) {
            throw new NoSuchElementException
          }
          val elem = data.next()
          (elem._1._2, elem._2)
        }
      }
</code></pre>
<h4><a id="352_spillsmerage_386"></a>3.5.2 spills数组不为空，让我们接下来看看merage的代码片段吧：</h4>
<pre><code>   * Merge a sequence of sorted files, giving an iterator over partitions and then over elements
   * inside each partition. This can be used to either write out a new file or return data to
   * the user.
   *
   * Returns an iterator over all the data written to this object, grouped by partition. For each
   * partition we then have an iterator over its contents, and these are expected to be accessed
   * in order (you can't "skip ahead" to one partition without reading the previous one).
   * Guaranteed to return a key-value pair for each partition, in order of partition ID.
</code></pre>
<p>桃李不言，下自成蹊。merge最终返回一个迭代器，方便按照分区顺序写正式的文件：</p>
<pre><code> private def merge(spills: Seq[SpilledFile], inMemory: Iterator[((Int, K), C)])
      : Iterator[(Int, Iterator[Product2[K, C]])] = {
    val readers = spills.map(new SpillReader(_))           ====&gt;气绝古今的亮点，读磁盘到内存
    val inMemBuffered = inMemory.buffered
    (0 until numPartitions).iterator.map { p =&gt;
      val inMemIterator = new IteratorForPartition(p, inMemBuffered)
      
      val iterators = readers.map(_.readNextPartition()) ++ Seq(inMemIterator) ====&gt;气绝古今的亮点
      
      if (aggregator.isDefined) {
        // Perform partial aggregation across partitions 
        (p, mergeWithAggregation(
          iterators, aggregator.get.mergeCombiners, keyComparator, ordering.isDefined)) ====&gt;气绝古今的亮点
      } else if (ordering.isDefined) {
        // No aggregator given, but we have an ordering (e.g. used by reduce tasks in sortByKey);
        // sort the elements without trying to merge them
        (p, mergeSort(iterators, ordering.get))             ====&gt;气绝古今的亮点
      } else {
        (p, iterators.iterator.flatten)                      ====&gt;气绝古今的亮点，简单累加，输出
      }
    }
  }
</code></pre>
<h4><a id="36__422"></a>3.6 超级总结</h4>
<p>spark map任务在执行结束后，需要进行持久化过程，因此会出现两种情况：</p>
<ul>
<li>有溢出文件 将溢出文件与内存中的文件合并后写入磁盘。</li>
<li>无溢出文件 将内存中的数据进行整理，排序后写入磁盘。<br>
实际过程如下：<br>
<img src="https://user-gold-cdn.xitu.io/2018/11/3/166d8f7fa128a515?w=1227&amp;h=331&amp;f=png&amp;s=77046" alt=""></li>
</ul>
<h2><a id="4___430"></a>4  最后</h2>
<p>几经易稿，终于成文，需进一步完善。</p>
<p>秦凯新 于深圳</p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>