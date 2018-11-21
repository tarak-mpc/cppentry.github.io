---
layout:     post
title:      hadoop fs -mkdir 报错 No such file or directory
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/xiakexiaohu/article/details/54574859				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><strong><span style="font-size:24px;">问题：$hadoop fs -mkdir /home/hadoop/hadoop/input</span></strong></p>
<p><strong><span style="font-size:24px;">（版本 hadoop 2-7-3）</span></strong></p>
<p><span style="font-size:24px;"><strong>报错：mkdir '/home/hadoop/hadoop/input' No such file or directory</strong></span></p>
<p><span style="font-size:24px;color:#ff0000;"><strong>解决方案：$hadoop fs -mkdir -p /home/hadoop/hadoo/input</strong></span></p>
            </div>
                </div>