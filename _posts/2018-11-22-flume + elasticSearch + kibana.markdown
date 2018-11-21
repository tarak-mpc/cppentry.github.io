---
layout:     post
title:      flume + elasticSearch + kibana
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/lanmo555/article/details/50471256				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="a">A</h1>

<p><img src="https://img-blog.csdn.net/20151211182458470" alt="design" title=""></p>

<p>flume: 1.6.0 </p>

<p>elasticSearch: 1.6.0</p>

<p>kibana： 4.1.3-linux-x64</p>



<h1 id="下载地址">下载地址：</h1>

<p><a href="http://mirrors.hust.edu.cn/apache/flume/1.6.0/apache-flume-1.6.0-bin.tar.gz" rel="nofollow">http://mirrors.hust.edu.cn/apache/flume/1.6.0/apache-flume-1.6.0-bin.tar.gz</a></p>

<p><a href="https://download.elastic.co/elasticsearch/elasticsearch/elasticsearch-1.6.0.tar.gz" rel="nofollow">https://download.elastic.co/elasticsearch/elasticsearch/elasticsearch-1.6.0.tar.gz</a> <br>
<a href="https://download.elastic.co/kibana/kibana/kibana-4.1.3-linux-x64.tar.gz" rel="nofollow">https://download.elastic.co/kibana/kibana/kibana-4.1.3-linux-x64.tar.gz</a></p>

<p>当前最新版: <br>
<a href="https://download.elasticsearch.org/elasticsearch/release/org/elasticsearch/distribution/zip/elasticsearch/2.1.0/elasticsearch-2.1.0.zip" rel="nofollow">https://download.elasticsearch.org/elasticsearch/release/org/elasticsearch/distribution/zip/elasticsearch/2.1.0/elasticsearch-2.1.0.zip</a> <br>
<a href="https://download.elastic.co/kibana/kibana/kibana-4.3.0-linux-x64.tar.gz" rel="nofollow">https://download.elastic.co/kibana/kibana/kibana-4.3.0-linux-x64.tar.gz</a></p>



<h1 id="启动-elasticsearch">启动 elasticSearch</h1>

<p>配置: config/elasticsearch.yml </p>

<p>启动: <code>bin/elasticSearch -d</code></p>



<h1 id="启动-kibana">启动 kibana</h1>

<p>先配置 config/kibana.yml</p>

<p>启动: bin/kibana</p>

<p>如果后台运行,可以使用这个  <a href="http://blog.csdn.net/lanmo555/article/details/49666715" rel="nofollow"> daemon.c </a></p>

<p><code>./daemon /data/elk/kibana-4.1.3-linux-x64/bin/kibana</code></p>



<h1 id="运行-flume-collector">运行 flume (collector)</h1>

<p>配置 avro.conf , sink有２个，分别输出到elasticSearch和文件（flume-ng-log4j-sink） <br>
743 <br>
bin/flume-ng agent -c ./conf -f conf/avro.conf -n avroAgent -Dflume.root.logger=INFO,console <br>
bin/flume-ng agent -c ./conf -f conf/tomcatLog.conf -n tomcatAgent -Dflume.root.logger=INFO,console</p>

<p>本地测试,使用 avro-client 上传一个文件内容,如果成功在 elasticSearch/kibana 就能看到数据</p>

<p>bin/flume-ng avro-client -c ./conf -H localhost -p 1234 -F /etc/passwd -Dflume.root.logger=DEBUG,console</p>



<h1 id="flume-agent">flume-agent</h1>

<p>如果是java项目可以使用 flume-ng-log4jappender 直接上传到avro</p>

<p>如果tail文件 则需要再增加一个保证agent存活的程序，如 daemontools, 而 daemontools 的存活需要使用系统服务来保证</p>

<p>flume-avro-elasticSearch-log4j</p>



<pre class="prettyprint"><code class=" hljs avrasm">avroAgent<span class="hljs-preprocessor">.sources</span> = avro
avroAgent<span class="hljs-preprocessor">.channels</span> = memoryChannel fileCh
avroAgent<span class="hljs-preprocessor">.sinks</span> = elasticSearch logfile

<span class="hljs-preprocessor"># For each one of the sources, the type is defined</span>
avroAgent<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.avro</span><span class="hljs-preprocessor">.type</span> = avro
avroAgent<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.avro</span><span class="hljs-preprocessor">.bind</span> = <span class="hljs-number">0.0</span><span class="hljs-number">.0</span><span class="hljs-number">.0</span>
avroAgent<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.avro</span><span class="hljs-preprocessor">.port</span> = <span class="hljs-number">1234</span> 
avroAgent<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.avro</span><span class="hljs-preprocessor">.threads</span> = <span class="hljs-number">20</span> 
avroAgent<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.avro</span><span class="hljs-preprocessor">.channels</span> = memoryChannel  

