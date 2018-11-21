---
layout:     post
title:      hadoop（hdfs mapreduce）及异常处理
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><strong>一 开篇<br>
1.1 学习方法：</strong><br>
战略上藐视，战术上重视<br>
让你的hadoop跑起来，让helloworld跑起来<br>
要有清晰的路线图<br><strong>1.3 hadoop与大数据时代</strong><br>
一提到大数据，我们就想到hadoop，hadoop几乎成为了大数据的代名词，不可置疑的是hadoop确实是处理大数据的一种利器。但是，hadoop只是新时代（大数据时代）里的老东西（hadoop早就有，大数据时代才刚到来，我们不能说hadoop催生了大数据时代，只能说大数据时代让我们想起了hadoop），大数据时代代表着生产和生活方式的变革（在这个时代里我们更多的是关注这东西是什么，这东西是为什么已不再重要），随着这种变革愈来愈近，愈来愈明确，hadoop自身也在面临着变革，如果不变革，hadoop也许会在大数据真正降临的前夜分崩离析。<br><strong>二 hadoop<br>
2.1 hadoop简介</strong><br>
狭义的hadoop指的是apache的hadoop项目，他包括common，HDFS，YARN，MapReduce这四个子模块，hadoop为我们提供一套分布式文件存储和并行计算处理框架。<br>
Common：一组分布文件系统和通用 I/O 组件与接口(序列化、Java RPC 和持久化数据结构)；<br>
 HDFS：一个分布式文件系统，属于Hadoop体系最底层的一个模块，为Hadoop各子项目提供各种工具，如：配置文件和日志操作等；<br>
YARN：作业调度和集群资源管理的框架<br>
Mapreduce：基于yarn的分布式计算框架，提供并行计算的环境和编程模型；</p>
<p>广义的hadoop是指hadoop生态圈，如图1-1所示：</p>
<p align="left"><br>
 <img src="https://img-blog.csdn.net/20130517084315586" alt=""><br>
图1-1：hadoop生态圈<br>
Hbase：基于hdfs的nosql数据库（分布式，按列存储），支持随机读写和mapreduce操作。<br>
Hive：数据仓库的基础设施，提供hiveql即席查询功能。<br>
Pig：提供一个高层次的数据流编程语言和执行框架Pig Latin。与hive相比而言，pig有更高的灵活性，对半结构化的数据集支持更好。<br>
Avro: 一种支持高效、跨语言的 RPC 以及永久存储数据的序列化系，主要负责数据的序列化。<br>
Mahout ：一个可扩展的机器学习和数据挖掘库<br>
ZooKeeper: 一个分布式、高可用的协调服务。ZooKeeper 提供分布式锁之类的基本服务用于构建分布式应用<br>
Sqoop：关系型数据库和hadoop之间的数据迁移工具。</p>
<p><br><strong>2.2 安装与配置</strong><br>
安装之前请注意版本的选择<br>
1、hadoop1.0.3与hadoop0.23.6哪个更新？<br>
hadoop有两个分支：0.20.x 和0.23.x。其中从0.20.x 分支发展出来的是hadoop1.0。从0.23.x 分支发展出来的是hadoop2.0。这两个分支的区别在于0.20这个分支用的原来的mapreduce，0.23分支用采用mapreduce2，又叫yarn。MRv2的主要思想是把jobtracker的任务分为两个基本的功能，一个是资源管理，一个是任务监控。从更新日期和功能上看，0.23.6要比hadoop1.0.3更新。<br>
2、hadoop生态圈之间的版本兼容问题<br>
   不选最新的，要选相互兼容和稳定的。</p>
<p><strong>2.2.1 hadoop安装与配置</strong><br>
Hadoop的安装有伪分布式和完全分布式两种安装方式，伪分布式一般安装在windows，这里的安装采用完全分布式安装。<br>
表1列示了hadoop安装过程中的关键步骤<br>
表2-1：Hadoop安装基本步骤<br>
 <img src="https://img-blog.csdn.net/20130517084528399" alt=""></p>
