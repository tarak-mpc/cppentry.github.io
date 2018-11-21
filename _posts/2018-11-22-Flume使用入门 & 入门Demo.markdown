---
layout:     post
title:      Flume使用入门 & 入门Demo
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/lemonZhaoTao/article/details/80640959				</div>
								            <div id="content_views" class="markdown_views prism-github-gist">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>Flume系列文章： <br>
<a href="https://blog.csdn.net/lemonzhaotao/article/details/80560092" rel="nofollow">Flume 概述 &amp; 架构 &amp; 组件介绍</a></p>

<p>在本篇文章中，将介绍Flume的使用</p>

<h3 id="使用flume-入门案例">使用Flume &amp; 入门案例</h3>



<h4 id="查看使用帮助">查看使用帮助</h4>

<pre class="prettyprint"><code class=" hljs bash">$&gt;<span class="hljs-built_in">cd</span> <span class="hljs-variable">$FLUME_HOME</span>/bin</code></pre>

<p>查看使用的帮助，会打印相关的命令指示，从而来帮助我们知道如何进行使用：</p>

<pre class="prettyprint"><code class=" hljs ruby"><span class="hljs-variable">$&gt;</span>./flume-ng</code></pre>

<p>列举出来的都是常用的，不常用的没有写在下面：</p>



<pre class="prettyprint"><code class=" hljs http"><span class="hljs-attribute">Usage</span>: <span class="hljs-string">./flume-ng &lt;command&gt; [options]...</span>

<span class="lasso">commands:
  agent                     run a Flume agent
                            使用的最多的方式
  avro<span class="hljs-attribute">-client</span>               run an avro Flume client
                            这种方式用的也不多，因为这种方式很low，没有办法在生产上面使用

<span class="hljs-built_in">global</span> options:
  <span class="hljs-subst">--</span>conf,<span class="hljs-attribute">-c</span> <span class="hljs-subst">&lt;</span>conf<span class="hljs-subst">&gt;</span>          use configs <span class="hljs-keyword">in</span> <span class="hljs-subst">&lt;</span>conf<span class="hljs-subst">&gt;</span> directory
                            来指定使用的配置文件在什么地方；其实这个配置文件指向的就是<span class="hljs-variable">$FLUME_HOME</span>/conf下的内容
  <span class="hljs-attribute">-Dproperty</span><span class="hljs-subst">=</span>value          sets a Java system property value
                            运行一些java的属性，等会会用到

agent options:
  <span class="hljs-subst">--</span>name,<span class="hljs-attribute">-n</span> <span class="hljs-subst">&lt;</span>name<span class="hljs-subst">&gt;</span>          the name of this agent (required)
                            agent的名字
  <span class="hljs-subst">--</span>conf<span class="hljs-attribute">-file</span>,<span class="hljs-attribute">-f</span> <span class="hljs-subst">&lt;</span>file<span class="hljs-subst">&gt;</span>     specify a config file (required <span class="hljs-keyword">if</span> <span class="hljs-attribute">-z</span> missing)
                            指定的是自己定义的flume的配置文件</span></code></pre>



<h4 id="相关参数解释">相关参数解释</h4>

<p>官网(1.6.0版本的)：<a href="http://archive.cloudera.com/cdh5/cdh/5/flume-ng-1.6.0-cdh5.7.0/FlumeUserGuide.html#a-simple-example" rel="nofollow">http://archive.cloudera.com/cdh5/cdh/5/flume-ng-1.6.0-cdh5.7.0/FlumeUserGuide.html#a-simple-example</a> <br>
代码解释：</p>

<pre class="prettyprint"><code class=" hljs vala"><span class="hljs-preprocessor"># a1：Agent的名称</span>
<span class="hljs-preprocessor"># sources的名称：r1</span>
<span class="hljs-preprocessor"># sink的名称：k1</span>
<span class="hljs-preprocessor"># channels的名称：c1</span>
a1.sources = r1
a1.sinks = k1
a1.channels = c1

