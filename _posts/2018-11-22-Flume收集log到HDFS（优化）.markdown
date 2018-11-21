---
layout:     post
title:      Flume收集log到HDFS（优化）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/lemonZhaoTao/article/details/81751220				</div>
								            <div id="content_views" class="markdown_views prism-github-gist">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>Flume系列文章： <br>
<a href="https://blog.csdn.net/lemonzhaotao/article/details/80560092" rel="nofollow">Flume 概述 &amp; 架构 &amp; 组件介绍</a> <br>
<a href="https://blog.csdn.net/lemonzhaotao/article/details/80640959" rel="nofollow">Flume 使用入门 &amp; 入门Demo</a> <br>
<a href="https://blog.csdn.net/lemonZhaoTao/article/details/81150902" rel="nofollow">Flume收集log到HDFS（雏形）</a></p>

<p>在本篇文章中，将针对上篇文章所提出的问题：Flume收集过来的文件过小，进行解决</p>



<h2 id="问题改进">问题改进</h2>

<p>由于文件过小，我们就不能这样干，需要做一定程度的改进 <br>
<a href="http://archive.cloudera.com/cdh5/cdh/5/flume-ng-1.6.0-cdh5.7.0/FlumeUserGuide.html#hdfs-sink" rel="nofollow">官网：hdfs-sink配置</a></p>

<p><strong>有关参数：</strong></p>



<pre class="prettyprint"><code class=" hljs avrasm">hdfs<span class="hljs-preprocessor">.rollInterval</span>   HDFS回滚的间隔，默认值为<span class="hljs-number">30</span>s(如果等于<span class="hljs-number">0</span>，就不会去滚动，也就是说不基于时间间隔进行滚动了)
hdfs<span class="hljs-preprocessor">.rollSize</span>       以文件大小来触发滚动，默认值是<span class="hljs-number">1024</span>字节(这个值在生产上是需要调大的)
hdfs<span class="hljs-preprocessor">.rollCount</span>      默认值是<span class="hljs-number">10</span>,<span class="hljs-number">10</span>个event来滚动一次(如果设置为<span class="hljs-number">0</span>，就不以记录数为滚动的依据)
这<span class="hljs-number">3</span>个参数如何进行设置，需要根据生产上Block的大小、数据量的频次来进行规划的</code></pre>

<p><code>$FLUME_HOME/conf/exec-memory-hdfs.conf</code></p>



<pre class="prettyprint"><code class=" hljs avrasm">a1<span class="hljs-preprocessor">.sources</span> = <span class="hljs-built_in">r1</span>
a1<span class="hljs-preprocessor">.sinks</span> = k1
a1<span class="hljs-preprocessor">.channels</span> = c1

a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.type</span> = exec
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.command</span> = tail -F /home/hadoop/data/data<span class="hljs-preprocessor">.log</span>
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.channels</span> = c1

a1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.type</span> = memory

a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.type</span> = hdfs
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.path</span> = hdfs://<span class="hljs-number">192.168</span><span class="hljs-number">.26</span><span class="hljs-number">.131</span>:<span class="hljs-number">8020</span>/data/flume/tail
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.batchSize</span> = <span class="hljs-number">10</span>
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.fileType</span> = DataStream
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.writeFormat</span> = Text
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.rollInterval</span> = <span class="hljs-number">0</span>
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.rollSize</span> = <span class="hljs-number">10485760</span>     // <span class="hljs-number">10</span>MB
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.rollCount</span> = <span class="hljs-number">0</span>
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.channel</span> = c1</code></pre>

<p><strong>提出问题：</strong> <br>
Flume正在运行，修改配置文件，是否会立即生效？ <br>
不用去重启Flume，是会自动切换的，这里涉及到设计模式：观察者模式</p>

<p><strong>追加数据到监控目录下：</strong> <br>
<code>$&gt;cat /opt/data/page_views.dat &gt;&gt; ~/data/data.log</code></p>

<p><strong>产生报错：</strong></p>



