---
layout:     post
title:      Spark内存管理（4）—— UnifiedMemoryManager分析
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/lemonZhaoTao/article/details/82659634				</div>
								            <div id="content_views" class="markdown_views prism-github-gist">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>Spark内存管理系列文章：  <br>
<a href="https://blog.csdn.net/lemonZhaoTao/article/details/81990408" rel="nofollow">Spark内存管理（1）—— 静态内存管理</a> <br>
<a href="https://blog.csdn.net/lemonZhaoTao/article/details/82027469" rel="nofollow">Spark内存管理（2）—— 统一内存管理</a> <br>
<a href="https://blog.csdn.net/lemonZhaoTao/article/details/82079375" rel="nofollow">Spark内存管理（3）—— 统一内存管理设计理念</a></p>



<h2 id="acquireexecutionmemory方法">acquireExecutionMemory方法</h2>

<p>关注UnifiedMemoryManager中的accquireExecutionMemory方法： <br>
<img src="https://img-blog.csdn.net/20180912122323565?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xlbW9uWmhhb1Rhbw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<p>当前的任务尝试从executor中<strong>获取numBytes这么大的内存</strong> <br>
该方法直接向ExecutionMemoryPool索要所需内存，索要内存有以下几个关注点：</p>

<ul>
<li>当ExecutionMemory 内存充足，则不会触发向Storage申请内存</li>
<li>每个Task能够被使用的内存是被限制的</li>
<li>索要内存的大小</li>
</ul>

<p>我们通过源码来进行分析： <br>
UnifiedMemoryManager.scala中： <br>
<img src="https://img-blog.csdn.net/20180912122435579?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xlbW9uWmhhb1Rhbw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""> <br>
我们点进去后会发现，会调用ExecutionMemoryPool.acquireMemory()方法</p>

<p>ExecutionMemoryPool.scala中： <br>
<img src="https://img-blog.csdn.net/20180912122502895?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xlbW9uWmhhb1Rhbw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""> <br>
我们可以发现每Task能够被使用的内存被限制在： <br>
poolSize / (2 * numActiveTasks) ~ maxPoolSize / numActiveTasks 之间</p>

<p>val maxMemoryPerTask = maxPoolSize /numActiveTasks <br>
val minMemoryPerTask = poolSize / (2 * numActiveTasks)</p>

<p>UnifiedMemoryManager.scala中： <br>
<img src="https://img-blog.csdn.net/20180912122613717?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xlbW9uWmhhb1Rhbw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""> <br>
其中maxPoolSize = maxMemory - math.min(storageMemoryUsed, storageRegionSize) <br>
maxMemory = storage + execution的最大内存 <br>
poolSize = 当前这个pool的大小 <br>
maxPoolSize = execution pool的最大内存</p>

<p>UnifiedMemoryManager.scala中： <br>
<img src="https://img-blog.csdn.net/20180912122657923?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xlbW9uWmhhb1Rhbw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""> <br>
从上述代码中我们可以知道索要内存的大小： <br>
val memoryReclaimableFromStorage=math.max <br>
(storageMemoryPool.memoryFree, storageMemoryPool.poolSize -storageRegionSize) <br>
取决于StorageMemoryPool的剩余内存和 storageMemoryPool 从ExecutionMemory借来的内存哪个大，取最大的那个，作为可以重新归还的最大内存 <br>
用公式表达出来就是这一个样子： <br>
ExecutionMemory 能借到的最大内存 = StorageMemory 借的内存 + StorageMemory 空闲内存 <br>
<strong>注意：</strong>如果实际需要的小于能够借到的最大值，则以实际需要值为准</p>

<p>能回收的内存大小为： <br>
val spaceToReclaim =storageMemoryPool.freeSpaceToShrinkPool <br>
( math.min(extraMemoryNeeded,memoryReclaimableFromStorage))</p>



<h2 id="executionmemorypoolacquirememory解析">ExecutionMemoryPool.acquireMemory()解析</h2>

