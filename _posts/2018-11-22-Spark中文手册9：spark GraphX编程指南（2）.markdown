---
layout:     post
title:      Spark中文手册9：spark GraphX编程指南（2）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;">问题导读</span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;"><span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;"><span style="color:#ff0000;">1.GraphX提供了几种方式从RDD或者磁盘上的顶点和边集合构造图?</span><br><span style="color:#ff0000;">2.PageRank算法在图中发挥什么作用？</span><br><span style="color:#ff0000;">3.三角形计数算法的作用是什么？</span><br><br><img id="aimg_XNkJA" class="zoom" border="0" alt="" src="http://www.aboutyun.com/static/image/hrline/4.gif" width="500" height="35"><br><br><br></span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;"><a href="http://www.aboutyun.com/thread-11413-1-1.html" rel="nofollow" style="color:rgb(51,102,153);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;">Spark中文手册-编程指南</a><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;"><a href="http://www.aboutyun.com/thread-11484-1-1.html" rel="nofollow" style="color:rgb(51,102,153);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;">Spark之一个快速的例子</a><a href="http://www.aboutyun.com/thread-11502-1-1.html" rel="nofollow" style="color:rgb(51,102,153);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;">Spark之基本概念</a><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;"><a href="http://www.aboutyun.com/thread-11502-1-1.html" rel="nofollow" style="color:rgb(51,102,153);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;">Spark之基本概念</a><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;"><a href="http://www.aboutyun.com/thread-11516-1-1.html" rel="nofollow" style="color:rgb(51,102,153);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;">Spark之基本概念（2）</a><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;"><a href="http://www.aboutyun.com/thread-11535-1-1.html" rel="nofollow" style="color:rgb(51,102,153);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;">Spark之基本概念（3）</a><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;"><a href="http://www.aboutyun.com/thread-11562-1-1.html" rel="nofollow" style="color:rgb(51,102,153);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;">Spark-sql由入门到精通</a><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;"><a href="http://www.aboutyun.com/thread-11575-1-1.html" rel="nofollow" style="color:rgb(51,102,153);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;">Spark-sql由入门到精通续</a><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;"><a href="http://www.aboutyun.com/thread-11589-1-1.html" rel="nofollow" style="color:rgb(51,102,153);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;">spark
 GraphX编程指南（1）</a><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;"><span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;">Pregel API</span>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;">
