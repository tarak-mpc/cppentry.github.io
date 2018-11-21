---
layout:     post
title:      实时日志收集-查询-分析系统(Flume+ElasticSearch+Kibana)
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p><strong>设计方案</strong>：Flume（日志收集） +　ElasticSearch（日志查询）+ Kibana（日志分析与展示）</p>

<p><strong>实验使用场景：</strong>通过ambari部署集群后，可以添加自己的日志系统，记录每个组件的产生的日志，实时的查询分析。</p>

<h4 id="一flume概述"><strong>一、Flume概述</strong></h4>

<p>Apache Flume is a distributed, reliable, and available system for efficiently collecting, aggregating and moving large amounts of log data from many different sources to a centralized data store.</p>

<p>The use of Apache Flume is not only restricted to log data aggregation. Since data sources are customizable, Flume can be used to transport massive quantities of event data including but not limited to network traffic data, social-media-generated data, email messages and pretty much any data source possible.</p>



<h4 id="二flume架构"><strong>二、Flume架构</strong></h4>

<p>每一个Flume agent包含<strong>三种类型</strong>的组件：source（从数据源获取生成event data），channel（接收source给put来的event data），sink（从channel取走event data）</p>

<p><em>注意上面写的是一个flume agent包含<strong>三种</strong>而不是<strong>三个</strong></em></p>

<p><img src="http://7xlhna.com1.z0.glb.clouddn.com/flume_arch.png" width="500" height="260" alt="flume-arch" align="center"></p>

<p>解释下什么是event data？</p>

<p>官方解释：A Flume event is defined as a unit of data flow having a byte payload and an optional set of string attributes. </p>

<p>简单理解：flume event data = headers + body，其中body的类型是byte[]，headers的类型是Map&lt;String，String&gt;，event代表着一个数据流的最小完整单元，如果是source是从文本文件中读数据，那event的body通常就是每行的内容，headers可以自行添加。</p>



<h4 id="三flume需要理解的内容"><strong>三、Flume需要理解的内容</strong></h4>

<ol>
<li><p>如何配好一个最简单的flume.conf，使得flume agent正常工作；</p></li>
<li><p>Flume的flow的种类和适用场景；</p></li>
<li><p>Flume的官方提供的sources，channels，sinks，如提供的不满足需求，可自定义适用于自己场景的source、channel和sink；</p></li>
</ol>



<h4 id="四elasticsearch概述"><strong>四、ElasticSearch概述</strong></h4>

<p>Elasticsearch是一个基于Apache Lucene(TM)的开源的、实时的、分布式的、全文存储、搜索、分析引擎。</p>

<p>Lucene使用起来非常复杂，ES（ElasticSearch）可以看成对其进行了封装，提供了丰富的REST API，上手非常容易。</p>



<h4 id="五elasticsearch的数据模型的简单理解"><strong>五、ElasticSearch的数据模型的简单理解</strong></h4>

<p>在Elasticsearch中，有几个概念（关键词），有别于我们使用的关系型数据库中的概念，注意类比：</p>



<pre class="prettyprint"><code class=" hljs coffeescript">Relational DB<span class="hljs-function">   -&gt;</span> Databases<span class="hljs-function">        -&gt;</span> Tables<span class="hljs-function"> -&gt;</span> Rows<span class="hljs-function">       -&gt;</span> Columns
Elasticsearch<span class="hljs-function">   -&gt;</span> Indices<span class="hljs-function"><span class="hljs-params">(Index)</span>   -&gt;</span> Types<span class="hljs-function">  -&gt;</span> Documents<span class="hljs-function">  -&gt;</span> Fields</code></pre>

<p>Elasticsearch集群可以包含多个索引(indices)（数据库），每一个索引可以包含多个类型(types)（表），每一个类型包含多个文档(documents)（行），然后每个文档包含多个字段(Fields)（列）。</p>

<p>如何定位es中的一个文档（Document）？</p>

<p>通过Index（索引: 文档存储的地方） +　Type（类型:文档代表的对象的类）+ Document_ID（唯一标识：文档的唯一标识），在ES内部的元数据表示为：_index + _type + _id。</p>



