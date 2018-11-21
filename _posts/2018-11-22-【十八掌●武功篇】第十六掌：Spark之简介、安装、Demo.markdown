---
layout:     post
title:      【十八掌●武功篇】第十六掌：Spark之简介、安装、Demo
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/chybin500/article/details/78686225				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p><strong>这一篇博文是【大数据技术●降龙十八掌】系列文章的其中一篇，点击查看目录：<img src="https://img-blog.csdn.net/20171116204808649?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2h5YmluNTAw/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""><a href="http://blog.csdn.net/chybin500/article/details/78551867" rel="nofollow" target="_blank">大数据技术●降龙十八掌</a><strong></strong></strong></p>

<hr>

<dl>
<dt>系列文章：</dt>
<dd><a href="http://blog.csdn.net/chybin500/article/details/78684482" rel="nofollow" target="_blank">【十八掌●武功篇】第十六掌：Spark之Scala安装和HelloWorld</a> <br>
<a href="http://blog.csdn.net/chybin500/article/details/78685808" rel="nofollow" target="_blank">【十八掌●武功篇】第十六掌：Spark之Scala语法快速概览</a> <br>
<a href="http://blog.csdn.net/chybin500/article/details/78686225" rel="nofollow" target="_blank"> 【十八掌●武功篇】第十六掌：Spark之简介、安装、Demo</a> <br>
<a href="http://blog.csdn.net/chybin500/article/details/78687122" rel="nofollow" target="_blank">【十八掌●武功篇】第十六掌：Spark之RDD简介</a></dd>
</dl>

<h3 id="一-spark功能和优势">一、  Spark功能和优势</h3>



<h4 id="1-spark功能">1. Spark功能</h4>

<p>Spark类似于MapReduce，是另一种分布式计算框架，由于MapReduce最大的痛点在于IO，包括硬盘IO和网络IO都成了限制计算的瓶颈，Spark是使用内存来计算，所以Spark是一种内存计算框架。将中间结果存入内存中，大大提高了计算的速度。不同于MapReduce只有map和reduce，Spark提供了上百种操作，功能强大。</p>



<h4 id="2-spark处理数据分三步走">2. Spark处理数据分三步走</h4>

<p>(1)读取数据：读取数据一般是从HDFS上读取数，如sc.textfile(‘/user/input’)</p>

<ul>
<li>对于Spark Core来说，将数据变为RDD。</li>
<li>对于Spark Sql来说，是将数据变为DataFrame</li>
<li>对于Streaming来说，将数据变为DStream</li>
</ul>

<p>(2) 处理数据 </p>

<ul>
<li>对于Spark Core来说，调用RDD的一系列方法。</li>
<li>对于Spark Sql来说，是调用df的一系列方法</li>
<li>对于Streaming来说，是调用dstream一系列方法</li>
</ul>

<p>这些方法大部分是高阶函数。使用各种方法来在内存中处理数据。</p>

<p>(3) 输出数据：输出数据也大部分是存入硬盘。</p>



<pre class="prettyprint"><code class="language-scala hljs ">sc.SaveAsTextFile
resultDF.write.jdbc()
resultDStream.foreach(Redis\HBase\)</code></pre>



<h4 id="3-spark优势">3. Spark优势</h4>

<p>Spark是对于海量数据的快速通用引擎。它的优势如下：</p>

<p>(1) 快</p>

<p>Spark运行快的原因一是因为运行过程中将中间结果存入内存，二是因为Spark运行前会将运行过程生成一张DAG图（有向无环图）。 <br>
当处理的源数据在文件中时，比Hadoop快10倍，当处理的源数据在内存中时，比Hadoop快100倍。</p>

<p>(2) 通用</p>

<p>可以使用Core/SQL/Streaming/Graphx/MLib/R/StructStreaming(2.0)等进行Spark计算。 <br>
处理的数据通用：可以处理HDFS/Hive/HBase/ES、JSON/JDBC等数据 <br>
Spark运行模式：Spark可以运行在本地模式、集群模式，集群模式时，可以运行在YARN上、Mesos上、Standalone集群上、云端</p>

