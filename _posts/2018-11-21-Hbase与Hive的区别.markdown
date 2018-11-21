---
layout:     post
title:      Hbase与Hive的区别
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/u012834750/article/details/80856249				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>一、HBase与Hive的对比</p>

<p>Hive和Hbase有各自不同的特征：hive是高延迟、结构化和面向分析的，hbase是低延迟、非结构化和面向编程的。Hive数据仓库在hadoop上是高延迟的。 </p>

<p>其中HBase位于结构化存储层，Hadoop HDFS为HBase提供了高可靠性的底层存储支持，Hadoop MapReduce为HBase提供了高性能的计算能力，Zookeeper为HBase提供了稳定服务和failover机制。 </p>

<p><strong>通过hive的存储接口，hive和Hbase可以整合使用。</strong> </p>

<ul>
<li><p>1、hive是sql语言，通过数据库的方式来操作hdfs文件系统，为了简化编程，底层计算方式为mapreduce。 </p></li>
<li><p>2、hive是面向行存储的数据库。 </p></li>
<li><p>3、Hive本身不存储和计算数据，它完全依赖于HDFS和MapReduce，Hive中的表纯逻辑。 </p></li>
<li><p>4、HBase为查询而生的，它通过组织起节点內所有机器的內存，提供一個超大的內存Hash表 。</p></li>
<li><p>5、hbase不是关系型数据库，而是一个在hdfs上开发的面向列的分布式数据库，不支持sql。 </p></li>
<li><p>6、hbase是物理表，不是逻辑表，提供一个超大的内存hash表，搜索引擎通过它来存储索引，方便查询操作。 </p></li>
<li><p>7、hbase是列存储。 </p></li>
</ul>

<p><strong>1) Hive</strong> <br>
(1) 数据仓库</p>

<blockquote>
  <p>Hive的本质其实就相当于将HDFS中已经存储的文件在Mysql中做了一个双射关系，以方便使用HQL去管理查询。</p>
</blockquote>

<p>(2) 用于数据分析、清洗</p>

<blockquote>
  <p>Hive适用于离线的数据分析和清洗，延迟较高。</p>
</blockquote>

<p>(3) 基于HDFS、MapReduce</p>

<blockquote>
  <p>Hive存储的数据依旧在DataNode上，编写的HQL语句终将是转换为MapReduce代码执行。</p>
</blockquote>

<p><strong>2) HBase</strong> <br>
(1) 数据库</p>

<blockquote>
  <p>是一种面向列存储的非关系型数据库。</p>
</blockquote>

<p>(2) 用于存储结构化和非结构话的数据</p>

<blockquote>
  <p>适用于单表非关系型数据的存储，不适合做关联查询，类似JOIN等操作。</p>
</blockquote>

<p>(3) 基于HDFS</p>

<blockquote>
  <p>数据持久化存储的体现形式是Hfile，存放于DataNode中，被ResionServer以region的形式进行管理。</p>
</blockquote>

<p>(4) 延迟较低，接入在线业务使用</p>

<blockquote>
  <p>面对大量的企业数据，HBase可以直线单表大量数据的存储，同时提供了高效的数据访问速度。</p>
</blockquote>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>