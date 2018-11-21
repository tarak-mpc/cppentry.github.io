---
layout:     post
title:      Spark Quick Start
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h3 id="一安装">一、安装</h3>

<p><a href="http://spark.apache.org/" rel="nofollow" target="_blank">spark下载&amp;解压</a></p>

<p><a href="http://spark.apache.org/docs/1.6.1/quick-start.html" rel="nofollow" target="_blank">Quick start</a></p>

<p><a href="http://spark.apache.org/docs/1.6.1/" rel="nofollow" target="_blank">Spark Overview</a></p>

<p><a href="http://spark.apache.org/docs/1.6.1/programming-guide.html#transformations" rel="nofollow" target="_blank">Spark Programming Guide</a></p>

<p><a href="http://spark.apache.org/docs/1.6.1/running-on-yarn.html" rel="nofollow" target="_blank">Running Spark on YARN</a></p>



<h3 id="二scala-shell">二、scala shell</h3>



<pre class="prettyprint"><code class=" hljs avrasm">./bin/spark-shell

scala&gt; val lines = sc<span class="hljs-preprocessor">.textFile</span>(<span class="hljs-string">"/Users/study/spark/derby.log"</span>)
<span class="hljs-label">lines:</span> org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.rdd</span><span class="hljs-preprocessor">.RDD</span>[String] = /Users/Documents/spark/derby<span class="hljs-preprocessor">.log</span> MapPartitionsRDD[<span class="hljs-number">1</span>] at textFile at &lt;console&gt;:<span class="hljs-number">27</span>

scala&gt; lines<span class="hljs-preprocessor">.count</span>()
<span class="hljs-label">res4:</span> Long = <span class="hljs-number">13</span>

scala&gt; lines<span class="hljs-preprocessor">.first</span>()

scala&gt; val sparkLine = lines<span class="hljs-preprocessor">.filter</span>(line =&gt; line<span class="hljs-preprocessor">.contains</span>(<span class="hljs-string">"spark"</span>))
<span class="hljs-label">sparkLine:</span> org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.rdd</span><span class="hljs-preprocessor">.RDD</span>[String] = MapPartitionsRDD[<span class="hljs-number">2</span>] at filter at &lt;console&gt;:<span class="hljs-number">29</span>

scala&gt; sparkLine<span class="hljs-preprocessor">.first</span>()
<span class="hljs-label">res7:</span> String = <span class="hljs-string">"on database directory /Users/Documents/spark/metastore_db with class loader org.apache.spark.sql.hive.client.IsolatedClientLoader$$anon$1@6bc8d8bd "</span>

scala&gt; sparkLine<span class="hljs-preprocessor">.count</span>()
<span class="hljs-label">res8:</span> Long = <span class="hljs-number">3</span>
</code></pre>



<h3 id="三核心概念介绍">三、核心概念介绍</h3>



<h4 id="31-宏观overview">3.1 宏观overview</h4>

<ul>
<li>Speed <br>
Run programs up to 100x faster than Hadoop MapReduce in memory, or 10x faster on disk.</li>
<li>Ease of Use <br>
Write applications quickly in Java, Scala, Python, R.</li>
<li>Generality <br>
Combine SQL, streaming, and complex analytics. <br>
<img src="http://note.youdao.com/yws/public/resource/2ac828482cacc7eb1b526d673dbf2bdd/xmlnote/AFF8734DC4BF40D4B5861626AF7482AF/22189" alt="image" title=""></li>
<li>Runs Everywhere <br>
Spark runs on Hadoop, Mesos, standalone, or in the cloud. It can access diverse data sources including HDFS, Cassandra, HBase, and S3. <br>
<a href="http://spark.apache.org/" rel="nofollow" target="_blank">引用源自Spark官网</a></li>
</ul>



<h4 id="32-spark知识点">3.2 spark知识点</h4>

<ul>
<li>Spark Core Api</li>
<li>Spark SQL </li>
<li>Spark Streaming 流式计算</li>
<li>Spark MLib 机器学习</li>
<li>Spark GraphX 并行的图计算</li>
<li>Spark的集群管理器（YARN、Mesos、自带的独立调度器）</li>
</ul>



<h4 id="33-任务运行过程">3.3 任务运行过程</h4>

<p>一个Spark应用 –&gt; 一个驱动器(driver program)节点 –&gt; 多个工作节点(worker node) <br>
一个工作节点 –&gt; 一个执行器(executor) –&gt; 多个并行任务(task)</p>

<p><img src="http://spark.apache.org/docs/1.6.1/img/cluster-overview.png" alt="image" title=""></p>



<h4 id="34-几个任务概念的区分">3.4 几个任务概念的区分</h4>

<p><a href="http://spark.apache.org/docs/1.6.1/cluster-overview.html" rel="nofollow" target="_blank">http://spark.apache.org/docs/1.6.1/cluster-overview.html</a></p>

<ul>
<li>job 一系列stage组成一个job，一个行动就是一个job</li>
<li>stage 一个job可以分为多个stage， stage划分的条件，shuffle或者行动操作</li>
<li>task executor上的最小任务单元称之为task，task是并行的，单个shuffle根据partition数划分成n个tasks</li>
</ul>

<table>
<thead>
<tr>
  <th>Term</th>
  <th>Meaning</th>
</tr>
</thead>
<tbody><tr>
  <td>Application</td>
  <td>User program built on Spark. Consists of a driver program and executors on the cluster.</td>
</tr>
<tr>
  <td>Application jar</td>
  <td>A jar containing the user’s Spark application. In some cases users will want to create an “uber jar” containing their application along with its dependencies. The user’s jar should never include Hadoop or Spark libraries, however, these will be added at runtime.</td>
</tr>
<tr>
  <td>Driver program</td>
  <td>The process running the main() function of the application and creating the SparkContext</td>
</tr>
<tr>
  <td>Cluster manager</td>
  <td>An external service for acquiring resources on the cluster (e.g. standalone manager, Mesos, YARN)</td>
</tr>
<tr>
  <td>Deploy mode</td>
  <td>Distinguishes where the driver process runs. In “cluster” mode, the framework launches the driver inside of the cluster. In “client” mode, the submitter launches the driver outside of the cluster.</td>
</tr>
<tr>
  <td>Worker node</td>
  <td>Any node that can run application code in the cluster</td>
</tr>
<tr>
  <td>Executor</td>
  <td>A process launched for an application on a worker node, that runs tasks and keeps data in memory or disk storage across them. Each application has its own executors.</td>
</tr>
<tr>
  <td>Task</td>
  <td>A unit of work that will be sent to one executor</td>
</tr>
<tr>
  <td>Job</td>
  <td>A parallel computation consisting of multiple tasks that gets spawned in response to a Spark action (e.g. save, collect); you’ll see this term used in the driver’s logs.</td>
</tr>
<tr>
  <td>Stage</td>
  <td>Each job gets divided into smaller sets of tasks called stages that depend on each other (similar to the map and reduce stages in MapReduce); you’ll see this term used in the driver’s logs.</td>
</tr>
</tbody></table>




<h3 id="四spark-core原理解析">四、Spark Core原理解析</h3>



<h4 id="41-架构">4.1 架构</h4>

<ul>
<li>Spark集群采用的是典型的主 / 从结构</li>
<li>一个Spark应用(application) = 一个中央协调驱动器(Driver)节点 + 多个执行任务的执行器(Executor)节点；</li>
<li>【Executor】负责并行的执行任务（task）、存储必要的RDD数据  </li>
<li>【Driver】负责任务拆分、任务调度</li>
<li>程序之间的RDD变换关系组成了一张逻辑上的DAG，程序运行时逻辑图将转换为物理执行过程，Driver在对任务划分的时候会将<strong>连续的映射转为流水线</strong>，将多个操作合并到同一个步骤（stage）中来，明显的例子</li>
</ul>



<pre class="prettyprint"><code class=" hljs avrasm">val local_lines = sc<span class="hljs-preprocessor">.textFile</span>(<span class="hljs-string">"XXX"</span>)
local_lines<span class="hljs-preprocessor">.first</span>()
<span class="hljs-preprocessor">#连续起来执行后只需要加载文件的第一行</span></code></pre>

<ul>
<li>Application -&gt; Jobs -&gt; stages -&gt; tasks</li>
</ul>



<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-number">1</span>) val local_lines = sc<span class="hljs-preprocessor">.textFile</span>(<span class="hljs-string">"XXX"</span>)
<span class="hljs-number">1.2</span>) val local_lines_1 = local_lines<span class="hljs-preprocessor">.map</span>(xxx)
<span class="hljs-number">2</span>) val local_lines_2 = sc<span class="hljs-preprocessor">.textFile</span>(<span class="hljs-string">"XXX"</span>)
<span class="hljs-number">3</span>) println(local_lines_1<span class="hljs-preprocessor">.union</span>(local_lines_2)) 

<span class="hljs-preprocessor"># 3)是一个job，可以拆分为1*）、2）两个stage</span>
<span class="hljs-preprocessor"># 每个stage可以分为并行的多个task</span></code></pre>

<ul>
<li>【集群管理器】启动执行器节点，某些特定情况(比如、–deloy-mode=cluster)下才会靠集群管理器来启动驱动器节点。程序启动的时候，驱动器程序与集群管理器通信申请资源启动执行器节点；程序结束的时候，驱动器程序终止执行器过程，并告诉集群管理器释放资源</li>
<li>集群管理器的主节点、从节点和Spark的驱动器、执行器节点是两个维度的概念； <br>


