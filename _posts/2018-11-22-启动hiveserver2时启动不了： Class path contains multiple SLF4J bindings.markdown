---
layout:     post
title:      启动hiveserver2时启动不了： Class path contains multiple SLF4J bindings
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p style="text-indent:0;"><img alt="" class="has" height="565" src="https://img-blog.csdn.net/20180826185145517?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NobWlseTQ5OTI4NzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="1194">jar包冲突，删除</p>

<p>/home/hadoop/apps/hbase/lib/slf4j-log4j12-1.7.5.jar</p>

<p>或者 </p>

<p>/home/hadoop/apps/hadoop/share/hadoop/common/lib/slf4j-log4j12-1.7.5.jar </p>

<p>即可解决</p>            </div>
                </div>