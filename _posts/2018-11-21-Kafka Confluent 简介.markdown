---
layout:     post
title:      Kafka Confluent 简介
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许随意转载。					https://blog.csdn.net/xianzhen376/article/details/51281420				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="简介">简介</h1>



<h2 id="基本模块">基本模块</h2>

<p><img src="https://img-blog.csdn.net/20160429150859300" alt="这里写图片描述" title=""></p>

<ul>
<li><p>Apache Kafka <br>
消息分发组件，数据采集后先入Kafka。</p></li>
<li><p>Schema Registry <br>
Schema管理服务，消息出入kafka、入hdfs时，给数据做序列化/反序列化处理。</p></li>
<li><p>Kafka Connect <br>
提供kafka到其他存储的管道服务，此次焦点是从kafka到hdfs，并建立相关HIVE表。</p></li>
<li><p>Kafka Rest Proxy <br>
提供kafka的Rest API服务。</p></li>
<li><p>Kafka Clients <br>
提供Client编程所需SDK。</p></li>
</ul>

<p>说明：以上服务除Apache kafka由Linkedin始创并开源，其他组件皆由Confluent公司开发并开源。上图解决方案由confluent提供。</p>



<h2 id="基本逻辑步骤">基本逻辑步骤</h2>

<ul>
<li>数据通过Kafka Rest/Kafka Client写入Kafka；</li>
<li>kafka Connect任务作为consumer从kafka订阅数据；</li>
<li>kafka Connect任务建立HIVE表和hdfs文件的映射关系；</li>
<li>kafka connect任务收到数据后，以指定格式，写入指定hdfs目录;</li>
</ul>

<h1 id="实操">实操：</h1>



<h3 id="1-启动服务">1. 启动服务</h3>

<h4 id="启动kafka-服务">启动kafka 服务</h4>

<ul>
<li>修改配置</li>
</ul>



<pre class="prettyprint"><code class=" hljs axapta"><span class="hljs-comment">/*
1.修改192.168.103.44、192.168.103.45、192.168.103.46三台服务器上配置
2.配置文件中broker.id值分别修改为0、1、2
*/</span>

cd  /home/ubuntu/confluent-<span class="hljs-number">2.0</span><span class="hljs-number">.0</span>
vi etc/kafka/<span class="hljs-keyword">server</span>.properties</code></pre>

<ul>
<li>命令行启动</li>
</ul>



<pre class="prettyprint"><code class=" hljs axapta">cd  /home/ubuntu/confluent-<span class="hljs-number">2.0</span><span class="hljs-number">.0</span>
nohup bin/kafka-<span class="hljs-keyword">server</span>-start etc/kafka/<span class="hljs-keyword">server</span>.properties &amp;</code></pre>

<ul>
<li>服务说明 <br>
kafka服务无Leader概念，服务访问端口为9092</li>
</ul>

<hr>



<h4 id="启动schema-register服务">启动Schema Register服务</h4>

<ul>
<li>命令行启动</li>
</ul>



<pre class="prettyprint"><code class=" hljs lasso"><span class="hljs-comment">//wonderwoman集群环境，只在woderwoman上启动了服务</span>
cd  /home/ubuntu/confluent<span class="hljs-subst">-</span><span class="hljs-number">2.0</span><span class="hljs-number">.0</span>
nohup bin/schema<span class="hljs-attribute">-registry</span><span class="hljs-attribute">-start</span> etc/schema<span class="hljs-attribute">-registry</span>/schema<span class="hljs-attribute">-registry</span><span class="hljs-built_in">.</span>properties <span class="hljs-subst">&amp;</span></code></pre>

<ul>
<li>服务说明 <br>
Schema Register服务端口为8081</li>
</ul>

<hr>



<h4 id="启动kafka-rest-api服务">启动Kafka Rest API服务</h4>

<ul>
<li>修改配置</li>
</ul>



<pre class="prettyprint"><code class=" hljs lasso"><span class="hljs-comment">// 修改schema服务器地址和zookeeper服务器地址</span>
cd  /home/ubuntu/confluent<span class="hljs-subst">-</span><span class="hljs-number">2.0</span><span class="hljs-number">.0</span>
vi etc/kafka<span class="hljs-attribute">-rest</span>/kafka<span class="hljs-attribute">-rest</span><span class="hljs-built_in">.</span>properties</code></pre>

<ul>
<li>命令行启动</li>
</ul>



<pre class="prettyprint"><code class=" hljs lasso"><span class="hljs-comment">//wonderwoman集群环境，只在woderwoman上启动了服务</span>
cd  /home/ubuntu/confluent<span class="hljs-subst">-</span><span class="hljs-number">2.0</span><span class="hljs-number">.0</span>
nohup bin/kafka<span class="hljs-attribute">-rest</span><span class="hljs-attribute">-start</span> <span class="hljs-built_in">.</span>/etc/kafka<span class="hljs-attribute">-rest</span>/kafka<span class="hljs-attribute">-rest</span><span class="hljs-built_in">.</span>properties <span class="hljs-subst">&amp;</span></code></pre>

