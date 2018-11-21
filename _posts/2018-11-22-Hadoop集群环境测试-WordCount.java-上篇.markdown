---
layout:     post
title:      Hadoop集群环境测试-WordCount.java-上篇
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-size:14px;"><strong><span style="color:#000000;">一. 验证</span><span style="color:#000000;">hadoop</span><span style="color:#000000;">的</span><span style="color:#000000;">hdfs</span><span style="color:#000000;">文件系统功能是否正常</span></strong></span></p>
<p><span style="font-size:14px;">安装好Hadoop后，其hdfs文件系统是空的。Hdfs实现文件的管理操作。此时，查看hdfs目录，将出现“cannot access …”错误。如下：</span></p>
<p><img src="http://img.my.csdn.net/uploads/201302/01/1359705524_9346.JPG" alt=""></p>
<p><span style="color:#000000;"><span style="font-family:'Times New Roman';font-size:14px;">1.</span>      
</span><span style="font-size:14px;"><span style="color:#000000;">从</span><span style="color:#000000;"><span style="font-family:'Times New Roman';">linux</span></span><span style="color:#000000;">系统中将文件</span><span style="color:#000000;"><span style="font-family:'Times New Roman';">copy</span></span><span style="color:#000000;">到</span><span style="color:#000000;"><span style="font-family:'Times New Roman';">hadoop</span></span><span style="color:#000000;">的</span><span style="color:#000000;"><span style="font-family:'Times New Roman';">hdfs</span></span><span style="color:#000000;">中。</span></span></p>
<p><span style="font-size:14px;"><span style="color:#000000;">首先，在</span><span style="color:#000000;"><span style="font-family:'Times New Roman';">linux</span></span><span style="color:#000000;">系统中常见</span><span style="color:#000000;"><span style="font-family:'Times New Roman';">input</span></span><span style="color:#000000;">（或</span><span style="color:#000000;"><span style="font-family:'Times New Roman';">file</span></span><span style="color:#000000;">）文件夹，并在其中创建</span><span style="color:#000000;"><span style="font-family:'Times New Roman';">test1.txt</span></span><span style="color:#000000;">和</span><span style="color:#000000;"><span style="font-family:'Times New Roman';">test2.txt</span></span><span style="color:#000000;">（也可以创建多个），即：</span></span></p>
<p><img src="http://img.my.csdn.net/uploads/201302/01/1359705565_7041.JPG" alt=""></p>
<p><img src="http://img.my.csdn.net/uploads/201302/01/1359705574_2309.JPG" alt=""></p>
<p>接着，将这些文件上传到hdfs文件系统中，如下。</p>
<p><img src="http://img.my.csdn.net/uploads/201302/01/1359705603_8156.JPG" alt=""></p>
<p>析：dfs程序带的一个参数，主要表示要操作的是dfs文件系统。-put表示要“上传”这个动作。./表示上传的源文件路径，该例中，表示要将file目录下的所有文件上传，所以是./。input表示上传文件的目的地址，即此时上传至hdfs的主目录下的input下。</p>
<p><span style="font-size:14px;">显示hdfs文件系统目录：</span></p>
<p><img src="http://img.my.csdn.net/uploads/201302/01/1359705638_6396.JPG" alt=""></p>
<p>显示input文件夹内容：</p>
<p><img src="http://img.my.csdn.net/uploads/201302/01/1359705667_2025.JPG" alt=""></p>
<p><span style="font-size:14px;">删除input文件夹：</span></p>
<p><img src="http://img.my.csdn.net/uploads/201302/01/1359705700_5338.JPG" alt=""></p>
            </div>
                </div>