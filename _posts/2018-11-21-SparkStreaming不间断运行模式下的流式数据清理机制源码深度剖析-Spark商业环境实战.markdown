---
layout:     post
title:      SparkStreaming不间断运行模式下的流式数据清理机制源码深度剖析-Spark商业环境实战
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本套技术专栏是作者（秦凯新）平时工作的总结和升华，通过从真实商业环境抽取案例进行总结和分享，并给出商业应用的调优建议和集群环境容量规划等内容，请持续关注本套博客。版权声明：禁止转载，欢迎学习。QQ邮箱地址：1120746959@qq.com，如有任何商业交流，可随时联系。					https://blog.csdn.net/shenshouniu/article/details/84001740				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>本套系列博客从真实商业环境抽取案例进行总结和分享，并给出Spark源码解读及商业实战指导，请持续关注本套博客。版权声明：本套Spark源码解读及商业实战归作者（秦凯新）所有，禁止转载，欢迎学习。</p>
<ul>
<li><a href="https://juejin.im/post/5bd55b4de51d4579470247eb" rel="nofollow">Spark商业环境实战-Spark内置框架rpc通讯机制及RpcEnv基础设施 </a></li>
<li><a href="https://juejin.im/post/5bd583f0e51d452c296ef5dd" rel="nofollow">Spark商业环境实战-Spark事件监听总线流程分析</a></li>
<li><a href="https://juejin.im/post/5bd854955188255ca65da7ed" rel="nofollow">Spark商业环境实战-Spark存储体系底层架构剖析</a></li>
<li><a href="https://juejin.im/post/5bd8553af265da0adb311f58" rel="nofollow">Spark商业环境实战-Spark底层多个MessageLoop循环线程执行流程分析</a></li>
<li><a href="https://blog.csdn.net/shenshouniu/article/details/83962930" rel="nofollow">Spark商业环境实战-Spark一级资源调度Shedule机制及SpreadOut模式源码深入剖析</a></li>
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
<li><a href="https://juejin.im/post/5be96a2fe51d454e0a1cc3a2" rel="nofollow">Spark商业环境实战-Spark PersistenceEngine持久化引擎与领导选举代理机制内核原理深入剖析</a></li>
<li>[Spark商业应用实战-Spark Shuffle 专业级核心参数调优源码深入剖析 ]</li>
<li>[Spark商业应用实战-Spark 内存管理体系UnifiedMemoryManager源码深入剖析 ]</li>
<li>[Spark商业应用实战-Spark 内存管理体系StaticMemoryManager源码深入剖析 ]</li>
<li>[Spark商业应用实战-Spark 基于JVM 统一内存使用内幕源码深入剖析 ]</li>
<li>[Spark商业应用实战-Spark 基于Tungsten内存分配器的管理机制内幕源码深入剖析 ]</li>
<li>[Spark商业应用实战-Spark 基于Task粒度的内存管理器及内存消费者源码深入剖析]</li>
<li>[Spark商业应用实战-Spark Shuffle  Block 索引与数据解析组件IndexShuffleBlocakResolver源码深入剖析 ]</li>
<li>[Spark商业应用实战-Spark Block数据传输服务NettyBlockTransferService 源码深入剖析 ]</li>
<li>[Spark商业应用实战-Spark 基于Yarn的资源申请过程源码深入剖析 ]</li>
<li>[Spark商业应用实战-Spark 基于K8s的资源申请流程源码深入剖析 ]</li>
<li><a href="https://juejin.im/post/5bdb091ff265da392b2c51bf" rel="nofollow">Spark商业环境实战-StreamingContext启动流程及Dtream 模板源码剖析</a></li>
<li><a href="https://juejin.im/post/5bdb0e76e51d456c6f1cc45f" rel="nofollow">Spark商业环境实战-ReceiverTracker 启动过程及接收器 receiver RDD 任务提交机制源码剖析</a></li>
<li><a href="https://juejin.im/post/5be677946fb9a049ca36db6f" rel="nofollow">Spark商业环境实战-SparkStreaming数据流从Batch到Block定时转化过程源码深度剖析</a></li>
<li><a href="https://juejin.im/post/5be6ac236fb9a049a42e8e37" rel="nofollow">Spark商业环境实战-SparkStreaming之JobGenerator周期性任务数据处理逻辑源码深度剖析</a></li>
<li>[Spark商业环境实战-SparkStreaming Graph 处理链迭代过程源码深度剖析]</li>
<li><a href="https://juejin.im/post/5be9a058e51d457c1c4dfcae" rel="nofollow">Spark商业环境实战-SparkStreaming不间断运行模式下的流式数据清理机制源码深度剖析</a></li>
<li>[Spark商业环境实战-SparkStreaming 容错机制源码深度剖析]</li>
<li>[Spark商业环境实战-SparkStreaming 之No Receiver方式基于Kafka 拉取内幕源码深度剖析]</li>
<li>[Spark商业环境实战-SparkStreaming 反压机制控制消费速率内幕源码深度剖析]</li>
</ul>
<h2><a id="1__38"></a>1 不扫一屋何以扫天下</h2>
<ul>
<li>SparkStreaming 应用在持续不断的运行，假设Spark 数据接入只进不出，那么即使Spark内存使用多么牛逼，也会崩掉的，因此及时进行内存数据的清理和磁盘的清理可谓重中之重。</li>
<li>那么SparkStreaming应用的对象，数据，元数据这些信息如何进行回收呢？先抛个问题。</li>
</ul>
<h2><a id="2__43"></a>2 何时扫天下？</h2>
<h3><a id="21_jobSchedulerjob_44"></a>2.1 jobScheduler之job的提交到结束</h3>
<ul>
<li>JobGenerator触发generateJobs</li>
<li>JobGenerator -&gt;  jobScheduler.submitJobSet(JobSet(time, jobs, streamIdToInputInfos))</li>
<li>submitJobSet -&gt; jobSet.jobs.foreach(job =&gt; jobExecutor.execute(new JobHandler(job)))</li>
<li>jobScheduler -&gt; _eventLoop.post(JobCompleted(job, clock.getTimeMillis()))</li>
<li>jobScheduler -&gt; handleJobCompletion(job, completedTime)</li>
<li>jobScheduler -&gt; jobGenerator.onBatchCompletion(jobSet.time)</li>
<li>jobGenerator -&gt; eventLoop.post(ClearMetadata(time))</li>
</ul>
<h3><a id="22_clearMetadata__54"></a>2.2 clearMetadata 神龙见首不见尾</h3>
<ul>
<li>
<h6><a id="_56"></a>主要的缓存元数据</h6>
<pre><code>private val batchTimeToInputInfos =
  new mutable.HashMap[Time, mutable.HashMap[Int, StreamInputInfo]]
  
 private val timeToAllocatedBlocks = new mutable.HashMap[Time, AllocatedBlocks]    
