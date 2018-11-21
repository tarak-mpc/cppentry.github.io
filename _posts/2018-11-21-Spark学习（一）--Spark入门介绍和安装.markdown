---
layout:     post
title:      Spark学习（一）--Spark入门介绍和安装
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>本次主要介绍spark的入门概念和安装</p>
<ul>
<li>Spark概念</li>
<li>Spark安装</li>
<li>Spark HA 高可用部署</li>
</ul>
<hr>
<h2><a id="1_Spark_5"></a>1. Spark概念</h2>
<h4><a id="11_Spark_6"></a>1.1 什么是Spark</h4>
<p>Spark 是一种快速、 通用、 可扩展的大数据分析引擎， 2009 年诞生于加州大学伯克利分校 AMPLab， 2010 年开源， 2013 年 6 月成为 Apache 孵化项目， 2014年 2 月成为 Apache 顶级项目。 目前， Spark 生态系统已经发展成为一个包含多个子项目的集合， 其中包含 SparkSQL、 Spark Streaming、 GraphX、 MLlib 等子项目，Spark 是基于内存计算的大数据并行计算框架。</p>
<p>Spark 基于内存计算， 提高了在大数据环境下数据处理的实时性， 同时保证了高容错性和高可伸缩性， 允许用户将Spark 部署在大量廉价硬件之上， 形成集群。</p>
<h4><a id="12_Spark_11"></a>1.2 Spark的优势</h4>
<p>Spark 是一个开源的类似于 Hadoop MapReduce 的通用的并行计算框架，Spark基于 map reduce 算法实现的分布式计算， 拥有 Hadoop MapReduce 所具有的优点； 但不同于 MapReduce 的是 Spark 中的 Job 中间输出和结果可以保存在内存中，从而不再需要读写 HDFS， 因此 Spark 能更好地适用于数据挖掘与机器学习等需要迭代的 map-reduce 的算法。<br>
<strong>Spark 是 MapReduce 的替代方案， 而且兼容 HDFS、 Hive， 可融入 Hadoop 的生态系统， 以弥补 MapReduce 的不足。</strong></p>
<h4><a id="13_Spark_15"></a>1.3 Spark的特点</h4>
<ul>
<li>
<p><strong>快</strong><br>
与 Hadoop 的 MapReduce 相比， Spark 基于内存的运算要比MR快 100 倍以上， Spark基于硬盘的运算也要比MR快 10 倍以上。 Spark 实现了高效的 DAG 执行引擎， 可以通过基于内存来高效处理数据流。<br>
<img src="https://img-blog.csdn.net/20181018124334409?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjIyOTA1Ng==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="在这里插入图片描述"></p>
</li>
<li>
<p><strong>易用</strong><br>
Spark 支持 Java、 Python 和 Scala 的 API， 还支持超过 80 种高级算法， 使用户可以快速构建不同的应用。 而且 Spark 支持交互式的 Python 和 Scala 的 shell， 可以非常方便地在这些 shell 中使用 Spark 集群来验证解决问题的方法。</p>
</li>
<li>
<p><strong>通用</strong><br>
Spark 提供了统一的解决方案。 Spark 可以用于批处理、 交互式查询（Spark SQL） 、 实时流处理（Spark Streaming） 、 机器学习（Spark MLlib） 和图计算（GraphX） 。 这些不同类型的处理都可以在同一个应用中无缝使用。 Spark统一的解决方案非常具有吸引力， 毕竟任何公司都想用统一的平台去处理遇到的问题， 减少开发和维护的人力成本和部署平台的物力成本。</p>
</li>
<li>
<p><strong>兼容性好</strong><br>
Spark 可以非常方便地与其他的开源产品进行融合。 比如， Spark 可以使用<br>
Hadoop 的 YARN 和 Apache Mesos 作为它的资源管理和调度器， 器， 并且可以处理所有 Hadoop 支持的数据， 包括 HDFS、 HBase 和 Cassandra 等。 这对于已经部署 Hadoop 集群的用户特别重要， 因为不需要做任何数据迁移就可以使用 Spark的强大处理能力。 Spark 也可以不依赖于第三方的资源管理和调度器， 它实现了Standalone 作为其内置的资源管理和调度框架， 这样进一步降低了 Spark 的使用门槛， 使得所有人都可以非常容易地部署和使用 Spark。 此外， Spark 还提供了在EC2 上部署 Standalone 的 Spark 集群的工具。<br>
<img src="https://img-blog.csdn.net/20181018124318406?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjIyOTA1Ng==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="在这里插入图片描述"></p>
</li>
</ul>
<h2><a id="2_Spark_31"></a>2. Spark的安装</h2>
<h4><a id="21__spark__32"></a>2.1 下载 spark 安装包</h4>
<p>下载地址 spark 官网： <a href="http://spark.apache.org/downloads.html" rel="nofollow">http://spark.apache.org/downloads.html</a><br>
这里我们使用 spark-2.0.2-bin-hadoop2.7 版本.<br>
<img src="https://img-blog.csdn.net/20181018124438640?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjIyOTA1Ng==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="在这里插入图片描述"></p>
<h4><a id="2_2__36"></a>2. 2 解压安装包</h4>
<pre><code>tar -zxvf spark-2.0.2-bin-hadoop2.7.tgz
</code></pre>
<h4><a id="23__41"></a>2.3 重命名目录</h4>
<pre><code>mv spark-2.0.2-bin-hadoop2.7 spark
</code></pre>
<h4><a id="24__46"></a>2.4 修改配置文件</h4>
<p>配置文件目录在 /opt/bigdata/spark/conf</p>
<ul>
<li>vi <a href="http://spark-env.sh" rel="nofollow">spark-env.sh</a> 修改文件(先把 spark-env.sh.template 重命名<br>
为 <a href="http://spark-env.sh" rel="nofollow">spark-env.sh</a>)</li>
</ul>
<pre><code>
#配置 java 环境变量
export JAVA_HOME=/export/server/jdk1.8.0_45

