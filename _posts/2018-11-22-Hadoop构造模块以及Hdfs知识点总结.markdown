---
layout:     post
title:      Hadoop构造模块以及Hdfs知识点总结
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>Hadoop的构造模块</p><p>Hadoop集群中运行的守护进程共有5类：</p><ul><li>NameNode</li><li>DataNode</li><li>Secondary NameNode</li><li>JobTracker</li><li>TaskTracker</li></ul><p>Hadoop集群中的机器（节点）分为2类：主节点和从节点，NameNode、JobTracker所在节点为主节点(负责管理)，DataNode和TaskTracker所在节点为从节点（负责干活儿）。</p><h3>NameNode</h3><p>NameNode节点负责将一个文件分成若干文件块，并记录了HDFS文件系统中的文件块放了在哪些DataNode中（一个数据块被冗余地放到1个或多个DataNode节点中），一个集群中只有一个NameNode节点（Hadoop2.X中情况有所不同了），且该节点通常不再运行DataNode和TaskTracker守护进程。它的主要功能是对内存及IO进行集中管理。</p><h3>DataNode</h3><p>DataNode实际管理很多NameNode分配给它的很多数据块，当有文件块变动时会通知NameNode，同时也从NameNode接受指令。一个集群中有多个DataNode节点，DataNode之间也会保持联系，复制冗余文件块，这样当一个DataNode出现故障后不会影响到文件的完整性。</p><h3>Secondary NameNode</h3><p>SNN只与NameNode通信，定时获取HDFS元数据的快照，一个集群只有一个SNN，且SNN所在节点只运行SNN守护进程，不干其它的事情。当NameNode出现故障后，可以人工启用SNN作为NameNode。定期保存HDFS元数据的快照。</p><h3>JobTracker</h3><p>JobTracker负责分配MapReduce任务给TaskTracker，负责监控任务的执行，如任务失败后重启任务。JobTracker守护进程运行在主节点上，通常该节点不运行DataNode和TaskTracker守护进程。</p><h3>TaskTracker</h3><p>TaskTracker负责完成JobTracker分配的任务并和JobTranker进行通信，回报情况。TaskTracker守护进程运行在多个子节点上</p><h3><img src="http://static.zybuluo.com/zzy0471/wyb5fy77m5ljpi2vkjax6imk/2016-07-23%2022-22-32%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE.png" alt="2016-07-23 22-22-32屏幕截图.png-15.8kB"><br></h3><p><span style="font-size:14pt;">1、HDFS的设计</span></p><p>HDFS是什么：HDFS即Hadoop分布式文件系统（Hadoop Distributed Filesystem），以<strong>流式数据访问模式</strong>来存储超大文件，运行于商用硬件集群上，是管理网络中跨多台计算机存储的文件系统。</p><p>HDFS不适合用在：要求低时间延迟数据访问的应用，存储大量的小文件，多用户写入，任意修改文件。</p><p> <span style="font-size:14pt;">2、HDFS的概念</span></p><p><strong>HDFS数据块</strong>：HDFS上的文件被划分为块大小的多个分块，作为独立的存储单元，称为数据块，默认大小是64MB。</p><p>使用数据块的好处是：</p><ul><li>一个文件的大小可以大于网络中任意一个磁盘的容量。文件的所有块不需要存储在同一个磁盘上，因此它们可以利用集群上的任意一个磁盘进行存储。</li><li>简化了存储子系统的设计，将存储子系统控制单元设置为块，可简化存储管理，同时元数据就不需要和块一同存储，用一个单独的系统就可以管理这些块的元数据。</li><li>数据块适合用于数据备份进而提供数据容错能力和提高可用性。</li></ul><p>查看块信息</p><p><img src="https://images2017.cnblogs.com/blog/1136325/201708/1136325-20170819112145115-250883951.png" alt=""></p><p><img src="https://images2017.cnblogs.com/blog/1136325/201708/1136325-20170819112241959-1460023990.jpg" alt=""></p><p> </p><strong>HDFS Federation</strong>（联邦HDFS）<p>通过添加namenode实现扩展，其中每个namenode管理文件系统命名空间中的一部分。每个namenode维护一个命名空间卷，包括命名空间的源数据和该命名空间下的文件的所有数据块的数据块池。</p><p> <strong>HDFS的高可用性</strong>（High-Availability）</p><p> Hadoop的2.x发行版本在HDFS中增加了对高可用性（HA）的支持。在这一实现中，配置了一对活动-备用（active-standby）namenode。当活动namenode失效，备用namenode就会接管它的任务并开始服务于来自客户端的请求，不会有明显的中断。</p><p>架构的实现包括：</p><ul><li>namenode之间通过高可用的共享存储实现编辑日志的共享。</li><li>datanode同时向两个namenode发送数据块处理报告。</li><li>客户端使用特定的机制来处理namenode的失效问题，这一机制对用户是透明的。</li></ul><p>故障转移控制器：管理着将活动namenode转移给备用namenode的转换过程，基于ZooKeeper并由此确保有且仅有一个活动namenode。每一个namenode运行着一个轻量级的故障转移控制器，其工作就是监视宿主namenode是否失效并在namenode失效时进行故障切换。</p><p> <span style="font-size:14pt;">3、命令行接口</span></p><p>两个属性项： <strong>fs.default.name</strong> 用来设置Hadoop的默认文件系统，设置hdfs URL则是配置HDFS为Hadoop的默认文件系统。<strong>dfs.replication</strong>  设置文件系统块的副本个数</p><p>文件系统的基本操作：hadoop fs -help可以获取所有的命令及其解释</p><p>常用的有：</p><ul><li>hadoop fs -ls /   列出hdfs文件系统根目录下的目录和文件</li><li>hadoop fs -copyFromLocal &lt;local path&gt; &lt;hdfs path&gt; 从本地文件系统将一个文件复制到HDFS</li><li>hadoop fs -rm -r &lt;hdfs dir or file&gt; 删除文件或文件夹及文件夹下的文件</li><li>hadoop fs -mkdir &lt;hdfs dir&gt;在hdfs中新建文件夹</li></ul><p>HDFS的文件访问权限：只读权限（r），写入权限（w），可执行权限（x）</p><p> <span style="font-size:14pt;"> 4、Hadoop文件系统</span></p><p>Hadoop有一个抽象的文件系统概念，HDFS只是其中的一个实现。Java抽象接口org.apache.hadoop.fs.FileSystem定义了Hadoop中的一个文件系统接口。该抽象类实现HDFS的具体实现是 hdfs.DistributedFileSystem</p><p> <span style="font-size:14pt;">5、Java接口</span></p><p>最简单的从Hadoop URL读取数据 （这里在Eclipse上连接HDFS编译运行）</p><div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><br></span></div><pre><span style="color:#0000ff;">package</span><span style="color:#000000;"> filesystem;

