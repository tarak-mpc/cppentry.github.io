---
layout:     post
title:      Spark ShuffleManager内存缓冲器UnsafeShuffleWriter设计思路剖析-Spark商业环境实战
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本套技术专栏是作者（秦凯新）平时工作的总结和升华，通过从真实商业环境抽取案例进行总结和分享，并给出商业应用的调优建议和集群环境容量规划等内容，请持续关注本套博客。版权声明：禁止转载，欢迎学习。QQ邮箱地址：1120746959@qq.com，如有任何商业交流，可随时联系。					https://blog.csdn.net/shenshouniu/article/details/83759588				</div>
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
<li>[Spark商业环境实战-Spark Shuffle 聚合拉取读数据（Reduce Task）过程深入剖析]</li>
<li><a href="https://juejin.im/post/5bdb091ff265da392b2c51bf" rel="nofollow">Spark商业环境实战-StreamingContext启动流程及Dtream 模板源码剖析</a></li>
<li><a href="https://juejin.im/post/5bdb0e76e51d456c6f1cc45f" rel="nofollow">Spark商业环境实战-ReceiverTracker与BlockGenerator数据流接收过程剖析</a></li>
</ul>
<h2><a id="1_ShuffeManager_16"></a>1 从ShuffeManager讲起</h2>
<p>一张图我已经用过多次了，不要见怪，因为毕竟都是一个主题，有关shuffle的。英文注释已经很详细了，这里简单介绍一下：</p>
<ul>
<li>目前只有一个实现 SortShuffleManager。</li>
<li>SortShuffleManager依赖于ShuffleWriter提供服务，通过ShuffleWriter定义的规范，可以将MapTask的任务中间结果按照约束的规范持久化到磁盘。</li>
<li>SortShuffleManager总共有三个子类， UnsafeShuffleWriter，SortShuffleWriter ，BypassMergeSortShuffleWriter。</li>
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
<h2><a id="2_UnsafeShuffleWriter_36"></a>2 UnsafeShuffleWriter的骨干成员</h2>
<p>static final int DEFAULT_INITIAL_SORT_BUFFER_SIZE = 4096;<br>
static final int DEFAULT_INITIAL_SER_BUFFER_SIZE = 1024 * 1024;</p>
<ul>
<li>
<p>private final BlockManager blockManager;</p>
</li>
<li>
<p>private final IndexShuffleBlockResolver shuffleBlockResolver;</p>
</li>
<li>
<p>private final TaskMemoryManager memoryManager;</p>
</li>
<li>
<p>private final SerializerInstance serializer;</p>
</li>
<li>
<p>private final Partitioner partitioner;</p>
</li>
<li>
<p>private final ShuffleWriteMetrics writeMetrics;</p>
</li>
<li>
<p>private final int shuffleId;</p>
</li>
<li>
<p>private final int mapId;</p>
</li>
<li>
<p>private final TaskContext taskContext;</p>
</li>
<li>
<p>private final SparkConf sparkConf;</p>
</li>
<li>
<p>private final boolean transferToEnabled =&gt; 是否采用NIO的从文件流待文件流的复制方式，spark.file.transferTo属性配置，默认是true。</p>
</li>
<li>
<p>private final int initialSortBufferSize =&gt;初始化的排序缓冲大小,可以通过spark.shuffle.sort.initialBuffer.size属性设置，默认是4096</p>
</li>
<li>
<p>private final int inputBufferSizeInBytes;</p>
</li>
<li>
<li>
<p>private final int outputBufferSizeInBytes;</p>
</li>
<li>
<p>@Nullable private MapStatus mapStatus;</p>
</li>
<li>
<p>@Nullable private ShuffleExternalSorter sorter;</p>
</li>
<li>
<p>private long peakMemoryUsedBytes = 0; =&gt;使用内存的峰值</p>
</li>
</ul>
<h2><a id="3_UnsafeShuffleWriterWriter_64"></a>3 UnsafeShuffleWriter核心实现方法Writer</h2>
<p>看看精彩的代码段：</p>
<pre><code>     public void write(scala.collection.Iterator&lt;Product2&lt;K, V&gt;&gt; records) throws IOException {
        // Keep track of success so we know if we encountered an exception
        // We do this rather than a standard try/catch/re-throw to handle
        // generic throwables.
        boolean success = false;
        try {
          while (records.hasNext()) {
          
            insertRecordIntoSorter(records.next());          &lt;=点睛之笔（将mapTask数据写入排序器）
            
          }
          
          
          closeAndWriteOutput();          &lt;=点睛之笔（将mapTask数据持久化到磁盘）
          
          
          success = true;
        } finally {
          if (sorter != null) {
            try {
              sorter.cleanupResources();
            } catch (Exception e) {
              // Only throw this error if we won't be masking another
              // error.
              if (success) {
                throw e;
              } else {
                logger.error("In addition to a failure during writing, we failed during " +
                             "cleanup.", e);
              }
            }
          }
        }
      }