<span class="hljs-preprocessor"># 配置source</span>
<span class="hljs-preprocessor"># sources代表source可以有多个，这里只有一个a1</span>
<span class="hljs-preprocessor"># r1对应的类型是netcat</span>
<span class="hljs-preprocessor"># 官网：http://archive.cloudera.com/cdh5/cdh/5/flume-ng-1.6.0-cdh5.7.0/FlumeUserGuide.html#netcat-source</span>
<span class="hljs-preprocessor"># 监听一个端口，把里面每一行的数据作为一个event给返回回来 ==&gt; 这和$&gt;nc -k -l [host] [port]是一样的</span>
<span class="hljs-preprocessor"># 必填的属性为：channels   </span>
<span class="hljs-preprocessor">#              type         必须为netcat</span>
<span class="hljs-preprocessor">#              bind         绑定在哪台机器上面，Host name or IP address都可以</span>
<span class="hljs-preprocessor">#              port         端口</span>
a1.sources.r1.type = netcat             
a1.sources.r1.bind = localhost
a1.sources.r1.port = <span class="hljs-number">44444</span>

<span class="hljs-preprocessor"># 配置channel</span>
<span class="hljs-preprocessor"># 使用内存的channel</span>
<span class="hljs-preprocessor"># 官网：http://archive.cloudera.com/cdh5/cdh/5/flume-ng-1.6.0-cdh5.7.0/FlumeUserGuide.html#memory-channel</span>
<span class="hljs-preprocessor"># 必填的属性：type            必须为memory  (就这1个必填的属性)</span>
<span class="hljs-preprocessor"># </span>
a1.channels.c1.type = memory

<span class="hljs-preprocessor"># 配置sink，使其输出到控制台</span>
<span class="hljs-preprocessor"># 官网：http://archive.cloudera.com/cdh5/cdh/5/flume-ng-1.6.0-cdh5.7.0/FlumeUserGuide.html#logger-sink</span>
<span class="hljs-preprocessor"># 必填的属性：channel</span>
<span class="hljs-preprocessor">#            type       必须为logger</span>
a1.sinks.k1.type = logger

<span class="hljs-preprocessor"># 配完source、channel、sink之后，这些东西都是散的，我们需要将他们给串起来</span>
<span class="hljs-preprocessor"># 上述的组件中，source与sink的必填属性中都有channel，我们都还未进行配置</span>
<span class="hljs-preprocessor"># 这里只需要将source的channel指定</span>
<span class="hljs-preprocessor">#          将sink的channel指定</span>
<span class="hljs-preprocessor">#          就OK了(channel都是自己定义的a1.channels = c1中的c1)</span>
<span class="hljs-preprocessor"># 可以这样进行理解：</span>
<span class="hljs-preprocessor">#   a1的source收集完数据之后，去了c1这个channel</span>
<span class="hljs-preprocessor">#   a1的sink从c1这个channel去取数据</span>
<span class="hljs-preprocessor"># 注意：</span>
<span class="hljs-preprocessor">#   a1.sources.r1.channels = c1     代表了1个数据源可以输出到多个不同的channel</span>
<span class="hljs-preprocessor">#   a1.sinks.k1.channel = c1        但是1个sink只能去1个channel里取数据，是不能从多个channel去取数据的</span>
a1.sources.r1.channels = c1
a1.sinks.k1.channel = c1</code></pre>



<h4 id="运行flume的入门案例">运行Flume的入门案例</h4>

<p><strong>编写配置文件：</strong></p>

<pre class="prettyprint"><code class=" hljs avrasm">$&gt;cd $FLUME_HOME/conf
$&gt;vi hello<span class="hljs-preprocessor">.conf</span>
  a1<span class="hljs-preprocessor">.sources</span> = <span class="hljs-built_in">r1</span>
  a1<span class="hljs-preprocessor">.sinks</span> = k1
  a1<span class="hljs-preprocessor">.channels</span> = c1

  a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.type</span> = netcat
  a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.bind</span> = localhost
  a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.port</span> = <span class="hljs-number">44444</span>
  a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.channels</span> = c1

  a1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.type</span> = memory

  a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.type</span> = logger
  a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.channel</span> = c1</code></pre>

<p><strong>启动agent：</strong> <br>
官网：<a href="http://archive.cloudera.com/cdh5/cdh/5/flume-ng-1.6.0-cdh5.7.0/FlumeUserGuide.html#starting-an-agent" rel="nofollow">http://archive.cloudera.com/cdh5/cdh/5/flume-ng-1.6.0-cdh5.7.0/FlumeUserGuide.html#starting-an-agent</a></p>

