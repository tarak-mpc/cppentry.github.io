---
layout:     post
title:      client如何访问HA HDFS
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div class="text-center" style="font-size:18px;"><strong>client如何访问HA HDFS</strong></div>
<div class="notes_tags"><span class="glyphicon glyphicon-tags" title="标签" style="font-size:16px;"></span><br></div>
<div id="notes_content">
<div style="line-height:28.18px;background-color:rgb(204,206,208);font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, u5b8bu4f53;color:rgb(129,129,129);font-size:16.36px;">
1：实验目的<br></div>
<br><div style="line-height:28.18px;background-color:rgb(204,206,208);font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, u5b8bu4f53;color:rgb(129,129,129);font-size:16.36px;">
A：client如何访问HA HDFS</div>
<br><div style="line-height:28.18px;background-color:rgb(204,206,208);font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, u5b8bu4f53;color:rgb(129,129,129);font-size:16.36px;">
B：client如何访问HA HDFS + Federation</div>
<br><div style="line-height:28.18px;background-color:rgb(204,206,208);font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, u5b8bu4f53;color:rgb(129,129,129);font-size:16.36px;">
<br></div>
<br><div style="line-height:28.18px;background-color:rgb(204,206,208);font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, u5b8bu4f53;color:rgb(129,129,129);font-size:16.36px;">
2：client如何访问HA HDFS</div>
<br><div style="line-height:28.18px;background-color:rgb(204,206,208);font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, u5b8bu4f53;color:rgb(129,129,129);font-size:16.36px;">
本实验环境搭建指导：<a href="http://mmicky.blog.163.com/blog/static/150290154201401812127758/" rel="nofollow" style="line-height:28px;color:rgb(123,155,189);text-decoration:none;">HDFS HA系列实验之二：HA+JournalNode+zookeeper</a></div>
<br><div style="line-height:28.18px;background-color:rgb(204,206,208);font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, u5b8bu4f53;color:rgb(129,129,129);font-size:16.36px;">
<a href="http://pan.baidu.com/s/1jG5bU6q" rel="nofollow" style="line-height:28px;color:rgb(123,155,189);text-decoration:none;"><span style="line-height:28.18px;color:rgb(255,0,0);">本实验相关的</span></a><a title="hadoop" href="http://www.51studyit.com/tags/10.htm" rel="nofollow">hadoop</a>配置文件下载（HA+JN+ZK）</div>
<br><div style="line-height:28.18px;background-color:rgb(204,206,208);font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, u5b8bu4f53;color:rgb(129,129,129);font-size:16.36px;">
<span style="line-height:28px;">根据上面相关资料重新配置HA+JournalNode+zookeeper环境，并格式化后启动，然后使用物理机192.168.100.111(机器名wyy)作为客户端来访问集群。</span></div>
<br><div style="line-height:28.18px;background-color:rgb(204,206,208);font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, u5b8bu4f53;color:rgb(129,129,129);font-size:16.36px;">
<br></div>
<br><div style="line-height:28.18px;background-color:rgb(204,206,208);font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, u5b8bu4f53;color:rgb(129,129,129);font-size:16.36px;">
A：启动集群</div>
<br><div style="line-height:28.18px;background-color:rgb(204,206,208);font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, u5b8bu4f53;color:rgb(129,129,129);font-size:16.36px;">
[hadoop@product202 ~]$ /app/hadoop/zookeeper345/bin/zkServer.sh start</div>
<br><div style="line-height:28.18px;background-color:rgb(204,206,208);font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, u5b8bu4f53;color:rgb(129,129,129);font-size:16.36px;">
[hadoop@product202 ~]$ /app/hadoop/hadoop220/sbin/hadoop-daemon.sh start journalnode</div>
<br><div style="line-height:28.18px;background-color:rgb(204,206,208);font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, u5b8bu4f53;color:rgb(129,129,129);font-size:16.36px;">
[hadoop@product203 ~]$ /app/hadoop/zookeeper345/bin/zkServer.sh start</div>
<br><div style="line-height:28.18px;background-color:rgb(204,206,208);font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, u5b8bu4f53;color:rgb(129,129,129);font-size:16.36px;">
[hadoop@product203 ~]$ /app/hadoop/hadoop220/sbin/hadoop-daemon.sh start journalnode</div>
<br><div style="line-height:28.18px;background-color:rgb(204,206,208);font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, u5b8bu4f53;color:rgb(129,129,129);font-size:16.36px;">
[hadoop@product204 ~]$ /app/hadoop/zookeeper345/bin/zkServer.sh start</div>
<br><div style="line-height:28.18px;background-color:rgb(204,206,208);font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, u5b8bu4f53;color:rgb(129,129,129);font-size:16.36px;">
[hadoop@product204 ~]$ /app/hadoop/hadoop220/sbin/hadoop-daemon.sh start journalnode</div>
<br><div style="line-height:28.18px;background-color:rgb(204,206,208);font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, u5b8bu4f53;color:rgb(129,129,129);font-size:16.36px;">
<br></div>
<br><div style="line-height:28.18px;background-color:rgb(204,206,208);font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, u5b8bu4f53;color:rgb(129,129,129);font-size:16.36px;">
[hadoop@product201 ~]$ cd /app/hadoop/hadoop220/</div>
<br><div style="line-height:28.18px;background-color:rgb(204,206,208);font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, u5b8bu4f53;color:rgb(129,129,129);font-size:16.36px;">
[hadoop@product201 hadoop220]$ bin/hdfs namenode -format</div>
<br><div style="line-height:28.18px;background-color:rgb(204,206,208);font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, u5b8bu4f53;color:rgb(129,129,129);font-size:16.36px;">
[hadoop@product201 hadoop220]$ bin/hdfs zkfc -formatZK</div>
<br><div style="line-height:28.18px;background-color:rgb(204,206,208);font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, u5b8bu4f53;color:rgb(129,129,129);font-size:16.36px;">
[hadoop@product201 hadoop220]$ sbin/hadoop-daemon.sh start zkfc</div>
<br><div style="line-height:28.18px;background-color:rgb(204,206,208);font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, u5b8bu4f53;color:rgb(129,129,129);font-size:16.36px;">
[hadoop@product202 ~]$ cd /app/hadoop/hadoop220/</div>
<br><div style="line-height:28.18px;background-color:rgb(204,206,208);font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, u5b8bu4f53;color:rgb(129,129,129);font-size:16.36px;">
[hadoop@product202 hadoop220]$ sbin/hadoop-daemon.sh start zkfc</div>
<br><div style="line-height:28.18px;background-color:rgb(204,206,208);font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, u5b8bu4f53;color:rgb(129,129,129);font-size:16.36px;">
[hadoop@product201 hadoop220]$ sbin/hadoop-daemon.sh start namenode</div>
<br><div style="line-height:28.18px;background-color:rgb(204,206,208);font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, u5b8bu4f53;color:rgb(129,129,129);font-size:16.36px;">
[hadoop@product202 hadoop220]$ bin/hdfs namenode -bootstrapStandby</div>
<br><div style="line-height:28.18px;background-color:rgb(204,206,208);font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, u5b8bu4f53;color:rgb(129,129,129);font-size:16.36px;">
[hadoop@product202 hadoop220]$ sbin/hadoop-daemon.sh start namenode</div>
<br><div style="line-height:28.18px;background-color:rgb(204,206,208);font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, u5b8bu4f53;color:rgb(129,129,129);font-size:16.36px;">
[hadoop@product201 hadoop220]$ sbin/hadoop-daemons.sh start datanode</div>
<br><div style="line-height:28.18px;background-color:rgb(204,206,208);font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, u5b8bu4f53;color:rgb(129,129,129);font-size:16.36px;">
<br></div>
<br><div style="line-height:28.18px;background-color:rgb(204,206,208);font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, u5b8bu4f53;color:rgb(129,129,129);font-size:16.36px;">
B：客户端连结测试</div>
<br><div style="line-height:28.18px;background-color:rgb(204,206,208);font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, u5b8bu4f53;color:rgb(129,129,129);font-size:16.36px;">
将hadoop集群的配置文件复制到客户端192.168.100.111(机器名wyy)的配置目录中，并在客户端的/etc/hosts解析集群的NN机器名。</div>
<br><div style="line-height:28.18px;background-color:rgb(204,206,208);font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, u5b8bu4f53;color:rgb(129,129,129);font-size:16.36px;">
hadoop@wyy:/app/hadoop/hadoop220/etc$ scp -r hadoop@product201:/app/hadoop/hadoop220/etc/hadoop ./</div>
<br><div style="line-height:28.18px;background-color:rgb(204,206,208);font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, u5b8bu4f53;color:rgb(129,129,129);font-size:16.36px;">
hadoop@wyy:/app/hadoop/hadoop220/etc$ cat /etc/hosts</div>
<br><div style="line-height:28.18px;background-color:rgb(204,206,208);font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, u5b8bu4f53;color:rgb(129,129,129);font-size:16.36px;">
<pre class="prettyprint" style="border-bottom:rgb(136,136,136) 1px solid;border-left-color:rgb(136,136,136);border-top:rgb(136,136,136) 1px solid;border-left-width:1px;border-right:rgb(136,136,136) 1px solid;"></pre><div style="line-height:28px;font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, '宋体';"><span class="lit" style="line-height:28.18px;color:rgb(0,102,102);">192.168</span><span class="pun" style="line-height:28.18px;color:rgb(102,102,0);">.</span><span class="lit" style="line-height:28.18px;color:rgb(0,102,102);">100.201</span><span class="pln" style="line-height:28.18px;color:rgb(0,0,0);"> product201 product201</span><span class="pun" style="line-height:28.18px;color:rgb(102,102,0);">.</span><span class="pln" style="line-height:28.18px;color:rgb(0,0,0);">product</span></div><div style="line-height:28px;font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, '宋体';"><span class="pln" style="line-height:28.18px;color:rgb(0,0,0);">192</span><span class="pun" style="line-height:28.18px;color:rgb(102,102,0);">.</span><span class="lit" style="line-height:28.18px;color:rgb(0,102,102);">168.100</span><span class="pun" style="line-height:28.18px;color:rgb(102,102,0);">.</span><span class="lit" style="line-height:28.18px;color:rgb(0,102,102);">202</span><span class="pln" style="line-height:28.18px;color:rgb(0,0,0);"> product202 product202</span><span class="pun" style="line-height:28.18px;color:rgb(102,102,0);">.</span><span class="pln" style="line-height:28.18px;color:rgb(0,0,0);">product</span></div>
</div>
<br><div style="line-height:28.18px;background-color:rgb(204,206,208);font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, u5b8bu4f53;color:rgb(129,129,129);font-size:16.36px;">
<span style="line-height:28px;">hadoop@wyy:/app/hadoop/hadoop220$ cat etc/hadoop/core-site.xml</span></div>
<br><div style="line-height:28.18px;background-color:rgb(204,206,208);font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, u5b8bu4f53;color:rgb(129,129,129);font-size:16.36px;">
<pre class="prettyprint" style="border-bottom:rgb(136,136,136) 1px solid;border-left-color:rgb(136,136,136);border-top:rgb(136,136,136) 1px solid;border-left-width:1px;border-right:rgb(136,136,136) 1px solid;"></pre><div style="line-height:28px;font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, '宋体';"><span class="tag" style="line-height:28.18px;color:rgb(0,0,136);">&lt;property&gt;</span></div><div style="line-height:28px;font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, '宋体';"><span class="pln" style="line-height:28.18px;color:rgb(0,0,0);"> </span><span class="tag" style="line-height:28.18px;color:rgb(0,0,136);">&lt;name&gt;</span><span class="pln" style="line-height:28.18px;color:rgb(0,0,0);">fs.defaultFS</span><span class="tag" style="line-height:28.18px;color:rgb(0,0,136);">&lt;/name&gt;</span></div><div style="line-height:28px;font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, '宋体';"><span class="pln" style="line-height:28.18px;color:rgb(0,0,0);"> </span><span class="tag" style="line-height:28.18px;color:rgb(0,0,136);">&lt;value&gt;</span><span class="pln" style="line-height:28.18px;color:rgb(0,0,0);">hdfs://product201:8020</span><span class="tag" style="line-height:28.18px;color:rgb(0,0,136);">&lt;/value&gt;</span></div><div style="line-height:28px;font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, '宋体';"><span class="tag" style="line-height:28.18px;color:rgb(0,0,136);">&lt;/property&gt;</span></div>
</div>
<br><div style="line-height:28.18px;background-color:rgb(204,206,208);font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, u5b8bu4f53;color:rgb(129,129,129);font-size:16.36px;">
<span style="line-height:28px;">hadoop@wyy:/app/hadoop/hadoop220/etc$ cd ..</span></div>
<br><div style="line-height:28.18px;background-color:rgb(204,206,208);font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, u5b8bu4f53;color:rgb(129,129,129);font-size:16.36px;">
hadoop@wyy:/app/hadoop/hadoop220$ bin/hdfs dfs -mkdir /test</div>
<br><div style="line-height:28.18px;background-color:rgb(204,206,208);font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, u5b8bu4f53;color:rgb(129,129,129);font-size:16.36px;">
hadoop@wyy:/app/hadoop/hadoop220$ bin/hdfs dfs -put /home/mmicky/data/2013.txt /test/.</div>
<br><div style="line-height:28.18px;background-color:rgb(204,206,208);font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, u5b8bu4f53;color:rgb(129,129,129);font-size:16.36px;">
hadoop@wyy:/app/hadoop/hadoop220$ bin/hdfs dfs -ls /test</div>
<br><div style="line-height:28.18px;background-color:rgb(204,206,208);font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, u5b8bu4f53;color:rgb(129,129,129);font-size:16.36px;">
Found 1 items</div>
<br><div style="line-height:28.18px;background-color:rgb(204,206,208);font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, u5b8bu4f53;color:rgb(129,129,129);font-size:16.36px;">
-rw-r--r--   3 hadoop supergroup  258570165 2014-02-23 13:35 /test/2013.txt</div>
<br><div style="line-height:28.18px;background-color:rgb(204,206,208);font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, u5b8bu4f53;color:rgb(129,129,129);font-size:16.36px;">
<br></div>
<br><div style="line-height:28.18px;background-color:rgb(204,206,208);font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, u5b8bu4f53;color:rgb(129,129,129);font-size:16.36px;">
杀死product201 namenode进程，模拟HA故障转移：</div>
<br><div style="line-height:28.18px;background-color:rgb(204,206,208);font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, u5b8bu4f53;color:rgb(129,129,129);font-size:16.36px;">
[root@product201 ~]# jps</div>
<br><div style="line-height:28.18px;background-color:rgb(204,206,208);font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, u5b8bu4f53;color:rgb(129,129,129);font-size:16.36px;">
1964 NameNode</div>
<br><div style="line-height:28.18px;background-color:rgb(204,206,208);font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, u5b8bu4f53;color:rgb(129,129,129);font-size:16.36px;">
1906 DFSZKFailoverController</div>
<br><div style="line-height:28.18px;background-color:rgb(204,206,208);font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, u5b8bu4f53;color:rgb(129,129,129);font-size:16.36px;">
2425 Jps</div>
<br><div style="line-height:28.18px;background-color:rgb(204,206,208);font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, u5b8bu4f53;color:rgb(129,129,129);font-size:16.36px;">
2061 DataNode</div>
<br><div style="line-height:28.18px;background-color:rgb(204,206,208);font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, u5b8bu4f53;color:rgb(129,129,129);font-size:16.36px;">
[root@product201 ~]# kill 1964</div>
<br><div style="line-height:28.18px;background-color:rgb(204,206,208);font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, u5b8bu4f53;color:rgb(129,129,129);font-size:16.36px;">
<br></div>
<br><div style="line-height:28.18px;background-color:rgb(204,206,208);font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, u5b8bu4f53;color:rgb(129,129,129);font-size:16.36px;">
<br></div>
<br><div style="line-height:28.18px;background-color:rgb(204,206,208);font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, u5b8bu4f53;color:rgb(129,129,129);font-size:16.36px;">
此时在客户端连结hadoop 集群的时候就会出错：</div>
<br><div style="line-height:28.18px;background-color:rgb(204,206,208);font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, u5b8bu4f53;color:rgb(129,129,129);font-size:16.36px;">
hadoop@wyy:/app/hadoop/hadoop220$ bin/hdfs dfs -ls /test</div>
<br><div style="line-height:28.18px;background-color:rgb(204,206,208);font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, u5b8bu4f53;color:rgb(129,129,129);font-size:16.36px;">
ls: Call From wyy/127.0.1.1 to product201:8020 failed on connection exception: java.net.ConnectException: 拒绝连接; For more details see:  http://wiki.apache.org/hadoop/ConnectionRefused</div>
<br><div style="line-height:28.18px;background-color:rgb(204,206,208);font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, u5b8bu4f53;color:rgb(129,129,129);font-size:16.36px;">
<br></div>
<br><div style="line-height:28.18px;background-color:rgb(204,206,208);font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, u5b8bu4f53;color:rgb(129,129,129);font-size:16.36px;">
C：解决方案</div>
<br><div style="line-height:28.18px;background-color:rgb(204,206,208);font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, u5b8bu4f53;color:rgb(129,129,129);font-size:16.36px;">
使用参数dfs.client.failover.proxy.provider.[nameservice ID]，该参数定义HDFS客户端用来和活动的namenode节目联系的java类。配置的java类是用来给HDFS客户端判断哪个namenode节点是活动的，当前是哪个namenode处理客户端的请求，目前hadoop2.2.0提供了唯一的一个实现类是ConfiguredFailoverProxyProvider，除非你自己定义了一个类，否则都将使用这个类。</div>
<br><div style="line-height:28.18px;background-color:rgb(204,206,208);font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, u5b8bu4f53;color:rgb(129,129,129);font-size:16.36px;">
<br></div>
<br><div style="line-height:28.18px;background-color:rgb(204,206,208);font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, u5b8bu4f53;color:rgb(129,129,129);font-size:16.36px;">
重新配置hdfs-site.xml和core.xml，然后重新将配置文件发布到所有节点和客户端，再进行测试。</div>
<br><div style="line-height:28.18px;background-color:rgb(204,206,208);font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, u5b8bu4f53;color:rgb(129,129,129);font-size:16.36px;">
[hadoop@product201 hadoop]$ vi hdfs-site.xml</div>
<br><div style="line-height:28.18px;background-color:rgb(204,206,208);font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, u5b8bu4f53;color:rgb(129,129,129);font-size:16.36px;">
[hadoop@product201 hadoop]$ cat hdfs-site.xml</div>
<br><div style="line-height:28.18px;background-color:rgb(204,206,208);font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, u5b8bu4f53;color:rgb(129,129,129);font-size:16.36px;">
<pre class="prettyprint" style="border-bottom:rgb(136,136,136) 1px solid;border-left-color:rgb(136,136,136);border-top:rgb(136,136,136) 1px solid;border-left-width:1px;border-right:rgb(136,136,136) 1px solid;"></pre><div style="line-height:28px;font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, '宋体';"><span class="tag" style="line-height:28.18px;color:rgb(0,0,136);">&lt;property&gt;</span></div><div style="line-height:28px;font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, '宋体';"><span class="pln" style="line-height:28.18px;color:rgb(0,0,0);">  </span><span class="tag" style="line-height:28.18px;color:rgb(0,0,136);">&lt;name&gt;</span><span class="pln" style="line-height:28.18px;color:rgb(0,0,0);">dfs.client.failover.proxy.provider.cluster1</span><span class="tag" style="line-height:28.18px;color:rgb(0,0,136);">&lt;/name&gt;</span></div><div style="line-height:28px;font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, '宋体';"><span class="pln" style="line-height:28.18px;color:rgb(0,0,0);">  </span><span class="tag" style="line-height:28.18px;color:rgb(0,0,136);">&lt;value&gt;</span><span class="pln" style="line-height:28.18px;color:rgb(0,0,0);">org.apache.hadoop.hdfs.server.namenode.ha.ConfiguredFailoverProxyProvider</span><span class="tag" style="line-height:28.18px;color:rgb(0,0,136);">&lt;/value&gt;</span></div><div style="line-height:28px;font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, '宋体';"><span class="tag" style="line-height:28.18px;color:rgb(0,0,136);">&lt;/property&gt;</span></div>
</div>
<br><div style="line-height:28.18px;background-color:rgb(204,206,208);font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, u5b8bu4f53;color:rgb(129,129,129);font-size:16.36px;">
<span style="line-height:28px;">[hadoop@product201 hadoop]$ vi core-site.xml</span></div>
<br><div style="line-height:28.18px;background-color:rgb(204,206,208);font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, u5b8bu4f53;color:rgb(129,129,129);font-size:16.36px;">
[hadoop@product201 hadoop]$ cat core-site.xml</div>
<br><div style="line-height:28.18px;background-color:rgb(204,206,208);font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, u5b8bu4f53;color:rgb(129,129,129);font-size:16.36px;">
<pre class="prettyprint" style="border-bottom:rgb(136,136,136) 1px solid;border-left-color:rgb(136,136,136);border-top:rgb(136,136,136) 1px solid;border-left-width:1px;border-right:rgb(136,136,136) 1px solid;"></pre><div style="line-height:28px;font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, '宋体';"><span class="tag" style="line-height:28.18px;color:rgb(0,0,136);">&lt;property&gt;</span></div><div style="line-height:28px;font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, '宋体';"><span class="pln" style="line-height:28.18px;color:rgb(0,0,0);"> </span><span class="tag" style="line-height:28.18px;color:rgb(0,0,136);">&lt;name&gt;</span><span class="pln" style="line-height:28.18px;color:rgb(0,0,0);">fs.defaultFS</span><span class="tag" style="line-height:28.18px;color:rgb(0,0,136);">&lt;/name&gt;</span></div><div style="line-height:28px;font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, '宋体';"><span class="pln" style="line-height:28.18px;color:rgb(0,0,0);"> </span><span class="tag" style="line-height:28.18px;color:rgb(0,0,136);">&lt;value&gt;</span><span class="pln" style="line-height:28.18px;color:rgb(0,0,0);">hdfs://cluster1</span><span class="tag" style="line-height:28.18px;color:rgb(0,0,136);">&lt;/value&gt;</span></div><div style="line-height:28px;font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, '宋体';"><span class="tag" style="line-height:28.18px;color:rgb(0,0,136);">&lt;/property&gt;</span></div>
</div>
<br><div style="line-height:28.18px;background-color:rgb(204,206,208);font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, u5b8bu4f53;color:rgb(129,129,129);font-size:16.36px;">
<span style="color:#ff0000;">注意以上配置中cluster1是hdfs-site.xml中定义的[nameservice ID]</span>。</div>
<br><div style="line-height:28.18px;background-color:rgb(204,206,208);font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, u5b8bu4f53;color:rgb(129,129,129);font-size:16.36px;">
<br></div>
<br><div style="line-height:28.18px;background-color:rgb(204,206,208);font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, u5b8bu4f53;color:rgb(129,129,129);font-size:16.36px;">
重新启动hadoop集群</div>
<br><div style="line-height:28.18px;background-color:rgb(204,206,208);font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, u5b8bu4f53;color:rgb(129,129,129);font-size:16.36px;">
hadoop@wyy:/app/hadoop/hadoop220$ bin/hdfs dfs -ls /test</div>
<br><div style="line-height:28.18px;background-color:rgb(204,206,208);font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, u5b8bu4f53;color:rgb(129,129,129);font-size:16.36px;">
Found 1 items</div>
<br><div style="line-height:28.18px;background-color:rgb(204,206,208);font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, u5b8bu4f53;color:rgb(129,129,129);font-size:16.36px;">
-rw-r--r--   3 hadoop supergroup  258570165 2014-02-23 13:35 /test/2013.txt</div>
<br><div style="line-height:28.18px;background-color:rgb(204,206,208);font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, u5b8bu4f53;color:rgb(129,129,129);font-size:16.36px;">
<br></div>
<br><div style="line-height:28.18px;background-color:rgb(204,206,208);font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, u5b8bu4f53;color:rgb(129,129,129);font-size:16.36px;">
杀死product201 namenode进程，模拟HA故障转移：</div>
<br><div style="line-height:28.18px;background-color:rgb(204,206,208);font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, u5b8bu4f53;color:rgb(129,129,129);font-size:16.36px;">
[hadoop@product201 hadoop220]$ jps</div>
<br><div style="line-height:28.18px;background-color:rgb(204,206,208);font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, u5b8bu4f53;color:rgb(129,129,129);font-size:16.36px;">
1776 DFSZKFailoverController</div>
<br><div style="line-height:28.18px;background-color:rgb(204,206,208);font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, u5b8bu4f53;color:rgb(129,129,129);font-size:16.36px;">
1834 NameNode</div>
<br><div style="line-height:28.18px;background-color:rgb(204,206,208);font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, u5b8bu4f53;color:rgb(129,129,129);font-size:16.36px;">
1978 Jps</div>
<br><div style="line-height:28.18px;background-color:rgb(204,206,208);font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, u5b8bu4f53;color:rgb(129,129,129);font-size:16.36px;">
[hadoop@product201 hadoop220]$ kill 1834</div>
<br><div style="line-height:28.18px;background-color:rgb(204,206,208);font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, u5b8bu4f53;color:rgb(129,129,129);font-size:16.36px;">
<br></div>
<br><div style="line-height:28.18px;background-color:rgb(204,206,208);font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, u5b8bu4f53;color:rgb(129,129,129);font-size:16.36px;">
此时在客户端连结hadoop集群的时候还是能正常访问</div>
<br><div style="line-height:28.18px;background-color:rgb(204,206,208);font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, u5b8bu4f53;color:rgb(129,129,129);font-size:16.36px;">
hadoop@wyy:/app/hadoop/hadoop220$ bin/hdfs dfs -ls /test</div>
<br><div style="line-height:28.18px;background-color:rgb(204,206,208);font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, u5b8bu4f53;color:rgb(129,129,129);font-size:16.36px;">
Found 1 items</div>
<br><div style="line-height:28.18px;background-color:rgb(204,206,208);font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, u5b8bu4f53;color:rgb(129,129,129);font-size:16.36px;">
-rw-r--r--   3 hadoop supergroup  258570165 2014-02-23 13:35 /test/2013.txt</div>
<br><div style="line-height:28.18px;background-color:rgb(204,206,208);font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, u5b8bu4f53;color:rgb(129,129,129);font-size:16.36px;">
<br></div>
<br><div style="line-height:28.18px;background-color:rgb(204,206,208);font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, u5b8bu4f53;color:rgb(129,129,129);font-size:16.36px;">
<br></div>
<br><div style="line-height:28.18px;background-color:rgb(204,206,208);font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, u5b8bu4f53;color:rgb(129,129,129);font-size:16.36px;">
3：client如何访问HA HDFS + Federation</div>
</div>
            </div>
                </div>