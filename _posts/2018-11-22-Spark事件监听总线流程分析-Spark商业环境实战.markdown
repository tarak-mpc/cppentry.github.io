---
layout:     post
title:      Spark事件监听总线流程分析-Spark商业环境实战
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本套技术专栏是作者（秦凯新）平时工作的总结和升华，通过从真实商业环境抽取案例进行总结和分享，并给出商业应用的调优建议和集群环境容量规划等内容，请持续关注本套博客。版权声明：禁止转载，欢迎学习。QQ邮箱地址：1120746959@qq.com，如有任何商业交流，可随时联系。					https://blog.csdn.net/shenshouniu/article/details/83511315				</div>
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
<h2><a id="1_Spark_16"></a>1. Spark事件监听总线流程分析</h2>
<h3><a id="11_Spark_19"></a>1.1 Spark事件监听总线流程分析</h3>
<p>如下图所示事件日志监听器EventLoggingListener，实现了SparkListenerInterface接口，重写了所有的事件处理函数，包括Stage提交，stage完成等。</p>
<p>通过在SparkContext初始化时把日志监听器EventLoggingListener注册到LiveListenerBus事件总线上,并启动LiveListenerBus内部的Thread线程，监听提交到总线上的事件，调用SparkListenerBus的eventQueue.poll  --&gt;postToAll(event) --&gt; doPostEvent方法，并进行事件匹配后处理,如：EventLoggingListener执行StageSubmited提交。</p>
<p><img src="https://user-gold-cdn.xitu.io/2018/10/28/166ba92c064a0661?w=1381&amp;h=737&amp;f=png&amp;s=317299" alt=""></p>
<h3><a id="12_Spark_UI__26"></a>1.2 Spark UI 事件监听总线流程分析</h3>
<p>Spark UI的可视化展示，是有不同的监听器实现的，他们都分别注册在LiveListenerBus上，如下面SparkContext的初始化片段：</p>
<pre><code>  if (conf.getBoolean("spark.ui.enabled", true)) {
    Some(SparkUI.createLiveUI(this, _conf, listenerBus, _jobProgressListener,
      _env.securityManager, appName, startTime = startTime))
  } else {
    // For tests, do not enable the UI
    None
  }
</code></pre>
<p>下面片段展示的是SparkUI.createLiveUI方法，可以看到监听器的注册，通过事件的投递（如：DAGScheduler ，DriverEndpoint等），从而实现UI的数据展示：</p>
<pre><code>  val _jobProgressListener: JobProgressListener = jobProgressListener.getOrElse {
  val listener = new JobProgressListener(conf)
  listenerBus.addListener(listener)
  listener
}

val environmentListener = new EnvironmentListener
val storageStatusListener = new StorageStatusListener(conf)
val executorsListener = new ExecutorsListener(storageStatusListener, conf)
val storageListener = new StorageListener(storageStatusListener)
val operationGraphListener = new RDDOperationGraphListener(conf)

listenerBus.addListener(environmentListener)
listenerBus.addListener(storageStatusListener)
listenerBus.addListener(executorsListener)
listenerBus.addListener(storageListener)
listenerBus.addListener(operationGraphListener)
</code></pre>
<h2><a id="3__58"></a>3 结语</h2>
<p>秦凯新 于深圳 2018-10-28</p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>