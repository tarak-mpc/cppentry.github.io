---
layout:     post
title:      kafka stream 概述
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：转自 http://www.234plus.com/					https://blog.csdn.net/ZhongGuoZhiChuang/article/details/78901557				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p style="border:0px;font-size:14px;line-height:1.8;clear:none;width:610px;font-family:Helvetica, 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif;">
</p>
<h3 style="font-size:18px;font-weight:normal;border:0px;clear:none;width:610px;color:rgb(34,34,34);line-height:36px;font-family:Helvetica, 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif;">
Kafka Stream是什么</h3>
<p></p>
<p style="border:0px;font-size:14px;line-height:1.8;clear:none;width:610px;font-family:Helvetica, 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif;">
Kafka Stream是Apache Kafka从0.10版本引入的一个新Feature。它是提供了对存储于Kafka内的数据进行流式处理和分析的功能。</p>
<p style="border:0px;font-size:14px;line-height:1.8;clear:none;width:610px;font-family:Helvetica, 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif;">
Kafka Stream的特点如下：</p>
<ul style="border:0px;clear:left;font-family:Helvetica, 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif;font-size:14px;"><li style="border:0px;clear:none;">
Kafka Stream提供了一个非常简单而轻量的Library，它可以非常方便地嵌入任意Java应用中，也可以任意方式打包和部署</li><li style="border:0px;clear:none;">
除了Kafka外，无任何外部依赖</li><li style="border:0px;clear:none;">
充分利用Kafka分区机制实现水平扩展和顺序性保证</li><li style="border:0px;clear:none;">
通过可容错的state store实现高效的状态操作（如windowed join和aggregation）</li><li style="border:0px;clear:none;">
支持正好一次处理语义</li><li style="border:0px;clear:none;">
提供记录级的处理能力，从而实现毫秒级的低延迟</li><li style="border:0px;clear:none;">
支持基于事件时间的窗口操作，并且可处理晚到的数据（late arrival of records）</li><li style="border:0px;clear:none;">
同时提供底层的处理原语Processor（类似于Storm的spout和bolt），以及高层抽象的DSL（类似于Spark的map/group/reduce）</li></ul><br><p style="border:0px;font-size:14px;line-height:1.8;clear:none;width:610px;font-family:Helvetica, 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif;">
Spark和Storm都是流式处理<span style="font-weight:600;border:0px;">框架</span>，而Kafka Stream提供的是一个基于Kafka的流式处理<span style="font-weight:600;border:0px;">类库</span>。框架要求开发者按照特定的方式去开发逻辑部分，供框架调用。开发者很难了解框架的具体运行方式，从而使得调试成本高，并且使用受限。而Kafka
 Stream作为流式处理<span style="font-weight:600;border:0px;">类库</span>，直接提供具体的类给开发者调用，整个应用的运行方式主要由开发者控制，方便使用和调试。</p>
