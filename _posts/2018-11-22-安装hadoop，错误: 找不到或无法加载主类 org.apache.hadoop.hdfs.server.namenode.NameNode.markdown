---
layout:     post
title:      安装hadoop，错误: 找不到或无法加载主类 org.apache.hadoop.hdfs.server.namenode.NameNode
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="color:rgb(41,249,20);font-family:'Andale Mono';font-size:18px;text-align:justify;background-color:rgb(0,0,0);">hdfs namenode -format</span>没有权限</p>
<p>会报错，前边加个sudo就好了</p>
<p><span style="color:rgb(41,249,20);font-family:'Andale Mono';font-size:18px;text-align:justify;background-color:rgb(0,0,0);">sudo hdfs namenode -format</span><br></p>
<p><br></p>
<p></p>
<p class="p1"><span class="s1">hadoop $ hdfs namenode -format</span></p>
<p class="p1"><span class="s1">错误: 找不到或无法加载主类 org.apache.hadoop.hdfs.server.namenode.NameNode</span></p>
<p class="p1"><span style="font-size:12px;">:hadoop $ hdfs namenode -format</span></p>
<p class="p1"><span class="s1">错误: 找不到或无法加载主类 org.apache.hadoop.hdfs.server.namenode.NameNode</span></p>
<p class="p1"><span class="s1">tripleone:hadoop fwj$ export HADOOP_CLASSPATH=$(cygpath -pw $(hadoop classpath)):$HADOOP_CLASSPATH</span></p>
<p class="p1"><span class="s1">-bash: cygpath: command not found</span></p>
<p class="p1"><span class="s1">tripleone:hadoop fwj$ sudo hdfs namenode -format</span></p>
<p class="p1"><span class="s1">Password:</span></p>
<p class="p1"><span class="s1">18/01/11 13:13:01 INFO namenode.NameNode: STARTUP_MSG:<span> </span></span></p>
<p class="p1"><span class="s1">/************************************************************</span></p>
<p class="p1"><span class="s1">STARTUP_MSG: Starting NameNode</span></p>
<p class="p1"><span class="s1">STARTUP_MSG: <span>  </span>user = root</span></p>
<p class="p1"><span class="s1">STARTUP_MSG: <span>  </span>host = tripleone.local/10.235.146.27</span></p>
<p class="p1"><span class="s1">STARTUP_MSG: <span>  </span>args = [-format]</span></p>
<p class="p1"><span class="s1">STARTUP_MSG: <span>  </span>version = 2.8.2</span></p>
<p class="p1"><span class="s1">STARTUP_MSG: <span>  </span>classpath = /usr/local/Cellar/hadoop/2.8.2/libexec/etc/hadoop:/usr/local/Cellar/hadoop/2.8.2/libexec/share/hadoop/common/lib/activation-1.1.jar:/usr/local/Cellar/hadoop/2.8.2/libexec/share/hadoop/common/lib/apacheds-i18n-2.0.0-M15.jar:/u</span></p>
<br>            </div>
                </div>