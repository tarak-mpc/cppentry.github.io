---
layout:     post
title:      IgniteRDD学习笔记(四)在SparkShell上部署测试IgniteRDD
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，转载请注明出处http://blog.csdn.net/wsdc0521					https://blog.csdn.net/wsdc0521/article/details/49444685				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><strong><span style="font-size:18px;">启动服务</span></strong></p>
<p>1)下载Spark到每个节点</p>
<p>2)下载Ignite到每个节点</p>
<p>3)在Master节点进入$SPARK_HOME执行下面的脚本</p>
<p></p><pre><code class="language-plain">sbin/start-master.sh</code></pre><br><span style="color:rgb(71,74,84);font-family:'Roboto Slab', serif;font-size:14px;line-height:23.7999992370605px;">master URL</span>检查SPARK日志：<span style="color:rgb(51,51,51);font-family:Consolas, 'Liberation Mono', Menlo, Courier, monospace;font-size:11.8999996185303px;line-height:23.7999992370605px;">spark://master_host:master_port</span> 
<p><span style="color:rgb(71,74,84);font-family:'Roboto Slab', serif;font-size:14px;line-height:23.7999992370605px;">Web UI url </span>检查SPARK日志：<span style="color:rgb(51,51,51);font-family:Consolas, 'Liberation Mono', Menlo, Courier, monospace;font-size:11.8999996185303px;line-height:23.7999992370605px;">http://master_host:8080</span> </p>
<p>4)在每个Worker节点上进入$SPARK_HOME执行以下脚本</p>
<p></p><pre><code class="language-plain">bin/spark-class org.apache.spark.deploy.worker.Worker spark://master_host:master_port</code></pre><br>
所有节点启动后，在WEB UI检查状态，所有子节点应该都是ALIVE的
<p>5)在每个worker节点进入$IGNITE_HOME执行下面的脚本启动igniteNodes</p>
<p></p><pre><code class="language-plain">bin/ignite.sh</code></pre><br><strong><span style="font-size:18px;">在SPARK_SHELL里测试IgniteRDD</span></strong>
<p><span style="font-size:12px;">1)启动spark shell</span></p>
<p><span style="font-size:12px;">i)通过提供maven地址来启动</span></p>
<p></p><pre><code class="language-plain">&lt;span style="font-size:14px;"&gt;./bin/spark-shell 
  --packages org.apache.ignite:ignite-spark:1.3.0
  --master spark://master_host:master_port
  --repositories http://www.gridgainsystems.com/nexus/content/repositories/external&lt;/span&gt;</code></pre><br><span style="font-size:12px;">ii)通过jar包来启动</span>
<p></p><pre><code class="language-plain">&lt;span style="font-size:14px;"&gt;./bin/spark-shell --jars path/to/ignite-core.jar,path/to/ignite-spark.jar,path/to/cache-api.jar,path/to/ignite-log4j.jar,path/to/log4j.jar --master spark://master_host:master_port&lt;/span&gt;</code></pre><br><span style="font-size:12px;">iii)如果需要spring启动(暂时不懂是什么意思)</span>
<p></p><pre><code class="language-plain">&lt;span style="font-size:14px;"&gt;./bin/spark-shell 
  --packages org.apache.ignite:ignite-spark:1.3.0,org.apache.ignite:ignite-spring:1.3.0
  --master spark://master_host:master_port
  --repositories http://www.gridgainsystems.com/nexus/content/repositories/external&lt;/span&gt;</code></pre><br><span style="font-size:12px;">2)通过默认配置来创建一个IgniteContext实例</span>
<p></p><pre><code class="language-plain">&lt;span style="font-size:14px;"&gt;import org.apache.ignite.spark._
import org.apache.ignite.configuration._

val ic = new IgniteContext[Integer, Integer](sc, () =&gt; new IgniteConfiguration())&lt;/span&gt;</code></pre>可以看到下面的提示信息：
<p></p><pre><code class="language-html">&lt;span style="font-size:14px;"&gt;ic: org.apache.ignite.spark.IgniteContext[Integer,Integer] = org.apache.ignite.spark.IgniteContext@62be2836&lt;/span&gt;</code></pre>也可以用配置文件来创造Context实例(需要添加$IGNITE_HOME环境变量)：
<p></p><pre><code class="language-plain">import org.apache.ignite.spark._
import org.apache.ignite.configuration._

val ic = new IgniteContext[Integer, Integer](sc, "config/default-config.xml")</code></pre><br>
3)通过默认配置和名为"partitioned"缓存来创建IgniteRDD实例
<p></p><pre><code class="language-plain">val sharedRDD = ic.fromCache("partitioned")</code></pre>可以看到以下信息：
<p></p><pre><code class="language-html">shareRDD: org.apache.ignite.spark.IgniteRDD[Integer,Integer] = IgniteRDD[0] at RDD at IgniteAbstractRDD.scala:27</code></pre>需要注意的是创建RDD是一个本地操作，不会在集群中缓存
<p>4)现在让spark来使用刚创建的IgniteRDD,(例：获取所有小于10的键值对)</p>
<p></p><pre><code class="language-html">sharedRDD.filter(_._2 &lt; 10).collect()</code></pre>因为此时的缓存是空的，所以得到一个空的Array
<p></p><pre><code class="language-html">res0: Array[(Integer, Integer)] = Array()</code></pre><br>
5)现在给IgniteRDD里写入一些值
<p></p><pre><code class="language-html">sharedRDD.savePairs(sc.parallelize(1 to 100000, 10).map(i =&gt; (i, i)))</code></pre>执行后，缓存里有100000个元素
<p>6)现在我们关闭spark shell，然后重新执行1-&gt;3的步骤，在另一个spark job中读取刚才的IgniteRDD</p>
<p>来测试一下有多少大于50000的值</p>
<p></p><pre><code class="language-html">sharedRDD.filter(_._2 &gt; 50000).count</code></pre>结果为
<p></p><pre><code class="language-html">res0: Long = 50000</code></pre><br><br><br><p><br><br><br><br><br><br></p>
<p><strong><span style="font-size:18px;"><br></span></strong></p>
<p><strong><span style="font-size:18px;"><br></span></strong></p>
            </div>
                </div>