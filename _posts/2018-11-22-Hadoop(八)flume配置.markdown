---
layout:     post
title:      Hadoop(八)flume配置
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/mingyunxiaohai/article/details/80585280				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>实时日志收集系统Flume 分为 Flume NG 和Flume OG，Flume NG是Flume OG的进化版，更简单，更小，更易于部署，Flume NG不一定会向后兼容，所以如果是刚入门的话，最好使用Flume NG。</p>

<p>Flume的一些核心概念</p>

<ul>
<li>Event  事件是可由Flume NG运输的单个数据单元。事件类似于JMS和类似消息传递系统中的消息，并且通常很小（大约几个字节到几千字节）。事件通常也是大数据集中的单个记录。一个事件由头和身体组成; 前者是键/值映射，后者是任意字节数组</li>
<li>Source Flume NG从中接收数据的数据源。源可以是可轮询的或事件驱动的，（source从Client收集数据，传递给Channel）</li>
<li>Sink 接收器  它是Flume NG中数据的目的地，Sink从Channel中取出事件，然后将数据发到别处，可以向文件系统、数据库、 hadoop存数据</li>
<li>Channel  Source和sink之间的中转站，当发送比接收快的时候，可以在channel中缓冲</li>
<li>Agent   一个独立的Flume进程，包含组件Source、 Channel、 Sink。（Agent使用JVM 运行Flume。每台机器运行一个agent，但是可以在一个agent中包含多个sources和sinks）</li>
<li>Client 生产数据，运行在一个独立的线程。</li>
</ul>

<p>下载地址 <a href="http://flume.apache.org/download.html" rel="nofollow">http://flume.apache.org/download.html</a></p>

<p>下载完成之后上传到linux服务器并解压</p>

<pre class="prettyprint"><code class=" hljs lasso">tar <span class="hljs-attribute">-zxvf</span> apache<span class="hljs-attribute">-flume</span><span class="hljs-subst">-</span><span class="hljs-number">1.8</span><span class="hljs-number">.0</span><span class="hljs-attribute">-bin</span><span class="hljs-built_in">.</span>tar<span class="hljs-built_in">.</span>gz</code></pre>

<p>进入到解压目录conf目录中，里面给了一个模板flume-conf.properties.template复制一份并编辑</p>



<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-keyword">cp</span> flume-conf<span class="hljs-preprocessor">.properties</span><span class="hljs-preprocessor">.template</span> flume<span class="hljs-preprocessor">.conf</span>
vim flume<span class="hljs-preprocessor">.conf</span>  </code></pre>

<p>Flume使用基于Java属性文件的配置格式。需要我们在运行代理时告诉Flume通过选项使用哪个文件</p>

<p>一个基本的小例子，把下面的复制道flume.conf中</p>

<pre class="prettyprint"><code class=" hljs avrasm">
<span class="hljs-preprocessor"># Define a memory channel called ch1 on agent1</span>
agent1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.ch</span>1<span class="hljs-preprocessor">.type</span> = memory

<span class="hljs-preprocessor"># Define an Avro source called avro-source1 on agent1 and tell it</span>
<span class="hljs-preprocessor"># to bind to 0.0.0.0:41414. Connect it to channel ch1.</span>
agent1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.avro</span>-source1<span class="hljs-preprocessor">.channels</span> = ch1
agent1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.avro</span>-source1<span class="hljs-preprocessor">.type</span> = avro
agent1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.avro</span>-source1<span class="hljs-preprocessor">.bind</span> = <span class="hljs-number">0.0</span><span class="hljs-number">.0</span><span class="hljs-number">.0</span>
agent1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.avro</span>-source1<span class="hljs-preprocessor">.port</span> = <span class="hljs-number">41414</span>

<span class="hljs-preprocessor"># Define a logger sink that simply logs all events it receives</span>
<span class="hljs-preprocessor"># and connect it to the other end of the same channel.</span>
agent1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.log</span>-sink1<span class="hljs-preprocessor">.channel</span> = ch1
agent1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.log</span>-sink1<span class="hljs-preprocessor">.type</span> = logger

