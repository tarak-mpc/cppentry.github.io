---
layout:     post
title:      Flume-ng在windows环境搭建并测试+log4j日志通过Flume输出到HDFS
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/antgan/article/details/52087926				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<blockquote>
  <p>首先，去Flume官网<a href="http://flume.apache.org/download.html" rel="nofollow">下载</a>最新的版本 <br>
  这里我使用的是<a href="http://www.apache.org/dyn/closer.lua/flume/1.6.0/apache-flume-1.6.0-bin.tar.gz" rel="nofollow">apache-flume-1.6.0-bin.tar.gz</a></p>
</blockquote>

<hr>

<blockquote>
  <h3 id="开发环境">开发环境</h3>
  
  <p>系统：window7 <br>
   Jdk：1.7 <br>
  Flume：1.6.0 <br>
  Hadoop:2.5.2</p>
</blockquote>

<hr>



<h3 id="一下载flume并解压安装配置">一、下载flume并解压安装配置</h3>

<ol>
<li><p>下载解压到你想要的目录 <br>
<img src="https://img-blog.csdn.net/20160801172532978" alt="这里写图片描述" title=""></p></li>
<li><p>配置环境变量 <br>
新建FLUME_HOME变量，填写flume安装目录，如图 <br>
<img src="https://img-blog.csdn.net/20160801172728898" alt="这里写图片描述" title=""> <br>
然后编辑系统变量<strong>path</strong>，追加%FLUME_HOME%\conf; %FLUME_HOME%\bin;</p></li>
<li><p>修改flume下conf文件夹中的<strong>flume-env.sh</strong>（如果没有就复制一份flume-env.sh.template，改名为flume-env.sh） <br>
中的JAVA_HOME，指定jdk安装路径 <br>
如：<strong>export JAVA_HOME=D:\java\jdk</strong></p></li>
<li><p>测试是否安装成功 <br>
cmd进入命令窗口，输入</p></li>
</ol>



<pre class="prettyprint"><code class=" hljs livecodeserver">flume-ng <span class="hljs-built_in">version</span></code></pre>

<p>如果安装成功，会出现如图的版本号。 <br>
<img src="https://img-blog.csdn.net/20160801173413512" alt="这里写图片描述" title=""></p>

<hr>



<h3 id="二测试实例">二、测试实例</h3>



<h4 id="1-数据源为avro-使用telnet连接source写入数据产生日志数据输出控制台">1. 数据源为avro ，使用telnet连接source写入数据，产生日志数据输出控制台。</h4>

<p>①在conf文件夹中创建<strong>example.conf</strong>文件，写入</p>



<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-preprocessor"># example.conf: A single-node Flume configuration</span>

<span class="hljs-preprocessor"># Name the components on this agent</span>
a1<span class="hljs-preprocessor">.sources</span> = <span class="hljs-built_in">r1</span>
a1<span class="hljs-preprocessor">.sinks</span> = k1
a1<span class="hljs-preprocessor">.channels</span> = c1

<span class="hljs-preprocessor"># Describe/configure the source</span>
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.type</span> = netcat
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.bind</span> = localhost
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.port</span> = <span class="hljs-number">44444</span>

<span class="hljs-preprocessor"># Describe the sink</span>
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.type</span> = logger

<span class="hljs-preprocessor"># Use a channel which buffers events in memory</span>
a1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.type</span> = memory
a1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.capacity</span> = <span class="hljs-number">1000</span>
a1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.transactionCapacity</span> = <span class="hljs-number">100</span>

<span class="hljs-preprocessor"># Bind the source and sink to the channel</span>
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.channels</span> = c1
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.channel</span> = c1</code></pre>

<p>②打开cmd，切到conf目录下，输入命令启动flume</p>



<pre class="prettyprint"><code class=" hljs brainfuck"><span class="hljs-comment">flume</span><span class="hljs-literal">-</span><span class="hljs-comment">ng</span> <span class="hljs-comment">agent</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">conf</span> <span class="hljs-comment">conf</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">conf</span><span class="hljs-literal">-</span><span class="hljs-comment">file</span> <span class="hljs-comment">example</span><span class="hljs-string">.</span><span class="hljs-comment">conf</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">name</span> <span class="hljs-comment">a1</span> </code></pre>

<p>启动成功，如图所示，弹出一个小黑框 <br>
<img src="https://img-blog.csdn.net/20160801174349110" alt="这里写图片描述" title=""></p>

<p>③再打开一个cmd命令窗口，使用telnet命令，写入些数据，让flume检测。（如果windows还没开启telnet服务，<a href="http://jingyan.baidu.com/article/870c6fc3cd6fa9b03fe4bee4.html" rel="nofollow">请戳这开启服务</a>）</p>

