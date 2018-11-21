---
layout:     post
title:      Hadoop之HDFS的简单介绍及常用命令
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/chenweijiSun/article/details/71106023				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>【目录】</p>

<blockquote>
  <p><a href="#1" rel="nofollow">1. HDFS介绍</a> <br>
  <a href="#2" rel="nofollow">2. HDFS优点</a> <br>
  <a href="#3" rel="nofollow">3. HDFS缺点</a> <br>
  <a href="#4" rel="nofollow">4. HDFS架构图</a> <br>
  <a href="#5" rel="nofollow">5. HDFS数据存储单元</a> <br>
  <a href="#6" rel="nofollow">6. HDFS结构</a> <br>
  <a href="#7" rel="nofollow">7. Block的副本放置策略</a> <br>
  <a href="http://blog.csdn.net/gaijianwei/article/details/45918337" rel="nofollow">8. Hadoop之HDFS文件读写过程</a> <br>
  <a href="#9" rel="nofollow">9. 安全模式</a> <br>
  <a href="#10" rel="nofollow">10. HDFS常用命令</a></p>
</blockquote>

<p>1、<span id="1"></span><strong>HDFS</strong>(HadoopDistributedFileSystem)是分布式存储系统，提供了高可靠性、高扩展性和高吞吐率的数据存储服务。</p>

<p>2、HDFS优点<span id="2"></span></p>

<pre class="prettyprint"><code class=" hljs mathematica">– 高容错性
    • 数据自动保存多个副本 
    • 副本丢失后，自动恢复
– 适合批处理
    • 移动计算而非数据
    • 数据位置暴露给计算框架
– 适合大数据处理
    • GB、TB、甚至PB级数据 
    • 百万规模以上的文件数量 
    • <span class="hljs-number">10</span><span class="hljs-keyword">K</span>+节点
– 可构建在廉价机器上
    • 通过多副本提高可靠性 
    • 提供了容错和恢复机制</code></pre>

<p>3、HDFS缺点<span id="3"></span></p>

<pre class="prettyprint"><code class=" hljs go">– 低延迟数据访问
    • 比如毫秒级
    • 低延迟与高吞吐率
– 小文件存取
    • 占用NameNode大量内存 
    • 寻道时间超过读取时间
– 并发写入、文件随机修改
    • 一个文件只能有一个写者
    • 仅支持<span class="hljs-built_in">append</span></code></pre>

<p>4、HDFS架构图<span id="4"></span></p>

<p><img src="http://images.cnitblog.com/blog/352072/201311/26104230-8109ac513de14fe1b115b775581751f2.jpg" alt="这里写图片描述" title=""></p>

<p>5、HDFS 数据存储单元<span id="5"></span></p>

<pre class="prettyprint"><code class=" hljs vhdl">– 文件被切分成固定大小的数据块
    • 默认数据块大小为<span class="hljs-number">64</span>MB，可配置
    • 若文件大小不到<span class="hljs-number">64</span>MB，则单独存成一个<span class="hljs-keyword">block</span>
– 一个文件存储方式
    • 按大小被切分成若干个<span class="hljs-keyword">block</span>，存储到不同节点上 
    • 默认情况下每个<span class="hljs-keyword">block</span>都有三个副本
– <span class="hljs-keyword">Block</span>大小和副本数通过Client端上传文件时设置，文件上传成功后副本数可以变更，<span class="hljs-keyword">Block</span> Size不可变更。</code></pre>

<p><img src="https://img-blog.csdn.net/20170503001845022?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2hlbndlaWppU3Vu/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p>6、HDFS的结构<span id="6"></span></p>

<p>HDFS也是按照Master和Slave的结构。分NameNode、SecondaryNameNode、DataNode这几个角色：</p>

<p>NameNode(NN)</p>



<pre class="prettyprint"><code class=" hljs mathematica">– NameNode主要功能:接受客户端的读写服务
– NameNode保存metadate信息包括 
    • 文件owership和permissions
    • 文件包含哪些块
    • <span class="hljs-keyword">Block</span>保存在哪个DataNode(由DataNode启动时上报)
– NameNode的metadate信息在启动后会加载到内存 
    • metadata存储到磁盘文件名为”fsimage”
    • <span class="hljs-keyword">Block</span>的位置信息不会保存到fsimage 
    • edits记录对metadata的操作日志</code></pre>

