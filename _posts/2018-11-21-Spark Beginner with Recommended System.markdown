---
layout:     post
title:      Spark Beginner with Recommended System
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p></p><div class="toc"><div class="toc">
<ul>
<li><a href="#spark-beginner-step-by-step" rel="nofollow">Spark Beginner Step by Step</a><ul>
<li><a href="#chapter-1-spark-installation" rel="nofollow">Chapter 1 Spark Installation</a><ul>
<li><a href="#download" rel="nofollow">Download</a></li>
<li><a href="#install" rel="nofollow">Install</a></li>
<li><a href="#start-python-shell" rel="nofollow">Start Python Shell</a></li>
<li><a href="#building-spark-cluster" rel="nofollow">Building Spark Cluster</a><ul>
<li><a href="#start-mastersh" rel="nofollow">start-mastersh</a></li>
<li><a href="#start-slavesh" rel="nofollow">start-slavesh</a></li>
<li><a href="#cluster-information" rel="nofollow">Cluster Information</a></li>
</ul>
</li>
</ul>
</li>
<li><a href="#chapter-2-hello-world-in-spark" rel="nofollow">Chapter 2 Hello World in Spark</a></li>
<li><a href="#chapter-3-understand-rdd-operations-transformations-and-actions" rel="nofollow">Chapter 3 Understand RDD Operations Transformations and Actions</a><ul>
<li><a href="#rdd-abstraction" rel="nofollow">RDD Abstraction</a></li>
<li><a href="#spark-programming-interface" rel="nofollow">Spark Programming Interface</a></li>
<li><a href="#example-console-log-mining" rel="nofollow">Example Console Log Mining</a></li>
<li><a href="#rdd-operations" rel="nofollow">RDD Operations</a><ul>
<li><a href="#example" rel="nofollow">Example</a><ul>
<li><a href="#flatmap-operation" rel="nofollow">Flatmap Operation</a></li>
</ul>
</li>
<li><a href="#transformation-lazy-evaluation-will-bring-us-more-chance-of-optimizing-our-job" rel="nofollow">Transformation  Lazy Evaluation will Bring us More Chance of Optimizing Our Job</a></li>
<li><a href="#rdd-dependency-types-and-the-optimization-at-dag-scheduler" rel="nofollow">RDD Dependency Types and the Optimization at DAG Scheduler</a></li>
</ul>
</li>
</ul>
</li>
<li><a href="#chapter-4-film-recommendation-system-with-spark-mllib" rel="nofollow">Chapter 4 Film Recommendation System with Spark MLlib</a><ul>
<li><a href="#moive-recommend-system-introduction" rel="nofollow">Moive Recommend System Introduction</a></li>
<li><a href="#movie-data" rel="nofollow">Movie Data</a></li>
<li><a href="#parameters-of-als-in-spark-mllib" rel="nofollow">Parameters of ALS in Spark MLlib</a></li>
<li><a href="#using-implicit-training" rel="nofollow">Using Implicit Training</a></li>
<li><a href="#predict-model" rel="nofollow">Predict Model</a></li>
<li><a href="#recommend-algorithms" rel="nofollow">Recommend Algorithms</a></li>
<li><a href="#matrix-factorization" rel="nofollow">Matrix Factorization</a><ul>
<li><a href="#explicit-matrix-factorization" rel="nofollow">Explicit Matrix Factorization</a></li>
<li><a href="#implicit-matrix-factorization" rel="nofollow">Implicit Matrix Factorization</a></li>
</ul>
</li>
<li><a href="#als-algorithm" rel="nofollow">ALS Algorithm</a></li>
<li><a href="#iteration-scenario" rel="nofollow">Iteration Scenario</a></li>
<li><a href="#envaluation" rel="nofollow">Envaluation</a></li>
</ul>
</li>
</ul>
</li>
</ul>
</div>
</div>




<h1 id="spark-beginner-step-by-step">Spark Beginner Step by Step</h1>



<h2 id="chapter-1-spark-installation">Chapter 1. Spark Installation</h2>



<h3 id="download">Download</h3>

<p>进入<a href="http://spark.apache.org/downloads.html" rel="nofollow">spark的下载页面</a>，选择你需要的Spark release和package type，如下图所示<img src="https://img-blog.csdn.net/20161121081610093" alt="Download" title="">点击4中的链接可直接下载spark软件包。（随着时间的推移，新的开发环境会出现，点击spark的下载页面的超链接，按需下载）</p>



<h3 id="install">Install</h3>

<p>将下载的spark安装包直接解压</p>



<pre class="prettyprint"><code class="language-shell hljs ruby"><span class="hljs-constant">XXXXXX</span><span class="hljs-variable">@ubuntu</span><span class="hljs-symbol">:~/spark</span><span class="hljs-variable">$ </span>pwd
/home/<span class="hljs-constant">XXXXXX</span>/spark
<span class="hljs-constant">XXXXXX</span><span class="hljs-variable">@ubuntu</span><span class="hljs-symbol">:~/spark</span><span class="hljs-variable">$ </span>ls
spark-<span class="hljs-number">1.6</span>.<span class="hljs-number">0</span>-bin-hadoop2.<span class="hljs-number">6</span>.tgz
<span class="hljs-constant">XXXXXX</span><span class="hljs-variable">@ubuntu</span><span class="hljs-symbol">:~/spark</span><span class="hljs-variable">$ </span>tar xvf spark-<span class="hljs-number">1.6</span>.<span class="hljs-number">0</span>-bin-hadoop2.<span class="hljs-number">6</span>.tgz</code></pre>

<p>解压后，进入对应目录，目录结构如下</p>



<pre class="prettyprint"><code class="language-shell hljs ruby"><span class="hljs-constant">XXXXXX</span><span class="hljs-variable">@ubuntu</span><span class="hljs-symbol">:~/spark</span><span class="hljs-variable">$ </span>cd spark-<span class="hljs-number">1.6</span>.<span class="hljs-number">0</span>-bin-hadoop2.<span class="hljs-number">6</span>/
<span class="hljs-constant">XXXXXX</span><span class="hljs-variable">@ubuntu</span><span class="hljs-symbol">:~/spark/spark-</span><span class="hljs-number">1.6</span>.<span class="hljs-number">0</span>-bin-hadoop2.<span class="hljs-number">6</span><span class="hljs-variable">$ </span>ls
bin          conf  ec2       lib      licenses  python  <span class="hljs-constant">README</span>.md  sbin
<span class="hljs-constant">CHANGES</span>.txt  data  examples  <span class="hljs-constant">LICENSE</span>  <span class="hljs-constant">NOTICE</span>    <span class="hljs-constant">R</span>       <span class="hljs-constant">RELEASE</span></code></pre>



<h3 id="start-python-shell">Start Python Shell</h3>

<p>在spark的根目录下，运行bin目录下的脚本pyspark，</p>



<pre class="prettyprint"><code class="language-shell hljs ruby"><span class="hljs-constant">XXXXXX</span><span class="hljs-variable">@trusty</span><span class="hljs-symbol">:~/software/spark/spark-</span><span class="hljs-number">1.6</span>.<span class="hljs-number">0</span>-bin-hadoop2.<span class="hljs-number">6</span><span class="hljs-variable">$ </span>./bin/pyspark</code></pre>

<p>可以启动spark的python shell</p>



<pre class="prettyprint"><code class="language-shell hljs vbnet"><span class="hljs-number">16</span>/<span class="hljs-number">09</span>/<span class="hljs-number">14</span> <span class="hljs-number">11</span>:<span class="hljs-number">11</span>:<span class="hljs-number">49</span> INFO NettyBlockTransferService: Server created <span class="hljs-keyword">on</span> <span class="hljs-number">59474</span>
<span class="hljs-number">16</span>/<span class="hljs-number">09</span>/<span class="hljs-number">14</span> <span class="hljs-number">11</span>:<span class="hljs-number">11</span>:<span class="hljs-number">49</span> INFO BlockManagerMaster: Trying <span class="hljs-keyword">to</span> register BlockManager
<span class="hljs-number">16</span>/<span class="hljs-number">09</span>/<span class="hljs-number">14</span> <span class="hljs-number">11</span>:<span class="hljs-number">11</span>:<span class="hljs-number">49</span> INFO BlockManagerMasterEndpoint: Registering block manager localhost:<span class="hljs-number">59474</span> <span class="hljs-keyword">with</span> <span class="hljs-number">511.1</span> MB RAM, BlockManagerId(driver, localhost, <span class="hljs-number">59474</span>)
<span class="hljs-number">16</span>/<span class="hljs-number">09</span>/<span class="hljs-number">14</span> <span class="hljs-number">11</span>:<span class="hljs-number">11</span>:<span class="hljs-number">49</span> INFO BlockManagerMaster: Registered BlockManager
Welcome <span class="hljs-keyword">to</span>
      ____              __
     / __/__  ___ _____/ /__
    _\ \/ _ \/ _ `/ __/  <span class="hljs-comment">'_/</span>
   /__ / .__/\_,_/_/ /_/\_\   version <span class="hljs-number">1.6</span><span class="hljs-number">.0</span>
      /_/

<span class="hljs-keyword">Using</span> Python version <span class="hljs-number">2.7</span><span class="hljs-number">.6</span> (<span class="hljs-keyword">default</span>, Mar <span class="hljs-number">22</span> <span class="hljs-number">2014</span> <span class="hljs-number">22</span>:<span class="hljs-number">59</span>:<span class="hljs-number">56</span>)
SparkContext available <span class="hljs-keyword">as</span> sc, HiveContext available <span class="hljs-keyword">as</span> sqlContext.
&gt;&gt;&gt;</code></pre>

<p>在shell启动的过程中，client打印的信息较多，可以修改配置文件来改变打印级别。首先，我们结束Spark的python shell(ctrl+d)，然后修改conf下的配置文件。</p>



<pre class="prettyprint"><code class="language-shell hljs ruby"><span class="hljs-constant">XXXXXX</span><span class="hljs-variable">@trusty</span><span class="hljs-symbol">:~/software/spark/spark-</span><span class="hljs-number">1.6</span>.<span class="hljs-number">0</span>-bin-hadoop2.<span class="hljs-number">6</span>/conf<span class="hljs-variable">$ </span>cp log4j.properties.template log4j.properties</code></pre>

<p>编辑log4j.properties，将INFO替换为WARN，</p>



<pre class="prettyprint"><code class="language-shell hljs avrasm"><span class="hljs-preprocessor"># Set everything to be logged to the console</span>
 log4j<span class="hljs-preprocessor">.rootCategory</span>=WARN, console
 log4j<span class="hljs-preprocessor">.appender</span><span class="hljs-preprocessor">.console</span>=org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.log</span>4j<span class="hljs-preprocessor">.ConsoleAppender</span>
 log4j<span class="hljs-preprocessor">.appender</span><span class="hljs-preprocessor">.console</span><span class="hljs-preprocessor">.target</span>=System<span class="hljs-preprocessor">.err</span>
 log4j<span class="hljs-preprocessor">.appender</span><span class="hljs-preprocessor">.console</span><span class="hljs-preprocessor">.layout</span>=org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.log</span>4j<span class="hljs-preprocessor">.PatternLayout</span>
 log4j<span class="hljs-preprocessor">.appender</span><span class="hljs-preprocessor">.console</span><span class="hljs-preprocessor">.layout</span><span class="hljs-preprocessor">.ConversionPattern</span>=%d{yy/MM/dd HH:mm:ss} %p %c{<span class="hljs-number">1</span>}: %m%n
<span class="hljs-preprocessor"># Settings to quiet third party logs that are too verbose</span>
 log4j<span class="hljs-preprocessor">.logger</span><span class="hljs-preprocessor">.org</span><span class="hljs-preprocessor">.eclipse</span><span class="hljs-preprocessor">.jetty</span>=WARN
 log4j<span class="hljs-preprocessor">.logger</span><span class="hljs-preprocessor">.org</span><span class="hljs-preprocessor">.eclipse</span><span class="hljs-preprocessor">.jetty</span><span class="hljs-preprocessor">.util</span><span class="hljs-preprocessor">.component</span><span class="hljs-preprocessor">.AbstractLifeCycle</span>=ERROR
 log4j<span class="hljs-preprocessor">.logger</span><span class="hljs-preprocessor">.org</span><span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.repl</span><span class="hljs-preprocessor">.SparkIMain</span>$exprTyper=WARN
 log4j<span class="hljs-preprocessor">.logger</span><span class="hljs-preprocessor">.org</span><span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.repl</span><span class="hljs-preprocessor">.SparkILoop</span>$SparkILoopInterpreter=WARN</code></pre>

<p>修改后，再次启动Spark python shell，显示如下。下面的client的打印信息中已经没有了INFO级别信息，只留下了WARN级别的，</p>



<pre class="prettyprint"><code class="language-shell hljs vhdl">XXXXXX@trusty:~/software/spark/spark-<span class="hljs-number">1.6</span><span class="hljs-number">.0</span>-bin-hadoop2<span class="hljs-number">.6</span>$ ./bin/pyspark
Python <span class="hljs-number">2.7</span><span class="hljs-number">.12</span> |Anaconda <span class="hljs-number">4.1</span><span class="hljs-number">.1</span> (<span class="hljs-number">64</span>-<span class="hljs-typename">bit</span>)| (<span class="hljs-keyword">default</span>, Jul  <span class="hljs-number">2</span> <span class="hljs-number">2016</span>, <span class="hljs-number">17</span>:<span class="hljs-number">42</span>:<span class="hljs-number">40</span>)
[GCC <span class="hljs-number">4.4</span><span class="hljs-number">.7</span> <span class="hljs-number">20120313</span> (Red Hat <span class="hljs-number">4.4</span><span class="hljs-number">.7</span>-<span class="hljs-number">1</span>)] <span class="hljs-keyword">on</span> linux2
<span class="hljs-keyword">Type</span> <span class="hljs-string">"help"</span>, <span class="hljs-string">"copyright"</span>, <span class="hljs-string">"credits"</span> <span class="hljs-keyword">or</span> <span class="hljs-string">"license"</span> <span class="hljs-keyword">for</span> more information.
Anaconda <span class="hljs-keyword">is</span> brought <span class="hljs-keyword">to</span> you by Continuum Analytics.
Please check <span class="hljs-keyword">out</span>: http://continuum.io/thanks <span class="hljs-keyword">and</span> https://anaconda.org
<span class="hljs-number">16</span>/<span class="hljs-number">09</span>/<span class="hljs-number">14</span> <span class="hljs-number">16</span>:<span class="hljs-number">00</span>:<span class="hljs-number">52</span> WARN NativeCodeLoader: Unable <span class="hljs-keyword">to</span> load native-hadoop <span class="hljs-keyword">library</span> <span class="hljs-keyword">for</span> your platform... using builtin-java classes where applicable
<span class="hljs-number">16</span>/<span class="hljs-number">09</span>/<span class="hljs-number">14</span> <span class="hljs-number">16</span>:<span class="hljs-number">00</span>:<span class="hljs-number">52</span> WARN Utils: Your hostname, trusty resolves <span class="hljs-keyword">to</span> a loopback address: <span class="hljs-number">127.0</span><span class="hljs-number">.1</span><span class="hljs-number">.1</span>; using <span class="hljs-number">192.168</span><span class="hljs-number">.1</span><span class="hljs-number">.254</span> instead (<span class="hljs-keyword">on</span> interface eth0)
<span class="hljs-number">16</span>/<span class="hljs-number">09</span>/<span class="hljs-number">14</span> <span class="hljs-number">16</span>:<span class="hljs-number">00</span>:<span class="hljs-number">52</span> WARN Utils: Set SPARK_LOCAL_IP <span class="hljs-keyword">if</span> you need <span class="hljs-keyword">to</span> bind <span class="hljs-keyword">to</span> another address
Welcome <span class="hljs-keyword">to</span>
      ____              __
     / __/__  ___ _____/ /__
    _\ \/ _ \/ _ `/ __/  '_/
   /__ / .__/\_,_/_/ /_/\_\   version <span class="hljs-number">1.6</span><span class="hljs-number">.0</span>
      /_/

Using Python version <span class="hljs-number">2.7</span><span class="hljs-number">.12</span> (<span class="hljs-keyword">default</span>, Jul  <span class="hljs-number">2</span> <span class="hljs-number">2016</span> <span class="hljs-number">17</span>:<span class="hljs-number">42</span>:<span class="hljs-number">40</span>)
SparkContext available as sc, HiveContext available as sqlContext.
&gt;&gt;&gt;</code></pre>



<h3 id="building-spark-cluster">Building Spark Cluster</h3>



<h4 id="start-mastersh">start-master.sh</h4>

<p>我们使用脚本<strong>start-master.sh</strong>启动spark的master节点，命令如下</p>



<pre class="prettyprint"><code class="language-shell hljs ruby"><span class="hljs-constant">XXXXXX</span><span class="hljs-variable">@trusty</span><span class="hljs-symbol">:~/spark/spark-</span><span class="hljs-number">1.6</span>.<span class="hljs-number">0</span>-bin-hadoop2.<span class="hljs-number">6</span><span class="hljs-variable">$ </span>./sbin/start-master.sh --ip <span class="hljs-constant">XXXXXX</span></code></pre>

<p>其中，IP地址<strong>XXXXXX</strong>是由下面的命令中的<strong>inet addr</strong>获得。</p>



<pre class="prettyprint"><code class="language-shell hljs ruby"><span class="hljs-constant">XXXXXX</span><span class="hljs-variable">@trusty</span><span class="hljs-symbol">:~/spark/spark-</span><span class="hljs-number">1.6</span>.<span class="hljs-number">0</span>-bin-hadoop2.<span class="hljs-number">6</span><span class="hljs-variable">$ </span>ifconfig</code></pre>



<h4 id="start-slavesh">start-slave.sh</h4>

<p>Spark Cluster中的Worker可由脚本<strong>start-slave.sh</strong>来启动，命令如下，</p>



<pre class="prettyprint"><code class="language-shell hljs ruby"><span class="hljs-constant">XXXXXX</span><span class="hljs-variable">@trusty</span><span class="hljs-symbol">:~/software/spark/spark-</span><span class="hljs-number">1.6</span>.<span class="hljs-number">0</span>-bin-hadoop2.<span class="hljs-number">6</span>/sbin<span class="hljs-variable">$ </span>./start-slave.sh <span class="hljs-constant">XXXXXX</span><span class="hljs-symbol">:</span><span class="hljs-number">7077</span></code></pre>

<p>其中，IP地址<strong>XXXXXX</strong>指的就是master的IP地址。</p>



<h4 id="cluster-information">Cluster Information</h4>

<p>通过脚本<strong>start-master.sh</strong>和<strong>start-slave.sh</strong>可创建Spark Cluster。相关的信息，可在master节点通过网址<strong>localhost:8080</strong>查看。 <br>
<img src="https://img-blog.csdn.net/20161121081849611" alt="Cluster" title=""></p>



<h2 id="chapter-2-hello-world-in-spark">Chapter 2. “Hello World!” in Spark</h2>

<p>Spark环境搭建完毕以后，自然需要来测试一下。大数据领域的第一个程序是wordcount，就好像我们新接触一门编码语言，第一个程序就是hello world一样。接下来，我们就尝试用python shell在spark里实现word count的功能。</p>



