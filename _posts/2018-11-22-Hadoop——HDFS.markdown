---
layout:     post
title:      Hadoop——HDFS
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/u012848709/article/details/83389267				</div>
								            <div id="content_views" class="markdown_views prism-tomorrow-night-eighties">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1><a id="_0"></a>楔子</h1>
<blockquote>
<p>Hadoop 使用的版本是<code>cdh5.3.6</code> CentOS6.9</p>
</blockquote>
<h2><a id="11_HDFS_3"></a>1.1 认识HDFS</h2>
<blockquote>
<p>HDFS和其他分布式文件有很多相似性。它最显著的差别在于高容错，以及其可以运行在廉价的商用机器上。HDFS基于流式的、高吞吐数据访问。适用于大数据处理。</p>
</blockquote>
<hr>
<blockquote>
<p>相关概念</p>
</blockquote>
<ol>
<li>
<p>数据块(Block)</p>
<blockquote>
<p>在HDFS中，块是一个抽象的概念，HDFS 2.X中默认大小是128M。HDFS文件系统也是分块进行存储，它是HDFS文件系统存储的逻辑单元。</p>
</blockquote>
</li>
<li>
<p>NameNode和DataNode</p>
<blockquote>
<p>HDFS文件系统有NameNode和DataNode两类节点组成，NameNode负责管理整个HDFS文件系统。整个文件系统的元数据包括FSimage和Edit Log两种。</p>
<p>​	DataNode负责存储真实的文件数据，并定时向NameNode发起心跳。</p>
</blockquote>
</li>
</ol>
<h2><a id="12_HDFS_28"></a>1.2 HDFS架构</h2>
<blockquote>
<p>HDFS是一个典型的主/从(master/slave)结构，一个HDFS集群由一个NameNode和多个DataNode组成。</p>
</blockquote>
<blockquote>
<p>​	NameNode是一个中央服务器，负责管理文件系统的名字空间，记录文件数据块的位置信息和副本信息，以及协助客户端读写文件。DataNode负责管理节点上的数据以及向NameNode发起心跳、汇报文件块信息。客户端通过NameNode和DataNode交互读写文件。当客户端需要操作一个文件时。首先练习NameNode获取文件的元数据(位置信息，块信息)，然后客户端和DataNode直接交互。</p>
<p>​	在HDFS文件系统中，一个文件在内部被分成一个或者多个数据块，这些数据块存储在一组DataNode上，DataNode在NameNode的指挥下对数据进行创建、删除、复制。</p>
</blockquote>
<h3><a id="111__40"></a>1.1.1 文件系统名字空间</h3>
<blockquote>
<p>HDFS支持传统的层次文件组织，与大多数其他文件系统类似，在HDFS文件系统中用户可以创建目录；创建、删除、移动和重命名文件。当前的HDFS不支持文件系统配额和权限设置，也不支持链接。(或许以后是支持)。NameNode维护文件系统的名字空间，任何对文件系统名字空间和文件属性的修改都将被NameNode记录下来。用户可以设置HDFS保存数据文件的副本数，这信息也是NamoNode保存。</p>
</blockquote>
<h3><a id="112__46"></a>1.1.2 数据复制</h3>
<blockquote>
<p>1、机架感知</p>
<p>​	副本存放策略是HDFS可靠性和性能的关键，副本存放策略也是有别于其他分布式文件系统。HDFS采用了Rack-aware的策略，这种策略提高了数据的可靠性、有效性和网络宽带的利用。在一个大的HDFS集群中，HDFS实例运行在多个机架的多台机器上，位于集群中不同的机架中的两个节点的通讯需要通过交换机、在一般情况下，同一台机架的两个节点间的宽带会比不同机架间的两个节点宽带大。</p>
<p>​	通常一个简单没有优化副本存放策略是将每个副本分别存放在一个机架上。这样可提高HDFS的数据可靠性，当某个Block出现故障时，允许从多个机架读取数据。但是这个简单的策略的缺点就是降低了宽带的利用，提高了写的代价，一个写操作需要多个机架中的数据节点交互。</p>
<p>2、安全模式</p>
<p>​	在HDFS启动过程中，NameNode启动会进入安全模式。在安全模式下，NameNode不行进行数据块的复制，只接受所有的DataNode发送的心跳包和数据块报告(BlockReport)。BlockReport包括了DataNode上所有数据块列表信息，每个数据块都有最小指定的副本，当数据块达到最小副本时，该数据块被认为是安全的。在一个比例的数据块被NameNode检测确认为安全后，再等待额外的时间30秒，NameNode自动退出安全模式。当检测到副本数不足的时候，该数据块会被复制到其他数据节点，直到达到最小副本数。</p>
</blockquote>
<h3><a id="113__60"></a>1.1.3 元数据持久化</h3>
<blockquote>
<p>​	NameNode上保存着HDFS的名字空间。FSimage和Edit Logs是NameNode中非常重要的两个元素。NameNode采用数据镜像文件FSimage和日志文件 Edit Logs的备份机制，其中镜像文件是某一时刻内存元数据的真实情况，而日志文件记录了该时刻以后所有元数据的操作。文件对于任何文件系统元素产生修改的操作，NameNode都会使用一种称为EditLog的事务日志记录下来。例如，在HDFS中创建一个文件，NameNode就会在日志文件中插入一条数据记录来表示；同样地，修改文件副本数也会往EditLog插入记录 。</p>
<p>​	NameNode在内存保存着整个文件系统的名字空间和文件数据块映射的映像。</p>
<p>​	DataNdoe将HDFS数据已文件的形式存储在本地的文件系统中，它并不指导有关HDFS的信息。它把每个HDFS数据块存储在本地文件系统的一个单独文件中，当一个DataNode启动时，它会扫描本地文件系统中所有的数据块，产生状态报告发送到NameNode。</p>
</blockquote>
<h3><a id="114__74"></a>1.1.4 文件系统的健壮性</h3>
<blockquote>
<p>​	文件系统的健壮性主要包括NameNode和DataNode的可靠性。</p>
<p>​	1、数据磁盘失效、心跳检测和副本复制</p>
<p>​	2、元数据磁盘失效</p>
<p>​	3、快照</p>
<p>​		快照支持某个时间的数据复制，当HDFS数据损坏的时候，可以恢复到过去一个已确定的时间点(HDFS目前还没实现快照功能)</p>
</blockquote>
<h2><a id="13_HDFS_90"></a>1.3 操作HDFS</h2>
<h3><a id="131_92"></a>1.3.1基本操作</h3>
<blockquote>
<p>​	Hadoop提供了两种方式操作HDFS，分别是脚本命令和API接口。</p>
</blockquote>
<blockquote>
<p>hadoop命令列表</p>
</blockquote>
<pre><code class="prism language-shell"><span class="token punctuation">[</span>grq@hadoop module<span class="token punctuation">]</span>$ hadoop --help
Usage: hadoop <span class="token punctuation">[</span>--config confdir<span class="token punctuation">]</span> COMMAND
       where COMMAND is one of:
  fs                   run a generic filesystem user client
  version              print the version
  jar <span class="token operator">&lt;</span>jar<span class="token operator">&gt;</span>            run a jar <span class="token function">file</span>
  checknative <span class="token punctuation">[</span>-a<span class="token operator">|</span>-h<span class="token punctuation">]</span>  check native hadoop and compression libraries availability
  distcp <span class="token operator">&lt;</span>srcurl<span class="token operator">&gt;</span> <span class="token operator">&lt;</span>desturl<span class="token operator">&gt;</span> copy <span class="token function">file</span> or directories recursively
  archive -archiveName NAME -p <span class="token operator">&lt;</span>parent path<span class="token operator">&gt;</span> <span class="token operator">&lt;</span>src<span class="token operator">&gt;</span>* <span class="token operator">&lt;</span>dest<span class="token operator">&gt;</span> create a hadoop archive
  classpath            prints the class path needed to get the
  credential           interact with credential providers
                       Hadoop jar and the required libraries
  daemonlog            get/set the log level <span class="token keyword">for</span> each daemon
 or
  CLASSNAME            run the class named CLASSNAME

