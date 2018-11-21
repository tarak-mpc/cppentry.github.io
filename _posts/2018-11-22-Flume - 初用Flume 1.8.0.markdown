---
layout:     post
title:      Flume - 初用Flume 1.8.0
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/u011669700/article/details/79993335				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="flume-初用flume">Flume - 初用Flume</h1>

<p>在Flume中，最重要的三个部件分别为：</p>

<ol>
<li>source</li>
<li>channels</li>
<li>sink</li>
</ol>

<p>在本例中我们使用如图的架构来进行Flume数据采集：</p>

<p></p><center><img src="http://static.zybuluo.com/ZzzJoe/6v453g3hha02365men2nrovf/FlumeDemo.png" alt="FlumeDemo.png-7.5kB" title=""></center><p></p>

<p>当前使用的flume版本号为<code>1.8.0</code>，如果相对其他类别的配置有更详细的了解，可查看：<a href="http://flume.apache.org/FlumeUserGuide.html" rel="nofollow">http://flume.apache.org/FlumeUserGuide.html</a></p>

<p>对于flume中的使用主要是对于配置文件的使用，本文所使用的的配置文件名为flume安装目录下的<code>conf/flume-conf.properties</code></p>



<hr>



<h1 id="一总体配置">一、总体配置</h1>



<pre class="prettyprint"><code class=" hljs avrasm">agent<span class="hljs-preprocessor">.sources</span> = dirSrc
agent<span class="hljs-preprocessor">.channels</span> = memoryChannel
agent<span class="hljs-preprocessor">.sinks</span> = kafkaSink</code></pre>

<p>在这里面我们总共做了如下几个事情：</p>

<ol>
<li>定义该flume应用的名称为agent</li>
<li>该应用的sources名称为dirSrc</li>
<li>该应用的channels名称为memoryChannel</li>
<li>该应用的sinks名称为kafkaSink</li>
</ol>



<h1 id="二定义sources">二、定义sources</h1>

<p>我们使用的 sources 类型为<code>Spooling Directory Source</code>。</p>

<p>其作用是：监听一个文件夹，收集文件夹下新文件数据，收集完新文件数据会将文件名称的后缀改为.COMPLETED，缺点是不支持老文件新增数据的收集，并且不能够对嵌套文件夹递归监听。</p>

<p>我们配置文件的内容为：</p>



<pre class="prettyprint"><code class=" hljs avrasm">agent<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.dirSrc</span><span class="hljs-preprocessor">.type</span> = spooldir
agent<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.dirSrc</span><span class="hljs-preprocessor">.spoolDir</span>=/mnt/vdb/bigdata/flume/data
agent<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.dirSrc</span><span class="hljs-preprocessor">.fileHeader</span>=true 
agent<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.dirSrc</span><span class="hljs-preprocessor">.channels</span> = memoryChannel</code></pre>

<p><strong>关键参数说明：</strong></p>

<ol>
<li>type：source类型为spooldir。</li>
<li>spoolDir：source监听的文件夹。</li>
<li>fileHeader：是否添加文件的绝对路径到event的header中，默认值false。</li>
<li>fileHeaderKey：添加到event header中文件绝对路径的键值，默认值file。</li>
<li>selector.type：选择器类型，默认replicating（可选值为replicating或multiplexing）。</li>
<li>fileSuffix：收集完新文件数据给文件添加的后缀名称，默认值：.COMPLETED。</li>
<li>channels：Source对接的Channel名称。</li>
</ol>



<h1 id="三定义channels">三、定义channels</h1>

<p>Memory Channel读写速度快，但是存储数据量小，Flume进程挂掉、服务器停机或者重启都会导致数据丢失。部署Flume Agent的线上服务器内存资源充足、不关心数据丢失的场景下可以使用。</p>

<p>我们配置文件的内容为：</p>



<pre class="prettyprint"><code class=" hljs avrasm">agent<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.memoryChannel</span><span class="hljs-preprocessor">.type</span>=memory
agent<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.memoryChannel</span><span class="hljs-preprocessor">.capacity</span>=<span class="hljs-number">10000</span>
agent<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.memoryChannel</span><span class="hljs-preprocessor">.transactionCapacity</span>=<span class="hljs-number">10000</span></code></pre>

<p><strong>关键参数说明：</strong></p>

