---
layout:     post
title:      Flume学习笔记 --- Flume内置source,channel, sink介绍
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：学习交流为主，未经博主同意禁止转载，禁止用于商用。					https://blog.csdn.net/u012965373/article/details/54377419				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;"><span style="font-size:18px;">Flume主要由3个重要的组件构成：<br><br></span></span></p>
<p><span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;"><span style="font-size:18px;">DATA MODEL（DM）：</span></span></p>
<p><span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;"><span style="font-size:18px;"><br></span></span></p>
<p><span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;"><span style="font-size:18px;"><img src="https://img-blog.csdn.net/20170112140938392?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMjk2NTM3Mw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></span></p>
<p><span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;"><span style="font-size:18px;"><br></span></span></p>
<p><span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;"><span style="font-size:18px;">Source:完成对日志数据的收集，分成transtion 和 event 打入到channel之中。  <br>
Channel:主要提供一个队列的功能，对source提供中的数据进行简单的缓存。  <br>
Sink:取出Channel中的数据，进行相应的存储文件系统，数据库，或者提交到远程服务器。  <br><br><br><br>
对现有程序改动最小的使用方式是使用是直接读取程序原来记录的日志文件，基本可以实现无缝接入，不需要对现有程序进行任何改动。  <br>
对于直接读取文件Source,有两种方式：  <br>
ExecSource:以运行Linux命令的方式，持续的输出最新的数据，如tail -F 文件名指令，在这种方式下，取的文件名必须是指定的。  <br>
SpoolSource:是监测配置的目录下新增的文件，并将文件中的数据读取出来。<br>
需要注意两点：<br>
1、拷贝到spool目录下的文件不可以再打开编辑。<br>
2、spool目录下不可包含相应的子目录。<br><br><br>
在实际使用的过程中，可以结合log4j使用，使用log4j的时候，将log4j的文件分割机制设为1分钟一次，将文件拷贝到spool的监控目录。log4j有一个TimeRolling的插件，可以把log4j分割的文件到spool目录。基本实现了实时的监控。<br>
Flume在传完文件之后，将会修改文件的后缀，变为.COMPLETED（后缀也可以在配置文件中灵活指定）  <br>
ExecSource，SpoolSource对比：<br>
ExecSource可以实现对日志的实时收集，但是存在Flume不运行或者指令执行出错时，将无法收集到日志数据，无法何证日志数据的完整性。SpoolSource虽然无法实现实时的收集数据，但是可以使用以分钟的方式分割文件，趋近于实时。如果应用无法实现以分钟切割日志文件的话，可以两种收集方式结合使用。<br><br><br>
Channel有多种方式：<br>
有MemoryChannel,JDBC Channel,MemoryRecoverChannel,FileChannel。MemoryChannel可以实现高速的吞吐，但是无法保证数据的完整性。MemoryRecoverChannel在官方文档的建议上已经建义使用FileChannel来替换。FileChannel保证数据的完整性与一致性。在具体配置不现的FileChannel时，建议FileChannel设置的目录和程序日志文件保存的目录设成不同的磁盘，以便提高效率。  <br><br><br>
Sink在设置存储数据时，可以向文件系统中，数据库中，hadoop中储数据，在日志数据较少时，可以将数据存储在文件系中，并且设定一定的时间间隔保存数据。在日志数据较多时，可以将相应的日志数据存储到Hadoop中，便于日后进行相应的数据分析。<br></span></span></p>
            </div>
                </div>