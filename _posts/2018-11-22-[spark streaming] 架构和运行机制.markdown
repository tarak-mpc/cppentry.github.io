---
layout:     post
title:      [spark streaming] 架构和运行机制
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p><strong>本期内容：</strong></p>

<p><strong>　　1. Spark Streaming架构</strong></p>

<p><strong>　　2. Spark Streaming运行机制</strong></p>

<p><strong>       3.解密Spark Streaming Job架构和运行机制</strong></p>

<p><strong>       4.解密Spark Streaming 容错架构和运行机制</strong></p>

<p>　　Spark<a href="http://www.thebigdata.cn/" rel="nofollow">大数据</a>分析框架的核心部件： spark Core、spark  Streaming流计算、GraphX图计算、MLlib机器学习、Spark SQL、Tachyon文件系统、SparkR计算引擎等主要部件.</p>

<p>　　<img alt="" class="has" src="https://images2015.cnblogs.com/blog/948437/201605/948437-20160503161530841-167128522.png"></p>

<p>　　Spark Streaming 其实是构建在spark core之上的一个应用程序，要构建一个强大的Spark应用程序 ，spark  Streaming是一个值得借鉴的参考，spark  Streaming涉及多个job交叉配合，基本涉及到了spark的所有的核心组件，精通掌握spark streaming是至关重要的。</p>

<p> </p>

<p>　　<strong>Spark Streaming基础概念理解：</strong></p>

<p>　　　　1.<strong> 离散流</strong>：(Discretized Stream ,DStream):这是spark streaming对内部的持续的实时数据流的抽象描述，也即我们处理的一个实时数据流，在spark streaming中对应一个DStream ；</p>

<p>　　　　2. <strong>批数据</strong>：将实时流时间以时间为单位进行分批，将数据处理转化为时间片数据的批处理；</p>

<p>　　　　3. <strong>时间片或者批处理时间间隔：</strong>逻辑级别的对数据进行定量的标准，以时间片作为拆分流数据的依据；</p>

<p>　　　　4. 窗口长度：一个窗口覆盖的流数据的时间长度。比如说要每隔5分钟统计过去30分钟的数据，窗口长度为6，因为30分钟是batch interval 的6倍；</p>

<p>　　　　5. <strong>滑动时间间隔</strong>：比如说要每隔5分钟统计过去30分钟的数据，窗口时间间隔为5分钟；</p>

<p>　　　　6. input DStream :一个inputDStream是一个特殊的DStream 将spark streaming连接到一个外部数据源来读取数据。</p>

<p>　　　　7. <strong>Receiver </strong>:长时间（可能7*24小时）运行在Excutor之上，每个Receiver负责一个inuptDStream (比如读取一个kafka消息的输入流)。每个Receiver,加上inputDStream 会占用一个core/slot ；</p>

<p> 　　　</p>

<p>　　Spark Core处理的每一步都是基于RDD的，RDD之间有依赖关系。下图中的RDD的DAG显示的是有3个Action，会触发3个job，RDD自下向上依赖，RDD产生job就会具体的执行。从DSteam Graph中可以看到，DStream的逻辑与RDD基本一致，它就是在RDD的基础上加上了时间的依赖。RDD的DAG又可以叫空间维度，也就是说整个Spark Streaming多了一个时间维度，也可以成为时空维度。</p>

<p>　　<img alt="" class="has" src="https://images2015.cnblogs.com/blog/948437/201605/948437-20160503153415294-1829221977.png"></p>

<p> </p>

<p>　　从这个角度来讲，可以将Spark Streaming放在坐标系中。其中Y轴就是对RDD的操作，RDD的依赖关系构成了整个job的逻辑，而X轴就是时间。随着时间的流逝，固定的时间间隔（Batch Interval）就会生成一个job实例，进而在集群中运行。</p>

<p><img alt="" class="has" src="https://images2015.cnblogs.com/blog/948437/201605/948437-20160503153544622-353722546.png"></p>

<p>　　对于Spark Streaming来说，当不同的数据来源的数据流进来的时候，基于固定的时间间隔，会形成一系列固定不变的数据集或event集合（例如来自flume和kafka）。而这正好与RDD基于固定的数据集不谋而合，事实上，由DStream基于固定的时间间隔行程的RDD Graph正是基于某一个batch的数据集的。</p>

<p>　　从上图中可以看出，在每一个Batch上，空间维度的RDD依赖关系都是一样的，不同的是这个五个Batch流入的数据规模和内容不一样，所以说生成的是不同的RDD依赖关系的实例，所以说RDD的Graph脱胎于DStream的Graph，也就是说DStream就是RDD的模板，不同的时间间隔，生成不同的RDD Graph实例。</p>

<p> </p>

<p>　　从源码解读DStream :</p>

<p>　　<img alt="" class="has" src="https://images2015.cnblogs.com/blog/948437/201605/948437-20160503153903794-975518658.png"></p>

