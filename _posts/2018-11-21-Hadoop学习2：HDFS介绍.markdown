---
layout:     post
title:      Hadoop学习2：HDFS介绍
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，欢迎转载，转载请注明出处！					https://blog.csdn.net/qq_36743482/article/details/78220982				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p><strong><em>HDFS：Hadoop Distributed File System:Hadoop分布式文件系统</em></strong></p>



<h3 id="简介">简介</h3>

<p>HDFS为了做到可靠性创建了多份数据块的复制，并将它们放置在服务器群的计算节点中，MapReduce就可以在它们所在的节点上处理这些数据。 <br>
<img src="https://img-blog.csdn.net/20171012231517453?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMzY3NDM0ODI=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>



<h3 id="hdfs节点">HDFS节点</h3>

<ul>
<li>NameNode：（一般一个） <br>
存储元数据（除文件内容之外的数据，如文件名，大小，权限等）因而，<strong>文件越多，NameNode数据越大</strong>。 <br>
保存在磁盘中，启动时加载到内存中（因此工作过程都是在内存中读数据）； <br>
保存文件，block和DataNode之间的映射关系。</li>
<li>DataNode：（一般多个） <br>
存储文件内容； <br>
保存在磁盘中； <br>
维护了block id到DataNode本地文件的映射关系。</li>
<li>Secondary NameNode <br>
<img src="https://img-blog.csdn.net/20171012232629879?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMzY3NDM0ODI=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></li>
</ul>



<h3 id="hdfs运行机制">HDFS运行机制</h3>

<ul>
<li>一般一个NameNode和多个DataNode</li>
<li>数据复制（冗余机制） <br>
存放的位置：机架感知策略</li>
<li>故障检测： <br>
数据节点：心跳包(是否宕机)、块报告(安全模式下检测)、数据完整性检测(校验和比较) <br>
名字节点：日志文件，镜像文件</li>
<li>空间回收机制</li>
</ul>



<h3 id="hdfs架构">HDFS架构</h3>



<h3 id="hdfs存储单元">HDFS存储单元</h3>

<ul>
<li>HDFS数据存储单元(block)：是逻辑结构，不是磁盘空间</li>
<li>文件被切分成固定大小的数据块 <br>
默认数据块大小为128(老版本64)M，可配置 <br>
若文件大小不到128M，单独存一个block；超过，则被分割在几个block中。 <br>
一个block只能存储一个文件的数据。</li>
<li>一个文件存储方式 <br>
按大小被切分成若干个block，存储在不同的节点上 <br>
默认情况下每个block都有三个副本。</li>
<li>Block大小和副本数通过Client端上传文件时设置。文件上传成功后副本数可以更改。Block size不接更改。 <br>
有一个Block的数据损坏，某个块的副本数小于指定数目，系统会自动备份，使数量和设置值一致。</li>
</ul>

<h3 id="hdfs优点">HDFS优点</h3>

<ul>
<li>高容错性：数据自动保存多个副本；副本丢失后，自动恢复</li>
<li>适合批处理：移动计算而非数据，数据位置暴露给计算框架</li>
<li>适合大数据处理：10K+节点</li>
<li>可构建在廉价机器上：通过多副本提高可靠性，提供容错和恢复机制</li>
</ul>



<h3 id="hdfs缺点不适用于">HDFS缺点(不适用于)</h3>

<ul>
<li>低延迟数据访问(如订单)：毫秒级、低延迟与高吞吐率；</li>
<li>小文件存取(可将小文件压缩到一个进行解决，但是压缩后不可更改)：占用NameNode大量内存，寻道时间超过读取时间；</li>
<li>并发写入、文件随机修改：一个文件只能有一个写者，仅支持append。</li>
</ul>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>