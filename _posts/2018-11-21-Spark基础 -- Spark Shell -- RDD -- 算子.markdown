---
layout:     post
title:      Spark基础 -- Spark Shell -- RDD -- 算子
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1><a id="Spark__Spark_Shell__RDD___0"></a>Spark基础 – Spark Shell – RDD – 算子</h1>
<p></p><div class="toc"><h3>文章目录</h3><ul><li><a href="#Spark__Spark_Shell__RDD___0" rel="nofollow">Spark基础 -- Spark Shell -- RDD -- 算子</a></li><ul><ul><li><a href="#_3" rel="nofollow">一、简介</a></li><li><a href="#Spark_163_7" rel="nofollow">二、Spark 1.6.3部署</a></li><ul><ul><li><a href="#_9" rel="nofollow">准备工作</a></li><li><a href="#_17" rel="nofollow">解压安装</a></li><li><a href="#sparkmaster_25" rel="nofollow">配置spark，master高可用</a></li><li><a href="#_54" rel="nofollow">配置环境变量</a></li><li><a href="#Spark_64" rel="nofollow">分发配置好的Spark到其他节点</a></li></ul></ul><li><a href="#Spark_73" rel="nofollow">三、Spark集群启动和测试</a></li><ul><ul><li><a href="#_75" rel="nofollow">启动</a></li><li><a href="#_89" rel="nofollow">测试</a></li></ul></ul><li><a href="#Spark_Shell_111" rel="nofollow">四、Spark Shell</a></li><ul><ul><li><a href="#spark_shell_113" rel="nofollow">spark shell简介</a></li><li><a href="#spark_shell__117" rel="nofollow">spark shell 启动</a></li><li><a href="#spark_shellwordcount_146" rel="nofollow">在spark shell中编写wordcount程序</a></li></ul></ul><li><a href="#IDEAWordCount_155" rel="nofollow">五、IDEA中编写WordCount</a></li><ul><ul><li><a href="#pomxml_157" rel="nofollow">pom.xml</a></li><li><a href="#_220" rel="nofollow">编写代码</a></li><li><a href="#Linux_259" rel="nofollow">打包到Linux服务器中运行</a></li></ul></ul><li><a href="#RDD_275" rel="nofollow">六、弹性分布式数据集RDD</a></li><ul><ul><li><a href="#RDD_277" rel="nofollow">RDD简介</a></li><li><a href="#RDD_281" rel="nofollow">RDD属性</a></li><li><a href="#RDD_300" rel="nofollow">RDD创建</a></li></ul></ul><li><a href="#RDDAPI_312" rel="nofollow">七、RDD编程API</a></li><ul><ul><li><a href="#Transformation_314" rel="nofollow">Transformation</a></li><li><a href="#Action_341" rel="nofollow">Action</a></li></ul></ul><li><a href="#_358" rel="nofollow">八、算子进阶</a></li></ul></ul></ul></div><p></p>
<h3><a id="_3"></a>一、简介</h3>
<p>​	Apache Spark 是专为大规模数据处理而设计的快速通用的计算引擎。Spark是UC Berkeley AMP lab (加州大学伯克利分校的AMP实验室)所开源的类Hadoop MapReduce的通用并行框架，Spark，拥有Hadoop MapReduce所具有的优点；但不同于MapReduce的是——Job中间输出结果可以保存在内存中，从而不再需要读写HDFS，因此Spark能更好地适用于数据挖掘与机器学习等需要迭代的MapReduce的算法。</p>
<h3><a id="Spark_163_7"></a>二、Spark 1.6.3部署</h3>
<h5><a id="_9"></a>准备工作</h5>
<ol>
<li>
<p>三台Linux服务器，安装好JDK1.8、Hadoop2.6</p>
</li>
<li>
<p>下载安装包<a href="http://archive.apache.org/dist/spark/spark-1.6.3/spark-1.6.3-bin-hadoop2.6.tgz" rel="nofollow">spark1.6.3</a></p>
</li>
<li>
<p>将安装包上传到第一台Linux服务器上</p>
</li>
</ol>
<h5><a id="_17"></a>解压安装</h5>
<p>解压安装包到指定位置</p>
<blockquote>
<p>tar -zxvf spark-1.6.3-bin-hadoop2.6.tgz -C /home/bigdata/installsoft/</p>
</blockquote>
<p>将文件夹重命名为spark-1.6.3</p>
<h5><a id="sparkmaster_25"></a>配置spark，master高可用</h5>
<ol>
<li>
<p>进入spark安装目录下的conf目录</p>
<blockquote>
<p>cd /home/bigdata/installsoft/spark-1.6.3/conf</p>
</blockquote>
</li>
<li>
<p><a href="http://xn--spark-env-kj5q.sh.xn--templatespark-env-rn60an32f84buz10d.sh" rel="nofollow">将spark-env.sh.template重命名为spark-env.sh</a></p>
<blockquote>
<p>mv spark-env.sh.template <a href="http://spark-env.sh" rel="nofollow">spark-env.sh</a></p>
</blockquote>
</li>
<li>
<p>编辑spark-env.sh并添加配置</p>
<pre><code>export JAVA_HOME=/home/bigdata/installsoft/jdk1.8.0_181/
export SPARK_DAEMON_JAVA_OPTS="-Dspark.deploy.recoveryMode=ZOOKEEPER -Dspark.deploy.zookeeper.url=cdhnocms01,cdhnocms02,cdhnocms03 -Dspark.deploy.zookeeper.dir=/spark"
export SPARK_MASTER_PORT=7077
export HADOOP_CONF_DIR=/home/bigdata/installsoft/hadoop-2.6.0-cdh5.13.2/etc/hadoop
</code></pre>
</li>
<li>
<p>将slaves.template重命名为slaves</p>
<blockquote>
<p>mv slaves.template slaves</p>
</blockquote>
</li>
<li>
<p>在slaves文件汇总添加worker节点所在的主机</p>
<blockquote>
<p>cdhnocms01<br>
cdhnocms02<br>
cdhnocms03</p>
</blockquote>
</li>
</ol>
<h5><a id="_54"></a>配置环境变量</h5>
<p>在用户家目录下.bash_profile文件中添加</p>
<blockquote>
<p>SPARK_HOME=/home/bigdata/installsoft/spark-1.6.3/</p>
<p>PATH=<span class="katex--inline"><span class="katex"><span class="katex-mathml"><math><semantics><mrow><mi>P</mi><mi>A</mi><mi>T</mi><mi>H</mi><mo>:</mo></mrow><annotation encoding="application/x-tex">PATH:</annotation></semantics></math></span><span class="katex-html" aria-hidden="true"><span class="base"><span class="strut" style="height: 0.68333em; vertical-align: 0em;"></span><span class="mord mathit" style="margin-right: 0.13889em;">P</span><span class="mord mathit">A</span><span class="mord mathit" style="margin-right: 0.13889em;">T</span><span class="mord mathit" style="margin-right: 0.08125em;">H</span><span class="mspace" style="margin-right: 0.277778em;"></span><span class="mrel">:</span></span></span></span></span>SPARK_HOME/bin:$SPARK_HOME/sbin</p>
</blockquote>
<p>保存退出后source .bash_profile</p>
<h5><a id="Spark_64"></a>分发配置好的Spark到其他节点</h5>
<pre><code>scp -r /home/bigdata/installsoft/spark-1.6.3/ cdhnocms02:/home/bigdata/installsoft/
scp -r /home/bigdata/installsoft/spark-1.6.3/ cdhnocms02:/home/bigdata/installsoft/
</code></pre>
<p>直接将环境变量文件发送到其他节点，或者在其他节点上一一配置环境变量</p>
<h3><a id="Spark_73"></a>三、Spark集群启动和测试</h3>
<h5><a id="_75"></a>启动</h5>
<p>在cdhnocms01节点上执行/home/bigdata/installsoft/spark-1.6.3/sbin/start-all.sh</p>
<p>在cdhnocms02节点上执行/home/bigdata/installsoft/spark-1.6.3/sbin/start-master.sh</p>
<p>此时使用jps查看三台机器进程，如下表</p>

