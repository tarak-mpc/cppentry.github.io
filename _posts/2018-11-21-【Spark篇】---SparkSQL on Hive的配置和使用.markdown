---
layout:     post
title:      【Spark篇】---SparkSQL on Hive的配置和使用
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/LHWorldBlog/article/details/80822841				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <div class="postBody">
			<div id="cnblogs_post_body" class="blogpost-body"><p><strong><span style="color:#ff0000;font-size:18px;">一、前述</span></strong></p>
<p class="15">Spark on Hive<span style="font-family:'微软雅黑';">：</span><span style="color:#ff0000;"><strong> Hive只作为储存角色</strong></span>，<strong><span style="color:#ff0000;">Spark负责sql解析优化，执行。</span></strong></p>
<p class="15"><strong><span style="font-size:18px;color:#ff0000;">二、具体配置</span></strong></p>
<p><span style="color:#ff0000;font-size:16px;"><strong><span style="font-family:'微软雅黑';">1、在</span>Spark<span style="color:#0000ff;">客户端</span>配置Hive On Spark</strong></span></p>
<p class="15"> </p>
<p class="15"><span style="font-family:'微软雅黑';">            在</span>Spark客户端安装包下spark-1.6.0/conf<span style="font-family:'微软雅黑';">中创建文件</span>hive-site.xml<span style="font-family:'微软雅黑';">：</span></p>
<p class="15"><span style="font-family:'微软雅黑';color:#000000;"><span style="font-family:'微软雅黑';"><span style="font-family:'微软雅黑';">            配置hive的metastore路径</span></span></span></p>
<div class="cnblogs_code">
<pre><span style="color:#0000ff;">&lt;</span><span style="color:#800000;">configuration</span><span style="color:#0000ff;">&gt;</span>
   <span style="color:#0000ff;">&lt;</span><span style="color:#800000;">property</span><span style="color:#0000ff;">&gt;</span>
        <span style="color:#0000ff;">&lt;</span><span style="color:#800000;">name</span><span style="color:#0000ff;">&gt;</span>hive.metastore.uris<span style="color:#0000ff;">&lt;/</span><span style="color:#800000;">name</span><span style="color:#0000ff;">&gt;</span>
        <span style="color:#0000ff;">&lt;</span><span style="color:#800000;">value</span><span style="color:#0000ff;">&gt;</span>thrift://node1:9083<span style="color:#0000ff;">&lt;/</span><span style="color:#800000;">value</span><span style="color:#0000ff;">&gt;</span>
   <span style="color:#0000ff;">&lt;/</span><span style="color:#800000;">property</span><span style="color:#0000ff;">&gt;</span>
<span style="color:#0000ff;">&lt;/</span><span style="color:#800000;">configuration</span><span style="color:#0000ff;">&gt;</span></pre>
</div>
<p class="15"> </p>
<p><span style="color:#ff0000;"><strong><span style="font-size:16px;"><span>2、启动</span>Hive的metastore服务</span></strong></span></p>
<p><span style="color:#000000;font-size:15px;"><strong>       <span style="color:#0000ff;">hive --service metastore</span></strong></span></p>
<p class="15"> </p>
<p><span style="color:#ff0000;"><strong><span style="font-size:16px;"><span style="font-family:'微软雅黑';">3、启动</span>zookeeper集群，启动HDFS集群。</span></strong></span></p>
<p><strong><span style="font-size:16px;color:#ff0000;"><span style="font-family:'微软雅黑';">4、启动</span>SparkShell 读取Hive中的表总数，对比hive中查询同一表查询总数测试时间。</span></strong></p>
<p> </p>
<div class="cnblogs_code">
<pre><span style="color:#000000;">./spark-shell 
--master spark://node1:7077,node2:7077 
 --executor-cores 1 
--executor-memory 1g 
--total-executor-cores 1
import org.apache.spark.sql.hive.HiveContext
val hc = new HiveContext(sc)
hc.sql("show databases").show
hc.sql("user default").show
hc.sql("select count(*) from jizhan").show</span></pre>
</div>
<p class="15"><span style="color:#ff0000;"><strong><span style="color:#0000ff;">可以发现性能明显提升！！！</span> <br></strong></span></p>
<p class="15"><span style="color:#ff0000;"><strong><span style="font-family:'微软雅黑';">注意：</span></strong></span></p>
<p class="15"><span style="font-family:'微软雅黑';">如果使用</span>Spark on Hive  查询数据时，出现错误：</p>
<p class="15"><img src="https://images2017.cnblogs.com/blog/1250469/201802/1250469-20180208152704920-1644225822.png" alt=""></p>
<p><span style="font-family:'微软雅黑';">找不到</span>HDFS集群路径，要在客户端机器conf/spark-env.sh中设置HDFS的路径：</p>
<p> export HADOOP_CONF_DIR=$HADOOP_HOME/etc/hadoop</p>
<p><span style="color:#ff0000;font-size:18px;"><strong> 三、<span style="font-family:'微软雅黑';">读取</span>Hive中的数据加载成DataFrame</strong></span></p>
<p> </p>
<p>   <strong><span style="color:#ff0000;">1、</span></strong>HiveContext是SQLContext的子类，<strong><span style="color:#ff0000;">连接Hive建议使用HiveContext。</span></strong></p>
<p><strong><span style="color:#ff0000;">   2、</span></strong><span style="font-family:'微软雅黑';">由于本地没有</span>Hive环境，要提交到集群运行，提交命令：</p>
<div class="cnblogs_code">
<pre><span style="color:#000000;">/spark-submit 
--master spark://node1:7077,node2:7077 
--executor-cores 1 
--executor-memory 2G 
--total-executor-cores 1
--class com.bjsxt.sparksql.dataframe.CreateDFFromHive 
/root/test/HiveTest.jar</span></pre>
</div>
<p> java代码：</p>
<div class="cnblogs_code">
<pre>SparkConf conf = <span style="color:#0000ff;">new</span><span style="color:#000000;"> SparkConf();
conf.setAppName(</span>"hive"<span style="color:#000000;">);
JavaSparkContext sc </span>= <span style="color:#0000ff;">new</span><span style="color:#000000;"> JavaSparkContext(conf);
</span><span style="color:#ff0000;"><strong>//</strong><strong>HiveContext是SQLContext的子类。</strong></span>
HiveContext hiveContext = <span style="color:#0000ff;">new</span><span style="color:#000000;"> HiveContext(sc);
hiveContext.sql(</span>"USE spark"<span style="color:#000000;">);
hiveContext.sql(</span>"DROP TABLE IF EXISTS student_infos"<span style="color:#000000;">);
</span><span style="color:#008000;">//</span><span style="color:#008000;">在hive中创建student_infos表</span>
hiveContext.sql("CREATE TABLE IF NOT EXISTS student_infos (name STRING,age INT) row format delimited fields terminated by '\t' "<span style="color:#000000;">);
hiveContext.sql(</span>"load data local inpath '/root/test/student_infos' into table student_infos"<span style="color:#000000;">);

