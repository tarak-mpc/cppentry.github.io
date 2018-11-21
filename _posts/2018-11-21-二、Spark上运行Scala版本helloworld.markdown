---
layout:     post
title:      二、Spark上运行Scala版本helloworld
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p><strong>摘要：</strong>Spark作为新一代大数据计算引擎，因为内存计算的特性，具有比hadoop更快的计算速度。这里总结下对Spark的认识、虚拟机Spark安装、Spark开发环境搭建及编写第一个scala程序、运行第一个Spark程序。</p>

<h3 id="autoid-0-0-0">1.Spark是什么</h3>

<p>Spark是一个快速且通用的集群计算平台</p>

<h3 id="autoid-1-0-0">2.Spark的特点</h3>

<p>1）Spark是快速的</p>

<p>   Spark扩充了流行的Mapreduce计算模型</p>

<p>   Spark是基于内存的计算</p>

<p>2）Spark是通用的</p>

<p>    Spark的设计容纳了其它分布式系统拥有的功能</p>

<p>    批处理，迭代式计算，交互查询和流处理等</p>

<p>3）Spark是高度开放的</p>

<p>    Spark提供了Python,Java,Scala,SQL的API和丰富的内置库。</p>

<p>    Spark和其它的大数据工具整合的很好，包括hadoop,kafka等</p>

<h3 id="autoid-2-0-0">3.Spark的组件</h3>

<p>Spark包括多个紧密集成的组件</p>

<p><img alt="" class="has" height="192" src="https://images2015.cnblogs.com/blog/160633/201707/160633-20170707210324659-1725791751.png" width="607"></p>

<p> Spark Core:</p>

<p>　　包含Spark的基本功能，包含任务调度，内存管理，容错机制等</p>

<p>　　内部定义了RDDs(弹性分布式数据集)</p>

<p>　　提供了很多APIs来创建和操作这些RDDs</p>

<p>　　应用场景，为其他组件提供底层的服务</p>

<p>Spark SQL:</p>

<p> 　　是Spark处理结构化数据的库，就像Hive SQL,Mysql一样</p>

<p>　　应用场景，企业中用来做报表统计</p>

<p>Spark Streaming:</p>

<p>　　是实时数据流处理组件，类似Storm</p>

<p>　　Spark Streaming提供了API来操作实时流数据</p>

<p>　　应用场景，企业中用来从Kafka接收数据做实时统计</p>

<p>MLlib:</p>

<p>       一个包含通用机器学习功能的包，Machine learning lib</p>

<p>       包含分类，聚类，回归等，还包括模型评估和数据导入。</p>

<p>　　MLlib提供的上面这些方法，都支持集群上的横向扩展。</p>

<p>　　应用场景，机器学习。</p>

<p>Graphx:</p>

<p>　　是处理图的库（例如，社交网络图），并进行图的并行计算。</p>

<p>　　像Spark Streaming,Spark SQL一样，它也继承了RDD API。</p>

<p>　　它提供了各种图的操作，和常用的图算法，例如PangeRank算法。</p>

<p>　　应用场景，图计算。</p>

<p>Cluster Managers:</p>

<p>　　就是集群管理，Spark自带一个集群管理是单独调度器。</p>

<p>　　常见集群管理包括Hadoop YARN,Apache Mesos</p>

<h3 id="autoid-3-0-0">4.紧密集成的优点</h3>

<p>　　Spark底层优化了，基于Spark底层的组件也得到了相应的优化。</p>

<p>　　紧密集成，节省了各个组件组合使用时的部署、测试等时间。</p>

<p>　　向Spark增加新的组件时，其它组件，可立刻享用新组件的功能。</p>

<h3 id="autoid-4-0-0">5.Spark与Hadoop的比较</h3>

<p>　　Hadoop应用场景：离线处理、对时效性要求不高</p>

<p>　　Spark应用场景：时效性要求高的场景、机器学习等领域</p>

<p>　　Doug Cutting的观点：这是生态系统，每个组件都有其作用，各善其职即可。Spark不具有HDFS的存储能力，要借助HDFS等持久化数据。大数据将会孕育出更多的新技术。</p>

