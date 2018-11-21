---
layout:     post
title:      Hive启动Error：Found class jline.Terminal, but interface was expected
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <div class="iteye-blog-content-contain">
<p style="font-size:14px;">Error<br><img src="http://dl2.iteye.com/upload/attachment/0116/6018/d52fb04d-22f6-30f2-ad6f-fcfbccba2830.png" alt=""><br> </p>
<p style="font-size:14px;">Cause —— Hadoop里面有老版本的jline</p>
<p style="font-size:14px;"><img src="http://dl2.iteye.com/upload/attachment/0116/6020/4a352221-d867-3c7e-a6dd-e531f03c6e3e.png" alt=""><br> </p>
<p style="font-size:14px;">Solution</p>
<pre class="shell">cp jline-2.12.jar /home/hadoop/app/hadoop-2.6.4/share/hadoop/yarn/lib
rm /home/hadoop/app/hadoop-2.6.4/share/hadoop/yarn/lib/jline-0.9.94.jar</pre>
<p style="font-size:14px;"><img src="http://dl2.iteye.com/upload/attachment/0116/6022/3d5ec5e0-78be-3381-a840-173f355ffb47.png" alt=""><br> </p>
<p style="font-size:14px;">重新启动Hive</p>
<p style="font-size:14px;"><img src="http://dl2.iteye.com/upload/attachment/0116/6024/6042ae4b-0a91-3f34-952c-002eed6a5eef.png" alt=""><br> </p>
<p style="font-size:14px;"> </p>
<p style="font-size:14px;"> </p>
</div>            </div>
                </div>