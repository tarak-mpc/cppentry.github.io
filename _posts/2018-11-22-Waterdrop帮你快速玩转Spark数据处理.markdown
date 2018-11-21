---
layout:     post
title:      Waterdrop帮你快速玩转Spark数据处理
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="waterdrop帮你快速玩转spark数据处理">Waterdrop帮你快速玩转Spark数据处理</h1>

<blockquote>
  <p>屠龙宝刀，宝刀屠龙，踏遍天下，谁敢不从，倚天不出，谁与争锋！</p>
</blockquote>

<p><img src="https://github.com/garyelephant/blog/raw/master/images/waterdrop-release/logo.png" alt="waterdrop-logo" title=""></p>

<p>Databricks 开源的 Apache Spark 对于分布式数据处理来说是一个伟大的进步。我们在使用 Spark 时发现了很多可圈可点之处，我们在此与大家分享一下我们在简化Spark使用和编程以及加快Spark在生产环境落地上做的一些努力。</p>

<h2 id="一个spark-streaming读取kafka的案例">一个Spark Streaming读取Kafka的案例</h2>

<p>以一个线上案例为例，介绍如何使用Spark Streaming统计Nginx后端日志中每个域名下每个状态码每分钟出现的次数，并将结果数据输出到外部数据源Elasticsearch中。其中原始数据已经通过Rsyslog传输到了Kafka中。</p>



<h3 id="数据读取">数据读取</h3>

<p>从Kafka中每隔一段时间读取数据，生成DStream</p>



<pre class="prettyprint"><code class="language-Scala hljs vbnet">val directKafkaStream = KafkaUtils.createDirectStream[
     [<span class="hljs-keyword">key</span> <span class="hljs-keyword">class</span>], [value <span class="hljs-keyword">class</span>], [<span class="hljs-keyword">key</span> decoder <span class="hljs-keyword">class</span>], [value decoder <span class="hljs-keyword">class</span>] ](
     streamingContext, [map <span class="hljs-keyword">of</span> Kafka parameters], [<span class="hljs-keyword">set</span> <span class="hljs-keyword">of</span> topics <span class="hljs-keyword">to</span> consume])</code></pre>

<p>具体方法参考<a href="http://spark.apache.org/docs/latest/streaming-kafka-0-8-integration.html" rel="nofollow">Spark Streaming + Kafka Integration Guide</a></p>



<h3 id="数据清洗">数据清洗</h3>

<p>日志案例</p>

<pre><code>192.168.0.1 interestinglab.github.io 127.0.0.1 0.001s [22/Feb/2018:22:12:15 +0800] "GET /waterdrop HTTP/1.1" 200 8938 "http://github.com/" - "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/55.0.2883.87 Safari/537.36"
</code></pre>

<p>通过<code>Split</code>方法从非结构化的原始数据<code>message</code>中获取域名以及状态码字段，并组成方便聚合的结构化数据格式<em>Map(key -&gt; value)</em></p>



<pre class="prettyprint"><code class="language-Scala hljs fsharp"><span class="hljs-keyword">val</span> splitList = message.split(<span class="hljs-string">" "</span>)
<span class="hljs-keyword">val</span> domain = splitList(<span class="hljs-number">1</span>)
<span class="hljs-keyword">val</span> httpCode = splitList(<span class="hljs-number">9</span>)
<span class="hljs-keyword">val</span> item = Map((domain, httpCode) -&gt; <span class="hljs-number">1</span>L)</code></pre>



<h3 id="数据聚合">数据聚合</h3>

<p>利用Spark提供的<code>reduceByKey</code>方法对数据进行聚合计算，统计每分钟每个域名下的每个错误码出现的次数，其中<code>mapRdd</code>是在清洗数据阶段组成的RDD</p>



<pre class="prettyprint"><code class="language-Scala hljs coffeescript">val reduceRdd = mapRdd.reduceByKey<span class="hljs-function"><span class="hljs-params">((a:Long, b:Long) =&gt; (a + b))</span></span></code></pre>



<h3 id="数据输出">数据输出</h3>

