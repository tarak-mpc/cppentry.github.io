---
layout:     post
title:      Spark倒排，求中位数，CountOnce
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p><strong>倒排序</strong> <br>
输入： <br>
id1 spark hadoop <br>
id2 scala spark <br>
id3 java hadoop spark <br>
id4 scala java <br>
id5 the spark <br>
id6 the hadoop and spark <br>
id7 hadoop Flink storm spark <br>
输出： <br>
Flink id7 <br>
scala id2 id4 <br>
spark id1 id2 id3 id5 id6 id7 <br>
hadoop id1 id3 id6 id7 <br>
java id3 id4 <br>
and id6 <br>
storm id7 <br>
the id5 id6 <br>
代码（Scala版）：</p>



<pre class="prettyprint"><code class=" hljs avrasm">package <span class="hljs-keyword">com</span><span class="hljs-preprocessor">.dt</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.cores</span><span class="hljs-preprocessor">.scala</span>



import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span>.{SparkContext, SparkConf}

import scala<span class="hljs-preprocessor">.collection</span><span class="hljs-preprocessor">.mutable</span>



object InvertedIndex {
def main (args: Array[String]) {

val conf = new SparkConf()<span class="hljs-preprocessor">.setAppName</span>(<span class="hljs-string">"inverted index"</span>)<span class="hljs-preprocessor">.setMaster</span>(<span class="hljs-string">"local"</span>)
val sc = new SparkContext(conf)

val data = sc<span class="hljs-preprocessor">.textFile</span>(<span class="hljs-string">"E:\\workspases\\data\\invertedIndex.txt"</span>)

val keydata = data<span class="hljs-preprocessor">.map</span>(line =&gt; line<span class="hljs-preprocessor">.split</span>(<span class="hljs-string">"\t"</span>))<span class="hljs-preprocessor">.map</span>(linetemp =&gt;(linetemp(<span class="hljs-number">0</span>),linetemp(<span class="hljs-number">1</span>)))
val inverting = keydata<span class="hljs-preprocessor">.flatMap</span>(file =&gt;{
val list = new mutable<span class="hljs-preprocessor">.LinkedHashMap</span>[String,String]()
val words = file._2<span class="hljs-preprocessor">.split</span>(<span class="hljs-string">" "</span>)<span class="hljs-preprocessor">.iterator</span>
while (words<span class="hljs-preprocessor">.hasNext</span>)
{
list<span class="hljs-preprocessor">.put</span>(words<span class="hljs-preprocessor">.next</span>(),file._1)
}
list
})
inverting<span class="hljs-preprocessor">.reduceByKey</span>(_+<span class="hljs-string">" "</span>+_)<span class="hljs-preprocessor">.map</span>(pari=&gt;pari._1+<span class="hljs-string">"\t"</span>+pari._2)<span class="hljs-preprocessor">.foreach</span>(println)
sc<span class="hljs-preprocessor">.stop</span>()
}
}</code></pre>

<p><strong>中位数</strong></p>



<pre class="prettyprint"><code class=" hljs livecodeserver">package com.dt.spark.cores.scala

import org.apache.spark.{SparkContext, SparkConf}



