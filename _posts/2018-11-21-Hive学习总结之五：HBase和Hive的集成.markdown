---
layout:     post
title:      Hive学习总结之五：HBase和Hive的集成
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div class="blog-heading"><span class="status-tag original" title="原创博客"></span><span class="status-tag recommend" title="首页推荐过的博客"></span><br><div class="user-info"><br></div>
</div>
Hive和Hbase有各自不同的特征：hive是高延迟、结构化和面向分析的，hbase是低延迟、非结构化和面向编程的。Hive数据仓库在hadoop上是高延迟的。Hive集成Hbase就是为了使用hbase的一些特性。如下是hive和hbase的集成架构：
<div class="blog-body">
<div class="BlogContent">
<p><a href="http://coolbash.in/2013/03/hbase%E5%92%8Chive%E7%9A%84%E9%9B%86%E6%88%90/attachment/2/" rel="nofollow"><img alt="2" src="http://static.oschina.net/uploads/img/201303/10125738_wKLE.jpg" height="205" width="300"></a>
</p>
<p>  </p>
<p>图1 hive和hbase架构图 </p>
<p>        Hive集成HBase可以有效利用HBase数据库的存储特性，如行更新和列索引等。在集成的过程中注意维持HBase jar包的一致性。Hive集成HBase需要在Hive表和HBase表之间建立映射关系，也就是Hive表的列(columns)和列类型(column types)与HBase表的列族(column families)及列限定词(column qualifiers)建立关联。每一个在Hive表中的域都存在于HBase中，而在Hive表中不需要包含所有HBase中的列。HBase中的RowKey对应到Hive中为选择一个域使用:key来对应，列族(cf:)映射到Hive中的其它所有域，列为(cf:cq)。例如下图2为Hive表映射到HBase表：
</p>
<p><a href="http://coolbash.in/2013/03/hbase%E5%92%8Chive%E7%9A%84%E9%9B%86%E6%88%90/1-2/" rel="nofollow"><img alt="1" src="http://static.oschina.net/uploads/img/201303/10125738_5wLO.jpg" height="91" width="300"></a>
</p>
<p>图2 Hive表映射HBase表 </p>
</div>
</div>
            </div>
                </div>