<p>利用Spark提供的<code>foreachRDD</code>方法将结果数据<code>reduceRdd</code>输出到外部数据源Elasticsearch</p>



<pre class="prettyprint"><code class="language-Scala hljs avrasm">reduceRdd<span class="hljs-preprocessor">.foreachRDD</span>(rdd =&gt; {
    rdd<span class="hljs-preprocessor">.saveToEs</span>(<span class="hljs-string">"es_index"</span> + <span class="hljs-string">"/es_type"</span>, esCfg)
})</code></pre>



<h3 id="总结">总结</h3>

<p>我们的确可以利用Spark提供的API对数据进行任意处理，但是整套逻辑的开发是个不小的工程，需要一定的Spark基础以及使用经验才能开发出稳定高效的Spark代码。除此之外，项目的编译、打包、部署以及测试都比较繁琐，会带来不少得时间成本和学习成本。</p>

<p>除了开发方面的问题，数据处理时可能还会遇到以下不可逃避的麻烦：</p>

<ul>
<li><p>数据丢失与重复</p></li>
<li><p>任务堆积与延迟</p></li>
<li><p>吞吐量低</p></li>
<li><p>应用到生产环境周期长</p></li>
<li><p>缺少应用运行状态监控</p></li>
</ul>

<p>因此我们开始尝试更加简单高效的Spark方案，并试着解决以上问题</p>



<h2 id="一种简单高效的方式-waterdrop">一种简单高效的方式 – Waterdrop</h2>

<p>Waterdrop 是一个<code>非常易用</code>，<code>高性能</code>，能够应对<code>海量数据</code>的<code>实时</code>数据处理产品，构建于Apache Spark之上。</p>

<p>Waterdrop 项目地址：<a href="https://interestinglab.github.io/waterdrop" rel="nofollow">https://interestinglab.github.io/waterdrop</a></p>

<p>Spark固然是一个优秀的分布式数据处理工具，但是正如上文所表达的，Spark在我们的日常使用中还是存在不小的问题。因此我们也发现了我们的机会 —— 通过我们的努力让Spark的使用更简单，更高效，并将业界和我们使用Spark的优质经验固化到Waterdrop这个产品中，明显减少学习成本，加快分布式数据处理能力在生产环境落地</p>

<p>“Waterdrop” 的中文是“水滴”，来自中国当代科幻小说作家刘慈欣的《三体》系列，它是三体人制造的宇宙探测器，会反射几乎全部的电磁波， <br>
表面绝对光滑，温度处于绝对零度，全部由被强互作用力紧密锁死的质子与中子构成，无坚不摧。 <br>
在末日之战中，仅一个水滴就摧毁了人类太空武装力量近2千艘战舰。</p>



<h2 id="waterdrop-的特性">Waterdrop 的特性</h2>

<ul>
<li><p>简单易用，灵活配置，无需开发；可运行在单机、Spark Standalone集群、Yarn集群、Mesos集群之上。</p></li>
<li><p>实时流式处理, 高性能, 海量数据处理能力</p></li>
<li><p>模块化和插件化，易于扩展。Waterdrop的用户可根据实际的需要来扩展需要的插件，支持Java/Scala实现的Input、Filter、Output插件。 <br>
如果您对插件扩展感兴趣，可移步至<a href="https://interestinglab.github.io/waterdrop/#/zh-cn/developing-plugin" rel="nofollow">Waterdrop插件开发</a></p></li>
<li><p>支持利用SQL做数据处理和聚合</p></li>
</ul>



<h2 id="waterdrop-的原理和工作流程">Waterdrop 的原理和工作流程</h2>

<p><img src="https://github.com/garyelephant/blog/raw/master/images/waterdrop-release/internal_1.png" alt="waterdrop internal" title=""></p>

<p>Waterdrop 利用了Spark的Streaming, SQL, DataFrame等技术，Java的反射机制、Service Loader等技术以及Antlr4的语法解析技术， <br>
实现了一套完整的可插拔的数据处理工作流，如下：</p>

<p><img src="https://github.com/garyelephant/blog/raw/master/images/waterdrop-release/pipeline.png" alt="waterdrop pipeline" title=""></p>