<pre class="prettyprint"><code class=" hljs applescript"><span class="hljs-number">2018</span>-<span class="hljs-number">02</span>-<span class="hljs-number">07</span> <span class="hljs-number">05</span>:<span class="hljs-number">57</span>:<span class="hljs-number">42</span>,<span class="hljs-number">894</span> (pool-<span class="hljs-number">14</span>-thread-<span class="hljs-number">1</span>) [ERROR - org.apache.flume.source.ExecSource$ExecRunnable.<span class="hljs-command">run</span>(ExecSource.java:<span class="hljs-number">353</span>)] Failed <span class="hljs-keyword">while</span> <span class="hljs-property">running</span> command: tail -F /home/hadoop/data/data.<span class="hljs-command">log</span>
org.apache.flume.ChannelException: Unable <span class="hljs-keyword">to</span> <span class="hljs-keyword">put</span> batch <span class="hljs-function_start"><span class="hljs-keyword">on</span></span> required channel: org.apache.flume.channel.MemoryChannel{<span class="hljs-property">name</span>: c1}
        <span class="hljs-keyword">at</span> org.apache.flume.channel.ChannelProcessor.executeChannelTransaction(ChannelProcessor.java:<span class="hljs-number">253</span>)
        <span class="hljs-keyword">at</span> org.apache.flume.channel.ChannelProcessor.processEventBatch(ChannelProcessor.java:<span class="hljs-number">191</span>)
        <span class="hljs-keyword">at</span> org.apache.flume.source.ExecSource$ExecRunnable.flushEventBatch(ExecSource.java:<span class="hljs-number">382</span>)
        <span class="hljs-keyword">at</span> org.apache.flume.source.ExecSource$ExecRunnable.<span class="hljs-command">run</span>(ExecSource.java:<span class="hljs-number">342</span>)
        <span class="hljs-keyword">at</span> java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:<span class="hljs-number">511</span>)
        <span class="hljs-keyword">at</span> java.util.concurrent.FutureTask.<span class="hljs-command">run</span>(FutureTask.java:<span class="hljs-number">266</span>)
        <span class="hljs-keyword">at</span> java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:<span class="hljs-number">1142</span>)
        <span class="hljs-keyword">at</span> java.util.concurrent.ThreadPoolExecutor$Worker.<span class="hljs-command">run</span>(ThreadPoolExecutor.java:<span class="hljs-number">617</span>)
        <span class="hljs-keyword">at</span> java.lang.Thread.<span class="hljs-command">run</span>(Thread.java:<span class="hljs-number">745</span>)
Caused <span class="hljs-keyword">by</span>: org.apache.flume.ChannelFullException: Space <span class="hljs-keyword">for</span> commit <span class="hljs-keyword">to</span> queue couldn't be acquired. Sinks are likely <span class="hljs-keyword">not</span> keeping up <span class="hljs-keyword">with</span> sources, <span class="hljs-keyword">or</span> <span class="hljs-keyword">the</span> buffer size <span class="hljs-keyword">is</span> too tight
        <span class="hljs-keyword">at</span> org.apache.flume.channel.MemoryChannel$MemoryTransaction.doCommit(MemoryChannel.java:<span class="hljs-number">130</span>)
        <span class="hljs-keyword">at</span> org.apache.flume.channel.BasicTransactionSemantics.commit(BasicTransactionSemantics.java:<span class="hljs-number">151</span>)
        <span class="hljs-keyword">at</span> org.apache.flume.channel.ChannelProcessor.executeChannelTransaction(ChannelProcessor.java:<span class="hljs-number">245</span>)
        ... <span class="hljs-number">8</span> more