Most commands print <span class="token function">help</span> when invoked w/o parameters.
<span class="token punctuation">[</span>grq@hadoop module<span class="token punctuation">]</span>$ 

</code></pre>
<blockquote>
<p><code>Hadoop fs</code>命令进入HDFS文件系统，但是推荐使用<code>hdfs dfs</code>。</p>
</blockquote>
<pre><code class="prism language-shell"><span class="token comment"># 创建目录， -p表示父目录不存在则创建</span>
hdfs dfs  <span class="token function">mkdir</span> -p /uert/tmp
<span class="token comment"># 上传文件到HDFS</span>
hdfs dfs  -put <span class="token punctuation">[</span>-f<span class="token punctuation">]</span><span class="token punctuation">[</span>-p<span class="token punctuation">]</span><span class="token operator">&lt;</span>localstc<span class="token operator">&gt;</span> <span class="token operator">&lt;</span>dst<span class="token operator">&gt;</span>
<span class="token comment">## -f 如果目录中存在此文件，则用本地文件覆盖此文件</span>
<span class="token comment">## -p 保留文件的访问和修改时间、所有者、权限信息</span>

<span class="token comment"># 查看文件</span>
hdfs dfs -cat <span class="token operator">&lt;</span>file<span class="token operator">&gt;</span>

