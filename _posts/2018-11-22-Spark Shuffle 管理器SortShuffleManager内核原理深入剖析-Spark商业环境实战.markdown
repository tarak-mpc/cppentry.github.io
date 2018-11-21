---
layout:     post
title:      Spark Shuffle 管理器SortShuffleManager内核原理深入剖析-Spark商业环境实战
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本套技术专栏是作者（秦凯新）平时工作的总结和升华，通过从真实商业环境抽取案例进行总结和分享，并给出商业应用的调优建议和集群环境容量规划等内容，请持续关注本套博客。版权声明：禁止转载，欢迎学习。QQ邮箱地址：1120746959@qq.com，如有任何商业交流，可随时联系。					https://blog.csdn.net/shenshouniu/article/details/83870220				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<ul>
<li><a href="https://juejin.im/post/5bd55b4de51d4579470247eb" rel="nofollow">Spark商业环境实战-Spark内置框架rpc通讯机制及RpcEnv基础设施 </a></li>
<li><a href="https://juejin.im/post/5bd583f0e51d452c296ef5dd" rel="nofollow">Spark商业环境实战-Spark事件监听总线流程分析</a></li>
<li><a href="https://juejin.im/post/5bd854955188255ca65da7ed" rel="nofollow">Spark商业环境实战-Spark存储体系底层架构剖析</a></li>
<li><a href="https://juejin.im/post/5bd8553af265da0adb311f58" rel="nofollow">Spark商业环境实战-Spark底层多个MessageLoop循环线程执行流程分析</a></li>
<li><a href="https://juejin.im/post/5bd88932f265da0ae5056199" rel="nofollow">Spark商业环境实战-Spark二级调度系统Stage划分算法和最佳任务调度细节剖析</a></li>
<li><a href="https://juejin.im/post/5bdc2676f265da611b57cd20" rel="nofollow">Spark商业环境实战-Spark任务延迟调度及调度池Pool架构剖析</a></li>
<li><a href="https://juejin.im/post/5bdd0a87f265da612859937d" rel="nofollow">Spark商业环境实战-Task粒度的缓存聚合排序结构AppendOnlyMap详细剖析</a></li>
<li><a href="https://juejin.im/post/5bdd3ac86fb9a049d7471f52" rel="nofollow">Spark商业环境实战-ExternalSorter 外部排序器在Spark Shuffle过程中设计思路剖析</a></li>
<li><a href="https://juejin.im/post/5be02cbfe51d4505551f961e" rel="nofollow">Spark商业环境实战-ShuffleExternalSorter外部排序器在Spark Shuffle过程中的设计思路剖析</a></li>
<li><a href="https://juejin.im/post/5be0329d6fb9a049b3476f32" rel="nofollow">Spark商业环境实战-Spark ShuffleManager内存缓冲器SortShuffleWriter设计思路剖析</a></li>
<li><a href="https://juejin.im/post/5be07638f265da61764a58df" rel="nofollow">Spark商业环境实战-Spark ShuffleManager内存缓冲器UnsafeShuffleWriter设计思路剖析</a></li>
<li><a href="https://juejin.im/post/5be06561e51d4505466cdfa2" rel="nofollow">Spark商业环境实战-Spark ShuffleManager内存缓冲器BypassMergeSortShuffleWriter设计思路剖析</a></li>
<li><a href="https://juejin.im/post/5be446e86fb9a049df238efa" rel="nofollow">Spark商业环境实战-Spark Shuffle 核心组件BlockStoreShuffleReader内核原理深入剖析</a></li>
<li><a href="https://juejin.im/post/5be429c751882516ba2913d3" rel="nofollow">Spark商业环境实战-Spark Shuffle 管理器SortShuffleManager内核原理深入剖析</a></li>
<li><a href="https://juejin.im/post/5bdb091ff265da392b2c51bf" rel="nofollow">Spark商业环境实战-StreamingContext启动流程及Dtream 模板源码剖析</a></li>
<li><a href="https://juejin.im/post/5bdb0e76e51d456c6f1cc45f" rel="nofollow">Spark商业环境实战-ReceiverTracker与BlockGenerator数据流接收过程剖析</a></li>
</ul>
<h2><a id="1_ShuffeManager_17"></a>1 从ShuffeManager讲起</h2>
<p>一张图我已经用过多次了，不要见怪，因为毕竟都是一个主题，有关shuffle的。英文注释已经很详细了，这里简单介绍一下：</p>
<ul>
<li>目前只有一个实现 SortShuffleManager。</li>
<li>SortShuffleManager依赖于ShuffleWriter提供服务，通过ShuffleWriter定义的规范，可以将MapTask的任务中间结果按照约束的规范持久化到磁盘。</li>
<li>SortShuffleManager总共有三个子类， UnsafeShuffleWriter，SortShuffleWriter ，BypassMergeSortShuffleWriter用于Shuffle的写。</li>
<li>SortShuffleManager使用BlockStoreShuffleReader用于Shuffle的读（请详细参照BlockStoreShuffleReader的博客内容）。</li>
<li>SortShuffleManager依赖于ShuffleHandle样例类，主要还是负责向Task传递Shuffle信息。一个是序列化，一个是确定何时绕开合并和排序的Shuffle路径。</li>
</ul>
<p>官方英文介绍如下：</p>
<pre><code>     * Pluggable interface for shuffle systems. A ShuffleManager is created in SparkEnv on the 
     * driver and on each executor, based on the spark.shuffle.manager setting. The driver 
     * registers shuffles with it, and executors (or tasks running locally in the driver) can ask * to read and write data.
     
     * NOTE: this will be instantiated by SparkEnv so its constructor can take a SparkConf and
     * boolean isDriver as parameters.
