---
layout:     post
title:      MongoDB与Spark整合的环境搭建
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。转载请标明原博客地址以及本人博客地址：https://blog.gzczy.top					https://blog.csdn.net/qq_30438573/article/details/82658193				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h2>Spark介绍</h2>

<p><img alt="" class="has" src="http://spark.apache.org/images/spark-logo-trademark.png">         <br>
Spark是加州大学伯克利分校AMP实验室（Algorithms, Machines, and People Lab）开发通用内存并行计算框架。Spark在2013年6月进入Apache成为孵化项目，8个月后成为Apache顶级项目，速度之快足见过人之处，Spark以其先进的设计理念，迅速成为社区的热门项目，围绕着Spark推出了Spark SQL、Spark Streaming、MLLib和GraphX等组件，也就是BDAS（伯克利数据分析栈），这些组件逐渐形成大数据处理一站式解决平台。从各方面报道来看Spark抱负并非池鱼，而是希望替代Hadoop在大数据中的地位，成为大数据处理的主流标准      Spark是在借鉴了MapReduce之上发展而来的，继承了其分布式并行计算的优点并改进了MapReduce明显的缺陷，具体如下：</p>

<p>首先，Spark把中间数据放到内存中，迭代运算效率高。MapReduce中计算结果需要落地，保存到磁盘上，这样势必会影响整体速度，而Spark支持DAG图的分布式并行计算的编程框架，减少了迭代过程中数据的落地，提高了处理效率。</p>

<p>其次，Spark容错性高。Spark引进了弹性分布式数据集RDD (Resilient Distributed Dataset) 的抽象，它是分布在一组节点中的只读对象集合，这些集合是弹性的，如果数据集一部分丢失，则可以根据“血统”（即充许基于数据衍生过程）对它们进行重建。另外在RDD计算时可以通过CheckPoint来实现容错，而CheckPoint有两种方式：CheckPoint Data，和Logging The Updates，用户可以控制采用哪种方式来实现容错。</p>

<p>最后，Spark更加通用。不像Hadoop只提供了Map和Reduce两种操作，Spark提供的数据集操作类型有很多种，大致分为：Transformations和Actions两大类。Transformations包括Map、Filter、FlatMap、Sample、GroupByKey、ReduceByKey、Union、Join、Cogroup、MapValues、Sort和PartionBy等多种操作类型，同时还提供Count, Actions包括Collect、Reduce、Lookup和Save等操作。另外各个处理节点之间的通信模型不再像Hadoop只有Shuffle一种模式，用户可以命名、物化，控制中间结果的存储、分区等。</p>

<h2>Spark和Scala安装</h2>

<p>获取Spark和Scala的tgz安装包</p>

<p>注意：Spark在官网选择安装包时候需要根据你hadoop的实际版本做出选择，这里默认选择hadoop版本为2.7或者以上的，截止这篇文章时候，hadoop版本为3.1.1</p>

<pre>
wget http://mirrors.hust.edu.cn/apache/spark/spark-2.3.1/spark-2.3.1-bin-hadoop2.7.tgz 
wget https://downloads.lightbend.com/scala/2.12.6/scala-2.12.6.tgz
</pre>

<p>解压压缩包，配置生产环境变量（这里以Centos7作为演示）编辑etc/profile环境变量</p>

<pre>
export JAVA_HOME=/usr/java/jdk1.8.0_181-amd64
export JAVA_BIN=/usr/java/jdk1.8.0_181-amd64/bin
export PATH=$PATH:$JAVA_HOME/bin
export CLASSPATH=:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
export JAVA_HOME JAVA_BIN PATH CLASSPATH
export SCALA_HOME=/usr/etc/scala-2.12.6
export PATH=$PATH:$SCALA_HOME/bin:$PATH
export SPARK_HOME=/usr/etc/spark-2.3.1-bin-hadoop2.7
export PATH=$PATH:$SPARK_HOME/bin:$PATH
</pre>

<p>输入Scala检查指令</p>

<pre>
[root@vm_0_2_centos ~]# scala
Welcome to Scala 2.12.6 (Java HotSpot(TM) 64-Bit Server VM, Java 1.8.0_181).
Type in expressions for evaluation. Or try :help.
scala&gt;
</pre>

<h2>Spark配置</h2>

<p>进入spark-2.3.1-bin-hadoop2.7的目录，我们可以看到spark的目录结构（把重点挑出来了）</p>

