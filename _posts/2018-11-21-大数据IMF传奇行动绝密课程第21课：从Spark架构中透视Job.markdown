---
layout:     post
title:      大数据IMF传奇行动绝密课程第21课：从Spark架构中透视Job
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="从spark架构中透视job">从Spark架构中透视Job</h1>

<p>1、通过案例观察Spark架构 <br>
2、手动绘制Spark内部架构 <br>
3、Spark Job的逻辑视图解析 <br>
4、Spark Job的物理视图解析</p>

<p>spark粗粒度</p>

<p>Spark主从分布： <br>
Master <br>
Worker</p>

<p>在配置文件spark-env.sh中有内存、core的配置 <br>
spark-shell默认情况下没有任何job <br>
Executors个数有5个，其中4个在worker中，driver也是一个Executor进程。 <br>
当前的spark application分配一个ExecutorBackend进程</p>

<p>一个Worker上可以有多个Executor，如：cpu利用率不高，可配多个Executor <br>
application关闭Executor才会关闭 <br>
spark中，一旦程序启动，无论程序内部有多少作业，都是复用程序注册时获得的资源，具体分配资源的时候默认分配的方式在每个Worker上为当前程序分配一个ExecutorBackend进程，且默认情况下会最大化的使用cores和memory</p>

<p>Worker中，CoarseGrainedExecutorBackend里面有Executor，并发线程池线程复用的方式不断运行Task <br>
在一个Executor中一次性最多能运行多少并发的Task取决于当前Executor能够使用的cores的数量</p>

<p>应对OOM一个方法：多个Executor（如果有其他大数据平台）、增加分片数量（只有spark）</p>

<p>88个任务具体分配个谁主要取决于数据本地性</p>

<p>并行度也是被继承的</p>

<p>由于线程不关心具体Task中运行什么代码，所以Task和Thread是解耦和的，所以Thread是可以被复用的</p>

<p>当Spark集群启动的时候，首先启动Master进程负责整个集群资源的管理和分配，并接受作业的提交，且为作业分配计算资源。每个工作节点默认情况下都会启动一个Worker Process来管理当前结点的Memory、CPU等计算资源，并且向Master汇报Worker还能够正常工作（只是通过心跳），当用户提交作业给Master的时候，Master会为程序分配id，并分配计算资源，默认情况下，会为当前的应用程序在每个Worker Process下面分配一个CoarseGrainedExecutorBackend进程，该进程默认情况下会最大化的使用当前结点上的内存和cpu</p>

<p>整个集群分为Master和Worker结点，Master有Master守护进程，需要管理所有的Worker结点。Worker结点在Worker Node的守护进程会负责管理Executors。Worker Process根据Master的要求来管理资源。从CoarseGrainedExecutorBackend上看，是Worker Process在管理资源</p>

<p>Worker Process管理当前结点的内存和cpu等计算资源，实质上是通过Master来管理每台机器上的计算资源的</p>

<p>Worker节点上有Worker Process。 Worker Process会接受Master的指令为当前要运行的应用程序分配CoarseGrainedExecutorBackend进程</p>

<p>Worker Process 默认情况下和Executor一对一，可以一对多</p>

<p>Stage0是Stage1的Mapper，Stage1是Stage2的Mapper，Stage1是Stage0的Reducer，Stage2是Stage1的Reducer。 <br>
Spark是一个更加精致和高效的MapReduce思想的具体实现</p>

<p>最后一个Stage里面的Task是ResultTask类型，前面所有的Stage中Task的类型都是ShuffleMapTask类型</p>

<p>Stage里面的内容一定是在Executor中执行的，而且Stage必须从前往后执行。</p>

<p>Spark的一个应用程序中可以因为一个不同的Action产生众多的Job，每个Job至少有一个Stage</p>

<p><img title="" alt="图21-1 Spark内部架构" src="https://img-blog.csdn.net/20160811235138174"></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>