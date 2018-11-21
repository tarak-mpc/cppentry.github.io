---
layout:     post
title:      Spark Streaming运行原理
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p><span style="font-family:'微软雅黑';font-size:15px;text-align:left;background-color:rgb(255,255,255);"><span style="color:#000000;">spark程序是使用一个spark应用实例一次性对一批历史数据进行处理，spark streaming是将持续不断输入的数据流转换成多个batch分</span></span><span style="font-family:'微软雅黑';font-size:15px;text-align:left;"><span style="color:#000000;background-color:rgb(255,255,255);">片，使用一批spark应用实例进行处理。</span></span></p><p><span style="font-family:'微软雅黑';font-size:15px;text-align:left;"><span style="color:#000000;background-color:rgb(255,255,255);"><img src="https://img-blog.csdn.net/2018061814360098?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MTA3MDQzMQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt=""><br></span></span></p><span style="font-family:'微软雅黑';font-size:15px;text-align:left;"></span>            </div>
                </div>