---
layout:     post
title:      【Python3实战Spark大数据分析及调度】Spark Core 课程笔记（1）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>课程地址：<a href="https://coding.imooc.com/learn/list/249.html" rel="nofollow">https://coding.imooc.com/learn/list/249.html</a></p>

<hr><p id="main-toc"><strong>目录</strong></p>

<p id="Spark%20Core%3A%20Spark%20%E6%A0%B8%E5%BF%83%E8%BF%9B%E9%98%B6-toc" style="margin-left:0px;"><a href="#Spark%20Core%3A%20Spark%20%E6%A0%B8%E5%BF%83%E8%BF%9B%E9%98%B6" rel="nofollow">Spark Core: Spark 核心进阶</a></p>

<p id="main-toc-toc" style="margin-left:40px;"><a href="#main-toc" rel="nofollow">Spark 核心概念</a></p>

<p id="Spark%E8%BF%90%E8%A1%8C%E6%9E%B6%E6%9E%84%E5%8F%8A%E6%B3%A8%E6%84%8F%E4%BA%8B%E9%A1%B9-toc" style="margin-left:40px;"><a href="#Spark%E8%BF%90%E8%A1%8C%E6%9E%B6%E6%9E%84%E5%8F%8A%E6%B3%A8%E6%84%8F%E4%BA%8B%E9%A1%B9" rel="nofollow">Spark运行架构及注意事项</a></p>

<p id="%E6%9E%B6%E6%9E%84-toc" style="margin-left:80px;"><a href="#%E6%9E%B6%E6%9E%84" rel="nofollow">架构</a></p>

<p id="%E6%B3%A8%E6%84%8F%E4%BA%8B%E9%A1%B9-toc" style="margin-left:80px;"><a href="#%E6%B3%A8%E6%84%8F%E4%BA%8B%E9%A1%B9" rel="nofollow">注意事项</a></p>

<p id="Spark%E5%92%8CHadoop%E9%87%8D%E8%A6%81%E6%A6%82%E5%BF%B5%E5%8C%BA%E5%88%86-toc" style="margin-left:40px;"><a href="#Spark%E5%92%8CHadoop%E9%87%8D%E8%A6%81%E6%A6%82%E5%BF%B5%E5%8C%BA%E5%88%86" rel="nofollow">Spark和Hadoop重要概念区分</a></p>

<hr id="hr-toc"><h1> </h1>

<h1 id="Spark%20Core%3A%20Spark%20%E6%A0%B8%E5%BF%83%E8%BF%9B%E9%98%B6">Spark Core: Spark 核心进阶</h1>

<h2>Spark 核心概念</h2>

<p><a href="https://spark.apache.org/docs/latest/cluster-overview.html" rel="nofollow">https://spark.apache.org/docs/latest/cluster-overview.html</a></p>

<table><tbody><tr><td>Application</td>
			<td>
			<p>User program built on Spark. Consists of a <em>driver program</em> and <em>executors</em> on the cluster.</p>

			<p><span style="color:#3399ea;">基于spark的应用程序 = 1 driver + executors</span></p>
			</td>
		</tr><tr><td>Application jar</td>
			<td>
			<p>A jar containing the user's Spark application. In some cases, users will want to create an "uber jar" containing their application along with its dependencies. The user's jar should never include Hadoop or Spark libraries, however, these will be added at runtime.</p>

			<p><span style="color:#3399ea;">针对jave/scala, pyhton没有</span></p>
			</td>
		</tr><tr><td>Driver program</td>
			<td>
			<p>The process running the <span style="color:#3399ea;">main() </span>function of the application and <span style="color:#3399ea;">creating the SparkContext</span></p>
			</td>
		</tr><tr><td>Cluster manager</td>
			<td>
			<p>An <span style="color:#3399ea;">external service</span> for acquiring resources on the cluster (e.g. <span style="color:#3399ea;">standalone manager, Mesos, YARN</span>)</p>

			<p><span style="color:#3399ea;">spark-submit --master local[2]/spark://hadoop000:7077/yarn</span></p>
			</td>
		</tr><tr><td>Deploy mode</td>
			<td>
			<p>Distinguishes where the driver process runs. In "cluster" mode, the framework launches the driver inside of the cluster. In "client" mode, the submitter launches the driver outside of the cluster.</p>

			<p><span style="color:#3399ea;">driver运行在哪里，client:本地 cluster:集群</span></p>
			</td>
		</tr><tr><td>Worker node</td>
			<td>
			<p>Any node that can run application code in the cluster</p>
			</td>
		</tr><tr><td>Executor</td>
			<td>
			<p>A <span style="color:#3399ea;">process</span> launched for an application on a worker node, that runs tasks and keeps data in memory or disk storage across them.<span style="color:#3399ea;"> Each application has its own executors.</span></p>
			</td>
		</tr><tr><td>Task</td>
			<td>A unit of work that will be sent to one executor</td>
		</tr><tr><td>Job</td>
			<td>A parallel computation consisting of multiple tasks that gets spawned in response to a Spark <span style="color:#3399ea;">action</span> (e.g. <code>save</code>, <code>collect</code>); you'll see this term used in the driver's logs.</td>
		</tr><tr><td>Stage</td>
			<td><span style="color:#3399ea;">Each job gets divided into smaller sets of <em>stages</em></span> that depend on each other (similar to the map and reduce stages in MapReduce); you'll see this term used in the driver's logs.</td>
		</tr></tbody></table><p><strong>Job, stage 和task的关系</strong></p>

