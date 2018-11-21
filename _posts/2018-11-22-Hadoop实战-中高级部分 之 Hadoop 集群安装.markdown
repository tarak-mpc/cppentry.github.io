---
layout:     post
title:      Hadoop实战-中高级部分 之 Hadoop 集群安装
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<div><a href="http://sishuok.com/forum/blogPost/list/5833.html" rel="nofollow">Hadoop RestFul</a></div>
<div><a href="http://sishuok.com/forum/blogPost/list/5936.html" rel="nofollow">Hadoop HDFS原理1</a></div>
<div><a href="http://sishuok.com/forum/blogPost/list/5937.html" rel="nofollow">Hadoop HDFS原理2</a></div>
<div><a href="http://sishuok.com/forum/blogPost/list/5938.html" rel="nofollow">Hadoop作业调优参数调整及原理</a></div>
<div><a href="http://sishuok.com/forum/blogPost/list/5939.html" rel="nofollow">Hadoop HA</a></div>
<div><a href="http://sishuok.com/forum/blogPost/list/5961.html" rel="nofollow">Hadoop MapReduce高级编程</a></div>
<div><a href="http://sishuok.com/forum/blogPost/list/5963.html" rel="nofollow">Hadoop IO</a></div>
<div><a href="http://sishuok.com/forum/blogPost/list/5965.html" rel="nofollow">Hadoop MapReduce工作原理</a></div>
<div><a href="http://sishuok.com/forum/blogPost/list/5966.html" rel="nofollow">Hadoop 管理</a></div>
<div><a href="http://sishuok.com/forum/blogPost/list/5969.html" rel="nofollow">Hadoop 集群安装</a></div>
<div><a href="http://sishuok.com/forum/blogPost/list/5968.html" rel="nofollow">Hadoop RPC</a></div>
</div>
<div style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;"><span class="bold" style="font-weight:bold;">第一部分：环境规划</span></div>
<div style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<div class="O">
<div>•用户 test 密码 test</div>
<div>•机器</div>
<div>
<table dir="ltr" cellspacing="0" cellpadding="0" class="mceItemTable" style="border:1px dashed rgb(187,187,187);width:370px;"><tbody><tr><td bgcolor="#00CC99" width="74" height="23" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;border:1px dashed rgb(187,187,187);">
<strong>   主机名</strong></td>
<td bgcolor="#00CC99" width="128" height="23" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;border:1px dashed rgb(187,187,187);">
<strong>IP</strong></td>
<td bgcolor="#00CC99" width="169" height="23" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;border:1px dashed rgb(187,187,187);">
<strong>角色</strong></td>
</tr><tr><td bgcolor="#CBECDE" width="74" height="40" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;border:1px dashed rgb(187,187,187);">
<div>bigdata-1</div>
<div> </div>
</td>
<td bgcolor="#CBECDE" width="128" height="40" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;border:1px dashed rgb(187,187,187);">
172.16.206.205</td>
<td bgcolor="#CBECDE" width="169" height="40" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;border:1px dashed rgb(187,187,187);">
<div>NameNode JobTraker</div>
<div>Sencondary NameNode</div>
</td>
</tr><tr><td bgcolor="#E7F6EF" width="74" height="40" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;border:1px dashed rgb(187,187,187);">
<div>bigdata-2</div>
<div> </div>
</td>
<td bgcolor="#E7F6EF" width="128" height="40" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;border:1px dashed rgb(187,187,187);">
172.16.206.231</td>
<td bgcolor="#E7F6EF" width="169" height="40" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;border:1px dashed rgb(187,187,187);">
DataNode TaskTracker</td>
</tr></tbody></table></div>
<div>
<div class="O">•NameNode元数据目录</div>
<div class="O1">•/home/test/dfs/namenode</div>
<div class="O">•DataNode 数据存放目录</div>
<div class="O1">•/home/test/dfs/datanode</div>
<div class="O">•Sencondary NameNode</div>
<div class="O1">•/home/test/dfs/sencondnode</div>
<div class="O">            </div>
<div class="O">           </div>
<div class="O">
<div class="O">
<div><span class="bold" style="font-weight:bold;">第二部分：安装JDK并配置环境变量</span></div>
<div>
<div class="O">安装 <span lang="en-us" xml:lang="en-us">JDK</span> 并配置环境变量</div>
 
