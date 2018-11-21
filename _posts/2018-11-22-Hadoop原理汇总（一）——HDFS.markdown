---
layout:     post
title:      Hadoop原理汇总（一）——HDFS
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，欢迎转载并注明出处					https://blog.csdn.net/u010255818/article/details/72730864				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>摘要：本文主要对hadoop的原理性知识进行汇总，包括核心组件、HDFS存储原理、HDFS shell命令、HDFS Java端API、MapReduce基本原理、shuffle基本原理、sort过程等。</p>

<hr>



<h1 id="1-hadoop核心组件">1 Hadoop核心组件</h1>

<ol>
<li><p>生态系统图 <br>
<img src="https://img-blog.csdn.net/20170525161926948?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMDI1NTgxOA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p></li>
<li><p>HDFS文件系统 <br>
它是一个高度容错的系统，能检测和应对硬件故障，用于在低成本的通用硬件上运行。HDFS简化了文件的一致性模型，通过流式数据访问，提供高吞吐量应用程序数据访问功能，适合带有大型数据集的应用程序。 <br>
<img src="https://img-blog.csdn.net/20170525162050773?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMDI1NTgxOA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""> <br>
● Client：切分文件；与NameNode交互，获取文件位置信息；与DataNode交互，读取和写入数据。 <br>
● NameNode：管理HDFS的文件命名空间，处理客户端请求。 <br>
● DataNode：存储实际的数据，汇报存储信息给NameNode。 <br>
● Secondary NameNode：定期合并NameNode的fsImage(文件镜像)和fsEdits(操作日志)，推送给NameNode；可辅助恢复NameNode。</p></li>
<li><p>MapReduce分布式计算框架 <br>
Map对数据集上的独立元素进行指定的操作，生成键-值对形式中间结果。Reduce则对中间结果中相同 “键”的所有“值”进行规约，以得到最终结果。 <br>
<img src="https://img-blog.csdn.net/20170525162528947?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMDI1NTgxOA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""> <br>
JobTracker：<strong>管理所有作业</strong>，将任务分解成一系列任务，并分派给TaskTracker。 <br>
TaskTracker：<strong>运行Map Task和Reduce Task</strong>；并与JobTracker交互，汇报任务状态。 <br>
Map Task：解析每条数据记录，传递给用户编写的map()，将输出结果写入本地磁盘。 <br>
Reducer Task：从Map Task的执行结果中，远程读取输入数据，对数据进行排序，将数据按照分组传递给用户编写的reduce函数执行。 <br>
<img src="https://img-blog.csdn.net/20170525162718839?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMDI1NTgxOA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p></li>
<li><p>Hive数据仓库 <br>
Hive定义了一种类似SQL的查询语言(HQL),将SQL转化为MapReduce任务在Hadoop上执行。通常用于离线分析 <br>
<img src="https://img-blog.csdn.net/20170525162854746?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMDI1NTgxOA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p></li>
<li><p>HBase数据库 <br>
HBase 是一个面向列的动态模式数据库，键由<strong>行关键字、列关键字和时间戳构成</strong>。HBase提供了对大规模数据的随机、实时读写访问，HBase中保存的数据可以使用MapReduce来处理，它将数据存储和并行计算完美地结合在一起。 <br>
数据模型：<code>Schema--&gt;Table--&gt;Column Family--&gt;Column--&gt;RowKey--&gt;TimeStamp--&gt;Value</code> <br>
<img src="https://img-blog.csdn.net/20170525163108967?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMDI1NTgxOA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p></li>
<li><p>zookeeper分布式协作服务 <br>
解决分布式环境下的数据管理问题：统一命名，状态同步，集群管理，配置同步等</p></li>
<li><p>sqoop数据同步工具 <br>
Sqoop是SQL-to-Hadoop的缩写，主要用于传统数据库和Hadoop之前传输数据。 <br>
数据的导入和导出本质上是Mapreduce程序，充分利用了MR的并行化和容错性。</p></li>
<li><p>pig数据流系统 <br>
设计动机是提供一种基于MapReduce的ad-hoc(计算在query时发生)数据分析工具 <br>
定义了一种数据流语言—Pig Latin，将脚本转换为MapReduce任务在Hadoop上执行。 <br>
通常用于进行离线分析</p></li>
<li><p>Mahout数据挖掘算法库 <br>
Mahout 的主要目标是创建一些可扩展的机器学习领域经典算法的实现，旨在帮助开发人员更加方便快捷地创建智能应用程序。Mahout现在已经包含了聚类、分类、推 荐引擎（协同过滤）和频繁集挖掘等广泛使用的数据挖掘方法。除了算法，Mahout还包含数据的输入/输出工具、与其他存储系统（如数据库、 MongoDB 或Cassandra）集成等数据挖掘支持架构。</p></li>
<li><p>Flume日志收集工具 <br>
将数据从产生、传输、处理并最终写入目标的路径的过程抽象为数据流。在具体的数据流中，数据源支持在Flume中定制数据发送方，从而支持收集各种不同协议数据。同时，Flume数据流提供对日志数据进行简单处理的能力，如过滤、 格式转换等。此外，Flume还具有能够将日志写往各种数据目标（可定制）的能力。</p></li>
</ol>



