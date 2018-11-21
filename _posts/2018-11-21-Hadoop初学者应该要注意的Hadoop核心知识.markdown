---
layout:     post
title:      Hadoop初学者应该要注意的Hadoop核心知识
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p style="color:rgb(51,51,51);font-family:'microsoft yahei', arial;font-size:16px;line-height:27.2px;text-align:justify;">
上篇（给Hadoop初学者的一些建议）我们介绍了新手学习hadoop的入门注意事项。这篇来谈谈hadoop核心知识学习。 <br>
hadoop核心知识学习: <br>
hadoop分为hadoop1.X和hadoop2.X，并且还有hadoop生态系统。这里只能慢慢介绍了。一口也吃不成胖子。</p>
<p style="color:rgb(51,51,51);font-family:'microsoft yahei', arial;font-size:16px;line-height:27.2px;text-align:justify;">
那么下面我们以hadoop2.x为例进行详细介绍：</p>
<p style="color:rgb(51,51,51);font-family:'microsoft yahei', arial;font-size:16px;line-height:27.2px;text-align:justify;">
Hadoop的核心是mapreduce和hdfs。 <br>
Mapreduce：mapreduce是很多人都需要迈过去的槛，它比较难以理解，我们有时候即使写出了mapreduce程序，但是还是摸不着头脑。我们都知道mapreduce是一种编程模型，那么它能干什么，对我有什么用。它的原理是什么，为什么我们编写了map函数，reduce函数就可以在多台机器上运行，这些问题或许都给初学者带来了困扰。</p>
<p style="color:rgb(51,51,51);font-family:'microsoft yahei', arial;font-size:16px;line-height:27.2px;text-align:justify;">
那么我们就要了解：</p>
<ul style="color:rgb(51,51,51);font-family:'microsoft yahei', arial;font-size:16px;line-height:27.2px;text-align:justify;"><li>什么是mapreduce？</li><li>Mapreduce的工作原理是什么？</li><li>Mapreduce的工作流程是什么？</li><li>Mapreduce的编程模型是什么？</li><li>shuffle是什么？</li><li>partition是什么？</li><li>combiner是什麽？</li><li>他们三者之间的关系是什么？</li><li>map的个数由谁来决定，如何计算？</li><li>reduce个数由谁来决定，如何计算？</li></ul><p style="color:rgb(51,51,51);font-family:'microsoft yahei', arial;font-size:16px;line-height:27.2px;text-align:justify;">
mapreduce熟悉了，还有一些问题困扰着初学者，虽然有了Java基础，但是我们需要搭建开发环境，该如何搭建开发环境？</p>
<p style="color:rgb(51,51,51);font-family:'microsoft yahei', arial;font-size:16px;line-height:27.2px;text-align:justify;">
那么就需要我们学习Windows上如何使用Eclipse远程连接Hadoop并进行程序开发</p>
<p style="color:rgb(51,51,51);font-family:'microsoft yahei', arial;font-size:16px;line-height:27.2px;text-align:justify;">
因为在操作mapredcue过程中伴随着操作hdfs，就像我们传统开发，编程是离不开数据库一样。hdfs可以理解为传统编程的数据库，但是其实他不是，真正的数据库是hadoop data base，也就是hbase。好了下面，我们开始讲如何学习hdfs：</p>
<p style="color:rgb(51,51,51);font-family:'microsoft yahei', arial;font-size:16px;line-height:27.2px;text-align:justify;">
HDFS：我们至少应该学习以下内容</p>
<ul style="color:rgb(51,51,51);font-family:'microsoft yahei', arial;font-size:16px;line-height:27.2px;text-align:justify;"><li>什么是HDFS及HDFS架构设计？</li><li>HDFS体系结构简介及优缺点？</li><li>Hdfs如何存储数据？</li><li>Hdfs如何读取数据？</li><li>Hdfs如何写入文件？</li><li>Hdfs的副本存放策略？</li><li>如何访问hdfs？</li><li>Hdfs数据如何复制？</li><li>Namenode的热备？</li></ul><p style="color:rgb(51,51,51);font-family:'microsoft yahei', arial;font-size:16px;line-height:27.2px;text-align:justify;">
hadoop生态系统内容还是比较多的，但是最常用的是hive，hbase。 <br>
Hive是初学者进入大数据（hadoop）行业最好的选择入口，因为它提供了简单的类sql语句，使得不懂得mapreduce程序编写的学员也能够很轻松的进入大数据行业。所以建议大家（尤其是零基础的学员）在学习hadoop的过程中，可以着重加强对hive的学习，尤其是hive语句的熟练操作。当然对于有数据库基础的学员学习hive就更容易一些。</p>
<p style="color:rgb(51,51,51);font-family:'microsoft yahei', arial;font-size:16px;line-height:27.2px;text-align:justify;">
Hbase是一种nosql数据库，只有当数据量非常大时，比如TB、PB级，hbase才能发挥出很好的效果，所以对于致力于加入大公司的学员，可以深入学习hbase，尤其是hbase表的设计，rowkey的设计，hbase性能的调优，hbase和hive、impala的结合等。</p>
<p style="color:rgb(51,51,51);font-family:'microsoft yahei', arial;font-size:16px;line-height:27.2px;text-align:justify;">
Yarn是分布式集群资源管理框架，也是hadoop2.x和hadoop1.x明显不同的地方，所以我们还是有必要对yarn的原理、框架、组成部分进行详细的了解的。</p>
<p style="color:rgb(51,51,51);font-family:'microsoft yahei', arial;font-size:16px;line-height:27.2px;text-align:justify;">
对于hadoop其他的组件：比如海量日志收集工具flume，数据导入导出工具sqoop，应用程序协调服务zookeeper，学员可以结合实战项目学习其原理，如何使用即可。</p>
<p style="color:rgb(51,51,51);font-family:'microsoft yahei', arial;font-size:16px;line-height:27.2px;text-align:justify;">
对于想从事数据挖掘的学员，可以深入学习mahout、机器学习、算法等相关知识，根据学员自己的职业选择和兴趣爱好自主选择，建议零基础的学员最好是先从hive入手。</p>
<p style="color:rgb(51,51,51);font-family:'microsoft yahei', arial;font-size:16px;line-height:27.2px;text-align:justify;">
Storm是一种基于流的计算框架，spark是基于内存的计算框架，它们是不同于mapreduce的计算框架，但作用都是对数据的处理和分析，建议初学者在学习好mapreduce的前提下，可以对storm和spark进行深入的学习，切记贪多嚼不烂。通而不精。</p>
<p style="color:rgb(51,51,51);font-family:'microsoft yahei', arial;font-size:16px;line-height:27.2px;text-align:justify;">
如果想更加深入的学习，丰富自己的知识，可以选择性的学习一下shell、python脚本语言，Redis、MongoDB等nosql数据库，如果想做hadoop运维的，也可以学习ganglia和nagios等监控工具。</p>
<p style="color:rgb(51,51,51);font-family:'microsoft yahei', arial;font-size:16px;line-height:27.2px;text-align:justify;">
最后建议大家在学习过程中一定要由浅入深、从简单到复杂、理论和实践相结合，由于hadoop生态系统的工具比较多，每个工具有不同的侧重点，所以再次提醒大家切记贪多、切记浮躁、只有基础扎实了，后续的学习才会更轻松、更快速、更高效。</p>
            </div>
                </div>