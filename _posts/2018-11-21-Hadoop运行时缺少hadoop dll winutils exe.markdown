---
layout:     post
title:      Hadoop运行时缺少hadoop dll winutils exe
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p><strong>分享一下我老师大神的人工智能教程！零基础，通俗易懂！<a href="https://blog.csdn.net/jiangjunshow/article/details/77338485" rel="nofollow">http://blog.csdn.net/jiangjunshow</a></strong></p><p></p><p><strong>也欢迎大家转载本篇文章。分享知识，造福人民，实现我们中华民族伟大复兴！</strong></p><p></p><div class="htmledit_views">                <h1><a></a><span>1 发现问题</span></h1><p><span>在window连接虚拟机Hadoop集群时遇到问题：</span></p><span><strong>1.1 缺少winutils.exe</strong><br>Could not locate executable null \bin\winutils.exe in the hadoop binaries<br><strong>1.2 缺少hadoop.dll<br></strong>Unable to load native-hadoop library for your platform… using builtin-Java classes where applicable<br><br></span><h1><a></a><span>2 解决办法</span></h1><p><span><strong>2.1 下载资源</strong></span></p><p><span>http://download.csdn.NET/detail/lizhiguo18/8764975。首先将hadoop.dll和winutils.exe放到hadoop的bin目录下。</span></p><p><span><strong>2.2 配置环境变量</strong></span></p><p><span>HADOOP_HOME/bin;HADOOP_HOME/sbin</span></p><p><span><br></span></p><p><span>原贴地址：http://blog.csdn.net/lizhiguo18/article/details/46333433</span></p><p><span><br></span></p>            </div><p></p><strong></strong><h4>给我老师的人工智能教程打call！<a href="https://blog.csdn.net/jiangjunshow/article/details/77338485" rel="nofollow">http://blog.csdn.net/jiangjunshow</a></h4><div align="center"><img title="" alt="这里写图片描述" src="https://img-blog.csdn.net/20161220210733446?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3VuaHVhcWlhbmcx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast"></div>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>