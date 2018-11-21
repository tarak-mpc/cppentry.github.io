---
layout:     post
title:      “戏”说spark---spark 内存管理详解
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/weixin_35602748/article/details/78724947				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div style="text-align:center;line-height:1.75;font-size:14px;">
<span style="font-size:22px;font-weight:bold;">“戏”说spark---spark 内存管理</span></div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
<span style="font-size:18px;font-weight:bold;">Spark内存管理简述</span></div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
Spark 作为一个基于内存的分布式计算引擎，其内存管理模块在整个系统中扮演着非常重要的角色。理解 Spark 内存管理的基本原理，有助于更好地开发 Spark 应用程序和进行性能调优。</div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
在“戏”说Spark---Spark架构一文中，我们知道在执行 Spark 的应用程序时，Spark 集群会启动 Driver 和 Executor 两种 JVM 进程，前者为主控进程，负责创建 Spark 上下文，提交 Spark 作业（Job），并将作业转化为计算任务（Task），在各个 Executor 进程间协调任务的调度，后者负责在工作节点上执行具体的计算任务，并将结果返回给 Driver，同时为需要持久化的 RDD 提供存储功能。由于 Driver 的内存管理相对来说较为简单，本文主要对 Executor
 的内存管理进行分析，下文中的 Spark 内存均特指 Executor 的内存。</div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
作为一个 JVM 进程，Executor 的内存管理建立在 JVM 的内存管理之上，Spark 对 JVM 的堆内（On-heap）空间进行了更为详细的分配，以充分利用内存。同时，Spark 引入了堆外（Off-heap）内存，使之可以直接在工作节点的系统内存中开辟空间，进一步优化了内存的使用。</div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
<img src="https://img-blog.csdn.net/20171205211028629?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd2VpeGluXzM1NjAyNzQ4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
图解：</div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
<span style="font-size:16px;font-weight:bold;">堆内内存：</span></div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
1）存储（Storage）内存：</div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
堆内内存的大小，由 Spark 应用程序启动时的 –executor-memory 或 spark.executor.memory 参数配置。Executor 内运行的并发任务共享 JVM 堆内内存，这些任务在缓存 RDD 数据和广播（Broadcast）数据时占用的内存被规划为存储（Storage）内存</div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
2)执行（Execution）内存:</div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
这些任务在执行 Shuffle 时占用的内存被规划为执行（Execution）内存</div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
3)剩余内存</div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
剩余的部分不做特殊规划，那些 Spark 内部的对象实例，或者用户定义的 Spark 应用程序中的对象实例，均占用剩余的空间。不同的管理模式下，这三部分占用的空间大小各不相同</div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
<span style="font-size:16px;font-weight:bold;">堆外内存:</span></div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
为了进一步优化内存的使用以及提高 Shuffle 时排序的效率，Spark 引入了堆外（Off-heap）内存，使之可以直接在工作节点的系统内存中开辟空间，存储经过序列化的二进制数据。利用 JDK Unsafe API（从 Spark 2.0 开始，在管理堆外的存储内存时不再基于 Tachyon，而是与堆外的执行内存一样，基于 JDK Unsafe API 实现），Spark 可以直接操作系统堆外内存，减少了不必要的内存开销，以及频繁的 GC 扫描和回收，提升了处理性能。堆外内存可以被精确地申请和释放，而且序列化的数据占用的空间可以被精确计算，所以相比堆内内存来说降低了管理的难度，也降低了误差。</div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
在默认情况下堆外内存并不启用，可通过配置 spark.memory.offHeap.enabled 参数启用，并由 spark.memory.offHeap.size 参数设定堆外空间的大小。除了没有 other 空间，堆外内存与堆内内存的划分方式相同，所有运行中的并发任务共享存储内存和执行内存。</div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
运行流程：</div>
<ul><li style="list-style-type:disc;list-style-position:inside;text-align:left;line-height:1.75;font-size:14px;">
<span style="font-weight:bold;">申请内存</span>：</li></ul><ol><li style="list-style-type:decimal;list-style-position:inside;text-align:left;line-height:1.75;font-size:14px;">
Spark 在代码中 new 一个对象实例</li><li style="list-style-type:decimal;list-style-position:inside;text-align:left;line-height:1.75;font-size:14px;">
JVM 从堆内内存分配空间，创建对象并返回对象引用</li><li style="list-style-type:decimal;list-style-position:inside;text-align:left;line-height:1.75;font-size:14px;">
Spark 保存该对象的引用，记录该对象占用的内存</li></ol><ul><li style="list-style-type:disc;list-style-position:inside;text-align:left;line-height:1.75;font-size:14px;">
<span style="font-weight:bold;">释放内存</span>：</li></ul><ol><li style="list-style-type:decimal;list-style-position:inside;text-align:left;line-height:1.75;font-size:14px;">
Spark 记录该对象释放的内存，删除该对象的引用</li><li style="list-style-type:decimal;list-style-position:inside;text-align:left;line-height:1.75;font-size:14px;">
等待 JVM 的垃圾回收机制释放该对象占用的堆内内存</li></ol><div style="text-align:left;line-height:1.75;font-size:14px;">
<span style="font-size:18px;font-weight:bold;">堆内和堆外内存规划</span></div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
<span style="font-size:18px;font-weight:bold;">基于JVM的Spark内存管理</span></div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
我们知道，JVM 的对象可以以序列化的方式存储，序列化的过程是将对象转换为二进制字节流，本质上可以理解为将非连续空间的链式存储转化为连续空间或块存储，在访问时则需要进行序列化的逆过程——反序列化，将字节流转化为对象，序列化的方式可以节省存储空间，但增加了存储和读取时候的计算开销。</div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
对于 Spark 中序列化的对象，由于是字节流的形式，其占用的内存大小可直接计算，而对于非序列化的对象，其占用的内存是通过周期性地采样近似估算而得，即并不是每次新增的数据项都会计算一次占用的内存大小，这种方法降低了时间开销但是有可能误差较大，导致某一时刻的实际内存有可能远远超出预期。此外，在被 Spark 标记为释放的对象实例，很有可能在实际上并没有被 JVM 回收，导致实际可用的内存小于 Spark 记录的可用内存。所以 Spark 并不能准确记录实际可用的堆内内存，从而也就无法完全避免内存溢出（OOM,
 Out of Memory）的异常。</div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