<p>　　从这里可以看出，DStream就是Spark Streaming的核心，就想Spark Core的核心是RDD，它也有dependency和compute。更为关键的是下面的代码：</p>

<p><img alt="" class="has" src="https://images2015.cnblogs.com/blog/948437/201605/948437-20160503153952701-11495926.png"></p>

<p>　　这是一个HashMap，以时间为key，以RDD为Value，这也正应证了随着时间流逝，不断的生成RDD，产生依赖关系的job，并通过JbScheduler在集群上运行。再次验证了DStream就是RDD的模版。</p>

<p>　　DStream可以说是逻辑级别的，RDD就是物理级别的，DStream所表达的最终都是通过RDD的转化实现的。前者是更高级别的抽象，后者是底层的实现。DStream实际上就是在时间维度上对RDD集合的封装，DStream与RDD的关系就是随着时间流逝不断的产生RDD，对DStream的操作就是在固定时间上操作RDD。</p>

<p> </p>

<p>　　总结：</p>

<p>　　　　在空间维度上的业务逻辑作用于<strong>DStream</strong>，随着时间的流逝，每个Batch Interval形成了具体的数据集，产生了<strong>RDD</strong>，对RDD进行Transform操作，进而形成了RDD的依赖关系RDD DAG，形成Job。然后JobScheduler根据时间调度，基于RDD的依赖关系，把作业发布到Spark Cluster上去运行，不断的产生Spark作业。</p>

<h1>一、<strong>解密Spark Streaming Job架构和运行机制</strong></h1>

<p><strong>通过代码洞察Job的执行过程：</strong></p>