</code></pre>
<p><img src="https://user-gold-cdn.xitu.io/2018/11/5/166e3aebc8a3878c?w=1144&amp;h=562&amp;f=png&amp;s=123751" alt=""></p>
<h2><a id="2_SortShuffleManager_38"></a>2 SortShuffleManager的进阶</h2>
<ul>
<li>
<p>管理基于排序的shuffle(也即输入的记录按照目标分区ID排序数据，这些记录最终会输出一份正式的单独文件到磁盘，一个是写，一个是读)</p>
</li>
<li>
<p>写的话，举例如：通过SortShuffleWriter，利用其Write()函数，把MapTask的数据经过缓冲区，聚合排序后，写入磁盘。中间过程如溢出，Merge等操作，最终落盘。</p>
</li>
<li>
<p>读的话，举例如：通过BlockStoreShuffleReader，利用其read()方法，利用ShuffleBlockFetcherIterator来实现数据的迭代读取，通过缓冲区，聚合，排序到内存中，部分会溢出到磁盘。</p>
</li>
<li>
<p>英文解释，非常精准：</p>
<pre><code>   * In sort-based shuffle, incoming records are sorted according to their target partition ids, then
   * written to a single map output file. Reducers fetch contiguous regions of this file in order to
   * read their portion of the map output. In cases where the map output data is too large to fit in
   * memory, sorted subsets of the output can are spilled to disk and those on-disk files are merged
   * to produce the final output file.
</code></pre>
</li>
<li>
<p>numMapsForShuffle ：成员变量，shuffle ID 与map任务的数量之间的映射关系。</p>
</li>
<li>
<p>shuffleBlockResolver ：IndexShuffleBlockResolver</p>
<pre><code>   * Create and maintain the shuffle blocks' mapping between logic block and physical file location.
   * Data of shuffle blocks from the same map task are stored in a single consolidated data file.
   * The offsets of the data blocks in the data file are stored in a separate index file.
   *
   * We use the name of the shuffle data's shuffleBlockId with reduce ID set to 0 and add ".data"
   * as the filename postfix for data file, and ".index" as the filename postfix for index file.
