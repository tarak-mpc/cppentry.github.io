---
layout:     post
title:      [spark streaming]资源动态申请和动态控制消费速率原理剖析
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>本讲内容：</p>

<p>a. Spark Streaming资源动态分配 <br>
b. Spark Streaming动态控制消费速率</p>

<p>注：本讲内容基于Spark 1.6.1版本（在2016年5月来说是Spark最新版本）讲解</p>

<p><strong>上节回顾</strong></p>

<p>上一讲中，我们要给大家解密Spark Streaming中数据清理的流程，主要从背景、Spark Streaming数据是如何清理的、源码解析三个方面给大家逐一展开</p>

<p><strong>背景</strong></p>

<p>Spark Streaming数据清理的工作无论是在实际开发中，还是自己动手实践中都是会面临的，Spark Streaming中Batch Durations中会不断的产生RDD，这样会不断的有内存对象生成，其中包含元数据和数据本身。由此Spark Streaming本身会有一套产生元数据以及数据的清理机制</p>

<p><strong>Spark Streaming数据是如何清理的？</strong></p>

<p>a. 操作DStream的时候会产生元数据，所以要解决RDD的数据清理工作就一定要从DStream入手。因为DStream是RDD的模板，DStream之间有依赖关系。</p>

<p>DStream的操作产生了RDD,接收数据也靠DStream，数据的输入，数据的计算，输出整个生命周期都是由DStream构建的。由此，DStream负责RDD的整个生命周期。因此研究的入口的是DStream</p>

<p>b. 基于Kafka数据来源，通过Direct的方式访问Kafka,DStream随着时间的进行，会不断的在自己的内存数据结构中维护一个HashMap,HashMap维护的就是时间窗口，以及时间窗口下的RDD.按照Batch Duration来存储RDD以及删除RDD</p>

<p>c. Spark Streaming本身是一直在运行的，在自己计算的时候会不断的产生RDD，例如每秒Batch Duration都会产生RDD,除此之外可能还有累加器，广播变量。由于不断的产生这些对象，因此Spark Streaming有自己的一套对元数据以及数据的清理机制</p>

<p>d. Spark运行在jvm上，jvm会产生对象，jvm需要对对象进行回收工作，如果我们不管理gc（对象产生和回收），jvm很快耗尽。现在研究的是Spark Streaming的Spark GC。Spark Streaming对rdd的数据管理、元数据管理相当jvm对gc管理。 数据、元数据是操作DStream时产生的，数据、元数据的回收则需要研究DStream的产生和回收</p>

<p><strong>源码解密</strong></p>

<p><em>具体链接：<a href="http://blog.csdn.net/zisheng_wang_data/article/details/51729947" rel="nofollow">http://blog.csdn.net/zisheng_wang_data/article/details/51729947</a></em></p>

<p><strong>开讲</strong></p>

<p>本讲我们要给大家解密park Streaming两个比较高级的特性，资源动态申请和动态控制消费速率原理</p>

<p>此时你也许会有个疑问：<strong>为什么要动态资源分配和动态控制速率呢？</strong></p>

<p>默认情况下，Spark是先分配好资源，然后在进行计算，也就是粗粒度的资源分配 <br>
粗粒度的好处：资源是提前给分配好的，所以计算任务的时候，直接使用这些资源 <br>
粗粒度的缺点：从Spark Streaming的角度讲，有高峰值和低峰值，高峰和低峰的时候，需要的资源是不一样的；如果按照高峰值的角度去分配，低峰值的时候，有大量的资源的浪费</p>

<p>资源的动态分配是由一个定时器，不断的扫描Executor的情况，例如：有段时间之内，Executor没收到任何任务，所以会把这个Executor移除掉</p>

<p>动态资源调整的时候，最好不要设置太多的Core，Core设置的太多，假如资源调整太过频繁的话，是比较麻烦的(Core 的个数一般设置为奇数：3、5、7)</p>

<p>Spark Streaming要进行处理资源的动态调整，就是Executor的动态调整 <br>
Spark Streaming是Batch Duration的方式执行的，这个Batch Duration里需要很多资源，下一个Batch Duration里不需要那么多资源，可能想调整资源的时候，还没来得及调整完资源，当前的这个Batch Duration的运行已过期的情况，这个时候的资源调整就是浪费的</p>

<p><strong>资源动态申请</strong></p>

