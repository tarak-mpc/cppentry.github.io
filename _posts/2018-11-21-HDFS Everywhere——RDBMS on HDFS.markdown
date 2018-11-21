---
layout:     post
title:      HDFS Everywhere——RDBMS on HDFS
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/dcplove/article/details/46387723				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="关系型数据库和hdfs的结合"><strong>关系型数据库和HDFS的结合</strong></h1>

<p>**</p>



<h2 id="目录">目录</h2>

<p>** <br>
简介：  <br>
– 题目： <br>
– 摘要： <br>
– 背景： <br>
思路和计划： <br>
– 目标： <br>
分工：  <br>
已有解决方案：  <br>
– Sqoop: <br>
Solution： <br>
sqoop与loading对比：    </p>

<h2 id="项目简介">项目简介：</h2>

<p>– 题目： <br>
   HDFS Everywhere——RDBMS on HDFS</p>

<p>– 摘要： <br>
       组合RDBMS和HDFS，实现新的性能，综合利用RDBMS和Hadoop的各自优势、同时避免各自缺陷。</p>

<p>– 背景： <br>
       数据库十分常见，并经常和其他信息系统相结合，关系型数据库在查询结构化数据的时候操作简便，效率较高。有些人认为随着Apache Hadoop的发展，意味着原来的关系型数据库的死亡，但是这其实并不对。Hadoop不仅仅可以和关系型数据库同时存在，而且他们可以协同整合在一起。 <br>
       随着云计算和物联网等技术在全球的快速发展，企业对大数据 (Big Data) 业务的关注也持续升温。在大数据时代，数据无疑是企业的核心资产之一，若能盘活好数据，则能使企业在公司治理、企业决策和客户服务等方方面面受益匪浅；反之，则在现代企业竞争中，容易导致其核心竞争力下降，甚至衰落。 Apache Hadoop 由于擅长处理大数据分析业务，受到了广大企业的青睐。目前，多数使用 Hadoop 技术处理大数据业务的企业也同时有大量的数据存储于传统的关系型数据库 (RDBMS) 里，如 IBM DB2 数据库。在这种情况下，由于缺乏良好的工具支持，将企业分别存储在 Hadoop 和 DB2 系统里的数据进行传输是一件十分困难的事情，因此也大大限制住了企业更好地对数据进行统一管理和应用。 <br>
       关系型数据库在查询结构化数据的时候操作简便，效率较高，但数据容错性低，不适合集群配置。而Apache Hadoop的高可靠性、高扩展性、高效性是大规模数据存储的首选，但是其不支持小文件，难以满足大规模用户的读写。如何将二者结合，博取二者之长？</p>

<p>思路和计划：</p>

<p>– 目标： <br>
       HDFS非常适合部署云存储平台，但由于采用主从式架构，存储小文件（指小于HDFS上默认块（64MB）的文件）效率低下，而有时这种海量小文件数据的高效存取、管理十分关键。探索HDFS和RDBMS的结合方法，以及各种结合方法的优劣。 <br>
   HDFS Everywhere——RDBMS on HDFS <br>
通过结合后的结构和结合前进行对比，研究是否改变了提高了容错性。</p>

<h2 id="已有解决方案">已有解决方案：</h2>

<p>Sqoop可以实现mysql表和hive的导入导出；与hdfs的导入导出；利用eval在控制端实现sql语句；将数据库中所有table输出到不同目录；利用merge将hdfs上不同目录下的文件合并。 <br>
– Sqoop: <br>
Apache Sqoop（SQL-to-Hadoop）项目旨在协助RDBMS与Hadoop之间进行高效的大数据交流。用户可以在Sqoop的帮助下，轻松地把关系型数据库的数据导入到Hadoop与其相关的系统(如HBase和Hive)中；同时也可以把数据从Hadoop系统里抽取并导出到关系型数据库里。除了这些主要的功能外，Sqoop也提供了一些诸如查看数据库表等实用的小工具。</p>

<p><img src="https://img-blog.csdn.net/20150625164036593" alt="Sqoop设计示意" title=""> <br>
Sqoop是一个转换工具，用于在关系型数据库与HDFS之间进行数据转换。强大功能见下图: <br>
 <img src="https://img-blog.csdn.net/20150625164158364" alt="Sqoop流程图" title=""></p>

<p>Sqoop中一大亮点就是可以通过hadoop的mapreduce把数据从关系型数据库中导入数据到HDFS。Sqoop架构非常简单，其整合了Hive、Hbase和Oozie，通过map-reduce任务来传输数据，从而提供并发特性和容错。</p>

<p>Sqoop大概流程: <br>
1.  读取要导入数据的表结构，生成运行类，默认是QueryResult，打成jar包，然后提交给Hadoop <br>
2.  设置好job，主要也就是设置好以上各个参数 <br>
3.  这里就由Hadoop来执行MapReduce来执行Import命令 <br>
1)  首先要对数据进行切分； <br>
 2)  切分好范围后，写入范围，以便读取； <br>