<table>
<thead>
<tr>
<th>cdhnocms01</th>
<th>cdhnocms02</th>
<th>cdhnocms03</th>
</tr>
</thead>
<tbody>
<tr>
<td>Master、Worker</td>
<td>Master、Worker</td>
<td>Worker</td>
</tr>
</tbody>
</table><p>注意：虽然配置了环境变量，但由于名称相同，<a href="http://xn--start-all-k97no33enog47u1vfqwfizfea873phv2ekdag6978g.sh" rel="nofollow">如果直接在任意目录直接执行start-all.sh</a>，启动的将会是hadoop的相关进程。解决办法：修改启动脚本的文件名。</p>
<h5><a id="_89"></a>测试</h5>
<p>运行官方自带的例子</p>
<pre><code>spark-submit \
--class org.apache.spark.examples.SparkPi \
--master spark://cdhnocms01:7077 \
--executor-memory 1G \
--total-executor-cores 2 \
/home/bigdata/installsoft/spark-1.6.3/lib/spark-examples-1.6.3-hadoop2.6.0.jar \
100
</code></pre>
<p>运行完成后可以在命令行中找到结果</p>
<p><img src="https://sparkday01-1253629415.cos.ap-guangzhou.myqcloud.com/1542621376664.png" alt="1542621376664"></p>
<p>在web监控页面：cdhnocms02:8080上可以查看任务状态</p>
<p><img src="https://sparkday01-1253629415.cos.ap-guangzhou.myqcloud.com/1542621318493.png" alt="1542621318493"></p>
<h3><a id="Spark_Shell_111"></a>四、Spark Shell</h3>
<h5><a id="spark_shell_113"></a>spark shell简介</h5>
<p>spark-shell是Spark自带的交互式Shell程序，方便用户进行交互式编程，用户可以在该命令行下用scala编写spark程序。</p>
<h5><a id="spark_shell__117"></a>spark shell 启动</h5>
<pre><code>spark-shell \
--master spark://cdhnocms01:7077 \
--executor-memory 1G \
--total-executor-cores 2
</code></pre>
<p>参数说明</p>
<blockquote>
<p>–master spark://cdhnocms02:7077 指定Master的地址</p>
<p>–executor-memory 1G 指定每个worker可用内存为1G</p>
<p>–total-executor-cores 2 指定整个集群使用的cup核数为2个</p>
</blockquote>
<p>注意</p>
<blockquote>
<p>如果启动spark shell时没有指定master地址，但是也可以正常启动spark shell和执行spark shell中的程序，其实是启动了spark的local模式，该模式仅在本机启动一个进程，没有与集群建立联系。</p>
</blockquote>
<p>启动spark shell后，可以注意到在控制台有如下两条语句：</p>
<p><img src="https://sparkday01-1253629415.cos.ap-guangzhou.myqcloud.com/1542624224309.png" alt="1542624224309"></p>
<p><img src="https://sparkday01-1253629415.cos.ap-guangzhou.myqcloud.com/1542624246881.png" alt="1542624246881"></p>
<p>意思是Spark Shell中已经默认将SparkContext类初始化为对象sc，SQLContext类初始化为对象sqlContext。用户代码如果需要用到，则直接使用对应的对象名即可即可。</p>
<h5><a id="spark_shellwordcount_146"></a>在spark shell中编写wordcount程序</h5>
<ol>
<li>上传数据文件到hdfs://cdhnocms01:8020/userdata/wc.txt</li>
<li>val file = sc.textFile(“hdfs://cdhnocms01:8020/userdata/wc.txt”)</li>
<li>val words = file.flatMap(_.split(" "))</li>
<li>val map = words.map((_,1))</li>
<li>val result = map.reduceByKey(<em>+</em>)</li>
<li>接下来可以直接通过result.collect将结果打印到控制台，或者result.saveAsTextFile(“hdfs://cdhnocms01:8020/out/20181119”)，将结果文件存储在hdfs后查看结果</li>
</ol>
<h3><a id="IDEAWordCount_155"></a>五、IDEA中编写WordCount</h3>
<h5><a id="pomxml_157"></a>pom.xml</h5>
<pre><code>&lt;?xml version="1.0" encoding="UTF-8"?&gt;
&lt;project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd"&gt;
    &lt;modelVersion&gt;4.0.0&lt;/modelVersion&gt;

    &lt;groupId&gt;com&lt;/groupId&gt;
    &lt;artifactId&gt;spark&lt;/artifactId&gt;
    &lt;version&gt;1.0-SNAPSHOT&lt;/version&gt;

    &lt;dependencies&gt;
        &lt;dependency&gt;
            &lt;groupId&gt;org.apache.spark&lt;/groupId&gt;
            &lt;artifactId&gt;spark-core_2.11&lt;/artifactId&gt;
            &lt;version&gt;1.6.3&lt;/version&gt;
        &lt;/dependency&gt;
    &lt;/dependencies&gt;

    &lt;build&gt;
        &lt;sourceDirectory&gt;src/main/day01&lt;/sourceDirectory&gt;
        &lt;plugins&gt;

            &lt;plugin&gt;
                &lt;groupId&gt;org.scala-tools&lt;/groupId&gt;
                &lt;artifactId&gt;maven-scala-plugin&lt;/artifactId&gt;
                &lt;version&gt;2.15.2&lt;/version&gt;
                &lt;executions&gt;
                    &lt;execution&gt;
                        &lt;goals&gt;
                            &lt;goal&gt;compile&lt;/goal&gt;
                            &lt;goal&gt;testCompile&lt;/goal&gt;
                        &lt;/goals&gt;
                    &lt;/execution&gt;
                &lt;/executions&gt;
            &lt;/plugin&gt;

            &lt;plugin&gt;
                &lt;artifactId&gt;maven-compiler-plugin&lt;/artifactId&gt;
                &lt;version&gt;3.6.0&lt;/version&gt;
                &lt;configuration&gt;
                    &lt;source&gt;1.8&lt;/source&gt;
                    &lt;target&gt;1.8&lt;/target&gt;
                &lt;/configuration&gt;
            &lt;/plugin&gt;

            &lt;plugin&gt;
                &lt;groupId&gt;org.apache.maven.plugins&lt;/groupId&gt;
                &lt;artifactId&gt;maven-surefire-plugin&lt;/artifactId&gt;
                &lt;version&gt;2.19&lt;/version&gt;
                &lt;configuration&gt;
                    &lt;skip&gt;true&lt;/skip&gt;
                &lt;/configuration&gt;
            &lt;/plugin&gt;

        &lt;/plugins&gt;
    &lt;/build&gt;

&lt;/project&gt;
</code></pre>
<h5><a id="_220"></a>编写代码</h5>
<pre><code>import org.apache.spark.rdd.RDD
import org.apache.spark.{SparkConf, SparkContext}

object SparkWordCount_scala {

  def main(args: Array[String]): Unit = {
    // 1.获取spark的conf
    // 本地运行
    val conf = new SparkConf().setAppName("spark_wordcount_scala").setMaster("local[2]")
    // 提交到集群中运行
    //val conf = new SparkConf().setAppName("spark_wordcount_scala").setMaster("spark://cdhnocms01:7077")
    // 2.根据conf对象获取sparkContext(spark的上下文)
    val sc = new SparkContext(conf)
    // 读取hdfs中的数据
    sc.hadoopConfiguration.addResource("core-site.xml")
    sc.hadoopConfiguration.addResource("hdfs-site.xml")
   
    //第二种读取hdfs的HA的文件数据
    //    sc.hadoopConfiguration.set("")

    // 3.使用sc进行操作
    // 读取数据源
    val words:RDD[String] = sc.textFile(args(0))
    val res:RDD[(String,Int)] = words.flatMap(_.split(" ")).map((_,1)).groupBy(_._1).mapValues(_.size)
    // 打印
    res.foreach(f=&gt;println(f))
    //4. 关闭sc
    sc.stop()


  }
}
</code></pre>
<p>可以直接在idea中配置好输入参数后运行，可以的到结果</p>
<h5><a id="Linux_259"></a>打包到Linux服务器中运行</h5>
<p>使用Maven打包后，将jar包上传至Linux中，运行命令：</p>
<pre><code>spark-submit \
--class SparkWordCount_scala \
--master spark://cdhnocms01:7077 \
/home/bigdata/userjars/spark-1.0-SNAPSHOT.jar \
hdfs://bigdata/userdata/wc.txt
</code></pre>
<p>代码的输出结果(在网页监控端口任务的stdout中查看)：</p>
<p><img src="https://sparkday01-1253629415.cos.ap-guangzhou.myqcloud.com/1542625835062.png" alt="1542625835062"></p>
<h3><a id="RDD_275"></a>六、弹性分布式数据集RDD</h3>
<h5><a id="RDD_277"></a>RDD简介</h5>
<p>​	RDD(Resilient Distributed Dataset)，分布式数据集，是Spark中最基本的数据抽象，它代表一个<strong>不可变、可分区</strong>、里面的<strong>元素可并行计算</strong>的集合。RDD具有数据流模型的特点：<strong>自动容错、位置感知性调度和可伸缩性</strong>。RDD允许用户在执行多个查询时显式地将工作集缓存在内存中，后续的查询能够重用工作集，这极大地提升了查询速度。</p>
<h5><a id="RDD_281"></a>RDD属性</h5>
<pre><code>- A list of partitions
- A function for computing each split
- A list of dependencies on other RDDs
- Optionally, a Partitioner for key-value RDDs (e.g. to say that the RDD is hash-partitioned)
- Optionally, a list of preferred locations to compute each split on (e.g. block locations for
 an HDFS file)
</code></pre>
<p>属性详解：</p>
<ol>
<li>一组分片（Partition），即数据集的基本组成单位。对于RDD来说，每个分片都会被一个计算任务处理，并决定并行计算的粒度。用户可以在创建RDD时指定RDD的分片个数，如果没有指定，那么就会采用默认值。默认值就是程序所分配到的CPU Core的数目。</li>
<li>一个计算每个分区的函数。Spark中RDD的计算是以分片为单位的，每个RDD都会实现compute函数以达到这个目的。compute函数会对迭代器进行复合，不需要保存每次计算的结果。</li>
<li>RDD之间的依赖关系。RDD的每次转换都会生成一个新的RDD，所以RDD之间就会形成类似于流水线一样的前后依赖关系。在部分分区数据丢失时，Spark可以通过这个依赖关系重新计算丢失的分区数据，而不是对RDD的所有分区进行重新计算。</li>
<li>一个Partitioner，即RDD的分片函数。当前Spark中实现了两种类型的分片函数，一个是基于哈希的HashPartitioner，另外一个是基于范围的RangePartitioner。只有对于key-value的RDD，才会有Partitioner，非key-value的RDD的Parititioner的值是None。Partitioner函数不但决定了RDD本身的分片数量，也决定了parent RDD Shuffle输出时的分片数量。</li>
<li>一个列表，存储存取每个Partition的优先位置（preferred location）。对于一个HDFS文件来说，这个列表保存的就是每个Partition所在的块的位置。按照“移动数据不如移动计算”的理念，Spark在进行任务调度的时候，会尽可能地将计算任务分配到其所要处理数据块的存储位置。</li>
</ol>
<h5><a id="RDD_300"></a>RDD创建</h5>
<ol>
<li>
<p>由一个已经存在的Scala集合创建(Array、List、Seq等)</p>
<blockquote>
<p>sc.parallelize(args(0),args(1))</p>
<p>第一个参数代表已存在的Scala集合，第二个参数代表分片个数，如果不指定则会采用默认值—分配的CPU core数量</p>
</blockquote>
</li>
<li>
<p>由外部存储系统的数据集创建，包括本地的文件系统，还有所有Hadoop支持的数据集，比如HDFS、Cassandra、HBase等</p>
<blockquote>
<p>sc.textFile(“hdfs://cdhnocms01:8020/userdata/wc.txt”)</p>
</blockquote>
</li>
</ol>
<h3><a id="RDDAPI_312"></a>七、RDD编程API</h3>
<h5><a id="Transformation_314"></a>Transformation</h5>
<p>​	RDD中的所有转换都是延迟加载的，也就是说，它们并不会直接计算结果。相反的，它们只是记住这些应用到基础数据集（例如一个文件）上的转换动作。只有当发生一个要求返回结果给Driver的动作时，这些转换才会真正运行。</p>

<table>
<thead>
<tr>
<th align="center">Transformation</th>
<th align="center">含义</th>
</tr>
</thead>
<tbody>
<tr>
<td align="center"><strong>map</strong>(func)</td>
<td align="center">返回一个新的RDD，该RDD由每一个输入元素经过func函数转换后组成</td>
</tr>
<tr>
<td align="center"><strong>filter</strong>(func)</td>
<td align="center">返回一个新的RDD，该RDD由经过func函数计算后返回值为true的输入元素组成</td>
</tr>
<tr>
<td align="center"><strong>flatMap</strong>(func)</td>
<td align="center">类似于map，但是每一个输入元素可以被映射为0或多个输出元素（所以func应该返回一个序列，而不是单一元素）</td>
</tr>
<tr>
<td align="center"><strong>mapPartitions</strong>(func)</td>
<td align="center">类似于map，但独立地在RDD的每一个分片上运行，因此在类型为T的RDD上运行时，func的函数类型必须是Iterator[T]=&gt; Iterator[U]</td>
</tr>
<tr>
<td align="center"><strong>mapPartitionsWithIndex</strong>(func)</td>
<td align="center">类似于mapPartitions，但func带有一个整数参数表示分片的索引值，因此在类型为T的RDD上运行时，func的函数类型必须是(Int, Iterator[T]) =&gt; Iterator[U]</td>
</tr>
<tr>
<td align="center"><strong>sample</strong>(withReplacement,fraction, seed)</td>
<td align="center">根据fraction指定的比例对数据进行采样，可以选择是否使用随机数进行替换，seed用于指定随机数生成器种子</td>
</tr>
<tr>
<td align="center"><strong>union</strong>(otherDataset)</td>
<td align="center">对源RDD和参数RDD求并集后返回一个新的RDD</td>
</tr>
<tr>
<td align="center"><strong>intersection</strong>(otherDataset)</td>
<td align="center">对源RDD和参数RDD求交集后返回一个新的RDD</td>
</tr>
<tr>
<td align="center"><strong>distinct</strong>([numTasks]))</td>
<td align="center">对源RDD进行去重后返回一个新的RDD</td>
</tr>
<tr>
<td align="center"><strong>groupByKey</strong>([numTasks])</td>
<td align="center">在一个(K,V)的RDD上调用，返回一个(K, Iterator[V])的RDD</td>
</tr>
<tr>
<td align="center"><strong>reduceByKey</strong>(func,[numTasks])</td>
<td align="center">在一个(K,V)的RDD上调用，返回一个(K,V)的RDD，使用指定的reduce函数，将相同key的值聚合到一起，与groupByKey类似，reduce任务的个数可以通过第二个可选的参数来设置</td>
</tr>
<tr>
<td align="center"><strong>aggregateByKey</strong>(zeroValue)(seqOp,combOp, [numTasks])</td>
<td align="center"></td>
</tr>
<tr>
<td align="center"><strong>sortByKey</strong>([ascending],   [numTasks])</td>
<td align="center">在一个(K,V)的RDD上调用，K必须实现Ordered接口，返回一个按照key进行排序的(K,V)的RDD</td>
</tr>
<tr>
<td align="center"><strong>sortBy</strong>(func,[ascending],[numTasks])</td>
<td align="center">与sortByKey类似，但是可以指定根据什么排序</td>
</tr>
<tr>
<td align="center"><strong>join</strong>(otherDataset,[numTasks])</td>
<td align="center">在类型为(K,V)和(K,W)的RDD上调用，返回一个相同key对应的所有元素对在一起的(K,(V,W))的RDD</td>
</tr>
<tr>
<td align="center"><strong>cogroup</strong>(otherDataset,[numTasks])</td>
<td align="center">在类型为(K,V)和(K,W)的RDD上调用，返回一个(K,(Iterable,Iterable))类型的RDD</td>
</tr>
<tr>
<td align="center"><strong>cartesian</strong>(otherDataset)</td>
<td align="center">笛卡尔积</td>
</tr>
<tr>
<td align="center"><strong>coalesce</strong>(numPartitions)</td>
<td align="center">重新分区</td>
</tr>
<tr>
<td align="center"><strong>repartition</strong>(numPartitions)</td>
<td align="center">重新分区</td>
</tr>
<tr>
<td align="center"><strong>repartitionAndSortWithinPartitions</strong>(partitioner)</td>
<td align="center">重新分区</td>
</tr>
</tbody>
</table><h5><a id="Action_341"></a>Action</h5>

<table>
<thead>
<tr>
<th align="center">Action</th>
<th align="center">含义</th>
</tr>
</thead>
<tbody>
<tr>
<td align="center"><strong>reduce</strong>(<em>func</em>)</td>
<td align="center">通过func函数聚集RDD中的所有元素，这个功能必须是可交换且可并联的</td>
</tr>
<tr>
<td align="center"><strong>collect</strong>()</td>
<td align="center">在驱动程序中，以数组的形式返回数据集的所有元素</td>
</tr>
<tr>
<td align="center"><strong>count</strong>()</td>
<td align="center">返回RDD的元素个数</td>
</tr>
<tr>
<td align="center"><strong>first</strong>()</td>
<td align="center">返回RDD的第一个元素（类似于take(1)）</td>
</tr>
<tr>
<td align="center"><strong>take</strong>(<em>n</em>)</td>
<td align="center">返回一个由数据集的前n个元素组成的数组</td>
</tr>
<tr>
<td align="center"><strong>takeSample</strong>(<em>withReplacement</em>,<em>num</em>, [<em>seed</em>])</td>
<td align="center">返回一个数组，该数组由从数据集中随机采样的num个元素组成，可以选择是否用随机数替换不足的部分，seed用于指定随机数生成器种子</td>
</tr>
<tr>
<td align="center"><strong>takeOrdered</strong>(<em>n</em>, <em>[ordering]</em>)</td>
<td align="center">takeOrdered和top类似，只不过以和top相反的顺序返回元素</td>
</tr>
<tr>
<td align="center"><strong>saveAsTextFile</strong>(<em>path</em>)</td>
<td align="center">将数据集的元素以textfile的形式保存到HDFS文件系统或者其他支持的文件系统，对于每个元素，Spark将会调用toString方法，将它装换为文件中的文本</td>
</tr>
<tr>
<td align="center"><strong>saveAsSequenceFile</strong>(<em>path</em>)</td>
<td align="center">将数据集中的元素以Hadoopsequencefile的格式保存到指定的目录下，可以使HDFS或者其他Hadoop支持的文件系统。</td>
</tr>
<tr>
<td align="center"><strong>saveAsObjectFile</strong>(<em>path</em>)</td>
<td align="center"></td>
</tr>
<tr>
<td align="center"><strong>countByKey</strong>()</td>
<td align="center">针对(K,V)类型的RDD，返回一个(K,Int)的map，表示每一个key对应的元素个数。</td>
</tr>
<tr>
<td align="center"><strong>foreach</strong>(<em>func</em>)</td>
<td align="center">在数据集的每一个元素上，运行函数func进行更新。</td>
</tr>
</tbody>
</table><h3><a id="_358"></a>八、算子进阶</h3>
<ol>
<li>
<p>map/mapPartitions</p>
<pre><code>val rdd1 = sc.parallelize(List(1,2,3,4,5,6), 2)
val rdd2 = rdd1.map(_ * 10)
val rdd2 = rdd1.mapPartitions(_.map(_ * 10))
rdd2.collect
</code></pre>
<blockquote>
<p>Array[Int] = Array(10, 20, 30, 40, 50, 60)</p>
<p>Array[Int] = Array(10, 20, 30, 40, 50, 60)</p>
</blockquote>
<p>map算子是将rdd中的每一个元素拿出来进行操作</p>
<p>mapPartitions算子是将一整个分片中的数据拿出来操作，所以需要继续对每一个分片中各个数据拿出来操作</p>
<pre><code>rdd1.mapPartitions(_.toList.reverse.iterator).collect
</code></pre>
<p>此操作是将每一个分片中的数据翻转</p>
</li>
<li>
<p>mapWith</p>
<p>参数列表：(constructA: Int =&gt; A, preservesPartitioning: Boolean = false)(f: (T, A) =&gt; U)</p>
<p>其中preservesPartitioning指定是否需要使用父RDD的分片</p>
<pre><code>rdd1.mapWith(i =&gt; i*10)((a, b) =&gt; b+2).collect  
</code></pre>
<blockquote>
<p>Array[Int] = Array(2, 2, 2, 12, 12, 12)</p>
</blockquote>
<p>mapWith算子是将rdd的分片下标取出进行操作元组(a,b)中a指数据，b指该数据的下标</p>
</li>
<li>
<p>flatMapWith</p>
<p>参数列表：(constructA: Int =&gt; A, preservesPartitioning: Boolean = false)(f: (T, A) =&gt; Seq[U])</p>
<pre><code>rdd1.flatMapWith(i =&gt; i, true)((x, y) =&gt; List((y, x))).collect
</code></pre>
<blockquote>
<p>Array[(Int, Int)] = Array((0,1), (0,2), (0,3), (1,4), (1,5), (1,6))</p>
</blockquote>
<p>flatMapWith算子类似于mapWith，但是每一个输入元素可以被映射为0或多个输出元素</p>
</li>
<li>
<p>mapPartitionsWithIndex</p>
<p>参数列表：(f: (Int, Iterator[T]) =&gt; Iterator[U], preservesPartitioning: Boolean = false)</p>
<pre><code>val func = (index: Int, iter: Iterator[(Int)]) =&gt; {
  iter.toList.map(x =&gt; "[partID:" +  index + ", val: " + x + "]").iterator
}
val rdd1 = sc.parallelize(List(1,2,3,4,5,6,7,8,9), 2)
rdd1.mapPartitionsWithIndex(func).collect
</code></pre>
<blockquote>
<p>Array[String] = Array([partID:0, val: 1], [partID:0, val: 2], [partID:0, val: 3], [partID:0, val: 4], [partID:1, val: 5], [partID:1, val: 6], [partID:1, val: 7], [partID:1, val: 8], [partID:1, val: 9])</p>
</blockquote>
</li>
<li>
<p>aggregate</p>
<p>参数列表：(zeroValue: U)(seqOp: (U, T) =&gt; U, combOp: (U, U) =&gt; U): U</p>
<p>zeroValue：初始值；seqOp：单个分区的合并操作；combOp：所有分区的汇总操作</p>
<pre><code>def func1(index: Int, iter: Iterator[(Int)]) : Iterator[String] = {
  iter.toList.map(x =&gt; "[partID:" +  index + ", val: " + x + "]").iterator
}
val rdd1 = sc.parallelize(List(1,2,3,4,5,6,7,8,9), 2)
rdd1.mapPartitionsWithIndex(func1).collect
</code></pre>
<blockquote>
<p>Array[String] = Array([partID:0, val: 1], [partID:0, val: 2], [partID:0, val: 3], [partID:0, val: 4], [partID:1, val: 5], [partID:1, val: 6], [partID:1, val: 7], [partID:1, val: 8], [partID:1, val: 9])</p>
</blockquote>
<pre><code>rdd1.aggregate(0)(math.max(_, _), _ + _)
rdd1.aggregate(5)(math.max(_, _), _ + _)
</code></pre>
<blockquote>
<p>Int = 13  //首先在两个分区中各自获得最大值4、9，相加等于13</p>
<p>Int = 19  //首先在两个分区中各自获得最大值5、9，相加等于14，再加上初始值5等于19</p>
</blockquote>
<hr>
<pre><code>val rdd2 = sc.parallelize(List("a","b","c","d","e","f"),2)
def func2(index: Int, iter: Iterator[(String)]) : Iterator[String] = {
  iter.toList.map(x =&gt; "[partID:" +  index + ", val: " + x + "]").iterator
}
rdd2.mapPartitionsWithIndex(func2).collect
</code></pre>
<blockquote>
<p>Array[String] = Array([partID:0, val: a], [partID:0, val: b], [partID:0, val: c], [partID:1, val: d], [partID:1, val: e], [partID:1, val: f])</p>
</blockquote>
<pre><code>rdd2.aggregate("")(_ + _, _ + _)
rdd2.aggregate("=")(_ + _, _ + _)
</code></pre>
<blockquote>
<p>String = abcdef 或 String = defabc  //字符串拼接操作，在两个分区中先各自拼接，最终的拼接时的顺序是哪个分区先完成就哪个分区在前</p>
<p>String = ==def=abc 或 String = ==abc=def  //同上，但是在拼接前先加上初始值"="</p>
</blockquote>
<hr>
<pre><code>val rdd3 = sc.parallelize(List("12","23","345","4567"),2)
rdd3.aggregate("")((x,y) =&gt; math.max(x.length, y.length).toString, (x,y) =&gt; x + y)
</code></pre>
<blockquote>
<p>String = 42 或 String = 24</p>
<p>前一个参数列表 (x,y):第一次时分别代表初始值和分区中的第一个值，以后是分别代表上一次结果的值和分区中新的值</p>
<p>max(0,2) = 2, max(2,2) = 2</p>
<p>max(0,3) = 3, max(3,4) = 4</p>
<p>后一个参数列表(x,y):第一次是代表初始值与第一个分区的结果拼接，以后代表上一次的结果和新的分区的结果拼接</p>
<p>同上，由于不同分区的完成时间不同，结果会出现两种情况</p>
</blockquote>
<hr>
<pre><code>val rdd4 = sc.parallelize(List("12","23","345",""),2)
rdd4.aggregate("")((x,y) =&gt; math.min(x.length, y.length).toString, (x,y) =&gt; x + y)
</code></pre>
<blockquote>
<p>String = 10 或 String = 01</p>
<p>关键在于"".length=0，“0”.length=1</p>
</blockquote>
<hr>
<pre><code>val rdd5 = sc.parallelize(List("12","23","","345"),2)
rdd5.aggregate("")((x,y) =&gt; math.min(x.length, y.length).toString, (x,y) =&gt; x + y)
</code></pre>
<blockquote>
<p>String = 11</p>
</blockquote>
</li>
<li>
<p>aggregateByKey</p>
<p>相同的key进行操作</p>
<p>参数列表：(zeroValue: U, partitioner: Partitioner)(seqOp: (U, V) =&gt; U, combOp: (U, U) =&gt; U): RDD[(K, U)</p>
<pre><code>val pairRDD = sc.parallelize(List(("mouse", 2),("cat",2), ("cat", 5), ("mouse", 4),("cat", 12), ("dog", 12)), 2)
def func2(index: Int, iter: Iterator[(String, Int)]) : Iterator[String] = {
  iter.toList.map(x =&gt; "[partID:" +  index + ", val: " + x + "]").iterator
}
pairRDD.mapPartitionsWithIndex(func2).collect
</code></pre>
<blockquote>
<p>Array[String] = Array([partID:0, val: (mouse,2)], [partID:0, val: (cat,2)], [partID:0, val: (cat,5)], [partID:1, val: (mouse,4)], [partID:1, val: (cat,12)], [partID:1, val: (dog,12)])</p>
</blockquote>
<pre><code>pairRDD.aggregateByKey(0)(math.max(_, _), _ + _).collect
pairRDD.aggregateByKey(100)(math.max(_, _), _ + _).collect
</code></pre>
<blockquote>
<p>Array[(String, Int)] = Array((dog,12), (cat,17), (mouse,6))  // dog：12；cat：5+12；mouse：2+4</p>
<p>Array[(String, Int)] = Array((dog,100), (cat,200), (mouse,200))  // dog：100；cat：100+100；mouse：100+100</p>
</blockquote>
</li>
<li>
<p>combineByKey</p>
<p>参数列表：(createCombiner: V =&gt; C, mergeValue: (C, V) =&gt; C, mergeCombiners: (C, C) =&gt; C)</p>
<pre><code>val rdd1 = sc.textFile("hdfs://cdhnocms01:8020/userdata/wc.txt").flatMap(_.split(" ")).map((_, 1))
rdd1.combineByKey(x =&gt; x, (a: Int, b: Int) =&gt; a + b, (m: Int, n: Int) =&gt; m + n).collect
</code></pre>
<blockquote>
<p>Array[(String, Int)] = Array((word,2), (hello,2), (sql,1), (spark,3), (hadoop,2), (hi,1))</p>
</blockquote>
<pre><code>rdd1.combineByKey(x =&gt; x + 10, (a: Int, b: Int) =&gt; a + b, (m: Int, n: Int) =&gt; m + n).collect
</code></pre>
<blockquote>
<p>Array[(String, Int)] = Array((word,12), (hello,12), (sql,11), (spark,13), (hadoop,12), (hi,11))</p>
<p>// 对每一个value加10</p>
</blockquote>
<pre><code>val rdd4 = sc.parallelize(List("dog","cat","gnu","salmon","rabbit","turkey","wolf","bear","bee"), 3)
val rdd5 = sc.parallelize(List(1,1,2,2,2,1,2,2,2), 3)
val rdd6 = rdd5.zip(rdd4)
rdd6.combineByKey(List(_), (x: List[String], y: String) =&gt; x :+ y, (m: List[String], n: List[String]) =&gt; m ++ n).collect
</code></pre>
<blockquote>
<p>Array[(Int, List[String])] = Array((1,List(dog, cat, turkey)), (2,List(salmon, rabbit, wolf, bear, bee, gnu)))</p>
</blockquote>
</li>
<li>
<p>countByKey / countByValue</p>
<pre><code>val rdd1 = sc.parallelize(List(("a", 1), ("b", 2), ("b", 2), ("c", 2), ("c", 1)))
rdd1.countByKey
rdd1.countByValue
</code></pre>
<blockquote>
<p>scala.collection.Map[String,Long] = Map(b -&gt; 2, a -&gt; 1, c -&gt; 2)  // 统计相同key出现的次数</p>
<p>scala.collection.Map[(String, Int),Long] = Map((b,2) -&gt; 2, (c,2) -&gt; 1, (a,1) -&gt; 1, (c,1) -&gt; 1)  // 统计相同元素出现的次数</p>
</blockquote>
</li>
<li>
<p>filterByRange</p>
<pre><code>val rdd1 = sc.parallelize(List(("e", 5), ("c", 3), ("d", 4), ("c", 2), ("a", 1)))
val rdd2 = rdd1.filterByRange("c", "d")
rdd2.collect
</code></pre>
<blockquote>
<p>Array[(String, Int)] = Array((c,3), (d,4), (c,2))</p>
<p>// 对给定的范围进行过滤</p>
</blockquote>
</li>
<li>
<p>flatMapValues</p>
<pre><code>val rdd3 = sc.parallelize(List(("a", "1 2"), ("b", "3 4")))
rdd3.flatMapValues(_.split(" ")).collect
</code></pre>
<blockquote>
<p>Array[(String, String)] = Array((a,1), (a,2), (b,3), (b,4))</p>
</blockquote>
<p>对value进行相应的操作后压频</p>
</li>
<li>
<p>foldByKey</p>
<pre><code>val rdd1 = sc.parallelize(List("dog", "wolf", "cat", "bear"), 2)
val rdd2 = rdd1.map(x =&gt; (x.length, x))
val rdd3 = rdd2.foldByKey("")(_+_)
rdd3.collect
</code></pre>
<blockquote>
<p>Array[(Int, String)] = Array((4,bearwolf), (3,dogcat))</p>
</blockquote>
<pre><code>val rdd = sc.textFile("hdfs://cdhnocms01:8020/userdata/wc.txt").flatMap(_.split(" ")).map((_, 1))
rdd.foldByKey(0)(_+_).collect
</code></pre>
<blockquote>
<p>Array[(String, Int)] = Array((word,2), (hello,2), (sql,1), (spark,3), (hadoop,2), (hi,1))</p>
</blockquote>
</li>
<li>
<p>foreachPartition</p>
<pre><code>val rdd1 = sc.parallelize(List(1, 2, 3, 4, 5, 6, 7, 8, 9), 3)
rdd1.foreachPartition(x =&gt; println(x.reduce(_ + _)))
</code></pre>
</li>
<li>
<p>keyBy</p>
<pre><code>val rdd1 = sc.parallelize(List("dog", "salmon", "salmon", "rat", "elephant"), 3)
val rdd2 = rdd1.keyBy(_.length)
rdd2.collect
</code></pre>
<blockquote>
<p>Array[(Int, String)] = Array((3,dog), (6,salmon), (6,salmon), (3,rat), (8,elephant))</p>
<p>// 将结果作为key-value的key</p>
</blockquote>
</li>
<li>
<p>keys / values</p>
<pre><code>val rdd1 = sc.parallelize(List("dog", "tiger", "lion", "cat", "panther", "eagle"), 2)
val rdd2 = rdd1.map(x =&gt; (x.length, x))
rdd2.keys.collect
rdd2.values.collect
</code></pre>
<blockquote>
<p>Array[Int] = Array(3, 5, 4, 3, 7, 5)  // 获得key</p>
<p>Array[String] = Array(dog, tiger, lion, cat, panther, eagle)  // 获得value</p>
</blockquote>
</li>
<li>
<p>collectAsMap</p>
<pre><code>val rdd = sc.parallelize(List(("a", 1), ("b", 2)))
rdd.collectAsMap
</code></pre>
<blockquote>
<p>scala.collection.Map[String,Int] = Map(b -&gt; 2, a -&gt; 1)</p>
</blockquote>
</li>
<li>
<p>repartition, coalesce, partitionBy</p>
<p>重新分区</p>
<pre><code>val rdd1 = sc.parallelize(1 to 10, 3)
val rdd2 = rdd1.coalesce(2, false)
rdd2.partitions.length
</code></pre>
</li>
<li>
<p>checkpoint</p>
<pre><code>sc.setCheckpointDir("hdfs://cdhnocms01:8020/userdata/cp")
val rdd = sc.textFile("hdfs://cdhnocms01:8020/userdata/wc.txt").flatMap(_.split(" ")).map((_, 1)).reduceByKey(_+_)
rdd.checkpoint
rdd.isCheckpointed
rdd.count
rdd.isCheckpointed
rdd.getCheckpointFile
</code></pre>
</li>
</ol>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>