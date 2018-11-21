---
layout:     post
title:      hadoop安装日记（一）：在Ubuntu下创建hadoop用户组和用户
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>本篇文章来源于 Linux公社网站(<a href="http://www.linuxidc.com/" rel="nofollow">www.linuxidc.com</a>)  原文链接：<a href="http://www.linuxidc.com/Linux/2013-01/78112.htm" rel="nofollow">http://www.linuxidc.com/Linux/2013-01/78112.htm</a></p>
<p>1. 创建hadoop用户组：</p>
<p>sudo addgroup hadoop </p>
<p>2. 创建hadoop用户：</p>
<p>sudo adduser -ingroup hadoop hadoop</p>
<p>3. 给hadoop用户添加权限，打开/etc/sudoers文件：</p>
<p>sudo gedit /etc/sudoers</p>
<p>按回车键后就会打开/etc/sudoers文件了，给hadoop用户赋予root用户同样的权限。</p>
<p>在root  ALL=(ALL:ALL)  ALL下添加hadoop  ALL=(ALL:ALL)  ALL，</p>
<p>hadoop  ALL=(ALL:ALL) ALL </p>
<p> </p>
            </div>
                </div>