---
layout:     post
title:      Spark在Hadoop用户下启动
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>问题描述：Hadoop用户下启动Spark时，Worker和Mater进程在root用户下启动而不在Hadoop用户下启动。</p><p>解决方案：修改Spark所属权限：</p><p>chown -R hadoop:hadoop ./spark</p>            </div>
                </div>