---
layout:     post
title:      平台搭建---Kafka使用---Kafka重复消费和丢失数据
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/qingqing7/article/details/80054281				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p><a href="https://blog.csdn.net/zollty/article/details/53958641" rel="nofollow">来源</a></p>



<h2 id="1kafka保证数据不丢失的原理"><strong>1、Kafka保证数据不丢失的原理</strong></h2>



<h3 id="11kafka消息的位置"><strong>1.1、kafka消息的位置</strong></h3>

<p>用好Kafka，维护其消息偏移量对于避免消息的重复消费与遗漏消费,确保消息的Exactly-once是至关重要的。 <br>
kafka的消息所在的位置Topic、Partitions、Offsets三个因素决定。 <br>
Kafka消费者消费的消息位置还与consumer的group.id有关。 <br>
<strong>consumerOffsets与earlieastLeaderOffsets的关系</strong></p>

<table>
<thead>
<tr>
  <th>名称</th>
  <th>含义</th>
</tr>
</thead>
<tbody><tr>
  <td>earlieastLeaderOffsets</td>
  <td>存储在broker上的leader节点的最早的消息偏移量</td>
</tr>
<tr>
  <td>consumerOffsets</td>
  <td>消费者消费的消息偏移量位置</td>
</tr>
</tbody></table>


<p>为了表述方便，我们记earlieastLeaderOffsets为A，记consumerOffsets为B 。</p>

<ul>
<li>情况一：正常情况下，消费的消息偏移量应该大于broker上存储的最早的消息偏移量，即 A &lt; B：</li>
</ul>

<p><img src="https://img-blog.csdn.net/20170802165736275?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvT2l0ZUJvZHk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""> <br>
我们知道，存储在broker上的kafka的消息常设置消息过期配置，当到达过期时间时过期的消息将会被清除。</p>

<ul>
<li>情况二：如果A 依然小于 B，则仍可以正常消费： </li>
</ul>

<p><img src="https://img-blog.csdn.net/20170802170603704?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvT2l0ZUJvZHk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<ul>
<li>情况三：然而，当 A &gt; B 时，则说明还没有被消费的消息已经被清除</li>
</ul>

<p><img src="https://img-blog.csdn.net/20170802170701615?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvT2l0ZUJvZHk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""> <br>
此种情况会抛出 kafka.common.OffsetOutOfRangeException 异常。 <br>
<strong>consumerOffsets 小于 earlieastLeaderOffsets的影响与解决办法</strong> <br>
当情况三发生时，在（B，A）区间内的消息还没有被消费就已经被清除了，将导致两个后果。 <br>
1. 消息丢失。 <br>
2. 抛出 kafka.common.OffsetOutOfRangeException 异常。</p>

<p>在对消息完整性有严格要求的系统中，消息的丢失造成的影响会比较严重，所以在这种情况下，要保证消息不会遭到丢失。  <br>
<strong>避免消息丢失包含两个方面：</strong></p>

<ul>
<li>还没有被消费过的消息不会被清除。 <br>
在没有外部系统清除kafka消息的情况下，协调设置broker的最大保留大小 log.retention.bytes 和 最大保留时间log.retention.hours 等，来配合消费者端的读取消息。可以通过读取和监控消费者消费的offsets，来保证消息不会被意外清除。</li>
<li>消费者端消费消息没有遗漏。 <br>
当消费者意外中断时，重新启动消费时能够从上一次中断的消息偏移量开始消费。</li>
</ul>



<h3 id="12生产者数据的不丢失"><strong>1.2、生产者数据的不丢失</strong></h3>

<p>kafka的ack机制：在kafka发送数据的时候，每次发送消息都会有一个确认反馈机制，确保消息正常的能够被收到。</p>

<ul>
<li>如果是同步模式：ack机制能够保证数据的不丢失，如果ack设置为0，风险很大，一般不建议设置为0</li>
</ul>



