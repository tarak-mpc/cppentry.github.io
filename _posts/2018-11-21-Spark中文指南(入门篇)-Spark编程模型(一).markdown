---
layout:     post
title:      Spark中文指南(入门篇)-Spark编程模型(一)
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/u010994304/article/details/51770563				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2 id="前言">前言</h2>

<blockquote>
   本章将对Spark做一个简单的介绍，更多教程请参考：<a href="" rel="nofollow">Spark教程</a> 
</blockquote>

<h2 id="本章知识点概括">本章知识点概括</h2>

<ul>
<li>Apache Spark简介</li>
<li>Spark的四种运行模式</li>
<li>Spark基于Standlone的运行流程</li>
<li>Spark基于YARN的运行流程</li>
</ul>



<h2 id="apache-spark是什么">Apache Spark是什么？</h2>

<p>Spark是一个用来实现快速而通用的集群计算的平台。扩展了广泛使用的MapReduce计算模型，而且高效地支持更多的计算模式，包括交互式查询和流处理。在处理大规模数据集的时候，速度是非常重要的。Spark的一个重要特点就是能够在内存中计算，因而更快。即使在磁盘上进行的复杂计算，Spark依然比MapReduce更加高效。</p>



<h2 id="spark重要概念">Spark重要概念</h2>



<h3 id="1spark运行模式">（1）Spark运行模式</h3>

<p><span>目前Spark的运行模式主要有以下几种:</span>  </p>

<ul>
<li><code>local</code>:主要用于开发调试Spark应用程序  </li>
<li><code>Standlone</code>:利用Spark自带的资源管理与调度器运行Spark集群，采用Master/Slave结构，为解决单点故障，可以采用Xookeeper实现高可靠(High Availability, HA)  </li>
<li><code>Apache Mesos</code>:运行在著名的Mesos资源管理框架基础之上，该集群运行模式将资源管理管理交给Mesos,Spark只负责运行任务调度和计算  </li>
<li><code>Hadoop YARN</code>:集群运行在Yarn资源管理器上，资源管理交给YARN，Spark只负责进行任务调度和计算 <br>
Spark运行模式中Hadoop YARN的集群方式最为常用，前面一章关于<a href="" rel="nofollow">Spark集群搭建</a>就是采用的YARN模式。  </li>
</ul>



<h3 id="2spark组件components">（2）Spark组件(Components)</h3>

<p>一个完整的Spark应用程序，如前面一章当中的SparkWorkdCount程序，在提交集群运行时，它涉及到如下图所示的组件：  </p>

<p><img src="http://images2015.cnblogs.com/blog/633343/201606/633343-20160627190032046-1376896570.jpg" alt="Spark组件" title=""></p>

<p>每个Spark应用都由一个驱动器程序(drive program)来发起集群上的各种并行操作。驱动器程序包含应用的main函数，驱动器负责创建SparkContext,SparkContext可以与不同种类的集群资源管理器(Cluster Manager)，例如Hadoop YARN，Mesos进行通信，获取到集群进行所需的资源后，SparkContext将 <br>
得到集群中工作节点(Worker Node)上对应的Executor(不同的Spark程序有不同的Executor,他们之间是相互独立的进程，Executor为应用程序提供分布式计算以及数据存储功能)，之后SparkContext将应用程序代码发送到各Executor,最后将任务(Task)分配给executors执行</p>

<ul>
<li>ClusterManager:在Standalone模式中即为Master节点（主节点），控制整个集群，监控Worker.在YARN中为ResourceManager  </li>
<li>Worker:从节点，负责控制计算节点，启动Executor或Driver。在YARN模式中为NodeManager,负责计算节点的控制。  </li>
<li>Driver:运行Application的main()函数并创建SparkContect。  </li>
<li>Executor:执行器，在worker node上执行任务的组件、用于启动线程池运行任务。每个Application拥有独立的一组Executor。  </li>
<li>SparkContext:整个应用的上下文，控制应用的生命周期。  </li>
<li>RDD：Spark的计算单元，一组RDD可形成执行的有向无环图RDD Graph。  </li>
<li>DAG Scheduler:根据作业(Job)构建基于Stage的DAG，并提交Stage给TaskScheduler。  </li>
<li>TaskScheduler:将任务(Task)分发给Executor。  </li>
<li>SparkEnv:线程级别的上下文，存储运行时的重要组件的引用。 <br>
SparkEnv内构建并包含如下一些重要组件的引用。 <br>
1、MapOutPutTracker:负责Shuffle元信息的存储。 <br>
2、BroadcastManager:负责广播变量的控制与元信息的存储。 <br>
3、BlockManager:负责存储管理、创建和查找快。 <br>
4、MetricsSystem:监控运行时性能指标信息。 <br>
5、SparkConf:负责存储配置信息。  </li>
</ul>



<h2 id="spark的整体流程">Spark的整体流程</h2>

<pre><code>1、Client提交应用。  
2、Master找到一个Worker启动Driver  
3、Driver向Master或者资源管理器申请资源，之后将应用转化为RDD Graph  
4、再由DAGSchedule将RDD Graph转化为Stage的有向无环图提交给TaskSchedule。  
5、再由TaskSchedule提交任务给Executor执行。  
6、其它组件协同工作，确保整个应用顺利执行。  
</code></pre>

<p>图片：</p>

<p><img src="http://images2015.cnblogs.com/blog/633343/201606/633343-20160627190319921-1626468332.png" alt="Spark有向无环图" title=""></p>



<h2 id="spark-on-yarn流程">Spark on Yarn流程：</h2>

<pre><code>1、基于YARN的Spark作业首先由客户端生成作业信息，提交给ResourceManager。  
2、ResourceManager在某一NodeManager汇报时把AppMaster分配给NodeManager。  
3、NodeManager启动SparkAppMaster。
4、SparkAppMastere启动后初始化然后向ResourceManager申请资源。  
5、申请到资源后，SparkAppMaster通过RPC让NodeManager启动相应的SparkExecutor。  
6、SparkExecutor向SparkAppMaster汇报并完成相应的任务。  
7、SparkClient会通过AppMaster获取作业运行状态。  
</code></pre>

<p><img src="http://images2015.cnblogs.com/blog/633343/201606/633343-20160627190358046-563157254.png" alt="SparkOnYARN" title=""></p>



<h2 id="参考文档">参考文档</h2>

<ul>
<li>[1]<a href="http://spark.apache.org/docs/latest/cluster-overview.html" rel="nofollow">Spark官方文档</a></li>
<li>[2]<a href="http://blog.csdn.net/lovehuangjiaju/article/details/48580863" rel="nofollow">Spark修炼之道（进阶篇）——Spark入门到精通：第四节 Spark编程模型（一)</a></li>
</ul>



<h2 id="问题">问题</h2>

<ul>
<li>针对SparkContext和Drive program还没有解释清楚</li>
<li>关于Driver向Master请求资源这一块还没搞懂</li>
<li>关于Spark的整体流程图还是不太准确,以后找到好的再补上</li>
</ul>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>