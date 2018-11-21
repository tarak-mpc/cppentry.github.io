---
layout:     post
title:      Spark定制班第1课：通过案例对Spark Streaming透彻理解三板斧之一：解密Spark Streaming另类实验及Spark Streaming本质解析
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="font-family:'Microsoft YaHei';font-size:14px;"><span style="line-height:19.0909px;">    </span>   从今天起，我们踏上了新的Spark学习旅途。我们的目标是要像Spark官方机构那样有能力去定制Spark。</span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="font-family:'Microsoft YaHei';font-size:14px;">   <span style="line-height:19.0909px;">    </span>我们最开始将从Spark Streaming入手。</span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="font-family:'Microsoft YaHei';font-size:14px;">   <span style="line-height:19.0909px;">    </span>为何从Spark Streaming切入Spark定制？Spark的子框架已有若干，为何选择Spark Streaming？让我们细细道来。</span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="font-family:'Microsoft YaHei';font-size:14px;">    <span style="line-height:19.0909px;">   </span>Spark最开始只有Spark Core，没有目前的这些子框架。这些子框架是构建于Spark Core之上的。没有哪个子框架能摆脱Spark Core。我们通过对一个框架的彻底研究，肯定可以领会Spark力量的源泉，并<span style="line-height:19.0909px;">精通</span>所有问题的解决之道。</span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="font-family:'Microsoft YaHei';font-size:14px;">   <span style="line-height:19.0909px;">    </span>我们再看看目前的这些子框架。Spark SQL有太多语法，研究这些会太浪费精力。SparkR还没完善。Spark GraphX已无太多可改进之处，图计算相关的数学知识也不是目前重点。Spark MLlib中的机器学习也有太多算法是与数学相关，也不是做改进的好的选择 。所以我们选择了Spark
 Streaming。</span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="font-family:'Microsoft YaHei';font-size:14px;">   <span style="line-height:19.0909px;">    </span>2015年是<span style="background-color:inherit;line-height:1.5;">流式处理的一年。大家考虑用Spark，主要也是因为Spark Streaming。这是一个流处理的时代，一切数据如果与流式处理不相关的话，都是无效的数据。Spark之所以强悍的一个重要原因在于，它的流式处理可以在线使用图计算、机器学习或者SparkR的成果，这得益于Spark一体化、多元化的基础架构设计。也就是在Spark
 Streaming中可以调用其它子框架，无需任何设置。这是Spark的无可匹敌之处，也是Spark Streaming必将一统天下的根源。但Spark的应用中，Spark Streaming也是最容易出问题的。</span></span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="background-color:inherit;line-height:1.5;"><span style="font-family:'Microsoft YaHei';font-size:14px;">   <span style="line-height:19.0909px;">    </span>Spark Streaming与其它子框架不同之处在于，它更像是Spark Core之上的一个应用程序。所以如果要做Spark的定制开发，Spark Streaming则提供了最好的参考。你想掌握Spark
 Streaming，但你不去精通Spark Core的话，那是不可能的。所以我们选择Spark Streaming来提升自己，是找到了关键点。如果对照风水学的说法，我们已经幸运地找到了龙脉。如果要寻龙点穴，那么Spark Streaming就是龙穴之所在。找到了穴位，我们就能一日千里。</span></span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="background-color:inherit;line-height:1.5;"><span style="font-family:'Microsoft YaHei';font-size:14px;"><br style="background-color:inherit;"></span></span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="background-color:inherit;"><span style="font-family:'Microsoft YaHei';font-size:14px;"><strong>本期内容</strong></span></span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="font-family:'Microsoft YaHei';font-size:14px;">1 Spark Streaming另类在线实验</span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="font-family:'Microsoft YaHei';font-size:14px;">2 瞬间理解Spark Streaming本质</span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="background-color:inherit;"><span style="font-family:'Microsoft YaHei';font-size:14px;"><br style="background-color:inherit;"></span></span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="font-family:'Microsoft YaHei';font-size:14px;"><span style="background-color:inherit;"><strong>1 Spark Streaming另类在线实验</strong></span><br style="background-color:inherit;"></span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="font-family:'Microsoft YaHei';font-size:14px;">   <span style="line-height:19.0909px;">    </span>我们在研究Spark Streaming的过程中，会有困惑的事情：如何清晰的看到数据的流入、被处理的过程？</span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="font-family:'Microsoft YaHei';font-size:14px;">   <span style="line-height:19.0909px;">    </span>使用一个小技巧，通过调节放大Batch Interval的方式，来降低批处理次数，以方便看清楚各个环节。</span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="font-family:'Microsoft YaHei';font-size:14px;">   <span style="line-height:19.0909px;">    </span>我们从已写过的广告点击的在线黑名单过滤的Spark Streaming应用程序入手。</span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<div class="dp-highlighter bg_plain" style="font-family:Consolas, 'Courier New', Courier, mono, serif;font-size:12px;background-color:rgb(231,229,220);overflow:hidden;border-color:rgb(204,204,204);">