<span class="token comment">#下载文件</span>
hdfs dfs <span class="token punctuation">[</span>-get <span class="token punctuation">[</span>-p<span class="token punctuation">]</span> <span class="token punctuation">[</span>-ignoreCrc<span class="token punctuation">]</span> <span class="token punctuation">[</span>-crc<span class="token punctuation">]</span> <span class="token operator">&lt;</span>src<span class="token operator">&gt;</span> <span class="token punctuation">..</span>. <span class="token operator">&lt;</span>localdst<span class="token operator">&gt;</span><span class="token punctuation">]</span>
<span class="token comment">## -crc 将crc文件拷贝到本地文件系统</span>
<span class="token comment">## -p 保留问价修改访问时间、所有者、权限</span>
<span class="token comment">## -ignoreCrc 不进行文件CRC校验</span>

<span class="token comment">#其他操作</span>
使用hdfs dfs查看

</code></pre>
<h3><a id="132_HDFS_152"></a>1.3.2 HDFS管理操作</h3>
<blockquote>
<p>​	HDFS文件系统的管理命令有hadoop distcp、hdfs namenode、hdfs fsck、hdfs dfsadmin命令等。</p>
</blockquote>
<blockquote>
<p>​	1、hadoop distcp并行拷贝</p>
<p>应用程序distcp,用在HDFS文件系统中并行的复制大数据量文件，一般用于同版本的HDFS之间拷贝。</p>
<p><code>hadoop distcp hdfs://name1:9000/usr/ hdfs://name2:9000/app</code></p>
<p>​	上述命令会把第一个集群文件/usr以及文件夹下的文件拷贝到第二个集群/app下，目录不存在会自动创建，可以拷贝多个源，需要注意的是所有源路劲必须是绝对路劲。</p>
<p>​	默认会跳过存在的文件，可以加速参数<code>-overwrite</code> 覆盖已目标已经存在的文件，也可以使用<code>-update</code>仅同步有更新的文件。</p>
<p>​	distcp是通过MapReduce作业完成的。只是次MapReduce作业只有Map没有Reduce。 使用参数<code>-m</code>可以指定Map的个数。</p>
</blockquote>
<blockquote>
<p>​	2、hdfs namenode命令</p>
<p>​	hdfs namenode所是对于NameNode进行管理，包括格式化HDFS文件系统；备份、升级、恢复NameNode等。使用方法如下</p>
</blockquote>
<pre><code class="prism language-shell"><span class="token punctuation">[</span>grq@hadoop module<span class="token punctuation">]</span>$ hdfs namenode --help
Usage: java NameNode <span class="token punctuation">[</span>-backup<span class="token punctuation">]</span> <span class="token operator">|</span> 
	<span class="token punctuation">[</span>-checkpoint<span class="token punctuation">]</span> <span class="token operator">|</span> 
	<span class="token punctuation">[</span>-format <span class="token punctuation">[</span>-clusterid cid <span class="token punctuation">]</span> <span class="token punctuation">[</span>-force<span class="token punctuation">]</span> <span class="token punctuation">[</span>-nonInteractive<span class="token punctuation">]</span> <span class="token punctuation">]</span> <span class="token operator">|</span> 
	<span class="token punctuation">[</span>-upgrade <span class="token punctuation">[</span>-clusterid cid<span class="token punctuation">]</span> <span class="token punctuation">[</span>-renameReserved<span class="token operator">&lt;</span>k-v pairs<span class="token operator">&gt;</span><span class="token punctuation">]</span> <span class="token punctuation">]</span> <span class="token operator">|</span> 
	<span class="token punctuation">[</span>-upgradeOnly <span class="token punctuation">[</span>-clusterid cid<span class="token punctuation">]</span> <span class="token punctuation">[</span>-renameReserved<span class="token operator">&lt;</span>k-v pairs<span class="token operator">&gt;</span><span class="token punctuation">]</span> <span class="token punctuation">]</span> <span class="token operator">|</span> 
	<span class="token punctuation">[</span>-rollback<span class="token punctuation">]</span> <span class="token operator">|</span> 
	<span class="token punctuation">[</span>-rollingUpgrade <span class="token operator">&lt;</span>rollback<span class="token operator">|</span>downgrade<span class="token operator">|</span>started<span class="token operator">&gt;</span> <span class="token punctuation">]</span> <span class="token operator">|</span> 
	<span class="token punctuation">[</span>-finalize<span class="token punctuation">]</span> <span class="token operator">|</span> 
	<span class="token punctuation">[</span>-importCheckpoint<span class="token punctuation">]</span> <span class="token operator">|</span> 
	<span class="token punctuation">[</span>-initializeSharedEdits<span class="token punctuation">]</span> <span class="token operator">|</span> 
	<span class="token punctuation">[</span>-bootstrapStandby<span class="token punctuation">]</span> <span class="token operator">|</span> 
	<span class="token punctuation">[</span>-recover <span class="token punctuation">[</span> -force<span class="token punctuation">]</span> <span class="token punctuation">]</span> <span class="token operator">|</span> 
	<span class="token punctuation">[</span>-metadataVersion <span class="token punctuation">]</span>  <span class="token punctuation">]</span>