<span class="hljs-preprocessor"># Finally, now that we've defined all of our components, tell</span>
<span class="hljs-preprocessor"># agent1 which ones we want to activate.</span>
agent1<span class="hljs-preprocessor">.channels</span> = ch1
agent1<span class="hljs-preprocessor">.sources</span> = avro-source1
agent1<span class="hljs-preprocessor">.sinks</span> = log-sink1</code></pre>

<p>这个例子创建了一个 memory channel ，Avro RPC源和logger sink并将它们连接在一起 <br>
Avro源接收到的任何事件都被路由到通道ch1并传送到记录器接收器</p>

<p>启动flume</p>



<pre class="prettyprint"><code class=" hljs lasso">bin/flume<span class="hljs-attribute">-ng</span> agent <span class="hljs-subst">--</span>conf <span class="hljs-built_in">.</span>/conf<span class="hljs-subst">/</span> <span class="hljs-attribute">-f</span> conf/flume<span class="hljs-built_in">.</span>conf <span class="hljs-attribute">-Dflume</span><span class="hljs-built_in">.</span>root<span class="hljs-built_in">.</span>logger<span class="hljs-subst">=</span>DEBUG,console <span class="hljs-attribute">-n</span> agent1
</code></pre>

<p>–conf 代表配置的路径 <br>
-f 指定配置文件，这个配置文件必须在全局选项的–conf参数定义的目录下 <br>
-Dflume 设置log的输出级别 <br>
-n Agent的名字</p>

<p>可以看到日志</p>