<div class="bar">
<div class="tools" style="font-size:9px;line-height:normal;font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;color:#C0C0C0;background-color:rgb(248,248,248);border-left:3px solid rgb(108,226,108);">
<strong>[plain]</strong> <a href="http://blog.csdn.net/andyshar/article/details/51295030#" rel="nofollow" class="ViewSource" title="view plain" style="text-decoration:none;background-color:inherit;border:none;font-size:9px;color:rgb(12,137,207);display:inline-block;width:16px;text-indent:-2000px;">view
 plain</a><span> <a href="http://blog.csdn.net/andyshar/article/details/51295030#" rel="nofollow" class="CopyToClipboard" title="copy" style="text-decoration:none;background-color:inherit;border:none;font-size:9px;color:rgb(12,137,207);display:inline-block;width:16px;text-indent:-2000px;">copy</a></span>
<div style="width:18px;z-index:99;">
</div>
<span> </span><span class="tracking-ad"><a href="https://code.csdn.net/snippets/1669790" rel="nofollow" title="在CODE上查看代码片" style="text-decoration:none;background-color:inherit;border:none;font-size:9px;color:rgb(12,137,207);display:inline-block;width:16px;"><img src="https://code.csdn.net/assets/CODE_ico.png" width="12" height="12" alt="在CODE上查看代码片" style="border:none;"></a></span><span class="tracking-ad"><a href="https://code.csdn.net/snippets/1669790/fork" rel="nofollow" title="派生到我的代码片" style="text-decoration:none;background-color:inherit;border:none;font-size:9px;color:rgb(12,137,207);display:inline-block;width:16px;"><img src="https://code.csdn.net/assets/ico_fork.svg" width="12" height="12" alt="派生到我的代码片" style="border:none;"></a></span></div>
</div>
<ol start="1" style="border:none;background-color:rgb(255,255,255);color:rgb(92,92,92);"><li class="alt" style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);color:inherit;line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:#000000;background-color:inherit;"><span style="border:none;background-color:inherit;">package com.dt.spark.streaming  </span></span></li><li style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);background-color:rgb(248,248,248);line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:#000000;background-color:inherit;">  </span></li><li class="alt" style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);color:inherit;line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:#000000;background-color:inherit;">import org.apache.spark.SparkConf  </span></li><li style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);background-color:rgb(248,248,248);line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:#000000;background-color:inherit;">import org.apache.spark.streaming.StreamingContext  </span></li><li class="alt" style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);color:inherit;line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:#000000;background-color:inherit;">import org.apache.spark.streaming.Seconds  </span></li><li style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);background-color:rgb(248,248,248);line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:#000000;background-color:inherit;">  </span></li><li class="alt" style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);color:inherit;line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:#000000;background-color:inherit;">object OnlineBlackListFilter {  </span></li><li style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);background-color:rgb(248,248,248);line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:#000000;background-color:inherit;">    def main(args: Array[String]){  </span></li><li class="alt" style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);color:inherit;line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:#000000;background-color:inherit;">      /**  </span></li><li style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);background-color:rgb(248,248,248);line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:#000000;background-color:inherit;">       * 第1步：创建Spark的配置对象SparkConf，设置Spark程序的运行时的配置信息，  </span></li><li class="alt" style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);color:inherit;line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:#000000;background-color:inherit;">       * 例如说通过setMaster来设置程序要链接的Spark集群的Master的URL，如果设置  </span></li><li style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);background-color:rgb(248,248,248);line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:#000000;background-color:inherit;">       * 为local，则代表Spark程序在本地运行，特别适合于机器配置条件非常差（例如  </span></li><li class="alt" style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);color:inherit;line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:#000000;background-color:inherit;">       * 只有1G的内存）的初学者。  </span></li><li style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);background-color:rgb(248,248,248);line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:#000000;background-color:inherit;">       */  </span></li><li class="alt" style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);color:inherit;line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:#000000;background-color:inherit;">      // 创建SparkConf对象  </span></li><li style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);background-color:rgb(248,248,248);line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:#000000;background-color:inherit;">      val conf = new SparkConf()  </span></li><li class="alt" style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);color:inherit;line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:#000000;background-color:inherit;">      // 设置应用程序的名称，在程序运行的监控界面可以看到名称  </span></li><li style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);background-color:rgb(248,248,248);line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:#000000;background-color:inherit;">      conf.setAppName("OnlineBlackListFilter")  </span></li><li class="alt" style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);color:inherit;line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:#000000;background-color:inherit;">      // 此时，程序在Spark集群  </span></li><li style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);background-color:rgb(248,248,248);line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:#000000;background-color:inherit;">      conf.setMaster("spark://Master:7077")  </span></li><li class="alt" style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);color:inherit;line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:#000000;background-color:inherit;">  </span></li><li style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);background-color:rgb(248,248,248);line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:#000000;background-color:inherit;">      val ssc = new StreamingContext(conf, Seconds(30))  </span></li><li class="alt" style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);color:inherit;line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:#000000;background-color:inherit;">  </span></li><li style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);background-color:rgb(248,248,248);line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:#000000;background-color:inherit;">      /**  </span></li><li class="alt" style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);color:inherit;line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:#000000;background-color:inherit;">       * 黑名单数据准备，实际上黑名单一般都是动态的，例如在Redis或者数据库中，  </span></li><li style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);background-color:rgb(248,248,248);line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:#000000;background-color:inherit;">       * 黑名单的生成往往有复杂的业务逻辑，具体情况算法不同，  </span></li><li class="alt" style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);color:inherit;line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:#000000;background-color:inherit;">       * 但是在Spark Streaming进行处理的时候每次都能够访问完整的信息。  </span></li><li style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);background-color:rgb(248,248,248);line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:#000000;background-color:inherit;">       */  </span></li><li class="alt" style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);color:inherit;line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:#000000;background-color:inherit;">      val blackList = Array(("Spy", true),("Cheater", true))  </span></li><li style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);background-color:rgb(248,248,248);line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:#000000;background-color:inherit;">      val blackListRDD = ssc.sparkContext.parallelize(blackList, 8)  </span></li><li class="alt" style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);color:inherit;line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:#000000;background-color:inherit;">  </span></li><li style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);background-color:rgb(248,248,248);line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:#000000;background-color:inherit;">      val adsClickStream = ssc.socketTextStream("Master", 9999)  </span></li><li class="alt" style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);color:inherit;line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:#000000;background-color:inherit;">  </span></li><li style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);background-color:rgb(248,248,248);line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:#000000;background-color:inherit;">      /**  </span></li><li class="alt" style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);color:inherit;line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:#000000;background-color:inherit;">       * 此处模拟的广告点击的每条数据的格式为：time、name  </span></li><li style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);background-color:rgb(248,248,248);line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:#000000;background-color:inherit;">       * 此处map操作的结果是name、（time，name）的格式  </span></li><li class="alt" style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);color:inherit;line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:#000000;background-color:inherit;">       */  </span></li><li style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);background-color:rgb(248,248,248);line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:#000000;background-color:inherit;">      val adsClickStreamFormatted = adsClickStream.map { ads =&gt; (ads.split(" ")(1), ads) }  </span></li><li class="alt" style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);color:inherit;line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:#000000;background-color:inherit;">      adsClickStreamFormatted.transform(userClickRDD =&gt; {  </span></li><li style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);background-color:rgb(248,248,248);line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:#000000;background-color:inherit;">        // 通过leftOuterJoin操作既保留了左侧用户广告点击内容的RDD的所有内容，  </span></li><li class="alt" style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);color:inherit;line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:#000000;background-color:inherit;">        // 又获得了相应点击内容是否在黑名单中  </span></li><li style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);background-color:rgb(248,248,248);line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:#000000;background-color:inherit;">        val joinedBlackListRDD = userClickRDD.leftOuterJoin(blackListRDD)  </span></li><li class="alt" style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);color:inherit;line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:#000000;background-color:inherit;">  </span></li><li style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);background-color:rgb(248,248,248);line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:#000000;background-color:inherit;">        /**  </span></li><li class="alt" style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);color:inherit;line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:#000000;background-color:inherit;">         * 进行filter过滤的时候，其输入元素是一个Tuple：（name,((time,name), boolean)）  </span></li><li style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);background-color:rgb(248,248,248);line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:#000000;background-color:inherit;">         * 其中第一个元素是黑名单的名称，第二元素的第二个元素是进行leftOuterJoin的时候是否存在的值。  </span></li><li class="alt" style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);color:inherit;line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:#000000;background-color:inherit;">         * 如果存在的话，表明当前广告点击是黑名单，需要过滤掉，否则的话是有效点击内容；  </span></li><li style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);background-color:rgb(248,248,248);line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:#000000;background-color:inherit;">         */  </span></li><li class="alt" style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);color:inherit;line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:#000000;background-color:inherit;">        val validClicked = joinedBlackListRDD.filter(joinedItem =&gt; {  </span></li><li style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);background-color:rgb(248,248,248);line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:#000000;background-color:inherit;">          if(joinedItem._2._2.getOrElse(false))  </span></li><li class="alt" style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);color:inherit;line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:#000000;background-color:inherit;">          {  </span></li><li style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);background-color:rgb(248,248,248);line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:#000000;background-color:inherit;">            false  </span></li><li class="alt" style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);color:inherit;line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:#000000;background-color:inherit;">          } else {  </span></li><li style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);background-color:rgb(248,248,248);line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:#000000;background-color:inherit;">            true  </span></li><li class="alt" style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);color:inherit;line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:#000000;background-color:inherit;">          }  </span></li><li style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);background-color:rgb(248,248,248);line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:#000000;background-color:inherit;">  </span></li><li class="alt" style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);color:inherit;line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:#000000;background-color:inherit;">        })  </span></li><li style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);background-color:rgb(248,248,248);line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:#000000;background-color:inherit;">  </span></li><li class="alt" style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);color:inherit;line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:#000000;background-color:inherit;">        validClicked.map(validClick =&gt; {validClick._2._1})  </span></li><li style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);background-color:rgb(248,248,248);line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:#000000;background-color:inherit;">      }).print  </span></li><li class="alt" style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);color:inherit;line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:#000000;background-color:inherit;">  </span></li><li style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);background-color:rgb(248,248,248);line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:#000000;background-color:inherit;">      /**  </span></li><li class="alt" style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);color:inherit;line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:#000000;background-color:inherit;">       * 计算后的有效数据一般都会写入Kafka中，下游的计费系统会从kafka中pull到有效数据进行计费  </span></li><li style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);background-color:rgb(248,248,248);line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:#000000;background-color:inherit;">       */  </span></li><li class="alt" style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);color:inherit;line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:#000000;background-color:inherit;">      ssc.start()  </span></li><li style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);background-color:rgb(248,248,248);line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:#000000;background-color:inherit;">      ssc.awaitTermination()  </span></li><li class="alt" style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);color:inherit;line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:#000000;background-color:inherit;">  </span></li><li style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);background-color:rgb(248,248,248);line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:#000000;background-color:inherit;">    }  </span></li><li class="alt" style="border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);color:inherit;line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:#000000;background-color:inherit;">}  </span></li></ol></div>
<span style="font-family:'Microsoft YaHei';font-size:14px;line-height:19.0909px;">   <span style="line-height:19.0909px;">    </span><span style="font-family:'微软雅黑';font-size:13.6364px;line-height:19.0909px;">把程序的Batch Interval设置从30秒改成300秒：</span></span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="font-family:'Microsoft YaHei';font-size:14px;">    <span style="line-height:19.0909px;">   </span><span style="background-color:inherit;line-height:1.5;">val ssc = new StreamingContext(conf, Seconds(300))</span></span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="background-color:inherit;line-height:1.5;"><span style="font-family:'Microsoft YaHei';font-size:14px;">   <span style="line-height:19.0909px;">    </span>重新生成一下jar包 。</span></span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="background-color:inherit;line-height:1.5;"><span style="font-family:'Microsoft YaHei';font-size:14px;"><br style="background-color:inherit;"></span></span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="font-family:'Microsoft YaHei';font-size:14px;line-height:1.5;background-color:inherit;">   <span style="line-height:19.0909px;">    </span>Spark集群有5台机器：Master、Worker1、Worker2、Worker3、Worker4。</span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="line-height:1.5;background-color:inherit;"><span style="font-family:'Microsoft YaHei';font-size:14px;"></span></span>
<div style="line-height:19.0909px;"><span style="line-height:1.5;background-color:inherit;"><span style="font-family:'Microsoft YaHei';font-size:14px;">   <span style="line-height:19.0909px;">    </span>启动Spark的History Server。</span></span></div>
</div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="background-color:inherit;line-height:1.5;"><span style="font-family:'Microsoft YaHei';font-size:14px;">   <span style="line-height:19.0909px;">    </span>打开数据发送的端口：</span></span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="background-color:inherit;line-height:1.5;"><span style="font-family:'Microsoft YaHei';font-size:14px;">   <span style="line-height:19.0909px;">    </span>nc -lk 9999</span></span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="background-color:inherit;line-height:1.5;"><span style="font-family:'Microsoft YaHei';font-size:14px;">   <span style="line-height:19.0909px;">    </span>用spark-submit运行前面生成的jar包。</span></span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="background-color:inherit;line-height:1.5;"><span style="font-family:'Microsoft YaHei';font-size:14px;"><br style="background-color:inherit;"></span></span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="background-color:inherit;line-height:1.5;"><span style="font-family:'Microsoft YaHei';font-size:14px;">   <span style="line-height:19.0909px;">    </span>在数据发送端口输入若干数据，比如：</span></span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="background-color:inherit;line-height:1.5;"><span style="font-family:'Microsoft YaHei';font-size:14px;"></span></span></div>
<div style="color:rgb(85,85,85);font-family:'微软雅黑';font-size:13.6364px;line-height:19.0909px;">
<span style="background-color:inherit;line-height:1.5;">    <span style="font-family:'Microsoft YaHei';font-size:14px;line-height:19.0909px;">   </span></span><span style="background-color:inherit;font-size:14px;line-height:1.5;">1375864674543</span><span style="background-color:inherit;line-height:1.5;font-size:14px;"> Tom</span></div>
<div style="color:rgb(85,85,85);font-family:'微软雅黑';font-size:13.6364px;line-height:19.0909px;">
<span style="background-color:inherit;line-height:1.5;">    <span style="font-family:'Microsoft YaHei';font-size:14px;line-height:19.0909px;">   </span></span><span style="background-color:inherit;font-size:14px;line-height:1.5;">1375864674553</span><span style="background-color:inherit;line-height:1.5;font-size:14px;"> Spy</span></div>
<div style="color:rgb(85,85,85);font-family:'微软雅黑';font-size:13.6364px;line-height:19.0909px;">
<span style="background-color:inherit;line-height:1.5;">    <span style="font-family:'Microsoft YaHei';font-size:14px;line-height:19.0909px;">   </span></span><span style="background-color:inherit;font-size:14px;line-height:1.5;">1375864674571</span><span style="background-color:inherit;line-height:1.5;font-size:14px;"> Andy</span></div>
<div style="color:rgb(85,85,85);font-family:'微软雅黑';font-size:13.6364px;line-height:19.0909px;">
<span style="background-color:inherit;line-height:1.5;">    <span style="font-family:'Microsoft YaHei';font-size:14px;line-height:19.0909px;">   </span></span><span style="background-color:inherit;font-size:14px;line-height:1.5;">1375864688436</span><span style="background-color:inherit;line-height:1.5;font-size:14px;"> Cheater</span></div>
<div style="color:rgb(85,85,85);font-family:'微软雅黑';font-size:13.6364px;line-height:19.0909px;">
<span style="background-color:inherit;line-height:1.5;">    <span style="font-family:'Microsoft YaHei';font-size:14px;line-height:19.0909px;">   </span></span><span style="background-color:inherit;font-size:14px;line-height:1.5;">1375864784240</span><span style="background-color:inherit;line-height:1.5;font-size:14px;"> Kelvin</span></div>
<div style="color:rgb(85,85,85);font-family:'微软雅黑';font-size:13.6364px;line-height:19.0909px;">
<span style="background-color:inherit;line-height:1.5;">    <span style="font-family:'Microsoft YaHei';font-size:14px;line-height:19.0909px;">   </span></span><span style="background-color:inherit;font-size:14px;line-height:1.5;">1375864853892</span><span style="background-color:inherit;line-height:1.5;font-size:14px;"> Steven</span></div>
<div style="color:rgb(85,85,85);font-family:'微软雅黑';font-size:13.6364px;line-height:19.0909px;">
<span style="background-color:inherit;line-height:1.5;">    <span style="font-family:'Microsoft YaHei';font-size:14px;line-height:19.0909px;">   </span></span><span style="background-color:inherit;font-size:14px;line-height:1.5;">1375864979347</span><span style="background-color:inherit;line-height:1.5;font-size:14px;"> John</span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="background-color:inherit;line-height:1.5;"><span style="font-family:'Microsoft YaHei';font-size:14px;"></span></span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="font-family:'Microsoft YaHei';font-size:14px;"><br style="background-color:inherit;"></span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="font-family:'Microsoft YaHei';font-size:14px;">   <span style="line-height:19.0909px;">    </span>打开浏览器，看History Server的日志信息：</span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="font-family:'Microsoft YaHei';font-size:14px;"><img src="https://img-blog.csdn.net/20160502071336026?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" style="border:none;background-color:inherit;display:inline-block;"></span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="font-family:'Microsoft YaHei';font-size:14px;"><br style="background-color:inherit;"></span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="font-family:'Microsoft YaHei';font-size:14px;">   <span style="line-height:19.0909px;">    </span>点击最新的应用，看我们目前运行的应用程序中有些什么Job：</span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="font-family:'Microsoft YaHei';font-size:14px;"><img src="https://img-blog.csdn.net/20160502071414976?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" style="border:none;background-color:inherit;display:inline-block;"></span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="font-family:'Microsoft YaHei';font-size:14px;">    <span style="line-height:19.0909px;">    </span>总共竟然有5个Job。这完全不是我们此前做Spark SQL之类的应用程序时看到的样子。</span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="font-family:'Microsoft YaHei';font-size:14px;">   <span style="line-height:19.0909px;">    </span>我们接下来看一看这些Job的内容，主要揭示一些现象，不会做完全深入的剖析，只是为了先让大家进行一些思考。</span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="font-family:'Microsoft YaHei';font-size:14px;"><br style="background-color:inherit;"></span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="line-height:1.5;background-color:inherit;"><span style="font-family:'Microsoft YaHei';font-size:14px;">   <span style="line-height:19.0909px;">    </span><span style="background-color:inherit;">Job 0：</span>此Job不体现我们的业务逻辑代码。这个Job是出于对后面计算的负载均衡的考虑。</span></span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="font-family:'Microsoft YaHei';font-size:14px;"><img src="https://img-blog.csdn.net/20160502071451477?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" style="border:none;background-color:inherit;display:inline-block;"></span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="font-family:'Microsoft YaHei';font-size:14px;">   <span style="line-height:19.0909px;">    </span>Job 0包含有Stage 0、Stage 1。随便看一个Stage，比如Stage 1。看看其中的Aggregated Metrics by Executor部分：</span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="font-family:'Microsoft YaHei';font-size:14px;"><img src="https://img-blog.csdn.net/20160502071533230?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" style="border:none;background-color:inherit;display:inline-block;"></span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="font-family:'Microsoft YaHei';font-size:14px;"><span style="line-height:21px;">   <span style="line-height:19.0909px;">    </span>发现此Stage<span style="line-height:21px;">在</span>所有Executor上都存在。</span><br></span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="font-family:'Microsoft YaHei';font-size:14px;"><br style="background-color:inherit;"></span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="font-family:'Microsoft YaHei';font-size:14px;">    <span style="line-height:19.0909px;">   </span><span style="background-color:inherit;">Job 1：</span>运行时间比较长，耗时1.5分钟。</span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="font-family:'Microsoft YaHei';font-size:14px;"><img src="https://img-blog.csdn.net/20160502071556760?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" style="border:none;background-color:inherit;display:inline-block;"></span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="font-family:'Microsoft YaHei';font-size:14px;">   <span style="line-height:19.0909px;">    </span>点击Stage 2的链接，进去看看Aggregated Metrics By Executor部分：</span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="font-family:'Microsoft YaHei';font-size:14px;"><img src="https://img-blog.csdn.net/20160502071622996?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" style="border:none;background-color:inherit;display:inline-block;"></span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="line-height:1.5;background-color:inherit;"><span style="font-family:'Microsoft YaHei';font-size:14px;">   <span style="line-height:19.0909px;">    </span>可以知道，Stage 2只在Worker4上的一个Executor执行，而且执行了1.5分钟。</span></span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="line-height:1.5;background-color:inherit;"><span style="font-family:'Microsoft YaHei';font-size:14px;">   <span style="line-height:19.0909px;">    </span>是否会觉得奇怪：从业务处理的角度看，我们发送的那么一点数据，没有必要去启动一个运行1.5分钟的任务吧。那这个任务是做什么呢？</span></span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="font-family:'Microsoft YaHei';font-size:14px;">   <span style="line-height:19.0909px;">    </span>从DAG Visualization部分，就知道此Job实际就是启动了一个接收数据的Receiver：</span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="font-family:'Microsoft YaHei';font-size:14px;"><img src="https://img-blog.csdn.net/20160502080633814?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" style="border:none;"><br></span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="font-family:'Microsoft YaHei';font-size:14px;">   <span style="line-height:19.0909px;">    </span>原来Receiver是通过一个Job来启动的。那肯定有一个Action来触发它。</span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="font-family:'Microsoft YaHei';font-size:14px;">   <span style="line-height:19.0909px;">    </span>看看Tasks部分：</span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="font-family:'Microsoft YaHei';font-size:14px;"><img src="https://img-blog.csdn.net/20160502071648200?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" style="border:none;background-color:inherit;display:inline-block;"></span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="font-family:'Microsoft YaHei';font-size:14px;">   <span style="line-height:19.0909px;">    </span>只有一个Worker运行此Job。是用于接收数据。<br style="background-color:inherit;"></span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="font-family:'Microsoft YaHei';font-size:14px;">   <span style="line-height:19.0909px;">    </span>Locality Level是PROCESS_LOCAL，原来是内存节点。所以，默认情况下，数据接收不会使用磁盘，而是直接使用内存中的数据。<br style="background-color:inherit;"></span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="line-height:1.5;"><span style="font-family:'Microsoft YaHei';font-size:14px;">   <span style="line-height:19.0909px;">    </span>看来，Spark Streaming应用程序启动后，自己会启动一些Job。默认启动了一个Job来接收数据，为后续处理做准备。</span></span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="font-family:'Microsoft YaHei';font-size:14px;"><br style="background-color:inherit;"><span style="background-color:inherit;line-height:1.5;"><span style="line-height:19.0909px;">   </span><span style="line-height:19.0909px;">    </span><span style="line-height:19.0909px;"></span>重要启示：一个Spark应用程序中可以启动很多Job，而这些不同的Job之间可以相互配合。这一认识为我们写复杂Spark程序奠定了良好的基础。</span></span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="font-family:'Microsoft YaHei';font-size:14px;"><br style="background-color:inherit;"></span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="font-family:'Microsoft YaHei';font-size:14px;"><span style="line-height:19.0909px;">   </span><span style="line-height:19.0909px;">    </span><span style="line-height:19.0909px;"></span><span style="background-color:inherit;">Job 2：</span>看Details可以发现有我们程序的主要业务逻辑，体现在Stage
 3、Stage 4、Stage 5中。</span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="font-family:'Microsoft YaHei';font-size:14px;"><img src="https://img-blog.csdn.net/20160502071715700?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" style="border:none;background-color:inherit;display:inline-block;"></span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="font-family:'Microsoft YaHei';font-size:14px;">   <span style="line-height:19.0909px;">    </span>我们看Stage 3、Stage 4的详情，可以知道这2个Stage都是用4个Executor执行的。所有数据处理是在4台机器上进行的。</span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="font-family:'Microsoft YaHei';font-size:14px;"><img src="https://img-blog.csdn.net/20160502071741778?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" style="border:none;background-color:inherit;display:inline-block;"></span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="font-family:'Microsoft YaHei';font-size:14px;">   <span style="line-height:19.0909px;">    </span>Stage 5只在Worker4上。这是因为这个Stage有Shuffle操作。<br style="background-color:inherit;"></span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="font-family:'Microsoft YaHei';font-size:14px;"><img src="https://img-blog.csdn.net/20160502071812981?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" style="border:none;background-color:inherit;display:inline-block;"></span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="font-family:'Microsoft YaHei';font-size:14px;"><br style="background-color:inherit;"></span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="font-family:'Microsoft YaHei';font-size:14px;">    <span style="line-height:19.0909px;">    </span><span style="background-color:inherit;">Job3：有Stage 6、Stage 7、Stage 8。其中Stage 6、Stage 7被跳过。</span></span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="font-family:'Microsoft YaHei';font-size:14px;"><img src="https://img-blog.csdn.net/20160502071853982?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" style="border:none;background-color:inherit;display:inline-block;"></span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="font-family:'Microsoft YaHei';font-size:14px;">   <span style="line-height:19.0909px;">    </span>看看Stage 8的Aggregated Metrics by Executor部分。可以看到，数据处理是在4台机器上进行的：</span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="font-family:'Microsoft YaHei';font-size:14px;"><img src="https://img-blog.csdn.net/20160502071918513?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" style="border:none;background-color:inherit;display:inline-block;"></span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="line-height:1.5;background-color:inherit;"><span style="font-family:'Microsoft YaHei';font-size:14px;">   </span></span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="font-family:'Microsoft YaHei';font-size:14px;"><span style="background-color:inherit;">   <span style="line-height:19.0909px;">    </span>Job4：</span>也体现了我们应用程序中的业务逻辑 。有Stage 9、Stage 10、Stage 11。其中Stage 9、Stage 10被跳过。</span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="font-family:'Microsoft YaHei';font-size:14px;"><img src="https://img-blog.csdn.net/20160502071942154?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" style="border:none;background-color:inherit;display:inline-block;"></span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="font-family:'Microsoft YaHei';font-size:14px;">   <span style="line-height:19.0909px;">    </span>看看Stage 11的详情。可以看到，数据处理是在Worker2之外的其它3台机器上进行的：<br style="background-color:inherit;"></span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="font-family:'Microsoft YaHei';font-size:14px;"><img src="https://img-blog.csdn.net/20160502072009138?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" style="border:none;background-color:inherit;display:inline-block;"></span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="font-family:'Microsoft YaHei';font-size:14px;"><br style="background-color:inherit;"></span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="font-family:'Microsoft YaHei';font-size:14px;">   <span style="line-height:19.0909px;">    </span>综合以上的现象可以知道，Spark Streaming的一个应用中，运行了这么多Job，远不是我们从网络博客或者书籍上看的那么简单。</span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="font-family:'Microsoft YaHei';font-size:14px;">   <span style="line-height:19.0909px;">    </span>我们有必要通过这些现象，反过来回溯去寻根问源。不过这次暂不做深入分析。</span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="line-height:1.5;background-color:inherit;"><span style="font-family:'Microsoft YaHei';font-size:14px;">   <span style="line-height:19.0909px;">    </span>我们的神奇之旅才刚刚开始。</span></span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="font-family:'Microsoft YaHei';font-size:14px;"><br style="background-color:inherit;"></span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="font-family:'Microsoft YaHei';font-size:14px;"><strong>2 瞬间理解Spark Streaming本质</strong></span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<img src="https://img-blog.csdn.net/20160502081601458?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" style="border:none;font-weight:bold;font-family:'Microsoft YaHei';font-size:14px;line-height:19.0909px;"></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="font-family:'Microsoft YaHei';font-size:14px;"></span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="font-family:'Microsoft YaHei';font-size:14px;">   <span style="line-height:19.0909px;">    </span>以上的连续4个图，分别对应以下4个段落的描述：</span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="font-family:'Microsoft YaHei';font-size:14px;"></span>
