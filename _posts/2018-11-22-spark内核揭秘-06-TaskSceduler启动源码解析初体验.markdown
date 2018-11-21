---
layout:     post
title:      spark内核揭秘-06-TaskSceduler启动源码解析初体验
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/stark_summer/article/details/42874467				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="color:#ff0000;">TaskScheduler实例对象启动源代码如下所示：</span></p>
<p><img src="https://img-blog.csdn.net/20150119161835209?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Rhcmtfc3VtbWVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p><span style="color:#ff0000;">从上面代码可以看出来，taskScheduler的启动是在SparkContext</span></p>
<p><img src="https://img-blog.csdn.net/20150119164200453?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Rhcmtfc3VtbWVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p><span style="color:#ff0000;">找到TaskSchedulerImpl实现类中的start方法实现：</span></p>
<p><img src="https://img-blog.csdn.net/20150119164848416?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Rhcmtfc3VtbWVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p>1、<span style="color:#ff0000;">从上代码看到，先启动CoarseGrainedSchedulerBackend，</span></p>
<p><img src="https://img-blog.csdn.net/20150119170958289?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Rhcmtfc3VtbWVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p><span style="color:#ff0000;">从上面CoarseGrainedSchedulerBackend类的代码，可以看出spark启动了DriverActor,名称为CoarseGrainedScheduler，这是一个akka消息通信类，会先运行preStart()方法</span></p>
<p><img src="https://img-blog.csdn.net/20150119171621223?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Rhcmtfc3VtbWVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p><span style="color:#ff0000;">从上面代码可以看到，初始化了akka客户端监听，还有最重要的是调用了系统的scheduler调度，参数函数是立即执行调度，间隔1000毫秒，运行ReviveOffers方法</span></p>
<p><img src="https://img-blog.csdn.net/20150119172443872?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Rhcmtfc3VtbWVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p><span style="color:#ff6666;">进入makeOffers()方法：</span></p>
<p><img src="https://img-blog.csdn.net/20150119172536148?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Rhcmtfc3VtbWVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p><span style="color:#ff0000;">运行launchTask方法:</span></p>
<p><img src="https://img-blog.csdn.net/20150119172649296?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Rhcmtfc3VtbWVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br><br></p>
<p><img src="https://img-blog.csdn.net/20150119172752819?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Rhcmtfc3VtbWVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p>这段代码是spark序列号任务大小超过akkaFrameSize - AkkaUtils.reservedSizeBytes大小，那就报错为”<span style="color:#ff6666;">Serialized task %s:%d was %d bytes, which exceeds max allowed: " +<br>
                "spark.akka.frameSize (%d bytes) - reserved (%d bytes). Consider increasing " +<br>
                "spark.akka.frameSize or using broadcast variables for large values.</span>“ ，此刻会将该任务终止，并将任务从任务列表中移除，这样推荐使用broadcast广播方式</p>
<p>否则，将获取执行任务数据，并减少空闲cpu数，发送消息执行 LaunchTask(new SerializableBuffer(serializedTask))方法，即CoarsedGrainedExecutorBackend类的LaunchTask方法：</p>
<p><img src="https://img-blog.csdn.net/20150119174914140?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Rhcmtfc3VtbWVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p><span style="color:#ff0000;">上面代码 会运行executor 的launchTask方法：</span></p>
<p><img src="https://img-blog.csdn.net/20150119175107945?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Rhcmtfc3VtbWVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p><span style="color:#ff0000;">TaskRunner就是一个多线程：</span></p>
<p><img src="https://img-blog.csdn.net/20150119175243230?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Rhcmtfc3VtbWVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p><img src="https://img-blog.csdn.net/20150119175314337?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Rhcmtfc3VtbWVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p><img src="https://img-blog.csdn.net/20150119175346036?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Rhcmtfc3VtbWVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p>代码太多，我就不截图了，其实实际就是根据机器状况，运行task任务</p>
<p>2、<span style="color:#ff0000;">然后我们回到TaskSchedulerImpl实现类中的start方法</span><br></p>
<p><img src="https://img-blog.csdn.net/20150119164848416?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Rhcmtfc3VtbWVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p>如果isLocal=false and spark.speculation=true，不是local模式，那就要dispatcher分发任务了，默认是100毫秒后立即启动，并间隔100毫秒循环运行，</p>
<p><img src="https://img-blog.csdn.net/20150119180219527?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Rhcmtfc3VtbWVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p><span style="color:#ff0000;">CoarseGrainedSchedulerBackend的reviveOffers：</span></p>
<p><img src="https://img-blog.csdn.net/20150119181108758?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Rhcmtfc3VtbWVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p><br></p>
            </div>
                </div>