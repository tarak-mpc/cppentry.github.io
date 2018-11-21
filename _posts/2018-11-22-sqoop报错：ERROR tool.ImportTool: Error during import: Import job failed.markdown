---
layout:     post
title:      sqoop报错：ERROR tool.ImportTool: Error during import: Import job failed
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/fibonacci2015/article/details/79283687				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div style="text-indent:28px;line-height:1.5;font-size:14px;">
可能是文件权限</div>
<div style="text-indent:28px;line-height:1.5;font-size:14px;">
修改hive数据仓库权限</div>
<div style="text-indent:28px;line-height:1.5;font-size:14px;">
hadoop fs -chmod 777 /data/hive</div>
<div style="text-indent:28px;line-height:1.5;font-size:14px;">
hadoop fs -chmod 777 /data/hive/warehouse</div>
            </div>
                </div>