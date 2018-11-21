---
layout:     post
title:      hadoop-hdfs简介(三)
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-size:13.3333px;">1.操作 HDFS 的基本命令</span></p>
<p></p>
<p style="font-size:13.3333px;">1) 打印文件列表（ls）<br></p>
<p style="font-size:13.3333px;">hadoop fs -ls hdfs:/ #hdfs: 明确说明是 HDFS 系统路径<br></p>
<p style="font-size:13.3333px;">简写：hadoop fs -ls / #默认是 HDFS 系统下的根目录<br></p>
<p style="font-size:13.3333px;">hadoop fs -ls /package/test/ #HDFS 系统下某个目录<br></p>
<p style="font-size:13.3333px;">2) 上传文件、目录（put、copyFromLocal）<br></p>
<p style="font-size:13.3333px;">上传新文件：</p>
<p style="font-size:13.3333px;">hdfs fs -put file:/root/test.txt hdfs:/ #上传本地 test.txt 文件到HDFS根目录，HDFS根目录须无同名文件，否则“File exists”</p>
<p style="font-size:13.3333px;">hdfs fs -put test.txt /test2.txt #上传并重命名文件。</p>
<p style="font-size:13.3333px;">hdfs fs -put test1.txt test2.txt hdfs:/ #一次上传多个文件到 HDFS 路径。</p>
<p style="font-size:13.3333px;">上传文件夹：</p>
<p style="font-size:13.3333px;">hdfs fs -put mypkg /newpkg #上传并重命名了文件夹。</p>
<p style="font-size:13.3333px;">覆盖上传：</p>
<p style="font-size:13.3333px;">hdfs fs -put -f /root/test.txt / #如果 HDFS 目录中有同名文件会被覆盖<br></p>
<p style="font-size:13.3333px;">上传文件并重命名：</p>
<p style="font-size:13.3333px;">hadoop fs -copyFromLocal file:/test.txt hdfs:/test2.txt</p>
<p style="font-size:13.3333px;">覆盖上传：</p>
<p style="font-size:13.3333px;">hadoop fs -copyFromLocal -f test.txt /test.txt<br></p>
<p style="font-size:13.3333px;">3) 下载文件、目录（get、copyToLocal）<br></p>
<div>拷贝文件到本地目录1：</div>
<div>hadoop fs -get hdfs:/test.txt file:/root/拷贝文件并重命名，</div>
<div>可以简写：hadoop fs -get /test.txt /root/test.txt<br></div>
<div>拷贝文件到本地目录2：hadoop fs -copyToLocal hdfs:/test.txt file:/root/拷贝文件并重命名，</div>
<div>可以简写：hadoop fs -copyToLocal /test.txt /root/test.txt</div>
<div>4) 拷贝文件、目录（cp）<br></div>
<div>从本地到 HDFS：<br></div>
<div>hadoop fs -cp file:/test.txt hdfs:/test2.txt <br></div>
从 HDFS 到 HDFS：
<p>hadoop fs -cp hdfs:/test.txt hdfs:/test2.txthadoop fs -cp /test.txt /test2.txt<br></p>
<p>5) 移动文件（mv）<br></p>
<p>hadoop fs -mv hdfs:/test.txt hdfs:/dir/test.txt</p>
<p>hadoop fs -mv /test.txt /dir/test.txt<br></p>
<p>6) 删除文件、目录（rm）<br></p>
<p>删除指定文件：</p>
<p>hadoop fs -rm /a.txt</p>
<p>删除全部 txt 文件：</p>
<p>hadoop fs -rm /*.txt</p>
<p>递归删除全部文件和目录：</p>
<p>hadoop fs -rm -R /dir/<br></p>
<p>7) 读取文件（cat、tail）</p>
<p>hadoop fs -cat /test.txt    #以字节码的形式读取</p>
<p>hadoop fs -tail /test.txt</p>
<p>8) 创建空文件（touchz）</p>
<p>hadoop fs - touchz /newfile.txt</p>
<p>9) 创建文件夹（mkdir）</p>
<p>hadoop fs -mkdir /newdir /newdir2    #可以同时创建多个</p>
<p>hadoop fs -mkdir -p /newpkg/newpkg2/newpkg3    #同时创建父级目录</p>
<p>10) 获取逻辑空间文件、目录大小（du）</p>
<p>hadoop fs - du /    #显示 HDFS 根目录中各文件和文件夹大小</p>
<p>hadoop fs -du -h /    #以最大单位显示 HDFS 根目录中各文件和文件夹大小</p>
<p>hadoop fs -du -s / #仅显示 HDFS 根目录大小。即各文件和文件夹大小之和<br></p>
            </div>
                </div>