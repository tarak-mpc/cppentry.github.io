---
layout:     post
title:      SparkStreaming实战
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，出处为 http://blog.csdn.net/silentwolfyh					https://blog.csdn.net/silentwolfyh/article/details/70156432				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2 id="目录">目录：</h2>



<h3 id="一spark-streaming是什么">一、Spark Streaming是什么</h3>



<h3 id="二spark-streaming的a-quick-example">二、Spark Streaming的A Quick Example</h3>



<h3 id="三discretized-streams-dstreams">三、Discretized Streams (DStreams)</h3>



<h3 id="四dstream时间窗口">四、Dstream时间窗口</h3>



<h3 id="五dstream操作">五、Dstream操作</h3>



<h3 id="六spark-streaming优化和特性">六、Spark Streaming优化和特性</h3>



<h2 id="title">——————————————————————————————————————–</h2>



<h3 id="一spark-streaming是什么-1">一、Spark Streaming是什么</h3>

<p>       Spark Streaming is an extension of the core Spark API that enables scalable, high-throughput, fault-tolerant stream processing of live data streams. <strong><font color="#ff0000">Data can be ingested from many sources like Kafka, Flume, Twitter, ZeroMQ, Kinesis, or TCP sockets</font></strong>, and can be processed using complex algorithms expressed with high-level functions like map, reduce, join and window. Finally,  <strong><font color="#ff0000">processed data can be pushed out to filesystems, databases, and live dashboards. </font></strong> In fact, you can apply Spark’s machine learning and graph processing algorithms on data streams. <br>
</p><center><img src="https://img-blog.csdn.net/20170413111701063?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc2lsZW50d29sZnlo/font/5a6L5L2T/fontsize/400/fill/I0ZGMDAwMA==/dissolve/70/gravity/Center" alt="这里写图片描述" title=""></center><p></p>



<h3 id="二spark-streaming的a-quick-example-1">二、Spark Streaming的A Quick Example</h3>

<p><img src="https://img-blog.csdn.net/20170413111736185?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc2lsZW50d29sZnlo/font/5a6L5L2T/fontsize/400/fill/I0ZGMDAwMA==/dissolve/70/gravity/Center" alt="这里写图片描述" title=""> <br>
<img src="https://img-blog.csdn.net/20170413111759892?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc2lsZW50d29sZnlo/font/5a6L5L2T/fontsize/400/fill/I0ZGMDAwMA==/dissolve/70/gravity/Center" alt="这里写图片描述" title=""></p>



<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
   <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.apache.spark<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span> 
   <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>spark-streaming_2.11<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span> 
   <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>2.1.0<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span> 
<span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span> 
</code></pre>



<h3 id="三discretized-streams-dstreams-1">三、Discretized Streams (DStreams)</h3>

<p><strong><font color="#ff0000">提前参考：SparkStreaming在启动执行步鄹和DStream的理解 </font></strong> <br>
<a href="http://blog.csdn.net/silentwolfyh/article/details/70157445" rel="nofollow" target="_blank">http://blog.csdn.net/silentwolfyh/article/details/70157445</a></p>

<p>       Discretized Stream or DStream is the basic abstraction provided by Spark Streaming.A DStream is represented by a continuous series of RDDs <br>
</p><center><img src="https://img-blog.csdn.net/20170413111908874?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc2lsZW50d29sZnlo/font/5a6L5L2T/fontsize/400/fill/I0ZGMDAwMA==/dissolve/70/gravity/Center" alt="这里写图片描述" title=""></center><p></p>

<p><strong><font color="#ff0000">备注：Dstream就是一个基础抽象的管道，每一个Duration就是一个RDD</font></strong></p>



<h3 id="四dstream时间窗口-1">四、Dstream时间窗口</h3>

<p>       Spark Streaming also provides windowed  computations, which allow you to apply transformations over a sliding window of data. The following figure illustrates this sliding window. <br>
</p><center><img src="https://img-blog.csdn.net/20170413112023688?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc2lsZW50d29sZnlo/font/5a6L5L2T/fontsize/400/fill/I0ZGMDAwMA==/dissolve/70/gravity/Center" alt="这里写图片描述" title=""></center><p></p>

