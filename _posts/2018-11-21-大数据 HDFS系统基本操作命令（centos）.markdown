---
layout:     post
title:      大数据 HDFS系统基本操作命令（centos）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>1. hadoop fs -ls  /     文件查找</p><p>    hadoop fs -ls -R  /    递归查看</p><p>  hadoop fs -put  文件名  /     将文件传到根节点</p><p>  hadoop fs  -cat  /hello.txt    查看根节点hello.txt文件内容（hadoop fs -text  作用相同）</p><p>  hadoop fs -mkdir  /test/  创建文件夹</p><p>  hadoop fs -get /hello.txt   赋值文件到本地</p><p>  hadoop fs -rm  -R   /test/  删除文件夹</p>            </div>
                </div>