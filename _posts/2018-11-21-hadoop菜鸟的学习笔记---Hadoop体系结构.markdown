---
layout:     post
title:      hadoop菜鸟的学习笔记---Hadoop体系结构
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>         语言是表达思想最有利的武器！</p>
<p><span></span>HDFS和MapReduce是Hadoop体系结构的核心，HDFS在集群上实现了分布式文件系统，MapReduce在集群上实现了分布式计算和处理任务。HDFS在MapReduce任务处理过程中提供了对文件操作和存储的支持,MapReduce在HDFS基础上实现了任务的分发、跟踪、执行等工作，并收集结果。</p>
<p><span></span>HDFS采用了主从Master/Slave结构模型，HDFS集群是由一个NameNode和若干个DataNode组成的，namenode作为主服务器，管理文件系统的命名空间和客户端对文件的访问操作；集群中的DataNode管理存储的数据，namenode也负责数据块到具体的datanode的映射，datanode也负责处理文件系统客户端的文件读写请求，并在namenode的统一调度下进行数据块的创建、删除和复制等工作。</p>
            </div>
                </div>