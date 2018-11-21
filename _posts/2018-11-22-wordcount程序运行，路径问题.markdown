---
layout:     post
title:      wordcount程序运行，路径问题
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div id="sina_keyword_ad_area2" class="articalContent newfont_family">配置<br>
1<br>
/home/hadoop/hadoop/jgd/input/test_1/home/hadoop/hadoop/jgd/output/out01<br>
2<br>
hdfs://localhost:9000/user/hadoop/jgd/inputhdfs://localhost:9000/user/hadoop/jgd/output/out5<br><br>
输入路径1，eclipse-wordcount程序可以运行<br>
输入路径2，不可以运行，<br>
解决方案：<br>
localhost 修改为master，集群配置不同，需要根据自己的配置来设定目录<br>
hdfs://master:9000/user/hadoop/jgd/inputhdfs://master:9000/user/hadoop/jgd/output/out5<br><br><br></div>
            </div>
                </div>