<p> </p>

<table><tbody><tr><td style="vertical-align:top;">目录</td>
			<td style="vertical-align:top;">bin</td>
			<td style="vertical-align:top;">conf</td>
			<td style="vertical-align:top;">data</td>
			<td style="vertical-align:top;">example</td>
			<td style="vertical-align:top;">jars</td>
			<td style="vertical-align:top;">sbin</td>
			<td style="vertical-align:top;">logs</td>
			<td style="vertical-align:top;">yarn</td>
		</tr><tr><td style="vertical-align:top;">作用</td>
			<td style="vertical-align:top;">运行脚本目录</td>
			<td style="vertical-align:top;">配置文件</td>
			<td style="vertical-align:top;">案例中用到的数据</td>
			<td style="vertical-align:top;">运行案例目录</td>
			<td style="vertical-align:top;">依赖Jar包</td>
			<td style="vertical-align:top;">集群启动脚本</td>
			<td style="vertical-align:top;">日志记录</td>
			<td style="vertical-align:top;">yarn模式</td>
		</tr></tbody></table><p>进入Conf目录 我们可以看到以下几个文件</p>

<pre>
[root@vm_0_2_centos ~]# cd /usr/etc/spark-2.3.1-bin-hadoop2.7/conf
[root@vm_0_2_centos conf]# ls
docker.properties.template  log4j.properties.template    slaves.template
fairscheduler.xml.template  metrics.properties.template  spark-defaults.conf.template  spark-env.sh.template
</pre>

<p>这些是默认文件，我们可以直接修改后把template后缀去除，也可以复制一份新文件，我们这里复制一份新的文件，我们复制后打开查看配置选项</p>

<pre>
cp spark-env.sh.template  spark-env.sh
</pre>

<p>打开spark-env.sh进行配置,下面来解释一下下面的一些启动需要的基础设置，如果需要详细了解各个设置可以在spark-env.sh里面看到，也有官方解释</p>

<pre>
export JAVA_HOME=/usr/java/jdk1.8.0_181-amd64   配置JAVA目录
export SCALA_HOME=/usr/etc/scala-2.12.6  配置scala目录
export SPARK_MASTER_HOST=172.16.0.2 MasterIP
export SPARK_LOCAL_IP=172.16.0.2  本地IP
export SPARK_WORKER_MEMORY=1500m 设置worker的工作内存【避免全部内存被占用，默认占用1G】
</pre>

<p>建议可以修改自己的HostName，因为默认master地址就是以你的Hostname，除非你自己手动输入指令指定</p>

<pre>
[root@centos7 ~]$ hostnamectl set-hostname czy             # 使用这个命令会立即生效且重启也生效
[root@centos7 ~]$ hostname                                 # 查看下czy
[root@centos7 ~]$ vim /etc/hosts                           # 编辑下hosts文件， 给127.0.0.1添加hostname
[root@centos7 ~]$ cat /etc/hosts                           # 检查
</pre>

<h2>Spark启动</h2>

<p>启动master：</p>

<pre>
./sbin/start-master.sh
</pre>

<p>启动成功后，我们可以到WebUI控制台查看信息，默认端口WebUI8080</p>

<p><img alt="" class="has" src="https://image.gzczy.top/2018/09/1NLHT4GRPMAXTF7XQ8KE.png"></p>

<p>启动worker节点：（可以启动多个worker节点，只要你机器内存够）</p>

<pre>
./sbin/start-slave.sh spark://192.168.2.110:7077
</pre>

<p>worker节点默认是7077端口，worker节点工作状态默认是从8081端口开始，每添加一个worker则端口数+1</p>

<p>启动后 我们可以以submit方式提交JAVA的JAR包交给节点submit执行</p>

<pre>
spark-submit --master spark://192.168.2.110:7077 --executor-memory 1g --total-executor-cores 2  --driver-memory 2G  --class com.test.main.test test.jar
</pre>

<p>下面我来解释一下这些提交参数是什么意思：</p>

<p>--master spark集群主节点的地址<br>
--executor-memory 每个Executor进程的内存<br>
--total-executor-cores 所有的executor使用的总CPU核数<br>
--driver-memory    设置Driver进程的内存<br>
--class 要运行的main函数类 类所在的jar包</p>

<p>执行后我们可以在控制台点击你刚刚执行的任务，看到一些运行状态和结果</p>

