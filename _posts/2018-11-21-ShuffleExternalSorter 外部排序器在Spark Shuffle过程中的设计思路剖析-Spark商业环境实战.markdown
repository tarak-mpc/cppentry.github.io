---
layout:     post
title:      ShuffleExternalSorter 外部排序器在Spark Shuffle过程中的设计思路剖析-Spark商业环境实战
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本套技术专栏是作者（秦凯新）平时工作的总结和升华，通过从真实商业环境抽取案例进行总结和分享，并给出商业应用的调优建议和集群环境容量规划等内容，请持续关注本套博客。版权声明：禁止转载，欢迎学习。QQ邮箱地址：1120746959@qq.com，如有任何商业交流，可随时联系。					https://blog.csdn.net/shenshouniu/article/details/83756862				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h3><a id="Spark_0"></a>Spark商业环境实战及调优进阶系列</h3>
<ul>
<li><a href="https://juejin.im/post/5bd55b4de51d4579470247eb" rel="nofollow">Spark商业环境实战-Spark内置框架rpc通讯机制及RpcEnv基础设施 </a></li>
<li><a href="https://juejin.im/post/5bd583f0e51d452c296ef5dd" rel="nofollow">Spark商业环境实战-Spark事件监听总线流程分析</a></li>
<li><a href="https://juejin.im/post/5bd854955188255ca65da7ed" rel="nofollow">Spark商业环境实战-Spark存储体系底层架构剖析</a></li>
<li><a href="https://juejin.im/post/5bd8553af265da0adb311f58" rel="nofollow">Spark商业环境实战-Spark底层多个MessageLoop循环线程执行流程分析</a></li>
<li><a href="https://juejin.im/post/5bd88932f265da0ae5056199" rel="nofollow">Spark商业环境实战-Spark二级调度系统Stage划分算法和最佳任务调度细节剖析</a></li>
<li><a href="https://juejin.im/post/5bdc2676f265da611b57cd20" rel="nofollow">Spark商业环境实战-Spark任务延迟调度及调度池Pool架构剖析</a></li>
<li><a href="https://juejin.im/post/5bdd0a87f265da612859937d" rel="nofollow">Spark商业环境实战-Task粒度的缓存聚合排序结构AppendOnlyMap详细剖析</a></li>
<li><a href="https://juejin.im/post/5bdd3ac86fb9a049d7471f52" rel="nofollow">Spark商业环境实战-ExternalSorter 外部排序器在Spark Shuffle过程中设计思路剖析</a></li>
<li><a href="https://juejin.im/post/5be02cbfe51d4505551f961e" rel="nofollow">Spark商业环境实战-ShuffleExternalSorter 外部排序器在Shuffle过程中的设计思路剖析</a></li>
<li>[Spark商业环境实战-ShuffeManager之统一存储服务SortShuffleWriter设计思路剖析]</li>
<li>[Spark商业环境实战-ShuffeManager之统一存储服务UnsafeShuffleWriter设计思路剖析]</li>
<li>[Spark商业环境实战-ShuffeManager之统一存储服务BypassMergeSortShuffleWriter设计思路剖析]</li>
<li>[Spark商业环境实战-Shuffle Reader 在数据拉取执行过程中设计思路剖析]</li>
<li><a href="https://juejin.im/post/5bdb091ff265da392b2c51bf" rel="nofollow">Spark商业环境实战-StreamingContext启动流程及Dtream 模板源码剖析</a></li>
<li><a href="https://juejin.im/post/5bdb0e76e51d456c6f1cc45f" rel="nofollow">Spark商业环境实战-ReceiverTracker与BlockGenerator数据流接收过程剖析</a></li>
</ul>
<h2><a id="1_ShuffleExternalSorter__17"></a>1 ShuffleExternalSorter 外部排序器</h2>
<h3><a id="11_ShuffleExternalSorter__18"></a>1.1 ShuffleExternalSorter 外部排序器江湖地位</h3>
<p>ShuffleExternalSorter和 ExternalSorter 外部排序器功能类似，但是也有不同的地方。不过在详细剖析ShuffleExternalSorter之前，我们先看看ShuffleExternalSorter在下图中所处的位置。可以看到最终的调用方是unsafeShuffleWriter。在下一节，我会详细剖析UnsafeShuffleWriter。</p>
<p><img src="https://user-gold-cdn.xitu.io/2018/11/5/166e3d4c71910481?w=1121&amp;h=563&amp;f=png&amp;s=126646" alt=""></p>
<h3><a id="12_ShuffleExternalSorter__24"></a>1.2 ShuffleExternalSorter 外部排序器与众不同的特色</h3>
<h4><a id="_26"></a>相同点：</h4>
<ul>
<li>ShuffleExternalSorter与ExternalSorter都是将记录插入到内存中。</li>
</ul>
<h4><a id="_28"></a>不同点：</h4>
<ul>
<li>ExternalSorter除了将数据存入内存中，还会进行聚合操作，ShuffleExternalSorter没有聚合功能。</li>
<li>ShuffleExternalSorter使用的是Tungsten缓存(因此可能是JVM堆缓存，也可能是操作系统的内存)</li>
<li>溢出前排序操作：ExternalSorter是按照分区ID和key进行排序实现，ShuffleExternalSorter除了按照分区ID的排序外，也有基于基数排序（Radix Sort）的实现。</li>
<li>ShuffleExternalSorter没有了applendOnlyMapz这种数据结构。</li>
</ul>
<h3><a id="12_ShuffleExternalSorter__34"></a>1.2 ShuffleExternalSorter 主要成员</h3>
<ul>
<li>
<p>ShuffleInMemorySorter :用于在内存中对插入的记录进行排序，算法还是TimSort。</p>
</li>
<li>
<p>spills :溢出文件的元数据信息列表。</p>
</li>
<li>
<p>numElementsForSpillThreshold ：磁盘溢出的元素数量。可以通过spark.shuffle.spill.numElementsForceSpillThreshold属性来进行配置，默认是1M</p>
</li>
<li>
<p>taskMemoryManager：</p>
</li>
<li>
<p>allocatedPages：已经分配的Page列表（即MemoryBlock）列表</p>
<pre><code> * Memory pages that hold the records being sorted. The pages in this list are freed when
 * spilling, although in principle we could recycle these pages across spills (on the other hand,
 * this might not be necessary if we maintained a pool of re-usable pages in the TaskMemoryManager
 * itself)。
