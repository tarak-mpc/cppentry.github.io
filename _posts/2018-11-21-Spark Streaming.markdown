---
layout:     post
title:      Spark Streaming
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h1>1. <strong>课程目标</strong></h1>
<h2>1.1. <strong><span style="font-family:'宋体';">掌握</span>Spark Streaming<span style="font-family:'宋体';">的原理</span></strong></h2>
<h2>1.2. <strong><span style="font-family:'宋体';">熟练使用</span>Spark Streaming<span style="font-family:'宋体';">完成流式计算任务</span></strong></h2>
<h1>2. <strong>Spark Streaming<span style="font-family:'宋体';">介绍</span></strong></h1>
<h2>2.1. <strong>Spark Streaming<span style="font-family:'宋体';">概述</span></strong></h2>
<h3>2.1.1. <strong><span style="font-family:'宋体';">什么是</span>Spark Streaming</strong></h3>
<p> <img src="https://img-blog.csdn.net/20170926085546878" alt=""></p>
<p>Spark Streaming<span style="font-family:'宋体';">类似于</span><span style="font-family:Calibri;">Apache Storm</span><span style="font-family:'宋体';">，用于流式数据的处理。根据其官方文档介绍，</span><span style="font-family:Calibri;">Spark Streaming</span><span style="font-family:'宋体';">有高吞吐量和容错能力强等特点。</span><span style="font-family:Calibri;">Spark
 Streaming</span><span style="font-family:'宋体';">支持的数据输入源很多，例如：</span><span style="font-family:Calibri;">Kafka</span><span style="font-family:'宋体';">、</span><span style="font-family:Calibri;">Flume</span><span style="font-family:'宋体';">、</span><span style="font-family:Calibri;">Twitter</span><span style="font-family:'宋体';">、</span><span style="font-family:Calibri;">ZeroMQ</span><span style="font-family:'宋体';">和简单的</span><span style="font-family:Calibri;">TCP</span><span style="font-family:'宋体';">套接字等等。数据输入后可以用</span><span style="font-family:Calibri;">Spark</span><span style="font-family:'宋体';">的高度抽象原语如：</span><span style="font-family:Calibri;">map</span><span style="font-family:'宋体';">、</span><span style="font-family:Calibri;">reduce</span><span style="font-family:'宋体';">、</span><span style="font-family:Calibri;">join</span><span style="font-family:'宋体';">、</span><span style="font-family:Calibri;">window</span><span style="font-family:'宋体';">等进行运算。而结果也能保存在很多地方，如</span><span style="font-family:Calibri;">HDFS</span><span style="font-family:'宋体';">，数据库等。另外</span><span style="font-family:Calibri;">Spark
 Streaming</span><span style="font-family:'宋体';">也能和</span><span style="font-family:Calibri;">MLlib</span><span style="font-family:'宋体';">（机器学习）以及</span><span style="font-family:Calibri;">Graphx</span><span style="font-family:'宋体';">完美融合。</span></p>
<p> </p>
<h3>2.1.2. <strong><span style="font-family:'宋体';">为什么要学习</span>Spark Streaming</strong></h3>
<p>1.易用</p>
<p>2.容错</p>
<p>3.<span style="font-family:'宋体';">易整合到</span>Spark<span style="font-family:'宋体';">体系</span></p>
<p> </p>
<h3>2.1.3. <strong>Spark<span style="font-family:'宋体';">与</span><span style="font-family:Calibri;">Storm</span><span style="font-family:'宋体';">的对比</span></strong></h3>
<table><tbody><tr><td valign="top">
<p align="center"><strong>Spark</strong></p>
</td>
<td valign="top">
<p align="center"><strong>Storm</strong></p>
</td>
</tr><tr><td valign="top">
<p> <img src="https://img-blog.csdn.net/20170926085810067" alt=""></p>
</td>
<td valign="top">
<p> <img src="https://img-blog.csdn.net/20170926085840599" alt=""></p>
</td>
</tr><tr><td valign="top">
<p align="center">开发语言：<span style="font-family:Calibri;">Scala</span></p>
</td>
<td valign="top">
<p align="center">开发语言：<span style="font-family:Calibri;">Clojure</span></p>
</td>
</tr><tr><td valign="top">
<p align="center">编程模型：<span style="font-family:Calibri;">DStream</span></p>
</td>
<td valign="top">
<p align="center">编程模型：<span style="font-family:Calibri;">Spout/Bolt</span></p>
</td>
</tr><tr><td valign="top">
<p align="center"><img src="https://img-blog.csdn.net/20170926085910306" alt=""> </p>
</td>
<td valign="top">
<p align="center"> <img src="https://img-blog.csdn.net/20170926085944731" alt=""></p>
</td>
</tr></tbody></table><p> </p>
<h1>3. <strong>DStream</strong></h1>
<h2>3.1. <strong><span style="font-family:'宋体';">什么是</span>DStream</strong></h2>
<p>Discretized Stream<span style="font-family:'宋体';">是</span><span style="font-family:Calibri;">Spark Streaming</span><span style="font-family:'宋体';">的基础抽象，代表持续性的数据流和经过各种</span><span style="font-family:Calibri;">Spark</span><span style="font-family:'宋体';">原语操作后的结果数据流。在内部实现上，</span><span style="font-family:Calibri;">DStream</span><span style="font-family:'宋体';">是一系列连续的</span><span style="font-family:Calibri;">RDD</span><span style="font-family:'宋体';">来表示。每个</span><span style="font-family:Calibri;">RDD</span><span style="font-family:'宋体';">含有一段时间间隔内的数据，如下图：</span></p>
<p> <img src="https://img-blog.csdn.net/20170926090020986" alt=""></p>
<p><span style="font-family:'宋体';">对数据的操作也是按照</span>RDD<span style="font-family:'宋体';">为单位来进行的</span></p>
<p> </p>
<p><span style="font-family:'宋体';">计算过程由</span>Spark engine<span style="font-family:'宋体';">来完成</span></p>
<p> </p>
<h2>3.2. <strong>DStream<span style="font-family:'宋体';">相关操作</span></strong></h2>
<p>DStream<span style="font-family:'宋体';">上的原语与</span><span style="font-family:Calibri;">RDD</span><span style="font-family:'宋体';">的类似，分为</span><span style="font-family:Calibri;">Transformations</span><span style="font-family:'宋体';">（转换）和</span><span style="font-family:Calibri;">Output
 Operations</span><span style="font-family:'宋体';">（输出）两种，此外转换操作中还有一些比较特殊的原语，如：</span><span style="font-family:Calibri;">updateStateByKey()</span><span style="font-family:'宋体';">、</span><span style="font-family:Calibri;">transform()</span><span style="font-family:'宋体';">以及各种</span><span style="font-family:Calibri;">Window</span><span style="font-family:'宋体';">相关的原语。</span></p>
