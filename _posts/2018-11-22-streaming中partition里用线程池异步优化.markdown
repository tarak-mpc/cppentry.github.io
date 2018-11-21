---
layout:     post
title:      streaming中partition里用线程池异步优化
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<fieldset style="min-width:0px;border-width:1px;border-style:solid;border-color:rgb(0,187,236);color:rgb(51,51,51);font-family:'微软雅黑';background-color:rgb(248,247,245);"><legend>
<span class="main" style="color:rgb(255,255,255);font-weight:bold;font-size:14px;background-color:rgb(0,187,236);"><span style="line-height:24px;">点击hadoop123</span><img src="http://mmbiz.qpic.cn/mmbiz/xT7yN7TENODUPIKsMKdoQwbqBNWQUywCOkrPoF2lDgPERNak9vmYvMCW1VzjZSdtakKbZChnIvW5lqwibOOmnUA/640?&amp;tp=webp&amp;wxfrom=5&amp;wx_lazy=1" alt="" style="border:0px;line-height:24px;visibility:visible !important;"><span style="line-height:24px;">关注我哟</span></span></legend>
<blockquote style="border-left:3px solid rgb(219,219,219);">
<p style="clear:both;min-height:1em;">
<span style="line-height:24px;color:rgb(255,192,0);font-size:14px;">☀</span><span style="color:rgb(127,127,127);line-height:28px;"></span>最知名的<a href="http://lib.csdn.net/base/hadoop" rel="nofollow" class="replace_word" title="Hadoop知识库" style="color:rgb(223,52,52);font-weight:bold;">Hadoop</a>/<a href="http://lib.csdn.net/base/spark" rel="nofollow" class="replace_word" title="Apache Spark知识库" style="color:rgb(223,52,52);font-weight:bold;">Spark</a><a href="http://lib.csdn.net/base/hadoop" rel="nofollow" class="replace_word" title="Hadoop知识库" style="color:rgb(223,52,52);font-weight:bold;">大数据</a>技术分享基地，分享<span style="color:rgb(247,150,70);">hadoop/spark技术内幕</span>，<span style="color:rgb(0,176,80);">hadoop/spark最新技术进展</span>，<span style="color:rgb(255,0,0);">hadoop/spark行业技术应用</span>，<span style="color:rgb(128,100,162);">发布hadoop/spark相关职位和求职信息</span>，<span style="color:rgb(75,172,198);">hadoop/spark技术交流聚会</span>、<span style="color:rgb(79,129,189);">讲座以及会议</span>等。</p>
</blockquote>
</fieldset><p style="color:rgb(62,62,62);font-family:'Helvetica Neue', Helvetica, 'Hiragino Sans GB', 'Microsoft YaHei', Arial, sans-serif;font-size:16px;clear:both;min-height:1em;line-height:25.6px;">
<br></p>
<hr class="awb-s1" style="color:rgb(51,51,51);font-family:'微软雅黑';border-width:5px 0px 0px;border-top-color:rgb(0,187,236);border-top-style:double;"><p style="color:rgb(62,62,62);font-family:'Helvetica Neue', Helvetica, 'Hiragino Sans GB', 'Microsoft YaHei', Arial, sans-serif;font-size:16px;clear:both;min-height:1em;line-height:25.6px;">
<br></p>
<blockquote style="border:3px dashed rgb(0,187,236);color:rgb(68,68,68);font-family:'微软雅黑';font-size:13px;line-height:24px;">
<p style="clear:both;min-height:1em;">
作者：徐鑫</p>
<p style="clear:both;min-height:1em;">
作者简介：毕业于清华大学，就职于Hulu大数据基础平台部门，专注于大数据应用，现负责用户数据平台Lambda<a href="http://lib.csdn.net/base/architecture" rel="nofollow" class="replace_word" title="大型网站架构知识库" style="color:rgb(223,52,52);font-weight:bold;">架构</a>和Market Segment Analysis系统。 </p>
<p style="clear:both;min-height:1em;">
责任编辑：仲浩（zhonghao@csdn<a href="http://lib.csdn.net/base/dotnet" rel="nofollow" class="replace_word" title=".NET知识库" style="color:rgb(223,52,52);font-weight:bold;">.NET</a>） </p>
<p style="clear:both;min-height:1em;">
文章来源：《程序员》2月期 大数据专刊</p>
<p style="clear:both;min-height:1em;">
版权声明：本文为《程序员》原创文章，未经允许不得转载，原文链接：<span style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:12px;line-height:21.6px;">http://geek.csdn<a href="http://lib.csdn.net/base/dotnet" rel="nofollow" class="replace_word" title=".NET知识库" style="color:rgb(223,52,52);font-weight:bold;">.Net</a>/news/detail/54500</span></p>
</blockquote>
<p style="color:rgb(62,62,62);font-family:'Helvetica Neue', Helvetica, 'Hiragino Sans GB', 'Microsoft YaHei', Arial, sans-serif;font-size:16px;clear:both;min-height:1em;">
<span style="font-size:21px;line-height:24.15px;font-family:'宋体';"><br></span></p>
<p style="color:rgb(62,62,62);font-family:'Helvetica Neue', Helvetica, 'Hiragino Sans GB', 'Microsoft YaHei', Arial, sans-serif;font-size:16px;clear:both;min-height:1em;">
<span style="font-size:21px;line-height:24.15px;font-family:'宋体';"></span></p>
<hr class="awb-s1" style="color:rgb(51,51,51);font-family:'微软雅黑';border-width:5px 0px 0px;border-top-color:rgb(0,187,236);border-top-style:double;"><p style="color:rgb(62,62,62);font-family:'Helvetica Neue', Helvetica, 'Hiragino Sans GB', 'Microsoft YaHei', Arial, sans-serif;font-size:16px;clear:both;min-height:1em;">
<span style="font-size:21px;line-height:24.15px;font-family:'宋体';"><br></span></p>
<p style="color:rgb(62,62,62);font-family:'Helvetica Neue', Helvetica, 'Hiragino Sans GB', 'Microsoft YaHei', Arial, sans-serif;font-size:16px;clear:both;min-height:1em;">
<span style="font-size:21px;line-height:24.15px;font-family:'宋体';">一、</span><span style="font-size:21px;line-height:24.15px;">Spark
 Streaming</span><span style="font-size:21px;line-height:24.15px;font-family:'宋体';">概述</span><br><span style="font-size:21px;line-height:24.15px;font-family:'宋体';"></span></p>
