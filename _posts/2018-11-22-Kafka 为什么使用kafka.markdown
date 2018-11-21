---
layout:     post
title:      Kafka 为什么使用kafka
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p><strong>分享一下我老师大神的人工智能教程！零基础，通俗易懂！<a href="https://blog.csdn.net/jiangjunshow/article/details/77338485" rel="nofollow">http://blog.csdn.net/jiangjunshow</a></strong></p><p></p><p><strong>也欢迎大家转载本篇文章。分享知识，造福人民，实现我们中华民族伟大复兴！</strong></p><p></p><div class="htmledit_views">                <p>在介绍为什么使用kafka之前，我们有必要来了解一下什么是kafka？</p><h3 id="1-kafka"><a></a>1. 什么是kafka？<a href="http://gitlab.corp.qunar.com/jifeng.si/learningnotes/blob/master/IT/%E5%A4%A7%E6%95%B0%E6%8D%AE/Kafka/%5BKafka%5D%E4%B8%BA%E4%BB%80%E4%B9%88%E4%BD%BF%E7%94%A8kafka%3F.md#1-kafka" rel="nofollow" target="_blank"></a></h3><p>Kafka是由LinkedIn开发的一个分布式的消息系统，使用Scala编写，它以可水平扩展和高吞吐率而被广泛使用。目前越来越多的开源分布式处理系统如Storm，Spark，Flink都支持与Kafka集成。现在我们的数据实时处理平台也使用到了kafka。现在它已被多家不同类型的公司作为多种类型的数据管道和消息系统使用。</p><h3 id="2"><a></a>2. 为什么使用消息系统？<a href="http://gitlab.corp.qunar.com/jifeng.si/learningnotes/blob/master/IT/%E5%A4%A7%E6%95%B0%E6%8D%AE/Kafka/%5BKafka%5D%E4%B8%BA%E4%BB%80%E4%B9%88%E4%BD%BF%E7%94%A8kafka%3F.md#2" rel="nofollow" target="_blank"></a></h3><p>上面我们提到kafka是一个分布式的消息系统。那为什么要在我们的数据处理平台中使用这样的一个消息系统呢？消息系统能给我们带来什么样的好处呢？</p><p>(1) 解耦</p><p>在项目启动之初来预测将来项目会碰到什么需求，是极其困难的。消息系统在处理过程中间插入了一个隐含的、基于数据的接口层，两边的处理过程都要实现这一接口。这允许你独立的扩展或修改两边的处理过程，只要确保它们遵守同样的接口约束。</p><p>(2) 冗余</p><p>有些情况下，处理数据的过程会失败。除非数据被持久化，否则将造成丢失。消息队列把数据进行持久化直到它们已经被完全处理，通过这一方式规避了数据丢失风险。许多消息队列所采用的"插入-获取-删除"范式中，在把一个消息从队列中删除之前，需要你的处理系统明确的指出该消息已经被处理完毕，从而确保你的数据被安全的保存直到你使用完毕。</p><p>(3) 扩展性</p><p>因为消息队列解耦了你的处理过程，所以增大消息入队和处理的频率是很容易的，只要另外增加处理过程即可。不需要改变代码、不需要调节参数。扩展就像调大电力按钮一样简单。</p><p>(4) 灵活性 &amp; 峰值处理能力</p><p>在访问量剧增的情况下，应用仍然需要继续发挥作用，但是这样的突发流量并不常见；如果为以能处理这类峰值访问为标准来投入资源随时待命无疑是巨大的浪费。使用消息队列能够使关键组件顶住突发的访问压力，而不会因为突发的超负荷的请求而完全崩溃。</p><p>(5) 顺序保证</p><p>在大多使用场景下，数据处理的顺序都很重要。大部分消息队列本来就是排序的，并且能保证数据会按照特定的顺序来处理。Kafka保证一个Partition内的消息的有序性。</p><p>(6) 缓冲</p><p>在任何重要的系统中，都会有需要不同的处理时间的元素。例如，加载一张图片比应用过滤器花费更少的时间。消息队列通过一个缓冲层来帮助任务最高效率的执行———写入队列的处理会尽可能的快速。该缓冲有助于控制和优化数据流经过系统的速度。</p><h3 id="3-kafka"><a></a>3. 为什么是kafka？<a href="http://gitlab.corp.qunar.com/jifeng.si/learningnotes/blob/master/IT/%E5%A4%A7%E6%95%B0%E6%8D%AE/Kafka/%5BKafka%5D%E4%B8%BA%E4%BB%80%E4%B9%88%E4%BD%BF%E7%94%A8kafka%3F.md#3-kafka" rel="nofollow" target="_blank"></a></h3><p>上面我们知道我们有必要在数据处理系统中使用一个消息系统，但是我们为什么一定要选kafka呢？现在的消息系统可不只有kafka，俗话说得好，货比三家，我们看一下kafka与其他消息系统的区别。</p><p>LinkedIn团队做了个实验研究，对比Kafka与Apache ActiveMQ V5.4和RabbitMQ V2.4的性能。LinkedIn在两台Linux机器上运行他们的实验，每台机器的配置为8核2GHz、16GB内存，6个磁盘使用RAID10。两台机器通过1GB网络连接。一台机器作为代理，另一台作为生产者或者消费者。</p><h4 id="3-1">3.1 生产者测试<a href="http://gitlab.corp.qunar.com/jifeng.si/learningnotes/blob/master/IT/%E5%A4%A7%E6%95%B0%E6%8D%AE/Kafka/%5BKafka%5D%E4%B8%BA%E4%BB%80%E4%B9%88%E4%BD%BF%E7%94%A8kafka%3F.md#3-1" rel="nofollow" target="_blank"></a></h4><p>对每个系统，运行一个生产者，总共发布1000万条消息，每条消息200字节。Kafka生产者以1和50批量方式发送消息。ActiveMQ和RabbitMQ似乎没有简单的办法来批量发送消息，LinkedIn假定它的批量值为1。结果如下图所示：</p><p><img alt="" src="https://res.infoq.com/articles/apache-kafka/zh/resources/0609015.png"></p><p>Kafka性能要好很多的主要原因包括：</p><p>(1) Kafka不等待代理的确认，以代理能处理的最快速度发送消息。</p><p>(2)Kafka有更高效的存储格式。平均而言，Kafka每条消息有9字节的开销，而ActiveMQ有144字节。其原因是JMS所需的沉重消息头，以及维护各种索引结构的开销。LinkedIn注意到ActiveMQ一个最忙的线程大部分时间都在存取B-Tree以维护消息元数据和状态。</p><h4 id="3-2">3.2 消费者测试<a href="http://gitlab.corp.qunar.com/jifeng.si/learningnotes/blob/master/IT/%E5%A4%A7%E6%95%B0%E6%8D%AE/Kafka/%5BKafka%5D%E4%B8%BA%E4%BB%80%E4%B9%88%E4%BD%BF%E7%94%A8kafka%3F.md#3-2" rel="nofollow" target="_blank"></a></h4><p>为了做消费者测试，LinkedIn使用一个消费者获取总共1000万条消息。LinkedIn让所有系统每次拉请求都预获取大约相同数量的数据，最多1000条消息或者200KB。对ActiveMQ和RabbitMQ，LinkedIn设置消费者确认模型为自动。结果如下图所示:</p><p><img alt="" src="https://res.infoq.com/articles/apache-kafka/zh/resources/0609016.png"></p><p>Kafka性能要好很多的主要原因包括：</p><p>(1) Kafka有更高效的存储格式；在Kafka中，从代理传输到消费者的字节更少。</p><p>(2) ActiveMQ和RabbitMQ两个容器中的代理必须维护每个消息的传输状态。LinkedIn团队注意到其中一个ActiveMQ线程在测试过程中，一直在将KahaDB页写入磁盘。与此相反，Kafka代理没有磁盘写入动作。最后，Kafka通过使用sendfile API降低了传输开销。</p>            </div><p></p><strong></strong><h4>给我老师的人工智能教程打call！<a href="https://blog.csdn.net/jiangjunshow/article/details/77338485" rel="nofollow">http://blog.csdn.net/jiangjunshow</a></h4><div align="center"><img title="" alt="这里写图片描述" src="https://img-blog.csdn.net/20161220210733446?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3VuaHVhcWlhbmcx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast"></div>
你好！ 这是你第一次使用 **Markdown编辑器** 所展示的欢迎页。如果你想学习如何使用Markdown编辑器, 可以仔细阅读这篇文章，了解一下Markdown的基本语法知识。
<h2><a id="_3"></a>新的改变</h2>
<p>我们对Markdown编辑器进行了一些功能拓展与语法支持，除了标准的Markdown编辑器功能，我们增加了如下几点新功能，帮助你用它写博客：</p>
<ol>
<li><strong>全新的界面设计</strong> ，将会带来全新的写作体验；</li>
<li>在创作中心设置你喜爱的代码高亮样式，Markdown <strong>将代码片显示选择的高亮样式</strong> 进行展示；</li>
<li>增加了 <strong>图片拖拽</strong> 功能，你可以将本地的图片直接拖拽到编辑区域直接展示；</li>
<li>全新的 <strong>KaTeX数学公式</strong> 语法；</li>
<li>增加了支持<strong>甘特图的mermaid语法<sup class="footnote-ref"><a id="fnref1" href="#fn1" rel="nofollow">1</a></sup></strong> 功能；</li>
<li>增加了 <strong>多屏幕编辑</strong> Markdown文章功能；</li>
<li>增加了 <strong>焦点写作模式、预览模式、简洁写作模式、左右区域同步滚轮设置</strong> 等功能，功能按钮位于编辑区域与预览区域中间；</li>
<li>增加了 <strong>检查列表</strong> 功能。</li>
</ol>
<h2><a id="_17"></a>功能快捷键</h2>
<p>撤销：<kbd>Ctrl/Command</kbd> + <kbd>Z</kbd><br>
重做：<kbd>Ctrl/Command</kbd> + <kbd>Y</kbd><br>
加粗：<kbd>Ctrl/Command</kbd> + <kbd>B</kbd><br>
斜体：<kbd>Ctrl/Command</kbd> + <kbd>I</kbd><br>
标题：<kbd>Ctrl/Command</kbd> + <kbd>Shift</kbd> + <kbd>H</kbd><br>
无序列表：<kbd>Ctrl/Command</kbd> + <kbd>Shift</kbd> + <kbd>U</kbd><br>
有序列表：<kbd>Ctrl/Command</kbd> + <kbd>Shift</kbd> + <kbd>O</kbd><br>
检查列表：<kbd>Ctrl/Command</kbd> + <kbd>Shift</kbd> + <kbd>C</kbd><br>
插入代码：<kbd>Ctrl/Command</kbd> + <kbd>Shift</kbd> + <kbd>K</kbd><br>
插入链接：<kbd>Ctrl/Command</kbd> + <kbd>Shift</kbd> + <kbd>L</kbd><br>
插入图片：<kbd>Ctrl/Command</kbd> + <kbd>Shift</kbd> + <kbd>G</kbd></p>
<h2><a id="_32"></a>合理的创建标题，有助于目录的生成</h2>
<p>直接输入1次<kbd>#</kbd>，并按下<kbd>space</kbd>后，将生成1级标题。<br>
输入2次<kbd>#</kbd>，并按下<kbd>space</kbd>后，将生成2级标题。<br>
以此类推，我们支持6级标题。有助于使用<code>TOC</code>语法后生成一个完美的目录。</p>
<h2><a id="_40"></a>如何改变文本的样式</h2>
<p><em>强调文本</em> <em>强调文本</em></p>
<p><strong>加粗文本</strong> <strong>加粗文本</strong></p>
<p><mark>标记文本</mark></p>
<p><s>删除文本</s></p>
<blockquote>
<p>引用文本</p>
</blockquote>
<p>H<sub>2</sub>O is是液体。</p>
<p>2<sup>10</sup> 运算结果是 1024.</p>
<h2><a id="_58"></a>插入链接与图片</h2>
<p>链接: <a href="https://mp.csdn.net" rel="nofollow">link</a>.</p>
<p>图片: <img alt="Alt" src="https://avatar.csdn.net/7/7/B/1_ralf_hx163com.jpg"></p>
<p>带尺寸的图片: <img width="30" height="30" alt="Alt" src="https://avatar.csdn.net/7/7/B/1_ralf_hx163com.jpg"></p>
<p>当然，我们为了让用户更加便捷，我们增加了图片拖拽功能。</p>
<h2><a id="_69"></a>如何插入一段漂亮的代码片</h2>
<p>去<a href="https://mp.csdn.net/configure" rel="nofollow">博客设置</a>页面，选择一款你喜欢的代码片高亮样式，下面展示同样高亮的 <code>代码片</code>.</p>
<pre><code class="prism language-javascript"><span class="token comment">// An highlighted block var foo = 'bar'; </span></code></pre>
<h2><a id="_78"></a>生成一个适合你的列表</h2>
<ul>
<li>项目
<ul>
<li>项目
<ul>
<li>项目</li>
</ul>
</li>
</ul>
</li>
</ul>
<ol>
<li>项目1</li>
<li>项目2</li>
<li>项目3</li>
</ol>
<ul>
<li class="task-list-item"><input disabled class="task-list-item-checkbox" type="checkbox"> 计划任务</li>
<li class="task-list-item"><input disabled class="task-list-item-checkbox" type="checkbox" checked> 完成任务</li>
</ul>
<h2><a id="_92"></a>创建一个表格</h2>
<p>一个简单的表格是这么创建的：</p>

