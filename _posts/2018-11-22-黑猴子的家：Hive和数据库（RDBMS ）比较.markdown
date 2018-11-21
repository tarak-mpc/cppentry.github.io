---
layout:     post
title:      黑猴子的家：Hive和数据库（RDBMS ）比较
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/qq_28652401/article/details/83509636				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
    <div class="show-content-free">
            <p>由于 Hive 采用了类似SQL 的查询语言 HQL(hive query language)，因此很容易将 Hive 理解为数据库。其实从结构上来看，Hive 和数据库除了拥有类似的查询语言，再无类似之处。本文将从多个方面来阐述 Hive 和数据库的差异。数据库可以用在 Online 的应用中，但是Hive 是为数据仓库而设计的，清楚这一点，有助于从应用角度理解 Hive 的特性。</p>
<table><thead><tr><th style="text-align:center;">对比项</th>
<th style="text-align:center;">Hive</th>
<th style="text-align:center;">RDBMS</th>
</tr></thead><tbody><tr><td style="text-align:center;">查询语言</td>
<td style="text-align:center;">HQL</td>
<td style="text-align:center;">SQL</td>
</tr><tr><td style="text-align:center;">数据存储</td>
<td style="text-align:center;">HDFS</td>
<td style="text-align:center;">Raw Device or Local FS</td>
</tr><tr><td style="text-align:center;">执行器</td>
<td style="text-align:center;">MapReduce</td>
<td style="text-align:center;">Executor</td>
</tr><tr><td style="text-align:center;">数据插入</td>
<td style="text-align:center;">支持批量导入/单条插入</td>
<td style="text-align:center;">支持单条或者批量导入</td>
</tr><tr><td style="text-align:center;">数据操作</td>
<td style="text-align:center;">覆盖追加</td>
<td style="text-align:center;">行级更新删除</td>
</tr><tr><td style="text-align:center;">处理数据规模</td>
<td style="text-align:center;">大</td>
<td style="text-align:center;">小</td>
</tr><tr><td style="text-align:center;">执行延迟</td>
<td style="text-align:center;">高</td>
<td style="text-align:center;">低</td>
</tr><tr><td style="text-align:center;">分区</td>
<td style="text-align:center;">支持</td>
<td style="text-align:center;">支持</td>
</tr><tr><td style="text-align:center;">索引</td>
<td style="text-align:center;">0.8版本之后加入简单索引</td>
<td style="text-align:center;">支持复杂的索引</td>
</tr><tr><td style="text-align:center;">扩展性</td>
<td style="text-align:center;">高（好）</td>
<td style="text-align:center;">有限（差）</td>
</tr><tr><td style="text-align:center;">数据加载模式</td>
<td style="text-align:center;">读模式（快）</td>
<td style="text-align:center;">写模式（慢）</td>
</tr><tr><td style="text-align:center;">应用场景</td>
<td style="text-align:center;">海量数据查询</td>
<td style="text-align:center;">实时查询</td>
</tr></tbody></table><p><strong>Hive 具有 SQL 数据库的外表，但应用场景完全不同，Hive 只适合用来做海量离线数 据统计分析，也就是数据仓库。</strong></p>
<h4>1、查询语言</h4>
<p>由于SQL被广泛的应用在数据仓库中，因此，专门针对Hive的特性设计了类SQL的查询语言HQL。熟悉SQL开发的开发者可以很方便的使用Hive进行开发。</p>
<h4>2、数据存储位置</h4>
<p>Hive 是建立在 Hadoop 之上的，所有 Hive 的数据都是存储在 HDFS 中的。而数据库则可以将数据保存在块设备或者本地文件系统中。</p>
<h4>3、数据更新</h4>
<p>由于Hive是针对数据仓库应用设计的，而数据仓库的内容是读多写少的。因此，Hive中不支持对数据的改写和添加，所有的数据都是在加载的时候中确定好的。而数据库中的数据通常是需要经常进行修改的，因此可以使用 INSERT INTO …  VALUES 添加数据，使用 UPDATE … SET修改数据。</p>
<h4>4、索引</h4>
<p>Hive在加载数据的过程中不会对数据进行任何处理，甚至不会对数据进行扫描，因此也没有对数据中的某些Key建立索引。Hive要访问数据中满足条件的特定值时，需要暴力扫描整个数据，因此访问延迟较高。由于 MapReduce 的引入， Hive 可以并行访问数据，因此即使没有索引，对于<a href="http://lib.csdn.net/base/hadoop" rel="nofollow"><strong>大数据</strong></a>量的访问，Hive 仍然可以体现出优势。数据库中，通常会针对一个或者几个列建立索引，因此对于少量的特定条件的数据的访问，数据库可以有很高的效率，较低的延迟。由于数据的访问延迟较高，决定了 Hive 不适合在线数据查询。</p>
<h4>5、执行</h4>
<p>Hive中大多数查询的执行是通过 Hadoop 提供的 MapReduce 来实现的。而数据库通常有自己的执行引擎。</p>
<h4>6、执行延迟</h4>
<p>Hive 在查询数据的时候，由于没有索引，需要扫描整个表，因此延迟较高。另外一个导致 Hive 执行延迟高的因素是 MapReduce框架。由于MapReduce 本身具有较高的延迟，因此在利用MapReduce 执行Hive查询时，也会有较高的延迟。相对的，数据库的执行延迟较低。当然，这个低是有条件的，即数据规模较小，当数据规模大到超过数据库的处理能力的时候，Hive的并行计算显然能体现出优势。</p>
<h4>7、可扩展性</h4>
<p>由于Hive是建立在Hadoop之上的，因此Hive的可扩展性是和Hadoop的可扩展性是一致的（世界上最大的Hadoop 集群在 Yahoo!，2009年的规模在4000 台节点左右）。而数据库由于 ACID 语义的严格限制，扩展行非常有限。目前最先进的并行数据库 <a href="http://lib.csdn.net/base/oracle" rel="nofollow"><strong>Oracle</strong></a> 在理论上的扩展能力也只有100台左右。</p>
<h4>8、数据规模</h4>
<p>由于Hive建立在集群上并可以利用MapReduce进行并行计算，因此可以支持很大规模的数据；对应的，数据库可以支持的数据规模较小。</p>

          </div>
              </div>
                </div>