---
layout:     post
title:      idea项目运行时报错提示Could not locate executable null\bin\winutils.exe in the Hadoop binaries
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>1.需要在java项目中添加配置：System.setProperty("hadoop.home.dir","C:\hadoop解压后的路径")</p><p>（前提：将winutils.exe程序添加到hadoop解压后的路径中）</p><p>2.配置hadoop的环境变量</p><p>（包含winutils.exe程序的hadoop解压路径H:\hadoop\bin）</p>            </div>
                </div>