<h2>使用MongoDB Spark Connector连接MongoDB</h2>

<p>MongoDB官方提供了连接器给Spark和MongoDB作为连接桥梁，目前版本为2.3.x</p>

<p>开源地址：<a href="https://github.com/mongodb/mongo-spark/tree/2.3.x" rel="nofollow">https://github.com/mongodb/mongo-spark/tree/2.3.x</a></p>

<p>官方文档：<a href="https://docs.mongodb.com/spark-connector/master/scala-api/" rel="nofollow">https://docs.mongodb.com/spark-connector/master/scala-api/</a></p>

<p><img alt="" class="has" src="https://image.gzczy.top/2018/09/sparkFromMongoDB_1.png"></p>

<p>MongoDB在spark可以如下图：</p>

<p><img alt="" class="has" src="https://image.gzczy.top/2018/09/sparkFromMongoDB_2.png"></p>

<p> </p>

<p>我们通常都是以HDFS作为大数据存储介质，如果用MongoDB有什么好处？两者区别又在哪里？下面我们来对比一下</p>

<p>在说区别之前，其实我们可以先来注意一下两者的共同点。HDFS和MongoDB都是基于廉价x86服务器的横向扩展架构，都能支持到TB到PB级的数据量。数据会在多节点自动备份，来保证数据的高可用和冗余。两者都支持非结构化数据的存储，等等。</p>

<p>但是，HDFS和MongoDB更多的是差异点：</p>

<ul><li>
	<p>如在存储方式上 HDFS的存储是以文件为单位，每个文件64MB到128MB不等。而MongoDB则是细颗粒化的、以文档为单位的存储。</p>
	</li>
	<li>
	<p>HDFS不支持索引的概念，对数据的操作局限于扫描性质的读，MongoDB则支持基于二级索引的快速检索。</p>
	</li>
	<li>
	<p>MongoDB可以支持常见的增删改查场景，而HDFS一般只是一次写入后就很难进行修改。</p>
	</li>
	<li>
	<p>从响应时间上来说，HDFS一般是分钟级别而MongoDB对手请求的响应时间通常以毫秒作为单位。</p>
	</li>
</ul><p> </p>

<p>Spark的工作流程可以概括为三部曲：创建并发任务，对数据进行transformation操作，如map， filter，union，intersect等，然后执行运算，如reduce，count，或者简单地收集结果。</p>

<p>这里我给出一副架构图，基于spark整合MongoDB的业务，如图所示，MongoDB是作为连接器一个中续器 负责读取数据和写入数据，我们可以用来读取MongoDB中数据，交给spark计算，计算后的结果回写进入MongoDB，然后可以通过web进行查询回写的数据</p>

<p><img alt="" class="has" src="https://image.gzczy.top/2018/09/sparkFromMongoDB.png"></p>

<p>Spark任务一般由Spark的driver节点发起，经过Spark Master进行资源调度分发。比如这里我们有4个Spark worker节点，这些节点上的几个executor 计算进程就会同时开始工作。一般一个core就对应一个executor。每个executor会独立的去MongoDB取来原始数据，直接套用Spark提供的分析算法或者使用自定义流程来处理数据，计算完后把相应结果写回到MongoDB。</p>

<p>我们需要提到的是：在这里，所有和MongoDB的交互都是通过Mongo-Spark的连接器来完成的。</p>

<p> </p>

<p>下面我们通过连接器写一个简单的案例【Java版本】</p>

<pre>
//使用builer创建sparkSession，在Spark2.0 sparkContext可以通过sparksession获取，inputURL指是在MongoDB读取的集合，outputURL是写入那个集合中
SparkSession spark = SparkSession.builder().master(master).appName("Task_Java").
      config("spark.mongodb.input.uri", "mongodb://192.168.2.99:20000/gslog.test?authSource=admin")
      .config("spark.mongodb.output.uri","mongodb://192.168.2.99:20000/one.testCollection?authSource=admin")
      .config("spark.executor.memory","512mb")
      .getOrCreate();
