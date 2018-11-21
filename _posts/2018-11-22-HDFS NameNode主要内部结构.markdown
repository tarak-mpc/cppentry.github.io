---
layout:     post
title:      HDFS NameNode主要内部结构
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p style="margin-left:0px;line-height:1.2;border-bottom-width:1px;border-bottom-style:solid;border-bottom-color:rgb(238,238,238);color:rgb(51,51,51);">
<span style="font-family:SimSun;"><span style="font-size:14px;"><span style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, 'Segoe UI', Arial, freesans, sans-serif, 'Apple Color Emoji', 'Segoe UI Emoji', 'Segoe UI Symbol';font-size:16px;line-height:25.6px;"><img src="http://t.dbdao.com/wp-content/uploads/2015/09/h1-750x410.jpg" alt="h1"></span></span></span></p>
<p style="margin-left:0px;line-height:1.2;border-bottom-width:1px;border-bottom-style:solid;border-bottom-color:rgb(238,238,238);color:rgb(51,51,51);">
<span style="font-size:24px;font-family:'Trebuchet MS', verdana, arial, helvetica, sans-serif;line-height:25.6px;">1、HDFS结构</span></p>
<p style="margin-left:0px;line-height:1.2;border-bottom-width:1px;border-bottom-style:solid;border-bottom-color:rgb(238,238,238);">
<span style="line-height:25.6px;"><span style="line-height:21px;background-color:rgb(250,247,239);"><span style="line-height:21px;background-color:rgb(250,247,239);"><span style="font-size:24px;"><span style="line-height:21px;background-color:rgb(250,247,239);"><span style="font-family:'Comic Sans MS';color:#663333;"><span style="color:rgb(51,51,51);font-size:16px;font-family:'Helvetica Neue', Helvetica, 'Segoe UI', Arial, freesans, sans-serif, 'Apple Color Emoji', 'Segoe UI Emoji', 'Segoe UI Symbol';line-height:25.6px;"><span style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;">hdfs的采用的是master/slave模型，一个hdfs
 cluster包含一个NameNode和若干的DataNode，NameNode是master。</span></span></span></span></span></span></span></span></p>
<p style="margin-left:0px;line-height:1.2;border-bottom-width:1px;border-bottom-style:solid;border-bottom-color:rgb(238,238,238);">
<span style="line-height:25.6px;"><span style="line-height:21px;"><span style="line-height:21px;"><span style="font-size:24px;"><span style="line-height:21px;"><span style="font-family:'Comic Sans MS';color:#663333;"><span style="color:rgb(51,51,51);font-size:16px;font-family:'Helvetica Neue', Helvetica, 'Segoe UI', Arial, freesans, sans-serif, 'Apple Color Emoji', 'Segoe UI Emoji', 'Segoe UI Symbol';line-height:25.6px;"><span style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;"><span style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;"><span style="font-size:24px;line-height:21px;background-color:rgb(250,247,239);"><span style="font-family:'Comic Sans MS';color:#663333;"><span style="color:rgb(51,51,51);font-size:16px;font-family:'Helvetica Neue', Helvetica, 'Segoe UI', Arial, freesans, sans-serif, 'Apple Color Emoji', 'Segoe UI Emoji', 'Segoe UI Symbol';line-height:25.6px;"><span style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;">NameNode</span></span></span></span><span style="background-color:rgb(250,247,239);">主要负责管理hdfs文件系统，</span><span style="font-size:24px;line-height:21px;background-color:rgb(250,247,239);"><span style="font-family:'Comic Sans MS';color:#663333;"><span style="color:rgb(51,51,51);font-size:16px;font-family:'Helvetica Neue', Helvetica, 'Segoe UI', Arial, freesans, sans-serif, 'Apple Color Emoji', 'Segoe UI Emoji', 'Segoe UI Symbol';line-height:25.6px;">掌握着整个HDFS的文件目录树及其目录与文件，这些信息会以文件的形式永久地存储在本地磁盘。</span></span></span><span style="background-color:rgb(250,247,239);">具体地包括</span><span style="background-color:rgb(255,204,153);">namespace管理（其实就是目录结构）</span><span style="background-color:rgb(250,247,239);">，</span></span><span style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(153,153,0);">block管理（其中包括
 filename-&gt;block，block-&gt;ddatanode list的对应关系）</span><span style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(250,247,239);">。<span style="font-size:24px;line-height:21px;background-color:rgb(250,247,239);"><span style="font-family:'Comic Sans MS';color:#663333;"><span style="color:rgb(51,51,51);font-size:16px;font-family:'Helvetica Neue', Helvetica, 'Segoe UI', Arial, freesans, sans-serif, 'Apple Color Emoji', 'Segoe UI Emoji', 'Segoe UI Symbol';line-height:25.6px;"><span style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;">NameNode</span></span></span></span>提供的是始终被动接收服务的server，主要有三类协议接口：ClientProtocol接口、DatanodeProtocol接口、NamenodeProtocol接口。</span></span></span></span></span></span></span></span></span></p>
