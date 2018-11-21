---
layout:     post
title:       Flume OG 与 Flume NG 的对比 
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <div class="iteye-blog-content-contain" style="font-size:14px;">
<p>引自：http://blog.csdn.net/rzhzhz/article/details/8778266</p>
<p> </p>
<p><span style="font-family:'Microsoft YaHei';font-size:14px;">很久没接触flume 了，刚掀开官网一看，发现flume已然不是以前的那个flume了，其实早在flume技术群就听到NG这个字眼，以前没特注意，今天做了些对比，发现 flume确实有了投胎换骨般的改变。首先介绍下Flume OG &amp; Flume NG这两个概念</span></p>
<p><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';"><strong>Flume OG:</strong>Flume original generation 即Flume 0.9.x版本</span></span></p>
<p><span style="font-size:14px;"><span style="font-family:'Microsoft YaHei';"><strong>Flume NG:</strong>Flume next generation ，即Flume 1.x版本</span></span></p>
<p> </p>
<p><span style="font-family:'Microsoft YaHei';font-size:14px;">对于<strong>Flume OG</strong> ,可以说他是一个分布式日志收集系统，有Mater概念，依赖于zookeeper，以下是其架构图</span></p>
<p><span style="font-family:'Microsoft YaHei';font-size:14px;"><img src="http://my.csdn.net/uploads/201204/11/1334113302_3896.png" alt=""></span></p>
<p><span style="font-family:'Microsoft YaHei';font-size:14px;">Agent用于采集数据，agent是flume中产生数据流的地方，同时，agent会将产生的数据流传输到collector。对应的，collector用于对数据进行聚合，往往会产生一个更大的流。</span></p>
<p><span style="font-family:'Microsoft YaHei';font-size:14px;">而对于<strong>Flume NG</strong>， 它摒弃了Master和zookeeper，collector也没有了，web配置台也没有了，只剩下source，sink和channel，此时一 个Agent的概念包括source,channel和sink，完全由一个分布式系统变成了传输工具。不同机器之间的数据传输不再是OG那样由 agent-&gt;collector，而是由一个Agent端的sink流向另一个agent的source。其新的架构如下</span></p>
<div><span style="font-size:14px;"><br></span></div>
<div>
<br><span style="font-family:'Microsoft YaHei';font-size:14px;"><img src="http://dl.iteye.com/upload/attachment/0071/7379/e8fd34bb-0ba3-3a23-9482-804e4e541ecf.png" alt=""><br>  </span>
</div>
<div><span style="font-size:14px;"><br></span></div>
<div><span style="font-size:14px;"><br></span></div>
<div>
<br><span style="font-family:'Microsoft YaHei';font-size:14px;"><img src="http://dl.iteye.com/upload/attachment/0071/7381/974f9150-bfaa-36cb-b3a4-ec7e65dbda86.png" alt=""></span>
</div>
<div> </div>
<div><span style="font-family:'Microsoft YaHei';font-size:14px;">以下是官网资料</span></div>
<div>
<p style="color:#333333;line-height:17px;font-family:Helvetica, Arial, sans-serif;font-size:13px;background-color:#ffffff;"><span style="font-family:'Microsoft YaHei';font-size:14px;">Flume NG is a huge departure from Flume OG (original generation, or "original gangsta," if you prefer) in its implementation although many of the original concepts are the same. If you're already familiar with Flume, here's what you need to know.</span></p>
<ul style="color:#333333;line-height:17px;font-family:Helvetica, Arial, sans-serif;font-size:13px;background-color:#ffffff;"><li style="line-height:13pt;font-size:10pt;"><span style="font-family:'Microsoft YaHei';font-size:14px;">You still have <em>sources</em> and <em>sinks</em> and they still do the same thing. They are now connected by <em>channels</em>.</span></li>
<li style="line-height:13pt;font-size:10pt;"><span style="font-family:'Microsoft YaHei';font-size:14px;">Channels are pluggable and dictate durability. Flume NG ships with an in-memory channel for fast, but non-durable event delivery and a JDBC-based channel for durable event delivery. We have recently added a file-based durable channel too. </span></li>
<li style="line-height:13pt;font-size:10pt;"><span style="font-family:'Microsoft YaHei';font-size:14px;">There's no more logical or physical nodes. We call all physical nodes <em>agents</em> and agents can run zero or more sources and sinks.</span></li>
<li style="line-height:13pt;font-size:10pt;"><span style="font-family:'Microsoft YaHei';font-size:14px;">There's no master and no ZooKeeper dependency anymore. At this time, Flume runs with a simple file-based configuration system.</span></li>
<li style="line-height:13pt;font-size:10pt;"><span style="font-family:'Microsoft YaHei';font-size:14px;">Just about everything is a plugin, some end user facing, some for tool and system developers. (Specifically, sources, sinks, channels, configuration providers, lifecycle management policies, input and output formats, compression, source and sink channel adapters, and the kitchen sink.)</span></li>
<li style="line-height:13pt;font-size:10pt;">
<span style="font-family:'Microsoft YaHei';font-size:14px;">Tons of things are not yet implemented. Please file </span><a href="https://issues.apache.org/jira/browse/FLUME" rel="nofollow"><span style="font-family:'Microsoft YaHei';font-size:14px;color:#aa2828;">JIRAs</span></a><span style="font-family:'Microsoft YaHei';font-size:14px;"> and / or vote for features you deem important. </span>
</li>
</ul></div>
</div>            </div>
                </div>