<ul>
<li>服务说明 <br>
Schema Register服务端口为8082</li>
</ul>

<hr>



<h4 id="启动kafka-connect服务">启动Kafka connect服务</h4>

<ul>
<li>修改配置</li>
</ul>



<pre class="prettyprint"><code class=" hljs lasso"><span class="hljs-comment">/*
修改如下配置项:
1.bootstrap.servers, 所有kafka broker的地址
2.group.id 标志connect集群，集群内配一致
3.key.converter.schema.registry.url，schema服务地址
4.value.converter.schema.registry.url，schema服务地址
*/</span>
cd /home/ubuntu/confluent<span class="hljs-subst">-</span><span class="hljs-number">2.0</span><span class="hljs-number">.0</span><span class="hljs-subst">/</span>
vi etc/schema<span class="hljs-attribute">-registry</span>/connect<span class="hljs-attribute">-avro</span><span class="hljs-attribute">-distributed</span><span class="hljs-built_in">.</span>properties</code></pre>

<ul>
<li>命令行启动</li>
</ul>



<pre class="prettyprint"><code class=" hljs lasso"><span class="hljs-comment">//启动分布式connect，在zookeeper集群内所有机器上启动</span>
cd /home/ubuntu/confluent<span class="hljs-subst">-</span><span class="hljs-number">2.0</span><span class="hljs-number">.0</span><span class="hljs-subst">/</span>
nohup bin/connect<span class="hljs-attribute">-distributed</span> etc/schema<span class="hljs-attribute">-registry</span>/connect<span class="hljs-attribute">-avro</span><span class="hljs-attribute">-distributed</span><span class="hljs-built_in">.</span>properties <span class="hljs-subst">&gt;</span> /tmp/<span class="hljs-number">1.</span><span class="hljs-keyword">log</span>
</code></pre>



<h3 id="2-创建schema">2. 创建schema</h3>

<ul>
<li>Rest API方式 <br>
<ul><li>准备shema的定义文件，例如myschema.json</li>
<li>注册topic和schema，返回schema的ID</li></ul></li>
</ul>

<pre class="prettyprint"><code class=" hljs lasso">curl <span class="hljs-attribute">-X</span> POST <span class="hljs-attribute">-i</span> <span class="hljs-attribute">-H</span> <span class="hljs-string">"Content-Type: application/vnd.schemaregistry.v1+json"</span> <span class="hljs-subst">--</span><span class="hljs-built_in">data</span> @myschema<span class="hljs-built_in">.</span>json http:<span class="hljs-comment">//localhost:8081/subjects/20160427/versions</span>
</code></pre>

<ul>
<li>Kafka Client方式 <br>
详见 Kafka Client提供的接口，支持java/c/c++等语言</li>
</ul>



<h3 id="3-创建topic">3. 创建Topic</h3>

<p>创建的topic名称为xianzhen，3个分区，每分区数据存两份</p>

<ul>
<li>命令行方式</li>
</ul>

<pre class="prettyprint"><code class=" hljs brainfuck"><span class="hljs-comment">cd</span> <span class="hljs-comment">/home/ubuntu/confluent</span><span class="hljs-literal">-</span><span class="hljs-comment">2</span><span class="hljs-string">.</span><span class="hljs-comment">0</span><span class="hljs-string">.</span><span class="hljs-comment">0/</span>
<span class="hljs-string">.</span><span class="hljs-comment">/bin/kafka</span><span class="hljs-literal">-</span><span class="hljs-comment">topics</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">create</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">topic</span> <span class="hljs-comment">xianzhen</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">partitions</span> <span class="hljs-comment">3</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">replication</span><span class="hljs-literal">-</span><span class="hljs-comment">factor</span> <span class="hljs-comment">2</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">zookeeper</span> <span class="hljs-comment">192</span><span class="hljs-string">.</span><span class="hljs-comment">168</span><span class="hljs-string">.</span><span class="hljs-comment">103</span><span class="hljs-string">.</span><span class="hljs-comment">44:2181</span></code></pre>

<ul>
<li><p>Rest API方式 <br>
略，后续补充</p></li>
<li><p>Kafka Client方式 <br>
详见 Kafka Client提供的接口，支持java/c/c++等语言</p></li>
</ul>



<h3 id="4-创建connector">4. 创建Connector</h3>

<p>分布式只支持以REST API的方式提交Connector作业</p>

<ul>
<li>Rest API方式 <br>
<ul><li>创建配置文件</li></ul></li>
</ul>