<pre class="prettyprint"><code class=" hljs lasso">$<span class="hljs-subst">&gt;</span>bin/flume<span class="hljs-attribute">-ng</span> agent <span class="hljs-attribute">-n</span> <span class="hljs-variable">$agent_name</span> <span class="hljs-attribute">-c</span> conf <span class="hljs-attribute">-f</span> conf/flume<span class="hljs-attribute">-conf</span><span class="hljs-built_in">.</span>properties<span class="hljs-built_in">.</span>template
$<span class="hljs-subst">&gt;</span>flume<span class="hljs-attribute">-ng</span> agent <span class="hljs-subst">\</span>
  <span class="hljs-subst">--</span>name a1  <span class="hljs-subst">\</span>
  <span class="hljs-subst">--</span>conf <span class="hljs-variable">$FLUME_HOME</span>/conf <span class="hljs-subst">\</span>
  <span class="hljs-subst">--</span>conf<span class="hljs-attribute">-file</span> <span class="hljs-variable">$FLUME_HOME</span>/conf/hello<span class="hljs-built_in">.</span>conf <span class="hljs-subst">\</span>
  <span class="hljs-attribute">-Dflume</span><span class="hljs-built_in">.</span>root<span class="hljs-built_in">.</span>logger<span class="hljs-subst">=</span>INFO,console              <span class="hljs-comment">// 指定日志级别(建议使用起来)</span></code></pre>

<p>观察打印的log信息：</p>



<pre class="prettyprint"><code class=" hljs haskell"><span class="hljs-type">Creating</span> <span class="hljs-keyword">instance</span> <span class="hljs-keyword">of</span> channel c1 <span class="hljs-typedef"><span class="hljs-keyword">type</span> memory </span>
<span class="hljs-type">Creating</span> <span class="hljs-keyword">instance</span> <span class="hljs-keyword">of</span> source r1, <span class="hljs-typedef"><span class="hljs-keyword">type</span> netcat</span>
<span class="hljs-type">Creating</span> <span class="hljs-keyword">instance</span> <span class="hljs-keyword">of</span> sink: k1, <span class="hljs-typedef"><span class="hljs-keyword">type</span>: logger</span>
<span class="hljs-type">Channel</span> c1 connected to [r1, k1]
<span class="hljs-type">Starting</span> <span class="hljs-type">Channel</span> c1
<span class="hljs-type">Starting</span> <span class="hljs-type">Sink</span> k1
<span class="hljs-type">Starting</span> <span class="hljs-type">Source</span> r1</code></pre>

<p><strong>安装telnet</strong></p>

<pre class="prettyprint"><code class=" hljs ruby"><span class="hljs-variable">$&gt;</span>yum install telnet-server
<span class="hljs-variable">$&gt;</span>yum install telnet</code></pre>

<p><strong>测试</strong></p>

<pre class="prettyprint"><code class=" hljs ruby"><span class="hljs-variable">$&gt;</span>telnet localhost <span class="hljs-number">44444</span>
输入信息：
huhuniao
flume test
man city
man united</code></pre>

<p>观察启动agent那端的，所显示的log信息：</p>