图本身是递归数据结构，顶点的属性依赖于它们邻居的属性，这些邻居的属性又依赖于自己邻居的属性。所以许多重要的图算法都是迭代的重新计算每个顶点的属性，直到满足某个确定的条件。 一系列的graph-parallel抽象已经被提出来用来表达这些迭代算法。GraphX公开了一个类似Pregel的操作，它是广泛使用的Pregel和GraphLab抽象的一个融合。</div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;">
在GraphX中，更高级的Pregel操作是一个约束到图拓扑的批量同步（bulk-synchronous）并行消息抽象。Pregel操作者执行一系列的超级步骤（super steps），在这些步骤中，顶点从 之前的超级步骤中接收进入(inbound)消息的总和，为顶点属性计算一个新的值，然后在以后的超级步骤中发送消息到邻居顶点。不像Pregel而更像GraphLab，消息作为一个边三元组的函数被并行 计算，消息计算既访问了源顶点特征也访问了目的顶点特征。在超级步中，没有收到消息的顶点被跳过。当没有消息遗留时，Pregel操作停止迭代并返回最终的图。</div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;">
注意，与更标准的Pregel实现不同的是，GraphX中的顶点仅仅能发送信息给邻居顶点，并利用用户自定义的消息函数构造消息。这些限制允许在GraphX进行额外的优化。</div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;">
一下是[url=https://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.graphx.GraphOps@pregel[A](A,Int,EdgeDirection]Pregel操作[/url]((VertexId,VD,A)⇒VD,(EdgeTriplet[VD,ED])⇒Iterator[(VertexId,A)],(A,A)⇒A)(ClassTag[A]):Graph[VD,ED])的类型签名以及实现草图（注意，访问graph.cache已经被删除）</div>
<div class="blockcode" style="overflow:hidden;background-color:rgb(247,247,247);color:rgb(102,102,102);border:1px solid rgb(204,204,204);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;">
<div id="code_nss">
<ol><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
class GraphOps[VD, ED] {<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
  def pregel[A]<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
      (initialMsg: A,<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
       maxIter: Int = Int.MaxValue,<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
       activeDir: EdgeDirection = EdgeDirection.Out)<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
      (vprog: (VertexId, VD, A) =&gt; VD,<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
       sendMsg: EdgeTriplet[VD, ED] =&gt; Iterator[(VertexId, A)],<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
       mergeMsg: (A, A) =&gt; A)<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
    : Graph[VD, ED] = {<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
    // Receive the initial message at each vertex<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
    var g = mapVertices( (vid, vdata) =&gt; vprog(vid, vdata, initialMsg) ).cache()<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
    // compute the messages<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
    var messages = g.mapReduceTriplets(sendMsg, mergeMsg)<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
    var activeMessages = messages.count()<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
    // Loop until no messages remain or maxIterations is achieved<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
    var i = 0<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
    while (activeMessages &gt; 0 &amp;&amp; i &lt; maxIterations) {<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
      // Receive the messages: -----------------------------------------------------------------------<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
      // Run the vertex program on all vertices that receive messages<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
      val newVerts = g.vertices.innerJoin(messages)(vprog).cache()<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
      // Merge the new vertex values back into the graph<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
      g = g.outerJoinVertices(newVerts) { (vid, old, newOpt) =&gt; newOpt.getOrElse(old) }.cache()<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
      // Send Messages: ------------------------------------------------------------------------------<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
      // Vertices that didn't receive a message above don't appear in newVerts and therefore don't<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
      // get to send messages.  More precisely the map phase of mapReduceTriplets is only invoked<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
      // on edges in the activeDir of vertices in newVerts<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
      messages = g.mapReduceTriplets(sendMsg, mergeMsg, Some((newVerts, activeDir))).cache()<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
      activeMessages = messages.count()<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;background-color:rgb(255,255,255);color:rgb(51,102,153);">
      i += 1<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
    }<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
    g<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
  }<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
}</li></ol></div>
<span style="margin-left:43px;font-size:12px;color:rgb(51,102,153) !important;">复制代码</span></div>
<br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;"><div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;">
注意，pregel有两个参数列表（graph.pregel(list1)(list2)）。第一个参数列表包含配置参数初始消息、最大迭代数、发送消息的边的方向（默认是沿边方向出）。第二个参数列表包含用户 自定义的函数用来接收消息（vprog）、计算消息（sendMsg）、合并消息（mergeMsg）。</div>
<br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;"><div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;">
我们可以用Pregel操作表达计算单源最短路径( single source shortest path)。</div>
<div class="blockcode" style="overflow:hidden;background-color:rgb(247,247,247);color:rgb(102,102,102);border:1px solid rgb(204,204,204);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;">
<div id="code_RY1">
<ol><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
import org.apache.spark.graphx._<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
// Import random graph generation library<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
import org.apache.spark.graphx.util.GraphGenerators<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
// A graph with edge attributes containing distances<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
val graph: Graph[Int, Double] =<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
  GraphGenerators.logNormalGraph(sc, numVertices = 100).mapEdges(e =&gt; e.attr.toDouble)<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
val sourceId: VertexId = 42 // The ultimate source<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
// Initialize the graph such that all vertices except the root have distance infinity.<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
val initialGraph = graph.mapVertices((id, _) =&gt; if (id == sourceId) 0.0 else Double.PositiveInfinity)<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
val sssp = initialGraph.pregel(Double.PositiveInfinity)(<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
  (id, dist, newDist) =&gt; math.min(dist, newDist), // Vertex Program<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
  triplet =&gt; {  // Send Message<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
    if (triplet.srcAttr + triplet.attr &lt; triplet.dstAttr) {<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
      Iterator((triplet.dstId, triplet.srcAttr + triplet.attr))<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
    } else {<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
      Iterator.empty<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
    }<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
  },<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
  (a,b) =&gt; math.min(a,b) // Merge Message<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;background-color:rgb(255,255,255);color:rgb(51,102,153);">
  )<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
println(sssp.vertices.collect.mkString("\n"))</li></ol></div>
<span style="margin-left:43px;font-size:12px;color:rgb(51,102,153) !important;">复制代码</span></div>
<br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;"><span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;">图构造者</span>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;">
GraphX提供了几种方式从RDD或者磁盘上的顶点和边集合构造图。默认情况下，没有哪个图构造者为图的边重新分区，而是把边保留在默认的分区中（例如HDFS中它们的原始块）。<a href="https://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.graphx.Graph@groupEdges((ED,ED" rel="nofollow" style="color:rgb(51,102,153);">Graph.groupEdges</a>⇒ED):Graph[VD,ED])
 需要重新分区图，因为它假定相同的边将会被分配到同一个分区，所以你必须在调用groupEdges之前调用<a href="https://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.graphx.Graph@partitionBy(PartitionStrategy" rel="nofollow" style="color:rgb(51,102,153);">Graph.partitionBy</a>:Graph[VD,ED])</div>
