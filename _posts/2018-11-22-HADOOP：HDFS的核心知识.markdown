---
layout:     post
title:      HADOOP：HDFS的核心知识
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<hr>

<p><strong>Hadoop的核心是mapreduce和hdfs。</strong></p>



<h2 id="hdfs"><strong>hdfs</strong></h2>

<hr>



<h3 id="什么是hdfs及hdfs架构设计"><strong>什么是HDFS及HDFS架构设计？</strong></h3>

<blockquote>
  <p>全称Hadoop Distributed File System(即Hadoop的分布式文件系统)。Hadoop分布式文件系统(HDFS)被设计成适合运行在通用硬件(commodity hardware)上的分布式文件系统。它和现有的分布式文件系统有很多共同点。但同时，它和其他的分布式文件系统的区别也是很明显的。HDFS是一个高度容错性的系统，适合部署在廉价的机器上。HDFS能提供高吞吐量的数据访问，非常适合大规模数据集上的应用。HDFS是Apache Hadoop Core项目的一部分。</p>
  
  <hr>
  
  <p>架构设计</p>
  
  <ol>
  <li><p>超大文件：适合存储几百M G PB 的数据。</p></li>
  <li><p>流式数据访问：一次写入，多次读取是最高效的访问模式。数据集通常由数据源复制而来，接着长时间的在此数据集上进行各种数据分析，每次都涉及部分数据或全部，因此读取整个数据集的延迟时间比读取第一条记录的延迟时间更为重要。</p></li>
  <li><p>商用硬件：HDFS文件系统设计运行在商用硬件的集群上的。</p></li>
  <li><p>低时间延迟的数据访问：要求低时间延迟访问数据的应用，例如几百毫秒范围，不适合在HDFS上运行。HDFS是以高数据量吞吐量进行优化的，这可能会以提高时间延迟为代价，对于低延迟的访问数据要求，HBASE更合适。</p></li>
  <li><p>大量的小文件：由于namenode将文件系统的元数据存储在内存中，因此该文件系统所能存储的文件总数受限于namenode的内存容量。根据经验，每个文件，目录和数据块的存储信息大约占150字节。因此，举个例子来说的话，存储100万个文件，每个文件占一个数据库，那至少需要300M内存，因此存储上百万个文件是可行的，但若存储上亿个就不可行了。</p></li>
  </ol>
</blockquote>

<hr>



<h3 id="hdfs体系结构简介及优缺点"><strong>HDFS体系结构简介及优缺点？</strong></h3>

<blockquote>
  <p>HDFS是一个主/从(Master/slave)体系结构，从最终用户的角度来看，它就像传统的文件系统一样，可以通过目录路径对文件系统执行CRUD(Create、Read、Update、Delete)操作。但由于分布式存储的性质，HDFS集群拥有一个NameNode和一些DataNode。NameNode管理文件系统的元数据，DataNode存储实际的数据。客户端通过同NameNode和DataNode的交互访问文件系统。客户端联系NameNode以获取文件的元数据，而真正的文件I/O操作是直接和DataNode进行交互的。下面是HDFS总体结构示意图： <br>
  <img src="https://img-blog.csdn.net/20161228161516047?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMDcyNTY5MA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="体系结构" title=""></p>
  
  <hr>
</blockquote>

<p><strong>相关概念：</strong></p>



<h4 id="namenode"><strong>NameNode</strong></h4>

<blockquote>
  <p>NameNode可以看作是分布式文件系统中的管理者，主要负责管理文件系统的命名空间、集群配置信息和存储块的复制等，NameNode会将文件系统的Meta-data存储到内存中，这些信息主要包括了文件信息、每一个文件对应的文件块的信息和每一个文件块在DataNode的信息等。</p>
</blockquote>



<h4 id="secondary-namenode"><strong>Secondary NameNode</strong></h4>

<blockquote>
  <p>首先要明确一个问题，Secondary Node不是NameNode的热备，Secondary NameNode主要是辅助NameNode，分担其工作量；定期合并fsimage和fsedits，推送给NameNode；在紧急情况下，可以恢复NameNode。</p>
</blockquote>



<h4 id="datanode"><strong>DataNode</strong></h4>

