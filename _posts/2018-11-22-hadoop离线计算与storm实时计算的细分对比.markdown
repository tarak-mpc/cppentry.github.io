---
layout:     post
title:      hadoop离线计算与storm实时计算的细分对比
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h3>一、资源调度对比</h3>

<p>Hadoop MapReduceV2(Yarn) 框架</p>

<p>         Hadoop从 0.23.0 版本开始，Hadoop 的 MapReduce 框架完全重构，发生了根本的变化。新的 Hadoop MapReduce 框架命名为 MapReduceV2 或者叫 Yarn。新旧Hadoop计算框架变化参考链接：</p>

<p><a href="https://www.ibm.com/developerworks/cn/opensource/os-cn-hadoop-yarn/" rel="nofollow">https://www.ibm.com/developerworks/cn/opensource/os-cn-hadoop-yarn/</a></p>

<p><span style="color:#000000;">         Apache Hadoop </span><span style="color:#3399ea;"><strong>YARN</strong> </span><span style="color:#000000;">（Yet Another Resource Negotiator，另一种资源协调者）<br>
 yarn 理解为相当于一个分布式的操作系统平台，而 mapreduce 等运算程序则相当于运行于操作系统之上的应用程序，Yarn 为这些程序提供运算所需的资源（内存、cpu）</span></p>

<table cellspacing="0"><tbody><tr><td style="text-align:center;vertical-align:middle;width:108pt;"> </td>
			<td style="text-align:left;vertical-align:middle;width:374pt;"><strong><span style="color:#3399ea;">YARN</span></strong><span style="color:#000000;">（作业调度和集群资源管理的框架）：解决资源任务调度</span></td>
			<td style="text-align:left;vertical-align:middle;width:331pt;"><strong><span style="color:#86ca5e;">storm</span></strong></td>
		</tr><tr><td style="text-align:center;vertical-align:middle;width:108pt;">任务接收</td>
			<td style="text-align:left;vertical-align:middle;width:374pt;"><span style="color:#3399ea;"><strong>ResourceManager</strong> </span>负责所有资源的监控、分配和管理 , 其中<span>包含</span><span style="color:#3399ea;"><strong>YARN Scheduler</strong></span><span style="color:#000000;"><strong> </strong></span></td>
			<td style="text-align:left;vertical-align:middle;width:331pt;"><span style="color:#86ca5e;"><strong>Nimbus</strong></span>：负责资源分配和任务调度。<br>
			1. 获取zk中空闲的worker(按topology中conf指定数量)<br>
			2.根据需要分配节点信息<br>
			3.上传任务到zk</td>
		</tr><tr><td style="text-align:center;vertical-align:middle;width:108pt;"> </td>
			<td style="text-align:left;vertical-align:middle;"><span style="color:#3399ea;"><strong>YARN Scheduler</strong></span><span style="color:#000000;"> 根据 application 的请求为其分配资源，不负责 application job 的监控、追踪、运行状态反馈、启动等工作。</span></td>
			<td style="text-align:left;vertical-align:middle;"><span style="color:#86ca5e;"><strong>zookeeper</strong></span></td>
		</tr><tr><td style="text-align:center;vertical-align:middle;width:108pt;">接受任务</td>
			<td style="text-align:left;vertical-align:middle;width:374pt;"><span style="color:#3399ea;"><strong>NodeManager</strong> </span>负责每一个节点的维护以及该节点资源的管理和监控,以心跳的方式向 ResourceManager 汇报资源使用情况.NodeManager 接收并处理来自 ApplicationMaster 的 Container 启动、停止等各种请求。</td>
			<td style="text-align:left;vertical-align:middle;width:331pt;"><span style="color:#86ca5e;"><strong>Supervisor</strong></span>：负责接受 nimbus 分配的任务，启动和停止属于自己管理的 worker 进程。 </td>
		</tr><tr><td colspan="1" rowspan="2" style="text-align:center;vertical-align:middle;width:108pt;">资源</td>
			<td style="text-align:left;vertical-align:middle;width:374pt;"><span style="color:#000000;">对于所有的 applications，RM 拥有绝对的控制权和对资源的分配权。而每个 AM 则会和RM 协商资源(container)，同时和 NodeManager 通信来执行和监控 task。</span></td>
			<td style="text-align:left;vertical-align:middle;width:331pt;"><span style="color:#86ca5e;"><strong>Worker</strong></span>：运行具体处理组件逻辑的进程。<br>
			Task：worker 中每一个 spout/bolt 的线程称为一个 task</td>
		</tr><tr><td style="text-align:left;vertical-align:middle;width:374pt;"><span style="color:#3399ea;"><strong>Container </strong></span>: 申请成功的container由AM进行初始化,container运行期间,container通过RPC协议向对应AM汇报自己的进度和状态</td>
			<td style="text-align:left;vertical-align:middle;width:331pt;"><span style="color:#86ca5e;"><strong>executor</strong> </span>: 同一个 spout/bolt 的 task 可能会共享一个物理线程，该线程称为 executor</td>
		</tr><tr><td style="text-align:center;vertical-align:middle;"><span style="color:#000000;">执行程序命令</span></td>
			<td style="text-align:left;vertical-align:middle;"><span style="color:#000000;">hadoop jar hadoop-mapreduce-examples-2.7.4.jar</span></td>
			<td style="text-align:left;vertical-align:middle;width:331pt;"><span style="color:#000000;">bin/storm jar xxxxx.jar storm.starter.WordCountTopology wordcount</span></td>
		</tr></tbody></table><h3>二、计算框架细节对比</h3>

