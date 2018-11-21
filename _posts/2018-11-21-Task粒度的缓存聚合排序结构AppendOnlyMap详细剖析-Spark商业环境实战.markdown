---
layout:     post
title:      Task粒度的缓存聚合排序结构AppendOnlyMap详细剖析-Spark商业环境实战
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本套技术专栏是作者（秦凯新）平时工作的总结和升华，通过从真实商业环境抽取案例进行总结和分享，并给出商业应用的调优建议和集群环境容量规划等内容，请持续关注本套博客。版权声明：禁止转载，欢迎学习。QQ邮箱地址：1120746959@qq.com，如有任何商业交流，可随时联系。					https://blog.csdn.net/shenshouniu/article/details/83686638				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h3><a id="Spark_0"></a>Spark商业环境实战及调优进阶系列</h3>
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
<h3><a id="1_AppendOnlyMap__13"></a>1. AppendOnlyMap 何许人也？</h3>
<p>Spark提供了AppendOnlyMap数据结构来对任务执行结果进行聚合运算。可谓一件利器，为什么这样说呢？因为Spark是基于内存运算的大数据计算引擎，即能基于内存做数据存储，也能基于内存进行插入，更新，聚合，排序等操作。由此可以看出，Spark真正把内存的使用技术发挥到了极致。</p>
<pre><code>    * A simple open hash table optimized for the append-only use case, where keys
    * are never removed, but the value for each key may be changed.
    *
    * This implementation uses quadratic probing with a power-of-2 hash table
    * size, which is guaranteed to explore all spaces for each key (see
    * http://en.wikipedia.org/wiki/Quadratic_probing).
    * The map can support up to `375809638 (0.7 * 2 ^ 29)` elements.
</code></pre>
<h4><a id="11_AppendOnlyMap__23"></a>1.1 AppendOnlyMap 内部成员及特殊使命</h4>
<ul>
<li>
<p>提供对null值得缓存</p>
</li>
<li>
<p>initialCapacity : 主构造函数传入  class AppendOnlyMap[K, V](initialCapacity: Int = 64)</p>
</li>
<li>
<p>capacity ：容量取值为：nextPowerOf2(initialCapacity)，具体就是补零对比，相同为原值，不相同则左移加一位。</p>
</li>
<li>
<p>data : 用于保存key和聚合值得数组。new Array[AnyRef](2 * capacity)</p>
<pre><code>  * Holds keys and values in the same array for memory locality;
  * specifically, the order of elements is key0, value0, key1, value1, 
  * key2, value2, etc. 
</code></pre>
</li>
<li>
<p>LOAD_FACTOR :默认为0.7</p>
</li>
<li>
<p>growThreshold : (LOAD_FACTOR * capacity).toInt</p>
</li>
</ul>
<h4><a id="12_AppendOnlyMap__35"></a>1.2 AppendOnlyMap 常用方法：</h4>
<ul>
<li>
<p>growTable ：扩容容量为原先的两倍，对key进行re-hash放入新数组。Double the table’s size and re-hash everything。</p>
</li>
<li>
<p>update ：key和value的更新。三种情况：1：rehash(key.hashCode) &amp; mask对应位置没有值，直接插入。2：对应位置有值且等于原先key，直接更新。3：对应位置有值且<br>
不等于原先key，向后挪动一位。</p>
<pre><code>def update(key: K, value: V): Unit = {
   assert(!destroyed, destructionMessage)
   val k = key.asInstanceOf[AnyRef]
   if (k.eq(null)) {
     if (!haveNullValue) {
       incrementSize()
     }
     nullValue = value
     haveNullValue = true
     return
    }
    var pos = rehash(key.hashCode) &amp; mask
    var i = 1
    while (true) {
     val curKey = data(2 * pos)
     if (curKey.eq(null)) {
       data(2 * pos) = k
       data(2 * pos + 1) = value.asInstanceOf[AnyRef]
       incrementSize()  // Since we added a new key
       return
     } else if (k.eq(curKey) || k.equals(curKey)) {
       data(2 * pos + 1) = value.asInstanceOf[AnyRef]
       return
     } else {
       val delta = i
       pos = (pos + delta) &amp; mask
       i += 1
     }
   }
 }
</code></pre>
</li>
<li>
<p>changeValue ：缓存聚合算法，根据指定函数进行值的聚合操作，updateFunc为匿名函数。三种情况：1：rehash(key.hashCode) &amp; mask对应位置没有值，与NULL值聚合。2：对应位置有值且等于原先key，直接聚合。3：对应位置有值，且不等于原先key，向后挪动一位插入。</p>
<pre><code> def changeValue(key: K, updateFunc: (Boolean, V) =&gt; V): V = {
   assert(!destroyed, destructionMessage)
   val k = key.asInstanceOf[AnyRef]
   if (k.eq(null)) {
     if (!haveNullValue) {
       incrementSize()
     }
     nullValue = updateFunc(haveNullValue, nullValue)
     haveNullValue = true
     return nullValue
   }
   var pos = rehash(k.hashCode) &amp; mask
   var i = 1
   while (true) {
     val curKey = data(2 * pos)
     if (curKey.eq(null)) {
       val newValue = updateFunc(false, null.asInstanceOf[V])
       data(2 * pos) = k
       data(2 * pos + 1) = newValue.asInstanceOf[AnyRef]
       incrementSize()
       return newValue
     } else if (k.eq(curKey) || k.equals(curKey)) {
       val newValue = updateFunc(true, data(2 * pos + 1).asInstanceOf[V])
       data(2 * pos + 1) = newValue.asInstanceOf[AnyRef]
       return newValue
     } else {
       val delta = i
       pos = (pos + delta) &amp; mask
       i += 1
     }
   }
   null.asInstanceOf[V] // Never reached but needed to keep compiler happy
 }
</code></pre>
</li>
<li>
<p>destructiveSortedIterator：在不牺牲额外内存和不牺牲AppendOnlyMap的有效性的前提下，对AppendOnlyMap的data数组中的数据进行排序实现。这里使用了优化版的TimSort，英文解释如下：</p>
<pre><code>  * return an iterator of the map in sorted order. This provides a way to sort the
  * map without using additional memory, at the expense of destroying the validity
  * of the map.
</code></pre>
<p>代码片段如下：</p>
<pre><code>   def destructiveSortedIterator(keyComparator: Comparator[K]): Iterator[(K, V)] = {
      destroyed = true
      // Pack KV pairs into the front of the underlying array
       var keyIndex, newIndex = 0
       while (keyIndex &lt; capacity) {
        if (data(2 * keyIndex) != null) {
          data(2 * newIndex) = data(2 * keyIndex)
          data(2 * newIndex + 1) = data(2 * keyIndex + 1)
          newIndex += 1
        }
        keyIndex += 1
      }
      assert(curSize == newIndex + (if (haveNullValue) 1 else 0))
      new Sorter(new KVArraySortDataFormat[K, AnyRef]).sort(data, 0, newIndex, keyComparator)
      new Iterator[(K, V)] {
        var i = 0
        var nullValueReady = haveNullValue
        def hasNext: Boolean = (i &lt; newIndex || nullValueReady)
        def next(): (K, V) = {
          if (nullValueReady) {
            nullValueReady = false
            (null.asInstanceOf[K], nullValue)
          } else {
            val item = (data(2 * i).asInstanceOf[K], data(2 * i + 1).asInstanceOf[V])
            i += 1
            item
          }
        }
      }
    }   
</code></pre>
</li>
</ul>
<h3><a id="2_AppendOnlyMap__147"></a>2. AppendOnlyMap 孩子的延伸特性？</h3>
<ul>
<li>
<p>SizeTrackingAppendOnlyMap ：以自身的大小进行样本采集和大小估算。</p>
<pre><code>  An append-only map that keeps track of its estimated size in bytes.
</code></pre>
<p>SizeTrackingAppendOnlyMap的代码段，好短啊：</p>
<pre><code>  private[spark] class SizeTrackingAppendOnlyMap[K, V]
    extends AppendOnlyMap[K, V] with SizeTracker
  {
    override def update(key: K, value: V): Unit = {
      super.update(key, value)
      super.afterUpdate()
    }
  
    override def changeValue(key: K, updateFunc: (Boolean, V) =&gt; V): V = {
      val newValue = super.changeValue(key, updateFunc)
      super.afterUpdate()
      newValue
    }
  
    override protected def growTable(): Unit = {
      super.growTable()
      resetSamples()
    }
  }
</code></pre>
</li>
<li>
<p>PartitionedAppendOnlyMap ：增加了partitionedDestructiveSortedIterator，调用了AppendOnlyMap的destructiveSortedIterator对底层数组进行整理和排序后获得迭代器。</p>
<h5><a id="1keyId__175"></a>（1）主要作用是根据指定的key比较器，返回对集合中的数据按照分区Id 顺序进行迭代的迭代器。</h5>
<pre><code>  * Implementation of WritablePartitionedPairCollection that wraps a map in which the
  * keys are tuples of (partition ID, K)

  private[spark] class PartitionedAppendOnlyMap[K, V]
    extends SizeTrackingAppendOnlyMap[(Int, K), V] with
    WritablePartitionedPairCollection[K, V] {
  
        （WritablePartitionedPairCollection定义的接口，未实现）
    def partitionedDestructiveSortedIterator(keyComparator: Option[Comparator[K]])
      : Iterator[((Int, K), V)] = {
      val comparator = keyComparator.map(partitionKeyComparator).getOrElse(partitionComparator)
     
     （AppendOnlyMap内部方法，对底层的data数组进行整理和排序后获得迭代器）
        destructiveSortedIterator(comparator)
    }
</code></pre>
<h5><a id="2__keypartition_______keyupdatepartition_key_value_193"></a>（2）  同时对插入的键值进行了扩展，增加了分区和key的键值对元祖(partition,       key)类型，如下：update((partition, key), value)。</h5>
<pre><code>    def insert(partition: Int, key: K, value: V): Unit = {
      update((partition, key), value)
    }
  }
</code></pre>
</li>
</ul>
<h3><a id="3_AppendOnlyMap_202"></a>3 原创总结AppendOnlyMap牛在哪里？</h3>
<ul>
<li>1 大大减少了数据占用内存的大小？</li>
<li>2 来对中间结果进行聚合，Tim sort 优化排序算法</li>
<li>3 Spark的Map任务逐条输出计算结果，而不是一次性输出到内存，并通过使用AppendOnlyMap缓存及其聚合算法来对中间结果进行聚合，这样大大减少了中间结果所占用的内存。</li>
<li>4 Spark的reduce任务对拉取到的map任务中间结果逐条读取，而不是一次性读入内存，并在内存中进行聚合和排序， 本质上读入内存操作都是经过AppendOnlyMap，大大减少了数据占用内存的大小。</li>
<li>5 通过优化的SizeTrackingAppendOnlyMap，SizeTrackingPairBuff及Tungsten的page进行溢出判断，当超过限制时，会把数据写入磁盘，放着内存溢出。</li>
</ul>
<h3><a id="4_Spark_shuffle__209"></a>4 Spark shuffle 的流程剖析</h3>
<pre><code>- 1: Spark的Map任务在输出时会根据分区进行计算，并输出数据文件和索引文件。
- 2：Spark的shuffle过程会伴随着缓存，排序，聚合，溢出，合并操作。当然远端拉取Block的操作必不可少。
</code></pre>
<h3><a id="5__212"></a>5 最后</h3>
<p>秦凯新 于深圳</p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>