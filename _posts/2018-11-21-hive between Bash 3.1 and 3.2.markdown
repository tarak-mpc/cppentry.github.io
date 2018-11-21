---
layout:     post
title:      hive between Bash 3.1 and 3.2
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
启动hive 报错：<br>
[hadoop@master hadoop]$ hive<br>
/exapp/hive/hive-0.8.1/bin/hive: line 179: conditional binary operator expected<br>
/exapp/hive/hive-0.8.1/bin/hive: line 179: syntax error near `=~'<br><p>/exapp/hive/hive-0.8.1/bin/hive: line 179: `if [[ "$HADOOP_VERSION" =~ $hadoop_version_re ]]; then'</p>
<p>自己查看代码</p>
<p><br><span style="color:#3366FF;"># between Bash 3.1 and 3.2</span><br>
hadoop_version_re="^([[:digit:]]+)\.([[:digit:]]+)(\.([[:digit:]]+))?.*$"<br><br>
if [[ "$HADOOP_VERSION" =~ $hadoop_version_re ]]; then.......</p>
<p><br></p>
<p>上面说的bash 版本 必须在3.1和3.2之间</p>
<p><br></p>
<p>查看下自己bash 版本<br><br></p>
<p>[hadoop@master hadoop]$ bash --version<br>
GNU bash,<span style="color:#3366FF;"> version 2.05b.0(1)-release</span> (i386-redhat-linux-gnu)<br>
Copyright (C) 2002 Free Software Foundation, Inc.</p>
<p>貌似才2.05， 遂升级。</p>
<p>地址：<a href="http://ftp.gnu.org/gnu/bash/bash-3.1.tar.gz" rel="nofollow">http://ftp.gnu.org/gnu/bash/bash-3.1.tar.gz</a><br></p>
<p>下载后上传至服务器</p>
<p><br>
[hadoop@master hadoop]$ ls<br><span style="color:#FF0000;">bash-3.1.tar.gz</span>  hive-0.8.1.tar.gz  .....</p>
<p>权限不够切换用户：<br></p>
<p>[hadoop@master hadoop]$ su - root<br></p>
<p>[hadoop@master root]# mv bash-3.1.tar.gz /usr/local/src</p>
<p>[hadoop@master root]# cd /usr/local/src</p>
<p>[hadoop@master root]# tar zxvf bash-3.1.tar.gz</p>
<p>[hadoop@master root]# cd bash-3.1</p>
<p>[hadoop@master root]# ./configure</p>
<p>[hadoop@master root]# make</p>
<p>[hadoop@master root]# make install</p>
<p></p>
<p></p>
<p>重新登录用户，查看：</p>
<p>[hadoop@master hadoop]$ bash -version<br>
GNU bash,<span style="color:#3366FF;"> version 3.1.0</span>(1)-release (i686-pc-linux-gnu)<br>
Copyright (C) 2005 Free Software Foundation, Inc.</p>
<br>
[hadoop@master hadoop]$ hive<br>
Logging initialized using configuration in file:/exapp/hive/hive-0.8.1/conf/hive-log4j.properties<br>
Hive history file=/tmp/hadoop/hive_job_log_hadoop_201302050055_247239527.txt<br><p>hive&gt;</p>
<p><br></p>
<p>进来了，搞定！<br></p>
<p><br><br></p>
<p><br><br><br></p>
<p><br></p>
<br>            </div>
                </div>