---
layout:     post
title:      大数据时代之hadoop(五)：hadoop 分布式计算框架（MapReduce）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div class="article_title" style="font-size:20px;line-height:30px;font-family:'Microsoft YaHei';">
<span class="ico ico_type_Original" style="display:inline-block;width:19px;vertical-align:middle;"></span> 
<h1 style="display:inline;font-weight:normal;font-size:20px;vertical-align:middle;">
<span class="link_title"><a href="http://blog.csdn.net/chaofanwei/article/details/40581887" rel="nofollow" style="color:rgb(0,0,0);text-decoration:none;"><span style="color:#FF0000;">[置顶]</span> 大数据时代之hadoop(五)：hadoop 分布式计算框架（MapReduce）</a></span></h1>
</div>
<div class="article_manage" style="color:rgb(153,153,153);line-height:24px;font-family:Arial;text-align:right;">
<span class="link_categories">分类： <a href="http://blog.csdn.net/MINEZHANGHAO/article/category/2593909" rel="nofollow" style="color:rgb(202,0,0);text-decoration:none;">hadoop</a> <a href="http://blog.csdn.net/MINEZHANGHAO/article/category/2582075" rel="nofollow" style="color:rgb(202,0,0);text-decoration:none;">大数据</a></span><span class="link_postdate">2014-11-03
 10:33</span> <span class="link_view" title="阅读次数">555人阅读</span> <span class="link_comments" title="评论次数"><a href="http://blog.csdn.net/chaofanwei/article/details/40581887#comments" rel="nofollow" style="color:rgb(202,0,0);text-decoration:none;">评论</a>(1)</span> <span class="link_collect"><a title="收藏" style="color:rgb(202,0,0);">收藏</a></span> <span class="link_report"><a href="http://blog.csdn.net/chaofanwei/article/details/40581887#report" rel="nofollow" title="举报" style="color:rgb(202,0,0);text-decoration:none;">举报</a></span></div>
<div class="tag2box" style="font-family:Arial, Console, Verdana, 'Courier New';">
<a href="http://www.csdn.net/tag/hadoop" rel="nofollow" style="color:rgb(202,0,0);text-decoration:none;display:inline-block;line-height:12px;border:1px solid rgb(238,238,238);background-color:rgb(238,238,238);">hadoop</a><a href="http://www.csdn.net/tag/%E5%88%86%E5%B8%83%E5%BC%8F%E8%AE%A1%E7%AE%97" rel="nofollow" style="color:rgb(202,0,0);text-decoration:none;display:inline-block;line-height:12px;border:1px solid rgb(238,238,238);background-color:rgb(238,238,238);">分布式计算</a><a href="http://www.csdn.net/tag/mapreduce" rel="nofollow" style="color:rgb(202,0,0);text-decoration:none;display:inline-block;line-height:12px;border:1px solid rgb(238,238,238);background-color:rgb(238,238,238);">mapreduce</a><a href="http://www.csdn.net/tag/%E8%BE%93%E5%85%A5%E8%BE%93%E5%87%BA%E6%A0%BC%E5%BC%8F" rel="nofollow" style="color:rgb(202,0,0);text-decoration:none;display:inline-block;line-height:12px;border:1px solid rgb(238,238,238);background-color:rgb(238,238,238);">输入输出格式</a><a href="http://www.csdn.net/tag/shuffle" rel="nofollow" style="color:rgb(202,0,0);text-decoration:none;display:inline-block;line-height:12px;border:1px solid rgb(238,238,238);background-color:rgb(238,238,238);">shuffle</a></div>
<div style="font-family:Arial, Console, Verdana, 'Courier New';clear:both;"></div>
<div style="font-family:Arial, Console, Verdana, 'Courier New';border:1px solid rgb(204,204,204);background-color:rgb(238,238,238);min-width:200px;">
<p style="text-align:right;">
<span>目录<a href="http://blog.csdn.net/chaofanwei/article/details/40581887#" rel="nofollow" title="系统根据文章中H1到H6标签自动生成文章目录" style="color:rgb(202,0,0);text-decoration:none;">(?)</a></span><a href="http://blog.csdn.net/chaofanwei/article/details/40581887#" rel="nofollow" title="展开" style="color:rgb(202,0,0);text-decoration:none;">[+]</a></p>
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
<span class="link_title"><a href="http://blog.csdn.net/chaofanwei/article/details/39695743" rel="nofollow" style="color:rgb(202,0,0);text-decoration:none;">大数据时代之hadoop(三)：hadoop数据流（生命周期）</a></span></p>
<p>
<span class="link_title"><a href="http://blog.csdn.net/chaofanwei/article/details/40209527" rel="nofollow" style="color:rgb(202,0,0);text-decoration:none;">大数据时代之hadoop(四)：hadoop 分布式文件系统（HDFS)</a></span></p>
<p>
</p>
<p>
 </p>
