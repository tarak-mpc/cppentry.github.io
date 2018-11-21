---
layout:     post
title:      Spark DataFrame vector 类型存储到Hive表
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="spark-dataframe-vector-类型存储到hive表">Spark DataFrame vector 类型存储到Hive表</h1>

<p></p><div class="toc"><div class="toc">
<ul>
<li><a href="#spark-dataframe-vector-%E7%B1%BB%E5%9E%8B%E5%AD%98%E5%82%A8%E5%88%B0hive%E8%A1%A8" rel="nofollow">Spark DataFrame vector 类型存储到Hive表</a><ul>
<li><a href="#1-%E8%BD%AF%E4%BB%B6%E7%89%88%E6%9C%AC" rel="nofollow">软件版本</a></li>
<li><a href="#2-%E5%9C%BA%E6%99%AF%E6%8F%8F%E8%BF%B0" rel="nofollow">场景描述</a></li>
<li><a href="#3-%E9%97%AE%E9%A2%98%E7%9A%84%E8%BF%82%E5%9B%9E%E8%A7%A3%E5%86%B3%E6%96%B9%E6%B3%95" rel="nofollow">问题的迂回解决方法</a></li>
<li><a href="#4-%E7%A4%BA%E4%BE%8B" rel="nofollow">示例</a></li>
</ul>
</li>
</ul>
</div>
</div>




<h2 id="1-软件版本">1. 软件版本</h2>

<table>
<thead>
<tr>
  <th>软件</th>
  <th>版本</th>
</tr>
</thead>
<tbody><tr>
  <td>Spark</td>
  <td>1.6.0</td>
</tr>
<tr>
  <td>Hive</td>
  <td>1.2.1</td>
</tr>
</tbody></table>




<h2 id="2-场景描述">2. 场景描述</h2>

<p>在使用Spark时，有时需要存储DataFrame数据到Hive表中，一般的存储方式如下：</p>



<pre class="prettyprint"><code class="language-scala hljs "> <span class="hljs-comment">// 注册临时表</span>
 myDf.registerTempTable(<span class="hljs-string">"t1"</span>)
 <span class="hljs-comment">// 使用SQLContext从临时表创建Hive表</span>
 sqlContext.sql(<span class="hljs-string">"create table h1 as select * from t1"</span>)</code></pre>

<p>在DataFrame中存储一般的数据类型，比如Double、Float、String等到Hive表是没有问题的，但是在DataFrame中还有一个数据类型：<strong>vector</strong> ， 如果存储这种类型到Hive表那么会报错，类似：</p>



<pre class="prettyprint"><code class=" hljs haskell"><span class="hljs-title">org</span>.apache.spark.sql.<span class="hljs-type">AnalysisException</span>: cannot resolve 'cast(norF <span class="hljs-keyword">as</span> struct&lt;<span class="hljs-typedef"><span class="hljs-keyword">type</span>:tinyint,size:int,indices:array&lt;int&gt;,values:array&lt;double&gt;&gt;)' due to <span class="hljs-keyword">data</span> <span class="hljs-keyword">type</span> mismatch: cannot cast org.apache.spark.mllib.linalg.<span class="hljs-type">VectorUDT</span>@f71b0bce to <span class="hljs-type">StructType</span><span class="hljs-container">(<span class="hljs-type">StructField</span>(<span class="hljs-title">type</span>,<span class="hljs-type">ByteType</span>,<span class="hljs-title">true</span>)</span>, <span class="hljs-type">StructField</span><span class="hljs-container">(<span class="hljs-title">size</span>,<span class="hljs-type">IntegerType</span>,<span class="hljs-title">true</span>)</span>, <span class="hljs-type">StructField</span><span class="hljs-container">(<span class="hljs-title">indices</span>,<span class="hljs-type">ArrayType</span>(<span class="hljs-type">IntegerType</span>,<span class="hljs-title">true</span>)</span>,true), <span class="hljs-type">StructField</span><span class="hljs-container">(<span class="hljs-title">values</span>,<span class="hljs-type">ArrayType</span>(<span class="hljs-type">DoubleType</span>,<span class="hljs-title">true</span>)</span>,true));</span></code></pre>

