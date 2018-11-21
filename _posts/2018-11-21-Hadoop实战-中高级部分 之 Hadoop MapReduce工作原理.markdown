---
layout:     post
title:      Hadoop实战-中高级部分 之 Hadoop MapReduce工作原理
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div class="O" style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.1875px;background-color:rgb(239,239,239);">
<div>
<div><a href="http://sishuok.com/forum/blogPost/list/5833.html" rel="nofollow" style="color:rgb(0,102,153);">Hadoop RestFul</a></div>
<div><a href="http://sishuok.com/forum/blogPost/list/5936.html" rel="nofollow" style="color:rgb(0,102,153);">Hadoop HDFS原理1</a></div>
<div><a href="http://sishuok.com/forum/blogPost/list/5937.html" rel="nofollow" style="color:rgb(0,102,153);">Hadoop HDFS原理2</a></div>
<div><a href="http://sishuok.com/forum/blogPost/list/5938.html" rel="nofollow" style="color:rgb(0,102,153);">Hadoop作业调优参数调整及原理</a></div>
<div><a href="http://sishuok.com/forum/blogPost/list/5939.html" rel="nofollow" style="color:rgb(0,102,153);">Hadoop HA</a></div>
<div><a href="http://sishuok.com/forum/blogPost/list/5961.html" rel="nofollow" style="color:rgb(0,102,153);">Hadoop MapReduce高级编程</a></div>
<div><a href="http://sishuok.com/forum/blogPost/list/5963.html" rel="nofollow" style="color:rgb(0,102,153);">Hadoop IO</a></div>
<div><a href="http://sishuok.com/forum/blogPost/list/5965.html" rel="nofollow" style="color:rgb(0,102,153);">Hadoop MapReduce工作原理</a></div>
<div><a href="http://sishuok.com/forum/blogPost/list/5966.html" rel="nofollow" style="color:rgb(0,102,153);">Hadoop 管理</a></div>
<div><a href="http://sishuok.com/forum/blogPost/list/5969.html" rel="nofollow" style="color:rgb(0,102,153);">Hadoop 集群安装</a></div>
<div><a href="http://sishuok.com/forum/blogPost/list/5968.html" rel="nofollow" style="color:rgb(0,102,153);">Hadoop RPC</a></div>
<div> </div>
</div>
<div><span class="bold" style="font-weight:bold;">第一部分：MapReduce工作原理</span></div>
<div><span class="bold" style="font-weight:bold;"><img alt="" src="http://sishuok.com/forum/upload/2012/9/6/63230e70acbd88114f10ed272ccc81f3__1.JPG" style="border:0px;"><br></span></div>
<div><span class="bold" style="font-weight:bold;"><img alt="" src="http://sishuok.com/forum/upload/2012/9/6/f830f7039fc9363669f133b7db2e81fe__2.JPG" style="border:0px;"><br></span></div>
<div> </div>
</div>
<div style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.1875px;background-color:rgb(239,239,239);">
<div class="O"><span class="bold" style="font-weight:bold;">MapReduce 角色</span></div>
<div class="O">
<div>•Client ：作业提交发起者。</div>
<div>•JobTracker: 初始化作业，分配作业，与TaskTracker通信，协调整个作业。</div>
<div>•TaskTracker：保持JobTracker通信，在分配的数据片段上执行MapReduce任务。</div>
<div>
<div class="O"><span class="bold" style="font-weight:bold;">提交作业</span></div>
<div>
<div class="O">•在作业提交之前，需要对作业进行配置</div>
<div class="O1">•程序代码，主要是自己书写的MapReduce程序。</div>
<div class="O1">•输入输出路径</div>
<div class="O1">•其他配置，如输出压缩等。</div>
<div class="O1">•配置完成后，通过JobClinet来提交</div>
<div class="O">作业的初始化</div>
<div class="O">
<div class="O">
<div>•客户端提交完成后，JobTracker会将作业加入队列，然后进行调度，默认的调度方法是FIFO调试方式。</div>
<div>
<div class="O"><span class="bold" style="font-weight:bold;">任务的分配</span></div>
<div class="O">
<div>•TaskTracker和JobTracker之间的通信与任务的分配是通过心跳机制完成的。</div>
<div>•TaskTracker会主动向JobTracker询问是否有作业要做，如果自己可以做，那么就会申请到作业任务，这个任务可以使Map也可能是Reduce任务。</div>
<div>
<div class="O"><span class="bold" style="font-weight:bold;">任务的执行</span></div>
<div>
<div class="O">•申请到任务后，TaskTracker会做如下事情：</div>
<div class="O1">  •拷贝代码到本地</div>
<div class="O1">  •拷贝任务的信息到本地</div>
<div class="O1">  •启动JVM运行任务</div>
<div class="O1">
<div class="O"><span class="bold" style="font-weight:bold;">状态与任务的更新</span></div>
<div class="O">
<div>•任务在运行过程中，首先会将自己的状态汇报给TaskTracker，然后由TaskTracker汇总告之JobTracker。</div>
<div>•任务进度是通过计数器来实现的。</div>
<div> </div>
</div>
<div class="O"><span class="bold" style="font-weight:bold;">作业的完成</span></div>
<div class="O">
<div>•JobTracker是在接受到最后一个任务运行完成后，才会将任务标志为成功。</div>
<div>•此时会做删除中间结果等善后处理工作。</div>
<div> </div>
</div>
</div>
<div class="O">
<div class="O">
<div><span class="bold" style="font-weight:bold;">第二部分：错误处理</span></div>
<div>
<div class="O"><span class="bold" style="font-weight:bold;">任务失败</span></div>
</div>
<div>
<div class="O">
<div>•MapReduce在设计之出，就假象任务会失败，所以做了很多工作，来保证容错。</div>
<div>•一种情况:    子任务失败</div>
<div>•另一种情况：子任务的JVM突然退出</div>
<div>•任务的挂起</div>
<div>
<div class="O"><span class="bold" style="font-weight:bold;">TaskTracker失败</span></div>
<div class="O">
<div>•TaskTracker崩溃后会停止向Jobtracker发送心跳信息。</div>
<div>•Jobtracker会将该TaskTracker从等待的任务池中移除。并将该TaskTracker上的任务，移动到其他地方去重新运行。</div>
<div>•TaskTracker可以被JobTracker放入到黑名单，即使它没有失败。</div>
<div> </div>
</div>
<div class="O"><span class="bold" style="font-weight:bold;">JobTracker失败</span></div>
<div class="O">
<div>•单点故障，Hadoop新的0.23版本解决了这个问题。</div>
<div>
<div class="O">
<div><span class="bold" style="font-weight:bold;">第三部分：作业调度</span></div>
<div>
<div class="O"><span class="bold" style="font-weight:bold;">FIFO</span></div>
<div class="O">
<div>         Hadoop 中默认的调度器，它先按照作业的优先级高低，再按照到达时间的先后选 择被执行的作业</div>
<div>
<div class="O"><span class="bold" style="font-weight:bold;">公平调度器</span></div>
<div>
<div class="O2">      为任务分配资源的方法，其目的是随着时间的推移，让提交的作业获取等量的集群共享资源，让用户公平地共享集群。具体做法是：当集群上只有一个任务在运行时，它将使用整个集群，当有其他作业提交时，系统会将TaskTracker节点空间的时间片分配给这些新的作业，并保证每个任务都得到大概等量的CPU时间。</div>
<div class="O2">
<div class="O"><span class="bold" style="font-weight:bold;">容量调度器</span></div>
</div>
<div class="O"> 
<div class="O">
<div>            支持多个队列，每个队列可配置一定的资源量，每个队列采用 FIFO 调度策略，为 了防止同一个用户的作业独占队列中的资源，该调度器会对同一用户提交 的作业所 占资源量进行限定。调度时，首先按以下策略选择一个合适队列：计算每个队列中 正在运行的任务数与其应该分得的计算资源之间的比值，选择一个该比值 最小的队 列；然后按以下策略选择该队列中一个作业：按照作业优先级和提交时间顺序选择 ，同时考虑用户资源量限制和内存限制。但是不可剥夺式</div>
<div>
<div class="O"><span class="bold" style="font-weight:bold;">配置公平调度器</span></div>
</div>
<div>
<div class="O">
<div>1.修改mapred-stie.xml 加入如下内容</div>
<div>&lt;property&gt;</div>
<div>      &lt;name&gt;mapred.jobtracker.taskScheduler&lt;/name&gt;</div>
<div>      &lt;value&gt;org.apache.hadoop.mapred.FairScheduler&lt;/value&gt;</div>
<div>       &lt;/property&gt;</div>
<div>       &lt;property&gt;</div>
<div>      &lt;name&gt;mapred.fairscheduler.allocation.file&lt;/name&gt;</div>
<div>      &lt;value&gt;/opt/hadoop/conf/allocations.xml&lt;/value&gt;</div>
<div>       &lt;/property&gt;</div>
<div>        &lt;property&gt;</div>
<div>     &lt;name&gt;mapred.fairscheduler.poolnameproperty&lt;/name&gt;</div>
<div>      &lt;value&gt;pool.name&lt;/value&gt;</div>
<div>       &lt;/property&gt;</div>
<div> </div>
<div>
<div class="O">
<div>2 . 在 Hadoop conf 下创建</div>
<div>    allocations.xml</div>
<div>   内容为</div>
<div>     &lt;?xml version="1.0"?&gt;</div>
<div>        &lt;alloctions&gt;</div>
<div>        &lt;/alloctions&gt;</div>
<div>
<div class="O">
<div>样例：</div>
<div> &lt;pool name="sample_pool"&gt;</div>
<div>&lt;minMaps&gt;5&lt;/minMaps&gt;</div>
<div>&lt;minReduces&gt;5&lt;/minReduces&gt;</div>
<div>&lt;weight&gt;2.0&lt;/weight&gt;</div>
<div>&lt;/pool&gt;</div>
<div>&lt;user name="sample_user"&gt;</div>
<div>&lt;maxRunningJobs&gt;6&lt;/maxRunningJobs&gt;</div>
<div>&lt;/user&gt;</div>
<div>&lt;userMaxJobsDefault&gt;3&lt;/userMaxJobsDefault&gt;</div>
<div>
<div class="O">
<div>3. 重启 JobTracker</div>
<div>4. 访问 http://jobTracker:50030/scheduler , 查看 FariScheduler 的 UI</div>
<div>5 . 提交任务测试</div>
<div> </div>
</div>
</div>
<div> </div>
<div> 
<div class="O">
<div><span class="bold" style="font-weight:bold;">第四部分：Shuffle与排序</span></div>
<div>
<div class="O">
<div>          Mapreduce 的 map 结束后，把数据重新组织，作为 reduce 阶段的输入，该过程称 之为 shuffle--- 洗牌。</div>
<div>          而数据在 Map 与 Reduce 端都会做排序。</div>
<div>
<div class="O"><span class="bold" style="font-weight:bold;">Map</span></div>
</div>
<div>
<div class="O">
<div>• Map 的输出是由collector控制的</div>
<div>• 我们从collect函数入手</div>
<div>
<div class="O"><span class="bold" style="font-weight:bold;">Reduce</span></div>
<div class="O">
<div>•reduce的Shuffle过程，分成三个阶段：复制Map输出、排序合并、reduce处理。</div>
<div>•主要代码在reduce的 run函数</div>
<div> 
<div class="O"><span class="bold" style="font-weight:bold;">Shuffle优化</span></div>
</div>
</div>
<div>
<div class="O">•首先Hadoop的Shuffle在某些情况并不是最优的，例如，如果需要对2集合合并，那么其实排序操作时不需要的。</div>
<div class="O">•我们可以通过调整参数来优化Shuffle</div>
<div class="O1">•Map端</div>
<div class="O2">•io.sort.mb</div>
<div class="O1">•Reduce端</div>
<div class="O2">•mapred.job.reduce.input.buffer.percent</div>
<div class="O">
<div class="O">
<div> </div>
<div><span class="bold" style="font-weight:bold;">第五部分：任务的执行时的一些特有的概念</span></div>
<div>
<div class="O"><span class="bold" style="font-weight:bold;">推测式执行</span></div>
<div class="O">
<div>•每一道作业的任务都有运行时间，而由于机器的异构性，可能会会造成某些任务会比所有任务的平均运行时间要慢很多。</div>
<div>•这时MapReduce会尝试在其他机器上重启慢的任务。为了是任务快速运行完成。</div>
<div>•该属性默认是启用的。</div>
<div>  </div>
</div>
<div class="O"><span class="bold" style="font-weight:bold;">JVM重用</span></div>
</div>
<div>
<div class="O">
<div>•启动JVM是一个比较耗时的工作，所以在MapReduce中有JVM重用的机制。</div>
<div>•条件是统一个作业的任务。</div>
<div>•可以通过mapred.job.reuse.jvm.num.tasks定义重用次数，如果属性是-1那么为无限制。</div>
<div> </div>
</div>
</div>
</div>
<div class="O"><span class="bold" style="font-weight:bold;">跳过坏记录</span></div>
<div class="O">
<div>•数据的一些记录不符合规范，处理时抛出异常，MapReduce可以讲次记录标为坏记录。重启任务时会跳过该记录。</div>
<div>•默认情况下该属性是关闭的。</div>
<div>
<div class="O"><span class="bold" style="font-weight:bold;">任务执行环境</span></div>
<div>
<div class="O">•Hadoop为Map与Reduce任务提供运行环境。</div>
<div class="O">•如：Map可以知道自己的处理的文件</div>
<div class="O">•问题：多个任务可能会同时写一个文件</div>
<div class="O1">•解决办法：将输出写到任务的临时文件夹。目录为：{mapred.out. put.dir}/temp/${mapred.task.id}</div>
<div class="O"> </div>
</div>
</div>
<div> </div>
</div>
 