<p>        storm的集群表面上和Hadoop集群非常像。但Hadoop上运行的Mapreduce的作业（job），而在storm上运行的是Topology。Storm和Hadoop的一个关键区别就是Hadoop的Mapreduce作业最终会终止，而Storm的Topology会一直运行（除非显示地杀掉它）</p>

<p> </p>

<table border="1" cellpadding="1" cellspacing="1"><tbody><tr><td rowspan="1" style="width:90px;"> </td>
			<td colspan="1" rowspan="1" style="width:412px;"><strong><span style="color:#3399ea;">MAPREDUCE</span></strong>（分布式运算编程框架）：解决海量数据计算</td>
			<td colspan="1" rowspan="1" style="width:295px;"><span style="color:#86ca5e;"><strong>storm</strong></span>编程模型</td>
			<td rowspan="1" style="width:85px;">差异</td>
		</tr><tr><td style="width:90px;"> </td>
			<td style="width:412px;">
			<p><span style="color:#3399ea;"><strong>ApplicationMaster </strong></span>: 读取job.split , 负责每一个具体应用程序（map或reduce任务）的调度和协调。每 个 应 用 程 序 均 包 含 一 个ApplicationMaster（实现APPMaster接口）,负责与 RM 调度器协商以获取资源（用Container 表示）将得到的任务进一步分配给内部的任务(资源的二次分配)，从而决定yarnchild的动态产生。</p>

			<p>应用运行结束后,AM向RM注销自己,并允许container收回.</p>
			</td>
			<td style="width:295px;"><strong><span style="color:#86ca5e;">Topology </span></strong>:<br>
			Storm 中运行的一个实时应用程序，因为各个组件间的消息流动形成逻辑上的一个拓扑结构</td>
			<td style="width:85px;"> </td>
		</tr><tr><td style="width:90px;">数据获取阶段</td>
			<td style="width:412px;">
			<p><span style="color:#3399ea;"><strong>InputFormat</strong></span>（默认 TextInputFormat）会通过 getSplits方法对输入目录中文件进行逻辑切片(128M)规划得到 splits，有多少个 split 就对应启动多少个 MapTask</p>

			<p>(逻辑切片发生在客户端,在job提交之前完成切片规划,规划完成后生成文件job.split)<br>
			当客户端提交mr job时,会把job.split、xxx.jar、job.xml三个文件提交到yarn指定位置.</p>
			</td>
			<td style="width:295px;"><strong><span style="color:#86ca5e;">spout </span></strong>: 通常情况下 spout 会从外部数据源中读取数据，然后转换为 topology 内部的源数据。Spout 是一个主动的角色，其接口中有个 nextTuple()函数，storm 框架会不停地调用此函数</td>
			<td style="width:85px;"> </td>
		</tr><tr><td style="width:90px;"> </td>
			<td style="width:412px;"><strong><span style="color:#3399ea;">map</span></strong></td>
			<td style="width:295px;"><span style="color:#86ca5e;"><strong>bolt </strong></span>: 在一个 topology 中接受数据然后执行处理的组件。Bolt 可以执行过滤、函数操作、合并、写数据库等任何操作。Bolt 是一个被动的角色，其接口中有个 execute(Tuple input)函数,在接受到消息后会调用此函数</td>
			<td style="width:85px;">
			<p>bolt可无限连，相当于map和reduce</p>

			<p> </p>
			</td>
		</tr><tr><td colspan="1" rowspan="2" style="width:90px;"><span style="color:#3399ea;"><strong>Shuffle阶段描述</strong></span></td>
			<td style="width:412px;"><strong><span style="color:#3399ea;">outputCollect </span></strong>: map 逻辑完之后，将 map 的每条结果通过 context.write 进行 collect 数据收集。在 collect 中，会先对其进行分区处理，默认使用 HashPartitioner。<br><strong><span style="color:#3399ea;">环形缓冲区 </span></strong>: 默认100M,溢出比0:8,达到阈值（buffer size * spill percent = 100MB * 0.8 = 80MB），溢写线程启动<br><strong><span style="color:#3399ea;">spill溢出</span></strong> : 溢写线程启动后，需要对这 80MB 空间内的 key 做排序(Sort),如果 job 设置过 Combiner，那么现在就是使用 Combiner 的时候了<br><strong><span style="color:#3399ea;">merge合并</span></strong> :当整个数据处理结束之后开始对磁盘中的临时文件进行merge 合并，因为最终的文件只有一个，写入磁盘，并且为这个文件提供了一个索引文件，以记录每个 reduce 对应数据的偏移量</td>
			<td style="width:295px;">
			<p><strong><span style="color:#86ca5e;">Stream grouping</span></strong>(分组策略)：即消息的partition方法</p>
			1. Shuffle grouping<br>
			2. Fields grouping<br>
			3. Partial Key grouping<br>
			4. All grouping<br>
			5. Global grouping<br>
			6. None grouping<br>
			7. Direct grouping<br>
			8. Local or shuffle grouping</td>
			<td colspan="1" rowspan="3" style="width:85px;">storm其他概念</td>
		</tr><tr><td style="width:412px;"><span style="color:#3399ea;"><strong>Copy 阶段</strong></span>:简单地拉取数据。Reduce 进程启动一些数据 copy 线程(Fetcher)，通过 HTTP 方式请求 maptask 获取属于自己的文件<br><span style="color:#3399ea;"><strong>Merge 阶段</strong></span> : Copy 过来的数据会先放入内存缓冲区中，这里的缓冲区大小要比 map 端的更为灵活<br><span style="color:#3399ea;"><strong>Sort 阶段</strong></span>:在对数据进行合并的同时，会进行排序操作，由于 MapTask阶段已经对数据进行了局部的排序，ReduceTask 只需保证 Copy 的数据的最终整体有效性</td>
			<td style="width:295px;"><span style="color:#86ca5e;"><strong>Tuple</strong></span>：一次消息传递的基本单元。本来应该是一个 key-value 的 map，但是由于各个组件间传递的 tuple 的字段名称已经事先定义好，所以 tuple 中只要按序填入各个 value 就行了，所以就是一个 value list.</td>
		</tr><tr><td style="width:90px;"> </td>
			<td style="width:412px;"><strong><span style="color:#3399ea;">reduce</span></strong></td>
			<td style="width:295px;"><span style="color:#86ca5e;"><strong>Stream</strong></span>：源源不断传递的 tuple 就组成了 stream。</td>
		</tr></tbody></table><p> </p>            </div>
                </div>