<span class="hljs-number">2018</span>-<span class="hljs-number">02</span>-<span class="hljs-number">07</span> <span class="hljs-number">05</span>:<span class="hljs-number">57</span>:<span class="hljs-number">42</span>,<span class="hljs-number">899</span> (timedFlushExecService50-<span class="hljs-number">0</span>) [ERROR - org.apache.flume.source.ExecSource$ExecRunnable$<span class="hljs-number">1.</span><span class="hljs-command">run</span>(ExecSource.java:<span class="hljs-number">328</span>)] Exception occured when processing event batch
org.apache.flume.ChannelException: Unable <span class="hljs-keyword">to</span> <span class="hljs-keyword">put</span> batch <span class="hljs-function_start"><span class="hljs-keyword">on</span></span> required channel: org.apache.flume.channel.MemoryChannel{<span class="hljs-property">name</span>: c1}
        <span class="hljs-keyword">at</span> org.apache.flume.channel.ChannelProcessor.executeChannelTransaction(ChannelProcessor.java:<span class="hljs-number">253</span>)
        <span class="hljs-keyword">at</span> org.apache.flume.channel.ChannelProcessor.processEventBatch(ChannelProcessor.java:<span class="hljs-number">191</span>)
        <span class="hljs-keyword">at</span> org.apache.flume.source.ExecSource$ExecRunnable.flushEventBatch(ExecSource.java:<span class="hljs-number">382</span>)
        <span class="hljs-keyword">at</span> org.apache.flume.source.ExecSource$ExecRunnable.access$<span class="hljs-number">100</span>(ExecSource.java:<span class="hljs-number">255</span>)
        <span class="hljs-keyword">at</span> org.apache.flume.source.ExecSource$ExecRunnable$<span class="hljs-number">1.</span><span class="hljs-command">run</span>(ExecSource.java:<span class="hljs-number">324</span>)
        <span class="hljs-keyword">at</span> java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:<span class="hljs-number">511</span>)
        <span class="hljs-keyword">at</span> java.util.concurrent.FutureTask.runAndReset(FutureTask.java:<span class="hljs-number">308</span>)
        <span class="hljs-keyword">at</span> java.util.concurrent.ScheduledThreadPoolExecutor$ScheduledFutureTask.access$<span class="hljs-number">301</span>(ScheduledThreadPoolExecutor.java:<span class="hljs-number">180</span>)
        <span class="hljs-keyword">at</span> java.util.concurrent.ScheduledThreadPoolExecutor$ScheduledFutureTask.<span class="hljs-command">run</span>(ScheduledThreadPoolExecutor.java:<span class="hljs-number">294</span>)
        <span class="hljs-keyword">at</span> java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:<span class="hljs-number">1142</span>)
        <span class="hljs-keyword">at</span> java.util.concurrent.ThreadPoolExecutor$Worker.<span class="hljs-command">run</span>(ThreadPoolExecutor.java:<span class="hljs-number">617</span>)
        <span class="hljs-keyword">at</span> java.lang.Thread.<span class="hljs-command">run</span>(Thread.java:<span class="hljs-number">745</span>)
Caused <span class="hljs-keyword">by</span>: org.apache.flume.ChannelException: java.lang.InterruptedException
        <span class="hljs-keyword">at</span> org.apache.flume.channel.BasicTransactionSemantics.commit(BasicTransactionSemantics.java:<span class="hljs-number">154</span>)
        <span class="hljs-keyword">at</span> org.apache.flume.channel.ChannelProcessor.executeChannelTransaction(ChannelProcessor.java:<span class="hljs-number">245</span>)
        ... <span class="hljs-number">11</span> more
Caused <span class="hljs-keyword">by</span>: java.lang.InterruptedException
        <span class="hljs-keyword">at</span> java.util.concurrent.locks.AbstractQueuedSynchronizer.doAcquireSharedNanos(AbstractQueuedSynchronizer.java:<span class="hljs-number">1039</span>)
        <span class="hljs-keyword">at</span> java.util.concurrent.locks.AbstractQueuedSynchronizer.tryAcquireSharedNanos(AbstractQueuedSynchronizer.java:<span class="hljs-number">1328</span>)
        <span class="hljs-keyword">at</span> java.util.concurrent.Semaphore.tryAcquire(Semaphore.java:<span class="hljs-number">582</span>)
        <span class="hljs-keyword">at</span> org.apache.flume.channel.MemoryChannel$MemoryTransaction.doCommit(MemoryChannel.java:<span class="hljs-number">128</span>)
        <span class="hljs-keyword">at</span> org.apache.flume.channel.BasicTransactionSemantics.commit(BasicTransactionSemantics.java:<span class="hljs-number">151</span>)
        ... <span class="hljs-number">12</span> more</code></pre>

