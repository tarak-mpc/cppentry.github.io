---
layout:     post
title:      Spark学习笔记 --- spark RDD加载文件
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：学习交流为主，未经博主同意禁止转载，禁止用于商用。					https://blog.csdn.net/u012965373/article/details/62215885				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-size:18px;">对于Spark RDD加载的文件，可以分为几类：</span></p>
<p><span style="font-size:18px;"><br></span></p>
<p><span style="font-size:18px;">首先加载本地文件，加载方式：</span></p>
<p><br></p>
<p></p><pre><code class="language-plain">val localFileRDD = sc.textFile("file://usr/wordcount.txt")</code></pre>
<p><br></p>
<span style="font-size:18px;"><br>
其次加载hdfs文件，加载方式：</span>
<p><br></p>
<p></p><pre><code class="language-plain">val textHDFSFile = sc.textFile("/user/README.md")</code></pre><br><br><p><span style="font-size:18px;">或者：</span></p>
<p><br></p><pre><code class="language-plain">val textHDFSFile1 = sc.textFile("hdfs://user/README.md")</code></pre><br><p><br></p>
<p><br></p>
            </div>
                </div>