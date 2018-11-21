---
layout:     post
title:      试用Hive on Spark
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="试用hive-on-spark">试用Hive on Spark</h1>

<p>Hive-1.2.0发布已经有几天了，下载后发现hive.execution.engine新增了spark选项，于是配置spark环境，试试Hive on Spark。</p>

<h2 id="配置hive">配置Hive</h2>

<p>配置hive-site.xml</p>

<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-tag">&lt;<span class="hljs-title">configuration</span>&gt;</span>

<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>javax.jdo.option.ConnectionURL<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>jdbc:mysql://n1:3306/hive12mb?createDatabaseIfNotExist=true<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-title">description</span>&gt;</span>JDBC connect string for a JDBC metastore<span class="hljs-tag">&lt;/<span class="hljs-title">description</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>

<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>javax.jdo.option.ConnectionDriverName<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>com.mysql.jdbc.Driver<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-title">description</span>&gt;</span>Driver class name for a JDBC metastore<span class="hljs-tag">&lt;/<span class="hljs-title">description</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>

<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>javax.jdo.option.ConnectionUserName<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>root<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-title">description</span>&gt;</span>username to use against metastore database<span class="hljs-tag">&lt;/<span class="hljs-title">description</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>

<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>javax.jdo.option.ConnectionPassword<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>123456<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-title">description</span>&gt;</span>password to use against metastore database<span class="hljs-tag">&lt;/<span class="hljs-title">description</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>

<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>hive.execution.engine<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>spark<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>

<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>hive.stats.dbconnectionstring<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>jdbc:mysql://n1:3306/hive_stats?createDatabaseIfNotExist=true&amp;amp;user=root&amp;amp;password=123456<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-title">description</span>&gt;</span>The default connection string for the database that stores temporary hive statistics.<span class="hljs-tag">&lt;/<span class="hljs-title">description</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>

<span class="hljs-comment">&lt;!--
&lt;property&gt;
&lt;name&gt;hive.metastore.local&lt;/name&gt;
&lt;value&gt;false&lt;/value&gt;
&lt;description&gt;controls whether to connect to remove metastore server
or open a new metastore server in Hive Client JVM&lt;/description&gt;
&lt;/property&gt;
&lt;property&gt;
&lt;name&gt;hive.metastore.uris&lt;/name&gt;
&lt;value&gt;thrift://n1:9083&lt;/value&gt;
&lt;description&gt;controls whether to connect to remove metastore server
or open a new metastore server in Hive Client JVM&lt;/description&gt;
&lt;/property&gt;
--&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">configuration</span>&gt;</span></code></pre>



<h2 id="编译spark">编译Spark</h2>

<p>下载最新Spark代码，修改pom.xml文件并编译</p>

