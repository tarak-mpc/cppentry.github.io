---
layout:     post
title:      kafka connect+debezium(binlog)，解析binlog至kafka
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2 id="本文目标">本文目标</h2>

<p>在已知kafka connect和debezium作用，会使用kafka的基础上，学会使用debezium来读取binlog，并通过kafka connect将读取的内容放入kafka topic中。 <br>
基于卡夫卡0.10.0和Debezium0.6,mysql5.6</p>



<h2 id="kafka-connect">kafka connect</h2>

<ul>
<li>Kafka Connect是一种用于Kafka和其他数据系统之间进行数据传输的工具。</li>
<li>仅关注数据的复制，并且不处理其他任务</li>
<li>Kafka connect有两个概念，一个source，另一个是sink。source是把数据从一个系统拷贝到kafka里，sink是从kafka拷贝到另一个系统里。</li>
<li>可使用插件，获取不同系统的数据。例如通过Debezium插件解析mysql的日志，获取数据。</li>
<li>支持集群，可以通过REST API管理Kafka Connect。</li>
<li>对数据的传输进行管理和监控。</li>
</ul>



<h2 id="debezium">Debezium</h2>

<ul>
<li>Debezium是一个分布式平台，可将现有数据库转换为事件流，因此应用程序可以立即查看并立即响应数据库中每一行的更改。</li>
<li>Debezium建立在Apache Kafka之上，并提供用于监视特定数据库管理系统的Kafka Connect兼容连接器。</li>
<li>本教程使用Debezium监控binlog。</li>
</ul>



<h2 id="准备操作">准备操作</h2>

<ul>
<li>mysql需开启binlog</li>
</ul>



<pre class="prettyprint"><code class=" hljs rsl">[mysqld]
<span class="hljs-built_in">log</span>-bin=mysql-bin <span class="hljs-preprocessor">#添加这一行就ok</span>
binlog-<span class="hljs-built_in">format</span>=ROW <span class="hljs-preprocessor">#选择row模式</span>
server_id=<span class="hljs-number">1</span> <span class="hljs-preprocessor">#配置mysql replaction需要定义，不能和canal的slaveId重复</span></code></pre>

<ul>
<li>mysql需创建一个有mysql slave相关权限的账号，若mysql不在本机，则需要远程权限，防火墙放行。</li>
</ul>



<pre class="prettyprint"><code class=" hljs sql">//mysql slave相关权限
<span class="hljs-operator"><span class="hljs-keyword">CREATE</span> <span class="hljs-keyword">USER</span> canal IDENTIFIED <span class="hljs-keyword">BY</span> <span class="hljs-string">'debezium'</span>;</span>  
<span class="hljs-operator"><span class="hljs-keyword">GRANT</span> <span class="hljs-keyword">SELECT</span>, REPLICATION SLAVE, REPLICATION CLIENT <span class="hljs-keyword">ON</span> *.* <span class="hljs-keyword">TO</span> <span class="hljs-string">'debezium'</span>@<span class="hljs-string">'%'</span>;</span>
<span class="hljs-comment">-- GRANT ALL PRIVILEGES ON *.* TO 'debezium'@'%' ;</span>
FLUSH PRIVILEGES;</code></pre>



<h2 id="操作概述">操作概述</h2>

<ul>
<li>安装并启动kafka</li>
<li>安装并启动mysql</li>
<li>下载Debezium的mysql连接器<a href="http://debezium.io/docs/install/" rel="nofollow" target="_blank">http://debezium.io/docs/install/</a>并解压</li>
<li>安装debezium，即将解压目录写入classpath变量，例如：export classpath=/root/debezium-connector-mysql/* <br>
只在当前shell有效</li>
<li>参考<a href="http://debezium.io/docs/connectors/mysql/" rel="nofollow" target="_blank">http://debezium.io/docs/connectors/mysql/</a>的配置文件示例，写好配置文件。</li>
<li>以独立模式启动kafka connect，此时debezium会对数据库中的每一个表创建一个topic，消费相应的topic，即可获取binlog解析信息。</li>
</ul>

<pre class="prettyprint"><code class=" hljs lasso"><span class="hljs-comment">//启动kafka connect</span>
bin/connect<span class="hljs-attribute">-standalone</span><span class="hljs-built_in">.</span>sh config/connect<span class="hljs-attribute">-standalone</span><span class="hljs-built_in">.</span>properties mysql<span class="hljs-built_in">.</span>properties
<span class="hljs-comment">//查看topic列表</span>
bin/kafka<span class="hljs-attribute">-topics</span><span class="hljs-built_in">.</span>sh <span class="hljs-subst">--</span><span class="hljs-built_in">list</span> <span class="hljs-subst">--</span>zookeeper localhost:<span class="hljs-number">2181</span>
<span class="hljs-comment">//消费该主题</span>
bin/kafka<span class="hljs-attribute">-console</span><span class="hljs-attribute">-consumer</span><span class="hljs-built_in">.</span>sh <span class="hljs-subst">--</span>bootstrap<span class="hljs-attribute">-server</span> localhost:<span class="hljs-number">9092</span> <span class="hljs-subst">--</span>topic test <span class="hljs-subst">--</span>from<span class="hljs-attribute">-beginning</span></code></pre>



<h2 id="配置文件">配置文件</h2>



<pre class="prettyprint"><code class=" hljs avrasm">    //mysql<span class="hljs-preprocessor">.properties</span>
    name=inventory-connector
    connector<span class="hljs-preprocessor">.class</span>=io<span class="hljs-preprocessor">.debezium</span><span class="hljs-preprocessor">.connector</span><span class="hljs-preprocessor">.mysql</span><span class="hljs-preprocessor">.MySqlConnector</span>
    database<span class="hljs-preprocessor">.hostname</span>=<span class="hljs-number">192.168</span><span class="hljs-number">.99</span><span class="hljs-number">.100</span>
    database<span class="hljs-preprocessor">.port</span>=<span class="hljs-number">3306</span>
    database<span class="hljs-preprocessor">.user</span>=debezium
    database<span class="hljs-preprocessor">.password</span>=dbz
    database<span class="hljs-preprocessor">.server</span><span class="hljs-preprocessor">.id</span>=<span class="hljs-number">184054</span>
    database<span class="hljs-preprocessor">.server</span><span class="hljs-preprocessor">.name</span>=fullfillment
    database<span class="hljs-preprocessor">.whitelist</span>=inventory
    database<span class="hljs-preprocessor">.history</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.bootstrap</span><span class="hljs-preprocessor">.servers</span>=<span class="hljs-number">192.168</span><span class="hljs-number">.30</span><span class="hljs-number">.30</span>:<span class="hljs-number">9092</span>
    database<span class="hljs-preprocessor">.history</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.topic</span>=dbhistory<span class="hljs-preprocessor">.fullfillment</span>
    include<span class="hljs-preprocessor">.schema</span><span class="hljs-preprocessor">.changes</span>=true</code></pre>



<h2 id="索引">索引</h2>

<p>debezium官网 <a href="http://debezium.io/" rel="nofollow" target="_blank">http://debezium.io/</a> <br>
kafka文档 <a href="http://kafka.apache.org/0100/documentation.html" rel="nofollow" target="_blank">http://kafka.apache.org/0100/documentation.html</a></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>