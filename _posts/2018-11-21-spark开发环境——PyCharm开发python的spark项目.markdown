---
layout:     post
title:      spark开发环境——PyCharm开发python的spark项目
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/qq_38628350/article/details/79087192				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>1.点击运行-编辑结构</p>
<p><img src="https://img-blog.csdn.net/20180117171115946?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMzg2MjgzNTA=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br></p>
<p>2.在python的环境变量中新加一个PYTHONPATH</p>
<p>在其值中加入本地的spark中的python文件夹</p>
<p>D:\spark\spark-2.2.0-bin-hadoop2.6\python</p>
<p>以及本地的spark中的python文件夹中的lib下的<span style="color:rgb(79,79,79);text-align:justify;">py4j-0.10.4-src.zip    </span></p>
<p>D:\spark\spark-2.2.0-bin-hadoop2.6\python\lib\py4j-0.10.4-src.zip</p>
<p><img src="https://img-blog.csdn.net/20180117171610311?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMzg2MjgzNTA=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br></p>
<p><img src="https://img-blog.csdn.net/20180117171832462?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMzg2MjgzNTA=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br></p>
<p><img src="https://img-blog.csdn.net/20180117171855339?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMzg2MjgzNTA=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br></p>
<p>3.项目关联py4j-some-version.zip和pyspark.zip两个zip包</p>
<p>打开文件-设置-项目-ProjecStructure，选择Add Content Root，选中spark安装目录下的python中的lib中的两个zip包</p>
<p><img src="https://img-blog.csdn.net/20180117172322948?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMzg2MjgzNTA=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br></p>
<p><br></p>
<p><br></p>
            </div>
                </div>