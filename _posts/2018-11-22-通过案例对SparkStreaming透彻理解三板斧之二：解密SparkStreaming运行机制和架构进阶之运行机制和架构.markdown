---
layout:     post
title:      通过案例对SparkStreaming透彻理解三板斧之二：解密SparkStreaming运行机制和架构进阶之运行机制和架构
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/askvinson/article/details/51340747				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
<span style="line-height:21px;font-family:'Microsoft YaHei';font-size:14px;">      Spark<a title="大数据" href="http://www.thebigdata.cn/" rel="nofollow" style="color:rgb(67,113,166);">大数据</a>分析框架的核心部件： spark Core、spark  Streaming流计算、GraphX图计算、MLlib机器学习、Spark
 SQL、Tachyon文件系统、SparkR计算引擎等主要部件.</span></p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
<span style="line-height:19.5px;font-family:'Microsoft YaHei';">　　<img alt="" src="http://images2015.cnblogs.com/blog/948437/201605/948437-20160503161530841-167128522.png" style="border:0px;"></span></p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
<span style="line-height:19.5px;font-family:Calibri;">　　<span style="line-height:21px;font-family:'Microsoft YaHei';font-size:14px;">Spark Streaming 其实是构建在spark core之上的一个应用程序，要构建一个强大的Spark应用程序 ，spark  Streaming是一个值得借鉴的参考，spark  Streaming涉及多个job交叉配合，基本涉及到了spark的所有的核心组件，精通掌握spark
 streaming是至关重要的。</span></span></p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';line-height:20.8px;">
<span style="line-height:19.5px;font-family:Calibri;"><span style="line-height:21px;font-family:'Microsoft YaHei';"></span></span></p>
<p style="font-size:13px;color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';line-height:20.8px;">
<strong><span style="line-height:21px;font-family:'Microsoft YaHei';font-size:14px;">       Spark Streaming基础概念理解：</span></strong></p>
<p style="font-size:13px;color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';line-height:20.8px;">
<span style="line-height:21px;font-family:'Microsoft YaHei';font-size:14px;">　　　　1. 离散流：(Discretized Stream ,DStream):这是spark streaming对内部的持续的实时数据流的抽象描述，也即我们处理的一个实时数据流，在spark streaming中对应一个DStream ；</span></p>
<p style="font-size:13px;color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';line-height:20.8px;">
<span style="line-height:21px;font-family:'Microsoft YaHei';font-size:14px;">　　　　2. 批数据：将实时流时间以时间为单位进行分批，将数据处理转化为时间片数据的批处理；</span></p>
<p style="font-size:13px;color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';line-height:20.8px;">
<span style="line-height:21px;font-family:'Microsoft YaHei';font-size:14px;">　　　　3. 时间片或者批处理时间间隔：逻辑级别的对数据进行定量的标准，以时间片作为拆分流数据的依据；</span></p>
<p style="font-size:13px;color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';line-height:20.8px;">
<span style="line-height:21px;font-family:'Microsoft YaHei';font-size:14px;">　　　　4. 窗口长度：一个窗口覆盖的流数据的时间长度。比如说要每隔5分钟统计过去30分钟的数据，窗口长度为6，因为30分钟是batch interval 的6倍；</span></p>
<p style="font-size:13px;color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';line-height:20.8px;">
<span style="line-height:21px;font-family:'Microsoft YaHei';font-size:14px;">　　　　5. 滑动时间间隔：比如说要每隔5分钟统计过去30分钟的数据，窗口时间间隔为5分钟；</span></p>
<p style="font-size:13px;color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';line-height:20.8px;">
<span style="line-height:21px;font-family:'Microsoft YaHei';font-size:14px;">　　　　6. input DStream :一个inputDStream是一个特殊的DStream 将spark streaming连接到一个外部数据源来读取数据。</span></p>
<p style="font-size:13px;color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';line-height:20.8px;">
<span style="line-height:21px;font-family:'Microsoft YaHei';font-size:14px;">　　　　7. Receiver :长时间（可能7*24小时）运行在Excutor之上，每个Receiver负责一个inuptDStream (比如读取一个kafka消息的输入流)。每个Receiver,加上inputDStream 会占用一个core/slot ；</span></p>
<p style="font-size:13px;color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';line-height:20.8px;">
<span style="line-height:21px;font-family:'Microsoft YaHei';font-size:14px;"><span style="color:rgb(75,75,75);font-family:'Microsoft YaHei';font-size:14px;line-height:21px;">       Spark Core处理的每一步都是基于RDD的，RDD之间有依赖关系。下图中的RDD的DAG显示的是有3个Action，会触发3个job，RDD自下向上依赖，RDD产生job就会具体的执行。从DSteam
 Graph中可以看到，DStream的逻辑与RDD基本一致，它就是在RDD的基础上加上了时间的依赖。RDD的DAG又可以叫空间维度，也就是说整个Spark Streaming多了一个时间维度，也可以成为时空维度。</span><br></span></p>