<p style="color:rgb(62,62,62);font-family:'Helvetica Neue', Helvetica, 'Hiragino Sans GB', 'Microsoft YaHei', Arial, sans-serif;font-size:16px;clear:both;min-height:1em;text-indent:32px;">
Spark<span style="font-family:'宋体';">是美国</span><span style="font-family:'宋体';">加州</span><span style="font-family:'宋体';">伯克利大学</span>AMP<span style="font-family:'宋体';">实验室推出的新一代分布式计算框架，其核心概念是</span>RDD<span style="font-family:'宋体';">，一个只读的、有容错机制的分布式数据集，</span>RDD<span style="font-family:'宋体';">可以全部缓存在内存中，并在多次计算中重复使用。相比于</span>MapReduce<span style="font-family:'宋体';">编程模型，</span>Spark<span style="font-family:'宋体';">具有</span><span style="font-family:'宋体';">以下几个优点：</span></p>
<ol class="list-paddingleft-2" style="color:rgb(62,62,62);font-family:'Helvetica Neue', Helvetica, 'Hiragino Sans GB', 'Microsoft YaHei', Arial, sans-serif;font-size:16px;list-style-type:lower-alpha;"><li>
<p style="clear:both;min-height:1em;text-indent:32px;">
<span style="font-family:'宋体';line-height:1.6;">更大的灵活性和更高的抽象层次，使得用户用更少的代码即可实现同样的功能；</span></p>
</li><li>
<p style="clear:both;min-height:1em;text-indent:32px;">
<span style="line-height:1.6;font-family:'宋体';">适合迭代<a href="http://lib.csdn.net/base/datastructure" rel="nofollow" class="replace_word" title="算法与数据结构知识库" style="color:rgb(223,52,52);font-weight:bold;">算法</a>，在</span><span style="line-height:1.6;">MapReduce</span><span style="line-height:1.6;font-family:'宋体';">编程模型中，每一轮迭代都需要读写一次</span><span style="line-height:1.6;">HDFS</span><span style="line-height:1.6;font-family:'宋体';">，磁盘</span><span style="line-height:1.6;">IO</span><span style="line-height:1.6;font-family:'宋体';">负载很大，相比之下，</span><span style="line-height:1.6;">Spark</span><span style="line-height:1.6;font-family:'宋体';">中的</span><span style="line-height:1.6;">RDD</span><span style="line-height:1.6;font-family:'宋体';">可以缓存在内存中，只需第一次读入</span><span style="line-height:1.6;">HDFS</span><span style="line-height:1.6;font-family:'宋体';">文件，之后迭代的数据全都存储在内存中，这使得程序的计算速度可提升约</span><span style="line-height:1.6;">10-100</span><span style="line-height:1.6;font-family:'宋体';">倍。</span></p>
</li></ol><p style="color:rgb(62,62,62);font-family:'Helvetica Neue', Helvetica, 'Hiragino Sans GB', 'Microsoft YaHei', Arial, sans-serif;font-size:16px;clear:both;min-height:1em;text-indent:32px;">
SparkStreaming<span style="font-family:'宋体';">是</span>Spark<span style="font-family:'宋体';">生态系统中的重要组成部分，在实现上复用</span>Spark<span style="font-family:'宋体';">计算引擎。</span><span style="font-family:'宋体';">如图</span>1<span style="font-family:'宋体';">所示，</span>Spark
 Streaming<span style="font-family:'宋体';">支持</span><span style="font-family:'宋体';">的</span><span style="font-family:'宋体';">数据源有很多，如</span>Kafka<span style="font-family:'宋体';">、</span>Flume<span style="font-family:'宋体';">、</span>TCP<span style="font-family:'宋体';">等。</span>SparkStreaming<span style="font-family:'宋体';">内部的</span><span style="font-family:'宋体';">数据表示形式为</span>DStream<span style="font-family:'宋体';">（</span>DiscretizedStream<span style="font-family:'宋体';">，</span><span style="font-family:'宋体';">离散的数据流</span><span style="font-family:'宋体';">）</span><span style="font-family:'宋体';">，</span><span style="font-family:'宋体';">其</span><span style="font-family:'宋体';">接口设计与</span>RDD<span style="font-family:'宋体';">非常相似，</span><span style="font-family:'宋体';">这使得它</span><span style="font-family:'宋体';">对</span>Spark<span style="font-family:'宋体';">用户非常友好。</span>SparkStreaming<span style="font-family:'宋体';">的核心思想是把流式处理转化为“微批处理”，即以时间为单位切分数据流，每个切片内的数据对应一个</span>RDD<span style="font-family:'宋体';">，进而可以采用</span>Spark<span style="font-family:'宋体';">引擎进行快速计算。由于</span>SparkStreaming<span style="font-family:'宋体';">采用了微批处理方式，是近实时的处理系统，而不是严格意义上的流式处理系统。</span></p>
<p style="color:rgb(62,62,62);font-family:'Helvetica Neue', Helvetica, 'Hiragino Sans GB', 'Microsoft YaHei', Arial, sans-serif;font-size:16px;clear:both;min-height:1em;text-indent:32px;text-align:center;">
<span style="font-size:11px;line-height:12.65px;font-family:'宋体';"><img src="http://mmbiz.qpic.cn/mmbiz/zX4KydZIKdD7jFxhzibGqibjibzqDL6R3o9103ZHmTlsSeJwArPImuzibUuDMuibRO2tiaVya8Rt89drfPU3TKsmWib2Q/640?wx_fmt=png&amp;tp=webp&amp;wxfrom=5&amp;wx_lazy=1" alt="" style="border:none;visibility:visible !important;"></span></p>
<p style="color:rgb(62,62,62);font-family:'Helvetica Neue', Helvetica, 'Hiragino Sans GB', 'Microsoft YaHei', Arial, sans-serif;font-size:16px;clear:both;min-height:1em;text-align:center;">
<span style="font-size:11px;line-height:12.65px;font-family:'宋体';">图</span><span style="font-size:11px;line-height:12.65px;">1</span><span style="font-size:11px;line-height:12.65px;font-family:'宋体';">：</span><span style="font-size:11px;line-height:12.65px;">Spark
 Streaming</span><span style="font-size:11px;line-height:12.65px;font-family:'宋体';">数据流</span></p>
<p style="color:rgb(62,62,62);font-family:'Helvetica Neue', Helvetica, 'Hiragino Sans GB', 'Microsoft YaHei', Arial, sans-serif;font-size:16px;clear:both;min-height:1em;">
        <span style="font-family:'宋体';">
另一个著名的开源流式计算引擎是</span>Storm<span style="font-family:'宋体';">，这是一个真正的流式处理系统，它每次从数据源读一条数据，然后单独处理。相比于</span>SparkStreaming<span style="font-family:'宋体';">，</span>Storm<span style="font-family:'宋体';">有更快速的响应时间（小于一秒），更适合低延迟的应用场景，比如信用卡欺诈系统，广告系统等。相比之下，</span>SparkStreaming<span style="font-family:'宋体';">的优势是吞吐量大，响应时间也可以接受（秒级），并且兼容</span>Spark<span style="font-family:'宋体';">系统中的其他工具库如</span>MLlib<span style="font-family:'宋体';">和</span>GraphX<span style="font-family:'宋体';">。对于时间不敏感且流量很大的系统，</span>Spark
 Streaming<span style="font-family:'宋体';">是更优的选择。</span></p>
<p style="color:rgb(62,62,62);font-family:'Helvetica Neue', Helvetica, 'Hiragino Sans GB', 'Microsoft YaHei', Arial, sans-serif;font-size:16px;clear:both;min-height:1em;">
 </p>
<p style="color:rgb(62,62,62);font-family:'Helvetica Neue', Helvetica, 'Hiragino Sans GB', 'Microsoft YaHei', Arial, sans-serif;font-size:16px;clear:both;min-height:1em;">
<span style="font-size:21px;line-height:24.15px;font-family:'宋体';">二、</span><span style="font-size:21px;line-height:24.15px;">Spark
 Streaming</span><span style="font-size:21px;line-height:24.15px;font-family:'宋体';">在</span><span style="font-size:21px;line-height:24.15px;">Hulu</span><span style="font-size:21px;line-height:24.15px;font-family:'宋体';">应用</span></p>