<h1 id="2-hdfs存储原理">2 HDFS存储原理</h1>



<h2 id="21-hdfs整体结构">2.1 HDFS整体结构</h2>

<p><img src="https://img-blog.csdn.net/20170525163730486?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMDI1NTgxOA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p>（1）NameNode <br>
  管理数据节点和文件块的映射关系；处理客户端对数据的读写请求。 <br>
  NameNode保存了两个核心的数据结构FsImage和EditLog。<strong>FsImage用于维护文件系统树以及元数据；EditLog记录了文件的操作</strong>。NameNode不持久化Block与DataNode的映射信息，而是在系统每次启动时扫描所有DataNode来重构这些信息。</p>

<p>（2）DataNode <br>
  负责数据的存储和读取；向NameNode定期发送自己的存储块信息；周期性地向NameNode发送心跳信息报告自己的状态。 <br>
  HDFS集群中只有一个NameNode，负责所有元数据的管理；有若干DataNode，每个DataNode运行在一个独立节点上。</p>

<p>（3）SecondaryNameNode <br>
  对NameNode进行备份。周期性地从<code>NameNode</code>下载EditLog与FsImage，将EditLog与FsImage合并得到FsImage.ckpt，将合并后的FsImage.ckpt上传到NameNode，更新NameNode的EditLog与FsImage。</p>

<p>（4）读写过程 <br>
  客户端向HDFS写文件时，先请求NameNode节点获取分配的存储位置，然后根据存储位置直接把数据写入DataNode；客户端向HDFS读数据时，先请求NameNode获取文件块和数据节点的映射关系，然后直接到数据节点访问相应位置的文件块</p>



<h2 id="22-关于block大小">2.2 关于Block大小</h2>

<p>（1）HDFS默认文件块Block大小为64MB，如果一个文件小于Block，则它并不占用整个Block空间大小。</p>

<p>（2）Block不宜过大，MapReduce的Map任务一次只能处理一个Block的数据，Block过大会使启动的Map数量过少，影响并行处理速度。</p>

<p>（3）HDFS无法高效存储大量小文件</p>

<blockquote>
  <p>检索效率：HDFS中NameNode的元数据保存在内存中，过多的小文件需要大量内存空间，降低数据检索效率； <br>
  寻址开销：访问大量小文件，需要不断从一个DataNode跳到另一个DataNode，寻址开销增大； <br>
  线程开销：MapReduce处理大量小文件时会产生过多的Map任务，线程管理开销会大大增加；</p>
</blockquote>



<h2 id="23-hdfs数据存取策略">2.3  HDFS数据存取策略</h2>

<p>（1）数据存放 <br>
  HDFS默认冗余参数是3，一个Block会保存在3个地方。两份副本存放在同一机架的不同机器上，第三个副本存放在不同机架的机器上，即保证高可靠性，又提高读写性能。 <br>
（2）数据读取 <br>
  客户端从名称节点获取不同副本的存放位置列表，当发现有数据副本的机架与客户端机架相同时，优先选择该副本；否则，随机选取一个副本。 <br>
