---
layout:     post
title:      运行hadoop中的实例wordcount及问题
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/hujian0923/article/details/77938577				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div style="text-align:left;line-height:1.75;font-size:14px;">
1.准备数据</div>
<div style="text-indent:28px;text-align:left;line-height:1.75;font-size:14px;">
wc.txt wc2.txt</div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
2.在hdfs中创建/wordcount/input文件夹</div>
<div style="text-indent:28px;text-align:left;line-height:1.75;font-size:14px;">
hdfs dfs -mkdir -p /wordcount/input</div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
3.上传到hdfs文件系统中</div>
<div style="text-indent:28px;text-align:left;line-height:1.75;font-size:14px;">
hdfs dfs -put wc.txt wc2.txt /wordcount/input</div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
4.进入apps/hadoop-2.6.4/share/hadoop/mapreduce目录</div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
cd apps/hadoop-2.6.4/share/hadoop/mapreduce</div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
5.运行实例wordcount</div>
<div style="text-indent:28px;text-align:left;line-height:1.75;font-size:14px;">
hadoop jar hadoop-mapreduce-examples-2.6.4.jar wordcount /wordcount/input /wordcount/output</div>
<div style="text-indent:28px;text-align:left;line-height:1.75;font-size:14px;">
<br></div>
<div style="text-align:left;line-height:1.75;font-size:14px;">
<span style="color:#df402a;">注意：</span></div>
<div style="text-indent:28px;text-align:left;line-height:1.75;font-size:14px;">
<span style="color:#df402a;">运行wordcount实例代码时，显示</span></div>
<div style="text-indent:28px;text-align:left;line-height:1.75;font-size:14px;">
INFO ipc.Client: <span style="color:#df402a;">Retrying connect to server</span>: node01/192.168.30.134:8032. Already tried 0 time(s); retry policy is RetryUpToMaximumCountWithFixedSleep(maxRetries=10, sleepTime=1000 MILLISECONDS)<span style="color:#df402a;">会重新连接服务器10次</span></div>
<div style="text-indent:28px;text-align:left;line-height:1.75;font-size:14px;">
<span style="color:#df402a;">原因：</span></div>
<div style="text-indent:56px;text-align:left;line-height:1.75;font-size:14px;">
<span style="color:#df402a;">没有启动yarn或者启动失败</span></div>
<div style="text-indent:28px;text-align:left;line-height:1.75;font-size:14px;">
<span style="color:#df402a;">解决：</span></div>
<div style="text-indent:56px;text-align:left;line-height:1.75;font-size:14px;">
<span style="color:#df402a;">启动yarn</span></div>
<div style="text-indent:56px;text-align:left;line-height:1.75;font-size:14px;">
<span style="color:#df402a;">start-yarn.sh</span></div>
            </div>
                </div>