3)  读取以上2）写入的范围； <br>
4)  然后创建RecordReader从数据库中读取数据； <br>
5)  创建MAP，MapTextImportMapper.setup(Context context)； <br>
6)  RecordReader一行一行从关系型数据库中读取数据，设置好Map的Key和Value，交给MapDBRecordReader.nextKeyValue() <br>
7)  运行MAP，mapTextImportMapper.map(LongWritable key, SqoopRecord val, Context context),最后生成的Key是行数据，由QueryResult生成，Value是NullWritable.get()；</p>

<p>总结来说，sqoop有这些功能： <br>
sqoop功能：（列出数据库,表，表结构复制到hive，与hive导入导出，与hdfs导入导出（支持增量导入导出）,利用eval在控制端实现sql语句,利用codegen将关系数据库表映射为一个java文件，将数据库中所有table输出到（不同目录），利用merge将hdfs上的不同目录下文件合并）</p>



<h2 id="问题">问题：</h2>

<pre><code>1、Sqoop只能向已知表结构中写入数据，即数据库中原表必须存在；
2、Sqoop不能自动对不同的表进行分割，组合后的表必须人工分割再写入指定的表中；
</code></pre>

<p>3、Sqoop在hdfs上存储的只是各列的value信息，容错性低。 <br>
 <img src="https://img-blog.csdn.net/20150625164253133" alt="这里写图片描述" title=""></p>

<p>当本地中原表被删除或意外失效时，从Sqoop恢复原表时会出错，主要是因为Sqoop上传到HDFS上的数据只有Table的ColumnValue，并没有保存ColumnType和PrimaryKey等信息，因此，当没有原表结构的时候是不能从HDFS上恢复原表的。 <br>
 <img src="https://img-blog.csdn.net/20150625164319213" alt="这里写图片描述" title=""></p>



<h2 id="solution">Solution：</h2>

<pre><code>针对这一发现，我们小组实现了以下功能：
</code></pre>

<p>1、将mysql中大量小型表进行组合，利用hdfs进行存储，实现文件的大规模高效存储； <br>
2、支持不同类型表的组合，完全保留表中信息（表的主键、各列的属性信息等）； <br>
3、支持hdfs导入导出，mysql中各表的智能恢复（实现灾备恢复）； <br>
4、支持由hdfs上的文件恢复成不同结构的表； <br>
4、支持列数据的检验，提高容错性。 <br>
 <img src="https://img-blog.csdn.net/20150625164339981" alt="这里写图片描述" title=""></p>

<p>当原表被删除时，我们仍然能够恢复原表。</p>

<p><img src="https://img-blog.csdn.net/20150625164353680" alt="这里写图片描述" title=""> <br>
 Related Work <br>
1、  利用loading将hdfs与mysql进行互联（不仅传送表数据，也传送表结构） <br>
2、  同时读取mysql的多个表，将其传送至hdfs <br>
3、  在hadoop全分布模式上进行hdfs与mysql各种表互传测试</p>

<p>在进行hdfs与mysql的文件互传中： <br>
1、  将mysql数据库中表的数据传送至hdfs时，我们不仅传送表内数据，且将表的结构描述传送至hdfs； <br>
2、  当用户需要将hdfs中数据传回mysql的表时，即便mysql数据库中不存在该表，我们仍能完整恢复表。 <br>
在进行hdfs与mysql的多文件互传中 <br>
1、  为每个文件设置唯一标签</p>

<p>Results <br>
mysql—&gt;hadoop: <br>
mysql的表能够完整地传至hdfs; <br>
hadoop—&gt;mysql: <br>
                该过程既可完成对mysql表的追加功能，也可完成表的完全恢复</p>

<p>sqoop与loadoop对比： <br>
sqoop的功能： <br>
mysql表与hive的导入导出；与hdfs的导入导出；利用eval在控制端实现sql语句；将数据库中所有table输出到（不同目录），利用merge将hdfs上的不同目录下文件合并；</p>

<p>sqoop的功能： <br>
mysql各种表与hdfs的导入导出（不仅包含表数据，且包含表的结构）；在hadoop完全分布式中进行多文件的互传；将各种mysql表直接导入到hdfs同一目录；</p>

<p>loadoop的优势： <br>
        Sqoop是一个用来将Hadoop和关系型数据库中的数据相互转移的工具，它可以将mysql数据库中表的数据传送至hdfs，也可以将hdfs中数据传回mysql的表中，但是，sqoop将表从hdfs传回mysql有一个很大的限制：mysql中必须有相应结构的表存在！</p>

<p>我们的工具不受此限制！</p>

<p>Sqoop传送的是表数据: <br>
 <img src="https://img-blog.csdn.net/20150625164421128" alt="这里写图片描述" title=""></p>

<p>Loadoop传送的是表数据和表结构:</p>

<p><img src="https://img-blog.csdn.net/20150625164443287" alt="这里写图片描述" title=""></p>

<p><img src="https://img-blog.csdn.net/20150625164518162" alt="这里写图片描述" title=""></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>