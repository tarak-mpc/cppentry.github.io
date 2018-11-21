---
layout:     post
title:      Spark资源申请确定内存和CPU数量案例实战-Spark商业应用实战
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本套技术专栏是作者（秦凯新）平时工作的总结和升华，通过从真实商业环境抽取案例进行总结和分享，并给出商业应用的调优建议和集群环境容量规划等内容，请持续关注本套博客。版权声明：禁止转载，欢迎学习。QQ邮箱地址：1120746959@qq.com，如有任何商业交流，可随时联系。					https://blog.csdn.net/shenshouniu/article/details/83752004				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<ul>
<li><a href="https://juejin.im/post/5bded9d9e51d45053b5c73b4" rel="nofollow">秦凯新的技术社区-大数据商业实战系列全集目录  </a></li>
<li><a href="https://juejin.im/post/5bded7bf518825171425a23d" rel="nofollow">Spark商业应用实战-Spark聚合开窗与自定义累加器的高级应用 </a></li>
<li><a href="https://juejin.im/post/5bdef3dd51882516df02fdc4" rel="nofollow">Spark商业应用实战-Spark自定义排序规则以及SQL高级应用 </a></li>
<li><a href="https://juejin.im/post/5bdefd4a518825170b1013b3" rel="nofollow">Spark商业应用实战-SparkStreaming窗口滑动以及拉链实现转化率的高阶应用 </a></li>
<li><a href="https://juejin.im/post/5bdff5806fb9a049b07ccc9a" rel="nofollow">Spark商业应用实战-Spark资源申请确定内存和Cpu数量案例实战 </a></li>
</ul>
<h2><a id="1_SparkYarn_6"></a>1 Spark基于Yarn模式进行资源申请</h2>
<h3><a id="11_executor_7"></a>1.1 executor个数确定</h3>
<p>以yarn模式启动(必须拷贝spark jar包)在yarn模式下，如何确定executor个数，直接指定 –num-executors 这个参数即可。</p>
<p>我们知道，使用yarn作为cluster manager时，spark（以client模式为例）用spark-submit提交应用程序（或者是spark-shell交互操作）不加任何资源参数时，会使用如下几个默认配置来向yarn的resourcemanager申请container资源：</p>
<ul>
<li>spark.executor.memory     1g</li>
<li>spark.executor.cores      1</li>
<li>spark.executor.instances  2</li>
<li>spark.yarn.am.memory      512m</li>
<li>spark.yarn.am.cores       1</li>
</ul>
<p>按照参数的默认值，yarn将会生成3个containers，第一个是container0，用来执行applicationmaster功能，另外两个container，就是分配给spark程序的CoarseGrainedExecutorBackend.<br>
结合上面这些默认值，我们认为将会占用集群的3个vcores，3.5G memory。<br>
第一个问题来了，为什么memory使用的数量是5个， 为什么memory使用的数量不是想象中的3.5g呢？</p>
<p>原来，yarn对于应用程序所要申请的内存资源，有两个参数来影响实际申请到内存容量：<br>
第一个是yarn.scheduler.minimum-allocation-mb：最小可申请内存量，默认是1024。<br>
第二个是规整化因子（FIFO和Capacity Scheduler时，规整化因子等于最小可申请资源量，不可单独配置；Fair Scheduler时，规整化因子通过参数yarn.scheduler.increment-allocation-mb设置，默认是1024），其作用是应用程序申请的资源如果不是该因子的整数倍，则将被修改为最小的整数倍对应的值。<br>
由于每个容器都会占用一些额外的内存，所以导致CoarseGrainedExecutorBackend的每个容器实际使用的内存数 &gt; 申请的1G，在规整化因子的作用下，这些容器实际申请的内存，就会是2G；而applicationmaster所在的容器，因为申请内存不到1G，则在最小可申请内存的作用下，实际申请的内存就会是1G。</p>
<pre><code>$SPARK_HOME/bin/spark-submit --master yarn  --deploy-mode cluster --class you.class.Name --executor-memory 1g --executor-cores 1 --num-executors 8 --driver-memory 2g  /you/jar.jar
</code></pre>
<h3><a id="12_cpu_corememory_29"></a>1.2 同时调度cpu core和memory</h3>
<pre><code>yarn-site.xml
&lt;property&gt;
    &lt;name&gt;yarn.resourcemanager.scheduler.class&lt;/name&gt;   
    &lt;value&gt;org.apache.hadoop.yarn.server.resourcemanager.scheduler.capacity.CapacityScheduler&lt;/value&gt;