<div>
<div class="O">•2 台机器做同样的事情</div>
<div class="O">•更改执行权限</div>
<div class="O1">•chmod +x jdk-6u24-linux-i586.bin</div>
<div class="O">•安装</div>
<div class="O1">•./jdk-6u24-linux-i586.bin</div>
<div class="O">•配置环境变量</div>
<div class="O1">•Home路径下，打开.bashrc文件，命令如下</div>
<div class="O2">•vi .bashrc</div>
<div class="O1">•添加JAVA环境变量</div>
<div class="O2">•export JAVA_HOME=/home/hadoop/java</div>
<div class="O2">•export PATH=$JAVA_HOME/bin:$PATH</div>
<div class="O2"> </div>
<div class="O"> </div>
<div class="O">
<div class="O">
<div><span class="bold" style="font-weight:bold;">第三部分：建立互信配置SSH</span></div>
<div>
<div class="O"><span class="bold" style="font-weight:bold;">配置互信</span></div>
<div class="O">
<div>每台机器上执行</div>
<div><span lang="en-us" xml:lang="en-us">$ ssh-keygen</span></div>
<div><span lang="en-us" xml:lang="en-us">$ ssh-copy-id   -i   ~/.ssh/id_rsa.pub   bigdata</span> <span lang="en-us" xml:lang="en-us">-1</span></div>
<div><span lang="en-us" xml:lang="en-us">$ ssh-copy-id   -i   ~/.ssh/id_rsa.pub   bigdata-2</span></div>
<div>             </div>
<div>      </div>
</div>
<div class="O">
<div><span class="bold" style="font-weight:bold;">第四部分：安装Hadoop并配置环境变量</span></div>
<div>
<div class="O"><span class="bold" style="font-weight:bold;">安装并配置Hadoop的环境变量</span></div>
<div>
<div class="O">•解压Hadoop，到/home/test路径下</div>
<div class="O1">• tar zxvf hadoop-1.0.0.tar.gz</div>
<div class="O">•建立软连接</div>
<div class="O1">•ln –s   hadoop-1.0.0  hadoop</div>
<div class="O">•配置环境变量</div>
<div class="O1">•vi  .bashrc</div>
<div class="O1">•添加如下内容</div>
<div class="O1">          export HADOOP_CORE_HOME=/home/test/ hadoop</div>
<div class="O1">          export PATH=.:$HADOOP_CORE_HOME/bin:$PATH</div>
<div class="O2"> </div>
<div class="O"> </div>
</div>
<div><span class="bold" style="font-weight:bold;">第五部分：配置Hadoop集群</span></div>
<div class="O">
<div class="O"><span class="bold" style="font-weight:bold;"><span lang="en-us" xml:lang="en-us">core</span><span lang="en-us" xml:lang="en-us">-site.xml</span></span></div>
<div class="O">
<div>&lt;configuration&gt;</div>
<div>&lt;property&gt;</div>
<div>&lt;name&gt;fs.default.name&lt;/name&gt;</div>
<div>&lt;value&gt;hdfs://bigdata-1:9000&lt;/value&gt;</div>
<div>&lt;/property&gt;</div>
<div>&lt;property&gt;</div>
<div>&lt;name&gt;fs.checkpoint.dir&lt;/name&gt;</div>
<div>        &lt;value&gt;/home/test/dfs/sencondnode&lt;/value&gt;</div>
<div>&lt;/property&gt;</div>
<div>&lt;/configuration&gt;</div>
<div>
<div class="O"><span class="bold" lang="en-us" style="font-weight:bold;" xml:lang="en-us">hdfs-site.xml</span></div>
<div class="O">
<div>&lt;configuration&gt;</div>
<div>&lt;property&gt;</div>
<div>            &lt;name&gt;dfs.name.dir&lt;/name&gt;</div>
<div>     &lt;value&gt;/home/test/dfs/namenode &lt;/value&gt;</div>
<div>&lt;/property&gt;</div>
<div>&lt;property&gt;</div>
<div>             &lt;name&gt;dfs.data.dir&lt;/name&gt;</div>
<div>             &lt;value&gt; &gt;/home/test/dfs/datanode&lt;/value&gt;</div>
<div>&lt;/property&gt;</div>
<div>&lt;property&gt;</div>
<div>      &lt;name&gt;dfs.replication&lt;/name&gt;</div>
<div>        &lt;value&gt;1&lt;/value&gt;</div>
<div>&lt;/property&gt;</div>
<div>&lt;/configuration&gt;</div>
<div>  </div>
<div>
<div class="O"><span class="bold" lang="en-us" style="font-weight:bold;" xml:lang="en-us">mapred-site.xml</span></div>
</div>
</div>
<div class="O">
<div>&lt;configuration&gt;</div>
<div>&lt;property&gt;</div>
<div>  &lt;name&gt;mapred.job.tracker&lt;/name&gt;</div>
<div>  &lt;value&gt;bigdata-1:9001&lt;/value&gt;</div>
<div>&lt;/property&gt;</div>
<div>&lt;/configuration&gt;</div>
<div>
<div class="O"><span class="bold" lang="en-us" style="font-weight:bold;" xml:lang="en-us">master与slaves</span></div>
<div class="O">
<div>•master</div>
<div>     文件添加内容如下</div>
<div>                   bigdata-1</div>
<div>•    Slaves</div>
<div>     文件添加内容如下</div>
<div>                    bigdata-2</div>
<div>
<div class="O"><span class="bold" lang="en-us" style="font-weight:bold;" xml:lang="en-us">Hadoop-env.sh</span></div>
<div class="O">
<div>•export JAVA_HOME=/home/tes/soft/java</div>
<div> </div>
<div>
<div class="O">
<div><span class="bold" style="font-weight:bold;">第六部分：启动Hadoop集群</span></div>
<div>
<div class="O"><span class="bold" style="font-weight:bold;">启动<span lang="en-us" xml:lang="en-us">Hadoop</span>集群</span></div>
<div class="O">
<div>•hadoop namenode -format</div>
<div>•start-all.sh</div>
<div>•start-dfs.sh start-mapred.sh</div>
<div>•hadoop-deamon.sh start  namenode</div>
<div>                                              datanode</div>
<div>                                              jobtracker</div>
<div>                                              tasktracker</div>
<div>                                              sencondnamenode</div>
<div>
<div class="O">
<div>  </div>
<div><span class="bold" style="font-weight:bold;">第七部分：测试WordCount 程序</span> </div>
<div>
<div class="O"><span class="bold" style="font-weight:bold;">测试WordCount 程序</span></div>
</div>
<div>
<div class="O">
<div>•hadoop fs –mkdir input</div>
<div>•hadoop fs –put test.txt input</div>
<div>•hadoop jar  hadoop-examples-*.jar   wordcount  input output</div>
<div> </div>
<div><br>
转载请注明出处【   <a href="http://sishuok.com/forum/blogPost/list/0/5969.html" rel="nofollow">http://sishuok.com/forum/blogPost/list/0/5969.html</a> 】</div>
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