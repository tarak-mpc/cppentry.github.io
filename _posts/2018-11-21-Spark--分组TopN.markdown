---
layout:     post
title:      Spark--分组TopN
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/wuxintdrh/article/details/72814448				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2 id="为了获取每个分组的topn-首先要进行分组-再对每个分组进行排序获取topn">为了获取每个分组的topN， 首先要进行分组， 再对每个分组进行排序，获取TopN。</h2>

<h2 id="测试数据">测试数据</h2>



<pre class="prettyprint"><code class=" hljs ">hadoop 23
spark 45
java 90
spark 57
spark 90
hadoop 99
hadoop 76
spark 45
spark 88
spark 89
hadoop 45
hadoop 90
java 78
java 70</code></pre>

<h2 id="11第一步-将源数据转化为key-value格式便于按照key分组">1.1、第一步， 将源数据转化为（key， value）格式，便于按照key分组</h2>



<pre class="prettyprint"><code class=" hljs lasso">        SparkConf conf <span class="hljs-subst">=</span> <span class="hljs-literal">new</span> SparkConf()<span class="hljs-built_in">.</span>setMaster(<span class="hljs-string">"local"</span>)<span class="hljs-built_in">.</span>setAppName(<span class="hljs-string">"WordCount"</span>);
        <span class="hljs-comment">//内部实际调用的SparkContext</span>
        JavaSparkContext jsc <span class="hljs-subst">=</span> <span class="hljs-literal">new</span> JavaSparkContext(conf);
        <span class="hljs-comment">//读取文件，将每行数据转换为</span>
        JavaRDD<span class="hljs-subst">&lt;</span><span class="hljs-built_in">String</span><span class="hljs-subst">&gt;</span> lines <span class="hljs-subst">=</span> jsc<span class="hljs-built_in">.</span>textFile(<span class="hljs-string">"C:\\Users\\12285\\Desktop\\test"</span>);<span class="hljs-comment">//hadoopRDD</span>

        JavaPairRDD<span class="hljs-subst">&lt;</span><span class="hljs-built_in">String</span>, <span class="hljs-built_in">Integer</span><span class="hljs-subst">&gt;</span> pairs <span class="hljs-subst">=</span> lines<span class="hljs-built_in">.</span>mapToPair(<span class="hljs-literal">new</span> PairFunction<span class="hljs-subst">&lt;</span><span class="hljs-built_in">String</span>, <span class="hljs-built_in">String</span>    , <span class="hljs-built_in">Integer</span><span class="hljs-subst">&gt;</span>() {

            <span class="hljs-keyword">private</span> static final long serialVersionUID <span class="hljs-subst">=</span> <span class="hljs-number">1</span>L;

            <span class="hljs-keyword">public</span> Tuple2<span class="hljs-subst">&lt;</span><span class="hljs-built_in">String</span>, <span class="hljs-built_in">Integer</span><span class="hljs-subst">&gt;</span> call(<span class="hljs-built_in">String</span> line) throws Exception {
                <span class="hljs-keyword">return</span> <span class="hljs-literal">new</span> Tuple2<span class="hljs-subst">&lt;</span><span class="hljs-built_in">String</span>, <span class="hljs-built_in">Integer</span><span class="hljs-subst">&gt;</span>(line<span class="hljs-built_in">.</span>split(<span class="hljs-string">" "</span>)<span class="hljs-preprocessor">[</span><span class="hljs-number">0</span><span class="hljs-preprocessor">]</span><span class="hljs-markup">, Integer.valueOf(line.split(" ")</span><span class="hljs-preprocessor">[</span><span class="hljs-number">1</span><span class="hljs-preprocessor">]</span><span class="hljs-markup">));
            }
        });</span></code></pre>

<h2 id="12第二步按照key分组">1.2、第二步，按照key分组</h2>



<pre class="prettyprint"><code class=" hljs lasso">        <span class="hljs-comment">//按照科目分组</span>
        JavaPairRDD<span class="hljs-subst">&lt;</span><span class="hljs-built_in">String</span>, Iterable<span class="hljs-subst">&lt;</span><span class="hljs-built_in">Integer</span><span class="hljs-subst">&gt;&gt;</span> groupPairs <span class="hljs-subst">=</span> pairs<span class="hljs-built_in">.</span>groupByKey();
</code></pre>

<h2 id="13第三步组内进行排序">1.3、第三步，组内进行排序</h2>

