---
layout:     post
title:      HDFS--hadoop分布式文件系统模型
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/u014726937/article/details/51010977				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="hdfs的shell命令练习">HDFS的Shell命令练习</h1>

<p><font size="4"><strong>查看</strong></font>HDFS根目录</p>

<blockquote>
  <p><font size="4"> hadoop　fs　-ls　/</font></p>
</blockquote>

<p>在根目录<font size="4"><strong>创建</strong></font>一个目录test</p>

<blockquote>
  <p><font size="4">hadoop　fs　-mkdir　/test </font></p>
</blockquote>

<p><font size="4"><strong>上传</strong></font>文件</p>

<blockquote>
  <p><font size="4">hadoop　fs　-put　./test.txt　/test　 <br>
  或 hadoop　fs　-copyFromLocal　./test.txt　/test</font></p>
</blockquote>

<p><font size="4"><strong>下载</strong></font>文件</p>

<blockquote>
  <p><font size="4">hadoop　fs　-get　/test/test.txt　. <br>
  或 hadoop　fs　-getToLocal　/test/test.txt .</font></p>
</blockquote>

<p><font size="4"><strong>拷贝</strong></font>文件</p>

<blockquote>
  <p><font size="4">hadoop　fs　-cp　/test/test.txt　/test1  </font></p>
</blockquote>

<p><font size="4"><strong>删除</strong></font>文件</p>

<blockquote>
  <p><font size="4">hadoop　fs　-rm　/test1/test.txt </font></p>
</blockquote>

<p><font size="4"><strong>移动</strong></font>文件</p>

<blockquote>
  <p><font size="4">hadoop　fs　-mv　/test/test.txt　/test1  </font></p>
</blockquote>

<p><font size="4"><strong>递归删除</strong></font>文件目录</p>

<blockquote>
  <p><font size="4">hadoop　fs　–rm　-r　/test1　   </font></p>
</blockquote>



<h1 id="hdfs架构">HDFS架构</h1>

<blockquote>
  <p>-NameNode <br>
       -DataNode <br>
       -Secondary NameNode <br>
   <img src="https://img-blog.csdn.net/20160330090459841" alt="这里写图片描述" title=""></p>
</blockquote>



<h3 id="metadata元数据">MetaData（元数据）</h3>

<p>——元数据：好比仓库管理员记账的账本（100斤白菜，存放在哪，几号存放的； <br>
图书管理系统，存放一本书，需要记录书名，存放位置，存放时间，有几个副本… <br>
<img src="https://img-blog.csdn.net/20160330090750830" alt="元数据存储细节" title=""></p>



<h2 id="namenode">NameNode</h2>

<ul>
<li>是整个文件系统的管理节点。它维护着整个文件系统的文件目录树，文件/目录的元信息和每个文件对应的数据块列表。接收用户的操作请求。</li>
<li>文件包括： <br>
①   fsimage:元数据镜像文件。存储某一时段NameNode内存元数据信息（hadoop1.0和hadoop2.0的伪分布式并不是和内存中的内存元数据实时同步的）。 <br>
②   edits:操作日志文件。 <br>
③   fstime:保存最近一次checkpoint的时间（比如windows系统的还原点） <br>
   以上这些文件是保存在linux的文件系统中。 <br>
   Namenode始终在内存中保存metedata，用于处理“读请求” <br>
   到有“写请求”到来时，namenode会首先写editlog到磁盘，即向edits文件中写日志，成功返回后，才会修改内存，并且向客户端返回 <br>
   Hadoop会维护一个fsimage文件，也就是namenode中metedata的镜像，但是fsimage不会随时与namenode内存中的metedata保持一致（1.0集群和2.0的伪分布式），而是每隔一段时间通过合并edits文件来更新内容。Secondary namenode就是用来合并fsimage和edits文件来更新NameNode的metedata的。</li>
</ul>



<h2 id="secondarynamenode">SecondaryNameNode</h2>

<ul>
<li>HA的一个解决方案。但不支持热备。配置即可。</li>
<li>执行过程：从NameNode上下载元数据信息（fsimage,edits），然后把二者合并，生成新的fsimage，在本地保存，并将其推送到NameNode，替换旧的fsimage.</li>
<li>默认在安装在NameNode节点上（1.0），但这样…不安全！</li>
</ul>

<p><strong>secondary namenode的工作流程：</strong> <br>
<img src="https://img-blog.csdn.net/20160330090903552" alt="这里写图片描述" title=""> <br>
- secondary通知namenode切换edits文件 <br>
 - secondary从namenode获得fsimage和edits(通过http) <br>
 - secondary将fsimage载入内存，然后开始合并edits <br>
 - secondary将新的fsimage发回给namenode <br>
 - namenode用新的fsimage替换旧的fsimage</p>



<h2 id="什么时候开始checkpiont同步">什么时候开始checkpiont（同步）？</h2>

<p><img src="https://img-blog.csdn.net/20160330091134491" alt="这里写图片描述" title=""></p>