<p style="color:rgb(62,62,62);font-family:'Helvetica Neue', Helvetica, 'Hiragino Sans GB', 'Microsoft YaHei', Arial, sans-serif;font-size:16px;clear:both;min-height:1em;text-indent:32px;">
Hulu<span style="font-family:'宋体';">是美国的专业在线视频网站，每天会有大量用户在线观看视频，进而产生大量用户观看行为数据，这些数据通过收集系统进入</span>Hulu<span style="font-family:'宋体';">的大数据平台，从而进行存储和进一步处理。在大数据平台之上，各个团队会根据需要设计相应的算法对数据进行分析和挖掘以便产生商业价值：推荐团队从这些数据里挖掘出用户感兴趣的内容并做精准推荐，广告团队根据用户的历史行为推送最合适的广告，数据团队从数据的各个维度进行分析从而为公司的策略制定提供可靠依据。</span></p>
<p style="color:rgb(62,62,62);font-family:'Helvetica Neue', Helvetica, 'Hiragino Sans GB', 'Microsoft YaHei', Arial, sans-serif;font-size:16px;clear:both;min-height:1em;text-indent:32px;">
Hulu<span style="font-family:'宋体';">大数据平台的实现依循</span>Lambda<span style="font-family:'宋体';">架构。</span>Lambda<span style="font-family:'宋体';">架构是一个通用的大数据处理框架，</span><span style="font-family:'宋体';">包含离线的批处理层、在线的加速层和服务层三部分，具体如图</span>2<span style="font-family:'宋体';">所示。</span><span style="font-family:'宋体';">服务层</span><span style="font-family:'宋体';">一般使用</span>HTTP<span style="font-family:'宋体';">服务或自定制的客户端</span><span style="font-family:'宋体';">对外提供数据访问</span><span style="font-family:'宋体';">，离线的批处理层一般使用</span><span style="font-family:'宋体';">批处理计算框架</span>Spark<span style="font-family:'宋体';">和</span>MapReduce<span style="font-family:'宋体';">进行数据分析</span><span style="font-family:'宋体';">，在线的加速层一般</span><span style="font-family:'宋体';">使用流式实时计算框架</span>SparkStreaming<span style="font-family:'宋体';">和</span>Storm<span style="font-family:'宋体';">进行数据分析</span><span style="font-family:'宋体';">。</span></p>
<p style="color:rgb(62,62,62);font-family:'Helvetica Neue', Helvetica, 'Hiragino Sans GB', 'Microsoft YaHei', Arial, sans-serif;font-size:16px;clear:both;min-height:1em;text-indent:32px;">
<span style="font-family:'宋体';"><br></span></p>
<p style="color:rgb(62,62,62);font-family:'Helvetica Neue', Helvetica, 'Hiragino Sans GB', 'Microsoft YaHei', Arial, sans-serif;font-size:16px;clear:both;min-height:1em;text-align:center;">
<img src="http://blog.csdn.net/xiao_jun_0820/article/details/50715623" alt="" style="border:none;visibility:visible !important;"><br></p>
<p style="color:rgb(62,62,62);font-family:'Helvetica Neue', Helvetica, 'Hiragino Sans GB', 'Microsoft YaHei', Arial, sans-serif;font-size:16px;clear:both;min-height:1em;text-align:center;">
<span style="font-size:11px;line-height:12.65px;font-family:'宋体';">图</span><span style="font-size:11px;line-height:12.65px;">2</span><span style="font-size:11px;line-height:12.65px;font-family:'宋体';">：</span><span style="font-size:11px;line-height:12.65px;">lambda</span><span style="font-size:11px;line-height:12.65px;font-family:'宋体';">架构原理图</span></p>
<p style="color:rgb(62,62,62);font-family:'Helvetica Neue', Helvetica, 'Hiragino Sans GB', 'Microsoft YaHei', Arial, sans-serif;font-size:16px;clear:both;min-height:1em;text-indent:32px;">
<span style="font-family:'宋体';">对于</span><span style="font-family:'宋体';">实时计算部分，</span>Hulu<span style="font-family:'宋体';">内部使用了</span>Kafka<span style="font-family:'宋体';">、</span>Codis<span style="font-family:'宋体';">和</span>SparkStreaming<span style="font-family:'宋体';">。</span><span style="font-family:'宋体';">下</span><span style="font-family:'宋体';">面按照数据流的过程，介绍我们的项目。</span></p>
<p style="color:rgb(62,62,62);font-family:'Helvetica Neue', Helvetica, 'Hiragino Sans GB', 'Microsoft YaHei', Arial, sans-serif;font-size:16px;clear:both;min-height:1em;">
1.<span style="font-size:9px;line-height:12.5455px;">    </span><span style="font-family:'宋体';">收集数据</span> -<span style="font-family:'宋体';">从服务器日志中收集数据，流程如图</span>3<span style="font-family:'宋体';">所示：</span></p>
<ol class="list-paddingleft-2" style="color:rgb(62,62,62);font-family:'Helvetica Neue', Helvetica, 'Hiragino Sans GB', 'Microsoft YaHei', Arial, sans-serif;font-size:16px;list-style-type:lower-alpha;"><li>
<p style="margin-left:56px;clear:both;min-height:1em;">
<span style="line-height:1.6;font-family:'宋体';">来自网页、手机</span><span style="line-height:1.6;">App</span><span style="line-height:1.6;font-family:'宋体';">、机顶盒等设备的用户产生视频观看、广告点击等行为，这些行为数据记录在各自的</span><span style="line-height:1.6;">Nginx</span><span style="line-height:1.6;font-family:'宋体';">服务的日志中；</span><br></p>
</li><li>
<p style="margin-left:56px;clear:both;min-height:1em;">
<span style="line-height:1.6;font-family:'宋体';">使用</span><span style="line-height:1.6;">Flume</span><span style="line-height:1.6;font-family:'宋体';">将用户行为数据同时导入</span><span style="line-height:1.6;">HDFS</span><span style="line-height:1.6;font-family:'宋体';">和</span><span style="line-height:1.6;">Kafka</span><span style="line-height:1.6;font-family:'宋体';">，其中</span><span style="line-height:1.6;">HDFS</span><span style="line-height:1.6;font-family:'宋体';">中的数据用</span><span style="line-height:1.6;font-family:'宋体';">于离线分析，而</span><span style="line-height:1.6;">Kafka</span><span style="line-height:1.6;font-family:'宋体';">中数据则用于流式实时分析。</span><img src="http://blog.csdn.net/xiao_jun_0820/article/details/50715623" alt="" style="border:none;font-family:'宋体';text-align:center;line-height:1.6;visibility:visible !important;"></p>
</li></ol><p style="color:rgb(62,62,62);font-family:'Helvetica Neue', Helvetica, 'Hiragino Sans GB', 'Microsoft YaHei', Arial, sans-serif;font-size:16px;clear:both;min-height:1em;text-align:center;">
<span style="font-size:11px;line-height:12.65px;font-family:'宋体';">图</span><span style="font-size:11px;line-height:12.65px;">3</span><span style="font-size:11px;line-height:12.65px;font-family:'宋体';">：</span><span style="font-size:11px;line-height:12.65px;">Hulu</span><span style="font-size:11px;line-height:12.65px;font-family:'宋体';">数据收集流程</span></p>
<p style="color:rgb(62,62,62);font-family:'Helvetica Neue', Helvetica, 'Hiragino Sans GB', 'Microsoft YaHei', Arial, sans-serif;font-size:16px;clear:both;min-height:1em;">
2.<span style="font-size:9px;line-height:12.5455px;">   </span> <span style="font-family:'宋体';">存储标签数据</span> - Hulu<span style="font-family:'宋体';">使用</span><a href="http://lib.csdn.net/base/hbase" rel="nofollow" class="replace_word" title="Hbase知识库" style="color:rgb(223,52,52);font-weight:bold;">Hbase</a><span style="font-family:'宋体';">存储用户标签数据，包括基本信息如性别、年龄、是否付费，以及其他模型推测出来的偏好属性。这些属性需要作为计算模型的输入，同时</span>HBase<span style="font-family:'宋体';">随机读取的速度比较慢，所以需要将数据同步到缓存服务器中以加快数据读取速度。</span><a href="http://lib.csdn.net/base/redis" rel="nofollow" class="replace_word" title="Redis知识库" style="color:rgb(223,52,52);font-weight:bold;">Redis</a><span style="font-family:'宋体';">是一个应用广泛的开源缓存服务器，</span><span style="font-family:'宋体';">一个免费开源的高性能</span>Key-Value<span style="font-family:'宋体';"><a href="http://lib.csdn.net/base/mysql" rel="nofollow" class="replace_word" title="MySQL知识库" style="color:rgb(223,52,52);font-weight:bold;">数据库</a>，但其本身是个单机系统，不能很好地支持大量数据的缓存。为解决</span>Redis<span style="font-family:'宋体';">扩展性差的问题，豌豆荚开源了</span>Codis<span style="font-family:'宋体';">，一个分布式</span>Redis<span style="font-family:'宋体';">解决方案。</span>Hulu<span style="font-family:'宋体';">将</span>Codis<span style="font-family:'宋体';">打成</span><a href="http://lib.csdn.net/base/docker" rel="nofollow" class="replace_word" title="Docker知识库" style="color:rgb(223,52,52);font-weight:bold;">Docker</a><span style="font-family:'宋体';">镜像，并实现一键式构建缓存系统，附带自动监控和修复功能。为了更精细的监控，我们构建了多个</span>Codis<span style="font-family:'宋体';">缓存，分别是：</span></p>
<ol class="list-paddingleft-2" style="color:rgb(62,62,62);font-family:'Helvetica Neue', Helvetica, 'Hiragino Sans GB', 'Microsoft YaHei', Arial, sans-serif;font-size:16px;list-style-type:lower-alpha;"><li>
<p style="clear:both;min-height:1em;">
<span style="line-height:1.6;font-family:Wingdings;"><span style="font-size:9px;line-height:normal;">  </span></span><span style="line-height:1.6;">codis-profile</span><span style="line-height:1.6;font-family:'宋体';">，同步</span><span style="line-height:1.6;">HBase</span><span style="line-height:1.6;font-family:'宋体';">中的用户属性；</span></p>
</li><li>
<p style="clear:both;min-height:1em;">
<span style="line-height:1.6;font-family:Wingdings;"><span style="font-size:9px;line-height:normal;">  </span></span><span style="line-height:1.6;">codis-action</span><span style="line-height:1.6;font-family:'宋体';">，缓存来自</span><span style="line-height:1.6;">Kafka</span><span style="line-height:1.6;font-family:'宋体';">的用户行为；</span></p>
</li><li>
<p style="clear:both;min-height:1em;">
<span style="line-height:1.6;font-family:Wingdings;"><span style="font-size:9px;line-height:normal;">  </span></span><span style="line-height:1.6;">codis-result</span><span style="line-height:1.6;font-family:'宋体';">，记录计算结果</span><span style="line-height:1.6;font-family:'宋体';">。</span></p>
</li></ol><p style="color:rgb(62,62,62);font-family:'Helvetica Neue', Helvetica, 'Hiragino Sans GB', 'Microsoft YaHei', Arial, sans-serif;font-size:16px;clear:both;min-height:1em;">
3.<span style="font-size:9px;line-height:12.5455px;">  </span>  <span style="font-family:'宋体';">实时处理数据</span> -<span style="font-family:'宋体';">准备就绪，启动</span>Spark
 Streaming<span style="font-family:'宋体';">程序</span><span style="font-family:'宋体';">：</span></p>
