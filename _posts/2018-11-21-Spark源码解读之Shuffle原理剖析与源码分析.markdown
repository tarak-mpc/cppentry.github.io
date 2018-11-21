---
layout:     post
title:      Spark源码解读之Shuffle原理剖析与源码分析
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/qq_37142346/article/details/81875249				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>在前面几篇文章中，介绍了Spark的启动流程<a href="https://blog.csdn.net/qq_37142346/article/details/81204921" rel="nofollow">Spark内核架构流程深度剖析</a>，<a href="https://blog.csdn.net/qq_37142346/article/details/81317017" rel="nofollow">Spark源码分析之DAGScheduler详解</a>，<a href="https://blog.csdn.net/qq_37142346/article/details/81367757" rel="nofollow">Spark源码解读之Executor以及Task工作原理剖析</a>，<a href="https://blog.csdn.net/qq_37142346/article/details/81395725" rel="nofollow">Spark源码解读之Executor以及Task工作原理剖析</a>等Spark重要组件的源码剖析之后，接着之前的文章，本篇文章来剖析Shuffle的原理，shuffle阶段无论是mapreduce还是Spark都是其核心以及难点，了解了其shuffle操作原理之后，更加有利于我们调优系统 ，避免不必要的错误。</p>

<p>转载请标明原文地址：<a href="https://blog.csdn.net/qq_37142346/article/details/81875249" rel="nofollow">原文地址</a></p>

<p>spark的shuffle操作有之前的版本和现在优化后的版本，它可以通过一个参数来调节，具体我们后面会详述，本篇主要从以下几个方面来深入Shuffle原理：</p>

<ol>
<li>普通shuffle操作的原理剖析</li>
<li>优化后的shuffle操作原理剖析</li>
<li>shuffle源码剖析</li>
</ol>



<h2 id="普通shuffle操作的原理剖析">普通shuffle操作的原理剖析</h2>

<p>首先来看看早期没有任何优化的shuffle操作的原理，如下图是shuffle简单原理图：</p>

<p><img src="https://img-blog.csdn.net/20180820164931960?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3MTQyMzQ2/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<p>这里我们假设每一个节点上运行着两个ShuffleMapTask，每一个ShuffleMapTask都会为每一个ResultTask创建一个bucket缓存，并且接着会将bucket缓存中的数据刷新到磁盘文件shuffleBlockFile中，ShuffleMapTask刷新到磁盘中的数据信息会封装在MapStatus中，发送到Driver的DAGScheduler的MapOutputTracker中，而每一个ResultTask会用BlockStoreShuffleFetcher去MapOutputTracker中获取自己所需要的数据的信息，然后通过底层的BlockManager将数据拉取过来。</p>

<p>将数据拉取过来之后，会将这些数据组成一个RDD，即ShuffleRDD，优先存入内存当中，其次再写入磁盘中。然后每一个ResultTask针对这些RDD数据执行自己的聚合操作或者算子函数生成MapPartitionRDD。</p>

<p>以上就是普通Shuffle操作的执行原理，从上图我们可以发现一个问题，每一个ShuffleMapTask都需要为每一个ResultTask生成一个文件和bucket缓存，假设有100个ShuffleMapTask，100个ResultTask，那么就需要总共生成10000个文件，此时会有大量的磁盘IO操作，严重的影响shuffle的性能。</p>

<p>因此，在后期的新版本的Spark中，加入了优化操作，具体原理我们来看看。</p>



<h2 id="优化后的shuffle操作原理剖析">优化后的shuffle操作原理剖析</h2>

<p>优化后的Shuffle操作原理图如下： <br>
<img src="https://img-blog.csdn.net/20180820171724349?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3MTQyMzQ2/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<p>这里假设我们的服务器上有两个CPU cores，运行着4个ShuffleMapTask，因此每次可以并行执行两个两个ShuffleMapTask，在之前的版本中，当前并行执行的一批ShuffleMapTask执行完毕之后执行下一批时会重新生成bucket缓存，而且在刷新到磁盘上的时候也会重新生成ShuffleBlockFile。但是在优化后的Shuffle操作中它不会重新生成缓存和磁盘文件，而是将数据写入之前的缓存和磁盘文件中，即合并了多个ShuffleMpaTask产生的文件，这也叫做<strong>consolidation机制</strong>。在多个ShuffleMapTask合并产生的文件称为一组<strong>ShuffleGroup</strong>，里面存储了多个ShuffleMapTask的数据，每个ShuffleMapTask的数据称为一个<strong>segment</strong>，此外还会通过一些索引来标识每个ShuffleMapTask在ShuffleBlockFile中的位置以及偏移量，来进行区分不同的ShuffleMapTask产生的数据。</p>