<h4 id="六kibana概述"><strong>六、Kibana概述</strong></h4>

<p>可以看成是ES的一个插件，提供的功能：</p>

<ol>
<li><p>Flexible analytics and visualization platform</p></li>
<li><p>Real-time summary and charting of streaming data</p></li>
<li><p>Intuitive interface for a variety of users</p></li>
<li><p>Instant sharing and embedding of dashboards</p></li>
</ol>



<h4 id="七系统实现"><strong>七、系统实现</strong></h4>

<p>环境：</p>



<pre class="prettyprint"><code class=" hljs lasso">（<span class="hljs-number">1</span>）JDK版本：java <span class="hljs-attribute">-version</span>
java version <span class="hljs-string">"1.7.0_75"</span>
OpenJDK Runtime Environment (rhel<span class="hljs-subst">-</span><span class="hljs-number">2.5</span><span class="hljs-number">.4</span><span class="hljs-number">.0</span><span class="hljs-built_in">.</span>el6_6<span class="hljs-attribute">-x86_64</span> u75<span class="hljs-attribute">-b13</span>)
OpenJDK <span class="hljs-number">64</span><span class="hljs-attribute">-Bit</span> Server VM (build <span class="hljs-number">24.75</span><span class="hljs-attribute">-b04</span>, mixed mode)
（<span class="hljs-number">2</span>）Flume1<span class="hljs-number">.6</span><span class="hljs-number">.0</span>
（<span class="hljs-number">3</span>）ElasticSearch1<span class="hljs-number">.7</span><span class="hljs-number">.5</span></code></pre>

<p>注意：我这里实验显示Flume1.6.0不能导数据到ES2.2.0</p>

<p><img src="http://7xlhna.com1.z0.glb.clouddn.com/log_arch.png" width="600" height="330" alt="log-arch" align="center"></p>

<p>Flume使用的conf，可以简单的设置如下：</p>



<pre class="prettyprint"><code class=" hljs avrasm">agent<span class="hljs-preprocessor">.sources</span> = yarnSrc hbaseSrc
agent<span class="hljs-preprocessor">.channels</span> = memoryChannel
agent<span class="hljs-preprocessor">.sinks</span> = elasticsearch

<span class="hljs-preprocessor"># source1:hdfsSrc</span>
agent<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.hdfsSrc</span><span class="hljs-preprocessor">.type</span> = exec
agent<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.hdfsSrc</span><span class="hljs-preprocessor">.command</span> = tail -F /var/log/tbds/hdfs/hdfs/hadoop-hdfs-datanode-<span class="hljs-number">10.151</span><span class="hljs-number">.139</span><span class="hljs-number">.111</span><span class="hljs-preprocessor">.log</span>
agent<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.hdfsSrc</span><span class="hljs-preprocessor">.channels</span> = memoryChannel

<span class="hljs-preprocessor"># source2:yarnSrc</span>
agent<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.yarnSrc</span><span class="hljs-preprocessor">.type</span> = exec
agent<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.yarnSrc</span><span class="hljs-preprocessor">.command</span> = tail -F /var/log/tbds/yarn/yarn/yarn-yarn-nodemanager-<span class="hljs-number">10.151</span><span class="hljs-number">.139</span><span class="hljs-number">.111</span><span class="hljs-preprocessor">.log</span>
agent<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.yarnSrc</span><span class="hljs-preprocessor">.channels</span> = memoryChannel

<span class="hljs-preprocessor"># source3:hbaseSrc</span>
agent<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.hbaseSrc</span><span class="hljs-preprocessor">.type</span> = exec
agent<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.hbaseSrc</span><span class="hljs-preprocessor">.command</span> = tail -F /var/log/tbds/hbase/hbase-hbase-regionserver-<span class="hljs-number">10.151</span><span class="hljs-number">.139</span><span class="hljs-number">.111</span><span class="hljs-preprocessor">.log</span>
agent<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.hbaseSrc</span><span class="hljs-preprocessor">.channels</span> = memoryChannel

