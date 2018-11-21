---
layout:     post
title:      SparkStreaming之updateStateByKey
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主九师兄（QQ群:spark源代码 198279782 欢迎来探讨技术）原创文章，未经博主允许不得转载。					https://blog.csdn.net/qq_21383435/article/details/80573674				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-light">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p><img src="https://img-blog.csdn.net/20180604214144342?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIxMzgzNDM1/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<p><img src="https://img-blog.csdn.net/20180604214219717?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIxMzgzNDM1/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<p>我们一直执行 <code>hadoop fs  -put a.txt /hdfs</code> 的话， <br>
第1次执行是<code>（spark,4）</code> <br>
第2次执行是<code>（spark,4）</code> <br>
第3次执行是<code>（spark,4）</code></p>

<p>但是如果我们想要这种效果呢？ <br>
第1次执行是<code>（spark,4）</code> <br>
第2次执行是<code>（spark,8）</code> <br>
第3次执行是<code>（spark,12）</code></p>

<p>这样的话，就重用了历史数据。</p>



<pre class="prettyprint"><code class=" hljs scala"><span class="hljs-keyword">package</span> com.llcc.sparkSql.MyTimeSort

<span class="hljs-keyword">import</span> org.apache.spark.streaming.StreamingContext
<span class="hljs-keyword">import</span> org.apache.spark.SparkConf
<span class="hljs-keyword">import</span> org.apache.spark.streaming.Seconds

<span class="hljs-class"><span class="hljs-keyword">object</span> <span class="hljs-title">NetWordCountUpdateStateByKey</span> {</span>
  <span class="hljs-keyword">def</span> main(args: Array[String]): Unit = {
    <span class="hljs-javadoc">/**
      * local[1]  中括号里面的数字都代表的是启动几个工作线程
      * 默认情况下是一个工作线程。那么做为sparkstreaming 我们至少要开启
      * 两个线程，因为其中一个线程用来接收数据，这样另外一个线程用来处理数据。
      */</span>
    <span class="hljs-keyword">val</span> conf=<span class="hljs-keyword">new</span> SparkConf().setMaster(<span class="hljs-string">"local[2]"</span>).setAppName(<span class="hljs-string">"NetWordCountUpdateStateByKey"</span>)
    <span class="hljs-javadoc">/**
      * Seconds  指的是每次数据数据的时间范围 （bacth interval）
      */</span>
    <span class="hljs-keyword">val</span>  ssc=<span class="hljs-keyword">new</span> StreamingContext(conf,Seconds(<span class="hljs-number">2</span>));


    <span class="hljs-keyword">val</span> fileDS=ssc.socketTextStream(<span class="hljs-string">"hadoop1"</span>, <span class="hljs-number">9999</span>)
    <span class="hljs-javadoc">/**
      * 需要设置一个checkpoint的目录
      * 因为我们的计算结果有中间状态，这些中间状态需要存储
      */</span>
    ssc.checkpoint(<span class="hljs-string">"."</span>)
    <span class="hljs-keyword">val</span> wordDS=fileDS.flatMap { line =&gt; line.split(<span class="hljs-string">"\t"</span>) }
      .map { word =&gt; (word,<span class="hljs-number">1</span>) }
    <span class="hljs-javadoc">/**
      * updateFunc: (Seq[Int], Option[S]) =&gt; Option[S]
      * updateFunc 这是一个匿名函数
      *  (Seq[Int], Option[S]) 两个参数
      *  Option[S] 返回值
      *  首先我们考虑一个问题
      *  wordDS  做的bykey的计算，说明里面的内容是tuple类型，是键值对的形式，说白了是不是
      *  就是【K V】
      *  wordDS[K,V]
      *  (Seq[Int], Option[S])
      *  参数一：Seq[Int] Seq代表的是一个集合，int代表的是V的数据类型
      *      ---分组的操作，key相同的为一组 (hadoop,{1,1,1,1})
      *  参数二：Option[S] S代表的是中间状态State的数据类型，S对于我们的这个wordcount例子来讲，应该是
      *  int类型。中间状态存储的是单词出现的次数。 hadoop -&gt; 4
      *
      *  Option[S] 返回值  应该跟中间状态一样吧。
      *  Option Some/None
      *
      */</span>
    <span class="hljs-keyword">val</span> wordcountDS=wordDS.updateStateByKey((values:Seq[Int],state:Option[Int]) =&gt;{
      <span class="hljs-keyword">val</span> currentCount= values.sum;  <span class="hljs-comment">//获取此次本单词出现的次数</span>
      <span class="hljs-keyword">val</span> count=state.getOrElse(<span class="hljs-number">0</span>);<span class="hljs-comment">//获取上一次的结果 也就是中间状态</span>
      Some(currentCount+count);
    })
    wordcountDS.print()
    <span class="hljs-comment">//启动应用</span>
    ssc.start()
    <span class="hljs-comment">//等待任务结束</span>
    ssc.awaitTermination()
  }
}
</code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>