&lt;/property&gt;

capacity-scheduler.xml
&lt;property&gt;
    &lt;name&gt;yarn.scheduler.capacity.resource-calculator&lt;/name&gt;
    &lt;value&gt;org.apache.hadoop.yarn.util.resource.DominantResourceCalculator&lt;/value&gt;
&lt;/property&gt;
</code></pre>
<h3><a id="13_Yarn_43"></a>1.3 Yarn模式启动提交脚本</h3>
<pre><code>spark-shell --master yarn --executor-memory 512m --num-executors 4 --executor-cores 2

./bin/spark-submit --class org.apache.spark.examples.SparkPi --master yarn --executor-memory 512m --num-executors 3 ./examples/jars/spark-examples_2.11-2.3.0.jar 1000
</code></pre>
<h2><a id="2_Sparkstandalone_49"></a>2 Spark以standalone模式进行资源申请</h2>
<h3><a id="21_executor_50"></a>2.1 executor个数确定</h3>
<p>standlone模式下,如何确定内存和cpu数量：公式：execuoterNum = spark.cores.max/spark.executor.cores<br>
spark.cores.max：表示整个集群所具有的cpu内核数量，相关参数在启动具体应用时指定</p>
<ul>
<li>–total-executor-cores</li>
<li>–executor-cores</li>
</ul>
<p>它们共同决定了当前应用启动executor的个数，所以通过设置total-executor-cores，可以决定executor的个数。</p>
<pre><code>./bin/spark-submit --class org.apache.spark.examples.SparkPi --master spark://bd-master:7077 --executor-memory 512m --num-executors 3 ./examples/jars/spark-examples_2.11-2.3.0.jar 1000

spark-shell --master spark://bd-master:7077 --total-executor-cores 40 --executor-memory 4096m --executor-cores 4
</code></pre>
<h3><a id="21_Spark_Shell_62"></a>2.1 Spark Shell测试</h3>
<pre><code>sc.textFile("hdfs://bd-master:9000/user/root/input").flatMap(_.split(" ")).map(x=&gt;(x,1)).reduceByKey(_+_).map(x=&gt;(x._2,x._1)).sortByKey(false).map(x=&gt;(x._2,x._1)).collect.foreach(println)

sc.textFile("hdfs://bd-master:9000/waflog").flatMap(_.split("|")).collect.take(10).foreach(println)
</code></pre>
<h3><a id="22__68"></a>2.2 日志模型案例测试</h3>
<pre><code>$remote_addr | $time_local | $request | $status | $body_bytes_sent | $bytes_sent | $gzip_ratio 
| $http_referer | $http_user_agent | $http_x_forwarded_for | $upstream_addr 
| $upstream_response_time | $upstream_status | $request_time | $host;
</code></pre>
<h3><a id="23ETL_74"></a>2.3：离线ETL</h3>
<pre><code>    import java.text.SimpleDateFormat
    import org.apache.log4j.{Level, Logger}
    import org.apache.spark.{SparkConf, SparkContext}
    import java.util.{Calendar, Date}
    
    val DATE_FORMAT = new SimpleDateFormat("yyyyMMdd")
    val DATE_FORMAT_ = new SimpleDateFormat("yyyy-MM-dd")
    
    val lines = sc.textFile("/opendir/opendir/access.log-20180620")
    
    val formatedLog = lines.map(log =&gt;{
      val logSplited = log .split("\\|")
      val eventTime = logSplited(1)
      val todayDate = DATE_FORMAT_.format(new Date().getTime)
      val cutTime = eventTime.substring(13, eventTime.length - 7)
      val dataTime = todayDate + " " + cutTime
      logSplited(1)=dataTime
    
      for(i &lt;- 0 to (logSplited.length-1)){
        logSplited(i)=logSplited(i).trim
      }
      logSplited.mkString("@@")
    })
    
    val outpath = "hdfs://bd-master:9000/waflog/access.log-20180620"
    formatedLog.saveAsTextFile(outpath) 
