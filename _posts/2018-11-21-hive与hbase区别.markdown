---
layout:     post
title:      hive与hbase区别
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：作者：sun_shang 出处：http://blog.csdn.net/sun_shang/ 文章版权归本人所有，欢迎转载，但未经作者同意必须保留此段声明，且在文章页面明显位置给出原文连接，否则保留追究法律责任的权利。					https://blog.csdn.net/sun_shang/article/details/77984398				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
hive与hbase的区别： <br><span></span>hive <span></span><br><span></span>核心将sql转换为MapReduce  <br><span></span>将存储在hdfs上的结构化的数据映射成一张数据库表，并提供类sql语句进行查询<span></span><br><span></span><br><span></span>hive的特点： <br><span></span>高延迟<br><span></span>存储的是结构化的数据 <br><span></span>hive是面向分析，使用的hql语言<br><span></span>hive不能接入业务使用  <br><span></span>hive是面向行存储的数据仓库工具，是一种纯逻辑表  <br><span></span>hive本身不存储数据和计算数据，完全依赖于hdfs和MapReduce<br><span></span>hive的本质就是将sql转换为MapReduce  <br><span></span><br><span></span>hbase的特点： <br><span></span>低延迟<br><span></span>适合存储非结构化的数据（结构化的数据也是可以存储）<br><span></span>hbase面向数据的存储和检索<br>
   <span> </span>hbase可以接入业务使用 <br><span></span>hbase是列存储，是物理表，通过索引可以快速的查询、更新、删除等操作<br><span></span>hbase不是关系型数据库，是一个构建在hdfs上的面向列存储的分布式数据库<br><span></span>hbase本身不支持sql，no-sql数据库  <br><p><span></span></p>
<p>hbase的相关特点：<br>
 BigTable是Google设计的分布式数据存储系统，用来处理海量的数据的一种非关系型的数据库。<br>
Bigtable的设计目的是快速且可靠地处理PB级别的数据，并且能够部署到上千台机器上。<br><span></span>hbase源自于BigTable，和hdfs结合使用<span></span><br><span></span>hbase是构建在hdfs上的一个分布式数据库<br><span></span>为【海量】数据提供的检索和存储平台 <br><span></span>海量： <br><span></span>单表数据量在PB级别以上的<br><span></span>上亿条数据<br><span></span>基于【列存储】的分布式非关系型数据库<br><span></span>hive默认的存储格式: 行<br><span></span>hbase默认的存储格式: 列<br><span></span>列存储和行存储的对比： <br><span></span>行存储： <br><span></span>RDBMS（关系型数据库管理系统）都是基于行存储<br><span></span>每行数据是一个连续的存储单元<br><span></span>每行数据保存在一起，插入或更新或删除操作时会更简单<br><span></span>如果select时只涉及到表的某几个列（字段），则行所有的字段都会别加载读取，系统io 工作将加大。 <br><span></span>列存储： <br><span></span>每列数据是一个连续的存储单元<br><span></span>如果select时只涉及到表的某几个列（字段），只有涉及到的列会被加载读取，大大减小系统的io 工作。 <br><span></span>以列为单元存储，因为每个列的数据类型相同，更容易实现压缩存储，并且数据会更加安全  <br><span></span>hbase适合存储【非结构化数据】<br><span></span>结构化的数据每条数据的字段的数量及种类固定 <br><span></span>非结构化的数据每条数据的字段的数量及种类可能不一样 <br><span></span>基于key-value的形式存储数据<br><span></span>key（rowkey+列簇+列）-》value <br><span></span>高可靠性、高性能、可伸缩的分布式数据库 <br><span></span>可靠性、可伸缩：hbase是构建在hdfs上的一个数据库<br><span></span>高性能：对比传统关系型数据库，hbase是大规模的高并发的分布式数据库 <br><span></span><br><span></span><br></p>
            </div>
                </div>