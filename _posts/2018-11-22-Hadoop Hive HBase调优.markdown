---
layout:     post
title:      Hadoop Hive HBase调优
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/ZYC88888/article/details/78993233				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div style="color:rgb(85,85,85);font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:16px;line-height:25.6px;">
性能调优之于Hadoop来说无异于打通任督二脉，对于Hadoop的计算能力会有质的的提升，而运维之于Hadoop来说，就好像金钟罩、铁布衫一般，有了稳定的运维，Hadoop才能在海量数据之中大展拳脚，两者相辅相成，缺一不可。</div>
<div style="color:rgb(85,85,85);font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:16px;line-height:25.6px;">
<br></div>
<div style="color:rgb(85,85,85);font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:16px;line-height:25.6px;">
总体来说，Hadoop运维维度取决于Hadoop的运行环境以及Hadoop软件本身。而Hadoop 性能调优是一项工程浩大的工作，它不仅涉及Hadoop本身的性能调优，还涉及硬件、操作系统和Java虚拟机的调优。<br></div>
<div style="color:rgb(85,85,85);font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:16px;line-height:25.6px;">
<br></div>
<div style="color:rgb(85,85,85);font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:16px;line-height:25.6px;">
<h2 style="font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-weight:100;line-height:1.1;color:rgb(69,69,69);font-size:2.15em;">
Hadoop参数调优:</h2>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;">
<span style="font-size:14px;">通过修改hadoop三个配置文件的参数来提高性能。主要有三个文件core-site.xml、hdfs-site.xml、mapred-site.xml。下面分别介绍这三个文件常用的参数配置。我们的环境上面的路径是: /usr/lib/hadoop/etc/hadoop</span></p>
<p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;">
<span style="font-size:14px;">三个配置文件介绍：</span></p>
<h3 style="font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-weight:100;line-height:1.1;color:rgb(69,69,69);font-size:1.7em;">
<a name="t4" style="background:transparent;color:rgb(79,161,219);"></a><a style="background:transparent;color:rgb(255,153,0);"></a>core-site.xml</h3>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;">
<span style="font-size:14px;">该文件中是集群的一些基本参数，与hadoop部署密切相关，但是对于性能优化作用不是特别明显。这里就简单介绍几个常用的配置参数。</span></p>
<table border="1" cellspacing="0" cellpadding="0" width="501" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;"><tbody><tr><td valign="top">
<p align="left">
<span style="font-size:14px;">fs.default.name</span></p>
</td>
<td valign="top">
<p align="left">
<span style="font-size:14px;">主节点地址。</span></p>
</td>
</tr><tr><td valign="top">
<p align="left">
<span style="font-size:14px;">hadoop.tmp.dir</span></p>
</td>
<td valign="top">
<p align="left">
<span style="font-size:14px;">集群的临时文件存放目录。</span></p>
</td>
</tr><tr><td valign="top">
<p align="left">
<span style="font-size:14px;">io.file.buffer.size</span></p>
</td>
<td valign="top">
<p align="left">
<span style="font-size:14px;">系统I/O的属性，读写缓冲区的大小。</span></p>
</td>
</tr><tr><td valign="top">
<p align="left">
<span style="font-size:14px;">io.seqfile.compress.blocksize</span></p>
</td>
<td valign="top">
<p align="left">
<span style="font-size:14px;">块压缩时块的最小块大小。</span></p>
</td>
</tr><tr><td valign="top">
<p align="left">
<span style="font-size:14px;">io.seqfile.lazydecompress</span></p>
</td>
<td valign="top">
<p align="left">
<span style="font-size:14px;">压缩块解压的相关参数。</span></p>
</td>
</tr></tbody></table><p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;">
<span style="font-size:14px;"> </span></p>
<h3 style="font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-weight:100;line-height:1.1;color:rgb(69,69,69);font-size:1.7em;">
<a name="t5" style="background:transparent;color:rgb(79,161,219);"></a><a style="background:transparent;color:rgb(255,153,0);"></a>hdfs-site.xml</h3>
<p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;">
<span style="font-size:14px;">该文件与HDFS子项目密切相关，其参数对集群性能调整具有很大影响。</span></p>
<table border="1" cellspacing="0" cellpadding="0" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;"><tbody><tr><td valign="top">
<p>
<span style="font-size:14px;">dfs.name.dir</span></p>
</td>
<td valign="top">
<p>
<span style="font-size:14px;">指定name镜像文件存放目录，如不指定则默认为core-site中配置的tmp目录。</span></p>
</td>
</tr><tr><td valign="top">
<p>
<span style="font-size:14px;">dfs.data.dir</span></p>
</td>
<td valign="top">
<p>
<span style="font-size:14px;">真正的datanode数据保存路径，可以写多块硬盘，中间以逗号分隔。</span></p>
</td>
</tr><tr><td valign="top">
<p>
<span style="font-size:14px;">dfs.replication</span></p>
</td>
<td valign="top">
<p>
<span style="font-size:14px;">hdfs数据块的复制份数，默认3，理论上份数越多跑数速度越快，但是需要的存储空间也更多。</span></p>
</td>
</tr><tr><td valign="top">
<p>
<span style="font-size:14px;">dfs.permissions</span></p>
</td>
<td valign="top">
<p>
<span style="font-size:14px;">是否需要角色权限验证，上传文件时会用到。一般设置false，设置为true有时候会遇到数据因为权限访问不了。</span></p>
</td>
</tr><tr><td valign="top">
<p>
<span style="font-size:14px;">dfs.block.size</span></p>
</td>
<td valign="top">
<p>
<span style="font-size:14px;">每个文件块的大小，默认是64M，对于大型文件可以设置为128M。</span></p>
</td>
</tr><tr><td valign="top">
<p>
<span style="font-size:14px;">dfs.namenode.handler.count</span></p>
</td>
<td valign="top">
<p>
<span style="font-size:14px;">NameNode 节点上面为处理datanode节点来来气的远程调用的服务线程数量。</span></p>
</td>
</tr><tr><td valign="top">
<p>
<span style="font-size:14px;">dfs.datanode.max.xcievers</span></p>
</td>
<td valign="top">
<p>
<span style="font-size:14px;">相当于linux下的打开文件最大数量，文档中无此参数，当出现DataXceiver报错的时候，需要调大。默认256</span></p>
</td>
</tr><tr><td valign="top">
<p>
<span style="font-size:14px;">dfs.datanode.handler.count</span></p>
</td>
<td valign="top">
<p>
<span style="font-size:14px;">datanode节点上为处理datanode节点的远程调用开启的服务线程数量，默认为3。当有很多HDFS客户端时可以设置更大。</span></p>
</td>
</tr></tbody></table><p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;">
<span style="font-size:14px;"> </span></p>
<h3 style="font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-weight:100;line-height:1.1;color:rgb(69,69,69);font-size:1.7em;">
<a name="t6" style="background:transparent;color:rgb(79,161,219);"></a><a style="background:transparent;color:rgb(255,153,0);"></a>mapred-site.xml</h3>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;">
<span style="font-size:14px;">该文件与mapreduce计算模型密切相关，其中的参数对集群的性能影响很大。</span></p>
<table border="1" cellspacing="0" cellpadding="0" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;"><tbody><tr><td valign="top">
<p>
<span style="font-size:14px;">mapred.job.tracker</span></p>
</td>
<td valign="top">
<p>
<span style="font-size:14px;">Job tracker地址</span></p>
</td>
</tr><tr><td valign="top">
<p>
<span style="font-size:14px;">mapred.job.tracker.handler.count</span></p>
</td>
<td valign="top">
<p>
<span style="font-size:14px;">jobtracker服务的线程数，一般默认为15.</span></p>
</td>
</tr><tr><td valign="top">
<p>
<span style="font-size:14px;">mapred.map.tasks</span></p>
</td>
<td valign="top">
<p>
<span style="font-size:14px;">默认每个job所使用的map数，意思是假设设置dfs块大小为64M，需要排序一个60M的文件，也会开启2个map线程，当jobtracker设置为本地是不起作用。</span></p>
</td>
</tr><tr><td valign="top">
<p>
<span style="font-size:14px;">mapred.reduce.tasks</span></p>
</td>
<td valign="top">
<p>
<span style="font-size:14px;">每个job的reduce任务数量，经常设置成与集群中存在的主机数量很接近的一个数值。</span></p>
</td>
</tr><tr><td valign="top">
<p>
<span style="font-size:14px;">mapred.tasktracker.map.tasks.maximum</span></p>
</td>
<td valign="top">
<p>
<span style="font-size:14px;">一个task tracker上可以同时运行的map任务的最大数量。</span></p>
</td>
</tr><tr><td valign="top">
<p>
<span style="font-size:14px;">mapred.tasktracker.reduce.tasks.maximum</span></p>
</td>
<td valign="top">
<p>
<span style="font-size:14px;">一个task tracker上可以同时运行的reduce任务的最大数量。</span></p>
</td>
</tr><tr><td valign="top">
<p>
<span style="font-size:14px;">io.sort.mb</span></p>
</td>
<td valign="top">
<p>
<span style="font-size:14px;">排序所使用的内存数量。默认值：100M，需要与mapred.child.java.opts相配 默认：-Xmx200m。不能超过mapred.child.java.opt设置，否则会OOM。</span></p>
</td>
</tr><tr><td valign="top">
<p>
<span style="font-size:14px;">io.sort.factor</span></p>
<p>
<span style="font-size:14px;"> </span></p>
</td>
<td valign="top">
<p>
<span style="font-size:14px;">处理流merge时的文件数， 默认：10</span></p>
<p>
<span style="font-size:14px;">,建议调大到100.</span></p>
</td>
</tr></tbody></table><h2 style="font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-weight:100;line-height:1.1;color:rgb(69,69,69);font-size:2.15em;">
<a name="t7" style="background:transparent;color:rgb(79,161,219);"></a><a style="background:transparent;color:rgb(255,153,0);"></a><span style="font-size:14px;">参数设置调优：</span></h2>
<p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;">
<span style="font-size:14px;">对于这些常用的参数设置，需要整体把握的一些主体思想：</span></p>
<p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;">
<span style="font-size:14px;">（1）       Reduce个数设置：</span></p>
<p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;">
<span style="font-size:14px;">A． 如果reduce个数设置太小，单个reducetask执行速度很慢，这样出错重新调试的时间花销就比较多。</span></p>
<p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;">
<span style="font-size:14px;">B． 如果怕Reduce个数设置太大，Shuffle开销及调度开销很大，job输出大量文件，影响后续Job的执行。</span></p>
<p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;">
<span style="font-size:14px;">C． 推荐的reduce的个数。单个reducetask处理数据量介于1~10G之间，reduce的个数要少于map的个数。</span></p>
<p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;">
<span style="font-size:14px;">（2）       压缩中间数据，用CPU换磁盘和网络，设置mapred.compress.map.output设为true</span></p>
<p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;">
<span style="font-size:14px;">A． 减少磁盘操作</span></p>
<p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;">
<span style="font-size:14px;">B． 减少网络传输数据量</span></p>
<br></div>
<div style="color:rgb(85,85,85);font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:16px;line-height:25.6px;">
<br></div>
<div style="color:rgb(85,85,85);font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:16px;line-height:25.6px;">
<br></div>
<div style="color:rgb(85,85,85);font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:16px;line-height:25.6px;">
<span><strong>1、Hadoop客户端</strong></span></div>
<div style="color:rgb(85,85,85);font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:16px;line-height:25.6px;">
<br></div>
<div style="color:rgb(85,85,85);font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:16px;line-height:25.6px;">
<br></div>
<div style="color:rgb(85,85,85);font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:16px;line-height:25.6px;">
在开始介绍性能调优和运维之前，我们先来回顾一下Hadoop的架构，Hadoop是主从（master/slave）架构，具体存储和计算都是由节点负责，主节点负责调度、元数据存储、资源管理等。我们不禁会问，应该在哪个节点提交任务、上传文件呢？当然，主节点可以，集群上任意一个节点也可以，但是这样做会让运维的难度增大，使得集群不在纯净，所以不推荐这样做。</div>
<div style="color:rgb(85,85,85);font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:16px;line-height:25.6px;">
<br></div>
<div style="color:rgb(85,85,85);font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:16px;line-height:25.6px;">
我们很自然地希望将这些提交任务的工作交由一个节点来完成，但是又希望这个节点在集群之外，事实上，这样的节点是存在的我们称之为Hadoop客户端。Hadoop客户端可以被认为是独立于集群之外，或者是集群内特殊的一个节点，它不参与计算和存储，可以用来提交任务。</div>
<div style="color:rgb(85,85,85);font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:16px;line-height:25.6px;">
<br></div>
<div style="color:rgb(85,85,85);font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:16px;line-height:25.6px;">
<img src="https://img-blog.csdn.net/20170822153200345" alt="" style="border:none;"><br><br></div>
<div style="color:rgb(85,85,85);font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:16px;line-height:25.6px;">
<br></div>
<div style="color:rgb(85,85,85);font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:16px;line-height:25.6px;">
该节点的部署很简单，将集群内任意一台机器的安装文件，不做任何修改直接全部发送到Hadoop节点的相同目录下即可部署。这么一来，在Hadoop客户端就可以直接执行Hadoop客户端相关命令，如Hadoop jar、Hadoop dfs等等，甚至直接可以在Hadoop客户端执行start-all.sh脚本，由于在集群的配置文件slaves中并没有配置Hadoop客户端的IP地址，所以Hadoop不会在Hadoop客户端启动DataNode、NodeManager进程。</div>
<div style="color:rgb(85,85,85);font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:16px;line-height:25.6px;">
<br></div>
<div style="color:rgb(85,85,85);font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:16px;line-height:25.6px;">
当这个节点部署好以后，Hadoop客户端就接管了集群中所有与计算存储无关的任务，例如向HDFS中上传下载文件、提交计算任务等等。那么自然而然的，Hive、Sqoop的安装位置也应该是Hadoop客户端。本质上，Hive和Sqoop对于Hadoop来说就是一个客户端，通过提交任务和集群进行交互。那么一个规范的集群，用户应该通过访问Hadoop客户端来和Hadoop集群进行交互。</div>
<div style="color:rgb(85,85,85);font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:16px;line-height:25.6px;">
<br></div>
<div style="color:rgb(85,85,85);font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:16px;line-height:25.6px;">
<br></div>
<div style="color:rgb(85,85,85);font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:16px;line-height:25.6px;">
<span><strong><span>2、Hadoop性能调优</span></strong></span></div>
<div style="color:rgb(85,85,85);font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:16px;line-height:25.6px;">
<br></div>
<div style="color:rgb(85,85,85);font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:16px;line-height:25.6px;">
<br></div>
<div style="color:rgb(85,85,85);font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:16px;line-height:25.6px;">
对于Hadoop来说，初始配置和调优有的配置，两者性能之间的差距十分明显，为了加深印象，我们不妨先运行一个10GB大小的WordCount作业，或者是对于一个Hive大表执行一条HiveQL，记录下运行时间，然后和调优过后的时间进行比较。</div>
<div style="color:rgb(85,85,85);font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:16px;line-height:25.6px;">
<br></div>
<div style="color:rgb(85,85,85);font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:16px;line-height:25.6px;">
Hadoop性能调优需要全方面进行考虑，本身Hadoop就不是独立存在的，它和Java虚拟机（JVM）、操作系统甚至硬件都是有很紧密的联系。管理员负责为用户作业提供一个高效的运行环境，需要从全局出发，通过调整一些关键参数提高系统的吞吐率和性能。总体上来看，管理员需从硬件选择、操作系统参数调优、JVM参数调优和Hadoop参数调优等四个角度入手，为Hadoop用户提供一个高效的作业运行环境。</div>
<div style="color:rgb(85,85,85);font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:16px;line-height:25.6px;">
<div></div>
<div><br></div>
<div><img src="https://img-blog.csdn.net/20170822173119506" alt="" style="border:none;"><br></div>
<div><br></div>
<div><br></div>
<div><strong><span>2.1、硬件选择</span></strong></div>
<div><br></div>
<div>Hadoop的优点之一就是能在普通硬件上良好地运行，所以不需要购买昂贵的硬件。但是仍然需要考虑实际工作中的硬件符合。例如CPU、内存和硬盘。</div>
<div><br></div>
<div><strong>原则1：主节点可靠性要好于从节点</strong></div>
<div>
<div></div>
</div>
<div><br></div>
<div>Hadoop是主从架构，Hadoop自身架构的基本特点决定了其硬件配置的选项。Hadoop采用了Master/Slave架构，其中，主节点维护了全局元数据信息，是YARM的ResourceManager和HDFS的NameNode，其重要性远远大于slave。一旦主节点挂掉，会造成整个集群不可用。在较低Hadoop版本中，master存在单点故障问题，因此，master的配置应远远好于各个slave。<br></div>
<div><br></div>
<div><strong>原则2：多路多核、高频率CPU、大内存</strong></div>
<div><br></div>
<div>NameNode的内存决定了集群保存文件数量的总量，ResourceManager同时运行的作业会消耗一定的内存。</div>
<div><br></div>
<div>对于NameNode来说，在内存方面要特别考虑，每当HDFS启动的时候，NameNode会将元数据加载进内存。所以HDFS所能存储的文件总数受限于NameNode的内存容量。而SecondaryNameNode的配置应该与NameNode相同，当NameNode宕机时，这些信息被保存在ResourceManagerde的内存中。ResourceManager的内存需求也是很高，与NameNode不同的是，它与HDFS文件总数无关，与需要处理的作业有关。</div>
<div><br></div>
<div>从节点负责计算和存储，所以需要保证CPU和内存能够满足计算要求，而磁盘满足存储要求。对YARN来说，它要运行各种各样的任务，当然希望有足够的计算资源。一般来说，内存和虚拟CPU要满足一个线性比例，这样在分配的时候才不容易造成浪费。</div>
<div><br></div>
<div>虚拟CPU的个数计算公式=CPU数X单个CPU核数X单个CPU核的超线程数”，例如，一个双路的六核CPU，具有HT超线程技术，那么它的虚拟CPU个数是2X6X2=24个，按照每个虚拟CPU分配4GB8GB的原则，服务内存应该为96GB-192GB。才外还需要操作系统以及其他其他服务预留资源。</div>
<div><br></div>
<div><strong>原则3：根据数据量确定集群规模</strong></div>
<div><br></div>
<div>在规划的存储能力时候，首先考虑因素是块的副本数，如果副本数是3，那么平均下来1TB文件在集群实际上需要3TB文件，平摊到每个节点就是每个节点所需要的最小容量。当然这个容量还是不够的，最好是1.3倍。</div>
<br><strong>原则4：不要让网络IO成为瓶颈</strong></div>
<div style="color:rgb(85,85,85);font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:16px;line-height:25.6px;">
<br></div>
<div style="color:rgb(85,85,85);font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:16px;line-height:25.6px;">
Hadoop作业通常是IO密集型而非计算密集型，瓶颈通常集中在IO上。计算能力可以通过添加新节点进行线性扩展，而IO方面的瓶颈却很难进行扩展。例如硬盘读写能力，交换机背板带宽等。<br><div></div>
<div></div>
<div><strong><br></strong></div>
<div><strong><br></strong></div>
<div><strong><span>2.2、操作系统调优与JVM调优</span></strong></div>
<div><strong><br></strong></div>
<div><strong><br></strong></div>
<div><strong>( 1 )、避免使用SWAP分区，因为将Hadoop守护进程的数据交换到磁盘的行为可能会导致操作超时</strong></div>
<div><br></div>
<div>swap分区是指在系统的物理内存不够用的时候，把物理内存中的一部分空间释放出来，以供当前运行的程序使用。通过vm.swapiness参数进行控制，值域是0-100，值越高说明操作系统内核更加积极地将应用程序的数据交换到磁盘。将Hadoop守护进程的数据交换到磁盘的行为是危险的，有可能导致操作超时，所以将该值设定为0。</div>
<div><br></div>
<div>在Linux中，如果一个进程的内存空间不足，那么，它会将内存中的部分数据暂时写到磁盘上，当需要时，再将磁盘上的数据动态置换到内存中，通常而言，这种行为会大大降低进程的执行效率。在MapReduce分布式计算环境中，用户完全可以通过控制每个作业处理的数据量和每个任务运行过程中用到的各种缓冲区大小，避免使用swap分区。</div>
<div><br>
具体方法是调整/etc/sysctl.conf文件中的vm.swappiness参数。vm.swappiness有效范围是0~100，值越高表明内核应该更积极将应用程序的数据交换到磁盘，较低的值表示将延迟这种行为，而不是强制丢弃文件系统的缓冲区。</div>
<div><br></div>
<div>[root@Hadoop ~]# echo "vm.swappiness = 0 " &gt;&gt; /etc/sysctl.conf <br></div>
<div><br></div>
<div><strong>( 2 )、调整内存分配策略</strong></div>
<div><br></div>
<div>操作系统内核根据vm.overcommit_memory的值来决定内存分配策略，并且通过vm.overcommit_ratio的值来设定超过物理内存的比例。前者一般设置为2，后者根据实际情况调整。</div>
<div><br></div>
<div>[root@NameNode ~]# echo 2 &gt; /proc/sys/vm/overcommit_memory <br></div>
<div><br></div>
<div>
<p>
进程通常调用malloc()函数来分配内存，内存决定是否有足够的可用内存，并允许或拒绝内存分配的请求。<a href="http://lib.csdn.net/base/linux" rel="nofollow" title="Linux知识库" style="color:rgb(12,137,207);">Linux</a>支持超量分配内存，以允许分配比可用RAM加上交换内存的请求。</p>
<p>
<br></p>
<p>
vm.overcommit_memory参数有三种可能的配置。0表示检查是否有足够的内存可用，如果是，允许分配；如果内存不够，拒绝该请求，并返回一个错误给应用程序。1 表示根据vm.overcommit_ratio定义的值，允许分配超出物理内存加上交换内存的请求。vm.overcommit_ratio参数是一个百分比，加上内存量决定内存可以超量分配多少内存。例如，vm.overcommit_ratio值为50，而内存有1GB，那么这意味着在内存分配请求失败前，加上交换内存，内存将允许高达1.5GB的内存分配请求。2
 表示内核总是返回true。</p>