（3）数据复制 <br>
  采用流水线复制策略。Client向HDFS写文件时，先将文件写到本地，并切分成若干块，每个块都向NameNode发送写请求。Client根据DataNode的使用情况，返回一个节点列表给客户端。客户端把数据首先写入第一个DataNode，同时把节点列表传给第一个DataNode；当第一个DataNode接收4KB数据时，写入本地，并向列表的第二个DataNode发起连接，把接收到的4KB数据和节点列表传给第二个DataNode；依次列推，列表中的多个数据节点形成一条数据复制流水线。当文件写完时，数据复制也同时完成。</p>



<h2 id="24-数据错误与恢复">2.4 数据错误与恢复</h2>

<p>NameNode出错：利用SecondaryNode中的FsImage与EditLog数据进行恢复</p>

<p>DataNode出错：DataNode定期向NameNode发送心跳信息报告自己的状态，超时未收到心跳信息会被标记为宕机，该DataNode不再可用。NameNode一旦发现某个Block的副本数量小于冗余因子，则对数据进行冗余复制。</p>

<p>数据出错：网络传输与磁盘错误等因素均会造成数据错误。读取数据时，客户端会采用MD5和shal对数据块进行校验；写数据时，将文件块的信息摘录写入同一路径的隐藏文件，在读取时进行校验，如果校验失败则请求读取另一副本，并报告NameNode。</p>



<h1 id="3-hdfs的shell操作">3 HDFS的Shell操作</h1>

<p>HDFS的shell操作中部分基于hadoop工具的操作已经被hdfs工具替代，但仍有一些基于hadoop工具的操作未被替代。 <br>
（1）hdfs工具 <br>
<img src="https://img-blog.csdn.net/20170526091409895?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMDI1NTgxOA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p>（2）hadoop工具 <br>
<img src="https://img-blog.csdn.net/20170526091503840?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMDI1NTgxOA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p>（3）主要命令</p>



<pre class="prettyprint"><code class=" hljs css"><span class="hljs-tag">hdfs</span> <span class="hljs-tag">namenode</span> <span class="hljs-tag">-format</span>                           格式化<span class="hljs-tag">DFS</span>文件系统
<span class="hljs-tag">hdfs</span> <span class="hljs-attr_selector">[namenode | secondaryNameNode | datanode]</span>  启动命令
<span class="hljs-tag">hdfs</span> <span class="hljs-tag">dfsadmin</span> <span class="hljs-tag">-safemode</span> <span class="hljs-attr_selector">[enter | leave]</span>         进入或退出安全模式</code></pre>

<p><img src="https://img-blog.csdn.net/20170526091649319?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMDI1NTgxOA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>



<pre class="prettyprint"><code class=" hljs lasso">hdfs balancer                                   手动负载均衡
hdfs getconf <span class="hljs-preprocessor">[</span><span class="hljs-attribute">-namenodes</span> <span class="hljs-subst">|</span> <span class="hljs-attribute">-secondaryNameNodes</span> <span class="hljs-subst">|</span> <span class="hljs-attribute">-backupNodes</span> <span class="hljs-subst">|</span> <span class="hljs-attribute">-nnRpcAddresses</span><span class="hljs-preprocessor">]</span><span class="hljs-markup">    获取配置信息

hadoop 
hadoop jar &lt;xx.jar&gt; &lt;mainClass&gt; </span><span class="hljs-preprocessor">[</span>args, args<span class="hljs-preprocessor">]</span><span class="hljs-markup">        运行jar文件
hadoop fs                                   运行通用文件系统用户客户端
hdfs dfs                                        运行hadoop支持的文件系统命令</span></code></pre>

<p>hadoop fs命令参数与hdfs dfs命令参数完全一致，HDFS中hdfs dfs工具替代了hadoop fs工具： <br>
<img src="https://img-blog.csdn.net/20170526093202893?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMDI1NTgxOA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>



<h1 id="4-hdfs-java端api">4 HDFS Java端API</h1>

<p>（1）Configuration类</p>



