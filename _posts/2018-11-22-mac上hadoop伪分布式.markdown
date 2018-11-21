---
layout:     post
title:      mac上hadoop伪分布式
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;">
一.首先安装一下Hadoop</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;">
</p><pre><code class="language-plain">http://www.cnblogs.com/micrari/p/5716851.html</code></pre>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;">
<br></p>
二.<span style="font-family:Verdana, Arial, Helvetica, sans-serif;">配置hadoop</span>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;">
<span style="font-family:Verdana, Arial, Helvetica, sans-serif;">http://www.cnblogs.com/aijianiula/p/3842581.html<br></span></p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;">
<span style="font-family:Verdana, Arial, Helvetica, sans-serif;"><br></span></p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;">
三、问题</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;">
</p>
<p class="p1"><span class="s1"></span></p>
<p style="font-family:Arial;font-size:14px;">
<strong><span style="font-size:24px;">问题：$<a href="http://lib.csdn.net/base/hadoop" rel="nofollow" class="replace_word" title="Hadoop知识库" style="color:rgb(223,52,52);">Hadoop</a> fs -mkdir /home/hadoop/hadoop/input</span></strong></p>
<p style="font-family:Arial;font-size:14px;">
<strong><span style="font-size:24px;">（版本 hadoop 2-7-3）</span></strong></p>
<p style="font-family:Arial;font-size:14px;">
<span style="font-size:24px;"><strong>报错：mkdir '/home/hadoop/hadoop/input' No such file or directory</strong></span></p>
<p style="font-family:Arial;font-size:14px;">
<span style="font-size:24px;color:rgb(255,0,0);"><strong>解决方案：$hadoop fs -mkdir -p /home/hadoop/hadoo/input</strong></span></p>
<p style="font-family:Arial;font-size:14px;">
<span style="font-size:24px;color:rgb(255,0,0);"><strong><br></strong></span></p>
<p style="font-family:Arial;font-size:14px;">
<span style="font-size:24px;color:rgb(255,0,0);"><strong><br></strong></span></p>
<br><p style="font-family:Verdana, Arial, Helvetica, sans-serif;">
<br></p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;">
<br></p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;">
<br><br></p>
            </div>
                </div>