---
layout:     post
title:      hive导出hbase数据
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/ma141982/article/details/71713817				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>hive和hbase连接使用是生产中很常见的一种方法，hbase当做存储结构，hive用作查询，下面的方法是使用hive导出hbase数据的一种方法，前提是hive已经和hbase整合，如果你还没整合，可以去看看我的另一篇文章，里面有hive和hbase整合的方法</p>
<p>#b不能丢弃，否则hbase数据转换不出中文</p>
<p>--建立映射表<br><br><br>
CREATE EXTERNAL TABLE if not exists table(<br>
rkey STRING,<br>
1y_new_enddate STRING,<br>
nl string,<br>
sx string,<br>
xb string,<br>
xm string,<br>
xz string<br>
)<br>
ROW FORMAT SERDE 'org.apache.hadoop.hive.hbase.HBaseSerDe'<br>
stored by 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'<br>
WITH SERDEPROPERTIES(<br>
"hbase.columns.mapping"=":key,tag:1y_new_enddate#b,tag:rksx_nl#b,tag:rksx_sx#b,tag:rksx_xb#b,tag:rksx_xm#b,tag:rksx_xz#b"<br>
)<br>
TBLPROPERTIES(<br>
"hbase.table.name"="hbase数据库名"<br>
);                   <br><br><br>
--输出数据到本地<br><br><br>
insert overwrite local directory '/mwork/nothing/hbase_out' row format delimited FIELDS TERMINATED BY '\t' select * from hiveconf:out-table;  <br><br><br>
--输出数据到HDFS<br><br><br>
--insert overwrite directory '/tmp/hive_hbase_out' row format delimited FIELDS TERMINATED BY '\t' select * from out-table;  <br></p>
            </div>
                </div>