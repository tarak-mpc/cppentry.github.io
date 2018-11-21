---
layout:     post
title:      Spark学习五：spark sql
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="spark学习五spark-sql">Spark学习五：spark sql</h1>

<p>标签（空格分隔）： Spark</p>

<hr>

<p></p><div class="toc">
<ul>
<li><a href="#spark%E5%AD%A6%E4%B9%A0%E4%BA%94spark-sql" rel="nofollow">Spark学习五spark sql</a><ul>
<li><a href="#%E4%B8%80%E6%A6%82%E8%BF%B0" rel="nofollow">一概述</a></li>
<li><a href="#%E4%BA%8Cspark%E7%9A%84%E5%8F%91%E5%B1%95%E5%8E%86%E5%8F%B2" rel="nofollow">二Spark的发展历史</a></li>
<li><a href="#%E4%B8%89spark-sql%E5%92%8Chive%E5%AF%B9%E6%AF%94" rel="nofollow">三Spark sql和hive对比</a></li>
<li><a href="#%E5%9B%9Bspark-sql-%E6%9E%B6%E6%9E%84" rel="nofollow">四spark sql 架构</a></li>
<li><a href="#%E4%BA%94sprk-sql%E8%AE%BF%E9%97%AEhive%E6%95%B0%E6%8D%AE" rel="nofollow">五sprk sql访问hive数据</a></li>
<li><a href="#%E5%85%ADcatalyst" rel="nofollow">六catalyst</a></li>
<li><a href="#%E4%B8%83thriftserver" rel="nofollow">七thriftserver </a></li>
<li><a href="#%E5%85%ABdataframe" rel="nofollow">八Dataframe</a></li>
<li><a href="#%E4%B9%9D%E5%8A%A0%E8%BD%BD%E5%A4%96%E9%83%A8%E6%95%B0%E6%8D%AE%E6%BA%90" rel="nofollow">九加载外部数据源</a></li>
<li><a href="#spark-sql%E5%BC%BA%E5%A4%A7%E8%AF%9E%E7%94%9F%E4%BA%86" rel="nofollow">Spark SQL强大诞生了</a></li>
</ul>
</li>
</ul>
</div>




<h2 id="一概述">一，概述：</h2>

<p><img src="http://static.zybuluo.com/forrestxing/avfldsfpy7jimmvghc69e979/001.PNG" alt="001.PNG-100.9kB" title=""></p>

<p><img src="http://static.zybuluo.com/forrestxing/zxxp7kwpr28vwxhnpj2cl4cc/002.PNG" alt="002.PNG-25.9kB" title=""></p>

<p><img src="http://static.zybuluo.com/forrestxing/7690tu2xftxpt6f0i58brfs1/001.PNG" alt="001.PNG-139.3kB" title=""></p>



<h2 id="二spark的发展历史">二，Spark的发展历史</h2>

<p><img src="http://static.zybuluo.com/forrestxing/qe8rfsbenf0j0fibvglfpgfk/001.PNG" alt="001.PNG-95.3kB" title=""></p>

<p><img src="http://static.zybuluo.com/forrestxing/qlhzmo8k4hvuvfv2f83cg2ah/003.PNG" alt="003.PNG-106.6kB" title=""></p>

<p><img src="http://static.zybuluo.com/forrestxing/cflafk6tx8auag06ysxiv0eu/002.PNG" alt="002.PNG-93kB" title=""></p>

<p><img src="http://static.zybuluo.com/forrestxing/25taqu81k7vo1hu7ykngubrw/005.PNG" alt="005.PNG-370.8kB" title=""></p>

<p><img src="http://static.zybuluo.com/forrestxing/9bctnacmykpzlss0b12t54md/004.PNG" alt="004.PNG-197kB" title=""></p>



<h2 id="三spark-sql和hive对比">三，Spark sql和hive对比</h2>

