---
layout:     post
title:      Flume连接HDFS和Hive
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/qq_24699959/article/details/79657304				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>记录一下在CDH中配置flume</p>

<h3 id="flume连接hdfs">Flume连接HDFS</h3>

<p>1、进入Flume配置 <br>
<img title="" alt="这里写图片描述" src="https://img-blog.csdn.net/20180323103030894?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI0Njk5OTU5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"> <br>
<img title="" alt="这里写图片描述" src="https://img-blog.csdn.net/20180323103040409?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI0Njk5OTU5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"> <br>
<img title="" alt="这里写图片描述" src="https://img-blog.csdn.net/20180323103048454?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI0Njk5OTU5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></p>

<p>2、配置flume.conf <br>
<img title="" alt="这里写图片描述" src="https://img-blog.csdn.net/20180323104919161?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI0Njk5OTU5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></p>



<pre class="prettyprint"><code class="language-c hljs "> <span class="hljs-preprocessor"># Name the components on this agent</span>
 a1.sources = r1
 a1.sinks = k1
 a1.channels = c1

 <span class="hljs-preprocessor"># sources</span>
 a1.sources.r1.type = netcat
 a1.sources.r1.bind = <span class="hljs-number">0.0</span><span class="hljs-number">.0</span><span class="hljs-number">.0</span>
 a1.sources.r1.port = <span class="hljs-number">41414</span>

 <span class="hljs-preprocessor"># sinks</span>
 a1.sinks.k1.type = hdfs
 a1.sinks.k1.hdfs.path = hdfs:<span class="hljs-comment">//slave1/flume/events/%y-%m-%d/%H%M/%S</span>
 a1.sinks.k1.hdfs.filePrefix = events-
 a1.sinks.k1.hdfs.round = <span class="hljs-keyword">true</span>
 a1.sinks.k1.hdfs.roundValue = <span class="hljs-number">10</span>
 a1.sinks.k1.hdfs.roundUnit = minute
 a1.sinks.k1.hdfs.useLocalTimeStamp=<span class="hljs-keyword">true</span>
 a1.sinks.k1.hdfs.batchSize = <span class="hljs-number">10</span>
 a1.sinks.k1.hdfs.fileType = DataStream

 <span class="hljs-preprocessor"># channels</span>
 a1.channels.c1.type = memory
 a1.channels.c1.capacity = <span class="hljs-number">1000</span>
 a1.channels.c1.transactionCapacity = <span class="hljs-number">100</span>

 <span class="hljs-preprocessor"># Bind the source and sink to the channel</span>
 a1.sources.r1.channels = c1
 a1.sinks.k1.channel = c1</code></pre>

<p>3、测试telnet通信</p>



<pre class="prettyprint"><code class="language-sh hljs ">telnet slave1 41414</code></pre>

<p><img title="" alt="这里写图片描述" src="https://img-blog.csdn.net/20180323114050735?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI0Njk5OTU5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></p>

<p>4、查看日志找到HDFS文件 <br>
<img title="" alt="这里写图片描述" src="https://img-blog.csdn.net/20180323114201967?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI0Njk5OTU5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></p>

<p>5、查看文件内容，测试成功 <br>
<img title="" alt="这里写图片描述" src="https://img-blog.csdn.net/20180323114209380?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI0Njk5OTU5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></p>

<hr>



<h3 id="windows下flume连接hive">Windows下Flume连接Hive</h3>

<p>1、配置CDH下的flume <br>
<img title="" alt="这里写图片描述" src="https://img-blog.csdn.net/20180326172534803?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMjQ2OTk5NTk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast"></p>



<pre class="prettyprint"><code class="language-c hljs "> <span class="hljs-preprocessor"># Name the components on this agent</span>
a1.sources=r1
a1.sinks=k1
a1.channels=c1

<span class="hljs-preprocessor"># source</span>
a1.sources.r1.type=avro
a1.sources.r1.bind=<span class="hljs-number">0.0</span><span class="hljs-number">.0</span><span class="hljs-number">.0</span>
a1.sources.r1.port=<span class="hljs-number">43434</span>

 <span class="hljs-preprocessor"># sink</span>
