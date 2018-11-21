---
layout:     post
title:      【Hadoop】--HDFS介绍
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/qq_38377523/article/details/83302654				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p id="main-toc"> </p>

<p><strong>目录</strong></p>

<p id="%E4%B8%80%E3%80%81%E4%BB%80%E4%B9%88%E6%98%AFHDFS-toc" style="margin-left:80px;"><a href="#%E4%B8%80%E3%80%81%E4%BB%80%E4%B9%88%E6%98%AFHDFS" rel="nofollow">一、什么是HDFS</a></p>

<p id="%E4%BA%8C%E3%80%81HDFS%E7%9A%84%E7%89%B9%E7%82%B9-toc" style="margin-left:80px;"><a href="#%E4%BA%8C%E3%80%81HDFS%E7%9A%84%E7%89%B9%E7%82%B9" rel="nofollow">二、HDFS的特点</a></p>

<p id="%E4%B8%89%E3%80%81HDFS%E7%9A%84%E8%AF%BB%E5%86%99%E8%BF%87%E7%A8%8B-toc" style="margin-left:80px;"><a href="#%E4%B8%89%E3%80%81HDFS%E7%9A%84%E8%AF%BB%E5%86%99%E8%BF%87%E7%A8%8B" rel="nofollow">三、HDFS的读写过程</a></p>

<p id="%E5%9B%9B%E3%80%81HDFS%E7%9A%84%E5%B8%B8%E7%94%A8%E6%8C%87%E4%BB%A4-toc" style="margin-left:80px;"><a href="#%E5%9B%9B%E3%80%81HDFS%E7%9A%84%E5%B8%B8%E7%94%A8%E6%8C%87%E4%BB%A4" rel="nofollow">四、HDFS的常用指令</a></p>

<hr id="hr-toc"><h3>一、什么是HDFS</h3>

<p>HDFS是基于Java的分布式文件系统，允许您在Hadoop集群中的多个节点上存储大量数据。它专门存储超大数据文件，为整个Hadoop生态圈提供了基础的存储服务</p>

<p>HDFS是一个主/从（Master/Slave）体系架构，由于分布式存储的性质，集群拥有两类节点NameNode和DataNode。NameNode（名字节点）：系统中通常只有一个，中心服务器的角色，管理存储和检索多个DataNode的实际数据所需的所有元数据。</p>

<p>DataNode（数据节点）：系统中通常有多个，是文件系统中真正存储数据的地方，在NameNode统一调度下进行数据块的创建、删除和复制。</p>

<p><img alt="" class="has" src="https://img-blog.csdn.net/20181023101918421?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM4Mzc3NTIz/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></p>

<p>                                                              HDFS官方框架图</p>

<h3 id="%E4%BA%8C%E3%80%81HDFS%E7%9A%84%E7%89%B9%E7%82%B9">二、HDFS的特点</h3>

<p>优点</p>

<ul><li>高容错性：数据自动保存多个副本，副本丢失后，自动恢复</li>
	<li>适合批处理：移动计算而飞数据。数据位置暴露给计算框架</li>
	<li>适合大数据处理：GB,TB,设置PB级数据。百万规模以上文件数量。10K+节点规模。</li>
	<li>流式文件访问：一次性写入，多次读取。保证数据一致性。</li>
	<li>可构建在廉价机器上：通过多副本提高可靠性。提供容错和恢复机制。</li>
</ul><p>缺点</p>

<ul><li>不适合低延迟数据访问场景：比如毫秒级，低延迟与高吞吐率</li>
	<li>不适合小文件存取场景:占用NameNode大量内存。寻道时间超过读取时间。</li>
	<li>不适合并发写入，文件随机修改场景：一个文件只能有一个写者。仅支持append</li>
</ul><h3 id="%E4%B8%89%E3%80%81HDFS%E7%9A%84%E8%AF%BB%E5%86%99%E8%BF%87%E7%A8%8B">三、HDFS的读写过程</h3>

<p>hdfs的读过程：</p>

<p><img alt="" class="has" height="376" src="https://img-blog.csdn.net/20181023103927182?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM4Mzc3NTIz/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="610"></p>

<p>1、客户端发送请求，调用DistributedFileSystem API的open方法发送请求到Namenode，获得block的位置信息，因为真正的block是存在Datanode节点上的，而namenode里存放了block位置信息的元数据。</p>

<p>2、Namenode返回所有block的位置信息，并将这些信息返回给客户端。</p>

<p>3、客户端拿到block的位置信息后调用FSDataInputStream API的read方法并行的读取block信息，图中4和5流程是并发的，block默认有3个副本，所以每一个block只需要从一个副本读取就可以。</p>

<p>4、datanode返回给客户端。</p>

<p>HDFS的写过程：</p>

<p><img alt="" class="has" height="423" src="https://img-blog.csdn.net/20181023104111551?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM4Mzc3NTIz/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="623"></p>

<p>1、客户端发送请求，调用DistributedFileSystem API的create方法去请求namenode，并告诉namenode上传文件的文件名、文件大小、文件拥有者。</p>

<p>2、namenode根据以上信息算出文件需要切成多少块block，以及block要存放在哪个datanode上，并将这些信息返回给客户端。</p>

<p>3、客户端调用FSDataInputStream API的write方法首先将其中一个block写在datanode上，每一个block默认都有3个副本，并不是由客户端分别往3个datanode上写3份，而是由</p>

<p>     已经上传了block的datanode产生新的线程，由这个namenode按照放置副本规则往其它datanode写副本，这样的优势就是快。</p>

<p>4、写完后返回给客户端一个信息，然后客户端在将信息反馈给namenode。</p>

<p>5、需要注意的是上传文件的拥有者就是客户端上传文件的用户名</p>

<h3 id="%E5%9B%9B%E3%80%81HDFS%E7%9A%84%E5%B8%B8%E7%94%A8%E6%8C%87%E4%BB%A4">四、HDFS的常用指令</h3>

<pre class="has">
<code class="language-css"> hadoop fs -mkdir /tmp/input              在HDFS上新建文件夹
 hadoop fs -put input1.txt /tmp/input  把本地文件input1.txt传到HDFS的/tmp/input目录下
 hadoop fs -get  input1.txt /tmp/input/input1.txt  把HDFS文件拉到本地
 hadoop fs -ls /tmp/output                  列出HDFS的某目录
 hadoop fs -cat /tmp/ouput/output1.txt  查看HDFS上的文件
 hadoop fs -rmr /home/less/hadoop/tmp/output  删除HDFS上的目录
 hadoop dfsadmin -report 查看HDFS状态，比如有哪些datanode，每个datanode的情况
 hadoop dfsadmin -safemode leave  离开安全模式
 hadoop dfsadmin -safemode enter  进入安全模式</code></pre>

<p> </p>            </div>
                </div>