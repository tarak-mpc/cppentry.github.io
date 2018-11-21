---
layout:     post
title:      Hadoop笔记：HDFS读官方文档
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/japson_iot/article/details/80467489				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="大数据笔记hdfs读官方文档">大数据笔记：HDFS读官方文档</h1>

<p>标签： 大数据</p>

<p></p><div class="toc">
<ul>
<li><a href="#%E5%A4%A7%E6%95%B0%E6%8D%AE%E7%AC%94%E8%AE%B0hdfs%E8%AF%BB%E5%AE%98%E6%96%B9%E6%96%87%E6%A1%A3" rel="nofollow">大数据笔记：HDFS读官方文档</a><ul>
<li><a href="#%E4%BB%8B%E7%BB%8D" rel="nofollow">介绍</a></li>
<li><a href="#namenode-and-datanodes" rel="nofollow">NameNode and DataNodes</a><ul>
<li><a href="#hdfs%E6%9E%B6%E6%9E%84%E5%8F%8A%E5%85%B6%E5%86%85%E9%83%A8" rel="nofollow">HDFS架构及其内部</a></li>
<li><a href="#namenode%E5%92%8Cdata%E7%9A%84%E4%BD%9C%E7%94%A8" rel="nofollow">NameNode和Data的作用</a></li>
</ul>
</li>
<li><a href="#%E5%91%BD%E5%90%8D%E7%A9%BA%E9%97%B4%E5%92%8C%E5%89%AF%E6%9C%AC" rel="nofollow">命名空间和副本</a><ul>
<li><a href="#%E5%91%BD%E5%90%8D%E7%A9%BA%E9%97%B4" rel="nofollow">命名空间</a></li>
<li><a href="#%E5%89%AF%E6%9C%AC%E6%9C%BA%E5%88%B6" rel="nofollow">副本机制</a></li>
<li><a href="#%E5%89%AF%E6%9C%AC%E5%AD%98%E6%94%BE%E7%AD%96%E7%95%A5" rel="nofollow">副本存放策略</a></li>
</ul>
</li>
</ul>
</li>
</ul>
</div>


<hr>



<h2 id="介绍">介绍</h2>

<blockquote>
  <p>The Hadoop Distributed File System (HDFS) is a distributed file system designed to run on commodity hardware. It has many similarities with existing distributed file systems. However, the differences from other distributed file systems are significant. HDFS is highly fault-tolerant and is designed to be deployed on low-cost hardware. HDFS provides high throughput access to application data and is suitable for applications that have large data sets. HDFS relaxes a few POSIX requirements to enable streaming access to file system data. HDFS was originally built as infrastructure for the Apache Nutch web search engine project. HDFS is part of the Apache Hadoop Core project. The project URL is <a href="http://hadoop.apache.org/" rel="nofollow">http://hadoop.apache.org/</a>.</p>
</blockquote>

<p>主要说的是：HDFS具有高容错、高可靠性、高扩展性的特点，可以部署在低成本硬件上。</p>

<p>HDFS提供对应用程序数据的高吞吐量访问，适用于具有大型数据集的应用程序。</p>

<p>一般来讲，hadoop：</p>

<ul>
<li>适用于大规模数据吞吐而不是境地数据访问的延时</li>
<li>流式访问数据集（写一次，读多次），不适用于频繁修改文件</li>
<li>HDFS设计更多用于批处理，而不是用户交互式使用。</li>
</ul>



<h2 id="namenode-and-datanodes">NameNode and DataNodes</h2>



<h3 id="hdfs架构及其内部">HDFS架构及其内部</h3>

<blockquote>
  <p>HDFS has a master/slave architecture. </p>
</blockquote>

<p>首先说HDFS架构是：1个master带n个slaves</p>

<blockquote>
  <p>An HDFS cluster consists of a single NameNode, a master server that manages the file system namespace and regulates access to files by clients. In addition, there are a number of DataNodes, usually one per node in the cluster, which manage storage attached to the nodes that they run on. </p>
</blockquote>

<p>一个HDFS集群存在：</p>

<ul>
<li><p>一个single NameNode，master服务器管理着文件系统的namespace并调节客户端访问文件。</p></li>
<li><p>多个DataNode，通常是群集中的每个节点一个DataNode，用于管理连接到它们所运行的节点的存储。</p></li>
</ul>

<blockquote>
  <p>HDFS exposes a file system namespace and allows user data to be stored in files.</p>
</blockquote>

<p>HDFS暴露了一个文件系统的namespace，并允许数据存储到namespace的文件中的</p>

<blockquote>
  <p>Internally, a file is split into one or more blocks and these blocks are stored in a set of DataNodes. </p>
</blockquote>

<p>但是在内部，一个文件是被拆分成多个Block，其大小为128M。这些块分散地存储在一些DataNode中（防止机器挂掉）。</p>