<p> </p>
<h3>3.2.1. <strong>Transformations on DStreams</strong></h3>
<br><br><table><tbody><tr><td valign="top">
<p align="center"><strong></strong></p>
</td>
</tr><tr><th style="border:1px solid rgb(204,204,204);font-weight:normal;background:rgb(238,238,238);">
Transformation算子</th>
<th style="border:1px solid rgb(204,204,204);font-weight:normal;background:rgb(238,238,238);">
用途</th>
</tr><tr><td style="border:1px solid rgb(204,204,204);"><strong>map</strong>(<em>func</em>)</td>
<td style="border:1px solid rgb(204,204,204);">返回会一个新的DStream，并将源DStream中每个元素通过func映射为新的元素</td>
</tr><tr><td style="border:1px solid rgb(204,204,204);"><strong>flatMap</strong>(<em>func</em>)</td>
<td style="border:1px solid rgb(204,204,204);">和map类似，不过每个输入元素不再是映射为一个输出，而是映射为0到多个输出</td>
</tr><tr><td style="border:1px solid rgb(204,204,204);"><strong>filter</strong>(<em>func</em>)</td>
<td style="border:1px solid rgb(204,204,204);">返回一个新的DStream，并包含源DStream中被func选中（func返回true）的元素</td>
</tr><tr><td style="border:1px solid rgb(204,204,204);"><strong>repartition</strong>(<em>numPartitions</em>)</td>
<td style="border:1px solid rgb(204,204,204);">更改DStream的并行度（增加或减少分区数）</td>
</tr><tr><td style="border:1px solid rgb(204,204,204);"><strong>union</strong>(<em>otherStream</em>)</td>
<td style="border:1px solid rgb(204,204,204);">返回新的DStream，包含源DStream和otherDStream元素的并集</td>
</tr><tr><td style="border:1px solid rgb(204,204,204);"><strong>count</strong>()</td>
<td style="border:1px solid rgb(204,204,204);">返回一个包含单元素RDDs的DStream，其中每个元素是源DStream中各个RDD中的元素个数</td>
</tr><tr><td style="border:1px solid rgb(204,204,204);"><strong>reduce</strong>(<em>func</em>)</td>
<td style="border:1px solid rgb(204,204,204);">返回一个包含单元素RDDs的DStream，其中每个元素是通过源RDD中各个RDD的元素经func（func输入两个参数并返回一个同类型结果数据）聚合得到的结果。func必须满足结合律，以便支持并行计算。</td>
</tr><tr><td style="border:1px solid rgb(204,204,204);"><strong>countByValue</strong>()</td>
<td style="border:1px solid rgb(204,204,204);">如果源DStream包含的元素类型为K，那么该算子返回新的DStream包含元素为(K, Long)键值对，其中K为源DStream各个元素，而Long为该元素出现的次数。</td>
</tr><tr><td style="border:1px solid rgb(204,204,204);"><strong>reduceByKey</strong>(<em>func</em>, [<em>numTasks</em>])</td>
<td style="border:1px solid rgb(204,204,204);">如果源DStream 包含的元素为 (K, V) 键值对，则该算子返回一个新的也包含(K, V)键值对的DStream，其中V是由func聚合得到的。注意：默认情况下，该算子使用Spark的默认并发任务数（本地模式为2，集群模式下由spark.default.parallelism 决定）。你可以通过可选参数numTasks来指定并发任务个数。</td>
</tr><tr><td style="border:1px solid rgb(204,204,204);"><strong>join</strong>(<em>otherStream</em>, [<em>numTasks</em>])</td>
<td style="border:1px solid rgb(204,204,204);">如果源DStream包含元素为(K, V)，同时otherDStream包含元素为(K, W)键值对，则该算子返回一个新的DStream，其中源DStream和otherDStream中每个K都对应一个 (K, (V, W))键值对元素。</td>
</tr><tr><td style="border:1px solid rgb(204,204,204);"><strong>cogroup</strong>(<em>otherStream</em>, [<em>numTasks</em>])</td>
<td style="border:1px solid rgb(204,204,204);">如果源DStream包含元素为(K, V)，同时otherDStream包含元素为(K, W)键值对，则该算子返回一个新的DStream，其中每个元素类型为包含(K, Seq[V], Seq[W])的tuple。</td>
</tr><tr><td style="border:1px solid rgb(204,204,204);"><strong>transform</strong>(<em>func</em>)</td>
<td style="border:1px solid rgb(204,204,204);">返回一个新的DStream，其包含的RDD为源RDD经过func操作后得到的结果。利用该算子可以对DStream施加任意的操作。</td>
</tr><tr><td style="border:1px solid rgb(204,204,204);"><strong>updateStateByKey</strong>(<em>func</em>)</td>
<td style="border:1px solid rgb(204,204,204);">返回一个包含新”状态”的DStream。源DStream中每个key及其对应的values会作为func的输入，而func可以用于对每个key的“状态”数据作任意的更新操作。</td>
</tr><tr><td style="border:1px solid rgb(204,204,204);color:rgb(102,102,102);font-size:14px;">
 </td>
