---
layout:     post
title:      Spark Programming Guide(二)
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：转载请标注原文地址					https://blog.csdn.net/soul_code/article/details/78031075				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h5 id="basics">Basics</h5>

<p>To illustrate RDD basics, consider the simple program below: <br>
仔细观察下面的程序，介绍了RDD的基本功能：</p>



<pre class="prettyprint"><code class=" hljs livecodeserver">val <span class="hljs-keyword">lines</span> = sc.textFile(<span class="hljs-string">"data.txt"</span>)
val lineLengths = <span class="hljs-keyword">lines</span>.map(s =&gt; s.<span class="hljs-built_in">length</span>)
val totalLength = lineLengths.reduce((<span class="hljs-operator">a</span>, b) =&gt; <span class="hljs-operator">a</span> + b)</code></pre>

<p>The first line defines a base RDD from an external file. This dataset is not loaded in memory or otherwise acted on: lines is merely a pointer to the file. The second line defines lineLengths as the result of a map transformation. Again, lineLengths is not immediately computed, due to laziness. Finally, we run reduce, which is an action. At this point Spark breaks the computation into tasks to run on separate machines, and each machine runs both its part of the map and a local reduction, returning only its answer to the driver program.</p>

<p>第一行代码从外部文件定义了一个基本的RDD，这里数据集并没有被真正的加载进内存，这里仅仅是建立了一个指向文件的一个指针。第二行定义了一个lineLengths的数据集由第一个数据集转换操作产生。同样由于RDD是惰性加载的，这里lineLengths并不会立即进行计算。最后，我们执行了action进行reduce计算。到此时Spark将计算任务分配到不同的机器上，每台机器同时对分区数据进行map和本地的reduce操作，最后把结果数据返回到驱动程序。</p>

<p>If we also wanted to use lineLengths again later, we could add:</p>

<p>如果我们在后面需要继续使用lineLengths，我们可以调用如下方法：</p>



<pre class="prettyprint"><code class=" hljs avrasm">lineLengths<span class="hljs-preprocessor">.persist</span>()</code></pre>

<p>before the reduce, which would cause lineLengths to be saved in memory after the first time it is computed.</p>

<p>当执行第一个reduce计算操作之后lineLengths将会被持久化到内存中</p>



<h5 id="passing-functions-to-spark">Passing Functions to Spark</h5>

<p>Spark’s API relies heavily on passing functions in the driver program to run on the cluster. There are two recommended ways to do this:</p>

<p>Spark的API很大程度上依赖于从驱动程序传递函数到集群之上，这里有两种推荐的方法来做这件事：</p>

<ol>
<li>Anonymous function syntax, which can be used for short pieces of code.</li>
</ol>

<p>使用简短的匿名函数语法</p>

<ol>
<li>Static methods in a global singleton object. For example, you can define object MyFunctions and then pass MyFunctions.func1, as follows:</li>
</ol>

<p>使用全集单例对象的静态方法。比如，你可以定义一个对象MyFunctions并且调用MyFunctions.func1方法，就像下面：</p>



<pre class="prettyprint"><code class=" hljs python">object MyFunctions {
  <span class="hljs-function"><span class="hljs-keyword">def</span> <span class="hljs-title">func1</span><span class="hljs-params">(s: String)</span>:</span> String = { ... }
}

myRdd.map(MyFunctions.func1)</code></pre>

<p>Note that while it is also possible to pass a reference to a method in a class instance (as opposed to a singleton object), this requires sending the object that contains that class along with the method. For example, consider:</p>

<p>注意，虽然还可以传递类实例中的方法的引用（而不是单例对象），但这需要将包含该类的对象连同方法一起发送出去。例如，考虑：</p>



<pre class="prettyprint"><code class=" hljs r">class MyClass {
  def func1(s: String): String = { <span class="hljs-keyword">...</span> }
  def doStuff(rdd: RDD[String]): RDD[String] = { rdd.map(func1) }
}</code></pre>

