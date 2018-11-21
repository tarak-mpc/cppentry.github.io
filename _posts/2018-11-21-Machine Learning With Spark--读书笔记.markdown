---
layout:     post
title:      Machine Learning With Spark--读书笔记
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/qq_32108329/article/details/72964880				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="第一章-spark的环境搭建与运行">第一章 Spark的环境搭建与运行</h1>

<p>Spark的支持四种运行模式 <br>
 - 本地单机模式Spark Standalone：所有Spark进程都运行在同一个Java虚拟机中。 <br>
 - 集群单机模式：使用Spark内置的任务调度框架。 <br>
 - 基于Mesos：Mesos是一个流行的开源集群计算框架。 <br>
 -基于YARN：即Hadoop2，它是一个与Hadoop关联的集群计算和资源调度框架。</p>



<h2 id="11-spark的本地安装与配置">1.1 Spark的本地安装与配置</h2>

<p>推荐阅读Spark官方文档: <a href="http://spark.apache.org/docs/latest" rel="nofollow" target="_blank">http://spark.apache.org/docs/latest</a> <br>
安装配置单机版Spark之后，可以运行Spark附带的一个示例程序来测试是否一切正常：</p>



<pre class="prettyprint"><code class=" hljs avrasm">&gt;./bin/run-example org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.examples</span><span class="hljs-preprocessor">.SparkPi</span>
要在本地模式下设置并行的级别，以local[N]的格式来指定一个master变量即可。上述参数中的N表示要使用的线程数目。比如只使用两个线程时，可输入如下命令：
&gt;MASTER=local[<span class="hljs-number">2</span>] ./bin/run-example org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.examples</span><span class="hljs-preprocessor">.SparkPi</span></code></pre>



<h2 id="12-spark集群">1.2 Spark集群</h2>

<p>本书中将使用Spark的本地单机模式做概念讲解和举例说明，但所用的代码也可运行在Spark集群上。比如在一个Spark单机集群上运行上述示例，只需传入主节点的URL即可:</p>



<pre class="prettyprint"><code class=" hljs avrasm">&gt;MASTER-spark://IP:PORT ./bin/run-example org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.examples</span><span class="hljs-preprocessor">.SparkPi</span></code></pre>

<p>其中IP和PORT是主节点Master的IP地址和端口号。</p>



<h2 id="13-spark编程模型">1.3 Spark编程模型</h2>



<h3 id="131-sparkcontext类与sparkconf类">1.3.1 SparkContext类与SparkConf类</h3>

<p>我的理解是这一步是任何Spark程序的开始，即可以称为Environment Initialization。</p>



<pre class="prettyprint"><code class=" hljs fsharp"><span class="hljs-number">1.</span>first edition
<span class="hljs-keyword">val</span> conf = <span class="hljs-keyword">new</span> SparkConf().setAppName(<span class="hljs-string">"Test Spark App"</span>).setMaster(<span class="hljs-string">"local[4]"</span>)
<span class="hljs-keyword">val</span> sc = <span class="hljs-keyword">new</span> SparkContext(conf)
<span class="hljs-number">2.</span>second edition
<span class="hljs-keyword">val</span> sc = <span class="hljs-keyword">new</span> SparkContext(<span class="hljs-string">"local[4]"</span>, <span class="hljs-string">"Test Spark App"</span>)

ps:如果是提交到Spark集群上去运行，那么setMaster(<span class="hljs-string">"spark://host:port"</span>)填写指定Master URL，具体可以去WebUI中查看。</code></pre>



<h3 id="132-spark-shell">1.3.2 Spark Shell</h3>

<p>支持利用Scala或Python的交互式shell来进行交互式的程序编写。输入的代码会被立即计算，比较容易理解。spark shell会自动给你初始化好sc as SparkContext。</p>

<pre class="prettyprint"><code class=" hljs livecodeserver">使用scala <span class="hljs-keyword">for</span> spark <span class="hljs-built_in">shell</span>:
&gt;./bin/spark-<span class="hljs-built_in">shell</span>
使用python <span class="hljs-keyword">for</span> spark <span class="hljs-built_in">shell</span>:
&gt;./bin/pyspark</code></pre>



