---
layout:     post
title:      Hive Metastore sql 关联查询出Hive Table字段
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：学习交流为主，未经博主同意禁止转载，禁止用于商用。					https://blog.csdn.net/u012965373/article/details/78285778				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div><span style="font-size:18px;">解析存储Hive Meta的几张表，然后我们选择</span></div>
<div><span style="font-size:18px;">SDS 数据存储信息表</span></div>
<div><span style="font-size:18px;">COLUMNS_V2 字段信息表</span></div>
<div><span style="font-size:18px;">这两张表</span></div>
<div><span style="font-size:18px;"><br></span></div>
<div><span style="font-size:18px;">然后利用查询操作即可查询出指定的database and table的字段信息：</span></div>
<div><span style="font-size:18px;"><br></span></div>
<div><span style="font-size:18px;">求字段未排序的结果：</span></div>
<div><span style="font-size:18px;"><br></span></div>
<div><span style="font-size:18px;">select COLUMN_NAME from COLUMNS_V2 t left join SDS t2 on t.CD_ID = t2.CD_ID where t2.LOCATION = "hdfs://localhost:8020/apps/hive/warehouse/database_name/table_name”;</span></div>
<div><span style="font-size:18px;"><br></span></div>
<div><span style="font-size:18px;">求排序后的结果：</span></div>
<div><span style="font-size:18px;">select COLUMN_NAME from COLUMNS_V2 t left join SDS t1 on t.CD_ID = t1.CD_ID where t1.LOCATION = "hdfs://localhost:8020/apps/hive/warehouse/database_name/table_name" order by t.INTEGER_IDX;</span></div>
<div><span style="font-size:18px;"><br></span></div>
<div><br></div>
            </div>
                </div>