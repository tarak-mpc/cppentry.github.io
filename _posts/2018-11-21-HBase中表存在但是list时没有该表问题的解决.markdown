---
layout:     post
title:      HBase中表存在但是list时没有该表问题的解决
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/xiaoyancj/article/details/52702696				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-size:14px;">今天在HBase中创建表的时候，提示我表已经存在，大体的错误如下：</span></p>
<p><span style="font-size:14px;"><img src="https://img-blog.csdn.net/20160929160036750?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></p>
<p><span style="font-size:14px;">首先判断出表没有存在，然后去创建，但是创建的时候呢，又发现hbase中该表已经存在。</span></p>
<p><span style="font-size:14px;">解决方法如下：</span></p>
<p></p>
<ol><li><span style="font-size:14px;">hbase zkcli</span></li><li><span style="font-size:14px;">ls /hbase/table</span></li><li><span style="font-size:14px;">rmr /hbase/table/ICD</span></li><li><span style="font-size:14px;">ls /hbase/table</span></li></ol><div><span style="font-size:14px;"><img src="https://img-blog.csdn.net/20160929160401651?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></div>
<div><span style="font-size:14px;">再次执行代码，发现成功。</span></div>
<div><span style="font-size:14px;"><img src="https://img-blog.csdn.net/20160929160638072?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></div>
<p></p>
            </div>
                </div>