</span><span style="color:#0000ff;">import</span><span style="color:#000000;"> java.io.IOException;
</span><span style="color:#0000ff;">import</span><span style="color:#000000;"> java.io.InputStream;
</span><span style="color:#0000ff;">import</span><span style="color:#000000;"> java.net.MalformedURLException;
</span><span style="color:#0000ff;">import</span><span style="color:#000000;"> java.net.URL;

</span><span style="color:#0000ff;">import</span><span style="color:#000000;"> org.apache.hadoop.fs.FsUrlStreamHandlerFactory;
</span><span style="color:#0000ff;">import</span><span style="color:#000000;"> org.apache.hadoop.io.IOUtils;

</span><span style="color:#0000ff;">public</span> <span style="color:#0000ff;">class</span><span style="color:#000000;"> URLCat {
    </span><span style="color:#0000ff;">static</span><span style="color:#000000;"> {
        URL.setURLStreamHandlerFactory(</span><span style="color:#0000ff;">new</span><span style="color:#000000;"> FsUrlStreamHandlerFactory());
    }
    </span><span style="color:#0000ff;">public</span> <span style="color:#0000ff;">static</span> <span style="color:#0000ff;">void</span> main(String[] args) <span style="color:#0000ff;">throws</span><span style="color:#000000;"> MalformedURLException, IOException {
        InputStream in </span>= <span style="color:#0000ff;">null</span><span style="color:#000000;">;
        String input </span>= "hdfs://192.168.92.138:9000/user/test.txt"<span style="color:#000000;">;
        </span><span style="color:#0000ff;">try</span><span style="color:#000000;"> {
            in </span>= <span style="color:#0000ff;">new</span><span style="color:#000000;"> URL(input).openStream();
            IOUtils.copyBytes(in, System.out, </span>4096,<span style="color:#0000ff;">false</span><span style="color:#000000;">);
        }</span><span style="color:#0000ff;">finally</span><span style="color:#000000;"> {
            IOUtils.closeStream(in);
        }
    }
}</span></pre></div><p>这里调用Hadoop的IOUtils类，在输入流和输出流之间复制数据（in和System.out）最后两个参数用于第一个设置复制的缓冲区大小，第二个设置结束后是否关闭数据流。</p><p> 还可以通过FileSystem API读取数据</p><p>代码如下：<span class="cnblogs_code_copy"></span></p><div class="cnblogs_code"><pre><span style="color:#0000ff;">package</span><span style="color:#000000;"> filesystem;

