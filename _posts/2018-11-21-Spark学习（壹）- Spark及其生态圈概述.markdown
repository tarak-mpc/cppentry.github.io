---
layout:     post
title:      Spark学习（壹）- Spark及其生态圈概述
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/bingdianone/article/details/84105561				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p></p><div class="toc"><h3>文章目录</h3><ul><li><a href="#Spark_1" rel="nofollow">Spark概述及特点</a></li><li><a href="#Spark_16" rel="nofollow">Spark产生背景</a></li><li><a href="#Spark_27" rel="nofollow">Spark发展历史</a></li><li><a href="#SparkHadoop_31" rel="nofollow">Spark对比Hadoop</a></li><ul><li><a href="#Hadoop_32" rel="nofollow">Hadoop生态系统</a></li><li><a href="#Spark_BDASBDASBerkeley_Data_Analytics_Stack_34" rel="nofollow">Spark生态系统 BDAS（BDAS:Berkeley Data Analytics Stack）</a></li><li><a href="#HadoopSpark_36" rel="nofollow">Hadoop和Spark生态圈对比：</a></li><li><a href="#HadoopSpark_47" rel="nofollow">Hadoop对比Spark：</a></li><li><a href="#MapReduceSpark_57" rel="nofollow">MapReduce对比Spark</a></li></ul><li><a href="#SparkHadoop_62" rel="nofollow">Spark和Hadoop的协作性</a></li><ul><li><a href="#Hadoop_63" rel="nofollow">Hadoop优势</a></li><li><a href="#Spark_77" rel="nofollow">Spark的优势</a></li><li><a href="#HadoopSpark_82" rel="nofollow">Hadoop+Spark（相辅相成）</a></li></ul></ul></div><p></p>
<h1><a id="Spark_1"></a>Spark概述及特点</h1>
<p>Apache Spark™是一个统一的分析引擎进行大规模数据处理</p>
<ol>
<li>快速<br>
更快地运行工作负载100倍。<br>
Apache Spark使用最先进的DAG调度器、查询优化器和物理执行引擎，实现了批处理和流数据的高性能。</li>
<li>易用<br>
用Java、Scala、Python、R和SQL快速编写应用程序。<br>
Spark提供了超过80个高级操作符，使得构建并行应用程序变得容易。可以在Scala、Python、R和SQL shell中交互式地使用它。</li>
<li>通用<br>
结合SQL、流和复杂分析。<br>
Spark支持一系列库，包括SQL和DataFrames、用于机器学习的MLlib、GraphX和Spark流。您可以在同一个应用程序中无缝地组合这些库。</li>
<li>到处运行<br>
Spark运行在Hadoop、Apache Mesos、Kubernetes、独立或云中。它可以访问不同的数据源。<br>
您可以使用Spark的独立集群模式在EC2上、Hadoop YARN上、Mesos上或Kubernetes上运行。访问HDFS、Alluxio、Apache Cassandra、Apache HBase、Apache Hive和其他数百个数据源中的数据。</li>
</ol>
<h1><a id="Spark_16"></a>Spark产生背景</h1>
<p>MapReduce的局限性：</p>
<ol>
<li>代码繁琐；</li>
<li>只能够支持map和reduce方法；</li>
<li>执行效率低下；</li>
<li>不适合迭代多次、交互式、流式的处理；</li>
</ol>
<p>框架多样化：</p>
<ol>
<li>批处理（离线）：MapReduce、Hive、Pig</li>
<li>流式处理（实时）： Storm、JStorm</li>
<li>交互式计算：Impala</li>
</ol>
<h1><a id="Spark_27"></a>Spark发展历史</h1>
<p><img src="https://img-blog.csdnimg.cn/20181115160127599.png" alt="在这里插入图片描述"><br>
Spark官网详细历史：<br>
<a href="http://spark.apache.org/news/index.html" rel="nofollow">http://spark.apache.org/news/index.html</a></p>
<h1><a id="SparkHadoop_31"></a>Spark对比Hadoop</h1>
<h2><a id="Hadoop_32"></a>Hadoop生态系统</h2>
<p><img src="https://img-blog.csdnimg.cn/20181115163418573.png" alt="在这里插入图片描述"></p>
<h2><a id="Spark_BDASBDASBerkeley_Data_Analytics_Stack_34"></a>Spark生态系统 BDAS（BDAS:Berkeley Data Analytics Stack）</h2>
<p><img src="https://img-blog.csdnimg.cn/20181115163522302.png" alt="在这里插入图片描述"></p>
<h2><a id="HadoopSpark_36"></a>Hadoop和Spark生态圈对比：</h2>
<p><img src="https://img-blog.csdnimg.cn/20181115170155647.png" alt="在这里插入图片描述"><br>
表格解读：</p>

