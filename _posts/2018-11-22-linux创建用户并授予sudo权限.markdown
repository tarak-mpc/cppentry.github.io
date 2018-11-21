---
layout:     post
title:      linux创建用户并授予sudo权限
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p style="line-height:19px;color:rgb(75,75,75);font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;font-size:13px;">
<strong>1. 新建用户</strong></p>
<div class="cnblogs_code" style="background-color:rgb(245,245,245);font-family:'Courier New';border:1px solid rgb(204,204,204);overflow:auto;color:rgb(75,75,75);">
<pre style="font-family:'Courier New';">root@ubuntu:~<span style="line-height:1.5;color:rgb(0,0,0);"># adduser hadoop
root@ubuntu:</span>~# <span style="line-height:1.5;color:rgb(0,0,255);">passwd</span> hadoop #为hadoop用户设置密码</pre>
</div>
<p style="line-height:19px;color:rgb(75,75,75);font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;font-size:13px;">
<strong>2. 为hadoop用户添加sudo权限</strong></p>
<div class="cnblogs_code" style="background-color:rgb(245,245,245);font-family:'Courier New';border:1px solid rgb(204,204,204);overflow:auto;color:rgb(75,75,75);">
<pre style="font-family:'Courier New';">root@ubuntu:~# <span style="line-height:1.5;color:rgb(0,0,255);">sudo</span> vi /etc/sudoers</pre>
</div>
<p style="line-height:19px;color:rgb(75,75,75);font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;font-size:13px;">
把root那行复制后改成hadoop，hadoop就有了sudo权限，需要sudo密码则为ALL，不需要则NOPASSWD:ALL。</p>
<div class="cnblogs_code" style="background-color:rgb(245,245,245);font-family:'Courier New';border:1px solid rgb(204,204,204);overflow:auto;color:rgb(75,75,75);">
<pre style="font-family:'Courier New';">root      ALL=<span style="line-height:1.5;color:rgb(0,0,0);">(ALL)       NOPASSWD:ALL
hadoop    ALL</span>=(ALL:ALL)   ALL</pre>
</div>
            </div>
                </div>