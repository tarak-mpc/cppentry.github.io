---
layout:     post
title:      Kafka总结（六）：Kafka Stream详解
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p><a href="https://mp.csdn.net/postedit/81283142" rel="nofollow">Kafka总结（一）：Kafka概述</a></p>

<p><a href="https://mp.csdn.net/postedit/81283229" rel="nofollow">Kafka总结（二）：Kafka核心组件</a></p>

<p><a href="https://mp.csdn.net/postedit/81283286" rel="nofollow">Kafka总结（三）：Kafka核心流程分析</a></p>

<p><a href="https://mp.csdn.net/postedit/81283397" rel="nofollow">Kafka总结（四）：Kafka命令操作</a></p>

<p><a href="https://mp.csdn.net/postedit/81283460" rel="nofollow">Kafka总结（五）：API编程详解</a></p>

<p><a href="https://mp.csdn.net/postedit/81283491" rel="nofollow">Kafka总结（六）：Kafka Stream详解</a></p>

<p><a href="https://mp.csdn.net/postedit/81283546" rel="nofollow">Kafka总结（七）：数据采集应用</a></p>

<p><a href="https://mp.csdn.net/postedit/81283568" rel="nofollow">Kafka总结（八）：KafKa与ELK整合应用</a></p>

<p><a href="https://mp.csdn.net/postedit/81283606" rel="nofollow">Kafka总结（九）：KafKa 与Spark整合应用</a></p>

<h2 style="margin-left:0cm;"><strong>1. KafKa Streams简介</strong></h2>

<ul><li><span style="color:#3f3f3f;">功能强大</span> <br><span style="color:#3f3f3f;">高拓展性，弹性，容错</span> <br><span style="color:#3f3f3f;">有状态和无状态处理</span> <br><span style="color:#3f3f3f;">基于事件时间的</span><span style="color:#3f3f3f;">Window</span><span style="color:#3f3f3f;">，</span><span style="color:#3f3f3f;">Join</span><span style="color:#3f3f3f;">，</span><span style="color:#3f3f3f;">Aggergations</span></li>
	<li><strong><span style="color:#3f3f3f;">轻量级</span></strong> <br><span style="color:#3f3f3f;">无需专门的集群</span> <br><span style="color:#3f3f3f;">没有外部以来</span> <br><span style="color:#3f3f3f;">一个库，而不是框架</span></li>
	<li><strong><span style="color:#3f3f3f;">完全集成</span></strong><span style="color:#3f3f3f;"> <br>
	100%</span><span style="color:#3f3f3f;">的</span><span style="color:#3f3f3f;">Kafka 0.10.0</span><span style="color:#3f3f3f;">版本兼容</span> <br><span style="color:#3f3f3f;">易于集成到现有的应用程序</span> <br><span style="color:#3f3f3f;">程序部署无需手工处理</span><span style="color:#3f3f3f;">(</span><span style="color:#3f3f3f;">这个指的应该是</span><span style="color:#3f3f3f;">Kafka</span><span style="color:#3f3f3f;">多分区机制对</span><span style="color:#3f3f3f;">Kafka Streams</span><span style="color:#3f3f3f;">多实例的自动匹配</span><span style="color:#3f3f3f;">)</span></li>
	<li><strong><span style="color:#3f3f3f;">实时性</span></strong> <br><span style="color:#3f3f3f;">毫秒级延迟</span> <br><span style="color:#3f3f3f;">并非微批处理</span> <br><span style="color:#3f3f3f;">窗口允许乱序数据</span> <br><span style="color:#3f3f3f;">允许迟到数据</span></li>
</ul><p style="margin-left:0cm;">虽然Kafka Streams只是一个java库，但是它可以解决如下类似问题：</p>

<ol><li>一次一件事件的处理而不是微批处理，延迟在毫秒级别；</li>
	<li>有状态的处理，包括连接操作（join）和聚合操作</li>
	<li>提供了必要的流处理原语，包括高级流处理DSL和低级流处理API。高级流处理DSL提供了常用的流处理变换操作，低级处理器API支持客户端自定义处理器并与状态仓库（state store）交互；</li>
	<li>使用类似于DataFlow的模型来处理乱序数据的事件窗口问题；</li>
	<li>分布式处理，有容错机制，可以快速容错；</li>
	<li>有重新处理数据的能力；</li>
</ol><h2 style="margin-left:0cm;"><strong>2. KafKa Streams基本概念</strong></h2>