</tr><tr><td valign="top">
<p></p>
</td>
</tr></tbody></table><p> </p>
<p><strong><span style="font-family:'宋体';">特殊的</span>Transformations</strong></p>
<p><strong> </strong></p>
<p>1.<strong>UpdateStateByKey Operation</strong></p>
<p>UpdateStateByKey<span style="font-family:'宋体';">原语用于记录历史记录，上文中</span><span style="font-family:Calibri;">Word Count</span><span style="font-family:'宋体';">示例中就用到了该特性。若不用</span><span style="font-family:Calibri;">UpdateStateByKey</span><span style="font-family:'宋体';">来更新状态，那么每次数据进来后分析完成后，结果输出后将不在保存</span></p>
<p> </p>
<p>2.<strong>Transform Operation</strong></p>
<p>Transform<span style="font-family:'宋体';">原语允许</span><span style="font-family:Calibri;">DStream</span><span style="font-family:'宋体';">上执行任意的</span><span style="font-family:Calibri;">RDD-to-RDD</span><span style="font-family:'宋体';">函数。通过该函数可以方便的扩展</span><span style="font-family:Calibri;">Spark
 API</span><span style="font-family:'宋体';">。此外，</span><span style="font-family:Calibri;">MLlib</span><span style="font-family:'宋体';">（机器学习）以及</span><span style="font-family:Calibri;">Graphx</span><span style="font-family:'宋体';">也是通过本函数来进行结合的。</span></p>
<p> </p>
<p>3.<strong>Window Operations</strong></p>
<p>Window Operations<span style="font-family:'宋体';">有点类似于</span><span style="font-family:Calibri;">Storm</span><span style="font-family:'宋体';">中的</span><span style="font-family:Calibri;">State</span><span style="font-family:'宋体';">，可以设置窗口的大小和滑动窗口的间隔来动态的获取当前</span><span style="font-family:Calibri;">Steaming</span><span style="font-family:'宋体';">的允许状态</span></p>
<p><span style="font-family:'宋体';">这里列出一些基于窗口计算的算子</span></p>
<p><br></p><table border="1" cellspacing="0" cellpadding="2" style="border-collapse:collapse;border:0px;color:rgb(102,102,102);font-size:14px;"><tbody><tr><th style="border:1px solid rgb(204,204,204);font-weight:normal;background:rgb(238,238,238);">
Transformation窗口算子</th>
<th style="border:1px solid rgb(204,204,204);font-weight:normal;background:rgb(238,238,238);">
用途</th>
</tr><tr><td style="border:1px solid rgb(204,204,204);"><strong>window</strong>(<em>windowLength</em>, <em>slideInterval</em>)</td>
<td style="border:1px solid rgb(204,204,204);">将源DStream窗口化，并返回转化后的DStream</td>
</tr><tr><td style="border:1px solid rgb(204,204,204);"><strong>countByWindow</strong>(<em>windowLength</em>,<em>slideInterval</em>)</td>
<td style="border:1px solid rgb(204,204,204);">返回数据流在一个滑动窗口内的元素个数</td>
</tr><tr><td style="border:1px solid rgb(204,204,204);"><strong>reduceByWindow</strong>(<em>func</em>, <em>windowLength</em>,<em>slideInterval</em>)</td>
<td style="border:1px solid rgb(204,204,204);">基于数据流在一个滑动窗口内的元素，用func做聚合，返回一个单元素数据流。func必须满足结合律，以便支持并行计算。</td>
</tr><tr><td style="border:1px solid rgb(204,204,204);"><strong>reduceByKeyAndWindow</strong>(<em>func</em>,<em>windowLength</em>, <em>slideInterval</em>, [<em>numTasks</em>])</td>
<td style="border:1px solid rgb(204,204,204);">基于(K, V)键值对DStream，将一个滑动窗口内的数据进行聚合，返回一个新的包含(K,V)键值对的DStream，其中每个value都是各个key经过func聚合后的结果。<br>
注意：如果不指定numTasks，其值将使用Spark的默认并行任务数（本地模式下为2，集群模式下由 spark.default.parallelism决定）。当然，你也可以通过numTasks来指定任务个数。</td>
</tr><tr><td style="border:1px solid rgb(204,204,204);"><strong>reduceByKeyAndWindow</strong>(<em>func</em>, <em>invFunc</em>,<em>windowLength</em>,<em>slideInterval</em>, [<em>numTasks</em>])</td>
<td style="border:1px solid rgb(204,204,204);">和前面的reduceByKeyAndWindow() 类似，只是这个版本会用之前滑动窗口计算结果，递增地计算每个窗口的归约结果。当新的数据进入窗口时，这些values会被输入func做归约计算，而这些数据离开窗口时，对应的这些values又会被输入 invFunc 做”反归约”计算。举个简单的例子，就是把新进入窗口数据中各个单词个数“增加”到各个单词统计结果上，同时把离开窗口数据中各个单词的统计个数从相应的统计结果中“减掉”。不过，你的自己定义好”反归约”函数，即：该算子不仅有归约函数（见参数func），还得有一个对应的”反归约”函数（见参数中的
 invFunc）。和前面的reduceByKeyAndWindow() 类似，该算子也有一个可选参数numTasks来指定并行任务数。注意，这个算子需要配置好检查点（<a href="http://spark.apache.org/docs/latest/streaming-programming-guide.html#checkpointing" rel="nofollow" style="color:rgb(0,161,158);">checkpointing</a>）才能用。</td>