</span><span style="color:#0000ff;">import</span><span style="color:#000000;"> java.io.IOException;
</span><span style="color:#0000ff;">import</span><span style="color:#000000;"> java.io.InputStream;
</span><span style="color:#0000ff;">import</span><span style="color:#000000;"> java.net.URI;

</span><span style="color:#0000ff;">import</span><span style="color:#000000;"> org.apache.hadoop.conf.Configuration;
</span><span style="color:#0000ff;">import</span><span style="color:#000000;"> org.apache.hadoop.fs.FileSystem;
</span><span style="color:#0000ff;">import</span><span style="color:#000000;"> org.apache.hadoop.fs.Path;
</span><span style="color:#0000ff;">import</span><span style="color:#000000;"> org.apache.hadoop.io.IOUtils;

</span><span style="color:#0000ff;">public</span> <span style="color:#0000ff;">class</span><span style="color:#000000;"> FileSystemCat {

    </span><span style="color:#0000ff;">public</span> <span style="color:#0000ff;">static</span> <span style="color:#0000ff;">void</span> main(String[] args) <span style="color:#0000ff;">throws</span><span style="color:#000000;"> IOException {
        String uri </span>= "hdfs://192.168.92.136:9000/user/test.txt"<span style="color:#000000;">;
        Configuration conf </span>= <span style="color:#0000ff;">new</span><span style="color:#000000;"> Configuration();

        FileSystem fs </span>=<span style="color:#000000;"> FileSystem.get(URI.create(uri),conf);
        InputStream in </span>= <span style="color:#0000ff;">null</span><span style="color:#000000;">;
        </span><span style="color:#0000ff;">try</span><span style="color:#000000;"> {
            in </span>= fs.open(<span style="color:#0000ff;">new</span><span style="color:#000000;"> Path(uri));
            IOUtils.copyBytes(in, System.out, </span>1024,<span style="color:#0000ff;">false</span><span style="color:#000000;">);
        }</span><span style="color:#0000ff;">finally</span><span style="color:#000000;"> {
            IOUtils.closeStream(in);
        }
    }
}</span></pre></div><p>这里调用open()函数来获取文件的输入流，FileSystem的get()方法获取FileSystem实例。</p><p> 使用FileSystem API写入数据</p><p>代码如下：</p><div class="cnblogs_code"><pre><span style="color:#0000ff;">package</span><span style="color:#000000;"> filesystem;

</span><span style="color:#0000ff;">import</span><span style="color:#000000;"> java.io.BufferedInputStream;
</span><span style="color:#0000ff;">import</span><span style="color:#000000;"> java.io.FileInputStream;
</span><span style="color:#0000ff;">import</span><span style="color:#000000;"> java.io.FileNotFoundException;
</span><span style="color:#0000ff;">import</span><span style="color:#000000;"> java.io.InputStream;
</span><span style="color:#0000ff;">import</span><span style="color:#000000;"> java.io.OutputStream;
</span><span style="color:#0000ff;">import</span><span style="color:#000000;"> java.net.URI;

</span><span style="color:#0000ff;">import</span><span style="color:#000000;"> org.apache.hadoop.conf.Configuration;
</span><span style="color:#0000ff;">import</span><span style="color:#000000;"> org.apache.hadoop.fs.FileSystem;
</span><span style="color:#0000ff;">import</span><span style="color:#000000;"> org.apache.hadoop.fs.Path;
</span><span style="color:#0000ff;">import</span><span style="color:#000000;"> org.apache.hadoop.io.IOUtils;
</span><span style="color:#0000ff;">import</span><span style="color:#000000;"> org.apache.hadoop.util.Progressable;