<p><img src="http://static.zybuluo.com/forrestxing/w8wrbnkbvn8wjboakgqz24kh/001.PNG" alt="001.PNG-158.8kB" title=""></p>

<p><img src="http://static.zybuluo.com/forrestxing/o0y305yc2zn82exm3gsd058d/002.PNG" alt="002.PNG-139.7kB" title=""></p>

<p><img src="http://static.zybuluo.com/forrestxing/glk6xxffv9v31f8c616pvsmr/003.PNG" alt="003.PNG-109.7kB" title=""></p>



<h2 id="四spark-sql-架构">四，spark sql 架构</h2>

<p><img src="http://static.zybuluo.com/forrestxing/f5c611fis4572pvqveiikuof/004.PNG" alt="004.PNG-90.6kB" title=""></p>



<h2 id="五sprk-sql访问hive数据">五，sprk sql访问hive数据</h2>

<p><img src="http://static.zybuluo.com/forrestxing/6tga9yzdui95yxc1okb6zf5k/001.PNG" alt="001.PNG-315.7kB" title=""></p>

<p><img src="http://static.zybuluo.com/forrestxing/pgroh39yy5d8xbzq9iusnqns/002.PNG" alt="002.PNG-14.4kB" title=""></p>



<pre class="prettyprint"><code class=" hljs lasso">hive<span class="hljs-attribute">-site</span><span class="hljs-built_in">.</span><span class="hljs-built_in">xml</span>需要拷贝到spark的conf目录下面</code></pre>

<p>启动方式一：</p>



<pre class="prettyprint"><code class=" hljs lasso"><span class="hljs-comment">//启动应用</span>
bin/spark<span class="hljs-attribute">-shell</span> <span class="hljs-subst">--</span>driver<span class="hljs-attribute">-class</span><span class="hljs-attribute">-path</span> jars/mysql<span class="hljs-attribute">-connector</span><span class="hljs-attribute">-java</span><span class="hljs-subst">-</span><span class="hljs-number">5.1</span><span class="hljs-number">.27</span><span class="hljs-attribute">-bin</span><span class="hljs-built_in">.</span>jar <span class="hljs-subst">--</span>master <span class="hljs-built_in">local</span><span class="hljs-preprocessor">[</span><span class="hljs-number">2</span><span class="hljs-preprocessor">]</span><span class="hljs-markup"></span></code></pre>



<pre class="prettyprint"><code class=" hljs avrasm">sqlContext<span class="hljs-preprocessor">.sql</span>(<span class="hljs-string">"show databases"</span>)<span class="hljs-preprocessor">.show</span>()
</code></pre>

<p><img src="http://static.zybuluo.com/forrestxing/ytujyid3m0jpkzgfxnfwsu57/002.PNG" alt="002.PNG-14.4kB" title=""></p>



<pre class="prettyprint"><code class=" hljs avrasm">sqlContext<span class="hljs-preprocessor">.sql</span>(<span class="hljs-string">"use default"</span>)<span class="hljs-preprocessor">.show</span>()

sqlContext<span class="hljs-preprocessor">.sql</span>(<span class="hljs-string">"show tables"</span>)<span class="hljs-preprocessor">.show</span>()</code></pre>

<p>启动方式二：</p>



<pre class="prettyprint"><code class=" hljs lasso"><span class="hljs-comment">//启动应用</span>
bin/spark<span class="hljs-attribute">-sql</span> <span class="hljs-subst">--</span>driver<span class="hljs-attribute">-class</span><span class="hljs-attribute">-path</span> jars/mysql<span class="hljs-attribute">-connector</span><span class="hljs-attribute">-java</span><span class="hljs-subst">-</span><span class="hljs-number">5.1</span><span class="hljs-number">.27</span><span class="hljs-attribute">-bin</span><span class="hljs-built_in">.</span>jar <span class="hljs-subst">--</span>master <span class="hljs-built_in">local</span><span class="hljs-preprocessor">[</span><span class="hljs-number">2</span><span class="hljs-preprocessor">]</span><span class="hljs-markup"></span></code></pre>



