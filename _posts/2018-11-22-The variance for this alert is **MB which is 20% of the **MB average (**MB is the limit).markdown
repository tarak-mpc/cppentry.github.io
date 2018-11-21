---
layout:     post
title:      The variance for this alert is **MB which is 20% of the **MB average (**MB is the limit)
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>1、调整如下阀值</p>
<p><img src="https://img-blog.csdn.net/20170901093138304?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvWmhvdXl1YW5MaW5saQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p>2、检查HDFS文件系统使用率，清空HDFS上的.trash垃圾数据</p>
<p></p>
<ol style="color:rgb(51,51,51);font-family:Menlo, Monaco, Consolas, 'Courier New', monospace;font-size:13px;"><li class="L0" style="line-height:20px;list-style-type:none;"><span class="com" style="color:rgb(136,0,0);"># su - hdfs</span></li><li class="L1" style="line-height:20px;list-style-type:none;background:rgb(238,238,238);">
<span class="pln" style="color:rgb(0,0,0);"> </span></li><li class="L2" style="line-height:20px;list-style-type:none;"><span class="com" style="color:rgb(136,0,0);"># hdfs dfs -df -h</span></li></ol><div><span></span># hadoop fs -expunge</div>
<img src="https://img-blog.csdn.net/20170901093453055?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvWmhvdXl1YW5MaW5saQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br><p></p>
<p><br></p>
            </div>
                </div>