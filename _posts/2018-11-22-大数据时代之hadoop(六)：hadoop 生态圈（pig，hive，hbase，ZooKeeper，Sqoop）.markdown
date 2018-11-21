---
layout:     post
title:      大数据时代之hadoop(六)：hadoop 生态圈（pig，hive，hbase，ZooKeeper，Sqoop）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div class="article_title" style="font-size:20px;line-height:30px;font-family:'Microsoft YaHei';">
<span class="ico ico_type_Original" style="display:inline-block;width:19px;vertical-align:middle;"></span> 
<h1 style="display:inline;font-weight:normal;font-size:20px;vertical-align:middle;">
<span class="link_title"><a href="http://blog.csdn.net/chaofanwei/article/details/40982435" rel="nofollow" style="color:rgb(0,0,0);text-decoration:none;"><span style="color:#FF0000;">[置顶]</span> 大数据时代之hadoop(六)：hadoop 生态圈（pig，hive，hbase，ZooKeeper，Sqoop）</a></span></h1>
</div>
<div class="article_manage" style="color:rgb(153,153,153);line-height:24px;font-family:Arial;text-align:right;">
<span class="link_categories">分类： <a href="http://blog.csdn.net/MINEZHANGHAO/article/category/2593909" rel="nofollow" style="color:rgb(202,0,0);text-decoration:none;">hadoop</a> <a href="http://blog.csdn.net/MINEZHANGHAO/article/category/2582075" rel="nofollow" style="color:rgb(202,0,0);text-decoration:none;">大数据</a></span><span class="link_postdate">2014-11-11
 08:40</span> <span class="link_view" title="阅读次数">942人阅读</span> <span class="link_comments" title="评论次数"><a href="http://blog.csdn.net/chaofanwei/article/details/40982435#comments" rel="nofollow" style="color:rgb(202,0,0);text-decoration:none;">评论</a>(2)</span> <span class="link_collect"><a title="收藏" style="color:rgb(202,0,0);">收藏</a></span> <span class="link_report"><a href="http://blog.csdn.net/chaofanwei/article/details/40982435#report" rel="nofollow" title="举报" style="color:rgb(202,0,0);text-decoration:none;">举报</a></span></div>
