---
layout:     post
title:      sparksql 操作hive
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/u014236541/article/details/78688445				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p style="font-family:'Helvetica Neue', Helvetica, Verdana, Arial, sans-serif;font-size:14px;line-height:23.8px;">
SparkSQL操作Hive中的表数据<br>
spark可以通过读取hive的元数据来兼容hive，读取hive的表数据，然后在spark引擎中进行sql统计分析，从而，通过sparksql与hive结合实现数据分析将成为一种最佳实践。详细实现步骤如下：<br><br>
1、启动hive的元数据服务<br>
hive可以通过服务的形式对外提供元数据读写操作，通过简单的配置即可<br>
    <span>编辑 $HIVE_HOME/conf/hive-site.xml,增加如下内容</span>:<br>
&lt;property&gt;<br>
&lt;name&gt;hive.metastore.uris&lt;/name&gt;<br>
&lt;value&gt;thrift:// hdp-node-01:9083&lt;/value&gt;<br>
&lt;/property&gt;<br><br>
    <span>启动hive metastore</span><br>
[hadoop@hdp-node-01 ~]${HIVE_HOME}/bin/hive --service metastore  1&gt;/dev/null  2&gt;&amp;1  &amp;<br><br>
    查看 metastore:<br>
[hadoop@hdp-node-01 ~] jobs<br>
[1]+ Running hive --service metastore &amp;<br><br><br>
2、spark配置<br>
    将hive的配置文件拷贝给spark<br><span>将 $HIVE_HOME/conf/hive-site.xml copy $SPARK_HOME/conf/</span><br><br>
    <span>将mysql的jdbc驱动包拷贝给spark</span><br>
将 $HIVE_HOME/lib/mysql-connector-java-5.1.12.jar copy或者软链到$SPARK_HOME/lib/<br><br>
3、启动spark-sql的shell交互界面<br>
spark-sql已经集成在spark-shell中，因此，只要启动spark-shell，就可以使用spakr-sql的shell交互接口：<br>
[hadoop@hdp-node-01 spark] bin/spark-shell --master spark://hdp-node-01:7077</p>
<div style="font-family:'Helvetica Neue', Helvetica, Verdana, Arial, sans-serif;font-size:14px;line-height:23.8px;">
<img src="http://www.itcast.cn/files/image/201512/20151229155639858.png" alt="" style="border:0px;"></div>
<p style="font-family:'Helvetica Neue', Helvetica, Verdana, Arial, sans-serif;font-size:14px;line-height:23.8px;">
 <br>
或者，可以启动spark-sql界面，使用起来更方便<br>
[hadoop@hdp-node-01 spark] bin/spark-sql --master spark://hdp-node-01:7077</p>
<p style="font-family:'Helvetica Neue', Helvetica, Verdana, Arial, sans-serif;font-size:14px;line-height:23.8px;">
就可以使用hivesql了<br>
由于在console中会打印很多info级别日志,所以可以改变spark的日志级别</p>
<p style="font-family:'Helvetica Neue', Helvetica, Verdana, Arial, sans-serif;font-size:14px;line-height:23.8px;">
4、在交互界面输入sql进行查询<br>
注：以下所用到的库和表，都是已经在hive中存在的库和表<br><br>
    如果在spark-shell中执行sql查询，使用sqlContext对象调用sql()方法<br>
scala&gt; sqlContext.sql("select remote_addr from dw_weblog.t_ods_detail group by remote_addr").collect.foreach(println)<br><br>
    如果是在spark-sql中执行sql查询，则可以直接输入sql语句<br>
scala&gt; show databases<br>
scala&gt; use dw_weblog<br>
scala&gt; select remote_addr from dw_weblog.t_ods_detail group by remote_addr<br><br><br><br>
5、在IDEA中编写代码使用hive-sql<br>
如下所示：<br>
val hiveContext = new HiveContext(sc)<br>
    import hiveContext.implicits._<br>
    import hiveContext.sql<br>
    //指定库<br>
sql("use dw_weblog")<br>
//执行标准sql语句<br>
sql("create table sparksql as select remote_addr,count(*) from t_ods_detail group by remote_addr")<br>
……<br>
综上所述，sparksql类似于hive，可以支持sql语法来对海量数据进行分析查询，跟hive不同的是，hive执行sql任务的底层运算引擎采用mapreduce运算框架，而sparksql执行sql任务的运算引擎是spark core，从而充分利用spark内存计算及DAG模型的优势，大幅提升海量数据的分析查询速度<br>
源码</p>
<p style="font-family:'Helvetica Neue', Helvetica, Verdana, Arial, sans-serif;font-size:14px;line-height:23.8px;">
 最后:</p>
<p style="font-family:'Helvetica Neue', Helvetica, Verdana, Arial, sans-serif;font-size:14px;line-height:23.8px;">
sparksql 如果连接报错可能的原因是hive元数据库的编码不是utf8的,</p>
<p style="font-family:'Helvetica Neue', Helvetica, Verdana, Arial, sans-serif;font-size:14px;line-height:23.8px;">
alter database hive character set latin1;<br>
ALTER TABLE hive.* DEFAULT CHARACTER SET latin1;</p>
<p style="font-family:'Helvetica Neue', Helvetica, Verdana, Arial, sans-serif;font-size:14px;line-height:23.8px;">
sparksql 执行创建表的时候报错</p>
<p style="font-family:'Helvetica Neue', Helvetica, Verdana, Arial, sans-serif;font-size:14px;line-height:23.8px;">
org.apache.spark.sql.execution.QueryExecutionException: FAILED: Execution Error, return code 1 from org.apache.hadoop.hive.ql.exec.DDLTask. MetaException(message:file:/user/hive/warehouse/student2 is not a directory or unable to create one)</p>
<p style="font-family:'Helvetica Neue', Helvetica, Verdana, Arial, sans-serif;font-size:14px;line-height:23.8px;">
可能是没有启动hive元数据服务</p>
            </div>
                </div>