<p><strong>原因分析：</strong>内存设小了</p>

<p><strong>解决方案：</strong></p>

<ul>
<li>capacity <br>
在channel中能够存储的最大event的数量</li>
<li>transactionCapacity <br>
从source过来最大的event数量 或 到sink去的最大的event数量</li>
</ul>

<p><strong>修改配置文件：</strong> <br>
<code>$FLUME_HOME/conf/exec-memory-hdfs.conf</code></p>



<pre class="prettyprint"><code class=" hljs avrasm">a1<span class="hljs-preprocessor">.sources</span> = <span class="hljs-built_in">r1</span>
a1<span class="hljs-preprocessor">.sinks</span> = k1
a1<span class="hljs-preprocessor">.channels</span> = c1

a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.type</span> = exec
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.command</span> = tail -F /home/hadoop/data/data<span class="hljs-preprocessor">.log</span>
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.channels</span> = c1

a1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.type</span> = memory
a1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.capacity</span> = <span class="hljs-number">10000</span>
a1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.transactionCapacity</span> = <span class="hljs-number">1000</span>

a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.type</span> = hdfs
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.path</span> = hdfs://<span class="hljs-number">192.168</span><span class="hljs-number">.26</span><span class="hljs-number">.131</span>:<span class="hljs-number">8020</span>/data/flume/tail
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.batchSize</span> = <span class="hljs-number">10</span>
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.fileType</span> = DataStream
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.writeFormat</span> = Text
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.rollInterval</span> = <span class="hljs-number">0</span>
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.rollSize</span> = <span class="hljs-number">10485760</span>
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.rollCount</span> = <span class="hljs-number">0</span>
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.channel</span> = c1</code></pre>

<p><strong>向data.log文件中追加内容：</strong> <br>
<code>$&gt;cat /opt/data/page_views.dat &gt;&gt; ~/data/data.log</code> <br>
多执行几次</p>

<p><strong>最终/data/flume/tail路径下的文件内容：</strong></p>



<pre class="prettyprint"><code class=" hljs brainfuck"><span class="hljs-literal">-</span><span class="hljs-comment">rw</span><span class="hljs-literal">-</span><span class="hljs-comment">r</span><span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">r</span><span class="hljs-literal">-</span><span class="hljs-literal">-</span>  <span class="hljs-comment">hadoop</span>  <span class="hljs-comment">supergroup</span>  <span class="hljs-comment">10</span><span class="hljs-string">.</span><span class="hljs-comment">05</span> <span class="hljs-comment">MB</span>    <span class="hljs-comment">Wed</span> <span class="hljs-comment">Feb</span> <span class="hljs-comment">07</span> <span class="hljs-comment">06:58:57</span> <span class="hljs-literal">+</span><span class="hljs-comment">0800</span> <span class="hljs-comment">2018</span>  <span class="hljs-comment">1</span>   <span class="hljs-comment">128</span> <span class="hljs-comment">MB</span>  <span class="hljs-comment">FlumeData</span><span class="hljs-string">.</span><span class="hljs-comment">1517957895215</span>

