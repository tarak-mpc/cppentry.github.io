---
layout:     post
title:      在windows上使用eclipse提交Spark任务到Spark平台上
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="在windows上使用eclipse提交spark任务到spark平台上">在windows上使用eclipse提交Spark任务到Spark平台上</h1>

<p>平台环境：</p>

<ul>
<li><strong>本地win7系统</strong></li>
<li><strong>本地spark和集群spark都是2.0.0</strong></li>
<li><strong>eclipse（luna）</strong></li>
</ul>

<p>运行模式：</p>

<ul>
<li><strong>local</strong></li>
<li><strong>Spark Standalone</strong></li>
<li><strong>YARN</strong></li>
</ul>

<hr>

<p>程序代码如下：</p>

<pre class="prettyprint"><code class=" hljs avrasm">package sparkproject1<span class="hljs-comment">;</span>

import scala<span class="hljs-preprocessor">.Tuple</span>2<span class="hljs-comment">;</span>

import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.SparkConf</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.api</span><span class="hljs-preprocessor">.java</span><span class="hljs-preprocessor">.JavaPairRDD</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.api</span><span class="hljs-preprocessor">.java</span><span class="hljs-preprocessor">.JavaRDD</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.api</span><span class="hljs-preprocessor">.java</span><span class="hljs-preprocessor">.JavaSparkContext</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.api</span><span class="hljs-preprocessor">.java</span><span class="hljs-preprocessor">.function</span><span class="hljs-preprocessor">.FlatMapFunction</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.api</span><span class="hljs-preprocessor">.java</span><span class="hljs-preprocessor">.function</span><span class="hljs-preprocessor">.Function</span>2<span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.api</span><span class="hljs-preprocessor">.java</span><span class="hljs-preprocessor">.function</span><span class="hljs-preprocessor">.PairFunction</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.sql</span><span class="hljs-preprocessor">.SparkSession</span><span class="hljs-comment">;</span>

import java<span class="hljs-preprocessor">.util</span><span class="hljs-preprocessor">.Arrays</span><span class="hljs-comment">;</span>
import java<span class="hljs-preprocessor">.util</span><span class="hljs-preprocessor">.Iterator</span><span class="hljs-comment">;</span>
import java<span class="hljs-preprocessor">.util</span><span class="hljs-preprocessor">.List</span><span class="hljs-comment">;</span>
import java<span class="hljs-preprocessor">.util</span><span class="hljs-preprocessor">.regex</span><span class="hljs-preprocessor">.Pattern</span><span class="hljs-comment">;</span>

public final class wordcount {
  private static final Pattern SPACE = Pattern<span class="hljs-preprocessor">.compile</span>(<span class="hljs-string">" "</span>)<span class="hljs-comment">;</span>

  public static void main(String[] args) throws Exception {
    SparkConf conf = new SparkConf()<span class="hljs-preprocessor">.setAppName</span>(<span class="hljs-string">"JavaWordCount"</span>)<span class="hljs-preprocessor">.setMaster</span>(<span class="hljs-string">"spark://**.**.*.*:7077"</span>)<span class="hljs-comment">;</span>
    JavaSparkContext sc = new JavaSparkContext(conf)<span class="hljs-comment">;</span>
    sc<span class="hljs-preprocessor">.addJar</span>(<span class="hljs-string">"F:\\大数据\\jar包\\wordcount.jar"</span>)<span class="hljs-comment">;</span>

    JavaRDD&lt;String&gt; lines = sc<span class="hljs-preprocessor">.textFile</span>(<span class="hljs-string">"hdfs://****:9000/input/input.txt"</span>)<span class="hljs-comment">;</span>

    JavaRDD&lt;String&gt; words = lines<span class="hljs-preprocessor">.flatMap</span>(new FlatMapFunction&lt;String, String&gt;() {
      @Override
      public Iterator&lt;String&gt; <span class="hljs-keyword">call</span>(String s) {
        return Arrays<span class="hljs-preprocessor">.asList</span>(SPACE<span class="hljs-preprocessor">.split</span>(s))<span class="hljs-preprocessor">.iterator</span>()<span class="hljs-comment">;</span>
      }
    })<span class="hljs-comment">;</span>

    JavaPairRDD&lt;String, Integer&gt; ones = words<span class="hljs-preprocessor">.mapToPair</span>(
      new PairFunction&lt;String, String, Integer&gt;() {
        @Override
        public Tuple2&lt;String, Integer&gt; <span class="hljs-keyword">call</span>(String s) {
          return new Tuple2&lt;&gt;(s, <span class="hljs-number">1</span>)<span class="hljs-comment">;</span>
        }
      })<span class="hljs-comment">;</span>

    JavaPairRDD&lt;String, Integer&gt; counts = ones<span class="hljs-preprocessor">.reduceByKey</span>(
      new Function2&lt;Integer, Integer, Integer&gt;() {
        @Override
        public Integer <span class="hljs-keyword">call</span>(Integer i1, Integer i2) {
          return i1 + i2<span class="hljs-comment">;</span>
        }
      })<span class="hljs-comment">;</span>

    List&lt;Tuple2&lt;String, Integer&gt;&gt; output = counts<span class="hljs-preprocessor">.collect</span>()<span class="hljs-comment">;</span>
    for (Tuple2&lt;?,?&gt; tuple : output) {
      System<span class="hljs-preprocessor">.out</span><span class="hljs-preprocessor">.println</span>(tuple._1() + <span class="hljs-string">": "</span> + tuple._2())<span class="hljs-comment">;</span>
    }
  }
}
</code></pre>



