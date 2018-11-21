---
layout:     post
title:      Spark Structed Streaming的一些主要特性及与Spark Streaming之间的对比
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，转载请注明出处，欢迎交流，qq911283415。					https://blog.csdn.net/HaixWang/article/details/82953004				</div>
								            <div id="content_views" class="markdown_views prism-dracula">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2><a id="Spark_Streaming_0"></a>Spark Streaming</h2>
<p>众所周知，Spark Streaming中的数据结构是DStream，是对RDD的进一步的封装，是”微批“的准实时处理。将连续的流数据转换为连续的批作业，这里的“批”，就是指RDD序列——DStream。</p>
<p>除了文件数据流之外，所有的输入DStream都会被绑定到一个Receiver对象上，Receiver接受数据。所以一个Spark Streaming程序，至少需要占用2个cpu core，一个给spark streaming application的executor，另一个给Receiver。</p>
<p><strong>程序开发流程：</strong></p>
<ol>
<li>DStream数据源<br>
（Flink中：Source）（Flink、Spark Streaming中，DStream/Source都可以是并行多个的。）</li>
<li>Transformation+Action操作<br>
（Flink为Transformation）</li>
<li>调用StreamingContext的start开始执行程序<br>
（Flink为ExecutionEnvironment的execute()方法）</li>
<li>stop</li>
</ol>
<h2><a id="Spark_Structed_Streaming_14"></a>Spark Structed Streaming</h2>
<p>之前在<a href="https://blog.csdn.net/HaixWang/article/details/82917371#Spark_Streaming_304" rel="nofollow">实时大数据平台技术选型概要</a>这篇文章中有提到：</p>
<p>Structured Streaming是Spark2提出的新的实时流框架（2.0和2.1是实验版本，从Spark2.2开始为稳定版本），Spark2 将流式计算也统一到DataFrame里去。<br>
<img src="https://img-blog.csdn.net/20181020201648553?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0hhaXhXYW5n/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="structured-streaming-stream-as-a-table.png"></p>
<p>总的来说，Structured Streaming 的意义有：</p>
<ul>
<li>重新抽象了流式计算</li>
<li>易于实现数据的exactly-once（2.0之前的Spark Streaming 只能做到at-least once）</li>
<li>解决了Spark Streaming存在的代码升级，DAG图变化引起的任务失败，无法断点续传的问题</li>
<li>保证与批处理作业的强一致性</li>
<li>API简化（引入和流函数的封装。<br>
举个例子：Kafka中读取的数据，通过stream方法形成流，就可以直接与jdbc中读取的数据在DataSet层面就进行Join，不用使用transform或者foreachRDD方法。<br>
stream方法底层依赖Dataset和Dataframe，集成了SparkSql和Dataset几乎所有的功能，把流处理的代码编写一下子简化了很多。）</li>
<li>基于Event-Time，相比于Spark Streaming的Processing-Time更精确</li>
<li>未来将支持使用SQL和JDBC来实时查询Streaming data</li>
</ul>
<p>通俗的说，<strong>以前只是流式计算，数据计算完之后放入外部存储，现在向持续计算发展，希望通过一套API实现数据的持续计算（数据在计算时，既可以结合离线数据，又可以提供实时查询，又可以与外部存储比如redis、hbase交互）。</strong></p>
<h4><a id="_33"></a>持续计算</h4>
<p>自Spark 2.3以来，引入了一种称为<strong>连续处理</strong>的新型低延迟处理模式，它可以实现低至1毫秒的端到端延迟，并且具有至少一次保证（at-least-once）。</p>
<h4><a id="_35"></a>无边界表</h4>
<p>以下内容翻译自：<a href="https://databricks.com/blog/2018/03/20/low-latency-continuous-processing-mode-in-structured-streaming-in-apache-spark-2-3-0.html" rel="nofollow">Introducing Low-latency Continuous Processing Mode in Structured Streaming in Apache Spark 2.3</a></p>
<p>在Spark2.0中，Structured Streaming将微批处理模式与其高级API分离开来。原因有两个：首先，简化了API的使用，屏蔽了底层的微批处理细节。其次，Structured Streaming允许开发人员将流数据看做一个无界表，并且查询体验和静态表一致。</p>
<p>为了充分利用这一优势，在Spark2.3.0中引入一种被称为连续模式的可以实现毫秒级延迟的流处理模式。<br>
<img src="https://img-blog.csdn.net/20181020203009873?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0hhaXhXYW5n/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="在这里插入图片描述"><br>
Structured Streaming默认使用微批处理执行模型。 这意味着Spark流式计算引擎会定期检查流数据源，并对自上一批次结束后到达的新数据执行批量查询。 在高层次上，它看起来像这样。<br>
<img src="https://databricks.com/wp-content/uploads/2018/03/image7-1.png" alt="在这里插入图片描述"></p>
<ul>
<li>Driver驱动程序通过将记录偏移量保存到预写日志中来对数据处理进度设置检查点</li>
<li>当前到达的数据需要等待当前的微批处理作业完成，且其中数据的偏移量范围被计入日志后，才能在下一个微批作业中得到处理。</li>
</ul>
<p><strong>连续处理（Continuous Processing）</strong><br>
<mark>连续处理是Spark 2.3中引入的一种新的实验性流执行模式，可实现低（~1 ms）端到端延迟，并且至少具有一次容错保证。</mark><br>
在连续处理模式中，Spark不再是启动定期任务，而是启动一系列连续读取，处理和写入数据的长时间运行的任务。 在高层次上，设置和记录级时间线看起来像这些（与上述微量批处理执行图的对比）。<br>
<img src="https://databricks.com/wp-content/uploads/2018/03/image2-2.png" alt="在这里插入图片描述"></p>
<p><img src="https://databricks.com/wp-content/uploads/2018/03/image4-2.png" alt="在这里插入图片描述"><br>
由于事件在到达时会被立即处理和写入结果，所以端到端延迟只有几毫秒。</p>
<p>此外，我们利用著名的Chandy-Lamport算法对查询进度设置检查点。 特殊标记的记录被注入到每个任务的输入数据流中; 我们将它们称为“时间代标记（epoch marker）”，并将它们之间的差距称为“时间代(epoch)”。当任务遇到标记时，任务异步报告处理后的最后偏移量给driver。 一旦driver程序接收到写入接收器的所有任务的偏移量，它就会将它们写入前述的预写日志。 由于检查点的设置是完全异步的，任务可以不间断地持续并提供一致的毫秒级延迟。</p>
<p>在Spark2.3.0中，流数据的连续处理模式还是一种实验性功能，在此模式下支持Structured Streaming所支持的所有流数据源以及DataFrame / Dataset / SQL操作的子集。</p>
<hr>
<blockquote>
<p>官方给的Spark Structed Streaming示例代码倒是与Spark Streaming没区别：</p>
</blockquote>
<pre><code class="prism language-java">object StructuredNetworkWordCount <span class="token punctuation">{</span>
  def <span class="token function">main</span><span class="token punctuation">(</span>args<span class="token operator">:</span> Array<span class="token punctuation">[</span>String<span class="token punctuation">]</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token keyword">if</span> <span class="token punctuation">(</span>args<span class="token punctuation">.</span>length <span class="token operator">&lt;</span> <span class="token number">2</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
      System<span class="token punctuation">.</span>err<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span><span class="token string">"Usage: StructuredNetworkWordCount &lt;hostname&gt; &lt;port&gt;"</span><span class="token punctuation">)</span>
      System<span class="token punctuation">.</span><span class="token function">exit</span><span class="token punctuation">(</span><span class="token number">1</span><span class="token punctuation">)</span>
    <span class="token punctuation">}</span>

    val host <span class="token operator">=</span> <span class="token function">args</span><span class="token punctuation">(</span><span class="token number">0</span><span class="token punctuation">)</span>
    val port <span class="token operator">=</span> <span class="token function">args</span><span class="token punctuation">(</span><span class="token number">1</span><span class="token punctuation">)</span><span class="token punctuation">.</span>toInt

    val spark <span class="token operator">=</span> SparkSession
      <span class="token punctuation">.</span>builder
      <span class="token punctuation">.</span><span class="token function">appName</span><span class="token punctuation">(</span><span class="token string">"StructuredNetworkWordCount"</span><span class="token punctuation">)</span>
      <span class="token punctuation">.</span><span class="token function">getOrCreate</span><span class="token punctuation">(</span><span class="token punctuation">)</span>

    <span class="token keyword">import</span> spark<span class="token punctuation">.</span>implicits<span class="token punctuation">.</span>_

    <span class="token comment">// Create DataFrame representing the stream of input lines from connection to host:port</span>
    val lines <span class="token operator">=</span> spark<span class="token punctuation">.</span>readStream
      <span class="token punctuation">.</span><span class="token function">format</span><span class="token punctuation">(</span><span class="token string">"socket"</span><span class="token punctuation">)</span>
      <span class="token punctuation">.</span><span class="token function">option</span><span class="token punctuation">(</span><span class="token string">"host"</span><span class="token punctuation">,</span> host<span class="token punctuation">)</span>
      <span class="token punctuation">.</span><span class="token function">option</span><span class="token punctuation">(</span><span class="token string">"port"</span><span class="token punctuation">,</span> port<span class="token punctuation">)</span>
      <span class="token punctuation">.</span><span class="token function">load</span><span class="token punctuation">(</span><span class="token punctuation">)</span>

    <span class="token comment">// Split the lines into words</span>
    val words <span class="token operator">=</span> lines<span class="token punctuation">.</span>as<span class="token punctuation">[</span>String<span class="token punctuation">]</span><span class="token punctuation">.</span><span class="token function">flatMap</span><span class="token punctuation">(</span>_<span class="token punctuation">.</span><span class="token function">split</span><span class="token punctuation">(</span><span class="token string">" "</span><span class="token punctuation">)</span><span class="token punctuation">)</span>

    <span class="token comment">// Generate running word count</span>
    val wordCounts <span class="token operator">=</span> words<span class="token punctuation">.</span><span class="token function">groupBy</span><span class="token punctuation">(</span><span class="token string">"value"</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">count</span><span class="token punctuation">(</span><span class="token punctuation">)</span>

    <span class="token comment">// Start running the query that prints the running counts to the console</span>
    val query <span class="token operator">=</span> wordCounts<span class="token punctuation">.</span>writeStream
      <span class="token punctuation">.</span><span class="token function">outputMode</span><span class="token punctuation">(</span><span class="token string">"complete"</span><span class="token punctuation">)</span>
      <span class="token punctuation">.</span><span class="token function">format</span><span class="token punctuation">(</span><span class="token string">"console"</span><span class="token punctuation">)</span>
      <span class="token punctuation">.</span><span class="token function">start</span><span class="token punctuation">(</span><span class="token punctuation">)</span>

    query<span class="token punctuation">.</span><span class="token function">awaitTermination</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
  <span class="token punctuation">}</span>