<table>
<thead>
<tr>
<th>项目</th>
<th>Value</th>
</tr>
</thead>
<tbody>
<tr>
<td>电脑</td>
<td>$1600</td>
</tr>
<tr>
<td>手机</td>
<td>$12</td>
</tr>
<tr>
<td>导管</td>
<td>$1</td>
</tr>
</tbody>
</table><h3><a id="_100"></a>设定内容居中、居左、居右</h3>
<p>使用<code>:---------:</code>居中<br>
使用<code>:----------</code>居左<br>
使用<code>----------:</code>居右</p>

<table>
<thead>
<tr>
<th align="center">第一列</th>
<th align="right">第二列</th>
<th align="left">第三列</th>
</tr>
</thead>
<tbody>
<tr>
<td align="center">第一列文本居中</td>
<td align="right">第二列文本居右</td>
<td align="left">第三列文本居左</td>
</tr>
</tbody>
</table><h3><a id="SmartyPants_109"></a>SmartyPants</h3>
<p>SmartyPants将ASCII标点字符转换为“智能”印刷标点HTML实体。例如：</p>

<table>
<thead>
<tr>
<th>TYPE</th>
<th>ASCII</th>
<th><abbr title="超文本标记语言">HTML</abbr></th>
</tr>
</thead>
<tbody>
<tr>
<td>Single backticks</td>
<td><code>'Isn't this fun?'</code></td>
<td>‘Isn’t this fun?’</td>
</tr>
<tr>
<td>Quotes</td>
<td><code>"Isn't this fun?"</code></td>
<td>“Isn’t this fun?”</td>
</tr>
<tr>
<td>Dashes</td>
<td><code>-- is en-dash, --- is em-dash</code></td>
<td>– is en-dash, — is em-dash</td>
</tr>
</tbody>
</table><h2><a id="_118"></a>创建一个自定义列表</h2>
<dl>
<dt>Markdown</dt>
<dd>Text-to-<abbr title="超文本标记语言">HTML</abbr> conversion tool</dd>
<dt>Authors</dt>
<dd>John</dd>
<dd>Luke</dd>
</dl>
<h2><a id="_127"></a>如何创建一个注脚</h2>
<p>一个具有注脚的文本。<sup class="footnote-ref"><a id="fnref2" href="#fn2" rel="nofollow">2</a></sup></p>
<h2><a id="_134"></a>注释也是必不可少的</h2>
<p>Markdown将文本转换为 <abbr title="超文本标记语言">HTML</abbr>。</p>
<h2><a id="KaTeX_141"></a>KaTeX数学公式</h2>
<p>您可以使用渲染LaTeX数学表达式 <a href="https://khan.github.io/KaTeX/" rel="nofollow">KaTeX</a>:</p>
<p>Gamma公式展示 <span class="katex--inline"><span class="katex"><span class="katex-mathml"><math><semantics><mrow><mi mathvariant="normal">Γ</mi><mo>(</mo><mi>n</mi><mo>)</mo><mo>=</mo><mo>(</mo><mi>n</mi><mo>−</mo><mn>1</mn><mo>)</mo><mo>!</mo><mspace width="1em"></mspace><mi mathvariant="normal">∀</mi><mi>n</mi><mo>∈</mo><mi mathvariant="double-struck">N</mi></mrow><annotation encoding="application/x-tex">\Gamma(n) = (n-1)!\quad\forall
n\in\mathbb N</annotation></semantics></math></span><span class="katex-html" aria-hidden="true"><span class="base"><span class="strut" style="height: 1em; vertical-align: -0.25em;"></span><span class="mord">Γ</span><span class="mopen">(</span><span class="mord mathit">n</span><span class="mclose">)</span><span class="mspace" style="margin-right: 0.27em;"></span><span class="mrel">=</span><span class="mspace" style="margin-right: 0.27em;"></span></span><span class="base"><span class="strut" style="height: 1em; vertical-align: -0.25em;"></span><span class="mopen">(</span><span class="mord mathit">n</span><span class="mspace" style="margin-right: 0.22em;"></span><span class="mbin">−</span><span class="mspace" style="margin-right: 0.22em;"></span></span><span class="base"><span class="strut" style="height: 1em; vertical-align: -0.25em;"></span><span class="mord">1</span><span class="mclose">)</span><span class="mclose">!</span><span class="mspace" style="margin-right: 1em;"></span><span class="mord">∀</span><span class="mord mathit">n</span><span class="mspace" style="margin-right: 0.27em;"></span><span class="mrel">∈</span><span class="mspace" style="margin-right: 0.27em;"></span></span><span class="base"><span class="strut" style="height: 0.68em; vertical-align: 0em;"></span><span class="mord mathbb">N</span></span></span></span></span> 是通过欧拉积分</p>
<p><span class="katex--display"><span class="katex-display"><span class="katex"><span class="katex-mathml"><math><semantics><mrow><mi mathvariant="normal">Γ</mi><mo>(</mo><mi>z</mi><mo>)</mo><mo>=</mo><msubsup><mo>∫</mo><mn>0</mn><mi mathvariant="normal">∞</mi></msubsup><msup><mi>t</mi><mrow><mi>z</mi><mo>−</mo><mn>1</mn></mrow></msup><msup><mi>e</mi><mrow><mo>−</mo><mi>t</mi></mrow></msup><mi>d</mi><mi>t</mi>&amp;ThinSpace;<mi mathvariant="normal">.</mi></mrow><annotation encoding="application/x-tex">
\Gamma(z) = \int_0^\infty t^{z-1}e^{-t}dt\,.
</annotation></semantics></math></span><span class="katex-html" aria-hidden="true"><span class="base"><span class="strut" style="height: 1em; vertical-align: -0.25em;"></span><span class="mord">Γ</span><span class="mopen">(</span><span class="mord mathit" style="margin-right: 0.04em;">z</span><span class="mclose">)</span><span class="mspace" style="margin-right: 0.27em;"></span><span class="mrel">=</span><span class="mspace" style="margin-right: 0.27em;"></span></span><span class="base"><span class="strut" style="height: 2.32em; vertical-align: -0.91em;"></span><span class="mop"><span class="mop op-symbol large-op" style="top: 0em; margin-right: 0.44em; position: relative;">∫</span><span class="msupsub"><span class="vlist-t vlist-t2"><span class="vlist-r"><span class="vlist" style="height: 1.41em;"><span style="top: -1.78em; margin-right: 0.05em; margin-left: -0.44em;"><span class="pstrut" style="height: 2.7em;"></span><span class="sizing reset-size6 size3 mtight"><span class="mord mtight">0</span></span></span><span style="top: -3.81em; margin-right: 0.05em;"><span class="pstrut" style="height: 2.7em;"></span><span class="sizing reset-size6 size3 mtight"><span class="mord mtight">∞</span></span></span></span><span class="vlist-s">​</span></span><span class="vlist-r"><span class="vlist" style="height: 0.91em;"><span></span></span></span></span></span></span><span class="mspace" style="margin-right: 0.16em;"></span><span class="mord"><span class="mord mathit">t</span><span class="msupsub"><span class="vlist-t"><span class="vlist-r"><span class="vlist" style="height: 0.86em;"><span style="top: -3.11em; margin-right: 0.05em;"><span class="pstrut" style="height: 2.7em;"></span><span class="sizing reset-size6 size3 mtight"><span class="mord mtight"><span class="mord mathit mtight" style="margin-right: 0.04em;">z</span><span class="mbin mtight">−</span><span class="mord mtight">1</span></span></span></span></span></span></span></span></span><span class="mord"><span class="mord mathit">e</span><span class="msupsub"><span class="vlist-t"><span class="vlist-r"><span class="vlist" style="height: 0.84em;"><span style="top: -3.11em; margin-right: 0.05em;"><span class="pstrut" style="height: 2.7em;"></span><span class="sizing reset-size6 size3 mtight"><span class="mord mtight"><span class="mord mtight">−</span><span class="mord mathit mtight">t</span></span></span></span></span></span></span></span></span><span class="mord mathit">d</span><span class="mord mathit">t</span><span class="mspace" style="margin-right: 0.16em;"></span><span class="mord">.</span></span></span></span></span></span></p>
<blockquote>
<p>你可以找到更多关于的信息 <strong>LaTeX</strong> 数学表达式<a href="http://meta.math.stackexchange.com/questions/5020/mathjax-basic-tutorial-and-quick-reference" rel="nofollow">here</a>.</p>
</blockquote>
<h2><a id="_155"></a>新的甘特图功能，丰富你的文章</h2>
<pre><code class="prism language-mermaid">gantt
        dateFormat  YYYY-MM-DD
        title Adding GANTT diagram functionality to mermaid
        section 现有任务
        已完成               :done,    des1, 2014-01-06,2014-01-08
        进行中               :active,  des2, 2014-01-09, 3d
        计划一               :         des3, after des2, 5d
        计划二               :         des4, after des3, 5d
