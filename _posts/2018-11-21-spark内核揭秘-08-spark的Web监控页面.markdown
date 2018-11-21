---
layout:     post
title:      spark内核揭秘-08-spark的Web监控页面
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/stark_summer/article/details/42913881				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="color:#ff0000;">在SparkContext中可以看到初始化UI代码：</span></p>
<p></p>
<pre style="background-color:#2b2b2b;color:#a9b7c6;font-family:'宋体';font-size:22pt;"><span style="color:#808080;">// Initialize the Spark UI
</span><span style="color:#cc7832;"><strong>private</strong></span>[spark] <span style="color:#cc7832;"><strong>val </strong></span><span style="color:#9876aa;"><em>ui</em></span>: Option[SparkUI] =
  <span style="color:#cc7832;"><strong>if </strong></span>(<span style="color:#9876aa;"><em>conf</em></span>.getBoolean(<span style="color:#6a8759;">"spark.ui.enabled"</span><span style="color:#cc7832;">, </span><span style="color:#cc7832;"><strong>true</strong></span>)) {
    <span style="font-style:italic;">Some</span>(SparkUI.<span style="font-style:italic;">createLiveUI</span>(<span style="color:#cc7832;"><strong>this</strong></span><span style="color:#cc7832;">, </span><span style="color:#9876aa;"><em>conf</em></span><span style="color:#cc7832;">, </span><span style="color:#9876aa;"><em>listenerBus</em></span><span style="color:#cc7832;">, </span><span style="color:#9876aa;"><em>jobProgressListener</em></span><span style="color:#cc7832;">,
</span><span style="color:#cc7832;">      </span><span style="color:#9876aa;"><em>env</em></span>.securityManager<span style="color:#cc7832;">,</span><span style="color:#9876aa;"><em>appName</em></span>))
  } <span style="color:#cc7832;"><strong>else </strong></span>{
    <span style="color:#808080;">// For tests, do not enable the UI
</span><span style="color:#808080;">    </span>None
  }