<p>Here, if we create a new MyClass instance and call doStuff on it, the map inside there references the func1 method of that MyClass instance, so the whole object needs to be sent to the cluster. It is similar to writing rdd.map(x =&gt; this.func1(x)).</p>

<p>这里，如果我们创建一个MyClass的实例并调用了它的doStuff方法，这里的map操作中引用了MyClass实例的func1方法，所以整个MyClass对象都需要被发送的集群上，等同于rdd.map(x =&gt; this.func1(x)).</p>

<p>In a similar way, accessing fields of the outer object will reference the whole object:</p>

<p>以类似的方式，访问外部对象的字段将引用整个对象：</p>



<pre class="prettyprint"><code class=" hljs haskell"><span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-type">MyClass</span> {
  val field = "<span class="hljs-type">Hello</span>"
  def doStuff<span class="hljs-container">(<span class="hljs-title">rdd</span>: <span class="hljs-type">RDD</span>[<span class="hljs-type">String</span>])</span>: <span class="hljs-type">RDD</span>[<span class="hljs-type">String</span>] = { rdd.map<span class="hljs-container">(<span class="hljs-title">x</span> =&gt; <span class="hljs-title">field</span> + <span class="hljs-title">x</span>)</span> }
}</span></code></pre>

<p>is equivalent to writing rdd.map(x =&gt; this.field + x), which references all of this. To avoid this issue, the simplest way is to copy field into a local variable instead of accessing it externally:</p>

<p>这等同于rdd.map(x =&gt; this.field + x)，引用了整个对象。最简单的方法是将字段复制到本地变量，而不是外部访问它：</p>



<pre class="prettyprint"><code class=" hljs python"><span class="hljs-function"><span class="hljs-keyword">def</span> <span class="hljs-title">doStuff</span><span class="hljs-params">(rdd: RDD[String])</span>:</span> RDD[String] = {
  val field_ = this.field
  rdd.map(x =&gt; field_ + x)
}</code></pre>



<h5 id="understanding-closures">Understanding closures</h5>

<p>One of the harder things about Spark is understanding the scope and life cycle of variables and methods when executing code across a cluster. RDD operations that modify variables outside of their scope can be a frequent source of confusion. In the example below we’ll look at code that uses foreach() to increment a counter, but similar issues can occur for other operations as well.</p>

<p>想要理解Spark的方法和变量在集群中的生命周期是比较困难的。RDD操作在其范围之外修改变量可能是造成混淆的常见原因。在下面的示例中，我们将查看使用foreach()来增加计数器的代码，但是类似的问题也可能发生在其他操作中。</p>



<h6 id="example">Example</h6>

<p>Consider the naive RDD element sum below, which may behave differently depending on whether execution is happening within the same JVM. A common example of this is when running Spark in local mode (–master = local[n]) versus deploying a Spark application to a cluster (e.g. via spark-submit to YARN):</p>

<p>看下面一个简单的 RDD 元素求和，执行结果可能不同，具体取决于是否在同一个 JVM 中执行. 一个常见的例子是当 Spark 运行在 local 本地模式（–master = local[n]）时，与部署 Spark 应用到群集（例如，通过 spark-submit 到 YARN）:</p>



<pre class="prettyprint"><code class=" hljs php"><span class="hljs-keyword">var</span> counter = <span class="hljs-number">0</span>
<span class="hljs-keyword">var</span> rdd = sc.parallelize(data)

<span class="hljs-comment">// Wrong: Don't do this!!</span>
rdd.<span class="hljs-keyword">foreach</span>(x =&gt; counter += x)

println(<span class="hljs-string">"Counter value: "</span> + counter)</code></pre>



<h6 id="local-vs-cluster-modes">Local vs. cluster modes</h6>

<p>The behavior of the above code is undefined, and may not work as intended. To execute jobs, Spark breaks up the processing of RDD operations into tasks, each of which is executed by an executor. Prior to execution, Spark computes the task’s closure. The closure is those variables and methods which must be visible for the executor to perform its computations on the RDD (in this case foreach()). This closure is serialized and sent to each executor.</p>