</code></pre>
</li>
<li>
<p>ssc.graph.clearMetadata : 基于outputStreams 清除 RDD，通过BlockManager清除Block数据</p>
</li>
<li>
<p>jobScheduler.inputInfoTracker.cleanup : 基于inputInfoTracker清除缓存中的timeToAllocatedBlocks。</p>
</li>
<li>
<p>jobScheduler.inputInfoTracker.cleanup : 基于inputInfoTracker清除batchTimeToInputInfos中元数据</p>
<pre><code>private def clearMetadata(time: Time) {

    ssc.graph.clearMetadata(time)               &lt;- 核心之处

    // If checkpointing is enabled, then checkpoint,
    // else mark batch to be fully processed
    if (shouldCheckpoint) {
      eventLoop.post(DoCheckpoint(time, clearCheckpointDataLater = true))  &lt;- 核心之处

    } else {
      // If checkpointing is not enabled, then delete metadata information about
      // received blocks (block data not saved in any case). Otherwise, wait for
      // checkpointing of this batch to complete.
      val maxRememberDuration = graph.getMaxInputStreamRememberDuration()

      jobScheduler.receiverTracker.cleanupOldBlocksAndBatches(time - maxRememberDuration)  &lt;- 核心之处
      jobScheduler.inputInfoTracker.cleanup(time - maxRememberDuration)    &lt;- 核心之处
      markBatchFullyProcessed(time)
    }
  } 
