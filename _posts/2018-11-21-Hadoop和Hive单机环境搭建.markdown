---
layout:     post
title:      Hadoop和Hive单机环境搭建
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<strong>下载hadoop</strong><br>
hadoop下载地址：<br><a href="http://www.apache.org/dyn/closer.cgi/hadoop/core/" rel="nofollow" style="color:rgb(29,88,209);">http://www.apache.org/dyn/closer.cgi/hadoop/core/</a><br>
这里下载的版本是1.0.3<br>
$ mkdir hadoop<br>
$ wget http://www.fayea.com/apache-mirror/hadoop/core/stable/hadoop-1.0.3.tar.gz .<br><br></p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<strong>安装java</strong><br>
首先用“java -version”查看是否已安装，如果显示类似于java version "1.7.0_147-icedtea 则表示已安装<br>
没有安装可以参考：<a href="http://blog.csdn.net/yang_hui1986527/article/details/6677450" rel="nofollow" style="color:rgb(29,88,209);">http://blog.csdn.net/yang_hui1986527/article/details/6677450</a><br>
安装必须设置JAVA_HOME和CLASSPATH<br>
我的配置：<br>
export PATH=${PATH}:/usr/lib/jvm/java-6-openjdk-amd64/bin<br>
export JAVA_HOME=/usr/lib/jvm/java-6-openjdk-amd64/<br>
export JRE_HOME=${JAVA_HOME}/jre<br>
export CLASSPATH=.:${JRE_HOME}/lib:${JAVA_HOME}/lib/dt.jar:${JAVA_HOME}/lib/tools.jar<br>
并将这两行语句放在：～/.bashrc中<br><br></p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<strong>安装ssh</strong><br>
$ sudo apt-get install ssh<br>
安装完成后，需要确认可以用ssh免密码登录localhost<br>
$ ssh localhost<br>
如果需要密码才能登录，则需要设置：<br>
$ ssh-keygen -t dsa -P '' -f ~/.ssh/id_dsa<br>
$ cat ~/.ssh/id_dsa.pub &gt;&gt; ~/.ssh/authorized_keys<br><br><strong>安装rsync</strong><br>
rsync是linux实现远程同步的软件<br>
$ sudo apt-get install rsync<br><br><strong>配置启动hadoop</strong><br>
解压：<br>
$ tar -zxvf  hadoop-1.0.3.tar.gz<br>
设置JAVA_HOME<br>
编辑conf/hadoop-env.sh文件，找到：<br>
# export JAVA_HOME=/usr/lib/j2sdk1.5-sun<br>
修改为：<br>
export JAVA_HOME=/usr/lib/jvm/java-6-openjdk-amd64/<br><br>
修改配置文件：<br>
如果不知道java在什么地方请用whereis java查询<br>
修改conf/core-site.xml：</p>
<div class="dp-highlighter bg_plain" style="font-family:Consolas, 'Courier New', Courier, mono, serif;overflow:auto;">
<div class="bar">
<div class="tools" style="font-size:9px;line-height:normal;font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;color:#C0C0C0;background-color:rgb(248,248,248);border-left:3px solid rgb(108,226,108);">
<strong>[plain]</strong> <a class="ViewSource" title="view plain" href="http://blog.csdn.net/yfkiss/article/details/7715476" rel="nofollow" style="color:rgb(160,160,160);background-color:inherit;border:none;font-size:9px;">view
 plain</a><a class="CopyToClipboard" title="copy" href="http://blog.csdn.net/yfkiss/article/details/7715476" rel="nofollow" style="color:rgb(160,160,160);background-color:inherit;border:none;font-size:9px;">copy</a>
<div> </div>
</div>
</div>
<ol start="1" style="border:none;color:rgb(92,92,92);"><li class="alt" style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);list-style-type:decimal;color:inherit;line-height:1.6;list-style-position:outside !important;">
&lt;configuration&gt;  </li><li style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);list-style-type:decimal;background-color:rgb(248,248,248);line-height:1.6;list-style-position:outside !important;">
&lt;property&gt;  </li><li class="alt" style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);list-style-type:decimal;color:inherit;line-height:1.6;list-style-position:outside !important;">
&lt;name&gt;fs.default.name&lt;/name&gt;  </li><li style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);list-style-type:decimal;background-color:rgb(248,248,248);line-height:1.6;list-style-position:outside !important;">
&lt;value&gt;hdfs://localhost:9000&lt;/value&gt;  </li><li class="alt" style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);list-style-type:decimal;color:inherit;line-height:1.6;list-style-position:outside !important;">
&lt;/property&gt;  </li><li style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);list-style-type:decimal;background-color:rgb(248,248,248);line-height:1.6;list-style-position:outside !important;">
  </li><li class="alt" style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);list-style-type:decimal;color:inherit;line-height:1.6;list-style-position:outside !important;">
