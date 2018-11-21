---
layout:     post
title:      (一) spark源码理解之SparkContext
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-size:18px;">        好久没更新博客了，过年在家变懒了，哈哈。。继续坚持写博客，刚开始接触Spark是在学校举行的一次讲座上，在那之前对于Spark一无所知，听完讲座后觉得Spark在这个大数据时代必然会有它的用武之地，刚好又碰上师兄的毕业设计用到了Spark，于是开始尝试了解Spark，学习Spark的过程是比较艰辛且孤独的，因为身边懂Spark的人比较少，网上资源也不尽详细，但是我仍然收获颇多，可能接下来关于Spark源码的博文会有理解错误的地方，希望各位小伙伴们不吝指教，并且加以体谅，废话不多说，进入正题。。。</span></p>
<p><span style="font-size:18px;"> 我是从 <strong><span style="font-size:18px;">SparkContext</span> </strong>开始理解Spark源码<strong> 的，SparkContext </strong>是spark的入口<br>
1.创建<span style="font-size:18px;">SparkContext</span>，方式有如下几种：</span></p>
<p><img src="https://img-blog.csdn.net/20150305171910531?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2Robmxzag==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" width="1000" height="100" style="font-size:18px;"><span style="font-size:18px;">          <img src="" alt=""><img src="" alt=""></span></p>
<p><span style="font-size:18px;">其中master和appName在创建SparkContext的过程中必须要有的，否则会报错，当然master的方式在应用程序运行时可以进行指定</span></p>
<p><span style="font-size:18px;">创建完SparkContext后，Spark会进行接下来的操作</span></p>
<p><span style="font-size:18px;">2.设置spark的驱动地址和端口，若没有设置的话，则端口为0</span></p>
<p><span style="font-size:18px;">        <img src="" alt=""><img src="https://img-blog.csdn.net/20150305172315398?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2Robmxzag==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" width="800" height="100" alt=""></span></p>
<p><span style="font-size:18px;">3.在Tachyon（spark的分布式文件系统）创建一个临时文件夹</span></p>
<p><span style="font-size:18px;">        <img src="https://img-blog.csdn.net/20150305172338112?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2Robmxzag==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" width="800" height="100" alt=""></span></p>
<p><span style="font-size:18px;">4.创建spark事件监听器，<strong>listenerBus</strong>，listenerBus是一个LiveListenerBus对象，继承SparkListenerBus，LiveListenerBus会为每一个事件创建一个监听线程，事件包括stage的完成，job的完成等等，事件会放在事件队列eventQueue中，监听线程开始时就从eventQueue中取出一个事件，若该事件的监听器关闭了，则不做任何事情，否则监听器调用postToAll方法判断该事件的状态是什么，是stageSubmitted还是jobEnd等等，之后做出相应的反应，个人认为监听器在整个spark过程中是很重要的</span></p>
<p><span style="font-size:18px;">        <img src="https://img-blog.csdn.net/20150305172357302?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2Robmxzag==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" width="800" height="100" alt=""></span></p>
<p><span style="font-size:18px;">5.创建spark的执行环境变量<strong>SparkEnv</strong></span></p>
<p><span style="font-size:18px;">        <img src="" alt=""><img src="https://img-blog.csdn.net/20150305172436777?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2Robmxzag==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" width="800" height="100" alt=""></span></p>
<p><span style="font-size:18px;">SparkEnv类中又有其他一些比较重要的变量，正是因为这些变量才构成了SparkEnv，形成了spark赖以执行的环境</span></p>
<p><span style="font-size:18px;">6.Spark中有个SparkUI类的变量，感觉它跟我一直疑惑的从web界面查看作业运行情况很相关，因此可以有机会好好了解下，通过<a href="http://master/" rel="nofollow"><span style="color:#0000FF;">http://master</span></a>的外网IP://4040可以了解spark的运行情况</span></p>
<p><span style="font-size:18px;">Spark中还创建了时间日志类，用来记录所发生的各种事件，如stage的提交，task的完成，跟之前所提到过的监听器有一定的联系</span></p>
<p><span style="font-size:18px;">7.紧接着Spark装载application运行所需的JAR包，文件（本地文件，HDFS等）;</span></p>
<p><span style="font-size:18px;">   设置从节点的环境变量，包括内存设置，运行spark的用户;</span></p>
<p><span style="font-size:18px;">   之后进入核心环节，spark的调度环节：TaskScheduler--&gt;DAGScheduler;</span></p>
<p><span style="font-size:18px;">   TaskScheduler是在DAGScheduler之前创建的，因为DAGScheduler的创建过程需要TaskScheduler</span></p>
<p><span style="font-size:18px;">   接下来我将介绍下SparkEnv的源码理解。。。。未完待续。。</span></p>
<p><span style="font-size:18px;"> </span></p>
            </div>
                </div>