---
layout:     post
title:      hadoop创建文件夹及将本地文件移动到hdfs中
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/hjtlovelife/article/details/52496065				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
[root@master localfiles]# hdfs dfs -mkdir /user<br>
[root@master localfiles]# hdfs dfs -mkdir /user/root<br>
[root@master localfiles]# hdfs dfs -mkdir /user/root/input<br>
[root@master localfiles]# hdfs dfs -mkdir /user/root/output<br>
[root@master localfiles]# hdfs dfs -put ./testHdfs.txt /user/root/input
            </div>
                </div>