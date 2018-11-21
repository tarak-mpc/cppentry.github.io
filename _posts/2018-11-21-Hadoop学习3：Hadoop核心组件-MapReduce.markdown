---
layout:     post
title:      Hadoop学习3：Hadoop核心组件-MapReduce
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，欢迎转载，转载请注明出处！					https://blog.csdn.net/qq_36743482/article/details/78319173				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p><strong><em>HDFS用于解决大数据的存储，而MapReduce用于解决大数据的分析计算。</em></strong> <br>
MapReduce：Hadoop分布式计算框架的一种，适合于离线计算。 <br>
Storm：流式计算框架，适合实时计算。 <br>
Spark：内存计算框架，适合快速得到结果。</p>

<h3 id="mapreduce设计理念">Mapreduce设计理念</h3>

<ul>
<li>移动计算，而不移动数据 <br>
计算框架MR <br>
<img src="https://img-blog.csdn.net/20171023152155136?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMzY3NDM0ODI=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></li>
<li>四个步骤： <br>
1  ：split(切成块)：对HDFS上的数据处理成一个一个的碎片快，每一个片段有一个map线程并发执行；</li>
</ul>



<pre class="prettyprint"><code class=" hljs livecodeserver"><span class="hljs-built_in">split</span>计算公式：
<span class="hljs-built_in">max</span>.<span class="hljs-built_in">split</span>、<span class="hljs-built_in">min</span>.<span class="hljs-built_in">split</span>、block
<span class="hljs-built_in">split</span> = <span class="hljs-built_in">max</span>(<span class="hljs-built_in">min</span>.<span class="hljs-built_in">split</span> , <span class="hljs-built_in">min</span>( block , <span class="hljs-built_in">max</span>.<span class="hljs-built_in">split</span>))
所以一般是Block的大小，即<span class="hljs-number">128</span>M</code></pre>

<p>2  ：map(Java线程，输出和输入必须键值对)，map的执行由程序员控制； <br>
3  ：shuffler(合并和排序，相同的key合并，并处理成集合)； <br>
4  ：reduce(reduce有可能只有一个，有程序决定)</p>

<h3 id="shuffler">Shuffler</h3>

<p>shuffler处于map和reduce之间的一个步骤，大部分工作由MapReduce框架自己完成，程序员只可以控制一小部分。 <br>
 - 可以把map的输出按照某种key值重新切分和组合成n份，把key值符合某种范围的输出送到特定的reduce去处理； <br>
 - 可以简化reduce的过程。 <br>
<img src="https://img-blog.csdn.net/20171023154429381?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMzY3NDM0ODI=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p><strong><em>在map的机器中操作</em></strong> <br>
1 . map得到的结果放在内存中，当到达一定值就会溢写；每个maptask对应一个内存缓冲区，默认100M，存储map的输出结果。80%时就会溢写，留有20%用于继续处理map的输出结果。 <br>
当缓冲区快满的时候，将缓冲区的数据以一个临时文件的方式存放在磁盘。 <br>
溢写有单独线程完成，不影响往缓冲区写map结果的线程。（spill.percent，默认80%）。 <br>
<img src="https://img-blog.csdn.net/20171023161705998?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMzY3NDM0ODI=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""> <br>
2 . <strong>partition</strong>，分区。同一分区的数据会被放在同一个reduce中处理，用于解决<strong>负载均衡</strong>和<strong>数据倾斜</strong>问题。<strong>可以自定义</strong>，也有默认分区规则（对key取hash值，然后对reduce的个数进行模运算 hashVal%reduceNum）。 <br>
3 . <strong>sort</strong>：默认排序方式-ASCII码值排序，即字典排序。（10排在9的前面），<strong>可自定义</strong>。 <br>
4 . spill to disk：溢写到磁盘。 <br>
5 . merge on disk：把溢写的文件按照hash值（及key值相同）合并：<strong>combiner</strong>，目的：提高下一步网络数据copy的效率，<strong>可以自定义</strong>。</p>

<p><strong><em>进入reduce的机器中操作</em></strong> <br>
6 . 根据分区把各个map数据copy到reduce中。 <br>
7 . 将得到的数据merge合并，key相同合并。 <br>
8 . 把执行的结果合并。</p>

<hr>

<p>*注：map不可能产生数据倾斜，只有reduce会产生。 <br>
原因：map的数据来源split，而split是按照一定规则的，同时split的数据来源于block，因而，输入map中的数据是大小基本相同的。reduce的数据来源于shuffle的，取决于partition，好的partition可以避免数据倾斜*。</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>