<p style="margin-left:0px;line-height:1.2;border-bottom-width:1px;border-bottom-style:solid;border-bottom-color:rgb(238,238,238);">
<span style="line-height:25.6px;"><span style="line-height:21px;background-color:rgb(250,247,239);"><span style="line-height:21px;background-color:rgb(250,247,239);"><span style="font-size:24px;"><span style="line-height:21px;background-color:rgb(250,247,239);"><span style="font-family:'Comic Sans MS';color:#663333;"><span style="color:rgb(51,51,51);font-size:16px;font-family:'Helvetica Neue', Helvetica, 'Segoe UI', Arial, freesans, sans-serif, 'Apple Color Emoji', 'Segoe UI Emoji', 'Segoe UI Symbol';line-height:25.6px;"><span style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;"><span style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;"><span style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;">DataNode主要是用来存储数据文件，hdfs将一个文件分割成一个个的block，这些block可能存储在一个DataNode上或者是多个DataNode上。dn负责实际的底层的文件的读写，如果客户端client程序发起了读hdfs上的文件的命令，那么首先将这些文件分成block，然后<span style="font-size:24px;line-height:21px;background-color:rgb(250,247,239);"><span style="font-family:'Comic Sans MS';color:#663333;"><span style="color:rgb(51,51,51);font-size:16px;font-family:'Helvetica Neue', Helvetica, 'Segoe UI', Arial, freesans, sans-serif, 'Apple Color Emoji', 'Segoe UI Emoji', 'Segoe UI Symbol';line-height:25.6px;"><span style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;">DataNode</span></span></span></span>将告知client这些block数据是存储在哪些<span style="font-size:24px;line-height:21px;background-color:rgb(250,247,239);"><span style="font-family:'Comic Sans MS';color:#663333;"><span style="color:rgb(51,51,51);font-size:16px;font-family:'Helvetica Neue', Helvetica, 'Segoe UI', Arial, freesans, sans-serif, 'Apple Color Emoji', 'Segoe UI Emoji', 'Segoe UI Symbol';line-height:25.6px;"><span style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;">DataNode</span></span></span></span>上的，之后，client将直接和<span style="font-size:24px;line-height:21px;background-color:rgb(250,247,239);"><span style="font-family:'Comic Sans MS';color:#663333;"><span style="color:rgb(51,51,51);font-size:16px;font-family:'Helvetica Neue', Helvetica, 'Segoe UI', Arial, freesans, sans-serif, 'Apple Color Emoji', 'Segoe UI Emoji', 'Segoe UI Symbol';line-height:25.6px;"><span style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;">DataNode</span></span></span></span>交互。</span></span></span></span></span></span></span></span></span></span></p>
<p style="margin-left:0px;line-height:1.2;border-bottom-width:1px;border-bottom-style:solid;border-bottom-color:rgb(238,238,238);">
<span style="line-height:25.6px;"><span style="line-height:21px;background-color:rgb(250,247,239);"><span style="line-height:21px;background-color:rgb(250,247,239);"><span style="font-size:24px;"><strong><span style="line-height:21px;background-color:rgb(250,247,239);"><span style="font-family:'Comic Sans MS';color:#663333;">2、FSNamesystem</span></span></strong></span></span></span></span></p>
<p style="margin-left:0px;line-height:1.2;border-bottom-width:1px;border-bottom-style:solid;border-bottom-color:rgb(238,238,238);color:rgb(51,51,51);">
<span style="font-family:SimSun;"><span style="font-size:14px;"><span style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, 'Segoe UI', Arial, freesans, sans-serif, 'Apple Color Emoji', 'Segoe UI Emoji', 'Segoe UI Symbol';font-size:16px;line-height:25.6px;"><span style="font-size:24px;line-height:21px;background-color:rgb(250,247,239);"><span style="font-family:'Comic Sans MS';color:#663333;"><span style="color:rgb(57,57,57);font-family:verdana, 'ms song', Arial, Helvetica, sans-serif;font-size:14px;">NameNode的核心是FSNamesysem</span></span></span></span></span></span></p>
<p style="margin-left:0px;line-height:1.2;border-bottom-width:1px;border-bottom-style:solid;border-bottom-color:rgb(238,238,238);color:rgb(51,51,51);">
<span style="font-family:SimSun;"><span style="font-size:14px;"><span style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, 'Segoe UI', Arial, freesans, sans-serif, 'Apple Color Emoji', 'Segoe UI Emoji', 'Segoe UI Symbol';font-size:16px;line-height:25.6px;"><span style="color:rgb(57,57,57);font-family:verdana, 'ms song', Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(250,247,239);">FSNamesystem持有几大主要数据结构：FSDirectory维护系统目录结构、BlocksMap维护数据块信息、LeaseManagr维护租约信息；此外，还通过DatandeDescriptor、corruptReplicas等维护数据结点（DN）状态、坏副本等信息；</span></span></span></span></p>
<p style="margin-left:0px;line-height:1.2;border-bottom-width:1px;border-bottom-style:solid;border-bottom-color:rgb(238,238,238);color:rgb(51,51,51);">
<span style="font-size:14px;color:rgb(57,57,57);font-family:verdana, 'ms song', Arial, Helvetica, sans-serif;line-height:21px;background-color:rgb(250,247,239);">FSNamesystem</span><span style="font-family:'Hiragino Sans GB', Helvetica, Arial, sans-serif;font-size:14px;line-height:30px;background-color:rgb(250,247,239);">内部维护多个数据结构之间的关系：</span></p>
<p style="margin-left:0px;line-height:1.2;border-bottom-width:1px;border-bottom-style:solid;border-bottom-color:rgb(238,238,238);color:rgb(51,51,51);">
<span style="font-family:SimSun;"><span style="font-size:14px;"><span style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, 'Segoe UI', Arial, freesans, sans-serif, 'Apple Color Emoji', 'Segoe UI Emoji', 'Segoe UI Symbol';font-size:16px;line-height:25.6px;"></span></span></span></p>
<ol style="color:rgb(51,51,51);font-family:'Hiragino Sans GB', Helvetica, Arial, sans-serif;font-size:14px;line-height:30px;"><li>fsname-&gt;block列表的映射</li><li>所有有效blocks集合</li><li>block与其所属的datanodes之间的映射（该映射是通过block reports动态构建的，维护在namenode的内存中。每个datanode在启动时向namenode报告其自身node上的block）</li><li>每个datanode与其上的blocklist的映射</li><li>采用心跳检测根据LRU算法更新的机器（datanode）列表
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;">
<span style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;"><span style="color:rgb(57,57,57);font-family:verdana, 'ms song', Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(250,247,239);">FSNamesystem</span>体系结构</span></p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;">
<img src="http://pic002.cnblogs.com/images/2011/99916/2011090120561923.jpg" alt="" style="border:0px;"></p>
</li></ol><h3 style="font-family:'Hiragino Sans GB', Helvetica, Arial, sans-serif;line-height:40px;color:rgb(51,51,51);font-size:20px;">
2.1、FSDirectory</h3>
<span style="color:rgb(57,57,57);font-family:verdana, 'ms song', Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(250,247,239);"><span style="color:rgb(51,51,51);font-family:'Hiragino Sans GB', Helvetica, Arial, sans-serif;font-size:14px;line-height:30px;">FSDirectory用于维护当前系统中的文件树，<span style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;">存储整个文件系统的目录状态</span>。</span>FSDirectory通过FSImage及FSEditLog保存目录结构的某一时刻镜像及对镜像的修改（<span style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;">从namenode本地磁盘读取元数据信息和向本地磁盘写入元数据信息，并登记对目录结构所作的修改到日志文件</span>）；<span style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;">另外，FSDirectory保存了文件名和数据块的映射关系。</span></span>
<p></p>
<p style="margin-left:0px;line-height:1.2;border-bottom-width:1px;border-bottom-style:solid;border-bottom-color:rgb(238,238,238);color:rgb(51,51,51);">
<span style="font-family:SimSun;"><span style="font-size:14px;"><span style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, 'Segoe UI', Arial, freesans, sans-serif, 'Apple Color Emoji', 'Segoe UI Emoji', 'Segoe UI Symbol';font-size:16px;line-height:25.6px;"><span style="color:rgb(57,57,57);font-family:verdana, 'ms song', Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(250,247,239);"><span style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;">存储整个文件系统的目录状态</span><span style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, 'Segoe UI', Arial, freesans, sans-serif, 'Apple Color Emoji', 'Segoe UI Emoji', 'Segoe UI Symbol';font-size:16px;line-height:25.6px;">我们可以在$HADOOP_HOME/tmp/dfs/name/current下找到这些文件：fsimage以及edits。</span></span></span></span></span></p>
<p style="margin-left:0px;border-bottom-width:1px;border-bottom-style:solid;border-bottom-color:rgb(238,238,238);line-height:1.2;color:rgb(51,51,51);">
<span style="font-family:SimSun;"><span style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, 'Segoe UI', Arial, freesans, sans-serif, 'Apple Color Emoji', 'Segoe UI Emoji', 'Segoe UI Symbol';line-height:25.6px;"></span></span></p>
<p style="font-size:16px;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, 'Segoe UI', Arial, freesans, sans-serif, 'Apple Color Emoji', 'Segoe UI Emoji', 'Segoe UI Symbol';line-height:25.6px;">
<span>fsimage</span>：保存了最新的元数据检查点；<br><span>edits</span>：保存了HDFS中自最新的元数据检查点后的命名空间变化记录；</p>
<p style="font-size:16px;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, 'Segoe UI', Arial, freesans, sans-serif, 'Apple Color Emoji', 'Segoe UI Emoji', 'Segoe UI Symbol';line-height:25.6px;">
为了防止edits中保存的最新变更记录过大，HDFS会定期合并fsimage和edits文件形成新的fsimage文件，然后重新记录edits文件。由于NameNode存在单点问题（Hadoop2.0以前版本），因此为了减少NameNode的压力，HDFS把fsimage和edits的合并的工作放到SecondaryNameNode上，然后将合并后的文件返回给NameNode。但是，这也会造成一个新的问题，当NameNode宕机，那么NameNode中edits的记录就会丢失。也就是说，NameNode中的命名空间信息有可能发生丢失。</p>
<p style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, 'Segoe UI', Arial, freesans, sans-serif, 'Apple Color Emoji', 'Segoe UI Emoji', 'Segoe UI Symbol';line-height:25.6px;">
</p>
<h2 style="color:rgb(57,57,57);font-family:verdana, 'ms song', Arial, Helvetica, sans-serif;">
<span style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, 'Segoe UI', Arial, freesans, sans-serif, 'Apple Color Emoji', 'Segoe UI Emoji', 'Segoe UI Symbol';line-height:1.43;"><span style="font-size:24px;">2.2、FsImage</span></span></h2>
<div><span style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, 'Segoe UI', Arial, freesans, sans-serif, 'Apple Color Emoji', 'Segoe UI Emoji', 'Segoe UI Symbol';line-height:1.43;"><span style="font-size:24px;"><span style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><strong>在fsimage中，并没有记录每一个block对应到哪几个datanodes的对应表信息，而只是存储了所有的关于namespace的相关信息。</strong></span></span><br></span></span></div>
<p></p>
<div style="font-size:16px;"><span style="font-family:'Hiragino Sans GB', Helvetica, Arial, sans-serif;font-size:14px;line-height:30px;">FSImage用于持久化文件树的变更以及系统启动时加载持久化数据。 HDFS启动时通过FSImage来加载磁盘中原有的文件树，系统Standby之后，通过FSEditlog来保存在文件树上的修改，FSEditLog定期将保存的修改信息刷到FSImage中进行持久化存储。</span><br></div>
<div style="font-size:16px;"><span style="color:rgb(51,51,51);font-family:'Hiragino Sans GB', Helvetica, Arial, sans-serif;font-size:14px;line-height:30px;"><br></span></div>
<p style="font-size:16px;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, 'Segoe UI', Arial, freesans, sans-serif, 'Apple Color Emoji', 'Segoe UI Emoji', 'Segoe UI Symbol';line-height:25.6px;">
Fsimage是一个二进制文件，它记录了HDFS中所有文件和目录的元数据信息。关于fsimage的内部结构我们可以参看下图：<br><a href="https://github.com/leotse90/SparkNotes/blob/master/images/fsimage.jpg" rel="nofollow" style="color:rgb(64,120,192);text-decoration:none;background-color:transparent;"><img src="https://github.com/leotse90/SparkNotes/raw/master/images/fsimage.jpg" alt="fsimage" style="border:0px;"></a><br>
第一行是文件系统元数据，第二行是目录的元数据信息，第三行是文件的元数据信息。<span style="line-height:25.6px;">namespaceID</span><span style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, 'Segoe UI', Arial, freesans, sans-serif, 'Apple Color Emoji', 'Segoe UI Emoji', 'Segoe UI Symbol';font-size:16px;line-height:25.6px;">：当前命名空间的ID，在NameNode的生命周期内保持不变，DataNode注册时，返回该ID作为其registrationID，每次和NameNode通信时都要检查，不认识的namespaceID拒绝连接；</span><span style="line-height:25.6px;">path的length为0，即表示这个目录为根目录。</span></p>
<p style="font-size:16px;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, 'Segoe UI', Arial, freesans, sans-serif, 'Apple Color Emoji', 'Segoe UI Emoji', 'Segoe UI Symbol';line-height:25.6px;">
<span style="line-height:25.6px;">NameNode将这些信息读入内存之后，构造一个文件目录结构树，将表示文件或目录的节点填入到结构中。</span></p>
<p style="font-size:16px;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, 'Segoe UI', Arial, freesans, sans-serif, 'Apple Color Emoji', 'Segoe UI Emoji', 'Segoe UI Symbol';line-height:25.6px;">
<span style="line-height:25.6px;"><span style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;">在<span style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, 'Segoe UI', Arial, freesans, sans-serif, 'Apple Color Emoji', 'Segoe UI Emoji', 'Segoe UI Symbol';font-size:16px;line-height:25.6px;">NameNode</span>加载fsimage完成之后，BlocksMap中只有每个block到其所属的datanodes
 list的对应关系信息还没建立。然后通过dn的blockReport来收集构建。当所有的Data<span style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, 'Segoe UI', Arial, freesans, sans-serif, 'Apple Color Emoji', 'Segoe UI Emoji', 'Segoe UI Symbol';font-size:16px;line-height:25.6px;">Node</span>汇报给<span style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, 'Segoe UI', Arial, freesans, sans-serif, 'Apple Color Emoji', 'Segoe UI Emoji', 'Segoe UI Symbol';font-size:16px;line-height:25.6px;">NameNode</span>的blockReport处理完毕后，BlocksMap整个结构也就构建完成了。</span><br></span></p>
