---
layout:     post
title:      Hive执行INSERT overwrite 报错：query returned non-zero code 1
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>解决办法：重新启动hive的<span style="font-family:'微软雅黑';font-size:14px;line-height:21px;">metastore 等的相关进程即可。</span></p>
<p><span style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><span style="font-size:10.5pt;line-height:21px;font-family:'宋体';background-color:inherit;">nohup</span><span style="font-family:'微软雅黑';font-size:14px;line-height:1.5;"> hive --service metastore &amp;</span><br></span></p>
<p><span style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><span style="font-family:'微软雅黑';font-size:14px;line-height:1.5;"><span style="font-family:'宋体';font-size:14px;line-height:21px;">nohup hive --service hiveserver -p 10002 &amp;</span><br></span></span></p>
            </div>
                </div>