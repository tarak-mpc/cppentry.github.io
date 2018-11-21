---
layout:     post
title:      Apache Spark 之 入门笔记
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2 id="spark-是什么-草稿-待整理">Spark 是什么 ## [草稿 - 待整理]</h2>

<p><em>Spark is a java virtual machine(JVM) based distributed data processing engine that scales. And  it is fast compare to other data processing framework</em> <br>
Spark的内核是一套通用的执行引擎。Spark平台基于该引擎的其他功能包括： <br>
<em>Spark Core is the general execution engine for the Spark platform that other functionality is built atop</em> <br>
<a href="https://people.eecs.berkeley.edu/~alig/papers/mesos.pdf" rel="nofollow">Mesos文献</a></p>

<ul>
<li>高速内存计算能力 <br>
<em>- in-memory computing capabilities deliver speed</em></li>
<li>广泛的用例支持 <br>
<em>- general execution model supports wide variety of use cases</em></li>
<li>易于开发 <br>
<em>- ease of development – native APIs in Java, Scala, Python (+ SQL, Clojure, R)</em></li>
</ul>

<table>
<thead>
<tr>
  <th>Situation</th>
  <th>Trade-off</th>
  <th>Tech</th>
</tr>
</thead>
<tbody><tr>
  <td>Cheap Memory</td>
  <td>Recompute</td>
  <td>RDD</td>
</tr>
<tr>
  <td>Cheap Storage</td>
  <td>Replicate</td>
  <td>DFS</td>
</tr>
<tr>
  <td>Cheap Network</td>
  <td>Reference</td>
  <td>URI</td>
</tr>
</tbody></table>


<p><em>RDD: Resilient Distributed Dataset</em> <br>
<em>DFS:Distributed File System</em></p>

<h2 id="spark-优势">Spark 优势</h2>

<ul>
<li>比Hadoop MapReduce快100倍</li>
<li>利用了工作节点的主要内存进行处理</li>
<li>防止了磁盘不必要的I/O运行</li>
<li>在应用程序层串联任务的能力</li>
<li>最小化磁盘书写数量</li>
</ul>



<h2 id="spark-效率">Spark 效率</h2>

<ul>
<li>使用DAG(Directed Acyclic Graph) 有向无环图</li>
</ul>

<hr>



<h2 id="spark-安装">Spark 安装</h2>

<p><em>Scala, Python, R –&gt; REPL</em></p>



<h2 id="title">—————————————————————————–</h2>



<h2 id="spark-集成">Spark 集成</h2>

<p><img src="https://img-blog.csdn.net/20170222154249387?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaW5kaWdvMDIx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""> <br>
 - Databricks Cloud <br>
 -<img src="https://img-blog.csdn.net/20170222154327788?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaW5kaWdvMDIx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""> <br>
 - Spark + Hadoop + Hbase + etc. <br>
 <img src="https://img-blog.csdn.net/20170222154412898?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaW5kaWdvMDIx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""> <br>
 - Spark + PyData  <br>
 - Kafka + Spark + Cassandra <br>
 - Spark + ElasticSearch <br>
 - Spark + Play <br>
 - Spark +Mesos <br>
 - </p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>