<p>
<span style="font-size:18px;">        hadoop的核心分为两块，一是<strong>分布式存储系统-hdfs</strong>，这个我已经在上一章节大致讲了一下，另一个就是<strong>hadoop的计算框架-mapreduce</strong>。</span></p>
<p>
<span style="font-size:18px;">     </span></p>
<p>
<span style="font-size:18px;">        <strong>mapreduce其实就是一个移动式的基于key-value形式的分布式计算框架</strong>。</span></p>
<p>
<span style="font-size:18px;"></span> </p>
<p>
<span style="font-size:18px;">        其计算分为两个阶段，map阶段和reduce阶段，都是对数据的处理，由于其入门非常简单，但是若想理解其中各个环节及实现细节还是有一定程度的困难，因此我计划在本文中只是挑几个mapreduce的核心来进行分析讲解。</span></p>
<p>
<span style="font-size:18px;"></span><br>
 </p>
<h1><a name="t0" style="color:rgb(202,0,0);"></a>1、MapReduce驱动程序默认值</h1>
<p>
 </p>
<p>
<span style="font-size:18px;">        编写mapreduce程序容易入手的其中一个原因就在于它提供了一些了的默认值，而这些默认值刚好就是供开发环境设置而设定的。虽然容易入手，但还是的理解mapreduce的精髓，因为它是mapreduce的引擎，只有理解了mapreduce的核心，当你在编写mapreduce程序的时候，你所编写的程序才是最终稳重的，想要的程序。废话少说，见下面代码：</span></p>
<div class="dp-highlighter bg_java" style="font-family:Consolas, 'Courier New', Courier, mono, serif;font-size:12px;background-color:rgb(231,229,220);overflow:auto;">
<div class="bar">
<div class="tools" style="font-size:9px;line-height:normal;font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;color:#C0C0C0;background-color:rgb(248,248,248);border-left-width:3px;border-left-style:solid;border-left-color:rgb(153,153,153);">
<strong>[java]</strong> <a href="http://blog.csdn.net/chaofanwei/article/details/40581887#" rel="nofollow" class="ViewSource" title="view plain" style="color:rgb(160,160,160);text-decoration:none;background-color:inherit;border:none;font-size:9px;">view
 plain</a><a href="http://blog.csdn.net/chaofanwei/article/details/40581887#" rel="nofollow" class="CopyToClipboard" title="copy" style="color:rgb(160,160,160);text-decoration:none;background-color:inherit;border:none;font-size:9px;">copy</a><a href="http://blog.csdn.net/chaofanwei/article/details/40581887#" rel="nofollow" class="PrintSource" title="print" style="color:rgb(160,160,160);text-decoration:none;background-color:inherit;border:none;font-size:9px;">print</a><a href="http://blog.csdn.net/chaofanwei/article/details/40581887#" rel="nofollow" class="About" title="?" style="color:rgb(160,160,160);text-decoration:none;background-color:inherit;border:none;font-size:9px;">?</a><a href="https://code.csdn.net/snippets/505858" rel="nofollow" title="在CODE上查看代码片" style="color:rgb(160,160,160);text-decoration:none;background-color:inherit;border:none;font-size:9px;"><img src="https://code.csdn.net/assets/CODE_ico.png" width="12" height="12" alt="在CODE上查看代码片" style="border:none;"></a><a href="https://code.csdn.net/snippets/505858/fork" rel="nofollow" title="派生到我的代码片" style="color:rgb(160,160,160);text-decoration:none;background-color:inherit;border:none;font-size:9px;"><img src="https://code.csdn.net/assets/ico_fork.svg" width="12" height="12" alt="派生到我的代码片" style="border:none;"></a>