<p><br>
关键步骤 hadoop1.03    <br>
安装规划 三台装有CentOS release 5.5 的linux机器<br>
172.16.18.191    m.yiqifa.com  namenode<br>
172.16.18.192    test105   datanode<br>
172.16.18.188   host8188  datanode    <br>
新建hadoop用户和组 三台机器同样的操作，要求同名，同组，同id    <br>
安装java 1.6以上版本，配置javahome，三台机器同样操作    <br>
配置无密码登陆 要求namenode能够ssh到所有datanode    <br>
安装hadoop 解压文件，修改配置文件，同步文件到所有节点<br>
hadoop-env.sh  运行参数<br>
core-site.xml  hdfs-site.xml  hdfs配置<br>
mapred-site.xml  mapreduce配置<br>
masters salves  主从节点配置    <br>
修改环境变量 /etc/profile 或者是用户主目录下的 .bashrc ，有可能需要修改hosts文件    <br>
namenode格式化 hadoop namenode –format    <br>
启动并验证 start-all.sh 验证jps查看进程， web页面查看hdfs和jobtracker状态<br><a href="http://172.16.18.191:50030/jobtracker.jsp" rel="nofollow">http://172.16.18.191:50030/jobtracker.jsp</a><br><a href="http://172.16.18.191:50070/dfshealth.jsp" rel="nofollow">http://172.16.18.191:50070/dfshealth.jsp</a> 
</p>
<p>2.2.2 IDE集成<br>
Hadoop下的原始生产模式：<br>
Ide编码——》打jar包——》登陆hadoop——》hadoop jar 执行<br>
集成生产模式：<br>
Ide编码——》run on hadoop</p>
<p>环境：<br>
Eclipse3.6  Hadoop1.03  hadoop-eclipse3.6or4.2-plugin-1.0.3.jar<br>
第一步：解压hadoop到指定目录<br>
E:\hadoop\hadoop-1.0.3<br>
第二步：解压eclipse到指定目录<br>
E:\eclipse3.6.2-win32-dropins<br>
第三步：拷贝并解压hadoop-eclipse3.6or4.2-plugin-1.0.3.jar到plugin目录和dropin目录、<br>
第四步：启动eclipse</p>
<p><img src="https://img-blog.csdn.net/20130517084759266" alt=""><br>
 <br><strong>2.2.3 几个问题</strong><br>
（1）关于格式化<br>
   此格式化非彼格式化，在format的时候会清空dfs.name.dir的文件并创建一些文件，用这些持久化到硬盘的文件来维护整个hadoop的名字空间，文件与block的对应关系。<br>
多次格式化后可能namenode和datanode的格式化版本号不一致，从而导致hadoop无法启动。<br>
分块（block）与分片（split），分块针对存储而言的，默认64m为一个block，一个block是一个最小存储单位。分片是针对hadoopjob而言的，hadoop的作业在提交过程中，需要把具体的输入进行分片。具体的分片细节由InputSplitFormat指定。分片的规则为  FileInputFormat.class中的getSplits()方法指定。</p>
<p>（2）关于hadoop ha（availablity，scalability，reliablity）<br>
现象：hadoop0.23以前一个hadoop集群中只有一个namenode，吞吐率和整个集群的容量受namenode限制，一旦namenode宕机，整个集群将不可用。针对namenode的点点故障，社区有多种解决方法，如nfs、secondary namenode，还有就是Facebook的AvaterNode。<br>
Ha最新方案：<br>
0.23以后借鉴AvaterNode实现了hadoop的ha，其主要原理是将NameNode分为两种角色，Active和Standby，Active就是正在进行服务的NameNode，而Standby又分三种情况<br>
Cold Standby：是当Active NN已经挂掉后才起来的，它本身没有保存任何数据，这时候并不会减少恢复时间 <br>
Warm Standby：是在Active NN挂掉前起来的，其中保存了一部分数据，所以在恢复时只需要恢复没有的数据，减少了恢复时间 <br>
Hot Standby：它里面保存的数据和Active是完成一样的，可以直接热切换到它上面继续服务 <br>
（3）关于权限<br>
系统级权限  core-site.xmll修改后要重启 和 hadoop-policy.xm 可以动态加载<br>
作业调度级权限 mapred-site.xml<br>
Dfs权限 hdfs-site.xml <br>
2.3 hdfs<br>
2.3.1 Hdfs体系结构<br>
 <img src="https://img-blog.csdn.net/20130517085017888" alt=""><br>
图2-1：hdfs结构图<br>
2.3.2 Name node<br>
current  image  in_use.lock  previous.checkpoint<br>
edits  fsimage  fstime  VERSION<br>
fsimage<br>
edits  fsimage  fstime  VERSION<br>
fsimage：存储命名空间(实际上就是目录和文件)的元数据信息<br>
edits：用来存储对命名空间操作的日志信息，实现NameNode节的恢复<br>
fstime：用来存储check point 的时间；<br>
 VERSION：用来存储NameNode版本信息<br>
