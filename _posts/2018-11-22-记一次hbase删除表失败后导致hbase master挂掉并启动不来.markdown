---
layout:     post
title:      记一次hbase删除表失败后导致hbase master挂掉并启动不来
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>hbase删除表，失败后导致hbase master启动不来的问题<br>
在hbase shell中执行 disable ‘Tag_data_backups’<br>
然后执行drop ‘Tag_data_backups’<br>
卡了很久，一直到失败，并导致hbase master挂了，并且启动失败。<br>
hbase master报错如下：<br>
Failed to archive class org.apache.hadoop.hbase.backup.HFileArchiver$FileablePath, file:hdfs://master:8020/hbase/.tmp/data/default/Tag_data_backups/0ffe0ff7a79dd92c21e6a40cb947a9df/recovered.edits/11729848.seqid<br>
<img src="https://img-blog.csdnimg.cn/20181113153412959.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjgzODE1OA==,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"></p>
<p>解决办法：<br>
删除上图画红线的/hbase/.tmp/data/default/下Tag_data_backups文件夹，然后重启hbase就可以了。</p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>