</div>
<div><strong><br></strong></div>
<div><strong>( 3 )、修改net.core.somaxconn参数</strong></div>
<div><br></div>
<div>该参数是Linux中的内核参数，表示socket监听backlog的上限，backlog是套接字的监听队列，默认是128，socket服务器会一次性处理backlog中的所有请求，当服务器繁忙的时候，128远远不够的，建议大于等于32768。并且修改core-default.xml中的.ipc.server.listen.queue.size参数（该参数表示服务端套接字的监听队列长度，即backlog长度）。</div>
<div><strong><br></strong></div>
<div>
<div><strong>( 4 )、选择合适的文件系统，并且禁用文件的访问时间</strong></div>
<div><br></div>
<div>Hadoop运行在Linux系统，常见文件系统就是ext3、ext4、xfs。对于不同文件系统，ext3、ext4性能会有所不同。</div>
<div><br></div>
<div>当文件系统被格式化以后，还需要做一项优化工作，禁用文件的访问时间。对于普通管得机器，这项功能可以让用户知道哪些文件近期被查看或者修改，但是对于HFDS没有多大意义。因为HDFS目前不支持这类操作，并且获取某个文件的某个块什么时候被访问并没有意义。如果记录了文件的访问时间的话，在每次读操作的时候，也会伴随一个写操作，这个开销是无谓的。所以在挂载数据分区的时候，需要禁用文件的访问时间。</div>
<div><br></div>
<div><strong>( 5 )、增大同时打开的文件描述符和网络连接上限</strong></div>
<div><br></div>
<div>
<div>对内核来说，所有打开的文件都通过文件描述符引用，文件描述符是一个非负整数。当打开一个现有文件或者创建一个文件的时候，内核向进程返回一个文件描述符。在CDH<a href="https://www.iteblog.com/archives/tag/hadoop/" rel="nofollow" title="" style="color:rgb(12,137,207);"></a>集群中，由于涉及的作业和任务数目非常多。Hadoop的作业经常会读写大量文件，需要增大同时打开文件描述符的上限。因此，管理员在启动Hadoop集群时，使用ulimit命令将允许同时打开的文件描述符数目上限增大至一个合适的值。同时，调整内核参数net.core.somaxconn网络连接数目至一个足够大的值。此外，Hadoop
 RPC采用了epoll作为高并发库，如果你使用的Linux内核版本在2.6.28以上，你需要适当调整epoll的文件描述符上限。</div>