<div style="line-height:19.0909px;"><span style="font-family:'Microsoft YaHei';font-size:14px;">   <span style="line-height:19.0909px;">    </span>Spark Streaming接收<span style="line-height:19.0909px;">Kafka、Flume、HDFS和Kinesis等各种来源的</span>实时输入数据，进行处理后，处理结果保存在HDFS、Databases等各种地方。</span></div>
<div style="line-height:19.0909px;"><span style="font-family:'Microsoft YaHei';font-size:14px;">   <span style="line-height:19.0909px;">    </span>Spark Streaming接收这些实时输入数据流，会将它们按批次划分，然后交给Spark引擎处理，生成按照批次划分的结果流。</span></div>
<div style="line-height:19.0909px;"><span style="font-family:'Microsoft YaHei';font-size:14px;">   <span style="line-height:19.0909px;">    </span>Spark Streaming提供了表示连续数据流的、高度抽象的被称为离散流的DStream。<span style="line-height:19.0909px;">DStream本质上表示RDD的序列。<span style="line-height:19.0909px;">DStream中的每个RDD都包含来自一个时间间隔的数据。</span></span></span></div>
<div style="line-height:19.0909px;"><span style="font-family:'Microsoft YaHei';font-size:14px;">   <span style="line-height:19.0909px;">    </span>Spark Streaming除了</span><span style="font-family:'Microsoft YaHei';font-size:14px;line-height:19.0909px;">使用数据源产生的数据流创建DStream，也可以在已有的DStream上使用一些操作来创建新的DStream。<span style="line-height:19.0909px;">任何对DStream的操作都会转变为对底层RDD的操作。本图例子是对lines
 Dstream做了flatMap操作，生成words Dstream。</span></span></div>
