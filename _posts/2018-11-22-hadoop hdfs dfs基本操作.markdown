---
layout:     post
title:      hadoop hdfs dfs基本操作
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/u011204487/article/details/81327650				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>hdfs version:查看版本</p>

<p>1、查询</p>

<p>hdfs dfs -ls / ：列出/目录下的文件和目录</p>

<p>hdfs dfs -ls -R / ：列出/目录下的所有文件，由于有-R参数，会在文件夹和子文件夹下执行ls操作。</p>

<p>2、新建文件夹</p>

<p>hdfs dfs -mkdir -p /home/testdata/</p>

<p>3、查看hdfs文件中的内容</p>

<p>hdfs dfs -cat /home/testdata/1.txt</p>

<p>4、删除文件</p>

<p>hdfs dfs -rm -f /home/testdata/1.txt</p>

<p>5、删除文件夹</p>

<p>hdfs dfs -rm -r /home/testdata</p>            </div>
                </div>