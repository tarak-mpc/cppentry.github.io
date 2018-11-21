---
layout:     post
title:      Local模式下开发第一个Spark程序并运行于集群环境
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-size:14px;">第一阶段（<span style="font-family:Calibri;">1-3</span><span style="font-family:'宋体';">月）：会从浅入深，基于大量案例实战，深度剖析和讲解</span><span style="font-family:Calibri;">Spark</span><span style="font-family:'宋体';">，并且会包含完全从企业真实复杂业务需求中抽取出的案例实战。课程会涵盖</span><span style="font-family:Calibri;">Scala</span><span style="font-family:'宋体';">编程详解、</span><span style="font-family:Calibri;">Spark</span><span style="font-family:'宋体';">核心编程、</span><span style="font-family:Calibri;">Spark SQL</span><span style="font-family:'宋体';">和</span><span style="font-family:Calibri;">Spark Streaming</span><span style="font-family:'宋体';">、</span><span style="font-family:Calibri;">Spark GraphX</span><span style="font-family:'宋体';">、</span><span style="font-family:Calibri;">SparkR</span><span style="font-family:'宋体';">、</span><span style="font-family:Calibri;">Machine Learning</span><span style="font-family:'宋体';">、</span><span style="font-family:Calibri;">Spark</span><span style="font-family:'宋体';">内核以及源码剖析、性能调优、企业级案例实战等部分</span></span></p>
<p><span style="font-size:14px;">第二阶段（<span style="font-family:Calibri;">Spark</span><span style="font-family:'宋体';">超大规模大数据案例实战）：使用了</span><span style="font-family:Calibri;">Spark</span><span style="font-family:'宋体';">技术生态栈中的</span><span style="font-family:Calibri;">Spark Core</span><span style="font-family:'宋体';">、</span><span style="font-family:Calibri;">Spark SQL</span><span style="font-family:'宋体';">、</span><span style="font-family:Calibri;">Spark Streaming</span><span style="font-family:'宋体';">、</span><span style="font-family:Calibri;">SparkR</span><span style="font-family:'宋体';">、</span><span style="font-family:Calibri;">Machine Learning</span><span style="font-family:'宋体';">，进行离线计算和实时计算业务模块的开发、数据的关联性分析、用户行为模式和特征的训练与应用、用户网络的社区发现、用户影响力、能量传播、标签传播、标签推理、人群划分、年龄段预测、商品交易时序跳转</span></span></p>
<p><span style="font-family:'宋体';color:#ff0000;"><span style="font-size:14px;"><strong>作业：实现广告点击排序（尾部）</strong></span></span></p>
<p>本期内容：</p>
<p>1 <span style="font-family:'宋体';">使用</span><span style="font-family:Calibri;">IDE</span><span style="font-family:'宋体';">开发</span><span style="font-family:Calibri;">Spark</span><span style="font-family:'宋体';">分析</span></p>
<p>2 <span style="font-family:'宋体';">使用</span><span style="font-family:Calibri;">IDE</span><span style="font-family:'宋体';">开发</span><span style="font-family:Calibri;">Spark</span><span style="font-family:'宋体';">实战</span></p>
<p>3<span style="font-family:'宋体';">，使用</span><span style="font-family:Calibri;">IDE</span><span style="font-family:'宋体';">开发</span><span style="font-family:Calibri;">Spark</span><span style="font-family:'宋体';">的</span><span style="font-family:Calibri;">Local</span><span style="font-family:'宋体';">和</span><span style="font-family:Calibri;">Cluster</span></p>
<p><span style="font-family:Calibri;"></span></p>
<p><strong>开发<span style="font-family:Calibri;">Spark</span><span style="font-family:'宋体';">（</span><span style="font-family:Calibri;">IDE</span><span style="font-family:'宋体';">、</span><span style="font-family:Calibri;">eclipse</span><span style="font-family:'宋体';">）</span></strong></p>
<p>1、下载<span style="font-family:Calibri;">IDE  scala-ide.org(</span><span style="font-family:'宋体';">本地</span><span style="font-family:Calibri;">win</span><span style="font-family:'宋体';">上开发</span><span style="font-family:Calibri;">)</span></p>
<p>安装<span style="font-family:Calibri;">Java</span><span style="font-family:'宋体';">和</span><span style="font-family:Calibri;">Scala-SDK</span></p>
<p>2、 设置<span style="font-family:Calibri;">eclipse</span></p>
<p>导入Spark依赖包jar文件（spark-assembly-1.6.0-hadoop2.6.0）这边Spark版本为1.6.0，Hadoop版本为2.6.0</p>
<p><img src="https://img-blog.csdn.net/20160110090745833?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p><br></p>
<pre><code class="language-java"> /**
   * 第一步：创建Spark的配置对象SparkConf，设置Spark程序的运行时
   * 的配置信息，例如通过SetMaster来设置程序的连接Spark集群的Master的
   * URL，若设置为local，则代表Spark程序在本地运行，特别适合于
   * 机器配置特别差的初学者。
   * 
   */
  val conf = new SparkConf() 
  conf.setAppName("Wow,My First Spark App!")
  //conf.setMaster("local")  //若将改行注释，可以运行于集群环境
  //conf.setMaster("spark://Master:7077")  //绑定了程序必须运行于集群上
  //此时，程序运行于本地模式，无需Spark集群
  
  /**
   *第二步：创建SparkCon对象
   * SparkContext是Spark程序所有功能的唯一入口，无论采用Scala
   * Java、Pytho等不同语言；
   * SparkCon核心作用：初始化Spark应用程序运行所需要的核心组件，包括
   * DAGSchedular、 taskSchedular、SchedularBacked，
   * 同时还负责Spark应用程序中最为重要的一个对象。
   */
   val sc = new SparkContext(conf)
  //创建SparkConte对象，通过传入SparkContext实例来定制Spark运行的具体参数和配置信息。
   
   /**
    *第三步：根据具体的数据来源（HDFS、Hbase、Local FS、DB、S3等）通过SparkContext创建RDD
    * RDD的创建基本有三种方式：
    *   1、根据外部的数据来源（HDFS） 
    *   2、Scala集合
    *   3、有其他的RDD操作
    * 数据会被RDD划分为一系列的Partition、分配到每个Partition的数据属于一个Task的处理范围
    */
  
    val lines = sc.textFile("C://Hello",1)   //并行度为1（partition）,读取本地文件
    //根据类型推断，这边的lines是RDD[String]
    
    /**
     * 第四部：对初始的RDD进行Transformation级别的处理（高阶函数：map、filter、flatmap等）
     * 来进行具体的数据计算；
     *  4.1、将每一行的字符串拆分成单个的单词
     */
    val words = lines.flatmap(line =&gt; line.split(" "))
    //对每一行的字符串进行单词拆分并把所以行的拆分结果通过flat合并成一个大的单词集合
  
    /**
     * 第四部：对初始的RDD进行Transformation级别的处理（高阶函数：map、filter、flatmap等）
     * 来进行具体的数据计算；
     *  4.2、在单词拆分的基础上对每个单词实例计数为1，也就是word =&gt;(word,1)    
     */
  
    val pairs = words.map{ word =&gt; (word,1)}
  
    /**
     * 第四部：对初始的RDD进行Transformation级别的处理（高阶函数：map、filter、flatmap等）
     * 来进行具体的数据计算；
     *  4.3、在每个单词实例计数为1的基础上统计每个单词在文件中出现的总次数    
     */
     val wordCounts.pairs.reduceBykey(_+_)
     //对相同的Key，进行value的累积（包括local和Reduce级别同时进行Reduce）
     
     wordCounts.foreach(wordNumberPair =&gt; println(wordNumberPair._1 + ":" +wordNumberPair._2))
     
     sc.stop()
     /**
      * 改程序没有Hadoop，运行会报错，属于正常，不影响运行
      * Spark prebult with Hadoop
      * 非程序错误
      */</code></pre>
