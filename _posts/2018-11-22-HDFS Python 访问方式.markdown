---
layout:     post
title:      HDFS Python 访问方式
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：转载请附此链接https://blog.csdn.net/qq_42393859					https://blog.csdn.net/qq_42393859/article/details/82767423				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p># python3  hdfs 模块<br>
1. pip 安装hdfs模块 <br>
# 获得HDFS 连接<br>
from hdfs import Client<br>
client = Client("http://ip:50070")  （ip为你自己的ip或映射）</p>

<p>#等同于  bin/hdfs dfs -ls /<br>
  files = client.list("/")<br>
  print(files)</p>

<p>  #文件上传，但是需要注意  修改hdfs访问权限<br>
  client.upload("/suns","suns")</p>

<p>  #文件下载<br>
  client.download("/suns/suns",'xiaohei')</p>

<p>  #删除文件<br>
  client.delete("/suns/suns")<br>
  client.delete("/suns",True)</p>

<p>  #新建文件目录<br>
  client.makedirs("hdfs_path")<br>
  #文件改名，改路径<br>
  client.rename()<br>
  ```</p>            </div>
                </div>