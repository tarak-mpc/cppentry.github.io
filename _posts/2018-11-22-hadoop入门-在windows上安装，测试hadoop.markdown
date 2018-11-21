---
layout:     post
title:      hadoop入门-在windows上安装，测试hadoop
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>上一篇简单叙述了怎么样在windows上编译hadoop，接着上一篇，这篇叙述怎么样安装hadoop并进行简单的验证安装是否正确。编译的机器与安装的机器分开。</p>
<p>我编译的机器是windows7，安装的机器是windows 2008 r2.</p>
<p>第一步：编译完之后，会在target目录下生成<span style="font-family:'Courier New';">hadoop-2.2.0.tar.gz文件，将该文件解压到一个目录下面，然后将整个目录拷贝到目标机器上，尽量选择简单的目录，比如e:\hd</span></p>
<p><span style="font-family:'Courier New';">第二步：添加HADOOP_HOME到系统环境变量里，值为e:\hd.并将%HADOOP_HOME%\bin 添加到path里面。</span></p>
<p><span style="font-family:'Courier New';">第三步：配置hadoop</span></p>
<p><span style="font-family:'Courier New';">1.修改core_site.xml文件</span></p>
<p><span style="font-family:'Courier New';">&lt;configuration&gt;<br>
   &lt;property&gt;<br>
      &lt;name&gt;fs.defaultFS&lt;/name&gt;<br>
      &lt;value&gt;hdfs://localhost:9000&lt;/value&gt;<br>
   &lt;/property&gt;<br>
&lt;/configuration&gt;</span></p>
<p><span style="font-family:'Courier New';">2.修改hdfs-site.xml文件</span></p>
<p><span style="font-family:'Courier New';">&lt;configuration&gt;<br>
   &lt;property&gt;<br>
      &lt;name&gt;dfs.replication&lt;/name&gt;<br>
      &lt;value&gt;1&lt;/value&gt;<br>
   &lt;/property&gt;<br>
   &lt;property&gt;<br>
      &lt;name&gt;dfs.namenode.name.dir&lt;/name&gt;<br>
      &lt;value&gt;<strong><tt>file:/hdp/data/dfs/namenode</tt></strong>&lt;/value&gt;<br>
   &lt;/property&gt;<br>
   &lt;property&gt;<br>
      &lt;name&gt;dfs.datanode.data.dir&lt;/name&gt;<br>
      &lt;value&gt;<strong><tt>file:/hdp/data/dfs/datanode</tt></strong>&lt;/value&gt;<br>
   &lt;/property&gt;<br>
&lt;/configuration&gt;<br>
3.修改yarn-site.xml文件</span></p>
<p><span style="font-family:'Courier New';">&lt;configuration&gt;<br>
&lt;property&gt;<br>
   &lt;name&gt;yarn.nodemanager.aux-services&lt;/name&gt;<br>
   &lt;value&gt;mapreduce_shuffle&lt;/value&gt;<br>
&lt;/property&gt;<br>
&lt;property&gt;<br>
   &lt;name&gt;<br>
      yarn.nodemanager.aux-services.mapreduce_shuffle.class<br>
   &lt;/name&gt;<br>
   &lt;value&gt;org.apache.hadoop.mapred.ShuffleHandler&lt;/value&gt;<br>
&lt;/property&gt;<br>
&lt;/configuration&gt;<br>
4.修改mapred-site.xml文件</span></p>
<p><span style="font-family:'Courier New';">&lt;configuration&gt;<br>
    &lt;property&gt;<br>
       &lt;name&gt;mapreduce.framework.name&lt;/name&gt;<br>
       &lt;value&gt;yarn&lt;/value&gt;<br>
    &lt;/property&gt;<br>
&lt;/configuration&gt;<br>
第四步：准备hadoop cluster</span></p>
<p><span style="font-family:'Courier New';">执行</span></p>
<p><span style="font-family:'Courier New';">cd c:/%HADOOP_HOME%/bin<br>
hdfs namenode -format</span></p>
<p><span style="font-family:'Courier New';">说明：如果提示JAVA_HOME没有设置或者设置不对，请安装jdk，最好将jdk安装在没有空格的路径里，然后设置JAVA_HOME系统变量。</span></p>
<p><span style="font-family:'Courier New';">第五步：启动HDFS</span></p>
<p><span style="font-family:'Courier New';">执行</span></p>
<p><span style="font-family:'Courier New';">cd c:/%HADOOP_HOME%/sbin<br>
start-dfs<br>
第六步：启动yarn</span></p>
<p><span style="font-family:'Courier New';">执行命令：</span></p>
<p><span style="font-family:'Courier New';">cd c:/%HADOOP_HOME%/sbin<br>
start-yarn</span></p>
<p><span style="font-family:'Courier New';">第七步：验证hadoop cluster是否工作</span></p>
<p><span style="font-family:'Courier New';">访问<a class="url" href="http://localhost:8042" rel="nofollow">http://localhost:8042</a>和<a class="url" href="http://localhost:50070" rel="nofollow">http://localhost:50070</a></span></p>
<p><span style="font-family:'Courier New';">第八步：测试cluster</span></p>
<p><span style="font-family:'Courier New';">执行</span></p>
<p><span style="font-family:'Courier New';">hadoop dfs -mkdir /test</span></p>
<p><span style="font-family:'Courier New';">以及</span></p>
<p><span style="font-family:'Courier New';">hadoop dfs -ls /<br></span></p>
<p><br></p>
<div>﻿﻿</div>
            </div>
                </div>