<pre class="prettyprint"><code class=" hljs avrasm">producer<span class="hljs-preprocessor">.type</span>=sync 
request<span class="hljs-preprocessor">.required</span><span class="hljs-preprocessor">.acks</span>=<span class="hljs-number">1</span></code></pre>

<ul>
<li>如果是异步模式：通过buffer来进行控制数据的发送，有两个值来进行控制，时间阈值与消息的数量阈值，如果buffer满了数据还没有发送出去，如果设置的是立即清理模式，风险很大，一定要设置为阻塞模式 <br>
结论：producer有丢数据的可能，但是可以通过配置保证消息的不丢失</li>
</ul>



<pre class="prettyprint"><code class=" hljs avrasm">producer<span class="hljs-preprocessor">.type</span>=async 
request<span class="hljs-preprocessor">.required</span><span class="hljs-preprocessor">.acks</span>=<span class="hljs-number">1</span> 
queue<span class="hljs-preprocessor">.buffering</span><span class="hljs-preprocessor">.max</span><span class="hljs-preprocessor">.ms</span>=<span class="hljs-number">5000</span> 
queue<span class="hljs-preprocessor">.buffering</span><span class="hljs-preprocessor">.max</span><span class="hljs-preprocessor">.messages</span>=<span class="hljs-number">10000</span> 
queue<span class="hljs-preprocessor">.enqueue</span><span class="hljs-preprocessor">.timeout</span><span class="hljs-preprocessor">.ms</span> = -<span class="hljs-number">1</span> 
batch<span class="hljs-preprocessor">.num</span><span class="hljs-preprocessor">.messages</span>=<span class="hljs-number">200</span></code></pre>



<h3 id="13消费者数据的不丢失"><strong>1.3、消费者数据的不丢失</strong></h3>

<p>通过offset commit 来保证数据的不丢失，kafka自己记录了每次消费的offset数值，下次继续消费的时候，接着上次的offset进行消费即可。</p>



<h3 id="14kafka-的offset的重置"><strong>1.4、kafka 的offset的重置</strong></h3>

<p><a href="https://blog.csdn.net/yxgxy270187133/article/details/53666760" rel="nofollow">来源：</a> <br>
spark读取kafka消息时，每次读取都会从kafka最新的offset读取。但是如果数据丢失，如果在使用Kafka来分发消息，在数据处理的过程中可能会出现处理程序出异常或者是其它的错误，会造成数据丢失或不一致。这个时候你也许会想要通过kafka把数据从新处理一遍，或者指定kafka的offset读取。kafka默认会在磁盘上保存到7天的数据，你只需要把kafka的某个topic的consumer的offset设置为某个值或者是最小值，就可以使该consumer从你设置的那个点开始消费。这就需要从zk里面修改offset的值。</p>



<h4 id="141查询topic的offset的范围"><strong>1.4.1、查询topic的offset的范围</strong></h4>

<p>用下面命令可以查询到topic:DynamicRange broker:SparkMaster:9092的offset的最小值：</p>



<pre class="prettyprint"><code class=" hljs lasso">bin/kafka<span class="hljs-attribute">-run</span><span class="hljs-attribute">-class</span><span class="hljs-built_in">.</span>sh kafka<span class="hljs-built_in">.</span>tools<span class="hljs-built_in">.</span>GetOffsetShell <span class="hljs-subst">--</span>broker<span class="hljs-attribute">-list</span> slave6:<span class="hljs-number">9092</span> <span class="hljs-attribute">-topic</span> videoplay <span class="hljs-subst">--</span>time <span class="hljs-subst">-</span><span class="hljs-number">2</span></code></pre>

<p>输出</p>



<pre class="prettyprint"><code class=" hljs css"><span class="hljs-tag">DynamicRange</span><span class="hljs-pseudo">:0</span><span class="hljs-pseudo">:1288</span></code></pre>

<p>查询offset的最大值：</p>