object Median {

def main (args: Array[String]) {

val conf = <span class="hljs-built_in">new</span> SparkConf().setAppName(<span class="hljs-string">"Median"</span>).setMaster(<span class="hljs-string">"local"</span>)
val sc = <span class="hljs-built_in">new</span> SparkContext(conf)

val data = sc.textFile(<span class="hljs-string">"E:\\workspases\\data\\Median.txt"</span>)

val <span class="hljs-keyword">words</span> = data.flatMap(_.<span class="hljs-built_in">split</span>(<span class="hljs-string">" "</span>)).map(<span class="hljs-built_in">word</span> =&gt; <span class="hljs-built_in">word</span>.toInt)
val <span class="hljs-built_in">number</span> = <span class="hljs-keyword">words</span>.map(<span class="hljs-built_in">word</span> =&gt;(<span class="hljs-built_in">word</span>/<span class="hljs-number">4</span>,<span class="hljs-built_in">word</span>)).sortByKey()
val pariCount = <span class="hljs-keyword">words</span>.map(<span class="hljs-built_in">word</span> =&gt; (<span class="hljs-built_in">word</span>/<span class="hljs-number">4</span>,<span class="hljs-number">1</span>)).reduceByKey(_+_).sortByKey()
val count = <span class="hljs-keyword">words</span>.count().toInt
var <span class="hljs-keyword">mid</span> =<span class="hljs-number">0</span>
<span class="hljs-keyword">if</span>(count%<span class="hljs-number">2</span> != <span class="hljs-number">0</span>)
{
<span class="hljs-keyword">mid</span> = count/<span class="hljs-number">2</span>+<span class="hljs-number">1</span>
}<span class="hljs-keyword">else</span>
{
<span class="hljs-keyword">mid</span> = count/<span class="hljs-number">2</span>
}

var temp =<span class="hljs-number">0</span>
var temp1= <span class="hljs-number">0</span>
var index = <span class="hljs-number">0</span>
val tongNumber = pariCount.count().toInt

var foundIt = <span class="hljs-constant">false</span>
<span class="hljs-keyword">for</span>(i &lt;- <span class="hljs-number">0</span> <span class="hljs-built_in">to</span> tongNumber-<span class="hljs-number">1</span> <span class="hljs-keyword">if</span> !foundIt)
{
temp = temp + pariCount.collectAsMap()(i)
temp1 = temp - pariCount.collectAsMap()(i)
<span class="hljs-keyword">if</span>(temp &gt;= <span class="hljs-keyword">mid</span>)
{
index = i
foundIt = <span class="hljs-constant">true</span>
}
}
val tonginneroffset = <span class="hljs-keyword">mid</span> - temp1

val <span class="hljs-built_in">median</span> = <span class="hljs-built_in">number</span>.<span class="hljs-built_in">filter</span>(_.<span class="hljs-title">_1</span>==index).takeOrdered(tonginneroffset)
sc.setLogLevel(<span class="hljs-string">"ERROR"</span>)
println(<span class="hljs-built_in">median</span>(tonginneroffset-<span class="hljs-number">1</span>).<span class="hljs-title">_2</span>)
sc.stop()

}
}
</code></pre>

<p><strong>CountOnce</strong> <br>
问题描述： <br>
假设HDFS只存储一个标号为ID的Block，每份数据保存2个备份，这样就有2个机器存储了相同的数据。其中ID是小于10亿的整数。若有一个数据块丢失，则需要找到哪个是丢失的数据块。 <br>
在某个时间，如果得到一个数据块ID的列表，能否快速地找到这个表中仅出现一次的ID？即快速找出出现故障的数据块的ID。 <br>
问题阐述：已知一个数组，数组中只有一个数据是出现一遍的，其他数据都是出现两遍，将出现一次的数据找出。 <br>
输入： <br>
5 <br>
3 <br>
2 <br>
2 <br>
3 <br>
5 <br>
7 <br>
6 <br>
6 <br>
9 <br>
9 <br>
10 <br>
11 <br>
16 <br>
11 <br>
10 <br>
16</p>

<p>输出： <br>
7</p>

<p>代码：</p>



<pre class="prettyprint"><code class=" hljs avrasm">package <span class="hljs-keyword">com</span><span class="hljs-preprocessor">.dt</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.cores</span><span class="hljs-preprocessor">.scala</span>

import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span>.{SparkContext, SparkConf}


object CountOnce {

def main (args: Array[String]) {
val conf = new SparkConf()<span class="hljs-preprocessor">.setAppName</span>(<span class="hljs-string">"CountOnce"</span>)<span class="hljs-preprocessor">.setMaster</span>(<span class="hljs-string">"local"</span>)
val sc = new SparkContext(conf)

val data = sc<span class="hljs-preprocessor">.textFile</span>(<span class="hljs-string">"E:\\workspases\\data\\CountOnce.txt"</span>)
val word = data<span class="hljs-preprocessor">.map</span>(line =&gt; line<span class="hljs-preprocessor">.toInt</span>)
val result =word<span class="hljs-preprocessor">.reduce</span>(_^_)
println(result)
}
}</code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>