</code></pre>
<ul>
<li>关于 <strong>甘特图</strong> 语法，参考 <a href="https://mermaidjs.github.io/" rel="nofollow">这儿</a>,</li>
</ul>
<h2><a id="UML__169"></a>UML 图表</h2>
<p>可以使用UML图表进行渲染。 <a href="https://mermaidjs.github.io/" rel="nofollow">Mermaid</a>. 例如下面产生的一个序列图：:</p>
<div class="mermaid"><svg xmlns="http://www.w3.org/2000/svg" id="mermaid-svg-Xtj4HVVwyHbmIEsE" style="max-width: 750px;" viewbox="-50 -10 750 467.35" width="100%" height="100%"><g></g><g><line class="actor-line" id="actor3" stroke="#999" stroke-width="0.5px" x1="75" y1="5" x2="75" y2="456.35"></line><rect class="actor" fill="#eaeaea" stroke="#666" x="0" y="0" width="150" height="65" rx="3" ry="3"></rect><text class="actor" style="text-anchor: middle;" alignment-baseline="central" dominant-baseline="central" x="75" y="32.5"><tspan x="75" dy="0">张三</tspan></text></g><g><line class="actor-line" id="actor4" stroke="#999" stroke-width="0.5px" x1="275" y1="5" x2="275" y2="456.35"></line><rect class="actor" fill="#eaeaea" stroke="#666" x="200" y="0" width="150" height="65" rx="3" ry="3"></rect><text class="actor" style="text-anchor: middle;" alignment-baseline="central" dominant-baseline="central" x="275" y="32.5"><tspan x="275" dy="0">李四</tspan></text></g><g><line class="actor-line" id="actor5" stroke="#999" stroke-width="0.5px" x1="475" y1="5" x2="475" y2="456.35"></line><rect class="actor" fill="#eaeaea" stroke="#666" x="400" y="0" width="150" height="65" rx="3" ry="3"></rect><text class="actor" style="text-anchor: middle;" alignment-baseline="central" dominant-baseline="central" x="475" y="32.5"><tspan x="475" dy="0">王五</tspan></text></g><defs><marker id="arrowhead" refx="5" refy="2" markerwidth="6" markerheight="4" orient="auto"><path d="M 0 0 V 4 L 6 2 Z"></path></marker></defs><defs><marker id="crosshead" refx="16" refy="4" markerwidth="15" markerheight="8" orient="auto"><path style="stroke-dasharray: 0,0;" fill="black" stroke="#000000" stroke-width="1px" d="M 9 2 V 6 L 16 4 Z"></path><path style="stroke-dasharray: 0,0;" fill="none" stroke="#000000" stroke-width="1px" d="M 0 1 L 6 7 M 6 1 L 0 7"></path></marker></defs><g><text class="messageText" style="text-anchor: middle;" x="175" y="93">你好！李四, 最近怎么样?</text><line class="messageLine0" style="fill: none;" marker-end='url("#arrowhead")' stroke="black" stroke-width="2" x1="75" y1="100" x2="275" y2="100"></line></g><g><text class="messageText" style="text-anchor: middle;" x="375" y="128">你最近怎么样，王五？</text><line class="messageLine1" style="fill: none; stroke-dasharray: 3,3;" marker-end='url("#arrowhead")' stroke="black" stroke-width="2" x1="275" y1="135" x2="475" y2="135"></line></g><g><text class="messageText" style="text-anchor: middle;" x="175" y="163">我很好，谢谢!</text><line class="messageLine1" style="fill: none; stroke-dasharray: 3,3;" marker-end='url("#crosshead")' stroke="black" stroke-width="2" x1="275" y1="170" x2="75" y2="170"></line></g><g><text class="messageText" style="text-anchor: middle;" x="375" y="198">我很好，谢谢!</text><line class="messageLine0" style="fill: none;" marker-end='url("#crosshead")' stroke="black" stroke-width="2" x1="275" y1="205" x2="475" y2="205"></line></g><g><rect class="note" fill="#edf2ae" stroke="#666" x="500" y="215" width="150" height="86.35" rx="0" ry="0"></rect><text class="noteText" fill="black" x="516" y="245"><tspan x="516">李四想了很长时间,</tspan><tspan x="516" dy="23">文字太长了</tspan><tspan x="516" dy="23">不适合放在一行.</tspan></text></g><g><text class="messageText" style="text-anchor: middle;" x="175" y="329.35">打量着王五...</text><line class="messageLine1" style="fill: none; stroke-dasharray: 3,3;" marker-end='url("#arrowhead")' stroke="black" stroke-width="2" x1="275" y1="336.35" x2="75" y2="336.35"></line></g><g><text class="messageText" style="text-anchor: middle;" x="275" y="364.35">很好... 王五, 你怎么样?</text><line class="messageLine0" style="fill: none;" marker-end='url("#arrowhead")' stroke="black" stroke-width="2" x1="75" y1="371.35" x2="475" y2="371.35"></line></g><g><rect class="actor" fill="#eaeaea" stroke="#666" x="0" y="391.35" width="150" height="65" rx="3" ry="3"></rect><text class="actor" style="text-anchor: middle;" alignment-baseline="central" dominant-baseline="central" x="75" y="423.85"><tspan x="75" dy="0">张三</tspan></text></g><g><rect class="actor" fill="#eaeaea" stroke="#666" x="200" y="391.35" width="150" height="65" rx="3" ry="3"></rect><text class="actor" style="text-anchor: middle;" alignment-baseline="central" dominant-baseline="central" x="275" y="423.85"><tspan x="275" dy="0">李四</tspan></text></g><g><rect class="actor" fill="#eaeaea" stroke="#666" x="400" y="391.35" width="150" height="65" rx="3" ry="3"></rect><text class="actor" style="text-anchor: middle;" alignment-baseline="central" dominant-baseline="central" x="475" y="423.85"><tspan x="475" dy="0">王五</tspan></text></g></svg></div>
<p>这将产生一个流程图。:</p>
<div class="mermaid"><svg xmlns="http://www.w3.org/2000/svg" id="mermaid-svg-yNdgIs0xA7G66vTR" viewbox="0 0 232 150" width="212" height="130"><g><g class="output"><g class="clusters"></g><g class="edgePaths"><g class="edgePath" style="opacity: 1;"><path class="path" style="fill: none;" marker-end='url("#arrowhead31")' d="M 40 55 L 65 30 L 90 30"></path><defs><marker id="arrowhead31" viewbox="0 0 10 10" refx="9" refy="5" markerunits="strokeWidth" markerwidth="8" markerheight="6" orient="auto"><path class="arrowheadPath" style="stroke-dasharray: 1,0; stroke-width: 1;" d="M 0 0 L 10 5 L 0 10 Z"></path></marker></defs></g><g class="edgePath" style="opacity: 1;"><path class="path" style="fill: none;" marker-end='url("#arrowhead32")' d="M 40 75 L 65 100 L 90 100"></path><defs><marker id="arrowhead32" viewbox="0 0 10 10" refx="9" refy="5" markerunits="strokeWidth" markerwidth="8" markerheight="6" orient="auto"><path class="arrowheadPath" style="stroke-dasharray: 1,0; stroke-width: 1;" d="M 0 0 L 10 5 L 0 10 Z"></path></marker></defs></g><g class="edgePath" style="opacity: 1;"><path class="path" style="fill: none;" marker-end='url("#arrowhead33")' d="M 110 30 L 135 30 L 167.868 58.1316"></path><defs><marker id="arrowhead33" viewbox="0 0 10 10" refx="9" refy="5" markerunits="strokeWidth" markerwidth="8" markerheight="6" orient="auto"><path class="arrowheadPath" style="stroke-dasharray: 1,0; stroke-width: 1;" d="M 0 0 L 10 5 L 0 10 Z"></path></marker></defs></g><g class="edgePath" style="opacity: 1;"><path class="path" style="fill: none;" marker-end='url("#arrowhead34")' d="M 110 100 L 135 100 L 167.868 72.8684"></path><defs><marker id="arrowhead34" viewbox="0 0 10 10" refx="9" refy="5" markerunits="strokeWidth" markerwidth="8" markerheight="6" orient="auto"><path class="arrowheadPath" style="stroke-dasharray: 1,0; stroke-width: 1;" d="M 0 0 L 10 5 L 0 10 Z"></path></marker></defs></g></g><g class="edgeLabels"><g class="edgeLabel" style="opacity: 1;" transform=""><g class="label" transform="translate(0)"><foreignobject width="0" height="0"><div style="display: inline-block; white-space: nowrap;" xmlns="http://www.w3.org/1999/xhtml"><span class="edgeLabel">链接</span></div></foreignobject></g></g><g class="edgeLabel" style="opacity: 1;" transform=""><g class="label" transform="translate(0)"><foreignobject width="0" height="0"><div style="display: inline-block; white-space: nowrap;" xmlns="http://www.w3.org/1999/xhtml"><span class="edgeLabel"></span></div></foreignobject></g></g><g class="edgeLabel" style="opacity: 1;" transform=""><g class="label" transform="translate(0)"><foreignobject width="0" height="0"><div style="display: inline-block; white-space: nowrap;" xmlns="http://www.w3.org/1999/xhtml"><span class="edgeLabel"></span></div></foreignobject></g></g><g class="edgeLabel" style="opacity: 1;" transform=""><g class="label" transform="translate(0)"><foreignobject width="0" height="0"><div style="display: inline-block; white-space: nowrap;" xmlns="http://www.w3.org/1999/xhtml"><span class="edgeLabel"></span></div></foreignobject></g></g></g><g class="nodes"><g class="node" id="A" style="opacity: 1;" transform="translate(30 65)"><rect x="-10" y="-10" width="20" height="20" rx="0" ry="0"></rect><g class="label" transform="translate(0)"><g transform="translate(0)"><foreignobject width="0" height="0"><div style="display: inline-block; white-space: nowrap;" xmlns="http://www.w3.org/1999/xhtml">长方形</div></foreignobject></g></g></g><g class="node" id="B" style="opacity: 1;" transform="translate(100 30)"><circle r="10" x="-10" y="-10"></circle><g class="label" transform="translate(0)"><g transform="translate(0)"><foreignobject width="0" height="0"><div style="display: inline-block; white-space: nowrap;" xmlns="http://www.w3.org/1999/xhtml">圆</div></foreignobject></g></g></g><g class="node" id="C" style="opacity: 1;" transform="translate(100 100)"><rect x="-10" y="-10" width="20" height="20" rx="5" ry="5"></rect><g class="label" transform="translate(0)"><g transform="translate(0)"><foreignobject width="0" height="0"><div style="display: inline-block; white-space: nowrap;" xmlns="http://www.w3.org/1999/xhtml">圆角长方形</div></foreignobject></g></g></g><g class="node" id="D" style="opacity: 1;" transform="translate(176 65)"><polygon transform="translate(-16 16)" points="16,0 32,-16 16,-32 0,-16" rx="5" ry="5"></polygon><g class="label" transform="translate(0)"><g transform="translate(0)"><foreignobject width="0" height="0"><div style="display: inline-block; white-space: nowrap;" xmlns="http://www.w3.org/1999/xhtml">菱形</div></foreignobject></g></g></g></g></g></g></svg></div>
<ul>
<li>关于 <strong>Mermaid</strong> 语法，参考 <a href="https://mermaidjs.github.io/" rel="nofollow">这儿</a>,</li>
</ul>
<h2><a id="FLowchart_197"></a>FLowchart流程图</h2>
<p>我们依旧会支持flowchart的流程图：</p>
<div class="mermaid"><svg xmlns="http://www.w3.org/2000/svg" id="mermaid-svg-wAZEuHNIHbC0xAEz"><g></g></svg></div>
<ul>
<li>关于 <strong>Flowchart流程图</strong> 语法，参考 <a href="http://adrai.github.io/flowchart.js/" rel="nofollow">这儿</a>.</li>
</ul>
<h2><a id="_215"></a>导出与导入</h2>
<h3><a id="_217"></a>导出</h3>
<p>如果你想尝试使用此编辑器, 你可以在此篇文章任意编辑。当你完成了一篇文章的写作, 在上方工具栏找到 <strong>文章导出</strong> ，生成一个.md文件或者.html文件进行本地保存。</p>
<h3><a id="_220"></a>导入</h3>
<p>如果你想加载一篇你写过的.md文件或者.html文件，在上方工具栏可以选择导入功能进行对应扩展名的文件导入，<br>
继续你的创作。</p>
<hr class="footnotes-sep">
<section class="footnotes">
<ol class="footnotes-list">
<li class="footnote-item" id="fn1"><p><a href="https://mermaidjs.github.io/" rel="nofollow">mermaid语法说明</a> <a class="footnote-backref" href="#fnref1" rel="nofollow">↩︎</a></p>
</li>
<li class="footnote-item" id="fn2"><p>注脚的解释 <a class="footnote-backref" href="#fnref2" rel="nofollow">↩︎</a></p>
</li>
</ol>
</section>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>