<pre class="prettyprint"><code class=" hljs lasso">bin/kafka<span class="hljs-attribute">-run</span><span class="hljs-attribute">-class</span><span class="hljs-built_in">.</span>sh kafka<span class="hljs-built_in">.</span>tools<span class="hljs-built_in">.</span>GetOffsetShell <span class="hljs-subst">--</span>broker<span class="hljs-attribute">-list</span> slave6:<span class="hljs-number">9092</span> <span class="hljs-attribute">-topic</span> videoplay <span class="hljs-subst">--</span>time <span class="hljs-subst">-</span><span class="hljs-number">1</span></code></pre>

<p>输出</p>



<pre class="prettyprint"><code class=" hljs css"><span class="hljs-tag">DynamicRange</span><span class="hljs-pseudo">:0</span><span class="hljs-pseudo">:7885</span></code></pre>

<p>从上面的输出可以看出topic:DynamicRange只有一个partition:0 offset范围为:[1288,7885]</p>



<h4 id="142设置consumer-group的offset"><strong>1.4.2、设置consumer group的offset</strong></h4>

<p><strong>启动zookeeper client</strong></p>



<pre class="prettyprint"><code class=" hljs avrasm">/zookeeper/bin/zkCli<span class="hljs-preprocessor">.sh</span></code></pre>

<p>通过下面命令<strong>设置consumer group:DynamicRangeGroup topic:DynamicRange partition:0的offset</strong>为1288:</p>



<pre class="prettyprint"><code class=" hljs bash"><span class="hljs-keyword">set</span> /consumers/DynamicRangeGroup/offsets/DynamicRange/<span class="hljs-number">0</span> <span class="hljs-number">1288</span></code></pre>

<p>注意如果你的kafka设置了zookeeper root，比如为/kafka，那么命令应该改为：</p>



<pre class="prettyprint"><code class=" hljs bash"><span class="hljs-keyword">set</span> /kafka/consumers/DynamicRangeGroup/offsets/DynamicRange/<span class="hljs-number">0</span> <span class="hljs-number">1288</span></code></pre>

<p>生效 </p>

<p><strong>重启相关的应用程序</strong>，就可以从设置的offset开始读数据了。 </p>

<p>参考：<a href="https://metabroadcast.com/blog/resetting-kafka-offsets" rel="nofollow">https://metabroadcast.com/blog/resetting-kafka-offsets</a></p>



<h4 id="143手动更新kafka存在zookeeper中的偏移量"><strong>1.4.3、手动更新Kafka存在Zookeeper中的偏移量</strong></h4>

<p>我们有时候需要手动将某个主题的偏移量设置成某个值，这时候我们就需要更新Zookeeper中的数据了。Kafka内置为我们提供了修改偏移量的类：kafka.tools.UpdateOffsetsInZK，我们可以通过它修改Zookeeper中某个主题的偏移量，具体操作如下：</p>



<pre class="prettyprint"><code class=" hljs avrasm">bin/kafka-run-class<span class="hljs-preprocessor">.sh</span> kafka<span class="hljs-preprocessor">.tools</span><span class="hljs-preprocessor">.UpdateOffsetsInZK</span>
<span class="hljs-label">USAGE:</span> kafka<span class="hljs-preprocessor">.tools</span><span class="hljs-preprocessor">.UpdateOffsetsInZK</span>$ [earliest | latest] consumer<span class="hljs-preprocessor">.properties</span> topic</code></pre>

<p>在不输入参数的情况下，我们可以得知kafka.tools.UpdateOffsetsInZK类需要输入的参数。我们的consumer.properties文件配置内容如下：</p>



<pre class="prettyprint"><code class=" hljs avrasm">zookeeper<span class="hljs-preprocessor">.connect</span>=www<span class="hljs-preprocessor">.iteblog</span><span class="hljs-preprocessor">.com</span>:<span class="hljs-number">2181</span>

