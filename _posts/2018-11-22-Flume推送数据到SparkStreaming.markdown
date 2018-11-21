---
layout:     post
title:      Flume推送数据到SparkStreaming
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>Flume<span style="font-family:'宋体';">推送数据给</span><span style="font-family:Calibri;">streaming</span><span style="font-family:'宋体';">其实是配置</span></p>
<p> <img src="" alt=""></p>
<p>把数据推送给端口，<span style="font-family:Calibri;">streaming</span><span style="font-family:'宋体';">直接去读端口。</span></p>
<p> </p>
<p>Flume<span style="font-family:'宋体';">的安装：</span></p>
<p>1.配置系统环境变量</p>
<p>2.配置<span style="font-family:Calibri;">flume</span><span style="font-family:'宋体';">的</span><span style="font-family:Calibri;">conf</span><span style="font-family:'宋体';">文件，里面有</span><span style="font-family:Calibri;">agent</span><span style="font-family:'宋体';">，</span><span style="font-family:Calibri;">channel</span><span style="font-family:'宋体';">，</span><span style="font-family:Calibri;">sink</span></p>
<p>3.可以在<span style="font-family:Calibri;">spark-env</span><span style="font-family:'宋体';">中配置</span><span style="font-family:Calibri;">java_home
</span><span style="font-family:'宋体';">和</span><span style="font-family:Calibri;">java_opts</span></p>
<p> </p>
<p>关于数据采集<span style="font-family:Calibri;">flume</span><span style="font-family:'宋体';">和</span><span style="font-family:Calibri;">kafka</span><span style="font-family:'宋体';">选择的理由？</span></p>
<p>数据大规模或者持续产生时采用<span style="font-family:Calibri;">kafka</span></p>
<p>数据量不稳定时用<span style="font-family:Calibri;">flume</span></p>
<p> </p>
<p>其他的</p>
<p>rocketMQ</p>
<p>Zeromq</p>
<p>Activemq</p>
<p> </p>
<p> </p>
<p>Flume <span style="font-family:'宋体';">用</span><span style="font-family:Calibri;">push</span><span style="font-family:'宋体';">的方式给</span><span style="font-family:Calibri;">streaming</span><span style="font-family:'宋体';">注意的问题？</span></p>
<p>1.可能<span style="font-family:Calibri;">streaming</span><span style="font-family:'宋体';">无法及时处理，导致崩溃。</span></p>
<p>解决办法：限速。但是可能会导致<span style="font-family:Calibri;">port</span><span style="font-family:'宋体';">崩溃</span></p>
<p>2.先启动<span style="font-family:Calibri;">streaming</span><span style="font-family:'宋体';">，在启动</span><span style="font-family:Calibri;">flume</span></p>
<p>3.综上所述的问题，最好的办法仍然是使用<span style="font-family:Calibri;">flume+kafka</span><span style="font-family:'宋体';">的配合。</span></p>
<p> </p>
<p> </p>
<p>Streaming<span style="font-family:'宋体';">一直产生</span><span style="font-family:Calibri;">rdd</span><span style="font-family:'宋体';">，</span><span style="font-family:Calibri;">rdd</span><span style="font-family:'宋体';">是对象，内存撑爆怎么办（重要）？</span></p>
<p>1.每次触发作用都会触发一个消息给消息循环体，消息循环清理<span style="font-family:Calibri;">rdd</span></p>
<p>2.发现状态报错，会<span style="font-family:Calibri;">checkpoint</span><span style="font-family:'宋体';">，此时由内存转为磁盘。</span></p>
<p align="justify"> </p>
<p align="justify">代码实现：</p>
<p>SparkConf conf = new SparkConf().setMaster("local[4]").setAppName("FlumePushDate2SparkStreaming");</p>
<p> </p>
<p>JavaStreamingContext jsc = new JavaStreamingContext(conf, Durations.seconds(30));</p>
<p> </p>
<p>JavaReceiverInputDStream lines = FlumeUtils.createStream(jsc,"Master", 9999);</p>
<p></p>
<p>/*</p>
<p> * <span style="font-family:'宋体';">第四步：接下来就像对于</span><span style="font-family:Calibri;">RDD</span><span style="font-family:'宋体';">编程一样基于</span><span style="font-family:Calibri;">DStream</span><span style="font-family:'宋体';">进行编程！！！原因是</span><span style="font-family:Calibri;">DStream</span><span style="font-family:'宋体';">是</span><span style="font-family:Calibri;">RDD</span><span style="font-family:'宋体';">产生的模板（或者说类），在</span><span style="font-family:Calibri;">Spark
 Streaming</span><span style="font-family:'宋体';">具体</span></p>
<p> * <span style="font-family:'宋体';">发生计算前，其实质是把每个</span><span style="font-family:Calibri;">Batch</span><span style="font-family:'宋体';">的</span><span style="font-family:Calibri;">DStream</span><span style="font-family:'宋体';">的操作翻译成为对</span><span style="font-family:Calibri;">RDD</span><span style="font-family:'宋体';">的操作！！！</span></p>
<p> *<span style="font-family:'宋体';">对初始的</span><span style="font-family:Calibri;">DStream</span><span style="font-family:'宋体';">进行</span><span style="font-family:Calibri;">Transformation</span><span style="font-family:'宋体';">级别的处理，例如</span><span style="font-family:Calibri;">map</span><span style="font-family:'宋体';">、</span><span style="font-family:Calibri;">filter</span><span style="font-family:'宋体';">等高阶函数等的编程，来进行具体的数据计算</span></p>
<p>     * 第<span style="font-family:Calibri;">4.1</span><span style="font-family:'宋体';">步：讲每一行的字符串拆分成单个的单词</span></p>
<p>     */</p>
<p>JavaDStream&lt;String&gt; words = lines.flatMap(new FlatMapFunction&lt;SparkFlumeEvent, String&gt;() { //<span style="font-family:'宋体';">如果是</span><span style="font-family:Calibri;">Scala</span><span style="font-family:'宋体';">，由于</span><span style="font-family:Calibri;">SAM</span><span style="font-family:'宋体';">转换，所以可以写成</span><span style="font-family:Calibri;">val
 words = lines.flatMap { line =&gt; line.split(" ")}</span></p>
<p> </p>
<p>@Override</p>
<p>public Iterable&lt;String&gt; call(SparkFlumeEvent event) throws Exception {</p>
<p>String line = new String(event.event().getBody().array());</p>
<p>return Arrays.asList(line.split(" "));</p>
<p>}</p>
<p>});</p>
            </div>
                </div>