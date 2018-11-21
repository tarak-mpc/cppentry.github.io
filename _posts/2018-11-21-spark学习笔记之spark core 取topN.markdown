---
layout:     post
title:      spark学习笔记之spark core 取topN
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/wjxing86/article/details/52333743				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="spark学习笔记之spark-core-取topn">spark学习笔记之spark core 取topN</h1>

<p>初入职场的菜鸟，业务上遇到一个取topN的问题，要求是分组取app top5，这些app数据存储在hive表里分属在5个不同字段，也就是说一条用户数据里有5个app，例如：</p>



<pre class="prettyprint"><code class=" hljs perl">北京|<span class="hljs-string">qq|微信|</span>手机百度|微博|淘宝
北京|微信|京东|头条|微博|appstore
上海|<span class="hljs-string">qq|支付宝|</span>手机百度|微博|淘宝
上海|优酷|饿了么|||
...</code></pre>

<p>根据业务需求，本来利用sparl-sql的开窗函数就可以一条sql语句轻松解决，但是公司的spark集群版本太低，不支持开窗，无奈之下写程序来解决，为防止后续再遇此类问题，因此做笔记以记录</p>

<p>闲篇扯完我们来进入正题：</p>



<h2 id="思路">思路：</h2>



<h3 id="步骤一统计每个市下的每个app的总数统计成如下形式">步骤一：统计每个市下的每个app的总数，统计成如下形式：</h3>

<pre class="prettyprint"><code class=" hljs r">北京|qq,<span class="hljs-number">888</span>
北京|微信,<span class="hljs-number">999</span>
广州|支付宝,<span class="hljs-number">777</span>
北京|京东,<span class="hljs-number">567</span>
上海|淘宝,<span class="hljs-number">666</span>
上海|qq,<span class="hljs-number">999</span>
北京|微博,<span class="hljs-number">789</span>
深圳|头条,<span class="hljs-number">555</span>
<span class="hljs-keyword">...</span></code></pre>



<h6 id="从文件获取rddstring">从文件获取RDD&lt;String&gt;：</h6>



<pre class="prettyprint"><code class=" hljs javascript">SparkConf conf = <span class="hljs-keyword">new</span> SparkConf()
    .setAppName(<span class="hljs-string">"sparktest"</span>)
    .setMaster(<span class="hljs-string">"local"</span>);
@SuppressWarnings(<span class="hljs-string">"resource"</span>)
JavaSparkContext sc = <span class="hljs-keyword">new</span> JavaSparkContext(conf);

JavaRDD&lt;<span class="hljs-built_in">String</span>&gt; lines = sc.textFile(<span class="hljs-string">"d:/test.txt"</span>)

</code></pre>



<pre class="prettyprint"><code class=" hljs java">JavaPairRDD&lt;String, Integer&gt; multipleRDD = lines.flatMapToPair(<span class="hljs-keyword">new</span> PairFlatMapFunction&lt;String, String, Integer&gt;() {
    <span class="hljs-keyword">private</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">final</span> <span class="hljs-keyword">long</span> serialVersionUID = <span class="hljs-number">1</span>L;

    <span class="hljs-javadoc">/**
    * PairFlatMapFunction类 包含一个返回Iterable迭代器的抽象方法，精华在于此，迭代器可迭代包含多个Tuple2的List集合
    */</span>
    <span class="hljs-annotation">@Override</span>
    <span class="hljs-keyword">public</span> Iterable&lt;Tuple2&lt;String, Integer&gt;&gt; <span class="hljs-title">call</span>(String line) <span class="hljs-keyword">throws</span> Exception {
        <span class="hljs-comment">// TODO Auto-generated method stub</span>
        <span class="hljs-keyword">final</span> String[] lineSplited = line.split(<span class="hljs-string">"\\|"</span>); 
        Iterable&lt;Tuple2&lt;String, Integer&gt;&gt; iterable = <span class="hljs-keyword">new</span> Iterable&lt;Tuple2&lt;String,Integer&gt;&gt;() {

            <span class="hljs-annotation">@Override</span>
            <span class="hljs-keyword">public</span> Iterator&lt;Tuple2&lt;String, Integer&gt;&gt; <span class="hljs-title">iterator</span>() {
                <span class="hljs-comment">// TODO Auto-generated method stub</span>
                <span class="hljs-comment">// 将多个Tuple2放到List里</span>
                List&lt;Tuple2&lt;String, Integer&gt;&gt; tuple2s = <span class="hljs-keyword">new</span> ArrayList&lt;Tuple2&lt;String, Integer&gt;&gt;();
                <span class="hljs-comment">//lineSplited[0]市</span>
                String preKey = lineSplited[<span class="hljs-number">0</span>];
                <span class="hljs-javadoc">/**
                 * 遍历切分好的字段，判断该字段是那个信息，
                 * 如：下表1到5为app；把信息和preKey组成需要的key，相当于wordCount中的word
                 */</span>
                <span class="hljs-keyword">for</span> (<span class="hljs-keyword">int</span> i = <span class="hljs-number">0</span>; i &lt; lineSplited.length; i++) {

                    <span class="hljs-keyword">if</span>(i &gt;=<span class="hljs-number">1</span> &amp;&amp; i &lt;= <span class="hljs-number">5</span>){
                        <span class="hljs-javadoc">/**
                         * 单个用户的appTop5
                         */</span>
                        tuple2s.add(<span class="hljs-keyword">new</span> Tuple2&lt;String, Integer&gt;(preKey+<span class="hljs-string">"|"</span>+lineSplited[i],Integer.valueOf(<span class="hljs-number">1</span>)));
                    }
                }
                <span class="hljs-comment">//返回迭代；</span>
                <span class="hljs-keyword">return</span> tuple2s.iterator();
            }
        };
        <span class="hljs-keyword">return</span> iterable;
    }
}).reduceByKey(<span class="hljs-keyword">new</span>  Function2&lt;Integer, Integer, Integer&gt;() {
        <span class="hljs-keyword">private</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">final</span> <span class="hljs-keyword">long</span> serialVersionUID = <span class="hljs-number">1</span>L;

        <span class="hljs-annotation">@Override</span>
        <span class="hljs-keyword">public</span> Integer <span class="hljs-title">call</span>(Integer i1, Integer i2) {
            <span class="hljs-keyword">return</span> i1 + i2;
        }
});


