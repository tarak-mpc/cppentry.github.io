---
layout:     post
title:      Hadoop之--HDFS原理
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/PoorGuy_tn/article/details/82054878				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2 id="hdfs介绍">HDFS介绍:</h2>

<ul>
<li>HDFS是Hadoop的存储结构,HDFS为了做到可靠性(reliability) 创建了多份数据块(data block)的复制,并将他们放置在服务器群的计算节点中(compute notes),MapReduce就可以在他们所在的这些节点上处理这些数据了.</li>
<li><img src="https://img-blog.csdn.net/20180825185500572?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1Bvb3JHdXlfdG4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></li>
</ul>



<h2 id="hdfs结构">HDFS结构:</h2>

<ul>
<li>下面是HDFS的结构图</li>
</ul>

<p><img src="https://img-blog.csdn.net/20180825185938930?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1Bvb3JHdXlfdG4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<p>注释: <br>
元数据:元数据是指除文件内容之外的其他数据.(Eg:文件名,文件大小,文件存储位置等) <br>
block:block是指将一个文件切成许多个block,然后将这些block分别存在不同的节点上,任何一个block至少都会有3个以上的存储.</p>



<h2 id="hdfs运行机制">HDFS运行机制:</h2>

<ul>
<li>一个名字节点和多个数据节点</li>
<li>数据复制(冗余机制) <br>
 存放位置:(机架感知策略)</li>
<li><p>故障检测 <br>
 –数据节点 <br>
 心跳包(检测是否坏掉) <br>
 块检测(安全模式下的检测) <br>
 数据完整性检测(校验和比较) <br>
 –名字节点(日志文件,镜像文件)</p></li>
<li><p>空间回收机制</p></li>
</ul>

<p>注意:</p>

<ul>
<li>心跳包:各个节点每过一段时间就要发送个NameNode一次请求,在10分钟内没有发送的认为是坏的机器.</li>
<li>快检测:主要对块的数量进行检测,由于有坏的机器的出现,使得有些block的数量达不到标准,NameNode就会将这些不够备份数量的block放在空余的节点上.</li>
<li>每一次新开机启动,都会进行这些检测,称之为安全模式,请不要轻易选择强制停止安全模式,这样有可能造成数据的丢失.</li>
</ul>



<h2 id="hdfs有点">HDFS有点:</h2>

<p>–高容错性</p>

<ul>
<li>数据自动保存多个副本</li>
<li>副本丢失后,自动回复</li>
</ul>

<p>–适合批处理</p>

<ul>
<li>移动计算而非数据</li>
<li>数据位置暴露给计算框架</li>
</ul>

<p>–适合大数据处理</p>

<ul>
<li>GB,TB,甚至PB级数据</li>
<li>百万规模以上的文件数量</li>
<li>10K+节点</li>
</ul>

<p>–可构建在廉价的机器上:</p>

<ul>
<li>通过多副本提高稳定性</li>
<li>提供了容错和恢复机制</li>
</ul>

<p>注意:因为HDFS容错性做的好,所以可以在廉价的机器上,不怕机器故障丢失数据.</p>



<h2 id="hdfs缺点">HDFS缺点</h2>

<p>–低延迟数据访问</p>

<ul>
<li>比如毫秒级</li>
<li>低延迟与高吞吐率 <br>
Eg:订单,由于我们输入单号就要得到商品,不适合用,更适合使用关系型数据库. <br>
–小文件存储:</li>
<li>占用NameNode大量内存</li>
<li><p>寻道时间超过读取时间 <br>
因为我们知道元数据是存储在NameNode里的,每一次开机都会将元数据加载到内存,数据量大了元数据会变多. <br>
–并发写入,文件随机修改:</p></li>
<li><p>一个文件只能有一个写者</p></li>
<li>仅支持append <br>
Eg:现在我们所听说的各大网盘基本上都是使用Hadoop的HDFS存储的,所以没有修改文件内容的功能(修改文件名不属于修改文件内数据)</li>
</ul>



<h2 id="hdfs架构图">HDFS架构图:</h2>

<p><img src="https://img-blog.csdn.net/20180825193304761?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1Bvb3JHdXlfdG4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""> <br>
注意:在网上看到这张图片,但是这张图片有些错误,五条虚线应该上面和HDFS Client相连而不是与NameNode相连.</p>

<ol>
<li>首先我们可以看上面的图片,当客户端发出写请求,客户端会首先找到NameNode(因为NameNode中存储了元数据,并且存放了block块所在的位置),如果NameNode文件丢失,所有block将会变成垃圾文件,没有办法重新组合和被找到.</li>
<li>NameNode会将文件的block信息储存起来,并且将各个block存到不同的节点中,然后节点进行复制,以达到要求的备份数量.</li>
<li>我们在右面可以看到一个名字为Secondary NameNode的东西,一会会细致的说一下,现在可以说他是作为热备份用的.</li>
</ol>

