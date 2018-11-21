---
layout:     post
title:      Spark Shell编写第一个代码
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><strong>Spark Shell编写第一个代码</strong></p>
<p>学习Spark程序开发，首先通过<span style="color:#FF0000;">spark-shell</span><span style="color:#FF0000;">交互式学习</span>，加深Spark程序开发的理解。</p>
<p>尝试<span style="color:#FF0000;">Spark Shell </span><span style="color:#FF0000;">的基本使用</span>。Spark shell 提供了简单的方式来学习API，也提供了交互的方式来分析数据。</p>
<p>Spark Shell 支持 Scala 和 Python，选择使用 Scala结合对文件的简单操作来进行介绍。</p>
<p>（1）启动Hadoop、Spark Shell。注意start-all.sh和spark-shell文件的路径</p>
<p><img src="https://img-blog.csdn.net/20171205170410307?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYnVqaWFuZ2Rhb2xp/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" width="700" height="200" alt=""><br></p>
<p><img src="https://img-blog.csdn.net/20171205170539149?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYnVqaWFuZ2Rhb2xp/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" width="700" height="350" alt=""><br></p>
<p></p>
<p></p>
<p>（2）加载text文件，并执行简单RDD操作。</p>
<p>Spark Shell在启动时自动创建sc，可以加载本地文件和HDFS文件创建RDD。这里用Spark自带的本地文件README.md文件测试。</p>
<p><img src="https://img-blog.csdn.net/20171205170610875?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYnVqaWFuZ2Rhb2xp/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" width="600" height="90" alt=""><br></p>
<p></p>
<p> </p>
<p>注：<span style="background:#FFFF00;">加载</span><span style="background:#FFFF00;">HDFS</span><span style="background:#FFFF00;">文件和本地文件都是使用</span><span style="background:#FFFF00;">textFile</span><span style="background:#FFFF00;">，区别是添加前缀</span><span style="background:#FFFF00;">(hdfs://</span><span style="background:#FFFF00;">和</span><a><span style="color:#000000;background:#FFFF00;">file://)</span><span style="color:#000000;background:#FFFF00;">进行标识</span></a><span style="background:#FFFF00;">。</span></p>
<p> </p>
<p>简单RDD操作如<strong>：</strong></p>
<p>//获取RDD文件textFile的第一行内容</p>
<p>lines.first()</p>
<p>//获取RDD文件textFile所有项的计数</p>
<p>lines.count()</p>
<p>//抽取含有“Spark”的行，返回一个新的RDD</p>
<p>val lineWithSpark = lines.filter(line =&gt;line.contains("Spark"))</p>
<p>//统计新的RDD的行数</p>
<p>lineWithSpark.count()</p>
<p>//找出文本中每行的最多单词数</p>
<p>lines.map(line =&gt; line.split(" ").size).reduce((a,b) =&gt; if (a &gt; b) a else b)</p>
<p><img src="https://img-blog.csdn.net/20171205170642039?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYnVqaWFuZ2Rhb2xp/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" width="600" height="300" alt=""><br></p>
<p></p>
<p> </p>
            </div>
                </div>