---
layout:     post
title:      Spark on Yarn
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="spark-on-yarn深度剖析">Spark ON Yarn深度剖析</h1>

<ul>
<li>我们都知道Spark支持在yarn上运行，但是Spark on yarn有分为两种模式yarn-cluster和yarn-client，它们究竟有什么区别与联系？ Spark支持可插拔的集群管理模式(Standalone、Mesos以及YARN )，集群管理负责启动executor进程，编写Spark application 的人根本不需要知道Spark用的是什么集群管理。Spark支持的三种集群模式，这三种集群模式都由两个组件组成:master和slave。Master服务(YARN ResourceManager,Mesos master和Spark standalone master)决定哪些application可以运行，什么时候运行以及哪里去运行。而slave服务( YARN NodeManager, Mesos slave和Spark standalone slave)实际上运行executor进程。 <br>
当在YARN上运行Spark作业，每个Spark executor作为一个YARN容器(container)运行。Spark可以使得多个Tasks在同一个容器(container)里面运行。这是个很大的优点。 <br>
[这里有一点疑惑，Spark可以使得多个Tasks在同一个容器(container)里面运行，这算是很大的优点吗，Standalone 里面的exectutor不也是包含多个task嘛，按原理来说executor（container）是为一个application 而产生的，好了到此为止，不想了 - - ||]</li>
</ul>

<hr>

<ol>
<li>Spark提交方式中，我们最常用的一种方式是standalone模式，基于Spark自己的Master-Worker集群。</li>
<li>但是在生产环境中，一般我们不会直接提交到Spark本身自带的Master节点，而是提交到资源调度框架，比如yearn，mesos等框架</li>
<li>Yarn有分为2种，yarn-cluster和yarn-client模式</li>
<li>最后两种方式，我们只要在spark-subtmit方式中在追加—master参数，变成yarn-cluster，yarn-client。Messo可以变成mesos。如果没有任何设置，默认的就是standalone模式。</li>
<li>Yarn只有在运行完Application所有的所有任务的时候，才会释放资源，这样是会影响到其它的并行application <br>
而Mesos是边运行边释放资源</li>
</ol>

<p>备注：在实际中还是以yarn 为主，  一般我们会将spark与hadoop进行结合，将调度管理的责任同一交给yarn , 因为如果使用spark本身的调度管理框架，意味着要使用基于Spark自己的Master-Worker集群。所以与hadoop结合使用，要用yarn 管理</p>

<hr>

<p>来看看图吧</p>

<p><img src="http://i.imgur.com/ZaCGb11.png" alt="" title=""></p>



<h2 id="yarn-cluster">yarn - cluster</h2>

<ul>
<li>首先在启动脚本spark-submit的时候设置参数 –master yarn ，–deploy-mode cluster，然后向RM(ResourceManager)发出一个事件请求通知，我要运行spark，申请AM（ApplocationMaster）资源，就是向集群申请，给我分配一台机器作为AM ,来跑我的程序，所以这里的AM ，扮演的角色相当于Standalone模式下面的Driver</li>
<li>再来看看 Yarn集群，RM（ResourceManager）接收到注册申请之后就会从NM中挑一个机器创建AM（ApplicationMaster）,其实AM 就是跑在集群内某台集群上的一个Container(容器)，是以一个进程，基于AKKA 模型，</li>
<li>RM(ResourceManager) 挑选一台NM(NodeManager)，创建AM，当AM创建好之后，会向RM发出注册通知，RegisterApptiton，为AM分配资源，比如exectors,memory,core等等</li>
<li>当RM 收到AM 的注册信息，根据任务指定相应的计划，将任务分配给集群当中的NM节点，承担相应的任务，就是在NM节点上启动相应的Container【在Yarn体系里面，进程一般被称为Container】，当进程启动好了，就会反向注册到AM 上面，这个过程总结来说就是，AM 告诉 RM ，我要注册个任务，你给我资源，RM 将任务分配给集群当中的NM ，NM 收到任务后，告诉AM ，我收到RM 分配给我的任务了，这个交流的过程底层都是用 AKKA Actor来实现的，</li>
<li>当AM 接收到所有的NM 上面的executor的Container注册之后，加载并创建RDD</li>
</ul>

<hr>



<h2 id="yarn-client">yarn - client</h2>

<hr>

<ul>
<li>首先在启动脚本spark-submit脚本的时候，– master yarn,–deploy-mode client，要注意的是这种模式下，Driver会被创建在本地，</li>
<li>首先client向RM（ResourceManager）,发出一个事件注册通知，申请AM</li>
<li>RM接收到注册申请之后，就会让NM中创建AM(Application Master),并在本地创建Driver,RM根据Client提交的注册信息，进行任务分配，当NM接收到RM 分配的 任务后，创建好Container之后，会将确认信息，发给AM ，告诉AM ,我们接收到任务了，已经创建好Container</li>
<li>当AM接收到所有的NM上面的exector的Container注册之后，加载并创建RDD..</li>
</ul>



<h2 id="yarn-cluster与-yarn-client-的比较">yarn-cluster与 yarn - client 的比较</h2>

<ul>
<li>yarn-client,这种方式主要用于我们的测试，因为Driver在本地创建，在和yarn进行交互的过程中，会产生大量的网络流量，会对性能造成一定的影响，但是它却方便我们进行调试</li>
<li>yarn-cluster，主要用于生产环境下，因为driver创建，任务分配等等没有像yarn-client这种网络流量激增的问题，会有yarn集群自行来分配资源，运行在nodemanager之上进行管理调度等，但是，不方便我们进行代码测试，如果程序出现问题，我们需要对每个节点挨个查询问题，一个聪明的做饭是通过查看SparkUI ,来查看日志信息，</li>
<li>实际上yarn-client和yarn-cluster 最大的区别就在于SparkContext的创建，Driver的创建，client的SparkConext创建在本地，cluster的Driver或SparkConext就是Yarn创建的第一个Container</li>
</ul>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>