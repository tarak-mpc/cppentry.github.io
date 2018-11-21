---
layout:     post
title:      用Python连接HDFS
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/qq_32284189/article/details/81262802				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p style="margin-left:0pt;">如果各位目前有用到大数据技术，相信很多的结果写在hdfs是最方便也最快速的。</p>

<p style="margin-left:0pt;">本篇博客就主要讲一下怎么利用python直接去连接HDFS文件系统，并对文件进行操作。</p>

<p style="margin-left:0pt;">Python连接hbase需要一个三方库（这里python版本2.7）hdfs，利用pip install hdfs即可下载安装。</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;">配置好HDFS Url：</p>

<pre class="has">
<code class="language-python">HDFSUrl = "http://***.***.***.**:50070"</code></pre>

<p style="margin-left:0pt;">配置HDFS文件所在的目录：</p>

<pre class="has">
<code class="language-python">inputpath = "/market/data/output/"</code></pre>

<p style="margin-left:0pt;">建立连接：</p>

<pre class="has">
<code class="language-python">client = hdfs.Client(HDFSUrl, root='/')</code></pre>

<p>对文件进行操作：</p>

<pre class="has">
<code class="language-python"># 获取output文件夹下所有的文件夹名称
fs = client.list(inputpath)
for rootqvalues, dirsqvalues, filesqvalues in client.walk(qvalues_path):
    # 获取目录下 路径 文件夹  以及文件
    print rootqvalues
    print dirsqvalues
    print filesqvalues </code></pre>

<p> </p>            </div>
                </div>