<p style="font-size:16px;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, 'Segoe UI', Arial, freesans, sans-serif, 'Apple Color Emoji', 'Segoe UI Emoji', 'Segoe UI Symbol';line-height:25.6px;">
<span style="line-height:25.6px;"><code style="font-family:Menlo, Monaco, Consolas, 'Courier New', monospace;font-size:12.6px;color:rgb(199,37,78);background-color:rgb(249,242,244);">GFS</code><span style="color:rgb(51,51,51);font-family:Arial, '宋体', sans-serif;font-size:14px;line-height:20px;"> 在 </span><code style="font-family:Menlo, Monaco, Consolas, 'Courier New', monospace;font-size:12.6px;color:rgb(199,37,78);background-color:rgb(249,242,244);">Master</code><span style="color:rgb(51,51,51);font-family:Arial, '宋体', sans-serif;font-size:14px;line-height:20px;"> 上存储的文件结构是采用命名空间的方式，没有目录内存储的文件列表结构，没有链接或别名机制，每一个命名空间的映射条目都是采用文件的绝对路径存储，并且采用前缀压缩技术</span><span class="text-muted" style="color:rgb(119,119,119);font-family:Arial, '宋体', sans-serif;font-size:14px;line-height:20px;">（Prefix
 compression）</span><span style="color:rgb(51,51,51);font-family:Arial, '宋体', sans-serif;font-size:14px;line-height:20px;">进行压缩存储。这样可以更高效地进行文件的定位和访问。</span><br></span></p>
