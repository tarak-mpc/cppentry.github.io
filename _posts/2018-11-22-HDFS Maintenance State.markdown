---
layout:     post
title:      HDFS Maintenance State
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/Androidlushangderen/article/details/54731243				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="前言">前言</h1>

<hr>

<p>初一看文章标题，很多人可能比较奇怪“HDFS Maintenance”是什么意思，“HDFS包含”的意思？首先Maintenance这个形似Maintain的单词可不是什么包含的意思，它的解释是维护，维修。那么HDFS Maintenance具体是什么意思呢，HDFS处于维护状态？说起维护状态，我们不禁可以联想到HDFS RollingUpgrad，没错，RollingUpgrade确实与HDFS的维护有点关联。确切地说，HDFS的RollingUpgrade是HDFS Maintenance功能的一个使用场景。下面给大家介绍一下社区目前在开发的这个feature。</p>

<h1 id="hdfs-maintenance的缘来">HDFS Maintenance的缘来</h1>

<hr>

<p>HDFS Maintenance的提出源自于HDFS Upgrade Domain（升级域）的提出。HDFS升级域是方便于集群做大规模滚动升级之类的操作而提出的HDFS升级域的相关内容可查阅笔者的另外一篇文章：<a href="http://blog.csdn.net/androidlushangderen/article/details/52901974" rel="nofollow">HDFS升级域：Upgrade Domain</a>。在HDFS升级域的讨论与实现过程中，社区提出了DataNode的Maintenance（维护状态）。Maintenance状态是一类新的状态,HDFS现有的状态有以下几种，看完大家可能就知道这是什么意思了。</p>

<ul>
<li>Live（In Sevice）</li>
<li>Decommissioning</li>
<li>Decommissioned</li>
<li>Dead</li>
</ul>

<p>这些状态我们在NameNode的WebUI界面上也能直接看到。那么问题来了，上面4种是否是完美的呢？答案显然不是，如果我们仔细进行分析的话，上面状态的划分不够灵活。在这样的情况下，DataNode不是处于服务状态，就是处于Out of Service状态。而一旦处于Out of Service状态，接下来就会导致replication操作，使集群副本数满足副本系数的要求。所以这里会引出一个问题，我们是否能够定义一个新的状态，使得我们对DataNode的操作能尽可能地对集群没有影响，并能够快速的恢复回去呢？这就是DataNode Maintenance状态的缘由。</p>

<h1 id="datanode-maintenance状态的定义">DataNode Maintenance状态的定义</h1>

<hr>

<p>与Decommission下线状态类似，Maintenance状态同样有2个细分状态，Enter_Maintenance和In_Maintenance状态。Enter_Maintenance表示的意思是正在进入Maintenance状态。而In_Maintenance表示目前已为Maintenance状态。为什么这里还会有进入Maintenance这样的状态呢？其实大家可以拿Decommission过程与此过程做一个比较。我们在执行下线操作时，为了满足集群副本数的要求，会有一个Decommissioning的状态，此时集群会做replication操作。同理，Enter_Maintenance状态也会做这样的事情，但是它与Decommission过程的目的略有不同。Enter_Maintenance是为了保证集群内有至少满足1个副本以上的数据副本，来保证数据的可用性。比如说当进入Maintenance状态的节点拥有集群中唯一的某块副本时，这时就会做一次replication操作。从一定程度上而言，Maintenance情况下需要满足的最低副本数应比Decommission过程要低，理论上只要存在一个即可。所以如果我们集群都是默认3副本的情况时，那么进入Maintenance状态，基本不会有replication操作了。</p>

<p>如果DataNode处于Maintenance状态了，HDFS对DataNode的节点是怎样处理的呢？主要有以下几点主要影响：</p>

<ul>
<li>集群内不会触发replicate此节点内的块数据。</li>
<li>DataNode将不接收来自DFSClient端的读写请求。以往DataNode如果处于Out of Service的状态，就会抛出很多的异常。</li>
<li>DataNode将不会被选为block的存储节点。</li>
<li>Balancer/Mover操作也将避免移动此节点上的数据。</li>
</ul>

<p>所以从这里我们可以看到，Maintenance状态的DataNode就是处于一个“维护”的状态，它并不等同于Dead、Decommissioned状态，它能够主动地让NameNode知道自己的状态，并能够随时快速的切换回服务状态。</p>

<h1 id="hdfs-maintenance功能的使用场景">HDFS Maintenance功能的使用场景</h1>