<p style="border:0px;font-size:14px;line-height:1.8;clear:none;width:610px;font-family:Helvetica, 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif;">
</p>
<h3 style="font-size:18px;font-weight:normal;border:0px;clear:none;width:610px;color:rgb(34,34,34);line-height:36px;font-family:Helvetica, 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif;">
KTable vs. KStream</h3>
<p></p>
<p style="border:0px;font-size:14px;line-height:1.8;clear:none;width:610px;font-family:Helvetica, 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif;">
KTable和KStream是Kafka Stream中非常重要的两个概念，它们是Kafka实现各种语义的基础。因此这里有必要分析下二者的区别。</p>
<p style="border:0px;font-size:14px;line-height:1.8;clear:none;width:610px;font-family:Helvetica, 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif;">
KStream是一个数据流，可以认为所有记录都通过Insert only的方式插入进这个数据流里。而KTable代表一个完整的数据集，可以理解为数据库中的表。由于每条记录都是Key-Value对，这里可以将Key理解为数据库中的Primary Key，而Value可以理解为一行记录。可以认为KTable中的数据都是通过Update only的方式进入的。也就意味着，如果KTable对应的Topic中新进入的数据的Key已经存在，那么从KTable只会取出同一Key对应的最后一条数据，相当于新的数据更新了旧的数据。</p>
<p style="border:0px;font-size:14px;line-height:1.8;clear:none;width:610px;font-family:Helvetica, 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif;">
以下图为例，假设有一个KStream和KTable，基于同一个Topic创建，并且该Topic中包含如下图所示5条数据。此时遍历KStream将得到与Topic内数据完全一样的所有5条数据，且顺序不变。而此时遍历KTable时，因为这5条记录中有3个不同的Key，所以将得到3条记录，每个Key对应最新的值，并且这三条数据之间的顺序与原来在Topic中的顺序保持一致。这一点与Kafka的日志compact相同。</p>
<p style="border:0px;font-size:14px;line-height:1.8;clear:none;width:610px;font-family:Helvetica, 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif;">
(点击放大图像)</p>
<p style="border:0px;font-size:14px;line-height:1.8;clear:none;width:610px;font-family:Helvetica, 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif;">
<a href="https://res.infoq.com/articles/kafka-analysis-part-7/zh/resources/08.png" rel="nofollow" style="color:rgb(40,106,178);border:0px;"><img src="https://res.infoq.com/articles/kafka-analysis-part-7/zh/resources/08.png" width="550" alt="" style="border:0px;"></a></p>
<p style="border:0px;font-size:14px;line-height:1.8;clear:none;width:610px;font-family:Helvetica, 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif;">
此时如果对该KStream和KTable分别基于key做Group，对Value进行Sum，得到的结果将会不同。对KStream的计算结果是&lt;Jack，4&gt;，&lt;Lily，7&gt;，&lt;Mike，4&gt;。而对Ktable的计算结果是&lt;Mike，4&gt;，&lt;Jack，3&gt;，&lt;Lily，5&gt;。</p>
<h2 style="font-size:20px;font-weight:normal;border:0px;clear:none;width:610px;color:rgb(34,34,34);line-height:24px;font-family:Helvetica, 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif;">
Kafka Stream如何解决流式系统中关键问题</h2>
<h3 style="font-size:18px;font-weight:normal;border:0px;clear:none;width:610px;color:rgb(34,34,34);line-height:36px;font-family:Helvetica, 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif;">
时间</h3>
<p style="border:0px;font-size:14px;line-height:1.8;clear:none;width:610px;font-family:Helvetica, 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif;">
在流式数据处理中，时间是数据的一个非常重要的属性。从Kafka 0.10开始，每条记录除了Key和Value外，还增加了timestamp属性。目前Kafka Stream支持三种时间</p>
<ul style="border:0px;clear:left;font-family:Helvetica, 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif;font-size:14px;"><li style="border:0px;clear:none;">
事件发生时间。事件发生的时间，包含在数据记录中。发生时间由Producer在构造ProducerRecord时指定。并且需要Broker或者Topic将message.timestamp.type设置为CreateTime（默认值）才能生效。</li><li style="border:0px;clear:none;">
消息接收时间，也即消息存入Broker的时间。当Broker或Topic将message.timestamp.type设置为LogAppendTime时生效。此时Broker会在接收到消息后，存入磁盘前，将其timestamp属性值设置为当前机器时间。一般消息接收时间比较接近于事件发生时间，部分场景下可代替事件发生时间。</li><li style="border:0px;clear:none;">
消息处理时间，也即Kafka Stream处理消息时的时间。</li></ul><p style="border:0px;font-size:14px;line-height:1.8;clear:none;width:610px;font-family:Helvetica, 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif;">
注：Kafka Stream允许通过实现org.apache.kafka.streams.processor.TimestampExtractor接口自定义记录时间。</p>
<h3 style="font-size:18px;font-weight:normal;border:0px;clear:none;width:610px;color:rgb(34,34,34);line-height:36px;font-family:Helvetica, 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif;">
窗口</h3>
<p style="border:0px;font-size:14px;line-height:1.8;clear:none;width:610px;font-family:Helvetica, 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif;">
前文提到，流式数据是在时间上无界的数据。而聚合操作只能作用在特定的数据集，也即有界的数据集上。因此需要通过某种方式从无界的数据集上按特定的语义选取出有界的数据。窗口是一种非常常用的设定计算边界的方式。不同的流式处理系统支持的窗口类似，但不尽相同。</p>
<p style="border:0px;font-size:14px;line-height:1.8;clear:none;width:610px;font-family:Helvetica, 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif;">
Kafka Stream支持的窗口如下。</p>
<ol style="border:0px;width:549px;clear:left;font-family:Helvetica, 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif;font-size:14px;"><li style="border:none;clear:none;text-align:left;">
Hopping Time Window 该窗口定义如下图所示。它有两个属性，一个是Window size，一个是Advance interval。Window size指定了窗口的大小，也即每次计算的数据集的大小。而Advance interval定义输出的时间间隔。一个典型的应用场景是，每隔5秒钟输出一次过去1个小时内网站的PV或者UV。
<p style="border:0px;line-height:1.8;clear:none;width:539px;">
(点击放大图像)</p>
<p style="border:0px;line-height:1.8;clear:none;width:539px;">
<a href="https://res.infoq.com/articles/kafka-analysis-part-7/zh/resources/09.gif" rel="nofollow" style="color:rgb(40,106,178);border:0px;"><img src="https://res.infoq.com/articles/kafka-analysis-part-7/zh/resources/09.gif" width="550" alt="" style="border:0px;"></a></p>
</li><li style="border:none;clear:none;text-align:left;">
Tumbling Time Window该窗口定义如下图所示。可以认为它是Hopping Time Window的一种特例，也即Window size和Advance interval相等。它的特点是各个Window之间完全不相交。
<p style="border:0px;line-height:1.8;clear:none;width:539px;">
(点击放大图像)</p>
<p style="border:0px;line-height:1.8;clear:none;width:539px;">
<a href="https://res.infoq.com/articles/kafka-analysis-part-7/zh/resources/10.gif" rel="nofollow" style="color:rgb(40,106,178);border:0px;"><img src="https://res.infoq.com/articles/kafka-analysis-part-7/zh/resources/10.gif" width="550" alt="" style="border:0px;"></a></p>
</li><li style="border:none;clear:none;text-align:left;">
Sliding Window该窗口只用于2个KStream进行Join计算时。该窗口的大小定义了Join两侧KStream的数据记录被认为在同一个窗口的最大时间差。假设该窗口的大小为5秒，则参与Join的2个KStream中，记录时间差小于5的记录被认为在同一个窗口中，可以进行Join计算。</li><li style="border:none;clear:none;text-align:left;">
Session Window该窗口用于对Key做Group后的聚合操作中。它需要对Key做分组，然后对组内的数据根据业务需求定义一个窗口的起始点和结束点。一个典型的案例是，希望通过Session Window计算某个用户访问网站的时间。对于一个特定的用户（用Key表示）而言，当发生登录操作时，该用户（Key）的窗口即开始，当发生退出操作或者超时时，该用户（Key）的窗口即结束。窗口结束时，可计算该用户的访问时间或者点击次数等。</li></ol><h3 style="font-size:18px;font-weight:normal;border:0px;clear:none;width:610px;color:rgb(34,34,34);line-height:36px;font-family:Helvetica, 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif;">
Join</h3>
<p style="border:0px;font-size:14px;line-height:1.8;clear:none;width:610px;font-family:Helvetica, 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif;">
Kafka Stream由于包含KStream和Ktable两种数据集，因此提供如下Join计算</p>
<ul style="border:0px;clear:left;font-family:Helvetica, 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif;font-size:14px;"><li style="border:0px;clear:none;">
KTable Join KTable 结果仍为KTable。任意一边有更新，结果KTable都会更新。</li><li style="border:0px;clear:none;">
KStream Join KStream 结果为KStream。必须带窗口操作，否则会造成Join操作一直不结束。</li><li style="border:0px;clear:none;">
KStream Join KTable / GlobakKTable 结果为KStream。只有当KStream中有新数据时，才会触发Join计算并输出结果。KStream无新数据时，KTable的更新并不会触发Join计算，也不会输出数据。并且该更新只对下次Join生效。一个典型的使用场景是，KStream中的订单信息与KTable中的用户信息做关联计算。</li></ul><p style="border:0px;font-size:14px;line-height:1.8;clear:none;width:610px;font-family:Helvetica, 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif;">
对于Join操作，如果要得到正确的计算结果，需要保证参与Join的KTable或KStream中Key相同的数据被分配到同一个Task。具体方法是</p>
<ul style="border:0px;clear:left;font-family:Helvetica, 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif;font-size:14px;"><li style="border:0px;clear:none;">
参与Join的KTable或KStream的Key类型相同（实际上，业务含意也应该相同）</li><li style="border:0px;clear:none;">
参与Join的KTable或KStream对应的Topic的Partition数相同</li><li style="border:0px;clear:none;">
Partitioner策略的最终结果等效（实现不需要完全一样，只要效果一样即可），也即Key相同的情况下，被分配到ID相同的Partition内</li></ul><p style="border:0px;font-size:14px;line-height:1.8;clear:none;width:610px;font-family:Helvetica, 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif;">
如果上述条件不满足，可通过调用如下方法使得它满足上述条件。</p>
<pre style="border:1px solid rgb(232,232,232);font-family:Consolas, Monaco, 'Andale Mono', monospace;background-color:rgb(245,242,240);color:rgb(49,78,100);line-height:21px;width:597.797px;overflow:auto;clear:none;font-size:14px;">KStream&lt;K, V&gt; through(Serde&lt;K&gt; keySerde, Serde&lt;V&gt; valSerde, StreamPartitioner&lt;K, V&gt; partitioner, String topic)</pre>
<h3 style="font-size:18px;font-weight:normal;border:0px;clear:none;width:610px;color:rgb(34,34,34);line-height:36px;font-family:Helvetica, 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif;">
聚合与乱序处理</h3>
<p style="border:0px;font-size:14px;line-height:1.8;clear:none;width:610px;font-family:Helvetica, 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif;">
聚合操作可应用于KStream和KTable。当聚合发生在KStream上时必须指定窗口，从而限定计算的目标数据集。</p>
<p style="border:0px;font-size:14px;line-height:1.8;clear:none;width:610px;font-family:Helvetica, 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif;">
需要说明的是，聚合操作的结果肯定是KTable。因为KTable是可更新的，可以在晚到的数据到来时（也即发生数据乱序时）更新结果KTable。</p>
<p style="border:0px;font-size:14px;line-height:1.8;clear:none;width:610px;font-family:Helvetica, 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif;">
这里举例说明。假设对KStream以5秒为窗口大小，进行Tumbling Time Window上的Count操作。并且KStream先后出现时间为1秒, 3秒, 5秒的数据，此时5秒的窗口已达上限，Kafka Stream关闭该窗口，触发Count操作并将结果3输出到KTable中（假设该结果表示为&lt;1-5,3&gt;）。若1秒后，又收到了时间为2秒的记录，由于1-5秒的窗口已关闭，若直接抛弃该数据，则可认为之前的结果&lt;1-5,3&gt;不准确。而如果直接将完整的结果&lt;1-5,4&gt;输出到KStream中，则KStream中将会包含该窗口的2条记录，&lt;1-5,3&gt;,
 &lt;1-5,4&gt;，也会存在肮数据。因此Kafka Stream选择将聚合结果存于KTable中，此时新的结果&lt;1-5,4&gt;会替代旧的结果&lt;1-5,3&gt;。用户可得到完整的正确的结果。</p>