</code></pre>
</li>
</ul>
<h3><a id="13_ShuffleExternalSorter_insertRecord__45"></a>1.3 ShuffleExternalSorter insertRecord 代码欣赏</h3>
<ul>
<li>
<p>数据溢出，通过inMemSorter.numRecords() &gt;= numElementsForSpillThreshold来判断，若满足直接溢出操作。</p>
</li>
<li>
<p>growPointerArrayIfNecessary：进行空间检查和数据容量扩容。</p>
</li>
<li>
<p>acquireNewPageIfNecessary：进行空间检查，若不满足申请新page。</p>
</li>
<li>
<p>Platform.copyMemory：将数据拷贝到Page所代表的的内存块中。</p>
</li>
<li>
<p>inMemSorter.insertRecord：将记录的元数据存到内部的长整型数组中，便于排序。其中高24位是存储分区ID，中间13位为存储页号，低27位存储偏移量。</p>
<pre><code>  Write a record to the shuffle sorter.
  
  public void insertRecord(Object recordBase, long recordOffset, int length, int partitionId)
      throws IOException {
      // for tests
      assert(inMemSorter != null);
      
      if (inMemSorter.numRecords() &gt;= numElementsForSpillThreshold) {    &lt;= 神来之笔
      
        logger.info("Spilling data because number of spilledRecords crossed the threshold " +
          numElementsForSpillThreshold);
        spill();
      }
  
      growPointerArrayIfNecessary();                                  &lt;= 神来之笔
      // Need 4 bytes to store the record length.
      final int required = length + 4;
      
      acquireNewPageIfNecessary(required);
  
      assert(currentPage != null);
      final Object base = currentPage.getBaseObject();
      final long recordAddress = taskMemoryManager.encodePageNumberAndOffset(currentPage, pageCursor);
      Platform.putInt(base, pageCursor, length);
      pageCursor += 4;
      Platform.copyMemory(recordBase, recordOffset, base, pageCursor, length);  &lt;= 神来之笔
      pageCursor += length;
      inMemSorter.insertRecord(recordAddress, partitionId);      &lt;= 神来之笔，排序后写入内存。
    }
</code></pre>
</li>
</ul>
<h3><a id="13_ShuffleExternalSorter_spill__82"></a>1.3 ShuffleExternalSorter spill 代码欣赏</h3>
<ul>
<li>
<p>writeSortedFile:作用在于将内存中的记录排序后输出到磁盘中，排序规则有两种：<br>
一种：对分区ID进行排序。二种是采用基数排序（Radix Sort）</p>
<pre><code>   public long spill(long size, MemoryConsumer trigger) throws IOException {
      if (trigger != this || inMemSorter == null || inMemSorter.numRecords() == 0) {
        return 0L;
      }
      logger.info("Thread {} spilling sort data of {} to disk ({} {} so far)",
        Thread.currentThread().getId(),
        Utils.bytesToString(getMemoryUsage()),
        spills.size(),
        spills.size() &gt; 1 ? " times" : " time");
  
      writeSortedFile(false);                              &lt;= 神来之笔
      final long spillSize = freeMemory();
      inMemSorter.reset();
      
      // Reset the in-memory sorter's pointer array only after freeing up the memory pages
      holding the records. Otherwise, if the task is over allocated memory, then without freeing the memory pages, we might not be able to get memory for the pointer array.
      
      taskContext.taskMetrics().incMemoryBytesSpilled(spillSize);
      return spillSize;
    }
</code></pre>
</li>
</ul>
<h2><a id="2__107"></a>2 最后</h2>
<p>本篇需要挖掘的点还有很多，鉴于可参考的资料太少，只能暂时到此结束，后续会继续完善</p>
<p>秦凯新  于深圳</p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>