<blockquote>
  <p>A typical deployment has a dedicated machine that runs only the NameNode software. Each of the other machines in the cluster runs one instance of the DataNode software. The architecture does not preclude running multiple DataNodes on the same machine but in a real deployment that is rarely the case.</p>
</blockquote>

<p>典型的部署是有一台只运行NameNode软件的机器。群集中的每台其他机器运行一个DataNode。 <br>
也不排除在同一台机器上运行多个DataNode，但在实际部署中很少出现这种情况。</p>



<h3 id="namenode和data的作用">NameNode和Data的作用</h3>

<p><strong>NameNode：</strong></p>

<blockquote>
  <p>The NameNode executes file system namespace operations like opening, closing, and renaming files and directories. It also determines the mapping of blocks to DataNodes.</p>
</blockquote>

<p>NameNode执行的是文件系统的namspace操作，比如打开、不安比、重命名文件和目录，也决定这DataNode中块的映射。 <br>
主要负责： <br>
1. 客户端请求的响应 <br>
2. 元数据的管理，包括文件的名称、副本系数、Block存放的DataNode的管理</p>

<p><strong>DataNode：</strong></p>

<blockquote>
  <p>The DataNodes are responsible for serving read and write requests from the file system’s clients. The DataNodes also perform block creation, deletion, and replication upon instruction from the NameNode.</p>
</blockquote>

<p>DataNode负责提供来自文件系统客户端的读取和写入请求。 <br>
DataNode还根据来自NameNode的指令执行block的创建，删除和副本的一些操作。 <br>
主要负责： <br>
1. 存储用户的文件对应的数据块 <br>
2. 定期向NameNode发送心跳信息，汇报本身及其所有的block信息，健康状况</p>



<h2 id="命名空间和副本">命名空间和副本</h2>



<h3 id="命名空间">命名空间</h3>

<p>那么我们看看 文件系统的命名空间是怎样的。</p>

<blockquote>
  <p>HDFS supports a traditional hierarchical file organization. A user or an application can create directories and store files inside these directories. </p>
</blockquote>

<p>HDFS支持传统的层级目录结构，用户或者程序可以在一个目录下创建目录或存放文件。</p>

<blockquote>
  <p>The NameNode maintains the file system namespace. Any change to the file system namespace or its properties is recorded by the NameNode. An application can specify the number of replicas of a file that should be maintained by HDFS. The number of copies of a file is called the replication factor of that file. This information is stored by the NameNode.</p>
</blockquote>

<p>NameNode维持一个文件系统命名空间，任何文件系统命名空间或属性的改动，都会被NomeNode记录。一个应用程序可以指定HDFS维持的副本文件的数量。这个数量成为该文件的副本因子。这个信息是存在NameNode上的。</p>



<h3 id="副本机制">副本机制</h3>

<p>我们知道了，副本因子就是HDFS维持的副本文件的数量，那么为什么有副本因子?</p>

<blockquote>
  <p>HDFS is designed to reliably store very large files across machines in a large cluster. It stores each file as a sequence of blocks. The blocks of a file are replicated for fault tolerance. The block size and replication factor are configurable per file.</p>
</blockquote>

<p>HDFS被设计成可以在大集群上跨机器可靠存储大量文件。它将每个文件存储为一系列的块。</p>

<blockquote>
  <p>All blocks in a file except the last block are the same size</p>
</blockquote>

<p>文件中除最后一个块之外的所有块都具有相同的大小。</p>

<blockquote>
  <p>An application can specify the number of replicas of a file. The replication factor can be specified at file creation time and can be changed later. Files in HDFS are write-once (except for appends and truncates) and have strictly one writer at any time.</p>
</blockquote>

<p>应用程序可以指定文件中副本的数量，副本因子可以在文件创建时被指定，之后也可以被更改。文件在HDFS中只能被写一次，并且严格限定只写一次。</p>

<blockquote>
  <p>The NameNode makes all decisions regarding replication of blocks. It periodically receives a Heartbeat and a Blockreport from each of the DataNodes in the cluster. Receipt of a Heartbeat implies that the DataNode is functioning properly. A Blockreport contains a list of all blocks on a DataNode.</p>
</blockquote>

<p>NameNode做出关于副本block的所有的决定。定期获取每个DataNode的心跳和Blockreport。回复心跳意味着DataNode在正常工作，Blockreport包含DtaNode中所有块的列表。</p>



<h3 id="副本存放策略">副本存放策略</h3>

<p>如何为每个block副本选择存放的DataNode呢？</p>

<p>副本存放策略是： <br>
1. 对于第一个副本，放在当前操作的客户端上 <br>
2. 第二个副本与第一个副本不再同一个机架上 <br>
3. 第三个副本放在与第一个副本相同机架的不同节点上</p>

<p>防止某一个机架挂掉。</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>