<h3 style="margin-left:0cm;"><strong>1.流</strong></h3>

<p style="margin-left:0cm;">流（stream）是Kafka Streams提供的最重要的抽象，它代表一个无限的、不断更新的数据集。一个流就是由一个有序的、可重放的、支持故障转移的不可变的数据记录（data record）序列，其中每个数据记录被定义成一个键值对。</p>

<h3 style="margin-left:0cm;"><strong>2.流处理器</strong></h3>

<p style="margin-left:0cm;">一个流处理器（stream processor）是处理拓扑中的一个节点，它代表了拓扑中的处理步骤。</p>

<p style="margin-left:0cm;">一个流处理器从它所在的拓扑上游接收数据，通过Kafka Streams提供的流处理的基本方法，如map()、filter()、join()以及聚合等方法，对数据进行处理，然后将处理之后的一个或者多个输出结果发送给下游流处理器。一个拓扑中的流处理器有Source和Sink处理器连个特殊的流处理器；</p>

<ol><li>Source处理器：</li>
</ol><p style="margin-left:0cm;">该处理器没有任何上游处理器</p>

<p>     2.Sink处理器：该处理器没有任何下游处理器。该处理器将从上游处理器接受到的任何数据发送到指定的主题当中；</p>

<h3 style="margin-left:0cm;"><strong>3.处理器拓扑</strong></h3>

<p style="margin-left:0cm;">处理器拓扑（processor topology）是流处理应用程序进行数据处理的计算逻辑。一个处理器拓扑是由流处理器和相连接的流组成的有向无环图，流处理器是图的节点，流是图的边</p>

<p style="margin-left:0cm;"><img alt="" class="has" height="1200" src="https://img-blog.csdn.net/20180730132826879?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2ODA3ODYy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="883"></p>

<p style="margin-left:0cm;"> </p>

<p style="margin-left:0cm;">Kafka提供了两种定义流处理拓扑的API：</p>

<p style="margin-left:0cm;"><span style="color:#4f4f4f;">KafkaStreams DSL API.</span><span style="color:#4f4f4f;">：这种类型的API提供了一些开箱即用的数据转换操作算子例如：map、filter 和join和聚合类算子，开发这无需处理底层实现细节，缺点就是在一定程度上不够灵活，这样你就不必从头开始实现这些流处理器。</span></p>

<p style="margin-left:0cm;"><span style="color:#4f4f4f;">Low-levelAPI</span><span style="color:#4f4f4f;">：这些低级API允许开发人员定义和连接定制处理器和状态存储器进行交换，更加灵活，但是开发难度相对较大。</span></p>

<h3 style="margin-left:0cm;"><strong>4.时间</strong></h3>

<p style="margin-left:0cm;">流处理的一个很关键的方面就是时间，以及它是如何进行建模和整合的。比如，一些像Windows这样的窗口操作就是基于时间的界限所定义的。</p>

<p style="margin-left:0cm;">处理上时间相关的概念主要如下：</p>

<ol><li>事件时间（event time）：事件或者记录产生的时间。即事件在源头最初创建的时间。事件时间在语义上通常要在产生的时候嵌入一个时间戳字段。</li>
</ol><p style="margin-left:0cm;">例如：如果事件产生于汽车GPS传感器报道的位置变化，那么相关的事件时间就是GPS传感器捕捉位置变化的时间。（也就是说，这个时间通常是在流处理系统以外产生的。）</p>

<p>     2.处理时间（processing time）：流处理应用程序开始处理事件的时间点。（即事件进入流处理系统的时间。）这个处理时间到事件时间的间隔可能是毫秒，秒，小时，天或者更久远的时间。</p>

<p style="margin-left:0cm;">例如：假设有一个应用程序用来读取和处理来自汽车GPS传感器报告的地理位置数据，并将其呈现给车队管理仪表盘。在这里，应用程序中的处理事件可能是毫秒或者秒(例如基于Apache的Kafka 和Kafka Stream流实时管道)或者小时（例如基于 Apache Hadoop或者Apache Spark的管道）。</p>