<h2 id="hdfs数据存储单元block">HDFS数据存储单元(block):</h2>

<p>–文件被切分成固定大小的数据块</p>

<ul>
<li>默认数据块大小为64MB,可自行配置大小</li>
<li><p>若文件大小不够64MB,则单独存在一个block中</p>

<p>–一个文件的存储方式</p></li>
<li><p>按大小被切分成若干block,存储在不同的节点上</p></li>
<li>默认情况下每个block都有三个副本</li>
</ul>

<p>–Block大小和副本数量通过Client短上传文件时设置,文件上传成功后副本数可以变更,Block Size不可变更.</p>

<p>注意:Block占有的是逻辑结构,磁盘空间是多少就是多少</p>

<h2 id="hdfs设计思想">HDFS设计思想:</h2>

<p><img src="https://img-blog.csdn.net/20180825194928627?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1Bvb3JHdXlfdG4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""> <br>
注意: <br>
每一个block都要存在三个不同的节点上.并且第一个副本要和源文件在不同的机架上,第二个副本和第一个副本在同一机架上不同的节点上(目的:为了传输速度快)</p>



<h2 id="各节点的功能">各节点的功能:</h2>

<ul>
<li>NameNode的功能: <br>
<img src="https://img-blog.csdn.net/20180825195203753?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1Bvb3JHdXlfdG4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></li>
<li>SecondaryNameNode的功能: <br>
<img src="https://img-blog.csdn.net/20180825195347319?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1Bvb3JHdXlfdG4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""> <br>
SNN合并图解: <br>
<img src="https://img-blog.csdn.net/20180825195505873?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1Bvb3JHdXlfdG4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></li>
<li><p>DataNode的功能: <br>
<img src="https://img-blog.csdn.net/20180825195628778?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1Bvb3JHdXlfdG4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p></li>
<li><p>Block的副本放置策略:</p></li>
</ul>

<p>–第一个副本: <br>
    放置在上传文件的DN;如果是集群外提交,则随机挑选一台磁盘不太满,CPU不太忙的节点.</p>

<p>–第二个副本: <br>
放置在与第一个副本不同的机架节点上.</p>

<p>–第三个副本: <br>
与第二个副本相同的机架上的节点</p>

<p>–更多副本: <br>
随机放置</p>



<h2 id="hdfs读写流程图">HDFS读写流程图:</h2>

<p><img src="https://img-blog.csdn.net/20180825200104356?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1Bvb3JHdXlfdG4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<p>注释: <br>
上图中open是一个函数 <br>
<img src="https://img-blog.csdn.net/201808252002325?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1Bvb3JHdXlfdG4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>



<h2 id="hdfs文件权限">HDFS文件权限:</h2>

<p>–与Linux文件权限类似:</p>

<ul>
<li>r:read;w:write;x:execute,权限x对于文件忽略,对于文件夹表示是否允许访问其内容.</li>
</ul>

<p>–如果Linux系统用户PoorGuy使用hadoop命令创建一个文件,那么这个文件在HDFS中owner就是PoorGuy</p>

<p>–HDFS的权限目的:阻止好人做错事,而不是阻止坏人做坏事.HDFS相信,你告诉我你是谁,我就认为你是谁. <br>
注:正是因为这个原因所以安全性不高,但是企业不会将重要数据存在Hadoop上,Eg用户名密码等.</p>



<h2 id="安全模式">安全模式</h2>

<ul>
<li>namenode启动的时候,首先将映像文件载入内存,并执行编辑日志中的各项操作.</li>
<li>一旦在内存中成功建立文件系统数据的映射,则船舰一个新的fsimage文件.</li>
<li>此刻namenode运行在安全模式下,及namenode的文件系统对客户端来说是只读的.(显示目录,显示文件内容,写,删除,重命名都会失败)</li>
<li>在此阶段namenode收集datanode的报告,当书记块达到最小副本数以上的时候,会被认为是安全的,在一定比例下(可设置)的数据块被确定为”安全”后,再过若干时间,安全模式结束.</li>
<li>当检测到副本数不足的数据块时,该快会被复制直到达到最小副本数,系统中数据块的位置并不是由namenode维护的,而是以块列表形式存储在datanode中的.</li>
</ul>

<h2 id="附加">附加:</h2>

<p>HDFS上传原理简写:</p>

<ol>
<li>Client端发送一个添加文件到HDFS的请求给NameNode;</li>
<li>NameNode告诉Client端如何来分发数据块以及分发的位置.</li>
<li>Client端把数据分为快(block),然后把这些块分发到DataNode中;</li>
<li>DataNode在NameNode的指导下复制这些快,保持冗余.</li>
</ol>

<p>以上是通过书本和视频总结的一些,供大家学习交流.</p>

<p>小白创作,大佬勿喷,谢谢合作</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>