<blockquote>
  集群的主从表示集群的中心化和分布式的部分 <br>
  Spark的执行器、驱动器节点描述的是执行Spark程序的两种进程的节点 <br>
  二者没有关联性，所以即使在YARN的工作节点上，Spark也是可以跑执行器和驱动器进程的  </blockquote></li>
  </ul>
  


<h4 id="42-dagjobsstagetask详解">4.2 DAG、Jobs、Stage、Task详解</h4>






<pre class="prettyprint"><code class=" hljs livecodeserver"><span class="hljs-number">1</span>）val input = sc.textFile(<span class="hljs-string">"file:///tmp/input.txt"</span>)
<span class="hljs-number">2</span>）val tokenized = input.map(<span class="hljs-built_in">line</span> =&gt; <span class="hljs-built_in">line</span>.<span class="hljs-built_in">split</span>(<span class="hljs-string">" "</span>)).<span class="hljs-built_in">filter</span>(<span class="hljs-keyword">words</span> =&gt; <span class="hljs-keyword">words</span>.size&gt;<span class="hljs-number">0</span>)
<span class="hljs-number">3</span>）val counts = tokenized.map(<span class="hljs-keyword">words</span> =&gt; (<span class="hljs-keyword">words</span>(<span class="hljs-number">0</span>), <span class="hljs-number">1</span>)).reduceByKey((<span class="hljs-operator">a</span>,b) =&gt; <span class="hljs-operator">a</span>+b)
<span class="hljs-number">4</span>）counts.collect()

<span class="hljs-comment"># 每一个RDD都记录了父节点的关系</span>
scala&gt; input.toDebugString
res82: String =
(<span class="hljs-number">2</span>) <span class="hljs-built_in">file</span>:<span class="hljs-comment">///tmp/input.txt MapPartitionsRDD[62] at textFile at &lt;console&gt;:27 []</span>
 |  <span class="hljs-built_in">file</span>:<span class="hljs-comment">///tmp/input.txt HadoopRDD[61] at textFile at &lt;console&gt;:27 []</span>

scala&gt; tokenized.toDebugString
res83: String =
(<span class="hljs-number">2</span>) MapPartitionsRDD[<span class="hljs-number">64</span>] <span class="hljs-keyword">at</span> <span class="hljs-built_in">filter</span> <span class="hljs-keyword">at</span> &lt;console&gt;:<span class="hljs-number">29</span> []
 |  MapPartitionsRDD[<span class="hljs-number">63</span>] <span class="hljs-keyword">at</span> map <span class="hljs-keyword">at</span> &lt;console&gt;:<span class="hljs-number">29</span> []
 |  <span class="hljs-built_in">file</span>:<span class="hljs-comment">///tmp/input.txt MapPartitionsRDD[62] at textFile at &lt;console&gt;:27 []</span>
 |  <span class="hljs-built_in">file</span>:<span class="hljs-comment">///tmp/input.txt HadoopRDD[61] at textFile at &lt;console&gt;:27 []</span>

scala&gt; counts.toDebugString
res84: String =
(<span class="hljs-number">2</span>) ShuffledRDD[<span class="hljs-number">66</span>] <span class="hljs-keyword">at</span> reduceByKey <span class="hljs-keyword">at</span> &lt;console&gt;:<span class="hljs-number">31</span> []
 +-(<span class="hljs-number">2</span>) MapPartitionsRDD[<span class="hljs-number">65</span>] <span class="hljs-keyword">at</span> map <span class="hljs-keyword">at</span> &lt;console&gt;:<span class="hljs-number">31</span> []
    |  MapPartitionsRDD[<span class="hljs-number">64</span>] <span class="hljs-keyword">at</span> <span class="hljs-built_in">filter</span> <span class="hljs-keyword">at</span> &lt;console&gt;:<span class="hljs-number">29</span> []
    |  MapPartitionsRDD[<span class="hljs-number">63</span>] <span class="hljs-keyword">at</span> map <span class="hljs-keyword">at</span> &lt;console&gt;:<span class="hljs-number">29</span> []
    |  <span class="hljs-built_in">file</span>:<span class="hljs-comment">///tmp/input.txt MapPartitionsRDD[62] at textFile at &lt;console&gt;:27 []</span>
    |  <span class="hljs-built_in">file</span>:<span class="hljs-comment">///tmp/input.txt HadoopRDD[61] at textFile at &lt;console&gt;:27 []</span>

<span class="hljs-comment">## </span>
stage_1 : HadoopRDD <span class="hljs-comment">--&gt; MapPartitionsRDD --&gt; map --&gt; filter --&gt; map --&gt; reduceByKey</span>
stage_2 : collect</code></pre>


<ul>
<li>一个job（counts.collect()）被拆成了两个stages</li>
<li>在行动操作之前，一切都是逻辑的DAG，行动操作是真实的物理变化发生时</li>
<li>驱动器程序执行了“流水线操作”，将多个RDD合并要一起执行</li>
<li>系谱图是自下而上的查找，这意味着如果任何一个父RDD上已经有数据缓存，这条链路都将得到优化</li>
<li>Spark的执行流程：用户代码定义DAG - 行动操作将DAG转转义为执行计划 - 任务在集群中调度并执行</li>
</ul>



<h4 id="43-执行器节点内存分配">4.3 执行器节点内存分配</h4>

<ul>
<li>默认60% RDD存储</li>
</ul>



<pre class="prettyprint"><code class=" hljs erlang"><span class="hljs-function"><span class="hljs-title">cache</span><span class="hljs-params">()</span>
<span class="hljs-title">persist</span><span class="hljs-params">()</span></span></code></pre>

<ul>
<li><p>默认20% 数据清洗与聚合 <br>
缓存数据混洗的输出数据，存储聚合的中间结果，通过spark.shuffle.memoryFraction来限定内存占比</p></li>
<li><p>默认20% 用户代码 <br>
与代码中的中间数据存储，比如创建数组</p></li>
</ul>



<h4 id="44-容错性">4.4 容错性</h4>

<p>Spark会自动重新执行失败的 或 较慢的任务来应对有错误的或者比较慢的机器 <br>
Spark还可能会在一台新的节点上投机的执行一个新的重复任务，如果提前结束，则提前获取结果，因此一个方法可能被执行多次 <br>
所以spark的累加器并不一定准</p>



<h3 id="五rdd编程">五、RDD编程</h3>



<h4 id="51-rdd是什么">5.1 RDD是什么</h4>

<p>RDD（弹性分布式数据集、Resilient Distributed Dataset）是Spark的数据结构。RDD的行为只分为三种：创建、转化（产生一个新的RDD）、行动（对当前RDD进行统计）</p>



<h4 id="52-rdd创建">5.2 RDD创建</h4>



