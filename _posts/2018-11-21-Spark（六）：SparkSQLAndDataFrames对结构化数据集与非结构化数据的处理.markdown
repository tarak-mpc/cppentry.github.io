---
layout:     post
title:      Spark（六）：SparkSQLAndDataFrames对结构化数据集与非结构化数据的处理
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p><a href="https://my.oschina.net/gently?tab=newest&amp;catalogId=3583988" rel="nofollow">Spark（六）：SparkSQLAndDataFrames对结构化数据集与非结构化数据的处理</a></p>
<p>如上转载的这篇文章写得不错！！！</p>
<hr>
<h3><a id="SparkSQL_7"></a>一：简单了解SparkSQL。</h3>
<p>Spark SQL 是结构化的数据处理一个Spark模块。与基本的Spark RDD API不同，Spark SQL 所提供的接口为Spark 提供有关数据和正在执行的计算的结构的详细信息。Spark SQL内部使用这些额外的信息来执行额外的优化。有几种方法与Spark SQL 包括 SQL、 DataFrames API 和数据集 API 进行交互。计算结果相同的执行引擎在使用时，独立的 API/语言使用的表达计算。这种统一意味着开发人员很容易可以提供最自然的方式来表达一个给定的转换基于各种 Api 之间来回切换。</p>
<p>Spark SQL是Spark中的一个模块，主要用于进行结构化数据的处理。它提供的最核心的编程抽象，就是DataFrame。同时Spark SQL还可以作为分布式的SQL查询引擎。Spark SQL最重要的功能之一，就是从Hive中查询数据。</p>
<h3><a id="DataFrame_14"></a>二：简单了解DataFrame。</h3>
<p>DataFrame是一个以命名列方式组织的分布式数据集，等同于关系型数据库中的一个表，也相当于R/Python中的data frames（但是进行了更多的优化）。DataFrame可以通过很多来源进行构建，包括：结构化的数据文件，Hive中的表，外部的关系型数据库，以及RDD。</p>
<p>接下来是对 结构化数据集 与 非结构化数据集 的操作。</p>
<h3><a id="_JSONDataFrame_20"></a>三：结构化数据集： 如何把JSON文件转化为DataFrame</h3>
<h5><a id="31HDFSJSON_22"></a>3.1.在HDFS上放置了两个JSON文件，即</h5>
<p>people.json, 文件内容如下：</p>
<pre><code class="prism language-json"><span class="token punctuation">{</span><span class="token string">"id"</span><span class="token punctuation">:</span> <span class="token string">"19"</span><span class="token punctuation">,</span><span class="token string">"name"</span><span class="token punctuation">:</span> <span class="token string">"berg"</span><span class="token punctuation">,</span><span class="token string">"sex"</span><span class="token punctuation">:</span> <span class="token string">"male"</span><span class="token punctuation">,</span><span class="token string">"age"</span><span class="token punctuation">:</span> <span class="token number">19</span><span class="token punctuation">}</span>
<span class="token punctuation">{</span><span class="token string">"id"</span><span class="token punctuation">:</span> <span class="token string">"20"</span><span class="token punctuation">,</span><span class="token string">"name"</span><span class="token punctuation">:</span> <span class="token string">"cccc"</span><span class="token punctuation">,</span><span class="token string">"sex"</span><span class="token punctuation">:</span> <span class="token string">"female"</span><span class="token punctuation">,</span><span class="token string">"age"</span><span class="token punctuation">:</span> <span class="token number">20</span><span class="token punctuation">}</span>
<span class="token punctuation">{</span><span class="token string">"id"</span><span class="token punctuation">:</span> <span class="token string">"21"</span><span class="token punctuation">,</span><span class="token string">"name"</span><span class="token punctuation">:</span> <span class="token string">"xxxx"</span><span class="token punctuation">,</span><span class="token string">"sex"</span><span class="token punctuation">:</span> <span class="token string">"male"</span><span class="token punctuation">,</span><span class="token string">"age"</span><span class="token punctuation">:</span> <span class="token number">21</span><span class="token punctuation">}</span>
<span class="token punctuation">{</span><span class="token string">"id"</span><span class="token punctuation">:</span> <span class="token string">"22"</span><span class="token punctuation">,</span><span class="token string">"name"</span><span class="token punctuation">:</span> <span class="token string">"jjjj"</span><span class="token punctuation">,</span><span class="token string">"sex"</span><span class="token punctuation">:</span> <span class="token string">"female"</span><span class="token punctuation">,</span><span class="token string">"age"</span><span class="token punctuation">:</span> <span class="token number">21</span><span class="token punctuation">}</span>
</code></pre>
<p>student.json，文件内容如下：</p>
<pre><code class="prism language-json"><span class="token punctuation">{</span><span class="token string">"id"</span><span class="token punctuation">:</span> <span class="token string">"1"</span><span class="token punctuation">,</span><span class="token string">"name"</span><span class="token punctuation">:</span> <span class="token string">"china"</span><span class="token punctuation">,</span><span class="token string">"sex"</span><span class="token punctuation">:</span> <span class="token string">"female"</span><span class="token punctuation">,</span><span class="token string">"age"</span><span class="token punctuation">:</span> <span class="token number">100</span><span class="token punctuation">}</span>
<span class="token punctuation">{</span><span class="token string">"id"</span><span class="token punctuation">:</span> <span class="token string">"19"</span><span class="token punctuation">,</span><span class="token string">"name"</span><span class="token punctuation">:</span> <span class="token string">"xujun"</span><span class="token punctuation">,</span><span class="token string">"sex"</span><span class="token punctuation">:</span> <span class="token string">"male"</span><span class="token punctuation">,</span><span class="token string">"age"</span><span class="token punctuation">:</span> <span class="token number">22</span><span class="token punctuation">}</span>
</code></pre>
<h5><a id="32_DataFrameAPIDataFrame_36"></a>3.2 通过DataFrame的API来操作数据,熟悉下DataFrame中方法的使用：</h5>
<pre><code class="prism language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">SparkSqlDemo</span> <span class="token punctuation">{</span>

	<span class="token keyword">private</span> <span class="token keyword">static</span> String appName <span class="token operator">=</span> <span class="token string">"Test Spark RDD"</span><span class="token punctuation">;</span>
	<span class="token keyword">private</span> <span class="token keyword">static</span> String master <span class="token operator">=</span> <span class="token string">"local"</span><span class="token punctuation">;</span>

	<span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">main</span><span class="token punctuation">(</span>String<span class="token punctuation">[</span><span class="token punctuation">]</span> args<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		SparkConf conf <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">SparkConf</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		conf<span class="token punctuation">.</span><span class="token function">set</span><span class="token punctuation">(</span><span class="token string">"spark.testing.memory"</span><span class="token punctuation">,</span> <span class="token string">"269522560000"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		JavaSparkContext sc <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">JavaSparkContext</span><span class="token punctuation">(</span>master<span class="token punctuation">,</span> appName<span class="token punctuation">,</span> conf<span class="token punctuation">)</span><span class="token punctuation">;</span>

		<span class="token comment">//创建了 sqlContext的上下文，注意，它是DataFrame的起点。</span>
		SQLContext sqlContext <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">SQLContext</span><span class="token punctuation">(</span>sc<span class="token punctuation">)</span><span class="token punctuation">;</span>

		<span class="token comment">//本地的JSON文件转化为DataFrame</span>
		DataFrame df <span class="token operator">=</span> sqlContext<span class="token punctuation">.</span><span class="token function">read</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">json</span><span class="token punctuation">(</span><span class="token string">"hdfs://192.168.226.129:9000/txt/sparkshell/people.json"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

		<span class="token comment">//输出表结构</span>
		df<span class="token punctuation">.</span><span class="token function">printSchema</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

		<span class="token comment">//显示DataFrame的内容。</span>
		df<span class="token punctuation">.</span><span class="token function">show</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

		<span class="token comment">//选择name</span>
		df<span class="token punctuation">.</span><span class="token function">select</span><span class="token punctuation">(</span>df<span class="token punctuation">.</span><span class="token function">col</span><span class="token punctuation">(</span><span class="token string">"name"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">show</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

		<span class="token comment">// 选择所有年龄大于21岁的人，只保留name字段</span>
		df<span class="token punctuation">.</span><span class="token function">filter</span><span class="token punctuation">(</span>df<span class="token punctuation">.</span><span class="token function">col</span><span class="token punctuation">(</span><span class="token string">"age"</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">lt</span><span class="token punctuation">(</span><span class="token number">21</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">select</span><span class="token punctuation">(</span><span class="token string">"name"</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">show</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

		<span class="token comment">// 选择name，并把age字段自增 1</span>
		df<span class="token punctuation">.</span><span class="token function">select</span><span class="token punctuation">(</span>df<span class="token punctuation">.</span><span class="token function">col</span><span class="token punctuation">(</span><span class="token string">"name"</span><span class="token punctuation">)</span><span class="token punctuation">,</span> df<span class="token punctuation">.</span><span class="token function">col</span><span class="token punctuation">(</span><span class="token string">"age"</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">plus</span><span class="token punctuation">(</span><span class="token number">1</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">show</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

		<span class="token comment">//按年龄分组计数：</span>
		df<span class="token punctuation">.</span><span class="token function">groupBy</span><span class="token punctuation">(</span><span class="token string">"age"</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">count</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">show</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>  <span class="token comment">// 应该有一条数据记录为  2 </span>

		<span class="token comment">//把另个JSON文件转化为DataFrame</span>
		DataFrame df2 <span class="token operator">=</span> sqlContext<span class="token punctuation">.</span><span class="token function">read</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">json</span><span class="token punctuation">(</span><span class="token string">"hdfs://192.168.226.129:9000/txt/sparkshell/student.json"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

		df2<span class="token punctuation">.</span><span class="token function">show</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		
		<span class="token comment">//表的关联。</span>
		df<span class="token punctuation">.</span><span class="token function">join</span><span class="token punctuation">(</span>df2<span class="token punctuation">,</span>df<span class="token punctuation">.</span><span class="token function">col</span><span class="token punctuation">(</span><span class="token string">"id"</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">equalTo</span><span class="token punctuation">(</span>df2<span class="token punctuation">.</span><span class="token function">col</span><span class="token punctuation">(</span><span class="token string">"id"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">show</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		
		
		<span class="token comment">//以编程方式运行SQL：</span>
		<span class="token comment">//把DataFrame对象转化为一个虚拟的表</span>
		df<span class="token punctuation">.</span><span class="token function">registerTempTable</span><span class="token punctuation">(</span><span class="token string">"people"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		sqlContext<span class="token punctuation">.</span><span class="token function">sql</span><span class="token punctuation">(</span><span class="token string">"select age,count(*) from people group by age"</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">show</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

		System<span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span>  <span class="token string">"-------------"</span> <span class="token punctuation">)</span><span class="token punctuation">;</span>
		sqlContext<span class="token punctuation">.</span><span class="token function">sql</span><span class="token punctuation">(</span><span class="token string">"select * from people"</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">show</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

	<span class="token punctuation">}</span>
<span class="token punctuation">}</span>
</code></pre>
<h5><a id="33__SQL_sql_93"></a>3.3 以编程方式运行 SQL 查询并返回作为综合结果，通过注册表，操作sql的方式来操作数据：</h5>
<pre><code class="prism language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">SparkSqlDemo1</span> <span class="token punctuation">{</span>

	<span class="token keyword">private</span> <span class="token keyword">static</span> String appName <span class="token operator">=</span> <span class="token string">"Test Spark RDD"</span><span class="token punctuation">;</span>
	<span class="token keyword">private</span> <span class="token keyword">static</span> String master <span class="token operator">=</span> <span class="token string">"local"</span><span class="token punctuation">;</span>

	<span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">main</span><span class="token punctuation">(</span>String<span class="token punctuation">[</span><span class="token punctuation">]</span> args<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		SparkConf conf <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">SparkConf</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		conf<span class="token punctuation">.</span><span class="token function">set</span><span class="token punctuation">(</span><span class="token string">"spark.testing.memory"</span><span class="token punctuation">,</span> <span class="token string">"269522560000"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		JavaSparkContext sc <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">JavaSparkContext</span><span class="token punctuation">(</span>master<span class="token punctuation">,</span> appName<span class="token punctuation">,</span> conf<span class="token punctuation">)</span><span class="token punctuation">;</span>

		<span class="token comment">//创建了 sqlContext的上下文，注意，它是DataFrame的起点。</span>
		SQLContext sqlContext <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">SQLContext</span><span class="token punctuation">(</span>sc<span class="token punctuation">)</span><span class="token punctuation">;</span>

		<span class="token comment">//本地的JSON文件转化为DataFrame</span>
		DataFrame df <span class="token operator">=</span> sqlContext<span class="token punctuation">.</span><span class="token function">read</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">json</span><span class="token punctuation">(</span><span class="token string">"hdfs://192.168.226.129:9000/txt/sparkshell/people.json"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

		<span class="token comment">//把另一个JSON文件转化为DataFrame</span>
		DataFrame df2 <span class="token operator">=</span> sqlContext<span class="token punctuation">.</span><span class="token function">read</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">json</span><span class="token punctuation">(</span><span class="token string">"hdfs://192.168.226.129:9000/txt/sparkshell/student.json"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>		

		<span class="token comment">//以编程方式运行SQL：</span>
		<span class="token comment">//把DataFrame对象转化为一个虚拟的表</span>
		df<span class="token punctuation">.</span><span class="token function">registerTempTable</span><span class="token punctuation">(</span><span class="token string">"people"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		df2<span class="token punctuation">.</span><span class="token function">registerTempTable</span><span class="token punctuation">(</span><span class="token string">"student"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

		<span class="token comment">// 查询虚拟表 people 中所有数据</span>
		sqlContext<span class="token punctuation">.</span><span class="token function">sql</span><span class="token punctuation">(</span><span class="token string">"select * from people"</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">show</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

		<span class="token comment">//查看某个字段  </span>
		sqlContext<span class="token punctuation">.</span><span class="token function">sql</span><span class="token punctuation">(</span><span class="token string">"select name from people "</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">show</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

		<span class="token comment">//查看多个字段  </span>
		sqlContext<span class="token punctuation">.</span><span class="token function">sql</span><span class="token punctuation">(</span><span class="token string">"select name,age+1 from people "</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">show</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>  

		<span class="token comment">//过滤某个字段的值  </span>
		sqlContext<span class="token punctuation">.</span><span class="token function">sql</span><span class="token punctuation">(</span><span class="token string">"select name, age from people where age&gt;=21"</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">show</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

		<span class="token comment">//count group 某个字段的值  </span>
		sqlContext<span class="token punctuation">.</span><span class="token function">sql</span><span class="token punctuation">(</span><span class="token string">"select age,count(*) countage from people group by age"</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">show</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>


		<span class="token comment">//关联： 内联 。 </span>
		sqlContext<span class="token punctuation">.</span><span class="token function">sql</span><span class="token punctuation">(</span><span class="token string">"select * from people inner join student on people.id = student.id "</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">show</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token comment">/*
	    +---+---+----+----+---+---+-----+----+
		|age| id|name| sex|age| id| name| sex|
		+---+---+----+----+---+---+-----+----+
		| 19| 19|berg|male| 22| 19|xujun|male|
		+---+---+----+----+---+---+-----+----+ 
		 */</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span>
</code></pre>
<h3><a id="_148"></a>四：非结构化数据集：</h3>
<pre><code>第一种方法使用反射来推断架构 RDD 包含特定类型的对象。
这种基于反射方法导致更简洁的代码和工程好当您已经知道该Schema编写Spark应用程序时。

创建 DataFrames 的第二个方法是通过允许您构建一个Schema，然后将它应用于现有 RDD 的编程接口。
虽然这种方法更为详细，它允许您构建 DataFrames 时直到运行时才知道的列和它们的类型。
</code></pre>
<h5><a id="41__usertxt_156"></a>4.1  非结构化的数据集文件，user.txt，内容如下：</h5>
<pre><code class="prism language-txt">1,"Hadoop",20
2,"HBase", 21
3,"Zookeeper",22
4,"Hive",23
5,"Spark",24
6,"Berg",22
7,"Xujun",23
</code></pre>
<h5><a id="42___class_166"></a>4.2  通过 class反射来注册一张表。</h5>
<pre><code>    Spark SQL 支持 JavaBeans RDD 自动转换分布式数据集。BeanInfo，使用反射来获取定义表的架构。目前，Spark SQL 不支持包含嵌套的 JavaBeans 或包含复杂的类型，例如列表或数组。您可以通过创建一个类，实现可序列化并有 getter 和 setter 方法的所有其字段创建 JavaBean。
</code></pre>
<pre><code class="prism language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">SparkSqlDemo2</span> <span class="token punctuation">{</span>

	<span class="token keyword">private</span> <span class="token keyword">static</span> String appName <span class="token operator">=</span> <span class="token string">"Test Spark RDD"</span><span class="token punctuation">;</span>
	<span class="token keyword">private</span> <span class="token keyword">static</span> String master <span class="token operator">=</span> <span class="token string">"local"</span><span class="token punctuation">;</span>

	<span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">main</span><span class="token punctuation">(</span>String<span class="token punctuation">[</span><span class="token punctuation">]</span> args<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		SparkConf conf <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">SparkConf</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		conf<span class="token punctuation">.</span><span class="token function">set</span><span class="token punctuation">(</span><span class="token string">"spark.testing.memory"</span><span class="token punctuation">,</span> <span class="token string">"269522560000"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		JavaSparkContext sc <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">JavaSparkContext</span><span class="token punctuation">(</span>master<span class="token punctuation">,</span> appName<span class="token punctuation">,</span> conf<span class="token punctuation">)</span><span class="token punctuation">;</span>

		<span class="token comment">//创建了 sqlContext的上下文，注意，它是DataFrame的起点。</span>
		SQLContext sqlContext <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">SQLContext</span><span class="token punctuation">(</span>sc<span class="token punctuation">)</span><span class="token punctuation">;</span>

		<span class="token comment">//把加载的文本文件 并 每行转换 JavaBean</span>
		JavaRDD<span class="token generics function"><span class="token punctuation">&lt;</span>String<span class="token punctuation">&gt;</span></span> rdd <span class="token operator">=</span> sc<span class="token punctuation">.</span><span class="token function">textFile</span><span class="token punctuation">(</span><span class="token string">"hdfs://192.168.226.129:9000/txt/sparkshell/user.txt"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

		JavaRDD<span class="token generics function"><span class="token punctuation">&lt;</span>User<span class="token punctuation">&gt;</span></span> userRDD <span class="token operator">=</span> rdd<span class="token punctuation">.</span><span class="token function">map</span><span class="token punctuation">(</span> <span class="token keyword">new</span> <span class="token class-name">Function</span><span class="token generics function"><span class="token punctuation">&lt;</span>String<span class="token punctuation">,</span> User<span class="token punctuation">&gt;</span></span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>

			<span class="token keyword">private</span> <span class="token keyword">static</span> <span class="token keyword">final</span> <span class="token keyword">long</span> serialVersionUID <span class="token operator">=</span> <span class="token number">1</span>L<span class="token punctuation">;</span>

			<span class="token keyword">public</span> User <span class="token function">call</span><span class="token punctuation">(</span>String line<span class="token punctuation">)</span> <span class="token keyword">throws</span> Exception <span class="token punctuation">{</span>
				String<span class="token punctuation">[</span><span class="token punctuation">]</span> parts <span class="token operator">=</span> line<span class="token punctuation">.</span><span class="token function">split</span><span class="token punctuation">(</span><span class="token string">","</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
				User user <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">User</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
				user<span class="token punctuation">.</span><span class="token function">setId</span><span class="token punctuation">(</span>Integer<span class="token punctuation">.</span><span class="token function">parseInt</span><span class="token punctuation">(</span>parts<span class="token punctuation">[</span><span class="token number">0</span><span class="token punctuation">]</span><span class="token punctuation">.</span><span class="token function">trim</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
				user<span class="token punctuation">.</span><span class="token function">setName</span><span class="token punctuation">(</span>parts<span class="token punctuation">[</span><span class="token number">1</span><span class="token punctuation">]</span><span class="token punctuation">.</span><span class="token function">trim</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
				user<span class="token punctuation">.</span><span class="token function">setAge</span><span class="token punctuation">(</span>Integer<span class="token punctuation">.</span><span class="token function">parseInt</span><span class="token punctuation">(</span>parts<span class="token punctuation">[</span><span class="token number">2</span><span class="token punctuation">]</span><span class="token punctuation">.</span><span class="token function">trim</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
				<span class="token keyword">return</span> user<span class="token punctuation">;</span>
			<span class="token punctuation">}</span>
		<span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

		<span class="token comment">// collect 属于行动算子Action 提交作业并触发运算。</span>
		List<span class="token generics function"><span class="token punctuation">&lt;</span>User<span class="token punctuation">&gt;</span></span> list <span class="token operator">=</span> userRDD<span class="token punctuation">.</span><span class="token function">collect</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token keyword">for</span> <span class="token punctuation">(</span>User user <span class="token operator">:</span> list<span class="token punctuation">)</span> <span class="token punctuation">{</span>
			System<span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span>  user <span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token punctuation">}</span>

		<span class="token comment">//通过 class 反射注册一张表</span>
		DataFrame df <span class="token operator">=</span> sqlContext<span class="token punctuation">.</span><span class="token function">createDataFrame</span><span class="token punctuation">(</span>userRDD<span class="token punctuation">,</span> User<span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		df<span class="token punctuation">.</span><span class="token function">registerTempTable</span><span class="token punctuation">(</span><span class="token string">"user"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

		DataFrame df1 <span class="token operator">=</span> sqlContext<span class="token punctuation">.</span><span class="token function">sql</span><span class="token punctuation">(</span><span class="token string">"SELECT id,name,age FROM user WHERE age &gt;= 21 AND age &lt;= 23"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

		<span class="token comment">// 通过sql 查询的结果是 DataFrame 即df1 它还是支持 RDD的所有正常操作。</span>
		df1<span class="token punctuation">.</span><span class="token function">show</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		
		<span class="token comment">//并且 结果中的行列可以按序号访问。</span>
		List<span class="token generics function"><span class="token punctuation">&lt;</span>String<span class="token punctuation">&gt;</span></span> listString <span class="token operator">=</span> df1<span class="token punctuation">.</span><span class="token function">javaRDD</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">map</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">Function</span><span class="token generics function"><span class="token punctuation">&lt;</span>Row<span class="token punctuation">,</span> String<span class="token punctuation">&gt;</span></span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
			
			<span class="token keyword">private</span> <span class="token keyword">static</span> <span class="token keyword">final</span> <span class="token keyword">long</span> serialVersionUID <span class="token operator">=</span> <span class="token number">1</span>L<span class="token punctuation">;</span>

			<span class="token keyword">public</span> String <span class="token function">call</span><span class="token punctuation">(</span>Row row<span class="token punctuation">)</span> <span class="token punctuation">{</span>
				<span class="token keyword">return</span> <span class="token string">"Id: "</span> <span class="token operator">+</span> row<span class="token punctuation">.</span><span class="token function">getInt</span><span class="token punctuation">(</span><span class="token number">0</span><span class="token punctuation">)</span> <span class="token operator">+</span> <span class="token string">", Name: "</span><span class="token operator">+</span>row<span class="token punctuation">.</span><span class="token function">getString</span><span class="token punctuation">(</span><span class="token number">1</span><span class="token punctuation">)</span> <span class="token operator">+</span> <span class="token string">", Age: "</span> <span class="token operator">+</span> row<span class="token punctuation">.</span><span class="token function">getInt</span><span class="token punctuation">(</span><span class="token number">2</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
			<span class="token punctuation">}</span>
		<span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">collect</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		
		<span class="token keyword">for</span> <span class="token punctuation">(</span>String string <span class="token operator">:</span> listString<span class="token punctuation">)</span> <span class="token punctuation">{</span>
			System<span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span>  string <span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token punctuation">}</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span>
</code></pre>
<h5><a id="43____schema__232"></a>4.3   以编程方式指定 schema， 通过字段反射来映射注册临时表</h5>
<pre><code>在某些情况下不能提前定义 JavaBean 类 （例如，记录的结构编码的字符串，或将解析文本数据集和领域预计将以不同的方式为不同的用户），
三个步骤，可以以编程方式创建分布式数据集。

1. 从原始 RDD; 创建行 RDD

2. 创建由 StructType 中 RDD 在步骤 1 中创建的行结构相匹配的schema。

3.适用于行 RDD 通过 createDataFrame 方法由 SQLContext 提供的schema。
</code></pre>
<pre><code class="prism language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">SparkSqlDemo3</span> <span class="token punctuation">{</span>

	<span class="token keyword">private</span> <span class="token keyword">static</span> String appName <span class="token operator">=</span> <span class="token string">"Test Spark RDD"</span><span class="token punctuation">;</span>
	<span class="token keyword">private</span> <span class="token keyword">static</span> String master <span class="token operator">=</span> <span class="token string">"local"</span><span class="token punctuation">;</span>

	<span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">main</span><span class="token punctuation">(</span>String<span class="token punctuation">[</span><span class="token punctuation">]</span> args<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		SparkConf conf <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">SparkConf</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		conf<span class="token punctuation">.</span><span class="token function">set</span><span class="token punctuation">(</span><span class="token string">"spark.testing.memory"</span><span class="token punctuation">,</span> <span class="token string">"269522560000"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		JavaSparkContext sc <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">JavaSparkContext</span><span class="token punctuation">(</span>master<span class="token punctuation">,</span> appName<span class="token punctuation">,</span> conf<span class="token punctuation">)</span><span class="token punctuation">;</span>

		<span class="token comment">//创建了 sqlContext的上下文，注意，它是DataFrame的起点。</span>
		SQLContext sqlContext <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">SQLContext</span><span class="token punctuation">(</span>sc<span class="token punctuation">)</span><span class="token punctuation">;</span>

		<span class="token comment">//把加载的文本文件 并 每行转换 JavaBean</span>
		JavaRDD<span class="token generics function"><span class="token punctuation">&lt;</span>String<span class="token punctuation">&gt;</span></span> rdd <span class="token operator">=</span> sc<span class="token punctuation">.</span><span class="token function">textFile</span><span class="token punctuation">(</span><span class="token string">"hdfs://192.168.226.129:9000/txt/sparkshell/user.txt"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

		<span class="token comment">// schema 以字符串形式编码</span>
		String schemaString <span class="token operator">=</span> <span class="token string">"id name age"</span><span class="token punctuation">;</span>

		<span class="token comment">// 基于 字符串的schema生成 schema。</span>
		List<span class="token generics function"><span class="token punctuation">&lt;</span>StructField<span class="token punctuation">&gt;</span></span> fields <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">ArrayList</span><span class="token generics function"><span class="token punctuation">&lt;</span>StructField<span class="token punctuation">&gt;</span></span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		
		String<span class="token punctuation">[</span><span class="token punctuation">]</span> str <span class="token operator">=</span> schemaString<span class="token punctuation">.</span><span class="token function">split</span><span class="token punctuation">(</span><span class="token string">" "</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		fields<span class="token punctuation">.</span><span class="token function">add</span><span class="token punctuation">(</span>DataTypes<span class="token punctuation">.</span><span class="token function">createStructField</span><span class="token punctuation">(</span>str<span class="token punctuation">[</span><span class="token number">0</span><span class="token punctuation">]</span><span class="token punctuation">,</span> DataTypes<span class="token punctuation">.</span>IntegerType<span class="token punctuation">,</span> <span class="token boolean">true</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		fields<span class="token punctuation">.</span><span class="token function">add</span><span class="token punctuation">(</span>DataTypes<span class="token punctuation">.</span><span class="token function">createStructField</span><span class="token punctuation">(</span>str<span class="token punctuation">[</span><span class="token number">1</span><span class="token punctuation">]</span><span class="token punctuation">,</span> DataTypes<span class="token punctuation">.</span>StringType<span class="token punctuation">,</span> <span class="token boolean">true</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		fields<span class="token punctuation">.</span><span class="token function">add</span><span class="token punctuation">(</span>DataTypes<span class="token punctuation">.</span><span class="token function">createStructField</span><span class="token punctuation">(</span>str<span class="token punctuation">[</span><span class="token number">2</span><span class="token punctuation">]</span><span class="token punctuation">,</span> DataTypes<span class="token punctuation">.</span>IntegerType<span class="token punctuation">,</span> <span class="token boolean">true</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

		StructType schema <span class="token operator">=</span> DataTypes<span class="token punctuation">.</span><span class="token function">createStructType</span><span class="token punctuation">(</span>fields<span class="token punctuation">)</span><span class="token punctuation">;</span>  <span class="token comment">//  id name age</span>

		JavaRDD<span class="token generics function"><span class="token punctuation">&lt;</span>Row<span class="token punctuation">&gt;</span></span> rowRDD <span class="token operator">=</span> rdd<span class="token punctuation">.</span><span class="token function">map</span><span class="token punctuation">(</span> <span class="token keyword">new</span> <span class="token class-name">Function</span><span class="token generics function"><span class="token punctuation">&lt;</span>String<span class="token punctuation">,</span> Row<span class="token punctuation">&gt;</span></span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>

			<span class="token keyword">private</span> <span class="token keyword">static</span> <span class="token keyword">final</span> <span class="token keyword">long</span> serialVersionUID <span class="token operator">=</span> <span class="token number">1</span>L<span class="token punctuation">;</span>
			<span class="token keyword">public</span> Row <span class="token function">call</span><span class="token punctuation">(</span>String record<span class="token punctuation">)</span> <span class="token keyword">throws</span> Exception <span class="token punctuation">{</span>
				String<span class="token punctuation">[</span><span class="token punctuation">]</span> fields <span class="token operator">=</span> record<span class="token punctuation">.</span><span class="token function">split</span><span class="token punctuation">(</span><span class="token string">","</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
				<span class="token keyword">return</span> RowFactory<span class="token punctuation">.</span><span class="token function">create</span><span class="token punctuation">(</span>Integer<span class="token punctuation">.</span><span class="token function">parseInt</span><span class="token punctuation">(</span>fields<span class="token punctuation">[</span><span class="token number">0</span><span class="token punctuation">]</span><span class="token punctuation">.</span><span class="token function">trim</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">,</span> fields<span class="token punctuation">[</span><span class="token number">1</span><span class="token punctuation">]</span><span class="token punctuation">.</span><span class="token function">trim</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">,</span>Integer<span class="token punctuation">.</span><span class="token function">parseInt</span><span class="token punctuation">(</span>fields<span class="token punctuation">[</span><span class="token number">2</span><span class="token punctuation">]</span><span class="token punctuation">.</span><span class="token function">trim</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
			<span class="token punctuation">}</span>
		<span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

		List<span class="token generics function"><span class="token punctuation">&lt;</span>Row<span class="token punctuation">&gt;</span></span> list <span class="token operator">=</span> rowRDD<span class="token punctuation">.</span><span class="token function">collect</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token keyword">for</span> <span class="token punctuation">(</span>Row row <span class="token operator">:</span> list<span class="token punctuation">)</span> <span class="token punctuation">{</span>
			System<span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span>  row<span class="token punctuation">.</span><span class="token function">getInt</span><span class="token punctuation">(</span><span class="token number">0</span><span class="token punctuation">)</span> <span class="token operator">+</span> <span class="token string">"\t"</span><span class="token operator">+</span> row<span class="token punctuation">.</span><span class="token function">getString</span><span class="token punctuation">(</span><span class="token number">1</span><span class="token punctuation">)</span> <span class="token operator">+</span> <span class="token string">"\t"</span><span class="token operator">+</span>row<span class="token punctuation">.</span><span class="token function">getInt</span><span class="token punctuation">(</span><span class="token number">2</span><span class="token punctuation">)</span>  <span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token punctuation">}</span>

		<span class="token comment">//对RDD应用schema 并注册一张表：</span>
		DataFrame df <span class="token operator">=</span> sqlContext<span class="token punctuation">.</span><span class="token function">createDataFrame</span><span class="token punctuation">(</span>rowRDD<span class="token punctuation">,</span> schema<span class="token punctuation">)</span><span class="token punctuation">;</span>
		System<span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span> <span class="token string">"df : "</span> <span class="token operator">+</span> df<span class="token punctuation">)</span><span class="token punctuation">;</span>
		df<span class="token punctuation">.</span><span class="token function">registerTempTable</span><span class="token punctuation">(</span><span class="token string">"user"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

		df<span class="token punctuation">.</span><span class="token function">show</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		DataFrame df2 <span class="token operator">=</span> sqlContext<span class="token punctuation">.</span><span class="token function">sql</span><span class="token punctuation">(</span><span class="token string">"SELECT id,name,age FROM user WHERE age &gt;= 21 AND age &lt;= 23"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

		<span class="token comment">// 通过sql 查询的结果是 DataFrame 即df1 它还是支持 RDD的所有正常操作。</span>
		df2<span class="token punctuation">.</span><span class="token function">show</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token comment">// 并且 结果中的行列可以按序号访问。</span>
		List<span class="token generics function"><span class="token punctuation">&lt;</span>String<span class="token punctuation">&gt;</span></span> listString <span class="token operator">=</span> df2<span class="token punctuation">.</span><span class="token function">javaRDD</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">map</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">Function</span><span class="token generics function"><span class="token punctuation">&lt;</span>Row<span class="token punctuation">,</span> String<span class="token punctuation">&gt;</span></span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>

			<span class="token keyword">private</span> <span class="token keyword">static</span> <span class="token keyword">final</span> <span class="token keyword">long</span> serialVersionUID <span class="token operator">=</span> <span class="token number">1</span>L<span class="token punctuation">;</span>
			<span class="token keyword">public</span> String <span class="token function">call</span><span class="token punctuation">(</span>Row row<span class="token punctuation">)</span> <span class="token punctuation">{</span>
				System<span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span> row <span class="token punctuation">)</span><span class="token punctuation">;</span>
				<span class="token keyword">return</span> <span class="token string">"Id: "</span> <span class="token operator">+</span> row<span class="token punctuation">.</span><span class="token function">getInt</span><span class="token punctuation">(</span><span class="token number">0</span><span class="token punctuation">)</span> <span class="token operator">+</span> <span class="token string">", Name: "</span><span class="token operator">+</span>row<span class="token punctuation">.</span><span class="token function">getString</span><span class="token punctuation">(</span><span class="token number">1</span><span class="token punctuation">)</span> <span class="token operator">+</span> <span class="token string">", Age: "</span> <span class="token operator">+</span> row<span class="token punctuation">.</span><span class="token function">getInt</span><span class="token punctuation">(</span><span class="token number">2</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
			<span class="token punctuation">}</span>
		<span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">collect</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

		<span class="token keyword">for</span> <span class="token punctuation">(</span>String string <span class="token operator">:</span> listString<span class="token punctuation">)</span> <span class="token punctuation">{</span>
			System<span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span>  string <span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token punctuation">}</span>

	<span class="token punctuation">}</span>
<span class="token punctuation">}</span>
</code></pre>
<p>注意如果将上述代码段中的一段，即：</p>
<pre><code class="prism language-java">        String<span class="token punctuation">[</span><span class="token punctuation">]</span> str <span class="token operator">=</span> schemaString<span class="token punctuation">.</span><span class="token function">split</span><span class="token punctuation">(</span><span class="token string">" "</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

        fields<span class="token punctuation">.</span><span class="token function">add</span><span class="token punctuation">(</span>DataTypes<span class="token punctuation">.</span><span class="token function">createStructField</span><span class="token punctuation">(</span>str<span class="token punctuation">[</span><span class="token number">0</span><span class="token punctuation">]</span><span class="token punctuation">,</span> DataTypes<span class="token punctuation">.</span>IntegerType<span class="token punctuation">,</span> <span class="token boolean">true</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

        fields<span class="token punctuation">.</span><span class="token function">add</span><span class="token punctuation">(</span>DataTypes<span class="token punctuation">.</span><span class="token function">createStructField</span><span class="token punctuation">(</span>str<span class="token punctuation">[</span><span class="token number">1</span><span class="token punctuation">]</span><span class="token punctuation">,</span> DataTypes<span class="token punctuation">.</span>StringType<span class="token punctuation">,</span> <span class="token boolean">true</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

        fields<span class="token punctuation">.</span><span class="token function">add</span><span class="token punctuation">(</span>DataTypes<span class="token punctuation">.</span><span class="token function">createStructField</span><span class="token punctuation">(</span>str<span class="token punctuation">[</span><span class="token number">2</span><span class="token punctuation">]</span><span class="token punctuation">,</span> DataTypes<span class="token punctuation">.</span>IntegerType<span class="token punctuation">,</span> <span class="token boolean">true</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
</code></pre>
<p>改为下面这段代码：</p>
<pre><code class="prism language-java">   <span class="token keyword">for</span> <span class="token punctuation">(</span>String fieldName<span class="token operator">:</span> schemaString<span class="token punctuation">.</span><span class="token function">split</span><span class="token punctuation">(</span><span class="token string">" "</span><span class="token punctuation">)</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>


          fields<span class="token punctuation">.</span><span class="token function">add</span><span class="token punctuation">(</span>DataTypes<span class="token punctuation">.</span><span class="token function">createStructField</span><span class="token punctuation">(</span>fieldName<span class="token punctuation">,</span> DataTypes<span class="token punctuation">.</span>StringType<span class="token punctuation">,</span> <span class="token boolean">true</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

      <span class="token punctuation">}</span>
</code></pre>
<p>将会出现以下错误：</p>
<pre><code>        Caused by: scala.MatchError: 1 (of class java.lang.Integer)
</code></pre>
<p>那就来认识下   局部套用和部分应用 ： <a href="http://www.ibm.com/developerworks/cn/java/j-jn9/" rel="nofollow">http://www.ibm.com/developerworks/cn/java/j-jn9/</a></p>
<hr>
<p>转载 <a href="https://my.oschina.net/gently?tab=newest&amp;catalogId=3583988" rel="nofollow">https://my.oschina.net/gently?tab=newest&amp;catalogId=3583988</a></p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>