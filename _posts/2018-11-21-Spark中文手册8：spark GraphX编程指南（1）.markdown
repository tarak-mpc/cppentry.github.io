---
layout:     post
title:      Spark中文手册8：spark GraphX编程指南（1）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<span style="word-wrap:break-word; font-weight:700; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px"><span style="color:#ff0000; word-wrap:break-word">1.什么是GraphX？<br style="word-wrap:break-word">
2.如何将Spark和GraphX引入到项目中？<br style="word-wrap:break-word">
3.从一个图中取出顶点特征加入到另外一个图中如何实现？<br style="word-wrap:break-word">
</span></span><br style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<br style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<br style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<img id="aimg_lDZbm" class="zoom" border="0" alt="" src="http://www.aboutyun.com/static/image/hrline/4.gif" width="500" height="35" style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px"><br style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<br style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<br style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<br style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<a target="_blank" href="http://www.aboutyun.com/thread-11413-1-1.html" rel="nofollow" style="word-wrap:break-word; color:rgb(51,102,153); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">Spark中文手册-编程指南</a><br style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<a target="_blank" href="http://www.aboutyun.com/thread-11484-1-1.html" rel="nofollow" style="word-wrap:break-word; color:rgb(51,102,153); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">Spark之一个快速的例子</a><a target="_blank" href="http://www.aboutyun.com/thread-11502-1-1.html" rel="nofollow" style="word-wrap:break-word; color:rgb(51,102,153); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">Spark之基本概念</a><br style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<a target="_blank" href="http://www.aboutyun.com/thread-11502-1-1.html" rel="nofollow" style="word-wrap:break-word; color:rgb(51,102,153); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">Spark之基本概念</a><br style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<a target="_blank" href="http://www.aboutyun.com/thread-11516-1-1.html" rel="nofollow" style="word-wrap:break-word; color:rgb(51,102,153); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">Spark之基本概念（2）</a><br style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<a target="_blank" href="http://www.aboutyun.com/thread-11535-1-1.html" rel="nofollow" style="word-wrap:break-word; color:rgb(51,102,153); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">Spark之基本概念（3）</a><br style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<a target="_blank" href="http://www.aboutyun.com/thread-11562-1-1.html" rel="nofollow" style="word-wrap:break-word; color:rgb(51,102,153); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">Spark-sql由入门到精通</a><br style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<a target="_blank" href="http://www.aboutyun.com/thread-11575-1-1.html" rel="nofollow" style="word-wrap:break-word; color:rgb(51,102,153); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">Spark-sql由入门到精通续</a><br style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<br style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<span style="font-size:24px; word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun"><span style="word-wrap:break-word; font-weight:700">GraphX编程指南</span></span><br style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<br style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<br style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<div align="left" style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
GraphX是一个新的（alpha）Spark API，它用于图和并行图(graph-parallel)的计算。GraphX通过引入<a target="_blank" href="http://endymecy.gitbooks.io/spark-programming-guide-zh-cn/content/graphx-programming-guide/property-graph.html" rel="nofollow" style="word-wrap:break-word; color:rgb(51,102,153)">Resilient
 Distributed Property Graph</a>：带有 顶点和边属性的有向多重图，来扩展Spark RDD。为了支持图计算，GraphX公开一组基本的功能操作以及Pregel API的一个优化。另外，GraphX包含了一个日益增长的图算法和图builders的 集合，用以简化图分析任务。</div>
<div align="left" style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
从社交网络到语言建模，不断增长的规模和图形数据的重要性已经推动了许多新的<span style="font-family:Consolas,Liberation Mono,Menlo,Courier,monospace; word-wrap:break-word">graph-parallel</span>系统（如<a target="_blank" href="http://giraph.apache.org/" rel="nofollow" style="word-wrap:break-word; color:rgb(51,102,153)">Giraph</a>和<a target="_blank" href="http://graphlab.org/" rel="nofollow" style="word-wrap:break-word; color:rgb(51,102,153)">GraphLab</a>）的发展。
 通过限制可表达的计算类型和引入新的技术来划分和分配图，这些系统可以高效地执行复杂的图形算法，比一般的<span style="font-family:Consolas,Liberation Mono,Menlo,Courier,monospace; word-wrap:break-word">data-parallel</span>系统快很多。</div>
