---
layout:     post
title:      Spark Streaming——DStream Transformation操作
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="spark-streamingdstream-transformation操作">Spark Streaming——DStream Transformation操作</h1>

<p>Spark的各个子框架都是基于spark core的，Spark Streaming在内部的处理机制是，接收实时流的数据，并根据一定的时间间隔拆分成一批数据，然后通过Spark Engine处理这些批数据，最终得到处理后的一批结果数据。</p>

<p>对应的批数据，在spark内核对应一个RDD实例，因此，对应流数据的DStream可以看成是一组RDD，即RDD的一个序列。通俗点理解，在流数据分成一批批后，通过一个先入先出的队列，然后Spark Engine从该队列中依次取出一个个批数据，然后把批数据封装成一个RDD，然后进行处理，这是一个典型的生产消费者模型。</p>



<h2 id="1-术语定义">1 术语定义</h2>

<ul>
<li>离散流(discretized stream或DStream) : 这是Spark STreaming对内部持续的实时数据流的抽象描述，即我们处理的一个实时数据流，在spark Streaming中对应一个DStream实例</li>
<li>批数据(batch data) : 这是化整为零的第一步，将实时流数据以时间片为单位进行分批，将流处理转换为时间片数据的批处理。随着持续时间的推移，这些处理结果就形成了对应的结果数据流。</li>
<li>时间片或批处理时间间隔(batch interval) : 这是程序员对六数据进行定量的标准，以时间片作为我们拆分流数据的依据。一个时间片的数据对应一个RDD实例。</li>
<li>窗口长度(window length) : 一个窗口覆盖的六数据的时间长度。必须是批处理时间间隔的倍数。</li>
<li>滑动时间间隔 : 前一个窗口到后一个窗口所经过的时间长度。必须是批处理时间间隔的背书。</li>
<li>Input DStream : 一个Dstream是一个特殊的DStream，将spark Streaming连接到一个外部数据源来读取数据</li>
</ul>



<h2 id="2-dstream-transformation操作">2 Dstream Transformation操作</h2>



<h3 id="21-函数说明">2.1 函数说明</h3>

<table>
<thead>
<tr>
  <th>Transformaiton</th>
  <th>Meaning</th>
</tr>
</thead>
<tbody><tr>
  <td>map(func)</td>
  <td>对DStream中的各个元素进行func函数操作，然后返回一个新的DStream.</td>
</tr>
<tr>
  <td>flatMap(func)</td>
  <td>与map方法类似，只不过各个输入项可以被输出为零个或多个输出项</td>
</tr>
<tr>
  <td>filter(func)</td>
  <td>过滤出所有函数func返回值为true的DStream元素并返回一个新的DStream</td>
</tr>
<tr>
  <td>repartition(numPartitions)</td>
  <td>增加或减少DStream中的分区数，从而改变DStream的并行度</td>
</tr>
<tr>
  <td>union(otherStream)</td>
  <td>将源DStream和输入参数为otherDStream的元素合并，并返回一个新的DStream.</td>
</tr>
<tr>
  <td>count()</td>
  <td>通过对DStreaim中的各个RDD中的元素进行计数，然后返回只有一个元素的RDD构成的DStream</td>
</tr>
<tr>
  <td>reduce(func)</td>
  <td>对源DStream中的各个RDD中的元素利用func进行聚合操作，然后返回只有一个元素的RDD构成的新的DStream.</td>
</tr>
<tr>
  <td>countByValue()</td>
  <td>对于元素类型为K的DStream，返回一个元素为（K,Long）键值对形式的新的DStream，Long对应的值为源DStream中各个RDD的key出现的次数</td>
</tr>
<tr>
  <td>reduceByKey(func, [numTasks])</td>
  <td>利用func函数对源DStream中的key进行聚合操作，然后返回新的（K，V）对构成的DStream</td>
</tr>
<tr>
  <td>join(otherStream, [numTasks])</td>
  <td>输入为（K,V)、（K,W）类型的DStream，返回一个新的（K，（V，W）类型的DStream</td>
</tr>
<tr>
  <td>cogroup(otherStream, [numTasks])</td>
  <td>输入为（K,V)、（K,W）类型的DStream，返回一个新的 (K, Seq[V], Seq[W]) 元组类型的DStream</td>
</tr>
<tr>
  <td>transform(func)</td>
  <td>通过RDD-to-RDD函数作用于源码DStream中的各个RDD，可以是任意的RDD操作，从而返回一个新的RDD</td>
</tr>
<tr>
  <td>updateStateByKey(func)</td>
  <td>根据于key的前置状态和key的新值，对key进行更新，返回一个新状态的DStream</td>
</tr>
</tbody></table>