<p>上述代码的行为未定义，可能不会按预期的方式工作。执行作业时，Spark将会分解RDD操作并加入到任务队列中，每个任务都运行在一个单独的执行器中。在执行之前，Spark将确认任务的闭包，而闭包是在RDD上的executor必须能够访问的变量和方法（例中的 foreach()）。闭包被序列化并被发送到每个执行器。</p>

<p>The variables within the closure sent to each executor are now copies and thus, when counter is referenced within the foreach function, it’s no longer the counter on the driver node. There is still a counter in the memory of the driver node but this is no longer visible to the executors! The executors only see the copy from the serialized closure. Thus, the final value of counter will still be zero since all operations on counter were referencing the value within the serialized closure.</p>

<p>闭包的变量副本发给每个 counter ，当 counter 被 foreach 函数引用的时候，它已经不再是 driver node 的 counter 了。虽然在 driver node 仍然有一个 counter 在内存中，但是对 executors 已经不可见。executor 看到的只是序列化的闭包一个副本。所以 counter 最终的值还是 0，因为对 counter 所有的操作均引用序列化的 closure 内的值。</p>

<p>In local mode, in some circumstances the foreach function will actually execute within the same JVM as the driver and will reference the same original counter, and may actually update it.</p>

<p>在 local 本地模式，在某些情况下的foreach功能实际上是同一JVM上的驱动程序中执行，并会引用同一个原始的counter计数器，实际上可能进行更新操作.</p>

<p>To ensure well-defined behavior in these sorts of scenarios one should use an Accumulator. Accumulators in Spark are used specifically to provide a mechanism for safely updating a variable when execution is split up across worker nodes in a cluster. The Accumulators section of this guide discusses these in more detail.</p>

<p>为了确保类似的场景下行为明确应用使用累加器（Accumulator）。Spark的累加器使用了一种特殊的方式提供了一种安全的机制去更新集群中各个worker节点的变量。本指南的累加器部分将更详细地讨论这些问题。</p>

<p>In general, closures - constructs like loops or locally defined methods, should not be used to mutate some global state. Spark does not define or guarantee the behavior of mutations to objects referenced from outside of closures. Some code that does this may work in local mode, but that’s just by accident and such code will not behave as expected in distributed mode. Use an Accumulator instead if some global aggregation is needed.</p>

<p>通常情况下，closures - constructs类似于循环或局部定义的方法，不应该用户去改变一些全局的状态。Spark不能保证类似的行为能够正常的执行。有些代码可以在本地模式下工作，但这只是偶然的，这样的代码在分布式模式下不会像预期的那样运行。如果需要一些全局的聚合功能，应使用 Accumulator（累加器）。</p>



<h6 id="printing-elements-of-an-rdd">Printing elements of an RDD</h6>

<p>Another common idiom is attempting to print out the elements of an RDD using rdd.foreach(println) or rdd.map(println). On a single machine, this will generate the expected output and print all the RDD’s elements. However, in cluster mode, the output to stdout being called by the executors is now writing to the executor’s stdout instead, not the one on the driver, so stdout on the driver won’t show these! To print all elements on the driver, one can use the collect() method to first bring the RDD to the driver node thus: rdd.collect().foreach(println). This can cause the driver to run out of memory, though, because collect() fetches the entire RDD to a single machine; if you only need to print a few elements of the RDD, a safer approach is to use the take(): rdd.take(100).foreach(println).</p>

<p>另一种常见的语法用于打印 RDD 的所有元素使用rdd.foreach(println)或rdd.map(println)。在单台机器上，这将预期输出和打印 RDD 的所有元素。在集群模式下，标准输出将会被集群节点中的executor所替代，而不会再驱动程序中输出，所以驱动程序中不会输出元素信息！要打印 driver 程序的所有元素，可以使用的 collect() 方法首先把 RDD 放到 driver 程序节点上，像这样: rdd.collect().foreach(println)。但是这可能会导致驱动节点内存耗尽，由于collect()会获取RDD所有元素到一台机器上，如果你仅仅需要打印几个元素，一种更安全的方式是使用take()方法：rdd.take(100).foreach(println).</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>