<p>多个Filter构建了数据处理的Pipeline，满足各种各样的数据处理需求，如果您熟悉SQL，也可以直接通过SQL构建数据处理的Pipeline，简单高效。 <br>
目前Waterdrop支持的Filter列表(数据处理插件), 仍然在不断扩充中。 <br>
您也可以开发自己的数据处理插件，整个系统是易于扩展的。通过下面的配置示例，你可以快速了解到这种工作流程：</p>

<pre class="prettyprint"><code class=" hljs avrasm">spark {
  <span class="hljs-preprocessor"># Waterdrop defined streaming batch duration in seconds</span>
  spark<span class="hljs-preprocessor">.streaming</span><span class="hljs-preprocessor">.batchDuration</span> = <span class="hljs-number">5</span>

  spark<span class="hljs-preprocessor">.app</span><span class="hljs-preprocessor">.name</span> = <span class="hljs-string">"Waterdrop"</span>
  spark<span class="hljs-preprocessor">.ui</span><span class="hljs-preprocessor">.port</span> = <span class="hljs-number">13000</span>
}

input {
  socket {}
}

filter {
  split {
    fields = [<span class="hljs-string">"msg"</span>, <span class="hljs-string">"name"</span>]
    delimiter = <span class="hljs-string">","</span>
  }
}

output {
  stdout {}
}</code></pre>

<ul>
<li><code>spark</code>是spark相关的配置，</li>
</ul>

<p>可配置的spark参数见： <br>
<a href="https://spark.apache.org/docs/latest/configuration.html#available-properties" rel="nofollow">Spark Configuration</a>, <br>
其中master, deploy-mode两个参数不能在这里配置，需要在Waterdrop启动脚本中指定。</p>

<ul>
<li><p><code>input</code>可配置任意的input插件及其参数，具体参数随不同的input插件而变化。input支持包括<code>File</code>, <code>Hdfs</code>, <code>Kafka</code>, <code>S3</code>, <code>Socket</code>等插件。</p></li>
<li><p><code>filter</code>可配置任意的filter插件及其参数，具体参数随不同的filter插件而变化。filter支持包括<code>Date</code>, <code>Json</code>, <code>Split</code>, <code>Sql</code>, <code>Table</code>, <code>Repartition</code>等40+个插件。</p></li>
</ul>

<p>filter中的多个插件按配置顺序形成了数据处理的pipeline, 上一个filter的输出是下一个filter的输入。</p>

<ul>
<li><code>output</code>可配置任意的output插件及其参数，具体参数随不同的output插件而变化。</li>
</ul>

<p><code>filter</code>处理完的数据，会发送给<code>output</code>中配置的每个插件。output支持包括<code>Elasticsearch</code>, <code>File</code>, <code>Hdfs</code>, <code>Jdbc</code>, <code>Kafka</code>, <code>Mysql</code>, <code>S3</code>等插件。</p>



<h2 id="如何使用-waterdrop">如何使用 Waterdrop</h2>

<p>Step 1 : 使用 Waterdrop前请先准备好Spark和Java运行环境。</p>

<p>Step 2 : 下载<a href="https://github.com/InterestingLab/waterdrop/releases" rel="nofollow">Waterdrop安装包</a> 并解压:</p>



<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-preprocessor"># 以waterdrop 1.0.2为例:</span>
wget https://github<span class="hljs-preprocessor">.com</span>/InterestingLab/waterdrop/releases/download/v1<span class="hljs-number">.0</span><span class="hljs-number">.2</span>/waterdrop-<span class="hljs-number">1.0</span><span class="hljs-number">.2</span><span class="hljs-preprocessor">.zip</span> -O waterdrop-<span class="hljs-number">1.0</span><span class="hljs-number">.2</span><span class="hljs-preprocessor">.zip</span>
unzip waterdrop-<span class="hljs-number">1.0</span><span class="hljs-number">.2</span><span class="hljs-preprocessor">.zip</span>
ln -s waterdrop-<span class="hljs-number">1.0</span><span class="hljs-number">.2</span> waterdrop
cd waterdrop</code></pre>