<pre class="prettyprint"><code class=" hljs fsharp">    <span class="hljs-keyword">while</span> (<span class="hljs-keyword">true</span>) {
      <span class="hljs-keyword">val</span> numActiveTasks = memoryForTask.keys.size
      <span class="hljs-keyword">val</span> curMem = memoryForTask(taskAttemptId)

      maybeGrowPool(numBytes - memoryFree)

      <span class="hljs-keyword">val</span> maxPoolSize = computeMaxPoolSize()
      <span class="hljs-keyword">val</span> maxMemoryPerTask = maxPoolSize / numActiveTasks
      <span class="hljs-keyword">val</span> minMemoryPerTask = poolSize / (<span class="hljs-number">2</span> * numActiveTasks)

      <span class="hljs-keyword">val</span> maxToGrant = math.min(numBytes, math.max(<span class="hljs-number">0</span>, maxMemoryPerTask - curMem))

      <span class="hljs-keyword">val</span> toGrant = math.min(maxToGrant, memoryFree)

      <span class="hljs-keyword">if</span> (toGrant &lt; numBytes &amp;&amp; curMem + toGrant &lt; minMemoryPerTask) {
        logInfo(s<span class="hljs-string">"TID $taskAttemptId waiting for at least 1/2N of $poolName pool to be free"</span>)
        lock.wait()
      } <span class="hljs-keyword">else</span> {
        memoryForTask(taskAttemptId) += toGrant
        <span class="hljs-keyword">return</span> toGrant
      }
    }
</code></pre>

<p>整体流程解析： <br>
程序一直处理该task的请求，直到系统判定无法满足该请求或者已经为该请求分配到足够的内存为止；如果当前execution内存池剩余内存不足以满足此次请求时，会向storage部分请求释放出被借走的内存以满足此次请求</p>

<p>根据此刻execution内存池的总大小maxPoolSize，以及从memoryForTask中统计出的处于active状态的task的个数计算出： <br>
每个task能够得到的最大内存数 maxMemoryPerTask = maxPoolSize / numActiveTasks <br>
每个task能够得到的最少内存数 minMemoryPerTask = poolSize /(2 * numActiveTasks)</p>

<p>根据申请内存的task当前使用的execution内存大小决定分配给该task多少内存，总的内存不能超过maxMemoryPerTask；但是如果execution内存池能够分配的最大内存小于numBytes，并且如果把能够分配的内存分配给当前task，但是该task最终得到的execution内存还是小于minMemoryPerTask时，该task进入等待状态，等其他task申请内存时再将其唤醒，唤醒之后如果此时满足，就会返回能够分配的内存数，并且更新memoryForTask，将该task使用的内存调整为分配后的值 <br>
<strong>一个Task最少需要minMemoryPerTask才能开始执行</strong></p>

<h2 id="acquirestoragememory方法">acquireStorageMemory方法</h2>

<p>流程和acquireExecutionMemory类似，当storage的内存不足时，同样会向execution借内存，但区别是当且仅当ExecutionMemory有空闲内存时，StorageMemory 才能借走该内存</p>

<p>UnifiedMemoryManager.scala中： <br>
<img src="https://img-blog.csdn.net/20180912122840985?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xlbW9uWmhhb1Rhbw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""> <br>
从上述代码中我们可以知道能借到的内存数为： <br>
val memoryBorrowedFromExecution = Math.min <br>
(onHeapExecutionMemoryPool.memoryFree,numBytes) <br>
所以StorageMemory从ExecutionMemory借走的内存，完全取决于当时ExecutionMemory是不是有空闲内存；借到内存后，storageMemoryPool增加借到的这部分内存，之后同上一样，会调用StorageMemoryPool的acquireMemory()方法</p>

<h2 id="storagememorypoolacquirememory">StorageMemoryPool.acquireMemory</h2>

<p><img src="https://img-blog.csdn.net/20180912122921248?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xlbW9uWmhhb1Rhbw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""> <br>
整体流程解析： <br>
在申请内存时，如果numBytes大于此刻storage内存池的剩余内存，即if (numBytesToFree &gt; 0)，那么需要storage内存池释放一部分内存以满足申请需求 <br>
注意：这里的numBytesToFree可以理解为numBytes大小减去Storage内存池剩余大小，大于0，即所需要申请的numBytes大于Storage内存池剩余的内存 <br>
释放内存后如果memoryFree &gt;= numBytes，就会把这部分内存分配给申请内存的task，并且更新storage内存池的使用情况 <br>
同时StorageMemoryPool与ExecutionMemoryPool不同的是，<strong>他不会像前者那样分不到资源就进行等待，acquireStorageMemory只会返回一个true或是false，告知内存分配是否成功</strong></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>