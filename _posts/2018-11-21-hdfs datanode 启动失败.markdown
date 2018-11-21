---
layout:     post
title:      hdfs datanode 启动失败
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div style="font-size:14px;color:rgb(63,63,63);line-height:30px;">解决方法：</div>
<div style="font-size:14px;color:rgb(63,63,63);line-height:30px;">
<div>
<div>将hdfs存储数据的所在目录删掉，重新格式化hdfs（相关参数：<span style="color:rgb(0,128,0);">dfs.name.dir  dfs.data.dir</span>）：</div>
<div><span style="color:rgb(0,128,0);">hadoop namenode -format</span></div>
</div>
</div>
            </div>
                </div>