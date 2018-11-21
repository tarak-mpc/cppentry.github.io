---
layout:     post
title:      Flume内置channel,source,sink三组件介绍
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
Flume还是一个非常不错的日志收集系统的，其设计理念非常易用，简洁。并且是一个开源项目，基于Java语言开发，可以进行一些自定义的功能开发。运行Flume时，机器必须安装装JDK6.0以上的版本，并且，Flume目前只有Linux系统的启动脚本，没有Windows环境的启动脚本。  <br><div style="text-align:center;">       <img id="aimg_3583" src="http://www.aboutyun.com/data/attachment/forum/201404/17/024819j5n9opawy6cx5ryo.png" class="zoom" width="600" alt="">    </div>
</div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<br><span style="font-weight:700;"><span style="font-size:18px;">Flume主要由3个重要的组件购成：  </span></span></div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-weight:700;">Source</span>:完成对日志数据的收集，分成transtion 和 event 打入到channel之中。  <br><span style="font-weight:700;">Channel</span>:主要提供一个队列的功能，对source提供中的数据进行简单的缓存。  <br><span style="font-weight:700;">Sink</span>:取出Channel中的数据，进行相应的存储文件系统，数据库，或者提交到远程服务器。  </div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<br>
对现有程序改动最小的使用方式是使用是直接读取程序原来记录的日志文件，基本可以实现无缝接入，不需要对现有程序进行任何改动。  <br></div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-weight:700;">对于直接读取文件Source,有两种方式：  </span></div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-weight:700;">ExecSource</span>:以运行Linux命令的方式，持续的输出最新的数据，如tail -F 文件名指令，在这种方式下，取的文件名必须是指定的。  <br><span style="font-weight:700;">SpoolSource</span>:是监测配置的目录下新增的文件，并将文件中的数据读取出来。</div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
需要注意两点：</div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
1、拷贝到spool目录下的文件不可以再打开编辑。</div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
2、spool目录下不可包含相应的子目录。</div>
<br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
在实际使用的过程中，可以结合log4j使用，使用log4j的时候，将log4j的文件分割机制设为1分钟一次，将文件拷贝到spool的监控目录。log4j有一个TimeRolling的插件，可以把log4j分割的文件到spool目录。基本实现了实时的监控。</div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
Flume在传完文件之后，将会修改文件的后缀，变为.COMPLETED（后缀也可以在配置文件中灵活指定）  </div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-weight:700;">ExecSource，SpoolSource对比：</span></div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
ExecSource可以实现对日志的实时收集，但是存在Flume不运行或者指令执行出错时，将无法收集到日志数据，无法何证日志数据的完整性。SpoolSource虽然无法实现实时的收集数据，但是可以使用以分钟的方式分割文件，趋近于实时。如果应用无法实现以分钟切割日志文件的话，可以两种收集方式结合使用。</div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<br><span style="font-weight:700;">Channel有多种方式：</span></div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
有MemoryChannel,JDBC Channel,MemoryRecoverChannel,FileChannel。MemoryChannel可以实现高速的吞吐，但是无法保证数据的完整性。MemoryRecoverChannel在官方文档的建议上已经建义使用FileChannel来替换。FileChannel保证数据的完整性与一致性。在具体配置不现的FileChannel时，建议FileChannel设置的目录和程序日志文件保存的目录设成不同的磁盘，以便提高效率。  </div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<br><span style="font-weight:700;">Sink在设置存储数据时</span>，可以向文件系统中，数据库中，hadoop中储数据，在日志数据较少时，可以将数据存储在文件系中，并且设定一定的时间间隔保存数据。在日志数据较多时，可以将相应的日志数据存储到Hadoop中，便于日后进行相应的数据分析。</div>
            </div>
                </div>