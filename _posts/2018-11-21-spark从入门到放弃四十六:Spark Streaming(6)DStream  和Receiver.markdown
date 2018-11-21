---
layout:     post
title:      spark从入门到放弃四十六:Spark Streaming(6)DStream  和Receiver
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2 id="1简介">1.简介</h2>

<p>输入DStream代表了来自数据源的输入数据流。在之前的worldcount  例子中，lines  就是一个输入DStream  （JavaReceiverInputDstream）,代表了从netcat   服务接收到数据流，除了文件数据流之外，所有输入的DStream 都会绑定成一个Receiver  对象，该对象是一个关键的组件，用来从数据源接收数据，并将其存储到Spark   内存中，以供后续处理 <br>
Spark Streaming   提供了两种内置的数据源支持 <br>
1.  基础数据源：StreamingContext   API  种直接提供了对这些数据源的支持，比如文件，socket  Akka  Actor   等 <br>
2. 高级数据源：  诸如kafka   ，flume,kinesis,Twitter  等数据源，通过第三方工具类提供支持。这些数据源的使用，需要引入其他依赖。 <br>
3. 自定义数据源：我们可以自己定义数据源，来决定如何接受和存储数据</p>

<p>要注意的是，如果你想要再实时计算应用中并行接受多条数据流，可以创建多个输入DStream   ，  这样就会创建多个Receiver  ,从而并行的接收多个数据流。但是要注意的事，一个Spark  Streaming    Application   的Executor   ，是一个长时间运行的任务，因此它会独占分配给Spark Streaming   的cpu  core   。从而只要Spark   Streaming   运行起来后这个节点上面的cpu  core   就没法给以他应用使用了。 <br>
使用本地模式，运行程序的时候绝对不能用local   或者 local[1]   因为那样的话，只会给只能输入的Dstream   的executor   分配一个线程。而spark  Streaming  的底层原理是，至少两个线程，一个线程用来分配给Receiver  接收数据，一条线程用来处理接收到的数据。因此必须使用local[n]  的方式，n&gt;2  的模式</p>

<p>如果不设置Master   也即是直接将SparkStreaming   应用提交到集群去运行，  那么首先必须要求集群节点上之后有一个cpu  core   其次给Spark Stream   的每个executor  分配的core   必须大于1  这样才能保证分配到executor  上运行的输入DStream   两条线程并行，一条receiver   接收数据；一条处理数据否则只会接收数据不会处理数据。</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>