<span class="hljs-preprocessor"># timeout in ms for connecting to zookeeper</span>
zookeeper<span class="hljs-preprocessor">.connection</span><span class="hljs-preprocessor">.timeout</span><span class="hljs-preprocessor">.ms</span>=<span class="hljs-number">6000</span>

<span class="hljs-preprocessor">#consumer group id</span>
group<span class="hljs-preprocessor">.id</span>=group</code></pre>

<p>这个工具<strong>只能把Zookeeper中偏移量设置成earliest或者latest</strong>，如下：</p>



<pre class="prettyprint"><code class=" hljs avrasm">bin/kafka-run-class<span class="hljs-preprocessor">.sh</span> kafka<span class="hljs-preprocessor">.tools</span><span class="hljs-preprocessor">.UpdateOffsetsInZK</span>  earliest config/consumer<span class="hljs-preprocessor">.properties</span> iteblog</code></pre>



<pre class="prettyprint"><code class=" hljs livecodeserver">updating partition <span class="hljs-number">0</span> <span class="hljs-operator">with</span> <span class="hljs-built_in">new</span> <span class="hljs-built_in">offset</span>: <span class="hljs-number">276022922</span>
updating partition <span class="hljs-number">1</span> <span class="hljs-operator">with</span> <span class="hljs-built_in">new</span> <span class="hljs-built_in">offset</span>: <span class="hljs-number">234360148</span>
updating partition <span class="hljs-number">2</span> <span class="hljs-operator">with</span> <span class="hljs-built_in">new</span> <span class="hljs-built_in">offset</span>: <span class="hljs-number">157237157</span>
updating partition <span class="hljs-number">3</span> <span class="hljs-operator">with</span> <span class="hljs-built_in">new</span> <span class="hljs-built_in">offset</span>: <span class="hljs-number">106968019</span>
updating partition <span class="hljs-number">4</span> <span class="hljs-operator">with</span> <span class="hljs-built_in">new</span> <span class="hljs-built_in">offset</span>: <span class="hljs-number">80696130</span>
updating partition <span class="hljs-number">5</span> <span class="hljs-operator">with</span> <span class="hljs-built_in">new</span> <span class="hljs-built_in">offset</span>: <span class="hljs-number">317144986</span>
updating partition <span class="hljs-number">6</span> <span class="hljs-operator">with</span> <span class="hljs-built_in">new</span> <span class="hljs-built_in">offset</span>: <span class="hljs-number">299182459</span>
updating partition <span class="hljs-number">7</span> <span class="hljs-operator">with</span> <span class="hljs-built_in">new</span> <span class="hljs-built_in">offset</span>: <span class="hljs-number">197012246</span>
updating partition <span class="hljs-number">8</span> <span class="hljs-operator">with</span> <span class="hljs-built_in">new</span> <span class="hljs-built_in">offset</span>: <span class="hljs-number">230433681</span>
updating partition <span class="hljs-number">9</span> <span class="hljs-operator">with</span> <span class="hljs-built_in">new</span> <span class="hljs-built_in">offset</span>: <span class="hljs-number">120971431</span>
updating partition <span class="hljs-number">10</span> <span class="hljs-operator">with</span> <span class="hljs-built_in">new</span> <span class="hljs-built_in">offset</span>: <span class="hljs-number">51200673</span>
updated <span class="hljs-operator">the</span> <span class="hljs-built_in">offset</span> <span class="hljs-keyword">for</span> <span class="hljs-number">11</span> partitions</code></pre>



<h3 id="15kafka-consumer消费者-offset-groupid详解"><strong>1.5、kafka consumer消费者 offset groupID详解</strong></h3>