<p>Step 3 : 配置 Waterdrop(从kafka消费数据，做字符串分割，输出到终端), 编辑 <code>config/application.conf</code></p>



<pre class="prettyprint"><code class=" hljs avrasm">spark {
  <span class="hljs-preprocessor"># Waterdrop defined streaming batch duration in seconds</span>
  spark<span class="hljs-preprocessor">.streaming</span><span class="hljs-preprocessor">.batchDuration</span> = <span class="hljs-number">5</span>

  spark<span class="hljs-preprocessor">.app</span><span class="hljs-preprocessor">.name</span> = <span class="hljs-string">"Waterdrop"</span>
  spark<span class="hljs-preprocessor">.ui</span><span class="hljs-preprocessor">.port</span> = <span class="hljs-number">13000</span>
  spark<span class="hljs-preprocessor">.executor</span><span class="hljs-preprocessor">.instances</span> = <span class="hljs-number">2</span>
  spark<span class="hljs-preprocessor">.executor</span><span class="hljs-preprocessor">.cores</span> = <span class="hljs-number">1</span>
  spark<span class="hljs-preprocessor">.executor</span><span class="hljs-preprocessor">.memory</span> = <span class="hljs-string">"1g"</span>
}

input {
  kafka {
    topics = <span class="hljs-string">"mytopic"</span>
    consumer<span class="hljs-preprocessor">.bootstrap</span><span class="hljs-preprocessor">.servers</span> = <span class="hljs-string">"localhost:9092"</span>
    consumer<span class="hljs-preprocessor">.zookeeper</span><span class="hljs-preprocessor">.connect</span> = <span class="hljs-string">"localhost:2181"</span>
    consumer<span class="hljs-preprocessor">.group</span><span class="hljs-preprocessor">.id</span> = <span class="hljs-string">"waterdrop_group"</span>
  }
}

filter {
  split {
    fields = [<span class="hljs-string">"msg"</span>, <span class="hljs-string">"name"</span>]
    delimiter = <span class="hljs-string">","</span>
  }
}

output {
  stdout {}
}</code></pre>

<p>Step 4 : 启动 Waterdrop</p>



<pre class="prettyprint"><code class=" hljs brainfuck"><span class="hljs-string">.</span><span class="hljs-comment">/bin/start</span><span class="hljs-literal">-</span><span class="hljs-comment">waterdrop</span><span class="hljs-string">.</span><span class="hljs-comment">sh</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">master</span> <span class="hljs-comment">yarn</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">deploy</span><span class="hljs-literal">-</span><span class="hljs-comment">mode</span> <span class="hljs-comment">client</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">config</span> <span class="hljs-string">.</span><span class="hljs-comment">/config/application</span><span class="hljs-string">.</span><span class="hljs-comment">conf</span></code></pre>

<p>更详细的使用方法见<a href="https://interestinglab.github.io/waterdrop/#/zh-cn/quick-start" rel="nofollow">Waterdrop Quick Start</a></p>



<h2 id="waterdrop-未来发展路线">Waterdrop 未来发展路线</h2>

<p>Waterdrop 会分为3条路线，详细展开：</p>

<p>（1）提供更多Input, Filter, Output插件，提高易用性、可靠性、数据一致性。</p>

<p>（2）支持Apache Flink / Apache Beam，支持Spark以外的分布式数据计算模型。</p>

<p>（3）支持流式机器学习，能够通过简单的Pipeline和配置，完成常用流式机器学习模型的训练。</p>

<p>Waterdrop 项目地址：<a href="https://interestinglab.github.io/waterdrop" rel="nofollow">https://interestinglab.github.io/waterdrop</a></p>

<hr>

<p>Waterdrop 项目由<strong>Interesting Lab</strong>开源。Interesting Lab （<a href="https://github.com/InterestingLab" rel="nofollow">https://github.com/InterestingLab</a>）, 中文译名有趣实验室。成立于2016年，致力于让大数据变得更简单有价值。</p>

<p>原文地址：<a href="https://github.com/garyelephant/blog/blob/master/waterdrop-release.md" rel="nofollow">https://github.com/garyelephant/blog/blob/master/waterdrop-release.md</a></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>