---
layout:     post
title:      flume整合kafka
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/ggz631047367/article/details/50754333				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-preprocessor"># Please paste flume.conf here. Example:</span>
<span class="hljs-preprocessor"># Sources, channels, and sinks are defined per</span>
<span class="hljs-preprocessor"># agent name, in this case 'tier1'.</span>
tier1<span class="hljs-preprocessor">.sources</span>  = source1
tier1<span class="hljs-preprocessor">.channels</span> = channel1
tier1<span class="hljs-preprocessor">.sinks</span>    = sink1

<span class="hljs-preprocessor"># For each source, channel, and sink, set</span>
<span class="hljs-preprocessor"># standard properties.</span>
tier1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.source</span>1<span class="hljs-preprocessor">.type</span>     = syslogtcp
tier1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.source</span>1<span class="hljs-preprocessor">.bind</span>     = <span class="hljs-number">127.0</span><span class="hljs-number">.0</span><span class="hljs-number">.1</span>
tier1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.source</span>1<span class="hljs-preprocessor">.port</span>     = <span class="hljs-number">9999</span>
tier1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.source</span>1<span class="hljs-preprocessor">.channels</span> = channel1
tier1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.channel</span>1<span class="hljs-preprocessor">.type</span>   = memory

tier1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.sink</span>1<span class="hljs-preprocessor">.channel</span>      = channel1
tier1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.sink</span>1<span class="hljs-preprocessor">.type</span> = org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.sink</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.KafkaSink</span>
tier1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.sink</span>1<span class="hljs-preprocessor">.topic</span> = ggz
tier1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.sink</span>1<span class="hljs-preprocessor">.brokerList</span> = ha1:<span class="hljs-number">9092</span>
tier1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.sink</span>1<span class="hljs-preprocessor">.requiredAcks</span> = <span class="hljs-number">1</span>
tier1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.sink</span>1<span class="hljs-preprocessor">.batchSize</span> = <span class="hljs-number">20</span>


<span class="hljs-preprocessor"># Other properties are specific to each type of</span>
<span class="hljs-preprocessor"># source, channel, or sink. In this case, we</span>
<span class="hljs-preprocessor"># specify the capacity of the memory channel.</span>

tier1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.channel</span>1<span class="hljs-preprocessor">.capacity</span> = <span class="hljs-number">100</span></code></pre>

<p>测试： <br>
生产者：nc ha1 9999 <br>
消费者：kafka-console-consumer  –zookeeper ha0 –topic ggz –from-beginning</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>