<p>输入指令 <br>
<img src="https://img-blog.csdn.net/20160801174711318" alt="这里写图片描述" title=""> <br>
输入helloworld <br>
<img src="https://img-blog.csdn.net/20160801174814332" alt="这里写图片描述" title=""> <br>
观察flume的小黑窗，已经监控到有数据访问，并输出。 <br>
<img src="https://img-blog.csdn.net/20160801174843395" alt="这里写图片描述" title=""></p>

<hr>



<h4 id="2-数据源为avro-监控log4j日志输出并写入hdfs当中">2. 数据源为avro ，监控log4j日志输出，并写入hdfs当中</h4>

<p>①启动hadoop服务，如果还没搭建的小伙伴可以点击<a href="http://blog.csdn.net/antgan/article/details/52067441" rel="nofollow">这查看windows如何搭建hadoop服务。</a></p>

<p>②新建Java Project项目，我这里使用maven构建项目，为了方便引入log4j依赖。 <br>
<img src="https://img-blog.csdn.net/20160801175254912" alt="这里写图片描述" title=""></p>

<p>③引入log4j.jar包依赖和flume相关依赖 <br>
<strong>pom.xml</strong></p>



<pre class="prettyprint"><code class=" hljs xml">        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>log4j<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>log4j<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>1.2.17<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.apache.flume<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>flume-ng-core<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>1.5.0<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>  
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.apache.flume.flume-ng-clients<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>  
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>flume-ng-log4jappender<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>  
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>1.6.0<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>  
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>  </code></pre>

<p>④配置<strong>log4j.properties</strong>，如下</p>



<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-preprocessor">### set log levels ###</span>
log4j<span class="hljs-preprocessor">.rootLogger</span>=INFO, stdout, file, flume
log4j<span class="hljs-preprocessor">.logger</span><span class="hljs-preprocessor">.per</span><span class="hljs-preprocessor">.flume</span>=INFO

<span class="hljs-preprocessor">### flume ###</span>
log4j<span class="hljs-preprocessor">.appender</span><span class="hljs-preprocessor">.flume</span>=org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.clients</span><span class="hljs-preprocessor">.log</span>4jappender<span class="hljs-preprocessor">.Log</span>4jAppender
log4j<span class="hljs-preprocessor">.appender</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.layout</span>=org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.log</span>4j<span class="hljs-preprocessor">.PatternLayout</span>
log4j<span class="hljs-preprocessor">.appender</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.Hostname</span>=localhost
log4j<span class="hljs-preprocessor">.appender</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.Port</span>=<span class="hljs-number">44444</span>

<span class="hljs-preprocessor">### stdout ###</span>
log4j<span class="hljs-preprocessor">.appender</span><span class="hljs-preprocessor">.stdout</span>=org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.log</span>4j<span class="hljs-preprocessor">.ConsoleAppender</span>
log4j<span class="hljs-preprocessor">.appender</span><span class="hljs-preprocessor">.stdout</span><span class="hljs-preprocessor">.Threshold</span>=INFO
log4j<span class="hljs-preprocessor">.appender</span><span class="hljs-preprocessor">.stdout</span><span class="hljs-preprocessor">.Target</span>=System<span class="hljs-preprocessor">.out</span>
log4j<span class="hljs-preprocessor">.appender</span><span class="hljs-preprocessor">.stdout</span><span class="hljs-preprocessor">.layout</span>=org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.log</span>4j<span class="hljs-preprocessor">.PatternLayout</span>
log4j<span class="hljs-preprocessor">.appender</span><span class="hljs-preprocessor">.stdout</span><span class="hljs-preprocessor">.layout</span><span class="hljs-preprocessor">.ConversionPattern</span>=%d{yyyy-MM-dd HH:mm:ss} %c{<span class="hljs-number">1</span>} [%p] %m%n

<span class="hljs-preprocessor">### file ###</span>
log4j<span class="hljs-preprocessor">.appender</span><span class="hljs-preprocessor">.file</span>=org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.log</span>4j<span class="hljs-preprocessor">.DailyRollingFileAppender</span>
log4j<span class="hljs-preprocessor">.appender</span><span class="hljs-preprocessor">.file</span><span class="hljs-preprocessor">.Threshold</span>=INFO
log4j<span class="hljs-preprocessor">.appender</span><span class="hljs-preprocessor">.file</span><span class="hljs-preprocessor">.Append</span>=true
log4j<span class="hljs-preprocessor">.appender</span><span class="hljs-preprocessor">.file</span><span class="hljs-preprocessor">.DatePattern</span>=<span class="hljs-string">'.'</span>yyyy-MM-dd
log4j<span class="hljs-preprocessor">.appender</span><span class="hljs-preprocessor">.file</span><span class="hljs-preprocessor">.layout</span>=org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.log</span>4j<span class="hljs-preprocessor">.PatternLayout</span>
log4j<span class="hljs-preprocessor">.appender</span><span class="hljs-preprocessor">.file</span><span class="hljs-preprocessor">.layout</span><span class="hljs-preprocessor">.ConversionPattern</span>=%d{yyyy-MM-dd HH:mm:ss} %c{<span class="hljs-number">1</span>} [%p] %m%n</code></pre>