<blockquote>
  <p>DataNode是文件存储的基本单元，他将Block存储在本地文件系统中，保存了Block的meta-data，同时周期性的将所有存在的Block信息发送给NameNode。slave存储实际的数据块，执行数据块的读写。</p>
</blockquote>



<h4 id="client"><strong>Client</strong></h4>

<blockquote>
  <p>文件切分与NameNode的交互，获取文件位置信息；与DataNode交互，读取或者写入数据；管理HDFS；访问HDFS。</p>
</blockquote>



<h4 id="文件写入"><strong>文件写入</strong></h4>

<pre><code>Client向NameNode发起文件读取的请求。
NameNode返回文件存储的DataNode的信息。
Client读取文件信息。
</code></pre>

<blockquote>
  <p>HDFS典型的部署时在一个专门的机器上运行NameNode，集群中的其他机器各运行一个DataNode；也可以在运行NameNode的机器上同时运行DataNode，或者一台机器上运行多个DataNode。一个集群只有一个NameNode的设计大大简化了系统架构。</p>
</blockquote>



<h4 id="优点"><strong>优点</strong></h4>

<blockquote>
  <p>超大文件：适合存储几百M G PB 的数据。</p>
  
  <p>流式数据访问：一次写入，多次读取是最高效的访问模式。数据集通常由数据源复制而来，接着长时间的在此数据集上进行各种数据分析，每次都涉及部分数据或全部，因此读取整个数据集的延迟时间比读取第一条记录的延迟时间更为重要。</p>
  
  <p>商用硬件：HDFS文件系统设计运行在商用硬件的集群上的。</p>
</blockquote>



<h4 id="缺点"><strong>缺点</strong></h4>

<blockquote>
  <p>低时间延迟的数据访问：要求低时间延迟访问数据的应用，例如几百毫秒范围，不适合在HDFS上运行。HDFS是以高数据量吞吐量进行优化的，这可能会以提高时间延迟为代价，对于低延迟的访问数据要求，HBASE更合适。</p>
  
  <p>大量的小文件：由于namenode将文件系统的元数据存储在内存中，因此该文件系统所能存储的文件总数受限于namenode的内存容量。根据经验，每个文件，目录和数据块的存储信息大约占150字节。因此，举个例子来说的话，存储100万个文件，每个文件占一个数据库，那至少需要300M内存，因此存储上百万个文件是可行的，但若存储上亿个就不可行了。</p>
</blockquote>



<h4 id="改进策略"><strong>改进策略</strong></h4>

<blockquote>
  <p>要想让HDFS能处理好小文件，有不少方法。</p>
  
  <blockquote>
    <ol>
    <li>利用SequenceFile、MapFile、Har等方式归档小文件，这个方法的原理就是把小文件归档起来管理，HBase就是基于此的。对于这种方法，如果想找回原来的小文件内容，那就必须得知道与归档文件的映射关系。</li>
    <li>横向扩展，一个Hadoop集群能管理的小文件有限，那就把几个Hadoop集群拖在一个虚拟服务器后面，形成一个大的Hadoop集群。</li>
    <li>等.</li>
    </ol>
  </blockquote>
</blockquote>

<hr>



<h3 id="hdfs如何存储数据"><strong>Hdfs如何存储数据？</strong></h3>

<p><strong>HDFS中的基础概念</strong></p>