<p style="color:rgb(62,62,62);font-family:'Helvetica Neue', Helvetica, 'Hiragino Sans GB', 'Microsoft YaHei', Arial, sans-serif;font-size:16px;margin-left:56px;clear:both;min-height:1em;">
1)<span style="font-size:9px;line-height:12.5455px;">     </span>SparkStreaming<span style="font-family:'宋体';">启动</span>Kafka
 Receiver<span style="font-family:'宋体';">，持续</span><span style="font-family:'宋体';">地</span><span style="font-family:'宋体';">从</span>Kafka<span style="font-family:'宋体';">服务器拉</span><span style="font-family:'宋体';">取</span><span style="font-family:'宋体';">数据；</span></p>
<p style="color:rgb(62,62,62);font-family:'Helvetica Neue', Helvetica, 'Hiragino Sans GB', 'Microsoft YaHei', Arial, sans-serif;font-size:16px;margin-left:56px;clear:both;min-height:1em;">
2)<span style="font-size:9px;line-height:12.5455px;">     </span><span style="font-family:'宋体';">每隔两秒，</span>Kafka<span style="font-family:'宋体';">的数据被整理成一个</span>RDD<span style="font-family:'宋体';">，交给</span>Spark<span style="font-family:'宋体';">引擎处理；</span></p>
<p style="color:rgb(62,62,62);font-family:'Helvetica Neue', Helvetica, 'Hiragino Sans GB', 'Microsoft YaHei', Arial, sans-serif;font-size:16px;margin-left:56px;clear:both;min-height:1em;">
3)<span style="font-size:9px;line-height:12.5455px;">     </span><span style="font-family:'宋体';">对一条用户行为，</span>Spark<span style="font-family:'宋体';">会从</span>codis-action<span style="font-family:'宋体';">缓存中拿到该用户的行为记录，然后把新的行为追加进去；</span></p>
<p style="color:rgb(62,62,62);font-family:'Helvetica Neue', Helvetica, 'Hiragino Sans GB', 'Microsoft YaHei', Arial, sans-serif;font-size:16px;margin-left:56px;clear:both;min-height:1em;">
4)<span style="font-size:9px;line-height:12.5455px;">     </span>Spark<span style="font-family:'宋体';">从</span>codis-action<span style="font-family:'宋体';">和</span>codis-profile<span style="font-family:'宋体';">中获得该用户的所有相关属性，然后</span><span style="font-family:'宋体';">执行</span><span style="font-family:'宋体';">广告</span><span style="font-family:'宋体';">和</span><span style="font-family:'宋体';">推荐的</span><span style="font-family:'宋体';">计算</span><span style="font-family:'宋体';">模型，最后把结果写入</span>codis-result<span style="font-family:'宋体';">，进而供服务层实时读取这些结果</span><span style="font-family:'宋体';">。</span></p>
<p style="color:rgb(62,62,62);font-family:'Helvetica Neue', Helvetica, 'Hiragino Sans GB', 'Microsoft YaHei', Arial, sans-serif;font-size:16px;clear:both;min-height:1em;">
 </p>
<p style="color:rgb(62,62,62);font-family:'Helvetica Neue', Helvetica, 'Hiragino Sans GB', 'Microsoft YaHei', Arial, sans-serif;font-size:16px;clear:both;min-height:1em;">
<span style="font-size:21px;line-height:24.15px;font-family:'宋体';">三、</span><span style="font-size:21px;line-height:24.15px;">Spark
 Streaming</span><span style="font-size:21px;line-height:24.15px;font-family:'宋体';">优化经验</span></p>
<p style="color:rgb(62,62,62);font-family:'Helvetica Neue', Helvetica, 'Hiragino Sans GB', 'Microsoft YaHei', Arial, sans-serif;font-size:16px;clear:both;min-height:1em;">
        <span style="font-family:'宋体';">
实践中，业务逻辑首先保证完成，使得在</span>Kafka<span style="font-family:'宋体';">输入数据量较小的情况下系统稳定运行，且输入输出满足项目需求。然后开始调优，修改</span>SparkStreaming<span style="font-family:'宋体';">的参数，比如</span>Executor<span style="font-family:'宋体';">的数量，</span>Core<span style="font-family:'宋体';">的数量，</span>Receiver<span style="font-family:'宋体';">的流量等。最后发现仅调参数无法完全满足本项目的业务场景，所以有更进一步的优化方案，总结如下：</span></p>
<p style="color:rgb(62,62,62);font-family:'Helvetica Neue', Helvetica, 'Hiragino Sans GB', 'Microsoft YaHei', Arial, sans-serif;font-size:16px;margin-left:.25in;clear:both;min-height:1em;">
1.<span style="font-size:9px;line-height:normal;">    </span>Executor<span style="font-family:'宋体';">初始化</span></p>
<p style="color:rgb(62,62,62);font-family:'Helvetica Neue', Helvetica, 'Hiragino Sans GB', 'Microsoft YaHei', Arial, sans-serif;font-size:16px;clear:both;min-height:1em;text-indent:32px;">
<span style="font-family:'宋体';">很多<a href="http://lib.csdn.net/base/machinelearning" rel="nofollow" class="replace_word" title="机器学习知识库" style="color:rgb(223,52,52);font-weight:bold;">机器学习</a>的模型在第一次运行时，需要执行初始化方法，还会连接外部的数据库，常常需要</span>5-10<span style="font-family:'宋体';">分钟，这会成为潜在的不稳定因素。在</span>Spark
 Streaming<span style="font-family:'宋体';">应用中，当</span>Receiver<span style="font-family:'宋体';">完成初始化，它就开始源源不断地接收数据，并且由</span>Driver<span style="font-family:'宋体';">定期调度任务消耗这些数据。如果刚启动时</span>Executor<span style="font-family:'宋体';">需要几分钟做准备，会导致第一个作业一直没有完成，这段时间内</span>Driver<span style="font-family:'宋体';">不会调度新的作业。这时候在</span>Kafka
 Receiver<span style="font-family:'宋体';">端会有数据积压，随着积压的数据量越来越大，大部分数据会撑过新生代进入老年代，进而给</span><a href="http://lib.csdn.net/base/java" rel="nofollow" class="replace_word" title="Java 知识库" style="color:rgb(223,52,52);font-weight:bold;">Java
