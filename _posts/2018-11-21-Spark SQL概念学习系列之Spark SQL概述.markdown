---
layout:     post
title:      Spark SQL概念学习系列之Spark SQL概述
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h3 class="jquery-notebook editor" style="font-size:16px;line-height:1.5;font-family:Verdana, Arial, Helvetica, sans-serif;">
<span style="font-size:18pt;">Spark SQL结构化数据处理</span></h3>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<span style="font-size:18px;"><span>概要：</span></span></p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<span>01 Spark SQL概述</span></p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<span>02 Spark SQL基本原理</span></p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<span>03 Spark SQL编程</span></p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
04 分布式SQL引擎</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
05 用户自定义函数</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
06 性能调优</p>
<h3 class="jquery-notebook editor" style="font-size:16px;line-height:1.5;font-family:Verdana, Arial, Helvetica, sans-serif;">
<span style="font-size:18pt;">Spark SQL概述</span></h3>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<span>Spark SQL是什么？</span></p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
Spark SQL is a Spark module for structured data processing　<img src="https://images2015.cnblogs.com/blog/855959/201704/855959-20170409100335628-1332322826.png" alt="" style="border:0px;"></p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 特别注意：<span><span style="color:rgb(255,0,0);">1</span><span style="color:rgb(255,0,0);">.3.0 及后续版本中，SchemaRDD 已经被DataFrame 所取代</span></span>。所以，我们以后的重点是DataFrame，各位博友们！</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<span style="font-size:18pt;"><br></span></p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<span style="font-size:18pt;">何为结构化数据</span></p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<span><img src="https://images2015.cnblogs.com/blog/855959/201704/855959-20170409100925644-919956681.png" alt="" style="border:0px;"></span></p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<span style="font-size:18pt;">SparkSQL 与 Spark Core的关系</span></p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<span><span style="color:rgb(255,0,0);">Spark SQL构建在Spark Core之上</span></span>，<span><span style="color:rgb(255,0,0);">专门用来处理结构化数据(不仅仅是SQL)</span></span>。</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
Spark SQL在Spark Core的基础上针对结构化数据处理进行很多优化和改进，</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
简单来讲：</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
Spark SQL 支持很多种结构化数据源，可以让你跳过复杂的读取过程，轻松从各种数据源中读取数据</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
当你使用SQL查询这些数据源中的数据并且只用到了一部分字段时，SparkSQL可以智能地只扫描这些用到的字段，而不是像SparkContext.hadoopFile中那样简单粗暴地扫描全部数据.</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<span style="font-size:18pt;">Spark SQL前世今生：由Shark发展而来</span></p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<span><img src="https://images2015.cnblogs.com/blog/855959/201704/855959-20170409180007097-1557505711.png" alt="" style="border:0px;"></span></p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<span style="font-size:18pt;">Spark SQL前世今生：可以追溯到Hive</span></p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
由facebook 开源, 最初用于解决海量结构化的日志数据统计问题的ETL(Extraction-Transformation-Loading) 工具</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
构建在Hadoop上的数据仓库平台，设计目标是使得可以用传统SQL操作Hadoop上的数据，让熟悉SQL编程的人员也能拥抱Hadoop。</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
1.使用HQL 作为查询接口</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
2.使用HDFS 作为底层存储</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
3.使用MapRed 作为执行层</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
现已成为Hadoop平台上的标配。</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
曾在一段时间之内成为SQL on Hadoop的唯一选择！</p>
<div class="cnblogs_code" style="background-color:rgb(245,245,245);border:1px solid rgb(204,204,204);overflow:auto;font-family:'Courier New' !important;">
<pre style="font-family:'Courier New' !important;">http:<span style="color:rgb(0,128,0);line-height:1.5 !important;">//</span><span style="color:rgb(0,128,0);line-height:1.5 !important;">hive.apache.org/</span>
<span style="line-height:1.5 !important;">
https:</span><span style="color:rgb(0,128,0);line-height:1.5 !important;">//</span><span style="color:rgb(0,128,0);line-height:1.5 !important;">cwiki.apache.org/confluence/display/Hive/Home</span>
<span style="line-height:1.5 !important;">
https:</span><span style="color:rgb(0,128,0);line-height:1.5 !important;">//</span><span style="color:rgb(0,128,0);line-height:1.5 !important;">cwiki.apache.org/confluence/display/Hive/LanguageManual</span></pre>
</div>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<span style="font-size:18pt;">Spark SQL前世今生：Hive 到 Shark（在Hive上做改进）</span></p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
背景：Spark出现之后，社区开始考虑基于Spark提供SQL解决方案，这就是诞生的背景</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
基于Hive的代码库，修改了Hive的后端引擎使其运行在Spark上(在Hive上做改进)。　<img src="https://images2015.cnblogs.com/blog/855959/201704/855959-20170409180253347-964300679.png" alt="" style="border:0px;"></p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
　　导致社区放弃Shark的主要原因：</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 　　　　和Spark程序的集成有诸多限制</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
　　 Hive的优化器不是为Spark而设计的，计算模型的不同，使得Hive的优化器来优化Spark程序遇到了瓶颈。</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 <span style="font-size:18pt;">Spark SQL前世今生：Shark 到 Spark SQL(彻底摆脱但是兼容Hive)</span></p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<span><img src="https://images2015.cnblogs.com/blog/855959/201704/855959-20170409180342519-1344566414.png" alt="" style="border:0px;"></span></p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<span style="font-size:18pt;">Spark SQL前世今生：Hive 到 Hive on Spark</span></p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<span>Spark SQL诞生的同时，Hive还在继续发展，一些深耕Hive的用户意识到迁移还是需要成本的，于是Hive社区提出了Hive on Spark的计划</span>从Hive 1.1+开始可用，还在发展过程中</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 <span style="font-size:18pt;">Spark SQL前世今生</span></p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 <img src="https://images2015.cnblogs.com/blog/855959/201704/855959-20170409180430925-1182015013.png" alt="" style="border:0px;">　　　</p>
            </div>
                </div>