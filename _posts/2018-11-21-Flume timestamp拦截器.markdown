---
layout:     post
title:      Flume timestamp拦截器
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/caiandyong/article/details/44262649				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h2>Flume拦截器简介</h2>
Flume拦截器就是一个插件，可以在从信源到信宿传输事件的过程中操作和修改事件。<span style="color:#FF0000;">大多数拦截器要么在事件的基础上加上一些元数据要么基于某些规则删除一些事件。</span><br><br><h2>Flume拦截器例子</h2>
1.创建agent配置文件<br>
把下列内容存入agent5.conf,并保存到Flume的工作目录/opt/flume/bin下面<br><br>
agent5.sources = netsource<br>
agent5.sinks = hdfssink<br>
agent5.channels = memorychannel<br><br>
agent5.sources.netsource.type = netcat<br>
agent5.sources.netsource.bind = localhost<br>
agent5.sources.netsource.port = 3000<br>
agent5.sources.netsource.interceptors = ts<br><br>
agent5.sources.netsource.interceptors.ts.type = org.apache.flume.interceptor.TimestampInterceptor$Builder<br><br>
agent5.sinks.hdfssink.type = hdfs<br>
agent5.sinks.hdfssink.hdfs.path = /flume/ts-%Y-%m-%d<br>
agent5.sinks.hdfssink.hdfs.filePrefix = log-ts-<br>
agent5.sinks.hdfssink.hdfs.rollInterval = 0<br>
agent5.sinks.hdfssink.hdfs.rollCount = 5<br>
agent5.sinks.hdfssink.hdfs.fileType = DataStream<br><br>
agent5.channels.memorychannel.type = memory<br>
agent5.channels.memorychannel.capacity = 1000<br>
agent5.channels.memorychannel.transactionCapacity = 100<br><br>
agent5.sources.netsource.channels = memorychannel<br>
agent5.sinks.hdfssink.channel = memorychannel<br><br>
2.启动Flume代理<br>
caiyong@caiyong:/opt/flume/bin$<span style="color:#3366FF;"> flume-ng agent --conf conf --conf-file agent5.conf --name agent5</span><br><br>
3.在另一个窗口中开启一个远程连接并发送几个事件<br>
说明：在Flume里，<span style="color:#FF0000;">事件就相当于一行接一行的文本</span><br>
caiyong@caiyong:~$<span style="color:#3366FF;"> telnet localhost 3000</span><br>
Trying 127.0.0.1...<br>
Connected to localhost.<br>
Escape character is '^]'.<br><span style="color:#3366FF;">write to HDFS</span><br>
OK<br><span style="color:#3366FF;">add</span><br>
OK<br><span style="color:#3366FF;">timestamp</span><br>
OK<br><span style="color:#3366FF;">interceptor</span><br>
OK<br><span style="color:#3366FF;">sorry</span><br>
OK<br><span style="color:#3366FF;">bye</span><br>
OK<br><span style="color:#3366FF;">bye</span><br>
OK<br><br><br>
4.检查结果<br>
caiyong@caiyong:/opt/hadoop$ <span style="color:#3366FF;">bin/hadoop  fs -ls /flume/</span><br>
Found 4 items<br>
-rw-r--r--   1 caiyong supergroup         20 2015-03-14 14:45 /flume/log.1426315528974<br>
-rw-r--r--   1 caiyong supergroup         17 2015-03-14 14:45 /flume/log.1426315528975<br>
-rw-r--r--   1 caiyong supergroup          6 2015-03-14 14:46 /flume/log.1426315528976<br>
drwxr-xr-x   - caiyong supergroup          0 2015-03-14 15:12 <span style="color:#CC66CC;">
/flume/ts-2015-03-14</span><br>
caiyong@caiyong:/opt/hadoop$ <span style="color:#3366FF;">bin/hadoop  fs -ls /flume/ts*</span><br>
Found 2 items<br>
-rw-r--r--   1 caiyong supergroup         51 2015-03-14 15:12<span style="color:#CC66CC;"> /flume/ts-2015-03-14/log-ts-.1426317131125</span><br>
-rw-r--r--   1 caiyong supergroup          0 2015-03-14 15:12<span style="color:#CC66CC;"> /flume/ts-2015-03-14/log-ts-.1426317167866.tmp</span><br><br>
caiyong@caiyong:/opt/hadoop$ <span style="color:#3366FF;">bin/hadoop  fs -cat /flume/ts*/log*25</span><br><span style="color:#CC66CC;">write to HDFS<br>
add<br>
timestamp<br>
interceptor<br>
sorry</span><br>
caiyong@caiyong:/opt/hadoop$ <span style="color:#3366FF;">bin/hadoop  fs -cat /flume/ts*/log*.tmp</span><br><span style="color:#CC66CC;">bye<br>
bye</span><br><br><br><span style="color:#FF0000;">Flume用.tmp后缀标记正在写入的文件，这样就很好区分完整文件和正在写入的文件。MR作业只会处理完整的文件。<br></span>
<p><br></p>
<p><br></p>
<p><br></p>
            </div>
                </div>