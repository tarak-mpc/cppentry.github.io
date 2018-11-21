---
layout:     post
title:      大数据Spark企业级实战版【学习笔记】---Spark简介
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/henni_719/article/details/77048510				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h1 align="center">第 1 章 Spark编程模式</h1>
<p>       Hadoop和Spark联合构成了当今的大数据世界！<strong>Hadoop负责数据存储和资源管理，Spark负责一体化、多元化的不同规模的数据计算</strong>，而计算正是大数据的精髓之所在！</p>
<h2>1.1 Spark简介</h2>
<p>       基于RDD，Spark成功构建了一体化、多元化的大数据处理体系，而且Spark成功使用Spark SQL、Spark Streaming、MLlib、GraphX近乎完美地解决了大数据中BatchProcessing、StreamingProcessing、Ad-hoc Query等三大核心问题！在Spark中，Spark SQL、Spark Streaming、MLlib、Graphx四大子框架和库之间可以无缝地共享数据！ Spark技术堆栈如图1-1：</p>
<p>       <img src="https://img-blog.csdn.net/20170810133833444?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaGVubmlfNzE5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p>       Apache Spark官方的定义为<strong>：Spark是一个通用的大规模数据快速处理引擎。可以简单理解为Spark就是一个大数据分布式处理框架</strong>。</p>
<p>       Spark提供的基于RDD的一体化解决方案，将MapReduce、Streaming、SQL、Machine Learning、Graph Processing等模型统一到一个平台下，并以一致的API公开且提供相同的部署方案，使得Spark的工程应用领域变得更加广泛！</p>
<h3>1.1.1那为什么需要使用Spark呢？</h3>
<p>       由于Hadoop的<strong>MapReduce具有高延迟的致命弱点</strong>，导致无力处理很多对时间有要求的场景，而且Hadoop无力改变现状而导致其正在加速死亡！</p>
<p>       其实，Hadoop社区一直在改进Hadoop本身，但事实却是无力回天，主要有以下两个原因：</p>
<p><span></span>(1)    Hadoop的改进基本停留在代码层次。</p>
<p><span></span>(2)    Hadoop本身的计算模型决定了Hadoop上所有的工作都要转化成Map、Shuffle和Reduce等核心阶段，<strong>由于每次计算都要从磁盘读或者写数据，同时整个计算模型需要网络传输</strong>，这就导致了越来越不能忍受的延迟性，<strong>同时在前一个任务运行完之前，任何一个任务都不可以运行，这直接导致了其无力支持交互式应用</strong>。</p>
<p></p>
<p>从个人学习的角度，Spark具有以下特征也成为必须掌握Spark的原因：</p>
<p><strong>Ø  快速</strong></p>
<p>    基于Spark内存的计算速度比Hadoop MapReduce快100倍，基于磁盘的计算速度也要快10倍以上。原因是Spark有一个DirectedAcyclic Graph(DAG有向无循环图)执行引擎，支持循环数据流和内存计算。</p>
<p><strong>Ø  易用</strong></p>
<p>        提供多语言(Scala、Java、Python)API，能够快速实现应用，相比MapReduce有更简洁的代码，安装部署也无需复杂的配置。使用API可以轻松地构建分布式应用程序，当然也可以使用Scala和Python脚本进行交互式编程。</p>
<p><strong>Ø  通用</strong></p>
<p>        提供了一个强大的技术栈，包括查询语言Spark SQL、机器学习工具MLlib、图计算工具GraphX、实时流处理工具Spark Streaming，在同一个应用程序中可以无缝地连接使用这些框架！</p>
<p><strong>Ø  集成Hadoop</strong></p>
<p>        Spark可以运行在第二代Hadoop集群管理Yarn上，这使得Spark可以读取Hadoop的任何数据，如果已经有第二代Hadoop集群，那么无须安装既可以直接运行Spark，Spark很容易运行在standalone或者EC2H或者Mesos上。它能读取HDFS、HBase、Cassandra以及任何Hadoop数据源。</p>
<p><strong>Ø  活跃的社区</strong></p>
<p>              Spark起源于2009年，目前已有超过50个机构和250个工程师贡献过代       码，和2013年6月相比，代码行数几乎扩大3倍，Spark依然处于活跃的开     发过程中，代码变动频繁！</p>
<br>            </div>
                </div>