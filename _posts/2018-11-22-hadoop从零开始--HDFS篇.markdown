---
layout:     post
title:      hadoop从零开始--HDFS篇
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载，转载请注明出处：http:					https://blog.csdn.net/qq_26803795/article/details/64534239				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>一、HDFS体系结构与基本概念</p>

<p>珍惜生命，拒绝废话！ <br>
一句话概括：<strong>HDFS</strong>是distributed file system的简写，是众多分布式文件管理系统中的一种，不适合小文件，重要的事情再说一遍，不适合小文件。 <br>
<img src="https://img-blog.csdn.net/20170321213245978?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMjY4MDM3OTU=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="结构图" title=""></p>

<p><strong>二、HDFS的shell操作</strong> <br>
这里总结大部分常用命令，不再过多解释，建议有linux基础的情况下学习。 <br>
<strong>1、基础命令</strong> <br>
hdfs dfs -ls / 查看HDFS根目录  <br>
hdfs dfs -mkdir /test 在根目录创建一个目录test <br>
hdfs dfs -mkdir /test1 在根目录创建一个目录test1 <br>
echo -e ‘hadoop second lesson’ &gt;test.txt <br>
hdfs dfs -put ./test.txt /test 或 <br>
hdfs dfs -copyFromLocal ./test.txt /test  <br>
hdfs dfs -get /test/test.txt  <br>
hdfs dfs -getToLocal /test/test.txt  <br>
hdfs dfs -cp /test/test.txt /test1  <br>
hdfs dfs -rm /test1/test.txt  <br>
hdfs dfs -mv /test/test.txt /test1  <br>
hdfs dfs -rmr /test1  <br>
hdfs dfs -appendToFile</p>

<p><strong>2、dfsadmin命令</strong></p>

<p><strong>配额</strong> bin/hdfs dfsadmin -setQuota 10 lisi <br>
-clrQuota 清空 <br>
bin/hdfs dfsadmin -setSpaceQuota 4k /lisi/ <br>
-clrSpaceQuota 清空</p>

<p><strong>查看</strong> bin/hdfs dfs -count -q -h /lisi</p>

<p><strong>进出安全模式</strong> bin/hdfs dfsadmin -safemode enter/leave</p>

<p>hadoop在启动的时候会默认进入安全模式，不过不用担心，30秒后自动退出安全模式。</p>

<p><strong>3、httpFS访问</strong> <br>
很多人认为httpFS访问不重要，其实不然，前面的shell命令必须要在hadoop环境下才能执行，而httpFS命令却可以摆脱这种限制，重要性不再多说。</p>

<p>编辑文件：httpfs-env.sh <br>
编辑文件：core-site.xml <br>
 <br>
    hadoop.proxyuser.root.hosts <br>
    *//这里是无限制的意思 <br>
 <br>
 <br>
    hadoop.proxyuser.root.groups <br>
    * <br>
 <br>
编辑文件hdfs-site.xml <br>
 <br>
          dfs.webhdfs.enabled <br>
           true <br>
</p>

<p>重新启动namenode，执行sbin/httpfs.sh start <br>
执行命令：  curl -i “<a href="http://ip:14000/webhdfs/v1?user.name=root&amp;op=LISTSTATUS" rel="nofollow">http://ip:14000/webhdfs/v1?user.name=root&amp;op=LISTSTATUS</a>”</p>

<p>更多命令参考<a href="http://hadoop.apache.org/docs/r2.6.0/hadoop-project-dist/hadoop-hdfs/WebHDFS.html" rel="nofollow">http://hadoop.apache.org/docs/r2.6.0/hadoop-project-dist/hadoop-hdfs/WebHDFS.html</a></p>

<p><strong>三、工作流程</strong></p>

<p><strong>1、namenode</strong> <br>
是整个文件系统的管理节点。它维护着整个文件系统的文件目录树，文件/目录的元信息和每个文件对应的数据块列表。接收用户的操作请求。（老板）</p>

<p><strong>1.1、这个boss中包括下列文件：</strong> <br>
<strong>fsimage</strong>:元数据镜像文件。存储某一时段NameNode内存元数据信息。 <br>
<strong>edits</strong>:操作日志文件。 <br>
<strong>fstime</strong>:保存最近一次checkpoint的时间 <br>
另外这些文件都保存在linux的文件系统中</p>

