---
layout:     post
title:      HDFS 常用Shell命令
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/qq_28633249/article/details/78048051				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h1 align="center"><strong>HDFS Shell命令</strong></h1>
<h2><strong>概述</strong></h2>
<p>HDFS Shell<span style="font-family:'等线';">命令允许使用命令行在</span>HDFS存储中进行文件夹和文件操作。</p>
<p>如文件夹的增删改查、文件的增删改查等。</p>
<p> </p>
<p><strong><span style="color:rgb(255,0,0);"><span style="font-family:'宋体';">开始练习</span>hadoop时，打开Linux之后要用 start-dfs.sh </span><span style="color:rgb(255,0,0);"><br></span><span style="color:rgb(255,0,0);"><br></span><span style="color:rgb(255,0,0);"> 启动HDFS。</span><span style="color:rgb(255,0,0);"><br></span><span style="color:rgb(255,0,0);">HDFS不是开机启动的 </span></strong></p>
<p> <img src="https://img-blog.csdn.net/20170921092917721?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMjg2MzMyNDk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<h2><strong>常用命令</strong></h2>
<p><strong>命令格式</strong></p>
<p>hdfs dfs -<span style="font-family:'等线';">操作命令</span> <span style="font-family:'等线';">
参数</span></p>
<p><span style="font-family:'等线';">前面的【</span>hdfs dfs -】部分是固定的，后面的【操作命令 参数】部分是变化的</p>
<p> </p>
<p><strong><span style="font-family:'等线';">命令练习（练习时可结合</span>Hadoop站点观察操作结果）</strong></p>
<p><strong></strong></p>
<p>hdfs dfs -mkdir /abc #<span style="font-family:'等线';">创建名为</span>/abc的文件夹</p>
<p>hdfs dfs -ls / 0 #列出根目录中的内容</p>
<p>hdfs dfs -ls -R / #递归列出多层文件夹的内容</p>
<p>hdfs dfs -put /etc/hosts /abc/hosts #把Linux<span style="font-family:'等线';">系统中</span>/etc/hosts<span style="font-family:'等线';">文件上传到</span>HDFS中</p>
<p>hdfs dfs -appendToFile /etc/hosts /abc/hosts</p>
<p>#向文件中追加内容</p>
<p>hdfs dfs -checksum /abc/hosts #查看文件的MD5值</p>
<p>hdfs dfs -du -h / #<span style="font-family:'等线';">查看文件</span>/文件夹的大小</p>
<p>#-h以人类友好的方式显示大小（过大时带单位）</p>
<p>hdfs dfs -get /abc/hosts ./hosts #把HDFS<span style="font-family:'等线';">中的文件下载到本地</span>Linux中</p>
<p>#注意./hosts是下载后保存到本地的位置</p>
<p>hdfs dfs -cat /abc/hosts #查看HDFS中文本文件的内容</p>
<p>#注意：只能查看文件文件</p>
<p>hdfs dfs -tail /abc/hosts #<span style="font-family:'等线';">列出文件结尾处</span>1KB的文件内容</p>
<p>hdfs dfs -mv /abc/hosts /abc/xyz #修改文件名字或移动位置</p>
<p>hdfs dfs -cp /abc/xyz /abc/hosts #复制文件</p>
<p>hdfs dfs -find / -name xyz #查找名字为xyz的文件的位置</p>
<p>hdfs dfs -rmdir /abc #删除名为/abc的文件夹</p>
<p>#注意：如果其中还有文件则不能删除</p>
<p>hdfs dfs -rm /abc/hosts #删除文件</p>
<p>hdfs dfs -rm -r /abc #<span style="font-family:'等线';">递归删除文件</span>/文件夹，文件夹中有文件也能删除</p>
<p>hdfs dfs -df #查看HDFS文件系统的磁盘使用情况</p>
<p></p>
<p></p>
<p> </p>
<p></p>
<p></p>
            </div>
                </div>