<p>优化参数的设置只需在SparkConf中设置即可，即设置<strong>spark.shuffle.consolidateFiles</strong>参数为true即可，可以看出来，在优化后的shuffle操作，它产生的磁盘文件是<strong>cpu core数量*ResultTask</strong>的数量，比如这里假设了2个cpu core，有100个ResultTask，因此会产生200个磁盘文件，相比之前没有优化的Shuffle操作，减少了20倍的磁盘文件，对系统的性能有很大的提升。</p>

<p>对Spark Shuffle操作有以下两个特点：</p>

<ol>
<li>在早期版本中，bucket缓存十分重要，因为ShuffleMapTask只有将数据写入缓存中，然后才会刷新到磁盘中，但是如果缓存过多，有可能会导致OutOfMemory，因此，在新版中，进行了优化设置，缩小了缓存的大小，默认是100KB，当超过这个阀值时，就会将数据一点点写入磁盘中。但是这样也有一个缺点，当数据过多的时候 ，会有大量的磁盘IO操作。</li>
<li>与MpaReduce的Shuffle不一样，MapReduce它必须将所有的数据写入磁盘文件之后才会去进行Reduce操作，因为MapReduce会对数据进行排序。但是Spark不会对数据进行排序，因此不需要等待全部数据写入磁盘就ResultTask就可以拉取数据进行计算。这样，明显比MapReduce快很多，但是MapReduce可以直接在Reduce端对每一个key的value进行计算，但是Spark由于实时拉取的机制，因此只有先执行action操作，进行Shuffle操作，生成对应的MapPartitionRDD，然后去进行计算。</li>
</ol>

<h2 id="shuffle源码剖析"><strong>shuffle源码剖析</strong></h2>

<p>在上面了解了Shuffle的原理之后，我们来简单看看其内部源码。</p>

<p>Shuffle操作会在stage之间进行操作，之前的stage会先将数据写入到磁盘，这里使用了HashShuffleWriter，在这个类中，我们来看看write方法：</p>



<pre class="prettyprint"><code class=" hljs scala"><span class="hljs-javadoc">/** Write a bunch of records to this task's output */</span>
  <span class="hljs-javadoc">/**
    * 将每个ShuffleMapTask计算出来的新的RDD的partition数据，写入本地磁盘
    * <span class="hljs-javadoctag">@param</span> records
    */</span>
  <span class="hljs-keyword">override</span> <span class="hljs-keyword">def</span> write(records: Iterator[_ &lt;: Product2[K, V]]): Unit = {
    <span class="hljs-comment">//判断是否需要进行本地，如果是reduceByKey这种操作，则要进行聚合操作</span>
    <span class="hljs-comment">//即dep.aggregator.isDefined为true</span>
    <span class="hljs-comment">//dep.mapSideCombine也为true</span>
    <span class="hljs-keyword">val</span> iter = <span class="hljs-keyword">if</span> (dep.aggregator.isDefined) {
      <span class="hljs-keyword">if</span> (dep.mapSideCombine) {
        <span class="hljs-comment">//这里进行本地聚合操作，比如本地有(hello,1),(hello,1)</span>
        <span class="hljs-comment">//则可以聚合成(hello,2)</span>
        dep.aggregator.get.combineValuesByKey(records, context)
      } <span class="hljs-keyword">else</span> {
        records
      }
    } <span class="hljs-keyword">else</span> {
      require(!dep.mapSideCombine, <span class="hljs-string">"Map-side combine without Aggregator specified!"</span>)
      records
    }

    <span class="hljs-comment">//如果需要本地聚合，则先进行聚合</span>
    <span class="hljs-comment">//然后遍历数据，对每一个数据，进行partition操作，默认的是HashPartitioner,并且生成bucketId</span>
    <span class="hljs-comment">//也就表示这数据要写入哪一个bucket</span>
    <span class="hljs-keyword">for</span> (elem &lt;- iter) {
      <span class="hljs-keyword">val</span> bucketId = dep.partitioner.getPartition(elem._1)
      <span class="hljs-comment">//调用shuffleBlockManager.forMapTask()方法生成bucketId对应的writer,然后用writer将数据写入bucket</span>
      shuffle.writers(bucketId).write(elem)
    }
  }</code></pre>