<p style="font-size:13px;color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';line-height:20.8px;">
<span style="line-height:21px;font-family:'Microsoft YaHei';font-size:14px;"><span style="color:rgb(75,75,75);font-family:'Microsoft YaHei';font-size:14px;line-height:21px;">   <img alt="" src="http://images2015.cnblogs.com/blog/948437/201605/948437-20160503153415294-1829221977.png"></span></span></p>
<p style="font-size:13px;color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';line-height:20.8px;">
<span style="line-height:21px;font-family:'Microsoft YaHei';font-size:14px;"><span style="color:rgb(75,75,75);font-family:'Microsoft YaHei';font-size:14px;line-height:21px;"><span style="color:rgb(75,75,75);font-family:'Microsoft YaHei';font-size:14px;line-height:21px;"> 
      从这个角度来讲，可以将Spark Streaming放在坐标系中。其中Y轴就是对RDD的操作，RDD的依赖关系构成了整个job的逻辑，而X轴就是时间。随着时间的流逝，固定的时间间隔（Batch Interval）就会生成一个job实例，进而在集群中运行。</span><br></span></span></p>
<p style="font-size:13px;color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';line-height:20.8px;">
<span style="line-height:21px;font-family:'Microsoft YaHei';font-size:14px;"><span style="color:rgb(75,75,75);font-family:'Microsoft YaHei';font-size:14px;line-height:21px;"><span style="color:rgb(75,75,75);font-family:'Microsoft YaHei';font-size:14px;line-height:21px;"><img alt="" src="http://images2015.cnblogs.com/blog/948437/201605/948437-20160503153544622-353722546.png"><br></span></span></span></p>
<p style="font-size:13px;color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';line-height:20.8px;">
<span style="line-height:21px;font-family:'Microsoft YaHei';font-size:14px;"><span style="color:rgb(75,75,75);font-family:'Microsoft YaHei';font-size:14px;line-height:21px;"><span style="color:rgb(75,75,75);font-family:'Microsoft YaHei';font-size:14px;line-height:21px;"></span></span></span></p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
<span style="line-height:21px;font-family:'Microsoft YaHei';font-size:14px;">       对于Spark Streaming来说，当不同的数据来源的数据流进来的时候，基于固定的时间间隔，会形成一系列固定不变的数据集或event集合（例如来自flume和kafka）。而这正好与RDD基于固定的数据集不谋而合，事实上，由DStream基于固定的时间间隔行程的RDD Graph正是基于某一个batch的数据集的。</span></p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
<span style="line-height:21px;font-family:'Microsoft YaHei';font-size:14px;">　　从上图中可以看出，在每一个Batch上，空间维度的RDD依赖关系都是一样的，不同的是这个五个Batch流入的数据规模和内容不一样，所以说生成的是不同的RDD依赖关系的实例，所以说RDD的Graph脱胎于DStream的Graph，也就是说DStream就是RDD的模板，不同的时间间隔，生成不同的RDD Graph实例。</span></p>
<span style="line-height:21px;color:rgb(75,75,75);font-size:14px;font-family:'Microsoft YaHei';">从源码解读</span><span style="line-height:21px;color:rgb(75,75,75);font-size:14px;font-family:Calibri;"><span style="font-family:'Microsoft YaHei';">DStream :</span></span><br><p style="font-size:13px;color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';line-height:20.8px;">
<span style="line-height:21px;font-family:'Microsoft YaHei';font-size:14px;"><span style="color:rgb(75,75,75);font-family:'Microsoft YaHei';font-size:14px;line-height:21px;"><span style="color:rgb(75,75,75);font-family:'Microsoft YaHei';font-size:14px;line-height:21px;"><span style="line-height:21px;color:rgb(75,75,75);font-size:14px;font-family:Calibri;"><span style="font-family:'Microsoft YaHei';"><img alt="" src="http://images2015.cnblogs.com/blog/948437/201605/948437-20160503153903794-975518658.png"><br></span></span></span></span></span></p>
<p style="font-size:13px;color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';line-height:20.8px;">
<span style="line-height:21px;font-family:'Microsoft YaHei';font-size:14px;"><span style="color:rgb(75,75,75);font-family:'Microsoft YaHei';font-size:14px;line-height:21px;"><span style="color:rgb(75,75,75);font-family:'Microsoft YaHei';font-size:14px;line-height:21px;"><span style="line-height:21px;color:rgb(75,75,75);font-size:14px;font-family:Calibri;"><span style="font-family:'Microsoft YaHei';"><span style="color:rgb(75,75,75);font-family:'Microsoft YaHei';font-size:14px;line-height:21px;"> 
      从这里可以看出，DStream就是Spark Streaming的核心，就想Spark Core的核心是RDD，它也有dependency和compute。更为关键的是下面的代码：</span><br></span></span></span></span></span></p>