<hr>

<p>介绍完Maintenance状态的定义之后，我们再来看看它的使用场景。</p>

<p>首先HDFS Maintenance的提出源自于HDFS 升级域的讨论。所以毫无疑问，在滚动升级的过程中,HDFS Maintenance可以起到很大的帮助。这样的话，当每次进行批量rolling upgrade操作的时候，就能够快速进行服务状态的切换，而不用担心操作时间过长导致集群进行replication操作了。</p>

<p>第二个使用场景是DataNode服务的快速切换。在很多情况下，当节点本身出现软件或者硬件层面的问题时，这时我们可能会对其进行临时的停止操作来进行软、硬件的故障恢复操作。这个时候我们就可以使DataNode进入Maintenance状态。在这里，我们可以把DataNode Maintenance理解为DataNode背后的一道开关。</p>

<h1 id="hdfs-maintenance的核心实现">HDFS Maintenance的核心实现</h1>

<hr>

<p>在HDFS-7877上，原作者提交了此功能的patch。笔者对其也进行了学习，给我的感觉是HDFS Maintenance的引入好比在各个操作方法入口上加了一道开关。这里的开关可以具象的理解为if(datanode.inService())的判断。主要在下面几个场景代码中做了处理。</p>

<p>第一处，在调用BlockManager#createLocatedBlock获取块信息的时候做了处理，这步操作会在每次的读请求中被调用。代码改动如下：</p>

<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">private</span> LocatedBlock <span class="hljs-title">createLocatedBlock</span>(<span class="hljs-keyword">final</span> BlockInfoContiguous blk, <span class="hljs-keyword">final</span> lon

     <span class="hljs-keyword">final</span> <span class="hljs-keyword">int</span> numNodes = blocksMap.numNodes(blk);
     <span class="hljs-keyword">final</span> <span class="hljs-keyword">boolean</span> isCorrupt = numCorruptNodes == numNodes;