<span class="hljs-preprocessor"># Each sink's type must be defined</span>
avroAgent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.elasticSearch</span><span class="hljs-preprocessor">.type</span> = org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.sink</span><span class="hljs-preprocessor">.elasticsearch</span><span class="hljs-preprocessor">.ElasticSearchSink</span>

<span class="hljs-preprocessor">#Specify the channel the sink should use</span>
avroAgent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.elasticSearch</span><span class="hljs-preprocessor">.channel</span> = memoryChannel
avroAgent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.elasticSearch</span><span class="hljs-preprocessor">.batchSize</span> = <span class="hljs-number">100</span>
avroAgent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.elasticSearch</span><span class="hljs-preprocessor">.hostNames</span>=<span class="hljs-number">172.16</span><span class="hljs-number">.0</span><span class="hljs-number">.18</span>:<span class="hljs-number">9300</span> 
avroAgent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.elasticSearch</span><span class="hljs-preprocessor">.indexName</span>=longdai
avroAgent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.elasticSearch</span><span class="hljs-preprocessor">.indexType</span>=longdai
avroAgent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.elasticSearch</span><span class="hljs-preprocessor">.clusterName</span>=longdai 
avroAgent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.elasticSearch</span><span class="hljs-preprocessor">.client</span> = transport
avroAgent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.elasticSearch</span><span class="hljs-preprocessor">.serializer</span>=org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.sink</span><span class="hljs-preprocessor">.elasticsearch</span><span class="hljs-preprocessor">.ElasticSearchLogStashEventSerializer</span>

avroAgent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.logfile</span><span class="hljs-preprocessor">.type</span> = org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.sink</span><span class="hljs-preprocessor">.log</span>4j<span class="hljs-preprocessor">.Log</span>4jSink
avroAgent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.logfile</span><span class="hljs-preprocessor">.channel</span> = memoryChannel
avroAgent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.logfile</span><span class="hljs-preprocessor">.configFile</span> = /home/apache-flume-<span class="hljs-number">1.6</span><span class="hljs-number">.0</span>-bin/conf/log4j<span class="hljs-preprocessor">.xml</span>

avroAgent<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.fileCh</span><span class="hljs-preprocessor">.type</span> = file 
avroAgent<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.fileCh</span><span class="hljs-preprocessor">.keep</span>-alive = <span class="hljs-number">3</span> 
avroAgent<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.fileCh</span><span class="hljs-preprocessor">.overflowCapacity</span> = <span class="hljs-number">1000000</span> 
avroAgent<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.fileCh</span><span class="hljs-preprocessor">.dataDirs</span> = /data/flume/ch/data

<span class="hljs-preprocessor"># Each channel's type is defined.</span>
avroAgent<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.memoryChannel</span><span class="hljs-preprocessor">.type</span> = memory

<span class="hljs-preprocessor"># Other config values specific to each type of channel(sink or source)</span>
<span class="hljs-preprocessor"># can be defined as well</span>
<span class="hljs-preprocessor"># In this case, it specifies the capacity of the memory channel</span>
avroAgent<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.memoryChannel</span><span class="hljs-preprocessor">.capacity</span> = <span class="hljs-number">2000</span>
avroAgent<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.memoryChannel</span><span class="hljs-preprocessor">.transactionCapacity</span> = <span class="hljs-number">2000</span>
avroAgent<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.memoryChannel</span><span class="hljs-preprocessor">.keep</span>-alive = <span class="hljs-number">3</span> 

<span class="hljs-preprocessor">#-- END --</span></code></pre>

<p>flume-tail-elasticSearch </p>



<pre class="prettyprint"><code class=" hljs avrasm">tomcatAgent<span class="hljs-preprocessor">.sources</span> = tomcatLog 
tomcatAgent<span class="hljs-preprocessor">.channels</span> = memoryChannel
tomcatAgent<span class="hljs-preprocessor">.sinks</span> = elasticSearch 

<span class="hljs-preprocessor"># For each one of the sources, the type is defined</span>
tomcatAgent<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.tomcatLog</span><span class="hljs-preprocessor">.type</span> = exec 

<span class="hljs-preprocessor"># The channel can be defined as follows.</span>
tomcatAgent<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.tomcatLog</span><span class="hljs-preprocessor">.channels</span> = memoryChannel
tomcatAgent<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.tomcatLog</span><span class="hljs-preprocessor">.command</span> = tail -F /data/longdai/logs/longdai<span class="hljs-preprocessor">.log</span>
tomcatAgent<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.tomcatLog</span><span class="hljs-preprocessor">.bufferCount</span> = <span class="hljs-number">200</span>

