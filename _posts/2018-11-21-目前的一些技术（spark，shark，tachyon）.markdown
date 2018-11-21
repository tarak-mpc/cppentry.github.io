---
layout:     post
title:      目前的一些技术（spark，shark，tachyon）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;"><span></span>        Spark是一个高效的分布式计算系统</span><span style="color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">，</span><span style="color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">相比Hadoop，它在性能上比Hadoop要高100倍。Spark提供比Hadoop更上层的API，同样的算法在Spark中实现往往只有Hadoop的1/10或者1/100的长度。</span></p>
<p><span style="color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;"><span style="color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;"><span></span> 
       Shark类似“SQL on Spark” or “Hive on Spark”，是一个在Spark上数据仓库的实现，在兼容Hive的情况下，性能最高可以达到Hive的一百倍。</span><span style="color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;"> </span><br></span></p>
<p><span style="color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;"><span style="color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;"><span style="color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;"><span></span> 
       Tachyon是一个高容错的分布式文件系统，允许文件以内存的速度在集群框架中进行可靠的共享，类似Spark和 MapReduce。通过利用lineage信息，积极地使用内存，Tachyon的吞吐量要比HDFS高300多倍。Tachyon都是在内存中处理缓存文件，并且让不同的 Jobs/Queries以及框架都能内存的速度来访问缓存文件。</span><br></span></span></p>
<p><span><span></span><span style="color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">AMPLab正在开发一个大数据的处理平台叫做Berkeley Data Analytics Stack (BDAS)，其中包括了Spark,
 Shark等项目。</span></span>   </p>
<p>            Sparrow<span style="color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">一个低延迟计算集群调度系统。</span></p>
<p><span><span style="color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;"><span></span></span></span></p>
            </div>
                </div>