<div class="tag2box" style="font-family:Arial, Console, Verdana, 'Courier New';">
<a href="http://www.csdn.net/tag/hadoop" rel="nofollow" style="color:rgb(202,0,0);text-decoration:none;display:inline-block;line-height:12px;border:1px solid rgb(238,238,238);background-color:rgb(238,238,238);">hadoop</a><a href="http://www.csdn.net/tag/zookeeper" rel="nofollow" style="color:rgb(202,0,0);text-decoration:none;display:inline-block;line-height:12px;border:1px solid rgb(238,238,238);background-color:rgb(238,238,238);">zookeeper</a><a href="http://www.csdn.net/tag/hbase" rel="nofollow" style="color:rgb(202,0,0);text-decoration:none;display:inline-block;line-height:12px;border:1px solid rgb(238,238,238);background-color:rgb(238,238,238);">hbase</a><a href="http://www.csdn.net/tag/hive" rel="nofollow" style="color:rgb(202,0,0);text-decoration:none;display:inline-block;line-height:12px;border:1px solid rgb(238,238,238);background-color:rgb(238,238,238);">hive</a><a href="http://www.csdn.net/tag/pig" rel="nofollow" style="color:rgb(202,0,0);text-decoration:none;display:inline-block;line-height:12px;border:1px solid rgb(238,238,238);background-color:rgb(238,238,238);">pig</a></div>
<div style="font-family:Arial, Console, Verdana, 'Courier New';clear:both;"></div>
<div style="font-family:Arial, Console, Verdana, 'Courier New';border:1px solid rgb(204,204,204);background-color:rgb(238,238,238);min-width:200px;">
<p style="text-align:right;">
<span>目录<a href="http://blog.csdn.net/chaofanwei/article/details/40982435#" rel="nofollow" title="系统根据文章中H1到H6标签自动生成文章目录" style="color:rgb(202,0,0);text-decoration:none;">(?)</a></span><a href="http://blog.csdn.net/chaofanwei/article/details/40982435#" rel="nofollow" title="展开" style="color:rgb(202,0,0);text-decoration:none;">[+]</a></p>
</div>
<div style="font-family:Arial, Console, Verdana, 'Courier New';clear:both;"></div>
<div id="article_content" class="article_content" style="font-size:14px;line-height:26px;font-family:Arial;">
<p>
<span class="link_title"><a href="http://blog.csdn.net/chaofanwei/article/details/39553621" rel="nofollow" style="color:rgb(202,0,0);text-decoration:none;">大数据时代之hadoop(一)：hadoop安装</a></span></p>
<p>
<span class="link_title"><a href="http://blog.csdn.net/chaofanwei/article/details/39641361" rel="nofollow" style="color:rgb(202,0,0);text-decoration:none;">大数据时代之hadoop(二)：hadoop脚本解析</a></span></p>
<p>
<span class="link_title"><a href="http://blog.csdn.net/chaofanwei/article/details/39695743" rel="nofollow" style="color:rgb(202,0,0);text-decoration:none;">大数据时代之hadoop(三)：hadoop数据流（生命周期）</a></span></p>
<p>
<span class="link_title"><a href="http://blog.csdn.net/chaofanwei/article/details/40209527" rel="nofollow" style="color:rgb(202,0,0);text-decoration:none;">大数据时代之hadoop(四)：hadoop 分布式文件系统（HDFS)</a></span></p>
<p>
<span class="link_title"><a href="http://blog.csdn.net/chaofanwei/article/details/40581887" rel="nofollow" style="color:rgb(202,0,0);text-decoration:none;">大数据时代之hadoop(五)：hadoop 分布式计算框架（MapReduce）</a></span></p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
<span style="font-size:18px;">          hadoop是有apache基金会所开发的分布式系统基础架构，其主要提供了两方面的功能：<strong>分布式存储和分布式计算</strong>。其中分布式存储是分布式计算的基础，在hadoop的实现里面，提供了分布式存储的接口，并自己实现了一个分布式存储的实现即HDFS,但并不代表hadoop只支持HDFS这一中实现，其同时支持别的存储系统，并在别的存储系统上运行分布式计算程序（mapreduce）。</span></p>
<p>
<span style="font-size:18px;"></span> </p>
<span style="font-size:18px;"></span>
<p>
<img alt="" src="https://img-blog.csdn.net/20141110164015390?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2hhb2ZhbndlaQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" style="border:none;"></p>
<p>
<br>
      <span style="font-size:18px;">  从开发角度来说，hadoop给开发人员预留了两个接口，即map接口和reduce接口，而整个作业的处理流程是固定的，即用户所能做的就是根据具体的项目需求来找到合适的方法实现自己的map函数和reduce函数，从而达到目的。</span></p>
<p>
<br>
       <span style="font-size:18px;"> 从整体上来学习hadoop毕竟还是有点难度的，但已经有一些开源的工具已经替我们做了很多，如pig，hive，hbase等，本节的重点在于了解这些构建在hadoop基础之上的一些开源工具，也可称为hadoop的生态圈吧。</span></p>
<p>
 </p>
<h1><a name="t0" style="color:rgb(202,0,0);"></a>1、pig</h1>
<span style="font-size:18px;"></span>
<p>
   <img alt="" src="https://img-blog.csdn.net/20141110164046028?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2hhb2ZhbndlaQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" style="border:none;"></p>
<p>
<span style="font-size:18px;">      pigs eat anything！</span></p>
<p>
<br><span style="font-size:18px;">      pig 是Yahoo 为了让研究员和工程师能够更简单的挖掘大规模数据集而发明的。</span></p>
<p>
<br><span style="font-size:18px;">      pig为大型数据集的处理提供了更高层次的抽象。MapReduce使程序员能够自定定义连续执行的map和reduce函数。但是，数据处理通常需要多个mapreduce过程才能实现，所以将数据处理要求改写成mapreduce模式很复杂的。</span></p>
<p>
<br><span style="font-size:18px;">    与mapreduce相比，pig提供了更丰富的数据结构，还提供了一套强大的数据变换操作。</span></p>
<p>
<br><span style="font-size:18px;">    Pig包括两部分：<br>
          用于描述数据流的语言，称为pig latin。<br>
          用于运行pig latin程序的执行环境。用两种模式：<strong>单JVM中的本地环境和hadoop集群上的分布式执行环境</strong>。</span></p>
