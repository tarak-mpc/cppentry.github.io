---
layout:     post
title:      Hadoop脚本——Hadoop实战初级部分学习笔记
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h1 style="font-family:Verdana, Arial, Helvetica, sans-serif;">4、Hadoop脚本</h1>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
1、bin目录</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
hadoop：hadoop shell</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
hadoop-config.sh  给hadoop的一些变量赋值 HADOOP_HOME、HADOOP_CONF等</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
hadoop-deamon.sh</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
call salves.sh</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
start-all.sh</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
start-dfs sh</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
start-balancer sh</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
start-jobhistoryserver sh</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
start-mapred.sh</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
其他的还是参考ppt吧，</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
hadoop namenode –format 格式化 类似于格式化磁盘</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
hadoop fsck</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
hadoop diskcp  “hdfs://localhost:9000/tmp/test” “hdfs://localhost:9000/tmp/test2”  (会启动mapreduce) 虚拟机比较慢。。 集群直接复制，hadoop版本必须一致。</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
hadoop classpath  查看依赖的jar包 </p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
hadoop deamonlog  -getlevel localhost:50070 namenode 看日志级别</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
hadoop deamonlog  -setlevel localhost:50070 namenode info 设置日志级别</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
            </div>
                </div>