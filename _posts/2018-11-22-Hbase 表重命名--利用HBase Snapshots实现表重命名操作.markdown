---
layout:     post
title:      Hbase 表重命名--利用HBase Snapshots实现表重命名操作
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>支持hbase 0.94.x<br></p>
<p>1、开启HBase Snapshots<br></p>
<p>&lt;property&gt;<br>
  &lt;name&gt;hbase.snapshot.enabled&lt;/name&gt;<br>
  &lt;value&gt;true&lt;/value&gt;<br>
&lt;/property&gt;</p>
<p>2、重命名表<br></p>
hbase shell&gt; disable 'tablea'<br>
hbase shell&gt; snapshot 'tablea', 'tableaSnapshot'<br>
hbase shell&gt; clone_snapshot 'tableaSnapshot', 'tablea_newname'<br>
hbase shell&gt; delete_snapshot 'tableaSnapshot'<br>
hbase shell&gt; drop 'tablea'
            </div>
                </div>