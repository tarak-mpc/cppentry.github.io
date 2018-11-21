---
layout:     post
title:      Flume教程(一) Flume入门教程
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/yuan_xw/article/details/51143698				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div class="WordSection1">
<p class="MsoNormalIndent"><span lang="en-us" xml:lang="en-us"> </span></p>
<p class="MsoNormalIndent"><span lang="en-us" xml:lang="en-us"><br></span></p>
<p class="a7" align="center"><span lang="en-us" style="font-size:16pt;line-height:150%;" xml:lang="en-us">Flume</span><span style="font-family:'楷体_GB2312';font-size:16pt;line-height:150%;">教程</span><span lang="en-us" style="font-size:16pt;line-height:150%;" xml:lang="en-us">(</span><span style="font-family:'楷体_GB2312';font-size:16pt;line-height:150%;">一</span><span lang="en-us" style="font-size:16pt;line-height:150%;" xml:lang="en-us">)
 Flume</span><span style="font-family:'楷体_GB2312';font-size:16pt;line-height:150%;">入门教程</span></p>
<p class="MsoNormalIndent"><span lang="en-us" xml:lang="en-us"> </span></p>
<span class="MsoHyperlink"><span lang="en-us" xml:lang="en-us"><a href="#_Toc448314910" rel="nofollow"><span lang="en-us" style="font-family:'宋体';" xml:lang="en-us"><span lang="en-us" xml:lang="en-us"><span lang="en-us" xml:lang="en-us"></span></span></span></a></span></span></div>
<div class="WordSection2">
<p class="MsoNormalIndent"></p>
</div>
<span lang="en-us" style="font-family:'Times New Roman', serif;font-size:10.5pt;line-height:150%;" xml:lang="en-us"><br clear="all"></span>
<div class="WordSection3">
<h1><span lang="en-us" style="font-size:16pt;line-height:150%;" xml:lang="en-us">1 </span><span lang="en-us" style="font-size:16pt;line-height:150%;" xml:lang="en-us">Flume</span><span style="font-family:'宋体';font-size:16pt;line-height:150%;">入门教程</span></h1>
<p class="MsoNormalIndent" align="left" style="text-align:left;text-indent:24pt;">
<span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">Flume</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">是</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">Cloudera</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">提供的一个高可用的，高可靠的，分布式的海量日志采集、聚合和传输的系统，</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">Flume</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">支持在日志系统中定制各类数据发送方，用于收集数据；同时，</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">Flume</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">提供对数据进行简单处理，并写到各种数据接受方（可定制）的能力。</span></p>
<p class="MsoNormalIndent" align="left" style="text-align:left;text-indent:24pt;">
<span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">当前</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">Flume</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">有两个版本</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">Flume
 0.9X</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">版本的统称</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">Flume-og</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">，</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">Flume1.X</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">版本的统称</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">Flume-ng</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">。由于</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">Flume-ng</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">经过重大重构，与</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">Flume-og</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">有很大不同，使用时请注意区分。</span></p>