<p>
<span style="font-size:18px;">          一个pig latin程序有一系列的“操作（operation）”和“变换（transformation）”组成。每个操作或变换对输入进行处理，然后产生输出结果。这些操作整体上描述了一个数据流。Pig执行环境把数据流翻译成可执行的内部表示，并运行它。</span></p>
<p>
<br><span style="font-size:18px;">示例：</span></p>
<div class="dp-highlighter bg_html" style="font-family:Consolas, 'Courier New', Courier, mono, serif;font-size:12px;background-color:rgb(231,229,220);overflow:auto;">
<div class="bar">
<div class="tools" style="font-size:9px;line-height:normal;font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;color:#C0C0C0;background-color:rgb(248,248,248);border-left-width:3px;border-left-style:solid;border-left-color:rgb(153,153,153);">
<strong>[html]</strong> <a href="http://blog.csdn.net/chaofanwei/article/details/40982435#" rel="nofollow" class="ViewSource" title="view plain" style="color:rgb(160,160,160);text-decoration:none;background-color:inherit;border:none;font-size:9px;">view
 plain</a><a href="http://blog.csdn.net/chaofanwei/article/details/40982435#" rel="nofollow" class="CopyToClipboard" title="copy" style="color:rgb(160,160,160);text-decoration:none;background-color:inherit;border:none;font-size:9px;">copy</a><a href="http://blog.csdn.net/chaofanwei/article/details/40982435#" rel="nofollow" class="PrintSource" title="print" style="color:rgb(160,160,160);text-decoration:none;background-color:inherit;border:none;font-size:9px;">print</a><a href="http://blog.csdn.net/chaofanwei/article/details/40982435#" rel="nofollow" class="About" title="?" style="color:rgb(160,160,160);text-decoration:none;background-color:inherit;border:none;font-size:9px;">?</a>
<div style="width:29px;z-index:99;">
</div>
</div>
</div>
<ol start="1" class="dp-xml" style="border:none;background-color:rgb(255,255,255);color:rgb(92,92,92);"><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span style="border:none;background-color:inherit;">--加载数据，并按照as 后指定的格式加载  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:rgb(85,85,85);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span class="attribute" style="border:none;color:#FF0000;background-color:inherit;">records</span><span style="border:none;background-color:inherit;"> = </span><span class="attribute-value" style="border:none;color:#0000FF;background-color:inherit;">load</span><span style="border:none;background-color:inherit;"> ‘/home/user/input/temperature1.txt’ as (year: chararray,temperature: int);  </span></span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">--打印records 对象  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:rgb(85,85,85);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">dump records;  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">describe records;  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:rgb(85,85,85);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">--过滤掉temperature!=999的数据  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span class="attribute" style="border:none;color:#FF0000;background-color:inherit;">valid_records</span><span style="border:none;background-color:inherit;"> = </span><span class="attribute-value" style="border:none;color:#0000FF;background-color:inherit;">filter</span><span style="border:none;background-color:inherit;"> records by temperature!=999;  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:rgb(85,85,85);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">--按year分组  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span class="attribute" style="border:none;color:#FF0000;background-color:inherit;">grouped_records</span><span style="border:none;background-color:inherit;"> = </span><span class="attribute-value" style="border:none;color:#0000FF;background-color:inherit;">group</span><span style="border:none;background-color:inherit;"> valid_records by year;  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:rgb(85,85,85);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">dump grouped_records;  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">describe grouped_records;  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:rgb(85,85,85);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">--取最大数  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span class="attribute" style="border:none;color:#FF0000;background-color:inherit;">max_temperature</span><span style="border:none;background-color:inherit;"> = </span><span class="attribute-value" style="border:none;color:#0000FF;background-color:inherit;">foreach</span><span style="border:none;background-color:inherit;"> grouped_records generate group,MAX(valid_records.temperature);  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:rgb(85,85,85);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">--备注：valid_records是字段名，在上一语句的describe命令结果中可以查看到group_records 的具体结构。  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">dump max_temperature;  </span></li></ol></div>
<p>
<br><strong><span style="font-size:18px;">与传统数据库比较:</span></strong></p>
<p>
<strong></strong><span style="font-size:18px;"> </span></p>
<p>
<span style="font-size:18px;">        pig latin 是一种<strong>数据流编程语言</strong>，而SQL是一种描述性编程语言。<br>
        pig <strong>不支持事物和索引，不支持低延时查询</strong>。</span></p>