<p>(3) 使用简单</p>

<p>可以使用Python、Scala、Java等开发。</p>



<h3 id="二-spark与mapreduce的比较">二、  Spark与MapReduce的比较</h3>

<table>
<thead>
<tr>
  <th><strong>比较项</strong></th>
  <th><strong>MapReduce</strong></th>
  <th><strong>Spark</strong></th>
</tr>
</thead>
<tbody><tr>
  <td><strong>数据存储结构</strong></td>
  <td>磁盘HDFS文件系统</td>
  <td>使用内存构建弹性分布式数据集RDD对数据进行运算和缓存</td>
</tr>
<tr>
  <td><strong>编程范式</strong></td>
  <td>Map+Reduce</td>
  <td>DAG(有向无环图)：Transformation+action</td>
</tr>
<tr>
  <td><strong>中间结果存储</strong></td>
  <td>中间结果落地磁盘，IO及序列化反序列化代价比较大</td>
  <td>中间结果存储在内存中，速度比磁盘多几个数量级</td>
</tr>
<tr>
  <td><strong>运行方式</strong></td>
  <td>Task以进程方式维护，任务启动慢</td>
  <td>Task以线程方式维护，任务启动快</td>
</tr>
</tbody></table>




<h3 id="三-spark源码编译">三、  Spark源码编译</h3>



<h4 id="1-下载spark源码">1、 下载Spark源码</h4>

<p>Spark源码下载：<a href="http://spark.apache.org/downloads.html" rel="nofollow" target="_blank">http://spark.apache.org/downloads.html</a> <br>
我们这里选择Spark1.6.1的源码进行编译。</p>

<p><img src="https://img-blog.csdn.net/20171201140926313?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2h5YmluNTAw/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>



<h4 id="2-编译要求">2、 编译要求</h4>

<p>Spark编译官方文档地址为：<a href="http://spark.apache.org/docs/1.6.1/building-spark.html" rel="nofollow" target="_blank">http://spark.apache.org/docs/1.6.1/building-spark.html</a> <br>
Spark源码编译有三种方式：SBT编译、Maven编译、打包编译 <br>
官方文档上讲到Spark1.6.1编译要求Maven版本最低是3.3.3，Java版本最低是7。</p>



<h4 id="3-注意事项">3、 注意事项</h4>

<p>(1) 修改make-distribution.sh文件</p>

<p>make-distribution.sh在源码的根目录下，脚本里有动态查找Spark版本、Scala版本、Hadoop版本、Hive版本的代码，如果编译时去计算会很慢，可以直接将版本写死，可以提高编译速度。</p>



<pre class="prettyprint"><code class="language-sh hljs bash">原来的配置：
VERSION=$(<span class="hljs-string">"<span class="hljs-variable">$MVN</span>"</span> <span class="hljs-built_in">help</span>:evaluate -Dexpression=project.version <span class="hljs-variable">$@</span> <span class="hljs-number">2</span>&gt;/dev/null | grep -v <span class="hljs-string">"INFO"</span> | tail -n <span class="hljs-number">1</span>)
SCALA_VERSION=$(<span class="hljs-string">"<span class="hljs-variable">$MVN</span>"</span> <span class="hljs-built_in">help</span>:evaluate -Dexpression=scala.binary.version <span class="hljs-variable">$@</span> <span class="hljs-number">2</span>&gt;/dev/null\
    | grep -v <span class="hljs-string">"INFO"</span>\
    | tail -n <span class="hljs-number">1</span>)
SPARK_HADOOP_VERSION=$(<span class="hljs-string">"<span class="hljs-variable">$MVN</span>"</span> <span class="hljs-built_in">help</span>:evaluate -Dexpression=hadoop.version <span class="hljs-variable">$@</span> <span class="hljs-number">2</span>&gt;/dev/null\
    | grep -v <span class="hljs-string">"INFO"</span>\
    | tail -n <span class="hljs-number">1</span>)