<span class="token punctuation">}</span>
</code></pre>
<h4><a id="_104"></a>输出模式</h4>
<p>有几种类型的输出模式：</p>
<ul>
<li>追加模式（默认） ：<br>
这是默认模式，其中只有自上次触发后新添加到结果表中的行会输出到接收器。<br>
仅支持那些添加到结果表中的行永远不会更改的查询。因此，该模式保证每行仅输出一次（假设容错接收器）。例如，select，where，map，flatMap，filter，join等查询支持Append模式。</li>
<li>完成模式 ：<br>
每次触发后，整个结果表将输出到接收器。聚合查询支持此功能。</li>
<li>更新模式：<br>
自Spark 2.1.1起可用）仅将结果表中自上次触发后更新的行输出到接收器。</li>
</ul>
<pre><code class="prism language-java"> val query <span class="token operator">=</span> wordCounts<span class="token punctuation">.</span>writeStream
      <span class="token punctuation">.</span><span class="token function">outputMode</span><span class="token punctuation">(</span><span class="token string">"complete"</span><span class="token punctuation">)</span> <span class="token comment">//与.outputMode(OutputMode.Complete)等价</span>
      <span class="token punctuation">.</span><span class="token function">format</span><span class="token punctuation">(</span><span class="token string">"console"</span><span class="token punctuation">)</span>
      <span class="token punctuation">.</span><span class="token function">start</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
</code></pre>
<p>【<strong>与追加模式的差别，还不是很理解？</strong>】<br>
追加和完成比较容易理解（完成理解为完整更佳），比如说第一秒输入：<br>
abcd<br>
第二秒输入：<br>
efgh<br>
如果是收集信息后打印，那么完成模式会打印abcdefgh，而追加更新模式是efgh。<br>
但是更新模式，不是很理解，</p>
<h2><a id="_130"></a>参考</h2>
<ol>
<li><a href="https://databricks.com/blog/2018/03/20/low-latency-continuous-processing-mode-in-structured-streaming-in-apache-spark-2-3-0.html" rel="nofollow">在Apache Spark 2.3中引入结构化流中的低延迟连续处理模式</a></li>
<li><a href="http://spark.apache.org/docs/latest/structured-streaming-programming-guide.html" rel="nofollow">doc: structured-streaming-programming-guide</a></li>
</ol>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>