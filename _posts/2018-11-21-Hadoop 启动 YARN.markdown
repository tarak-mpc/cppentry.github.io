---
layout:     post
title:      Hadoop 启动 YARN
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/u012414590/article/details/54318315				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div>1，首先进入hadoop目录：cd /usr/local/hadoop</div>
<div><span style="background-color:rgb(254,254,254);"><br></span></div>
<div><span style="background-color:rgb(254,254,254);">2，修改配置文件 </span><span style="background-color:rgb(254,254,254);">mapred-site.xml</span><span style="background-color:rgb(254,254,254);">，这边需要先进行重命名：</span></div>
<div><span style="background-color:rgb(254,254,254);">     mv ./etc/hadoop/mapred-site.xml.template ./etc/hadoop/mapred-site.xml</span></div>
<div><span style="background-color:rgb(254,254,254);">     然后修改内容：gedit ./etc/hadoop/mapred-site.xml</span></div>
<div><span style="background-color:rgb(254,254,254);">     &lt;configuration&gt;</span></div>
<div><span style="background-color:rgb(254,254,254);">        &lt;property&gt;</span></div>
<div><span style="background-color:rgb(254,254,254);">             &lt;name&gt;mapreduce.framework.name&lt;/name&gt;</span></div>
<div><span style="background-color:rgb(254,254,254);">             &lt;value&gt;yarn&lt;/value&gt;</span></div>
<div><span style="background-color:rgb(254,254,254);">        &lt;/property&gt;</span></div>
<div><span style="background-color:rgb(254,254,254);">     &lt;/configuration&gt;</span></div>
<div><span style="background-color:rgb(254,254,254);"><br></span></div>
<div><span style="background-color:rgb(254,254,254);">3，</span> <span style="background-color:rgb(254,254,254);">
接着修改配置文件 </span><span style="background-color:rgb(254,254,254);">yarn-site.xml:</span></div>
<div><span style="background-color:rgb(254,254,254);"><strong>     </strong>&lt;configuration&gt;</span></div>
<div><span style="background-color:rgb(254,254,254);">        &lt;property&gt;</span></div>
<div><span style="background-color:rgb(254,254,254);">             &lt;name&gt;yarn.nodemanager.aux-services&lt;/name&gt;</span></div>
<div><span style="background-color:rgb(254,254,254);">             &lt;value&gt;mapreduce_shuffle&lt;/value&gt;</span></div>
<div><span style="background-color:rgb(254,254,254);">         &lt;/property&gt;</span></div>
<div><span style="background-color:rgb(254,254,254);">     &lt;/configuration&gt;</span></div>
<div><span style="background-color:rgb(254,254,254);"><br></span></div>
<div><span style="background-color:rgb(254,254,254);">4，启动yarn（需要先执行过./sbin/start-dfs.sh）</span></div>
<div><span style="background-color:rgb(254,254,254);">     ./sbin/start-yarn.sh      # 启动YARN</span></div>
<div><span style="background-color:rgb(254,254,254);">     ./sbin/mr-jobhistory-daemon.sh start historyserver  # 开启历史服务器，才能在Web中查看任务运行情况</span></div>
<div><span style="background-color:rgb(254,254,254);"><br></span></div>
<div><span style="background-color:rgb(254,254,254);">5，</span> <span style="background-color:rgb(254,254,254);">
启动 YARN 之后，运行实例的方法还是一样的，仅仅是资源管理方式、任务调度不同。观察日志信息可以发现，不启用 YARN 时，是 “mapred.LocalJobRunner” 在跑任务，启用 YARN 之后，是 “mapred.YARNRunner” 在跑任务。启动 YARN 有个好处是可以通过 Web 界面查看任务的运行情况：</span><span style="background-color:rgb(254,254,254);text-decoration:none;color:rgb(0,0,0);"><a href="http://localhost:8088/cluster" rel="nofollow" style="background-color:rgb(254,254,254);text-decoration:none;">http://localhost:8088/cluster</a></span></div>
<div><img src="https://img-blog.csdn.net/20170110215927431?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMjQxNDU5MA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></div>
<div></div>
<div><span style="background-color:rgb(254,254,254);"><br></span></div>
<div><span style="background-color:rgb(254,254,254);">6，</span> <span style="font-weight:bold;">
不启动 YARN 需重命名 mapred-site.xml</span>
<div>
<div>如果不想启动 YARN，务必把配置文件 <span>mapred-site.xml</span> 重命名，改成 mapred-site.xml.template，需要用时改回来就行。否则在该配置文件存在，而未开启 YARN 的情况下，运行程序会提示 “Retrying connect to server: 0.0.0.0/0.0.0.0:8032” 的错误，这也是为何该配置文件初始文件名为 mapred-site.xml.template。</div>
<div><br></div>
<div>7，关闭yarn脚本如下：</div>
<div>     ./sbin/stop-yarn.sh</div>
<div>     ./sbin/mr-jobhistory-daemon.sh stop historyserver</div>
</div>
</div>
            </div>
                </div>