---
layout:     post
title:      启动Hive时遇到Caused by: org.apache.hadoop.hdfs.server.namenode.SafeModeException 块丢失的问题
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2><a id="HDFSblock_corrupt_2"></a>HDFS的block corrupt事件</h2>
<p><img src="https://img-blog.csdn.net/20181019203239675?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1MDc4Njg4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="在这里插入图片描述"></p>
<p><img src="https://img-blog.csdn.net/20181019203328657?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1MDc4Njg4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="在这里插入图片描述"></p>
<p>用 hdfs fsck /  检查<br>
<img src="https://img-blog.csdn.net/20181019205107768?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1MDc4Njg4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="在这里插入图片描述"></p>
<p>关掉安全模式<br>
hdfs dfsadmin -safemode leave<br>
<img src="https://img-blog.csdn.net/20181019211942507?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1MDc4Njg4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="在这里插入图片描述"></p>
<p>hadoop  fsck /<br>
用这个命令可以检查整个文件系统的健康状况，但是要注意它不会主动恢复备份缺失的block，这个是由NameNode单独的线程异步处理的。</p>
<p>如果hadoop不能自动恢复，则只能删除 corrupted blocks;<br>
hdfs fsck -delete</p>
<p><a href="http://blog.51cto.com/xiaolanlan/2071642" rel="nofollow">http://blog.51cto.com/xiaolanlan/2071642</a></p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>