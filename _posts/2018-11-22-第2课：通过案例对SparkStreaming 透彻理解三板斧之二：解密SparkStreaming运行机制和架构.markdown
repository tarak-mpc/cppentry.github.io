---
layout:     post
title:      第2课：通过案例对SparkStreaming 透彻理解三板斧之二：解密SparkStreaming运行机制和架构
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>上节课讲到spark就是大数据领域的龙脉之所在，而spark的龙穴或关键点就是spark streaming.我们通过把batch interval调大的方式对spark streaming进行了降维，然后运行了实战程序，并通过观察知道特定的时间段里确实具体是rdd的工作。本节课在上节课的基础上主要讲解Spark streaming的运行机制和架构。 <br>
与其说spark streaming是spark core上的一个框架，不如说它是spark core上的一个应用程序，它在启动的时候运行了很多Job，这些Job包含围绕batch或window运行的具体的job,也有为了框架正常运行而启动的job,例如receiver就是通过启动一个Job运行的，而且这个job是为其他Job服务的，由此我们也得出了一个结论，要做负责的spark应用程序，往往多个job之间会进行配合。Spark streming就是一个很复杂的spark应用程序，掌握了他，任意的spark程序的编写都没有问题。 <br>
 <img src="https://img-blog.csdn.net/20160523111327708" alt="这里写图片描述" title=""> <br>
Spark刚推出的时候（2010，2011年）只有spark core,后续才陆续推出了core之上的子框架。通过对这些子框架的使用，可以洞察出spark内部的机制。我们选择了spark streaming来研究spark内部的机制，而不是其它子框架，有一个原因就是spark sql 中有太多的语法，这些和spark机制无关，浪费精力；机器学习有太多的算法，这些和spark机制无关，浪费精力;图计算也有太多的算法，这些和spark机制无关，浪费精力,而且最近图计算发展不多，所以spark streaming是最佳切入点。 <br>
 <img src="https://img-blog.csdn.net/20160523111351702" alt="这里写图片描述" title=""> <br>
Spark core中都基于rdd编程，包括后来推出dataframe,dataset也是基于rdd。Spark streaming在rdd的dag基础上加上了一个时间维度。如下Job运行log可见，很多都是spark core相关的，如MapPartitionsRDD,BlockManager,ShuffledRDD,等等；当然也有spark streaing自己的，比如JobScheduler,SocketInputDstream,ReceivedBlockTracker,InputInfoTracker,TransformedDstrea: <br>
 <img src="https://img-blog.csdn.net/20160523111453968" alt="这里写图片描述" title=""> <br>
Spark streaming底层执行的时候，基于时间的维度，不断的执行，rdd的dag依赖相当于空间维度，所以说spark streaming是时空的维度。 <br>
 <img src="https://img-blog.csdn.net/20160523111515193" alt="这里写图片描述" title=""> <br>
数据不断的流进来，根据时间划分成不同的rdd（以时间为依据划分作业的方式，是软件系统中最大的解耦合的实现），可以把它想象成x轴，是时间的维度；Rdd的依赖关系构成了业务处理逻辑，可以把它想象成y轴，是空间的维度。每个batch interval中产生的Job实例在集群中运行,就类似于在时空中万物的运动。数据不断的流进来，spark streaming以batch interval的方式把数据积攒起来，在batch interval中有很多event(如果是基于flume或kafka就能感知到event的存在), 这些event构成了数据的集合，rdd处理的时候基于固定不变的数据集合， 每个batch interval中产生的Job就基于这些event进行处理。对于连续不断的流进来的数据，（从spark streaming的角度来讲，它只考虑时间维度），由event构成了不同的batch实例，由于时间是固定的，这些event也是不变的。Rdd的dag依赖不会限定你有多少个Job。每个batch interval中的空间维度是完全一样的，只不过是时间维度的不同导致了流入数据的不同，而导致了数据量和数据内容的不同，就是不同的实例。Rdd graph脱胎于dstream的graph,后者是前者的模板。即使没有数据，job也会照常产生，照常运行。 <br>
从整个spark streaming角度考虑，由于运行在spark core上，你需要有一种机制表示rdddag的处理逻辑，即空间维度，这就是dstream graph,它就是rdd的dag的模板，静态的模板表示空间的处理逻辑；另一方面，随着时间的流逝，需要一个动态的job的控制器，在我们设定的时间间隔中用收到的数据去填充dstream graph模板，把该模板实例化，让他活起来，变成rdd graph，进而针对这个时间间隔产生的数据进行处理。 <br>
RDD的模板是DStream；Dstream是逻辑级别的，RDD是物理级别的。RDD的dag的模板是Dstream的DStreamGraph；Dstream类中随着时间的流逝，封装了rdd的集合/有一系列rdd，如下源码所示： <br>
 <img src="https://img-blog.csdn.net/20160523111640314" alt="这里写图片描述" title=""> <br>
总结：随着时间的流逝，dstream中不断产生rdd,而对dstream的操作其实就是对不断的产生的一个又一个的rdd的操作，(作用一个集合，转过来作用该集合的每一个元素。)Dstream就是对rdd封装的一个集合，对Dstream的transform的操作(对Dstream的操作由业务逻辑决定，也就是空间维度）就会转过来作用于内部封装的rdd集合的每一个rdd的操作,对rdd的操作又产生了rdd之间的依赖关系，就构成了dag的依赖关系。随着时间的流逝，batch interval产生了一个又一个rdd的dag依赖，jobscheduler根据时间调度作业，基于dag依赖关系，不断地产生作业,不断地 把作业发布到集群中去运行。 <br>
思考spark streaming就从这五点去思考: <br>
 <img src="https://img-blog.csdn.net/20160523111721491" alt="这里写图片描述" title=""></p>

<p>由于不断地产生Job交给cluster不断地去执行，若cluster不能及时消化掉，就会崩溃，所以系统的安全运行/容错就尤为重要。Spark streaming系统的设计必须考入到这些。具体job的容错就是rdd的容错，因为它就是rdd，由Spark streaming 底层的spark core 来保证；spark streaming本身作为一个框架，也有自己的容错方式，例如对于处理不完的数据进行限流，以及在处理过程中根据需要动态调整集群资源如cpu和内存。最重要的还是基于sparkcore的容错，比如checkpoint。我们即可以基于rdd进行checkpoint,也可以基于基于其他元素，应用程序本身进行checkpoint。 <br>
事务处理也至关重要。事务处理指数据一定会被处理，且只会被处理一次。事务的一个关键点就是在崩溃后仍能保证语义的一致性，即有且仅有一次。在这方面，spark streaming 做得越来越好，即使在系统崩溃后。</p>

<p>本次分享来自于王家林老师的课程‘源码版本定制发行班’，在此向王家林老师表示感谢！ <br>
欢迎大家交流技术知识！一起学习，共同进步!</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>