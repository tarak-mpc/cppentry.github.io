---
layout:     post
title:      apache kafka系列之性能优化架构分析
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/lizhitao/article/details/38439769				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-size:24px;color:rgb(51,51,51);line-height:1.5;">Apache kafka性能优化架构分析</span></p>
<p><img src="https://img-blog.csdn.net/20140808151200678?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGl6aGl0YW8=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br></p>
<p><span style="font-size:18px;color:rgb(51,102,255);">应用程序优化：数据压缩</span></p>
<p><br></p>
<p><img src="https://img-blog.csdn.net/20140808151242190?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGl6aGl0YW8=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br></p>
<p><br></p>
<p><br></p>
<p><img src="https://img-blog.csdn.net/20140808151306479?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGl6aGl0YW8=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br></p>
<p><br></p>
<p><br></p>
<p><br></p>
<p><img src="https://img-blog.csdn.net/20140808151409503?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGl6aGl0YW8=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br></p>
<p><br></p>
<p><br></p>
<p><br></p>
<p><img src="https://img-blog.csdn.net/20140808151517831?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGl6aGl0YW8=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br></p>
<p><span style="font-size:18px;">consumer offset默认情况下是定时批量更新topics的partitions offset值</span></p>
<p><br></p>
            </div>
                </div>