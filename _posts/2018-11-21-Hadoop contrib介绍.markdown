---
layout:     post
title:      Hadoop contrib介绍
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-family:'Lucida Grande', 'Lucida Sans Unicode', Verdana, sans-serif;line-height:24px;background-color:rgb(247,243,237);"><br></span></p>
<p><span style="font-family:'Lucida Grande', 'Lucida Sans Unicode', Verdana, sans-serif;line-height:24px;background-color:rgb(247,243,237);">Hadoop Contrib是Hadoop代码中第三方公司贡献的工具包，一般作为Hadoop kernel的扩展功能，它包含多个非常有用的扩展包，本文以Hadoop 1.0为例对Hadoop Contrib中的各个工具包进行介绍。</span></p>
<p style="list-style:none;text-indent:2.1em;font-family:'Lucida Grande', 'Lucida Sans Unicode', Verdana, sans-serif;line-height:24px;background-color:rgb(247,243,237);">
</p>
<h3 style="list-style:none;font-size:13.1999998092651px;font-family:Verdana, sans-serif;line-height:24px;background-color:rgb(247,243,237);">
<span>【Gridmix2】</span></h3>
<p style="list-style:none;text-indent:2.1em;font-family:'Lucida Grande', 'Lucida Sans Unicode', Verdana, sans-serif;line-height:24px;background-color:rgb(247,243,237);">
Hadoop Gridmix2是针对hadoop系统的基准测试程序。它具备评测大规模数据处理系统所需的各个功能模块，包括：产生数据，生成并提交作业，统计作业完成时间等。Gridmix2可产生各种类型不同长度的混合作业，以帮助用户测试Hadoop集群的吞吐率、响应时间等基本指标。</p>
<h3 style="list-style:none;font-size:13.1999998092651px;font-family:Verdana, sans-serif;line-height:24px;background-color:rgb(247,243,237);">
<span>【Gridmix】</span></h3>
<p style="list-style:none;text-indent:2.1em;font-family:'Lucida Grande', 'Lucida Sans Unicode', Verdana, sans-serif;line-height:24px;background-color:rgb(247,243,237);">
即Gridmix3，是一种track-based gridmix，它可帮助用户在实验集群中重现或者模拟线上集群中出现的问题，它不是gridmix2的替代品，而是对gridmix2不足的一种弥补。通常情况下，当用户作业在线上集群出现问题时，用户可使用Rumen（<a href="https://issues.apache.org/jira/browse/MAPREDUCE-1918" rel="nofollow" style="color:rgb(0,84,179);text-decoration:none;">https://issues.apache.org/jira/browse/MAPREDUCE-1918</a>）在线上集群上获取该作业（从JobHistory日志中提取）的运行轨迹，
 然后将作业的轨迹文件作为Gridmix3输入，运行到实验集群上，以重现线上集群出现的问题，进而发现问题。</p>