<div style="line-height:19.0909px;"><span style="font-family:'Microsoft YaHei';font-size:14px;line-height:19.0909px;"><br></span></div>
<div><span style="line-height:19.0909px;"></span>
<div style="line-height:19.0909px;"><span style="font-family:'Microsoft YaHei';font-size:14px;">    <span style="line-height:19.0909px;">   </span>在我们前面的实验中，每300秒会接收一批数据，基于这批数据会生成RDD，进而触发Job，执行处理。</span></div>
<div style="line-height:19.0909px;"><span style="font-family:'Microsoft YaHei';font-size:14px;"><br></span></div>
<div style="line-height:19.0909px;"><span style="font-family:'Microsoft YaHei';font-size:14px;">   <span style="line-height:19.0909px;">    </span>DStream是一个没有边界的集合，没有大小的限制。</span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:14px;line-height:19.0909px;">  <span style="line-height:19.0909px;">     </span>DStream代表了时空的概念。随着时间的推移，里面不断产生RDD。</span></div>
</div>
</div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="font-family:'Microsoft YaHei';font-size:14px;">   <span style="line-height:19.0909px;">    </span><span style="line-height:19.0909px;">锁定</span>到时间片<span style="line-height:19.0909px;">后</span>，就是空间的操作，也就是</span><span style="font-family:'Microsoft YaHei';font-size:14px;line-height:19.0909px;">对本时间片的对应批次的数据的处理。</span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="font-family:'Microsoft YaHei';font-size:14px;"><br></span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="font-family:'Microsoft YaHei';font-size:14px;">  <span style="line-height:19.0909px;">     </span>下面用实例来讲解数据处理过程。</span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="font-family:'Microsoft YaHei';font-size:14px;">       从Spark Streaming程序转换为Spark执行的作业的过程中，使用了<span style="line-height:19.0909px;">DStreamGraph。</span></span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="font-family:'Microsoft YaHei';font-size:14px;">   <span style="line-height:19.0909px;">    Spark Streaming程序中一般会</span>有若干个对DStream的操作。<span style="line-height:19.0909px;">DStreamGraph就是由这些操作的依赖关系构成。</span></span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="font-family:'Microsoft YaHei';font-size:14px;">       从程序到DStreamGraph的转换，如以下图例所示：</span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<img src="https://img-blog.csdn.net/20160502095524501?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" style="border:none;font-family:'Microsoft YaHei';font-size:14px;line-height:19.0909px;"></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<div style="line-height:19.0909px;"><span style="font-family:'Microsoft YaHei';font-size:14px;">       <span style="line-height:19.0909px;">本例中，从</span><span style="line-height:19.0909px;">每个foreach开始，都会进行回溯。</span><span style="line-height:19.0909px;">从后往前回溯</span>这些操作之间的依赖关系，也就形成了DStreamGraph。</span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:14px;line-height:19.0909px;">       </span><span style="font-family:'Microsoft YaHei';font-size:14px;line-height:19.0909px;">执行从DStream到RDD的转换，也就</span><span style="font-family:'Microsoft YaHei';font-size:14px;line-height:19.0909px;">形成了RDD
 Graph，如下图所示：</span></div>