<p>       使用Spark Streaming每次只能消费当前批次内的数据，当然可以通过window操作，消费过去一段时间（多个批次）内的数据。举个简例子，需要每隔10秒，统计当前小时的PV和UV，在数据量特别大的情况下，使用window操作并不是很好的选择，通常是借助其它如Redis、HBase等完成数据统计。 <br>
</p><center><img src="https://img-blog.csdn.net/20170413112101065?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc2lsZW50d29sZnlo/font/5a6L5L2T/fontsize/400/fill/I0ZGMDAwMA==/dissolve/70/gravity/Center" alt="这里写图片描述" title=""></center><p></p>

<p><img src="https://img-blog.csdn.net/20170413112123222?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc2lsZW50d29sZnlo/font/5a6L5L2T/fontsize/400/fill/I0ZGMDAwMA==/dissolve/70/gravity/Center" alt="这里写图片描述" title=""></p>

<p><strong><font color="#ff0000">备注：时间窗口的时间一定是每个Duration产生成RDD的倍数</font></strong></p>



<h3 id="五dstream操作-1">五、Dstream操作</h3>

<p></p><center><img src="https://img-blog.csdn.net/20170413112207862?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc2lsZW50d29sZnlo/font/5a6L5L2T/fontsize/400/fill/I0ZGMDAwMA==/dissolve/70/gravity/Center" alt="这里写图片描述" title=""></center><p></p>

<p><strong><font color="#ff0000">转换操作        Transformations on Dstreams</font></strong> <br>
<img src="https://img-blog.csdn.net/20170413112222831?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc2lsZW50d29sZnlo/font/5a6L5L2T/fontsize/400/fill/I0ZGMDAwMA==/dissolve/70/gravity/Center" alt="这里写图片描述" title=""> <br>
<img src="https://img-blog.csdn.net/20170413112241300?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc2lsZW50d29sZnlo/font/5a6L5L2T/fontsize/400/fill/I0ZGMDAwMA==/dissolve/70/gravity/Center" alt="这里写图片描述" title=""></p>

<p><strong><font color="#ff0000">基于窗口的转换操作      Window Operations</font></strong> <br>
 <img src="https://img-blog.csdn.net/20170413112327364?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc2lsZW50d29sZnlo/font/5a6L5L2T/fontsize/400/fill/I0ZGMDAwMA==/dissolve/70/gravity/Center" alt="这里写图片描述" title=""></p>

<p><strong><font color="#ff0000">输出操作        Output Operations on DStreams</font></strong> <br>
<img src="https://img-blog.csdn.net/20170413112345395?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc2lsZW50d29sZnlo/font/5a6L5L2T/fontsize/400/fill/I0ZGMDAwMA==/dissolve/70/gravity/Center" alt="这里写图片描述" title=""></p>



<h3 id="六spark-streaming优化和特性-1">六、Spark Streaming优化和特性</h3>

<p><strong>运行时间优化</strong> <br>
1、增加并行度 <br>
2、减少数据序列化、反序列化的负担 <br>
3、设置合理的批处理间隔 <br>
4、减少因任务提交和分发带来的负担</p>

<p><strong>内存使用优化</strong> <br>
1、控制批处理间隔内的数据量 <br>
2、及时清理不在使用的数据 <br>
3、观察及适当调整GC策略</p>

<p><strong>测试例子路径</strong> <br>
spark1.6.2\spark-1.6.2-bin-hadoop2.6\examples\src\main\scala\org\apache\spark\examples\streaming <br>
<img src="https://img-blog.csdn.net/20170413112447208?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc2lsZW50d29sZnlo/font/5a6L5L2T/fontsize/400/fill/I0ZGMDAwMA==/dissolve/70/gravity/Center" alt="这里写图片描述" title=""></p>

<p>       <font size="5" color="#ff0000" face="黑体">如果您喜欢我写的博文，读后觉得收获很大，不妨小额赞助我一下，让我有动力继续写出高质量的博文，感谢您的赞赏！！！</font> <br>
</p><center><img src="https://img-blog.csdn.net/20170607081951153" width="180" height="180"></center><p></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>