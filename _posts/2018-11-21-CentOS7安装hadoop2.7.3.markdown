---
layout:     post
title:      CentOS7安装hadoop2.7.3
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>安装准备工作</p>
<p>下载Hadoop以及jdk</p>
<p>安装hadoop前必须安装jdk   安装方法</p>
<p>http://blog.csdn.net/csdn_bay/article/details/70767421<br></p>
<p>安装完jdk后，把下载好的hadoop解压到/opt/hadoop目录下</p>
<p>修改hadoop配置文件，具体修改文件有5个 </p>
<p>hadoop/etc/hadoop目录下</p>
<p><span style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;text-align:justify;background-color:rgb(250,250,252);">core-site.xml</span><br></p>
<p><span style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;text-align:justify;background-color:rgb(250,250,252);"><span style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;text-align:justify;background-color:rgb(250,250,252);">hdfs-site.xml</span><br></span></p>
<p><span style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;text-align:justify;background-color:rgb(250,250,252);"><span style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;text-align:justify;background-color:rgb(250,250,252);"><span style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;text-align:justify;background-color:rgb(250,250,252);">mapred-site.xml</span><br></span></span></p>
<p><span style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;text-align:justify;background-color:rgb(250,250,252);"><span style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;text-align:justify;background-color:rgb(250,250,252);"><span style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;text-align:justify;background-color:rgb(250,250,252);"><span style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;text-align:justify;background-color:rgb(250,250,252);">hadoop-env.sh</span><br></span></span></span></p>
<p><span style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;text-align:justify;background-color:rgb(250,250,252);"><span style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;text-align:justify;background-color:rgb(250,250,252);"><span style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;text-align:justify;background-color:rgb(250,250,252);"><span style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;text-align:justify;background-color:rgb(250,250,252);"><span style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;text-align:justify;background-color:rgb(250,250,252);">slaves</span><br></span></span></span></span></p>
<p><span style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;text-align:justify;background-color:rgb(250,250,252);"><span style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;text-align:justify;background-color:rgb(250,250,252);"><span style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;text-align:justify;background-color:rgb(250,250,252);"><span style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;text-align:justify;background-color:rgb(250,250,252);"><span style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;text-align:justify;background-color:rgb(250,250,252);"><br></span></span></span></span></span></p>
<p><span style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;text-align:justify;background-color:rgb(250,250,252);"><span style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;text-align:justify;background-color:rgb(250,250,252);"><span style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;text-align:justify;background-color:rgb(250,250,252);"><span style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;text-align:justify;background-color:rgb(250,250,252);"><span style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;text-align:justify;background-color:rgb(250,250,252);"><span style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;text-align:justify;background-color:rgb(250,250,252);">core-site.xml </span><br></span></span></span></span></span></p>
<p><span style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;text-align:justify;background-color:rgb(250,250,252);"><span style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;text-align:justify;background-color:rgb(250,250,252);"><span style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;text-align:justify;background-color:rgb(250,250,252);"><span style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;text-align:justify;background-color:rgb(250,250,252);"><span style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;text-align:justify;background-color:rgb(250,250,252);"><span style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;text-align:justify;background-color:rgb(250,250,252);">&lt;property&gt;<br>
        &lt;name&gt;fs.defaultFS&lt;/name&gt;<br>
        &lt;value&gt;hdfs://127.0.0.1:9000&lt;/value&gt;<br>
    &lt;/property&gt;<br>
    &lt;property&gt;<br>
        &lt;name&gt;hadoop.tmp.dir&lt;/name&gt;<br>
        &lt;value&gt;file:/home/hadoop/tmp&lt;/value&gt;<br>
    &lt;/property&gt;<br>
    &lt;property&gt;<br>
        &lt;name&gt;io.file.buffer.size&lt;/name&gt;<br>
        &lt;value&gt;131702&lt;/value&gt;<br>
    &lt;/property&gt;<br></span></span></span></span></span></span></p>
<p><span style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;text-align:justify;background-color:rgb(250,250,252);"><span style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;text-align:justify;background-color:rgb(250,250,252);"><span style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;text-align:justify;background-color:rgb(250,250,252);"><span style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;text-align:justify;background-color:rgb(250,250,252);"><span style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;text-align:justify;background-color:rgb(250,250,252);"><span style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;text-align:justify;background-color:rgb(250,250,252);"><br></span></span></span></span></span></span></p>
<p><span style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;text-align:justify;background-color:rgb(250,250,252);">hdfs-site.xml</span><br></p>
<p><span style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;text-align:justify;background-color:rgb(250,250,252);">&lt;property&gt;<br>
        &lt;name&gt;dfs.namenode.name.dir&lt;/name&gt;<br>
        &lt;value&gt;file:/home/hadoop/dfs/name&lt;/value&gt;<br>
    &lt;/property&gt;<br>
 &lt;property&gt;<br>
        &lt;name&gt;dfs.datanode.data.dir&lt;/name&gt;<br>
        &lt;value&gt;file:/home/hadoop/dfs/data&lt;/value&gt;<br>
    &lt;/property&gt;<br>
    &lt;property&gt;<br>
        &lt;name&gt;dfs.replication&lt;/name&gt;<br>
        &lt;value&gt;3&lt;/value&gt;<br>
    &lt;/property&gt;<br>
 &lt;property&gt;<br>
    &lt;name&gt;dfs.webhdfs.enabled&lt;/name&gt;<br>
    &lt;value&gt;true&lt;/value&gt;<br>
    &lt;/property&gt;<br></span></p>