</code></pre>
<h3><a id="24_102"></a>2.4：数据修正–细化粒度</h3>
<pre><code>    import java.sql.Timestamp
    import java.text.SimpleDateFormat
    import java.util.{Calendar, Date}
    
    val DATE_FORMAT =  new SimpleDateFormat("yyyyMMdd")
    val DATE_FORMAT_ = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss")
    private val cal: Calendar = Calendar.getInstance
    
    //日志模型--加入小时分钟标志
    case class wafLogModel(remote_addr:String, time_local:Timestamp, request:String,
                             status:String, body_bytes_sent:String, bytes_sent:Long,
                             gzip_ratio:String, http_referer:String, http_user_agent:String,
                             http_x_forwarded_for:String, upstream_addr:String, upstream_response_time:String,
                             upstream_status:String, request_time:String, host:String, hour_flag:String, minute_flag:String) 
    val fileRDD = sc.textFile("hdfs://bd-master:9000/waflog/access.log-20180620")
    import spark.implicits._
    val wafLogRDD = fileRDD.filter(x=&gt;{
            if(x.contains("\\xFAW\\xF7")) {
              false
            }else{
              true
            }
         }).map(line =&gt; line.split("@@")).map(x =&gt; {
          val ip = x(0).trim
          val urlDetails = x(2).split("/")
          var url = ""
          if (urlDetails.length == 1)  url = urlDetails(0).trim
          else if (urlDetails.length == 2) url = urlDetails(0)+" "+urlDetails(1).trim
          else if (urlDetails.length == 3) url = urlDetails(0)+" "+urlDetails(1) + "/" + urlDetails(2).trim
          else if (urlDetails.length &gt;= 4) url = urlDetails(0)+" "+urlDetails(1) + "/" + urlDetails(2) + "/" + urlDetails(3).trim
    
          val eventTime = Timestamp.valueOf(x(1))
          val format_date = DATE_FORMAT_.format(eventTime)
          val hourflag = format_date.substring(11,13)
          val minuteflag = hourflag+":"+format_date.substring(14,16)
    
          var bytesSent = ""
          var host=""
          if(x(5).trim.equals("/error= HTTP/1.1")){
            bytesSent=x(8).trim
            host = x(17).trim
            url ="GET = ReportProductSn&amp;LoginCode=LoginID&amp;ProductSn=ZJSN/error= HTTP/1.1 (Exception Log)"
          }else{
            bytesSent = x(5).trim
            host = x(14).trim
          }
          val bytesSentMb:Long = bytesSent.toLong/1024/1024L;
    
          wafLogModel(x(0),eventTime, url, x(3), x(4),bytesSentMb, x(6), x(7), x(8), x(9), x(10), x(11), x(12), x(13),host,hourflag, minuteflag)
        })
</code></pre>
<h3><a id="25__155"></a>2.5: 数据分析–域名访问统计</h3>
<pre><code>val wafLogDs = wafLogRDD.toDS()
wafLogDs.createOrReplaceTempView("wafLog")
val urlStat = spark.sql("SELECT host, remote_addr, count(*) as total_count FROM wafLog group by host, remote_addr order by total_count desc limit 10")
urlStat.show

+--------------------+--------------+-----------+                               
|                host|   remote_addr|total_count|
+--------------------+--------------+-----------+
|  hcdt.dataserver.cn|192.168.100.61|      73642|
|resource.dataserv...| 58.60.228.148|      61113|
|  hcdt.dataserver.cn| 58.60.228.148|      45858|
|testuweb.dataserv...| 58.60.228.148|      44042|
|hcautotestkyj.dat...| 58.60.228.148|      42827|
|gdlmdt.dataserver.cn| 14.157.120.63|      36587|
|resource.dataserv...| 14.157.120.63|      26947|
|   cbs.dataserver.cn|192.168.100.62|      26726|
|   cbs.dataserver.cn|192.168.100.61|      26503|
|message.dataserve...| 58.60.228.148|      25739|
+--------------------+--------------+-----------+

val urlStatStore =  urlStat.map(row =&gt; row(0)+"|"+row(1)+"|"+row(2)).rdd
urlStatStore.saveAsTextFile("/wafResult/20180620");

hcdt.dataserver.cn|192.168.100.61|73642
resource.dataserver.cn|58.60.228.148|61113
hcdt.dataserver.cn|58.60.228.148|45858
testuweb.dataserver.cn|58.60.228.148|44042
hcautotestkyj.dataserver.cn|58.60.228.148|42827
gdlmdt.dataserver.cn|14.157.120.63|36587
resource.dataserver.cn|14.157.120.63|26947
cbs.dataserver.cn|192.168.100.62|26726
cbs.dataserver.cn|192.168.100.61|26503
message.dataserver.cn|58.60.228.148|25739