SPARK_HIVE=$(<span class="hljs-string">"<span class="hljs-variable">$MVN</span>"</span> <span class="hljs-built_in">help</span>:evaluate -Dexpression=project.activeProfiles -pl sql/hive <span class="hljs-variable">$@</span> <span class="hljs-number">2</span>&gt;/dev/null\
    | grep -v <span class="hljs-string">"INFO"</span>\
    | fgrep --count <span class="hljs-string">"&lt;id&gt;hive&lt;/id&gt;"</span>;\
    <span class="hljs-comment"># Reset exit status to 0, otherwise the script stops here if the last grep finds nothing\</span>
    <span class="hljs-comment"># because we use "set -o pipefail"</span>
    <span class="hljs-built_in">echo</span> -n)
修改为：
VERSION=<span class="hljs-number">1.6</span>.<span class="hljs-number">1</span>
SCALA_VERSION=<span class="hljs-number">2.10</span>.<span class="hljs-number">4</span>
SPARK_HADOOP_VERSION=<span class="hljs-number">2.5</span>.<span class="hljs-number">0</span>-cdh5.<span class="hljs-number">3.6</span>
SPARK_HIVE=<span class="hljs-number">1</span>
</code></pre>

<p>这里版本号一定要跟实际的情况一致。 <br>
VERSION是Spark的版本号 <br>
SPARK_HIVE为1是支持Hive，0是不支持hive</p>

<p>(2) 配置镜像</p>



<pre class="prettyprint"><code class="language-sh hljs ruby">[hadoop<span class="hljs-variable">@spark01</span>-<span class="hljs-number">61</span>cdh apache-maven-<span class="hljs-number">3.3</span>.<span class="hljs-number">3</span>]<span class="hljs-variable">$ </span>vim /opt/modules/apache-maven-<span class="hljs-number">3.3</span>.<span class="hljs-number">3</span>/conf/settings.xml</code></pre>



<pre class="prettyprint"><code class="language-xml hljs "><span class="hljs-tag">&lt;<span class="hljs-title">mirror</span>&gt;</span>
      <span class="hljs-tag">&lt;<span class="hljs-title">id</span>&gt;</span>mirrorId<span class="hljs-tag">&lt;/<span class="hljs-title">id</span>&gt;</span>
      <span class="hljs-tag">&lt;<span class="hljs-title">mirrorOf</span>&gt;</span>repositoryId<span class="hljs-tag">&lt;/<span class="hljs-title">mirrorOf</span>&gt;</span>
      <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>Human Readable Name for this Mirror.<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
      <span class="hljs-tag">&lt;<span class="hljs-title">url</span>&gt;</span>http://my.repository.com/repo/path<span class="hljs-tag">&lt;/<span class="hljs-title">url</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">mirror</span>&gt;</span></code></pre>

<p>(3) 修改下域名解析服务器配置</p>



<pre class="prettyprint"><code class="language-sh hljs ruby">[hadoop<span class="hljs-variable">@spark01</span>-<span class="hljs-number">61</span>cdh apache-maven-<span class="hljs-number">3.3</span>.<span class="hljs-number">3</span>]<span class="hljs-variable">$ </span>sudo vim /etc/resolv.conf
nameserver <span class="hljs-number">8.8</span>.<span class="hljs-number">8.8</span>
nameserver <span class="hljs-number">8.8</span>.<span class="hljs-number">4.4</span>
</code></pre>



<h4 id="4-mvn编译">4、 mvn编译</h4>