<p>在写入bucket的时候，会调用shuffleBlockManager.forMapTask()方法生成bucketId对应的writer,然后用writer将数据写入bucket，因此我们进行forMapTask这个 方法中：</p>



<pre class="prettyprint"><code class=" hljs scala"><span class="hljs-javadoc">/**
   * Get a ShuffleWriterGroup for the given map task, which will register it as complete
   * when the writers are closed successfully
   */</span>
  <span class="hljs-javadoc">/**
    * 给每一个map task生成 一个ShuffleWriterGroup
    */</span>
  <span class="hljs-keyword">def</span> forMapTask(shuffleId: Int, mapId: Int, numBuckets: Int, serializer: Serializer,
      writeMetrics: ShuffleWriteMetrics) = {
    <span class="hljs-keyword">new</span> ShuffleWriterGroup {
      shuffleStates.putIfAbsent(shuffleId, <span class="hljs-keyword">new</span> ShuffleState(numBuckets))
      <span class="hljs-keyword">private</span> <span class="hljs-keyword">val</span> shuffleState = shuffleStates(shuffleId)
      <span class="hljs-keyword">private</span> <span class="hljs-keyword">var</span> fileGroup: ShuffleFileGroup = <span class="hljs-keyword">null</span>

      <span class="hljs-keyword">val</span> openStartTime = System.nanoTime
      <span class="hljs-comment">//判断是否开启了consolidate优化，如果开启了，就不会为每一个bucket获取一个输出文件</span>
      <span class="hljs-comment">//而是为每一个bucket获取一个ShuffleGroup的write</span>
      <span class="hljs-keyword">val</span> writers: Array[BlockObjectWriter] = <span class="hljs-keyword">if</span> (consolidateShuffleFiles) {
        fileGroup = getUnusedFileGroup()
        Array.tabulate[BlockObjectWriter](numBuckets) { bucketId =&gt;
          <span class="hljs-comment">//首先生成一个唯一的blockId，然后用bucketId来调用ShuffleFileGroup的apply函数来获取一个writer</span>
          <span class="hljs-keyword">val</span> blockId = ShuffleBlockId(shuffleId, mapId, bucketId)
          <span class="hljs-comment">//使用blockManager.getDiskWriter()函数来获取一个writer</span>
          <span class="hljs-comment">//实际上在开启优化配置后，对一个bucketId，不再是像之前一样获取一个独立的ShuffleBlockFile的writer</span>
          <span class="hljs-comment">//而是获取ShuffleFileGroup中的一个writer</span>
          <span class="hljs-comment">//这样就实现了多个ShufffleMapTask的输出文件的合并</span>
          blockManager.getDiskWriter(blockId, fileGroup(bucketId), serializer, bufferSize,
            writeMetrics)
        }
      } <span class="hljs-keyword">else</span> {
        <span class="hljs-comment">//如果没有进行shuffle优化配置，也会针对每一个shuffleMapTask创建一个ShuffleBlockFile</span>
        Array.tabulate[BlockObjectWriter](numBuckets) { bucketId =&gt;
          <span class="hljs-keyword">val</span> blockId = ShuffleBlockId(shuffleId, mapId, bucketId)
          <span class="hljs-keyword">val</span> blockFile = blockManager.diskBlockManager.getFile(blockId)
          <span class="hljs-comment">// Because of previous failures, the shuffle file may already exist on this machine.</span>
          <span class="hljs-comment">// If so, remove it.</span>
          <span class="hljs-comment">//如果ShuffleBlockFile存在，则进行删除</span>
          <span class="hljs-keyword">if</span> (blockFile.exists) {
            <span class="hljs-keyword">if</span> (blockFile.delete()) {
              logInfo(s<span class="hljs-string">"Removed existing shuffle file $blockFile"</span>)
            } <span class="hljs-keyword">else</span> {
              logWarning(s<span class="hljs-string">"Failed to remove existing shuffle file $blockFile"</span>)
            }
          }
          <span class="hljs-comment">//写入磁盘中</span>
          blockManager.getDiskWriter(blockId, blockFile, serializer, bufferSize, writeMetrics)
        }
      }</code></pre>

<p>将结果数据写入磁盘文件之后，就开始了Shuffle操作，Shuffle操作的入口在RDD包下的ShuffleRDD类中，源码如下：</p>



<pre class="prettyprint"><code class=" hljs scala"> <span class="hljs-javadoc">/**
    *shuffle的入口
    */</span>
  <span class="hljs-keyword">override</span> <span class="hljs-keyword">def</span> compute(split: Partition, context: TaskContext): Iterator[(K, C)] = {
    <span class="hljs-comment">//这里会调用shuffleManager.getReader()来获取一个HashShuffleReader</span>
    <span class="hljs-comment">//然后调用它的reader方法来拉取resultTask需要聚合的数据</span>
    <span class="hljs-keyword">val</span> dep = dependencies.head.asInstanceOf[ShuffleDependency[K, V, C]]
    SparkEnv.get.shuffleManager.getReader(dep.shuffleHandle, split.index, split.index + <span class="hljs-number">1</span>, context)
      .read()
      .asInstanceOf[Iterator[(K, C)]]
  }</code></pre>

<p>在读取数据的时候使用的HashShuffleReader这个类。在这个类的read方法中：</p>



<pre class="prettyprint"><code class=" hljs scala"> <span class="hljs-javadoc">/** Read the combined key-values for this reduce task */</span>
  <span class="hljs-keyword">override</span> <span class="hljs-keyword">def</span> read(): Iterator[Product2[K, C]] = {
    <span class="hljs-keyword">val</span> ser = Serializer.getSerializer(dep.serializer)
    <span class="hljs-comment">//通过BlockStoreShuffleFetcher的fetch方法来从DAGScheduler的MapOutputTrackerMaster中获取</span>
    <span class="hljs-comment">//自己需要的数据的信息，然后底层再通过对应的BlockManager拉取需要的数据</span>
    <span class="hljs-keyword">val</span> iter = BlockStoreShuffleFetcher.fetch(handle.shuffleId, startPartition, context, ser)

    <span class="hljs-keyword">val</span> aggregatedIter: Iterator[Product2[K, C]] = <span class="hljs-keyword">if</span> (dep.aggregator.isDefined) {
      <span class="hljs-keyword">if</span> (dep.mapSideCombine) {
        <span class="hljs-keyword">new</span> InterruptibleIterator(context, dep.aggregator.get.combineCombinersByKey(iter, context))
      } <span class="hljs-keyword">else</span> {
        <span class="hljs-keyword">new</span> InterruptibleIterator(context, dep.aggregator.get.combineValuesByKey(iter, context))
      }
    } <span class="hljs-keyword">else</span> {
      require(!dep.mapSideCombine, <span class="hljs-string">"Map-side combine without Aggregator specified!"</span>)

      <span class="hljs-comment">// Convert the Product2s to pairs since this is what downstream RDDs currently expect</span>
      iter.asInstanceOf[Iterator[Product2[K, C]]].map(pair =&gt; (pair._1, pair._2))
    }

    <span class="hljs-comment">// Sort the output if there is a sort ordering defined.</span>
    dep.keyOrdering <span class="hljs-keyword">match</span> {
      <span class="hljs-keyword">case</span> Some(keyOrd: Ordering[K]) =&gt;
        <span class="hljs-comment">// Create an ExternalSorter to sort the data. Note that if spark.shuffle.spill is disabled,</span>
        <span class="hljs-comment">// the ExternalSorter won't spill to disk.</span>
        <span class="hljs-keyword">val</span> sorter = <span class="hljs-keyword">new</span> ExternalSorter[K, C, C](ordering = Some(keyOrd), serializer = Some(ser))
        sorter.insertAll(aggregatedIter)
        context.taskMetrics.incMemoryBytesSpilled(sorter.memoryBytesSpilled)
        context.taskMetrics.incDiskBytesSpilled(sorter.diskBytesSpilled)
        sorter.iterator
      <span class="hljs-keyword">case</span> None =&gt;
        aggregatedIter
    }
  }</code></pre>

<p>这里会使用BlockStoreShuffleFetcher调用fetch方法来拉取所需要的数据，我们可以进行这个方法中简单看一下：</p>



<pre class="prettyprint"><code class=" hljs scala"> <span class="hljs-keyword">def</span> fetch[T](
      shuffleId: Int,
      reduceId: Int,
      context: TaskContext,
      serializer: Serializer)
    : Iterator[T] =
  {
    logDebug(<span class="hljs-string">"Fetching outputs for shuffle %d, reduce %d"</span>.format(shuffleId, reduceId))
    <span class="hljs-keyword">val</span> blockManager = SparkEnv.get.blockManager

    <span class="hljs-keyword">val</span> startTime = System.currentTimeMillis

    <span class="hljs-comment">//获取一个全局的MapOutputTracker，并且调用其getServerStatuses方法</span>
    <span class="hljs-comment">//注意这里传入了两个参数，shuffleId和reduceId</span>
    <span class="hljs-comment">//shuffle有两个stage参与，因此shuffleId代表表示上一个stage，使用这个参数来获取</span>
    <span class="hljs-comment">//上一个stage的ShuffleMapTask shuffle write输出的MapStatus数据信息</span>
    <span class="hljs-comment">//在获取到MapStatus之后，还要使用reduceId来拉取当前stage需要获取的之前stage的ShuffleMapTask的输出文件信息</span>
    <span class="hljs-comment">//这个getServerStatuses方法是需要走网络通信的，因为它要连接Driver上的DAGScheduler来获取MapOutputTracker上的数据信息</span>
    <span class="hljs-keyword">val</span> statuses = SparkEnv.get.mapOutputTracker.getServerStatuses(shuffleId, reduceId)

    logDebug(<span class="hljs-string">"Fetching map output location for shuffle %d, reduce %d took %d ms"</span>.format(
      shuffleId, reduceId, System.currentTimeMillis - startTime))

    <span class="hljs-keyword">val</span> splitsByAddress = <span class="hljs-keyword">new</span> HashMap[BlockManagerId, ArrayBuffer[(Int, Long)]]
    <span class="hljs-keyword">for</span> (((address, size), index) &lt;- statuses.zipWithIndex) {
      splitsByAddress.getOrElseUpdate(address, ArrayBuffer()) += ((index, size))
    }

    <span class="hljs-keyword">val</span> blocksByAddress: Seq[(BlockManagerId, Seq[(BlockId, Long)])] = splitsByAddress.toSeq.map {
      <span class="hljs-keyword">case</span> (address, splits) =&gt;
        (address, splits.map(s =&gt; (ShuffleBlockId(shuffleId, s._1, reduceId), s._2)))
    }

    <span class="hljs-keyword">def</span> unpackBlock(blockPair: (BlockId, Try[Iterator[Any]])) : Iterator[T] = {
      <span class="hljs-keyword">val</span> blockId = blockPair._1
      <span class="hljs-keyword">val</span> blockOption = blockPair._2
      blockOption <span class="hljs-keyword">match</span> {
        <span class="hljs-keyword">case</span> Success(block) =&gt; {
          block.asInstanceOf[Iterator[T]]
        }
        <span class="hljs-keyword">case</span> Failure(e) =&gt; {
          blockId <span class="hljs-keyword">match</span> {
            <span class="hljs-keyword">case</span> ShuffleBlockId(shufId, mapId, _) =&gt;
              <span class="hljs-keyword">val</span> address = statuses(mapId.toInt)._1
              <span class="hljs-keyword">throw</span> <span class="hljs-keyword">new</span> FetchFailedException(address, shufId.toInt, mapId.toInt, reduceId, e)
            <span class="hljs-keyword">case</span> _ =&gt;
              <span class="hljs-keyword">throw</span> <span class="hljs-keyword">new</span> SparkException(
                <span class="hljs-string">"Failed to get block "</span> + blockId + <span class="hljs-string">", which is not a shuffle block"</span>, e)
          }
        }
      }
    }

    <span class="hljs-keyword">val</span> blockFetcherItr = <span class="hljs-keyword">new</span> ShuffleBlockFetcherIterator(
      context,
      SparkEnv.get.blockManager.shuffleClient,
      blockManager,
      blocksByAddress,
      serializer,
      SparkEnv.get.conf.getLong(<span class="hljs-string">"spark.reducer.maxMbInFlight"</span>, <span class="hljs-number">48</span>) * <span class="hljs-number">1024</span> * <span class="hljs-number">1024</span>)
    <span class="hljs-keyword">val</span> itr = blockFetcherItr.flatMap(unpackBlock)

    <span class="hljs-keyword">val</span> completionIter = CompletionIterator[T, Iterator[T]](itr, {
      context.taskMetrics.updateShuffleReadMetrics()
    })

    <span class="hljs-keyword">new</span> InterruptibleIterator[T](context, completionIter) {
      <span class="hljs-keyword">val</span> readMetrics = context.taskMetrics.createShuffleReadMetricsForDependency()
      <span class="hljs-keyword">override</span> <span class="hljs-keyword">def</span> next(): T = {
        readMetrics.incRecordsRead(<span class="hljs-number">1</span>)
        delegate.next()
      }
    }
  }</code></pre>

<p>这个方法中主要会去连接Driver去MapOutputTracker中去获取数据信息，然后进行拉取。具体源代码读者可以自行去查看，本篇文章就介绍完毕，如有任何问题，欢迎指教。</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>