<p>1.2、如何查看namenode内容</p>

<p>..启动服务器bin/hdfs oiv -i 某个fsimage文件</p>

<p>..查看内容bin/hdfs dfs -ls  -R webhdfs://127.0.0.1:5978/</p>

<p>..导出结果bin/hdfs oiv -p XML -i  tmp/dfs/name/current/fsimage_0000000000000000055  -o fsimage.xml</p>

<p>..查看edtis内容bin/hdfs oev -i tmp/dfs/name/current/edits_0000000000000000057-0000000000000000186   -o edits.xml</p>

<p><strong>2、DataNode</strong> <br>
提供真实文件数据的存储服务（打工的）</p>

<p><strong>2.1、员工包含下列文件</strong></p>

<p><strong>文件块（block）：</strong>最基本的存储单位。对于文件内容而言，一个文件的长度大小是size，那么从文件的０偏移开始，按照固定的大小，顺序对文件进行划分并编号，划分好的每一个块称一个Block。HDFS默认Block大小是128MB，以一个256MB文件，共有256/128=2个Block. <br>
不同于普通文件系统的是，HDFS中，如果一个文件小于一个数据块的大小，并不占用整个数据块存储空间！</p>

<p><strong>Replication：</strong>多复本。默认是三个。</p>

<hr>

<p><strong>数据存储流程（staging）总结：</strong> <br>
HDFS client上传数据到HDFS时，会首先在本地缓存数据，当数据达到一个block大小时，请求NameNode分配一个block。NameNode会把block所在的DataNode的地址告诉HDFS client。HDFS client会直接和DataNode通信，把数据写到DataNode节点一个block文件中。</p>

<p><strong>四、数据存储解析</strong></p>

<p><strong>1、读文件解析</strong> <br>
<img src="https://img-blog.csdn.net/20170322101608670?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMjY4MDM3OTU=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="数据存储之读文件解析" title=""></p>

<p>1.首先调用FileSystem对象的<strong>open</strong>方法，其实是一个DistributedFileSystem的实例</p>

<p>2.DistributedFileSystem通过<strong>rpc</strong>获得文件的第一批个block的<strong>locations</strong>，同一block按照重复数会返回多个locations，这些locations按照<strong>hadoop拓扑结构</strong>排序，距离客户端近的排在前面.</p>

<p>3.前两步会返回一个<strong>FSDataInputStream</strong>对象，该对象会被封装成<strong>DFSInputStream</strong>对象，DFSInputStream可以方便的管理datanode和namenode数据流。客户端调用read方法，DFSInputStream最会找出离客户端最近的datanode并连接。</p>

<p>4.数据从<strong>datanode</strong>源源不断的流向客户端。</p>

<p>5.如果第一块的数据读完了，就会<strong>关闭</strong>指向第一块的datanode连接，接着读取下一块。这些操作对客户端来说是透明的，客户端的角度看来只是读一个<strong>持续不断</strong>的流。</p>

<p>6.如果第一批block都读完了，DFSInputStream就会去namenode找**下一批**blocks的location，然后继续读，如果所有的块都读完，这时就会关闭掉所有的流。</p>



<h2 id="注意"><strong>注意：</strong></h2>

<p>如果在读数据的时候，DFSInputStream和datanode的通讯发生异常，就会尝试正在读的block的排第二近的datanode,并且会记录哪个datanode发生错误，剩余的blocks读的时候就会直接跳过该datanode。DFSInputStream也会检查block数据校验和，如果发现一个坏的block,就会先报告到namenode节点，然后DFSInputStream在其他的datanode上读该block的镜像</p>

<p><em>该设计的方向就是客户端直接连接datanode来检索数据并且namenode来负责为每一个block提供最优的datanode，namenode仅仅处理block location的请求，这些信息都加载在namenode的内存中，hdfs通过datanode集群可以承受大量客户端的并发访问。</em></p>

<p>2、写文件解析 <br>
<img src="https://img-blog.csdn.net/20170322102437502?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMjY4MDM3OTU=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="数据存储之写文件解析" title=""></p>

<p>1.客户端通过调用DistributedFileSystem的<strong>create</strong>方法创建新文件</p>

