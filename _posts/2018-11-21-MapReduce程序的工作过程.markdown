---
layout:     post
title:      MapReduce程序的工作过程
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-size:18px;">自己对MR程序（特指Hadoop 1.x版本）的工作过程一直都不是很清楚，现在重点总结一下，为MR编程打好基础。由</span><span style="font-size:18px;">于</span><span style="font-size:18px;">MapReduce是基于HDFS的操作，因此要想深入理解MapReduce（解决的是分布式计算问题），首先得深入理解</span><span style="font-size:18px;">HDFS（解决的是分布式存储问题）。</span></p>
<p><span style="font-size:18px;">一. HDFS框架组成<br></span></p>
<p style="text-align:center;"><span style="font-size:18px;"><img src="https://img-blog.csdn.net/20170730233741751" alt=""><br></span></p>
<p><span style="font-size:18px;">HDFS采用master/slaver的主从架构，一个HDFS集群包括一个NameNode节点（主节点）和多个DataNode节点（从</span><span style="font-size:18px;">节点），并提供应用程序的访问接口。NameNode，DataNode和Client的解释，如下所示：</span></p>
<p><span style="font-size:18px;">（1）NameNode负责文件系统名字空间的管理与维护，同时负责客户端文件操作（比如打开，关闭，重命名文件或</span><span style="font-size:18px;">目录</span><span style="font-size:18px;">等）的控制及具体存储任务的管理与分配（比如确定数据块到具体DataNode节点的映射等）；</span></p>
<p><span style="font-size:18px;">（2）DataNode负责处理文件系统客户端的读写请求，提供真实文件数据的存储服务；</span></p>
<p><span style="font-size:18px;">（3）Client是客户端，一般指的是访问HDFS接口的应用程序，或者HDFS的Web服务（就是让用户通过浏览器来查</span><span style="font-size:18px;">看</span><span style="font-size:18px;">HDFS的运行状况）等。</span></p>
<p><span style="font-size:18px;">1. 文件的读取<br></span></p>
<p><span style="font-size:18px;">Client与之交互的HDFS、NameNode、DataNode文件的读取流程，如下所示：<br></span></p>
<p style="text-align:center;"><span style="font-size:18px;"><img src="https://img-blog.csdn.net/20170730234122158" alt=""><br></span></p>
<p><span style="font-size:18px;">（1）Client向远程的NameNode发起RPC请求；</span></p>
<p><span style="font-size:18px;">（2）NameNode会返回文件的部分或者全部Block列表，对于每个Block，NameNode都会返回该Block副本的</span></p>
<p><span style="font-size:18px;">DataNode地址；</span></p>
<p><span style="font-size:18px;">（3）Client选择与其最接近DataNode来读取Block，如果Client本身是DataNode，那么将从本地直接读取数</span><span style="font-size:18px;">据；</span></p>
<p><span style="font-size:18px;">（4）读完当前Block后，关闭与当前的DataNode连接，并为读取下一个Block寻找最近的DataNode；</span></p>
<p><span style="font-size:18px;">（5）读完Block列表后，并且文件读取还没有结束，Client会继续向NameNode获取下一批Block列表；</span></p>
<p><span style="font-size:18px;">（6）读完一个Block都会进行Cheeksum验证，如果读取DataNode时出现错误，Client会通知NameNode，然后从该</span><span style="font-size:18px;">Block的另外一个最近邻DataNode继续读取数据。Client读取数据完毕之后，关闭数据流。</span></p>
<p><span style="font-size:18px;">2. 文件的写入</span><br></p>
<p><span style="font-size:18px;">Client与之交互的HDFS、NameNode、DataNode文件的写入流程，如下所示：<br></span></p>
<p style="text-align:center;"><span style="font-size:18px;"><img src="https://img-blog.csdn.net/20170730234610963" alt=""><br></span></p>
<p><span style="font-size:18px;">（1）Client向远程的NameNode发起RPC请求；</span></p>
<p><span style="font-size:18px;">（2）NameNode便会检查要创建的文件是否已经存在，创建者是否有权限进行操作等，如果满足相关条件，就会创</span><span style="font-size:18px;">建文件，否则会让Client抛出异常；</span></p>
<p><span style="font-size:18px;">（3）在Client开始写入文件的时候，开发库（即DFSOutputStream）会将文件切分成一个个的数据包，并写入”数据</span><span style="font-size:18px;">队列“，然后向NameNode申请新的Block，从而得到用来存储复本（默认为3）的合适的DataNode列表，每个列表的</span><span style="font-size:18px;">大小根据NameNode中对replication的设置而定；</span></p>
<p><span style="font-size:18px;">（4）首先把一个数据包以流的方式写入第一个DataNode，其次将其传递给在此管线中的下一个DataNode，然后直</span><span style="font-size:18px;">到最后一个DataNode，这种写数据的方式呈流水线的形式；（假设复本为3，那么管线由3个DataNode节点构成，即</span><span style="font-size:18px;">Pipeline of datanodes）</span></p>
<p><span style="font-size:18px;">（5）当最后一个DataNode完成之后，就会返回一个确认包，在管线里传递至Client，开发库（即</span></p>
<p><span style="font-size:18px;">DFSOutputStream）也维护着一个”确认队列”，当成功收到DataNode发回确认包后便从“确认队列”中删除相应的</span><span style="font-size:18px;">包；</span></p>
<p><span style="font-size:18px;">（6）如果某个DataNode出现了故障，那么DataNode就会从当前的管线中删除，剩下的Block会继续在余下的</span></p>
<p><span style="font-size:18px;">DataNode中以管线的形式传播，同时NameNode会再分配一个新的DataNode，以保持replication设定的数量。Client</span><span style="font-size:18px;">写入数据完毕之后，关闭数据流。</span></p>
<p><span style="font-size:18px;">说明：HDFS默认Block的大小为64M，提供SequenceFile和MapFile二种类型的文件。<br></span></p>
<p><span style="font-size:18px;"><br></span></p>
<p><span style="font-size:18px;">二. MapReduce框架组成<br></span></p>
<p><span style="font-size:18px;">MapReduce框架的主要组成部分和它们之间的相关关系，如下所示：<br></span></p>
<p style="text-align:center;"><span style="font-size:18px;"><img src="https://img-blog.csdn.net/20170730235056377" alt=""><br></span></p>
<p><span style="font-size:18px;">上述过程包含4个实体，各实体的功能，如下所示：</span></p>
<p><span style="font-size:18px;">（1）Client：提交的MapReduce作业，比如，写的MR程序，或者CLI执行的命令等；</span></p>
<p><span style="font-size:18px;">（2）JobTracker：协调作业的运行，本质是一个管理者；</span></p>
<p><span style="font-size:18px;">（3）TaskTracker：运行作业划分后的任务，本质就是一个执行者；</span></p>
<p><span style="font-size:18px;">（4）HDFS：用来在集群间共享存储的一种抽象文件系统。</span></p>
<p><span style="font-size:18px;">直观来说，NameNode就是一个元数据仓库，就像Windows中的注册表一样。SecondaryNameNode可以看成</span></p>
<p><span style="font-size:18px;">NameNode的备份。DataNode可以看成是用来存储作业划分后的任务。在通常搭建的3台Hadoop分布式集群中，</span><span style="font-size:18px;">Master是NameNode，SecondaryNameNode，JobTracker，其它2台Slaver都是TaskTracker，DataNode，并且</span><span style="font-size:18px;">TaskTracker都需要运行在HDFS的DataNode上面。</span></p>
<p><span style="font-size:18px;">上述用到的类，或者进程的功能，如下所示：</span></p>
<p><span style="font-size:18px;">（1）Mapper和Reducer：基于Hadoop的MapReduce应用程序最今本的组成部分包括：一个Mapper抽象类，一个</span><span style="font-size:18px;">Reducer</span><span style="font-size:18px;">抽象类，一个创建JobConf的执行程序。</span></p>
<p><span style="font-size:18px;">（2）JobTracker：JobTracker属于master，一般情况应该部署在单独的机器上，它的功能就是接收Job，负责调度</span><span style="font-size:18px;">Job的每</span><span style="font-size:18px;">一个子任务Task运行在TaskTracker上，并且监控它们，如果发现有失败的Task就重启它即可。</span></p>
<p><span style="font-size:18px;">（3）TaskTracker：TaskTracker是运行于多节点的slaver服务，它的功能是主动通过心跳与JobTracker进行通信接收</span><span style="font-size:18px;">作</span><span style="font-size:18px;">业，并且负责执行每一个任务。</span></p>
<p><span style="font-size:18px;">（4）JobClient：JobClient的功能是在Client提交作业后，把一些文件上传到HDFS，比如作业的jar包（包括应用程序</span><span style="font-size:18px;">以及</span><span style="font-size:18px;">配置参数）等，并且把路径提交到JobTracker，然后由JobTracker创建每一个Task（即MapTask和</span></p>
<p><span style="font-size:18px;">ReduceTask）并</span><span style="font-size:18px;">将它们分别发送到各个TaskTracker上去执行。</span></p>
<p><span style="font-size:18px;">（5）JobInProgress：JobClient提交Job后，JobTracker会创建一个JobInProgress来跟踪和调度这个Job，并且把它</span><span style="font-size:18px;">添加到</span><span style="font-size:18px;">Job队列中。JobInProgress根据提交的Job Jar中定义的输入数据集（已分解成FileSplit）创建对应的一批</span><span style="font-size:18px;">TaskInProgress1用于监控和调度Task。</span></p>
<p><span style="font-size:18px;">（6）TaksInProgress2：JobTracker通过每一个TaskInProgress1来运行Task，这时会把Task对象（即MapTask和</span><span style="font-size:18px;">ReduceTask）序列化写入相应的TaskTracker中去，TaskTracker会创建对应的TaskInProgress2用于监控和调度该</span><span style="font-size:18px;">MapTask和ReduceTask。</span></p>
<p><span style="font-size:18px;">（7）MapTask和ReduceTask：Mapper根据Job Jar中定义的输入数据&lt;key1, value1&gt;读入，生成临时的&lt;key2, </span><span style="font-size:18px;">value2&gt;，</span><span style="font-size:18px;">如果定义了Combiner，MapTask会在Mapper完成后调用该Combiner将相同Key的值做合并处理，目的是</span><span style="font-size:18px;">为了减少输</span><span style="font-size:18px;">出结果。MapTask全部完成后交给ReduceTask进程调用Reducer处理，生成最终结果&lt;key3,
 value3&gt;。具</span><span style="font-size:18px;">体过程可以</span><span style="font-size:18px;">参见 [4]。</span></p>
