---
layout:     post
title:      centos7下hadoop3.0.3  hdfs状态查看命令
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>centos7下hadoop3.0.3  hdfs状态查看命令</p>

<p>查看安全模式状态 <br>
hdfs  dfsadmin -safemode get</p>

<p>通过命令来查看NameNode的状态（是Active还是Standby） <br>
hdfs haadmin -getServiceState nn1 <br>
hdfs haadmin -getServiceState nn2 <br>
hdfs haadmin -getServiceState nn3</p>

<p>查看zookeeper集群状态 <br>
runRemoteCmd.sh “zkServer.sh status” all</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>