<ol>
<li>type：channel类型memory。</li>
<li>capacity：channel中存储的最大event数，默认值100。</li>
<li>transactionCapacity：一次事务中写入和读取的event最大数，默认值100。</li>
<li>keep-alive：在Channel中写入或读取event等待完成的超时时间，默认值3（单位秒）。</li>
<li>byteCapacityBufferPercentage：缓冲空间占Channel容量（byteCapacity）的百分比，为event中的头信息保留了空间，默认值20（单位：百分比）。</li>
<li>byteCapacity：Channel占用内存的最大容量，默认值为Flume堆内存的80%，如果该参数设置为0则强制设置Channel占用内存为200G。</li>
</ol>



<h1 id="四定义sinks">四、定义Sinks</h1>

<p>Kafka是一款开源的分布式消息队列，在消息传递过程中引入Kafka会从很大程度上降低系统之间的耦合度，提高系统稳定性和容错能力。Flume通过Kafka Sink将Event写入到Kafka中的主题，其他应用通过订阅主题消费数据。Flume1.7开始支持Kafka0.9及以上版本。</p>

<p>我们配置文件的内容为：</p>



<pre class="prettyprint"><code class=" hljs avrasm">agent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.kafkaSink</span><span class="hljs-preprocessor">.channel</span>=memoryChannel
agent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.kafkaSink</span><span class="hljs-preprocessor">.type</span>=org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.sink</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.KafkaSink</span>
agent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.kafkaSink</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.topic</span>=sensor_data
agent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.kafkaSink</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.bootstrap</span><span class="hljs-preprocessor">.servers</span>=master:<span class="hljs-number">9092</span>
agent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.kafkaSink</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.flumeBatchSize</span>=<span class="hljs-number">100</span>
agent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.kafkaSink</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.producer</span><span class="hljs-preprocessor">.acks</span>=<span class="hljs-number">1</span>
agent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.kafkaSink</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.producer</span><span class="hljs-preprocessor">.linger</span><span class="hljs-preprocessor">.ms</span>=<span class="hljs-number">10</span>
agent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.kafkaSink</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.producer</span><span class="hljs-preprocessor">.compression</span><span class="hljs-preprocessor">.type</span>=lz4</code></pre>

<p><strong>关键参数说明：</strong></p>

<ol>
<li>type：Sink类型，值为KafkaSink类路径org.apache.flume.sink.kafka.KafkaSink。</li>
<li>kafka.bootstrap.servers：Broker列表，定义格式host:port，多个Broker之间用逗号隔开，可以配置一个也可以配置多个，用于Producer发现集群中的Broker，建议配置多个，防止当前Broker出现问题连接失败。</li>
<li>kafka.topic：Kafka中Topic主题名称，默认值为flume-topic。</li>
<li>flumeBatchSize：Producer端单次批量发送的消息条数，该值应该根据实际环境适当调整，增大批量发送消息的条数能够在一定程度上提高性能，但是同时也增加了延迟和Producer端数据丢失的风险。默认值为100。</li>
<li>kafka.producer.acks：设置Producer端发送消息到Borker是否等待接收Broker返回成功送达信号。0表示Producer发送消息到Broker之后不需要等待Broker返回成功送达的信号，这种方式吞吐量高，但是存在数据丢失的风险，“retries”配置的发送消息失败重试次数将失效。1表示Broker接收到消息成功写入本地log文件后向Producer返回成功接收的信号，不需要等待所有的Follower全部同步完消息后再做回应，这种方式在数据丢失风险和吞吐量之间做了平衡。all（或者-1）表示Broker接收到Producer的消息成功写入本地log并且等待所有的Follower成功写入本地log后向Producer返回成功接收的信号，这种方式能够保证消息不丢失，但是性能最差。默认值为1。</li>
<li>useFlumeEventFormat：默认情况下，Producer只会将Event主体信息以字节形式发送到Kafka Topic中。如果设置为true，Producer发送到Kafka Topic中的Event将能够保留Producer端头信息，以Flume Avro二进制形式存储，结合下游Kafka Source或者Kafka Channel中的parseAsFlumeEvent属性一起使用。默认值为false。</li>
</ol>



<h1 id="五启动命令">五、启动命令</h1>

<p>进入到flume的解压目录下：</p>



<pre class="prettyprint"><code class=" hljs lasso">bin/flume<span class="hljs-attribute">-ng</span> agent <span class="hljs-attribute">-n</span> agent <span class="hljs-attribute">-c</span> conf <span class="hljs-attribute">-f</span> conf/flume<span class="hljs-attribute">-conf</span><span class="hljs-built_in">.</span>properties</code></pre>

