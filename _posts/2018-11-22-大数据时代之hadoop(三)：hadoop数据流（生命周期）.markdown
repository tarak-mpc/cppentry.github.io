---
layout:     post
title:      大数据时代之hadoop(三)：hadoop数据流（生命周期）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div class="article_title" style="font-size:20px;line-height:30px;font-family:'Microsoft YaHei';">
<span class="ico ico_type_Original" style="display:inline-block;width:19px;vertical-align:middle;"></span> 
<h1 style="display:inline;font-weight:normal;font-size:20px;vertical-align:middle;">
<span class="link_title"><a href="http://blog.csdn.net/chaofanwei/article/details/39695743" rel="nofollow" style="color:rgb(0,0,0);text-decoration:none;"><span style="color:#FF0000;">[置顶]</span> 大数据时代之hadoop(三)：hadoop数据流（生命周期）</a></span></h1>
</div>
<div class="article_manage" style="color:rgb(153,153,153);line-height:24px;font-family:Arial;text-align:right;">
<span class="link_categories">分类： <a href="http://blog.csdn.net/MINEZHANGHAO/article/category/2593909" rel="nofollow" style="color:rgb(202,0,0);text-decoration:none;">hadoop</a> <a href="http://blog.csdn.net/MINEZHANGHAO/article/category/2582075" rel="nofollow" style="color:rgb(202,0,0);text-decoration:none;">大数据</a></span><span class="link_postdate">2014-10-15
 09:38</span> <span class="link_view" title="阅读次数">828人阅读</span> <span class="link_comments" title="评论次数"><a href="http://blog.csdn.net/chaofanwei/article/details/39695743#comments" rel="nofollow" style="color:rgb(202,0,0);text-decoration:none;">评论</a>(0)</span> <span class="link_collect"><a title="收藏" style="color:rgb(202,0,0);">收藏</a></span> <span class="link_report"><a href="http://blog.csdn.net/chaofanwei/article/details/39695743#report" rel="nofollow" title="举报" style="color:rgb(202,0,0);text-decoration:none;">举报</a></span></div>
<div class="tag2box" style="font-family:Arial, Console, Verdana, 'Courier New';">
<a href="http://www.csdn.net/tag/hadoop" rel="nofollow" style="color:rgb(202,0,0);text-decoration:none;display:inline-block;line-height:12px;border:1px solid rgb(238,238,238);background-color:rgb(238,238,238);">hadoop</a><a href="http://www.csdn.net/tag/%E6%95%B0%E6%8D%AE%E6%B5%81" rel="nofollow" style="color:rgb(202,0,0);text-decoration:none;display:inline-block;line-height:12px;border:1px solid rgb(238,238,238);background-color:rgb(238,238,238);">数据流</a><a href="http://www.csdn.net/tag/%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F" rel="nofollow" style="color:rgb(202,0,0);text-decoration:none;display:inline-block;line-height:12px;border:1px solid rgb(238,238,238);background-color:rgb(238,238,238);">生命周期</a></div>
<div style="font-family:Arial, Console, Verdana, 'Courier New';clear:both;"></div>
<div style="font-family:Arial, Console, Verdana, 'Courier New';border:1px solid rgb(204,204,204);background-color:rgb(238,238,238);min-width:200px;">
<p style="text-align:right;">
<span>目录<a href="http://blog.csdn.net/chaofanwei/article/details/39695743#" rel="nofollow" title="系统根据文章中H1到H6标签自动生成文章目录" style="color:rgb(202,0,0);text-decoration:none;">(?)</a></span><a href="http://blog.csdn.net/chaofanwei/article/details/39695743#" rel="nofollow" title="展开" style="color:rgb(202,0,0);text-decoration:none;">[+]</a></p>
</div>
<div style="font-family:Arial, Console, Verdana, 'Courier New';clear:both;"></div>
<div id="article_content" class="article_content" style="font-size:14px;line-height:26px;font-family:Arial;">
<p>
 </p>
<p>
<span class="link_title"><a href="http://blog.csdn.net/chaofanwei/article/details/39553621" rel="nofollow" style="color:rgb(202,0,0);text-decoration:none;">大数据时代之hadoop(一)：hadoop安装</a></span></p>
<p>
<span class="link_title"><a href="http://blog.csdn.net/chaofanwei/article/details/39641361" rel="nofollow" style="color:rgb(202,0,0);text-decoration:none;">大数据时代之hadoop(二)：hadoop脚本解析</a></span></p>
<p>
<span class="link_title"></span><br>
 </p>
<p>
<br></p>
<p>
<span style="font-size:18px;">       了解hadoop，首先就需要先了解hadoop的数据流，就像了解servlet的生命周期似的。<strong>hadoop是一个分布式存储（hdfs）和分布式计算框架（mapreduce）</strong>，但是hadoop也有一个很重要的特性：<strong>hadoop会将mapreduce计算移动到存储有部分数据的各台机器上</strong>。</span><br></p>
<p>
<span style="font-size:18px;"><img alt="" src="https://img-blog.csdn.net/20140930144014783?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2hhb2ZhbndlaQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" style="border:none;"><br></span></p>
<p>
<br></p>
<h1><a name="t0" style="color:rgb(202,0,0);"></a><span style="font-size:18px;">术语</span></h1>
<div><span style="font-size:18px;"><br></span></div>
<div><span style="font-size:18px;">        MapReduce 作业（job）是客户端需要执行的一个工作单元：它包括输入数据、mapreduce程序和配置信息。hadoop将作业分成若干个小任务（task）来执行，其中包括两类任务：<strong>map任务和reduce任务</strong>。<br><br><br>
       有两类节点控制着作业执行过程：<strong>一个jobtracker及一系列tasktracker</strong>。jobtracker通过调度tasktracker上运行的任务，来协调所有运行在系统上的作业。tasktracker在运行任务的同时将运行进度报告发送给jobtracker，jobtracker由此记录每项作业任务的整体进度情况。如果其中一个任务失败，jobtracker可以在另外一个tasktracker节点上重新调度该任务。<br></span></div>