<pre class="prettyprint"><code class=" hljs axapta"><span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">Configuration</span> <span class="hljs-inheritance"><span class="hljs-keyword">extends</span></span> <span class="hljs-title">Object</span> <span class="hljs-inheritance"><span class="hljs-keyword">implements</span></span> <span class="hljs-title">Iterable</span>&lt;<span class="hljs-title">Map</span>.<span class="hljs-title">Entry</span>&lt;<span class="hljs-title">String</span>,<span class="hljs-title">String</span>&gt;&gt;, <span class="hljs-title">Writable</span> {</span>
<span class="hljs-comment">// 提供配置参数，如果没有具体指明，hadoop从core-site.xml中获取配置信息</span>
    <span class="hljs-comment">//构造方法</span>
    <span class="hljs-keyword">public</span> Configuration() {}
    <span class="hljs-keyword">public</span> Configuration(Configuration conf) {}
    <span class="hljs-keyword">public</span> Configuration(<span class="hljs-keyword">boolean</span> loadDefaults) {}<span class="hljs-comment">//是否从默认文件core-site.xml中读取配置信息</span>

    <span class="hljs-comment">//set与get</span>
    <span class="hljs-keyword">public</span> String get(String name) {}<span class="hljs-comment">//根据属性名获取值，属性不存在时返回null</span>
    <span class="hljs-keyword">public</span> String get(String name, String defaultValue){}
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> set(String name, String value) {}

    <span class="hljs-comment">//迭代遍历</span>
    <span class="hljs-keyword">public</span> Iterator&lt;Map.Entry&lt;String,String&gt;&gt; iterator(){}
}</code></pre>

<p>（2）FileSystem <br>
第一个无参构造方法返回默认文件系统，即core-site.xml中fs.defaultFS项，若未设置则默认返回本地文件系统；其他带URI参数的构造方法通过URI参数指定文件系统。</p>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">public</span> <span class="hljs-keyword">abstract</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">FileSystem</span> <span class="hljs-keyword">extends</span> <span class="hljs-title">Configured</span> <span class="hljs-keyword">implements</span> <span class="hljs-title">Closeable</span> {</span>
<span class="hljs-comment">//通用文件系统的抽象基类</span>
    <span class="hljs-comment">//工厂模式，获取实例</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> FileSystem <span class="hljs-title">get</span>(Configuration conf) <span class="hljs-keyword">throws</span> IOException {}
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> FileSystem <span class="hljs-title">get</span>(URI uri, Configuration conf, String user) <span class="hljs-keyword">throws</span> IOException
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> FileSystem <span class="hljs-title">get</span>(URI uri, Configuration conf) <span class="hljs-keyword">throws</span> IOException {}
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> LocalFileSystem <span class="hljs-title">getLocal</span>(Configuration conf) <span class="hljs-keyword">throws</span> IOException {}
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> FileSystem <span class="hljs-title">newInstance</span>(URI uri, Configuration conf) <span class="hljs-keyword">throws</span> IOException

    //创建输出/输入流对象
    <span class="hljs-keyword">public</span> FSDataOutputStream <span class="hljs-title">create</span>(Path f, <span class="hljs-keyword">boolean</span> overwrite, <span class="hljs-keyword">int</span> bufferSize) <span class="hljs-keyword">throws</span> IOException
    <span class="hljs-keyword">public</span> FSDataInputStream <span class="hljs-title">open</span>(Path f) <span class="hljs-keyword">throws</span> IOException
    <span class="hljs-keyword">public</span> FSDataOutputStream <span class="hljs-title">append</span>(Path f, <span class="hljs-keyword">int</span> bufferSize) <span class="hljs-keyword">throws</span> IOException
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">concat</span>(Path trg, Path[] psrcs) <span class="hljs-keyword">throws</span> IOException{} <span class="hljs-comment">//Concat existing files together</span>

    <span class="hljs-comment">//文件移动</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">moveFromLocalFile</span>(Path src, Path dst) <span class="hljs-keyword">throws</span> IOException{} <span class="hljs-comment">//从本地向HDFS移动文件</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">moveToLocalFile</span>(Path src, Path dst) <span class="hljs-keyword">throws</span> IOException{} <span class="hljs-comment">//从HDFS向本地移动文件</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">copyFromLocalFile</span>(<span class="hljs-keyword">boolean</span> delSrc, <span class="hljs-keyword">boolean</span> overwrite, Path src, Path dst) <span class="hljs-keyword">throws</span> IOException{} <span class="hljs-comment">//从本地向HDFS复制文件</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">copyToLocalFile</span>(<span class="hljs-keyword">boolean</span> delSrc, Path src, Path dst) <span class="hljs-keyword">throws</span> IOException{} <span class="hljs-comment">//从HDFS向本地复制文件</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">abstract</span> <span class="hljs-keyword">boolean</span> <span class="hljs-title">rename</span>(Path src, Path dst) <span class="hljs-keyword">throws</span> IOException{} <span class="hljs-comment">//在本地磁盘间或HDFS之间移动文件，即重命名</span>

    <span class="hljs-comment">//判断方法</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">boolean</span> <span class="hljs-title">exists</span>(Path f) <span class="hljs-keyword">throws</span> IOException{} <span class="hljs-comment">//判断文件或目录是否存在</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">boolean</span> <span class="hljs-title">isDirectory</span>(Path f) <span class="hljs-keyword">throws</span> IOException{}
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">boolean</span> <span class="hljs-title">isFile</span>(Path f) <span class="hljs-keyword">throws</span> IOException{}

    <span class="hljs-comment">//创建目录</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">boolean</span> <span class="hljs-title">mkdirs</span>(Path f) <span class="hljs-keyword">throws</span> IOException{}

    <span class="hljs-comment">//删除文件或目录</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">abstract</span> <span class="hljs-keyword">boolean</span> <span class="hljs-title">delete</span>(Path f, <span class="hljs-keyword">boolean</span> recursive) <span class="hljs-keyword">throws</span> IOException

    //获取文件统计信息
    <span class="hljs-keyword">public</span> FsStatus <span class="hljs-title">getStatus</span>(Path p) <span class="hljs-keyword">throws</span> IOException
    <span class="hljs-keyword">public</span> FileStatus[] <span class="hljs-title">listStatus</span>(Path f, PathFilter filter) <span class="hljs-keyword">throws</span> FileNotFoundException, IOException

    //关闭
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">close</span>() <span class="hljs-keyword">throws</span> IOException{}
}</code></pre>

