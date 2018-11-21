---
layout:     post
title:      hadoop做HA后，hbase修改
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。安金龙 的博客。					https://blog.csdn.net/smile0198/article/details/48086533				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>由于hadoop做了HA，namenode可能进行切换，hbase中的配置要做修改：</p>
<p>hbase-site.xml中，rootdir改为和hadoop的dfs.nameservices一样，</p>
<p>并将hadoop的core-site.xml和hdfs-site.xml拷贝到hbase的conf下，然后重启hbase</p>
<p><br></p>
<p>&lt;name&gt;hbase.rootdir&lt;/name&gt;</p>
<p>&lt;value&gt;hdfs://<span style="color:#ff0000;">masters</span>/hbase&lt;/value&gt;</p>
            </div>
                </div>