<pre class="prettyprint"><code class=" hljs sql"><span class="hljs-operator"><span class="hljs-keyword">show</span> databases;</span></code></pre>

<p><img src="http://static.zybuluo.com/forrestxing/0qx2th1ermd2qdvb1zjqrqev/002.PNG" alt="002.PNG-78.8kB" title=""></p>



<pre class="prettyprint"><code class=" hljs lasso"><span class="hljs-comment">//缓存</span>
<span class="hljs-keyword">cache</span> table emp;
<span class="hljs-comment">//取消缓存</span>
uncache table emp;</code></pre>

<p><img src="http://static.zybuluo.com/forrestxing/zuqout96z7uy3cokbrf4zdl4/003.PNG" alt="003.PNG-47.8kB" title=""></p>



<h2 id="六catalyst">六，catalyst</h2>

<p><img src="http://static.zybuluo.com/forrestxing/2kh9ve4215y5qot15vzhdxxd/001.PNG" alt="001.PNG-542.5kB" title=""></p>

<p><img src="http://static.zybuluo.com/forrestxing/ibtm18jx2bxnzp1ygyl52u2e/002.PNG" alt="002.PNG-61.1kB" title=""></p>

<p><img src="http://static.zybuluo.com/forrestxing/4ggerpodu8tjankpdmxxb7cl/003.PNG" alt="003.PNG-74.3kB" title=""></p>



<h2 id="七thriftserver">七,thriftserver </h2>

<p>启动服务</p>



<pre class="prettyprint"><code class=" hljs brainfuck"><span class="hljs-comment">sbin/start</span><span class="hljs-literal">-</span><span class="hljs-comment">thriftserver</span><span class="hljs-string">.</span><span class="hljs-comment">sh</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">master</span> <span class="hljs-comment">local</span><span class="hljs-title">[</span><span class="hljs-comment">2</span><span class="hljs-title">]</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">driver</span><span class="hljs-literal">-</span><span class="hljs-comment">class</span><span class="hljs-literal">-</span><span class="hljs-comment">path</span> <span class="hljs-comment">jars/mysql</span><span class="hljs-literal">-</span><span class="hljs-comment">connector</span><span class="hljs-literal">-</span><span class="hljs-comment">java</span><span class="hljs-literal">-</span><span class="hljs-comment">5</span><span class="hljs-string">.</span><span class="hljs-comment">1</span><span class="hljs-string">.</span><span class="hljs-comment">27</span><span class="hljs-literal">-</span><span class="hljs-comment">bin</span><span class="hljs-string">.</span><span class="hljs-comment">jar</span></code></pre>

<p>启动beeline客户端</p>



<pre class="prettyprint"><code class=" hljs cs">bin/beeline
beeline&gt; !connect jdbc:hive2:<span class="hljs-comment">//localhost:10000</span></code></pre>

<p><img src="http://static.zybuluo.com/forrestxing/g58fycemvxhuyuozxjr0fuh7/001.PNG" alt="001.PNG-99kB" title=""></p>



<h2 id="八dataframe">八，Dataframe</h2>

<p><img src="http://static.zybuluo.com/forrestxing/0q560gpbhgsfyjbjhyr94x7q/001.PNG" alt="001.PNG-97.9kB" title=""></p>

<p><img src="http://static.zybuluo.com/forrestxing/aepjsmt6nngxho9ev6yr8jiv/002.PNG" alt="002.PNG-255.6kB" title=""></p>

<p><img src="http://static.zybuluo.com/forrestxing/69h1eibjkox90aojf0pmg2ki/003.PNG" alt="003.PNG-215kB" title=""></p>

