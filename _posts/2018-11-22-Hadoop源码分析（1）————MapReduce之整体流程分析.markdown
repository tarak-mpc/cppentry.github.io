---
layout:     post
title:      Hadoop源码分析（1）————MapReduce之整体流程分析
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/qq_33771258/article/details/79811098				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="欢迎来到何大鹰的hadoop理解内容">欢迎来到何大鹰的Hadoop理解内容</h1>

<p>由于Hadoop已经火了有一段时间啦~，所以编译hadoop和部署hadoop的内容在这里我就不多讲了，编译hadoop大家可以看看这篇博客<a href="https://blog.csdn.net/cruise_h/article/details/18709969" rel="nofollow">https://blog.csdn.net/cruise_h/article/details/18709969</a> <br>
至于部署hadoop大家完全可以按照官网上的文档慢慢配置，还可以练习一下英文阅读能力。 <br>
运行和编译hadoop的环境我还是比较推荐ubuntu。</p>

<hr>

<ul>
<li><strong>然后来到正文内容</strong></li>
</ul>

<p>像平时一样，我们还是通过hadoop最最普遍的程序————wordcount来当做我们的切入点，当你成功配置好了一个hadoop环境之后（我们这里以伪分布式或者分布式来当做典型，单机模式我们就不提了），我们设置好测试数据，然后满怀激动的启动了wordcount，hadoop就会经历大概以下几步。</p>

<ul>
<li><p><strong>根据jobconf启动job</strong> <br>
job是hadoop的作业，一个hadoop理论上可以启动多个job，也就是hadoop的作业。在目前这种情况下，也就是wordcount这个作业。</p></li>
<li><p><strong>job启动task</strong> <br>
job被成功启动后，job就会去启动Task，也就是任务，但每个job究竟是启动多少个任务呢，据官方说法，数据量每达到一个128M，就会启动一个task，也就是说，具体启动多少个task是根据所计算的数据量决定的。~JOB会把输入数据分割成几个部分，然后分给被启动的task。</p></li>
<li><p><strong>job启动task</strong> <br>
每一个task都有两个部分 <br>
（1）MapTask <br>
MapTask类继承与Task类 <br>
MapTask一般主要有五项工作</p>

<h5 id="read通过recordreader从inputsplit分片中将数据解析成一个个keyvalue">read：通过RecordReader从InputSplit分片中将数据解析成一个个key/value。</h5>

<h5 id="map将由recordreader解析出的keyvalue交给map方法处理此处所说的是用户自己编写的map函数经历这个函数可以让数据生成一个个新的符合用户需求的keyvalue">map：将由RecordReader解析出的key/value交给map()方法处理，此处所说的是用户自己编写的map函数，经历这个函数可以让数据生成一个个新的符合用户需求的key/value。</h5>

<h5 id="collect将map中新生成keyvalue由outpcollectorcollect写入内存中的环形数据缓冲区">collect：将map()中新生成key/value由OutpCollector.collect()写入内存中的环形数据缓冲区。</h5>

<h5 id="spill当环形缓冲区达到一定阀值后会将数据写到本地磁盘上生成一个spill文件在写文件之前会先将数据进行一次本地排序也就是merge函数必要的时候按配置要求还会对数据进行压缩">spill：当环形缓冲区达到一定阀值后，会将数据写到本地磁盘上，生成一个spill文件。在写文件之前，会先将数据进行一次本地排序也就是merge函数，必要的时候(按配置要求)还会对数据进行压缩。</h5>

<h5 id="combine当所有数据处理完后将所有的临时的spill文件进行一次合并最终之生成一个数据文件这一步是用户可选的如果不主动调用合并的话是不会被执行的">combine：当所有数据处理完后，将所有的临时的spill文件进行一次合并，最终之生成一个数据文件。这一步是用户可选的，如果不主动调用合并的话是不会被执行的。</h5>

<p>（2）ReduceTask <br>
ReduceTask的主要过程总体来说相对比较简单，但是细节部分非常精细，其中的Shuffler过程，更是常常被称为奇迹发生的地方。在shuffler中有两个线程比较重要。这两个线程是并行运行的。 <br>
1、EventFetcher <br>
由于Hadoop的架构非常庞大，如果还是仅仅依靠像小架构那样的通过流程控制来掌控整个程序运行的走向，是非常不现实的。大部分庞大架构的软件系统都是通过状态机来控制的，而根据我们对于MapReduce的理解，Reduce是在Map将结果写入到磁盘后也就是Map任务完成后开始启动的。理所应当，当前这个线程的主要任务就是，在MapReduce的事件队列中取到所有关于MapTask执行完成的事件， <br>
并且从事件中剥离出相关信息，把已经确认执行无差错完成的Map节点的信息放入一个list结构中。 <br>
2、Fetcher <br>
这个线程的任务是基于EventFetcher线程的，它会通过一个随机函数取出list中的一个Map节点的信息然后通过剥离出有用的信息，在相应的Map节点中将自己的数据取到自己所在节点上。 <br>
以上两个线程完成后，就是shuffler的主要部分了，通过不断地merge等操作从而得出真正的结果并写入HDFS，由于我目前的任务不涉及到Reduce的merge以及以后的工作，所以这里就不再赘述。上述所提到的内容的具体的源码部分，会在以后的博客中慢慢更新。</p></li>
<li><p><strong>task启动attempt</strong> <br>
然后的就是我自己的一些理解，我也无法确认我理解的正确性，如有错误，欢迎大家指正，谢谢。 <br>
我想说的是，在我的理解中，每个Task并不是只有一个进程会真正执行，而是拥有很多个，每个task会以不同的方式启动多个执行策略，可能有推测执行，尝试执行，等等执行策略，当哪个执行策略以一个最快的速度正确的执行完成后，task则确认这个正确数据就是Task最后的结果，而其他的执行策略可能会被卸载掉，</p></li>
</ul>

<p>用图来说可能大概就是这个意思 <br>
<img src="https://img-blog.csdn.net/20180403234801500?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMzM3NzEyNTg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p>目前我所阅读的源码大概包含了以上的流程，后面会通过状态机，Spill ，发送时间的通信协议，EventFetcher ，Fetcher等作为切入点说一说源码的事情。</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>