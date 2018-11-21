---
layout:     post
title:      Spark SQL基础学习【二】以编程方式执行Spark SQL查询
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/freefish_yzx/article/details/77622225				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>  前面我们学习了如何在Spark Shell中使用SQL完成查询，现在我们来实现在自定义的程序中编写Spark SQL查询程序。首先在maven项目的pom.xml中添加Spark SQL的依赖</p>
<h1>1.maven添加Spark SQL的依赖</h1>
<div><span style="color:#ff0000;">我这里是1.6.1</span></div>
<p></p><pre><code class="language-java">&lt;dependency&gt;
    &lt;groupId&gt;org.apache.spark&lt;/groupId&gt;
    &lt;artifactId&gt;spark-sql_2.10&lt;/artifactId&gt;
    &lt;version&gt;1.6.1&lt;/version&gt;
&lt;/dependency&gt;
</code></pre><br><h1>2.编写代码</h1>
<div><pre><code class="language-java">package SQL

import org.apache.spark.{SparkConf, SparkContext}
import org.apache.spark.sql.SQLContext

object SQLDemo {
  def main(args: Array[String]): Unit = {
    val conf=new SparkConf().setAppName("SQLDemo")//.setMaster("local[2]") 本地模式
    val sc=new SparkContext(conf)
    val sqlContext=new SQLContext(sc)
    //用户的权限
    System.setProperty("user.name","root")
    val studentRdd=sc.textFile("hdfs://hadoop01:9000/student.txt").map(line=&gt;{
      val fields=line.split(",")
      student(fields(0).toLong,fields(1),fields(2).toInt)
    })
    //导入隐式转换，如果不到入无法将RDD转换成DataFrame
    //将RDD转换成DataFrame
    import sqlContext.implicits._

    val studentDf=studentRdd.toDF
    studentDf.registerTempTable("t_student")
    sqlContext.sql("select * from t_student where age&gt;20 order by age limit 2").show()
    sc.stop()
  }
}
case class student(id:Long,name:String,age:Int)
</code></pre><br><h1>3.将程序打成jar包，上传到spark集群，提交Spark任务</h1>
<h2>3.1将程序打成jar包</h2>
<div><img src="https://img-blog.csdn.net/20170827190304973?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZnJlZWZpc2hfeXp4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></div>
<div><img src="https://img-blog.csdn.net/20170827190440459?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZnJlZWZpc2hfeXp4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></div>
<h2>3.2上传到集群</h2>
<div><img src="https://img-blog.csdn.net/20170827190717301?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZnJlZWZpc2hfeXp4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></div>
<div> 这时候你已经复制了jar包的全路径了 </div>
<div>只要Alt+p  然后put  加 粘贴就好</div>
<h2>3.3提交Spark任务</h2>
<div><pre><code class="language-java">/home/hadoop/apps/spark-1.6.1-bin-hadoop2.6/bin/spark-submit  --class  SQL.SQLDemo  --master spark://hadoop01:7077 /root/wordcount-1.0-SNAPSHOT.jar</code></pre><br>
  SQL.SQLDemo  是包名和类名</div>
<div>/root/wordcount-1.0-SNAPSHOT.jar  是你上传的jar<br></div>
<div><br></div>
<div>至此 完成！</div>
<div><br></div>
</div>
<p></p>
            </div>
                </div>