<div class="O">
<div class="O">
<div><span class="bold" style="font-weight:bold;">第六部分：<strong>MapReduce的类型与格式</strong></span></div>
<div>
<div class="O"><span class="bold" style="font-weight:bold;">类型</span></div>
•MapReduce的类型 使用键值对作为输入类型(key,value)
<div class="O">
<div>•输入输出的数据类型是通过输入输出的格式进行设定的。</div>
<div>
<div class="O"><span class="bold" style="font-weight:bold;">输入格式</span></div>
<div class="O">
<div>•输入分片与记录</div>
<div>•文件输入</div>
<div>•文本输入</div>
<div>•二进制输入</div>
<div>•多文件输入</div>
<div>•数据库格式的输入</div>
<div>
<div class="O"><span class="bold" style="font-weight:bold;">输入分片与记录</span><br></div>
<div class="O">
<div>•Hadoop通过InputSplit表示分片。</div>
<div>•一个分片并不是数据本身，而是对分片数据的引用。</div>
<div>•InputFormat接口负责生成分片</div>
</div>
<div><img alt="" src="http://sishuok.com/forum/upload/2012/9/6/cc24d3cf55569628f1a22c571225786f__3.JPG" style="border:0px;"></div>
<div>
<div class="O"><br><span class="bold" style="font-weight:bold;">文件输入</span></div>
<div class="O">
<div class="O">•实现类：FileInputFormat</div>
<div class="O">•通过文件作为输入源的基类。</div>
<div class="O">•四个方法：</div>
<div class="O1">•addInputPath()</div>
<div class="O1">•addInputPaths()</div>
<div class="O1">•setInputPath()</div>
<div class="O1">•setInputPaths()</div>
<div class="O1">•FileInputFormat会按HDFS块的大小来分割文件</div>
<div class="O1">
<div>
<div class="O">•避免分割</div>
<div class="O1">•继承FileInputFormat 重载isSplitable()</div>
<div class="O1">•return false</div>
<div class="O">
<div class="O"><br><span class="bold" style="font-weight:bold;">文本输入</span></div>
</div>
</div>
<div>
<div class="O">•实现类：TextInputFormat</div>
<div class="O">•TextInputFormat 是默认的输入格式。</div>
<div class="O">•包括：</div>
<div class="O1">•KeyValueTextInputFormat</div>
<div class="O1">•NLineInputFormat</div>
<div class="O1">•XML</div>
<div class="O">•输入分片与HDFS块之间的关系</div>
<div class="O1">•TextInputFormat的某一条记录可能跨块存在</div>
<div class="O">
<div class="O"><br><span class="bold" style="font-weight:bold;">二进制输入</span><br></div>
<div>
<div class="O">•实现类：SequenceFileInputFormat</div>
<div class="O">•处理二进制数据</div>
<div class="O">•包括：</div>
<div class="O1">•SequenceFileAsTextInputFormat</div>
<div class="O1">•SequenceFileAsBinaryInputFormat</div>
<div class="O1">
<div class="O"><br><span class="bold" style="font-weight:bold;">多文件输入</span><br></div>
<div>
<div class="O">•实现类：MultipleInputs</div>
<div class="O">•处理多种文件输入</div>
<div class="O">•包括：</div>
<div class="O1">•addInputPath</div>
<div class="O1">
<div class="O"><br><span class="bold" style="font-weight:bold;">数据库输入</span><br></div>
<div class="O">
<div>•实现类：DBInputFormat</div>
<div>•注意使用，因为连接过多，数据库无法承受。</div>
<div>
<div class="O"><span class="bold" style="font-weight:bold;">输出格式</span></div>
<div class="O">
<div>•文本输出</div>
<div>•二进制输出</div>
<div>•多文件输出</div>
<div>•数据库格式的输出</div>
<div><span class="bold" style="font-weight:bold;">文本输出</span></div>
<div>
<div class="O">
<div>•实现类：TextOutputFormat</div>
<div>•默认的输出方式</div>
<div>• 以 "key \t value" 的方式输出</div>
<div>
<div class="O"><span class="bold" style="font-weight:bold;">二进制输出</span><br></div>
<div class="O">
<div>•基类： <strong></strong>SequenceFileOutputFormat</div>
<div>•实现类： <strong></strong>SequenceFileAsTextOutputFormat</div>
<div>              MapFileOutputFormat</div>
<div>              SequenceFileAsBinaryOutputFormat<strong></strong></div>
<div> 
<div class="O"><span class="bold" style="font-weight:bold;">多文件输出</span><br></div>
</div>
<div>•MutipleOutputFormat
<div class="O">
<div>•MutipleOutputs</div>
<div>•两者的不同在于MutipleOutputs可以产生不同类型的输出</div>
<div>
<div class="O"><span class="bold" style="font-weight:bold;">数据库格式输出</span><br></div>
</div>
</div>
</div>
</div>
<div class="O">• <strong>实现类</strong>DBOutputFormat</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
            </div>
                </div>