<p>这个错误如果搜索的话，可以找到类似这种结果： <a href="https://issues.apache.org/jira/browse/SPARK-22137" rel="nofollow">Failed to insert VectorUDT to hive table with DataFrameWriter.insertInto(tableName: String)</a></p>

<p>也即是说暂时使用Spark是不能够直接存储vector类型的DataFrame到Hive表的，那么有没有一种方法可以存储呢？ <br>
想到这里，那么在Spark中是有一个工具类<strong>VectorAssembler</strong> 可以达到相反的目的，即把多个列（也需要要求这些列的类型是一致的）合并成一个vector列。但是并没有相反的工具类，也就是我们的需求。</p>



<h2 id="3-问题的迂回解决方法">3. 问题的迂回解决方法</h2>

<p>这里提出一个解决方法如下： <br>
假设： <br>
1. DataFrame中数据类型是vector的列中的数据类型都是已知的，比如Double，数值类型； <br>
2. vector列中的具体子列个数也是已知的； <br>
有了上面两个假设就可以通过构造RDD[Row]以及schema的方式来生成新的DataFrame，并且这个新的DataFrame的类型是基本类型，如Double。这样就可以保存到Hive中了。</p>



<h2 id="4-示例">4. 示例</h2>

<p>本例流程如下：</p>