<p><span style="font-size:18px;"><br></span></p>
<p><span style="font-size:18px;">三. MapReduce工作原理</span></p>
<p><span style="font-size:18px;">整个MapReduce作业的工作工程，如下所示：</span></p>
<p style="text-align:center;"><span style="font-size:18px;"><img src="https://img-blog.csdn.net/20170731194249947" alt=""><br></span></p>
<p><span style="font-size:18px;">1. 作业的提交</span></p>
<p><span style="font-size:18px;">JobClient的submitJob()方法实现的作业提交过程，如下所示：</span></p>
<p><span style="font-size:18px;">（1）通过JobTracker的getNewJobId()请求一个新的作业ID；[2]</span></p>
<p><span style="font-size:18px;">（2）检查作业的输出说明（比如没有指定输出目录或输出目录已经存在，就抛出异常）；</span></p>
<p><span style="font-size:18px;">（3）计算作业的输入分片（当分片无法计算时，比如输入路径不存在等原因，就抛出异常）；</span></p>
<p><span style="font-size:18px;">（4）将运行作业所需的资源（比如作业Jar文件，配置文件，计算所得的输入分片等）复制到一个以作业ID命名的目</span><span style="font-size:18px;">录中。</span><span style="font-size:18px;">（集群中有多个副本可供TaskTracker访问）[3]</span></p>
<p><span style="font-size:18px;">（5）通过调用JobTracker的submitJob()方法告知作业准备执行。[4]</span></p>
<p><span style="font-size:18px;">2. 作业的初始化</span></p>
<p><span style="font-size:18px;">（1）JobTracker接收到对其submitJob()方法的调用后，就会把这个调用放入一个内部队列中，交由作业调度器（比</span><span style="font-size:18px;">如先进</span><span style="font-size:18px;">先出调度器，容量调度器，公平调度器等）进行调度；[5]</span></p>
<p><span style="font-size:18px;">（2）初始化主要是创建一个表示正在运行作业的对象——封装任务和记录信息，以便跟踪任务的状态和进程；[5]</span></p>
<p><span style="font-size:18px;">（3）为了创建任务运行列表，作业调度器首先从HDFS中获取JobClient已计算好的输入分片信息[6]。然后为每个</span><span style="font-size:18px;">分片</span><span style="font-size:18px;">创建一个MapTask，并且创建ReduceTask。（Task在此时被指定ID，请区分清楚Job的ID和Task的ID）。</span></p>
<p><span style="font-size:18px;">3. 任务的分配</span></p>
<p><span style="font-size:18px;">（1）TaskTracker定期通过“心跳”与JobTracker进行通信，主要是告知JobTracker自身是否还存活，以及是否已经准</span><span style="font-size:18px;">备好运</span><span style="font-size:18px;">行新的任务等；[7]</span></p>
<p><span style="font-size:18px;">（2）JobTracker在为TaskTracker选择任务之前，必须先通过作业调度器选定任务所在的作业；</span></p>
<p><span style="font-size:18px;">（3）对于MapTask和ReduceTask，TaskTracker有固定数量的任务槽（准确数量由TaskTracker核的数量和内存大小</span><span style="font-size:18px;">来决</span><span style="font-size:18px;">定）。JobTracker会先将TaskTracker的MapTask填满，然后分配ReduceTask到TaskTracker；</span></p>
<p><span style="font-size:18px;">（4）对于MapTrask，JobTracker通过会选取一个距离其输入分片文件最近的TaskTracker。对于ReduceTask，因为</span><span style="font-size:18px;">无法考</span><span style="font-size:18px;">虑数据的本地化，所以也没有什么标准来选择哪个TaskTracker。</span></p>
<p><span style="font-size:18px;">4. 任务的执行</span></p>
<p><span style="font-size:18px;">（1）TaskTracker分配到一个任务后，通过从HDFS把作业的Jar文件复制到TaskTracker所在的文件系统（Jar本地化</span><span style="font-size:18px;">用来启</span><span style="font-size:18px;">动JVM），同时TaskTracker将应用程序所需要的全部文件从分布式缓存复制到本地磁盘；[8]</span></p>
<p><span style="font-size:18px;">（2）TaskTracker为任务新建一个本地工作目录，并把Jar文件中的内容解压到这个文件夹中；</span></p>
<p><span style="font-size:18px;">（3）TaskTracker启动一个新的JVM（9）来运行每个Task（包括MapTask和ReduceTask），这样Client的</span></p>
<p><span style="font-size:18px;">MapReduce就</span><span style="font-size:18px;">不会影响TaskTracker守护进程（比如，导致崩溃或挂起等）；</span></p>
<p><span style="font-size:18px;">（4）子进程通过umbilical接口与父进程进行通信，Task子进程每隔几秒便告知父进程它的进度，直到任务完成。</span></p>
<p><span style="font-size:18px;">5. 进程和状态的更新</span></p>
<p><span style="font-size:18px;">一个作业和它的每个任务都有一个状态信息，包括作业或任务的运行状态，Map和Reduce的进度，计数器值，状态</span><span style="font-size:18px;">消息或描述（可以由用户代码来设置）。这些状态信息在作业期间不断改变，它们是如何与Client通信的呢？</span></p>
<p style="text-align:center;"><span style="font-size:18px;"><img src="https://img-blog.csdn.net/20170731194324853" alt=""><br></span></p>
<p><span style="font-size:18px;">（1）任务在运行时，对其进度（即任务完成的百分比）保持追踪。对于MapTask，任务进度是已处理输入所占的比</span><span style="font-size:18px;">例。对</span><span style="font-size:18px;">于ReduceTask，情况稍微有点复杂，但系统仍然会估计已处理Reduce输入的比例；</span></p>
<p><span style="font-size:18px;">（2）这些消息通过一定的时间间隔由Child JVM—&gt;TaskTracker—&gt;JobTracker汇聚。JobTracker将产生一个表明所</span><span style="font-size:18px;">有运行</span><span style="font-size:18px;">作业及其任务状态的全局视图。可以通过Web UI查看。同时JobClient通过每秒查询JobTracker来获得最新状</span><span style="font-size:18px;">态，并</span><span style="font-size:18px;">且输出到控制台上。</span></p>
<p><span style="font-size:18px;">6. 作业的完成</span></p>
<p><span style="font-size:18px;">当JobTracker收到作业最后一个任务已完成的通知后，便把作业的状态设置为"成功"。然后，在JobClient查询状态</span><span style="font-size:18px;">时，便知道作业已成功完成，于是JobClient打印一条消息告知用户，最后从runJob()方法返回。</span></p>
<p><span style="font-size:18px;"><br></span></p>
<p><span style="font-size:18px;">四. Shuffle阶段和Sort阶段</span></p>
<p><span style="font-size:18px;">Shuffle阶段是指从Map的输出开始，包括系统执行排序以及传送Map输出到Reduce作为输入的过程。Sort阶段是指对</span><span style="font-size:18px;">Map端输出的Key进行排序的过程。不同的Map可能输出相同的Key，相同的Key必须发送到同一个Reduce端处理。</span><span style="font-size:18px;">Shuffle阶段可以分为Map端Shuffle和Reduce端Shuffle。Shuffle阶段和Sort阶段工作过程，如下所示：</span></p>
<p style="text-align:center;"><span style="font-size:18px;"><img src="https://img-blog.csdn.net/20170731194344978" alt=""></span></p>
<p><span style="font-size:18px;">如果说以上是从物理实体的角度来讲解MapReduce的工作原理，那么以上便是从逻辑实体的角度来讲解MapReduce</span><span style="font-size:18px;">的工作原理，如下所示：</span></p>
<p><span style="font-size:18px;">1. Map端的Shuffle</span></p>
<p><span style="font-size:18px;">（1）Map函数开始产生输出时，并不是简单地把数据写到磁盘，因为频繁的磁盘操作会导致性能严重下降。它的处</span><span style="font-size:18px;">理过程更复杂，数据首先写到内存中的一个缓冲区，并做一些预排序，以提升效率；</span></p>
<p><span style="font-size:18px;">（2）每个MapTask都有一个用来写入输出数据的循环内存缓冲区（默认大小为100MB），当缓冲区中的数据量达到</span><span style="font-size:18px;">一个特</span><span style="font-size:18px;">定阈值时（默认是80%）系统将会启动一个后台线程把缓冲区中的内容写到磁盘（即spill阶段）。在写磁盘过</span><span style="font-size:18px;">程中，</span><span style="font-size:18px;">Map输出继续被写到缓冲区，但如果在此期间缓冲区被填满，那么Map就会阻塞直到写磁盘过程完成；</span></p>
<p><span style="font-size:18px;">（3）在写磁盘前，线程首先根据数据最终要传递到的Reducer把数据划分成相应的分区（partition）。在每个分区</span><span style="font-size:18px;">中，后台</span><span style="font-size:18px;">线程按Key进行排序（快速排序），如果有一个Combiner（即Mini Reducer）便会在排序后的输出上运行；</span></p>
<p><span style="font-size:18px;">（4）一旦内存缓冲区达到溢出写的阈值，就会创建一个溢出写文件，因此在MapTask完成其最后一个输出记录后，</span><span style="font-size:18px;">便会有</span><span style="font-size:18px;">多个溢出写文件。在在MapTask完成前，溢出写文件被合并成一个索引文件和数据文件（多路归并排序）</span><span style="font-size:18px;">（Sort阶</span><span style="font-size:18px;">段）；</span></p>
<p><span style="font-size:18px;">（5）溢出写文件归并完毕后，Map将删除所有的临时溢出写文件，并告知TaskTracker任务已完成，只要其中一个</span><span style="font-size:18px;">MapTask</span><span style="font-size:18px;">完成，ReduceTask就开始复制它的输出（Copy阶段）；</span></p>
<p><span style="font-size:18px;">（6）Map的输出文件放置在运行MapTask的TaskTracker的本地磁盘上，它是运行ReduceTask的TaskTracker所需要</span><span style="font-size:18px;">的输入</span><span style="font-size:18px;">数据，但是Reduce输出不是这样的，它一般写到HDFS中（Reduce阶段）。</span></p>
<p><span style="font-size:18px;">2. Reduce端的Shuffle</span></p>
<p><span style="font-size:18px;">（1）Copy阶段：Reduce进程启动一些数据copy线程，通过HTTP方式请求MapTask所在的TaskTracker以获取输出</span><span style="font-size:18px;">文件。</span></p>
<p><span style="font-size:18px;">（2）Merge阶段：将Map端复制过来的数据先放入内存缓冲区中，Merge有3种形式，分别是内存到内存，内存到磁</span><span style="font-size:18px;">盘，磁</span><span style="font-size:18px;">盘到磁盘。默认情况下第一种形式不启用，第二种Merge方式一直在运行（spill阶段）直到结束，然后启用第</span><span style="font-size:18px;">三种磁</span><span style="font-size:18px;">盘到磁盘的Merge方式生成最终的文件。</span></p>
<p><span style="font-size:18px;">（3）Reduce阶段：最终文件可能存在于磁盘，也可能存在于内存中，但是默认情况下是位于磁盘中的。当Reduce</span><span style="font-size:18px;">的输入</span><span style="font-size:18px;">文件已定，整个Shuffle就结束了，然后就是Reduce执行，把结果放到HDFS中。</span></p>
<p><span style="font-size:18px;"><br></span></p>
<p><span style="font-size:18px;">五. 其它</span></p>
<p><span style="font-size:18px;">HDFS和MapReduce是Hadoop的基础架构。除了上述讲解之外，还有MapReduce容错机制，任务JVM重用，作业调</span><span style="font-size:18px;">度器等都还没有总结。彻底理解了MapReduce的工作原理之后就可以大量的MapReduce编程了，计划将Hadoop自带</span><span style="font-size:18px;">实例看完后，再研读《Mahout实战》，同步学习《Hadoop技术内幕：深入解析YARN架构设计与实现原理》，正式</span><span style="font-size:18px;">迈入Hadoop
 2.x版本的大门。</span></p>
<p><span style="font-size:18px;"><br></span></p>
<p><span style="font-size:18px;">参考文献：</span></p>
<p><span style="font-size:18px;">[1] 《Hadoop权威指南》（第二版）</span></p>
<p><span style="font-size:18px;">[2] 《Hadoop应用开发技术详解》</span></p>
<p><span style="font-size:18px;">[3] Hadoop 0.18文档：<a href="http://hadoop.apache.org/docs/r1.0.4/cn/hdfs_design.html" rel="nofollow">http://hadoop.apache.org/docs/r1.0.4/cn/hdfs_design.html</a></span></p>
<p><span style="font-size:18px;">[4] WordCount源码剖析：<a href="http://www.voidcn.com/blog/recommender_system/article/p-4237582.html" rel="nofollow">http://www.voidcn.com/blog/recommender_system/article/p-4237582.html</a></span></p>
<p><span style="font-size:18px;">[5] 外部排序技术之多路归并排序：<a href="http://blog.chinaunix.net/uid-25324849-id-2182916.html" rel="nofollow">http://blog.chinaunix.net/uid-25324849-id-2182916.html</a><br></span></p>
            </div>
                </div>