</code></pre>
<h2><a id="4_UnsafeShuffleWriterinsertRecordIntoSorter_104"></a>4 UnsafeShuffleWriter核心实现方法insertRecordIntoSorter</h2>
<p>将mapTask数据写入排序器，实现内存中排序，但是无聚合</p>
<pre><code>void insertRecordIntoSorter(Product2&lt;K, V&gt; record) throws IOException {
    assert(sorter != null);
    final K key = record._1();
    
    final int partitionId = partitioner.getPartition(key);   &lt;=点睛之笔
    
    serBuffer.reset();
    serOutputStream.writeKey(key, OBJECT_CLASS_TAG);
    serOutputStream.writeValue(record._2(), OBJECT_CLASS_TAG);
    serOutputStream.flush();

    final int serializedRecordSize = serBuffer.size();
    assert (serializedRecordSize &gt; 0);

    sorter.insertRecord(                              &lt;=点睛之笔，将serBuffer字节数组写入Tungsten
      serBuffer.getBuf(), Platform.BYTE_ARRAY_OFFSET, serializedRecordSize, partitionId);
  }
</code></pre>
<h2><a id="5_UnsafeShuffleWritercloseAndWriteOutput_126"></a>5 UnsafeShuffleWriter核心实现方法closeAndWriteOutput</h2>
<p>将mapTask数据持久化到磁盘</p>
<pre><code>void closeAndWriteOutput() throws IOException {
    assert(sorter != null);
    updatePeakMemoryUsed();
    serBuffer = null;
    serOutputStream = null;
    final SpillInfo[] spills = sorter.closeAndGetSpills();
    sorter = null;
    final long[] partitionLengths;
    final File output = shuffleBlockResolver.getDataFile(shuffleId, mapId);
    final File tmp = Utils.tempFileWith(output);
    try {
      try {
      
        partitionLengths = mergeSpills(spills, tmp);         &lt;=点睛之笔（合并所有溢出文件为正式Block文件）
        
      } finally {
        for (SpillInfo spill : spills) {
          if (spill.file.exists() &amp;&amp; ! spill.file.delete()) {
            logger.error("Error while deleting spill file {}", spill.file.getPath());
          }
        }
      }
      shuffleBlockResolver.writeIndexFileAndCommit(shuffleId, mapId, partitionLengths, tmp);   &lt;=点睛之笔（写索引）
      
    } finally {
      if (tmp.exists() &amp;&amp; !tmp.delete()) {
        logger.error("Error while deleting temp file {}", tmp.getAbsolutePath());
      }
    }
    mapStatus = MapStatus$.MODULE$.apply(blockManager.shuffleServerId(), partitionLengths);
  }
</code></pre>
<h2><a id="5__161"></a>5 总结</h2>
<p>UnsafeShuffleWriter内部主要使用Tungsten缓存，当然也可能使用JVM内存。和ExternalSortWriter有明显的区别。</p>
<p>秦凯新  于深圳  1:19</p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>