虽然不能精准控制堆内内存的申请和释放，但 Spark 通过对存储内存和执行内存各自独立的规划管理，可以决定是否要在存储内存里缓存新的 RDD，以及是否为新的任务分配执行内存，在一定程度上可以提升内存的利用率，减少异常的出现。</div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
<span style="font-size:18px;font-weight:bold;">内存管理接口</span></div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
Spark 为存储内存和执行内存的管理提供了统一的接口——MemoryManager，同一个 Executor 内的任务都调用这个接口的方法来申请或释放内存:</div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
内存管理接口的主要方法：</div>
<div style="font-size:14px;overflow:auto;">
<table width="621" cellspacing="0" cellpadding="0" border="1" style="table-layout:fixed;border-collapse:collapse;border:1px solid rgb(204,204,204);"><colgroup><col style="width:620px;"></colgroup><tbody><tr><td>//申请存储内存<br>
def acquireStorageMemory(blockId: BlockId, numBytes: Long, memoryMode: MemoryMode): Boolean<br>
//申请展开内存<br>
def acquireUnrollMemory(blockId: BlockId, numBytes: Long, memoryMode: MemoryMode): Boolean<br>
//申请执行内存<br>
def acquireExecutionMemory(numBytes: Long, taskAttemptId: Long, memoryMode: MemoryMode): Long<br>
//释放存储内存<br>
def releaseStorageMemory(numBytes: Long, memoryMode: MemoryMode): Unit<br>
//释放执行内存<br>
def releaseExecutionMemory(numBytes: Long, taskAttemptId: Long, memoryMode: MemoryMode): Unit<br>
//释放展开内存<br>
def releaseUnrollMemory(numBytes: Long, memoryMode: MemoryMode): Unit</td>
</tr></tbody></table></div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
我们看到，在调用这些方法时都需要指定其内存模式（MemoryMode），这个参数决定了是在堆内还是堆外完成这次操作。</div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
MemoryManager 的具体实现上，Spark 1.6 之后默认为统一管理（<a href="https://github.com/apache/spark/blob/v2.1.0/core/src/main/scala/org/apache/spark/memory/UnifiedMemoryManager.scala" rel="nofollow"><span>Unified Memory Manager</span></a>）方式，1.6 之前采用的静态管理（<a href="https://github.com/apache/spark/blob/v2.1.0/core/src/main/scala/org/apache/spark/memory/StaticMemoryManager.scala" rel="nofollow"><span>Static
 Memory Manager</span></a>）方式仍被保留，可通过配置 spark.memory.useLegacyMode 参数启用。</div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
