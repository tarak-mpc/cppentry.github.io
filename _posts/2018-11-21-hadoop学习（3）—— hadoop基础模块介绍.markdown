---
layout:     post
title:      hadoop学习（3）—— hadoop基础模块介绍
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/leifeng_001/article/details/70184546				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>一、分布式系统市建立在网络之上的软件系统，内聚性和透明性。 <br>
二、Hadoop四个模块 <br>
    Hadoop Common <br>
        为其他Hadoop模块提供基础设施 <br>
    Hadoop HDFS <br>
        一个高科好、高吞吐量的分布式文件系统 <br>
    Hadoop MapReduce <br>
        一个分布式的离线并行计算框架 <br>
    Hadoop YARN <br>
        一个新的MapReduce框架，任务调度与资源管理 <br>
三、 <br>
    NameNode是主节点，存储文件的元数据如文件名，文件目录结构，文件属性（生成时间，副本数，文件权限），以及每个文件的块列表和块所在的DataNode等。 <br>
    DataNode在本地文件系统存储文件块数据，以及文件块数据的校验和。 <br>
    Secondary NameNode用来监控HDFS状态的辅助后台程序，每隔一段时间获取HDFS元数据的快照。</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>