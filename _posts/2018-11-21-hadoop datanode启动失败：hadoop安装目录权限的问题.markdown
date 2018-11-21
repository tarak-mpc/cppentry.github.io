---
layout:     post
title:      hadoop datanode启动失败：hadoop安装目录权限的问题
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                安装了伪分布式hadoop，启动时很纠结（很多处提示输入root密码），而且启动后访问namenode的web页面发现没有datanode<br>
原因是：<br>
解压hadoop发布包时su到了root，启动hadoop的时候没有将hadoop安装目录的所有者改成hadoop用户<br><p>解决：</p>
<p></p>
<pre><code class="language-html">$ sudo chown -R hadoop:hadoop /opt/hadoop-0.2.203.0</code></pre>
<br><p>重启hadoop搞定</p>            </div>
                </div>