---
layout:     post
title:      HBase学习笔记 --- 调研HBase Indexer
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：学习交流为主，未经博主同意禁止转载，禁止用于商用。					https://blog.csdn.net/u012965373/article/details/54693914				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-size:32px;"><strong>简介：</strong></span></p>
<p><br></p>
<p><span><span style="font-size:18px;"><strong><a href="http://lib.csdn.net/base/hbase" rel="nofollow" class="replace_word" title="Hbase知识库" style="color:rgb(223,52,52);text-decoration:none;font-family:'microsoft yahei';">Hbase</a><span style="font-family:'microsoft yahei';"> Indexer全名为Lily
 Hbase Indexer，是NGDATA公司为了将lily子系统里面相关HBase数据存储到Solr而开发的一个软件。</span></strong></span></span></p>
<p><span><span style="font-size:18px;"><strong><span style="font-family:'microsoft yahei';">NGDATA公司将源代码开源并托管在Github上，通过以下Github地址访问Hbase-Indexer项目主页及代码： </span></strong></span></span></p>
<p><span><span style="font-family:'microsoft yahei';"><a href="https://github.com/NGDATA/hbase-indexer" rel="nofollow" style="color:rgb(202,0,0);text-decoration:none;font-family:'microsoft yahei';"><span style="font-size:18px;"><strong><span></span>https://github.com/NGDATA/hbase-indexer</strong></span></a><span style="font-size:14px;"><br></span></span></span></p>
<p><span><br></span></p>
<p><span style="font-size:32px;"><strong>工作机制图：</strong></span></p>
<p><span></span></p>
<p><span><img src="https://img-blog.csdn.net/20170123153932158?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMjk2NTM3Mw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></p>
<p></p>
<p style="font-family:'microsoft yahei';">
<span style="font-size:18px;"><strong>HBase Indexer的地位类似于HBase高可用方案里面的Slave集群，事实上HBase Indexer上每一个indexer（底层核心是SepConsumer类）都是一个伪装的、弱化了的RegionServer，负责接收SepEvent，并处理自己感兴趣的那部分数据，然后将数据存储到Solr集群上。</strong></span></p>
<p style="font-family:'microsoft yahei';">
<span><span style="font-size:18px;"><strong>为什么选择Replication而不选择Coprocessor来实现HBase Indexer？</strong></span></span></p>
<p style="font-family:'microsoft yahei';">
<span style="font-size:18px;"><strong>1）HBase Replication的处理是由RegionServer开启独立的线程去处理的，处理方式是并行且异步的，依靠这种机制来实现HBase Indexer并不会给HBase带来入侵式的代码，而且不会影响写入性能。而通过Coprocessor来实现的话会给RegionServer带来入侵式代码，假如我们实现的逻辑出现问题，很可能会影响到HBase集群的性能（Coprocessor的实现至少会影响HBase集群的写性能），严重的情况还会导致整个HBase集群挂掉。</strong></span></p>
<p style="font-family:'microsoft yahei';">
<span style="font-size:18px;"><strong>2）虽然选择Replication机制只能实现近实时的索引同步，但是这种实现方式具备很高的灵活性和可扩展性，最重要的是它对HBase集群的使用是几乎没有侵占性的，不会影响HBase集群的写性能（当然开启Replication会对HBase集群性能造成一定影响）</strong></span><br></p>
<p style="font-family:'microsoft yahei';font-size:14px;">
<br></p>
<span><br></span>
<p><span><br></span></p>
<p><span></span></p>
<h2 id="3-hbase-indexer核心-sep" style="font-family:'microsoft yahei';">
<span style="font-size:32px;">HBase Indexer核心 ——SEP</span><span style="font-weight:100;"><br></span></h2>
<div><br><span style="font-size:18px;"><br></span><strong><span style="font-size:24px;">SEP介绍</span></strong><br><br><span style="font-size:18px;"><strong><br>
SEP，即Side-Effect Processor，是实现HBase Indexer功能的核心子模块，项目源码在hbase-sep模块里面。SEP的主要功能是：监听HBase集群上发生的一切数据变化，</strong></span></div>
<div><span style="font-size:18px;"><strong>对这些变化做出处理。这里说的数据变化是记录在WAL日志里面的，所以SEP要做的就是监听WAL日志变化，并对日志变化做出响应：将这些变化封装成一个SepEvent集合，</strong></span></div>
<div><span style="font-size:18px;"><strong>交给EventListener去处理。而HBase-Indexer所做的就是实现EventListener接口（实现类为IndexingEventListener）里面的processEvents方法，在这个方法里面处理数据并</strong></span></div>
<div><span style="font-size:18px;"><strong>将数据索引到solr里面。</strong></span><br></div>
<br><p></p>
<p><span></span></p>
<p><img src="https://img-blog.csdn.net/20170123154851947?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMjk2NTM3Mw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p><br></p>
<p></p>
<p style="font-family:'microsoft yahei';">
<span style="font-size:18px;"><strong>注意，SEP并不关心WAL日志是怎么被读取的，因为这部分处理是由HBase Replication System提供的。SEP就相当于设计模式里面的监听者，而HBase Replication System则负责将事件通知给对应的监听者SEP。</strong></span></p>
<p style="font-family:'microsoft yahei';">
<span style="font-size:18px;"><strong>如果我们将HBase-Indexer部署到多台机器上面，那么当需要进行replication的表数据变动的时候，HBase Replication System每次都会随机选择一台HBase Indexer作为接收方来发送通知（SepEvent），前提是在hbase-site.xml（HBase集群配置）里面配置了replication.source.ratio = 1。HBase Replication确保重放WAL日志是顺序的，以此来保证存储到Solr集群的数据跟HBase最新的数据是一致的。但是，对于SEP，其内部会采用多线程来处理（消费）这些WAL日志，以提高效率。（我们可以把这种模式想象成生产者消费者模式，其中SEP对应于消费者，而消费者为了提高处理效率采用了多线程方式来处理事件）</strong></span><br></p>
<p style="font-family:'microsoft yahei';font-size:14px;">
</p>
<span style="font-size:32px;"><strong>SepConsumer</strong></span><br><br><span style="font-size:18px;"><strong><br>
实现SEP功能的主类是SepConsumer。SepConsumer负责与HBase集群上的RegionServer通讯并接收WAL日志事件，使用内部线程池对日志事件进行处理，真正的处理需要由用户自定义。用户要实现WAL日志事件的处理只需要实现EventListener接口，在processEvents方法里面进行处理。HBase-Indexer所做的就是实现EventListener接口（实现类为IndexingEventListener）里面的processEvents方法，在这个方法里面处理数据并将数据索引到solr里面。</strong></span><br><p><br></p>
<p></p>
<h4 id="321-sepconsumer线程模型" style="font-family:'microsoft yahei';">
<span style="font-size:32px;">SepConsumer线程模型</span></h4>
<div><br></div>
<div><img src="https://img-blog.csdn.net/20170123155231027?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMjk2NTM3Mw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></div>
<div><br></div>
<div><br></div>
<div><span style="font-size:18px;"><strong><span style="font-family:'microsoft yahei';">一个SepConsumer主要包括与HBase集群通讯的RpcServer和处理SepEvents的ThreadPoolExecutors，其中RpcServer会消耗大概20个线程左右，而处理SepEvents的线程池大小是可控制的，由</span><code style="font-family:'Source Code Pro', monospace;color:rgb(63,63,63);">hbaseindexer.indexer.threads</code><span style="font-family:'microsoft yahei';">参数决定，默认为10个，这里的10个是指10个线程池，但是每个线程池只使用1个工作线程，并且工作队列使用</span><code style="font-family:'Source Code Pro', monospace;color:rgb(63,63,63);">ArrayBlockingQueue</code><span style="font-family:'microsoft yahei';">，容量为100，RejectedPolicy选择WaitPolicy。因此，当线程池里面的工作队列满了，后面的作业将继续等待。</span><span style="font-family:'microsoft yahei';">总的来说，一个SepConsumer将使用将近30个线程，可以看出SepConsumer对于线程资源的要求是挺高的。</span><br></strong></span></div>
<div><strong style="font-size:32px;"><br></strong></div>
<div><strong style="font-size:32px;">EventListener</strong></div>
<br><span style="font-size:18px;"><strong><br>
SepConsumer将SepEvent委托给EventListener的实现类进行处理，EventListener接口仅仅定义了一个processEvents方法。而Hbase Indexer正是通过实现EventListener接口（实现类为IndexingEventListener）的processEvents方法来处理数据映射并同步到Solr。</strong></span><br><br><br><p></p>
<p><span style="font-size:32px;"><strong> 主要流程分析</strong></span><br><br><br><span style="font-size:18px;"><strong>HBase Indexer入口分析<br><br><br>
HBase Indexer的入口在hbase-indexer-server模块的Main类里面，入口实现的动作有：<br><br><br>
1） 使用后台线程方式启动hbase-indexer <br>
2） 创建配置信息，主要包括hbase以及hbase-indexer的配置信息 <br>
3） 如果配置了Ganglia进行性能监控，那么启动该监控 <br>
4） 处理IndexerMaster的选举和启动，接下来由IndexerSupervisor启动所有注册了的Indexer，然后保存所有成功启动的Indexer的注册信息，最后使用Jetty启动http服务（主要是为了将indexer注册信息发布成REST服务）。<br><br><br>
注意，所有成功启动的Indexer将伪装成一台Slave RegionServer，去监听HBase RegionServer集群所产生的数据变化（WAL日志）。<br><br><br>
IndexerMaster启动流程<br><br><br>
一个HBase Indexer集群只能有一台HBase Indexer竞选为master（IndexerMaster）。IndexerMaster的主要功能有：管理并维护集群上indexer的增加、更新、删除，协调MapReduce作业的运行。（主要是通过MapReduce来进行HBase数据的全量索引）<br><br><br>
IndexerMaster的启动流程主要就是竞选master的过程，竞选成功的那台HBase Indexer将启动EventWork线程负责监听集群上indexer事件，包括增加、更新、删除以及MapReduce作业的提交。而竞选不到的master就不负责这些事情，但是会等待下一次竞选的到来。（防止已有的master挂掉，再次参与选举）<br><br><br>
增删改Indexer处理流程<br><br><br>
前面提到增、删、改Indexer最终是由竞选为master的IndexerMaster负责处理的，具体逻辑在内部类EventWorker的实现里面。IndexerMaster上的EventWorker会使用一个阻塞队列接收IndexerModelEvent（代表Indexer增删改事件），根据这些事件的类型（INDEXER_ADDED INDEXER_UPDATED INDEXER_DELETED）作出相应处理。<br><br><br>
那么IndexerModelEvent是怎么来的呢？实际上，我们发出的增删改Indexer操作首先会在zookeeper上创建相应的路径和节点。而IndexModelImpl的IndexModelChangeWatcher会监听这些节点的变化，从而唤醒IndexerCacheRefresher线程触发刷新事件，IndexerCacheRefresher会把节点变化封装成IndexerModelEvent并交给IndexerModelListener进行处理。而IndexerModelListener最终将事件委托给IndexerMaster的EventWorker去处理。<br><br><br>
处理流程如下图所示：<br></strong></span></p>
<p><br></p>
<p><img src="https://img-blog.csdn.net/20170123155338559?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMjk2NTM3Mw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p><br></p>
<p><span style="font-size:18px;"><strong>WAL事件处理流程<br><br><br>
前面提到，Indexer实际上是一个伪装的Slave RegionServer，负责监听来自HBase RegionServer产生的数据变</strong></span></p>
<p><span style="font-size:18px;"><strong>化。Indexer的底层核心实现是SepConsumer类，HBase RegionServer对于产生的数据变化，会随机选择一台</strong></span></p>
<p><span style="font-size:18px;"><strong>HBase Indexer进行WAL日志的发送。那么将会通知这台HBase Indexer上的所有Indexer，最终调用</strong></span></p>
<p><span style="font-size:18px;"><strong>SepConsumer类的replicateLogEntries方法。（注意高版本的HBase这个方法名有变化）因此WAL事件处理就集</strong></span></p>
<p><span style="font-size:18px;"><strong>中在SepConsumer类的replicateLogEntries方法里面。这个方法完成的动作主要是：将接收到的WAL日志封装成</strong></span></p>
<p><span style="font-size:18px;"><strong>SepEvent，传递给SepEventExecutor进行处理，当累计到一定的数据量，进行一次batch操作。</strong></span></p>
<p><span style="font-size:18px;"><strong>（SepEventExecutor.scheduleSepEvent方法的调用）最终对SepEvent事件的处理将委托给</strong></span></p>
<p><span style="font-size:18px;"><strong>EventListener.processEvents方法进行处理。<br><br><br>
注意，EventListener是构造SepConsumer必须传递的对象，HBase Indexer也正是实现了EventListener接口来自定义对SepEvent的处理：将数据经过映射处理后索引到Solr里面。实现了EventListener的类是IndexingEventListener。</strong></span><br><br><br>
处理流程如下图所示：<br></p>
<p><br></p>
<p><br></p>
<p><img src="https://img-blog.csdn.net/20170123155459368?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMjk2NTM3Mw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p><br></p>
<p><span style="font-size:32px;"><strong>HBase Indexer评估</strong></span></p>
<p><br><br><span style="font-size:18px;"><strong>经过对HBase Indexer重要模块源码的初步了解以及实际的案例测试，可以发现HBase Indexer的使用代价比较高，主要体现在：<br><br><br>
1）HBase RegionServer选择一个HBase Indexer节点进行WAL日志重放的时候，该HBase Indexer上所有的Indexer（SepConsumer）都会同时监听到这些日志，然后再从这些日志过滤出符合自己所要索引的那部分数据进行处理。所以，如果我们创建的indexer非常多的话，很多情况下大部分indexer都没有捕获到自己感兴趣的日志，那么会产生比较大的性能损耗。<br><br><br>
2）每一个Indexer（SepConsumer）将会占用至少30个线程，包括与HBase RegionServer通讯的RPC线程，Indexer处理日志的线程等。因此如果我们创建的indexer比较多并且很多indexer同时在线的情况下，对资源的占用可想而知是多么大。其中最主要的资源是CPU和内存。<br><br><br>
3）我们在测试案例中发现如果有大数据量的数据导入HBase或者大量的数据从HBase中被删除，那么会引起HBase Indexer集群一直处于繁忙状态，那么这个时候Solr的数据同步可能存在较大的延迟。综合1）和2）我们不难发现这一点。<br><br><br>
综合以上情况考虑，在设备允许的情况下，HBase Indexer应该部署在单独的集群上面，与HBase、Solr等集群隔离开，避免资源竞争明显的现象。</strong></span><br></p>
<p><span style="font-size:18px;"><strong><br></strong></span></p>
<p><span style="font-size:18px;"><strong>参考：</strong></span></p>
<p><span style="font-size:18px;"><strong>    对于上述的总结，根据以下资料，感谢下面的作者：</strong></span></p>
<p><span style="font-size:18px;"><strong>https://github.com/NGDATA/hbase-indexer/wiki<br></strong></span></p>
<p><span style="font-size:18px;"><strong>http://www.2cto.com/database/201612/570655.html<br></strong></span></p>
<p><span style="font-size:18px;"><strong>http://blog.csdn.net/d6619309/article/details/51500368<br></strong></span></p>
<p><br></p>
<p><br></p>
            </div>
                </div>