<div>
<div><br></div>
<div><strong>( 6 )、设置合理的预读取缓冲区大小</strong></div>
<div><br></div>
<div>磁盘I/O性能的发展远远滞后于CPU和内存，因而成为现代计算机系统的一个主要瓶颈。预读可以有效地减少磁盘的寻道次数和应用程序的I/O等待时间，是改进磁盘读I/O性能的重要优化手段之一。管理员可使用Linux命令blockdev设置预读取缓冲区的大小，以提高Hadoop中大文件顺序读的性能。当然，也可以只为Hadoop系统本身增加预读缓冲区大小。<br></div>
<div><br></div>
<div><strong>( 7 )、设置内核死锁参数</strong></div>
<div><br></div>
<div>
<div>softlockup(watchdog)用于检测系统调度是否正常，即软锁的情况，当发生softlockup时，内核不能调度，但还能响应中断，对用户的表现可能为：能ping通，但无法登陆系统，无法进行正常操作。其基本原理为：为每个CPU启动一个内核线程(watchdog/x)，此线程为优先级最高的实时线程，在该线程得到调度时，会更新相应的计数(时间戳)，同时会启动定时器，当定时器到期时检查相应的时间戳，如果超过指定时间，都没有更新，则说明这段时间内都没有发生调度(因为此线程优先级最高)，则打印相应告警或根据配置可以进入panic流程，警告中包含占用时长和进程名及pid。</div>
<div><br></div>
<div>[root@NameNode ~]# echo  60 &gt; /proc/sys/kernel/watchdog_thresh      # 超时阈值，目前设置为60秒</div>
<div>[root@NameNode ~]# echo  1 &gt; /proc/sys/kernel/softlockup_panic        # 默认为0，即只打印日志；1为宕机重启</div>
<div><br></div>
<div>
<div><strong>（8 )、关闭THP</strong></div>
<div><br></div>
<div>Huge  Pages就是大小为2MB-1GB的内存页，而THP（Transparent  Huge Pages）是一个使管理Huge Pages自动化的抽象层，但是在运行Hadoop作业时，THP会引起CPU占用率偏高，需要将其关闭。</div>
<br></div>
</div>
<div><strong>（9 )、JVM参数调优</strong></div>
<div><br></div>
<div>由于Hadoop中的每个服务和任务均会运行在一个单独的JVM中，因此，JVM的一些重要参数也会影响Hadoop性能。管理员可通过调整JVM FLAGS和JVM垃圾回收机制提高Hadoop性能，调整后的执行效率大概有4%的提升。</div>
</div>
</div>
</div>
<div>
<div></div>
</div>
<div><br></div>
<div><br></div>
<div><strong><span>2.3、Haddop参数调优</span></strong></div>
<div><br></div>
<div><span style="line-height:25.6px;"><br></span></div>
<div><span style="line-height:25.6px;">Hadoop为用户作业提供了多种可配置的参数，以允许用户根据作业特点调整这些参数值使作业运行效率达到最优。</span><br></div>
<div><br></div>
<div>
<div><strong>2.3.1、hdfs-site.xml</strong></div>
<div><span style="color:inherit;line-height:1.6;"><br></span></div>
<div><span style="color:inherit;line-height:1.6;">&lt;property&gt;</span></div>
<div>    &lt;name&gt;dfs.blocksize&lt;/name&gt;<br></div>
<div>    &lt;value&gt;134217728&lt;/value&gt;<br></div>
<div>&lt;/property&gt;<br></div>
<div><span style="color:inherit;line-height:1.6;">该参数表示Hadoop文件</span><span style="color:inherit;line-height:1.6;"></span><span style="color:inherit;line-height:1.6;">块大小，通常设置为128MB或者256MB。</span><br></div>
<div><span style="color:inherit;line-height:1.6;"><br></span></div>
<div><span style="color:inherit;line-height:1.6;"></span>
<div>&lt;property&gt;<br></div>
<div>    &lt;name&gt;dfs.replication&lt;/name&gt;<br></div>
<div>    &lt;value&gt;3&lt;/value&gt;<br></div>
<div>&lt;/property&gt;</div>
<div>该参数表示控制HDFS文件的副本数，默认是3，当许多任务同时读取一个文件时，读取可能造成瓶颈，这时增大副本数能有效缓解，但会造成大量的磁盘空间占用，这时只修改Hadoop客户端的配置，这样，从Hadoop客户端上传的文件的副本数以Hadoop客户端的为准。<br></div>
<div><br></div>
</div>
<div><span style="color:inherit;line-height:1.6;">&lt;property&gt;</span><br></div>
<div>    &lt;name&gt;dfs.namenode.handler.count&lt;/name&gt;<br></div>
<div>    &lt;value&gt;40&lt;/value&gt;<br></div>
<div>&lt;/property&gt;</div>
<div>该参数表示NameNode同时和DataNode通信的线程数，默认是10，将其增大为40。<br></div>
</div>
</div>
<div style="color:rgb(85,85,85);font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:16px;line-height:25.6px;">
<p>
<br></p>
<p>
<strong>2.3.2、core-site.xml</strong></p>
<p>
<strong><br></strong></p>
<p>
&lt;property&gt;<br></p>
<p>
    &lt;name&gt;io.file.buffer.size&lt;/name&gt;<br></p>