<h2 style="font-size:21px;color:rgb(57,57,57);font-family:verdana, 'ms song', Arial, Helvetica, sans-serif;">
<span style="font-size:18pt;background-color:rgb(255,255,255);"><span><br></span></span></h2>
<h2 style="color:rgb(57,57,57);font-family:verdana, 'ms song', Arial, Helvetica, sans-serif;">
<span style="background-color:rgb(255,255,255);"><span style="font-size:24px;"><span>2.3、BlocksMap</span><a name="BlocksMap"></a></span></span></h2>
<div style="font-size:16px;"><span style="font-size:18pt;background-color:rgb(255,255,255);"></span>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;">
<strong><span style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">namenode中是通过block-&gt;datanode list的方式来维护一个block的副本是保存在哪几个datanodes上的对应关系的。</span><br></strong></p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;">
<strong>2.3.1、版本一</strong></p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;">
</p>
<div align="left" style="font-size:14px;line-height:21px;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;">
从以上fsimage中加载如namenode内存中的信息中可以很明显的看出，在fsimage中，并没有记录每一个block对应到哪几个datanodes的对应表信息，而只是存储了所有的关于namespace的相关信息。而真正每个block对应到datanodes列表的信息在hadoop中并没有进行持久化存储，而是在所有datanode启动时，每个datanode对本地磁盘进行扫描，将本datanode上保存的block信息汇报给namenode，namenode在接收到每个datanode的块信息汇报后，将接收到的块信息，以及其所在的datanode信息等保存在内存中。HDFS就是通过这种块信息汇报的方式来完成
 block -&gt; datanodes list的对应表构建。Datanode向namenode汇报块信息的过程叫做blockReport，而namenode将block -&gt; datanodes list的对应表信息保存在一个叫BlocksMap的数据结构中。</div>
