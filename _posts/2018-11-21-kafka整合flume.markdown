---
layout:     post
title:      kafka整合flume
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/wuxintdrh/article/details/79478619				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>一、kafka和flume的安装 <br>
 apache-flume-1.5.2-bin <br>
 kafka_2.10-0.10.2.1 <br>
<a href="http://blog.csdn.net/wuxintdrh/article/details/62414061" rel="nofollow">kafka安装部署 </a> <br>
<a href="http://blog.csdn.net/wuxintdrh/article/details/62246961" rel="nofollow">flume学习01—安装 </a></p>



<h2 id="二flume监控某个目录将数据发送kafka">二、flume监控某个目录，将数据发送kafka</h2>



<h3 id="21flume-source-使用spooldir-监控一个目录下的文件内容">2.1、flume source 使用SpoolDir 监控一个目录下的文件内容</h3>

<p>SpoolDir监控目录下文件， 处理后的文件修改文件名 <br>
<img src="https://img-blog.csdn.net/20180307234814800?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3V4aW50ZHJo/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>



<h2 id="问题-如果上传来的文件还没有上传完spooldir扫描到改文件">问题： 如果上传来的文件,还没有上传完，spoolDir扫描到改文件，</h2>

<p>三、在slave2上  测试消费kafka数据 <br>
./bin/kafka-console-consumer.sh  –bootstrap-server master:9092,slave1:9092,slave2:9092  –topic topic2</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>