<span class="hljs-preprocessor"># Each sink's type must be defined</span>
tomcatAgent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.elasticSearch</span><span class="hljs-preprocessor">.type</span> = org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.sink</span><span class="hljs-preprocessor">.elasticsearch</span><span class="hljs-preprocessor">.ElasticSearchSink</span>

<span class="hljs-preprocessor">#Specify the channel the sink should use</span>
tomcatAgent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.elasticSearch</span><span class="hljs-preprocessor">.channel</span> = memoryChannel
tomcatAgent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.elasticSearch</span><span class="hljs-preprocessor">.batchSize</span> = <span class="hljs-number">100</span>
tomcatAgent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.elasticSearch</span><span class="hljs-preprocessor">.hostNames</span>=<span class="hljs-number">172.16</span><span class="hljs-number">.0</span><span class="hljs-number">.18</span>:<span class="hljs-number">9300</span> 
tomcatAgent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.elasticSearch</span><span class="hljs-preprocessor">.indexName</span>=longdai
tomcatAgent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.elasticSearch</span><span class="hljs-preprocessor">.indexType</span>=longdai
tomcatAgent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.elasticSearch</span><span class="hljs-preprocessor">.clusterName</span>=longdai 
tomcatAgent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.elasticSearch</span><span class="hljs-preprocessor">.client</span> = transport
tomcatAgent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.elasticSearch</span><span class="hljs-preprocessor">.serializer</span>=org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.sink</span><span class="hljs-preprocessor">.elasticsearch</span><span class="hljs-preprocessor">.ElasticSearchLogStashEventSerializer</span>

<span class="hljs-preprocessor"># Each channel's type is defined.</span>
tomcatAgent<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.memoryChannel</span><span class="hljs-preprocessor">.type</span> = memory

<span class="hljs-preprocessor"># Other config values specific to each type of channel(sink or source)</span>
<span class="hljs-preprocessor"># can be defined as well</span>
<span class="hljs-preprocessor"># In this case, it specifies the capacity of the memory channel</span>
tomcatAgent<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.memoryChannel</span><span class="hljs-preprocessor">.capacity</span> = <span class="hljs-number">100</span></code></pre>

<p>另:</p>



<pre class="prettyprint"><code class=" hljs avrasm">cd /data/apache-flume-<span class="hljs-number">1.6</span><span class="hljs-number">.0</span>-bin/
wget http://central<span class="hljs-preprocessor">.maven</span><span class="hljs-preprocessor">.org</span>/maven2/org/elasticsearch/elasticsearch/<span class="hljs-number">1.6</span><span class="hljs-number">.0</span>/elasticsearch-<span class="hljs-number">1.6</span><span class="hljs-number">.0</span><span class="hljs-preprocessor">.jar</span>
mv elasticsearch-<span class="hljs-number">1.6</span><span class="hljs-number">.0</span><span class="hljs-preprocessor">.jar</span> lib/
wget http://central<span class="hljs-preprocessor">.maven</span><span class="hljs-preprocessor">.org</span>/maven2/org/apache/lucene/lucene-core/<span class="hljs-number">4.10</span><span class="hljs-number">.4</span>/lucene-core-<span class="hljs-number">4.10</span><span class="hljs-number">.4</span><span class="hljs-preprocessor">.jar</span>
mv lucene-core-<span class="hljs-number">4.10</span><span class="hljs-number">.4</span><span class="hljs-preprocessor">.jar</span> lib/</code></pre>

<p>flume-agent  一定要修个 bin/flume-ng, 增大堆内存（<code>Xmx默认20m</code>）, 如果使用memchannel总是感觉内存有问题，那就缓存file channel, 使用固态硬盘速度够用了</p>

<p><img src="https://img-blog.csdn.net/20160106193505459" alt="kibana" title=""></p>

<hr>

<p><img src="https://img-blog.csdn.net/20160106193556680" alt="kibana" title=""></p>

<h1 id="o">O</h1>

<p>如果你的项目是java项目,且使用 log4j-1.x socket 收集日志也可以使用服务端log4j-collector收集，可以将日志按照自己需求路由到不同的appender。线上亲测，到目前为止无故障连续运行3个多月。可以搭配keepalive/deamontools使其更加稳定</p>

<p><img src="https://raw.githubusercontent.com/blackshadowwalker/log4j-collector/master/doc/20151204161519.png" alt="screenshot" title=""></p>

<ul>
<li><a href="https://github.com/blackshadowwalker/flume-ng-log4j-sink" rel="nofollow">flume-ng-log4j-sink</a></li>
<li><a href="https://github.com/blackshadowwalker/log4j-collector" rel="nofollow">log4j-collector(socket server)</a></li>
</ul>

<hr>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>