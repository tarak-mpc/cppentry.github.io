---
layout:     post
title:      hdfs:Hadoop分布式文件系统
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/lin434406218/article/details/52324850				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p><strong>1、hdfs:Hadoop分布式文件系统</strong> <br>
HDFS是一个高度容错性的系统，适合部署在廉价的机器上。 <br>
HDFS能提供高吞吐量的数据访问，非常适合大规模数据集上的应用。 <br>
HDFS放宽了一部分POSIX约束，来实现流式读取文件系统数据的目的。 <br>
HDFS在最开始是作为Apache Nutch搜索引擎项目的基础架构而开发的。 <br>
HDFS是Apache Hadoop Core项目的一部分。</p>

<p><strong>2、hdfs架构</strong> <br>
<em>namenode</em>：管理文件系统的元数据 <br>
<em>datanode</em>：存储实际的数据。读写都是调用<em>datanode</em> 的方法</p>

<p><strong>3、读操作</strong> <br>
文件内容读取的代码可以分为三个大步骤 。 <br>
1、获取文件系统 <br>
2、通过文件系统打开文件 <br>
3、将文件内容输出</p>



<pre class="prettyprint"><code class=" hljs cs"><span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">read</span>(Path path) throws IOException{
    FileSystem hdfs = HdfsUtils.getFilesystem();  <span class="hljs-comment">//步骤 1</span>
    FSDataInputStream fsDataInputStream =  hdfs.open(path); <span class="hljs-comment">//步骤 2</span>
    IOUtils.copyBytes(fsDataInputStream, System.<span class="hljs-keyword">out</span>, <span class="hljs-number">4096</span>,<span class="hljs-keyword">false</span>);  <span class="hljs-comment">//步骤 3</span>
}</code></pre>

<p><strong>4、写操作</strong> <br>
第一台备份：写在离客户端最近的一台IP机器上 <br>
第二台备份：第一台IP机子的同一个机架上 <br>
第三台备份：必须写在与第一台IP机子不同的机架上</p>

<p><strong>5、hdfs的文件</strong> <br>
fsimage–目录结构 <br>
edits–日志文件（log） <br>
SecondNamenode对fsimage和edits的文件操作。</p>

<p><strong>6、hdfs不擅长的领域：</strong> <br>
低时间延迟的数据访问：不能处理实时性的数据  OLTP(事务)，OLAP（分析） <br>
存储大量的小文件：处理的是大数据，数据越大，效率越高 <br>
多用户写入，任意修改文件：处理的是历史数据</p>

<p><strong>7、hdfs -IO 基于硬盘</strong> <br>
    spark 基于内存 <br>
    基于内存的永远是不可靠的</p>

<p><strong>8、hdfs中文件块默认是64M</strong></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>