<blockquote>
  <p><strong>Block</strong>：HDFS中的存储单元是每个数据块block，HDFS默认的最基本的存储单位是64M的数据块。和普通的文件系统相同的是，HDFS中的文件也是被分成64M一块的数据块存储的。不同的是，在HDFS中，如果一个文件大小小于一个数据块的大小，它是不需要占用整个数据块的存储空间的。更多的情况下会设置为128M(如果寻址时间为10ms,传输速率为100MB/s，为了使寻址时间仅占传输时间的1%，要将块大小的设置为100M，显示更接近于128M，随着磁盘传输速率的提升，这个值也会被设置的更大)。</p>
  
  <p><strong>NameNode</strong>：元数据节点。该节点用来管理文件系统中的命名空间，是master。其将所有的为了见和文件夹的元数据保存在一个文件系统树中，这些信息在硬盘上保存为了命名空间镜像（namespace image）以及修改日志（edit log），后面还会讲到。此外，NameNode还保存了一个文件包括哪些数据块，分布在哪些数据节点上。然而，这些信息不存放在硬盘上，而是在系统启动的时候从数据节点收集而成的。</p>
  
  <p><strong>DataNode</strong>：数据节点，是HDFS真正存储数据的地方。客户端（client）和元数据节点（NameNode）可以向数据节点请求写入或者读出数据块。此外，DataNode需要周期性的向元数据节点回报其存储的数据块信息。</p>
  
  <p><strong>Secondary NameNode</strong>:从元数据节点。从元数据节点并不是NameNode出现问题时候的备用节点，它的主要功能是周期性的将NameNode中的namespace image和edit log合并，以防log文件过大。此外，合并过后的namespace image文件也会在Secondary NameNode上保存一份，以防NameNode失败的时候，可以恢复。</p>
  
  <p><strong>edit log</strong>：修改日志，当文件系统客户端client进行写操作的时候，我们就要把这条记录放在修改日志中。在记录了修改日志后，NameNode则修改内存中的数据结构。每次写操作成功之前，edit log都会同步到文件系统中。</p>
  
  <p><strong>fsimage</strong>：命名空间镜像，它是内存中的元数据在硬盘上的checkpoint。当NameNode失败的时候，最新的checkpoint的元数据信息就会从fsimage加载到内存中，然后注意重新执行修改日志中的操作。而Secondary NameNode就是用来帮助元数据节点将内存中的元数据信息checkpoint到硬盘上的。        </p>
  
  <hr>
</blockquote>

<p>具体checkpoint的过程如下图： <br>
<img src="https://img-blog.csdn.net/20161228174548167?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMDcyNTY5MA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<blockquote>
  <p><strong>checkpoint</strong>的过程如下：Secondary NameNode通知NameNode生成新的日志文件，以后的日志都写到新的日志文件中。Secondary NameNode用http get从NameNode获得fsimage文件及旧的日志文件。Secondary NameNode将fsimage文件加载到内存中，并执行日志文件中的操作，然后生成新的fsimage文件。Secondary NameNode将新的fsimage文件用http post传回NameNode。NameNode可以将旧的fsimage文件及旧的日志文件，换为新的fsimage文件和新的日志文件(第一步生成的)，然后更新fstime文件，写入此次checkpoint的时间。这样NameNode中的fsimage文件保存了最新的checkpoint的元数据信息，日志文件也重新开始，不会变的很大了。</p>
  
  <hr>
</blockquote>



<h3 id="hdfs如何写入文件"><strong>Hdfs如何写入文件？</strong></h3>

<blockquote>
  <p>在HDFS中，文件的读写过程就是client和NameNode以及DataNode一起交互的过程。我们已经知道NameNode管理着文件系统的元数据，DataNode存储的是实际的数据，那么client就会联系NameNode以获取文件的元数据，而真正的文件读取操作是直接和DataNode进行交互的。 <br>
  <img src="https://img-blog.csdn.net/20161228170539505?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMDcyNTY5MA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>
  
  <hr>
  
  <blockquote>
    <p>客户端调用create()来创建文件，DistributedFileSystem用RPC调用元数据节点，在文件系统的命名空间中创建一个新的文件。元数据节点首先确定文件原来不存在，并且客户端有创建文件的权限，然后创建新文件。DistributedFileSystem返回DFSOutputStream，客户端用于写数据。</p>
    
    <p>客户端开始写入数据，DFSOutputStream将数据分成块，写入data queue。Data queue由Data Streamer读取，并通知元数据节点分配数据节点，用来存储数据块(每块默认复制3块)。分配的数据节点放在一个pipeline里。Data Streamer将数据块写入pipeline中的第一个数据节点。第一个数据节点将数据块发送给第二个数据节点。第二个数据节点将数据发送给第三个数据节点。DFSOutputStream为发出去的数据块保存了ack queue，等待pipeline中的数据节点告知数据已经写入成功。如果数据节点在写入的过程中失败：关闭pipeline，将ack queue中的数据块放入data queue的开始。</p>
    
    <p>当前的数据块在已经写入的数据节点中被元数据节点赋予新的标示，则错误节点重启后能够察觉其数据块是过时的，会被删除。失败的数据节点从pipeline中移除，另外的数据块则写入pipeline中的另外两个数据节点。元数据节点则被通知此数据块是复制块数不足，将来会再创建第三份备份。</p>
    
    <p>当客户端结束写入数据，则调用stream的close函数。此操作将所有的数据块写入pipeline中的数据节点，并等待ack queue返回成功。最后通知元数据节点写入完毕。</p>
  </blockquote>