<span class="hljs-literal">-</span><span class="hljs-comment">rw</span><span class="hljs-literal">-</span><span class="hljs-comment">r</span><span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">r</span><span class="hljs-literal">-</span><span class="hljs-literal">-</span>  <span class="hljs-comment">hadoop</span>  <span class="hljs-comment">supergroup</span>  <span class="hljs-comment">10</span><span class="hljs-string">.</span><span class="hljs-comment">05</span> <span class="hljs-comment">MB</span>    <span class="hljs-comment">Wed</span> <span class="hljs-comment">Feb</span> <span class="hljs-comment">07</span> <span class="hljs-comment">06:59:12</span> <span class="hljs-literal">+</span><span class="hljs-comment">0800</span> <span class="hljs-comment">2018</span>  <span class="hljs-comment">1</span>   <span class="hljs-comment">128</span> <span class="hljs-comment">MB</span>  <span class="hljs-comment">FlumeData</span><span class="hljs-string">.</span><span class="hljs-comment">1517957895216</span>
<span class="hljs-literal">-</span><span class="hljs-comment">rw</span><span class="hljs-literal">-</span><span class="hljs-comment">r</span><span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">r</span><span class="hljs-literal">-</span><span class="hljs-literal">-</span>  <span class="hljs-comment">hadoop</span>  <span class="hljs-comment">supergroup</span>  <span class="hljs-comment">10</span><span class="hljs-string">.</span><span class="hljs-comment">05</span> <span class="hljs-comment">MB</span>    <span class="hljs-comment">Wed</span> <span class="hljs-comment">Feb</span> <span class="hljs-comment">07</span> <span class="hljs-comment">06:59:15</span> <span class="hljs-literal">+</span><span class="hljs-comment">0800</span> <span class="hljs-comment">2018</span>  <span class="hljs-comment">1</span>   <span class="hljs-comment">128</span> <span class="hljs-comment">MB</span>  <span class="hljs-comment">FlumeData</span><span class="hljs-string">.</span><span class="hljs-comment">1517957895217</span>

<span class="hljs-literal">-</span><span class="hljs-comment">rw</span><span class="hljs-literal">-</span><span class="hljs-comment">r</span><span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">r</span><span class="hljs-literal">-</span><span class="hljs-literal">-</span>  <span class="hljs-comment">hadoop</span>  <span class="hljs-comment">supergroup</span>  <span class="hljs-comment">10</span><span class="hljs-string">.</span><span class="hljs-comment">05</span> <span class="hljs-comment">MB</span>    <span class="hljs-comment">Wed</span> <span class="hljs-comment">Feb</span> <span class="hljs-comment">07</span> <span class="hljs-comment">06:59:35</span> <span class="hljs-literal">+</span><span class="hljs-comment">0800</span> <span class="hljs-comment">2018</span>  <span class="hljs-comment">1</span>   <span class="hljs-comment">128</span> <span class="hljs-comment">MB</span>  <span class="hljs-comment">FlumeData</span><span class="hljs-string">.</span><span class="hljs-comment">1517957895218</span>
<span class="hljs-literal">-</span><span class="hljs-comment">rw</span><span class="hljs-literal">-</span><span class="hljs-comment">r</span><span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">r</span><span class="hljs-literal">-</span><span class="hljs-literal">-</span>  <span class="hljs-comment">hadoop</span>  <span class="hljs-comment">supergroup</span>  <span class="hljs-comment">10</span><span class="hljs-string">.</span><span class="hljs-comment">05</span> <span class="hljs-comment">MB</span>    <span class="hljs-comment">Wed</span> <span class="hljs-comment">Feb</span> <span class="hljs-comment">07</span> <span class="hljs-comment">06:59:36</span> <span class="hljs-literal">+</span><span class="hljs-comment">0800</span> <span class="hljs-comment">2018</span>  <span class="hljs-comment">1</span>   <span class="hljs-comment">128</span> <span class="hljs-comment">MB</span>  <span class="hljs-comment">FlumeData</span><span class="hljs-string">.</span><span class="hljs-comment">1517957895219</span>