<p><a href="https://blog.csdn.net/suphieliang/article/details/80421577" rel="nofollow">来源：</a> <br>
kafka consumer：消费者可以从多个broker中读取数据。消费者可以消费多个topic中的数据。 <br>
因为Kafka的broker是无状态的，所以consumer必须使用partition offset来记录消费了多少数据。如果一个consumer指定了一个topic的offset，意味着该consumer已经消费了该offset之前的所有数据。consumer可以通过指定offset，从topic的指定位置开始消费数据。consumer的offset存储在Zookeeper中。 <br>
    offset：用来保存消费进度。offset表示在当前topic，当前groupID下消费到的位置。offset为earliest并不代表offset=1.在不进行过期配置的情况下，kafka消息默认7天时间就会过期。过期后其offset也就随之发生变化，使得用数字进行配置的消费进度并不准确。 <br>
1） earliest：自动重置到最早的offset。 <br>
2） latest：看上去重置到最晚的offset。 <br>
3） none：如果边更早的offset也没有的话，就抛出异常给consumer，告诉consumer在整个consumer group中都没有发现有这样的offset。 <br>
<strong>groupID:</strong> <br>
一个字符串用来指示一组consumer所在的组。相同的groupID表示在一个组里。相同的groupID消费记录offset时，记录的是同一个offset。所以，此处需要注意，（1）如果多个地方都使用相同的groupid，可能造成个别消费者消费不到的情况（2）如果单个消费者消费能力不足的话，可以启动多个相同groupid的consumer消费，处理相同的逻辑。但是，多线程的时候，需要增加每个groupid下的partition分区数量，便于每个线程稳定读取固定的partition，提高消费能力。</p>



<h2 id="2kafka重复消费原因"><strong>2、Kafka重复消费原因</strong></h2>

<p>底层根本原因：<strong>已经消费了数据，但是offset没提交</strong>。 <br>
<strong>原因1：</strong>强行kill线程，导致消费后的数据，offset没有提交。 <br>
<strong>原因2：</strong>设置offset为自动提交，关闭kafka时，如果在close之前，调用 consumer.unsubscribe() 则有可能部分offset没提交，下次重启会重复消费。例如：</p>



<pre class="prettyprint"><code class=" hljs matlab"><span class="hljs-keyword">try</span> <span class="hljs-cell">{
    consumer.unsubscribe();
}</span> <span class="hljs-keyword">catch</span> (Exception e) <span class="hljs-cell">{
}</span>

<span class="hljs-keyword">try</span> <span class="hljs-cell">{
    consumer.close();
}</span> <span class="hljs-keyword">catch</span> (Exception e) <span class="hljs-cell">{
}</span></code></pre>

<p>上面代码会导致部分offset没提交，下次启动时会重复消费。 <br>
<strong>原因3（重复消费最常见的原因）：</strong>消费后的数据，当offset还没有提交时，partition就断开连接。比如，通常会遇到消费的数据，处理很耗时，导致超过了Kafka的session timeout时间（0.10.x版本默认是30秒），那么就会re-blance重平衡，此时有一定几率offset没提交，会导致重平衡后重复消费。 <br>
<strong>原因4：</strong>当消费者重新分配partition的时候，可能出现从头开始消费的情况，导致重发问题。 <br>
<strong>原因5：</strong>当消费者消费的速度很慢的时候，可能在一个session周期内还未完成，导致心跳机制检测报告出问题。</p>



<h2 id="3kafka-consumer丢失数据原因"><strong>3、Kafka Consumer丢失数据原因</strong></h2>

<p>猜测：设置offset为自动定时提交，当offset被自动定时提交时，数据还在内存中未处理，此时刚好把线程kill掉，那么offset已经提交，但是数据未处理，导致这部分内存中的数据丢失。 <br>
消息推动服务，每天早上，手机上各终端都会给用户推送消息，这时候流量剧增，可能会出现kafka发送数据过快，导致服务器网卡爆满，或者磁盘处于繁忙状态，可能会出现丢包现象。 <br>
总结起来就是来得快，去得慢，然后offset又提交了。 <br>
<strong>解决方案：</strong>首先对kafka进行限速，其次启用重试机制，重试间隔时间设置长一些，最后Kafka设置acks=all。 <br>
检测方法：使用重试机制，查看问题所在。 <br>
kafka配置如下： </p>



