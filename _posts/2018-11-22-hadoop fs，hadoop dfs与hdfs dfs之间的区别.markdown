---
layout:     post
title:      hadoop fs，hadoop dfs与hdfs dfs之间的区别
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <ol><li>hadoop fs:使用范围最广</li>
	<li>hadoop dfs:仅仅针对HDFS分布式文件系统</li>
	<li>hdfs dfs:针对HDFS文件系统的操作，官方推荐使用，HDFS中使用hadoop dfs会自动转化成hdfs dfs</li>
</ol><blockquote>
<p>有时会出现WARN hdfs.DFSClient: DFSInputStream has been closed already </p>

<p>说明：This was introduced by <s>HDFS_7494</s>. The easiest thing is to just remove the warning from the code.</p>

<p>最好的方法是删除警告</p>
</blockquote>            </div>
                </div>