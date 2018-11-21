---
layout:     post
title:      Spark Streaming之运行架构
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/zhanglh046/article/details/78505067				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-family:'Times New Roman';font-size:18px;">一 Spark Streaming 运行架构图</span></p>
<p><span style="font-family:'Times New Roman';font-size:18px;"><img src="https://img-blog.csdn.net/20171111102918046?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvemhhbmdsaDA0Ng==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></p>
<p></p>
<p><span style="font-family:'Times New Roman';font-size:18px;">二 Spark Streaming各个组件</span></p>
<p><span style="font-family:'Times New Roman';font-size:18px;">2.1 StreamingContext: Spark Streaming 中Driver端的上下文对象，初始化的时候会构造Spark Streaming应用程序需要使用的组件，比如DStreamGraph、JobScheduler等</span></p>
<p><span style="font-family:'Times New Roman';font-size:18px;">2.2 DStreamGraph：用于保存DStream和DStream之间依赖关系等信息</span></p>
<p><span style="font-family:'Times New Roman';font-size:18px;">2.3 JobScheduler: 主要用于调度job。JobScheduler主要通过JobGenerator产生job，并且通过ReceiverTracker管理流数据接收器Receiver</span></p>
<p><span style="font-family:'Times New Roman';font-size:18px;">2.4 JobGenerator: 主要是从DStream产生job, 且根据指定时间执行checkpoint. 他维护了一个定时器，该定时器在批处理时间到来的时候会进行生成作业的操作。</span></p>
<p><span style="font-family:'Times New Roman';font-size:18px;">2.5 ReceiverTracker: 管理各个Executor上的Receiver的元数据。它在启动的时候，需要根据流数据接收器Receiver分发策略通知对应的Executor中的ReceiverSupervisor(接收器管理着)启动，然后再由ReceiverSupervisor来启动对应节点的Receiver</span></p>
<p><span style="font-family:'Times New Roman';font-size:18px;">2.6 ReceiverTrackerEndpoint: ReceiverTracker用于通信的RPC终端</span></p>
<p><span style="font-family:'Times New Roman';font-size:18px;">2.7 Receiver：数据接收器，用于接收数据，通过ReceiverSupervisor将数据交给ReceiveBlockHandler来处理</span></p>
<p><span style="font-family:'Times New Roman';font-size:18px;">2.8 ReceiverSupervisor：主要用于管理各个worker节点上的Receivor,比如启动worker上的Receiver，或者是转存数据，交给ReceiveBlockHandler来处理；数据转存完毕，将数据存储的元信息汇报给ReceiverTracker，由它来负责管理收到的数据块元信息</span></p>
<p><span style="font-family:'Times New Roman';font-size:18px;">2.9 BlockGenerator: 这个类的主要作用是创建Receiver接收的数据的batches,然后根据时间间隔命名为合适的block. 并且把准备就绪的batches作为block 推送到BlockManager</span></p>
<p><span style="font-family:'Times New Roman';font-size:18px;">2.10 ReceiveBlockHandler：主要根据是否启用WAL预写日志的机制，区分为预写日志和非预写日志存储。非预写日志则是直接将数据通过BlockManager写入Worker的内存或者磁盘;而预写日志则是在预写日志的同时把数据写入Worker的内存或者磁盘</span></p>
<p><span style="font-family:'Times New Roman';font-size:18px;">2.11 ReceiverSchedulingPolicy： Receiver调度策略</span></p>
<p><span style="font-family:'Times New Roman';font-size:18px;">三 消息通信机制</span></p>
<span style="font-family:'Times New Roman';font-size:18px;"><br></span>
<p><span style="font-family:'Times New Roman';font-size:18px;"><img src="https://img-blog.csdn.net/20171111102924897?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvemhhbmdsaDA0Ng==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></p>
<p></p>
<p><span style="font-family:'Times New Roman';font-size:18px;">启动过程中，会启动所有的数据接收器Receiver和注册Receiver。在接收存储数据中，当数据块存储完成后发送添加数据块消息，而当Spark Streaming停止的时候发送关闭所有的Receiver的消息</span></p>
<p><span style="font-family:'Times New Roman';font-size:18px;"> </span></p>
<p><span style="font-family:'Times New Roman';font-size:18px;">四 Receiver分发、调度机制</span></p>
<p><span style="font-family:'Times New Roman';font-size:18px;">Spark Streaming 中处理数据一方面通过内部接口获取，一方面来自原kafka,flume或者其他外部系统，获取之后对这些输入源进行map,join、window等操作。这些数据均抽象于DStream。</span></p>
<p><span style="font-family:'Times New Roman';font-size:18px;">根据DStream是初始输入的还是之后转换操作形成的，分为InputDStream、ForEachDStream、MappedDStream、TransformedDStream、ShuffledDStream等</span></p>
<p><span style="font-family:'Times New Roman';font-size:18px;">对于InputDStream，我们根据不同的输入来源，又区别为FileInputDStream、DirectKafkaInputDStream、QueueInputDStream以及ReceiverInputDStream等等，另外我们还可以通过继承PluggableInputDStream自定义流数据接收器。</span></p>
<p><span style="font-family:'Times New Roman';font-size:18px;"> </span></p>
<p><span style="font-family:'Times New Roman';font-size:18px;">Streaming 启动过程中，ReceiverTracker会起动所有的Receiver，所以需要对Receiver进行调度或者说把Receiver分发到Executor上，然后由Executor中ReceiverSupervisor起动对应的Receiver。</span></p>
<p><span style="font-family:'Times New Roman';font-size:18px;"> </span></p>
<p><span style="font-family:'Times New Roman';font-size:18px;">ReceiverSchedulingPolicy：这个类就是Receiver调度策略。对于调度Receiver有两个阶段：</span></p>
<p><span style="font-family:'Times New Roman';font-size:18px;">第一阶段：当ReceiverTracker启动的时候开始进行全局的调度，即启动所有的Receiver</span></p>
<p><span style="font-family:'Times New Roman';font-size:18px;">第二阶段：是本地调度，当Receiver重启的时候，这儿有两种情况：</span></p>
<p><span style="font-family:'Times New Roman';font-size:18px;"># case 1: 实际的location和调度的location不匹配，换句话说，从建议的或者候选的scheduleReceiver中获取一个Receiver启动失败，ReceiverTracker应该首先选择调度的location列表中仍然存活的Executor,染回使用它们发起Receiver</span></p>
<p><span style="font-family:'Times New Roman';font-size:18px;"># case 2: 因为没有调度的location列表而重启，或者有调度的location列表，但是里面的Executors都挂了，ReceiverTracker应该调用方法</span></p>
<p><span style="font-family:'Times New Roman';font-size:18px;">rescheduleReceiver</span></p>
<p><span style="font-family:'Times New Roman';font-size:18px;"><br>
4.1 scheduleReceivers</span></p>
<p><span style="font-family:'Times New Roman';font-size:18px;"># 对待分配的executors按照host进行分组</span></p>
<p><span style="font-family:'Times New Roman';font-size:18px;"># 构建一个receivers长度的数组，存放着TaskLocation数组，相当于每一个receiver都对应着一组候选的TaskLocation</span></p>
<p><span style="font-family:'Times New Roman';font-size:18px;"># 遍历receiver的首选位置, 判断该host是否是未知的host，如果不是选择一个有较少的receiver的executor,然后放入location中。如果是未知的则创建一个TaskLocation放在对应的位置上</span></p>
<p><span style="font-family:'Times New Roman';font-size:18px;"># 对于那些不存在首选位置的Receiver,即locations里面为空的数组，根据numReceiversOnExecutor获取receiver数量最少的元素；该数组添加这个最少receiver数量的executor，并且更新对应的receiver的数量</span></p>
<p><span style="font-family:'Times New Roman';font-size:18px;"># 然后将receivers和location对应的TaskLocation数组进行拉链操作</span></p>
<p><span style="font-family:'Times New Roman';font-size:18px;"> </span></p>
<p><span style="font-family:'Times New Roman';font-size:18px;">4.2 rescheduleReceiver</span></p>
<p><span style="font-family:'Times New Roman';font-size:18px;"># 创建一个location集合，首先把首选位置映射成TaskLocation添加到scheduledLocations</span></p>
<p><span style="font-family:'Times New Roman';font-size:18px;"># 尝试转化一个receiver tracking info为executor 权重,然后转化成一个map</span></p>
<p><span style="font-family:'Times New Roman';font-size:18px;"># 获取空闲的executor，即没有运行也没有分配receiver运行的executor</span></p>
<p><span style="font-family:'Times New Roman';font-size:18px;"># 如果存在空闲executor,将空闲的executors更新到scheduledLocations</span></p>
<p><span style="font-family:'Times New Roman';font-size:18px;"># 如果不存在，则按照权重排序</span></p>
<br>            </div>
                </div>