<h3 id="133-弹性分布式数据集resilient-distributed-dataset-rdd">1.3.3 弹性分布式数据集(Resilient Distributed Dataset, RDD)</h3>

<p>1.创建RDD</p>



<pre class="prettyprint"><code class=" hljs fsharp"><span class="hljs-keyword">val</span> collection = List(<span class="hljs-string">"a"</span>, <span class="hljs-string">"b"</span>, <span class="hljs-string">"c"</span>, <span class="hljs-string">"d"</span>, <span class="hljs-string">"e"</span>)
<span class="hljs-keyword">val</span> rddFormCollection = sc.parallelize(collection)
OR
<span class="hljs-keyword">val</span> rddFormTextFile = sc.textFile(<span class="hljs-string">"File Path"</span>)</code></pre>

<p>2.Spark操作 <br>
Spark Operations = Transformation(Wait) + Action(Run) <br>
转换操作是Lazy的，即只有当运行Action操作时才会执行Transformation操作。</p>

<p>3.RDD缓存策略 <br>
persist and cache。详情：<a href="http://spark.apache.org/docs/latest/programming-guide.html#rdd-persistence" rel="nofollow" target="_blank">http://spark.apache.org/docs/latest/programming-guide.html#rdd-persistence</a></p>



<h3 id="134-广播变量和累加器">1.3.4 广播变量和累加器</h3>

<p>暂时用不到，就不写这部分了。</p>



<h2 id="14-spark-scala-编程入门">1.4 Spark Scala 编程入门</h2>

<p>熟能生巧看代码。 <br>
输入数据： <br>
John,iPhone Cover,9.99 <br>
John,Headphones,5.49 <br>
Jack,iPhone Cover,9.99 <br>
Jill,Samsung Galaxy Cover,8.95 <br>
Bob,iPad Cover,5.49</p>



<pre class="prettyprint"><code class=" hljs scala"><span class="hljs-class"><span class="hljs-keyword">object</span> <span class="hljs-title">Learning_SparkWithScal</span> {</span>
  <span class="hljs-keyword">def</span> main(args: Array[String]): Unit = {
    <span class="hljs-keyword">val</span> conf = <span class="hljs-keyword">new</span> SparkConf()
      .setAppName(<span class="hljs-string">"Learn Spark"</span>)
      .setMaster(<span class="hljs-string">"local[2]"</span>)
    <span class="hljs-keyword">val</span> sc = <span class="hljs-keyword">new</span> SparkContext(conf)

    <span class="hljs-keyword">val</span> data = sc.textFile(<span class="hljs-string">"data/UserPurchaseHistory.csv"</span>)
      .map(line =&gt; line.split(<span class="hljs-string">","</span>))
      .map(purchaseRecord =&gt; (purchaseRecord(<span class="hljs-number">0</span>), purchaseRecord(<span class="hljs-number">1</span>), purchaseRecord(<span class="hljs-number">2</span>)))

    <span class="hljs-comment">//求购买次数</span>
    <span class="hljs-keyword">val</span> numPurchases = data.count()
    <span class="hljs-comment">//求有多少个不同用户购买过商品</span>
    <span class="hljs-keyword">val</span> uniqueUsers = data.map{ <span class="hljs-keyword">case</span> (user, product, price) =&gt; user}.distinct().count()
    <span class="hljs-comment">//求和得出总收入</span>
    <span class="hljs-keyword">val</span> totalRevenue = data.map{<span class="hljs-keyword">case</span> (user, product, price) =&gt; price.toDouble}.sum()
    <span class="hljs-comment">//求出最畅销的产品是什么</span>
    <span class="hljs-keyword">val</span> productsByPopularity = data.map{ <span class="hljs-keyword">case</span> (user, product, price) =&gt; (product, <span class="hljs-number">1</span>)}
      .reduceByKey(_ + _)
      .collect()
      .sortBy(-_._2)
    <span class="hljs-keyword">val</span> mostPopular = productsByPopularity(<span class="hljs-number">0</span>)

    println(<span class="hljs-string">"Total purchases: "</span> + numPurchases)
    println(<span class="hljs-string">"Unique users: "</span> + uniqueUsers)
    println(<span class="hljs-string">"Total revenue: "</span> + totalRevenue)
    println(<span class="hljs-string">"Most popular product: %s with %d purchases"</span>.format(mostPopular._1, mostPopular._2))
  }
}</code></pre>



