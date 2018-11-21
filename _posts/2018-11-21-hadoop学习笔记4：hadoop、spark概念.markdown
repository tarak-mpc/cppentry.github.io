---
layout:     post
title:      hadoop学习笔记4：hadoop、spark概念
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/yhhyhhyhhyhh/article/details/60346815				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="hadoop学习笔记4hadoopspark概念">hadoop学习笔记4：hadoop、spark概念</h1>

<p></p><div class="toc"><div class="toc">
<ul>
<li><a href="#hadoop%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B04hadoopspark%E6%A6%82%E5%BF%B5" rel="nofollow">hadoop学习笔记4hadoopspark概念</a><ul>
<li><a href="#1hadoop" rel="nofollow">hadoop</a><ul>
<li><a href="#11hdfs" rel="nofollow">1HDFS</a></li>
<li><a href="#12mapreduce" rel="nofollow">2MapReduce</a></li>
<li><a href="#13yarn" rel="nofollow">3YARN</a></li>
</ul>
</li>
<li><a href="#2spark" rel="nofollow">spark</a></li>
</ul>
</li>
</ul>
</div>
</div>




<h2 id="1hadoop"><strong>1.hadoop</strong></h2>

<blockquote>
  <p>Hadoop是一种开源的适合大数据的分布式存储和处理的平台。</p>
  
  <p>Hadoop有三种不同的模式操作，分别为单机模式、<a href="http://blog.csdn.net/yhhyhhyhhyhh/article/details/53945810" rel="nofollow">伪分布模式</a>和<a href="http://blog.csdn.net/yhhyhhyhhyhh/article/details/57415890" rel="nofollow">完全分布模</a>。</p>
  
  <p>Hadoop的核心是HDFS、Mapreduce和YARN（hadoop2.0以上）</p>
</blockquote>



<h3 id="11hdfs">1.1HDFS</h3>

<p><img src="https://img-blog.csdn.net/20170304205554067?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveWhoeWhoeWhoeWho/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p>主要特点：</p>

<blockquote>
  <p>保存多个副本，且提供容错机制，副本丢失或宕机自动恢复。默认存3份。 <br>
  运行在廉价的机器上。适合大数据的处理。HDFS默认会将文件分割成block，64M为1个block。然后将block按键值对存储在HDFS上，并将键值对的映射存到内存中。如果小文件太多，那内存的负担会很重。</p>
</blockquote>

<ul>
<li><p><strong>Client</strong>：切分文件；访问HDFS；与NameNode交互，获取文件位置信息；与DataNode交互，读取和写入数据。</p></li>
<li><p><strong>NameNode</strong>：Master节点，在hadoop1.X中只有一个，管理HDFS的名称空间和数据块映射信息，配置副本策略，处理客户端请求。是HDFS里面的管理者，发挥者管理、协调、操控的作用</p></li>
<li><p><strong>DataNode</strong>：Slave节点，存储实际的数据，汇报存储信息给NameNode。是HDFS的工作者，发挥按着Namenode的命令干活，并且把干活的进展和问题反馈到Namenode的作用。</p></li>
<li><p><strong>Secondary NameNode：</strong>辅助NameNode，分担其工作量；定期合并fsimage和fsedits，推送给NameNode；紧急情况下，可辅助恢复NameNode，但Secondary NameNode并非NameNode的热备。</p></li>
</ul>

<h3 id="12mapreduce">1.2MapReduce</h3>

<p><img src="https://img-blog.csdn.net/20170304205613911?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveWhoeWhoeWhoeWho/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<blockquote>
  <p>MapReduce是一种简化并行 计算的编程模型，思想就是“<strong>分而治之</strong>”。</p>
  
  <p>（1）Mapper负责“分”，即把复杂的任务分解为若干个“简单的任务”来处理。“简单的任务”包含三层含义：一是数据或计算的规模相对原任 务要大大缩小；二是就近计算原则，即任务会分配到存放着所需数据的节点上进行计算；三是这些小任务可以并行计算，彼此间几乎没有依赖关系。</p>
  
  <p>（2）Reducer负责对map阶段的结果进行汇总。至于需要多少个Reducer，用户可以根据具体问题，通过在mapred-site.xml配置文件里设置参数mapred.reduce.tasks的值，缺省值为1。</p>