<pre class="prettyprint"><code class=" hljs ruby">  &lt;modules&gt;
    &lt;<span class="hljs-class"><span class="hljs-keyword">module</span>&gt;<span class="hljs-title">core</span><span class="hljs-inheritance">&lt;</span>/<span class="hljs-title">module</span>&gt;</span>
    &lt;<span class="hljs-class"><span class="hljs-keyword">module</span>&gt;<span class="hljs-title">bagel</span><span class="hljs-inheritance">&lt;</span>/<span class="hljs-title">module</span>&gt;</span>
    &lt;<span class="hljs-class"><span class="hljs-keyword">module</span>&gt;<span class="hljs-title">graphx</span><span class="hljs-inheritance">&lt;</span>/<span class="hljs-title">module</span>&gt;</span>
    &lt;<span class="hljs-class"><span class="hljs-keyword">module</span>&gt;<span class="hljs-title">mllib</span><span class="hljs-inheritance">&lt;</span>/<span class="hljs-title">module</span>&gt;</span>
    &lt;<span class="hljs-class"><span class="hljs-keyword">module</span>&gt;<span class="hljs-title">tools</span><span class="hljs-inheritance">&lt;</span>/<span class="hljs-title">module</span>&gt;</span>
    &lt;<span class="hljs-class"><span class="hljs-keyword">module</span>&gt;<span class="hljs-title">network</span>/<span class="hljs-title">common</span><span class="hljs-inheritance">&lt;</span>/<span class="hljs-title">module</span>&gt;</span>
    &lt;<span class="hljs-class"><span class="hljs-keyword">module</span>&gt;<span class="hljs-title">network</span>/<span class="hljs-title">shuffle</span><span class="hljs-inheritance">&lt;</span>/<span class="hljs-title">module</span>&gt;</span>
    &lt;<span class="hljs-class"><span class="hljs-keyword">module</span>&gt;<span class="hljs-title">streaming</span><span class="hljs-inheritance">&lt;</span>/<span class="hljs-title">module</span>&gt;</span>
    &lt;<span class="hljs-class"><span class="hljs-keyword">module</span>&gt;<span class="hljs-title">sql</span>/<span class="hljs-title">catalyst</span><span class="hljs-inheritance">&lt;</span>/<span class="hljs-title">module</span>&gt;</span>
    &lt;<span class="hljs-class"><span class="hljs-keyword">module</span>&gt;<span class="hljs-title">sql</span>/<span class="hljs-title">core</span><span class="hljs-inheritance">&lt;</span>/<span class="hljs-title">module</span>&gt;</span>
    &lt;!--
    &lt;<span class="hljs-class"><span class="hljs-keyword">module</span>&gt;<span class="hljs-title">sql</span>/<span class="hljs-title">hive</span><span class="hljs-inheritance">&lt;</span>/<span class="hljs-title">module</span>&gt;</span>
    --&gt;
    &lt;<span class="hljs-class"><span class="hljs-keyword">module</span>&gt;<span class="hljs-title">unsafe</span><span class="hljs-inheritance">&lt;</span>/<span class="hljs-title">module</span>&gt;</span>
    &lt;<span class="hljs-class"><span class="hljs-keyword">module</span>&gt;<span class="hljs-title">assembly</span><span class="hljs-inheritance">&lt;</span>/<span class="hljs-title">module</span>&gt;</span>
    &lt;!--
    &lt;<span class="hljs-class"><span class="hljs-keyword">module</span>&gt;<span class="hljs-title">external</span>/<span class="hljs-title">twitter</span><span class="hljs-inheritance">&lt;</span>/<span class="hljs-title">module</span>&gt;</span>
    &lt;<span class="hljs-class"><span class="hljs-keyword">module</span>&gt;<span class="hljs-title">external</span>/<span class="hljs-title">flume</span><span class="hljs-inheritance">&lt;</span>/<span class="hljs-title">module</span>&gt;</span>
    &lt;<span class="hljs-class"><span class="hljs-keyword">module</span>&gt;<span class="hljs-title">external</span>/<span class="hljs-title">flume</span>-<span class="hljs-title">sink</span><span class="hljs-inheritance">&lt;</span>/<span class="hljs-title">module</span>&gt;</span>
    &lt;<span class="hljs-class"><span class="hljs-keyword">module</span>&gt;<span class="hljs-title">external</span>/<span class="hljs-title">mqtt</span><span class="hljs-inheritance">&lt;</span>/<span class="hljs-title">module</span>&gt;</span>
    &lt;<span class="hljs-class"><span class="hljs-keyword">module</span>&gt;<span class="hljs-title">external</span>/<span class="hljs-title">zeromq</span><span class="hljs-inheritance">&lt;</span>/<span class="hljs-title">module</span>&gt;</span>
    &lt;<span class="hljs-class"><span class="hljs-keyword">module</span>&gt;<span class="hljs-title">examples</span><span class="hljs-inheritance">&lt;</span>/<span class="hljs-title">module</span>&gt;</span>
    --&gt;
    &lt;<span class="hljs-class"><span class="hljs-keyword">module</span>&gt;<span class="hljs-title">repl</span><span class="hljs-inheritance">&lt;</span>/<span class="hljs-title">module</span>&gt;</span>
    &lt;<span class="hljs-class"><span class="hljs-keyword">module</span>&gt;<span class="hljs-title">launcher</span><span class="hljs-inheritance">&lt;</span>/<span class="hljs-title">module</span>&gt;</span>
  &lt;<span class="hljs-regexp">/modules&gt;</span></code></pre>



<pre class="prettyprint"><code class=" hljs lasso">mvn <span class="hljs-attribute">-Pyarn</span> <span class="hljs-attribute">-Phadoop</span><span class="hljs-subst">-</span><span class="hljs-number">2.4</span> <span class="hljs-attribute">-Dhadoop</span><span class="hljs-built_in">.</span>version<span class="hljs-subst">=</span><span class="hljs-number">2.6</span><span class="hljs-number">.0</span> <span class="hljs-attribute">-DskipTests</span> clean package</code></pre>

<p>使用新编译的assembly/target/scala-2.10/spark-assembly-1.4.0-SNAPSHOT-hadoop2.6.0.jar 文件安装Spark</p>

<p>一切就绪后启动各个组件</p>



<pre class="prettyprint"><code class=" hljs sql"><span class="hljs-operator"><span class="hljs-keyword">start</span>-dfs.sh
<span class="hljs-keyword">start</span>-yarn.sh 
{SPARK_HOME}/sbin/<span class="hljs-keyword">start</span>-<span class="hljs-keyword">all</span>.sh</span></code></pre>

<p>进入hive后操作如下： <br>
<img src="https://img-blog.csdn.net/20150603225344938" alt="这里写图片描述" title=""></p>

<p>第一次启动时在Spark上启动application，所以第一条SQL比较慢，退出hive，spark application结束 <br>
<img src="https://img-blog.csdn.net/20150603230800623" alt="这里写图片描述" title=""></p>

<hr>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>