<p><span style="color:#f33b45;">1 action = 1 job = x stages, 1 stage = n tasks (1 task on 1 worker node) (线程) &lt;=&gt; n executors(进程)</span></p>

<p> </p>

<h2 id="Spark%E8%BF%90%E8%A1%8C%E6%9E%B6%E6%9E%84%E5%8F%8A%E6%B3%A8%E6%84%8F%E4%BA%8B%E9%A1%B9">Spark运行架构及注意事项</h2>

<h3 id="%E6%9E%B6%E6%9E%84">架构</h3>

<p><img alt="" class="has" src="https://img-blog.csdnimg.cn/2018111417522096.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3poYW5nMTU5NTM3MDk5MTM=,size_16,color_FFFFFF,t_70"></p>

<p><span style="color:#3399ea;">Spark applications run as independent sets of processes on a cluster</span>, coordinated by the <span style="color:#3399ea;"><code>SparkContext</code></span> object in your <span style="color:#3399ea;">main</span> program (called the <em>driver program</em>). </p>

<p>Specifically, to run on a cluster, the SparkContext can connect to several types of <em>cluster managers</em> (either Spark’s own standalone cluster manager, Mesos or YARN), which allocate resources across applications. Once connected, Spark acquires <em>executors</em> on nodes in the cluster, which are processes that run computations and store data for your application. Next, it sends your application code (defined by JAR or Python files passed to SparkContext) to the executors. Finally, SparkContext sends <em>tasks</em> to the executors to run.</p>

<h3 id="%E6%B3%A8%E6%84%8F%E4%BA%8B%E9%A1%B9">注意事项</h3>

<p><span style="color:#f33b45;">There are several useful things to note about this architecture:</span></p>

<ol><li><span style="color:#3399ea;">Each application gets its own executor processes</span>, which stay up for the duration of the whole application and run tasks in multiple threads. <span style="color:#3399ea;">This has the benefit of isolating applications from each other</span>, on both the scheduling side (each driver schedules its own tasks) and executor side (tasks from different applications run in different JVMs).<span style="color:#f33b45;"> However, it also means that data cannot be shared across different Spark applications (instances of SparkContext)</span> without writing it to an external storage system.（一个application的data不能被其他的application用）</li>
	<li><span style="color:#3399ea;">Spark is agnostic（不感知的） to the underlying cluster manager. </span>As long as it can acquire executor processes, and these communicate with each other, it is relatively easy to run it even on a cluster manager that also supports other applications (e.g. Mesos/YARN). 写spark程序的时候不用在意它会运行在哪</li>
	<li><span style="color:#3399ea;">The driver program must listen for and accept incoming connections from its executors throughout its lifetime</span> (e.g., see <a href="https://spark.apache.org/docs/latest/configuration.html#networking" rel="nofollow">spark.driver.port in the network config section</a>). As such, the driver program must be network addressable from the worker nodes. driver要和executor能够通讯</li>
	<li><span style="color:#3399ea;">Because the driver schedules tasks on the cluster, it should be run close to the worker nodes, preferably on the same local area network</span>. If you’d like to send requests to the cluster remotely, it’s better to open an RPC to the driver and have it submit operations from nearby than to run a driver far away from the worker nodes.  Driver最好跟worker nodes在一个局域网里运行</li>
</ol><h2 id="Spark%E5%92%8CHadoop%E9%87%8D%E8%A6%81%E6%A6%82%E5%BF%B5%E5%8C%BA%E5%88%86">Spark和Hadoop重要概念区分</h2>

<p><img alt="" class="has" src="https://img-blog.csdnimg.cn/2018111418361717.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3poYW5nMTU5NTM3MDk5MTM=,size_16,color_FFFFFF,t_70"></p>

<p> </p>            </div>
                </div>