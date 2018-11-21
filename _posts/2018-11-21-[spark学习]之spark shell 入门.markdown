---
layout:     post
title:      [spark学习]之spark shell 入门
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>spark shell 是spark自带的一个快速原型开发的工具，在spark目录下面的bin目录下面，</p>
<p>1.进入spark shell ：</p>
<pre><code class="language-python">[hadoop@localhost bin]$ MASTER=spark://localhost:7077 ./spark-shell
</code></pre><br>
或者直接输入
<p></p>
<p></p>
<pre><code class="language-html">[hadoop@localhost bin]$  ./spark-shell
</code></pre><br><pre><code class="language-python">14/05/23 15:14:00 INFO spark.HttpServer: Starting HTTP Server
14/05/23 15:14:00 INFO server.Server: jetty-7.x.y-SNAPSHOT
14/05/23 15:14:00 INFO server.AbstractConnector: Started SocketConnector@0.0.0.0:53024
Welcome to
      ____              __
     / __/__  ___ _____/ /__
    _\ \/ _ \/ _ `/ __/  '_/
   /___/ .__/\_,_/_/ /_/\_\   version 0.9.1
      /_/

Using Scala version 2.10.3 (Java HotSpot(TM) 64-Bit Server VM, Java 1.7.0_55)
Type in expressions to have them evaluated.
Type :help for more information.
14/05/23 15:14:06 INFO slf4j.Slf4jLogger: Slf4jLogger started

14/05/23 15:14:08 INFO client.AppClient$ClientActor: Executor updated: app-20140523151407-0000/0 is now RUNNING
Created spark context..
Spark context available as sc.

scala&gt; 14/05/23 15:14:09 INFO cluster.SparkDeploySchedulerBackend: Registered executor: Actor[akka.tcp://sparkExecutor@localhost:52462/user/Executor#2047458293] with ID 0
14/05/23 15:14:10 INFO storage.BlockManagerMasterActor$BlockManagerInfo: Registering block manager localhost:51467 with 294.9 MB RAM


scala&gt; 
</code></pre><br>
出现scala表示进入成功!接下来就可以进行交互式的变成了  与python的命令行类似
<p></p>
<p><br></p>
<p>1：加载一个简单的文本文件</p>
<p>当我们通过spark shell 连接到一个存在的cluster上面的时候，spark会产生一个appid =app-20140523151407-0000  的   name=spark shell 的spark任务，可以通过spark自带的web ui看到 默认端口是8080 如下图所示：</p>
<p><img src="https://img-blog.csdn.net/20140523152331906?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3VuZmxvd2VyX2Nhbw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""></p>
<p>说明我们已经成功链接到spark cluster上面，</p>
<p>现在可以下载数据集来做各种实验了</p>
<p>这里可以下载The Elements of Statistical Learning的数据集，</p>
<p></p>
<pre><code class="language-python">wget http://www-stat.stanford.edu/~tibs/ElemStatLearn/datasets/spam.data
</code></pre><br><br>
http://statweb.stanford.edu/~tibs/ElemStatLearn/datasets/   在这个网址下面有spam.info.txt，顾名思义，这个是spam数据集的解释，有兴趣的可以看下
<p></p>
<p>数据失效的可以来这里下载http://download.csdn.net/detail/sunflower_cao/7390523</p>
<p>1.1  load数据集 <br></p>
<p></p>
<pre><code class="language-python">scala&gt; val inFile = sc.textFile("/home/hadoop/sparkData/spam.data");
14/05/23 15:46:46 INFO storage.MemoryStore: ensureFreeSpace(140074) called with curMem=0, maxMem=309225062
14/05/23 15:46:46 INFO storage.MemoryStore: Block broadcast_0 stored as values to memory (estimated size 136.8 KB, free 294.8 MB)
inFile: org.apache.spark.rdd.RDD[String] = MappedRDD[1] at textFile at &lt;console&gt;:12
scala&gt; println(inFile)
MappedRDD[1] at textFile at &lt;console&gt;:12

 </code></pre><br>
sc是在进入spark shell 时候创建一个spark content  就是spark上下文的意思，val 是scala语法中声明常量的方式，通过println我们可以看到读入的文件被处理成一个MappedRDD的对象 mapred相信学过hadoop的人都不会陌生，RDD是Resilient Distributed Datasets，是一种弹性分布式数据集。<br><p></p>
<p>这个sc.textFile的操作就把spam.data的数据load到内存中了，另外我们可以通过下面的方式载入数据集</p>
<p></p>
<pre><code class="language-python">scala&gt; import org.apache.spark.SparkFiles;
scala&gt; val file = sc.addFile("spam.data")
scala&gt; val inFile = sc.textFile(SparkFiles.get("spam.data"))
</code></pre><br>
接下来我们要使用spark来处理这个数据集。
<p></p>
<p>初始载入的数据spark会默认认为是每行为一个字符串，这里我们需要自己将字符串变换成数值类型的。</p>
<p></p>
<pre><code class="language-python">scala&gt; val nums = inFile.map(x =&gt; x.split(' ').map(_.toDouble))
nums: org.apache.spark.rdd.RDD[Array[Double]] = MappedRDD[2] at map at &lt;console&gt;:14
</code></pre>
<p></p>
<p>可以看到执行的结果显示nums是一个double的数组，可以通过如下命令查看nums的内容：</p>
<p></p>
<pre><code class="language-python">scala&gt; inFile.first()
14/05/23 16:07:12 INFO mapred.FileInputFormat: Total input paths to process : 1
14/05/23 16:07:12 INFO spark.SparkContext: Starting job: first at &lt;console&gt;:15
14/05/23 16:07:12 INFO scheduler.DAGScheduler: Got job 0 (first at &lt;console&gt;:15) with 1 output partitions (allowLocal=true)
14/05/23 16:07:12 INFO scheduler.DAGScheduler: Final stage: Stage 0 (first at &lt;console&gt;:15)
14/05/23 16:07:12 INFO scheduler.DAGScheduler: Parents of final stage: List()
14/05/23 16:07:12 INFO scheduler.DAGScheduler: Missing parents: List()
14/05/23 16:07:12 INFO scheduler.DAGScheduler: Computing the requested partition locally
14/05/23 16:07:12 INFO rdd.HadoopRDD: Input split: hdfs://localhost:9000/user/hadoop/spam.data:0+349170
14/05/23 16:07:12 INFO spark.SparkContext: Job finished: first at &lt;console&gt;:15, took 0.171142196 s
res7: String = 0 0.64 0.64 0 0.32 0 0 0 0 0 0 0.64 0 0 0 0.32 0 1.29 1.93 0 0.96 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0.778 0 0 3.756 61 278 1

scala&gt; val nums = inFile.map(x =&gt; x.split(' ').map(_.toDouble))
nums: org.apache.spark.rdd.RDD[Array[Double]] = MappedRDD[10] at map at &lt;console&gt;:14

scala&gt; nums.first()
14/05/23 16:07:40 INFO spark.SparkContext: Starting job: first at &lt;console&gt;:17
14/05/23 16:07:40 INFO scheduler.DAGScheduler: Got job 1 (first at &lt;console&gt;:17) with 1 output partitions (allowLocal=true)
14/05/23 16:07:40 INFO scheduler.DAGScheduler: Final stage: Stage 1 (first at &lt;console&gt;:17)
14/05/23 16:07:40 INFO scheduler.DAGScheduler: Parents of final stage: List()
14/05/23 16:07:40 INFO scheduler.DAGScheduler: Missing parents: List()
14/05/23 16:07:40 INFO scheduler.DAGScheduler: Computing the requested partition locally
14/05/23 16:07:40 INFO rdd.HadoopRDD: Input split: hdfs://localhost:9000/user/hadoop/spam.data:0+349170
14/05/23 16:07:40 INFO spark.SparkContext: Job finished: first at &lt;console&gt;:17, took 0.016749499 s
res8: Array[Double] = Array(0.0, 0.64, 0.64, 0.0, 0.32, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.64, 0.0, 0.0, 0.0, 0.32, 0.0, 1.29, 1.93, 0.0, 0.96, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.778, 0.0, 0.0, 3.756, 61.0, 278.0, 1.0)

scala&gt; 
</code></pre><br>
比较可以发现转换前后的数据是一致的，
<p></p>
<p>下面我们在spark shel上面运行一个简单的逻辑回归：<br></p>
<p>1.引入向量</p><pre><code class="language-python">scala&gt; import org.apache.spark.util.Vector
import org.apache.spark.util.Vector
</code></pre>2。定义class
<p></p><pre><code class="language-python">scala&gt; case class DataPoint(x:Vector,y:Double) 
defined class DataPoint
</code></pre>3.方法定义
<p></p><pre><code class="language-python">scala&gt; def parsePoint(x:Array[Double]):DataPoint = { 
     | DataPoint(new Vector(x.slice(0,x.size-2)),x(x.size-1))
     | }
parsePoint: (x: Array[Double])DataPoint

scala&gt; val points = nums.map(parsePoint(_))
points: org.apache.spark.rdd.RDD[DataPoint] = MappedRDD[3] at map at &lt;console&gt;:21


scala&gt; import java.util.Random
import java.util.Random

scala&gt; val rand = new Random
rand: java.util.Random = java.util.Random@3432d918

scala&gt; print(rand)
java.util.Random@3432d918
scala&gt; var w = Vector(nums.first.size-2,_=&gt;rand.nextDouble)
14/05/23 17:35:21 INFO spark.SparkContext: Starting job: first at &lt;console&gt;:20
14/05/23 17:35:21 INFO scheduler.DAGScheduler: Got job 2 (first at &lt;console&gt;:20) with 1 output partitions (allowLocal=true)
14/05/23 17:35:21 INFO scheduler.DAGScheduler: Final stage: Stage 2 (first at &lt;console&gt;:20)
14/05/23 17:35:21 INFO scheduler.DAGScheduler: Parents of final stage: List()
14/05/23 17:35:21 INFO scheduler.DAGScheduler: Missing parents: List()
14/05/23 17:35:21 INFO scheduler.DAGScheduler: Computing the requested partition locally
14/05/23 17:35:21 INFO rdd.HadoopRDD: Input split: hdfs://localhost:9000/user/hadoop/spam.data:0+698341
14/05/23 17:35:21 INFO spark.SparkContext: Job finished: first at &lt;console&gt;:20, took 0.016991332 s
w: org.apache.spark.util.Vector = (0.952221852788799, 0.9714597116349201, 0.7208915588991835, 0.07589933961475825, 
0.2667808507956261, 0.3948288310305056, 0.6639668214583728, 0.0177678883648561, 0.7834825789466021, 0.21619962601820275, 
0.7689006905185577, 0.23899675994782543, 0.19701920708655185, 0.24938329746255294, 0.5238641715751765, 0.4743887082787235,
 0.41753899708170594, 0.14508060129237388, 0.6086603124713224, 0.6482106767570478, 0.7518858516034792, 0.5820085883501841,
 0.9346525771103898, 0.4710559310737781, 0.22861595032021675, 0.6234485440108845, 0.5329532903683406, 0.6494084790081912,
 0.7748294082927212, 0.16136533243961715, 0.8197109926984887, 0.06414167157536643, 0.6022697329934645, 0.058724026894469095,
 0.5573752106213121, 0.7282840400102886, 0.3408071617768965, 0.070356518...
scala&gt; val iteration = 100
iteration: Int = 100

scala&gt; import scala.math._
import scala.math._

scala&gt; for(i&lt;-1 to iterations){
     | val gradient = points.map(p =&gt;
     |  (1 / (1+exp(-p.y*(w dot p.x)))-1)*p.y*p.x
     | ).reduce(_ + _)
     | w -= gradient
     | }


14/05/23 17:50:23 INFO scheduler.DAGScheduler: Stage 102 (reduce at &lt;console&gt;:37) finished in 0.084 s
14/05/23 17:50:23 INFO spark.SparkContext: Job finished: reduce at &lt;console&gt;:37, took 0.090548155 s

scala&gt; w
res3: org.apache.spark.util.Vector = (0.7291462605526978, 0.8011493694345105, 0.6632462451894483, 0.9783179057774432, 
0.5894806547559924, 0.46413037169154797, 0.5352673058138914, 0.04151002242309652, 0.3074579788453562, 0.8554814465008911,
 0.8421319858358445, 0.723306806645535, 0.24382860800094663, 0.17140711871915207, 0.5006326041454038, 0.9408116975991101,
 0.7739239734124745, 0.790122616980566, 0.9701103050755487, 0.4106048776506287, 0.8098841935066842, 0.16512808143309984, 
0.18074648984915714, 0.3268703791115973, 0.28167747744431826, 0.20995838053594057, 0.5823059390134736, 0.4489520120935588,
0.44030859962613983, 0.6419368289264852, 0.5191533895589641, 0.43170678028084863, 0.9237602493794835, 0.5175019655845293,
 0.4800004611303587, 0.2587440164596575, 0.020567743652946585, 0.1855540...
scala&gt; 


 </code></pre><br>
心情有点小激动呢 没想到用java写动辙几十个类的程序被scala这么容易的解决了 <br><p><br></p>
<p>大家看书的时候注意 import spark.util.Vector这个类的时候会出错 是因为spark现在已经是apache的顶级项目了，包名发生了改变 import org.apache.spark.util.Vector就可以了</p>
<p></p><pre><code class="language-python">scala&gt; import spark.util.Vector
&lt;console&gt;:15: error: not found: value spark
       import spark.util.Vector
              ^

scala&gt; import org.apache.spark.util.Vector
import org.apache.spark.util.Vector
</code></pre><br><br>            </div>
                </div>