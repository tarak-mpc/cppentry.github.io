---
layout:     post
title:      Distributed Computing: Application Design in Hadoop
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p></p><div class="toc"><div class="toc">
<ul>
<li><a href="#%E8%AE%BE%E8%AE%A1hadoop%E7%AE%97%E6%B3%95" rel="nofollow">设计Hadoop算法</a></li>
<li><a href="#hadoop%E7%9A%84%E8%AE%BE%E8%AE%A1%E5%8E%9F%E5%88%99" rel="nofollow">Hadoop的设计原则</a><ul>
<li><a href="#local-aggregationcount" rel="nofollow">local aggregationcount</a></li>
<li><a href="#pairs-and-stripesco-occurrence" rel="nofollow">pairs and stripesco-occurrence</a></li>
<li><a href="#order-inversionrelative-frequency" rel="nofollow">order inversionrelative frequency</a></li>
</ul>
</li>
</ul>
</div>
</div>


<h1 id="设计hadoop算法">设计Hadoop算法</h1>

<p>key-value pair是Hadoop中是最基本的数据结构。</p>

<p>键值和数值可以是整数、字符串和任意的数据结构。</p>

<p>所以在Hadoop算法设计中，最重要的就是找到key-value pair，因为在一下地方会用到key-value pair。</p>

<p><img src="https://img-blog.csdn.net/20170410110416127?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYzYwMjI3MzA5MQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p>在上面可以看出在map和reduce两个地方都需要使用key-value pair。</p>

<p>Hadoop使用的最核心的原理就是map-reduce，所以可以总结为：</p>

<p>mapper：是一种数据结构，映射输入数据到中间键值。</p>

<p>reducer：针对同一个中间键值的数据做一个整合。</p>

<p>之后的话，需要对数据做一个整合。</p>

<p>所以在整个过程中看到中间键值是透明的。</p>

<p>那么整个map-reducer的网络结构可以写成：</p>

<p><img src="https://img-blog.csdn.net/20170410110848598?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYzYwMjI3MzA5MQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p>整个算法的伪代码可以写成：</p>

<p><img src="https://img-blog.csdn.net/20170410110946903?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYzYwMjI3MzA5MQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>



<h1 id="hadoop的设计原则">Hadoop的设计原则</h1>

<p>整个算法包括了： <br>
1、准备数据 <br>
2、运行mapper和reducer <br>
3、选择、设计combiner和partitioner</p>

<p>Hadoop可以控制什么？</p>

<ul>
<li>数据结构比如：键和值。</li>
<li>用于mapper和reducer的初始化和终止代码</li>
<li>可以在mapper和reducer中保存大量输入和中间键值</li>
<li>可以控制中间键的排序</li>
<li>可以控制键的空间的分区</li>
</ul>

<p>Hadoop不能控制的东西？ <br>
 - mapper和reducer会在哪里运行 <br>
 - 什么时候mapper和reducer会开始或者结束 <br>
 - 什么样的键值对会被对应的mapper和reducer处理。</p>

<p>优化的点： <br>
 1. scalability <br>
 2. resource requirement</p>



<h2 id="local-aggregationcount">local aggregation——count</h2>

<p>为什么需要进行local aggregation？ 在数据很多的分布式计算里，中间键值交换会是代价很大的操作；网络和磁盘的延时很大。</p>

<p>所以使用局部整合，就是在每个节点进行整合，那么就可以减少中间键值的数量以及减少需要进行shuffle的键值对的数量。</p>

<p>设计原则： <br>
1、In-Mapper Combining：在局部聚合发生的时候提供控制； <br>
2、设计可以决定聚合是怎么进行的。</p>

<p>Combiners（min-reducer）：把局部的结果进行一个汇集。使用相关数组去累计中间结果。emit方法在所有的InputRecords被处理之后被调用。</p>

<p>保存状态：在调用map方法的整个过程中保存状态。</p>

<ul>
<li>初始化：创建永久的数据结构</li>
<li>关闭：释放中间键值对，只有所有任务都做完的时候。</li>
</ul>

<p>为了保存状态，那么需要使用：</p>

<blockquote>
  <p>mapreduce.job.jvm.numtasks = -1</p>
</blockquote>

<p>combiner可以写成： <br>
<img src="https://img-blog.csdn.net/20170410113657939?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYzYwMjI3MzA5MQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""> <br>
我们可以发现在每个Mapper里面，做了一个局部聚合的工作。</p>

<p>和原来的进行对比： <br>
<img src="https://img-blog.csdn.net/20170410113452951?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYzYwMjI3MzA5MQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p>采用local aggregation的评价：</p>

<p>1、效率：减少I/O的带宽（减少中间键值对的数量），提高了带宽的使用率；不必要的目标创建和销毁。 <br>
2、瓶颈：in-mapper是依赖于有足够的带宽进行数据处理。很多线程都在竞争使用资源。 <br>
3、precaution：把函数的范式打乱，因为状态保存。状态保存：算法的行为可能会依赖于执行的顺序。</p>

<h2 id="pairs-and-stripesco-occurrence">pairs and stripes——co-occurrence</h2>

