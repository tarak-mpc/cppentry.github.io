---
layout:     post
title:      大数据系列之日志采集Flume（一）Flume简介，架构组成与工作流程，安装
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/u011444062/article/details/81186475				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>1.Flume简介</p>

<p>  Flume是Cloudera提供的一个高可用的，高可靠的，分布式的<span style="color:#f33b45;">海量日志采集、聚合和传输的系统</span>，Flume支持在日志系统中定制各类数据发送方，用于收集数据；同时，Flume提供对数据进行简单处理，并写到各种数据接受方（可定制）的能力。</p>

<p>  当前Flume有两个版本Flume 0.9X版本的统称Flume-og，Flume1.X版本的统称Flume-ng。由于Flume-ng经过重大重构，与Flume-og有很大不同，使用时请注意区分。</p>

<p>  Flume-og采用了多Master的方式。为了保证配置数据的一致性，Flume引入了ZooKeeper，用于保存配置数据，ZooKeeper本身可保证配置数据的一致性和高可用，另外，在配置数据发生变化时，ZooKeeper可以通知Flume Master节点。Flume Master间使用gossip协议同步数据。</p>

<p>  Flume-ng最明显的改动就是取消了集中管理配置的 Master 和 Zookeeper，变为一个纯粹的传输工具。Flume-ng另一个主要的不同点是读入数据和写出数据现在由不同的工作线程处理（称为 Runner）。 在 Flume-og 中，读入线程同样做写出工作（除了故障重试）。如果写出慢的话（不是完全失败），它将阻塞 Flume 接收数据的能力。这种异步的设计使读入线程可以顺畅的工作而无需关注下游的任何问题。</p>

<p>                       <img alt="" class="has" height="226" src="https://img-blog.csdn.net/20180724161658547?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE0NDQwNjI=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="634"></p>

<p><span style="color:#f33b45;">  Flume是收集，移动，聚合大量日志数据的服务。基于流数据的架构，用于在线日志分析。其基于事件，在生产者和消费者之间启动协调作用。提供了事务保证，确保消息一定被分发。具有的优势如下：</span></p>

<p>1. Flume可以将应用产生的数据存储到任何集中存储器中，比如HDFS,HBase</p>

<p>2. 当收集数据的速度超过将写入数据的时候，也就是当收集信息遇到峰值时，这时候收集的信息非常大，甚至超过了系统的写入数据能力，这时候，Flume会在数据生产者和数据收容器间做出调整，保证其能够在两者之间提供平稳的数据.</p>

<p>3. 提供上下文路由特征</p>

<p>4. Flume的管道是基于事务，保证了数据在传送和接收时的一致性.</p>

<p>5. Flume是可靠的，容错性高的，可升级的，易管理的,并且可定制的。</p>

<p><span style="color:#f33b45;">2.架构组成与工作流程</span></p>

<p><img alt="" class="has" height="471" src="https://img-blog.csdn.net/20180724212507224?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE0NDQwNjI=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="1147"></p>

<p>如上图所示，Flume是由一个个Agent组成的，Agent是Flume的最小组成单元，每个Agent有事由3部分组成的，分别是Source,Channel,Sink。</p>

<p>Source：主要用来接收数据，类型有多种。Source可以监听一个或者多个网络端口，用来接收数据或者从本地文件系统读取数据，每个Source<span style="color:#f33b45;">至少</span>连接一个Channel，Source可以写入<span style="color:#f33b45;">多个</span>Channel，Source的包括多种类型：netcat,exec,spooling directory source等等。</p>

<p>Channel：临时存放地，对Source中来的数据进行缓冲，直到Sink消费掉。当flume的Source读取了外部的数据后，flume会将数据存放到一个或多个Channel中，然后Channel将其缓存起来（数据可以放在内存，也可以在磁盘）等待Sink消费掉。</p>

<p>Sink:从channel提取数据存放到中央化存储。Sink主要是HDFS.,HBASE,HIVE等等。<span style="color:#f33b45;">一个Sink只能从一个Channel中获取数据。</span></p>

<p><span style="color:#f33b45;">flume的工作流程图如下所示（图片来源于网上）</span></p>

<p>          <img alt="" class="has" height="416" src="https://img-blog.csdn.net/20180724220917608?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE0NDQwNjI=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="738"></p>

<p><span style="color:#f33b45;">我们可以看到在流程中一个Source可以连接到多个Channel上，而一个Sink只能连接到一个Channel上获取数据，Sink可以直接存到中央话存储，也可以输出到另外一个Agent中，作为另外一个Agent的消息源输入带Source中。</span></p>

<p><span style="color:#f33b45;">3.Flume的</span><span style="color:#f33b45;">安装(软件包放在最后提供下载)</span></p>

<p><span style="color:#f33b45;">    1.下载<br>
    2.tar开</span></p>

<p><span style="color:#f33b45;">    3.建立符号链接</span></p>

<p><span style="color:#f33b45;">     ln -s apache-flume-1.7.0-bin  flume<br>
    4.环境变量[/etc/profile]</span></p>

<p><span style="color:#f33b45;">    #flume<br>
   export FLUME_HOME=/home/zpx/soft/flume<br>
   export PATH=$PATH:$FLUME_HOME/bin</span></p>

<p><span style="color:#f33b45;">   然后 source /etc/profile<br>
    5.验证flume是否成功<br>
        $&gt;flume-ng version            //next generation.下一代.</span></p>

<p>              <img alt="" class="has" height="102" src="https://img-blog.csdn.net/20180724222126717?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE0NDQwNjI=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="631"></p>

<p>软件包：</p>

<p>链接：https://pan.baidu.com/s/1IVsb56QI_NmjbbbyuxJKfw 密码：bxn8</p>            </div>
                </div>