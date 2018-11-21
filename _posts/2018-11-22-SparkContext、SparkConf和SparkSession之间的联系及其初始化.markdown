---
layout:     post
title:      SparkContext、SparkConf和SparkSession之间的联系及其初始化
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/love666666shen/article/details/79260944				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="1-sparkconf和sparkcontext">1 SparkConf和SparkContext</h1>

<h2 id="11-启动spark-shell">1.1 启动spark-shell</h2>

<p><strong>每个Spark应用程序都需要一个Spark环境，这是Spark RDD API的主要入口点。Spark Shell提供了一个名为“sc”的预配置Spark环境和一个名为“spark”的预配置Spark会话。</strong></p>

<p>使用spark-shell启动交互式命令的过程如下： <br>
进入spark的安装目录，转到bin目录下，启动命令./spark-shell，出现如下图所示的给出上spark和scala的版本号即启动成功。</p>

<p><img src="https://img-blog.csdn.net/20180205160939572?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbG92ZTY2NjY2NnNoZW4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p>使用Spark Shell的时候，本身是预配置了sc，即SparkConf和SparkContext的，但是在实际使用编辑器编程过程中是需要设置这些配置的。</p>

<h2 id="12-在spark-shell中出现error-not-found-value-sc问题">1.2 在spark-shell中出现error: not found: value sc问题</h2>

<p>在spark-shell中运行val myRDD = sc.parallelize(List(“hdfs”, “spark”, “rdd”))命令时，出现“error: not found: value sc”找不到sc的问题。</p>

<p>原因：在linux系统下，启动交互式命令行时使用的是scala命令，而不是spark-shell命令，这样就找不到spark配置的环境变量，由于每个Spark应用程序都需要一个Spark环境，这样在使用scala命令时就找不到“sc”的预配置，所以会出现上面的问题。</p>

<p>解决方式：进入spark的安装目录，转到spark目录下的bin目录,运行命令./spark-shell命令（而不是scala命令），再运行上面的语句val myRDD = sc.parallelize(List(“hdfs”, “spark”, “rdd”))就不会出现该问题了。</p>

<h2 id="13-error-not-found-type-sparkconf或type-sparkcontext问题">1.3 error: not found: type SparkConf或type SparkContext问题</h2>

<p>运行下面的程序时，出现error: not found: type SparkConf或type SparkContext问题：</p>

<pre class="prettyprint"><code class=" hljs fsharp"><span class="hljs-keyword">val</span> conf = <span class="hljs-keyword">new</span> SparkConf().setMaster(<span class="hljs-string">"master"</span>).setAppName(<span class="hljs-string">"appName1"</span>)
<span class="hljs-keyword">val</span> sc = <span class="hljs-keyword">new</span> SparkContext(conf)  <span class="hljs-comment">//或者val sc = new SparkContext("master","appName1")</span></code></pre>

<p>解决方式：在该配置语句之前导入以下语句： <br>
import org.apache.spark.SparkConf <br>
同样的，在设置完SparkConf之后，需要设置SparkContext，这时需要导入: <br>
import org.apache.spark.SparkContext，才不至于出现error: not found: type SparkContext的错误。 <br>
在spark-shell中的运行过程如下：</p>

<pre class="prettyprint"><code class=" hljs avrasm">scala&gt; import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.SparkConf</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.SparkConf</span>

scala&gt; val conf = new SparkConf()<span class="hljs-preprocessor">.setMaster</span>(<span class="hljs-string">"master"</span>)<span class="hljs-preprocessor">.set</span>
<span class="hljs-keyword">set</span>      setAppName       setIfMissing   setMaster      
setAll   setExecutorEnv   setJars        setSparkHome   

scala&gt; val conf = new SparkConf()<span class="hljs-preprocessor">.setMaster</span>(<span class="hljs-string">"master"</span>)<span class="hljs-preprocessor">.setAppName</span>(<span class="hljs-string">"appName"</span>)
<span class="hljs-label">conf:</span> org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.SparkConf</span> = org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.SparkConf</span>@c7d173f

scala&gt; val sc = new SparkContext(conf)
&lt;console&gt;:<span class="hljs-number">26</span>: error: not found: type SparkContext
       val sc = new SparkContext(conf)
                    ^

scala&gt; import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.Spark</span>
SparkContext                    SparkExecutorInfoImpl   SparkStageInfo       
SparkDriverExecutionException   SparkFiles              SparkStageInfoImpl   
SparkEnv                        SparkFirehoseListener   SparkStatusTracker   
SparkException                  SparkJobInfo                                 
SparkExecutorInfo               SparkJobInfoImpl                             

scala&gt; import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.SparkContext</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.SparkContext</span></code></pre>

<p>注意：一旦设置完成SparkConf，就不可被修改</p>

<p><strong>对于单元测试，也可以调用SparkConf（false）来跳过加载外部设置，并获得相同的配置</strong>，无论系统属性如何。</p>

<h2 id="14-setmaster和setappname源码分析">1.4 setMaster（）和setAppName（）源码分析</h2>