<br style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<br style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<div align="left" style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<img id="aimg_12153" src="http://www.aboutyun.com/data/attachment/forum/201502/08/221306yrcah3cmhe6m4ywz.png" class="zoom" width="600" alt="" style="word-wrap:break-word"></div>
<br style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<br style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<div align="left" style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
然而，通过这种限制可以提高性能，但是很难表示典型的图分析途径（构造图、修改它的结构或者表示跨多个图的计算）中很多重要的stages。另外，我们如何看待数据取决于我们的目标，并且同一原始数据可能有许多不同表和图的视图。</div>
<br style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<br style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<div align="left" style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<img id="aimg_12154" src="http://www.aboutyun.com/data/attachment/forum/201502/08/221309zi9t24nno9zce9nr.png" class="zoom" width="600" alt="" style="word-wrap:break-word"></div>
<br style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<br style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<div align="left" style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
结论是，图和表之间经常需要能够相互移动。然而，现有的图分析管道必须组成<span style="font-family:Consolas,Liberation Mono,Menlo,Courier,monospace; word-wrap:break-word">graph-parallel</span>和<span style="font-family:Consolas,Liberation Mono,Menlo,Courier,monospace; word-wrap:break-word">data-
 parallel</span>系统`，从而实现大数据的迁移和复制并生成一个复杂的编程模型。</div>
<div align="left" style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<img id="aimg_12155" src="http://www.aboutyun.com/data/attachment/forum/201502/08/221317t40j4quqqrw8j38q.png" class="zoom" width="600" alt="" style="word-wrap:break-word"></div>
<div align="left" style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
GraphX项目的目的就是将<span style="font-family:Consolas,Liberation Mono,Menlo,Courier,monospace; word-wrap:break-word">graph-parallel</span>和<span style="font-family:Consolas,Liberation Mono,Menlo,Courier,monospace; word-wrap:break-word">data-parallel</span>统一到一个系统中，这个系统拥有一个唯一的组合API。GraphX允许用户将数据当做一个图和一个集合（RDD），而不需要数据移动或者复制。通过将最新的进展整合进<span style="font-family:Consolas,Liberation Mono,Menlo,Courier,monospace; word-wrap:break-word">graph-parallel</span>系统，GraphX能够优化图操作的执行。</div>
<ul style="word-wrap:break-word; margin:0px 0px 0px 14px; padding:0px; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px; list-style:disc">
开始</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px; list-style:disc">
属性图</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px; list-style:disc">
图操作符</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px; list-style:disc">
Pregel API</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px; list-style:disc">
图构造者</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px; list-style:disc">
顶点和边RDDs</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px; list-style:disc">
图算法</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px; list-style:disc">
例子<br style="word-wrap:break-word">
</li></ul>
<br style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<br style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<br style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<span style="font-size:24px; word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun"><span style="word-wrap:break-word; font-weight:700">开始</span></span><br style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<br style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<span style="color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">开始的第一步是引入Spark和GraphX到你的项目中，如下面所示</span><br style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<div class="blockcode" style="word-wrap:break-word; overflow:hidden; margin:10px 0px; padding:10px 0px 5px 10px; background-color:rgb(247,247,247); color:rgb(102,102,102); zoom:1; border:1px solid rgb(204,204,204); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<div id="code_hN5" style="word-wrap:break-word">
<ol style="word-wrap:break-word; margin:0px 0px 0px 10px!important; padding:0px!important">
<li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
mport org.apache.spark._<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
import org.apache.spark.graphx._<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
// To make some of the examples work we will also need RDD<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
import org.apache.spark.rdd.RDD</li></ol>
</div>
<span style="word-wrap:break-word; margin-left:43px; font-size:12px; color:rgb(51,102,153)!important">复制代码</span></div>
<br style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<br style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<span style="color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">如果你没有用到Spark shell，你还将需要SparkContext。</span><br style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<br style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<span style="font-size:24px; word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun"><span style="word-wrap:break-word; font-weight:700">属性图</span></span><br style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<br style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<br style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<div align="left" style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<a target="_blank" href="https://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.graphx.Graph" rel="nofollow" style="word-wrap:break-word; color:rgb(51,102,153)">属性图</a>是一个有向多重图，它带有连接到每个顶点和边的用户定义的对象。 有向多重图中多个并行(parallel)的边共享相同的源和目的地顶点。支持并行边的能力简化了建模场景，这个场景中，相同的顶点存在多种关系(例如co-worker和friend)。每个顶点由一个
 唯一的64位长的标识符（VertexID）作为key。GraphX并没有对顶点标识强加任何排序。同样，顶点拥有相应的源和目的顶点标识符。</div>
<div align="left" style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
属性图通过vertex(VD)和edge(ED)类型参数化，这些类型是分别与每个顶点和边相关联的对象的类型。</div>
<div align="left" style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
在某些情况下，在相同的图形中，可能希望顶点拥有不同的属性类型。这可以通过继承完成。例如，将用户和产品建模成一个二分图，我们可以用如下方式</div>
<div class="blockcode" style="word-wrap:break-word; overflow:hidden; margin:10px 0px; padding:10px 0px 5px 10px; background-color:rgb(247,247,247); color:rgb(102,102,102); zoom:1; border:1px solid rgb(204,204,204); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<div id="code_AG2" style="word-wrap:break-word">
<ol style="word-wrap:break-word; margin:0px 0px 0px 10px!important; padding:0px!important">
<li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
class VertexProperty()<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
case class UserProperty(val name: String) extends VertexProperty<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
case class ProductProperty(val name: String, val price: Double) extends VertexProperty<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
// The graph might then have the type:<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
var graph: Graph[VertexProperty, String] = null</li></ol>
</div>
<span style="word-wrap:break-word; margin-left:43px; font-size:12px; color:rgb(51,102,153)!important">复制代码</span></div>
<br style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<div align="left" style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
和RDD一样，属性图是不可变的、分布式的、容错的。图的值或者结构的改变需要按期望的生成一个新的图来实现。注意，原始图的大部分都可以在新图中重用，用来减少这种固有的功能数据结构的成本。 执行者使用一系列顶点分区试探法来对图进行分区。如RDD一样，图中的每个分区可以在发生故障的情况下被重新创建在不同的机器上。</div>
<div align="left" style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
逻辑上的属性图对应于一对类型化的集合(RDD),这个集合编码了每一个顶点和边的属性。因此，图类包含访问图中顶点和边的成员.</div>
<br style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<div class="blockcode" style="word-wrap:break-word; overflow:hidden; margin:10px 0px; padding:10px 0px 5px 10px; background-color:rgb(247,247,247); color:rgb(102,102,102); zoom:1; border:1px solid rgb(204,204,204); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<div id="code_Nj6" style="word-wrap:break-word">
<ol style="word-wrap:break-word; margin:0px 0px 0px 10px!important; padding:0px!important">
<li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
class Graph[VD, ED] {<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em; background-color:rgb(255,255,255); color:rgb(51,102,153)">
  val vertices: VertexRDD[VD]<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
  val edges: EdgeRDD[ED]<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
}</li></ol>
</div>
<span style="word-wrap:break-word; margin-left:43px; font-size:12px; color:rgb(51,102,153)!important">复制代码</span></div>
<br style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<div align="left" style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
VertexRDD[VD]和EdgeRDD[ED]类分别继承和优化自RDD[(VertexID, VD)]和RDD[Edge[ED]]。VertexRDD[VD]和EdgeRDD[ED]都支持额外的功能来建立在图计算和利用内部优化。</div>
<span style="word-wrap:break-word; font-weight:700; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">属性图的例子</span>
<div align="left" style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
在GraphX项目中，假设我们想构造一个包括不同合作者的属性图。顶点属性可能包含用户名和职业。我们可以用描述合作者之间关系的字符串标注边缘。</div>
<div align="left" style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<img id="aimg_12156" src="http://www.aboutyun.com/data/attachment/forum/201502/08/221544r14slxxmlxbs9lw8.png" class="zoom" width="600" alt="" style="word-wrap:break-word"></div>
<div align="left" style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
所得的图形将具有类型签名</div>
<div class="blockcode" style="word-wrap:break-word; overflow:hidden; margin:10px 0px; padding:10px 0px 5px 10px; background-color:rgb(247,247,247); color:rgb(102,102,102); zoom:1; border:1px solid rgb(204,204,204); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<div id="code_DgY" style="word-wrap:break-word">
<ol style="word-wrap:break-word; margin:0px 0px 0px 10px!important; padding:0px!important">
<li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
val userGraph: Graph[(String, String), String]</li></ol>
</div>
<span style="word-wrap:break-word; margin-left:43px; font-size:12px; color:rgb(51,102,153)!important">复制代码</span></div>
<br style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<br style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<div align="left" style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
有很多方式从一个原始文件、RDD构造一个属性图。最一般的方法是利用<a target="_blank" href="https://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.graphx.Graph%24" rel="nofollow" style="word-wrap:break-word; color:rgb(51,102,153)">Graph object</a>。 下面的代码从RDD集合生成属性图。</div>
<div class="blockcode" style="word-wrap:break-word; overflow:hidden; margin:10px 0px; padding:10px 0px 5px 10px; background-color:rgb(247,247,247); color:rgb(102,102,102); zoom:1; border:1px solid rgb(204,204,204); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<div id="code_GpP" style="word-wrap:break-word">
<ol style="word-wrap:break-word; margin:0px 0px 0px 10px!important; padding:0px!important">
<li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
// Assume the SparkContext has already been constructed<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
val sc: SparkContext<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
// Create an RDD for the vertices<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
val users: RDD[(VertexId, (String, String))] =<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em; background-color:rgb(255,255,255); color:rgb(51,102,153)">
  sc.parallelize(Array((3L, ("rxin", "student")), (7L, ("jgonzal", "postdoc")),<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
                       (5L, ("franklin", "prof")), (2L, ("istoica", "prof"))))<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
// Create an RDD for edges<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
val relationships: RDD[Edge[String]] =<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
  sc.parallelize(Array(Edge(3L, 7L, "collab"),    Edge(5L, 3L, "advisor"),<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
                       Edge(2L, 5L, "colleague"), Edge(5L, 7L, "pi")))<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
// Define a default user in case there are relationship with missing user<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
val defaultUser = ("John Doe", "Missing")<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
// Build the initial Graph<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
val graph = Graph(users, relationships, defaultUser)</li></ol>
</div>
<span style="word-wrap:break-word; margin-left:43px; font-size:12px; color:rgb(51,102,153)!important">复制代码</span></div>
<br style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<br style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<div align="left" style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
在上面的例子中，我们用到了<a target="_blank" href="https://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.graphx.Edge" rel="nofollow" style="word-wrap:break-word; color:rgb(51,102,153)">Edge</a>样本类。边有一个srcId和dstId分别对应 于源和目标顶点的标示符。另外，Edge类有一个attr成员用来存储边属性。</div>
<div align="left" style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
我们可以分别用graph.vertices和graph.edges成员将一个图解构为相应的顶点和边。</div>
<div class="blockcode" style="word-wrap:break-word; overflow:hidden; margin:10px 0px; padding:10px 0px 5px 10px; background-color:rgb(247,247,247); color:rgb(102,102,102); zoom:1; border:1px solid rgb(204,204,204); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<div id="code_w0I" style="word-wrap:break-word">
<ol style="word-wrap:break-word; margin:0px 0px 0px 10px!important; padding:0px!important">
<li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
val graph: Graph[(String, String), String] // Constructed from above<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
// Count all users which are postdocs<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
graph.vertices.filter { case (id, (name, pos)) =&gt; pos == "postdoc" }.count<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
// Count all the edges where src &gt; dst<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
graph.edges.filter(e =&gt; e.srcId &gt; e.dstId).count</li></ol>
</div>
<span style="word-wrap:break-word; margin-left:43px; font-size:12px; color:rgb(51,102,153)!important">复制代码</span></div>
<br style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<div align="left" style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
注意，graph.vertices返回一个VertexRDD[(String, String)]，它继承于 RDD[(VertexID, (String, String))]。所以我们可以用scala的case表达式解构这个元组。另一方面，</div>
<div align="left" style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
graph.edges返回一个包含Edge[String]对象的EdgeRDD。我们也可以用到case类的类型构造器，如下例所示。</div>
<div class="blockcode" style="word-wrap:break-word; overflow:hidden; margin:10px 0px; padding:10px 0px 5px 10px; background-color:rgb(247,247,247); color:rgb(102,102,102); zoom:1; border:1px solid rgb(204,204,204); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<div id="code_NYg" style="word-wrap:break-word">
<ol style="word-wrap:break-word; margin:0px 0px 0px 10px!important; padding:0px!important">
<li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
graph.edges.filter { case Edge(src, dst, prop) =&gt; src &gt; dst }.count</li></ol>
</div>
<span style="word-wrap:break-word; margin-left:43px; font-size:12px; color:rgb(51,102,153)!important">复制代码</span></div>
<br style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<div align="left" style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
除了属性图的顶点和边视图，GraphX也包含了一个三元组视图，三元视图逻辑上将顶点和边的属性保存为一个RDD[EdgeTriplet[VD, ED]]，它包含EdgeTriplet类的实例。 可以通过下面的Sql表达式表示这个连接。</div>
<div class="blockcode" style="word-wrap:break-word; overflow:hidden; margin:10px 0px; padding:10px 0px 5px 10px; background-color:rgb(247,247,247); color:rgb(102,102,102); zoom:1; border:1px solid rgb(204,204,204); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<div id="code_W67" style="word-wrap:break-word">
<ol style="word-wrap:break-word; margin:0px 0px 0px 10px!important; padding:0px!important">
<li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
SELECT src.id, dst.id, src.attr, e.attr, dst.attr<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
FROM edges AS e LEFT JOIN vertices AS src, vertices AS dst<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
ON e.srcId = src.Id AND e.dstId = dst.Id</li></ol>
</div>
<span style="word-wrap:break-word; margin-left:43px; font-size:12px; color:rgb(51,102,153)!important">复制代码</span></div>
<br style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<br style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<div align="left" style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
除了属性图的顶点和边视图，GraphX也包含了一个三元组视图，三元视图逻辑上将顶点和边的属性保存为一个RDD[EdgeTriplet[VD, ED]]，它包含<a target="_blank" href="https://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.graphx.EdgeTriplet" rel="nofollow" style="word-wrap:break-word; color:rgb(51,102,153)">EdgeTriplet</a>类的实例。
 可以通过下面的Sql表达式表示这个连接。</div>
<div class="blockcode" style="word-wrap:break-word; overflow:hidden; margin:10px 0px; padding:10px 0px 5px 10px; background-color:rgb(247,247,247); color:rgb(102,102,102); zoom:1; border:1px solid rgb(204,204,204); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<div id="code_S3B" style="word-wrap:break-word">
<ol style="word-wrap:break-word; margin:0px 0px 0px 10px!important; padding:0px!important">
<li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
SELECT src.id, dst.id, src.attr, e.attr, dst.attr<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
FROM edges AS e LEFT JOIN vertices AS src, vertices AS dst<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
ON e.srcId = src.Id AND e.dstId = dst.Id</li></ol>
</div>
<span style="word-wrap:break-word; margin-left:43px; font-size:12px; color:rgb(51,102,153)!important">复制代码</span></div>
<br style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<br style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<div align="left" style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
或者通过下面的图来表示。</div>
<div align="left" style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<img id="aimg_12157" src="http://www.aboutyun.com/data/attachment/forum/201502/08/221545q82f8b6o5o6ea5b4.png" class="zoom" width="600" alt="" style="word-wrap:break-word"></div>
<div align="left" style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
EdgeTriplet类继承于Edge类，并且加入了srcAttr和dstAttr成员，这两个成员分别包含源和目的的属性。我们可以用一个三元组视图渲染字符串集合用来描述用户之间的关系。</div>
<div class="blockcode" style="word-wrap:break-word; overflow:hidden; margin:10px 0px; padding:10px 0px 5px 10px; background-color:rgb(247,247,247); color:rgb(102,102,102); zoom:1; border:1px solid rgb(204,204,204); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<div id="code_k0L" style="word-wrap:break-word">
<ol style="word-wrap:break-word; margin:0px 0px 0px 10px!important; padding:0px!important">
<li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
val graph: Graph[(String, String), String] // Constructed from above<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
// Use the triplets view to create an RDD of facts.<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
val facts: RDD[String] =<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
  graph.triplets.map(triplet =&gt;<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
    triplet.srcAttr._1 + " is the " + triplet.attr + " of " + triplet.dstAttr._1)<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
facts.collect.foreach(println(_))</li></ol>
</div>
<span style="word-wrap:break-word; margin-left:43px; font-size:12px; color:rgb(51,102,153)!important">复制代码</span></div>
<br style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<div align="left" style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<span style="font-size:24px; word-wrap:break-word"><span style="word-wrap:break-word; font-weight:700">图操作符</span></span></div>
<br style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<div align="left" style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
正如RDDs有基本的操作map, filter和reduceByKey一样，属性图也有基本的集合操作，这些操作采用用户自定义的函数并产生包含转换特征和结构的新图。定义在<a target="_blank" href="http://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.graphx.Graph" rel="nofollow" style="word-wrap:break-word; color:rgb(51,102,153)">Graph</a>中的
 核心操作是经过优化的实现。表示为核心操作的组合的便捷操作定义在<a target="_blank" href="http://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.graphx.GraphOps" rel="nofollow" style="word-wrap:break-word; color:rgb(51,102,153)">GraphOps</a>中。然而， 因为有Scala的隐式转换，定义在GraphOps中的操作可以作为Graph的成员自动使用。例如，我们可以通过下面的方式计算每个顶点(定义在GraphOps中)的入度。</div>
<div class="blockcode" style="word-wrap:break-word; overflow:hidden; margin:10px 0px; padding:10px 0px 5px 10px; background-color:rgb(247,247,247); color:rgb(102,102,102); zoom:1; border:1px solid rgb(204,204,204); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<div id="code_w2c" style="word-wrap:break-word">
<ol style="word-wrap:break-word; margin:0px 0px 0px 10px!important; padding:0px!important">
<li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
val graph: Graph[(String, String), String]<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
// Use the implicit GraphOps.inDegrees operator<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
val inDegrees: VertexRDD[Int] = graph.inDegrees</li></ol>
</div>
<span style="word-wrap:break-word; margin-left:43px; font-size:12px; color:rgb(51,102,153)!important">复制代码</span></div>
<br style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<div align="left" style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
区分核心图操作和GraphOps的原因是为了在将来支持不同的图表示。每个图表示都必须提供核心操作的实现并重用很多定义在GraphOps中的有用操作。</div>
<span style="word-wrap:break-word; font-weight:700; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">操作一览</span>
<div align="left" style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
一下是定义在Graph和GraphOps中（为了简单起见，表现为图的成员）的功能的快速浏览。注意，某些函数签名已经简化（如默认参数和类型的限制已删除），一些更高级的功能已经被 删除，所以请参阅API文档了解官方的操作列表。</div>
<div class="blockcode" style="word-wrap:break-word; overflow:hidden; margin:10px 0px; padding:10px 0px 5px 10px; background-color:rgb(247,247,247); color:rgb(102,102,102); zoom:1; border:1px solid rgb(204,204,204); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<div id="code_eT3" style="word-wrap:break-word">
<ol style="word-wrap:break-word; margin:0px 0px 0px 10px!important; padding:0px!important">
<li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
/** Summary of the functionality in the property graph */<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
class Graph[VD, ED] {<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
  // Information about the Graph ===================================================================<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
  val numEdges: Long<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
  val numVertices: Long<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
  val inDegrees: VertexRDD[Int]<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
  val outDegrees: VertexRDD[Int]<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
  val degrees: VertexRDD[Int]<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
  // Views of the graph as collections =============================================================<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
  val vertices: VertexRDD[VD]<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
  val edges: EdgeRDD[ED]<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
  val triplets: RDD[EdgeTriplet[VD, ED]]<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
  // Functions for caching graphs ==================================================================<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
  def persist(newLevel: StorageLevel = StorageLevel.MEMORY_ONLY): Graph[VD, ED]<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
  def cache(): Graph[VD, ED]<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
  def unpersistVertices(blocking: Boolean = true): Graph[VD, ED]<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
  // Change the partitioning heuristic  ============================================================<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
  def partitionBy(partitionStrategy: PartitionStrategy): Graph[VD, ED]<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
  // Transform vertex and edge attributes ==========================================================<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
  def mapVertices[VD2](map: (VertexID, VD) =&gt; VD2): Graph[VD2, ED]<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
  def mapEdges[ED2](map: Edge[ED] =&gt; ED2): Graph[VD, ED2]<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
  def mapEdges[ED2](map: (PartitionID, Iterator[Edge[ED]]) =&gt; Iterator[ED2]): Graph[VD, ED2]<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
  def mapTriplets[ED2](map: EdgeTriplet[VD, ED] =&gt; ED2): Graph[VD, ED2]<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
  def mapTriplets[ED2](map: (PartitionID, Iterator[EdgeTriplet[VD, ED]]) =&gt; Iterator[ED2])<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
    : Graph[VD, ED2]<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
  // Modify the graph structure ====================================================================<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
  def reverse: Graph[VD, ED]<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
  def subgraph(<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
      epred: EdgeTriplet[VD,ED] =&gt; Boolean = (x =&gt; true),<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
      vpred: (VertexID, VD) =&gt; Boolean = ((v, d) =&gt; true))<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
    : Graph[VD, ED]<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
  def mask[VD2, ED2](other: Graph[VD2, ED2]): Graph[VD, ED]<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
  def groupEdges(merge: (ED, ED) =&gt; ED): Graph[VD, ED]<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
  // Join RDDs with the graph ======================================================================<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
  def joinVertices[U](table: RDD[(VertexID, U)])(mapFunc: (VertexID, VD, U) =&gt; VD): Graph[VD, ED]<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
  def outerJoinVertices[U, VD2](other: RDD[(VertexID, U)])<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
      (mapFunc: (VertexID, VD, Option[U]) =&gt; VD2)<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
    : Graph[VD2, ED]<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
  // Aggregate information about adjacent triplets =================================================<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
  def collectNeighborIds(edgeDirection: EdgeDirection): VertexRDD[Array[VertexID]]<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
  def collectNeighbors(edgeDirection: EdgeDirection): VertexRDD[Array[(VertexID, VD)]]<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
  def aggregateMessages[Msg: ClassTag](<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
      sendMsg: EdgeContext[VD, ED, Msg] =&gt; Unit,<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
      mergeMsg: (Msg, Msg) =&gt; Msg,<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
      tripletFields: TripletFields = TripletFields.All)<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
    : VertexRDD[A]<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
  // Iterative graph-parallel computation ==========================================================<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
  def pregel[A](initialMsg: A, maxIterations: Int, activeDirection: EdgeDirection)(<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
      vprog: (VertexID, VD, A) =&gt; VD,<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
      sendMsg: EdgeTriplet[VD, ED] =&gt; Iterator[(VertexID,A)],<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
      mergeMsg: (A, A) =&gt; A)<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
    : Graph[VD, ED]<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
  // Basic graph algorithms ========================================================================<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
  def pageRank(tol: Double, resetProb: Double = 0.15): Graph[Double, Double]<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
  def connectedComponents(): Graph[VertexID, ED]<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
  def triangleCount(): Graph[Int, ED]<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
  def stronglyConnectedComponents(numIter: Int): Graph[VertexID, ED]<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
}</li></ol>
</div>
<span style="word-wrap:break-word; margin-left:43px; font-size:12px; color:rgb(51,102,153)!important">复制代码</span></div>
<br style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<br style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<span style="word-wrap:break-word; font-weight:700; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">属性操作</span>
<div align="left" style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
如RDD的map操作一样，属性图包含下面的操作：</div>
<div class="blockcode" style="word-wrap:break-word; overflow:hidden; margin:10px 0px; padding:10px 0px 5px 10px; background-color:rgb(247,247,247); color:rgb(102,102,102); zoom:1; border:1px solid rgb(204,204,204); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<div id="code_kmr" style="word-wrap:break-word">
<ol style="word-wrap:break-word; margin:0px 0px 0px 10px!important; padding:0px!important">
<li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
class Graph[VD, ED] {<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
  def mapVertices[VD2](map: (VertexId, VD) =&gt; VD2): Graph[VD2, ED]<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
  def mapEdges[ED2](map: Edge[ED] =&gt; ED2): Graph[VD, ED2]<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
  def mapTriplets[ED2](map: EdgeTriplet[VD, ED] =&gt; ED2): Graph[VD, ED2]<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
}</li></ol>
</div>
<span style="word-wrap:break-word; margin-left:43px; font-size:12px; color:rgb(51,102,153)!important">复制代码</span></div>
<br style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<div align="left" style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
每个操作都产生一个新的图，这个新的图包含通过用户自定义的map操作修改后的顶点或边的属性。</div>
<div align="left" style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
注意，每种情况下图结构都不受影响。这些操作的一个重要特征是它允许所得图形重用原有图形的结构索引(indices)。下面的两行代码在逻辑上是等价的，但是第一个不保存结构索引，所以 不会从GraphX系统优化中受益。</div>
<div class="blockcode" style="word-wrap:break-word; overflow:hidden; margin:10px 0px; padding:10px 0px 5px 10px; background-color:rgb(247,247,247); color:rgb(102,102,102); zoom:1; border:1px solid rgb(204,204,204); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<div id="code_J38" style="word-wrap:break-word">
<ol style="word-wrap:break-word; margin:0px 0px 0px 10px!important; padding:0px!important">
<li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
val newVertices = graph.vertices.map { case (id, attr) =&gt; (id, mapUdf(id, attr)) }<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
val newGraph = Graph(newVertices, graph.edges)</li></ol>
</div>
<span style="word-wrap:break-word; margin-left:43px; font-size:12px; color:rgb(51,102,153)!important">复制代码</span></div>
<br style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<div align="left" style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
另一种方法是用[url=http://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.graphx.Graph@mapVertices[VD2]((VertexId,VD]mapVertices[/url]⇒VD2)(ClassTag[VD2]):Graph[VD2,ED])保存索引。</div>
<div class="blockcode" style="word-wrap:break-word; overflow:hidden; margin:10px 0px; padding:10px 0px 5px 10px; background-color:rgb(247,247,247); color:rgb(102,102,102); zoom:1; border:1px solid rgb(204,204,204); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<div id="code_I2F" style="word-wrap:break-word">
<ol style="word-wrap:break-word; margin:0px 0px 0px 10px!important; padding:0px!important">
<li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
val newGraph = graph.mapVertices((id, attr) =&gt; mapUdf(id, attr))</li></ol>
</div>
<span style="word-wrap:break-word; margin-left:43px; font-size:12px; color:rgb(51,102,153)!important">复制代码</span></div>
<br style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<div align="left" style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
这些操作经常用来初始化的图形，用作特定计算或者用来处理项目不需要的属性。例如，给定一个图，这个图的顶点特征包含出度，我们为PageRank初始化它。</div>
<div class="blockcode" style="word-wrap:break-word; overflow:hidden; margin:10px 0px; padding:10px 0px 5px 10px; background-color:rgb(247,247,247); color:rgb(102,102,102); zoom:1; border:1px solid rgb(204,204,204); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<div id="code_tCu" style="word-wrap:break-word">
<ol style="word-wrap:break-word; margin:0px 0px 0px 10px!important; padding:0px!important">
<li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
// Given a graph where the vertex property is the out degree<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
val inputGraph: Graph[Int, String] =<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
  graph.outerJoinVertices(graph.outDegrees)((vid, _, degOpt) =&gt; degOpt.getOrElse(0))<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
// Construct a graph where each edge contains the weight<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
// and each vertex is the initial PageRank<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
val outputGraph: Graph[Double, Double] =<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
  inputGraph.mapTriplets(triplet =&gt; 1.0 / triplet.srcAttr).mapVertices((id, _) =&gt; 1.0)</li></ol>
</div>
<span style="word-wrap:break-word; margin-left:43px; font-size:12px; color:rgb(51,102,153)!important">复制代码</span></div>
<br style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<br style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<div align="left" style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<span style="font-size:24px; word-wrap:break-word"><span style="word-wrap:break-word; font-weight:700">结构性操作</span></span></div>
<div align="left" style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
当前的GraphX仅仅支持一组简单的常用结构性操作。下面是基本的结构性操作列表。</div>
<div class="blockcode" style="word-wrap:break-word; overflow:hidden; margin:10px 0px; padding:10px 0px 5px 10px; background-color:rgb(247,247,247); color:rgb(102,102,102); zoom:1; border:1px solid rgb(204,204,204); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<div id="code_C4U" style="word-wrap:break-word">
<ol style="word-wrap:break-word; margin:0px 0px 0px 10px!important; padding:0px!important">
<li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
class Graph[VD, ED] {<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
  def reverse: Graph[VD, ED]<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
  def subgraph(epred: EdgeTriplet[VD,ED] =&gt; Boolean,<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
               vpred: (VertexId, VD) =&gt; Boolean): Graph[VD, ED]<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
  def mask[VD2, ED2](other: Graph[VD2, ED2]): Graph[VD, ED]<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
  def groupEdges(merge: (ED, ED) =&gt; ED): Graph[VD,ED]<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
}</li></ol>
</div>
<span style="word-wrap:break-word; margin-left:43px; font-size:12px; color:rgb(51,102,153)!important">复制代码</span></div>
<br style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<div align="left" style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
[url=http://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.graphx.Graph@reverse:Graph[VD,ED]]reverse[/url]操作返回一个新的图，这个图的边的方向都是反转的。例如，这个操作可以用来计算反转的PageRank。因为反转操作没有修改顶点或者边的属性或者改变边的数量，所以我们可以 在不移动或者复制数据的情况下有效地实现它。</div>
<div align="left" style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
[url=http://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.graphx.Graph@subgraph((EdgeTriplet[VD,ED]]subgraph[/url]⇒Boolean,(VertexId,VD)⇒Boolean):Graph[VD,ED])操作 利用顶点和边的谓词（predicates），返回的图仅仅包含满足顶点谓词的顶点、满足边谓词的边以及满足顶点谓词的连接顶点（connect vertices）。subgraph操作可以用于很多场景，如获取
 感兴趣的顶点和边组成的图或者获取清除断开链接后的图。下面的例子删除了断开的链接。</div>
<div class="blockcode" style="word-wrap:break-word; overflow:hidden; margin:10px 0px; padding:10px 0px 5px 10px; background-color:rgb(247,247,247); color:rgb(102,102,102); zoom:1; border:1px solid rgb(204,204,204); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<div id="code_n05" style="word-wrap:break-word">
<ol style="word-wrap:break-word; margin:0px 0px 0px 10px!important; padding:0px!important">
<li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
// Create an RDD for the vertices<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
val users: RDD[(VertexId, (String, String))] =<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
  sc.parallelize(Array((3L, ("rxin", "student")), (7L, ("jgonzal", "postdoc")),<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
                       (5L, ("franklin", "prof")), (2L, ("istoica", "prof")),<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
                       (4L, ("peter", "student"))))<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
// Create an RDD for edges<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
val relationships: RDD[Edge[String]] =<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
  sc.parallelize(Array(Edge(3L, 7L, "collab"),    Edge(5L, 3L, "advisor"),<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
                       Edge(2L, 5L, "colleague"), Edge(5L, 7L, "pi"),<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
                       Edge(4L, 0L, "student"),   Edge(5L, 0L, "colleague")))<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
// Define a default user in case there are relationship with missing user<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
val defaultUser = ("John Doe", "Missing")<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
// Build the initial Graph<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
val graph = Graph(users, relationships, defaultUser)<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
// Notice that there is a user 0 (for which we have no information) connected to users<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
// 4 (peter) and 5 (franklin).<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
graph.triplets.map(<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
    triplet =&gt; triplet.srcAttr._1 + " is the " + triplet.attr + " of " + triplet.dstAttr._1<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
  ).collect.foreach(println(_))<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
// Remove missing vertices as well as the edges to connected to them<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
val validGraph = graph.subgraph(vpred = (id, attr) =&gt; attr._2 != "Missing")<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
// The valid subgraph will disconnect users 4 and 5 by removing user 0<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
validGraph.vertices.collect.foreach(println(_))<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
validGraph.triplets.map(<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
    triplet =&gt; triplet.srcAttr._1 + " is the " + triplet.attr + " of " + triplet.dstAttr._1<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
  ).collect.foreach(println(_))</li></ol>
</div>
<span style="word-wrap:break-word; margin-left:43px; font-size:12px; color:rgb(51,102,153)!important">复制代码</span></div>
<br style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<div align="left" style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
注意，上面的例子中，仅仅提供了顶点谓词。如果没有提供顶点或者边的谓词，subgraph操作默认为true。</div>
<div align="left" style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
[url=http://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.graphx.Graph@mask[VD2,ED2](Graph[VD2,ED2]]mask[/url](ClassTag[VD2],ClassTag[ED2]):Graph[VD,ED])操作 构造一个子图，这个子图包含输入图中包含的顶点和边。这个操作可以和subgraph操作相结合，基于另外一个相关图的特征去约束一个图。例如，我们可能利用缺失顶点的图运行连通体（？连通组件connected
 components），然后返回有效的子图。</div>
<div class="blockcode" style="word-wrap:break-word; overflow:hidden; margin:10px 0px; padding:10px 0px 5px 10px; background-color:rgb(247,247,247); color:rgb(102,102,102); zoom:1; border:1px solid rgb(204,204,204); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<div id="code_RrM" style="word-wrap:break-word">
<ol style="word-wrap:break-word; margin:0px 0px 0px 10px!important; padding:0px!important">
<li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
/ Run Connected Components<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
val ccGraph = graph.connectedComponents() // No longer contains missing field<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
// Remove missing vertices as well as the edges to connected to them<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
val validGraph = graph.subgraph(vpred = (id, attr) =&gt; attr._2 != "Missing")<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
// Restrict the answer to the valid subgraph<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
val validCCGraph = ccGraph.mask(validGraph)</li></ol>
</div>
<span style="word-wrap:break-word; margin-left:43px; font-size:12px; color:rgb(51,102,153)!important">复制代码</span></div>
<br style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<div align="left" style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<a target="_blank" href="http://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.graphx.Graph@groupEdges((ED,ED" rel="nofollow" style="word-wrap:break-word; color:rgb(51,102,153)">groupEdges</a>⇒ED):Graph[VD,ED])操作合并多重图 中的并行边(如顶点对之间重复的边)。在大量的应用程序中，并行的边可以合并（它们的权重合并）为一条边从而降低图的大小。</div>
<span style="word-wrap:break-word; font-weight:700; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">连接操作</span>
<div align="left" style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
在许多情况下，有必要将外部数据加入到图中。例如，我们可能有额外的用户属性需要合并到已有的图中或者我们可能想从一个图中取出顶点特征加入到另外一个图中。这些任务可以用join操作完成。 下面列出的是主要的join操作。</div>
<div class="blockcode" style="word-wrap:break-word; overflow:hidden; margin:10px 0px; padding:10px 0px 5px 10px; background-color:rgb(247,247,247); color:rgb(102,102,102); zoom:1; border:1px solid rgb(204,204,204); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<div id="code_K11" style="word-wrap:break-word">
<ol style="word-wrap:break-word; margin:0px 0px 0px 10px!important; padding:0px!important">
<li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
class Graph[VD, ED] {<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
  def joinVertices[U](table: RDD[(VertexId, U)])(map: (VertexId, VD, U) =&gt; VD)<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
    : Graph[VD, ED]<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
  def outerJoinVertices[U, VD2](table: RDD[(VertexId, U)])(map: (VertexId, VD, Option[U]) =&gt; VD2)<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
    : Graph[VD2, ED]<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
}</li></ol>
</div>
<span style="word-wrap:break-word; margin-left:43px; font-size:12px; color:rgb(51,102,153)!important">复制代码</span></div>
<br style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<div align="left" style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
[url=http://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.graphx.GraphOps@joinVertices[U](RDD[(VertexId,U]joinVertices[/url]])((VertexId,VD,U)⇒VD)(ClassTag[U]):Graph[VD,ED]) 操作将输入RDD和顶点相结合，返回一个新的带有顶点特征的图。这些特征是通过在连接顶点的结果上使用用户定义的map函数获得的。在RDD中没有匹配值的顶点保留其原始值。</div>
<div align="left" style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
注意，对于给定的顶点，如果RDD中有超过1个的匹配值，则仅仅使用其中的一个。建议用下面的方法保证输入RDD的唯一性。下面的方法也会预索引返回的值用以加快后续的join操作。</div>
<div class="blockcode" style="word-wrap:break-word; overflow:hidden; margin:10px 0px; padding:10px 0px 5px 10px; background-color:rgb(247,247,247); color:rgb(102,102,102); zoom:1; border:1px solid rgb(204,204,204); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<div id="code_O1m" style="word-wrap:break-word">
<ol style="word-wrap:break-word; margin:0px 0px 0px 10px!important; padding:0px!important">
<li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
val nonUniqueCosts: RDD[(VertexID, Double)]<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
val uniqueCosts: VertexRDD[Double] =<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
  graph.vertices.aggregateUsingIndex(nonUnique, (a,b) =&gt; a + b)<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
val joinedGraph = graph.joinVertices(uniqueCosts)(<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
  (id, oldCost, extraCost) =&gt; oldCost + extraCost)</li></ol>
</div>
<span style="word-wrap:break-word; margin-left:43px; font-size:12px; color:rgb(51,102,153)!important">复制代码</span></div>
<br style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<div align="left" style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
除了将用户自定义的map函数用到所有顶点和改变顶点属性类型以外，更一般的[url=http://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.graphx.Graph@outerJoinVertices[U,VD2](RDD[(VertexId,U]outerJoinVertices[/url]])((VertexId,VD,Option[U])⇒VD2)(ClassTag[U],ClassTag[VD2]):Graph[VD2,ED])与joinVertices类似。
 因为并不是所有顶点在RDD中拥有匹配的值，map函数需要一个option类型。</div>
<div class="blockcode" style="word-wrap:break-word; overflow:hidden; margin:10px 0px; padding:10px 0px 5px 10px; background-color:rgb(247,247,247); color:rgb(102,102,102); zoom:1; border:1px solid rgb(204,204,204); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<div id="code_qI7" style="word-wrap:break-word">
<ol style="word-wrap:break-word; margin:0px 0px 0px 10px!important; padding:0px!important">
<li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
val outDegrees: VertexRDD[Int] = graph.outDegrees<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
val degreeGraph = graph.outerJoinVertices(outDegrees) { (id, oldAttr, outDegOpt) =&gt;<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
  outDegOpt match {<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
    case Some(outDeg) =&gt; outDeg<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
    case None =&gt; 0 // No outDegree means zero outDegree<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
  }<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
}</li></ol>
</div>
<span style="word-wrap:break-word; margin-left:43px; font-size:12px; color:rgb(51,102,153)!important">复制代码</span></div>
<br style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<br style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<div align="left" style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
你可能已经注意到了，在上面的例子中用到了curry函数的多参数列表。虽然我们可以将f(a)(b)写成f(a,b)，但是f(a,b)意味着b的类型推断将不会依赖于a。因此，用户需要为定义 的函数提供类型标注。</div>
<br style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<div class="blockcode" style="word-wrap:break-word; overflow:hidden; margin:10px 0px; padding:10px 0px 5px 10px; background-color:rgb(247,247,247); color:rgb(102,102,102); zoom:1; border:1px solid rgb(204,204,204); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<div id="code_Q2f" style="word-wrap:break-word">
<ol style="word-wrap:break-word; margin:0px 0px 0px 10px!important; padding:0px!important">
<li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
val joinedGraph = graph.joinVertices(uniqueCosts,<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
  (id: VertexID, oldCost: Double, extraCost: Double) =&gt; oldCost + extraCost)</li></ol>
</div>
<span style="word-wrap:break-word; margin-left:43px; font-size:12px; color:rgb(51,102,153)!important">复制代码</span></div>
<br style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<span style="word-wrap:break-word; font-weight:700; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">相邻聚合（Neighborhood Aggregation）</span>
<div align="left" style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
图分析任务的一个关键步骤是汇总每个顶点附近的信息。例如我们可能想知道每个用户的追随者的数量或者每个用户的追随者的平均年龄。许多迭代图算法（如PageRank，最短路径和连通体） 多次聚合相邻顶点的属性。</div>
<div align="left" style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
为了提高性能，主要的聚合操作从graph.mapReduceTriplets改为了新的graph.AggregateMessages。虽然API的改变相对较小，但是我们仍然提供了过渡的指南。</div>
<span style="word-wrap:break-word; font-weight:700; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">聚合消息(aggregateMessages)</span>
<div align="left" style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
GraphX中的核心聚合操作是[url=http://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.graphx.Graph@aggregateMessages[A]((EdgeContext[VD,ED,A]]aggregateMessages[/url]⇒Unit,(A,A)⇒A,TripletFields)(ClassTag[A]):VertexRDD[A])。 这个操作将用户定义的sendMsg函数应用到图的每个边三元组(edge
 triplet)，然后应用mergeMsg函数在其目的顶点聚合这些消息。</div>
<div class="blockcode" style="word-wrap:break-word; overflow:hidden; margin:10px 0px; padding:10px 0px 5px 10px; background-color:rgb(247,247,247); color:rgb(102,102,102); zoom:1; border:1px solid rgb(204,204,204); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<div id="code_wPu" style="word-wrap:break-word">
<ol style="word-wrap:break-word; margin:0px 0px 0px 10px!important; padding:0px!important">
<li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
class Graph[VD, ED] {<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
  def aggregateMessages[Msg: ClassTag](<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
      sendMsg: EdgeContext[VD, ED, Msg] =&gt; Unit,<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
      mergeMsg: (Msg, Msg) =&gt; Msg,<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
      tripletFields: TripletFields = TripletFields.All)<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
    : VertexRDD[Msg]<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
}</li></ol>
</div>
<span style="word-wrap:break-word; margin-left:43px; font-size:12px; color:rgb(51,102,153)!important">复制代码</span></div>
<br style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<div align="left" style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
用户自定义的sendMsg函数是一个<a target="_blank" href="http://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.graphx.EdgeContext" rel="nofollow" style="word-wrap:break-word; color:rgb(51,102,153)">EdgeContext</a>类型。它暴露源和目的属性以及边缘属性 以及发送消息给源和目的属性的函数(<a target="_blank" href="http://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.graphx.EdgeContext@sendToSrc(msg:A" rel="nofollow" style="word-wrap:break-word; color:rgb(51,102,153)">sendToSrc</a>:Unit)和<a target="_blank" href="http://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.graphx.EdgeContext@sendToDst(msg:A" rel="nofollow" style="word-wrap:break-word; color:rgb(51,102,153)">sendToDst</a>:Unit))。
 可将sendMsg函数看做map-reduce过程中的map函数。用户自定义的mergeMsg函数指定两个消息到相同的顶点并保存为一个消息。可以将mergeMsg函数看做map-reduce过程中的reduce函数。 [url=http://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.graphx.Graph@aggregateMessages[A]((EdgeContext[VD,ED,A]]aggregateMessages[/url]⇒Unit,(A,A)⇒A,TripletFields)(ClassTag[A]):VertexRDD[A])
 操作返回一个包含聚合消息(目的地为每个顶点)的VertexRDD[Msg]。没有接收到消息的顶点不包含在返回的VertexRDD中。</div>
<div align="left" style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
另外，[url=http://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.graphx.Graph@aggregateMessages[A]((EdgeContext[VD,ED,A]]aggregateMessages[/url]⇒Unit,(A,A)⇒A,TripletFields)(ClassTag[A]):VertexRDD[A]) 有一个可选的tripletFields参数，它指出在EdgeContext中，哪些数据被访问（如源顶点特征而不是目的顶点特征）。tripletsFields可能的选项定义在<a target="_blank" href="http://spark.apache.org/docs/latest/api/java/org/apache/spark/graphx/TripletFields.html" rel="nofollow" style="word-wrap:break-word; color:rgb(51,102,153)">TripletFields</a>中。
 tripletFields参数用来通知GraphX仅仅只需要EdgeContext的一部分允许GraphX选择一个优化的连接策略。例如，如果我们想计算每个用户的追随者的平均年龄，我们仅仅只需要源字段。 所以我们用TripletFields.Src表示我们仅仅只需要源字段。</div>
<div align="left" style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
在下面的例子中，我们用aggregateMessages操作计算每个用户更年长的追随者的年龄。</div>
<div class="blockcode" style="word-wrap:break-word; overflow:hidden; margin:10px 0px; padding:10px 0px 5px 10px; background-color:rgb(247,247,247); color:rgb(102,102,102); zoom:1; border:1px solid rgb(204,204,204); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<div id="code_M7C" style="word-wrap:break-word">
<ol style="word-wrap:break-word; margin:0px 0px 0px 10px!important; padding:0px!important">
<li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
// Import random graph generation library<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
import org.apache.spark.graphx.util.GraphGenerators<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
// Create a graph with "age" as the vertex property.  Here we use a random graph for simplicity.<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
val graph: Graph[Double, Int] =<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
  GraphGenerators.logNormalGraph(sc, numVertices = 100).mapVertices( (id, _) =&gt; id.toDouble )<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
// Compute the number of older followers and their total age<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
val olderFollowers: VertexRDD[(Int, Double)] = graph.aggregateMessages[(Int, Double)](<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
  triplet =&gt; { // Map Function<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
    if (triplet.srcAttr &gt; triplet.dstAttr) {<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
      // Send message to destination vertex containing counter and age<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
      triplet.sendToDst(1, triplet.srcAttr)<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
    }<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
  },<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
  // Add counter and age<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
  (a, b) =&gt; (a._1 + b._1, a._2 + b._2) // Reduce Function<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
)<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
// Divide total age by number of older followers to get average age of older followers<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
val avgAgeOfOlderFollowers: VertexRDD[Double] =<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
  olderFollowers.mapValues( (id, value) =&gt; value match { case (count, totalAge) =&gt; totalAge / count } )<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
// Display the results<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
avgAgeOfOlderFollowers.collect.foreach(println(_))</li></ol>
</div>
<span style="word-wrap:break-word; margin-left:43px; font-size:12px; color:rgb(51,102,153)!important">复制代码</span></div>
<br style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<div align="left" style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
当消息（以及消息的总数）是常量大小(列表和连接替换为浮点数和添加)时，aggregateMessages操作的效果最好。</div>
<span style="word-wrap:break-word; font-weight:700; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">Map Reduce三元组过渡指南</span>
<div align="left" style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
在之前版本的GraphX中，利用[mapReduceTriplets]操作完成相邻聚合。</div>
<div class="blockcode" style="word-wrap:break-word; overflow:hidden; margin:10px 0px; padding:10px 0px 5px 10px; background-color:rgb(247,247,247); color:rgb(102,102,102); zoom:1; border:1px solid rgb(204,204,204); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<div id="code_jqw" style="word-wrap:break-word">
<ol style="word-wrap:break-word; margin:0px 0px 0px 10px!important; padding:0px!important">
<li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
class Graph[VD, ED] {<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
  def mapReduceTriplets[Msg](<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
      map: EdgeTriplet[VD, ED] =&gt; Iterator[(VertexId, Msg)],<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
      reduce: (Msg, Msg) =&gt; Msg)<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
    : VertexRDD[Msg]<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
}</li></ol>
</div>
<span style="word-wrap:break-word; margin-left:43px; font-size:12px; color:rgb(51,102,153)!important">复制代码</span></div>
<br style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<div align="left" style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
mapReduceTriplets操作在每个三元组上应用用户定义的map函数，然后保存用用户定义的reduce函数聚合的消息。然而，我们发现用户返回的迭代器是昂贵的，它抑制了我们添加额外优化(例如本地顶点的重新编号)的能力。 [url=http://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.graphx.Graph@aggregateMessages[A]((EdgeContext[VD,ED,A]]aggregateMessages[/url]⇒Unit,(A,A)⇒A,TripletFields)(ClassTag[A]):VertexRDD[A])
 暴露三元组字段和函数显示的发送消息到源和目的顶点。并且，我们删除了字节码检测转而需要用户指明三元组的哪些字段实际需要。</div>
<div align="left" style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
下面的代码用到了mapReduceTriplets</div>
<div class="blockcode" style="word-wrap:break-word; overflow:hidden; margin:10px 0px; padding:10px 0px 5px 10px; background-color:rgb(247,247,247); color:rgb(102,102,102); zoom:1; border:1px solid rgb(204,204,204); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<div id="code_Z5D" style="word-wrap:break-word">
<ol style="word-wrap:break-word; margin:0px 0px 0px 10px!important; padding:0px!important">
<li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
val graph: Graph[Int, Float] = ...<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
def msgFun(triplet: Triplet[Int, Float]): Iterator[(Int, String)] = {<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
  Iterator((triplet.dstId, "Hi"))<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
}<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
def reduceFun(a: Int, b: Int): Int = a + b<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
val result = graph.mapReduceTriplets[String](msgFun, reduceFun)</li></ol>
</div>
<span style="word-wrap:break-word; margin-left:43px; font-size:12px; color:rgb(51,102,153)!important">复制代码</span></div>
<br style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<div align="left" style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
下面的代码用到了aggregateMessages</div>
<div class="blockcode" style="word-wrap:break-word; overflow:hidden; margin:10px 0px; padding:10px 0px 5px 10px; background-color:rgb(247,247,247); color:rgb(102,102,102); zoom:1; border:1px solid rgb(204,204,204); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<div id="code_wZz" style="word-wrap:break-word">
<ol style="word-wrap:break-word; margin:0px 0px 0px 10px!important; padding:0px!important">
<li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
val graph: Graph[Int, Float] = ...<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
def msgFun(triplet: EdgeContext[Int, Float, String]) {<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
  triplet.sendToDst("Hi")<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
}<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
def reduceFun(a: Int, b: Int): Int = a + b<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
val result = graph.aggregateMessages[String](msgFun, reduceFun)</li></ol>
</div>
<span style="word-wrap:break-word; margin-left:43px; font-size:12px; color:rgb(51,102,153)!important">复制代码</span></div>
<br style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<span style="word-wrap:break-word; font-weight:700; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">计算度信息</span>
<div align="left" style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
最一般的聚合任务就是计算顶点的度，即每个顶点相邻边的数量。在有向图中，经常需要知道顶点的入度、出度以及总共的度。<a target="_blank" href="http://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.graphx.GraphOps" rel="nofollow" style="word-wrap:break-word; color:rgb(51,102,153)">GraphOps</a> 类包含一个操作集合用来计算每个顶点的度。例如，下面的例子计算最大的入度、出度和总度。</div>
<div class="blockcode" style="word-wrap:break-word; overflow:hidden; margin:10px 0px; padding:10px 0px 5px 10px; background-color:rgb(247,247,247); color:rgb(102,102,102); zoom:1; border:1px solid rgb(204,204,204); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<div id="code_qNy" style="word-wrap:break-word">
<ol style="word-wrap:break-word; margin:0px 0px 0px 10px!important; padding:0px!important">
<li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
// Define a reduce operation to compute the highest degree vertex<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
def max(a: (VertexId, Int), b: (VertexId, Int)): (VertexId, Int) = {<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
  if (a._2 &gt; b._2) a else b<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
}<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
// Compute the max degrees<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
val maxInDegree: (VertexId, Int)  = graph.inDegrees.reduce(max)<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
val maxOutDegree: (VertexId, Int) = graph.outDegrees.reduce(max)<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
val maxDegrees: (VertexId, Int)   = graph.degrees.reduce(max)</li></ol>
</div>
<span style="word-wrap:break-word; margin-left:43px; font-size:12px; color:rgb(51,102,153)!important">复制代码</span></div>
<br style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<br style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<span style="word-wrap:break-word; font-weight:700; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">Collecting Neighbors</span>
<div align="left" style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
在某些情况下，通过收集每个顶点相邻的顶点及它们的属性来表达计算可能更容易。这可以通过<a target="_blank" href="http://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.graphx.GraphOps@collectNeighborIds(EdgeDirection" rel="nofollow" style="word-wrap:break-word; color:rgb(51,102,153)">collectNeighborIds</a>:VertexRDD[Array[VertexId]])
 和<a target="_blank" href="http://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.graphx.GraphOps@collectNeighbors(EdgeDirection" rel="nofollow" style="word-wrap:break-word; color:rgb(51,102,153)">collectNeighbors</a>:VertexRDD[Array[(VertexId,VD)]])操作来简单的完成</div>
<div class="blockcode" style="word-wrap:break-word; overflow:hidden; margin:10px 0px; padding:10px 0px 5px 10px; background-color:rgb(247,247,247); color:rgb(102,102,102); zoom:1; border:1px solid rgb(204,204,204); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<div id="code_oEQ" style="word-wrap:break-word">
<ol style="word-wrap:break-word; margin:0px 0px 0px 10px!important; padding:0px!important">
<li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
class GraphOps[VD, ED] {<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
  def collectNeighborIds(edgeDirection: EdgeDirection): VertexRDD[Array[VertexId]]<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
  def collectNeighbors(edgeDirection: EdgeDirection): VertexRDD[ Array[(VertexId, VD)] ]<br style="word-wrap:break-word">
</li><li style="word-wrap:break-word; margin:0px 0px 0px 2em; padding:0px 0px 0px 10px; list-style-type:decimal-leading-zero; font-family:Monaco,Consolas,'Lucida Console','Courier New',serif; font-size:12px; line-height:1.8em">
}</li></ol>
</div>
<span style="word-wrap:break-word; margin-left:43px; font-size:12px; color:rgb(51,102,153)!important">复制代码</span></div>
<br style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<div align="left" style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
这些操作是非常昂贵的，因为它们需要重复的信息和大量的通信。如果可能，尽量用aggregateMessages操作直接表达相同的计算。</div>
<span style="word-wrap:break-word; font-weight:700; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">缓存和不缓存</span>
<div align="left" style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
在Spark中，RDDs默认是不缓存的。为了避免重复计算，当需要多次利用它们时，我们必须显示地缓存它们。GraphX中的图也有相同的方式。当利用到图多次时，确保首先访问Graph.cache()方法。</div>
<div align="left" style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
在迭代计算中，为了获得最佳的性能，不缓存可能是必须的。默认情况下，缓存的RDDs和图会一直保留在内存中直到因为内存压力迫使它们以LRU的顺序删除。对于迭代计算，先前的迭代的中间结果将填充到缓存 中。虽然它们最终会被删除，但是保存在内存中的不需要的数据将会减慢垃圾回收。只有中间结果不需要，不缓存它们是更高效的。这涉及到在每次迭代中物化一个图或者RDD而不缓存所有其它的数据集。 在将来的迭代中仅用物化的数据集。然而，因为图是由多个RDD组成的，正确的不持久化它们是困难的。对于迭代计算，我们建议使用Pregel API，它可以正确的不持久化中间结果。</div>
<br style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<br style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<span style="font-size:24px; word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun"><span style="word-wrap:break-word; font-weight:700">相关内容：</span></span><br style="word-wrap:break-word; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px">
<span style="word-wrap:break-word; font-weight:700; color:rgb(68,68,68); font-family:Tahoma,'Microsoft Yahei',Simsun; font-size:14.399999618530273px; line-height:16.799999237060547px"><br style="word-wrap:break-word">
Spark中文手册1-编程指南<br style="word-wrap:break-word">
<a target="_blank" href="http://www.aboutyun.com/thread-11413-1-1.html" rel="nofollow" style="word-wrap:break-word; color:rgb(51,102,153)">http://www.aboutyun.com/thread-11413-1-1.html</a><br style="word-wrap:break-word">
<br style="word-wrap:break-word">
Spark中文手册2：Spark之一个快速的例子<br style="word-wrap:break-word">
<a target="_blank" href="http://www.aboutyun.com/thread-11484-1-1.html" rel="nofollow" style="word-wrap:break-word; color:rgb(51,102,153)">http://www.aboutyun.com/thread-11484-1-1.html</a><br style="word-wrap:break-word">
<br style="word-wrap:break-word">
Spark中文手册3：Spark之基本概念<br style="word-wrap:break-word">
<a target="_blank" href="http://www.aboutyun.com/thread-11502-1-1.html" rel="nofollow" style="word-wrap:break-word; color:rgb(51,102,153)">http://www.aboutyun.com/thread-11502-1-1.html</a><br style="word-wrap:break-word">
<br style="word-wrap:break-word">
Spark中文手册4：Spark之基本概念（2）<br style="word-wrap:break-word">
<a target="_blank" href="http://www.aboutyun.com/thread-11516-1-1.html" rel="nofollow" style="word-wrap:break-word; color:rgb(51,102,153)">http://www.aboutyun.com/thread-11516-1-1.html</a><br style="word-wrap:break-word">
<br style="word-wrap:break-word">
<br style="word-wrap:break-word">
Spark中文手册5：Spark之基本概念（3）<br style="word-wrap:break-word">
<a target="_blank" href="http://www.aboutyun.com/thread-11535-1-1.html" rel="nofollow" style="word-wrap:break-word; color:rgb(51,102,153)">http://www.aboutyun.com/thread-11535-1-1.html</a><br style="word-wrap:break-word">
<br style="word-wrap:break-word">
Spark中文手册6：Spark-sql由入门到精通<br style="word-wrap:break-word">
<a target="_blank" href="http://www.aboutyun.com/thread-11562-1-1.html" rel="nofollow" style="word-wrap:break-word; color:rgb(51,102,153)">http://www.aboutyun.com/thread-11562-1-1.html</a><br style="word-wrap:break-word">
<br style="word-wrap:break-word">
<br style="word-wrap:break-word">
Spark中文手册7：Spark-sql由入门到精通【续】<br style="word-wrap:break-word">
<a target="_blank" href="http://www.aboutyun.com/thread-11575-1-1.html" rel="nofollow" style="word-wrap:break-word; color:rgb(51,102,153)">http://www.aboutyun.com/thread-11575-1-1.html</a><br style="word-wrap:break-word">
<br style="word-wrap:break-word">
<br style="word-wrap:break-word">
<br style="word-wrap:break-word">
Spark中文手册9：spark GraphX编程指南（2）<br style="word-wrap:break-word">
<a target="_blank" href="http://www.aboutyun.com/thread-11601-1-1.html" rel="nofollow" style="word-wrap:break-word; color:rgb(51,102,153)">http://www.aboutyun.com/thread-11601-1-1.html</a><br style="word-wrap:break-word">
<br style="word-wrap:break-word">
Spark中文手册10：spark部署：提交应用程序及独立部署模式<br style="word-wrap:break-word">
<a target="_blank" href="http://www.aboutyun.com/thread-11615-1-1.html" rel="nofollow" style="word-wrap:break-word; color:rgb(51,102,153)">http://www.aboutyun.com/thread-11615-1-1.html</a><br style="word-wrap:break-word">
<br style="word-wrap:break-word">
<br style="word-wrap:break-word">
Spark中文手册11：Spark 配置指南<br style="word-wrap:break-word">
<a target="_blank" href="http://www.aboutyun.com/thread-10652-1-1.html" rel="nofollow" style="word-wrap:break-word; color:rgb(51,102,153)">http://www.aboutyun.com/thread-10652-1-1.html</a></span>
            </div>
                </div>