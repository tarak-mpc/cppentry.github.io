---
layout:     post
title:      spark on yarn 的安装
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>spark有两种集群模式</p>
<p>分别是spark on yarn和standalone</p>
<p>spark on yarn：</p>
<p><span></span>运行在 yarn（Yet Another Resource Negotiator） 资源管理器框架之上，由 yarn 负责资源管理，Spark 负责任务调度和计算。<br></p>
<p>standalone：</p>
<p><span></span>类似于单机模式</p>
<p><br></p>
<p><br></p>
<p>我的配置环境：</p>
<p></p>
<p>软件环境：</p>
<p>Ubuntu 14.04</p>
<p>Hadoop 2.6.2</p>
<p>Spark 1.5.2</p>
<p>JDK 1.80u66</p>
<p>Scala 2.10.6</p>
<br><p>参照链接：</p>
<p><a href="http://www.aboutyun.com/thread-12798-1-1.html" rel="nofollow">www.aboutyun.com/thread-12798-1-1.html</a></p>
<p>这个链接用来安装hadoop</p>
<p><a href="http://wuchong.me/blog/2015/04/04/spark-on-yarn-cluster-deploy/" rel="nofollow">wuchong.me/blog/2015/04/04/spark-on-yarn-cluster-deploy/</a></p>
<p>这个链接实现hadoop yarn的搭建(yarn原来是hadoop的框架，spark也可在上面跑）+spark的安装和配置，安装yarn前需要先安装hadoop的</p>
<p><br></p>
<p>值得注意的点：</p>
<p>环境变量一定要正确</p>
<p>文件的权限要正确</p>
<p>ip不要经常变，不要拔网线（因为配置了ssh免密登陆）</p>
<p><br></p>
<p><br></p>
<p><br></p>
<p><br></p>
            </div>
                </div>