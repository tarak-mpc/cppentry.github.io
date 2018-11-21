---
layout:     post
title:      为什么需要flume以及flume的原理
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主女朋友允许不得转载。					https://blog.csdn.net/qq_26442553/article/details/79032805				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>1.为什么要有flume?<br><span style="color:#ff0000;">    flume的设计宗旨是向hadoop集群批量导入基于事件的海量数据。</span>一个典型的例子就是利用flume从一组web服务器中收集日志文件，然后把这些文件中的日志事件转移到一个新的HDFS汇总文件中以做进一步的处理，所以flume的终点sink一般是HDFS,当然因为flume本生的灵活性，又可以将采集到的数据输出到HDFS、hbase、hive、kafka等众多外部存储系统中<br></p>
<p><br></p>
<p>2.flume的本质是什么？</p>
<p>    要想使用 flume，就需使用flume的代理（agent）<span style="color:#ff0000;">,Flume的代理agent是由持续运行的source(数据源)，sink(数据目标)以及channel（用于连接source源和sink目标地的通道）构成的Java进程</span></p>
<p>    Flume分布式系统中最核心的角色是agent，flume采集系统就是由一个个agent所连接起来形成。每一个agent（进程）相当于一个数据传递员，内部有三个组件：</p>
<p>         ①Source：采集源，用于跟数据源对接，以获取数据 </p>
<p>         ②Sink：下沉地，采集数据的传送目的，用于往下一级agent传递数据或者往最终存储系统传递数据<br></p>
<p>         ③<span style="color:rgb(1,1,1);font-family:Calibri;">Channel</span><span style="color:rgb(1,1,1);font-family:'宋体';">：</span><span style="color:rgb(1,1,1);font-family:Calibri;">angent</span><span style="color:rgb(1,1,1);font-family:'宋体';">内部的数据传输通道，用于从</span><span style="color:rgb(1,1,1);font-family:Calibri;">source</span><span style="color:rgb(1,1,1);font-family:'宋体';">将数据传递到</span><span style="color:rgb(1,1,1);font-family:Calibri;">sink</span></p>
<p><span style="font-family:Calibri;"><span style="color:#ff0000;">     注意：</span><span style="color:#010101;">.Source 到 Channel 到 Sink之间传递数据的形式是Event事件；Event事件是一个数据流单元。面对具体不同的采集源，有不同的实现类，同理sink也是，所以不需要我们编程。</span></span></p>
<p>3.flume实际开发中常见的采集系统结构图</p>
<p></p>
<div style="min-height:14pt;"><span style="min-height:14pt;font-size:10pt;color:rgb(1,1,1);font-family:'宋体';"><strong>  1.单个</strong></span><span style="min-height:14pt;font-size:10pt;color:rgb(1,1,1);font-family:Calibri;"><strong>agent</strong></span><span style="min-height:14pt;font-size:10pt;color:rgb(1,1,1);font-family:'宋体';"><strong>采集数据。</strong></span></div>
<div style="min-height:14pt;"><span style="min-height:14pt;font-size:10pt;color:rgb(1,1,1);font-family:'宋体';"><strong>  <img src="https://img-blog.csdn.net/20180111125632945?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMjY0NDI1NTM=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></strong></span></div>
<div style="min-height:14pt;"><span style="min-height:14pt;font-size:10pt;color:rgb(1,1,1);font-family:'宋体';"><strong>  2.复杂结构：多级agent之间串联</strong></span></div>
<div style="min-height:14pt;"><span style="min-height:14pt;font-size:10pt;font-family:'宋体';"><strong><span style="color:#010101;"> </span><span style="color:#ff0000;">   注意，多个agent级联时：一个source可以对接多个chanel,但是一个chanel只能对接一个sink</span></strong></span></div>
<div style="min-height:14pt;"><span style="min-height:14pt;font-size:10pt;color:rgb(1,1,1);font-family:'宋体';"><strong><img src="https://img-blog.csdn.net/20180111125737594" alt=""><br></strong></span></div>
<div style="min-height:14pt;"><span style="min-height:14pt;font-size:10pt;color:rgb(1,1,1);font-family:'宋体';"><strong><br></strong></span></div>
<div style="min-height:14pt;"><span style="min-height:14pt;font-size:10pt;color:rgb(1,1,1);font-family:'宋体';"><strong><img src="https://img-blog.csdn.net/20180111125754034" alt=""><br></strong></span></div>
<div style="min-height:156pt;">下面一章讲解flume的配置使用，以及核心原理分析</div>
            </div>
                </div>