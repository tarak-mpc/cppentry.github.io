---
layout:     post
title:      为什么越来越多的公司在使用Spark Streaming
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div class="mscc">
<h1 class="mscctitle" style="line-height:33px;">
<a href="http://dataunion.org/21152.html" rel="nofollow" style="color:rgb(214,98,61);font-size:25px;">为</a></h1>
<h1 class="mscctitle" style="line-height:33px;">
<a href="http://dataunion.org/21152.html" rel="nofollow" style="color:rgb(214,98,61);font-size:25px;">什么越来越多的公司在使用Spark Streaming</a></h1>
<address class="msccaddress" style="color:rgb(162,162,162);font-style:normal;">
<span style="color:rgb(214,98,61);">1,122 次阅读 -</span> <a href="http://dataunion.org/category/tech/base" rel="nofollow" style="color:rgb(162,162,162);">基础架构</a></address>
</div>
<div class="content-text" style="color:rgb(101,101,101);font-family:'Microsoft Yahei', '冬青黑体简体中文 w3';font-size:13px;letter-spacing:.5px;">
<p style="clear:both;font-size:16px;line-height:33px;text-indent:35px;color:rgb(85,85,85);">
<span>作者：<span class="author_general"><a class="editorlink f_taxonomyEditor" href="http://www.infoq.com/cn/author/%E5%AD%99%E9%95%9C%E6%B6%9B" rel="nofollow" style="color:rgb(102,102,102);font-size:13px;">孙镜涛</a></span></span></p>
<p style="clear:both;font-size:16px;line-height:33px;text-indent:35px;color:rgb(85,85,85);">
Databricks最近对1400多家Spark用户进行了一次调查，结果显示这些用户对Spark Streaming的使用率与2014年相比增长了56%，另外，有48%的受访者将Spark Streaming标记为最常用的Spark组件。在Spark Streaming不断增长的用户群中，Uber、Netflix和Pinterest等家喻户晓的公司赫然在列，那么为什么使用Spark Streaming加速业务发展的公司越来越多呢？最近Spark Streaming的主要开发人员Tathagata Das在DataNami上发表了一篇文章，<a href="http://www.datanami.com/2015/11/30/spark-streaming-what-is-it-and-whos-using-it/" rel="nofollow" style="color:rgb(102,102,102);font-size:13px;">从需求、功能以及用户场景等方面对此进行了介绍</a>。</p>
<p style="clear:both;font-size:16px;line-height:33px;text-indent:35px;color:rgb(85,85,85);">
<span>流分析的需求</span></p>
<p style="clear:both;font-size:16px;line-height:33px;text-indent:35px;color:rgb(85,85,85);">
现在，几乎所有的公司都是一家软件公司，它们实时地监控传感器、物联网设备、社交网络和在线事务系统产生的数据，然后通过大规模、实时的流处理系统 对其进行分析从而实现快速响应。此外，公司还会使用这些数据生成日报和业务模型。也就是说，现代流处理框架不仅需要应对实时场景，还需要处理预处理和后处 理等非实时场景。</p>
<p style="clear:both;font-size:16px;line-height:33px;text-indent:35px;color:rgb(85,85,85);">
例如，电商需要对用户在线购买时产生的数据（包括日期、时间、物品、价格等）进行实时分析完成广告推送和相关性推荐。银行需要使用训练好的欺诈模型实时地对每一笔交易进行检测从而定位欺诈行为。</p>
<p style="clear:both;font-size:16px;line-height:33px;text-indent:35px;color:rgb(85,85,85);">
Spark Streaming不仅能够非常好地解决这些问题，同时它还统一了技术框架，使用了与Spark一致的编程模型和处理引擎。而在Spark Streaming出现之前，用户需要借助多种开源软件才能构建出具有流处理、批处理和机器学习能力的系统。</p>
<p style="clear:both;font-size:16px;line-height:33px;text-indent:35px;color:rgb(85,85,85);">
<img src="http://cdn1.infoqstatic.com/statics_s1_20151224-0209/resource/news/2016/01/spark-streaming-what-is-it-and-w/zh/resources/1.png" alt="" width="530" style="border:0px;display:block;"></p>
<p style="clear:both;font-size:16px;line-height:33px;text-indent:35px;color:rgb(85,85,85);">
<span>Spark Streaming的能力</span></p>
<p style="clear:both;font-size:16px;line-height:33px;text-indent:35px;color:rgb(85,85,85);">
Spark Streaming是在2013年被添加到Apache Spark中的，作为核心Spark API的扩展它允许数据工程师和数据科学家实时地处理来自于Kafka、Flume以及Amazon Kinesis等多种源的数据。这种对不同数据的统一处理能力就是Spark Streaming会被大家迅速采用的关键原因之一。<br>
Spark Streaming能够按照batch size（如1秒）将输入数据分成一段段的离散数据流（Discretized Stream，即DStream），这些流具有与RDD一致的核心数据抽象，能够与MLlib和Spark SQL等Spark组件无缝集成。<br>
通过Spark Streaming开发者能够容易地使用一种框架满足所有的处理需求，例如通过MLlib离线训练模型，然后直接在Spark Streaming中使用训练好的模型在线处理实时数据。同时，开发者编写的代码和业务逻辑也能够在流处理、批处理和交互式处理引擎中共享和重用。此外， 流数据源中的数据还可以与很多其他的Spark SQL能够访问的静态数据源进行联合。例如，在将Amazon Redshift的静态数据推送到下游系统之前，可以先将其加载到Spark的内存中进行处理以丰富流数据。</p>
<p style="clear:both;font-size:16px;line-height:33px;text-indent:35px;color:rgb(85,85,85);">
<span>用例——从Uber到Pinterest</span></p>
<p style="clear:both;font-size:16px;line-height:33px;text-indent:35px;color:rgb(85,85,85);">
虽然针对不同的目标和业务案例使用Spark Streaming的方式也不同，但其主要场景包括：</p>
<ul style="list-style:none;"><li style="list-style:none;">流ETL——将数据推入存储系统之前对其进行清洗和聚合</li><li style="list-style:none;">触发器——实时检测异常行为并触发相关的处理逻辑</li><li style="list-style:none;">数据浓缩——将实时数据与静态数据浓缩成更为精炼的数据以用于实时分析</li><li style="list-style:none;">复杂会话和持续学习——将与实时会话相关的事件（例如用户登陆Web网站或者应用程序之后的行为）组合起来进行分析。</li></ul><p style="clear:both;font-size:16px;line-height:33px;text-indent:35px;color:rgb(85,85,85);">
例如，Uber通过Kafka、Spark Streaming和HDFS构建了持续性的ETL管道，该管道首先对每天从移动用户那里收集到的TB级的事件数据进行转换，将原始的非结构化事件数据转 换成结构化的数据，然后再进行实时地遥测分析。Pinterest的ETL数据管道始于Kafka，通过Spark Streaming将数据推入Spark中实时分析全球用户对Pin的使用情况，从而优化推荐引擎为用户显示更相关的Pin。Netflix也是通过 Kafka和Spark Streaming构建了实时引擎，对每天从各种数据源接收到的数十亿事件进行分析完成电影推荐。</p>
<p style="clear:both;font-size:16px;line-height:33px;text-indent:35px;color:rgb(85,85,85);">
 </p>
</div>
<div style="color:rgb(101,101,101);font-family:'Microsoft Yahei', '冬青黑体简体中文 w3';font-size:13px;letter-spacing:.5px;">
<span>注：转载文章均来自于公开网络，仅供学习使用，不会用于任何商业用途，如果侵犯到原作者的权益，请您与我们联系删除或者授权事宜，联系邮箱：contact@dataunion.org。转载数盟网站文章请注明原文章作者，否则产生的任何版权纠纷与数盟无关。</span></div>
            </div>
                </div>