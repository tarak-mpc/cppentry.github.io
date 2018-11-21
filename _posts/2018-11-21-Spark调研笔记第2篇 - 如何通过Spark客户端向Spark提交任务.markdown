---
layout:     post
title:      Spark调研笔记第2篇 - 如何通过Spark客户端向Spark提交任务
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-size:14px;">在<a href="http://blog.csdn.net/slvher/article/details/46377553" rel="nofollow">上篇笔记</a>的基础上，本文介绍Spark客户端的基本配置及Spark任务提交方式。</span></p>
<p><span style="font-size:14px;"><strong>1. Spark客户端及基本配置</strong><br>
从Spark官网下载的pre-built包中集成了Spark客户端，如与hadoop ver1.x兼容的Spark客户端位于spark-1.3.1-bin-hadoop1/bin目录下。<br></span><span style="font-size:14px;">Spark客户端通常部署在要提交计算任务的机器上，用来向集群提交应用。特别地，客户端自带的bin/pyspark脚本支持以交互模式向集群提交应用，<strong>在交互模式下测试spark python api的执行结果是很方便的</strong>。<br></span><span style="font-size:14px;">Spark客户端的配置文件通常位于conf目录下，</span><span style="font-size:14px;">典型的配置文件列表如下所列：<br></span></p><pre><code class="language-plain">spark-defaults.conf  // 设置spark master地址、每个executor进程的内存、占用核数，等等
spark-env.sh         // spark相关的各种环境变量
log4j.properties.template    // 设置driver向console输出的日志的等级及格式
fairscheduler.xml.template   // 设置调度方式
metrics.properties.template  // 设置spark内部metrics系统，一般无需改动
slaves               // 设置spark集群中的slave节点（即worker节点），无需改动
hadoop-default.xml   // hadoop配置，主要是hdfs的配置
hadoop-site.xml      // hadoop集群的访问配置（如master地址）</code></pre><strong>注意：</strong><span style="font-size:14px;">由于spark的输入文件通常来自HDFS，故除spark本身的配置文件外，通常还需在conf目录下包含hadoop相关的配置文件，如上述示例中的hadoop-default.xml和hadoop-site.xml。<br></span>在这些配置文件中，最重要的是spark-defaults.conf，典型配置模板如下所示。<br><pre><code class="language-plain"># Default system properties included when running spark-submit.
# This is useful for setting default environmental settings.

# Example:
# spark.master                     spark://master:7077
# spark.eventLog.enabled           true
# spark.eventLog.dir               hdfs://namenode:8021/directory
# spark.serializer                 org.apache.spark.serializer.KryoSerializer
# spark.driver.memory              5g
# spark.executor.extraJavaOptions  -XX:+PrintGCDetails -Dkey=value -Dnumbers="one two three"</code></pre>从模板可见，必须的配置项是spark.master的地址。此外还有一些影响Spark运行性能的配置项，限于篇幅，这里不再赘述。建议仔细阅读<a href="http://spark.apache.org/docs/latest/configuration.html" rel="nofollow">Spark Configuration</a>文档。
<p><span style="font-size:14px;"><strong>2. 如何向spark集群提交应用</strong><br>
Spark客户端自带的bin/spark-submit脚本可以用来向集群提交应用，如下面的示例命令通过Spark客户端提交了一个基于ALS算法的矩阵分解模型用来实现电影个性化推荐：<br></span></p><pre><code class="language-plain">spark-1.3.0.5-bin/bin/spark-submit movie_als_rec.py</code></pre><strong>如果应用程序依赖了其它库，则需要将应用程序及其依赖整体打包提交到spark集群</strong>。具体而言，若提交java应用，则可借助sbt或Maven的相关插件打成jar文件再提交（无需包含spark或hadoop相关的依赖，它们由cluster manager提供）；若提交Python应用，则可先将应用脚本及其依赖打包成.zip或.egg包，然后借助—py-files参数将zip或egg文件传给spark-submit脚本。<br>
spark-submit支持的参数列表可从官网文档了解，也可打开spark-submit脚本查看，脚本中解析参数的代码片段如下：<br><img src="https://img-blog.csdn.net/20150605175321047?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc2x2aGVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br><strong>这里对deploy-mode参数做特别说明：</strong><br>
1) deploy mode分为client和cluster两种。<br>
2) 若待提交的应用部署的节点与集群worker节点在物理网络上很近，则以client模式提交应用较为合理。在client模式下，driver由spark应用脚本所在机器节点的spark-submit直接调起，driver针对应用的输入/输出会打印至该节点的终端控制台。<br>
3) 若spark应用脚本部署节点与spark集群worker节点物理网络距离较远，则以cluster提交可以减少driver和executors间的网络延时（因为正常情况下，应用分解出的若干tasks均会由driver负责调度executor来执行，每次调度均有网络开销）。<br>
4) 目前以standalone或mesos方式部署的spark集群不支持cluster模式，基于Python的spark应用提交也不支持cluster模式。<br><br>
未完待续，下篇笔记将会介绍Spark集群对应用的调度方式。
<p><span style="font-size:14px;">【参考资料】<br>
1. <a href="http://spark.apache.org/docs/latest/configuration.html" rel="nofollow">Spark Configuration</a></span></p>
<p><span style="font-size:14px;">==================== EOF ===================<br><br></span></p>
            </div>
                </div>