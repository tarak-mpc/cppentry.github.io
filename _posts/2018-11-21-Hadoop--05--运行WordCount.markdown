---
layout:     post
title:      Hadoop--05--运行WordCount
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/baidu_21578557/article/details/51959642				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><strong><span style="color:#252525;">1、  </span><span style="color:rgb(37,37,37);">启动Hadoop</span>：</strong></p>
<p><strong><span style="color:rgb(37,37,37);">Start-all.sh</span></strong></p>
<p><strong>2、 <span style="color:rgb(37,37,37);">创建file </span>文件夹,并随便写些内容</strong></p>
<p><strong><span style="color:rgb(37,37,37);">Mkdir /hadoop/test.txt</span></strong></p>
<p><strong><span style="color:#252525;">3、  </span><span style="color:rgb(37,37,37);">在HDFS</span>上创建输入文件夹目录 input ：</strong></p>
<p><strong><span style="color:rgb(37,37,37);">Hadoop fs –put /hadoop/test.txt/input</span></strong></p>
<p><strong>4、 <span style="color:rgb(37,37,37);">Hadoop</span><span style="color:rgb(37,37,37);">自带的运行 wordcount
</span>例子的 jar 包：</strong></p>
<p><strong>hadoop-mapreduce-examples-2.6.0.jar</strong></p>
<p><strong><img src="https://img-blog.csdn.net/20160719212019492?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></strong></p>
<p><strong></strong></p>
<p><strong>5、 <span style="color:rgb(37,37,37);">开始运行 wordcount</span>：</strong></p>
<p><strong>hadoop jar hadoop-mapreduce-examples-2.6.0.jarwordcount</strong></p>
<p><strong> s/input /output/wordcount1</strong></p>
<p>6、  <span style="color:rgb(37,37,37);">查看运行结果</span></p>
<p><span style="color:rgb(37,37,37);"><img src="https://img-blog.csdn.net/20160719212030258?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></p>
<p></p>
            </div>
                </div>