</code></pre>
</li>
</ul>
<h2><a id="2_SortShuffleManager_59"></a>2 SortShuffleManager的主干方法</h2>
<h3><a id="21_registerShuffle_61"></a>2.1 registerShuffle</h3>
<pre><code> override def registerShuffle[K, V, C](
      shuffleId: Int,
      numMaps: Int,
      dependency: ShuffleDependency[K, V, C]): ShuffleHandle = {
    if (SortShuffleWriter.shouldBypassMergeSort(conf, dependency)) {
      // If there are fewer than spark.shuffle.sort.bypassMergeThreshold partitions and we don't
      // need map-side aggregation, then write numPartitions files directly and just concatenate
      // them at the end. This avoids doing serialization and deserialization twice to merge
      // together the spilled files, which would happen with the normal code path. The downside is
      // having multiple files open at a time and thus more memory allocated to buffers.
      new BypassMergeSortShuffleHandle[K, V](
        shuffleId, numMaps, dependency.asInstanceOf[ShuffleDependency[K, V, V]])
    } else if (SortShuffleManager.canUseSerializedShuffle(dependency)) {
      // Otherwise, try to buffer map outputs in a serialized form, since this is more efficient:
      new SerializedShuffleHandle[K, V](
        shuffleId, numMaps, dependency.asInstanceOf[ShuffleDependency[K, V, V]])
    } else {
      // Otherwise, buffer map outputs in a deserialized form:
      new BaseShuffleHandle(shuffleId, numMaps, dependency)
    }
  }
</code></pre>
<h3><a id="22_getReader_85"></a>2.2 getReader</h3>
<p>根据map任务的输出的分区数据文件中从startPartition to endPartition-1范围内的数据进行读取的读取器（BlockStoreShuffleReader）</p>
<pre><code>  override def getReader[K, C](
      handle: ShuffleHandle,
      startPartition: Int,
      endPartition: Int,
      context: TaskContext): ShuffleReader[K, C] = {
    new BlockStoreShuffleReader(
      handle.asInstanceOf[BaseShuffleHandle[K, _, C]], startPartition, endPartition, context)
  }
</code></pre>
<h3><a id="22_getWriter_97"></a>2.2 getWriter</h3>
<p>用于根据ShuffleHandle获取ShuffleWriter。</p>
<pre><code>      override def getWriter[K, V](
          handle: ShuffleHandle,
          mapId: Int,
          context: TaskContext): ShuffleWriter[K, V] = {
        numMapsForShuffle.putIfAbsent(
          handle.shuffleId, handle.asInstanceOf[BaseShuffleHandle[_, _, _]].numMaps)
        val env = SparkEnv.get
        handle match {
          case unsafeShuffleHandle: SerializedShuffleHandle[K @unchecked, V @unchecked] =&gt;
          
            new UnsafeShuffleWriter(
              env.blockManager,
              shuffleBlockResolver.asInstanceOf[IndexShuffleBlockResolver],
              context.taskMemoryManager(),
              unsafeShuffleHandle,
              mapId,
              context,
              env.conf)                                                          &lt;=点睛之笔
          case bypassMergeSortHandle: BypassMergeSortShuffleHandle[K @unchecked, V @unchecked] =&gt;
          
            new BypassMergeSortShuffleWriter(
              env.blockManager,
              shuffleBlockResolver.asInstanceOf[IndexShuffleBlockResolver],
              bypassMergeSortHandle,
              mapId,
              context,
              env.conf)                                                          &lt;=点睛之笔
          case other: BaseShuffleHandle[K @unchecked, V @unchecked, _] =&gt;
          
            new SortShuffleWriter(shuffleBlockResolver, other, mapId, context)    &lt;=点睛之笔
        }
      }
</code></pre>
<h2><a id="3_SortShuffleManager_133"></a>3 SortShuffleManager总结</h2>
<ul>
<li>注册Shuffle</li>
<li>获取SHuffle的ShuffleWriter</li>
<li>获取SHuffle的ShuffleRead</li>
</ul>
<p>秦凯新 于深圳</p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>