<div style="color:rgb(57,57,57);font-family:verdana, 'ms song', Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;">
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;">
BlocksMap的内部数据结构如下：</div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;">
<img id="aimg_3546" src="http://www.aboutyun.com/data/attachment/forum/201404/15/022642ovenqnm6qwmpznvn.jpeg" class="zoom" width="600" alt=""> <br></div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;">
如上图显示，BlocksMap实际上就是一个Block对象对BlockInfo对象的一个Map表，其中Block对象中只记录了blockid，block大小以及时间戳信息，这些信息在fsimage中都有记录。而BlockInfo是从Block对象继承而来，因此除了Block对象中保存的信息外，<span style="background-color:rgb(204,204,204);">还包括代表该block所属的HDFS文件的INodeFile对象引用以及该block所属datanodes列表的信息</span>（即上图中的DN1，DN2，DN3，该数据结构会在下文详述）。</div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;">
<span style="font-weight:700;"><br></span></div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;">
<span style="font-weight:700;"> BlockInfo中datanode列表数据结构</span>
<div align="left">在BlockInfo中，将该block所属的datanodes列表保存在一个Object[]数组中，但该数组不仅仅保存了datanodes列表，还包含了额外的信息。实际上该数组保存了如下信息：</div>
<div align="left"><img id="aimg_3545" src="http://www.aboutyun.com/data/attachment/forum/201404/15/022642pvucsng6ufsvcd6c.jpeg" class="zoom" width="600" alt=""> <br></div>
<div align="left">上图表示一个block包含有三个副本，分别放置在DN1，DN2和DN3三个datanode上，每个datanode对应一个三元组，该三元组中的第二个元素，即上图中prev block所指的是该block在该datanode上的前一个BlockInfo引用。第三个元素，也就是上图中next Block所指的是该block在该datanode上的下一个BlockInfo引用。每个block有多少个副本，其对应的BlockInfo对象中就会有多少个这种三元组。</div>
<div align="left"><br></div>
<div align="left">Namenode采用这种结构来保存block-&gt;datanode list的目的在于节约namenode内存。由于namenode将block-&gt;datanodes的对应关系保存在了内存当中，随着HDFS中文件数的增加，block数也会相应的增加，namenode为了保存block-&gt;datanodes的信息已经耗费了相当多的内存，如果还像这种方式一样的保存datanode-&gt;block list的对应表，势必耗费更多的内存，而且在实际应用中，要查一个datanode上保存的block
 list的应用实际上非常的少，大部分情况下是要根据block来查datanode列表，所以namenode中通过上图的方式来保存block-&gt;datanode list的对应关系，当需要查询datanode-&gt;block list的对应关系时，只需要沿着该数据结构中next Block的指向关系，就能得出结果，而又无需保存datanode-&gt;block list在内存中。</div>
