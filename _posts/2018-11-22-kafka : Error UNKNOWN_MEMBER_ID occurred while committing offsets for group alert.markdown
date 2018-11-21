---
layout:     post
title:      kafka : Error UNKNOWN_MEMBER_ID occurred while committing offsets for group alert
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/jinzhencs/article/details/71713707				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<hr>

<h2 id="2017-05-16最新">2017-05-16最新:</h2>

<p>经过2天的折腾，总算是解决了这个问题。 <br>
1.最开始遇到这个问题，总结为网络原因，可能确实是网络原因，但是需要解决。因此希望调大session.timeout，但是服务端没有设置，导致客户端无法调超过30s,所以没法调高超时时间等。而且感觉这个治标不治本！</p>

<p>2.尝试调低一次拉取的最大值，从而减少一次处理的条数。确实有收益，但是还需要继续！</p>

<p><strong>3.尝试发现处理一条数据过程中耗时最久的一步，优化之。这次找到源头所在，处理逻辑中有一步是HTTP请求，这步有一些耗时25ms(batch commit?),当一次消费3W条数据(fetch size :5M)，这步就非常耗时，并且某些时候可能会有网络原因导致阻塞？所以就导致了整个poll循环出现本文的问题。发现方法:注释掉sendKafka().解决方法:sendKafka()改为异步处理,send()只是put in memory BlockQueue。</strong></p>

<p>这次基本算是解决了问题。另外中途遇到了:faild allocate memory within max.bolck.ms</p>

<p>解决: <br>
batch.size 1024 -&gt; 10240 <br>
max.block.ms 1000不变 <br>
buffer.memory 4096 -&gt; 40960</p>

<p>总结:这个问题是必然报出来的，因为确实不该在poll循环里面做非常耗时的、不稳定的逻辑.一定要注意一切有关网络开销、数据库等的。</p>

<p>附.消费者配置</p>

<pre class="prettyprint"><code class=" hljs avrasm">    &lt;prop key=<span class="hljs-string">"bootstrap.servers"</span>&gt;${kafka<span class="hljs-preprocessor">.event</span><span class="hljs-preprocessor">.host</span>}&lt;/prop&gt;
    &lt;prop key=<span class="hljs-string">"retries"</span>&gt;${metric<span class="hljs-preprocessor">.report</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.retries</span>:<span class="hljs-number">3</span>}&lt;/prop&gt;
    &lt;prop key=<span class="hljs-string">"batch.size"</span>&gt;${metric<span class="hljs-preprocessor">.report</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.batch</span><span class="hljs-preprocessor">.size</span>:<span class="hljs-number">10240</span>}&lt;/prop&gt;
    &lt;prop key=<span class="hljs-string">"max.block.ms"</span>&gt;${metric<span class="hljs-preprocessor">.report</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.max</span><span class="hljs-preprocessor">.block</span><span class="hljs-preprocessor">.ms</span>:<span class="hljs-number">1000</span>}&lt;/prop&gt;
    &lt;prop key=<span class="hljs-string">"linger.ms"</span>&gt;${metric<span class="hljs-preprocessor">.report</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.linger</span><span class="hljs-preprocessor">.ms</span>:<span class="hljs-number">100</span>}&lt;/prop&gt;
    &lt;prop key=<span class="hljs-string">"buffer.memory"</span>&gt;${metric<span class="hljs-preprocessor">.report</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.buffer</span><span class="hljs-preprocessor">.memory</span>:<span class="hljs-number">40960</span>}&lt;/prop&gt;
    &lt;prop key=<span class="hljs-string">"key.serializer"</span>&gt;org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.common</span><span class="hljs-preprocessor">.serialization</span><span class="hljs-preprocessor">.StringSerializer</span>&lt;/prop&gt;
    &lt;prop key=<span class="hljs-string">"value.serializer"</span>&gt;org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.common</span><span class="hljs-preprocessor">.serialization</span><span class="hljs-preprocessor">.StringSerializer</span>&lt;/prop&gt;</code></pre>

<hr>

<p>本地跑消费kafka数据,发现ERROR,网上找到这个哥们的: <br>
<a href="http://stackoverflow.com/questions/38394662/error-unknown-member-id-occurred-while-committing-offsets-for-group-xxx" rel="nofollow" target="_blank">http://stackoverflow.com/questions/38394662/error-unknown-member-id-occurred-while-committing-offsets-for-group-xxx</a></p>

<p><a href="https://cwiki.apache.org/confluence/display/KAFKA/KIP-41%3A+KafkaConsumer+Max+Records" rel="nofollow" target="_blank">https://cwiki.apache.org/confluence/display/KAFKA/KIP-41%3A+KafkaConsumer+Max+Records</a></p>

<p>大概意思就是说消费不过来，数据太大。自动commit超时了。 <br>
解决:</p>

<ol>
<li><strong>max.partition.fetch.bytes</strong>:一次拉取最大byte这个属性 小一点.默认1M</li>
<li><strong>session.timeout.ms</strong>:超时时间设置大一点</li>
</ol>

<hr>

