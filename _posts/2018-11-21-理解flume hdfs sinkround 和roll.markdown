---
layout:     post
title:      理解flume hdfs sinkround 和roll
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h3 style="background:#FFFFFF;"><span lang="en-us" xml:lang="en-us"><span style="font-size:24px;"><span><span lang="en-us" style="font-family:'宋体';text-decoration:none;" xml:lang="en-us"><span lang="en-us" xml:lang="en-us">理解</span></span></span><span><span style="text-decoration:none;">flume
 hdfs sinkround </span></span><span><span lang="en-us" style="font-family:'宋体';text-decoration:none;" xml:lang="en-us"><span lang="en-us" xml:lang="en-us">和</span></span></span><span><span style="text-decoration:none;">roll</span></span></span></span></h3>
<p><span style="font-size:14px;"><span style="font-family:'宋体';"><br></span></span></p>
<p><span style="font-size:14px;"><span style="font-family:'宋体';">配置</span><span lang="en-us" xml:lang="en-us"><span style="font-family:Calibri;">flume hdfs sink
</span></span><span style="font-family:'宋体';">的时候注意这两个配置项，比如：</span></span><span lang="en-us" style="font-family:'微软雅黑', sans-serif;" xml:lang="en-us"></span></p>
<p><span lang="en-us" style="font-family:'微软雅黑', sans-serif;" xml:lang="en-us"><span style="font-size:14px;">collector1.sinks.sink_hdfs.hdfs.rollSize= 1024000000 <br>
collector1.sinks.sink_hdfs.hdfs.rollCount = 0 <br>
collector1.sinks.sink_hdfs.hdfs.rollInterval = 21600<br>
collector1.sinks.sink_hdfs.hdfs.round = true <br>
collector1.sinks.sink_hdfs.hdfs.roundValue = 6</span></span></p>
<p><span lang="en-us" style="font-family:'微软雅黑', sans-serif;" xml:lang="en-us"><span style="font-size:14px;">collector1.sinks.sink_hdfs.hdfs.roundUnit= hour</span></span></p>
<p><span lang="en-us" xml:lang="en-us"><span style="font-family:Calibri;"><span style="font-size:18px;">rollsize</span></span></span><span style="font-size:14px;"><span style="font-family:'宋体';">的配置表示到</span><span lang="en-us" xml:lang="en-us"><span style="font-family:Calibri;">1G</span></span><span style="font-family:'宋体';">大小的时候回滚到下一个文件，也就是到了这个时间</span><span lang="en-us" xml:lang="en-us"><span style="font-family:Calibri;">
 hdfs</span></span><span style="font-family:'宋体';">就会</span><span lang="en-us" xml:lang="en-us"><span style="font-family:Calibri;">rename</span></span><span style="font-family:'宋体';">正在写的文件到已经写完。日志是这样的：</span></span><span lang="en-us" style="font-family:'微软雅黑', sans-serif;" xml:lang="en-us"></span></p>
<p><span lang="en-us" style="font-family:'微软雅黑', sans-serif;" xml:lang="en-us"><span style="font-size:14px;">Renaminghdfs://192.168.1.32:8020/staging/xxx/app.news.test/dt=<span style="font-family:'微软雅黑';">2017-08-</span>16 /events.<span style="font-family:'微软雅黑';">20170816163500</span>.
 1502901303222. log.tmp      to     hdfs://192.168.2.231:8020/staging/xxx/app.news.test/dt=<span style="font-family:'微软雅黑';">2017-08-16/events.20170816163500</span>. 1502901303222.log</span></span></p>
<p><span style="font-size:14px;"><span lang="en-us" xml:lang="en-us"><span style="font-family:Calibri;"><span style="font-size:18px;">rollInterval</span></span></span><span style="font-family:'宋体';">的配置表示每个</span><span lang="en-us" xml:lang="en-us"><span style="font-family:Calibri;">6</span></span><span style="font-family:'宋体';">小时回滚到下一个文件，但是</span><span lang="en-us" xml:lang="en-us"><span style="font-family:Calibri;">round</span></span><span style="font-family:'宋体';">的理解是，每</span><span lang="en-us" xml:lang="en-us"><span style="font-family:Calibri;">6</span></span><span style="font-family:'宋体';">小时产生一个新文件，比如把</span><span lang="en-us" xml:lang="en-us"><span style="font-family:Calibri;">24</span></span><span style="font-family:'宋体';">小时分成</span><span lang="en-us" xml:lang="en-us"><span style="font-family:Calibri;">4</span></span><span style="font-family:'宋体';">份，假设现在的时间是</span><span lang="en-us" xml:lang="en-us"><span style="font-family:Calibri;">15:40,</span></span><span style="font-family:'宋体';">如果这时候有新的日志到来，那么</span><span lang="en-us" xml:lang="en-us"><span style="font-family:Calibri;">hdfs
</span></span><span style="font-family:'宋体';">会创建一个新的</span><span lang="en-us" xml:lang="en-us"><span style="font-family:Calibri;">hdfs</span></span><span style="font-family:'宋体';">文件，文件名称是</span><span lang="en-us" style="font-family:'微软雅黑', sans-serif;" xml:lang="en-us">2017081612</span><span style="font-family:'宋体';">，就是</span><span lang="en-us" xml:lang="en-us"><span style="font-family:Calibri;">15:40
</span></span><span style="font-family:'宋体';">是分布在</span><span lang="en-us" xml:lang="en-us"><span style="font-family:Calibri;">12-18</span></span><span style="font-family:'宋体';">这个区间的，</span><span lang="en-us" xml:lang="en-us"><span style="font-family:Calibri;">flume
</span></span><span style="font-family:'宋体';">日志：</span></span><span lang="en-us" style="font-family:'微软雅黑', sans-serif;" xml:lang="en-us"></span></p>
<p><span style="font-size:14px;"><span lang="en-us" style="font-family:'微软雅黑', sans-serif;" xml:lang="en-us">Creating hdfs://nameservice1/staging/xxx/app.news.test/dt=<span style="font-family:'微软雅黑';">2017-08-</span><span style="font-family:'微软雅黑';">16/events.</span><span style="font-family:'微软雅黑';">20170816163500</span><span style="font-family:'微软雅黑';">.1502901303222.log.tmp</span></span><span style="font-family:'宋体';">。</span></span><span lang="en-us" style="font-family:'微软雅黑', sans-serif;" xml:lang="en-us"></span></p>
            </div>
                </div>