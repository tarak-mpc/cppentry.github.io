---
layout:     post
title:      Spark SQL演示与分布式SQL查询引擎
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p><span style="color:#f33b45;">（1）通过sparksql访问mysql中的数据</span></p>

<p>bin/spark-submit \<br>
--class bigdata.spark.sql.test.DFTest \<br>
--master local \<br>
./FirstSparkPro.jar</p>

<p><span style="color:#f33b45;">（2）SparkSQL与Hive结合（类似的有Hive中处理SQL，直接用MapReduce计算处理）</span></p>

<p>使用SparkSQL访问Hive</p>

<p>将hive安装包中conf/hive-site.xml配置文件拷贝到spark安装包的conf目录下</p>

<p>将mysql驱动jar包拷贝到spark安装包的jars目录下</p>

<p>启动：spark安装包下bin/spark-sql</p>

<p><img alt="" class="has" height="166" src="https://img-blog.csdn.net/20180807113525358?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3MzkyNTg5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="930"></p>

<p><img alt="" class="has" height="294" src="https://img-blog.csdn.net/201808071138309?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3MzkyNTg5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="924"></p>

<p><span style="color:#f33b45;">分布式SQL查询引擎（工作中最常用的）</span></p>

<p><span style="color:#f33b45;">（3）配置HiveServer2 Thrift服务的访问地址和端口号</span></p>

<p>方法1：在hive-site.xml文件中添加hiveserver2的配置信息</p>

<p>        &lt;property&gt;<br>
                &lt;name&gt;hive.server2.thrift.port&lt;/name&gt;<br>
                &lt;value&gt;10000&lt;/value&gt;<br>
        &lt;/property&gt;<br>
        &lt;property&gt;<br>
                &lt;name&gt;hive.server2.thrift.bind.host&lt;/name&gt;<br>
                &lt;value&gt;192.168.137.100&lt;/value&gt;<br>
        &lt;/property&gt;</p>

<p>方法2：在环境变量中配置</p>

<p>export HIVE_SERVER2_THRIFT_PORT=10010</p>

<p>export HIVE_SERVER2_THRIFT_BIND_HOST=192.168.137.100</p>

<p>方法3：在启动Spark Thrift Server的时候以参数的形式指定</p>

<p>--hiveconf hive.server2.thrift.port=10010</p>

<p>--hiveconf hive.server2.thrift.bind.host=192.168.137.100</p>

<p><br><span style="color:#f33b45;">启动Spark Thrift Server</span><br>
#Local模式启动：<br>
sbin/start-thriftserver.sh \<br>
--hiveconf hive.server2.thrift.port=10010 \<br>
--hiveconf hive.server2.thrift.bind.host=192.168.137.100</p>

<p> </p>

<p>#yarn-client模式启动：<br>
sbin/start-thriftserver.sh \<br>
--hiveconf hive.server2.thrift.port=10010 \<br>
--hiveconf hive.server2.thrift.bind.host=192.168.137.100 \<br>
--master yarn \<br>
--deploy-mode client \<br>
--executor-memory 3g \<br>
--executor-cores 1 \<br>
--num-executors 2 \<br>
--driver-cores 1 \<br>
--driver-memory 1g</p>

<p>查看是否报错以及是否连接 用命令：tail -100 路径和lsof -i:10010</p>

<p><img alt="" class="has" height="279" src="https://img-blog.csdn.net/20180807114633428?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3MzkyNTg5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="952"></p>

<p><img alt="" class="has" height="158" src="https://img-blog.csdn.net/20180807132928349?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3MzkyNTg5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="928"></p>

<p><img alt="" class="has" height="531" src="https://img-blog.csdn.net/20180807115016510?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3MzkyNTg5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="775"></p>

<p><img alt="" class="has" height="439" src="https://img-blog.csdn.net/20180807133044435?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3MzkyNTg5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="774"></p>

<p><span style="color:#f33b45;">连接分布式SQL查询引擎</span></p>

