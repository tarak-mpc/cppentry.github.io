---
layout:     post
title:      spark中application、driver、job、stage的理解
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<div id="mainContent">
    <div class="forFlow">

<div id="post_detail">
<!--done-->
<div id="topics">
    <div class="post">
        <h1 class="postTitle" id="spark中applicationdriverjobtaskstage理解">
            <a id="cb_post_title_url" class="postTitle2" href="http://www.cnblogs.com/superhedantou/p/5699201.html" rel="nofollow">Spark中：Application，Driver，Job，Task，Stage理解</a>
        </h1>
        看了spark的原始论文和相关资料，对spark中的一些经常用到的术语做了一些梳理，记录下。
<h3 id="1application">1，Application</h3>
<p>application（应用）其实就是用spark-submit提交的程序。比方说spark examples中的计算pi的SparkPi。一个application通常包含三部分：从数据源（比方说HDFS）取数据形成RDD，通过RDD的transformation和action进行计算，将结果输出到console或者外部存储（比方说collect收集输出到console）。</p>
<h3 id="2driver">2，Driver</h3>
<p> Spark中的driver感觉其实和yarn中Application Master的功能相类似。主要完成任务的调度以及和executor和cluster manager进行协调。有client和cluster联众模式。client模式driver在任务提交的机器上运行，而cluster模式会随机选择机器中的一台机器启动driver。从spark官网截图的一张图可以大致了解driver的功能。</p>
<p><img src="http://spark.apache.org/docs/latest/img/cluster-overview.png?_=5699201">
</p><p> </p>
<h3 id="3job">3，Job</h3>
<p> Spark中的Job和MR中Job不一样不一样。MR中Job主要是Map或者Reduce Job。而Spark的Job其实很好区别，一个action算子就算一个Job，比方说count，first等。</p>
<h3 id="4-task">4, Task</h3>
<p>Task是Spark中最新的执行单元。RDD一般是带有partitions的，每个partition的在一个executor上的执行可以任务是一个Task。 </p>
<h3 id="5-stage">5, Stage</h3>
<p> Stage概念是spark中独有的。一般而言一个Job会切换成一定数量的stage。各个stage之间按照顺序执行。至于stage是怎么切分的，首选得知道spark论文中提到的narrow dependency(窄依赖)和wide dependency（ 宽依赖）的概念。其实很好区分，看一下父RDD中的数据是否进入不同的子RDD，如果只进入到一个子RDD则是窄依赖，否则就是宽依赖。宽依赖和窄依赖的边界就是stage的划分点。从spark的论文中的两张截图，可以清楚的理解宽窄依赖以及stage的划分。</p>
<p><img src="http://images2015.cnblogs.com/blog/942797/201607/942797-20160724175058435-1946046847.png" alt=""> <img src="http://images2015.cnblogs.com/blog/942797/201607/942797-20160724175115716-405249899.png" alt="" width="439" height="355"></p>
<p>至于为什么这么划分，主要是宽窄依赖在容错恢复以及处理性能上的差异（宽依赖需要进行shuffer）导致的。</p>
<p> </p>
<p> 关于spark这几个术语的了解暂时就这么多，可能不是很到位，不过暂且就这么多了。</p>
<p> </p></div><div id="MySignature"></div>

<div class="clear"></div>

<div id="blog_post_info_block">
<div id="BlogPostCategory"></div>

<div id="EntryTag"></div></div></div></div></div></div>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>