<p>
    &lt;value&gt;65536&lt;/value&gt;<br></p>
<p>
&lt;/property&gt;<br></p>
<p>
缓冲区的大小用于读写HDFS文件，还有map中间结果输出都到这个缓冲区容量，默认4KB，增加128KB。</p>
<div>
<div><br></div>
<div><strong>2.3.3、Yarn-site.xml</strong></div>
<div><strong><br></strong></div>
<div><span style="color:inherit;line-height:1.6;">&lt;property&gt;</span><br></div>
<div>    &lt;name&gt;yarn.scheduler.increment-allocation-mb&lt;/name&gt;<br></div>
<div>    &lt;value&gt;512&lt;/value&gt;<br></div>
<div>&lt;/property&gt;<br></div>
<div>该参数表示内存申请大小的规整化单位，默认是1024MB，即如果申请的内存是1.5GB，将被计算为2GB。</div>
<div><br></div>
<div>
<div>&lt;property&gt;<br></div>
<div>    &lt;name&gt;yarn.scheduler.increment-allocation-vcores&lt;/name&gt;<br></div>
<div>    &lt;value&gt;1&lt;/value&gt;<br></div>
<div>&lt;/property&gt;</div>
该参数表示虚拟CPU申请的规整化单位，默认是1个。</div>
<div><br></div>
<div>&lt;property&gt;<br></div>
<div>    &lt;name&gt;yarn.scheduler.maximum-allocation-mb&lt;/name&gt;<br></div>
<div>&lt;value&gt;65536&lt;/value&gt;<br></div>
<div>
<div>该参数表示单个任务（容器）能够申请到的最大内存，根据容器内存总量进行设置，默认8GB。</div>
<br><div>&lt;property&gt;<br></div>
<div>    &lt;name&gt;yarn.scheduler.maximum-allocation-vcores&lt;/name&gt;<br></div>
<div>    &lt;value&gt;32&lt;/value&gt;<br></div>
<div>&lt;/property&gt;<br></div>
该参数表示单个任务（容器）能够申请到的最大虚拟CPU数，根据容器虚拟CPU总数进行设置，默认是4。</div>
<div>
<div><span><br></span></div>
<div><span>&lt;property&gt;</span><br></div>
<div>    &lt;name&gt;yarn.scheduler.minimum-allocation-mb&lt;/name&gt;<br></div>
<div>    &lt;value&gt;1&lt;/value&gt;<br></div>
<div>&lt;/property&gt;</div>
<span>该参数表示单个任务（容器）能够申请到的最小内存，根据容器内存总量进行设置，默认1GB。</span><br></div>
<div><span><br></span></div>
<div>&lt;property&gt;<br></div>
<div>    &lt;name&gt;yarn.scheduler.minimum-allocation-vcores&lt;/name&gt;<br></div>
<div>    &lt;value&gt;1&lt;/value&gt;<br></div>
<div>&lt;/property&gt;<br></div>
<div><span>该参数表示单个任务（容器）能够申请到的最小虚拟CPU数，根据容器虚拟CPU总数进行设置，默认是1。</span><br></div>
<div><br></div>
<div><strong>2.3.4、mapred-site.xml</strong></div>
<div><br></div>
</div>
<div>&lt;property&gt;<br></div>
<div>    &lt;name&gt;mapreduce.map.output.compress&lt;/name&gt;<br></div>
<div>    &lt;value&gt;true&lt;/value&gt;<br></div>
<div>&lt;/property&gt;<br></div>
<div>该参数表示Map任务的中间结果是否进行压缩，设定为true时，会对中间结果进行压缩，这样会减少数据传输时贷款的需要。</div>
<div><br></div>
<div>&lt;property&gt;<br></div>
<div>    &lt;name&gt;mapreduce.map.memory.mb&lt;/name&gt;<br></div>
<div>    &lt;value&gt;2048&lt;/value&gt;<br></div>
<div>&lt;/property&gt;<br></div>
<div>该参数表示Map任务需要的内存大小。</div>
<div><br></div>
<div>&lt;property&gt;<br></div>
<div>    &lt;name&gt;mapreduce.map.cpu.vcores&lt;/name&gt;<br></div>
<div>    &lt;value&gt;1&lt;/value&gt;<br></div>
<div>&lt;/property&gt;<br></div>
<div>该参数表示Map任务所需要的虚拟CPU数，默认是1。</div>
<div><br></div>
<div>&lt;property&gt;<br></div>
<div>    &lt;name&gt;mapreduce.reduce.memory.mb&lt;/name&gt;<br></div>
<div>    &lt;value&gt;2048&lt;/value&gt;<br></div>
<div>&lt;/property&gt;<br></div>
<div>该配置为Reduce任务所需要的内存大小。</div>
<div><br></div>
<div>&lt;property&gt;<br></div>
<div>    &lt;name&gt;mapreduce.reduce.cpu.vcores&lt;/name&gt;<br></div>
<div>    &lt;value&gt;1&lt;/value&gt;<br></div>
<div>&lt;/property&gt;</div>
<div>该配置为Reduce任务向调度器需要的虚拟CPU数，默认是1，根据容器虚拟CPU数设定。</div>
<div><br></div>
<div><br></div>
<div>
<div style="line-height:25.6px;">
<div>
<div><strong><span>3、Hive调优</span></strong></div>
<div><br></div>
<div><br></div>
<div>在实际应用中，我们会选择Hive而不是Mapduce开发应用，所以Hive使用的频率会非常高，有必要对其进行调优，调优的而方法是参数配置、优化HQL等等，Hive的配置文件是$Hive_HOME/conf/hive=site.xml文件。</div>
<div><br></div>
<div>其实Hive已经做了很多原生优化的工作了，所以在执行效率和稳定性上已经很不错，但是仍然有优化的空间，经过有针对性的调优，有利于HIve有效的运行。</div>
<div><br></div>
<div><strong>3.1、JOIN优化</strong></div>
<div><strong><br></strong></div>
<div>在执行JOIN语句中，需要将大表放在右边以获得更好的性能，如果一个表小到能影响全部加在到内存中，那么可以考虑执行map端JOIN。</div>
<div><br></div>
<div><strong>3.2、Reducer的数量</strong></div>
<div><strong><br></strong></div>
<div>将Reducer最大值设定为n*0.95，其中n为NodeManger数量，通过设置hive.reducers.max可以增加Reducer数量，但是这样并不能直接增大Hive作业的Reducer的个数，Hive作业的Redurce个数可以直接由以下两个参数配置决定的。</div>
<div><br></div>
<div>hive.exec.reducers.bytes.per.reducer（默认1GB）</div>
<div>hive.exec.reducers.max（默认999）</div>
<div><br></div>
<div>计算Reducer个数的公式为“Reducer的个数=min(参数是2，总输入蜀将/参数1）”，所以如果输入数据是5GB情况相爱，Hive会开启5个Reducer，我们可以通过改变这两个参数，来达到控制Reducer个数的目的。</div>
<div><br></div>
<div><strong>3.3、并行执行</strong></div>
<div><strong><br></strong></div>
<div>一个HQL会被Hive拆分成为多个作业，这些作业有时候相互之间会有依赖，有时候则相互独立，如表1和表2JOIN的结果与表3和表4JOIN的结果再JOIN，那么<span>表1和表2JOIN的结果与表3和表4JOIN这两个作业则是相互独立，而最后一次JOIN的作业依赖于前两个作业。从逻辑上看，第一个作业和第二个作业可以同时执行。但是HIve默认不考虑并行性。一次执行作业，我们可以通过设置参数hive.exec.paraller为true，开启Hive的并行模式。</span></div>
<div><br></div>
<div><br></div>
<div><strong><span>4、Hbase调优</span></strong></div>
<div><br></div>
<div><br></div>
<div>Hbase在使用过程中也需要调优，才能保证在极大的数据量下满足业务的需求。但Hbase调优其实比Hadoop要复杂和灵活一些，因为面对的情况不一样，如高并发读、高并发写、高并发读写混合，要求低延迟，等等，所以经常会用对某几个参数反复调试才能达到最佳效果。Hbase的调优覆盖是全方面的，有表设计上的，有运维上的，有硬件上的，还有配置参数上的。</div>
<div><br></div>
<div><img src="https://img-blog.csdn.net/20171012101032944" alt="" style="border:none;"><br></div>
<div><br></div>
<div><br></div>
<div><strong>4.1、通用调优</strong></div>
<div><br></div>
<div><span>Hbase是面向业务，业务场景不同，调优方式不同。例如，有些场景读多写少，有些场景写少读多，有些场景读写各半。Hbase有些通用优点几乎适用于所有场景，能让Hbase的性能获得整体提升。</span><br></div>
<div><span><br></span></div>
<div><span>增大Zookeeper连接数：Zookeeper最大连接数会影响Hbase的并发性能，最大连接数可以通过Zookepper上的maxClientCxns参数进行配置，在配置时候注意考虑硬件的性能。</span></div>
<div><span><br></span></div>
<div><span>适当的region大小：region越大越少，性能越好。</span></div>
<div><span><br></span></div>
<div><span>Region处理器数量：该配置定义了每一个RegionServer的RPC处理器的数量。Reguin Server通过RPC处理器接受外部请求并且加以处理。所以增加RPC处理的数量可以一定程度提高Hbase接受请求的能力，也就是RegionServer接受请求的能力。这个参数跟内存息息相关。</span></div>
<div><span><br></span></div>
<div><span>RegionServer内存：推荐32GB，再大的话，对性能影响不大。</span></div>
<div><span><br></span></div>
<div><span>考虑SSD硬盘：当对性能比较严格苛刻时候，采取SSD，这样性能可以得到显著提升，Hbae的瓶颈一般在硬盘和网络。</span></div>
<div><br></div>
<div><strong>4.2、客户端调优</strong></div>
<div><strong><br></strong></div>
<div>因为Hbase的读写请求都是由客户端发起的，所以根据不同的业务改变客户端的查询和写入方式，也能使Hbase的性能有所提升，并且客户端的参数的作用于都是会话范围，非常灵活。例如，客户端写入缓冲区、禁止自动刷新、设置客户端高速缓存、并行插入框架等等。</div>
<div><strong><br></strong></div>
<div><strong>4.3、表设计调优</strong></div>
<div><br></div>
<div>行健设计：行健设计对Hbase来说非常重要。它的设计直接影响了查询模式、查询性能和数据分布。</div>
<div><br></div>
<div>不要在一张表里面定义太多的列族。</div>
<div><br></div>
<div>在创建表时候，可以进行与分区：这样可以避免表频繁地进行solit，数据自然在集群分散分布。</div>
<div><br></div>
<div><br></div>
<div><span><strong>5、Hadoop运维</strong></span></div>
</div>
<div><br></div>
<div><br></div>
<div><span style="line-height:1.6;">Hadoop运维无外乎，监控整个集群的健康状态，了解磁盘的剩余空间，及时处理死掉的datanode，对磁盘碎片进行处理，</span><span style="line-height:1.6;">以便提高集群的运行效率。当集群中存在运行任务时，需要对任务的参数进行控制，保证整个集群能够正常的高效的完成任务</span><span style="line-height:1.6;">如果出现异常，需要分析异常，调整参数。一般的hadoop运维都是程序员出生，不然很难弄。你对hadoop一知半解，那整个</span><span style="line-height:1.6;">Hadoop集群出现问题，你都不知道从何入手。</span><br></div>
<div><span style="line-height:1.6;"><br></span></div>
<div><span style="line-height:1.6;"><img src="https://img-blog.csdn.net/20170828190601344" alt="" style="border:none;"><br></span></div>
<div><strong><br></strong></div>
<div>Cloudera Manager作为Hadoop大数据平台的管理工具，本身具有四大功能：管理功能，监控功能、诊断功能和集成功能。</div>
<div><br></div>
<div><img src="https://img-blog.csdn.net/20171009113621948" alt="" style="border:none;"><br></div>
<div><br></div>
<div><strong><br></strong></div>
<div><strong>5.1、查看日志</strong></div>
<div><strong><br></strong></div>
<div>日志是Hadoop运维最重要的依据，无论遇到什么异常情况，通常首先要做的就是查看日志。tail -f 查看 命令可以实时查看更新日志：$HADOOP_HOME/logs/下NameNode、DataNode、ResourceManager和NodeManager的日志。</div>
<div><strong><br></strong></div>
<div><strong><br></strong></div>
<strong>5.2、清理临时文件</strong></div>
<div style="line-height:25.6px;"><br></div>
<div style="line-height:25.6px;">很多时候，由于对集群的操作太过频繁，或是日志输出不合理，日志文件或者历史文件可能变得十分巨大，影响正常HDFS存储，可以视情况定期清理。有时候，我们发现，即使删除了某些大文件，但是df -h查看分区占用率依旧填满。那是什么原因呢？</div>
<div style="line-height:25.6px;"><br>
原来，Linux系统下文件名是存在父目录的block里面，并指向这个文件的inode节点，这个文件的inode节点再指向存放这个文件的block数据块。当我们删除一个文件时，实际上并不是清除了inode节点和block数据块。只是在这个文件的父目录里面的block中，删除了这个文件的名字，从而使这个文件名消失，并且无法指向这个文件的inode节点而已。</div>
<div style="line-height:25.6px;"><br>
所以，rm -f 这个操作只是将文件的 i_nlink减少了，如果没有其它的链接 i_nlink就为0了。但是由于该文件依然被进程引用，因此，此时文件对应的 i_count并不为0，所以执行rm操作，系统并没有真正的删除这个文件，只有当 i_nlink和 i_count都为 0 的时候，这个文件才会被真正的删除。也就是说，必须要解除该进程对该文件的调用，才能真正的删除。<br><br>
lsof简介lsof(list open files)是一个列出当前系统打开文件的工具。查看已经删除的文件，空间有没有释放，没有的话kill掉pid<br><br>
lsof -n |grep deleted<br></div>
<div style="line-height:25.6px;"><strong><br></strong></div>
<div style="line-height:25.6px;"><strong><br></strong></div>
<div style="line-height:25.6px;"><strong>5.3、定期执行数据均衡脚本</strong></div>
<div style="line-height:25.6px;"><strong><br></strong></div>
<div style="line-height:25.6px;">导致HDFS数据不均衡的原因有很多种，如新增一个DataNode、快速删除HDFS上的大量文件、计算任务分布不均匀等等。可以通过执行Hadoop自带的均衡器脚本来重新平衡整个集群，脚本的路径是$Hadoop_HOME/bin/start-balancer.sh。</div>
</div>
</div>
            </div>
                </div>