<p style="list-style:none;text-indent:2.1em;font-family:'Lucida Grande', 'Lucida Sans Unicode', Verdana, sans-serif;line-height:24px;background-color:rgb(247,243,237);">
具体可参考：</p>
<p style="list-style:none;text-indent:2.1em;font-family:'Lucida Grande', 'Lucida Sans Unicode', Verdana, sans-serif;line-height:24px;background-color:rgb(247,243,237);">
<a href="http://developer.yahoo.com/blogs/hadoop/posts/2010/04/gridmix3_emulating_production/" rel="nofollow" style="color:rgb(0,84,179);text-decoration:none;">http://developer.yahoo.com/blogs/hadoop/posts/2010/04/gridmix3_emulating_production/</a></p>
<p style="list-style:none;text-indent:2.1em;font-family:'Lucida Grande', 'Lucida Sans Unicode', Verdana, sans-serif;line-height:24px;background-color:rgb(247,243,237);">
<a href="https://issues.apache.org/jira/browse/MAPREDUCE-728" rel="nofollow" style="color:rgb(0,84,179);text-decoration:none;">https://issues.apache.org/jira/browse/MAPREDUCE-728</a></p>
<h3 style="list-style:none;font-size:13.1999998092651px;font-family:Verdana, sans-serif;line-height:24px;background-color:rgb(247,243,237);">
<span>【Vaidya】</span></h3>
<p style="list-style:none;text-indent:2.1em;font-family:'Lucida Grande', 'Lucida Sans Unicode', Verdana, sans-serif;line-height:24px;background-color:rgb(247,243,237);">
Vaidya是一个基于规则的MapReduce作业性能诊断工具，通常，用户运行完一个作业后，可运行Vaidya 以发现该作业存在的各种性能问题。它的基本工作原理是解析作业JobHistory日志和作业配置文件中的参数和统计信息，并将这些信息作用到一些事先定义好的规则上，最后输出规则判定结果。</p>
<p style="list-style:none;text-indent:2.1em;font-family:'Lucida Grande', 'Lucida Sans Unicode', Verdana, sans-serif;line-height:24px;background-color:rgb(247,243,237);">
具体可参考：<a href="http://hadoop.apache.org/docs/r1.0.0/vaidya.html" rel="nofollow" style="color:rgb(0,84,179);text-decoration:none;">http://hadoop.apache.org/docs/r1.0.0/vaidya.html</a></p>
<h3 style="list-style:none;font-size:13.1999998092651px;font-family:Verdana, sans-serif;line-height:24px;background-color:rgb(247,243,237);">
<span>【Failmon】</span></h3>
<p style="list-style:none;text-indent:2.1em;font-family:'Lucida Grande', 'Lucida Sans Unicode', Verdana, sans-serif;line-height:24px;background-color:rgb(247,243,237);">
Failmon是IBM贡献的日志收集工具，可将各个slave节点上的作业日志，系统日志解析后，格式化存储到HDFS的固定 目录下，用户可使用failmon提供的shell名利分析这些日志，也可以使用pig，Hive等进行分析处理。（它在0.19.0版本中已出现，且与Apache Chuwa功能重复）。</p>
<p style="list-style:none;text-indent:2.1em;font-family:'Lucida Grande', 'Lucida Sans Unicode', Verdana, sans-serif;line-height:24px;background-color:rgb(247,243,237);">
具体可参考：<a href="https://issues.apache.org/jira/browse/HADOOP-3585" rel="nofollow" style="color:rgb(0,84,179);text-decoration:none;">https://issues.apache.org/jira/browse/HADOOP-3585</a></p>
<h3 style="list-style:none;font-size:13.1999998092651px;font-family:Verdana, sans-serif;line-height:24px;background-color:rgb(247,243,237);">
<span>【Capacity Scheduler】</span></h3>
<p style="list-style:none;text-indent:2.1em;font-family:'Lucida Grande', 'Lucida Sans Unicode', Verdana, sans-serif;line-height:24px;background-color:rgb(247,243,237);">
Yahoo贡献的多用户多队列任务调度器，现已被很多公司采用。</p>
<h3 style="list-style:none;font-size:13.1999998092651px;font-family:Verdana, sans-serif;line-height:24px;background-color:rgb(247,243,237);">
<span>【Fair Scheduler】</span></h3>
<p style="list-style:none;text-indent:2.1em;font-family:'Lucida Grande', 'Lucida Sans Unicode', Verdana, sans-serif;line-height:24px;background-color:rgb(247,243,237);">
Facebook贡献的多用户多队列任务调度器，现已被很多公司采用。</p>
<h3 style="list-style:none;font-size:13.1999998092651px;font-family:Verdana, sans-serif;line-height:24px;background-color:rgb(247,243,237);">
<span>【Streaming】</span></h3>
<p style="list-style:none;text-indent:2.1em;font-family:'Lucida Grande', 'Lucida Sans Unicode', Verdana, sans-serif;line-height:24px;background-color:rgb(247,243,237);">
Hadoop提供的多语言编写MapReduce作业的工具，通过使用该工具，用户可将任意可执行文件或者脚本作为mapper或者reducer，大大提高了非java程序员开发mapreduce程序的效率。</p>
<h3 style="list-style:none;font-size:13.1999998092651px;font-family:Verdana, sans-serif;line-height:24px;background-color:rgb(247,243,237);">
<span>【Index】</span></h3>
<p style="list-style:none;text-indent:2.1em;font-family:'Lucida Grande', 'Lucida Sans Unicode', Verdana, sans-serif;line-height:24px;background-color:rgb(247,243,237);">
利用mapreduce程序分布式并行构建和更新索引文件。Index包将索引划分成若干个shard，每个shard对应一个Lucene实例。在Map阶段，Index程序对输入数据进行格式化、分析和解析，在Reduce阶段，对数据进行聚集、更新等操作。需要注意的是，在更新时，会用到本地磁盘，即当对某个索引块进行更新时，会将新生成的索引写到本地磁盘上，然后再拷贝回HDFS上。</p>
<h3 style="list-style:none;font-size:13.1999998092651px;font-family:Verdana, sans-serif;line-height:24px;background-color:rgb(247,243,237);">
<span>【MRunit（Hadoop 0.21.0）】</span></h3>
<p style="list-style:none;text-indent:2.1em;font-family:'Lucida Grande', 'Lucida Sans Unicode', Verdana, sans-serif;line-height:24px;background-color:rgb(247,243,237);">
MRUnit是一款由Couldera公司开发的专门针对Hadoop中编写MapReduce单元测试的框架。可以用MapDriver单独测试Mapperr，用ReduceDriver单独测试Reducer，用MapReduceDriver测试MapReduce作业。</p>
<p style="list-style:none;text-indent:2.1em;font-family:'Lucida Grande', 'Lucida Sans Unicode', Verdana, sans-serif;line-height:24px;background-color:rgb(247,243,237);">
具体可参考：<a href="http://www.taobaotest.com/blogs/qa?bid=10550" rel="nofollow" style="color:rgb(0,84,179);text-decoration:none;">http://www.taobaotest.com/blogs/qa?bid=10550</a></p>
<h3 style="list-style:none;font-size:13.1999998092651px;font-family:Verdana, sans-serif;line-height:24px;background-color:rgb(247,243,237);">
<span>【Mumak（Hadoop 0.21.0）】</span></h3>
<p style="list-style:none;text-indent:2.1em;font-family:'Lucida Grande', 'Lucida Sans Unicode', Verdana, sans-serif;line-height:24px;background-color:rgb(247,243,237);">
MapReduce模拟器，它可用少量节点模拟一个真实的线上生产环境（包括节点规模、拓扑结构等），它通过分析JobHistory日志还原作业的运行过程。该模拟器由Simulated JobTracker、Simulated Cluster (set of tasktrackers)、Client for handling job-submission等基础组件组成，它允许用户使用已存在的真实调度器，比如FIFO、FairScheduler、Capacity Scheduler等。</p>
<p style="list-style:none;text-indent:2.1em;font-family:'Lucida Grande', 'Lucida Sans Unicode', Verdana, sans-serif;line-height:24px;background-color:rgb(247,243,237);">
<a href="https://issues.apache.org/jira/browse/MAPREDUCE-728" rel="nofollow" style="color:rgb(0,84,179);text-decoration:none;">https://issues.apache.org/jira/browse/MAPREDUCE-728</a></p>
<h3 style="list-style:none;font-size:13.1999998092651px;font-family:Verdana, sans-serif;line-height:24px;background-color:rgb(247,243,237);">
<span>【Vertica（Hadoop 0.21.0）】</span></h3>
<p style="list-style:none;text-indent:2.1em;font-family:'Lucida Grande', 'Lucida Sans Unicode', Verdana, sans-serif;line-height:24px;background-color:rgb(247,243,237);">
用于支持Vertica数据库的组件。</p>
<p style="list-style:none;text-indent:2.1em;font-family:'Lucida Grande', 'Lucida Sans Unicode', Verdana, sans-serif;line-height:24px;background-color:rgb(247,243,237);">
<a href="https://issues.apache.org/jira/browse/MAPREDUCE/component/12313270" rel="nofollow" style="color:rgb(0,84,179);text-decoration:none;">https://issues.apache.org/jira/browse/MAPREDUCE/component/12313270</a></p>
<h3 style="list-style:none;font-size:13.1999998092651px;font-family:Verdana, sans-serif;line-height:24px;background-color:rgb(247,243,237);">
<span>【Raid（Hadoop 0.21.0）】</span></h3>
<p style="list-style:none;text-indent:2.1em;font-family:'Lucida Grande', 'Lucida Sans Unicode', Verdana, sans-serif;line-height:24px;background-color:rgb(247,243,237);">
由Facebook贡献的借鉴Raid思想的HDFS数据存储方式，它可让HDFS通过纠错编解码来实现文件存储的可靠性，以节省存储空间。</p>
<p style="list-style:none;text-indent:2.1em;font-family:'Lucida Grande', 'Lucida Sans Unicode', Verdana, sans-serif;line-height:24px;background-color:rgb(247,243,237);">
<a href="http://wiki.apache.org/hadoop/HDFS-RAID" rel="nofollow" style="color:rgb(0,84,179);text-decoration:none;">http://wiki.apache.org/hadoop/HDFS-RAID</a></p>
<p style="list-style:none;text-indent:2.1em;font-family:'Lucida Grande', 'Lucida Sans Unicode', Verdana, sans-serif;line-height:24px;background-color:rgb(247,243,237);">
<a href="http://blog.csdn.net/nexus/article/details/7567449" rel="nofollow" style="color:rgb(0,84,179);text-decoration:none;">http://blog.csdn.net/nexus/article/details/7567449</a></p>
<p style="list-style:none;text-indent:2.1em;font-family:'Lucida Grande', 'Lucida Sans Unicode', Verdana, sans-serif;line-height:24px;background-color:rgb(247,243,237);">
除以上几个工具包外，还包括data-join、dynamic Scheduler等，在此不具体介绍。</p>
<p style="list-style:none;text-indent:2.1em;font-family:'Lucida Grande', 'Lucida Sans Unicode', Verdana, sans-serif;line-height:24px;background-color:rgb(247,243,237);">
<br></p>
<p style="list-style:none;text-indent:2.1em;font-family:'Lucida Grande', 'Lucida Sans Unicode', Verdana, sans-serif;line-height:24px;background-color:rgb(247,243,237);">
</p>
<div style="font-family:'Lucida Grande', 'Lucida Sans Unicode', Verdana, sans-serif;line-height:24px;font-style:italic;background-color:rgb(247,243,237);">
<p style="list-style:none;font-style:normal;text-indent:2.1em;">
<span>本文链接地址:</span> <a href="http://dongxicheng.org/mapreduce/hadoop-contrib/" rel="nofollow" style="color:rgb(0,84,179);text-decoration:none;">http://dongxicheng.org/mapreduce/hadoop-contrib/</a></p>
</div>
<p></p>
<p style="list-style:none;text-indent:2.1em;font-family:'Lucida Grande', 'Lucida Sans Unicode', Verdana, sans-serif;line-height:24px;background-color:rgb(247,243,237);">
<span>作者：<a href="http://dongxicheng.org/about/" rel="nofollow" style="color:rgb(0,84,179);text-decoration:none;"><span style="color:rgb(255,0,0);"><u>Dong</u></span></a></span>，作者介绍：<a href="http://dongxicheng.org/about/" rel="nofollow" style="color:rgb(0,84,179);text-decoration:none;">http://dongxicheng.org/about/</a></p>
<br>            </div>
                </div>