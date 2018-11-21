---
layout:     post
title:      spark内核揭秘-spark任务调度系统
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:35px;">
<a href="http://lib.csdn.net/base/spark" rel="nofollow" class="replace_word" title="Apache Spark知识库" style="text-decoration:none;color:rgb(223,52,52);font-weight:bold;">Spark</a>的任务调度系统如下所示：</p>
<p style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:35px;">
</p>
<p style="color:rgb(85,85,85);font-family:Arial;font-size:14px;line-height:26px;">
<img src="https://img-blog.csdn.net/20150116114717005?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Rhcmtfc3VtbWVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" style="border:none;"></p>
<span style="color:rgb(51,51,51);font-family:'宋体';font-size:14px;line-height:28px;text-indent:28px;background-color:rgb(248,248,248);"></span><span style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:35px;"></span>
<p style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:35px;">
从上图中可以看出来由RDD Objects产生DAG，然后进入了DAGScheduler阶段，DAGScheduler是面向state的高层次的调度器，DAGScheduler把DAG拆分成很多的tasks，每组的tasks都是一个state，每当遇到shuffle就会产生新的state，可以看出上图一共有三个state；DAGScheduler需要记录那些RDD被存入磁盘等物化动作，同时需寻找task的最优化调度，例如数据本地性等；DAGScheduler还要监视因为shuffle输出导致的失败，如果发生这种失败，可能就需要重新提交该state：</p>
<p style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:35px;">
<img src="https://img-blog.csdn.net/20150118131357327?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Rhcmtfc3VtbWVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" style="border:none;"><br></p>
<p style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:35px;">
<span><span style="color:rgb(51,51,51);font-family:'宋体';font-size:14px;line-height:28px;text-indent:28px;background-color:rgb(248,248,248);">DAGScheduler划分state后以TaskSet为单位把任务，把任务交给底层次的可插拔的调度器TaskScheduler来处理：</span></span></p>
<p style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:35px;">
<img src="https://img-blog.csdn.net/20150118132444015?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Rhcmtfc3VtbWVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" style="border:none;"><br></p>
<p style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:35px;">
可以看出TaskScheduler是一个trait，在目前spark系统中TaskScheduler的实现类只有一个TaskSchedulerImpl：</p>
<p style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:35px;">
<img src="https://img-blog.csdn.net/20150118132615551?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Rhcmtfc3VtbWVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" style="border:none;"><br></p>
<p style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:35px;">
一个TaskScheduler只为一个SparkContext实例服务，TaskScheduler接受来自DAGScheduler发送过来的分组的任务，DAGScheduler给TaskScheduler发送任务的时候是以Stage为单位来提交的，TaskScheduler收到任务后负责把任务分发到集群中Worker的Executor中去运行，如果某个task运行失败，TaskScheduler要负责重试；另外如果TaskScheduler发现某个Task一直未运行完，就可能启动同样的任务运行同一个Task，那个任务先运行完就用哪个任务的结果。<br><br><br>
TaskScheduler发送的任务交给了Worker上的Executor以多线程的方式运行，每一个线程负责一个任务：<br></p>
<p style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:35px;">
<img src="https://img-blog.csdn.net/20150118132749822?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Rhcmtfc3VtbWVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" style="border:none;"><br></p>
<p style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:35px;">
<br></p>
<p style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:35px;">
<img src="https://img-blog.csdn.net/20150118132858135?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Rhcmtfc3VtbWVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" style="border:none;"><br></p>
<p style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:35px;">
<br></p>
<p style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:35px;">
其中的存储系统的管理是BlockManager来负责的：<br></p>
<p style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:35px;">
<span style="color:rgb(51,51,51);font-family:'宋体';font-size:14px;line-height:28px;text-indent:28px;background-color:rgb(248,248,248);"><img src="https://img-blog.csdn.net/20150118133225359?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Rhcmtfc3VtbWVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" style="border:none;"><br></span></p>
<p style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:35px;">
看一下TaskSet的源码：<br></p>
<p style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:35px;">
<span style="color:rgb(51,51,51);font-family:'宋体';font-size:14px;line-height:28px;text-indent:28px;background-color:rgb(248,248,248);"><img src="https://img-blog.csdn.net/20150118133321734?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Rhcmtfc3VtbWVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" style="border:none;"><br></span></p>
<p style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:35px;">
从TaskSet源码的第一个参数tasks就可以看出其是一个Task的数组，包含一组Task。<br></p>
<div><br></div>
            </div>
                </div>