</blockquote>



<h3 id="hdfs如何读取数据"><strong>Hdfs如何读取数据？</strong></h3>

<blockquote>
  <p>在HDFS中，文件的读写过程就是client和NameNode以及DataNode一起交互的过程。我们已经知道NameNode管理着文件系统的元数据，DataNode存储的是实际的数据，那么client就会联系NameNode以获取文件的元数据，而真正的文件读取操作是直接和DataNode进行交互的。 <br>
  <img src="https://img-blog.csdn.net/20161228170910132?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMDcyNTY5MA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>
  
  <hr>
  
  <p>客户端(client)用FileSystem的open()函数打开文件DistributedFileSystem用RPC调用元数据节点，得到文件的数据块信息。对于每一个数据块，元数据节点返回保存数据块的数据节点的地址。DistributedFileSystem返回FSDataInputStream给客户端，用来读取数据。客户端调用stream的read()函数开始读取数据。DFSInputStream连接保存此文件第一个数据块的最近的数据节点。Data从数据节点读到客户端(client)当此数据块读取完毕时，DFSInputStream关闭和此数据节点的连接，然后连接此文件下一个数据块的最近的数据节点。当客户端读取完毕数据的时候，调用FSDataInputStream的close函数。 在读取数据的过程中，如果客户端在与数据节点通信出现错误，则尝试连接包含此数据块的下一个数据节点。失败的数据节点将被记录，以后不再连接。</p>
</blockquote>

<hr>



<h3 id="hdfs的副本存放策略"><strong>Hdfs的副本存放策略？</strong></h3>

<p><strong>推荐一篇很棒的文章：</strong><a href="http://blog.csdn.net/androidlushangderen/article/details/51178253" rel="nofollow">http://blog.csdn.net/androidlushangderen/article/details/51178253</a></p>

<hr>



<h3 id="如何访问hdfs"><strong>如何访问hdfs？</strong></h3>

<hr>



<h4 id="命令行接口"><strong>命令行接口</strong></h4>

<pre><code>前提是已经设置了环境变量。
</code></pre>



<pre class="prettyprint"><code class="language-shell hljs livecodeserver">hadoop fs [<span class="hljs-command"><span class="hljs-keyword">command</span>]</span></code></pre>

<p><img src="https://img-blog.csdn.net/20161228173518465?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMDcyNTY5MA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<hr>



<h4 id="java接口"><strong>java接口</strong></h4>

<blockquote>
  <p>最常用的是通过FileSystem 类，打开进行流操作，传入的是hdfs地址.是命令行 hadoop fs 的实现。</p>
</blockquote>

<p>HDFS是一个分布式文件系统，既然是文件系统，就可以对其文件进行操作，比如说新建文件、删除文件、读取文件内容等操作。下面记录一下使用JAVA API对HDFS中的文件进行操作的过程。</p>

<p>对分HDFS中的文件操作主要涉及一下几个类：</p>

<p>Configuration类：该类的对象封转了客户端或者服务器的配置。</p>

<p>FileSystem类：该类的对象是一个文件系统对象，可以用该对象的一些方法来对文件进行操作。FileSystem fs = FileSystem.get(conf);通过FileSystem的静态方法get获得该对象。</p>

<p>FSDataInputStream和FSDataOutputStream：这两个类是HDFS中的输入输出流。分别通过FileSystem的open方法和create方法获得。</p>



<pre class="prettyprint"><code class="language-java hljs "><span class="hljs-keyword">package</span> com.hdfs;

<span class="hljs-keyword">import</span> java.io.FileInputStream;
<span class="hljs-keyword">import</span> java.io.IOException;
<span class="hljs-keyword">import</span> java.io.InputStream;

