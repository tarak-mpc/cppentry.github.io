---
layout:     post
title:      Hadoop系列之十：Hadoop配置文件及常用配置参数详解(未完成)
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/magedu_Linux/article/details/48420811				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div class="CopyrightStatement lh22" style="border:1px dashed rgb(213,213,213);line-height:22px;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;">
版权声明：原创作品，如需转载，请与作者联系。否则将追究法律责任。</div>
<div class="showContent" style="line-height:2;font-size:14px;border-bottom-width:1px;border-bottom-style:dashed;border-bottom-color:rgb(217,217,217);color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;">
<div><span></span>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:35px;">
<span style="font-size:14px;">学习联系QQ：1660809109、1661815153、2813150558</span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:35px;">
<span style="font-size:14px;">更多资讯：<a href="http://www.magedu.com/" rel="nofollow" style="text-decoration:none;color:rgb(12,137,207);">www.magedu.com</a></span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:35px;">
<br></div>
</div>
<div><span>1、Hadoop的配置文件</span></div>
<div> </div>
<div>hadoop-env.sh: 用于定义hadoop运行环境相关的配置信息，比如配置JAVA_HOME环境变量、为hadoop的JVM指定特定的选项、指定日志文件所在的目录路径以及master和slave文件的位置等；</div>
<div> </div>
<div>core-site.xml: 用于定义系统级别的参数，如HDFS URL、Hadoop的临时目录以及用于rack-aware集群中的配置文件的配置等，此中的参数定义会覆盖core-default.xml文件中的默认配置；</div>
<div> </div>
<div>hdfs-site.xml: HDFS的相关设定，如文件副本的个数、块大小及是否使用强制权限等，此中的参数定义会覆盖hdfs-default.xml文件中的默认配置；</div>
<div> </div>
<div>mapred-site.xml：HDFS的相关设定，如reduce任务的默认个数、任务所能够使用内存的默认上下限等，此中的参数定义会覆盖mapred-default.xml文件中的默认配置；</div>
<div> </div>
<div>masters: hadoop的secondary-masters主机列表，当启动Hadoop时，其会在当前主机上启动NameNode和JobTracker，然后通过SSH连接此文件中的主机以作为备用NameNode; </div>
<div> </div>
<p style="clear:both;overflow:hidden;">
slaves：Hadoop集群的slave主机列表，master启动时会通过SSH连接至此列表中的所有主机并为其启动DataNode和taskTracker进程； </p>
<p style="clear:both;overflow:hidden;">
本文出自 “<span style="color:#386d00;"><a href="http://blog.csdn.net/magedu_linux" rel="nofollow">马哥教育Linux运维培训</a></span>” 博客，转载请与作者联系！</p>
<p style="clear:both;overflow:hidden;">
</p>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:35px;">
<span style="font-size:14px;">学习联系QQ：1660809109、1661815153、2813150558</span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:35px;">
<span style="font-size:14px;">更多资讯：<a href="http://www.magedu.com/" rel="nofollow" style="text-decoration:none;color:rgb(12,137,207);">www.magedu.com</a></span></div>
<br><p></p>
</div>
            </div>
                </div>