<p style="border:0px;font-size:14px;line-height:1.8;clear:none;width:610px;font-family:Helvetica, 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif;">
这种方式保证了数据准确性，同时也提高了容错性。</p>
<p style="border:0px;font-size:14px;line-height:1.8;clear:none;width:610px;font-family:Helvetica, 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif;">
但需要说明的是，Kafka Stream并不会对所有晚到的数据都重新计算并更新结果集，而是让用户设置一个retention period，将每个窗口的结果集在内存中保留一定时间，该窗口内的数据晚到时，直接合并计算，并更新结果KTable。超过retention period后，该窗口结果将从内存中删除，并且晚到的数据即使落入窗口，也会被直接丢弃。</p>
<h3 style="font-size:18px;font-weight:normal;border:0px;clear:none;width:610px;color:rgb(34,34,34);line-height:36px;font-family:Helvetica, 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif;">
容错</h3>
<p style="border:0px;font-size:14px;line-height:1.8;clear:none;width:610px;font-family:Helvetica, 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif;">
Kafka Stream从如下几个方面进行容错</p>
<ul style="border:0px;clear:left;font-family:Helvetica, 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif;font-size:14px;"><li style="border:0px;clear:none;">
高可用的Partition保证无数据丢失。每个Task计算一个Partition，而Kafka数据复制机制保证了Partition内数据的高可用性，故无数据丢失风险。同时由于数据是持久化的，即使任务失败，依然可以重新计算。</li><li style="border:0px;clear:none;">
状态存储实现快速故障恢复和从故障点继续处理。对于Join和聚合及窗口等有状态计算，状态存储可保存中间状态。即使发生Failover或Consumer Rebalance，仍然可以通过状态存储恢复中间状态，从而可以继续从Failover或Consumer Rebalance前的点继续计算。</li><li style="border:0px;clear:none;">
KTable与retention period提供了对乱序数据的处理能力。</li></ul><br><p style="border:0px;font-size:14px;line-height:1.8;clear:none;width:610px;font-family:Helvetica, 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif;">
<br></p>
<div><br></div>
            </div>
                </div>