previous.checkpoint目录保存了上一个checkpoint的信息(current里的永远是最新的),临时目录用于创建新checkpoint,成功后,老的checkpoint保存在previous.checkpoint目录<br>
in_use.lock：在hdfs启动后产生，是一个空文件，它的作用是如果需要对整个系统做排斥操作，应用应该获取它，以它为锁；<br>
2.3.3 Data Node<br>
blocksBeingWritten  current  detach  in_use.lock  storage  tmp<br>
storage里存的东西是一些出错信息。<br>
current存的是当前有效的数据块，包含了一系列的数据块文件和数据块元数据文件。<br>
detach存放的是分离文件(用于分离硬链接，即copy-on-write)。<br>
tmp保存的是一些操作需要的临时数据块。<br>
/data2/hadoop/hdfs/data/current/subdir28<br>
 <img src="https://img-blog.csdn.net/20130517085139574" alt=""><br>
每个block默认64m，每个block有一个元数据文件，元数据文件存放数据块的校验信息。<br>
2.3.4 NameNode 和 DataNode 的通信与心跳机制<br>
Namenode存储HDFS的元数据在hdf系统启动后就放入内存中，一个元数据（一个HDFS文件块儿）占用200Bytes 。Namenode执行文件系统的namespace相关操作，例如打开、关闭、重命名文件和目录，同时决定了block到具体Datanode节点的映射。Datanode在Namenode的指挥下进行block的创建、删除和复制。用户请求Namenode得到文件的位置后就不需要通过Namenode参与而直接从Datanode进行。<br>
所有的HDFS通讯协议都是构建在TCP/IP协议上，客户端通过一个可配置的端口连接到Namenode，通过ClientProtocol与 Namenode交互。而Datanode是使用DatanodeProtocol与Namenode交互。从ClientProtocol和 Datanodeprotocol抽象出一个远程调用(RPC），在设计上，Namenode不会主动发起RPC，而是是响应来自客户端和 Datanode 的RPC请求。<br>
每个Datanode节点都向Namenode周期性地发送心跳包。网络切割可能导致一部分Datanode跟Namenode失去联系。 Namenode通过心跳包的缺失检测到这一情况，并将这些Datanode标记为dead，不会将新的IO请求发给它们。寄存在dead Datanode上的任何数据将不再有效。Datanode的死亡可能引起一些block的副本数目低于指定值，Namenode不断地跟踪需要复制的 block，在任何需要的情况下启动复制。在下列情况可能需要重新复制：某个Datanode节点失效，某个副本遭到损坏，Datanode上的硬盘错误，或者文件的replication因子增大。<br>
（5）hdfs API<br>
org.apache.hadoop.fs<br>
org.apache.hadoop.fs.FileSystem<br>
org.apache.hadoop.fs.FileUtil<br>
Hadoop整合了众多文件系统，在其中有一个综合性的文件系统抽象，它提供了文件系统实现的各类接口，HDFS只是这个抽象文件系统的一个实例。提供了一个高层的文件系统抽象类org.apache.hadoop.fs.FileSystem，这个抽象类展示了一个分布式文件系统.<br></p>
<p>org.apache.hadoop.io<br>
hadoop没有java的序列化，而是从新搞了一套序列化对象和接口。<br>
 <br><strong>2.4 mapreduce<br>
2.4.1 mapreduce的构成</strong><br>
Mapper：<br>
org.apache.hadoop.mapreduce.Mapper&lt;KEYIN,VALUEIN,KEYOUT,VALUEOUT&gt;<br>
 <br>
Method Summary    <br>
protected  void cleanup(Mapper.Context context) <br>
          Called once at the end of the task.    <br>
protected  void map(KEYIN key, VALUEIN value, Mapper.Context context) <br>
          Called once for each key/value pair in the input split.    <br>
 void run(Mapper.Context context) <br>
          Expert users can override this method for more complete control over the execution of the Mapper.   
<br>
protected  void setup(Mapper.Context context) <br>
          Called once at the beginning of the task.  <br>
通过map方法遍历KEYIN,VALUEIN键值对生产新的KEYOUT,VALUEOUT，供combiner或reducer使用。</p>
<p><strong>Combiner：</strong><br>
一个可以优化MapReduce作业所使用带宽的步骤，这个过程叫Combiner，它在Mapper之后Reducer之前运行。Combiner是可选的，如果这个过程适合于你的作业，Combiner实例会在每一个运行map任务的节点上运行。Combiner会接收特定节点上的Mapper实例的输出作为输入，接着Combiner的输出会被发送到Reducer那里，而不是发送Mapper的输出。Combiner是一个“迷你reduce”过程，它只处理单台机器生成的数据。如果你的reduce是可交换及可组合的，那么它也就可以作为一个Combiner。你只要在driver中添加下面这行代码就可以在词频统计程序中启用Combiner，conf.setCombinerClass(Reduce.class)。Combiner也是Reducer接口的实例。</p>
<p><strong>Reducer：</strong><br>
org.apache.hadoop.mapreduce.Reducer&lt;KEYIN,VALUEIN,KEYOUT,VALUEOUT&gt;<br>
 <br>
Method Summary    <br>
protected  void cleanup(Reducer.Context context) <br>
          Called once at the end of the task.    <br>
protected  void reduce(KEYIN key, Iterable&lt;VALUEIN&gt; values, Reducer.Context context)
<br>
          This method is called once for each key.    <br>
 void run(Reducer.Context context) <br>
          Advanced application writers can use the run(org.apache.hadoop.mapreduce.Reducer.Context) method to control how the reduce task works.   
<br>
protected  void setup(Reducer.Context context) <br>
          Called once at the start of the task.  </p>
<p>Mapper combiner reducer的输入类型：</p>
<p> </p>
<p><strong>2.4.2 mapreduce的执行</strong><br>
1.Job类初始化JobClient实例，JobClient中生成JobTracker的RPC实例，这样可以保持与JobTracker的通讯，JobTracker的地址和端口等都是外部配置的，通过Configuration对象读取并且传入。<br>
2.JobClient提交作业。<br>
3.JobClient生成作业目录。<br>
4.从本地拷贝MapReduce的作业jar文件(一般是自己写的程序代码jar)。<br>
5.如果DistributedCache中有需要的数据，从DistributedCache中拷贝这部分数据。<br>
6.根据InputFormat实例，实现输入数据的split，在作业目录上生成job.split和job.splitmetainfo文件。<br>
7.将配置文件写入到作业目录的job.xml文件中。<br>
8.JobClient和JobTracker通讯，提交作业。<br>
9.JobTracker将job加入到job队列中。<br>
10.JobTracker的TaskScheduler对job队列进行调度。<br>
11.TaskTracker通过心跳和JobTracker保持联系，JobTracker收到后根据心跳带来的数据，判断是否可以分配给TaskTracker Task，TaskScheduler会对Task进行分配。<br>
12.TaskTracker启动TaskRunner实例，在TaskRunner中启动单独的JVM进行Mapper运行。<br>
13.Map端会从HDFS中读取输入数据，执行之后Map输出数据先是在内存当中，当达到阀值后，split到硬盘上面，在此过程中如果有combiner的话要进行combiner，当然sort是肯定要进行的。<br>
14.Map结束了，Reduce开始运行，从Map端拷贝数据，称为shuffle阶段，之后执行reduce输出结果数据，之后进行commit的操作。<br>
15.TaskTracker在收到commit请求后和JobTracker进行通讯，JobTracker做最后收尾工作。<br>
16.JobTracker返回结果给JobClient，运行结束。<br>
 <br>
 <br>
2.5 异常处理<br>
（1）关于安全模式<br>
现象：hadoop进程在进程运行，可登陆查看，但是不可操作，查看日志报系统处于safemod模式。<br>
原因：重启会进入安全模式，其他原因如临时文件满也能导致hadoop进安全模式。<br>
解决：hadoop dfsamin –safemode leave<br>
（2）关于临时文件<br>
&lt;property&gt;<br>
    &lt;name&gt;hadoop.tmp.dir&lt;/name&gt;<br>
    &lt;value&gt;/usr/local/hadoop/tmp&lt;/value&gt;<br>
  &lt;/property&gt;<br>
Hadoop的临时文件由以上配置指定，默认值： /tmp，如果临时文件满，会导致hadoop进入safemode<br>
（3）关于stop-all<br>
现象：Stop-all.sh不起作用<br>
原因：进程pid文件存在tmp文件夹中，已经被清理掉了，stop-all命令无法找到对应进程<br>
解决：kill -9 或者指定pid目录<br>
（4）java heap space<br>
现象：运行mapreduce报内存溢出<br>
原因：内存设置过小，或者mapreduce函数有问题<br>
解决：调整内容设置，hadoop-env.sh 》export HADOOP_HEAPSIZE=2000，默认是1000<br>
（5）disk check error<br>
参照2，或者修改权限<br></p>
<p> </p>
            </div>
                </div>