<ol><li>摄取时间（ingestion time）：数据记录由KafkaBroker保存到 kafka topic对应分区的时间点。摄取时间类似事件时间，都是一个嵌入在数据记录中的时间戳字段。不同的是，摄取时间是由Kafka Broker附加在目标Topic上的，而不是附加在事件源上的。如果事件处理速度足够快，事件产生时间和写入Kafka的时间差就会非常小，这主要取决于具体的使用情况。因此，无法在摄取时间和事件时间之间进行二选一，两个语义是完全不同的。同时，数据还有可能没有摄取时间，比如旧版本的Kafka或者生产者不能直接生成时间戳（比如无法访问本地时钟。）</li>
</ol><p style="margin-left:0cm;">事件时间和摄取时间的选择是通过在Kafka（不是KafkaStreams）上进行配置实现的。从Kafka 0.10.X起，时间戳会被自动嵌入到Kafka的Message中，可以根据配置选择事件时间或者摄取时间。配置可以在broker或者topic中指定。Kafka Streams默认提供的时间抽取器会将这些嵌入的时间戳恢复原样。因此，应用程序的有效时间语义上依赖于这种嵌入时时间戳读取的配置。请参考：Developer Guide&lt;Timestamp Extractor&gt;来获取更进一步信息。</p>

<p style="margin-left:0cm;">Kafka Streams通过时间抽取器TimestampExtractor为每个记录分配时间戳，每个数据记录赋予时间戳之后就可以对数据进行聚合操作，实现窗口功能，能够方便的解决数据乱序的问题。Sreams中每个记录的时间戳(可能是乱序)可以进行时间相关的操作，比如Join，也可以用于相同应用中的多个输入数据流同步。</p>

<p style="margin-left:0cm;">时间提取器的具体实现方式主要是检索或者计算两种方式。可以根据数据记录的实际内容获取时间戳，也可以使用其他方式，比如本地时间戳。开发人员可以根据自己的业务需求选择不同的实现方式。</p>

<p style="margin-left:0cm;">注意：Kafka Streams中的摄取时间和其他流处理系统略有不同，其他流处理系统中的摄取时间指的是从数据源中获取到数据的时间，而kafka Streams中，摄取时间是指记录被追加到Kakfa topic中的时间。</p>

<p style="margin-left:0cm;">提示：使用时间是，你也应该保证时区和日历的其他方面是正确同步的，或者至少是了解之间的差异。例如，统一使用UTC或者Unix时间，指定时间信息格式一致，如统一精确到秒，你也不要混用不同语义的时间。</p>

<h3 style="margin-left:0cm;"><strong>5.状态</strong></h3>

<p style="margin-left:0cm;"><span style="color:#4f4f4f;">一些流处理应用不需要状态，这意味着每个消息的处理都是彼此独立的。如果你只需要进行消息转换，或者基于某些条件进行筛选，那么你的流处理应用程序的拓扑会非常简单。</span></p>

<p style="margin-left:0cm;"><span style="color:#4f4f4f;">然而，能够保存状态，就是的流处理系统可以应用在更多场景。你可以进行Join，Group by或者aggregate操作，Kafka Streams DSL供了很多这样的包含状态的DSL。</span></p>

<h3 style="margin-left:0cm;"><strong>6.KStream和KTable</strong></h3>

<ol><li>Kstream：</li>
</ol><p style="margin-left:0cm;">Kstream是一个由键值对构成的抽象记录流，每个键值对是一个独立的单元，即使相同的Key也不会覆盖，类似数据库的插入操作；</p>

<p>     2.Ktable：相同Key的每条记录只保存最新的一条记录，类似于数据库的基于主键更新</p>

<p style="margin-left:0cm;">无论是记录流（KStream）还是更新日志流（KTable）都可以从一个或者多个Kafka主题数据源来创建，一个Kstream可以与另一个Kstream或者Ktable进行join操作，或者聚合成一个Ktable，同样一个Ktable也可以转换成一个Kstream。</p>

<p style="margin-left:0cm;">将这些转换操作连在一起就构成了一个处理器拓扑；</p>

<h3 style="margin-left:0cm;"><strong>7.窗口</strong></h3>

<p style="margin-left:0cm;">对流数据按照时间进行分组，也就是按照时间把流分为多个窗口（window）。窗口是流处理状态转换操作的基本条件，一个窗口相关的操作通常需要存储中间状态，根据窗口的设置旧的状态在窗口中持续时间大于窗口大小之后就会被删除；</p>

<p style="margin-left:0cm;">一个窗口包括窗口大小和滑动步长两个属性：</p>