<pre class="prettyprint"><code class="language-sh hljs d">[hadoop<span class="hljs-keyword">@spark01</span>-<span class="hljs-number">61</span>cdh spark-<span class="hljs-number">1.6</span>.1]$ mvn clean <span class="hljs-keyword">package</span> -DskipTest -Phadoop-<span class="hljs-number">2.4</span> -Dhadoop.<span class="hljs-keyword">version</span>=<span class="hljs-number">2.5</span>.0 -Pyarn -Phive -Phive-thriftserver -Dmaven.test.skip=<span class="hljs-literal">true</span> -Dmaven.test.skip=<span class="hljs-literal">true</span> -e</code></pre>



<h4 id="5-make-distribution进行打包编译">5、 make-distribution进行打包编译</h4>

<p>命令参数说明</p>

<p>./make-distribution.sh –name chybin-spark-1.6.1-bin-2.5.0.tar.gz –tgz -Psparkr -Phadoop-2.5 -Phive -Phive-thriftserver –Pyarn</p>

<ul>
<li>name参数是指编译完成后tar包的名称，比如spark-1.6.1-bin-2.5.0.tar.gz</li>
<li>-Phadoop-2.5是指使用hadoop2.5版本</li>
<li>-Phive是指定spark支持hive</li>
<li>-Phive-thriftserver是指定支持hive-thriftserver</li>
<li>-Pyarn是指定支持yarn</li>
</ul>



<h3 id="四-安装spark本地部署模式">四、  安装Spark（本地部署模式）</h3>



<h4 id="1-解压spark-tar包">1、 解压spark tar包</h4>



<pre class="prettyprint"><code class="language-sh hljs ruby">[hadoop<span class="hljs-variable">@spark01</span>-<span class="hljs-number">61</span>cdh software]<span class="hljs-variable">$ </span>tar -zxf /opt/software/spark-<span class="hljs-number">1.6</span>.<span class="hljs-number">1</span>-bin-<span class="hljs-number">2.5</span>.<span class="hljs-number">0</span>-cdh5.<span class="hljs-number">3.6</span>.tgz -<span class="hljs-constant">C</span> /opt/modules
</code></pre>



<h4 id="2-检查环境">2、 检查环境</h4>



<pre class="prettyprint"><code class="language-sh hljs d">检查Java是否已经安装好
[hadoop<span class="hljs-keyword">@spark01</span>-<span class="hljs-number">61</span>cdh spark-<span class="hljs-number">1.6</span>.1-bin-<span class="hljs-number">2.5</span>.0-cdh5.3.6]$ java -<span class="hljs-keyword">version</span>
java <span class="hljs-keyword">version</span> <span class="hljs-string">"1.7.0_67"</span>
Java(TM) SE Runtime Environment (build <span class="hljs-number">1.7</span>.0_67-b01)
Java HotSpot(TM) <span class="hljs-number">64</span>-Bit Server VM (build <span class="hljs-number">24.65</span>-b04, mixed mode)
检查Scala是否已经安装好
[hadoop<span class="hljs-keyword">@spark01</span>-<span class="hljs-number">61</span>cdh spark-<span class="hljs-number">1.6</span>.1-bin-<span class="hljs-number">2.5</span>.0-cdh5.3.6]$ scala -<span class="hljs-keyword">version</span>
Scala code runner <span class="hljs-keyword">version</span> <span class="hljs-number">2.10</span>.4 -- Copyright <span class="hljs-number">2002</span>-<span class="hljs-number">2013</span>, LAMP/EPFL
</code></pre>



<h4 id="3-配置spark-envsh">3、 配置spark-env.sh</h4>

<p>从模板复制一个配置文件</p>



<pre class="prettyprint"><code class="language-sh hljs avrasm">[hadoop@spark01-<span class="hljs-number">61</span>cdh spark-<span class="hljs-number">1.6</span><span class="hljs-number">.1</span>-bin-<span class="hljs-number">2.5</span><span class="hljs-number">.0</span>-cdh5<span class="hljs-number">.3</span><span class="hljs-number">.6</span>]$ <span class="hljs-keyword">cp</span> conf/spark-env<span class="hljs-preprocessor">.sh</span><span class="hljs-preprocessor">.template</span> conf/spark-env<span class="hljs-preprocessor">.sh</span></code></pre>

