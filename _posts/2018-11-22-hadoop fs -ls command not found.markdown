---
layout:     post
title:      hadoop fs -ls command not found
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：分享的快乐。。					https://blog.csdn.net/baolibin528/article/details/43650755				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p style="text-align:center;"><span style="font-size:24px;background-color:rgb(0,153,0);">hadoop fs -ls command not found</span><br></p>
<p><br></p>
<p><span style="background-color:rgb(255,102,0);">问题描述：</span></p>
<p>hadoop fs -ls command not found</p>
<p> <img src="https://img-blog.csdn.net/20150208235211578?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYmFvbGliaW41Mjg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p align="left"></p>
<p> </p>
<p><span style="background-color:rgb(255,102,0);">解决方法：</span></p>
<p><span style="background-color:rgb(255,255,0);">进入hadoop的bin 目录执行： ./hadoop  fs  -ls</span></p>
<p align="left"><img src="https://img-blog.csdn.net/20150208235219001?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYmFvbGliaW41Mjg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p> </p>
<p><span style="background-color:rgb(255,255,0);">不好使的话，把hadoop的bin 路径加入到path里面去：</span></p>
<p>vim /etc/profile</p>
<p><img src="https://img-blog.csdn.net/20150208235217803?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYmFvbGliaW41Mjg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p> </p>
<p>source  /etc/profile</p>
<p> </p>
<p> </p>
<p> </p>
<p> </p>
<p> </p>
<p> </p>
<p> </p>
<p> </p>
            </div>
                </div>