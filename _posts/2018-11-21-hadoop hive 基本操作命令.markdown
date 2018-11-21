---
layout:     post
title:      hadoop hive 基本操作命令
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>查询列表 hadoop fs -ls -R /</p>
<p>删除文件 hadoop fs -rm -r /user<br></p>
<p>创建目录 hadoop fs -mkdir /mobile<br></p>
<p>上传文件 hadoop fs -put <span style="color:#ff0000;">/hadoop/hr06_11_05_2013.log</span> /mobile<br></p>
<p>查看文件 hadoop fs -cat /user/hive/warehouse/student<br></p>
<p><br></p>
<p>创建表 create table STUDENT1(name string) row format delimited fields terminated by ',';</p>
<p>导入本地数据 load data local inpath '<span style="color:#ff0000;">D:/Users/bin/student1.txt</span>' overwrite into table student1;</p>
<p><br></p>
            </div>
                </div>