---
layout:     post
title:      Spark入门到精通（入门）——第二节  Spark 2.0.0 查看job 历史日志
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：所有内容原创，如需转载，请声明					https://blog.csdn.net/lovedopa/article/details/82943234				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>本文十分的重要，希望对大家的spark学习有一些帮助：</p>

<h1 id="1%C2%A0%20%E5%BC%95%E8%A8%80%EF%BC%9A">1  引言：</h1>

<p>在使用<code>Spark</code>的时候，有时候我们会关注<code>job</code>的历史日志，但是在<code>Spark</code>中默认情况下，历史日志是关闭的，在本篇博客中主要介绍一下如何启动<code>spark</code>的历史日志。</p>

<p> </p>

<h1 id="2%C2%A0%20%E5%BC%80%E5%90%AF%E5%8E%86%E5%8F%B2%E6%97%A5%E5%BF%97%E6%9C%8D%E5%8A%A1%E5%99%A8">2  开启历史日志服务器</h1>

<h2 id="2.1%C2%A0%E5%AE%89%E8%A3%85spark">2.1 安装spark</h2>

<p>参考</p>

<p><a href="https://mp.csdn.net/postedit/82939152" rel="nofollow">spark简介和安装</a></p>

<h2 id="2.2%C2%A0%20%E4%BF%AE%E6%94%B9%E9%85%8D%E7%BD%AE%E6%96%87%E4%BB%B6">2.2  修改配置文件</h2>

<h3>1修改spark-default.conf</h3>

<h2>修改后的结果如图所示</h2>

<p> </p>

<blockquote>
<ol><li style="margin-left:0cm;"><span style="color:#000000;">spark.eventLog.enabled  </span><span style="color:#0000ff;">true</span></li>
	<li style="margin-left:0cm;"><span style="color:#000000;">spark.eventLog.dir       hdfs:</span><span style="color:#008000;">//linux102:9001/spark/jobhistory/logs</span></li>
	<li style="margin-left:0cm;"><span style="color:#000000;">spark.eventLog.compress </span><span style="color:#0000ff;">true</span></li>
</ol></blockquote>

<p style="margin-left:0cm;"> </p>

<p>当然，：HDFS上的目录需要提前存在</p>

<h3>2  修改spark-env.sh文件</h3>

<p>spark-env.sh</p>

<p>export SPARK_HISTORY_OPTS="-Dspark.history.ui.port=4000</p>

<p>-Dspark.history.retainedApplications=5</p>

<p>-Dspark.history.fs.logDirectory=<span style="color:#000000;">hdfs:</span><span style="color:#008000;">//linux102:9001/spark/jobhistory/logs</span></p>

<h3>3 参数描述：</h3>

<p><br>
spark.eventLog.dir：Application在运行过程中所有的信息均记录在该属性指定的路径下； <br>
spark.history.ui.port=4000  调整WEBUI访问的端口号为4000<br>
spark.history.fs.logDirectory=hdfs://linux102:9001/directory  配置了该属性后，在start-history-server.sh时就无需再显式的指定路径，Spark History Server页面只展示该指定路径下的信息<br>
注意：我这里修改了hdfs的端口号<br>
spark.history.retainedApplications=3   指定保存Application历史记录的个数，如果超过这个值，旧的应用程序信息将被删除，这个是内存中的应用数，而不是页面上显示的应用数。<br>
 </p>

<h3>4 测试</h3>

<p>将配置好的Spark文件拷贝到其他节点上 <br>
/opt/module/spark/sbin/start-all.sh</p>

<p>启动后执行：【别忘了启动HDFS】<br>
/opt/module/spark/sbin/start-history-server.sh</p>

<p><img alt="" class="has" height="210" src="https://img-blog.csdn.net/20181005123850805?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xvdmVkb3Bh/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="586"></p>

<p>到此为止，Spark History Server安装完毕.<br>
如果遇到Hadoop HDFS的写入权限问题：<br>
org.apache.hadoop.security.AccessControlException<br>
解决方案： 在hdfs-site.xml中添加如下配置，关闭权限验证<br>
&lt;property&gt;<br>
        &lt;name&gt;dfs.permissions&lt;/name&gt;<br>
        &lt;value&gt;false&lt;/value&gt;<br>
&lt;/property&gt;  <br>
 </p>

<p>5  当我们退出job，如果显示</p>

<p><img alt="" class="has" height="872" src="https://img-blog.csdn.net/2018100614020923?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xvdmVkb3Bh/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="1200"></p>

<p> </p>

<p>就表示安装成功了</p>

<p> </p>

<p> </p>

<p> </p>

<p> </p>

<p> </p>

<p> </p>

<p> </p>

<p> </p>

<p> </p>

<p> </p>

<p><br>
 </p>            </div>
                </div>