<pre class="prettyprint"><code class=" hljs fsharp"><span class="hljs-keyword">val</span> local_lines = sc.textFile(<span class="hljs-string">"file:///usr/local/opt/spark-1.6.1-bin-hadoop2.4/README.md"</span>)  
或者  
<span class="hljs-keyword">val</span> local_lines = sc.parallelize(List(<span class="hljs-string">"pandas"</span>, <span class="hljs-string">"i like pandas"</span>)</code></pre>



<h4 id="53-rdd的转化">5.3 RDD的转化</h4>

<p><img src="http://note.youdao.com/yws/public/resource/2ac828482cacc7eb1b526d673dbf2bdd/xmlnote/A05B62CFAB8A4D739BA1B4C12D04C24A/22322" alt="transformation" title=""> <br>
<img src="http://note.youdao.com/yws/public/resource/2ac828482cacc7eb1b526d673dbf2bdd/xmlnote/77567BF17B144D6385043D2D97CDBAFF/22324" alt="transformation" title=""> <br>
系谱图记录各个RDD之间的转换关系</p>



<h5 id="531-针对各个元素的转化">5.3.1 针对各个元素的转化</h5>



<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-preprocessor"># map() 针对每个元素一一对应的转换</span>
scala&gt; val numbers = sc<span class="hljs-preprocessor">.parallelize</span>(List(<span class="hljs-number">1</span>,<span class="hljs-number">2</span>,<span class="hljs-number">3</span>,<span class="hljs-number">4</span>))<span class="hljs-comment">;</span>
scala&gt; numbers<span class="hljs-preprocessor">.map</span>(<span class="hljs-built_in">x</span> =&gt; <span class="hljs-built_in">x</span>*<span class="hljs-built_in">x</span>)<span class="hljs-preprocessor">.collect</span>()<span class="hljs-preprocessor">.foreach</span>(println)
<span class="hljs-number">1</span>
<span class="hljs-number">4</span>
<span class="hljs-number">9</span>
<span class="hljs-number">16</span>

<span class="hljs-preprocessor"># filter() 针对每个元素的过滤选择</span>
scala&gt; val numbers = sc<span class="hljs-preprocessor">.parallelize</span>(List(<span class="hljs-number">1</span>,<span class="hljs-number">2</span>,<span class="hljs-number">3</span>,<span class="hljs-number">4</span>))<span class="hljs-comment">;</span>
scala&gt; numbers<span class="hljs-preprocessor">.filter</span>(<span class="hljs-built_in">x</span> =&gt; <span class="hljs-built_in">x</span>&gt;<span class="hljs-number">2</span>)<span class="hljs-preprocessor">.collect</span>()<span class="hljs-preprocessor">.foreach</span>(println)
<span class="hljs-number">3</span>
<span class="hljs-number">4</span>

<span class="hljs-preprocessor"># flatmap() 对每一个元素处理后放回同一个大集合，典型的例子：split</span>
scala&gt; val strings = sc<span class="hljs-preprocessor">.parallelize</span>(List(<span class="hljs-string">"huang#hai#feng"</span>, <span class="hljs-string">"zhong#guo"</span>, <span class="hljs-string">"huang#hai#feng"</span>))<span class="hljs-comment">;</span>
scala&gt; strings<span class="hljs-preprocessor">.flatMap</span>(<span class="hljs-built_in">x</span> =&gt; <span class="hljs-built_in">x</span><span class="hljs-preprocessor">.split</span>(<span class="hljs-string">"#"</span>))<span class="hljs-preprocessor">.collect</span>()<span class="hljs-preprocessor">.foreach</span>(println)
huang
hai
feng
zhong
guo
huang
hai
feng

<span class="hljs-preprocessor"># sample 采样，</span>
scala&gt; numbers<span class="hljs-preprocessor">.collect</span>()<span class="hljs-preprocessor">.foreach</span>(println)
<span class="hljs-number">1</span>
<span class="hljs-number">2</span>
<span class="hljs-number">3</span>
<span class="hljs-number">4</span>
<span class="hljs-preprocessor"># 每个位置按照随机种子，选 or 不选</span>
scala&gt; numbers<span class="hljs-preprocessor">.sample</span>(false, <span class="hljs-number">0.5</span>)<span class="hljs-preprocessor">.collect</span>()<span class="hljs-preprocessor">.foreach</span>(println)
<span class="hljs-number">1</span>
<span class="hljs-number">2</span>
<span class="hljs-number">4</span>
<span class="hljs-preprocessor"># 这个true，， 待理解</span>
scala&gt; numbers<span class="hljs-preprocessor">.sample</span>(true, <span class="hljs-number">0.5</span>)<span class="hljs-preprocessor">.collect</span>()<span class="hljs-preprocessor">.foreach</span>(println)
<span class="hljs-number">2</span>
<span class="hljs-number">3</span>
<span class="hljs-number">3</span>
<span class="hljs-number">4</span>
</code></pre>



<h5 id="532-伪集合操作">5.3.2 伪集合操作</h5>



<pre class="prettyprint"><code class=" hljs avrasm">scala&gt;  numbers<span class="hljs-preprocessor">.collect</span>()<span class="hljs-preprocessor">.foreach</span>(println)
<span class="hljs-number">1</span>
<span class="hljs-number">2</span>
<span class="hljs-number">3</span>
<span class="hljs-number">4</span>
scala&gt;  numbers_1<span class="hljs-preprocessor">.collect</span>()<span class="hljs-preprocessor">.foreach</span>(println)
<span class="hljs-number">3</span>
<span class="hljs-number">4</span>
<span class="hljs-number">5</span>
<span class="hljs-number">6</span>
<span class="hljs-preprocessor"># union 并集，允许重复元素</span>
scala&gt; numbers<span class="hljs-preprocessor">.union</span>(numbers_1)<span class="hljs-preprocessor">.collect</span>()<span class="hljs-preprocessor">.foreach</span>(println)
<span class="hljs-number">1</span>
<span class="hljs-number">2</span>
<span class="hljs-number">3</span>
<span class="hljs-number">4</span>
<span class="hljs-number">3</span>
<span class="hljs-number">4</span>
<span class="hljs-number">5</span>
<span class="hljs-number">6</span>
scala&gt; numbers<span class="hljs-preprocessor">.union</span>(numbers_1)<span class="hljs-preprocessor">.distinct</span>()<span class="hljs-preprocessor">.collect</span>()<span class="hljs-preprocessor">.foreach</span>(println)
<span class="hljs-number">4</span>
<span class="hljs-number">1</span>
<span class="hljs-number">5</span>
<span class="hljs-number">6</span>
<span class="hljs-number">2</span>
<span class="hljs-number">3</span>

<span class="hljs-preprocessor"># intersection交集 </span>
scala&gt; numbers<span class="hljs-preprocessor">.intersection</span>(numbers_1)<span class="hljs-preprocessor">.collect</span>()<span class="hljs-preprocessor">.foreach</span>(println)
<span class="hljs-number">4</span>
<span class="hljs-number">3</span>

<span class="hljs-preprocessor"># subtract差集</span>
scala&gt; numbers<span class="hljs-preprocessor">.subtract</span>(numbers_1)<span class="hljs-preprocessor">.collect</span>()<span class="hljs-preprocessor">.foreach</span>(println)
<span class="hljs-number">2</span>
<span class="hljs-number">1</span>

<span class="hljs-preprocessor"># cartesian笛卡尔乘积</span>
scala&gt; numbers<span class="hljs-preprocessor">.cartesian</span>(numbers_1)<span class="hljs-preprocessor">.collect</span>()<span class="hljs-preprocessor">.foreach</span>(println)
(<span class="hljs-number">1</span>,<span class="hljs-number">3</span>)
(<span class="hljs-number">1</span>,<span class="hljs-number">4</span>)
(<span class="hljs-number">2</span>,<span class="hljs-number">3</span>)
(<span class="hljs-number">2</span>,<span class="hljs-number">4</span>)
(<span class="hljs-number">1</span>,<span class="hljs-number">5</span>)
(<span class="hljs-number">1</span>,<span class="hljs-number">6</span>)
(<span class="hljs-number">2</span>,<span class="hljs-number">5</span>)
(<span class="hljs-number">2</span>,<span class="hljs-number">6</span>)
(<span class="hljs-number">3</span>,<span class="hljs-number">3</span>)
(<span class="hljs-number">3</span>,<span class="hljs-number">4</span>)
(<span class="hljs-number">4</span>,<span class="hljs-number">3</span>)
(<span class="hljs-number">4</span>,<span class="hljs-number">4</span>)
(<span class="hljs-number">3</span>,<span class="hljs-number">5</span>)
(<span class="hljs-number">3</span>,<span class="hljs-number">6</span>)
(<span class="hljs-number">4</span>,<span class="hljs-number">5</span>)
(<span class="hljs-number">4</span>,<span class="hljs-number">6</span>)</code></pre>



<h4 id="54-rdd的行动">5.4 RDD的行动</h4>

<p><img src="https://img-blog.csdn.net/20171102161628993?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaHVhbmdoYWlmZW5nMjAxMjEz/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="actions" title=""></p>



<pre class="prettyprint"><code class=" hljs coffeescript"><span class="hljs-comment"># reduce</span>
scala&gt; numbers.reduce<span class="hljs-function"><span class="hljs-params">((x, y) =&gt; x*y)</span>
<span class="hljs-title">res102</span>: <span class="hljs-title">Int</span> = 24

# <span class="hljs-title">countByValue</span> <span class="hljs-title">word</span> <span class="hljs-title">count</span>已实现
<span class="hljs-title">scala</span>&gt; <span class="hljs-title">numbers</span>.<span class="hljs-title">countByValue</span><span class="hljs-params">()</span>
<span class="hljs-title">res104</span>: <span class="hljs-title">scala</span>.<span class="hljs-title">collection</span>.<span class="hljs-title">Map</span>[<span class="hljs-title">Int</span>,<span class="hljs-title">Long</span>] = <span class="hljs-title">Map</span><span class="hljs-params">(<span class="hljs-number">4</span> -&gt; <span class="hljs-number">1</span>, <span class="hljs-number">2</span> -&gt; <span class="hljs-number">1</span>, <span class="hljs-number">1</span> -&gt; <span class="hljs-number">1</span>, <span class="hljs-number">3</span> -&gt; <span class="hljs-number">1</span>)</span>

# <span class="hljs-title">fold</span> 需要传入一个初始的单元值 加法是0 乘法是1
<span class="hljs-title">scala</span>&gt; <span class="hljs-title">numbers</span>.<span class="hljs-title">fold</span><span class="hljs-params">(<span class="hljs-number">1</span>)((x, y) =&gt; x*y)</span>
<span class="hljs-title">res103</span>: <span class="hljs-title">Int</span> = 24

# <span class="hljs-title">aggregate</span> 求平均数
<span class="hljs-title">scala</span>&gt; <span class="hljs-title">val</span> <span class="hljs-title">numbers</span> = <span class="hljs-title">sc</span>.<span class="hljs-title">parallelize</span><span class="hljs-params">(List(<span class="hljs-number">3</span>,<span class="hljs-number">4</span>,<span class="hljs-number">5</span>,<span class="hljs-number">6</span>))</span>
<span class="hljs-title">numbers</span>: <span class="hljs-title">org</span>.<span class="hljs-title">apache</span>.<span class="hljs-title">spark</span>.<span class="hljs-title">rdd</span>.<span class="hljs-title">RDD</span>[<span class="hljs-title">Int</span>] = <span class="hljs-title">ParallelCollectionRDD</span>[0] <span class="hljs-title">at</span> <span class="hljs-title">parallelize</span> <span class="hljs-title">at</span> &lt;<span class="hljs-title">console</span>&gt;:27

#<span class="hljs-title">numbers</span>.<span class="hljs-title">aggregate</span><span class="hljs-params">((<span class="hljs-number">0</span>,<span class="hljs-number">0</span>))(
#  ((x, value) =&gt; (x._1 + value, x._2 + <span class="hljs-number">1</span>)),
#  ((x1, x2) =&gt; (x1._1+x2._1, x1._2+x2._2))
#  )</span>
<span class="hljs-title">scala</span>&gt; <span class="hljs-title">numbers</span>.<span class="hljs-title">aggregate</span><span class="hljs-params">((<span class="hljs-number">0</span>,<span class="hljs-number">0</span>))(((x, value) =&gt; (x._1 + value, x._2 + <span class="hljs-number">1</span>)), ((x1, x2) =&gt; (x1._1+x2._1, x1._2+x2._2)))</span>
<span class="hljs-title">res0</span>: <span class="hljs-params">(Int, Int)</span> = <span class="hljs-params">(<span class="hljs-number">18</span>,<span class="hljs-number">4</span>)</span>

<span class="hljs-title">scala</span>&gt; <span class="hljs-title">res0</span>.<span class="hljs-title">_1</span>/<span class="hljs-title">res0</span>.<span class="hljs-title">_2</span>.<span class="hljs-title">toDouble</span>
<span class="hljs-title">res1</span>: <span class="hljs-title">Double</span> = 4.5
</span></code></pre>



<h4 id="55-rdd的打印">5.5 RDD的打印</h4>

<ul>
<li>take(n) 分区就近原则出</li>
</ul>



<pre class="prettyprint"><code class=" hljs avrasm">scala&gt; numbers<span class="hljs-preprocessor">.take</span>(<span class="hljs-number">2</span>)<span class="hljs-preprocessor">.foreach</span>(println)
<span class="hljs-number">1</span>
<span class="hljs-number">2</span></code></pre>

<ul>
<li>top(n)</li>
</ul>



<pre class="prettyprint"><code class=" hljs avrasm">scala&gt; numbers<span class="hljs-preprocessor">.top</span>(<span class="hljs-number">2</span>)<span class="hljs-preprocessor">.foreach</span>(println) 按照数据集合自己的顺序出
<span class="hljs-number">4</span>
<span class="hljs-number">3</span></code></pre>

<ul>
<li>sample(bWithReplacement, dFraction, seed) 丢骰子取样</li>
</ul>



<pre class="prettyprint"><code class=" hljs cs">scala&gt; numbers.sample(<span class="hljs-keyword">false</span>, <span class="hljs-number">0.3</span>).<span class="hljs-keyword">foreach</span>(println)
<span class="hljs-number">3</span>
<span class="hljs-number">4</span>
<span class="hljs-number">2</span></code></pre>

<ul>
<li>takeSample(bWithReplacement, n, seed) 随机取样n个</li>
</ul>



<pre class="prettyprint"><code class=" hljs cs">scala&gt; numbers.takeSample(<span class="hljs-keyword">false</span>, <span class="hljs-number">3</span>).<span class="hljs-keyword">foreach</span>(println)
<span class="hljs-number">4</span>
<span class="hljs-number">3</span>
<span class="hljs-number">1</span></code></pre>

<ul>
<li>collect() 全返回</li>
</ul>



<pre class="prettyprint"><code class=" hljs avrasm">scala&gt; numbers<span class="hljs-preprocessor">.collect</span>()<span class="hljs-preprocessor">.foreach</span>(println)
<span class="hljs-number">1</span>
<span class="hljs-number">2</span>
<span class="hljs-number">3</span>
<span class="hljs-number">4</span>
scala&gt; numbers<span class="hljs-preprocessor">.collect</span>()<span class="hljs-preprocessor">.mkString</span>(<span class="hljs-string">","</span>)
<span class="hljs-label">res2:</span> String = <span class="hljs-number">1</span>,<span class="hljs-number">2</span>,<span class="hljs-number">3</span>,<span class="hljs-number">4</span></code></pre>



<h4 id="56-持久化的几种类型">5.6 持久化的几种类型</h4>

<table>
<thead>
<tr>
  <th>级别</th>
  <th>使用的空间</th>
  <th>CPU时间</th>
  <th>是否在内存中</th>
  <th>是否在磁盘上</th>
  <th>备注</th>
</tr>
</thead>
<tbody><tr>
  <td>NONE</td>
  <td></td>
  <td></td>
  <td></td>
  <td></td>
  <td></td>
</tr>
<tr>
  <td>DISK_ONLY</td>
  <td>低</td>
  <td>高</td>
  <td>否</td>
  <td>是</td>
  <td></td>
</tr>
<tr>
  <td>DISK_ONLY_2</td>
  <td>低</td>
  <td>高</td>
  <td>否</td>
  <td>是</td>
  <td>同上一个级别，但存了两份</td>
</tr>
<tr>
  <td>MEMORY_ONLY</td>
  <td>高</td>
  <td>低</td>
  <td>是</td>
  <td>否</td>
  <td></td>
</tr>
<tr>
  <td>MEMORY_ONLY_2</td>
  <td>高</td>
  <td>低</td>
  <td>是</td>
  <td>否</td>
  <td>同上一个级别，但存了两份</td>
</tr>
<tr>
  <td>MEMORY_ONLY_SER</td>
  <td>低</td>
  <td>高</td>
  <td>是</td>
  <td>否</td>
  <td>ser是序列化的意思</td>
</tr>
<tr>
  <td>MEMORY_ONLY_SER_2</td>
  <td>低</td>
  <td>高</td>
  <td>是</td>
  <td>否</td>
  <td>同上一个级别，但存了两份</td>
</tr>
<tr>
  <td>MEMORY_AND_DISK</td>
  <td>高</td>
  <td>中等</td>
  <td>部分</td>
  <td>部分</td>
  <td>如果内存装不下了，多出了的写到磁盘</td>
</tr>
<tr>
  <td>MEMORY_AND_DISK_2</td>
  <td>高</td>
  <td>中等</td>
  <td>部分</td>
  <td>部分</td>
  <td>同上一个级别，但存了两份</td>
</tr>
<tr>
  <td>MEMORY_AND_DISK_SER</td>
  <td>低</td>
  <td>高</td>
  <td>部分</td>
  <td>部分</td>
  <td>内存存不下，多出来的部分存到磁盘，并将序列化数据写入内存</td>
</tr>
<tr>
  <td>MEMORY_AND_DISK_SER_2</td>
  <td>低</td>
  <td>高</td>
  <td>部分</td>
  <td>部分</td>
  <td>同上一个级别，但存了两份</td>
</tr>
<tr>
  <td>OFF_HEAP</td>
  <td></td>
  <td></td>
  <td></td>
  <td></td>
  <td></td>
</tr>
</tbody></table>


<p>- 如果内存使用的不够了， 我们使用最少使用原则（LRU）进行回收 <br>
- Spark还提供有unpersist()方法手动释放内存</p>

<h3 id="六pair-rdd编程">六、Pair RDD编程</h3>



<h4 id="61-创建pair-rdd">6.1 创建Pair RDD</h4>



<pre class="prettyprint"><code class=" hljs avrasm">scala&gt; numbers<span class="hljs-preprocessor">.collect</span>()<span class="hljs-preprocessor">.mkString</span>(<span class="hljs-string">","</span>)
<span class="hljs-label">res7:</span> String = <span class="hljs-number">3</span>,<span class="hljs-number">4</span>,<span class="hljs-number">5</span>,<span class="hljs-number">6</span>

scala&gt; val pairs = numbers<span class="hljs-preprocessor">.map</span>(<span class="hljs-built_in">x</span> =&gt; (<span class="hljs-built_in">x</span>+<span class="hljs-number">1</span>, <span class="hljs-built_in">x</span>*<span class="hljs-built_in">x</span>))
<span class="hljs-label">pairs:</span> org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.rdd</span><span class="hljs-preprocessor">.RDD</span>[(Int, Int)] = MapPartitionsRDD[<span class="hljs-number">1</span>] at map at &lt;console&gt;:<span class="hljs-number">29</span>

scala&gt; pairs<span class="hljs-preprocessor">.collect</span>()<span class="hljs-preprocessor">.mkString</span>(<span class="hljs-string">","</span>)
<span class="hljs-label">res9:</span> String = (<span class="hljs-number">4</span>,<span class="hljs-number">9</span>),(<span class="hljs-number">5</span>,<span class="hljs-number">16</span>),(<span class="hljs-number">6</span>,<span class="hljs-number">25</span>),(<span class="hljs-number">7</span>,<span class="hljs-number">36</span>)

val lines = sc<span class="hljs-preprocessor">.textFile</span>(<span class="hljs-string">"data.txt"</span>)
val pairs = lines<span class="hljs-preprocessor">.map</span>(s =&gt; (s, <span class="hljs-number">1</span>))
val counts = pairs<span class="hljs-preprocessor">.reduceByKey</span>((a, b) =&gt; a + b)</code></pre>



<h4 id="62-转化操作">6.2 转化操作</h4>

<p><img src="http://note.youdao.com/yws/public/resource/2ac828482cacc7eb1b526d673dbf2bdd/xmlnote/ED39BA7D712D4DAAB55A0F5E5C5BABB0/22345" alt="image" title=""> <br>
<img src="http://note.youdao.com/yws/public/resource/2ac828482cacc7eb1b526d673dbf2bdd/xmlnote/D110862D657C451692E02FC41EC288DE/22347" alt="image" title=""> <br>
<img src="http://note.youdao.com/yws/public/resource/2ac828482cacc7eb1b526d673dbf2bdd/xmlnote/9B12AADDB815469ABBE5DE9EC764330E/22349" alt="image" title=""></p>



<pre class="prettyprint"><code class=" hljs coffeescript">scala&gt; val pairs_1 = sc.parallelize(List((<span class="hljs-number">1</span>, <span class="hljs-number">2</span>), (<span class="hljs-number">3</span>, <span class="hljs-number">4</span>), (<span class="hljs-number">3</span>, <span class="hljs-number">6</span>)))
<span class="hljs-attribute">pairs_1</span>: org.apache.spark.rdd.RDD[(Int, Int)] = ParallelCollectionRDD[<span class="hljs-number">3</span>] at parallelize at &lt;<span class="hljs-built_in">console</span>&gt;:<span class="hljs-number">27</span>

scala&gt; pairs_1.collect.mkString(<span class="hljs-string">","</span>)
<span class="hljs-attribute">res12</span>: String = (<span class="hljs-number">1</span>,<span class="hljs-number">2</span>),(<span class="hljs-number">3</span>,<span class="hljs-number">4</span>),(<span class="hljs-number">3</span>,<span class="hljs-number">6</span>)

scala&gt; pairs_1.reduceByKey<span class="hljs-function"><span class="hljs-params">((x, y) =&gt; x+y)</span>.<span class="hljs-title">collect</span>.<span class="hljs-title">mkString</span><span class="hljs-params">(<span class="hljs-string">","</span>)</span>
<span class="hljs-title">res13</span>: <span class="hljs-title">String</span> = <span class="hljs-params">(<span class="hljs-number">1</span>,<span class="hljs-number">2</span>)</span>,<span class="hljs-params">(<span class="hljs-number">3</span>,<span class="hljs-number">10</span>)</span>

<span class="hljs-title">scala</span>&gt; <span class="hljs-title">pairs_1</span>.<span class="hljs-title">groupByKey</span><span class="hljs-params">()</span>.<span class="hljs-title">collect</span>.<span class="hljs-title">mkString</span><span class="hljs-params">(<span class="hljs-string">","</span>)</span>
<span class="hljs-title">res16</span>: <span class="hljs-title">String</span> = <span class="hljs-params">(<span class="hljs-number">1</span>,CompactBuffer(<span class="hljs-number">2</span>))</span>,<span class="hljs-params">(<span class="hljs-number">3</span>,CompactBuffer(<span class="hljs-number">4</span>, <span class="hljs-number">6</span>))</span>

<span class="hljs-title">scala</span>&gt; <span class="hljs-title">pairs_1</span>.<span class="hljs-title">mapValues</span><span class="hljs-params">(x =&gt; x+<span class="hljs-number">10</span>)</span>.<span class="hljs-title">collect</span>.<span class="hljs-title">mkString</span><span class="hljs-params">(<span class="hljs-string">","</span>)</span>
<span class="hljs-title">res17</span>: <span class="hljs-title">String</span> = <span class="hljs-params">(<span class="hljs-number">1</span>,<span class="hljs-number">12</span>)</span>,<span class="hljs-params">(<span class="hljs-number">3</span>,<span class="hljs-number">14</span>)</span>,<span class="hljs-params">(<span class="hljs-number">3</span>,<span class="hljs-number">16</span>)</span>

<span class="hljs-title">scala</span>&gt; <span class="hljs-title">pairs_1</span>.<span class="hljs-title">flatMapValues</span><span class="hljs-params">(x =&gt; (x to <span class="hljs-number">15</span>))</span>.<span class="hljs-title">collect</span>.<span class="hljs-title">mkString</span><span class="hljs-params">(<span class="hljs-string">","</span>)</span>
<span class="hljs-title">res19</span>: <span class="hljs-title">String</span> = <span class="hljs-params">(<span class="hljs-number">1</span>,<span class="hljs-number">2</span>)</span>,<span class="hljs-params">(<span class="hljs-number">1</span>,<span class="hljs-number">3</span>)</span>,<span class="hljs-params">(<span class="hljs-number">1</span>,<span class="hljs-number">4</span>)</span>,<span class="hljs-params">(<span class="hljs-number">1</span>,<span class="hljs-number">5</span>)</span>,<span class="hljs-params">(<span class="hljs-number">1</span>,<span class="hljs-number">6</span>)</span>,<span class="hljs-params">(<span class="hljs-number">1</span>,<span class="hljs-number">7</span>)</span>,<span class="hljs-params">(<span class="hljs-number">1</span>,<span class="hljs-number">8</span>)</span>,<span class="hljs-params">(<span class="hljs-number">1</span>,<span class="hljs-number">9</span>)</span>,<span class="hljs-params">(<span class="hljs-number">1</span>,<span class="hljs-number">10</span>)</span>,<span class="hljs-params">(<span class="hljs-number">1</span>,<span class="hljs-number">11</span>)</span>,<span class="hljs-params">(<span class="hljs-number">1</span>,<span class="hljs-number">12</span>)</span>,<span class="hljs-params">(<span class="hljs-number">1</span>,<span class="hljs-number">13</span>)</span>,<span class="hljs-params">(<span class="hljs-number">1</span>,<span class="hljs-number">14</span>)</span>,<span class="hljs-params">(<span class="hljs-number">1</span>,<span class="hljs-number">15</span>)</span>,<span class="hljs-params">(<span class="hljs-number">3</span>,<span class="hljs-number">4</span>)</span>,<span class="hljs-params">(<span class="hljs-number">3</span>,<span class="hljs-number">5</span>)</span>,<span class="hljs-params">(<span class="hljs-number">3</span>,<span class="hljs-number">6</span>)</span>,<span class="hljs-params">(<span class="hljs-number">3</span>,<span class="hljs-number">7</span>)</span>,<span class="hljs-params">(<span class="hljs-number">3</span>,<span class="hljs-number">8</span>)</span>,<span class="hljs-params">(<span class="hljs-number">3</span>,<span class="hljs-number">9</span>)</span>,<span class="hljs-params">(<span class="hljs-number">3</span>,<span class="hljs-number">10</span>)</span>,<span class="hljs-params">(<span class="hljs-number">3</span>,<span class="hljs-number">11</span>)</span>,<span class="hljs-params">(<span class="hljs-number">3</span>,<span class="hljs-number">12</span>)</span>,<span class="hljs-params">(<span class="hljs-number">3</span>,<span class="hljs-number">13</span>)</span>,<span class="hljs-params">(<span class="hljs-number">3</span>,<span class="hljs-number">14</span>)</span>,<span class="hljs-params">(<span class="hljs-number">3</span>,<span class="hljs-number">15</span>)</span>,<span class="hljs-params">(<span class="hljs-number">3</span>,<span class="hljs-number">6</span>)</span>,<span class="hljs-params">(<span class="hljs-number">3</span>,<span class="hljs-number">7</span>)</span>,<span class="hljs-params">(<span class="hljs-number">3</span>,<span class="hljs-number">8</span>)</span>,<span class="hljs-params">(<span class="hljs-number">3</span>,<span class="hljs-number">9</span>)</span>,<span class="hljs-params">(<span class="hljs-number">3</span>,<span class="hljs-number">10</span>)</span>,<span class="hljs-params">(<span class="hljs-number">3</span>,<span class="hljs-number">11</span>)</span>,<span class="hljs-params">(<span class="hljs-number">3</span>,<span class="hljs-number">12</span>)</span>,<span class="hljs-params">(<span class="hljs-number">3</span>,<span class="hljs-number">13</span>)</span>,<span class="hljs-params">(<span class="hljs-number">3</span>,<span class="hljs-number">14</span>)</span>,<span class="hljs-params">(<span class="hljs-number">3</span>,<span class="hljs-number">15</span>)</span>

<span class="hljs-title">scala</span>&gt; <span class="hljs-title">pairs_1</span>.<span class="hljs-title">keys</span>.<span class="hljs-title">collect</span>.<span class="hljs-title">mkString</span><span class="hljs-params">(<span class="hljs-string">","</span>)</span>
<span class="hljs-title">res24</span>: <span class="hljs-title">String</span> = 1,3,3

<span class="hljs-title">scala</span>&gt; <span class="hljs-title">pairs_1</span>.<span class="hljs-title">values</span>.<span class="hljs-title">collect</span>.<span class="hljs-title">mkString</span><span class="hljs-params">(<span class="hljs-string">","</span>)</span>
<span class="hljs-title">res25</span>: <span class="hljs-title">String</span> = 2,4,6

<span class="hljs-title">scala</span>&gt; <span class="hljs-title">pairs_1</span>.<span class="hljs-title">sortByKey</span><span class="hljs-params">()</span>.<span class="hljs-title">collect</span>.<span class="hljs-title">mkString</span><span class="hljs-params">(<span class="hljs-string">","</span>)</span>
<span class="hljs-title">res28</span>: <span class="hljs-title">String</span> = <span class="hljs-params">(<span class="hljs-number">1</span>,<span class="hljs-number">2</span>)</span>,<span class="hljs-params">(<span class="hljs-number">3</span>,<span class="hljs-number">4</span>)</span>,<span class="hljs-params">(<span class="hljs-number">3</span>,<span class="hljs-number">6</span>)</span>

<span class="hljs-title">scala</span>&gt; <span class="hljs-title">pairs_1</span>.<span class="hljs-title">sortByKey</span><span class="hljs-params">(<span class="hljs-literal">false</span>)</span>.<span class="hljs-title">collect</span>.<span class="hljs-title">mkString</span><span class="hljs-params">(<span class="hljs-string">","</span>)</span>
<span class="hljs-title">res49</span>: <span class="hljs-title">String</span> = <span class="hljs-params">(<span class="hljs-number">3</span>,<span class="hljs-number">4</span>)</span>,<span class="hljs-params">(<span class="hljs-number">3</span>,<span class="hljs-number">6</span>)</span>,<span class="hljs-params">(<span class="hljs-number">1</span>,<span class="hljs-number">2</span>)</span>

----------------------------

<span class="hljs-title">scala</span>&gt; <span class="hljs-title">pairs_1</span>.<span class="hljs-title">sortByKey</span><span class="hljs-params">()</span>.<span class="hljs-title">collect</span>.<span class="hljs-title">mkString</span><span class="hljs-params">(<span class="hljs-string">","</span>)</span>
<span class="hljs-title">res28</span>: <span class="hljs-title">String</span> = <span class="hljs-params">(<span class="hljs-number">1</span>,<span class="hljs-number">2</span>)</span>,<span class="hljs-params">(<span class="hljs-number">3</span>,<span class="hljs-number">4</span>)</span>,<span class="hljs-params">(<span class="hljs-number">3</span>,<span class="hljs-number">6</span>)</span>

<span class="hljs-title">scala</span>&gt; <span class="hljs-title">val</span> <span class="hljs-title">pairs_2</span> = <span class="hljs-title">sc</span>.<span class="hljs-title">parallelize</span><span class="hljs-params">(List((<span class="hljs-number">3</span>, <span class="hljs-number">9</span>)))</span>
<span class="hljs-title">pairs_2</span>: <span class="hljs-title">org</span>.<span class="hljs-title">apache</span>.<span class="hljs-title">spark</span>.<span class="hljs-title">rdd</span>.<span class="hljs-title">RDD</span>[<span class="hljs-params">(Int, Int)</span>] = <span class="hljs-title">ParallelCollectionRDD</span>[15] <span class="hljs-title">at</span> <span class="hljs-title">parallelize</span> <span class="hljs-title">at</span> &lt;<span class="hljs-title">console</span>&gt;:27

<span class="hljs-title">scala</span>&gt; <span class="hljs-title">pairs_1</span>.<span class="hljs-title">join</span><span class="hljs-params">(pairs_2)</span>.<span class="hljs-title">collect</span>.<span class="hljs-title">mkString</span><span class="hljs-params">(<span class="hljs-string">","</span>)</span>
<span class="hljs-title">res31</span>: <span class="hljs-title">String</span> = <span class="hljs-params">(<span class="hljs-number">3</span>,(<span class="hljs-number">4</span>,<span class="hljs-number">9</span>))</span>,<span class="hljs-params">(<span class="hljs-number">3</span>,(<span class="hljs-number">6</span>,<span class="hljs-number">9</span>))</span>

<span class="hljs-title">scala</span>&gt; <span class="hljs-title">pairs_1</span>.<span class="hljs-title">rightOuterJoin</span><span class="hljs-params">(pairs_2)</span>.<span class="hljs-title">collect</span>.<span class="hljs-title">mkString</span><span class="hljs-params">(<span class="hljs-string">","</span>)</span>
<span class="hljs-title">res33</span>: <span class="hljs-title">String</span> = <span class="hljs-params">(<span class="hljs-number">3</span>,(Some(<span class="hljs-number">4</span>),<span class="hljs-number">9</span>))</span>,<span class="hljs-params">(<span class="hljs-number">3</span>,(Some(<span class="hljs-number">6</span>),<span class="hljs-number">9</span>))</span>

<span class="hljs-title">scala</span>&gt; <span class="hljs-title">pairs_1</span>.<span class="hljs-title">leftOuterJoin</span><span class="hljs-params">(pairs_2)</span>.<span class="hljs-title">collect</span>.<span class="hljs-title">mkString</span><span class="hljs-params">(<span class="hljs-string">","</span>)</span>
<span class="hljs-title">res34</span>: <span class="hljs-title">String</span> = <span class="hljs-params">(<span class="hljs-number">1</span>,(<span class="hljs-number">2</span>,None))</span>,<span class="hljs-params">(<span class="hljs-number">3</span>,(<span class="hljs-number">4</span>,Some(<span class="hljs-number">9</span>)))</span>,<span class="hljs-params">(<span class="hljs-number">3</span>,(<span class="hljs-number">6</span>,Some(<span class="hljs-number">9</span>)))</span>

<span class="hljs-title">scala</span>&gt; <span class="hljs-title">pairs_1</span>.<span class="hljs-title">cogroup</span><span class="hljs-params">(pairs_2)</span>.<span class="hljs-title">collect</span>.<span class="hljs-title">mkString</span><span class="hljs-params">(<span class="hljs-string">","</span>)</span>
<span class="hljs-title">res35</span>: <span class="hljs-title">String</span> = <span class="hljs-params">(<span class="hljs-number">1</span>,(CompactBuffer(<span class="hljs-number">2</span>),CompactBuffer()))</span>,<span class="hljs-params">(<span class="hljs-number">3</span>,(CompactBuffer(<span class="hljs-number">4</span>, <span class="hljs-number">6</span>),CompactBuffer(<span class="hljs-number">9</span>)))</span>

<span class="hljs-title">scala</span>&gt; <span class="hljs-title">pairs_1</span>.<span class="hljs-title">filter</span>{<span class="hljs-title">case</span><span class="hljs-params">(x, y)</span> =&gt;</span> y&gt;<span class="hljs-number">4</span>}.collect.mkString(<span class="hljs-string">","</span>)
<span class="hljs-attribute">res40</span>: String = (<span class="hljs-number">3</span>,<span class="hljs-number">6</span>)</code></pre>



<h4 id="63-行动操作">6.3 行动操作</h4>



<pre class="prettyprint"><code class=" hljs avrasm">scala&gt; pairs_1<span class="hljs-preprocessor">.collect</span><span class="hljs-preprocessor">.mkString</span>(<span class="hljs-string">","</span>)
<span class="hljs-label">res48:</span> String = (<span class="hljs-number">1</span>,<span class="hljs-number">2</span>),(<span class="hljs-number">3</span>,<span class="hljs-number">4</span>),(<span class="hljs-number">3</span>,<span class="hljs-number">6</span>)

<span class="hljs-preprocessor">## 注意，返回的是一个Map</span>
scala&gt; pairs_1<span class="hljs-preprocessor">.countByKey</span>()
<span class="hljs-label">res50:</span> scala<span class="hljs-preprocessor">.collection</span><span class="hljs-preprocessor">.Map</span>[Int,Long] = Map(<span class="hljs-number">1</span> -&gt; <span class="hljs-number">1</span>, <span class="hljs-number">3</span> -&gt; <span class="hljs-number">2</span>)

<span class="hljs-preprocessor">## 注意，返回的是一个Map， 一个Key对应一个Value</span>
scala&gt; pairs_1<span class="hljs-preprocessor">.collectAsMap</span>()
<span class="hljs-label">res52:</span> scala<span class="hljs-preprocessor">.collection</span><span class="hljs-preprocessor">.Map</span>[Int,Int] = Map(<span class="hljs-number">1</span> -&gt; <span class="hljs-number">2</span>, <span class="hljs-number">3</span> -&gt; <span class="hljs-number">6</span>)

<span class="hljs-preprocessor">## 查询Value</span>
scala&gt; pairs_1<span class="hljs-preprocessor">.lookup</span>(<span class="hljs-number">3</span>)
<span class="hljs-label">res54:</span> Seq[Int] = WrappedArray(<span class="hljs-number">4</span>, <span class="hljs-number">6</span>)

scala&gt; pairs_1<span class="hljs-preprocessor">.lookup</span>(<span class="hljs-number">3</span>)<span class="hljs-preprocessor">.toString</span>
<span class="hljs-label">res55:</span> String = WrappedArray(<span class="hljs-number">4</span>, <span class="hljs-number">6</span>)</code></pre>



<h4 id="64-分区详解">6.4 分区详解</h4>

<p>每一个RDD都是不可变的，每一个RDD我们都可以指定其分区方法 <br>
- org.apache.spark.HashPartitioner(partitions : scala.Int) Hash分区 <br>
- org.apache.spark.RangePartitioner[K, V] 范围分区  </p>

<p>分区的好处不言而喻——减少数据的重新洗牌，大数据合并小数据集，小数据向着大数据集的分区靠拢，自然久省去了很多网络的耗时，一切就像是并行在单机上一样的 <br>
分一次分区都会创建新的RDD <br>
分区完毕后还需要用到则需要使用缓存函数persist，避免每次都重新分区</p>

<pre class="prettyprint"><code class=" hljs avrasm">scala&gt; pairs_1<span class="hljs-preprocessor">.partitioner</span>
<span class="hljs-label">res56:</span> Option[org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.Partitioner</span>] = None

scala&gt; pairs_1<span class="hljs-preprocessor">.partitionBy</span>(new org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.HashPartitioner</span>(<span class="hljs-number">2</span>))
<span class="hljs-label">res59:</span> org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.rdd</span><span class="hljs-preprocessor">.RDD</span>[(Int, Int)] = ShuffledRDD[<span class="hljs-number">42</span>] at partitionBy at &lt;console&gt;:<span class="hljs-number">30</span>

scala&gt; pairs_1<span class="hljs-preprocessor">.partitioner</span>
<span class="hljs-label">res60:</span> Option[org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.Partitioner</span>] = None

scala&gt; res59<span class="hljs-preprocessor">.partitioner</span>
<span class="hljs-label">res61:</span> Option[org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.Partitioner</span>] = Some(org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.HashPartitioner</span><span class="hljs-localvars">@2</span>)

scala&gt; pairs_1<span class="hljs-preprocessor">.sortByKey</span>()
<span class="hljs-label">res62:</span> org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.rdd</span><span class="hljs-preprocessor">.RDD</span>[(Int, Int)] = ShuffledRDD[<span class="hljs-number">45</span>] at sortByKey at &lt;console&gt;:<span class="hljs-number">30</span>

scala&gt; res62<span class="hljs-preprocessor">.partitioner</span>
<span class="hljs-label">res63:</span> Option[org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.Partitioner</span>] = Some(org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.RangePartitioner</span><span class="hljs-localvars">@8</span>ed)</code></pre>

<p>这里列出了所有会为生成的结果 RDD 设好分区方式的操作：</p>



<pre class="prettyprint"><code class=" hljs erlang"><span class="hljs-function"><span class="hljs-title">cogroup</span><span class="hljs-params">()</span>
<span class="hljs-title">groupWith</span><span class="hljs-params">()</span>
<span class="hljs-title">join</span><span class="hljs-params">()</span>
<span class="hljs-title">leftOuterJoin</span><span class="hljs-params">()</span>
<span class="hljs-title">rightOuterJoin</span><span class="hljs-params">()</span>
<span class="hljs-title">groupByKey</span><span class="hljs-params">()</span>
<span class="hljs-title">reduceByKey</span><span class="hljs-params">()</span>
<span class="hljs-title">combineByKey</span><span class="hljs-params">()</span>
<span class="hljs-title">partitionBy</span><span class="hljs-params">()</span>
<span class="hljs-title">sort</span><span class="hljs-params">()</span>
<span class="hljs-title">mapValues</span><span class="hljs-params">()</span>（如果父 RDD 有分区方式的话）
<span class="hljs-title">flatMapValues</span><span class="hljs-params">()</span>（如果父 RDD 有分区方式的话）
<span class="hljs-title">filter</span><span class="hljs-params">()</span>（如果父 RDD 有分区方式的话）</span></code></pre>

<p>对于二元操作，输出数据的分区方式取决于父 RDD 的分区方式。默认情况下，结果会采用哈希分区，分区的数量和操作的并行度一样。 <br>
如果其中的一个父 RDD 已经设置过分区方式，那么结果就会采用那种分区方式； <br>
如果两个父 RDD 都设置过分区方式，结果 RDD 会采用第一个父 RDD 的分区方式。但是分区数会选max</p>

<table>
<thead>
<tr>
  <th>action</th>
  <th>是否会修改分区数</th>
  <th>是否会修改分区方法</th>
</tr>
</thead>
<tbody><tr>
  <td>partitionBy(new HashPartitioner(n))</td>
  <td>n</td>
  <td>HashPartitioner</td>
</tr>
<tr>
  <td>distinct</td>
  <td>不变</td>
  <td>none</td>
</tr>
<tr>
  <td>distinct(n)</td>
  <td>n</td>
  <td>none</td>
</tr>
<tr>
  <td>mapValues</td>
  <td>不变</td>
  <td>不变</td>
</tr>
<tr>
  <td>reduceByKey</td>
  <td>不变</td>
  <td>不变</td>
</tr>
<tr>
  <td>map</td>
  <td>不变</td>
  <td>none</td>
</tr>
<tr>
  <td>zipWithUniqueId</td>
  <td>不变</td>
  <td>none</td>
</tr>
</tbody></table>




<h3 id="七文件操作">七、文件操作</h3>



<h4 id="71-spark支持的文件格式">7.1 Spark支持的文件格式</h4>

<table>
<thead>
<tr>
  <th>格式名称</th>
  <th>结构化</th>
  <th>备注</th>
</tr>
</thead>
<tbody><tr>
  <td>文本文件</td>
  <td>否</td>
  <td>普通的文本文件，每行一条记录</td>
</tr>
<tr>
  <td>JSON</td>
  <td>半结构化</td>
  <td>常见的基于文本的格式，大多数库都要求每行一条记录</td>
</tr>
<tr>
  <td>CSV</td>
  <td>是</td>
  <td>非常常见的基于文本的格式，通常在电子表格应用中使用</td>
</tr>
<tr>
  <td>SequenceFiles</td>
  <td>是</td>
  <td>一种用于键值对数据的常见 Hadoop 文件格式</td>
</tr>
<tr>
  <td>Protocol buffers</td>
  <td>是</td>
  <td>一种快速、节约空间的跨语言格式</td>
</tr>
<tr>
  <td>对象文件</td>
  <td>是</td>
  <td>用来将 Spark 作业中的数据存储下来以让共享的代码读取。改变类的时候 它会失效，因为它依赖于 Java 序列化</td>
</tr>
</tbody></table>




<pre class="prettyprint"><code class=" hljs vala"><span class="hljs-preprocessor"># 当传入的参数是目录的时候</span>
<span class="hljs-preprocessor">## 转化为一个RDD</span>
val input = sc.textFiles(inputFile)

<span class="hljs-preprocessor">## 转化为一个以文件名为Key的Pair RDD</span>
val input = sc.wholeTextFiles(inputFile)

<span class="hljs-preprocessor"># 输出的产出参数是一个目录，因为Spark是并发输出的</span>
rdd.saveAsTextFile(output_path)</code></pre>



<pre class="prettyprint"><code class=" hljs avrasm">def main(args: Array[String]) {
    if (args<span class="hljs-preprocessor">.length</span> &lt; <span class="hljs-number">3</span>) {
        println(<span class="hljs-string">"Usage: [sparkmaster] [inputfile] [outputfile]"</span>)
        exit(<span class="hljs-number">1</span>)
    }
    val master = args(<span class="hljs-number">0</span>)
    val inputFile = args(<span class="hljs-number">1</span>)
    val outputFile = args(<span class="hljs-number">2</span>)
    val sc = new SparkContext(master, <span class="hljs-string">"BasicParseJson"</span>, System<span class="hljs-preprocessor">.getenv</span>(<span class="hljs-string">"SPARK_HOME"</span>))
    val input = sc<span class="hljs-preprocessor">.textFile</span>(inputFile)
    //input<span class="hljs-preprocessor">.flatMap</span>(msg =&gt; if (JSON<span class="hljs-preprocessor">.parseObject</span>(msg)<span class="hljs-preprocessor">.getString</span>(<span class="hljs-string">"name"</span>)<span class="hljs-preprocessor">.contentEquals</span>(<span class="hljs-string">"Sparky The Bear"</span>)) { msg } else { <span class="hljs-string">""</span> })<span class="hljs-preprocessor">.collect</span>()<span class="hljs-preprocessor">.foreach</span>(print)

    input<span class="hljs-preprocessor">.map</span>(JSON<span class="hljs-preprocessor">.parseObject</span>(_))<span class="hljs-preprocessor">.saveAsTextFile</span>(outputFile)
}</code></pre>



<pre class="prettyprint"><code class=" hljs scala"><span class="hljs-class"><span class="hljs-keyword">case</span> <span class="hljs-keyword">class</span> <span class="hljs-title">Person</span><span class="hljs-params">(name: String, favouriteAnimal: String)</span></span>

<span class="hljs-keyword">def</span> main(args: Array[String]) {
    <span class="hljs-keyword">if</span> (args.length &lt; <span class="hljs-number">3</span>) {
        println(<span class="hljs-string">"Usage: [sparkmaster] [inputfile] [outputfile]"</span>)
        exit(<span class="hljs-number">1</span>)
    }
    <span class="hljs-keyword">val</span> master = args(<span class="hljs-number">0</span>)
    <span class="hljs-keyword">val</span> inputFile = args(<span class="hljs-number">1</span>)
    <span class="hljs-keyword">val</span> outputFile = args(<span class="hljs-number">2</span>)
    <span class="hljs-keyword">val</span> sc = <span class="hljs-keyword">new</span> SparkContext(master, <span class="hljs-string">"BasicParseCsv"</span>, System.getenv(<span class="hljs-string">"SPARK_HOME"</span>))
    <span class="hljs-keyword">val</span> input = sc.textFile(inputFile)
    <span class="hljs-keyword">val</span> result = input.map{ line =&gt;
        <span class="hljs-keyword">val</span> reader = <span class="hljs-keyword">new</span> CSVReader(<span class="hljs-keyword">new</span> StringReader(line));
        reader.readNext();
    }

    <span class="hljs-keyword">val</span> people = result.map(x =&gt; Person(x(<span class="hljs-number">0</span>), x(<span class="hljs-number">1</span>)))
    <span class="hljs-keyword">val</span> pandaLovers = people.filter(person =&gt; person.favouriteAnimal == <span class="hljs-string">"panda"</span>)

    pandaLovers.map(person =&gt; List(person.name, person.favouriteAnimal).toArray).mapPartitions{ people =&gt;
        <span class="hljs-keyword">val</span> stringWriter = <span class="hljs-keyword">new</span> StringWriter();
        <span class="hljs-keyword">val</span> csvWriter = <span class="hljs-keyword">new</span> CSVWriter(stringWriter);
        csvWriter.writeAll(people.toList)
        Iterator(stringWriter.toString)
    }.saveAsTextFile(outputFile)
}
</code></pre>



<h4 id="72-spark支持的文件存储方式">7.2 Spark支持的文件存储方式</h4>

<ul>
<li>File System</li>
<li>HDFS</li>
<li>Cassandra</li>
<li>HBase</li>
<li>Amazon S3</li>
<li>Spark SQL</li>
<li>etc. Spark supports text files, SequenceFiles, and any other Hadoop InputFormat.</li>
</ul>



<pre class="prettyprint"><code class=" hljs fsharp"><span class="hljs-keyword">val</span> lines = sc.textFile(<span class="hljs-string">"file:///usr/local/opt/spark-1.6.1-bin-hadoop2.4/README.md"</span>)  
<span class="hljs-keyword">val</span> lines = sc.textFile(<span class="hljs-string">"hdfs:///usr/local/opt/spark-1.6.1-bin-hadoop2.4/README.md"</span>)  
<span class="hljs-keyword">val</span> lines = sc.textFile(<span class="hljs-string">"s3n://bigdata-east/tmp/README.md"</span>)  </code></pre>



<h3 id="八spark编程进阶">八、Spark编程进阶</h3>



<h4 id="81-共享变量">8.1 共享变量</h4>



<h5 id="811-累加器-accumulator">8.1.1 累加器 accumulator</h5>

<ul>
<li>生命周期 <br>
在驱动器中创建 – 在执行器中累计 – 在驱动器中获取返回结果</li>
<li>累加器不是严格的只累计一次 <br>
转化操作可以因为一些原因被多次执行（任务执行失败重新执行、任务执行的太慢呗重新执行、原来RDD占用的内存被回收转化操作重新加载并执行方法），从而导致目前的累加器只适合做debug使用，或者foreach</li>
<li>累加器的操作需要满足交换律(即，a op b等同于b op a) 和 结合律(即、 (a op b) op c 等同于 a op (b op c)），比如加法、乘法、max函数</li>
</ul>



<pre class="prettyprint"><code class=" hljs scala"><span class="hljs-keyword">def</span> main(args: Array[String]) {
    <span class="hljs-keyword">val</span> master = args(<span class="hljs-number">0</span>)
    <span class="hljs-keyword">val</span> inputFile = args(<span class="hljs-number">1</span>)
    <span class="hljs-keyword">val</span> sc = <span class="hljs-keyword">new</span> SparkContext(master, <span class="hljs-string">"BasicLoadNums"</span>, System.getenv(<span class="hljs-string">"SPARK_HOME"</span>))
    <span class="hljs-keyword">val</span> file = sc.textFile(inputFile)
    <span class="hljs-keyword">val</span> errorLines = sc.accumulator(<span class="hljs-number">0</span>) <span class="hljs-comment">// Create an Accumulator[Int] initialized to 0</span>
    <span class="hljs-keyword">val</span> dataLines = sc.accumulator(<span class="hljs-number">0</span>) <span class="hljs-comment">// Create a second Accumulator[Int] initialized to 0</span>
    <span class="hljs-keyword">val</span> counts = file.flatMap(line =&gt; {
        <span class="hljs-keyword">try</span> {
            <span class="hljs-keyword">val</span> input = line.split(<span class="hljs-string">" "</span>)
            <span class="hljs-keyword">val</span> data = Some((input(<span class="hljs-number">0</span>), input(<span class="hljs-number">1</span>).toInt))
            dataLines += <span class="hljs-number">1</span>
            data
        } <span class="hljs-keyword">catch</span> {
            <span class="hljs-keyword">case</span> e: java.lang.NumberFormatException =&gt; {
                errorLines += <span class="hljs-number">1</span>
                None
            }
            <span class="hljs-keyword">case</span> e: java.lang.ArrayIndexOutOfBoundsException =&gt; {
                errorLines += <span class="hljs-number">1</span>
                None
            }
        }
    }).reduceByKey(_ + _)

    println(counts.collectAsMap().mkString(<span class="hljs-string">", "</span>))
    println(s<span class="hljs-string">"Too many errors ${errorLines.value} for ${dataLines.value}"</span>)
}</code></pre>



<h5 id="812-广播变量">8.1.2 广播变量</h5>

<ul>
<li>调用SparkContext.broadcast创建出一个Broadcast[T]对象。 任何可序列化的类型都可以</li>
<li>通过value属性访问该广播变量的值</li>
<li>广播变量只会被发到各个节点一次，应作为只读值处理(但是，如果修改了这个值，将不会影响到别的节点)</li>
<li>传输中选择一个既好又快的序列化格式是很重要的</li>
</ul>



<pre class="prettyprint"><code class=" hljs coffeescript">val signPrefixes = sc.broadcast(loadCallSignTable())
val countryContactCounts = contactCounts.map{
    <span class="hljs-reserved">case</span> <span class="hljs-function"><span class="hljs-params">(sign, count)</span> =&gt;</span>
        val country = lookupInArray(sign, signPrefixes.value)
        (country, count)
}.reduceByKey<span class="hljs-function"><span class="hljs-params">((x, y) =&gt; x + y)</span>

<span class="hljs-title">def</span> <span class="hljs-title">loadCallSignTable</span><span class="hljs-params">()</span> = {
    <span class="hljs-title">scala</span>.<span class="hljs-title">io</span>.<span class="hljs-title">Source</span>.<span class="hljs-title">fromFile</span><span class="hljs-params">(<span class="hljs-string">"./files/callsign_tbl_sorted"</span>)</span>.<span class="hljs-title">getLines</span><span class="hljs-params">()</span>
        .<span class="hljs-title">filter</span><span class="hljs-params">(_ != <span class="hljs-string">""</span>)</span>.<span class="hljs-title">toArray</span>
}

# ./<span class="hljs-title">files</span>/<span class="hljs-title">callsign_tbl_sorted</span>
3<span class="hljs-title">DM</span>, <span class="hljs-title">Swaziland</span> <span class="hljs-params">(Kingdom <span class="hljs-keyword">of</span>)</span>
3<span class="hljs-title">DZ</span>, <span class="hljs-title">Fiji</span> <span class="hljs-params">(Republic <span class="hljs-keyword">of</span>)</span>
3<span class="hljs-title">FZ</span>, <span class="hljs-title">Panama</span> <span class="hljs-params">(Republic <span class="hljs-keyword">of</span>)</span>
3<span class="hljs-title">GZ</span>, <span class="hljs-title">Chile</span>
3<span class="hljs-title">UZ</span>, <span class="hljs-title">China</span> <span class="hljs-params">(People<span class="hljs-string">'s Republic of)</span></span></span></code></pre>



<h4 id="82-调用第三方脚本-pipe">8.2 调用第三方脚本 Pipe</h4>



<pre class="prettyprint"><code class=" hljs avrasm">val pwd = System<span class="hljs-preprocessor">.getProperty</span>(<span class="hljs-string">"user.dir"</span>)
val distScript = pwd + <span class="hljs-string">"/bin/finddistance.R"</span>
val distScriptName = <span class="hljs-string">"finddistance.R"</span>
<span class="hljs-preprocessor">## 上传脚本</span>
sc<span class="hljs-preprocessor">.addFile</span>(distScript)
val pipeInputs = contactsContactLists<span class="hljs-preprocessor">.values</span><span class="hljs-preprocessor">.flatMap</span>(<span class="hljs-built_in">x</span> =&gt; <span class="hljs-built_in">x</span><span class="hljs-preprocessor">.map</span>(<span class="hljs-built_in">y</span> =&gt; s<span class="hljs-string">"${y.contactlat},${y.contactlong},${y.mylat},${y.mylong}"</span>))
println(pipeInputs<span class="hljs-preprocessor">.collect</span>()<span class="hljs-preprocessor">.toList</span>)
<span class="hljs-preprocessor">## 根据脚本名加载文件</span>
val distances = pipeInputs<span class="hljs-preprocessor">.pipe</span>(SparkFiles<span class="hljs-preprocessor">.get</span>(distScriptName))</code></pre>



<h4 id="83-数值rdd-statcounter">8.3 数值RDD - StatCounter</h4>

<p>调用stats()时，会通过一次遍历数据计算出大多数常用的数据统计 <br>
count() RDD 中的元素个数 <br>
mean() 元素的平均值 <br>
sum() 总和 <br>
max() 最大值 <br>
min() 最小值 <br>
variance() 元素的方差 <br>
sampleVariance() 从采样中计算出的方差 <br>
stdev() 标准差 <br>
sampleStdev() 采样的标准差</p>



<pre class="prettyprint"><code class=" hljs fsharp"><span class="hljs-keyword">val</span> stats = distanceDoubles.stats()
<span class="hljs-keyword">val</span> stddev = stats.stdev
<span class="hljs-keyword">val</span> mean = stats.mean</code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>