</code></pre>



<h3 id="步骤二对步骤一得出的汇总结果以市作为key分组">步骤二：对步骤一得出的汇总结果以市作为key分组：</h3>

<pre class="prettyprint"><code class=" hljs r">----------
北京,qq|<span class="hljs-number">888</span>
北京,微信|<span class="hljs-number">999</span>
北京,京东|<span class="hljs-number">567</span>
北京,微博|<span class="hljs-number">789</span>
<span class="hljs-keyword">...</span>
----------
上海，淘宝|<span class="hljs-number">666</span>
上海,qq|<span class="hljs-number">999</span>
<span class="hljs-keyword">...</span>
----------
<span class="hljs-keyword">...</span></code></pre>



<pre class="prettyprint"><code class=" hljs javascript">JavaPairRDD&lt;<span class="hljs-built_in">String</span>,<span class="hljs-built_in">String</span>&gt; grouped = multipleRDD.mapToPair(<span class="hljs-keyword">new</span> PairFunction&lt;Tuple2&lt;<span class="hljs-built_in">String</span>, Integer&gt;, <span class="hljs-built_in">String</span>, <span class="hljs-built_in">String</span>&gt;() {
    private static final long serialVersionUID = <span class="hljs-number">1</span>L;

    @Override
    public Tuple2&lt;<span class="hljs-built_in">String</span>, <span class="hljs-built_in">String</span>&gt; call(Tuple2&lt;<span class="hljs-built_in">String</span>, Integer&gt; arg0) throws Exception {
        <span class="hljs-comment">// TODO Auto-generated method stub</span>
        <span class="hljs-built_in">String</span>[] string = arg0._1.split(<span class="hljs-string">"\\|"</span>);
        <span class="hljs-built_in">String</span> str1 = string[<span class="hljs-number">0</span>]; <span class="hljs-comment">//key</span>
        <span class="hljs-keyword">return</span> <span class="hljs-keyword">new</span> Tuple2&lt;<span class="hljs-built_in">String</span>, <span class="hljs-built_in">String</span>&gt;(str1,string[<span class="hljs-number">1</span>]+<span class="hljs-string">"|"</span>+arg0._2);

    }
}).groupByKey();


</code></pre>



<h3 id="步骤三对步骤二得出的结果分市从大到小排序并取top5组成以分隔的字符串">步骤三：对步骤二得出的结果分市从大到小排序并取top5组成以”|”分隔的字符串：</h3>

<pre class="prettyprint"><code class=" hljs r">----------
北京,微信|<span class="hljs-number">999</span>
北京,qq|<span class="hljs-number">888</span>
北京,微博|<span class="hljs-number">789</span>
北京,京东|<span class="hljs-number">567</span>
<span class="hljs-keyword">...</span>
----------
上海,qq|<span class="hljs-number">999</span>
上海，淘宝|<span class="hljs-number">666</span>
<span class="hljs-keyword">...</span>
----------
<span class="hljs-keyword">...</span></code></pre>

<hr>