<span style="font-size:18px;font-weight:bold;">静态内存管理</span></div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
<span style="font-size:16px;font-weight:bold;">静态内存管理图示——堆内</span></div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
在 Spark 最初采用的静态内存管理机制下，存储内存、执行内存和其他内存的大小在 Spark 应用程序运行期间均为固定的，但用户可以应用程序启动前进行配置，堆内内存的分配如下图所示：</div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
<img src="https://img-blog.csdn.net/20171205211034002?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd2VpeGluXzM1NjAyNzQ4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
60%（90%（---80%（存储的是RDD的缓存数据和广播变量）---20%（unroll内存：解压序列化的数据)）---10%（预留出来的））---20%（80%（用于Shuffle聚合）---20%（预留内存，防止OOM））---20%（线程执行所需要的内存：task的计算）</div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
注意：</div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
调整静态的内存管理的配置：</div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
spark.storage.memoryFraction 0.6-----储存内存</div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
spark.storage.unrollFraction 0.2-----unroll内存</div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
spark.shuffle.memoryFraction 0.2----执行内存</div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
<span style="font-size:16px;font-weight:bold;">可用堆内内存空间</span></div>
<div style="font-size:14px;overflow:auto;">
<table cellspacing="0" cellpadding="0" border="1" style="table-layout:fixed;border-collapse:collapse;border:1px solid #ccc;width:620px;"><colgroup><col style="width:620px;"></colgroup><tbody><tr><td>可用的存储内存 = systemMaxMemory * spark.storage.memoryFraction * spark.storage.safetyFraction<br><br>
可用的执行内存 = systemMaxMemory * spark.shuffle.memoryFraction * spark.shuffle.safetyFraction</td>
</tr></tbody></table></div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
其中 systemMaxMemory 取决于当前 JVM 堆内内存的大小，最后可用的执行内存或者存储内存要在此基础上与各自的 memoryFraction 参数和 safetyFraction 参数相乘得出。上述计算公式中的两个 safetyFraction 参数，其意义在于在逻辑上预留出 1-safetyFraction 这么一块保险区域，降低因实际内存超出当前预设范围而导致 OOM 的风险（上文提到，对于非序列化对象的内存采样估算会产生误差）。值得注意的是，这个预留的保险区域仅仅是一种逻辑上的规划，在具体使用时
 Spark 并没有区别对待，和"其它内存"一样交给了 JVM 去管理。</div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