-    <span class="hljs-keyword">final</span> <span class="hljs-keyword">int</span> numMachines = isCorrupt ? numNodes: numNodes - numCorruptNodes;
+    <span class="hljs-keyword">int</span> numMachines = isCorrupt ? numNodes: numNodes - numCorruptNodes;
+    numMachines -= numberReplicas.inMaintenanceReplicas() +
+        numberReplicas.deadEnteringMaintenanceReplicas();
     <span class="hljs-keyword">final</span> DatanodeStorageInfo[] machines = <span class="hljs-keyword">new</span> DatanodeStorageInfo[numMachines];
     <span class="hljs-keyword">int</span> j = <span class="hljs-number">0</span>;
     <span class="hljs-keyword">if</span> (numMachines &gt; <span class="hljs-number">0</span>) {
       <span class="hljs-keyword">for</span>(DatanodeStorageInfo storage : blocksMap.getStorages(blk)) {
         <span class="hljs-keyword">final</span> DatanodeDescriptor d = storage.getDatanodeDescriptor();
         <span class="hljs-keyword">final</span> <span class="hljs-keyword">boolean</span> replicaCorrupt = corruptReplicas.isReplicaCorrupt(blk, d);
+        <span class="hljs-comment">// Don't pick IN_MAINTENANCE or dead ENTERING_MAINTENANCE states.</span>
+        <span class="hljs-keyword">if</span> (d.isInMaintenance() || (d.isEnteringMaintenance() &amp;&amp; !d.isAlive)) {
+          <span class="hljs-keyword">continue</span>;
+        }</code></pre>

<p>第二处，在Rereplication过程和普通写请求中的选出目标放置节点的方法过程中，进行了过滤处理，主要集中在下面2个方法。</p>



<pre class="prettyprint"><code class=" hljs cs">DatanodeDescriptor chooseSourceDatanode(Block block,
         corrupt += countableReplica;
       <span class="hljs-keyword">else</span> <span class="hljs-keyword">if</span> (node.isDecommissionInProgress() || node.isDecommissioned())
         decommissioned += countableReplica;
+      <span class="hljs-keyword">else</span> <span class="hljs-keyword">if</span> (node.isEnteringMaintenance()) {
+        enteringMaintenance += countableReplica;
+        <span class="hljs-keyword">if</span> (!node.isAlive) {
+          deadEnteringMaintenance += countableReplica;
+        }
+      } <span class="hljs-keyword">else</span> <span class="hljs-keyword">if</span> (node.isInMaintenance())
+        inMaintenance += countableReplica;
       <span class="hljs-keyword">else</span> <span class="hljs-keyword">if</span> (excessBlocks != <span class="hljs-keyword">null</span> &amp;&amp; excessBlocks.contains(block)) {
         excess += countableReplica;
       } <span class="hljs-keyword">else</span> {
@@ -<span class="hljs-number">1638</span>,<span class="hljs-number">7</span> +<span class="hljs-number">1682</span>,<span class="hljs-number">7</span> @@ <span class="hljs-keyword">else</span> <span class="hljs-keyword">if</span> (excessBlocks != <span class="hljs-keyword">null</span> &amp;&amp; excessBlocks.contains(block)) {
       <span class="hljs-keyword">if</span> ((nodesCorrupt != <span class="hljs-keyword">null</span>) &amp;&amp; nodesCorrupt.contains(node))
         <span class="hljs-keyword">continue</span>;
       <span class="hljs-keyword">if</span>(priority != UnderReplicatedBlocks.QUEUE_HIGHEST_PRIORITY 
-          &amp;&amp; !node.isDecommissionInProgress() 
+          &amp;&amp; !node.isDecommissionInProgress() &amp;&amp; !node.isEnteringMaintenance()
           &amp;&amp; node.getNumberOfBlocksToBeReplicated() &gt;= maxReplicationStreams)
       {</code></pre>

<p>下面是选择副本放置位置的时候，做了目标存储节点类别的判断。</p>



<pre class="prettyprint"><code class=" hljs java">@@ -<span class="hljs-number">764</span>,<span class="hljs-number">9</span> +<span class="hljs-number">764</span>,<span class="hljs-number">9</span> @@ <span class="hljs-keyword">private</span> <span class="hljs-keyword">boolean</span> <span class="hljs-title">isGoodTarget</span>(DatanodeStorageInfo storage,
     }

     DatanodeDescriptor node = storage.getDatanodeDescriptor();
-    <span class="hljs-comment">// check if the node is (being) decommissioned</span>
-    <span class="hljs-keyword">if</span> (node.isDecommissionInProgress() || node.isDecommissioned()) {
-      logNodeIsNotChosen(storage, <span class="hljs-string">"the node is (being) decommissioned "</span>);
+    <span class="hljs-comment">// check if the node is in service</span>
+    <span class="hljs-keyword">if</span> (!node.isInService()) {
+      logNodeIsNotChosen(storage, <span class="hljs-string">"the node isn't in service"</span>);
       <span class="hljs-keyword">return</span> <span class="hljs-keyword">false</span>;
     }</code></pre>

<p>第三处，Balancer/Mover操作的时候，需要过滤掉处于Maintenance状态的节点。</p>

<p>由于Balancer/Mover工具在运行的时候，都会调用Dispatcher init方法来执行初始化节点的操作，来过滤掉exclude，decommissioned这类的节点。所以我们在这里只需多加一类判断即可。</p>

<p>首先是Dispatcher的init初始化方法。</p>



<pre class="prettyprint"><code class=" hljs java">  <span class="hljs-javadoc">/** Get live datanode storage reports and then build the network topology. */</span>
  <span class="hljs-keyword">public</span> List&lt;DatanodeStorageReport&gt; <span class="hljs-title">init</span>() <span class="hljs-keyword">throws</span> IOException {
    <span class="hljs-keyword">final</span> DatanodeStorageReport[] reports = nnc.getLiveDatanodeStorageReport();
    <span class="hljs-keyword">final</span> List&lt;DatanodeStorageReport&gt; trimmed = <span class="hljs-keyword">new</span> ArrayList&lt;DatanodeStorageReport&gt;(); 
    <span class="hljs-comment">// create network topology and classify utilization collections:</span>
    <span class="hljs-comment">// over-utilized, above-average, below-average and under-utilized.</span>
    <span class="hljs-keyword">for</span> (DatanodeStorageReport r : DFSUtil.shuffle(reports)) {
      <span class="hljs-keyword">final</span> DatanodeInfo datanode = r.getDatanodeInfo();
      <span class="hljs-comment">// 此处判断节点是否需要过滤</span>
      <span class="hljs-keyword">if</span> (shouldIgnore(datanode)) {
        <span class="hljs-keyword">continue</span>;
      }
      trimmed.add(r);
      cluster.add(datanode);
    }
    <span class="hljs-keyword">return</span> trimmed;
  }</code></pre>

<p>然后我们进入shouldIgnore方法。</p>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">private</span> <span class="hljs-keyword">boolean</span> <span class="hljs-title">shouldIgnore</span>(DatanodeInfo dn) {
     <span class="hljs-keyword">final</span> <span class="hljs-keyword">boolean</span> decommissioned = dn.isDecommissioned();
     <span class="hljs-comment">// ignore decommissioning nodes</span>
     <span class="hljs-keyword">final</span> <span class="hljs-keyword">boolean</span> decommissioning = dn.isDecommissionInProgress();
+    <span class="hljs-comment">// ignore nodes in maintenance</span>
+    <span class="hljs-keyword">final</span> <span class="hljs-keyword">boolean</span> inMaintenance = dn.isInMaintenance();
+    <span class="hljs-comment">// ignore nodes entering maintenance</span>
+    <span class="hljs-keyword">final</span> <span class="hljs-keyword">boolean</span> enteringMaintenance = dn.isEnteringMaintenance();
     <span class="hljs-comment">// ignore nodes in exclude list</span>
     <span class="hljs-keyword">final</span> <span class="hljs-keyword">boolean</span> excluded = Util.isExcluded(excludedNodes, dn);
     <span class="hljs-comment">// ignore nodes not in the include list (if include list is not empty)</span>
     <span class="hljs-keyword">final</span> <span class="hljs-keyword">boolean</span> notIncluded = !Util.isIncluded(includedNodes, dn);

-    <span class="hljs-keyword">if</span> (decommissioned || decommissioning || excluded || notIncluded) {
+    <span class="hljs-keyword">if</span> (decommissioned || decommissioning || excluded || notIncluded ||
+        inMaintenance || enteringMaintenance) {
       <span class="hljs-keyword">if</span> (LOG.isTraceEnabled()) {
         LOG.trace(<span class="hljs-string">"Excluding datanode "</span> + dn + <span class="hljs-string">": "</span> + decommissioned + <span class="hljs-string">", "</span>
-            + decommissioning + <span class="hljs-string">", "</span> + excluded + <span class="hljs-string">", "</span> + notIncluded);
+            + decommissioning + <span class="hljs-string">", "</span> + excluded + <span class="hljs-string">", "</span> + notIncluded + <span class="hljs-string">", "</span>
+            + inMaintenance + <span class="hljs-string">", "</span> + enteringMaintenance);
       }
       <span class="hljs-keyword">return</span> <span class="hljs-keyword">true</span>;
     }</code></pre>

<p>从以上3处的处理我们可以看到，Maintenance状态的处理与原有的Decommission状态的处理极为相似。当然上述只是笔者简单分析了几处，更多的实现还是建议读者自行阅读原patch代码，上面还有Enter_Maintenance状态时进行最小副本数的判断逻辑。</p>

<h1 id="hdfs-maintenance状态转化图">HDFS Maintenance状态转化图</h1>

<hr>

<p>DataNode Maintenance状态同样支持多种状态之间的转换，下面是原设计中的状态转化图。</p>

<p></p><center> <br>
<img src="https://img-blog.csdn.net/20170125222630572?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQW5kcm9pZGx1c2hhbmdkZXJlbg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" width="50%"> <br>
 <br>
HDFS Maintenance状态转换图 <br>
</center><p></p>

<p>本文的内容就是如此，希望大家读完后有所收获。HDFS Maintenance功能预计发布在Hadoop 2.9.0的版本中，目前还在开发中。笔者目前也在关注这个Feature，希望也能贡献自己的一份力吧。</p>

<h1 id="参考资料">参考资料</h1>

<hr>

<p>[1].<a href="https://issues.apache.org/jira/browse/HDFS-7877" rel="nofollow">https://issues.apache.org/jira/browse/HDFS-7877</a> <br>
[2].<a href="https://issues.apache.org/jira/secure/attachment/12709388/Supportmaintenancestatefordatanodes-2.pdf" rel="nofollow">https://issues.apache.org/jira/secure/attachment/12709388/Supportmaintenancestatefordatanodes-2.pdf</a> <br>
[3].<a href="https://issues.apache.org/jira/secure/attachment/12709387/HDFS-7877-2.patch" rel="nofollow">https://issues.apache.org/jira/secure/attachment/12709387/HDFS-7877-2.patch</a></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>