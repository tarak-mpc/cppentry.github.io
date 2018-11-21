---
layout:     post
title:      Spark入门到精通视频学习资料--第三章：Spark进阶（2讲）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/dongzhumao86/article/details/43984275				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>掌握Spark内核是精通Spark的关键，也是驾驭Spark的精髓所在。</p>
<p>基于Spark内核，Spark构建起了一体化多元化的大数据处理流水线，在一个技术堆栈中即可以同时完成批处理、实时流处理、交互式查询、机器学习、图计算以及这些子框架之间数据和RDD算子的无缝共享与互操作。</p>
<p>可以说，Spark内核是每个想彻底掌握Spark的人员的必修课，通过对内核的探索，我们对整个Spark的运行机制会了如指掌，这对Spark的大规模应用、性能优化、系统自定义开发Spark系统都是至关重要的。</p>
<p><br></p>
<p></p>
<p><strong>一、Spark内核核心术语解析</strong></p>
<p>Application：</p>
<p>Application是创建了SparkContext实例对象的Spark用户，包含了Driver程序</p>
Spark-shell是一个应用程序，因为spark-shell在启动的时候创建了SparkContext对象，其名称为sc<br><p></p>
<p>Job：</p>
<p>和Spark的action相对应，每一个action例如count、savaAsTextFile等都会对应一个Job实例，该Job实例包含多任务的并行计算。</p>
<p>Driver Program：</p>
<p>运行main函数并且新建SparkContext实例的程序。</p>
<p>Cluster Manager：</p>
<p>集群资源管理的外部服务，在Spark上现在主要有Standalone、Yarn、Mesos等三种集群资源管理器，Spark自带的Standalone模式能够满足绝大部分纯粹的Spark计算环境中对集群资源管理的需求，基本上只有在集群中运行多套计算框架的时候才建议考虑Yarn和Mesos。</p>
<p>Worker Node：</p>
<p>集群中可以运行应用程序代码的工作节点，相当于Hadoop的slave节点。</p>
<p>Executor：</p>
<p>在一个Worker Node上为应用启动的工作进程，在进程中负责任务的运行，并且负责将数据存放在内存或磁盘上，必须注意的是，每个应用在一个Worker Node上只会有一个Executor，在Executor内部通过多线程的方式并发处理应用的任务。</p>
<p>Task：</p>
<p>被Driver送到executor上的工作单元，通常情况下一个task会处理一个split的数据，每个split一般就是一个Block块的大小</p>
<p></p>
<p>Stage：</p>
<p>一个Job会被拆分成很多任务，每一组任务被成为Stage，这个MapReduce的map和reduce任务很像，划分Stage的依据在于：Stage开始一般是由于读取外部数据或者Shuffle数据、一个Stage的结束一般是由于发生Shuffle（例如reduceByKey操作）或者整个Job结束时例如要把数据放到hdfs等存储系统上<br></p>
<br>
详细讲解请参考以下视频：
<p><del>深入spark内核（上）.mp4       http://pan.baidu.com/s/1bnB62MN</del></p>
<p><del>深入spark内核（上）.mp4       http://pan.baidu.com/s/1ntr9qsh</del><br></p>
<p><br></p>
<p>另附上讲解PPT：</p>
<p>深入spark内核.pdf       http://pan.baidu.com/s/1mgHZGhU<br></p>
<p><br></p>
<p></p>
<p style="font-family:Arial;font-size:14px;line-height:26px;">
<span style="background-color:rgb(255,255,204);">==========================================================</span></p>
<p style="font-family:Arial;font-size:14px;line-height:26px;">
<span style="background-color:rgb(255,255,204);">申明：视频资料已过期，建议不要再下载了。</span></p>
<p style="font-family:Arial;font-size:14px;line-height:26px;">
<span style="background-color:rgb(255,255,204);">==========================================================</span></p>
<br>            </div>
                </div>