<div align="left"><span style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><br></span></div>
<div align="left"><span style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">因此在namenode启动并加载fsimage完成之后，实际上BlocksMap中的key，也就是Block对象都已经加载到BlocksMap中，每个key对应的value(BlockInfo)中，除了表示其所属的datanodes列表的数组为空外，其他信息也都已经成功加载。所以可以说：fsimage加载完毕后，BlocksMap中仅缺少每个块对应到其所属的datanodes
 list的对应关系信息。所缺这些信息，就是通过上文提到的从各datanode接收blockReport来构建。当所有的datanode汇报给namenode的blockReport处理完毕后，BlocksMap整个结构也就构建完成。</span><br></div>
</div>
</div>
<span style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;"></span>
<p></p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;">
<span style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;"><br></span></p>
<strong>2.3.2、版本二</strong><br><p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;">
block-&gt;datanode的信息没有持久化存储，而是namenode通过datanode的blockreport获取block-&gt;datanode list</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;">
BlocksMap负责维护了三种信息(<span style="color:rgb(51,51,51);font-family:'Hiragino Sans GB', Helvetica, Arial, sans-serif;font-size:14px;line-height:30px;">维护Block -&gt; { INode, datanodes, self ref } 的映射 )</span>：</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;">
　　block-&gt;datanode list</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;">
　　block-&gt;INodeFile</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;">
　　datanode-&gt;blocks</p>
</div>
<div style="font-size:16px;"><span style="font-size:18pt;background-color:rgb(255,255,255);"></span>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;">
这要归功于<span style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;">Object[] triplets</span>结构：</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;">
<img src="http://pic002.cnblogs.com/images/2011/99916/2011090121284088.jpg" alt="" style="border:0px;"></p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;">
</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;">
他是一个三元组，每个block有几个副本，就有几个三元组。</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;">
三元组的第一个元素表示该block所属的Datanode，类型是DatanodeDescriptor，通过它获得block-&gt;datanode list</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;">
第二/三个元素表示该block所在Datanode上的前/后一个block（前驱和后继），类型是BlockInfo，通过它获得datanode-&gt;blocks</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;">
借助这个三元组可以找到一个block所属的所有datanode，也可以通过三元组的后两个元素信息找到一个datanode上所有的block。</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;">
<span style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;"><br></span></p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;">
<strong><span style="color:rgb(51,51,51);font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;">2.3.3、版本三</span></strong></p>
</div>
<p style="font-size:14px;color:rgb(57,57,57);font-family:verdana, 'ms song', Arial, Helvetica, sans-serif;line-height:21px;">
<span style="background-color:rgb(255,255,255);"><img src="http://images.cnitblog.com/blog/542675/201308/12170402-facbe220a06040c597afe6cf56eb2729.png" alt="" width="759" height="329" style="border:0px;"></span></p>
<p style="font-size:14px;color:rgb(57,57,57);font-family:verdana, 'ms song', Arial, Helvetica, sans-serif;line-height:21px;">
<span style="background-color:rgb(255,255,255);">BlocksMap是NameNode保存block对象的容器。所有的Block对象被封装成BlockInfo对象，BlockInfo对象组织成HashMap进行存储；<br><br>
BlockInfo对象使用多个（与复本个数相同）三元组（triplets）保存每个block复本的位置信息；</span></p>
<p style="font-size:14px;color:rgb(57,57,57);font-family:verdana, 'ms song', Arial, Helvetica, sans-serif;line-height:21px;">
<span style="background-color:rgb(255,255,255);">三元组中第一个位元指向相应复本所在的DataNode；如图中虚线所示；第二个位元指向相同DataNode中前一个Block；第三个位元指向相同DataNode中后一个Block；如图中实现所示，从一个DatanodeDescriptor开始，通过一个双向链表，可以找到该DataNode上所有的Block；</span></p>
<p style="font-size:14px;color:rgb(57,57,57);font-family:verdana, 'ms song', Arial, Helvetica, sans-serif;line-height:21px;">
<span style="background-color:rgb(255,255,255);"></span></p>
<p style="color:rgb(51,51,51);font-family:'Hiragino Sans GB', Helvetica, Arial, sans-serif;font-size:14px;line-height:30px;">
BlocksMap结构比较简单，实际上就是一个Block到BlockInfo的映射。</p>
<h4 style="font-family:'Hiragino Sans GB', Helvetica, Arial, sans-serif;line-height:20px;color:rgb(51,51,51);font-size:17.5px;">
Block</h4>
<p style="color:rgb(51,51,51);font-family:'Hiragino Sans GB', Helvetica, Arial, sans-serif;font-size:14px;line-height:30px;">
Block是HDFS中的基本读写单元，主要包括：</p>
<ol style="color:rgb(51,51,51);font-family:'Hiragino Sans GB', Helvetica, Arial, sans-serif;font-size:14px;line-height:30px;"><li>blockId: 一个long类型的块id</li><li>numBytes: 块大小</li><li>generationStamp: 块更新的时间戳</li></ol><h4 style="font-family:'Hiragino Sans GB', Helvetica, Arial, sans-serif;line-height:20px;color:rgb(51,51,51);font-size:17.5px;">
BlockInfo</h4>
<p style="color:rgb(51,51,51);font-family:'Hiragino Sans GB', Helvetica, Arial, sans-serif;font-size:14px;line-height:30px;">
BlockInfo扩展自Block，除基本信息外还包括一个inode引用，表示该block所属的文件；以及一个神奇的三元组数组Object[] triplets，用来表示保存该block的datanode信息，假设系统中的备份数量为3。那么这个数组结构如下：</p>
<p style="color:rgb(51,51,51);font-family:'Hiragino Sans GB', Helvetica, Arial, sans-serif;font-size:14px;line-height:30px;">
<img src="http://jiangbo.me/images/hdfs/triplets.png" alt="triplets" style="vertical-align:middle;border:0px;"></p>
<ol style="color:rgb(51,51,51);font-family:'Hiragino Sans GB', Helvetica, Arial, sans-serif;font-size:14px;line-height:30px;"><li>DN1，DN2，DN3分别表示存有改block的三个datanode的引用(DataNodeDescriptor）</li><li>DN1-prev-blk表示在DN1上block列表中当前block的前置block引用</li><li>DN1-next-blk表示在DN1上block列表中当前block的后置block引用</li></ol><p style="color:rgb(51,51,51);font-family:'Hiragino Sans GB', Helvetica, Arial, sans-serif;font-size:14px;line-height:30px;">
DN2,DN3的prev-blk和next-blk类似。 HDFS采用这种结构存放block-&gt;datanode list的信息主要是为了节省内存空间，block-&gt;datanodelist之间的映射关系需要占用大量内存，如果同样还要将datanode-&gt;blockslist的信息保存在内存中，同样要占用大量内存。采用三元组这种方式能够从其中一个block获得到该block所属的datanode上的所有block列表。</p>
<p style="color:rgb(51,51,51);font-family:'Hiragino Sans GB', Helvetica, Arial, sans-serif;font-size:14px;line-height:30px;">
<span style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;"></span></p>
<h3 style="line-height:1.43;font-size:1.5em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, 'Segoe UI', Arial, freesans, sans-serif, 'Apple Color Emoji', 'Segoe UI Emoji', 'Segoe UI Symbol';">
Conclusion</h3>
<p></p>
<p style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, 'Segoe UI', Arial, freesans, sans-serif, 'Apple Color Emoji', 'Segoe UI Emoji', 'Segoe UI Symbol';font-size:16px;line-height:25.6px;">
通过fsimage与blocksmap两种数据结构，NameNode就能建立起完整的命名空间信息以及文件块映射信息。在NameNode加载fsimage之后，BlocksMap中只有每个block到其所属的DataNode列表的对应关系信息还没建立，这个需要通过DataNode的blockReport来收集构建，当所有的DataNode上报给NameNode的blockReport处理完毕后，BlocksMap整个结构也就构建完成。</p>
<br><p style="margin-left:0px;line-height:1.2;border-bottom-width:1px;border-bottom-style:solid;border-bottom-color:rgb(238,238,238);color:rgb(51,51,51);">
<span style="font-family:SimSun;"><span style="font-size:14px;">HDFS文件系统命名空间</span></span></p>
<p style="margin-left:0px;line-height:1.2;border-bottom-width:1px;border-bottom-style:solid;border-bottom-color:rgb(238,238,238);color:rgb(51,51,51);">
<span style="font-family:SimSun;"><span style="font-size:14px;">http://www.aboutyun.com/thread-7388-1-1.html<br></span></span></p>
<p style="margin-left:0px;line-height:1.2;border-bottom-width:1px;border-bottom-style:solid;border-bottom-color:rgb(238,238,238);color:rgb(51,51,51);">
<span style="font-family:SimSun;"><span style="line-height:1.2;"><span style="font-size:10px;">https://github.com/leotse90/SparkNotes/blob/master/HDFS%E6%96%87%E4%BB%B6%E7%B3%BB%E7%BB%9F%E5%91%BD%E5%90%8D%E7%A9%BA%E9%97%B4.md#hdfs文件系统命名空间</span></span></span></p>
<p style="margin-left:0px;line-height:1.2;border-bottom-width:1px;border-bottom-style:solid;border-bottom-color:rgb(238,238,238);color:rgb(51,51,51);">
<span style="font-size:10px;line-height:1.2;"><span style="font-family:SimSun;">http://blog.csdn.net/liuaigui/article/details/5993604</span></span></p>
<p style="margin-left:0px;line-height:1.2;border-bottom-width:1px;border-bottom-style:solid;border-bottom-color:rgb(238,238,238);color:rgb(51,51,51);">
<span style="font-size:12px;font-family:Simsun;line-height:1.2;">GFS uses a B-tree to map namespace to metadata; no directory nodes; 100 bytes per file</span></p>
<p style="margin-left:0px;line-height:1.2;border-bottom-width:1px;border-bottom-style:solid;border-bottom-color:rgb(238,238,238);color:rgb(51,51,51);">
<span style="font-size:12px;color:rgb(55,56,57);font-family:sans-serif;line-height:1.2;">哈希表</span><a class="headerlink" href="http://origin.redisbook.com/datatype/hash.html#hash-chapter" rel="nofollow" title="Permalink to this headline" style="font-size:12px;font-family:sans-serif;line-height:1.2;color:rgb(237,237,229);text-decoration:none;visibility:visible;">¶</a></p>
<p style="margin-left:0px;line-height:1.2;border-bottom-width:1px;border-bottom-style:solid;border-bottom-color:rgb(238,238,238);color:rgb(51,51,51);">
<span style="line-height:1.2;"><span style="font-family:SimSun;">http://origin.redisbook.com/datatype/hash.html<br></span></span></p>
            </div>
                </div>