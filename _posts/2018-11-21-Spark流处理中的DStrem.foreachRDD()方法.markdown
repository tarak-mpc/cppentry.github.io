---
layout:     post
title:      Spark流处理中的DStrem.foreachRDD()方法
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：转载请注明出处。我的GitHub是:https://github.com/YaokaiYang-assaultmaster，欢迎来访。					https://blog.csdn.net/Yaokai_AssultMaster/article/details/71915640				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2 id="spark数据处理">Spark数据处理</h2>

<p>Spark作为分布式数据处理的一个开源框架，因其计算的高效性和简洁的API而广受欢迎。一般来说，Spark大部分时候被用来进行批处理。但现在Spark通过其SparkStreaming模块也实现了一定的流处理的功能。</p>



<h2 id="spark流处理的过程">Spark流处理的过程</h2>

<p>Spark中的流处理实际上并不是真正的流处理。Spark实现流处理的方法是通过<em>mini-batch</em>来对输入数据进行分块（但这个分块频率非常高以至于能够模拟流处理的过程）。 在进行<em>mini-batch</em>分块的时候，Spark引入了<em>DStream</em>的概念。</p>

<p>所谓的<code>DStream</code>，或者说<code>Discretized Stream</code>指的是将连续的流数据分成小块批数据的抽象。这就是我们上面说的<em>mini-batch</em>过程。每一个<em>mini-batch</em>体现为一个所谓的<em>RDD</em>（Resilient Distributed Dataset）。而<em>RDD</em>被 交给Spark executor进行进一步的处理。对每一个<em>mini-batch</em>间隔对应的<em>DStream</em>来说，有且仅有一个<em>RDD</em>被产生。   </p>

<p>一个<em>RDD</em>是一份分布式的数据集。我们可以把<em>RDD</em>当成指向集群中真正数据块的指针。</p>

<p><code>DStream.foreachRDD()</code>方法实际上是Spark流处理的一个处理及输出<em>RDD</em>的方法。这个方法使我们能够访问底层的<em>DStream</em>对应的<em>RDD</em>进而根据我们需要的逻辑对其进行处理。例如，我们可以通过<code>foreachRDD()</code>方法来访问每一条<em>mini-batch</em>中的数据，然后将它们存入数据库。    </p>

<p>这里需要注意的一点是<em>DStream</em>实际上是一组根据时间被分割出来的数据集合。这里我们可以通过如下两段程序来对比传统的集合和Spark中的时序集合<em>DStream</em>的区别。</p>



<pre class="prettyprint"><code class=" hljs bash">userList = [something...]
userList.foreach(lambda user:<span class="hljs-keyword">do</span>Something(user))</code></pre>

<p>这段程序将会对<code>userList</code>集合中的每一个元素执行<code>doSomething()</code>函数。</p>

<p>在流处理问题中，我们所面对的不同情况是我们提前并不知道所有的元素。因此我们无法将它们都放入一个<code>list</code>中。相反的，我们对于流中的每一个元素执行某种操作，就好像意见饭店不断服务前来吃饭的客人一样。</p>



<pre class="prettyprint"><code class=" hljs vala"><span class="hljs-preprocessor"># A DStream of user</span>
userDStream = ???

<span class="hljs-preprocessor"># For each RDD batch, process each element in it</span>
userDStream.foreachRDD(lambda userbatch:userbatch.<span class="hljs-keyword">foreach</span>(doSomeThing(user)))</code></pre>

<p>需要注意的是：   </p>

<ul>
<li><code>DStream.foreachRDD()</code>传给我们的参数是一个<code>RDD[userbatch]</code>，而不是单个的user。用上面饭店的例子来讲，我们得到的不是一个单个的客人，而是某一个时间段内到来的一波客人。因此我们需要进一步循环来根据需要处理其中每一个<code>user</code>。    </li>
<li>我们不能用传统的<code>for ele in iterable</code>方法来循环其中的元素。因此我们需要用<code>rdd.foreach()</code>来分别处理其中每一个<code>user</code>。</li>
</ul>

<p>进一步分析Spark的流处理过程：我们拥有几个Spark的executor。对于稳定到来的数据流，<strong>Spark Streaming</strong>负责根据一定的时间间隔将流输入分成小的数据块（<em>batch</em>），然后Spark将这些小数据块（<em>mini-batch</em>）分配给不同的executor，就像饭店将不同的顾客分配给不同的服务员一样。通过这样的操作，Spark实现了并行的数据计算，从而加速了数据处理的速度。</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>