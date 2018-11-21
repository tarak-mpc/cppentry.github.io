---
layout:     post
title:      Spark Streaming入门
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/Artisters/article/details/82989814				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1><a id="1_Spark_Streaming_0"></a>1. Spark Streaming入门</h1>
<h2><a id="1__2"></a>1. 概述</h2>
<p><img src="https://img-blog.csdn.net/20181009220628192?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FydGlzdGVycw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="在这里插入图片描述"></p>
<ul>
<li>Spark Streaming is an extension of the core Spark API that enables scalable(Spark Streaming是基于Spark Core的扩展)</li>
<li>high-throughput(高可用)</li>
<li>fault-tolerant(容错)</li>
<li>stream processing of live data streams(作用在实时数据流上)</li>
<li>Spark Streaming: 将不同的数据源的数据经过Spark Streaming处理之后将结果输出到外部文件系统</li>
<li>特点:
<ul>
<li>低延时</li>
<li>能从错误中高效的恢复: fault-tolerant</li>
<li>能够运行在成百上千的节点</li>
<li>能够将批处理、机器学习、图计算等子框架和Spark Streaming综合起来使用</li>
</ul>
</li>
<li>Spark Streaming 不需要单独部署，包含在Spark Project里。</li>
<li>One stack to rule them all: 一栈式解决。</li>
</ul>
<p><img src="https://img-blog.csdn.net/20181009220702207?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FydGlzdGVycw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="在这里插入图片描述"></p>
<h2><a id="2__21"></a>2. 应用场景</h2>
<ul>
<li>Real-time fraud detection in transactions(实时交易金融欺诈检测，银行业等)</li>
<li>React to anomalies in sensors in real-time(实时反应，电子业等)</li>
<li>电商网站（推荐系统等，以前是离线处理的）</li>
<li>实时监控（发现外界攻击等）</li>
<li>Java EE应用（实时日志错误统计、应变等）</li>
</ul>
<h2><a id="3_Spark_29"></a>3. 集成Spark生态系统使用</h2>
<p><img src="https://img-blog.csdn.net/2018100922072157?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FydGlzdGVycw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="在这里插入图片描述"></p>
<blockquote>
<p>Spark生态的组件，他们都是依托Spark Core进行各自的扩展，那么Spark Streaming如何与各组件间调用呢？</p>
</blockquote>
<ul>
<li>Join data streams with static data sets(数据流和静态数据)</li>
</ul>
<pre class=" language-scala"><code class="prism  language-scala">//Create data set from Hadoop file
val dataset = sparkContext.hadoopFile("file")

//Join each batch in stream with the dataset
//kafka数据 =&gt; RDD
kafkaStream.transform(batchRDD =&gt; {
    batchRDD.join(dataset).filter(...)
})

</code></pre>
<ul>
<li>Learn models offline, apply them online(使用机器学习模型))</li>
</ul>
<pre class=" language-scala"><code class="prism  language-scala">//Learn model offline
val model = KMeans.train(dataset, ...)

//Apply model online on stream
kafkaStream.map(event =&gt; {
    model.predict(event.featrue)
})
</code></pre>
<ul>
<li>Interactively query straming data with SQL(使用SQL查询交互式数据流)</li>
</ul>
<pre class=" language-scala"><code class="prism  language-scala">//Register each batch in stream as table
kafkaStream.map(batchRDD =&gt; {
    batchRDD.registerTempTable("latestEvents")
})

//interactively query table
sqlContext.sql("SELECT * FROM latestEvents")
</code></pre>
<h2><a id="4_Spark_Streaming_73"></a>4. Spark Streaming发展史</h2>
<ul>
<li>Late 2011 - idea AMPLab, UC Berkeley</li>
<li>Q2 2012 - prototype Rewrote large parts of Spark core Smallest job - 900 ms -&gt; &lt; 50ms</li>
<li>Q3 2012 - Spark core improvements open source in Spark 0.6</li>
<li>Feb 2013 - Alpha release 7.7k lines, merged in 7 days Released with Spark 0.7</li>
<li>Jan 2014 - Stable release Graduation with Spark 0.9</li>
</ul>
<h2><a id="5__81"></a>5. 从词频统计功能着手入门</h2>
<ul>
<li>spark-submit执行</li>
</ul>
<pre class=" language-shell"><code class="prism  language-shell">./spark-submit --master local<span class="token punctuation">[</span>2<span class="token punctuation">]</span> \
--class org.apache.spark.examples.streaming.NetworkWordCount \
/usr/local/spark/examples/jars/spark-examples.jar <span class="token punctuation">[</span>args1<span class="token punctuation">]</span> <span class="token punctuation">[</span>args2<span class="token punctuation">]</span>
</code></pre>
<ul>
<li>spark-shell执行</li>
</ul>
<pre class=" language-shell"><code class="prism  language-shell"><span class="token function">import</span> org.apache.spark.streaming.<span class="token punctuation">{</span>Seconds, StreamingContext<span class="token punctuation">}</span>
val ssc <span class="token operator">=</span> new StreamingContext<span class="token punctuation">(</span>sc, Seconds<span class="token punctuation">(</span>1<span class="token punctuation">))</span>
val lines <span class="token operator">=</span> ssc.socketTextStream<span class="token punctuation">(</span><span class="token string">"localhost"</span>, 9999<span class="token punctuation">)</span>
val wordCounts <span class="token operator">=</span> lines.flatMap<span class="token punctuation">(</span>_.split<span class="token punctuation">(</span><span class="token string">" "</span><span class="token punctuation">))</span>.map<span class="token punctuation">(</span>x <span class="token operator">=</span><span class="token operator">&gt;</span> <span class="token punctuation">(</span>x, 1<span class="token punctuation">))</span>.recudeByKey<span class="token punctuation">(</span>_ + _<span class="token punctuation">)</span>
wordCounts.print<span class="token punctuation">(</span><span class="token punctuation">)</span>
ssc.start<span class="token punctuation">(</span><span class="token punctuation">)</span>
ssc.awaitTermination<span class="token punctuation">(</span><span class="token punctuation">)</span>
</code></pre>
<ul>
<li>测试</li>
</ul>
<pre class=" language-shell"><code class="prism  language-shell"><span class="token comment">#向端口发送消息</span>
nc -lk 9999
</code></pre>
<h2><a id="6__110"></a>6. 工作原理</h2>
<ul>
<li>粗粒度：Spark Streaming接收到实时数据流，把数据按照指定的时间段切成一片片小的数据块，然后把小的数据库传给Spark Engine处理。</li>
</ul>
<p><img src="https://img-blog.csdn.net/20181009220737818?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FydGlzdGVycw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="在这里插入图片描述"></p>
<ul>
<li>细粒度</li>
</ul>
<p><img src="https://img-blog.csdn.net/20181009220745140?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FydGlzdGVycw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="在这里插入图片描述"></p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>