<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-number">2018</span>-<span class="hljs-number">06</span>-<span class="hljs-number">08</span> <span class="hljs-number">15</span>:<span class="hljs-number">06</span>:<span class="hljs-number">08</span>,<span class="hljs-number">636</span> (SinkRunner-PollingRunner-DefaultSinkProcessor) [DEBUG - org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.SinkRunner</span>$PollingRunner<span class="hljs-preprocessor">.run</span>(SinkRunner<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">141</span>)] Polling sink runner starting
<span class="hljs-number">2018</span>-<span class="hljs-number">06</span>-<span class="hljs-number">08</span> <span class="hljs-number">15</span>:<span class="hljs-number">06</span>:<span class="hljs-number">09</span>,<span class="hljs-number">246</span> (lifecycleSupervisor-<span class="hljs-number">1</span>-<span class="hljs-number">0</span>) [INFO - org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.instrumentation</span><span class="hljs-preprocessor">.MonitoredCounterGroup</span><span class="hljs-preprocessor">.register</span>(MonitoredCounterGroup<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">119</span>)] Monitored counter group for type: SOURCE, name: avro-source1: Successfully registered new MBean.
<span class="hljs-number">2018</span>-<span class="hljs-number">06</span>-<span class="hljs-number">08</span> <span class="hljs-number">15</span>:<span class="hljs-number">06</span>:<span class="hljs-number">09</span>,<span class="hljs-number">246</span> (lifecycleSupervisor-<span class="hljs-number">1</span>-<span class="hljs-number">0</span>) [INFO - org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.instrumentation</span><span class="hljs-preprocessor">.MonitoredCounterGroup</span><span class="hljs-preprocessor">.start</span>(MonitoredCounterGroup<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">95</span>)] Component type: SOURCE, name: avro-source1 started
<span class="hljs-number">2018</span>-<span class="hljs-number">06</span>-<span class="hljs-number">08</span> <span class="hljs-number">15</span>:<span class="hljs-number">06</span>:<span class="hljs-number">09</span>,<span class="hljs-number">262</span> (lifecycleSupervisor-<span class="hljs-number">1</span>-<span class="hljs-number">0</span>) [INFO - org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.source</span><span class="hljs-preprocessor">.AvroSource</span><span class="hljs-preprocessor">.start</span>(AvroSource<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">260</span>)] Avro source avro-source1 started.
<span class="hljs-number">2018</span>-<span class="hljs-number">06</span>-<span class="hljs-number">08</span> <span class="hljs-number">15</span>:<span class="hljs-number">06</span>:<span class="hljs-number">38</span>,<span class="hljs-number">635</span> (conf-file-poller-<span class="hljs-number">0</span>) [DEBUG - org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.node</span><span class="hljs-preprocessor">.PollingPropertiesFileConfigurationProvider</span>$FileWatcherRunnable<span class="hljs-preprocessor">.run</span>(PollingPropertiesFileConfigurationProvider<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">127</span>)] Checking file:conf/flume<span class="hljs-preprocessor">.conf</span> for changes
<span class="hljs-number">2018</span>-<span class="hljs-number">06</span>-<span class="hljs-number">08</span> <span class="hljs-number">15</span>:<span class="hljs-number">07</span>:<span class="hljs-number">08</span>,<span class="hljs-number">637</span> (conf-file-poller-<span class="hljs-number">0</span>) [DEBUG - org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.node</span><span class="hljs-preprocessor">.PollingPropertiesFileConfigurationProvider</span>$FileWatcherRunnable<span class="hljs-preprocessor">.run</span>(PollingPropertiesFileConfigurationProvider<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">127</span>)] Checking file:conf/flume<span class="hljs-preprocessor">.conf</span> for changes
</code></pre>

<p>日志中可以看到 Avro source avro-source1 started 已经启动，并隔一段时间去检查配置文件 Checking file:conf/flume.conf for changes</p>

<p>做个小例子，下面为每个Linux用户创建一个事件，并将其发送到本地主机上的Flume的avro源代码：41414。</p>

<p>在新窗口中输入以下内容：</p>

<pre class="prettyprint"><code class=" hljs lasso">bin/flume<span class="hljs-attribute">-ng</span> avro<span class="hljs-attribute">-client</span> <span class="hljs-subst">--</span>conf conf <span class="hljs-attribute">-H</span> localhost <span class="hljs-attribute">-p</span> <span class="hljs-number">41414</span> <span class="hljs-attribute">-F</span> /etc/passwd <span class="hljs-attribute">-Dflume</span><span class="hljs-built_in">.</span>root<span class="hljs-built_in">.</span>logger<span class="hljs-subst">=</span>DEBUG,console</code></pre>

<p>改窗口中可以看到以下日志</p>

<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-number">2018</span>-<span class="hljs-number">06</span>-<span class="hljs-number">08</span> <span class="hljs-number">15</span>:<span class="hljs-number">20</span>:<span class="hljs-number">04</span>,<span class="hljs-number">243</span> (main) [WARN - org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.api</span><span class="hljs-preprocessor">.NettyAvroRpcClient</span><span class="hljs-preprocessor">.configure</span>(NettyAvroRpcClient<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">634</span>)] Using default maxIOWorkers
<span class="hljs-number">2018</span>-<span class="hljs-number">06</span>-<span class="hljs-number">08</span> <span class="hljs-number">15</span>:<span class="hljs-number">20</span>:<span class="hljs-number">04</span>,<span class="hljs-number">680</span> (main) [DEBUG - org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.client</span><span class="hljs-preprocessor">.avro</span><span class="hljs-preprocessor">.AvroCLIClient</span><span class="hljs-preprocessor">.run</span>(AvroCLIClient<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">233</span>)] Finished
<span class="hljs-number">2018</span>-<span class="hljs-number">06</span>-<span class="hljs-number">08</span> <span class="hljs-number">15</span>:<span class="hljs-number">20</span>:<span class="hljs-number">04</span>,<span class="hljs-number">680</span> (main) [DEBUG - org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.client</span><span class="hljs-preprocessor">.avro</span><span class="hljs-preprocessor">.AvroCLIClient</span><span class="hljs-preprocessor">.run</span>(AvroCLIClient<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">236</span>)] Closing reader
<span class="hljs-number">2018</span>-<span class="hljs-number">06</span>-<span class="hljs-number">08</span> <span class="hljs-number">15</span>:<span class="hljs-number">20</span>:<span class="hljs-number">04</span>,<span class="hljs-number">681</span> (main) [DEBUG - org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.client</span><span class="hljs-preprocessor">.avro</span><span class="hljs-preprocessor">.AvroCLIClient</span><span class="hljs-preprocessor">.run</span>(AvroCLIClient<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">240</span>)] Closing RPC client
<span class="hljs-number">2018</span>-<span class="hljs-number">06</span>-<span class="hljs-number">08</span> <span class="hljs-number">15</span>:<span class="hljs-number">20</span>:<span class="hljs-number">04</span>,<span class="hljs-number">689</span> (main) [DEBUG - org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.client</span><span class="hljs-preprocessor">.avro</span><span class="hljs-preprocessor">.AvroCLIClient</span><span class="hljs-preprocessor">.main</span>(AvroCLIClient<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">84</span>)] Exiting
</code></pre>

<p>老窗口中可以看到Event的打印日志</p>

<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-number">2018</span>-<span class="hljs-number">06</span>-<span class="hljs-number">08</span> <span class="hljs-number">15</span>:<span class="hljs-number">20</span>:<span class="hljs-number">06</span>,<span class="hljs-number">800</span> (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.sink</span><span class="hljs-preprocessor">.LoggerSink</span><span class="hljs-preprocessor">.process</span>(LoggerSink<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">95</span>)] Event: { headers:{} body: <span class="hljs-number">72</span> <span class="hljs-number">6</span>F <span class="hljs-number">6</span>F <span class="hljs-number">74</span> <span class="hljs-number">3</span>A <span class="hljs-number">78</span> <span class="hljs-number">3</span>A <span class="hljs-number">30</span> <span class="hljs-number">3</span>A <span class="hljs-number">30</span> <span class="hljs-number">3</span>A <span class="hljs-number">72</span> <span class="hljs-number">6</span>F <span class="hljs-number">6</span>F <span class="hljs-number">74</span> <span class="hljs-number">3</span>A root:<span class="hljs-built_in">x</span>:<span class="hljs-number">0</span>:<span class="hljs-number">0</span>:root: }
<span class="hljs-number">2018</span>-<span class="hljs-number">06</span>-<span class="hljs-number">08</span> <span class="hljs-number">15</span>:<span class="hljs-number">20</span>:<span class="hljs-number">06</span>,<span class="hljs-number">801</span> (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.sink</span><span class="hljs-preprocessor">.LoggerSink</span><span class="hljs-preprocessor">.process</span>(LoggerSink<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">95</span>)] Event: { headers:{} body: <span class="hljs-number">62</span> <span class="hljs-number">69</span> <span class="hljs-number">6</span>E <span class="hljs-number">3</span>A <span class="hljs-number">78</span> <span class="hljs-number">3</span>A <span class="hljs-number">31</span> <span class="hljs-number">3</span>A <span class="hljs-number">31</span> <span class="hljs-number">3</span>A <span class="hljs-number">62</span> <span class="hljs-number">69</span> <span class="hljs-number">6</span>E <span class="hljs-number">3</span>A <span class="hljs-number">2</span>F <span class="hljs-number">62</span> bin:<span class="hljs-built_in">x</span>:<span class="hljs-number">1</span>:<span class="hljs-number">1</span>:bin:/b }
<span class="hljs-number">2018</span>-<span class="hljs-number">06</span>-<span class="hljs-number">08</span> <span class="hljs-number">15</span>:<span class="hljs-number">20</span>:<span class="hljs-number">06</span>,<span class="hljs-number">801</span> (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.sink</span><span class="hljs-preprocessor">.LoggerSink</span><span class="hljs-preprocessor">.process</span>(LoggerSink<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">95</span>)] Event: { headers:{} body: <span class="hljs-number">64</span> <span class="hljs-number">61</span> <span class="hljs-number">65</span> <span class="hljs-number">6</span>D <span class="hljs-number">6</span>F <span class="hljs-number">6</span>E <span class="hljs-number">3</span>A <span class="hljs-number">78</span> <span class="hljs-number">3</span>A <span class="hljs-number">32</span> <span class="hljs-number">3</span>A <span class="hljs-number">32</span> <span class="hljs-number">3</span>A <span class="hljs-number">64</span> <span class="hljs-number">61</span> <span class="hljs-number">65</span> daemon:<span class="hljs-built_in">x</span>:<span class="hljs-number">2</span>:<span class="hljs-number">2</span>:dae }
<span class="hljs-number">2018</span>-<span class="hljs-number">06</span>-<span class="hljs-number">08</span> <span class="hljs-number">15</span>:<span class="hljs-number">20</span>:<span class="hljs-number">06</span>,<span class="hljs-number">801</span> (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.sink</span><span class="hljs-preprocessor">.LoggerSink</span><span class="hljs-preprocessor">.process</span>(LoggerSink<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">95</span>)] Event: { headers:{} body: <span class="hljs-number">61</span> <span class="hljs-number">64</span> <span class="hljs-number">6</span>D <span class="hljs-number">3</span>A <span class="hljs-number">78</span> <span class="hljs-number">3</span>A <span class="hljs-number">33</span> <span class="hljs-number">3</span>A <span class="hljs-number">34</span> <span class="hljs-number">3</span>A <span class="hljs-number">61</span> <span class="hljs-number">64</span> <span class="hljs-number">6</span>D <span class="hljs-number">3</span>A <span class="hljs-number">2</span>F <span class="hljs-number">76</span> adm:<span class="hljs-built_in">x</span>:<span class="hljs-number">3</span>:<span class="hljs-number">4</span>:adm:/v }
<span class="hljs-number">2018</span>-<span class="hljs-number">06</span>-<span class="hljs-number">08</span> <span class="hljs-number">15</span>:<span class="hljs-number">20</span>:<span class="hljs-number">06</span>,<span class="hljs-number">801</span> (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.sink</span><span class="hljs-preprocessor">.LoggerSink</span><span class="hljs-preprocessor">.process</span>(LoggerSink<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">95</span>)] Event: { headers:{} body: <span class="hljs-number">6</span>C <span class="hljs-number">70</span> <span class="hljs-number">3</span>A <span class="hljs-number">78</span> <span class="hljs-number">3</span>A <span class="hljs-number">34</span> <span class="hljs-number">3</span>A <span class="hljs-number">37</span> <span class="hljs-number">3</span>A <span class="hljs-number">6</span>C <span class="hljs-number">70</span> <span class="hljs-number">3</span>A <span class="hljs-number">2</span>F <span class="hljs-number">76</span> <span class="hljs-number">61</span> <span class="hljs-number">72</span> lp:<span class="hljs-built_in">x</span>:<span class="hljs-number">4</span>:<span class="hljs-number">7</span>:lp:/var }
<span class="hljs-number">2018</span>-<span class="hljs-number">06</span>-<span class="hljs-number">08</span> <span class="hljs-number">15</span>:<span class="hljs-number">20</span>:<span class="hljs-number">06</span>,<span class="hljs-number">802</span> (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.sink</span><span class="hljs-preprocessor">.LoggerSink</span><span class="hljs-preprocessor">.process</span>(LoggerSink<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">95</span>)] Event: { headers:{} body: <span class="hljs-number">73</span> <span class="hljs-number">79</span> <span class="hljs-number">6</span>E <span class="hljs-number">63</span> <span class="hljs-number">3</span>A <span class="hljs-number">78</span> <span class="hljs-number">3</span>A <span class="hljs-number">35</span> <span class="hljs-number">3</span>A <span class="hljs-number">30</span> <span class="hljs-number">3</span>A <span class="hljs-number">73</span> <span class="hljs-number">79</span> <span class="hljs-number">6</span>E <span class="hljs-number">63</span> <span class="hljs-number">3</span>A sync:<span class="hljs-built_in">x</span>:<span class="hljs-number">5</span>:<span class="hljs-number">0</span>:sync: }
</code></pre>

<p>到这里 恭喜！你有Apache Flume运行成功</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>