<span class="hljs-keyword">import</span> org.apache.hadoop.conf.Configuration;
<span class="hljs-keyword">import</span> org.apache.hadoop.fs.FSDataOutputStream;
<span class="hljs-keyword">import</span> org.apache.hadoop.fs.FileStatus;
<span class="hljs-keyword">import</span> org.apache.hadoop.fs.FileSystem;
<span class="hljs-keyword">import</span> org.apache.hadoop.fs.Path;
<span class="hljs-keyword">import</span> org.apache.hadoop.io.IOUtils;

<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">HdfsTest</span> {</span>

    <span class="hljs-comment">//创建新文件</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">createFile</span>(String dst , <span class="hljs-keyword">byte</span>[] contents) <span class="hljs-keyword">throws</span> IOException{
        Configuration conf = <span class="hljs-keyword">new</span> Configuration();
        FileSystem fs = FileSystem.get(conf);
        Path dstPath = <span class="hljs-keyword">new</span> Path(dst); <span class="hljs-comment">//目标路径</span>
        <span class="hljs-comment">//打开一个输出流</span>
        FSDataOutputStream outputStream = fs.create(dstPath);
        outputStream.write(contents);
        outputStream.close();
        fs.close();
        System.out.println(<span class="hljs-string">"文件创建成功！"</span>);
    }

    <span class="hljs-comment">//上传本地文件</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">uploadFile</span>(String src,String dst) <span class="hljs-keyword">throws</span> IOException{
        Configuration conf = <span class="hljs-keyword">new</span> Configuration();
        FileSystem fs = FileSystem.get(conf);
        Path srcPath = <span class="hljs-keyword">new</span> Path(src); <span class="hljs-comment">//原路径</span>
        Path dstPath = <span class="hljs-keyword">new</span> Path(dst); <span class="hljs-comment">//目标路径</span>
        <span class="hljs-comment">//调用文件系统的文件复制函数,前面参数是指是否删除原文件，true为删除，默认为false</span>
        fs.copyFromLocalFile(<span class="hljs-keyword">false</span>,srcPath, dstPath);

        <span class="hljs-comment">//打印文件路径</span>
        System.out.println(<span class="hljs-string">"Upload to "</span>+conf.get(<span class="hljs-string">"fs.default.name"</span>));
        System.out.println(<span class="hljs-string">"------------list files------------"</span>+<span class="hljs-string">"\n"</span>);
        FileStatus [] fileStatus = fs.listStatus(dstPath);
        <span class="hljs-keyword">for</span> (FileStatus file : fileStatus) 
        {
            System.out.println(file.getPath());
        }
        fs.close();
    }

    <span class="hljs-comment">//文件重命名</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">rename</span>(String oldName,String newName) <span class="hljs-keyword">throws</span> IOException{
        Configuration conf = <span class="hljs-keyword">new</span> Configuration();
        FileSystem fs = FileSystem.get(conf);
        Path oldPath = <span class="hljs-keyword">new</span> Path(oldName);
        Path newPath = <span class="hljs-keyword">new</span> Path(newName);
        <span class="hljs-keyword">boolean</span> isok = fs.rename(oldPath, newPath);
        <span class="hljs-keyword">if</span>(isok){
            System.out.println(<span class="hljs-string">"rename ok!"</span>);
        }<span class="hljs-keyword">else</span>{
            System.out.println(<span class="hljs-string">"rename failure"</span>);
        }
        fs.close();
    }
    <span class="hljs-comment">//删除文件</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">delete</span>(String filePath) <span class="hljs-keyword">throws</span> IOException{
        Configuration conf = <span class="hljs-keyword">new</span> Configuration();
        FileSystem fs = FileSystem.get(conf);
        Path path = <span class="hljs-keyword">new</span> Path(filePath);
        <span class="hljs-keyword">boolean</span> isok = fs.deleteOnExit(path);
        <span class="hljs-keyword">if</span>(isok){
            System.out.println(<span class="hljs-string">"delete ok!"</span>);
        }<span class="hljs-keyword">else</span>{
            System.out.println(<span class="hljs-string">"delete failure"</span>);
        }
        fs.close();
    }

    <span class="hljs-comment">//创建目录</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">mkdir</span>(String path) <span class="hljs-keyword">throws</span> IOException{
        Configuration conf = <span class="hljs-keyword">new</span> Configuration();
        FileSystem fs = FileSystem.get(conf);
        Path srcPath = <span class="hljs-keyword">new</span> Path(path);
        <span class="hljs-keyword">boolean</span> isok = fs.mkdirs(srcPath);
        <span class="hljs-keyword">if</span>(isok){
            System.out.println(<span class="hljs-string">"create dir ok!"</span>);
        }<span class="hljs-keyword">else</span>{
            System.out.println(<span class="hljs-string">"create dir failure"</span>);
        }
        fs.close();
    }

    <span class="hljs-comment">//读取文件的内容</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">readFile</span>(String filePath) <span class="hljs-keyword">throws</span> IOException{
        Configuration conf = <span class="hljs-keyword">new</span> Configuration();
        FileSystem fs = FileSystem.get(conf);
        Path srcPath = <span class="hljs-keyword">new</span> Path(filePath);
        InputStream in = <span class="hljs-keyword">null</span>;
        <span class="hljs-keyword">try</span> {
            in = fs.open(srcPath);
            IOUtils.copyBytes(in, System.out, <span class="hljs-number">4096</span>, <span class="hljs-keyword">false</span>); <span class="hljs-comment">//复制到标准输出流</span>
        } <span class="hljs-keyword">finally</span> {
            IOUtils.closeStream(in);
        }
    }


    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">main</span>(String[] args) <span class="hljs-keyword">throws</span> IOException {
        <span class="hljs-comment">//测试上传文件</span>
        <span class="hljs-comment">//uploadFile("D:\\c.txt", "/user/hadoop/test/");</span>
        <span class="hljs-comment">//测试创建文件</span>
        <span class="hljs-comment">/*byte[] contents =  "hello world 世界你好\n".getBytes();
        createFile("/user/hadoop/test1/d.txt",contents);*/</span>
        <span class="hljs-comment">//测试重命名</span>
        <span class="hljs-comment">//rename("/user/hadoop/test/d.txt", "/user/hadoop/test/dd.txt");</span>
        <span class="hljs-comment">//测试删除文件</span>
        <span class="hljs-comment">//delete("test/dd.txt"); //使用相对路径</span>
        <span class="hljs-comment">//delete("test1");    //删除目录</span>
        <span class="hljs-comment">//测试新建目录</span>
        <span class="hljs-comment">//mkdir("test1");</span>
        <span class="hljs-comment">//测试读取文件</span>
        readFile(<span class="hljs-string">"test1/d.txt"</span>);
    }

}</code></pre>

