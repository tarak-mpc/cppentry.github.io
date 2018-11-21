---
layout:     post
title:      Hadoop实战-初级部分 之 Shell
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p></p>
<div class="div_content_text_all" style="border:0px;overflow:hidden;line-height:1.5em;">
<div class="O" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;"><span style="background-color:rgb(255,255,255);"><span style="font-family:'Comic Sans MS';font-size:18px;"><strong>第一部分：Hadoop Bin</strong></span></span></div>
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;"><span style="background-color:rgb(255,255,255);"><span style="font-family:'Comic Sans MS';font-size:18px;">后面根据项目的实际需要Hadoop Bin  包括:</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span style="background-color:rgb(255,255,255);"><span style="font-family:'Comic Sans MS';font-size:18px;">Hadoop  hadoop的Shell</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span style="background-color:rgb(255,255,255);"><span style="font-family:'Comic Sans MS';font-size:18px;">hadoop-config.sh 它的作用是对一些变量进行赋值</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span style="background-color:rgb(255,255,255);"><span style="font-family:'Comic Sans MS';font-size:18px;">     HADOOP_HOME（hadoop的安装目录）。</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span style="background-color:rgb(255,255,255);"><span style="font-family:'Comic Sans MS';font-size:18px;">     HADOOP_CONF_DIR（hadoop的配置文件目录）。HADOOP_SLAVES（--hosts指定的文件的地址）</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span style="background-color:rgb(255,255,255);"><span style="font-family:'Comic Sans MS';font-size:18px;">hadoop-daemon.sh 单节点启动</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span style="background-color:rgb(255,255,255);"><span style="font-family:'Comic Sans MS';font-size:18px;">hadoop-daemons.sh 启动slaves.sh和hadoop-daemon.sh</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span style="background-color:rgb(255,255,255);"><span style="font-family:'Comic Sans MS';font-size:18px;">start-all.sh    启动HDFS与MapReduce</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span style="background-color:rgb(255,255,255);"><span style="font-family:'Comic Sans MS';font-size:18px;">start-balancer.sh 启动Hadoop的负载均衡</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span style="background-color:rgb(255,255,255);"><span style="font-family:'Comic Sans MS';font-size:18px;">start-dfs.sh  启动HDFS</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span style="background-color:rgb(255,255,255);"><span style="font-family:'Comic Sans MS';font-size:18px;">start-jobhistoryserver.sh</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span style="background-color:rgb(255,255,255);"><span style="font-family:'Comic Sans MS';font-size:18px;">start-mapred.sh  启动MapReduce</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span style="background-color:rgb(255,255,255);"><span style="font-family:'Comic Sans MS';font-size:18px;">来进行调整。</span></span></div>
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;"><span style="background-color:rgb(255,255,255);"><span style="font-family:'Comic Sans MS';font-size:18px;">后面根据项目的实际需要Hadoop Bin  包括:</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span style="background-color:rgb(255,255,255);"><span style="font-family:'Comic Sans MS';font-size:18px;">Hadoop  hadoop的Shell</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span style="background-color:rgb(255,255,255);"><span style="font-family:'Comic Sans MS';font-size:18px;">hadoop-config.sh 它的作用是对一些变量进行赋值</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span style="background-color:rgb(255,255,255);"><span style="font-family:'Comic Sans MS';font-size:18px;">     HADOOP_HOME（hadoop的安装目录）。</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span style="background-color:rgb(255,255,255);"><span style="font-family:'Comic Sans MS';font-size:18px;">     HADOOP_CONF_DIR（hadoop的配置文件目录）。</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span style="background-color:rgb(255,255,255);"><span style="font-family:'Comic Sans MS';font-size:18px;">     HADOOP_SLAVES（--hosts指定的文件的地址）</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span style="background-color:rgb(255,255,255);"><span style="font-family:'Comic Sans MS';font-size:18px;">hadoop-daemon.sh 单节点启动</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span style="background-color:rgb(255,255,255);"><span style="font-family:'Comic Sans MS';font-size:18px;">hadoop-daemons.sh 在所有slaves上运行相同的脚本hadoop-daemon.sh</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span style="background-color:rgb(255,255,255);"><span style="font-family:'Comic Sans MS';font-size:18px;">start-all.sh    启动HDFS与MapReduce</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span style="background-color:rgb(255,255,255);"><span style="font-family:'Comic Sans MS';font-size:18px;">start-balancer.sh 启动Hadoop的负载均衡</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span style="background-color:rgb(255,255,255);"><span style="font-family:'Comic Sans MS';font-size:18px;">start-dfs.sh  启动HDFS</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span style="background-color:rgb(255,255,255);"><span style="font-family:'Comic Sans MS';font-size:18px;">start-jobhistoryserver.sh</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span style="background-color:rgb(255,255,255);"><span style="font-family:'Comic Sans MS';font-size:18px;">start-mapred.sh  启动MapReduce</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span style="background-color:rgb(255,255,255);"><span style="font-family:'Comic Sans MS';font-size:18px;">来进行调整。</span></span></div>
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;"><span style="background-color:rgb(255,255,255);"><span style="font-family:'Comic Sans MS';font-size:18px;">stop-all.sh   停止HDFS与MapReduce</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span style="background-color:rgb(255,255,255);"><span style="font-family:'Comic Sans MS';font-size:18px;">stop-balancer.sh 停止做负载均衡</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span style="background-color:rgb(255,255,255);"><span style="font-family:'Comic Sans MS';font-size:18px;">stop-dfs.sh   停止HDFS</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span style="background-color:rgb(255,255,255);"><span style="font-family:'Comic Sans MS';font-size:18px;">stop-jobhistoryserver.sh 停止Job追踪</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span style="background-color:rgb(255,255,255);"><span style="font-family:'Comic Sans MS';font-size:18px;">stop-mapred.sh 停止MapReduce</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span style="background-color:rgb(255,255,255);"><span style="font-family:'Comic Sans MS';font-size:18px;">task-controller</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span style="background-color:rgb(255,255,255);"><span style="font-family:'Comic Sans MS';font-size:18px;"><br></span></span></div>
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;"><span style="background-color:rgb(255,255,255);"><span style="font-family:'Comic Sans MS';font-size:18px;"><strong>第二部分：Hadoop Shell 基本操作</strong></span></span></div>
<div style="border-width:0px;overflow:hidden;"><span style="background-color:rgb(255,255,255);"><span style="font-family:'Comic Sans MS';font-size:18px;"> </span></span>
<div class="O" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;"><span style="background-color:rgb(255,255,255);"><span style="font-family:'Comic Sans MS';font-size:18px;">nHadoop shell</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span style="background-color:rgb(255,255,255);"><span style="font-family:'Comic Sans MS';font-size:18px;">  包括:    namenode -format     format the DFS filesystem</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span style="background-color:rgb(255,255,255);"><span style="font-family:'Comic Sans MS';font-size:18px;">           secondarynamenode    run the DFS secondary namenode</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span style="background-color:rgb(255,255,255);"><span style="font-family:'Comic Sans MS';font-size:18px;">           namenode             run the DFS namenode</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span style="background-color:rgb(255,255,255);"><span style="font-family:'Comic Sans MS';font-size:18px;">           datanode             run a DFS datanode</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span style="background-color:rgb(255,255,255);"><span style="font-family:'Comic Sans MS';font-size:18px;">           dfsadmin             run a DFS admin client</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span style="background-color:rgb(255,255,255);"><span style="font-family:'Comic Sans MS';font-size:18px;">           mradmin              run a Map-Reduce admin client</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span style="background-color:rgb(255,255,255);"><span style="font-family:'Comic Sans MS';font-size:18px;">           fsck                 run a DFS filesystem checking utility</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span style="background-color:rgb(255,255,255);"><span style="font-family:'Comic Sans MS';font-size:18px;">           fs                   run a generic filesystem user client</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span style="background-color:rgb(255,255,255);"><span style="font-family:'Comic Sans MS';font-size:18px;">           balancer             run a cluster balancing utility</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span style="background-color:rgb(255,255,255);"><span style="font-family:'Comic Sans MS';font-size:18px;">           fetchdt              fetch  delegation token from the NameNode</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span style="background-color:rgb(255,255,255);"><span style="font-family:'Comic Sans MS';font-size:18px;">           jobtracker           run the MapReduce job Tracker node</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span style="background-color:rgb(255,255,255);"><span style="font-family:'Comic Sans MS';font-size:18px;">           pipes                run a Pipes job</span></span></div>
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;"><span style="background-color:rgb(255,255,255);"><span style="font-family:'Comic Sans MS';font-size:18px;">tasktracker          run a MapReduce task Tracker node</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span style="background-color:rgb(255,255,255);"><span style="font-family:'Comic Sans MS';font-size:18px;">  historyserver        run job history servers as a standalone daemon</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span style="background-color:rgb(255,255,255);"><span style="font-family:'Comic Sans MS';font-size:18px;">  job                  manipulate MapReduce jobs</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span style="background-color:rgb(255,255,255);"><span style="font-family:'Comic Sans MS';font-size:18px;">  queue                get information regarding JobQueues</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span style="background-color:rgb(255,255,255);"><span style="font-family:'Comic Sans MS';font-size:18px;">  version              print the version</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span style="background-color:rgb(255,255,255);"><span style="font-family:'Comic Sans MS';font-size:18px;">  jar &lt;jar&gt;            run a jar file</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span style="background-color:rgb(255,255,255);"><span style="font-family:'Comic Sans MS';font-size:18px;">  distcp &lt;srcurl&gt; &lt;desturl&gt; copy file or directories recursively</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span style="background-color:rgb(255,255,255);"><span style="font-family:'Comic Sans MS';font-size:18px;">  archive -archiveName NAME -p &lt;parent path&gt; &lt;src&gt;* &lt;dest&gt; create a hadoop archive</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span style="background-color:rgb(255,255,255);"><span style="font-family:'Comic Sans MS';font-size:18px;">  classpath            prints the class path needed to get the</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span style="background-color:rgb(255,255,255);"><span style="font-family:'Comic Sans MS';font-size:18px;">                       Hadoop jar and the required libraries</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span style="background-color:rgb(255,255,255);"><span style="font-family:'Comic Sans MS';font-size:18px;">  daemonlog            get/set the log level for each daemon</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span style="background-color:rgb(255,255,255);"><span style="font-family:'Comic Sans MS';font-size:18px;"> or</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span style="background-color:rgb(255,255,255);"><span style="font-family:'Comic Sans MS';font-size:18px;">  CLASSNAME            run the class named CLASSNAME</span></span></div>
<div style="border-width:0px;overflow:hidden;"><span style="background-color:rgb(255,255,255);"><span style="font-family:'Comic Sans MS';font-size:18px;"> </span></span></div>
<div style="border-width:0px;overflow:hidden;"><span style="background-color:rgb(255,255,255);"><span style="font-family:'Comic Sans MS';font-size:18px;">  <span>私塾在线学习网原创内容，转载请注明出处【 <a href="http://sishuok.com/forum/blogPost/list/5457.html" rel="nofollow" style="color:rgb(0,94,167);text-decoration:none;">http://sishuok.com/forum/blogPost/list/5457.html</a></span> <span>】</span></span></span></div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
<div style="border-width:0px;overflow:hidden;width:700px;">
<span style="background-color:rgb(255,255,255);"><span style="font-family:'Comic Sans MS';font-size:18px;">资源附件</span></span></div>
<hr style="border-style:solid;border-color:rgb(223,223,223);"><div style="border:1px solid rgb(170,187,204);overflow:hidden;width:700px;">
<a href="http://sishuok.com/forum/download?filename=2012/8/9/fb0f2c2478e073d1fb4038d7dde8c391__Hadoop%20Shell.pdf" rel="nofollow" style="color:rgb(0,94,167);text-decoration:none;background-color:rgb(255,255,255);"><span style="font-family:'Comic Sans MS';font-size:18px;">Hadoop
 Shell.pdf</span></a></div>
<span style="background-color:rgb(255,255,255);"><span style="font-family:'Comic Sans MS';font-size:18px;"><br></span></span>
<p></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'Comic Sans MS';font-size:18px;"><br></span></span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="font-family:'Comic Sans MS';font-size:18px;">原文地址：<a href="http://sishuok.com/forum/blogPost/list/40/5457.html#34329" rel="nofollow">点击打开链接</a></span></span></p>
            </div>
                </div>