<p>2.DistributedFileSystem通过RPC调用namenode去创建一个没有blocks关联的新文件，创建前，namenode会做各种<strong>校验</strong>，比如文件是否存在，客户端有无权限去创建等。如果校验通过，namenode就会记录下新文件，否则就会抛出IO异常.</p>

<p>3.前两步结束后会返回<strong>FSDataOutputStream</strong>的对象，和读文件的时候相似，FSDataOutputStream被封装成DFSOutputStream.DFSOutputStream可以协调namenode和datanode。客户端开始写数据到DFSOutputStream,DFSOutputStream会把数据切成一个个小<strong>packet</strong>，然后排成队列<strong>data quene</strong>。</p>

<p>4.DataStreamer会去处理接受<strong>data quene</strong>，他先问询namenode这个新的block最适合存储的在哪几个datanode里（参考第二小节），比如重复数是3，那么就找到3个最适合的datanode，把他们排成一个<strong>pipeline</strong>。DataStreamer把packet按队列输出到管道的第一个datanode中，第一个datanode又把packet输出到第二个datanode中，以此类推。</p>

<p>5.DFSOutputStream还有一个对列叫<strong>ack quene</strong>，也是有packet组成，等待datanode的收到响应，当<strong>pipeline</strong>中的所有datanode都表示已经收到的时候，这时akc quene才会把对应的packet包移除掉。</p>

<p>6.客户端完成写数据后调用<strong>close</strong>方法关闭写入流</p>

<p>7.DataStreamer把剩余得包都刷到pipeline里然后等待ack信息，收到最后一个ack后，通知datanode把文件标示为<strong>已完成</strong>。</p>



<h2 id="注意-1"><strong>注意：</strong></h2>

<p>如果在写的过程中某个datanode发生错误，会采取以下几步： <br>
1) pipeline被关闭掉； <br>
2)为了防止丢包ack quene里的packet会同步到data quene里； <br>
3)把产生错误的datanode上当前在写但未完成的block删掉； <br>
4）block剩下的部分被写到剩下的两个正常的datanode中； <br>
5）namenode找到另外的datanode去创建这个块的复制。 <br>
当然，这些操作对客户端来说是无感知的。</p>

<p>另外要注意得一点，客户端执行write操作后，写完得block才是可见的，正在写的block对客户端是不可见的，只有调用sync方法，客户端才确保该文件被写操作已经全部完成，当客户端调用close方法时会默认调用sync方法。是否需要手动调用取决你根据程序需要在数据健壮性和吞吐率之间的权衡。</p>

<p><strong>3、缓解大量小文件</strong> <br>
<strong>Hadoop Archives</strong> (HAR files)是在0.18.0版本中引入的，它的出现就是为了缓解大量小文件消耗namenode内存的问题。HAR文件是通过在HDFS上构建一个层次化的文件系统来工作。一个HAR文件是通过hadoop的archive命令来创建，而这个命令实 际上也是运行了一个MapReduce任务来将小文件打包成HAR。对于client端来说，使用HAR文件没有任何影响。所有的原始文件都 （using har://URL）。但在HDFS端它内部的文件数减少了。</p>

<p>通过HAR来读取一个文件并不会比直接从HDFS中读取文件高效，而且实际上可能还会稍微低效一点，因为对每一个HAR文件的访问都需要完成两层 index文件的读取和文件本身数据的读取。并且尽管HAR文件可以被用来作为MapReduce job的input，但是并没有特殊的方法来使maps将HAR文件中打包的文件当作一个HDFS文件处理。</p>

<p>创建文件 hadoop archive -archiveName xxx.har -p  /src  /dest <br>
查看内容 hdfs dfs -lsr har:///dest/xxx.har</p>

<p>附录： <br>
hadoop的网络拓扑：<a href="http://blog.csdn.net/team77/article/details/51546145" rel="nofollow">http://blog.csdn.net/team77/article/details/51546145</a></p>

<p>hdfs的一致性：<a href="http://blog.csdn.net/qq_26803795/article/details/64906135" rel="nofollow">http://blog.csdn.net/qq_26803795/article/details/64906135</a> <br>
hdfs的负载均衡：<a href="http://www.cnblogs.com/oraclestudy/articles/5724656.html" rel="nofollow">http://www.cnblogs.com/oraclestudy/articles/5724656.html</a></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>