<pre class="prettyprint"><code class="language-java hljs ">String uri = <span class="hljs-string">"hdfs://grid-test-01:8020/user/hadoop/data/airline-csv/1987.csv"</span>;
Configuration conf = <span class="hljs-keyword">new</span> Configuration();
FileSystem fs = FileSystem.get(URI.create(uri), conf);
fs.(***);<span class="hljs-comment">//进行操作</span></code></pre>

<hr>



<h5 id="读取文件示例">读取文件示例</h5>



<pre class="prettyprint"><code class="language-java hljs ">String path = <span class="hljs-string">"hdfs://grid-test-01:8020/user/hadoop/output/selectotherfields/part-m-00000"</span>;<span class="hljs-comment">//指定路径</span>
Configuration conf = <span class="hljs-keyword">new</span> Configuration();
FileSystem fs = FileSystem.get(<span class="hljs-keyword">new</span> URI(path),conf);<span class="hljs-comment">//创建FileSystem</span>
InputStream is = fs.open(<span class="hljs-keyword">new</span> Path(path));<span class="hljs-comment">//打开输入流</span>
IOUtils.copyBytes(is,System.out,<span class="hljs-number">4096</span>,<span class="hljs-keyword">false</span>);</code></pre>



<h5 id="写入文件示例">写入文件示例</h5>