case class urlStatModel(host:String,remote_addr:String,total_count:String)
urlStat.as[urlStatModel].map(urlStat =&gt; urlStat.host+"|"+urlStat.remote_addr+"|"+urlStat.total_count).rdd.saveAsTextFile("/wafResult2/20180620");

hcdt.dataserver.cn|192.168.100.61|73642
resource.dataserver.cn|58.60.228.148|61113
hcdt.dataserver.cn|58.60.228.148|45858
testuweb.dataserver.cn|58.60.228.148|44042
hcautotestkyj.dataserver.cn|58.60.228.148|42827
gdlmdt.dataserver.cn|14.157.120.63|36587
resource.dataserver.cn|14.157.120.63|26947
cbs.dataserver.cn|192.168.100.62|26726
cbs.dataserver.cn|192.168.100.61|26503
message.dataserver.cn|58.60.228.148|25739
</code></pre>
<h3><a id="26_Hive_204"></a>2.6: Hive数据建模：</h3>
<pre><code>create table accesslog(
host string, 
remote_addr string,
total_count bigint
)row format delimited fields terminated by '|';

从hdfs导入到hive
load data inpath '/wafResult/20180620' overwrite into table accesslog;
</code></pre>
<h3><a id="27__214"></a>2.7: 数据分析–流量统计</h3>
<pre><code>val bytesStat = spark.sql("SELECT host, remote_addr, request, max(bytes_sent) as max_byte FROM wafLog group by host,remote_addr,request order by max_byte desc limit 10")
bytesStat.show
+--------------------+--------------+--------------------+--------+             
|                host|   remote_addr|             request|max_byte|
+--------------------+--------------+--------------------+--------+
|resource.dataserv...|27.123.214.103|GET  download/bro...|      42|
|  hcdt.dataserver.cn|61.178.233.112|GET  1.1/componen...|      40|
|qdakfhdt.dataserv...| 58.56.156.190|GET  1.1/componen...|      40|
|westdt.dataserver.cn|222.179.116.10|GET  1.1/componen...|      40|
|security.dataserv...| 119.23.123.17|GET  iosDeploy/el...|      28|
|bestlink.dataserv...|180.97.106.135|GET  /uploadfile/APP|      22|
|security.dataserv...| 112.17.244.69|GET  iosDeploy/uw...|      17|
|greatdt.dataserve...| 58.210.39.230|GET  monitor/webs...|      16|
|  rdts.dataserver.cn| 61.130.49.162|GET  rdts?ip=192....|      15|
|security.dataserv...| 119.23.123.25|GET  iosDeploy/ca...|      13|
+--------------------+--------------+--------------------+--------+
</code></pre>
<h3><a id="28__232"></a>2.8: 数据分析–按小时进行访问次数统计</h3>
<pre><code>val urlStat = spark.sql("SELECT hour_flag, host, remote_addr, count(*) as total_count FROM wafLog group by hour_flag,host,remote_addr order by total_count desc limit 50")
urlStat.show
+---------+--------------------+--------------+-----------+                     
|hour_flag|                host|   remote_addr|total_count|
+---------+--------------------+--------------+-----------+
|       13|  hcdt.dataserver.cn| 58.60.228.148|       8650|
|       08|  hcdt.dataserver.cn| 58.60.228.148|       8606|
|       21|sdryer2.dataserve...|171.213.124.37|       8324|
|       04|  hcdt.dataserver.cn|192.168.100.61|       7162|
|       05|  hcdt.dataserver.cn|192.168.100.61|       7144|
|       12|  hcdt.dataserver.cn|192.168.100.61|       7131|
|       13|  hcdt.dataserver.cn|192.168.100.61|       7108|
|       20|  hcdt.dataserver.cn|192.168.100.61|       7106|
|       21|  hcdt.dataserver.cn|192.168.100.61|       7083|
|       11|  hcdt.dataserver.cn|192.168.100.61|       6068|
|       03|  hcdt.dataserver.cn|192.168.100.61|       6064|
|       19|  hcdt.dataserver.cn|192.168.100.61|       6029|
|       09|gdlmdt.dataserver.cn| 14.157.120.63|       5557|
|       10|gdlmdt.dataserver.cn| 14.157.120.63|       5297|
|       14|gdlmdt.dataserver.cn| 14.157.120.63|       4148|
|       13|gdlmdt.dataserver.cn| 14.157.120.63|       4140|
|       14|  hcdt.dataserver.cn|192.168.100.61|       3867|
|       12|gdlmdt.dataserver.cn| 14.157.120.63|       3789|
|       11|gdlmdt.dataserver.cn| 14.157.120.63|       3771|
|       15|gdlmdt.dataserver.cn| 14.157.120.63|       3756|
+---------+--------------------+--------------+-----------+
</code></pre>
<h3><a id="29__261"></a>2.9: 数据分析–按小时进行接口流量统计</h3>
<pre><code> val bytesStat = spark.sql("SELECT hour_flag,host,remote_addr,request,max(bytes_sent) as max_byte FROM wafLog group by hour_flag, host, remote_addr, request order by max_byte desc limit 50")
 
 bytesStat.show
