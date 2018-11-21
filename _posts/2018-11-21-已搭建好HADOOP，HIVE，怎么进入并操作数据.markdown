---
layout:     post
title:      已搭建好HADOOP，HIVE，怎么进入并操作数据
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
 
<div id="blog_text" class="cnt">
<p>[hadoop@master ~]$ cd $HIVE_HOME                    --进入hive目录<br>
[hadoop@master hive]$ bin/hive                             --进入hive环境</p>
<p>hive&gt; show databases;                          显示已有数据库</p>
<p>hive&gt; use test;                              使用数据库</p>
<p>hive&gt; select * from test_1;             显示表中数据</p>
</div>
            </div>
                </div>