<h2 id="15-spark-java编程入门">1.5 Spark Java编程入门</h2>

<p>很少用，相比Scala来说没有啥优势。</p>



<h2 id="16-spark-python-编程入门">1.6 Spark Python 编程入门</h2>

<p>和Scala语法差不多，主要是匿名函数的写法上，但是没有Scala逼格高。：D</p>



<h2 id="17-在amazon-ec2-上运行spark">1.7 在Amazon EC2 上运行Spark</h2>

<p>搭建Spark集群，因为这一部分我自己搭过所以也算是略有心得了。</p>

<p>======================2017.6.10 更新===========================</p>



<h1 id="第二章-设计机器学习系统">第二章 设计机器学习系统</h1>

<p>这一章大多是文字介绍的功夫，这里就不多扯了。</p>



<h1 id="第三章-spark上数据的获取处理与准备">第三章 Spark上数据的获取、处理与准备</h1>

<p>本章内容包括：</p>

<ul>
<li>简要概述机器学习中用到的数据类型</li>
<li>举例说明从何处获取感兴趣的数据集（通常可从因特网上获取），其中一些会用于阐述本书中所涉及模型的应用</li>
<li>了解数据的处理、清理、探索和可视化方法</li>
<li>介绍将原始数据转换为可用于机器学习算法特征的各种技术</li>
<li>学习如何使用外部库或Spark内置函数来正则化输入特征</li>
</ul>

<h2 id="31-获取公开数据集">3.1 获取公开数据集</h2>

<p>常见的公开数据集：</p>

<ul>
<li>UCL机器学习知识库 <a href="http://archive.ics.uci.edu/ml/" rel="nofollow" target="_blank">http://archive.ics.uci.edu/ml/</a></li>
<li>Amazon AWS公开数据集 <a href="http://aws.amazon.com/publicdatasets/" rel="nofollow" target="_blank">http://aws.amazon.com/publicdatasets/</a></li>
<li>Kaggle <a href="http://www.kaggle.com/competitions" rel="nofollow" target="_blank">http://www.kaggle.com/competitions</a></li>
<li>KDnuggets <a href="http://www.kdnuggets.com/datasets/index.html" rel="nofollow" target="_blank">http://www.kdnuggets.com/datasets/index.html</a></li>
</ul>

<p>本章用到的数据集为MovieLens 100k数据集，下载地址: <a href="http://files.grouplens.org/datasets/movielens/ml-100k.zip" rel="nofollow" target="_blank">http://files.grouplens.org/datasets/movielens/ml-100k.zip</a> <br>
 下载下来后使用unzip命令解压缩，其中比较重要的文件有u.user(用户属性文件), u.item(电影元数据) 和 u.data(用户对电影的评级)</p>

<h2 id="32-探索与可视化数据">3.2 探索与可视化数据</h2>

<p>本文将使用IPython交互式终端和matplotlib库来对数据进行处理和可视化，故我们会用到Python和PySpark shell。 <br>
这里我们使用IPython Notebook，相关安装教程见链接： <br>
<a href="http://blog.csdn.net/schwxd/article/details/53863483" rel="nofollow" target="_blank">http://blog.csdn.net/schwxd/article/details/53863483</a> <br>
我这里安装的是Anaconda2,期间还发生了虚拟机内存不够的状况，大概花了30分钟左右才重新分配了更大的内存。关于Vmware虚拟机内存分配的相关链接： <br>
<a href="http://blog.csdn.net/timsley/article/details/50742755" rel="nofollow" target="_blank">http://blog.csdn.net/timsley/article/details/50742755</a> <br>
在上述博文里我也遇到了点问题。比如： <br>
<img src="https://img-blog.csdn.net/20170610162537255?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMzIxMDgzMjk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""> <br>
后来发现可以在软件商店中直接下载安装-.-。后序的操作都跟博文中的差不多了，可能就是博文中有些地方描述的过于简单，我一头雾水的操作了半天才弄懂。</p>