<h3 id="131由于分组后的rdd为javapairrddstring-iterableinteger-key为科目名称-value为成绩的集合所以将使用maptopair-在组内进行排序">1.3.1、由于分组后的RDD为JavaPairRDD&lt;String, Iterable&lt;Integer&gt;&gt;， key为科目名称， value为成绩的集合，所以将使用mapToPair， 在组内进行排序</h3>

<pre class="prettyprint"><code class=" hljs cs">
        JavaPairRDD&lt;String, Iterable&lt;Integer&gt;&gt; top5Pairs = groupPairs.mapToPair(<span class="hljs-keyword">new</span> PairFunction&lt;Tuple2&lt;String,Iterable&lt;Integer&gt;&gt;, String, Iterable&lt;Integer&gt;&gt;() {

            <span class="hljs-keyword">private</span> <span class="hljs-keyword">static</span> final <span class="hljs-keyword">long</span> serialVersionUID = <span class="hljs-number">1</span>L;

            <span class="hljs-keyword">public</span> Tuple2&lt;String, Iterable&lt;Integer&gt;&gt; <span class="hljs-title">call</span>(Tuple2&lt;String, Iterable&lt;Integer&gt;&gt; groupedPair) throws Exception {
                Integer[] top5 = <span class="hljs-keyword">new</span> Integer[<span class="hljs-number">5</span>];
                String groupedKey = groupedPair._1;
                Iterator&lt;Integer&gt; groupedValue = groupedPair._2.iterator();
                <span class="hljs-keyword">while</span>(groupedValue.hasNext()) {
                    Integer <span class="hljs-keyword">value</span> = groupedValue.next();

                    <span class="hljs-keyword">for</span> (<span class="hljs-keyword">int</span> i = <span class="hljs-number">0</span>; i &lt; top5.length; i++) {
                        <span class="hljs-keyword">if</span> (top5[i] == <span class="hljs-keyword">null</span>) {
                            top5[i] = <span class="hljs-keyword">value</span>;
                            <span class="hljs-keyword">break</span>;
                        }<span class="hljs-keyword">else</span> <span class="hljs-keyword">if</span>(top5[i] &gt; <span class="hljs-keyword">value</span>){ <span class="hljs-comment">//索引处的值比value大</span>
                            <span class="hljs-comment">//该位置之后的值向后移动</span>
                            <span class="hljs-keyword">for</span> (<span class="hljs-keyword">int</span> j = <span class="hljs-number">4</span>; j &gt; i; j--) {
                                top5[j] = top5[j-<span class="hljs-number">1</span>];
                            }
                            top5[i] = <span class="hljs-keyword">value</span>;
                            <span class="hljs-keyword">break</span>;
                        } 
                        <span class="hljs-comment">//否则， //索引处的值比value小， 在top5总继续向后比较</span>
                    }
                }
                <span class="hljs-keyword">return</span> <span class="hljs-keyword">new</span> Tuple2&lt;String, Iterable&lt;Integer&gt;&gt;(groupedKey, Arrays.asList(top5));
            }
        });</code></pre>

<h2 id="14完整代码">1.4、完整代码</h2>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">package</span> com.chb.sparkDemo.TopNGroup;

<span class="hljs-keyword">import</span> java.util.Arrays;
<span class="hljs-keyword">import</span> java.util.Iterator;

<span class="hljs-keyword">import</span> org.apache.spark.SparkConf;
<span class="hljs-keyword">import</span> org.apache.spark.api.java.JavaPairRDD;
<span class="hljs-keyword">import</span> org.apache.spark.api.java.JavaRDD;
<span class="hljs-keyword">import</span> org.apache.spark.api.java.JavaSparkContext;
<span class="hljs-keyword">import</span> org.apache.spark.api.java.function.PairFunction;
<span class="hljs-keyword">import</span> org.apache.spark.api.java.function.VoidFunction;

<span class="hljs-keyword">import</span> scala.Tuple2;