<h3 id="22-实例">2.2 实例</h3>



<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-comment">//读取本地文件~/streaming文件夹</span>
<span class="hljs-keyword">val</span> lines = ssc.textFileStream(args(<span class="hljs-number">0</span>))
<span class="hljs-keyword">val</span> words = lines.flatMap(_.split(<span class="hljs-string">" "</span>))
<span class="hljs-keyword">val</span> wordMap = words.map(x =&gt; (x, <span class="hljs-number">1</span>))
<span class="hljs-keyword">val</span> wordCounts=wordMap.reduceByKey(_ + _)
<span class="hljs-keyword">val</span> filteredWordCounts=wordCounts.filter(_._2&gt;<span class="hljs-number">1</span>)
<span class="hljs-keyword">val</span> numOfCount=filteredWordCounts.count()
<span class="hljs-keyword">val</span> countByValue=words.countByValue()
<span class="hljs-keyword">val</span> union=words.union(word1)
<span class="hljs-keyword">val</span> transform=words.transform(x=&gt;x.map(x=&gt;(x,<span class="hljs-number">1</span>)))
<span class="hljs-comment">//显式原文件</span>
lines.print()
<span class="hljs-comment">//打印flatMap结果</span>
words.print()
<span class="hljs-comment">//打印map结果</span>
wordMap.print()
<span class="hljs-comment">//打印reduceByKey结果</span>
wordCounts.print()
<span class="hljs-comment">//打印filter结果</span>
filteredWordCounts.print()
<span class="hljs-comment">//打印count结果</span>
numOfCount.print()
<span class="hljs-comment">//打印countByValue结果</span>
countByValue.print()
<span class="hljs-comment">//打印union结果</span>
union.print()
<span class="hljs-comment">//打印transform结果</span>
transform.print()</code></pre>

<p>执行结果如下：</p>



<pre class="prettyprint"><code class=" hljs asciidoc"><span class="hljs-code">-------------------------------------------
lines.print()
-------------------------------------------</span>
A B C D
A B

<span class="hljs-code">-------------------------------------------
flatMap结果
-------------------------------------------</span>
A
B
C
D
A
B

<span class="hljs-code">-------------------------------------------
map结果
-------------------------------------------</span>
(A,1)
(B,1)
(C,1)
(D,1)
(A,1)
(B,1)

<span class="hljs-code">-------------------------------------------
reduceByKey结果
-------------------------------------------</span>
(B,2)
(D,1)
(A,2)
(C,1)


<span class="hljs-code">-------------------------------------------
filter结果
-------------------------------------------</span>
(B,2)
(A,2)

<span class="hljs-code">-------------------------------------------
count结果
-------------------------------------------</span>
2

<span class="hljs-code">-------------------------------------------
countByValue结果
-------------------------------------------</span>
(B,2)
(D,1)
(A,2)
(C,1)

<span class="hljs-code">-------------------------------------------
union结果
-------------------------------------------</span>
A
B
C
D
A
B
A
B
C
D
<span class="hljs-bullet">...

</span><span class="hljs-code">-------------------------------------------
transform结果
-------------------------------------------</span>
(A,1)
(B,1)
(C,1)
(D,1)
(A,1)
(B,1)</code></pre>



<h2 id="3-窗口转换操作">3 窗口转换操作</h2>

<p>Spark streaming提供窗口操作(window option),如下图所示：</p>

<p><img src="https://img-blog.csdn.net/20151129130430993" alt="" title=""></p>

<p>上图中，红色实线表示窗口当前的滑动位置，虚线表示前一次窗口位置，窗口每滑动一次，落在该窗口中的RDD被一起同时处理，生成一个窗口DStream（windowed DStream），窗口操作需要设置两个参数：</p>

<ul>
<li>窗口长度（window length），即窗口的持续时间，上图中的窗口长度为3</li>
<li>滑动间隔（sliding interval），窗口操作执行的时间间隔，上图中的滑动间隔为2</li>
</ul>

<p>这两个参数必须是原始DStream 批处理间隔（batch interval）的整数倍（上图中的原始DStream的batch interval为1）</p>



<h2 id="31-函数说明">3.1 函数说明</h2>

<table>
<thead>
<tr>
  <th>Transformation</th>
  <th>Meaning</th>
</tr>
</thead>
<tbody><tr>
  <td>window(windowLength, slideInterval)</td>
  <td>返回一个基于源DStream的窗口批次计算后得到新的DStream。</td>
</tr>
<tr>
  <td>countByWindow(windowLength,slideInterval)</td>
  <td>返回基于滑动窗口的DStream中的元素的数量。</td>
