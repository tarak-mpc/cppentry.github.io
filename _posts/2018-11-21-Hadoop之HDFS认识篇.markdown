---
layout:     post
title:      Hadoop之HDFS认识篇
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：技术共享，如需转载，注明地址即可。					https://blog.csdn.net/Ink4T/article/details/77449473				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h3 id="前言">前言：</h3>

<blockquote>
  <p>Hadoop分布式文件系统(HDFS)被设计成适合运行在通用硬件(commodityhardware)上的<strong>分布式文件系统</strong>。它和现有的分布式文件系统有很多共同点。但同时，它和其他的分布式文件系统的区别也是很明显的。HDFS是一个<strong>高度容错性</strong>的系统，适合部署在<strong>廉价的机器</strong>上。HDFS能提供<strong>高吞吐量</strong>的数据访问，非常适合<strong>大规模数据集</strong>上的应用。</p>
</blockquote>



<h3 id="hdfs介绍">HDFS介绍</h3>

<blockquote>
  <p>HDFS为了做到可靠性创建了多份数据块的复制，并将它们放置在服务器群的计算机节点中，MapReduce就可以在它们所在的节点上处理这些数据了。 <br>
  <strong>通俗说</strong>就是，HDFS给一份数据备份了多份放在多个磁盘中，如果其中一个磁盘的数据丢失，那么也不会影响数据，详细见下图：</p>
</blockquote>

<p><img src="https://img-blog.csdn.net/20170821135545228?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvSW5rNFQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>



<h3 id="hdfs结构">HDFS结构</h3>

<blockquote>
  <p>主要由三大部分组成：NameNode（NN）、SecondaryNameNode（SNN）和DataNodes（DN）。</p>
  
  <ol>
  <li><strong>NameNode</strong>： <br>
  ① 存储<strong>元数据</strong>； <br>
  ② 元数据保存在<strong>内存</strong>中； <br>
  ③ 保存文件，block，datanode之间的映射关系。</li>
  <li><strong>DataNodes</strong>： <br>
  ① 存储文件内容； <br>
  ② 文件内容保存在磁盘； <br>
  ③ 维护了block id到datanode本地文件的映射关系。</li>
  </ol>
</blockquote>



<h3 id="hdfs架构">HDFS架构</h3>

<blockquote>
  <p>架构图如下所示：</p>
</blockquote>

<p><img src="https://img-blog.csdn.net/20170821140443398?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvSW5rNFQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>



<h3 id="主要功能介绍">主要功能介绍：</h3>



<h4 id="1-namenodenn"><strong>1、 NameNode（NN）</strong></h4>

<ul>
<li><p>NameNode主要功能：接受客户端的读写服务；</p></li>
<li><p>NameNode保存metadate信息包括：</p>

<ol><li>文件owership和permissons；</li>
<li>文件包含哪些块；</li>
<li>Block保存在哪个DataNode（又DataNode启动时上报）；</li>
<li>metadata存储到磁盘文件名为“fsimage”；</li>
<li>Block的位置信息不会保存到fsimage；</li>
<li>edits记录对metadata的操作日志</li></ol></li>
</ul>

<h4 id="2secondarynamenodesnn"><strong>2、SecondaryNameNode（SNN）</strong></h4>

<ul>
<li>它不是NN的备份（但可以做备份），它的主要工作是帮组NN合并edits log，减少NN启动时间；</li>
<li>SNN执行合并时机： <br>
<ol><li>根据配置文件设置的时间间隔fs.checkpoint.priod 默认3600秒；</li>
<li>根据配置文件设置edits log大小fs,checkpoint.size规定edits文件的最大值默认是64MB。</li></ol></li>
</ul>

<h4 id="3-datanodedn"><strong>3、 DataNode（DN）</strong></h4>

<ul>
<li>存储数据（Block）；</li>
<li>启动DN线程的时候会向NN汇报block信息；</li>
<li>通过想NN发送心跳保持与其联系（3秒一次），如果NN 10分钟没有收到DN的心跳，则认为其已经lost，并copy骑上的block到其他DN上去。</li>
</ul>

<h4 id="4block的副本放置策略"><strong>4、Block的副本放置策略</strong></h4>

<ul>
<li>第一个副本：放置在上传文件的DN；如果是集群外提交，则随机挑选一台磁盘不太满，CPU不太忙的节点。</li>
<li>第二个副本：放置在于第一个副本不同的机架节点上。</li>
<li>第三个副本：与第二副本相同机架的节点。</li>
<li>更多副本：随机节点。</li>
</ul>

<h3 id="snn合并流程">SNN合并流程</h3>

<blockquote>
  <p>建议：把SNN和NN放在不同的服务器上。 <br>
  <img src="https://img-blog.csdn.net/20170821142520326?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvSW5rNFQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>
</blockquote>



<h3 id="hdfs读流程">HDFS读流程</h3>

<blockquote>
  <p>具体操作如下图所示：</p>
</blockquote>

<p><img src="https://img-blog.csdn.net/20170821142724572?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvSW5rNFQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>



<h3 id="hdfs写流程">HDFS写流程</h3>

<blockquote>
  <p>具体操作如下图所示：</p>
</blockquote>

<p><img src="https://img-blog.csdn.net/20170821142807963?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvSW5rNFQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<blockquote>
  <p>ack：确认字符，表示发来的数据已确认接收无误。 <br>
  详细见百度百科：<a href="https://baike.baidu.com/item/ACK/3692629?fr=aladdin" rel="nofollow" target="_blank">https://baike.baidu.com/item/ACK/3692629?fr=aladdin</a></p>
</blockquote>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>