---
layout:     post
title:      Spark Streaming 入门
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/liuge36/article/details/78880079				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-light">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2 id="概述">概述</h2>

<p>什么是 Spark Streaming? <br>
<em>Spark Streaming is an extension of the core Spark API that enables scalable, high-throughput, fault-tolerant stream processing of live data streams.</em>  <br>
根据官网的解释,Spark Streaming是一个基于Spark Core的一个高扩展,高吞吐量,容错的一个处理实时流数据的 工具(流处理).</p>

<p><strong>数据的流处理</strong> <br>
<em>Data can be ingested from many sources like Kafka, Flume, Kinesis, or TCP sockets, and can be processed using complex algorithms expressed with high-level functions like map, reduce, join and window. Finally, processed data can be pushed out to filesystems, databases, and live dashboards.</em> </p>

<p>官网说明中,可以发现,数据可以是Kafka,Flume等数据源头,经过相应的转化处理,可以将处理的结果push到外部文件系统,数据库等</p>

<p><strong>处理流程图</strong></p>

<p><img src="https://img-blog.csdn.net/20171223151503265?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGl1Z2UzNg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="处理流程图" title=""></p>

<p><strong>一句话总结:将不同的数据源经过Spark Streaming处理,接着将处理的结果输出到外部文件系统</strong></p>

<blockquote>
  <p>其中,Spark Streaming拥有下面几个特性:</p>
</blockquote>

<ol>
<li>高度容错(能够从错误中快速恢复过来)</li>
<li>能够运行在成百上千的机器节点上</li>
<li>具有低延时</li>
<li>可以结合机器学习,图计算等同层框架做相应处理</li>
</ol>

<p>上面的第四点,想强调一下,这个特性很重要的,这样我们就可以结合其余的子框架来做更复杂的处理.这点也体现了 <br>
One Stack to rule them all. <br>
“一栈式解决不同场景”</p>

<p><strong>工作原理</strong> <br>
<em>Internally, it works as follows. Spark Streaming receives live input data streams and divides the data into batches, which are then processed by the Spark engine to generate the final stream of results in batches.</em></p>

<p>官网说明中,可以发现,Spark Streaming会去接收实时输入的数据流,将接收的数据流按照要求分成一些小批次数据,将拆分的每一个小批次数据交给Spark Engine去处理,最终,也会得到一些小批次的处理结果</p>

<p>如图所示: <br>
<img src="https://img-blog.csdn.net/20171223152728337?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGl1Z2UzNg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="工作原理" title=""></p>

<p>Spark Streaming应用场景 <br>
简单举个大家熟悉的例子: <br>
Spark Streaming可以用来做电商网站的实时推荐商品系统 <br>
,想想,现在,当你在购物网站上搜索浏览了某个数据,那么,马上它就会为你实时推荐相应的商品,供你选择,这样,可以加大网站的盈利</p>

<p><strong>再次理解Spark Streaming的工作原理</strong></p>

<blockquote>
  <p>工作原理(粗粒度)</p>
</blockquote>

<p>Spark Streaming接收到实时数据流,将数据按照指定的时间切成一些小的数据块,然后将一系列的小的数据库传给Spark Engine去处理.最后,将处理的结果,push到外部文件系统数据库等.</p>

<blockquote>
  <p>工作原理(细粒度)</p>
</blockquote>

<p>先理解一下有一个叫做Context上下文的概念; <br>
Spark SQL中有: <br>
SQL Context/HiveContext <br>
Spark Core中有: <br>
SparkContext <br>
Spark Streaming中有: <br>
Streaming Context等概念</p>

<p>Receiver:Driver端启动的,用于接收数据</p>

<p>一个Spark应用程序中存在SparkContext和SparkContext</p>

<p><img src="https://img-blog.csdn.net/20171223161744453?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGl1Z2UzNg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""> <br>
<em>[上图,来自慕课网Michael__PK老师]</em></p>

<blockquote>
  <p>处理步骤:</p>
</blockquote>

<ol>
<li>Driver会在Executor端中启动一些Receiver的接收器,</li>
<li>接收器启动之后,会把Input data Stream拆分为一些blocks存放在内存里面(多副本的话,会把blocks拷贝到相应的Executor中去),</li>
<li>之后,我们的Receiver会把我们blocks块信息告诉StreamingContext</li>
<li>,每个周期到了,我们的StreamingContex会通知SparkContext启动一些jobs,</li>
<li>SparkContext会把job分发到Executor上执行</li>
</ol>

<p>好的,到这里,Spark Streaming的基本介绍就完成了,接下来我们队核心概念进行理解</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>