<p>#（1）使用beeline jdbc连接<br>
./bin/beeline<br>
beeline&gt;!connect jdbc:hive2://192.168.137.100:10010</p>

<p><img alt="" class="has" height="676" src="https://img-blog.csdn.net/20180807133614664?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3MzkyNTg5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="727"></p>

<p><img alt="" class="has" height="507" src="https://img-blog.csdn.net/20180807133705523?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3MzkyNTg5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="731"></p>

<p>#（2）通过Java Api使用JDBC访问（代码在最下面）</p>

<p>通过JDBC访问spark thrift server<br>
bin/spark-submit \<br>
--class bigdata.spark.sql.SparkSqlJdbc \<br>
--master local \<br>
./FirstSparkPro.jar</p>

<p> </p>

<p> </p>

<p>代码：<br>
pom.xml:</p>

<p>&lt;?xml version="1.0" encoding="UTF-8"?&gt;<br>
&lt;project xmlns="http://maven.apache.org/POM/4.0.0"<br>
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"<br>
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd"&gt;<br>
    &lt;modelVersion&gt;4.0.0&lt;/modelVersion&gt;</p>

<p>    &lt;groupId&gt;cn.chinahadoop&lt;/groupId&gt;<br>
    &lt;artifactId&gt;FirstSparkPro&lt;/artifactId&gt;<br>
    &lt;version&gt;1.0-SNAPSHOT&lt;/version&gt;<br>
    &lt;properties&gt;<br>
        &lt;maven.compiler.source&gt;1.8&lt;/maven.compiler.source&gt;<br>
        &lt;maven.compiler.target&gt;1.8&lt;/maven.compiler.target&gt;<br>
        &lt;encoding&gt;UTF-8&lt;/encoding&gt;<br>
        &lt;scala.version&gt;2.11.11&lt;/scala.version&gt;<br>
        &lt;spark.version&gt;2.2.1&lt;/spark.version&gt;<br>
        &lt;hadoop.version&gt;2.7.4&lt;/hadoop.version&gt;<br>
    &lt;/properties&gt;</p>

<p>    &lt;dependencies&gt;<br>
        &lt;dependency&gt;<br>
            &lt;groupId&gt;org.scala-lang&lt;/groupId&gt;<br>
            &lt;artifactId&gt;scala-compiler&lt;/artifactId&gt;<br>
            &lt;version&gt;${scala.version}&lt;/version&gt;<br>
        &lt;/dependency&gt;<br>
        &lt;dependency&gt;<br>
            &lt;groupId&gt;org.scala-lang&lt;/groupId&gt;<br>
            &lt;artifactId&gt;scala-reflect&lt;/artifactId&gt;<br>
            &lt;version&gt;${scala.version}&lt;/version&gt;<br>
        &lt;/dependency&gt;<br>
        &lt;dependency&gt;<br>
            &lt;groupId&gt;org.scala-lang&lt;/groupId&gt;<br>
            &lt;artifactId&gt;scala-library&lt;/artifactId&gt;<br>
            &lt;version&gt;${scala.version}&lt;/version&gt;<br>
        &lt;/dependency&gt;<br>
        &lt;dependency&gt;<br>
            &lt;groupId&gt;org.scala-lang&lt;/groupId&gt;<br>
            &lt;artifactId&gt;scala-actors&lt;/artifactId&gt;<br>
            &lt;version&gt;${scala.version}&lt;/version&gt;<br>
        &lt;/dependency&gt;<br>
        &lt;dependency&gt;<br>
            &lt;groupId&gt;org.scala-lang&lt;/groupId&gt;<br>
            &lt;artifactId&gt;scalap&lt;/artifactId&gt;<br>
            &lt;version&gt;${scala.version}&lt;/version&gt;<br>
        &lt;/dependency&gt;<br>
        &lt;dependency&gt;<br>
            &lt;groupId&gt;org.apache.spark&lt;/groupId&gt;<br>
            &lt;artifactId&gt;spark-core_2.11&lt;/artifactId&gt;<br>
            &lt;version&gt;${spark.version}&lt;/version&gt;<br>
        &lt;/dependency&gt;<br>
        &lt;dependency&gt;<br>
            &lt;groupId&gt;org.apache.spark&lt;/groupId&gt;<br>
            &lt;artifactId&gt;spark-sql_2.11&lt;/artifactId&gt;<br>
            &lt;version&gt;${spark.version}&lt;/version&gt;<br>
        &lt;/dependency&gt;<br>
        &lt;dependency&gt;<br>
            &lt;groupId&gt;org.apache.hive&lt;/groupId&gt;<br>
            &lt;artifactId&gt;hive-jdbc&lt;/artifactId&gt;<br>
            &lt;version&gt;1.2.1&lt;/version&gt;<br>
        &lt;/dependency&gt;<br>
        &lt;dependency&gt;<br>
            &lt;groupId&gt;mysql&lt;/groupId&gt;<br>
            &lt;artifactId&gt;mysql-connector-java&lt;/artifactId&gt;<br>
            &lt;version&gt;5.1.45&lt;/version&gt;<br>
        &lt;/dependency&gt;</p>

