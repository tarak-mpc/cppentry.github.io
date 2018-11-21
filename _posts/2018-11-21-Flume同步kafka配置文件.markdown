---
layout:     post
title:      Flume同步kafka配置文件
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/baifanwudi/article/details/78973292				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>到flume官网下载flume,解压</p>



<pre class="prettyprint"><code class=" hljs avrasm">cd $FLUME_HOME/conf
<span class="hljs-keyword">cp</span> flume-conf<span class="hljs-preprocessor">.properties</span><span class="hljs-preprocessor">.template</span> applog-conf<span class="hljs-preprocessor">.properties</span></code></pre>

<p>修改applog-conf.properties属性</p>



<pre class="prettyprint"><code class=" hljs avrasm">
agent<span class="hljs-preprocessor">.sources</span> = KafkaApplog
agent<span class="hljs-preprocessor">.channels</span> = ApplogChannel
agent<span class="hljs-preprocessor">.sinks</span> = ApplogSink

<span class="hljs-preprocessor"># For each one of the sources, the type is defined</span>

agent<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.KafkaApplog</span><span class="hljs-preprocessor">.channels</span> = ApplogChannel
agent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.ApplogSink</span><span class="hljs-preprocessor">.channel</span> = ApplogChannel

agent<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.KafkaApplog</span><span class="hljs-preprocessor">.type</span> = org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.source</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.KafkaSource</span>
agent<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.KafkaApplog</span><span class="hljs-preprocessor">.batchSize</span> = <span class="hljs-number">1000</span>
agent<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.KafkaApplog</span><span class="hljs-preprocessor">.batchDurationMillis</span> = <span class="hljs-number">20000</span>

agent<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.KafkaApplog</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.bootstrap</span><span class="hljs-preprocessor">.servers</span> = dn120:<span class="hljs-number">9092</span>,dn121:<span class="hljs-number">9092</span>,dn122:<span class="hljs-number">9092</span>

agent<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.KafkaApplog</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.topics</span> = applog
agent<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.KafkaApplog</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.consumer</span><span class="hljs-preprocessor">.group</span><span class="hljs-preprocessor">.id</span> = flume
agent<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.KafkaApplog</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.consumer</span><span class="hljs-preprocessor">.auto</span><span class="hljs-preprocessor">.offset</span><span class="hljs-preprocessor">.reset</span>=earliest

agent<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.ApplogChannel</span><span class="hljs-preprocessor">.type</span> = memory
agent<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.ApplogChannel</span><span class="hljs-preprocessor">.capacity</span>=<span class="hljs-number">1000000</span>
agent<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.ApplogChannel</span><span class="hljs-preprocessor">.transactionCapacity</span>=<span class="hljs-number">2000</span>
agent<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.ApplogChannel</span><span class="hljs-preprocessor">.keep</span>-alive = <span class="hljs-number">60</span>


agent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.ApplogSink</span><span class="hljs-preprocessor">.type</span> = hdfs
<span class="hljs-preprocessor"># 注意, 我们输出到下面一个子文件夹datax中</span>
agent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.ApplogSink</span><span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.path</span> = hdfs://adups:<span class="hljs-number">8020</span>/user/kafka/flume/ota_app_log/pt=%<span class="hljs-built_in">Y</span>-%m-%d
agent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.ApplogSink</span><span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.writeFormat</span> = Text
agent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.ApplogSink</span><span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.fileType</span> = DataStream
agent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.ApplogSink</span><span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.callTimeout</span>= <span class="hljs-number">300000</span>
agent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.ApplogSink</span><span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.rollSize</span> = <span class="hljs-number">10240000</span>
agent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.ApplogSink</span><span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.rollCount</span> = <span class="hljs-number">20000</span>
agent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.ApplogSink</span><span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.rollInterval</span> = <span class="hljs-number">300</span>
agent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.ApplogSink</span><span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.inUsePrefix</span> = _</code></pre>

<p>进入FLUME_HOME目录,执行启动命令</p>



<pre class="prettyprint"><code class=" hljs lasso">bin/flume<span class="hljs-attribute">-ng</span> agent <span class="hljs-attribute">-c</span> conf  <span class="hljs-attribute">-f</span> conf/applog<span class="hljs-attribute">-conf</span><span class="hljs-built_in">.</span>properties <span class="hljs-attribute">-n</span> agent <span class="hljs-subst">&amp;</span></code></pre>

<p>本过程消费kafka中applog同步到HDFS目录 <br>
目录结构如下: <br>
<img src="https://img-blog.csdn.net/20180104172209903?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYmFpZmFud3VkaQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p>它会先生成临时文件tmp以”_”为开头,再转变为正式文件.</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>