<p><span style="font-family:'宋体';"></span></p>
<p style="font-family:Calibri;">第一步：修改依赖的Scala<span style="font-family:'宋体';">版本为</span>Scala 2.10.x<span style="font-family:'宋体';">；</span></p>
<p style="font-family:Calibri;">第二步：加入Spark 1.6.0<span style="font-family:'宋体';">的</span>jar<span style="font-family:'宋体';">文件依赖</span></p>
<p>第三步：找到依赖的<span style="font-family:Calibri;">Spark Jar</span><span style="font-family:'宋体';">文件并导入到</span><span style="font-family:Calibri;">Eclipse</span><span style="font-family:'宋体';">中的</span><span style="font-family:Calibri;">Jar</span><span style="font-family:'宋体';">依赖；</span></p>
<p>第四步：在<span style="font-family:Calibri;">src</span><span style="font-family:'宋体';">下建立</span><span style="font-family:Calibri;">Spark</span><span style="font-family:'宋体';">工程包；</span></p>
<p>第五步：创建<span style="font-family:Calibri;">Scala</span><span style="font-family:'宋体';">入口类；</span></p>
<p>第六步：把<span style="font-family:Calibri;">class</span><span style="font-family:'宋体';">变成</span><span style="font-family:Calibri;">object</span><span style="font-family:'宋体';">并编写</span><span style="font-family:Calibri;">main</span><span style="font-family:'宋体';">入口方法。</span></p>
<p><strong>导出文件为<span style="font-family:Calibri;">Jar</span></strong></p>
<p><strong><span style="font-family:Calibri;"><img src="https://img-blog.csdn.net/20160110081253237?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></strong></p>
<p><strong><span style="font-family:Calibri;"></span></strong></p>
<p>将该jar文件上传到集群上</p>
<img src="https://img-blog.csdn.net/20160110081324458?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br><p><span style="font-family:Calibri;"></span></p>
<p style="font-weight:bold;">运行该jar文件</p>
<p>./spark-aubmit</p>
<p>--class com.dt.spark.WordCount_cluster //<span style="font-family:'宋体';">运行包下的类</span></p>
<p>--master spark://Master:7077</p>
<p>//root/Documents/SparkApps/WordCount.jar //WordCount<span style="font-family:'宋体';">存放的地址</span></p>
<p><strong><span style="font-family:Calibri;"><img src="https://img-blog.csdn.net/20160110081347281?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></strong></p>
<p><strong><span style="font-family:Calibri;"></span></strong></p>
<p>运行完成后，将结果打印出来</p>
<img src="https://img-blog.csdn.net/20160110081503491?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br><p><span style="font-family:Calibri;"></span></p>
<p style="font-weight:bold;">写<span style="font-family:Calibri;">Shell</span><span style="font-family:'宋体';">脚本，生成</span><span style="font-family:Calibri;">Wordcount.sh</span><span style="font-family:'宋体';">脚本，运行脚本自动执行，</span><span style="font-family:'宋体';">在</span>History Web<span style="font-family:'宋体';">界面查看运行任务状态</span></p>
<p>chmod 755 Wordcount.sh</p>
<p><img src="https://img-blog.csdn.net/20160110081613211?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p><br></p>
<p><span style="background-color:rgb(255,255,255);"><span style="color:#ff0000;">作业：实现广告点击排序</span></span></p>
<p></p>
<pre><code class="language-java">package spark.kong.dt.job
import org.apache.spark.SparkConf
import org.apache.spark.SparkContext