</div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="font-family:'Microsoft YaHei';font-size:14px;"></span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="font-family:'Microsoft YaHei';font-size:14px;"><img src="https://img-blog.csdn.net/20160502095316049?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" style="border:none;"><br style="background-color:inherit;"></span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="font-family:'Microsoft YaHei';font-size:14px;">   <span style="line-height:19.0909px;">    </span>空间维度确定之后，随着时间不断推进，会不断实例化RDD Graph，然后触发Job去执行处理。</span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="font-family:'Microsoft YaHei';font-size:14px;"><span style="line-height:19.0909px;">  </span><span style="line-height:19.0909px;">     </span>现在再去读官方的Spark Streaming的文档，就好理解多了。</span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="font-family:'Microsoft YaHei';font-size:14px;"><img src="https://img-blog.csdn.net/20160502072034654?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" style="border:none;background-color:inherit;display:inline-block;"></span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="font-family:'Microsoft YaHei';font-size:14px;"><br style="background-color:inherit;"></span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<span style="font-family:'Microsoft YaHei';font-size:14px;"><br style="background-color:inherit;"></span></div>
<div style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:19.0909px;">
<div style="background-color:inherit;"><span style="font-family:'Microsoft YaHei';font-size:14px;">   <span style="line-height:19.0909px;">    </span>看来我们的学习，将从Spark Streaming的现象开始，深入到Spark Core和Spark Streaming的本质。</span></div>
<div style="background-color:inherit;"><span style="font-family:'Microsoft YaHei';font-size:14px;">   <span style="line-height:19.0909px;">    </span>正巧还在着手编写Spark Streaming源码剖析的书。有王家林这样的老师指引方向，这本书可以写得相当有料。</span></div>
<div style="background-color:inherit;"><span style="font-family:'Microsoft YaHei';font-size:14px;"><br></span></div>
<div style="background-color:inherit;"><span style="font-family:'Microsoft YaHei';font-size:14px;"></span>
<p style="font-family:'微软雅黑';font-size:14.4px;line-height:1.5;">
<span style="font-family:'Microsoft Yahei';background-color:inherit;font-size:14px;">备注：</span></p>
<p style="font-family:'微软雅黑';font-size:14.4px;line-height:1.5;">
<span style="background-color:inherit;font-size:14px;font-family:'Microsoft Yahei';line-height:1.5;">更多私密内容，请关注<a href="http://lib.csdn.net/base/wechat" rel="nofollow" class="replace_word" title="微信开发知识库" style="text-decoration:none;color:rgb(223,52,52);font-weight:bold;">微信</a>公众号：DT_Spark</span></p>
<p style="font-family:'微软雅黑';font-size:14.4px;line-height:1.5;">
<span style="font-family:'Microsoft Yahei';background-color:inherit;font-size:14px;">如果您对<a href="http://lib.csdn.net/base/hadoop" rel="nofollow" class="replace_word" title="Hadoop知识库" style="text-decoration:none;color:rgb(223,52,52);font-weight:bold;">大数据</a><a href="http://lib.csdn.net/base/spark" rel="nofollow" class="replace_word" title="Apache Spark知识库" style="text-decoration:none;color:rgb(223,52,52);font-weight:bold;">Spark</a>感兴趣，可以免费听由王家林老师每天晚上20：00开设的Spark永久免费公开课，地址YY房间号：68917580</span></p>
</div>
</div>
            </div>
                </div>