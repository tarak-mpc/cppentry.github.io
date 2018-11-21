---
layout:     post
title:      Apache Flume 分布式日志收集系统学习
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/lihong1191/article/details/51558759				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>初步学习Apache Flume ,从两个方面总结。</p>
<p>第一方面：Apache Flume 有两个版本 Flume 0.9x和 Flume NG（new generation），Flume NG是对传统的Apache Flume的精简，去掉了 Master 和Collector两种角色，只保留了Agent 一种角色。数据流（Data Flow）描述了日志数据从产生，传输，处理到最后写入目的地的路径，它是 Flume 的最抽象模型。</p>
<p><br></p>
<p><img src="https://img-blog.csdn.net/20160603163104197?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p><img src="" alt=""></p>
<p><span></span>Flume 数据流模型</p>
<p><br></p>
<p>第二方面：为了有效的手机日志，系统实施时候需要在每个手机日志的服务器上安装相同版本的  Flume NG ,由于不同的网站选取的Web  server不同,为了对数据有效的收集，需要对每类设定一个或者多个 Agent，对收集的不同数据进行不同的处理。</p>
<p><img src="https://img-blog.csdn.net/20160603163140948?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p><span></span>基于 Flume 的日志数据收集流程</p>
<p><br></p>
            </div>
                </div>