</code></pre>
</li>
</ul>
<h3><a id="23_sscgraphclearMetadata_RDD_90"></a>2.3 ssc.graph.clearMetadata 之RDD再见</h3>
<pre><code>   * Clear metadata that are older than `rememberDuration` of this DStream.
   * This is an internal method that should not be called directly. This default
   * implementation clears the old generated RDDs. Subclasses of DStream may override
   * this to clear their own metadata along with the generated RDDs.
   
  private[streaming] def clearMetadata(time: Time) {
    val unpersistData = ssc.conf.getBoolean("spark.streaming.unpersist", true)
    val oldRDDs = generatedRDDs.filter(_._1 &lt;= (time - rememberDuration))
    logDebug("Clearing references to old RDDs: [" +
      oldRDDs.map(x =&gt; s"${x._1} -&gt; ${x._2.id}").mkString(", ") + "]")
      
    generatedRDDs --= oldRDDs.keys        &lt;- 核心之处
    
    if (unpersistData) {
      logDebug(s"Unpersisting old RDDs: ${oldRDDs.values.map(_.id).mkString(", ")}")
      oldRDDs.values.foreach { rdd =&gt;
      
        rdd.unpersist(false)               &lt;- 核心之处
        
        // Explicitly remove blocks of BlockRDD
        rdd match {
          case b: BlockRDD[_] =&gt;
            logInfo(s"Removing blocks of RDD $b of time $time")
            b.removeBlocks()
          case _ =&gt;
        }
      }
    }
</code></pre>
<h5><a id="RDD_121"></a>RDD清除细节</h5>
<pre><code> private[spark] def unpersistRDD(rddId: Int, blocking: Boolean = true) {
    env.blockManager.master.removeRdd(rddId, blocking)
    persistentRdds.remove(rddId)
    listenerBus.post(SparkListenerUnpersistRDD(rddId))
  }
</code></pre>
<h3><a id="24__cleanupOldBlocksAndBatches__batch_129"></a>2.4  cleanupOldBlocksAndBatches 之 batch数据再见</h3>
<pre><code>   * Clean up block information of old batches. If waitForCompletion is true, this method
   * returns only after the files are cleaned up.
   
  def cleanupOldBatches(cleanupThreshTime: Time, waitForCompletion: Boolean): Unit = synchronized {
    require(cleanupThreshTime.milliseconds &lt; clock.getTimeMillis())
    
    val timesToCleanup = timeToAllocatedBlocks.keys.filter { _ &lt; cleanupThreshTime }.toSeq
    logInfo(s"Deleting batches: ${timesToCleanup.mkString(" ")}")
    
    if (writeToLog(BatchCleanupEvent(timesToCleanup))) {
      timeToAllocatedBlocks --= timesToCleanup
      writeAheadLogOption.foreach(_.clean(cleanupThreshTime.milliseconds, waitForCompletion))
    } else {
      logWarning("Failed to acknowledge batch clean up in the Write Ahead Log.")
    }
  }
</code></pre>
<h3><a id="25__cleanupOldBlocksAndBatches__batch_info__150"></a>2.5  cleanupOldBlocksAndBatches 之 batch info 元数据再见</h3>
<pre><code>  def cleanup(batchThreshTime: Time): Unit = synchronized {
    val timesToCleanup = batchTimeToInputInfos.keys.filter(_ &lt; batchThreshTime)
    logInfo(s"remove old batch metadata: ${timesToCleanup.mkString(" ")}")
    batchTimeToInputInfos --= timesToCleanup
  }
</code></pre>
<h3><a id="3__158"></a>3 总结</h3>
<p>不扫一屋何以扫天下  终章</p>
<p>秦凯新 于深圳 1:13 2018</p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>