#指定 spark 老大 Master 的 IP
export SPARK_MASTER_HOST=nodel01

#指定 spark 老大 Master 的端口
export SPARK_MASTER_PORT=7077

</code></pre>
<ul>
<li>vi slaves 修改文件(先把 slaves.template 重命名为 slaves)</li>
</ul>
<pre><code>#worker的启动节点配置
nodel02
nodel03
</code></pre>
<h4><a id="25__69"></a>2.5 拷贝文件到其他主机</h4>
<p>通过 scp 命令将 spark 的安装目录拷贝到其他机器上</p>
<pre><code> scp -r /export/server/spark nodel02:/export/server/
  scp -r /export/server/spark nodel03:/export/server/
</code></pre>
<h4><a id="26_spark__76"></a>2.6配置 spark 环境变量</h4>
<p>将 spark 添加到环境变量,添加以下内容到 /etc/profile</p>
<pre><code>#spark
export SPARK_HOME=/export/server/spark
export PATH=$PATH:$SPARK_HOME/bin:$SPARK_HOME/sbin
</code></pre>
<h4><a id="27spark_84"></a>2.7启动和通知spark</h4>
<pre><code>#在主节点启动spark
$spark /sbin/start-all.sh

#在主节点停止spark
$spark /sbin/stop-all.sh
</code></pre>
<h3><a id="28_sparkweb_93"></a>2.8 spark的web界面</h3>
<p>正常启动 spark 集群后， 可以通过访问 <a href="http://nodel01:8080" rel="nofollow">http://nodel01:8080</a>,查看 spark 的 web 界面，查看相关信息。<br>
<img src="https://img-blog.csdn.net/20181018172139989?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjIyOTA1Ng==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="在这里插入图片描述"></p>
<h2><a id="3_Spark_HA__97"></a>3. Spark HA 高可用部署</h2>
<h4><a id="31__98"></a>3.1 高可用部署说明</h4>
<p>Spark Standalone 集群是 Master-Slaves 架构的集群模式， 和大部分的Master-Slaves 结构集群一样， 存在着 Master 单点故障的问题。 如何解决这个单点故障的问题， Spark 提供了两种方案：</p>
<ul>
<li><strong>基 于 文 件 系 统 的 单 点 恢 复</strong><br>
主要用于开发或测试环境。 当 spark 提供目录保存 spark Application和 worker 的注册信息， 并将他们的恢复状态写入该目录中， 这时， 一旦 Master发生故障， 就可以通过重新启动 Master 进程（ sbin/start-master.sh） ， 恢复已运行的 spark Application 和 worker 的注册信息。</li>
<li><strong>基于 zookeeper 的 Standby Masters</strong><br>
用于生产模式。 其基本原理是通过 zookeeper 来选举一个 Master， 其他的 Master 处于 Standby 状态。 将 spark 集群连接到同一个 ZooKeeper 实例并启动多个 Master， 利用 zookeeper 提供的选举和状态保存功能， 可以使一个 Master被选举成活着的 master， 而其他 Master 处于 Standby 状态。 如果现任 Master死去， 另一个 Master 会通过选举产生， 并恢复到旧的 Master 状态， 然后恢复调度。 整个恢复过程可能要 1-2 分钟。</li>
</ul>
<h4><a id="32_zookeeper_104"></a>3.2 基于zookeeper的高可用集群部署</h4>
<p>该 HA 方案使用起来很简单， 首先需要搭建一个 zookeeper 集群， 然后启动<br>
zooKeeper 集群， 最后在不同节点上启动 Master。 具体配置如下：<br>
1） vi <a href="http://spark-env.sh" rel="nofollow">spark-env.sh</a><br>
注释掉 export SPARK_MASTER_HOST=nodel01<br>
2）在 <a href="http://spark-env.sh" rel="nofollow">spark-env.sh</a> 添加 SPARK_DAEMON_JAVA_OPTS， 内容如下：</p>
<pre><code>//HA的配置文件
export SPARK_DAEMON_JAVA_OPTS="-Dspark.deploy.recoveryMode=ZOOKEEPER  -Dspark.deploy.zookeeper.url=nodel01:2181,nodel02:2181,nodel03:2181, -Dspark.deploy.zookeeper.dir=/spark"
</code></pre>
<p>3）分发到另外两个节点</p>
<pre><code> scp spark-env.sh  nodel02:/export/server/spark/conf/
  scp spark-env.sh  nodel03:/export/server/spark/conf/
</code></pre>
<h4><a id="33_HA_121"></a>3.3 启动HA集群</h4>
<pre><code>// 1.首先在主节点启动zookeeper
startzk.sh

//2.在主节点启动spark
$spark /sbin/start-all.sh

//3.在任意分节点启动saprk备用节点
$spark /sbin/start-master.sh

//jps查看进程
</code></pre>
<h4><a id="34_HAweb_135"></a>3.4 HA的web页面</h4>
<p><strong>主节点</strong><br>
<img src="https://img-blog.csdn.net/20181018202433329?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjIyOTA1Ng==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="在这里插入图片描述"><br>
<strong>备用节点</strong><br>
<img src="https://img-blog.csdn.net/20181018202515650?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjIyOTA1Ng==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="在这里插入图片描述"></p>
<hr>
<p>本次结束</p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>