<span class="hljs-literal">-</span><span class="hljs-comment">rw</span><span class="hljs-literal">-</span><span class="hljs-comment">r</span><span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">r</span><span class="hljs-literal">-</span><span class="hljs-literal">-</span>  <span class="hljs-comment">hadoop</span>  <span class="hljs-comment">supergroup</span>  <span class="hljs-comment">10</span><span class="hljs-string">.</span><span class="hljs-comment">05</span> <span class="hljs-comment">MB</span>    <span class="hljs-comment">Wed</span> <span class="hljs-comment">Feb</span> <span class="hljs-comment">07</span> <span class="hljs-comment">06:59:47</span> <span class="hljs-literal">+</span><span class="hljs-comment">0800</span> <span class="hljs-comment">2018</span>  <span class="hljs-comment">1</span>   <span class="hljs-comment">128</span> <span class="hljs-comment">MB</span>  <span class="hljs-comment">FlumeData</span><span class="hljs-string">.</span><span class="hljs-comment">1517957895220</span>
<span class="hljs-literal">-</span><span class="hljs-comment">rw</span><span class="hljs-literal">-</span><span class="hljs-comment">r</span><span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">r</span><span class="hljs-literal">-</span><span class="hljs-literal">-</span>  <span class="hljs-comment">hadoop</span>  <span class="hljs-comment">supergroup</span>  <span class="hljs-comment">10</span><span class="hljs-string">.</span><span class="hljs-comment">05</span> <span class="hljs-comment">MB</span>    <span class="hljs-comment">Wed</span> <span class="hljs-comment">Feb</span> <span class="hljs-comment">07</span> <span class="hljs-comment">06:59:49</span> <span class="hljs-literal">+</span><span class="hljs-comment">0800</span> <span class="hljs-comment">2018</span>  <span class="hljs-comment">1</span>   <span class="hljs-comment">128</span> <span class="hljs-comment">MB</span>  <span class="hljs-comment">FlumeData</span><span class="hljs-string">.</span><span class="hljs-comment">1517957895221</span>

<span class="hljs-literal">-</span><span class="hljs-comment">rw</span><span class="hljs-literal">-</span><span class="hljs-comment">r</span><span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">r</span><span class="hljs-literal">-</span><span class="hljs-literal">-</span>  <span class="hljs-comment">hadoop</span>  <span class="hljs-comment">supergroup</span>  <span class="hljs-comment">3</span><span class="hljs-string">.</span><span class="hljs-comment">98</span> <span class="hljs-comment">MB</span>     <span class="hljs-comment">Wed</span> <span class="hljs-comment">Feb</span> <span class="hljs-comment">07</span> <span class="hljs-comment">07:00:52</span> <span class="hljs-literal">+</span><span class="hljs-comment">0800</span> <span class="hljs-comment">2018</span>  <span class="hljs-comment">1</span>   <span class="hljs-comment">128</span> <span class="hljs-comment">MB</span>  <span class="hljs-comment">FlumeData</span><span class="hljs-string">.</span><span class="hljs-comment">1517957895222</span></code></pre>

<p>很明显，文件相对于优化之前所执行所显示的，有了很大的提升</p>

<p><strong>建议：</strong> <br>
实际生产上，具体rollSize的值要根据生产上的数据量而定；比如说，可以设置100MB <br>
<strong>疑问：为什么不设置128MB呢？</strong> <br>
因为，我们可以发现：当我们设置10MB的时候，产生的量是10.05MB <br>
同理，如果设置为128MB，最终产生的文件大小可能会比128MB大 <br>
那么，就会产生2个Block，其中1个Block的size必然是很小的，这样小文件就又产生了 <br>
因此，千万不要顶着量去设置，要留点空间 <br>
<strong>注意：</strong> rollInterval、rollSize、rollCount这三个参数都要配置的，只要满足其中1个条件就会滚动了</p>

<p><strong>现象 &amp; 上述做法的弊端：</strong> <br>
配置了rollInterval、rollSize、rollCount这三个参数，可能会遇到如这篇文章所描述的问题：<a href="https://blog.csdn.net/simonchi/article/details/43231891" rel="nofollow">Flume中sink到hdfs，文件系统频繁产生文件，文件滚动配置不起作用</a></p>

<p><strong>即便配置了3个参数之后，仍可能存在的小文件问题：</strong> <br>
仍然有可能遇到小文件问题，即：低峰期的时候，解决方案就是开启作业级别的小文件合并，在作业正式运行前，先进行合并一轮，具体的做法，我们将在后续的文章中进行探讨</p>

<p>下一篇关于Flume的文章，将会带来Flume采集数据到HDFS上的路径、文件名的修改（主要还是修改配置文件）</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>