<p>（3）FSDataInputStream,继承自java.io.DataInputStream</p>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">FSDataInputStream</span> <span class="hljs-keyword">extends</span> <span class="hljs-title">DataInputStream</span> <span class="hljs-keyword">implements</span>...{</span>
    <span class="hljs-comment">//构造方法</span>
    <span class="hljs-keyword">public</span> <span class="hljs-title">FSDataInputStream</span>(InputStream in) {}

    <span class="hljs-comment">//读指针相关方法</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">seek</span>(<span class="hljs-keyword">long</span> desired) <span class="hljs-keyword">throws</span> IOException{}
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">long</span> <span class="hljs-title">getPos</span>() <span class="hljs-keyword">throws</span> IOException{}

    <span class="hljs-comment">//读操作</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">int</span> <span class="hljs-title">read</span>(<span class="hljs-keyword">long</span> position, <span class="hljs-keyword">byte</span>[] buffer, <span class="hljs-keyword">int</span> offset, <span class="hljs-keyword">int</span> length) <span class="hljs-keyword">throws</span> IOException
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">readFully</span>(<span class="hljs-keyword">long</span> position, <span class="hljs-keyword">byte</span>[] buffer, <span class="hljs-keyword">int</span> offset, <span class="hljs-keyword">int</span> length) <span class="hljs-keyword">throws</span> IOException
    /*<span class="hljs-title">read</span>(<span class="hljs-keyword">byte</span>[] b)方法实质是读取流上的字节直到流上没有字节为止，如果当声明的字节数组长度大于流上的数据长度时就提前返回，
    而<span class="hljs-title">readFully</span>(<span class="hljs-keyword">byte</span>[] b)方法是读取流上指定长度的字节数组，也就是说如果声明了长度为len的字节数组，
    <span class="hljs-title">readFully</span>(<span class="hljs-keyword">byte</span>[] b)方法只有读取len长度个字节的时候才返回，否则阻塞等待，如果超时，则会抛出异常 EOFException*/
}</code></pre>