<p>mapreduce中的复杂的策略：创建复杂的键值。</p>

<p>两个基本的技术：</p>

<ul>
<li>pairs： key-&gt;value 一对一</li>
<li>stripes：对于一个key有多个值和它对应</li>
</ul>

<p>使用word co-occurrence矩阵。一个nxn的矩阵，<span class="MathJax_Preview"></span><span class="MathJax" id="MathJax-Element-576-Frame" role="textbox" aria-readonly="true"><nobr><span class="math" id="MathJax-Span-4586" style="width: 1.816em; display: inline-block;"><span style="display: inline-block; position: relative; width: 1.443em; height: 0px; font-size: 125%;"><span style="position: absolute; clip: rect(1.763em 1000em 2.829em -0.477em); top: -2.344em; left: 0.003em;"><span class="mrow" id="MathJax-Span-4587"><span class="msubsup" id="MathJax-Span-4588"><span style="display: inline-block; position: relative; width: 1.443em; height: 0px;"><span style="position: absolute; clip: rect(1.976em 1000em 2.723em -0.477em); top: -2.557em; left: 0.003em;"><span class="mi" id="MathJax-Span-4589" style="font-family: MathJax_Math-italic;">m</span><span style="display: inline-block; width: 0px; height: 2.563em;"></span></span><span style="position: absolute; top: -2.291em; left: 0.856em;"><span class="texatom" id="MathJax-Span-4590"><span class="mrow" id="MathJax-Span-4591"><span class="mi" id="MathJax-Span-4592" style="font-size: 70.7%; font-family: MathJax_Math-italic;">i</span><span class="mi" id="MathJax-Span-4593" style="font-size: 70.7%; font-family: MathJax_Math-italic;">j</span></span></span><span style="display: inline-block; width: 0px; height: 2.456em;"></span></span></span></span></span><span style="display: inline-block; width: 0px; height: 2.349em;"></span></span></span><span style="border-left: 0.003em solid; display: inline-block; overflow: hidden; width: 0px; height: 1.07em; vertical-align: -0.463em;"></span></span></nobr></span><script type="math/tex" id="MathJax-Element-576">m_{ij}</script> 表示<span class="MathJax_Preview"></span><span class="MathJax" id="MathJax-Element-577-Frame" role="textbox" aria-readonly="true"><nobr><span class="math" id="MathJax-Span-4594" style="width: 3.149em; display: inline-block;"><span style="display: inline-block; position: relative; width: 2.509em; height: 0px; font-size: 125%;"><span style="position: absolute; clip: rect(1.709em 1000em 2.883em -0.477em); top: -2.557em; left: 0.003em;"><span class="mrow" id="MathJax-Span-4595"><span class="mi" id="MathJax-Span-4596" style="font-family: MathJax_Math-italic;">w</span><span class="mi" id="MathJax-Span-4597" style="font-family: MathJax_Math-italic;">o</span><span class="mi" id="MathJax-Span-4598" style="font-family: MathJax_Math-italic;">r</span><span class="msubsup" id="MathJax-Span-4599"><span style="display: inline-block; position: relative; width: 0.856em; height: 0px;"><span style="position: absolute; clip: rect(1.709em 1000em 2.723em -0.424em); top: -2.557em; left: 0.003em;"><span class="mi" id="MathJax-Span-4600" style="font-family: MathJax_Math-italic;">d<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.003em;"></span></span><span style="display: inline-block; width: 0px; height: 2.563em;"></span></span><span style="position: absolute; top: -2.291em; left: 0.536em;"><span class="mi" id="MathJax-Span-4601" style="font-size: 70.7%; font-family: MathJax_Math-italic;">i</span><span style="display: inline-block; width: 0px; height: 2.456em;"></span></span></span></span></span><span style="display: inline-block; width: 0px; height: 2.563em;"></span></span></span><span style="border-left: 0.003em solid; display: inline-block; overflow: hidden; width: 0px; height: 1.203em; vertical-align: -0.263em;"></span></span></nobr></span><script type="math/tex" id="MathJax-Element-577">word_i</script>和<span class="MathJax_Preview"></span><span class="MathJax" id="MathJax-Element-578-Frame" role="textbox" aria-readonly="true"><nobr><span class="math" id="MathJax-Span-4602" style="width: 3.149em; display: inline-block;"><span style="display: inline-block; position: relative; width: 2.509em; height: 0px; font-size: 125%;"><span style="position: absolute; clip: rect(1.709em 1000em 3.043em -0.477em); top: -2.557em; left: 0.003em;"><span class="mrow" id="MathJax-Span-4603"><span class="mi" id="MathJax-Span-4604" style="font-family: MathJax_Math-italic;">w</span><span class="mi" id="MathJax-Span-4605" style="font-family: MathJax_Math-italic;">o</span><span class="mi" id="MathJax-Span-4606" style="font-family: MathJax_Math-italic;">r</span><span class="msubsup" id="MathJax-Span-4607"><span style="display: inline-block; position: relative; width: 0.856em; height: 0px;"><span style="position: absolute; clip: rect(1.709em 1000em 2.723em -0.424em); top: -2.557em; left: 0.003em;"><span class="mi" id="MathJax-Span-4608" style="font-family: MathJax_Math-italic;">d<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.003em;"></span></span><span style="display: inline-block; width: 0px; height: 2.563em;"></span></span><span style="position: absolute; top: -2.291em; left: 0.536em;"><span class="mi" id="MathJax-Span-4609" style="font-size: 70.7%; font-family: MathJax_Math-italic;">j</span><span style="display: inline-block; width: 0px; height: 2.456em;"></span></span></span></span></span><span style="display: inline-block; width: 0px; height: 2.563em;"></span></span></span><span style="border-left: 0.003em solid; display: inline-block; overflow: hidden; width: 0px; height: 1.403em; vertical-align: -0.463em;"></span></span></nobr></span><script type="math/tex" id="MathJax-Element-578">word_j</script>同时出现在了一个contex里面。</p>