Generic options supported are
-conf <span class="token operator">&lt;</span>configuration file<span class="token operator">&gt;</span>     specify an application configuration <span class="token function">file</span>
-D <span class="token operator">&lt;</span>property<span class="token operator">=</span>value<span class="token operator">&gt;</span>            use value <span class="token keyword">for</span> given property
-fs <span class="token operator">&lt;</span>local<span class="token operator">|</span>namenode:port<span class="token operator">&gt;</span>      specify a namenode
-jt <span class="token operator">&lt;</span>local<span class="token operator">|</span>jobtracker:port<span class="token operator">&gt;</span>    specify a job tracker
-files <span class="token operator">&lt;</span>comma separated list of files<span class="token operator">&gt;</span>    specify comma separated files to be copied to the map reduce cluster
-libjars <span class="token operator">&lt;</span>comma separated list of jars<span class="token operator">&gt;</span>    specify comma separated jar files to include <span class="token keyword">in</span> the classpath.
-archives <span class="token operator">&lt;</span>comma separated list of archives<span class="token operator">&gt;</span>    specify comma separated archives to be unarchived on the compute machines.

The general <span class="token function">command</span> line syntax is
bin/hadoop <span class="token function">command</span> <span class="token punctuation">[</span>genericOptions<span class="token punctuation">]</span> <span class="token punctuation">[</span>commandOptions<span class="token punctuation">]</span>

</code></pre>
<blockquote>
<p>​	3、hdfs fsck命令</p>
<p>​	此命令用于检测HDFS文件系统状态、例如文件大小、所有者、修改时间、文件分布情况、文件块是否损坏或者丢失等。</p>
<p>[grq@hadoop module]$ hdfs fsck --help</p>
</blockquote>
<pre><code class="prism language-shell"><span class="token punctuation">[</span>grq@hadoop module<span class="token punctuation">]</span>$ hdfs  <span class="token function">fsck</span> /img/15c01.jpg
18/10/26 10:37:46 WARN util.NativeCodeLoader: Unable to load native-hadoop library <span class="token keyword">for</span> your platform<span class="token punctuation">..</span>. using builtin-java classes where applicable
Connecting to namenode via http://hadoop:50070
FSCK started by grq <span class="token punctuation">(</span>auth:SIMPLE<span class="token punctuation">)</span> from /192.168.48.200 <span class="token keyword">for</span> path /img/15c01.jpg at Fri Oct 26 10:37:48 CST 2018
.Status: HEALTHY
 Total size:	87202 B
 Total dirs:	0
 Total files:	1
 Total symlinks:		0
 Total blocks <span class="token punctuation">(</span>validated<span class="token punctuation">)</span>:	1 <span class="token punctuation">(</span>avg. block size 87202 B<span class="token punctuation">)</span>
 Minimally replicated blocks:	1 <span class="token punctuation">(</span>100.0 %<span class="token punctuation">)</span>
 Over-replicated blocks:	0 <span class="token punctuation">(</span>0.0 %<span class="token punctuation">)</span>
 Under-replicated blocks:	0 <span class="token punctuation">(</span>0.0 %<span class="token punctuation">)</span>
 Mis-replicated blocks:		0 <span class="token punctuation">(</span>0.0 %<span class="token punctuation">)</span>
 Default replication factor:	1
 Average block replication:	1.0
 Corrupt blocks:		0
 Missing replicas:		0 <span class="token punctuation">(</span>0.0 %<span class="token punctuation">)</span>
 Number of data-nodes:		1<span class="token comment">#1个数据节点</span>
 Number of racks:		1<span class="token comment"># 1个机架</span>
FSCK ended at Fri Oct 26 10:37:48 CST 2018 <span class="token keyword">in</span> 5 milliseconds


The filesystem under path <span class="token string">'/img/15c01.jpg'</span> is HEALTHY

</code></pre>
<h2><a id="14_API_248"></a>1.4 API接口</h2>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>