<p class="MsoNormalIndent" align="left" style="text-align:left;"><strong><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">Flume</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">运行环境</span></strong><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">：机器必须安装</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">JDK6.0</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">以上的版本，并且</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">Flume</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">目前只有</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">Linux</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">系统的启动脚本，没有</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">Windows</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">环境的启动脚本。</span></p>
<p class="MsoNormalIndent" align="left" style="text-align:left;"><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us"> </span></p>
<h2><span lang="en-us" xml:lang="en-us">1.1 </span><span style="font-family:'宋体';">体系架构</span></h2>
<p class="MsoNormalIndent" align="left" style="text-align:left;text-indent:24pt;">
<span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">Flume</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">的数据流由事件</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">(Event)</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">贯穿始终。事件是</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">Flume</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">的基本数据单位，它携带日志数据</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">(</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">字节数组形式</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">)</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">并且携带有头信息，这些</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">Event</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">由</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">Agent</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">外部的</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">Source</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">生成，当</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">Source</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">捕获事件后会进行特定的格式化，然后</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">Source</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">会把事件推入</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">(</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">单个或多个</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">)Channel</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">中。你可以把</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">Channel</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">看作是一个缓冲区，它将保存事件直到</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">Sink</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">处理完该事件。</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">Sink</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">负责持久化日志或者把事件推向另一个</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">Source</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">。</span></p>
<p class="MsoNormalIndent" align="left" style="text-align:left;text-indent:24pt;">
<span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us"> </span></p>
<p class="MsoNormalIndent" align="left" style="text-align:left;text-indent:24pt;">
<span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">Flume</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">以</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">agent</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">为最小的独立运行单位。一个</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">agent</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">就是一个</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">JVM</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">。单</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">agent</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">由</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">Source</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">、</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">Sink</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">和</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">Channel</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">三大组件构成，如下图：</span></p>
<p class="MsoNormalIndent" align="left" style="text-align:left;text-indent:24pt;">
<span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us"> </span></p>
<p class="MsoNormalIndent" align="left" style="text-align:left;"><span lang="en-us" xml:lang="en-us"><img src="https://img-blog.csdn.net/20160413151736957?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></p>
<p class="MsoNormalIndent" align="center" style="text-align:left;"><em><span style="font-family:'宋体';">图</span><span lang="en-us" xml:lang="en-us">1-1
</span><span style="font-family:'宋体';">数据流模型</span></em></p>
<p class="MsoNormalIndent" align="left" style="text-align:left;"><em><span lang="en-us" xml:lang="en-us"> </span></em></p>
<table class="MsoTableGrid" border="1" cellpadding="0" cellspacing="0" width="756" style="border-collapse:collapse;border:none;"><tbody><tr><td valign="top" width="76" style="width:56.75pt;border:solid #000000 1pt;">
<p class="MsoNormal" align="left" style="text-align:left;line-height:normal;">
<span style="font-family:'宋体';color:#000000;font-size:12pt;">组件</span></p>
</td>
<td valign="top" width="680" style="width:18cm;border:solid #000000 1pt;border-left:none;">
<p class="MsoNormal" align="left" style="text-align:left;line-height:normal;">
<span style="font-family:'宋体';color:#000000;font-size:12pt;">功能</span></p>
</td>
</tr><tr><td valign="top" width="76" style="width:56.75pt;border:solid #000000 1pt;border-top:none;">
<p class="MsoNormal"><span lang="en-us" style="font-family:Consolas;line-height:150%;" xml:lang="en-us">Agent</span></p>
</td>
<td valign="top" width="680" style="width:18cm;border-top:none;border-left:none;border-bottom:solid #000000 1pt;border-right:solid #000000 1pt;">
<p class="MsoNormal"><span style="font-family:'宋体';line-height:150%;">使用</span><span lang="en-us" style="font-family:Consolas;line-height:150%;" xml:lang="en-us">JVM</span><span lang="en-us" style="line-height:150%;" xml:lang="en-us"></span><span style="font-family:'宋体';line-height:150%;">运行</span><span lang="en-us" style="font-family:Consolas;line-height:150%;" xml:lang="en-us">Flume</span><span style="font-family:'宋体';line-height:150%;">。每台机器运行一个</span><span lang="en-us" style="font-family:Consolas;line-height:150%;" xml:lang="en-us">agent</span><span style="font-family:'宋体';line-height:150%;">，但是可以在一个</span><span lang="en-us" style="font-family:Consolas;line-height:150%;" xml:lang="en-us">agent</span><span style="font-family:'宋体';line-height:150%;">中包含多个</span><span lang="en-us" style="font-family:Consolas;line-height:150%;" xml:lang="en-us">sources</span><span style="font-family:'宋体';line-height:150%;">和</span><span lang="en-us" style="font-family:Consolas;line-height:150%;" xml:lang="en-us">sinks</span><span style="font-family:'宋体';line-height:150%;">。</span></p>
</td>
</tr><tr><td valign="top" width="76" style="width:56.75pt;border:solid #000000 1pt;border-top:none;">
<p class="MsoNormal"><span lang="en-us" style="font-family:Consolas;line-height:150%;" xml:lang="en-us">Client</span></p>
</td>
<td valign="top" width="680" style="width:18cm;border-top:none;border-left:none;border-bottom:solid #000000 1pt;border-right:solid #000000 1pt;">
<p class="MsoNormal"><span style="font-family:'宋体';line-height:150%;">生产数据，运行在一个独立的线程。</span></p>
</td>
</tr><tr><td valign="top" width="76" style="width:56.75pt;border:solid #000000 1pt;border-top:none;">
<p class="MsoNormal"><span lang="en-us" style="font-family:Consolas;line-height:150%;" xml:lang="en-us">Source</span></p>
</td>
<td valign="top" width="680" style="width:18cm;border-top:none;border-left:none;border-bottom:solid #000000 1pt;border-right:solid #000000 1pt;">
<p class="MsoNormal"><span style="font-family:'宋体';line-height:150%;">从</span><span lang="en-us" style="font-family:Consolas;line-height:150%;" xml:lang="en-us">Client</span><span style="font-family:'宋体';line-height:150%;">收集数据，传递给</span><span lang="en-us" style="font-family:Consolas;line-height:150%;" xml:lang="en-us">Channel</span><span style="font-family:'宋体';line-height:150%;">。</span></p>
</td>
</tr><tr><td valign="top" width="76" style="width:56.75pt;border:solid #000000 1pt;border-top:none;">
<p class="MsoNormal"><span lang="en-us" style="font-family:Consolas;line-height:150%;" xml:lang="en-us">Sink</span></p>
</td>
<td valign="top" width="680" style="width:18cm;border-top:none;border-left:none;border-bottom:solid #000000 1pt;border-right:solid #000000 1pt;">
<p class="MsoNormal"><span style="font-family:'宋体';line-height:150%;">从</span><span lang="en-us" style="line-height:150%;" xml:lang="en-us">Channel</span><span style="font-family:'宋体';line-height:150%;">收集数据，运行在一个独立线程。</span></p>
</td>
</tr><tr><td valign="top" width="76" style="width:56.75pt;border:solid #000000 1pt;border-top:none;">
<p class="MsoNormal"><span lang="en-us" style="font-family:Consolas;line-height:150%;" xml:lang="en-us">Channel</span></p>
</td>
<td valign="top" width="680" style="width:18cm;border-top:none;border-left:none;border-bottom:solid #000000 1pt;border-right:solid #000000 1pt;">
<p class="MsoNormal"><span style="font-family:'宋体';line-height:150%;">连接</span><span style="line-height:150%;"></span><span lang="en-us" style="font-family:Consolas;line-height:150%;" xml:lang="en-us">sources</span><span lang="en-us" style="line-height:150%;" xml:lang="en-us"></span><span style="font-family:'宋体';line-height:150%;">和</span><span style="line-height:150%;"></span><span lang="en-us" style="font-family:Consolas;line-height:150%;" xml:lang="en-us">sinks</span><span lang="en-us" style="line-height:150%;" xml:lang="en-us"></span><span style="font-family:'宋体';line-height:150%;">，这个有点像一个队列。</span></p>
</td>
</tr><tr><td valign="top" width="76" style="width:56.75pt;border:solid #000000 1pt;border-top:none;">
<p class="MsoNormal"><span lang="en-us" style="font-family:Consolas;line-height:150%;" xml:lang="en-us">Events</span></p>
</td>
<td valign="top" width="680" style="width:18cm;border-top:none;border-left:none;border-bottom:solid #000000 1pt;border-right:solid #000000 1pt;">
<p class="MsoNormal"><span style="font-family:'宋体';line-height:150%;">可以是日志记录、</span><span style="line-height:150%;"></span><span lang="en-us" style="font-family:Consolas;line-height:150%;" xml:lang="en-us">avro</span><span lang="en-us" style="line-height:150%;" xml:lang="en-us"></span><span style="font-family:'宋体';line-height:150%;">对象等。</span></p>
</td>
</tr></tbody></table><p class="MsoNormalIndent" align="left" style="text-align:left;"><em><span lang="en-us" xml:lang="en-us"> </span></em></p>
<h2><span lang="en-us" xml:lang="en-us">1.2 </span><span lang="en-us" xml:lang="en-us">Flume</span><span style="font-family:'宋体';">特点</span></h2>
<p class="MsoNormalIndent" align="left" style="text-align:left;"><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">FLUMOG</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">有三种角色的节点，如图</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">1-2</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">：代理节点（</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">agent</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">）、收集节点（</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">collector</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">）、主节点（</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">master</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">）。</span></p>
<p class="MsoNormalIndent"><span lang="en-us" xml:lang="en-us"> </span></p>
<p class="MsoNormalIndent" align="left" style="text-align:left;"><span lang="en-us" xml:lang="en-us"><img src="https://img-blog.csdn.net/20160413151811277?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></p>
<p class="MsoNormalIndent" align="left" style="text-align:left;"><em><span style="font-family:'宋体';">图</span><span lang="en-us" xml:lang="en-us"> 1-2 FLUM OG</span><span style="font-family:'宋体';">架构图</span></em></p>
<p class="MsoNormalIndent" align="center" style="text-align:center;"><span lang="en-us" xml:lang="en-us"> </span></p>
<p class="MsoNormalIndent" align="left" style="text-align:left;"><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">agent</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">从各个数据源收集日志数据，将收集到的数据集中到</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">
 collector</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">，然后由收集节点汇总存入</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us"> hdfs</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">。</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">master</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">负责管理</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">
 agent</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">，</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">collector</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">的活动。</span></p>
<p class="MsoNormalIndent" align="left" style="text-align:left;"><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us"> </span></p>
<p class="MsoNormalIndent" align="left" style="text-align:left;text-indent:0cm;">
<span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">  </span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">在</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">Flume</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">中，最重要的抽象是</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">data
 flow</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">（数据流），</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">data flow</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">描述了数据从产生，传输、处理并最终写入目标的一条路径。</span></p>
