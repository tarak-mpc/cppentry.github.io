---
layout:     post
title:      Flume把网络数据写入HDFS
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/caiandyong/article/details/44262591				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
1.创建agent配置文件<br>
把下列内容存入agent4.conf,并保存到Flume的工作目录/opt/flume/bin下面<br><br>
agent4.sources = netsource<br>
agent4.sinks = hdfssink<br>
agent4.channels = memorychannel<br><br>
agent4.sources.netsource.type = netcat<br>
agent4.sources.netsource.bind = localhost<br>
agent4.sources.netsource.port = 3000<br><br>
agent4.sinks.hdfssink.type = hdfs<br>
agent4.sinks.hdfssink.hdfs.path = /flume<br>
agent4.sinks.hdfssink.hdfs.filePrefix = log<br>
agent4.sinks.hdfssink..hdfs.rollInterval = 0<br>
agent4.sinks.hdfssink.hdfs.rollCount = 3<br>
agent4.sinks.hdfssink.hdfs.fileType = DataStream<br><br>
agent4.channels.memorychannel.type = memory<br>
agent4.channels.memorychannel.capacity = 1000<br>
agent4.channels.memorychannel.transactionCapacity = 100<br><br>
agent4.sources.netsource.channels = memorychannel<br>
agent4.sinks.hdfssink.channel = memorychannel<br><br>
配置说明:<span style="color:#CC0000;"><span style="background-color:rgb(255,255,255);">使用netcat信源和HDFS信宿，指定信宿文件存于HDFS的/flume目录，每一个文件都以log为前缀，每一个文件最多只能存储三条数据。</span></span><br><br>
2.启动Flume代理<br>
caiyong@caiyong:/opt/flume/bin$<span style="color:#3366FF;"> flume-ng agent --conf conf --conf-file agent4.conf --name agent4</span><br><br>
3.在另一个窗口中开启一个远程连接并发送几个事件<br><br>
caiyong@caiyong:~$ <span style="color:#3366FF;">telnet localhost 3000</span><br>
Trying 127.0.0.1...<br>
Connected to localhost.<br>
Escape character is '^]'.<br><span style="color:#3366FF;">write</span><br>
OK<br><span style="color:#3366FF;">data</span><br>
OK<br><span style="color:#3366FF;">based</span> <br>
OK<br><span style="color:#3366FF;">on</span><br>
OK<br><span style="color:#3366FF;">network</span><br>
OK<br><span style="color:#3366FF;">to</span><br>
OK<br><span style="color:#3366FF;">HDFS</span><br>
OK<br><br>
4.检查结果<br>
caiyong@caiyong:/opt/hadoop$ <span style="color:#3366FF;">bin/hadoop fs -ls /</span><br>
Found 7 items<br>
drwxr-xr-x   - caiyong supergroup          0 2015-03-14 14:46 <span style="color:#CC66CC;">
/flume</span><br>
drwxr-xr-x   - caiyong supergroup          0 2015-03-05 14:51 /hbase<br>
drwxr-xr-x   - caiyong supergroup          0 2015-03-14 13:07 /home<br>
drwxr-xr-x   - caiyong supergroup          0 2015-03-07 16:03 /pig<br>
drwxr-xr-x   - caiyong supergroup          0 2015-03-11 19:12 /testcopy<br>
drwxr-xr-x   - caiyong supergroup          0 2015-03-14 08:39 /tmp<br>
drwxr-xr-x   - caiyong supergroup          0 2015-03-11 19:04 /user<br>
caiyong@caiyong:/opt/hadoop$ <span style="color:#3366FF;">bin/hadoop fs -ls /flume/</span><br>
Found 3 items<br>
-rw-r--r--   1 caiyong supergroup         20 2015-03-14 14:45 <span style="color:#CC66CC;">
/flume/log.1426315528974</span><br>
-rw-r--r--   1 caiyong supergroup         17 2015-03-14 14:45 <span style="color:#CC66CC;">
/flume/log.1426315528975</span><br>
-rw-r--r--   1 caiyong supergroup          6 2015-03-14 14:46<span style="color:#CC66CC;"> /flume/log.1426315528976</span><br>
caiyong@caiyong:/opt/hadoop$ <span style="color:#3366FF;">bin/hadoop fs -cat /flume/*</span><br><span style="color:#CC66CC;">write<br>
data<br>
based<br>
on<br>
network<br>
to<br>
HDFS</span><br>
caiyong@caiyong:/opt/hadoop$ <span style="color:#3366FF;">bin/hadoop fs -cat /flume/log*4</span><br><span style="color:#CC66CC;">write<br>
data<br>
based</span><br>
caiyong@caiyong:/opt/hadoop$ <span style="color:#3366FF;">bin/hadoop fs -cat /flume/log*5</span><br><span style="color:#CC66CC;">on<br>
network<br>
to</span><br>
caiyong@caiyong:/opt/hadoop$ <span style="color:#3366FF;">bin/hadoop fs -cat /flume/log*6</span><br><span style="color:#CC66CC;">HDFS<br><br><br></span>
<p><span style="color:#CC66CC;"><br></span></p>
            </div>
                </div>