<div style="width:29px;z-index:99;">
</div>
</div>
</div>
<ol start="1" class="dp-j" style="border:none;background-color:rgb(255,255,255);color:rgb(92,92,92);"><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span class="keyword" style="border:none;color:#0000FF;background-color:inherit;font-weight:bold;">public</span><span style="border:none;background-color:inherit;"> </span><span class="keyword" style="border:none;color:#0000FF;background-color:inherit;font-weight:bold;">int</span><span style="border:none;background-color:inherit;"> run(String[] args) </span><span class="keyword" style="border:none;color:#0000FF;background-color:inherit;font-weight:bold;">throws</span><span style="border:none;background-color:inherit;"> IOException {  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:rgb(85,85,85);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">    JobConf conf = <span class="keyword" style="border:none;color:#0000FF;background-color:inherit;font-weight:bold;">new</span><span style="border:none;background-color:inherit;"> JobConf();  </span></span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">      </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:rgb(85,85,85);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">    <span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;">/**</span> </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:rgb(85,85,85);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;">     *默认的输入格式，即mapper程序要处理的数据的格式，hadoop支持很多种输入格式，下面会详细讲解，</span> </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;">     *但TextInputFormat是最常使用的（即普通文本文件，key为LongWritable-文件中每行的开始偏移量，value为Text-文本行）。</span> </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:rgb(85,85,85);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;">     **/</span><span style="border:none;background-color:inherit;">  </span></span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">    conf.setInputFormat(org.apache.hadoop.mapred.TextInputFormat.<span class="keyword" style="border:none;color:#0000FF;background-color:inherit;font-weight:bold;">class</span><span style="border:none;background-color:inherit;">);  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:rgb(85,85,85);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">      </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">    <span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;">/**</span> </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:rgb(85,85,85);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;">     *真正的map任务数量取决于输入文件的大小以及文件块的大小</span> </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;">     **/</span><span style="border:none;background-color:inherit;">  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:rgb(85,85,85);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">    conf.setNumMapTasks(<span class="number" style="border:none;color:rgb(192,0,0);background-color:inherit;">1</span><span style="border:none;background-color:inherit;">);  </span></span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">      </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:rgb(85,85,85);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">    <span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;">/**</span> </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;">     *默认的mapclass，如果我们不指定自己的mapper class时，就使用这个IdentityMapper 类</span> </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:rgb(85,85,85);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;">     **/</span><span style="border:none;background-color:inherit;">  </span></span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">    conf.setMapperClass(org.apache.hadoop.mapred.lib.IdentityMapper.<span class="keyword" style="border:none;color:#0000FF;background-color:inherit;font-weight:bold;">class</span><span style="border:none;background-color:inherit;">);  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:rgb(85,85,85);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">      </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">    <span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;">/**</span> </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:rgb(85,85,85);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;">     * map 任务是由MapRunner负责运行的，MapRunner是MapRunnable的默认实现，它顺序的为每一条记录调用一次Mapper的map()方法，详解代码  --重点</span> </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;">     */</span><span style="border:none;background-color:inherit;">  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:rgb(85,85,85);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">    conf.setMapRunnerClass(org.apache.hadoop.mapred.MapRunner.<span class="keyword" style="border:none;color:#0000FF;background-color:inherit;font-weight:bold;">class</span><span style="border:none;background-color:inherit;">);  </span></span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">      </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:rgb(85,85,85);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">    <span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;">/**</span> </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;">     * map任务输出结果的key 和value格式</span> </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:rgb(85,85,85);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;">     */</span><span style="border:none;background-color:inherit;">  </span></span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">    conf.setMapOutputKeyClass(org.apache.hadoop.io.LongWritable.<span class="keyword" style="border:none;color:#0000FF;background-color:inherit;font-weight:bold;">class</span><span style="border:none;background-color:inherit;">);  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:rgb(85,85,85);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">    conf.setMapOutputValueClass(org.apache.hadoop.io.Text.<span class="keyword" style="border:none;color:#0000FF;background-color:inherit;font-weight:bold;">class</span><span style="border:none;background-color:inherit;">);  </span></span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">      </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:rgb(85,85,85);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">    <span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;">/**</span> </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;">     * HashPartitioner 是默认的分区实现，它对map 任务运行后的数据进行分区，即把结果数据划分成多个块（每个分区对应一个reduce任务）。</span> </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:rgb(85,85,85);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;">     * HashPartitioner是对每条 记录的键进行哈希操作以决定该记录应该属于哪个分区。</span> </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;">     * </span> </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:rgb(85,85,85);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;">     */</span><span style="border:none;background-color:inherit;">  </span></span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">    conf.setPartitionerClass(org.apache.hadoop.mapred.lib.HashPartitioner.<span class="keyword" style="border:none;color:#0000FF;background-color:inherit;font-weight:bold;">class</span><span style="border:none;background-color:inherit;">);  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:rgb(85,85,85);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">      </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">    <span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;">/**</span> </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:rgb(85,85,85);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;">     * 设置reduce任务个数</span> </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;">     */</span><span style="border:none;background-color:inherit;">  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:rgb(85,85,85);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">    conf.setNumReduceTasks(<span class="number" style="border:none;color:rgb(192,0,0);background-color:inherit;">1</span><span style="border:none;background-color:inherit;">);  </span></span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">      </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:rgb(85,85,85);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">    <span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;">/**</span> </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;">    *默认的reduce class，如果我们不指定自己的reduce class时，就使用这个IdentityReducer 类</span> </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:rgb(85,85,85);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;">    **/</span><span style="border:none;background-color:inherit;">  </span></span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">    conf.setReducerClass(org.apache.hadoop.mapred.lib.IdentityReducer.<span class="keyword" style="border:none;color:#0000FF;background-color:inherit;font-weight:bold;">class</span><span style="border:none;background-color:inherit;">);  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:rgb(85,85,85);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">    <span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;">/**</span> </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:rgb(85,85,85);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;">     * 任务最终输出结果的key 和value格式</span> </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;">     */</span><span style="border:none;background-color:inherit;">  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:rgb(85,85,85);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">    conf.setOutputKeyClass(org.apache.hadoop.io.LongWritable.<span class="keyword" style="border:none;color:#0000FF;background-color:inherit;font-weight:bold;">class</span><span style="border:none;background-color:inherit;">);  </span></span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">    conf.setOutputValueClass(org.apache.hadoop.io.Text.<span class="keyword" style="border:none;color:#0000FF;background-color:inherit;font-weight:bold;">class</span><span style="border:none;background-color:inherit;">);  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:rgb(85,85,85);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">    <span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;">/**</span> </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:rgb(85,85,85);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;">     * 最终输出到文本文件类型中</span> </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;">     */</span><span style="border:none;background-color:inherit;">  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:rgb(85,85,85);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">    conf.setOutputFormat(org.apache.hadoop.mapred.TextOutputFormat.<span class="keyword" style="border:none;color:#0000FF;background-color:inherit;font-weight:bold;">class</span><span style="border:none;background-color:inherit;">);</span><span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;">/*]*/</span><span style="border:none;background-color:inherit;">  </span></span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">      </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:rgb(85,85,85);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">    JobClient.runJob(conf);  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">    <span class="keyword" style="border:none;color:#0000FF;background-color:inherit;font-weight:bold;">return</span><span style="border:none;background-color:inherit;"> </span><span class="number" style="border:none;color:rgb(192,0,0);background-color:inherit;">0</span><span style="border:none;background-color:inherit;">;  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:rgb(85,85,85);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">  }  </span></li></ol></div>
<p>
 </p>
<p>
<span style="font-size:18px;">我要说的大部分都包含在了代码的注释里面，除此之外，还有一点：<strong>由于java的泛型机制有很多限制：类型擦除导致运行过程中类型信息并非一直可见，所以hadoop需要明确设定map，reduce输入和结果类型</strong>。</span><br></p>
<p>
 </p>
<p>
<span style="font-size:18px;">上面比较重要的就是MapRunner这个类，它是map任务运行的引擎，默认实现如下：</span></p>
<span style="font-size:18px;"></span>
<div class="dp-highlighter bg_java" style="font-family:Consolas, 'Courier New', Courier, mono, serif;font-size:12px;background-color:rgb(231,229,220);overflow:auto;">
<div class="bar">
<div class="tools" style="font-size:9px;line-height:normal;font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;color:#C0C0C0;background-color:rgb(248,248,248);border-left-width:3px;border-left-style:solid;border-left-color:rgb(153,153,153);">
<strong>[java]</strong> <a href="http://blog.csdn.net/chaofanwei/article/details/40581887#" rel="nofollow" class="ViewSource" title="view plain" style="color:rgb(160,160,160);text-decoration:none;background-color:inherit;border:none;font-size:9px;">view
 plain</a><a href="http://blog.csdn.net/chaofanwei/article/details/40581887#" rel="nofollow" class="CopyToClipboard" title="copy" style="color:rgb(160,160,160);text-decoration:none;background-color:inherit;border:none;font-size:9px;">copy</a><a href="http://blog.csdn.net/chaofanwei/article/details/40581887#" rel="nofollow" class="PrintSource" title="print" style="color:rgb(160,160,160);text-decoration:none;background-color:inherit;border:none;font-size:9px;">print</a><a href="http://blog.csdn.net/chaofanwei/article/details/40581887#" rel="nofollow" class="About" title="?" style="color:rgb(160,160,160);text-decoration:none;background-color:inherit;border:none;font-size:9px;">?</a><a href="https://code.csdn.net/snippets/505858" rel="nofollow" title="在CODE上查看代码片" style="color:rgb(160,160,160);text-decoration:none;background-color:inherit;border:none;font-size:9px;"><img src="https://code.csdn.net/assets/CODE_ico.png" width="12" height="12" alt="在CODE上查看代码片" style="border:none;"></a><a href="https://code.csdn.net/snippets/505858/fork" rel="nofollow" title="派生到我的代码片" style="color:rgb(160,160,160);text-decoration:none;background-color:inherit;border:none;font-size:9px;"><img src="https://code.csdn.net/assets/ico_fork.svg" width="12" height="12" alt="派生到我的代码片" style="border:none;"></a>
<div style="width:29px;z-index:99;">
</div>
</div>
</div>
<ol start="1" class="dp-j" style="border:none;background-color:rgb(255,255,255);color:rgb(92,92,92);"><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span class="keyword" style="border:none;color:#0000FF;background-color:inherit;font-weight:bold;">public</span><span style="border:none;background-color:inherit;"> </span><span class="keyword" style="border:none;color:#0000FF;background-color:inherit;font-weight:bold;">class</span><span style="border:none;background-color:inherit;"> MapRunner&lt;K1, V1, K2, V2&gt;  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:rgb(85,85,85);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">    <span class="keyword" style="border:none;color:#0000FF;background-color:inherit;font-weight:bold;">implements</span><span style="border:none;background-color:inherit;"> MapRunnable&lt;K1, V1, K2, V2&gt; {  </span></span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">    </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:rgb(85,85,85);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">  <span class="keyword" style="border:none;color:#0000FF;background-color:inherit;font-weight:bold;">private</span><span style="border:none;background-color:inherit;"> Mapper&lt;K1, V1, K2, V2&gt; mapper;  </span></span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">  <span class="keyword" style="border:none;color:#0000FF;background-color:inherit;font-weight:bold;">private</span><span style="border:none;background-color:inherit;"> </span><span class="keyword" style="border:none;color:#0000FF;background-color:inherit;font-weight:bold;">boolean</span><span style="border:none;background-color:inherit;"> incrProcCount;  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:rgb(85,85,85);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">  <span class="annotation" style="border:none;color:rgb(100,100,100);background-color:inherit;">@SuppressWarnings</span><span style="border:none;background-color:inherit;">(</span><span class="string" style="border:none;color:#FF0000;background-color:inherit;">"unchecked"</span><span style="border:none;background-color:inherit;">)  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:rgb(85,85,85);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">  <span class="keyword" style="border:none;color:#0000FF;background-color:inherit;font-weight:bold;">public</span><span style="border:none;background-color:inherit;"> </span><span class="keyword" style="border:none;color:#0000FF;background-color:inherit;font-weight:bold;">void</span><span style="border:none;background-color:inherit;"> configure(JobConf job) {  </span></span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">  <span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;">//通过反射方式取得map 实例</span><span style="border:none;background-color:inherit;">  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:rgb(85,85,85);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">    <span class="keyword" style="border:none;color:#0000FF;background-color:inherit;font-weight:bold;">this</span><span style="border:none;background-color:inherit;">.mapper = ReflectionUtils.newInstance(job.getMapperClass(), job);  </span></span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">    <span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;">//increment processed counter only if skipping feature is enabled</span><span style="border:none;background-color:inherit;">  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:rgb(85,85,85);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">    <span class="keyword" style="border:none;color:#0000FF;background-color:inherit;font-weight:bold;">this</span><span style="border:none;background-color:inherit;">.incrProcCount = SkipBadRecords.getMapperMaxSkipRecords(job)&gt;</span><span class="number" style="border:none;color:rgb(192,0,0);background-color:inherit;">0</span><span style="border:none;background-color:inherit;"> &amp;&amp;   </span></span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">      SkipBadRecords.getAutoIncrMapperProcCount(job);  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:rgb(85,85,85);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">  }  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:rgb(85,85,85);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">  <span class="keyword" style="border:none;color:#0000FF;background-color:inherit;font-weight:bold;">public</span><span style="border:none;background-color:inherit;"> </span><span class="keyword" style="border:none;color:#0000FF;background-color:inherit;font-weight:bold;">void</span><span style="border:none;background-color:inherit;"> run(RecordReader&lt;K1, V1&gt; input, OutputCollector&lt;K2, V2&gt; output,  </span></span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">                  Reporter reporter)  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:rgb(85,85,85);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">    <span class="keyword" style="border:none;color:#0000FF;background-color:inherit;font-weight:bold;">throws</span><span style="border:none;background-color:inherit;"> IOException {  </span></span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">    <span class="keyword" style="border:none;color:#0000FF;background-color:inherit;font-weight:bold;">try</span><span style="border:none;background-color:inherit;"> {  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:rgb(85,85,85);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">      <span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;">// allocate key &amp; value instances that are re-used for all entries</span><span style="border:none;background-color:inherit;">  </span></span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">      K1 key = input.createKey();  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:rgb(85,85,85);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">      V1 value = input.createValue();  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">        </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:rgb(85,85,85);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">      <span class="keyword" style="border:none;color:#0000FF;background-color:inherit;font-weight:bold;">while</span><span style="border:none;background-color:inherit;"> (input.next(key, value)) {  </span></span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">        <span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;">// map pair to output</span><span style="border:none;background-color:inherit;">  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:rgb(85,85,85);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">    <span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;">//循环调用map函数</span><span style="border:none;background-color:inherit;">  </span></span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">        mapper.map(key, value, output, reporter);  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:rgb(85,85,85);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">        <span class="keyword" style="border:none;color:#0000FF;background-color:inherit;font-weight:bold;">if</span><span style="border:none;background-color:inherit;">(incrProcCount) {  </span></span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">          reporter.incrCounter(SkipBadRecords.COUNTER_GROUP,   </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:rgb(85,85,85);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">              SkipBadRecords.COUNTER_MAP_PROCESSED_RECORDS, <span class="number" style="border:none;color:rgb(192,0,0);background-color:inherit;">1</span><span style="border:none;background-color:inherit;">);  </span></span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">        }  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:rgb(85,85,85);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">      }  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">    } <span class="keyword" style="border:none;color:#0000FF;background-color:inherit;font-weight:bold;">finally</span><span style="border:none;background-color:inherit;"> {  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:rgb(85,85,85);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">      mapper.close();  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">    }  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:rgb(85,85,85);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">  }  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:rgb(85,85,85);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">  <span class="keyword" style="border:none;color:#0000FF;background-color:inherit;font-weight:bold;">protected</span><span style="border:none;background-color:inherit;"> Mapper&lt;K1, V1, K2, V2&gt; getMapper() {  </span></span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">    <span class="keyword" style="border:none;color:#0000FF;background-color:inherit;font-weight:bold;">return</span><span style="border:none;background-color:inherit;"> mapper;  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:rgb(85,85,85);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">  }  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">}  </span></li></ol></div>
<p>
<br>
 </p>
<p>
<span style="font-size:18px;">要相信，有些时候还是看源码理解的更快！</span></p>
<p>
 </p>
<p>
 </p>
<h1><a name="t1" style="color:rgb(202,0,0);"></a>2、shuffle</h1>
<p>
<span style="font-size:18px;">          shuffle过程其实就是从map的输出到reduce的输入过程中所经历的步骤，堪称mapreduce的“心脏”，分为3个阶段，<strong>map端分区、reduce端复制、reduce排序（合并）阶段。<br></strong></span></p>
<p>
<span style="font-size:18px;"><strong></strong></span> </p>
<h2><a name="t2" style="color:rgb(202,0,0);"></a>2.1、map端分区</h2>
<span style="font-size:18px;"></span>
<p>
         <span style="font-size:18px;">由于在mapreduce计算中，有多个map任务和若干个reduce任何，而且各个任务都可能处于不同的机器里面，所以如何从map任务的输出到reduce的输入是一个难点。</span></p>
<p>
<span style="font-size:18px;"> </span></p>
<p>
<br><span style="font-size:18px;">        map函数在产生输出时，并不是简单的写到磁盘中，而是利用<strong>缓冲的形式写入到内存，并出于效率进行预排序</strong>，过程如下图：</span></p>
<p>
 </p>
<p>
 </p>
<p>
<img alt="" src="https://img-blog.csdn.net/20141029100810641?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2hhb2ZhbndlaQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" style="border:none;"></p>
<p>
 </p>
<p>
       <span style="font-size:18px;">在<strong>写磁盘之前，线程首先根据reduce的个数将输出数据划分成响应的分区（partiton</strong>）。在每个分区中，后台线程按键进行内排序，如果有个一combiner，它会在排序后的输出上运行。<br></span></p>
<p>
<span style="font-size:18px;"> </span></p>
<h2><a name="t3" style="color:rgb(202,0,0);"></a>2.2、reduce端复制阶段</h2>
<p>
<span style="font-size:18px;">    </span></p>
<p>
<span style="font-size:18px;">    由于map任务的输出文件写到了本地磁盘上，并且<strong>划分成reduce个数的分区（每一个reduce需要一个分区</strong>），由于map任务完成的时间可能不同，因此只要一个任务完成，reduce任务就开始复制其输出，这就是reduce任务的复制阶段。如上图所示。</span></p>
<p>
 </p>
<h2><a name="t4" style="color:rgb(202,0,0);"></a>2.3、reduce端排序（合并）阶段</h2>
<p>
<span style="font-size:18px;"> </span></p>
<p>
    <span style="font-size:18px;"> 复制完所有map输出后，reduce任务进入排序阶段（sort phase），这个阶段将合并map输出，维持其顺序排序，如上图所示。</span></p>
<p>
 </p>
<h1><a name="t5" style="color:rgb(202,0,0);"></a><br>
3、输入与输出格式</h1>
<p>
<span style="font-size:18px;">       随着时间的增加，数据的增长也是指数级的增长，且数据的格式也越来越多，对大数据的处理也就越来越困难，为了适应能够处理各种各样的数据，hadoop提供了一系列的<strong>输入和输出格式控制，其目的很简单，就是能够解析各种输入文件，并产生需要的输出格式数据</strong>。</span></p>
<span style="font-size:18px;"></span>
<p>
<br>
       <span style="font-size:18px;">但是不管处理哪种格式的数据，都要与mapreduce结合起来，才能最大化的发挥hadoop的有点。</span></p>
<p>
<span style="font-size:18px;">    这部分也是hadoop的核心啊！</span></p>
<p>
 </p>
<h2><a name="t6" style="color:rgb(202,0,0);"></a>3.1、输入分片与记录</h2>
<p>
 </p>
<p>
<span style="font-size:18px;">        在讲HDFS的时候，说过，<strong>一个输入分片就是由单个map任务处理的输入块</strong>，<strong>一个分片的大小最好与hdfs的块大小相同</strong>。</span></p>
<p>
<span style="font-size:18px;"></span> </p>
<p>
<span style="font-size:18px;">        <strong>每个分片被划分成若干个记录，每个记录就是一个键值对，map一个接一个的处理每条记录</strong>。</span></p>
<span style="font-size:18px;"></span>
<p>
<br>
             <span style="font-size:18px;">在<strong>数据库常见中，一个输入分片可以对应一个表的若干行，而一条记录对应一行（DBInputFormat</strong>）。</span></p>
<p>
<span style="font-size:18px;"><br>
 </span></p>
<p>
<span style="font-size:18px;">        <strong>输入分片在hadoop中表示为InputSplit接口，有InputFormat创建的</strong>。</span></p>
<p>
<br><span style="font-size:18px;">       <strong> InputFormat负责产生输入分片并将他们分割成记录，其只是一个接口，具体任务有具体实现去做的</strong>。</span></p>
<p>
 </p>
<p>
<img alt="" src="https://img-blog.csdn.net/20141029102817646?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2hhb2ZhbndlaQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" style="border:none;"></p>
<p>
 </p>
<h2><a name="t7" style="color:rgb(202,0,0);"></a>3.2、FileInputFormat</h2>
<p>
<span style="font-size:18px;">          <strong> FileInputFormat是所有使用文件作为其数据源的InputFormat实现的基类，它提供了两个功能：<span style="color:rgb(255,0,0);">一个定义哪些文件包含在作业的输入中</span>；<span style="color:rgb(51,102,255);">一个为输入文件产生分片的实现</span>。<span style="color:rgb(255,153,0);">把分片割成基类的作业有其子类实现,<strong>FileInputFormat是个抽象类</strong></span></strong>。</span></p>
<p>
<span style="font-size:18px;"></span> </p>
<p>
<span style="font-size:18px;">   FileInputFormat实现了把文件分区的功能，但它是怎么来实现了呢？需要先说三个参数：<br></span></p>
<p>
<span style="font-size:18px;"></span></p>
<table border="1" cellspacing="0" cellpadding="0"><tbody><tr><td valign="top" style="background-color:rgb(247,150,70);">
<p>
<strong><span style="font-size:14px;"><span style="color:#FFFFFF;">属性名称</span></span></strong></p>
</td>
<td valign="top" style="background-color:rgb(247,150,70);">
<p>
<strong><span style="font-size:14px;"><span style="color:#FFFFFF;">类型</span></span></strong></p>
</td>
<td valign="top" style="background-color:rgb(247,150,70);">
<p>
<strong><span style="font-size:14px;"><span style="color:#FFFFFF;">默认值</span></span></strong></p>
</td>
<td valign="top" style="background-color:rgb(247,150,70);">
<p>
<strong><span style="font-size:14px;"><span style="color:#FFFFFF;">描述</span></span></strong></p>
</td>
</tr><tr><td valign="top" style="background-color:rgb(253,228,208);">
<p>
<strong><span style="font-family:Calibri;font-size:14px;">mapred.min.split.size</span></strong></p>
</td>
<td valign="top" style="background-color:rgb(253,228,208);">
<p>
<span style="font-family:Calibri;font-size:14px;">Int</span></p>
</td>
<td valign="top" style="background-color:rgb(253,228,208);">
<p>
<span style="font-family:Calibri;font-size:14px;">1</span></p>
</td>
<td valign="top" style="background-color:rgb(253,228,208);">
<p>
<span style="font-size:14px;">一个文件分片的最小字节数</span></p>
</td>
</tr><tr><td valign="top">
<p>
<strong><span style="font-family:Calibri;font-size:14px;">mapred.max.split.size</span></strong></p>
</td>
<td valign="top">
<p>
<span style="font-family:Calibri;font-size:14px;">Long</span></p>
</td>
<td valign="top">
<p>
<span style="font-family:Calibri;font-size:14px;">Long.MAX_VALUE</span></p>
</td>
<td valign="top">
<p>
<span style="font-size:14px;">一个文件分片的最大字节数</span></p>
</td>
</tr><tr><td valign="top" style="background-color:rgb(253,228,208);">
<p>
<strong><span style="font-family:Calibri;font-size:14px;">dfs.block.size</span></strong></p>
</td>
<td valign="top" style="background-color:rgb(253,228,208);">
<p>
<span style="font-family:Calibri;font-size:14px;">long</span></p>
</td>
<td valign="top" style="background-color:rgb(253,228,208);">
<p>
<span style="font-family:Calibri;font-size:14px;">64M</span></p>
</td>
<td valign="top" style="background-color:rgb(253,228,208);">
<p>
<span style="font-family:Calibri;font-size:14px;">HDFS</span>中块大小</p>
</td>
</tr></tbody></table><p>
</p>
<p>
<span style="font-size:18px;"> </span></p>
<p>
<span style="font-size:18px;">   分片的大小有一个公式计算（参考FileInputFomat类的computeSplitSize（）方法）</span></p>
<span style="font-size:18px;"></span>
<p>
<br>
                   <span style="font-size:18px;"> </span><span style="font-size:24px;"> <strong><span style="color:rgb(204,0,0);">max（minimumSize,min(maximumSize,blockSize)）</span></strong></span></p>
<strong></strong>
<p>
<br><span style="font-size:18px;"> 默认情况下： minimumSize  &lt;  blockSize &lt; maximumSize</span></p>
<p>
 </p>
<p>
 </p>
<p>
<span style="font-size:18px;">   <strong>FileInputFormat只分割大文件，即文件大小超过块大小的文件</strong>。</span></p>
<p>
<span style="font-size:18px;"><br>
   <strong>FileInputFormat生成的InputSplit是一整个文件（文件太小，未被分区，整个文件当成一个分区，供map任务处理）或该文件的一部分（文件大，被分区）</strong></span><strong>。</strong> <br></p>
<h2><a name="t8" style="color:rgb(202,0,0);"></a>3.3、常用的InputFormat实现</h2>
<p>
 </p>
<span style="font-size:18px;"></span>
<p>
 </p>
<p>
<span style="font-size:18px;"><strong>小文件与CombineFileInputFormat</strong></span></p>
<p>
<strong><span style="font-size:18px;"></span></strong><span style="font-size:18px;"><br>
      虽然hadoop适合处理大文件，但在实际的情况中，大量的小文件处理是少不了的，因此hadoop提供了一个CombineFileInputFormat，它针对小文件而设计的，<strong>它把多个文件打包到一个分片中一般每个mapper可以处理更多的数据</strong>。</span></p>
<p>
 </p>
<p>
<span style="font-size:18px;"></span> </p>
<p>
<span style="font-size:18px;"><strong>TextInputFormat</strong></span></p>
<p>
<span style="font-size:18px;"><strong><br></strong>     <span style="color:rgb(255,153,0);"><strong>hadoop默认的InputFormat，每个记录的键是文件中行的偏移量，值为行内容</strong>。</span></span></p>
<span style="font-size:18px;"></span>
<p>
 </p>
<p>
<br><strong><span style="font-size:18px;">KeyValueInputFormat</span></strong></p>
<strong></strong>
<p>
<br>
     <span style="font-size:18px;"><span style="color:rgb(255,102,0);">适合处理配置文件，文件中行中为key value格式的，如key=value类型的文件  ，key即为行中的key，value即为行中的value</span>。</span></p>
<p>
 </p>
<p>
<br><strong><span style="font-size:18px;">NLineInputFormat</span></strong></p>
<strong></strong>
<p>
<br><span style="font-size:18px;">     也是为处理文本文件而开发的，它的特点是为每个map任务收到<strong>固定行数的输入</strong>，其他与TextInputFormat相似。</span></p>
<p>
<span style="font-size:18px;"> </span></p>
<p>
<br><strong><span style="font-size:18px;">SequenceFileInputFormat（二进制输入）</span></strong></p>
<p>
<strong></strong><br><span style="font-size:18px;">    <span style="color:rgb(204,0,0);"> hadoop的顺序文件格式存储格式存储二进制的键值对序列，由于顺序文件里面存储的就是map结构的数据，所以刚好可以有SequenceFileInputFormat 来进行处理。</span></span></p>
<p>
<span style="color:rgb(204,0,0);"></span> </p>
<p>
<br><span style="font-size:18px;"><strong>DBInputFormat</strong></span></p>
<span style="font-size:18px;"><strong></strong></span>
<p>
<br><span style="font-size:18px;">     顾名思义，用于使用jdbc从关系数据库中读取数据。</span></p>
<p>
 </p>
<p>
 </p>
<p>
<span style="font-size:18px;"><strong>多种输入</strong></span></p>
<span style="font-size:18px;"><strong></strong></span>
<p>
<br><span style="font-size:18px;">        <strong>MultipleInputs类可以用来处理多种输入格式的数据</strong>，如输入数据中包含文本类型和二进制类型的，这个时候就可以用 MultipleInputs来指定某个文件有哪种输入类型和哪个map函数来解析。</span></p>
<p>
 </p>
<h2><a name="t9" style="color:rgb(202,0,0);"></a>3.4、输出格式</h2>
<p>
<span style="font-size:18px;">     既然有输入格式，就有输出格式，与输入格式对应。</span></p>
<p>
<span style="font-size:18px;"><br>
    <strong> 默认的输出格式是TextOutputFormat，它把记录写成文本行，键值对可以是任意类型， 键值对中间默认用<span style="color:rgb(255,0,0);">制表符分割</span></strong>。</span><br><br></p>
<p>
<img alt="" src="https://img-blog.csdn.net/20141029102933463?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2hhb2ZhbndlaQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" style="border:none;"><br></p>
<p>
 </p>
<p>
 </p>
<h2><a name="t10" style="color:rgb(202,0,0);"></a>3.5、hadoop特性</h2>
<p>
 </p>
<p>
<span style="font-size:18px;">       除了上面几点之外，还有计数器、排序、连接等需要关注，详细待后续吧。。。</span><br></p>
</div>
            </div>
                </div>