<p>        &lt;dependency&gt;<br>
            &lt;groupId&gt;org.apache.hadoop&lt;/groupId&gt;<br>
            &lt;artifactId&gt;hadoop-client&lt;/artifactId&gt;<br>
            &lt;version&gt;${hadoop.version}&lt;/version&gt;<br>
        &lt;/dependency&gt;<br>
    &lt;/dependencies&gt;</p>

<p>    &lt;build&gt;<br>
        &lt;sourceDirectory&gt;src/main/scala&lt;/sourceDirectory&gt;<br>
        &lt;testSourceDirectory&gt;src/test/scala&lt;/testSourceDirectory&gt;<br>
        &lt;plugins&gt;<br>
            &lt;plugin&gt;<br>
                &lt;artifactId&gt;maven-assembly-plugin&lt;/artifactId&gt;<br>
                &lt;version&gt;2.3&lt;/version&gt;<br>
                &lt;configuration&gt;<br>
                    &lt;classifier&gt;dist&lt;/classifier&gt;<br>
                    &lt;appendAssemblyId&gt;true&lt;/appendAssemblyId&gt;<br>
                    &lt;descriptorRefs&gt;<br>
                        &lt;descriptor&gt;jar-with-dependencies&lt;/descriptor&gt;<br>
                    &lt;/descriptorRefs&gt;<br>
                &lt;/configuration&gt;<br>
                &lt;executions&gt;<br>
                    &lt;execution&gt;<br>
                        &lt;id&gt;make-assembly&lt;/id&gt;<br>
                        &lt;phase&gt;package&lt;/phase&gt;<br>
                        &lt;goals&gt;<br>
                            &lt;goal&gt;single&lt;/goal&gt;<br>
                        &lt;/goals&gt;<br>
                    &lt;/execution&gt;<br>
                &lt;/executions&gt;<br>
            &lt;/plugin&gt;<br>
            &lt;plugin&gt;<br>
                &lt;groupId&gt;org.apache.maven.plugins&lt;/groupId&gt;<br>
                &lt;artifactId&gt;maven-compiler-plugin&lt;/artifactId&gt;<br>
                &lt;version&gt;2.3.2&lt;/version&gt;<br>
                &lt;executions&gt;<br>
                    &lt;execution&gt;<br>
                        &lt;id&gt;default-compile&lt;/id&gt;<br>
                        &lt;phase&gt;compile&lt;/phase&gt;<br>
                        &lt;goals&gt;<br>
                            &lt;goal&gt;compile&lt;/goal&gt;<br>
                        &lt;/goals&gt;<br>
                        &lt;configuration&gt;<br>
                            &lt;encoding&gt;UTF-8&lt;/encoding&gt;<br>
                        &lt;/configuration&gt;<br>
                    &lt;/execution&gt;<br>
                &lt;/executions&gt;<br>
            &lt;/plugin&gt;<br>
            &lt;plugin&gt;<br>
                &lt;groupId&gt;net.alchim31.maven&lt;/groupId&gt;<br>
                &lt;artifactId&gt;scala-maven-plugin&lt;/artifactId&gt;<br>
                &lt;version&gt;3.2.2&lt;/version&gt;<br>
                &lt;executions&gt;<br>
                    &lt;execution&gt;<br>
                        &lt;id&gt;scala-compile-first&lt;/id&gt;<br>
                        &lt;phase&gt;process-resources&lt;/phase&gt;<br>
                        &lt;goals&gt;<br>
                            &lt;goal&gt;compile&lt;/goal&gt;<br>
                        &lt;/goals&gt;<br>
                    &lt;/execution&gt;<br>
                &lt;/executions&gt;<br>
                &lt;configuration&gt;<br>
                    &lt;scalaVersion&gt;${scala.version}&lt;/scalaVersion&gt;<br>
                    &lt;recompileMode&gt;incremental&lt;/recompileMode&gt;<br>
                    &lt;useZincServer&gt;true&lt;/useZincServer&gt;<br>
                    &lt;args&gt;<br>
                        &lt;arg&gt;-unchecked&lt;/arg&gt;<br>
                        &lt;arg&gt;-deprecation&lt;/arg&gt;<br>
                        &lt;arg&gt;-feature&lt;/arg&gt;<br>
                    &lt;/args&gt;<br>
                    &lt;jvmArgs&gt;<br>
                        &lt;jvmArg&gt;-Xms1024m&lt;/jvmArg&gt;<br>
                        &lt;jvmArg&gt;-Xmx1024m&lt;/jvmArg&gt;<br>
                    &lt;/jvmArgs&gt;<br>
                    &lt;javacArgs&gt;<br>
                        &lt;javacArg&gt;-source&lt;/javacArg&gt;<br>
                        &lt;javacArg&gt;${java.version}&lt;/javacArg&gt;<br>
                        &lt;javacArg&gt;-target&lt;/javacArg&gt;<br>
                        &lt;javacArg&gt;${java.version}&lt;/javacArg&gt;<br>
                        &lt;javacArg&gt;-Xlint:all,-serial,-path&lt;/javacArg&gt;<br>
                    &lt;/javacArgs&gt;<br>
                &lt;/configuration&gt;<br>
            &lt;/plugin&gt;<br>
        &lt;/plugins&gt;<br>
    &lt;/build&gt;</p>