<div><br></div>
<h1><a name="t1" style="color:rgb(202,0,0);"></a><span style="font-size:18px;">输入</span></h1>
<div><span style="font-size:18px;"><br></span></div>
<div><span style="font-size:18px;">        hadoop将mapreduce的输入数据划分成等长的小数据块，称为<strong>输入分片（input split）或简称分片</strong>。<strong><span style="color:rgb(255,102,102);">hadoop为每个分片构建一个map任务</span></strong>，并由该任务来运行用户自定义的map函数从而处理分片中的每条记录。</span></div>
<div><span style="font-size:18px;"><br>
       对于大多数作业来说，<strong><span style="color:rgb(204,0,0);">一个合理的分片大小趋向于HDFS的一个块的大小，默认是64M</span></strong>，不过可以针对集群调整这个默认值。分片的大小一定要根据运行的任务来定，如果分片过小，那么管理分片的总时间和构建map任务的总时间将决定着作业的整个执行时间。<br><br>
       hadoop在存储有输入数据的节点上运行map任务，可以获得最佳性能，这就是所谓的<strong>数据本地化优化</strong>。因为块是hdfs存储数据的最小单元，每个块可以在多个节点上同时存在（备份），一个文件被分成的各个块被随机分部在多个节点上，因此如果一个map任务的输入分片跨越多个数据块，那么基本上没有一个节点能够恰好同时存在这几个连续的数据块，那么map任务就需要首先通过网络将不存在于此节点上的数据块远程复制到本节点上再运行map函数，那么这种任务显然效率非常低。<br></span></div>
<div><span style="font-size:18px;"><br></span></div>
<div><span style="font-size:18px;"><br></span></div>
<h1><a name="t2" style="color:rgb(202,0,0);"></a><span style="font-size:18px;">输出</span></h1>
<div><span style="font-size:18px;">      <strong><span style="color:rgb(51,51,255);"> map任务将其输出写入到本地磁盘，而非HDFS</span></strong>。这是因为map的输出是中间结果：该中间结果有reduce任务处理后才产生最终结果（保存在hdfs中）。而一旦作业完成，map的输出结果可以被删除。</span></div>
<div><span style="font-size:18px;"><br>
    reduce任务并不具备数据本地化优势：单个reduce任务的输入通常来自于所有的mapper任务的输出。<strong><span style="color:rgb(51,51,255);">reduce任务的输出通常存储于HDFS中以实现可靠存储。</span></strong><br></span></div>
<div><span style="font-size:18px;"><br></span></div>
<div><span style="font-size:18px;"><br></span></div>
<h1><a name="t3" style="color:rgb(202,0,0);"></a><span style="font-size:18px;">数据流</span></h1>
<div><span style="font-size:18px;"><br></span></div>
<div><span style="font-size:18px;">        作业根据设置的reduce任务的个数不同，数据流也不同，但大同小异。reduce任务的数量并非由输入数据的大小决定的，而是可以通过手动配置指定的。<br></span></div>
<div><span style="font-size:18px;"><br></span></div>
<h2><a name="t4" style="color:rgb(202,0,0);"></a><span style="font-size:18px;">单个reduce任务</span></h2>
<div><span style="font-size:18px;"><img alt="" src="https://img-blog.csdn.net/20140930144302608?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2hhb2ZhbndlaQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" style="border:none;"><br></span></div>
<div><span style="font-size:18px;"><br></span></div>
<div><span style="font-size:18px;"><br></span></div>
<h2><a name="t5" style="color:rgb(202,0,0);"></a><span style="font-size:18px;">多个reduce任务</span></h2>
<div><span style="font-size:18px;"><br></span></div>
<div><span style="font-size:18px;">         如果是多个reduce任务的话，则<strong>每个map任务都会对其输出进行分区（partition），即为每个reduce任务创建一个分区</strong>。分区有用户定义的分区函数控制，默认的分区器（partitioner） 通过哈希函数来分区。<br>
         map任务和reduce任务之间的数据流称为<strong>shuffle（混洗）</strong>。<br></span></div>
<div><span style="font-size:18px;"><br></span></div>
<div><span style="font-size:18px;"><img alt="" src="https://img-blog.csdn.net/20140930144316446?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2hhb2ZhbndlaQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" style="border:none;"><br></span></div>
<div><span style="font-size:18px;"><br></span></div>
<h2><a name="t6" style="color:rgb(202,0,0);"></a><span style="font-size:18px;">没有reduce任务</span></h2>
<div><span style="font-size:18px;">       当然也可能出现不需要执行reduce任务的情况，即数据可以完全的并行。<br></span></div>
<div><span style="font-size:18px;"><br></span></div>
<div><span style="font-size:18px;"><img alt="" src="https://img-blog.csdn.net/20140930144154875?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2hhb2ZhbndlaQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" style="border:none;"><br></span></div>
<div><span style="font-size:18px;"><br></span></div>
<div><br></div>
<h2><a name="t7" style="color:rgb(202,0,0);"></a><span style="font-size:18px;">combiner（合并函数）</span></h2>
<div><span style="font-size:18px;">        顺便在这说下combiner吧，hadoop运行用户针对map任务的输出指定一个合并函数，合并函数的输出作为reduce函数的输入。其实合并函数就是一个优化方案，说白了就是在map任务执行后在本机先执行合并函数（通常就是reduce函数的拷贝），减少网络传输量。<br></span></div>
</div>
            </div>
                </div>