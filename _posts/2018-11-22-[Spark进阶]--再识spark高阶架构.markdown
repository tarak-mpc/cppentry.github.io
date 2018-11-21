---
layout:     post
title:      [Spark进阶]--再识spark高阶架构
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：尊重原创，转载请标明"本文转自:https://blog.csdn.net/high2011"					https://blog.csdn.net/high2011/article/details/83895232				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>Spark EcoSystem几乎都是以 Spark Core为核心而构建起来的，那么，先看看 Spark Core的高阶架构：</p>

<p><img alt="" class="has" height="704" src="https://img-blog.csdnimg.cn/20181109113659689.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hpZ2gyMDEx,size_16,color_FFFFFF,t_70" width="528"></p>

<h1>分别介绍下几个概念</h1>

<p><strong>1、Driver Programs</strong><br>
        A driver program is an application that uses Spark as a library. It provides the data processing code that  Spark executes on the worker nodes. A driver program can launch one or more jobs on a Spark cluster.</p>

<p><strong>2、Executors</strong><br>
       An executor is a JVM (Java virtual machine) process that Spark creates on each worker for an application.  It executes application code concurrently in multiple threads. It can also cache data in memory or disk.<br>
An executor has the same lifespan as the application for which it is created. When a Spark application  terminates, all the executors created for it also terminate.</p>

<p><strong>3、Tasks</strong><br>
      A task is the smallest unit of work that Spark sends to an executor. It is executed by a thread in an executor  on a worker node. Each task performs some computations to either return a result to a driver program or  partition its output for shuffle.<br>
Spark creates a task per data partition. An executor runs one or more tasks concurrently. The amount  of parallelism is determined by the number of partitions. More partitions mean more tasks processing data  in parallel.<br>
 </p>

<h1><strong>Application Execution</strong></h1>

<p><br>
    This section briefly describes how data processing code is executed on a Spark cluster.</p>

<p><strong>Terminology</strong><br>
    Let’s define a few terms first:<br>
    Shuffle. A shuffle redistributes data among a cluster of nodes. It is an expensive  operation because it involves moving data across a network. Note that a shuffle does  not randomly redistribute data; it groups data elements into buckets based on some  criteria. Each bucket forms a new partition.</p>

<p>    Job. A job is a set of computations that Spark performs to return results to a driver  program. Essentially, it is an execution of a data processing algorithm on a Spark  cluster. An application can launch multiple jobs. Exactly how a job is executed is  covered later in this chapter.</p>

<p>    Stage. A stage is a collection of tasks. Spark splits a job into a DAG of stages. A stage  may depend on another stage. For example, a job may be split into two stages, stage  0 and stage 1, where stage 1 cannot begin until stage 0 is completed. Spark groups  tasks into stages using shuffle boundaries. Tasks that do not require a shuffle are  grouped into the same stage. A task that requires its input data to be shuffled begins  a new stage.</p>

<h1><strong>How an Application Works</strong></h1>

<p><br>
    With the definitions out of the way, I can now describe how a Spark application processes data in parallel  across a cluster of nodes. When a Spark application is run, Spark connects to a cluster manager and acquires  executors on the worker nodes. As mentioned earlier, a Spark application submits a data processing  algorithm as a job. Spark splits a job into a directed acyclic graph (DAG) of stages. It then schedules the  execution of these stages on the executors using a low-level scheduler provided by a cluster manager. The  executors run the tasks submitted by Spark in parallel.</p>

<p>    Every Spark application gets its own set of executors on the worker nodes. This design provides a few  benefits. <br>
    First, tasks from different applications are isolated from each other since they run in different  JVM processes. A misbehaving task from one application cannot crash another Spark application. </p>

<p>    Second,scheduling of tasks becomes easier. Spark has to schedule the tasks belonging to only one application at a time.  It does not have to handle the complexities of scheduling tasks from multiple concurrently running applications.<br>
    However, this design also has one disadvantage. Since applications run in separate JVM processes, they  cannot easily share data. <br>
    Even though they may be running on the same worker nodes, they cannot share  data without writing it to disk. <br>
    As previously mentioned, writing and reading data from disk are expensive  operations. Therefore, applications sharing data through disk will experience performance issues.<br>
 </p>

<h1>总结如下</h1>

<ol><li>一个物理节点可以有一个或多个worker</li>
	<li>一个worker中可以有一个或多个executor</li>
	<li>一个executor拥有多个cpu core和memory</li>
	<li>仅shuffle (把一组无规则的数据尽量转换成一组具有一定规则的数据)操作时才算作一个stage</li>
	<li>一个partition对应一个task</li>
</ol><p> </p>            </div>
                </div>