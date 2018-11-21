---
layout:     post
title:      spark从入门到放弃五十二:Spark Streaming(12)结合spark Sql
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p><strong>文章地址：<a href="http://www.haha174.top/article/details/253627" rel="nofollow">http://www.haha174.top/article/details/253627</a></strong></p>

<h2 id="1简介">1.简介</h2>

<p>Spark Streaming  强大的地方在于，可以于spark  core 和spark sql   整合使用，之前已经通过transform   foreachRDD  等算子看到了 如何将DStream  种的RDD  使用spark  core  执行批处理操作。现在就来看看  如何将spark  sql  和spark  Streaming  整合起来操作</p>



<h2 id="2案例">2.案例</h2>

<p>每隔10秒  ，统计最近60秒的，每个种类的每个商品的点击次数，然后统计出每个种类top3  热门的商品 <br>
下面给出</p>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">Top3HotProduct</span> {</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">main</span>(String[] args) <span class="hljs-keyword">throws</span> InterruptedException {
        SparkConf conf=<span class="hljs-keyword">new</span> SparkConf().setAppName(<span class="hljs-string">"Top3HotProduct"</span>).setMaster(<span class="hljs-string">"local[2]"</span>);
        JavaStreamingContext jssc=<span class="hljs-keyword">new</span> JavaStreamingContext(conf, Durations.seconds(<span class="hljs-number">1</span>));
        <span class="hljs-comment">//首先看一下，输入日志的格式</span>
        <span class="hljs-comment">//leo  product1 category1</span>
        <span class="hljs-comment">//首先获取输入数据</span>
        JavaReceiverInputDStream&lt;String&gt; lines=jssc.socketTextStream(<span class="hljs-string">"www.codeguoj.cn"</span>,<span class="hljs-number">9999</span>);
        JavaPairDStream&lt;String,Integer&gt; categoryProductDStream=lines.mapToPair(<span class="hljs-keyword">new</span> PairFunction&lt;String, String, Integer&gt;() {
            <span class="hljs-annotation">@Override</span>
            <span class="hljs-keyword">public</span> Tuple2&lt;String, Integer&gt; <span class="hljs-title">call</span>(String s) <span class="hljs-keyword">throws</span> Exception {
                String[] prudoctSplited=s.split(<span class="hljs-string">" "</span>);

                <span class="hljs-keyword">return</span> <span class="hljs-keyword">new</span> Tuple2&lt;&gt;(prudoctSplited[<span class="hljs-number">2</span>]+<span class="hljs-string">"-"</span>+prudoctSplited[<span class="hljs-number">1</span>],<span class="hljs-number">1</span>);
            }
        });
        <span class="hljs-comment">//然后执行window</span>
        <span class="hljs-comment">//到这里，就可以做到，每隔10秒钟，对最近60秒的数据，执行reduceByKey  操作</span>
        <span class="hljs-comment">//计算出来这60秒内，每个种类的每个商品的点击次数</span>
        JavaPairDStream&lt;String,Integer&gt; categoryProductDStreamed=categoryProductDStream.reduceByKeyAndWindow(<span class="hljs-keyword">new</span> Function2&lt;Integer, Integer, Integer&gt;() {
            <span class="hljs-annotation">@Override</span>
            <span class="hljs-keyword">public</span> Integer <span class="hljs-title">call</span>(Integer v1, Integer v2) <span class="hljs-keyword">throws</span> Exception {
                <span class="hljs-keyword">return</span> v1+v2;
            }
        },Durations.seconds(<span class="hljs-number">60</span>),Durations.seconds(<span class="hljs-number">10</span>));
        <span class="hljs-comment">//然后针对60秒内的每个种类的每个商品的点击次数</span>
        categoryProductDStreamed.foreachRDD(<span class="hljs-keyword">new</span> VoidFunction&lt;JavaPairRDD&lt;String, Integer&gt;&gt;() {
            <span class="hljs-annotation">@Override</span>
            <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">call</span>(JavaPairRDD&lt;String, Integer&gt; stringIntegerJavaPairRDD) <span class="hljs-keyword">throws</span> Exception {
                JavaRDD&lt;Row&gt; rowCategoryCount=    stringIntegerJavaPairRDD.map(<span class="hljs-keyword">new</span> Function&lt;Tuple2&lt;String, Integer&gt;, Row&gt;() {
                    <span class="hljs-annotation">@Override</span>
                    <span class="hljs-keyword">public</span> Row <span class="hljs-title">call</span>(Tuple2&lt;String, Integer&gt; v1) <span class="hljs-keyword">throws</span> Exception {
                        String category=v1._1.split(<span class="hljs-string">"-"</span>)[<span class="hljs-number">0</span>];
                        String product=v1._1.split(<span class="hljs-string">"-"</span>)[<span class="hljs-number">1</span>];
                        <span class="hljs-keyword">int</span> count=v1._2;
                        <span class="hljs-keyword">return</span> RowFactory.create(category,product,count);
                    }
                });
                <span class="hljs-comment">//DataSet  转换</span>
                List&lt;StructField&gt; structFields=<span class="hljs-keyword">new</span> ArrayList&lt;&gt;();
                structFields.add(DataTypes.createStructField(<span class="hljs-string">"category"</span>,DataTypes.StringType,<span class="hljs-keyword">true</span>));
                structFields.add(DataTypes.createStructField(<span class="hljs-string">"product"</span>,DataTypes.StringType,<span class="hljs-keyword">true</span>));
                structFields.add(DataTypes.createStructField(<span class="hljs-string">"click_count"</span>,DataTypes.IntegerType,<span class="hljs-keyword">true</span>));
                StructType structType=DataTypes.createStructType(structFields);
                SQLContext sqlContext=<span class="hljs-keyword">new</span> SQLContext(rowCategoryCount.context());
                Dataset cataCountDS=sqlContext.createDataFrame(rowCategoryCount,structType);
                <span class="hljs-comment">//  将60秒内的数据创建一个零时表</span>
                cataCountDS.registerTempTable(<span class="hljs-string">"product_click_log"</span>);
                Dataset cataSearchDS=   sqlContext.sql(
                        <span class="hljs-string">"SELECT category,product,click_count "</span>
                                + <span class="hljs-string">"FROM ("</span>
                                + <span class="hljs-string">"SELECT "</span>
                                + <span class="hljs-string">"category,"</span>
                                + <span class="hljs-string">"product,"</span>
                                + <span class="hljs-string">"click_count,"</span>
                                + <span class="hljs-string">"row_number() OVER (PARTITION BY category ORDER BY click_count DESC) rank "</span>
                                + <span class="hljs-string">"FROM product_click_log"</span>
                                + <span class="hljs-string">") tmp "</span>
                                + <span class="hljs-string">"WHERE rank&lt;=3"</span>);
                cataSearchDS.show();
            }
        });
        jssc.start();
        jssc.awaitTermination();
        jssc.stop();
        jssc.close();
    }
}
</code></pre>



<h2 id="欢迎关注更多福利">欢迎关注，更多福利</h2>

<p><img src="http://ou8xokgeo.bkt.clouddn.com/201841538119494850820180415150811.jpg" alt="这里写图片描述" title=""></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>