</span><span style="color:#0000ff;">public</span> <span style="color:#0000ff;">class</span><span style="color:#000000;"> FileCopyWithProgress {

    </span><span style="color:#0000ff;">public</span> <span style="color:#0000ff;">static</span> <span style="color:#0000ff;">void</span> main(String[] args) <span style="color:#0000ff;">throws</span><span style="color:#000000;"> Exception {
        String localSrc </span>= "E:\\share\\input\\2007_12_1.txt"<span style="color:#000000;">;
        String dst </span>= "hdfs://192.168.92.136:9000/user/logs/2008_10_2.txt"<span style="color:#000000;">;
        
        InputStream in </span>= <span style="color:#0000ff;">new</span> BufferedInputStream(<span style="color:#0000ff;">new</span><span style="color:#000000;"> FileInputStream(localSrc));
        
        Configuration conf </span>= <span style="color:#0000ff;">new</span><span style="color:#000000;"> Configuration();
        FileSystem fs </span>=<span style="color:#000000;"> FileSystem.get(URI.create(dst),conf);
        OutputStream out </span>= fs.create(<span style="color:#0000ff;">new</span> Path(dst),<span style="color:#0000ff;">new</span><span style="color:#000000;"> Progressable() {
            </span><span style="color:#0000ff;">public</span> <span style="color:#0000ff;">void</span><span style="color:#000000;"> progress() {
                System.out.print(</span>"*"<span style="color:#000000;">);
            }
        });
        IOUtils.copyBytes(in, out, </span>1024,<span style="color:#0000ff;">true</span><span style="color:#000000;">);
    }
}</span></pre><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"></span>FileSystem的create()方法用于新建文件,返回FSDataOutputStream对象。 Progressable()用于传递回掉窗口，可以用来把数据写入datanode的进度通知给应用。</div></div><p>使用FileSystem API删除数据</p><p>代码如下：</p><div class="cnblogs_code"><pre><span style="color:#0000ff;">package</span><span style="color:#000000;"> filesystem;

</span><span style="color:#0000ff;">import</span><span style="color:#000000;"> java.net.URI;

</span><span style="color:#0000ff;">import</span><span style="color:#000000;"> org.apache.hadoop.conf.Configuration;
</span><span style="color:#0000ff;">import</span><span style="color:#000000;"> org.apache.hadoop.fs.FileSystem;
</span><span style="color:#0000ff;">import</span><span style="color:#000000;"> org.apache.hadoop.fs.Path;