<div class="flow-chart"><svg height="417" version="1.1" width="626.453125" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" style="overflow: hidden; position: relative;"><desc style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);">Created with Raphaël 2.1.2</desc><defs style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path><marker id="raphael-marker-endblock33-obj1374" markerheight="3" markerwidth="3" orient="auto" refx="1.5" refy="1.5" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"><use xmlns:xlink="http://www.w3.org/1999/xlink" xlink:href="#raphael-marker-block" transform="rotate(180 1.5 1.5) scale(0.6,0.6)" stroke-width="1.6667" fill="black" stroke="none" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></use></marker><marker id="raphael-marker-endblock33-obj1375" markerheight="3" markerwidth="3" orient="auto" refx="1.5" refy="1.5" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"><use xmlns:xlink="http://www.w3.org/1999/xlink" xlink:href="#raphael-marker-block" transform="rotate(180 1.5 1.5) scale(0.6,0.6)" stroke-width="1.6667" fill="black" stroke="none" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></use></marker><marker id="raphael-marker-endblock33-obj1376" markerheight="3" markerwidth="3" orient="auto" refx="1.5" refy="1.5" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"><use xmlns:xlink="http://www.w3.org/1999/xlink" xlink:href="#raphael-marker-block" transform="rotate(180 1.5 1.5) scale(0.6,0.6)" stroke-width="1.6667" fill="black" stroke="none" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></use></marker><marker id="raphael-marker-endblock33-obj1377" markerheight="3" markerwidth="3" orient="auto" refx="1.5" refy="1.5" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"><use xmlns:xlink="http://www.w3.org/1999/xlink" xlink:href="#raphael-marker-block" transform="rotate(180 1.5 1.5) scale(0.6,0.6)" stroke-width="1.6667" fill="black" stroke="none" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></use></marker></defs><rect x="0" y="0" width="208.28125" height="39" rx="20" ry="20" fill="#ffffff" stroke="#000000" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);" stroke-width="2" class="flowchart" id="st" transform="matrix(1,0,0,1,210.0859,4)"></rect><text x="10" y="19.5" text-anchor="start" font-family="sans-serif" font-size="14px" stroke="none" fill="#000000" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0); text-anchor: start; font-family: sans-serif; font-size: 14px; font-weight: normal;" id="stt" class="flowchartt" font-weight="normal" transform="matrix(1,0,0,1,210.0859,4)"><tspan dy="5.5" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);">读取Hive表数据到DataFrame</tspan></text><rect x="0" y="0" width="385.5" height="39" rx="0" ry="0" fill="#ffffff" stroke="#000000" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);" stroke-width="2" class="flowchart" id="op1" transform="matrix(1,0,0,1,121.4766,97)"></rect><text x="10" y="19.5" text-anchor="start" font-family="sans-serif" font-size="14px" stroke="none" fill="#000000" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0); text-anchor: start; font-family: sans-serif; font-size: 14px; font-weight: normal;" id="op1t" class="flowchartt" font-weight="normal" transform="matrix(1,0,0,1,121.4766,97)"><tspan dy="5.5" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);">使用VectorAssembler把DataFrame各列合并到vector类型</tspan></text><rect x="0" y="0" width="431.015625" height="39" rx="0" ry="0" fill="#ffffff" stroke="#000000" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);" stroke-width="2" class="flowchart" id="op2" transform="matrix(1,0,0,1,98.7188,190)"></rect><text x="10" y="19.5" text-anchor="start" font-family="sans-serif" font-size="14px" stroke="none" fill="#000000" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0); text-anchor: start; font-family: sans-serif; font-size: 14px; font-weight: normal;" id="op2t" class="flowchartt" font-weight="normal" transform="matrix(1,0,0,1,98.7188,190)"><tspan dy="5.5" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);">使用合并后vector类型作为输入，传输到Normalizer中做归一化</tspan></text><rect x="0" y="0" width="620.453125" height="39" rx="0" ry="0" fill="#ffffff" stroke="#000000" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);" stroke-width="2" class="flowchart" id="op3" transform="matrix(1,0,0,1,4,283)"></rect><text x="10" y="19.5" text-anchor="start" font-family="sans-serif" font-size="14px" stroke="none" fill="#000000" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0); text-anchor: start; font-family: sans-serif; font-size: 14px; font-weight: normal;" id="op3t" class="flowchartt" font-weight="normal" transform="matrix(1,0,0,1,4,283)"><tspan dy="5.5" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);">扁平化归一化后的vector类型的rdd，并且获取原始数据的schema，最后拼接成为DataFrame</tspan><tspan dy="18" x="10" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></tspan></text><rect x="0" y="0" width="163.28125" height="39" rx="20" ry="20" fill="#ffffff" stroke="#000000" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);" stroke-width="2" class="flowchart" id="e" transform="matrix(1,0,0,1,232.5859,376)"></rect><text x="10" y="19.5" text-anchor="start" font-family="sans-serif" font-size="14px" stroke="none" fill="#000000" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0); text-anchor: start; font-family: sans-serif; font-size: 14px; font-weight: normal;" id="et" class="flowchartt" font-weight="normal" transform="matrix(1,0,0,1,232.5859,376)"><tspan dy="5.5" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);">存储DataFrame到Hive</tspan></text><path fill="none" stroke="#000000" d="M314.2265625,43C314.2265625,43,314.2265625,82.65409994125366,314.2265625,94.00043908460066" stroke-width="2" marker-end="url(#raphael-marker-endblock33-obj1374)" font-family="sans-serif" font-weight="normal" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0); font-family: sans-serif; font-weight: normal;"></path><path fill="none" stroke="#000000" d="M314.2265625,136C314.2265625,136,314.2265625,175.65409994125366,314.2265625,187.00043908460066" stroke-width="2" marker-end="url(#raphael-marker-endblock33-obj1375)" font-family="sans-serif" font-weight="normal" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0); font-family: sans-serif; font-weight: normal;"></path><path fill="none" stroke="#000000" d="M314.2265625,229C314.2265625,229,314.2265625,268.65409994125366,314.2265625,280.00043908460066" stroke-width="2" marker-end="url(#raphael-marker-endblock33-obj1376)" font-family="sans-serif" font-weight="normal" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0); font-family: sans-serif; font-weight: normal;"></path><path fill="none" stroke="#000000" d="M314.2265625,322C314.2265625,322,314.2265625,361.65409994125366,314.2265625,373.00043908460066" stroke-width="2" marker-end="url(#raphael-marker-endblock33-obj1377)" font-family="sans-serif" font-weight="normal" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0); font-family: sans-serif; font-weight: normal;"></path></svg></div>

<p>代码如下：</p>



<pre class="prettyprint"><code class=" hljs fsharp"><span class="hljs-comment">// 1.读取数据</span>
<span class="hljs-keyword">val</span> data = sqlContext.sql(<span class="hljs-string">"select * from normalize"</span>)</code></pre>