<p>言归正传：什么都搞完之后，启动ipthon notebook,然后就打开了如下网页： <br>
<img src="https://img-blog.csdn.net/20170610163031820?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMzIxMDgzMjk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""> <br>
在界面的右边进行如下操作：点击New-&gt; 选择Python2，就会新建一个python2的界面了！（感觉这个类似于一个网页版的python IDE） <br>
<img src="https://img-blog.csdn.net/20170610163143054?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMzIxMDgzMjk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""> <br>
输入一下简单的语句进行测试： <br>
<img src="https://img-blog.csdn.net/20170610163330154?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMzIxMDgzMjk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""> <br>
最后说一下简单的快捷键： <br>
<img src="https://img-blog.csdn.net/20170610163441562?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMzIxMDgzMjk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""> <br>
暂时看不懂没关系，编程的时候随便用用你就懂了！ <br>
好了，又掌握了一个新的看起来还不错的IDE，那么进行愉快的Spark的编程吧- -!</p>



<h3 id="321-探索用户数据">3.2.1 探索用户数据</h3>



<pre class="prettyprint"><code class=" hljs perl"><span class="hljs-comment">#读入数据</span>
user_data = sc.textFile(<span class="hljs-string">"/home/whu/ml-100k/u.user"</span>)
user_data.first()
<span class="hljs-comment">#统计各个维度的总数，count()函数</span>
user_fields = user_data.<span class="hljs-keyword">map</span>(lambda line: line.<span class="hljs-keyword">split</span>(<span class="hljs-string">"|"</span>))
num_users = user_fields.<span class="hljs-keyword">map</span>(lambda fields: fields[<span class="hljs-number">0</span>]).count()
num_genders = user_fields.<span class="hljs-keyword">map</span>(lambda fields: fields[<span class="hljs-number">2</span>]).distinct().count()
num_occupations = user_fields.<span class="hljs-keyword">map</span>(lambda fields: fields[<span class="hljs-number">3</span>]).distinct().count()
num_zipcodes = user_fields.<span class="hljs-keyword">map</span>(lambda fields: fields[<span class="hljs-number">4</span>]).distinct().count()
<span class="hljs-keyword">print</span> <span class="hljs-string">"Users: <span class="hljs-variable">%d</span>, genders: <span class="hljs-variable">%d</span>, occupations: <span class="hljs-variable">%d</span>, ZIP codes: <span class="hljs-variable">%d</span>"</span> % (num_users, num_genders, num_occupations, num_zipcodes)
<span class="hljs-comment">#画用户年龄段分布的直方图</span>
<span class="hljs-comment">#%pylab inline暂时是什么用不清楚，大概就是类似import numpy , matplotlib类似的功能吧</span>
<span class="hljs-variable">%pylab</span> inline
ages = user_fields.<span class="hljs-keyword">map</span>(lambda <span class="hljs-keyword">x</span>: <span class="hljs-keyword">int</span>(<span class="hljs-keyword">x</span>[<span class="hljs-number">1</span>])).collect()
hist(ages, bins=<span class="hljs-number">20</span>, color=<span class="hljs-string">'lightblue'</span>, normed=True)
<span class="hljs-comment">#hist函数的输入参数有ages数组，直方图的bins数目（即区间数，这里为20）。同时还使用了normed=True参数来正则化直方图，即让每个方条表示年龄在该区间内的数据量占总数据量的比。</span>
fig = matplotlib.pyplot.gcf()
fig.set_size_inches(<span class="hljs-number">16</span>, <span class="hljs-number">10</span>)</code></pre>