<ol><li>窗口大小：一条记录在窗口中持续的时间，持续时间超过窗口大小的记录将会被删除；</li>
	<li>滑动步长：指定了一个窗口每次相对于前一个窗口向前移动的距离。</li>
</ol><p style="margin-left:0cm;">滑动步长不能超过窗口大小，如果超过窗口大小则会导致部分记录不属于任何窗口而不被处理；</p>

<p style="margin-left:0cm;">Kafka Streams定义了三种窗口：</p>

<ol><li>跳跃时间窗口（hopping time window）：</li>
</ol><p style="margin-left:0cm;">大小固定，可能会重叠的窗口模型</p>

<p>     2.翻转时间窗口（tumbling time window）：</p>

<p style="margin-left:0cm;">大小固定，不可重叠，无间隙的一类窗口模型</p>

<p>     3.滑动窗口（sliding window）：</p>

<p style="margin-left:0cm;">大小固定并且沿着时间轴连续滑动的窗口模型，如果两条记录时间戳之差在窗口大小之内，则这两条数据记录属于同一个窗口。在Kafka流中，滑动窗口只有在join操作的时候才用到。</p>

<h2 style="margin-left:0cm;"><strong>3. KafKa Streams API介绍</strong></h2>

<p style="margin-left:0cm;">引入kafka-stream jar包</p>

<h3 style="margin-left:0cm;"><strong>1. KStream和Ktable</strong></h3>

<p style="margin-left:0cm;">通过KstreamBuilder类的stream()方法和table()方法分别创建一个Kstream和Ktable对象；</p>

<h3 style="margin-left:0cm;"><strong>2.窗口操作</strong></h3>

<p style="margin-left:0cm;">流式处理有时候可能需要将数据划分成多个时间段，以时间窗口滑动的形式处理，即通常所说的流上的窗口操作。窗口操作一般在连接和聚合等保存本地状态的程序中使用；</p>

<p style="margin-left:0cm;">我们说的窗口操作主要是指Kstream之间连接操作的JoinWindows以及聚合操作中使用的TimeWindows，这两个窗口都集成Windows类，通过指定窗口大小，将窗口内的数据集经过连接或者绝活操作存储到状态仓库中；</p>

<h3 style="margin-left:0cm;"><strong>3.连接操作</strong></h3>

<p style="margin-left:0cm;">连接操作是通过键将两个流的记录合并，并生成新的流；</p>

<p style="margin-left:0cm;">Kafka Streams将流抽象为Kstream和Ktable两种类型，因此连接操作也是这两类流之间的操作。即Kstream与Kstream、Ktable和Ktable，Kstream和Ktable之间的连接；</p>

<p style="margin-left:0cm;">而KStream，之间的连接操作往往都是基于窗口的，否则每次所有的数据都需要再保存，这样记录就会无限的增长；</p>

<p style="margin-left:0cm;">Kafka Streams提供了三种连接方式：</p>

<ol><li>内连接join()；</li>
	<li>左连接leftJoin()：</li>
	<li>外连接outerJoin()：</li>
</ol><h3 style="margin-left:0cm;"><strong>4.变换操作</strong></h3>

<p style="margin-left:0cm;">Kafka Streams提供了流处理的基本函数，包括过滤操作filter()，分组操作的GroupBY()和groupByKey()，映射操作的map()、mapValues()、flatMap()和flatMapValues()以及转换函数transform()等。这些变换操作都可以将一个Kstream和一个KTable对象变换为另一个Kstream或者Ktable对象，并且传给下游处理拓扑，将所有的操作连接起来就组成了一个复杂的处理拓扑；</p>

<h3 style="margin-left:0cm;"><strong>5.聚合操作</strong></h3>

<p style="margin-left:0cm;">聚合操作是一种有状态的转换；</p>

<p style="margin-left:0cm;">Kafka Streams定义了一个aggregate()方法，同时提供了一个Aggregator接口，我们通过该接口的apply()方法，在该方法中实现聚集函数的功能</p>

<p style="margin-left:0cm;">日志流和更新日志流都有appregate方法，不过在进行聚合操作之前需要将Kstream或者Ktable转换为KgroupStream或者KgroupedTable，然后再调用appgregate()方法进行聚合操作；</p>

<h2 style="margin-left:0cm;"><strong>4.接口恶意访问自动监测</strong></h2>