&lt;/configuration&gt;  </li></ol></div>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
修改conf/hdfs-site.xml:</p>
<div class="dp-highlighter bg_plain" style="font-family:Consolas, 'Courier New', Courier, mono, serif;overflow:auto;">
<div class="bar">
<div class="tools" style="font-size:9px;line-height:normal;font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;color:#C0C0C0;background-color:rgb(248,248,248);border-left:3px solid rgb(108,226,108);">
<strong>[plain]</strong> <a class="ViewSource" title="view plain" href="http://blog.csdn.net/yfkiss/article/details/7715476" rel="nofollow" style="color:rgb(160,160,160);background-color:inherit;border:none;font-size:9px;">view
 plain</a><a class="CopyToClipboard" title="copy" href="http://blog.csdn.net/yfkiss/article/details/7715476" rel="nofollow" style="color:rgb(160,160,160);background-color:inherit;border:none;font-size:9px;">copy</a>
<div> </div>
</div>
</div>
<ol start="1" style="border:none;color:rgb(92,92,92);"><li class="alt" style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);list-style-type:decimal;color:inherit;line-height:1.6;list-style-position:outside !important;">
&lt;configuration&gt;  </li><li style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);list-style-type:decimal;background-color:rgb(248,248,248);line-height:1.6;list-style-position:outside !important;">
&lt;property&gt;  </li><li class="alt" style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);list-style-type:decimal;color:inherit;line-height:1.6;list-style-position:outside !important;">
&lt;name&gt;dfs.replication&lt;/name&gt;  </li><li style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);list-style-type:decimal;background-color:rgb(248,248,248);line-height:1.6;list-style-position:outside !important;">
&lt;value&gt;1&lt;/value&gt;  </li><li class="alt" style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);list-style-type:decimal;color:inherit;line-height:1.6;list-style-position:outside !important;">
&lt;/property&gt;  </li><li style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);list-style-type:decimal;background-color:rgb(248,248,248);line-height:1.6;list-style-position:outside !important;">
&lt;property&gt;  </li><li class="alt" style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);list-style-type:decimal;color:inherit;line-height:1.6;list-style-position:outside !important;">
&lt;name&gt;hadoop.tmp.dir&lt;/name&gt;  </li><li style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);list-style-type:decimal;background-color:rgb(248,248,248);line-height:1.6;list-style-position:outside !important;">
&lt;value&gt;/home/work/hadoop_tmp&lt;/value&gt;  </li><li class="alt" style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);list-style-type:decimal;color:inherit;line-height:1.6;list-style-position:outside !important;">
&lt;/property&gt;  </li><li style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);list-style-type:decimal;background-color:rgb(248,248,248);line-height:1.6;list-style-position:outside !important;">
&lt;/configuration&gt;  </li></ol></div>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
修改conf/mapred-site.xml：</p>
<div class="dp-highlighter bg_plain" style="font-family:Consolas, 'Courier New', Courier, mono, serif;overflow:auto;">
<div class="bar">
<div class="tools" style="font-size:9px;line-height:normal;font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;color:#C0C0C0;background-color:rgb(248,248,248);border-left:3px solid rgb(108,226,108);">
<strong>[plain]</strong> <a class="ViewSource" title="view plain" href="http://blog.csdn.net/yfkiss/article/details/7715476" rel="nofollow" style="color:rgb(160,160,160);background-color:inherit;border:none;font-size:9px;">view
 plain</a><a class="CopyToClipboard" title="copy" href="http://blog.csdn.net/yfkiss/article/details/7715476" rel="nofollow" style="color:rgb(160,160,160);background-color:inherit;border:none;font-size:9px;">copy</a>
