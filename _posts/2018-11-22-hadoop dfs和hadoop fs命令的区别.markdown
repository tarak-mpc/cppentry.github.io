---
layout:     post
title:      hadoop dfs和hadoop fs命令的区别
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/sunny05296/article/details/54340403				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><br></p>
<p>为什么会对同一个功能提供两种命令标记?</p>
FS涉及到一个通用的文件系统，可以指向任何的文件系统如local，HDFS等。但是DFS仅是针对HDFS的。<br><br><p>hadoop fs: 使用面最广，可以操作任何文件系统。</p>
<p>hadoop dfs和hdfs dfs: 只能操作HDFS文件系统相关(包括与Local FS间的操作)，hadoop dfs已经废弃，被hdfs dfs代替。</p>
<p><br></p>
            </div>
                </div>