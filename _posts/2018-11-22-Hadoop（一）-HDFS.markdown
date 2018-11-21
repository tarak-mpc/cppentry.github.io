---
layout:     post
title:      Hadoop（一）-HDFS
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/u013963380/article/details/61616046				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>HDFS（Hadoop Distirbuted File System）hadoop分布式文件系统。 <br>
<strong>1.HDFS的设计理念</strong></p>

<ul>
<li>存储大文件：这里的大文件是指GB甚至TB级别的文件</li>
<li>高效的访问模式：一次写入，多次读取（流式数据访问）</li>
<li>运行在普通廉价的服务器上：能运行在普通的硬件上，即使硬件出现故障，也可以通过容错策略来保证数据的完整性 <br>
<em>注：由于HDFS是为高数据吞吐量应用而设计的，必然以高延迟为代价所以不会将hdfs用于对数据访问要求低延迟的场景；hdfs的元数据存储在namenode的内存中，而namenode是单节点，小文件的数量大到一定的程度，namenode的内存就吃不消，所以也不可用来存储大量的小文件。</em></li>
</ul>

<p><strong>2.HDFS的基本概念</strong></p>

<ul>
<li>Block：在HDFS中，大文件是被分割成多个block进行存储的，一般block的大小默认是64MB（2.0版本后是128MB也可以自己设置）。而每个block会在多个datanode上存储多份副本，默认是3份（一般和datanode的个数有关）。</li>
<li>Namenode：存储元数据信息，管理数据块的映射；处理客户端的读写请求；配置副本策略；管理hdfs的名称空间等。</li>
<li>SeconaryNamenode：Namenode的备份；当namenode节点死亡，迅速启动接替namenode的工作；合并fsimage和edits log与namenode保持一致。</li>
<li>Datanode：负责存储client发来的数据块block；执行数据块的读写操作。</li>
<li>fsimage：元数据的镜像文件。</li>
<li>edits：元数据的操作日志</li>
</ul>

<p><strong>3.HDFS的实现思想</strong></p>

<ul>
<li>hdfs通过分布式集群来存储文件</li>
<li>文件存储到hdfs集群中去的时候是被分切成若干的block</li>
<li>文件的block存放在若干台的datanode节点上</li>
<li>hdfs文件系统的文件与真实的文件block之间有映射关系，由namode管理</li>
<li>每个block在集群中都会存储多个副本</li>
</ul>

<p><strong>4.HDFS的基本架构</strong> <br>
<img src="https://img-blog.csdn.net/20170312210639901?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMzk2MzM4MA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p>这里的rack是指机柜（包含若干台服务器）。上面的图其实并不能完全说明HDFS的工作机制，下面以往hdfs上面存放大文件为例，说明写操作的机制。</p>

<p><img src="https://img-blog.csdn.net/20170313191314316?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMzk2MzM4MA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p>在这里，客户端将本地文件test.txt写到hdfs上（路径：hdfs://nn:9000/xudong, hadoop fs -put test.txt /xudong）,首先大文件test.txt被切分成3个block（block1，block2,block3），然后分别存储到三台datanode节点上，每个block有几个备份放在其他的datanode节点上，上面只画了block1的2个备份。Namenode负责管理这些节点上的block。</p>

<p><strong>5.NameNode元数据管理机制</strong> <br>
namenode管理元数据是有自己的机制的，为了配套上面的客户端写文件到hdfs，下面举例说明NN管理上传文件的过程：</p>

<ul>
<li>1客户端上传文件时，NN首先往edits log文件中记录元数据的操作日志</li>
<li>2客户端上传开始上传文件，完成后并返回成功信息给NN，NN就在内存中写入这次上传操作新产生的元数据。</li>
<li>3每当edits log写满时，需要将这段时间的元数据刷到fsimage中去。</li>
</ul>

<p><img src="https://img-blog.csdn.net/20170313194218372?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMzk2MzM4MA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p>在NomeNode中元数据存储的细节： <br>
NameNode（FileName，replicas，block_ids，idhost……..） <br>
/xudong/test.txt,3,{blk_1,blk_2,blk_3},blk_1:[h0,h1,h3]},{blk_2:[h1,h2,h3],{blk_3:h0,h2,h3}}</p>

<p>到这里，HDFS的基本知识就结束了，以后遇到更全的知识会陆续补充。下一篇将会介绍hadoop的第二个核心mapreduce。</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>