a1.sinks.k1.type = hive
a1.sinks.k1.hive.metastore = thrift:<span class="hljs-comment">//192.168.18.33:9083</span>
a1.sinks.k1.hive.database = bd14
a1.sinks.k1.hive.table = flume_log
a1.sinks.k1.useLocalTimeStamp = <span class="hljs-keyword">true</span>
a1.sinks.k1.serializer = DELIMITED
a1.sinks.k1.serializer.delimiter = <span class="hljs-string">"\t"</span>
a1.sinks.k1.serializer.serdeSeparator = <span class="hljs-string">'\t'</span>
a1.sinks.k1.serializer.fieldnames = id,time,context
a1.sinks.k1.hive.txnsPerBatchAsk = <span class="hljs-number">5</span>

 <span class="hljs-preprocessor"># channel</span>
a1.channels.c1.type=memory
a1.channels.c1.capacity=<span class="hljs-number">1000</span>
a1.channels.c1.transactionCapacity=<span class="hljs-number">100</span>

 <span class="hljs-preprocessor"># Bind the source and sink to the channel</span>
a1.sources.r1.channels=c1
a1.sinks.k1.channel=c1</code></pre>

<p>2、配置Windows下的flume</p>



<pre class="prettyprint"><code class="language-c hljs "> <span class="hljs-preprocessor"># Name the components on this agent</span>
 a1.sources = r1
 a1.sinks = k1
 a1.channels = c1

 <span class="hljs-preprocessor"># source</span>
 a1.sources.r1.type = spooldir
 a1.sources.r1.spoolDir = F:\\test
 a1.sources.r1.fileHeader = <span class="hljs-keyword">true</span>

 <span class="hljs-preprocessor"># sink</span>
 a1.sinks.k1.type = avro
 a1.sinks.k1.hostname = <span class="hljs-number">192.168</span><span class="hljs-number">.18</span><span class="hljs-number">.34</span>
 a1.sinks.k1.port = <span class="hljs-number">43434</span>

 <span class="hljs-preprocessor"># channel</span>
 a1.channels.c1.type = memory
 a1.channels.c1.capacity = <span class="hljs-number">1000</span>
 a1.channels.c1.transactionCapacity = <span class="hljs-number">100</span>

 <span class="hljs-preprocessor"># Bind the source and sink to the channel</span>
 a1.sources.r1.channels = c1
 a1.sinks.k1.channel = c1</code></pre>

<p>3、在hive中创建日志表 <br>
<img title="" alt="这里写图片描述" src="https://img-blog.csdn.net/20180326175839273?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI0Njk5OTU5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"> <br>
在flume文档中要求将hive表分桶以及设置为orc格式，测试不声明orc格式，Hive将不会收到数据</p>



<pre class="prettyprint"><code class="language-sql hljs "><span class="hljs-operator"><span class="hljs-keyword">create</span> <span class="hljs-keyword">table</span> flume_log(
id <span class="hljs-keyword">int</span>
,<span class="hljs-keyword">time</span> string
,context string
)
clustered <span class="hljs-keyword">by</span> (id) <span class="hljs-keyword">into</span> <span class="hljs-number">3</span> buckets
stored <span class="hljs-keyword">as</span> orc;</span></code></pre>

<p>4、创建日志文件到监控目录F:\test <br>
<img title="" alt="这里写图片描述" src="https://img-blog.csdn.net/20180326180019471?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI0Njk5OTU5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></p>

<p>5、在Windows中 flume的bin目录下启动flume</p>



<pre class="prettyprint"><code class="language-sh hljs lasso">flume<span class="hljs-attribute">-ng</span><span class="hljs-built_in">.</span>cmd agent <span class="hljs-attribute">-conf</span><span class="hljs-attribute">-file</span> <span class="hljs-built_in">..</span>/conf/windows<span class="hljs-built_in">.</span>conf <span class="hljs-attribute">-name</span> a1 <span class="hljs-attribute">-property</span> flume<span class="hljs-built_in">.</span>root<span class="hljs-built_in">.</span>logger<span class="hljs-subst">=</span>INFO,console</code></pre>

<p>6、在Windows中查找一个log文件拖放到F:\test中，内容如下 <br>
<img title="" alt="这里写图片描述" src="https://img-blog.csdn.net/20180327100123985?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI0Njk5OTU5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></p>

<p>7、当flume读取完文件后，文件后缀会增加completed <br>
<img title="" alt="这里写图片描述" src="https://img-blog.csdn.net/20180327095800731?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI0Njk5OTU5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></p>