+---------+--------------------+---------------+--------------------+--------+  
|hour_flag|                host|    remote_addr|             request|max_byte|
+---------+--------------------+---------------+--------------------+--------+
|       15|resource.dataserv...| 27.123.214.103|GET  download/bro...|      42|
|       09|westdt.dataserver.cn| 222.179.116.10|GET  1.1/componen...|      40|
|       15|qdakfhdt.dataserv...|  58.56.156.190|GET  1.1/componen...|      40|
|       09|  hcdt.dataserver.cn| 61.178.233.112|GET  1.1/componen...|      40|
|       11|security.dataserv...|  119.23.123.17|GET  iosDeploy/el...|      28|
|       09|security.dataserv...|  119.23.123.17|GET  iosDeploy/el...|      28|
|       11|westdt.dataserver.cn| 222.179.116.10|GET  1.1/componen...|      27|
|       23|bestlink.dataserv...| 180.97.106.135|GET  /uploadfile/APP|      22|
|       11|security.dataserv...|  112.17.244.69|GET  iosDeploy/uw...|      17|
|       07|greatdt.dataserve...|  58.210.39.230|GET  monitor/webs...|      16|
|       16|  rdts.dataserver.cn|  61.130.49.162|GET  rdts?ip=192....|      15|
|       16|security.dataserv...|  119.23.123.25|GET  iosDeploy/ca...|      13|
|       16|  rdts.dataserver.cn|  61.130.49.162|GET  rdts?ip=192....|      11|
|       23|  rdts.dataserver.cn|  183.33.59.157|GET  rdts?ip=192....|      11|
|       14|  rdts.dataserver.cn|  61.130.49.162|GET  rdts?ip=192....|      11|
|       21|bestlink.dataserv...|  123.125.71.74|GET  uploadfile/A...|       9|
|       13|hcuweb.dataserver.cn| 27.123.214.107|GET  uploadfile/A...|       9|
|       18|  hnks.dataserver.cn| 122.192.15.137|GET  uploadfile/s...|       9|
|       16|hcuweb.dataserver.cn|   122.192.13.2|GET  /uploadfile/...|       9|
|       07|bestlink.dataserv...|211.138.116.246|GET  /uploadfile/...|       8|
+---------+--------------------+---------------+--------------------+--------+
</code></pre>
<h3><a id="210__291"></a>2.10: 数据分析–按分钟进行访问次数统计</h3>
<pre><code>val urlStat = spark.sql("SELECT minute_flag,host,remote_addr,request, count(*) as total_count FROM wafLog group by minute_flag, host, remote_addr, request order by total_count desc limit 50")