<span class="hljs-preprocessor"># sink1:localSink</span>
agent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.localSink</span><span class="hljs-preprocessor">.type</span> = file_roll
agent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.localSink</span><span class="hljs-preprocessor">.sink</span><span class="hljs-preprocessor">.directory</span> = /var/log/flume
agent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.localSink</span><span class="hljs-preprocessor">.sink</span><span class="hljs-preprocessor">.rollInterval</span> = <span class="hljs-number">0</span>
agent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.localSink</span><span class="hljs-preprocessor">.channel</span> = memoryChannel

<span class="hljs-preprocessor"># sink2:esSink</span>
agent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.elasticsearch</span><span class="hljs-preprocessor">.channel</span> = memoryChannel
agent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.elasticsearch</span><span class="hljs-preprocessor">.type</span> = org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.sink</span><span class="hljs-preprocessor">.elasticsearch</span><span class="hljs-preprocessor">.ElasticSearchSink</span>
agent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.elasticsearch</span><span class="hljs-preprocessor">.hostNames</span> = <span class="hljs-number">10.151</span><span class="hljs-number">.139</span><span class="hljs-number">.111</span>:<span class="hljs-number">9300</span>

agent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.elasticsearch</span><span class="hljs-preprocessor">.indexName</span> = basis_log_info
agent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.elasticsearch</span><span class="hljs-preprocessor">.batchSize</span> = <span class="hljs-number">100</span>
agent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.elasticsearch</span><span class="hljs-preprocessor">.indexType</span> = logs
agent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.elasticsearch</span><span class="hljs-preprocessor">.clusterName</span> = my-test-es-cluster
agent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.elasticsearch</span><span class="hljs-preprocessor">.serializer</span> = org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.sink</span><span class="hljs-preprocessor">.elasticsearch</span><span class="hljs-preprocessor">.ElasticSearchLogStashEventSerializer</span>

<span class="hljs-preprocessor"># channel1</span>
agent<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.memoryChannel</span><span class="hljs-preprocessor">.type</span> = memory
agent<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.memoryChannel</span><span class="hljs-preprocessor">.capacity</span> = <span class="hljs-number">100</span></code></pre>

<p>注意要在flume/lib下加入两个包： <br>
lucene-core-4.10.4.jar <br>
elasticsearch-1.7.5.jar</p>



<pre class="prettyprint"><code class=" hljs livecodeserver">The elasticsearch <span class="hljs-operator">and</span> lucene-core jars required <span class="hljs-keyword">for</span> your environment must be placed <span class="hljs-operator">in</span> <span class="hljs-operator">the</span> lib <span class="hljs-built_in">directory</span> <span class="hljs-operator">of</span> <span class="hljs-operator">the</span> Apache Flume installation.</code></pre>

<p>之后分别运行elasticsearch和flume即可。</p>



<h4 id="八系统改进"><strong>八、系统改进</strong></h4>

<ol>
<li><p>配置flume interceptor加入各类headers，重写ElasticSearchLogStashEventSerializer使得event的header部分可以作为es的文档的field</p></li>
<li><p>memory channel与file channel的结合，参见美团日志系统的改进</p></li>
<li><p>日志若是错误信息，并不是每一行都是作为es的一个文档，而是若干行的内容才是es的一个文档的message</p></li>
</ol>

<h4 id="九系统实现效果"><strong>九、系统实现效果</strong></h4>

<p>导入es的文档数据结构：</p>

<p><img src="" width="600" height="330" alt="es-data-structure" align="center"></p>

<p>Kibana展示：</p>

<p><img src="" width="600" height="330" alt="kibana-result" align="center"></p>

<p>References：</p>

<ol>
<li><p>Apache Flume：<a href="https://flume.apache.org/" rel="nofollow">https://flume.apache.org/</a></p></li>
<li><p>elastic.co：<a href="https://www.elastic.co/" rel="nofollow">https://www.elastic.co/</a> </p></li>
<li><p>Elasticsearch权威指南（中文版）：<a href="https://www.gitbook.com/book/looly/elasticsearch-the-definitive-guide-cn/details" rel="nofollow">https://www.gitbook.com/book/looly/elasticsearch-the-definitive-guide-cn/details</a></p></li>
<li><p>Kibana ：<a href="https://www.elastic.co/products/kibana" rel="nofollow">https://www.elastic.co/products/kibana</a></p></li>
</ol>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>