<p style="font-size:13px;color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';line-height:20.8px;">
<span style="line-height:21px;font-family:'Microsoft YaHei';font-size:14px;"><span style="color:rgb(75,75,75);font-family:'Microsoft YaHei';font-size:14px;line-height:21px;"><span style="color:rgb(75,75,75);font-family:'Microsoft YaHei';font-size:14px;line-height:21px;"><span style="line-height:21px;color:rgb(75,75,75);font-size:14px;font-family:Calibri;"><span style="font-family:'Microsoft YaHei';"><span style="color:rgb(75,75,75);font-family:'Microsoft YaHei';font-size:14px;line-height:21px;"><img alt="" src="http://images2015.cnblogs.com/blog/948437/201605/948437-20160503153952701-11495926.png"><br></span></span></span></span></span></span></p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';line-height:20.8px;">
<span style="line-height:21px;font-family:'Microsoft YaHei';"><span style="color:rgb(75,75,75);font-family:'Microsoft YaHei';line-height:21px;"><span style="color:rgb(75,75,75);font-family:'Microsoft YaHei';line-height:21px;"><span style="line-height:21px;color:rgb(75,75,75);font-family:Calibri;"><span style="font-family:'Microsoft YaHei';"><span style="color:rgb(75,75,75);font-family:'Microsoft YaHei';line-height:21px;"></span></span></span></span></span></span></p>
<p style="font-size:13px;color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';line-height:20.8px;">
<span style="line-height:21px;font-family:'Microsoft YaHei';font-size:14px;">这是一个HashMap，以时间为key，以RDD为Value，这也正应证了随着时间流逝，不断的生成RDD，产生依赖关系的job，并通过JbScheduler在集群上运行。再次验证了DStream就是RDD的模版。</span></p>
<p style="font-size:13px;color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';line-height:20.8px;">
<span style="line-height:21px;font-family:'Microsoft YaHei';font-size:14px;">　　DStream可以说是逻辑级别的，RDD就是物理级别的，DStream所表达的最终都是通过RDD的转化实现的。前者是更高级别的抽象，后者是底层的实现。DStream实际上就是在时间维度上对RDD集合的封装，DStream与RDD的关系就是随着时间流逝不断的产生RDD，对DStream的操作就是在固定时间上操作RDD。</span></p>
<p style="font-size:13px;color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';line-height:20.8px;">
 </p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';line-height:20.8px;">
<span style="font-size:13px;">　　</span><span style="font-size:12px;">总结：</span></p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';line-height:20.8px;">
<span style="font-size:12px;">　     在空间维度上的业务逻辑作用于<span style="line-height:19.5px;font-family:Calibri;">DStream</span><span style="line-height:19.5px;font-family:'宋体';">，随着时间的流逝，每个</span><span style="line-height:19.5px;font-family:Calibri;">Batch Interval</span><span style="line-height:19.5px;font-family:'宋体';">形成了具体的数据集，产生了</span><span style="line-height:19.5px;font-family:Calibri;">RDD</span><span style="line-height:19.5px;font-family:'宋体';">，对</span><span style="line-height:19.5px;font-family:Calibri;">RDD</span><span style="line-height:19.5px;font-family:'宋体';">进行T</span><span style="line-height:19.5px;font-family:Calibri;">ransform</span><span style="line-height:19.5px;font-family:'宋体';">操作，进而形成了</span><span style="line-height:19.5px;font-family:Calibri;">RDD</span><span style="line-height:19.5px;font-family:'宋体';">的依赖关系</span><span style="line-height:19.5px;font-family:Calibri;">RDD
 DAG</span><span style="line-height:19.5px;font-family:'宋体';">，形成J</span><span style="line-height:19.5px;font-family:Calibri;">ob</span><span style="line-height:19.5px;font-family:'宋体';">。然后J</span><span style="line-height:19.5px;font-family:Calibri;">obScheduler</span><span style="line-height:19.5px;font-family:'宋体';">根据时间调度，基于</span><span style="line-height:19.5px;font-family:Calibri;">RDD</span><span style="line-height:19.5px;font-family:'宋体';">的依赖关系，把作业发布到</span><span style="line-height:19.5px;font-family:Calibri;">Spark
 Cluster</span><span style="line-height:19.5px;font-family:'宋体';">上去运行，不断的产生</span><span style="line-height:19.5px;font-family:Calibri;">Spark</span><span style="line-height:19.5px;font-family:'宋体';">作业</span></span></p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';line-height:20.8px;">
<span style="font-size:12px;"><span style="line-height:19.5px;font-family:'宋体';"></span></span><br></p>
            </div>
                </div>