<pre class="prettyprint"><code class="language-java hljs ">String source = <span class="hljs-string">"/home/hadoop/dic.txt"</span>;<span class="hljs-comment">//输入文件路径</span>
String dic = <span class="hljs-string">"hdfs://grid-test-01:8020/user/hadoop/test/dic"</span>;<span class="hljs-comment">//目标路径+文件名</span>
Configuration conf = <span class="hljs-keyword">new</span> Configuration();
InputStream is = <span class="hljs-keyword">new</span> BufferedInputStream(<span class="hljs-keyword">new</span> FileInputStream(source));<span class="hljs-comment">//输入流</span>
FileSystem fs = FileSystem.get(URI.create(dic),conf);
OutputStream os = fs.create(<span class="hljs-keyword">new</span> Path(dic));<span class="hljs-comment">//输出流</span>
IOUtils.copyBytes(is,os,<span class="hljs-number">4096</span>,<span class="hljs-keyword">true</span>);</code></pre>



<h5 id="创建文件夹删除文件夹等操作">创建文件夹，删除文件夹等操作</h5>



<pre class="prettyprint"><code class="language-java hljs ">fs.mkdirs();<span class="hljs-comment">//创建文件夹</span>
fs.delete();<span class="hljs-comment">//删除文件夹</span>
。。。。。。</code></pre>

<hr>



<h4 id="rpc访问"><strong>rpc访问</strong></h4>

<pre><code>使用hdfs://namenode:8020 进行rpc访问
比如说默认的50070端口中，即有这种访问方式。http://namenode:50070
</code></pre>

<p><img src="https://img-blog.csdn.net/20161228173437206?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMDcyNTY5MA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<hr>



<h3 id="hdfs数据如何复制"><strong>Hdfs数据如何复制？</strong></h3>

<blockquote>
  <p>HDFS的数据复制，HDFS被设计成在一个大集群中可以跨机器可靠地存储海量的文件。它将每个文件存储成Block序列，除了最后一个Block，所有的Block都是同样的大小。文件的所有Block为了容错都会被冗余复制存储。每个文件的Block大小和Replication因子都是可配置的</p>
</blockquote>

<p><img src="https://img-blog.csdn.net/20161228175337568?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMDcyNTY5MA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<blockquote>
  <p>文件/users/sameerp/data/part-0的复制因子Replication值是2，块的ID列表包括1和3，可以看到块1和块3分别被冗余备份了两份数据块;文件/users/sameerp/data/part-1的复制因子Replication值是3，块的ID列表包括2、4、5，可以看到块2、4、5分别被冗余复制了三份。在HDFS中，文件所有块的复制会全权由NameNode进行管理，NameNode周期性地从集群中的每个DataNode接收心跳包和一个Blockreport。心跳包的接收表示该DataNode节点正常工作，而Blockreport包括了该DataNode上所有的Block组成的列表</p>
  
  <hr>
</blockquote>



<h3 id="namenode的热备和ha"><strong>Namenode的热备和HA</strong></h3>

<blockquote>
  <p>Hadoop 2.0中的HDFS增加了两个重大特性，HA和Federaion。HA即为High Availability，用于解决NameNode单点故障问题，该特性通过热备的方式为主NameNode提供一个备用者，一旦主NameNode出现故障，可以迅速切换至备NameNode，从而实现不间断对外提供服务。Federation即为“联邦”，该特性允许一个HDFS集群中存在多个NameNode同时对外提供服务，这些NameNode分管一部分目录（水平切分），彼此之间相互隔离，但共享底层的DataNode存储资源。</p>
  
  <p>在一个典型的HDFSHA场景中，通常由两个NameNode组成，一个处于active状态，另一个处于standby状态。Active NameNode对外提供服务，比如处理来自客户端的RPC请求，而Standby NameNode则不对外提供服务，仅同步active namenode的状态，以便能够在它失败时快速进行切换。</p>
</blockquote>

<p><strong>具体的实现和配置，可在hadoop.apache.org 上找到相关的说明</strong></p>

<hr>

<p><strong>参考文献</strong> <br>
1. Hadoop权威指南第三版 <br>
2. 百度百科 <br>
3. <a href="http://www.cnblogs.com/thinkpad/p/5173698.html" rel="nofollow">http://www.cnblogs.com/thinkpad/p/5173698.html</a> <br>
4. <a href="http://www.linuxidc.com/Linux/2014-12/110512.htm" rel="nofollow">http://www.linuxidc.com/Linux/2014-12/110512.htm</a></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>