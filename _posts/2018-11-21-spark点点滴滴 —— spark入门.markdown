---
layout:     post
title:      spark点点滴滴 —— spark入门
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>spark由于其良好的生态，比hadoop快地速度，批处理，流式处理等多操作的支持，现在在大数据圈已经非常热门。</p>



<h1 id="spark初了解">spark初了解</h1>

<p>spark经常拿来和hadoop作比较，hadoop的MapReduce，是一个简单的Mapper和Reducer的抽象提供一个编程模型，可以在hadoop集群上并发地，分布式地处理大量的数据集，而把并发、分布式（如机器间通信）和故障恢复等计算细节隐藏起来。 <br>
但技术总是在进步，MapRecue存在的一些局限，使用起来比较困难。比如只提供两个操作，Map和Reduce，表达力欠缺；一个Job只有Map和Reduce两个阶段（Phase），复杂的计算需要大量的Job完成，Job之间的依赖关系是由开发者自己管理的。中间结果也放在HDFS文件系统中，导致延时很大；只适用Batch数据处理，对于交互式数据处理，实时数据处理的支持不够；对于迭代式数据处理性能比较差等。 <br>
而spark呢，Spark是一个新兴的大数据处理的引擎。你可以理解为spark解决了hadoop的MR任务的很多痛点。 <br>
spark提出的是“One Stack To Rule Them All”，想让spark囊括一切，可见spark的野心不小呀。看看spark支持的技术栈： <br>
<img src="https://img-blog.csdn.net/20160908103455704" alt="这里写图片描述" title=""> <br>
要了解spark，得先说说spark的RDD，这是理解spark的一个核心概念，也是spark对集群中分布式内存的一个抽象，RDD全称是Resilient Distributed Dataset，中文即“弹性分布式数据集”，RDD就是一个不可变的带分区的记录集合，RDD也是Spark中的编程模型，spark一切的操作都是在RDD数据集合上的操作。</p>



<h1 id="spark初体验">spark初体验</h1>

<p>要了解一门技术，不去动手体验是学不好的，庆幸的是，spark提供了一个spark-shell，让我们不必一开始就必须学习api才能跑一个spark任务，而是可以从spark-shell交互式的去体验一个spark任务。 <br>
如何使用spark-shell，当然，首先你需要安装spark集群，此处就不再多说，我是<a href="http://blog.csdn.net/qq_35799003/article/details/52164861" rel="nofollow">通过cloudera manager来安装spark</a>的。 <br>
下面开启我们的spark体验之旅： <br>
在spark集群机器上输入spark-shell <br>
启动会打印相关日志，也可以看到下面一个图片： <br>
<img src="https://img-blog.csdn.net/20160908141730673" alt="这里写图片描述" title=""> <br>
启动完成可以看到输入提示： <br>
scala&gt; <br>
至此，spark启动完成，下面以一个word count来开始体验一下吧。 <br>
首先创建一个text.txt文件，上传到hdfs上（hadoop fs -put test.txt /tmp），内容：</p>



<pre class="prettyprint"><code class=" hljs mathematica"><span class="hljs-keyword">I</span> love spark very very very much!</code></pre>

