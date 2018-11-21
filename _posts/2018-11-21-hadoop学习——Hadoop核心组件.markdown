---
layout:     post
title:      hadoop学习——Hadoop核心组件
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h1 class="csdn_top" style="font-size:24px;font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;line-height:38px;color:rgb(44,48,51);">
hadoop学习——Hadoop核心组件</h1>
<div><br></div>
<div>
<p style="text-indent:2em;line-height:25.2px;color:rgb(68,68,68);font-size:14px;overflow:hidden;font-family:'Microsoft Yahei', '宋体', '微软雅黑', Arial, 'Lucida Grande', Tahoma, sans-serif;">
   下图展示了hadoop生态系统的核心组件。</p>
<p style="text-indent:2em;line-height:25.2px;color:rgb(68,68,68);font-size:14px;overflow:hidden;font-family:'Microsoft Yahei', '宋体', '微软雅黑', Arial, 'Lucida Grande', Tahoma, sans-serif;">
                               <img src="http://www.hehongbo.com/wp-content/uploads/2014/09/20140915221927_14553.png" alt="" style="border:0px;vertical-align:middle;text-align:center;"></p>
<p style="text-indent:2em;line-height:25.2px;color:rgb(68,68,68);font-size:14px;overflow:hidden;font-family:'Microsoft Yahei', '宋体', '微软雅黑', Arial, 'Lucida Grande', Tahoma, sans-serif;">
    下面来简单介绍各个组件的作用：</p>
<p style="text-indent:2em;line-height:25.2px;color:rgb(68,68,68);font-size:14px;overflow:hidden;font-family:'Microsoft Yahei', '宋体', '微软雅黑', Arial, 'Lucida Grande', Tahoma, sans-serif;">
 HDFS(Hadoop distribute file system)——Hadoop生态系统的基础组件Hadoop分布式文件系统。它是其他一些工具的基础<span style="line-height:1.5;">HDFS的机制是将大量数据分布到计算机集群上，数据一次写入，但可以多次读取用于分析。HDFS让Hadoop可以最大化利用磁盘。</span></p>
<p style="text-indent:2em;line-height:25.2px;color:rgb(68,68,68);font-size:14px;overflow:hidden;font-family:'Microsoft Yahei', '宋体', '微软雅黑', Arial, 'Lucida Grande', Tahoma, sans-serif;">
<span style="line-height:1.5;"> HBase—— 一个构建在HDFS之上的面向列的NoSql数据库，HBase用于对打量数据进行快速读取/写入。HBase将Zookeeper用于自身的管理，以保证其所有组件都正在运行。HBase使得Hadoop可以最大化利用内存。</span></p>
<p style="text-indent:2em;line-height:25.2px;color:rgb(68,68,68);font-size:14px;overflow:hidden;font-family:'Microsoft Yahei', '宋体', '微软雅黑', Arial, 'Lucida Grande', Tahoma, sans-serif;">
<span style="line-height:1.5;"> MapReduce——MapReduce是Hadoop的主要执行框架，它是一个用于分布式并行数据处理的编程模型，将作业分为mapping阶段和reduce阶段。开发人员谓Hadoop编写MapReduce作业，并使用HDFS中存储的数据，而HDFS可以保证快速的数据访问。鉴于MapReduce作业的特性，Hadoop以并行的方式将处理过程移向数据。MapReduce使得Hadoop可以最大化利用CPU。</span></p>
<p style="text-indent:2em;line-height:25.2px;color:rgb(68,68,68);font-size:14px;overflow:hidden;font-family:'Microsoft Yahei', '宋体', '微软雅黑', Arial, 'Lucida Grande', Tahoma, sans-serif;">
<span style="line-height:1.5;"> Zookeeper——Zookeeper是Hadoop的分布式协调服务。Zookeeper被设计成可以在机器集群上运行，是一个具有高度可用性的服务，用于Hadoop操作的管理，而且很多Hadoop组件都依赖它。<br></span></p>
<p style="text-indent:2em;line-height:25.2px;color:rgb(68,68,68);font-size:14px;overflow:hidden;font-family:'Microsoft Yahei', '宋体', '微软雅黑', Arial, 'Lucida Grande', Tahoma, sans-serif;">
<span style="line-height:1.5;"> Oozie—— Oozie是一个北极测很难过到Hadoop软件栈中的可扩展的Workflow系统。用于协调多个MapReduce作业的执行。它能够处理大量的复杂性，基于外部事件来管理执行。<br></span></p>
<p style="text-indent:2em;line-height:25.2px;color:rgb(68,68,68);font-size:14px;overflow:hidden;font-family:'Microsoft Yahei', '宋体', '微软雅黑', Arial, 'Lucida Grande', Tahoma, sans-serif;">
 Pig——Pig是对MapReduce编程复杂性的抽象，Pig平台包含用于分析Hadoop数据集的执行环境和脚本语言(Pig Latin)。它的编译器将Pig Latin翻译为MapReduce程序序列。</p>
<p style="text-indent:2em;line-height:25.2px;color:rgb(68,68,68);font-size:14px;overflow:hidden;font-family:'Microsoft Yahei', '宋体', '微软雅黑', Arial, 'Lucida Grande', Tahoma, sans-serif;">
 Hive——类似于SQL的高级语言，用于执行对存储在Hadoop中数据的查询，Hive允许不熟悉MapReduce的开发人员编写数据查询语句，它会将翻译为Hadoop中的MapReduce作业。类似于Pig。Hive是一个抽象层，适合于较熟悉SQL而不是java编程的数据库分析师。</p>
<p style="text-indent:2em;line-height:25.2px;color:rgb(68,68,68);font-size:14px;overflow:hidden;font-family:'Microsoft Yahei', '宋体', '微软雅黑', Arial, 'Lucida Grande', Tahoma, sans-serif;">
Hadoop生态系统中还包含一些用于与其他企业级应用进行集成的框架,例如上图所示的Sqoop和Flume：</p>
<p style="text-indent:2em;line-height:25.2px;color:rgb(68,68,68);font-size:14px;overflow:hidden;font-family:'Microsoft Yahei', '宋体', '微软雅黑', Arial, 'Lucida Grande', Tahoma, sans-serif;">
 Sqoop是一个连通性工具，用于在关系型数据库和数据仓库Hadoop之间移动数据。Sqoop利用数据库来描述导入/导出数据的模式，并使用MapReduce实现并行操作和容错。</p>
<p style="text-indent:2em;line-height:25.2px;color:rgb(68,68,68);font-size:14px;overflow:hidden;font-family:'Microsoft Yahei', '宋体', '微软雅黑', Arial, 'Lucida Grande', Tahoma, sans-serif;">
 Fulme是一个分布式的、具有可靠性和高可用性的服务，用于从单独的机器上将大量数据高效的收集、聚合并移动到HDFS中。它给予一个简单灵活的架构，童工流式数据操所。它借助于简单可扩展的数据模型，允许将来自企业中多台机器上的数据移到Hadoop中。</p>
<br></div>
            </div>
                </div>