<p>结果图：至于为什么我这没有竖着的边缘黑线我也不是很清楚。。。 <br>
<img src="https://img-blog.csdn.net/20170610165344207?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMzIxMDgzMjk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""> <br>
接下来计算用户职业的分布情况：</p>

<pre class="prettyprint"><code class=" hljs python">count_by_occupation = user_fields.map(<span class="hljs-keyword">lambda</span> fields: (fields[<span class="hljs-number">3</span>], <span class="hljs-number">1</span>)).reduceByKey(<span class="hljs-keyword">lambda</span> x, y: x + y).collect()
<span class="hljs-comment">#将RDD转换为两个数组</span>
x_axis1 = np.array([c[<span class="hljs-number">0</span>] <span class="hljs-keyword">for</span> c <span class="hljs-keyword">in</span> count_by_occupation]) <span class="hljs-comment"># 职业标签轴</span>
y_axis1 = np.array([c[<span class="hljs-number">1</span>] <span class="hljs-keyword">for</span> c <span class="hljs-keyword">in</span> count_by_occupation]) <span class="hljs-comment"># 数量轴</span>
<span class="hljs-comment">#我们需要对该数据进行排序，从而在条形图中以从少到多的顺序来显示各个职业</span>
<span class="hljs-comment">#为此可先创建两个数组，调用numpy的argsort函数来以数量升序从各数组中选取元素</span>
x_axis = x_axis1[np.argsort(y_axis1)]
y_axis = y_axis1[np.argsort(y_axis1)]

pos = np.arange(len(x_axis))
width = <span class="hljs-number">1.0</span>
ax = plt.axes()
ax.set_xticks(pos + (width / <span class="hljs-number">2</span>))
ax.set_xticklabels(x_axis)

plt.bar(pos, y_axis, width, color=<span class="hljs-string">'lightblue'</span>)
plt.xticks(rotation=<span class="hljs-number">30</span>)
fig = matplotlib.pyplot.gcf()
fig.set_size_inches(<span class="hljs-number">16</span>, <span class="hljs-number">10</span>)</code></pre>

<p>结果图： <br>
<img src="https://img-blog.csdn.net/20170610170010835?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMzIxMDgzMjk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""> <br>
Spark对RDD提供了一个名为countByValue()的便捷函数。它会计算RDD里各不同值所分别出现的次数，并将其以Python dict函数的形式(或是Scala、Java下的Map函数)返回给驱动程序：</p>



<pre class="prettyprint"><code class=" hljs python">count_by_occupation2 = user_fields.map(<span class="hljs-keyword">lambda</span> fields: fields[<span class="hljs-number">3</span>]).countByValue()
<span class="hljs-keyword">print</span> <span class="hljs-string">"Map-reduce approach: "</span>
<span class="hljs-keyword">print</span> dict(count_by_occupation2)
<span class="hljs-keyword">print</span> <span class="hljs-string">""</span>
<span class="hljs-keyword">print</span> <span class="hljs-string">"countByValue approach:"</span>
<span class="hljs-keyword">print</span> dict(count_by_occupation)</code></pre>

<p>结果： <br>
<img src="https://img-blog.csdn.net/20170610170237539?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMzIxMDgzMjk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<h3 id="322-探索电影数据">3.2.2 探索电影数据</h3>



<pre class="prettyprint"><code class=" hljs python">movie_data = sc.textFile(<span class="hljs-string">"/home/whu/ml-100k/u.item"</span>)
<span class="hljs-keyword">print</span> movie_data.first()
num_movie = movie_data.count()
<span class="hljs-keyword">print</span> <span class="hljs-string">"Movies: %d"</span> % num_movie
<span class="hljs-comment"># 有些电影数据不规整，需要如下函数来处理解析错误。</span>
<span class="hljs-function"><span class="hljs-keyword">def</span> <span class="hljs-title">convert_year</span><span class="hljs-params">(x)</span> :</span>
    <span class="hljs-keyword">try</span>:
        <span class="hljs-keyword">return</span> int(x[-<span class="hljs-number">4</span>:]) <span class="hljs-comment"># 取最后四个元素</span>
    <span class="hljs-keyword">except</span>:
        <span class="hljs-keyword">return</span> <span class="hljs-number">1990</span>
