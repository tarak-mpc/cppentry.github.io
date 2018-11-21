---
layout:     post
title:      Spark 定制版：006~Spark Streaming源码解读之Job动态生成和深度思考
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/zisheng_wang_DATA/article/details/51477818				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>本讲内容：</p>

<p>a. Spark Streaming Job生成深度思考  <br>
b. Spark Streaming Job生成源码解析</p>

<p>注：本讲内容基于Spark 1.6.1版本（在2016年5月来说是Spark最新版本）讲解。</p>

<p><strong>上节回顾</strong></p>

<p>上节课，主要是从Spark Streaming+Spark SQL来实现分类最热门商品的在线动态计算的事例代码开始，并通过Spark源代码给大家贯通Spark Streaming流计算框架的运行。</p>

<p>并绘制了Spark Streaming应用运行流程的关键类：</p>

<p><img src="https://img-blog.csdn.net/20160522232611123" alt="这里写图片描述" title=""></p>

<p>根据上面的运行类图，逐步解密了Spark Streaming流计算框架，了解了Job整个运行的关键类，及其对类中方法的解读。</p>

<p><strong>开讲</strong></p>

<p>数据处理的方式：大致可以分为以下三种：</p>

<p>a.  批处理（<em>基本上是处理静态数据，一次读入大量的数据进行处理并生成输出结果</em>）</p>

<p>b.  微批处理（<em>结合了批处理和连续流操作符，将输入分成多个微批次进行处理；可以这么理解，微批处理是一个“收集后再处理”的计算模型；可以认为接近实时流处理</em>）</p>

<p>c.  连续流模型（<em>连续流操作符则在数据到达时进行处理，没有任何数据收集或处理延迟；此乃真正的实时流处理</em>）</p>

<p>换句话说：微批处理，当时间间隔足够短的时候就形成了流式处理，进而认为到达了实时处理（<strong><em>Spark 2.0 中Spark Streaming 有了重大的改进，可以期待一个Spark实时连续流处理时代的到来！</em></strong>）。</p>

<p>首先，我们来看一下Job动态生成图：</p>

<p><img src="https://img-blog.csdn.net/20160523000608903" alt="这里写图片描述" title=""></p>

<p>本讲，我们以上图中的JobGenerator类为重心，开始为大家左右延伸，解密Job之动态生成。</p>

<p>JobGenerator这个类生成的Jobs来自DStreams，并驱动检查点获得且是清洗掉上一个timer中DStream的数据之后的元数据。</p>

<p>其构造函数中需要传入JobScheduler对象，而JobScheduler类是Spark Streaming Job生成和提交Job到集群的核心。</p>

<p><img src="https://img-blog.csdn.net/20160523001347279" alt="这里写图片描述" title=""></p>

<p>我们可以看到生成Jobs的是JobGenerator的Start()方法。该方法中有个eventLoop，是具体操作事务循环，来不断获事务进程。</p>

<p><img src="https://img-blog.csdn.net/20160523231156699" alt="这里写图片描述" title=""></p>

<p>processEvent根据time，来执行相应的操作：generateJobs(time)，回到generateJobs(time: Time)； <br>
根据time产生Jobs</p>

<p><img src="https://img-blog.csdn.net/20160523231645378" alt="这里写图片描述" title=""> <br>
<img src="https://img-blog.csdn.net/20160523232357071" alt="这里写图片描述" title=""></p>

<p>graph.generateJobs(time)进入到generateJobs(time: Time): Seq[Job]</p>

<p><img src="https://img-blog.csdn.net/20160523232903738" alt="这里写图片描述" title=""></p>

<p>outputStream.generateJob(time)进入到generateJob(time: Time): Option[Job]</p>

<p><img src="https://img-blog.csdn.net/20160523233431842" alt="这里写图片描述" title=""></p>

<p>调用context.sparkContext.runJob(rdd, emptyFunc)，进入到runJob，计算同一个RDD中的所有数据块，并将结果存放在一个数组里。</p>

