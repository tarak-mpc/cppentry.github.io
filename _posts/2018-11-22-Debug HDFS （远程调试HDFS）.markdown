---
layout:     post
title:      Debug HDFS （远程调试HDFS）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/guotong1988/article/details/48544305				</div>
								            <div id="content_views" class="markdown_views prism-github-gist">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>替换bin/hdfs里的相应的启动配置 <br>
如果不替换直接添加，会有 <br>
ERROR: Cannot load this JVM TI agent twice, check your java command line for duplicate jdwp options. <br>
Error occurred during initialization of VM <br>
agent library failed to init: jdwp <br>
的错误 <br>
当然可以用echo看看启动配置里原来是什么</p>

<p>用sbin/hadoop-daemon.sh start datanode <br>
sbin/hadoop-daemon.sh start namenode启动</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>