<p>在spark-env.sh添加配置：</p>



<pre class="prettyprint"><code class="language-sh hljs javascript">JAVA_HOME=<span class="hljs-regexp">/opt/m</span>odules/jdk1<span class="hljs-number">.7</span><span class="hljs-number">.0</span>_67
SCALA_HOME=<span class="hljs-regexp">/opt/m</span>odules/scala-<span class="hljs-number">2.10</span><span class="hljs-number">.4</span>/bin
HADOOP_CONF_DIR=<span class="hljs-regexp">/opt/m</span>odules/hadoop-<span class="hljs-number">2.5</span><span class="hljs-number">.0</span>-cdh5<span class="hljs-number">.3</span><span class="hljs-number">.6</span>/etc/hadoop
</code></pre>



<h4 id="4-启动spark">4、 启动Spark</h4>

<p>[hadoop@spark01-61cdh spark-1.6.1-bin-2.5.0-cdh5.3.6]$ bin/spark-shell <br>
这样就进入到了Spark的shell交互式命令行。 <br>
这里请注意，在启动时的日志里有一句： <br>
16/10/12 23:40:36 INFO repl.SparkILoop: Created spark context.. <br>
Spark context available as sc. <br>
这里意思是spark会创建一个context对象叫做sc。这个sc是SparkContext，它是SparkCore的程序入口，SparkContext会创建一个RDD。</p>



<h4 id="5-web页面">5、 Web页面</h4>

<p>启动shell后，可用通过4040端口的Web页面查看监控页面。</p>

<p><img src="https://img-blog.csdn.net/20171201141610426?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2h5YmluNTAw/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>



<h3 id="五-spark-shell使用">五、  Spark Shell使用</h3>



<h4 id="1-准备数据">1、 准备数据</h4>

<p>将spark根目录下的README.md文件上传到HDFS上去 <br>
[hadoop@spark01-61cdh spark-1.6.1-bin-2.5.0-cdh5.3.6]$ hdfs dfs -put README.md /</p>



<h4 id="2-测试">2、 测试</h4>

<p>加载文件到rdd：scala&gt; val rdd=sc.textFile(“/README.md”) <br>
计算多少行：scala&gt; rdd.count <br>
计算包含Spark关键字的行数：scala&gt; rdd.filter(line=&gt;line.contains(“Spark”)).count <br>
取前5行数据：scala&gt; rdd.take(5)</p>



<h3 id="六-运行wordcount-demo">六、  运行WordCount Demo</h3>

<p>按照大数据处理三步走：</p>



<h4 id="1-读取数据">1、  读取数据</h4>



<pre class="prettyprint"><code class="language-scala hljs ">scala&gt; <span class="hljs-keyword">val</span> rdd=sc.textFile(<span class="hljs-string">"/input.txt"</span>)</code></pre>

<p>这个sc是一个SparkContext对象，textFile方法是读取HDFS上的文件，读取文件后，赋值给一个RDD对象。后续的操作都是用户RDD来操作的。</p>



<h4 id="2-处理数据">2、 处理数据</h4>

<p>●   scala&gt; var wordcountRdd=rdd.flatMap(line =&gt; line.split(” “)).map(word =&gt;(word,1)).reduceByKey((a,b)=&gt;(a+b)) <br>
flatMap是将文件中的内容根据空格分隔开后，变换为一个单词数组。 <br>
map是针对每一个单词生成一个键值对，键为单词，值为1。 <br>
reduceByKey是将每一个键值对的值根据key进行合并相加，来统计各个单词的个数。 <br>
●   scala&gt; wordcountRdd.count <br>
●   scala&gt; wordcountRdd.take(11) <br>
res4: Array[(String, Int)] = Array((min,1), (hive,2), (word,1), (hua,2), (hello,1), (zhongh,1), (spark,2), (hadoop,2), (ren,2), (work,1), (storm,1))</p>