<h2 id="local模式">local模式</h2>

<p>local模式只需要将程序中的setMaster(“local”)就可以了，一般不会出现什么问题。</p>



<h2 id="spark-standalone模式">Spark Standalone模式</h2>

<p>在没有加sc.addJar(“F:\大数据\jar包\wordcount.jar”);这条语句之前报如下错误：</p>



<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-label">ava.lang.ClassCastException:</span> cannot assign instance of scala<span class="hljs-preprocessor">.collection</span><span class="hljs-preprocessor">.immutable</span><span class="hljs-preprocessor">.List</span>$SerializationProxy to field 

org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.rdd</span><span class="hljs-preprocessor">.RDD</span><span class="hljs-preprocessor">.org</span>$apache$spark$rdd$RDD$$dependencies_ of type scala<span class="hljs-preprocessor">.collection</span><span class="hljs-preprocessor">.Seq</span> <span class="hljs-keyword">in</span> instance of 

org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.rdd</span><span class="hljs-preprocessor">.MapPartitionsRDD</span>
</code></pre>

<p>设置之后，所有工作节点报错：</p>



<pre class="prettyprint"><code class=" hljs ruby">java.lang.<span class="hljs-constant">RuntimeException</span><span class="hljs-symbol">:</span> <span class="hljs-constant">Stream</span> <span class="hljs-string">'/jars/wordcount.jar'</span> was <span class="hljs-keyword">not</span> found.</code></pre>

<p>显然是因为jar包未能传给工作节点，将生成的jar包放在上面路径中，运行成功。</p>

<h2 id="yarn模式">YARN模式</h2>

<p>修改的代码如下：</p>



<pre class="prettyprint"><code class=" hljs avrasm">SparkConf conf = new SparkConf()<span class="hljs-preprocessor">.setAppName</span>(<span class="hljs-string">"JavaWordCount"</span>)<span class="hljs-preprocessor">.setMaster</span>(<span class="hljs-string">"yarn-client"</span>)<span class="hljs-comment">;</span>
conf<span class="hljs-preprocessor">.set</span>(<span class="hljs-string">"spark.yarn.dist.files"</span>, <span class="hljs-string">"src\\yarn-site.xml"</span>)<span class="hljs-comment">;</span></code></pre>

<p>将core-site.xml、hdfs-site.xml、yarn-site.xml三个文件放在项目src文件夹下，这三个文件从hadoop集群配置文件夹中复制下来，直接run java application就可以了。 <br>
有些教程还会有如下代码：</p>

<pre class="prettyprint"><code class=" hljs sql">sparkConf.<span class="hljs-operator"><span class="hljs-keyword">set</span>(<span class="hljs-string">"spark.yarn.jar"</span>, <span class="hljs-string">"hdfs://192.168.0.1:9000/user/bigdatagfts/spark-assembly-1.5.2-hadoop2.6.0.jar"</span>);</span></code></pre>

<p>设置spark jar包地址，我并没有设置，但是日志显示，也会有jar包的上传过程。具体原理还不是很清楚，为什么需要上传这样的jar包。上面设置的yarn-client，如果你的电脑是在集群里面的，应该是设置为yarn-cluster的。</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>