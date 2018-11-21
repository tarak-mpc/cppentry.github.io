---
layout:     post
title:      本地开发spark代码上传spark集群服务并运行（基于spark官网文档）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/stark_summer/article/details/42528081				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>打开IDEA 在src下的main下的scala下右击创建一个scala类 名字为SimpleApp ,内容如下</p>
<p></p>
<pre style="background-color:#2b2b2b;color:#a9b7c6;font-family:'宋体';font-size:22pt;"><span style="color:#cc7832;"><strong>import </strong></span>org.apache.spark.SparkContext
<span style="color:#cc7832;"><strong>import </strong></span>org.apache.spark.SparkContext._
<span style="color:#cc7832;"><strong>import </strong></span>org.apache.spark.SparkConf
<span style="color:#cc7832;"><strong>object </strong></span><span style="background-color:#344134;">SimpleApp</span> {
  <span style="color:#cc7832;"><strong>def </strong></span><span style="color:#ffc66d;">main</span>(args: Array[String]) {
    <span style="color:#cc7832;"><strong>val </strong></span>logFile = <span style="color:#6a8759;">"/home/spark/opt/spark-1.2.0-bin-hadoop2.4/README.md" </span><span style="color:#808080;">// Should be some file on your system
</span><span style="color:#808080;">    </span><span style="color:#cc7832;"><strong>val </strong></span>conf = <span style="color:#cc7832;"><strong>new </strong></span>SparkConf().setAppName(<span style="color:#6a8759;">"Simple Application"</span>)
    <span style="color:#cc7832;"><strong>val </strong></span>sc = <span style="color:#cc7832;"><strong>new </strong></span>SparkContext(conf)
    <span style="color:#cc7832;"><strong>val </strong></span>logData = sc.textFile(logFile<span style="color:#cc7832;">, </span><span style="color:#6897bb;">2</span>).cache()
    <span style="color:#cc7832;"><strong>val </strong></span>numAs = logData.filter(line =&gt; line.contains(<span style="color:#6a8759;">"a"</span>)).count()
    <span style="color:#cc7832;"><strong>val </strong></span>numBs = logData.filter(line =&gt; line.contains(<span style="color:#6a8759;">"b"</span>)).count()
    println(<span style="color:#6a8759;">"Lines with a: %s, Lines with b: %s"</span>.format(numAs<span style="color:#cc7832;">, </span>numBs))
  }
}</pre>
打包文件：
<p></p>
<p>File--&gt;&gt;ProjectStructure --&gt;点击Artificats--&gt;&gt;点击绿色加号 --&gt; 点击JAR--&gt;&gt;选择 From module with dependices </p>
<p><img src="https://img-blog.csdn.net/20150108183941494?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Rhcmtfc3VtbWVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p>点击Output Layout  看看是否没有第三方jar包，因为你使用spark集群环境，所以你不需要第三方jar包</p>
<p><img src="https://img-blog.csdn.net/20150108183401367?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Rhcmtfc3VtbWVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p>重新build：</p>
<p>Build--&gt;&gt;Build Artifcat ..---&gt;&gt;build 或者rebuild</p>
<p><img src="https://img-blog.csdn.net/20150108183649769?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Rhcmtfc3VtbWVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p><img src="https://img-blog.csdn.net/20150108183701750?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Rhcmtfc3VtbWVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p><img src="https://img-blog.csdn.net/20150108183712795?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Rhcmtfc3VtbWVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p>执行后 就会在D:\mygit\study-scala\out\artifacts\study_scala_jar目录下看到study-scala.jar</p>
<p><img src="https://img-blog.csdn.net/20150108183744187?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Rhcmtfc3VtbWVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p>上传到spark集群服务器的 spark_home下的myApp下</p>
<p><img src="https://img-blog.csdn.net/20150108184146076?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Rhcmtfc3VtbWVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p>上传/home/spark/opt/spark-1.2.0-bin-hadoop2.4/README.md到HDFS中</p>
<p><img src="https://img-blog.csdn.net/20150108184421468?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Rhcmtfc3VtbWVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p>提交spark任务：</p>
<p><span style="color:#ff6666;"> ./bin/spark-submit  --class "SimpleApp" --master local[4]  myApp/study-scala.jar</span><br></p>
<p><img src="https://img-blog.csdn.net/20150108184509671?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Rhcmtfc3VtbWVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p><img src="https://img-blog.csdn.net/20150108184635160?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Rhcmtfc3VtbWVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p>执行结果为a:60,b:29</p>
            </div>
                </div>