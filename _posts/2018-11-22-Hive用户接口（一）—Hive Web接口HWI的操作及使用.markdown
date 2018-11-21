---
layout:     post
title:      Hive用户接口（一）—Hive Web接口HWI的操作及使用
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/liuxiao723846/article/details/53353769				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>Hive提供了三种用户接口：CLI、HWI和客户端方式。其中最常用的的就是CLI，CLI启动的时候，会同时启动一个Hive副本。Client是Hive的客户端，用户连接至Hive Server。在启动 Client模式的时候，需要指出Hive Server所在节点，并且在该节点启动Hive Server。 HWI是通过浏览器访问Hive。本文介绍Hive Web访问接口。</p>
<p><br></p>
<p>启动 <span style="font-family:Calibri;font-size:14px;line-height:26.6667px;">hwi ：</span></p>
<p></p>
<p style="line-height:20pt;font-family:Calibri;font-size:10.5pt;">#hive --service hwi 
</p>
<p style="line-height:20pt;font-family:Calibri;font-size:10.5pt;">ls:cannot access/home/hadoopUser/cloud/hive/apache-hive-0.13.1-bin/lib/hive-hwi-*.war: No suchfile or directory 
</p>
<p style="line-height:20pt;font-family:Calibri;font-size:10.5pt;">报错误的原因是，Hive目前的发行包里没有hwi的war包文件，没有这个war包就不能启动hwi服务。</p>
<br><p><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">1、终端启动HWI服务</span><br></p>
<p><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;"></span></p>
<p style="line-height:20pt;font-size:10.5pt;"><a href="http://apache.fayea.com/hive/" rel="nofollow"><span lang="zh-cn" style="font-family:Calibri;" xml:lang="zh-cn">http://apache.fayea.com/hive/</span></a><span lang="en-us" style="font-family:Calibri;" xml:lang="en-us">
</span><span lang="zh-cn" style="font-family:'宋体';" xml:lang="zh-cn">下载</span><span lang="zh-cn" style="font-family:Calibri;" xml:lang="zh-cn">apache-hive-1.2.1-src.tar.gz</span><span lang="en-us" style="font-family:Calibri;" xml:lang="en-us">
</span><span lang="zh-cn" style="font-family:'宋体';" xml:lang="zh-cn">源码包，然后解压：</span></p>
<p><span style="color:rgb(51,51,51);font-size:14px;line-height:26px;"></span></p><pre><code class="language-html"># tar -zxvf apache-hive-1.2.1-src.tar.gz   
# cd hwi/
# pwd
/usr/local/apache-hive-1.2.1-src/hwi
# jar cvfM0 hive-hwi-1.2.1.war -C web/ . 
# ll
total 164
-rw-r--r-- 1 root root 151343 Nov 26 18:29 hive-hwi-1.2.1.war
-rw-rw-r-- 1 root root   4982 Jun 19  2015 pom.xml
drwxrwxr-x 4 root root   4096 Apr 30  2015 src

drwxrwxr-x 5 root root   4096 Nov 26 18:21 web</code></pre>
<p style="font-family:Arial;font-size:10.5pt;"><span lang="en-us" style="font-family:Calibri;" xml:lang="en-us">2、</span><span lang="zh-cn" style="font-family:'宋体';" xml:lang="zh-cn">将生成的</span><span lang="zh-cn" style="font-family:Calibri;" xml:lang="zh-cn">hive-hwi-1.2.1.war</span><span lang="zh-cn" style="font-family:'宋体';" xml:lang="zh-cn">拷贝到</span><span lang="zh-cn" style="font-family:Calibri;" xml:lang="zh-cn">/usr/local/hive/lib/</span></p>
<p style="font-size:10.5pt;"><span lang="en-us" style="font-family:Calibri;" xml:lang="en-us">3</span><span lang="en-us" xml:lang="en-us"><span style="font-family:'宋体';">、</span></span><span lang="zh-cn" style="font-family:Calibri;" xml:lang="zh-cn">在hive-site.xml中添加HWI配置</span><span lang="zh-cn" style="font-family:'宋体';" xml:lang="zh-cn">：</span></p>
<pre><code class="language-html">&lt;property&gt;  
  &lt;name&gt;hive.hwi.war.file&lt;/name&gt;  
  &lt;value&gt;lib/hive-hwi-1.2.1.war&lt;/value&gt;  
  &lt;description&gt;This sets the path to the HWI war file, relative to ${HIVE_HOME}. &lt;/description&gt;  
&lt;/property&gt;  
&lt;property&gt;  
  &lt;name&gt;hive.hwi.listen.host&lt;/name&gt;  
  &lt;value&gt;centos1&lt;/value&gt;  
  &lt;description&gt;This is the host address the Hive Web Interface will listen on&lt;/description&gt;  
&lt;/property&gt;  
&lt;property&gt;  
  &lt;name&gt;hive.hwi.listen.port&lt;/name&gt;  
    &lt;value&gt;9999&lt;/value&gt;  
    &lt;description&gt;This is the port the Hive Web Interface will listen on&lt;/description&gt;  
&lt;/property&gt; 
</code></pre><br><p style="line-height:20pt;font-family:'宋体';font-size:10.5pt;">4、启动：</p>
<p style="line-height:20pt;font-family:Calibri;font-size:10.5pt;">#hive --service hwi</p>
<p style="line-height:20pt;font-family:Calibri;font-size:10.5pt;">#hive --service metastore</p>
<p style="line-height:20pt;font-family:Calibri;font-size:10.5pt;"><span style="color:#FF0000;"><strong>注</strong></span><span style="color:#FF0000;">：不管是使用HiveCLI、客户端还是HWI访问Hive，都需要首先启动Hive 元数据服务，否则无法访问Hive数据库</span>。</p>
<p style="line-height:20pt;font-size:10.5pt;"><span lang="en-us" style="font-family:Calibri;" xml:lang="en-us">5、</span><span lang="zh-cn" style="font-family:'宋体';" xml:lang="zh-cn">访问：</span></p>
<img src="https://img-blog.csdn.net/20161126185342039?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br><p style="line-height:20pt;font-size:10.5pt;"><span style="font-family:'宋体';color:#FF0000;"><strong>注</strong></span><span style="font-family:'宋体';">：启动时可能会报错，</span><span style="font-family:Calibri;">Unable to find a javaccompiler;com.sun.tools.javac.Main
 is not on the classpath.Perhaps JAVA_HOMEdoes not point to the JDK.It is currently set to
</span></p>
<p style="line-height:20pt;font-size:10.5pt;"><span style="font-family:'宋体';">解决方法：</span><span style="font-family:Calibri;">cp/usr/java/jdk1.7.0_55/lib/tools.jar /usr/local/hive/lib/</span></p>
<br>            </div>
                </div>