<p>⑤新建<strong>WriteLog.java</strong> 类</p>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">package</span> com.flume.log;
<span class="hljs-keyword">import</span> java.util.Date;
<span class="hljs-keyword">import</span> org.apache.log4j.Logger;

<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">WriteLog</span> {</span>
    <span class="hljs-keyword">protected</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">final</span> Logger logger = Logger.getLogger(WriteLog.class);

    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">main</span>(String[] args) <span class="hljs-keyword">throws</span> Exception {
        <span class="hljs-keyword">while</span> (<span class="hljs-keyword">true</span>) {
            logger.info(<span class="hljs-string">"hello world:"</span>+ String.valueOf(<span class="hljs-keyword">new</span> Date().getTime()));
            Thread.sleep(<span class="hljs-number">2000</span>);
        }
    }
}</code></pre>

<p>⑥在flume安装目录下的conf新建<strong>example-hdfs.conf</strong>，如下</p>



<pre class="prettyprint"><code class=" hljs avrasm">a1<span class="hljs-preprocessor">.sources</span>=source1  
a1<span class="hljs-preprocessor">.channels</span>=channel1  
a1<span class="hljs-preprocessor">.sinks</span>=sink1  

a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.source</span>1<span class="hljs-preprocessor">.type</span>=avro  
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.source</span>1<span class="hljs-preprocessor">.bind</span>=<span class="hljs-number">0.0</span><span class="hljs-number">.0</span><span class="hljs-number">.0</span>  
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.source</span>1<span class="hljs-preprocessor">.port</span>=<span class="hljs-number">44444</span>  
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.source</span>1<span class="hljs-preprocessor">.channels</span>=channel1  

a1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.channel</span>1<span class="hljs-preprocessor">.type</span>=memory  
a1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.channel</span>1<span class="hljs-preprocessor">.capacity</span>=<span class="hljs-number">10000</span>  
a1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.channel</span>1<span class="hljs-preprocessor">.transactionCapacity</span>=<span class="hljs-number">1000</span>  
a1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.channel</span>1<span class="hljs-preprocessor">.keep</span>-alive=<span class="hljs-number">30</span>  

a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.sink</span>1<span class="hljs-preprocessor">.type</span>=hdfs  
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.sink</span>1<span class="hljs-preprocessor">.channel</span>=channel1  
<span class="hljs-preprocessor"># 指定了我的flume日志目录，还没创建请先行创建</span>
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.sink</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.path</span>=hdfs://localhost:<span class="hljs-number">9000</span>/user/flume
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.sink</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.fileType</span>=DataStream  
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.sink</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.writeFormat</span>=Text  
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.sink</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.rollInterval</span>=<span class="hljs-number">0</span>  
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.sink</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.rollSize</span>=<span class="hljs-number">10240</span>  
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.sink</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.rollCount</span>=<span class="hljs-number">0</span>  
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.sink</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.idleTimeout</span>=<span class="hljs-number">60</span> </code></pre>

<p>⑦运行flume服务 <br>
cmd切到conf目录下，运行指令</p>



<pre class="prettyprint"><code class=" hljs brainfuck"><span class="hljs-comment">flume</span><span class="hljs-literal">-</span><span class="hljs-comment">ng</span> <span class="hljs-comment">agent</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">conf</span> <span class="hljs-comment">conf</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">conf</span><span class="hljs-literal">-</span><span class="hljs-comment">file</span> <span class="hljs-comment">example</span><span class="hljs-literal">-</span><span class="hljs-comment">hdfs</span><span class="hljs-string">.</span><span class="hljs-comment">conf</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">name</span> <span class="hljs-comment">a1</span> </code></pre>

<p>⑧运行<strong>WriteLog.java</strong></p>

<p>当日志写满10k，flume自动新建另一个日志文件继续写入，最终效果图。 <br>
<img src="https://img-blog.csdn.net/20160801180132932" alt="这里写图片描述" title=""></p>

<hr>

<p>完毕。</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>