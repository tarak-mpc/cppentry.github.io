---
layout:     post
title:      pyspark系列--连接pyspark
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p><strong>目录</strong> <br>
<!-- TOC --></p>

<ul>
<li><a href="#1-%E8%BF%9E%E6%8E%A5spark" rel="nofollow">1. 连接spark</a> <br>
<ul><li><a href="#11-%E7%AE%80%E5%8D%95%E8%BF%9E%E6%8E%A5spark" rel="nofollow">1.1. 简单连接spark</a></li>
<li><a href="#12-%E8%BF%9E%E6%8E%A5spark%E9%9B%86%E7%BE%A4" rel="nofollow">1.2. 连接spark集群</a></li>
<li><a href="#13-%E9%9B%86%E7%BE%A4python%E7%8E%AF%E5%A2%83" rel="nofollow">1.3. 集群python环境</a></li>
<li><a href="#14-config%E5%8F%82%E6%95%B0" rel="nofollow">1.4. config参数</a></li></ul></li>
<li><a href="#2-%E6%8F%90%E4%BA%A4%E4%BD%9C%E4%B8%9A" rel="nofollow">2. 提交作业</a></li>
</ul>

<!-- /TOC -->



<h1 id="1-连接spark">1. 连接spark</h1>



<h2 id="11-简单连接spark">1.1. 简单连接spark</h2>



<pre class="prettyprint"><code class="language-python hljs "><span class="hljs-keyword">from</span> pyspark.sql <span class="hljs-keyword">import</span> SparkSession

spark=SparkSession \
        .builder \
        .appName(<span class="hljs-string">'my_first_app_name'</span>) \
        .getOrCreate()</code></pre>



<h2 id="12-连接spark集群">1.2. 连接spark集群</h2>



<pre class="prettyprint"><code class="language-python hljs "><span class="hljs-comment"># 使支持hive</span>
spark = SparkSession \
        .builder \
        .enableHiveSupport() \      
        .master(<span class="hljs-string">"xxx.xxx.xxx.xxx:7077"</span>) \
        .appName(<span class="hljs-string">"my_first_app_name"</span>) \
        .getOrCreate()</code></pre>

<h2 id="13-集群python环境">1.3. 集群python环境</h2>

<p>如果需要在集群中使用指定的python版本（系统默认是2.6），如python3.5，那么就需要在每个节点都安装python3.5，而且将python3.5的目录增加到spark的环境变量中。</p>

<p>或者在python程序中指定也行。</p>



<pre class="prettyprint"><code class="language-python hljs "><span class="hljs-keyword">import</span> os
os.environ[<span class="hljs-string">'SPARK_HOME'</span>] = <span class="hljs-string">'/usr/local/workspace/spark-2.1.0-bin-hadoop2.7'</span>
os.environ[<span class="hljs-string">'PYSPARK_PYTHON'</span>] = <span class="hljs-string">'/usr/local/bin/python3.5'</span>
os.environ[<span class="hljs-string">'PYSPARK_DRIVER_PYTHON'</span>]=<span class="hljs-string">'python3'</span>

<span class="hljs-keyword">from</span> pyspark.sql <span class="hljs-keyword">import</span> SparkSession
spark = SparkSession\
        .builder\
        .enableHiveSupport()\
        .master(<span class="hljs-string">"xxx.xxx.xxx.xxx:7077"</span>)\
        .appName(<span class="hljs-string">"my_first_app_name"</span>)\
        .getOrCreate()</code></pre>

<h2 id="14-config参数">1.4. config参数</h2>



<pre class="prettyprint"><code class="language-python hljs "><span class="hljs-comment"># 在连接spark的时候，还可以添加其他参数，用config</span>
<span class="hljs-keyword">from</span> pyspark.sql <span class="hljs-keyword">import</span> SparkSession
spark = SparkSession\
        .builder\
        .enableHiveSupport()\
        .master(<span class="hljs-string">"xxx.xxx.xxx.xxx:7077"</span>)\
        .appName(<span class="hljs-string">"my_first_app_name"</span>)\
        .config(<span class="hljs-string">'spark.some.config.option'</span>,<span class="hljs-string">'value'</span>) \
        .config(<span class="hljs-string">'spark.some.config.option'</span>,<span class="hljs-string">'value'</span>) \
        ...
        .getOrCreate()</code></pre>

<h1 id="2-提交作业">2. 提交作业</h1>

<p>提交作业可以通过上面的方法，先连接spark，然后使用spark session做各种操作。另一种就是通过submit方式将.py文件提交到spark集群。</p>

<p>特别的，如果使用python虚拟环境，可以通过 <br>
<code>PYSPARK_DRIVER_PYTHON</code>，<code>PYSPARK_PYTHON</code> 指定。</p>

<pre class="prettyprint"><code class="language-shell hljs haml"># 提交spark作业
PYSPARK_DRIVER_PYTHON=/opt/anaconda3/envs/xxljob/bin/python \
PYSPARK_PYTHON=/opt/anaconda3/envs/xxljob/bin/python \
<span class="hljs-comment">
/usr/local/workspace/spark-2.1.0-bin-hadoop2.7/bin/spark-submit \</span>
        -<span class="ruby">-master yarn \    <span class="hljs-comment">#也可以是  yarn-client，yarn-cluster</span>
</span>        -<span class="ruby">-queue ai \
</span>        -<span class="ruby">-num-executors <span class="hljs-number">12</span> \
</span>        -<span class="ruby">-driver-memory <span class="hljs-number">30</span>g \
</span>        -<span class="ruby">-executor-cores <span class="hljs-number">4</span> \
</span>        -<span class="ruby">-executor-memory <span class="hljs-number">32</span>G 
</span><span class="hljs-comment">/tmp/test_spark.py</span></code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>