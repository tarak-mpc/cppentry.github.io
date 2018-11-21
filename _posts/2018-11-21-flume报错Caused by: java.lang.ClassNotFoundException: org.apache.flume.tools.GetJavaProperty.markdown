---
layout:     post
title:      flume报错Caused by: java.lang.ClassNotFoundException: org.apache.flume.tools.GetJavaProperty
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>flume运行flume-ng version时报错信息：</p>
<p></p>
<p style="font-family:'微软雅黑';font-size:14px;line-height:21px;">
<span lang="en-us" style="background-color:inherit;" xml:lang="en-us">Exception in thread "main" java.lang.NoClassDefFoundError: org/apache/flume/tools/GetJavaProperty</span></p>
<p style="font-family:'微软雅黑';font-size:14px;line-height:21px;">
<span lang="en-us" style="background-color:inherit;" xml:lang="en-us">Caused by: java.lang.ClassNotFoundException: org.apache.flume.tools.GetJavaProperty</span></p>
<p style="font-family:'微软雅黑';font-size:14px;line-height:21px;">
<span lang="en-us" style="background-color:inherit;" xml:lang="en-us">        at java.net.URLClassLoader$1.run(URLClassLoader.java:202)</span></p>
<p style="font-family:'微软雅黑';font-size:14px;line-height:21px;">
<span lang="en-us" style="background-color:inherit;" xml:lang="en-us">        at java.security.AccessController.doPrivileged(Native Method)</span></p>
<p style="font-family:'微软雅黑';font-size:14px;line-height:21px;">
<span lang="en-us" style="background-color:inherit;" xml:lang="en-us">        at java.net.URLClassLoader.findClass(URLClassLoader.java:190)</span></p>
<p style="font-family:'微软雅黑';font-size:14px;line-height:21px;">
<span lang="en-us" style="background-color:inherit;" xml:lang="en-us">        at java.lang.ClassLoader.loadClass(ClassLoader.java:306)</span></p>
<p style="font-family:'微软雅黑';font-size:14px;line-height:21px;">
<span lang="en-us" style="background-color:inherit;" xml:lang="en-us">        at sun.misc.Launcher$AppClassLoader.loadClass(Launcher.java:301)</span></p>
<p style="font-family:'微软雅黑';font-size:14px;line-height:21px;">
<span lang="en-us" style="background-color:inherit;" xml:lang="en-us">        at java.lang.ClassLoader.loadClass(ClassLoader.java:247)</span></p>
<p style="font-family:'微软雅黑';font-size:14px;line-height:21px;">
<span lang="en-us" style="background-color:inherit;" xml:lang="en-us"></span></p>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><span lang="en-us" style="background-color:inherit;font-family:Calibri, sans-serif;font-size:10.5pt;" xml:lang="en-us">Could not find the main class: org.apache.flume.tools.GetJavaProperty.  Program will exit.</span></div>
<br><p>解决：将你的jdk6变成jdk7</p>
<p><br></p>
<p>变成jdk7后报错变为：</p>
<p><span style="font-size:10.5pt;line-height:21px;font-family:'宋体';">错误</span><span lang="en-us" style="font-family:Calibri, sans-serif;font-size:10.5pt;line-height:21px;" xml:lang="en-us">: </span><span style="font-size:10.5pt;line-height:21px;font-family:'宋体';">找不到或无法加载主类</span><span lang="en-us" style="font-family:Calibri, sans-serif;font-size:10.5pt;line-height:21px;" xml:lang="en-us"> org.apache.flume.tools.GetJavaProperty</span><br></p>
<p><span lang="en-us" style="font-family:Calibri, sans-serif;font-size:10.5pt;line-height:21px;" xml:lang="en-us">将你的hbase配置文件的hbas-env.sh中的配置变为：</span></p>
<p><span lang="en-us" style="font-family:Calibri, sans-serif;font-size:10.5pt;line-height:21px;" xml:lang="en-us"></span></p>
<div style="font-family:Calibri, sans-serif;font-size:14px;line-height:21px;"><span lang="en-us" style="background-color:inherit;font-size:10.5pt;" xml:lang="en-us">1、将hbase的hbase.env.sh的一行配置注释掉</span></div>
<div style="font-family:Calibri, sans-serif;font-size:14px;line-height:21px;"><span lang="en-us" style="background-color:inherit;font-size:10.5pt;" xml:lang="en-us"># Extra Java CLASSPATH elements. Optional.</span></div>
<div style="font-family:Calibri, sans-serif;font-size:14px;line-height:21px;"><span lang="en-us" style="background-color:inherit;font-size:10.5pt;" xml:lang="en-us">#export HBASE_CLASSPATH=/home/hadoop/hbase/conf</span></div>
<div style="font-family:Calibri, sans-serif;font-size:14px;line-height:21px;"><span lang="en-us" style="background-color:inherit;font-size:10.5pt;" xml:lang="en-us">2、或者将</span><span style="line-height:1.5;">HBASE_CLASSPATH改为JAVA_CLASSPATH,配置如下</span></div>
<div style="font-family:Calibri, sans-serif;font-size:14px;line-height:21px;">
<div><span lang="en-us" style="background-color:inherit;font-size:10.5pt;" xml:lang="en-us"># Extra Java CLASSPATH elements. Optional.</span></div>
<div>export JAVA_CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar</div>
</div>
<br><p></p>
            </div>
                </div>