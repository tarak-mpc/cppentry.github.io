---
layout:     post
title:      centos7通过命令来查看NameNode的状态（是Active还是Standby）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>通过浏览器虽然可以查看HDFS的NameNode的状态，如果感觉不方便，可以直接使用命令来查看（前提是HDFS已经启动）：</p>

<p>[root@hadoop01 ~]# hdfs haadmin -getServiceState nn1 <br>
active <br>
[root@hadoop01 ~]# hdfs haadmin -getServiceState nn2 <br>
standby <br>
[root@hadoop01 ~]# </p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>