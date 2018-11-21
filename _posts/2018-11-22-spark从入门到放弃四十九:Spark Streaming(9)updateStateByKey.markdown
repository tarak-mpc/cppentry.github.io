---
layout:     post
title:      spark从入门到放弃四十九:Spark Streaming(9)updateStateByKey
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p><strong>文章地址：<a href="http://www.haha174.top/article/details/255473" rel="nofollow">http://www.haha174.top/article/details/255473</a></strong> <br>
updateStateByKey 操作可以让我们为每个key  维护一份state  ，并持续不断的更新该state <br>
1.  首先要定义一个state  可以是任意的数据类型 <br>
2. 其次要定义state  更新指定一个函数如何使用之前的state  和新值来更新 <br>
当然 对于每个出现的key  也会执行state  更新函数。 <br>
注意 updateStateByKey  操作要求开启checkPoint <br>
下面给出java  示例和注释：</p>

<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">updateStateByKeyWorldCount</span> {</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">main</span>(String[] args) <span class="hljs-keyword">throws</span> InterruptedException {


        System.setProperty(<span class="hljs-string">"HADOOP_USER_NAME"</span>, <span class="hljs-string">"root"</span>);

        SparkConf conf=<span class="hljs-keyword">new</span> SparkConf().setMaster(<span class="hljs-string">"local[2]"</span>).setAppName(<span class="hljs-string">"updateStateByKeyWorldCount"</span>);
        JavaStreamingContext jssc=<span class="hljs-keyword">new</span> JavaStreamingContext(conf, Durations.seconds(<span class="hljs-number">5</span>));

        jssc.checkpoint(<span class="hljs-string">"hdfs://192.168.1.26:8020/study/spark/checkPoint"</span>);
        JavaReceiverInputDStream&lt;String&gt; lines=jssc.socketTextStream(<span class="hljs-string">"192.168.1.26"</span>,<span class="hljs-number">9999</span>);

        JavaDStream&lt;String&gt; worlds=lines.flatMap(<span class="hljs-keyword">new</span> FlatMapFunction&lt;String, String&gt;() {
            <span class="hljs-annotation">@Override</span>
            <span class="hljs-keyword">public</span> Iterator&lt;String&gt; <span class="hljs-title">call</span>(String s) <span class="hljs-keyword">throws</span> Exception {
                <span class="hljs-keyword">return</span> Arrays.asList(s.split(<span class="hljs-string">" "</span>)).iterator();
            }
        }) ;

        JavaPairDStream&lt;String,Integer&gt; pairs=worlds.mapToPair(<span class="hljs-keyword">new</span> PairFunction&lt;String, String, Integer&gt;() {
            <span class="hljs-annotation">@Override</span>
            <span class="hljs-keyword">public</span> Tuple2&lt;String, Integer&gt; <span class="hljs-title">call</span>(String s) <span class="hljs-keyword">throws</span> Exception {
                <span class="hljs-keyword">return</span> <span class="hljs-keyword">new</span> Tuple2(s,<span class="hljs-number">1</span>);
            }
        }) ;

        JavaPairDStream&lt;String,Integer&gt; results=pairs.updateStateByKey(<span class="hljs-keyword">new</span> Function2&lt;List&lt;Integer&gt;, Optional&lt;Integer&gt;, Optional&lt;Integer&gt;&gt;() {
            <span class="hljs-annotation">@Override</span>
            <span class="hljs-comment">//  Optional  代表值的存在状态  有可能存在有可能不存在</span>

            <span class="hljs-comment">//  实际上对于每个单词每次batch  计算的时候都会调用这个函数</span>
            <span class="hljs-comment">//  第一个参数  相当于这个batch  中这个key  新的值·</span>
            <span class="hljs-comment">// 第二个参数就是key  之前的状态</span>

            <span class="hljs-keyword">public</span> Optional&lt;Integer&gt; <span class="hljs-title">call</span>(List&lt;Integer&gt; v1, Optional&lt;Integer&gt; v2) <span class="hljs-keyword">throws</span> Exception {
                Integer newValue=<span class="hljs-number">0</span>;
                <span class="hljs-comment">//其次判断v2  是否存在</span>
                <span class="hljs-keyword">if</span>(v2.isPresent()){
                    newValue=v2.get();
                }

                <span class="hljs-comment">//  将本次新出现的值都加到newValue  中</span>
                <span class="hljs-keyword">for</span>(Integer value:v1){
                    newValue+=value;
                }
                <span class="hljs-keyword">return</span> Optional.of(newValue);
            }
        });
        <span class="hljs-comment">// 到这里为止   相当于的事  每个batch  过来  计算到pairsDstream  就会执行全局的updateStateByKey算子updateStateByKey  返回的JavaPairDStream</span>
        <span class="hljs-comment">//代表每个key  的全局计数</span>
        results.print();

        jssc.start();
        jssc.awaitTermination();
        jssc.stop();
        jssc.close();
    }
}
</code></pre>