<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">TopNGroupTest</span> {</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">main</span>(String[] args) {
        SparkConf conf = <span class="hljs-keyword">new</span> SparkConf().setMaster(<span class="hljs-string">"local"</span>).setAppName(<span class="hljs-string">"WordCount"</span>);
        <span class="hljs-comment">//内部实际调用的SparkContext</span>
        JavaSparkContext jsc = <span class="hljs-keyword">new</span> JavaSparkContext(conf);
        <span class="hljs-comment">//读取文件，将每行数据转换为</span>
        JavaRDD&lt;String&gt; lines = jsc.textFile(<span class="hljs-string">"C:\\Users\\12285\\Desktop\\test"</span>);<span class="hljs-comment">//hadoopRDD</span>

        JavaPairRDD&lt;String, Integer&gt; pairs = lines.mapToPair(<span class="hljs-keyword">new</span> PairFunction&lt;String, String    , Integer&gt;() {

            <span class="hljs-keyword">private</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">final</span> <span class="hljs-keyword">long</span> serialVersionUID = <span class="hljs-number">1</span>L;

            <span class="hljs-keyword">public</span> Tuple2&lt;String, Integer&gt; <span class="hljs-title">call</span>(String line) <span class="hljs-keyword">throws</span> Exception {
                <span class="hljs-keyword">return</span> <span class="hljs-keyword">new</span> Tuple2&lt;String, Integer&gt;(line.split(<span class="hljs-string">" "</span>)[<span class="hljs-number">0</span>], Integer.valueOf(line.split(<span class="hljs-string">" "</span>)[<span class="hljs-number">1</span>]));
            }
        });

        <span class="hljs-comment">//按照科目分组</span>
        JavaPairRDD&lt;String, Iterable&lt;Integer&gt;&gt; groupPairs = pairs.groupByKey();

        JavaPairRDD&lt;String, Iterable&lt;Integer&gt;&gt; top5Pairs = groupPairs.mapToPair(<span class="hljs-keyword">new</span> PairFunction&lt;Tuple2&lt;String,Iterable&lt;Integer&gt;&gt;, String, Iterable&lt;Integer&gt;&gt;() {

            <span class="hljs-keyword">private</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">final</span> <span class="hljs-keyword">long</span> serialVersionUID = <span class="hljs-number">1</span>L;

            <span class="hljs-keyword">public</span> Tuple2&lt;String, Iterable&lt;Integer&gt;&gt; <span class="hljs-title">call</span>(Tuple2&lt;String, Iterable&lt;Integer&gt;&gt; groupedPair) <span class="hljs-keyword">throws</span> Exception {
                Integer[] top5 = <span class="hljs-keyword">new</span> Integer[<span class="hljs-number">5</span>];
                String groupedKey = groupedPair._1;
                Iterator&lt;Integer&gt; groupedValue = groupedPair._2.iterator();
                <span class="hljs-keyword">while</span>(groupedValue.hasNext()) {
                    Integer value = groupedValue.next();

                    <span class="hljs-keyword">for</span> (<span class="hljs-keyword">int</span> i = <span class="hljs-number">0</span>; i &lt; top5.length; i++) {
                        <span class="hljs-keyword">if</span> (top5[i] == <span class="hljs-keyword">null</span>) {
                            top5[i] = value;
                            <span class="hljs-keyword">break</span>;
                        }<span class="hljs-keyword">else</span> <span class="hljs-keyword">if</span>(top5[i] &gt; value){ <span class="hljs-comment">//索引处的值比value大</span>
                            <span class="hljs-comment">//该位置之后的值向后移动</span>
                            <span class="hljs-keyword">for</span> (<span class="hljs-keyword">int</span> j = <span class="hljs-number">4</span>; j &gt; i; j--) {
                                top5[j] = top5[j-<span class="hljs-number">1</span>];
                            }
                            top5[i] = value;
                            <span class="hljs-keyword">break</span>;
                        } 
                        <span class="hljs-comment">//否则， //索引处的值比value小， 在top5总继续向后比较</span>
                    }
                }
                <span class="hljs-keyword">return</span> <span class="hljs-keyword">new</span> Tuple2&lt;String, Iterable&lt;Integer&gt;&gt;(groupedKey, Arrays.asList(top5));
            }
        });

        top5Pairs.foreach(<span class="hljs-keyword">new</span> VoidFunction&lt;Tuple2&lt;String,Iterable&lt;Integer&gt;&gt;&gt;() {

            <span class="hljs-keyword">private</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">final</span> <span class="hljs-keyword">long</span> serialVersionUID = <span class="hljs-number">1</span>L;

            <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">call</span>(Tuple2&lt;String, Iterable&lt;Integer&gt;&gt; pair) <span class="hljs-keyword">throws</span> Exception {
                String groupedKey = pair._1;
                System.out.println(<span class="hljs-string">"Grouped Key : "</span> + groupedKey);
                Iterator&lt;Integer&gt; groupedValue = pair._2.iterator();
                <span class="hljs-keyword">while</span>(groupedValue.hasNext()) {
                    Integer value = groupedValue.next();
                    System.out.println(value);
                }
                System.out.println(<span class="hljs-string">"=================================="</span>);
            }
        });


    }
}
</code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>