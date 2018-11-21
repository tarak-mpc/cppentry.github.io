---
layout:     post
title:      Spark ShuffleManager内存缓冲器BypassMergeSortShuffleWriter设计思路剖析-Spark商业环境实战
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本套技术专栏是作者（秦凯新）平时工作的总结和升华，通过从真实商业环境抽取案例进行总结和分享，并给出商业应用的调优建议和集群环境容量规划等内容，请持续关注本套博客。版权声明：禁止转载，欢迎学习。QQ邮箱地址：1120746959@qq.com，如有任何商业交流，可随时联系。					https://blog.csdn.net/shenshouniu/article/details/83759503				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>本套系列博客从真实商业环境抽取案例进行总结和分享，并给出Spark源码解读及商业实战指导，请持续关注本套博客。版权声明：本套Spark源码解读及商业实战归作者（秦凯新）所有，禁止转载，欢迎学习。</p>
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
<h2><a id="1_ShuffeManager_18"></a>1 从ShuffeManager讲起</h2>
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
<h2><a id="1_BypassMergeSortShuffleWriter_38"></a>1 华山论剑之BypassMergeSortShuffleWriter</h2>
<p>从命名来看，绝对是投机取巧，绕开合并和排序的ShuffleWriter，姑且称之为投机侠吧。</p>
<pre><code> * This class implements sort-based shuffle's hash-style shuffle fallback path. This write path
 * writes incoming records to separate files, one file per reduce partition, then concatenates these
 * per-partition files to form a single output file, regions of which are served to reducers.
 * Records are not buffered in memory. It writes output in a format
</code></pre>
<h2><a id="2__45"></a>2 华山论剑之成员力量</h2>
<p>BypassMergeSortShuffleWriter可是直接开挂的节奏，完全没有什么排序器啊，我来承担一切。我最屌，我承担一切，心声，嘿嘿。</p>
<h3><a id="21_BypassMergeSortShuffleWriter_48"></a>2.1 BypassMergeSortShuffleWriter的孩子：</h3>
<ul>
<li>
<p>partitionWriters : 看看初始化为数组 ==&gt; private DiskBlockObjectWriter[] partitionWriters，每一个DiskBlockObjectWriter负责处理一个分区的数据。</p>
</li>
<li>
<p>private final int fileBufferSize ==&gt;文件缓冲大小，通过Spark.shuffle.file.buffer属性配置，默认是32KB。</p>
</li>
<li>
<p>private final boolean transferToEnabled =&gt; 是否采用spark.file.transferTo属性配置，默认是true。</p>
</li>
<li>
<p>private final int numPartitions =&gt; 分区数</p>
</li>
<li>
<p>private final BlockManager blockManager</p>
</li>
<li>
<p>private final Partitioner partitioner =&gt; 分区计算器</p>
</li>
<li>
<p>private final ShuffleWriteMetrics writeMetrics</p>
</li>
<li>
<p>private final int shuffleId;</p>
</li>
<li>
<p>private final int mapId  ==&gt;map任务的身份标识。</p>
</li>
<li>
<p>private final Serializer serializer;</p>
</li>
<li>
<p>private final IndexShuffleBlockResolver shuffleBlockResolver</p>
</li>
<li>
<p>private FileSegment[] partitionWriterSegments ==&gt;FileSegment数组，每一个DiskBlockObjectWriter对应一个分区，也因此对应一个处理的文件片。</p>
</li>
<li>
<p>@Nullable private MapStatus mapStatus;</p>
</li>
<li>
<p>private long[] partitionLengths;</p>
</li>
</ul>
<h2><a id="2_BypassMergeSortShuffleWriterWriter_66"></a>2 BypassMergeSortShuffleWriter核心实现方法Writer</h2>
<p>先欣赏代码段：</p>
<pre><code>    public void write(Iterator&lt;Product2&lt;K, V&gt;&gt; records) throws IOException {
        assert (partitionWriters == null);
        if (!records.hasNext()) {
          partitionLengths = new long[numPartitions];
          shuffleBlockResolver.writeIndexFileAndCommit(shuffleId, mapId, partitionLengths, null);
          mapStatus = MapStatus$.MODULE$.apply(blockManager.shuffleServerId(), partitionLengths);
          return;
        }
        final SerializerInstance serInstance = serializer.newInstance();
        final long openStartTime = System.nanoTime();
        
        partitionWriters = new DiskBlockObjectWriter[numPartitions];    &lt;=点睛之笔
        partitionWriterSegments = new FileSegment[numPartitions];       &lt;=点睛之笔
        
        for (int i = 0; i &lt; numPartitions; i++) {              &lt;=点睛之笔（按照分区来写片段）
          final Tuple2&lt;TempShuffleBlockId, File&gt; tempShuffleBlockIdPlusFile =
            blockManager.diskBlockManager().createTempShuffleBlock();
          final File file = tempShuffleBlockIdPlusFile._2();
          final BlockId blockId = tempShuffleBlockIdPlusFile._1();
          
          partitionWriters[i] =                     &lt;=点睛之笔（得到不同分区的DiskBlockObjectWriter）
            blockManager.getDiskWriter(blockId, file, serInstance, fileBufferSize, writeMetrics);
        }                                           
        
        
        // Creating the file to write to and creating a disk writer both involve interacting with
        // the disk, and can take a long time in aggregate when we open many files, so should be
        // included in the shuffle write time.
        writeMetrics.incWriteTime(System.nanoTime() - openStartTime);
    
        while (records.hasNext()) {
          final Product2&lt;K, V&gt; record = records.next();
          final K key = record._1();
          partitionWriters[partitioner.getPartition(key)].write(key, record._2());
        }
    
        for (int i = 0; i &lt; numPartitions; i++) {
          final DiskBlockObjectWriter writer = partitionWriters[i];
          partitionWriterSegments[i] = writer.commitAndGet();  &lt;= 生成一堆临时文件，写入到磁盘
          writer.close();
        }
    
        File output = shuffleBlockResolver.getDataFile(shuffleId, mapId);  &lt;==获取一堆临时文件
        File tmp = Utils.tempFileWith(output);
        try {
        
        
          partitionLengths = writePartitionedFile(tmp);   &lt;==多个分区文件合并
          shuffleBlockResolver.writeIndexFileAndCommit(shuffleId, mapId, partitionLengths, tmp);
                                                          &lt;==生成索引
                                                          
                                                          
        } finally {
          if (tmp.exists() &amp;&amp; !tmp.delete()) {
            logger.error("Error while deleting temp file {}", tmp.getAbsolutePath());
          }
        }
        mapStatus = MapStatus$.MODULE$.apply(blockManager.shuffleServerId(), partitionLengths);
      }