<div class="blockcode" style="overflow:hidden;background-color:rgb(247,247,247);color:rgb(102,102,102);border:1px solid rgb(204,204,204);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;">
<div id="code_g51">
<ol><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
object GraphLoader {<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
  def edgeListFile(<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
      sc: SparkContext,<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
      path: String,<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
      canonicalOrientation: Boolean = false,<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
      minEdgePartitions: Int = 1)<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
    : Graph[Int, Int]<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
}</li></ol></div>
<span style="margin-left:43px;font-size:12px;color:rgb(51,102,153) !important;">复制代码</span></div>
<br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;"><div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;">
<a href="https://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.graphx.GraphLoader%24@edgeListFile(SparkContext,String,Boolean,Int" rel="nofollow" style="color:rgb(51,102,153);">GraphLoader.edgeListFile</a>:Graph[Int,Int])
 提供了一个方式从磁盘上的边列表中加载一个图。它解析如下形式（源顶点ID，目标顶点ID）的连接表，跳过以#开头的注释行。</div>
<div class="blockcode" style="overflow:hidden;background-color:rgb(247,247,247);color:rgb(102,102,102);border:1px solid rgb(204,204,204);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;">
<div id="code_EzV">
<ol><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
# This is a comment<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
2 1<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
4 1<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
1 2</li></ol></div>
<span style="margin-left:43px;font-size:12px;color:rgb(51,102,153) !important;">复制代码</span></div>
<br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;"><div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;">
它从指定的边创建一个图，自动地创建边提及的所有顶点。所有的顶点和边的属性默认都是1。canonicalOrientation参数允许重定向正方向(srcId &lt; dstId)的边。这在<a href="https://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.graphx.lib.ConnectedComponents%24" rel="nofollow" style="color:rgb(51,102,153);">connected
 components</a> 算法中需要用到。minEdgePartitions参数指定生成的边分区的最少数量。边分区可能比指定的分区更多，例如，一个HDFS文件包含更多的块。</div>