<h4 id="3-保存数据">3、 保存数据</h4>



<pre class="prettyprint"><code class="language-scala hljs ">scala&gt; wordcountRdd.saveAsTextFile(<span class="hljs-string">"/spark-out"</span>)  </code></pre>

<p>保存到HDFS根目录下的spark-out目录下</p>



<h3 id="七-spark-topkey-demo">七、  Spark TopKey Demo</h3>



<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-comment">//1、加载数据</span>
scala&gt; <span class="hljs-keyword">val</span> rdd=sc.textFile(<span class="hljs-string">"/input.txt"</span>)

<span class="hljs-comment">//2、处理数据</span>
scala&gt; <span class="hljs-keyword">var</span> wordcountRdd=rdd.flatMap(line =&gt; line.split(<span class="hljs-string">" "</span>)).map(word =&gt;(word,<span class="hljs-number">1</span>)).reduceByKey((a,b)=&gt;(a+b))

<span class="hljs-comment">//3、根据个数排序后，取前5个</span>
scala&gt; wordcountRdd.map(tuple=&gt;(tuple._2,tuple._1)).sortByKey(<span class="hljs-keyword">false</span>).map(tuple=&gt;(tuple._2,tuple._1)).take(<span class="hljs-number">5</span>)

<span class="hljs-comment">//链式编程：</span>
sc.textFile(<span class="hljs-string">"/input.txt"</span>). flatMap(line =&gt; line.split(<span class="hljs-string">" "</span>)).map(word =&gt;(word,<span class="hljs-number">1</span>)).reduceByKey((a,b)=&gt;(a+b)). map(tuple=&gt;(tuple._2,tuple._1)).sortByKey(<span class="hljs-keyword">false</span>).map(tuple=&gt;(tuple._2,tuple._1)).take(<span class="hljs-number">5</span>)
</code></pre>



<h3 id="八-idea开发spark应用案例">八、  IDEA开发Spark应用案例</h3>



<h4 id="1-配置windows环境变量">1、 配置Windows环境变量</h4>

<p>下载winutils的windows版本,下载地址是：<a href="https://github.com/srccodes/hadoop-common-2.2.0-bin" rel="nofollow" target="_blank">https://github.com/srccodes/hadoop-common-2.2.0-bin</a>,解压到F:\windowshadoop。 <br>
设置Windows的系统环境变量： <br>
HADOOP_HOME=F:\windowshadoop\hadoop-common-2.2.0-bin <br>
path环境变量添加一个 $HADOOP_HOME\bin <br>
因为在Spark程序在Windows上运行的时候会查找HADOOP_HOME环境变量，并查找目录下的winutils.exe文件，所以要保证HADOOP_HOME下的winutils.exe可以访问到。</p>



<h4 id="2-idea创建一个scala项目">2、 IDEA创建一个Scala项目</h4>

<p>创建一个Scala项目，Project name为SparkProject， <br>
<img src="https://img-blog.csdn.net/20171201141903921?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2h5YmluNTAw/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>



<h4 id="3-创建目录">3、 创建目录</h4>

<p>在Project Structure的Modeles中添加如下目录结构，并将main目录设置为Source类型，将resources目录设置为Resources类型。</p>

<p><img src="https://img-blog.csdn.net/20171201141930940?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2h5YmluNTAw/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>



<h4 id="4-复制配置文件">4、 复制配置文件</h4>

<p>将运行时连接的Hadoop集群中的core-site.xml和hdfs-site.xml复制到resources目录下。</p>

<p><img src="https://img-blog.csdn.net/20171201141957376?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2h5YmluNTAw/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p>这两个配置文件保证了Spark程序可以正确地访问到hadoop集群。</p>



<h4 id="5-添加spark-jar包lib">5、 添加spark jar包Lib</h4>

