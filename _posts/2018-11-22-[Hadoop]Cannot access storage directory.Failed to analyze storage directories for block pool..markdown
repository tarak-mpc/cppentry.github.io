---
layout:     post
title:      [Hadoop]Cannot access storage directory.Failed to analyze storage directories for block pool.
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/Balmunc/article/details/81137625				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>DataNode启动不了</p>

<p>vim hadoop-hadoop-datanode-slave.log</p>

<p><img alt="" class="has" src="https://img-blog.csdn.net/20180720192711944?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0JhbG11bmM=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></p>

<p>关掉hadoop，删掉hadoop/data下的文件，重新启动Hadoop即可。</p>            </div>
                </div>