<p>8、查看Hive表 <br>
<img title="" alt="这里写图片描述" src="https://img-blog.csdn.net/20180327100200367?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI0Njk5OTU5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></p>

<p>9、测试成功，本来是想通过impala查询Hive表，但Impala不支持orc格式的Hive表，而flume中sink端需要采用orc格式传输数据，所以只能放弃impala，后续解决问题再进行补充</p>

<p><strong>三、遇到问题</strong></p>

<p>1、Flume无法连接到HDFS <br>
解决：a1.sinks.k1.hdfs.path = hdfs://slave1:9000/flume/events/%y-%m-%d/%H%M/%S <br>
改为 a1.sinks.k1.hdfs.path = hdfs://slave1/flume/events/%y-%m-%d/%H%M/%S</p>

<p>原因：在CDH的Flume中，设置路径只需要IP地址，不需要配置端口</p>

<p>2、HDFS文件存在乱码 <br>
<img title="" alt="这里写图片描述" src="https://img-blog.csdn.net/20180323112640330?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI0Njk5OTU5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"> <br>
解决：在flume配置中添加</p>



<pre class="prettyprint"><code class="language-c hljs ">a1.sinks.k1.hdfs.fileType = DataStream</code></pre>

<p>原因： <br>
hdfs.fileType默认为SequenceFile，会压缩文件 <br>
<img title="" alt="这里写图片描述" src="https://img-blog.csdn.net/20180323113020234?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI0Njk5OTU5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></p>

<p>3、AvroRuntimeException: Excessively large list allocation request detected: 825373449 items! <br>
<img title="" alt="这里写图片描述" src="https://img-blog.csdn.net/20180323165215559?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI0Njk5OTU5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"> <br>
解决：调整flume中java堆栈大小 <br>
原因：Flume内存溢出</p>

<p>4、NoClassDefFoundError: org/apache/hive/hcatalog/streaming/RecordWriter <br>
<img title="" alt="这里写图片描述" src="https://img-blog.csdn.net/20180323165545827?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI0Njk5OTU5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"> <br>
解决： <br>
找到Hive的jar包所在目录 <br>
<img title="" alt="这里写图片描述" src="https://img-blog.csdn.net/20180323170058458?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI0Njk5OTU5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"> <br>
找到Flume的jar包所在目录 <br>
<img title="" alt="这里写图片描述" src="https://img-blog.csdn.net/20180323170046803?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI0Njk5OTU5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></p>



<pre class="prettyprint"><code class="language-sh hljs avrasm"><span class="hljs-keyword">cp</span> /opt/cloudera/parcels/CDH-<span class="hljs-number">5.11</span><span class="hljs-number">.1</span>-<span class="hljs-number">1.</span>cdh5<span class="hljs-number">.11</span><span class="hljs-number">.1</span><span class="hljs-preprocessor">.p</span>0<span class="hljs-number">.4</span>/jars/hive-* /opt/cloudera/parcels/CDH-<span class="hljs-number">5.11</span><span class="hljs-number">.1</span>-<span class="hljs-number">1.</span>cdh5<span class="hljs-number">.11</span><span class="hljs-number">.1</span><span class="hljs-preprocessor">.p</span>0<span class="hljs-number">.4</span>/lib/flume-ng/lib/</code></pre>

<p>原因：flume缺少了hive的jar包，需要从CDH拷贝</p>

<p>5、EventDeliveryException: java.lang.NullPointerException: Expected timestamp in the Flume event headers, but it was null <br>
<img title="" alt="这里写图片描述" src="https://img-blog.csdn.net/20180323171117922?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI0Njk5OTU5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"> <br>
原因：时间戳参数设置错误 <br>
解决： <br>
在flume的conf文件中配置sink端</p>



<pre class="prettyprint"><code class="language-c hljs ">a1.sinks.k1.hive.useLocalTimeStamp=<span class="hljs-keyword">true</span></code></pre>

<p>参考文章： <br>
<a href="https://blog.csdn.net/lifuxiangcaohui/article/details/49949865" rel="nofollow">https://blog.csdn.net/lifuxiangcaohui/article/details/49949865</a> <br>
<a href="https://blog.csdn.net/panguoyuan/article/details/39555239" rel="nofollow">https://blog.csdn.net/panguoyuan/article/details/39555239</a> <br>
<a href="http://miximixi.me/index.php/archives/961" rel="nofollow">http://miximixi.me/index.php/archives/961</a></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>