<p><img src="http://static.zybuluo.com/forrestxing/cdp95ps7fc2seo6c274pgx6u/004.PNG" alt="004.PNG-233.3kB" title=""></p>

<p><img src="http://static.zybuluo.com/forrestxing/xv210o6b1ftq17q8fue8m2xg/005.PNG" alt="005.PNG-257.1kB" title=""></p>

<p><img src="http://static.zybuluo.com/forrestxing/ywrwps5gdy8fea81fqb70u4h/006.PNG" alt="006.PNG-239.5kB" title=""></p>

<p><img src="http://static.zybuluo.com/forrestxing/ijn7njm4oi9wd09clkrwy1f9/007.PNG" alt="007.PNG-270.6kB" title=""></p>

<p><img src="http://static.zybuluo.com/forrestxing/w8xnzdbo0x8z8fv6h8vf86b0/008.PNG" alt="008.PNG-238.5kB" title=""></p>

<p><img src="http://static.zybuluo.com/forrestxing/xycz4ox4tqqk77110q8sp0be/009.PNG" alt="009.PNG-161.6kB" title=""></p>

<p><img src="http://static.zybuluo.com/forrestxing/59ghoztdujd25xhq0rp8vt98/%E6%88%AA%E5%9B%BE06.png" alt="截图06.png-143.5kB" title=""></p>

<p><img src="http://static.zybuluo.com/forrestxing/dv9l86cc1kmngvr61s345nw2/%E6%88%AA%E5%9B%BE07.png" alt="截图07.png-299.2kB" title=""></p>

<p><img src="http://static.zybuluo.com/forrestxing/0cecis7cwe3zuzqx9139psfd/001.PNG" alt="001.PNG-157.3kB" title=""></p>

<p><img src="http://static.zybuluo.com/forrestxing/a0612bid1p3s70lbgu9pxpj7/002.PNG" alt="002.PNG-66.7kB" title=""></p>



<h2 id="九加载外部数据源">九，加载外部数据源</h2>

<p>1，加载json数据</p>



<pre class="prettyprint"><code class=" hljs avrasm">val json_df=sqlContext<span class="hljs-preprocessor">.jsonFile</span>(<span class="hljs-string">"hdfs://study.com.cn:8020/spark/people.json"</span>)

json_df<span class="hljs-preprocessor">.show</span>()</code></pre>

<p>2,加载hive数据</p>



<pre class="prettyprint"><code class=" hljs avrasm">sqlContext<span class="hljs-preprocessor">.table</span>(<span class="hljs-string">"default"</span>)<span class="hljs-preprocessor">.show</span>()</code></pre>

<p>3,加载parquet格式数据</p>



<pre class="prettyprint"><code class=" hljs avrasm">val parquet_df=sqlContext<span class="hljs-preprocessor">.jsonFile</span>(<span class="hljs-string">"hdfs://study.com.cn:8020/spark/users.parquet"</span>)
parquet_df<span class="hljs-preprocessor">.show</span>()</code></pre>

<p>4,jdbc方式获取数据</p>



<pre class="prettyprint"><code class=" hljs lasso">val df <span class="hljs-subst">=</span> sqlContext<span class="hljs-built_in">.</span>jdbc(<span class="hljs-string">"jdbc:mysql://localhost:3306/db_0306?user=root&amp;password=123456"</span>, <span class="hljs-string">"my_user"</span>)

val mysql_df <span class="hljs-subst">=</span> sqlContext<span class="hljs-built_in">.</span>load(<span class="hljs-string">"jdbc"</span>, <span class="hljs-built_in">Map</span>(<span class="hljs-string">"url"</span> <span class="hljs-subst">-&gt; </span><span class="hljs-string">"jdbc:mysql://localhost:3306/db_0306?user=root&amp;password=123456"</span>,<span class="hljs-string">"dbtable"</span> <span class="hljs-subst">-&gt; </span><span class="hljs-string">"my_user"</span>))
</code></pre>

