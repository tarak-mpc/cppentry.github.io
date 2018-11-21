---
layout:     post
title:      hive select报错 Unable to determine if hdfs
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/u012551524/article/details/78044178				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p></p>
<p>hive报错信息：</p>
FAILED: SemanticException Unable to determine if hdfs://nameservice1/user/hive/warehouse/peacebird.db/st_pause_store_item is encrypted: java.lang.IllegalArgumentException: Wrong FS: hdfs://nameservice1/user/hive/warehouse/peacebird.db/st_pause_store_item, expected:
 hdfs://zt01:8020
<p><br></p>
<p>这种情况一般是hive的数据存储路径不对，直接去mysql里修改hive的原数据就可以：</p>
<p>mysql执行如下命令：</p>
<p><br></p>
<p>use hive;</p>
<p><br></p>
<p>update DBS set DB_LOCATION_URI=REPLACE (DB_LOCATION_URI,'nameservice1','zt01')<br></p>
<p><br></p>
<p><br></p>
<p>update SDS set LOCATION=REPLACE (LOCATION,'nameservice1','zt01');<br></p>
            </div>
                </div>