---
layout:     post
title:      单个flume agent实践上线后宕机过程记录
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/warrah/article/details/56677400				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p><strong>一 单节点flume部署</strong> <br>
<strong>1 hadoop准备</strong> <br>
 在hdfs中创建flume目录，并将flume目录的权限分配给flume用户 <br>
hdfs dfs -mkdir flume  <br>
hdfs dfs -chown -R flume:flume /flume <br>
<strong>2 flume-env.sh</strong> <br>
进入${FLUME_HOME}/conf</p>

<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-keyword">cp</span> flume-env<span class="hljs-preprocessor">.sh</span><span class="hljs-preprocessor">.template</span> flume-env<span class="hljs-preprocessor">.sh</span>
vi flume-env<span class="hljs-preprocessor">.sh</span>，设置JAVA的环境变量
export JAVA_HOME=/usr/java/jdk1<span class="hljs-number">.7</span><span class="hljs-number">.0</span>_79</code></pre>

<p><strong>3 flume-conf.properties</strong> <br>
下方配置中application和dir由应用系统在logback.xml中配置</p>

<pre class="prettyprint"><code class=" hljs avrasm">agent1<span class="hljs-preprocessor">.sources</span>=source1  
agent1<span class="hljs-preprocessor">.channels</span>=channel1  
agent1<span class="hljs-preprocessor">.sinks</span>=sink1  

agent1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.source</span>1<span class="hljs-preprocessor">.type</span>=avro  
agent1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.source</span>1<span class="hljs-preprocessor">.bind</span>=<span class="hljs-number">0.0</span><span class="hljs-number">.0</span><span class="hljs-number">.0</span>  
agent1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.source</span>1<span class="hljs-preprocessor">.port</span>=<span class="hljs-number">44444</span>  
agent1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.source</span>1<span class="hljs-preprocessor">.channels</span>=channel1  

agent1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.channel</span>1<span class="hljs-preprocessor">.type</span>=memory  
agent1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.channel</span>1<span class="hljs-preprocessor">.capacity</span>=<span class="hljs-number">10000</span>  
agent1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.channel</span>1<span class="hljs-preprocessor">.transactionCapacity</span>=<span class="hljs-number">1000</span>  
agent1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.channel</span>1<span class="hljs-preprocessor">.keep</span>-alive=<span class="hljs-number">30</span>  

agent1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.sink</span>1<span class="hljs-preprocessor">.type</span>=hdfs  
agent1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.sink</span>1<span class="hljs-preprocessor">.channel</span>=channel1  
agent1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.sink</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.path</span>=hdfs://dashuju174:<span class="hljs-number">9000</span>/flume/%{application}/%{dir}/%<span class="hljs-built_in">Y</span>%m%d
agent1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.sink</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.fileType</span>=DataStream  
agent1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.sink</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.writeFormat</span>=Text  
agent1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.sink</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.rollInterval</span>=<span class="hljs-number">0</span>  
agent1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.sink</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.rollSize</span>=<span class="hljs-number">10240</span>  
agent1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.sink</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.rollCount</span>=<span class="hljs-number">0</span>  
agent1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.sink</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.idleTimeout</span>=<span class="hljs-number">60</span></code></pre>

<p><strong>4 logback配置</strong> <br>
logback.xml中添加下方日志，专门针对TRACE级别的日志。选择TRACE的原因是因为开发人员对INFO\ERROR\DEBUG比较熟悉，对TRACE应用较为生疏，故我这里要求开发人员统一使用TRACE级别的日志。</p>