<p>准备改的时候，想了一想，先看看我这程序能处理多少，结果一跑起来才发现，压根不是程序的锅。本地网络波动导致的！因为这个线程没用到redis，但是这个异常每次都和redis timeout一起报， <br>
我设置的 poll(100 ms). max.partition.fetch.bytes没设置,默认1M， <br>
然后打印了消费5000条数据情况下耗时，是1s。所以白担心了一场，不过也学到了一些东西。</p>

<hr>



<h3 id="总结">总结：</h3>

<p>遇到错不一定就是程序的锅。其他环境、引用都有可能导致ERROR！！！</p>

<p>kafka客户端性能优化参考: <br>
<a href="http://www.jianshu.com/p/4e00dff97f39" rel="nofollow" target="_blank">http://www.jianshu.com/p/4e00dff97f39</a></p>

<hr>



<h2 id="2017-05-15">2017-05-15</h2>

<p>今天部署到服务器又报这个异常，</p>



<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-number">2017</span>-<span class="hljs-number">05</span>-<span class="hljs-number">12</span> <span class="hljs-number">11</span>:<span class="hljs-number">17</span>:<span class="hljs-number">07</span>,<span class="hljs-number">553</span> [pool-<span class="hljs-number">1</span>-thread-<span class="hljs-number">1</span>] WARN org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.clients</span><span class="hljs-preprocessor">.consumer</span><span class="hljs-preprocessor">.internals</span><span class="hljs-preprocessor">.ConsumerCoordinator</span>:<span class="hljs-number">445</span> - Auto offset commit failed:                                          
<span class="hljs-number">2017</span>-<span class="hljs-number">05</span>-<span class="hljs-number">12</span> <span class="hljs-number">11</span>:<span class="hljs-number">17</span>:<span class="hljs-number">07</span>,<span class="hljs-number">554</span> [pool-<span class="hljs-number">1</span>-thread-<span class="hljs-number">1</span>] INFO org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.clients</span><span class="hljs-preprocessor">.consumer</span><span class="hljs-preprocessor">.internals</span><span class="hljs-preprocessor">.AbstractCoordinator</span>:<span class="hljs-number">354</span> - Attempt to join group alert failed due to unknown member id, resetti
ng <span class="hljs-keyword">and</span> retrying.                                                                                                                                                                         
<span class="hljs-number">2017</span>-<span class="hljs-number">05</span>-<span class="hljs-number">15</span> <span class="hljs-number">02</span>:<span class="hljs-number">03</span>:<span class="hljs-number">23</span>,<span class="hljs-number">737</span> [pool-<span class="hljs-number">1</span>-thread-<span class="hljs-number">1</span>] INFO org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.clients</span><span class="hljs-preprocessor">.consumer</span><span class="hljs-preprocessor">.internals</span><span class="hljs-preprocessor">.AbstractCoordinator</span>:<span class="hljs-number">623</span> - Attempt to heart beat failed since the group is rebalancing, try to 
re-join group.                                                                                                                                                                           
<span class="hljs-number">2017</span>-<span class="hljs-number">05</span>-<span class="hljs-number">15</span> <span class="hljs-number">02</span>:<span class="hljs-number">03</span>:<span class="hljs-number">49</span>,<span class="hljs-number">150</span> [pool-<span class="hljs-number">1</span>-thread-<span class="hljs-number">1</span>] INFO org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.clients</span><span class="hljs-preprocessor">.consumer</span><span class="hljs-preprocessor">.internals</span><span class="hljs-preprocessor">.AbstractCoordinator</span>:<span class="hljs-number">623</span> - Attempt to heart beat failed since the group is rebalancing, try to 
re-join group.                                                                                                                                                                           
<span class="hljs-number">2017</span>-<span class="hljs-number">05</span>-<span class="hljs-number">15</span> <span class="hljs-number">02</span>:<span class="hljs-number">05</span>:<span class="hljs-number">09</span>,<span class="hljs-number">154</span> [pool-<span class="hljs-number">1</span>-thread-<span class="hljs-number">1</span>] INFO org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.clients</span><span class="hljs-preprocessor">.consumer</span><span class="hljs-preprocessor">.internals</span><span class="hljs-preprocessor">.AbstractCoordinator</span>:<span class="hljs-number">623</span> - Attempt to heart beat failed since the group is rebalancing, try to </code></pre>

<p>看来确实在某些时刻，程序无法消费过来，调高一下心跳时间试试：</p>

<blockquote>
  <p>heartbeat.interval.ms 25000  》 60000</p>
</blockquote>

<p>参考文章: <br>
<a href="http://blog.csdn.net/xianzhen376/article/details/51802736" rel="nofollow" target="_blank">http://blog.csdn.net/xianzhen376/article/details/51802736</a></p>

<p>Kafka源码深度解析－序列7 －Consumer －coordinator协议与heartbeat实现原理 <br>
<a href="http://blog.csdn.net/chunlongyu/article/details/52791874" rel="nofollow" target="_blank">http://blog.csdn.net/chunlongyu/article/details/52791874</a></p>

<p><a href="http://blog.csdn.net/stark_summer/article/details/50203133" rel="nofollow" target="_blank">http://blog.csdn.net/stark_summer/article/details/50203133</a></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>