<pre class="prettyprint"><code class=" hljs lasso">vi confluentHive<span class="hljs-attribute">-connector</span><span class="hljs-built_in">.</span>json
{
    <span class="hljs-string">"name"</span>: <span class="hljs-string">"hive-final"</span>,
    <span class="hljs-string">"config"</span>: {
        <span class="hljs-string">"connector.class"</span>: <span class="hljs-string">"io.confluent.connect.hdfs.HdfsSinkConnector"</span>,
        <span class="hljs-string">"tasks.max"</span>: <span class="hljs-string">"6"</span>,
        <span class="hljs-string">"topics"</span>: <span class="hljs-string">"confluentHive"</span>,
        <span class="hljs-string">"hdfs.url"</span>: <span class="hljs-string">"hdfs://ns1"</span>,
        <span class="hljs-string">"hadoop.conf.dir"</span>: <span class="hljs-string">"/home/ubuntu/hadoop-2.7.1/etc/hadoop"</span>,
        <span class="hljs-string">"hadoop.home"</span>: <span class="hljs-string">"/home/ubuntu/hadoop-2.7.1"</span>,
        <span class="hljs-string">"flush.size"</span>: <span class="hljs-string">"100"</span>,
        <span class="hljs-string">"rotate.interval.ms"</span>: <span class="hljs-string">"1000"</span>,
        <span class="hljs-string">"hive.integration"</span>:<span class="hljs-literal">true</span>,
        <span class="hljs-string">"hive.metastore.uris"</span>:<span class="hljs-string">"thrift://192.168.103.44:9083"</span>,
        <span class="hljs-string">"schema.compatibility"</span>:<span class="hljs-string">"BACKWARD"</span>,
        <span class="hljs-string">"hive.home"</span>:<span class="hljs-string">"/home/ubuntu/apache-hive-1.2.1-bin"</span>,
        <span class="hljs-string">"hive.conf.dir"</span>:<span class="hljs-string">"/home/ubuntu/spark-1.6.1/conf"</span>
    }
}</code></pre>

<ul>
<li>部分配置说明</li>
</ul>

<table>
<thead>
<tr>
  <th align="left">配置项</th>
  <th align="left">说明</th>
</tr>
</thead>
<tbody><tr>
  <td align="left">name</td>
  <td align="left">Connector名称，会对应kafka consumer Group的名称</td>
</tr>
<tr>
  <td align="left">connector.class</td>
  <td align="left">Connector实现类，连接hdfs时，配置见例</td>
</tr>
<tr>
  <td align="left">tasks.max</td>
  <td align="left">最大任务数量，对应于处理的线程数量，不是越多就越快，受kafka分区数限制</td>
</tr>
<tr>
  <td align="left">topics</td>
  <td align="left">订阅的kafka topic名称，也对应于最终HIVE的表名</td>
</tr>
<tr>
  <td align="left">hdfs.url</td>
  <td align="left">hdfs的地址，和hadoop的core-site.xml中配置对应上</td>
</tr>
<tr>
  <td align="left">hadoop.home</td>
  <td align="left">hadoop的目录</td>
</tr>
<tr>
  <td align="left">hadoop.conf.dir</td>
  <td align="left">hadoop的配置目录</td>
</tr>
<tr>
  <td align="left">hive.integration</td>
  <td align="left">是否写入HIVE表</td>
</tr>
<tr>
  <td align="left">hive.metastore.uris</td>
  <td align="left">hive Metastore服务地址</td>
</tr>
<tr>
  <td align="left">hive.home</td>
  <td align="left">HIVE目录</td>
</tr>
<tr>
  <td align="left">hive.conf.dir</td>
  <td align="left">HIVE的配置目录</td>
</tr>
</tbody></table>


<ul>
<li>提交Connector </li>
</ul>



<pre class="prettyprint"><code class=" hljs lasso">curl <span class="hljs-attribute">-X</span> POST <span class="hljs-attribute">-H</span> <span class="hljs-string">"Content-Type: application/json"</span> <span class="hljs-subst">--</span><span class="hljs-built_in">data</span> @confluent<span class="hljs-attribute">-connector</span><span class="hljs-built_in">.</span>json http:<span class="hljs-comment">//192.168.103.44:8083/connectors</span></code></pre>



<h3 id="5-数据写入kafka">5. 数据写入kafka</h3>

<p>写入数据时，应带上schema，或者已创建schema的ID</p>

<h4 id="rest-api方式">REST API方式</h4>



<pre class="prettyprint"><code class=" hljs scilab">curl -X POST -H <span class="hljs-string">"Content-Type: application/vnd.kafka.avro.v1+json"</span>   --data <span class="hljs-string">'{"</span>value_schema_id<span class="hljs-string">":27, "</span>records<span class="hljs-string">": [{"</span>value<span class="hljs-string">": {"</span>sip<span class="hljs-string">":"</span><span class="hljs-number">1.1</span><span class="hljs-number">.1</span><span class="hljs-number">.1</span><span class="hljs-string">", "</span>dip<span class="hljs-string">":"</span><span class="hljs-number">1.1</span><span class="hljs-number">.1</span><span class="hljs-number">.2</span><span class="hljs-string">", "</span>smac<span class="hljs-string">":"</span><span class="hljs-number">111</span><span class="hljs-string">", "</span>dmac<span class="hljs-string">":"</span><span class="hljs-number">112</span><span class="hljs-string">"}}]}'</span>  http:<span class="hljs-comment">//192.168.103.44:8082/topics/confluentHive</span></code></pre>



<h4 id="kafka-client方式">Kafka Client方式</h4>

<p>详见 Kafka Client提供的接口，支持java/c/c++等语言</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>