<div class="blockcode" style="overflow:hidden;background-color:rgb(247,247,247);color:rgb(102,102,102);border:1px solid rgb(204,204,204);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;">
<div id="code_d5Q">
<ol><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
object Graph {<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
  def apply[VD, ED](<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
      vertices: RDD[(VertexId, VD)],<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
      edges: RDD[Edge[ED]],<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
      defaultVertexAttr: VD = null)<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
    : Graph[VD, ED]<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
  def fromEdges[VD, ED](<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
      edges: RDD[Edge[ED]],<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
      defaultValue: VD): Graph[VD, ED]<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
  def fromEdgeTuples[VD](<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
      rawEdges: RDD[(VertexId, VertexId)],<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
      defaultValue: VD,<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
      uniqueEdges: Option[PartitionStrategy] = None): Graph[VD, Int]<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
}</li></ol></div>
<span style="margin-left:43px;font-size:12px;color:rgb(51,102,153) !important;">复制代码</span></div>
<br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;"><div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;">
[url=https://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.graphx.Graph$@apply[VD,ED](RDD[(VertexId,VD]Graph.apply[/url]],RDD[Edge[ED]],VD)(ClassTag[VD],ClassTag[ED]):Graph[VD,ED]) 允许从顶点和边的RDD上创建一个图。重复的顶点可以任意的选择其中一个，在边RDD中而不是在顶点RDD中发现的顶点分配默认的属性。</div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;">
[url=https://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.graphx.Graph$@fromEdges[VD,ED](RDD[Edge[ED]],VD]Graph.fromEdges[/url](ClassTag[VD],ClassTag[ED]):Graph[VD,ED]) 允许仅仅从一个边RDD上创建一个图，它自动地创建边提及的顶点，并分配这些顶点默认的值。</div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;">
[url=https://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.graphx.Graph$@fromEdgeTuples[VD](RDD[(VertexId,VertexId]Graph.fromEdgeTuples[/url]],VD,Option[PartitionStrategy])(ClassTag[VD]):Graph[VD,Int]) 允许仅仅从一个边元组组成的RDD上创建一个图。分配给边的值为1。它自动地创建边提及的顶点，并分配这些顶点默认的值。它还支持删除边。为了删除边，需要传递一个<a href="https://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.graphx.PartitionStrategy" rel="nofollow" style="color:rgb(51,102,153);">PartitionStrategy</a> 为值的Some作为uniqueEdges参数（如uniqueEdges
 = Some(PartitionStrategy.RandomVertexCut)）。分配相同的边到同一个分区从而使它们可以被删除，一个分区策略是必须的。</div>
<br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;"><span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;">顶点和边RDDs</span>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;">
GraphX暴露保存在图中的顶点和边的RDD。然而，因为GraphX包含的顶点和边拥有优化的数据结构，这些数据结构提供了额外的功能。顶点和边分别返回VertexRDD和EdgeRDD。这一章 我们将学习它们的一些有用的功能。</div>
<span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;">VertexRDDs</span>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;">
VertexRDD[A]继承自RDD[(VertexID, A)]并且添加了额外的限制，那就是每个VertexID只能出现一次。此外，VertexRDD[A]代表了一组属性类型为A的顶点。在内部，这通过 保存顶点属性到一个可重复使用的hash-map数据结构来获得。所以，如果两个VertexRDDs从相同的基本VertexRDD获得（如通过filter或者mapValues），它们能够在固定的时间内连接 而不需要hash评价。为了利用这个索引数据结构，VertexRDD暴露了一下附加的功能：</div>
<div class="blockcode" style="overflow:hidden;background-color:rgb(247,247,247);color:rgb(102,102,102);border:1px solid rgb(204,204,204);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;">
<div id="code_gXA">
<ol><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
class VertexRDD[VD] extends RDD[(VertexID, VD)] {<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
  // Filter the vertex set but preserves the internal index<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
  def filter(pred: Tuple2[VertexId, VD] =&gt; Boolean): VertexRDD[VD]<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
  // Transform the values without changing the ids (preserves the internal index)<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
  def mapValues[VD2](map: VD =&gt; VD2): VertexRDD[VD2]<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
  def mapValues[VD2](map: (VertexId, VD) =&gt; VD2): VertexRDD[VD2]<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
  // Remove vertices from this set that appear in the other set<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
  def diff(other: VertexRDD[VD]): VertexRDD[VD]<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
  // Join operators that take advantage of the internal indexing to accelerate joins (substantially)<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
  def leftJoin[VD2, VD3](other: RDD[(VertexId, VD2)])(f: (VertexId, VD, Option[VD2]) =&gt; VD3): VertexRDD[VD3]<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
  def innerJoin[U, VD2](other: RDD[(VertexId, U)])(f: (VertexId, VD, U) =&gt; VD2): VertexRDD[VD2]<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
  // Use the index on this RDD to accelerate a `reduceByKey` operation on the input RDD.<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
  def aggregateUsingIndex[VD2](other: RDD[(VertexId, VD2)], reduceFunc: (VD2, VD2) =&gt; VD2): VertexRDD[VD2]<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
}</li></ol></div>
<span style="margin-left:43px;font-size:12px;color:rgb(51,102,153) !important;">复制代码</span></div>
<br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;"><div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;">
举个例子，filter操作如何返回一个VertexRDD。过滤器实际使用一个BitSet实现，因此它能够重用索引以及保留和其它VertexRDDs做连接时速度快的能力。同样的，mapValues操作 不允许map函数改变VertexID，因此可以保证相同的HashMap数据结构能够重用。当连接两个从相同的hashmap获取的VertexRDDs和使用线性扫描而不是昂贵的点查找实现连接操作时，leftJoin 和innerJoin都能够使用。</div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;">
从一个RDD[(VertexID, A)]高效地构建一个新的VertexRDD，aggregateUsingIndex操作是有用的。概念上，如果我通过一组顶点构造了一个VertexRDD[B]，而VertexRDD[B]是 一些RDD[(VertexID, A)]中顶点的超集，那么我们就可以在聚合以及随后索引RDD[(VertexID, A)]中重用索引。例如：</div>
<div class="blockcode" style="overflow:hidden;background-color:rgb(247,247,247);color:rgb(102,102,102);border:1px solid rgb(204,204,204);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;">
<div id="code_Wfr">
<ol><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
val setA: VertexRDD[Int] = VertexRDD(sc.parallelize(0L until 100L).map(id =&gt; (id, 1)))<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
val rddB: RDD[(VertexId, Double)] = sc.parallelize(0L until 100L).flatMap(id =&gt; List((id, 1.0), (id, 2.0)))<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
// There should be 200 entries in rddB<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
rddB.count<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
val setB: VertexRDD[Double] = setA.aggregateUsingIndex(rddB, _ + _)<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
// There should be 100 entries in setB<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
setB.count<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
// Joining A and B should now be fast!<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
val setC: VertexRDD[Double] = setA.innerJoin(setB)((id, a, b) =&gt; a + b)</li></ol></div>
<span style="margin-left:43px;font-size:12px;color:rgb(51,102,153) !important;">复制代码</span></div>
<br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;"><span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;">EdgeRDDs</span>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;">
EdgeRDD[ED]继承自RDD[Edge[ED]]，使用定义在<a href="https://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.graphx.PartitionStrategy" rel="nofollow" style="color:rgb(51,102,153);">PartitionStrategy</a>的 各种分区策略中的一个在块分区中组织边。在每个分区中，边属性和相邻结构被分别保存，当属性值改变时，它们可以最大化的重用。</div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;">
EdgeRDD暴露了三个额外的函数</div>
<div class="blockcode" style="overflow:hidden;background-color:rgb(247,247,247);color:rgb(102,102,102);border:1px solid rgb(204,204,204);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;">
<div id="code_IXw">
<ol><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
// Transform the edge attributes while preserving the structure<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
def mapValues[ED2](f: Edge[ED] =&gt; ED2): EdgeRDD[ED2]<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
// Revere the edges reusing both attributes and structure<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
def reverse: EdgeRDD[ED]<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
// Join two `EdgeRDD`s partitioned using the same partitioning strategy.<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
def innerJoin[ED2, ED3](other: EdgeRDD[ED2])(f: (VertexId, VertexId, ED, ED2) =&gt; ED3): EdgeRDD[ED3]</li></ol></div>
<span style="margin-left:43px;font-size:12px;color:rgb(51,102,153) !important;">复制代码</span></div>
<br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;"><div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;">
在大多数的应用中，我们发现，EdgeRDD操作可以通过图操作者(graph operators)或者定义在基本RDD中的操作来完成。</div>
<span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;">图算法</span>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;">
GraphX包括一组图算法来简化分析任务。这些算法包含在org.apache.spark.graphx.lib包中，可以被直接访问。</div>
<span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;">PageRank算法</span>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;">
PageRank度量一个图中每个顶点的重要程度，假定从u到v的一条边代表v的重要性标签。例如，一个Twitter用户被许多其它人粉，该用户排名很高。GraphX带有静态和动态PageRank的实现方法 ，这些方法在<a href="https://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.graphx.lib.PageRank%24" rel="nofollow" style="color:rgb(51,102,153);">PageRank
 object</a>中。静态的PageRank运行固定次数 的迭代，而动态的PageRank一直运行，直到收敛。<a href="http://endymecy.gitbooks.io/spark-programming-guide-zh-cn/content/graphx-programming-guide/graph-algorithms.html" rel="nofollow" style="color:rgb(51,102,153);">GraphOps</a>允许直接调用这些算法作为图上的方法。</div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;">
GraphX包含一个我们可以运行PageRank的社交网络数据集的例子。用户集在graphx/data/users.txt中，用户之间的关系在graphx/data/followers.txt中。我们通过下面的方法计算 每个用户的PageRank。</div>
<div class="blockcode" style="overflow:hidden;background-color:rgb(247,247,247);color:rgb(102,102,102);border:1px solid rgb(204,204,204);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;">
<div id="code_r75">
<ol><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
// Load the edges as a graph<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
val graph = GraphLoader.edgeListFile(sc, "graphx/data/followers.txt")<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
// Run PageRank<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
val ranks = graph.pageRank(0.0001).vertices<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
// Join the ranks with the usernames<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
val users = sc.textFile("graphx/data/users.txt").map { line =&gt;<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
  val fields = line.split(",")<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
  (fields(0).toLong, fields(1))<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
}<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
val ranksByUsername = users.join(ranks).map {<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
  case (id, (username, rank)) =&gt; (username, rank)<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
}<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
// Print the result<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
println(ranksByUsername.collect().mkString("\n"))</li></ol></div>
<span style="margin-left:43px;font-size:12px;color:rgb(51,102,153) !important;">复制代码</span></div>
<br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;"><span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;">连通体算法</span>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;">
连通体算法用id标注图中每个连通体，将连通体中序号最小的顶点的id作为连通体的id。例如，在社交网络中，连通体可以近似为集群。GraphX在<a href="https://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.graphx.lib.ConnectedComponents%24" rel="nofollow" style="color:rgb(51,102,153);">ConnectedComponents
 object</a> 中包含了一个算法的实现，我们通过下面的方法计算社交网络数据集中的连通体。</div>
<div class="blockcode" style="overflow:hidden;background-color:rgb(247,247,247);color:rgb(102,102,102);border:1px solid rgb(204,204,204);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;">
<div id="code_l66">
<ol><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
/ Load the graph as in the PageRank example<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
val graph = GraphLoader.edgeListFile(sc, "graphx/data/followers.txt")<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
// Find the connected components<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
val cc = graph.connectedComponents().vertices<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
// Join the connected components with the usernames<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
val users = sc.textFile("graphx/data/users.txt").map { line =&gt;<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
  val fields = line.split(",")<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
  (fields(0).toLong, fields(1))<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
}<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
val ccByUsername = users.join(cc).map {<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
  case (id, (username, cc)) =&gt; (username, cc)<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
}<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
// Print the result<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
println(ccByUsername.collect().mkString("\n"))</li></ol></div>
<span style="margin-left:43px;font-size:12px;color:rgb(51,102,153) !important;">复制代码</span></div>
<br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;"><span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;">三角形计数算法</span>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;">
一个顶点有两个相邻的顶点以及相邻顶点之间的边时，这个顶点是一个三角形的一部分。GraphX在TriangleCount object 中实现了一个三角形计数算法，它计算通过每个顶点的三角形的数量。需要注意的是，在计算社交网络数据集的三角形计数时，TriangleCount需要边的方向是规范的方向(srcId &lt; dstId), 并且图通过Graph.partitionBy分片过。</div>
<div class="blockcode" style="overflow:hidden;background-color:rgb(247,247,247);color:rgb(102,102,102);border:1px solid rgb(204,204,204);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;">
<div id="code_HZL">
<ol><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
// Load the edges in canonical order and partition the graph for triangle count<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
val graph = GraphLoader.edgeListFile(sc, "graphx/data/followers.txt", true).partitionBy(PartitionStrategy.RandomVertexCut)<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
// Find the triangle count for each vertex<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
val triCounts = graph.triangleCount().vertices<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
// Join the triangle counts with the usernames<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
val users = sc.textFile("graphx/data/users.txt").map { line =&gt;<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
  val fields = line.split(",")<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
  (fields(0).toLong, fields(1))<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
}<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
val triCountByUsername = users.join(triCounts).map { case (id, (username, tc)) =&gt;<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
  (username, tc)<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
}<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;background-color:rgb(255,255,255);color:rgb(51,102,153);">
<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
// Print the result<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
println(triCountByUsername.collect().mkString("\n"))</li></ol></div>
<span style="margin-left:43px;font-size:12px;color:rgb(51,102,153) !important;">复制代码</span></div>
<br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;"><span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;">例子</span>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;">
假定我们想从一些文本文件中构建一个图，限制这个图包含重要的关系和用户，并且在子图上运行page-rank，最后返回与top用户相关的属性。可以通过如下方式实现.</div>
<div class="blockcode" style="overflow:hidden;background-color:rgb(247,247,247);color:rgb(102,102,102);border:1px solid rgb(204,204,204);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;">
<div id="code_zSF">
<ol><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
// Connect to the Spark cluster<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
val sc = new SparkContext("spark://master.amplab.org", "research")<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
// Load my user data and parse into tuples of user id and attribute list<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
val users = (sc.textFile("graphx/data/users.txt")<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
  .map(line =&gt; line.split(",")).map( parts =&gt; (parts.head.toLong, parts.tail) ))<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
// Parse the edge data which is already in userId -&gt; userId format<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
val followerGraph = GraphLoader.edgeListFile(sc, "graphx/data/followers.txt")<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
// Attach the user attributes<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
val graph = followerGraph.outerJoinVertices(users) {<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
  case (uid, deg, Some(attrList)) =&gt; attrList<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
  // Some users may not have attributes so we set them as empty<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
  case (uid, deg, None) =&gt; Array.empty[String]<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
}<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
// Restrict the graph to users with usernames and names<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
val subgraph = graph.subgraph(vpred = (vid, attr) =&gt; attr.size == 2)<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
// Compute the PageRank<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
val pagerankGraph = subgraph.pageRank(0.001)<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
// Get the attributes of the top pagerank users<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
val userInfoWithPageRank = subgraph.outerJoinVertices(pagerankGraph.vertices) {<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
  case (uid, attrList, Some(pr)) =&gt; (pr, attrList.toList)<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
  case (uid, attrList, None) =&gt; (0.0, attrList.toList)<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
}<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
println(userInfoWithPageRank.vertices.top(5)(Ordering.by(_._2._1)).mkString("\n"))</li></ol></div>
<span style="margin-left:43px;font-size:12px;color:rgb(51,102,153) !important;">复制代码</span></div>
<br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;"><span style="font-size:24px;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;"><span style="font-weight:700;">相关内容：</span></span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;"><span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14.399999618530273px;line-height:16.799999237060547px;"><br>
Spark中文手册1-编程指南<br><a href="http://www.aboutyun.com/thread-11413-1-1.html" rel="nofollow" style="color:rgb(51,102,153);">http://www.aboutyun.com/thread-11413-1-1.html</a><br><br>
Spark中文手册2：Spark之一个快速的例子<br><a href="http://www.aboutyun.com/thread-11484-1-1.html" rel="nofollow" style="color:rgb(51,102,153);">http://www.aboutyun.com/thread-11484-1-1.html</a><br><br>
Spark中文手册3：Spark之基本概念<br><a href="http://www.aboutyun.com/thread-11502-1-1.html" rel="nofollow" style="color:rgb(51,102,153);">http://www.aboutyun.com/thread-11502-1-1.html</a><br><br>
Spark中文手册4：Spark之基本概念（2）<br><a href="http://www.aboutyun.com/thread-11516-1-1.html" rel="nofollow" style="color:rgb(51,102,153);">http://www.aboutyun.com/thread-11516-1-1.html</a><br><br><br>
Spark中文手册5：Spark之基本概念（3）<br><a href="http://www.aboutyun.com/thread-11535-1-1.html" rel="nofollow" style="color:rgb(51,102,153);">http://www.aboutyun.com/thread-11535-1-1.html</a><br><br>
Spark中文手册6：Spark-sql由入门到精通<br><a href="http://www.aboutyun.com/thread-11562-1-1.html" rel="nofollow" style="color:rgb(51,102,153);">http://www.aboutyun.com/thread-11562-1-1.html</a><br><br><br>
Spark中文手册7：Spark-sql由入门到精通【续】<br><a href="http://www.aboutyun.com/thread-11575-1-1.html" rel="nofollow" style="color:rgb(51,102,153);">http://www.aboutyun.com/thread-11575-1-1.html</a><br><br><br>
Spark中文手册8：spark GraphX编程指南（1） <br><a href="http://www.aboutyun.com/thread-11589-1-1.html" rel="nofollow" style="color:rgb(51,102,153);">http://www.aboutyun.com/thread-11589-1-1.html</a><br><br><br><br>
Spark中文手册10：spark部署：提交应用程序及独立部署模式<br><a href="http://www.aboutyun.com/thread-11615-1-1.html" rel="nofollow" style="color:rgb(51,102,153);">http://www.aboutyun.com/thread-11615-1-1.html</a><br><br><br>
Spark中文手册11：Spark 配置指南<br><a href="http://www.aboutyun.com/thread-10652-1-1.html" rel="nofollow" style="color:rgb(51,102,153);">http://www.aboutyun.com/thread-10652-1-1.html</a></span>
            </div>
                </div>