</tr><tr><td style="border:1px solid rgb(204,204,204);"><strong>countByValueAndWindow</strong>(<em>windowLength</em>,<em>slideInterval</em>, [<em>numTasks</em>])</td>
<td style="border:1px solid rgb(204,204,204);">基于包含(K, V)键值对的DStream，返回新的包含(K, Long)键值对的DStream。其中的Long value都是滑动窗口内key出现次数的计数。<br>
和前面的reduceByKeyAndWindow() 类似，该算子也有一个可选参数numTasks来指定并行任务数。</td>
</tr></tbody></table><br><p> </p>
<h3>3.2.2. <strong>Output Operations on DStreams</strong></h3>
<p>Output Operations<span style="font-family:'宋体';">可以将</span><span style="font-family:Calibri;">DStream</span><span style="font-family:'宋体';">的数据输出到外部的数据库或文件系统，当某个</span><span style="font-family:Calibri;">Output Operations</span><span style="font-family:'宋体';">原语被调用时（与</span><span style="font-family:Calibri;">RDD</span><span style="font-family:'宋体';">的</span><span style="font-family:Calibri;">Action</span><span style="font-family:'宋体';">相同），</span><span style="font-family:Calibri;">streaming</span><span style="font-family:'宋体';">程序才会开始真正的计</span><br></p><table border="1" cellspacing="0" cellpadding="2" style="border-collapse:collapse;border:0px;color:rgb(102,102,102);font-size:14px;"><tbody><tr><th style="border:1px solid rgb(204,204,204);font-weight:normal;background:rgb(238,238,238);">
输出算子</th>
<th style="border:1px solid rgb(204,204,204);font-weight:normal;background:rgb(238,238,238);">
用途</th>
</tr><tr><td style="border:1px solid rgb(204,204,204);"><strong>print</strong>()</td>
<td style="border:1px solid rgb(204,204,204);">在驱动器（driver）节点上打印DStream每个批次中的头十个元素。<br>
Python API 对应的Python API为 <strong>pprint()</strong></td>
</tr><tr><td style="border:1px solid rgb(204,204,204);"><strong>saveAsTextFiles</strong>(<em>prefix</em>, [<em>suffix</em>])</td>
<td style="border:1px solid rgb(204,204,204);">将DStream的内容保存到文本文件。<br>
每个批次一个文件，各文件命名规则为 “prefix-TIME_IN_MS[.suffix]”</td>
</tr><tr><td style="border:1px solid rgb(204,204,204);"><strong>saveAsObjectFiles</strong>(<em>prefix</em>, [<em>suffix</em>])</td>
<td style="border:1px solid rgb(204,204,204);">将DStream内容以序列化Java对象的形式保存到顺序文件中。<br>
每个批次一个文件，各文件命名规则为 “prefix-TIME_IN_MS[.suffix]”Python API 暂不支持Python</td>
</tr><tr><td style="border:1px solid rgb(204,204,204);"><strong>saveAsHadoopFiles</strong>(<em>prefix</em>, [<em>suffix</em>])</td>
<td style="border:1px solid rgb(204,204,204);">将DStream内容保存到Hadoop文件中。<br>
每个批次一个文件，各文件命名规则为 “prefix-TIME_IN_MS[.suffix]”Python API 暂不支持Python</td>
</tr><tr><td style="border:1px solid rgb(204,204,204);"><strong>foreachRDD</strong>(<em>func</em>)</td>
<td style="border:1px solid rgb(204,204,204);">这是最通用的输出算子了，该算子接收一个函数func，func将作用于DStream的每个RDD上。<br>
func应该实现将每个RDD的数据推到外部系统中，比如：保存到文件或者写到数据库中。<br>
注意，func函数是在streaming应用的驱动器进程中执行的，所以如果其中包含RDD的action算子，就会触发对DStream中RDDs的实际计算过程。</td>
</tr></tbody></table>
算过程。
<p><span style="font-family:'宋体';"><br></span></p>
<table><tbody><tr><td valign="top"> </td>
</tr><tr><td valign="top">
<p></p>
</td>
</tr></tbody></table><h1>4. <strong>实战</strong></h1>
<h2>4.1. <strong><span style="font-family:'宋体';">用</span>Spark Streaming<span style="font-family:'宋体';">实现实时</span><span style="font-family:Cambria;">WordCount</span></strong></h2>
<p>架构图：</p>
<p> <img src="https://img-blog.csdn.net/20170926090211288" alt=""></p>
<p><span style="font-size:14px;">1.安装并启动生成者</span></p>
<p><span style="font-family:'宋体';">首先在一台</span>Linux<span style="font-family:'宋体';">（</span><span style="font-family:Calibri;">ip</span><span style="font-family:'宋体';">：</span><span style="font-family:Calibri;">192.168.10.101（）</span><span style="font-family:'宋体';">）上用</span><span style="font-family:Calibri;">YUM</span><span style="font-family:'宋体';">安装</span><span style="font-family:Calibri;">nc</span><span style="font-family:'宋体';">工具</span></p>
<p><span style="color:rgb(51,51,51);">yum install -y nc</span></p>
<p><span style="color:rgb(51,51,51);"> </span></p>
<p><span style="font-family:'宋体';">启动一个服务端并监听</span>9999<span style="font-family:'宋体';">端口</span></p>
<p><span style="color:rgb(51,51,51);">nc -lk 9999</span></p>
<p><span style="color:rgb(51,51,51);"> </span></p>
<p><span style="font-size:14px;">2.<span style="font-family:'宋体';">编写</span>Spark Streaming<span style="font-family:'宋体';">程序</span></span></p>
<table><tbody><tr><td valign="top">
<p><strong><span style="color:rgb(0,0,128);">package org</span></strong>.spark.streaming<br><strong><span style="color:rgb(0,0,128);">import </span></strong>cn.itcast.spark.util.LoggerLevel<br><strong><span style="color:rgb(0,0,128);">import </span></strong>org.apache.spark.<span style="background:rgb(228,228,255);">SparkConf</span><br><strong><span style="color:rgb(0,0,128);">import </span></strong>org.apache.spark.streaming.{Seconds, StreamingContext}<br><strong><span style="color:rgb(0,0,128);">object </span></strong>NetworkWordCount {<br>
  <strong><span style="color:rgb(0,0,128);">def </span></strong>main(args: Array[<span style="color:rgb(32,153,157);">String</span>]) {<br>
    <em><span style="color:rgb(128,128,128);">//设置日志级别</span><span style="color:rgb(128,128,128);"><br></span><span style="color:rgb(128,128,128);">    </span></em>LoggerLevel.<em>setStreamingLogLevels</em>()<br>
    <em><span style="color:rgb(128,128,128);">//创建SparkConf并设置为本地模式运行</span><span style="color:rgb(128,128,128);"><br></span><span style="color:rgb(128,128,128);">    //注意local[2]代表开两个线程</span><span style="color:rgb(128,128,128);"><br></span><span style="color:rgb(128,128,128);">    </span></em><strong><span style="color:rgb(0,0,128);">val</span></strong>conf =<strong><span style="color:rgb(0,0,128);">new
</span></strong><span style="background:rgb(228,228,255);">SparkConf</span>().setMaster(<strong><span style="color:rgb(0,128,0);">"local[2]"</span></strong>).setAppName(<strong><span style="color:rgb(0,128,0);">"NetworkWordCount"</span></strong>)<br>
    <em><span style="color:rgb(128,128,128);">//设置DStream批次时间间隔为2秒</span><span style="color:rgb(128,128,128);"><br></span><span style="color:rgb(128,128,128);">    </span></em><strong><span style="color:rgb(0,0,128);">val</span></strong>ssc =<strong><span style="color:rgb(0,0,128);">new
</span></strong>StreamingContext(conf,<em>Seconds</em>(<span style="color:rgb(0,0,255);">2</span>))<br>
    <em><span style="color:rgb(128,128,128);">//通过网络读取数据</span><span style="color:rgb(128,128,128);"><br></span><span style="color:rgb(128,128,128);">    </span></em><strong><span style="color:rgb(0,0,128);">val</span></strong>lines = ssc.socketTextStream(<strong><span style="color:rgb(0,128,0);">"192.168.10.101"</span></strong>,<span style="color:rgb(0,0,255);">9999</span>)<br>
    <em><span style="color:rgb(128,128,128);">//将读到的数据用空格切成单词</span><span style="color:rgb(128,128,128);"><br></span><span style="color:rgb(128,128,128);">    </span></em><strong><span style="color:rgb(0,0,128);">val</span></strong>words = lines.flatMap(_.split(<strong><span style="color:rgb(0,128,0);">" "</span></strong>))<br>
    <em><span style="color:rgb(128,128,128);">//将单词和1组成一个pair</span><span style="color:rgb(128,128,128);"><br></span><span style="color:rgb(128,128,128);">    </span></em><strong><span style="color:rgb(0,0,128);">val</span></strong>pairs = words.map(word =&gt; (word,<span style="color:rgb(0,0,255);">1</span>))<br>
    <em><span style="color:rgb(128,128,128);">//按单词进行分组求相同单词出现的次数</span><span style="color:rgb(128,128,128);"><br></span><span style="color:rgb(128,128,128);">    </span></em><strong><span style="color:rgb(0,0,128);">val</span></strong>wordCounts = pairs.reduceByKey(_ + _)<br>
    <em><span style="color:rgb(128,128,128);">//打印结果到控制台</span><span style="color:rgb(128,128,128);"><br></span><span style="color:rgb(128,128,128);">    </span></em>wordCounts.print()<br>
    <em><span style="color:rgb(128,128,128);">//开始计算</span><span style="color:rgb(128,128,128);"><br></span><span style="color:rgb(128,128,128);">    </span></em>ssc.start()<br>
    <em><span style="color:rgb(128,128,128);">//等待停止</span><span style="color:rgb(128,128,128);"><br></span><span style="color:rgb(128,128,128);">    </span></em>ssc.awaitTermination()<br>
  }<br>
}</p>
</td>
</tr></tbody></table><p> </p>
<p><span style="font-size:14px;">3.<span style="font-family:'宋体';">启动</span><span style="font-family:Calibri;">Spark Streaming</span><span style="font-family:'宋体';">程序：由于使用的是本地模式</span><strong><span style="color:rgb(0,128,0);">"local[2]"</span></strong><span style="font-family:'宋体';">所以可以直接在本地运行该程序</span></span></p>
<p><span style="color:rgb(255,0,0);">注意：</span><span style="font-family:'宋体';">要指定并行度，如在本地运行设置</span>setMaster("local[2]")<span style="font-family:'宋体';">，相当于启动两个线程，一个给</span><span style="font-family:Calibri;">receiver</span><span style="font-family:'宋体';">，一个给</span><span style="font-family:Calibri;">computer</span><span style="font-family:'宋体';">。如果是在集群中运行，必须要求集群中可用</span><span style="font-family:Calibri;">core</span><span style="font-family:'宋体';">数大于</span><span style="font-family:Calibri;">1</span></p>
<p> <img src="https://img-blog.csdn.net/20170926090303598" alt=""></p>
<p> </p>
<p><span style="font-size:14px;">4.<span style="font-family:'宋体';">在</span><span style="font-family:Calibri;">Linux</span><span style="font-family:'宋体';">端命令行中输入单词</span></span></p>
<p> #nc -lk 9999</p>
<p>hello tom hello jerry  hello tom</p>
<p><span style="font-size:14px;">5.<span style="font-family:'宋体';">在</span>IDEA<span style="font-family:'宋体';">控制台中查看结果</span></span></p>
<p> <img src="https://img-blog.csdn.net/20170926090502924" alt=""></p>
<p><span style="color:rgb(255,0,0);">问题：</span><span style="font-family:'宋体';">结果每次在</span>Linux<span style="font-family:'宋体';">段输入的单词次数都被正确的统计出来，但是结果不能累加！如果需要累加需要使用</span><span style="font-family:Calibri;">updateStateByKey(func)</span><span style="font-family:'宋体';">来更新状态，下面给出一个例子：</span></p>
<table><tbody><tr><td valign="top">
<p><strong><span style="color:rgb(0,0,128);">package </span></strong>cn.itcast.spark.streaming<br><strong><span style="color:rgb(0,0,128);">import </span></strong>cn.itcast.spark.util.LoggerLevel<br><strong><span style="color:rgb(0,0,128);">import </span></strong>org.apache.spark.{HashPartitioner, SparkConf}<br><strong><span style="color:rgb(0,0,128);">import </span></strong>org.apache.spark.streaming.{StreamingContext, Seconds}<br><strong><span style="color:rgb(0,0,128);">object </span></strong>NetworkUpdateStateWordCount {<br>
  <em><span style="color:rgb(128,128,128);">/**</span><span style="color:rgb(128,128,128);"><br></span><span style="color:rgb(128,128,128);">    * String : 单词 hello</span><span style="color:rgb(128,128,128);"><br></span><span style="color:rgb(128,128,128);">    * Seq[Int] ：单词在当前批次出现的次数</span><span style="color:rgb(128,128,128);"><br></span><span style="color:rgb(128,128,128);">    * Option[Int] ： 历史结果</span><span style="color:rgb(128,128,128);"><br></span><span style="color:rgb(128,128,128);">    */</span><span style="color:rgb(128,128,128);"><br></span><span style="color:rgb(128,128,128);">  </span></em><strong><span style="color:rgb(0,0,128);">val</span></strong><em><span style="color:rgb(102,14,122);">updateFunc</span></em>= (iter:<span style="color:rgb(32,153,157);">Iterator</span>[(<span style="color:rgb(32,153,157);">String</span>,<span style="color:rgb(32,153,157);">Seq</span>[Int],
 Option[Int])]) =&gt; {<br>
    <em><span style="color:rgb(128,128,128);">//iter.flatMap(it=&gt;Some(it._2.sum + it._3.getOrElse(0)).map(x=&gt;(it._1,x)))</span><span style="color:rgb(128,128,128);"><br></span><span style="color:rgb(128,128,128);">    </span></em>iter.flatMap{<strong><span style="color:rgb(0,0,128);">case</span></strong>(x,y,z)=&gt;<em>Some</em>(y.sum + z.getOrElse(<span style="color:rgb(0,0,255);">0</span>)).map(m=&gt;(x, m))}<br>
  }<br><br>
  <strong><span style="color:rgb(0,0,128);">def </span></strong>main(args: Array[<span style="color:rgb(32,153,157);">String</span>]) {<br>
    LoggerLevel.<em>setStreamingLogLevels</em>()<br>
    <strong><span style="color:rgb(0,0,128);">val </span></strong>conf = <strong><span style="color:rgb(0,0,128);">new</span></strong>SparkConf().setMaster(<strong><span style="color:rgb(0,128,0);">"local[2]"</span></strong>).setAppName(<strong><span style="color:rgb(0,128,0);">"NetworkUpdateStateWordCount"</span></strong>)<br>
    <strong><span style="color:rgb(0,0,128);">val </span></strong>ssc = <strong><span style="color:rgb(0,0,128);">new</span></strong>StreamingContext(conf,<em>Seconds</em>(<span style="color:rgb(0,0,255);">5</span>))<br>
    <em><span style="color:rgb(128,128,128);">//做checkpoint 写入共享存储中</span><span style="color:rgb(128,128,128);"><br></span><span style="color:rgb(128,128,128);">    </span></em>ssc.checkpoint(<strong><span style="color:rgb(0,128,0);">"c://aaa"</span></strong>)<br>
    <strong><span style="color:rgb(0,0,128);">val </span></strong>lines = ssc.socketTextStream(<strong><span style="color:rgb(0,128,0);">"192.168.10.100"</span></strong>,<span style="color:rgb(0,0,255);">9999</span>)<br>
    <em><span style="color:rgb(128,128,128);">//reduceByKey 结果不累加</span><span style="color:rgb(128,128,128);"><br></span><span style="color:rgb(128,128,128);">    //val result = lines.flatMap(_.split(" ")).map((_, 1)).reduceByKey(_+_)</span><span style="color:rgb(128,128,128);"><br></span><span style="color:rgb(128,128,128);">    //updateStateByKey结果可以累加但是需要传入一个自定义的累加函数：updateFunc</span><span style="color:rgb(128,128,128);"><br></span><span style="color:rgb(128,128,128);">    </span></em><strong><span style="color:rgb(0,0,128);">val</span></strong>results = lines.flatMap(_.split(<strong><span style="color:rgb(0,128,0);">" "</span></strong>)).map((_,<span style="color:rgb(0,0,255);">1</span>)).updateStateByKey(<em><span style="color:rgb(102,14,122);">updateFunc</span></em>,<strong><span style="color:rgb(0,0,128);">new</span></strong>HashPartitioner(ssc.sparkContext.defaultParallelism),<strong><span style="color:rgb(0,0,128);">true</span></strong>)<br>
    results.print()<br>
    ssc.start()<br>
    ssc.awaitTermination()<br>
  }<br>
}</p>
<p> </p>
</td>
</tr></tbody></table><h2>4.2. <strong>Spark Streaming<span style="font-family:'宋体';">整合</span><span style="font-family:Cambria;">Kafka</span><span style="font-family:'宋体';">完成网站点击流实时统计</span></strong></h2>
<p>   Apache Kafka是一个分布式的消息发布-订阅系统。可以说，在任何实时大数据处理工具缺少与Kafka整合都是不完善的。使用Spark Streaming从Kafka中接受数据，这里会介绍两种方法：（1）使用Receivers和Kafka高层次的API；（2）【spark2.0版本以后最常用】使用Direct API，这是使用低层次的Kafka API,并没有使用到Receivers</p>
<p><span style="font-size:14px;">1.<span style="font-family:'宋体';">安装并配置</span>zk</span></p>
<p><span style="font-size:14px;">2.<span style="font-family:'宋体';">安装并配置</span>Kafka</span></p>
<p><span style="font-size:14px;">3.<span style="font-family:'宋体';">启动</span>zk</span></p>
<p><span style="font-size:14px;">4.<span style="font-family:'宋体';">启动</span>Kafka</span></p>
<p><span style="font-size:14px;">5.<span style="font-family:'宋体';">创建</span>topic</span></p>
<p><span style="color:rgb(51,51,51);">bin/kafka-topics.sh --create --zookeeper node1.itcast.cn:2181,node2.itcast.cn:2181 \</span></p>
<p><span style="color:rgb(51,51,51);">--replication-factor 3 --partitions 3 --topic urlcount</span></p>
<p><strong><span style="font-size:14px;">6.<span style="font-family:'宋体';">编写基于Receivers的</span>Spark Streaming<span style="font-family:'宋体';">应用程序</span></span></strong></p>
<p>
</p><table><tbody><tr><td valign="top">
<p><strong><span style="color:rgb(0,0,128);">package org</span></strong>.spark.streaming<br><strong><span style="color:rgb(0,0,128);">import </span></strong>org.apache.spark.{HashPartitioner, SparkConf}<br><strong><span style="color:rgb(0,0,128);">import </span></strong>org.apache.spark.storage.StorageLevel<br><strong><span style="color:rgb(0,0,128);">import </span></strong>org.apache.spark.streaming.kafka.KafkaUtils<br><strong><span style="color:rgb(0,0,128);">import </span></strong>org.apache.spark.streaming.{Seconds, StreamingContext}<br><br></p><table border="0" cellpadding="0" cellspacing="0" style="font-size:13px;color:rgb(0,0,0);border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;min-height:auto !important;background:none !important;"><tbody style="border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;min-height:auto !important;background:none !important;"><tr style="border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;min-height:auto !important;background:none !important;"><td class="code" style="border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;font-size:1em !important;min-height:auto !important;">
<div style="border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;min-height:auto !important;">
<div class="line number1 index0 alt2" style="border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;min-height:auto !important;">
<span style="background-color:rgb(255,255,255);"><code class="scala keyword" style="color:rgb(137,89,168) !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-weight:bold !important;font-size:1em !important;min-height:auto !important;"></code></span></div>
</div>
</td>
</tr></tbody></table>
objectKafkaWordCount {<br>
  defmain(args:Array[String]) {<br>
    if(args.length &lt; 4) {<br>
      System.err.println("Usage: KafkaWordCount &lt;zkQuorum&gt; &lt;group&gt; &lt;topics&gt; &lt;numThreads&gt;")<br>
      System.exit(1)<br>
    }<br>
    StreamingExamples.setStreamingLogLevels() //需要编写设置打印日志级别<br><br>
    valArray(zkQuorum, group, topics, numThreads) =args<br>
    valsparkConf=newSparkConf().setAppName("KafkaWordCount")<br>
    valssc= newStreamingContext(sparkConf, Seconds(2))<br>
    ssc.checkpoint("checkpoint")<br><br>
    valtopicMap=topics.split(",").map((_,numThreads.toInt)).toMap<br>
    vallines=KafkaUtils.createStream(ssc, zkQuorum, group, topicMap).map(_._2)<br>
    valwords=lines.flatMap(_.split(" "))<br>
    valwordCounts=words.map(x=&gt; (x, 1L))<br>
      .reduceByKeyAndWindow(_+_,_-_, Minutes(10), Seconds(2),2)<br>
    wordCounts.print()<br>
 <br>
    ssc.start()<br>
    ssc.awaitTermination()<br>
  }<br>
}<br></td>
</tr></tbody></table><span style="font-size:14px;"><br><strong>7.<span style="font-family:'宋体';">编写基于Direct API的</span>Spark Streaming<span style="font-family:'宋体';">应用程序</span></strong></span>
<p><span style="font-size:14px;"><span style="font-family:'宋体';"><br>
import org.apache.spark.SparkConf<br>
import org.apache.spark.streaming._<br>
import org.apache.spark.streaming.kafka010._<br><br>
object DirectKafkaWordCount{<br>
  def main(args: Array[String]): Unit = {<br>
    if (args.length &lt; 2) {<br>
      System.err.println(s"""<br>
                       |Usage: DirectKafkaWordCount &lt;brokers&gt; &lt;topics&gt;<br>
                       |  &lt;brokers&gt; is a list of one or more Kafka brokers<br>
                       |  &lt;topics&gt; is a list of one or more kafka topics to consume from<br>
                       |<br>
        """.stripMargin)<br>
      System.exit(1)<br>
    }<br>
    StreamingExamples.setStreamingLogLevels()<br><br>
    val Array(brokers, topics) = args<br><br>
    // Create context with 2 second batch interval<br>
    val sparkConf = new SparkConf().setAppName("DirectKafkaWordCount")<br>
    val ssc = new StreamingContext(sparkConf, Seconds(2))<br><br>
    // Create direct kafka stream with brokers and topics<br>
    val topicsSet = topics.split(",").toSet<br>
    val kafkaParams = Map[String, String]("metadata.broker.list" -&gt; brokers)<br>
    val messages = KafkaUtils.createDirectStream[String, String](<br>
      ssc,<br>
      LocationStrategies.PreferConsistent,<br>
      ConsumerStrategies.Subscribe[String, String](topicsSet, kafkaParams))<br><br>
    // Get the lines, split them into words, count the words and print<br>
    val lines = messages.map(_.value)<br>
    val words = lines.flatMap(_.split(" "))<br>
    val wordCounts = words.map(x =&gt; (x, 1L)).reduceByKey(_ + _)<br>
    wordCounts.print()<br><br>
    // Start the computation<br>
    ssc.start()<br>
    ssc.awaitTermination()<br>
  }<br>
}</span></span></p>
<p><span style="font-size:14px;"><span style="font-family:'宋体';"><strong>8.Direct API方式与Receivers相比，有以下优点：</strong></span></span></p>
<p><span style="font-family:'宋体';"></span></p>
<p style="font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;line-height:26px;">
<span style="font-size:15px;">（1）、</span><strong><span style="font-size:12px;">简化并行</span><span style="font-size:15px;">。</span></strong><span style="font-size:15px;">我们不需要创建多个Kafka 输入流，然后union他们。而使用directStream，</span><span class="wp_keywordlink_affiliate" style="font-size:15px;"><a href="https://www.iteblog.com/archives/tag/spark/" rel="nofollow" title="" style="color:rgb(0,166,124);text-decoration:none;">Spark</a></span><span style="font-size:15px;"> Streaming将会创建和Kafka分区一样的RDD分区个数，而且会从Kafka并行地读取数据，也就是说Spark分区将会和Kafka分区有一一对应的关系，这对我们来说很容易理解和使用；</span></p>
<p style="font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;line-height:26px;">
<span style="font-size:15px;">　　（2）、</span><strong><span style="font-size:12px;">高效</span><span style="font-size:15px;">。</span></strong><span style="font-size:15px;">第一种实现零数据丢失是通过将数据预先保存在WAL中，这将会复制一遍数据，这种方式实际上很不高效，因为这导致了数据被拷贝两次：一次是被Kafka复制；另一次是写到WAL中。但是本文介绍的方法因为没有Receiver，从而消除了这个问题，所以不需要WAL日志；</span></p>
<p style="font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;line-height:26px;">
<span style="font-size:15px;">　　（3）、</span><strong><span style="font-size:12px;">恰好一次语义（Exactly-once semantics）</span><span style="font-size:15px;">。Receivers方式</span></strong><span style="font-size:15px;">通过使用Kafka高层次的API把偏移量写入Zookeeper中，这是读取Kafka中数据的传统方法。虽然这种方法可以保证零数据丢失，但是还是存在一些情况导致数据会丢失，因为在失败情况下通过Spark
 Streaming读取偏移量和Zookeeper中存储的偏移量可能不一致。而本文提到的方法是通过Kafka低层次的API，并没有使用到Zookeeper，偏移量仅仅被Spark Streaming保存在Checkpoint中。这就消除了Spark Streaming和Zookeeper中偏移量的不一致，而且可以保证每个记录仅仅被Spark Streaming读取一次，即使是出现故障。</span></p>
<div><span style="font-size:32px;">有关sparkStreaming的详细代码详见(github)https://github.com/xiaoxiaobaixiong/sparkStreamingLearning</span></div>
<div><span style="font-size:32px;">官方sparkStreaming 2.2.0文档http://spark.apache.org/docs/latest/streaming-programming-guide.html</span></div>
<div><span style="font-size:32px;">Spark Streaming编程指南http://ifeve.com/spark-streaming-2/</span></div>
            </div>
                </div>