<p style="margin-left:0cm;">开发一款产品的时候，防止接口被恶意的攻击也是进行系统设计时候的需要考虑的问题之一，特别是对于Web应用，例如，用户通过一个无限循环的向服务器端发送请求，就会导致服务端产生大量的垃圾数据，同时还会增加服务器端的负载；</p>

<p style="margin-left:0cm;">针对这种恶意的请求，采用的一种常用的也是比较简单的方式就是：IP地址黑名单过滤，首先从应用接口调用频率考虑，例如当一个用户在1min之内如果访问次数超过我们设置的阈值，则认为该用户存在恶意的请求的嫌疑，因此我们将该用户的IP加入到黑名单之中，例如写到Redis中，在应用层进行处理的时候对于用户的每次访问首先查询Redis，判断用户的IP是否在黑名单之列，如果该IP仍然在黑名单之列，则禁止本次的访问。当然这个判断的操作还可以提前到负载均衡层面，在负载均衡代理层就直接拦截掉。同时利用Redis有效期设置功能，我们在将每个用户IP拉入黑名单保存到Redis的时候设置其有效时间，例如在30min之内不能再次访问，那么油门就可以将其的有效时间设置为30分钟，30分钟之后，该用户的IP从黑名单自动的移除；</p>

<p style="margin-left:0cm;">当然，这种只是简单的模拟这种场景，并没有考虑到一个局域网共用外网地址的情况，也没有对用户唯一标识进行过多的分析判断；</p>

<h3 style="margin-left:0cm;"><strong>1.应用描述</strong></h3>

<p style="margin-left:0cm;">我们将用户请求日志发送到Kafka，然后通过Kafka Streams实时计算1min之内每个用户请求的次数，如果次数超过了我们预设的阈值，则将该IP写入到Redis。</p>

<p style="margin-left:0cm;">对于IP黑名单的计算我们采用Kafka Streams，同时为了简单，我们这里认为一个IP地址既能唯一标识一个用户的一次访问。将用户访问日志写入到Kafka的一个名为‘access-log’的主题中，然后一个Kafka Streams应用订阅该主题，对IP进行实时的计算。</p>

<p style="margin-left:0cm;">由于本例中设计的规则是用户1分钟之内的访问次数是否命中预设阈值，因此在编码实现的时候采用一个时间窗口大小为1min，滑动步长也为1分钟的翻转时间窗口，并利用Kafka Streams提供的低级Processor API自定义一个Processor，将满足条件的IP插入到Redis。由于我们在计算的时候采用了时间窗口，因此无需管理在状态仓库的计算结果，如果不用时间窗口计算，我们可以在自定义的Processor里将命名规则的IP插入到Redis后，更新该IP在状态仓库的值为0。</p>

<h3 style="margin-left:0cm;"><strong>2.具体实现</strong></h3>

<p style="margin-left:0cm;">实例化Kstream对象</p>

<p style="margin-left:0cm;">KstreamBuilder builder = new KstreamBuilder();</p>

<p style="margin-left:0cm;">Kstream&lt;String,String&gt; accessLog = builder.stream(“access-log”);</p>

<p style="margin-left:0cm;">自定义一个Procssor ，该Processor功能是将命令时间窗口的规则IP写入到Redis中。</p>

<p style="margin-left:0cm;">自定义一个Processor只需要实现Processor接口即可：</p>

<p style="margin-left:0cm;">process方法：用于对收到的数据集执行对状态仓库的操作；</p>

<p style="margin-left:0cm;">init()方法：可以获取ProcessorContext实例，用来维护当前上下文；通过上下文ProcessorContext得到状态仓库实例以及使用上下文用于基于时间推移周期性的执行；</p>

<p style="margin-left:0cm;">punctuate():用于基于时间推移周期性执行；</p>

<p style="margin-left:0cm;">close:关闭相应资源操作</p>

<h2 style="margin-left:0cm;"><strong>5.小结</strong></h2>

<p style="margin-left:0cm;">Kafka Streams是在0.10版本之后新增的，是一个轻量级的java库，但提供了流处理数据的基本操作，处理依赖Kafka自身之外，Kafka Stream不再依赖于任何的其他框架。</p>

<p style="margin-left:0cm;">客户端通过订阅Kafka收集的数据，然后由Kafka Streams程序可以简单的进行实时数据的分析。</p>

<p style="margin-left:0cm;">例如：通过用户访问日志、系统日志数据可以很方便的实时统计出PV/UV以及实现日志监控报警系统等；</p>            </div>
                </div>