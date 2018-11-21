---
layout:     post
title:      spark-core 和spark-sql的区别
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>转自：<br>
<a href="http://www.cnblogs.com/zlslch/p/6685679.html" rel="nofollow">http://www.cnblogs.com/zlslch/p/6685679.html</a><br>
Spark SQL构建在Spark Core之上，专门用来处理结构化数据(不仅仅是SQL)。即Spark SQL是Spark Core封装而来的！<br>
　　Spark SQL在Spark Core的基础上针对结构化数据处理进行很多优化和改进，<br>
　　简单来讲：<br>
　　　　Spark SQL 支持很多种结构化数据源，可以让你跳过复杂的读取过程，轻松从各种数据源中读取数据。<br>
　　　　当你使用SQL查询这些数据源中的数据并且只用到了一部分字段时，SparkSQL可以智能地只扫描这些用到的字段，而不是像SparkContext.hadoopFile中那样简单粗暴地扫描全部数据。</p>
<p>可见，Spark Core与Spark SQL的数据结构是不一样的!<br>
　　　<img src="https://img-blog.csdnimg.cn/20181110174431321.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQxOTE5Mjg0,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"></p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>