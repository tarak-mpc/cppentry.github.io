---
layout:     post
title:      Shell脚本(remove-sufixes.sh)—去掉指定文件夹下所有文件的文件名后缀
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>#!/bin/sh<br>
path=/usr/soft_r/nginx/logs/flume<br>
files=$(ls $path)<br>
for filename in $files<br>
do<br>
  mv /usr/soft_r/nginx/logs/flume/$filename /usr/soft_r/nginx/logs/flume/${filename%.*}<br>
done<br>
 </p>

<p> </p>            </div>
                </div>