<p>
 </p>
<h1><a name="t1" style="color:rgb(202,0,0);"></a>2、hive</h1>
<span style="font-size:18px;"></span>
<p>
<img alt="" src="https://img-blog.csdn.net/20141110164128819?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2hhb2ZhbndlaQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" style="border:none;"></p>
<p>
<span style="font-size:18px;">       Hive是一个构建在hadoop上的数据仓库框架，它的设计目的是让精通SQL技能分析师能够在facebook存放在hdfs的大规模数据集进行查询。</span></p>
<p>
<span style="font-size:18px;"></span> </p>
<p>
<span style="font-size:18px;">       HIVE会把查询转换为一系列在hadoop集群上运行的mapreduce作业。hive把数据组织为表，通过这种方式为存储在HDFS的数据赋予结构。元数据如表模式存储在名为metastore的数据库中。</span></p>
<span style="font-size:18px;"></span>
<p>
<br><span style="font-size:18px;">示例：</span></p>
<p>
 </p>
<div class="dp-highlighter bg_html" style="font-family:Consolas, 'Courier New', Courier, mono, serif;font-size:12px;background-color:rgb(231,229,220);overflow:auto;">
<div class="bar">
<div class="tools" style="font-size:9px;line-height:normal;font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;color:#C0C0C0;background-color:rgb(248,248,248);border-left-width:3px;border-left-style:solid;border-left-color:rgb(153,153,153);">
<strong>[html]</strong> <a href="http://blog.csdn.net/chaofanwei/article/details/40982435#" rel="nofollow" class="ViewSource" title="view plain" style="color:rgb(160,160,160);text-decoration:none;background-color:inherit;border:none;font-size:9px;">view
 plain</a><a href="http://blog.csdn.net/chaofanwei/article/details/40982435#" rel="nofollow" class="CopyToClipboard" title="copy" style="color:rgb(160,160,160);text-decoration:none;background-color:inherit;border:none;font-size:9px;">copy</a><a href="http://blog.csdn.net/chaofanwei/article/details/40982435#" rel="nofollow" class="PrintSource" title="print" style="color:rgb(160,160,160);text-decoration:none;background-color:inherit;border:none;font-size:9px;">print</a><a href="http://blog.csdn.net/chaofanwei/article/details/40982435#" rel="nofollow" class="About" title="?" style="color:rgb(160,160,160);text-decoration:none;background-color:inherit;border:none;font-size:9px;">?</a><a href="https://code.csdn.net/snippets/514864" rel="nofollow" title="在CODE上查看代码片" style="color:rgb(160,160,160);text-decoration:none;background-color:inherit;border:none;font-size:9px;"><img src="https://code.csdn.net/assets/CODE_ico.png" width="12" height="12" alt="在CODE上查看代码片" style="border:none;"></a><a href="https://code.csdn.net/snippets/514864/fork" rel="nofollow" title="派生到我的代码片" style="color:rgb(160,160,160);text-decoration:none;background-color:inherit;border:none;font-size:9px;"><img src="https://code.csdn.net/assets/ico_fork.svg" width="12" height="12" alt="派生到我的代码片" style="border:none;"></a>
