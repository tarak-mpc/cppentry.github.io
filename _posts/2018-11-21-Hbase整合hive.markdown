---
layout:     post
title:      Hbase整合hive
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/u010010664/article/details/72571298				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span></span>HBase是被设计用来做k-v查询的，但有时候，也会遇到基于HBase表的复杂统计，写MR很不方便。Hive考虑到了这点，提供了操作HBase表的接口。使用Hive操作HBase中的表，只是提供了便捷性，对于性能上，较MapReduce并不会提升太多，请大家酌情使用（建议只用于离线查询）</p>
<h3><span></span>1、在hbase中创建好表（通过hbase shell命令创建）</h3>
<h3><span></span>2、在hive中创建于hbase对应的</h3>
<div><pre><code class="language-sql">CREATE EXTERNAL TABLE dmp.lm_pc_user (
adx_user_id string,
property map&lt;STRING,STRING&gt;
) STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
WITH SERDEPROPERTIES ("hbase.columns.mapping" = ":key,property:")
TBLPROPERTIES ("hbase.table.name" = "lm_pc_user");	</code></pre>
<h3><span></span><span>3</span><span>、在hive中查询hbase的数据（map中获取数据）</span></h3>
</div>
<div><span><span></span></span>
<div>
<h3><span></span>4<span>、在hive插入数据到hbase（不建议使用）</span></h3>
</div>
<div><span></span><pre><code class="language-sql">INSERT INTO TABLE dmp.lm_pc_userSELECT 'row1' AS adx_user_id, map('aaa','adx') AS property, map('bbb','user_id') AS property
FROM DUAL limit 1;</code></pre><br></div>
<div><span><br></span></div>
</div>
<div><span><span></span></span></div>
<div><span><br></span></div>
<p><span></span></p>
<p><span></span></p>
            </div>
                </div>