<div> </div>
</div>
</div>
<ol start="1" style="border:none;color:rgb(92,92,92);"><li class="alt" style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);list-style-type:decimal;color:inherit;line-height:1.6;list-style-position:outside !important;">
&lt;configuration&gt;  </li><li style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);list-style-type:decimal;background-color:rgb(248,248,248);line-height:1.6;list-style-position:outside !important;">
&lt;property&gt;  </li><li class="alt" style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);list-style-type:decimal;color:inherit;line-height:1.6;list-style-position:outside !important;">
&lt;name&gt;mapred.job.tracker&lt;/name&gt;  </li><li style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);list-style-type:decimal;background-color:rgb(248,248,248);line-height:1.6;list-style-position:outside !important;">
&lt;value&gt;localhost:9001&lt;/value&gt;  </li><li class="alt" style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);list-style-type:decimal;color:inherit;line-height:1.6;list-style-position:outside !important;">
&lt;/property&gt;  </li><li style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);list-style-type:decimal;background-color:rgb(248,248,248);line-height:1.6;list-style-position:outside !important;">
&lt;/configuration&gt;  </li></ol></div>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
初始化hadoop Namenode：<br>
$ bin/hadoop namenode –format<br>
启动：<br>
$ bin/start-all.sh<br>
确认启动：<br>
$ jps<br>
5146 Jps<br>
4538 TaskTracker<br>
4312 JobTracker<br>
4015 DataNode<br>
4228 SecondaryNameNode<br>
3789 NameNode<br>
表示启动成功<br><br><strong>以下内容写入~/.bashrc:</strong></p>
<div class="dp-highlighter bg_plain" style="font-family:Consolas, 'Courier New', Courier, mono, serif;overflow:auto;">
<div class="bar">
<div class="tools" style="font-size:9px;line-height:normal;font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;color:#C0C0C0;background-color:rgb(248,248,248);border-left:3px solid rgb(108,226,108);">
<strong>[plain]</strong> <a class="ViewSource" title="view plain" href="http://blog.csdn.net/yfkiss/article/details/7715476" rel="nofollow" style="color:rgb(160,160,160);background-color:inherit;border:none;font-size:9px;">view
 plain</a><a class="CopyToClipboard" title="copy" href="http://blog.csdn.net/yfkiss/article/details/7715476" rel="nofollow" style="color:rgb(160,160,160);background-color:inherit;border:none;font-size:9px;">copy</a>