<p>在Project Structure中的Libraries中添加jar包，将spark的五个jar包添加进来，这个五个jar包可以在spark的lib目录下找到。</p>

<p><img src="https://img-blog.csdn.net/20171201142025060?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2h5YmluNTAw/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>



<h4 id="6-编码">6、 编码</h4>

<p>在main目录下添加WordCount Scala Object文件。 <br>
<img src="https://img-blog.csdn.net/20171201142103084?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2h5YmluNTAw/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p>代码如下：</p>



<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-keyword">import</span> org.apache.spark.{SparkConf, SparkContext}

<span class="hljs-javadoc">/**
  * Created by 鸣宇淳 on 2017/10/18.
  */</span>
<span class="hljs-class"><span class="hljs-keyword">object</span> <span class="hljs-title">WordCount</span> {</span>
  <span class="hljs-keyword">def</span> main(args: Array[String]) {

    <span class="hljs-comment">//设置运行账号为hadoop，跟Hadoop的账号一致</span>
    System.setProperty(<span class="hljs-string">"HADOOP_USER_NAME"</span>, <span class="hljs-string">"hadoop"</span>);
    <span class="hljs-comment">//创建一个SparkConf</span>
    <span class="hljs-keyword">val</span> sparkConf = <span class="hljs-keyword">new</span> SparkConf()
      <span class="hljs-comment">//设置应用的名称,会在WEB监控页面中显示</span>
      .setAppName(<span class="hljs-string">"WordCountSparkApp"</span>)
      <span class="hljs-comment">//设置spark运行在本地</span>
      <span class="hljs-comment">//.setMaster("spark://spark01-61cdh.chybinmy.com:7077")</span>
      <span class="hljs-comment">//.setMaster("local")</span>
      .setMaster(args(<span class="hljs-number">0</span>))

    <span class="hljs-comment">//创建一个SparkContext</span>
    <span class="hljs-keyword">val</span> sc = <span class="hljs-keyword">new</span> SparkContext(sparkConf)

    <span class="hljs-javadoc">/** ========================================================*/</span>
    <span class="hljs-comment">//第一步，读取数据</span>
    <span class="hljs-comment">//从HDFS上读取数据</span>
    <span class="hljs-keyword">val</span> rdd = sc.textFile(args(<span class="hljs-number">1</span>))

    <span class="hljs-comment">//第二步：处理数据</span>
    <span class="hljs-keyword">val</span> wordCountRdd = rdd
      <span class="hljs-comment">//根据空格分隔符分割每行的单词，形成一个数组</span>
      .flatMap(line =&gt; line.split(<span class="hljs-string">" "</span>))
      <span class="hljs-comment">//每个单词生成一个二元组对，键为单词，值为1</span>
      .map(word =&gt; (word, <span class="hljs-number">1</span>))
      <span class="hljs-comment">//根据Key合并计算数量，得出各个单词的个数</span>
      .reduceByKey(_ + _)

    <span class="hljs-comment">//第三步：输出数据</span>
    <span class="hljs-comment">//wordCountRdd.foreach(tuple =&gt; println(tuple))</span>
    wordCountRdd.saveAsTextFile(args(<span class="hljs-number">2</span>))

    <span class="hljs-javadoc">/** ========================================================*/</span>

    <span class="hljs-comment">//停止sparkContext</span>
    sc.stop()
  }
}
</code></pre>



<h4 id="7-准备测试数据">7、 准备测试数据</h4>

<p>在HDFS上新建一个文件，内容如下：</p>



<pre class="prettyprint"><code class="language-sh hljs ruby">[hadoop<span class="hljs-variable">@bigdata</span>-<span class="hljs-number">51</span>cdh hadoop-<span class="hljs-number">2.5</span>.<span class="hljs-number">0</span>-cdh5.<span class="hljs-number">3.6</span>]<span class="hljs-variable">$ </span>hdfs dfs -cat /input.txt
hadoop spark hello word
work storm hive
zhongh hadoop ren min 
hadoop spark hive
ren hive hua hadoop
</code></pre>