<p><img src="https://img-blog.csdn.net/20180205164744350?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbG92ZTY2NjY2NnNoZW4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p>根据setMaster()setAppName()方法的代码及其注释，可以看出，<strong>setMaster主要是连接主节点，如果参数是”local”，则在本地用单线程运行spark，如果是 local[4]，则在本地用4核运行，如果设置为spark://master:7077，就是作为单节点运行，而setAppName就是在web端显示应用名而已，它们说到底都调用了set()函数</strong>，其定义如下： <br>
 <img src="https://img-blog.csdn.net/20180205165237414?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbG92ZTY2NjY2NnNoZW4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p>上面代码中的logDeprecation(key)是日志输出函数，防止输入参数名无效， 看看settings，是个HashMap结构，追溯一下：</p>

<pre class="prettyprint"><code class=" hljs vbnet"><span class="hljs-keyword">private</span> val settings = <span class="hljs-keyword">new</span> ConcurrentHashMap[<span class="hljs-built_in">String</span>, <span class="hljs-built_in">String</span>]()</code></pre>



<h2 id="15-concurrenthashmap">1.5 ConcurrentHashMap</h2>

<p>settings是个ConcurrentHashMap对象，<strong>ConcurrentHashMap主要作用是解决多线程并发下数据段访问效率，该类相对于hashMap而言具有同步map中的数据，对于hashTable而言，该同步数据对于并发程序在效率上有很大的提高，所以在使用缓存机制的时候如果对map中的值具有高并发的情况的话，那么我们就需要使用ConcurrentHashMap</strong>，ConcurrentHashMap中主要实体类有三个：ConcurrentHashMap（整个Hash表）,Segment（桶），HashEntry（节点） <br>
，CurrentHashMap的初始化一共有三个参数，一个initialCapacity，表示初始的容量，一个loadFactor，表示负载参数，最后一个是concurrentLevel，代表ConcurrentHashMap内部的Segment的数量，ConcurrentLevel一经指定，不可改变，这也是为什么SparkConf配置好了就无法更改的原因。</p>

<p><strong>ConcurrentHashMap应用了锁分段技术，HashTable容器在竞争激烈的并发环境下表现出效率低下，是因为所有访问HashTable的线程都必须竞争同一把锁，那假如容器里有多把锁，每一把锁用于锁容器其中一部分数据，那么当多线程访问容器里不同数据段的数据时，线程间就不会存在锁竞争，从而可以有效的提高并发访问效率，这就是ConcurrentHashMap所使用的锁分段技术，首先将数据分成一段一段的存储，然后给每一段数据配一把锁，当一个线程占用锁访问其中一个段数据的时候，其他段的数据也能被其他线程访问。</strong></p>

<p>另外，如果ConcurrentHashMap的元素数量增加导致ConrruentHashMap需要扩容，ConcurrentHashMap是不会增加Segment的数量的，而只会增加Segment中链表数组的容量大小，这样的好处是扩容过程不需要对整个ConcurrentHashMap做rehash，而只需要对Segment里面的元素做一次rehash就可以了。</p>



<h1 id="2-sparksession">2 SparkSession</h1>

<p>SparkSession： SparkSession实质上是SQLContext和HiveContext的组合（未来可能还会加上StreamingContext），所以在SQLContext和HiveContext上可用的API在SparkSession上同样是可以使用的。</p>



<h2 id="21-sparksession的简单示例">2.1 SparkSession的简单示例</h2>

<p>SparkSession内部封装了SparkContext，所以计算实际上是由SparkContext完成的。</p>

<pre class="prettyprint"><code class=" hljs avrasm">val sparkSession = SparkSession<span class="hljs-preprocessor">.builder</span>
                    <span class="hljs-preprocessor">.master</span>(<span class="hljs-string">"master"</span>)
                    <span class="hljs-preprocessor">.appName</span>(<span class="hljs-string">"appName"</span>)
                    <span class="hljs-preprocessor">.getOrCreate</span>()
//或者SparkSession<span class="hljs-preprocessor">.builder</span><span class="hljs-preprocessor">.config</span>(conf=SparkConf())</code></pre>

<p>上面代码类似于创建一个SparkContext，master设置为”master”，然后创建了一个SQLContext封装它。</p>



<h2 id="22-创建支持hive的sparksession">2.2 创建支持Hive的SparkSession</h2>

<p>如果你想创建hiveContext，可以使用下面的方法来创建SparkSession，以使得它支持Hive(HiveContext)：</p>

<pre class="prettyprint"><code class=" hljs avrasm">val sparkSession = SparkSession<span class="hljs-preprocessor">.builder</span>
                    <span class="hljs-preprocessor">.master</span>(<span class="hljs-string">"master"</span>)
                    <span class="hljs-preprocessor">.appName</span>(<span class="hljs-string">"appName"</span>)
                    <span class="hljs-preprocessor">.enableHiveSupport</span>()
                    <span class="hljs-preprocessor">.getOrCreate</span>()
//sparkSession 从csv读取数据：
val dq = sparkSession<span class="hljs-preprocessor">.read</span><span class="hljs-preprocessor">.option</span>(<span class="hljs-string">"header"</span>, <span class="hljs-string">"true"</span>)<span class="hljs-preprocessor">.csv</span>(<span class="hljs-string">"src/main/resources/scala.csv"</span>)</code></pre>