</tr>
<tr>
  <td>reduceByWindow(func, windowLength,slideInterval)</td>
  <td>基于滑动窗口对源DStream中的元素进行聚合操作，得到一个新的DStream</td>
</tr>
<tr>
  <td>reduceByKeyAndWindow(func,windowLength, slideInterval, [numTasks])</td>
  <td>基于滑动窗口对（K，V）键值对类型的DStream中的值按K使用聚合函数func进行聚合操作，得到一个新的DStream。</td>
</tr>
<tr>
  <td>reduceByKeyAndWindow(func, invFunc,windowLength, slideInterval, [numTasks])</td>
  <td>一个更高效的reduceByKkeyAndWindow()的实现版本，先对滑动窗口中新的时间间隔内数据增量聚合并移去最早的与新增数据量的时间间隔内的数据统计量。例如，计算t+4秒这个时刻过去5秒窗口的WordCount，那么我们可以将t+3时刻过去5秒的统计量加上[t+3，t+4]的统计量，在减去[t-2，t-1]的统计量，这种方法可以复用中间三秒的统计量，提高统计的效率</td>
</tr>
<tr>
  <td>countByValueAndWindow(windowLength,slideInterval, [numTasks])</td>
  <td>基于滑动窗口计算源DStream中每个RDD内每个元素出现的频次并返回DStream[(K,Long)]，其中K是RDD中元素的类型，Long是元素频次。与countByValue一样，reduce任务的数量可以通过一个可选参数进行配置。</td>
</tr>
</tbody></table>


<p>对于窗口操作，批处理间隔、窗口间隔和滑动间隔是非常重要的三个时间概念，是理解窗口操作的关键所在。</p>



<h3 id="32-代码示例">3.2 代码示例</h3>



<pre class="prettyprint"><code class=" hljs avrasm">// WindowWordCount——reduceByKeyAndWindow方法使用
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span>.{SparkContext, SparkConf}
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.storage</span><span class="hljs-preprocessor">.StorageLevel</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.streaming</span>._
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.streaming</span><span class="hljs-preprocessor">.StreamingContext</span>._

object WindowWordCount {
  def main(args: Array[String]) {
    //传入的参数为localhost <span class="hljs-number">9999</span> <span class="hljs-number">30</span> <span class="hljs-number">10</span>
    if (args<span class="hljs-preprocessor">.length</span> != <span class="hljs-number">4</span>) {
      System<span class="hljs-preprocessor">.err</span><span class="hljs-preprocessor">.println</span>(<span class="hljs-string">"Usage: WindowWorldCount &lt;hostname&gt; &lt;port&gt; &lt;windowDuration&gt; &lt;slideDuration&gt;"</span>)
      System<span class="hljs-preprocessor">.exit</span>(<span class="hljs-number">1</span>)
    }
    StreamingExamples<span class="hljs-preprocessor">.setStreamingLogLevels</span>()

    val conf = new SparkConf()<span class="hljs-preprocessor">.setAppName</span>(<span class="hljs-string">"WindowWordCount"</span>)<span class="hljs-preprocessor">.setMaster</span>(<span class="hljs-string">"local[4]"</span>)
    val sc = new SparkContext(conf)

    // 创建StreamingContext，batch interval为<span class="hljs-number">5</span>秒
    val ssc = new StreamingContext(sc, Seconds(<span class="hljs-number">5</span>))


    //Socket为数据源
    val lines = ssc<span class="hljs-preprocessor">.socketTextStream</span>(args(<span class="hljs-number">0</span>), args(<span class="hljs-number">1</span>)<span class="hljs-preprocessor">.toInt</span>, StorageLevel<span class="hljs-preprocessor">.MEMORY</span>_ONLY_SER)

    val words = lines<span class="hljs-preprocessor">.flatMap</span>(_<span class="hljs-preprocessor">.split</span>(<span class="hljs-string">" "</span>))

    // windows操作，对窗口中的单词进行计数
    val wordCounts = words<span class="hljs-preprocessor">.map</span>(<span class="hljs-built_in">x</span> =&gt; (<span class="hljs-built_in">x</span> , <span class="hljs-number">1</span>))<span class="hljs-preprocessor">.reduceByKeyAndWindow</span>((a:Int,b:Int) =&gt; (a + b), Seconds(args(<span class="hljs-number">2</span>)<span class="hljs-preprocessor">.toInt</span>), Seconds(args(<span class="hljs-number">3</span>)<span class="hljs-preprocessor">.toInt</span>))

    wordCounts<span class="hljs-preprocessor">.print</span>()
    ssc<span class="hljs-preprocessor">.start</span>()
    ssc<span class="hljs-preprocessor">.awaitTermination</span>()
  }
}</code></pre>