<div> </div>
</div>
</div>
<ol start="1" style="border:none;color:rgb(92,92,92);"><li class="alt" style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);list-style-type:decimal;color:inherit;line-height:1.6;list-style-position:outside !important;">
alias hadoop='/home/zxm/hadoop/hadoop-1.0.3/bin/hadoop'    </li><li style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);list-style-type:decimal;background-color:rgb(248,248,248);line-height:1.6;list-style-position:outside !important;">
alias hls='hadoop fs -ls'   </li><li class="alt" style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);list-style-type:decimal;color:inherit;line-height:1.6;list-style-position:outside !important;">
alias hlsr='hadoop fs -lsr'   </li><li style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);list-style-type:decimal;background-color:rgb(248,248,248);line-height:1.6;list-style-position:outside !important;">
alias hcp='hadoop fs -cp '   </li><li class="alt" style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);list-style-type:decimal;color:inherit;line-height:1.6;list-style-position:outside !important;">
alias hmv='hadoop fs -mv'   </li><li style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);list-style-type:decimal;background-color:rgb(248,248,248);line-height:1.6;list-style-position:outside !important;">
alias hget='hadoop fs -get'   </li><li class="alt" style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);list-style-type:decimal;color:inherit;line-height:1.6;list-style-position:outside !important;">
alias hput='hadoop fs -put'   </li><li style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);list-style-type:decimal;background-color:rgb(248,248,248);line-height:1.6;list-style-position:outside !important;">
alias hrm='hadoop fs -rm'   </li><li class="alt" style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);list-style-type:decimal;color:inherit;line-height:1.6;list-style-position:outside !important;">
alias hmkdir='hadoop fs -mkdir'   </li><li style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);list-style-type:decimal;background-color:rgb(248,248,248);line-height:1.6;list-style-position:outside !important;">
alias hcat='hadoop fs -cat'   </li><li class="alt" style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);list-style-type:decimal;color:inherit;line-height:1.6;list-style-position:outside !important;">
alias hrmr='hadoop fs -rmr'   </li><li style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);list-style-type:decimal;background-color:rgb(248,248,248);line-height:1.6;list-style-position:outside !important;">
alias hstat='hadoop fs -stat'   </li><li class="alt" style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);list-style-type:decimal;color:inherit;line-height:1.6;list-style-position:outside !important;">
alias htest='hadoop fs -test'   </li><li style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);list-style-type:decimal;background-color:rgb(248,248,248);line-height:1.6;list-style-position:outside !important;">
alias htext='hadoop fs -text'   </li><li class="alt" style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);list-style-type:decimal;color:inherit;line-height:1.6;list-style-position:outside !important;">
alias htouchz='hadoop fs -touchz'   </li><li style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);list-style-type:decimal;background-color:rgb(248,248,248);line-height:1.6;list-style-position:outside !important;">
alias hdu='hadoop fs -du'   </li><li class="alt" style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);list-style-type:decimal;color:inherit;line-height:1.6;list-style-position:outside !important;">
alias hdus='hadoop fs -dus'   </li><li style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);list-style-type:decimal;background-color:rgb(248,248,248);line-height:1.6;list-style-position:outside !important;">
alias hchmod='hadoop fs -chmod'   </li><li class="alt" style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);list-style-type:decimal;color:inherit;line-height:1.6;list-style-position:outside !important;">
alias hchgrp='hadoop fs -chgrp'   </li><li style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);list-style-type:decimal;background-color:rgb(248,248,248);line-height:1.6;list-style-position:outside !important;">
alias hchown='hadoop fs -chown'   </li><li class="alt" style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);list-style-type:decimal;color:inherit;line-height:1.6;list-style-position:outside !important;">
alias htail='hadoop fs -tail'&lt;span style="font-family:Arial, Helvetica, sans-serif;"&gt;&lt;span style="white-space: normal;"&gt;  </li><li style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);list-style-type:decimal;background-color:rgb(248,248,248);line-height:1.6;list-style-position:outside !important;">
&lt;/span&gt;&lt;/span&gt;  </li></ol></div>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<br>
常见问题解决方案：<br>
问题1：运行hadoop命令是出现“Warning: $HADOOP_HOME is deprecated.”报警<br>
解决：添加   export HADOOP_HOME_WARN_SUPPRESS=TRUE 到 hadoop-env.sh  中<br><br>
问题2：namenode无法启动<br>
解决：删除/tmp/hadoop*　执行bin/hadoop namenode –format</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
在<a href="http://blog.csdn.net/yfkiss/article/details/7715476" rel="nofollow" style="color:rgb(29,88,209);">hadoop单机环境搭建</a>成功后，可以搭建hive。<br><br>
在hdfs上建目录：</p>
<div class="dp-highlighter bg_plain" style="font-family:Consolas, 'Courier New', Courier, mono, serif;overflow:auto;">
<div class="bar">
<div class="tools" style="font-size:9px;line-height:normal;font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;color:#C0C0C0;background-color:rgb(248,248,248);border-left:3px solid rgb(108,226,108);">
<strong>[plain]</strong> <a class="ViewSource" title="view plain" href="http://blog.csdn.net/yfkiss/article/details/7721329" rel="nofollow" style="color:rgb(160,160,160);background-color:inherit;border:none;font-size:9px;">view
 plain</a><a class="CopyToClipboard" title="copy" href="http://blog.csdn.net/yfkiss/article/details/7721329" rel="nofollow" style="color:rgb(160,160,160);background-color:inherit;border:none;font-size:9px;">copy</a>
<div> </div>
</div>
</div>
<ol start="1" style="border:none;color:rgb(92,92,92);"><li class="alt" style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);list-style-type:decimal;color:inherit;line-height:1.6;list-style-position:outside !important;">
$ hadoop fs -mkdir /tmp  </li><li style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);list-style-type:decimal;background-color:rgb(248,248,248);line-height:1.6;list-style-position:outside !important;">
$ hadoop fs -mkdir /user/hive/warehouse  </li></ol></div>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<br>
添加权限：</p>
<div class="dp-highlighter bg_plain" style="font-family:Consolas, 'Courier New', Courier, mono, serif;overflow:auto;">
<div class="bar">
<div class="tools" style="font-size:9px;line-height:normal;font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;color:#C0C0C0;background-color:rgb(248,248,248);border-left:3px solid rgb(108,226,108);">
<strong>[plain]</strong> <a class="ViewSource" title="view plain" href="http://blog.csdn.net/yfkiss/article/details/7721329" rel="nofollow" style="color:rgb(160,160,160);background-color:inherit;border:none;font-size:9px;">view
 plain</a><a class="CopyToClipboard" title="copy" href="http://blog.csdn.net/yfkiss/article/details/7721329" rel="nofollow" style="color:rgb(160,160,160);background-color:inherit;border:none;font-size:9px;">copy</a>