<table>
<thead>
<tr>
<th>用例</th>
<th>其他</th>
<th>Spark生态圈</th>
</tr>
</thead>
<tbody>
<tr>
<td>批处理</td>
<td>Hadoop中的MapRdeuce（Java操作MR,Pig,Hive）</td>
<td>Spark RDDs（java/scala/python调用相关api即可）</td>
</tr>
<tr>
<td>SQL查询</td>
<td>Hadoop中的Hive</td>
<td>Spark SQL</td>
</tr>
<tr>
<td>流处理/实时处理</td>
<td>Storm，Kafka</td>
<td>Spark Streaming</td>
</tr>
<tr>
<td>机器学习</td>
<td>Mahout（已停止更新）</td>
<td>Spark ML Lib</td>
</tr>
<tr>
<td>实时查询</td>
<td>NoSQL（Hbase,Cassandra等等）</td>
<td>无相关spark组件；但是Spark可以在NoSQL存储中查询数据（api调用即可）</td>
</tr>
</tbody>
</table><h2><a id="HadoopSpark_47"></a>Hadoop对比Spark：</h2>
<p><img src="https://img-blog.csdnimg.cn/20181115171206579.png" alt="在这里插入图片描述">表格解读：</p>

<table>
<thead>
<tr>
<th>Hadoop</th>
<th>Spark</th>
</tr>
</thead>
<tbody>
<tr>
<td>分布式存储+分布式计算</td>
<td>只关心分布式计算</td>
</tr>
<tr>
<td>MapReduce框架</td>
<td>广义的计算（通用的计算（流处理-机器学习等））</td>
</tr>
<tr>
<td>通常数据存储在磁盘上（HDFS）</td>
<td>即存储在磁盘上又存储在内存中（可设置存储策略）</td>
</tr>
<tr>
<td>不适合迭代工作</td>
<td>擅长迭代工作（机器学习）</td>
</tr>
<tr>
<td>批处理</td>
<td>即可批处理又可流处理；磁盘上的数据速度要快到2x-10x，而内存上的数据速度要快到100x</td>
</tr>
<tr>
<td>一般使用java</td>
<td>支持Java, Python, Scala，R等</td>
</tr>
</tbody>
</table><h2><a id="MapReduceSpark_57"></a>MapReduce对比Spark</h2>
<p>iter：作业处理；<br>
MapReduce作业1和作业2之间的数据交互要落地磁盘；速度慢。<br>
Spark作业1和作业2之间的数据交互无需落地；速度快。<br>
<img src="https://img-blog.csdnimg.cn/20181115172549714.png" alt="在这里插入图片描述"></p>
<h1><a id="SparkHadoop_62"></a>Spark和Hadoop的协作性</h1>
<h2><a id="Hadoop_63"></a>Hadoop优势</h2>
<ol>
<li>数据规模<br>
支持多种不同数据源<br>
支持多种应用<br>
支持多种用户</li>
<li>企业级的平台<br>
高可靠<br>
多租户<br>
安全性</li>
<li>应用范围<br>
文件数据<br>
数据库数据<br>
支持半结构化数据<br>
<img src="https://img-blog.csdnimg.cn/20181115172821243.png" alt="在这里插入图片描述"></li>
</ol>
<h2><a id="Spark_77"></a>Spark的优势</h2>
<ol>
<li>API简单，支持python，scala，java</li>
<li>内存计算框架</li>
<li>综合多个子框架进行使用<br>
<img src="https://img-blog.csdnimg.cn/20181115173004560.png" alt="在这里插入图片描述"></li>
</ol>
<h2><a id="HadoopSpark_82"></a>Hadoop+Spark（相辅相成）</h2>
<p><img src="https://img-blog.csdnimg.cn/2018111519354220.png" alt="在这里插入图片描述"><br>
<img src="https://img-blog.csdnimg.cn/20181115193615623.png" alt="在这里插入图片描述"></p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>