<p>（4）FSDataOutputStream,继承自java.io.DataOutputStream</p>

<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">FSDataOutputStream</span> <span class="hljs-keyword">extends</span> <span class="hljs-title">DataOutputStream</span> <span class="hljs-keyword">implements</span>...{</span>
<span class="hljs-comment">//写方法全部继承自父类</span>
}</code></pre>

<p>（5）FileStatus</p>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">FileStatus</span>{</span>
    <span class="hljs-comment">//构造方法</span>
    <span class="hljs-keyword">public</span> <span class="hljs-title">FileStatus</span>(){}

    <span class="hljs-comment">//判断方法</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">boolean</span> <span class="hljs-title">isFile</span>(){};
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">boolean</span> <span class="hljs-title">isDirectory</span>(){};

    <span class="hljs-comment">//get方法</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">long</span> <span class="hljs-title">getLen</span>(){};
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">long</span> <span class="hljs-title">getBlockSize</span>(){};
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">short</span> <span class="hljs-title">getReplication</span>(){};
    <span class="hljs-keyword">public</span> String <span class="hljs-title">getOwner</span>(){};

    <span class="hljs-keyword">public</span> Path <span class="hljs-title">getPath</span>(){};

    <span class="hljs-comment">//set方法</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">setPath</span>(Path p){};
    <span class="hljs-keyword">protected</span> <span class="hljs-keyword">void</span> <span class="hljs-title">setOwner</span>(String owner){};
}</code></pre>

<p>（6）Path</p>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">Path</span> <span class="hljs-keyword">extends</span> <span class="hljs-title">Object</span> <span class="hljs-keyword">implements</span> <span class="hljs-title">Comparable</span> {</span>
    <span class="hljs-comment">//构造方法</span>
    <span class="hljs-keyword">public</span> <span class="hljs-title">Path</span>(String parent, String child){}
    <span class="hljs-keyword">public</span> <span class="hljs-title">Path</span>(URI uri){}

    <span class="hljs-comment">//合并路径</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> Path <span class="hljs-title">mergePaths</span>(Path path1, Path path2) {}

    <span class="hljs-keyword">public</span> URI <span class="hljs-title">toUri</span>(){};

    <span class="hljs-comment">//get方法</span>
    <span class="hljs-keyword">public</span> FileSystem <span class="hljs-title">getFileSystem</span>(Configuration conf) <span class="hljs-keyword">throws</span> IOException {}
    <span class="hljs-comment">//该方法可获取fs对象，在输出路径存在的情况下删除输出路径</span>

    <span class="hljs-keyword">public</span> String <span class="hljs-title">getName</span>(){} <span class="hljs-comment">//Returns the final component of this path</span>
    <span class="hljs-keyword">public</span> Path <span class="hljs-title">getParent</span>(){} <span class="hljs-comment">//Returns the parent of a path or null if at root</span>

    <span class="hljs-comment">//判断方法</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">boolean</span> <span class="hljs-title">isAbsolute</span>(){};
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">boolean</span> <span class="hljs-title">isRoot</span>(){};
}</code></pre>

<p>（7）URI，java.net.URI</p>



<pre class="prettyprint"><code class=" hljs axapta"><span class="hljs-keyword">public</span> <span class="hljs-keyword">final</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">URI</span> <span class="hljs-inheritance"><span class="hljs-keyword">extends</span></span> <span class="hljs-title">Object</span> <span class="hljs-inheritance"><span class="hljs-keyword">implements</span></span> <span class="hljs-title">Comparable</span>&lt;<span class="hljs-title">URI</span>&gt;, <span class="hljs-title">Serializable</span> {</span>
    <span class="hljs-comment">//构造方法</span>
    <span class="hljs-keyword">public</span> URI(String <span class="hljs-keyword">str</span>) {};
    <span class="hljs-keyword">public</span> URI(String scheme, String host, String path, String fragment) {};
}</code></pre>

<p>（8）PathFilter</p>



<pre class="prettyprint"><code class=" hljs cs"><span class="hljs-keyword">public</span> <span class="hljs-keyword">interface</span> PathFilter{
    boolean accept(Path path);
}</code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>