---
layout:     post
title:      Hive与Hbase使用域
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-size:18px;">Hive和Hbase有各自的不同特性：</span></p>
<p><span style="font-size:18px;">hive是高延迟，结构化和面向分析的，hbase是低延迟，非结构化和面向编程的。</span></p>
<p><span style="font-size:18px;">hive数据仓库在hadoop上时高延迟的，hive集成hbase就是为了使用hbase的一些特性，hive集成hbase可以有效利用habse数据库的存储特性，如行更新和列索引等，在集成的过程中注意维持hbase jar包的一致性。hive集成hbase需要在hive表和hbase表之间建立映射关系，也就是hive表的列和列类型与hbase表的列族及列限定词建立关联。每一个在hive表中的域都存在于hbase中，而在hive表中不需要包含所有的hbase中的列，hbase中的rowkey对应到hive中为选择一个域使用：key来对应，列族映射到hive中的所有域。</span></p>
<p><span style="font-size:18px;">Hive是为了简化编写MapReduce程序而生的，使用MapReduce做过数据分析的人都知道，很多分析程序除业务逻辑不同外，程序流程基本一样。在这种情况下，就需要Hive这样的用戶编程接口。Hive本身不存储和计算数据，它完全依赖于HDFS和MapReduce，Hive中的表纯逻辑，就是些表的定义等，也就是表的元数据。使用SQL实现Hive是因为SQL大家都熟悉，转换成本低，类似作用的Pig就不是SQL。<br>
HBase为查询而生的，它通过组织起节点內所有机器的內存，提供一個超大的內存Hash表，它需要组织自己的数据结构，包括磁盘和內存中的，而Hive是不做这个的，表在HBase中是物理表，而不是逻辑表，搜索引擎使用它來存储索引，以满足查询的实时性需求。<br></span></p>
<p></p>
<pre id="answer-content-1039038278" class="answer-text mb-10" style="font-family:arial, 'courier new', courier, '宋体', monospace;color:rgb(51,51,51);line-height:24px;background-color:rgb(255,255,255);"><span style="font-size:18px;">简单来说hive用来批量处理数据，HBase用来快速索引数据。
HBase是一个分布式的基于列存储的非关系型数据库。HBase的查询效率很高，主要由于查询和展示结果。
hive是分布式的关系型数据库。主要用来并行分布式 处理 大量数据。hive中的所有查询除了"select * from table;"都是需要通过Map\Reduce的方式来执行的。由于要走Map\Reduce，即使一个只有1行1列的表，如果不是通过select * from table;方式来查询的，可能也需要8、9秒。但hive比较擅长处理大量数据。当要处理的数据很多，并且Hadoop集群有足够的规模，这时就能体现出它的优势。
通过hive的存储接口，hive和Hbase可以整合使用。</span></pre>
<br><p></p>
            </div>
                </div>