<h3 id="autoid-5-0-0">6.Spark运行环境</h3>

<p>　　Spark是Scala写的，运行在JVM上，所以运行环境Java7+</p>

<p>　　如果使用Python API,需要安装Python2.6+或者Python3.4+</p>

<p>　　版本对应：Spark1.6.2 -  Scala2.10    Spark2.0.0 -  Scala2.11</p>

<h3 id="autoid-6-0-0">7.Spark安装</h3>

<p>　　Spark下载地址：http://spark.apache.org/downloads.html     注：搭Spark不需要Hadoop,如有hadoop集群，可下载相应的版本。</p>

<p>　　<img alt="" class="has" src="https://images2015.cnblogs.com/blog/160633/201707/160633-20170708161338081-1264339667.png"></p>

<p>　　这里安装在CentOS6.5虚拟机上，将下载好的文件上传虚拟机，并执行解压：tar -zxvf spark-2.0.1-bin-hadoop2.6.tgz</p>

<p>　　Spark目录：</p>

<p>　　　　bin包含用来和Spark交互的可执行文件，如Spark shell。</p>

<p>　　　　examples包含一些单机Spark job,可以研究和运行这些例子。</p>

<p>　　Spark的Shell:</p>

<p>　　　　Spark的shell能够处理分布在集群上的数据。</p>

<p>　　　　Spark把数据加载到节点的内存中，因此分布式处理可在秒级完成。</p>

<p>　　　　快速使用迭代式计算，实时查询、分析一般能够在shells中完成。</p>

<p>　　　　Spark提供了Python shells和Scala shells。</p>

<p>　　这里以Scala shell为例，演示读取本地文件并进行操作：</p>

<p>　　　　进入Scala shell:./spark-shell</p>

<p>　　　　<img alt="" class="has" src="https://images2015.cnblogs.com/blog/160633/201707/160633-20170709110332415-1250113989.png"></p>

<p>　　　　创建测试文件helloSpark并输入内容：</p>

<p>　　　　<img alt="" class="has" src="https://images2015.cnblogs.com/blog/160633/201707/160633-20170709111031290-1066023809.png"></p>

<p>　　　　输入val lines=sc.textFile("/home/lucy/hellospark") 加载文件内容，输入lines.count()进行统计行数：    （ 注：sc为spark content）</p>

<p> 　　　　<img alt="" class="has" src="https://images2015.cnblogs.com/blog/160633/201707/160633-20170709111832806-732959196.png"></p>

<p>　　ssh的配置：（ssh localhost需要输入密码，这在运行spark程序时是不可以的）</p>

<p>　　　　ssh-keygen （生成秘钥）</p>

<p>　　　　.ssh目录下cat xxx_rsa.pub&gt; authorized_keys</p>

<p>　　　　chmod 600 authorized_keys</p>

<h3 id="autoid-7-0-0">8.Spark开发环境搭建</h3>

<p>　　Scala 下载地址: http://www.scala-lang.org/download/2.11.6.html   注：默认安装选项会自动配置环境变量,安装路径不能有空格。</p>

<p>　　IntelliJ IDEA 下载地址：https://www.jetbrains.com/idea/</p>

<p>　　注册码地址：http://idea.lanyus.com</p>

<p>　　由于这里下载的ideaIU-15.0.2.exe，已经包含有Scala插件，如果不包含需要下载。查看是否已有scala插件可以新建项目，打开Files-&gt;settings选择Plugins,输入scala查看：</p>

<p>　　<img alt="" class="has" src="https://images2015.cnblogs.com/blog/160633/201707/160633-20170709143214978-627148134.png"></p>

<h3 id="autoid-8-0-0">9.编写第一个Scala程序</h3>

<p>　　依次点击File-&gt;New-&gt;Project，选择Scala-&gt;SBT，下一步，打开如下窗口：</p>

<p>　　<img alt="" class="has" src="https://images2015.cnblogs.com/blog/160633/201707/160633-20170709152927634-1808062523.png"></p>

<p>　　这里Scala选择为2.11.6，创建完成后会进行初始化操作，自动下载jar包等。下载时常看具体网络情况。待所有进度条完成后，项目目录已经出来了，如下：</p>