<p>下面给出scala  示例：</p>



<pre class="prettyprint"><code class=" hljs scala"><span class="hljs-class"><span class="hljs-keyword">object</span> <span class="hljs-title">updateStateByKeyWorldCount</span> {</span>
  <span class="hljs-keyword">def</span> main(args: Array[String]): Unit = {
    System.setProperty(<span class="hljs-string">"HADOOP_USER_NAME"</span>, <span class="hljs-string">"root"</span>)

    <span class="hljs-keyword">val</span> conf = <span class="hljs-keyword">new</span> SparkConf().setAppName(<span class="hljs-string">"updateStateByKeyWorldCount"</span>).setMaster(<span class="hljs-string">"local[2]"</span>)
    <span class="hljs-comment">//创建JavaStreamingContext</span>
    <span class="hljs-comment">//该对象就类似于spark  core  中的JavaSparkContext</span>
    <span class="hljs-comment">//该对象除了接收sparkConf  对象之外还必须接收一个batch interval  参数，就是说 每收集多长时间的数据，划分一个batch  ，进行处理</span>
    <span class="hljs-comment">//这里设置一秒</span>
    <span class="hljs-keyword">val</span> jssc = <span class="hljs-keyword">new</span> StreamingContext(conf, Durations.seconds(<span class="hljs-number">5</span>))
    <span class="hljs-comment">//首先创建输入DStream   代表一个数据源 （比如kafka  ,socket）  来持续不断的实时数据流</span>
    <span class="hljs-comment">//调用JavaStreamingContext 的socketTextStream   方法可以创建一个数据源为socket  的网路端口的数据源</span>
    jssc.checkpoint(<span class="hljs-string">"hdfs://192.168.1.26:8020/study/spark/checkPoint"</span>)
    <span class="hljs-keyword">val</span> lines = jssc.socketTextStream(<span class="hljs-string">"192.168.1.26"</span>, <span class="hljs-number">9999</span>)
    <span class="hljs-keyword">val</span> worlds = lines.flatMap(line=&gt;line.split(<span class="hljs-string">" "</span>))
    <span class="hljs-keyword">val</span> pairs = worlds.map(pair=&gt;(pair,<span class="hljs-number">1</span>))
    <span class="hljs-keyword">val</span> results= pairs.updateStateByKey((values:Seq[Int],state:Option[Int])=&gt;{

      <span class="hljs-keyword">var</span> newValue:Int=state.getOrElse(<span class="hljs-number">0</span>)

      <span class="hljs-keyword">for</span>(value&lt;-values){
        newValue+= value
      }
      Option(newValue)
    })
    results.print()
    jssc.start()
    jssc.awaitTermination()
  }
}
</code></pre>

<h2 id="欢迎关注更多福利">欢迎关注，更多福利</h2>

<p><img src="http://ou8xokgeo.bkt.clouddn.com/201841538119494850820180415150811.jpg" alt="这里写图片描述" title=""></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>