<p>上面代码中的getOrCreate()方法表示有就拿过来，没有就创建，类似于单例模式：</p>

<pre class="prettyprint"><code class=" hljs avrasm">val s1 = SparkSession()<span class="hljs-preprocessor">.builder</span><span class="hljs-preprocessor">.config</span>(<span class="hljs-string">"k1"</span>, <span class="hljs-string">"v1"</span>)<span class="hljs-preprocessor">.getOrCreat</span>()
val s2 = SparkSession()<span class="hljs-preprocessor">.builder</span><span class="hljs-preprocessor">.config</span>(<span class="hljs-string">"k2"</span>, <span class="hljs-string">"v2"</span>)<span class="hljs-preprocessor">.getOrCreat</span>()
return s1<span class="hljs-preprocessor">.conf</span><span class="hljs-preprocessor">.get</span>(<span class="hljs-string">"k1"</span>) == s2<span class="hljs-preprocessor">.conf</span><span class="hljs-preprocessor">.get</span>(<span class="hljs-string">"k2"</span>)</code></pre>



<h1 id="3-spark应用框架">3 Spark应用框架</h1>

<p><img src="https://img-blog.csdn.net/20180205173642830?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbG92ZTY2NjY2NnNoZW4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""> <br>
客户Spark程序（Driver Program）操作Spark集群是通过SparkContext对象来进行，SparkContext作为一个操作和调度的总入口，在初始化过程中集群管理器会创建DAGScheduler作业调度和TaskScheduler任务调度。 <br>
DAGScheduler作业调度模块是基于Stage的高层调度模块，DAG全称 Directed Acyclic Graph，有向无环图。简单的来说，就是一个由顶点和有方向性的边构成的图中，从任意一个顶点出发，没有任何一条路径会将其带回到出发的顶点。它为每个Spark Job计算具有依赖关系的多个Stage任务阶段（通常根据Shuffle来划分Stage，如groupByKey, reduceByKey等涉及到shuffle的transformation就会产生新的stage），然后将每个Stage划分为具体的一组任务，以TaskSets的形式提交给底层的任务调度模块来具体执行。TaskScheduler任务调度模块负责启动任务，监控和汇报任务运行情况。</p>

<p>创建SparkContext一般要经过下面几个步骤： <br>
a). 导入Spark的类和隐式转换 <br>
import org.apache.spark.{SparkContext, SparkConf} <br>
import org.apache.spark.SparkContext._</p>

<p>b). 构建Spark应用程序的应用信息对象SparkConf <br>
val conf = new SparkConf().setAppName(appName).setMaster(master_url)</p>

<p>c). 利用SparkConf对象来初始化SparkContext <br>
val sc = new SparkContext(conf)</p>

<p>d). 创建RDD、执行相应的Transformation和Action并得到最终结果。</p>

<p>e). 关闭Context <br>
在完成代码的编写和测试后，使用spark-submit来提交应用的jar包。spark-submit的命令行参考如下： <br>
<a href="http://spark.apache.org/docs/latest/submitting-applications.html" rel="nofollow" target="_blank">Submitting Applications</a></p>



<pre class="prettyprint"><code class=" hljs r">cd /usr/local/spark   <span class="hljs-comment">#进入spark安装目录，这里的/usr/local/spark为spark的安装目录</span>
./bin/spark-submit 
  --class &lt;main-class&gt;
  --master &lt;master-url&gt; 
  --deploy-mode &lt;deploy-mode&gt; 
  <span class="hljs-keyword">...</span> <span class="hljs-comment"># other options</span>
  &lt;application-jar&gt; 
  [application-arguments]</code></pre>

<p>Spark的运行模式取决于传递给SparkContext的master环境变量的值。master-url可以是以下任一种形式： <br>
local 使用一个Worker线程本地化运行Spark(完全不并行) <br>
local[*] 使用逻辑CPU数量的线程来本地化运行Spark <br>
local[K] 使用K个Worker线程本地化运行Spark（理想情况下，K应该根据运行机器的CPU核数设定） <br>
spark://HOST:PORT 连接到指定的Spark standalone master。默认端口是7077。 <br>
yarn-client 以客户端模式连接YARN集群。集群的位置可以在HADOOP_CONF_DIR 环境变量中找到。 <br>
yarn-cluster 以集群模式连接YARN集群。集群的位置可以在HADOOP_CONF_DIR 环境变量中找到。 <br>
mesos://HOST:PORT 连接到指定的Mesos集群。默认接口是5050. <br>
而spark-shell会在启动的时候自动构建SparkContext，名称为sc。</p>

<p>参考：</p>

<p><a href="https://www.cnblogs.com/Forever-Road/p/7351245.html" rel="nofollow">https://www.cnblogs.com/Forever-Road/p/7351245.html</a> <br>
<a href="http://blog.csdn.net/weipanp/article/details/45075125" rel="nofollow">http://blog.csdn.net/weipanp/article/details/45075125</a></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>