/**
 * @author css-kxr
 * DT_大数据梦工厂
 * HomeWord：广告点击排序
 */
object rankHit {
  def main(args:String){
    /**
     * first:create new object of SparkConf,There is none Parameters
     */
    val conf = new SparkConf()
    conf.setAppName("That is so cool Rank the Hit rates advertisements")
    
    /**
     *Second:create new object of SparkContext that is a only program Entrance to run 
     */
    val sc = new SparkContext(conf)
    
    /**
     * third：According to the data resource HDFS,S3&amp; DB to create RDD
     * textFile("Path of files",1)  number 1 = one Partition
     */
     val lines = sc.textFile("C://spark.kong.dt.job//spark-1.6.0-bin-hadoop-2.6.0//README.MD", 1)
     
     /**
      * fourth:Ececute the lines like map or filter and so on
      */
     
     &lt;p&gt; val rank = lines.flatMap(line =&gt; line.split(" ")).map(word =&gt;(word,1)).reduceByKey(_+_).map{word =&gt;(word._2 ,word._1)}.sortByKey().collect().reverse.map{word =&gt;(word._2 ,word._1)}foreach(println) &lt;/p&gt;&lt;p&gt;   Sc.stop()&lt;/p&gt;
  }
}</code></pre><br><br><p></p>
<p><br></p>
<p><br></p>
<p><br></p>
<p><span style="font-family:Calibri;"></span></p>
<p>ＤＴ大数据梦工厂</p>
<p>新浪微博：<a href="http://www.weibo.com/ilovepains/" rel="nofollow">www.weibo.com/ilovepains/</a></p>
<p>微信公众号：<span style="font-family:Calibri;">DT_Spark</span></p>
<p>博客：<a href="/ilovepains" rel="nofollow">http://.blog.sina.com.cn/ilovepains</a></p>
<p>TEL:18610086859</p>
<p>Email:18610086859@vip.126.com</p>
<br><p><span style="font-family:Calibri;"><br></span></p>
<p><strong><span style="font-family:Calibri;"><br></span></strong></p>
<p><strong><span style="font-family:Calibri;"><br></span></strong></p>
<p><strong><span style="font-family:Calibri;"><br></span></strong></p>
<p><strong><span style="font-family:Calibri;"><br></span></strong></p>
<p><strong><span style="font-family:Calibri;"><br></span></strong></p>
<p><strong><span style="font-family:Calibri;"><br></span></strong></p>
<br>            </div>
                </div>