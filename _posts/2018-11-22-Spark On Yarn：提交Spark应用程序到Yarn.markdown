---
layout:     post
title:      Spark On Yarn：提交Spark应用程序到Yarn
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>转载自：<a href="http://lxw1234.com/archives/2015/07/416.htm" rel="nofollow">http://lxw1234.com/archives/2015/07/416.htm</a> <br>
关键字：Spark On Yarn、Spark Yarn Cluster、Spark Yarn Client</p>

<p>Spark On Yarn模式配置非常简单，只需要下载编译好的Spark安装包，在一台带有Hadoop Yarn客户端的机器上解压，简单配置之后即可使用。</p>

<p>要把Spark应用程序提交到Yarn运行，首先需要配置HADOOP_CONF_DIR或者YARN_CONF_DIR，让Spark知道Yarn的配置信息，比如：ResourceManager的地址。可以配置在spark-env.sh中，也可以在提交Spark应用之前export：</p>

<p>export HADOOP_CONF_DIR=/etc/hadoop/conf <br>
yarn-cluster模式提交Spark应用程序</p>



<pre class="prettyprint"><code class=" hljs haml">
./spark-submit \
-<span class="ruby">-<span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">com</span>.<span class="hljs-title">lxw1234</span>.<span class="hljs-title">test</span>.<span class="hljs-title">WordCount</span> \</span>
</span>-<span class="ruby">-master yarn-cluster \
</span>-<span class="ruby">-executor-memory <span class="hljs-number">4</span>G \
</span>-<span class="ruby">-num-executors <span class="hljs-number">10</span> \
</span><span class="hljs-comment">/home/lxw1234/spark-wordcount.jar \</span>
<span class="hljs-comment">/logs/2015-07-14/ /tmp/lxw1234/output/</span></code></pre>

<p>yarn-client模式提交Spark应用程序</p>



<pre class="prettyprint"><code class=" hljs haml">
./spark-submit \
-<span class="ruby">-<span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">com</span>.<span class="hljs-title">lxw1234</span>.<span class="hljs-title">test</span>.<span class="hljs-title">WordCount</span> \</span>
</span>-<span class="ruby">-master yarn-client \
</span>-<span class="ruby">-executor-memory <span class="hljs-number">4</span>G \
</span>-<span class="ruby">-num-executors <span class="hljs-number">10</span> \
</span><span class="hljs-comment">/home/lxw1234/spark-wordcount.jar \</span>
<span class="hljs-comment">/logs/2015-07-14/ /tmp/lxw1234/output/</span></code></pre>

<p>Yarn Cluster模式和Yarn Client模式的主要区别</p>

<p>yarn-cluster模式中，应用程序(包括SparkContext)都是作为Yarn框架所需要的</p>

<p>ApplicationMaster,在Yarn ResourceManager为其分配的一个随机节点上运行；</p>

<p>而在yarn-client模式中，SparkContext运行在本地，该模式适用于应用程序本身需要在本地进行交互的场合。</p>

<p>Spark Standalone模式下提交Spark应用程序，可参考：</p>

<p><a href="http://lxw1234.com/archives/2015/05/215.htm" rel="nofollow">http://lxw1234.com/archives/2015/05/215.htm</a></p>

<p>以下是一些Spark On Yarn相关的配置参数： <br>
spark.yarn.am.memory</p>

<p>默认值：512M</p>

<p>在yarn-client模式下，申请Yarn App Master所用的内存。 <br>
spark.driver.memory</p>

<p>默认值：512M</p>

<p>在yarn-cluster模式下，申请Yarn App Master（包括Driver）所用的内存。 <br>
spark.yarn.am.cores</p>

<p>默认值：1</p>

<p>在yarn-client模式下，申请Yarn App Master所用的CPU核数 <br>
spark.driver.cores</p>

<p>默认值：1</p>

<p>在yarn-cluster模式下，申请Yarn App Master（包括Driver）所用的CPU核数。 <br>
spark.yarn.am.waitTime</p>

<p>默认值：100s</p>

<p>在yarn-cluster模式下，Yarn App Master等待SparkContext初始化完成的时间；</p>

<p>在yarn-client模式下，Yarn App Master等待SparkContext链接它的时间； <br>
spark.yarn.submit.file.replication</p>

<p>默认值：HDFS副本数</p>

<p>Spark应用程序的依赖文件上传到HDFS时，在HDFS中的副本数，这些文件包括Spark的Jar包、应用程序的Jar包、其他作为DistributeCache使用的文件等。通常，如果你的集群节点数越多，相应地就需要设置越多的拷贝数以加快这些文件的分发。 <br>
spark.yarn.preserve.staging.files</p>

<p>默认值：false</p>

<p>在应用程序结束后是否保留上述上传的文件。 <br>
spark.yarn.scheduler.heartbeat.interval-ms</p>

<p>默认值：5000</p>

<p>Spark Application Master向Yarn ResourceManager发送心跳的时间间隔，单位毫秒。 <br>
spark.yarn.max.executor.failures</p>

<p>默认值：numExecutors * 2 (最小为3)</p>

<p>最多允许失败的Executor数量。 <br>
spark.yarn.historyServer.address</p>

<p>默认值：none</p>

<p>Spark运行历史Server的地址，主机:host，如：lxw1234.com:18080，注意不能包含http://</p>

<p>默认不配置，必须开启Spark的historyServer之后才能配置。该地址用于Yarn ResourceManager在Spark应用程序结束时候，将该application的运行URL从ResourceManager的UI指向Spark historyServer UI。 <br>
spark.executor.instances</p>

<p>默认值：2</p>

<p>Executor实例的数量，不能与spark.dynamicAllocation.enabled同时使用。 <br>
spark.yarn.queue</p>

<p>默认值：default</p>

<p>指定提交到Yarn的资源池 <br>
spark.yarn.jar</p>

<p>Spark应用程序使用的Jar包位置，比如：hdfs://cdh5/lxw1234.com/</p>

<p>参考更多大数据Hadoop、Spark、Hive相关：lxw的大数据田地</p>

<p>另外，在提交Spark应用程序到Yarn时候，可以使用—files指定应用程序所需要的文件；</p>

<p>使用—jars 和 –archives添加应用程序所依赖的第三方jar包等。</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>