movie_fields = movie_data.map(<span class="hljs-keyword">lambda</span> lines: lines.split(<span class="hljs-string">"|"</span>))
years = movie_fields.map(<span class="hljs-keyword">lambda</span> fields: fields[<span class="hljs-number">2</span>]).map(<span class="hljs-keyword">lambda</span> x: convert_year(x))
<span class="hljs-comment">#使用Spark的filter操作过滤掉这些1990数据</span>
years_filter = years.filter(<span class="hljs-keyword">lambda</span> x: x != <span class="hljs-number">1990</span>)
<span class="hljs-comment">#画电影的年龄分布图（用当前年份1998减去发行年份代表电影的年龄）</span>
movie_ages = years_filter.map(<span class="hljs-keyword">lambda</span> yr: <span class="hljs-number">1998</span>-yr).countByValue()
values = movie_ages.values()
bins = movie_ages.keys()
hist(values, bins=bins, color=<span class="hljs-string">'lightblue'</span>, normed=<span class="hljs-keyword">True</span>)
fig = matplotlib.pyplot.gcf()
fig.set_size_inches(<span class="hljs-number">6</span>, <span class="hljs-number">10</span>)</code></pre>

<p>结果图： <br>
<img src="https://img-blog.csdn.net/20170610170823138?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMzIxMDgzMjk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<h3 id="323-探索评级数据">3.2.3 探索评级数据</h3>



<pre class="prettyprint"><code class=" hljs perl">rating_data = sc.textFile(<span class="hljs-string">"/home/whu/ml-100k/u.data"</span>)
<span class="hljs-keyword">print</span> rating_data.first()
num_ratings = rating_data.count()
<span class="hljs-keyword">print</span> <span class="hljs-string">"Ratings: <span class="hljs-variable">%d</span>"</span> % num_ratings

rating_data_raw = rating_data.<span class="hljs-keyword">map</span>(lambda line: line.<span class="hljs-keyword">split</span>(<span class="hljs-string">"\t"</span>))
ratings = rating_data_raw.<span class="hljs-keyword">map</span>(lambda fields: <span class="hljs-keyword">int</span>(fields[<span class="hljs-number">2</span>]))
max_rating = ratings.reduce(lambda <span class="hljs-keyword">x</span>,<span class="hljs-keyword">y</span>: max(<span class="hljs-keyword">x</span>, <span class="hljs-keyword">y</span>))
min_rating = ratings.reduce(lambda <span class="hljs-keyword">x</span>,<span class="hljs-keyword">y</span>: min(<span class="hljs-keyword">x</span>, <span class="hljs-keyword">y</span>))
mean_rating = ratings.reduce(lambda <span class="hljs-keyword">x</span>,<span class="hljs-keyword">y</span>: <span class="hljs-keyword">x</span> + <span class="hljs-keyword">y</span>) / double(num_ratings)
median_rating = np.median(ratings.collect())
ratings_per_user = num_ratings / num_users
ratings_per_movie = num_ratings / num_movie

<span class="hljs-keyword">print</span> <span class="hljs-string">"Min rating: <span class="hljs-variable">%d</span>"</span> % min_rating
<span class="hljs-keyword">print</span> <span class="hljs-string">"Max rating: <span class="hljs-variable">%d</span>"</span> % max_rating
<span class="hljs-keyword">print</span> <span class="hljs-string">"Average rating: <span class="hljs-variable">%2</span>.2f"</span> % mean_rating
<span class="hljs-keyword">print</span> <span class="hljs-string">"Median rating: <span class="hljs-variable">%d</span>"</span> % median_rating
<span class="hljs-keyword">print</span> <span class="hljs-string">"Average # of ratings per user: <span class="hljs-variable">%2</span>.2f"</span> % ratings_per_user
<span class="hljs-keyword">print</span> <span class="hljs-string">"Average # of ratings per movie: <span class="hljs-variable">%2</span>.2f"</span> % ratings_per_movie