</span><span style="color:#0000ff;">public</span> <span style="color:#0000ff;">class</span><span style="color:#000000;"> FileDelete {

    </span><span style="color:#0000ff;">public</span> <span style="color:#0000ff;">static</span> <span style="color:#0000ff;">void</span> main(String[] args) <span style="color:#0000ff;">throws</span><span style="color:#000000;"> Exception{
        String uri </span>= "hdfs://192.168.92.136:9000/user/1400.txt"<span style="color:#000000;">;
        
        Configuration conf </span>= <span style="color:#0000ff;">new</span><span style="color:#000000;"> Configuration();
        FileSystem fs </span>=<span style="color:#000000;"> FileSystem.get(URI.create(uri),conf);
        fs.delete(</span><span style="color:#0000ff;">new</span><span style="color:#000000;"> Path(uri));

    }

}</span></pre><div class="cnblogs_code_toolbar">使用delete()方法来永久性删除文件或目录。</div></div><p> FileSystem的其它一些方法：</p><ul><li>public boolean mkdirs(Path f)  throws IOException  用来创建目录，创建成功返回true。</li><li>public FileStatus getFileStates(Path f) throws FIleNotFoundException 用来获取文件或目录的FileStatus对象。</li><li>public FileStatus[ ] listStatus(Path f)throws IOException  列出目录中的内容</li><li>public FileStatus[ ] globStatu(Path pathPattern) throws IOException 返回与其路径匹配于指定模式的所有文件的FileStatus对象数组，并按路径排序 </li></ul><p> <span style="font-size:14pt;">6、数据流</span></p><p>HDFS读取文件过程：</p><p>               <img src="https://images2017.cnblogs.com/blog/1136325/201708/1136325-20170819224028334-650086017.png" alt=""></p><p>过程描述：</p><p>　（1）客户端调用FileSystem对象的open()方法在分布式文件系统中<strong>打开要读取的文件</strong>。</p><p>　（2）分布式文件系统通过使用RPC（远程过程调用）来调用namenode，<strong>确定文件起始块的位置。</strong></p><p>　（3）分布式文件系统的DistributedFileSystem类返回一个支持文件定位的输入流FSDataInputStream对象，FSDataInputStream对象接着封装DFSInputStream对象（<strong>存储着文件起始几个块的datanode地址</strong>），客户端对这个输入流调用read()方法。</p><p>　　（4）DFSInputStream连接距离最近的datanode，通过反复调用read方法，<strong>将数据从datanode传输到客户端</strong>。</p><p>　　（5） 到达块的末端时，DFSInputStream关闭与该datanode的连接，<strong>寻找下一个块的最佳datanode</strong>。</p><p>　　（6）客户端完成读取，对FSDataInputStream调用close()方法<strong>关闭连接</strong>。</p><p> HDFS文件写入的过程：</p><p>                 <img src="https://images2017.cnblogs.com/blog/1136325/201708/1136325-20170819224650490-146173168.png" alt=""></p><p>过程描述：</p><p>写文件过程分析：</p><p>　　（1） 客户端通过对DistributedFileSystem对象调用create()函数来<strong>新建文件</strong>。</p><p>　　（2） 分布式文件系统对namenod创建一个RPC调用，在文件系统的<strong>命名空间中新建一个文件</strong>。</p><p>　　（3）Namenode对新建文件进行检查无误后，分布式文件系统返回给客户端一个FSDataOutputStream对象，FSDataOutputStream对象封装一个DFSoutPutstream对象，负责处理namenode和datanode之间的通信，<strong>客户端开始写入数据</strong>。</p><p>　　（4）FSDataOutputStream将<strong>数据分成一个一个的数据包，写入内部队列“数据队列”</strong>，DataStreamer负责将数据包依次流式传输到由一组namenode构成的管线中。</p><p>　　（5）DFSOutputStream维护着确认队列来等待datanode收到确认回执，<strong>收到管道中所有datanode确认后，数据包从确认队列删除</strong>。</p><p>　　（6）<strong>客户端完成数据的写入</strong>，对数据流调用close()方法。</p><p>　　（7）<strong>namenode确认完成</strong>。</p><p>namenode如何选择在那个datanode存储复本？</p><p>需要对可靠性，写入带宽和读取带宽进行权衡。默认布局是：在运行客户端的节点上放第一个复本（如果客户端运行在集群之外，则在避免挑选存储太满或太忙的节点的情况下随机选择一个节点。）第二个复本放在与第一个不同且随机另外选择的机架中节点上。第三个复本与第二个复本放在同一个机架上，且随机选择另一个节点。其它复本放在集群中随机选择的节点中，尽量避免在同一个机架上放太多复本。</p><p>一个复本个数为3的集群放置位置如图：</p><p>             <img src="https://images2017.cnblogs.com/blog/1136325/201708/1136325-20170819233258850-1030371346.png" alt=""></p><p>HDFS一致性：HDFS在写数据务必要保证数据的一致性与持久性，目前HDFS提供的两种两个保证数据一致性的方法<strong> hsync()方法和hflush()方法</strong>。</p><p>hflush: 保证flush的数据被新的reader读到，但是不保证数据被datanode持久化。<br>hsync: 与hflush几乎一样，不同的是hsync保证数据被datanode持久化。</p><p> 深入hsync()和hflush()参考两篇博客</p><p>http://www.cnblogs.com/foxmailed/p/4145330.html</p><p>http://www.cnblogs.com/yangjiandan/p/3540498.html</p><p> <span style="font-size:14pt;">7、通过Flume和Sqoop导入数据</span></p><p> 可以考虑使用一些现成的工具将数据导入。</p><p>Apache Fluem是一个将<strong>大规模流数据</strong>导入HDFS的工具。典型应用是从另外一个系统中收集日志数据并实现在HDFS中的聚集操作以便用于后期的分析操作。</p><p>Apache Sqoop用来将数据从<strong>结构化存储设备</strong>批量导入HDFS中，例如关系数据库。Sqoop应用场景是组织将白天生产的数据库中的数据在晚间导入Hive数据仓库中进行分析。</p><p> <span style="font-size:14pt;">8、通过distcp并行复制</span></p><p>distcp分布式复制程序，它从Hadoop文件系统间复制大量数据，也可以将大量的数据复制到Hadoop。</p><p>典型应用场景是在HDFS集群之间传输数据。</p><p>% hadoop  distcp  hdfs://namenode1/foo  hdfs://namenode2/bar</p><p> <span style="font-size:14pt;">9、Hadoop存档</span></p><p>HDFS中每个文件均按块方式存储，每个块的元数据存储在namenode的内存中，因此Hadoop存储小文件会非常低效。因为大量的小文件会耗尽namenode中的大部分内存。Hadoop的存档文件或HAR文件，将文件存入HDFS块，减少namenode内存使用，允许对文件进行透明地访问。</p><p>Hadoop存档是通过<strong>archive工具</strong>根据一组文件创建而来的。运行archive指令：</p><p>% hadoop  archive  -archiveName  files.har  /my/files  /my</p><p>列出HAR文件的组成部分：</p><p>% hadoop  fs  -ls  /my/files.har</p><p>files.har是存档文件的名称，这句指令存储 HDFS下/my/files中的文件。</p><p>HAR文件的组成部分：两个索引文件以及部分文件的集合。</p><p><strong>存档的不足：</strong></p><p>新建一个存档文件会创建原始文件的一个副本，因此需要与要存档的文件容量相同大小的磁盘空间。</p><p>一旦存档文件，不能从中增加或删除文件。</p>            </div>
                </div>