---
layout:     post
title:      hadoop集群节点暴力删除后，hdfs异常处理
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/u012551524/article/details/79000794				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>测试集群暴力删除5个节点之后，会出现hdfs异常问题，导致hive，hbase异常：</p>
<p>1、hdfs推出安全模式</p>
<p><span style="color:rgb(102,102,102);font-family:'宋体', Arial;">hadoop dfsadmin -safemode leave</span><br></p>
<p>2、hdfs数据块丢失</p>
<p>hdfs fsck / -delete 删掉丢失块儿</p>
<p><br></p>
            </div>
                </div>