<p>运行，之后可以使用 <code>jps</code> 里面查看服务有无启动，服务名称为<code>Application</code>。 <br>
</p><center><img src="http://static.zybuluo.com/ZzzJoe/came8i2bsdyerzb1pxqn5wsh/%E8%BF%9B%E7%A8%8B%E6%9F%A5%E7%9C%8B.png" alt="进程查看.png-2.8kB" title=""></center><p></p>



<h1 id="六运行结果">六、运行结果</h1>

<p>在定义 Source 的时候，我们定义的路径为<code>/mnt/vdb/bigdata/flume/data</code>，在程序运行之前我们可以查看到该目录下的文件： <br>
</p><center><img src="http://static.zybuluo.com/ZzzJoe/sh1sni1y0968yw1ypbu8evvr/%E8%BE%93%E5%85%A5%E6%95%B0%E6%8D%AE.png" alt="输入数据.png-5.7kB" title=""></center><p></p>

<p>在 Flume 程序运行之后，我们可以看到文件发生了改变：</p>

<p></p><center><img src="http://static.zybuluo.com/ZzzJoe/z25i3q7e0xuyxwxhidth3r8g/%E8%BE%93%E5%87%BA%E6%95%B0%E6%8D%AE.png" alt="输出数据.png-27.3kB" title=""></center><p></p>

<p>同时在定义kafkaSink的时候，我们定义的<code>topic</code>为 <code>sensor_data</code>，进入到kafka的安装目录下，我们可以使用如下命令来查看主题列表：</p>



<pre class="prettyprint"><code class=" hljs brainfuck"><span class="hljs-comment">bin/kafka</span><span class="hljs-literal">-</span><span class="hljs-comment">topics</span><span class="hljs-string">.</span><span class="hljs-comment">sh</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">list</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">zookeeper</span> <span class="hljs-comment">localhost:2181/kafka</span></code></pre>

<p></p><center><img src="http://static.zybuluo.com/ZzzJoe/1jrrjcr0ss7y6cob16yftx7t/kafka%E4%B8%BB%E9%A2%98.png" alt="kafka主题.png-1.3kB" title=""></center><p></p>

<p>同时我们可以启动 窗口消费者 去查看主题中是否存有数据：</p>



<pre class="prettyprint"><code class=" hljs brainfuck"><span class="hljs-comment">bin/kafka</span><span class="hljs-literal">-</span><span class="hljs-comment">console</span><span class="hljs-literal">-</span><span class="hljs-comment">consumer</span><span class="hljs-string">.</span><span class="hljs-comment">sh</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">zookeeper</span> <span class="hljs-comment">localhost:2181/kafka</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">topic</span> <span class="hljs-comment">sensor_data</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">from</span><span class="hljs-literal">-</span><span class="hljs-comment">beginning</span></code></pre>

<p></p><center><img src="http://static.zybuluo.com/ZzzJoe/o27htaa4jg64mvv7yx3xcds0/kafka%E4%B8%BB%E9%A2%98%E6%95%B0%E6%8D%AE.png" alt="kafka主题数据.png-1.2kB" title=""></center><p></p>

<p>从图片中我们可以看到，已经消费完所有数据进入阻塞态，等待新数据的接入。</p>



<h1 id="七启动时trouble-shooting">七、启动时Trouble shooting</h1>

<p>当应用启动时没有得到对应的结果时，应该首先查看flume安装目录下的logs文件夹，查看<code>flume.log</code>文件，用于判断应用启动失败的原因：</p>

<p>比如我在启动时遇到了这样的一个异常：</p>



<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-label">org.apache.flume.FlumeException:</span> Unable to create sink: kafkaSink, type: org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.source</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.KafkaSource</span>, class: org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.source</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.KafkaSource</span></code></pre>

<p>这是因为我在定义<code>agent.sinks.kafkaSink.type</code>属性时，把类路径写错了，因此无法正常启动。</p>



<h1 id="八如何关闭flume">八、如何关闭Flume</h1>

<p>使用kill命令</p>



<pre class="prettyprint"><code class=" hljs livecodeserver"><span class="hljs-built_in">kill</span> pid</code></pre>

<h1 id="九flume-启动命令详解">九、Flume - 启动命令详解</h1>

<p><a href="https://blog.csdn.net/qianshangding0708/article/details/48088611" rel="nofollow">https://blog.csdn.net/qianshangding0708/article/details/48088611</a></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>