JavaSparkContext jsc = new JavaSparkContext(spark.sparkContext());
//使用Spark连接器载入sparkContext，获取RDD对象
JavaMongoRDD&lt;Document&gt; rdd = MongoSpark.load(jsc);
//使用MongoDB管道 过滤符合条件的数据
JavaMongoRDD&lt;Document&gt; aggregatedRdd = rdd.withPipeline(singletonList(Document.parse("{ $match: { _date : { $gt : '2018-07-03' } } }")));
//根据userName 聚合flowSize
JavaPairRDD&lt;Integer, Integer&gt; result = aggregatedRdd.mapToPair(
      (PairFunction&lt;Document, Integer, Integer&gt;) arg0 -&gt;
            new Tuple2(arg0.getInteger("_lv"), arg0.getInteger("realMoney"))).cache();
//spark ReduceByKey计算            
JavaPairRDD&lt;Integer, Integer&gt; rds = result.reduceByKey((v1, v2) -&gt; v1 + v2);
//将聚合结果转化为Document
JavaRDD&lt;Document&gt; saveR = rds.map((Function&lt;Tuple2&lt;Integer, Integer&gt;, Document&gt;) arg0 -&gt;
      Document.parse("{_lv:\""+arg0._1+"\",_vip:"+arg0._2+"}")); 
//将结果回写mongodb
MongoSpark.save(saveR);
jsc.close();
</pre>

<h2>执行简单案例</h2>

<ol><li>
	<p>可以通过idea直接执行，但是需要打包后放在spark/jars 目录里面，否则直接在idea报错【ClassNotFoundException】</p>
	</li>
	<li>
	<p>打包后放在spark/jars 目录里面 通过submit执行</p>
	</li>
</ol><h2>总结</h2>

<p>上面只是一些简单的演示，实际上Spark + MongoDB的使用可以通过Spark的很多种形式来使用。我们来总结一下Spark ＋ Mongo的应用场景。在座的同学可能很多人已经使用了MongoDB，也有些人已经使用了Hadoop。我们可以从两个角度来考虑这个事情：</p>

<p>对那些已经使用MongoDB的用户，如果你希望在你的MongoDB驱动的应用上提供个性化功能，比如说像Yahoo一样为你找感兴趣的新闻，能够在你的MongoDB数据上利用到Spark强大的机器学习或者流处理，你就可以考虑在MongoDB集群上部署Spark来实现这些功能。</p>

<p>如果你已经使用Hadoop而且数据已经在HDFS里面，你可以考虑使用Spark来实现更加实时更加快速的分析型需求，并且如果你的分析结果有数据量大、格式多变以及这些结果数据要及时提供给前台APP使用的需求，那么MongoDB可以用来作为你分析结果的一个存储方案</p>

<h2>参考</h2>

<p>感谢以下文章，参考后有感而发</p>

<p>spark+mongodb + quartz</p>

<p><a href="https://blog.csdn.net/changyuan101/article/details/53257799" rel="nofollow">https://blog.csdn.net/changyuan101/article/details/53257799</a></p>

<p>关于Spark报错不能连接到Server的解决办法</p>

<p><a href="https://blog.csdn.net/ybdesire/article/details/70666544" rel="nofollow">https://blog.csdn.net/ybdesire/article/details/70666544</a></p>

<p>spark作业提交：</p>

<p><a href="https://blog.csdn.net/qq_30307645/article/details/51628907" rel="nofollow">https://blog.csdn.net/qq_30307645/article/details/51628907</a></p>

<p>spark-JAR包问题：</p>

<p><a href="https://www.jianshu.com/p/2c7bcee7001a" rel="nofollow">https://www.jianshu.com/p/2c7bcee7001a</a></p>

<p>MongoDB：完整的大数据解决方案</p>

<p><a href="http://www.mongoing.com/tj/mongodb_shanghai_spark" rel="nofollow">http://www.mongoing.com/tj/mongodb_shanghai_spark</a></p>

<p>MongoDB+spark：完整的大数据解决方案演讲稿</p>

<p><a href="http://www.mongoing.com/wp-content/uploads/2016/08/MDBSH2016/TJ_MongoDB+Spark.pdf" rel="nofollow">http://www.mongoing.com/wp-content/uploads/2016/08/MDBSH2016/TJ_MongoDB+Spark.pdf</a>​</p>

<p>我的博客原文： <a href="https://blog.gzczy.top/index.php/2018/09/07/mongodb%E4%B8%8Espark%E6%95%B4%E5%90%88%E7%9A%84%E7%8E%AF%E5%A2%83%E6%90%AD%E5%BB%BA/" rel="nofollow">https://blog.gzczy.top/index.php/2018/09/07/mongodb与spark整合的环境搭建/</a></p>            </div>
                </div>