<ul>
<li><p>fs.checkpoint.period <strong>指定两次checkpoint的最大时间间隔</strong>，默认<strong>3600秒</strong>。</p></li>
<li><p>fs.checkpoint.size <br>
<strong>规定edits文件的最大值</strong>，一旦超过这个值则强制checkpoint，不管是否到达最大时间间隔。<strong>默认大小是64M</strong>。</p></li>
</ul>

<p>满足以上任何两个条件，就开始合并</p>

<h2 id="datanode"><strong>Datanode</strong></h2>

<ul>
<li>提供真实文件数据的存储服务。</li>
<li>文件块（block）：最基本的存储单位。对于文件内容而言，一个文件的长度大小是size，那么从文件的０偏移开始，按照固定的大小，顺序对文件进行划分并编号，划分好的每一个块称一个Block。HDFS默认Block大小是128MB，以一个256MB文件，共有256/128=2个Block.</li>
<li>不同于普通文件系统的是，HDFS中，如果一个文件小于一个数据块的大小，并不占用整个数据块存储空间</li>
<li>Replication。多复本。默认是三个。（hdfs-site.xml的dfs.replication属性） <br>
（复本所保存的那个机器down掉了，namenode会主动发送复制命令再复制一个复本到其他datanode，这其中包含了datanode的心跳机制，datanode会定期发送心跳给namenode，如果datanode过了这个周期没有发送心跳，namenode会认为这个datanode down掉了。）</li>
</ul>



<h1 id="hdfs是怎么分配块大小的shell命令练习验证块大小">hdfs是怎么分配块大小的？（Shell命令练习：验证块大小）</h1>

<ul>
<li>方法：上传大于128MB的文件，观察块大小</li>
<li>验证：使用 <a href="http://hadoop0:50070" rel="nofollow">http://hadoop0:50070</a> 观察 <br>
清除# hadoop fs -ls / 下所有文件：hadoop fs - rm –r 文件夹名..  （递归删除） <br>
文件大小：154773078 字节（147M）——-jdk-7u79-linux-i586.gz  上传到HDFS的  /jdk1.7 目录下 <br>
hadoop fs -put /root/jdk-7u79-linux-i586.gz /jdk1.7</li>
</ul>

<p>在/itcast/hadoop-2.4.1/tmp/dfs/data/current/BP-1152942234-192.168.1.10-1458206501707/current/finalized目录下找到这4条记录：</p>



<pre class="prettyprint"><code class=" hljs brainfuck"><span class="hljs-literal">-</span><span class="hljs-comment">rw</span><span class="hljs-literal">-</span><span class="hljs-comment">r</span><span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">r</span><span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-string">.</span> <span class="hljs-comment">1</span> <span class="hljs-comment">root</span> <span class="hljs-comment">root</span> <span class="hljs-comment">134217728</span> <span class="hljs-comment">Mar</span> <span class="hljs-comment">20</span> <span class="hljs-comment">01:52</span> <span class="hljs-comment">blk_1073741839</span>
<span class="hljs-literal">-</span><span class="hljs-comment">rw</span><span class="hljs-literal">-</span><span class="hljs-comment">r</span><span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">r</span><span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-string">.</span> <span class="hljs-comment">1</span> <span class="hljs-comment">root</span> <span class="hljs-comment">root</span>   <span class="hljs-comment">1048583</span> <span class="hljs-comment">Mar</span> <span class="hljs-comment">20</span> <span class="hljs-comment">01:52</span> <span class="hljs-comment">blk_1073741839_1015</span><span class="hljs-string">.</span><span class="hljs-comment">meta</span>
<span class="hljs-literal">-</span><span class="hljs-comment">rw</span><span class="hljs-literal">-</span><span class="hljs-comment">r</span><span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">r</span><span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-string">.</span> <span class="hljs-comment">1</span> <span class="hljs-comment">root</span> <span class="hljs-comment">root</span>  <span class="hljs-comment">20555350</span> <span class="hljs-comment">Mar</span> <span class="hljs-comment">20</span> <span class="hljs-comment">01:52</span> <span class="hljs-comment">blk_1073741840</span>
<span class="hljs-literal">-</span><span class="hljs-comment">rw</span><span class="hljs-literal">-</span><span class="hljs-comment">r</span><span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">r</span><span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-string">.</span> <span class="hljs-comment">1</span> <span class="hljs-comment">root</span> <span class="hljs-comment">root</span>    <span class="hljs-comment">160599</span> <span class="hljs-comment">Mar</span> <span class="hljs-comment">20</span> <span class="hljs-comment">01:52</span> <span class="hljs-comment">blk_1073741840_1016</span><span class="hljs-string">.</span><span class="hljs-comment">meta</span></code></pre>

<p>其中不带<strong>.meta</strong>后缀的文件是block文件，可以看到其中一个block大小是<strong>128M</strong>（134217728字节），另一个则占剩余的大小。</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>