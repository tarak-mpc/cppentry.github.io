---
layout:     post
title:      spark内核揭秘-13-Worker中Executor启动过程源码分析
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/stark_summer/article/details/42975383				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="color:#ff0000;">进入Worker类源码：</span></p>
<p><span style="color:#ff0000;"><img src="https://img-blog.csdn.net/20150121203300640?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Rhcmtfc3VtbWVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></p>
<p><span style="color:#ff0000;"></span></p>
<p><span style="color:#ff0000;"><br>
可以看出Worker本身是Akka中的一个Actor。<br></span></p>
<p><br></p>
<p><span style="color:#ff0000;">进入Worker类的LaunchExecutor：</span></p>
<p><span style="color:#ff0000;"><img src="https://img-blog.csdn.net/20150121200514093?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Rhcmtfc3VtbWVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></p>
<p><span style="color:rgb(255,0,0);"><img src="https://img-blog.csdn.net/20150121200619076?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Rhcmtfc3VtbWVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></p>
<p><span style="color:rgb(255,0,0);">从源代码可以看出Worker节点上要分配CPU和Memory给新的Executor，首先需要创建一个ExecutorRunner：</span></p>
<p><span style="color:rgb(255,0,0);">ExecutorRunner是用于维护executor进程的：</span></p>
<p><span style="color:rgb(255,0,0);"></span></p>
<p></p>
<p><span style="color:rgb(255,0,0);">1、进入ExecutorRunner 的start方法：</span></p>
<p><span style="color:rgb(255,0,0);"><img src="https://img-blog.csdn.net/20150121200737875?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Rhcmtfc3VtbWVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></p>
<p><span style="color:rgb(255,0,0);">1.1、进入fetchAndRunExecutor()方法（核心方法）：</span></p>
<p><span style="color:rgb(255,0,0);"><img src="https://img-blog.csdn.net/20150121200914093?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Rhcmtfc3VtbWVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></p>
<p><span style="color:rgb(255,0,0);"><img src="https://img-blog.csdn.net/20150121200938616?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Rhcmtfc3VtbWVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></p>
<p><span style="color:rgb(255,0,0);"><img src="https://img-blog.csdn.net/20150121201002016?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Rhcmtfc3VtbWVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></p>
<p><span style="color:#ff0000;">2、进入</span><span style="color:rgb(255,0,0);"> master ! ExecutorStateChanged(appId, execId, manager.state, None, None)方法：</span></p>
<p><span style="color:rgb(255,0,0);">发送消息给Master：</span></p>
<p><span style="color:rgb(255,0,0);"><img src="https://img-blog.csdn.net/20150121204956309?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Rhcmtfc3VtbWVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></p>
<p><span style="color:rgb(255,0,0);"><img src="https://img-blog.csdn.net/20150121205014984?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Rhcmtfc3VtbWVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></p>
<p>我们再回到到launchExecutor方法：</p>
<p><br></p>
<p><img src="https://img-blog.csdn.net/20150121212446556?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Rhcmtfc3VtbWVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p><span style="color:rgb(255,0,0);">进入ExecutorAdded方法：</span></p>
<p><span style="color:rgb(255,0,0);"><img src="https://img-blog.csdn.net/20150121212610390?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Rhcmtfc3VtbWVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></p>
<p><span style="color:#ff0000;">上面代码利用Akka 发送ExecutorStateChanged方法给Master</span></p>
<p><span style="color:#ff0000;"></span></p>
<p><span style="color:#ff0000;">进入 Master 的</span>ExecutorStateChanged方法：</p>
<p><img src="https://img-blog.csdn.net/20150121212954828?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Rhcmtfc3VtbWVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p><span style="color:rgb(255,0,0);"><img src="https://img-blog.csdn.net/20150121212952890?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Rhcmtfc3VtbWVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></p>
<p>PS:感觉代码分析的有点乱，不是很理想，希望大家知道，谢谢</p>
<div>
<p><span style="color:#ff0000;">进入Worker类的LaunchExecutor：</span></p>
<p><span style="color:#ff0000;"><img src="https://img-blog.csdn.net/20150121200514093?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Rhcmtfc3VtbWVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></p>
<p><span style="color:rgb(255,0,0);"><img src="https://img-blog.csdn.net/20150121200619076?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Rhcmtfc3VtbWVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></p>
<p><span style="color:rgb(255,0,0);">1、进入ExecutorRunner 的start方法：</span></p>
<p><span style="color:rgb(255,0,0);"><img src="https://img-blog.csdn.net/20150121200737875?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Rhcmtfc3VtbWVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></p>
<p><span style="color:rgb(255,0,0);">2、进入fetchAndRunExecutor()方法：</span></p>
<p><span style="color:rgb(255,0,0);"><img src="https://img-blog.csdn.net/20150121200914093?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Rhcmtfc3VtbWVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></p>
<p><span style="color:rgb(255,0,0);"><img src="https://img-blog.csdn.net/20150121200938616?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Rhcmtfc3VtbWVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></p>
<p><span style="color:rgb(255,0,0);"><img src="https://img-blog.csdn.net/20150121201002016?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Rhcmtfc3VtbWVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></p>
</div>
            </div>
                </div>