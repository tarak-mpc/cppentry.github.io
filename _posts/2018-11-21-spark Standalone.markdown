---
layout:     post
title:      spark Standalone
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/lsshlsw/article/details/40932153				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h1 align="center"><span style="font-size:12px;"></span>
</h1><h1 align="center" style="text-align:center;"><span lang="en-us" xml:lang="en-us">Spark Standalone</span></h1>

<p align="left">        Spark Standalone模式中，资源调度是Spark框架自己实现的，其节点类型分为Master节点和Worker节点，其中Driver运行在Master节点中，并且有常驻内存的Master进程守护，Worker节点上常驻Worker守护进程，负责与Master通信，通过ExecutorRunner来控制运行在当前节点上的CoarseGrainedExecutorBackend。每个Worker 上存在一个或者多个 CoarseGrainedExecutorBackend进程。每个进程包含一个Executor对象，该对象持有一个线程池，每个线程可以执行一个
 Task。 </p>
<p align="left">        SparkStandalone是Master-Slaves架构的集群模式，和大部分的Master-Slaves结构集群类似，都存在着Master单点故障的问题。目前Spark提供了两种方式来解决此问题，一种是基于文件系统的故障恢复模式，这种方法适合当Master进程挂掉之后，直接重启即可；另一种方式是基于Zookeeper的HA方式，类似于HDFS的NameNode的HA方案，ActiveMaster挂掉之后，Standby Master会立即切换过去继续对外提供服务，同时这种基于Zookeeper的HA方案也被很多分布式框架采用，例如另外一种流式计算框架Storm以及HBase等。</p>
<h2>集群启动脚本</h2>
<p>在spark目录下执行，这些脚本只能在集群的master上执行</p>
<p><br></p>
<p>sbin/start-master.sh  创建一个master实例</p>
<p>sbin/start-slaves.sh   创建一个slave实例（conf/slaves中指定的每台机器上）</p>
<p>sbin/start-all.sh      同时创建一个master和多个slave实例</p>
<p>sbin/stop-all.sh      停止master(由bin/start-master.sh创建)</p>
<p>sbin/stop-slaves.sh   停止所有slave实例（位于conf/slaves上的机器）</p>
<p>sbin/stop-all.sh      同时停止master和slaves</p>
<p> </p>
<h2>Application的集群挂载</h2>
<p>        配置好集群后可以再集群上挂载一个应用（application），只需要把master的URL传递给SparkContext或SparkConf接口的构造方法即可。</p>
<p>例如：</p>
<p>val conf = newSparkConf.setMaster(“spark://master:7077”)</p>
<p>val sc = newSparkContext(conf)</p>
<p> </p>
<p>如果要把Spark-shell中运行集群上只需要输入：</p>
<p>Spark-shell--master spark://master:7077</p>
<p> </p>
<p>当然Spark-shell中还有很多参数，例如--core &lt;numCores&gt;来控制spark-shell在集群上所使用的CPU核数。</p>
<p> </p>
<h2>启动编译的程序（Application）</h2>
<p>        脚本spark-submit提供了standalone集群提交编译的spark-application的最直接的方式。对于standalone集群，spark目前仅支持客户端作业进程内的驱动程序(client deploy mode)。</p>
<p>        如果spark-application通过脚本spark-submit启动，那么application包就会自动分配到所有的worker节点上，如果application还同时依赖其他包文件，就应该用—jars 的标志来指定它们，不同包文件之间用逗号分隔(例如--jars jar1,jar2 ),更多的spark-submit参数，参考以前写过的 spark任务提交。</p>
<p> </p>
<h2>资源调度</h2>
<p>        Standalone的集群模式目前只支持简单的FIFO作业调度方式。但是，你可以通过控制Spark作业能够获得的最大资源数来实现大量的并发作业。默认的情况下，一个作业会占用集群中的全部资源，但是这在只有一个作业进程的情况下才有意义。你可以在SparkConf中设置spark.cores.max来覆盖系统设置的CPU可用核数的属性，例如：将该作业的核数设为10</p>
<p>        val conf = newSparkConf().set(“spark.cores.max”,”10”)</p>
<p>        另外，可以在集群的master进程中配置脚本文件spark.deploy.defaultCores来改变作业运行所需要最大内核数的默认值属性，但这需要将下面的配置选项添加到conf/spark-env.sh中：</p>
<p>        exportSPARK_MASTER_OPTS=”-Dspark.deploy.defaultCores=&lt;value&gt;”</p>
<p>        这在共享集群中非常有用，因为用户不可能单独配置自己作业的最大可用核数属性。</p>
<h2>监控和日志</h2>
<p>        Spark通过Web UI来监控集群，master和每个worker都有自己的webUI用于显示集群和作业的统计资料。默认情形下，用户可以通过访问master的主端口master:8080进入到master的Web UI,端口值可以通过配置文件或命令行选项来改变。</p>
<p>        此外，每个作业输出的详细日志可以写入到它的工作目录中（默认为每个Worker节点上的SPARK_HOME/work）。在工作目录中，每个作业对应两个文件stdout和stderr,这两个文件包含了该作业所有控制台输出的信息。</p>
<p>        注：还会有master发送到该worker上的程序jar包。</p>
<h2>高可用性（HA）</h2>
<p>        通常情况下，单机模式的集群调度具有一定的容错能力（目前Spark通过移除失败作业到其他的worker上来实现自身的抗故障特性）。然后，用master做调度决策会产生单点故障的问题，如果master崩溃，就无法创建新的作业了。Spark提出了两种解决方案。</p>
<h3>1. ZooKeeper的StandbyMasters</h3>
<h4>1.1 描述</h4>
<p>        将Standalone集群连接到同一个Zookeeper实例并启动多个Master，利用ZooKeeper提供的选举和状态保存功能，选举一个Master，其他的Master处于Standby状态。如果当前的Master发生故障，另一个Master同样通过选举产生，并恢复到发生故障的Master的状态，恢复调度。整个过程可能需要1-2分钟。该延时只会影响新的Application的调度，对于故障切换过程中正在运行的Application没有任何影响。</p>
<h4>1.2 配置</h4>
<p>在spark-env里对SPARK_DAEMON_JAVA_OPTS设置以启用该恢复模式：</p>
<p> </p>
<p>
</p><table border="1" cellspacing="0" cellpadding="0"><tbody><tr><td valign="top">
<p align="left"><strong><span style="color:#333333;">系统属性</span></strong></p>
</td>
<td valign="top">
<p align="left"><strong><span style="color:#333333;">描述</span></strong></p>
</td>
</tr><tr><td valign="top" style="background:#D2EAF1;">
<p align="left"><strong><span style="color:#444444;background:#FFFFFF;">spark.deploy.recoveryMode</span></strong></p>
</td>
<td valign="top" style="background:#D2EAF1;">
<p align="left"><span style="color:#333333;">设置</span><span style="color:#333333;">ZOOKEEPER</span><span style="color:#333333;">启用</span><span style="color:#333333;">standby Master</span><span style="color:#333333;">恢复模式（缺省值：</span><span style="color:#333333;">NONE</span><span style="color:#333333;">）</span></p>
</td>
</tr><tr><td valign="top">
<p align="left"><strong><span style="color:rgb(68,68,68);">spark.deploy.zookeeper.url</span></strong></p>
</td>
<td valign="top">
<p align="left"><span style="color:#333333;">Zooker</span><span style="color:#333333;">集群的</span><span style="color:#333333;">URL (e.g., 192.168.1.100:2181,192.168.1.101:2181).</span></p>
</td>
</tr><tr><td valign="top" style="background:#D2EAF1;">
<p align="left"><strong><span style="color:#444444;background:#FFFFFF;">spark.deploy.zookeeper.dir</span></strong></p>
</td>
<td valign="top" style="background:#D2EAF1;">
<p align="left"><span style="color:#333333;">设置</span><span style="color:#333333;">Zookeeper</span><span style="color:#333333;">集群中用于存储恢复状态的文件目录</span><span style="color:#333333;"> (</span><span style="color:#333333;">缺省值</span><span style="color:#333333;">: /spark).</span></p>
</td>
</tr></tbody></table><span style="font-size:14px;"><br></span>
<h4>1.3 补充</h4>
<p>        完成ZooKeeper集群的设置后，启用其高可用性是非常简单的。首先启动这个ZooKeeper集群，然后在不同节点上启动多个Masters，注意这些节点需要具有相同的ZooKeeper配置（ZooKeeper URL 和目录），而且Masters 可以随时被添加和移除。</p>
<p>        当调用新的Applications或添加新的Workers时，它们需要知道当前Master的IP地址，这种HA方案处理这种情况很简单，只需要使SparkContext接口指向一个Master列表就可以了，如spark://host1:port1,host2:port2。SparkContext就会尝试着依次对Master列表进行注册并轮询，如果host1出现故障，配置信息对于寻找host2仍然会有效。</p>
<p>        注册接入Master和正常运行没有什么重要不同，当集群启动时， application或worker需要能够找到并注册接入到当前的lead Master， 一旦注册成功，它们就会被并入系统（例如储存在ZooKeeper中）。 发生故障切换时，新被选中的Master就会联系所有先前已经注册成功的applications和Workers，并告知它们领导关系的变化。因此从某种意义上讲，这些applications和workers并不需要事先知道新Master的存在性。</p>
<p>        正是由于这一属性，新的Masters可以在任何时候被创建。唯一需要注意的是，在这个新的Masters成为leader的情形下，新的applications和workers 能够找到并注册接入到这个Master。 一旦注册成功，这个application或worker就会被启用。</p>
<h3>2. 基于文件系统的单点恢复</h3>
<h4>2.1 描述</h4>
<p><span></span>尽管Zookeeper是用于生产模式高可用性的最佳途径，但如果你仅仅想故障后重启你的Master，文件系统（FILESYSTEM）模式就可以做到。Spark提供了目录来保存Application和worker的注册状态信息，一但Master发生故障，就可以通过重新启动Master进程的方式来恢复Application和worker的旧有状态。</p>
<h4>2.2 配置</h4>
<p> 在spark-env里对SPARK_DAEMON_JAVA_OPTS设置：</p>
<p>
</p><table border="1" cellspacing="0" cellpadding="0"><tbody><tr><td valign="top">
<p align="left"><strong><span style="color:#333333;">系统属性</span></strong></p>
</td>
<td valign="top">
<p align="left"><strong><span style="color:#333333;">含义</span></strong></p>
</td>
</tr><tr><td valign="top" style="background:#D2EAF1;">
<p align="left"><strong><span style="color:#444444;background:#FFFFFF;">spark.deploy.recoveryMode</span></strong></p>
</td>
<td valign="top" style="background:#D2EAF1;">
<p align="left"><span style="color:#333333;">设置文件系统启用单点恢复模式（缺省值：</span><span style="color:#333333;">NONE</span><span style="color:#333333;">）</span></p>
</td>
</tr><tr><td valign="top">
<p align="left"><strong><span style="color:rgb(68,68,68);">spark.deploy.recoveryDirectory</span></strong></p>
</td>
<td valign="top">
<p align="left"><span style="color:#333333;">Spark-Master</span><span style="color:#333333;">可进入的文件目录，用于存储作业的恢复状态</span></p>
</td>
</tr></tbody></table><br><h2>集群环境变量配置</h2>
<p></p>
<h4>1. 环境变量</h4>
<p>配置在conf/spark-env.sh（通过conf/spark-env.sh.template模板创建的）中，然后拷贝到所有的worker节点上生效：</p>
<p>
</p><table border="1" cellspacing="0" cellpadding="0"><tbody><tr><td valign="top">
<p align="left"><strong><span style="color:#333333;">环境变量</span></strong></p>
</td>
<td valign="top">
<p align="left"><strong><span style="color:#333333;">含义</span></strong></p>
</td>
</tr><tr><td valign="top" style="background:#D2EAF1;">
<p align="left"><strong><span style="color:#444444;background:#FFFFFF;">SPARK_MASTER_IP</span></strong></p>
</td>
<td valign="top" style="background:#D2EAF1;">
<p align="left"><span style="color:#333333;">Master</span><span style="color:#333333;">节点的</span><span style="color:#333333;">IP</span></p>
</td>
</tr><tr><td valign="top">
<p align="left"><strong><span style="color:rgb(68,68,68);">SPARK_MASTER_PORT</span></strong></p>
</td>
<td valign="top">
<p align="left"><span style="color:#333333;">Master</span><span style="color:#333333;">的端口号</span><span style="color:#333333;"> (</span><span style="color:#333333;">缺省值</span><span style="color:#333333;">: 7077).</span></p>
</td>
</tr><tr><td valign="top" style="background:#D2EAF1;">
<p align="left"><strong><span style="color:#444444;background:#FFFFFF;">SPARK_MASTER_WEBUI_PORT</span></strong></p>
</td>
<td valign="top" style="background:#D2EAF1;">
<p align="left"><span style="color:#333333;">master web UI</span><span style="color:#333333;">端口号</span><span style="color:#333333;"> (</span><span style="color:#333333;">缺省值</span><span style="color:#333333;">: 8080).</span></p>
</td>
</tr><tr><td valign="top">
<p align="left"><strong><span style="color:rgb(68,68,68);">SPARK_MASTER_OPTS</span></strong></p>
</td>
<td valign="top">
<p align="left"><span style="color:rgb(51,51,51);">设置只适用于Master</span><span style="color:rgb(51,51,51);">的配置属性，形式为</span><span style="color:rgb(51,51,51);">”-Dx=y”</span><span style="color:rgb(51,51,51);">，配置选项参见下面列表</span></p>
</td>
</tr><tr><td valign="top" style="background:#D2EAF1;">
<p align="left"><strong><span style="color:#444444;background:#FFFFFF;">SPARK_LOCAL_DIRS</span></strong></p>
</td>
<td valign="top" style="background:#D2EAF1;">
<p align="left"><span style="color:#333333;">设置</span><span style="color:#333333;">Spark</span><span style="color:#333333;">缓存空间目录，包括存储在磁盘上</span><span style="color:#333333;">shuffle</span><span style="color:#333333;">的</span><span style="color:#333333;">map</span><span style="color:#333333;">阶段的输出文件和</span><span style="color:#333333;">RDD</span><span style="color:#333333;">，该文件夹应该设置在你系统中一个高速的本地磁盘上（提高运行效率），也可以是用逗号分割的位于不同磁盘的多个目录</span></p>
</td>
</tr><tr><td valign="top">
<p align="left"><strong><span style="color:rgb(68,68,68);">SPARK_WORKER_CORES</span></strong></p>
</td>
<td valign="top">
<p align="left"><span style="color:#333333;">设置</span><span style="color:#333333;">Worker</span><span style="color:#333333;">节点</span><span style="color:#333333;">Application</span><span style="color:#333333;">可用的</span><span style="color:#333333;">CPU</span><span style="color:#333333;">总核数（缺省值：所有）</span></p>
</td>
</tr><tr><td valign="top" style="background:#D2EAF1;">
<p align="left"><strong><span style="color:#444444;background:#FFFFFF;">SPARK_WORKER_MEMORY</span></strong></p>
</td>
<td valign="top" style="background:#D2EAF1;">
<p align="left"><span style="color:#333333;">设置</span><span style="color:#333333;">Worker</span><span style="color:#333333;">节点</span><span style="color:#333333;">Application</span><span style="color:#333333;">可用的内存总量，</span><span style="color:#333333;">eg.1000M,2G</span></p>
<p align="left"><span style="color:#333333;">(</span><span style="color:#333333;">缺省值：内存总量</span><span style="color:#333333;">-1G) </span></p>
<p align="left"><span style="color:#333333;">注：在单个</span><span style="color:#333333;">Application</span><span style="color:#333333;">的内存配置使用</span><span style="color:#444444;background:#FFFFFF;">spark.executor.memory</span><span style="color:#333333;">属性，</span></p>
<p align="left"><span style="color:#333333;">在</span><span style="color:#333333;">SparkConf</span><span style="color:#333333;">或</span><span style="color:#333333;">SparkContext</span><span style="color:#333333;">中设置</span></p>
</td>
</tr><tr><td valign="top">
<p align="left"><strong><span style="color:rgb(68,68,68);">SPARK_WORKER_PORT</span></strong></p>
</td>
<td valign="top">
<p align="left"><span style="color:#333333;">Worker</span><span style="color:#333333;">节点端口号</span><span style="color:#333333;"> (</span><span style="color:#333333;">缺省值</span><span style="color:#333333;">:
</span><span style="color:#333333;">随机</span><span style="color:#333333;">).</span></p>
</td>
</tr><tr><td valign="top" style="background:#D2EAF1;">
<p align="left"><strong><span style="color:#444444;background:#FFFFFF;">SPARK_WORKER_WEBUI_PORT</span></strong></p>
</td>
<td valign="top" style="background:#D2EAF1;">
<p align="left"><span style="color:#333333;">Worker</span><span style="color:#333333;">节点</span><span style="color:#333333;"> web UI</span><span style="color:#333333;">端口号</span><span style="color:#333333;"> (</span><span style="color:#333333;">缺省值</span><span style="color:#333333;">:
 8081).</span></p>
</td>
</tr><tr><td valign="top">
<p align="left"><strong><span style="color:rgb(68,68,68);">SPARK_WORKER_INSTANCES</span></strong></p>
</td>
<td valign="top">
<p align="left"><span style="color:#333333;">每个</span><span style="color:#333333;">Worker</span><span style="color:#333333;">节点启动的</span><span style="color:#333333;">worker</span><span style="color:#333333;">进程数量（缺省值：</span><span style="color:#333333;">1</span><span style="color:#333333;">）。</span></p>
<p align="left"><span style="color:#333333;">如果使</span><span style="color:#333333;">worker</span><span style="color:#333333;">的实例数大于</span><span style="color:#333333;">1</span><span style="color:#333333;">。要使用</span><span style="color:#333333;">SPARK_WORKER_CORES</span></p>
<p align="left"><span style="color:#333333;">来限制每个</span><span style="color:#333333;">worker</span><span style="color:#333333;">进程所允许使用的内核数，</span></p>
<p align="left"><span style="color:#333333;">否则每个</span><span style="color:#333333;">worker</span><span style="color:#333333;">会尝试使用所有的处理器内核。</span></p>
</td>
</tr><tr><td valign="top" style="background:#D2EAF1;">
<p align="left"><strong><span style="color:#444444;background:#FFFFFF;">SPARK_WORKER_DIR</span></strong></p>
</td>
<td valign="top" style="background:#D2EAF1;">
<p align="left"><span style="color:#333333;">Worker</span><span style="color:#333333;">节点的工作路径，包括日志和暂存空间等。</span></p>
<p align="left"><span style="color:#333333;">（缺省值：</span><span style="color:#333333;">Woker</span><span style="color:#333333;">节点上的</span><span style="color:#333333;">SPARK_HOME/work</span><span style="color:#333333;">）</span></p>
</td>
</tr><tr><td valign="top">
<p align="left"><strong><span style="color:rgb(68,68,68);">SPARK_WORKER_OPTS</span></strong></p>
</td>
<td valign="top">
<p align="left"><span style="color:#333333;">设置只适用于</span><span style="color:#333333;">Woker</span><span style="color:#333333;">节点的配置属性，形式为</span><span style="color:#333333;">”-Dx=y”</span><span style="color:#333333;">，配置选项参见下面列表</span></p>
</td>
</tr><tr><td valign="top" style="background:#D2EAF1;">
<p align="left"><strong><span style="color:#444444;background:#FFFFFF;">SPARK_DAEMON_MEMORY</span></strong></p>
</td>
<td valign="top" style="background:#D2EAF1;">
<p align="left"><span style="color:#333333;">为</span><span style="color:#333333;">Spark master</span><span style="color:#333333;">和</span><span style="color:#333333;">worker</span><span style="color:#333333;">守护进程分配内存</span><span style="color:#333333;"> (</span><span style="color:#333333;">缺省值</span><span style="color:#333333;">:
 512m).</span></p>
</td>
</tr><tr><td valign="top">
<p align="left"><strong><span style="color:rgb(68,68,68);">SPARK_DAEMON_JAVA_OPTS</span></strong></p>
</td>
<td valign="top">
<p align="left"><span style="color:#333333;">配置</span><span style="color:#333333;">Master</span><span style="color:#333333;">节点和</span><span style="color:#333333;">Worker</span><span style="color:#333333;">节点都使用的属性（缺省值</span><span style="color:#333333;">:
</span><span style="color:#333333;">无）</span></p>
</td>
</tr><tr><td valign="top" style="background:#D2EAF1;">
<p align="left"><strong><span style="color:#444444;background:#FFFFFF;">SPARK_PUBLIC_DNS</span></strong></p>
</td>
<td valign="top" style="background:#D2EAF1;">
<p align="left"><span style="color:#333333;">设置</span><span style="color:#333333;">Spark master</span><span style="color:#333333;">和</span><span style="color:#333333;">workers</span><span style="color:#333333;">的公共</span><span style="color:#333333;">DNS</span><span style="color:#333333;">名称</span><span style="color:#333333;">
 (</span><span style="color:#333333;">缺省值</span><span style="color:#333333;">: </span>
<span style="color:#333333;">无</span><span style="color:#333333;">).</span></p>
</td>
</tr></tbody></table><br><br><p></p>
<h4>2. Master参数</h4>
<p></p>
<p><span style="color:rgb(51,51,51);">SPARK_MASTER_OPTS</span><span style="color:rgb(51,51,51);">支持的参数如下：</span></p>
<table border="1" cellspacing="0" cellpadding="0"><tbody><tr><td valign="top">
<p align="left"><strong><span style="color:#333333;">属性名称</span></strong></p>
</td>
<td valign="top">
<p align="left"><strong><span style="color:#333333;">缺省值</span></strong></p>
</td>
<td valign="top">
<p align="left"><strong><span style="color:#333333;">含义</span></strong></p>
</td>
</tr><tr><td valign="top" style="background:#D2EAF1;">
<p align="left"><strong><span style="color:#444444;background:#FFFFFF;">spark.deploy.retainedApplications</span></strong></p>
</td>
<td valign="top" style="background:#D2EAF1;">
<p align="left"><span style="color:#333333;">200</span></p>
</td>
<td valign="top" style="background:#D2EAF1;">
<p align="left"><span style="color:#333333;">显示已完成的</span><span style="color:#333333;">Application</span><span style="color:#333333;">的最大数目，当超过这个数目的时候，将从</span><span style="color:#333333;">UI</span><span style="color:#333333;">中删除之前的</span><span style="color:#333333;">Application</span><span style="color:#333333;">以维持这个数目。</span></p>
</td>
</tr><tr><td valign="top">
<p align="left"><strong><span style="color:rgb(68,68,68);">spark.deploy.retainedDrivers</span></strong></p>
</td>
<td valign="top">
<p align="left"><span style="color:#333333;">200</span></p>
</td>
<td valign="top">
<p align="left"><span style="color:#333333;">显示已完成的</span><span style="color:#333333;">driver</span><span style="color:#333333;">的最大数目，当超过这个数目的时候，将从</span><span style="color:#333333;">UI</span><span style="color:#333333;">中删除之前的</span><span style="color:#333333;">Application</span><span style="color:#333333;">以维持这个数目。</span></p>
</td>
</tr><tr><td valign="top" style="background:#D2EAF1;">
<p align="left"><strong><span style="color:#444444;background:#FFFFFF;">spark.deploy.spread</span></strong><span style="color:rgb(68,68,68);font-weight:bold;background-color:#FFFFFF;">Out</span></p>
</td>
<td valign="top" style="background:#D2EAF1;">
<p align="left"><span style="color:rgb(51,51,51);">true</span></p>
</td>
<td valign="top" style="background:#D2EAF1;">
<p align="left"><span style="color:#333333;">Standalone</span><span style="color:#333333;">集群管理器是否自由选择节点（</span><span style="color:#333333;">true</span><span style="color:#333333;">）还是固定到尽可能少的节点</span><span style="color:#333333;">(false)</span><span style="color:#333333;">，前者会有更好的数据本地性，后者对于密集型工作负载更有效</span></p>
</td>
</tr><tr><td valign="top">
<p align="left"><strong><span style="color:rgb(68,68,68);">spark.deploy.defaultCores</span></strong></p>
</td>
<td valign="top">
<p align="left"><span style="color:#333333;">(infinite)</span></p>
</td>
<td valign="top">
<p align="left"><span style="color:#333333;">Standalone</span><span style="color:#333333;">集群模式中，在没有设置</span><span style="color:#333333;">spark.cores.max</span><span style="color:#333333;">的情况下，系统默认分给</span><span style="color:#333333;">application</span><span style="color:#333333;">的</span><span style="color:#333333;">CPU</span><span style="color:#333333;">核数。该情况下</span><span style="color:#333333;">Application</span><span style="color:#333333;">获得全部</span><span style="color:#333333;">CPU</span><span style="color:#333333;">核数，在共享集群上（例如</span><span style="color:#333333;">mapReduce</span><span style="color:#333333;">），应该手动设置</span><span style="color:#333333;">spark.cores.max</span><span style="color:#333333;">的值，以防止默认获得整个集群的资源。</span></p>
</td>
</tr><tr><td valign="top" style="background:#D2EAF1;">
<p align="left"><strong><span style="color:#444444;background:#FFFFFF;">spark.worker.timeout</span></strong></p>
</td>
<td valign="top" style="background:#D2EAF1;">
<p align="left"><span style="color:#333333;">60</span></p>
</td>
<td valign="top" style="background:#D2EAF1;">
<p align="left"><span style="color:#333333;">设置时限（秒），</span><span style="color:#333333;">master</span><span style="color:#333333;">超过这个时间收不到</span><span style="color:#333333;">worker</span><span style="color:#333333;">的心跳，既认定</span><span style="color:#333333;">worker</span><span style="color:#333333;">已经丢失（故障）</span></p>
</td>
</tr></tbody></table><br><h4></h4>
<h4>3. Worker的参数</h4>
<p><span style="color:rgb(51,51,51);">SPARK_MASTER_OPTS</span><span style="color:rgb(51,51,51);">支持的参数如下：</span></p>
<p>
</p><table border="1" cellspacing="0" cellpadding="0"><tbody><tr><td valign="top">
<p align="left"><strong><span style="color:#333333;">属性名称</span></strong></p>
</td>
<td valign="top">
<p align="left"><strong><span style="color:#333333;">缺省值</span></strong></p>
</td>
<td valign="top">
<p align="left"><strong><span style="color:#333333;">含义</span></strong></p>
</td>
</tr><tr><td valign="top" style="background:#D2EAF1;">
<p align="left"><strong><span style="color:#444444;background:#FFFFFF;">spark.worker.cleanup.enabled</span></strong></p>
</td>
<td valign="top" style="background:#D2EAF1;">
<p align="left"><span style="color:#333333;">false</span></p>
</td>
<td valign="top" style="background:#D2EAF1;">
<p align="left"><span style="color:#333333;">是否定期清理</span><span style="color:#333333;">Worker</span><span style="color:#333333;">节点的应用程序工作目录，清理时不管</span><span style="color:#333333;">Application</span><span style="color:#333333;">是否运行，</span></p>
<p align="left"><span style="color:#333333;">该参数只对</span><span style="color:#333333;">Standalone</span><span style="color:#333333;">生效</span></p>
</td>
</tr><tr><td valign="top">
<p align="left"><strong><span style="color:rgb(68,68,68);">spark.worker.cleanup.interval</span></strong></p>
</td>
<td valign="top">
<p align="left"><span style="color:#333333;">1800 (30 minutes)</span></p>
</td>
<td valign="top">
<p align="left"><span style="color:#333333;">清理</span><span style="color:#333333;">Worker</span><span style="color:#333333;">节点本地过期的应用程序工作目录的时间间隔</span></p>
</td>
</tr><tr><td valign="top" style="background:#D2EAF1;">
<p align="left"><strong><span style="color:#444444;background:#FFFFFF;">spark.worker.cleanup.appDataTtl</span></strong></p>
</td>
<td valign="top" style="background:#D2EAF1;">
<p align="left"><span style="color:#333333;">7 * 24 * 3600 (7 days)</span></p>
</td>
<td valign="top" style="background:#D2EAF1;">
<p><span style="color:#333333;">Worker</span><span style="color:#333333;">保留应用程序工作目录的有效时间。该时间由磁盘空间，应用程序日志，应用程序的jar</span>包以及应用程序的提交频率来设定，默认7天。</p>
</td>
</tr></tbody></table><h4>4. 一些配置的样例</h4>
<p></p>
<p>在conf/spark-env.sh中配置：</p>
<p>//指定Standalone集群IP</p>
<p>export SPARK_MASTER_IP=master   </p>
<p> </p>
<p>//设定每个Worker节点可用内存</p>
<p>export SPARK_WORKER_MEMORY=1g </p>
<p> </p>
<p>//设置Application能使用的CPU核数为4，默认为使用所有集群中的COU核数。</p>
<p>export SPARK_JAVA_OPTS=”-Dspark.cores.max=4”  </p>
<p> </p>
<p>//指定Master的HA，依赖于zookeeper集群，url参数和dir参数在上方的HA有说明。</p>
<p>export SPARK_DAEMON_JAVA_OPTS=”-Dspark.deploy.recoveryMode=ZOOKEEPER</p>
<p>-Dspark.deploy.zookeeper.url=host1:port,host2:port</p>
<p>-Dspark.deploy.zookeeper.dir=/spark”</p>
<p> </p>
<p>注：在设置Worker进程的CPU个数和内存大小，要注意机器的实际硬件条件，如果配置的超过当前Worker节点的硬件条件，Worker进程会启动失败。</p>
            </div>
                </div>