<pre class="prettyprint"><code class=" hljs css">2018<span class="hljs-tag">-02-05</span> 14<span class="hljs-pseudo">:14</span><span class="hljs-pseudo">:40</span>,661 (<span class="hljs-tag">SinkRunner-PollingRunner-DefaultSinkProcessor</span>) <span class="hljs-attr_selector">[INFO - org.apache.flume.sink.LoggerSink.process(LoggerSink.java:94)]</span> <span class="hljs-tag">Event</span>: <span class="hljs-rules">{ <span class="hljs-rule"><span class="hljs-attribute">headers</span>:<span class="hljs-value">{</span></span></span>} <span class="hljs-tag">body</span>: 68 65 6<span class="hljs-tag">C</span> 6<span class="hljs-tag">C</span> 6<span class="hljs-tag">F</span> 20 68 75 68 75 6<span class="hljs-tag">E</span> 69 61 6<span class="hljs-tag">F</span> 0<span class="hljs-tag">D</span>    <span class="hljs-tag">hello</span> <span class="hljs-tag">huhuniao</span>. }
2018<span class="hljs-tag">-02-05</span> 14<span class="hljs-pseudo">:14</span><span class="hljs-pseudo">:44</span>,662 (<span class="hljs-tag">SinkRunner-PollingRunner-DefaultSinkProcessor</span>) <span class="hljs-attr_selector">[INFO - org.apache.flume.sink.LoggerSink.process(LoggerSink.java:94)]</span> <span class="hljs-tag">Event</span>: <span class="hljs-rules">{ <span class="hljs-rule"><span class="hljs-attribute">headers</span>:<span class="hljs-value">{</span></span></span>} <span class="hljs-tag">body</span>: 66 6<span class="hljs-tag">C</span> 75 6<span class="hljs-tag">D</span> 65 20 74 65 73 74 0<span class="hljs-tag">D</span>                <span class="hljs-tag">flume</span> <span class="hljs-tag">test</span>. }
2018<span class="hljs-tag">-02-05</span> 14<span class="hljs-pseudo">:14</span><span class="hljs-pseudo">:47</span>,607 (<span class="hljs-tag">SinkRunner-PollingRunner-DefaultSinkProcessor</span>) <span class="hljs-attr_selector">[INFO - org.apache.flume.sink.LoggerSink.process(LoggerSink.java:94)]</span> <span class="hljs-tag">Event</span>: <span class="hljs-rules">{ <span class="hljs-rule"><span class="hljs-attribute">headers</span>:<span class="hljs-value">{</span></span></span>} <span class="hljs-tag">body</span>: 6<span class="hljs-tag">D</span> 61 6<span class="hljs-tag">E</span> 20 63 69 74 79 0<span class="hljs-tag">D</span>                      <span class="hljs-tag">man</span> <span class="hljs-tag">city</span>. }
2018<span class="hljs-tag">-02-05</span> 14<span class="hljs-pseudo">:14</span><span class="hljs-pseudo">:51</span>,609 (<span class="hljs-tag">SinkRunner-PollingRunner-DefaultSinkProcessor</span>) <span class="hljs-attr_selector">[INFO - org.apache.flume.sink.LoggerSink.process(LoggerSink.java:94)]</span> <span class="hljs-tag">Event</span>: <span class="hljs-rules">{ <span class="hljs-rule"><span class="hljs-attribute">headers</span>:<span class="hljs-value">{</span></span></span>} <span class="hljs-tag">body</span>: 6<span class="hljs-tag">D</span> 61 6<span class="hljs-tag">E</span> 20 75 6<span class="hljs-tag">E</span> 69 74 65 64 0<span class="hljs-tag">D</span>                <span class="hljs-tag">man</span> <span class="hljs-tag">united</span>. }</code></pre>

<p><strong>这样将指定机器端口上的数据给收集过来了</strong></p>



<h3 id="flume工作原理">Flume工作原理</h3>

<p>Event就是Flume数据传输的基本单元：</p>

<pre class="prettyprint"><code class=" hljs css"><span class="hljs-tag">Event</span>: <span class="hljs-rules">{ 
    <span class="hljs-rule"><span class="hljs-attribute">headers</span>:<span class="hljs-value">{</span></span></span>} 
    <span class="hljs-tag">body</span>: 68 65 6<span class="hljs-tag">C</span> 6<span class="hljs-tag">C</span> 6<span class="hljs-tag">F</span> 20 68 75 68 75 6<span class="hljs-tag">E</span> 69 61 6<span class="hljs-tag">F</span> 0<span class="hljs-tag">D</span>    
        <span class="hljs-tag">hello</span> <span class="hljs-tag">huhuniao</span>. }</code></pre>

<ul>
<li>一条数据就是一个event</li>
<li>event由header和body构成</li>
<li>body里面的内容是字节数组</li>
</ul>

<p><strong>基本工作原理：</strong> <br>
参看基本工作原理图： <br>
<img src="https://img-blog.csdn.net/20180610142115804?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xlbW9uWmhhb1Rhbw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<p>数据从source里面过来 <br>
把数据put到channel(channel可以理解为队列，先进先出； <br>
每个event慢慢地从队尾到队头，后面进来的event在队尾) <br>
sink取数据从队头开始取，即往下面压，从上面取</p>

<p>该案例仅仅只能作为1个<strong>demo</strong>，玩玩而已(用来入门、了解flume的)，生产上是没有人从socket去拿数据然后输出信息到控制台的；后续的文章将继续介绍Flume，Flume在生产中的使用</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>