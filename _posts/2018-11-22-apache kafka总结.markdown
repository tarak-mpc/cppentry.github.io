---
layout:     post
title:      apache kafka总结
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="apache-kafka总结">apache kafka总结</h1>

<p>本文将从多个方面对apache kafka进行总结.</p>



<h2 id="简介">简介</h2>

<p>apache kafka是一个分布式消息队列. <br>
这个消息队列在很多场景中被应用, 这一点在kafka apache项目的网站中就有介绍. <br>
相对于其他消息队列而言(比如rabbitMQ等), 在大量数据传输方面性能较好. <br>
当然也有它的不足, 暂时没有足够的中文文档是一方面, 另一方面, kafka在用户制定方面也有所欠缺. <br>
图1-1是apache kafka简略架构图.</p>

<p></p><center> <br>
<img src="https://img-blog.csdn.net/20160423144820530" alt="这里写图片描述" title=""> <br>
</center> <br>
<center> <br>
图1-1 <br>
</center><p></p>

<p>生产者(producer)与apache kafka通讯, 向apache kafka发送信息; <br>
消费者(consumer)与apache kafka通讯, 订阅相应的消息, apache kafka向消费者发送订阅的信息. <br>
这一点与普通消息队列相似.</p>

<h2 id="架构">架构</h2>



<h3 id="整体">整体</h3>

<p>上一节简单介绍了apache kafka, 本节将详细介绍apache kafka的详细架构. <br>
完整的架构图如图2-1所是. <br>
</p><center> <br>
<img src="https://img-blog.csdn.net/20160423144919656" alt="这里写图片描述" title=""> <br>
</center> <br>
<center> <br>
图2-1 <br>
</center><p></p>

<p>从它的架构图中, 我们可以看到, apache kafka包含四个方面的组件: <br>
生产者(producer), 卡夫卡中间人(kafka broker), 消费者(consumer), zookeeper.</p>

<ol>
<li>生产者 “消息的制造者”, 可以来自前端, 服务, 代理, 适配器等各种来源, 数据的输入口.</li>
<li>消费者 “消息的接收者”, 可以是输入到实时显示页面, 存储到数据库, 发送给hadoop, 存储到数据仓库等.</li>
<li>中间人 用于接收生产者生产的数据, 根据特定的规则进行存储, 提供订阅服务.</li>
<li>zookeeper 队列信息的存储. 对于中间人来说, 用于topic信息；对于消费者而言, 用于存储订阅的消息的获取情况.</li>
</ol>

<p>在上面四个组件中, zookeeper是apache的一个顶级项目, 在apache kafka中充当一个存储介质的角色. <br>
在部署apache kafka之前需要先部署apache zookeeper.</p>

<p>生产者和消费者的开发将在下文中介绍.</p>

<h3 id="生产者端">生产者端</h3>

<p>生产者端将信息根据不同的话题发送给apache kafka. <br>
消息有三个部分组成, 话题, 键, 值. <br>
其中键可以省略.</p>

<p>生产者发送的方式分为两种, 一种是同步发送, 一种是异步发送.</p>

<p>对于同步发送而言, 发送端的接口每被调用一次, 就发送一条信息. <br>
这种方式性能较差, 但是能够保证数据发送的实时性.</p>

<p>第二种是异步发送, 异步发送也分为两种模式, 一种是限时发送, 一种是限量发送. <br>
对于前者, 设置一个缓存区, 同时一个时间戳, 每个一定的时间, 将缓存区的数据发送到apache kafka. <br>
对于后者, 设置一个缓存区, 同时设置一个缓存区的大小, 当缓存区的大小达到这个阖值, 将缓存区中的数据发送到apache kafka.</p>



<h3 id="消费者端">消费者端</h3>



<h3 id="话题和日志">话题和日志</h3>

<p>话题(topic)是apache kafka中一个重要的关键词. <br>
这个关键字用于划分消息的类别. <br>
消息传递的过程如图2-2所是.</p>

<p></p><center> <br>
<img src="https://img-blog.csdn.net/20160423144949406" alt="这里写图片描述" title=""> <br>
</center> <br>
<center> <br>
图2-2 <br>
</center><p></p>

<p>生产者将需要传输的信息设置特定的话题. <br>
apache kafka将接收到的数据根据话题进行分类, 分别存储在特定的分块集中.</p>

<p>消费者根据话题进行订阅. <br>
每个订阅这个话题的组将收到这个话题的消息.</p>

<p>关于组的介绍, 将在下一部分进行详细介绍.</p>

