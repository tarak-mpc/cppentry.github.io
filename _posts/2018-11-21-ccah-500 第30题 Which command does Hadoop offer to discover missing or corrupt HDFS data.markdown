---
layout:     post
title:      ccah-500 第30题 Which command does Hadoop offer to discover missing or corrupt HDFS data
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，转载请注明。					https://blog.csdn.net/tianbaochao/article/details/51672717				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p class="p" style="text-indent:0pt;vertical-align:baseline;">
<span style="font-family:'宋体';color:rgb(67,74,83);letter-spacing:0pt;font-size:12pt;">30.Which command does Hadoop offer to discover missing or corrupt HDFS data?</span><span style="font-family:'宋体';color:rgb(67,74,83);letter-spacing:0pt;font-size:12pt;"></span></p>
<p class="p" style="text-indent:0pt;vertical-align:baseline;">
<span style="font-family:'宋体';color:rgb(67,74,83);letter-spacing:0pt;font-size:12pt;">A. Hdfs fs -du
</span><span style="font-family:'宋体';color:rgb(67,74,83);letter-spacing:0pt;font-size:12pt;"></span></p>
<p class="p" style="text-indent:0pt;vertical-align:baseline;">
<span style="font-family:'宋体';color:rgb(67,74,83);letter-spacing:0pt;font-size:12pt;">B. Hdfs fsck</span><span style="font-family:'宋体';color:rgb(67,74,83);letter-spacing:0pt;font-size:12pt;"></span></p>
<p class="p" style="text-indent:0pt;vertical-align:baseline;">
<span style="font-family:'宋体';color:rgb(67,74,83);letter-spacing:0pt;font-size:12pt;">C. Dskchk
</span><span style="font-family:'宋体';color:rgb(67,74,83);letter-spacing:0pt;font-size:12pt;"></span></p>
<p class="p" style="text-indent:0pt;vertical-align:baseline;">
<span style="font-family:'宋体';color:rgb(67,74,83);letter-spacing:0pt;font-size:12pt;">D. The map-only checksum
</span><span style="font-family:'宋体';color:rgb(67,74,83);letter-spacing:0pt;font-size:12pt;"></span></p>
<p class="p" style="text-indent:0pt;vertical-align:baseline;">
<span style="font-family:'宋体';color:rgb(67,74,83);letter-spacing:0pt;font-size:12pt;">E. Hadoop does not provide any tools to discover missing or corrupt data; there is not need because three replicas are kept for each data block</span><span style="font-family:'宋体';color:rgb(67,74,83);letter-spacing:0pt;font-size:12pt;"></span></p>
<p class="p" style="text-indent:0pt;vertical-align:baseline;">
<span style="font-family:'宋体';color:rgb(67,74,83);letter-spacing:0pt;font-size:12pt;">Answer: B</span><span style="font-family:'宋体';color:rgb(67,74,83);letter-spacing:0pt;font-size:12pt;"></span></p>
<p class="p" style="text-indent:0pt;vertical-align:baseline;">
<span style="font-family:'宋体';color:rgb(67,74,83);letter-spacing:0pt;font-size:12pt;"> </span></p>
<p class="p" style="text-indent:0pt;vertical-align:baseline;">
<span style="font-family:'宋体';color:rgb(67,74,83);letter-spacing:0pt;font-size:12pt;">explanation:</span><span style="font-family:'宋体';color:rgb(67,74,83);letter-spacing:0pt;font-size:12pt;"></span></p>
<p class="p" style="text-indent:0pt;vertical-align:baseline;">
<span style="font-family:'宋体';color:rgb(67,74,83);letter-spacing:0pt;font-size:12pt;">A,C<span style="font-family:'宋体';">没有此命令</span></span><span style="font-family:'宋体';color:rgb(67,74,83);letter-spacing:0pt;font-size:12pt;"></span></p>
<p class="p" style="text-indent:0pt;vertical-align:baseline;">
<span style="font-family:'宋体';color:rgb(67,74,83);letter-spacing:0pt;font-size:12pt;">Reference:https://twiki.grid.iu.edu/bin/view/Storage/HadoopRecovery</span><span style="font-family:'宋体';color:rgb(67,74,83);letter-spacing:0pt;font-size:12pt;"></span></p>
<p class="p" style="text-indent:0pt;vertical-align:baseline;">
<span style="font-family:'宋体';color:rgb(67,74,83);letter-spacing:0pt;font-size:12pt;">Hadoop's fsck utility will show Status: CORRUPT in its output whenever a file is corrupt. The following command will display the status for your entire filesystem namespace.</span><span style="font-family:'宋体';color:rgb(67,74,83);letter-spacing:0pt;font-size:12pt;"></span></p>
<p class="p" style="text-indent:0pt;vertical-align:baseline;">
<span style="font-family:'宋体';color:rgb(67,74,83);letter-spacing:0pt;font-size:12pt;"> <img src="https://img-blog.csdn.net/20160614162544554?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></p>
            </div>
                </div>