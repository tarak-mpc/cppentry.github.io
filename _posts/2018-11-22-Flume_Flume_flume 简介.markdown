---
layout:     post
title:      Flume_Flume_flume 简介
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/u010003835/article/details/83117416				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p> </p>

<p> </p>

<p>本文对 flume 进行一个简单的介绍，后续还会陆续补充。</p>

<p> </p>

<p>下面介绍下 flume 中的核心概念：</p>

<p> </p>

<p>    flume-1.0 + , 也就是 我们现在一般都在使用的 flume。</p>

<p>都只有1个核心租组件，也就是 agent 。</p>

<p>   其中 Agent 又分为3个模块，source, channel, sink ,</p>

<p>   而 flume 中主要进行数据的搬运，其搬运的基本单位是event 。</p>

<p> </p>

<p>核心组件：Agent 代理</p>

<p>使用JVM 运行Flume。每台机器运行一个agent，但是可以在一个agent中包含多个sources和sinks。</p>

<p> </p>

<p>===================================================</p>

<p> </p>

<p>Agent 代理中的 3个模块， source , channel, sink</p>

<p> </p>

<p>模块名称：Source源 </p>

<p>从Client收集数据，传递给Channel。</p>

<p> </p>

<p>模块名称：Channel通道</p>

<p>连接 sources 和 sinks ，这个有点像一个队列。</p>

<p> </p>

<p>模块名称：Sink接收器</p>

<p>从Channel收集数据，进行相关操作，运行在一个独立线程。</p>

<p> </p>

<p>========================================================</p>

<p>传输单位： Events事件    </p>

<p>传输的基本数据负载。</p>

<p> </p>

<p>下面是一段官网上关于 event 的定义：</p>

<p><strong><span style="color:#f33b45;">注意 ： 但是这个解释是针对Avro反序列化系统中的Event的定义，而flume ng中很多event用的不是这个。</span></strong></p>

<p><img alt="" class="has" src="https://img-blog.csdn.net/20181017211358913?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTAwMDM4MzU=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></p>

<p> </p>

<p>    一行文本内容会被反序列化成一个event。<br>
    <br>
    event的最大定义为2048字节，一行文本超过2048字节，则会被切割。<br>
剩下的会被放到下一个event中。</p>

<p>    默认编码是UTF-8，这都是统一的。</p>

<p>Tips:</p>

<p>    序列化是将对象状态转换为可保持或传输的格式的过程。与序列化相对的是反序列化，<br>
它将流转换为对象。这两个过程结合起来，可以轻松地存储和传输数据.</p>

<p> </p>

<p> </p>            </div>
                </div>