<pre class="prettyprint"><code class="language-shell hljs vhdl">XXXXXX@trusty:~/software/spark/spark-<span class="hljs-number">1.6</span><span class="hljs-number">.0</span>-bin-hadoop2<span class="hljs-number">.6</span>$ ls
bin          data       examples  licenses      NOTICE  README.md  work
CHANGES.txt  derby.log  lib       logs          python  <span class="hljs-keyword">RELEASE</span>    workspace
conf         ec2        LICENSE   metastore_db  R       sbin
XXXXXX@trusty:~/software/spark/spark-<span class="hljs-number">1.6</span><span class="hljs-number">.0</span>-bin-hadoop2<span class="hljs-number">.6</span>$ ./bin/pyspark
Python <span class="hljs-number">2.7</span><span class="hljs-number">.12</span> |Anaconda <span class="hljs-number">4.1</span><span class="hljs-number">.1</span> (<span class="hljs-number">64</span>-<span class="hljs-typename">bit</span>)| (<span class="hljs-keyword">default</span>, Jul  <span class="hljs-number">2</span> <span class="hljs-number">2016</span>, <span class="hljs-number">17</span>:<span class="hljs-number">42</span>:<span class="hljs-number">40</span>)
[GCC <span class="hljs-number">4.4</span><span class="hljs-number">.7</span> <span class="hljs-number">20120313</span> (Red Hat <span class="hljs-number">4.4</span><span class="hljs-number">.7</span>-<span class="hljs-number">1</span>)] <span class="hljs-keyword">on</span> linux2
<span class="hljs-keyword">Type</span> <span class="hljs-string">"help"</span>, <span class="hljs-string">"copyright"</span>, <span class="hljs-string">"credits"</span> <span class="hljs-keyword">or</span> <span class="hljs-string">"license"</span> <span class="hljs-keyword">for</span> more information.
Anaconda <span class="hljs-keyword">is</span> brought <span class="hljs-keyword">to</span> you by Continuum Analytics.
Please check <span class="hljs-keyword">out</span>: http://continuum.io/thanks <span class="hljs-keyword">and</span> https://anaconda.org
<span class="hljs-number">16</span>/<span class="hljs-number">09</span>/<span class="hljs-number">14</span> <span class="hljs-number">16</span>:<span class="hljs-number">00</span>:<span class="hljs-number">52</span> WARN NativeCodeLoader: Unable <span class="hljs-keyword">to</span> load native-hadoop <span class="hljs-keyword">library</span> <span class="hljs-keyword">for</span> your platform... using builtin-java classes where applicable
<span class="hljs-number">16</span>/<span class="hljs-number">09</span>/<span class="hljs-number">14</span> <span class="hljs-number">16</span>:<span class="hljs-number">00</span>:<span class="hljs-number">52</span> WARN Utils: Your hostname, trusty resolves <span class="hljs-keyword">to</span> a loopback address: <span class="hljs-number">127.0</span><span class="hljs-number">.1</span><span class="hljs-number">.1</span>; using <span class="hljs-number">192.168</span><span class="hljs-number">.1</span><span class="hljs-number">.254</span> instead (<span class="hljs-keyword">on</span> interface eth0)
<span class="hljs-number">16</span>/<span class="hljs-number">09</span>/<span class="hljs-number">14</span> <span class="hljs-number">16</span>:<span class="hljs-number">00</span>:<span class="hljs-number">52</span> WARN Utils: Set SPARK_LOCAL_IP <span class="hljs-keyword">if</span> you need <span class="hljs-keyword">to</span> bind <span class="hljs-keyword">to</span> another address
Welcome <span class="hljs-keyword">to</span>
      ____              __
     / __/__  ___ _____/ /__
    _\ \/ _ \/ _ `/ __/  '_/
   /__ / .__/\_,_/_/ /_/\_\   version <span class="hljs-number">1.6</span><span class="hljs-number">.0</span>
      /_/

Using Python version <span class="hljs-number">2.7</span><span class="hljs-number">.12</span> (<span class="hljs-keyword">default</span>, Jul  <span class="hljs-number">2</span> <span class="hljs-number">2016</span> <span class="hljs-number">17</span>:<span class="hljs-number">42</span>:<span class="hljs-number">40</span>)
SparkContext available as sc, HiveContext available as sqlContext.
&gt;&gt;&gt; textFile = sc.textFile(<span class="hljs-string">"README.md"</span>)
&gt;&gt;&gt;</code></pre>

<p>上面命令读取README，用textFile方法创建RDD数据集，下面的命令使用RDD count方法获取文件行数，</p>



<pre class="prettyprint"><code class="language-shell hljs python"><span class="hljs-prompt">&gt;&gt;&gt; </span>textFile.count()
<span class="hljs-number">95</span></code></pre>

<p>然后，使用filter方法创建一个新的RDD数据集（包含有’Spark’的行），</p>



<pre class="prettyprint"><code class="language-shell hljs livecodeserver">&gt;&gt;&gt; linesWithSpark = textFile.<span class="hljs-built_in">filter</span>(lambda <span class="hljs-built_in">line</span>: <span class="hljs-string">"Spark"</span> <span class="hljs-operator">in</span> <span class="hljs-built_in">line</span>)</code></pre>

<p>最后，使用RDD count方法获取文件内容包含”Spark”的行数</p>



<pre class="prettyprint"><code class="language-shell hljs python"><span class="hljs-prompt">&gt;&gt;&gt; </span>linesWithSpark.count()
<span class="hljs-number">17</span></code></pre>



<h2 id="chapter-3-understand-rdd-operations-transformations-and-actions">Chapter 3. Understand RDD Operations: Transformations and Actions</h2>

<p>无论是工业界还是学术界，都已经广泛使用高级集群编程模型来处理日益增长的数据，如MapReduce和Dryad。这些系统将分布式编程简化为自动提供位置感知性调度、容错以及负载均衡，使得大量用户能够在商用集群上分析超大数据集。</p>

<p>大多数现有的集群计算系统都是基于非循环的数据流模型。从稳定的物理存储（如分布式文件系统）中加载记录，记录被传入由一组确定性操作构成的DAG，然后写回稳定存储。DAG数据流图能够在运行时自动实现任务调度和故障恢复。</p>

<p>尽管非循环数据流是一种很强大的抽象方法，但仍然有些应用无法使用这种方式描述，它们的特点是在多个并行操作之间重用工作数据集。这类应用包括： <br>
+ 机器学习和图应用中常用的迭代算法（每一步对数据执行相似的函数）； <br>
+ 交互式数据挖掘工具（用户反复查询一个数据子集）;</p>

<p>为解决上述问题，弹性分布式数据集（RDD，Resilient Distributed Datasets）应运而生。它支持基于工作集的应用，同时具有数据流模型的特点：自动容错、位置感知调度和可伸缩性。RDD允许用户在执行多个查询时显式地将工作集缓存在内存中，后续的查询能够重用工作集，这极大地提升了查询速度。</p>

<p>RDD提供了一种高度受限的共享内存模型，即RDD是只读的记录分区的集合，只能通过在其他RDD执行确定的转换操作（如map、join和group by）而创建，然而这些限制使得实现容错的开销很低。与分布式共享内存系统需要付出高昂代价的检查点和回滚机制不同，RDD通过Lineage来重建丢失的分区：一个RDD中包含了如何从其他RDD衍生所必需的相关信息，从而不需要检查点操作就可以重构丢失的数据分区。尽管RDD不是一个通用的共享内存抽象，但却具备了良好的描述能力、可伸缩性和可靠性，但却能够广泛适用于数据并行类应用。</p>

<p>Spark通过微基准和用户应用程序来评估RDD。实验表明，在处理迭代式应用上Spark比Hadoop快高达20多倍，计算数据分析类报表的性能提高了40多倍，同时能够在5-7秒的延时内交互式扫描1TB数据集。</p>

<p>本部分描述RDD（弹性分布式数据集）和编程模型。首先讨论设计目标和RDD定义，然后讨论Spark的编程模型，并给出一个示例，最后举例分析下Spark中RDD的操作。</p>



<h3 id="rdd-abstraction">RDD Abstraction</h3>

<p>RDD目标是为基于工作集的应用（即多个并行操作重用中间结果的这类应用）提供抽象，同时保持MapReduce及其相关模型的优势特性：即自动容错、位置感知性调度和可伸缩性。RDD比数据流模型更易于编程，同时基于工作集的计算也具有良好的描述能力。</p>

<p>虽然只支持粗粒度转换限制了编程模型，但RDD仍然可以很好地适用于很多应用，特别是支持数据并行的批量分析应用，包括数据挖掘、机器学习和图算法等，因为这些程序通常都会在很多记录上执行相同的操作。RDD不太适合那些异步更新共享状态的应用，例如并行web爬行器。</p>

<p>RDD是只读的、分区记录的集合。RDD只能基于在稳定物理存储中的数据集和其他已有的RDD上执行确定性操作来创建。这些确定性操作称之为转换，如map、filter、groupBy、join（转换不是程开发人员在RDD上执行的操作）。</p>

<p>RDD不需要物化。RDD含有如何从其他RDD衍生（即计算）出本RDD的相关信息（即Lineage），据此可以从物理存储的数据计算出相应的RDD分区。</p>



<h3 id="spark-programming-interface">Spark Programming Interface</h3>

<p>在Spark中，RDD被表示为对象，通过这些对象上的方法（或函数）调用转换。</p>

<p>定义RDD之后，程序员就可以在动作中使用RDD了。动作是向应用程序返回值，或向存储系统导出数据的那些操作，例如，count（返回RDD中的元素个数），collect（返回元素本身），save（将RDD输出到存储系统）。在Spark中，只有在动作第一次使用RDD时，才会计算RDD（即延迟计算）。这样在构建RDD的时候，运行时通过管道的方式传输多个转换。</p>

<p>程序员还可以从两个方面控制RDD，即缓存和分区。用户可以请求将RDD缓存，这样运行时将已经计算好的RDD分区存储起来，以加速后期的重用。缓存的RDD一般存储在内存中，但如果内存不够，可以写到磁盘上。</p>

<p>另一方面，RDD还允许用户根据关键字（key）指定分区顺序，这是一个可选的功能。目前支持哈希分区和范围分区。例如，应用程序请求将两个RDD按照同样的哈希分区方式进行分区（将同一机器上具有相同关键字的记录放在一个分区），以加速它们之间的join操作。在Pregel和HaLoop中，多次迭代之间采用一致性的分区置换策略进行优化，我们同样也允许用户指定这种优化。</p>



<h3 id="example-console-log-mining">Example: Console Log Mining</h3>

<p>本部分我们通过一个具体示例来阐述RDD。假定有一个大型网站出错，操作员想要检查Hadoop文件系统（HDFS）中的日志文件（TB级大小）来找出原因。通过使用Spark，操作员只需将日志中的错误信息装载到一组节点的内存中，然后执行交互式查询。首先，需要在Spark解释器中输入如下Python命令：</p>



<pre class="prettyprint"><code class="language-python hljs "><span class="hljs-comment"># SparkContext available as spark</span>
lines = spark.textFile(<span class="hljs-string">"hdfs://..."</span>)
errors = lines.filter(_.startsWith(<span class="hljs-string">"ERROR"</span>))
errors.cache()</code></pre>

<p>第1行从HDFS文件定义了一个RDD（即一个文本行集合），第2行获得一个过滤后的RDD，第3行请求将errors缓存起来。</p>

<p>这时集群还没有开始执行任何任务。但是，用户已经可以在这个RDD上执行对应的动作，例如统计错误消息的数目：</p>



<pre class="prettyprint"><code class="language-python hljs ">errors.count()</code></pre>

<p>用户还可以在RDD上执行更多的转换操作，并使用转换结果，如：</p>



<pre class="prettyprint"><code class="language-python hljs "><span class="hljs-comment"># Count errors mentioning MySQL:</span>
errors.filter(_.contains(<span class="hljs-string">"MySQL"</span>)).count()
<span class="hljs-comment"># Return the time fields of errors mentioning</span>
<span class="hljs-comment"># HDFS as an array (assuming time is field</span>
<span class="hljs-comment"># number 3 in a tab-separated format):</span>
errors.filter(_.contains(<span class="hljs-string">"HDFS"</span>))
    .map(_.split(<span class="hljs-string">'\t'</span>)(<span class="hljs-number">3</span>))
    .collect()</code></pre>

<p>使用errors的第一个action运行以后，Spark会把errors的分区缓存在内存中，极大地加快了后续计算速度。注意，最初的RDD lines不会被缓存。因为错误信息可能只占原数据集的很小一部分（小到足以放入内存）。</p>

<p>最后，为了说明模型的容错性，下图给出了第3个查询的Lineage图。在lines RDD上执行filter操作，得到errors，然后再filter、map后得到新的RDD，在这个RDD上执行collect操作。Spark调度器以流水线的方式执行后两个转换，向拥有errors分区缓存的节点发送一组任务。此外，如果某个errors分区丢失，Spark只在相应的lines分区上执行filter操作来重建该errors分区。 <br>
<img src="https://img-blog.csdn.net/20161121082730366" alt="Lineage" title=""></p>



<h3 id="rdd-operations">RDD Operations</h3>

<p>下图中列出了Spark中的RDD转换和动作。每个操作都给出了标识，其中方括号表示类型参数。前面说过转换是延迟操作，用于定义新的RDD；而动作启动计算操作，并向用户程序返回值或向外部存储写数据。 <br>
<img src="https://img-blog.csdn.net/20161122080545970" alt="RDD_Operation" title=""></p>

<p>注意，有些操作只对键值对可用，比如join。另外，函数名与Scala及其他函数式语言中的API匹配，例如map是一对一的映射，而flatMap是将每个输入映射为一个或多个输出（与MapReduce中的map类似）。</p>

<p>除了这些操作以外，用户还可以请求将RDD缓存起来。而且，用户还可以通过Partitioner类获取RDD的分区顺序，然后将另一个RDD按照同样的方式分区。有些操作会自动产生一个哈希或范围分区的RDD，像groupByKey，reduceByKey和sort等。</p>

<h4 id="example">Example</h4>



<h5 id="flatmap-operation">Flatmap Operation</h5>

<p>此操作，首先对每一个分区进行一个映射，然后返回一个新的flattening后的RDD数据。 <br>
<img src="https://img-blog.csdn.net/20161122074122954" alt="FlatMap" title=""></p>

<p>参考如下的代码，</p>



<pre class="prettyprint"><code class="language-python hljs "><span class="hljs-comment"># 读入一个txt文件</span>
<span class="hljs-comment"># SparkContext available as spark</span>
lines = spark.textFile(<span class="hljs-string">"hdfs://input.txt"</span>);

<span class="hljs-comment"># 产生RDD数据</span>
words = lines.flatMap(line =&gt; line.split(<span class="hljs-string">" "</span>));</code></pre>



<h4 id="transformation-lazy-evaluation-will-bring-us-more-chance-of-optimizing-our-job">Transformation &amp; Lazy Evaluation will Bring us More Chance of Optimizing Our Job</h4>

<p>那么RDD在Spark架构中是如何运行的呢？从高层次来看，主要分为三步： <br>
- 创建RDD对象； <br>
- DAGScheduler模块介入运算，计算RDD之间的依赖关系。RDD之间的依赖关系就形成了DAG； <br>
- 每一个JOB被分为多个Stage，划分Stage的一个主要依据是当前计算因子的输入是否是确定的。如果是则将其分在同一个Stage，避免多个Stage之间的消息传递开销； <br>
<img src="https://img-blog.csdn.net/20161122074210627" alt="Spark Jobs" title=""></p>

<p>根据下面的例子，分析其在spark中流程，</p>



<pre class="prettyprint"><code class="language-pyhon hljs livecodeserver">logFile = <span class="hljs-string">"YOUR_SPARK_HOME/README.md"</span>
conf = <span class="hljs-built_in">new</span> SparkConf().setAppName(<span class="hljs-string">"Simple Application"</span>)
sc = <span class="hljs-built_in">new</span> SparkContext(conf)
logData = sc.textFile(logFile, <span class="hljs-number">2</span>).cache()
numAs = logData.<span class="hljs-built_in">filter</span>(<span class="hljs-built_in">line</span> =&gt; <span class="hljs-built_in">line</span>.<span class="hljs-operator">contains</span>(<span class="hljs-string">"a"</span>)).count()
print <span class="hljs-string">'Lines with a: '</span> numAs</code></pre>

<p>首先，我们创建一个RDD通过SparkContext。然后，我们使用filter转换对RDD进行转换操作，并且使用count操作对RDD进行计数。</p>

<p>在计数操作的过程中，SparkContext首先向DAG Scheduler提交对应的Job。当DAG Scheduler获取到相关的数据和操作信息之后， <br>
1. 创建Stages； <br>
2. 然后将Stages提交到Task Scheduler； <br>
3. 如果有Stages失败，DAG Scheduler将从新提交错误的Stages。</p>

<p>Task Scheduler负责将对应的Tasks提交到集群中的任务执行者，并将对应的计算结果返回给DAG Scheduler。</p>



<h4 id="rdd-dependency-types-and-the-optimization-at-dag-scheduler">RDD Dependency Types and the Optimization at DAG Scheduler</h4>

<p>RDD之间的依赖关系可以分为两类，即： <br>
1. 窄依赖（narrow dependencies）：子RDD的每个分区依赖于常数个父分区（即与数据规模无关）； <br>
2. 宽依赖（wide dependencies）：子RDD的每个分区依赖于所有父RDD分区。例如，map产生窄依赖，而join则是宽依赖（除非父RDD被哈希分区）。 <br>
<img src="https://img-blog.csdn.net/20161122074312081" alt="Dependency" title=""></p>



<h2 id="chapter-4-film-recommendation-system-with-spark-mllib">Chapter 4. Film Recommendation System with Spark MLlib</h2>

<p>推荐算法就是利用用户的一些行为，通过一些数学算法，推测出用户可能喜欢的东西。随着电子商务规模的不断扩大，商品数量和种类不断增长，用户对于检索和推荐提出了更高的要求。由于不同用户在兴趣爱好、关注领域、个人经历等方面的不同，以满足不同用户的不同推荐需求为目的、不同人可以获得不同推荐为重要特征的个性化推荐系统应运而生。</p>

<p>推荐系统成为一个相对独立的研究方向一般被认为始自1994年明尼苏达大学GroupLens研究组推出的GroupLens系统。该系统有两大重要贡献：一是首次提出了基于协同过滤(Collaborative Filtering)来完成推荐任务的思想，二是为推荐问题建立了一个形式化的模型。基于该模型的协同过滤推荐引领了之后推荐系统在今后十几年的发展方向。</p>

<p>目前，推荐算法已经已经被广泛集成到了很多商业应用系统中，比较著名的有Netflix在线视频推荐系统、Amazon网络购物商城等。实际上，大多数的电子商务平台尤其是网络购物平台，都不同程度地集成了推荐算法，如淘宝、京东商城等。Amazon发布的数据显示，亚马逊网络书城的推荐算法为亚马逊每年贡献近三十个百分点的创收。</p>



<h3 id="moive-recommend-system-introduction">Moive Recommend System Introduction</h3>

<p>我们将在Spark上搭建一个简单的小型的电影推荐系统，实践下Spark的相关知识。</p>

<p>整个系统的工作流程描述如下： <br>
+ 某电影网站拥有可观的电影资源和用户数，通过各个用户对各个电影的评分，汇总得到了海量的用户-电影-评分数据； <br>
+ 用户在一个电影网站上看了几部电影，并都为其做了评分操作（0-5分）； <br>
+ 该电影网站的推荐系统根据用户对那几部电影的评分，要预测出在该网站的电影资源库中，有哪些电影是适合该用户，并推荐给用户后续看；</p>



<h3 id="movie-data">Movie Data</h3>

<p>在此将采用显式评级数据，这样所需的输入数据就只需包括每个评级对应的用户ID、影片ID和具体的星级。本文中使用显式数据也就是用户对movie的rating信息，这个数据来源于网络上的MovieLens<a href="http://files.grouplens.org/papers/ml-1m.zip" rel="nofollow">标准数据集</a>。首先，我们下载此数据文件，</p>



<pre class="prettyprint"><code class="language-shell hljs avrasm">curl -O http://files<span class="hljs-preprocessor">.grouplens</span><span class="hljs-preprocessor">.org</span>/papers/ml-<span class="hljs-number">1</span>m<span class="hljs-preprocessor">.zip</span>
unzip -j ml-<span class="hljs-number">1</span>m<span class="hljs-preprocessor">.zip</span> <span class="hljs-string">"*.dat"</span></code></pre>

<p>电影数据结构如下所示， <br>
<img src="https://img-blog.csdn.net/20161122074411333" alt="Rating Data" title=""></p>

<p>在Spark的python shell中，</p>



<pre class="prettyprint"><code class="language-python hljs ">Using Python version <span class="hljs-number">2.7</span><span class="hljs-number">.12</span> (default, Jul  <span class="hljs-number">2</span> <span class="hljs-number">2016</span> <span class="hljs-number">17</span>:<span class="hljs-number">42</span>:<span class="hljs-number">40</span>)
SparkContext available <span class="hljs-keyword">as</span> sc, HiveContext available <span class="hljs-keyword">as</span> sqlContext.
<span class="hljs-prompt">&gt;&gt;&gt; </span>rawData = sc.textFile(<span class="hljs-string">"ratings.dat"</span>)
<span class="hljs-prompt">&gt;&gt;&gt; </span><span class="hljs-keyword">print</span> rawData.first()
<span class="hljs-number">1</span>::<span class="hljs-number">1193</span>::<span class="hljs-number">5</span>::<span class="hljs-number">978300760</span>
<span class="hljs-prompt">&gt;&gt;&gt; </span>rawRatings = rawData.map(<span class="hljs-keyword">lambda</span> x: x.split(<span class="hljs-string">'::'</span>))
<span class="hljs-prompt">&gt;&gt;&gt; </span>rawRatings.take(<span class="hljs-number">5</span>)
[[<span class="hljs-string">u'1'</span>, <span class="hljs-string">u'1193'</span>, <span class="hljs-string">u'5'</span>, <span class="hljs-string">u'978300760'</span>], [<span class="hljs-string">u'1'</span>, <span class="hljs-string">u'661'</span>, <span class="hljs-string">u'3'</span>, <span class="hljs-string">u'978302109'</span>], [<span class="hljs-string">u'1'</span>, <span class="hljs-string">u'914'</span>, <span class="hljs-string">u'3'</span>, <span class="hljs-string">u'978301968'</span>], [<span class="hljs-string">u'1'</span>, <span class="hljs-string">u'3408'</span>, <span class="hljs-string">u'4'</span>, <span class="hljs-string">u'978300275'</span>], [<span class="hljs-string">u'1'</span>, <span class="hljs-string">u'2355'</span>, <span class="hljs-string">u'5'</span>, <span class="hljs-string">u'978824291'</span>]]
&gt;&gt;&gt;</code></pre>

<p>导入Rating和ALS模块，生成相关的用户ID-电影ID-评分三元组数据，用于电影评价系统建模，</p>



<pre class="prettyprint"><code class="language-python hljs "><span class="hljs-prompt">&gt;&gt;&gt; </span><span class="hljs-keyword">from</span> pyspark.mllib.recommendation <span class="hljs-keyword">import</span> Rating, ALS
<span class="hljs-prompt">&gt;&gt;&gt; </span>ratings = rawRatings.map(<span class="hljs-keyword">lambda</span> x: Rating(int(x[<span class="hljs-number">0</span>]),int(x[<span class="hljs-number">1</span>]),float(x[<span class="hljs-number">2</span>])))
<span class="hljs-prompt">&gt;&gt;&gt; </span><span class="hljs-keyword">print</span> ratings.take(<span class="hljs-number">5</span>)
[Rating(user=<span class="hljs-number">1</span>, product=<span class="hljs-number">1193</span>, rating=<span class="hljs-number">5.0</span>), Rating(user=<span class="hljs-number">1</span>, product=<span class="hljs-number">661</span>, rating=<span class="hljs-number">3.0</span>), Rating(user=<span class="hljs-number">1</span>, product=<span class="hljs-number">914</span>, rating=<span class="hljs-number">3.0</span>), Rating(user=<span class="hljs-number">1</span>, product=<span class="hljs-number">3408</span>, rating=<span class="hljs-number">4.0</span>), Rating(user=<span class="hljs-number">1</span>, product=<span class="hljs-number">2355</span>, rating=<span class="hljs-number">5.0</span>)]</code></pre>



<h3 id="parameters-of-als-in-spark-mllib">Parameters of ALS in Spark MLlib</h3>

<p>对于Spark中的ALS算法模型参考下图， <br>
<img src="https://img-blog.csdn.net/20161122074525210" alt="ALS_API" title=""></p>

<p>对于图中所示的相关参数，简要说明如下， <br>
+ blocks：用于并行化计算的分块个数（-1为自动分配）； <br>
+ rank：对应ALS模型中的因子个数，也就是在低阶近似矩阵中的隐含特征个数。因子个数一般越多越好。但它也会直接影响模型训练和保存时所需的内存开销，尤其是在用户和物品很多的时候。因此实践中该参数常作为训练效果与系统开销之间的调节参数。通常，其合理取值为10到200; <br>
+ iterations：对应运行时的迭代次数。ALS能确保每次迭代都能降低评级矩阵的重建误差，但一般经少数次迭代后ALS模型便已能收敛为一个比较合理的好模型。这样,大部分情况下都没必要迭代太多次; <br>
+ lambda：该参数控制模型的正则化过程，从而控制模型的过拟合情况。其值越高，正则化越严厉。该参数的赋值与实际数据的大小、特征和稀疏程度有关。和其他的机器学习模型一样,正则参数应该通过用非样本的测试数据进行交叉验证来调整;</p>

<p>作为示例，这里将使用的rank、iterations和lambda参数的值分别为50、10和0.01</p>



<pre class="prettyprint"><code class="language-python hljs "><span class="hljs-prompt">&gt;&gt;&gt; </span>model = ALS.train(ratings, <span class="hljs-number">50</span>, <span class="hljs-number">10</span>, <span class="hljs-number">0.01</span>)
<span class="hljs-number">16</span>/<span class="hljs-number">09</span>/<span class="hljs-number">20</span> <span class="hljs-number">14</span>:<span class="hljs-number">43</span>:<span class="hljs-number">06</span> WARN BLAS: Failed to load implementation <span class="hljs-keyword">from</span>: com.github.fommil.netlib.NativeSystemBLAS
<span class="hljs-number">16</span>/<span class="hljs-number">09</span>/<span class="hljs-number">20</span> <span class="hljs-number">14</span>:<span class="hljs-number">43</span>:<span class="hljs-number">06</span> WARN BLAS: Failed to load implementation <span class="hljs-keyword">from</span>: com.github.fommil.netlib.NativeRefBLAS
<span class="hljs-number">16</span>/<span class="hljs-number">09</span>/<span class="hljs-number">20</span> <span class="hljs-number">14</span>:<span class="hljs-number">43</span>:<span class="hljs-number">07</span> WARN LAPACK: Failed to load implementation <span class="hljs-keyword">from</span>: com.github.fommil.netlib.NativeSystemLAPACK
<span class="hljs-number">16</span>/<span class="hljs-number">09</span>/<span class="hljs-number">20</span> <span class="hljs-number">14</span>:<span class="hljs-number">43</span>:<span class="hljs-number">07</span> WARN LAPACK: Failed to load implementation <span class="hljs-keyword">from</span>: com.github.fommil.netlib.NativeRefLAPACK
<span class="hljs-prompt">&gt;&gt;&gt; </span>userFeatures = model.userFeatures()
<span class="hljs-prompt">&gt;&gt;&gt; </span><span class="hljs-keyword">print</span> userFeatures.take(<span class="hljs-number">2</span>)
[(<span class="hljs-number">4</span>, array(<span class="hljs-string">'d'</span>, [<span class="hljs-number">0.7637861967086792</span>, -<span class="hljs-number">0.2737080156803131</span>, -<span class="hljs-number">0.17861029505729675</span>, -<span class="hljs-number">0.15782339870929718</span>, <span class="hljs-number">0.5641644597053528</span>, -<span class="hljs-number">0.7890649437904358</span>, <span class="hljs-number">0.030582157894968987</span>, -<span class="hljs-number">0.5173158049583435</span>, -<span class="hljs-number">0.3407333791255951</span>, <span class="hljs-number">0.6648253798484802</span>, <span class="hljs-number">0.3050779402256012</span>, -<span class="hljs-number">0.21968813240528107</span>, <span class="hljs-number">0.173197939991951</span>, -<span class="hljs-number">0.6081686019897461</span>, <span class="hljs-number">0.4522302448749542</span>, -<span class="hljs-number">0.7958189845085144</span>, -<span class="hljs-number">0.08689826726913452</span>, -<span class="hljs-number">0.09954997152090073</span>, -<span class="hljs-number">0.3353758156299591</span>, -<span class="hljs-number">0.4138887822628021</span>, -<span class="hljs-number">0.7452021241188049</span>, -<span class="hljs-number">0.031780023127794266</span>, <span class="hljs-number">0.5922057032585144</span>, <span class="hljs-number">0.7325875163078308</span>, <span class="hljs-number">0.4383866488933563</span>, <span class="hljs-number">0.01752650924026966</span>, <span class="hljs-number">0.19423671066761017</span>, <span class="hljs-number">0.6452142000198364</span>, -<span class="hljs-number">0.237614706158638</span>, <span class="hljs-number">0.4996830224990845</span>, <span class="hljs-number">0.433722585439682</span>, -<span class="hljs-number">0.527918815612793</span>, -<span class="hljs-number">0.06985001266002655</span>, <span class="hljs-number">0.3345005214214325</span>, -<span class="hljs-number">0.455691397190094</span>, <span class="hljs-number">0.6103637218475342</span>, -<span class="hljs-number">0.4949045479297638</span>, -<span class="hljs-number">0.29415786266326904</span>, <span class="hljs-number">0.1458829641342163</span>, -<span class="hljs-number">0.13409490883350372</span>, -<span class="hljs-number">0.7043140530586243</span>, -<span class="hljs-number">0.3189659118652344</span>, <span class="hljs-number">0.9378584027290344</span>, <span class="hljs-number">0.3959159255027771</span>, -<span class="hljs-number">0.4623756408691406</span>, <span class="hljs-number">0.48755812644958496</span>, -<span class="hljs-number">0.6423717737197876</span>, -<span class="hljs-number">0.74339359998703</span>, -<span class="hljs-number">0.12484308332204819</span>, <span class="hljs-number">0.7191870808601379</span>])),
(<span class="hljs-number">8</span>, array(<span class="hljs-string">'d'</span>, [<span class="hljs-number">0.18474148213863373</span>, -<span class="hljs-number">0.058425139635801315</span>, -<span class="hljs-number">0.44903039932250977</span>, <span class="hljs-number">0.19399809837341309</span>, <span class="hljs-number">0.32332539558410645</span>, <span class="hljs-number">0.4514048397541046</span>, -<span class="hljs-number">0.2615528404712677</span>, <span class="hljs-number">0.4042547643184662</span>, <span class="hljs-number">0.24401091039180756</span>, <span class="hljs-number">0.22522710263729095</span>, <span class="hljs-number">0.003954704850912094</span>, -<span class="hljs-number">0.6550437808036804</span>, -<span class="hljs-number">0.10648829489946365</span>, -<span class="hljs-number">0.08467081934213638</span>, -<span class="hljs-number">0.35362696647644043</span>, -<span class="hljs-number">0.0243882704526186</span>, -<span class="hljs-number">0.49195241928100586</span>, -<span class="hljs-number">0.015496794134378433</span>, -<span class="hljs-number">0.6088727712631226</span>, -<span class="hljs-number">0.7618396282196045</span>, -<span class="hljs-number">0.5728880763053894</span>, -<span class="hljs-number">0.08133324980735779</span>, -<span class="hljs-number">0.2797628343105316</span>, <span class="hljs-number">0.6432862281799316</span>, -<span class="hljs-number">0.37794432044029236</span>, <span class="hljs-number">0.2251107096672058</span>, <span class="hljs-number">0.4254800081253052</span>, <span class="hljs-number">0.07602629065513611</span>, <span class="hljs-number">0.29608646035194397</span>, -<span class="hljs-number">0.025677282363176346</span>, <span class="hljs-number">0.20981505513191223</span>, -<span class="hljs-number">0.11121115833520889</span>, -<span class="hljs-number">0.40469440817832947</span>, -<span class="hljs-number">0.6033666133880615</span>, <span class="hljs-number">0.10213682055473328</span>, <span class="hljs-number">0.6090074181556702</span>, <span class="hljs-number">0.14255429804325104</span>, <span class="hljs-number">0.4098706841468811</span>, -<span class="hljs-number">0.12171165645122528</span>, -<span class="hljs-number">0.5418315529823303</span>, -<span class="hljs-number">0.7681489586830139</span>, -<span class="hljs-number">0.17904889583587646</span>, <span class="hljs-number">0.4459456503391266</span>, <span class="hljs-number">0.2680012583732605</span>, -<span class="hljs-number">0.432824969291687</span>, <span class="hljs-number">0.2132682204246521</span>, -<span class="hljs-number">0.5714149475097656</span>, -<span class="hljs-number">0.26383259892463684</span>, <span class="hljs-number">0.06570298224687576</span>, -<span class="hljs-number">0.07780900597572327</span>]))]
&gt;&gt;&gt;</code></pre>

<p>注意，MLlib中ALS的实现里所用的操作都是延迟性的转换操作。所以，只在当用户因子或物品因子结果RDD调用了执行操作时，实际的计算才会发生。</p>



<pre class="prettyprint"><code class="language-python hljs "><span class="hljs-prompt">&gt;&gt;&gt; </span><span class="hljs-keyword">print</span> model.userFeatures().count()
<span class="hljs-number">6040</span>
<span class="hljs-prompt">&gt;&gt;&gt; </span><span class="hljs-keyword">print</span> model.productFeatures().count()
<span class="hljs-number">3706</span>
&gt;&gt;&gt;</code></pre>



<h3 id="using-implicit-training">Using Implicit Training</h3>

<p>MLlib中标准的矩阵分解模型用于显式评级数据的处理。若要处理隐式数据，则可使用trainImplicit函数。其调用方式和标准的train模式类似,但多了一个可设置的alpha参数（也是一个正则化参数，alpha应通过测试和交叉验证法来设置）。Alpha参数指定了信心权重所应达到的基准线。该值越高则所训练出的模型越认为用户与他所没评级过的电影之间没有相关性。</p>



<h3 id="predict-model">Predict Model</h3>

<p>有了训练好的模型后便可用它来做预测。预测通常有两种：为某个用户推荐物品或找出与某个物品相关或相似的其他物品。</p>

<p>从MovieLens 100k数据集生成电影推荐。MLlib的推荐模型基于矩阵分解，因此可用模型所求得的因子矩阵来计算用户对物品的预计评级。下面只针对利用MovieLens中显式数据做推荐的情形，使用隐式模型时的方法与之类似。</p>



<pre class="prettyprint"><code class="language-python hljs "><span class="hljs-prompt">&gt;&gt;&gt; </span><span class="hljs-keyword">print</span> len(userFeatures.first()[<span class="hljs-number">1</span>])
<span class="hljs-number">50</span>
<span class="hljs-prompt">&gt;&gt;&gt; </span>predictRating = model.predict(<span class="hljs-number">789</span>, <span class="hljs-number">123</span>)
<span class="hljs-prompt">&gt;&gt;&gt; </span><span class="hljs-keyword">print</span> predictRating
<span class="hljs-number">2.22793962877</span></code></pre>

<p>可以看到,该模型预测用户789对电影123的评级为2.2279。</p>

<p>predict函数同样可以以(user, item)ID对类型的RDD对象为输入,这时它将为每一对都生成相应的预测得分。我们可以借助这个函数来同时为多个用户和物品进行预测。要为某个用户生成前K个推荐物品，可借助MatrixFactorizationModel所提供的recommendProducts函数来实现。该函数需两个输入参数：user和num。其中user是用户ID,而num是要推荐的物品个数。返回值为预测得分最高的前num个物品。这些物品的序列按得分排序。该得分为相应的用户因子向量和各个物品因子向量的点积。</p>

<p>计算给用户789推荐的前10个物品，代码如下：</p>



<pre class="prettyprint"><code class="language-python hljs "><span class="hljs-prompt">&gt;&gt;&gt; </span>topKRecs = model.recommendProducts(<span class="hljs-number">789</span>,<span class="hljs-number">10</span>)
<span class="hljs-prompt">&gt;&gt;&gt; </span><span class="hljs-keyword">for</span> rec <span class="hljs-keyword">in</span> topKRecs:
<span class="hljs-prompt">... </span>    <span class="hljs-keyword">print</span> rec
...
Rating(user=<span class="hljs-number">789</span>, product=<span class="hljs-number">2301</span>, rating=<span class="hljs-number">6.0918980985395965</span>)
Rating(user=<span class="hljs-number">789</span>, product=<span class="hljs-number">72</span>, rating=<span class="hljs-number">5.944789814994465</span>)
Rating(user=<span class="hljs-number">789</span>, product=<span class="hljs-number">3235</span>, rating=<span class="hljs-number">5.678107122287396</span>)
Rating(user=<span class="hljs-number">789</span>, product=<span class="hljs-number">2021</span>, rating=<span class="hljs-number">5.57785308220733</span>)
Rating(user=<span class="hljs-number">789</span>, product=<span class="hljs-number">2872</span>, rating=<span class="hljs-number">5.5128742571509495</span>)
Rating(user=<span class="hljs-number">789</span>, product=<span class="hljs-number">1220</span>, rating=<span class="hljs-number">5.452961264362774</span>)
Rating(user=<span class="hljs-number">789</span>, product=<span class="hljs-number">3327</span>, rating=<span class="hljs-number">5.4298917773861115</span>)
Rating(user=<span class="hljs-number">789</span>, product=<span class="hljs-number">678</span>, rating=<span class="hljs-number">5.410994833670508</span>)
Rating(user=<span class="hljs-number">789</span>, product=<span class="hljs-number">3677</span>, rating=<span class="hljs-number">5.4040372563053225</span>)
Rating(user=<span class="hljs-number">789</span>, product=<span class="hljs-number">3552</span>, rating=<span class="hljs-number">5.396600269052178</span>)</code></pre>

<p>到此，我们就已经实现了一个简单电影推荐系统。上说所有的操作，我已经打包写成了相关的python服务，其中主要功能文件的代码如下，</p>



<pre class="prettyprint"><code class="language-python hljs "><span class="hljs-comment">#!/usr/bin/env python</span>
<span class="hljs-comment"># -*- coding: utf-8 -*-</span>
<span class="hljs-string">"""
Collaborative Filtering ALS Recommender System using Spark MLlib adapted from
the Spark Summit 2014 Recommender System training example.

Usage:
    ./recommend.py train &lt;training_data_file&gt; [--partitions=&lt;n&gt;]
                   [--ranks=&lt;n&gt;] [--lambdas=&lt;n&gt;] [--iterations=&lt;n&gt;]
    ./recommend.py recommend &lt;training_data_file&gt; &lt;movies_meta_data&gt;
                   [--ratings=&lt;n&gt;] [--partitions=&lt;n&gt;] [--rank=&lt;n&gt;]
                   [--iteration=&lt;n&gt;] [--lambda=&lt;n&gt;]
    ./recommend.py metrics &lt;training_data_file&gt; &lt;movies_meta_data&gt;
    ./recommend.py (-h | --help)

Options:
    -h, --help         Show this screen and exit.
    --partitions=&lt;n&gt;   Partition count [Default: 4]
    --ranks=&lt;n&gt;        List of ranks [Default: 6,8,12]
    --lambdas=&lt;n&gt;      List of lambdas [Default: 0.1,1.0,10.0]
    --iterations=&lt;n&gt;   List of iterations [Default: 10,20]

    --ratings=&lt;n&gt;      Ratings for 5 popular films [Default: 5,4,5,5,5]
    --rank=&lt;n&gt;        Rank value [Default: 12]
    --lambda=&lt;n&gt;      Lambda value [Default: 0.1]
    --iteration=&lt;n&gt;   Iteration value [Default: 20]

Examples:
    bin/spark-submit recommend.py train ratings.dat
    bin/spark-submit recommend.py metrics ratings.dat movies.dat
    bin/spark-submit --driver-memory 2g \
        recommend.py recommend ratings.dat movies.dat
"""</span>

<span class="hljs-keyword">import</span> contextlib
<span class="hljs-keyword">import</span> itertools
<span class="hljs-keyword">from</span> math <span class="hljs-keyword">import</span> sqrt
<span class="hljs-keyword">from</span> operator <span class="hljs-keyword">import</span> add
<span class="hljs-keyword">import</span> sys

<span class="hljs-keyword">from</span> docopt <span class="hljs-keyword">import</span> docopt
<span class="hljs-keyword">from</span> pyspark <span class="hljs-keyword">import</span> SparkConf, SparkContext
<span class="hljs-keyword">from</span> pyspark.mllib.recommendation <span class="hljs-keyword">import</span> ALS


SPARK_EXECUTOR_MEMORY = <span class="hljs-string">'2g'</span>
SPARK_APP_NAME = <span class="hljs-string">'movieRecommender'</span>
SPARK_MASTER = <span class="hljs-string">'local'</span>


<span class="hljs-decorator">@contextlib.contextmanager</span>
<span class="hljs-function"><span class="hljs-keyword">def</span> <span class="hljs-title">spark_manager</span><span class="hljs-params">()</span>:</span>
    conf = SparkConf().setMaster(SPARK_MASTER) \
                      .setAppName(SPARK_APP_NAME) \
                      .set(<span class="hljs-string">"spark.executor.memory"</span>, SPARK_EXECUTOR_MEMORY)
    spark_context = SparkContext(conf=conf)

    <span class="hljs-keyword">try</span>:
        <span class="hljs-keyword">yield</span> spark_context
    <span class="hljs-keyword">finally</span>:
        spark_context.stop()


<span class="hljs-function"><span class="hljs-keyword">def</span> <span class="hljs-title">parse_rating</span><span class="hljs-params">(line)</span>:</span>
    <span class="hljs-string">"""
    Parses a rating record that's in MovieLens format.

    :param str line: userId::movieId::rating::timestamp
    """</span>
    fields = line.strip().split(<span class="hljs-string">"::"</span>)

    <span class="hljs-comment"># The data is divided into three parts for training, validation, and</span>
    <span class="hljs-comment"># testing. This is why the sets were keyed with integers &lt; 10. These </span>
    <span class="hljs-comment"># methods are very quick and scalable big-data tricks to make random </span>
    <span class="hljs-comment"># key-value buckets without using any randomizing functions.</span>
    <span class="hljs-keyword">return</span> long(fields[<span class="hljs-number">3</span>]) % <span class="hljs-number">10</span>, (int(fields[<span class="hljs-number">0</span>]), <span class="hljs-comment"># User ID</span>
                                  int(fields[<span class="hljs-number">1</span>]), <span class="hljs-comment"># Movie ID</span>
                                  float(fields[<span class="hljs-number">2</span>])) <span class="hljs-comment"># Rating</span>


<span class="hljs-function"><span class="hljs-keyword">def</span> <span class="hljs-title">compute_rmse</span><span class="hljs-params">(model, data, validation_count)</span>:</span>
    <span class="hljs-string">"""
    Compute RMSE (Root Mean Squared Error).

    :param object model:
    :param list data:
    :param integer validation_count:
    """</span>
    predictions = model.predictAll(data.map(<span class="hljs-keyword">lambda</span> x: (x[<span class="hljs-number">0</span>], x[<span class="hljs-number">1</span>])))
    predictionsAndRatings = \
        predictions.map(<span class="hljs-keyword">lambda</span> x: ((x[<span class="hljs-number">0</span>], x[<span class="hljs-number">1</span>]), x[<span class="hljs-number">2</span>])) \
                   .join(data.map(<span class="hljs-keyword">lambda</span> x: ((x[<span class="hljs-number">0</span>], x[<span class="hljs-number">1</span>]), x[<span class="hljs-number">2</span>]))) \
                   .values()
    <span class="hljs-keyword">return</span> sqrt(
        predictionsAndRatings.map(
            <span class="hljs-keyword">lambda</span> x: (x[<span class="hljs-number">0</span>] - x[<span class="hljs-number">1</span>]) ** <span class="hljs-number">2</span>
        ).reduce(add) / float(validation_count)
    )


<span class="hljs-function"><span class="hljs-keyword">def</span> <span class="hljs-title">metrics</span><span class="hljs-params">(training_data_file, movies_meta_data)</span>:</span>
    <span class="hljs-string">"""
    Print metrics for the ratings database

    :param str training_data_file: file location of ratings.dat
    :param str movies_meta_data: file location of movies.dat
    """</span>
    movies = {}

    <span class="hljs-keyword">with</span> open(movies_meta_data, <span class="hljs-string">'r'</span>) <span class="hljs-keyword">as</span> open_file:
        movies = {int(line.split(<span class="hljs-string">'::'</span>)[<span class="hljs-number">0</span>]): line.split(<span class="hljs-string">'::'</span>)[<span class="hljs-number">1</span>]
                  <span class="hljs-keyword">for</span> line <span class="hljs-keyword">in</span> open_file
                  <span class="hljs-keyword">if</span> len(line.split(<span class="hljs-string">'::'</span>)) == <span class="hljs-number">3</span>}

    <span class="hljs-comment"># with open(movies_meta_data, 'r') as open_file:</span>
    <span class="hljs-comment">#     movies_index = [int(line.split('::')[0]) for line in open_file]</span>
    <span class="hljs-comment">#     open_file.seek(0)</span>
    <span class="hljs-comment">#     movies_name = [line.split('::')[1] for line in open_file]</span>
    <span class="hljs-comment">#     movies = dict(zip(movies_index, movies_name))</span>



    <span class="hljs-comment"># The training file with all the rating is loaded as a spark Resilient </span>
    <span class="hljs-comment"># Distributed Dataset (RDD), and the parse_rating method is applied to</span>
    <span class="hljs-comment"># each line that has been read from the file. RDD is a fault-tolerant </span>
    <span class="hljs-comment"># collection of elements that can be operated on in parallel.</span>
    <span class="hljs-keyword">with</span> spark_manager() <span class="hljs-keyword">as</span> context:
        ratings = context.textFile(training_data_file) \
                         .filter(<span class="hljs-keyword">lambda</span> x: x <span class="hljs-keyword">and</span> len(x.split(<span class="hljs-string">'::'</span>)) == <span class="hljs-number">4</span>) \
                         .map(parse_rating)

        most_rated = ratings.values() \
                            .map(<span class="hljs-keyword">lambda</span> r: (r[<span class="hljs-number">1</span>], <span class="hljs-number">1</span>)) \
                            .reduceByKey(add) \
                            .map(<span class="hljs-keyword">lambda</span> r: (r[<span class="hljs-number">1</span>], r[<span class="hljs-number">0</span>])) \
                            .sortByKey(ascending=<span class="hljs-keyword">False</span>) \
                            .collect()[:<span class="hljs-number">10</span>]

    <span class="hljs-keyword">print</span>
    <span class="hljs-keyword">print</span> <span class="hljs-string">'10 most rated films:'</span>

    <span class="hljs-keyword">for</span> (ratings, movie_id) <span class="hljs-keyword">in</span> most_rated:
        <span class="hljs-keyword">print</span> <span class="hljs-string">'{:10,} #{} {}'</span>.format(ratings, movie_id, movies[movie_id])


<span class="hljs-function"><span class="hljs-keyword">def</span> <span class="hljs-title">train</span><span class="hljs-params">(training_data_file, numPartitions, ranks, lambdas, iterations)</span>:</span>
    <span class="hljs-string">"""
    Print metrics for the ratings database

    :param str training_data_file: file location of ratings.dat
    :param int numPartitions: number of partitions
    :param list ranks: list of ranks to use
    :param list lambdas: list of lambdas to use
    :param list iterations: list of iteration counts
    """</span>
    <span class="hljs-comment"># The training file with all the rating is loaded as a spark Resilient </span>
    <span class="hljs-comment"># Distributed Dataset (RDD), and the parse_rating method is applied to</span>
    <span class="hljs-comment"># each line that has been read from the file. RDD is a fault-tolerant </span>
    <span class="hljs-comment"># collection of elements that can be operated on in parallel.</span>
    <span class="hljs-keyword">with</span> spark_manager() <span class="hljs-keyword">as</span> context:
        ratings = context.textFile(training_data_file) \
                         .filter(<span class="hljs-keyword">lambda</span> x: x <span class="hljs-keyword">and</span> len(x.split(<span class="hljs-string">'::'</span>)) == <span class="hljs-number">4</span>) \
                         .map(parse_rating)

        numRatings = ratings.count()

        numUsers = ratings.values() \
                          .map(<span class="hljs-keyword">lambda</span> r: r[<span class="hljs-number">0</span>]) \
                          .distinct() \
                          .count()

        numMovies = ratings.values() \
                           .map(<span class="hljs-keyword">lambda</span> r: r[<span class="hljs-number">1</span>]) \
                           .distinct() \
                           .count()

        training = ratings.filter(<span class="hljs-keyword">lambda</span> x: x[<span class="hljs-number">0</span>] &lt; <span class="hljs-number">6</span>) \
                          .values() \
                          .repartition(numPartitions) \
                          .cache()

        validation = ratings.filter(<span class="hljs-keyword">lambda</span> x: x[<span class="hljs-number">0</span>] &gt;= <span class="hljs-number">6</span> <span class="hljs-keyword">and</span> x[<span class="hljs-number">0</span>] &lt; <span class="hljs-number">8</span>) \
                            .values() \
                            .repartition(numPartitions) \
                            .cache()

        test = ratings.filter(<span class="hljs-keyword">lambda</span> x: x[<span class="hljs-number">0</span>] &gt;= <span class="hljs-number">8</span>) \
                      .values() \
                      .cache()

        numTraining = training.count()
        numValidation = validation.count()
        numTest = test.count()

        <span class="hljs-comment"># We will test 18 combinations resulting from the cross product of 3 </span>
        <span class="hljs-comment"># different ranks (6, 8, 12), 3 different lambdas (0.1, 1.0, 10.0), </span>
        <span class="hljs-comment"># and two different numbers of iterations (10, 20). We will use</span>
        <span class="hljs-comment"># compute_rmse to compute the RMSE (Root Mean Squared Error) on the</span>
        <span class="hljs-comment"># validation set for each model. The model with the smallest RMSE on the</span>
        <span class="hljs-comment"># validation set becomes the one selected and its RMSE on the test set</span>
        <span class="hljs-comment"># is used as the final metric.</span>
        bestValidationRmse = float(<span class="hljs-string">"inf"</span>)
        bestModel, bestRank, bestLambda, bestNumIter = <span class="hljs-keyword">None</span>, <span class="hljs-number">0</span>, -<span class="hljs-number">1.0</span>, -<span class="hljs-number">1</span>

        <span class="hljs-comment"># Collaborative filtering is commonly used for recommender systems.</span>
        <span class="hljs-comment"># These techniques aim to fill in the missing entries of a user-item </span>
        <span class="hljs-comment"># association matrix, in our case, the user-movie rating matrix. MLlib </span>
        <span class="hljs-comment"># currently supports model-based collaborative filtering, in which </span>
        <span class="hljs-comment"># users and products are described by a small set of latent factors </span>
        <span class="hljs-comment"># that can be used to predict missing entries. In particular, we </span>
        <span class="hljs-comment"># implement the alternating least squares (ALS) algorithm to learn </span>
        <span class="hljs-comment"># these latent factors.</span>
        <span class="hljs-keyword">for</span> rank, lmbda, numIter <span class="hljs-keyword">in</span> itertools.product(ranks,
                                                      lambdas,
                                                      iterations):
            model = ALS.train(ratings=training,
                              rank=rank,
                              iterations=numIter,
                              lambda_=lmbda)

            validationRmse = compute_rmse(model, validation, numValidation)

            <span class="hljs-keyword">if</span> validationRmse &lt; bestValidationRmse:
                bestModel, bestValidationRmse = model, validationRmse
                bestRank, bestLambda, bestNumIter = rank, lmbda, numIter

        <span class="hljs-comment"># Evaluate the best model on the test set</span>
        testRmse = compute_rmse(bestModel, test, numTest)

    <span class="hljs-keyword">print</span>
    <span class="hljs-keyword">print</span> <span class="hljs-string">'Ratings:     {:10,}'</span>.format(numRatings)
    <span class="hljs-keyword">print</span> <span class="hljs-string">'Users:       {:10,}'</span>.format(numUsers)
    <span class="hljs-keyword">print</span> <span class="hljs-string">'Movies:      {:10,}'</span>.format(numMovies)
    <span class="hljs-keyword">print</span>
    <span class="hljs-keyword">print</span> <span class="hljs-string">'Training:    {:10,}'</span>.format(numTraining)
    <span class="hljs-keyword">print</span> <span class="hljs-string">'Validation:  {:10,}'</span>.format(numValidation)
    <span class="hljs-keyword">print</span> <span class="hljs-string">'Test:        {:10,}'</span>.format(numTest)
    <span class="hljs-keyword">print</span>
    <span class="hljs-keyword">print</span> <span class="hljs-string">'The best model was trained with:'</span>
    <span class="hljs-keyword">print</span> <span class="hljs-string">'    Rank:             {:10,}'</span>.format(bestRank)
    <span class="hljs-keyword">print</span> <span class="hljs-string">'    Lambda:           {:10,.6f}'</span>.format(bestLambda)
    <span class="hljs-keyword">print</span> <span class="hljs-string">'    Iterations:       {:10,}'</span>.format(bestNumIter)
    <span class="hljs-keyword">print</span> <span class="hljs-string">'    RMSE on test set: {:10,.6f}'</span>.format(testRmse)


<span class="hljs-function"><span class="hljs-keyword">def</span> <span class="hljs-title">recommend</span><span class="hljs-params">(training_data_file, movies_meta_data, user_ratings,
              numPartitions, rank, iterations, _lambda)</span>:</span>
    <span class="hljs-string">"""
    Recommend films to the user based on their ratings of 5 popular films

    :param str training_data_file: file location of ratings.dat
    :param str movies_meta_data: file location of movies.dat
    :param list user_ratings: list of floats of ratings for 5 popular films

    :param int numPartitions: number of partitions
    :param int rank: rank amount
    :param int iterations: iterations count
    :param float _lambda: lambda amount
    """</span>
    <span class="hljs-comment"># Collect the users ratings of 5 popular films</span>
    my_ratings = (
        (<span class="hljs-number">0</span>, <span class="hljs-number">2858</span>, user_ratings[<span class="hljs-number">0</span>]), <span class="hljs-comment"># American Beauty (1999)</span>
        (<span class="hljs-number">0</span>, <span class="hljs-number">480</span>,  user_ratings[<span class="hljs-number">1</span>]), <span class="hljs-comment"># Jurassic Park (1993)</span>
        (<span class="hljs-number">0</span>, <span class="hljs-number">589</span>,  user_ratings[<span class="hljs-number">2</span>]), <span class="hljs-comment"># Terminator 2: Judgement Day (1991)</span>
        (<span class="hljs-number">0</span>, <span class="hljs-number">2571</span>, user_ratings[<span class="hljs-number">3</span>]), <span class="hljs-comment"># Matrix, The (1999)</span>
        (<span class="hljs-number">0</span>, <span class="hljs-number">1270</span>, user_ratings[<span class="hljs-number">4</span>]), <span class="hljs-comment"># Back to the Future (1985)</span>
    )

    films_seen = set([_rating[<span class="hljs-number">1</span>] <span class="hljs-keyword">for</span> _rating <span class="hljs-keyword">in</span> my_ratings])

    <span class="hljs-keyword">with</span> spark_manager() <span class="hljs-keyword">as</span> context:
        training = context.textFile(training_data_file) \
                          .filter(<span class="hljs-keyword">lambda</span> x: x <span class="hljs-keyword">and</span> len(x.split(<span class="hljs-string">'::'</span>)) == <span class="hljs-number">4</span>) \
                          .map(parse_rating) \
                          .values() \
                          .repartition(numPartitions) \
                          .cache()

        model = ALS.train(training, rank, iterations, _lambda)

        films_rdd = context.textFile(training_data_file) \
                           .filter(<span class="hljs-keyword">lambda</span> x: x <span class="hljs-keyword">and</span> len(x.split(<span class="hljs-string">'::'</span>)) == <span class="hljs-number">4</span>) \
                           .map(parse_rating)

        films = films_rdd.values() \
                         .map(<span class="hljs-keyword">lambda</span> r: (r[<span class="hljs-number">1</span>], <span class="hljs-number">1</span>)) \
                         .reduceByKey(add) \
                         .map(<span class="hljs-keyword">lambda</span> r: r[<span class="hljs-number">0</span>]) \
                         .filter(<span class="hljs-keyword">lambda</span> r: r <span class="hljs-keyword">not</span> <span class="hljs-keyword">in</span> films_seen) \
                         .collect()

        candidates = context.parallelize(films) \
                            .map(<span class="hljs-keyword">lambda</span> x: (x, <span class="hljs-number">1</span>)) \
                            .repartition(numPartitions) \
                            .cache()

        predictions = model.predictAll(candidates).collect()

        <span class="hljs-comment"># Get the top 50 recommendations</span>
        recommendations = sorted(predictions,
                                 key=<span class="hljs-keyword">lambda</span> x: x[<span class="hljs-number">2</span>],
                                 reverse=<span class="hljs-keyword">True</span>)[:<span class="hljs-number">50</span>]

    <span class="hljs-comment"># Map each film id and name to a key, value dictionary</span>
    movies = {}

    <span class="hljs-keyword">with</span> open(movies_meta_data, <span class="hljs-string">'r'</span>) <span class="hljs-keyword">as</span> open_file:
        movies = {int(line.split(<span class="hljs-string">'::'</span>)[<span class="hljs-number">0</span>]): line.split(<span class="hljs-string">'::'</span>)[<span class="hljs-number">1</span>]
                  <span class="hljs-keyword">for</span> line <span class="hljs-keyword">in</span> open_file
                  <span class="hljs-keyword">if</span> len(line.split(<span class="hljs-string">'::'</span>)) == <span class="hljs-number">3</span>}

    <span class="hljs-comment"># with open(movies_meta_data, 'r') as open_file:</span>
    <span class="hljs-comment">#     movies_index = [int(line.split('::')[0]) for line in open_file]</span>
    <span class="hljs-comment">#     open_file.seek(0)</span>
    <span class="hljs-comment">#     movies_name = [line.split('::')[1] for line in open_file]</span>
    <span class="hljs-comment">#     movies = dict(zip(movies_index, movies_name))</span>


    <span class="hljs-keyword">for</span> movie_id, _, _ <span class="hljs-keyword">in</span> recommendations:
        <span class="hljs-keyword">print</span> movies[movie_id] <span class="hljs-keyword">if</span> movie_id <span class="hljs-keyword">in</span> movies <span class="hljs-keyword">else</span> movie_id


<span class="hljs-function"><span class="hljs-keyword">def</span> <span class="hljs-title">main</span><span class="hljs-params">(argv)</span>:</span>
    <span class="hljs-string">"""
    :param dict argv: command line arguments
    """</span>
    opt = docopt(__doc__, argv)

    <span class="hljs-keyword">if</span> opt[<span class="hljs-string">'train'</span>]:
        ranks    = [int(rank)      <span class="hljs-keyword">for</span> rank <span class="hljs-keyword">in</span> opt[<span class="hljs-string">'--ranks'</span>].split(<span class="hljs-string">','</span>)]
        lambdas  = [float(_lambda) <span class="hljs-keyword">for</span> _lambda <span class="hljs-keyword">in</span> opt[<span class="hljs-string">'--lambdas'</span>].split(<span class="hljs-string">','</span>)]
        iterations = [int(_iter)   <span class="hljs-keyword">for</span> _iter <span class="hljs-keyword">in</span> opt[<span class="hljs-string">'--iterations'</span>].split(<span class="hljs-string">','</span>)]

        train(opt[<span class="hljs-string">'&lt;training_data_file&gt;'</span>],
              int(opt[<span class="hljs-string">'--partitions'</span>]),
              ranks,
              lambdas,
              iterations)

    <span class="hljs-keyword">if</span> opt[<span class="hljs-string">'metrics'</span>]:
        metrics(opt[<span class="hljs-string">'&lt;training_data_file&gt;'</span>],
                opt[<span class="hljs-string">'&lt;movies_meta_data&gt;'</span>])

    <span class="hljs-keyword">if</span> opt[<span class="hljs-string">'recommend'</span>]:
        ratings = [float(_rating) <span class="hljs-keyword">for</span> _rating <span class="hljs-keyword">in</span> opt[<span class="hljs-string">'--ratings'</span>].split(<span class="hljs-string">','</span>)]
        recommend(training_data_file=opt[<span class="hljs-string">'&lt;training_data_file&gt;'</span>],
                  movies_meta_data=opt[<span class="hljs-string">'&lt;movies_meta_data&gt;'</span>],
                  user_ratings=ratings,
                  numPartitions=int(opt[<span class="hljs-string">'--partitions'</span>]),
                  rank=int(opt[<span class="hljs-string">'--rank'</span>]),
                  iterations=int(opt[<span class="hljs-string">'--iteration'</span>]),
                  _lambda=float(opt[<span class="hljs-string">'--lambda'</span>]))


<span class="hljs-keyword">if</span> __name__ == <span class="hljs-string">"__main__"</span>:
    <span class="hljs-keyword">try</span>:
        main(sys.argv[<span class="hljs-number">1</span>:])
    <span class="hljs-keyword">except</span> KeyboardInterrupt:
        <span class="hljs-keyword">pass</span>
</code></pre>

<p>使用如下的命令即可自动的获取推荐的电影名称，</p>



<pre class="prettyprint"><code class="language-shell hljs oxygene">XXXXXX@trusty:~/software/spark/spark-<span class="hljs-number">1.6</span>.<span class="hljs-number">0</span>-bin-hadoop2.<span class="hljs-number">6</span>/workspace/recommend$ ../../bin/spark-submit recommend.py recommend ratings.dat movies.dat --rank=<span class="hljs-number">15</span> --lambda=<span class="hljs-number">0.33</span> --iteration=<span class="hljs-number">3</span>

<span class="hljs-number">16</span>/<span class="hljs-number">09</span>/<span class="hljs-number">20</span> <span class="hljs-number">15</span>:<span class="hljs-number">44</span>:<span class="hljs-number">37</span> WARN NativeCodeLoader: Unable <span class="hljs-keyword">to</span> load native-hadoop <span class="hljs-keyword">library</span> <span class="hljs-keyword">for</span> your <span class="hljs-keyword">platform</span>... <span class="hljs-keyword">using</span> builtin-java classes <span class="hljs-keyword">where</span> applicable
<span class="hljs-number">16</span>/<span class="hljs-number">09</span>/<span class="hljs-number">20</span> <span class="hljs-number">15</span>:<span class="hljs-number">44</span>:<span class="hljs-number">37</span> WARN Utils: Your hostname, trusty resolves <span class="hljs-keyword">to</span> a loopback address: <span class="hljs-number">127.0</span>.<span class="hljs-number">1.1</span>; <span class="hljs-keyword">using</span> <span class="hljs-number">192.168</span>.<span class="hljs-number">1.254</span> instead (<span class="hljs-keyword">on</span> <span class="hljs-keyword">interface</span> eth0)
<span class="hljs-number">16</span>/<span class="hljs-number">09</span>/<span class="hljs-number">20</span> <span class="hljs-number">15</span>:<span class="hljs-number">44</span>:<span class="hljs-number">37</span> WARN Utils: <span class="hljs-keyword">Set</span> SPARK_LOCAL_IP <span class="hljs-keyword">if</span> you need <span class="hljs-keyword">to</span> bind <span class="hljs-keyword">to</span> another address
<span class="hljs-number">16</span>/<span class="hljs-number">09</span>/<span class="hljs-number">20</span> <span class="hljs-number">15</span>:<span class="hljs-number">45</span>:<span class="hljs-number">01</span> WARN BLAS: Failed <span class="hljs-keyword">to</span> load <span class="hljs-keyword">implementation</span> <span class="hljs-keyword">from</span>: com.github.fommil.netlib.NativeSystemBLAS
<span class="hljs-number">16</span>/<span class="hljs-number">09</span>/<span class="hljs-number">20</span> <span class="hljs-number">15</span>:<span class="hljs-number">45</span>:<span class="hljs-number">01</span> WARN BLAS: Failed <span class="hljs-keyword">to</span> load <span class="hljs-keyword">implementation</span> <span class="hljs-keyword">from</span>: com.github.fommil.netlib.NativeRefBLAS
<span class="hljs-number">16</span>/<span class="hljs-number">09</span>/<span class="hljs-number">20</span> <span class="hljs-number">15</span>:<span class="hljs-number">45</span>:<span class="hljs-number">02</span> WARN LAPACK: Failed <span class="hljs-keyword">to</span> load <span class="hljs-keyword">implementation</span> <span class="hljs-keyword">from</span>: com.github.fommil.netlib.NativeSystemLAPACK
<span class="hljs-number">16</span>/<span class="hljs-number">09</span>/<span class="hljs-number">20</span> <span class="hljs-number">15</span>:<span class="hljs-number">45</span>:<span class="hljs-number">02</span> WARN LAPACK: Failed <span class="hljs-keyword">to</span> load <span class="hljs-keyword">implementation</span> <span class="hljs-keyword">from</span>: com.github.fommil.netlib.NativeRefLAPACK
Goya <span class="hljs-keyword">in</span> Bordeaux (Goya en Bodeos) (<span class="hljs-number">1999</span>)
Slums <span class="hljs-keyword">of</span> Beverly Hills, The (<span class="hljs-number">1998</span>)
<span class="hljs-keyword">New</span> Jersey Drive (<span class="hljs-number">1995</span>)
Bottle Rocket (<span class="hljs-number">1996</span>)
I<span class="hljs-string">'ll Be Home For Christmas (1998)
Big Daddy (1999)
Kurt &amp; Courtney (1998)
Kika (1993)
Forrest Gump (1994)
Reality Bites (1994)
Omega Man, The (1971)
Boogie Nights (1997)
Boiler Room (2000)
Miami Rhapsody (1995)
Bob Roberts (1992)</span></code></pre>



<h3 id="recommend-algorithms">Recommend Algorithms</h3>

<p>推荐算法大致可分为如下几类， <br>
+ 基于人口统计学的推荐(Demographic-Based Recommendation)：该方法所基于的基本假设是“一个用户有可能会喜欢与其相似的用户所喜欢的物品”。当我们需要对一个User进行个性化推荐时，利用User Profile计算其它用户与其之间的相似度，然后挑选出与其最相似的前K个用户，之后利用这些用户的购买和打分信息进行推荐。 <br>
+ 基于内容的推荐(Content-Based Recommendation)：Content-Based方法所基于的基本假设是“一个用户可能会喜欢和他曾经喜欢过的物品相似的物品”。 <br>
+ 基于协同过滤的推荐(Collaborative Filtering-Based Recommendation)：是指收集用户过去的行为以获得其对产品的显式或隐式信息，即根据用户对物品或者信息的偏好，发现物品或者内容本身的相关性、或用户的相关性，然后再基于这些关联性进行推荐。基于协同过滤的推荐可以分基于用户的推荐(User-based Recommendation)，基于物品的推荐(Item-based Recommendation)，基于模型的推荐(Model-based Recommendation)等子类。</p>



<h3 id="matrix-factorization">Matrix Factorization</h3>

<p>矩阵分解(decomposition, factorization)是将矩阵拆解为数个三角形矩阵(triangular matrix)的一种操作。</p>



<h4 id="explicit-matrix-factorization">Explicit Matrix Factorization</h4>

<p>User Ratings数据： <br>
Tom, Star Wars, 5Jane, Titanic, 4Bill, Batman, 3Jane, Star Wars, 2Bill, Titanic, 3</p>

<p>以User为行，Movie为列构造对应Rating Matrix， <br>
<img src="https://img-blog.csdn.net/20161122075006732" alt="MF Matrix" title=""> <br>
MF就是一种直接建模user-item矩阵的方法，利用两个低维度的小矩阵的乘积来表示，属于一种降维的技术。</p>

<p>如果我们有U个Users，I个Items，若不经过MF处理，它看来会使这样的： <br>
<img src="https://img-blog.csdn.net/20161122075054230" alt="Matrix" title=""></p>

<p>是一个极其稀疏的矩阵，经过MF处理后，表示为两个维度较小的矩阵相乘： <br>
<img src="https://img-blog.csdn.net/20161122075238860" alt="Matrix_2" title=""></p>

<p>这类模型被称为Latent Feature Models，旨在寻找那些潜在的特征，来间接表示User-Item Rating的矩阵。这类潜在的Features并不直接建模User对Item的Rating关系，而是通过Latent Features更趋近于建模用户对某类Items的偏好，例如某类影片、风格等等，而这些事通过MF寻找其内在的信息，无需Items的详细描述。</p>

<p>MF模型如何计算一个User对某个Item的偏好，对应向量相乘即可： <br>
<img src="https://img-blog.csdn.net/20161122075309595" alt="Matrix_3" title=""></p>

<p>MF模型的好处是一旦模型创建好后，Predict变得十分容易，并且性能也很好.但是在海量的用户和Item Set时，存储和生产MF中的如上图的这两个矩阵会变得具有挑战性。</p>



<h4 id="implicit-matrix-factorization">Implicit Matrix Factorization</h4>

<p>前面我们都在讨论显式的一些偏好信息，比如Rating。但是在大部分应用中，拿不到这类信息，我们更多是搜集一些隐性的反馈信息，这类反馈信息没有明确地告诉某个用户对某个Item的偏好信息，但是却可以从用户对某个Item的交互信息中建模出来，例如一些二值特征，包括是否浏览过、是否购买过产品以及多少次看过某部电影等等。</p>

<p>MLlib中提供了一种处理这类隐性特征的方法，将前面的输入Ratings矩阵其实可以看做是两个矩阵：二值偏好矩阵P和信心权重矩阵C；</p>

<p>举个例子：假定我们的网站上面没有设计对Movie的Rating部分，只能通过log查看到用户是否观看过影片。然后通过后期处理，可以看出他观看到过多少次某部影片，这里P来表示影片是否被某用户看过，C来描述这里的Confidence Weighting也就是观看的次数： <br>
<img src="https://img-blog.csdn.net/20161122075423971" alt="Matrix_5" title=""></p>

<p>这里我们把P和C的点积来替代前面的rating矩阵，那么我们最终建模来预估某用户对Item的偏好。</p>



<h3 id="als-algorithm">ALS Algorithm</h3>

<p>ALS是Alternating Least Squares的缩写，意为交替最小二乘法，该方法常用于基于矩阵分解的推荐系统中。</p>

<p>对于一个Users-Products-Rating的评分数据集，ALS会建立一个User和Product的<span class="MathJax_Preview"></span><span class="MathJax" id="MathJax-Element-1-Frame" role="textbox" aria-readonly="true"><nobr><span class="math" id="MathJax-Span-1" style="width: 3.309em; display: inline-block;"><span style="display: inline-block; position: relative; width: 2.659em; height: 0px; font-size: 123%;"><span style="position: absolute; clip: rect(1.629em 1000em 2.442em -0.485em); top: -2.274em; left: 0.003em;"><span class="mrow" id="MathJax-Span-2"><span class="mi" id="MathJax-Span-3" style="font-family: MathJax_Math-italic;">m</span><span class="mo" id="MathJax-Span-4" style="font-family: MathJax_Main; padding-left: 0.22em;">×</span><span class="mi" id="MathJax-Span-5" style="font-family: MathJax_Math-italic; padding-left: 0.22em;">n</span></span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span><span style="border-left-width: 0.003em; border-left-style: solid; display: inline-block; overflow: hidden; width: 0px; height: 0.737em; vertical-align: -0.063em;"></span></span></nobr></span><script type="math/tex" id="MathJax-Element-1">m \times n</script>的矩阵。其中，m为Users的数量，n为Products的数量。但是在这个数据集中，并不是每个用户都对每个产品进行过评分，所以这个矩阵往往是稀疏的，用户<span class="MathJax_Preview"></span><span class="MathJax" id="MathJax-Element-2-Frame" role="textbox" aria-readonly="true"><nobr><span class="math" id="MathJax-Span-6" style="width: 1.358em; display: inline-block;"><span style="display: inline-block; position: relative; width: 1.087em; height: 0px; font-size: 123%;"><span style="position: absolute; clip: rect(1.412em 1000em 2.604em -0.431em); top: -2.274em; left: 0.003em;"><span class="mrow" id="MathJax-Span-7"><span class="msubsup" id="MathJax-Span-8"><span style="display: inline-block; position: relative; width: 1.033em; height: 0px;"><span style="position: absolute; clip: rect(1.412em 1000em 2.442em -0.431em); top: -2.274em; left: 0.003em;"><span class="mi" id="MathJax-Span-9" style="font-family: MathJax_Math-italic;">U<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.111em;"></span></span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span><span style="position: absolute; top: -2.111em; left: 0.707em;"><span class="mi" id="MathJax-Span-10" style="font-size: 70.7%; font-family: MathJax_Math-italic;">i</span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span></span></span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span><span style="border-left-width: 0.003em; border-left-style: solid; display: inline-block; overflow: hidden; width: 0px; height: 1.203em; vertical-align: -0.263em;"></span></span></nobr></span><script type="math/tex" id="MathJax-Element-2">U_i</script>对产品<span class="MathJax_Preview"></span><span class="MathJax" id="MathJax-Element-3-Frame" role="textbox" aria-readonly="true"><nobr><span class="math" id="MathJax-Span-11" style="width: 1.358em; display: inline-block;"><span style="display: inline-block; position: relative; width: 1.087em; height: 0px; font-size: 123%;"><span style="position: absolute; clip: rect(1.412em 1000em 2.713em -0.431em); top: -2.274em; left: 0.003em;"><span class="mrow" id="MathJax-Span-12"><span class="msubsup" id="MathJax-Span-13"><span style="display: inline-block; position: relative; width: 1.033em; height: 0px;"><span style="position: absolute; clip: rect(1.412em 1000em 2.442em -0.431em); top: -2.274em; left: 0.003em;"><span class="mi" id="MathJax-Span-14" style="font-family: MathJax_Math-italic;">P<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.111em;"></span></span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span><span style="position: absolute; top: -2.111em; left: 0.707em;"><span class="mi" id="MathJax-Span-15" style="font-size: 70.7%; font-family: MathJax_Math-italic;">j</span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span></span></span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span><span style="border-left-width: 0.003em; border-left-style: solid; display: inline-block; overflow: hidden; width: 0px; height: 1.337em; vertical-align: -0.397em;"></span></span></nobr></span><script type="math/tex" id="MathJax-Element-3">P_j</script>的评分往往是空的。ALS要解决的问题就是将这个稀疏矩阵通过一定的规律填满，这样就可以从矩阵中得到任意一个User对任意一个Product的评分，ALS填充的评分项也称为用户<span class="MathJax_Preview"></span><span class="MathJax" id="MathJax-Element-4-Frame" role="textbox" aria-readonly="true"><nobr><span class="math" id="MathJax-Span-16" style="width: 1.358em; display: inline-block;"><span style="display: inline-block; position: relative; width: 1.087em; height: 0px; font-size: 123%;"><span style="position: absolute; clip: rect(1.412em 1000em 2.604em -0.431em); top: -2.274em; left: 0.003em;"><span class="mrow" id="MathJax-Span-17"><span class="msubsup" id="MathJax-Span-18"><span style="display: inline-block; position: relative; width: 1.033em; height: 0px;"><span style="position: absolute; clip: rect(1.412em 1000em 2.442em -0.431em); top: -2.274em; left: 0.003em;"><span class="mi" id="MathJax-Span-19" style="font-family: MathJax_Math-italic;">U<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.111em;"></span></span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span><span style="position: absolute; top: -2.111em; left: 0.707em;"><span class="mi" id="MathJax-Span-20" style="font-size: 70.7%; font-family: MathJax_Math-italic;">i</span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span></span></span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span><span style="border-left-width: 0.003em; border-left-style: solid; display: inline-block; overflow: hidden; width: 0px; height: 1.203em; vertical-align: -0.263em;"></span></span></nobr></span><script type="math/tex" id="MathJax-Element-4">U_i</script>对产品<span class="MathJax_Preview"></span><span class="MathJax" id="MathJax-Element-5-Frame" role="textbox" aria-readonly="true"><nobr><span class="math" id="MathJax-Span-21" style="width: 1.358em; display: inline-block;"><span style="display: inline-block; position: relative; width: 1.087em; height: 0px; font-size: 123%;"><span style="position: absolute; clip: rect(1.412em 1000em 2.713em -0.431em); top: -2.274em; left: 0.003em;"><span class="mrow" id="MathJax-Span-22"><span class="msubsup" id="MathJax-Span-23"><span style="display: inline-block; position: relative; width: 1.033em; height: 0px;"><span style="position: absolute; clip: rect(1.412em 1000em 2.442em -0.431em); top: -2.274em; left: 0.003em;"><span class="mi" id="MathJax-Span-24" style="font-family: MathJax_Math-italic;">P<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.111em;"></span></span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span><span style="position: absolute; top: -2.111em; left: 0.707em;"><span class="mi" id="MathJax-Span-25" style="font-size: 70.7%; font-family: MathJax_Math-italic;">j</span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span></span></span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span><span style="border-left-width: 0.003em; border-left-style: solid; display: inline-block; overflow: hidden; width: 0px; height: 1.337em; vertical-align: -0.397em;"></span></span></nobr></span><script type="math/tex" id="MathJax-Element-5">P_j</script>的预测得分。所以说，ALS算法的核心就是通过什么样子的规律来填满（预测）这个稀疏矩阵。</p>

<p>例如：将用户(user)对商品(item)的评分矩阵分解为两个矩阵： <br>
- 一个是用户对商品隐含特征的偏好矩阵； <br>
- 另一个是商品所包含的隐含特征的矩阵； <br>
在这个矩阵分解的过程中，评分缺失项得到了填充，也就是说我们可以基于这个填充的评分来给用户最商品推荐了。</p>

<p>由于评分数据中有大量的缺失项，传统的矩阵分解SVD（奇异值分解）不方便处理这个问题，而ALS能够很好的解决这个问题。对于<span class="MathJax_Preview"></span><span class="MathJax" id="MathJax-Element-6-Frame" role="textbox" aria-readonly="true"><nobr><span class="math" id="MathJax-Span-26" style="width: 5.152em; display: inline-block;"><span style="display: inline-block; position: relative; width: 4.176em; height: 0px; font-size: 123%;"><span style="position: absolute; clip: rect(1.358em 1000em 2.713em -0.431em); top: -2.274em; left: 0.003em;"><span class="mrow" id="MathJax-Span-27"><span class="mi" id="MathJax-Span-28" style="font-family: MathJax_Math-italic;">R</span><span class="mo" id="MathJax-Span-29" style="font-family: MathJax_Main;">(</span><span class="mi" id="MathJax-Span-30" style="font-family: MathJax_Math-italic;">m</span><span class="mo" id="MathJax-Span-31" style="font-family: MathJax_Main; padding-left: 0.22em;">×</span><span class="mi" id="MathJax-Span-32" style="font-family: MathJax_Math-italic; padding-left: 0.22em;">n</span><span class="mo" id="MathJax-Span-33" style="font-family: MathJax_Main;">)</span></span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span><span style="border-left-width: 0.003em; border-left-style: solid; display: inline-block; overflow: hidden; width: 0px; height: 1.337em; vertical-align: -0.397em;"></span></span></nobr></span><script type="math/tex" id="MathJax-Element-6">R(m×n)</script>的矩阵，ALS旨在找到两个低维矩阵<span class="MathJax_Preview"></span><span class="MathJax" id="MathJax-Element-7-Frame" role="textbox" aria-readonly="true"><nobr><span class="math" id="MathJax-Span-34" style="width: 5.152em; display: inline-block;"><span style="display: inline-block; position: relative; width: 4.176em; height: 0px; font-size: 123%;"><span style="position: absolute; clip: rect(1.358em 1000em 2.713em -0.485em); top: -2.274em; left: 0.003em;"><span class="mrow" id="MathJax-Span-35"><span class="mi" id="MathJax-Span-36" style="font-family: MathJax_Math-italic;">X<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.003em;"></span></span><span class="mo" id="MathJax-Span-37" style="font-family: MathJax_Main;">(</span><span class="mi" id="MathJax-Span-38" style="font-family: MathJax_Math-italic;">m</span><span class="mo" id="MathJax-Span-39" style="font-family: MathJax_Main; padding-left: 0.22em;">×</span><span class="mi" id="MathJax-Span-40" style="font-family: MathJax_Math-italic; padding-left: 0.22em;">k</span><span class="mo" id="MathJax-Span-41" style="font-family: MathJax_Main;">)</span></span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span><span style="border-left-width: 0.003em; border-left-style: solid; display: inline-block; overflow: hidden; width: 0px; height: 1.337em; vertical-align: -0.397em;"></span></span></nobr></span><script type="math/tex" id="MathJax-Element-7">X(m \times k)</script>和矩阵<span class="MathJax_Preview"></span><span class="MathJax" id="MathJax-Element-8-Frame" role="textbox" aria-readonly="true"><nobr><span class="math" id="MathJax-Span-42" style="width: 4.664em; display: inline-block;"><span style="display: inline-block; position: relative; width: 3.797em; height: 0px; font-size: 123%;"><span style="position: absolute; clip: rect(1.358em 1000em 2.713em -0.431em); top: -2.274em; left: 0.003em;"><span class="mrow" id="MathJax-Span-43"><span class="mi" id="MathJax-Span-44" style="font-family: MathJax_Math-italic;">Y<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.165em;"></span></span><span class="mo" id="MathJax-Span-45" style="font-family: MathJax_Main;">(</span><span class="mi" id="MathJax-Span-46" style="font-family: MathJax_Math-italic;">n</span><span class="mo" id="MathJax-Span-47" style="font-family: MathJax_Main; padding-left: 0.22em;">×</span><span class="mi" id="MathJax-Span-48" style="font-family: MathJax_Math-italic; padding-left: 0.22em;">k</span><span class="mo" id="MathJax-Span-49" style="font-family: MathJax_Main;">)</span></span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span><span style="border-left-width: 0.003em; border-left-style: solid; display: inline-block; overflow: hidden; width: 0px; height: 1.337em; vertical-align: -0.397em;"></span></span></nobr></span><script type="math/tex" id="MathJax-Element-8">Y(n \times k)</script>，来近似逼近<span class="MathJax_Preview"></span><span class="MathJax" id="MathJax-Element-9-Frame" role="textbox" aria-readonly="true"><nobr><span class="math" id="MathJax-Span-50" style="width: 5.152em; display: inline-block;"><span style="display: inline-block; position: relative; width: 4.176em; height: 0px; font-size: 123%;"><span style="position: absolute; clip: rect(1.358em 1000em 2.713em -0.431em); top: -2.274em; left: 0.003em;"><span class="mrow" id="MathJax-Span-51"><span class="mi" id="MathJax-Span-52" style="font-family: MathJax_Math-italic;">R</span><span class="mo" id="MathJax-Span-53" style="font-family: MathJax_Main;">(</span><span class="mi" id="MathJax-Span-54" style="font-family: MathJax_Math-italic;">m</span><span class="mo" id="MathJax-Span-55" style="font-family: MathJax_Main; padding-left: 0.22em;">×</span><span class="mi" id="MathJax-Span-56" style="font-family: MathJax_Math-italic; padding-left: 0.22em;">n</span><span class="mo" id="MathJax-Span-57" style="font-family: MathJax_Main;">)</span></span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span><span style="border-left-width: 0.003em; border-left-style: solid; display: inline-block; overflow: hidden; width: 0px; height: 1.337em; vertical-align: -0.397em;"></span></span></nobr></span><script type="math/tex" id="MathJax-Element-9">R(m \times n)</script>，即：<span class="MathJax_Preview"></span><span class="MathJax" id="MathJax-Element-10-Frame" role="textbox" aria-readonly="true"><nobr><span class="math" id="MathJax-Span-58" style="width: 5.423em; display: inline-block;"><span style="display: inline-block; position: relative; width: 4.393em; height: 0px; font-size: 123%;"><span style="position: absolute; clip: rect(1.249em 1000em 2.442em -0.431em); top: -2.274em; left: 0.003em;"><span class="mrow" id="MathJax-Span-59"><span class="mi" id="MathJax-Span-60" style="font-family: MathJax_Math-italic;">R</span><span class="mo" id="MathJax-Span-61" style="font-family: MathJax_Main; padding-left: 0.274em;">≈</span><span class="mi" id="MathJax-Span-62" style="font-family: MathJax_Math-italic; padding-left: 0.274em;">X<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.003em;"></span></span><span class="msubsup" id="MathJax-Span-63"><span style="display: inline-block; position: relative; width: 1.466em; height: 0px;"><span style="position: absolute; clip: rect(1.412em 1000em 2.442em -0.431em); top: -2.274em; left: 0.003em;"><span class="mi" id="MathJax-Span-64" style="font-family: MathJax_Math-italic;">Y<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.165em;"></span></span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span><span style="position: absolute; top: -2.707em; left: 0.87em;"><span class="mi" id="MathJax-Span-65" style="font-size: 70.7%; font-family: MathJax_Math-italic;">T<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.111em;"></span></span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span></span></span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span><span style="border-left-width: 0.003em; border-left-style: solid; display: inline-block; overflow: hidden; width: 0px; height: 1.27em; vertical-align: -0.063em;"></span></span></nobr></span><script type="math/tex" id="MathJax-Element-10"> R \approx XY^T</script>，其中，<span class="MathJax_Preview"></span><span class="MathJax" id="MathJax-Element-11-Frame" role="textbox" aria-readonly="true"><nobr><span class="math" id="MathJax-Span-66" style="width: 5.152em; display: inline-block;"><span style="display: inline-block; position: relative; width: 4.176em; height: 0px; font-size: 123%;"><span style="position: absolute; clip: rect(1.358em 1000em 2.713em -0.431em); top: -2.274em; left: 0.003em;"><span class="mrow" id="MathJax-Span-67"><span class="mi" id="MathJax-Span-68" style="font-family: MathJax_Math-italic;">R</span><span class="mo" id="MathJax-Span-69" style="font-family: MathJax_Main;">(</span><span class="mi" id="MathJax-Span-70" style="font-family: MathJax_Math-italic;">m</span><span class="mo" id="MathJax-Span-71" style="font-family: MathJax_Main; padding-left: 0.22em;">×</span><span class="mi" id="MathJax-Span-72" style="font-family: MathJax_Math-italic; padding-left: 0.22em;">n</span><span class="mo" id="MathJax-Span-73" style="font-family: MathJax_Main;">)</span></span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span><span style="border-left-width: 0.003em; border-left-style: solid; display: inline-block; overflow: hidden; width: 0px; height: 1.337em; vertical-align: -0.397em;"></span></span></nobr></span><script type="math/tex" id="MathJax-Element-11"> R(m \times n) </script>代表用户对商品的评分矩阵，<span class="MathJax_Preview"></span><span class="MathJax" id="MathJax-Element-12-Frame" role="textbox" aria-readonly="true"><nobr><span class="math" id="MathJax-Span-74" style="width: 5.152em; display: inline-block;"><span style="display: inline-block; position: relative; width: 4.176em; height: 0px; font-size: 123%;"><span style="position: absolute; clip: rect(1.358em 1000em 2.713em -0.485em); top: -2.274em; left: 0.003em;"><span class="mrow" id="MathJax-Span-75"><span class="mi" id="MathJax-Span-76" style="font-family: MathJax_Math-italic;">X<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.003em;"></span></span><span class="mo" id="MathJax-Span-77" style="font-family: MathJax_Main;">(</span><span class="mi" id="MathJax-Span-78" style="font-family: MathJax_Math-italic;">m</span><span class="mo" id="MathJax-Span-79" style="font-family: MathJax_Main; padding-left: 0.22em;">×</span><span class="mi" id="MathJax-Span-80" style="font-family: MathJax_Math-italic; padding-left: 0.22em;">k</span><span class="mo" id="MathJax-Span-81" style="font-family: MathJax_Main;">)</span></span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span><span style="border-left-width: 0.003em; border-left-style: solid; display: inline-block; overflow: hidden; width: 0px; height: 1.337em; vertical-align: -0.397em;"></span></span></nobr></span><script type="math/tex" id="MathJax-Element-12"> X(m \times k) </script>代表用户对隐含特征的偏好矩阵，<span class="MathJax_Preview"></span><span class="MathJax" id="MathJax-Element-13-Frame" role="textbox" aria-readonly="true"><nobr><span class="math" id="MathJax-Span-82" style="width: 4.664em; display: inline-block;"><span style="display: inline-block; position: relative; width: 3.797em; height: 0px; font-size: 123%;"><span style="position: absolute; clip: rect(1.358em 1000em 2.713em -0.431em); top: -2.274em; left: 0.003em;"><span class="mrow" id="MathJax-Span-83"><span class="mi" id="MathJax-Span-84" style="font-family: MathJax_Math-italic;">Y<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.165em;"></span></span><span class="mo" id="MathJax-Span-85" style="font-family: MathJax_Main;">(</span><span class="mi" id="MathJax-Span-86" style="font-family: MathJax_Math-italic;">n</span><span class="mo" id="MathJax-Span-87" style="font-family: MathJax_Main; padding-left: 0.22em;">×</span><span class="mi" id="MathJax-Span-88" style="font-family: MathJax_Math-italic; padding-left: 0.22em;">k</span><span class="mo" id="MathJax-Span-89" style="font-family: MathJax_Main;">)</span></span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span><span style="border-left-width: 0.003em; border-left-style: solid; display: inline-block; overflow: hidden; width: 0px; height: 1.337em; vertical-align: -0.397em;"></span></span></nobr></span><script type="math/tex" id="MathJax-Element-13"> Y(n \times k)</script>表示商品所包含隐含特征的矩阵，T表示矩阵Y的转置。实际中，一般取<span class="MathJax_Preview"></span><span class="MathJax" id="MathJax-Element-14-Frame" role="textbox" aria-readonly="true"><nobr><span class="math" id="MathJax-Span-90" style="width: 8.621em; display: inline-block;"><span style="display: inline-block; position: relative; width: 6.995em; height: 0px; font-size: 123%;"><span style="position: absolute; clip: rect(1.358em 1000em 2.713em -0.431em); top: -2.274em; left: 0.003em;"><span class="mrow" id="MathJax-Span-91"><span class="mi" id="MathJax-Span-92" style="font-family: MathJax_Math-italic;">k</span><span class="mo" id="MathJax-Span-93" style="font-family: MathJax_Main; padding-left: 0.274em;">&lt;<span style="font-family: MathJax_Main;">&lt;</span></span><span class="mi" id="MathJax-Span-94" style="font-family: MathJax_Math-italic; padding-left: 0.274em;">m</span><span class="mi" id="MathJax-Span-95" style="font-family: MathJax_Math-italic;">i</span><span class="mi" id="MathJax-Span-96" style="font-family: MathJax_Math-italic;">n</span><span class="mo" id="MathJax-Span-97" style="font-family: MathJax_Main;">(</span><span class="mi" id="MathJax-Span-98" style="font-family: MathJax_Math-italic;">m</span><span class="mo" id="MathJax-Span-99" style="font-family: MathJax_Main;">,</span><span class="mi" id="MathJax-Span-100" style="font-family: MathJax_Math-italic; padding-left: 0.165em;">n</span><span class="mo" id="MathJax-Span-101" style="font-family: MathJax_Main;">)</span></span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span><span style="border-left-width: 0.003em; border-left-style: solid; display: inline-block; overflow: hidden; width: 0px; height: 1.337em; vertical-align: -0.397em;"></span></span></nobr></span><script type="math/tex" id="MathJax-Element-14"> k << min(m, n) </script>，也就是相当于降维了。这里的低维矩阵，有的地方也叫低秩矩阵。</p>

<p>为了找到低维矩阵X，Y最大程度地逼近矩分矩阵R，最小化下面的平方误差损失函数, <br>
<span class="MathJax_Preview"></span></p><div class="MathJax_Display" role="textbox" aria-readonly="true" style="text-align: center;"><span class="MathJax" id="MathJax-Element-15-Frame"><nobr><span class="math" id="MathJax-Span-102" style="width: 16.154em; display: inline-block;"><span style="display: inline-block; position: relative; width: 13.119em; height: 0px; font-size: 123%;"><span style="position: absolute; clip: rect(1.141em 1000em 3.743em -0.431em); top: -2.274em; left: 0.003em;"><span class="mrow" id="MathJax-Span-103"><span class="mi" id="MathJax-Span-104" style="font-family: MathJax_Math-italic;">L</span><span class="mo" id="MathJax-Span-105" style="font-family: MathJax_Main;">(</span><span class="mi" id="MathJax-Span-106" style="font-family: MathJax_Math-italic;">X<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.003em;"></span></span><span class="mo" id="MathJax-Span-107" style="font-family: MathJax_Main;">,</span><span class="mi" id="MathJax-Span-108" style="font-family: MathJax_Math-italic; padding-left: 0.165em;">Y<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.165em;"></span></span><span class="mo" id="MathJax-Span-109" style="font-family: MathJax_Main;">)</span><span class="mo" id="MathJax-Span-110" style="font-family: MathJax_Main; padding-left: 0.274em;">=</span><span class="munderover" id="MathJax-Span-111" style="padding-left: 0.274em;"><span style="display: inline-block; position: relative; width: 1.412em; height: 0px;"><span style="position: absolute; clip: rect(2.008em 1000em 3.743em -0.431em); top: -3.141em; left: 0.003em;"><span class="mo" id="MathJax-Span-112" style="font-family: MathJax_Size2; vertical-align: 0.003em;">∑</span><span style="display: inline-block; width: 0px; height: 3.146em;"></span></span><span style="position: absolute; clip: rect(1.629em 1000em 2.659em -0.485em); top: -1.19em; left: 0.328em;"><span class="texatom" id="MathJax-Span-113"><span class="mrow" id="MathJax-Span-114"><span class="mi" id="MathJax-Span-115" style="font-size: 70.7%; font-family: MathJax_Math-italic;">u</span><span class="mo" id="MathJax-Span-116" style="font-size: 70.7%; font-family: MathJax_Main;">,</span><span class="mi" id="MathJax-Span-117" style="font-size: 70.7%; font-family: MathJax_Math-italic;">i</span></span></span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span></span><span class="texatom" id="MathJax-Span-118" style="padding-left: 0.165em;"><span class="mrow" id="MathJax-Span-119"><span class="mo" id="MathJax-Span-120" style="font-family: MathJax_Main;">(</span><span class="msubsup" id="MathJax-Span-121"><span style="display: inline-block; position: relative; width: 1.087em; height: 0px;"><span style="position: absolute; clip: rect(1.683em 1000em 2.442em -0.485em); top: -2.274em; left: 0.003em;"><span class="mi" id="MathJax-Span-122" style="font-family: MathJax_Math-italic;">r</span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span><span style="position: absolute; top: -2.111em; left: 0.436em;"><span class="texatom" id="MathJax-Span-123"><span class="mrow" id="MathJax-Span-124"><span class="mi" id="MathJax-Span-125" style="font-size: 70.7%; font-family: MathJax_Math-italic;">u</span><span class="mi" id="MathJax-Span-126" style="font-size: 70.7%; font-family: MathJax_Math-italic;">i</span></span></span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span></span><span class="mo" id="MathJax-Span-127" style="font-family: MathJax_Main; padding-left: 0.22em;">−</span><span class="msubsup" id="MathJax-Span-128" style="padding-left: 0.22em;"><span style="display: inline-block; position: relative; width: 0.978em; height: 0px;"><span style="position: absolute; clip: rect(1.683em 1000em 2.442em -0.431em); top: -2.274em; left: 0.003em;"><span class="mi" id="MathJax-Span-129" style="font-family: MathJax_Math-italic;">x</span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span><span style="position: absolute; top: -2.111em; left: 0.545em;"><span class="mi" id="MathJax-Span-130" style="font-size: 70.7%; font-family: MathJax_Math-italic;">u</span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span></span><span class="mo" id="MathJax-Span-131" style="font-family: MathJax_Main; padding-left: 0.22em;">×</span><span class="msubsup" id="MathJax-Span-132" style="padding-left: 0.22em;"><span style="display: inline-block; position: relative; width: 1.195em; height: 0px;"><span style="position: absolute; clip: rect(1.683em 1000em 2.659em -0.485em); top: -2.274em; left: 0.003em;"><span class="mi" id="MathJax-Span-133" style="font-family: MathJax_Math-italic;">y<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.003em;"></span></span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span><span style="position: absolute; clip: rect(1.629em 1000em 2.442em -0.485em); top: -2.599em; left: 0.599em;"><span class="mi" id="MathJax-Span-134" style="font-size: 70.7%; font-family: MathJax_Math-italic;">T<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.111em;"></span></span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span><span style="position: absolute; clip: rect(1.629em 1000em 2.442em -0.485em); top: -1.949em; left: 0.545em;"><span class="mi" id="MathJax-Span-135" style="font-size: 70.7%; font-family: MathJax_Math-italic;">i</span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span></span><span class="msubsup" id="MathJax-Span-136"><span style="display: inline-block; position: relative; width: 0.816em; height: 0px;"><span style="position: absolute; clip: rect(1.358em 1000em 2.713em -0.431em); top: -2.274em; left: 0.003em;"><span class="mo" id="MathJax-Span-137" style="font-family: MathJax_Main;">)</span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span><span style="position: absolute; top: -2.762em; left: 0.382em;"><span class="mn" id="MathJax-Span-138" style="font-size: 70.7%; font-family: MathJax_Main;">2</span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span></span></span></span></span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span><span style="border-left-width: 0.003em; border-left-style: solid; display: inline-block; overflow: hidden; width: 0px; height: 2.937em; vertical-align: -1.663em;"></span></span></nobr></span></div><script type="math/tex; mode=display" id="MathJax-Element-15">
L(X, Y) = \sum_{u, i}{(r_{ui} - x_u \times y^T_i)^2}
</script> <br>
其中，<span class="MathJax_Preview"></span><span class="MathJax" id="MathJax-Element-16-Frame" role="textbox" aria-readonly="true"><nobr><span class="math" id="MathJax-Span-139" style="width: 4.881em; display: inline-block;"><span style="display: inline-block; position: relative; width: 3.959em; height: 0px; font-size: 123%;"><span style="position: absolute; clip: rect(1.358em 1000em 2.713em -0.431em); top: -2.274em; left: 0.003em;"><span class="mrow" id="MathJax-Span-140"><span class="msubsup" id="MathJax-Span-141"><span style="display: inline-block; position: relative; width: 0.978em; height: 0px;"><span style="position: absolute; clip: rect(1.683em 1000em 2.442em -0.431em); top: -2.274em; left: 0.003em;"><span class="mi" id="MathJax-Span-142" style="font-family: MathJax_Math-italic;">x</span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span><span style="position: absolute; top: -2.111em; left: 0.545em;"><span class="mi" id="MathJax-Span-143" style="font-size: 70.7%; font-family: MathJax_Math-italic;">u</span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span></span><span class="mo" id="MathJax-Span-144" style="font-family: MathJax_Main;">(</span><span class="mn" id="MathJax-Span-145" style="font-family: MathJax_Main;">1</span><span class="mo" id="MathJax-Span-146" style="font-family: MathJax_Main; padding-left: 0.22em;">×</span><span class="mi" id="MathJax-Span-147" style="font-family: MathJax_Math-italic; padding-left: 0.22em;">k</span><span class="mo" id="MathJax-Span-148" style="font-family: MathJax_Main;">)</span></span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span><span style="border-left-width: 0.003em; border-left-style: solid; display: inline-block; overflow: hidden; width: 0px; height: 1.337em; vertical-align: -0.397em;"></span></span></nobr></span><script type="math/tex" id="MathJax-Element-16">x_u(1 \times k)</script>表示用户<span class="MathJax_Preview"></span><span class="MathJax" id="MathJax-Element-17-Frame" role="textbox" aria-readonly="true"><nobr><span class="math" id="MathJax-Span-149" style="width: 1.304em; display: inline-block;"><span style="display: inline-block; position: relative; width: 1.033em; height: 0px; font-size: 123%;"><span style="position: absolute; clip: rect(1.683em 1000em 2.604em -0.431em); top: -2.274em; left: 0.003em;"><span class="mrow" id="MathJax-Span-150"><span class="msubsup" id="MathJax-Span-151"><span style="display: inline-block; position: relative; width: 0.978em; height: 0px;"><span style="position: absolute; clip: rect(1.683em 1000em 2.442em -0.431em); top: -2.274em; left: 0.003em;"><span class="mi" id="MathJax-Span-152" style="font-family: MathJax_Math-italic;">x</span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span><span style="position: absolute; top: -2.111em; left: 0.545em;"><span class="mi" id="MathJax-Span-153" style="font-size: 70.7%; font-family: MathJax_Math-italic;">u</span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span></span></span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span><span style="border-left-width: 0.003em; border-left-style: solid; display: inline-block; overflow: hidden; width: 0px; height: 0.87em; vertical-align: -0.263em;"></span></span></nobr></span><script type="math/tex" id="MathJax-Element-17">x_u</script>的偏好的隐含特征向量，<span class="MathJax_Preview"></span><span class="MathJax" id="MathJax-Element-18-Frame" role="textbox" aria-readonly="true"><nobr><span class="math" id="MathJax-Span-154" style="width: 4.664em; display: inline-block;"><span style="display: inline-block; position: relative; width: 3.797em; height: 0px; font-size: 123%;"><span style="position: absolute; clip: rect(1.358em 1000em 2.713em -0.485em); top: -2.274em; left: 0.003em;"><span class="mrow" id="MathJax-Span-155"><span class="msubsup" id="MathJax-Span-156"><span style="display: inline-block; position: relative; width: 0.816em; height: 0px;"><span style="position: absolute; clip: rect(1.683em 1000em 2.659em -0.485em); top: -2.274em; left: 0.003em;"><span class="mi" id="MathJax-Span-157" style="font-family: MathJax_Math-italic;">y<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.003em;"></span></span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span><span style="position: absolute; top: -2.057em; left: 0.545em;"><span class="mi" id="MathJax-Span-158" style="font-size: 70.7%; font-family: MathJax_Math-italic;">i</span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span></span><span class="mo" id="MathJax-Span-159" style="font-family: MathJax_Main;">(</span><span class="mn" id="MathJax-Span-160" style="font-family: MathJax_Main;">1</span><span class="mo" id="MathJax-Span-161" style="font-family: MathJax_Main; padding-left: 0.22em;">×</span><span class="mi" id="MathJax-Span-162" style="font-family: MathJax_Math-italic; padding-left: 0.22em;">k</span><span class="mo" id="MathJax-Span-163" style="font-family: MathJax_Main;">)</span></span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span><span style="border-left-width: 0.003em; border-left-style: solid; display: inline-block; overflow: hidden; width: 0px; height: 1.337em; vertical-align: -0.397em;"></span></span></nobr></span><script type="math/tex" id="MathJax-Element-18">y_i(1 \times k)</script>表示商品<span class="MathJax_Preview"></span><span class="MathJax" id="MathJax-Element-19-Frame" role="textbox" aria-readonly="true"><nobr><span class="math" id="MathJax-Span-164" style="width: 1.087em; display: inline-block;"><span style="display: inline-block; position: relative; width: 0.87em; height: 0px; font-size: 123%;"><span style="position: absolute; clip: rect(1.683em 1000em 2.713em -0.485em); top: -2.274em; left: 0.003em;"><span class="mrow" id="MathJax-Span-165"><span class="msubsup" id="MathJax-Span-166"><span style="display: inline-block; position: relative; width: 0.816em; height: 0px;"><span style="position: absolute; clip: rect(1.683em 1000em 2.659em -0.485em); top: -2.274em; left: 0.003em;"><span class="mi" id="MathJax-Span-167" style="font-family: MathJax_Math-italic;">y<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.003em;"></span></span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span><span style="position: absolute; top: -2.057em; left: 0.545em;"><span class="mi" id="MathJax-Span-168" style="font-size: 70.7%; font-family: MathJax_Math-italic;">i</span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span></span></span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span><span style="border-left-width: 0.003em; border-left-style: solid; display: inline-block; overflow: hidden; width: 0px; height: 1.003em; vertical-align: -0.397em;"></span></span></nobr></span><script type="math/tex" id="MathJax-Element-19">y_i</script>包含的隐含特征向量，<span class="MathJax_Preview"></span><span class="MathJax" id="MathJax-Element-20-Frame" role="textbox" aria-readonly="true"><nobr><span class="math" id="MathJax-Span-169" style="width: 1.412em; display: inline-block;"><span style="display: inline-block; position: relative; width: 1.141em; height: 0px; font-size: 123%;"><span style="position: absolute; clip: rect(1.683em 1000em 2.604em -0.485em); top: -2.274em; left: 0.003em;"><span class="mrow" id="MathJax-Span-170"><span class="msubsup" id="MathJax-Span-171"><span style="display: inline-block; position: relative; width: 1.087em; height: 0px;"><span style="position: absolute; clip: rect(1.683em 1000em 2.442em -0.485em); top: -2.274em; left: 0.003em;"><span class="mi" id="MathJax-Span-172" style="font-family: MathJax_Math-italic;">r</span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span><span style="position: absolute; top: -2.111em; left: 0.436em;"><span class="texatom" id="MathJax-Span-173"><span class="mrow" id="MathJax-Span-174"><span class="mi" id="MathJax-Span-175" style="font-size: 70.7%; font-family: MathJax_Math-italic;">u</span><span class="mi" id="MathJax-Span-176" style="font-size: 70.7%; font-family: MathJax_Math-italic;">i</span></span></span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span></span></span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span><span style="border-left-width: 0.003em; border-left-style: solid; display: inline-block; overflow: hidden; width: 0px; height: 0.87em; vertical-align: -0.263em;"></span></span></nobr></span><script type="math/tex" id="MathJax-Element-20">r_{ui}</script>表示用户<span class="MathJax_Preview"></span><span class="MathJax" id="MathJax-Element-21-Frame" role="textbox" aria-readonly="true"><nobr><span class="math" id="MathJax-Span-177" style="width: 1.304em; display: inline-block;"><span style="display: inline-block; position: relative; width: 1.033em; height: 0px; font-size: 123%;"><span style="position: absolute; clip: rect(1.683em 1000em 2.604em -0.431em); top: -2.274em; left: 0.003em;"><span class="mrow" id="MathJax-Span-178"><span class="msubsup" id="MathJax-Span-179"><span style="display: inline-block; position: relative; width: 0.978em; height: 0px;"><span style="position: absolute; clip: rect(1.683em 1000em 2.442em -0.431em); top: -2.274em; left: 0.003em;"><span class="mi" id="MathJax-Span-180" style="font-family: MathJax_Math-italic;">x</span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span><span style="position: absolute; top: -2.111em; left: 0.545em;"><span class="mi" id="MathJax-Span-181" style="font-size: 70.7%; font-family: MathJax_Math-italic;">u</span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span></span></span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span><span style="border-left-width: 0.003em; border-left-style: solid; display: inline-block; overflow: hidden; width: 0px; height: 0.87em; vertical-align: -0.263em;"></span></span></nobr></span><script type="math/tex" id="MathJax-Element-21">x_u</script>对商品<span class="MathJax_Preview"></span><span class="MathJax" id="MathJax-Element-22-Frame" role="textbox" aria-readonly="true"><nobr><span class="math" id="MathJax-Span-182" style="width: 1.087em; display: inline-block;"><span style="display: inline-block; position: relative; width: 0.87em; height: 0px; font-size: 123%;"><span style="position: absolute; clip: rect(1.683em 1000em 2.713em -0.485em); top: -2.274em; left: 0.003em;"><span class="mrow" id="MathJax-Span-183"><span class="msubsup" id="MathJax-Span-184"><span style="display: inline-block; position: relative; width: 0.816em; height: 0px;"><span style="position: absolute; clip: rect(1.683em 1000em 2.659em -0.485em); top: -2.274em; left: 0.003em;"><span class="mi" id="MathJax-Span-185" style="font-family: MathJax_Math-italic;">y<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.003em;"></span></span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span><span style="position: absolute; top: -2.057em; left: 0.545em;"><span class="mi" id="MathJax-Span-186" style="font-size: 70.7%; font-family: MathJax_Math-italic;">i</span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span></span></span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span><span style="border-left-width: 0.003em; border-left-style: solid; display: inline-block; overflow: hidden; width: 0px; height: 1.003em; vertical-align: -0.397em;"></span></span></nobr></span><script type="math/tex" id="MathJax-Element-22">y_i</script>的评分，向量<span class="MathJax_Preview"></span><span class="MathJax" id="MathJax-Element-23-Frame" role="textbox" aria-readonly="true"><nobr><span class="math" id="MathJax-Span-187" style="width: 1.304em; display: inline-block;"><span style="display: inline-block; position: relative; width: 1.033em; height: 0px; font-size: 123%;"><span style="position: absolute; clip: rect(1.683em 1000em 2.604em -0.431em); top: -2.274em; left: 0.003em;"><span class="mrow" id="MathJax-Span-188"><span class="msubsup" id="MathJax-Span-189"><span style="display: inline-block; position: relative; width: 0.978em; height: 0px;"><span style="position: absolute; clip: rect(1.683em 1000em 2.442em -0.431em); top: -2.274em; left: 0.003em;"><span class="mi" id="MathJax-Span-190" style="font-family: MathJax_Math-italic;">x</span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span><span style="position: absolute; top: -2.111em; left: 0.545em;"><span class="mi" id="MathJax-Span-191" style="font-size: 70.7%; font-family: MathJax_Math-italic;">u</span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span></span></span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span><span style="border-left-width: 0.003em; border-left-style: solid; display: inline-block; overflow: hidden; width: 0px; height: 0.87em; vertical-align: -0.263em;"></span></span></nobr></span><script type="math/tex" id="MathJax-Element-23">x_u</script>和<span class="MathJax_Preview"></span><span class="MathJax" id="MathJax-Element-24-Frame" role="textbox" aria-readonly="true"><nobr><span class="math" id="MathJax-Span-192" style="width: 1.087em; display: inline-block;"><span style="display: inline-block; position: relative; width: 0.87em; height: 0px; font-size: 123%;"><span style="position: absolute; clip: rect(1.683em 1000em 2.713em -0.485em); top: -2.274em; left: 0.003em;"><span class="mrow" id="MathJax-Span-193"><span class="msubsup" id="MathJax-Span-194"><span style="display: inline-block; position: relative; width: 0.816em; height: 0px;"><span style="position: absolute; clip: rect(1.683em 1000em 2.659em -0.485em); top: -2.274em; left: 0.003em;"><span class="mi" id="MathJax-Span-195" style="font-family: MathJax_Math-italic;">y<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.003em;"></span></span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span><span style="position: absolute; top: -2.057em; left: 0.545em;"><span class="mi" id="MathJax-Span-196" style="font-size: 70.7%; font-family: MathJax_Math-italic;">i</span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span></span></span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span><span style="border-left-width: 0.003em; border-left-style: solid; display: inline-block; overflow: hidden; width: 0px; height: 1.003em; vertical-align: -0.397em;"></span></span></nobr></span><script type="math/tex" id="MathJax-Element-24">y_i</script>的内积<span class="MathJax_Preview"></span><span class="MathJax" id="MathJax-Element-25-Frame" role="textbox" aria-readonly="true"><nobr><span class="math" id="MathJax-Span-197" style="width: 4.23em; display: inline-block;"><span style="display: inline-block; position: relative; width: 3.417em; height: 0px; font-size: 123%;"><span style="position: absolute; clip: rect(1.304em 1000em 2.767em -0.431em); top: -2.274em; left: 0.003em;"><span class="mrow" id="MathJax-Span-198"><span class="msubsup" id="MathJax-Span-199"><span style="display: inline-block; position: relative; width: 0.978em; height: 0px;"><span style="position: absolute; clip: rect(1.683em 1000em 2.442em -0.431em); top: -2.274em; left: 0.003em;"><span class="mi" id="MathJax-Span-200" style="font-family: MathJax_Math-italic;">x</span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span><span style="position: absolute; top: -2.111em; left: 0.545em;"><span class="mi" id="MathJax-Span-201" style="font-size: 70.7%; font-family: MathJax_Math-italic;">u</span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span></span><span class="mo" id="MathJax-Span-202" style="font-family: MathJax_Main; padding-left: 0.22em;">×</span><span class="msubsup" id="MathJax-Span-203" style="padding-left: 0.22em;"><span style="display: inline-block; position: relative; width: 1.195em; height: 0px;"><span style="position: absolute; clip: rect(1.683em 1000em 2.659em -0.485em); top: -2.274em; left: 0.003em;"><span class="mi" id="MathJax-Span-204" style="font-family: MathJax_Math-italic;">y<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.003em;"></span></span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span><span style="position: absolute; clip: rect(1.629em 1000em 2.442em -0.485em); top: -2.599em; left: 0.599em;"><span class="mi" id="MathJax-Span-205" style="font-size: 70.7%; font-family: MathJax_Math-italic;">T<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.111em;"></span></span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span><span style="position: absolute; clip: rect(1.629em 1000em 2.442em -0.485em); top: -1.949em; left: 0.545em;"><span class="mi" id="MathJax-Span-206" style="font-size: 70.7%; font-family: MathJax_Math-italic;">i</span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span></span></span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span><span style="border-left-width: 0.003em; border-left-style: solid; display: inline-block; overflow: hidden; width: 0px; height: 1.537em; vertical-align: -0.463em;"></span></span></nobr></span><script type="math/tex" id="MathJax-Element-25">x_u \times y^T_i</script>是用户<span class="MathJax_Preview"></span><span class="MathJax" id="MathJax-Element-26-Frame" role="textbox" aria-readonly="true"><nobr><span class="math" id="MathJax-Span-207" style="width: 1.304em; display: inline-block;"><span style="display: inline-block; position: relative; width: 1.033em; height: 0px; font-size: 123%;"><span style="position: absolute; clip: rect(1.683em 1000em 2.604em -0.431em); top: -2.274em; left: 0.003em;"><span class="mrow" id="MathJax-Span-208"><span class="msubsup" id="MathJax-Span-209"><span style="display: inline-block; position: relative; width: 0.978em; height: 0px;"><span style="position: absolute; clip: rect(1.683em 1000em 2.442em -0.431em); top: -2.274em; left: 0.003em;"><span class="mi" id="MathJax-Span-210" style="font-family: MathJax_Math-italic;">x</span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span><span style="position: absolute; top: -2.111em; left: 0.545em;"><span class="mi" id="MathJax-Span-211" style="font-size: 70.7%; font-family: MathJax_Math-italic;">u</span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span></span></span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span><span style="border-left-width: 0.003em; border-left-style: solid; display: inline-block; overflow: hidden; width: 0px; height: 0.87em; vertical-align: -0.263em;"></span></span></nobr></span><script type="math/tex" id="MathJax-Element-26">x_u</script>对商品<span class="MathJax_Preview"></span><span class="MathJax" id="MathJax-Element-27-Frame" role="textbox" aria-readonly="true"><nobr><span class="math" id="MathJax-Span-212" style="width: 1.087em; display: inline-block;"><span style="display: inline-block; position: relative; width: 0.87em; height: 0px; font-size: 123%;"><span style="position: absolute; clip: rect(1.683em 1000em 2.713em -0.485em); top: -2.274em; left: 0.003em;"><span class="mrow" id="MathJax-Span-213"><span class="msubsup" id="MathJax-Span-214"><span style="display: inline-block; position: relative; width: 0.816em; height: 0px;"><span style="position: absolute; clip: rect(1.683em 1000em 2.659em -0.485em); top: -2.274em; left: 0.003em;"><span class="mi" id="MathJax-Span-215" style="font-family: MathJax_Math-italic;">y<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.003em;"></span></span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span><span style="position: absolute; top: -2.057em; left: 0.545em;"><span class="mi" id="MathJax-Span-216" style="font-size: 70.7%; font-family: MathJax_Math-italic;">i</span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span></span></span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span><span style="border-left-width: 0.003em; border-left-style: solid; display: inline-block; overflow: hidden; width: 0px; height: 1.003em; vertical-align: -0.397em;"></span></span></nobr></span><script type="math/tex" id="MathJax-Element-27">y_i</script>的评分的近似。

<p>为防止过拟合给上述公式加上正则项，公式改下为: <br>
<span class="MathJax_Preview"></span></p><div class="MathJax_Display" role="textbox" aria-readonly="true" style="text-align: center;"><span class="MathJax" id="MathJax-Element-28-Frame"><nobr><span class="math" id="MathJax-Span-217" style="width: 26.778em; display: inline-block;"><span style="display: inline-block; position: relative; width: 21.737em; height: 0px; font-size: 123%;"><span style="position: absolute; clip: rect(1.141em 1000em 3.743em -0.431em); top: -2.274em; left: 0.003em;"><span class="mrow" id="MathJax-Span-218"><span class="mi" id="MathJax-Span-219" style="font-family: MathJax_Math-italic;">L</span><span class="mo" id="MathJax-Span-220" style="font-family: MathJax_Main;">(</span><span class="mi" id="MathJax-Span-221" style="font-family: MathJax_Math-italic;">X<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.003em;"></span></span><span class="mo" id="MathJax-Span-222" style="font-family: MathJax_Main;">,</span><span class="mi" id="MathJax-Span-223" style="font-family: MathJax_Math-italic; padding-left: 0.165em;">Y<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.165em;"></span></span><span class="mo" id="MathJax-Span-224" style="font-family: MathJax_Main;">)</span><span class="mo" id="MathJax-Span-225" style="font-family: MathJax_Main; padding-left: 0.274em;">=</span><span class="munderover" id="MathJax-Span-226" style="padding-left: 0.274em;"><span style="display: inline-block; position: relative; width: 1.412em; height: 0px;"><span style="position: absolute; clip: rect(2.008em 1000em 3.743em -0.431em); top: -3.141em; left: 0.003em;"><span class="mo" id="MathJax-Span-227" style="font-family: MathJax_Size2; vertical-align: 0.003em;">∑</span><span style="display: inline-block; width: 0px; height: 3.146em;"></span></span><span style="position: absolute; clip: rect(1.629em 1000em 2.659em -0.485em); top: -1.19em; left: 0.328em;"><span class="texatom" id="MathJax-Span-228"><span class="mrow" id="MathJax-Span-229"><span class="mi" id="MathJax-Span-230" style="font-size: 70.7%; font-family: MathJax_Math-italic;">u</span><span class="mo" id="MathJax-Span-231" style="font-size: 70.7%; font-family: MathJax_Main;">,</span><span class="mi" id="MathJax-Span-232" style="font-size: 70.7%; font-family: MathJax_Math-italic;">i</span></span></span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span></span><span class="texatom" id="MathJax-Span-233" style="padding-left: 0.165em;"><span class="mrow" id="MathJax-Span-234"><span class="mo" id="MathJax-Span-235" style="font-family: MathJax_Main;">(</span><span class="msubsup" id="MathJax-Span-236"><span style="display: inline-block; position: relative; width: 1.087em; height: 0px;"><span style="position: absolute; clip: rect(1.683em 1000em 2.442em -0.485em); top: -2.274em; left: 0.003em;"><span class="mi" id="MathJax-Span-237" style="font-family: MathJax_Math-italic;">r</span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span><span style="position: absolute; top: -2.111em; left: 0.436em;"><span class="texatom" id="MathJax-Span-238"><span class="mrow" id="MathJax-Span-239"><span class="mi" id="MathJax-Span-240" style="font-size: 70.7%; font-family: MathJax_Math-italic;">u</span><span class="mi" id="MathJax-Span-241" style="font-size: 70.7%; font-family: MathJax_Math-italic;">i</span></span></span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span></span><span class="mo" id="MathJax-Span-242" style="font-family: MathJax_Main; padding-left: 0.22em;">−</span><span class="msubsup" id="MathJax-Span-243" style="padding-left: 0.22em;"><span style="display: inline-block; position: relative; width: 0.978em; height: 0px;"><span style="position: absolute; clip: rect(1.683em 1000em 2.442em -0.431em); top: -2.274em; left: 0.003em;"><span class="mi" id="MathJax-Span-244" style="font-family: MathJax_Math-italic;">x</span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span><span style="position: absolute; top: -2.111em; left: 0.545em;"><span class="mi" id="MathJax-Span-245" style="font-size: 70.7%; font-family: MathJax_Math-italic;">u</span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span></span><span class="mo" id="MathJax-Span-246" style="font-family: MathJax_Main; padding-left: 0.22em;">×</span><span class="msubsup" id="MathJax-Span-247" style="padding-left: 0.22em;"><span style="display: inline-block; position: relative; width: 1.195em; height: 0px;"><span style="position: absolute; clip: rect(1.683em 1000em 2.659em -0.485em); top: -2.274em; left: 0.003em;"><span class="mi" id="MathJax-Span-248" style="font-family: MathJax_Math-italic;">y<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.003em;"></span></span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span><span style="position: absolute; clip: rect(1.629em 1000em 2.442em -0.485em); top: -2.599em; left: 0.599em;"><span class="mi" id="MathJax-Span-249" style="font-size: 70.7%; font-family: MathJax_Math-italic;">T<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.111em;"></span></span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span><span style="position: absolute; clip: rect(1.629em 1000em 2.442em -0.485em); top: -1.949em; left: 0.545em;"><span class="mi" id="MathJax-Span-250" style="font-size: 70.7%; font-family: MathJax_Math-italic;">i</span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span></span><span class="msubsup" id="MathJax-Span-251"><span style="display: inline-block; position: relative; width: 0.816em; height: 0px;"><span style="position: absolute; clip: rect(1.358em 1000em 2.713em -0.431em); top: -2.274em; left: 0.003em;"><span class="mo" id="MathJax-Span-252" style="font-family: MathJax_Main;">)</span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span><span style="position: absolute; top: -2.762em; left: 0.382em;"><span class="mn" id="MathJax-Span-253" style="font-size: 70.7%; font-family: MathJax_Main;">2</span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span></span></span></span><span class="mo" id="MathJax-Span-254" style="font-family: MathJax_Main; padding-left: 0.22em;">+</span><span class="mi" id="MathJax-Span-255" style="font-family: MathJax_Math-italic; padding-left: 0.22em;">λ</span><span class="mo" id="MathJax-Span-256" style="font-family: MathJax_Main;">(</span><span class="mo" id="MathJax-Span-257" style="font-family: MathJax_Main;">∣</span><span class="msubsup" id="MathJax-Span-258" style="padding-left: 0.274em;"><span style="display: inline-block; position: relative; width: 0.978em; height: 0px;"><span style="position: absolute; clip: rect(1.683em 1000em 2.442em -0.431em); top: -2.274em; left: 0.003em;"><span class="mi" id="MathJax-Span-259" style="font-family: MathJax_Math-italic;">x</span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span><span style="position: absolute; top: -2.111em; left: 0.545em;"><span class="mi" id="MathJax-Span-260" style="font-size: 70.7%; font-family: MathJax_Math-italic;">u</span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span></span><span class="msubsup" id="MathJax-Span-261" style="padding-left: 0.274em;"><span style="display: inline-block; position: relative; width: 0.707em; height: 0px;"><span style="position: absolute; clip: rect(1.358em 1000em 2.713em -0.377em); top: -2.274em; left: 0.003em;"><span class="mo" id="MathJax-Span-262" style="font-family: MathJax_Main;">∣</span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span><span style="position: absolute; top: -2.762em; left: 0.274em;"><span class="mn" id="MathJax-Span-263" style="font-size: 70.7%; font-family: MathJax_Main;">2</span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span></span><span class="mo" id="MathJax-Span-264" style="font-family: MathJax_Main; padding-left: 0.274em;">+</span><span class="mo" id="MathJax-Span-265" style="font-family: MathJax_Main; padding-left: 0.274em;">∣</span><span class="msubsup" id="MathJax-Span-266" style="padding-left: 0.274em;"><span style="display: inline-block; position: relative; width: 0.816em; height: 0px;"><span style="position: absolute; clip: rect(1.683em 1000em 2.659em -0.485em); top: -2.274em; left: 0.003em;"><span class="mi" id="MathJax-Span-267" style="font-family: MathJax_Math-italic;">y<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.003em;"></span></span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span><span style="position: absolute; top: -2.057em; left: 0.545em;"><span class="mi" id="MathJax-Span-268" style="font-size: 70.7%; font-family: MathJax_Math-italic;">i</span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span></span><span class="msubsup" id="MathJax-Span-269" style="padding-left: 0.274em;"><span style="display: inline-block; position: relative; width: 0.707em; height: 0px;"><span style="position: absolute; clip: rect(1.358em 1000em 2.713em -0.377em); top: -2.274em; left: 0.003em;"><span class="mo" id="MathJax-Span-270" style="font-family: MathJax_Main;">∣</span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span><span style="position: absolute; top: -2.762em; left: 0.274em;"><span class="mn" id="MathJax-Span-271" style="font-size: 70.7%; font-family: MathJax_Main;">2</span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span></span><span class="mo" id="MathJax-Span-272" style="font-family: MathJax_Main;">)</span></span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span><span style="border-left-width: 0.003em; border-left-style: solid; display: inline-block; overflow: hidden; width: 0px; height: 2.937em; vertical-align: -1.663em;"></span></span></nobr></span></div><script type="math/tex; mode=display" id="MathJax-Element-28">
L(X, Y) = \sum_{u, i}{(r_{ui} - x_u \times y^T_i)^2} + \lambda (\mid x_u \mid ^2 + \mid y_i \mid ^2)
</script> <br>
其中，<span class="MathJax_Preview"></span><span class="MathJax" id="MathJax-Element-29-Frame" role="textbox" aria-readonly="true"><nobr><span class="math" id="MathJax-Span-273" style="width: 4.285em; display: inline-block;"><span style="display: inline-block; position: relative; width: 3.472em; height: 0px; font-size: 123%;"><span style="position: absolute; clip: rect(1.195em 1000em 2.604em -0.431em); top: -2.274em; left: 0.003em;"><span class="mrow" id="MathJax-Span-274"><span class="msubsup" id="MathJax-Span-275"><span style="display: inline-block; position: relative; width: 0.978em; height: 0px;"><span style="position: absolute; clip: rect(1.683em 1000em 2.442em -0.431em); top: -2.274em; left: 0.003em;"><span class="mi" id="MathJax-Span-276" style="font-family: MathJax_Math-italic;">x</span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span><span style="position: absolute; top: -2.111em; left: 0.545em;"><span class="mi" id="MathJax-Span-277" style="font-size: 70.7%; font-family: MathJax_Math-italic;">u</span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span></span><span class="mo" id="MathJax-Span-278" style="font-family: MathJax_Main; padding-left: 0.274em;">∈</span><span class="msubsup" id="MathJax-Span-279" style="padding-left: 0.274em;"><span style="display: inline-block; position: relative; width: 1.249em; height: 0px;"><span style="position: absolute; clip: rect(1.412em 1000em 2.442em -0.431em); top: -2.274em; left: 0.003em;"><span class="mi" id="MathJax-Span-280" style="font-family: MathJax_Math-italic;">R</span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span><span style="position: absolute; top: -2.707em; left: 0.762em;"><span class="mi" id="MathJax-Span-281" style="font-size: 70.7%; font-family: MathJax_Math-italic;">d<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.003em;"></span></span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span></span></span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span><span style="border-left-width: 0.003em; border-left-style: solid; display: inline-block; overflow: hidden; width: 0px; height: 1.47em; vertical-align: -0.263em;"></span></span></nobr></span><script type="math/tex" id="MathJax-Element-29"> x_u \in R^d </script>，<span class="MathJax_Preview"></span><span class="MathJax" id="MathJax-Element-30-Frame" role="textbox" aria-readonly="true"><nobr><span class="math" id="MathJax-Span-282" style="width: 4.068em; display: inline-block;"><span style="display: inline-block; position: relative; width: 3.309em; height: 0px; font-size: 123%;"><span style="position: absolute; clip: rect(1.195em 1000em 2.713em -0.485em); top: -2.274em; left: 0.003em;"><span class="mrow" id="MathJax-Span-283"><span class="msubsup" id="MathJax-Span-284"><span style="display: inline-block; position: relative; width: 0.816em; height: 0px;"><span style="position: absolute; clip: rect(1.683em 1000em 2.659em -0.485em); top: -2.274em; left: 0.003em;"><span class="mi" id="MathJax-Span-285" style="font-family: MathJax_Math-italic;">y<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.003em;"></span></span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span><span style="position: absolute; top: -2.057em; left: 0.545em;"><span class="mi" id="MathJax-Span-286" style="font-size: 70.7%; font-family: MathJax_Math-italic;">i</span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span></span><span class="mo" id="MathJax-Span-287" style="font-family: MathJax_Main; padding-left: 0.274em;">∈</span><span class="msubsup" id="MathJax-Span-288" style="padding-left: 0.274em;"><span style="display: inline-block; position: relative; width: 1.249em; height: 0px;"><span style="position: absolute; clip: rect(1.412em 1000em 2.442em -0.431em); top: -2.274em; left: 0.003em;"><span class="mi" id="MathJax-Span-289" style="font-family: MathJax_Math-italic;">R</span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span><span style="position: absolute; top: -2.707em; left: 0.762em;"><span class="mi" id="MathJax-Span-290" style="font-size: 70.7%; font-family: MathJax_Math-italic;">d<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.003em;"></span></span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span></span></span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span><span style="border-left-width: 0.003em; border-left-style: solid; display: inline-block; overflow: hidden; width: 0px; height: 1.537em; vertical-align: -0.397em;"></span></span></nobr></span><script type="math/tex" id="MathJax-Element-30"> y_i \in R^d </script>， <span class="MathJax_Preview"></span><span class="MathJax" id="MathJax-Element-31-Frame" role="textbox" aria-readonly="true"><nobr><span class="math" id="MathJax-Span-291" style="width: 5.531em; display: inline-block;"><span style="display: inline-block; position: relative; width: 4.501em; height: 0px; font-size: 123%;"><span style="position: absolute; clip: rect(1.466em 1000em 2.604em -0.377em); top: -2.274em; left: 0.003em;"><span class="mrow" id="MathJax-Span-292"><span class="mn" id="MathJax-Span-293" style="font-family: MathJax_Main;">1</span><span class="mo" id="MathJax-Span-294" style="font-family: MathJax_Main; padding-left: 0.274em;">≤</span><span class="mi" id="MathJax-Span-295" style="font-family: MathJax_Math-italic; padding-left: 0.274em;">u</span><span class="mo" id="MathJax-Span-296" style="font-family: MathJax_Main; padding-left: 0.274em;">≤</span><span class="mi" id="MathJax-Span-297" style="font-family: MathJax_Math-italic; padding-left: 0.274em;">m</span></span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span><span style="border-left-width: 0.003em; border-left-style: solid; display: inline-block; overflow: hidden; width: 0px; height: 1.137em; vertical-align: -0.263em;"></span></span></nobr></span><script type="math/tex" id="MathJax-Element-31"> 1 \leq u \leq m </script>，<span class="MathJax_Preview"></span><span class="MathJax" id="MathJax-Element-32-Frame" role="textbox" aria-readonly="true"><nobr><span class="math" id="MathJax-Span-298" style="width: 4.935em; display: inline-block;"><span style="display: inline-block; position: relative; width: 4.014em; height: 0px; font-size: 123%;"><span style="position: absolute; clip: rect(1.466em 1000em 2.604em -0.377em); top: -2.274em; left: 0.003em;"><span class="mrow" id="MathJax-Span-299"><span class="mn" id="MathJax-Span-300" style="font-family: MathJax_Main;">1</span><span class="mo" id="MathJax-Span-301" style="font-family: MathJax_Main; padding-left: 0.274em;">≤</span><span class="mi" id="MathJax-Span-302" style="font-family: MathJax_Math-italic; padding-left: 0.274em;">i</span><span class="mo" id="MathJax-Span-303" style="font-family: MathJax_Main; padding-left: 0.274em;">≤</span><span class="mi" id="MathJax-Span-304" style="font-family: MathJax_Math-italic; padding-left: 0.274em;">n</span></span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span><span style="border-left-width: 0.003em; border-left-style: solid; display: inline-block; overflow: hidden; width: 0px; height: 1.137em; vertical-align: -0.263em;"></span></span></nobr></span><script type="math/tex" id="MathJax-Element-32"> 1 \leq i \leq n </script>，<span class="MathJax_Preview"></span><span class="MathJax" id="MathJax-Element-33-Frame" role="textbox" aria-readonly="true"><nobr><span class="math" id="MathJax-Span-305" style="width: 0.707em; display: inline-block;"><span style="display: inline-block; position: relative; width: 0.545em; height: 0px; font-size: 123%;"><span style="position: absolute; clip: rect(1.412em 1000em 2.442em -0.431em); top: -2.274em; left: 0.003em;"><span class="mrow" id="MathJax-Span-306"><span class="mi" id="MathJax-Span-307" style="font-family: MathJax_Math-italic;">λ</span></span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span><span style="border-left-width: 0.003em; border-left-style: solid; display: inline-block; overflow: hidden; width: 0px; height: 1.003em; vertical-align: -0.063em;"></span></span></nobr></span><script type="math/tex" id="MathJax-Element-33"> \lambda </script>是正则项的系数。

<p>到这里，协同过滤就成功转化成了一个优化问题。由于变量<span class="MathJax_Preview"></span><span class="MathJax" id="MathJax-Element-34-Frame" role="textbox" aria-readonly="true"><nobr><span class="math" id="MathJax-Span-308" style="width: 1.304em; display: inline-block;"><span style="display: inline-block; position: relative; width: 1.033em; height: 0px; font-size: 123%;"><span style="position: absolute; clip: rect(1.683em 1000em 2.604em -0.431em); top: -2.274em; left: 0.003em;"><span class="mrow" id="MathJax-Span-309"><span class="msubsup" id="MathJax-Span-310"><span style="display: inline-block; position: relative; width: 0.978em; height: 0px;"><span style="position: absolute; clip: rect(1.683em 1000em 2.442em -0.431em); top: -2.274em; left: 0.003em;"><span class="mi" id="MathJax-Span-311" style="font-family: MathJax_Math-italic;">x</span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span><span style="position: absolute; top: -2.111em; left: 0.545em;"><span class="mi" id="MathJax-Span-312" style="font-size: 70.7%; font-family: MathJax_Math-italic;">u</span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span></span></span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span><span style="border-left-width: 0.003em; border-left-style: solid; display: inline-block; overflow: hidden; width: 0px; height: 0.87em; vertical-align: -0.263em;"></span></span></nobr></span><script type="math/tex" id="MathJax-Element-34">x_u</script>和<span class="MathJax_Preview"></span><span class="MathJax" id="MathJax-Element-35-Frame" role="textbox" aria-readonly="true"><nobr><span class="math" id="MathJax-Span-313" style="width: 1.087em; display: inline-block;"><span style="display: inline-block; position: relative; width: 0.87em; height: 0px; font-size: 123%;"><span style="position: absolute; clip: rect(1.683em 1000em 2.713em -0.485em); top: -2.274em; left: 0.003em;"><span class="mrow" id="MathJax-Span-314"><span class="msubsup" id="MathJax-Span-315"><span style="display: inline-block; position: relative; width: 0.816em; height: 0px;"><span style="position: absolute; clip: rect(1.683em 1000em 2.659em -0.485em); top: -2.274em; left: 0.003em;"><span class="mi" id="MathJax-Span-316" style="font-family: MathJax_Math-italic;">y<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.003em;"></span></span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span><span style="position: absolute; top: -2.057em; left: 0.545em;"><span class="mi" id="MathJax-Span-317" style="font-size: 70.7%; font-family: MathJax_Math-italic;">i</span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span></span></span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span><span style="border-left-width: 0.003em; border-left-style: solid; display: inline-block; overflow: hidden; width: 0px; height: 1.003em; vertical-align: -0.397em;"></span></span></nobr></span><script type="math/tex" id="MathJax-Element-35">y_i</script>耦合到一起，这个问题并不好求解，所以我们引入了ALS，也就是说我们可以先固定Y（例如随机初始化X），然后利用公式（2）先求解X，然后固定X，再求解Y，如此交替往复直至收敛，即所谓的交替最小二乘法求解法。</p>

<p>具体的求解方法说明如下： <br>
+ 先固定Y，将损失函数<span class="MathJax_Preview"></span><span class="MathJax" id="MathJax-Element-36-Frame" role="textbox" aria-readonly="true"><nobr><span class="math" id="MathJax-Span-318" style="width: 4.23em; display: inline-block;"><span style="display: inline-block; position: relative; width: 3.417em; height: 0px; font-size: 123%;"><span style="position: absolute; clip: rect(1.358em 1000em 2.713em -0.431em); top: -2.274em; left: 0.003em;"><span class="mrow" id="MathJax-Span-319"><span class="mi" id="MathJax-Span-320" style="font-family: MathJax_Math-italic;">L</span><span class="mo" id="MathJax-Span-321" style="font-family: MathJax_Main;">(</span><span class="mi" id="MathJax-Span-322" style="font-family: MathJax_Math-italic;">X<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.003em;"></span></span><span class="mo" id="MathJax-Span-323" style="font-family: MathJax_Main;">,</span><span class="mi" id="MathJax-Span-324" style="font-family: MathJax_Math-italic; padding-left: 0.165em;">Y<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.165em;"></span></span><span class="mo" id="MathJax-Span-325" style="font-family: MathJax_Main;">)</span></span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span><span style="border-left-width: 0.003em; border-left-style: solid; display: inline-block; overflow: hidden; width: 0px; height: 1.337em; vertical-align: -0.397em;"></span></span></nobr></span><script type="math/tex" id="MathJax-Element-36">L(X, Y)</script>对<span class="MathJax_Preview"></span><span class="MathJax" id="MathJax-Element-37-Frame" role="textbox" aria-readonly="true"><nobr><span class="math" id="MathJax-Span-326" style="width: 1.304em; display: inline-block;"><span style="display: inline-block; position: relative; width: 1.033em; height: 0px; font-size: 123%;"><span style="position: absolute; clip: rect(1.683em 1000em 2.604em -0.431em); top: -2.274em; left: 0.003em;"><span class="mrow" id="MathJax-Span-327"><span class="msubsup" id="MathJax-Span-328"><span style="display: inline-block; position: relative; width: 0.978em; height: 0px;"><span style="position: absolute; clip: rect(1.683em 1000em 2.442em -0.431em); top: -2.274em; left: 0.003em;"><span class="mi" id="MathJax-Span-329" style="font-family: MathJax_Math-italic;">x</span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span><span style="position: absolute; top: -2.111em; left: 0.545em;"><span class="mi" id="MathJax-Span-330" style="font-size: 70.7%; font-family: MathJax_Math-italic;">u</span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span></span></span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span><span style="border-left-width: 0.003em; border-left-style: solid; display: inline-block; overflow: hidden; width: 0px; height: 0.87em; vertical-align: -0.263em;"></span></span></nobr></span><script type="math/tex" id="MathJax-Element-37">x_u</script>求偏导，并令导数等于零，得到：<span class="MathJax_Preview"></span><span class="MathJax" id="MathJax-Element-38-Frame" role="textbox" aria-readonly="true"><nobr><span class="math" id="MathJax-Span-331" style="width: 13.282em; display: inline-block;"><span style="display: inline-block; position: relative; width: 10.789em; height: 0px; font-size: 123%;"><span style="position: absolute; clip: rect(1.195em 1000em 2.713em -0.431em); top: -2.274em; left: 0.003em;"><span class="mrow" id="MathJax-Span-332"><span class="msubsup" id="MathJax-Span-333"><span style="display: inline-block; position: relative; width: 0.978em; height: 0px;"><span style="position: absolute; clip: rect(1.683em 1000em 2.442em -0.431em); top: -2.274em; left: 0.003em;"><span class="mi" id="MathJax-Span-334" style="font-family: MathJax_Math-italic;">x</span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span><span style="position: absolute; top: -2.111em; left: 0.545em;"><span class="mi" id="MathJax-Span-335" style="font-size: 70.7%; font-family: MathJax_Math-italic;">u</span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span></span><span class="mo" id="MathJax-Span-336" style="font-family: MathJax_Main; padding-left: 0.274em;">=</span><span class="mo" id="MathJax-Span-337" style="font-family: MathJax_Main; padding-left: 0.274em;">(</span><span class="msubsup" id="MathJax-Span-338"><span style="display: inline-block; position: relative; width: 1.466em; height: 0px;"><span style="position: absolute; clip: rect(1.412em 1000em 2.442em -0.431em); top: -2.274em; left: 0.003em;"><span class="mi" id="MathJax-Span-339" style="font-family: MathJax_Math-italic;">Y<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.165em;"></span></span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span><span style="position: absolute; top: -2.707em; left: 0.87em;"><span class="mi" id="MathJax-Span-340" style="font-size: 70.7%; font-family: MathJax_Math-italic;">T<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.111em;"></span></span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span></span><span class="mi" id="MathJax-Span-341" style="font-family: MathJax_Math-italic;">Y<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.165em;"></span></span><span class="mo" id="MathJax-Span-342" style="font-family: MathJax_Main; padding-left: 0.22em;">+</span><span class="mi" id="MathJax-Span-343" style="font-family: MathJax_Math-italic; padding-left: 0.22em;">λ</span><span class="mi" id="MathJax-Span-344" style="font-family: MathJax_Math-italic;">I<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.057em;"></span></span><span class="msubsup" id="MathJax-Span-345"><span style="display: inline-block; position: relative; width: 1.358em; height: 0px;"><span style="position: absolute; clip: rect(1.358em 1000em 2.713em -0.431em); top: -2.274em; left: 0.003em;"><span class="mo" id="MathJax-Span-346" style="font-family: MathJax_Main;">)</span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span><span style="position: absolute; top: -2.762em; left: 0.382em;"><span class="texatom" id="MathJax-Span-347"><span class="mrow" id="MathJax-Span-348"><span class="mo" id="MathJax-Span-349" style="font-size: 70.7%; font-family: MathJax_Main;">−</span><span class="mn" id="MathJax-Span-350" style="font-size: 70.7%; font-family: MathJax_Main;">1</span></span></span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span></span><span class="msubsup" id="MathJax-Span-351"><span style="display: inline-block; position: relative; width: 1.466em; height: 0px;"><span style="position: absolute; clip: rect(1.412em 1000em 2.442em -0.431em); top: -2.274em; left: 0.003em;"><span class="mi" id="MathJax-Span-352" style="font-family: MathJax_Math-italic;">Y<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.165em;"></span></span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span><span style="position: absolute; top: -2.707em; left: 0.87em;"><span class="mi" id="MathJax-Span-353" style="font-size: 70.7%; font-family: MathJax_Math-italic;">T<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.111em;"></span></span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span></span><span class="msubsup" id="MathJax-Span-354"><span style="display: inline-block; position: relative; width: 0.87em; height: 0px;"><span style="position: absolute; clip: rect(1.683em 1000em 2.442em -0.485em); top: -2.274em; left: 0.003em;"><span class="mi" id="MathJax-Span-355" style="font-family: MathJax_Math-italic;">r</span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span><span style="position: absolute; top: -2.111em; left: 0.436em;"><span class="mi" id="MathJax-Span-356" style="font-size: 70.7%; font-family: MathJax_Math-italic;">u</span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span></span></span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span><span style="border-left-width: 0.003em; border-left-style: solid; display: inline-block; overflow: hidden; width: 0px; height: 1.603em; vertical-align: -0.397em;"></span></span></nobr></span><script type="math/tex" id="MathJax-Element-38">x_u = (Y^TY + \lambda I)^{-1}Y^Tr_u</script> <br>
+ 同理固定X，可得： <br>
<span class="MathJax_Preview"></span><span class="MathJax" id="MathJax-Element-39-Frame" role="textbox" aria-readonly="true"><nobr><span class="math" id="MathJax-Span-357" style="width: 13.173em; display: inline-block;"><span style="display: inline-block; position: relative; width: 10.68em; height: 0px; font-size: 123%;"><span style="position: absolute; clip: rect(1.195em 1000em 2.713em -0.485em); top: -2.274em; left: 0.003em;"><span class="mrow" id="MathJax-Span-358"><span class="msubsup" id="MathJax-Span-359"><span style="display: inline-block; position: relative; width: 0.816em; height: 0px;"><span style="position: absolute; clip: rect(1.683em 1000em 2.659em -0.485em); top: -2.274em; left: 0.003em;"><span class="mi" id="MathJax-Span-360" style="font-family: MathJax_Math-italic;">y<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.003em;"></span></span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span><span style="position: absolute; top: -2.057em; left: 0.545em;"><span class="mi" id="MathJax-Span-361" style="font-size: 70.7%; font-family: MathJax_Math-italic;">i</span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span></span><span class="mo" id="MathJax-Span-362" style="font-family: MathJax_Main; padding-left: 0.274em;">=</span><span class="mo" id="MathJax-Span-363" style="font-family: MathJax_Main; padding-left: 0.274em;">(</span><span class="msubsup" id="MathJax-Span-364"><span style="display: inline-block; position: relative; width: 1.52em; height: 0px;"><span style="position: absolute; clip: rect(1.412em 1000em 2.442em -0.485em); top: -2.274em; left: 0.003em;"><span class="mi" id="MathJax-Span-365" style="font-family: MathJax_Math-italic;">X<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.003em;"></span></span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span><span style="position: absolute; top: -2.707em; left: 0.924em;"><span class="mi" id="MathJax-Span-366" style="font-size: 70.7%; font-family: MathJax_Math-italic;">T<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.111em;"></span></span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span></span><span class="mi" id="MathJax-Span-367" style="font-family: MathJax_Math-italic;">X<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.003em;"></span></span><span class="mo" id="MathJax-Span-368" style="font-family: MathJax_Main; padding-left: 0.22em;">+</span><span class="mi" id="MathJax-Span-369" style="font-family: MathJax_Math-italic; padding-left: 0.22em;">λ</span><span class="mi" id="MathJax-Span-370" style="font-family: MathJax_Math-italic;">I<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.057em;"></span></span><span class="msubsup" id="MathJax-Span-371"><span style="display: inline-block; position: relative; width: 1.358em; height: 0px;"><span style="position: absolute; clip: rect(1.358em 1000em 2.713em -0.431em); top: -2.274em; left: 0.003em;"><span class="mo" id="MathJax-Span-372" style="font-family: MathJax_Main;">)</span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span><span style="position: absolute; top: -2.762em; left: 0.382em;"><span class="texatom" id="MathJax-Span-373"><span class="mrow" id="MathJax-Span-374"><span class="mo" id="MathJax-Span-375" style="font-size: 70.7%; font-family: MathJax_Main;">−</span><span class="mn" id="MathJax-Span-376" style="font-size: 70.7%; font-family: MathJax_Main;">1</span></span></span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span></span><span class="msubsup" id="MathJax-Span-377"><span style="display: inline-block; position: relative; width: 1.52em; height: 0px;"><span style="position: absolute; clip: rect(1.412em 1000em 2.442em -0.485em); top: -2.274em; left: 0.003em;"><span class="mi" id="MathJax-Span-378" style="font-family: MathJax_Math-italic;">X<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.003em;"></span></span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span><span style="position: absolute; top: -2.707em; left: 0.924em;"><span class="mi" id="MathJax-Span-379" style="font-size: 70.7%; font-family: MathJax_Math-italic;">T<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.111em;"></span></span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span></span><span class="msubsup" id="MathJax-Span-380"><span style="display: inline-block; position: relative; width: 0.707em; height: 0px;"><span style="position: absolute; clip: rect(1.683em 1000em 2.442em -0.485em); top: -2.274em; left: 0.003em;"><span class="mi" id="MathJax-Span-381" style="font-family: MathJax_Math-italic;">r</span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span><span style="position: absolute; top: -2.111em; left: 0.436em;"><span class="mi" id="MathJax-Span-382" style="font-size: 70.7%; font-family: MathJax_Math-italic;">i</span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span></span></span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span><span style="border-left-width: 0.003em; border-left-style: solid; display: inline-block; overflow: hidden; width: 0px; height: 1.603em; vertical-align: -0.397em;"></span></span></nobr></span><script type="math/tex" id="MathJax-Element-39">y_i = (X^TX + \lambda I)^{-1}X^Tr_i</script></p>

<p>其中，<span class="MathJax_Preview"></span><span class="MathJax" id="MathJax-Element-40-Frame" role="textbox" aria-readonly="true"><nobr><span class="math" id="MathJax-Span-383" style="width: 4.881em; display: inline-block;"><span style="display: inline-block; position: relative; width: 3.959em; height: 0px; font-size: 123%;"><span style="position: absolute; clip: rect(1.358em 1000em 2.713em -0.485em); top: -2.274em; left: 0.003em;"><span class="mrow" id="MathJax-Span-384"><span class="msubsup" id="MathJax-Span-385"><span style="display: inline-block; position: relative; width: 0.87em; height: 0px;"><span style="position: absolute; clip: rect(1.683em 1000em 2.442em -0.485em); top: -2.274em; left: 0.003em;"><span class="mi" id="MathJax-Span-386" style="font-family: MathJax_Math-italic;">r</span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span><span style="position: absolute; top: -2.111em; left: 0.436em;"><span class="mi" id="MathJax-Span-387" style="font-size: 70.7%; font-family: MathJax_Math-italic;">u</span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span></span><span class="mo" id="MathJax-Span-388" style="font-family: MathJax_Main;">(</span><span class="mn" id="MathJax-Span-389" style="font-family: MathJax_Main;">1</span><span class="mo" id="MathJax-Span-390" style="font-family: MathJax_Main; padding-left: 0.22em;">×</span><span class="mi" id="MathJax-Span-391" style="font-family: MathJax_Math-italic; padding-left: 0.22em;">n</span><span class="mo" id="MathJax-Span-392" style="font-family: MathJax_Main;">)</span></span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span><span style="border-left-width: 0.003em; border-left-style: solid; display: inline-block; overflow: hidden; width: 0px; height: 1.337em; vertical-align: -0.397em;"></span></span></nobr></span><script type="math/tex" id="MathJax-Element-40">r_u(1 \times n)</script>是R的第u行，<span class="MathJax_Preview"></span><span class="MathJax" id="MathJax-Element-41-Frame" role="textbox" aria-readonly="true"><nobr><span class="math" id="MathJax-Span-393" style="width: 5.043em; display: inline-block;"><span style="display: inline-block; position: relative; width: 4.068em; height: 0px; font-size: 123%;"><span style="position: absolute; clip: rect(1.358em 1000em 2.713em -0.485em); top: -2.274em; left: 0.003em;"><span class="mrow" id="MathJax-Span-394"><span class="msubsup" id="MathJax-Span-395"><span style="display: inline-block; position: relative; width: 0.707em; height: 0px;"><span style="position: absolute; clip: rect(1.683em 1000em 2.442em -0.485em); top: -2.274em; left: 0.003em;"><span class="mi" id="MathJax-Span-396" style="font-family: MathJax_Math-italic;">r</span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span><span style="position: absolute; top: -2.111em; left: 0.436em;"><span class="mi" id="MathJax-Span-397" style="font-size: 70.7%; font-family: MathJax_Math-italic;">i</span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span></span><span class="mo" id="MathJax-Span-398" style="font-family: MathJax_Main;">(</span><span class="mn" id="MathJax-Span-399" style="font-family: MathJax_Main;">1</span><span class="mo" id="MathJax-Span-400" style="font-family: MathJax_Main; padding-left: 0.22em;">×</span><span class="mi" id="MathJax-Span-401" style="font-family: MathJax_Math-italic; padding-left: 0.22em;">m</span><span class="mo" id="MathJax-Span-402" style="font-family: MathJax_Main;">)</span></span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span><span style="border-left-width: 0.003em; border-left-style: solid; display: inline-block; overflow: hidden; width: 0px; height: 1.337em; vertical-align: -0.397em;"></span></span></nobr></span><script type="math/tex" id="MathJax-Element-41">r_i(1 \times m)</script>是R的第i列，I是<span class="MathJax_Preview"></span><span class="MathJax" id="MathJax-Element-42-Frame" role="textbox" aria-readonly="true"><nobr><span class="math" id="MathJax-Span-403" style="width: 2.713em; display: inline-block;"><span style="display: inline-block; position: relative; width: 2.171em; height: 0px; font-size: 123%;"><span style="position: absolute; clip: rect(1.412em 1000em 2.442em -0.431em); top: -2.274em; left: 0.003em;"><span class="mrow" id="MathJax-Span-404"><span class="mi" id="MathJax-Span-405" style="font-family: MathJax_Math-italic;">k</span><span class="mo" id="MathJax-Span-406" style="font-family: MathJax_Main; padding-left: 0.22em;">×</span><span class="mi" id="MathJax-Span-407" style="font-family: MathJax_Math-italic; padding-left: 0.22em;">k</span></span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span><span style="border-left-width: 0.003em; border-left-style: solid; display: inline-block; overflow: hidden; width: 0px; height: 1.003em; vertical-align: -0.063em;"></span></span></nobr></span><script type="math/tex" id="MathJax-Element-42"> k \times k</script>的单位矩阵。</p>



<h3 id="iteration-scenario">Iteration Scenario</h3>

<p>首先随机初始化Y，利用固定Y后得到的公式更新得到X，然后固定X得到的公式更新Y，直到均方根误差变RMSE化很小或者到达最大迭代次数。</p>



<h3 id="envaluation">Envaluation</h3>

<p>当然，我们不能凭着自己的感觉评价模型的好坏，尽管我们直觉告诉我们，这个结果看上去不错。我们需要量化的指标来评价模型的优劣。我们可以通过计算均方根误差(Root Mean Squared Error, RMSE)来衡量模型的好坏。数理统计中均方根误差是指参数估计值与参数真值之差平方的期望值，记为RMSE， <br>
<span class="MathJax_Preview"></span></p><div class="MathJax_Display" role="textbox" aria-readonly="true" style="text-align: center;"><span class="MathJax" id="MathJax-Element-43-Frame"><nobr><span class="math" id="MathJax-Span-408" style="width: 13.282em; display: inline-block;"><span style="display: inline-block; position: relative; width: 10.789em; height: 0px; font-size: 123%;"><span style="position: absolute; clip: rect(0.111em 1000em 3.472em -0.431em); top: -2.274em; left: 0.003em;"><span class="mrow" id="MathJax-Span-409"><span class="mi" id="MathJax-Span-410" style="font-family: MathJax_Math-italic;">R</span><span class="mi" id="MathJax-Span-411" style="font-family: MathJax_Math-italic;">M<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.057em;"></span></span><span class="mi" id="MathJax-Span-412" style="font-family: MathJax_Math-italic;">S<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.057em;"></span></span><span class="mi" id="MathJax-Span-413" style="font-family: MathJax_Math-italic;">E<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.003em;"></span></span><span class="mo" id="MathJax-Span-414" style="font-family: MathJax_Main; padding-left: 0.274em;">=</span><span class="msqrt" id="MathJax-Span-415" style="padding-left: 0.274em;"><span style="display: inline-block; position: relative; width: 6.344em; height: 0px;"><span style="position: absolute; clip: rect(2.171em 1000em 4.881em -0.485em); top: -4.008em; left: 0.978em;"><span class="mrow" id="MathJax-Span-416"><span class="mfrac" id="MathJax-Span-417"><span style="display: inline-block; position: relative; width: 5.098em; height: 0px; margin-right: 0.111em; margin-left: 0.111em;"><span style="position: absolute; clip: rect(1.195em 1000em 2.767em -0.431em); top: -3.033em; left: 50%; margin-left: -2.491em;"><span class="mrow" id="MathJax-Span-418"><span class="mo" id="MathJax-Span-419" style="font-family: MathJax_Size1; vertical-align: 0.003em;">∑</span><span class="mo" id="MathJax-Span-420" style="font-family: MathJax_Main;">(</span><span class="mi" id="MathJax-Span-421" style="font-family: MathJax_Math-italic;">R</span><span class="mo" id="MathJax-Span-422" style="font-family: MathJax_Main; padding-left: 0.22em;">−</span><span class="texatom" id="MathJax-Span-423" style="padding-left: 0.22em;"><span class="mrow" id="MathJax-Span-424"><span class="munderover" id="MathJax-Span-425"><span style="display: inline-block; position: relative; width: 0.762em; height: 0px;"><span style="position: absolute; clip: rect(1.412em 1000em 2.442em -0.431em); top: -2.274em; left: 0.003em;"><span class="mi" id="MathJax-Span-426" style="font-family: MathJax_Math-italic;">R</span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span><span style="position: absolute; clip: rect(1.412em 1000em 1.9em -0.377em); top: -2.545em; left: 0.22em;"><span class="mo" id="MathJax-Span-427" style="font-family: MathJax_Main;">^</span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span></span></span></span><span class="msubsup" id="MathJax-Span-428"><span style="display: inline-block; position: relative; width: 0.816em; height: 0px;"><span style="position: absolute; clip: rect(1.358em 1000em 2.713em -0.431em); top: -2.274em; left: 0.003em;"><span class="mo" id="MathJax-Span-429" style="font-family: MathJax_Main;">)</span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span><span style="position: absolute; top: -2.762em; left: 0.382em;"><span class="mn" id="MathJax-Span-430" style="font-size: 70.7%; font-family: MathJax_Main;">2</span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span></span></span><span style="display: inline-block; width: 0px; height: 2.333em;"></span></span><span style="position: absolute; clip: rect(1.412em 1000em 2.442em -0.431em); top: -1.569em; left: 50%; margin-left: -0.431em;"><span class="mi" id="MathJax-Span-431" style="font-family: MathJax_Math-italic;">N<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.111em;"></span></span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span><span style="position: absolute; clip: rect(0.87em 1000em 1.249em -0.485em); top: -1.298em; left: 0.003em;"><span style="border-left-width: 5.098em; border-left-style: solid; display: inline-block; overflow: hidden; width: 0px; height: 1.25px; vertical-align: 0.003em;"></span><span style="display: inline-block; width: 0px; height: 1.087em;"></span></span></span></span></span><span style="display: inline-block; width: 0px; height: 4.014em;"></span></span><span style="position: absolute; clip: rect(3.58em 1000em 4.068em -0.485em); top: -5.688em; left: 0.978em;"><span style="display: inline-block; position: relative; width: 5.369em; height: 0px;"><span style="position: absolute; font-family: MathJax_Main; top: -4.008em; left: -0.106em;">−<span style="display: inline-block; width: 0px; height: 4.014em;"></span></span><span style="position: absolute; font-family: MathJax_Main; top: -4.008em; left: 4.664em;">−<span style="display: inline-block; width: 0px; height: 4.014em;"></span></span><span style="font-family: MathJax_Main; position: absolute; top: -4.008em; left: 0.436em;">−<span style="display: inline-block; width: 0px; height: 4.014em;"></span></span><span style="font-family: MathJax_Main; position: absolute; top: -4.008em; left: 0.978em;">−<span style="display: inline-block; width: 0px; height: 4.014em;"></span></span><span style="font-family: MathJax_Main; position: absolute; top: -4.008em; left: 1.466em;">−<span style="display: inline-block; width: 0px; height: 4.014em;"></span></span><span style="font-family: MathJax_Main; position: absolute; top: -4.008em; left: 2.008em;">−<span style="display: inline-block; width: 0px; height: 4.014em;"></span></span><span style="font-family: MathJax_Main; position: absolute; top: -4.008em; left: 2.55em;">−<span style="display: inline-block; width: 0px; height: 4.014em;"></span></span><span style="font-family: MathJax_Main; position: absolute; top: -4.008em; left: 3.092em;">−<span style="display: inline-block; width: 0px; height: 4.014em;"></span></span><span style="font-family: MathJax_Main; position: absolute; top: -4.008em; left: 3.634em;">−<span style="display: inline-block; width: 0px; height: 4.014em;"></span></span><span style="font-family: MathJax_Main; position: absolute; top: -4.008em; left: 4.122em;">−<span style="display: inline-block; width: 0px; height: 4.014em;"></span></span></span><span style="display: inline-block; width: 0px; height: 4.014em;"></span></span><span style="position: absolute; clip: rect(2.117em 1000em 5.423em -0.377em); top: -4.225em; left: 0.003em;"><span style="font-family: MathJax_Size4;">√</span><span style="display: inline-block; width: 0px; height: 4.014em;"></span></span></span></span></span><span style="display: inline-block; width: 0px; height: 2.279em;"></span></span></span><span style="border-left-width: 0.003em; border-left-style: solid; display: inline-block; overflow: hidden; width: 0px; height: 3.803em; vertical-align: -1.33em;"></span></span></nobr></span></div><script type="math/tex; mode=display" id="MathJax-Element-43">
RMSE = \sqrt \frac{\sum(R - \hat R)^2}{N}
</script> <br>
RMSE是衡量“平均误差”的一种较方便的方法，RMSE可以评价数据的变化程度，RMSE的值越小，说明预测模型描述实验数据具有更好的精确度。

<p>我们可以调整rank，numIterations，lambda，alpha这些参数，不断优化结果，使均方差变小。比如：iterations越多，lambda较小，均方差会较小，推荐结果较优。</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>