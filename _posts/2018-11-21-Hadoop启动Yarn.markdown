---
layout:     post
title:      Hadoop启动Yarn
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>1，首先进入hadoop目录：cd /usr/local/hadoop</p>

<p>2，修改配置文件 mapred-site.xml，这边需要先进行重命名：</p>

<p>     mv ./etc/hadoop/mapred-site.xml.template ./etc/hadoop/mapred-site.xml</p>

<p>     然后修改内容：gedit ./etc/hadoop/mapred-site.xml</p>

<p>     &lt;configuration&gt;</p>

<p>        &lt;property&gt;</p>

<p>             &lt;name&gt;mapreduce.framework.name&lt;/name&gt;</p>

<p>             &lt;value&gt;yarn&lt;/value&gt;</p>

<p>        &lt;/property&gt;</p>

<p>     &lt;/configuration&gt;</p>

<p> </p>

<p>3， 接着修改配置文件 yarn-site.xml:</p>

<p><strong>     </strong>&lt;configuration&gt;</p>

<p>        &lt;property&gt;</p>

<p>             &lt;name&gt;yarn.nodemanager.aux-services&lt;/name&gt;</p>

<p>             &lt;value&gt;mapreduce_shuffle&lt;/value&gt;</p>

<p>         &lt;/property&gt;</p>

<p>     &lt;/configuration&gt;</p>

<p> </p>

<p>4，启动yarn</p>

<p>       start-yarn.sh      # 启动YARN</p>

<p>5， 启动 YARN 之后，运行实例的方法还是一样的，仅仅是资源管理方式、任务调度不同。观察日志信息可以发现，启动 YARN 有个好处是可以通过 Web 界面查看任务的运行情况：<a href="http://localhost:8088/cluster" rel="nofollow">http://IP:8088/cluster</a></p>

<p><img alt="" class="has" height="642" src="https://img-blog.csdn.net/20180803100717827?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMyOTQxODgx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="1200"></p>            </div>
                </div>