<p>5,读取text file <br>
第一种方式：</p>



<pre class="prettyprint"><code class=" hljs scala"><span class="hljs-class"><span class="hljs-keyword">case</span> <span class="hljs-keyword">class</span> <span class="hljs-title">Person</span><span class="hljs-params">(name:String,age:Int)</span></span>
<span class="hljs-keyword">val</span> people_rdd = sc.textFile(<span class="hljs-string">"spark/sql/people.txt"</span>)
<span class="hljs-keyword">val</span> rowRdd = people_rdd.map(x =&gt; x.split(<span class="hljs-string">","</span>)).map(x =&gt; Person(x(<span class="hljs-number">0</span>), x(<span class="hljs-number">1</span>).trim.toInt))
<span class="hljs-keyword">val</span> people_df=rowRdd.toDF()</code></pre>

<p>第二种方式：</p>



<pre class="prettyprint"><code class=" hljs avrasm">val people_rdd = sc<span class="hljs-preprocessor">.textFile</span>(<span class="hljs-string">"spark/sql/people.txt"</span>)
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.sql</span>._
val rowRdd = people_rdd<span class="hljs-preprocessor">.map</span>(<span class="hljs-built_in">x</span> =&gt; <span class="hljs-built_in">x</span><span class="hljs-preprocessor">.split</span>(<span class="hljs-string">","</span>))<span class="hljs-preprocessor">.map</span>(<span class="hljs-built_in">x</span> =&gt; Row(<span class="hljs-built_in">x</span>(<span class="hljs-number">0</span>), <span class="hljs-built_in">x</span>(<span class="hljs-number">1</span>)<span class="hljs-preprocessor">.trim</span><span class="hljs-preprocessor">.toInt</span>))

import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.sql</span><span class="hljs-preprocessor">.types</span>._
val schema = StructType(Array(StructField(<span class="hljs-string">"name"</span>,StringType, true), StructField(<span class="hljs-string">"age"</span>, IntegerType, false)))

val rdd2df = sqlContext<span class="hljs-preprocessor">.createDataFrame</span>(rowRdd, schema)</code></pre>

<p>测试：</p>



<h2 id="spark-sql强大诞生了">Spark SQL强大诞生了，</h2>

<p>Hive Table <br>
    emp <br>
MySQL Table <br>
    dept</p>

<p>针对上述两个表进行join，</p>



<pre class="prettyprint"><code class=" hljs avrasm">val hive_emp_df = sqlContext<span class="hljs-preprocessor">.table</span>(<span class="hljs-string">"db_0228.emp"</span>)
val mysql_dept_df = sqlContext<span class="hljs-preprocessor">.jdbc</span>(<span class="hljs-string">"jdbc:mysql://localhost:3306/db_0306?user=root&amp;password=123456"</span>, <span class="hljs-string">"tb_dept"</span>)
val join_df = hive_emp_df<span class="hljs-preprocessor">.join</span>(mysql_dept_df, hive_emp_df(<span class="hljs-string">"deptno"</span>) === mysql_dept_df(<span class="hljs-string">"deptno"</span>))
join_df<span class="hljs-preprocessor">.show</span></code></pre>

<p>案例分析</p>

<p>SQLLogAnalyzer.scala</p>



<pre class="prettyprint"><code class=" hljs scala"><span class="hljs-keyword">package</span> com.ibeifeng.bigdata.spark.app

<span class="hljs-keyword">import</span> org.apache.spark.sql.SQLContext
<span class="hljs-keyword">import</span> org.apache.spark.{SparkConf, SparkContext}


<span class="hljs-javadoc">/**
 * Created by XuanYu on 2016/4/17.
 */</span>

