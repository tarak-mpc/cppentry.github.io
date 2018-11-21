---
layout:     post
title:      Hadoop Linux常用命令
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
启动hadoop：<br>
cd /usr/local/hadoop<br>
./sbin/start-dfs.sh<br>
HDFS上传文件：<br>
./bin/hdfs dfs -put /home/hadoop/myLocalFile.txt  input<br>
HDFS下载文件：<br>
./bin/hdfs dfs -get input/myLocalFile.txt  /home/hadoop/下载<br>
从HDFS中的一个目录拷贝到HDFS中的另外一个目录：<br>
./bin/hdfs dfs -cp input/myLocalFile.txt  /input
            </div>
                </div>