堆外的空间分配较为简单，只有存储内存和执行内存，如图 3 所示。可用的执行内存和存储内存占用的空间大小直接由参数 spark.memory.storageFraction 决定，由于堆外内存占用的空间可以被精确计算，所以无需再设定保险区域。</div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
<span style="font-size:16px;font-weight:bold;">静态内存管理图示——堆外</span></div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
<span style="font-size:16px;font-weight:bold;"><img src="https://img-blog.csdn.net/20171205211039161?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd2VpeGluXzM1NjAyNzQ4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
静态内存管理机制实现起来较为简单，但如果用户不熟悉 Spark 的存储机制，或没有根据具体的数据规模和计算任务或做相应的配置，很容易造成"一半海水，一半火焰"的局面，即存储内存和执行内存中的一方剩余大量的空间，而另一方却早早被占满，不得不淘汰或移出旧的内容以存储新的内容。由于新的内存管理机制的出现，这种方式目前已经很少有开发者使用，出于兼容旧版本的应用程序的目的，Spark 仍然保留了它的实现。</div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
<span style="font-size:18px;font-weight:bold;">统一内存管理</span></div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
Spark 1.6 之后引入的统一内存管理机制，与静态内存管理的区别在于存储内存和执行内存共享同一块空间，可以动态占用对方的空闲区域</div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
<span style="font-size:16px;font-weight:bold;">统一内存管理图示——堆内</span></div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
<span style="font-size:16px;font-weight:bold;"><img src="https://img-blog.csdn.net/20171205211044785?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd2VpeGluXzM1NjAyNzQ4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
75%(50%(存储RDD的缓存数据和广播变量)---50%(shuffle的聚合)----50%可以互相借用（申--检--借--还）)---25%（task的计算）---300M(用于JVM自身的运行)</div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
统一的内存管理的配置：</div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
spark.memory.fraction 0.75-----统一内存</div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
spark.memory.storageFraction 0.5----Storage内存</div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
spark.memory.useLegacyMode false（内存管理模式的选择，默认使用同一的内存管理）</div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
<span style="font-size:16px;font-weight:bold;">统一内存管理图示——堆外</span></div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
<span style="font-size:16px;font-weight:bold;"><img src="https://img-blog.csdn.net/20171205211050304?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd2VpeGluXzM1NjAyNzQ4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
其中最重要的优化在于动态占用机制，其规则如下：</div>
<ul><li style="list-style-type:disc;list-style-position:inside;text-align:left;line-height:1.75;font-size:14px;">
设定基本的存储内存和执行内存区域（spark.storage.storageFraction 参数），该设定确定了双方各自拥有的空间的范围</li><li style="list-style-type:disc;list-style-position:inside;text-align:left;line-height:1.75;font-size:14px;">
双方的空间都不足时，则存储到硬盘；若己方空间不足而对方空余时，可借用对方的空间;（存储空间不足是指不足以放下一个完整的 Block）</li><li style="list-style-type:disc;list-style-position:inside;text-align:left;line-height:1.75;font-size:14px;">
执行内存的空间被对方占用后，可让对方将占用的部分转存到硬盘，然后"归还"借用的空间</li><li style="list-style-type:disc;list-style-position:inside;text-align:left;line-height:1.75;font-size:14px;">
存储内存的空间被对方占用后，无法让对方"归还"，因为需要考虑 Shuffle 过程中的很多因素，实现起来较为复杂</li></ul><div style="text-align:left;line-height:1.75;font-size:14px;">
<span style="font-size:13px;font-weight:bold;">动态占用机制图示</span></div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
<span style="font-size:13px;font-weight:bold;"><img src="https://img-blog.csdn.net/20171205211054949?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd2VpeGluXzM1NjAyNzQ4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
凭借统一内存管理机制，Spark 在一定程度上提高了堆内和堆外内存资源的利用率，降低了开发者维护 Spark 内存的难度，但并不意味着开发者可以高枕无忧。譬如，所以如果存储内存的空间太大或者说缓存的数据过多，反而会导致频繁的全量垃圾回收，降低任务执行时的性能，因为缓存的 RDD 数据通常都是长期驻留内存的 。所以要<span style="color:#4d80bf;font-weight:bold;">想充分发挥 Spark 的性能，需要开发者进一步了解存储内存和执行内存各自的管理方式和实现原理。</span></div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
<span style="font-size:16px;font-weight:bold;">Spark Unroll</span></div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
RDD 在缓存到存储内存之前，Partition 中的数据一般以迭代器（<a href="http://www.scala-lang.org/docu/files/collections-api/collections_43.html" rel="nofollow"><span>Iterator</span></a>）的数据结构来访问，这是 Scala 语言中一种遍历数据集合的方法。通过 Iterator 可以获取分区中每一条序列化或者非序列化的数据项(Record)，这些
 Record 的对象实例在逻辑上占用了 JVM 堆内内存的 other 部分的空间，同一 Partition 的不同 Record 的空间并不连续。</div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