<p>spark streaming是基于spark core的，spark core也支持资源动态分配。可以在spark.dynamicAllocation.enabled中进行配置是否开启动态分配。如果开启就new ExecutorAllocationManager</p>

<p><img alt="这里写图片描述" class="has" src="https://img-blog.csdn.net/20160622213828776"></p>

<p>通过配置参数：spark.dynamicAllocation.enabled看是否需要开启Executor的动态分配</p>

<p><img alt="这里写图片描述" class="has" src="https://img-blog.csdn.net/20160622214018758"></p>

<p>根据代码发现，你可以在程序运行时不断设置spark.dynamicAllocation.enabled参数的值，如果支持资源动态分配的话就使用ExecutorAllocationManager类</p>

<p><img alt="这里写图片描述" class="has" src="https://img-blog.csdn.net/20160622214644705"></p>

<p><em>在ExecutorAllocationManager中可以配置动态executor的分配（spark.dynamicAllocation.enabled），动态executor的下限 （spark.dynamicAllocation.minExecutors），动态executor的上限 （spark.dynamicAllocation.maxExecutors），动态executor的初始值（spark.dynamicAllocation.initialExecutors）</em></p>

<p>ExecutorAllocationManager的原理：ExecutorAllocationManager中有个定时器会不断的扫描executor的情况，扫描executor的什么情况呢？例如说正在运行的stage，stage肯定要运行在不同的executor中，所谓动态就是要么增加executor要么减少executor。举个例子，减少executor的情况，假如在60秒的时候executor一个任务还没有运行，就有可能把这个executor rmove掉。为什么可以去掉executor？在driver的数据结构中有当前应用程序运行的所有executor的引用，每次进行schedule的时候循环遍历这个executor列表，看一下每个executor的可用资源，ExecutorAllocationManager的定时器就会不断的循环看是否满足添加executor或者减少executor的条件</p>

<p><strong>需要实现资源动态调度的话需要一个时钟协助，资源默认分配的方式在master的scheduler如果通过配置动态分配资源会调用ExecutorAllocationManager类的scheduler方法</strong></p>

<p><img alt="这里写图片描述" class="has" src="https://img-blog.csdn.net/20160622215441058"></p>

<p><img alt="这里写图片描述" class="has" src="https://img-blog.csdn.net/20160622215452314"></p>

<p>schedule方法的启动是在start的时候，把schedule方法封装进一个runable借口中，使用线程池周期性的调用。默认时间是100ms，每隔100ms就调整一次。生产情况下一般给一个executor设置3-5个core，奇数稳定性可能好点。不适合给一个executor设置过多的core，因为资源经常调整的话，core的申请和释放也是一种消耗</p>

<p><img alt="这里写图片描述" class="has" src="https://img-blog.csdn.net/20160622215953704"></p>

<p>以上是spark core的资源动态调整</p>

<p>spark streaming在进行资源动态调整的时候有别的问题，spark streaming是基于batchduration运行的，可能这个batchduration需要的资源少，下一个batchduration需要的资源多，可能资源调整的时候还没有调整完这个运行就过期了，这样调整就没有意义了。可以考虑把资源调整的时间设置和batchduration一样</p>

<p><strong>动态控制消费速率</strong></p>

<p>实际生产环境下，动态资源分配可能要自己做好定制。</p>

<p>Spark Streaming的动态调整的复杂之处在于，即使在batch duration内刚做了调整，但可能本batch duration马上就会过期</p>

<p>你可以考虑改变执行周期（intervalMills），来动态调整。在一个batch duration中要对数据分片，可以算一下已拥有闲置的core，如果不够，则可以申请增加Executor，从而把任务分配到新增的Executor</p>

<p>也可以考量针对上一个batch duration的资源需求情况，因为峰值出现时，往往会延续在多个连续的batch duration中。考量上一个batch duration的情况，用某种算法来动态调整后续的batch duration的资源。修改Spark Streaming可以设计StreamingContext的新子类</p>

<p>其实前面的动态资源分配的定制方式做起来不容易，可能仍不太合适</p>

<p>Spark Streaming中已经有自动的动态控制速度流进来的速度的方式。配置项是 spark.streaming.backpressure.enabled。<strong>详见论文 <em><strong>dynamic_batching.pdf</strong></em></strong>(<strong>强烈建议设为true</strong>)</p>            </div>
                </div>