<p>pairs approach：这个方法就是键值不再是一个key，而是联合的键值。所以在这里， <br>
 - mapper：每个键对应的值就是1， 然后计算co-occurrence矩阵。 <br>
 - reducer：计算每个键值对的最终值。</p>

<p>写成伪代码就是： <br>
<img src="https://img-blog.csdn.net/20170410115607683?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYzYwMjI3MzA5MQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p>stripes是键值是一系列的东西组成的，然后在这里mapper的时候是采用了co-occurrence矩阵，再根据其中的associate array计算其中的关系。</p>

<p>reducer接受同一个词的所有associate，然后对同一个key进行叠加。输出的key就是associate的形式。</p>

<p><img src="https://img-blog.csdn.net/20170410135804445?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYzYwMjI3MzA5MQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p>我们对于pairs和stripes进行对比，我们可以发现： <br>
pair会产生大量的中间键，这是内存的一种浪费，combiner的结合是一种限制，不会受到内存分页的影响。</p>

<p>stripes的方法：会产生更少的中间键，从combiner中获益，因为combiner是字典的大小。</p>

<p>数值更加复杂，线性化和去线性化带来的代价。</p>

<p>会有不少的内存分页问题。</p>

<h2 id="order-inversionrelative-frequency">order inversion——relative frequency</h2>

<p>计算原来的map-reduce的co-occurrence的频率。</p>

<p><img src="https://img-blog.csdn.net/20170410140313813?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYzYwMjI3MzA5MQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p>如果是计算stripe key-value的频率，就根据associate array进行计算。</p>

<p>如果是计算pair key-value的频率的话，就根据（<span class="MathJax_Preview"></span><span class="MathJax" id="MathJax-Element-589-Frame" role="textbox" aria-readonly="true"><nobr><span class="math" id="MathJax-Span-4691" style="width: 3.096em; display: inline-block;"><span style="display: inline-block; position: relative; width: 2.456em; height: 0px; font-size: 125%;"><span style="position: absolute; clip: rect(2.136em 1000em 3.203em -0.477em); top: -2.717em; left: 0.003em;"><span class="mrow" id="MathJax-Span-4692"><span class="msubsup" id="MathJax-Span-4693"><span style="display: inline-block; position: relative; width: 1.016em; height: 0px;"><span style="position: absolute; clip: rect(1.976em 1000em 2.723em -0.477em); top: -2.557em; left: 0.003em;"><span class="mi" id="MathJax-Span-4694" style="font-family: MathJax_Math-italic;">w</span><span style="display: inline-block; width: 0px; height: 2.563em;"></span></span><span style="position: absolute; top: -2.291em; left: 0.696em;"><span class="mi" id="MathJax-Span-4695" style="font-size: 70.7%; font-family: MathJax_Math-italic;">i</span><span style="display: inline-block; width: 0px; height: 2.456em;"></span></span></span></span><span class="mo" id="MathJax-Span-4696" style="font-family: MathJax_Main;">,</span><span class="msubsup" id="MathJax-Span-4697" style="padding-left: 0.163em;"><span style="display: inline-block; position: relative; width: 1.016em; height: 0px;"><span style="position: absolute; clip: rect(1.976em 1000em 2.723em -0.477em); top: -2.557em; left: 0.003em;"><span class="mi" id="MathJax-Span-4698" style="font-family: MathJax_Math-italic;">w</span><span style="display: inline-block; width: 0px; height: 2.563em;"></span></span><span style="position: absolute; top: -2.291em; left: 0.696em;"><span class="mi" id="MathJax-Span-4699" style="font-size: 70.7%; font-family: MathJax_Math-italic;">j</span><span style="display: inline-block; width: 0px; height: 2.456em;"></span></span></span></span></span><span style="display: inline-block; width: 0px; height: 2.723em;"></span></span></span><span style="border-left: 0.003em solid; display: inline-block; overflow: hidden; width: 0px; height: 1.07em; vertical-align: -0.463em;"></span></span></nobr></span><script type="math/tex" id="MathJax-Element-589">w_i, w_j</script>）计算。</p>

<p>以上翻译来自于18-645课程Module 4.2的课件。</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>