<span style="color:#808080;">// Bind the UI before starting the task scheduler to communicate
</span><span style="color:#808080;">// the bound port to the cluster manager properly
</span><span style="color:#9876aa;"><em>ui</em></span>.foreach(_.bind())</pre>
<span style="color:#ff0000;">创建SparkUI对象：</span>
<pre style="background-color:#2b2b2b;color:#a9b7c6;font-family:'宋体';font-size:22pt;"><span style="color:#cc7832;"><strong>def </strong></span><span style="background-color:#344134;">createLiveUI</span>(
    sc: SparkContext<span style="color:#cc7832;">,
</span><span style="color:#cc7832;">    </span>conf: SparkConf<span style="color:#cc7832;">,
</span><span style="color:#cc7832;">    </span>listenerBus: SparkListenerBus<span style="color:#cc7832;">,
</span><span style="color:#cc7832;">    </span>jobProgressListener: JobProgressListener<span style="color:#cc7832;">,
</span><span style="color:#cc7832;">    </span>securityManager: SecurityManager<span style="color:#cc7832;">,
</span><span style="color:#cc7832;">    </span>appName: <span style="color:#4e807d;">String</span>): SparkUI =  {
  <span style="font-style:italic;">create</span>(<span style="font-style:italic;">Some</span>(sc)<span style="color:#cc7832;">, </span>conf<span style="color:#cc7832;">, </span>listenerBus<span style="color:#cc7832;">, </span>securityManager<span style="color:#cc7832;">, </span>appName<span style="color:#cc7832;">,
</span><span style="color:#cc7832;">    </span>jobProgressListener = <span style="font-style:italic;">Some</span>(jobProgressListener))
}</pre>
<p><span style="color:#ff0000;">进入create方法：</span></p>
<p><img src="https://img-blog.csdn.net/20150120111158505?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Rhcmtfc3VtbWVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p><img src="https://img-blog.csdn.net/20150120111453163?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Rhcmtfc3VtbWVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p><span style="color:#ff0000;">进入SparkUI:</span></p>
<p><img src="https://img-blog.csdn.net/20150120113029695?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Rhcmtfc3VtbWVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p><span style="color:#ff0000;">SparkUI 继承了WebUI：</span></p>
<p><img src="https://img-blog.csdn.net/20150120113823905?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Rhcmtfc3VtbWVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p><span style="color:#ff0000;">而WebUI类定义了def initialize()方法</span></p>
<p><img src="https://img-blog.csdn.net/20150120113931516?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Rhcmtfc3VtbWVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p><span style="color:#ff0000;">SparkUI的initialize()实现方法：</span></p>
<p><img src="https://img-blog.csdn.net/20150120114013968?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Rhcmtfc3VtbWVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p>上面代码分析：增加页面的tab页面</p>
<p><span style="color:#ff0000;">1、JobsTab：</span></p>
<p><img src="https://img-blog.csdn.net/20150120115140130?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Rhcmtfc3VtbWVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p><span style="color:#ff0000;">2、stagesTab：</span></p>
<p><img src="https://img-blog.csdn.net/20150120115423042?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Rhcmtfc3VtbWVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p><span style="color:#ff0000;">3、StorageTab：</span></p>
<p><img src="https://img-blog.csdn.net/20150120115515364?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Rhcmtfc3VtbWVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p><span style="color:#ff0000;">4、EnvironmentTab：</span></p>
<p><img src="https://img-blog.csdn.net/20150120115603147?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Rhcmtfc3VtbWVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p><span style="color:#ff0000;">5、ExecutorsTab：</span></p>
<p><img src="https://img-blog.csdn.net/20150120115645361?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Rhcmtfc3VtbWVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p><span style="color:#ff0000;">6、createStaticHandler：</span></p>
<p><img src="https://img-blog.csdn.net/20150120115715406?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Rhcmtfc3VtbWVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p><span style="color:#ff0000;">7、createRedirectHandler：</span></p>
<p><img src="https://img-blog.csdn.net/20150120115859646?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Rhcmtfc3VtbWVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p><span style="background-color:rgb(255,0,0);">当执行完initialize()方法后，我们回到SparkContext 的Initialize the spark UI:</span></p>
<p><img src="https://img-blog.csdn.net/20150120120149234?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Rhcmtfc3VtbWVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p><span style="color:#ff0000;">bind  port：</span></p>
<p><img src="https://img-blog.csdn.net/20150120120303100?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Rhcmtfc3VtbWVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p><span style="color:#ff0000;">从上面代码可以看出来，启动了端口号为4040的本地JettyServer</span></p>
<p><img src="https://img-blog.csdn.net/20150120120613750?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Rhcmtfc3VtbWVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p><img src="https://img-blog.csdn.net/20150120120840606?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Rhcmtfc3VtbWVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p>上面代码分析：</p>
<p><span style="color:#ff0000;">1、创建ContextHandlerCollection并将handlers设置到ContextHandlerCollection中</span></p>
<p><span style="color:#ff0000;">2、增加一个filter：</span></p>
<p><img src="https://img-blog.csdn.net/20150120122109673?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Rhcmtfc3VtbWVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p><img src="https://img-blog.csdn.net/20150120122132293?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Rhcmtfc3VtbWVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p><span style="color:#ff0000;">3、创建Jetty Server并绑定端口号并创建一个QueuedThreadPool，设置到Server中：</span></p>
<p><img src="https://img-blog.csdn.net/20150120122543718?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Rhcmtfc3VtbWVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p><span style="color:#ff0000;">4、启动Jetty server，如果启动失败，就抛出异常：</span></p>
<p><img src="https://img-blog.csdn.net/20150120122713325?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Rhcmtfc3VtbWVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p><span style="color:#ff0000;">5、试着重试几次启动startServiceOnPort，如果启动失败，就更换新端口号（规则是1+oldPort），重新启动：</span></p>
<p><img src="https://img-blog.csdn.net/20150120122913859?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Rhcmtfc3VtbWVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p><img src="https://img-blog.csdn.net/20150120123014878?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Rhcmtfc3VtbWVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p><img src="https://img-blog.csdn.net/20150120123012359?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Rhcmtfc3VtbWVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p><br></p>
<p><span style="color:#ff0000;">最终启动成功后，会把信息东西存放在ServerInfo中：</span></p>
<p><img src="https://img-blog.csdn.net/20150120123154360?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Rhcmtfc3VtbWVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p><br></p>
            </div>
                </div>