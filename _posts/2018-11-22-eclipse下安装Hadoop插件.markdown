---
layout:     post
title:      eclipse下安装Hadoop插件
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/AC_great/article/details/47297815				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
whereis 文件名      结果：产生文件路径 <br>
find / -name 文件名    结果：产生文件路径 <br>
hadoop fs -mkdir <br>
hadoop fs -put 文件路径名   主机下的文件路径名 <br>
hadoop fs -ls /  查看当前路径下的所有文件<br><br>
eclipse下安装hadoop插件<br><br><p>复制hadoop目录下的hadoop-eclipse-plugin-0.6.0.jar   JAR包放在/usr/local/eclipse/plugins中，重启eclipse</p>
<p>另外，我没找到我的hadoop目录下有这个插件，所以。。。。我也是从网上用积分下载下来的，我就分享一下</p>
<p>适用于hadoop2.6.0的插件包吧</p>
<p>http://download.csdn.net/detail/ac_great/8963413<br></p>
<br><img src="https://img-blog.csdn.net/20150805152138651?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br><br><br><p><img src="https://img-blog.csdn.net/20150805152214839?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p>右键项目刷新即可查看到服务器中的文件，还可以直接在上面进行下载、创建、上传、删除等操作：</p>
<p><br></p>
            </div>
                </div>