<div> </div>
</div>
</div>
<ol start="1" style="border:none;color:rgb(92,92,92);"><li class="alt" style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);list-style-type:decimal;color:inherit;line-height:1.6;list-style-position:outside !important;">
$ hadoop fs -chmod g+w   /tmp  </li><li style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);list-style-type:decimal;background-color:rgb(248,248,248);line-height:1.6;list-style-position:outside !important;">
$ hadoop fs -chmod g+w   /user/hive/warehouse  </li></ol></div>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<br>
下载解压hive：<br>
$ wget http://labs.mop.com/apache-mirror/hive/stable/hive-0.8.1.tar.gz .<br>
$ tar -zxvf hive-0.8.1.tar.gz<br><br>
设置HADOOP_HOME、HIVE_HOME，并将其添加到~/.bashrc</p>
<div class="dp-highlighter bg_plain" style="font-family:Consolas, 'Courier New', Courier, mono, serif;overflow:auto;">
<div class="bar">
<div class="tools" style="font-size:9px;line-height:normal;font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;color:#C0C0C0;background-color:rgb(248,248,248);border-left:3px solid rgb(108,226,108);">
<strong>[plain]</strong> <a class="ViewSource" title="view plain" href="http://blog.csdn.net/yfkiss/article/details/7721329" rel="nofollow" style="color:rgb(160,160,160);background-color:inherit;border:none;font-size:9px;">view
 plain</a><a class="CopyToClipboard" title="copy" href="http://blog.csdn.net/yfkiss/article/details/7721329" rel="nofollow" style="color:rgb(160,160,160);background-color:inherit;border:none;font-size:9px;">copy</a>
<div> </div>
</div>
</div>
<ol start="1" style="border:none;color:rgb(92,92,92);"><li class="alt" style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);list-style-type:decimal;color:inherit;line-height:1.6;list-style-position:outside !important;">
export HADOOP_HOME=/home/zxm/hadoop/hadoop-1.0.3  </li><li style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);list-style-type:decimal;background-color:rgb(248,248,248);line-height:1.6;list-style-position:outside !important;">
export HIVE_HOME=/home/work/hadoop/hive-0.8.1  </li></ol></div>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<br>
多用户支持<br>
(确认已安装好mysql)<br>
启动mysql：<br>
$ mysql -u root -p<br>
mysql&gt;grant all on hive.* to hive@localhost identified by '123456'</p>
<div style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">修改hive conf/hive-site.xml，如下：<br><div class="dp-highlighter bg_plain" style="font-family:Consolas, 'Courier New', Courier, mono, serif;font-size:12px;overflow:auto;">
<div class="bar">
<div class="tools" style="font-size:9px;line-height:normal;font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;color:#C0C0C0;background-color:rgb(248,248,248);border-left:3px solid rgb(108,226,108);">
<strong>[plain]</strong> <a class="ViewSource" title="view plain" href="http://blog.csdn.net/yfkiss/article/details/7721329" rel="nofollow" style="color:rgb(160,160,160);background-color:inherit;border:none;font-size:9px;">view
 plain</a><a class="CopyToClipboard" title="copy" href="http://blog.csdn.net/yfkiss/article/details/7721329" rel="nofollow" style="color:rgb(160,160,160);background-color:inherit;border:none;font-size:9px;">copy</a>
<div> </div>
</div>
</div>
<ol start="1" style="border:none;color:rgb(92,92,92);"><li class="alt" style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);list-style-type:decimal;color:inherit;line-height:1.6;list-style-position:outside !important;">
&lt;property&gt;    </li><li style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);list-style-type:decimal;background-color:rgb(248,248,248);line-height:1.6;list-style-position:outside !important;">
  &lt;name&gt;javax.jdo.option.ConnectionURL&lt;/name&gt;    </li><li class="alt" style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);list-style-type:decimal;color:inherit;line-height:1.6;list-style-position:outside !important;">
  &lt;value&gt;jdbc:mysql://localhost:3306/hive?createDatabaseIfNotExist=true&lt;alue&gt;    </li><li style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);list-style-type:decimal;background-color:rgb(248,248,248);line-height:1.6;list-style-position:outside !important;">
  &lt;description&gt;JDBC connect string for a JDBC metastore&lt;/description&gt;    </li><li class="alt" style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);list-style-type:decimal;color:inherit;line-height:1.6;list-style-position:outside !important;">