<span class="hljs-class"><span class="hljs-keyword">object</span> <span class="hljs-title">SQLLogAnalyzer</span> {</span>
  <span class="hljs-keyword">def</span> main(args: Array[String]) {

    <span class="hljs-comment">// create SparkConf instance</span>
    <span class="hljs-keyword">val</span> sparkConf = <span class="hljs-keyword">new</span> SparkConf()
      .setAppName(<span class="hljs-string">"SQLLogAnalyzer"</span>)
      .setMaster(<span class="hljs-string">"local[2]"</span>)
    <span class="hljs-comment">// create SparkContext instance</span>
    <span class="hljs-keyword">val</span> sc = <span class="hljs-keyword">new</span> SparkContext(sparkConf)

    <span class="hljs-comment">// create SQLcontext instance</span>
    <span class="hljs-keyword">val</span> sqlContext = <span class="hljs-keyword">new</span> SQLContext(sc)
    <span class="hljs-keyword">import</span> sqlContext.implicits._

    <span class="hljs-comment">// ==============================================================</span>
    <span class="hljs-comment">// input files</span>
    <span class="hljs-keyword">val</span> logFile = <span class="hljs-string">"hdfs://bigdata-senior01.ibeifeng.com:8020/user/beifeng/apache.access.log"</span> <span class="hljs-comment">//</span>

    <span class="hljs-comment">//create rdd</span>
    <span class="hljs-keyword">val</span> accessLogs_df = sc.textFile(logFile)
      <span class="hljs-javadoc">/**
       *  filter log datas
       */</span>
      .filter(ApacheAccessLog.isValidateLogLine)
      <span class="hljs-javadoc">/**
       * parse log
       */</span>
      .map(log =&gt; ApacheAccessLog.parseLogLine(log))
      .toDF()

    accessLogs_df.registerTempTable(<span class="hljs-string">"accessLogs"</span>)

    <span class="hljs-comment">// cache</span>
    accessLogs_df.cache()

<span class="hljs-comment">// =======================================================================================</span>

    <span class="hljs-comment">// compute</span>
    <span class="hljs-keyword">val</span> avgContentSize = sqlContext.sql(<span class="hljs-string">"select avg(contentSize) from accessLogs"</span>).first().get(<span class="hljs-number">0</span>)
    <span class="hljs-keyword">val</span> minContentSize = sqlContext.sql(<span class="hljs-string">"select min(contentSize) from accessLogs"</span>).first().get(<span class="hljs-number">0</span>)
    <span class="hljs-keyword">val</span> maxcontentSize = sqlContext.sql(<span class="hljs-string">"select max(contentSize) from accessLogs"</span>).first().get(<span class="hljs-number">0</span>)

    <span class="hljs-comment">// println</span>
    println(<span class="hljs-string">"Content Size Avg: %s, Min: %s , Max: %s"</span>.format(
      avgContentSize, minContentSize, maxcontentSize
    ))

    <span class="hljs-comment">//</span>
    accessLogs_df.unpersist()

    <span class="hljs-keyword">val</span> avg_df = accessLogs_df.agg(<span class="hljs-string">"contentSize"</span> -&gt; <span class="hljs-string">"avg"</span>)
    <span class="hljs-keyword">val</span> min_df = accessLogs_df.agg(<span class="hljs-string">"contentSize"</span> -&gt; <span class="hljs-string">"min"</span>)
    <span class="hljs-keyword">val</span> max_df = accessLogs_df.agg(<span class="hljs-string">"contentSize"</span> -&gt; <span class="hljs-string">"max"</span>)

    <span class="hljs-comment">// println</span>
    println(<span class="hljs-string">" === Content Size Avg: %s, Min: %s , Max: %s"</span>.format(
      avg_df.first().get(<span class="hljs-number">0</span>),min_df.first().get(<span class="hljs-number">0</span>),max_df.first().get(<span class="hljs-number">0</span>)
    ))

    <span class="hljs-comment">// ==============================================================</span>

    <span class="hljs-comment">// stop SparkContext</span>
    sc.stop()
  }

}</code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>