<h4 id="8-调试运行">8、 调试运行</h4>

<p>在IDEA中调试运行。</p>



<h4 id="9-查看结果">9、 查看结果</h4>



<pre class="prettyprint"><code class="language-sh hljs ruby">[hadoop<span class="hljs-variable">@bigdata</span>-<span class="hljs-number">51</span>cdh hadoop-<span class="hljs-number">2.5</span>.<span class="hljs-number">0</span>-cdh5.<span class="hljs-number">3.6</span>]<span class="hljs-variable">$ </span>hdfs dfs -cat /out1018/part-<span class="hljs-number">00000</span>
(min,<span class="hljs-number">1</span>)
(spark,<span class="hljs-number">2</span>)
(hive,<span class="hljs-number">3</span>)
(hadoop,<span class="hljs-number">4</span>)
(ren,<span class="hljs-number">2</span>)
(work,<span class="hljs-number">1</span>)
(word,<span class="hljs-number">1</span>)
(hua,<span class="hljs-number">1</span>)
(hello,<span class="hljs-number">1</span>)
(zhongh,<span class="hljs-number">1</span>)
(storm,<span class="hljs-number">1</span>)
</code></pre>



<h4 id="10-打包">10、    打包</h4>

<p>(1) 添加Artifacts</p>

<p>Project Structure中的Artifacts下添加jar，选择main函数所在类。 <br>
<img src="https://img-blog.csdn.net/20171201142245509?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2h5YmluNTAw/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p>(2) 删除spark相关的jar包 <br>
因为用户代码打的jar包里不能有Hadoop、Spark相关的jar包，因为会在执行时自动添加，所以要将其他引用删除。</p>

<p><img src="https://img-blog.csdn.net/20171201142308106?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2h5YmluNTAw/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p>(3) 打包</p>

<p>在Build菜单里选择Build Artifact，然后选择Build，开始打包，打包后如下： <br>
<img src="https://img-blog.csdn.net/20171201142329229?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2h5YmluNTAw/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>



<h4 id="11-运行jar包">11、    运行jar包</h4>

<p>在Spark所在目录执行spark-submit命令： <br>
[hadoop@spark01-61cdh spark-1.6.1-bin-2.5.0-cdh5.3.6]$ bin/spark-submit SparkProject.jar “local” “/input.txt” “/out1019” –class WordCount</p>

<p>(4) 查看HDFS上的输出文件</p>



<pre class="prettyprint"><code class="language-sh hljs ruby">[hadoop<span class="hljs-variable">@bigdata</span>-<span class="hljs-number">51</span>cdh hadoop-<span class="hljs-number">2.5</span>.<span class="hljs-number">0</span>-cdh5.<span class="hljs-number">3.6</span>]<span class="hljs-variable">$ </span>hdfs dfs -cat /out1019/part-<span class="hljs-number">00000</span>
(min,<span class="hljs-number">1</span>)
(spark,<span class="hljs-number">2</span>)
(hive,<span class="hljs-number">3</span>)
(hadoop,<span class="hljs-number">4</span>)
(ren,<span class="hljs-number">2</span>)
(work,<span class="hljs-number">1</span>)
(word,<span class="hljs-number">1</span>)
(hua,<span class="hljs-number">1</span>)
(hello,<span class="hljs-number">1</span>)
(zhongh,<span class="hljs-number">1</span>)
(storm,<span class="hljs-number">1</span>)
</code></pre>

<p>可以看到，已经完成对单词的统计</p>

<hr>

<p><strong>这一篇博文是【大数据技术●降龙十八掌】系列文章的其中一篇，点击查看目录：<img src="https://img-blog.csdn.net/20171116204808649?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2h5YmluNTAw/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""><a href="http://blog.csdn.net/chybin500/article/details/78551867" rel="nofollow" target="_blank">大数据技术●降龙十八掌</a><strong></strong></strong></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>