</a>GC<span style="font-family:'宋体';">带来严重的压力，容易引发应用程序崩溃。</span></p>
<p style="color:rgb(62,62,62);font-family:'Helvetica Neue', Helvetica, 'Hiragino Sans GB', 'Microsoft YaHei', Arial, sans-serif;font-size:16px;clear:both;min-height:1em;text-indent:32px;">
<span style="font-family:'宋体';">本项目的解决方案是，修改</span>Spark<span style="font-family:'宋体';">内核，在每个</span>Executor<span style="font-family:'宋体';">接收任务之前先执行一个用户自定义的初始化函数，初始化函数中可以执行一些独立的用户逻辑。</span><span style="font-family:'宋体';">示例代码如下：</span></p>
<table cellspacing="0" cellpadding="0" width="320" style="color:rgb(62,62,62);font-family:'Helvetica Neue', Helvetica, 'Hiragino Sans GB', 'Microsoft YaHei', Arial, sans-serif;font-size:16px;border-collapse:collapse;width:670px;"><tbody><tr><td width="426" valign="top" style="border:1px solid #000000;">
<p style="clear:both;min-height:1em;">
  // sc:<span style="font-family:'宋体';">是</span>SparkContext, setupEnvironment<span style="font-family:'宋体';">是</span>Hulu<span style="font-family:'宋体';">扩展的</span>API</p>
<p style="clear:both;min-height:1em;">
  sc.setupEnvironment(() =&gt; {</p>
<p style="clear:both;min-height:1em;">
    application.initialize() // <span style="font-family:'宋体';">
用户应用程序初始化，需执行几分钟</span></p>
<p style="clear:both;min-height:1em;">
    println(“Invoke executor  setup method successfully.”)</p>
<p style="clear:both;min-height:1em;">
  })</p>
</td>
</tr></tbody></table><p style="color:rgb(62,62,62);font-family:'Helvetica Neue', Helvetica, 'Hiragino Sans GB', 'Microsoft YaHei', Arial, sans-serif;font-size:16px;clear:both;min-height:1em;">
<span style="font-family:'宋体';">该方案需要更改</span>Spark<span style="font-family:'宋体';">的任务调度器，首先将每个</span>Executor<span style="font-family:'宋体';">设置为未初始化状态。此时，调度器只会给未初始化状态的</span>Executor<span style="font-family:'宋体';">分配初始化任务（执行前面提到的初始化函数）。等初始化任务完毕，调度器更新</span>Executor<span style="font-family:'宋体';">的状态为已初始化，这样的</span>Executor<span style="font-family:'宋体';">才可以分配正常的计算任务。</span></p>
<p style="color:rgb(62,62,62);font-family:'Helvetica Neue', Helvetica, 'Hiragino Sans GB', 'Microsoft YaHei', Arial, sans-serif;font-size:16px;margin-left:.25in;clear:both;min-height:1em;">
2.<span style="font-size:9px;line-height:normal;">    </span><span style="font-family:'宋体';">异步处理</span>Task<span style="font-family:'宋体';">中的业务逻辑</span></p>
<p style="color:rgb(62,62,62);font-family:'Helvetica Neue', Helvetica, 'Hiragino Sans GB', 'Microsoft YaHei', Arial, sans-serif;font-size:16px;clear:both;min-height:1em;text-indent:32px;">
<span style="font-family:'宋体';">本项目中，模型的输入参数均来自</span>Codis<span style="font-family:'宋体';">，甚至模型内部也可能访问外部存储，直接导致模型计算时长不稳定，很多时间消耗在网络等待上。</span></p>
<p style="color:rgb(62,62,62);font-family:'Helvetica Neue', Helvetica, 'Hiragino Sans GB', 'Microsoft YaHei', Arial, sans-serif;font-size:16px;clear:both;min-height:1em;text-indent:32px;">
<span style="font-family:'宋体';">为提高系统吞吐量，增大并行度是比较通用的优化方案，但在本项目的场景中并不适用。因为</span>Spark<span style="font-family:'宋体';">作业的调度策略是，等待上一个作业的所有</span>Task<span style="font-family:'宋体';">执行完毕，然后调度下一个作业。如果单个</span>Task<span style="font-family:'宋体';">的运行时间不稳定，易发生个别</span>Task<span style="font-family:'宋体';">拖慢整个作业的情况，以至于资源利用率不高，系统吞吐量上不去；甚至并行度越大，该问题越严重。一种常用的解决</span>Task<span style="font-family:'宋体';">不稳定的方案是增大</span>Spark
 Streaming<span style="font-family:'宋体';">的</span>micro batch<span style="font-family:'宋体';">的时间间隔，该方案会使整个实时系统的延迟变长，并不推荐。</span></p>
<p style="color:rgb(62,62,62);font-family:'Helvetica Neue', Helvetica, 'Hiragino Sans GB', 'Microsoft YaHei', Arial, sans-serif;font-size:16px;clear:both;min-height:1em;text-indent:32px;">
<span style="font-family:'宋体';">该问题的解决方案是异步处理</span>Task<span style="font-family:'宋体';">中的业务逻辑。如下文的代码所示，同步方案中，</span>Task<span style="font-family:'宋体';">内执行业务逻辑，处理时间不定；异步方案中，</span>Task<span style="font-family:'宋体';">把业务逻辑嵌入线程，交给线程池执行，</span>Task<span style="font-family:'宋体';">立刻结束，</span>Executor<span style="font-family:'宋体';">向</span>Driver<span style="font-family:'宋体';">报告执行完毕，异步处理的时间非常短，在</span>100ms<span style="font-family:'宋体';">以内。另外，当线程池中积压的线程数量太大时（代码中</span>qsize&gt;100<span style="font-family:'宋体';">的情况），会暂时使用同步处理，配合反压机制（见下文的参数</span>spark.streaming.backpressure.enabled<span style="font-family:'宋体';">），可以保证不会因为数据积压过多而导致系统崩溃。为设置合适的线程池大小，我们借助</span>JVisualVM<span style="font-family:'宋体';">工具监控</span>Executor<span style="font-family:'宋体';">的</span>CPU<span style="font-family:'宋体';">使用率，通过调整参数找到最优并发线程数。经实验验证，该方案大大提高了系统的吞吐量。</span></p>
<table cellspacing="0" cellpadding="0" style="color:rgb(62,62,62);font-family:'Helvetica Neue', Helvetica, 'Hiragino Sans GB', 'Microsoft YaHei', Arial, sans-serif;font-size:16px;border-collapse:collapse;width:670px;"><tbody><tr><td width="426" valign="top" style="border:1px solid;">
<p style="clear:both;min-height:1em;">
  // <span style="font-family:'宋体';">
同步处理</span></p>
<p style="clear:both;min-height:1em;">
  // <span style="font-family:'宋体';">
函数</span>runBusinessLogic<span style="font-family:'宋体';">是</span> Task<span style="font-family:'宋体';">中的业务逻辑，执行时间不定</span></p>
<p style="clear:both;min-height:1em;">
  rdd.foreachPartition(partition  =&gt; runBusinessLogic (partition))</p>
