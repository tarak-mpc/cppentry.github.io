---
layout:     post
title:      hadoop用户使用svn时无法保存密钥的解决办法
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div></div>
groupadd hadoop<br>
useradd -g hadoop hadoop<br>
mkdir /home/hadoop<br>
chown -R hadoop:hadoop  /home/hadoop<br>
usermod -d /home/hadoop hadoop
<div></div>
<div>然后就行了</div>
<div><br></div>
            </div>
                </div>