<p><span style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;text-align:justify;background-color:rgb(250,250,252);"><br></span></p>
<p><span style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;text-align:justify;background-color:rgb(250,250,252);"><span style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;text-align:justify;background-color:rgb(250,250,252);">mapred-site.xml</span><br></span></p>
<p><span style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;text-align:justify;background-color:rgb(250,250,252);"><span style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;text-align:justify;background-color:rgb(250,250,252);"><br></span></span></p>
<p><span style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;text-align:justify;background-color:rgb(250,250,252);">&lt;property&gt;<br>
        &lt;name&gt;yarn.nodemanager.aux-services&lt;/name&gt;<br>
        &lt;value&gt;mapreduce_shuffle&lt;/value&gt;<br>
    &lt;/property&gt;<br>
    &lt;property&gt;<br>
        &lt;name&gt;yarn.nodemanager.auxservices.mapreduce.shuffle.class&lt;/name&gt;<br>
        &lt;value&gt;org.apache.hadoop.mapred.ShuffleHandler&lt;/value&gt;<br>
    &lt;/property&gt;<br>
    &lt;property&gt;<br>
        &lt;name&gt;yarn.resourcemanager.address&lt;/name&gt;<br>
        &lt;value&gt;127.0.0.1:8032&lt;/value&gt;<br>
    &lt;/property&gt;<br>
    &lt;property&gt;<br>
        &lt;name&gt;yarn.resourcemanager.scheduler.address&lt;/name&gt;<br>
        &lt;value&gt;127.0.0.1:8030&lt;/value&gt;<br>
    &lt;/property&gt;<br>
    &lt;property&gt;<br>
        &lt;name&gt;yarn.resourcemanager.resource-tracker.address&lt;/name&gt;<br>
        &lt;value&gt;127.0.0.1:8031&lt;/value&gt;<br>
    &lt;/property&gt;<br>
    &lt;property&gt;<br>
        &lt;name&gt;yarn.resourcemanager.admin.address&lt;/name&gt;<br>
        &lt;value&gt;127.0.0.1:8033&lt;/value&gt;<br>
    &lt;/property&gt;<br>
    &lt;property&gt;<br>
        &lt;name&gt;yarn.resourcemanager.webapp.address&lt;/name&gt;<br>
        &lt;value&gt;127.0.0.1:8088&lt;/value&gt;<br>
    &lt;/property&gt;<br>
    &lt;property&gt;<br>
        &lt;name&gt;yarn.nodemanager.resource.memory-mb&lt;/name&gt;<br>
        &lt;value&gt;768&lt;/value&gt;<br>
    &lt;/property&gt;<br></span></p>
<p><span style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;text-align:justify;background-color:rgb(250,250,252);"><br></span></p>
<p style="text-align:justify;"><span style="font-family:tahoma, '宋体';color:#333333;"><span style="font-size:14px;background-color:rgb(250,250,252);">slaves 中改为localhost</span></span></p>
<p style="text-align:justify;"><span style="font-family:tahoma, '宋体';color:#333333;"><span style="font-size:14px;background-color:rgb(250,250,252);"><br></span></span></p>
<p style="text-align:justify;"><span style="font-family:tahoma, '宋体';color:#333333;"><span style="font-size:14px;background-color:rgb(250,250,252);">hadoop-env.sh</span></span></p>
<p style="text-align:justify;"><span style="font-family:tahoma, '宋体';color:#333333;"><span style="font-size:14px;background-color:rgb(250,250,252);"><br></span></span></p>
<p style="text-align:justify;"><span style="font-family:tahoma, '宋体';color:#333333;"><span style="font-size:14px;background-color:rgb(250,250,252);">设置JAVA_HOME为jdk安装路径</span></span></p>
<p style="text-align:justify;"><span style="font-family:tahoma, '宋体';color:#333333;"><span style="font-size:14px;background-color:rgb(250,250,252);"><br></span></span></p>
<p style="text-align:justify;"><span style="font-family:tahoma, '宋体';color:#333333;"><span style="font-size:14px;background-color:rgb(250,250,252);">配置完成后   在hadoop/sbin目录下用终端启动start-all.sh （root模式下）输入密码后完成启动，完成启动以后输入jps查看启动情况</span></span></p>
<p style="text-align:justify;"><span style="font-family:tahoma, '宋体';color:#333333;"><span style="font-size:14px;background-color:rgb(250,250,252);"><img src="" alt=""><br></span></span></p>
<p style="text-align:justify;"><span style="font-family:tahoma, '宋体';color:#333333;"><span style="font-size:14px;background-color:rgb(250,250,252);">如果有以上节点说明配置成功</span></span></p>
<p style="text-align:justify;"><span style="font-family:tahoma, '宋体';color:#333333;"><span style="font-size:14px;background-color:rgb(250,250,252);"><br></span></span></p>
<p style="text-align:justify;"><span style="font-family:tahoma, '宋体';color:#333333;"><span style="font-size:14px;background-color:rgb(250,250,252);"><br></span></span></p>
<p style="text-align:justify;"><span style="font-family:tahoma, '宋体';color:#333333;"><span style="font-size:14px;background-color:rgb(250,250,252);">本文参考http://www.linuxidc.com/Linux/2015-11/124800.htm设置的单节点模式</span></span></p>
<p style="text-align:justify;"><span style="font-family:tahoma, '宋体';color:#333333;"><span style="font-size:14px;background-color:rgb(250,250,252);"><br></span></span></p>
            </div>
                </div>