<p style="clear:both;min-height:1em;">
 </p>
<p style="clear:both;min-height:1em;">
  // <span style="font-family:'宋体';">
异步处理，</span>threadPool<span style="font-family:'宋体';">是线程池</span></p>
<p style="clear:both;min-height:1em;">
   rdd.foreachPartition(partition =&gt; {</p>
<p style="clear:both;min-height:1em;">
    val  qsize = threadPool.getQueue.size // <span style="font-family:'宋体';">
线程池中积压的线程数</span></p>
<p style="clear:both;min-height:1em;">
    if  (qsize &gt; 100) {</p>
<p style="clear:both;min-height:1em;">
      runBusinessLogic(partition)  // <span style="font-family:'宋体';">
暂时同步处理</span></p>
<p style="clear:both;min-height:1em;">
    }</p>
<p style="clear:both;min-height:1em;">
     threadPool.execute(new Runnable {</p>
<p style="clear:both;min-height:1em;">
       override def run() = runBusinessLogic(partition)</p>
<p style="clear:both;min-height:1em;">
    })</p>
<p style="clear:both;min-height:1em;">
  })</p>
</td>
</tr></tbody></table><p style="color:rgb(62,62,62);font-family:'Helvetica Neue', Helvetica, 'Hiragino Sans GB', 'Microsoft YaHei', Arial, sans-serif;font-size:16px;clear:both;min-height:1em;text-indent:32px;">
<span style="font-family:'宋体';">异步化</span>Task<span style="font-family:'宋体';">也存在缺点：如果</span>Executor<span style="font-family:'宋体';">发生异常，存放在线程池中的业务逻辑无法重新计算，会导致部分数据丢失。经实验验证，仅当</span>Executor<span style="font-family:'宋体';">异常崩溃时有数据丢失，且不常见，在本项目的场景中可以接受。</span></p>
<p style="color:rgb(62,62,62);font-family:'Helvetica Neue', Helvetica, 'Hiragino Sans GB', 'Microsoft YaHei', Arial, sans-serif;font-size:16px;margin-left:.25in;clear:both;min-height:1em;">
3.<span style="font-size:9px;line-height:normal;">    </span>Kafka Receiver<span style="font-family:'宋体';">的稳定性</span></p>
<p style="color:rgb(62,62,62);font-family:'Helvetica Neue', Helvetica, 'Hiragino Sans GB', 'Microsoft YaHei', Arial, sans-serif;font-size:16px;clear:both;min-height:1em;text-indent:32px;">
<span style="font-family:'宋体';">本项目</span><span style="font-family:'宋体';">使用</span><span style="font-family:'宋体';">了</span>SparkStreaming<span style="font-family:'宋体';">中的</span>Kafka
 Receiver<span style="font-family:'宋体';">，</span><span style="font-family:'宋体';">本质上</span><span style="font-family:'宋体';">调用</span>Kafka<span style="font-family:'宋体';">官方的客户端</span>ZookeeperConsumerConnector<span style="font-family:'宋体';">。其策略是</span><span style="font-family:'宋体';">每个客户端在</span>Zookeeper<span style="font-family:'宋体';">的固定路径下把自己注册为临时节点，于是所有客户端都知道其他客户端的存在，然后自动协调和分配</span>Kafka<span style="font-family:'宋体';">的数据资源。该策略存在一个弊端，当一个客户端与</span>Zookeeper<span style="font-family:'宋体';">的连接状态发生改变（断开或者连上），所有的客户端都会通过</span>Zookeeper<span style="font-family:'宋体';">协调，</span><span style="font-family:'宋体';">重新分配</span>Kafka<span style="font-family:'宋体';">的数据资源；在此期间所有客户端都断开与</span>Kafka<span style="font-family:'宋体';">的连接，系统接收不到</span>Kafka<span style="font-family:'宋体';">的数据，直到重新分配成功。如果网络质量不佳，并且</span>Receiver<span style="font-family:'宋体';">的个数较多，这种策略会造成数据输入不稳定，很多</span>SparkStreaming<span style="font-family:'宋体';">用户遇到这样的问题。在我们的系统中，该策略并没有产生明显的负面影响。值得注意的是，</span>Kafka<span style="font-family:'宋体';">客户端与</span>Zookeeper<span style="font-family:'宋体';">有个默认的参数</span>zookeeper.session.timeout.ms=6000<span style="font-family:'宋体';">，表示客户端与</span>Zookeeper<span style="font-family:'宋体';">连接的</span>session<span style="font-family:'宋体';">有效时间为</span>6<span style="font-family:'宋体';">秒，我们的客户端多次出现因为</span>Full
 GC<span style="font-family:'宋体';">超过</span>6<span style="font-family:'宋体';">秒而与</span>Zookeeper<span style="font-family:'宋体';">断开连接，之后再次连接上，期间所有客户端都受到影响，系统表现不稳定。</span><span style="font-family:'宋体';">所以</span><span style="font-family:'宋体';">项目</span><span style="font-family:'宋体';">中</span><span style="font-family:'宋体';">设置参数</span>zookeeper.session.timeout.ms=30000<span style="font-family:'宋体';">。</span></p>
<p style="color:rgb(62,62,62);font-family:'Helvetica Neue', Helvetica, 'Hiragino Sans GB', 'Microsoft YaHei', Arial, sans-serif;font-size:16px;margin-left:.25in;clear:both;min-height:1em;">
4.<span style="font-size:9px;line-height:normal;">    </span>YARN<span style="font-family:'宋体';">资源抢占问题</span></p>
<p style="color:rgb(62,62,62);font-family:'Helvetica Neue', Helvetica, 'Hiragino Sans GB', 'Microsoft YaHei', Arial, sans-serif;font-size:16px;clear:both;min-height:1em;">
       <span style="font-family:'宋体';">
在</span>Hulu<span style="font-family:'宋体';">内部，</span>Spark Streaming<span style="font-family:'宋体';">这样的长时服务与</span>MapRedue<span style="font-family:'宋体';">，</span>Spark<span style="font-family:'宋体';">，</span><a href="http://lib.csdn.net/base/hive" rel="nofollow" class="replace_word" title="Hive知识库" style="color:rgb(223,52,52);font-weight:bold;">Hive</a><span style="font-family:'宋体';">等批处理应用共享</span>YARN<span style="font-family:'宋体';">集群资源。在共享环境中，经常因一个批处理应用占用大量网络资源或者</span>CPU<span style="font-family:'宋体';">资源，导致</span>Spark
 Streaming<span style="font-family:'宋体';">服务不稳定（尽管我们采用了</span>CGroup<span style="font-family:'宋体';">进行资源隔离，但效果不佳）。更严重的问题是，如果个别</span><a href="http://lib.csdn.net/base/docker" rel="nofollow" class="replace_word" title="Docker知识库" style="color:rgb(223,52,52);font-weight:bold;">Container</a><span style="font-family:'宋体';">崩溃</span>Driver<span style="font-family:'宋体';">需要向</span>YARN<span style="font-family:'宋体';">申请新的</span>Container<span style="font-family:'宋体';">，或者如果整个应用崩溃需要重启，</span>SparkStreaming<span style="font-family:'宋体';">不能保证很快申请到足够的资源，也就无法保证线上服务的质量。为解决该问题，</span>Hulu<span style="font-family:'宋体';">使用</span>label-based
 scheduling<span style="font-family:'宋体';">的调度策略，从</span>YARN<span style="font-family:'宋体';">集群中隔离出若干节点专门运行</span>SparkStreaming<span style="font-family:'宋体';">和其他长时服务，避免与批处理程序竞争资源。</span></p>
