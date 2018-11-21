---
layout:     post
title:      [Hive]使用HDFS目录数据创建Hive表分区
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/yeweiouyang/article/details/42082663				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><strong><span style="font-family:'Microsoft YaHei';font-size:14px;">描述：</span></strong></p>
<p><span style="font-family:'Microsoft YaHei';font-size:14px;">Hive表pms.cross_sale_path建立以日期作为分区，将hdfs目录/user/pms/workspace/ouyangyewei/testUsertrack/job1Output/crossSale上的数据，写入该表的$yesterday分区上</span></p>
<p><strong><span style="font-family:'Microsoft YaHei';font-size:14px;">表结构：</span></strong></p>
<p></p><pre><code class="language-sql">hive -e "
set mapred.job.queue.name=pms;

drop table if exists pms.cross_sale_path;
create external table pms.cross_sale_path
(
track_id string,
track_time string,
session_id string,
gu_id string,
end_user_id string,
page_category_id bigint,
algorithm_id int,
is_add_cart int,
rcmd_product_id bigint,
product_id bigint,
category_id bigint,
path_id string,
path_type int,
path_length int,
path_list string,
order_code string
)
PARTITIONED BY (ds string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t' 
LINES TERMINATED BY '\n' 
STORED AS TEXTFILE;"</code></pre>
<p></p>
<p><strong><span style="font-family:'Microsoft YaHei';font-size:14px;">写法：</span></strong></p>
<pre><code class="language-sql">yesterday=`date -d -1days +%Y-%m-%d`
hive -e "load data inpath '/user/pms/workspace/ouyangyewei/testUsertrack/job1Output/crossSale' into table pms.cross_sale_path partition(ds='$yesterday');"
</code></pre>
<p></p>
            </div>
                </div>