</code></pre>
<h2><a id="2_BypassMergeSortShuffleWriterwritePartitionedFile_129"></a>2 BypassMergeSortShuffleWriter核心实现方法writePartitionedFile</h2>
<p>聚合每一个分区文件为正式的Block文件</p>
<pre><code>    Concatenate all of the per-partition files into a single combined file.

    private long[] writePartitionedFile(File outputFile) throws IOException {
        // Track location of the partition starts in the output file
        final long[] lengths = new long[numPartitions];
        if (partitionWriters == null) {
          // We were passed an empty iterator
          return lengths;
        }
    
        final FileOutputStream out = new FileOutputStream(outputFile, true);
        final long writeStartTime = System.nanoTime();
        boolean threwException = true;
        try {
          for (int i = 0; i &lt; numPartitions; i++) {
            final File file = partitionWriterSegments[i].file();
            if (file.exists()) {
              final FileInputStream in = new FileInputStream(file);
              boolean copyThrewException = true;
              try {
                lengths[i] = Utils.copyStream(in, out, false, transferToEnabled);
                copyThrewException = false;
              } finally {
                Closeables.close(in, copyThrewException);
              }
              if (!file.delete()) {
                logger.error("Unable to delete file for partition {}", i);
              }
            }
          }
          threwException = false;
        } finally {
          Closeables.close(out, threwException);
          writeMetrics.incWriteTime(System.nanoTime() - writeStartTime);
        }
        partitionWriters = null;
        return lengths;
      }
</code></pre>
<h2><a id="3_BypassMergeSortShuffleWritershuffle_write_171"></a>3 BypassMergeSortShuffleWriter核心shuffle write流程</h2>
<ul>
<li>根据分区ID，为每一个分区创建DiskBlockObjectWriter</li>
<li>按照分区ID升序写入正式的Shuffle数据文件</li>
<li>最终通过writeIndexFileAndCommit建立MapTask输出的数据索引</li>
</ul>
<p>不废话，这张图简直画的太好了，望原图作者看到留言于我。<br>
<img src="https://user-gold-cdn.xitu.io/2018/11/6/166e4c6d05c6686a?w=1243&amp;h=825&amp;f=png&amp;s=488631" alt=""></p>
<h2><a id="4__179"></a>4 总结</h2>
<p>本节内容是作者投入大量时间优化后的内容，采用最平实的语言来剖析 ShuffeManager之统一存储服务BypassMergeSortShuffleWriter设计思路。</p>
<p>秦凯新 于深圳 0:53分</p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>