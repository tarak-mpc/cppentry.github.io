---
layout:     post
title:      [Hive]MapReduce将数据写入Hive分区表
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/yeweiouyang/article/details/44834073				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h2>业务需求：</h2>
<p><span></span>将当天产生的数据写入Hive分区表中（以日期作为分区）</p>
<h2>业务分析：</h2>
<p><span></span>利用MapReduce将数据写入Hive表实则上就是将数据写入至Hive表的HDFS目录下，但是问题在于写入至当天的分区，因此问题转换为：如何事先创建Hive表的当天分区</p>
<h2>解决方案：</h2>
<p><strong>1.<span> </span>创建Hive表</strong></p>
<p></p>
<pre><code class="language-sql"># 先创建分区表rcmd_valid_path
hive -e "set mapred.job.queue.name=pms;

drop table if exists pms.test_rcmd_valid_path;
create table if not exists pms.test_rcmd_valid_path 
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
path_id string,
path_type string,
path_length int,
path_list string,
order_code string,
groupon_id bigint
)
partitioned by (ds string) 
ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t' 
LINES TERMINATED BY '\n';"
</code></pre><strong>2.<span> </span>创建表的date当天分区（若分区不存在则创建）<br></strong>
<p></p>
<pre><code class="language-sql"># 创建正式表rcmd_valid_path表date当天的分区目录
hive -e "set mapred.job.queue.name=pms;

insert overwrite table pms.test_rcmd_valid_path partition(ds='$date')
select track_id,
track_time,
session_id,
gu_id,
end_user_id,
page_category_id,
algorithm_id,
is_add_cart,
rcmd_product_id,
product_id,
path_id,
path_type,
path_length,
path_list,
order_code,
groupon_id 
from pms.test_rcmd_valid_path where ds = '$date';" 
</code></pre><strong>3.<span> </span>Job直接写入即可（留意job2OutputPath）<br></strong>
<p></p>
<pre><code class="language-plain">hadoop jar lib/bigdata-datamining-1.1-user-trace-jar-with-dependencies.jar com.yhd.datamining.data.usertrack.offline.job.mapred.TrackPathJob \
--similarBrandPath /user/pms/recsys/algorithm/schedule/warehouse/relation/brand/$yesterday \
--similarCategoryPath /user/pms/recsys/algorithm/schedule/warehouse/relation/category/$yesterday \
--mcSiteCategoryPath /user/hive/warehouse/mc_site_category \
--extractPreprocess /user/hive/warehouse/test_extract_preprocess \
--engineMatchRule /user/pms/recsys/algorithm/schedule/warehouse/mix/artificial/product/$yesterday \
--artificialMatchRule /user/pms/recsys/algorithm/schedule/warehouse/ruleengine/artificial/product/$yesterday \
--category /user/hive/warehouse/category \
--keywordCategoryTopN 3 \
--termCategory /user/hive/pms/temp_term_category \
--extractGrouponInfo /user/hive/pms/extract_groupon_info \
--extractProductSerial /user/hive/pms/product_serial_id \
--job1OutputPath /user/pms/workspace/ouyangyewei/testUsertrack/job1Output \
--job2OutputPath /user/hive/pms/test_rcmd_valid_path/ds=$date 
</code></pre><br><p></p>
<h2>方法二（使用load data）：</h2>
<pre><code class="language-html"># 执行提取有效路径代码
# NOTE:相似类目和相似品牌的数据只有最近两到三天的。
hadoop jar $userTrackLibHome/bigdata-datamining-$userTrackVersion-jar-with-dependencies.jar com.yhd.datamining.data.usertrack.offline.job.mapred.TrackPathJob \
--similarBrandPath /user/pms/recsys/algorithm/schedule/warehouse/relation/brand/$yesterday \
--similarCategoryPath /user/pms/recsys/algorithm/schedule/warehouse/relation/category/$yesterday \
--mcSiteCategoryPath /user/hive/warehouse/mc_site_category \
--extractPreprocess /user/hive/warehouse/extract_preprocess \
--engineMatchRule /user/pms/recsys/algorithm/schedule/warehouse/mix/artificial/product/$yesterday \
--artificialMatchRule /user/pms/recsys/algorithm/schedule/warehouse/ruleengine/artificial/product/$yesterday \
--category /user/hive/warehouse/category \
--keywordCategoryTopN 3 \
--termCategory /user/hive/pms/temp_term_category \
--extractGrouponInfo /user/hive/pms/extract_groupon_info \
--extractProductSerial /user/hive/pms/product_serial_id \
--job1OutputPath /user/pms/workspace/chenwu/usertrack/job1OutputPath \
--job2OutputPath /user/hive/pms/rcmd_valid_path_test/ds=$date</code></pre><pre><code class="language-html"># 将Job1的交叉销售数据load到交叉销售有效路径表的分区中
hive -e "load data inpath '/user/pms/workspace/chenwu/usertrack/job1OutputPath/crossSale' overwrite into table pms.cross_sale_path partition(ds='$date');"</code></pre><br><p></p>
            </div>
                </div>