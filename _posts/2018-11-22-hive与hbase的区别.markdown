---
layout:     post
title:      hive与hbase的区别
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/James__Tao/article/details/78106379				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div style="line-height:1.75;"><span style="font-size:24px;">hive与hbase的区别：</span></div>
<div style="line-height:1.75;font-size:14px;">hive 核心将sql转换为MapReduce</div>
<div style="line-height:1.75;font-size:14px;">将存储在hdfs上的结构化的数据映射成一张表，并提供类sql语句进行查询</div>
<div style="line-height:1.75;font-size:14px;"></div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><strong>hive的特点：</strong></div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;">高延迟</div>
<div style="line-height:1.75;font-size:14px;">存储的是结构化的数据 </div>
<div style="line-height:1.75;font-size:14px;">hive是面向分析，使用的hql语言</div>
<div style="line-height:1.75;font-size:14px;">hive不能接入业务使用 </div>
<div style="line-height:1.75;font-size:14px;">hive是面向行存储的数据仓库工具，是一种纯逻辑表</div>
<div style="line-height:1.75;font-size:14px;">hive本身不存储数据和计算数据，完全依赖于hdfs和MapReduce</div>
<div style="line-height:1.75;font-size:14px;">hive的本质就是将sql转换为MapReduce</div>
<div style="line-height:1.75;font-size:14px;"></div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><strong>hbase的特点：</strong></div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;">低延迟</div>
<div style="line-height:1.75;font-size:14px;">适合存储非结构化的数据（结构化的数据也是可以存储）</div>
<div style="line-height:1.75;font-size:14px;">hbase面向数据的存储和检索</div>
<div style="line-height:1.75;font-size:14px;">hbase可以接入业务使用</div>
<div style="line-height:1.75;font-size:14px;">hbase是列存储，是物理表，通过索引可以快速的查询、更新、删除等操作</div>
<div style="line-height:1.75;font-size:14px;">hbase不是关系型数据库，是一个构建在hdfs上的面向列存储的分布式数据库</div>
<div style="line-height:1.75;font-size:14px;">hbase本身不支持sql，no-sql数据库</div>
<div style="line-height:1.75;font-size:14px;"><br></div>
            </div>
                </div>