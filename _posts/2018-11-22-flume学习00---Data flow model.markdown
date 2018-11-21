---
layout:     post
title:      flume学习00---Data flow model
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/wuxintdrh/article/details/63249959				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h3 id="一flume的数据流模型">一·、flume的数据流模型</h3>

<p>Flume Event被定义为具有有字节的字节载荷和可选的字符串属性结合的<font size="4" color="red"><strong>数据流单元</strong></font>，flume agent 是承载事件从外部源流向下一个目的地的组件的JVM进程。</p>



<h3 id="下图为flume-agent流程图">下图为Flume agent流程图</h3>

<p><img src="https://img-blog.csdn.net/20170318084133367?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3V4aW50ZHJo/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""> <br>
<font size="4"> <br>
<strong>Flume  source</strong> 消费由外部源（如Web Server）传递给他的事件。 <br>
外部源以一种格式化的Event发送给Flume,能够被目标 Flume source识别。 <br>
如：</font></p>

<ul>
<li>Avro source :用于从Avro客户端，或者其他Avro sink中发送的Arvo Event事件</li>
<li>类似的流可以使用Thrift Flume Source 来定义，用于接收来自Thrift Sink或者Thrift RPC 客户端，或Thrift客户端，这些客户端基于Thrift协议 使用任何语言编写的。</li>
</ul>

<p>当Flume Source 接收到event时，它会将其存到一个或多个channels中。 <br>
<strong>Flume Channel</strong> 是一个被动存储。保存event直到被Flume Sink消费， <br>
如File channel:</p>

<ul>
<li>file channels 由本地文件系统支持， sink 从channel中移除事件，将其写道外部的存储中，<strong>或转发个下一个Flume agent中的source,</strong></li>
</ul>

<p>The source and sink within the given agent run asynchronously with the events staged in the channel.</p>

<p></p>



<h3 id="复杂的数据流">复杂的数据流</h3>



<pre class="prettyprint"><code class=" hljs applescript">Flume allows a user <span class="hljs-keyword">to</span> build multi-hop flows <span class="hljs-keyword">where</span> events travel <span class="hljs-keyword">through</span> multiple agents <span class="hljs-keyword">before</span> reaching <span class="hljs-keyword">the</span> final destination. It also allows fan-<span class="hljs-keyword">in</span> <span class="hljs-keyword">and</span> fan-out flows, contextual routing <span class="hljs-keyword">and</span> backup routes (fail-<span class="hljs-keyword">over</span>) <span class="hljs-keyword">for</span> failed hops.
</code></pre>



<h3 id="二可靠性reliability">二、可靠性（Reliability）</h3>

<p>event在每个agent的channel中存储。 然后将事件传递到流中的下一个agent或最终的存储库（如HDFS）。 event只有被存储在下一agent的channel中或最终存储库中之后，才会从channel中移除这些event。 这是Flume中的单跳消息传递语义如何提供流的<strong>端到端可靠性</strong>。 <br>
Flume使用事务方法（transactional approach）来保证event的可靠传递。 source和sink在事务中封装分别存储/检索由信道提供的事务放置或提供的event。 这确保event集合可靠地在流中从点到点传递。 在多跳流的情况下，来自前一跳的sink和来自下一跳的source都使它们的事务运行，以确保数据安全地存储在下一跳的信道中。</p>



<h3 id="三可恢复性recoverability">三、可恢复性（Recoverability）</h3>

<p>event存储载channel中，管理从故障中恢复。 Flume支持由本地文件系统支持的持久file channel。 还有一个memory channel，它简单地将event存储在内存队列中，这更快，但是当agent process 死亡时，仍然留在memory channel中的任何event都无法恢复。</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>