<p>读取数据如下： <br>
<img src="https://img-blog.csdn.net/20180224101303951?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZmFuc3kxOTkw/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="data数据" title=""></p>



<pre class="prettyprint"><code class=" hljs avrasm">// <span class="hljs-number">2.</span>构造vector数据
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.ml</span><span class="hljs-preprocessor">.feature</span><span class="hljs-preprocessor">.VectorAssembler</span>
val cols = data<span class="hljs-preprocessor">.schema</span><span class="hljs-preprocessor">.fieldNames</span>
val newFeature = <span class="hljs-string">"fea"</span>
val asb = new VectorAssembler()<span class="hljs-preprocessor">.setInputCols</span>(cols)<span class="hljs-preprocessor">.setOutputCol</span>(newFeature)
val newDf = asb<span class="hljs-preprocessor">.transform</span>(data)
newDf<span class="hljs-preprocessor">.show</span>(<span class="hljs-number">1</span>)</code></pre>

<p><img src="https://img-blog.csdn.net/2018022410142052?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZmFuc3kxOTkw/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="vector数据" title=""></p>



<pre class="prettyprint"><code class=" hljs avrasm">// <span class="hljs-number">3.</span>做归一化
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.ml</span><span class="hljs-preprocessor">.feature</span><span class="hljs-preprocessor">.Normalizer</span>
val norFeature =<span class="hljs-string">"norF"</span>
val normalizer = new Normalizer()<span class="hljs-preprocessor">.setInputCol</span>(newFeature)<span class="hljs-preprocessor">.setOutputCol</span>(norFeature)<span class="hljs-preprocessor">.setP</span>(<span class="hljs-number">1.0</span>)
val l1NormData = normalizer<span class="hljs-preprocessor">.transform</span>(newDf)
l1NormData<span class="hljs-preprocessor">.show</span>(<span class="hljs-number">1</span>)
// 存储DataFrame vector类型报错
// l1NormData<span class="hljs-preprocessor">.select</span>(norFeature)<span class="hljs-preprocessor">.registerTempTable</span>(<span class="hljs-string">"t1"</span>)
// sqlContext<span class="hljs-preprocessor">.sql</span>(<span class="hljs-string">"create table h2 as select * from t1"</span>)</code></pre>

<p><img src="https://img-blog.csdn.net/2018022410153058?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZmFuc3kxOTkw/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="归一化后数据" title=""></p>



<pre class="prettyprint"><code class=" hljs avrasm">// <span class="hljs-number">4.</span>扁平转换vector到row
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.sql</span><span class="hljs-preprocessor">.Row</span>
val finalRdd= l1NormData<span class="hljs-preprocessor">.select</span>(norFeature)<span class="hljs-preprocessor">.rdd</span><span class="hljs-preprocessor">.map</span>(row =&gt; Row<span class="hljs-preprocessor">.fromSeq</span>(row<span class="hljs-preprocessor">.getAs</span>[org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.mllib</span><span class="hljs-preprocessor">.linalg</span><span class="hljs-preprocessor">.DenseVector</span>](<span class="hljs-number">0</span>)<span class="hljs-preprocessor">.toArray</span>))
val finalDf = sqlContext<span class="hljs-preprocessor">.createDataFrame</span>(finalRdd,data<span class="hljs-preprocessor">.schema</span>)
finalDf<span class="hljs-preprocessor">.show</span>(<span class="hljs-number">1</span>)</code></pre>

<p><img src="https://img-blog.csdn.net/20180224101742193?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZmFuc3kxOTkw/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="最终数据" title=""></p>



<pre class="prettyprint"><code class=" hljs sql">// 5. 存储到Hive中
finalDf.registerTempTable("t1")
sqlContext.sql("<span class="hljs-operator"><span class="hljs-keyword">create</span> <span class="hljs-keyword">table</span> h1 <span class="hljs-keyword">as</span> <span class="hljs-keyword">select</span> * <span class="hljs-keyword">from</span> t1<span class="hljs-string">")</span></span></code></pre>

<p><img src="https://img-blog.csdn.net/20180224101842263?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZmFuc3kxOTkw/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="hive表数据" title=""></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>