<p>SecondaryNameNode(SNN)</p>



<pre class="prettyprint"><code class=" hljs avrasm">– 它不是NN的备份(但可以做备份)，它的主要工作是帮助NN合并edits
log，减少NN启动时间。 
– SNN执行合并时机
    • 根据配置文件设置的时间间隔fs<span class="hljs-preprocessor">.checkpoint</span><span class="hljs-preprocessor">.period</span> 默认<span class="hljs-number">3600</span>秒
    • 根据配置文件设置editslog大小fs<span class="hljs-preprocessor">.checkpoint</span><span class="hljs-preprocessor">.size</span>规定edits文件的最大值默认是<span class="hljs-number">64</span>MB</code></pre>

<p>DataNode(DN)</p>



<pre class="prettyprint"><code class=" hljs oxygene">– 存储数据(<span class="hljs-keyword">Block</span>)
– 启动DN线程的时候会向NN汇报<span class="hljs-keyword">block</span>信息
– 通过向NN发送心跳保持与其联系(<span class="hljs-number">3</span>秒一次)，如果NN10分钟没有收到DN的心跳，则认为其已经lost，并<span class="hljs-keyword">copy</span>其上的<span class="hljs-keyword">block</span>到其它DN</code></pre>

<p>7、Block的副本放置策略<span id="7"></span></p>



<pre class="prettyprint"><code class=" hljs scss">– 第一个副本<span class="hljs-value">:放置在上传文件的DN;</span> 如果是集群外提交，则随机挑选一台磁盘不太满，CPU不太忙的节点。
– 第二个副本<span class="hljs-value">:放置在于第一个副本不同的机架的节点上。
– 第三个副本:与第二个副本相同机架的节点。
– 更多副本:随机节点</span></code></pre>

<p>8、 Hadoop之HDFS文件读写过程  <br>
参考：<a href="http://blog.csdn.net/gaijianwei/article/details/45918337" rel="nofollow">http://blog.csdn.net/gaijianwei/article/details/45918337</a></p>

<p>9、安全模式<span id="9"></span></p>



<pre class="prettyprint"><code class=" hljs ">– namenode启动的时候，首先将映像文件(fsimage)载入内存，并执行编辑日志(edits)中的各项操作。
– 一旦在内存中成功建立文件系统元数据的映射，则创建一个新的fsimage文件(这个操作不需要SecondaryNameNode)和一个空的编辑日志。
– 此刻namenode运行在安全模式。即namenode的文件系统对于客服端来说是只读的。(显示目录，显示文件内容等。写、删除、重命名都会失败)。
– 在此阶段Namenode收集各个datanode的报告，当数据块达到最小副本数以上时，会被认为是“安全”的，在一定比例(可设置)的数据块被确定为“安全”后，再过若干时间，安 全模式结束
– 当检测到副本数不足的数据块时，该块会被复制直到达到最小副本数，系统中数据块的位 置并不是由namenode维护的，而是以块列表形式存储在datanode中</code></pre>

<p>10、HDFS常用命令<span id="10"></span></p>

<p>1）列出HDFS下的文件</p>

<p>hadoop fs -ls</p>

<p>2）上传本地文件到HDFS</p>

<p>hadoop fs -put  dir1  dir2</p>

<p>说明：dir1，本地文件系统文件目录；dir2，HDFS文件系统文件目录</p>

<p>3）下载HDFS文件到本地</p>

<p>hadoop fs -get dir1  dir2</p>

<p>说明：dir1，本地文件系统文件目录；dir2，HDFS文件系统文件目录</p>

<p>4）创建HDFS文件</p>

<p>hadoop fs -mkdir  dir</p>

<p>5）删除HDFS下的文档</p>

<p>hadoop fs -rmr dir</p>

<p>6）查看HDFS下某个文件的内容</p>

<p>hadoop fs -cat   文件路径</p>

<p>7）查阅帮助</p>

<p>获取所用Hadoop版本关于HDFS完整的命令列表，可执行</p>

<p>hadoop fs</p>

<p>或者</p>

<p>使用help来显示某个命令的用法与简短描述。</p>

<p>例如 要了解ls，可执行</p>

<p>hadoop fs -help ls</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>