<p><img src="https://img-blog.csdn.net/20160523233717906" alt="这里写图片描述" title=""></p>

<p>至此，我们从新回过头来，看看JobGenerator中的generateJobs(time: Time)，里面有个jobScheduler.submitJobSet(JobSet(time, jobs, streamIdToInputInfos))方法</p>

<p><img src="https://img-blog.csdn.net/20160523234338284" alt="这里写图片描述" title=""></p>

<p>进入到jobScheduler.submitJobSet中，最终交给了new JobHandler(job))</p>

<p><img src="https://img-blog.csdn.net/20160523234711855" alt="这里写图片描述" title=""></p>

<p>进入到JobHandler中。我们发现继承了Runnable接口。启动了run方法，循环执行各种业务逻辑任务。</p>

<p><img src="https://img-blog.csdn.net/20160523235116334" alt="这里写图片描述" title=""></p>

<p>进入上图中的job.run()，执行很多个任务</p>

<p><img src="https://img-blog.csdn.net/20160523235657384" alt="这里写图片描述" title=""></p>

<p>由上图中的func()，再次回到DStream中的 generateJob(time: Time): Option[Job]</p>

<p><img src="https://img-blog.csdn.net/20160524000332893" alt="这里写图片描述" title=""></p>

<p>至此，我们回到JobScheduler类，start()方法中一个receiverTracker.start()方法</p>

<p><img src="https://img-blog.csdn.net/20160524205757853" alt="这里写图片描述" title=""></p>

<p>进入到receiverTracker.start()方法中</p>

<p><img src="https://img-blog.csdn.net/20160524210045045" alt="这里写图片描述" title=""></p>

<p>进入到ReceiverTrackerEndpoint</p>

<p><img src="https://img-blog.csdn.net/20160524210803226" alt="这里写图片描述" title=""></p>

<p><em>思考一个问题：DStreamGraph逻辑级别翻译成物理级别的RDD Graph，最后一个操作是RDD的action操作，是否会立即触发Job？</em></p>

<p>a. action触发作业，这个时候作为Runnable接口封装，他会定义一个方法，这个方法里面是基于DStream的依赖关系生成的RDD。翻译的时候 是将DStream的依赖关系翻译成RDD的依赖关系，由于DStream的依赖关系最后一个是action级别的，翻译成RDD的时候，RDD的最后一 个操作也应该是action级别的，如果翻译的时候直接执行的话，就直接生成了Job，就没有所谓的队列，所以会将翻译的事件放到一个函数中或者一个方法fun() 中，因此，如果这个函数没有指定的action触发作业是执行不了的。</p>

<p>b. Spark Streaming根据时间不断的去管理我们的生成的作业，所以这个时候我们每个作业又有action级别的操作，这个action操作是对 DStream进行逻辑级别的操作，他生成每个Job放到队列的时候，他一定会被翻译为RDD的操作，那基于RDD操作的最后一个一定是action级别 的，如果翻译的话直接就是触发action的话整个Spark Streaming的Job就不受管理了。因此我们既要保证他的翻译，又要保证对他的管理，把DStream之间的依赖关系转变为RDD之间的依赖关系， 最后一个DStream使得action的操作，翻译成一个RDD之间的action操作，整个翻译后的内容他是一块内容，他这一块内容是放在一个函数体 中的，这个函数体，他会函数的定义，这个函数由于他只是定义还没有执行，所以他里面的RDD的action不会执行，不会触发Job,当我们的 JobScheduler要调度Job的时候，转过来在线程池中拿出一条线程执行刚才的封装的方法。</p>

<p><strong>我们再次聚焦Spark 作业动态生成三大核心</strong></p>

<p>a.  JobGenerator: 负责Job生成</p>

<p>b. JobSheduler:负责Job调度</p>

<p>c. ReceiverTracker:获取元数据</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>