<div style="width:29px;z-index:99;">
</div>
</div>
</div>
<ol start="1" class="dp-xml" style="border:none;background-color:rgb(255,255,255);color:rgb(92,92,92);"><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span style="border:none;background-color:inherit;"> (1)创建表  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:rgb(85,85,85);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">CREATE TABLE csdn (username STRING,passw STRING,mailaddr STRING) row format delimited fields terminated by '#';  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:rgb(85,85,85);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">(2)load本地文件进csdn表：  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:rgb(85,85,85);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">LOAD DATA LOCAL INPATH '/home/development/csdnfile' OVERWRITE INTO TABLE csdn;  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:rgb(85,85,85);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">(3) 执行查询并将结果输出至本地目录：  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:rgb(85,85,85);line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(153,153,153);list-style:outside;background-color:rgb(245,250,226);color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">INSERT OVERWRITE LOCAL DIRECTORY '/home/development/csdntop' SELECT passw,count(*) as passwdnum FROM csdn group by passw order by passwdnum desc;  </span></li></ol></div>
<p>
<br>
 </p>
<p>
<span style="font-size:18px;"><strong>与传统数据库比较:</strong></span></p>
<strong><span style="font-size:18px;"></span></strong>
<p>
<br><span style="font-size:18px;">    hive介于pig和传统RDBMS之间，hive的查询语言HiveQL，是基于SQL的。<br>
    hive对数据的验证并不在加载数据时进行，而在查询时进行，称为“读时模式”，而传统的数据库是“写实模式”。<br>
    hive<strong>也不支持事物和索引</strong>，不支持低延时查询。</span></p>
<p>
<span style="font-size:18px;"></span> </p>
<h1><a name="t2" style="color:rgb(202,0,0);"></a>3、hbase</h1>
<p>
<img alt="" src="https://img-blog.csdn.net/20141110164216649?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2hhb2ZhbndlaQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" style="border:none;"></p>
<p>
<span style="font-size:18px;">      <strong> HBase是一个在HDFS上开发的<span style="color:rgb(255,102,0);">面向列的分布式数据库</span>，并支持实时地随机读写超大规模数据集</strong>。</span></p>
<span style="font-size:18px;"></span>
<p>
<br><span style="font-size:18px;">       hbase是我们能够在廉价硬件构成的集群上管理<strong>超大规模的稀疏表</strong>。</span></p>
<p>
<br><strong><span style="font-size:18px;">数据模型</span></strong></p>
<p>
<br><span style="font-size:18px;">   在hbase中，表格是由版本的，单元格的内容是字节数组，表中的行的主键也是字节数组，所有对表的访问都要通过标的主键。</span></p>
<p>
<br><span style="font-size:18px;">   一个表的列族必须作为表模式定义的一部分预先给出，但是列族中新的列成员可以随后按需加入。</span></p>
<p>
 </p>
<p>
<span style="font-size:18px;">   hbase是有一个面向列族的存储器，即按列族存储。hbase在水平方向上把表分采“区域”存储，每个区域有表中的子集构成。</span></p>
<p>
<br><span style="font-size:18px;">   <strong>hbase是一个分布式的、面向列的数据存储系统</strong>。</span></p>
<h1><a name="t3" style="color:rgb(202,0,0);"></a><br>
4、ZooKeeper</h1>
<p>
<img alt="" src="https://img-blog.csdn.net/20141110164422625?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2hhb2ZhbndlaQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" style="border:none;"></p>
<p>
<span style="font-size:18px;">         ZooKeeper是hadoop的分布式协调服务，诞生于Yahoo公司。</span></p>
<span style="font-size:18px;"></span>
<p>
<br>
   <span style="font-size:18px;">      ZooKeeper提供了一组工具，使我们在<strong>构建分布式引用时能够<span style="color:rgb(255,102,102);">对部分失败进行处理</span></strong>。</span></p>
<p>
 </p>
<h1><a name="t4" style="color:rgb(202,0,0);"></a>5、Sqoop</h1>
<p>
              <span style="font-size:18px;">Sqoop是一个开源工具，它允许用户将数据从关系型数据库抽取到hadoop中，用以进一步处理。抽取处理的数据可以被mapreduce程序使用，也可以被其他类似于hive的工具使用。一旦形成分析结果，Sqoop便可以将这些结果导回数据库，供其他客户端使用。</span></p>
</div>
            </div>
                </div>