<p>kafka将各个主题的数据存储在特定的分块集中. <br>
而在分块集中, 每一条信息通过特定的算法添加到不同的分块中, 对于每个块中的数据排列, 都是从旧到新的排列方式, <br>
存储的示意图如图所时.</p>

<p></p><center> <br>
<img src="https://img-blog.csdn.net/20160423145034516" alt="这里写图片描述" title=""> <br>
</center> <br>
<center> <br>
图2-3 <br>
</center><p></p>

<p>图中的编号为当前信息在这个分块中的编号(offset). <br>
对于每个组获得信息的情况, 信息分块情况, kafka都将其存储在zookeeper中.</p>

<h3 id="组">组</h3>

<p>apache kafka在生产者端将信息划分成多个话题(topic). <br>
在消费者端, 消费者可以进行分组, 对于订阅当前话题的组, apache kafka将向这个组发送一份消息. <br>
这个组中的一个消费者将收到这份信息. <br>
当多个组同时订阅了这个话题, 那么每个组都会收到同一份数据.</p>

<p>效果图如图所时:</p>

<p></p><center> <br>
<img src="https://img-blog.csdn.net/20160423145325083" alt="这里写图片描述" title=""> <br>
</center> <br>
<center> <br>
图2-4 <br>
</center><p></p>

<h3 id="备份">备份</h3>

<p>apache kafka提供了备份的功能, 当部署多节点时, 可以在话题声明时, 设置备份的次数, 默认情况下, 无备份.</p>

<p>当消息发送到apache kafka中的leader节点时, 如果设置了备份节点, 那么同样的数据将发送给备份节点. <br>
消费者获得信息时, 依旧从leader节点获得数据.</p>

<p>效果图如图所时:</p>

<p></p><center> <br>
<img src="https://img-blog.csdn.net/20160423145355142" alt="这里写图片描述" title=""> <br>
</center> <br>
<center> <br>
图2-5 <br>
</center><p></p>

<h2 id="部署">部署</h2>



<h3 id="单机单节点">单机单节点</h3>

<p>单机单节点的apache kafka部署的架构图如图所时:</p>

<p></p><center> <br>
<img src="https://img-blog.csdn.net/20160423145424709" alt="这里写图片描述" title=""> <br>
</center> <br>
<center> <br>
图3-1 <br>
</center><p></p>

<p>apache kafka项目的配置文件默认是设置为这种部署方式.</p>

<p>部署这种方式的apache kafka的步骤为:</p>

<p>启动zookeeper服务, 使用下面的命令:</p>

<pre><code>bin/zookeeper-server-start.sh conf/zookeeper.properties
</code></pre>

<p>启动kafka broker进程:</p>

<pre><code>bin/kafka-server-start.sh config/server.properties
</code></pre>

<h3 id="单机多节点">单机多节点</h3>

<p>单机多节点的apache kafka部署的架构图如图所是:</p>

<p></p><center> <br>
<img src="https://img-blog.csdn.net/20160423145524674" alt="这里写图片描述" title=""> <br>
</center> <br>
<center> <br>
图3-2 <br>
</center><p></p>

<p>跟单机单节点一样的方式启动zookeeper.</p>

<p>与单节点的kafka broker不同, 这次需要启动多个节点, 因此需要设置不同的broker.id, port, log.dir参数.</p>

<p>创建server-1.properties文件, 并添加下面的内容:</p>

<pre><code>broker.id=1
port=9093
log.dir=/tmp/kafka-logs-1
</code></pre>

<p>创建server-2.properties文件, 并添加下面的内容:</p>

<pre><code>broker.id=2
port=9094
log.dir=/tmp/kafka-log-2
</code></pre>

<p>分别重新启动两个broker, 命令如下:</p>

<pre><code>bin/kafka-server-start.sh config/server-1.properties
bin/kafka-server-start.sh config/server-2.properties
</code></pre>

<h3 id="多机多节点">多机多节点</h3>

<p>多机多节点的部署方式如图所是:</p>

<p></p><center> <br>
<img src="https://img-blog.csdn.net/20160423145609315" alt="这里写图片描述" title=""> <br>
</center> <br>
<center> <br>
图3-3 <br>
</center><p></p>

<p>多机多节点的kafka部署方式与单机多节点的部署方式相似. <br>
kafka broker需要连接同一个zookeeper节点. <br>
需要修改broker节点的配置文件, 将zookeeper.connect设置为同一个zookeeper即可.</p>

<h2 id="协议">协议</h2>

<p>传输解析后加</p>



<h2 id="分块划分算法">分块划分算法</h2>

<p>划分算法后加</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>