<pre class="prettyprint"><code class=" hljs avrasm">props<span class="hljs-preprocessor">.put</span>(<span class="hljs-string">"compression.type"</span>, <span class="hljs-string">"gzip"</span>)<span class="hljs-comment">;</span>
props<span class="hljs-preprocessor">.put</span>(<span class="hljs-string">"linger.ms"</span>, <span class="hljs-string">"50"</span>)<span class="hljs-comment">;</span>
props<span class="hljs-preprocessor">.put</span>(<span class="hljs-string">"acks"</span>, <span class="hljs-string">"all"</span>)<span class="hljs-comment">;</span>
props<span class="hljs-preprocessor">.put</span>(<span class="hljs-string">"retries "</span>, <span class="hljs-number">30</span>)<span class="hljs-comment">;</span>
props<span class="hljs-preprocessor">.put</span>(<span class="hljs-string">"reconnect.backoff.ms "</span>, <span class="hljs-number">20000</span>)<span class="hljs-comment">;</span>
props<span class="hljs-preprocessor">.put</span>(<span class="hljs-string">"retry.backoff.ms"</span>, <span class="hljs-number">20000</span>)<span class="hljs-comment">;</span></code></pre>



<h2 id="4解决方案"><strong>4、解决方案</strong></h2>



<h3 id="41维护offset"><strong>4.1、维护offset</strong></h3>

<p>记录offset和恢复offset的方案。理论上记录offset，下一个group consumer可以接着记录的offset位置继续消费。 <br>
<strong>简单offset记录方案：</strong> <br>
 每次消费时更新每个topic+partition位置的offset在内存中，</p>



<pre class="prettyprint"><code class=" hljs livecodeserver">Map&lt;key, <span class="hljs-built_in">value</span>&gt;，key=topic+<span class="hljs-string">'-'</span>+partition，<span class="hljs-built_in">value</span>=<span class="hljs-built_in">offset</span></code></pre>

<p>当调用关闭consumer线程时，把上面Map的offset数据记录到 文件中*（分布式集群可能要记录到redis中）。 <br>
下一次启动consumer，需要读取上一次的offset信息，方法是 以当前的topic+partition为key，从上次的Map中去寻找offset。 <br>
然后使用consumer.seek()方法指定到上次的offset位置。 <br>
 <strong>说明：</strong> <br>
1、该方案针对单台服务器比较简单，直接把offset记录到本地文件中即可，但是对于多台服务器集群，offset也要记录到同一个地方，并且需要做去重处理。 <br>
如果线上程序是由多台服务器组成的集群，是否可以用一台服务器来支撑？应该可以，只是消费慢一点，没多大影响。 <br>
2、如何保证接着offset消费的数据正确性 <br>
为了确保consumer消费的数据一定是接着上一次consumer消费的数据，consumer消费时，记录第一次取出的数据，将其offset和上次consumer最后消费的offset进行对比，如果相同则继续消费。如果不同，则停止消费，检查原因。</p>



<h3 id="42数据缓存维护offset"><strong>4.2、数据缓存+维护offset</strong></h3>

<p>对于离线接入，设置较大的缓存；对于近实时接入，设置较小缓存。 <br>
缓存好的数据写入hive中，就相当于改文件名字，很快（可以看成不会出错）。 <br>
对于同时处理离线和实时的数据接入任务，可以“记录实时接入已读取偏移量”、“实时接入已提交偏移量”、“实时接入消息队列输出偏移量”； <br>
已读取偏移量肯定是大于已提交偏移量和消息队列输出偏移量，当数据写入出错，那么缓存的数据就会丢失，没有写入到hive,这时根据记录实时接入已提交偏移量和实时接入消息队列输出偏移量重新从kafka拉取数据。这样主要是保证两条线路的数据一致性。</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>