<span class="hljs-comment">#Spark对RDD提供一个名为stats()的函数，功能如下：</span>
ratings.stats()
<span class="hljs-comment"># 结果：(count: 100000, mean: 3.52986, stdev: 1.12566797076, max: 5.0, min: 1.0)】</span>

<span class="hljs-comment"># 绘制电影评级的分布</span>
count_by_ratings = ratings.countByValue()
x_axis = np.array(count_by_ratings.<span class="hljs-keyword">keys</span>())
y_axis = np.array([float(c) <span class="hljs-keyword">for</span> c in count_by_ratings.<span class="hljs-keyword">values</span>()])
<span class="hljs-comment">#这里对y轴正则化，使它表示百分比</span>
y_axis_normed = y_axis / y_axis.sum()
<span class="hljs-keyword">pos</span> = np.arange(len(x_axis))
width = <span class="hljs-number">1.0</span>

ax = plt.axes()
ax.set_xticks(<span class="hljs-keyword">pos</span> + (width / <span class="hljs-number">2</span>))
ax.set_xticklabels(x_axis)

plt.bar(<span class="hljs-keyword">pos</span>, y_axis_normed, width, color=<span class="hljs-string">'lightblue'</span>)
plt.xticks(rotation=<span class="hljs-number">30</span>)
fig = matplotlib.pyplot.gcf()
fig.set_size_inches(<span class="hljs-number">16</span>, <span class="hljs-number">10</span>)

<span class="hljs-comment">#绘制各个用户的电影评级分布</span>
user_ratings_grouped = rating_data_raw.<span class="hljs-keyword">map</span>(lambda fields: (<span class="hljs-keyword">int</span>(fields[<span class="hljs-number">0</span>]), <span class="hljs-number">1</span>)).reduceByKey(lambda <span class="hljs-keyword">x</span>, <span class="hljs-keyword">y</span>: <span class="hljs-keyword">x</span> + <span class="hljs-keyword">y</span>)
<span class="hljs-comment">#user_ratings_byuser = user_ratings_grouped.map(lambda (k, v): (k, len(v)))</span>
user_ratings_grouped.take(<span class="hljs-number">5</span>)
<span class="hljs-comment"># 结果：[(1, 272), (2, 62), (3, 54), (4, 24), (5, 175)]</span>

user_ratings_byuser_local = user_ratings_byuser.<span class="hljs-keyword">map</span>(lambda (k, v): v).collect()
hist(user_ratings_byuser_local, bins=<span class="hljs-number">200</span>, color=<span class="hljs-string">'lightblue'</span>, normed=True)
fig = matplotlib.pyplot.gcf()
fig.set_size_inches(<span class="hljs-number">16</span>, <span class="hljs-number">10</span>)</code></pre>

<h2 id="33-处理与转换数据">3.3 处理与转换数据</h2>

<p>主要是 非规整数据和缺失数据的填充，比如之前用1990年来填充那些没有发行年份的电影。 <br>
这段代码不是很懂np函数参数的写法，暂时也懒得去看了-。-</p>

<pre class="prettyprint"><code class=" hljs markdown">years<span class="hljs-emphasis">_pre_</span>processed = movie<span class="hljs-emphasis">_fields.map(lambda fields: fields[2]).map(lambda x: convert_</span>year(x)).collect()
years<span class="hljs-emphasis">_pre_</span>processed<span class="hljs-emphasis">_array = np.array(years_</span>pre_processed)

mean<span class="hljs-emphasis">_year = np.mean(years_</span>pre<span class="hljs-emphasis">_processed_</span>array[years<span class="hljs-emphasis">_pre_</span>processed_array != 1990])
median<span class="hljs-emphasis">_year = np.median(years_</span>pre<span class="hljs-emphasis">_processed_</span>array[years<span class="hljs-emphasis">_pre_</span>processed_array != 1990])
index<span class="hljs-emphasis">_bad_</span>data = np.where(years<span class="hljs-emphasis">_pre_</span>processed_array == 1990)[<span class="hljs-link_label">0</span>][<span class="hljs-link_reference">0</span>]
years<span class="hljs-emphasis">_pre_</span>processed<span class="hljs-emphasis">_array[index_</span>bad<span class="hljs-emphasis">_data] = median_</span>year