<pre class="prettyprint"><code class="language-xml hljs "><span class="hljs-tag">&lt;<span class="hljs-title">appender</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"flume"</span> <span class="hljs-attribute">class</span>=<span class="hljs-value">"com.gilt.logback.flume.FlumeLogstashV1Appender"</span>&gt;</span>
  <span class="hljs-comment">&lt;!-- 此项是连接flume节点的ip和端口 --&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-title">flumeAgents</span>&gt;</span> 192.168.5.174:44444  <span class="hljs-tag">&lt;/<span class="hljs-title">flumeAgents</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-title">flumeProperties</span>&gt;</span>connect-timeout=4000;request-timeout=8000<span class="hljs-tag">&lt;/<span class="hljs-title">flumeProperties</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-title">batchSize</span>&gt;</span>100<span class="hljs-tag">&lt;/<span class="hljs-title">batchSize</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-title">reportingWindow</span>&gt;</span>1000<span class="hljs-tag">&lt;/<span class="hljs-title">reportingWindow</span>&gt;</span>
  <span class="hljs-comment">&lt;!-- 此项配置avro头部信息 --&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-title">additionalAvroHeaders</span>&gt;</span>myHeader=dengjun<span class="hljs-tag">&lt;/<span class="hljs-title">additionalAvroHeaders</span>&gt;</span>
  <span class="hljs-comment">&lt;!-- 此项配置当前应用   --&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-title">application</span>&gt;</span>bds<span class="hljs-tag">&lt;/<span class="hljs-title">application</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-title">filter</span> <span class="hljs-attribute">class</span>=<span class="hljs-value">"ch.qos.logback.classic.filter.LevelFilter"</span>&gt;</span><span class="hljs-comment">&lt;!-- 只打印错误日志 --&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">level</span>&gt;</span>TRACE<span class="hljs-tag">&lt;/<span class="hljs-title">level</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">onMatch</span>&gt;</span>ACCEPT<span class="hljs-tag">&lt;/<span class="hljs-title">onMatch</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">onMismatch</span>&gt;</span>DENY<span class="hljs-tag">&lt;/<span class="hljs-title">onMismatch</span>&gt;</span>
  <span class="hljs-tag">&lt;/<span class="hljs-title">filter</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-title">layout</span> <span class="hljs-attribute">class</span>=<span class="hljs-value">"ch.qos.logback.classic.PatternLayout"</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">pattern</span>&gt;</span>%message%n%ex<span class="hljs-tag">&lt;/<span class="hljs-title">pattern</span>&gt;</span>
  <span class="hljs-tag">&lt;/<span class="hljs-title">layout</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">appender</span>&gt;</span></code></pre>

<p><strong>5 环境变量</strong> <br>
flume依赖zookeeper，故安装flume之前需要安装zookeeper <br>
tar zxvf apache-flume-1.6.0-bin.tar.gz <br>
ln -s apache-flume-1.6.0 flume <br>
设置flume的环境变量 <br>
export FLUME_HOME=/home/hadoop/application/flume <br>
export PATH=<span class="MathJax_Preview"></span><span class="MathJax" id="MathJax-Element-30-Frame" role="textbox" aria-readonly="true"><nobr><span class="math" id="MathJax-Span-204" style="width: 4.536em; display: inline-block;"><span style="display: inline-block; position: relative; width: 3.629em; height: 0px; font-size: 125%;"><span style="position: absolute; clip: rect(1.869em 1000em 2.883em -0.424em); top: -2.717em; left: 0.003em;"><span class="mrow" id="MathJax-Span-205"><span class="mi" id="MathJax-Span-206" style="font-family: MathJax_Math-italic;">P<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.109em;"></span></span><span class="mi" id="MathJax-Span-207" style="font-family: MathJax_Math-italic;">A</span><span class="mi" id="MathJax-Span-208" style="font-family: MathJax_Math-italic;">T<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.109em;"></span></span><span class="mi" id="MathJax-Span-209" style="font-family: MathJax_Math-italic;">H<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.056em;"></span></span><span class="mo" id="MathJax-Span-210" style="font-family: MathJax_Main; padding-left: 0.269em;">:</span></span><span style="display: inline-block; width: 0px; height: 2.723em;"></span></span></span><span style="border-left: 0.003em solid; display: inline-block; overflow: hidden; width: 0px; height: 1.003em; vertical-align: -0.063em;"></span></span></nobr></span><script type="math/tex" id="MathJax-Element-30">PATH:</script>FLUME_HOME/bin <br>
<strong>二 上线运行宕机记录</strong> <br>
按照上述配置后，上线第3天，flume宕机了。我这里把解决过程记录下来，毕竟第一次使用flume。 <br>
待续。。。</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>