<p>　　<img alt="" class="has" src="https://images2015.cnblogs.com/blog/160633/201707/160633-20170709153633697-1649567216.png"></p>

<p>　　编辑build.sbt:</p>

<p>　　name := "LearnSpark"</p>

<p>　　version := "1.0"</p>

<p>　　scalaVersion := "2.11.1"</p>

<p>　　libraryDependencies += "org.apache.spark" % "spark-core_2.11" % "2.0.2"</p>

<p>　　编辑完成后，点击刷新，后台自动下载对应的依赖：</p>

<p>　　<img alt="" class="has" src="https://images2015.cnblogs.com/blog/160633/201707/160633-20170709160234197-1301433555.png"></p>

<p>　　src-&gt;scala右击新建scala类WordCount</p>

<p>　　<img alt="" class="has" src="https://images2015.cnblogs.com/blog/160633/201707/160633-20170709160924212-1107952223.png"></p>

<p><a><img alt="复制代码" class="has" src="https://common.cnblogs.com/images/copycode.gif"></a></p>

<pre>
import org.apache.spark.{SparkContext, SparkConf}
/**
  * Created by Lucy on 2017/7/4.
  */
object WordCount {
  def main(args: Array[String]) {
    val conf=new SparkConf().setAppName("wordcount")
    val sc=new SparkContext(conf)
    val input=sc.textFile("/home/lucy/helloSpark")
    val lines=input.flatMap(line=&gt;line.split(" "))
    val count=lines.map(word=&gt;(word,1)).reduceByKey{case (x,y)=&gt;x+y}
    val output=count.saveAsTextFile("/home/lucy/hellosparkRes")
  }
}</pre>

<p><a><img alt="复制代码" class="has" src="https://common.cnblogs.com/images/copycode.gif"></a></p>

<p>　　代码编写完成后，进行打包（配置jar包，build）:</p>

<p>　　配置jar包：File-&gt;Project Structure，选择Artifacts，点击+号：</p>

<p>　　<img alt="" class="has" src="https://images2015.cnblogs.com/blog/160633/201707/160633-20170709162933009-626864230.png"></p>

<p>　　<img alt="" class="has" src="https://images2015.cnblogs.com/blog/160633/201707/160633-20170709163131993-479145970.png">　　</p>

<p>　　这里不打包依赖。配置jar包完成后，Build-&gt;Build Artifacts,等待build完成。</p>

<h3 id="autoid-9-0-0">10.运行第一个Spark程序</h3>

<p>　　这里需要先启动集群：</p>

<p>　　启动master:  ./sbin/start-master.sh</p>

<p>　　启动worker:  ./bin/spark-class org.apache.spark.deploy.worker.Worker spark://localhost:7077  </p>

<p>　　　　　　　　这里的地址为：启动master后,在浏览器输入localhost:8080,查看到的master地址</p>

<p>　　　　　　　　<img alt="" class="has" src="https://images2015.cnblogs.com/blog/160633/201707/160633-20170709164628884-2012834048.png"></p>

<p>　　启动成功后，jps查看进程：</p>

<p>　　<img alt="" class="has" src="https://images2015.cnblogs.com/blog/160633/201707/160633-20170709164844540-893497592.png"></p>

<p>　　接下来执行提交命令，将打好的jar包上传到linux目录，jar包在项目目录下的out\artifacts下。</p>

<p>　　提交作业： ./bin/spark-submit --master spark://localhost:7077 --class WordCount /home/lucy/learnspark.jar</p>

<p>　　可以在4040端口查看job进度：</p>

<p>　　<img alt="" class="has" src="https://images2015.cnblogs.com/blog/160633/201707/160633-20170709171142962-699064477.png"></p>

<p>　　查看结果：</p>

<p>　　<img alt="" class="has" src="https://images2015.cnblogs.com/blog/160633/201707/160633-20170709171516853-396451951.png"></p>

<p>　　由于按照空格分割字符串，所以这里将Spark! 视为一个单词。至此，任务运行结束！</p>            </div>
                </div>