<p><br>
&lt;/project&gt;</p>

<p> </p>

<p> </p>

<p> </p>

<p><br>
DFTest.scala:</p>

<p>package bigdata.spark.sql.test</p>

<p>import java.util.Properties</p>

<p>import org.apache.spark.SparkConf<br>
import org.apache.spark.sql.{SaveMode, Row, SparkSession}<br>
import org.apache.spark.sql.types.{IntegerType, StringType, StructField, StructType}</p>

<p>/**<br>
 * Created by pc on 2018/8/6.<br>
 */<br>
object DFTest {<br>
  case class UserCoreDF(userId : String,age : Int,gender : String,core : Int)<br>
  def main(args: Array[String]) {<br>
    val conf = new SparkConf()<br>
    //创建sparksession<br>
    val spark = SparkSession.builder()<br>
                            .master("local[2]")<br>
                            .appName("dftest")<br>
                            .config(conf)<br>
                            .getOrCreate()</p>

<p>    val sc = spark.sparkContext<br>
    //RDD -&gt; DataFrame 通过反射<br>
    val userCoresRdd = sc.textFile("hdfs://192.168.137.100:9000/data/sparksql/user")<br>
    val userCoreRdd = userCoresRdd.map(_.split("\t")).map(cols =&gt; UserCoreDF(cols(0),cols(1).toInt,cols(2),cols(3).toInt))<br>
    //引入隐式转换，将RDD隐式转换为DataFrame<br>
    import spark.implicits._<br>
    val userCoreDF = userCoreRdd.toDF()<br>
//    userCoreDF.take(2).foreach(println(_))<br>
//    println("userCoreDF count ---------&gt;"+userCoreDF.count())</p>

<p>    //RDD -&gt; DataFrame 显式定义schema<br>
    //定义schema<br>
    val userCoreSchema = StructType(<br>
      List(<br>
        StructField("userId",StringType,true),<br>
        StructField("age",IntegerType,true),<br>
        StructField("gender",StringType,true),<br>
        StructField("core",IntegerType,true)<br>
      )<br>
    )<br>
    val userCoreRdd2 = userCoresRdd.map(_.split("\t")).map(cols =&gt; Row(cols(0),cols(1).toInt,cols(2),cols(3).toInt))<br>
    val userCoreDF2 = spark.createDataFrame(userCoreRdd2,userCoreSchema)<br>
//    println("userCoreDF2 count ---------&gt;"+userCoreDF2.count())<br>
/*</p>

<p>    //dataframe -&gt; hdfs json文件<br>
    userCoreDF2.write.mode(SaveMode.Overwrite).json("hdfs://192.168.137.100:9000/tmp/user_json" )<br>
    //dataframe -&gt; hdfs parquet文件<br>
    userCoreDF2.write.mode(SaveMode.Overwrite).parquet("hdfs://192.168.137.100:9000/tmp/user_parquet" )<br>
*/<br>
    //通过内置API读取指定格式文件，创建DataFrame<br>
//    val jsonDF1 = spark.read.format("json").load("hdfs://192.168.137.100:9000/tmp/user_json")<br>
//    println("jsonDF1 count------------&gt;"+jsonDF1.count())<br>
    val jsonDF2 = spark.read.json("hdfs://192.168.137.100:9000/tmp/user_json")<br>
//    println("jsonDF2 count------------&gt;"+jsonDF2.count())<br>
//    println("jsonDF2 count------------&gt;"+jsonDF2.schema)</p>

<p><br>
    //通过JDBC读取mysql 创建DataFrame<br>
    val connconf = new Properties()<br>
    connconf.put("user","root")<br>
    connconf.put("password","haohu123")<br>
    val jdbcdf = spark.read.jdbc("jdbc:mysql://192.168.137.100:3306","hive.TBLS",connconf)<br>
    jdbcdf.show()</p>

<p>    //通过DSF<br>
//    jsonDF2.filter("age &gt; 20").select("gender","core").groupBy("gender").sum("core").show()</p>

<p>//    jsonDF2.createOrReplaceTempView("user_core")<br>
//<br>
//    spark.sql("select gender,sum(core) from user_core where age&gt;20 group by gender").show()<br>
  }<br>
}</p>

<p> </p>

<p> </p>

<p> </p>

<p>SparkSqlJdbc.scala:</p>

<p>package bigdata.spark.sql.test</p>

<p>import java.sql.DriverManager</p>

<p>/**<br>
 * Created by pc on 2018/8/6.<br>
 */<br>
object SparkSqlJdbc {<br>
  def main(args: Array[String]) {<br>
    val driver="org.apache.hive.jdbc.HiveDriver"<br>
    Class.forName(driver)<br>
    val (url,username,userpasswd)=("jdbc:hive2://192.168.137.100:10010/rel","hadoop","haohu123")<br>
    val conn = DriverManager.getConnection(url,username,userpasswd)<br>
    val sql ="select * from rel.user_core_info"<br>
    val stat= conn.prepareStatement(sql)<br>
    val rs = stat.executeQuery()<br>
    while(rs.next()){<br>
      println("userId-&gt;"+rs.getString(1))<br>
      println("name-&gt;"+rs.getString(2))<br>
    }<br>
    rs.close()<br>
    stat.close()<br>
    conn.close()</p>

<p>  }<br>
}</p>            </div>
                </div>