<p class="MsoNormalIndent" align="left" style="text-align:left;text-indent:0cm;">
<span lang="en-us" xml:lang="en-us"><img src="https://img-blog.csdn.net/20160413151831411?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></p>
<p class="MsoNormalIndent" align="left" style="text-align:left;text-indent:24pt;">
<span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">对于</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">agent</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">数据流配置就是从哪得到数据，把数据发送到哪个</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">collector</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">。</span></p>
<p class="MsoNormalIndent" align="left" style="text-align:left;text-indent:24pt;">
<span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">对于</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">collector</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">是接收</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">agent</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">发过来的数据，把数据发送到指定的目标机器上。</span></p>
<p class="MsoNormalIndent" align="left" style="text-align:left;text-indent:24pt;">
<span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">Flume</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">框架对</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">hadoop</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">和</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">zookeeper</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">的依赖只是在</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">jar</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">包上，并不要求</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">flume</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">启动时必须将</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">hadoop</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">和</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">zookeeper</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">服务也启动。</span></p>
<p class="MsoNormalIndent" align="left" style="text-align:left;text-indent:24pt;">
<span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us"> </span></p>
<h2><span lang="en-us" xml:lang="en-us">1.3 </span><span lang="en-us" xml:lang="en-us">Flume</span><span style="font-family:'宋体';">核心组件</span></h2>
<p class="MsoNormal" align="left" style="margin-left:38.55pt;text-align:left;text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;" xml:lang="en-us">1、<span style="font:7pt 'Times New Roman';"></span></span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;" xml:lang="en-us">Flume</span><span style="font-family:'宋体';color:#000000;font-size:12pt;">核心组件</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;" xml:lang="en-us">-<strong>Source</strong></span><span style="font-family:'宋体';color:#000000;font-size:12pt;">：</span></p>
<p class="MsoListParagraph" style="margin-left:84pt;text-indent:-21pt;"><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">1.<span style="font:7pt 'Times New Roman';"> </span></span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">FlumeSource</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">：完成对日志的收集，分成</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">transtion</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">和</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">event</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">打入到</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">channel</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">之中。</span></p>
<p class="MsoListParagraph" style="margin-left:84pt;text-indent:-21pt;"><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">2.<span style="font:7pt 'Times New Roman';"> </span></span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">Flume</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">提供了各种</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">source</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">的实现，包括：</span></p>
<p class="MsoListParagraph" style="margin-left:84pt;text-indent:0cm;"><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">AvroSource</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">、</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">ExceSource</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">、</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">SpoolingDirectorySource</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">、</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">NetCatSource</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">、</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">SyslogSource</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">、</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">SyslogTCPSource</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">、</span></p>
<p class="MsoListParagraph" style="margin-left:84pt;text-indent:0cm;"><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">SyslogUDPSource</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">、</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">HTTPSource</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">、</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">HDFSSource</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">、</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">etc</span></p>
<p class="MsoListParagraph" style="margin-left:84pt;text-indent:-21pt;"><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">3.<span style="font:7pt 'Times New Roman';"> </span></span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">Flume</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">自带了很多直接可用的数据源（</span><strong><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">source</span></strong><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">），如下表：</span></p>
<p class="MsoListParagraph" style="margin-left:63pt;text-indent:0cm;"><strong><span lang="en-us" xml:lang="en-us">1.</span><span style="font-family:'宋体';">》</span><span lang="en-us" xml:lang="en-us">Flume’s Tiered Event Sources</span><span style="font-family:'宋体';">：</span></strong></p>
<table class="MsoTableGrid" border="1" cellpadding="0" cellspacing="0" style="margin-left:63pt;border-collapse:collapse;border:none;"><tbody><tr><td valign="top" width="369" style="width:276.8pt;border:solid #000000 1pt;">
<p><span lang="en-us" xml:lang="en-us">collectorSource[(<em>port</em>)]</span></p>
</td>
<td valign="top" width="406" style="width:304.8pt;border:solid #000000 1pt;border-left:none;">
<p><span lang="en-us" xml:lang="en-us">Collector source</span>，监听端口汇聚数据</p>
</td>
</tr><tr><td valign="top" width="369" style="width:276.8pt;border:solid #000000 1pt;border-top:none;">
<p><span lang="en-us" xml:lang="en-us">autoCollectorSource</span></p>
</td>
<td valign="top" width="406" style="width:304.8pt;border-top:none;border-left:none;border-bottom:solid #000000 1pt;border-right:solid #000000 1pt;">
<p>通过<span lang="en-us" xml:lang="en-us">master</span>协调物理节点自动汇聚数据</p>
</td>
</tr><tr><td valign="top" width="369" style="width:276.8pt;border:solid #000000 1pt;border-top:none;">
<p><span lang="en-us" xml:lang="en-us">logicalSource</span></p>
</td>
<td valign="top" width="406" style="width:304.8pt;border-top:none;border-left:none;border-bottom:solid #000000 1pt;border-right:solid #000000 1pt;">
<p>逻辑<span lang="en-us" xml:lang="en-us">source</span>，由<span lang="en-us" xml:lang="en-us">master</span>分配端口并监听<span lang="en-us" xml:lang="en-us">rpcSink</span></p>
</td>
</tr></tbody></table><p class="MsoNormal" align="left" style="margin-left:63pt;text-align:left;line-height:normal;">
<span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;" xml:lang="en-us"> </span></p>
<p class="MsoNormal" align="left" style="margin-left:63pt;text-align:left;line-height:normal;">
<span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;" xml:lang="en-us"> </span></p>
<p class="MsoNormal" align="left" style="margin-left:63pt;text-align:left;line-height:normal;">
<strong><span lang="en-us" xml:lang="en-us">2.</span><span style="font-family:'宋体';">》</span><span lang="en-us" xml:lang="en-us">Flume’s Basic Sources</span><span style="font-family:'宋体';">：</span></strong></p>
<table class="MsoTableGrid" border="1" cellpadding="0" cellspacing="0" style="margin-left:63pt;border-collapse:collapse;border:none;"><tbody><tr><td valign="top" width="366" style="width:274.8pt;border:solid #000000 1pt;">
<p><span lang="en-us" xml:lang="en-us">null</span></p>
</td>
<td valign="top" width="407" style="width:305.5pt;border:solid #000000 1pt;border-left:none;">
<p class="MsoNormal"><span lang="en-us" xml:lang="en-us"> </span></p>
</td>
</tr><tr><td valign="top" width="366" style="width:274.8pt;border:solid #000000 1pt;border-top:none;">
<p><span lang="en-us" xml:lang="en-us">console</span></p>
</td>
<td valign="top" width="407" style="width:305.5pt;border-top:none;border-left:none;border-bottom:solid #000000 1pt;border-right:solid #000000 1pt;">
<p>监听用户编辑历史和快捷键输入，只在<span lang="en-us" xml:lang="en-us">node_nowatch</span>模式下可用</p>
</td>
</tr><tr><td valign="top" width="366" style="width:274.8pt;border:solid #000000 1pt;border-top:none;">
<p><span lang="en-us" xml:lang="en-us">stdin</span></p>
</td>
<td valign="top" width="407" style="width:305.5pt;border-top:none;border-left:none;border-bottom:solid #000000 1pt;border-right:solid #000000 1pt;">
<p>监听标准输入，只在<span lang="en-us" xml:lang="en-us">node_nowatch</span>模式下可用，每行将作为一个<span lang="en-us" xml:lang="en-us">event source</span></p>
</td>
</tr><tr><td valign="top" width="366" style="width:274.8pt;border:solid #000000 1pt;border-top:none;">
<p><span lang="en-us" xml:lang="en-us">rpcSource(<em>port</em>)</span></p>
</td>
<td valign="top" width="407" style="width:305.5pt;border-top:none;border-left:none;border-bottom:solid #000000 1pt;border-right:solid #000000 1pt;">
<p>由<span lang="en-us" xml:lang="en-us">rpc</span>框架<span lang="en-us" xml:lang="en-us">(thrift/avro)</span>监听<span lang="en-us" xml:lang="en-us">tcp</span>端口</p>
</td>
</tr><tr><td valign="top" width="366" style="width:274.8pt;border:solid #000000 1pt;border-top:none;">
<p><span lang="en-us" xml:lang="en-us">text("<em>filename</em>")</span></p>
</td>
<td valign="top" width="407" style="width:305.5pt;border-top:none;border-left:none;border-bottom:solid #000000 1pt;border-right:solid #000000 1pt;">
<p>一次性读取一个文本，每行为一个<span lang="en-us" xml:lang="en-us">event</span></p>
</td>
</tr><tr><td valign="top" width="366" style="width:274.8pt;border:solid #000000 1pt;border-top:none;">
<p><span lang="en-us" xml:lang="en-us">tail("<em>filename</em>"[, <em>startFromEnd</em>=false])</span></p>
</td>
<td valign="top" width="407" style="width:305.5pt;border-top:none;border-left:none;border-bottom:solid #000000 1pt;border-right:solid #000000 1pt;">
<p>每行为一个<span lang="en-us" xml:lang="en-us">event</span>。监听文件尾部的追加行，如果<span lang="en-us" xml:lang="en-us">startFromEnd</span>为<span lang="en-us" xml:lang="en-us">true</span>，<span lang="en-us" xml:lang="en-us">tail</span>将从文件尾读取，如果为<span lang="en-us" xml:lang="en-us">false</span>，<span lang="en-us" xml:lang="en-us">tail</span>将从文件开始读取全部数据</p>
</td>
</tr><tr><td valign="top" width="366" style="width:274.8pt;border:solid #000000 1pt;border-top:none;">
<p><span lang="en-us" xml:lang="en-us">multitail("<em>filename</em>"[, <em>file2</em> [,<em>file3</em>… ] ])</span></p>
</td>
<td valign="top" width="407" style="width:305.5pt;border-top:none;border-left:none;border-bottom:solid #000000 1pt;border-right:solid #000000 1pt;">
<p>同上，同时监听多个文件的末尾</p>
</td>
</tr><tr><td valign="top" width="366" style="width:274.8pt;border:solid #000000 1pt;border-top:none;">
<p><span lang="en-us" xml:lang="en-us">tailDir("<em>dirname</em>"[, fileregex=".*"[, startFromEnd=false[, recurseDepth=0]]])</span></p>
</td>
<td valign="top" width="407" style="width:305.5pt;border-top:none;border-left:none;border-bottom:solid #000000 1pt;border-right:solid #000000 1pt;">
<p>监听目录中的文件末尾，使用正则去选定需要监听的文件（不包含目录），<span lang="en-us" xml:lang="en-us">recurseDepth</span>为递归监听其下子目录的深度</p>
</td>
</tr><tr><td valign="top" width="366" style="width:274.8pt;border:solid #000000 1pt;border-top:none;">
<p><span lang="en-us" xml:lang="en-us">seqfile("<em>filename</em>")</span></p>
</td>
<td valign="top" width="407" style="width:305.5pt;border-top:none;border-left:none;border-bottom:solid #000000 1pt;border-right:solid #000000 1pt;">
<p>监听<span lang="en-us" xml:lang="en-us">hdfs</span>的<span lang="en-us" xml:lang="en-us">sequencefile</span>，全路径</p>
</td>
</tr><tr><td valign="top" width="366" style="width:274.8pt;border:solid #000000 1pt;border-top:none;">
<p><span lang="en-us" xml:lang="en-us">syslogUdp(<em>port</em>)</span></p>
</td>
<td valign="top" width="407" style="width:305.5pt;border-top:none;border-left:none;border-bottom:solid #000000 1pt;border-right:solid #000000 1pt;">
<p>监听<span lang="en-us" xml:lang="en-us">Udp</span>端口</p>
</td>
</tr><tr><td valign="top" width="366" style="width:274.8pt;border:solid #000000 1pt;border-top:none;">
<p><span lang="en-us" xml:lang="en-us">syslogTcp(<em>port</em>)</span></p>
</td>
<td valign="top" width="407" style="width:305.5pt;border-top:none;border-left:none;border-bottom:solid #000000 1pt;border-right:solid #000000 1pt;">
<p>监听<span lang="en-us" xml:lang="en-us">Tcp</span>端口</p>
</td>
</tr><tr><td valign="top" width="366" style="width:274.8pt;border:solid #000000 1pt;border-top:none;">
<p><span lang="en-us" xml:lang="en-us">syslogTcp1(<em>port</em>)</span></p>
</td>
<td valign="top" width="407" style="width:305.5pt;border-top:none;border-left:none;border-bottom:solid #000000 1pt;border-right:solid #000000 1pt;">
<p>只监听<span lang="en-us" xml:lang="en-us">Tcp</span>端口的一个链接</p>
</td>
</tr><tr><td valign="top" width="366" style="width:274.8pt;border:solid #000000 1pt;border-top:none;">
<p><span lang="en-us" xml:lang="en-us">execPeriodic("<em>cmdline</em>", <em>ms</em>)</span></p>
</td>
<td valign="top" width="407" style="width:305.5pt;border-top:none;border-left:none;border-bottom:solid #000000 1pt;border-right:solid #000000 1pt;">
<p>周期执行指令，监听指令的输出，整个输出都被作为一个<span lang="en-us" xml:lang="en-us">event</span></p>
</td>
</tr><tr><td valign="top" width="366" style="width:274.8pt;border:solid #000000 1pt;border-top:none;">
<p><span lang="en-us" xml:lang="en-us">execStream("<em>cmdline</em>")</span></p>
</td>
<td valign="top" width="407" style="width:305.5pt;border-top:none;border-left:none;border-bottom:solid #000000 1pt;border-right:solid #000000 1pt;">
<p>执行指令，监听指令的输出，输出的每一行被作为一个<span lang="en-us" xml:lang="en-us">event</span></p>
</td>
</tr><tr><td valign="top" width="366" style="width:274.8pt;border:solid #000000 1pt;border-top:none;">
<p><span lang="en-us" xml:lang="en-us">exec("<em>cmdline</em>"[, <em>aggregate</em>=false[,<em>restart</em>=false[,<em>period</em>=0]]])</span></p>
</td>
<td valign="top" width="407" style="width:305.5pt;border-top:none;border-left:none;border-bottom:solid #000000 1pt;border-right:solid #000000 1pt;">
<p>执行指令，监听指令的输出，<span lang="en-us" xml:lang="en-us">aggregate</span>如果为<span lang="en-us" xml:lang="en-us">true</span>，整个输出作为一个<span lang="en-us" xml:lang="en-us">event</span>如果为<span lang="en-us" xml:lang="en-us">false</span>，则每行作为一个<span lang="en-us" xml:lang="en-us">event</span>。如果<span lang="en-us" xml:lang="en-us">restart</span>为<span lang="en-us" xml:lang="en-us">true</span>，则按<span lang="en-us" xml:lang="en-us">period</span>为周期重新运行</p>
</td>
</tr><tr><td valign="top" width="366" style="width:274.8pt;border:solid #000000 1pt;border-top:none;">
<p><span lang="en-us" xml:lang="en-us">synth(<em>msgCount</em>,<em>msgSize</em>)</span></p>
</td>
<td valign="top" width="407" style="width:305.5pt;border-top:none;border-left:none;border-bottom:solid #000000 1pt;border-right:solid #000000 1pt;">
<p>随即产生字符串<span lang="en-us" xml:lang="en-us">event,msgCount</span>为产生数量，<span lang="en-us" xml:lang="en-us">msgSize</span>为串长度</p>
</td>
</tr><tr><td valign="top" width="366" style="width:274.8pt;border:solid #000000 1pt;border-top:none;">
<p><span lang="en-us" xml:lang="en-us">synthrndsize(<em>msgCount</em>,<em>minSize</em>,<em>maxSize</em>)</span></p>
</td>
<td valign="top" width="407" style="width:305.5pt;border-top:none;border-left:none;border-bottom:solid #000000 1pt;border-right:solid #000000 1pt;">
<p>同上，<span lang="en-us" xml:lang="en-us">minSize – maxSize</span></p>
</td>
</tr><tr><td valign="top" width="366" style="width:274.8pt;border:solid #000000 1pt;border-top:none;">
<p><span lang="en-us" xml:lang="en-us">nonlsynth(<em>msgCount</em>,<em>msgSize</em>)</span></p>
</td>
<td valign="top" width="407" style="width:305.5pt;border-top:none;border-left:none;border-bottom:solid #000000 1pt;border-right:solid #000000 1pt;">
<p class="MsoNormal"><span lang="en-us" xml:lang="en-us"> </span></p>
</td>
</tr><tr><td valign="top" width="366" style="width:274.8pt;border:solid #000000 1pt;border-top:none;">
<p><span lang="en-us" xml:lang="en-us">asciisynth(<em>msgCount</em>,<em>msgSize</em>)</span></p>
</td>
<td valign="top" width="407" style="width:305.5pt;border-top:none;border-left:none;border-bottom:solid #000000 1pt;border-right:solid #000000 1pt;">
<p><span lang="en-us" xml:lang="en-us">Ascii</span>码字符</p>
</td>
</tr><tr><td valign="top" width="366" style="width:274.8pt;border:solid #000000 1pt;border-top:none;">
<p><span lang="en-us" xml:lang="en-us">twitter("<em>username</em>","<em>pw</em>"[,"<em>url</em>"])</span></p>
</td>
<td valign="top" width="407" style="width:305.5pt;border-top:none;border-left:none;border-bottom:solid #000000 1pt;border-right:solid #000000 1pt;">
<p>尼玛<span lang="en-us" xml:lang="en-us">twitter</span>的插件啊</p>
</td>
</tr><tr><td valign="top" width="366" style="width:274.8pt;border:solid #000000 1pt;border-top:none;">
<p><span lang="en-us" xml:lang="en-us">irc("<em>server</em>",<em>port</em>, "<em>nick</em>","<em>chan</em>")</span></p>
</td>
<td valign="top" width="407" style="width:305.5pt;border-top:none;border-left:none;border-bottom:solid #000000 1pt;border-right:solid #000000 1pt;">
<p class="MsoNormal"><span lang="en-us" xml:lang="en-us"> </span></p>
</td>
</tr><tr><td valign="top" width="366" style="width:274.8pt;border:solid #000000 1pt;border-top:none;">
<p><span lang="en-us" xml:lang="en-us">scribe[(+<em>port</em>)]</span></p>
</td>
<td valign="top" width="407" style="width:305.5pt;border-top:none;border-left:none;border-bottom:solid #000000 1pt;border-right:solid #000000 1pt;">
<p><span lang="en-us" xml:lang="en-us">Scribe</span>插件</p>
</td>
</tr><tr><td valign="top" width="366" style="width:274.8pt;border:solid #000000 1pt;border-top:none;">
<p><span lang="en-us" xml:lang="en-us">report[(periodMillis)]</span></p>
</td>
<td valign="top" width="407" style="width:305.5pt;border-top:none;border-left:none;border-bottom:solid #000000 1pt;border-right:solid #000000 1pt;">
<p>生成所有<span lang="en-us" xml:lang="en-us">physical node</span>报告为事件源</p>
</td>
</tr></tbody></table><p class="MsoNormal" align="left" style="margin-left:63pt;text-align:left;line-height:normal;">
<strong><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;" xml:lang="en-us"> </span></strong></p>
<p class="MsoNormal" align="left" style="margin-left:38.55pt;text-align:left;line-height:normal;">
<span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;" xml:lang="en-us"></span></p>
<p class="MsoNormal" align="left" style="margin-left:38.55pt;text-align:left;line-height:normal;">
<span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;" xml:lang="en-us"> </span></p>
<p class="MsoNormal" align="left" style="margin-left:38.55pt;text-align:left;text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;" xml:lang="en-us">2、<span style="font:7pt 'Times New Roman';"></span></span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;" xml:lang="en-us">Flume</span><span style="font-family:'宋体';color:#000000;font-size:12pt;">核心组件</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;" xml:lang="en-us">-<strong>Sink</strong></span><span style="font-family:'宋体';color:#000000;font-size:12pt;">：</span></p>
<p class="MsoListParagraph" style="margin-left:84pt;text-indent:-21pt;"><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">1.<span style="font:7pt 'Times New Roman';"> </span></span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">Flume
 Sink</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">：取出</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">Channel</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">中的数据，进行相应的存储文件系统，数据库，或者提交到远程服务器。</span></p>
