---
layout:     post
title:      Flume 源码学习（二）Channel组件介绍
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="flume-源码学习二channel组件介绍">Flume 源码学习（二）Channel组件介绍</h1>

<hr>

<p>Channel是Flume中第二个组件，是日志从source传输到sink的通道。根据Flume文档，channel有两个重要衡量指标：</p>

<blockquote>
  <ul>
  <li>Reliability：可依赖性。即channel需要接收到的event被下个agent接收或被最终的sink接收。Flume使用事务来保证Reliability。支持可以持久化的基于文件系统的FileChannel和不能保证持久化的MemoryChannel</li>
  <li>Recoverability：可恢复性。如果发生Failure，是否能够恢复数据；Flume支持可恢复的FileChannel和不支持恢复但传输速度快得MemoryChannel</li>
  </ul>
</blockquote>

<hr>

<h2 id="类图">类图</h2>

<p>首先还是来看Channel相关类的类图： <br>
<img src="https://img-blog.csdn.net/20160310181809867" alt="这里写图片描述" title=""> <br>
两个望文生义的类的说明：</p>

<table>
<thead>
<tr>
  <th>接口名</th>
  <th align="center">作用</th>
</tr>
</thead>
<tbody><tr>
  <td>BasicChannelSemantics</td>
  <td align="center">基本的channel语义的实现，包含了Threadlocal保存的Transaction实例。通俗的讲就是采用ThreadLocal来保存和当前Channel关联的Transaction的抽象父类</td>
</tr>
<tr>
  <td>SpillableMemoryChannel</td>
  <td align="center">集成了FileChannel和MemoryChannel功能，首先用MemoryChannel,在这个channel达到capacity再改用FileChannel继续</td>
</tr>
</tbody></table>




<h3 id="memorychannel">MemoryChannel</h3>

<p>我们首先来看MemoryChannel的实现，MemoryChannel有几个重要参数可以配置：</p>

<ul>
<li>capacity：Channel的最大容积</li>
<li>transactionCapacity：一个事务最多可以包含多少个events</li>
<li>byteCapacity：Channel的Queue的最大的byte 容积</li>
<li>byteCapacityBufferPercent：定义了byteCapacity和评估的event size之间缓冲的百分比，概要的说就是如果MemoryChannel中等待写出的EventList的byte size最多只能达到(1-byteCapacityBufferPercent/100)*100%，剩下的是缓冲buffer（如果没有buffer，很可能出现内存溢出）</li>
<li>keep-alive：定义了允许在队列中等待的最大秒数</li>
</ul>

<p>根据上面的类图，我们知道MemoryChannel继承了BasicChannelSemantics抽象类，而后者 <br>
有一个抽象方法createTransaction()需要子类自己实现。所以，MemoryChannel中第一个重要的功能就是基于内存的Transaction的实现。MemoryChannel中是MemoryTransaction这一个内部类，代码如下：</p>



