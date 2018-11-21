---
layout:     post
title:      Hadoop体系中，hive和hbase的区别
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/xaowoniu/article/details/74963444				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
首先理清hive和hbase的概念吧：<br><br><br>
1、hive是什么？<br>
hive可以认为是map-reduce的一个包装。<br>
hive的意义就是把好写的hive的sql（也叫hql）转换为复杂难写的map-reduce程序，从而降低使用Hadoop中使用map-reduce的难度。<br>
Hive本身不存储和计算数据，它完全依赖于HDFS和MapReduce，Hive中的表纯逻辑（只是个逻辑表）<br><br><br>
2、hbase是什么？<br>
hbase可以认为是hdfs的一个包装。他的本质是数据存储，是个NoSql数据库；hbase部署于hdfs之上，并且克服了hdfs在随机读写方面的缺点。<br>
hbase可以理解为为hdfs建立了索引，查询不走map-reduce，直接走自己的表<br>
hbase是物理表，不是逻辑表，提供一个超大的内存hash表，搜索引擎通过它来存储索引，方便查询操作。 
            </div>
                </div>