<p>通过下列代码启动netcat server</p>



<pre class="prettyprint"><code class=" hljs ruby">root<span class="hljs-variable">@sparkmaster</span><span class="hljs-symbol">:~</span><span class="hljs-comment"># nc -lk 9999</span>
<span class="hljs-constant">Spark</span> is a fast <span class="hljs-keyword">and</span> general cluster computing system <span class="hljs-keyword">for</span> <span class="hljs-constant">Big</span> <span class="hljs-constant">Data</span>. <span class="hljs-constant">It</span> provides</code></pre>

<p>执行结果如下：</p>



<pre class="prettyprint"><code class=" hljs asciidoc"><span class="hljs-code">-------------------------------------------
Time: 1448778805000 ms（10秒，第一个滑动窗口时间）
-------------------------------------------</span>
(provides,1)
(is,1)
(general,1)
(Big,1)
(fast,1)
(cluster,1)
(Data.,1)
(computing,1)
(Spark,1)
(a,1)
<span class="hljs-bullet">...

</span><span class="hljs-code">-------------------------------------------
Time: 1448778815000 ms（10秒后，第二个滑动窗口时间）
-------------------------------------------</span>
(provides,1)
(is,1)
(general,1)
(Big,1)
(fast,1)
(cluster,1)
(Data.,1)
(computing,1)
(Spark,1)
(a,1)
<span class="hljs-bullet">...

</span><span class="hljs-code">-------------------------------------------
Time: 1448778825000 ms（10秒后，第三个滑动窗口时间）
-------------------------------------------</span>
(provides,1)
(is,1)
(general,1)
(Big,1)
(fast,1)
(cluster,1)
(Data.,1)
(computing,1)
(Spark,1)
(a,1)
<span class="hljs-bullet">...

</span><span class="hljs-code">-------------------------------------------
Time: 1448778835000 ms（再经10秒后，超出window length窗口长度，不在计数范围内）
-------------------------------------------</span>

<span class="hljs-code">-------------------------------------------
Time: 1448778845000 ms
-------------------------------------------</span></code></pre>



<h2 id="4-输出操作">4 输出操作</h2>

<p>Spark Streaming允许DStream的数据被输出到外部系统，如数据库或文件系统。由于输出操作实际上使transformation操作后的数据可以通过外部系统被使用，同时输出操作触发所有DStream的transformation操作的实际执行（类似于RDD操作）。以下表列出了目前主要的输出操作：</p>

<table>
<thead>
<tr>
  <th>函数</th>
  <th>解释</th>
</tr>
</thead>
<tbody><tr>
  <td>print()</td>
  <td>在Driver中打印出DStream中数据的前10个元素。</td>
</tr>
<tr>
  <td>saveAsTextFiles(prefix, [suffix])</td>
  <td>将DStream中的内容以文本的形式保存为文本文件，其中每次批处理间隔内产生的文件以prefix-TIME_IN_MS[.suffix]的方式命名</td>
</tr>
<tr>
  <td>saveAsObjectFiles(prefix, [suffix])</td>
  <td>将DStream中的内容按对象序列化并且以SequenceFile的格式保存。其中每次批处理间隔内产生的文件以prefix-TIME_IN_MS[.suffix]的方式命名。</td>
</tr>
<tr>
  <td>saveAsHadoopFiles(prefix, [suffix])</td>
  <td>将DStream中的内容以文本的形式保存为Hadoop文件，其中每次批处理间隔内产生的文件以prefix-TIME_IN_MS[.suffix]的方式命名。</td>
</tr>
<tr>
  <td>foreachRDD(func)</td>
  <td>最基本的输出操作，将func函数应用于DStream中的RDD上，这个操作会输出数据到外部系统，比如保存RDD到文件或者网络数据库等。需要注意的是func函数是在运行该streaming应用的Driver进程里执行的。</td>
</tr>
</tbody></table>


<h2 id="参考博文">参考博文</h2>

<p><a href="http://www.cnblogs.com/shishanyuan/p/4747735.html" rel="nofollow" target="_blank">http://www.cnblogs.com/shishanyuan/p/4747735.html</a></p>

<p><a href="https://yq.aliyun.com/articles/60316?spm=5176.8251999.569296.76.20f9f017kExcvc#" rel="nofollow" target="_blank">https://yq.aliyun.com/articles/60316?spm=5176.8251999.569296.76.20f9f017kExcvc#</a></p>

<p><a href="https://yq.aliyun.com/articles/60315?spm=5176.8251999.569296.77.20f9f017XgNbIM" rel="nofollow" target="_blank">https://yq.aliyun.com/articles/60315?spm=5176.8251999.569296.77.20f9f017XgNbIM</a></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>