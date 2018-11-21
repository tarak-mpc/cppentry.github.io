---
layout:     post
title:      【Spark亚太研究院系列丛书】Spark实战高手之路-第一章 构建Spark集群（第三步）（2）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p></p>
<h1 style="font-family:verdana, Arial, Helvetica, sans-serif;text-align:center;"><span style="font-family:'Microsoft YaHei';">安装Spark</span></h1>
<p></p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;">
 </p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;">
Master、Slave1、Slave2这三台机器上均需要安装Spark。</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;">
首先在Master上安装Spark，具体步骤如下：</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;">
<strong>第一步：把Master上的Spark解压：</strong></p>
<p><img src="https://img-blog.csdn.net/20140909124518869?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0dHoxOTc0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="" style="font-size:12px;"></p>
<p><span style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;">我们直接解压到当前目录下：</span><br></p>
<p><img src="https://img-blog.csdn.net/20140909124316984?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0dHoxOTc0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="" style="font-size:12px;"></p>
<p><span style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;">此时，我们创建Spark的目录“/usr/local/spark”:</span><br></p>
<p><img src="https://img-blog.csdn.net/20140909124947939?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0dHoxOTc0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br></p>
<p><span style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;">把解压后的“spark-1.0.0-bin-hadoop1”复制到/usr/local/spark”下面：</span><br></p>
<p><img src="https://img-blog.csdn.net/20140909124544906?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0dHoxOTc0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br></p>
<p></p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;">
<strong>第二步：配置环境变量</strong></p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;">
       进入配置文件：</p>
<p><img src="https://img-blog.csdn.net/20140909124551567?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0dHoxOTc0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br></p>
<p><span style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;">在配置文件中加入“SPARK_HOME”并把spark的bin目录加到PATH中：</span><br></p>
<p><img src="https://img-blog.csdn.net/20140909124346500?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0dHoxOTc0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br></p>
<p><span style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;">配置后保存退出，然后使配置生效：</span><br></p>
<p><img src="https://img-blog.csdn.net/20140909124352656?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0dHoxOTc0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="" style="font-size:12px;"></p>
<p></p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;">
<strong>第三步：配置Spark</strong></p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;">
 </p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;">
进入Spark的conf目录：</p>
<p><img src="https://img-blog.csdn.net/20140909124804386?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0dHoxOTc0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br></p>
<p><span style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;">在配置文件中加入“SPARK_HOME”并把spark的bin目录加到PATH中：</span><br></p>
<p><img src="https://img-blog.csdn.net/20140909124604265?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0dHoxOTc0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br></p>
<p><span style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;">使用vim打开spark-env.sh：</span><br></p>
<p><img src="https://img-blog.csdn.net/20140909124612609?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0dHoxOTc0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br></p>
<p><span style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;">在配置文件中添加如下配置信息:</span><br></p>
<p><img src="https://img-blog.csdn.net/20140909124833371?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0dHoxOTc0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br></p>
<p></p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;">
其中：</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;">
JAVA_HOME:指定的是Java的安装目录；</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;">
SCALA_HOME:指定的是Scala的安装目录；</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;">
SPARK_MASTER_IP:指定的是Spark集群的Master节点的IP地址；</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;">
SPARK_WORKER_MEMOERY:指定的Worker节点能够最大分配给Excutors的内存大小，因为我们的三台机器配置都是2g，为了最充分的使用内存，这里设置为了2g；</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;">
HADOOP_CONF_DIR:指定的是我们原来的Hadoop集群的配置文件的目录；</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;">
 </p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;">
保存退出。</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;">
接下来配置Spark的conf下的slaves文件，把Worker节点都添加进去：</p>
<br><p><img src="https://img-blog.csdn.net/20140909124842185?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0dHoxOTc0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br></p>
<p><span style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;">打开后文件的内容：</span><br></p>
<p><img src="https://img-blog.csdn.net/20140909124849173?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0dHoxOTc0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br></p>
<p><span style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;">我们需要把内容修改为：</span><br></p>
<p><img src="https://img-blog.csdn.net/20140909124856037?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3d0dHoxOTc0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br></p>
<p></p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;">
可以看出我们把三台机器都设置为了Worker节点，也就是我们的主节点即是Master又是Worker节点。</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;">
保存退出。</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;">
上述就是Master上的Spark的安装。</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;">
 </p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;">
<strong>第四步：Slave1和Slave2采用和Master完全一样的Spark安装配置，在此不再赘述。</strong></p>
<br><p><br></p>
<p><br></p>
            </div>
                </div>