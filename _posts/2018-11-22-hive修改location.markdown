---
layout:     post
title:      hive修改location
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<span style="font-size:18px;">hive&gt; alter table wms_c_wlsydmx set location '<span style="font-size:18px;">/user/hive/warehouse/zwpdb.db/wms_c_wlsydmx1</span>'             <br>
    &gt; ;<br>
FAILED: Error in metadata: <span style="font-size:18px;">/user/hive/warehouse/zwpdb.db/wms_c_wlsydmx1</span> is not absolute or has no scheme information. Please specify a complete absolute uri with scheme information.<br>
FAILED: Execution Error, return code 1 from org.apache.hadoop.hive.ql.exec.DDLTask<br>
hive&gt; alter table wms_c_wlsydmx set location '<span style="color:#ff0000;">hdfs://h252020:9000/</span>user/hive/warehouse/zwpdb.db/wms_c_wlsydmx1'<br>
    &gt; ;<br></span>
<p><span style="font-size:18px;">OK</span></p>
<p><span style="font-size:18px;">很明显，需要加上<span style="font-size:18px;"><span style="color:#ff0000;">hdfs://h252020:9000</span></span></span></p>
            </div>
                </div>