hiveContext.sql(</span>"DROP TABLE IF EXISTS student_scores"<span style="color:#000000;">); 
hiveContext.sql(</span>"CREATE TABLE IF NOT EXISTS student_scores (name STRING, score INT) row format delimited fields terminated by '\t'"<span style="color:#000000;">);  
hiveContext.sql(</span>"LOAD DATA "
+ "LOCAL INPATH '/root/test/student_scores'"
+ "INTO TABLE student_scores"<span style="color:#000000;">);
</span><span style="color:#008000;">/**</span><span style="color:#008000;">
 * 查询表生成DataFrame
 </span><span style="color:#008000;">*/</span><span style="color:#000000;">
DataFrame goodStudentsDF </span>= hiveContext.sql("SELECT si.name, si.age, ss.score "
+ "FROM student_infos si "
+ "JOIN student_scores ss "
+ "ON si.name=ss.name "
+ "WHERE ss.score&gt;=80"<span style="color:#000000;">);

hiveContext.sql(</span>"DROP TABLE IF EXISTS good_student_infos"<span style="color:#000000;">);

goodStudentsDF.registerTempTable(</span>"goodstudent"<span style="color:#000000;">);
DataFrame result </span>= hiveContext.sql("select * from goodstudent"<span style="color:#000000;">);
result.show();

</span><span style="color:#008000;">/**</span><span style="color:#008000;">
 * 将结果保存到hive表 good_student_infos
 </span><span style="color:#008000;">*/</span><span style="color:#000000;">
goodStudentsDF.write().mode(SaveMode.Overwrite).saveAsTable(</span>"good_student_infos"<span style="color:#000000;">);

Row[] goodStudentRows </span>= hiveContext.table("good_student_infos"<span style="color:#000000;">).collect();  
</span><span style="color:#0000ff;">for</span><span style="color:#000000;">(Row goodStudentRow : goodStudentRows) {
    System.out.println(goodStudentRow);  
}
sc.stop();</span></pre>
</div>
<p>scala代码：</p>
<div class="cnblogs_Highlighter">
<pre class="brush:scala;gutter:true;">val conf = new SparkConf()
 conf.setAppName("HiveSource")
 val sc = new SparkContext(conf)
 /**
  * HiveContext是SQLContext的子类。
  */
 val hiveContext = new HiveContext(sc)
 hiveContext.sql("use spark")
 hiveContext.sql("drop table if exists student_infos")
 hiveContext.sql("create table if not exists student_infos (name string,age int) row format  delimited fields terminated by '\t'")
 hiveContext.sql("load data local inpath '/root/test/student_infos' into table student_infos")
 
 hiveContext.sql("drop table if exists student_scores")
 hiveContext.sql("create table if not exists student_scores (name string,score int) row format delimited fields terminated by '\t'")
 hiveContext.sql("load data local inpath '/root/test/student_scores' into table student_scores")
 
 val df = hiveContext.sql("select si.name,si.age,ss.score from student_infos si,student_scores ss where si.name = ss.name")
 hiveContext.sql("drop table if exists good_student_infos")
 /**<strong><span style="color:#ff0000;">
  * 将结果写入到hive表中</span></strong>
  */
 <span style="color:#ff0000;"><strong>df.write.mode(SaveMode.Overwrite).saveAsTable("good_student_infos")</strong></span>
 
 sc.stop()
</pre>
</div>
<p> 结果：</p>
<p><img src="https://images2017.cnblogs.com/blog/1250469/201802/1250469-20180208190153498-375724947.png" alt=""></p>
<p><strong><span style="color:#ff0000;">可以看到分组内有序，组间并不是有序的！！！！</span></strong></p></div><div id="MySignature"></div>
<div class="clear"></div>
<div id="blog_post_info_block">
<div id="BlogPostCategory"></div>
<div id="EntryTag"></div>
<div id="blog_post_info">
</div>
<div class="clear"></div>
<div id="post_next_prev"></div>
</div>


		</div>            </div>
                </div>