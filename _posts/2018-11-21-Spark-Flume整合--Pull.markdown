---
layout:     post
title:      Spark-Flume整合--Pull
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为涂志海博客原创文章，未经博主允许不得转载。					https://blog.csdn.net/tuzhihai/article/details/78800681				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h3 id="第二种sparkstreaming-整合flume">第二种sparkStreaming 整合Flume</h3>

<blockquote>
  <blockquote>
    <h3 id="flume采用-netcat-memory-customer-sink架构">flume采用 netcat-memory-customer sink架构</h3>
  </blockquote>
  
  <h3 id="本地测试">本地测试</h3>
  
  <blockquote>
    <h3 id="1本地启动sprakstreaming服务192168145128-10000">1：本地启动sprakStreaming服务，（192.168.145.128 10000）</h3>
    
    <h3 id="2-服务器中启动flume-agent">2. 服务器中启动flume agent</h3>
    
    <h3 id="3-telnet往端口中输入数据观察本地idea控制台输出数据">3. telnet往端口中输入数据，观察本地idea控制台输出数据</h3>
  </blockquote>
  
  <h3 id="服务器测试">服务器测试</h3>
  
  <blockquote>
    <h3 id="mvn打包mvn-clean-package-dskiptests">mvn打包：mvn clean package -DskipTests</h3>
    
    <h3 id="上传至服务器">上传至服务器</h3>
    
    <h3 id="先启动flume">先启动flume</h3>
  </blockquote>
</blockquote>



<pre class="prettyprint"><code class=" hljs haml">flume-ng agent \
  -<span class="ruby">-name netcat-memory-avro \
</span>  -<span class="ruby">-conf <span class="hljs-variable">$FLUME_HOME</span>/conf \
</span>  -<span class="ruby">-conf-file <span class="hljs-variable">$FLUME_HOME</span>/conf/netcat-memory-avro.conf \
</span>  -<span class="ruby"><span class="hljs-constant">Dflume</span>.root.logger=<span class="hljs-constant">INFO</span>,console </span></code></pre>

<blockquote>
  <blockquote>
    <h3 id="后启动spark">后启动spark</h3>
  </blockquote>
</blockquote>



<pre class="prettyprint"><code class=" hljs haml">spark-submit \
-<span class="ruby">-<span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">com</span>.<span class="hljs-title">tuzhihai</span>.<span class="hljs-title">flumespark</span>.<span class="hljs-title">SparkPullFlume</span> \</span>
</span>-<span class="ruby">-master local[<span class="hljs-number">2</span>] \
</span>-<span class="ruby">-packages org.apache.<span class="hljs-symbol">spark:</span>spark-streaming-flume_2.<span class="hljs-number">11</span><span class="hljs-symbol">:</span><span class="hljs-number">2.2</span>.<span class="hljs-number">0</span> \
</span><span class="hljs-comment">/root/soft_down/lib/sparklearn-1.0.jar \</span>
192.168.145.128 10000</code></pre>

<blockquote>
  <blockquote>
    <h3 id="在端口输入数据">在端口输入数据</h3>
  </blockquote>
</blockquote>



<pre class="prettyprint"><code class=" hljs nginx"><span class="hljs-title">telnet</span> <span class="hljs-number">192.168.145.128</span> <span class="hljs-number">9990</span></code></pre>

<blockquote>
  <blockquote>
    <h3 id="观察flume控制台">观察flume控制台</h3>
    
    <blockquote>
      <h3 id="pull方式为什么要先启动flume后启动spark">pull方式为什么要先启动flume后启动spark?</h3>
      
      <blockquote>
        <h3 id="首先说明pull方式比push方式更加可靠在实际工作中应用极多">首先说明，pull方式比push方式更加可靠，在实际工作中应用极多。</h3>
        
        <h3 id="pull方式是flume采集到数据后存储在一个agent中然后spark想要数据的时候直接从这个agent中pull就ok了这个方式明显更加友好更加符合工作要求不是你主动传给我而是我想要的时候我自己从你那拿">pull方式是flume采集到数据后存储在一个Agent中，然后spark想要数据的时候，直接从这个Agent中pull就ok了。这个方式明显更加友好，更加符合工作要求，不是你主动传给我，而是我想要的时候我自己从你那拿。</h3>
      </blockquote>
    </blockquote>
  </blockquote>
</blockquote>

<hr>

<hr>

<hr>



<h5 id="spark-pull-flumeconf">spark-pull-flume.conf</h5>



<pre class="prettyprint"><code class=" hljs avrasm"> flume-ng agent \
  --name netcat-memory-spark \
  --conf $FLUME_HOME/conf \
  --conf-file $FLUME_HOME/conf/spark-pull-flume<span class="hljs-preprocessor">.conf</span> \
  -Dflume<span class="hljs-preprocessor">.root</span><span class="hljs-preprocessor">.logger</span>=INFO,console 

<span class="hljs-preprocessor"># example netcat-memory-spark</span>
netcat-memory-spark<span class="hljs-preprocessor">.sources</span> = netcat-source
netcat-memory-spark<span class="hljs-preprocessor">.sinks</span> = spark-sink
netcat-memory-spark<span class="hljs-preprocessor">.channels</span> = memory-channel

<span class="hljs-preprocessor"># Describe/configure the source</span>
netcat-memory-spark<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.netcat</span>-source<span class="hljs-preprocessor">.type</span> = netcat
netcat-memory-spark<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.netcat</span>-source<span class="hljs-preprocessor">.bind</span> = <span class="hljs-number">192.168</span><span class="hljs-number">.145</span><span class="hljs-number">.128</span>
netcat-memory-spark<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.netcat</span>-source<span class="hljs-preprocessor">.port</span> = <span class="hljs-number">9999</span>

<span class="hljs-preprocessor"># Describe/ the sink</span>
netcat-memory-spark<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.spark</span>-sink<span class="hljs-preprocessor">.type</span> = org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.streaming</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.sink</span><span class="hljs-preprocessor">.SparkSink</span>
netcat-memory-spark<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.spark</span>-sink<span class="hljs-preprocessor">.hostname</span> = <span class="hljs-number">192.168</span><span class="hljs-number">.145</span><span class="hljs-number">.128</span>
netcat-memory-spark<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.spark</span>-sink<span class="hljs-preprocessor">.port</span> = <span class="hljs-number">10000</span>

<span class="hljs-preprocessor"># Use a channel which buffers events in memory</span>
netcat-memory-spark<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.memory</span>-channel<span class="hljs-preprocessor">.type</span> = memory

<span class="hljs-preprocessor"># Bind the source and sink to the channel</span>
netcat-memory-spark<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.netcat</span>-source<span class="hljs-preprocessor">.channels</span> = memory-channel
netcat-memory-spark<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.spark</span>-sink<span class="hljs-preprocessor">.channel</span> = memory-channel
</code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>