<p class="MsoListParagraph" style="margin-left:84pt;text-indent:-21pt;"><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">2.<span style="font:7pt 'Times New Roman';"> </span></span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">Flume</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">提供了各种</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">sink</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">的实现，包括：</span></p>
<p class="MsoListParagraph" style="margin-left:84pt;text-indent:0cm;"><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">HDFSsink</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">、</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">Loggersink</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">、</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">Avrosink</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">、</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">FileRollsink</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">、</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">Nullsink</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">、</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">Hbasesink</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">、</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">etc</span></p>
<p class="MsoListParagraph" style="margin-left:84pt;text-indent:-21pt;"><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">3.<span style="font:7pt 'Times New Roman';"> </span></span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">Flume</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">自带了很多直接可用的数据源（</span><strong><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">source</span></strong><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">），如下表：</span></p>
<p class="MsoListParagraph" style="margin-left:63pt;text-indent:0cm;"><strong><span lang="en-us" xml:lang="en-us">1.</span><span style="font-family:'宋体';">》</span><span lang="en-us" xml:lang="en-us">Flume’s Collector Tier Event Sinks</span><span style="font-family:'宋体';">：</span></strong></p>
<table class="MsoTableGrid" border="1" cellpadding="0" cellspacing="0" style="margin-left:63pt;border-collapse:collapse;border:none;"><tbody><tr><td valign="top" width="369" style="width:276.8pt;border:solid #000000 1pt;">
<p><span lang="en-us" xml:lang="en-us">collectorSink( "fsdir","fsfileprefix",rollmillis)</span></p>
</td>
<td valign="top" width="406" style="width:304.8pt;border:solid #000000 1pt;border-left:none;">
<p><span lang="en-us" xml:lang="en-us">collectorSink</span>，数据通过<span lang="en-us" xml:lang="en-us">collector</span>汇聚之后发送到<span lang="en-us" xml:lang="en-us">hdfs, fsdir</span>是<span lang="en-us" xml:lang="en-us">hdfs</span>目录，<span lang="en-us" xml:lang="en-us">fsfileprefix</span>为文件前缀码</p>
</td>
</tr></tbody></table><p class="MsoListParagraph" style="text-indent:24pt;"><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">     </span></p>
<p class="MsoNormal" align="left" style="margin-left:63pt;text-align:left;line-height:normal;">
<strong><span lang="en-us" xml:lang="en-us">2.</span><span style="font-family:'宋体';">》</span><span lang="en-us" xml:lang="en-us">Flume’s Agent Tier Event Sinks</span><span style="font-family:'宋体';">：</span></strong></p>
<table class="MsoTableGrid" border="1" cellpadding="0" cellspacing="0" width="813" style="margin-left:63pt;border-collapse:collapse;border:none;"><tbody><tr><td valign="top" width="407" style="width:305.15pt;border:solid #000000 1pt;">
<p class="MsoNormal" align="left" style="text-align:left;line-height:normal;"><span lang="en-us" style="font-family:'宋体';font-size:12pt;" xml:lang="en-us">agentSink[("machine"[,port])]</span></p>
</td>
<td valign="top" width="406" style="width:304.8pt;border:solid #000000 1pt;border-left:none;">
<p class="MsoNormal" align="left" style="text-align:left;line-height:normal;"><span lang="en-us" style="font-family:'宋体';font-size:12pt;" xml:lang="en-us">Defaults to agentE2ESink</span><span style="font-family:'宋体';font-size:12pt;">，如果省略，<span lang="en-us" xml:lang="en-us">machine</span>参数，默认使用<span lang="en-us" xml:lang="en-us">flume.collector.event.host</span>与<span lang="en-us" xml:lang="en-us">flume.collector.event.port</span>作为默认<span lang="en-us" xml:lang="en-us">collecotr</span>（以下同此）</span></p>
</td>
</tr><tr><td valign="top" width="407" style="width:305.15pt;border:solid #000000 1pt;border-top:none;">
<p class="MsoNormal" align="left" style="text-align:left;line-height:normal;"><span lang="en-us" style="font-family:'宋体';font-size:12pt;" xml:lang="en-us">agentE2ESink[("machine"[,port])]</span></p>
</td>
<td valign="top" width="406" style="width:304.8pt;border-top:none;border-left:none;border-bottom:solid #000000 1pt;border-right:solid #000000 1pt;">
<p class="MsoNormal" align="left" style="text-align:left;line-height:normal;"><span style="font-family:'宋体';font-size:12pt;">执着的<span lang="en-us" xml:lang="en-us">agent</span>，如果<span lang="en-us" xml:lang="en-us">agent</span>发送<span lang="en-us" xml:lang="en-us">event</span>没有收到<span lang="en-us" xml:lang="en-us">collector</span>成功写入的状态码，该<span lang="en-us" xml:lang="en-us">event</span>将被<span lang="en-us" xml:lang="en-us">agent</span>重复发送，直到接到成功写入的状态码</span></p>
</td>
</tr><tr><td valign="top" width="407" style="width:305.15pt;border:solid #000000 1pt;border-top:none;">
<p class="MsoNormal" align="left" style="text-align:left;line-height:normal;"><span lang="en-us" style="font-family:'宋体';font-size:12pt;" xml:lang="en-us">agentDFOSink[("machine" [,port])]</span></p>
</td>
<td valign="top" width="406" style="width:304.8pt;border-top:none;border-left:none;border-bottom:solid #000000 1pt;border-right:solid #000000 1pt;">
<p class="MsoNormal" align="left" style="text-align:left;line-height:normal;"><span style="font-family:'宋体';font-size:12pt;">本地热备<span lang="en-us" xml:lang="en-us">agent</span>，<span lang="en-us" xml:lang="en-us">agent</span>发现<span lang="en-us" xml:lang="en-us">collector</span>节点故障后，不断检查<span lang="en-us" xml:lang="en-us">collector</span>的存活状态以便重新发送<span lang="en-us" xml:lang="en-us">event</span>，在此间产生的数据将缓存到本地磁盘中</span></p>
</td>
</tr><tr><td valign="top" width="407" style="width:305.15pt;border:solid #000000 1pt;border-top:none;">
<p class="MsoNormal" align="left" style="text-align:left;line-height:normal;"><span lang="en-us" style="font-family:'宋体';font-size:12pt;" xml:lang="en-us">agentBESink[("machine"[,port])]</span></p>
</td>
<td valign="top" width="406" style="width:304.8pt;border-top:none;border-left:none;border-bottom:solid #000000 1pt;border-right:solid #000000 1pt;">
<p class="MsoNormal" align="left" style="text-align:left;line-height:normal;"><span style="font-family:'宋体';font-size:12pt;">不负责的<span lang="en-us" xml:lang="en-us">agent</span>，如果<span lang="en-us" xml:lang="en-us">collector</span>故障，将不做任何处理，它发送的数据也将被直接丢弃</span></p>
</td>
</tr><tr><td valign="top" width="407" style="width:305.15pt;border:solid #000000 1pt;border-top:none;">
<p class="MsoNormal" align="left" style="text-align:left;line-height:normal;"><span lang="en-us" style="font-family:'宋体';font-size:12pt;" xml:lang="en-us">agentE2EChain("m1[:_p1_]" [,"m2[:_p2_]"[,…]])</span></p>
</td>
<td valign="top" width="406" style="width:304.8pt;border-top:none;border-left:none;border-bottom:solid #000000 1pt;border-right:solid #000000 1pt;">
<p class="MsoNormal" align="left" style="text-align:left;line-height:normal;"><span style="font-family:'宋体';font-size:12pt;">指定多个<span lang="en-us" xml:lang="en-us">collector</span>提高可用性。 当向主<span lang="en-us" xml:lang="en-us">collector</span>发送<span lang="en-us" xml:lang="en-us">event</span>失效后，转向第二个<span lang="en-us" xml:lang="en-us">collector</span>发送，当所有的<span lang="en-us" xml:lang="en-us">collector</span>失败后，它会非常执着的再来一遍<span lang="en-us" xml:lang="en-us">...</span></span></p>
</td>
</tr><tr><td valign="top" width="407" style="width:305.15pt;border:solid #000000 1pt;border-top:none;">
<p class="MsoNormal" align="left" style="text-align:left;line-height:normal;"><span lang="en-us" style="font-family:'宋体';font-size:12pt;" xml:lang="en-us">agentDFOChain("m1[:_p1_]"[, "m2[:_p2_]"[,…]])</span></p>
</td>
<td valign="top" width="406" style="width:304.8pt;border-top:none;border-left:none;border-bottom:solid #000000 1pt;border-right:solid #000000 1pt;">
<p class="MsoNormal" align="left" style="text-align:left;line-height:normal;"><span style="font-family:'宋体';font-size:12pt;">同上，当向所有的<span lang="en-us" xml:lang="en-us">collector</span>发送事件失效后，他会将<span lang="en-us" xml:lang="en-us">event</span>缓存到本地磁盘，并检查<span lang="en-us" xml:lang="en-us">collector</span>状态，尝试重新发送</span></p>
</td>
</tr><tr><td valign="top" width="407" style="width:305.15pt;border:solid #000000 1pt;border-top:none;">
<p class="MsoNormal" align="left" style="text-align:left;line-height:normal;"><span lang="en-us" style="font-family:'宋体';font-size:12pt;" xml:lang="en-us">agentBEChain("m1[:_p1_]"[, "m2[:_p2_]"[,…]])</span></p>
</td>
<td valign="top" width="406" style="width:304.8pt;border-top:none;border-left:none;border-bottom:solid #000000 1pt;border-right:solid #000000 1pt;">
<p class="MsoNormal" align="left" style="text-align:left;line-height:normal;"><span style="font-family:'宋体';font-size:12pt;">同上，当向所有的<span lang="en-us" xml:lang="en-us">collector</span>发送事件失效后，他会将<span lang="en-us" xml:lang="en-us">event</span>丢弃</span></p>
</td>
</tr><tr><td valign="top" width="407" style="width:305.15pt;border:solid #000000 1pt;border-top:none;">
<p class="MsoNormal" align="left" style="text-align:left;line-height:normal;"><span lang="en-us" style="font-family:'宋体';font-size:12pt;" xml:lang="en-us">autoE2EChain</span></p>
</td>
<td valign="top" width="406" style="width:304.8pt;border-top:none;border-left:none;border-bottom:solid #000000 1pt;border-right:solid #000000 1pt;">
<p class="MsoNormal" align="left" style="text-align:left;line-height:normal;"><span style="font-family:'宋体';font-size:12pt;">无需指定<span lang="en-us" xml:lang="en-us">collector,</span>由<span lang="en-us" xml:lang="en-us">master</span>协调管理<span lang="en-us" xml:lang="en-us">event</span>的流向</span></p>
</td>
</tr><tr><td valign="top" width="407" style="width:305.15pt;border:solid #000000 1pt;border-top:none;">
<p class="MsoNormal" align="left" style="text-align:left;line-height:normal;"><span lang="en-us" style="font-family:'宋体';font-size:12pt;" xml:lang="en-us">autoDFOChain</span></p>
</td>
<td valign="top" width="406" style="width:304.8pt;border-top:none;border-left:none;border-bottom:solid #000000 1pt;border-right:solid #000000 1pt;">
<p class="MsoNormal" align="left" style="text-align:left;line-height:normal;"><span style="font-family:'宋体';font-size:12pt;">同上</span></p>
</td>
</tr><tr><td valign="top" width="407" style="width:305.15pt;border:solid #000000 1pt;border-top:none;">
<p class="MsoNormal" align="left" style="text-align:left;line-height:normal;"><span lang="en-us" style="font-family:'宋体';font-size:12pt;" xml:lang="en-us">autoBEChain</span></p>
</td>
<td valign="top" width="406" style="width:304.8pt;border-top:none;border-left:none;border-bottom:solid #000000 1pt;border-right:solid #000000 1pt;">
<p class="MsoNormal" align="left" style="text-align:left;line-height:normal;"><span style="font-family:'宋体';font-size:12pt;">同上</span></p>
</td>
</tr></tbody></table><p class="MsoListParagraph" style="text-indent:24pt;"><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us"> </span></p>
<p class="MsoListParagraph" style="margin-left:63pt;text-indent:0cm;"><strong><span lang="en-us" xml:lang="en-us">3.</span><span style="font-family:'宋体';">》</span><span lang="en-us" xml:lang="en-us">Flume’s Logical Sinks</span><span style="font-family:'宋体';">：</span></strong></p>
<table class="MsoTableGrid" border="1" cellpadding="0" cellspacing="0" style="margin-left:63pt;border-collapse:collapse;border:none;"><tbody><tr><td valign="top" width="407" style="width:305.15pt;border:solid #000000 1pt;">
<p><span lang="en-us" xml:lang="en-us">logicalSink("<em>logicalnode</em>")</span></p>
</td>
<td valign="top" width="406" style="width:304.8pt;border:solid #000000 1pt;border-left:none;">
<p><span lang="en-us" xml:lang="en-us"> </span></p>
</td>
</tr></tbody></table><p class="MsoListParagraph" style="text-indent:24pt;"><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us"> </span></p>
<p class="MsoListParagraph" style="margin-left:63pt;text-indent:0cm;"><strong><span lang="en-us" xml:lang="en-us">4.</span><span style="font-family:'宋体';">》</span><span lang="en-us" xml:lang="en-us">Flume’s Basic Sinks</span><span style="font-family:'宋体';">：</span></strong></p>
<table class="MsoTableGrid" border="1" cellpadding="0" cellspacing="0" style="margin-left:63pt;border-collapse:collapse;border:none;"><tbody><tr><td valign="top" width="407" style="width:305.15pt;border:solid #000000 1pt;">
<p><span lang="en-us" xml:lang="en-us">null</span></p>
</td>
<td valign="top" width="406" style="width:304.8pt;border:solid #000000 1pt;border-left:none;">
<p><span lang="en-us" xml:lang="en-us">null</span></p>
</td>
</tr><tr><td valign="top" width="407" style="width:305.15pt;border:solid #000000 1pt;border-top:none;">
<p><span lang="en-us" xml:lang="en-us">console[("formatter")]</span></p>
</td>
<td valign="top" width="406" style="width:304.8pt;border-top:none;border-left:none;border-bottom:solid #000000 1pt;border-right:solid #000000 1pt;">
<p>转发到控制台</p>
</td>
</tr><tr><td valign="top" width="407" style="width:305.15pt;border:solid #000000 1pt;border-top:none;">
<p><span lang="en-us" xml:lang="en-us">text("txtfile" [,"formatter"])</span></p>
</td>
<td valign="top" width="406" style="width:304.8pt;border-top:none;border-left:none;border-bottom:solid #000000 1pt;border-right:solid #000000 1pt;">
<p>转发到文本文件</p>
</td>
</tr><tr><td valign="top" width="407" style="width:305.15pt;border:solid #000000 1pt;border-top:none;">
<p><span lang="en-us" xml:lang="en-us">seqfile("filename")</span></p>
</td>
<td valign="top" width="406" style="width:304.8pt;border-top:none;border-left:none;border-bottom:solid #000000 1pt;border-right:solid #000000 1pt;">
<p>转发到<span lang="en-us" xml:lang="en-us">seqfile</span></p>
</td>
</tr><tr><td valign="top" width="407" style="width:305.15pt;border:solid #000000 1pt;border-top:none;">
<p><span lang="en-us" xml:lang="en-us">dfs("hdfspath")</span></p>
</td>
<td valign="top" width="406" style="width:304.8pt;border-top:none;border-left:none;border-bottom:solid #000000 1pt;border-right:solid #000000 1pt;">
<p>转发到<span lang="en-us" xml:lang="en-us">hdfs</span></p>
</td>
</tr><tr><td valign="top" width="407" style="width:305.15pt;border:solid #000000 1pt;border-top:none;">
<p><span lang="en-us" xml:lang="en-us">customdfs("hdfspath"[, "format"])</span></p>
</td>
<td valign="top" width="406" style="width:304.8pt;border-top:none;border-left:none;border-bottom:solid #000000 1pt;border-right:solid #000000 1pt;">
<p>自定义格式<span lang="en-us" xml:lang="en-us">dfs</span></p>
</td>
</tr><tr><td valign="top" width="407" style="width:305.15pt;border:solid #000000 1pt;border-top:none;">
<p><span lang="en-us" xml:lang="en-us">+escapedCustomDfs("hdfspath", "file", "format")</span></p>
</td>
<td valign="top" width="406" style="width:304.8pt;border-top:none;border-left:none;border-bottom:solid #000000 1pt;border-right:solid #000000 1pt;">
<p class="MsoNormal"><span lang="en-us" xml:lang="en-us"> </span></p>
</td>
</tr><tr><td valign="top" width="407" style="width:305.15pt;border:solid #000000 1pt;border-top:none;">
<p><span lang="en-us" xml:lang="en-us">rpcSink("host"[, port])</span></p>
</td>
<td valign="top" width="406" style="width:304.8pt;border-top:none;border-left:none;border-bottom:solid #000000 1pt;border-right:solid #000000 1pt;">
<p><span lang="en-us" xml:lang="en-us">Rpc</span>框架</p>
</td>
</tr><tr><td valign="top" width="407" style="width:305.15pt;border:solid #000000 1pt;border-top:none;">
<p><span lang="en-us" xml:lang="en-us">syslogTcp("host"[,port])</span></p>
</td>
<td valign="top" width="406" style="width:304.8pt;border-top:none;border-left:none;border-bottom:solid #000000 1pt;border-right:solid #000000 1pt;">
<p>发向网络地址</p>
</td>
</tr></tbody></table><p class="MsoListParagraph" style="text-indent:24pt;"><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us"> </span></p>
<p class="MsoListParagraph" style="text-indent:24pt;"><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us"> </span></p>
<p class="MsoNormal" align="left" style="margin-left:38.55pt;text-align:left;text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;" xml:lang="en-us">3、<span style="font:7pt 'Times New Roman';"></span></span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;" xml:lang="en-us">Flume</span><span style="font-family:'宋体';color:#000000;font-size:12pt;">核心组件</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;" xml:lang="en-us">-<strong>Channel</strong></span><span style="font-family:'宋体';color:#000000;font-size:12pt;">：</span></p>
<p class="MsoListParagraph" style="margin-left:84pt;text-indent:-21pt;"><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">1.<span style="font:7pt 'Times New Roman';"> </span></span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">MemoryChannel</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">可以实现高速的的吞吐，但是无法保证数据的完整性。</span></p>
<p class="MsoNormal" align="left" style="margin-left:84pt;text-align:left;text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;" xml:lang="en-us">2.<span style="font:7pt 'Times New Roman';"> </span></span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;" xml:lang="en-us">MemoryRecoverChannel</span><span style="font-family:'宋体';color:#000000;font-size:12pt;">在官方文档的建议上已经建议使用</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;" xml:lang="en-us">FileChannel</span><span style="font-family:'宋体';color:#000000;font-size:12pt;">来替换。</span></p>
<p class="MsoNormal" align="left" style="margin-left:84pt;text-align:left;text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;" xml:lang="en-us">3.<span style="font:7pt 'Times New Roman';"> </span></span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;" xml:lang="en-us">FileChannel</span><span style="font-family:'宋体';color:#000000;font-size:12pt;">保证数据的完整性与一致性。在具体配置不现的</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;" xml:lang="en-us">FileChannel</span><span style="font-family:'宋体';color:#000000;font-size:12pt;">时，建议</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;" xml:lang="en-us">FileChannel</span><span style="font-family:'宋体';color:#000000;font-size:12pt;">设置目录和程序日志保存的目录设成不同的磁盘，以便提高效率。</span></p>
<p class="MsoNormalIndent"><span lang="en-us" xml:lang="en-us"> </span></p>
<p class="MsoNormal" align="left" style="margin-left:38.55pt;text-align:left;text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;" xml:lang="en-us">4、<span style="font:7pt 'Times New Roman';"></span></span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;" xml:lang="en-us">Flume</span><span style="font-family:'宋体';color:#000000;font-size:12pt;">核心组件</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;" xml:lang="en-us">-<strong>collector</strong></span><span style="font-family:'宋体';color:#000000;font-size:12pt;">：</span></p>
<p class="MsoListParagraph" style="margin-left:84pt;text-indent:-21pt;"><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">1.<span style="font:7pt 'Times New Roman';"> </span></span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">collector</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">的作用是将多个</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">agent</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">的数据汇总后，加载到</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">storage</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">中。它的</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">source</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">和</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">sink</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">与</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">agent</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">类似。</span></p>
<p class="MsoListParagraph" style="margin-left:59.55pt;text-indent:3.45pt;"><strong><span lang="en-us" xml:lang="en-us">1.</span><span style="font-family:'宋体';">》</span><strong><span lang="en-us" xml:lang="en-us">source</span></strong><span style="font-family:'宋体';">：</span></strong></p>
<table class="MsoTableGrid" border="1" cellpadding="0" cellspacing="0" style="margin-left:63pt;border-collapse:collapse;border:none;"><tbody><tr><td valign="top" width="407" style="width:305.15pt;border:solid #000000 1pt;">
<p><span lang="en-us" xml:lang="en-us">collectorSource[(<em><span style="font-family:'宋体';">port</span></em>)]</span></p>
</td>
<td valign="top" width="406" style="width:304.8pt;border:solid #000000 1pt;border-left:none;">
<p><span lang="en-us" xml:lang="en-us">Collector source</span>，监听端口汇聚数据</p>
</td>
</tr><tr><td valign="top" width="407" style="width:305.15pt;border:solid #000000 1pt;border-top:none;">
<p><span lang="en-us" xml:lang="en-us">autoCollectorSource</span></p>
</td>
<td valign="top" width="406" style="width:304.8pt;border-top:none;border-left:none;border-bottom:solid #000000 1pt;border-right:solid #000000 1pt;">
<p>通过<span lang="en-us" xml:lang="en-us">master</span>协调物理节点自动汇聚数据</p>
</td>
</tr><tr><td valign="top" width="407" style="width:305.15pt;border:solid #000000 1pt;border-top:none;">
<p><span lang="en-us" xml:lang="en-us">logicalSource</span></p>
</td>
<td valign="top" width="406" style="width:304.8pt;border-top:none;border-left:none;border-bottom:solid #000000 1pt;border-right:solid #000000 1pt;">
<p>逻辑<span lang="en-us" xml:lang="en-us">source</span>，由<span lang="en-us" xml:lang="en-us">master</span>分配端口并监听<span lang="en-us" xml:lang="en-us">rpcSink</span></p>
</td>
</tr></tbody></table><p class="MsoNormal" align="left" style="margin-left:63pt;text-align:left;line-height:normal;">
<span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;" xml:lang="en-us"> </span></p>
<p class="MsoListParagraph" style="margin-left:59.55pt;text-indent:3.45pt;"><strong><span lang="en-us" xml:lang="en-us">2.</span><span style="font-family:'宋体';">》</span><span lang="en-us" xml:lang="en-us">sinks</span><span style="font-family:'宋体';">：</span></strong></p>
<table class="MsoTableGrid" border="1" cellpadding="0" cellspacing="0" style="margin-left:63pt;border-collapse:collapse;border:none;"><tbody><tr><td valign="top" width="407" style="width:305.15pt;border:solid #000000 1pt;">
<p><span lang="en-us" xml:lang="en-us">collectorSink( "fsdir","fsfileprefix",rollmillis)</span></p>
</td>
<td valign="top" width="406" style="width:304.8pt;border:solid #000000 1pt;border-left:none;">
<p><span lang="en-us" xml:lang="en-us">collectorSink</span>，数据通过<span lang="en-us" xml:lang="en-us">collector</span>汇聚之后发送到<span lang="en-us" xml:lang="en-us">hdfs, fsdir</span>是<span lang="en-us" xml:lang="en-us">hdfs</span>目录，<span lang="en-us" xml:lang="en-us">fsfileprefix</span>为文件前缀码</p>
</td>
</tr><tr><td valign="top" width="407" style="width:305.15pt;border:solid #000000 1pt;border-top:none;">
<p><span lang="en-us" xml:lang="en-us">customdfs("hdfspath"[, "format"])</span></p>
</td>
<td valign="top" width="406" style="width:304.8pt;border-top:none;border-left:none;border-bottom:solid #000000 1pt;border-right:solid #000000 1pt;">
<p>自定义格式<span lang="en-us" xml:lang="en-us">dfs</span></p>
</td>
</tr></tbody></table><p class="MsoNormal" align="left" style="margin-left:63pt;text-align:left;line-height:normal;">
<span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;" xml:lang="en-us"> </span></p>
<p class="MsoNormal" align="left" style="margin-left:38.55pt;text-align:left;text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;" xml:lang="en-us">5、<span style="font:7pt 'Times New Roman';"></span></span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;" xml:lang="en-us">Flume</span><span style="font-family:'宋体';color:#000000;font-size:12pt;">核心组件</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;" xml:lang="en-us">-<strong>storage</strong></span><span style="font-family:'宋体';color:#000000;font-size:12pt;">：</span></p>
<p class="MsoNormal" align="left" style="margin-left:84pt;text-align:left;text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;" xml:lang="en-us">1.<span style="font:7pt 'Times New Roman';"> </span></span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;" xml:lang="en-us">storage</span><span style="font-family:'宋体';color:#000000;font-size:12pt;">是存储系统，可以是一个普通</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;" xml:lang="en-us">file</span><span style="font-family:'宋体';color:#000000;font-size:12pt;">，也可以是</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;" xml:lang="en-us">HDFS</span><span style="font-family:'宋体';color:#000000;font-size:12pt;">，</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;" xml:lang="en-us">HIVE</span><span style="font-family:'宋体';color:#000000;font-size:12pt;">，</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;" xml:lang="en-us">HBase</span><span style="font-family:'宋体';color:#000000;font-size:12pt;">，分布式存储等。</span></p>
<p class="MsoNormal" align="left" style="margin-left:38.55pt;text-align:left;text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;" xml:lang="en-us">6、<span style="font:7pt 'Times New Roman';"></span></span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;" xml:lang="en-us">Flume</span><span style="font-family:'宋体';color:#000000;font-size:12pt;">核心组件</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;" xml:lang="en-us">-<strong>Master</strong></span><span style="font-family:'宋体';color:#000000;font-size:12pt;">：</span></p>
<p class="MsoNormal" align="left" style="margin-left:84pt;text-align:left;text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;" xml:lang="en-us">1.<span style="font:7pt 'Times New Roman';"> </span></span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;" xml:lang="en-us">Master</span><span style="font-family:'宋体';color:#000000;font-size:12pt;">是管理协调</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;" xml:lang="en-us">agent</span><span style="font-family:'宋体';color:#000000;font-size:12pt;">和</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;" xml:lang="en-us">collector</span><span style="font-family:'宋体';color:#000000;font-size:12pt;">的配置等信息，是</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;" xml:lang="en-us">flume</span><span style="font-family:'宋体';color:#000000;font-size:12pt;">集群的控制器。</span></p>
<p class="MsoNormalIndent"><span lang="en-us" xml:lang="en-us"> </span></p>
<h2><span lang="en-us" xml:lang="en-us">1.4 </span><span style="font-family:'宋体';color:#000000;">学习</span><span lang="en-us" style="font-family:Consolas;color:#000000;" xml:lang="en-us">Linux</span><span style="font-family:'宋体';color:#000000;">推荐书籍：</span></h2>
<p class="MsoNormalIndent" style="margin-left:49.35pt;text-indent:-21pt;"><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">1.<span style="font:7pt 'Times New Roman';"> </span></span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">《</span><span lang="en-us" xml:lang="en-us">Using<em><span style="font-style:normal;">Flume</span></em></span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">》</span></p>
<p class="MsoNormalIndent" style="margin-left:49.35pt;text-indent:-21pt;"><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">2.<span style="font:7pt 'Times New Roman';"> </span></span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">《</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">Flume</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">日志收集与</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">MapReduce</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">模式》</span></p>
<p class="MsoNormalIndent" style="margin-left:49.35pt;text-indent:-21pt;"><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">3.<span style="font:7pt 'Times New Roman';"> </span></span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">《</span><span lang="en-us" style="font-family:Consolas;color:#000000;font-size:12pt;line-height:150%;" xml:lang="en-us">Flume</span><span style="font-family:'宋体';color:#000000;font-size:12pt;line-height:150%;">：构建高可用、可扩展的海量日志采集系统》</span></p>
<p class="MsoNormal" align="left" style="text-align:left;line-height:normal;">
<span lang="en-us" style="font-family:'Courier New';color:#000000;font-size:10pt;" xml:lang="en-us"> </span></p>
<p class="MsoNormal" align="left" style="text-align:left;line-height:normal;">
<span lang="en-us" style="font-family:'Courier New';color:#000000;font-size:10pt;" xml:lang="en-us"><br></span></p>
<span style="font-family:'Comic Sans MS';font-size:18px;">                --以上为《Flume教程(一) Flume入门教程》，如有不当之处请指出，我后续逐步完善更正，大家共同提高。谢谢大家对我的关注。</span>
<p>                                                                                                                                                                                      <span lang="en-us" xml:lang="en-us"><span><span style="font-size:18px;"><span style="font-family:'Comic Sans MS';"><span lang="en-us" xml:lang="en-us"><span><span style="font-family:'宋体';">——</span></span></span><span style="font-family:'宋体';">厚积薄发</span>(yuanxw)</span></span></span></span></p>
<p><span lang="en-us" xml:lang="en-us"><span><span style="font-size:18px;"><span style="font-family:'Comic Sans MS';"><br></span></span></span></span></p>
</div>
            </div>
                </div>