<p><strong>scala&gt;</strong> val textFile = sc.textFile(“hdfs://hostname:8020/tmp/test.txt”) <br>
显示以下内容：</p>



<pre class="prettyprint"><code class=" hljs livecodeserver"><span class="hljs-number">16</span>/<span class="hljs-number">09</span>/<span class="hljs-number">08</span> <span class="hljs-number">14</span>:<span class="hljs-number">45</span>:<span class="hljs-number">54</span> INFO MemoryStore: ensureFreeSpace(<span class="hljs-number">276138</span>) called <span class="hljs-operator">with</span> curMem=<span class="hljs-number">297273</span>, maxMem=<span class="hljs-number">278302556</span>
<span class="hljs-number">16</span>/<span class="hljs-number">09</span>/<span class="hljs-number">08</span> <span class="hljs-number">14</span>:<span class="hljs-number">45</span>:<span class="hljs-number">54</span> INFO MemoryStore: Block broadcast_1 stored <span class="hljs-keyword">as</span> values <span class="hljs-operator">in</span> memory (estimated size <span class="hljs-number">269.7</span> KB, free <span class="hljs-number">264.9</span> MB)
<span class="hljs-number">16</span>/<span class="hljs-number">09</span>/<span class="hljs-number">08</span> <span class="hljs-number">14</span>:<span class="hljs-number">45</span>:<span class="hljs-number">54</span> INFO MemoryStore: ensureFreeSpace(<span class="hljs-number">21207</span>) called <span class="hljs-operator">with</span> curMem=<span class="hljs-number">573411</span>, maxMem=<span class="hljs-number">278302556</span>
<span class="hljs-number">16</span>/<span class="hljs-number">09</span>/<span class="hljs-number">08</span> <span class="hljs-number">14</span>:<span class="hljs-number">45</span>:<span class="hljs-number">54</span> INFO MemoryStore: Block broadcast_1_piece0 stored <span class="hljs-keyword">as</span> <span class="hljs-keyword">bytes</span> <span class="hljs-operator">in</span> memory (estimated size <span class="hljs-number">20.7</span> KB, free <span class="hljs-number">264.8</span> MB)
<span class="hljs-number">16</span>/<span class="hljs-number">09</span>/<span class="hljs-number">08</span> <span class="hljs-number">14</span>:<span class="hljs-number">45</span>:<span class="hljs-number">54</span> INFO BlockManagerInfo: Added broadcast_1_piece0 <span class="hljs-operator">in</span> memory <span class="hljs-command"><span class="hljs-keyword">on</span> <span class="hljs-title">xxxxx</span>:<span class="hljs-title">18142</span> (<span class="hljs-title">size</span>: <span class="hljs-title">20</span><span class="hljs-number">.7</span> <span class="hljs-title">KB</span>, <span class="hljs-title">free</span>: <span class="hljs-title">265</span><span class="hljs-number">.4</span> <span class="hljs-title">MB</span>)</span>
<span class="hljs-number">16</span>/<span class="hljs-number">09</span>/<span class="hljs-number">08</span> <span class="hljs-number">14</span>:<span class="hljs-number">45</span>:<span class="hljs-number">54</span> INFO BlockManagerMaster: Updated info <span class="hljs-operator">of</span> block broadcast_1_piece0
<span class="hljs-number">16</span>/<span class="hljs-number">09</span>/<span class="hljs-number">08</span> <span class="hljs-number">14</span>:<span class="hljs-number">45</span>:<span class="hljs-number">54</span> INFO SparkContext: Created broadcast <span class="hljs-number">1</span> <span class="hljs-built_in">from</span> textFile <span class="hljs-keyword">at</span> &lt;console&gt;:<span class="hljs-number">21</span>
textFile: org.apache.spark.rdd.RDD[String] = hdfs://hostnamexxxx:<span class="hljs-number">8020</span>/tmp/test.txt MapPartitionsRDD[<span class="hljs-number">3</span>] <span class="hljs-keyword">at</span> textFile <span class="hljs-keyword">at</span> &lt;console&gt;:<span class="hljs-number">21</span></code></pre>

<p>执行： <br>
<strong>scala&gt;</strong> val wordCounts = textFile.flatMap(line =&gt; line.split(” “)).map(word =&gt; (word, 1)).reduceByKey((a,b) =&gt; a + b) <br>
显示以下内容：</p>



<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-number">16</span>/<span class="hljs-number">09</span>/<span class="hljs-number">08</span> <span class="hljs-number">16</span>:<span class="hljs-number">27</span>:<span class="hljs-number">19</span> INFO FileInputFormat: Total input paths to process : <span class="hljs-number">1</span>
<span class="hljs-label">wordCounts:</span> org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.rdd</span><span class="hljs-preprocessor">.RDD</span>[(String, Int)] = ShuffledRDD[<span class="hljs-number">4</span>] at reduceByKey at &lt;console&gt;:<span class="hljs-number">23</span></code></pre>

<p>执行 <br>
scala&gt; wordCounts.collect() <br>
可以在最后看到：</p>



<pre class="prettyprint"><code class=" hljs mathematica"><span class="hljs-number">16</span>/<span class="hljs-number">09</span>/<span class="hljs-number">08</span> <span class="hljs-number">16</span>:<span class="hljs-number">28</span>:<span class="hljs-number">41</span> INFO DAGScheduler: Job <span class="hljs-number">0</span> finished: collect at &lt;console&gt;:<span class="hljs-number">26</span>, took <span class="hljs-number">3.240093</span> s
res0: <span class="hljs-keyword">Array</span>[(<span class="hljs-keyword">String</span>, Int)] = <span class="hljs-keyword">Array</span>((love,<span class="hljs-number">1</span>), (very,<span class="hljs-number">3</span>), (much!,<span class="hljs-number">1</span>), (spark,<span class="hljs-number">1</span>), (<span class="hljs-keyword">I</span>,<span class="hljs-number">1</span>))</code></pre>

<p>以上，我们可以在不写代码的情况下来跑spark任务了！</p>



<h1 id="spark初编程">spark初编程</h1>

<p>下面演示一下如何通过spark api来写一个任务提交。照样，使用word count示例。 <br>
首先需要确定依赖的spark版本，我用的cm安装的spark 1.3.0，因此，我的pom文件是：</p>



<pre class="prettyprint"><code class="language-java hljs ">        &lt;dependency&gt;
            &lt;groupId&gt;org.apache.spark&lt;/groupId&gt;
            &lt;artifactId&gt;spark-core_2<span class="hljs-number">.10</span>&lt;/artifactId&gt;
            &lt;version&gt;<span class="hljs-number">1.3</span><span class="hljs-number">.0</span>-cdh5<span class="hljs-number">.4</span><span class="hljs-number">.0</span>&lt;/version&gt;
        &lt;/dependency&gt;</code></pre>

<p>spark的wordCount代码如下</p>



<pre class="prettyprint"><code class="language-java hljs "><span class="hljs-keyword">package</span> spark;

<span class="hljs-keyword">import</span> org.apache.spark.SparkConf;
<span class="hljs-keyword">import</span> org.apache.spark.api.java.JavaPairRDD;
<span class="hljs-keyword">import</span> org.apache.spark.api.java.JavaRDD;
<span class="hljs-keyword">import</span> org.apache.spark.api.java.JavaSparkContext;
<span class="hljs-keyword">import</span> org.apache.spark.api.java.function.FlatMapFunction;
<span class="hljs-keyword">import</span> org.apache.spark.api.java.function.Function2;
<span class="hljs-keyword">import</span> org.apache.spark.api.java.function.PairFunction;
<span class="hljs-keyword">import</span> scala.Tuple2;

<span class="hljs-keyword">import</span> java.util.Arrays;

<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">SparkWordCount</span> {</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">main</span>(String[] args) {
        <span class="hljs-comment">// 指定hdfs文件路径，注意：hostname替换为你的hdfs路径</span>
        String filePath = <span class="hljs-string">"hdfs://hostname:8020/tmp/test.txt"</span>;

        <span class="hljs-comment">// master可以在这里设置，也可以提交任务时候指定，在这里，我们简单的在程序里设置</span>
        <span class="hljs-comment">// 并且，没有指定master的路径，用的是local模式，表示本地内容，2个线程执行</span>
        SparkConf sparkConf = <span class="hljs-keyword">new</span> SparkConf().setMaster(<span class="hljs-string">"local[2]"</span>).setAppName(<span class="hljs-string">"wordCount"</span>);

        <span class="hljs-comment">// sc</span>
        JavaSparkContext sc = <span class="hljs-keyword">new</span> JavaSparkContext(sparkConf);

        <span class="hljs-comment">// 读文件进来</span>
        JavaRDD&lt;String&gt; lines = sc.textFile(filePath);

        <span class="hljs-comment">// flatMap操作，不清楚可以查一下，将字符串映射为序列</span>
        JavaRDD&lt;String&gt; words = lines.flatMap(
                <span class="hljs-keyword">new</span> FlatMapFunction&lt;String, String&gt;() {
                    <span class="hljs-annotation">@Override</span>
                    <span class="hljs-keyword">public</span> Iterable&lt;String&gt; <span class="hljs-title">call</span>(String s) <span class="hljs-keyword">throws</span> Exception {
                        <span class="hljs-keyword">return</span> Arrays.asList(s.split(<span class="hljs-string">" "</span>));
                    }
                }
        );

        <span class="hljs-comment">// mapToPair操作，将单词映射为统计值</span>
        JavaPairRDD&lt;String, Integer&gt; pairs = words.mapToPair(
                <span class="hljs-keyword">new</span> PairFunction&lt;String, String, Integer&gt;() {
                    <span class="hljs-annotation">@Override</span>
                    <span class="hljs-keyword">public</span> Tuple2&lt;String, Integer&gt; <span class="hljs-title">call</span>(String s) <span class="hljs-keyword">throws</span> Exception {
                        <span class="hljs-keyword">return</span> <span class="hljs-keyword">new</span> Tuple2&lt;String, Integer&gt;(s, <span class="hljs-number">1</span>);
                    }
                }
        );

        <span class="hljs-comment">// reduceByKey操作，归并</span>
        JavaPairRDD&lt;String, Integer&gt; wordCounts = pairs.reduceByKey(
                <span class="hljs-keyword">new</span> Function2&lt;Integer, Integer, Integer&gt;() {
                    <span class="hljs-annotation">@Override</span>
                    <span class="hljs-keyword">public</span> Integer <span class="hljs-title">call</span>(Integer integer, Integer integer2) <span class="hljs-keyword">throws</span> Exception {
                        <span class="hljs-keyword">return</span> integer + integer2;
                    }
                }
        );

        <span class="hljs-comment">// 查看集合</span>
        wordCounts.collect();
    }
}</code></pre>

<p>mvn package生成jar包为spark-test.jar <br>
在spark上提交任务 <br>
<strong>spark-submit –class spark.SparkWordCount ./spark-test.jar</strong> <br>
其中，–class指定的是main入口类，spark-test.jar为刚刚编译的jar包</p>

<p>提交执行后，即可看到和spark-shell一样的效果。</p>



<h1 id="结语">结语</h1>

<p>至此，我们简单了解了spark，使用spark-shell体验了spark的功能，使用spark java api编写了一段小程序。开启spark之路吧！</p>

<hr>

<p>官网入门：<a href="http://spark.apache.org/docs/latest/quick-start.html" rel="nofollow">http://spark.apache.org/docs/latest/quick-start.html</a></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>