<p style="color:rgb(62,62,62);font-family:'Helvetica Neue', Helvetica, 'Hiragino Sans GB', 'Microsoft YaHei', Arial, sans-serif;font-size:16px;margin-left:.25in;clear:both;min-height:1em;">
5.<span style="font-size:9px;line-height:normal;">    </span><span style="font-family:'宋体';">完善</span><span style="font-family:'宋体';">监控</span><span style="font-family:'宋体';">信息</span></p>
<p style="color:rgb(62,62,62);font-family:'Helvetica Neue', Helvetica, 'Hiragino Sans GB', 'Microsoft YaHei', Arial, sans-serif;font-size:16px;clear:both;min-height:1em;text-indent:32px;">
<span style="font-family:'宋体';">监控反映系统运行的性能状态，也是一切优化的基础。</span>SparkStreaming Web<span style="font-family:'宋体';">界面提供了比较丰富的监控信息，同时本项目依据业务逻辑的特点增加了更多监控。</span>Hulu<span style="font-family:'宋体';">使用</span>Graphite<span style="font-family:'宋体';">和</span>Grafana<span style="font-family:'宋体';">作为第三方监控系统，本项目把系统中关键的性能参数（如计算时长和次数）发送给</span>Graphite<span style="font-family:'宋体';">服务器，就能够在</span>Grafana<span style="font-family:'宋体';">网页上看到直观的统计图。</span></p>
<p style="color:rgb(62,62,62);font-family:'Helvetica Neue', Helvetica, 'Hiragino Sans GB', 'Microsoft YaHei', Arial, sans-serif;font-size:16px;clear:both;min-height:1em;">
<img src="http://blog.csdn.net/xiao_jun_0820/article/details/50715623" alt="" style="border:none;visibility:visible !important;"><br><span style="text-align:center;font-size:11px;line-height:12.65px;font-family:'宋体';">图</span><span style="text-align:center;font-size:11px;line-height:12.65px;">4</span><span style="text-align:center;font-size:11px;line-height:12.65px;font-family:'宋体';">：</span><span style="text-align:center;font-size:11px;line-height:12.65px;">Graphite</span><span style="text-align:center;font-size:11px;line-height:12.65px;font-family:'宋体';">监控信息，展示了</span><span style="text-align:center;font-size:11px;line-height:12.65px;">Kafka</span><span style="text-align:center;font-size:11px;line-height:12.65px;font-family:'宋体';">中日志的剩余数量，一条线对应于一个</span><span style="text-align:center;font-size:11px;line-height:12.65px;">partition</span><span style="text-align:center;font-size:11px;line-height:12.65px;font-family:'宋体';">的历史余量</span></p>
<p style="color:rgb(62,62,62);font-family:'Helvetica Neue', Helvetica, 'Hiragino Sans GB', 'Microsoft YaHei', Arial, sans-serif;font-size:16px;clear:both;min-height:1em;text-indent:32px;">
<span style="font-family:'宋体';">图</span>4<span style="font-family:'宋体';">是统计</span>Kafka<span style="font-family:'宋体';">中日志的剩余数量，一条线对应于一个</span>partition<span style="font-family:'宋体';">的历史余量，大部分情况下余量接近零，符合预期。图中</span>09:55<span style="font-family:'宋体';">左右日志余量开始出现很明显的尖峰，之后又迅速逼近零。事后经过多种数据核对，证实</span>Kafka<span style="font-family:'宋体';">的数据一直稳定，而当时</span>Spark
 Streaming<span style="font-family:'宋体';">执行作业突然变慢，反压机制生效，于是</span>Kafka Receiver<span style="font-family:'宋体';">减小读取日志的速率，造成</span>Kafka<span style="font-family:'宋体';">数据积压；一段时间之后</span>SparkStreaming<span style="font-family:'宋体';">又恢复正常，快速消耗了</span>Kafka<span style="font-family:'宋体';">中的数据余量。</span></p>
<p style="color:rgb(62,62,62);font-family:'Helvetica Neue', Helvetica, 'Hiragino Sans GB', 'Microsoft YaHei', Arial, sans-serif;font-size:16px;clear:both;min-height:1em;text-indent:32px;">
<span style="font-family:'宋体';">直观的监控系统能有效地暴露问题，进而理解和强化系统。对于不同的业务逻辑，需要监控的信息也不相同。在我们的实践中，主要的监控指标有：</span></p>
<ol class="list-paddingleft-2" style="color:rgb(62,62,62);font-family:'Helvetica Neue', Helvetica, 'Hiragino Sans GB', 'Microsoft YaHei', Arial, sans-serif;font-size:16px;list-style-type:lower-alpha;"><li>
<p style="clear:both;min-height:1em;text-indent:32px;">
<span style="line-height:1.6;font-family:Wingdings;"><span style="font-size:9px;line-height:normal;">  </span></span><span style="font-size:9px;line-height:12.5455px;"> </span><span style="line-height:1.6;">Kafka</span><span style="line-height:1.6;font-family:'宋体';">的剩余数据量</span></p>
</li><li>
<p style="clear:both;min-height:1em;text-indent:32px;">
<span style="line-height:1.6;font-family:Wingdings;"><span style="font-size:9px;line-height:normal;">  </span></span><span style="line-height:1.6;">Spark</span><span style="line-height:1.6;font-family:'宋体';">的作业运行时间和调度时间</span></p>
</li><li>
<p style="clear:both;min-height:1em;text-indent:32px;">
<span style="line-height:1.6;font-family:Wingdings;"><span style="font-size:9px;line-height:normal;">  </span></span><span style="line-height:1.6;font-family:'宋体';">每个</span><span style="line-height:1.6;">Task</span><span style="line-height:1.6;font-family:'宋体';">的计算时间</span></p>
</li><li>
<p style="clear:both;min-height:1em;text-indent:32px;">
<span style="line-height:1.6;font-family:Wingdings;"><span style="font-size:9px;line-height:normal;">  </span></span><span style="line-height:1.6;">Codis</span><span style="line-height:1.6;font-family:'宋体';">的访问次数、时间、命中率</span></p>
</li></ol><p style="color:rgb(62,62,62);font-family:'Helvetica Neue', Helvetica, 'Hiragino Sans GB', 'Microsoft YaHei', Arial, sans-serif;font-size:16px;clear:both;min-height:1em;">
<span style="font-family:'宋体';">另外，有脚本定期分析这些统计数据，出现异常则发邮件报警。比如图</span>4<span style="font-family:'宋体';">中</span> Kafka<span style="font-family:'宋体';">的日志余量过大时，会有连续的报警邮件。我们的经验是，监控越细致，之后的优化工作越轻松。</span><span style="font-family:'宋体';">同时，优秀的监控也需要对系统深刻的理解。</span></p>
<p style="color:rgb(62,62,62);font-family:'Helvetica Neue', Helvetica, 'Hiragino Sans GB', 'Microsoft YaHei', Arial, sans-serif;font-size:16px;margin-left:.25in;clear:both;min-height:1em;">
6.<span style="font-size:9px;line-height:normal;">    </span><span style="font-family:'宋体';">参数优化</span></p>
<p style="color:rgb(62,62,62);font-family:'Helvetica Neue', Helvetica, 'Hiragino Sans GB', 'Microsoft YaHei', Arial, sans-serif;font-size:16px;margin-left:.25in;clear:both;min-height:1em;">
<span style="font-family:'宋体';">下表列出本项目中比较关键的几个参数：</span></p>
<table cellspacing="0" cellpadding="0" width="432" style="color:rgb(62,62,62);font-family:'Helvetica Neue', Helvetica, 'Hiragino Sans GB', 'Microsoft YaHei', Arial, sans-serif;font-size:16px;border-collapse:collapse;width:670px;"><tbody><tr><td width="2" valign="top" style="border-width:1px;border-style:solid solid solid none;border-color:#000000 #000000 #000000 rgb(221,221,221);">
<p style="clear:both;min-height:1em;">
spark.yarn.max.executor.failures</p>
</td>
<td width="3" valign="top" style="border-width:1px;border-style:solid none;border-color:#000000 rgb(221,221,221);">
<p style="clear:both;min-height:1em;">
Executor<span style="font-family:'宋体';">允许的失败上限</span><span style="font-family:'宋体';">；</span><span style="font-family:'宋体';">如果超过</span><span style="font-family:'宋体';">该上限</span><span style="font-family:'宋体';">，整个</span>Spark
 Streaming<span style="font-family:'宋体';">会失败</span><span style="font-family:'宋体';">，需要</span><span style="font-family:'宋体';">设置比较大</span></p>
