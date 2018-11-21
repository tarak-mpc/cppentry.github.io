---
layout:     post
title:      Spark存储体系底层架构剖析-Spark商业环境实战
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本套技术专栏是作者（秦凯新）平时工作的总结和升华，通过从真实商业环境抽取案例进行总结和分享，并给出商业应用的调优建议和集群环境容量规划等内容，请持续关注本套博客。版权声明：禁止转载，欢迎学习。QQ邮箱地址：1120746959@qq.com，如有任何商业交流，可随时联系。					https://blog.csdn.net/shenshouniu/article/details/83552422				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h3><a id="Spark_1"></a>Spark商业环境实战及调优进阶系列</h3>
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
<h2><a id="1_Spark_13"></a>1. Spark存储体系组件关系解释</h2>
<p>BlockInfoManger 主要提供读写锁控制，层级仅仅位于BlockManger之下，通常Spark读写操作都先调用BlockManger，然后咨询BlockInfoManger是否存在锁竞争，然后才会调用DiskStore和MemStore，进而调用DiskBlockManger来确定数据与位置映射，或者调用 MemoryManger来确定内存池的软边界和内存使用申请。</p>
<p><img src="https://user-gold-cdn.xitu.io/2018/10/30/166c5a60cb7efa54?w=615&amp;h=556&amp;f=png&amp;s=62956" alt=""></p>
<h3><a id="11_Driver__Executor__SparkEnv__BlockManger__18"></a>1.1 Driver 与 Executor 与 SparkEnv 与 BlockManger 组件关系：</h3>
<p>Driver与 Executor 组件各自拥有任务执行的SparkEnv环境，而每一个SparkEnv 中都有一个BlockManger负责存储服务，作为高层抽象，BlockManger 之间需要通过 RPCEnv，ShuffleClient，及BlocakTransferService相互通讯。</p>
<h3><a id="11_BlockInfoManger__BlockInfo___20"></a>1.1 BlockInfoManger 与 BlockInfo  共享锁和排它锁读写控制关系：</h3>
<p>BlockInfo中具有读写锁的标志，通过标志可以判断是否进行写控制</p>
<pre><code>  val NO_WRITER: Long = -1
  val NON_TASK_WRITER: Long = -1024
  
 * The task attempt id of the task which currently holds the write lock for this block, or
 * [[BlockInfo.NON_TASK_WRITER]] if the write lock is held by non-task code, or
 * [[BlockInfo.NO_WRITER]] if this block is not locked for writing.
 
 def writerTask: Long = _writerTask
 def writerTask_=(t: Long): Unit = {
 _writerTask = t
    checkInvariants()
</code></pre>
<p>BlockInfoManager具有BlockId与BlockInfo的映射关系以及任务id与BlockId的锁映射：</p>
<pre><code> private[this] val infos = new mutable.HashMap[BlockId, BlockInfo]  
 
 *Tracks the set of blocks that each task has locked for writing.
 private[this] val writeLocksByTask = new mutable.HashMap[TaskAttemptId, mutable.Set[BlockId]]
                                       with mutable.MultiMap[TaskAttemptId, BlockId]
 
 *Tracks the set of blocks that each task has locked for reading, along with the number of times
 *that a block has been locked (since our read locks are re-entrant).
 private[this] val readLocksByTask =
 new mutable.HashMap[TaskAttemptId, ConcurrentHashMultiset[BlockId]]
</code></pre>
<h3><a id="13_DiskBlockManager__DiskStore__51"></a>1.3 DiskBlockManager 与 DiskStore 组件关系：</h3>
<p>可以看到DiskStore内部会调用DiskBlockManager来确定Block的读写位置：</p>
<ul>
<li>
<p>以下是DiskStore的抽象写操作，需要传入FileOutputStream =&gt; Unit高阶函数：</p>
<pre><code>  def put(blockId: BlockId)(writeFunc: FileOutputStream =&gt; Unit): Unit = {
  if (contains(blockId)) {
  throw new IllegalStateException(s"Block $blockId is already present in the disk store")
  }
  logDebug(s"Attempting to put block $blockId")
  val startTime = System.currentTimeMillis
  
  val file = diskManager.getFile(blockId)
  
  val fileOutputStream = new FileOutputStream(file)
  var threwException: Boolean = true
  try {
      writeFunc(fileOutputStream)
      threwException = false
  } finally {
   try {
      Closeables.close(fileOutputStream, threwException)
   } finally {
   if (threwException) {
    remove(blockId)
          }
      }
  }
  val finishTime = System.currentTimeMillis
  logDebug("Block %s stored as %s file on disk in %d ms".format(
  file.getName,
  Utils.bytesToString(file.length()),
  finishTime - startTime))
  }
</code></pre>
</li>
<li>
<p>以下是DiskStore的读操作，调用DiskBlockManager来获取数据位置：</p>
<pre><code>  def getBytes(blockId: BlockId): ChunkedByteBuffer = {
  
  val file = diskManager.getFile(blockId.name)
 
  val channel = new RandomAccessFile(file, "r").getChannel
  Utils.tryWithSafeFinally {
* For small files, directly read rather than memory map
  if (file.length &lt; minMemoryMapBytes) {
  val buf = ByteBuffer.allocate(file.length.toInt)
  channel.position(0)
  while (buf.remaining() != 0) {
    if (channel.read(buf) == -1) {
      throw new IOException("Reached EOF before filling buffer\n" +
        s"offset=0\nfile=${file.getAbsolutePath}\nbuf.remaining=${buf.remaining}")
    }
  }
  buf.flip()
  new ChunkedByteBuffer(buf)
  } else {
  new ChunkedByteBuffer(channel.map(MapMode.READ_ONLY, 0, file.length))
      }
  } {
  channel.close()
   }
  }
</code></pre>
</li>
</ul>
<h3><a id="13_MemManager__MemStore__MemoryPool__114"></a>1.3 MemManager 与 MemStore 与 MemoryPool 组件关系：</h3>
<p>在这里要强调的是：第一代大数据框架hadoop只将内存作为计算资源，而Spark不仅将内存作为计算资源外，还将内存的一部分纳入存储体系：</p>
<ul>
<li>内存池模型 ：逻辑上分为堆内存和堆外内存，然后堆内存（或堆外内存）内部又分为StorageMemoryPool和ExecutionMemoryPool。</li>
<li>MemManager是抽象的，定义了内存管理器的接口规范，方便以后扩展，比如：老版的StaticMemoryManager和新版的UnifiedMemoryManager.</li>
<li>MemStore 依赖于UnifiedMemoryManager进行内存的申请和软边界变化或内存释放。</li>
<li>MemStore 内部同时负责存储真实的对象，比如内部成员变量：entries ，建立了内存中的BlockId与MemoryEntry(Block的内存的形式)之间的映射。</li>
<li>MemStore 内部的“占座”行为，如：内部变量offHeapUnrollMemoryMap 和onHeapUnrollMemoryMap。</li>
</ul>
<h3><a id="14_BlockManagerMaster__BlockManager__123"></a>1.4 BlockManagerMaster 与 BlockManager 组件关系：</h3>
<ul>
<li>BlockManagerMaster的作用就是对存在于Dirver或Executor上的BlockManger进行统一管理，这简直是代理行为，因为他持有BlockManagerMasterEndpointREf，进而和BlockManagerMasterEndpoint进行通讯。</li>
</ul>
<h2><a id="2_SparkBlockTransferServic_126"></a>2. Spark存储体系组件BlockTransferServic传输服务</h2>
<p>未完待续</p>
<h2><a id="3__130"></a>3. 总结</h2>
<p>存储体系是Spark的基石，我争取把每一块细微的知识点进行剖析，和大部分博客不同的是，我会尽量采用最平实的语言，毕竟技术就是一层窗户纸。</p>
<p>秦凯新  20181031 凌晨</p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>