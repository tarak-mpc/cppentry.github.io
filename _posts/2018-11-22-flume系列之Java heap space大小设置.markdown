---
layout:     post
title:      flume系列之Java heap space大小设置
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/welcome66/article/details/73225609				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>当使用flume-ng进行日志采集的时候，如果日志文件很大，容易导致flume出现：</p>
<p><span style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;">java.lang.OutOfMemoryError: Java heap space</span><br></p>
<p><span style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;">这样的错误，这就需要调整flume相应的jvm启动参数。</span></p>
<p><span style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;"><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;">修改 flume下的conf/flume-env.sh文件：</span></span></p>
<p><span style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;"> export JAVA_OPTS="-Xms512m -Xmx1024m -Dcom.sun.management.jmxremote"</span></p>
<p><span style="color:rgb(51,51,51);font-size:14px;"><span style="font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;">其中：<br style="color:rgb(51,51,51);font-size:14px;"></span><span style="font-family:Arial;color:rgb(51,51,51);font-size:14px;">-Xms&lt;size&gt;        set initial Java heap size.........................</span><br style="color:rgb(51,51,51);font-family:Arial;font-size:14px;"><span style="font-family:Arial;color:rgb(51,51,51);font-size:14px;">-Xmx&lt;size&gt;        set maximum Java heap size.........................</span><br></span></p>
<p><span style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;"><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;"><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;">主要修改Xmx和Xms两个参数,可以根据OS内存的大小进行合理设置,一般一个flume
 agent 1G左右大小即可</span><br></span></span></p>
<p><span style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;"><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;"><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;"><br></span></span></span></p>
            </div>
                </div>