<pre class="prettyprint"><code class="language-java hljs "><span class="hljs-keyword">private</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">MemoryTransaction</span> <span class="hljs-keyword">extends</span> <span class="hljs-title">BasicTransactionSemantics</span> {</span>
    <span class="hljs-keyword">private</span> LinkedBlockingDeque&lt;Event&gt; takeList;<span class="hljs-comment">//从channel中take，准备推送到sink</span>
    <span class="hljs-keyword">private</span> LinkedBlockingDeque&lt;Event&gt; putList;<span class="hljs-comment">//从source put到channel的队列</span>
    <span class="hljs-keyword">private</span> <span class="hljs-keyword">final</span> ChannelCounter channelCounter;<span class="hljs-comment">//counter</span>
    <span class="hljs-keyword">private</span> <span class="hljs-keyword">int</span> putByteCounter = <span class="hljs-number">0</span>;<span class="hljs-comment">//put的字节counter</span>
    <span class="hljs-keyword">private</span> <span class="hljs-keyword">int</span> takeByteCounter = <span class="hljs-number">0</span>;<span class="hljs-comment">//take的字节counter</span>

    <span class="hljs-keyword">public</span> <span class="hljs-title">MemoryTransaction</span>(<span class="hljs-keyword">int</span> transCapacity, ChannelCounter counter) {
      putList = <span class="hljs-keyword">new</span> LinkedBlockingDeque&lt;Event&gt;(transCapacity);
      takeList = <span class="hljs-keyword">new</span> LinkedBlockingDeque&lt;Event&gt;(transCapacity);

      channelCounter = counter;
    }
    <span class="hljs-comment">//往putList增加Event，如果没有空间抛错</span>
    <span class="hljs-annotation">@Override</span>
    <span class="hljs-keyword">protected</span> <span class="hljs-keyword">void</span> <span class="hljs-title">doPut</span>(Event event) <span class="hljs-keyword">throws</span> InterruptedException {
      channelCounter.incrementEventPutAttemptCount();
      <span class="hljs-keyword">int</span> eventByteSize = (<span class="hljs-keyword">int</span>)Math.ceil(estimateEventSize(event)/byteCapacitySlotSize);

      <span class="hljs-keyword">if</span> (!putList.offer(event)) {
        <span class="hljs-keyword">throw</span> <span class="hljs-keyword">new</span> ChannelException(
          <span class="hljs-string">"Put queue for MemoryTransaction of capacity "</span> +
            putList.size() + <span class="hljs-string">" full, consider committing more frequently, "</span> +
            <span class="hljs-string">"increasing capacity or increasing thread count"</span>);
      }
      putByteCounter += eventByteSize;
    }
    <span class="hljs-comment">//从外部类MemoryChannel的等待Take的Event List中take一个过来放到事务的takeList里面，作为本次事务需要提交的Event</span>
    <span class="hljs-annotation">@Override</span>
    <span class="hljs-keyword">protected</span> Event <span class="hljs-title">doTake</span>() <span class="hljs-keyword">throws</span> InterruptedException {
      channelCounter.incrementEventTakeAttemptCount();
      <span class="hljs-keyword">if</span>(takeList.remainingCapacity() == <span class="hljs-number">0</span>) {<span class="hljs-comment">//首先判断takeList是否还有空间</span>
        <span class="hljs-keyword">throw</span> <span class="hljs-keyword">new</span> ChannelException(<span class="hljs-string">"Take list for MemoryTransaction, capacity "</span> +
            takeList.size() + <span class="hljs-string">" full, consider committing more frequently, "</span> +
            <span class="hljs-string">"increasing capacity, or increasing thread count"</span>);
      }
      <span class="hljs-keyword">if</span>(!queueStored.tryAcquire(keepAlive, TimeUnit.SECONDS)) {<span class="hljs-comment">//通过queueStored这Semaphore信号来判断外部类MemoryChannel是否有queued Event等待被take，用信号量而不是直接判断queueStored（LinkedBlockingDeque&lt;Event&gt;）的poll()主要是后者会阻塞queueStored上的所有操作，尤其是包括为空得情况下put操作。可以提高throughput。</span>
        <span class="hljs-keyword">return</span> <span class="hljs-keyword">null</span>;<span class="hljs-comment">//没有获取到，意味着可能queue当前为空。</span>
      }
      Event event;
      <span class="hljs-keyword">synchronized</span>(queueLock) {
        event = queue.poll();<span class="hljs-comment">//queueStored不为空。</span>
      }
      Preconditions.checkNotNull(event, <span class="hljs-string">"Queue.poll returned NULL despite semaphore "</span> +
          <span class="hljs-string">"signalling existence of entry"</span>);
      takeList.put(event);
      <span class="hljs-comment">//计算taken的byte</span>
      <span class="hljs-keyword">int</span> eventByteSize = (<span class="hljs-keyword">int</span>)Math.ceil(estimateEventSize(event)/byteCapacitySlotSize);
      takeByteCounter += eventByteSize;

      <span class="hljs-keyword">return</span> event;
    }
    <span class="hljs-comment">//实际事务的提交</span>
    <span class="hljs-annotation">@Override</span>
    <span class="hljs-keyword">protected</span> <span class="hljs-keyword">void</span> <span class="hljs-title">doCommit</span>() <span class="hljs-keyword">throws</span> InterruptedException {
      <span class="hljs-keyword">int</span> remainingChange = takeList.size() - putList.size();
      <span class="hljs-keyword">if</span>(remainingChange &lt; <span class="hljs-number">0</span>) {<span class="hljs-comment">//take size &lt; putsize，sink的消费速度慢于source的产生速度</span>
        <span class="hljs-keyword">if</span>(!bytesRemaining.tryAcquire(putByteCounter, keepAlive,
          TimeUnit.SECONDS)) {<span class="hljs-comment">//判断是否有足够空间接收putList中events所占的空间</span>
          <span class="hljs-keyword">throw</span> <span class="hljs-keyword">new</span> ChannelException(<span class="hljs-string">"Cannot commit transaction. Byte capacity "</span> +
            <span class="hljs-string">"allocated to store event body "</span> + byteCapacity * byteCapacitySlotSize +
            <span class="hljs-string">"reached. Please increase heap space/byte capacity allocated to "</span> +
            <span class="hljs-string">"the channel as the sinks may not be keeping up with the sources"</span>);
        }
        <span class="hljs-keyword">if</span>(!queueRemaining.tryAcquire(-remainingChange, keepAlive, TimeUnit.SECONDS)) {<span class="hljs-comment">//判断queue是否还有空间接收（因为生产速度快于消费速度）</span>
          bytesRemaining.release(putByteCounter);
          <span class="hljs-keyword">throw</span> <span class="hljs-keyword">new</span> ChannelFullException(<span class="hljs-string">"Space for commit to queue couldn't be acquired."</span> +
              <span class="hljs-string">" Sinks are likely not keeping up with sources, or the buffer size is too tight"</span>);
        }
      }
      <span class="hljs-keyword">int</span> puts = putList.size();<span class="hljs-comment">//事务期间生产的event</span>
      <span class="hljs-keyword">int</span> takes = takeList.size();<span class="hljs-comment">//事务期间等待消费的event</span>
      <span class="hljs-keyword">synchronized</span>(queueLock) {
        <span class="hljs-keyword">if</span>(puts &gt; <span class="hljs-number">0</span> ) {
          <span class="hljs-keyword">while</span>(!putList.isEmpty()) {
            <span class="hljs-keyword">if</span>(!queue.offer(putList.removeFirst())) {<span class="hljs-comment">//将putList中新生产的Events保存到queue</span>
              <span class="hljs-keyword">throw</span> <span class="hljs-keyword">new</span> RuntimeException(<span class="hljs-string">"Queue add failed, this shouldn't be able to happen"</span>);
            }
          }
        }
        putList.clear();<span class="hljs-comment">//重置事务的putList，下同</span>
        takeList.clear();
      }
      bytesRemaining.release(takeByteCounter);
      takeByteCounter = <span class="hljs-number">0</span>;
      putByteCounter = <span class="hljs-number">0</span>;

      queueStored.release(puts);<span class="hljs-comment">//从queueStored释放puts个信号量</span>
      <span class="hljs-keyword">if</span>(remainingChange &gt; <span class="hljs-number">0</span>) {
        queueRemaining.release(remainingChange);<span class="hljs-comment">//queueRemaining释放remainingChange个信号量</span>
      }
      <span class="hljs-keyword">if</span> (puts &gt; <span class="hljs-number">0</span>) {
        channelCounter.addToEventPutSuccessCount(puts);
      }
      <span class="hljs-keyword">if</span> (takes &gt; <span class="hljs-number">0</span>) {
        channelCounter.addToEventTakeSuccessCount(takes);
      }

      channelCounter.setChannelSize(queue.size());
    }

    <span class="hljs-annotation">@Override</span>
    <span class="hljs-keyword">protected</span> <span class="hljs-keyword">void</span> <span class="hljs-title">doRollback</span>() {
      <span class="hljs-keyword">int</span> takes = takeList.size();
      <span class="hljs-keyword">synchronized</span>(queueLock) {
        Preconditions.checkState(queue.remainingCapacity() &gt;= takeList.size(), <span class="hljs-string">"Not enough space in memory channel "</span> +
            <span class="hljs-string">"queue to rollback takes. This should never happen, please report"</span>);
        <span class="hljs-keyword">while</span>(!takeList.isEmpty()) {
          queue.addFirst(takeList.removeLast());<span class="hljs-comment">//把takelist中的events原封不动的放回queue</span>
        }
        putList.clear();
      }
      bytesRemaining.release(putByteCounter);
      putByteCounter = <span class="hljs-number">0</span>;
      takeByteCounter = <span class="hljs-number">0</span>;

      queueStored.release(takes);
      channelCounter.setChannelSize(queue.size());
    }

  }</code></pre>

<p>通过上面的代码我们可以看到MemoryChanne的createTransaction内部使用的是基于内存的MemoryTransaction，没有实现持久化。在生产和消费的过程中，非常关注byteCapacity，防止内存溢出。</p>

<hr>



<h3 id="filechannel">FileChannel</h3>

<p>接下来来看FileChannel的实现TBD</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>