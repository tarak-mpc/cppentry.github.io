---
layout:     post
title:      Spark任务延迟调度及调度池Pool架构剖析-Spark商业环境实战
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本套技术专栏是作者（秦凯新）平时工作的总结和升华，通过从真实商业环境抽取案例进行总结和分享，并给出商业应用的调优建议和集群环境容量规划等内容，请持续关注本套博客。版权声明：禁止转载，欢迎学习。QQ邮箱地址：1120746959@qq.com，如有任何商业交流，可随时联系。					https://blog.csdn.net/shenshouniu/article/details/83686714				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h3><a id="Spark_1"></a>Spark商业环境实战及调优进阶系列</h3>
<h3><a id="Spark_2"></a>Spark商业环境实战及调优进阶系列</h3>
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
<h2><a id="1__14"></a>1. 任务延迟调度原理</h2>
<p>Spark数据本地化即移动计算而不是移动数据，为了让task能尽可能的以最优本地化级别（Locality Levels）来启动，Spark的延迟调度应运而生，资源不够可在该Locality Levels对应的限制时间内重试，超过限制时间后还无法启动则降低Locality Levels再尝试启动。</p>
<h2><a id="2_TaskSetManger_18"></a>2. TaskSetManger重要成员</h2>
<ul>
<li>taskSet：TaskSet由当前TaskSetManager管理，一个TaskSet对应一个Stage，一个TaskSet归属于一个TaskSetManager，而TaskSetManager又归属于TaskSetManager Pool来管理。</li>
</ul>
<h2><a id="3__22"></a>3. 任务的启动过程</h2>
<ul>
<li>
<p>注意 CoarseGrainedSchedulerBackend.makeOffers在任意Executor上变动时，开始调用，makeOffers属于公共方法。</p>
<pre><code>   StatusUpdate
   RegisterExecutor
</code></pre>
</li>
<li>
<p>首先选定一个Executor，选中在指定executor上的任务，以最大优先级分配。</p>
<pre><code>  CoarseGrainedSchedulerBackend.makeOffers(公共方法，任务分配触发点，过滤活的Executor())
                        ||
                        ||
                       \||/
   val tasks = TaskSchedulerImpl.resourceOffer(workOffers) (分配开始)
                        ||
                        ||
                       \||/
  (遍历所有TaskSet内部的Task的优先级，以最大本地性开始分配任务)
  for (currentMaxLocality &lt;- taskSet.myLocalityLevels) 
                        ||
                        ||
                       \||/
      (遍历所有可用的Executor，以指定Executor开始分配)
      for (i &lt;- 0 until shuffledOffers.size)  
                        ||
                        ||
                       \||/
        (选定一个Executor，通过TaskSetManager进行专项任务分配)
         for (task &lt;- taskSet.resourceOffer(execId, host, maxLocality)) {  
              tasks(i) += task
              val tid = task.taskId
              taskIdToTaskSetManager(tid) = taskSet
              taskIdToExecutorId(tid) = execId
              executorIdToRunningTaskIds(execId).add(tid)
              availableCpus(i) -= CPUS_PER_TASK
              assert(availableCpus(i) &gt;= 0)
              launchedTask = true
           }
                        ||
                        ||
                       \||/
       (TaskSchedulerImpl引用结束后，返回Tasks，后执行)
       CoarseGrainedSchedulerBackend.launchTasks(taskS)  
</code></pre>
</li>
<li>
<p>通过在CoarseGrainedSchedulerBackend 中的makeOffers方法，通过scheduler的引用，执行TaskSchedulerImpl.resourceOffers 方法，返回taskDescs（包含了所有Task的位置信息和task的算子等），后执行launchTasks，向 executor 发送消息executorData.executorEndpoint.send(LaunchTask(new SerializableBuffer(serializedTask)))</p>
<pre><code> private def makeOffers() {
 
 val taskDescs = CoarseGrainedSchedulerBackend.this.synchronized {
 
 val activeExecutors = executorDataMap.filterKeys(executorIsAlive)
 val workOffers = activeExecutors.map {
   case (id, executorData) =&gt;
     new WorkerOffer(id, executorData.executorHost, executorData.freeCores)
 }.toIndexedSeq
 
  scheduler.resourceOffers(workOffers)
</code></pre>
<p>}<br>
if (!taskDescs.isEmpty) {<br>
launchTasks(taskDescs)<br>
}<br>
}</p>
</li>
<li>
<p>TaskSetManager中resourceOffer内部的是如何分配任务的呢？</p>
</li>
<li>
<pre><code>  -&gt; allowedLocality = getAllowedLocalityLevel(curTime) (延迟调度)

  -&gt; dequeueTask(execId, host, allowedLocality).map { case ((index, taskLocality,
     speculative)  （返回TaskDescription序列，方便后续发送到Executor）
</code></pre>
</li>
</ul>
<h2><a id="4__95"></a>4 总结</h2>
<p>本篇内容还需要完善，并做进一步剖析。</p>
<p>秦凯新 于深圳</p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>