urlStat.show
+-----------+--------------------+--------------+--------------------+-----------+
|minute_flag|                host|   remote_addr|             request|total_count|
+-----------+--------------------+--------------+--------------------+-----------+
|      21:33|sdryer2.dataserve...|171.213.124.37|GET  itas-app/web...|        304|
|      21:37|sdryer2.dataserve...|171.213.124.37|GET  itas-app/web...|        302|
|      21:35|sdryer2.dataserve...|171.213.124.37|GET  itas-app/web...|        302|
|      21:34|sdryer2.dataserve...|171.213.124.37|GET  itas-app/web...|        302|
|      22:00|sdryer2.dataserve...|171.213.124.37|GET  itas-app/web...|        299|
|      22:01|sdryer2.dataserve...|171.213.124.37|GET  itas-app/web...|        298|
|      21:36|sdryer2.dataserve...|171.213.124.37|GET  itas-app/web...|        296|
|      22:02|sdryer2.dataserve...|171.213.124.37|GET  itas-app/web...|        293|
|      21:39|sdryer2.dataserve...|171.213.124.37|GET  itas-app/web...|        293|
|      21:40|sdryer2.dataserve...|171.213.124.37|GET  itas-app/web...|        292|
|      21:55|sdryer2.dataserve...|171.213.124.37|GET  itas-app/web...|        292|
|      21:53|sdryer2.dataserve...|171.213.124.37|GET  itas-app/web...|        292|
|      21:52|sdryer2.dataserve...|171.213.124.37|GET  itas-app/web...|        289|
|      21:31|sdryer2.dataserve...|171.213.124.37|GET  itas-app/web...|        288|
|      21:58|sdryer2.dataserve...|171.213.124.37|GET  itas-app/web...|        288|
|      21:38|sdryer2.dataserve...|171.213.124.37|GET  itas-app/web...|        286|
|      21:42|sdryer2.dataserve...|171.213.124.37|GET  itas-app/web...|        286|
|      21:48|sdryer2.dataserve...|171.213.124.37|GET  itas-app/web...|        284|
|      21:59|sdryer2.dataserve...|171.213.124.37|GET  itas-app/web...|        282|
|      21:54|sdryer2.dataserve...|171.213.124.37|GET  itas-app/web...|        280|
+-----------+--------------------+--------------+--------------------+-----------+
</code></pre>
<h3><a id="220__320"></a>2.20: 数据分析–按分钟进行接口流量统计</h3>
<pre><code>val bytesStat = spark.sql("SELECT minute_flag,host,remote_addr,request,max(bytes_sent) as max_byte FROM wafLog group by minute_flag, host, remote_addr, request order by max_byte desc limit 50")

bytesStat.show
+-----------+--------------------+---------------+--------------------+--------+
|minute_flag|                host|    remote_addr|             request|max_byte|
+-----------+--------------------+---------------+--------------------+--------+
|      15:21|resource.dataserv...| 27.123.214.103|GET  download/bro...|      42|
|      09:29|  hcdt.dataserver.cn| 61.178.233.112|GET  1.1/componen...|      40|
|      09:42|westdt.dataserver.cn| 222.179.116.10|GET  1.1/componen...|      40|
|      15:58|qdakfhdt.dataserv...|  58.56.156.190|GET  1.1/componen...|      40|
|      11:49|security.dataserv...|  119.23.123.17|GET  iosDeploy/el...|      28|
|      09:21|security.dataserv...|  119.23.123.17|GET  iosDeploy/el...|      28|
|      11:03|westdt.dataserver.cn| 222.179.116.10|GET  1.1/componen...|      27|
|      23:31|bestlink.dataserv...| 180.97.106.135|GET  /uploadfile/APP|      22|
|      11:06|security.dataserv...|  112.17.244.69|GET  iosDeploy/uw...|      17|
|      07:51|greatdt.dataserve...|  58.210.39.230|GET  monitor/webs...|      16|
|      16:35|  rdts.dataserver.cn|  61.130.49.162|GET  rdts?ip=192....|      15|
|      16:41|security.dataserv...|  119.23.123.25|GET  iosDeploy/ca...|      13|
|      14:01|  rdts.dataserver.cn|  61.130.49.162|GET  rdts?ip=192....|      11|
|      23:00|  rdts.dataserver.cn|  183.33.59.157|GET  rdts?ip=192....|      11|
|      16:35|  rdts.dataserver.cn|  61.130.49.162|GET  rdts?ip=192....|      11|
|      18:37|  hnks.dataserver.cn| 122.192.15.137|GET  uploadfile/s...|       9|
|      21:40|bestlink.dataserv...|  123.125.71.74|GET  uploadfile/A...|       9|
|      16:12|hcuweb.dataserver.cn|   122.192.13.2|GET  /uploadfile/...|       9|
|      13:02|hcuweb.dataserver.cn| 27.123.214.107|GET  uploadfile/A...|       9|
|      07:56|bestlink.dataserv...|211.138.116.246|GET  /uploadfile/...|       8|
+-----------+--------------------+---------------+--------------------+--------+
</code></pre>
<h2><a id="3__349"></a>3: 总结</h2>
<p>秦凯新 于深圳</p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>