RDD 在缓存到存储内存之后，Partition 被转换成 Block，Record 在堆内或堆外存储内存中占用一块连续的空间。<span style="font-weight:bold;">将Partition由不连续的存储空间转换为连续存储空间的过程，Spark称之为"展开"（Unroll）。</span>Block 有序列化和非序列化两种存储格式，具体以哪种方式取决于该 RDD 的存储级别。非序列化的 Block 以一种 DeserializedMemoryEntry 的数据结构定义，用一个数组存储所有的对象实例，序列化的
 Block 则以 SerializedMemoryEntry的数据结构定义，用字节缓冲区（ByteBuffer）来存储二进制数据。每个 Executor 的 Storage 模块用一个链式 Map 结构（LinkedHashMap）来管理堆内和堆外存储内存中所有的 Block 对象的实例[6]，对这个 LinkedHashMap 新增和删除间接记录了内存的申请和释放。</div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
因为不能保证存储空间可以一次容纳 Iterator 中的所有数据，当前的计算任务在 Unroll 时要向 MemoryManager 申请足够的 Unroll 空间来临时占位，空间不足则 Unroll 失败，空间足够时可以继续进行。对于序列化的 Partition，其所需的 Unroll 空间可以直接累加计算，一次申请。而非序列化的 Partition 则要在遍历 Record 的过程中依次申请，即每读取一条 Record，采样估算其所需的 Unroll 空间并进行申请，空间不足时可以中断，释放已占用的 Unroll
 空间。如果最终 Unroll 成功，当前 Partition 所占用的 Unroll 空间被转换为正常的缓存 RDD 的存储空间</div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
<img src="https://img-blog.csdn.net/20171205211100761?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd2VpeGluXzM1NjAyNzQ4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
在静态内存管理时，Spark 在存储内存中专门划分了一块 Unroll 空间，其大小是固定的，统一内存管理时则没有对 Unroll 空间进行特别区分，当存储空间不足时会根据动态占用机制进行处理。</div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
注意：</div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
<span style="font-weight:bold;">配置项的使用方式：</span></div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
1：代码中设置，但是不推荐，属于硬编码，不灵活。例如：new SparkConf().set("spark.shuffle.file.buffer","64")</div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
2：提交应用程序的时候设置,推荐使用，灵活 spark-submit --master --conf spark.shuffle.file.buffer=64 --conf spark.reduce.maxSizeInFlight=24</div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
3：在conf目录下面的spark-default.conf下面配置，不推荐，写死了</div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
<span style="font-size:18px;font-weight:bold;">总结：</span></div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
Spark的内存的管理是基于JVM的内存管理，spark中的Driver进程是JVM进程，Executor进程是JVM进程。Executor进程负责工作节点上具体的计算任务，同时为需要持久化的RDD提供存储功能。Spark对Executor的堆内内存进行了更为详细的分配，同时引进了堆外内存,可以直接在工作节点的系统内存中开辟空间，进一步优化了内存的使用。</div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
堆内的内存又分为存储内存和执行内存，存储内存为tasks并发任务执行时的共享内存，缓存 RDD 数据和广播（Broadcast）数据时占用的内存。执行内存任务执行的时候遇到Shuffle时候占用的内存；剩余的内存为Spark 内部的对象实例，或者用户定义的 Spark 应用程序中的对象实例占用的内存。Spark的内存管理分为统一的内存管理和静态的内存管理，不同的管理模式下，存储内存，执行内存，剩余内存部分占用的空间大小各不相同。</div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
Spark的堆外内存 可以直接操作系统堆外内存，减少了不必要的内存开销，以及频繁的 GC 扫描和回收，提升了处理性能。堆外内存可以被精确地申请和释放，而且序列化的数据占用的空间可以被精确计算，所以相比堆内内存来说降低了管理的难度，也降低了误差。</div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
Spark 为存储内存和执行内存的管理提供了统一的接口——MemoryManager.MemoryManager 的具体实现上分为静态管理（<a href="https://github.com/apache/spark/blob/v2.1.0/core/src/main/scala/org/apache/spark/memory/StaticMemoryManager.scala" rel="nofollow"><span>Static Memory Manager</span></a>）方式和统一管理（<a href="https://github.com/apache/spark/blob/v2.1.0/core/src/main/scala/org/apache/spark/memory/UnifiedMemoryManager.scala" rel="nofollow"><span>Unified
 Memory Manager</span></a>）方式。park 1.6 之后默认为统一管理管理方式。</div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
