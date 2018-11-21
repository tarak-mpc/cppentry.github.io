---
layout:     post
title:      Spark搭建及第一个Spark程序
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="spark">Spark</h1>



<h2 id="简介">简介</h2>

<p>spark: 一个<strong>基于内存</strong>的，<strong>分布式</strong>的大数据处理<strong>框架</strong>（<strong>计算引擎</strong>）</p>

<p>官网： <a href="http://spark.apache.org/" rel="nofollow">http://spark.apache.org/</a> </p>

<p>中文官网： <a href="http://spark.apachecn.org/docs/cn/2.2.0/" rel="nofollow">http://spark.apachecn.org/docs/cn/2.2.0/</a></p>



<h2 id="比较hadoop">比较Hadoop</h2>

<ul>
<li><p>中间结果的存储，spark优先使用内存，hadoop 使用磁盘</p></li>
<li><p>spark的容错性更高。RDD，底层实现不一样  DAG</p></li>
<li><p>spark更加通用，mapreduce只有两个API  map  reduce,</p>

<p>spark提供了更丰富的编程API map flatMap  filter 等</p></li>
<li><p>支持的语言更多， spark: scala, java, python, R </p></li>
</ul>

<p>**注意**spark并不是替代hadoop，而是替代了mapreduce编程模型，可以兼容hadoop</p>



<h2 id="优点">优点</h2>

<ul>
<li><p>速度快(基于内存)</p></li>
<li><p>易用性</p>

<blockquote>
  <p>4种语言的api，scala，python，java，R</p>
</blockquote></li>
<li><p>通用性</p>

<blockquote>
  <p>一站式解决方案， 离线处理  结构化数据处理，实时处理， mllib，图计算等</p>
</blockquote></li>
<li><p>兼容性</p>

<blockquote>
  <p>spark可以和大数据生态圈中的一些组件无缝对接</p>
</blockquote></li>
</ul>



<h2 id="四种部署模式">四种部署模式</h2>

<ul>
<li>Local模式</li>
<li>standalone模式</li>
<li>Spark on yarn</li>
<li>Spark on mesos(mesos也是一个资源调度平台,同yarn )</li>
</ul>



<h2 id="安装">安装</h2>



<pre class="prettyprint"><code class="language-shell hljs coffeescript"><span class="hljs-comment"># 解压</span>
tar -zxvf spark-<span class="hljs-number">2.2</span><span class="hljs-number">.0</span>-bin-hadoop2<span class="hljs-number">.7</span>
<span class="hljs-comment">######</span><span class="hljs-comment">######</span><span class="hljs-comment">######</span><span class="hljs-comment">######</span><span class="hljs-comment">######</span><span class="hljs-comment">######</span><span class="hljs-comment">######</span><span class="hljs-comment">######</span><span class="hljs-comment">######</span><span class="hljs-comment">######</span><span class="hljs-comment">######</span><span class="hljs-comment">######</span><span class="hljs-comment">#</span>
<span class="hljs-comment"># 修改配置文件</span>
cd spark-<span class="hljs-number">2.2</span><span class="hljs-number">.0</span>-bin-hadoop2<span class="hljs-number">.7</span>/conf
mv spark-env.sh.template spark-env.sh
vi spark-env.sh
<span class="hljs-comment">#添加变量信息</span>
<span class="hljs-reserved">export</span> JAVA_HOME=/usr/local/jdk
<span class="hljs-comment">#指定spark-master 所在主机</span>
<span class="hljs-reserved">export</span> SPARK_MASTER_HOST=linux11
<span class="hljs-comment">#指定spark-master 所在主机rpc端口,默认为7077</span>
<span class="hljs-reserved">export</span> SPARK_MASTER_HOST=<span class="hljs-number">7077</span>
<span class="hljs-comment">######</span><span class="hljs-comment">######</span><span class="hljs-comment">######</span><span class="hljs-comment">######</span><span class="hljs-comment">######</span><span class="hljs-comment">######</span><span class="hljs-comment">######</span><span class="hljs-comment">######</span><span class="hljs-comment">######</span><span class="hljs-comment">######</span><span class="hljs-comment">######</span><span class="hljs-comment">######</span><span class="hljs-comment">##</span>
<span class="hljs-comment">#指定woker所在主机,方便使用脚本sbin/start-all.sh启动集群</span>
mv slaves.template slaves
vi slaves    <span class="hljs-comment">#确保删除里面所有内容</span>
linux12      
linux13     <span class="hljs-comment">#三台worker主机</span>
linux14
<span class="hljs-comment">######</span><span class="hljs-comment">######</span><span class="hljs-comment">######</span><span class="hljs-comment">######</span><span class="hljs-comment">######</span><span class="hljs-comment">######</span><span class="hljs-comment">######</span><span class="hljs-comment">######</span><span class="hljs-comment">######</span><span class="hljs-comment">######</span><span class="hljs-comment">######</span><span class="hljs-comment">######</span>
<span class="hljs-comment">#将配置同步到其他几台服务器</span>
<span class="hljs-keyword">for</span> i <span class="hljs-keyword">in</span> <span class="hljs-number">2</span> <span class="hljs-number">3</span> <span class="hljs-number">4</span> ;
<span class="hljs-keyword">do</span> 
scp -r spark-<span class="hljs-number">2.2</span><span class="hljs-number">.0</span>-bin-hadoop2<span class="hljs-number">.7</span>/ <span class="hljs-attribute">linux1$i</span>:$PWD ;
done</code></pre>



