---
layout:     post
title:      Hadoop基础组件之一：HDFS
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/sunshine_lzy/article/details/80747980				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="hdfs">HDFS</h1>

<p>Hadoop2.0下包含了很多子模块，如下图，其中，比较核心的为：HDFS、YARN、MapReduce及一些其他工具。 <br>
<img src="https://img-blog.csdn.net/20180620143649916?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3N1bnNoaW5lX2x6eQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="Hadoop生态系统" title=""></p>

<h2 id="hdfs简介">HDFS简介</h2>

<p><strong>HDFS，分布式文件系统</strong>，是运行在通用硬件上的高容错分布式文件系统，能提供高吞吐量的数据访问，适合存储大量数据。HDFS的优势：</p>

<ul>
<li>安全 <br>
 数据在HDFS中保存，通常会备份多份，即使其中一台机器宕机，也不会影响数据的读取。</li>
<li>可扩展 <br>
 HDFS系统中，当出现硬件不够或出现故障时，可以通过增加节点的形式来扩充HDFS容量。此方法方便简单。</li>
<li>廉价</li>
</ul>



<h2 id="hdfs体系架构">HDFS体系架构</h2>

<p><img src="https://img-blog.csdn.net/20180620161711514?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3N1bnNoaW5lX2x6eQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="HDFS结构图" title=""> <br>
HDFS是一个Master/Slave体系结构，一个HDFS系统由一个NameNode和多个DataNode组成。NameNode主要用来管理名字空间及映射数据在DataNode上的位置。DataNode主要用来保存数据。一个文件被分割为多个块，每个块保存在不同的DataNode上，每一个块的默认大小为128M。 <br>
客户端首先与NameNode进行交互，通过NameNode找到文件对应的DataNode，然后通过IO直接与DataNode进行交互获取数据。 <br>
<strong>NameNode</strong></p>

<ul>
<li>操作名字空间（重命名等）</li>
<li>映射数据块到具体的DataNode</li>
</ul>

<p>NameNode的名字空间以及与DataNode的映射，也会在磁盘中保存下来，分别为：命名空间镜像（image）及操作日志（editlog）。文件数据块分布在哪个节点上，是启动HDFS时，DataNode主动向NameNode进行汇报，而不需要让NameNode记录下来。</p>

<p><strong>DataNode</strong></p>

<ul>
<li>数据块存储</li>
<li>与客户端进行IO操作</li>
</ul>



<h2 id="hdfs数据复制">HDFS数据复制</h2>

<p>HDFS之所以可靠，是由于它会将数据进行复制，确保数据不会丢失。一个文件，会被划分为多个数据块，每个数据块的默认大小为128M。这些数据块分布在不同的DataNode上，并且，会留下副本。副本的位置，是由机架感知的策略来实现的。即保证一份数据即安全又存取高效。因此，HDFS的方式是，将原数据保存在当前机器上，然后再复制一个副本保存在同一机架下的其他节点上。最后一份数据保存在不同机架下的一个节点中。</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>