Spark 静态内存管理机制下，存储内存、执行内存和其他内存的大小在 Spark 应用程序运行期间均为固定的，但用户可以应用程序启动前进行配置。spark的静态内存管理：存储内存，执行内存，剩余内存占用比例如下：</div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
堆内内存比例划分：</div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
60%（90%（---80%（存储的是RDD的缓存数据和广播变量）---20%（unroll内存：解压序列化的数据)）---10%（预留出来的））---20%（80%（用于Shuffle聚合）---20%（预留内存，防止OOM））---20%（线程执行所需要的内存：task的计算）</div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
堆外内存比例划分：</div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
50%(storage内存)---50%(Execution内存)</div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
静态的内存管理由于其存储内存和执行内存中的一方剩余大量的空间，而另一方却早早被占满，不得不淘汰或移出旧的内容以存储新的内容。因此比较难把握，现在已经很少在使用。</div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
Spark的统一的内存管理以其存储内存和执行内存共享同一块空间，互相可以动态的“借用”而比较高效。动态占用机制有其一套的规则</div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
spark的统一的内存管理：存储内存，执行内存，剩余内存占用比例如下：</div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
堆内内存比例划分：</div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
75%(50%(存储RDD的缓存数据和广播变量)---50%(shuffle的聚合)----50%可以互相借用（申--检--借--还）)---25%（task的计算）---300M(用于JVM自身的运行)</div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
堆外内存比例划分：</div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
50%(storage内存)---50%(Execution内存)</div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
在静态内存管理时，Spark 在存储内存中专门划分了一块 Unroll 空间，其大小是固定的，统一内存管理时则没有对 Unroll 空间进行特别区分，当存储空间不足时会根据动态占用机制进行处理。</div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
在提交应用程序的时候，为了提高应用程序的性能和执行速度，一般需要设置配置文件优化，设置优化的方式有三种，可以conf配置文件中设置，可以代码中设置，但是这两种方式都是硬编码的方式，缺少灵活性。通常建议使用提交应用程序的时候--conf进行设置，可以灵活的修改，调优。</div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
<span style="font-size:18px;font-weight:bold;">思维导图构建你的知识架构：</span></div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
<img src="https://img-blog.csdn.net/20171205211109821?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd2VpeGluXzM1NjAyNzQ4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
参考：</div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
注：本文大部分参考博客（优秀）：</div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
<a href="https://www.ibm.com/developerworks/cn/analytics/library/ba-cn-apache-spark-memory-management/index.html?ca=drs-&amp;utm_source=tuicool&amp;utm_medium=referral" rel="nofollow"><span style="text-decoration:underline;">https://www.ibm.com/developerworks/cn/analytics/library/ba-cn-apache-spark-memory-management/index.html?ca=drs-&amp;utm_source=tuicool&amp;utm_medium=referral</span></a></div>
            </div>
                </div>