</td>
</tr><tr><td width="2" valign="top" style="border-width:1px;border-style:none solid solid none;border-color:rgb(221,221,221) #000000 #000000 rgb(221,221,221);">
<p style="clear:both;min-height:1em;">
spark.yarn.executor.memoryOverhead</p>
</td>
<td width="3" valign="top" style="border-width:1px;border-style:none none solid;border-color:rgb(221,221,221) rgb(221,221,221) #000000;">
<p style="clear:both;min-height:1em;">
Executor<span style="font-family:'宋体';">中</span>JVM<span style="font-family:'宋体';">的开销，与堆内存不一样，设置太小会导致内存溢出异常</span></p>
</td>
</tr><tr><td width="2" valign="top" style="border-width:1px;border-style:none solid solid none;border-color:rgb(221,221,221) #000000 #000000 rgb(221,221,221);">
<p style="clear:both;min-height:1em;">
spark.receivers.num</p>
</td>
<td width="3" valign="top" style="border-width:1px;border-style:none none solid;border-color:rgb(221,221,221) rgb(221,221,221) #000000;">
<p style="clear:both;min-height:1em;">
Kafka Receiver<span style="font-family:'宋体';">的个数</span></p>
</td>
</tr><tr><td width="2" valign="top" style="border-width:1px;border-style:none solid solid none;border-color:rgb(221,221,221) #000000 #000000 rgb(221,221,221);">
<p style="clear:both;min-height:1em;">
spark.streaming.receiver.maxRate</p>
</td>
<td width="3" valign="top" style="border-width:1px;border-style:none none solid;border-color:rgb(221,221,221) rgb(221,221,221) #000000;">
<p style="clear:both;min-height:1em;">
<span style="font-family:'宋体';">每个</span>Receiver<span style="font-family:'宋体';">能够接受数据的最大速率；这个值超过峰值约</span>50%</p>
</td>
</tr><tr><td width="2" valign="top" style="border-width:1px;border-style:none solid solid none;border-color:rgb(221,221,221) #000000 #000000 rgb(221,221,221);">
<p style="clear:both;min-height:1em;">
spark.streaming.backpressure.enabled</p>
</td>
<td width="3" valign="top" style="border-width:1px;border-style:none none solid;border-color:rgb(221,221,221) rgb(221,221,221) #000000;">
<p style="clear:both;min-height:1em;">
<span style="font-family:'宋体';">反压机制；如果目前系统的延迟较长，</span>Receiver<span style="font-family:'宋体';">端会自动减小接受数据的速率，避免系统因数据积压过多而崩溃</span></p>
</td>
</tr><tr><td width="2" valign="top" style="border-width:1px;border-style:none solid solid none;border-color:rgb(221,221,221) #000000 #000000 rgb(221,221,221);">
<p style="clear:both;min-height:1em;">
spark.locality.wait</p>
</td>
<td width="3" valign="top" style="border-width:1px;border-style:none none solid;border-color:rgb(221,221,221) rgb(221,221,221) #000000;">
<p style="clear:both;min-height:1em;">
<span style="font-family:'宋体';">系统调度</span>Task<span style="font-family:'宋体';">会尽量考虑数据的局部性，如果超过</span>spark.locality.wait<span style="font-family:'宋体';">设置时间的上限，就放弃局部性；该参数直接影响</span>Task<span style="font-family:'宋体';">的调度时间</span></p>
</td>
</tr><tr><td width="2" valign="top" style="border-width:1px;border-style:none solid solid none;border-color:rgb(221,221,221) #000000 #000000 rgb(221,221,221);">
<p style="clear:both;min-height:1em;">
spark.cleaner.ttl</p>
</td>
<td width="3" valign="top" style="border-width:1px;border-style:none none solid;border-color:rgb(221,221,221) rgb(221,221,221) #000000;">
<p style="clear:both;min-height:1em;">
Spark<span style="font-family:'宋体';">系统内部的元信息的超时时间；</span>Streaming<span style="font-family:'宋体';">长期运行，元信息累积太多会影响性能</span></p>
</td>
</tr></tbody></table><p style="color:rgb(62,62,62);font-family:'Helvetica Neue', Helvetica, 'Hiragino Sans GB', 'Microsoft YaHei', Arial, sans-serif;font-size:16px;clear:both;min-height:1em;">
<span style="font-size:21px;line-height:24.15px;font-family:'宋体';">四、总结</span></p>
<p style="color:rgb(62,62,62);font-family:'Helvetica Neue', Helvetica, 'Hiragino Sans GB', 'Microsoft YaHei', Arial, sans-serif;font-size:16px;clear:both;min-height:1em;">
        Spark Streaming<span style="font-family:'宋体';">的产品上线</span><span style="font-family:'宋体';">运行</span><span style="font-family:'宋体';">一年多，期间进行了多次</span>Spark<span style="font-family:'宋体';">版本升级，</span><span style="font-family:'宋体';">从最早期的</span>0.8<span style="font-family:'宋体';">版本到</span><span style="font-family:'宋体';">最近</span><span style="font-family:'宋体';">的</span>1.5.x<span style="font-family:'宋体';">版本</span><span style="font-family:'宋体';">。</span><span style="font-family:'宋体';">总体上</span>Spark
 Streaming<span style="font-family:'宋体';">是一款优秀的实时计算框架，可以在线上使用</span><span style="font-family:'宋体';">。但仍然存在一些不足，包括：</span></p>
<p style="color:rgb(62,62,62);font-family:'Helvetica Neue', Helvetica, 'Hiragino Sans GB', 'Microsoft YaHei', Arial, sans-serif;font-size:16px;margin-left:.5in;clear:both;min-height:1em;">
1.<span style="font-size:9px;line-height:normal;">    </span>Spark<span style="font-family:'宋体';">同时使用堆内和堆外的内存，缺乏一些有效的监控信息，遇到</span>OOM<span style="font-family:'宋体';">时分析和调试比较困难；</span></p>
<p style="color:rgb(62,62,62);font-family:'Helvetica Neue', Helvetica, 'Hiragino Sans GB', 'Microsoft YaHei', Arial, sans-serif;font-size:16px;margin-left:.5in;clear:both;min-height:1em;">
2.<span style="font-size:9px;line-height:normal;">    </span><span style="font-family:'宋体';">缺少</span>Executor<span style="font-family:'宋体';">初始化接口；</span></p>
<p style="color:rgb(62,62,62);font-family:'Helvetica Neue', Helvetica, 'Hiragino Sans GB', 'Microsoft YaHei', Arial, sans-serif;font-size:16px;margin-left:.5in;clear:both;min-height:1em;">
3.<span style="font-size:9px;line-height:normal;">    </span>Spark<span style="font-family:'宋体';">采用函数式编程方式，抽象层次高，好处是使用方便，坏处是理解和优化困难；</span></p>
<p style="color:rgb(62,62,62);font-family:'Helvetica Neue', Helvetica, 'Hiragino Sans GB', 'Microsoft YaHei', Arial, sans-serif;font-size:16px;margin-left:.5in;clear:both;min-height:1em;">
4.<span style="font-size:9px;line-height:normal;">    </span><span style="font-family:'宋体';">新版本的</span>Spark<span style="font-family:'宋体';">有一些异常，如</span>Shuffle<span style="font-family:'宋体';">过程中</span>Block<span style="font-family:'宋体';">丢失、内存溢出。</span></p>
            </div>
                </div>