print "Mean year of release: %d" % mean_year
print "Median year of release: %d" % median_year
print "Index of '1990' after assigning median: %s" % np.where(years<span class="hljs-emphasis">_pre_</span>processed_array == 1990)[0]
</code></pre>



<h2 id="34-从数据中提取有用特征">3.4 从数据中提取有用特征</h2>

<p>主要针对：</p>

<ul>
<li>数值特性</li>
<li>类别特征</li>
<li>文本特征</li>
<li>其他特征</li>
</ul>



<h3 id="341-数值特征">3.4.1 数值特征</h3>

<p>这个就比较简单了，基本上不需要做啥处理。</p>

<h3 id="342-类别特征">3.4.2 类别特征</h3>

<p>类别特征就是那些各个取值之间没有明确的顺序关系的，比如性别、职业等等。将类别特征表示为数字形式，常用k之1的方法，即Hash思想编号。</p>

<pre class="prettyprint"><code class=" hljs perl">all_occupations = user_fields.<span class="hljs-keyword">map</span>(lambda fields: fields[<span class="hljs-number">3</span>]).distinct().collect()
all_occupations.<span class="hljs-keyword">sort</span>()
idx = <span class="hljs-number">0</span>
all_occupations_dict = {}
<span class="hljs-keyword">for</span> o in all_occupations:
    all_occupations_dict[o] = idx
    idx += <span class="hljs-number">1</span>
<span class="hljs-keyword">print</span> <span class="hljs-string">"Encoding of 'doctor': <span class="hljs-variable">%d</span>"</span> % all_occupations_dict[<span class="hljs-string">'doctor'</span>]
<span class="hljs-keyword">print</span> <span class="hljs-string">"Encoding of 'programmer': <span class="hljs-variable">%d</span>"</span> % all_occupations_dict[<span class="hljs-string">'programmer'</span>]
<span class="hljs-comment"># 结果：Encoding of 'doctor': 2</span>
<span class="hljs-comment">#      Encoding of 'programmer': 14</span>

<span class="hljs-comment">#再将编号转化为长度为k的二元向量来表示一个变量的取值</span>
K = len(all_occupations_dict)
binary_x = np.zeros(K)
k_programmer = all_occupations_dict[<span class="hljs-string">'programmer'</span>]
binary_x[k_programmer] = <span class="hljs-number">1</span>
<span class="hljs-keyword">print</span> <span class="hljs-string">"Binary feature vector: <span class="hljs-variable">%s</span>"</span> % binary_x
<span class="hljs-keyword">print</span> <span class="hljs-string">"Length of binary vector: <span class="hljs-variable">%d</span>"</span> % K
<span class="hljs-comment">#输出：</span>
Binary feature vector: [ <span class="hljs-number">0</span>.  <span class="hljs-number">0</span>.  <span class="hljs-number">0</span>.  <span class="hljs-number">0</span>.  <span class="hljs-number">0</span>.  <span class="hljs-number">0</span>.  <span class="hljs-number">0</span>.  <span class="hljs-number">0</span>.  <span class="hljs-number">0</span>.  <span class="hljs-number">0</span>.  <span class="hljs-number">0</span>.  <span class="hljs-number">0</span>.  <span class="hljs-number">0</span>.  <span class="hljs-number">0</span>.  <span class="hljs-number">1</span>.  <span class="hljs-number">0</span>.  <span class="hljs-number">0</span>.  <span class="hljs-number">0</span>. <span class="hljs-number">0</span>.  <span class="hljs-number">0</span>.  <span class="hljs-number">0</span>.]
Length of binary vector: <span class="hljs-number">21</span></code></pre>



<h3 id="343-派生特征">3.4.3 派生特征</h3>

<p>======================今天就先学到这里，周六晚上给自己放个假吧================</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>