<h2 id="启动并验证">启动并验证</h2>

<ul>
<li><p>第一种(单个启动)</p>

<pre class="prettyprint"><code class="language-shell hljs vala">
<span class="hljs-preprocessor">#启动master</span>

./sbin/start-master.sh

<span class="hljs-preprocessor">#启动slaves</span>

./sbin/start-slaves.sh</code></pre></li>
<li><p>第二种(一键启动)</p>

<pre class="prettyprint"><code class="language-shell hljs sql">./sbin/<span class="hljs-operator"><span class="hljs-keyword">start</span>-<span class="hljs-keyword">all</span>.sh</span></code></pre>

<p>访问:<a href="http://linux11:8080" rel="nofollow">http://linux11:8080</a> 查看页面验证</p></li>
</ul>



<h2 id="提交spark任务命令">提交spark任务命令</h2>



<pre class="prettyprint"><code class="language-shell hljs vala"><span class="hljs-preprocessor">#第一种(练习使用,交互式命令)</span>
./sbin/spark-shell --master spark:<span class="hljs-comment">//linux11:7077</span>
<span class="hljs-preprocessor">#第二种</span>
./sbin/spark-submit --master spark:<span class="hljs-comment">//linux11:7077 --class com.xxx.Main /root/xxx.jar  args</span></code></pre>

<blockquote>
  <p>官网测试程序example,计算圆周率</p>
  
  <p>./sbin/spark-submit –master spark://linux11:7077 –class  org.apache.spark.examples.SparkPi /root/apps/spark-2.2.0-bin-hadoop2.7/examples/jars/spark-examples_2.11-2.2.0.jar 1000 </p>
</blockquote>

<h2 id="第一个spark程序wordcount">第一个Spark程序(wordcount)</h2>



<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-javadoc">/**
*  统计词频
*/</span>
<span class="hljs-class"><span class="hljs-keyword">object</span> <span class="hljs-title">WordCount</span>{</span>

    <span class="hljs-keyword">def</span> main(args:Array[String]){
        <span class="hljs-keyword">if</span> (args.length != <span class="hljs-number">2</span>) {
              println(<span class="hljs-string">"Usage : ScalaWordCount &lt;input&gt; &lt;output&gt;"</span>)
              sys.exit(<span class="hljs-number">1</span>)
        }
        <span class="hljs-keyword">val</span> Array(input, output) = args
        <span class="hljs-keyword">val</span> conf = <span class="hljs-keyword">new</span> SparkConfig()
        <span class="hljs-comment">//.setMaster("local[*]")      //local模式下使用</span>
        <span class="hljs-comment">//.setAppName(this.getClass.getSimpleName)</span>

        <span class="hljs-comment">//创建sprkcontext实例</span>
        <span class="hljs-keyword">val</span> sc:SparkContext = <span class="hljs-keyword">new</span> SparkContext(conf)
        <span class="hljs-comment">//获取文件内容</span>
        <span class="hljs-keyword">val</span> file: RDD[String] = sc.textFile(input)
        <span class="hljs-keyword">val</span> splitedrdd: RDD[String] = file.flatMap(_.split(<span class="hljs-string">" "</span>))
          <span class="hljs-comment">// 组装</span>
        <span class="hljs-keyword">val</span> wordWithOne: RDD[(String, Int)] = splitedrdd.map((_, <span class="hljs-number">1</span>))
        <span class="hljs-comment">//统计词频</span>
        <span class="hljs-keyword">val</span> result: RDD[(String, Int)] = wordWithOne.reduceByKey(_ + _)
        <span class="hljs-comment">// 排序</span>
        <span class="hljs-keyword">val</span> finalRes: RDD[(String, Int)] = result.sortBy(-_._2)

        <span class="hljs-comment">// 把结果数据写入到hdfs中</span>
        finalRes.saveAsTextFile(output)
        <span class="hljs-comment">//关闭资源</span>
        sc.stop()

    }
}</code></pre>



<pre class="prettyprint"><code class="language-shell hljs ruby"><span class="hljs-comment">#上传到集群上面运行</span>
spark-submit --master <span class="hljs-symbol">spark:</span>/<span class="hljs-regexp">/linux11:7077 --class com.test.WordCount /root</span><span class="hljs-regexp">/wordcount.jar  hdfs:/</span><span class="hljs-regexp">/linux11:9000/wordcount</span><span class="hljs-regexp">/input  hdfs:/</span><span class="hljs-regexp">/linux11:9000/wordcount</span><span class="hljs-regexp">/output</span></code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>