</blockquote>

<ul>
<li><strong>JobTracker</strong>：Master节点，只有一个，管理所有作业，作业/任务的监控、错误处理等；将任务分解成一系列任务，并分派给TaskTracker。</li>
<li><strong>TaskTracker</strong>：Slave节点，运行Map Task和Reduce Task；并与JobTracker交互，汇报任务状态。</li>
<li><p><strong>Map Task</strong>：解析每条数据记录，传递给用户编写的map(),并执行，将输出结果写入本地磁盘(如果为map-only作业，直接写入HDFS)。</p></li>
<li><p><strong>Reducer Task</strong>：从Map Task的执行结果中，远程读取输入数据，对数据进行排序，将数据按照分组传递给用户编写的reduce函数执行。</p></li>
</ul>

<h3 id="13yarn">1.3YARN</h3>

<blockquote>
  <p>YARN总体上采用master/slave架构；YARN将资源管理和处理组件分开，对集群中的资源进行统一管理和调度，大大提高了集群的资源利用率并降低了集群管理成本。YARN作为一个通用数据操作系统，既可以运行像MapReduce、Spark这样的短作业，也可以部署像Web Server、MySQL Server这种长服务，真正实现一个集群多用途。</p>
</blockquote>

<ul>
<li><strong>ResourceManager</strong>：Master节点，ResourceManager（代替集群管理器）负责对各个NodeManager上的资源进行统一管理和调度。当用户提交一个应用程序时，需要提供一个用以跟踪和管理这个程序的ApplicationMaster，它负责向ResourceManager申请资源，并要求NodeManger启动可以占用一定资源的Container</li>
<li><p><strong>NodeManager</strong>：Slave节点，（ 代替 TaskTracker）</p></li>
<li><p><strong>ApplicationMaster</strong> 代替一个专用且短暂的 JobTracker</p></li>
<li><strong>一个分布式应用</strong>程序代替一个 MapReduce 作业</li>
</ul>

<h2 id="2spark">2.spark</h2>

<p><img src="https://img-blog.csdn.net/20170304205703229?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveWhoeWhoeWhoeWho/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p><a href="http://lib.csdn.net/base/spark" rel="nofollow">Spark</a>是一个计算框架(内存集群计算)，是MapReduce的替代方案，专注于计算，而不承担数据存储功能。</p>

<ul>
<li>Spark 启用了内存分布数据集，除了能够提供交互式查询外，它还可以优化迭代工作负载。</li>
<li>Job中间输出结果可以保存在内存中，从而不再需要读写HDFS，因此Spark能更好地适用于数据挖掘与机器学习等需要迭代的map reduce的算法。</li>
<li>Spark 是为了支持分布式数据集上的迭代作业，但是实际上它是对 Hadoop 的补充，可以在 Hadoop 文件系统中并行运行(目前部署Spark的企业，其实都在现有的Hadoop集群中运行Spark)</li>
</ul>

<p>参考文献：</p>

<p>YARN 简介：<a href="http://www.ibm.com/developerworks/cn/data/library/bd-yarn-intro/" rel="nofollow">http://www.ibm.com/developerworks/cn/data/library/bd-yarn-intro/</a> <br>
<a href="http://www.36dsj.com/archives/23504" rel="nofollow">一文看懂大数据的技术生态圈，Hadoop，hive，spark都有了</a> <br>
<a href="http://hadoop.apache.org/docs/r1.0.4/cn/hdfs_design.html" rel="nofollow">http://hadoop.apache.org/docs/r1.0.4/cn/hdfs_design.html</a></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>