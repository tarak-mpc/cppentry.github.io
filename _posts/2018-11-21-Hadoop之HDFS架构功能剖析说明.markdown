---
layout:     post
title:      Hadoop之HDFS架构功能剖析说明
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h3>HDFS系统架构图层面解析</h3>
<p>Hadoop Distributed File System （HDFS）：<strong>分布式文件系统</strong></p>
<p><strong>*分布式应用分为主从架构：主节点NameNode（一个）从节点：DateNode（多个）</strong></p>
<p><strong>＊HDFS服务组件：namenode，datanode，SecondaryNamenode</strong></p>
<p><strong>＊Hdfs存储形式：存储在HDFS上的文件是以块（block）的形式存储的，Hadoop2.x的版本中默认块的大小是128M。</strong></p>
<p><strong>HDFS服务功能</strong></p>
<p><strong>＊Namenode：主节点，存储文件的元数据（文件名，文件目录结构，文件属性（生成时间，副本数，文件权限等））</strong></p>
<p>，以及每个文件的块列表和块所在的DataNode等。</p>
<p>＊DataNode：在本地文件系统存储文件数据，以及块数据的校验和（长度、创建时间、CRC32校验和）。</p>
<p><strong>＊Secondary NameNode：</strong>用来监控HDFS状态的辅助后台程序，每隔一段时间获取HDFS元数据的快照。定时的将NameNode的镜像文件和编辑日志文件合并为一个文件，叫做新镜像文件。</p>
<p><br></p>
<p>＊＊＊＊＊＊＊＊＊＊＊＊＊＊＊＊＊＊＊＊＊＊＊＊＊＊＊＊＊＊＊＊＊＊＊＊＊＊＊＊＊＊＊＊＊＊＊＊＊＊＊＊＊＊＊＊＊＊＊＊＊</p>
<p></p>
<p>HDFS中有两个基本组成</p>
<p>是由‘一台Namenode服务器和多台DataNode服务器组成’</p>
<p>A） NameNode：名称节点，用来存储元数据（帮助你快速找到数据块，就是数据块的映射）</p>
<p>HDFS存储：（块操作）是把一个文件从逻辑上分为块，么一个块默认的存储是128M，把这些块依次的存到不同的DataNode服务器上，每个DataNode上存了哪些块是记录在NameNode上的，读取的时候也是首先要去访问NameNode，（NameNode代表每一个几群的访问入口）通过NameNode去找到对应的块文件，NameNode其实就是存储的对应关系元数据。</p>
<p> </p>
<p>B）  Datanode：数据节点，用来存储真实的数据。</p>
<p>DataNode其实是用来存储数据块的</p>
<p>***注意：当我们吧很多台服务器组成一个HDFS集群以后，我们会去启动一个角色是NameNode和DataNode进程，作为NameNode服务器要去启动对应的的角色</p>
<p>           当某个DataNode服务器启动失败了，他不影响里面的数据访问，比如所有的DataNode服务器内存全部满了，我们可以直接加服务器进去，可以随意添加，互不影响，这就是分布式文件存储系统</p>
<p> </p>
<p>在集群中我们有很多台服务器，</p>
<p>           有一台服务器启动的是namenode</p>
<p>           其他服务器启动的是DataNode</p>
<br><p><strong><br></strong></p>
<p><strong><br></strong></p>
<p><strong><img src="https://img-blog.csdn.net/20170505092015981?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveXV5dXl1eGlhb2xlaQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></strong></p>
<p><strong><br></strong></p>
<p><strong><br></strong></p>
<p><strong><br></strong></p>
<p><strong><br></strong></p>
<p><strong>yarn服务的功能</strong></p>
<p><strong></strong></p>
<p><strong>ResourceManager：</strong>处理客户端请求</p>
<p>                      启动/监控ApplicationMaster</p>
<p>                      监控NodeManager</p>
<p>                      资源分配与调度</p>
<p> <strong>ApplicationMaster：</strong>数据切分</p>
<p>                      为应用程序申请资源，并分配给内部任务</p>
<p>                      任务监控与容错</p>
<p><strong>NodeManager：</strong>单个节点上的资源管理</p>
<p>                      处理来自ResourceManager的命令</p>
<p>                      处理来自ApplicationMaster的命令</p>
<p> <strong>Container：</strong>对多任务运行环境的抽象，封装了CPU、内存等多维资源以及环境变量、启动命 令等任务运行相关信息</p>
<br><p><strong><br></strong></p>
<p><strong>MapReduce 如何运行在YARN上<br></strong></p>
<p><strong><br></strong></p>
<p><strong><br></strong></p>
<p><strong></strong></p>
<p>1. 客户端向ResourceManager提交任务。</p>
<p>2.<strong> </strong>ResourceManager向NodeManager分配一个ApplicationsManager，并找到一个容器生 成一个MR App Mstr。</p>
<p>3.<strong> </strong>该应用的管理者向ResourceManager申请资源。</p>
<p>4. 资源申请完毕后，找到NodeManager将容器中的MR App Mstr启动起来。</p>
<p>5. Map任务和Reduce任务启动。</p>
<p>6. Map与Reduce程序运行过程中都要向MR App Mstr提交信息。</p>
<p>7. 当程序运行结束时，应用管理者向ResourceManager提交信息。</p>
<br><p><strong><br></strong></p>
<p><strong><br></strong></p>
<p><strong><br></strong></p>
            </div>
                </div>