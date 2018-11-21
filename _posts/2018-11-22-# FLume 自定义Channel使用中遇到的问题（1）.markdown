---
layout:     post
title:      # FLume 自定义Channel使用中遇到的问题（1）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>本文主要Mark一下在生产环境中使用Flume的自定义Channel时遇到的问题以及解决方案，分享以及好记性不如烂博客</p>

<ul>
<li><strong>FlumeChannel 简述</strong></li>
<li><strong>自定义Channel使用背景</strong></li>
<li><strong>自定义Channel的原理 </strong></li>
<li><strong>自定义Channel会存在的问题</strong></li>
<li><strong>自定义Channel逻辑优化</strong></li>
</ul>

<hr>

<h3 id="flume-channel-简述">Flume Channel 简述</h3>

<p>Flume自身提供Channel有两种，一种是MemoryChannel， 一种是FileChannel。 <br>
优劣势对比：</p>

<table>
<thead>
<tr>
  <th>对比</th>
  <th>MemoryChannel</th>
  <th>FileChannel</th>
</tr>
</thead>
<tbody><tr>
  <td>劣势</td>
  <td>内存容量远小于磁盘容量，重启内存数据丢失</td>
  <td>文件操作为磁盘操作，慢</td>
</tr>
<tr>
  <td>优势</td>
  <td>基于内存存取数据，速度快，吞吐量大</td>
  <td>队列可以无限大，重启不丢数据</td>
</tr>
</tbody></table>


<p><br>
这两种Channel的数据结构都是队列，先进先出。Flume源码中MemoryChannel用的是Java的双端阻塞队列 LinkedBlockingDeque。</p>



<pre class="prettyprint"><code class="language-java hljs "><span class="hljs-annotation">@GurardedBy</span>(value = <span class="hljs-string">"queueLock"</span>)
<span class="hljs-keyword">private</span> LinkedBlockingDeque&lt;Event&gt; queue;</code></pre>

<p>通过查找Flume性能测试报告，可以大致得知 MemoryChannel的性能比FileChannel的性能高5-10倍左右，但是这个完全取决于不同的配置，对于FileChannel来说，每次批处理的大小，也就是事务处理Event的量（TransactionCapacity）直接影响到性能。</p>



<h3 id="自定义channel的产生背景-memoryandfilechannel">自定义Channel的产生背景 — MemoryAndFileChannel</h3>

<p>公司的游戏日志采集逻辑是这样的，有两部分数据： <br>
    1、游戏的实时数据 （通过FlumeSDK 发送到游戏服上的Flume） <br>
    <strong>2、每个小时的20分统一发送上个小时的数据（数据源于游戏打印的不规范日志文件，通过定时跑程序完成）</strong></p>

<p>这样的数据流就有一个特点 每个小时20分会有一个小高峰，因为通过第二部分的数据是集中式的发送的。</p>

<p>所以我们Flume中ChannelSize的变化大致是这样的：</p>

<p><strong><em><strong>______</strong></em></strong>/\____________/\________</p>

<p>因为之前用的是MemoryChannel，这个Channel的大小是有上限的，我们之前最大设置为100W，但是在一些用户比较多的服务器上，还是会出现在20-28分这个小高峰，ChannelSize容量满了的情况。 <br>
一旦满了，后续的数据Flume就无法接受了，也就会出现数据丢失的情况。 <br>
而且最为关键的点是：游戏通过FlumeSDK 发送的实时数据的逻辑因为历史原因有一些坑爹的地方，在尝试发送到Flume的数据如果失败的情况，会不停的重试，且重试的过程中会阻塞游戏业务逻辑。</p>

<p>所以，针对于这种情况，公司同事给出的方案是：</p>

<pre><code> 通过使用MemoryAndFileChannel来弄一个容量无限大的Channel
</code></pre>

<p>我能想到的方案还有：</p>

<pre><code>增加Sink的数据，加快下游处理的速度。
</code></pre>



<h3 id="memoryandfilechannel-的运行原理">MemoryAndFileChannel 的运行原理</h3>

<p>MemoryAndFileChannel的底层其实用的依然是MemoryChannel和FileChannel，只是组合起来使用而且，然后在往这个两个Channel中存取的时候加一些判定条件。 <br>
     我们第一版的MemoryAndFileChannel 的逻辑如下： <br>
     1、存数据的逻辑 <br>
         MemoryChannel的容量为50W ,当这50W容量用完的时候，数据往FileChannel中存，容量为1亿（几乎不可能满） <br>
    2、取数据的逻辑 <br>
        先从MemoryChannel中取数据，如果MemoryChannel 中为数据为0，那么再处理FileChannel中的数据。</p>



<h3 id="自定义channel存在的问题">自定义Channel存在的问题</h3>

<p>运营的小伙伴跟我们反馈的情况是这样的，说有玩家充了一笔钱，买了些东西，但是他们查不到。最后把问题定位到日志，说数据从日志出来的，但是从日志里面查不到。用Flume的小伙伴都清楚，Flume能不宕掉的情况下，是不可能丢失数据的。关键我们也没收到任何告警说，Flume宕了，ChannelSize满了之类的。</p>

<p>然后查Zabbix监控，观察我们的自定义Channel中两个Channel的ChannelSize的变化情况。</p>

<p>内存队列大小依旧一样，是这个样子的： <br>
<strong><em>_</em></strong>/\________/\________/\_________/\________</p>

<p>文件队列大小的变化其实就能说明问题了， 是这个样子的：</p>

<p>FileChannel 大小变化<img src="https://img-blog.csdn.net/20171201172037318?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZnJhY2x5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p>也就是在相当长的时间内FileChannel中的大小只涨不减，也就说明，在FileChannel中的数据到最后下降的时候才被发送到下游，也就出现了巨大延迟的情况。</p>

<p>然后找FileChannel出现这个问题的原因，为啥里面的数据就是不被下游处理呢？ <br>
这个问题很坑爹，找了很久都没找到，因为下游没有任何问题，上游也没有问题。只能拿本地机器模拟线上环境，然后通过Flume打印日志最终找到了问题，因为MemoryCHannel的曲线中，0和1的很小，然后高峰时刻又是50W，所以没有发现MemoryChannel的大小其实很长一段时间都是1，而不是0。</p>

<p>从我们上面说到MemoryAndFileChannel的运行原理中，可以发现，如果MemoryChannel的大小不为0，那么就一直处理MemoryChannel中的数据，不去处理FileChannel中的数据，直到某个时刻MemoryChannel中的大小为0。</p>

<p>所以结论就是:</p>

<p><strong>因为MemoryChannel的大小一直为1，导致高峰时刻写进到FileChannel中的必须要等到MemoryChannel的大小为0。所以延时的时间取决去MemoryChannel何时为0，根据我们观察，只有到第二天凌晨才有机会为0。</strong></p>



<h3 id="优化方案">优化方案</h3>

<p>因为之前的逻辑的缺陷在于FileChannel中的数据直到MemoryChannel的大小为0时才有机会处理，然而事实却是MemoryChannel会长时间为1，所以导致延迟 <br>
    所以修改逻辑： <br>
        <strong>当MemoryChannel中的数据小于等于某个值时，就去处理FileChannel中的数据，而不是必须等到0。</strong> <br>
        修改完之后： <br>
        FileChannel和MemoryChannel的变化图都是这样的： <br>
        <strong><em>_</em></strong>/\________/\_________/\_________/\</p>

<p>数据延迟的问题被解决。</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>