<table border="0" cellpadding="0" cellspacing="0"><tbody><tr><td>
			<p>1</p>

			<p>2</p>

			<p>3</p>

			<p>4</p>

			<p>5</p>

			<p>6</p>

			<p>7</p>

			<p>8</p>

			<p>9</p>

			<p>10</p>

			<p>11</p>

			<p>12</p>

			<p>13</p>

			<p>14</p>

			<p>15</p>

			<p>16</p>

			<p>17</p>

			<p>18</p>

			<p>19</p>

			<p>20</p>

			<p>21</p>

			<p>22</p>

			<p>23</p>

			<p>24</p>

			<p>25</p>

			<p>26</p>

			<p>27</p>

			<p>28</p>

			<p>29</p>

			<p>30</p>

			<p>31</p>

			<p>32</p>

			<p>33</p>

			<p>34</p>

			<p>35</p>
			</td>
			<td>
			<p><code>object</code> <code>OnlineForeachRDD</code><code>2</code><code>DB {</code></p>

			<p><code>  </code><code>def</code> <code>main(args</code><code>:</code> <code>Array[String]){</code></p>

			<p><code>    </code><code>/*</code></p>

			<p><code>      </code><code>* 第1步：创建Spark的配置对象SparkConf，设置Spark程序的运行时的配置信息</code></p>

			<p><code>      </code><code>*/</code></p>

			<p><code>    </code><code>val</code> <code>conf </code><code>=</code> <code>new</code> <code>SparkConf() </code><code>//创建SparkConf对象</code></p>

			<p><code>    </code><code>conf.setAppName(</code><code>"OnlineForeachRDD"</code><code>) </code><code>//设置应用程序的名称</code></p>

			<p><code>//    conf.setMaster("spark://Master:7077") //此时，程序在Spark集群</code></p>

			<p><code>    </code><code>conf.setMaster(</code><code>"local[6]"</code><code>）</code></p>

			<p><code> </code> </p>

			<p><code>    </code><code>//设置batchDuration时间间隔来控制Job生成的频率并且创建Spark Streaming执行的入口</code></p>

			<p><code>    </code><code>val</code> <code>ssc </code><code>=</code> <code>new</code> <code>StreamingContext(conf, Seconds(</code><code>5</code><code>))</code></p>

			<p><code>  </code> </p>

			<p><code>    </code><code>val</code> <code>lines </code><code>=</code> <code>ssc.socketTextStream(</code><code>"Master"</code><code>, </code><code>9999</code><code>)</code></p>

			<p><code>  </code> </p>

			<p><code>    </code><code>val</code> <code>words </code><code>=</code> <code>lines.flatMap(</code><code>_</code><code>.split(</code><code>" "</code><code>))</code></p>

			<p><code>    </code><code>val</code> <code>wordCounts </code><code>=</code> <code>words.map(x </code><code>=</code><code>&gt; (x, </code><code>1</code><code>)).reduceByKey(</code><code>_</code> <code>+ </code><code>_</code><code>)</code></p>

			<p><code>    </code><code>wordCounts.foreachRDD { rdd </code><code>=</code><code>&gt;</code></p>

			<p><code>      </code><code>rdd.foreachPartition { partitionOfRecords </code><code>=</code><code>&gt; {</code></p>

			<p><code>        </code><code>// ConnectionPool is a static, lazily initialized pool of connections</code></p>

			<p><code>        </code><code>val</code> <code>connection </code><code>=</code> <code>ConnectionPool.getConnection()</code></p>

			<p><code>        </code><code>partitionOfRecords.foreach(record </code><code>=</code><code>&gt; {</code></p>

			<p><code>          </code><code>val</code> <code>sql </code><code>=</code> <code>"insert into streaming_itemcount(item,count) values('"</code> <code>+ record.</code><code>_</code><code>1</code> <code>+ </code><code>"',"</code> <code>+ record.</code><code>_</code><code>2</code> <code>+ </code><code>")"</code></p>

			<p><code>          </code><code>val</code> <code>stmt </code><code>=</code> <code>connection.createStatement();</code></p>

			<p><code>          </code><code>stmt.executeUpdate(sql);</code></p>

			<p><code>  </code> </p>

			<p><code>        </code><code>})</code></p>

			<p><code>        </code><code>ConnectionPool.returnConnection(connection)  </code><code>// return to the pool for future reuse</code></p>

			<p><code>      </code><code>}</code></p>

			<p><code>      </code><code>}</code></p>

			<p><code>    </code><code>}</code></p>

			<p><code>    </code><code>ssc.start()</code></p>

			<p><code>    </code><code>ssc.awaitTermination()</code></p>

			<p><code>  </code><code>}</code></p>

			<p><code>}</code></p>
			</td>
		</tr></tbody></table><p>通过观察Job在Spark集群上运行的Log和结合源代码分析出如下流程：</p>

<ol><li>创建SparkConf,设置Spark程序运行时的配置信息;</li>
	<li>创建StreamingContext，设置batchDuration时间间隔来控制Job生成的频率并且创建Spark Streaming执行的入口；</li>
	<li>在创建StreamingContext的过程中，会实例化JobScheduler和JobGenerator，调用StreamingContext的start方法时，在JobScheduler.start()内部实例化EventLoop，并执行EventLoop.start()进行消息循环，在JobScheduler的start方法内部会构造JobGenerator和ReveiverTracker,并分别调用它们的start方法；</li>
	<li>JobGenerator启动后会不断的根据batchDuration生成一个个的Job；</li>
	<li>ReceiverTracker启动后首先在Spark Cluster中启动Receiver，其实是在Executor中首先启动ReceiverSupervisor，Receiver收到数据后会通过ReceiverSupervisor存储到Executor并且把数据的Metadata信息发送给Driver中的ReceiverTracker，在ReceiverTracker内部会通过ReceivedBlockTracker来管理接受到的元数据信息(元数据：数据存储的位置、索引等)。</li>
	<li>
	<p>时间不断的流动，job怎么产生的？每个BatchInterval会产生一个具体的Job，其实这里的Job不是Spark Core中所指的Job，它只是基于DStreamGraph而生成的RDD 的DAG而已，从Java角度讲，相当于Runnable接口实例，此时要想运行Job需要提交给JobScheduler，在JobScheduler中通过线程池的方式找到一个单独的线程来提交Job到集群运行（其实是在线程中基于RDD的Action触发真正的作业的运行）；</p>
	</li>
</ol><p>      为什么使用线程池呢？</p>

<ol><li>作业不断生成，所以为了提升效率，我们需要线程池；这和在Executor中通过线程池执行Task有异曲同工之妙；</li>
	<li>有可能设置了Job的FAIR公平调度的方式，这个时候也需要多线程的支持；</li>
</ol><h1>二、<strong>解密Spark Streaming 容错架构和运行机制</strong></h1>

<p>        </p>

<p>      我们知道DStream与RDD的关系就是随着时间流逝不断的产生RDD，对DStream的操作就是在固定时间上操作RDD。所以从某种意义上而言，Spark Streaming的基于DStream的容错机制，实际上就是划分到每一次形成的RDD的容错机制，</p>

<p>      这也是Spark Streaming的高明之处。</p>

<p>      Spark Streaming的容错要考虑两个方面：</p>

<ol><li>
	<p>Driver运行失败时的恢复</p>

	<p>使用Checkpoint，记录Driver运行时的状态，失败后可以读取Checkpoint并恢复Driver状态。</p>
	</li>
	<li>
	<p>具体的每次Job运行失败时的恢复</p>

	<p>要考虑到Receiver的失败恢复，也要考虑到RDD计算失败的恢复。Receiver可以采用写wal日志的方式。RDD的容错是spark core天生提供的，基于RDD的特性，它的容错机制主要就是两种：</p>
	</li>
</ol><p>　　    01. 基于<strong>checkpoint</strong>；</p>

<p>              在stage之间，是宽依赖，产生了shuffle操作，lineage链条过于复杂和冗长，这时候就需要做checkpoint。</p>

<p>　　   02. 基于<strong>lineage</strong>（血统）的容错：</p>

<p>　　        一般而言，spark选择血统容错，因为对于大规模的数据集，做检查点的成本很高。考虑到RDD的依赖关系，每个stage内部都是窄依赖，此时一般基于lineage容错，方便高效。</p>            </div>
                </div>