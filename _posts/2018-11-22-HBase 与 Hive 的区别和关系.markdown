---
layout:     post
title:      HBase 与 Hive 的区别和关系
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文只为记录博主的学习点滴，欢迎转载与交流。					https://blog.csdn.net/u010758410/article/details/79812860				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>　　Hive和Hbase都是Hadoop生态体系中的重要角色，初接触难免有些理解不透彻的地方，谨以此博文做一小小记录。 <br>
　　</p>



<h1 id="通俗的理解">通俗的理解</h1>

<p>这里引用别人的一些经验。</p>

<ul>
<li><p>Hive是什么？</p>

<p>　　如果非要用一句话来说明Hive，则是这样的：Hive是sql到mapreduce程序的翻译工具。hive可以认为是map-reduce的一个包装，hive的意义就是把好写的hive的sql转换为复杂难写的map-reduce程序。</p></li>
<li><p>Hbase是什么？</p>

<p>　　也用一句话来说明一下Hbase，则是这样的：hbase可以认为是hdfs的一个包装。他的本质是数据存储，是个NoSql数据库；hbase部署于hdfs之上，并且克服了hdfs在随机读写方面的缺点。</p></li>
</ul>



<h1 id="hive和hbase区别">HIVE和HBASE区别</h1>



<h2 id="两者分别是什么">两者分别是什么？</h2>

<p>　　Apache Hive是一个构建在Hadoop基础设施之上的数据仓库。通过Hive可以使用HQL语言查询存放在HDFS上的数据。HQL是一种类SQL语言，这种语言最终被转化为Map/Reduce. 虽然Hive提供了SQL查询功能，但是Hive不能够进行交互查询–因为它只能够在Haoop上批量的执行Hadoop。</p>

<p>　　Apache HBase是一种Key/Value系统，它运行在HDFS之上。和Hive不一样，Hbase的能够在它的数据库上实时运行，而不是运行MapReduce任务。Hive被分区为表格，表格又被进一步分割为列簇。列簇必须使用schema定义，列簇将某一类型列集合起来（列不要求schema定义）。例如，“message”列簇可能包含：“to”, ”from” “date”, “subject”, 和”body”. 每一个 key/value对在Hbase中被定义为一个cell，每一个key由row-key，列簇、列和时间戳。在Hbase中，行是key/value映射的集合，这个映射通过row-key来唯一标识。Hbase利用Hadoop的基础设施，可以利用通用的设备进行水平的扩展。</p>

<h2 id="两者的特点">两者的特点</h2>

<p>　　Hive帮助熟悉SQL的人运行MapReduce任务。因为它是JDBC兼容的，同时，它也能够和现存的SQL工具整合在一起。运行Hive查询会花费很长时间，因为它会默认遍历表中所有的数据。虽然有这样的缺点，一次遍历的数据量可以通过Hive的分区机制来控制。分区允许在数据集上运行过滤查询，这些数据集存储在不同的文件夹内，查询的时候只遍历指定文件夹（分区）中的数据。这种机制可以用来，例如，只处理在某一个时间范围内的文件，只要这些文件名中包括了时间格式。</p>

<p>　　HBase通过存储key/value来工作。它支持四种主要的操作：增加或者更新行，查看一个范围内的cell，获取指定的行，删除指定的行、列或者是列的版本。版本信息用来获取历史数据（每一行的历史数据可以被删除，然后通过Hbase compactions就可以释放出空间）。虽然HBase包括表格，但是schema仅仅被表格和列簇所要求，列不需要schema。Hbase的表格包括增加/计数功能。</p>

<h2 id="限制">限制</h2>

<p>　　Hive目前不支持更新操作。另外，由于hive在hadoop上运行批量操作，它需要花费很长的时间，通常是几分钟到几个小时才可以获取到查询的结果。Hive必须提供预先定义好的schema将文件和目录映射到列，并且Hive与ACID不兼容。</p>

<p>　　HBase查询是通过特定的语言来编写的，这种语言需要重新学习。类SQL的功能可以通过Apache Phonenix实现，但这是以必须提供schema为代价的。另外，Hbase也并不是兼容所有的ACID特性，虽然它支持某些特性。最后但不是最重要的–为了运行Hbase，Zookeeper是必须的，zookeeper是一个用来进行分布式协调的服务，这些服务包括配置服务，维护元信息和命名空间服务。</p>

<h2 id="应用场景">应用场景</h2>

<p>　　Hive适合用来对一段时间内的数据进行分析查询，例如，用来计算趋势或者网站的日志。Hive不应该用来进行实时的查询。因为它需要很长时间才可以返回结果。</p>

<p>　　Hbase非常适合用来进行大数据的实时查询。Facebook用Hbase进行消息和实时的分析。它也可以用来统计Facebook的连接数。</p>

<h2 id="总结">总结</h2>

<p>　　Hive和Hbase是两种基于Hadoop的不同技术–Hive是一种类SQL的引擎，并且运行MapReduce任务，Hbase是一种在Hadoop之上的NoSQL 的Key/vale数据库。当然，这两种工具是可以同时使用的。就像用Google来搜索，用FaceBook进行社交一样，Hive可以用来进行统计查询，HBase可以用来进行实时查询，数据也可以从Hive写到Hbase，设置再从Hbase写回Hive。</p>

<h1 id="小结">小结：</h1>

<h2 id="共同点">共同点：</h2>

<ol>
<li>hbase与hive都是架构在hadoop之上的。都是用hadoop作为底层存储</li>
</ol>



<h2 id="区别">区别：</h2>

<ol>
<li>Hive是建立在Hadoop之上为了减少MapReduce jobs编写工作的批处理系统，HBase是为了支持弥补Hadoop对实时操作的缺陷的项目 ; </li>
<li>想象你在操作RMDB数据库，如果是全表扫描，就用Hive+Hadoop,如果是索引访问，就用HBase+Hadoop ; </li>
<li>Hive query就是MapReduce jobs可以从5分钟到数小时不止，HBase是非常高效的，肯定比Hive高效的多。 </li>
<li>Hive本身不存储和计算数据，它完全依赖于HDFS和MapReduce，Hive中的表纯逻辑，实际文件会落地在hdfs，并形成一个文件夹; </li>
<li>hive借用hadoop的MapReduce来完成一些hive中的命令的执行 </li>
<li>hbase是物理表，不是逻辑表，提供一个超大的内存hash表，搜索引擎通过它来存储索引，方便查询操作。 </li>
<li>hbase是列存储。 </li>
<li>hdfs作为底层存储，hdfs是存放文件的系统，而Hbase负责组织文件。 </li>
<li>hive需要用到hdfs存储文件，需要用到MapReduce计算框架。</li>
</ol>

<p>参考：<a href="https://blog.csdn.net/andrewgb/article/details/50493088" rel="nofollow">https://blog.csdn.net/andrewgb/article/details/50493088</a> <br>
参考：<a href="https://blog.csdn.net/z69183787/article/details/46403849" rel="nofollow">https://blog.csdn.net/z69183787/article/details/46403849</a></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>