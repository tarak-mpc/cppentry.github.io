---
layout:     post
title:      一：hadoop和spark的区别
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/yuming226/article/details/78805977				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-size:18px;">hadoop是基于磁盘的，它的运算结果保存在磁盘当中；而spark的运算是基于内存的。因此spark的运算速度是</span></p>
<p><span style="font-size:18px;">hadoop的100倍；即使在磁盘当中运算，spark也是hadoop的10倍左右，原因就是spark具有优秀的作业调度策略。</span></p>
<p><span style="font-size:18px;">故spark的速度快与hadoop的两个原因：</span></p>
<p><span style="font-size:18px;">（1）spark是基于内存，hadoop基于磁盘：</span></p>
<p><img src="https://img-blog.csdn.net/20171223213109372?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveXVtaW5nMjI2/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p></p>
<div><span style="font-size:18px;">在hadoop中HDFS用于数据的存储，MapReduce用于计算。</span></div>
<div><span style="font-size:18px;">MapReduce：从磁盘中读取文件-&gt;把迭代结果存储到磁盘中-&gt;再从磁盘中读取文件-&gt;..................</span></div>
<div><span style="font-size:18px;">Spark:从磁盘中读取文件-&gt;把迭代后的结果存储到内存当中-&gt;再从内存中读取数据-&gt;..................</span></div>
<div><span style="font-size:18px;">Spark的计算速度是hadoop的100倍，即使再磁盘中操作数据也是hadoop'的10倍。</span></div>
<p></p>
<p></p>
<p><span style="font-size:18px;">（2）spark具有优秀的作业调度</span><span style="font-size:18px;">策略（使用了有向无环图，关于这部分会在RDD学习模块中介绍）。</span></p>
<br>            </div>
                </div>