&lt;/property&gt;    </li><li style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);list-style-type:decimal;background-color:rgb(248,248,248);line-height:1.6;list-style-position:outside !important;">
&lt;property&gt;    </li><li class="alt" style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);list-style-type:decimal;color:inherit;line-height:1.6;list-style-position:outside !important;">
  &lt;name&gt;javax.jdo.option.ConnectionDriverName&lt;/name&gt;    </li><li style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);list-style-type:decimal;background-color:rgb(248,248,248);line-height:1.6;list-style-position:outside !important;">
  &lt;value&gt;com.mysql.jdbc.Driver&lt;alue&gt;    </li><li class="alt" style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);list-style-type:decimal;color:inherit;line-height:1.6;list-style-position:outside !important;">
  &lt;description&gt;Driver class name for a JDBC metastore&lt;/description&gt;    </li><li style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);list-style-type:decimal;background-color:rgb(248,248,248);line-height:1.6;list-style-position:outside !important;">
&lt;/property&gt;    </li><li class="alt" style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);list-style-type:decimal;color:inherit;line-height:1.6;list-style-position:outside !important;">
&lt;property&gt;    </li><li style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);list-style-type:decimal;background-color:rgb(248,248,248);line-height:1.6;list-style-position:outside !important;">
  &lt;name&gt;javax.jdo.option.ConnectionUserName&lt;/name&gt;    </li><li class="alt" style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);list-style-type:decimal;color:inherit;line-height:1.6;list-style-position:outside !important;">
  &lt;value&gt;hive&lt;alue&gt;    </li><li style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);list-style-type:decimal;background-color:rgb(248,248,248);line-height:1.6;list-style-position:outside !important;">
  &lt;description&gt;username to use against metastore database&lt;/description&gt;    </li><li class="alt" style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);list-style-type:decimal;color:inherit;line-height:1.6;list-style-position:outside !important;">
&lt;/property&gt;    </li><li style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);list-style-type:decimal;background-color:rgb(248,248,248);line-height:1.6;list-style-position:outside !important;">
&lt;property&gt;    </li><li class="alt" style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);list-style-type:decimal;color:inherit;line-height:1.6;list-style-position:outside !important;">
  &lt;name&gt;javax.jdo.option.ConnectionPassword&lt;/name&gt;    </li><li style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);list-style-type:decimal;background-color:rgb(248,248,248);line-height:1.6;list-style-position:outside !important;">
  &lt;value&gt;123456&lt;alue&gt;    </li><li class="alt" style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);list-style-type:decimal;color:inherit;line-height:1.6;list-style-position:outside !important;">
  &lt;description&gt;password to use against metastore database&lt;/description&gt;    </li><li style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);list-style-type:decimal;background-color:rgb(248,248,248);line-height:1.6;list-style-position:outside !important;">
&lt;/property&gt;  </li></ol></div>
下载mysql jdbc包：<br>
wget http://downloads.mysql.com/archives/mysql-connector-java-5.0/mysql-connector-java-5.0.8.tar.gz .<br>
解压：<br>
tar -zxvf mysql-connector-java-5.0.8.tar.gz </div>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
将mysql-connector-java-5.0.8-bin.jar拷贝到hive lib目录下：<br>
cp mysql-connector-java-5.0.8/mysql-connector-java-5.0.8-bin.jar  ./lib<br><br>
启动hive：<br>
$ cd /home/zxm/hadoop/hive-0.8.1 ; ./bin/hive<br><br>
测试：<br>
$ ./hive<br>
WARNING: org.apache.hadoop.metrics.jvm.EventCounter is deprecated. Please use org.apache.hadoop.log.metrics.EventCounter in all the log4j.properties files.<br>
Logging initialized using configuration in jar:file:/home/zxm/hadoop/hive-0.8.1/lib/hive-common-0.8.1.jar!/hive-log4j.properties<br>
Hive history file=/tmp/work/hive_job_log_work_201207051945_218572007.txt<br>
hive&gt; SHOW TABLES;<br>
OK<br>
Time taken: 7.281 seconds<br>
hive&gt; CREATE TABLE pokes (foo INT, bar STRING);<br>
OK<br>
Time taken: 0.398 seconds<br>
hive&gt; SHOW TABLES;                             <br>
OK<br>
pokes<br>
Time taken: 0.181 seconds<br>
hive&gt; DESCRIBE pokes;<br>
OK<br>
foo     int<br>
bar     string<br>
Time taken: 0.58 seconds<br>
hive&gt; </p>
            </div>
                </div>