<pre class="prettyprint"><code class=" hljs perl">北京|微信|<span class="hljs-string">qq|微博|</span>京东|淘宝
上海|微博|<span class="hljs-string">qq|微信|</span>京东|手机百度
...</code></pre>



<pre class="prettyprint"><code class=" hljs java">JavaPairRDD&lt;String, String&gt; groupedTopN = grouped.mapToPair(<span class="hljs-keyword">new</span> PairFunction&lt;Tuple2&lt;String,Iterable&lt;String&gt;&gt;, String, String&gt;() {
    <span class="hljs-keyword">private</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">final</span> <span class="hljs-keyword">long</span> serialVersionUID = <span class="hljs-number">1</span>L;

    <span class="hljs-javadoc">/**
    * 这里的关键是组合字段和排序
    * list.sort(new Comparator),用比较器排序
    */</span>
    <span class="hljs-annotation">@Override</span>
    <span class="hljs-keyword">public</span> Tuple2&lt;String, String&gt; <span class="hljs-title">call</span>(Tuple2&lt;String, Iterable&lt;String&gt;&gt; arg0) <span class="hljs-keyword">throws</span> Exception {
        <span class="hljs-comment">// TODO Auto-generated method stub</span>
        Iterator&lt;String&gt; iterable = arg0._2.iterator();
        <span class="hljs-javadoc">/**
         * 最终字符串信息初始化，
         */</span>
        String str = arg0._1;
        <span class="hljs-comment">//存一个分组下的存所有app</span>
        List&lt;String&gt; list1 = <span class="hljs-keyword">new</span> ArrayList&lt;String&gt;();
        List&lt;String&gt; appTop5;
        <span class="hljs-javadoc">/**
         * 迭代一个key，得到其下的所有value
         */</span>
        <span class="hljs-keyword">while</span> (iterable.hasNext()) {
            <span class="hljs-comment">//切分value并转换为list</span>
            String[] strings =iterable.next().split(<span class="hljs-string">"\\|"</span>);
            list1 = Arrays.asList(strings);

        }
        <span class="hljs-javadoc">/**
         * app排序
         */</span>
        Collections.sort(list1, <span class="hljs-keyword">new</span> Comparator&lt;String&gt;() {
            <span class="hljs-comment">//把app|count切分，按count数量排序</span>
            <span class="hljs-annotation">@Override</span>
            <span class="hljs-keyword">public</span> <span class="hljs-keyword">int</span> <span class="hljs-title">compare</span>(String o1, String o2) {
                <span class="hljs-comment">// TODO Auto-generated method stub</span>
                <span class="hljs-keyword">int</span> i1 = Integer.parseInt(o1.split(<span class="hljs-string">"\\|"</span>)[<span class="hljs-number">1</span>]);
                <span class="hljs-keyword">int</span> i2 = Integer.parseInt(o2.split(<span class="hljs-string">"\\|"</span>)[<span class="hljs-number">1</span>]);
                <span class="hljs-keyword">return</span> -(i1 - i2);<span class="hljs-comment">//逆序</span>
            }
        });
        <span class="hljs-javadoc">/**
         * 取appTop5
         */</span>
        <span class="hljs-keyword">if</span>(list1.size()&lt;<span class="hljs-number">5</span>){
            appTop5 = list1;
        }<span class="hljs-keyword">else</span>{
            appTop5 = list1.subList(<span class="hljs-number">0</span>, <span class="hljs-number">5</span>);
        }

        <span class="hljs-comment">//组字符串</span>
        <span class="hljs-keyword">for</span> (String string : apps) {
            str +=<span class="hljs-string">"|"</span>+string;
        }

        <span class="hljs-keyword">return</span> <span class="hljs-keyword">new</span> Tuple2&lt;String, String&gt;(arg0._1,str);
    }

});


</code></pre>



<h3 id="步骤四持久化到hdfs入hive表或到控制台">步骤四：持久化到HDFS入Hive表或到控制台</h3>

<pre class="prettyprint"><code class=" hljs java"><span class="hljs-javadoc">/**
 * 输出到控制台
 */</span>
groupedTopN.foreach(<span class="hljs-keyword">new</span> VoidFunction&lt;Tuple2&lt;String,String&gt;&gt;() {

    <span class="hljs-annotation">@Override</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">call</span>(Tuple2&lt;String, String&gt; arg0) <span class="hljs-keyword">throws</span> Exception {
        <span class="hljs-comment">// TODO Auto-generated method stub</span>
        System.out.println(arg0._2);
    }
});

</code></pre>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-javadoc">/**
 * 输出到文件
 */</span>
finalRDD.rdd().saveAsTextFile(outputPath);

</code></pre>

<p>需要的jar包：</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>