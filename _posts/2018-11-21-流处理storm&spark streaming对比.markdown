---
layout:     post
title:      流处理storm&spark streaming对比
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>之前的项目里用到的是storm处理流数据（从kafka读出，粗略转换后放入hbase），spark单单进行计算的，近来各位大神考虑到了用spark streaming来处理流数据，网上搜罗了各种资源，总的来说，Strom的实时性更好（spark是批处理），spark streaming的扩展性、吞吐性更好。自己没有真正码过这些组件，就不多信口了。</p>
<p><a href="https://www.cnblogs.com/yaohaitao/p/5703288.html" rel="nofollow">spark和storm的对比</a></p>
<p><a href="http://blog.csdn.net/yangbutao/article/details/44538637" rel="nofollow">Spark streaming&amp;storm流计算的相关对比</a><br></p>
<p><a href="https://www.csdn.net/article/2014-08-04/2821018/2" rel="nofollow">从Storm和Spark 学习流式实时分布式计算的设计</a><br></p>
            </div>
                </div>