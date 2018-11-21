---
layout:     post
title:      Hive vs RDBMS[笔记3]
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/xiangxizhishi/article/details/77203637				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<span style="font-family:SimSun;font-size:14px;">由于 Hive 采用了 SQL 的查询语言 HQL，因此很容易将 Hive理解为数据库。其实从结构上来看，Hive 和数据库除了拥有类似的查询语言，再无类似之处。本文将从多个方面来阐述 Hive 和数据库的差异。数据库可以用在 Online的应用中，但是Hive 是为数据仓库而设计的，清楚这一点，有助于从应用角度理解<br><br>
Hive 的特性。<br><br>
Hive 和数据库的比较 查询语言 HQL SQL<br>
数据存储位置 HDFS Raw Device 或者 Local FS<br>
数据格式 用户定义 系统决定<br>
数据更新 支持 不支持<br>
索引 无 有<br>
执行 MapRedcue Executor<br>
执行延迟 高 低<br>
可扩展性 高 低<br>
数据规模 大 小<br><br>
查询语言。由于 SQL 被广泛的应用在数据仓库中，因此，专门针对Hive 的特性设计了类 SQL 的查询语言 HQL。熟悉 SQL 开发的开发<br>
者可以很方便的使用 Hive 进行开发。数据存储位置。Hive 是建立在 Hadoop 之上的，所有 Hive 的数据<br>
都是存储在 HDFS 中的。而数据库则可以将数据保存在块设备或者本地文件系统中。数据格式。Hive 中没有定义专门的数据格式，数据格式可以由用户<br>
指定，用户定义数据格式需要指定三个属性：列分隔符（通常为空格、”\t”、”\x001″）、行分隔符（”\n”）以及读取文件数据的方法（Hive 中默认有三个文件格式 TextFile，SequenceFile 以及RCFile）。由于在加载数据的过程中，不需要从用户数据格式到<br>
Hive 定义的数据格式的转换，因此，Hive 在加载的过程中不会对数<br>
据本身进行任何修改，而只是将数据内容复制或者移动到相应的<br>
HDFS 目录中。而在数据库中，不同的数据库有不同的存储引擎，定义了自己的数据格式。所有数据都会按照一定的组织存储，因此，数<br>
据库加载数据的过程会比较耗时。数据更新。由于 Hive 是针对数据仓库应用设计的，而数据仓库的内<br>
容是读多写少的。因此，Hive 中不支持对数据的改写和添加，所有<br>
的数据都是在加载的时候中确定好的。而数据库中的数据通常是需要经常进行修改的，因此可以使用 INSERT INTO ...  VALUES 添加数<br>
据，使用 UPDATE ... SET 修改数据。索引。之前已经说过，Hive 在加载数据的过程中不会对数据进行任何处理，甚至不会对数据进行扫描，因此也没有对数据中的某些 Key建立索引。Hive 要访问数据中满足条件的特定值时，需要暴力扫描整个数据，因此访问延迟较高。由于 MapReduce 的引入， Hive 可以并行访问数据，因此即使没有索引，对于大数据量的访问，Hive仍然可以体现出优势。数据库中，通常会针对一个或者几个列建立索引，因此对于少量的特定条件的数据的访问，数据库可以有很高的效率，较低的延迟。由于数据的访问延迟较高，决定了
 Hive 不适合在线数据查询。执行。Hive 中大多数查询的执行是通过 Hadoop 提供的 MapReduce来实现的（类似 select * from tbl 的查询不需要 MapReduce）。而数据库通常有自己的执行引擎。执行延迟。之前提到，Hive在查询数据的时候，由于没有索引，需要扫描整个表，因此延迟较高。另外一个导致 Hive 执行延迟高的因<br>
素是 MapReduce 框架。由于 MapReduce 本身具有较高的延迟，因此在利用 MapReduce 执行 Hive 查询时，也会有较高的延迟。相对的，数据库的执行延迟较低。当然，这个低是有条件的，即数据规模较小，当数据规模大到超过数据库的处理能力的时候，Hive 的并行计<br>
算显然能体现出优势。可扩展性。由于 Hive 是建立在 Hadoop 之上的，因此 Hive 的可扩展性是和 Hadoop 的可扩展性是一致的（世界上最大的 Hadoop 集群在 Yahoo!，2009年的规模在 4000 台节点左右）。而数据库由于ACID 语义的严格限制，扩展行非常有限。目前最先进的并行数据库Oracle 在理论上的扩能力也只有 100 台左右。数据规模。由于 Hive 建立在集群上并可以利用 MapReduce 进行并行计算，因此可以支持很大规模的数据；对应的，数据库可以支持的数据规模较小。
<br><br></span>
            </div>
                </div>